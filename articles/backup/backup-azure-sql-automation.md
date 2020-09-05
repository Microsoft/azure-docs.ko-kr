---
title: PowerShell을 통해 Azure VM backup의 SQL DB & 복원
description: Azure Backup 및 PowerShell을 사용 하 여 Azure Vm에서 SQL 데이터베이스를 백업 및 복원 합니다.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: b355aaa465132e86c636c68552f3d650b51b08f1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004989"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>PowerShell을 사용 하 여 Azure Vm에서 SQL 데이터베이스 백업 및 복원

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 사용 하 여 Azure VM 내에서 SQL DB를 백업 하 고 복구 하는 방법을 설명 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정 하 고 Azure Recovery Services 공급자를 등록 합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure VM 내에서 SQL DB에 대 한 백업을 구성 합니다.
> * 백업 작업을 실행 합니다.
> * 백업 된 SQL DB를 복원 합니다.
> * 백업 및 복원 작업을 모니터링 합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md) .
* [Azure vm 내에서 SQL db를 백업](backup-azure-sql-database.md#before-you-start)하기 위한 기능 기능에 대해 알아보세요.
* Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az. RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) 참조를 검토 합니다.

### <a name="set-up-and-install"></a>설정 및 설치

PowerShell을 다음과 같이 설정 합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.5.0입니다.

2. 다음 명령을 사용 하 여 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup 및 Recovery Services 자격 증명 모음에 대 한 별칭 및 cmdlet을 검토 합니다. 다음은 표시 될 수 있는 항목의 예입니다. Cmdlet의 전체 목록은 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **AzAccount**를 사용 하 여 Azure 계정에 로그인 합니다.
5. 표시 되는 웹 페이지에 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

    * 또는 **자격**증명을 사용 하 여 **AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테 넌 트에 대해 작동 하는 CSP 파트너 인 경우 해당 tenantID 또는 테 넌 트 주 도메인 이름을 사용 하 여 고객을 테 넌 트로 지정 합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

6. 계정에 여러 구독이 있을 수 있으므로 사용 하려는 구독을 계정과 연결 합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. 처음으로 Azure Backup을 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Service 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록 되었는지 확인 합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 **Registrationstate** 가 **등록**됨으로 변경 되었는지 확인 합니다. 그렇지 않은 경우 **AzResourceProvider** cmdlet을 다시 실행 합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

