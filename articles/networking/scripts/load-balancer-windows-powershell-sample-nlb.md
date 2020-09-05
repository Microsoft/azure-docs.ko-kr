---
title: HA용 VM에 트래픽 부하 분산 - Azure PowerShell
description: Azure PowerShell 스크립트 샘플 - 고가용성을 위해 VM에 트래픽 부하 분산
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8663a5c86a4d3a247209fb7aedaa422eb6ec9e4
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079016"
---
# <a name="load-balance-traffic-to-vms-for-high-availability"></a>고가용성을 위해 VM에 트래픽 부하 분산

이 스크립트 샘플에서는 항상 사용 가능하고 부하 분산된 구성에서 구성된 여러 Windows 가상 머신을 실행하는 데 필요한 모든 항목을 만듭니다. 스크립트를 실행하면 3개의 가상 머신이 Azure 가용성 집합에 조인되고 Azure Load Balancer를 통해 액세스할 수 있습니다.

필요한 경우 [Azure PowerShell 가이드](https://docs.microsoft.com/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure Virtual Network 및 서브넷을 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | 고정 IP 주소 및 연결된 DNS 이름을 사용하여 공용 IP 주소를 만듭니다. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Azure Load Balancer를 만듭니다. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 부하 분산 장치 프로브를 만듭니다. 부하 분산 장치 프로브는 부하 분산 장치 집합의 각 VM을 모니터링하는 데 사용됩니다. 모든 VM이 액세스할 수 없게 되면 트래픽은 VM에 라우팅되지 않습니다. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 부하 분산 장치 규칙을 만듭니다. 이 샘플에서는 포트 80에 대한 규칙을 만듭니다. HTTP 트래픽이 부하 분산 장치에 도착하면 부하 분산 장치 집합에서 VM 중 하나인 포트 80에 라우팅됩니다. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 부하 분산 장치 NAT(네트워크 주소 변환) 규칙을 만듭니다.  NAT 규칙은 VM에 있는 포트에 부하 분산 장치의 포트를 매핑합니다. 이 샘플에서는 부하 분산 장치 집합의 각 VM에 SSH 트래픽에 대한 NAT 규칙을 만듭니다.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 인터넷과 가상 머신 간에 보안 경계인 NSG(네트워크 보안 그룹)을 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 인바운드 트래픽을 허용하도록 NSG 규칙을 만듭니다. 이 샘플에서 SSH 트래픽에 대해 포트 22가 열립니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 가상 네트워크 카드를 만들고 가상 네트워크, 서브넷 및 NSG에 연결합니다. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | 가용성 집합을 만듭니다. 가용성 집합을 사용하면 오류가 발생하는 경우 물리적 리소스 간에 가상 머신을 분산하여 애플리케이션 가동 시간을 확인합니다. 전체 집합은 영향을 받지 않습니다. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | 가상 머신을 만들고 네트워크 카드, 가상 네트워크, 서브넷 및 NSG에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 모든 중첩 리소스를 포함한 리소스 그룹을 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/)를 참조하세요.

추가 네트워킹 PowerShell 스크립트 샘플은 [Azure 네트워킹 개요 설명서](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json)에서 찾을 수 있습니다.
