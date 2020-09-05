---
title: Azure Databricks와 Azure Key Vault를 사용하여 Azure Blob Storage에 액세스
description: 이 자습서에서는 Azure Key Vault에 저장된 비밀을 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스하는 방법을 알아봅니다.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: bb574bb3dd000682090c6c3f861e885761753e19
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588520"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>자습서: Azure Databricks와 Azure Key Vault를 사용하여 Azure Blob Storage에 액세스

이 자습서에서는 Azure Key Vault에 저장된 비밀을 사용하여 Azure Databricks에서 Azure Blob Storage에 액세스하는 방법을 알아봅니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 스토리지 계정 및 Blob 컨테이너 만들기
> * Key Vault 만들기 및 비밀 설정
> * Azure Databricks 작업 영역 만들기 및 Key Vault 비밀 범위 추가
> * Azure Databricks 작업 영역에서 Blob 컨테이너에 액세스

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 자습서를 시작하기 전에 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)를 설치해야 합니다.

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Azure CLI를 사용하여 스토리지 계정 및 Blob 컨테이너 만들기

Blob을 사용하려면 먼저 범용 스토리지 계정을 만들어야 합니다. [리소스 그룹](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)이 없는 경우 명령을 실행하기 전에 하나를 만듭니다. 다음 명령은 스토리지 컨테이너의 메타데이터를 만들고 표시합니다. **ID**를 복사합니다.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![위의 명령의 콘솔 출력입니다. ID는 최종 사용자가 볼 수 있도록 녹색으로 강조 표시됩니다.](../media/databricks-command-output-1.png)

Blob을 업로드하는 컨테이너를 만들려면 먼저 스스로에게 [Storage Blob Data Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 역할을 할당해야 합니다. 이 예제에서는 이전에 만든 스토리지 계정에 역할이 할당됩니다.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

이제 스토리지 계정에 역할을 할당했으므로 Blob에 대한 컨테이너를 만들 수 있습니다.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

컨테이너를 만든 후에는 해당 컨테이너에 Blob(선택한 파일)을 업로드할 수 있습니다. 이 예제에서는 helloworld로 .txt 파일을 업로드합니다.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

컨테이너에 있는 Blob을 나열하여 컨테이너에 있는지 확인합니다.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![위의 명령의 콘솔 출력입니다. 방금 컨테이너에 저장된 파일을 표시합니다.](../media/databricks-command-output-2.png)

다음 명령을 사용하여 스토리지 컨테이너의 **key1** 값을 가져옵니다. 값을 복사합니다.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![위의 명령의 콘솔 출력입니다. key1의 값은 녹색 상자에 강조 표시됩니다.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Key Vault 만들기 및 비밀 설정

다음 명령을 사용하여 Key Vault를 만듭니다. 이 명령은 Key Vault의 메타데이터도 표시합니다. **ID**및 **vaultUri**를 복사합니다.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![이미지](../media/databricks-command-output-4.png)
![위의 명령의 콘솔 출력입니다. 사용자가 볼 수 있도록 ID와 vaultUri 모두 녹색으로 강조 표시됩니다.](../media/databricks-command-output-5.png)

비밀을 만들려면 다음 명령을 사용합니다. 비밀 값을 스토리지 계정의 **key1** 값으로 설정합니다.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Azure Databricks 작업 영역 만들기 및 Key Vault 비밀 범위 추가

이 섹션은 명령줄을 통해 완료할 수 없습니다. 이 [가이드](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope)를 따릅니다. [Azure Portal](https://ms.portal.azure.com/#home)에 액세스할 때 필요합니다.

1. Azure Databricks 리소스 만들기
1. 작업 영역 시작
1. Key Vault 지원 비밀 범위 만들기

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>Azure Databricks 작업 영역에서 Blob 컨테이너에 액세스

이 섹션은 명령줄을 통해 완료할 수 없습니다. 이 [가이드](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks)를 따릅니다. Azure Databricks 작업 영역을 사용하여 다음 작업을 수행해야 합니다.

1. **새 클러스터** 만들기
1. **새 Notebook** 만들기
1. Python 스크립트에서 해당 필드 입력
1. Python 스크립트 실행

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>다음 단계

Key Vault를 복구할 수 있는지 확인합니다.
> [!div class="nextstepaction"]
> [리소스 정리](https://docs.microsoft.com/azure/azure-resource-manager/management/delete-resource-group?tabs=azure-powershell)