1. 자격 증명 모음은 리소스 그룹에 배치 됩니다. 기존 리소스 그룹이 없는 경우 [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)를 사용 하 여 새 리소스 그룹을 만듭니다. 이 예제에서는 미국 서 부 지역에 새 리소스 그룹을 만듭니다.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. [AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet을 사용 하 여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 자격 증명 모음 저장소에 사용할 중복성 유형을 지정 합니다.

    * [로컬 중복 스토리지](../storage/common/storage-redundancy.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy.md)를 사용할 수 있습니다.
    * 다음 예에서는 **GeoRedundant**로 설정 된 **testvault 된** 에 대 한 **BackupStorageRedundancy** 옵션을[AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd로 설정 합니다.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사 합니다. 연결 된 리소스 그룹 및 위치가 제공 됩니다.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

자격 증명 모음 개체를 변수에 저장 하 고 자격 증명 모음 컨텍스트를 설정 합니다.

* 많은 Azure Backup cmdlet에는 입력으로 Recovery Services 자격 증명 모음 개체가 필요 하므로 자격 증명 모음 개체를 변수에 저장 하는 것이 편리 합니다.
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)를 사용 하 여 설정 합니다. 컨텍스트를 설정한 후에는 모든 후속 cmdlet에 적용 됩니다.

다음 예제에서는 **testvault**에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 가져옵니다.

Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 사용 중단 계획 합니다. 대신, 자격 증명 모음 ID를 저장 하거나 인출 하 고 다음과 같이 관련 명령에 전달할 수 있습니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정 및 백업 복구 지점이 유지 되는 기간을 지정 합니다.

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* [AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)를 사용 하 여 기본 백업 정책 보존을 확인 합니다.
* [AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)를 사용 하 여 기본 백업 정책 일정을 확인 합니다.
* [AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) cmdlet을 사용 하 여 새 백업 정책을 만듭니다. 일정 및 보존 정책 개체를 입력 합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 되어 있습니다. 다음 예를 사용 하 여 시작 시간을 원하는 시작 시간으로 변경할 수 있습니다. 원하는 시작 시간은 UTC로도 지정 해야 합니다. 다음 예에서는 원하는 시작 시간이 매일 백업의 경우 01:00 AM UTC 라고 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30 분의 배수로 시작 시간을 제공 해야 합니다. 위의 예제에서 "01:00:00" 또는 "02:30:00"만 가능 합니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수를 새 정책에 대 한 매개 변수로 사용 합니다 (**Newsqlpolicy**). **Newsqlpolicy** 는 매일 "전체" 백업을 수행 하며, 180 일 동안 유지 하 고 2 시간 마다 로그 백업을 수행 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 유사 합니다.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>백업 사용

### <a name="registering-the-sql-vm"></a>SQL VM 등록

Azure VM 백업 및 Azure 파일 공유의 경우 Backup 서비스는 이러한 Azure Resource Manager 리소스에 연결 하 여 관련 세부 정보를 가져올 수 있습니다. SQL은 Azure VM 내의 응용 프로그램 이므로 Backup 서비스에는 응용 프로그램에 액세스 하 고 필요한 세부 정보를 가져올 수 있는 권한이 필요 합니다. 이를 위해 Recovery Services 자격 증명 모음을 사용 하 여 SQL 응용 프로그램이 포함 된 Azure VM을 *' 등록 '* 해야 합니다. 자격 증명 모음을 사용 하 여 SQL VM을 등록 한 후에는 SQL Db를 해당 자격 증명 모음 으로만 보호할 수 있습니다. [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer) PowerShell cmdlet을 사용 하 여 VM을 등록 합니다.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

이 명령은이 리소스의 ' 백업 컨테이너 '를 반환 하 고 상태는 ' 등록 됨 '으로 나타납니다.

> [!NOTE]
> Force 매개 변수를 지정 하지 않으면 '이 컨테이너에 대 한 보호를 사용 하지 않도록 설정 하 시겠습니까 ' 텍스트를 확인 하는 메시지가 표시 됩니다. 이 텍스트는 무시 하 고 확인 하려면 "Y"를 입력 하세요. 이것은 알려진 문제 이며, force 매개 변수에 대 한 텍스트 및 요구 사항을 제거 하기 위해 작업 하 고 있습니다.

### <a name="fetching-sql-dbs"></a>SQL Db 페치

등록이 완료 되 면 Backup 서비스는 VM 내에서 사용 가능한 모든 SQL 구성 요소를 나열할 수 있습니다. 이 자격 증명 모음에 백업 해야 하는 모든 SQL 구성 요소를 보려면 [AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용 하세요.

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

출력에는 항목 유형과 ServerName을 사용 하 여이 자격 증명 모음에 등록 된 모든 SQL Vm의 보호 되지 않은 모든 SQL 구성 요소가 표시 됩니다. '-Container ' 매개 변수를 전달 하 여 특정 SQL VM을 추가로 필터링 하거나 ' Name ' 및 ' ServerName ' 조합을 사용 하 여 ItemType 플래그와 함께 고유한 SQL 항목에 도달할 수 있습니다.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>백업 구성

필요한 SQL DB와이를 백업 해야 하는 정책을 만들었으므로 [AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) cmdlet을 사용 하 여이 sql db에 대 한 백업을 구성할 수 있습니다.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

명령은 구성 백업이 완료 될 때까지 대기 하 고 다음 출력을 반환 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>새 SQL Db를 가져오는 중

컴퓨터가 등록 되 면 Backup 서비스는 사용할 수 있는 Db의 세부 정보를 가져옵니다. 나중에 SQL Db 또는 SQL 인스턴스를 등록 된 컴퓨터에 추가 하는 경우 새 ' 조회 '를 수행 하 여 보호 되지 않는 **모든** db (새로 추가 된 db 포함)를 가져오도록 백업 서비스를 수동으로 트리거해야 합니다. SQL VM에서 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/initialize-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용 하 여 새 조회를 수행 합니다. 명령은 작업이 완료 될 때까지 대기 합니다. 나중에 [AzRecoveryServicesBackupProtectableItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectableitem) PowerShell cmdlet을 사용 하 여 보호 되지 않는 최신 SQL 구성 요소 목록을 가져옵니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

관련 된 보호 가능한 항목을 인출 한 후에는 [위의 섹션](#configuring-backup)에 설명 된 대로 백업을 사용 하도록 설정 합니다.
새 Db를 수동으로 검색 하지 않으려는 경우 [아래](#enable-autoprotection)설명 된 대로 자동 보호를 선택할 수 있습니다.

## <a name="enable-autoprotection"></a>Start-autoprotection 사용

나중에 추가 된 모든 Db가 특정 정책을 사용 하 여 자동으로 보호 되도록 백업을 구성할 수 있습니다. Autoprotection을 사용 하도록 설정 하려면 [AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet을 사용 합니다.

이 명령은 이후의 모든 Db를 백업 하는 것 이므로 SQLInstance 수준에서 작업이 수행 됩니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Start-autoprotection 의도가 제공 되 면 새로 추가 된 Db를 가져오기 위해 컴퓨터로 조회 하는 작업이 8 시간 마다 예약 된 백그라운드 작업으로 수행 됩니다.

## <a name="restore-sql-dbs"></a>SQL Db 복원

다음과 같이 Azure Vm에서 실행 되는 SQL Server 데이터베이스를 복원할 수 Azure Backup.

* 트랜잭션 로그 백업을 사용 하 여 특정 날짜 또는 시간 (초)으로 복원 합니다. Azure Backup은 선택 된 시간에 따라 복원 하는 데 필요한 적절 한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정 합니다.
* 특정 복구 지점으로 복원 하기 위해 특정 전체 또는 차등 백업을 복원 합니다.

SQL Db를 복원 하기 전에 [여기](restore-sql-database-azure-vm.md#prerequisites) 에 언급 된 전제 조건을 확인 하세요.

먼저 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용 하 여 관련 백업 된 SQL DB를 인출 합니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>관련 복원 시간 페치

위에서 설명한 대로 백업 된 SQL DB를 전체/차등 복사 **또는** 로그 지정 시간으로 복원할 수 있습니다.

#### <a name="fetch-distinct-recovery-points"></a>고유 복구 지점의 페치

AzRecoveryServicesBackupRecoveryPoint를 사용 하 여 백업 된 SQL DB에 대 한 고유 (전체/차등) 복구 지점만 [가져옵니다](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) .

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

출력은 다음 예제와 유사 합니다.

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

' RecoveryPointId ' 필터 또는 배열 필터를 사용 하 여 관련 복구 지점을 페치합니다.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>특정 시점 복구 지점 가져오기

DB를 특정 시점으로 복원 하려면 [AzRecoveryServicesBackupRecoveryLogChain](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverylogchain) PowerShell cmdlet을 사용 합니다. Cmdlet은 해당 SQL 백업 항목에 대해 끊어지지 않은 연속 로그 체인의 시작 및 종료 시간을 나타내는 날짜 목록을 반환 합니다. 원하는 지정 시간은이 범위 내에 있어야 합니다.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

출력은 다음 예제와 유사 합니다.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

위의 출력은 표시 된 시작 시간과 종료 시간 사이의 특정 시점으로 복원할 수 있음을 의미 합니다. 시간은 UTC 단위입니다. PowerShell에서 위에 표시 된 범위 내에 있는 특정 시점을 구성 합니다.

> [!NOTE]
> 복원에 대 한 로그 지정 시간을 선택한 경우 DB가 복원 되는 전체 백업 또는 시작 지점을 지정할 필요가 없습니다. Azure Backup 서비스는 전체 복구 계획, 즉 선택할 전체 백업, 적용할 로그 백업 등을 처리 합니다.

### <a name="determine-recovery-configuration"></a>복구 구성 확인

SQL DB 복원의 경우 다음 복원 시나리오가 지원 됩니다.

* 백업 된 SQL DB를 다른 복구 지점의 데이터로 재정의-OriginalWorkloadRestore
* 동일한 SQL 인스턴스에서 새 DB로 SQL DB 복원-AlternateWorkloadRestore
* 다른 sql VM의 다른 SQL 인스턴스에서 새 DB로 SQL DB 복원-AlternateWorkloadRestore
* SQL DB를 .bak 파일로 복원-RestoreAsFiles

관련 복구 지점 (고유 또는 로그 지정 시간)을 인출 한 후 [AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet을 사용 하 여 원하는 복구 계획에 따라 복구 구성 개체를 가져옵니다.

#### <a name="original-workload-restore"></a>원래 워크 로드 복원

복구 지점의 데이터로 백업 된 DB를 재정의 하려면 다음 예와 같이 올바른 플래그와 관련 복구 지점만 지정 하면 됩니다.

##### <a name="original-restore-with-distinct-recovery-point"></a>고유 복구 지점을 사용 하 여 원래 복원

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>로그 지정 시간으로 원래 복원

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>대체 작업 복원

> [!IMPORTANT]
> 백업 된 SQL DB는이 자격 증명 모음에 대 한 Azure VM ' 등록 '에 있는 다른 SQLInstance에만 새 DB로 복원할 수 있습니다.

위에서 설명한 것 처럼 대상 SQLInstance 다른 Azure VM에 있는 경우 [이 자격 증명 모음에 등록](#registering-the-sql-vm) 되어 있는지 확인 하 고 관련 SQLInstance 보호 가능한 항목으로 나타납니다.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

그런 다음 아래와 같이 관련 복구 지점을 전달 하 고 올바른 플래그를 사용 하 여 대상 SQL 인스턴스를 전달 하기만 하면 됩니다.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>서로 다른 복구 지점을 사용 하 여 대체 복원

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>로그 지정 시간으로 대체 복원

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>파일로 복원

데이터베이스 대신 .bak 파일로 백업 데이터를 복원 하려면 **파일로 복원** 옵션을 선택 합니다. 백업 된 SQL DB는이 자격 증명 모음에 등록 된 모든 대상 VM으로 복원 될 수 있습니다.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>고유 복구 지점을 사용 하 여 파일로 복원

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>최신 전체에서 로그 지정 시간이 포함 된 파일로 복원

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>지정 된 전체에서 로그 지정 시간이 포함 된 파일로 복원

복원에 사용할 특정 전체를 지정 하려면 다음 명령을 사용 합니다.

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

[AzRecoveryServicesBackupWorkloadRecoveryConfig](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupworkloadrecoveryconfig) PowerShell cmdlet에서 가져온 최종 복구 지점 구성 개체는 다음과 같이 복원에 대 한 모든 관련 정보를 포함 합니다.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

복원 된 DB 이름, OverwriteWLIfpresent, targetPhysicalPath 필드를 편집할 수 있습니다. 아래 표시 된 것 처럼 대상 파일 경로에 대 한 자세한 내용을 확인 하세요.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

아래와 같이 관련 PowerShell 속성을 문자열 값으로 설정 합니다.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> 복원 작업이 구성 개체를 기반으로 하기 때문에 최종 복구 구성 개체에 필요한 모든 값 및 적절 한 값이 있는지 확인 합니다.

### <a name="restore-with-relevant-configuration"></a>관련 구성을 사용 하 여 복원

관련 복구 구성 개체를 가져오고 확인 한 후에는 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용 하 여 복원 프로세스를 시작 합니다.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

복원 작업은 추적할 작업을 반환 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL 백업 관리

### <a name="on-demand-backup"></a>주문형 백업

DB에 대해 백업을 사용 하도록 설정한 후에는 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) PowerShell cmdlet을 사용 하 여 db에 대 한 요청 시 백업을 트리거할 수도 있습니다. 다음 예에서는 압축을 사용 하 여 SQL DB에서 전체 백업을 트리거하고 전체 백업은 60 일 동안 보존 되어야 합니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

주문형 백업 명령은 추적할 작업을 반환 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

출력이 손실 되거나 관련 작업 ID를 가져오려는 경우 Azure Backup 서비스에서 [작업 목록을 가져온](#track-azure-backup-jobs) 다음 해당 작업의 세부 정보를 추적 합니다.

### <a name="change-policy-for-backup-items"></a>백업 항목에 대 한 정책 변경

백업 된 항목의 정책을 *Policy1* 에서 *Policy2*로 변경할 수 있습니다. 백업 항목에 대 한 정책을 전환 하려면 관련 정책을 페치하고 항목을 백업 하 고 백업 항목과 함께 [AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) 명령을 매개 변수로 사용 합니다.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

명령은 구성 백업이 완료 될 때까지 대기 하 고 다음 출력을 반환 합니다.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="edit-an-existing-backup-policy"></a>기존 백업 정책 편집

기존 정책을 편집 하려면 [AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) 명령을 사용 합니다.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -SchedulePolicy $SchPol -RetentionPolicy $RetPol
```

모든 오류를 추적 하기 위해 시간이 지난 후 백업 작업을 확인 합니다. 문제가 있는 경우 문제를 해결 해야 합니다. 그런 다음 **FixForInconsistentItems** 매개 변수를 사용 하 여 정책 편집 명령을 다시 실행 하 여 작업이 이전에 실패 한 모든 백업 항목에 대 한 정책 편집을 다시 시도 합니다.

```powershell
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $Pol -FixForInconsistentItems
```

### <a name="re-register-sql-vms"></a>SQL Vm 다시 등록

> [!WARNING]
> 다시 등록을 시도 하기 전에 오류 증상과 원인을 이해 하려면이 [문서](backup-sql-server-azure-troubleshoot.md#re-registration-failures) 를 참조 하세요.

SQL VM의 재등록을 트리거하려면 관련 된 백업 컨테이너를 가져와 register cmdlet에 전달 합니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>보호 중지

#### <a name="retain-data"></a>데이터 보존

보호를 중지 하려는 경우 [AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet을 사용할 수 있습니다. 이렇게 하면 예약 된 백업이 중지 되지만 지금까지 백업 된 데이터는 영구적으로 유지 됩니다.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>백업 데이터 삭제

자격 증명 모음에 저장 된 백업 데이터를 완전히 제거 하려면 '-RemoveRecoveryPoints ' 플래그/스위치를 [' 사용 안 함 ' 보호 명령](#retain-data)에 추가 합니다.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>자동 보호 사용 안 함

Start-autoprotection이 SQLInstance에 구성 된 경우 [AzRecoveryServicesBackupAutoProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection) PowerShell cmdlet을 사용 하 여 사용 하지 않도록 설정할 수 있습니다.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL VM 등록 취소

SQL server의 모든 Db [가 더 이상 보호 되지 않고 백업 데이터가 없으면](#delete-backup-data)이 자격 증명 모음에서 sql VM의 등록을 취소할 수 있습니다. 그 다음에만 Db를 다른 자격 증명 모음으로 보호할 수 있습니다. [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer) PowerShell cmdlet을 사용 하 여 SQL VM의 등록을 취소 합니다.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Azure Backup 작업 추적

Azure Backup는 SQL 백업에서 사용자 트리거 작업만 추적 하는 것이 중요 합니다. 예약 된 백업 (로그 백업 포함)은 포털 또는 PowerShell에 표시 되지 않습니다. 그러나 예약 된 작업이 실패 하는 경우 [백업 경고가](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) 생성 되 고 포털에 표시 됩니다. [Azure Monitor를 사용](backup-azure-monitoring-use-azuremonitor.md) 하 여 모든 예약 된 작업 및 기타 관련 정보를 추적할 수 있습니다.

사용자는 백업과 같은 비동기 작업의 [출력](#on-demand-backup) 에서 반환 된 JobID를 사용 하 여 요청 시/사용자 트리거 작업을 추적할 수 있습니다. [AzRecoveryServicesBackupJobDetail](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjobdetail) PowerShell cmdlet을 사용 하 여 작업 및 해당 세부 정보를 추적할 수 있습니다.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Azure Backup 서비스에서 주문형 작업 및 해당 상태 목록을 가져오려면 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) PowerShell cmdlet을 사용 합니다. 다음 예에서는 진행 중인 모든 SQL 작업을 반환 합니다.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

진행 중인 작업을 취소 하려면 [AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) PowerShell cmdlet을 사용 합니다.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always On 가용성 그룹 관리

SQL Always On 가용성 그룹의 경우 AG (가용성 그룹)의 [모든 노드를 등록](#registering-the-sql-vm) 해야 합니다. 모든 노드에 대 한 등록이 완료 되 면 보호 가능한 항목 아래에 SQL 가용성 그룹 개체가 논리적으로 만들어집니다. SQL AG 아래의 데이터베이스가 ' Backup-sqldatabase '로 나열 됩니다. 노드는 독립 실행형 인스턴스로 표시 되 고 기본 SQL 데이터베이스는 SQL database로도 나열 됩니다.

예를 들어 sql AG에는 두 개의 노드가 있습니다. 즉, sql *server-0* 과 *sql server-1* 및 1 sql ag DB가 있다고 가정해 보겠습니다. 두 노드를 모두 등록 한 후에는 [보호 가능한 항목](#fetching-sql-dbs)을 나열 하는 경우 다음 구성 요소를 나열 합니다.

* SQL AG 개체-보호 가능한 항목 유형 (SQLAvailabilityGroup)
* SQL AG DB 보호 가능한 항목 유형 (Backup-sqldatabase)
* SQLInstance으로 보호 가능한 항목 유형 (sql server-0)
* SQLInstance로 보호 되는 항목 유형 (sql server-1)
* Backup-sqldatabase에서 보호 가능한 항목 종류의 모든 기본 SQL Db (master, model, msdb)
* Sql server-1의 모든 기본 SQL Db (master, model, msdb)-Backup-sqldatabase으로 보호 가능한 항목 유형

[백업 컨테이너가 나열 되](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)면 sql server-0, sql-server-1도 "AzureVMAppContainer"로 표시 됩니다.

관련 데이터베이스를 인출 하 여 [백업을 사용 하도록 설정](#configuring-backup) 하 고 [요청 시 백업](#on-demand-backup) 및 [복원 PowerShell cmdlet](#restore-sql-dbs) 이 동일 합니다.
