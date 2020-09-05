---
title: Azure PowerShell를 통해 Azure Stack Edge GPU 장치에 Vm 배포
description: Azure PowerShell를 사용 하 여 Azure Stack Edge GPU 장치에서 Vm (가상 머신)을 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: d5210a3788f7bb054492c2d83c595c26fa3c4f42
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265714"
---
# <a name="deploy-vms-on-your-azure-stack-edge-gpu-device-via-azure-powershell"></a>Azure PowerShell를 통해 Azure Stack Edge GPU 장치에 Vm 배포

<!--[!INCLUDE [azure-stack-edge-gateway-deploy-vm-overview](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-overview.md)]-->

이 자습서에서는 Azure PowerShell를 사용 하 여 Azure Stack Edge 장치에서 VM을 만들고 관리 하는 방법을 설명 합니다.

## <a name="vm-deployment-workflow"></a>VM 배포 워크플로

배포 워크플로는 다음 다이어그램에 설명 되어 있습니다.

![VM 배포 워크플로](media/azure-stack-edge-j-series-deploy-virtual-machine-powershell/vm-workflow_r.svg)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [azure-stack-edge-gateway-deploy-vm-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]



## <a name="query-for-built-in-subscription-on-the-device"></a>장치에서 기본 제공 구독의 쿼리

Azure Resource Manager의 경우 사용자가 볼 수 있는 단일 고정 구독만 지원 됩니다. 이 구독은 장치 마다 고유 하며이 구독 이름 또는 구독 ID는 변경할 수 없습니다.

이 구독에는 VM을 만드는 데 필요한 모든 리소스가 포함 됩니다. 

> [!IMPORTANT]
> 이 구독은 Azure 구독과 연결 되어 있지 않으며 장치에 로컬로 상주 합니다.

이 구독은 Vm을 배포 하는 데 사용 됩니다.

