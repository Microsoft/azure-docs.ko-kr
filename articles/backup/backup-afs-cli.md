---
title: Azure CLI를 사용 하 여 Azure 파일 공유 백업
description: Azure CLI를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844044"
---
# <a name="back-up-azure-file-shares-with-cli"></a>CLI를 사용 하 여 Azure 파일 공유 백업

Azure CLI (명령줄 인터페이스)는 Azure 리소스를 관리 하기 위한 명령줄 환경을 제공 합니다. Azure 리소스를 사용 하는 사용자 지정 자동화를 빌드하기 위한 좋은 도구입니다. 이 문서에서는 Azure CLI를 사용 하 여 Azure 파일 공유를 백업 하는 방법을 자세히 설명 합니다. [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation)을 사용하거나 [Azure Portal](backup-afs.md)에서 이 단계를 수행할 수도 있습니다.

이 자습서의 끝 부분에서는 Azure CLI 사용 하 여 아래와 같은 작업을 수행 하는 방법을 설명 합니다.

* 복구 서비스 자격 증명 모음 만들기
* Azure 파일 공유에 대한 백업 사용
* 파일 공유에 대한 주문형 백업 트리거

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0.18 이상을 사용해야 합니다. CLI 버전을 찾으려면를 `run az --version`합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery services 자격 증명 모음은 모든 백업 항목에 대해 통합 된 보기 및 관리 기능을 제공 하는 엔터티입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

Recovery services 자격 증명 모음을 만들려면 다음 단계를 따르세요.

1. 자격 증명 모음은 리소스 그룹에 배치 됩니다. 기존 리소스 그룹이 없는 경우 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 를 사용 하 여 새 리소스 그룹을 만듭니다. 이 자습서에서는 미국 동부 지역에 새 리소스 그룹 *azurefiles* 를 만듭니다.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. [Az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet을 사용 하 여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    다음 예제에서는 미국 동부 지역에 *azurefilesvault* 라는 recovery services 자격 증명 모음을 만듭니다.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. 자격 증명 모음 저장소에 사용할 중복성 유형을 지정 합니다. [로컬 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) 또는 [지역 중복 스토리지](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)를 사용할 수 있습니다.

    다음 예에서는 [az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet을 사용 하 여 *azurefilesvault* 에 대한 저장소 중복성 옵션을 **Georedundant** 로 설정 합니다.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    자격 증명 모음이 성공적으로 만들어졌는지 확인 하려면 [az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) cmdlet을 사용 하 여 자격 증명 모음에 대한 세부 정보를 가져올 수 있습니다. 다음 예에서는 위의 단계에서 만든 *azurefilesvault* 의 세부 정보를 표시 합니다.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    출력은 다음 응답과 유사 합니다.

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure 파일 공유에 대한 백업 사용

이 섹션에서는 백업을 구성 하려는 Azure 파일 공유가 이미 있다고 가정 합니다. 없는 경우 [az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) 명령을 사용 하 여 Azure 파일 공유를 만듭니다.

파일 공유에 대한 백업을 사용 하도록 설정 하려면 백업 작업이 실행 되는 시기와 복구 지점이 저장 되는 기간을 정의 하는 보호 정책을 만들어야 합니다. [Az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet을 사용 하 여 백업 정책을 만들 수 있습니다.

다음 예제에서는 [az backup protection enable](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) -azurefilefilecmdlet을 사용 하 여 *schedule 1* 백업 정책을 사용 하 여 *afsaccount* 저장소 계정에서 *azurefileshare* 파일 공유에 대한 백업을 사용 하도록 설정 합니다.

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

출력의 **이름** 특성은 백업 **사용** 작업을 위해 백업 서비스에서 만든 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유에 대한 주문형 백업 트리거

예약 된 시간에 백업 정책이 작업을 실행 하기를 기다리는 대신 파일 공유에 대한 요청 시 백업을 트리거하려면 [az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) cmdlet을 사용 합니다.

요청 시 백업을 트리거하기 위해 다음 매개 변수를 정의 해야 합니다.

* **--container-name** 은 파일 공유를 호스트 하는 저장소 계정의 이름입니다. 컨테이너의 **이름** **또는 이름을 검색** 하려면 [az backup container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) 명령을 사용 합니다.
* **--name** 은 요청 시 백업을 트리거할 파일 공유의 이름입니다. 백업 된 항목 의 이름 **또는 이름을** 검색 하려면 [az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) 명령을 사용 합니다.
* **--유지-** 복구 지점을 보존할 시점까지 날짜를 지정 합니다. 값은 UTC 시간 형식 (dd-mm-yyyy)으로 설정 해야 합니다.

다음 예제에서는 azuresfiles에 대한 *20-01-2020*보존이 있는 *afsaccount* 저장소 계정에서 파일 공유에 대한 요청 시 백업을 트리거합니다.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

출력의 **name** 특성은 "주문형 백업" 작업에 대해 백업 서비스에서 만든 작업의 이름에 해당 합니다. 작업 상태를 추적 하려면 [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet을 사용 합니다.

## <a name="next-steps"></a>다음 단계

* [CLI를 사용 하 여 Azure 파일 공유를 복원](restore-afs-cli.md) 하는 방법 알아보기
* [CLI를 사용 하 여 Azure 파일 공유 ackups를 관리](manage-afs-backup-cli.md) 하는 방법 알아보기
