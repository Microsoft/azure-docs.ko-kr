---
title: SQL Server에서 Azure Premium Storage 사용 | Microsoft Docs
description: 이 문서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, Azure Virtual Machines에서 실행되는 SQL Server에서 Azure Premium Storage를 사용하기 위한 지침을 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 7ccf99d7-7cce-4e3d-bbab-21b751ab0e88
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3b3bb206286629a68c14b6444f3f88ffa0af50dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583273"
---
# <a name="use-azure-premium-storage-with-sql-server-on-virtual-machines"></a>Virtual Machines의 SQL Server에서 Azure Premium Storage 사용

## <a name="overview"></a>개요

[Azure 프리미엄 SSD](../disks-types.md)는 대기 시간이 짧고 처리량 IO가 높은 차세대 스토리지로, IaaS [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)의 SQL Server와 같이 IO를 많이 사용하는 주요 워크로드에서 매우 효율적입니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

이 문서에서는 SQL Server를 실행하는 Virtual Machine이 Premium Storage를 사용하도록 마이그레이션하기 위한 계획 및 지침을 제공합니다. 여기에는 Azure 인프라(네트워킹, 저장소) 및 게스트 Windows VM 관련 단계가 포함됩니다. [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) 의 예제에서는 PowerShell을 통해 개선된 로컬 SSD 저장소를 활용하도록 대규모 VM을 이동하는 전체 마이그레이션 방법을 보여 줍니다.

IaaS VM의 SQL Server에서 Azure Premium Storage를 활용하는 전체 프로세스를 이해해야 합니다. 다음 내용이 포함됩니다.

* Premium Storage를 사용하기 위한 필수 구성 요소 파악
* 새 배포를 위해 IaaS의 SQL Server를 Premium Storage에 배포하는 예제
* 기존 배포 환경(SQL Always On 가용성 그룹을 사용하는 배포와 독립 실행형 서버 배포)을 마이그레이션하는 예제
* 가능한 마이그레이션 방법
* 기존 Always On 구현 환경의 마이그레이션을 위한 Azure, Windows 및 SQL Server 관련 단계를 전체적으로 보여주는 예제

