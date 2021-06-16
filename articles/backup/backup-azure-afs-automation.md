---
title: PowerShell을 사용하여 Azure 파일 공유 백업
description: 이 문서에서는 Azure Backup 서비스와 PowerShell을 사용하여 Azure Files 파일 공유를 백업하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74478d98d63a18582d93c8cddb872ffb3c3774f8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681490"
---
# <a name="back-up-an-azure-file-share-by-using-powershell"></a>PowerShell을 사용하여 Azure 파일 공유 백업

이 문서에서는 Azure PowerShell을 사용하여 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음을 통해 Azure Files 파일 공유를 백업하는 방법을 설명합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * PowerShell을 설정하고 Recovery Services 공급자를 등록합니다.
> * Recovery Services 자격 증명 모음을 만듭니다.
> * Azure 파일 공유에 대한 백업을 구성합니다.
> * 백업 작업을 실행합니다.

## <a name="before-you-start"></a>시작하기 전에

* Recovery Services 자격 증명 모음에 대한 [자세한 내용](backup-azure-recovery-services-vault-overview.md)을 알아봅니다.
* Azure 라이브러리의 Az.RecoveryServices [cmdlet 참조](/powershell/module/az.recoveryservices)를 검토합니다.
* Recovery Services에 대한 다음 PowerShell 개체 계층 구조를 검토합니다.

  ![Recovery Services 개체 계층 구조](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

## <a name="set-up-powershell"></a>PowerShell 설정

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음과 같이 PowerShell을 설정합니다.

1. [최신 버전의 Azure PowerShell을 다운로드](/powershell/azure/install-az-ps)합니다.

    > [!NOTE]
    > Azure 파일 공유를 백업하는 데 필요한 최소 PowerShell 버전은 Az.RecoveryServices 2.6.0입니다. 최신 버전 또는 최소 버전 이상을 사용하면 기존 스크립트 문제를 방지하는 데 도움이 됩니다. 다음 PowerShell 명령을 사용하여 최소 버전을 설치합니다.
    >
    > ```powershell
    > Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
    > ```

2. 다음 명령을 사용하여 Azure Backup에 대한 PowerShell cmdlet을 찾습니다.

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Azure Backup, Azure Site Recovery, Recovery Services 자격 증명 모음의 별칭과 cmdlet을 검토합니다. 다음은 관련 예제입니다. 이 목록은 cmdlet의 전체 목록이 아닙니다.

    ![Recovery Services cmdlet 목록](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. **Connect-AzAccount** 를 사용하여 Azure 계정에 로그인합니다.
5. 표시되는 웹 페이지에 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

    또는 **Connect-AzAccount** cmdlet에서 **-Credential** 을 사용하여 계정 자격 증명을 매개 변수로 포함할 수 있습니다.

    테넌트를 대신하여 작업 중인 CSP 파트너인 경우 고객을 테넌트로 지정합니다. 테넌트 ID 또는 테넌트 주 도메인 이름을 사용합니다. **Connect-AzAccount -Tenant "fabrikam.com"** 을 예로 들 수 있습니다.

6. 계정에 여러 구독이 있을 수 있으므로 사용할 구독을 계정과 연결합니다.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Azure Backup을 처음 사용하는 경우 **Register-AzResourceProvider** cmdlet을 사용하여 구독에 Azure Recovery Services 공급자를 등록합니다.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. 공급자가 성공적으로 등록되었는지 확인합니다.

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. 명령 출력에서 **RegistrationState** 가 **Registered** 로 변경되는지 확인합니다. 변경되지 않은 경우 **Register-AzResourceProvider** cmdlet을 다시 실행합니다.

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음은 Resource Manager 리소스이므로 리소스 그룹에 배치해야 합니다. 기존 리소스 그룹을 사용할 수도 있고 **New-AzResourceGroup** cmdlet을 사용하여 리소스 그룹을 만들 수도 있습니다. 리소스 그룹을 만드는 경우 이름 및 위치를 지정합니다.

다음 단계에 따라 Recovery Services 자격 증명 모음을 만듭니다.

1. 기존 리소스 그룹이 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만듭니다. 이 예제에서는 미국 서부 지역에 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet을 사용하여 자격 증명 모음을 만듭니다. 리소스 그룹에 사용한 위치와 동일한 위치를 자격 증명 모음에도 지정합니다.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

### <a name="view-the-vaults-in-a-subscription"></a>구독의 자격 증명 모음 보기

구독의 모든 자격 증명 모음을 보려면 [Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)를 사용합니다.

```powershell
Get-AzRecoveryServicesVault
```

출력은 다음과 유사합니다. 출력은 연결된 리소스 그룹 및 위치를 제공합니다.

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

자격 증명 모음 개체를 변수에 저장하고 자격 증명 모음 컨텍스트를 설정합니다.

많은 Azure Backup cmdlet에 입력으로 Recovery Services 자격 증명 모음 개체가 필요하므로 자격 증명 모음 개체를 변수에 저장하는 것이 편리합니다.

자격 증명 모음 컨텍스트는 자격 증명 모음에서 보호되는 데이터의 형식입니다. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext)를 사용하여 설정합니다. 컨텍스트가 설정되면 모든 후속 cmdlet에 적용됩니다.

다음 예제에서는 자격 증명 모음 컨텍스트를 **testvault** 로 설정합니다.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>자격 증명 모음 ID 가져오기

Azure PowerShell 지침과 관련하여 자격 증명 모음 컨텍스트 설정을 사용 중단할 계획입니다. 대신 자격 증명 모음 ID를 보관하거나 가져오고 관련 명령으로 전달할 수 있습니다. 자격 증명 모음 컨텍스트를 설정하지 않았거나 특정 자격 증명 모음에 대해 실행할 명령을 지정하려는 경우 다음과 같이 자격 증명 모음 ID를 `-vaultID`로 모든 관련 명령에 전달합니다.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>백업 정책 구성

백업 정책은 백업 일정과 백업 복구 지점을 얼마나 오래 보관해야 하는지 지정합니다.

백업 정책은 하나 이상의 보존 정책과 연관됩니다. 보존 정책은 복구 지점을 삭제하기 유지할 기간을 정의합니다. 매일, 매주, 매월 또는 매년 보존을 사용하여 백업을 구성할 수 있습니다.

다음은 백업 정책에 대한 몇 가지 cmdlet입니다.

* [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject)를 사용하여 기본 백업 정책 보존을 확인합니다.
* [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject)를 사용하여 기본 백업 정책 일정을 확인합니다.
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)를 사용하여 새 백업 정책을 만듭니다. 입력으로 일정과 보존 정책 개체를 입력합니다.

