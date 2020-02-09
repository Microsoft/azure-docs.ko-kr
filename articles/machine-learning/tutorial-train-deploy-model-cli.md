---
title: CLI에서 모델 학습 및 배포
titleSuffix: Azure Machine Learning
description: Azure CLI에 대해 machine learning 확장을 사용 하 여 명령줄에서 모델을 학습, 등록 및 배포 하는 방법에 대해 알아봅니다.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 1f609c33ea474508eb107c0df9993c2ba3483660
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77087035"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>자습서: CLI에서 모델 학습 및 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 자습서에서는 Azure CLI에 대해 machine learning 확장을 사용 하 여 모델을 학습, 등록 및 배포 합니다.

이 자습서의 Python 학습 스크립트에서는 [scikit](https://scikit-learn.org/) 를 사용 하 여 기본 모델을 학습 합니다. 이 자습서의 초점은 스크립트나 모델에는 적용 되지 않지만 CLI를 사용 하 여 Azure Machine Learning 사용 하는 프로세스입니다.

다음 작업을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Machine learning 확장 설치
> * Azure Machine Learning 작업 영역 만들기
> * 모델 학습에 사용 되는 계산 리소스 만들기
> * 모델 학습에 사용 되는 데이터 집합 정의 및 등록
> * 학습 실행 시작
> * 모델 등록 및 다운로드
> * 모델을 웹 서비스로 배포
> * 웹 서비스를 사용 하 여 데이터 점수 매기기

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* **로컬 환경**에서이 문서의 CLI 명령을 사용 하려면 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)필요 합니다.

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)사용 하는 경우 CLI는 브라우저를 통해 액세스할 수 있으며 클라우드에 있습니다.

## <a name="download-the-example-project"></a>예제 프로젝트 다운로드

이 자습서에서는 [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) 프로젝트를 다운로드 합니다. 이 자습서의 단계에서는 `examples/cli-train-deploy` 디렉터리의 파일을 사용 합니다.

파일의 로컬 복사본을 가져오려면 [.zip 보관](https://github.com/microsoft/MLOps/archive/master.zip)파일을 다운로드 하거나 다음 Git 명령을 사용 하 여 리포지토리를 복제 합니다.

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>학습 파일

프로젝트의 `examples/cli-train-deploy` 디렉터리에는 모델을 학습 하는 데 사용 되는 다음 파일이 포함 되어 있습니다.

* `.azureml\mnist.runconfig`: __실행 구성__ 파일입니다. 이 파일은 모델을 학습 하는 데 필요한 런타임 환경을 정의 합니다. 또한이 예제에서는 학습 환경으로 모델을 학습 하는 데 사용 되는 데이터를 탑재 합니다.
* `scripts\train.py`: 학습 스크립트입니다. 이 파일은 모델을 학습 합니다.
* `scripts\utils.py`: 학습 스크립트에서 사용 하는 도우미 파일입니다.
* `.azureml\conda_dependencies.yml`: 학습 스크립트를 실행 하는 데 필요한 소프트웨어 종속성을 정의 합니다.
* `dataset.json`: 데이터 집합 정의입니다. Azure Machine Learning 작업 영역에 MNIST 데이터 집합을 등록 하는 데 사용 됩니다.

### <a name="deployment-files"></a>배포 파일

리포지토리에는 학습 된 모델을 웹 서비스로 배포 하는 데 사용 되는 다음 파일이 포함 되어 있습니다.

* `aciDeploymentConfig.yml`: __배포 구성__ 파일입니다. 이 파일은 모델에 필요한 호스팅 환경을 정의 합니다.
* `inferenceConfig.yml`: 유추 configuration__ 파일입니다. 이 파일은 서비스에서 모델을 사용 하 여 데이터의 점수를 매기는 데 사용 하는 소프트웨어 환경을 정의 합니다.
* `score.py`: 들어오는 데이터를 수락 하 고 모델을 사용 하 여 점수를 지정한 다음 응답을 반환 하는 python 스크립트입니다.
* `scoring-env.yml`: 모델 및 `score.py` 스크립트를 실행 하는 데 필요한 conda 종속성입니다.
* `testdata.json`: 배포 된 웹 서비스를 테스트 하는 데 사용할 수 있는 데이터 파일입니다.

## <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결

CLI에서 Azure 구독에 인증 하는 방법에는 여러 가지가 있습니다. 가장 기본적인 방법은 브라우저를 사용 하 여 대화형으로 인증 하는 것입니다. 대화형으로 인증 하려면 명령줄 또는 터미널을 열고 다음 명령을 사용 합니다.

```azurecli-interactive
az login
```

CLI가 기본 브라우저를 열 수 있는 경우, 그렇게 하고 로그인 페이지를 로드합니다. 그렇지 않으면 브라우저를 열고 명령줄의 지침을 따르세요. 지침에는 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 를 찾고 인증 코드를 입력 하는 작업이 포함 됩니다.

## <a name="install-the-machine-learning-extension"></a>Machine learning 확장 설치

Machine learning 확장을 설치 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

확장이 이미 설치 되었다는 메시지가 표시 되 면 다음 명령을 사용 하 여 최신 버전으로 업데이트 합니다.

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 플랫폼의 기본 리소스 컨테이너입니다. Azure Machine Learning 작업할 때 리소스 그룹은 Azure Machine Learning 작업 영역을 포함 합니다. 또한 작업 영역에서 사용 하는 다른 Azure 서비스를 포함 합니다. 예를 들어 클라우드 기반 계산 리소스를 사용 하 여 모델을 학습 하는 경우 해당 리소스는 리소스 그룹에 만들어집니다.

__새 리소스 그룹을 만들려면__다음 명령을 사용 합니다. `<resource-group-name>`을이 리소스 그룹에 사용할 이름으로 바꿉니다. `<location>`을이 리소스 그룹에 사용할 Azure 지역으로 바꿉니다.

> [!TIP]
> Azure Machine Learning 사용할 수 있는 지역을 선택 해야 합니다. 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)을 참조 하세요.

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