Azure Virtual Machines의 SQL Server에 대한 추가 배경 정보는 [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

**작성자:** Daniel Sol **기술 검토자:** Luis Carlos Vargas Herring, Sanjay Mishra, Pravin Mital, Juergen Thomas, Gonzalo Ruiz.

## <a name="prerequisites-for-premium-storage"></a>Premium Storage 사용을 위한 필수 구성 요소

Premium Storage를 사용하려면 몇 가지 필수 구성 요소를 충족해야 합니다.

### <a name="machine-size"></a>컴퓨터 크기

Premium Storage를 사용하려면 DS 시리즈 VM(Virtual Machines)을 사용해야 합니다. 이전에 클라우드 서비스에서 DS 시리즈 컴퓨터를 사용한 적이 없으면 기존 VM을 삭제하고 연결된 디스크를 보관한 다음 새 클라우드 서비스를 만들고 DS* 역할 크기로 VM을 다시 만들어야 합니다. Virtual Machine 크기에 대한 자세한 내용은 [Azure를 위한 가상 머신 및 클라우드 서비스 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="cloud-services"></a>클라우드 서비스

새 클라우드 서비스에서 VM을 만들 때는 Premium Storage를 사용하는 DS* VM만 사용할 수 있습니다. Azure에서 SQL Server Always On을 사용하는 경우 Always On 수신기는 클라우드 서비스와 연결된 Azure 내부 또는 외부 부하 분산 장치 IP 주소를 참조합니다. 이 문서에서는 이러한 시나리오에서 가용성을 유지하면서 마이그레이션을 수행하는 방법을 중점적으로 설명합니다.

> [!NOTE]
> 새 클라우드 서비스로 배포하는 첫 번째 VM이 DS* 시리즈여야 합니다.
>
>

### <a name="regional-vnets"></a>지역별 VNET

DS* VM에 대해 VM을 호스팅하는 VNET(Virtual Network)을 해당 지역용으로 구성해야 합니다. 이와 같이 VNET을 확장하면 대형 VM을 다른 클러스터에 프로비전할 수 있으며 클러스터가 서로 통신할 수 있습니다. 다음 스크린샷에서 강조 표시된 위치에는 지역 VNET이 나와 있고 첫 번째 결과에는 축소된 VNET이 나와 있습니다.

![RegionalVNET][1]

VNET으로 마이그레이션하려면 Microsoft 지원 티켓을 제출할 수 있습니다. 그러면 Microsoft에서 변경 작업을 수행합니다. 지역 VNET으로 마이그레이션을 완료하려면 네트워크 구성에서 AffinityGroup 속성을 변경합니다. 먼저 PowerShell에서 네트워크 구성을 내보낸 다음 **VirtualNetworkSite** 요소의 **AffinityGroup** 속성을 **Location** 속성으로 바꿉니다. 이때 `Location = XXXX`를 지정합니다. 여기서 `XXXX`가 Azure 지역입니다. 그런 다음 새 구성을 가져옵니다.

다음 VNET 구성을 예로 살펴보겠습니다.

```xml
<VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

이 VNET을 서유럽(West Europe)의 지역 VNET으로 이동하려면 구성을 다음과 같이 변경합니다.
```xml
<VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
<AddressSpace>
  <AddressPrefix>10.0.0.0/8</AddressPrefix>
  <AddressPrefix>172.16.0.0/12</AddressPrefix>
</AddressSpace>
<Subnets>
...
</VirtualNetworkSite>
```

### <a name="storage-accounts"></a>Storage 계정

Premium Storage용으로 구성된 새 스토리지 계정을 만들어야 합니다. Premium Storage 사용은 개별 VHD가 아니라 Storage 계정에서 설정되지만 DS* 시리즈 VM을 사용할 때는 프리미엄 및 Standard Storage 계정에서 VHD를 연결할 수 있습니다. Premium Storage 계정에 OS VHD를 배치하지 않으려는 경우 이러한 방식을 사용할 수 있습니다.

아래에 나와 있는 "Premium_LRS" **형식**을 사용하는 **New-AzureStorageAccountPowerShell** 명령은 Premium Storage 계정을 만듭니다.

```powershell
$newstorageaccountname = "danpremstor"
New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   
```

### <a name="vhds-cache-settings"></a>VHD 캐시 설정

Premium Storage 계정의 일부분인 디스크를 만들 때의 기본적인 차이점은 디스크 캐시 설정입니다. SQL Server 데이터 볼륨 디스크의 경우에는 ‘**Read Caching**’을 사용하는 것이 좋습니다. 트랜잭션 로그 볼륨의 경우에는 디스크 캐시 설정을 ‘**None**’으로 지정해야 합니다. 이러한 설정은 Standard Storage 계정에 대한 권장 사항과는 다릅니다.

VHD를 연결한 후에는 캐시 설정을 변경할 수 없습니다. 업데이트된 캐시 설정으로 VHD를 분리했다가 다시 연결해야 합니다.

### <a name="windows-storage-spaces"></a>Windows 저장소 공간

이전 Standard Storage와 같이 [Windows 스토리지 공간](https://technet.microsoft.com/library/hh831739.aspx)을 사용할 수 있습니다. 그러면 이미 스토리지 공간을 사용 중인 VM을 마이그레이션할 수 있습니다. [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)의 예제(9단계부터)에서는 여러 VHD가 연결된 VM을 추출하고 가져오는 PowerShell 코드를 보여 줍니다.

스토리지 풀은 처리량을 높이고 대기 시간을 줄이기 위해 표준 Azure Storage 계정에서 사용되었습니다. 새 배포의 경우 Premium Storage에서도 스토리지 풀을 사용해 볼 수 있습니다. 그러나 이렇게 하면 스토리지 설정이 더 복잡해집니다.

#### <a name="how-to-find-which-azure-virtual-disks-map-to-storage-pools"></a>저장소 풀에 매핑되는 Azure 가상 디스크를 찾는 방법

연결된 VHD에 따라 각기 다른 캐시 설정을 사용하는 것이 좋으므로 VHD를 Premium Storage 계정에 복사할 수 있습니다. 그러나 새 DS 시리즈 VM에 VHD를 다시 연결할 때는 캐시 설정을 변경해야 할 수 있습니다. SQL 데이터 파일 및 로그 파일이 모두 포함된 VHD를 하나 사용하기보다는 두 파일에 대해 각각 별도의 VHD를 사용하는 경우 Premium Storage 권장 캐시 설정을 보다 간편하게 적용할 수 있습니다.

> [!NOTE]
> SQL Server 데이터 파일과 로그 파일이 같은 볼륨에 있으면 데이터베이스 작업의 IO 액세스 패턴에 따라 캐싱 옵션을 선택합니다. 테스트를 통해서만 이 시나리오에 가장 적합한 캐싱 옵션을 확인할 수 있습니다.
>
>

그러나 여러 VHD로 구성된 Windows 저장소 공간을 사용하는 경우에는 원본 스크립트를 확인하여 연결된 어떤 VHD가 어떤 특정 풀에 있는지 파악해야 각 디스크에 적절한 캐시 설정을 지정할 수 있습니다.

저장소 풀에 매핑되는 VHD를 보여 주는 원본 스크립트를 사용할 수 없는 경우에는 다음 단계에 따라 디스크/저장소 풀 매핑을 확인할 수 있습니다.

각 디스크에 대해 다음 단계를 수행합니다.

1. **Get-AzureVM** 명령을 사용하여 VM에 연결된 디스크 목록을 가져옵니다.

```powershell
Get-AzureVM -ServiceName <servicename> -Name <vmname> | Get-AzureDataDisk
```

1. DiskName과 LUN을 note 합니다.

    ![DisknameAndLUN][2]
1. VM에 원격 데스크톱으로 연결합니다. 그런 다음 **컴퓨터 관리** | **장치 관리자** | **디스크 드라이브**로 이동합니다. 각 ‘Microsoft 가상 디스크'의 속성을 확인합니다.

    ![VirtualDiskProperties][3]
1. 여기서 LUN 번호는 VHD를 VM에 연결할 때 지정하는 LUN 번호에 대한 참조입니다.
1. 'Microsoft 가상 디스크'의 경우 **세부 정보** 탭으로 이동한 다음 **속성** 목록에서 **드라이버 키**로 이동합니다. **값**에서 **오프셋**을 확인합니다. 아래 스크린샷에서 오프셋은 0002입니다. 0002는 저장소 풀이 참조하는 PhysicalDisk2를 나타냅니다.

    ![VirtualDiskPropertyDetails][4]
1. 각 저장소 풀에 대해 연결된 디스크를 덤프합니다.

```powershell
Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk
```

![GetStoragePool][5]

이제 이 정보를 사용하여 연결된 VHD를 저장소 풀의 실제 디스크에 연결할 수 있습니다.

스토리지 풀의 실제 디스크에 매핑한 VHD는 분리하여 Premium Storage 계정으로 복사한 다음 올바른 캐시 설정을 사용하여 연결할 수 있습니다. [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) 8~12단계의 예제를 참조하세요. 이러한 단계에서는 VM에 연결된 VHD 디스크 구성을 CSV파일에 추출하고 VHD를 복사한 다음 디스크 구성 캐시 설정을 변경하고 마지막으로 모든 연결된 디스크와 함께 VM을 DS 시리즈 VM으로 다시 배포하는 방법을 보여 줍니다.

### <a name="vm-storage-bandwidth-and-vhd-storage-throughput"></a>VM 저장소 대역폭 및 VHD 저장소 처리량

저장소 성능은 지정한 DS* VM 크기와 VHD 크기에 따라 달라집니다. VM마다 연결할 수 있는 VHD 수와 지원하는 최대 대역폭(MB/s)이 다릅니다. 구체적인 대역폭 수치는 [Azure를 위한 Virtual Machine 및 클라우드 서비스 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

디스크가 클수록 IOPS가 높아집니다. 마이그레이션 경로를 고려할 때는 이 점에 유의해야 합니다. 자세한 내용은 [IOPS 및 디스크 유형의 테이블을 참조하세요](../disks-types.md#premium-ssd).

마지막으로, VM마다 연결된 모든 디스크에 대해 지원하는 최대 디스크 대역폭이 서로 다르다는 점도 고려해야 합니다. 부하가 높을 때는 해당 VM 역할 크기에 사용 가능한 최대 디스크 대역폭을 모두 사용하게 될 수 있습니다. 예를 들어 Standard_DS14는 최대 512MB/s를 지원하므로 P30 디스크가 3개인 경우 VM의 디스크 대역폭이 모두 사용됩니다. 그러나 이 예제에서는 읽기 및 쓰기 IO 조합에 따라 처리량 제한을 초과할 수도 있습니다.

## <a name="new-deployments"></a>새 배포

다음 두 섹션에서는 SQL Server VM을 Premium Storage로 배포하는 방법을 설명합니다. 앞에서 설명한 것처럼 OS 디스크를 반드시 Premium Storage에 배치할 필요는 없습니다. IO를 많이 사용하는 작업을 OS VHD에서 수행하려는 경우에는 OS 디스크를 프리미엄 저장소에 배치할 수 있습니다.

첫 번째 예제에서는 기존 Azure 갤러리 이미지를 사용하는 방법을 보여 줍니다. 두 번째 예제에서는 기존 표준 저장소 계정 내의 사용자 지정 VM 이미지를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 이러한 예제에서는 지역 VNET을 이미 만들었다고 가정합니다.

### <a name="create-a-new-vm-with-premium-storage-with-gallery-image"></a>갤러리 이미지를 사용하여 Premium Storage에서 새 VM 만들기

아래 예제에서는 OS VHD를 Premium Storage에 배치하고 Premium Storage VHD를 연결하는 방법을 보여 줍니다. 그러나 Standard Storage 계정에 OS 디스크를 배치한 다음 Premium Storage 계정에 있는 VHD를 연결할 수도 있습니다. 여기서는 두 시나리오를 모두 살펴봅니다.

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Set up subscription
Set-AzureSubscription -SubscriptionName $mysubscription
Select-AzureSubscription -SubscriptionName $mysubscription -Current  
```

#### <a name="step-1-create-a-premium-storage-account"></a>1단계: Premium Storage 계정 만들기

```powershell
#Create Premium Storage account, note Type
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
```

#### <a name="step-2-create-a-new-cloud-service"></a>2단계: 새 클라우드 서비스 만들기

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-reserve-a-cloud-service-vip-optional"></a>3단계: 클라우드 서비스 VIP 예약(선택 사항)

```powershell
#check exisitng reserved VIP
Get-AzureReservedIP

$reservedVIPName = “sqlcloudVIP”
New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location
```

#### <a name="step-4-create-a-vm-container"></a>4단계: VM 컨테이너 만들기

```powershell
#Generate storage keys for later
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

##Generate storage acc contexts
$xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   

#Create container
$containerName = 'vhds'
New-AzureStorageContainer -Name $containerName -Context $xioContext
```

#### <a name="step-5-placing-os-vhd-on-standard-or-premium-storage"></a>5단계: 표준 또는 Premium Storage에 OS VHD 배치

```powershell
#NOTE: Set up subscription and default storage account which is used to place the OS VHD in

#If you want to place the OS VHD Premium Storage Account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  

#If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
$standardstorageaccountname = "danstdams"

Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname
```

#### <a name="step-6-create-vm"></a>6단계: VM 만들기

```powershell
#Get list of available SQL Server Images from the Azure Image Gallery.
$galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"}
$image = $galleryImage.ImageName

#Set up Machine Specific Information
$vmName = "dsDan1"
$vnet = "dansvnetwesteur"
$subnet = "SQL"
$ipaddr = "192.168.0.8"

#Remember to change to DS series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#Machine User Credentials
$userName = "myadmin"
$pass = "mycomplexpwd4*"

#Create VM Config
$vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Add Data and Log Disks to VM Config
#Note the size specified ‘-DiskSizeInGB 1023’, this attaches 2 x P30 Premium Storage Disk Type
#Utilising the Premium Storage enabled Storage account

$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
$vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"

#Create VM
$vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  

#Add RDP Endpoint
$EndpointNameRDPInt = "3389"
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM

#Check VHD storage account, these should be in $newxiostorageaccountname
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
```

### <a name="create-a-new-vm-to-use-premium-storage-with-a-custom-image"></a>사용자 지정 이미지를 사용하여 Premium Storage를 사용하도록 새 VM 만들기

이 시나리오에서는 Standard Storage 계정에 기존의 사용자 지정된 이미지가 있는 경우를 설명합니다. 앞에서 설명한 것처럼, OS VHD를 Premium Storage에 배치하려는 경우 Standard Storage 계정에 있는 이미지를 복사한 다음, Premium Storage로 전송해야 해당 VHD를 사용할 수 있습니다. 온-프레미스에 이미지가 있는 경우에는 이 방법을 통해 Premium Storage 계정으로 이미지를 직접 복사할 수도 있습니다.

#### <a name="step-1-create-storage-account"></a>1단계: Storage 계정 만들기

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Standard Storage account
$origstorageaccountname = "danstdams"
```

#### <a name="step-2-create-cloud-service"></a>2단계: 클라우드 서비스 만들기

```powershell
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-3-use-existing-image"></a>3단계: 기존 이미지 사용

기존 이미지를 사용할 수 있으며 [기존 컴퓨터의 이미지를 사용](../classic/capture-image-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)할 수도 있습니다. 이미지를 생성하는 컴퓨터는 DS* 컴퓨터가 아니어도 됩니다. 다음 단계에서는 이미지를 생성한 후 **Start-AzureStorageBlobCopy** PowerShell commandlet을 사용하여 Premium Storage 계정에 해당 이미지를 복사하는 방법을 보여 줍니다.

```powershell
#Get storage account keys:
#Standard Storage account
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
#Premium Storage account
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Set up contexts for the storage accounts:
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
```

#### <a name="step-4-copy-blob-between-storage-accounts"></a>4단계: Storage 계정 간에 Blob 복사

```powershell
#Get Image VHD
$myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
$containerName = 'vhds'

#Copy Blob between accounts
$blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
-DestContainer vhds -Destblob "prem-$myImageVHD" `
-Context $origContext -DestContext $destContext  
```

#### <a name="step-5-regularly-check-copy-status"></a>5단계: 정기적으로 복사 상태 확인

```powershell
$blob | Get-AzureStorageBlobCopyState
```

#### <a name="step-6-add-image-disk-to-azure-disk-repository-in-subscription"></a>6단계: 구독의 Azure 디스크 리포지토리에 이미지 디스크 추가

```powershell
$imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD
$newimageName = "prem"+"dansoldonorsql2k14"

Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
```

> [!NOTE]
> 상태는 성공으로 보고되는데 디스크 임대 오류가 발생하는 경우가 있습니다. 이 경우 10분 정도 기다립니다.

#### <a name="step-7--build-the-vm"></a>7단계:  VM 빌드

이 단계에서는 이미지에서 VM을 빌드하고 Premium Storage VHD 두 개를 연결합니다.

```powershell
$newimageName = "prem"+"dansoldonorsql2k14"
#Set up Machine Specific Information
$vmName = "dansolchild"
$vnet = "westeur"
$subnet = "Clients"
$ipaddr = "192.168.0.41"

#This needs to be a new cloud service
$destcloudsvc = "danregsvcamsxio2"

#Use to DS Series VM
$newInstanceSize = "Standard_DS1"

#create new Availability Set
$availabilitySet = "cloudmigAVAMS3"

#Machine User Credentials
$userName = "myadmin"
$pass = "theM)stC0mplexP@ssw0rd!”


#Create VM Config
$vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd"
$vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd"



$vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet
```

## <a name="existing-deployments-that-do-not-use-always-on-availability-groups"></a>Always On 가용성 그룹을 사용하지 않는 기존 배포

> [!NOTE]
> 기존 배포의 경우 먼저 이 문서의 [필수 조건](#prerequisites-for-premium-storage) 섹션을 참조하세요.

Always On 가용성 그룹 사용 여부에 따라 SQL Server 배포 관련 고려 사항이 달라집니다. Always On을 사용하지 않으며 기존 독립 실행형 SQL Server가 있는 경우 새 클라우드 서비스 및 Storage 계정을 사용하여 Premium Storage로 업그레이드할 수 있습니다. 이때 다음 옵션을 사용할 수 있습니다.

* **새 SQL Server VM 만들기**. 새 배포에서 설명하는 것처럼 Premium Storage 계정을 사용하는 새 SQL Server VM을 만들 수 있습니다. 그런 후에 SQL Server 구성과 사용자 데이터베이스를 백업 및 복원합니다. 내부 또는 외부에서 액세스되는 경우 새 SQL Server를 참조하도록 애플리케이션을 업데이트해야 합니다. 병렬(SxS) SQL Server 마이그레이션을 수행하는 것처럼 db 외부의 모든 개체를 복사해야 합니다. 여기에는 로그인, 인증서, 연결된 서버 등의 개체도 포함됩니다.
* **기존 SQL Server VM 마이그레이션**. 이 옵션을 사용하는 경우 SQL Server VM을 오프라인으로 전환한 다음, 새 클라우드 서비스로 전송해야 합니다. 이때 연결된 모든 VHD를 Premium Storage 계정으로 복사합니다. VM이 온라인 상태가 되면 애플리케이션은 이전과 같이 서버 호스트 이름을 참조합니다. 기존 디스크의 크기가 성능 특성에 영향을 준다는 것에 유의하세요. 예를 들어 400GB 디스크의 경우 P20으로 반올림될 수 있습니다. 그만큼 높은 디스크 성능이 필요하지 않으면 VM을 DS 시리즈 VM으로 다시 만든 다음 필요한 크기/성능 사양의 Premium Storage VHD를 연결하면 됩니다. 그런 후에 SQL DB 파일을 분리했다가 다시 연결합니다.

> [!NOTE]
> VHD 디스크를 복사할 때는 크기를 고려해야 합니다. 즉, 디스크는 크기에 따라 각기 다른 Premium Storage 디스크 형식에 속하며 해당하는 디스크 성능 사양이 결정됩니다. Azure에서는 디스크 크기를 가장 가까운 크기로 반올림하므로 400GB 디스크의 경우 P20으로 반올림됩니다. OS VHD의 기존 IO 요구 사항에 따라서는 해당 디스크를 Premium Storage 계정으로 마이그레이션하지 않아도 될 수 있습니다.

외부에서 SQL Server에 액세스하는 경우에는 클라우드 서비스 VIP가 변경됩니다. 끝점, ACL 및 DNS 설정도 업데이트해야 합니다.

## <a name="existing-deployments-that-use-always-on-availability-groups"></a>Always On 가용성 그룹을 사용하는 기존 배포

> [!NOTE]
> 기존 배포의 경우 먼저 이 문서의 [필수 조건](#prerequisites-for-premium-storage) 섹션을 참조하세요.

이 섹션에서는 먼저 Always On이 Azure 네트워킹과 상호 작용하는 방식을 살펴봅니다. 그런 다음, 마이그레이션을 두 가지 시나리오, 즉 어느 정도의 가동 중지 시간이 발생해도 되는 마이그레이션과 가동 중지 시간을 최소화해야 하는 시나리오로 구분하여 살펴봅니다.

온-프레미스 SQL Server Always On 가용성 그룹은 하나 이상의 SQL Server 간에 공유되는 IP 주소와 함께 가상 DNS 이름을 등록하는 온-프레미스 수신기를 사용합니다. 클라이언트는 연결 시 수신기 IP를 통해 주 SQL Server로 라우팅됩니다. 이 시점에서는 해당 서버가 Always On IP 리소스를 소유하고 있습니다.

![DeploymentsUseAlways On][6]

Microsoft Azure에서는 VM의 NIC에 IP 주소를 하나만 할당할 수 있으므로 온-프레미스와 같은 추상화 계층을 적용할 수 있도록 Azure에서는 ILB/ELB(내부/외부 부하 분산 장치)에 할당되는 IP 주소를 활용합니다. 서버 간에 공유되는 IP 리소스는 ILB/ELB와 같은 IP로 설정됩니다. 이 IP가 DNS에 게시되며, 클라이언트 트래픽은 ILB/ELB를 통해 주 SQL Server 복제본으로 전달됩니다. ILB/ELB는 검색을 사용하여 Always On IP 리소스를 검색하므로 주 SQL Server를 확인할 수 있습니다. 위의 예제에서는 ELB/ILB가 참조하는 엔드포인트가 포함된 각 노드를 검색합니다. 여기서 응답하는 서버가 주 SQL Server입니다.

> [!NOTE]
> ILB와 ELB는 모두 특정 Azure 클라우드 서비스에 할당되므로 Azure에서 클라우드 마이그레이션을 수행하면 부하 분산 장치 IP가 변경될 가능성이 높습니다.
>
>

### <a name="migrating-always-on-deployments-that-can-allow-some-downtime"></a>어느 정도의 가동 중지 시간이 발생해도 되는 Always On 배포 마이그레이션

어느 정도의 가동 중지 시간이 발생해도 되는 Always On 배포를 마이그레이션할 때는 두 가지 전략을 사용합니다.

1. **기존 Always On 클러스터에 보조 복제본 더 추가**
2. **새 Always On 클러스터로 마이그레이션**

#### <a name="1-add-more-secondary-replicas-to-an-existing-always-on-cluster"></a>1. 기존 Always On 클러스터에 보조 복제본 더 추가

전략 중 하나는 Always On 가용성 그룹에 보조 복제본을 더 추가하는 것입니다. 이러한 복제본을 새 클라우드 서비스에 추가하고 새 부하 분산 장치 IP로 수신기를 업데이트해야 합니다.

##### <a name="points-of-downtime"></a>가동 중지 시간 발생 시점:

* 클러스터 유효성 검사 시
* 새 보조 복제본에 대해 Always On 장애 조치(failover) 테스트 시

IO 처리량을 높이기 위해 VM 내에서 Windows 저장소 풀을 사용하는 경우 전체 클러스터 유효성 검사 중에 해당 풀이 오프라인으로 설정됩니다. 클러스터에 노드를 추가할 때는 유효성 검사 테스트를 수행해야 합니다. 테스트를 실행하는 데 걸리는 시간은 경우에 따라 다르므로 대표 테스트 환경에서 이 테스트를 수행하여 소요 시간을 대략적으로 파악해야 합니다.

새로 추가한 노드에서 수동 장애 조치(failover) 및 문제 상황 테스트를 수행할 수 있는 시간을 배정하여 Always On 고가용성 기능이 정상적으로 작동하는지를 확인해야 합니다.

![DeploymentUseAlways On2][7]

> [!NOTE]
> 유효성 검사를 실행하기 전에 저장소 풀이 사용되는 모든 SQL Server 인스턴스를 중지해야 합니다.
>
> ##### <a name="high-level-steps"></a>대략적인 단계
>

1. Premium Storage가 연결된 새 클라우드 서비스에 SQL Server 두 개를 새로 만듭니다.
2. **NORECOVERY**를 사용하여 전체 백업 및 복원을 복사합니다.
3. 로그인 등의 사용자 DB 외부에 있는 종속 개체를 복사합니다.
4. 새 ILB(내부 부하 분산 장치) 또는 ELB(외부 부하 분산 장치)를 만든 다음 두 새 노드에 모두 부하 분산된 엔드포인트를 설정합니다.

   > [!NOTE]
   > 계속하기 전에 모든 노드의 엔드포인트 구성이 올바른지 확인합니다.
   >
   >
5. 저장소 풀을 사용 중인 경우 SQL Server에 대한 사용자/응용 프로그램 액세스를 중지합니다.
6. 저장소 풀을 사용 중인 경우 모든 노드에서 SQL Server Engine Services를 중지합니다.
7. 새 노드를 클러스터에 추가하고 전체 유효성 검사를 실행합니다.
8. 유효성 검사가 정상적으로 완료되면 모든 SQL Server 서비스를 시작합니다.
9. 트랜잭션 로그를 백업하고 사용자 데이터베이스를 복원합니다.
10. Always On 가용성 그룹에 새 노드를 추가하고 복제를 **동기**로 설정합니다.
11. [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)의 다중 사이트 예제를 기준으로 하여 Always On용 PowerShell을 통해 새 클라우드 서비스 ILB/ELB의 IP 주소 리소스를 추가합니다. Windows 클러스터링에서 **IP 주소** 리소스의 **가능한 소유자**를 새 노드로 설정합니다. 자세한 내용은 [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)의 ‘같은 서브넷에서 IP 주소 리소스 추가' 섹션을 참조하세요.
12. 새 노드 중 하나로 장애 조치(failover)합니다.
13. 새 노드를 자동 장애 조치(failover)로 지정한 다음 장애 조치(failover)를 테스트합니다.
14. 가용성 그룹에서 원래 노드를 제거합니다.

##### <a name="advantages"></a>장점

* 새 SQL Server(SQL Server 및 애플리케이션)를 Always On에 추가하기 전에 테스트할 수 있습니다.
* VM 크기를 변경할 수 있으며 정확한 요구 사항에 맞게 저장소를 사용자 지정할 수 있습니다. 그러나 모든 SQL 파일 경로는 동일하게 유지하는 것이 좋습니다.
* 보조 복제본에 DB 백업이 전송이 시작되는 시간을 제어할 수 있습니다. 이 방식은 Azure **Start-AzureStorageBlobCopy** commandlet을 사용하여 VHD를 복사하는 비동기 복사와는 다릅니다.

##### <a name="disadvantages"></a>단점

* Windows 저장소 풀을 사용할 경우 새 추가 노드에 대한 전체 클러스터 유효성 검사를 수행하는 동안 클러스터의 가동이 중지됩니다.
* SQL Server 버전 및 기존 보조 복제본의 수에 따라서는 기존 보조 복제본을 제거해야 보조 복제본을 더 추가할 수 있는 경우도 있습니다.
* 보조 복제본을 설정하는 동안 SQL 데이터 전송 시간이 길어질 수 있습니다.
* 새 컴퓨터를 병렬로 실행하면 마이그레이션 중에 추가 비용이 발생할 수 있습니다.

#### <a name="2-migrate-to-a-new-always-on-cluster"></a>2. 새 Always On 클러스터로 마이그레이션

두 번째 전략은 새 클라우드 서비스에서 새 노드를 사용하여 Always On 클러스터를 새로 만든 다음 클라이언트가 해당 클러스터를 사용하도록 리디렉션하는 것입니다.

##### <a name="points-of-downtime"></a>가동 중지 시간 발생 시점

애플리케이션 및 사용자를 새 Always On 수신기로 전송할 때 가동 중지 시간이 발생합니다. 가동 중지 시간은 다음 시간에 따라 달라집니다.

* 최종 트랜잭션 로그 백업을 새 서버의 데이터베이스로 복원하는 데 걸리는 시간
* 새 Always On 수신기를 사용하도록 클라이언트 애플리케이션을 업데이트하는 데 걸리는 시간

##### <a name="advantages"></a>장점

* 실제 프로덕션 환경, SQL Serve 및 OS 빌드 변경 내용을 테스트할 수 있습니다.
* 저장소를 사용자 지정할 수 있으며 VM의 크기도 줄일 수 있습니다. 따라서 비용을 절감할 수 있습니다.
* 이 과정에서 SQL Server 빌드 또는 버전을 업데이트할 수 있습니다. 운영 체제도 업그레이드할 수 있습니다.
* 이전 Always On 클러스터를 명확한 롤백 대상으로 사용할 수 있습니다.

##### <a name="disadvantages"></a>단점

* 두 Always On 클러스터를 동시에 실행하려는 경우 수신기의 DNS 이름을 변경해야 합니다. 따라서 클라이언트 애플리케이션 문자열이 새 수신기 이름을 반영해야 하므로 마이그레이션 중에 관리 오버헤드가 추가됩니다.
* 마이그레이션 전에 최종 동기화 요구 사항을 최소화할 수 있도록 두 환경 간의 동기화 메커니즘을 최대한 근접하게 구현해야 합니다.
* 새 환경을 실행하는 동안 마이그레이션 중에 비용이 추가됩니다.

### <a name="migrating-always-on-deployments-for-minimal-downtime"></a>가동 중지 시간을 최소화해야 하는 Always On 배포 마이그레이션

가동 중지 시간을 최소화해야 하는 Always On 배포를 마이그레이션할 때는 두 가지 전략을 사용할 수 있습니다.

1. **기존 보조 복제본 활용: 단일 사이트**
2. **기존 보조 복제본 활용: 다중 사이트**

#### <a name="1-utilize-an-existing-secondary-single-site"></a>1. 기존 보조 복제본 활용: 단일 사이트

가동 중지 시간을 최소화하는 전략 중 하나는 기존의 클라우드 보조 복제본을 가져온 다음 현재 클라우드 서비스에서 제거하는 것입니다. 그런 다음 새 Premium Storage 계정에 VHD를 복사하고 새 클라우드 서비스에서 VM을 만듭니다. 그리고 나서 클러스터링 및 장애 조치(failover)에서 수신기를 업데이트합니다.

##### <a name="points-of-downtime"></a>가동 중지 시간 발생 시점

* 부하 분산된 엔드포인트로 최종 노드를 업데이트할 때 가동 중지 시간이 발생합니다.
* 클라이언트/DNS 구성에 따라 클라이언트 다시 연결이 지연될 수 있습니다.
* Always On 클러스터 그룹을 오프라인으로 설정하여 IP 주소를 교환하려는 경우에는 가동 중지 시간이 추가로 발생합니다. 추가되는 IP 주소 리소스에 대해 OR 종속성 및 가능한 소유자를 사용하면 이러한 가동 중지 시간을 방지할 수 있습니다. 자세한 내용은 [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)의 ‘같은 서브넷에서 IP 주소 리소스 추가' 섹션을 참조하세요.

> [!NOTE]
> 추가한 노드를 Always On 장애 조치(failover) 파트너로 사용하려는 경우에는 부하 분산된 집합에 대한 참조와 함께 Azure 엔드포인트를 추가해야 합니다. 이를 위해 **Add-AzureEndpoint** 명령을 실행하면 현재 연결은 계속 열려 있지만 부하 분산 장치를 업데이트할 때까지 수신기에 대한 새 연결을 설정할 수 없습니다. 테스트에서는 이 시간이 90~120초로 확인되었지만 실제 환경에서 테스트를 수행해야 합니다.

##### <a name="advantages"></a>장점

* 마이그레이션 중에 추가 비용이 발생하지 않습니다.
* 일대일 마이그레이션입니다.
* 복잡성이 감소합니다.
* Premium Storage SKU에서 보다 높아진 IOPS를 사용할 수 있습니다. 디스크를 VM에서 분리한 다음 새 클라우드 서비스로 복사할 때는 타사 도구를 사용하여 VHD 크기를 늘릴 수 있습니다. 그러면 처리량이 더 높아집니다. VHD 크기를 늘리는 방법에 대한 자세한 내용은 이 [포럼 토론](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)을 참조하세요.

##### <a name="disadvantages"></a>단점

* 마이그레이션 중에 HA 및 DR이 일시적으로 손실됩니다.
* 일대일 마이그레이션이므로 VHD 수를 지원하는 최소 VM 크기를 사용해야 하기 때문에 VM 크기를 줄이지 못할 수도 있습니다.
* 이 시나리오에서는 비동기 Azure **Start-AzureStorageBlobCopy** commandlet을 사용합니다. 복사 완료에 대한 SLA는 없습니다. 복사 시간은 큐의 대기 시간과 전송할 데이터 양에 따라 달라집니다. 다른 지역의 Premium Storage를 지원하는 다른 Azure 데이터 센터로 전송하는 경우에는 복사 시간이 길어집니다. 노드가 2개뿐인 경우 테스트할 때보다 복사 시간이 더 오래 걸리면 이러한 현상을 완화할 수 있는 방법을 고려합니다. 다음과 같은 방법을 사용할 수 있습니다.
  * 가동 중지 시간을 합의한 마이그레이션을 수행하기 전에 HA용 세 번째 임시 SQL Server 노드를 추가합니다.
  * Azure 예약 유지 관리를 수행하지 않는 시간에 마이그레이션을 실행합니다.
  * 클러스터 쿼럼을 올바르게 구성했는지 확인합니다.  

##### <a name="high-level-steps"></a>대략적인 단계

이 문서에서는 전체 종단 간 예제를 제공하지는 않으며 [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage) 에서 이 단계를 수행하는 데 활용할 수 있는 세부 정보를 제공합니다.

![MinimalDowntime][8]

* 디스크 구성 수집 및 노드 제거(연결된 VHD는 삭제하지 않음)
* Standard Storage 계정에서 Premium Storage 계정을 만들고 VHD 복사
* 새 클라우드 서비스를 만들고 해당 클라우드 서비스에서 SQL2 VM 다시 배포 복사한 원래 OS VHD를 사용하고 복사한 VHD를 연결하여 VM 만들기
* ILB/ELB를 구성하고 엔드포인트 추가
* 다음 중 하나를 수행하여 수신기 업데이트
  * Always On 그룹을 오프라인으로 설정하고 새 ILB/ELB IP 주소로 Always On 수신기를 업데이트합니다.
  * PowerShell을 통해 새 클라우드 서비스 iLB/ELB의 IP 주소 리소스를 Windows 클러스터링에 추가합니다. 그런 다음 IP 주소 리소스의 가능한 소유자를 마이그레이션된 노드인 SQL2로 설정하고 네트워크 이름에서 해당 노드를 OR 종속성으로 설정합니다. 자세한 내용은 [부록](#appendix-migrating-a-multisite-always-on-cluster-to-premium-storage)의 ‘같은 서브넷에서 IP 주소 리소스 추가' 섹션을 참조하세요.
* 클라이언트에 대한 DNS 구성/전파 확인
* SQL1 VM을 마이그레이션하고 2~4단계 수행
* 5ii단계를 사용하는 경우 추가된 IP 주소 리소스의 가능한 소유자로 SQL1 추가
* 장애 조치(failover) 테스트

#### <a name="2-utilize-existing-secondary-replicas-multi-site"></a>2. 기존 보조 복제본 활용: 다중 사이트

둘 이상의 Azure DC(데이터 센터)에 노드가 있거나 하이브리드 환경을 사용하는 경우에는 이 환경에서 Always On 구성을 사용하여 가동 중지 시간을 최소화할 수 있습니다.

이때 사용하는 방식은 온-프레미스 또는 보조 Azure DC에 대해 Always On 동기화를 동기로 변경한 다음 해당 SQL Server로 장애 조치(failover)하는 것입니다. 그런 후에 VHD를 Premium Storage 계정에 복사하고 새 클라우드 서비스로 컴퓨터를 다시 배포합니다. 마지막으로 수신기를 업데이트하고 장애 복구(failback)합니다.

##### <a name="points-of-downtime"></a>가동 중지 시간 발생 시점

가동 중지 시간은 대체 DC로 장애 조치(failover)한 다음 장애 복구(failback)하는 시간으로 구성됩니다. 또한 클라이언트/DNS 구성에 따라서도 가동 중지 시간이 달라지며, 이로 인해 클라이언트 다시 연결이 지연될 수 있습니다.
하이브리드 Always On 구성의 다음 예제를 살펴보세요.

![MultiSite1][9]

##### <a name="advantages"></a>장점

* 기존 인프라를 활용할 수 있습니다.
* DR Azure DC에서 Azure 저장소를 먼저 업그레이드할 수 있습니다.
* DR Azure DC 저장소를 다시 구성할 수 있습니다.
* 테스트 장애 조치(failover)를 제외하면 마이그레이션 중에 최소한 두 번의 장애 조치(failover)가 수행됩니다.
* 백업과 복원 시에 SQL Server 데이터를 이동할 필요가 없습니다.

##### <a name="disadvantages"></a>단점

* SQL Server에 대한 클라이언트 액세스에 따라 SQL Server가 애플리케이션에 대한 대체 DC에서 실행될 때는 대기 시간이 길어질 수 있습니다.
* Premium Storage에 VHD를 복사하는 데 시간이 오래 걸릴 수 있습니다. 이러한 시간 증가는 가용성 그룹에 노드를 유지할지를 결정하는 데 영향을 줄 수 있습니다. 마이그레이션 중에 로그를 많이 사용하는 워크로드를 실행하는 경우 이 사항을 고려해야 합니다. 주 노드에서 복제되지 않은 트랜잭션을 트랜잭션 로그에 보관해야 하기 때문입니다. 따라서 로그의 크기가 대폭 증가할 수 있습니다.
* 이 시나리오에서는 비동기 Azure **Start-AzureStorageBlobCopy** commandlet을 사용합니다. 완료 시 SLA는 제공되지 않습니다. 복사 시간은 큐의 대기 시간과 전송할 데이터 양에 따라 달라집니다. 따라서 두 번째 데이터 센터에 노드가 하나뿐인 경우에는 테스트할 때보다 복사 시간이 오래 걸리면 완화 단계를 수행해야 합니다. 이러한 완화 단계에는 다음 아이디어가 포함됩니다.
  * 가동 중지 시간을 합의한 마이그레이션을 수행하기 전에 HA용 두 번째 임시 SQL 노드를 추가합니다.
  * Azure 예약 유지 관리를 수행하지 않는 시간에 마이그레이션을 실행합니다.
  * 클러스터 쿼럼을 올바르게 구성했는지 확인합니다.

이 시나리오에서는 사용자가 설치 작업을 문서화했으며 최적의 디스크 캐시 설정을 적용할 수 있도록 변경하기 위해 저장소를 매핑하는 방법을 알고 있다고 가정합니다.

##### <a name="high-level-steps"></a>대략적인 단계

![Multisite2][10]

* 온-프레미스/대체 Azure DC를 주 SQL Server로 지정하고 다른 DC를 AFP, 즉 자동 장애 조치(failover) 파트너)로 지정
* SQL2에서 디스크 구성 정보를 수집하고 노드 제거(연결된 VHD는 제거하지 않음)
* Standard Storage 계정에서 Premium Storage 계정을 만들고 VHD 복사
* 새 클라우드 서비스를 만들고 Premium Storage 디스크가 연결된 SQL2VM 만들기
* ILB/ELB를 구성하고 엔드포인트 추가
* 새 ILB/ELB IP 주소로 Always On 수신기를 업데이트하고 장애 조치(failover) 테스트
* DNS 구성 확인
* AFP를 SQL2로 변경한 다음 SQL1을 마이그레이션하고 2~5단계 진행
* 장애 조치(failover) 테스트
* AFP를 다시 SQL1 및 SQL2로 전환

## <a name="appendix-migrating-a-multisite-always-on-cluster-to-premium-storage"></a>부록: 다중 사이트 Always On 클러스터를 Premium Storage로 마이그레이션

이 문서의 나머지 부분에서는 다중 사이트 Always On 클러스터를 Premium Storage로 변환하는 자세한 예제를 제공합니다. 또한 ELB(외부 부하 분산 장치)를 사용하는 수신기가 ILB(내부 부하 분산 장치)를 사용하도록 변환합니다.

### <a name="environment"></a>Environment

* Windows 2012/SQL 2012
* SP의 DB 파일 하나
* 노드당 저장소 풀 2개

![Appendix1][11]

### <a name="vm"></a>VM:

이 예제에서는 ELB에서 ILB로 이동하는 방법을 설명합니다. ELB가 ILB보다 먼저 제공되었으므로 여기서는 마이그레이션 중에 ILB로 전환하는 방법을 보여줍니다.

![Appendix2][12]

### <a name="pre-steps-connect-to-subscription"></a>이전 단계: 구독에 연결

```powershell
Add-AzureAccount

#Set up subscription
Get-AzureSubscription
```

#### <a name="step-1-create-new-storage-account-and-cloud-service"></a>1단계: 새 Storage 계정 및 클라우드 서비스 만들기

```powershell
$mysubscription = "DansSubscription"
$location = "West Europe"

#Storage accounts
#current storage account where the vm to migrate resides
$origstorageaccountname = "danstdams"

#Create Premium Storage account
$newxiostorageaccountname = "danspremsams"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

#Generate storage keys for later
$originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
$xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname

#Generate storage acc contexts
$origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
$xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#CREATE NEW CLOUD SVC
$vnet = "dansvnetwesteur"

##Existing cloud service
$sourceSvc="dansolSrcAms"

##Create new cloud service
$destcloudsvc = "danNewSvcAms"
New-AzureService $destcloudsvc -Location $location
```

#### <a name="step-2-increase-the-permitted-failures-on-resources-optional"></a>2단계: 리소스에 허용 된 오류를 늘리려면 \<옵션 >

Always On 가용성 그룹에 속하는 특정 리소스에는 클러스터 서비스가 리소스 그룹의 재시작을 시도하는 일정 기간 동안 발생 가능한 실패 횟수에 대한 한도가 있습니다. 이 절차를 진행하면서 해당 횟수를 늘리는 것이 좋습니다. 수동으로 장애 조치(failover)하지 않고 컴퓨터를 종료하여 장애 조치(failover)를 트리거하는 경우에는 이 제한에 접근할 수 있기 때문입니다.

실패 허용 횟수는 두 배로 늘리는 것이 좋습니다. 이렇게 하려면 장애 조치(Failover) 클러스터 관리자에서 Always On 리소스 그룹의 속성으로 이동합니다.

![Appendix3][13]

최대 실패 횟수를 6으로 변경합니다.

#### <a name="step-3-addition-ip-address-resource-for-cluster-group-optional"></a>3단계: 클러스터 그룹에 대 한 추가 IP 주소 리소스 \<옵션 >

클러스터 그룹의 IP 주소가 하나뿐이며 해당 주소가 클라우드 서브넷에 지정되어 있는 경우, 해당 네트워크에서 클라우드의 모든 클러스터 노드를 실수로 오프라인으로 전환하면 클러스터 IP 리소스 및 클러스터 네트워크 이름이 온라인으로 전환될 수 없습니다. 이 상황에서는 다른 클러스터 리소스에 대한 업데이트가 차단됩니다.

#### <a name="step-4-dns-configuration"></a>4단계: DNS 구성

원활한 전환을 구현하려면 DNS 사용 및 업데이트 방법을 고려해야 합니다.
Always On이 설치될 때 Windows 클러스터 리소스 그룹을 만듭니다. 장애 조치(failover) 클러스터 관리자를 열면 3개 이상의 리소스가 표시되는데, 문서에 설명되어 있는 두 리소스는 다음과 같습니다.

* VNN(가상 네트워크 이름) - Always On을 통해 SQL Server에 연결하려는 경우에 클라이언트가 연결하는 DNS 이름입니다.
* IP 주소 리소스 – VNN과 연결된 IP 주소입니다. 둘 이상의 리소스가 있을 수 있으며, 다중 사이트 구성에서는 사이트/서브넷당 IP 주소 하나가 포함됩니다.

SQL Server에 연결할 때 SQL Server 클라이언트 드라이버는 수신기와 연결된 DNS 레코드를 검색한 다음, Always On에 연결된 각 IP 주소에 연결을 시도합니다. 다음으로, 이 연결에 영향을 줄 수 있는 몇 가지 요인에 대해 설명합니다.

수신기 이름에 연결되는 동시 DNS 레코드의 수는 연결된 IP 주소의 수뿐만 아니라 Always On VNN 리소스에 대한 장애 조치(failover) 클러스터링의 ‘RegisterAllIpProviders’ 설정에 따라서도 달라집니다.

Azure에서 Always On을 배포할 때는 다른 단계를 수행하여 수신기와 IP 주소를 만듭니다. 온-프레미스 Always On 배포에서는 ‘RegisterAllIpProviders’가 이미 1로 설정되어 있지만, 이 경우에는 해당 값을 1로 수동 구성해야 합니다.

‘RegisterAllIpProviders’가 0이면 수신기와 연결된 DNS에 DNS 레코드가 하나만 표시됩니다.

![Appendix4][14]

'RegisterAllIpProviders'가 1인 경우에는 다음 레코드가 표시됩니다.

![Appendix5][15]

아래 코드는 VNN 설정을 덤프하고 자동으로 설정합니다. 변경 내용을 적용하려면 VNN을 오프라인으로 전환했다가 다시 온라인으로 전환해야 합니다. 이 경우 수신기가 오프라인으로 전환되어 클라이언트 연결이 중단됩니다.

```powershell
##Always On Listener Name
$ListenerName = "Mylistener"
##Get AlwaysOn Network Name Settings
Get-ClusterResource $ListenerName| Get-ClusterParameter
##Set RegisterAllProvidersIP
Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1
```

이후 마이그레이션 단계에서는 부하 분산 장치를 참조하는 업데이트된 IP 주소를 사용하여 Always On 수신기를 업데이트해야 합니다. 여기에는 IP 주소 리소스 제거 및 추가가 포함됩니다. IP 업데이트 후에는 DNS 영역에서 새 IP 주소가 업데이트되었으며 클라이언트가 로컬 DNS 캐시를 업데이트하는지를 확인해야 합니다.

클라이언트가 다른 네트워크 세그먼트에 있으며 다른 DNS 서버를 참조하는 경우에는 마이그레이션 중에 DNS 영역 전송과 관련하여 발생하는 현상을 고려해야 합니다. 애플리케이션 다시 연결 시간은 최소한 수신기에 대한 새 IP 주소의 영역 전송 시간에 의해 제한되기 때문입니다. 여기서 시간 제약이 있는 경우에는 Windows 팀과 논의하여 강제 증분 영역 전송을 테스트해야 하며, 클라이언트가 업데이트되도록 DNS 호스트 레코드의 TTL(Time to Live)을 줄여야 합니다. 자세한 내용은 [증분 영역 전송](https://technet.microsoft.com/library/cc958973.aspx) 및 [Start-DnsServerZoneTransfer](https://docs.microsoft.com/powershell/module/dnsserver/start-dnsserverzonetransfer)를 참조하세요.

기본적으로 Azure에서 Always On 수신기와 연결된 DNS 레코드의 TTL은 1200초입니다. 마이그레이션 중에 시간 제약이 있는 경우에는 클라이언트가 수신기에 대해 업데이트된 IP 주소로 DNS를 업데이트하도록 이 시간을 줄일 수 있습니다. VNN의 구성을 덤프하면 구성을 확인하고 수정할 수 있습니다.

```powershell
$AGName = "myProductionAG"
$ListenerName = "Mylistener"
#Look at HostRecordTTL
Get-ClusterResource $ListenerName| Get-ClusterParameter

#Set HostRecordTTL Examples
Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120
```

> [!NOTE]
> ‘HostRecordTTL’이 낮을수록 DNS 트래픽이 더 많이 발생합니다.

##### <a name="client-application-settings"></a>클라이언트 애플리케이션 설정

SQL 클라이언트 응용 프로그램에.NET 4.5 SQLClient를 지 원하는 경우 사용할 수 ' MULTISUBNETFAILOVER = TRUE' 키워드입니다. 이 키워드는 장애 조치(failover) 중 SQL Always On 가용성 그룹에 더 빠르게 연결할 수 있게 하므로 적용해야 합니다. 이 키워드는 Always On 수신기에 연결된 모든 IP 주소를 병렬로 열거하며 장애 조치(failover) 중에 TCP 연결 다시 시도를 더 빠르게 수행합니다.

이전 설정에 대한 자세한 내용은 [MultiSubnetFailover 키워드 및 관련 기능](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)을 참조하세요. 또한 [SqlClient의 고가용성 및 재해 복구 지원](https://msdn.microsoft.com/library/hh205662\(v=vs.110\).aspx)도 참조하세요.

#### <a name="step-5-cluster-quorum-settings"></a>5단계: 클러스터 쿼럼 설정

이 단계에서는 SQL Server를 한 번에 하나 이상 중지할 것이므로 클러스터 쿼럼 설정을 수정해야 합니다. 두 개의 노드에서 FSW(파일 공유 감시)를 사용하는 경우에는 노드 과반수를 허용하고 동적 응답(voting)을 활용하도록 쿼럼을 설정해야 합니다. 이렇게 해야 하나의 노드를 계속 실행 상태로 유지할 수 있습니다.

```powershell
Set-ClusterQuorum -NodeMajority  
```

클러스터 쿼럼을 관리하고 구성하는 방법에 대한 자세한 내용은 [Windows Server 2012 장애 조치(failover) 클러스터에서 쿼럼 구성 및 관리](https://technet.microsoft.com/library/jj612870.aspx)를 참조하세요.

#### <a name="step-6-extract-existing-endpoints-and-acls"></a>6단계: 기존 엔드포인트 및 ACL 추출

```powershell
#GET Endpoint info
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
#GET ACL Rules for Each EP, this example is for the Always On Endpoint
Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  
```

이 텍스트를 파일에 저장합니다.

#### <a name="step-7-change-failover-partners-and-replication-modes"></a>7단계: 장애 조치(failover) 파트너 및 복제 모드 변경

SQL Server가 셋 이상이면 다른 DC 또는 온-프레미스의 다른 보조 복제본 장애 조치(failover)를 ‘동기’로 변경하고 해당 복제본을 AFP(자동 장애 조치(failover) 파트너)로 지정해야 합니다. 그러면 변경 시 HA가 유지됩니다. SSMS를 통해 TSQL modify를 사용하여 이 작업을 수행할 수 있습니다.

![Appendix6][16]

#### <a name="step-8-remove-secondary-vm-from-cloud-service"></a>8단계: 클라우드 서비스에서 보조 VM 제거

먼저 클라우드 보조 노드를 마이그레이션하도록 계획해야 합니다. 이 노드가 현재 기본 노드인 경우 수동 장애 조치(failover)를 시작해야 합니다.

```powershell
$vmNameToMigrate="dansqlams2"

#Check machine status
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Shutdown secondary VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM


#Extract disk configuration

##Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk config, make sure below returns the disks associated with the VM
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machibe is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-9-change-disk-caching-settings-in-csv-file-and-save"></a>9단계: CSV 파일에서 디스크 캐싱 설정 변경 및 저장

데이터 볼륨의 경우 디스크 캐싱을 READONLY로 설정해야 합니다.

TLOG 볼륨의 경우 디스크 캐싱을 NONE으로 설정해야 합니다.

![Appendix7][17]

#### <a name="step-10-copy-vhds"></a>10단계: VHDS 복사

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContext

####DISK COPYING####
#Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
}
```


Premium Storage 계정으로의 VHD의 복사 상태를 확인할 수 있습니다.

```powershell
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName

   $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix8][18]

