---
title: PowerShell을 사용 하 여 Azure Files 백업
description: 이 문서에서는 Azure Backup 서비스와 PowerShell을 사용 하 여 Azure Files를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120508"
---
# <a name="back-up-azure-files-with-powershell"></a>PowerShell을 사용 하 여 Azure Files 백업

이 문서에서는 Azure PowerShell를 사용 하 여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 사용 하 여 Azure Files 파일 공유를 백업 하는 방법을 설명 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정 하 고 Azure Recovery Services 공급자를 등록 합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure 파일 공유에 대 한 백업을 구성 합니다.
> * 백업 작업을 실행 합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요](backup-azure-recovery-services-vault-overview.md) .
* [Azure 파일 공유 백업](backup-afs.md)에 대 한 미리 보기 기능에 대해 알아보세요.
* Recovery Services에 대 한 PowerShell 개체 계층 구조를 검토 합니다.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services 개체 계층 구조

개체 계층 구조는 다음 다이어그램에 요약 되어 있습니다.

![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure 라이브러리에서 **Az. RecoveryServices** [cmdlet 참조](/powershell/module/az.recoveryservices) 참조를 검토 합니다.

## <a name="set-up-and-install"></a>설정 및 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 다음과 같이 설정 합니다.

1. [최신 버전의 Az PowerShell](/powershell/azure/install-az-ps)을 다운로드합니다. 필요한 최소 버전은 1.0.0입니다.

> [!WARNING]
> Preview에 필요한 최소 버전의 PS는 ' Az 1.0.0 ' 이었습니다. GA의 예정 된 변경 내용으로 인해 필요한 최소 PS 버전은 ' Az. RecoveryServices 2.6.0 '입니다. 기존 PS 버전을 모두이 버전으로 업그레이드 하는 것이 매우 중요 합니다. 그렇지 않으면 GA 후에 기존 스크립트가 중단 됩니다. 다음 PS 명령을 사용 하 여 최소 버전을 설치 합니다.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. 다음 명령을 사용 하 여 Azure Backup PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup 및 Azure Site Recovery에 대 한 별칭 및 cmdlet을 검토 하 고 Recovery Services 자격 증명 모음이 표시 됩니다. 다음은 표시 될 수 있는 항목의 예입니다. Cmdlet의 전체 목록은 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **AzAccount**를 사용 하 여 Azure 계정에 로그인 합니다.
5. 표시 되는 웹 페이지에 계정 자격 증명을 입력 하 라는 메시지가 표시 됩니다.

    * 또는 **자격**증명을 사용 하 여 **AzAccount** cmdlet에 계정 자격 증명을 매개 변수로 포함할 수 있습니다.
    * 테 넌 트를 대신 하 여 작업 중인 CSP 파트너인 경우 tenantID 또는 테 넌 트 주 도메인 이름을 사용 하 여 고객을 테 넌 트로 지정 합니다. 예: **Connect-AzAccount -Tenant** fabrikam.com.

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

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹 내에 배치해야 합니다. 기존 리소스 그룹을 사용하거나 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들 때 리소스 그룹의 이름과 위치를 지정합니다.

다음 단계에 따라 Recovery Services 자격 증명 모음을 만들 수 있습니다.

1. 자격 증명 모음은 리소스 그룹에 배치 됩니다. 기존 리소스 그룹이 없는 경우 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0)를 사용 하 여 새 리소스 그룹을 만듭니다. 이 예제에서는 미국 서 부 지역에 새 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. [AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) cmdlet을 사용 하 여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용된 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. 자격 증명 모음 저장소에 사용할 중복성 유형을 지정 합니다.

   * [로컬 중복 스토리지](../storage/common/storage-redundancy-lrs.md) 또는 [지역 중복 스토리지](../storage/common/storage-redundancy-grs.md)를 사용할 수 있습니다.
   * 다음 예에서는 **GeoRedundant**로 설정 된 **testvault 된** 에 대 한 **BackupStorageRedundancy** 옵션을[AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd로 설정 합니다.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사 합니다. 연결 된 리소스 그룹 및 위치가 제공 됩니다.

```powershell
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
* 자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0)를 사용 하 여 설정 합니다. 컨텍스트를 설정한 후에는 모든 후속 cmdlet에 적용 됩니다.

다음 예제에서는 **testvault**에 대한 자격 증명 모음 컨텍스트를 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID를 가져옵니다.

Azure PowerShell 지침에 따라 자격 증명 모음 컨텍스트 설정을 사용 중단 계획 합니다. 대신 자격 증명 모음 ID를 저장 하거나 인출 하 고 관련 명령에 전달할 수 있습니다. 따라서 자격 증명 모음 컨텍스트를 설정 하지 않았거나 특정 자격 증명 모음에 대해 실행할 명령을 지정 하려는 경우 다음과 같이 자격 증명 모음 ID를 모든 관련 명령에 "-vaultID"으로 전달 합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정 및 백업 복구 지점이 유지 되는 기간을 지정 합니다.

* 백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다.
* [AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)를 사용 하 여 기본 백업 정책 보존을 확인 합니다.
* [AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)를 사용 하 여 기본 백업 정책 일정을 확인 합니다.
* [AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) cmdlet을 사용 하 여 새 백업 정책을 만듭니다. 일정 및 보존 정책 개체를 입력 합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의 되어 있습니다. 다음 예를 사용 하 여 시작 시간을 원하는 시작 시간으로 변경할 수 있습니다. 원하는 시작 시간은 UTC로도 지정 해야 합니다. 아래 예제에서는 원하는 시작 시간이 매일 백업의 경우 01:00 AM UTC 라고 가정 합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30 분의 배수로 시작 시간을 제공 해야 합니다. 위의 예제에서 "01:00:00" 또는 "02:30:00"만 가능 합니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수를 새 정책에 대 한 매개 변수로 사용 합니다 (**Newafspolicy**). **NewAFSPolicy**는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

출력은 다음과 유사 합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>백업 사용

백업 정책을 정의한 후에는 정책을 사용 하 여 Azure 파일 공유에 대 한 보호를 사용 하도록 설정할 수 있습니다.

### <a name="retrieve-a-backup-policy"></a>백업 정책 검색

[AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0)를 사용 하 여 관련 정책 개체를 가져옵니다. 이 cmdlet을 사용 하 여 특정 정책을 가져오거나 작업 유형과 연결 된 정책을 볼 수 있습니다.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>작업 유형에 대 한 정책 검색

다음 예에서는 **Azurefiles**작업 유형에 대 한 정책을 검색 합니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

출력은 다음과 유사 합니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 **BackupTime** 필드의 표준 시간대는UTC(협정 세계시)입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

### <a name="retrieve-a-specific-policy"></a>특정 정책 검색

다음 정책은 **dailyafs**라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>백업 사용 및 정책 적용

[AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0)를 사용 하 여 보호를 사용 하도록 설정 합니다. 정책이 자격 증명 모음과 연결 된 후 백업은 정책 일정에 따라 트리거됩니다.

다음 예제에서는 정책 **dailyafs**를 사용 하 여 저장소 계정 **Testazurefileshare**에서 Azure 파일 공유 **testazurefileshare** 공유에 대 한 보호를 사용 하도록 설정 합니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

명령은 보호 구성 작업을 마칠 때까지 대기하며 다음과 같은 출력을 제공합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>중요 알림-AFS 백업에 대 한 백업 항목 id

이 섹션에서는 GA를 준비할 때 AFS 백업에서 중요 한 변경 사항에 대해 간략하게 설명 합니다.

AFS에 대해 백업을 사용 하도록 설정 하는 동안 사용자는 고객에 게 친숙 한 파일 공유 이름을 엔터티 이름으로 제공 하 고 백업 항목을 만듭니다. 백업 항목의 ' n a s e '는 Azure Backup 서비스에서 만든 고유 식별자입니다. 일반적으로 식별자는 사용자에 게 친숙 한 이름을 포함 합니다. 하지만 파일 공유를 삭제할 수 있고 동일한 이름을 사용 하 여 다른 파일 공유를 만들 수 있는 일시 삭제의 중요 한 시나리오를 처리 하기 위해 Azure 파일 공유의 고유 id는 이제 고객에 게 친숙 한 이름 대신 ID가 됩니다. 각 항목의 고유한 id/이름을 확인 하려면 backupManagementType 및 WorkloadType에 대 한 관련 필터를 사용 하 여 ```Get-AzRecoveryServicesBackupItem``` 명령을 실행 하 여 모든 관련 항목을 가져온 다음 반환 된 PS 개체/응답에서 이름 필드를 관찰 하면 됩니다. 항목을 나열 하 고 응답의 ' 이름 ' 필드에서 고유한 이름을 검색 하는 것이 항상 권장 됩니다. ' Name ' 매개 변수를 사용 하 여 항목을 필터링 하려면이 값을 사용 합니다. 그렇지 않으면 FriendlyName 매개 변수를 사용 하 여 고객에 게 친숙 한 이름/식별자로 항목을 검색 합니다.

> [!WARNING]
> AFS 백업의 경우 PS 버전이 ' Az. RecoveryServices 2.6.0 '의 최소 버전으로 업그레이드 되었는지 확인 합니다. 이 버전을 사용 하면 ' friendlyName ' 필터를 ```Get-AzRecoveryServicesBackupItem``` 명령에 사용할 수 있습니다. Azure 파일 공유 이름을 friendlyName 매개 변수로 전달 합니다. Azure 파일 공유 이름을 ' Name ' 매개 변수에 전달 하는 경우이 버전은 친근 한 이름을 name 매개 변수에 전달 하는 경고를 throw 합니다. 이 최소 버전을 설치 하지 않으면 기존 스크립트에 오류가 발생할 수 있습니다. 다음 명령을 사용 하 여 PS의 최소 버전을 설치 합니다.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

[AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) 를 사용 하 여 보호 된 Azure 파일 공유에 대 한 주문형 백업을 실행 합니다.

1. [AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)를 사용 하 여 백업 데이터를 보관 하는 자격 증명 모음의 컨테이너에서 저장소 계정을 검색 합니다.
2. 백업 작업을 시작 하려면 [AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)를 사용 하 여 Azure 파일 공유에 대 한 정보를 가져옵니다.
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)을 사용하여 주문형 백업을 실행합니다.

다음과 같이 주문형 백업을 실행 합니다.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

이 명령은 다음 예제에 표시된 대로 추적할 ID가 포함된 작업을 반환합니다.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

백업을 수행하는 동안 Azure 파일 공유 스냅샷이 사용되기 때문에 일반적으로 명령에 이 출력이 반환될 때까지 작업이 완료됩니다.

### <a name="using-on-demand-backups-to-extend-retention"></a>주문형 백업을 사용 하 여 보존 연장

주문형 백업은 10 년 동안 스냅숏을 유지 하는 데 사용할 수 있습니다. Scheduler를 사용 하 여 선택한 보존으로 주문형 PowerShell 스크립트를 실행 하 여 매주, 월 또는 연도 마다 스냅숏을 정기적으로 수행할 수 있습니다. 정기적인 스냅숏을 만드는 동안 Azure backup을 사용 하 여 [주문형 백업의 제한 사항을](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) 참조 하세요.

샘플 스크립트를 찾고 있는 경우 주기적으로 백업을 예약 하 고 최대 10 년까지 유지할 수 있도록 하는 Azure Automation runbook을 사용 하 여 GitHub (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>)에서 샘플 스크립트를 참조할 수 있습니다.

> [!WARNING]
> Automation runbook에서 AFS 백업의 경우 PS 버전이 ' Az. RecoveryServices 2.6.0 '의 최소 버전으로 업그레이드 되었는지 확인 합니다. 이전 ' AzureRM ' 모듈을 ' Az ' 모듈로 바꾸어야 합니다. 이 버전을 사용 하면 ' friendlyName ' 필터를 ```Get-AzRecoveryServicesBackupItem``` 명령에 사용할 수 있습니다. Azure 파일 공유 이름을 friendlyName 매개 변수로 전달 합니다. Azure 파일 공유 이름을 ' Name ' 매개 변수에 전달 하는 경우이 버전은 친근 한 이름을 name 매개 변수에 전달 하는 경고를 throw 합니다.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure Files를 백업 하는 [방법에 대해 알아봅니다](backup-afs.md) .
