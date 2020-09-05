---
title: Azure PowerShell를 사용 하 여 Azure VM에서 SQL Server를 프로 비전 하는 가이드
description: SQL Server 가상 머신 갤러리 이미지를 사용하여 Azure VM을 만드는 단계 및 PowerShell 명령을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 94ac7282375660308f8dab37ae5bd874828f778a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074749"
---
# <a name="how-to-use-azure-powershell-to-provision-sql-server-on-azure-virtual-machines"></a>Azure PowerShell를 사용 하 여 Azure Virtual Machines에서 SQL Server를 프로 비전 하는 방법

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 가이드에서는 PowerShell을 사용 하 여 Azure Virtual Machines (Vm)에서 SQL Server를 프로 비전 하는 옵션을 설명 합니다. 기본값에 의존 하는 간소화 된 Azure PowerShell 예제는 [SQL VM Azure PowerShell 빠른](sql-vm-create-powershell-quickstart.md)시작을 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>구독 구성

1. PowerShell을 열고 **Connect-AzAccount** 명령을 실행하여 Azure 계정에 대한 액세스를 설정합니다.

   ```powershell
   Connect-AzAccount
   ```

1. 메시지가 표시되면 자격 증명을 입력합니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

## <a name="define-image-variables"></a>이미지 변수 정의

값을 다시 사용하고 스크립트 작성을 간소화하려면 먼저 다양한 변수를 정의합니다. 원하는 대로 매개 변수 값을 변경하지만, 제공된 값을 수정할 때 이름 길이 및 특수 문자와 관련된 명명 제한 사항에 주의하세요.

### <a name="location-and-resource-group"></a>위치 및 리소스 그룹

다른 VM 리소스를 만들려는 데이터 영역 및 리소스 그룹을 정의 합니다.

원하는 대로 수정한 후 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>스토리지 속성

가상 머신에서 사용할 스토리지 계정 및 스토리지 유형을 정의합니다.

