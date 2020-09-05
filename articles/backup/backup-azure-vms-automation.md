---
title: PowerShell을 사용 하 여 Azure Vm 백업 및 복구
description: PowerShell과 함께 Azure Backup를 사용 하 여 Azure Vm을 백업 및 복구 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: e4564ba2b6109296a7383fb4b056c2f4b1890fda
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89178134"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>PowerShell을 사용 하 여 Azure Vm 백업 및 복원

이 문서에서는 PowerShell cmdlet을 사용 하 여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음에서 Azure VM을 백업 및 복원 하는 방법을 설명 합니다.

이 문서에서는 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Recovery Services 자격 증명 모음을 만들고 자격 증명 모음 컨텍스트를 설정합니다.
> * 백업 정책 정의
> * 여러 가상 머신을 보호하기 위해 백업 정책 적용
> * 보호된 가상 머신에 대한 주문형 백업 작업 트리거. 가상 머신을 백업하거나 보호하려면 먼저 [필수 조건](backup-azure-arm-vms-prepare.md)을 완료하여 VM을 보호하기 위한 환경을 준비해야 합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md) .
* Azure VM 백업에 대 한 아키텍처를 [검토](backup-architecture.md#architecture-built-in-azure-vm-backup) 하 고, 백업 프로세스 [에 대해 알아보고](backup-azure-vms-introduction.md) , 지원, 제한 사항 및 필수 구성 요소를 [검토](backup-support-matrix-iaas.md) 합니다.
* Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az. RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices/) 참조를 검토 합니다.

## <a name="set-up-and-register"></a>설정 및 등록

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

시작하려면

1. [최신 버전의 PowerShell 다운로드](/powershell/azure/install-az-ps)

