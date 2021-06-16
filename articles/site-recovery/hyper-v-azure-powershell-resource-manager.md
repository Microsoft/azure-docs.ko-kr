---
title: Azure Site Recovery 및 PowerShell을 사용하는 Hyper-V VM 재해 복구
description: PowerShell 및 Azure Resource Manager를 사용하여 Azure Site Recovery 서비스로 Hyper-V VM과 Azure 간 재해 복구를 자동화합니다.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 987a3f82ca94f51c82a9077be0c0cb172187e362
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110703220"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정

[Azure Site Recovery](site-recovery-overview.md)는 Azure VM(가상 머신)과 온-프레미스 VM 및 물리적 서버의 복제, 장애 조치(failover) 및 복구를 오케스트레이션하여 BCDR(비즈니스 연속성 및 재해 복구) 전략에 기여합니다.

이 문서에서는 Windows PowerShell을 Azure Resource Manager와 함께 사용하여 Hyper-V VM을 Azure로 복제하는 방법을 설명합니다. 이 문서에 사용된 예제에서는 Azure에 대한 Hyper-V 호스트에서 실행 중인 단일 VM을 복제하는 방법을 보여줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell은 Windows PowerShell을 사용하여 Azure를 관리하기 위한 cmdlet을 제공합니다. Azure Resource Manager에 대한 Azure PowerShell과 함께 사용할 수 있는 사이트 복구 PowerShell cmdlet을 사용하여 Azure에서 서버를 보호하고 복구할 수 있습니다.

이 문서를 사용하기 위해 PowerShell 전문가가 될 필요는 없지만 모듈, cmdlet 및 세션과 같은 기본 개념은 이해해야 합니다. 자세한 정보는 [PowerShell 설명서](/powershell) 및 [Azure Resource Manager를 사용하여 Azure PowerShell 사용](../azure-resource-manager/management/manage-resources-powershell.md)을 참조하세요.

> [!NOTE]
> CSP(클라우드 솔루션 공급자) 프로그램의 Microsoft 파트너는 고객의 각 CSP 구독(테넌트 구독)에 대한 고객의 서버 보호를 구성하고 관리할 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

다음 필수 조건이 충족되었는지 확인합니다.