모든 작업이 성공으로 기록될 때까지 기다립니다.

개별 Blob에 대한 정보를 확인하려면 다음을 실행합니다.

```powershell
Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext
```

#### <a name="step-11-register-os-disk"></a>11단계: OS 디스크 등록

```powershell
#Change storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"
```

#### <a name="step-12-import-secondary-into-new-cloud-service"></a>12단계: 새 클라우드 서비스에 보조 복제본 가져오기

아래 코드에서는 추가된 옵션도 사용합니다. 여기서 컴퓨터를 가져오고 유지 가능한 VIP를 사용할 수 있습니다.

```powershell
#Build VM Config
$ipaddr = "192.168.0.5"
#Remember to change to XIO
$newInstanceSize = "Standard_DS13"
$subnet = "SQL"

#Create new Availability Set
$availabilitySet = "cloudmigAVAMS"

#build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
```

#### <a name="step-13-create-ilb-on-new-cloud-svc-add-load-balanced-endpoints-and-acls"></a>13단계: 새 클라우드 서비스에서 ILB를 만들고 부하 분산된 엔드포인트 및 ACL 추가

```powershell
#Check for existing ILB
GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc

$ilb="sqlIntIlbDest"
$subnet = "SQL"
$IP="192.168.0.25"
Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM

#SET Azure ACLs or Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx

####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####
```