기본적으로 시작 시간은 일정 정책 개체에 정의되어 있습니다. 다음 예제를 사용하여 시작 시간을 원하는 시간으로 변경합니다. 원하는 시작 시간은 UTC(협정 세계시)로 지정해야 합니다. 이 예제에서는 원하는 일일 백업 시작 시간이 01:00 AM UTC라고 가정합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> 30분의 배수로 시작 시간을 제공해야 합니다. 앞의 예제에서는 "01:00:00" 또는 "02:30:00"만 사용할 수 있습니다. 시작 시간은 "01:15:00" 일 수 없습니다.

다음 예제에서는 일정 정책 및 보존 정책을 변수에 저장합니다. 그런 다음 해당 변수를 새 정책의 매개 변수로 사용합니다(**NewAFSPolicy**). **NewAFSPolicy** 는 매일 백업을 생성하여 30일 동안 보존합니다.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

다음과 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>백업 사용

백업 정책을 정의하면 이 정책을 통해 Azure 파일 공유에 대한 보호를 활성화할 수 있습니다.

### <a name="retrieve-a-backup-policy"></a>백업 정책 검색

[AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)을 사용하여 관련 정책 개체를 가져옵니다. 이 cmdlet을 사용하여 특정 정책을 가져오거나 워크로드 유형과 연결된 정책을 볼 수 있습니다.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>워크로드 유형에 대한 정책 검색

다음 예제에서는 **AzureFiles**:워크로드 유형을 검색합니다.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

다음과 유사하게 출력됩니다.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> PowerShell에서 **BackupTime** 필드의 표준 시간대는 UTC입니다. Azure Portal에 백업 시간이 표시될 때는, 사용자의 현지 표준 시간대로 시간이 조정됩니다.

#### <a name="retrieve-a-specific-policy"></a>특정 정책 검색

