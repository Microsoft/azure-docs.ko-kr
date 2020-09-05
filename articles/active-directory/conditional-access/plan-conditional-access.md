---
title: Azure Active Directory 조건부 액세스 배포 계획
description: 조건부 액세스 정책을 설계하고 조직에 효과적으로 배포하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 06/09/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 14612247d241aed420a76ff40e19d04164cbe150
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049930"
---
# <a name="plan-a-conditional-access-deployment"></a>조건부 액세스 배포 계획

조건부 액세스 배포를 계획하는 것은 앱 및 리소스에 대한 조직의 액세스 전략을 구현하는 데 중요합니다.

모바일 우선, 클라우드 우선 환경에서 사용자는 다양한 디바이스와 앱을 사용하여 어디서나 조직의 리소스에 액세스합니다. 따라서 리소스에 액세스할 수 있는 사용자에게만 초점을 맞추는 것은 더 이상 충분하지 않습니다. 또한 사용자의 위치, 사용하는 디바이스, 액세스하는 리소스 등을 고려해야 합니다. 

Azure AD(Azure Active Directory) CA(조건부 액세스)는 사용자, 디바이스 및 위치와 같은 신호를 분석하여 결정을 자동화하고 리소스에 대한 조직의 액세스 정책을 적용합니다. CA 정책을 사용하여 MFA(Multi-Factor Authentication)와 같은 액세스 제어를 적용할 수 있습니다. CA 정책을 사용하면 보안이 필요한 경우 사용자에게 MFA를 요청하는 메시지를 표시하고, 필요하지 않은 경우 사용자를 방해하지 않을 수 있습니다.

![조건부 액세스 개요](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft는 기본 보안 수준을 보장하는 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md)이라는 표준 조건부 정책을 제공합니다. 그러나 조직에는 보안 기본값에서 제공하는 것보다 더 많은 유연성이 필요할 수 있습니다. 조건부 액세스를 사용하여 더 많은 세분성으로 보안 기본값을 사용자 지정하고 요구 사항에 맞는 새 정책을 구성할 수 있습니다.

## <a name="learn"></a>배우기

시작하기 전에 [조건부 액세스](overview.md)의 작동 방식과 사용 시기를 이해해야 합니다.

### <a name="benefits"></a>이점

조건부 액세스를 배포하는 경우 다음과 같은 이점이 있습니다.

* 생산성을 향상시킵니다. 하나 이상의 신호에서 보증하는 경우 MFA와 같은 로그인 조건을 사용하는 사용자만 중단합니다. CA 정책을 사용하면 사용자에게 MFA를 요구하는 메시지가 표시되는 경우, 액세스가 차단되는 경우 및 신뢰할 수 있는 디바이스를 사용해야 하는 경우를 제어할 수 있습니다.

* 위험을 관리합니다. 정책 조건을 사용하여 위험 평가를 자동화하면 위험한 로그인을 한 번에 식별하여 수정하거나 차단할 수 있습니다. 비정상 이벤트 및 의심스러운 이벤트를 검색하는 [ID 보호](../identity-protection/overview-identity-protection.md)와 조건부 액세스를 결합하면 리소스에 대한 액세스가 차단되거나 제어될 때 대상을 지정할 수 있습니다. 

* 규정 준수 및 거버넌스를 처리합니다. 조건부 액세스를 사용하면 애플리케이션에 대한 액세스를 감사하고, 동의를 위한 사용 약관을 제시하며, 규정 준수 정책에 따라 액세스를 제한할 수 있습니다.

* 비용을 관리합니다. 액세스 정책을 Azure AD로 이동하면 조건부 액세스에 대한 사용자 지정 또는 온-프레미스 솔루션의 의존도와 해당 인프라 비용이 줄어듭니다.

### <a name="license-requirements"></a>라이선스 요구 사항

[조건부 액세스 라이선스 요구 사항](overview.md)을 참조하세요.

추가 기능이 필요한 경우 관련 라이선스도 필요할 수 있습니다. 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

### <a name="prerequisites"></a>사전 요구 사항

* Azure AD Premium 또는 평가판 라이선스를 사용하도록 설정된 작업 Azure AD 테넌트 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 조건부 액세스 관리자 권한이 있는 계정