#### <a name="step-14-update-always-on"></a>14단계: Always On 업데이트

```powershell
#Code to be executed on a Cluster Node
$ClusterNetworkNameAmsterdam = "Cluster Network 2" # the azure cluster subnet network name
$newCloudServiceIPAmsterdam = "192.168.0.25" # IP address of your cloud service

$AGName = "myProductionAG"
$ListenerName = "Mylistener"


Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop

#set dependency and NETBIOS, then remove old IP address

#set NETBIOS, then remove old IP address
Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0

#set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:

#Make sure no static records in DNS
```

![Appendix9][19]

이제 이전 클라우드 서비스 IP 주소를 제거합니다.

![Appendix10][20]

#### <a name="step-15-dns-update-check"></a>15단계: DNS 업데이트 확인

이제 SQL Server 클라이언트 네트워크의 DNS 서버를 확인하고 클러스터링에서 추가된 IP 주소에 대해 여분의 호스트 레코드를 추가했는지 확인해야 합니다. 해당 DNS 서버가 업데이트되지 않은 경우 강제 DNS 영역 전송을 수행하고 서브넷 내의 클라이언트를 두 Always On IP 주소로 모두 확인할 수 있는지 확인할 수 있습니다. 그러면 자동 DNS 복제가 수행되기를 기다리지 않아도 됩니다.

