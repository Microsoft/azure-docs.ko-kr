---
title: Azure Site Revoery에서 PowerShell을 사용하여 VMware 재해 복구 설정
description: Azure Site Recovery에서 PowerShell을 사용하여 VMware VM의 재해 복구를 위해 Azure로 복제 및 장애 조치(failover)를 설정하는 방법을 알아봅니다.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: de25a3f9df04b09a7337dc889a688a171d98db28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86129915"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정

이 문서에서는 Azure PowerShell을 사용하여 VMware 가상 머신을 Azure로 복제 및 장애 조치(failover)하는 방법을 설명합니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> - Recovery Services 자격 증명 모음을 만들고 자격 증명 모음 컨텍스트를 설정합니다.
> - 자격 증명 모음에서 서버 등록 유효성을 검사합니다.
> - 복제 정책을 포함하여 복제를 설정합니다. vCenter 서버를 추가하고 VM을 검색합니다.
> - vCenter 서버 추가 및 검색
> - 복제 로그 또는 데이터를 저장하고 VM을 복제할 스토리지 계정을 만듭니다.
> - 장애 조치(failover)를 수행합니다. 장애 조치(failover) 설정을 구성하고 가상 머신 복제를 위한 설정을 수행합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에

- [시나리오 아키텍처 및 구성 요소](vmware-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](./vmware-physical-azure-support-matrix.md)을 검토합니다.
- Azure PowerShell `Az` 모듈이 있어야 합니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="log-into-azure"></a>Azure에 로그인

Connect-AzAccount cmdlet을 사용하여 Azure 구독에 로그인합니다.

```azurepowershell
Connect-AzAccount
```
VMware 가상 머신을 복제할 대상 Azure 구독을 선택합니다. Get-AzSubscription cmdlet을 사용하여 액세스 권한이 있는 Azure 구독 목록을 가져옵니다. Select-AzSubscription cmdlet을 사용하여 작업에 사용할 Azure 구독을 선택합니다.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 설정

1. Recovery Services 자격 증명 모음을 만들 리소스 그룹을 만듭니다. 아래 예에서 리소스 그룹의 이름이 VMwareDRtoAzurePS로 지정되고 동아시아 지역에 생성되었습니다.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Recovery Services 자격 증명 모음을 만듭니다. 아래 예에서 Recovery Services 자격 증명 모음의 이름은 VMwareDRToAzurePs로 지정되고 동아시아 지역 및 이전 단계에서 만든 리소스 그룹에 생성되었습니다.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
   ```
   ```
   Name              : VMwareDRToAzurePs
   ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
   Type              : Microsoft.RecoveryServices/vaults
   Location          : eastasia
   ResourceGroupName : VMwareDRToAzurePs
   SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
   Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
   ```

3. 자격 증명 모음의 자격 증명 모음 등록 키를 다운로드합니다. 자격 증명 모음 등록 키는 온-프레미스 구성 서버를 이 자격 증명 모음에 등록하는 데 사용됩니다. 등록은 구성 서버 소프트웨어 설치 프로세스의 일부입니다.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. 다운로드한 자격 증명 모음 등록 키를 사용하여 아래 제공된 문서의 단계에 따라 구성 서버의 설치 및 등록을 완료합니다.
   - [보호 목표 선택](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [원본 환경 설정](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

Set-ASRVaultContext cmdlet을 사용하여 자격 증명 모음 컨텍스트를 설정합니다. 설정이 되면 PowerShell 세션의 후속 Azure Site Recovery 작업은 선택한 자격 증명 모음의 컨텍스트에서 수행됩니다.

> [!TIP]
> Azure Site Recovery PowerShell 모듈(Az.RecoveryServices module)은 대부분의 cmdlet에 사용하기 쉬운 별칭이 함께 제공됩니다. 모듈의 cmdlet은 *\<Operation>-**AzRecoveryServicesAsr**\<Object>* 형식이며 *\<Operation>-**ASR**\<Object>* 형식에 해당하는 별칭이 있습니다. 사용 편의성을 위해 cmdlet 별칭을 바꿀 수 있습니다.

아래 예에서 $vault 변수의 자격 증명 모음 세부 정보가 PowerShell 세션의 자격 증명 모음 컨텍스트를 지정하는 데 사용됩니다.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

Set-ASRVaultContext cmdlet 대신 Import-Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet을 사용하여 자격 증명 모음 컨텍스트를 설정할 수도 있습니다. 자격 증명 모음 등록 키 파일이 있는 경로를 Import-AzRecoveryServicesAsrVaultSettingsFile cmdlet에 대한 -path 매개 변수로 지정합니다. 예를 들면 다음과 같습니다.

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
이 문서의 다음 섹션에서는 Azure Site Recovery 작업에 대한 자격 증명 모음 컨텍스트가 설정되었다고 가정합니다.

## <a name="validate-vault-registration"></a>자격 증명 모음 등록 유효성 검사

이 예제의 조건은 다음과 같습니다.

- 구성 서버(**ConfigurationServer**)가 이 자격 증명 모음에 등록되었습니다.
- 추가 프로세스 서버(**ScaleOut-ProcessServer**)가 *ConfigurationServer* 에 등록되었습니다.
- 계정(**vCenter_account**, **WindowsAccount**, **LinuxAccount**)이 구성 서버에 설정되었습니다. 이러한 계정은 vCenter Server를 추가하여 가상 머신을 검색하고 Windows 및 Linux 서버에 복제될 모바일 서비스 소프트웨어를 강제 설치하는 데 사용됩니다.

1. 등록된 구성 서버는 Site Recovery의 패브릭 개체로 표시됩니다. 자격 증명 모음에서 패브릭 개체 목록을 가져와서 구성 서버를 식별합니다.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
   $ASRFabrics.count
   ```
   ```
   1
   ```
   ```azurepowershell
   #Print details of the Configuration Server
   $ASRFabrics[0]
   ```
   ```
   Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   FriendlyName          : ConfigurationServer
   ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                           /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
   Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
   FabricType            : VMware
   SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
   FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
   ```

