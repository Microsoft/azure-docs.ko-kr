---
title: 클라우드 워크 로드를 보호 하는 데 도움이 되는 보안 기능
description: Azure Backup의 보안 기능을 사용 하 여 백업을 더 안전 하 게 만드는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 20cf322dec0827c00b15a62bf4f7695fc4ed0992
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705499"
---
# <a name="security-features-to-help-protect-cloud-workloads-that-use-azure-backup"></a>Azure Backup를 사용 하는 클라우드 워크 로드를 보호 하는 데 도움이 되는 보안 기능

맬웨어, 랜섬웨어 및 침입 같은 보안 문제에 대한 우려가 증가하고 있습니다. 이 보안 문제는 비용과 데이터 측면 모두에서 지출이 클 수 있습니다. 이러한 공격 으로부터 보호 하기 위해 Azure Backup는 삭제 후에도 백업 데이터를 보호 하는 데 도움이 되는 보안 기능을 제공 합니다.

이러한 기능 중 하나는 일시 삭제입니다. 일시 삭제를 사용 하는 경우 악성 행위자가 VM의 백업 (또는 실수로 삭제 됨)을 삭제 하더라도 백업 데이터는 14 일 동안 보존 되므로 데이터 손실 없이 해당 백업 항목을 복구할 수 있습니다. "일시 삭제" 상태의 백업 데이터에 대한 14 일의 추가 보존은 고객에 게 비용을 부과 하지 않습니다. 또한 Azure는 데이터를 더욱 안전 하 게 보호 하기 위해 [저장소 서비스 암호화](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) 를 사용 하 여 미사용 백업 된 데이터를 모두 암호화 합니다.

Azure virtual machines에 대한 일시 삭제 보호는 일반적으로 사용할 수 있습니다.

>[!NOTE]
>Azure VM의 SQL server에 대한 일시 삭제 및 Azure VM 워크 로드의 SAP HANA에 대한 일시 삭제는 이제 미리 보기로 제공 됩니다.<br>
>미리 보기에 등록 하려면 AskAzureBackupTeam@microsoft.com에 씁니다.

## <a name="soft-delete"></a>일시 삭제

### <a name="soft-delete-for-vms"></a>Vm에 대한 일시 삭제

Vm에 대한 일시 삭제는 의도 하지 않은 삭제 로부터 Vm의 백업을 보호 합니다. 백업이 삭제 된 후에도 14 일 동안 일시 삭제 상태로 유지 됩니다.

> [!NOTE]
> 일시 삭제는 삭제 된 백업 데이터만 보호 합니다. 백업을 사용 하지 않고 VM을 삭제 하면 일시 삭제 기능은 데이터를 보존 하지 않습니다. 모든 리소스는 전체 복원 력을 보장 하기 위해 Azure Backup로 보호 되어야 합니다.
>

### <a name="supported-regions"></a>지원되는 지역

일시 삭제는 현재 미국 서 부, 동아시아, 캐나다 중부, 캐나다 동부, 프랑스 중부, 프랑스 남부, 대한민국 중부, 대한민국, 영국 남부, 영국 서부, 오스트레일리아 동부, 오스트레일리아 남부 동부, 유럽 서 부, 미국 서 부, 미국 서 부, 미국 미국, 미국 서 부에서 지원 됩니다. 동아시아, 미국 중 북부, 미국 중 북부, 일본 동부, 일본 서 부, 인도 남부, 인도 중부, 인도 서 부, 미국 동부 2, 스위스 북부, 스위스 서부, 모든 국가 지역.

### <a name="soft-delete-for-vms-using-azure-portal"></a>Azure Portal를 사용 하는 Vm에 대한 일시 삭제

1. VM의 백업 데이터를 삭제 하려면 백업을 중지 해야 합니다. Azure Portal 복구 서비스 자격 증명 모음으로 이동 하 여 백업 항목을 마우스 오른쪽 단추로 클릭 하 고 **백업 중지**를 선택 합니다.

   ![Azure Portal 백업 항목의 스크린샷](./media/backup-azure-security-feature-cloud/backup-stopped.png)

