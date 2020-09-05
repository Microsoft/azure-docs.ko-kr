---
title: WAF 사용자 지정 규칙을 사용하는 Azure PowerShell 스크립트 샘플
description: Azure PowerShell 스크립트 샘플 - Application Gateway 사용자 지정 규칙에서 Web Application Firewall 만들기
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: sample
ms.date: 09/30/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8d800e77ed0e609091d31d9b3177af447ac06f5b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079509"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Azure PowerShell을 사용하여 WAF 사용자 지정 규칙 만들기

이 스크립트는 사용자 지정 규칙을 사용하는 Application Gateway 웹 애플리케이션 방화벽을 만듭니다. 요청 헤더에 사용자 에이전트 *evilbot*이 포함되어 있으면 사용자 지정 규칙이 트래픽을 차단합니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-powershell-module"></a>Azure PowerShell 모듈

Azure PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 스크립트를 실행하려면 Azure PowerShell 모듈 버전 2.1.0 이상이 필요합니다.

1. 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요.
2. `Connect-AzAccount`를 실행하여 Azure와 연결합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, 애플리케이션 게이트웨이 및 모든 관련 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
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
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Application Gateway의 자동 크기 조정 구성을 만듭니다.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|방화벽 조건의 일치 변수를 만듭니다.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|사용자 지정 규칙의 일치 조건을 만듭니다.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|애플리케이션 게이트웨이 방화벽 정책에 대한 새로운 사용자 지정 규칙을 만듭니다.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|애플리케이션 게이트웨이 방화벽 정책을 만듭니다.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|애플리케이션 게이트웨이의 WAF 구성을 만듭니다.|

## <a name="next-steps"></a>다음 단계

- WAF 사용자 지정 규칙에 대한 자세한 내용은 [웹 애플리케이션 방화벽에 대한 사용자 지정 규칙](../ag/custom-waf-rules-overview.md)을 참조하세요.
- Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.
