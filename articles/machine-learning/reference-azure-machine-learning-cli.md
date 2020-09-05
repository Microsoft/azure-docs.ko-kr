---
title: Azure Machine Learning CLI 설치 및 사용
description: Azure Machine Learning CLI 확장을 설치하고 사용하여 작업 영역, 데이터 저장소, 데이터 세트, 파이프라인, 모델 및 배포와 같은 리소스를 만들고 관리하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 06/22/2020
ms.custom: seodec18
ms.openlocfilehash: f037ea30a1507d4736db7f837e5286701db030e0
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146704"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Azure Machine Learning용 CLI 확장 설치 및 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장은 Azure Machine Learning을 사용하기 위한 명령을 제공합니다. 이 확장을 사용하여 기계 학습 활동을 자동화할 수 있습니다. 다음 목록에서는 CLI 확장을 통해 수행할 수 있는 몇 가지 예제 작업을 보여 줍니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 자동화에 적합한 매개 변수가 많은 작업을 처리하는 데 최적화된 보완 도구입니다.

## <a name="prerequisites"></a>사전 요구 사항

* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경**에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)을 사용하는 경우 CLI는 브라우저를 통해 액세스하고 클라우드에 있습니다.

## <a name="full-reference-docs"></a>전체 참조 문서

[Azure CLI의 azure-cli-ml 확장에 대한 전체 참조 문서](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)를 찾습니다.

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

> [!IMPORTANT]
> Azure Cloud Shell을 사용하는 경우 이 섹션을 건너뛸 수 있습니다. Cloud Shell은 Azure 구독에 로그인하는 계정을 사용하여 사용자를 자동으로 인증합니다.