2. 컴퓨터를 복제하는 데 사용할 수 있는 프로세스 서버를 식별합니다.

   ```azurepowershell
   $ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
   for($i=0; $i -lt $ProcessServers.count; $i++) {
    "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
   }
   ```
   ```
   0     ScaleOut-ProcessServer
   1     ConfigurationServer
   ```

   위의 출력 내용에서 * **$ProcessServers[0]** _는 _ScaleOut-ProcessServer*에 해당하고 * **$ProcessServers[1]**_ 는 _ConfigurationServer*의 프로세스 서버 역할에 해당함

3. 구성 서버에 설정된 계정을 식별합니다.

   ```azurepowershell
   $AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
   #Print the account details
   $AccountHandles
   ```
   ```
   AccountId AccountName
   --------- -----------
   1         vCenter_account
   2         WindowsAccount
   3         LinuxAccount
   ```

   위의 출력 내용에서 * **$AccountHandles[0]** _은 _vCenter_account* 계정에 해당하고 * **$AccountHandles[1]**_ 은 _WindowsAccount* 계정에 해당하고 * **$AccountHandles[2]**_ 는 _LinuxAccount* 계정에 해당함

## <a name="create-a-replication-policy"></a>복제 정책 만들기

이 단계에서는 두 가지 복제 정책이 만들어집니다. VMware 가상 머신을 Azure에 복제하는 정책 하나와 Azure에서 실행되는 장애 조치(failover)된 가상 시스템을 온-프레미스 VMware 사이트로 복제하는 또 다른 정책입니다.

> [!NOTE]
> 대부분의 Azure Site Recovery 작업은 비동기적으로 실행됩니다. 작업을 시작하면 Azure Site Recovery 작업이 제출되고 작업 추적 개체가 반환됩니다. 이 작업 추적 개체는 작업 상태를 모니터링하는 데 사용될 수 있습니다.

