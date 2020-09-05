---
title: NVA를 통한 트래픽 라우팅 - Azure PowerShell 스크립트 샘플
description: Azure PowerShell 스크립트 샘플 - 방화벽 네트워크 가상 어플라이언스를 통해 트래픽을 라우팅합니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f23514c9dd86491e94155db60add40b73bfcfb5
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074137"
---
# <a name="route-traffic-through-a-network-virtual-appliance-script-sample"></a>네트워크 가상 어플라이언스를 통한 트래픽 라우팅 스크립트 샘플

이 스크립트 샘플은 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 또한 두 서브넷 간에 트래픽을 라우팅할 수 있게 하는 IP 전달을 사용하여 VM을 만듭니다. 스크립트를 실행한 후에 방화벽 애플리케이션과 같은 네트워크 소프트웨어를 VM에 배포할 수 있습니다.

Azure [Cloud Shell](https://shell.azure.com/powershell) 또는 로컬 PowerShell 설치에서 스크립트를 실행할 수 있습니다. PowerShell을 로컬로 사용하는 경우 이 스크립트에는 Az PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 백 엔드 및 DMZ 서브넷을 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 가상 네트워크 인터페이스를 만들고 이 인터페이스를 위해 IP 전달을 사용하도록 설정합니다. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | NSG(네트워크 보안 그룹)을 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | VM에 대한 인바운드 HTTP 및 HTTPS 포트를 허용하는 NSG 규칙을 만듭니다. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| NSG 및 경로 테이블을 서브넷에 연결합니다. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| 모든 경로에 대한 경로 테이블을 만듭니다. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| VM을 통해 서브넷과 인터넷 간 트래픽을 라우팅하는 경로를 만듭니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만들고 NIC를 이 가상 머신에 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | 리소스 그룹 및 이 그룹에 속한 모든 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](https://docs.microsoft.com/powershell/azure/)를 참조하세요.

추가 가상 네트워크 PowerShell 스크립트 샘플은 [가상 네트워크 PowerShell 샘플](../powershell-samples.md)에 나와 있습니다.
