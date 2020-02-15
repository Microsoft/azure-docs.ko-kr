---
title: Azure Active Directory에서 조건부 액세스 정책 계획 Microsoft Docs
description: 이 문서에서는 Azure Active Directory에 대 한 조건부 액세스 정책을 계획 하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martincoetzer
ms.collection: M365-identity-device-management
ms.openlocfilehash: 266fa2403ef96e808a0c1f1eb46b4f7065c06252
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185897"
---
# <a name="how-to-plan-your-conditional-access-deployment-in-azure-active-directory"></a>방법: Azure Active Directory에서 조건부 액세스 배포 계획

조직의 앱 및 리소스에 대 한 필수 액세스 전략을 달성할 수 있도록 조건부 액세스 배포 계획을 세우는 것이 중요 합니다. 배포의 계획 단계에서 대부분의 시간을 사용 하 여 선택한 조건에 따라 사용자에 게 액세스를 부여 하거나 차단 하는 데 필요한 다양 한 정책을 디자인 합니다. 이 문서에서는 안전 하 고 효과적인 조건부 액세스 정책을 구현 하기 위해 수행 해야 하는 단계를 설명 합니다. 시작 하기 전에 [조건부 액세스](overview.md) 의 작동 방식 및 사용 시기를 이해 해야 합니다.

## <a name="what-you-should-know"></a>알아야 할 사항

조건부 액세스는 독립 실행형 기능 대신 조직의 앱 및 리소스에 대 한 액세스를 제어할 수 있는 프레임 워크로 간주 됩니다. 따라서 일부 조건부 액세스 설정의 경우 추가 기능을 구성 해야 합니다. 예를 들어 특정 [로그인 위험 수준](../identity-protection/howto-identity-protection-configure-risk-policies.md)에 응답하는 정책을 구성할 수 있습니다. 그러나 로그인 위험 수준을 기반으로 하는 정책의 경우 [Azure Active Directory ID 보호](../identity-protection/overview-identity-protection.md)를 사용하도록 설정해야 합니다.

추가 기능이 필요한 경우 관련 라이선스를 얻어야 할 수도 있습니다. 예를 들어 조건부 액세스가 P1 기능을 Azure AD Premium 하는 동안 id 보호에 Azure AD Premium P2 라이선스가 필요 합니다.

조건부 액세스 정책에는 기준선과 표준 이라는 두 가지 유형이 있습니다. [기준 정책은](baseline-protection.md) 미리 정의 된 조건부 액세스 정책입니다. 기준 수준 이상의 보안을 유지하는 것이 이 정책의 목표입니다. 기준 정책. 기준 정책은 모든 Azure AD 버전에서 사용할 수 있으며 제한된 사용자 지정 옵션만 제공합니다. 시나리오에 유연성이 더 필요한 경우 기준 정책을 사용하지 않도록 설정하고 사용자 지정 표준 정책에서 요구 사항을 구현합니다.

표준 조건부 액세스 정책에서 모든 설정을 사용자 지정 하 여 비즈니스 요구 사항에 대 한 정책을 조정할 수 있습니다. 표준 정책에는 Azure AD Premium P1 라이선스가 필요합니다.

## <a name="draft-policies"></a>초안 정책

조건부 액세스를 Azure Active Directory 하 여 클라우드 앱을 새로운 수준으로 보호할 수 있습니다. 이 새로운 수준에서는 정적 액세스 구성 대신 동적 정책 평가를 기준으로 클라우드 앱에 액세스할 수 있습니다. 조건부 액세스 정책을 사용 하 여 액세스 조건 (**이 경우**)에 대 한**응답 (이 경우)을**정의 합니다.

![이유 및 응답](./media/plan-conditional-access/10.png)

이 계획 모델을 사용 하 여 구현 하려는 모든 조건부 액세스 정책을 정의 합니다. 계획 연습은 다음을 수행합니다.

- 각 정책에 대한 응답 및 조건의 개요 작성에 도움이 됩니다.
- 조직에 대해 잘 문서화 된 조건부 액세스 정책 카탈로그가 생성 됩니다. 

카탈로그를 사용하여 정책 구현이 조직의 비즈니스 요구 사항을 반영하는지 여부를 평가할 수 있습니다. 

다음 예제 템플릿을 사용 하 여 조직에 대 한 조건부 액세스 정책을 만듭니다.