다음 정책은 **dailyafs** 라는 백업 정책을 검색합니다.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-protection-and-apply-the-policy"></a>보호 사용 및 정책 적용

[Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection)을 사용하여 보호를 사용하도록 설정합니다. 정책이 자격 증명 모음과 연결되면 정책 일정에 따라 백업이 트리거됩니다.

다음 예제에서는 **dailyafs** 정책을 사용하여 스토리지 계정 **testStorageAcct** 에서 Azure 파일 공유 **testAzureFileShare** 에 대한 보호를 사용하도록 설정합니다.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

이 명령은 보호 구성 작업을 마칠 때까지 대기하며 다음 예제와 비슷한 출력을 제공합니다.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

스토리지 계정의 파일 공유 목록을 가져오는 방법에 대한 자세한 내용은 [이 문서](/powershell/module/az.storage/get-azstorageshare)를 참조하세요.

## <a name="important-notice-backup-item-identification"></a>중요 알림: 백업 항목 식별

이 섹션에서는 일반 공급에 대비한 Azure 파일 공유 백업의 중요한 변경 사항을 간략하게 설명합니다.

Azure 파일 공유 백업을 사용하도록 설정하는 동안 사용자는 고객에게 파일 공유 이름을 엔터티 이름으로 제공하고 백업 항목을 만듭니다. 백업 항목 이름은 Azure Backup 서비스에서 만드는 고유 식별자입니다. 일반적으로 식별자는 사용자 식별 이름입니다. 하지만 파일 공유를 삭제할 수 있고 동일한 이름으로 또 다른 파일 공유를 만들 수 있는 일시 삭제 시나리오를 처리하려면 Azure 파일 공유의 고유 ID가 이제 ID가 됩니다.

각 항목의 고유 ID를 확인하려면 **backupManagementType** 및 **WorkloadType** 에 대한 관련 필터를 사용하여 **Get-AzRecoveryServicesBackupItem** 명령을 실행하고 모든 관련 항목을 가져옵니다. 그런 다음, 반환된 PowerShell 개체/응답에서 이름 필드를 확인합니다.

항목을 나열한 후 응답의 이름 필드에서 고유한 이름을 검색하는 것이 좋습니다. *Name* 매개 변수를 사용하여 항목을 필터링하려면 이 값을 사용합니다. 또는 *FriendlyName* 매개 변수를 사용하여 해당 ID를 가진 항목을 검색합니다.

> [!IMPORTANT]
> PowerShell은 Azure 파일 공유 백업을 위한 최소 버전(Az.RecoveryServices 2.6.0)으로 업그레이드되어야 합니다. 이 버전이 있으면 **Get-AzRecoveryServicesBackupItem** 명령에 *FriendlyName* 필터를 사용할 수 있습니다.
>
> Azure 파일 공유 이름을 *FriendlyName* 매개 변수에 전달합니다. 파일 공유 이름을 *Name* 매개 변수에 전달하면 이 버전은 경고를 throw하여 해당 이름을 *FriendlyName* 매개 변수에 전달합니다.
>
> 최소 버전을 설치하지 않으면 기존 스크립트가 실패할 수 있습니다. 다음 명령을 사용하여 PowerShell의 최소 버전을 설치하세요.
>
>```powershell
>Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
>```

## <a name="trigger-an-on-demand-backup"></a>주문형 백업 트리거

[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem)을 사용하여 보호되는 Azure 파일 공유에 대한 주문형 백업을 실행합니다.

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)를 사용하여 백업 데이터가 보관된 자격 증명 모음의 컨테이너에서 스토리지 계정을 검색합니다.
2. 백업 작업을 시작하려면 [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)을 사용하여 Azure 파일 공유에 대한 정보를 가져옵니다.
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)을 사용하여 주문형 백업을 실행합니다.

다음과 같이 주문형 백업을 실행합니다.

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

백업을 수행하는 동안 Azure 파일 공유 스냅샷이 사용됩니다. 일반적으로 명령에서 이 출력을 반환할 때까지 작업이 완료됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 Azure Files를 백업](backup-afs.md)하는 방법을 알아봅니다.
* Azure Automation Runbook을 사용하여 백업을 예약하려면 [GitHub의 샘플 스크립트](https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup).
