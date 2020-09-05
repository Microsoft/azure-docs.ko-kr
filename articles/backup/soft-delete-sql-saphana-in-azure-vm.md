---
title: Azure vm의 SQL server에 대 한 일시 삭제 및 Azure VM 워크 로드의 SAP HANA
description: Azure VM의 SQL server에 대 한 일시 삭제 및 Azure VM 워크 로드의 SAP HANA에 대 한 자세한 내용은 백업을 더욱 안전 하 게 만듭니다.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 26525ec758b3a27d6e0e1b9754b11041bd1fa0d2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022295"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure vm의 SQL server에 대 한 일시 삭제 및 Azure VM 워크 로드의 SAP HANA

이제 Azure Backup은 Azure VM의 SQL server에 대 한 일시 삭제와 Azure VM 워크 로드의 SAP HANA를 제공 합니다. 이는 이미 지원 되는 [Azure Virtual machine 일시 삭제 시나리오](soft-delete-virtual-machines.md)와 함께 사용할 수 있습니다.

[일시 삭제](backup-azure-security-feature-cloud.md) 는 삭제 후에도 백업 데이터를 보호 하는 보안 기능입니다. 일시 삭제를 사용 하는 경우 악의적인 행위자가 데이터베이스의 백업 (또는 실수로 삭제 된 백업 데이터)을 삭제 하더라도 백업 데이터는 14 일 동안 보존 됩니다. 이렇게 하면 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대 한 14 일의 추가 보존은 고객에 게 비용을 부과 하지 않습니다.

>[!NOTE]
>구독에 대해 미리 보기가 활성화 된 후에는 동일한 자격 증명 모음에서 가상 컴퓨터에 대해 사용 하도록 설정 된 상태로 유지 하면서 SQL server 또는 SAP HANA Db에 대해서만 일시 삭제를 사용 하지 않도록 설정할 수 없습니다. 세부적인 제어를 위해 별도의 자격 증명 모음을 만들 수 있습니다.

## <a name="steps-to-enroll-in-preview"></a>미리 보기에 등록 하는 단계

1. Azure 계정에 로그인 합니다.

   ```powershell
   Login-AzureRmAccount
   ```

2. 미리 보기에 등록 하려는 구독을 선택 합니다.

   ```powershell
   Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
   ```

3. 이 구독을 미리 보기 프로그램에 등록 합니다.

   ```powershell
   Register-AzureRMProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

4. 구독이 미리 보기에 등록 될 때까지 30 분 동안 기다립니다.

5. 상태를 확인 하려면 다음 cmdlet을 실행 합니다.

   ```powershell
   Get-AzureRmProviderFeature -FeatureName WorkloadBackupSoftDelete -ProviderNamespace Microsoft.RecoveryServices
   ```

6. 구독이 등록 된 것으로 표시 되 면 다음 명령을 실행 합니다.

   ```powershell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

