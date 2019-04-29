---
title: 사용자 지정 규칙 및 첫 번째 관문-Azure PowerShell에 대 한 기본 루즈 집합을 사용 하 여 웹 응용 프로그램 방화벽 (WAF) 정책 구성
description: WAF를 구성 하는 방법은 기존 프런트 도어 끝점에 대 한 사용자 지정 및 관리 되는 규칙의 정책으로 구성 됩니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: kumud;tyao
ms.openlocfilehash: 7d024dd958e6b29b52f095a9a55a67154bf6cde6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459796"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 웹 응용 프로그램 방화벽 정책 구성
Azure 웹 응용 프로그램 방화벽 (WAF) 정책 정의 검사 요청이 첫 번째 관문에서 도착 하는 경우에 필요 합니다.
이 문서에서는 Azure 관리 되는 기본 루즈 집합으로 사용 하도록 설정 하 고 일부 사용자 지정 규칙의 구성 된 WAF 정책을 구성 하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
속도 제한 정책을 설정 하려면 먼저 PowerShell 환경을 설정 하 고 첫 번째 관문 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. Azure 자격 증명을 사용 하 여 로그인 페이지의 지침을 따르고 Az PowerShell 모듈을 설치 합니다.

#### <a name="sign-in-to-azure"></a>Azure에 로그인
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
[빠른 시작: 첫 번째 관문 프로필 만들기](quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Http 매개 변수를 기준으로 사용자 지정 규칙

다음 예제에서는 사용자 지정 규칙을 사용 하 여 두 가지 일치 조건을 구성 하는 방법을 보여 줍니다 [새로 만들기-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject)합니다. 참조 페이지에 정의 된 대로 요청은 지정한 사이트에서 및 쿼리 문자열 "암호"를 포함 되지 않습니다. 

```powershell-interactive
$referer = New-AzFrontDoorMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Http 요청 메서드를 기준으로 사용자 지정 규칙
"PUT" 메서드를 사용 하 여 차단 하는 규칙을 만들 [새로 만들기-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) 다음과 같습니다.

```powershell-interactive
$put = New-AzFrontDoorMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>크기 제약 조건을 기반으로 사용자 지정 규칙 만들기

다음 예제에서는 Azure PowerShell을 사용 하는 100 자 보다 긴 Url 사용 하 여 요청을 차단 하는 규칙을 만듭니다.
```powershell-interactive
$url = New-AzFrontDoorMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>관리 되는 기본 규칙 집합 추가

다음 예제에서는 관리 되는 기본 규칙 집합을 Azure PowerShell을 사용 하 여 만듭니다.
```powershell-interactive
$managedRules = New-AzFrontDoorManagedRuleObject -Type DefaultRuleSet -Version "preview-0.1"
```
## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 다음으로 사용 하 여 이전 단계에서 만든된 규칙을 사용 하 여 보안 정책을 구성 [새로 만들기-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) 프런트 도어 프로필이 포함 된 지정 된 리소스 그룹에.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorFireWallPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>첫 번째 관문 프런트 엔드 호스트에 연결 정책
기존 프런트 도어 프런트 엔드 호스트에 보안 정책 개체에 연결 하 고 첫 번째 관문 속성을 업데이트 합니다. 먼저 사용 하 여 첫 번째 관문 개체를 검색할 [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)합니다.
그런 다음 프런트 엔드를 설정 *WebApplicationFirewallPolicyLink* 속성을 합니다 *resourceId* 사용 하 여 이전 단계에서 만든 "$myWAFPolicy $" [집합-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1*을 사용합니다. [빠른 시작: Front Door 만들기](quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다. 또한는 아래 예제에서는 $frontDoorName 프런트 도어 프로필의 이름으로 대체 합니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 설정 해야 *WebApplicationFirewallPolicyLink* 프런트 엔드를 프런트 도어 보안 정책을 연결할 속성을 한 번입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [프런트 도어](front-door-overview.md) 
- 자세한 내용은 [프런트 도어에 대 한 WAF](waf-overview.md)
