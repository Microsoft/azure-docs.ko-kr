---
title: Azure Application Gateway의 HTTP 헤더 다시 쓰기
description: 이 문서에서는 Azure PowerShell을 사용 하 여 Azure Application Gateway의 HTTP 헤더를 다시 작성 해야 하는 방법에 대해 설명
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64947197"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Azure Application Gateway-Azure PowerShell을 사용 하 여 HTTP 요청 및 응답 헤더를 다시 작성

이 문서에서는 Azure PowerShell을 사용 하 여 구성 하는 방법에 설명 합니다는 [Application Gateway v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 인스턴스 요청 및 응답의 HTTP 헤더를 다시 작성 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

- 이 문서의 단계를 완료 하려면 로컬 Azure PowerShell을 실행 해야 합니다. 나중에 설치 하거나 Az 모듈 버전 1.0.0 있어야 할 수도 있습니다. 실행 `Import-Module Az` 차례로 `Get-Module Az` 설치한 버전을 확인 합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. PowerShell 버전을 확인한 후 `Login-AzAccount`를 실행하여 Azure와의 연결을 만듭니다.
- Application Gateway v2 SKU 인스턴스 해야 합니다. 헤더를 다시 작성은 v1 SKU에서 지원 되지 않습니다. V2 SKU가 없는 경우 만듭니다는 [Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 시작 하기 전에 인스턴스.

## <a name="create-required-objects"></a>필요한 개체 만들기

HTTP 헤더 다시 쓰기를 구성 하려면 다음이 단계를 완료 해야 합니다.

1. HTTP 헤더 다시 쓰기에 필요한 개체를 만듭니다.

   - **RequestHeaderConfiguration**: 다시 작성 하려는 요청 헤더 필드와 헤더에 대한 새 값을 지정 하는 데 사용 합니다.

   - **ResponseHeaderConfiguration**: 다시 작성 하려는 응답 헤더 필드와 헤더에 대한 새 값을 지정 하는 데 사용 합니다.

   - **ActionSet**: 이전에 지정 된 요청 및 응답 헤더의 구성을 포함 합니다.

   - **조건**: 선택적 구성입니다. 다시 쓰기 조건이 HTTP (S) 요청 및 응답의 콘텐츠를 평가 합니다. 다시 작성 작업을 다시 작성 조건과 일치 하는 HTTP (S) 요청 또는 응답 하는 경우 발생 합니다.

     작업을 사용 하 여 하나 이상의 조건에 연결 하는 경우 모든 조건이 충족 되는 경우에 동작이 발생 합니다. 즉, 논리적 AND 연산을 작업이 나타납니다.

   - **RewriteRule**: 여러 다시 쓰기 작업을 포함 / 조건 조합을 다시 작성 합니다.

   - **RuleSequence**: 선택적 구성 하면 결정 하는 다시 쓰기 규칙 순서를 실행 합니다. 이 구성을 다시 쓰기 집합의 다시 쓰기 규칙이 여러 개 있는 경우에 유용 합니다. 낮은 규칙 순서 값이 있는 다시 쓰기 규칙을 먼저 실행 됩니다. 두 재작성 규칙에 동일한 규칙 순서 값을 할당 하는 경우 실행 순서가 명확 하지 않습니다.

     RuleSequence 명시적으로 지정 하지 않으면 기본값은 100이 설정 됩니다.

   - **RewriteRuleSet**: 요청 라우팅 규칙에 관련 된 여러 다시 쓰기 규칙을 포함 합니다.

2. 라우팅 규칙에는 RewriteRuleSet를 연결 합니다. 다시 쓰기 구성 라우팅 규칙을 통해 원본 수신기에 연결 됩니다. 기본 라우팅 규칙을 사용 하면 헤더 재작성 구성이 원본 수신기와 연결 된 이며 전체 머리글을 다시 작성 합니다. 경로 기반 라우팅 규칙을 사용 하는 경우 헤더 재작성 구성은 URL 경로 맵에 정의 됩니다. 이 경우 사이트의 특정 경로 영역에만 적용 됩니다.

여러 HTTP 헤더 재작성 집합을 만들고 여러 수신기를 설정 하는 각 다시 쓰기를 적용할 수 있습니다. 그러나 특정 수신기로 다시 작성 하나에 적용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>HTTP 헤더를 재작성 규칙 구성을 지정 합니다.

이 예제에서는 azurewebsites.net에 대한 참조를 포함 하는 location 헤더 때마다 HTTP 응답의 location 헤더를 다시 작성 하 여 리디렉션 URL을 수정 합니다. 이 위해 응답의 location 헤더 azurewebsites.net 포함 되는지 여부를 평가 하는 조건을 추가 합니다. 패턴을 사용 하 여 `(https?):\/\/.*azurewebsites\.net(.*)$`입니다. 사용 및 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 헤더 값으로. 이 값으로 대체 됩니다 *azurewebsites.net* 사용 하 여 *contoso.com* location 헤더에 있습니다.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Application gateway의 구성을 검색합니다

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>요청 라우팅 규칙의 구성을 검색 합니다.

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>HTTP 헤더를 다시 작성 하기 위한 구성을 사용 하 여 application gateway를 업데이트 합니다.

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>다시 쓰기 규칙 삭제

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>다음 단계

몇 가지 일반적인 사용 사례를 설정 하는 방법에 대한 자세한 내용은 참조 하세요 [공용 헤더 시나리오를 다시 작성](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)합니다.