1. *ReplicationPolicy* 라는 복제 정책을 만들어서 지정된 속성으로 VMware 가상 머신을 Azure에 복제합니다.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

   # Track Job status to check for completion
   while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){
           sleep 10;
           $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
   }

   #Display job status
   $Job_PolicyCreate
   ```
   ```
   Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
   ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                      9-479f-430d-b76b-6bc7eb2d0b3e
   Type             :
   JobType          : AddProtectionProfile
   DisplayName      : Create replication policy
   ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
   State            : Succeeded
   StateDescription : Completed
   StartTime        : 11/24/2017 2:49:24 AM
   EndTime          : 11/24/2017 2:49:23 AM
   TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
   TargetObjectType : ProtectionProfile
   TargetObjectName : ReplicationPolicy
   AllowedActions   :
   Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
   Errors           : {}
   ```

2. Azure에서 온-프레미스 VMware 사이트로의 장애 복구(failback)에 사용할 복제 정책을 만듭니다.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   *$Job_FailbackPolicyCreate* 의 작업 세부 정보를 사용하여 작업이 완료될 때까지 추적합니다.

   * 보호 컨테이너 매핑을 만들어서 복제 정책을 구성 서버와 매핑합니다.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines
      running in Azure, to the primary VMware site.) the protection container corresponding to the
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>vCenter Server 추가 및 VM 검색

vCenter Server를 IP 주소 또는 호스트 이름으로 추가합니다. **-port** 매개 변수는 연결 대상이 되는 vCenter Server의 포트를 지정하고 **-Name** 매개 변수는 vCenter Server에 사용할 이름을 지정하며 **-Account** 매개 변수는 vCenter Server에서 관리하는 가상 머신을 검색하는 데 사용할 구성 서버의 계정 핸들을 지정합니다.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) {
        sleep 30;
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts-for-replication"></a>복제에 사용할 스토리지 계정 만들기

**관리 디스크를 쓰려면 [Powershell Az.RecoveryServices 모듈 2.0.0](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview) 이상을 사용합니다.** 로그 스토리지 계정 만들기만 필요합니다. 임시 로그를 저장하는 데에만 사용되므로 표준 계정 형식 및 LRS 중복성을 사용하는 것이 좋습니다. 스토리지 계정을 자격 증명 모음과 동일한 Azure 지역에 만들어야 합니다.

2\.0.0보다 오래된 Az.RecoveryServices 모듈의 버전을 사용하는 경우 다음 단계를 사용하여 스토리지 계정을 만듭니다. 이 스토리지 계정은 나중에 가상 머신을 복제하는 데 사용됩니다. 스토리지 계정을 자격 증명 모음과 동일한 Azure 지역에 만들어야 합니다. 복제에 기존 스토리지 계정을 사용하려는 경우 이 단계를 건너뛸 수 있습니다.

> [!NOTE]
> 온-프레미스 가상 머신을 Premium Storage 계정에 복제하는 동안 표준 스토리지 계정을 추가로 지정해야 합니다(로그 스토리지 계정). 로그 스토리지 계정에는 Premium Storage 대상에 로그가 적용될 때까지 복제 로그가 중간 스토리지로 보관됩니다.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware VM 복제

vCenter Server에서 가상 머신을 검색하는 데 15~20분 정도 걸립니다. 검색이 되면 검색된 각각의 가상 머신에 대해 보호 가능한 항목 개체가 Azure Site Recovery에 만들어집니다. 이 단계에서는 검색된 가상 머신 중 3개가 이전 단계에서 만든 Azure Storage 계정에 복제됩니다.

검색된 가상 머신을 보호하려면 다음 세부 정보가 필요합니다.

* 복제할 보호 가능한 항목.
* 가상 머신을 복제할 스토리지 계정(스토리지 계정에 복제하는 경우에만 해당). 
* 프리미엄 스토리지 계정 또는 관리 디스크에 가상 머신을 보호하기 위해 로그 스토리지가 필요합니다.
* 복제에 사용할 프로세스 서버. 사용 가능한 프로세스 서버 목록이 검색되어 * **$ProcessServers[0]** _  _(ScaleOut-ProcessServer)* 및 * **$ProcessServers[1]**_ _(ConfigurationServer)* 변수에 저장됩니다.
* 모바일 서비스 소프트웨어를 컴퓨터에 강제 설치하는 데 사용할 계정. 사용 가능한 계정 목록은 검색되어 ***$AccountHandles*** 변수에 저장됩니다.
* 복제에 사용되는 복제 정책에 대한 보호 컨테이너 매핑.
* 장애 조치(failover)시 가상 머신이 만들어져야 하는 리소스 그룹.
* 선택적으로 장애 조치(failover)된 가상 머신이 연결되어야 하는 Azure 가상 네트워크 및 서브넷.

이제 이 테이블에 지정된 설정을 사용하여 다음 가상 머신을 복제합니다.


|가상 머신  |프로세스 서버        |스토리지 계정              |로그 스토리지 계정  |정책           |모바일 서비스 설치를 위한 계정|대상 리소스 그룹  | 대상 가상 네트워크  |대상 서브넷  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer   |해당 없음| logstorageaccount1                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| 해당 없음                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

복제를 사용하도록 설정하는 작업이 완료되면 가상 머신에 대해 초기 복제가 시작됩니다. 초기 복제는 복제할 데이터의 양과 복제에 사용할 수 있는 대역폭에 따라 다소 시간이 걸릴 수 있습니다. 초기 복제가 완료되면 가상 머신이 보호된 상태로 전환됩니다. 가상 머신이 보호된 상태가 되면 가상 머신에 대한 테스트 장애 조치(failover)를 수행하고 복구 계획 등에 추가할 수 있습니다.

가상 머신의 복제 상황 및 복제 상태는 Get-ASRReplicationProtectedItem cmdlet을 사용하여 확인할 수 있습니다.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>장애 조치(failover) 설정 구성

보호된 컴퓨터에 대한 장애 조치(failover) 설정은 Set-ASRReplicationProtectedItem cmdlet을 사용하여 업데이트할 수 있습니다. 이 cmdlet을 통해 업데이트할 수 있는 설정은 다음과 같습니다.
* 장애 조치(failover) 시 만들어지는 가상 머신의 이름
* 장애 조치(failover) 시 만들어지는 가상 머신의 VM 크기
* 장애 조치(failover) 시 가상 머신의 NIC를 연결해야 하는 Azure 가상 네트워크 및 서브넷
* 관리 디스크에 장애 조치(failover)
* Azure 하이브리드 사용 혜택 적용
* 장애 조치(failover) 시 가상 머신에 할당될 대상 가상 네트워크의 고정 IP 주소를 할당합니다.

이 예제에서는 가상 머신 *Win2K12VM1* 에 대한 장애 조치(failover) 시 만들어지는 가상 머신의 VM 크기를 업데이트하고 장애 조치(failover) 시 가상 머신이 관리 디스크를 사용하도록 지정합니다.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="run-a-test-failover"></a>테스트 장애 조치(failover) 실행

1. 다음과 같이 DR 연습(테스트 장애 조치(failover))을 실행합니다.

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. 테스트 장애 조치(failover) 작업이 완료되면 이름에 *"-Test"* 접미사가 붙은 가상 머신(이 경우 Win2K12VM1-Test)이 Azure에 만들어집니다.
3. 이제 테스트 장애 조치(failover)된 가상 머신에 연결하여 테스트 장애 조치(failover)의 유효성을 검사할 수 있습니다.
4. Start-ASRTestFailoverCleanupJob cmdlet을 사용하여 테스트 장애 조치(failover)를 정리합니다. 이 작업은 테스트 장애 조치(failover) 작업의 일환으로 만들어진 가상 머신을 삭제합니다.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Azure로 장애 조치(failover)

이 단계에서는 가상 머신 Win2K12VM1을 특정 복구 지점으로 장애 조치(failover)합니다.

1. 장애 조치(failover)에 사용할 복구 지점 목록을 가져옵니다.
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. 장애 조치(failover)가 완료되면 장애 조치(failover) 작업을 커밋하고 Azure에서 온-프레미스 VMware 사이트로 역방향 복제를 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Site Recovery PowerShell 참조](/powershell/module/Az.RecoveryServices)를 사용하여 더 많은 작업을 자동화하는 방법에 대해 알아봅니다.
