---
title: Azure PowerShell 및 Azure Site Recovery를 사용 하는 Azure Vm에 대 한 재해 복구
description: Azure PowerShell을 사용하여 Azure Site Recovery와 함께 Azure Virtual Machines에 대한 재해 복구를 설정하는 방법을 알아봅니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212275"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Virtual Machines에 대한 재해 복구 설정

이 문서에서는 Azure PowerShell를 사용 하 여 Azure 가상 머신에 대 한 재해 복구를 설정 하 고 테스트 하는 방법을 확인 합니다.

다음 방법을 알아봅니다.

> [!div class="checklist"]
> - Recovery Services 자격 증명 모음을 만듭니다.
> - PowerShell 세션에 대한 자격 증명 모음 컨텍스트를 설정합니다.
> - Azure Virtual Machines 복제를 시작하려면 자격 증명 모음을 준비합니다.
> - 네트워크 매핑을 만듭니다.
> - 가상 머신을 복제할 대상 스토리지 계정을 만듭니다.
> - 재해 복구에 대한 복구 지역에 Azure Virtual Machines를 복제합니다.
> - 테스트 장애 조치(failover), 유효성 검사 및 테스트 장애 조치(failover) 정리를 수행합니다.
> - 복구 지역으로 장애 조치 (failover) 합니다.

> [!NOTE]
> 포털을 통해 사용할 수 있는 일부 시나리오 기능은 Azure PowerShell을 통해 사용할 수 있습니다. 현재 Azure PowerShell을 통해 지원되지 않는 일부 시나리오 기능은 다음과 같습니다.
> - 가상 머신의 모든 디스크는 가상 머신의 각 디스크를 명시적으로 지정할 필요 없이 복제되어야 함을 지정하는 기능입니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음을 수행합니다.
- [시나리오 아키텍처 및 구성 요소](azure-to-azure-architecture.md)를 이해해야 합니다.
- 모든 구성 요소에 대한 [지원 요구 사항](azure-to-azure-support-matrix.md)을 검토합니다.
- Azure PowerShell `Az` 모듈이 있습니다. Azure PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

`Connect-AzAccount` cmdlet을 사용 하 여 Azure 구독에 로그인 합니다.

```azurepowershell
Connect-AzAccount
```

Azure 구독을 선택합니다. `Get-AzSubscription` cmdlet을 사용 하 여 액세스할 수 있는 Azure 구독 목록을 가져올 수 있습니다. `Set-AzContext` cmdlet을 사용 하 여 작업할 Azure 구독을 선택 합니다.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>복제할 가상 컴퓨터의 세부 정보를 가져옵니다.

이 문서에서는 미국 동부 지역에 있는 가상 머신을 복제 하 여 미국 서 부 2 지역에서 복구 합니다. 복제 되는 가상 컴퓨터에는 OS 디스크와 단일 데이터 디스크가 있습니다. 예제에서 사용 되는 가상 컴퓨터의 이름은 `AzureDemoVM`입니다.

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

가상 컴퓨터의 디스크에 대 한 디스크 세부 정보를 가져옵니다. 디스크 세부 정보는 나중에 가상 머신에 대한 복제를 시작할 때 사용됩니다.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음 만들기

Recovery Services 자격 증명 모음을 만들 리소스 그룹을 만듭니다.

> [!IMPORTANT]
> * Recovery Services 자격 증명 모음 및 보호 중인 가상 머신은 서로 다른 Azure 위치에 있어야 합니다.
> * Recovery Services 자격 증명 모음의 리소스 그룹 및 보호 중인 가상 머신은 서로 다른 Azure 위치에 있어야 합니다.
> * Recovery Services 자격 증명 모음 및 이에 속한 리소스 그룹은 동일한 Azure 위치에 있을 수 있습니다.

이 문서의 예제에서는 보호 중인 가상 머신이 미국 동부 지역에 있습니다. 재해 복구를 위해 선택한 복구 지역은 미국 서부 2 지역입니다. Recovery services 자격 증명 모음 및 자격 증명 모음의 리소스 그룹은 둘 다 복구 지역 (미국 서 부 2)에 있습니다.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Recovery Services 자격 증명 모음을 만듭니다. 이 예제에서는 `a2aDemoRecoveryVault` 이라는 Recovery Services 자격 증명 모음이 미국 서 부 2 지역에 생성 됩니다.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>자격 증명 모음 컨텍스트 설정