2. 다음 명령을 입력하여 사용할 수 있는 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Azure Backup, Azure Site Recovery 및 Recovery Services 자격 증명 모음의 별칭과 cmdlet이 나타납니다. 다음 이미지는 표시되는 예이며 Cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services 목록](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. **Connect-AzAccount**를 사용하여 Azure 계정에 로그인합니다. 이 cmdlet은 계정 자격 증명을 묻는 웹 페이지를 엽니다.

    * 또는 **Connect-AzAccount** cmdlet에서 **-Credential** 매개 변수를 사용하여 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테 넌 트를 대신 하 여 작업 중인 CSP 파트너인 경우 tenantID 또는 테 넌 트 주 도메인 이름을 사용 하 여 고객을 테 넌 트로 지정 합니다. 예: **AzAccount-Tenant "fabrikam.com"**

4. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup를 처음으로 사용 하는 경우 **[AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** cmdlet을 사용 하 여 Azure 복구 서비스 공급자를 구독에 등록 해야 합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. 다음 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인할 수 있습니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    명령 출력에서 **RegistrationState**는 **등록됨**으로 변경해야 합니다. 그렇지 않은 경우 **[AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** cmdlet을 다시 실행 하면 됩니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계는 Recovery Services 자격 증명 모음을 만드는 과정을 안내합니다. Recovery Services 자격 증명 모음은 Backup 자격 증명 모음과 다릅니다.

1. Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용 하거나 **[AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** cmdlet을 사용 하 여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet을 사용하여 Recovery Services 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정해야 합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 사용할 스토리지 중복 유형을 지정합니다. [LRS (로컬 중복 저장소)](../storage/common/storage-redundancy.md) 또는 [GRS (지역 중복 저장소](../storage/common/storage-redundancy.md))를 사용할 수 있습니다. 다음 예제는 testvault에 대한 -BackupStorageRedundancy 옵션이 GeoRedundant로 설정된 것을 보여 줍니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > 많은 Azure Backup cmdlet에는 Recovery Services 자격 증명 모음 개체가 입력으로 필요합니다. 이러한 이유로 백업 Recovery Services 자격 증명 모음 개체를 변수에 저장 하는 것이 편리 합니다.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)를 사용 합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음 예제와 유사하며, 연관된 ResourceGroupName 및 위치가 제공됩니다.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Azure VM 백업

Recovery Services 자격 증명 모음을 사용하여 가상 머신을 보호합니다. 보호를 적용하기 전에 자격 증명 모음 컨텍스트(자격 증명 모음에 보호된 데이터 형식)를 설정하고 보호 정책을 확인합니다. 보호 정책은 백업 작업을 실행하는 시간과 각 백업 스냅샷을 유지하는 기간에 대한 일정입니다.

### <a name="set-vault-context"></a>자격 증명 모음 컨텍스트 설정

VM에 대 한 보호를 사용 하도록 설정 하기 전에 [AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) 를 사용 하 여 자격 증명 모음 컨텍스트를 설정 합니다. 자격 증명 모음 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다. 다음 예제에서는 자격 증명 모음, *testvault*에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 가져옵니다.

Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 사용 중단 계획 합니다. 대신 자격 증명 모음 ID를 저장 하거나 인출 하 고 관련 명령에 전달할 수 있습니다. 따라서 자격 증명 모음 컨텍스트를 설정 하지 않았거나 특정 자격 증명 모음에 대해 실행할 명령을 지정 하려는 경우 다음과 같이 자격 증명 모음 ID를 모든 관련 명령에 "-vaultID"으로 전달 합니다.

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

또는

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>저장소 복제 설정 수정

[AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) 명령을 사용 하 여 자격 증명 모음에 대 한 저장소 복제 구성을 LRS/GRS로 설정 합니다.

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> 자격 증명 모음에 보호된 백업 항목이 없는 경우에만 스토리지 중복을 수정할 수 있습니다.

### <a name="create-a-protection-policy"></a>보호 정책 만들기

Recovery Services 자격 증명 모음을 만들면 기본 보호 및 보존 정책이 함께 제공됩니다. 기본 보호 정책은 매일 지정된 시간에 백업 작업을 트리거합니다. 기본 보존 정책은 매일 복구 지점을 30일 동안 유지합니다. 기본 정책을 사용하여 VM을 신속하게 보호하고, 나중에 다른 세부 정보로 정책을 편집할 수 있습니다.

**[AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** 를 사용 하 여 자격 증명 모음에서 사용할 수 있는 보호 정책을 볼 수 있습니다. 특정 정책을 받거나 워크로드 유형과 연결된 정책을 보려면 이 cmdlet을 사용할 수 있습니다. 다음 예제에서는 워크로드 유형, AzureVM에 대한 정책을 가져옵니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

다음 예제와 유사하게 출력됩니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell에서 BackupTime 필드의 표준 시간대는 UTC입니다. 하지만, 백업 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

백업 보호 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.

* 기본 보존 정책을 보려면 [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)를 사용합니다.
* 마찬가지로 [AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) 를 사용 하 여 기본 일정 정책을 가져올 수 있습니다.
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet은 백업 정책 정보를 보유하는 PowerShell 개체를 만듭니다.
* 일정 및 보존 정책 개체는 New-AzRecoveryServicesBackupProtectionPolicy cmdlet에 대해 입력으로 사용됩니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 되어 있습니다. 다음 예를 사용 하 여 시작 시간을 원하는 시작 시간으로 변경할 수 있습니다. 원하는 시작 시간은 UTC로도 지정 해야 합니다. 다음 예에서는 원하는 시작 시간이 매일 백업의 경우 01:00 AM UTC 라고 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30 분의 배수로 시작 시간을 제공 해야 합니다. 위의 예제에서는 "01:00:00" 또는 "02:30:00"만 가능 합니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 예제에서는 이러한 변수를 사용하여 보호 정책, *NewPolicy*를 만들 때 매개 변수를 정의합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

다음 예제와 유사하게 출력됩니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>보호 사용

보호 정책을 정의한 후에는 항목에 대한 정책을 계속 사용해야 합니다. [AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 를 사용 하 여 보호를 사용 하도록 설정 합니다. 보호를 사용하도록 설정하는 데에는 항목 및 정책이라는 두 가지 개체가 필요합니다. 정책이 자격 증명 모음과 연결되고 나면, 백업 워크플로가 정책 일정에 정의된 시간에 트리거됩니다.

> [!IMPORTANT]
> PowerShell을 사용 하 여 한 번에 여러 Vm에 대 한 백업을 사용 하도록 설정 하는 동안 단일 정책에 연결 된 Vm이 100 이상 인지 확인 합니다. 이는 [권장 모범 사례](./backup-azure-vm-backup-faq.md#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy)입니다. VM이 100개를 넘는 경우 지금은 PowerShell 클라이언트가 명시적으로 차단하고 있진 않지만 향후 확인 절차를 추가하고자 합니다.

다음 예제에서는 NewPolicy 정책을 사용하여 V2VM 항목에 대해 보호를 활성화합니다. 예제는 VM이 암호화되었는지 여부와 암호화 유형에 따라 다릅니다.

**암호화 되지 않은 리소스 관리자 vm**에 대 한 보호를 사용 하도록 설정 하려면:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

암호화된 VM(BEK 및 KEK를 사용하여 암호화된)에 대한 보호를 사용하도록 설정하려면 Azure Backup 서비스에 주요 자격 증명 모음에서 키 및 암호를 읽을 수 있는 권한을 제공해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

**암호화된 VM(BEK만 사용하여 암호화된)** 에 대한 보호를 사용하도록 설정하려면 Azure Backup 서비스에 주요 자격 증명 모음에서 암호를 읽을 수 있는 권한을 제공해야 합니다.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Azure Government 클라우드를 사용 하는 경우 `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet의 **ServicePrincipalName** 매개 변수 값을 사용 합니다.
>

## <a name="monitoring-a-backup-job"></a>백업 작업 모니터링

Azure Portal을 사용하지 않고 백업 작업과 같은 장기 실행 작업을 모니터링할 수 있습니다. 진행 중인 작업의 상태를 가져오려면 [AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet을 사용 합니다. 이 cmdlet은 특정 자격 증명 모음에 대한 백업 작업을 가져오고 해당 자격 증명 모음은 자격 증명 모음 컨텍스트에 지정됩니다. 다음 예제에서는 배열과 같은 진행 중인 작업의 상태를 가져와 $joblist 변수에 상태를 저장합니다.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

다음 예제와 유사하게 출력됩니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

이러한 작업을 완료 하기 위해 폴링하는 대신 불필요 한 추가 코드 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 합니다. 이 cmdlet은 작업이 완료되거나 지정된 시간 제한 값에 도달할 때까지 실행을 일시 중지합니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Azure VM 백업 관리

### <a name="modify-a-protection-policy"></a>보호 정책 수정

보호 정책을 수정 하려면 [AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 를 사용 하 여 SchedulePolicy 또는 보존 정책 개체를 수정 합니다.

#### <a name="modifying-scheduled-time"></a>예약 된 시간 수정

보호 정책을 만들 때 기본적으로 시작 시간이 할당 됩니다. 다음 예에서는 보호 정책의 시작 시간을 수정 하는 방법을 보여 줍니다.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>보존 수정

다음 예제는 복구 지점 보존 기간을 365일로 변경합니다.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>즉시 복원 스냅숏 보존 구성

> [!NOTE]
> Azure PowerShell 버전 1.6.0부터 PowerShell을 사용 하 여 정책에서 즉시 복원 스냅숏 보존 기간을 업데이트할 수 있습니다.

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

기본값은 2입니다. 최소값은 1이 고 최대값은 5로 설정할 수 있습니다. 주간 백업 정책의 경우 기간은 5로 설정 되며 변경할 수 없습니다.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>스냅숏 보존 중 Azure Backup 리소스 그룹을 만드는 중

> [!NOTE]
> Azure PowerShell 버전 3.7.0 부터는 인스턴트 스냅숏을 저장 하기 위해 만든 리소스 그룹을 만들고 편집할 수 있습니다.

리소스 그룹 만들기 규칙 및 기타 관련 세부 정보에 대 한 자세한 내용은 [Virtual Machines에 대 한 Azure Backup 리소스 그룹](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) 설명서를 참조 하세요.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="trigger-a-backup"></a>백업 트리거

백업 [-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) 를 사용 하 여 백업 작업을 트리거합니다. 초기 백업인 경우 전체 백업입니다. 후속 백업에서는 증분 복사를 수행합니다. 다음 예제에서는 60 일 동안 보존 되는 VM 백업을 수행 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

다음 예제와 유사하게 출력됩니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell에서 표시되는 StartTime 및 EndTime 필드의 시간대는 UTC입니다. 하지만, 시간이 Azure 포털에 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.
>
>

### <a name="change-policy-for-backup-items"></a>백업 항목에 대 한 정책 변경

기존 정책을 수정 하거나 백업 된 항목의 정책을 Policy1에서 Policy2로 변경할 수 있습니다. 백업 항목에 대 한 정책을 전환 하려면 관련 정책을 페치하고 항목을 백업 하 고 백업 항목과 함께 [AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 명령을 매개 변수로 사용 합니다.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

명령은 구성 백업이 완료 될 때까지 대기 하 고 다음 출력을 반환 합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

보호를 중지 하려는 경우 [AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용할 수 있습니다. 이렇게 하면 예약 된 백업이 중지 되지만 지금까지 백업 된 데이터는 영구적으로 유지 됩니다.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>백업 데이터 삭제

자격 증명 모음에 저장 된 백업 데이터를 완전히 제거 하려면 '-RemoveRecoveryPoints ' 플래그/스위치를 [' 사용 안 함 ' 보호 명령](#retain-data)에 추가 합니다.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure VM 복원

Azure Portal를 사용 하 여 VM을 복원 하는 것과 PowerShell을 사용 하 여 VM을 복원 하는 것 간에는 중요 한 차이점이 있습니다. PowerShell을 사용하면 복구 지점에서 디스크 및 구성 정보가 만들어질 때 복원 작업이 완료됩니다. 복원 작업 중에 가상 머신은 만들어지지 않습니다. 디스크에서 가상 머신을 만들려면 [저장된 디스크에서 VM 만들기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) 섹션을 참조하세요. 전체 VM을 복원하지 않고 Azure VM 백업에서 몇 개의 파일을 복원하거나 복구하려면 [파일 복구](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup) 섹션을 참조하세요.

> [!Tip]
> 복원 작업 중에 가상 머신은 만들어지지 않습니다.
>
>

다음 그래픽에서는 RecoveryServicesVault에서 BackupRecoveryPoint까지로 내려가는 개체 계층 구조를 보여 줍니다.

![BackupContainer를 표시하는 Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

백업 데이터를 복원하려면 백업 항목 및 지정 시간 데이터가 포함된 복구 지점을 식별합니다. [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) 를 사용 하 여 자격 증명 모음에서 계정으로 데이터를 복원 합니다.

Azure VM을 복원하는 기본 단계는 다음과 같습니다.

* VM을 선택합니다.
* 복구 지점을 선택합니다.
* 디스크를 복원합니다.
* 저장된 디스크에서 VM을 만듭니다.

### <a name="select-the-vm-when-restoring-files"></a>VM (파일을 복원 하는 경우) 선택

올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택 하려면 [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet을 사용 하 고 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet으로 파이프 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>복구 지점 선택 (파일을 복원 하는 경우)

[Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모르겠으면 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택 하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 지난 7 일간의 선택 된 백업 항목에 대 한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

다음 예제와 유사하게 출력됩니다.

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>디스크 복원

[AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet을 사용 하 여 백업 항목의 데이터 및 구성을 복구 지점으로 복원 합니다. 복구 지점을 식별하고 나면 이 지점을 **-RecoveryPoint** 매개 변수의 값으로 사용합니다. 위의 샘플에서 **$rp [0]** 은 사용할 복구 지점입니다. 아래 샘플 코드에서는 **$rp[0]** 이 디스크를 복원하는 데 사용할 복구 지점이 됩니다.

디스크 및 구성 정보를 복원하려면

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Managed Disks 복원

> [!NOTE]
> 지원 되는 VM에 관리 디스크를 포함 하는 경우이를 관리 디스크로 복원 하려면 Azure PowerShell RM 모듈 v 6.7.0의 기능을 도입 했습니다. 부터.
>
>

추가 매개 변수를 **TargetResourceGroupName**을 제공하여 Managed Disks를 복원할 RG를 지정합니다.

> [!IMPORTANT]
> 관리 디스크를 복원 하는 데 **TargetResourceGroupName** 매개 변수를 사용 하는 것이 좋습니다. 이렇게 하면 성능이 크게 향상 됩니다. 이 매개 변수를 지정 하지 않으면 즉시 복원 기능을 사용할 수 없고 복원 작업의 속도가 더 느려집니다. 관리 디스크를 관리 되지 않는 디스크로 복원 해야 하는 경우에는이 매개 변수를 제공 하지 말고 매개 변수를 제공 하 여 의도를 명확 하 게 `-RestoreAsUnmanagedDisks` 합니다. `-RestoreAsUnmanagedDisks`매개 변수는 Azure PowerShell 3.7.0 이상에서 사용할 수 있습니다. 이후 버전에서는 올바른 복원 환경에 이러한 매개 변수 중 하나를 제공 해야 합니다.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

**VMConfig.JSON** 파일은 스토리지 계정으로 복원되고, Managed Disks는 지정한 대상 RG로 복원됩니다.

다음 예제와 유사하게 출력됩니다.

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

[AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 하 여 복원 작업이 완료 될 때까지 기다립니다.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

복원 작업이 완료 되 면 [AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet을 사용 하 여 복원 작업의 세부 정보를 가져옵니다. JobDetails 속성에는 VM을 다시 빌드하는 데 필요한 정보가 포함됩니다.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

디스크를 복원한 후 다음 섹션으로 이동하여 VM을 만듭니다.

## <a name="replace-disks-in-azure-vm"></a>Azure VM에서 디스크 교체

디스크 및 구성 정보를 교체 하려면 다음 단계를 수행 합니다.

* 1 단계: [디스크 복원](backup-azure-vms-automation.md#restore-the-disks)
* 2 단계: [PowerShell을 사용 하 여 데이터 디스크 분리](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* 3 단계: [PowerShell을 사용 하 여 WINDOWS VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>복원된 디스크에서 VM 만들기

디스크를 복원한 후 다음 단계를 사용하여 디스크에서 가상 머신을 만들고 구성합니다.

> [!NOTE]
>
> 1. AzureAz module 3.0.0 이상이 필요 합니다. <br>
> 2. 복원된 디스크에서 암호화된 VM을 만들려면 Azure 역할에 **Microsoft.KeyVault/vaults/deploy/action** 작업을 수행할 권한이 있어야 합니다. 역할에이 권한이 없는 경우이 작업을 사용 하 여 사용자 지정 역할을 만듭니다. 자세한 내용은 [Azure RBAC에서 사용자 지정 역할](../role-based-access-control/custom-roles.md)을 참조하세요. <br>
> 3. 디스크를 복원하면 이제 새 VM을 만드는 데 직접 사용할 수 있는 배포 템플릿을 가져올 수 있습니다. 암호화/암호화 되지 않은 관리/관리 되지 않는 Vm을 만드는 데 다른 PowerShell cmdlet이 필요 하지 않습니다.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>배포 템플릿을 사용 하 여 VM 만들기

결과 작업 세부 정보는 쿼리 및 배포 가능한 템플릿 URI를 제공합니다.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

템플릿은 고객의 스토리지 계정과 지정된 컨테이너에 있기 때문에 직접 액세스할 수 없습니다. 이 템플릿에 액세스하려면 (임시 SAS 토큰과 함께) 전체 URL이 필요합니다.

1. 먼저 템플릿 Bloburi에서 템플릿 이름을 추출 합니다. 형식은 아래에 설명 되어 있습니다. PowerShell에서 split 작업을 사용 하 여이 URL에서 최종 템플릿 이름을 추출할 수 있습니다.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. 그런 다음 [여기](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)에 설명 된 대로 전체 URL을 생성할 수 있습니다.

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. 템플릿을 배포 하 여 [여기](../azure-resource-manager/templates/deploy-powershell.md)에 설명 된 대로 새 VM을 만듭니다.

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
    ```

### <a name="create-a-vm-using-the-config-file"></a>구성 파일을 사용 하 여 VM 만들기

다음 섹션에서는 "VMConfig" 파일을 사용하여 VM을 만드는 데 필요한 단계를 나열합니다.

> [!NOTE]
> 위에 설명 된 배포 템플릿을 사용 하 여 VM을 만드는 것이 좋습니다. 이 섹션(포인트 1-6)은 곧 삭제될 예정입니다.

1. 복원된 디스크 속성에서 작업 세부 정보를 쿼리합니다.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Azure Storage 컨텍스트를 설정하고 JSON 구성 파일을 복원합니다.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. JSON 구성 파일을 사용하여 VM 구성을 만듭니다.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. OS 디스크 및 데이터 디스크를 연결합니다. 이 단계에서는 다양한 관리 및 암호화된 VM 구성에 대한 예제를 제공합니다. VM 구성에 맞는 예제를 사용하세요.

    * **관리되지 않고 암호화되지 않은 VM** - 관리되지 않고 암호화되지 않은 VM에는 다음 샘플을 사용합니다.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD를 포함하며 BEK만 사용하여 암호화된 비관리 VM** - Azure AD를 포함하며 BEK만 사용하여 암호화된 비관리 VM의 경우에는 키 자격 증명 모음에 비밀을 복원해야 디스크를 연결할 수 있습니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md)을 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다. OS 디스크를 설정할 때 관련 OS 형식을 언급해야 합니다.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 비관리 VM** - Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 비관리 VM의 경우에는 디스크를 연결하기 전에 키 자격 증명 모음에 키와 비밀을 복원합니다. 자세한 내용은 [Azure Backup 복구 지점에서 암호화된 가상 머신 복원](backup-azure-restore-key-secret.md)을 참조하세요. 다음 샘플은 암호화된 VM에 대해 OS 및 데이터 디스크를 연결하는 방법을 보여 줍니다.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 비관리 VM** - Azure AD를 포함하지 않으며 BEK만 사용하여 암호화된 비관리 VM의 경우 원본 **키 자격 증명 모음/비밀을 사용할 수 없으면**[Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 비밀을 복원합니다. 그런 후 다음 스크립트를 실행 하 여 복원 된 OS blob에 대 한 암호화 세부 정보를 설정 합니다 (데이터 blob에는이 단계가 필요 하지 않음). 복원된 키 자격 증명 모음에서 $dekurl을 가져올 수 있습니다.

    다음 스크립트는 원본 keyVault/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    OS Blob에서 암호화 세부 정보가 설정되고 **비밀이 사용 가능**해지면 아래에 나와 있는 스크립트를 사용하여 디스크를 연결합니다.

    원본 키 자격 증명 모음/비밀을 이미 사용할 수 있는 경우 위의 스크립트를 실행할 필요가 없습니다.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 비관리 VM** - Azure AD를 포함하지 않으며 BEK 및 KEK를 사용하여 암호화된 비관리 VM의 경우 원본 **키 자격 증명 모음/키/비밀을 사용할 수 없으면**[Azure Backup 복구 지점에서 암호화되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 수행하여 키 자격 증명 모음에 키와 비밀을 복원합니다. 그런 후 다음 스크립트를 실행 하 여 복원 된 OS blob에 대 한 암호화 세부 정보를 설정 합니다 (데이터 blob에는이 단계가 필요 하지 않음). 복원된 키 자격 증명 모음에서 $dekurl 및 $kekurl을 가져올 수 있습니다.

    아래 스크립트는 원본 키 자격 증명 모음/키/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    OS Blob에서 암호화 세부 정보가 설정되고 **키/비밀이 사용 가능**해지면 아래에 나와 있는 스크립트를 사용하여 디스크를 연결합니다.

    원본 키 자격 증명 모음/키/암호를 사용할 수 있는 경우 위의 스크립트를 실행할 필요가 없습니다.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **관리 및 암호화되지 않은 VM** - 암호하되지 않은 관리되는 VM의 경우 복원된 Managed Disks를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

    * **Azure AD를 포함하며 BEK만 사용하여 암호화된 관리형 VM** - Azure AD를 포함하며 BEK만 사용하여 암호화된 관리형 VM의 경우 복원된 관리형 디스크를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

    * **Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 관리형 VM** - Azure AD를 포함하며 BEK 및 KEK를 사용하여 암호화된 관리형 VM의 경우에는 복원된 관리 디스크를 연결합니다. 자세한 내용은 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

    * **AZURE ad가 없는 관리 되 고 암호화 된 vm (bek만 해당)** -azure ad가 없는 관리 되 고 암호화 된 VM (bek만 사용 하 여 암호화 됨)의 경우, 원본 키 **자격 증명 모음/암호를 사용할 수 없는** 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 key vault에 비밀을 복원 합니다. 그런 후 다음 스크립트를 실행 하 여 복원 된 OS 디스크에서 암호화 세부 정보를 설정 합니다 (데이터 디스크에는이 단계가 필요 하지 않음). 복원된 키 자격 증명 모음에서 $dekurl을 가져올 수 있습니다.

    아래 스크립트는 원본 keyVault/암호를 사용할 수 없는 경우에만 실행 해야 합니다.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    OS 디스크에서 암호화 세부 정보가 설정되고 비밀이 사용 가능해진 후에 복원된 관리 디스크를 연결하려면 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

    * **AZURE ad가 없는 관리 되 고 암호화 된 vm (bek 및 KEK)** -azure ad가 없는 관리 되 고 암호화 된 VM (bek & KEK를 사용 하 여 암호화 됨)의 경우 원본 키 **자격 증명 모음/키/암호를 사용할 수** 없는 경우 [Azure Backup 복구 지점에서 암호화 되지 않은 가상 머신 복원](backup-azure-restore-key-secret.md)의 절차를 사용 하 여 키 및 비밀을 key vault에 복원 그런 후 다음 스크립트를 실행 하 여 복원 된 OS 디스크에서 암호화 세부 정보를 설정 합니다 (데이터 디스크에는이 단계가 필요 하지 않음). 복원된 키 자격 증명 모음에서 $dekurl 및 $kekurl을 가져올 수 있습니다.

    다음 스크립트는 원본 키 자격 증명 모음/키/암호를 사용할 수 없는 경우에만 실행 해야 합니다.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    OS 디스크에서 암호화 세부 정보가 설정되고 키/비밀이 사용 가능해진 후에 복원된 관리 디스크를 연결하려면 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](../virtual-machines/windows/attach-disk-ps.md)을 참조하세요.

5. 네트워크 설정을 지정합니다.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. 가상 머신을 만듭니다.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. ADE 확장을 푸시합니다.
   ADE 확장이 푸시되 지 않으면 데이터 디스크가 암호화 되지 않은 것으로 표시 되므로 아래 단계를 실행 하는 것이 필수입니다.

   * **Azure AD가 포함된 VM** - 다음 명령을 사용하여 데이터 디스크에 대해 암호화를 수동으로 사용하도록 설정합니다.  

     **BEK만 사용**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Azure AD가 포함되지 않은 VM** - 다음 명령을 사용하여 데이터 디스크에 대해 암호화를 수동으로 사용하도록 설정합니다.

     명령 실행 중에 AADClientID를 요청 하는 경우에는 Azure PowerShell를 업데이트 해야 합니다.

     **BEK만 사용**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK 및 KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> 암호화 된 VM 복원 디스크 프로세스의 일부로 만든 JASON 파일을 수동으로 삭제 해야 합니다.

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM 백업에서 파일 복원

디스크를 복원하는 것 외에 Azure VM 백업에서 개별 파일을 복원할 수도 있습니다. 파일 복원 기능은 복구 지점의 모든 파일에 대한 액세스를 제공합니다. 일반 파일의 경우처럼 파일 탐색기를 통해 파일을 관리합니다.

Azure VM 백업에서 파일을 복원하는 기본 단계는 다음과 같습니다.

* VM 선택
* 복구 지점 선택
* 복구 지점의 디스크 탑재
* 필요한 파일 복사
* 디스크 분리

### <a name="select-the-vm-when-restoring-the-vm"></a>Vm을 선택 합니다 (VM을 복원 하는 경우).

올바른 백업 항목을 식별하는 PowerShell 개체를 가져오려면, 자격 증명 모음에 있는 컨테이너에서 시작하여, 개체 계층 구조를 따라 내려가는 방식으로 작업해야 합니다. VM을 나타내는 컨테이너를 선택 하려면 [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet을 사용 하 고 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet으로 파이프 합니다.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>복구 지점 선택 (VM을 복원 하는 경우)

[Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet을 사용하여 백업 항목에 대한 모든 복구 지점을 나열합니다. 그런 후 복원할 복구 지점을 선택합니다. 사용할 복구 지점을 잘 모르겠으면 목록에서 가장 최근의 RecoveryPointType = AppConsistent 지점을 선택 하는 것이 좋습니다.

다음 스크립트에서 변수 **$rp**는 지난 7 일간의 선택 된 백업 항목에 대 한 복구 지점의 배열입니다. 배열은 인덱스 0의 가장 최근 복구 지점부터 역 시간순으로 정렬됩니다. 복구 지점을 선택하려면 표준 PowerShell 배열 인덱싱을 사용합니다. 예에서, $rp[0]은 최신 복구 지점을 선택합니다.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

다음 예제와 유사하게 출력됩니다.

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>복구 지점의 디스크 탑재

[AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) cmdlet을 사용 하 여 복구 지점의 모든 디스크를 탑재 하는 스크립트를 가져옵니다.

> [!NOTE]
> 디스크는 스크립트가 실행되는 컴퓨터에 iSCSI 연결 디스크로 탑재됩니다. 탑재가 즉시 발생하며, 요금이 부과되지 않습니다.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

다음 예제와 유사하게 출력됩니다.

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

파일을 복구할 컴퓨터에서 스크립트를 실행합니다. 스크립트를 실행하려면 제공된 암호를 입력해야 합니다. 디스크가 연결된 후에 Windows 파일 탐색기를 사용하여 새 볼륨 및 파일을 검색합니다. 자세한 내용은 Backup 문서 [Azure virtual machine 백업에서 파일 복구](backup-azure-restore-files-from-vm.md)를 참조 하세요.

### <a name="unmount-the-disks"></a>디스크 분리

필요한 파일이 복사 된 후 [AzRecoveryServicesBackupRPMountScript](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) 를 사용 하 여 디스크를 분리 합니다. 복구 지점의 파일에 대한 액세스가 제거되도록 디스크를 분리해야 합니다.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 Azure 리소스와 연결하려는 경우 [Windows Server용 Backup 배포 및 관리](backup-client-automation.md) PowerShell 문서를 참조하세요. DPM 백업을 관리하는 경우 [DPM에 대한 Backup 배포 및 관리](backup-dpm-automation.md) 문서를 참조하세요.