#### <a name="step-16-reconfigure-always-on"></a>16단계: Always On 다시 구성

여기서는 마이그레이션한 보조 노드가 온-프레미스 노드와 완전히 다시 동기화되어 동기 복제 노드로 전환될 때까지 기다렸다가 해당 노드를 AFP로 지정합니다.  

#### <a name="step-17-migrate-second-node"></a>17단계: 두 번째 노드 마이그레이션

```powershell
$vmNameToMigrate="dansqlams1"

Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate

#Get endpoint information
$endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint

#Shutdown VM
Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM

#Get disk config

#Building Existing Data Disk Configuration
$file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
$datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
foreach ($disk in $datadisks)
{
    $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
    $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName
    $adddisk | add-content -path $file
}

#Get OS Disk
$osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
$osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
$osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
$addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName
$addosdisk | add-content -path $file

#Import disk config
$diskobjects  = Import-CSV $file

#Check disk configuration
$diskobjects

#Identify OS Disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osdiskforbuild = $osdiskimport.diskName

#Check machine is off
Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate

#Drop machine and rebuild to new cls
Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate
```

#### <a name="step-18-change-disk-caching-settings-in-csv-file-and-save"></a>18단계: CSV 파일에서 디스크 캐싱 설정 변경 및 저장

