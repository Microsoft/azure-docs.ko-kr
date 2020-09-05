---
title: 자습서 - Azure CLI를 사용하여 VM 복원
description: Backup 및 Recovery Services를 사용하여 Azure에서 디스크를 복원하고 복구된 VM을 만드는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 56ea3de451e625ef5c55f92daa1b86bd34b1c4c4
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141349"
---
# <a name="restore-a-vm-with-azure-cli"></a>Azure CLI를 사용하여 VM 복원

Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 개별 파일을 복원할 수 있습니다. 이 문서에서는 CLI를 사용하여 전체 VM을 복원하는 방법에 대해 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 복구 지점 나열 및 선택
> * 복구 지점에서 디스크 복원
> * 복원된 디스크에서 VM 만들기

PowerShell을 사용하여 디스크를 복원하고 복구된 VM을 만드는 방법은 [PowerShell을 사용하여 Azure VM 백업 및 복원](backup-azure-vms-automation.md#restore-an-azure-vm)을 참조하세요.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.18 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서는 Azure Backup으로 보호된 Linux VM이 필요합니다. 실수로 인한 VM 삭제 및 복구 프로세스를 시뮬레이션하려면 복구 지점의 디스크에서 VM을 만듭니다. Azure Backup으로 보호된 Linux VM이 필요한 경우 [CLI를 사용하여 Azure에서 가상 머신 백업](quick-backup-vm-cli.md)을 참조하세요.

## <a name="backup-overview"></a>Backup 개요

Azure에서 백업을 시작하면 VM에 대한 백업 확장에서 특정 시점 스냅샷을 만듭니다. 첫 번째 백업이 요청될 때 백업 확장이 VM에 설치됩니다. 또한 백업이 수행될 때 VM이 실행되고 있지 않으면 Azure Backup에서 기본 스토리지의 스냅샷을 만들 수도 있습니다.

기본적으로 Azure Backup은 파일 시스템 일치 백업을 만듭니다. Azure Backup에서 스냅샷을 만들면 데이터가 Recovery Services 자격 증명 모음으로 전송됩니다. 효율성을 극대화하기 위해 Azure Backup은 이전 백업 이후에 변경된 데이터 블록만 식별하여 전송합니다.

데이터 전송이 완료되면 스냅샷이 제거되고 복구 지점이 생성됩니다.

## <a name="list-available-recovery-points"></a>사용 가능한 복구 지점 나열

디스크를 복원하려면 복구 지점을 복구 데이터에 대한 원본으로 선택합니다. 기본 정책은 매일 복구 지점을 만들고 이를 30일 동안 유지하므로 복구 시점을 특정 시점으로 선택할 수 있는 복구 지점 집합을 유지할 수 있습니다.

사용 가능한 복구 지점 목록을 보려면 [az backup recoverypoint list](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list)를 사용합니다. 복구 지점 **이름**은 디스크를 복구하는 데 사용됩니다. 이 자습서에서는 사용 가능한 최근 복구 지점을 사용하겠습니다. `--query [0].name` 매개 변수는 다음과 같이 최근 복구 지점 이름을 선택합니다.

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>VM 디스크 복원

> [!IMPORTANT]
> Az CLI 버전 2.0.74 이상을 사용하여 관리 디스크 복원을 비롯한 빠른 복원의 모든 이점을 활용할 것을 강력하게 권장합니다. 사용자가 항상 최신 버전을 사용하는 것이 가장 좋습니다.

### <a name="managed-disk-restore"></a>관리 디스크 복원

백업된 VM에 관리 디스크가 있고 복구 지점에서 관리 디스크를 복원하려는 경우 먼저 Azure 스토리지 계정을 제공해야 합니다. 이 스토리지 계정은 나중에 복원된 디스크에서 VM을 배포하는 데 사용할 수 있는 VM 구성 및 배포 템플릿을 저장하는 데 사용됩니다. 그런 다음, 관리 디스크를 복원할 대상 리소스 그룹을 입력합니다.

1. [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 사용하여 스토리지 계정을 만듭니다. 스토리지 계정 이름은 모두 소문자여야 하며 전역적으로 고유해야 합니다. *mystorageaccount*를 사용자 고유의 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks)를 사용하여 복구 지점에서 디스크를 복원합니다. *mystorageaccount*를 이전 명령에서 만든 스토리지 계정의 이름으로 바꿉니다. *myRecoveryPointName*을 이전 [az backup recoverypoint list](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 명령의 출력에서 얻은 복구 지점 이름으로 바꿉니다. ***그리고 관리 디스크를 복원할 대상 리소스 그룹을 입력합니다***.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > 대상-리소스 그룹을 입력하지 않으면 관리 디스크는 지정된 스토리지 계정에 비관리 디스크로 복원됩니다. 이 경우 지정된 스토리지 계정에 따라 디스크 복원에 걸리는 시간이 크게 달라지므로 복원 시간에 큰 영향을 미치게 됩니다. 고객은 arget-resource-group 매개 변수가 지정된 경우에만 인스턴트 복원의 이점을 얻을 수 있습니다. 관리 디스크를 관리되지 않는 상태로 복원하려는 경우에는 target-resource-group 매개 변수를 제공하지 않고 대신 아래에 표시된 대로 restore-as-unmanaged-disk 매개 변수를 제공합니다. 이 매개 변수는 az 3.4.0 이상에서 사용할 수 있습니다.

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

이렇게 하면 관리 디스크는 지정된 스토리지 계정에 관리되지 않는 디스크로 복원되며 '인스턴트' 복원 기능을 활용하지 않습니다. 이후 버전의 CLI에서는 target-resource-group 매개 변수 또는 'restore-as-unmanaged-disk' 매개 변수를 제공해야 합니다.

### <a name="unmanaged-disks-restore"></a>비관리 디스크 복원

백업된 VM에 관리되지 않는 디스크가 있고 복구 지점에서 디스크를 복원하려는 경우 먼저 Azure 스토리지 계정을 제공해야 합니다. 이 스토리지 계정은 나중에 복원된 디스크에서 VM을 배포하는 데 사용할 수 있는 VM 구성 및 배포 템플릿을 저장하는 데 사용됩니다. 기본적으로 비관리 디스크는 원래 스토리지 계정에 복원됩니다. 사용자가 모든 비관리 디스크를 한 곳에 복원하려는 경우에는 지정된 스토리지 계정을 해당 디스크의 준비 위치로도 사용할 수 있습니다.

추가 단계에서 복원된 디스크를 사용하여 VM을 만듭니다.

1. [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 사용하여 스토리지 계정을 만듭니다. 스토리지 계정 이름은 모두 소문자여야 하며 전역적으로 고유해야 합니다. *mystorageaccount*를 사용자 고유의 이름으로 바꿉니다.

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks)를 사용하여 복구 지점에서 디스크를 복원합니다. *mystorageaccount*를 이전 명령에서 만든 스토리지 계정의 이름으로 바꿉니다. *myRecoveryPointName*을 이전 [az backup recoverypoint list](/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) 명령의 출력에서 얻은 복구 지점 이름으로 바꿉니다.

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

위에서 언급했듯이, 비관리 디스크는 원래 스토리지 계정에 복원됩니다. 따라서 최상의 복원 성능을 제공합니다. 그러나 모든 비관리 디스크를 지정된 스토리지 계정에 복원해야 하는 경우에는 아래와 같이 관련 플래그를 사용합니다.

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

출력은 다음 예제와 비슷합니다. 여기서는 복원 작업 상태가 *진행 중*이라고 표시됩니다.

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

복원 작업의 *상태*가 *완료*인 경우 필요한 정보(VM 구성 및 배포 템플릿)가 스토리지 계정에 복원된 것입니다.

## <a name="create-a-vm-from-the-restored-disk"></a>복원된 디스크에서 VM 만들기

마지막 단계는 복원된 디스크에서 VM을 만드는 것입니다. 지정된 스토리지 계정에 다운로드한 배포 템플릿을 사용하여 VM을 만들 수 있습니다.

### <a name="fetch-the-job-details"></a>작업 세부 정보 가져오기

결과 작업 세부 정보는 쿼리 및 배포 가능한 템플릿 URI를 제공합니다. 트리거된 복원 작업에 대한 자세한 정보를 보려면 작업 표시 명령을 사용합니다.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

이 쿼리의 출력은 모든 세부 정보를 제공하지만, 스토리지 계정 내용에만 관심이 있습니다. Azure CLI의 [쿼리 기능](/cli/azure/query-azure-cli?view=azure-cli-latest)을 사용하여 관련 세부 정보를 가져올 수 있습니다.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>배포 템플릿 가져오기

템플릿은 고객의 스토리지 계정과 지정된 컨테이너에 있기 때문에 직접 액세스할 수 없습니다. 이 템플릿에 액세스하려면 (임시 SAS 토큰과 함께) 전체 URL이 필요합니다.

먼저 작업 세부 정보에서 템플릿 Blob URI를 추출합니다.

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

템플릿 Blob URI는 이 형식이며 템플릿 이름을 추출합니다.

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

따라서 위 예제의 템플릿은 ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```이고 컨테이너 이름은 ```myVM-daa1931199fd4a22ae601f46d8812276```입니다.

이제 [여기](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)에 설명된 대로 이 컨테이너의 SAS 토큰과 템플릿을 가져옵니다.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>템플릿을 배포하여 VM 만들기

이제 [여기](../azure-resource-manager/templates/deploy-cli.md)에 설명된 대로 템플릿을 배포하여 VM을 만듭니다.

```azurecli-interactive
az group deployment create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

VM이 복구된 디스크에서 만들어졌는지 확인하려면 다음과 같이 [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list)를 사용하여 리소스 그룹에 VM을 나열합니다.

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 복구 지점에서 디스크를 복원한 다음 디스크에서 VM을 만들었습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 복구 지점 나열 및 선택
> * 복구 지점에서 디스크 복원
> * 복원된 디스크에서 VM 만들기

복구 지점에서 개별 파일을 복원하는 방법을 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [Azure에서 가상 머신에 파일 복원](tutorial-restore-files.md)
