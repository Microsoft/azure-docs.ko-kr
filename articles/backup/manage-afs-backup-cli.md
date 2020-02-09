---
title: Azure CLI를 사용 하 여 Azure 파일 공유 백업 관리
description: Azure CLI를 사용 하 여 Azure Backup에서 백업 된 Azure 파일 공유를 관리 하 고 모니터링 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934886"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure 파일 공유 백업 관리

Azure CLI은 Azure 리소스를 관리 하기 위한 명령줄 환경을 제공 합니다. Azure 리소스를 사용 하는 사용자 지정 자동화를 빌드하기 위한 좋은 도구입니다. 이 문서에서는 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)에 의해 백업 되는 Azure 파일 공유를 관리 하 고 모니터링 하기 위한 작업을 수행 하는 방법을 설명 합니다. [Azure Portal](https://portal.azure.com/)를 사용 하 여 이러한 단계를 수행할 수도 있습니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치 하 고 사용 하려면 Azure CLI 버전 2.0.18 이상을 이상을 실행 해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)으로 백업 된 Azure 파일 공유가 이미 있다고 가정 합니다. 없으면 [CLI를 사용 하 여 Azure 파일 공유](backup-afs-cli.md) 백업을 참조 하 여 파일 공유에 대한 백업을 구성 합니다. 이 문서에서는 다음 리소스를 사용 합니다.

* **리소스 그룹**: *azurefiles*
* **Recoveryservicesvault**: *azurefilesvault*
* **Storage 계정**: *afsaccount*
* **파일 공유**: *azurefiles*

## <a name="monitor-jobs"></a>작업 모니터링

백업 또는 복원 작업을 트리거하는 경우 backup 서비스는 추적 작업을 만듭니다. 완료 되었거나 현재 실행 중인 작업을 모니터링 하려면 [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet을 사용 합니다. CLI를 사용 하 여 [현재 실행 중인 작업을 일시 중단](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) 하거나 [작업이 완료 될 때까지 기다릴](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)수도 있습니다.

다음 예에서는 *azurefilesvault* Recovery Services 자격 증명 모음에 대한 백업 작업의 상태를 표시 합니다.

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>정책 수정

[Az backup item set-policy](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)를 사용 하 여 백업 빈도 또는 보존 범위를 변경 하는 백업 정책을 수정할 수 있습니다.

정책을 변경 하려면 다음 매개 변수를 정의 합니다.

* **--container-name**: 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 **이름** **또는 이름을 검색** 하려면 [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용 합니다.
* **--name**: 정책을 변경 하려는 파일 공유의 이름입니다. 백업 된 항목 의 이름 **또는 이름을** 검색 하려면 [az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용 합니다.
* **--policy-name**: 파일 공유에 대해 설정 하려는 백업 정책의 이름입니다. [Az backup policy list](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list) 를 사용 하 여 자격 증명 모음에 대한 모든 정책을 볼 수 있습니다.

다음 예에서는 *afsaccount* 저장소 계정에 있는 *azurefiles* 파일 공유에 대한 *schedule2* 백업 정책을 설정 합니다.

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

또한 다음과 같은 두 개의 추가 매개 변수를 제공 하 여 컨테이너 및 항목에 대한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다.

* **--백업 관리-유형**: *azurestorage*
* **--워크 로드 유형**: *azurefileshare 공유*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **name** 특성은 변경 정책 작업에 대해 backup 서비스에서 만든 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="stop-protection-on-a-file-share"></a>파일 공유에 대한 보호 중지

Azure 파일 공유를 중지하는 두 가지 방법이 있습니다.

* 미래의 모든 백업 작업을 중지 하 고 복구 지점은 모두 *삭제* 합니다.
* 이후의 모든 백업 작업을 중지 하 고 복구 지점은 *그대로 둡니다* .

Azure Backup에서 생성 되는 기본 스냅숏은 유지 되므로 저장소에 복구 지점의 종료와 관련 된 비용이 있을 수 있습니다. 복구 지점이 남아 있을 경우의 혜택은 나중에 원하는 경우 파일 공유를 복원 하는 옵션입니다. 복구 지점을 유지하는 비용에 대한 자세한 내용은 [가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/storage/files)를 참조하세요. 모든 복구 지점은 삭제 하도록 선택 하는 경우 파일 공유를 복원할 수 없습니다.

파일 공유에 대한 보호를 중지 하려면 다음 매개 변수를 정의 합니다.

* **--container-name**: 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 **이름** **또는 이름을 검색** 하려면 [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용 합니다.
* **--item-name**: 보호를 중지 하려는 파일 공유의 이름입니다. 백업 된 항목 의 이름 **또는 이름을** 검색 하려면 [az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용 합니다.

### <a name="stop-protection-and-retain-recovery-points"></a>보호 중지 및 복구 지점의 보존

데이터를 유지 하는 동안 보호를 중지 하려면 [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용 합니다.

다음 예에서는 *azurefiles* 파일 공유에 대한 보호를 중지 하지만 모든 복구 지점을 유지 합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

또한 다음 두 개의 추가 매개 변수를 제공 하 여 컨테이너 및 항목에 대한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다.

* **--백업 관리-유형**: *azurestorage*
* **--워크 로드 유형**: *azurefileshare 공유*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

출력의 **이름** 특성은 보호 중지 작업에 대해 백업 서비스에서 만든 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용 합니다.

### <a name="stop-protection-without-retaining-recovery-points"></a>복구 지점이 유지 되지 않고 보호 중지

복구 지점이 유지 되지 않고 보호를 중지 하려면 [az backup protection disable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet을 사용 하 여 **삭제-백업-데이터** 옵션을 **true**로 설정 합니다.

다음 예에서는 복구 지점을 유지 하지 않고 *azurefiles* 파일 공유에 대한 보호를 중지 합니다.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

또한 다음 두 개의 추가 매개 변수를 제공 하 여 컨테이너 및 항목에 대한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다.

* **--백업 관리-유형**: *azurestorage*
* **--워크 로드 유형**: *azurefileshare 공유*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>파일 공유에 대한 보호 다시 시작

Azure 파일 공유에 대한 보호를 중지 했지만 복구 지점이 남아 있는 경우 나중에 보호를 다시 시작할 수 있습니다. 복구 지점이 유지 되지 않으면 보호를 다시 시작할 수 없습니다.

파일 공유에 대한 보호를 다시 시작 하려면 다음 매개 변수를 정의 합니다.

* **--container-name**: 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 **이름** **또는 이름을 검색** 하려면 [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용 합니다.
* **--item-name**: 보호를 다시 시작 하려는 파일 공유의 이름입니다. 백업 된 항목 의 이름 **또는 이름을** 검색 하려면 [az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용 합니다.
* **--policy-name**: 파일 공유에 대한 보호를 다시 시작 하려는 백업 정책의 이름입니다.

다음 예에서는 [az backup protection resume](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet을 사용 하 여 *schedule1* backup 정책을 통해 *azurefiles* 파일 공유에 대한 보호를 다시 시작 합니다.

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

또한 다음 두 개의 추가 매개 변수를 제공 하 여 컨테이너 및 항목에 대한 친숙 한 이름을 사용 하 여 이전 명령을 실행할 수 있습니다.

* **--백업 관리-유형**: *azurestorage*
* **--워크 로드 유형**: *azurefileshare 공유*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

출력의 **이름** 특성은 보호 다시 시작 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="unregister-a-storage-account"></a>저장소 계정 등록 취소

다른 Recovery Services 자격 증명 모음을 사용 하 여 특정 저장소 계정에서 파일 공유를 보호 하려면 먼저 해당 저장소 계정의 [모든 파일 공유에 대한 보호를 중지](#stop-protection-on-a-file-share) 합니다. 그런 다음 현재 보호에 사용 되는 Recovery Services 자격 증명 모음에서 계정을 등록 취소 합니다.

저장소 계정의 등록을 취소 하려면 컨테이너 이름을 제공 해야 합니다. 컨테이너의 **이름** **또는 이름을 검색** 하려면 [az backup container list](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용 합니다.

다음 예제에서는 [az backup container 등록 취소](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) cmdlet을 사용 하 여 *azurefilesvault* 에서 *afsaccount* 저장소 계정의 등록을 취소 합니다.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

다음 추가 매개 변수를 제공 하 여 컨테이너에 대한 친숙 한 이름을 사용 하 여 이전 cmdlet을 실행할 수도 있습니다.

* **--백업 관리-유형**: *azurestorage*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure 파일 공유 백업 문제 해결](troubleshoot-azure-files.md)을 참조 하세요.