이 명령의 응답은 다음 JSON과 유사 합니다.

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

리소스 그룹 작업에 대 한 자세한 내용은 [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)을 참조 하세요.

## <a name="create-a-workspace"></a>작업 영역 만들기

새 작업 영역을 만들려면 다음 명령을 사용 합니다. `<workspace-name>`를이 작업 영역에 사용할 이름으로 바꿉니다. `<resource-group-name>`을 리소스 그룹의 이름으로 바꿉니다.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사 합니다.

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>작업 영역에 로컬 프로젝트 연결

터미널 또는 명령 프롬프트에서 다음 명령을 사용 하 여 디렉터리를 `cli-train-deploy` 디렉터리로 변경한 다음 작업 영역에 연결 합니다.

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

이 명령의 출력은 다음 JSON과 유사 합니다.

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

이 명령은 작업 영역에 연결 하는 데 필요한 정보가 포함 된 `.azureml/config.json` 파일을 만듭니다. 이 자습서에서 사용 되는 나머지 `az ml` 명령은이 파일을 사용 하므로 모든 명령에 작업 영역 및 리소스 그룹을 추가 하지 않아도 됩니다.

## <a name="create-the-compute-target-for-training"></a>학습을 위한 계산 대상 만들기

이 예제에서는 Azure Machine Learning 계산 클러스터를 사용 하 여 모델을 학습 합니다. 새 계산 클러스터를 만들려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

이 명령의 출력은 다음 JSON과 유사 합니다.

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

이 명령은 최대 4 개의 노드를 사용 하 여 `cpu`라는 새 계산 대상을 만듭니다. 선택한 VM 크기는 GPU 리소스를 포함 하는 VM을 제공 합니다. VM 크기에 대 한 자세한 내용은 [VM 유형 및 크기]를 참조 하세요.

> [!IMPORTANT]
> 계산 대상의 이름 (이 경우`cpu`)은 중요 합니다. 다음 섹션에서 사용 되는 `.azureml/mnist.runconfig` 파일에서 참조 됩니다.

## <a name="define-the-dataset"></a>데이터 집합 정의