데이터 볼륨의 경우 캐시 설정을 READONLY로 설정해야 합니다.

TLOG 볼륨의 경우 캐시 설정을 NONE으로 설정해야 합니다.

![Appendix11][21]

#### <a name="step-19-create-new-independent-storage-account-for-secondary-node"></a>19단계: 보조 노드에 대한 새 독립 Storage 계정 만들기

```powershell
$newxiostorageaccountnamenode2 = "danspremsams2"
New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  

#Reset the storage account src if node 1 in a different storage account
$origstorageaccountname2nd = "danstdams2"

#Generate storage keys for later
$xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2

#Generate storage acc contexts
$xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  

#Set up subscription and default storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current
```

#### <a name="step-20-copy-vhds"></a>20단계: VHDS 복사

```powershell
#Ensure you have created the container for these:
$containerName = 'vhds'

#Create container
New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  

####DISK COPYING####
##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
ForEach ($disk in $diskobjects)
{
   $lun = $disk.Lun
   $vhdname = $disk.vhdname
   $cacheoption = $disk.HostCaching
   $disklabel = $disk.DiskLabel
   $diskName = $disk.DiskName
   Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"

   #Start async copy
   Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContextnode2
}

#Check for copy progress

#check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
```

모든 VHD의 VHD 복사 상태를 확인할 수 있습니다.

