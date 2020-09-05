---
title: 가상 머신의 일시 삭제
description: 가상 컴퓨터에 대 한 일시 삭제로 백업을 더 안전 하 게 만드는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/30/2020
ms.custom: references_regions
ms.openlocfilehash: a8b70d4c8240d096c19e5a8d7449921557b8896c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022244"
---
# <a name="soft-delete-for-virtual-machines"></a>가상 머신의 일시 삭제

Vm에 대 한 일시 삭제는 의도 하지 않은 삭제 로부터 Vm의 백업을 보호 합니다. 백업이 삭제 된 후에도 14 일 동안 일시 삭제 상태로 유지 됩니다.

> [!NOTE]
> 일시 삭제는 삭제 된 백업 데이터만 보호 합니다. 백업을 사용 하지 않고 VM을 삭제 하면 일시 삭제 기능은 데이터를 보존 하지 않습니다. 모든 리소스는 전체 복원 력을 보장 하기 위해 Azure Backup로 보호 되어야 합니다.
>

## <a name="supported-regions"></a>지원되는 지역

일시 삭제는 현재 미국 서 부에서 지원 됩니다. 동아시아, 캐나다 중부, 캐나다 동부, 프랑스 중부, 프랑스 남부, 대한민국 중부, 대한민국 남부, 영국 남부, 영국 서부, 오스트레일리아 동부, 오스트레일리아 남부 동부, 미국 서 부, 미국 미국, 미국 서 부, 미국 중 북부, 미국 중 동아시아 북부, 미국 동부, 일본 서 부, 인도 남부, 인도 중부, 인도 서 부, 미국 동부 2 , 스위스 북부, 스위스 서부, 노르웨이 서 부, 노르웨이 동부 및 모든 국가 지역입니다.

## <a name="soft-delete-for-vms-using-azure-portal"></a>Azure Portal를 사용 하는 Vm에 대 한 일시 삭제

