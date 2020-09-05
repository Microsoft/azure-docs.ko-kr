---
title: 다중 계층 애플리케이션을 위한 VNet 만들기 - Azure PowerShell 스크립트 샘플
description: Azure PowerShell 스크립트 샘플 - 다중 계층 애플리케이션을 위한 가상 네트워크를 만듭니다.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c592681cc2aeb900a5b7e370277aa0d148bb37e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078489"
---
# <a name="create-a-network-for-multi-tier-applications-script-sample"></a>다중 계층 애플리케이션을 위한 네트워크 만들기 스크립트 샘플

이 스크립트 샘플은 프런트 엔드 및 백 엔드 서브넷이 있는 가상 네트워크를 만듭니다. 프런트 엔드 서브넷에 대한 트래픽은 HTTP 및 SSH로 제한되며, 백 엔드 서브넷에 대한 트래픽은 MySQL(3306 포트)로 제한됩니다. 스크립트를 실행한 후에는 웹 서버와 MySQL 소프트웨어를 배포할 수 있는 두 개의 가상 머신이 각 서브넷에 하나씩 있게 됩니다.

Azure [Cloud Shell](https://shell.azure.com/powershell) 또는 로컬 PowerShell 설치에서 스크립트를 실행할 수 있습니다. PowerShell을 로컬로 사용하는 경우 이 스크립트에는 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!-- gitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/17748 -->
서브넷 ID는 가상 네트워크를 만든 후 -Subnet 옵션이 포함된 New-AzVirtualNetwork cmdlet을 사용하여 할당됩니다. New-AzVirtualNetwork를 호출하기 전에 New-AzVirtualNetworkSubnetConfig cmdlet을 사용하여 서브넷을 구성하는 경우 서브넷 ID는 New-AzVirtualNetwork를 호출할 때까지 표시되지 않습니다.

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 네트워크 및 네트워크 보안 그룹을 만듭니다. 다음 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Azure 가상 네트워크 및 프런트 엔드 서브넷을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 백 엔드 서브넷을 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 인터넷에서 VM에 액세스하기 위한 공용 IP 주소를 만듭니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 가상 네트워크 인터페이스를 만들고 가상 네트워크의 프런트 엔드 및 백 엔드 서브넷에 연결합니다. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | 프런트 엔드 및 백 엔드 서브넷과 연결되는 NSG(네트워크 보안 그룹)을 만듭니다. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |특정 포트를 특정 서브넷에 허용하거나 차단하는 NSG 규칙을 만듭니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만들고 각 VM에 NIC를 연결합니다. 또한 이 명령은 사용할 가상 머신 이미지와 관리 자격 증명을 지정합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 이 그룹에 속한 모든 리소스를 삭제합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 네트워크 PowerShell 스크립트 샘플은 [가상 네트워크 PowerShell 샘플](../powershell-samples.md)에 나와 있습니다.
