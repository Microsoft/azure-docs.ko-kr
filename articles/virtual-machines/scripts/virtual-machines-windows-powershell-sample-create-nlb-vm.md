---
title: Azure PowerShell 스크립트 샘플 - Windows VM NLB 만들기
description: Azure PowerShell 스크립트 샘플 - Windows VM NLB 만들기
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 2501a2476c1a26b09cbc33bb4e12463eb2c734d3
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078812"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>고가용성 가상 머신 간에 트래픽 부하 분산

이 스크립트 샘플에서는 항상 사용 가능하고 부하 분산된 구성에서 구성된 여러 Windows Server 2016 가상 머신을 실행하는 데 필요한 모든 항목을 만듭니다. 스크립트를 실행하면 3개의 가상 머신이 Azure 가용성 집합에 조인되고 Azure Load Balancer를 통해 액세스할 수 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Create VM NLB")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. 이 구성은 가상 네트워크 만들기 프로세스에서 사용됩니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 가상 네트워크를 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다. |
| [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) | 부하 분산 장치에 대한 프런트 엔드 IP 구성을 만듭니다. |
| [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) | 부하 분산 장치에 대한 백 엔드 주소 풀 구성을 만듭니다. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 부하 분산 장치에 대한 프로브 구성을 만듭니다. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 부하 분산 장치에 대한 규칙 구성을 만듭니다. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 부하 분산 장치에 대한 인바운드 NAT 규칙 구성을 만듭니다. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | 부하 분산 장치를 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | 네트워크 보안 그룹 규칙 구성을 만듭니다. 이 구성은 NSG가 만들어질 때 NSG 규칙을 만드는 데 사용됩니다. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 네트워크 보안 그룹을 만듭니다. |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | 서브넷 정보를 가져옵니다. 이 정보는 네트워크 인터페이스를 만들 때 사용됩니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 네트워크 인터페이스를 만듭니다. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만듭니다. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

사용자 고유의 관리되는 사용자 지정 이미지를 사용하여 VM을 만들 수도 있습니다. VM 구성에서 `Set-AzVMSourceImage`의 경우 `-PublisherName`, `-Offer`, `-Skus` 및 `-Version` 대신 `-Id` 및 `-VM` 매개 변수를 사용합니다.

예를 들어 VM 구성을 만드는 방법은 다음과 같습니다.

```powershell
$vmConfig = New-AzVMConfig -VMName 'myVM3' -VMSize Standard_DS1_v2 -AvailabilitySetId $as.Id | `
  Set-AzVMOperatingSystem -Windows -ComputerName 'myVM3' -Credential $cred | `
  Set-AzVMSourceImage -Id <Image.ID of the custom managed image> | Add-AzVMNetworkInterface -Id $nicVM3.Id
 ```

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
