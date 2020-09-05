---
title: Azure PowerShell 샘플 - 전체 가상 머신 확장 집합 만들기
description: 이 스크립트는 개별 리소스가 구성 및 생성되는 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만듭니다.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: powershell
ms.date: 05/29/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: d37f5f624459db6bc336884987a16c60503492a8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078506"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 전체 가상 머신 확장 집합 만들기

이 스크립트는 Windows Server 2016을 실행하는 가상 머신 확장 집합을 만듭니다. [New-AzVmss에서 여기에 사용 가능한 기본 제공 리소스 생성 옵션](powershell-sample-create-simple-scale-set.md)을 사용하는 대신 개별 리소스가 구성되고 생성됩니다. 스크립트를 실행한 후에는 RDP를 통해 VM 인스턴스에 액세스할 수 있습니다.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName
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
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | 부하 분산 장치에 대한 인바운드 NAT 규칙 구성을 만듭니다. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) | 부하 분산 장치를 만듭니다. |
| [Add-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | 부하 분산 장치에 대한 프로브 구성을 만듭니다. |
| [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | 부하 분산 장치에 대한 규칙 구성을 만듭니다. |
| [Set-AzLoadBalancer](/powershell/module/az.Network/Set-azLoadBalancer) | 제공된 정보로 부하 분산 장치를 업데이트합니다. |
| [New-AzVmssIpConfig](/powershell/module/az.Compute/New-azVmssIpConfig) | 확장 집합 VM 인스턴스에 대한 IP 구성을 만듭니다. VM 인스턴스는 부하 분산 장치 백 엔드 풀, NAT 풀 및 가상 네트워크 서브넷에 연결됩니다. |
| [New-AzVmssConfig](/powershell/module/az.Compute/New-azVmssConfig) | 확장 집합 구성을 만듭니다. 이 구성에는 만들 VM 인스턴스 수, VM SKU(크기) 및 업그레이드 정책 모드와 같은 정보가 포함됩니다. 구성은 추가 cmdlet에 의해 추가되고, 확장 집합 생성 중에 사용됩니다. |
| [집합 AzVmssStorageProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | VM 인스턴스에 사용할 이미지를 정의하고 확장 집합 구성에 추가합니다. |
| [Set-AzVmssOsProfile](/powershell/module/az.Compute/Set-azVmssStorageProfile) | 관리 사용자 이름과 암호 자격 증명 및 VM 이름 지정 접두사를 정의합니다. 확장 집합 구성에 이러한 값을 추가합니다. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.Compute/Add-azVmssNetworkInterfaceConfiguration) | IP 구성에 따라 가상 네트워크 인터페이스를 VM 인스턴스에 추가합니다. 확장 집합 구성에 이러한 값을 추가합니다. |
| [New-AzVmss](/powershell/module/az.Compute/New-azVmss) | 확장 집합 구성에서 제공된 정보에 따라 확장 집합을 만듭니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.