|조건:|수행할 작업:|
|-|-|
|다음에 대한 액세스가 시도됩니다.<br>- 클라우드 앱<br>- 사용자 및 그룹에 의해<br>사용:<br>- 조건 1(예: 회사 네트워크 외부)<br>- 조건 2(예: 디바이스 플랫폼)|애플리케이션에 대한 액세스 차단|
|다음에 대한 액세스가 시도됩니다.<br>- 클라우드 앱<br>- 사용자 및 그룹에 의해<br>사용:<br>- 조건 1(예: 회사 네트워크 외부)<br>- 조건 2(예: 디바이스 플랫폼)|다음을 사용하여 액세스 권한 부여(AND):<br>- 요구 사항 1(예: MFA)<br>- 요구 사항 2(예: 디바이스 규정 준수)|
|다음에 대한 액세스가 시도됩니다.<br>- 클라우드 앱<br>- 사용자 및 그룹에 의해<br>사용:<br>- 조건 1(예: 회사 네트워크 외부)<br>- 조건 2(예: 디바이스 플랫폼)|다음을 사용하여 액세스 권한 부여(OR):<br>- 요구 사항 1(예: MFA)<br>- 요구 사항 2(예: 디바이스 규정 준수)|

최소한 **조건**은 클라우드 앱(**what**)에 액세스를 시도하는 보안 주체(**who**)를 정의합니다. 필요한 경우 액세스 시도가 수행되는 **방법**을 포함할 수도 있습니다. 조건부 액세스에서 조건을 정의 하는 요소 (조건)를 정의 하는 요소입니다. 자세한 내용은 [조건부 액세스 Azure Active Directory의 조건 이란?](concept-conditional-access-conditions.md) 을 참조 하세요. 

**수행할 작업**을 사용하여 액세스 조건에 대한 정책의 응답을 정의합니다. 응답에서 추가 요구 사항(예: MFA(다단계 인증))을 통해 액세스 권한을 부여하거나 차단합니다. 전체 개요는 [Azure Active Directory 조건부 액세스의 액세스 제어 란?](controls.md) 을 참조 하세요.  

조건과 액세스 제어의 조합이 조건부 액세스 정책을 나타냅니다.

![이유 및 응답](./media/plan-conditional-access/51.png)

