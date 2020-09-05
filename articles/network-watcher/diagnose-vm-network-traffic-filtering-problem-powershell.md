---
title: '빠른 시작: VM 네트워크 트래픽 필터 문제 진단 - Azure PowerShell'
titleSuffix: Azure Network Watcher
description: 이 빠른 시작에서는 Azure Network Watcher의 IP 흐름 확인 기능을 사용하여 가상 머신 네트워크 트래픽 필터 문제를 진단하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 6d1c41df638c092daf24938931c3526fb18ea3db
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074783"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-powershell"></a>빠른 시작: 가상 머신 네트워크 트래픽 필터 문제 진단 - Azure PowerShell

이 빠른 시작에서 VM(가상 머신)을 배포한 다음, IP 주소와 URL 및 IP 주소에서 통신을 확인합니다. 통신 오류의 원인 및 해결 방법을 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하도록 선택한 경우 이 빠른 시작에는 Azure PowerShell `Az` 모듈이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.



## <a name="create-a-vm"></a>VM 만들기

VM을 만들려면 먼저 VM이 포함될 리소스 그룹을 만들어야 합니다. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

[New-AzVM](/powershell/module/az.compute/new-azvm)으로 VM을 만듭니다. 이 단계를 실행할 때 자격 증명을 묻는 메시지가 나타납니다. 입력하는 값은 VM에 대한 사용자 이름과 암호로 구성됩니다.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

VM을 만드는 데 몇 분이 걸립니다. VM이 만들어지고 PowerShell에서 출력을 반환할 때까지 나머지 단계를 진행하지 마십시오.

## <a name="test-network-communication"></a>네트워크 통신 테스트

Network Watcher와의 네트워크 통신을 테스트하려면 먼저 테스트하려는 VM이 있는 지역에서 Network Watcher를 사용하도록 설정한 다음, Network Watcher의 IP 흐름 확인 기능을 사용하여 통신을 테스트해야 합니다.

### <a name="enable-network-watcher"></a>네트워크 감시자 사용

미국 동부 지역에서 활성화된 네트워크 감시자가 이미 있는 경우 [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher)를 사용하여 네트워크 감시자를 검색합니다. 다음 예에서는 *NetworkWatcherRG* 리소스 그룹에 있는 *NetworkWatcher_eastus*라는 기존 네트워크 감시자를 검색합니다.

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

미국 동부 지역에서 활성화된 네트워크 감시자가 아직 없는 경우 [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher)를 사용하여 미국 동부 지역에서 네트워크 감시자를 만듭니다.

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-ip-flow-verify"></a>IP 흐름 확인 사용

VM을 만들 때 Azure는 기본적으로 VM 간 네트워크 트래픽을 허용하고 거부합니다. 추가 트래픽 형식을 허용하거나 거부하여 나중에 Azure의 기본값을 재정의할 수 있습니다. 트래픽이 다른 대상에 대해, 그리고 원본 IP 주소에서 허용되거나 거부되는지 여부를 테스트하려면 [Test-AzNetworkWatcherIPFlow](/powershell/module/az.network/test-aznetworkwatcheripflow) 명령을 사용합니다.

VM에서 www.bing.com 에 대한 IP 주소 중 하나로 아웃바운드 통신을 테스트합니다.

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 13.107.21.200 `
  -RemotePort 80
```

몇 초 후 반환되는 결과는 액세스가 **AllowInternetOutbound**라는 보안 규칙을 통해 허용됨을 알립니다.

VM에서 172.31.0.100으로 아웃바운드 통신을 테스트합니다.

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Outbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 60000 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 80
```

반환되는 결과는 액세스가 **DefaultOutboundDenyAll**이라는 보안 규칙을 통해 거부됨을 알립니다.

172.31.0.100에서 VM으로 인바운드 통신을 테스트합니다.

```azurepowershell-interactive
Test-AzNetworkWatcherIPFlow `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $vM.Id `
  -Direction Inbound `
  -Protocol TCP `
  -LocalIPAddress 192.168.1.4 `
  -LocalPort 80 `
  -RemoteIPAddress 172.31.0.100 `
  -RemotePort 60000
```

반환되는 결과는 액세스가 **DefaultInboundDenyAll**이라는 보안 규칙으로 인해 거부됨을 알립니다. 이제 VM 간 트래픽을 허용하거나 거부하는 보안 규칙을 알고 있으므로 문제를 해결하는 방법을 결정할 수 있습니다.

## <a name="view-details-of-a-security-rule"></a>보안 규칙의 세부 정보 보기

