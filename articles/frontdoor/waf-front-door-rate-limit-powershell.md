---
title: 프런트 도어-Azure PowerShell에 대 한 웹 응용 프로그램 방화벽 속도 제한 규칙 구성
description: 기존 프런트 도어 끝점에 대 한 속도 제한 규칙을 구성 하는 방법에 알아봅니다.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2019
ms.author: kumud;tyao
ms.openlocfilehash: e0ad1e85a4cd47de823bc4f224b5a8834b1068b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459320"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 웹 응용 프로그램 방화벽 속도 제한 규칙 구성
Azure 웹 응용 프로그램 방화벽 (WAF) 속도 제한 규칙에 대 한 Azure 프런트 도어 1 분 기간 동안 단일 클라이언트 IP의 허용 된 요청의 수를 제어 합니다.
이 문서에 포함 하는 웹 응용 프로그램에 단일 클라이언트에서 허용 되는 요청 수를 제어 하는 WAF 속도 제한 규칙을 구성 하는 방법을 보여 줍니다 */promo* Azure PowerShell을 사용 하 여 URL에 있습니다.

> [!IMPORTANT]
> WAF 속도 제한 규칙 기능에 대 한 Azure 프런트 도어는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
속도 제한 정책을 설정 하려면 먼저 PowerShell 환경을 설정 하 고 첫 번째 관문 프로필을 만듭니다.
### <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. Azure 자격 증명을 사용 하 여 로그인 페이지의 지침을 따르고 Az PowerShell 모듈을 설치 합니다.

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
[빠른 시작: 첫 번째 관문 프로필 만들기](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Url 일치 조건 정의
(URL /promo를 포함 하는 데 사용) 하는 URL 일치 조건 정의 사용 하 여 [새로 만들기-AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject)합니다.
다음 예제에서는 찾습니다 */promo* 값으로는 *RequestUri* 변수:

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>사용자 지정 속도로 제한 규칙 만들기
사용 하 여 속도 제한 설정 [새로 만들기-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject)합니다. 다음 예제에서는 제한은 1000으로 설정 됩니다. 1 분 동안 1000을 초과 프로 모션 페이지에 모든 클라이언트에서 요청은 다음 분에 시작 될 때까지 차단 됩니다.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>보안 정책 구성

`Get-AzureRmResourceGroup`을 사용하여 Front Door 프로필이 포함된 리소스 그룹의 이름을 찾습니다. 다음으로, 보안 정책을 사용 하 여 사용자 지정 속도로 제한 규칙 구성 [새로 만들기-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy) 프런트 도어 프로필이 포함 된 지정 된 리소스 그룹에 있습니다.

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1*을 사용합니다. [빠른 시작: Front Door 만들기](quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다.

 사용 하 여 [새로 만들기-AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy)합니다.

```powershell-interactive
   $ratePolicy = New-AzFrontDoorFireWallPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>첫 번째 관문 프런트 엔드 호스트에 연결 정책
기존 프런트 도어 프런트 엔드 호스트에 보안 정책 개체에 연결 하 고 첫 번째 관문 속성을 업데이트 합니다. 먼저 사용 하 여 첫 번째 관문 개체를 검색할 [Get AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) 명령입니다.
그런 다음 프런트 엔드를 설정 *WebApplicationFirewallPolicyLink* 속성을 *resourceId* 사용 하 여 이전 단계에서 만든 "$ratePolicy"의 [집합 AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 명령입니다. 

아래 예제에서는 리소스 그룹 이름으로 *myResourceGroupFD1*을 사용합니다. [빠른 시작: Front Door 만들기](quickstart-create-front-door.md) 문서에 제공된 지침을 사용하여 Front Door 프로필을 만들었다는 가정을 했기 때문입니다. 또한는 아래 예제에서는 $frontDoorName 프런트 도어 프로필의 이름으로 대체 합니다. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> 설정 해야 *WebApplicationFirewallPolicyLink* 프런트 엔드를 프런트 도어 보안 정책을 연결할 속성을 한 번입니다. 프런트 엔드에 후속 정책 업데이트 자동 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [프런트 도어](front-door-overview.md) 


