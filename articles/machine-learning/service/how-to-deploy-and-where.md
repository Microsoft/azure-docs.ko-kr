---
title: 방법 및 모델을 배포할 수 있는 위치
titleSuffix: Azure Machine Learning service
description: 다음을 포함하는 Azure Machine Learning 서비스 모델을 배포하는 방법 및 위치를 알아봅니다. Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 Field-Programmable Gate Array
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 05/31/2019
ms.custom: seoapril2019
ms.openlocfilehash: b5a08b9b998f8d0b30091af016af564e836d4651
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331667"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

기계 학습 모델을 웹 서비스로 Azure 클라우드에서 또는 IoT Edge 장치에 배포 하는 방법에 알아봅니다. 

워크플로 관계 없이 비슷합니다 [배포할](#target) 모델:

1. 모델을 등록합니다.
1. 배포 준비 (자산, 사용량 계산 대상 지정)
1. 계산 대상에 모델을 배포 합니다.
1. 또한 웹 서비스 호출, 배포 된 모델을 테스트 합니다.

배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- 모델. 학습된 된 모델이 없는, 모델을 사용할 수 있습니다 & 종속성 파일에서 제공 [이 자습서](https://aka.ms/azml-deploy-cloud)합니다.

- 합니다 [Machine Learning 서비스에 대 한 Azure CLI extension](reference-azure-machine-learning-cli.md)를 [Azure Machine Learning Python SDK](https://aka.ms/aml-sdk), 또는 [Azure Machine Learning Visual Studio Code 확장](how-to-vscode-tools.md)합니다.

## <a id="registermodel"></a> 모델 등록

모델을 구성 하는 하나 이상의 파일에 대 한 등록 된 모델 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장 된 모델에 있으면 등록할 수 있습니다 작업 영역에서 단일 모델로 합니다. 등록이 완료 되 면 다음 또는 등록 된 모델을 배포할 수 있으며 등록 된 모든 파일을 수신 합니다.

기계 학습 모델은 Azure Machine Learning 작업 영역에 등록 됩니다. 모델을 Azure Machine Learning에서 가져올 수 있습니다 또는 다른 곳에서 가져올 수 있습니다. 다음 예제에서는 파일에서 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-experiment-run"></a>실험을 실행 하는 모델을 등록

+ **SDK를 사용 하 여 Scikit-learn 예제**
  ```python
  model = run.register_model(model_name='sklearn_mnist', model_path='outputs/sklearn_mnist_model.pkl')
  print(model.name, model.id, model.version, sep='\t')
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면 설정 `model_path` 파일이 포함 된 디렉터리에 있습니다.

+ **CLI를 사용 하 여**

  ```azurecli-interactive
  az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면 설정 `--asset-path` 파일이 포함 된 디렉터리에 있습니다.

+ **VS Code를 사용 하 여**

  모델 파일 또는 폴더를 사용 하 여 모델을 등록 합니다 [VS Code](how-to-vscode-tools.md#deploy-and-manage-models) 확장 합니다.

### <a name="register-an-externally-created-model"></a>외부에서 만든된 모델을 등록 합니다.

[!INCLUDE [trusted models](../../../includes/machine-learning-service-trusted-model.md)]

외부에서 만든된 모델을 제공 하 여 등록할 수 있습니다는 **로컬 경로** 모델입니다. 폴더 또는 단일 파일을 제공할 수 있습니다.

+ **Python SDK를 사용 하 여 ONNX 예제:**
  ```python
  onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
  urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
  !tar xvzf mnist.tar.gz
  
  model = Model.register(workspace = ws,
                         model_path ="mnist/model.onnx",
                         model_name = "onnx_mnist",
                         tags = {"onnx": "demo"},
                         description = "MNIST image classification CNN from ONNX Model Zoo",)
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면 설정 `model_path` 파일이 포함 된 디렉터리에 있습니다.

+ **CLI를 사용 하 여**
  ```azurecli-interactive
  az ml model register -n onnx_mnist -p mnist/model.onnx
  ```

  > [!TIP]
  > 모델 등록에 여러 파일을 포함 하려면 설정 `-p` 파일이 포함 된 디렉터리에 있습니다.

**예상 시간**: 약 10초

자세한 내용은 [Model 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

<a name="target"></a>

## <a name="choose-a-compute-target"></a>계산 대상을 선택 하십시오.

다음 대상 계산 또는 계산 리소스, 웹 서비스 배포를 호스팅에 사용할 수 있습니다. 

[!INCLUDE [aml-compute-target-deploy](../../../includes/aml-compute-target-deploy.md)]

## <a name="prepare-to-deploy"></a>배포 준비

웹 서비스로 배포 하는 유추 구성을 만들어야 합니다 (`InferenceConfig`) 및 배포 구성. 모델 점수 매기기 또는 유추 하는 단계 프로덕션 데이터에 가장 일반적으로 예측에 대 한 배포 된 모델이 사용 되는 위치입니다. 유추 구성에서 스크립트 및 모델을 제공 하는 데 필요한 종속성을 지정 합니다. 배포 구성에서 계산 대상에 모델을 제공 하는 방법의 세부 정보를 지정 합니다.


### <a id="script"></a> 1. 사용자 엔트리 스크립트가 종속성 정의

엔트리 스크립트가 배포 된 웹 서비스에 전송 된 데이터를 받고 모델에 전달 합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. **이 스크립트는 해당 모델**; 모델을 사용 하 고 반환 하는 데이터를 이해 해야 합니다.

스크립트를 로드 하 고 모델을 실행 하는 두 함수를 포함 되어 있습니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 웹 서비스에 대 한 Docker 컨테이너를 시작할 때 한 번만 실행 됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="what-is-getmodelpath"></a>Get_model_path 란?

모델을 등록 하면 레지스트리에서 모델 관리에 사용 되는 모델 이름을 제공 합니다. 사용 하 여이 이름을 사용 하는 [Model.get_model_path()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#get-model-path-model-name--version-none---workspace-none-) 로컬 파일 시스템에 모델 파일의 경로를 검색 합니다. 폴더 또는 파일의 컬렉션을 등록 하는 경우이 API 해당 파일이 포함 된 디렉터리 경로 반환 합니다.

모델을 등록 하면 모델 위치를 로컬 또는 서비스 배포 중에 해당 하는 이름을 제공 합니다.

아래 예제에서는 돌아갑니다 경로 라는 단일 파일 `sklearn_mnist_model.pkl` (이름으로 등록 된는 `sklearn_mnist`):

```python
model_path = Model.get_model_path('sklearn_mnist')
``` 

#### <a name="optional-automatic-swagger-schema-generation"></a>(선택 사항) 자동으로 Swagger 스키마 생성

자동으로 웹 서비스에 대 한 스키마를 생성, 입력의 샘플을 제공 및/또는 출력에 정의 된 형식이 개체 형식 및 샘플 중 하나에 대 한 생성자를 자동으로 스키마를 만드는 사용 됩니다. Azure Machine Learning 서비스를 만듭니다는 [OpenAPI](https://swagger.io/docs/specification/about/) 배포 하는 동안 웹 서비스에 대 한 (Swagger) 사양입니다.

형식은 현재 지원 됩니다.

* `pandas`
* `numpy`
* `pyspark`
* 표준 Python 개체

스키마 생성을 사용 하려면 포함는 `inference-schema` conda 환경 파일에 패키지 합니다. 다음 예제에서는 `[numpy-support]` 엔트리 스크립트가 numpy 매개 변수 형식을 사용 하므로: 

#### <a name="example-dependencies-file"></a>예제 종속성 파일
다음 YAML은 유추 Conda 종속성 파일의 예입니다.

```YAML
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn==0.20.0
    - inference-schema[numpy-support]
```

자동 스키마 생성, 입력 스크립트를 사용 하려는 경우 **해야 합니다** 가져오기는 `inference-schema` 패키지 있습니다. 

입력을 정의 하 고 출력의 샘플 형식 합니다 `input_sample` 및 `output_sample` 변수를 나타내는 웹 서비스에 대 한 요청 및 응답 형식입니다. 이러한 샘플을 사용 하 여 입력에서 및 데코레이터 함수에서 출력 된 `run()` 함수입니다. scikit-스키마 생성을 사용 하 여 아래 예제에 알아봅니다.

> [!TIP]
> 서비스를 배포한 후 사용 된 `swagger_uri` 스키마 JSON 문서를 검색할 속성입니다.

#### <a name="example-entry-script"></a>예제 항목 스크립트

다음 예에서는 허용 하 고 JSON 데이터를 반환 하는 방법을 보여 줍니다.

```python
#example: scikit-learn and Swagger
import json
import numpy as np
from sklearn.externals import joblib
from sklearn.linear_model import Ridge
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType

def init():
    global model
    # note here "sklearn_regression_model.pkl" is the name of the model registered under
    # this is a different behavior than before when the code is run locally, even though the code is the same.
    model_path = Model.get_model_path('sklearn_regression_model.pkl')
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = np.array([[10,9,8,7,6,5,4,3,2,1]])
output_sample = np.array([3726.995])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

#### <a name="example-script-with-dictionary-input-support-consumption-from-power-bi"></a>사전 입력 (Power BI에서 지원 소비)를 사용 하 여 예제 스크립트

다음 예제에서는 입력된 데이터를 정의 하는 방법 < 키: 값 > 데이터 프레임을 사용 하 여 사전입니다. 이 메서드는 Power BI에서 배포 된 웹 서비스 사용에 대 한 지원 ([Power BI에서 웹 서비스를 사용 하는 방법에 자세히 알아보려면](https://docs.microsoft.com/power-bi/service-machine-learning-integration)):

```python
import json
import pickle
import numpy as np
import pandas as pd
import azureml.train.automl
from sklearn.externals import joblib
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType
from inference_schema.parameter_types.pandas_parameter_type import PandasParameterType

def init():
    global model
    model_path = Model.get_model_path('model_name')   # replace model_name with your actual model name, if needed
    # deserialize the model file back into a sklearn model
    model = joblib.load(model_path)

input_sample = pd.DataFrame(data=[{
              "input_name_1": 5.1,         # This is a decimal type sample. Use the data type that reflects this column in your data
              "input_name_2": "value2",    # This is a string type sample. Use the data type that reflects this column in your data
              "input_name_3": 3            # This is a integer type sample. Use the data type that reflects this column in your data
            }])

output_sample = np.array([0])              # This is a integer type sample. Use the data type that reflects the expected result

@input_schema('data', PandasParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(data):
    try:
        result = model.predict(data)
        # you can return any datatype as long as it is JSON-serializable
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```
자세한 예제 스크립트의 경우 다음 예제를 참조 하세요.

* Pytorch: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-pytorch)
* TensorFlow: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-tensorflow)
* Keras: [https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning/train-hyperparameter-tune-deploy-with-keras)
* ONNX: [https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx/)
* 이진 데이터에 대 한 점수 매기기: [웹 서비스를 사용 하는 방법](how-to-consume-web-service.md)

### <a name="2-define-your-inferenceconfig"></a>2. 프로그램 InferenceConfig 정의

유추 구성 예측 하는 모델을 구성 하는 방법에 설명 합니다. 다음 예제는 유추 구성을 만드는 방법을 보여 줍니다.

```python
inference_config = InferenceConfig(source_directory="C:/abc",
                                   runtime= "python",
                                   entry_script="x/y/score.py",
                                   conda_file="env/myenv.yml")
```

### <a name="cli-example-of-inferenceconfig"></a>InferenceConfig의 CLI 예제
```JSON
{
   "entryScript": "x/y/score.py",
   "runtime": "python",
   "condaFile": "env/myenv.yml",
   "sourceDirectory":"C:/abc",
}
```
```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예제에서는 구성에는 다음 항목이 포함 됩니다.

* 유추 하는 데 필요한 자산을 포함 하는 디렉터리
* 이 모델에서는 Python는
* 합니다 [엔트리 스크립트가](#script), 배포 된 서비스에 전송 하는 웹 요청을 처리 하는 데 사용 되는
* 유추 하는 데 필요한 Python 패키지를 설명 하는 conda 파일

InferenceConfig 기능에 대 한 내용은 참조는 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스 참조 합니다.

유추 구성을 사용 하 여 사용자 지정 Docker 이미지 사용에 대 한 내용은 참조 하세요 [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법을](how-to-deploy-custom-docker-image.md)합니다.

### <a name="3-define-your-deployment-configuration"></a>3. 배포 구성 정의

를 배포 하기 전에 배포 구성을 정의 해야 합니다. 배포 구성 웹 서비스를 호스트 하는 계산 대상에 따라 다릅니다. 예를 들어, 로컬로 배포 하는 경우 서비스는 요청을 수락 하는 위치는 포트를 지정 해야 합니다.

계산 리소스를 만들 또한 해야 합니다. 예를 들어 있지 않으면 Azure Kubernetes Service 연결 작업 영역입니다.

다음 표에서 각 계산 대상에 대 한 배포 구성을 만드는 예제입니다.

| 계산 대상 | 배포 구성 예제 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instance | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

다음 섹션에서는 배포 구성 만들기를 사용 하 여 웹 서비스를 배포 하는 방법을 보여 줍니다.

### <a name="optional-profile-your-model"></a>선택 사항: 모델을 프로 파일링
서비스 모델을 배포 하기 전에 최적의 CPU 및 메모리 요구 사항을 확인 하는 프로 파일링 하는 것이 좋습니다.

사용자 프로필 모델 SDK 또는 CLI를 사용 하 여 수행할 수 있습니다.

자세한 내용은 여기 SDK 설명서를 확인할 수 있습니다. https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#profile-workspace--profile-name--models--inference-config--input-data-

실행 개체 모델 프로 파일링 결과 내보냅니다.
모델 프로필 스키마에 대 한 세부 사항은 확인할 수 있습니다. https://docs.microsoft.com/python/api/azureml-core/azureml.core.profile.modelprofile?view=azure-ml-py

## <a name="deploy-to-target"></a>대상에 배포

### <a id="local"></a> 로컬 배포

로컬에 배포 하려면 해야 **Docker가 설치 되어** 로컬 컴퓨터에 있습니다.

+ **SDK를 사용 하 여**

  ```python
  deployment_config = LocalWebservice.deploy_configuration(port=8890)
  service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI를 사용 하 여**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -ic inferenceconfig.json -dc deploymentconfig.json
  ```

### <a id="aci"></a> Azure Container Instances (DEVTEST)

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 웹 서비스로 배포합니다.
- 모델을 빠르게 배포하고 유효성을 검사해야 합니다.
- 개발 중인 모델을 테스트합니다. 

ACI에 대 한 할당량 및 지역 가용성을 확인, 참조를 [할당량 및 Azure Container Instances에 대 한 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서.

+ **SDK를 사용 하 여**

  ```python
  deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  ```

+ **CLI를 사용 하 여**

  ```azurecli-interactive
  az ml model deploy -m sklearn_mnist:1 -n aciservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```


+ **VS Code를 사용 하 여**

  하 [VS Code를 사용 하 여 모델을 배포할](how-to-vscode-tools.md#deploy-and-manage-models) ACI 컨테이너 즉석에서 생성 되므로 미리 테스트 하는 ACI 컨테이너를 만들 필요가 없습니다.

자세한 내용은 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="aks"></a>Azure Kubernetes Service (DEVTEST 및 프로덕션)

기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.

<a id="deploy-aks"></a>

연결 된 AKS 클러스터에 이미 있는 경우에 배포할 수 있습니다. 생성 또는 AKS 클러스터를 연결 하지 않은 경우 프로세스에 따라 <a href="#create-attach-aks">새 AKS 클러스터를 만들</a>합니다.

+ **SDK를 사용 하 여**

  ```python
  aks_target = AksCompute(ws,"myaks")
  # If deploying to a cluster configured for dev/test, ensure that it was created with enough
  # cores and memory to handle this deployment configuration. Note that memory is also used by
  # things such as dependencies and AML components.
  deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
  service = Model.deploy(ws, "aksservice", [model], inference_config, deployment_config, aks_target)
  service.wait_for_deployment(show_output = True)
  print(service.state)
  print(service.get_logs())
  ```

+ **CLI를 사용 하 여**

  ```azurecli-interactive
  az ml model deploy -ct myaks -m mymodel:1 -n aksservice -ic inferenceconfig.json -dc deploymentconfig.json
  ```

+ **VS Code를 사용 하 여**

  할 수도 있습니다 [VS Code 확장을 통해 AKS에 배포](how-to-vscode-tools.md#deploy-and-manage-models), AKS 클러스터를 미리 구성 해야 하지만 합니다.

AKS 배포 및에서 자동 크기 조정에 대 한 자세한 정보는 [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice) 참조 합니다.

#### 새 AKS 클러스터 만들기<a id="create-attach-aks"></a>
**예상 시간**: 약 20분입니다.

AKS 클러스터는 한 번 연결 또는 만들기 작업 영역에 대 한 처리 합니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다. 클러스터 또는 포함 하는 리소스 그룹을 삭제 하면 다음에 배포 해야 새 클러스터를 만들어야 합니다. 작업 영역에 연결 된 여러 AKS 클러스터를 사용할 수 있습니다.

개발, 유효성 검사 및 테스트에 대 한 AKS 클러스터를 만들려는 경우 설정한 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` 사용 하는 경우 [ `provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)합니다. 이 설정을 사용 하 여 만든 클러스터는 하나의 노드만 권한만 갖습니다.

> [!IMPORTANT]
> 설정 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` 프로덕션 트래픽 처리에 적합 하지 않은 AKS 클러스터를 만듭니다. 유추 시간이 프로덕션에 만든 클러스터에 보다 길어질 수 있습니다. 내결함성 개발/테스트 클러스터에 대 한 보장 되지 않습니다.
>
> 개발/테스트에 만든 클러스터 두 개 이상의 가상 Cpu를 사용 하는 것이 좋습니다.

다음 예제에서는 새 Azure Kubernetes Service 클러스터를 만드는 방법을 보여 줍니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksComputee.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

Azure Machine Learning SDK 외부에서 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.
* [AKS 클러스터 만들기](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 (포털) 만들기](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

에 대 한 자세한 합니다 `cluster_purpose` 매개 변수를 참조 합니다 [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) 참조 합니다.

> [!IMPORTANT]
> [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우, agent_count 및 vm_size에 대한 사용자 지정 값을 선택하는 경우 agent_count와 vm_size를 곱한 값이 12개 가상 CPU보다 크거나 같아야 합니다. 예를 들어, 4개의 가상 CPU가 있는 "Standard_D3_v2"의 vm_size를 사용하는 경우는 3 이상의 agent_count를 선택해야 합니다.
>
> Azure Machine Learning SDK에는 AKS 클러스터 크기 조정 지원을 제공 하지 않습니다. 클러스터의 노드 크기를 조정 하려면 Azure portal에서 AKS 클러스터에 대 한 UI를 사용 합니다. 클러스터의 VM 크기가 아니라 노드 수를 변경할 수 있습니다.

#### <a name="attach-an-existing-aks-cluster"></a>기존 AKS 클러스터를 연결 합니다.
**예상 시간:** 약 5 분입니다.

Azure 구독에서 AKS 클러스터를 이미 있는 경우 버전 1.12. # #, 이미지를 배포 하려면 사용할 수 있습니다.

> [!WARNING]
> AKS 클러스터에 작업 영역에 연결 하는 경우 클러스터를 사용 하는 설정 하 여는 방법을 정의할 수 있습니다는 `cluster_purpose` 매개 변수입니다.
>
> 설정 하지 않으면 경우는 `cluster_purpose` 매개 변수 또는 집합 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, 클러스터 12 개 이상의 가상 Cpu를 사용할 수 있어야 합니다. 그런 다음입니다.
>
> 설정한 경우 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, 클러스터에 12 가상 Cpu가 필요 하지 않습니다. 그러나 개발/테스트에 대해 구성 된 클러스터를 프로덕션 수준 트래픽에 대 한 적합 한 되지 않으며 유추 시간이 길어질 수 있습니다.

다음 코드는 기존 AKS 1.12를 연결 하는 방법에 설명 합니다. # # 작업 영역에 클러스터:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)
```

에 대 한 자세한 `attack_configuration()`를 참조 합니다 [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-) 참조 합니다.

에 대 한 자세한 합니다 `cluster_purpose` 매개 변수를 참조 합니다 [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py) 참조 합니다.

## <a name="consume-web-services"></a>웹 서비스 사용

모든 배포 된 웹 서비스는 프로그래밍 언어의 다양 한 클라이언트 응용 프로그램을 만들 수 있도록 REST API를 제공 합니다. 서비스에 대 한 인증을 설정한 경우에 요청 헤더에서 토큰으로 서비스 키를 제공 해야 합니다.

### <a name="request-response-consumption"></a>요청-응답 사용

Python에서 서비스를 호출 하는 방법의 예는 다음과 같습니다.
```python
import requests
import json

headers = {'Content-Type':'application/json'}

if service.auth_enabled:
    headers['Authorization'] = 'Bearer '+service.get_keys()[0]

print(headers)
    
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(service.scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

자세한 내용은 [웹 서비스를 사용할 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)를 참조하세요.


### <a id="azuremlcompute"></a> 일괄 처리 유추
Azure Machine Learning Compute 목표 생성 및 Azure Machine Learning 서비스에 의해 관리 됩니다. Azure Machine Learning 파이프라인에서 일괄 처리 예측을 위해 사용할 수 있습니다.

연습은 사용 하 여 Azure Machine Learning Compute batch 유추 읽기를 [일괄 처리 예측을 실행 하는 방법을](how-to-run-batch-predictions.md) 문서.

### <a id="iotedge"></a> IoT Edge 유추
에 지에 배포 하는 것에 대 한 지원은 미리 보기로 제공 됩니다. 자세한 내용은 참조는 [IoT Edge 모듈로 Azure Machine Learning 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning) 문서.


## <a id="update"></a> 웹 서비스를 업데이트 합니다.

새 모델을 만들 때 새 모델을 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에는 새 모델을 사용 하도록 웹 서비스를 업데이트 하는 방법을 보여 줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import Model

# register new model
new_model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)

service_name = 'myservice'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# Update to new model(s)
service.update(models = [new_model])
print(service.state)
print(service.get_logs())
```

## <a name="clean-up-resources"></a>리소스 정리
배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 참조 설명서를 참조 하세요 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), 및 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)합니다.

## <a name="next-steps"></a>다음 단계
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)