모델을 학습 하려면 데이터 집합을 사용 하 여 학습 데이터를 제공 하면 됩니다. CLI에서 데이터 집합을 만들려면 데이터 집합 정의 파일을 제공 해야 합니다. 리포지토리에 제공 된 `dataset.json` 파일은 MNIST 데이터를 사용 하 여 새 데이터 집합을 만듭니다. 만든 데이터 집합의 이름은 `mnist-dataset`입니다.

`dataset.json` 파일을 사용 하 여 데이터 집합을 등록 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

이 명령의 출력은 다음 JSON과 유사 합니다.

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```


> [!IMPORTANT]
> 다음 섹션에서 사용 되는 `id` 항목의 값을 복사 합니다.

데이터 집합에 대 한 보다 포괄적인 템플릿을 보려면 다음 명령을 사용 합니다.
```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>데이터 집합 참조

데이터 집합을 학습 환경에서 사용할 수 있도록 하려면 .runconfig 파일에서이를 참조 해야 합니다. `.azureml/mnist.runconfig` 파일에는 다음 YAML 항목이 포함 되어 있습니다.

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

데이터 집합을 등록할 때 반환 되는 값과 일치 하도록 `id` 항목의 값을 변경 합니다. 이 값은 학습 중에 계산 대상으로 데이터를 로드 하는 데 사용 됩니다.

이 YAML은 학습 중에 다음 작업을 수행 합니다.

* 학습 환경에서 데이터 집합의 ID를 기반으로 데이터 집합을 탑재 하 고 `mnist` 환경 변수에 탑재 지점에 대 한 경로를 저장 합니다.
* `--data-folder` 인수를 사용 하 여 학습 환경 내에 있는 데이터 (탑재 지점)의 위치를 스크립트에 전달 합니다.

.Runconfig 파일에는 학습 실행에 사용 되는 환경을 구성 하는 데 사용 되는 정보도 포함 됩니다. 이 파일을 검사 하면 이전에 만든 `cpu-compute` 계산 대상을 참조 하는 것을 볼 수 있습니다. 또한 학습 (`"nodeCount": "4"`) 할 때 사용할 노드 수를 나열 하 고, 학습 스크립트를 실행 하는 데 필요한 Python 패키지를 나열 하는 `"condaDependencies"` 섹션을 포함 합니다.

> [!TIP]
> .Runconfig 파일을 수동으로 만들 수 있지만이 예의 내용은 리포지토리에 포함 된 `generate-runconfig.py` 파일을 사용 하 여 만들었습니다. 이 파일은 등록 된 데이터 집합에 대 한 참조를 가져오고, 프로그래밍 방식으로 실행 구성을 만든 다음, 파일에 유지 합니다.

실행 구성 파일에 대 한 자세한 내용은 [모델 학습을 위한 계산 대상 설정 및 사용](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli)을 참조 하세요. 전체 JSON 참조는 [runconfigschema. json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)을 참조 하세요.

## <a name="submit-the-training-run"></a>학습 실행 제출

`cpu-compute` 계산 대상에서 학습 실행을 시작 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

이 명령은 실험의 이름을 지정 합니다 (`myexperiment`). 실험은이 실행에 대 한 정보를 작업 영역에 저장 합니다.

`-c mnist` 매개 변수는 `.azureml/mnist.runconfig` 파일을 지정 합니다.

`-t` 매개 변수는이 실행에 대 한 참조를 JSON 파일에 저장 하 고, 다음 단계에서 모델을 등록 하 고 다운로드 하는 데 사용 됩니다.

학습 실행 프로세스는 원격 계산 리소스의 학습 세션에서 정보를 스트리밍합니다. 정보의 일부는 다음 텍스트와 유사 합니다.

```text
Predict the test set
Accuracy is 0.9185
```

이 텍스트는 학습 스크립트에서 기록 되며 모델의 정확도를 표시 합니다. 다른 모델의 성능 메트릭은 서로 다를 수 있습니다.

학습 스크립트를 검사 하는 경우 `outputs/sklearn_mnist_model.pkl`에 학습 된 모델을 저장할 때 알파 값도 사용 한다는 것을 알 수 있습니다.

