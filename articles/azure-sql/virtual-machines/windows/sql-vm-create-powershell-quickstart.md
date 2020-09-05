---
title: Azure PowerShell을 사용하여 Windows 가상 머신에서 SQL Server 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure PowerShell을 사용하여 SQL Server 2017을 실행하는 Windows 가상 머신을 만드는 방법을 보여줍니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a458e0c03687df9d1cd53df69b8cf275da04174a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073679"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Windows 가상 머신에서 SQL Server 만들기

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 빠른 시작에서는 Azure PowerShell을 사용하여 SQL Server VM(가상 머신)을 만드는 과정을 안내합니다.

> [!TIP]
> - 이 빠른 시작에서는 신속하게 SQL VM을 프로비전하고 연결하는 경로를 제공합니다. SQL VM을 만들기 위한 다른 Azure PowerShell 옵션에 대한 자세한 내용은 [Azure PowerShell을 사용한 SQL Server VM 프로비전 가이드](create-sql-vm-powershell.md)를 참조하세요.
> - SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.md)을 참조하세요.

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Azure 구독 가져오기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Azure PowerShell 가져오기

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>PowerShell 구성

1. PowerShell을 열고 **Connect-AzAccount** 명령을 실행하여 Azure 계정에 대한 액세스를 설정합니다.

   ```powershell
   Connect-AzAccount
   ```

1. 로그인 창이 표시되면 자격 증명을 입력합니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

1. 고유한 리소스 그룹 이름을 사용하여 변수를 정의합니다. 빠른 시작의 나머지 부분을 단순화하기 위해 나머지 명령은 이 이름을 다른 리소스 이름의 기초로 사용합니다.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. 모든 VM 리소스에 대해 대상 Azure 지역의 위치를 정의합니다.

   ```powershell
   $Location = "East US"
   ```

1. 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>네트워크 설정 구성

1. 가상 네트워크, 서브넷 및 공용 IP 주소를 만듭니다. 이러한 리소스는 가상 머신에 네트워크 연결을 제공하고 인터넷에 연결하는 데 사용됩니다.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. 네트워크 보안 그룹을 만듭니다. RDP(원격 데스크톱) 및 SQL Server 연결을 허용하도록 규칙을 구성합니다.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. 네트워크 인터페이스를 만듭니다.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>SQL VM 만들기

1. 자격 증명을 정의하여 VM에 로그인합니다. 사용자 이름은 "azureadmin"입니다. 명령을 실행하기 전에 \<password>를 변경해야 합니다.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. 가상 컴퓨터 구성 개체를 만든 다음 VM을 만듭니다. 다음 명령은 Windows Server 2016에 SQL Server 2017 Developer Edition VM을 만듭니다.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > VM을 만드는 데 몇 분이 걸립니다.

## <a name="register-with-sql-vm-rp"></a>SQL VM RP에 등록 

포털 통합 및 SQL VM 기능을 가져오려면 [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md)에 등록해야 합니다.

전체 기능을 사용하려면 전체 모드에서 리소스 공급자에 등록해야 합니다. 그러나 이렇게 하면 SQL Server 서비스가 다시 시작되므로 경량 모드로 등록한 다음, 유지 관리 기간 동안 전체로 업그레이드하는 것이 좋습니다. 

먼저 SQL Server VM을 경량 모드로 등록합니다. 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

그런 다음, 유지 관리 기간 동안 전체 모드로 업그레이드합니다. 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL VM resource provider in full mode
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>VM에 원격 데스크톱으로 연결합니다.

1. 다음 명령을 사용하여 새 VM의 공용 IP 주소를 검색합니다.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. 반환된 IP 주소를 명령줄 매개 변수로서 **mstsc**에 전달하여 원격 데스크톱 세션을 새 VM에서 시작합니다.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. 자격 증명을 묻는 메시지가 표시되면 다른 계정의 자격 증명을 입력하도록 선택합니다. 백슬래시를 먼저 쓰고(예: `\azureadmin`) 이전에 이 빠른 시작에서 설정한 암호와 함께 사용자 이름을 입력합니다.

## <a name="connect-to-sql-server"></a>SQL Server에 연결

1. 원격 데스크톱 세션에 로그인한 다음, 시작 메뉴에서 **SQL Server Management Studio 2017**을 시작합니다.

1. **서버에 연결** 대화 상자에서 기본값을 유지합니다. 서버 이름은 VM 이름입니다. 인증은 **Windows 인증**으로 설정됩니다. **연결**을 선택합니다.

이제 SQL Server에 로컬로 연결됩니다. 원격으로 연결하려면 Azure Portal에서 또는 수동으로 [연결을 구성](ways-to-connect-to-sql.md)해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

VM을 계속해서 실행하지 않아도 되는 경우 사용 중이 아닌 VM을 중지하여 불필요한 요금을 방지할 수 있습니다. 다음 명령은 VM을 중지하지만 나중에 계속 사용할 수 있습니다.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

**Remove-AzResourceGroup** 명령을 사용하여 가상 머신과 관련된 모든 리소스를 영구적으로 삭제할 수도 있습니다. 그러면 가상 머신도 영구적으로 삭제되므로 이 명령을 사용할 때는 주의해야 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure PowerShell을 사용하여 SQL Server 2017 가상 머신을 만들었습니다. 새 SQL Server 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [SQL VM에 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)