1. VM의 백업 데이터를 삭제 하려면 백업을 중지 해야 합니다. Azure Portal에서 Recovery Services 자격 증명 모음으로 이동 하 여 백업 항목을 마우스 오른쪽 단추로 클릭 하 고 **백업 중지**를 선택 합니다.

   ![Azure Portal 백업 항목의 스크린샷](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 다음 창에는 백업 데이터를 삭제 하거나 보존할 수 있는 옵션이 제공 됩니다. **백업 데이터 삭제** 를 선택한 다음 **백업 중지**를 선택 하면 VM 백업이 영구적으로 삭제 되지 않습니다. 대신 백업 데이터는 일시 삭제 된 상태에서 14 일 동안 보존 됩니다. **백업 데이터 삭제** 를 선택 하면 사용자에 게 14 일 동안 백업 데이터에 대 한 보존 기간이 남아 있음을 알리는 전자 메일 삭제 경고가 구성 된 전자 메일 ID로 전송 됩니다. 또한 12 일에 전자 메일 경고가 전송 되어 삭제 된 데이터를 부활 시킬 두 일이 남아 있음을 알려 줍니다. 영구적 삭제가 발생 하 고 데이터의 영구 삭제를 알리는 최종 전자 메일 경고가 전송 될 때 15 일이 지연 됩니다.

   ![Azure Portal, 백업 중지 화면 스크린샷](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 이 14 일 동안 Recovery Services 자격 증명 모음에 일시 삭제 된 VM이 옆에 빨간색 "일시 삭제" 아이콘이 표시 됩니다.

   ![Azure Portal의 스크린샷, 일시 삭제 상태의 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 일시 삭제 된 백업 항목이 자격 증명 모음에 있으면 해당 시점에 자격 증명 모음을 삭제할 수 없습니다. 백업 항목이 영구적으로 삭제 되 고 자격 증명 모음에 일시 삭제 됨 상태의 항목이 없는 경우 자격 증명 모음을 삭제 해 보세요.

4. 일시 삭제 된 VM을 복원 하려면 먼저 삭제를 취소 해야 합니다. 삭제를 취소 하려면 일시 삭제 된 VM을 선택한 다음 **삭제 취소**옵션을 선택 합니다.

   ![Azure Portal의 스크린샷, VM 삭제 취소](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   삭제 취소를 선택 하면 VM에 대 한 모든 복원 지점이 삭제 취소 되 고 복원 작업을 수행 하는 데 사용할 수 있다는 경고가 표시 됩니다. VM은 백업 정책이 적용 되지 않고 일시 중지 되 고 백업 데이터가 영구적으로 보존 되는 "데이터 보존을 사용 하 여 보호 중지" 상태로 유지 됩니다.

   ![Azure Portal의 스크린샷, VM 삭제 취소 확인](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   이 시점에서 선택한 복원 지점에서 **Vm 복원** 을 선택 하 여 vm을 복원할 수도 있습니다.  

   ![Azure Portal, VM 복원 옵션의 스크린샷](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 사용자가 **백업 다시 시작** 작업을 수행한 후에만 가비지 수집기가 만료 된 복구 지점만 실행 및 정리 합니다.

5. 삭제 취소 프로세스가 완료 되 면 상태는 "데이터 보존으로 백업 중지"로 돌아간 다음 **백업 다시 시작**을 선택할 수 있습니다. 백업 **다시 시작** 작업은 백업 및 보존 일정을 정의 하는 사용자가 선택한 백업 정책과 연결 된 활성 상태의 백업 항목을 다시 가져옵니다.

   ![Azure Portal, 백업 다시 시작 옵션의 스크린샷](./media/backup-azure-security-feature-cloud/resume-backup.png)

## <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure PowerShell를 사용 하는 Vm에 대 한 일시 삭제

> [!IMPORTANT]
> Azure PowerShell를 사용 하 여 일시 삭제를 사용 하는 데 필요한 Az Services 버전은 최소 2.2.0입니다. ```Install-Module -Name Az.RecoveryServices -Force```를 사용 하 여 최신 버전을 가져옵니다.

Azure Portal에 대해 위에서 설명한 것 처럼 Azure PowerShell를 사용 하는 동안 단계 순서가 동일 합니다.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 백업 항목 삭제

[AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용 하 여 백업 항목을 삭제 합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

백업 항목의 ' e n t e r '가 ' NotDeleted '에서 ' ToBeDeleted '로 변경 됩니다. 백업 데이터는 14 일 동안 보존 됩니다. 삭제 작업을 되돌리려면 실행 취소-삭제를 수행 해야 합니다.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 삭제 작업 취소

먼저 일시 삭제 상태 (즉, 삭제 하려고 함)에 있는 관련 백업 항목을 가져옵니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

그런 다음 실행 취소- [AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet을 사용 하 여 실행 취소 작업을 수행 합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

백업 항목의 ' DeleteState '는 ' NotDeleted '로 돌아갑니다. 그러나 보호는 계속 중지 됩니다. [백업을 다시 시작](./backup-azure-vms-automation.md#change-policy-for-backup-items) 하 여 보호를 다시 사용 하도록 설정 합니다.

## <a name="soft-delete-for-vms-using-rest-api"></a>REST API를 사용 하는 Vm에 대 한 일시 삭제

- [여기](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)에 설명 된 대로 REST API를 사용 하 여 백업을 삭제 합니다.
- 이러한 삭제 작업을 실행 취소 하려면 [여기](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)에 설명 된 단계를 참조 하세요.

## <a name="how-to-disable-soft-delete"></a>일시 삭제를 사용 하지 않도록 설정 하는 방법

이 기능은 사용 하지 않는 것이 좋습니다. 일시 삭제를 사용 하지 않도록 설정 해야 하는 유일한 경우는 보호 된 항목을 새 자격 증명 모음으로 이동할 계획인 경우이 고, 테스트 환경에서와 같이 삭제 하 고 다시 보호 하기 전에 14 일을 기다릴 수 없는 경우입니다. 일시 삭제를 사용 하지 않도록 설정 하는 방법에 대 한 지침은 [일시 삭제 사용 및 사용 안 함](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 일시 삭제에 대 한 [자주 묻는 질문](backup-azure-security-feature-cloud.md#frequently-asked-questions) 읽기
- [Azure Backup의 모든 보안 기능](security-overview.md) 에 대해 읽기
