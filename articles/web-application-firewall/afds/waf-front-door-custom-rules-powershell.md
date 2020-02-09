---
title: Azure Front 문에 대 한 기본 규칙 집합 & WAF 사용자 지정 규칙 구성
description: WAF 정책을 구성 하는 방법에 대 한 자세한 내용은 기존 전방 도어 엔드포인트에 대 한 사용자 지정 및 관리 되는 규칙으로 구성 됩니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186643"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 웹 응용 프로그램 방화벽 정책 구성

Azure WAF (웹 응용 프로그램 방화벽) 정책은 요청이 전방 문에 도착할 때 필요한 검사를 정의 합니다.
이 문서에서는 일부 사용자 지정 규칙 및 Azure 관리 기본 규칙 집합 사용으로 구성 된 WAF 정책을 구성 하는 방법을 보여 줍니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>선행 조건

속도 제한 정책 설정을 시작 하기 전에 PowerShell 환경을 설정 하 고 전면 도어 프로필을 만듭니다.

### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정

Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명을 사용 하 여 로그인 하 고 Az PowerShell module을 설치 합니다.

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

[빠른 시작: Front 도어 프로필 만들기](../../frontdoor/quickstart-create-front-door.md) 에 설명 된 지침에 따라 front 도어 프로필을 만듭니다.

## <a name="custom-rule-based-on-http-parameters"></a>Http 매개 변수를 기반으로 하는 사용자 지정 규칙

다음 예제에서는 [AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject)를 사용 하 여 두 개의 일치 조건으로 사용자 지정 규칙을 구성 하는 방법을 보여 줍니다. 요청은 참조 페이지에 정의 된 대로 지정 된 사이트에서 시작 하 고 쿼리 문자열에는 "password"가 포함 되지 않습니다. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Http 요청 메서드를 기반으로 하는 사용자 지정 규칙

다음과 같이 [AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) 를 사용 하 여 "PUT" 메서드를 차단 하는 규칙을 만듭니다.

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>크기 제약 조건을 기반으로 사용자 지정 규칙 만들기

다음 예제에서는 Azure PowerShell를 사용 하 여 100 자 보다 긴 Url을 사용 하 여 규칙 차단 요청을 만듭니다.
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>관리 되는 기본 규칙 집합 추가

다음 예에서는 Azure PowerShell를 사용 하 여 관리 되는 기본 규칙 집합을 만듭니다.
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 다음으로, 이전 단계에서 만든 규칙을 사용 하 여 이전 단계에서 만든 규칙을 사용 하 여 보안 정책을 구성 합니다. 여기서는 [AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) 를 사용 하 여 지정 된 리소스 그룹에서 Front 도어 프로필

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>프런트 도어 프런트 엔드 호스트에 정책 연결

기존 Front 도어 프런트 엔드 호스트에 보안 정책 개체를 연결 하 고 Front 도어 속성을 업데이트 합니다. 먼저 [AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)를 사용 하 여 Front 도어 개체를 검색 합니다.
그런 다음 [AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)를 사용 하 여 프런트 엔드 *WebApplicationFirewallPolicyLink* 속성을 이전 단계에서 만든 "$MyWAFPolicy $"의 *resourceId* 로 설정 합니다. 

아래 예제에서는 [빠른 시작: 프런트 도어 만들기](../../frontdoor/quickstart-create-front-door.md) 문서에 제공 된 지침을 사용 하 여 front 도어 프로필을 만들었다고 가정 하 고 리소스 그룹 이름 *myResourceGroupFD1* 를 사용 합니다. 또한 아래 예제에서 $frontDoorName를 앞 도어 프로필의 이름으로 바꿉니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> *WebApplicationFirewallPolicyLink* 속성을 한 번 설정 하 여 보안 정책을 프런트 도어 프런트 엔드에 연결 하기만 하면 됩니다. 후속 정책 업데이트는 프런트 엔드에 자동으로 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- [전면 도어](../../frontdoor/front-door-overview.md) 에 대해 자세히 알아보기 
- [프런트 도어를 사용한 Waf](afds-overview.md) 에 대해 자세히 알아보기