[네트워크 통신 테스트](#test-network-communication)의 규칙이 통신을 허용하거나 방지하는 이유를 결정하려면 [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)을 사용하여 네트워크 인터페이스에 대해 유효한 보안 규칙을 검토합니다.

```azurepowershell-interactive
Get-AzEffectiveNetworkSecurityGroup `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup
```

반환된 출력은 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 www.bing.com 에 대한 아웃바운드 액세스를 허용한 **AllowInternetOutbound** 규칙에 대한 다음 텍스트를 포함합니다.

```powershell
{
  "Name":
"defaultSecurityRules/AllowInternetOutBound",
  "Protocol": "All",
  "SourcePortRange": [
    "0-65535"
  ],
  "DestinationPortRange": [
    "0-65535"
  ],
  "SourceAddressPrefix": [
    "0.0.0.0/0"
  ],
  "DestinationAddressPrefix": [
    "Internet"
  ],
  "ExpandedSourceAddressPrefix": [],
  "ExpandedDestinationAddressPrefix": [
    "1.0.0.0/8",
    "2.0.0.0/7",
    "4.0.0.0/6",
    "8.0.0.0/7",
    "11.0.0.0/8",
    "12.0.0.0/6",
    ...
    ],
    "Access": "Allow",
    "Priority": 65001,
    "Direction": "Outbound"
  },
```

**DestinationAddressPrefix**가 **인터넷**인 출력에서 확인할 수 있습니다. 그러나 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 테스트한 주소인 13.107.21.200이 **인터넷**과 어떻게 관련되어 있는지 분명하지 않습니다. **ExpandedDestinationAddressPrefix** 아래에 여러 가지 주소 접두사가 나열됩니다. 목록의 접두사 중 하나는 IP 주소의 12.0.0.1-15.255.255.254 범위를 포함하는 **12.0.0.0/6**입니다. 13.107.21.200은 해당 주소 범위에 있으므로 **AllowInternetOutBound** 규칙은 아웃바운드 트래픽을 허용합니다. 또한 이 규칙을 재정의하는 `Get-AzEffectiveNetworkSecurityGroup`에서 반환된 출력에 나열된 더 높은 **우선 순위**(낮은 수) 규칙이 없습니다. 13.107.21.200에 대한 아웃바운드 통신을 거부하려면 IP 주소에 대한 포트 80 아웃바운드를 거부하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

`Test-AzNetworkWatcherIPFlow` 명령을 실행하여 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 172.131.0.100에 대한 아웃바운드 통신을 테스트한 경우 출력은 **DefaultOutboundDenyAll** 규칙이 통신을 거부했음을 알렸습니다. **DefaultOutboundDenyAll** 규칙은 `Get-AzEffectiveNetworkSecurityGroup` 명령에서 다음 출력에 나열된 **DenyAllOutBound** 규칙과 동일합니다.

```powershell
{
"Name": "defaultSecurityRules/DenyAllOutBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Outbound"
}
```

규칙은 **DestinationAddressPrefix**로 **0.0.0.0/0**을 나열합니다. 규칙은 주소가 `Get-AzEffectiveNetworkSecurityGroup` 명령의 출력에서 다른 아웃바운드 규칙의 **DestinationAddressPrefix** 내에 없기 때문에 172.131.0.100에 대한 아웃바운드 통신을 거부합니다. 아웃바운드 통신을 허용하려면 172.131.0.100에서 포트 80에 대한 아웃바운드 트래픽을 허용하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

`Test-AzNetworkWatcherIPFlow` 명령을 실행하여 [IP 흐름 확인 사용](#use-ip-flow-verify)에서 172.131.0.100의 인바운드 통신을 테스트한 경우 출력은 **DefaultInboundDenyAll** 규칙이 통신을 거부했음을 알렸습니다. **DefaultInboundDenyAll** 규칙은 `Get-AzEffectiveNetworkSecurityGroup` 명령에서 다음 출력에 나열된 **DenyAllInBound** 규칙과 동일합니다.

```powershell
{
"Name": "defaultSecurityRules/DenyAllInBound",
"Protocol": "All",
"SourcePortRange": [
  "0-65535"
],
"DestinationPortRange": [
  "0-65535"
],
"SourceAddressPrefix": [
  "0.0.0.0/0"
],
"DestinationAddressPrefix": [
  "0.0.0.0/0"
],
"ExpandedSourceAddressPrefix": [],
"ExpandedDestinationAddressPrefix": [],
"Access": "Deny",
"Priority": 65500,
"Direction": "Inbound"
},
```

출력에 표시된 것과 같이 172.131.0.100에서 VM에 대한 포트 80 인바운드를 허용하는 `Get-AzEffectiveNetworkSecurityGroup` 명령에서 출력에 더 높은 우선 순위 규칙이 없으므로 **DenyAllInBound** 규칙이 적용됩니다. 인바운드 통신을 허용하려면 172.131.0.100에서 포트 80 인바운드를 허용하는 더 높은 우선 순위를 가진 보안 규칙을 추가할 수 있습니다.

이 빠른 시작에서 검사는 Azure 구성을 테스트했습니다. 검사가 예상된 결과를 반환하고 여전히 네트워크 문제가 있는 경우 VM과 통신하고 있는 엔드포인트 간에 방화벽이 없고 VM의 운영 체제에 통신을 허용하거나 거부하는 방화벽이 없는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 해당 그룹에 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 VM을 만들고 인바운드 및 아웃바운드 네트워크 트래픽 필터를 진단했습니다. VM 간 트래픽을 허용하거나 거부하는 네트워크 보안 그룹 규칙을 배웠습니다. [보안 규칙](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 및 [보안 규칙을 만드는](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule) 방법에 대해 자세히 알아봅니다.

적절한 네트워크 트래픽 필터가 준비되어 있더라도 라우팅 구성으로 인해 VM에 대한 통신이 여전히 실패할 수 있습니다. VM 네트워크 라우팅 문제를 진단하는 방법을 알아보려면 [VM 라우팅 문제 진단](diagnose-vm-network-routing-problem-powershell.md)을 참조하거나 하나의 도구로 아웃바운드 라우팅, 대기 시간 및 트래픽 필터링 문제를 진단하려면 [연결 문제 해결](network-watcher-connectivity-powershell.md)을 참조하세요.
