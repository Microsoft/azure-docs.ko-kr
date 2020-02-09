---
title: 전방 도어에 대해 WAF rate limit rule 구성-Azure PowerShell
description: 기존 전방 도어 엔드포인트에 대 한 요율 제한 규칙을 구성 하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 831d0876c67aa36248a54f3935e5ce7884c736ef
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186617"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 요금 제한 규칙 구성
Azure Front 도어의 Azure 웹 응용 프로그램 방화벽 (WAF) rate limit 규칙은 1 분 동안 단일 클라이언트 IP에서 허용 되는 요청 수를 제어 합니다.
이 문서에서는 Azure PowerShell를 사용 하 여 단일 클라이언트에서 */promo 모션* 을 포함 하는 웹 응용 프로그램에 대 한 단일 클라이언트에서 허용 되는 요청 수를 제어 하는 waf rate limit 규칙을 구성 하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>선행 조건
속도 제한 정책 설정을 시작 하기 전에 PowerShell 환경을 설정 하 고 전면 도어 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용 하 여 로그인 하 고 Az PowerShell module을 설치 합니다.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인용 대화형 대화 상자를 사용하여 Azure에 연결
```
Connect-AzAccount

```
Front Door 모듈을 설치하기 전에 현재 버전의 PowerShellGet을 설치했는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Front Door 프로필 만들기
[빠른 시작: Front 도어 프로필 만들기](../../frontdoor/quickstart-create-front-door.md) 에 설명 된 지침에 따라 front 도어 프로필을 만듭니다.

## <a name="define-url-match-conditions"></a>Url 일치 조건 정의
[AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용 하 여 url 일치 조건 (url 포함/프로 모션)을 정의 합니다.
다음 예에서는 */프로 모션* 을 *RequestUri* 변수의 값으로 일치 시킵니다.

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>사용자 지정 요율 제한 규칙 만들기
[AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)를 사용 하 여 요율 제한을 설정 합니다. 다음 예제에서 제한은 1000로 설정 됩니다. 1 분 동안 1000를 초과 하는 클라이언트에서 프로 모션 페이지로의 요청은 다음 분이 시작 될 때까지 차단 됩니다.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzureRmResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 그런 다음, Front 도어 프로필을 포함 하는 지정 된 리소스 그룹에서 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 를 사용 하 여 사용자 지정 rate limit 규칙으로 보안 정책을 구성 합니다.

아래 예제에서는 [빠른 시작: 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공 된 지침을 사용 하 여 front 도어 프로필을 만들었다고 가정 하 고 리소스 그룹 이름 *myResourceGroupFD1* 를 사용 합니다.

 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)사용.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>프런트 도어 프런트 엔드 호스트에 정책 연결
기존 Front 도어 프런트 엔드 호스트에 보안 정책 개체를 연결 하 고 Front 도어 속성을 업데이트 합니다. 먼저 [AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 명령을 사용 하 여 Front 도어 개체를 검색 합니다.
그런 다음 [AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령을 사용 하 여 프런트 엔드 *WebApplicationFirewallPolicyLink* 속성을 이전 단계에서 만든 "$ratePolicy"의 *resourceId* 로 설정 합니다. 

아래 예제에서는 [빠른 시작: 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공 된 지침을 사용 하 여 front 도어 프로필을 만들었다고 가정 하 고 리소스 그룹 이름 *myResourceGroupFD1* 를 사용 합니다. 또한 아래 예제에서 $frontDoorName를 앞 도어 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> *WebApplicationFirewallPolicyLink* 속성을 한 번 설정 하 여 보안 정책을 프런트 도어 프런트 엔드에 연결 하기만 하면 됩니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- [전면 도어](../../frontdoor/front-door-overview.md)에 대해 자세히 알아보세요. 


