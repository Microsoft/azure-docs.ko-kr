---
title: GPU를 사용 하 여 유추를 위한 모델 배포
titleSuffix: Azure Machine Learning
description: 이 문서에서는 Azure Machine Learning를 사용 하 여 GPU 사용 Tensorflow 심층 학습 모델을 웹 서비스로 배포 하는 방법을 설명 합니다. 서비스 및 점수 유추 요청
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: csteegz
ms.reviewer: larryfr
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: e4c2426d5248582a1255b9d3702bdb1e6d046936
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751648"
---
# <a name="deploy-a-deep-learning-model-for-inference-with-gpu"></a>GPU를 사용 하 여 유추를 위한 심층 학습 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning를 사용 하 여 GPU 사용 모델을 웹 서비스로 배포 하는 방법을 설명 합니다. 이 문서의 정보는 AKS (Azure Kubernetes Service)에 모델을 배포 하는 방법을 기반으로 합니다. AKS 클러스터는 유추를 위해 모델에서 사용 하는 GPU 리소스를 제공 합니다.

유추 또는 모델 점수 매기기는 배포 된 모델을 사용 하 여 예측을 수행 하는 단계입니다. Cpu 대신 Gpu를 사용 하면 매우 병렬화 계산에서 성능상의 이점이 있습니다.

> [!IMPORTANT]
> 웹 서비스 배포의 경우 GPU 유추는 Azure Kubernetes Service 에서만 지원 됩니다. __기계 학습 파이프라인__을 사용 하는 유추의 경우 gpu는 Azure Machine Learning 계산 에서만 지원 됩니다. ML 파이프라인 사용에 대 한 자세한 내용은 [일괄 처리 예측 실행](how-to-use-parallel-run-step.md)을 참조 하세요. 

> [!TIP]
> 이 문서의 코드 조각에서는 TensorFlow 모델을 사용 하지만 Gpu를 지 원하는 모든 기계 학습 프레임 워크에 정보를 적용할 수 있습니다.

> [!NOTE]
> 이 문서의 정보는 [Azure Kubernetes Service에 배포 하는 방법](how-to-deploy-azure-kubernetes-service.md) 문서에 있는 정보를 기반으로 합니다. 이 문서는 일반적으로 AKS에 대 한 배포를 다루는이 문서에서는 GPU 특정 배포에 대해 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

* Azure Machine Learning SDK가 설치 된 Python 개발 환경. 자세한 내용은 [AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)를 참조 하세요.  

* GPU를 사용 하는 등록 된 모델입니다.

    * 모델을 등록 하는 방법에 대 한 자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)를 참조 하세요.

    * 이 문서를 만드는 데 사용 되는 Tensorflow 모델을 만들고 등록 하려면 [Tensorflow 모델 학습 방법](how-to-train-tensorflow.md)을 참조 하세요.