CLI에서 Azure 구독에 인증하는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용하여 대화형으로 인증하는 것입니다. 대화형으로 인증하려면 명령줄 또는 터미널을 열고 다음 명령을 사용합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. CLI가 브라우저를 열 수 없는 경우에는 사용자가 브라우저를 열고 명령줄의 지침을 따릅니다. 지침에 따라 [https://aka.ms/devicelogin](https://aka.ms/devicelogin)으로 이동하고 인증 코드를 입력합니다.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

다른 인증 방법은 [Azure CLI로 로그인](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)을 참조하세요.

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 아래 명령과 함께 사용할 수 있는 예제 파일은 [여기](https://aka.ms/azml-deploy-cloud)에서 찾을 수 있습니다.

메시지가 표시되면 `y`를 선택하여 확장을 설치합니다.

확장이 설치되었는지 확인하려면 다음 명령을 사용하여 ML 관련 하위 명령 목록을 표시합니다.

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>확장 업데이트

Machine Learning CLI 확장을 업데이트하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>확장 제거

CLI 확장을 제거하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>리소스 관리

다음 명령은 CLI를 사용하여 Azure Machine Learning에서 사용하는 리소스를 관리하는 방법을 보여 줍니다.

+ 아직 없는 경우 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning 작업 영역을 만듭니다.

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > 이 명령은 기본 버전 작업 영역을 만듭니다. 엔터프라이즈 작업 영역을 만들려면 `az ml workspace create` 명령과 함께 `--sku enterprise` 스위치를 사용합니다. Azure Machine Learning 버전에 관한 자세한 내용은 [Azure Machine Learning이란?](overview-what-is-azure-ml.md#sku)을 참조하세요.

    자세한 내용은 [az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)를 참조하세요.

+ 작업 영역 구성을 폴더에 연결하여 CLI 상황별 인식을 사용하도록 설정합니다.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    이 명령은 예제 runconfig 및 conda 환경 파일을 포함하는 `.azureml` 하위 디렉터리를 만듭니다. 또한 Azure Machine Learning 작업 영역과 통신하는 데 사용되는 `config.json` 파일을 포함합니다.

    자세한 내용은 [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)를 참조하세요.

+ Azure Blob 컨테이너를 데이터 저장소로 연결합니다.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    자세한 내용은 [az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)을 참조하세요.

+ 데이터 저장소에 파일을 업로드합니다.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    자세한 내용은 [az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)를 참조하세요.

+ AKS 클러스터를 컴퓨팅 대상으로 연결합니다.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    자세한 내용은 [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)를 참조하세요.

### <a name="compute-clusters"></a>컴퓨팅 클러스터

+ 새 관리 되는 계산 클러스터를 만듭니다.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ 관리 id를 사용 하 여 관리 되는 계산 클러스터 새로 만들기

  + 사용자 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + 시스템 할당 관리 ID

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ 기존 클러스터에 관리 되는 id를 추가 합니다.

    + 사용자 할당 관리 ID
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + 시스템 할당 관리 ID

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

자세한 내용은 [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)를 참조하세요.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>컴퓨팅 인스턴스
계산 인스턴스를 관리 합니다.  아래의 모든 예제에서 계산 인스턴스의 이름은 **cpu** 입니다.

    + 새 새 새 인스턴스를 만듭니다.

        ```azurecli-interactive
        az ml computetarget create computeinstance  -n cpu -s "STANDARD_D3_V2" -v
        ```
    
        자세한 내용은 [az ml computetarget create einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance)를 참조 하세요.

    + 응답 하는 einstance를 중지 합니다.
    
        ```azurecli-interactive
        az ml computetarget stop computeinstance -n cpu -v
        ```
    
        자세한 내용은 [az ml computetarget stop](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)를 참조 하세요.
    
    + 를 시작 합니다.
    
        ```azurecli-interactive
        az ml computetarget start computeinstance -n cpu -v
       ```
    
        자세한 내용은 [az ml computetarget start einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)를 참조 하세요.
    
    + 다시 시작 하는 einstance.
    
        ```azurecli-interactive
        az ml computetarget restart computeinstance -n cpu -v
       ```
    
        자세한 내용은 [az ml computetarget restart 확인 einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart)를 참조 하세요.
    
    + 인 트 einstance를 삭제 합니다.
    
        ```azurecli-interactive
        az ml computetarget delete -n cpu -v
       ```
    
        자세한 내용은 [az ml computetarget delete einstance](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-delete)를 참조 하세요.


## <a name="run-experiments"></a><a id="experiments"></a>실험 실행

* 실험 실행을 시작합니다. 이 명령을 사용하는 경우 -c 매개 변수 가까이에 runconfig 파일의 이름(파일 시스템을 보고 있는 경우 \*.runconfig 앞의 텍스트)을 지정합니다.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` 명령은 두 개의 예제 runconfig 파일을 포함하는 `.azureml` 하위 디렉터리를 만듭니다. 
    >
    > 실행 구성 개체를 프로그래밍 방식으로 만드는 Python 스크립트가 있는 경우 [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-)를 사용하여 runconfig 파일로 저장합니다.
    >
    > 전체 runconfig 스키마는 이 [JSON 파일](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)에서 찾을 수 있습니다. 스키마는 각 개체의 `description` 키를 통해 자동으로 문서화합니다. 또한 가능한 값의 열거형과 끝 부분에 템플릿 코드 조각이 있습니다.

    자세한 내용은 [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)를 참조하세요.

* 실험 목록을 확인합니다.

    ```azurecli-interactive
    az ml experiment list
    ```

    자세한 내용은 [az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)를 참조하세요.

### <a name="hyperdrive-run"></a>HyperDrive 실행

Azure CLI에서 HyperDrive를 사용하여 매개 변수 튜닝 실행을 수행할 수 있습니다. 먼저 다음과 같은 형식으로 HyperDrive 구성 파일을 만듭니다. 하이퍼 매개 변수 튜닝 매개 변수에 대한 자세한 내용은 [모델의 하이퍼 매개 변수 튜닝](how-to-tune-hyperparameters.md) 문서를 참조하세요.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

실행 구성 파일과 함께 이 파일을 추가합니다. 그리고 다음 명령을 사용하여 HyperDrive 실행을 제출합니다.
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

runconfig의 *인수* 섹션과 HyperDrive 구성의 *매개 변수 공간*을 잘 보세요. 학습 스크립트에 전달할 명령줄 인수가 포함되어 있습니다. runconfig의 값은 각 반복에서 동일하게 유지되는 반면, HyperDrive 구성의 범위는 반복됩니다. 두 파일에서 같은 인수를 지정하지 마세요.

## <a name="dataset-management"></a>데이터 세트 관리

다음 명령은 Azure Machine Learning에서 데이터 세트를 사용하는 방법을 보여 줍니다.

+ 데이터 세트를 등록합니다.

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    데이터 세트를 정의하는 데 사용되는 JSON 파일의 형식을 알아보려면 `az ml dataset register --show-template`을 사용합니다.

    자세한 내용은 [az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-register)를 참조하세요.

+ 작업 영역의 모든 데이터 세트를 나열합니다.

    ```azurecli-interactive
    az ml dataset list
    ```

    자세한 내용은 [az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-list)를 참조하세요.

+ 데이터 세트의 세부 정보를 가져옵니다.

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    자세한 내용은 [az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-show)를 참조하세요.

+ 데이터베이스 등록을 취소합니다.

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    자세한 내용은 [az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)를 참조하세요.

## <a name="environment-management"></a>환경 관리

다음 명령은 작업 영역의 Azure Machine Learning [환경](how-to-configure-environment.md)을 만들고 등록하고 나열하는 방법을 보여 줍니다.

+ 환경의 스캐폴딩 파일을 만듭니다.

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    자세한 내용은 [az ml environment scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)를 참조하세요.

+ 환경을 등록합니다.

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    자세한 내용은 [az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)를 참조하세요.

+ 등록된 환경을 나열합니다.

    ```azurecli-interactive
    az ml environment list
    ```

    자세한 내용은 [az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)를 참조하세요.

+ 등록된 환경을 다운로드합니다.

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    자세한 내용은 [az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)를 참조하세요.

### <a name="environment-configuration-schema"></a>환경 구성 스키마

`az ml environment scaffold` 명령을 사용한 경우 이 명령은 CLI로 사용자 지정 환경 구성을 만드는 데 사용하고 수정할 수 있는 템플릿 `azureml_environment.json` 파일을 생성합니다. 최상위 개체는 Python SDK의 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) 클래스에 느슨하게 매핑됩니다. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

다음 표에는 JSON 파일의 각 최상위 필드, 해당 형식 및 설명이 자세히 나와 있습니다. 개체 형식이 Python SDK의 클래스에 연결된 경우에는 각 JSON 필드와 Python 클래스의 퍼블릭 변수 이름 간에 느슨한 1:1 일치가 있습니다. 경우에 따라 필드는 클래스 변수가 아니라 생성자 인수에 매핑될 수 있습니다. 예를 들어 `environmentVariables` 필드는 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) 클래스의 `environment_variables` 변수에 매핑됩니다.

| JSON 필드 | Type | Description |
|---|---|---|
| `name` | `string` | 환경의 이름입니다. 이름은 **Microsoft** 또는 **AzureML**로 시작하면 안 됩니다. |
| `version` | `string` | 환경의 버전입니다. |
| `environmentVariables` | `{string: string}` | 환경 변수 이름 및 값의 해시 맵입니다. |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | 대상 컴퓨팅 리소스에서 사용할 Python 환경 및 인터프리터를 정의하는 개체입니다. |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 환경 사양에 기본 제공된 Docker 이미지를 사용자 지정하는 설정을 정의합니다. |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | 이 섹션에서는 Spark 설정을 구성합니다. 프레임워크가 PySpark로 설정된 경우에만 사용됩니다. |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Databricks 라이브러리 종속성을 구성합니다. |
| `inferencingStackVersion` | `string` | 이미지에 추가된 유추 스택 버전을 지정합니다. 유추 스택을 추가하지 않으려면 이 필드를 `null`로 유지합니다. 유효한 값: “latest”. |

## <a name="ml-pipeline-management"></a>ML 파이프라인 관리

다음 명령은 기계 학습 파이프라인을 사용하는 방법을 보여 줍니다.

+ 기계 학습 파이프라인을 만듭니다.

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    자세한 내용은 [az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)를 참조하세요.

    파이프라인 YAML 파일에 관한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의](reference-pipeline-yaml.md)를 참조하세요.

+ 파이프라인을 실행합니다.

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    자세한 내용은 [az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)을 참조하세요.

    파이프라인 YAML 파일에 관한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의](reference-pipeline-yaml.md)를 참조하세요.

+ 파이프라인을 예약합니다.

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    자세한 내용은 [az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)을 참조하세요.

    파이프라인 일정 YAML 파일에 관한 자세한 내용은 [YAML의 기계 학습 파이프라인 정의](reference-pipeline-yaml.md#schedules)를 참조하세요.

## <a name="model-registration-profiling-deployment"></a>모델 등록, 프로파일링, 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    자세한 내용은 [az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)를 참조하세요.

+ **선택 사항** 배포를 위한 최적의 CPU 및 메모리 값을 얻으려면 모델을 프로파일링합니다.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    자세한 내용은 [az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)을 참조하세요.

+ AKS에 모델 배포
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    유추 구성 파일 스키마에 관한 자세한 내용은 [유추 구성 스키마](#inferenceconfig)를 참조하세요.
    
    배포 구성 파일 스키마에 관한 자세한 내용은 [배포 구성 스키마](#deploymentconfig)를 참조하세요.

    자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)를 참조하세요.

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>유추 구성 스키마

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>배포 구성 스키마

### <a name="local-deployment-configuration-schema"></a>로컬 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure Container Instance 배포 구성 스키마 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service 배포 구성 스키마

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>다음 단계

* [Machine Learning CLI 확장의 명령 참조](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Azure Pipelines를 사용하여 기계 학습 모델 학습 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)