모델이 학습 된 계산 대상의 `./outputs` 디렉터리에 저장 되었습니다. 이 경우 Azure 클라우드의 Azure Machine Learning 계산 인스턴스가 있습니다. 학습 프로세스는 Azure Machine Learning 작업 영역에 대 한 학습을 수행 하는 계산 대상에서 `./outputs` 디렉터리의 콘텐츠를 자동으로 업로드 합니다. 실험의 일부로 저장 됩니다 (이 예제에서는`myexperiment`).

## <a name="register-the-model"></a>모델 등록

실험에서 저장 된 버전의 모델을 직접 등록 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

이 명령은 학습 실행에서 만든 `outputs/sklearn_mnist_model.pkl` 파일을 `mymodel`라는 새 모델 등록으로 등록 합니다. `--assets-path`은 실험에서 경로를 참조 합니다. 이 경우 실험 및 실행 정보는 학습 명령으로 만든 `runoutput.json` 파일에서 로드 됩니다. `-t registeredmodel.json`는이 명령으로 만든 새 등록 된 모델을 참조 하는 JSON 파일을 만들고 등록 된 모델에서 작동 하는 다른 CLI 명령에 사용 됩니다.

이 명령의 출력은 다음 JSON과 유사 합니다.

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>모델 버전 관리

모델에 대해 반환 된 버전 번호를 확인 합니다. 이 이름을 사용 하 여 새 모델을 등록할 때마다 버전이 증가 합니다. 예를 들어 다음 명령을 사용 하 여 모델을 다운로드 하 고 로컬 파일에서 등록할 수 있습니다.

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

첫 번째 명령은 등록 된 모델을 현재 디렉터리에 다운로드 합니다. 파일 이름은 `sklearn_mnist_model.pkl`입니다 .이 파일은 모델을 등록할 때 참조 되는 파일입니다. 두 번째 명령은 로컬 모델 (`-p "sklearn_mnist_model.pkl"`)을 이전 등록과 같은 이름으로 등록 합니다 (`mymodel`). 이번에는 반환 되는 JSON 데이터에 버전 2가 나열 됩니다.

## <a name="deploy-the-model"></a>모델 배포

모델을 배포 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.yml --dc aciDeploymentConfig.yml
```

> [!NOTE]
> "LocalWebservice의 존재 여부를 확인 하지 못했습니다."에 대 한 경고가 표시 될 수 있습니다. 로컬 웹 서비스를 배포 하지 않으므로이를 무시 해도 안전 합니다.

이 명령은 이전에 등록 한 모델의 버전 1을 사용 하 여 `myservice`라는 새 서비스를 배포 합니다.

`inferenceConfig.yml` 파일은 유추에 모델을 사용 하는 방법에 대 한 정보를 제공 합니다. 예를 들어, 항목 스크립트 (`score.py`) 및 소프트웨어 종속성을 참조 합니다. 

이 파일의 구조에 대 한 자세한 내용은 [유추 구성 스키마](reference-azure-machine-learning-cli.md#inference-configuration-schema)를 참조 하세요. 항목 스크립트에 대 한 자세한 내용은 [Azure Machine Learning를 사용 하 여 모델 배포](how-to-deploy-and-where.md#prepare-deployment-artifacts)를 참조 하세요.

`aciDeploymentConfig.yml`은 서비스를 호스트 하는 데 사용 되는 배포 환경을 설명 합니다. 배포 구성은 배포에 사용 하는 계산 형식에만 적용 됩니다. 이 경우 Azure Container Instance가 사용 됩니다. 자세한 내용은 [배포 구성 스키마](reference-azure-machine-learning-cli.md#deployment-configuration-schema)를 참조 하세요.

배포 프로세스가 완료 되기까지 몇 분이 소요 됩니다.

> [!TIP]
> 이 예제에서는 Azure Container Instances를 사용 합니다. ACI를 배포 하면 필요한 ACI 리소스가 자동으로 만들어집니다. 대신 Azure Kubernetes Service에 배포 하는 경우 AKS 클러스터를 미리 만들고 `az ml model deploy` 명령의 일부로 지정 해야 합니다. AKS에 배포 하는 예제는 [Azure Kubernetes Service 클러스터에 모델 배포](how-to-deploy-azure-kubernetes-service.md)를 참조 하세요.

몇 분 후에 다음 JSON과 유사한 정보가 반환 됩니다.

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>점수 매기기 URI

배포에서 반환 되는 `scoringUri`는 웹 서비스로 배포 된 모델에 대 한 REST 엔드포인트입니다. 다음 명령을 사용 하 여이 URI를 가져올 수도 있습니다.

```azurecli-interactive
az ml service show -n myservice
```

이 명령은 `scoringUri`를 포함 하 여 동일한 JSON 문서를 반환 합니다.

REST 엔드포인트을 사용 하 여 데이터를 서비스로 보낼 수 있습니다. 서비스로 데이터를 전송 하는 클라이언트 응용 프로그램을 만드는 방법에 대 한 자세한 내용은 [웹 서비스로 배포 된 Azure Machine Learning 모델 사용](how-to-consume-web-service.md) 을 참조 하세요.

### <a name="send-data-to-the-service"></a>서비스에 데이터 보내기

엔드포인트을 호출 하는 클라이언트 응용 프로그램을 만들 수 있지만 machine learning CLI는 테스트 클라이언트 역할을 할 수 있는 유틸리티를 제공 합니다. 다음 명령을 사용 하 여 `testdata.json` 파일의 데이터를 서비스로 보냅니다.

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> PowerShell을 사용 하는 경우 다음 명령을 대신 사용 합니다.
>
> ```powershell
> az ml service run -n myservice -d `@testdata.json
> ```

