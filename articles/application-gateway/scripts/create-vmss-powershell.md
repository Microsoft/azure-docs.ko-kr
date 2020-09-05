---
title: Azure PowerShell 스크립트 샘플 - 웹 트래픽 관리 | Microsoft Docs
description: Azure PowerShell 스크립트 샘플 - 애플리케이션 게이트웨이 및 가상 머신 확장 집합을 사용하여 웹 트래픽을 관리합니다.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: a4f1961f9c7d79a95a0e8f4cc3fc18bdeac2f36f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078710"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Azure PowerShell을 사용하여 웹 트래픽 관리

이 스크립트는 백 엔드 서버에 가상 머신 확장 집합을 사용하는 애플리케이션 게이트웨이를 만듭니다. 그런 다음, 웹 트래픽을 관리하도록 애플리케이션 게이트웨이를 구성할 수 있습니다. 스크립트를 실행한 후에는 공용 IP 주소를 사용하여 애플리케이션 게이트웨이를 테스트할 수 있습니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | 서브넷 구성을 만듭니다. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | 서브넷 구성을 사용하여 가상 네트워크를 만듭니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 애플리케이션 게이트웨이의 공용 IP 주소를 만듭니다. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | 서브넷을 애플리케이션 게이트웨이에 연결하는 구성을 만듭니다. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | 애플리케이션 게이트웨이에 공용 IP 주소를 할당하는 구성을 만듭니다. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | 애플리케이션 게이트웨이에 액세스하는 데 사용할 포트를 할당합니다. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | 애플리케이션 게이트웨이의 백 엔드 풀을 만듭니다. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | 백 엔드 풀에 대한 설정을 구성합니다. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | 수신기를 만듭니다. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | 라우팅 규칙을 만듭니다. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | 애플리케이션 게이트웨이의 계층 및 용량을 지정합니다. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | 애플리케이션 게이트웨이를 만듭니다. |
| [집합 AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | 확장 집합의 스토리지 프로필을 만듭니다. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | 확장 집합의 운영 체제를 정의합니다. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | 확장 집합의 네트워크 인터페이스를 정의합니다. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | 가상 머신 확장 집합을 만듭니다. |
| [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) | 애플리케이션 게이트웨이의 공용 IP 주소를 가져옵니다. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. | 

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 애플리케이션 게이트웨이 PowerShell 스크립트 샘플은 [Azure Application Gateway 설명서](../powershell-samples.md)에서 찾을 수 있습니다.