자세한 내용은 [정책 적용에 필요한 사항](best-practices.md#whats-required-to-make-a-policy-work)을 참조하세요.

이 시점에서 정책의 명명 표준을 결정하는 것이 좋습니다. 이름 지정 표준은 정책을 찾고 Azure 관리 포털에서 열지 않고도 해당 용도를 파악하는 데 도움이 됩니다. 표시할 정책 이름:

- 시퀀스 번호
- 정책이 적용되는 클라우드 앱
- 응답
- 정책이 적용되는 대상
- 정책이 적용되는 시기(해당하는 경우)
 
![이름 지정 표준](./media/plan-conditional-access/11.png)

설명 하는 이름을 사용 하면 조건부 액세스 구현에 대 한 개요를 유지할 수 있지만 대화에서 정책을 참조 해야 하는 경우 시퀀스 번호는 유용 합니다. 예를 들어 휴대폰에서 동료 관리자에 게 연락 하는 경우 정책 EM063을 열도록 요청 하 여 문제를 해결할 수 있습니다.

예를 들어, 다음 이름은 정책에 따라 Dynamics CRP 앱을 사용할 때 외부 네트워크에서 마케팅 사용자에게 MFA를 요구함을 나타냅니다.

`CA01 - Dynamics CRP: Require MFA For marketing When on external networks`

활성 정책 외에도 [중단/응급 시나리오에서 복원력 있는 보조 액세스 제어](../authentication/concept-resilient-controls.md)로 작동하지만 사용하지 않도록 설정된 정책도 구현하는 것이 좋습니다. 대응 정책에 대한 명명 표준에 추가로 포함되어야 하는 항목은 다음과 같습니다. 

- 다른 정책 중에서 해당 이름이 눈에 띄도록 앞부분에 나오는 `ENABLE IN EMERGENCY`
- 적용해야 하는 중단의 이름
- 관리자가 사용하도록 설정해야 하는 순서 정책을 파악하는 데 도움이 되는 순서 지정 시퀀스 번호 

예를 들어 다음 이름은이 정책이 MFA 중단이 발생 한 경우 사용 하도록 설정 해야 하는 4 개 중 첫 번째 정책 임을 나타냅니다.

`EM01 - ENABLE IN EMERGENCY, MFA Disruption[1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users`

## <a name="plan-policies"></a>정책 계획

조건부 액세스 정책 솔루션을 계획할 때 다음 결과를 얻기 위해 정책을 만들어야 하는지 여부를 평가 합니다. 

### <a name="block-access"></a>액세스 차단

액세스 차단 옵션은 다음과 같은 이유로 강력합니다.

- 사용자에 대한 기타 모든 할당보다 우선함
- 전체 조직이 테넌트에 로그온할 수 없도록 차단함
 
모든 사용자에 대해 액세스를 차단하려는 경우 정책에서 하나 이상의 사용자(일반적으로 응급 액세스 계정)를 제외해야 합니다. 자세한 내용은 [사용자 및 그룹 선택](block-legacy-authentication.md#select-users-and-cloud-apps)을 참조하세요.  

### <a name="require-mfa"></a>MFA 요구

사용자의 로그인 환경을 간소화하기 위해 사용자가 사용자 이름 및 암호를 사용하여 클라우드 앱에 로그인하도록 허용할 수 있습니다. 그러나 일반적으로 보다 강력한 형태의 계정 확인을 요구하는 것이 좋은 몇 가지 시나리오가 있습니다. 조건부 액세스 정책을 사용 하 여 특정 시나리오에 대 한 MFA 요구 사항을 제한할 수 있습니다. 

MFA를 요구하는 일반적인 사용 사례는 다음과 같은 액세스입니다.

- [관리자에 의한 액세스](howto-baseline-protect-administrators.md)
- [특정 앱에 대한 액세스](app-based-mfa.md) 
- [신뢰하지 않는 네트워크 위치에서의 액세스](untrusted-networks.md)

### <a name="respond-to-potentially-compromised-accounts"></a>잠재적으로 손상된 계정에 대한 대응

조건부 액세스 정책을 사용 하 여 잠재적으로 손상 된 id에서 로그인에 대 한 자동화 된 응답을 구현할 수 있습니다. 계정이 손상되었을 확률은 위험 수준 형태로 표시됩니다. ID 보호를 통해 계산되는 두 가지 위험 수준(로그인 위험 및 사용자 위험)이 있습니다. 로그인 위험에 대한 응답을 구현하려면 다음 두 가지 옵션이 있습니다.

- 조건부 액세스 정책의 [로그인 위험 조건](concept-conditional-access-conditions.md#sign-in-risk)
- ID 보호의 [로그인 위험 정책](../identity-protection/howto-sign-in-risk-policy.md) 

더 많은 사용자 지정 옵션을 제공하므로 로그인 위험을 조건으로 처리하는 방법이 더 좋습니다.

사용자 위험 수준은 ID 보호에서 [사용자 위험 정책](../identity-protection/howto-user-risk-policy.md)으로만 사용할 수 있습니다. 

자세한 내용은 [Azure Active Directory Identity Protection이란?](../identity-protection/overview.md)을 참조하세요. 

### <a name="require-managed-devices"></a>관리 디바이스 필요

클라우드 리소스에 액세스하도록 지원되는 디바이스의 범위가 넓어지면 사용자의 생산성을 개선할 수 있습니다. 반대로 환경에서 특정 리소스에 알려지지 않은 보호 수준을 가진 디바이스가 액세스하지 않도록 하는 것이 좋습니다. 영향을 받는 리소스의 경우 사용자가 관리 디바이스를 사용해서만 액세스할 수 있도록 해야 합니다. 자세한 내용은 [조건부 액세스를 사용 하 여 cloud app access에서 관리 되는 장치를 요구 하는 방법](require-managed-devices.md)을 참조 하세요. 

### <a name="require-approved-client-apps"></a>승인된 클라이언트 앱 필요

BYOD(Bring Your Own Device) 시나리오에 대해 결정해야 하는 첫 번째 사항 중 하나는 전체 디바이스를 관리해야 하는지 아니면 디바이스의 데이터만 관리해야 하는지 여부입니다. 직원은 개인 및 회사 작업에 대해 모바일 디바이스를 사용합니다. 직원이 생산적이 될 수 있도록 하는 반면 데이터 손실을 방지하려고 합니다. Azure Active Directory (Azure AD) 조건부 액세스를 사용 하 여 회사 데이터를 보호할 수 있는 승인 된 클라이언트 앱으로 클라우드 앱에 대 한 액세스를 제한할 수 있습니다. 자세한 내용은 [조건부 액세스를 사용 하 여 cloud app access에서 승인 된 클라이언트 앱을 요구 하는 방법](app-based-conditional-access.md)을 참조 하세요.

### <a name="block-legacy-authentication"></a>레거시 인증 차단

Azure AD는 레거시 인증을 포함하여 가장 널리 사용되는 몇 가지 인증 및 권한 부여 프로토콜을 지원합니다. 레거시 인증을 사용하는 애플리케이션에서 테넌트의 리소스에 액세스하지 못하도록 방지하려면 어떻게 해야 할까요? 조건부 액세스 정책을 사용 하 여 차단 하는 것이 좋습니다. 필요한 경우 특정 사용자 및 특정 네트워크 위치만 레거시 인증을 기반으로 하는 애플리케이션을 사용하도록 허용합니다. 자세한 내용은 [조건부 액세스를 사용 하 여 AZURE AD에 대 한 레거시 인증을 차단 하는 방법](block-legacy-authentication.md)을 참조 하세요.

## <a name="test-your-policy"></a>정책 테스트

정책을 프로덕션 환경에 배포하기 전에 테스트하여 정책이 예상대로 작동하는지 확인해야 합니다.

1. 테스트 사용자 만들기
1. 테스트 계획 만들기
1. 정책 구성
1. 시뮬레이션된 로그인 평가
1. 정책 테스트
1. 정리

### <a name="create-test-users"></a>테스트 사용자 만들기

정책을 테스트하려면 환경의 사용자와 유사한 사용자 세트를 만듭니다. 테스트 사용자를 만들면 실제 사용자에게 영향을 주고 앱 및 리소스에 대한 액세스를 잠재적으로 손상하기 전에 정책이 예상대로 작동하는지 확인할 수 있습니다. 

일부 조직에는 이러한 용도의 테스트 테넌트가 있습니다. 그러나 정책의 결과를 완전히 테스트하기 위해 테스트 테넌트에서 모든 조건과 앱을 다시 만들기 어려울 수 있습니다. 

### <a name="create-a-test-plan"></a>테스트 계획 만들기

테스트 계획은 예상 결과와 실제 결과 간에 비교하는 것이 중요합니다. 그러므로 항상 테스트 전에 결과를 예상해야 합니다. 다음 표에는 예제 테스트 사례의 개요가 나와 있습니다. CA 정책이 구성된 방식에 따라 시나리오와 예상 결과를 조정합니다.

|정책 |시나리오 |예상 결과 | 결과 |
|---|---|---|---|
|[직장이 아닐 때 MFA 요구](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|권한 있는 사용자가 신뢰할 수 있는 위치/직장에서 ‘앱’에 로그인함|사용자에게 MFA에 대한 메시지가 표시되지 않음| |
|[직장이 아닐 때 MFA 요구](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)|권한 있는 사용자가 신뢰할 수 있는 위치/직장이 아닌 곳에서 ‘앱’에 로그인함|사용자에게 MFA에 대한 메시지가 표시되고 성공적으로 로그인할 수 있음| |
|[MFA 요구(관리자의 경우)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)|전역 관리자가 ‘앱’에 로그인함|관리자에게 MFA에 대한 메시지가 표시됨| |
|[위험한 로그인](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)|사용자가 [Tor 브라우저](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-playbook)를 사용하여 ‘앱’에 로그인함|관리자에게 MFA에 대한 메시지가 표시됨| |
|[디바이스 관리](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|권한 있는 사용자가 권한 있는 디바이스에서 로그인하려고 시도함|액세스 권한 부여됨| |
|[디바이스 관리](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|권한 있는 사용자가 권한 없는 디바이스에서 로그인하려고 시도함|액세스 차단됨| |
|[위험한 사용자에 대한 암호 변경](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)|권한 있는 사용자가 손상된 자격 증명을 사용하여 로그인하려고 시도함(높은 위험 로그인)|사용자에게 암호를 변경하라는 메시지가 표시되거나 정책에 따라 액세스가 차단됨| |

### <a name="configure-the-policy"></a>정책 구성

조건부 액세스 정책을 관리 하는 작업은 수동으로 실행 해야 합니다. Azure Portal의 단일 중앙 위치인 조건부 액세스 페이지에서 조건부 액세스 정책을 관리할 수 있습니다. 조건부 액세스 페이지의 한 진입점은 **Active Directory** 탐색 창의 **보안** 섹션입니다. 

![조건부 액세스](media/plan-conditional-access/03.png)

조건부 액세스 정책을 만드는 방법에 대 한 자세한 내용은 [조건부 액세스 Azure Active Directory 있는 특정 앱에 대 한 MFA 요구](app-based-mfa.md)를 참조 하세요. 이 빠른 시작은 다음을 수행하는 데 도움이 됩니다.

- 사용자 인터페이스를 파악합니다.
- 조건부 액세스의 작동 방식에 대 한 첫 번째 인상을 받습니다. 

### <a name="evaluate-a-simulated-sign-in"></a>시뮬레이션된 로그인 평가

조건부 액세스 정책을 구성했으니, 예상대로 작동하는지 확인해야 합니다. 첫 번째 단계로, 조건부 액세스 [what if 정책 도구](what-if-tool.md)를 사용하여 테스트 사용자 로그인을 시뮬레이션합니다. 이 시뮬레이션은 이 로그인이 정책에 미치는 영향을 평가하고, 시뮬레이션 보고서를 생성합니다.

>[!NOTE]
> 시뮬레이션 된 실행은 조건부 액세스 정책에 미치는 영향에 대 한 느낌을 제공 하지만 실제 테스트 실행을 대체 하지는 않습니다.

### <a name="test-your-policy"></a>정책 테스트

테스트 계획에 따라 테스트 사례를 실행합니다. 이 단계에서는 테스트 사용자에 대해 각 정책의 엔드투엔드 테스트를 실행하여 각 정책이 올바르게 작동하는지 확인합니다. 위에서 만든 시나리오를 사용하여 각 테스트를 실행합니다.

정책의 제외 기준을 테스트하는 것이 중요합니다. 예를 들어 MFA를 요구하는 정책에서 사용자 또는 그룹을 제외할 수 있습니다. 다른 정책의 조합에 해당 사용자에 대 한 MFA가 필요할 수 있으므로 제외 된 사용자에 게 MFA를 묻는 메시지가 표시 되는지 테스트 합니다.

### <a name="cleanup"></a>정리

정리 프로시저는 다음 단계로 구성됩니다.

1. 정책을 사용하지 않도록 설정합니다.
1. 할당된 사용자 및 그룹을 제거합니다.
1. 테스트 사용자를 삭제합니다.  

## <a name="move-to-production"></a>프로덕션 환경으로 이동

환경에 대한 새 정책이 준비되면 다음과 같은 단계로 배포합니다.

- 최종 사용자에게 내부 변경 통신을 제공합니다.
- 작은 사용자 세트로 시작하여 정책이 예상대로 작동하는지 확인합니다.
- 더 많은 사용자를 포함하도록 정책을 확장하는 경우 모든 관리자를 계속 제외합니다. 관리자를 제외하면 변경이 필요한 경우 누군가가 정책에 계속 액세스할 수 있습니다.
- 필요한 경우에만 모든 사용자에게 정책을 적용합니다.

다음과 같은 사용자 계정을 하나 이상 만드는 것이 좋습니다.

- 정책 관리 전용
- 모든 정책에서 제외

## <a name="rollback-steps"></a>롤백 단계

새로 구현된 정책을 롤백해야 하는 경우 다음 옵션 중 하나 이상을 사용하여 롤백합니다.

1. **정책 사용 안 함** - 정책을 사용하지 않도록 설정하면 사용자가 로그인할 때 정책이 적용되지 않습니다. 언제든지 이 정책을 사용하고 싶을 때 돌아와서 정책을 사용하도록 설정할 수 있습니다.

   ![정책 사용 안 함](media/plan-conditional-access/07.png)

1. **정책에서 사용자/그룹 제외** - 사용자가 앱에 액세스할 수 없는 경우 정책에서 사용자를 제외하도록 선택할 수 있습니다.

   ![사용자 제외](media/plan-conditional-access/08.png)

   > [!NOTE]
   > 이 옵션은 사용자를 신뢰할 수 있는 경우에만 신중하게 사용해야 합니다. 최대한 빨리 정책 또는 그룹에 사용자를 다시 추가해야 합니다.

1. **정책 삭제** - 정책이 더 이상 필요하지 않으면 삭제합니다.

## <a name="next-steps"></a>다음 단계

[Azure AD 조건부 액세스 설명서](index.yml)를 참조하여 사용 가능한 정보에 대한 개요를 확인합니다.