명령의 응답은 `[ 3 ]`와 비슷합니다.

## <a name="clean-up-resources"></a>리소스 정리

> [!IMPORTANT]
> 다른 Azure Machine Learning 자습서 및 방법 아티클에 대한 필수 조건으로 만든 리소스를 사용할 수 있습니다.

### <a name="delete-deployed-service"></a>배포 된 서비스 삭제

Azure Machine Learning 작업 영역을 계속 사용 하려는 경우 비용을 줄이기 위해 배포 된 서비스를 제거 하려면 다음 명령을 사용 합니다.

```azurecli-interactive
az ml service delete -n myservice
```

이 명령은 삭제 된 서비스의 이름이 포함 된 JSON 문서를 반환 합니다. 서비스가 삭제 되기까지 몇 분 정도 걸릴 수 있습니다.

### <a name="delete-the-training-compute"></a>학습 계산 삭제

Azure Machine Learning 작업 영역을 계속 사용 하려는 경우 학습을 위해 만든 `cpu-compute` 계산 대상을 제거 하려는 경우 다음 명령을 사용 합니다.

```azurecli-interactive
az ml computetarget delete -n cpu
```

이 명령은 삭제 된 계산 대상의 ID가 포함 된 JSON 문서를 반환 합니다. 계산 대상이 삭제 되기까지 몇 분 정도 걸릴 수 있습니다.

### <a name="delete-everything"></a>모든 항목 삭제

만든 리소스를 사용 하지 않으려는 경우 추가 비용이 발생 하지 않도록 해당 리소스를 삭제 합니다.

리소스 그룹 및이 문서에서 만든 모든 Azure 리소스를 삭제 하려면 다음 명령을 사용 합니다. `<resource-group-name>`를 앞에서 만든 리소스 그룹의 이름으로 바꿉니다.

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>다음 단계

이 Azure Machine Learning 자습서에서는 Machine Learning CLI를 사용 하 여 다음 작업을 수행 했습니다.

> [!div class="checklist"]
> * Machine learning 확장 설치
> * Azure Machine Learning 작업 영역 만들기
> * 모델 학습에 사용 되는 계산 리소스 만들기
> * 모델 학습에 사용 되는 데이터 집합 정의 및 등록
> * 학습 실행 시작
> * 모델 등록 및 다운로드
> * 모델을 웹 서비스로 배포
> * 웹 서비스를 사용 하 여 데이터 점수 매기기

CLI 사용에 대 한 자세한 내용은 [Azure Machine Learning에 cli 확장 사용](reference-azure-machine-learning-cli.md)을 참조 하세요.