- [Microsoft Azure](https://azure.microsoft.com/) 계정. [평가판](https://azure.microsoft.com/pricing/free-trial/)으로 시작할 수 있습니다. [Azure Site Recovery Manager 가격](https://azure.microsoft.com/pricing/details/site-recovery/)에 대해 알아볼 수도 있습니다.
- Azure PowerShell. 이 릴리스에 대한 정보 및 설치 방법은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

또한 이 문서에 설명된 특정 예제에는 다음과 같은 필수 조건이 있습니다.

- 하나 이상의 VM이 포함된 Windows Server 2012 R2 또는 Microsoft Hyper-V Server 2012 R2를 실행하는 Hyper-V 호스트. Hyper-V 서버가 직접 또는 프록시를 통해 인터넷에 연결되어야 합니다.
- 복제하려는 VM은 [이러한 필수 조건](hyper-v-azure-support-matrix.md#replicated-vms)에 부합해야 합니다.

## <a name="step-1-sign-in-to-your-azure-account"></a>1단계: Azure 계정에 로그인

1. PowerShell 콘솔을 열고 이 명령을 실행하여 Azure 계정에 로그인합니다. cmdlet은 계정 자격 증명을 묻는 웹 페이지를 엽니다: `Connect-AzAccount`.
   - 또는 **Credential** 매개 변수를 사용하여 `Connect-AzAccount` cmdlet에서 매개 변수로 계정 자격 증명을 포함할 수 있습니다.
   - 사용자가 테넌트를 대신하여 작업 중인 CSP 파트너인 경우에는 tenantID 또는 테넌트 기본 도메인 이름을 사용하여 고객을 테넌트로 지정합니다. 예: `Connect-AzAccount -Tenant "fabrikam.com"`
1. 계정에 여러 구독이 있을 수 있으므로 사용하려는 구독을 계정과 연결합니다.

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. 다음 명령을 사용하여 Recovery Services와 Site Recovery에 대해 Azure 공급자를 사용하도록 구독이 등록되었는지 확인합니다.

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. 명령의 출력에서 **RegistrationState** 가 **등록됨** 으로 설정된 경우에 2단계를 진행할 수 있습니다. 그렇지 않은 경우 다음 명령을 실행하여 구독에 누락된 공급자를 등록해야 합니다.

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. 다음 명령을 사용하여 공급자가 성공적으로 등록되었는지 확인합니다.

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>2단계: 자격 증명 모음 설정

1. 자격 증명 모음을 만들 Azure Resource Manager 리소스 그룹을 만들거나 기존 리소스 그룹을 사용합니다. 다음과 같이 새 리소스 그룹을 만듭니다. `$ResourceGroupName` 변수는 만들려는 리소스 그룹의 이름을 포함하며 $Geo 변수는 리소스 그룹을 만들 Azure 지역을 포함합니다(예: “브라질 남부").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. `Get-AzResourceGroup` cmdlet을 실행하여 구독에 리소스 그룹의 목록을 가져올 수 있습니다.
1. 다음과 같이 새 Azure Recovery Services 자격 증명 모음을 만듭니다.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

`Get-AzRecoveryServicesVault` cmdlet으로 기존 자격 증명 모음의 목록을 검색할 수 있습니다.

## <a name="step-3-set-the-recovery-services-vault-context"></a>3단계: Recovery Services 자격 증명 모음 설정

자격 증명 모음 컨텍스트를 다음과 같이 설정합니다.

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>4단계: Hyper-V 사이트 만들기

1. 다음과 같이 새 Hyper-V 사이트를 만듭니다.

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. 이 cmdlet은 사이트 복구 작업을 시작하여 사이트를 만들고 사이트 복구 작업 개체를 반환합니다. 작업이 완료될 때까지 기다리고 작업이 성공적으로 완료되었는지 확인합니다.
1. `Get-AzRecoveryServicesAsrJob` cmdlet을 사용하여 작업 개체를 검색하고, 작업의 현재 상태를 확인합니다.
1. 다음과 같이 사이트에 대한 등록 키를 생성하고 다운로드합니다.

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Hyper-V 호스트에 다운로드한 키를 복사합니다. 사이트에 Hyper-V 호스트를 등록하는 키가 필요합니다.

## <a name="step-5-install-the-provider-and-agent"></a>5단계: 공급자 및 에이전트 설치

1. [Microsoft](https://aka.ms/downloaddra)에서 공급자의 최신 버전을 위한 설치 관리자를 다운로드합니다.
1. Hyper-V 호스트에서 설치 프로그램을 실행합니다.
1. 설치가 끝나면 등록 단계를 계속합니다.
1. 메시지가 표시되면 다운로드한 키를 제공하고 사이트에 Hyper-V 호스트의 등록을 완료합니다.
1. 다음을 사용하여 Hyper-V 호스트가 사이트에 등록되었는지 확인합니다.

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Hyper-V 코어 서버를 실행하는 경우 설치 파일을 다운로드하고 다음 단계를 수행합니다.

1. 다음 명령을 실행하여 _AzureSiteRecoveryProvider.exe_ 에서 로컬 디렉터리로 파일을 추출합니다.

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. 다음 명령 실행:

   ```console
   .\setupdr.exe /i
   ```

   결과가 _%ProgramData%\ASRLogs\DRASetupWizard.log_ 에 로그됩니다.

1. 다음 명령을 실행하여 서버를 등록합니다.

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>6단계: 복제 정책 만들기

시작하기 전에 지정된 스토리지 계정은 자격 증명 모음과 같은 Azure 지역에 있어야 하며 지역에서 복제를 사용하도록 설정되어 있어야 합니다.

1. 다음과 같이 복제 정책을 만듭니다.

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. 반환된 작업을 확인하여 복제 정책 만들기 성공했는지 확인합니다.

1. 다음과 같이 사이트에 해당하는 보호 컨테이너를 가져옵니다.

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. 다음과 같이 보호 컨테이너를 복제 정책과 연결합니다.

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. 연결 작업이 완료될 때까지 기다립니다.

1. 보호 컨테이너 매핑을 검색합니다.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>단계7: VM 보호 사용

1. 다음과 같이 보호하려는 VM에 해당하는 보호 항목을 검색합니다.

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. VM을 보호합니다. 보호 중인 VM에 하나 이상의 디스크가 연결된 경우 **OSDiskName** 매개 변수를 사용하여 운영 체제 디스크를 지정합니다.

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. VM이 초기 복제 후 보호되는 상태에 도달할 때까지 기다립니다. 시간이 걸릴 수 있으며 복제될 데이터의 양과 Azure에 사용 가능한 업스트림 대역폭 등의 요인에 따라 걸리는 시간이 달라질 수 있습니다. 보호된 상태가 되면 작업 상태와 작업 설명이 다음과 같이 업데이트됩니다.

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. 장애 조치 후 VM NIC를 연결할 Azure 네트워크 및 복구 속성(예: VM 역할 크기)을 업데이트합니다.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Azure에서 CMK 사용 관리 디스크에 복제하려는 경우 Az PowerShell 3.3.0 이상을 사용하여 다음 단계를 수행합니다.
>
> 1. VM 속성을 업데이트하여 관리 디스크에 대한 장애 조치(failover) 사용
> 1. `Get-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet을 사용하여 보호된 항목의 각 디스크에 디스크 ID를 가져옵니다.
> 1. 디스크 암호화 집합에 디스크 ID 매핑을 포함하도록 `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` cmdlet을 사용하여 사전 개체를 만듭니다. 이러한 디스크 암호화 집합은 대상 지역에서 미리 생성됩니다.
> 1. **DiskIdToDiskEncryptionSetMap** 매개 변수에서 사전 개체를 전달하여 `Set-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet을 사용하여 VM 속성을 업데이트합니다.

## <a name="step-8-run-a-test-failover"></a>8단계: 테스트 장애 조치(failover) 실행

1. 다음과 같이 테스트 장애 조치(failover)를 실행합니다.

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. 테스트 VM이 Azure에서 만들어졌는지 확인합니다. Azure에서 테스트 VM을 만든 후 테스트 장애 조치 작업이 일시 중단됩니다.
1. 테스트장애 조치(failover)를 정리하고 완료하려면 다음을 실행합니다.

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>다음 단계

Azure Resource Manager PowerShell cmdlet을 사용한 Azure Site Recovery에 대해 [자세히 알아보기](/powershell/module/az.recoveryservices).
