---
title: 코드 배포 안 함 (미리 보기)
titleSuffix: Azure Machine Learning
description: 항목 스크립트 없이 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
ms.openlocfilehash: 9d6e234e1f4c8ac5199b92a09eb12bf7aa41b01b
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185488"
---
# <a name="preview-no-code-model-deployment"></a>모드 코드 없는 모델 배포

코드 없는 모델 배포는 현재 미리 보기로 제공 되며 다음과 같은 기계 학습 프레임 워크를 지원 합니다.

## <a name="tensorflow-savedmodel-format"></a>TensorFlow SavedModel 형식
코드 없는 모델 배포를 사용 하려면 TensorFlow 모델을 **SavedModel 형식** 으로 등록 해야 합니다.

SavedModel를 만드는 방법에 대 한 자세한 내용은 [이 링크](https://www.tensorflow.org/guide/saved_model) 를 참조 하세요.

TensorFlow가 제공 하는 [DockerHub](https://registry.hub.docker.com/r/tensorflow/serving/tags)의 "Tags" 아래에 나열 된 TensorFlow 버전을 지원 합니다.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='flowers',                        # Name of the registered model in your workspace.
                       model_path='./flowers_model',                # Local Tensorflow SavedModel folder to upload and register as a model.
                       model_framework=Model.Framework.TENSORFLOW,  # Framework used to create the model.
                       model_framework_version='1.14.0',            # Version of Tensorflow used to create the model.
                       description='Flowers model')

service_name = 'tensorflow-flower-service'
service = Model.deploy(ws, service_name, [model])
```

## <a name="onnx-models"></a>ONNX 모델

ONNX 모델 등록 및 배포는 모든 ONNX 유추 그래프에 대해 지원 됩니다. 전처리 및 postprocess 단계는 현재 지원 되지 않습니다.

MNIST ONNX 모델을 등록 하 고 배포 하는 방법의 예는 다음과 같습니다.

```python
from azureml.core import Model

model = Model.register(workspace=ws,
                       model_name='mnist-sample',                  # Name of the registered model in your workspace.
                       model_path='mnist-model.onnx',              # Local ONNX model to upload and register as a model.
                       model_framework=Model.Framework.ONNX ,      # Framework used to create the model.
                       model_framework_version='1.3',              # Version of ONNX used to create the model.
                       description='Onnx MNIST model')

service_name = 'onnx-mnist-service'
service = Model.deploy(ws, service_name, [model])
```

모델 점수를 매기는 [웹 서비스로 배포 된 Azure Machine Learning 모델 사용](https://docs.microsoft.com/azure/machine-learning/how-to-consume-web-service)을 참조 하세요. 많은 ONNX 프로젝트에서 protobuf 파일을 사용 하 여 저장소 학습 및 유효성 검사 데이터를 조밀 서비스에서 예상 하는 데이터 형식을 파악 하기 어려울 수 있습니다. 모델 개발자는 개발자를 위한 문서를 만들어야 합니다.

* 입력 형식 (JSON 또는 이진)
* 입력 데이터 모양 및 유형 (예: shape [100100, 3]의 float 배열)
* 도메인 정보 (예를 들어, 이미지, 색 공간, 구성 요소 순서 및 값의 정규화 여부)

Pytorch를 사용 하는 경우 [Pytorch에서 ONNX로 모델을 내보내면](https://github.com/onnx/tutorials/blob/master/tutorials/PytorchOnnxExport.ipynb) 변환 및 제한 사항에 대 한 세부 정보가 포함 됩니다. 

## <a name="scikit-learn-models"></a>Scikit-모델 배우기

모든 기본 제공 scikit 모델 유형에 대해서는 코드 모델 배포가 지원 되지 않습니다.

추가 코드 없이 추가 코드 없이 등록 하 고 배포 하는 방법의 예는 다음과 같습니다.

```python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = Model.register(workspace=ws,
                       model_name='my-sklearn-model',                # Name of the registered model in your workspace.
                       model_path='./sklearn_regression_model.pkl',  # Local file to upload and register as a model.
                       model_framework=Model.Framework.SCIKITLEARN,  # Framework used to create the model.
                       model_framework_version='0.19.1',             # Version of scikit-learn used to create the model.
                       resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5),
                       description='Ridge regression model to predict diabetes progression.',
                       tags={'area': 'diabetes', 'type': 'regression'})
                       
service_name = 'my-sklearn-service'
service = Model.deploy(ws, service_name, [model])
```

> [!NOTE]
> Predict_proba를 지 원하는 모델은 기본적으로 해당 메서드를 사용 합니다. 이를 재정의 하 여 예측을 사용 하려면 아래와 같이 게시물 본문을 수정할 수 있습니다.

```python
import json


input_payload = json.dumps({
    'data': [
        [ 0.03807591,  0.05068012,  0.06169621, 0.02187235, -0.0442235,
         -0.03482076, -0.04340085, -0.00259226, 0.01990842, -0.01764613]
    ],
    'method': 'predict'  # If you have a classification model, the default behavior is to run 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

> [!NOTE]
> 이러한 종속성은 미리 작성 된 scikit-학습 유추 컨테이너에 포함 되어 있습니다.

```yaml
    - dill
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
    - joblib
    - pandas
    - scipy
    - sklearn_pandas
```
## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
