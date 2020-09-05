---
title: 모델을 배포 하는 방법 및 위치
titleSuffix: Azure Machine Learning
description: Azure Container Instances, Azure Kubernetes Service, Azure IoT Edge 및 필드 프로그래밍 가능 게이트 배열을 포함 하 여 Azure Machine Learning 모델을 배포 하는 방법 및 위치를 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.reviewer: larryfr
ms.date: 07/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d7502414f6476cafcc85bbefd28a4ec463f62099
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751690"
---
# <a name="deploy-models-with-azure-machine-learning"></a>Azure Machine Learning을 사용하여 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 클라우드의 웹 서비스로 machine learning 모델을 배포 하거나 장치를 Azure IoT Edge 하는 방법에 대해 알아봅니다.

워크플로는 모델을 배포하는 위치와 관계없이 유사합니다.

1. 모델을 등록합니다.
1. 유추 구성 준비
1. [코드 배포](how-to-deploy-no-code-deployment.md)를 사용 하지 않는 경우 항목 스크립트 준비
1. 컴퓨팅 대상에 모델을 배포합니다.
1. 웹 서비스 라고도 하는 배포 된 모델을 테스트 합니다.

배포 워크플로와 관련 된 개념에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 관리, 배포 및 모니터링](concept-model-management-and-deployment.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
- 모델. 학습 된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에 제공 된 모델 및 종속성 파일을 사용할 수 있습니다.
- [Machine Learning 서비스에 대 한 AZURE CLI (명령줄 인터페이스) 확장](reference-azure-machine-learning-cli.md)입니다.

# <a name="python"></a>[Python](#tab/python)

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.
- 모델. 학습 된 모델이 없는 경우 [이 자습서](https://aka.ms/azml-deploy-cloud)에 제공 된 모델 및 종속성 파일을 사용할 수 있습니다.
- [Python 용 AZURE MACHINE LEARNING SDK (소프트웨어 개발 키트)](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

---

## <a name="connect-to-your-workspace"></a>작업 영역에 연결

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

Azure CLI 설명서의 지침에 따라 [구독 컨텍스트를 설정](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)합니다.

그런 다음 다음을 수행 합니다.

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

액세스 권한이 있는 작업 영역을 볼 수 있습니다.

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core import Workspace
ws = Workspace.from_config(path=".file-path/ws_config.json")
```

SDK를 사용 하 여 작업 영역에 연결 하는 방법에 대 한 자세한 내용은 [Python 용 AZURE MACHINE LEARNING sdk](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) 설명서를 참조 하세요.


---


## <a name="register-your-model"></a><a id="registermodel"></a> 모델 등록

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 작업 영역에서 단일 모델로 등록할 수 있습니다. 파일을 등록 한 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 한 모든 파일을 받을 수 있습니다.

> [!TIP]
> 모델을 등록할 때 학습 실행의 클라우드 위치 또는 로컬 디렉터리의 경로를 제공 합니다. 이 경로는 등록 프로세스의 일부로 업로드할 파일을 찾는 것입니다. 항목 스크립트에 사용 된 경로와 일치 하지 않아도 됩니다. 자세한 내용은 [항목 스크립트에서 모델 파일 찾기](how-to-deploy-advanced-entry-script.md#load-registered-models)를 참조 하세요.

기계 학습 모델은 Azure Machine Learning 작업 영역에 등록 됩니다. 모델은 Azure Machine Learning 또는 다른 위치에서 가져올 수 있습니다. 모델을 등록할 때 모델에 대 한 메타 데이터를 선택적으로 제공할 수 있습니다. `tags` `properties` 모델 등록에 적용 하는 및 사전을 사용 하 여 모델을 필터링 할 수 있습니다.

다음 예에서는 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML 학습 실행에서 모델 등록

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

`--asset-path`매개 변수는 모델의 클라우드 위치를 참조 합니다. 이 예제에서는 단일 파일의 경로를 사용 합니다. 모델 등록에 여러 파일을 포함 하려면를 `--asset-path` 파일을 포함 하는 폴더의 경로로 설정 합니다.

### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

모델 등록에 여러 파일을 포함 하려면를 `-p` 파일을 포함 하는 폴더의 경로로 설정 합니다.

에 대 한 자세한 내용은 `az ml model register` [참조 설명서](/cli/azure/ext/azure-cli-ml/ml/model)를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)


등록된 모델은 모델을 구성하는 하나 이상의 파일에 대한 논리적 컨테이너입니다. 예를 들어 여러 파일에 저장 된 모델의 경우 작업 영역에서 단일 모델로 등록할 수 있습니다. 파일을 등록 한 후 등록 된 모델을 다운로드 하거나 배포 하 고 등록 한 모든 파일을 받을 수 있습니다.

> [!TIP]
> 모델을 등록할 때 학습 실행의 클라우드 위치 또는 로컬 디렉터리의 경로를 제공 합니다. 이 경로는 등록 프로세스의 일부로 업로드할 파일을 찾는 것입니다. 항목 스크립트에 사용 된 경로와 일치 하지 않아도 됩니다. 자세한 내용은 [항목 스크립트에서 모델 파일 찾기](how-to-deploy-advanced-entry-script.md#load-registered-models)를 참조 하세요.

기계 학습 모델은 Azure Machine Learning 작업 영역에 등록 됩니다. 모델은 Azure Machine Learning 또는 다른 위치에서 가져올 수 있습니다. 모델을 등록할 때 모델에 대 한 메타 데이터를 선택적으로 제공할 수 있습니다. `tags` `properties` 모델 등록에 적용 하는 및 사전을 사용 하 여 모델을 필터링 할 수 있습니다.

다음 예에서는 모델을 등록 하는 방법을 보여 줍니다.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Azure ML 학습 실행에서 모델 등록

  SDK를 사용 하 여 모델을 학습 하는 경우 모델 학습 방법에 따라 [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) 개체나 [AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun) 개체를 받을 수 있습니다. 각 개체를 사용 하 여 실험 실행에서 만든 모델을 등록할 수 있습니다.

  + 개체에서 모델을 등록 합니다 `azureml.core.Run` .
 
    ```python
    model = run.register_model(model_name='sklearn_mnist',
                               tags={'area': 'mnist'},
                               model_path='outputs/sklearn_mnist_model.pkl')
    print(model.name, model.id, model.version, sep='\t')
    ```

    `model_path`매개 변수는 모델의 클라우드 위치를 참조 합니다. 이 예제에서는 단일 파일의 경로를 사용 합니다. 모델 등록에 여러 파일을 포함 하려면를 `model_path` 파일을 포함 하는 폴더의 경로로 설정 합니다. 자세한 내용은 [register_model](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) 설명서를 참조 하세요.

  + 개체에서 모델을 등록 합니다 `azureml.train.automl.run.AutoMLRun` .

    ```python
        description = 'My AutoML Model'
        model = run.register_model(description = description,
                                   tags={'area': 'mnist'})

        print(run.model_id)
    ```

    이 예제에서는 `metric` 및 `iteration` 매개 변수를 지정 하지 않으므로 가장 적합 한 기본 메트릭이 포함 된 반복이 등록 됩니다. `model_id`Run에서 반환 된 값은 모델 이름 대신 사용 됩니다.

    자세한 내용은 [Register_model AutoMLRun](/python/api/azureml-train-automl-client/azureml.train.automl.run.automlrun#register-model-model-name-none--description-none--tags-none--iteration-none--metric-none-) 설명서를 참조 하세요.


### <a name="register-a-model-from-a-local-file"></a>로컬 파일에서 모델 등록

모델의 로컬 경로를 제공 하 여 모델을 등록할 수 있습니다. 폴더 또는 단일 파일의 경로를 제공할 수 있습니다. 이 메서드를 사용 하 여 Azure Machine Learning으로 학습 된 모델을 등록 한 다음 다운로드할 수 있습니다. 이 메서드를 사용 하 여 Azure Machine Learning 외부에서 학습 된 모델을 등록할 수도 있습니다.

[!INCLUDE [trusted models](../../includes/machine-learning-service-trusted-model.md)]

+ **SDK 및 ONNX 사용**

    ```python
    import os
    import urllib.request
    from azureml.core.model import Model
    # Download model
    onnx_model_url = "https://www.cntk.ai/OnnxModels/mnist/opset_7/mnist.tar.gz"
    urllib.request.urlretrieve(onnx_model_url, filename="mnist.tar.gz")
    os.system('tar xvzf mnist.tar.gz')
    # Register model
    model = Model.register(workspace = ws,
                            model_path ="mnist/model.onnx",
                            model_name = "onnx_mnist",
                            tags = {"onnx": "demo"},
                            description = "MNIST image classification CNN from ONNX Model Zoo",)
    ```

  모델 등록에 여러 파일을 포함 하려면를 `model_path` 파일을 포함 하는 폴더의 경로로 설정 합니다.

자세한 내용은 [모델 클래스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py)에 대 한 설명서를 참조 하세요.

Azure Machine Learning 외부에서 학습 한 모델을 사용 하는 방법에 대 한 자세한 내용은 [기존 모델을 배포 하는 방법](how-to-deploy-existing-model.md)을 참조 하세요.

---

## <a name="define-an-entry-script"></a>항목 스크립트 정의

[!INCLUDE [write entry script](../../includes/machine-learning-entry-script.md)]


## <a name="define-an-inference-configuration"></a>유추 구성 정의

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

[!INCLUDE [inference config](../../includes/machine-learning-service-inference-config.md)]

다음 명령은 CLI를 사용 하 여 모델을 배포 하는 방법을 보여 줍니다.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

이 예에서는 구성에서 다음 설정을 지정 합니다.

* 모델에 Python이 필요 합니다.
* 배포 된 서비스로 전송 되는 웹 요청을 처리 하는 데 사용 되는 [항목 스크립트](#define-an-entry-script)
* 유추에 필요한 Python 패키지를 설명 하는 Conda 파일입니다.

유추 구성과 함께 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

유추 구성은 모델을 포함 하는 웹 서비스를 설정 하는 방법을 설명 합니다. 나중에 모델을 배포할 때 사용 됩니다.

유추 구성은 Azure Machine Learning 환경을 사용 하 여 배포에 필요한 소프트웨어 종속성을 정의 합니다. 환경을 사용 하 여 교육 및 배포에 필요한 소프트웨어 종속성을 만들고, 관리 하 고, 재사용할 수 있습니다. 사용자 지정 종속성 파일에서 환경을 만들거나 큐 레이트 Azure Machine Learning 환경 중 하나를 사용할 수 있습니다. 다음 YAML은 유추를 위한 Conda 종속성 파일의 예입니다. 모델을 웹 서비스로 호스트 하는 데 필요한 기능이 포함 되어 있으므로 버전 >= 1.0.45를 pip 종속성으로 지정 해야 합니다. 자동 스키마 생성을 사용 하려면 항목 스크립트도 패키지를 가져와야 합니다 `inference-schema` .

```YAML

name: project_environment
dependencies:
- python=3.6.2
- scikit-learn=0.22.1
- pip:
 # You must list azureml-defaults as a pip dependency
 - azureml-defaults>=1.0.45
 - inference-schema[numpy-support]
```

> [!IMPORTANT]
> Conda와 pip (PyPi)를 통해 종속성을 사용할 수 있는 경우 일반적으로 Conda 패키지는 더 안정적으로 설치 하는 미리 빌드된 이진 파일과 함께 제공 되므로 Conda 버전을 사용 하는 것이 좋습니다.
>
> 자세한 내용은 [Conda 및 Pip 이해](https://www.anaconda.com/understanding-conda-and-pip/)를 참조 하세요.
>
> Conda를 통해 종속성을 사용할 수 있는지 확인 하려면 명령을 사용 `conda search <package-name>` 하거나 및의 패키지 인덱스를 사용 [https://anaconda.org/anaconda/repo](https://anaconda.org/anaconda/repo) [https://anaconda.org/conda-forge/repo](https://anaconda.org/conda-forge/repo) 합니다.

종속성 파일을 사용 하 여 환경 개체를 만들고 나중에 사용할 수 있도록 작업 영역에 저장할 수 있습니다.

```python
from azureml.core.environment import Environment
myenv = Environment.from_conda_specification(name = 'myenv',
                                                file_path = 'path-to-conda-specification-file'
myenv.register(workspace=ws)
```

Azure Machine Learning를 사용 하 여 Python 환경을 사용자 지정 하 고 사용자 지정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning에서 소프트웨어 환경 만들기 & 사용](how-to-use-environments.md)을 참조 하세요.

유추 구성과 함께 사용자 지정 Docker 이미지를 사용 하는 방법에 대 한 자세한 내용은 [사용자 지정 docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)을 참조 하세요.


다음 예제에서는 작업 영역에서 환경을 로드 한 다음이를 유추 구성과 함께 사용 하는 방법을 보여 줍니다.

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig


myenv = Environment.get(workspace=ws, name='myenv', version='1')
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                    environment=myenv)
```

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.

유추 구성에 대 한 자세한 내용은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스 설명서를 참조 하세요.

---

## <a name="choose-a-compute-target"></a>계산 대상 선택

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>배포 구성 정의

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

배포 구성에 사용할 수 있는 옵션은 선택한 계산 대상에 따라 다릅니다.

[!INCLUDE [aml-local-deploy-config](../../includes/machine-learning-service-local-deploy-config.md)]

자세한 내용은 [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) 설명서를 참조 하세요.

# <a name="python"></a>[Python](#tab/python)

모델을 배포 하기 전에 배포 구성을 정의 해야 합니다. *배포 구성은 웹 서비스를 호스팅하는 계산 대상에만 적용 됩니다.* 예를 들어 모델을 로컬로 배포 하는 경우 서비스에서 요청을 수락 하는 포트를 지정 해야 합니다. 배포 구성은 입력 스크립트의 일부가 아닙니다. 모델 및 항목 스크립트를 호스팅할 계산 대상의 특성을 정의 하는 데 사용 됩니다.

예를 들어, 작업 영역과 연결 된 AKS (Azure Kubernetes Service) 인스턴스가 아직 없는 경우 계산 리소스를 만들어야 할 수도 있습니다.

다음 표에서는 각 계산 대상에 대 한 배포 구성을 만드는 예를 제공 합니다.

| 컴퓨팅 대상 | 배포 구성 예 |
| ----- | ----- |
| 로컬 | `deployment_config = LocalWebservice.deploy_configuration(port=8890)` |
| Azure Container Instances | `deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |
| Azure Kubernetes Service | `deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)` |

로컬, Azure Container Instances 및 AKS 웹 서비스에 대 한 클래스는에서 가져올 수 있습니다 `azureml.core.webservice` .

```python
from azureml.core.webservice import AciWebservice, AksWebservice, LocalWebservice
```

---



## <a name="deploy-your-model"></a>모델 배포

이제 모델을 배포할 준비가 되었습니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

### <a name="using-a-registered-model"></a>등록 된 모델 사용

Azure Machine Learning 작업 영역에 모델을 등록 한 경우 "mymodel: 1"을 모델 이름과 해당 버전 번호로 바꿉니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>로컬 모델 사용

모델을 등록 하지 않으려는 경우의 inferenceconfig.js에 "sourceDirectory" 매개 변수를 전달 하 여 모델을 제공할 로컬 디렉터리를 지정할 수 있습니다.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```

# <a name="python"></a>[Python](#tab/python)

아래 예제에서는 로컬 배포를 보여 줍니다. 구문은 이전 단계에서 선택한 계산 대상에 따라 달라 집니다.

```python
from azureml.core.webservice import LocalWebservice, Webservice

deployment_config = LocalWebservice.deploy_configuration(port=8890)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

자세한 내용은 [Localwebservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservice?view=azure-ml-py), [Model. deploy ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)및 [Webservice](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice?view=azure-ml-py)에 대 한 설명서를 참조 하세요.

---

### <a name="understanding-service-state"></a>서비스 상태 이해

모델을 배포 하는 동안 완전히 배포 되는 동안 서비스 상태 변경이 표시 될 수 있습니다.

다음 표에서는 다양 한 서비스 상태에 대해 설명 합니다.

| 웹 서비스 상태 | Description | 최종 상태?
| ----- | ----- | ----- |
| 변환은 | 서비스의 배포를 진행 중입니다. | 아니요 |
| Unhealthy | 서비스가 배포 되었지만 현재 연결할 수 없습니다.  | 아니요 |
| 예약 불가능 | 리소스가 부족 하 여 지금은 서비스를 배포할 수 없습니다. | 아니요 |
| 실패 | 오류 또는 충돌 때문에 서비스를 배포 하지 못했습니다. | 예 |
| 정상 | 서비스가 정상 상태 이며 끝점을 사용할 수 있습니다. | 예 |


### <a name="batch-inference"></a><a id="azuremlcompute"></a> 일괄 처리 유추
Azure Machine Learning Azure Machine Learning 계산 대상이 만들어지고 관리 됩니다. Azure Machine Learning 파이프라인에서 일괄 처리 예측에 사용할 수 있습니다.

Azure Machine Learning 계산을 사용한 일괄 처리 유추 연습은 [일괄 처리를 실행 하는 방법](tutorial-pipeline-batch-scoring-classification.md)을 참조 하세요.

### <a name="iot-edge-inference"></a><a id="iotedge"></a> IoT Edge 유추
에 지에 배포 하는 기능은 미리 보기 상태입니다. 자세한 내용은 [Azure Machine Learning를 IoT Edge 모듈로 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)를 참조 하세요.

## <a name="delete-resources"></a>리소스 삭제

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

배포 된 웹 서비스를 삭제 하려면를 사용 `az ml service <name of webservice>` 합니다.

작업 영역에서 등록 된 모델을 삭제 하려면 다음을 사용 합니다. `az ml model delete <model id>`

[웹 서비스를 삭제](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) 하 고 [모델을 삭제](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete)하는 방법에 대해 자세히 알아보세요.

# <a name="python"></a>[Python](#tab/python)

배포된 웹 서비스를 삭제하려면 `service.delete()`를 사용합니다.
등록된 모델을 삭제하려면 `model.delete()`를 사용합니다.

자세한 내용은 [WebService. delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py#delete--) 및 [Model. delete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#delete--)설명서를 참조 하십시오.

---


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

