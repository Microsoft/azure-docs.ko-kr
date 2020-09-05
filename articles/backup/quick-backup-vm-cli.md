---
title: 빠른 시작 - Azure CLI를 사용하여 VM 백업
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만드는 방법을 살펴봅니다.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 2bd3487e58f204ba7ada9320b50d4b87538ae987
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826568"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>CLI를 사용하여 Azure에서 가상 머신 백업

명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 Azure CLI가 사용됩니다. 정기적으로 백업을 수행하여 데이터를 보호할 수 있습니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장될 수 있는 복구 지점을 만듭니다. 이 문서에서는 Azure CLI를 사용하여 Azure에서 VM(가상 머신)을 백업하는 방법을 자세히 설명합니다. [Azure PowerShell](quick-backup-vm-powershell.md)을 사용하거나 [Azure Portal](quick-backup-vm-portal.md)에서 이 단계를 수행할 수도 있습니다.

이 빠른 시작을 사용하면 기존 Azure VM에서 백업할 수 있습니다. VM을 생성해야 하는 경우 [Azure CLI를 사용하여 VM을 만들](../virtual-machines/linux/quick-create-cli.md) 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하려는 경우 Azure CLI 버전 2.0.18 이상을 사용해야 합니다. CLI 버전을 찾으려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 Azure VM과 같은 각 보호된 리소스에 대한 백업 데이터를 저장하는 논리 컨테이너입니다. 보호된 리소스에 대한 백업 작업이 실행될 때 Recovery Services 자격 증명 모음 내에 복구 지점을 만듭니다. 이러한 복구 지점 중 하나를 사용하여 지정된 특정 시점으로 데이터를 복원할 수 있습니다.

[az backup vault create](/cli/azure/backup/vault#az-backup-vault-create)를 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 보호하려는 VM으로 동일한 리소스 그룹 및 위치를 지정합니다. [VM 빠른 시작](../virtual-machines/linux/quick-create-cli.md)을 사용한 경우 다음이 만들어집니다.

- *myResourceGroup*이라는 이름의 리소스 그룹,
- *myVM*이라는 이름의 VM,
- *eastus* 위치의 리소스.

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

기본적으로 Recovery Services 자격 증명 모음은 지역 중복 스토리지에 대해 설정됩니다. 지역 중복 스토리지는 주 지역에서 수백 마일 떨어져 있는 보조 Azure 지역에 백업 데이터가 복제되었음을 보장합니다. 스토리지 중복 설정을 수정해야 하는 경우 [az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet을 사용합니다.

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Azure VM에 백업 사용

보호 정책을 만들어 백업 작업이 실행되는 시기와 복구 지점이 저장되는 기간을 정의합니다. 기본 보호 정책은 매일 백업 작업을 실행하고 복구 지점을 30일 동안 유지합니다. VM을 신속하게 보호하기 위해 이러한 기본 정책 값을 사용할 수 있습니다. VM에 대한 백업 보호 사용을 설정하려면 [az backup protection enable-for-vm](/cli/azure/backup/protection#az-backup-protection-enable-for-vm)을 사용합니다. 보호할 리소스 그룹 및 VM, 사용할 정책을 지정합니다.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> VM이 자격 증명 모음과 동일한 리소스 그룹에 없는 경우 myResourceGroup은 자격 증명 모음이 생성된 리소스 그룹을 참조합니다. VM 이름 대신 아래 표시된 대로 VM ID를 제공합니다.

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> CLI를 사용하여 한 번에 여러 VM에 대한 백업을 사용하도록 설정하는 동안 단일 정책에 100개가 넘는 VM이 연결되어 있지 않은지 확인합니다. 이는 [권장 모범 사례](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy)입니다. VM이 100개를 넘는 경우 지금은 PowerShell 클라이언트가 명시적으로 차단하고 있진 않지만 향후 확인 절차를 추가하고자 합니다.

## <a name="start-a-backup-job"></a>백업 작업 시작

예약된 시간에 작업을 실행하는 기본 정책을 대기하지 않고 지금 백업을 시작하려면 [az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now)를 사용합니다. 이 첫 번째 백업 작업에서 전체 복구 지점이 생성됩니다. 이 초기 백업 후에 각 백업 작업에서 증분 복구 지점이 생성됩니다. 증분 복구 지점은 마지막 백업 이후 변경된 내용만을 전송하기 때문에 스토리지 및 시간 효율적입니다.

다음 매개 변수는 VM을 백업하는 데 사용됩니다.

- `--container-name`은 VM의 이름임
- `--item-name`은 VM의 이름임
- `--retain-until` 값은 UTC 시간 형식(**dd-mm-yyyy**)인 최근 사용 가능한 날짜로 설정합니다. 여기서 복구 지점을 사용하려고 합니다.

다음 예제에서는 *myVM*이라는 VM을 백업하고 복구 지점의 만료를 2017년 10월 18일로 설정합니다.

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>백업 작업 모니터링

백업 작업의 상태를 모니터링하려면 [az backup job list](/cli/azure/backup/job#az-backup-job-list)를 사용합니다.

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

출력은 다음 예제와 비슷합니다. 여기서는 백업 작업 상태가 *InProgress*라고 표시됩니다.

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

백업 작업의 *상태*가 *완료됨*으로 보고되는 경우 VM은 Recovery Services로 보호되고 전체 복구 지점이 저장됩니다.

## <a name="clean-up-deployment"></a>배포 정리

더 이상 필요하지 않은 경우 VM에 대한 보호를 사용하지 않도록 설정하고, 복원 지점 및 Recovery Services 자격 증명 모음을 제거한 후 리소스 그룹 및 연결된 VM 리소스를 삭제할 수 있습니다. 기존 VM을 사용하는 경우 최종 [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete) 명령으로 건너뛰어 리소스 그룹 및 VM을 준비할 수 있습니다.

VM에 대한 데이터를 복원하는 방법을 설명하는 백업 자습서를 사용해 보려는 경우 [다음 단계](#next-steps)로 이동합니다.

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Recovery Services 자격 증명 모음을 만들고, VM에 대한 보호를 사용하도록 설정하고, 초기 복구 지점을 만들었습니다. Azure Backup 및 Recovery Services에 대한 자세한 내용은 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [여러 Azure VM 백업](./tutorial-backup-vm-at-scale.md)