PowerShell 세션에 사용할 자격 증명 모음 컨텍스트를 설정합니다. 자격 증명 모음 컨텍스트를 설정한 후에는 PowerShell 세션의 Azure Site Recovery 작업이 선택한 자격 증명 모음의 컨텍스트에서 수행 됩니다.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Azure-Azure로 마이그레이션하는 경우 자격 증명 모음 컨텍스트를 새로 만든 자격 증명 모음으로 설정할 수 있습니다.

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Azure Virtual Machines 복제를 시작하려면 자격 증명 모음을 준비합니다.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>기본(원본) 지역을 나타내는 Site Recovery 패브릭 개체 만들기

자격 증명 모음에 있는 패브릭 개체는 Azure 지역을 나타냅니다. 기본 패브릭 개체는 자격 증명 모음에 보호 중인 가상 머신이 속한 Azure 지역을 나타내기 위해 작성됩니다. 이 문서의 예제에서는 보호 중인 가상 머신이 미국 동부 지역에 있습니다.

- 패브릭 개체는 지역당 하나만 만들 수 있습니다.
- 이전에 Azure Portal에서 VM에 대해 Site Recovery 복제를 사용하도록 설정한 경우 Site Recovery에서 패브릭 개체를 자동으로 만듭니다. 특정 지역의 패브릭 개체가 있으면 새 개체를 만들 수 없습니다.

시작 하기 전에 Site Recovery 작업이 비동기적으로 실행 되는 것을 이해 해야 합니다. 작업을 시작하면 Azure Site Recovery 작업이 제출되고 작업 추적 개체가 반환됩니다. 작업 추적 개체를 사용 하 여 작업의 최신 상태 (`Get-AzRecoveryServicesAsrJob`)를 가져오고 작업 상태를 모니터링할 수 있습니다.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

여러 Azure 지역의 가상 머신이 동일한 자격 증명 모음에 대해 보호되는 경우 각 원본 Azure 지역에 대한 하나의 패브릭 개체를 만듭니다.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>복구 지역을 나타내는 Site Recovery 패브릭 개체를 만듭니다.

복구 패브릭 개체는 복구 Azure 위치를 나타냅니다. 장애 조치 (failover)가 있는 경우 가상 머신이 복제 되 고 복구 패브릭에 의해 표시 되는 복구 지역으로 복구 됩니다. 이 예에서 사용된 복구 Azure 지역은 미국 서부 2입니다.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>기본 패브릭에서 Site Recovery 보호 컨테이너를 만듭니다.

보호 컨테이너는 패브릭 내에서 복제된 항목을 그룹화하는 데 사용하는 컨테이너입니다.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>복구 패브릭에서 Site Recovery 보호 컨테이너를 만듭니다.

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>복제 정책 만들기

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>기본 및 복구 보호 컨테이너 간의 보호 컨테이너 매핑을 만듭니다.

보호 컨테이너 매핑은 복구 보호 컨테이너 및 복제 정책을 사용하여 기본 보호 컨테이너를 매핑합니다. 보호 컨테이너 쌍 간에 가상 머신을 복제하는 데 사용할 각 복제 정책에 대한 매핑을 하나 만듭니다.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>장애 복구(failback)에 대한 보호 컨테이너 매핑을 만듭니다(장애 조치(failover) 후 역방향 복제).

장애 조치 (failover) 후 장애 조치 (failover) 된 가상 머신을 원래 Azure 지역으로 다시 가져올 준비가 되 면 장애 복구 (failback)를 수행 합니다. 장애 복구 (failback)를 수행 하려면 장애 조치 (failover) 된 가상 머신이 장애 조치 (failover) 된 지역에서 원래 지역으로 역방향 복제 됩니다. 역방향 복제의 경우 원래 지역과 복구 지역의 역할이 바뀝니다. 이제 원래 지역이 새 복구 지역이 되고 원래 복구 지역이었던 곳이 기본 지역이 됩니다. 역방향 복제에 대한 보호 컨테이너 매핑은 원래 지역과 복구 지역의 전환된 역할을 나타냅니다.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>캐시 저장소 계정 및 대상 저장소 계정 만들기

캐시 스토리지 계정은 복제되는 가상 머신과 같은 Azure 지역에 있는 표준 스토리지 계정입니다. 캐시 스토리지 계정은 변경 내용을 복구 Azure 지역으로 이동하기 전에 변경 내용 복제를 일시적으로 보류하는 데 사용됩니다. 필요 하지는 않지만 가상 컴퓨터의 다른 디스크에 대해 서로 다른 캐시 저장소 계정을 지정할 수 있습니다.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