1.  이 구독을 나열 하려면 다음을 입력 합니다.

    ```powershell
    Get-AzureRmSubscription
    ```
    
    샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\windows\system32> Get-AzureRmSubscription
    
    Name                 Id                 TenantId          State
    ----                 --                --------           -----
    Default Provider Subscription A4257FDE-B946-4E01-ADE7-674760B8D1A3 c0257de7-538f-415c-993a-1b87a031879d Enabled
    
    PS C:\windows\system32>
    ```
        
3.  장치에서 실행 되는 등록 된 리소스 공급자의 목록을 가져옵니다. 일반적으로이 목록에는 Compute, Network 및 Storage가 포함 됩니다.

    ```powershell
    Get-AzureRMResourceProvider
    ```

    > [!NOTE]
    > 리소스 공급자는 미리 등록 되어 있으며 수정 하거나 변경할 수 없습니다.
    
    샘플 출력은 다음과 같습니다.

    ```powershell
    Get-AzureRmResourceProvider
    ProviderNamespace : Microsoft.Compute
    RegistrationState : Registered
    ResourceTypes     : {virtualMachines, virtualMachines/extensions, locations, operations...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Network
    RegistrationState : Registered
    ResourceTypes     : {operations, locations, locations/operations, locations/usages...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Resources
    RegistrationState : Registered
    ResourceTypes     : {tenants, locations, providers, checkresourcename...}
    Locations         : {DBELocal}
    ZoneMappings      :
    
    ProviderNamespace : Microsoft.Storage
    RegistrationState : Registered
    ResourceTypes     : {storageaccounts, storageAccounts/blobServices, storageAccounts/tableServices,
                        storageAccounts/queueServices...}
    Locations         : {DBELocal}
    ZoneMappings      :
    ```
    
## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 저장소 계정, 디스크, 관리 디스크와 같은 Azure 리소스가 배포 및 관리 되는 논리적 컨테이너입니다.

> [!IMPORTANT]
> 모든 리소스는 장치와 동일한 위치에 만들어지고 위치는 **Dbelocal**로 설정 됩니다.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

샘플 출력은 다음과 같습니다.

```powershell
New-AzureRmResourceGroup -Name rg191113014333 -Location DBELocal 
Successfully created Resource Group:rg191113014333
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

이전 단계에서 만든 리소스 그룹을 사용 하 여 새 저장소 계정을 만듭니다. VM에 대 한 가상 디스크 이미지를 업로드 하는 데 사용 되는 **로컬 저장소 계정** 입니다.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> 로컬 중복 저장소 (Standard_LRS 또는 Premium_LRS)와 같은 로컬 저장소 계정만 Azure Resource Manager를 통해 만들 수 있습니다. 계층화 된 저장소 계정을 만들려면 [Azure Stack Edge에서 저장소 계정에 추가, 연결](azure-stack-edge-j-series-deploy-add-storage-accounts.md)의 단계를 참조 하세요.

샘플 출력은 다음과 같습니다.

```powershell
New-AzureRmStorageAccount -Name sa191113014333  -ResourceGroupName rg191113014333 -SkuName Standard_LRS -Location DBELocal

ResourceGroupName      : rg191113014333
StorageAccountName     : sa191113014333
Id                     : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Microsoft.Storage/storageaccounts/sa191113014333
Location               : DBELocal
Sku                    : Microsoft.Azure.Management.Storage.Models.Sku
Kind                   : Storage
Encryption             : Microsoft.Azure.Management.Storage.Models.Encryption
AccessTier             :
CreationTime           : 11/13/2019 9:43:49 PM
CustomDomain           :
Identity               :
LastGeoFailoverTime    :
PrimaryEndpoints       : Microsoft.Azure.Management.Storage.Models.Endpoints
PrimaryLocation        : DBELocal
ProvisioningState      : Succeeded
SecondaryEndpoints     :
SecondaryLocation      :
StatusOfPrimary        : Available
StatusOfSecondary      :
Tags                   :
EnableHttpsTrafficOnly : False
NetworkRuleSet         :
Context                : Microsoft.WindowsAzure.Commands.Common.Storage.LazyAzureStorageContext
ExtendedProperties     : {}
```

저장소 계정 키를 가져오려면 `Get-AzureRmStorageAccountKey` 명령을 실행 합니다. 이 명령의 샘플 출력은 다음과 같습니다.

```powershell
PS C:\Users\Administrator> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: my-resource-ase
Name:myasestoracct

KeyName Value
------- -----
key1 /IjVJN+sSf7FMKiiPLlDm8mc9P4wtcmhhbnCa7...
key2 gd34TcaDzDgsY9JtDNMUgLDOItUU0Qur3CBo6Q...
```

## <a name="add-blob-uri-to-hosts-file"></a>호스트 파일에 blob URI 추가

[끝점 이름 확인에 대 한 호스트 파일 수정](azure-stack-edge-j-series-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution)섹션에서 blob 저장소에 연결 하는 데 사용 하는 클라이언트의 호스트 파일에 blob URI를 이미 추가 했습니다. Blob URI에 대 한 항목입니다.

\<Azure consistent network services VIP \>\<storage name\>. blob. \<appliance name\> .\<dnsdomain\>


## <a name="install-certificates"></a>인증서 설치

*Https*를 사용 하는 경우 장치에 적절 한 인증서를 설치 해야 합니다. 이 경우 blob 끝점 인증서를 설치 합니다. 자세한 내용은 [인증서 관리](azure-stack-edge-j-series-manage-certificates.md)에서 인증서를 만들고 업로드 하는 방법을 참조 하세요.

## <a name="upload-a-vhd"></a>VHD 업로드

이전 단계에서 만든 로컬 저장소 계정의 페이지 blob에 사용할 디스크 이미지를 복사 합니다. [AzCopy](../storage/common/storage-use-azcopy-v10.md) 와 같은 도구를 사용 하 여 이전 단계에서 만든 저장소 계정에 VHD를 업로드할 수 있습니다. 

AzCopy를 사용 하기 전에 Azure Stack Edge 장치에서 사용 하는 blob storage REST API 버전에 사용할 수 있도록 [AzCopy가 올바르게 구성 되어](#configure-azcopy) 있는지 확인 합니다.

```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> [!NOTE]
> `BlobType`VHD에서 관리 디스크를 만들 수 있도록 페이지로 설정 합니다. `BlobType`AzCopy를 사용 하 여 계층화 된 저장소 계정에 쓸 때 차단으로 설정 합니다.

Marketplace에서 디스크 이미지를 다운로드할 수 있습니다. 자세한 단계는 [Azure marketplace에서 가상 디스크 이미지 가져오기](azure-stack-edge-j-series-create-virtual-machine-image.md)로 이동 합니다.

AzCopy 7.3를 사용 하는 샘플 출력은 다음과 같습니다. 이 명령에 대 한 자세한 내용은 AzCopy를 [사용 하 여 저장소 계정에 VHD 파일 업로드](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md)로 이동 하세요.


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```


## <a name="create-managed-disks-from-the-vhd"></a>VHD에서 관리 디스크 만들기

업로드 된 VHD에서 관리 디스크를 만듭니다.

```powershell
$DiskConfig = New-AzureRmDiskConfig -Location DBELocal -CreateOption Import -SourceUri "Source URL for your VHD"
```
샘플 출력은 다음과 같습니다. 

$DiskConfig = New-azurermdiskconfig-Location DBELocal-CreateOption Import – SourceUri http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages/ubuntu13.vhd 

```powershell
New-AzureRMDisk -ResourceGroupName <Resource group name> -DiskName <Disk name> -Disk $DiskConfig
```

샘플 출력은 다음과 같습니다. 이 cmdlet에 대 한 자세한 내용은 [new-azurermdisk](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermdisk?view=azurermps-6.13.0)를 참조 하세요.

```powershell
Tags               :
New-AzureRmDisk -ResourceGroupName rg191113014333 -DiskName ld191113014333 -Disk $DiskConfig

ResourceGroupName  : rg191113014333
ManagedBy          :
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              :
TimeCreated        : 11/13/2019 1:49:07 PM
OsType             :
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 30
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micros
                     oft.Compute/disks/ld191113014333
Name               : ld191113014333
Type               : Microsoft.Compute/disks
Location           : DBELocal
Tags               : {}
```

## <a name="create-a-vm-image-from-the-image-managed-disk"></a>이미지 관리 디스크에서 VM 이미지 만들기

다음 명령을 사용 하 여 관리 디스크에서 VM 이미지를 만듭니다. 내의 값을 \< \> 선택한 이름으로 바꿉니다.

```powershell
$imageConfig = New-AzureRmImageConfig -Location DBELocal
$ManagedDiskId = (Get-AzureRmDisk -Name <Disk name> -ResourceGroupName <Resource group name>).Id
Set-AzureRmImageOsDisk -Image $imageConfig -OsType '<OS type>' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId 

The supported OS types are Linux and Windows.

For OS Type=Linux, for example:
Set-AzureRmImageOsDisk -Image $imageConfig -OsType 'Linux' -OsState 'Generalized' -DiskSizeGB <Disk size> -ManagedDiskId $ManagedDiskId
New-AzureRmImage -Image $imageConfig -ImageName <Image name>  -ResourceGroupName <Resource group name>
```

샘플 출력은 다음과 같습니다. 이 cmdlet에 대 한 자세한 내용은 [new-azurermimage](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermimage?view=azurermps-6.13.0)를 참조 하세요.

```powershell
New-AzureRmImage -Image Microsoft.Azure.Commands.Compute.Automation.Models.PSImage -ImageName ig191113014333  -ResourceGroupName rg191113014333
ResourceGroupName    : rg191113014333
SourceVirtualMachine :
StorageProfile       : Microsoft.Azure.Management.Compute.Models.ImageStorageProfile
ProvisioningState    : Succeeded
Id                   : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg191113014333/providers/Micr
                       osoft.Compute/images/ig191113014333
Name                 : ig191113014333
Type                 : Microsoft.Compute/images
Location             : dbelocal
Tags                 : {}
```

## <a name="create-vm-with-previously-created-resources"></a>이전에 만든 리소스를 사용 하 여 VM 만들기

VM을 만들고 배포 하기 전에 가상 네트워크를 하나 만들고 가상 네트워크 인터페이스를 연결 해야 합니다.

> [!IMPORTANT]
> 가상 네트워크 및 가상 네트워크 인터페이스를 만드는 동안 다음 규칙이 적용 됩니다.
> - 하나의 Vnet만 만들 수 있으며 (리소스 그룹의 경우에도) 주소 공간을 기준으로 논리 네트워크와 정확히 일치 해야 합니다.
> -   Vnet에는 서브넷이 하나만 허용 됩니다. 서브넷은 Vnet과 정확히 동일한 주소 공간 이어야 합니다.
> -   Vnic를 만드는 동안에는 정적 할당 메서드만 허용 되며 사용자는 개인 IP 주소를 제공 해야 합니다.

 
**Vnet 만들기**

```powershell
$subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name <Subnet name> -AddressPrefix <Address Prefix>
$aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName <Resource group name> -Name <Vnet name> -Location DBELocal -AddressPrefix <Address prefix> -Subnet $subNetId
```

**Vnet 서브넷 ID를 사용 하 여 Vnic 만들기**

```powershell
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <IP config Name> -SubnetId $aRmVN.Subnets[0].Id -PrivateIpAddress <Private IP>
$Nic = New-AzureRmNetworkInterface -Name <Nic name> -ResourceGroupName <Resource group name> -Location DBELocal -IpConfiguration $ipConfig
```

이러한 명령의 샘플 출력은 다음과 같습니다.

```powershell
PS C:\Users\Administrator> $subNetId=New-AzureRmVirtualNetworkSubnetConfig -Name my-ase-subnet -AddressPrefix "5.5.0.0/16"

PS C:\Users\Administrator> $aRmVN = New-AzureRmVirtualNetwork -ResourceGroupName Resource-my-ase -Name my-ase-virtualnetwork -Location DBELocal -AddressPrefix "5.5.0.0/16" -Subnet $subNetId
WARNING: The output object type of this cmdlet will be modified in a future release.
PS C:\Users\Administrator> $ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name my-ase-ip -SubnetId $aRmVN.Subnets[0].Id
PS C:\Users\Administrator> $Nic = New-AzureRmNetworkInterface -Name my-ase-nic -ResourceGroupName Resource-my-ase -Location DBELocal -IpConfiguration $ipConfig
WARNING: The output object type of this cmdlet will be modified in a future release.

PS C:\Users\Administrator> $Nic

PS C:\Users\Administrator> (Get-AzureRmNetworkInterface)[0]

Name                        : nic200108020444
ResourceGroupName           : rg200108020444
Location                    : dbelocal
Id                          : /subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Network/networ
                              kInterfaces/nic200108020444
Etag                        : W/"f9d1759d-4d49-42fa-8826-e218e0b1d355"
ResourceGuid                : 3851ae62-c13e-4416-9386-e21d9a2fef0f
ProvisioningState           : Succeeded
Tags                        :
VirtualMachine              : {
                                "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Compu
                              te/virtualMachines/VM200108020444"
                              }
IpConfigurations            : [
                                {
                                  "Name": "ip200108020444",
                                  "Etag": "W/\"f9d1759d-4d49-42fa-8826-e218e0b1d355\"",
                                  "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/rg200108020444/providers/Microsoft.Net
                              work/networkInterfaces/nic200108020444/ipConfigurations/ip200108020444",
                                  "PrivateIpAddress": "5.5.166.65",
                                  "PrivateIpAllocationMethod": "Static",
                                  "Subnet": {
                                    "Id": "/subscriptions/a4257fde-b946-4e01-ade7-674760b8d1a3/resourceGroups/DbeSystemRG/providers/Microsoft.Netw
                              ork/virtualNetworks/vSwitch1/subnets/subnet123",
                                    "ResourceNavigationLinks": [],
                                    "ServiceEndpoints": []
                                  },
                                  "ProvisioningState": "Succeeded",
                                  "PrivateIpAddressVersion": "IPv4",
                                  "LoadBalancerBackendAddressPools": [],
                                  "LoadBalancerInboundNatRules": [],
                                  "Primary": true,
                                  "ApplicationGatewayBackendAddressPools": [],
                                  "ApplicationSecurityGroups": []
                                }
                              ]
DnsSettings                 : {
                                "DnsServers": [],
                                "AppliedDnsServers": []
                              }
EnableIPForwarding          : False
EnableAcceleratedNetworking : False
NetworkSecurityGroup        : null
Primary                     : True
MacAddress                  : 00155D18E432                :
```

필요에 따라 VM에 대 한 Vnic를 만들 때 공용 IP를 전달할 수 있습니다. 이 인스턴스에서 공용 IP는 개인 IP를 반환 합니다. 

```powershell
New-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <ResourceGroupName> -AllocationMethod Static -Location DBELocal
$publicIP = (Get-AzureRmPublicIPAddress -Name <Public IP> -ResourceGroupName <Resource group name>).Id
$ipConfig = New-AzureRmNetworkInterfaceIpConfig -Name <ConfigName> -PublicIpAddressId $publicIP -SubnetId $subNetId
```


**VM 만들기**

이제 VM 이미지를 사용 하 여 VM을 만들고 이전에 만든 가상 네트워크에 연결할 수 있습니다.

```powershell
$pass = ConvertTo-SecureString "<Password>" -AsPlainText -Force;
$cred = New-Object System.Management.Automation.PSCredential("<Enter username>", $pass)

You will use this username, password to login to the VM, once it is created and powered up.

$VirtualMachine = New-AzureRmVMConfig -VMName <VM name> -VMSize "Standard_D1_v2"

$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -<OS type> -ComputerName <Your computer Name> -Credential $cred

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name <OS Disk Name> -Caching "ReadWrite" -CreateOption "FromImage" -Linux -StorageAccountType StandardLRS

$nicID = (Get-AzureRmNetworkInterface -Name <nic name> -ResourceGroupName <Resource Group Name>).Id

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nicID

$image = (Get-AzureRmImage -ResourceGroupName <Resource Group Name> -ImageName $ImageName).Id

$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -Id $image

New-AzureRmVM -ResourceGroupName <Resource Group Name> -Location DBELocal -VM $VirtualMachine -Verbose
```

## <a name="connect-to-a-vm"></a>VM에 연결

VM을 만드는 동안 전달 된 개인 IP를 사용 하 여 VM에 연결 합니다.

SSH 세션을 열어 IP 주소와 연결 합니다.

`ssh -l <username> <ip address>`

메시지가 표시 되 면 VM을 만들 때 사용한 암호를 제공 합니다.

SSH 키를 제공 해야 하는 경우이 명령을 사용 합니다.

ssh-i c:/users/Administrator/. s t a/id_rsa Administrator@5.5.41.236

VM을 만들 때 공용 IP 주소를 사용한 경우 해당 IP를 사용 하 여 VM에 연결할 수 있습니다. 공용 IP를 가져오려면: 

```powershell
$publicIp = Get-AzureRmPublicIpAddress -Name <Public IP> -ResourceGroupName <Resource group name>
```
이 경우 공용 IP는 가상 네트워크 인터페이스를 만드는 동안 전달 된 개인 IP와 동일 합니다.


## <a name="manage-vm"></a>VM 관리

다음 섹션에서는 Azure Stack Edge 장치에서 만들 VM에 대 한 몇 가지 일반적인 작업에 대해 설명 합니다.

### <a name="list-vms-running-on-the-device"></a>장치에서 실행 되는 Vm 나열

Azure Stack Edge 장치에서 실행 되는 모든 Vm의 목록을 반환 하려면 다음 명령을 실행 합니다.


`Get-AzureRmVM -ResourceGroupName <String> -Name <String>`
 

### <a name="turn-on-the-vm"></a>VM 켜기

다음 cmdlet을 실행 하 여 장치에서 실행 되는 가상 컴퓨터를 설정 합니다.


`Start-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>`


이 cmdlet에 대 한 자세한 내용은 [new-azurermvm](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm?view=azurermps-6.13.0)를 참조 하세요.

### <a name="suspend-or-shut-down-the-vm"></a>VM 일시 중단 또는 종료

다음 cmdlet을 실행 하 여 장치에서 실행 되는 가상 컴퓨터를 중지 하거나 종료 합니다.


```powershell
Stop-AzureRmVM [-Name] <String> [-StayProvisioned] [-ResourceGroupName] <String>
```


이 cmdlet에 대 한 자세한 내용은 [new-azurermvm cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm?view=azurermps-6.13.0)을 참조 하세요.

### <a name="add-a-data-disk"></a>데이터 디스크 추가

VM에 대 한 워크 로드 요구 사항이 증가 하는 경우 데이터 디스크를 추가 해야 할 수 있습니다.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" -VhdUri "https://contoso.blob.core.windows.net/vhds/diskstandard03.vhd" -LUN 0 -Caching ReadOnly -DiskSizeinGB 1 -CreateOption Empty 
 
Update-AzureRmVM -ResourceGroupName "<Resource Group Name string>" -VM $VirtualMachine
```

### <a name="delete-the-vm"></a>VM 삭제

장치에서 가상 컴퓨터를 제거 하려면 다음 cmdlet을 실행 합니다.

```powershell
Remove-AzureRmVM [-Name] <String> [-ResourceGroupName] <String>
```

이 cmdlet에 대 한 자세한 내용은 [new-azurermvm cmdlet](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm?view=azurermps-6.13.0)을 참조 하세요.


## <a name="supported-vm-sizes"></a>지원되는 VM 크기

VM 크기에 따라 CPU, GPU, 메모리 등 VM에 사용할 수 있는 컴퓨팅 리소스의 양이 결정됩니다. 가상 머신은 워크로드에 맞는 VM 크기로 만들어야 합니다. 모든 컴퓨터가 동일한 하드웨어에서 실행 되는 경우에도 컴퓨터 크기는 디스크 액세스에 대해 서로 다른 제한을 가지 지만 Vm 전체에서 전체 디스크 액세스를 관리 하는 데 도움이 될 수 있습니다. 워크로드가 증가할 경우 기존 가상 머신의 크기를 조정할 수도 있습니다.

다음 표준 Dv2 시리즈 Vm은 Azure Stack Edge 장치에서 만들 수 있도록 지원 됩니다.

### <a name="dv2-series"></a>Dv2 시리즈
|Size     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | 최대 OS 디스크 처리량 (IOPS) | 최대 임시 저장소 처리량 (IOPS) | 최대 데이터 디스크/처리량 (IOPS) | 최대 NIC 수 |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4x500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>DSv2 시리즈
|Size     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | 최대 OS 디스크 처리량 (IOPS) | 최대 임시 저장소 처리량 (IOPS) | 최대 데이터 디스크/처리량 (IOPS) | 최대 NIC 수 |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Dv2 시리즈
|Size     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | 최대 OS 디스크 처리량 (IOPS) | 최대 임시 저장소 처리량 (IOPS) | 최대 데이터 디스크/처리량 (IOPS) | 최대 NIC 수 |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="dsv2-series"></a>DSv2 시리즈
|Size     |vCPU     |메모리(GiB) | 임시 스토리지(GiB)  | 최대 OS 디스크 처리량 (IOPS) | 최대 임시 저장소 처리량 (IOPS) | 최대 데이터 디스크/처리량 (IOPS) | 최대 NIC 수 |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

자세한 내용은 범용 [VM 크기에 대 한 Dv2 시리즈](../virtual-machines/dv2-dsv2-series.md#dv2-series)를 참조 하세요.

## <a name="unsupported-vm-operations-and-cmdlets"></a>지원 되지 않는 VM 작업 및 cmdlet

확장, 확장 집합, 가용성 집합, 스냅숏은 지원 되지 않습니다.

## <a name="configure-azcopy"></a>AzCopy 구성

최신 버전의 AzCopy을 설치 하는 경우 AzCopy Azure Stack Edge 장치의 blob storage REST API 버전과 일치 하는지 확인 하도록 구성 해야 합니다.

Azure Stack Edge 장치에 액세스 하는 데 사용 되는 클라이언트에서 blob storage REST API 버전과 일치 하도록 전역 변수를 설정 합니다.

### <a name="on-windows-client"></a>Windows 클라이언트 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### <a name="on-linux-client"></a>Linux 클라이언트에서

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

AzCopy에 대 한 환경 변수가 올바르게 설정 되었는지 확인 하려면 다음 단계를 수행 합니다.

1. "Azcopy env" 실행
2. `AZCOPY_DEFAULT_SERVICE_API_VERSION`매개 변수를 찾습니다. 이전 단계에서 설정한 값이 있어야 합니다.


## <a name="next-steps"></a>다음 단계

[Azure Resource Manager cmdlet](https://docs.microsoft.com/powershell/module/azurerm.resources/?view=azurermps-6.13.0)
