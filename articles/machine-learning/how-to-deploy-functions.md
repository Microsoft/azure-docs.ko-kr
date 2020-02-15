---
title: Azure Functions 앱에 ml 모델 배포 (미리 보기)
titleSuffix: Azure Machine Learning
description: Azure Machine Learning를 사용 하 여 Azure Functions 앱에 모델을 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: vaidyas
author: vaidyas
ms.reviewer: larryfr
ms.date: 11/22/2019
ms.openlocfilehash: 321f985bd375e6fa4337e060bb15d318ea306ab4
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116741"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Azure Functions에 machine learning 모델 배포 (미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Functions에서 함수 앱으로 Azure Machine Learning에서 모델을 배포 하는 방법에 대해 알아봅니다.

> [!IMPORTANT]
> Azure Machine Learning와 Azure Functions를 모두 사용할 수 있지만 함수에 대 한 Machine Learning 서비스에서 모델을 패키징하는 기능은 미리 보기 상태입니다.

Azure Machine Learning를 통해 학습 된 기계 학습 모델에서 Docker 이미지를 만들 수 있습니다. 이제 Azure Machine Learning에는 이러한 기계 학습 모델을 [Azure Functions에 배포할](https://docs.microsoft.com/azure/azure-functions/functions-deployment-technologies#docker-container)수 있는 함수 앱으로 빌드하는 미리 보기 기능이 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure Machine Learning 작업 영역 자세한 내용은 [작업 영역 만들기](how-to-manage-workspace.md) 문서를 참조 하세요.
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* 작업 영역에 등록 된 학습 된 기계 학습 모델입니다. 모델이 없는 경우 [이미지 분류 자습서: 학습 모델 학습](tutorial-train-models-with-aml.md) 및 등록을 사용 합니다.

    > [!IMPORTANT]
    > 이 문서의 코드 조각은 다음 변수를 설정 했다고 가정 합니다.
    >
    > * `ws`-Azure Machine Learning 작업 영역입니다.
    > * `model`-배포 될 등록 된 모델입니다.
    > * `inference_config`-모델에 대 한 유추 구성입니다.
    >
    > 이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="prepare-for-deployment"></a>배포 준비

을 배포 하기 전에 모델을 웹 서비스로 실행 하는 데 필요한 작업을 정의 해야 합니다. 다음 목록에서는 배포에 필요한 기본 항목에 대해 설명 합니다.

* __항목 스크립트__입니다. 이 스크립트는 요청을 수락 하 고, 모델을 사용 하 여 요청 점수를 생성 하 고, 결과를 반환 합니다.

    > [!IMPORTANT]
    > 항목 스크립트는 모델에 따라 다릅니다. 들어오는 요청 데이터의 형식, 모델에 필요한 데이터의 형식 및 클라이언트에 반환 되는 데이터 형식을 이해 해야 합니다.
    >
    > 요청 데이터가 모델에서 사용할 수 없는 형식인 경우 스크립트는이를 허용 되는 형식으로 변환할 수 있습니다. 클라이언트에 반환 하기 전에 응답을 변환할 수도 있습니다.
    >
    > 기본적으로 함수를 패키징하는 경우 입력은 텍스트로 처리 됩니다. Blob 트리거의 경우와 같이 입력의 원시 바이트를 사용 하는 데 관심이 있는 경우에는 [Amlrequest를 사용 하 여 원시 데이터를 수락](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where#binary-data)해야 합니다.


* 항목 스크립트나 모델을 실행 하는 데 필요한 도우미 스크립트 또는 Python/Conda 패키지와 같은 **종속성**

이러한 엔터티는 __유추 구성__에 캡슐화 됩니다. 유추 구성은 입력 스크립트 및 기타 종속성을 참조 합니다.

> [!IMPORTANT]
> Azure Functions에 사용할 유추 구성을 만드는 경우 [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment%28class%29?view=azure-ml-py) 개체를 사용 해야 합니다. 사용자 지정 환경을 정의 하는 경우 pip 종속성으로 version > = 1.0.45를 사용 하 여 azureml 기본값을 추가 해야 합니다. 이 패키지에는 모델을 웹 서비스로 호스팅하는 데 필요한 기능이 포함되어 있습니다. 다음 예제에서는 환경 개체를 만들고 유추 구성에서 사용 하는 방법을 보여 줍니다.
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

환경에 대 한 자세한 내용은 [교육 및 배포를 위한 환경 만들기 및 관리](how-to-use-environments.md)를 참조 하세요.

유추 구성에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

> [!IMPORTANT]
> 함수에 배포 하는 경우 __배포 구성을__만들 필요가 없습니다.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>함수 지원을 위한 SDK 미리 보기 패키지 설치

Azure Functions에 대 한 패키지를 빌드하려면 SDK 미리 보기 패키지를 설치 해야 합니다.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>이미지 만들기

Azure Functions에 배포 되는 Docker 이미지를 만들려면 사용 하려는 트리거에 대해 [azureml](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py) 또는 특정 패키지 함수를 사용 합니다. 다음 코드 조각에서는 모델 및 유추 구성에서 blob 트리거를 사용 하 여 새 패키지를 만드는 방법을 보여 줍니다.

> [!NOTE]
> 이 코드 조각은 `model`에 등록 된 모델이 포함 되어 있고 `inference_config` 유추 환경에 대 한 구성을 포함 하 고 있다고 가정 합니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md)를 참조 하세요.

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

`show_output=True`하면 Docker 빌드 프로세스의 출력이 표시 됩니다. 프로세스가 완료 되 면 작업 영역에 대 한 Azure Container Registry에 이미지가 생성 됩니다. 이미지가 빌드되면 Azure Container Registry의 위치가 표시 됩니다. 반환 되는 위치는 `<acrinstance>.azurecr.io/package@sha256:<hash>`형식입니다.

> [!NOTE]
> 함수에 대 한 패키징은 현재 HTTP 트리거, Blob 트리거 및 Service bus 트리거를 지원 합니다. 트리거에 대 한 자세한 내용은 [Azure Functions 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob?tabs=csharp#trigger---blob-name-patterns)을 참조 하세요.

> [!IMPORTANT]
> 이미지를 배포할 때 사용 되는 위치 정보를 저장 합니다.

## <a name="deploy-image-as-a-web-app"></a>이미지를 웹 앱으로 배포

1. 다음 명령을 사용 하 여 이미지를 포함 하는 Azure Container Registry에 대 한 로그인 자격 증명을 가져옵니다. `<myacr>`를 `package.location`에서 이전에 반환 된 값으로 바꿉니다. 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    이 명령의 출력은 다음 JSON 문서와 유사 합니다.

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    __사용자 이름__ 및 __암호__중 하나에 대 한 값을 저장 합니다.

1. 서비스를 배포 하기 위한 리소스 그룹 또는 app service 계획이 아직 없는 경우 다음 명령에서 두 가지를 모두 만드는 방법을 보여 줍니다.

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku EP1 --is-linux
    ```

    이 예제에서는 _Linux 프리미엄_ 가격 책정 계층 (`--sku EP1`)이 사용 됩니다.

    > [!IMPORTANT]
    > Azure Machine Learning에서 만든 이미지는 Linux를 사용 하므로 `--is-linux` 매개 변수를 사용 해야 합니다.

1. 웹 작업 저장소에 사용할 저장소 계정을 만들고 연결 문자열을 가져옵니다. `<webjobStorage>`를 사용 하려는 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create --name triggerStorage --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. 함수 앱을 만들려면 다음 명령을 사용 합니다. `<app-name>`를 사용 하려는 이름으로 바꿉니다. `<acrinstance>` 및 `<imagename>`를 앞에서 반환 된 `package.location` 값으로 바꿉니다. Replace `<webjobStorage>`을 이전 단계의 저장소 계정 이름으로 바꿉니다.

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > 이제 함수 앱이 생성 되었습니다. 그러나 이미지를 포함 하는 Azure Container Registry에 대 한 blob 트리거 또는 자격 증명에 대 한 연결 문자열을 제공 하지 않았으므로 함수 앱은 활성화 되지 않습니다. 다음 단계에서는 컨테이너 레지스트리에 대 한 연결 문자열과 인증 정보를 제공 합니다. 

1. Blob 트리거 저장소에 사용할 저장소 계정을 만들고 연결 문자열을 가져옵니다. `<triggerStorage>`를 사용 하려는 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    이 연결 문자열을 기록 하 여 함수 앱에 제공 합니다. 나중에 `<triggerConnectionString>`을 요청할 때이를 사용 합니다.

1. 저장소 계정에 입력 및 출력에 대 한 컨테이너를 만듭니다. `<triggerConnectionString>`를 앞에서 반환 된 연결 문자열로 바꿉니다.

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. 트리거 연결 문자열과 함수 앱을 연결 하려면 다음 명령을 사용 합니다. `<app-name>`을 함수 앱의 이름으로 바꿉니다. `<triggerConnectionString>`를 앞에서 반환 된 연결 문자열로 바꿉니다.

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. 다음 명령을 사용 하 여 만들어진 컨테이너와 연결 된 태그를 검색 해야 합니다. `<username>`를 컨테이너 레지스트리에서 앞에서 반환 된 사용자 이름으로 바꿉니다.

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    반환 된 값을 저장 합니다 .이 값은 다음 단계에서 `imagetag`로 사용 됩니다.

1. 컨테이너 레지스트리에 액세스 하는 데 필요한 자격 증명을 함수 앱에 제공 하려면 다음 명령을 사용 합니다. `<app-name>`를 사용 하려는 이름으로 바꿉니다. `<acrinstance>` 및 `<imagetag>`을 이전 단계에서 AZ CLI 호출의 값으로 바꿉니다. `<username>` 및 `<password>`를 앞에서 검색 한 ACR 로그인 정보로 바꿉니다.

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    이 명령은 다음 JSON 문서와 유사한 정보를 반환 합니다.

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

이 시점에서 함수 앱은 이미지를 로드 하기 시작 합니다.

> [!IMPORTANT]
> 이미지가 로드 되기까지 몇 분 정도 걸릴 수 있습니다. Azure Portal을 사용 하 여 진행률을 모니터링할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [함수](/azure/azure-functions/functions-create-function-linux-custom-image) 설명서에서 함수 앱을 구성 하는 방법에 대해 알아봅니다.
* Blob 저장소에 대 한 자세한 내용은 [Azure blob storage 바인딩](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-blob)을 트리거합니다.
* [Azure App Service에 모델을 배포](how-to-deploy-app-service.md)합니다.
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [API 참조](https://docs.microsoft.com/python/api/azureml-contrib-functions/azureml.contrib.functions?view=azure-ml-py)
