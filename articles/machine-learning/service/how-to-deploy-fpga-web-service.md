---
title: FPGA에 모델 배포
titleSuffix: Azure Machine Learning service
description: 대기 시간이 매우 짧은 추론을 위해 Azure Machine Learning 서비스를 사용하여 FPGA에서 실행되는 모델로 웹 서비스를 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 1/29/2019
ms.custom: seodec18
ms.openlocfilehash: 7aa0e11ed47219829830369d17b300270d3fbffb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819437"
---
# <a name="deploy-a-model-as-a-web-service-on-an-fpga-with-azure-machine-learning-service"></a>Azure Machine Learning 서비스를 사용하여 FPGA에 웹 서비스로 모델 배포

[FPGA(Field-Programmable Gate Array)](concept-accelerate-with-fpgas.md)에서 웹 서비스로 모델을 배포할 수 있습니다.  FPGA를 사용하면 단일 배치 크기로도 대기 시간이 엄청나게 짧은 추론이 가능합니다.  현재 사용 가능한 모델은 다음과 같습니다.
  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16   

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.
 
  - 작업 영역은 *미국 동부 2* 지역에 있어야 합니다.

  - 다음과 같이 contrib를 추가로 설치합니다.

    ```shell
    pip install --upgrade azureml-sdk[contrib]
    ```

  - 현재는 1.10 이하의 tensorflow 버전만 지원되므로 기타 모든 설치가 완료된 후에 해당 버전을 설치하세요.

    ```shell
    pip install "tensorflow==1.10"
    ```

## <a name="create-and-deploy-your-model"></a>모델 만들기 및 배포
입력된 이미지 전처리, ResNet 50을 FPGA에 사용 하 여 기능을 확인 하는 파이프라인 만들기 하 고 ImageNet 데이터 집합에서 학습 된 분류자를 통해 기능을 실행 합니다.

지침에 따라 다음을 수행합니다.

* 모델 파이프라인 정의
* 모델 배포
* 배포된 모델 사용
* 배포된 서비스 삭제

> [!IMPORTANT]
> 대기 시간과 처리량을 최적화하려면 클라이언트가 엔드포인트와 동일한 Azure 지역에 있어야 합니다.  현재 API는 미국 동부 Azure 지역에 만들어졌습니다.



### <a name="preprocess-image"></a>이미지 전처리
파이프라인의 첫 번째 단계는 이미지를 전처리하는 것입니다.

```python
import os
import tensorflow as tf

# Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
import azureml.contrib.brainwave.models.utils as utils
in_images = tf.placeholder(tf.string)
image_tensors = utils.preprocess_array(in_images)
print(image_tensors.shape)
```

### <a name="add-featurizer"></a>기능화기 추가
모델을 초기화하고 기능화기로 사용할 ResNet50의 양자화 버전에 대한 TensorFlow 검사점을 다운로드합니다.

```python
from azureml.contrib.brainwave.models import QuantizedResnet50
model_path = os.path.expanduser('~/models')
model = QuantizedResnet50(model_path, is_frozen = True)
feature_tensor = model.import_graph_def(image_tensors)
print(model.version)
print(feature_tensor.name)
print(feature_tensor.shape)
```

### <a name="add-classifier"></a>분류자 추가
이 분류자는 ImageNet 데이터 집합을 기반으로 학습되었습니다.

```python
classifier_output = model.get_default_classifier(feature_tensor)
```

### <a name="create-service-definition"></a>서비스 정의 만들기
이제 서비스에서 실행되는 이미지 전처리, 기능화기 및 분류자가 정의되었으므로 서비스 정의를 만들 수 있습니다. 서비스 정의는 FPGA 서비스에 배포된 모델에서 생성된 파일의 집합입니다. 서비스 정의는 파이프라인으로 구성됩니다. 파이프라인은 순서대로 실행되는 일련의 단계입니다.  TensorFlow 단계, Keras 단계 및 BrainWave 단계가 지원됩니다.  단계는 서비스에서 순서대로 실행되며 각 단계의 출력은 후속 단계의 입력이 됩니다.

TensorFlow 단계를 만들려면 그래프(이 경우 기본 그래프가 사용됨)와 이 단계에 대한 입력 및 출력 텐서가 포함된 세션을 지정합니다.  이 정보는 그래프를 저장하는 데 사용되므로 서비스에서 실행될 수 있습니다.

```python
from azureml.contrib.brainwave.pipeline import ModelDefinition, TensorflowStage, BrainWaveStage

save_path = os.path.expanduser('~/models/save')
model_def_path = os.path.join(save_path, 'model_def.zip')

model_def = ModelDefinition()
with tf.Session() as sess:
    model_def.pipeline.append(TensorflowStage(sess, in_images, image_tensors))
    model_def.pipeline.append(BrainWaveStage(sess, model))
    model_def.pipeline.append(TensorflowStage(sess, feature_tensor, classifier_output))
    model_def.save(model_def_path)
    print(model_def_path)
```

### <a name="deploy-model"></a>모델 배포
서비스 정의에서 서비스를 만듭니다.  작업 영역은 미국 동부 2 위치에 있어야 합니다.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep = '\n')

from azureml.core.model import Model
model_name = "resnet-50-rtai"
registered_model = Model.register(ws, model_def_path, model_name)

from azureml.core.webservice import Webservice
from azureml.exceptions import WebserviceException
from azureml.contrib.brainwave import BrainwaveWebservice, BrainwaveImage
service_name = "imagenet-infer"
service = None
try:
    service = Webservice(ws, service_name)
except WebserviceException:
    image_config = BrainwaveImage.image_configuration()
    deployment_config = BrainwaveWebservice.deploy_configuration()
    service = Webservice.deploy_from_model(ws, service_name, [registered_model], image_config, deployment_config)
    service.wait_for_deployment(True)
```

### <a name="test-the-service"></a>서비스 테스트
이미지를 API로 보내고 응답을 테스트하려면 출력 클래스 ID에서 ImageNet 클래스 이름으로 매핑을 추가합니다.

```python
import requests
classes_entries = requests.get("https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()
```

서비스를 호출하고 아래 “your-image.jpg” 파일 이름을 컴퓨터의 이미지로 바꿉니다. 

```python
with open('your-image.jpg') as f:
    results = service.run(f)
# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
``` 

### <a name="clean-up-service"></a>서비스 정리
서비스를 삭제합니다.

```python
service.delete()
    
registered_model.delete()
```

## <a name="secure-fpga-web-services"></a>보안 FPGA 웹 서비스

FPGA 웹 서비스 보안에 대한 자세한 내용은 [웹 서비스 보호](how-to-secure-web-service.md) 문서를 참조하세요.


## <a name="next-steps"></a>다음 단계

[웹 서비스로 배포된 ML 모델을 사용](how-to-consume-web-service.md)하는 방법에 대해 알아봅니다.