```powershell
ForEach ($disk in $diskobjects)
{
    $lun = $disk.Lun
    $vhdname = $disk.vhdname
    $cacheoption = $disk.HostCaching
    $disklabel = $disk.DiskLabel
    $diskName = $disk.DiskName

    $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status
}
```

![Appendix12][22]

모든 작업이 성공으로 기록될 때까지 기다립니다.

개별 Blob에 대한 정보를 확인하려면 다음을 실행합니다.

```powershell
#Check individual blob status
Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContextnode2
```

#### <a name="step-21-register-os-disk"></a>21단계: OS 디스크 등록

```powershell
#change storage account to the new XIO storage account
Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
Select-AzureSubscription -SubscriptionName $mysubscription -Current

#Register OS disk
$osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
$osvhd = $osdiskimport.vhdname
$osdiskforbuild = $osdiskimport.diskName

#Registering OS disk, but as XIO disk
$xioDiskName = $osdiskforbuild + "xio"
Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#Build VM Config
$ipaddr = "192.168.0.4"
$newInstanceSize = "Standard_DS13"

#Join to existing Availability Set

#Build machine config into object
$vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr

#Reload disk config
$diskobjects  = Import-CSV $file
$datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}

ForEach ( $attachdatadisk in $datadiskimport)
{
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname

    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label

}

#Create VM
$vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose
```