>[!NOTE]
>일시 삭제를 사용 하는 구독에서 새 자격 증명 모음/자격 증명 모음이 생성 될 때마다 새로 만든 자격 증명 모음에 대해이 기능을 사용 하려면 다음 명령을 다시 실행 해야 합니다.<BR>
> `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Azure Portal를 사용 하 여 Azure VM의 SQL server에 대 한 일시 삭제

>[!NOTE]
>이러한 지침은 Azure VM의 SAP HANA에도 적용 됩니다.

1. SQL server에서 데이터베이스의 백업 데이터를 삭제 하려면 백업을 중지 해야 합니다. Azure Portal에서 Recovery Services 자격 증명 모음으로 이동한 후 백업 항목으로 이동 하 고 **백업 중지**를 선택 합니다.

   ![백업 중지](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. 다음 창에는 백업 데이터를 삭제 하거나 보존할 수 있는 옵션이 제공 됩니다. **백업 데이터 삭제**를 선택 하면 데이터베이스 백업이 영구적으로 삭제 되지 않습니다. 대신 백업 데이터는 일시 삭제 된 상태에서 14 일 동안 보존 됩니다. 첫 번째, 12, 15 일에 대 한 일반 경고 전자 메일을 사용 하 여 15 일 후에 데이터베이스의 백업 상태를 사용자에 게 알릴 때까지 삭제는 지연 됩니다.

   ![백업 데이터 삭제](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. 이 14 일 동안 Recovery Services 자격 증명 모음에서 일시 삭제 된 항목 옆에 빨간색 "일시 삭제" 아이콘이 표시 됩니다.

   ![일시 삭제 된 항목](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. 일시 삭제 된 DB를 복원 하려면 먼저 삭제를 취소 해야 합니다. 삭제를 취소 하려면 일시 삭제 된 DB를 선택한 다음 **삭제 취소**옵션을 선택 합니다.

   ![데이터베이스 삭제 취소](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   삭제 취소를 선택 하면 데이터베이스의 모든 복원 지점이 삭제 취소 되 고 복원 작업을 수행 하는 데 사용할 수 있다는 경고가 표시 됩니다. 백업 항목은 백업 정책이 적용 되지 않고 일시 중지 되 고 백업 데이터가 영구적으로 보존 되는 "데이터 보존을 사용 하 여 보호 중지" 상태로 유지 됩니다.

   ![삭제 취소 경고](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. 이때 선택한 일시 삭제 된 백업 항목에 대해 **복원** 을 선택 하 여 데이터를 복원할 수도 있습니다.

   ![VM 복원](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. 삭제 취소 프로세스가 완료 되 면 상태는 "데이터 보존으로 백업 중지"로 돌아간 다음 **백업 다시 시작**을 선택할 수 있습니다. 백업 **다시 시작** 작업은 백업 및 보존 일정을 정의 하는 사용자가 선택한 백업 정책과 연결 된 활성 상태의 백업 항목을 다시 가져옵니다.

   ![백업 다시 시작](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 VM의 SQL server에 대 한 일시 삭제

>[!NOTE]
>Azure PowerShell를 사용 하 여 일시 삭제를 사용 하는 데 필요한 Az Services 버전은 최소 2.2.0입니다. `Install-Module -Name Az.RecoveryServices -Force`를 사용 하 여 최신 버전을 가져옵니다.

Azure PowerShell 사용에 대 한 단계 순서는 위에 설명 된 Azure Portal와 동일 합니다.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 백업 항목 삭제

[AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용 하 여 백업 항목을 삭제 합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

백업 항목의 **deletestate** 가 **Notdeleted** 에서 **tobedeleted**로 변경 됩니다. 백업 데이터는 14 일 동안 보존 됩니다. 삭제 작업을 되돌리려면 실행 취소-삭제를 수행 해야 합니다.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 삭제 작업 취소

먼저 일시 삭제 상태 (즉, 삭제 하려고 함)에 있는 관련 백업 항목을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

그런 다음 실행 취소- [AzRecoveryServicesBackupItemDeletion](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet을 사용 하 여 실행 취소 작업을 수행 합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

백업 항목의 아웃 **공간** 을 **notdeleted**로 되돌립니다. 그러나 보호는 계속 중지 됩니다. 백업을 다시 시작 하 여 보호를 다시 사용 하도록 설정 합니다.

## <a name="how-to-disable-soft-delete"></a>일시 삭제를 사용 하지 않도록 설정 하는 방법

이 기능은 사용 하지 않는 것이 좋습니다. 일시 삭제를 사용 하지 않도록 설정 해야 하는 유일한 경우는 보호 된 항목을 새 자격 증명 모음으로 이동할 계획인 경우이 고, 테스트 환경에서와 같이 삭제 하 고 다시 보호 하기 전에 14 일을 기다릴 수 없는 경우입니다. 일시 삭제를 사용 하지 않도록 설정 하는 방법에 대 한 지침은 [일시 삭제 사용 및 사용 안 함](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- 일시 삭제에 대 한 [자주 묻는 질문](backup-azure-security-feature-cloud.md#frequently-asked-questions) 읽기
- [Azure Backup의 모든 보안 기능](security-overview.md) 에 대해 읽기