* [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 일반적으로 이해 합니다.

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

기존 작업 영역에 연결 하려면 다음 코드를 사용 합니다.

> [!IMPORTANT]
> 이 코드 조각은 작업 영역 구성이 현재 디렉터리 또는 부모 디렉터리에 저장될 것으로 예상합니다. 작업 영역 만들기에 대한 자세한 내용은 [Azure Machine Learning 작업 영역 만들기 및 관리](how-to-manage-workspace.md)를 참조하세요.   파일에 구성 저장에 대한 자세한 내용은 [작업 영역 구성 파일 만들기](how-to-configure-environment.md#workspace)를 참조 하세요.

```python
from azureml.core import Workspace

# Connect to the workspace
ws = Workspace.from_config()
```

## <a name="create-a-kubernetes-cluster-with-gpus"></a>Gpu를 사용 하 여 Kubernetes 클러스터 만들기

Azure Kubernetes Service는 다양 한 GPU 옵션을 제공 합니다. 모델 유추에 이러한 항목 중 하나를 사용할 수 있습니다. 기능 및 비용에 대 한 전체 분석은 [N 시리즈 vm의 목록을](https://azure.microsoft.com/pricing/details/virtual-machines/linux/#n-series) 참조 하세요.

다음 코드에서는 작업 영역에 대 한 새 AKS 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import ComputeTarget, AksCompute
from azureml.exceptions import ComputeTargetException

# Choose a name for your cluster
aks_name = "aks-gpu"

# Check to see if the cluster already exists
try:
    aks_target = ComputeTarget(workspace=ws, name=aks_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    # Provision AKS cluster with GPU machine
    prov_config = AksCompute.provisioning_configuration(vm_size="Standard_NC6")

    # Create the cluster
    aks_target = ComputeTarget.create(
        workspace=ws, name=aks_name, provisioning_configuration=prov_config
    )

    aks_target.wait_for_completion(show_output=True)
```

> [!IMPORTANT]
> Azure는 AKS 클러스터가 있는 한 요금을 청구 합니다. 작업을 완료 하면 AKS 클러스터를 삭제 해야 합니다.

Azure Machine Learning와 함께 AKS를 사용 하는 방법에 대 한 자세한 내용은 [Azure Kubernetes Service에 배포 하는 방법](how-to-deploy-azure-kubernetes-service.md)을 참조 하세요.

## <a name="write-the-entry-script"></a>항목 스크립트 작성

항목 스크립트는 웹 서비스로 전송 된 데이터를 받아 모델에 전달 하 고 점수 매기기 결과를 반환 합니다. 다음 스크립트는 시작 시 Tensorflow 모델을 로드 한 다음 모델을 사용 하 여 데이터의 점수를 매기는 데 사용 됩니다.

> [!TIP]
> 항목 스크립트는 모델에 따라 다릅니다. 예를 들어 스크립트는 모델, 데이터 형식 등에 사용할 프레임 워크를 알고 있어야 합니다.

```python
import json
import numpy as np
import os
import tensorflow as tf

from azureml.core.model import Model


def init():
    global X, output, sess
    tf.reset_default_graph()
    model_root = os.getenv('AZUREML_MODEL_DIR')
    # the name of the folder in which to look for tensorflow model files
    tf_model_folder = 'model'
    saver = tf.train.import_meta_graph(
        os.path.join(model_root, tf_model_folder, 'mnist-tf.model.meta'))
    X = tf.get_default_graph().get_tensor_by_name("network/X:0")
    output = tf.get_default_graph().get_tensor_by_name("network/output/MatMul:0")

    sess = tf.Session()
    saver.restore(sess, os.path.join(model_root, tf_model_folder, 'mnist-tf.model'))


def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    out = output.eval(session=sess, feed_dict={X: data})
    y_hat = np.argmax(out, axis=1)
    return y_hat.tolist()
```

이 파일의 이름은 `score.py` 입니다. 항목 스크립트에 대 한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="define-the-conda-environment"></a>Conda 환경 정의

Conda 환경 파일은 서비스에 대 한 종속성을 지정 합니다. 여기에는 모델과 입력 스크립트 모두에 필요한 종속성이 포함 됩니다. 버전 >= 1.0.45를 pip 종속성으로 지정 해야 합니다. 여기에는 모델을 웹 서비스로 호스트 하는 데 필요한 기능이 포함 되어 있기 때문입니다. 다음 YAML은 Tensorflow 모델에 대 한 환경을 정의 합니다. `tensorflow-gpu`이 배포에 사용 되는 GPU를 사용 하는를 지정 합니다.

```yaml
name: project_environment
dependencies:
  # The python interpreter version.
  # Currently Azure ML only supports 3.5.2 and later.
- python=3.6.2

- pip:
  # You must list azureml-defaults as a pip dependency
  - azureml-defaults>=1.0.45
  - numpy
  - tensorflow-gpu=1.12
channels:
- conda-forge
```

이 예에서는 파일이로 저장 됩니다 `myenv.yml` .

## <a name="define-the-deployment-configuration"></a>배포 구성 정의

> [!IMPORTANT]
> AKS는 pod에서 Gpu를 공유할 수 있도록 허용 하지 않습니다. GPU 사용 웹 서비스의 복제본은 클러스터에 있는 Gpu 만큼만 사용할 수 있습니다.

배포 구성은 웹 서비스를 실행 하는 데 사용 되는 Azure Kubernetes 서비스 환경을 정의 합니다.

```python
from azureml.core.webservice import AksWebservice

gpu_aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=3,
                                                    cpu_cores=2,
                                                    memory_gb=4)
```

자세한 내용은 AksService에 대 한 참조 설명서를 참조 하세요 [deploy_configuration.](/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none--compute-target-name-none-)

## <a name="define-the-inference-configuration"></a>유추 구성 정의

유추 구성은 GPU를 지 원하는 docker 이미지를 사용 하는 항목 스크립트와 환경 개체를 가리킵니다. 환경 정의에 사용 되는 YAML 파일에는 웹 서비스로 모델을 호스트 하는 데 필요한 기능이 포함 되어 있으므로 1.0.45 = >버전의 azureml 종속성이 pip 종속성으로 나열 되어야 합니다.

```python
from azureml.core.model import InferenceConfig
from azureml.core.environment import Environment, DEFAULT_GPU_IMAGE

myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
myenv.docker.base_image = DEFAULT_GPU_IMAGE
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
```

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.
자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py)에 대 한 참조 설명서를 참조 하세요.

## <a name="deploy-the-model"></a>모델 배포

AKS 클러스터에 모델을 배포 하 고 서비스를 만들 때까지 기다립니다.

```python
from azureml.core.model import Model

# Name of the web service that is deployed
aks_service_name = 'aks-dnn-mnist'
# Get the registerd model
model = Model(ws, "tf-dnn-mnist")
# Deploy the model
aks_service = Model.deploy(ws,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=gpu_aks_config,
                           deployment_target=aks_target,
                           name=aks_service_name)

aks_service.wait_for_deployment(show_output=True)
print(aks_service.state)
```

자세한 내용은 [모델](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대 한 참조 설명서를 참조 하세요.

## <a name="issue-a-sample-query-to-your-service"></a>서비스에 대 한 샘플 쿼리 실행

배포 된 모델에 테스트 쿼리를 보냅니다. Jpeg 이미지를 모델로 보내면 이미지 점수가 표시 됩니다. 다음 코드 샘플에서는 테스트 데이터를 다운로드 한 다음 서비스에 보낼 무작위 테스트 이미지를 선택 합니다.

```python
# Used to test your webservice
import os
import urllib
import gzip
import numpy as np
import struct
import requests

# load compressed MNIST gz files and return numpy arrays
def load_data(filename, label=False):
    with gzip.open(filename) as gz:
        struct.unpack('I', gz.read(4))
        n_items = struct.unpack('>I', gz.read(4))
        if not label:
            n_rows = struct.unpack('>I', gz.read(4))[0]
            n_cols = struct.unpack('>I', gz.read(4))[0]
            res = np.frombuffer(gz.read(n_items[0] * n_rows * n_cols), dtype=np.uint8)
            res = res.reshape(n_items[0], n_rows * n_cols)
        else:
            res = np.frombuffer(gz.read(n_items[0]), dtype=np.uint8)
            res = res.reshape(n_items[0], 1)
    return res

# one-hot encode a 1-D array
def one_hot_encode(array, num_of_classes):
    return np.eye(num_of_classes)[array.reshape(-1)]

# Download test data
os.makedirs('./data/mnist', exist_ok=True)
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz', filename='./data/mnist/test-images.gz')
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz', filename='./data/mnist/test-labels.gz')

# Load test data from model training
X_test = load_data('./data/mnist/test-images.gz', False) / 255.0
y_test = load_data('./data/mnist/test-labels.gz', True).reshape(-1)

# send a random row from the test set to score
random_index = np.random.randint(0, len(X_test)-1)
input_data = "{\"data\": [" + str(list(X_test[random_index])) + "]}"

api_key = aks_service.get_keys()[0]
headers = {'Content-Type': 'application/json',
           'Authorization': ('Bearer ' + api_key)}
resp = requests.post(aks_service.scoring_uri, input_data, headers=headers)

print("POST to url", aks_service.scoring_uri)
print("label:", y_test[random_index])
print("prediction:", resp.text)
```

클라이언트 응용 프로그램을 만드는 방법에 대 한 자세한 내용은 [클라이언트를 만들어 배포 된 웹 서비스 사용을](how-to-consume-web-service.md)참조 하세요.

## <a name="clean-up-the-resources"></a>리소스 정리

이 예를 위해 특별히 AKS 클러스터를 만든 경우 완료 한 후 리소스를 삭제 합니다.

> [!IMPORTANT]
> Azure는 AKS 클러스터의 배포 기간에 따라 청구 합니다. 작업을 완료 한 후에는 정리 해야 합니다.

```python
aks_service.delete()
aks_target.delete()
```

## <a name="next-steps"></a>다음 단계

* [FPGA에 모델 배포](how-to-deploy-fpga-web-service.md)
* [ONNX를 사용 하 여 모델 배포](concept-onnx.md#deploy-onnx-models-in-azure)
* [Tensorflow DNN 모델 학습](how-to-train-tensorflow.md)
