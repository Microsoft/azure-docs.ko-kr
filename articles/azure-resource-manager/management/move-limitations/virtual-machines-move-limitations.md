---
title: 새 구독 또는 리소스 그룹으로 Azure Vm 이동
description: Azure Resource Manager를 사용 하 여 가상 컴퓨터를 새 리소스 그룹 또는 구독으로 이동할 수 있습니다.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 3878113f6874c40953bec87518a89519bdc6cb1a
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230962"
---
# <a name="move-guidance-for-virtual-machines"></a>가상 컴퓨터에 대 한 이동 지침

이 문서에서는 현재 지원 되지 않는 시나리오와 백업을 사용 하 여 가상 컴퓨터를 이동 하는 단계에 대해 설명 합니다.

## <a name="scenarios-not-supported"></a>지원 되지 않는 시나리오

다음 시나리오는 아직 지원되지 않습니다.

* 표준 SKU Load Balancer 또는 표준 SKU 공용 IP를 사용 하는 Virtual Machine Scale Sets는 이동할 수 없습니다.
* 요금제가 연결 된 Marketplace 리소스에서 만든 가상 머신은 구독 간에 이동할 수 없습니다. 현재 구독에서 가상 컴퓨터의 프로 비전을 해제 하 고 새 구독에 다시 배포 합니다.
* 가상 네트워크의 모든 리소스를 이동 하지 않는 경우 기존 가상 네트워크의 가상 컴퓨터를 새 구독으로 이동할 수 없습니다.
* 낮은 우선 순위의 가상 컴퓨터 및 우선 순위가 낮은 가상 컴퓨터 크기 집합은 리소스 그룹 또는 구독 간에 이동할 수 없습니다.
* 가용성 집합의 가상 머신은 개별적으로 이동할 수 없습니다.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

키 자격 증명 모음과 통합 된 가상 컴퓨터를 이동 하 여 Linux Vm 또는 [Windows vm 용 Azure Disk Encryption](../../../virtual-machines/windows/disk-encryption-overview.md) [에 대 한 Azure Disk Encryption](../../../virtual-machines/linux/disk-encryption-overview.md) 를 구현할 수 없습니다. VM을 이동 하려면 암호화를 사용 하지 않도록 설정 해야 합니다.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup를 사용 하는 가상 머신

Azure Backup를 사용 하 여 구성 된 가상 컴퓨터를 이동 하려면 자격 증명 모음에서 복원 위치를 삭제 해야 합니다.

가상 컴퓨터에 대해 [일시 삭제](../../../backup/backup-azure-security-feature-cloud.md) 를 사용 하는 경우 해당 복원 지점이 유지 되는 동안에는 가상 컴퓨터를 이동할 수 없습니다. [일시 삭제를 사용 하지 않도록 설정](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) 하거나 복원 지점이 삭제 된 후 14 일 동안 기다립니다.

### <a name="portal"></a>포털

1. 백업을 일시적으로 중지 하 고 백업 데이터를 보존 합니다.
2. Azure Backup로 구성 된 가상 컴퓨터를 이동 하려면 다음 단계를 수행 합니다.

   1. 가상 컴퓨터의 위치를 찾습니다.
   2. 다음 명명 패턴을 사용 하 여 리소스 그룹을 `AzureBackupRG_<VM location>_1` 찾습니다. 예를 들어 이름은 *AzureBackupRG_westus2_1*형식입니다.
   3. Azure Portal에서 **숨겨진 형식 표시**를 선택 합니다.
   4. 이름 지정 패턴이 있는 **restorePointCollections/** 형식의 리소스를 찾습니다 `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
   6. 삭제 작업을 완료 한 후 가상 컴퓨터를 이동할 수 있습니다.

3. VM을 대상 리소스 그룹으로 이동합니다.
4. 백업을 다시 시작 합니다.

### <a name="powershell"></a>PowerShell

1. 가상 컴퓨터의 위치를 찾습니다.

1. 이름 지정 패턴이 포함 된 리소스 그룹을 찾습니다 `AzureBackupRG_<VM location>_1` . 예를 들어 이름은 일 수 있습니다 `AzureBackupRG_westus2_1` .

1. 다음 명령을 사용 하 여 복원 지점 컬렉션을 가져옵니다.

   ```azurepowershell
   $RestorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

1. 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurepowershell
   Remove-AzResource -ResourceId $RestorePointCollection.ResourceId -Force
   ```

### <a name="azure-cli"></a>Azure CLI

1. 가상 컴퓨터의 위치를 찾습니다.

1. 이름 지정 패턴이 포함 된 리소스 그룹을 찾습니다 `AzureBackupRG_<VM location>_1` . 예를 들어 이름은 일 수 있습니다 `AzureBackupRG_westus2_1` .

1. 다음 명령을 사용 하 여 복원 지점 컬렉션을 가져옵니다.

   ```azurecli
   az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections
   ```

1. 명명 패턴을 사용 하 여 리소스에 대 한 리소스 ID를 찾습니다. `AzureBackup_<VM name>_###########`

1. 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurecli
   az resource delete --ids /subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.Compute/restorePointCollections/<name>
   ```

## <a name="next-steps"></a>다음 단계

* 리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.

* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.
