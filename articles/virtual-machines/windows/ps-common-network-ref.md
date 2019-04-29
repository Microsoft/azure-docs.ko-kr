---
title: Azure Virtual Networks에 대한 공통 PowerShell 명령 | Microsoft Docs
description: Vm에 대한 가상 네트워크 및 연결된 리소스 만들기를 시작하게 하는 공통 PowerShell 명령.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 020f2a4171a5bd656e53c91e59edb16931b20d0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597669"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Azure Virtual Networks에 대한 공통 PowerShell 명령

가상 머신을 만들려는 경우, [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 를 만들거나 VM을 추가할 수 있는 기존 가상 네트워크에 대해 알아야 합니다. 일반적으로 VM을 만들 때 이 문서에 설명된 리소스를 만드는 것이 좋습니다.

최신 버전의 Azure PowerShell 설치, 구독 선택, 자신의 계정에 로그인하는 방법에 대해서는 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/overview)을 참조하세요.

이 문서에 나오는 둘 이상의 명령을 실행하는 경우 다음과 같은 일부 변수가 유용할 수 있습니다.

- $location - 네트워크 리소스의 위치입니다. [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation)을 사용하여 사용자의 [지리적 하위 지역](https://azure.microsoft.com/regions/)을 찾을 수 있습니다.
- $myResourceGroup - 네트워크 리소스가 있는 리소스 그룹의 이름입니다.

## <a name="create-network-resources"></a>네트워크 리소스 만들기

| Task | 명령 |
| ---- | ------- |
| 서브넷 구성 만들기 |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>일반적인 네트워크는 [인터넷 연결 부하 분산 장치](../../load-balancer/load-balancer-internet-overview.md)에 대한 서브넷 및 [내부 부하 분산 장치](../../load-balancer/load-balancer-internal-overview.md)에 대한 별도 서브넷을 가질 수도 있습니다. |
| 가상 네트워크 만들기 |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| 고유한 도메인 이름에 대한 테스트 |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Location $location<BR><BR>Azure 관리 DNS 서버에서 공용 IP 주소에 대한 domainname.location.cloudapp.azure.com 매핑을 만드는 [공용 IP 리소스](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)에 대한 DNS 도메인 이름을 지정할 수 있습니다. 이 이름에는 문자, 숫자 및 하이픈만 포함할 수 있습니다. 첫 번째 및 마지막 문자는 문자 또는 숫자여야 하며 도메인 이름은 Azure 위치 내에서 고유해야 합니다. **True** 가 반환된 경우 제안한 이름이 전역적으로 고유합니다. |
| 공용 IP 주소 만들기 |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>공용 IP 주소는 이전에 테스트했고 부하 분산 장치의 프런트 엔드 구성에서 사용되는 도메인 이름을 사용합니다. |
| 프런트 엔드 IP 구성 만들기 |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>프런트 엔드 구성은 들어오는 네트워크 트래픽에 대해 이전에 만든 공용 IP 주소를 포함합니다. |
| 백 엔드 주소 풀 만들기 |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>네트워크 인터페이스를 통해 액세스 하는 부하 분산 장치의 백 엔드에 대한 내부 주소를 제공합니다. |
| 프로브 만들기 |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Name "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>백 엔드 주소 풀의 가상 머신 인스턴스 가용성을 확인하는 데 사용하는 상태 프로브를 포함합니다. |
| 부하 분산 규칙 만들기 |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>백 엔드 주소 풀에 있는 포트에 부하 분산 장치의 공용 포트를 할당하는 규칙을 포함합니다. |
| 인바운드 NAT 규칙 만들기 |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Name "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>백 엔드 주소 풀에서 특정 가상 머신에 대한 포트에 부하 분산 장치에는 공용 포트 매핑 규칙을 포함합니다. |
| 부하 분산 장치 만들기 |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Name "myLoadBalancer" -Location $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| 네트워크 인터페이스 만들기 |$nic1= [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Name "myNIC" -Location $location -PrivateIpAddress XX.X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>이전에 만든 공용 IP 주소 및 가상 네트워크 서브넷을 사용하여 네트워크 인터페이스를 만듭니다. |

## <a name="get-information-about-network-resources"></a>네트워크 리소스에 대한 정보 가져오기

| Task | 명령 |
| ---- | ------- |
| 가상 네트워크 나열 |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹의 모든 가상 네트워크를 나열합니다. |
| 가상 네트워크에 대한 정보 가져오기 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| 가상 네트워크의 서브넷 나열 |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| 서브넷에 대한 정보 가져오기 |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>지정된 가상 네트워크의 서브넷에 대한 정보를 가져옵니다. $vnet 값은 Get-AzVirtualNetwork에서 반환되는 개체를 나타냅니다. |
| IP 주소 나열 |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹의 공용 IP 주소를 나열합니다. |
| 부하 분산 장치 나열 |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹의 모든 부하 분산 장치를 나열합니다. |
| 네트워크 인터페이스 나열 |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹의 모든 네트워크 인터페이스를 나열합니다. |
| 네트워크 인터페이스에 대한 정보 가져오기 |Get-AzNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>특정 네트워크 인터페이스에 대한 정보를 가져옵니다. |
| 네트워크 인터페이스의 IP 구성 가져오기 |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Name "myNICIP" -NetworkInterface $nic<BR><BR>지정된 네트워크 인터페이스의 IP 구성에 대한 정보를 가져옵니다. $nic 값은 Get-AzNetworkInterface에서 반환되는 개체를 나타냅니다. |

## <a name="manage-network-resources"></a>네트워크 리소스 관리

| Task | 명령 |
| ---- | ------- |
| 가상 네트워크에 서브넷 추가 |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX.X.X.X/XX -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>기존 가상 네트워크에 서브넷을 추가합니다. $vnet 값은 Get-AzVirtualNetwork에서 반환되는 개체를 나타냅니다. |
| 가상 네트워크 삭제 |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹에서 지정된 가상 네트워크를 제거합니다. |
| 네트워크 인터페이스 삭제 |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹에서 지정된 네트워크 인터페이스를 제거합니다. |
| 부하 분산 장치 삭제 |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹에서 지정된 부하 분산 장치를 제거합니다. |
| 공용 IP 주소 삭제 |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>리소스 그룹에서 지정된 공용 IP 주소를 제거합니다. |

## <a name="next-steps"></a>다음 단계
* [VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 할 때 방금 만든 네트워크 인터페이스를 사용합니다.
* [여러 네트워크 인터페이스를 사용하여 VM 만들기](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md)를 할 수 있는 방법을 알아봅니다.