* 알고 있는 암호가 있는 관리자가 아닌 사용자(예: testuser). 사용자를 만들어야 하는 경우 [빠른 시작: Azure Active Directory에 새 사용자 추가](../fundamentals/add-users-azure-active-directory.md)의 정보를 사용할 수 있습니다.

* 관리자가 아닌 사용자가 멤버인 그룹. 그룹을 만들어야 하는 경우 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

### <a name="training-resources"></a>학습 리소스

조건부 액세스에 대해 알아볼 때 유용할 수 있는 리소스는 다음과 같습니다.


#### <a name="videos"></a>동영상

* [조건부 액세스란?](https://youtu.be/ffMAw2IVO7A)
* [조건부 액세스를 배포하는 방법](https://youtu.be/c_izIRNJNuk)
* [최종 사용자에게 CA 정책을 롤아웃하는 방법](https://youtu.be/0_Fze7Zpyvc)
* [디바이스 컨트롤을 사용하는 조건부 액세스](https://youtu.be/NcONUf-jeS4)
* [Azure MFA를 사용하는 조건부 액세스](https://youtu.be/Tbc-SU97G-w)
* [Enterprise Mobility + Security의 조건부 액세스](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>PluralSight의 온라인 과정

* [Microsoft Azure의 ID 관리 디자인](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Microsoft Azure 인증 디자인](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Microsoft Azure 권한 부여 디자인](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여](https://aka.ms/deploymentplans)시키고 프로젝트 역할을 명확하게 해야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

환경에 대한 새 정책이 준비되면 프로덕션 환경에서 단계별로 배포합니다. 먼저 테스트 환경에서 소수의 사용자에게 정책을 적용하고 정책이 예상대로 작동하는지 확인합니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

> [!NOTE]
> 관리자와 관련되지 않은 새 정책을 롤아웃하려면 모든 관리자를 제외합니다. 이렇게 하면 관리자가 정책에 계속 액세스하여 변경하거나, 심각한 영향이 있으면 철회할 수 있습니다. 모든 사용자에게 적용하기 전에 항상 작은 사용자 그룹을 사용하여 정책의 유효성을 검사합니다.

## <a name="understand-ca-policy-components"></a>CA 정책 구성 요소 이해
CA 정책은 if then 문: 할당이 충족 되는 경우이 액세스 제어를 적용 합니다.

CA 정책을 구성하는 경우 조건은 *할당*이라고 합니다. CA 정책을 사용하면 특정 할당에 따라 조직의 앱에 대한 액세스 제어를 적용할 수 있습니다.


자세한 내용은 [CA 정책 빌드](concept-conditional-access-policies.md)를 참조하세요.

![정책 만들기 화면](media/plan-conditional-access/create-policy.png)

[할당](concept-conditional-access-policies.md#assignments)은 다음을 정의합니다.

* 정책의 영향을 받을 [사용자 및 그룹](concept-conditional-access-users-groups.md)

* 정책이 적용 될 [클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md) 

* 정책이 적용 되는 [조건](concept-conditional-access-conditions.md) 입니다.

[액세스 제어](concept-conditional-access-policies.md) 설정에 따라 정책을 적용하는 방법이 결정됩니다.

* 클라우드 앱에 대한 액세스 권한을 [부여하거나 차단](concept-conditional-access-grant.md)합니다.

* [세션 컨트롤](concept-conditional-access-session.md)은 특정 클라우드 앱 내에서 제한된 환경을 사용하도록 설정합니다.

### <a name="ask-the-right-questions-to-build-your-policies"></a>정책을 빌드하기 위한 올바른 질문

정책은 리소스에 액세스해야 하는 사용자, 액세스해야 하는 리소스 및 조건에 대한 질문에 대답합니다. 정책은 액세스 권한을 부여하거나 차단하도록 설계할 수 있습니다. 정책에서 달성하려고 하는 목표에 대해 올바르게 질문해야 합니다. 

각 정책을 빌드하기 전에 해당 질문에 대한 대답을 문서화합니다. 

#### <a name="common-questions-about-assignments"></a>할당에 대한 일반적인 질문

[사용자 및 그룹](concept-conditional-access-users-groups.md)

* 정책에서 포함되거나 제외되는 사용자 및 그룹은 무엇입니까?

* 이 정책에는 모든 사용자, 특정 사용자 그룹, 디렉터리 역할 또는 외부 사용자가 포함됩니까?

[클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)

* 정책이 적용되는 애플리케이션은 무엇입니까?

* 이 정책이 적용되는 사용자 작업은 무엇입니까?

[조건](concept-conditional-access-conditions.md)

* 정책에서 포함되거나 제외되는 디바이스 플랫폼은 무엇입니까?

* 조직의 신뢰할 수 있는 위치는 무엇입니까?

* 정책에서 포함되거나 제외되는 위치는 무엇입니까?

* 정책에서 포함되거나 제외되는 클라이언트 앱 유형(브라우저, 모바일, 데스크톱 클라이언트, 레거시 인증 방법을 사용하는 앱)은 무엇입니까?

* 정책에서 Azure AD 조인 디바이스 또는 하이브리드 Azure AD 조인 디바이스를 제외하도록 추진하는 정책이 있습니까? 

* [ID 보호](../identity-protection/concept-identity-protection-risks.md)를 사용하는 경우 로그인 위험 보호를 통합하시겠습니까?

#### <a name="common-questions-about-access-controls"></a>액세스 제어에 대한 일반적인 질문

[권한 부여 또는 차단](concept-conditional-access-grant.md) 

다음 중 하나 이상을 요구하여 리소스에 대한 액세스 권한을 부여하시겠습니까?

* MFA 요구

* 디바이스를 준수 상태로 표시해야 함

* 하이브리드 Azure AD 조인 디바이스 필요

* 승인된 클라이언트 앱 필요

* 앱 보호 정책 필요

[세션 컨트롤](concept-conditional-access-session.md)

클라우드 앱에서 다음 액세스 제어를 적용하시겠습니까?

* 앱 적용 권한 사용

* 조건부 액세스 앱 제어 사용

* 로그인 빈도 적용

* 영구 브라우저 세션 사용

### <a name="access-token-issuance"></a>액세스 토큰 발급

액세스 토큰이 발급되는 방법을 이해해야 합니다. 

![액세스 토큰 발급 다이어그램](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> 할당이 필요 하지 않고 CA 정책이 적용 되지 않는 경우 기본 동작은 액세스 토큰을 발급 하는 것입니다. 

예를 들어 다음과 같은 정책을 살펴보겠습니다.

[IF]사용자가 그룹 1에 있으면 [THEN]MFA에서 앱 1에 액세스하도록 강제합니다.

그룹 1에 속하지 않은 사용자가 앱에 액세스하려고 하면 "if" 조건이 충족되지 않고 토큰이 발급됩니다. 그룹 1 이외의 사용자를 제외하려면 다른 모든 사용자를 차단하는 별도의 정책이 필요합니다.

## <a name="follow-best-practices"></a>모범 사례 준수

조건부 액세스 프레임워크는 뛰어난 구성 유연성을 제공합니다. 그러나 뛰어난 유연성은 바람직하지 않은 결과를 방지하기 위해 각 구성 정책을 릴리스하기 전에 신중하게 검토해야 한다는 것도 의미합니다.

### <a name="apply-ca-policies-to-every-app"></a>모든 앱에 CA 정책 적용

CA 정책 조건에서 액세스 제어를 트리거하지 않으면 액세스 토큰이 기본적으로 발급됩니다. 모든 앱에 하나 이상의 조건부 액세스 정책이 적용되어 있는지 확인합니다.

> [!IMPORTANT]
> 단일 정책에서 차단 및 모든 앱을 사용하는 경우 매우 주의해야 합니다. 이로 인해 관리자가 Azure 관리 포털에서 잠길 수 있으며 Microsoft Graph와 같은 중요한 엔드포인트에 대한 제외를 구성할 수 없습니다.

### <a name="minimize-the-number-of-ca-policies"></a>CA 정책 수 최소화

각 앱에 대한 정책을 만드는 것은 비효율적이며 관리가 어려워집니다. 조건부 액세스는 사용자당 초기 195개 정책만 적용합니다. 앱을 분석하여 동일한 사용자에 대한 동일한 리소스 요구 사항이 있는 애플리케이션으로 그룹화하는 것이 좋습니다. 예를 들어 모든 Microsoft 365 apps 또는 모든 HR 앱에 동일한 사용자에 대 한 동일한 요구 사항이 있으면 단일 정책을 만들고이 정책을 적용 하는 모든 앱을 포함 합니다. 

### <a name="set-up-emergency-access-accounts"></a>응급 액세스 계정 설정

정책을 잘못 구성하면 조직이 Azure Portal에서 잠길 수 있습니다. 조직에서 둘 이상의 [응급 액세스 계정](../users-groups-roles/directory-emergency-access.md)을 만들어 실수로 인한 관리자 잠금의 영향을 완화합니다.

* 정책 관리 전용 사용자 계정을 만들고 모든 정책에서 제외합니다.

* 하이브리드 환경에 대한 비상 시나리오는 다음과 같습니다.

  * 온-프레미스 보안 그룹을 만들어 Azure AD와 동기화합니다. 전용 정책 관리 계정이 보안 그룹에 포함됩니다. 

   * 모든 CA 정책에서 이 보안 그룹을 제외합니다.

   * 서비스 중단이 발생하면 다른 관리자를 온-프레미스 그룹에 적절히 추가하고 강제로 동기화합니다. 그러면 애니메이션 효과가 CA 정책의 제외에 적용됩니다.

### <a name="set-up-report-only-mode"></a>보고서 전용 모드 설정

다음과 같은 일반적인 배포 이니셔티브의 영향을 받는 사용자의 수와 이름은 예측하기가 어려울 수 있습니다.

* 레거시 인증 차단
* MFA 요구
* 로그인 위험 정책 구현

[보고서 전용 모드](concept-conditional-access-report-only.md)를 사용하면 관리자가 환경에서 CA 정책을 사용하도록 설정하기 전에 해당 정책의 영향을 평가할 수 있습니다.

[CA 정책에서 보고서 전용 모드를 구성](howto-conditional-access-insights-reporting.md)하는 방법에 대해 알아보세요.

### <a name="plan-for-disruption"></a>중단 계획

MFA 또는 네트워크 위치와 같은 단일 액세스 제어를 사용하여 IT 시스템을 보호하는 경우 해당 단일 액세스 제어를 사용할 수 없거나 잘못 구성하면 액세스 실패에 취약할 수 있습니다. 예기치 않은 중단 시 잠금 위험을 줄이려면 조직에 적용할 [전략을 계획](../authentication/concept-resilient-controls.md)합니다.

### <a name="set-naming-standards-for-your-policies"></a>정책에 대한 이름 지정 표준 설정

이름 지정 표준은 정책을 찾고 Azure 관리 포털에서 열지 않고도 해당 용도를 파악하는 데 도움이 됩니다. 표시할 정책의 이름을 지정하는 것이 좋습니다.

* 시퀀스 번호

* 적용되는 클라우드 앱

* 응답

* 정책이 적용되는 대상

* 정책이 적용되는 시기(해당하는 경우)

![이름 지정 표준](media/plan-conditional-access/11.png)

**예**: 외부 네트워크에서 Dynamics CRP 앱에 액세스하는 마케팅 사용자에게 MFA를 요구하는 정책은 다음과 같을 수 있습니다.

![이름 지정 표준](media/plan-conditional-access/naming-example.png)

설명이 포함된 이름을 사용하면 조건부 액세스 구현에 대한 개요를 유지할 수 있습니다. 시퀀스 번호는 대화에서 정책을 참조해야 하는 경우에 유용합니다. 예를 들어 관리자에게 전화로 연락하는 경우 문제를 해결하기 위해 CA01 정책을 열도록 요청할 수 있습니다.

#### <a name="naming-standards-for-emergency-access-controls"></a>응급 액세스 제어의 이름 지정 표준

활성 정책 외에도 [중단 또는 응급 시나리오에서 복원력 있는 보조 액세스 제어](../authentication/concept-resilient-controls.md)로 작동하지만 사용하지 않도록 설정되는 정책을 구현합니다. 대응 정책에 대한 이름 지정 표준에 포함되어야 하는 항목은 다음과 같습니다.
* 다른 정책 중에서 이름을 구분할 수 있도록 시작 부분에 ENABLE IN EMERGENCY(응급 시 사용) 사용

* 적용해야 하는 중단의 이름

* 관리자가 사용하도록 설정해야 하는 순서 정책을 파악하는 데 도움이 되는 순서 지정 시퀀스 번호

**예제**

MFA 중단이 발생하는 경우 사용하도록 설정하는 네 가지 정책 중 첫 번째 정책임을 나타내는 이름은 다음과 같습니다.

EM01 - ENABLE IN EMERGENCY: MFA 중단[1/4] - Exchange SharePoint: VIP 사용자에 대한 하이브리드 Azure AD 조인이 필요합니다.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>로그인이 필요하지 않은 국가는 제외합니다.

Azure Active Directory를 사용하면 [명명된 위치](location-condition.md)를 만들 수 있습니다. 로그인을 수행할 필요가 없는 모든 국가를 포함하는 명명된 위치를 만듭니다. 그런 다음, 해당하는 명명된 위치에서 로그인을 차단하는 [모든 앱] 정책을 만듭니다. **관리자는 이 정책에서 제외해야 합니다**.

### <a name="plan-your-policy-deployment"></a>정책 배포 계획

환경에 대한 새 정책이 준비되면 바람직하지 않은 결과를 방지하기 위해 각 정책을 릴리스하기 전에 검토해야 합니다. 정책을 적용하는 방법 및 문제를 방지하는 방법에 대한 중요한 정보를 이해하려면 다음 설명서를 참조하세요.

* [알아야 할 사항](best-practices.md)

* [수행하지 않아야 하는 작업](best-practices.md)

## <a name="common-policies"></a>일반 정책

CA 정책 솔루션을 계획하는 경우 다음 결과를 얻기 위해 정책을 만들어야 하는지 여부를 평가합니다.

* [MFA 요구](#require-mfa)
* [잠재적으로 손상된 계정에 대한 대응](#respond-to-potentially-compromised-accounts)
* [관리 디바이스 필요](#require-managed-devices)
* [승인 된 클라이언트 응용 프로그램 필요](#require-approved-client-apps)
* [액세스 차단](#block-access)

### <a name="require-mfa"></a>MFA 요구

MFA 액세스를 요구하는 일반적인 사용 사례:

* [관리자에 의한 액세스](howto-conditional-access-policy-admin-mfa.md)

* [특정 앱에 대한 액세스](../authentication/tutorial-enable-azure-mfa.md)

* [모든 사용자를 위한 액세스](howto-conditional-access-policy-all-users-mfa.md)

* [신뢰하지 않는 네트워크 위치에서 액세스](untrusted-networks.md)

* [Azure 관리를 위한 액세스](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>잠재적으로 손상된 계정에 대한 대응

CA 정책을 사용하면 잠재적으로 손상된 ID의 로그인에 대한 자동화된 응답을 구현할 수 있습니다. 계정이 손상되는 확률은 위험 수준 형태로 표현됩니다. ID 보호에서 계산되는 두 가지 위험 수준으로 로그인 위험 및 사용자 위험이 있습니다. 사용하도록 설정될 수 있는 세 가지 기본 정책은 다음과 같습니다.

* [모든 사용자에게 MFA 등록 요구](howto-conditional-access-policy-risk.md)

* [위험 수준이 높은 사용자에게 암호 변경 요구](howto-conditional-access-policy-risk.md)

* [로그인 위험 수준이 중간이거나 높은 사용자에게 MFA 요구](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>관리 디바이스 필요

클라우드 리소스에 액세스하도록 지원되는 디바이스의 범위가 넓어지면 사용자의 생산성을 개선할 수 있습니다. 보호 수준이 알려지지 않은 디바이스에서 사용자 환경의 특정 리소스에 액세스하는 것을 원하지 않을 수 있습니다. 이러한 리소스의 경우 [사용자가 관리 디바이스를 통해서만 해당 리소스에 액세스할 수 있어야 합니다](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>승인된 클라이언트 앱 필요

직원은 자신의 모바일 디바이스를 개인 및 회사 작업 모두에 사용합니다. BYOD 시나리오의 경우 디바이스 전체를 관리할지, 아니면 디바이스의 데이터만 관리할지를 결정해야 합니다. 데이터와 액세스만 관리하는 경우 회사 데이터를 보호할 수 있는 [승인된 클라우드 앱이 필요](app-based-conditional-access.md)할 수 있습니다. 예를 들어 일반 메일 프로그램이 아닌 Outlook 모바일을 통해서만 이메일에 액세스하도록 요구할 수 있습니다.

### <a name="block-access"></a>액세스 차단

[모든 액세스를 차단](howto-conditional-access-policy-block-access.md)하는 옵션은 강력합니다. 예를 들어 앱을 Azure AD로 마이그레이션하고 있지만 아직 사용자가 로그인할 준비가 되지 않은 경우에 사용할 수 있습니다. 액세스 차단은 다음과 같습니다. 

* 사용자에 대한 다른 모든 할당을 재정의함

* 전체 조직이 테넌트에 로그온할 수 없도록 차단함

> [!IMPORTANT]
> 모든 사용자에 대한 액세스를 차단하는 정책을 만드는 경우 응급 액세스 계정을 제외해야 하고, 모든 관리자를 정책에서 제외하는 것이 좋습니다.

사용자의 액세스를 차단할 수 있는 다른 일반적인 시나리오는 다음과 같습니다.

* 클라우드 앱에 액세스하는 [특정 네트워크 위치를 차단](howto-conditional-access-policy-location.md)합니다. 이 정책을 사용하여 트래픽이 발생하지 않아야 하는 특정 국가를 차단할 수 있습니다.

* Azure AD에서 레거시 인증을 지원합니다. 그러나 레거시 인증은 MFA를 지원하지 않으며, 많은 환경에서 ID 보안을 처리하는 데 필요합니다. 이 경우 [레거시 인증을 사용하는 앱에서 테넌트 리소스에 액세스하지 못하도록 차단](block-legacy-authentication.md)할 수 있습니다.

## <a name="build-and-test-policies"></a>정책 빌드 및 테스트

배포의 각 단계에서 예상된 결과인지를 평가해야 합니다. 

새 정책이 준비되면 프로덕션 환경에서 단계별로 배포합니다.

* 최종 사용자에게 내부 변경 통신을 제공합니다.

* 작은 사용자 세트로 시작하여 정책이 예상대로 작동하는지 확인합니다.

* 더 많은 사용자를 포함하도록 정책을 확장하는 경우 모든 관리자를 계속 제외합니다. 관리자를 제외하면 변경이 필요한 경우 누군가가 정책에 계속 액세스할 수 있습니다.

* 정책을 철저히 테스트한 후에만 모든 사용자에게 적용합니다. 정책이 적용되지 않는 하나 이상의 관리자 계정이 있어야 합니다.

### <a name="create-test-users"></a>테스트 사용자 만들기

프로덕션 환경의 사용자를 반영하는 테스트 사용자 세트를 만듭니다. 테스트 사용자를 만들면 실제 사용자에게 영향을 주고 앱 및 리소스에 대한 액세스를 잠재적으로 중단하기 전에 정책이 예상대로 작동하는지 확인할 수 있습니다.

일부 조직에는 이러한 용도의 테스트 테넌트가 있습니다. 그러나 정책의 결과를 완전히 테스트하기 위해 테스트 테넌트에서 모든 조건과 앱을 다시 만들기 어려울 수 있습니다.

### <a name="create-a-test-plan"></a>테스트 계획 만들기

테스트 계획은 예상 결과와 실제 결과 간에 비교하는 것이 중요합니다. 그러므로 항상 테스트 전에 결과를 예상해야 합니다. 다음 표에는 예제 테스트 사례의 개요가 나와 있습니다. CA 정책이 구성된 방식에 따라 시나리오와 예상 결과를 조정합니다.

| 정책| 시나리오| 예상 결과 |
| - | - | - |
| [직장이 아닐 때 MFA 요구](untrusted-networks.md)| 권한 있는 사용자가 신뢰할 수 있는 위치/회사에 있는 상태에서 앱에 로그인함| 사용자에게 MFA를 요구하는 메시지가 표시되지 않음 |
| [직장이 아닐 때 MFA 요구](untrusted-networks.md)| 권한 있는 사용자가 신뢰할 수 있는 위치/회사에 있지 않은 상태에서 앱에 로그인함| 사용자에게 MFA에 대한 메시지가 표시되고 성공적으로 로그인할 수 있음 |
| [MFA 요구(관리자의 경우)](../fundamentals/concept-fundamentals-security-defaults.md)| 전역 관리자가 앱에 로그인함| 관리자에게 MFA에 대한 메시지가 표시됨 |
| [위험한 로그인](../identity-protection/howto-identity-protection-configure-risk-policies.md)| 사용자가 승인 되지 않은 브라우저를 사용 하 여 앱에 로그인| 관리자에게 MFA에 대한 메시지가 표시됨 |
| [디바이스 관리](require-managed-devices.md)| 권한 있는 사용자가 권한 있는 디바이스에서 로그인하려고 시도함| 액세스 권한 부여됨 |
| [디바이스 관리](require-managed-devices.md)| 권한 있는 사용자가 권한 없는 디바이스에서 로그인하려고 시도함| 액세스 차단됨 |
| [위험한 사용자에 대한 암호 변경](../identity-protection/howto-identity-protection-configure-risk-policies.md)| 권한 있는 사용자가 손상된 자격 증명을 사용하여 로그인하려고 시도함(높은 위험 로그인)| 사용자에게 암호를 변경하라는 메시지가 표시되거나 정책에 따라 액세스가 차단됨 |


### <a name="configure-the-test-policy"></a>테스트 정책 구성

CA 정책은 [Azure Portal](https://portal.azure.com/)의 Azure Active Directory > 보안 > 조건부 액세스 아래에서 구성합니다.

CA 정책을 만드는 방법에 대해 자세히 알아보려면 다음 예제를 참조하세요. [사용자가 Azure Portal에 로그인할 때 MFA를 요구하는 CA 정책](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) 이 빠른 시작은 다음을 수행하는 데 도움이 됩니다.

* 사용자 인터페이스를 숙지합니다.

* 조건부 액세스의 작동 방식을 체험합니다.

### <a name="enable-the-policy-in-report-only-mode"></a>보고서 전용 모드의 정책 사용

정책의 영향을 평가하려면 먼저 [보고서 전용 모드](concept-conditional-access-report-only.md)에서 정책을 사용하도록 설정합니다. 보고서 전용 정책은 로그인하는 동안 평가되지만 권한 부여 컨트롤 및 세션 컨트롤은 적용되지 않습니다. 정책이 보고서 전용 모드로 저장되면 로그인 로그에서 실시간 로그인에 미치는 영향을 확인할 수 있습니다. 로그인 로그에서 이벤트를 선택하고, 보고서 전용 탭으로 이동하여 각 보고서 전용 정책의 결과를 확인합니다.


![보고서 전용 모드 ](media/plan-conditional-access/report-only-mode.png)

정책을 선택하면 정책 세부 정보 화면을 사용하여 정책의 할당 및 액세스 제어를 평가한 방법을 확인할 수도 있습니다. 정책을 로그인에 적용하려면 구성된 각 할당이 충족되어야 합니다. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>인사이트 및 보고 통합 문서를 사용하여 정책의 영향 이해

인사이트 및 보고 통합 문서에서 조건부 액세스 정책의 집계 영향을 확인할 수 있습니다. 통합 문서에 액세스하려면 Azure Monitor 구독이 필요하고 [로그인 로그를 Log Analytics 작업 영역으로 스트림](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)해야 합니다. 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>what-if 도구를 사용하여 로그인 시뮬레이션

조건부 액세스 정책의 유효성을 검사하는 또 다른 방법은 [what-if 도구](troubleshoot-conditional-access-what-if.md)를 사용하여 가상 환경에서 로그인하는 사용자에게 적용할 정책을 시뮬레이션하는 것입니다. 테스트하려는 로그인 특성(예: 사용자, 애플리케이션, 디바이스 플랫폼 및 위치)을 선택하고 적용할 정책을 확인합니다.

> [!NOTE] 
> 시뮬레이션된 실행은 CA 정책의 영향을 이해하는 데 유용하지만 실제 테스트 실행을 대체하지는 않습니다.

### <a name="test-your-policy"></a>정책 테스트

테스트 계획의 각 테스트는 테스트 사용자를 통해 수행합니다.

**정책의 제외 조건을 테스트해야 합니다**. 예를 들어 MFA를 요구하는 정책에서 사용자 또는 그룹을 제외할 수 있습니다. 다른 정책을 조합하면 제외된 사용자에게 MFA를 요구할 수도 있으므로 해당 사용자에게 MFA를 요구하는 메시지가 표시되는지 테스트합니다.

### <a name="roll-back-policies"></a>정책 롤백

새로 구현된 정책을 롤백해야 하는 경우 다음 옵션 중 하나 이상을 사용합니다.

* **정책을 사용하지 않도록 설정합니다.** 정책을 사용하지 않도록 설정하면 사용자가 로그인을 시도할 때 해당 정책이 적용되지 않습니다. 이 정책을 사용하려면 언제든지 다시 돌아와서 해당 정책을 사용하도록 설정할 수 있습니다.

![정책 사용 이미지](media/plan-conditional-access/enable-policy.png)

* **정책에서 사용자 또는 그룹을 제외합니다.** 사용자가 앱에 액세스할 수 없는 경우 정책에서 사용자를 제외하도록 선택할 수 있습니다.

![사용자 및 그룹 제외](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  이 옵션은 사용자를 신뢰할 수 있는 경우에만 신중하게 사용해야 합니다. 최대한 빨리 정책 또는 그룹에 사용자를 다시 추가해야 합니다.

* **정책을 삭제합니다.** 정책이 더 이상 필요하지 않은 경우 [삭제](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)합니다.

## <a name="manage-access-to-cloud-apps"></a>클라우드 앱에 대한 액세스 관리

다음 관리 옵션을 사용하여 CA 정책을 제어하고 관리합니다.

![manage-access](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>명명된 위치

CA 정책의 위치 조건을 사용하면 액세스 제어 설정을 사용자의 네트워크 위치에 연결할 수 있습니다. [명명된 위치](location-condition.md)를 사용하면 IP 주소 범위 또는 국가/지역의 논리적 그룹을 만들 수 있습니다.

### <a name="custom-controls"></a>사용자 지정 컨트롤

[사용자 지정 컨트롤](controls.md)은 Azure AD 외부의 인증 요구 사항을 충족시키기 위해 사용자를 호환 가능한 서비스로 리디렉션합니다. 이 컨트롤을 충족시키기 위해 사용자의 브라우저가 외부 서비스로 리디렉션되고, 필요한 인증을 수행한 다음, Azure AD로 다시 리디렉션됩니다. Azure AD에서 응답을 확인하고, 사용자가 성공적으로 인증되거나 유효성이 확인되면 해당 사용자가 조건부 액세스 흐름을 계속 진행합니다.

### <a name="terms-of-use"></a>사용 약관

사용자 환경에서 특정 클라우드 앱에 액세스하기 전에 ToU(사용 약관)를 수락하도록 하여 사용자의 동의를 받을 수 있습니다. 이 [빠른 시작](require-tou.md)에 따라 사용 약관을 만듭니다.

### <a name="classic-policies"></a>클래식 정책

CA 정책은 [Azure Portal](https://portal.azure.com/)의 Azure Active Directory > 보안 > 조건부 액세스 아래에서 찾을 수 있습니다. 또한 이 페이지를 사용하여 만들지 않은 이전 CA 정책도 조직에 있을 수 있습니다. 이러한 정책을 클래식 정책이라고 합니다. [Azure Portal에서 이러한 클래식 정책을 마이그레이션](best-practices.md)하는 것이 좋습니다.

## <a name="troubleshoot-conditional-access"></a>조건부 액세스 문제 해결

사용자에게 CA 정책과 관련된 문제가 있는 경우 문제를 쉽게 해결할 수 있도록 다음 정보를 수집합니다.

* 사용자 계정 이름

* 사용자 표시 이름

* 운영 체제 이름

* 타임스탬프(근삿값도 허용됨)

* 대상 애플리케이션

* 클라이언트 애플리케이션 유형(브라우저 및 클라이언트)

* 상관 관계 ID(로그인 고유의 ID임)

사용자가 [자세한 정보] 링크를 포함한 메시지를 받은 경우 이 정보의 대부분을 수집할 수 있습니다.

!['앱에 액세스할 수 없습니다.' 오류 메시지](media/plan-conditional-access/cant-get-to-app.png)

정보가 수집되었으면 다음 리소스를 참조하세요.

* [조건부 액세스 관련 로그인 문제](troubleshoot-conditional-access.md) – 오류 메시지 및 Azure AD 로그인 로그를 사용하여 조건부 액세스와 관련된 예기치 않은 로그인 결과를 파악합니다.

* [What-If 도구 사용](troubleshoot-conditional-access-what-if.md) - 특정 상황에서 정책이 사용자에게 적용되거나 적용되지 않은 이유 또는 정책이 알려진 상태에서 적용되는지 여부를 파악합니다.

## <a name="next-steps"></a>다음 단계

[Multi-Factor Authentication에 대해 자세히 알아보기](../authentication/concept-mfa-howitworks.md)

[ID 보호에 대해 자세히 알아보기](../identity-protection/overview-identity-protection.md)

[Microsoft Graph API를 사용하여 CA 정책 관리](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta.md)