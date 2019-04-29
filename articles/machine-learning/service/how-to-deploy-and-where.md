---
title: 방법 및 모델을 배포할 수 있는 위치
titleSuffix: Azure Machine Learning service
description: 다음을 포함하는 Azure Machine Learning 서비스 모델을 배포하는 방법 및 위치를 알아봅니다. Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 Field-Programmable Gate Array
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: a6ef53d56fa293791658b37b16cbaff94aee6ef3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819827"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Azure Machine Learning Services를 사용하여 모델 배포

이 문서에서는 모델을 Azure 클라우드에서 또는 IoT Edge 디바이스에 웹 서비스로 배포하는 방법을 알아봅니다. 

## <a name="compute-targets-for-deployment"></a>배포에 대 한 목표를 계산 합니다.

다음 위치에 학습 된 모델을 배포 하는 Azure Machine Learning SDK를 사용 합니다.

| 계산 대상 | 배포 유형 | 설명 |
| ----- | ----- | ----- |
| [AKS(Azure Kubernetes Service)](#aks) | 실시간 유추 | 확장성이 뛰어난 프로덕션 배포에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다. |
| [Azure Machine Learning Compute (amlcompute)](#azuremlcompute) | 일괄 처리 유추 | 서버 리스 계산에서 일괄 처리 예측을 실행 합니다. 일반 및 낮은 우선 순위 Vm 지원합니다. |
| [ACI(Azure Container Instances)](#aci) | 테스트 | 개발 또는 테스트에 적합합니다. **프로덕션 워크 로드에 적합 하지 않습니다.** |
| [Azure IoT Edge](#iotedge) | (미리 보기) IoT 모듈 | IoT 디바이스에서 모델을 배포합니다. 디바이스에서 추론이 발생합니다. |
| [FPGA(Field-programmable Gate Array)](#fpga) | (미리 보기) 웹 서비스 | 실시간 추론 시 대기 시간이 매우 짧습니다. |

## <a name="deployment-workflow"></a>배포 워크플로

모델을 배포하는 프로세스는 모든 컴퓨팅 대상에서 유사합니다.

1. 모델을 학습하고 등록합니다.
1. 모델을 사용하는 이미지를 구성하고 등록합니다.
1. 컴퓨팅 대상에 이미지를 배포합니다.
1. 배포 테스트

다음 비디오는 Azure Container Instances에 배포 하는 방법을 보여 줍니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Kwk3]


배포 워크플로에 관련된 개념에 대한 자세한 내용은 [Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조하세요.

## <a name="prerequisites-for-deployment"></a>배포 필수 구성 요소

[!INCLUDE [aml-prereq](../../../includes/aml-prereq.md)]

- 학습된 모델. 학습된 모델이 없는 경우 [모델 학습](tutorial-train-models-with-aml.md) 자습서의 단계를 사용하여 학습을 하고 Azure Machine Learning 서비스에 등록합니다.

    > [!NOTE]
    > Azure Machine Learning 서비스 수 Python 3에서 로드할 수 있는 모든 일반 모델을 사용 하는 동안이 문서의 예제에서는 Python pickle 형식으로 저장 된 모델을 사용 하 여 보여 줍니다.
    >
    > ONNX 모델을 사용하는 방법에 대한 자세한 내용은 [ONNX 및 Azure Machine Learning](how-to-build-deploy-onnx.md) 문서를 참조하세요.

## <a id="registermodel"></a> 학습된 모델 등록

모델 레지스트리는 학습된 모델을 Azure 클라우드에서 저장 및 구성하는 방법입니다. 모델은 Azure Machine Learning 서비스 작업 영역에 등록됩니다. Azure Machine Learning 또는 다른 서비스를 사용하여 모델을 학습할 수 있습니다. 다음 코드 파일에서 모델을 등록, 이름, 태그 및 설명을 설정 하는 방법을 보여 줍니다.

```python
from azureml.core.model import Model

model = Model.register(model_path = "outputs/sklearn_mnist_model.pkl",
                       model_name = "sklearn_mnist",
                       tags = {"key": "0.1"},
                       description = "test",
                       workspace = ws)
```

**예상 시간**: 약 10초

모델을 등록 하는 예제를 보려면 [이미지 분류자를 학습](tutorial-train-models-with-aml.md)합니다.

자세한 내용은 [Model 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

## <a id="configureimage"></a> 이미지 만들기 및 등록

배포된 모델은 이미지로 패키지됩니다. 이미지는 모델을 실행하는 데 필요한 종속성을 포함합니다.

**Azure Container Instance** **Azure Kubernetes Service** 및 **Azure IoT Edge** 배포의 경우 [azureml.core.image.ContainerImage](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py) 클래스를 사용하여 이미지 구성을 만듭니다. 이 이미지 구성은 새 Docker 이미지를 만드는 데 사용됩니다.

이미지 구성을 만들 때 사용할 수 있습니다는 __기본 이미지__ Azure Machine Learning 서비스에서 제공 또는 __사용자 지정 이미지__ 제공 하는 합니다.

다음 코드는 새 이미지 구성을 만드는 방법을 보여 줍니다.

```python
from azureml.core.image import ContainerImage

# Image configuration
image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                 runtime = "python",
                                                 conda_file = "myenv.yml"}
                                                 )
```

**예상 시간**: 약 10초

이 예제의 중요 매개 변수를 다음 표에서 설명합니다.

| 매개 변수 | 설명 |
| ----- | ----- |
| `execution_script` | 서비스에 제출되는 요청을 수신하는 데 사용되는 Python 스크립트를 지정합니다. 이 예제에서 스크립트는 `score.py` 파일에 포함됩니다. 자세한 내용은 [실행 스크립트](#script) 섹션을 참조하세요. |
| `runtime` | 이미지는 Python을 사용함을 나타냅니다. 다른 옵션은 `spark-py`로, Apache Spark와 함께 Python을 사용합니다. |
| `conda_file` | conda 환경 파일을 제공하는 데 사용됩니다. 이 파일은 배포된 모델에 대한 conda 환경을 정의합니다. 이 파일을 만드는 방법에 대한 자세한 내용은 [환경 파일(myenv.yml) 만들기](tutorial-deploy-models-with-aml.md#create-environment-file)를 참조하세요. |

이미지 구성 프로그램을 만드는 예제를 참조 하세요 [이미지 분류자를 배포](tutorial-deploy-models-with-aml.md)합니다.

자세한 내용은 [ContainerImage 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

### <a id="customimage"></a> 사용자 지정 이미지를 사용 하 여

사용자 지정 이미지를 사용 하는 경우 이미지에는 다음 요구 사항을 충족 해야 합니다.

* Ubuntu 16.04 이상입니다.
* Conda 4.5. # 이상.
* Python 3.5. # 또는 3.6. #.

사용자 지정 이미지를 사용 하려면 설정의 `base_image` 이미지의 주소로 이미지 구성의 속성입니다. 다음 예제에서는 두 공용 및 개인 Azure 컨테이너 레지스트리에서 이미지를 사용 하는 방법을 보여 줍니다.

```python
# use an image available in public Container Registry without authentication
image_config.base_image = "mcr.microsoft.com/azureml/o16n-sample-user-base/ubuntu-miniconda"

# or, use an image available in a private Container Registry
image_config.base_image = "myregistry.azurecr.io/mycustomimage:1.0"
image_config.base_image_registry.address = "myregistry.azurecr.io"
image_config.base_image_registry.username = "username"
image_config.base_image_registry.password = "password"
```

Azure Container Registry에 이미지 업로드에 대 한 자세한 내용은 참조 하세요. [개인 Docker 컨테이너 레지스트리로 이미지 밀어넣기](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-docker-cli)합니다.

모델은 Azure Machine Learning Compute에서 학습을 사용 하 여 __1.0.22 버전 이상__ Azure Machine Learning SDK의 이미지를 학습 하는 동안 만들어집니다. 다음 예제에서는이 이미지를 사용 하는 방법을 보여 줍니다.

```python
# Use an image built during training with SDK 1.0.22 or greater
image_config.base_image = run.properties["AzureML.DerivedImageName"]
```

### <a id="script"></a> 실행 스크립트

실행 스크립트는 배포된 이미지에 제출된 데이터를 수신한 후 모델에 전달합니다. 그런 후 모델이 반환한 응답을 수락한 후 클라이언트에 반환합니다. **이 스크립트는 해당 모델**; 모델을 사용 하 고 반환 하는 데이터를 이해 해야 합니다. 이미지 분류 모델을 사용 하는 예제 스크립트를 참조 하세요 [이미지 분류자를 배포](tutorial-deploy-models-with-aml.md)합니다.

스크립트를 로드 하 고 모델을 실행 하는 두 함수를 포함 되어 있습니다.

* `init()`: 일반적으로 이 함수는 모델을 전역 개체에 로드합니다. 이 함수는 Docker 컨테이너를 시작할 때 한 번만 실행됩니다.

* `run(input_data)`: 이 함수는 모델을 사용하여 입력 데이터를 기반으로 값을 예측합니다. 실행에 대한 입력 및 출력은 일반적으로 serialization 및 deserialization용으로 JSON을 사용합니다. 원시 이진 데이터를 사용할 수도 있습니다. 모델에 보내기 전에 또는 클라이언트에 반환하기 전에 데이터를 변환할 수 있습니다.

#### <a name="working-with-json-data"></a>JSON 데이터 작업

다음은 JSON 데이터를 수락하고 반환하는 예제 스크립트입니다. `run` 함수는 데이터를 모델이 예상하는 JSON으로 변환하고, 응답을 JSON으로 변환한 후 반환합니다.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

# load the model
def init():
    global model
    # retrieve the path to the model file using the model name
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)

# Passes data to the model and returns the prediction
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    # make prediction
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

#### <a name="working-with-binary-data"></a>이진 데이터 작업

모델이 __이진 데이터__를 수락하는 경우 `AMLRequest`, `AMLResponse` 및 `rawhttp`를 사용합니다. 다음은 이진 데이터를 허용하고 POST 요청에 대해 반전된 바이트를 반환하는 예제 스크립트입니다. GET 요청의 경우 응답 본문의 전체 URL을 반환합니다.

```python
from azureml.contrib.services.aml_request  import AMLRequest, rawhttp
from azureml.contrib.services.aml_response import AMLResponse

def init():
    print("This is init()")

# Accept and return binary data
@rawhttp
def run(request):
    print("This is run()")
    print("Request: [{0}]".format(request))
    # handle GET requests
    if request.method == 'GET':
        respBody = str.encode(request.full_path)
        return AMLResponse(respBody, 200)
    # handle POST requests
    elif request.method == 'POST':
        reqBody = request.get_data(False)
        respBody = bytearray(reqBody)
        respBody.reverse()
        respBody = bytes(respBody)
        return AMLResponse(respBody, 200)
    else:
        return AMLResponse("bad request", 500)
```

> [!IMPORTANT]
> 서비스를 개선을 위해 노력하므로 `azureml.contrib` 네임스페이스는 자주 변경됩니다. 따라서 이 네임 스페이스의 모든 것을 미리 보기로 간주하므로 Microsoft에서 완벽히 지원하지 않아도 됩니다.
>
> 로컬 개발 환경에서 이를 테스트해야 하는 경우 다음 명령을 사용하여 `contrib` 네임스페이스에 구성 요소를 설치할 수 있습니다.
> ```shell
> pip install azureml-contrib-services
> ```

### <a id="createimage"></a> 이미지 등록

일단 이미지 구성을 만들면 이미지를 등록하는 데 사용할 수 있습니다. 이 이미지는 작업 영역에 대한 컨테이너 레지스트리에 저장됩니다. 이미지는 한 번만 만든 후 여러 서버에 배포할 수 있습니다.

```python
# Register the image from the image configuration
image = ContainerImage.create(name = "myimage",
                              models = [model], #this is the model object
                              image_config = image_config,
                              workspace = ws
                              )
```

**예상 시간**: 약 3분입니다.

동일한 이름으로 여러 이미지를 등록할 때 이미지에 자동으로 버전이 지정됩니다. 예를 들어 `myimage`로 등록된 첫 번째 이미지에는 ID `myimage:1`이 할당됩니다. 다음 번에 이미지를 `myimage`로 등록하면 새 이미지의 ID는 `myimage:2`가 됩니다.

자세한 내용은 [ContainerImage 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.containerimage?view=azure-ml-py)에 대한 참조 설명서를 참조하세요.

## <a id="deploy"></a> 웹 서비스로 배포

배포를 가져오는 프로세스는 배포하는 컴퓨팅 대상에 따라 약간 다릅니다. 다음 섹션의 정보를 참조하여 배포 방법을 알아보세요.

| 계산 대상 | 배포 유형 | 설명 |
| ----- | ----- | ----- |
| [AKS(Azure Kubernetes Service)](#aks) | 웹 서비스 (실시간 유추)| 확장성이 뛰어난 프로덕션 배포에 적합합니다. 자동 크기 조정 및 빠른 응답 시간을 제공합니다. |
| [Azure ML 계산](#azuremlcompute) | 웹 서비스 (일괄 처리 유추)| 서버 리스 계산에서 일괄 처리 예측을 실행 합니다. 일반 및 낮은 우선 순위 Vm 지원합니다. |
| [ACI(Azure Container Instances)](#aci) | 웹 서비스 (개발/테스트)| 개발 또는 테스트에 적합합니다. **프로덕션 워크 로드에 적합 하지 않습니다.** |
| [Azure IoT Edge](#iotedge) | (미리 보기) IoT 모듈 | IoT 디바이스에서 모델을 배포합니다. 디바이스에서 추론이 발생합니다. |
| [FPGA(Field-programmable Gate Array)](#fpga) | (미리 보기) 웹 서비스 | 실시간 추론 시 대기 시간이 매우 짧습니다. |

> [!IMPORTANT]
> 모델을 웹 서비스로서 배포할 때 현재, CORS(크로스-원본 자원 공유)는 지원되지 않습니다.

이 예제에서는 사용 섹션 [deploy_from_image](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-), 모델 및 이미지 배포를 수행 하기 전에 등록 해야 합니다. 다른 배포 방법에 대 한 자세한 내용은 참조 하세요. [배포할](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-workspace--name--model-paths--image-config--deployment-config-none--deployment-target-none-) 하 고 [deploy_from_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#deploy-from-model-workspace--name--models--image-config--deployment-config-none--deployment-target-none-)합니다.

### <a id="aci"></a> Azure Container Instances (DEVTEST)에 배포

다음 조건 중 하나 이상에 해당하는 경우 Azure Container Instances를 사용하여 모델을 웹 서비스로 배포합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다. ACI 배포는 5분 이내에 완료됩니다.
- 개발 중인 모델을 테스트합니다. ACI에 대한 할당량 및 지역 가용성을 확인하려면 [Azure Container Instances에 대한 할당량 및 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 문서를 참조하세요.

Azure Container Instances에 배포하려면 다음 단계를 사용합니다.

1. 배포 구성을 정의합니다. 이 구성 모델의 요구 사항에 따라 달라 집니다. 다음 예제에서는 하나의 CPU 코어 및 1GB의 메모리를 사용하는 구성을 정의합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=configAci)]

2. 이 문서의 [이미지 만들기](#createimage) 섹션에서 만든 이미지를 배포하려면 다음 코드를 사용합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-deploy-to-aci/how-to-deploy-to-aci.py?name=option3Deploy)]

    **예상 시간**: 약 5 분입니다.

자세한 내용은 [AciWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="aks"></a> Azure Kubernetes Service (프로덕션)에 배포

모델을 확장성이 뛰어난 프로덕션 웹 서비스로 배포하려면 AKS(Azure Kubernetes Service)를 사용합니다. 기존 AKS 클러스터를 사용하거나 Azure Machine Learning SDK, CLI 또는 Azure Portal을 사용하여 새로운 클러스터를 만들 수 있습니다.

AKS 클러스터 만들기는 작업 영역에 대한 일회성 프로세스입니다. 이 클러스터를 여러 배포에 재사용할 수 있습니다.

> [!IMPORTANT]
> 클러스터를 삭제하면, 다음에 배포할 때 새 클러스터를 만들어야 합니다.

Azure Kubernetes Service는 다음과 같은 기능을 제공합니다.

* 자동 확장
* 로깅
* 모델 데이터 수집
* 웹 서비스에 대한 빠른 응답 시간
* TLS 종료
* Authentication

#### <a name="autoscaling"></a>자동 확장

자동 크기 조정을 설정 하 여 제어할 수 있습니다 `autoscale_target_utilization`, `autoscale_min_replicas`, 및 `autoscale_max_replicas` 는 AKS에 대 한 웹 서비스입니다. 다음 예제에서는 자동 크기 조정을 사용 하는 방법에 설명 합니다.

```python
aks_config = AksWebservice.deploy_configuration(autoscale_enabled=True,
                                                autoscale_target_utilization=30,
                                                autoscale_min_replicas=1,
                                                autoscale_max_replicas=4)
```

의사 결정을 확장 하거나 축소할 현재 컨테이너 복제본 사용률을 기반으로 합니다. (요청을 처리) 된 복제본의 수로 나눈 총 복제본 수는 현재 사용률입니다. 이 번호는 대상 사용률을 초과 하면 더 많은 복제본이 생성 됩니다. 낮은 경우 복제본 감소 됩니다. 기본적으로 대상 사용률은 70%입니다.

복제본을 추가 하는 결정 수행 되며 (약 1 초)을 신속 하 게 구현 됩니다. 복제본 시간도 길어 (약 1 분)을 제거 하려면 결정 합니다. 이 동작은 새 요청이 도착 처리할 수 있도록 하는 경우 잠시 주위 유휴 복제본을 유지 합니다.

다음 코드를 사용 하 여 필요한 복제본을 계산할 수 있습니다.

```python
from math import ceil
# target requests per second
targetRps = 20
# time to process the request (in seconds)
reqTime = 10
# Maximum requests per container
maxReqPerContainer = 1
# target_utilization. 70% in this example
targetUtilization = .7

concurrentRequests = targetRps * reqTime / targetUtilization

# Number of container replicas
replicas = ceil(concurrentRequests / maxReqPerContainer)
```

설정에 대 한 자세한 내용은 `autoscale_target_utilization`, `autoscale_max_replicas`, 및 `autoscale_min_replicas`를 참조 합니다 [AksWebservice.deploy_configuration](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#deploy-configuration-autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--primary-key-none--secondary-key-none--tags-none--properties-none--description-none-) 참조 합니다.

#### <a name="create-a-new-cluster"></a>새 클러스터 만들기

새 Azure Kubernetes Service 클러스터를 만들려면 다음 코드를 사용합니다.

> [!IMPORTANT]
> AKS 클러스터 만들기는 작업 영역에 대한 하나의 프로세스입니다. 만든 후에는 여러 배포에 대해 이 클러스터를 재사용할 수 있습니다. 클러스터 또는 클러스터가 포함된 리소스 그룹을 삭제하면, 다음에 배포할 때 새 클러스터를 만들어야 합니다.
> [`provisioning_configuration()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)의 경우, agent_count 및 vm_size에 대한 사용자 지정 값을 선택하는 경우 agent_count와 vm_size를 곱한 값이 12개 가상 CPU보다 크거나 같아야 합니다. 예를 들어, 4개의 가상 CPU가 있는 "Standard_D3_v2"의 vm_size를 사용하는 경우는 3 이상의 agent_count를 선택해야 합니다.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

**예상 시간**: 약 20분입니다.

#### <a name="use-an-existing-cluster"></a>기존 클러스터 사용

Azure 구독에서 AKS 클러스터를 이미 있는 경우 버전 1.12. # # 및 12 개 이상의 가상 Cpu에 이미지를 배포 하려면 사용할 수 있습니다. 다음 코드는 기존 AKS 1.12를 연결 하는 방법에 설명 합니다. # # 작업 영역에 클러스터:

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'mycluster'

# Attach the cluster to your workgroup
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'mycompute', attach_config)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

**예상 시간**: 약 3분입니다.

Azure Machine Learning SDK 외부에서 AKS 클러스터를 만드는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [AKS 클러스터 만들기](https://docs.microsoft.com/cli/azure/aks?toc=%2Fen-us%2Fazure%2Faks%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [AKS 클러스터 (포털) 만들기](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

#### <a name="deploy-the-image"></a>이미지 배포

이 문서의 [이미지 만들기](#createimage) 섹션에서 만든 이미지를 Azure Kubernetes Server 클러스터에 배포하려면 다음 코드를 사용합니다.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
service = Webservice.deploy_from_image(workspace = ws,
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
service.wait_for_deployment(show_output = True)
print(service.state)
```

**예상 시간**: 약 3분입니다.

자세한 내용은 [AksWebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py) 및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py) 클래스에 대한 참조 설명서를 참조하세요.

### <a id="azuremlcompute"></a> Azure ML Compute를 사용 하 여 유추

Azure ML 계산 대상 생성 및 Azure Machine Learning 서비스에 의해 관리 됩니다. Azure ML 파이프라인에서 일괄 처리 예측을 위해 사용할 수 있습니다.

연습은 사용 하 여 Azure ML Compute batch 유추 읽기를 [일괄 처리 예측을 실행 하는 방법을](how-to-run-batch-predictions.md) 문서.


### <a id="fpga"></a> FPGA(Field-programmable Gate Array)에서 배포

Project Brainwave를 사용하여 실시간 추론 요청에 대해 매우 짧은 대기 시간을 얻을 수 있습니다. Project Brainwave는 Azure 클라우드의 FPGA(Field-programmable gate arrays)에 배포된 DNN(심층 신경망)을 가속화합니다. 일반적으로 사용되는 DNN은 전달 학습을 위한 보조 기능으로 사용하거나 자신의 데이터로 학습된 가중치로 사용자 지정할 수 있습니다.

Project Brainwave를 사용하여 모델을 배포하는 방법에 대한 연습 과정은 [FPGA에 배포](how-to-deploy-fpga-web-service.md) 문서를 참조하세요.

## <a name="define-schema"></a>스키마를 정의 합니다.

사용자 지정 데코레이터에 사용할 수 있습니다 [OpenAPI](https://swagger.io/docs/specification/about/) 사양 생성 및 입력 웹 서비스를 배포 하는 경우 조작을 입력 합니다. 에 `score.py` 파일에 정의 된 형식 개체 중 하나에 대 한 입력 및/또는 생성자에서 출력 샘플을 제공 및 유형 및 샘플은 자동으로 스키마를 만드는 데 사용 됩니다. 형식은 현재 지원 됩니다.

* `pandas`
* `numpy`
* `pyspark`
* standard Python

먼저 필요한 종속성을 확인 합니다 `inference-schema` 패키지에 포함 된 프로그램 `env.yml` conda 환경 파일입니다. 이 예제에서는 합니다 `numpy` 매개 변수 형식 스키마에 대해 하므로 추가 pip `[numpy-support]` 도 설치 됩니다.

```python
%%writefile myenv.yml
name: project_environment
dependencies:
  - python=3.6.2
  - pip:
    - azureml-defaults
    - scikit-learn
    - inference-schema[numpy-support]
```

다음으로 수정 합니다 `score.py` 가져올 파일을 `inference-schema` 패키지 합니다. 입력을 정의 하 고 출력의 샘플 형식 합니다 `input_sample` 및 `output_sample` 변수를 나타내는 웹 서비스에 대 한 요청 및 응답 형식입니다. 이러한 샘플을 사용 하 여 입력에서 및 데코레이터 함수에서 출력 된 `run()` 함수입니다.

```python
%%writefile score.py
import json
import numpy as np
import os
import pickle
from sklearn.externals import joblib
from sklearn.linear_model import LogisticRegression
from azureml.core.model import Model

from inference_schema.schema_decorators import input_schema, output_schema
from inference_schema.parameter_types.numpy_parameter_type import NumpyParameterType


def init():
    global model
    model_path = Model.get_model_path('sklearn_mnist')
    model = joblib.load(model_path)


input_sample = np.array([[1.8]])
output_sample = np.array([43638.88])

@input_schema('data', NumpyParameterType(input_sample))
@output_schema(NumpyParameterType(output_sample))
def run(raw_data):
    data = np.array(json.loads(raw_data)['data'])
    y_hat = model.predict(data)
    return json.dumps(y_hat.tolist())
```

일반 이미지 등록 및 웹 서비스 배포 프로세스와 업데이트 된 후 `score.py` 파일, 서비스에서 Swagger uri를 검색 합니다. 반환이 uri를 요청 합니다 `swagger.json` 파일입니다.

```python
service.wait_for_deployment(show_output=True)
print(service.swagger_uri)
```



새 이미지를 만들 때 새 이미지를 사용하게 하려는 각 서비스를 수동으로 업데이트해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에서는 새 이미지를 사용하도록 웹 서비스를 업데이트하는 방법을 보여줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

자세한 내용은 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 클래스의 참조 설명서를 참조하세요.

## <a name="test-web-service-deployments"></a>웹 서비스 배포를 테스트 합니다.

웹 서비스 배포를 테스트하려면 Webservice 개체의 `run` 메서드를 사용할 수 있습니다. 다음 예제에서는 JSON 문서가 웹 서비스로 설정되고 결과가 표시됩니다. 전송되는 데이터는 모델에 필요한 데이터와 일치해야 합니다. 이 예제에서 데이터 형식은 diabetes 모델에 필요한 입력과 일치합니다.

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = service.run(input_data = test_sample)
print(prediction)
```

웹 서비스는 REST API이므로 다양한 프로그래밍 언어로 클라이언트 애플리케이션을 만들 수 있습니다. 자세한 내용은 [웹 서비스를 사용할 클라이언트 애플리케이션 만들기](how-to-consume-web-service.md)를 참조하세요.

## <a id="update"></a> 웹 서비스 업데이트

새 이미지를 만들 때 새 이미지를 사용하게 하려는 각 서비스를 수동으로 업데이트해야 합니다. 웹 서비스를 업데이트하려면 `update` 메서드를 사용합니다. 다음 코드에서는 새 이미지를 사용하도록 웹 서비스를 업데이트하는 방법을 보여줍니다.

```python
from azureml.core.webservice import Webservice
from azureml.core.image import Image

service_name = 'aci-mnist-3'
# Retrieve existing service
service = Webservice(name = service_name, workspace = ws)

# point to a different image
new_image = Image(workspace = ws, id="myimage2:1")

# Update the image used by the service
service.update(image = new_image)
print(service.state)
```

자세한 내용은 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py) 클래스의 참조 설명서를 참조하세요.

## <a id="iotedge"></a> Azure IoT Edge에 배포

Azure IoT Edge 디바이스는 Azure IoT Edge 런타임을 실행하는 Linux 또는 Windows 기반 디바이스입니다. Azure IoT Hub를 사용 하 여, 기계 학습 모델 이러한 장치에 IoT Edge 모듈로 배포할 수 있습니다. IoT Edge 디바이스에 모델을 배포하면 처리를 위해 클라우드로 데이터를 전송하는 대신 디바이스에서 직접 모델을 사용할 수 있습니다. 응답 시간은 더 빨라지고 데이터 전송은 더 적어집니다.

Azure IoT Edge 모듈은 컨테이너 레지스트리에서 디바이스에 배포됩니다. 모델에서 이미지를 만드는 경우 작업 영역에 대한 컨테이너 레지스트리에 저장됩니다.

> [!IMPORTANT]
> 이 섹션의 정보는 Azure IoT Hub 및 Azure IoT Edge 모듈을 사용 하 여 친숙 한 이미 있다고 가정 합니다. 이 섹션의 정보 중 일부는 Azure Machine Learning 서비스를 하는 동안 edge 장치에 배포 하는 프로세스의 대부분 Azure IoT 서비스에서 발생 합니다.
>
> Azure IoT를 사용 하 여 잘 모르는 경우 [Azure IoT 기본 사항](https://docs.microsoft.com/azure/iot-fundamentals/) 하 고 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) 기본 정보에 대 한 합니다. 특정 작업에 자세히 알아보려면 다음이 섹션의 다른 링크 사용

### <a name="set-up-your-environment"></a>환경 설정

* 개발 환경. 자세한 내용은 [개발 환경을 구성하는 방법](how-to-configure-environment.md) 문서를 참조하세요.

* Azure 구독의 [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md).

* 학습된 모델. 모델 학습에 관한 예제는 [Azure Machine Learning을 사용하여 이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 문서를 참조하세요. 미리 학습된 모델은 [Azure IoT Edge GitHub 리포지토리에 대한 AI 도구 키트](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial)에서 사용할 수 있습니다.

### <a id="getcontainer"></a> 컨테이너 레지스트리 자격 증명 가져오기

디바이스에 IoT Edge 모듈을 배포하려면 Azure IoT에 Azure Machine Learning 서비스가 Docker 이미지를 저장하는 컨테이너 레지스트리에 대한 자격 증명이 필요합니다.

두 가지 방법으로 자격 증명을 가져올 수 있습니다.

+ **Azure Portal에서**:

  1. [Azure Portal](https://portal.azure.com/signin/index)에 로그인합니다.

  1. Azure Machine Learning 서비스 작업 영역으로 이동하고 __개요__를 선택합니다. 컨테이너 레지스트리 설정으로 이동하려면 __레지스트리__ 링크를 선택합니다.

     ![컨테이너 레지스트리 항목의 이미지](./media/how-to-deploy-and-where/findregisteredcontainer.png)

  1. 컨테이너 레지스트리에서 **액세스 키**를 선택한 다음, 관리 사용자를 활성화합니다.

     ![액세스 키 화면의 이미지](./media/how-to-deploy-and-where/findaccesskey.png)

  1. **로그인 서버**, **사용자 이름** 및 **암호**의 값을 저장합니다.

+ **Python 스크립트 사용**:

  1. 위에서 컨테이너를 만들기 위해 실행한 코드 이후에 다음 Python 스크립트를 사용합니다.

     ```python
     # Getting your container details
     container_reg = ws.get_details()["containerRegistry"]
     reg_name=container_reg.split("/")[-1]
     container_url = "\"" + image.image_location + "\","
     subscription_id = ws.subscription_id
     from azure.mgmt.containerregistry import ContainerRegistryManagementClient
     from azure.mgmt import containerregistry
     client = ContainerRegistryManagementClient(ws._auth,subscription_id)
     result= client.registries.list_credentials(resource_group_name, reg_name, custom_headers=None, raw=False)
     username = result.username
     password = result.passwords[0].value
     print('ContainerURL{}'.format(image.image_location))
     print('Servername: {}'.format(reg_name))
     print('Username: {}'.format(username))
     print('Password: {}'.format(password))
     ```
  1. ContainerURL, servername, username 및 password 값을 저장합니다.

     이러한 자격 증명은 개인 컨테이너 레지스트리에 있는 이미지에 대한 IoT Edge 디바이스 액세스를 제공하는 데 필요합니다.

### <a name="prepare-the-iot-device"></a>IoT 디바이스 준비

Azure IoT Hub를 사용 하 여 장치를 등록 하 고 장치에서 IoT Edge 런타임을 설치 합니다. 이 프로세스를 사용 하 여 잘 모르는 경우 [빠른 시작: Linux x64 장치에 첫 번째 IoT Edge 모듈 배포](../../iot-edge/quickstart-linux.md)합니다.

장치를 등록 하는 다른 방법은 다음과 같습니다.

* [Azure Portal](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-portal)
* [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-cli)
* [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-register-device-vscode)

### <a name="deploy-the-model-to-the-device"></a>디바이스에 모델 배포

장치로 모델을 배포 하려면에서 수집한 레지스트리 정보를 사용 합니다 [컨테이너 레지스트리 자격 증명을 가져올](#getcontainer) 섹션 모듈 배포를 사용 하 여 IoT Edge 모듈에 대 한 단계입니다. 예를 들어, [Azure portal에서 Azure IoT Edge 배포 모듈](../../iot-edge/how-to-deploy-modules-portal.md)를 구성 해야 합니다는 __레지스트리 설정을__ 장치에 대 한 합니다. 사용 합니다 __로그인 서버__, __username__, 및 __암호__ 작업 영역 컨테이너 레지스트리에 대 한 합니다.

사용 하 여 배포할 수도 있습니다 [Azure CLI](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-cli) 하 고 [Visual Studio Code](https://docs.microsoft.com/azure/iot-edge/how-to-deploy-modules-vscode)합니다.

## <a name="clean-up"></a>정리

배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.

이미지를 삭제하려면 `image.delete()`를 사용합니다.

등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--), [Image.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image(class)?view=azure-ml-py#delete--) 및 [Model.delete()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)의 참조 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [SSL을 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [일괄 처리 예측 실행 방법](how-to-run-batch-predictions.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [Azure Machine Learning Service SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Azure Virtual Networks에서 Azure Machine Learning Service 사용](how-to-enable-virtual-network.md)
* [추천 시스템 빌드 모범 사례](https://github.com/Microsoft/Recommenders)
* [Azure에서 실시간 추천 API 빌드](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