2. 다음 창에는 백업 데이터를 삭제 하거나 보존할 수 있는 옵션이 제공 됩니다. **백업 데이터 삭제** 를 선택한 다음 **백업 중지**를 선택 하면 VM 백업이 영구적으로 삭제 되지 않습니다. 대신 백업 데이터는 일시 삭제 된 상태에서 14 일 동안 보존 됩니다. **백업 데이터 삭제** 를 선택 하면 사용자에 게 14 일 동안 백업 데이터에 대한 보존 기간이 남아 있음을 알리는 전자 메일 삭제 경고가 구성 된 전자 메일 ID로 전송 됩니다. 또한 12 일에 전자 메일 경고가 전송 되어 삭제 된 데이터를 부활 시킬 두 일이 남아 있음을 알려 줍니다. 영구적 삭제가 발생 하 고 데이터의 영구 삭제를 알리는 최종 전자 메일 경고가 전송 될 때 15 일이 지연 됩니다.

   ![Azure Portal, 백업 중지 화면 스크린샷](./media/backup-azure-security-feature-cloud/delete-backup-data.png)

3. 이 14 일 동안 Recovery Services 자격 증명 모음에 일시 삭제 된 VM이 옆에 빨간색 "일시 삭제" 아이콘이 표시 됩니다.

   ![Azure Portal의 스크린샷, 일시 삭제 상태의 VM](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

   > [!NOTE]
   > 일시 삭제 된 백업 항목이 자격 증명 모음에 있으면 해당 시점에 자격 증명 모음을 삭제할 수 없습니다. 백업 항목이 영구적으로 삭제 된 후 자격 증명 모음 삭제를 시도 하 고, 자격 증명 모음에 일시 삭제 됨 상태의 항목이 없습니다.

4. 일시 삭제 된 VM을 복원 하려면 먼저 삭제를 취소 해야 합니다. 삭제를 취소 하려면 일시 삭제 된 VM을 선택한 다음 **삭제 취소**옵션을 선택 합니다.

   ![Azure Portal의 스크린샷, VM 삭제 취소](./media/backup-azure-security-feature-cloud/choose-undelete.png)

   삭제 취소를 선택 하면 VM에 대한 모든 복원 지점이 삭제 취소 되 고 복원 작업을 수행 하는 데 사용할 수 있다는 경고가 표시 됩니다. VM은 백업 정책이 적용 되지 않고 일시 중지 되 고 백업 데이터가 영구적으로 보존 되는 "데이터 보존을 사용 하 여 보호 중지" 상태로 유지 됩니다.

   ![Azure Portal의 스크린샷, VM 삭제 취소 확인](./media/backup-azure-security-feature-cloud/undelete-vm.png)

   이 시점에서 선택한 복원 지점에서 **Vm 복원** 을 선택 하 여 vm을 복원할 수도 있습니다.  

   ![Azure Portal, VM 복원 옵션의 스크린샷](./media/backup-azure-security-feature-cloud/restore-vm.png)

   > [!NOTE]
   > 사용자가 **백업 다시 시작** 작업을 수행한 후에만 가비지 수집기가 만료 된 복구 지점만 실행 및 정리 합니다.

5. 삭제 취소 프로세스가 완료 되 면 상태는 "데이터 보존으로 백업 중지"로 돌아간 다음 **백업 다시 시작**을 선택할 수 있습니다. 백업 **다시 시작** 작업은 백업 및 보존 일정을 정의 하는 사용자가 선택한 백업 정책과 연결 된 활성 상태의 백업 항목을 다시 가져옵니다.

   ![Azure Portal, 백업 다시 시작 옵션의 스크린샷](./media/backup-azure-security-feature-cloud/resume-backup.png)

이 순서도는 일시 삭제를 사용 하는 경우 백업 항목의 여러 단계와 상태를 보여 줍니다.

![일시 삭제 된 백업 항목의 수명 주기](./media/backup-azure-security-feature-cloud/lifecycle.png)

자세한 내용은 아래의 질문과 대답 (질문과 [대답](backup-azure-security-feature-cloud.md#frequently-asked-questions) ) 섹션을 참조 하세요.

### <a name="soft-delete-for-vms-using-azure-powershell"></a>Azure Powershell을 사용 하 여 Vm에 대한 일시 삭제

> [!IMPORTANT]
> Azure PS를 사용 하 여 일시 삭제를 사용 하는 데 필요한 Az Service 버전은 min 2.2.0입니다. ```Install-Module -Name Az.RecoveryServices -Force```를 사용 하 여 최신 버전을 가져옵니다.

Azure Portal에 대해 위에서 설명한 것 처럼 Azure Powershell을 사용 하는 동안 단계 순서가 동일 합니다.

#### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure Powershell을 사용 하 여 백업 항목 삭제

[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS cmdlet을 사용 하 여 백업 항목을 삭제 합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

백업 항목의 ' e n t e r '가 ' NotDeleted '에서 ' ToBeDeleted '로 변경 됩니다. 백업 데이터는 14 일 동안 보존 됩니다. 삭제 작업을 되돌리려면 실행 취소-삭제를 수행 해야 합니다.

#### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure Powershell을 사용 하 여 삭제 작업 실행 취소

먼저 일시 삭제 상태 (즉, 삭제 됨)에 있는 관련 백업 항목을 가져옵니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

그런 다음 실행 취소- [AzRecoveryServicesBackupItemDeletion](https://docs.microsoft.com/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion?view=azps-3.1.0) PS cmdlet을 사용 하 여 실행 취소 작업을 수행 합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

백업 항목의 ' DeleteState '는 ' NotDeleted '로 돌아갑니다. 그러나 보호는 계속 중지 됩니다. 보호를 다시 사용 하도록 설정 하려면 [백업을 다시 시작](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#change-policy-for-backup-items) 해야 합니다.

### <a name="soft-delete-for-vms-using-rest-api"></a>REST API를 사용 하는 Vm에 대한 일시 삭제

- [여기](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)에 설명 된 대로 REST API를 사용 하 여 백업을 삭제 합니다.
- 사용자가 이러한 삭제 작업을 실행 취소 하려면 [여기](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)에 언급 된 단계를 참조 하세요.

## <a name="disabling-soft-delete"></a>일시 삭제 사용 안 함

일시 삭제는 새로 만든 자격 증명 모음에 대해 기본적으로 사용 하도록 설정 되어 실수로 또는 악의적 삭제 로부터 백업 데이터를 보호 합니다.  이 기능을 사용 하지 않도록 설정 하는 것은 권장 되지 않습니다. 일시 삭제를 사용 하지 않도록 설정 해야 하는 유일한 경우는 보호 된 항목을 새 자격 증명 모음으로 이동할 계획인 경우이 고, 테스트 환경에서와 같이 삭제 하 고 다시 보호 하기 전에 14 일 동안 기다릴 수 없는 경우입니다. 백업 관리자만이 기능을 사용 하지 않도록 설정할 수 있습니다. 이 기능을 사용 하지 않도록 설정 하면 보호 된 항목을 모두 삭제 하면 복원 기능 없이 즉시 제거 됩니다. 일시 삭제 된 상태에서이 기능을 사용 하지 않도록 설정 된 백업 데이터는 일시 삭제 된 상태로 유지 됩니다. 이러한 항목을 즉시 영구적으로 삭제 하려면 삭제를 취소 하 고 삭제 한 후 영구적으로 삭제 해야 합니다.

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure Portal를 사용 하 여 일시 삭제 사용 안 함

일시 삭제를 사용 하지 않도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 자격 증명 모음으로 이동한 다음 **설정** -> **속성**으로 이동 합니다.
2. 속성 창에서 **보안 설정** -> **업데이트**를 선택 합니다.  
3. 보안 설정 창의 **일시 삭제**아래에서 **사용 안 함**을 선택 합니다.

![일시 삭제 사용 안 함](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure Powershell을 사용 하 여 일시 삭제 사용 안 함

> [!IMPORTANT]
> Azure PS를 사용 하 여 일시 삭제를 사용 하는 데 필요한 Az Service 버전은 min 2.2.0입니다. ```Install-Module -Name Az.RecoveryServices -Force```를 사용 하 여 최신 버전을 가져옵니다.

사용 하지 않도록 설정 하려면 [AzRecoveryServicesVaultBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty?view=azps-3.1.0) PS cmdlet을 사용 합니다.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API를 사용 하 여 일시 삭제 사용 안 함

REST API를 사용 하 여 일시 삭제 기능을 사용 하지 않도록 설정 하려면 [여기](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)에 설명 된 단계를 참조 하세요.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>일시 삭제 된 백업 항목을 영구적으로 삭제

일시 삭제 된 상태에서이 기능을 사용 하지 않도록 설정 된 백업 데이터는 일시 삭제 된 상태로 유지 됩니다. 이러한 항목을 영구적으로 삭제 하려면 삭제 한 후 삭제 하 고 다시 삭제 하 여 영구적으로 삭제 합니다.

### <a name="using-azure-portal"></a>Azure Portal 사용

다음 단계를 수행하세요.

1. [일시 삭제를 사용 하지 않도록 설정](#disabling-soft-delete)하는 단계를 수행 합니다.
2. Azure Portal에서 자격 증명 모음으로 이동 하 고, **백업 항목**으로 이동 하 고, 일시 삭제 된 VM을 선택 합니다.

![일시 삭제 된 VM 선택](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. **삭제 취소**옵션을 선택 합니다.

![삭제 취소 선택](./media/backup-azure-security-feature-cloud/choose-undelete.png)


4. 창이 표시 됩니다. **삭제 취소**를 선택 합니다.

![삭제 취소 선택](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. 백업 데이터 **삭제** 를 선택 하 여 백업 데이터를 영구적으로 삭제 합니다.

![백업 데이터 삭제 선택](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-buttom.png)

6. 복구 지점이 삭제 되도록 확인할 백업 항목의 이름을 입력 합니다.

![백업 항목의 이름 입력](https://docs.microsoft.com/azure/backup/media/backup-azure-manage-vms/delete-backup-data1.png)

7. 항목에 대한 백업 데이터를 삭제 하려면 **삭제**를 선택 합니다. 알림 메시지를 통해 백업 데이터가 삭제 되었음을 알 수 있습니다.

### <a name="using-azure-powershell"></a>Azure Powershell 사용

일시 삭제를 사용 하지 않도록 설정 하기 전에 항목이 삭제 된 경우 일시 삭제 된 상태가 됩니다. 삭제 작업을 즉시 삭제 하려면 삭제 작업을 취소 한 후 다시 수행 해야 합니다.

일시 삭제 된 상태에 있는 항목을 식별 합니다.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

일시 삭제를 사용 하도록 설정 했을 때 수행 된 삭제 작업을 취소 합니다.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

이제 일시 삭제를 사용할 수 없으므로 삭제 작업으로 인해 백업 데이터가 즉시 제거 됩니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API 사용

일시 삭제를 사용 하지 않도록 설정 하기 전에 항목이 삭제 된 경우 일시 삭제 된 상태가 됩니다. 삭제 작업을 즉시 삭제 하려면 삭제 작업을 취소 한 후 다시 수행 해야 합니다.

1. 먼저 [여기](backup-azure-arm-userestapi-backupazurevms.md#undo-the-stop-protection-and-delete-data)에 설명 된 단계를 사용 하 여 삭제 작업을 실행 취소 합니다.
2. 그런 다음 [여기](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)에 설명 된 단계를 사용 하 여 REST API를 사용 하는 일시 삭제 기능을 비활성화 합니다.
3. 그런 다음 [여기](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)에 설명 된 대로 REST API를 사용 하 여 백업을 삭제 합니다.

## <a name="encryption"></a>암호화

### <a name="encryption-of-backup-data-using-microsoft-managed-keys"></a>Microsoft 관리 키를 사용 하 여 백업 데이터 암호화

백업 데이터는 Azure Storage 암호화를 사용 하 여 자동으로 암호화 됩니다. 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. 데이터는 256 비트 AES 암호화를 사용 하 여 투명 하 게 암호화 되 고 해독 되며, 사용 가능한 가장 강력한 블록 암호화 중 하나 이며 FIPS 140-2 규격입니다. Azure Storage 암호화는 Windows의 BitLocker 암호화와 비슷합니다.

Azure 내에서 Azure storage와 자격 증명 모음 간의 전송 데이터는 HTTPS에 의해 보호 됩니다. 이 데이터는 Azure 백본 네트워크에 남아 있습니다.

자세한 내용은 [미사용 데이터에 대한 암호화 Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)를 참조 하세요. [AZURE BACKUP FAQ](https://docs.microsoft.com/azure/backup/backup-azure-backup-faq#encryption) 를 참조 하 여 암호화에 대해 발생할 수 있는 질문에 대한 답변을 받을 수 있습니다.

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 백업 데이터 암호화

Azure Virtual Machines을 백업 하는 동안 Azure Key Vault에 저장 된 암호화 키를 사용 하 여 Recovery Services 자격 증명 모음에서 백업 데이터를 암호화 하는 옵션도 있습니다.

>[!NOTE]
>이 기능은 현재 초기 사용 중입니다. 고객 관리 키를 사용 하 여 백업 데이터를 암호화 하려는 경우 [이 설문 조사](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapURE9TTDRIUEUyNFhNT1lZS1BNVDdZVllHWi4u) 를 작성 합니다. 이 기능을 사용 하는 기능에는 Azure Backup 서비스의 승인이 적용 됩니다.

### <a name="backup-of-managed-disk-vm-encrypted-using-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 된 관리 되는 디스크 VM 백업

Azure Backup를 사용 하면 고객이 관리 하는 키를 사용 하 여 암호화 된 디스크가 포함 된 Azure Virtual Machines를 백업할 수 있습니다. 자세한 내용은 [고객 관리 키를 사용 하 여 관리 디스크의 암호화](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys)를 참조 하세요.

### <a name="backup-of-encrypted-vms"></a>암호화 된 Vm 백업

Azure Backup 서비스를 사용 하 여 암호화 된 디스크로 Windows 또는 Linux Azure Vm (가상 머신)을 백업 하 고 복원할 수 있습니다. 지침은 [Azure Backup를 사용 하 여 암호화 된 가상 머신 백업 및 복원](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)을 참조 하세요.

## <a name="other-security-features"></a>기타 보안 기능

### <a name="protection-of-azure-backup-recovery-points"></a>Azure Backup 복구 지점의 보호

Recovery services 자격 증명 모음에 사용 되는 저장소 계정은 격리 되며 악의적인 목적을 위해 사용자가 액세스할 수 없습니다. 액세스는 복원과 같은 Azure Backup 관리 작업을 통해서만 허용 됩니다. 이러한 관리 작업은 RBAC (역할 기반 Access Control)를 통해 제어 됩니다.

자세한 내용은 [역할 기반 Access Control를 사용 하 여 Azure Backup 복구 지점의 관리](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault)를 참조 하세요.

## <a name="frequently-asked-questions"></a>FAQ(질문과 대답)

### <a name="for-soft-delete"></a>일시 삭제의 경우

#### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>모든 자격 증명 모음에서 일시 삭제 기능을 사용 하도록 설정 해야 하나요?

아니요, 모든 recovery services 자격 증명 모음에 대해 기본적으로 빌드되고 사용 하도록 설정 됩니다.

#### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-delete-operation-is-complete"></a>삭제 작업이 완료 된 후 내 데이터가 일시 삭제 된 상태로 유지 되는 일 수를 구성할 수 있나요?

아니요, 삭제 작업 후 14 일 동안 추가 보존 기간이 수정 됩니다.

#### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>이 14 일 추가 보존에 대한 비용을 지불 해야 하나요?

아니요,이 14 일 추가 보존은 일시 삭제 기능의 일부로 무료로 제공 됩니다.

#### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>내 데이터가 일시 삭제 상태일 때 복원 작업을 수행할 수 있나요?

아니요, 복원 하려면 일시 삭제 된 리소스를 삭제 취소 해야 합니다. 삭제 취소 작업은 모든 특정 시점으로 복원할 수 있는 **데이터 보관을 사용 하 여 보호 중지 상태로** 리소스를 다시 가져옵니다. 가비지 수집기는이 상태에서 일시 중지 된 상태로 유지 됩니다.

#### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>내 스냅숏은 자격 증명 모음에서 복구 지점과 동일한 수명 주기를 따르고 있나요?

예.

#### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>일시 삭제 된 리소스에 대해 예약 된 백업을 다시 트리거하는 방법

다시 시작 작업 후 삭제 취소 작업은 리소스를 다시 보호 합니다. 다시 시작 작업은 백업 정책을 연결 하 여 선택한 보존 기간을 사용 하 여 예약 된 백업을 트리거합니다. 또한 가비지 수집기는 다시 시작 작업이 완료 되는 즉시 실행 됩니다. 만료 날짜를 지난 복구 지점에서 복원을 수행 하려는 경우 다시 시작 작업을 트리거하기 전에 수행 하는 것이 좋습니다.

#### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>자격 증명 모음에 일시 삭제 된 항목이 있는 경우 자격 증명 모음을 삭제할 수 있나요?

자격 증명 모음에 일시 삭제 된 상태의 백업 항목이 있는 경우에는 Recovery Services 자격 증명 모음을 삭제할 수 없습니다. 일시 삭제 된 항목은 삭제 작업 후 14 일이 지나면 영구적으로 삭제 됩니다. 14 일 동안 기다릴 수 없는 경우 [일시 삭제를 사용 하지 않도록 설정](#disabling-soft-delete)하 고 일시 삭제 된 항목의 삭제를 취소 한 다음 삭제 된 항목을 다시 삭제 하 여 영구적으로 삭제 합니다. 보호 된 항목이 없고 일시 삭제 된 항목이 없는 경우 자격 증명 모음을 삭제할 수 있습니다.  

#### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>삭제 후 14 일 일시 삭제 기간 보다 이전 데이터를 삭제할 수 있나요?

아닙니다. 일시 삭제 된 항목은 강제로 삭제할 수 없으며 14 일이 지나면 자동으로 삭제 됩니다. 이 보안 기능은 백업 된 데이터를 실수로 또는 악의적으로 삭제 하지 않도록 보호 하기 위해 사용 됩니다.  VM에서 다른 작업을 수행 하기 전에 14 일 동안 기다려야 합니다.  일시 삭제 된 항목은 요금이 청구 되지 않습니다.  일시 삭제 하도록 표시 된 Vm을 14 일 이내에 새 자격 증명 모음으로 다시 보호 해야 하는 경우 Microsoft 지원에 문의 하세요.

#### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell 또는 CLI에서 일시 삭제 작업을 수행할 수 있나요?

일시 삭제 작업은 [Powershell](#soft-delete-for-vms-using-azure-powershell)을 사용 하 여 수행할 수 있습니다. 현재 CLI는 지원 되지 않습니다.

#### <a name="is-soft-delete-supported-for-other-cloud-workloads-like-sql-server-in-azure-vms-and-sap-hana-in-azure-vms"></a>Azure vm의 SQL Server 및 Azure Vm의 SAP HANA 같은 다른 클라우드 워크 로드에 대해 일시 삭제가 지원 되나요?

아닙니다. 현재 일시 삭제는 Azure virtual machines에 대해서만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Backup에 대한 보안 제어를](backup-security-controls.md)참조 하세요.