**관리 디스크를 사용 하지 않는**가상 컴퓨터의 경우 대상 저장소 계정은 가상 컴퓨터의 디스크를 복제 하는 복구 지역의 저장소 계정입니다. 대상 스토리지 계정은 표준 스토리지 계정 또는 Premium Storage 계정 중 하나일 수 있습니다. 디스크의 데이터 변경 률 (IO 쓰기 빈도)에 따라 필요한 저장소 계정 종류와 저장소 형식에 대해 지원 되는 Azure Site Recovery 변동 제한을 선택 합니다.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>네트워크 매핑 만들기

네트워크 매핑은 기본 지역의 가상 네트워크를 복구 지역의 가상 네트워크에 연결합니다. 네트워크 매핑은 복구 지역에서 Azure virtual network를 지정 하며, 기본 가상 네트워크의 가상 머신은로 장애 조치 (failover) 되어야 합니다. 하나의 Azure Virtual Network는 복구 지역의 단일 Azure Virtual Network에만 매핑할 수 있습니다.

- 장애 조치할 복구 지역에 Azure 가상 네트워크를 만듭니다.

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- 주 가상 네트워크를 검색 합니다. 가상 머신이 연결 된 VNet:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- 주 가상 네트워크와 복구 가상 네트워크 간의 네트워크 매핑을 만듭니다.

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- 역방향 방향에 대 한 네트워크 매핑 만들기 (장애 복구):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure Virtual Machine 복제

**관리 디스크**를 사용하여 Azure Virtual Machine을 복제합니다.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

**비관리 디스크**를 사용하여 Azure Virtual Machine을 복제합니다.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

복제 시작 작업이 성공하면 가상 머신 데이터가 복구 지역으로 복제됩니다.

복제 프로세스는 처음에 복구 영역에 있는 가상 머신의 복제 디스크의 복사본을 시드하면서 시작됩니다. 이 단계를 초기 복제 단계라고 합니다.

초기 복제가 완료 되 면 복제는 차등 동기화 단계로 이동 합니다. 이 시점에서 가상 머신이 보호되고 테스트 장애 조치(failover) 작업을 수행할 수 있습니다. 초기 복제가 완료 된 후 가상 머신을 나타내는 복제 된 항목의 복제 상태가 **보호** 된 상태로 전환 됩니다.

해당하는 복제 보호된 항목의 세부 정보를 가져와서 복제 상태 및 가상 머신의 복제 상태를 모니터링합니다.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>테스트 장애 조치 (failover), 유효성 검사 및 테스트 장애 조치 (failover) 수행

가상 컴퓨터에 대 한 복제가 보호 된 상태에 도달한 후 가상 컴퓨터 (가상 컴퓨터의 복제 보호 된 항목)에서 테스트 장애 조치 (failover) 작업을 수행할 수 있습니다.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

테스트 장애 조치 (failover)를 수행 합니다.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

테스트 장애 조치(failover) 작업이 완료될 때까지 기다립니다.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

테스트 장애 조치 (failover) 작업이 성공적으로 완료 되 면 테스트 장애 조치 (failover) 된 가상 머신에 연결 하 고 테스트 장애 조치 (failover)의 유효성을 검사할 수 있습니다

테스트 장애 조치(failover)된 가상 머신에서 테스트가 완료되면 테스트 장애 조치(failover) 정리 작업을 시작하여 테스트 복사본을 정리합니다. 이 작업은 테스트 장애 조치(failover)로 만들어진 가상 머신의 테스트 복사본을 삭제합니다.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Azure로 장애 조치(failover)

가상 컴퓨터를 특정 복구 지점으로 장애 조치 (failover) 합니다.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

장애 조치 (failover) 작업이 성공적으로 완료 되 면 장애 조치 (failover) 작업을 커밋할 수 있습니다.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>다시 보호 및 원본 영역으로 장애 복구

장애 조치 (failover) 후 원래 지역으로 돌아갈 준비가 되 면 `Update-AzRecoveryServicesAsrProtectionDirection` cmdlet을 사용 하 여 복제 보호 된 항목에 대 한 역방향 복제를 시작 합니다.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

다시 보호 완료 되 면 반대 방향으로 장애 조치 (failover) 하 고 미국 동부에서 미국 동부로 장애 조치 (failover) 하 고 원본 지역으로 장애 복구 (failback) 할 수 있습니다.

## <a name="disable-replication"></a>복제 사용 안 함

`Remove-AzRecoveryServicesAsrReplicationProtectedItem` cmdlet을 사용 하 여 복제를 사용 하지 않도록 설정할 수 있습니다.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>다음 단계

Powershell을 사용 하 여 복구 계획을 만들고 장애 조치 (failover)를 테스트 하는 등의 다른 작업을 수행 하는 방법에 대 한 자세한 내용은 [Azure Site Recovery powershell 참조](/powershell/module/az.RecoveryServices) 를 확인 하세요.