#### <a name="step-22-add-load-balanced-endpoints-and-acls"></a>22단계: 부하 분산된 엔드포인트 및 ACL 추가

```powershell
#Endpoints
$epname="sqlIntEP"
$prot="tcp"
$locport=1433
$pubport=1433
Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM


#STOP!!! CHECK in the Azure portal or Machine Endpoints through PowerShell that these Endpoints are created!

#SET ACLs or Azure Network Security Groups & Windows FWs

#https://msdn.microsoft.com/library/azure/dn495192.aspx
```

#### <a name="step-23-test-failover"></a>23단계: 테스트 장애 조치

마이그레이션된 노드가 온-프레미스 Always On 노드와 동기화될 때까지 기다립니다. 동기 복제 모드로 설정하고 동기화될 때까지 기다립니다. 그런 다음 장애 조치에서 온-프레미스에서 첫 번째 노드 마이그레이션, afp. 장애 조치(failover)가 정상적으로 수행되면 마지막으로 마이그레이션한 노드를 AFP로 변경합니다.

모든 노드 간에 장애 조치(failover)를 테스트하고 비정상 상황 테스트를 실행하여 장애 조치(failover)가 제때 정상적으로 작동하는지 확인해야 합니다.

#### <a name="step-24-put-back-cluster-quorum-settings--dns-ttl--failover-pntrs--sync-settings"></a>24단계: 클러스터 쿼럼 설정/DNS TTL/장애 조치(failover) 파트너/동기화 설정 원래대로 변경

##### <a name="adding-ip-address-resource-on-same-subnet"></a>동일한 서브넷에 IP 주소 리소스 추가

SQL Server가 두 개뿐이며 이러한 SQL Server를 새 클라우드 서비스로 마이그레이션하되 동일한 서브넷에 유지하려는 경우 원래 Always On IP 주소를 삭제하고 새 IP 주소를 추가하기 위해 수신기를 오프라인으로 전환하지 않아도 됩니다. VM을 다른 서브넷으로 마이그레이션하는 경우에는 추가 클러스터 네트워크가 해당 서브넷을 참조하므로 이 작업을 수행할 필요가 없습니다.

마이그레이션한 보조 복제본을 작동시키고 새 클라우드 서비스의 새 IP 주소 리소스에 추가한 후에는 기존 주 복제본을 장애 조치(failover)하기 전에 클러스터 장애 조치(Failover) 관리자 내에서 다음 단계를 수행해야 합니다.

IP 주소를 추가하려면 부록의 14단계를 참조하세요.

1. 현재 IP 주소 리소스의 경우 가능한 소유자를 ‘기존 주 SQL Server’로 변경합니다(예제에서는 ‘dansqlams4’).

    ![Appendix13][23]
2. 새 IP 주소 리소스의 경우 가능한 소유자를 ‘마이그레이션된 보조 SQL Server’로 변경합니다(예제에서는 ‘dansqlams5’).

    ![Appendix14][24]
3. 이와 같이 설정하면 장애 조치(failover)를 수행할 수 있으며, 마지막 노드를 마이그레이션할 때 가능한 소유자를 편집하여 해당 노드를 가능한 소유자로 추가해야 합니다.

    ![Appendix15][25]

## <a name="additional-resources"></a>추가 리소스

* [Azure Premium Storage](../disks-types.md)
* [Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
* [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-windows-classic-sql-server-premium-storage/10_Appendix_15.png