원하는 대로 수정한 후 다음 cmdlet을 실행 하 여 이러한 변수를 초기화 합니다. 프로덕션 워크로드에는 [프리미엄 SSD](../../../virtual-machines/disks-types.md#premium-ssd)를 사용하는 것이 좋습니다.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>네트워크 속성

가상 컴퓨터의 네트워크에서 사용할 속성을 정의합니다. 

- Linux
- TCP/IP 할당 방법
- 가상 네트워크 이름
- 가상 서브넷 이름
- 가상 네트워크의 IP 주소 범위
- 서브넷의 IP 주소 범위
- 공용 도메인 이름 레이블

원하는 대로 수정한 후 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>가상 머신 속성

다음 속성을 정의 합니다.

- 가상 머신 이름
- 컴퓨터 이름
- 가상 머신 크기
- 가상 컴퓨터의 운영 체제 디스크 이름

원하는 대로 수정한 후 다음 cmdlet을 실행하여 이러한 변수를 초기화합니다.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>SQL Server 이미지 선택

다음 변수를 사용하여 가상 머신에 사용할 SQL Server 이미지를 정의합니다. 

1. 먼저 명령을 사용 하 여 모든 SQL Server 이미지 제품을 나열 `Get-AzVMImageOffer` 합니다. 이 명령은 Azure Portal에서 사용할 수 있는 현재 이미지와 PowerShell을 사용 하 여 설치할 수 있는 이전 이미지도 나열 합니다.

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. 이 자습서에서는 다음 변수를 사용하여 Windows Server 2016에서 SQL Server 2017을 지정합니다.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. 다음으로, 사용 가능한 제품의 버전을 나열합니다.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. 이 자습서에서는 SQL Server 2017 Developer Edition(**SQLDEV**)을 사용합니다. Developer Edition은 테스트 및 개발을 위해 무료로 사용이 허가되며 VM을 실행하는 요금만 지불하면 됩니다.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 관리자 배포 모델을 사용하여 만드는 첫 번째 개체는 리소스 그룹입니다. [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 Azure 리소스 그룹 및 해당 리소스를 만듭니다. 리소스 그룹 이름 및 위치에 대해 이전에 초기화한 변수를 지정합니다.

다음 cmdlet을 실행하여 새 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

가상 머신에 운영 체제 디스크와 SQL Server 데이터 및 로그 파일에 대한 스토리지 리소스가 필요합니다. 간단히 하기 위해 둘 다에 대한 단일 디스크를 만듭니다. SQL Server 데이터와 로그 파일을 전용 디스크에 배치하기 위해 [Add-Azure Disk](/powershell/module/servicemanagement/azure.service/add-azuredisk) cmdlet을 사용하여 나중에 추가 디스크를 연결할 수 있습니다. [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용하여 새 리소스 그룹에 표준 스토리지 계정을 만듭니다. 저장소 계정 이름, 저장소 SKU 이름 및 위치에 대해 이전에 초기화 한 변수를 지정 합니다.

다음 cmdlet을 실행하여 새 스토리지 계정을 만듭니다.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> 스토리지 계정을 만들려면 몇 분 정도 걸릴 수 있습니다.

## <a name="create-network-resources"></a>네트워크 리소스 만들기

가상 머신에 네트워크 연결을 위해 여러 네트워크 리소스가 필요합니다.

* 각 가상 머신에 가상 네트워크가 필요합니다.
* 가상 네트워크에는 하나 이상의 서브넷이 정의되어 있어야 합니다.
* 네트워크 인터페이스가 공용 또는 개인 IP 주소 중 하나로 정의되어야 합니다.

### <a name="create-a-virtual-network-subnet-configuration"></a>가상 네트워크 서브넷 구성 만들기

먼저 가상 네트워크에 대한 서브넷 구성을 만듭니다. 이 자습서에서는 [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet을 사용하여 기본 서브넷을 만듭니다. 서브넷 이름 및 주소 접두사에 대해 이전에 초기화한 변수를 지정합니다.

> [!NOTE]
> 이 cmdlet을 사용하여 가상 네트워크 서브넷 구성의 추가 속성을 정의할 수 있지만, 이 내용은 이 자습서에서 다루지 않습니다.

다음 cmdlet을 실행하여 가상 서브넷 구성을 만듭니다.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>가상 네트워크 만들기

다음으로, [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) cmdlet을 사용하여 새 리소스 그룹에 가상 네트워크를 만듭니다. 이름, 위치 및 주소 접두사에 대해 이전에 초기화한 변수를 지정합니다. 이전 단계에서 정의한 서브넷 구성을 사용합니다.

다음 cmdlet을 실행하여 가상 네트워크를 만듭니다.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>공용 IP 주소 만들기

이제 가상 네트워크를 정의했으므로 가상 머신에 연결하기 위해 IP 주소를 구성해야 합니다. 이 자습서에서는 인터넷 연결을 지원하도록 동적 IP 주소 지정을 사용하여 공용 IP 주소를 만듭니다. [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) cmdlet을 사용하여 새 리소스 그룹에 공용 IP 주소를 만듭니다. 이름, 위치, 할당 방법 및 DNS 도메인 이름 레이블에 대해 이전에 초기화한 변수를 지정합니다.

> [!NOTE]
> 이 cmdlet을 사용하여 공용 IP 주소의 추가 속성을 정의할 수 있지만, 이 내용은 이 초기 자습서에서 다루지 않습니다. 또한 고정 주소로 프라이빗 주소 또는 주소도 만들 수 있지만, 이 내용도 이 자습서에서 다루지 않습니다.

다음 cmdlet을 실행하여 공용 IP 주소를 만듭니다.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>네트워크 보안 그룹 만들기

VM 및 SQL Server 트래픽을 보호하려면 네트워크 보안 그룹을 만듭니다.

1. 먼저 rdp 연결을 허용 하는 RDP (원격 데스크톱)에 대 한 네트워크 보안 그룹 규칙을 만듭니다.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. TCP 포트 1433에서 트래픽을 허용하는 네트워크 보안 그룹 규칙을 구성합니다. 이렇게 하면 인터넷을 통해 SQL Server에 연결할 수 있습니다.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. 네트워크 보안 그룹을 만듭니다.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>네트워크 인터페이스 만들기

이제 가상 머신에 대 한 네트워크 인터페이스를 만들 준비가 되었습니다. [AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) cmdlet을 사용 하 여 새 리소스 그룹에 네트워크 인터페이스를 만듭니다. 이전에 정의한 이름, 위치, 서브넷 및 공용 IP 주소를 지정합니다.

다음 cmdlet을 실행하여 네트워크 인터페이스를 만듭니다.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>VM 개체 구성

이제 스토리지 및 네트워크 리소스를 정의했으므로 가상 머신에 대한 컴퓨팅 리소스를 정의할 수 있습니다.

- 가상 머신 크기 및 다양한 운영 체제 속성을 지정합니다.
- 이전에 만든 네트워크 인터페이스를 지정합니다.
- Blob 스토리지를 정의합니다.
- 운영 체제 디스크를 지정합니다.

### <a name="create-the-vm-object"></a>VM 개체 만들기

먼저 가상 머신 크기를 지정합니다. 이 자습서의 경우 DS13을 지정합니다. [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) cmdlet을 사용하여 구성 가능한 가상 머신 개체를 만듭니다. 이름 및 크기에 대해 이전에 초기화한 변수를 지정합니다.

다음 cmdlet을 실행하여 가상 머신 개체를 만듭니다.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>로컬 관리자 자격 증명에 대한 이름 및 암호를 저장하도록 자격 증명 개체 만들기

가상 머신에 대한 운영 체제 속성을 설정하려면 먼저 보안 문자열로 로컬 관리자 계정의 자격 증명을 제공해야 합니다. 이렇게 하려면 [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) cmdlet을 사용합니다.

다음 cmdlet을 실행합니다. PowerShell 자격 증명 요청 창에 VM의 로컬 관리자 이름과 암호를 입력 해야 합니다.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>가상 컴퓨터에 대한 운영 체제 속성 설정

이제 [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet을 사용하여 가상 머신의 운영 체제 속성을 설정할 준비가 되었습니다.

- 운영 체제 유형을 Windows로 설정합니다.
- [가상 머신 에이전트](../../../virtual-machines/extensions/agent-windows.md)를 설치해야 합니다.
- 이 cmdlet으로 자동 업데이트가 진행되도록 지정합니다.
- 가상 머신 이름, 컴퓨터 이름 및 자격 증명에 대해 이전에 초기화한 변수를 지정합니다.

다음 cmdlet을 실행하여 가상 머신의 운영 체제 속성을 설정합니다.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>가상 머신에 네트워크 인터페이스를 추가합니다.

다음으로, [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) cmdlet을 사용하여 이전에 정의한 변수를 사용하는 네트워크 인터페이스를 추가합니다.

다음 cmdlet을 실행하여 가상 머신의 네트워크 인터페이스를 설정합니다.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>가상 머신에서 사용할 디스크에 대한 Blob Storage 위치를 설정합니다.

다음으로, 이전에 정의한 변수를 사용 하 여 VM의 디스크에 대 한 blob 저장소 위치를 설정 합니다.

다음 cmdlet을 실행하여 Blob 스토리지 위치를 설정합니다.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>가상 머신에 대한 운영 체제 디스크 속성을 설정합니다.

다음으로, [Set-AzVMOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) cmdlet을 사용하여 가상 머신의 운영 체제 디스크 속성을 설정합니다. 

- 가상 머신의 운영 체제를 이미지에서 가져오도록 지정합니다.
- 캐싱을 읽기 전용으로 설정합니다(SQL Server가 동일한 디스크에 설치되므로).
- VM 이름 및 운영 체제 디스크에 대해 이전에 초기화한 변수를 지정합니다.

다음 cmdlet을 실행하여 가상 머신의 운영 체제 디스크 속성을 설정합니다.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>가상 머신에 대한 플랫폼 이미지 지정

마지막 구성 단계는 가상 머신에 대한 플랫폼 이미지를 지정하는 것입니다. 이 자습서에서는 최신 SQL Server 2016 CTP 이미지를 사용합니다. [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) cmdlet을 사용하여 이전에 정의한 변수로 이 이미지를 사용합니다.

다음 cmdlet을 실행하여 가상 머신의 플랫폼 이미지를 지정합니다.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>SQL VM 만들기

이제 구성 단계를 완료했으므로 가상 머신을 만들 수 있습니다. [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) cmdlet을 사용하여 정의한 변수로 가상 머신을 만듭니다.

> [!TIP]
> VM을 만드는 데 몇 분 정도 걸릴 수 있습니다.

다음 cmdlet을 실행하여 가상 머신을 만듭니다.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

가상 머신이 만들어집니다.

> [!NOTE]
> 부트 진단에 대한 오류가 발생하면 무시해도 됩니다. 가상 머신의 디스크에 대해 지정된 스토리지 계정은 Premium Storage 계정이므로 부팅 진단에 대해서는 표준 스토리지 계정이 만들어집니다.

## <a name="install-the-sql-iaas-agent"></a>SQL Iaas 에이전트 설치

SQL Server 가상 머신은 [SQL Server IaaS 에이전트 확장](sql-server-iaas-agent-extension-automate-management.md)을 사용하여 자동화된 관리 기능을 지원합니다. 새 VM에 에이전트를 설치 하 고 리소스 공급자에 등록 하려면 가상 머신을 만든 후 [AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) 명령을 실행 합니다. SQL Server VM에 맞는 라이선스 유형을 지정하고 [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 통해 종량제 또는 BYOL 중 하나를 선택합니다. 라이선스에 대한 자세한 내용은 [라이선스 모델](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>VM 중지 또는 제거

VM을 계속해서 실행하지 않아도 되는 경우 사용 중이 아닌 VM을 중지하여 불필요한 요금을 방지할 수 있습니다. 다음 명령은 VM을 중지하지만 나중에 계속 사용할 수 있습니다.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

**Remove-AzResourceGroup** 명령을 사용하여 가상 머신과 관련된 모든 리소스를 영구적으로 삭제할 수도 있습니다. 그러면 가상 머신도 영구적으로 삭제되므로 이 명령을 사용할 때는 주의해야 합니다.

## <a name="example-script"></a>예제 스크립트

다음 스크립트에는 이 자습서에 대한 전체 PowerShell 스크립트가 포함됩니다. **Connect-AzAccount** 및 **Select-AzSubscription** 명령을 사용하도록 이미 Azure 구독을 설정한 것으로 가정합니다.

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>다음 단계

가상 머신을 만든 후에는 다음을 수행할 수 있습니다.

- RDP를 사용하여 가상 머신에 연결
- 다음을 포함하여 VM에 대한 SQL Server 설정을 포털에서 구성합니다.
   - [스토리지 설정](storage-configuration.md) 
   - [자동화된 관리 작업](sql-server-iaas-agent-extension-automate-management.md)
- [연결 구성](ways-to-connect-to-sql.md).
- 새 SQL Server 인스턴스에 클라이언트 및 애플리케이션 연결
