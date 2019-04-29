---
title: Azure Active Directory에서 ID 인프라를 보호하기 위한 5단계
description: 이 문서에서는 관리자가 Azure AD 기능을 사용하여 해당 조직을 보호하기 위해 수행해야 하는 중요한 작업 목록을 간략히 안내합니다.
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: 92546e6aabdf43c2f9cb0339fb21dd2dfc641d44
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587876"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>ID 인프라를 보호하기 위한 5단계

이 문서를 읽는다면 보안의 중요성을 인식하고 있다는 뜻입니다. 이미 조직의 보안을 책임지고 있을 가능성도 있습니다. 보안의 중요성을 다른 사람들에게 납득시켜야 한다면 최신 [Microsoft 보안 인텔리전스 보고서](https://go.microsoft.com/fwlink/p/?linkid=2073747)를 읽도록 보내주세요.

이 문서는 사이버 공격에 대비하기 위한 5단계 검사 목록을 사용하여 Azure Active Directory의 기능을 통해 더 안전한 상태를 형성하는 데 도움이 됩니다.

이 검사 목록은 다음 사항의 수행 방법 설명을 통해 중요한 권장 작업을 빠르게 배포하여 조직을 즉시 보호하는 데 도움이 됩니다.

* 자격 증명을 강화합니다.
* 공격 노출 영역을 줄입니다.
* 위협 응답을 자동화합니다.
* 감사 및 모니터링의 인식률을 높입니다.
* 자가 진단으로 더 예측 가능하고 완벽한 최종 사용자 보안을 사용하도록 설정합니다.

> [!NOTE]
> 이 문서의 다양한 권장 사항은 ID 공급자로 Azure Active Directory를 사용하도록 구성되어 있는 애플리케이션에만 적용됩니다. Single Sign-On에 대해 앱을 구성하면 해당 앱에 자격 증명 정책, 위협 요소 탐지, 감사, 로깅 및 기타 기능의 이점이 추가됩니다. [Azure Active Directory를 통한 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso)은 이러한 모든 권장 사항의 기반이 되는 토대입니다.

이 문서의 권장 사항은 Azure AD 테넌트의 ID 보안 구성 평가를 자동화한 [ID 보안 점수](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)와 일치합니다. 조직은 Azure AD 포털의 [ID 보안 점수] 페이지를 통해 현재 보안 구성의 빈틈을 찾아 보안과 관련된 Microsoft의 현재 모범 사례를 따를 수 있습니다. [보안 점수] 페이지의 각 권장 사항을 구현하면 점수가 올라가고 진행 상황을 추적할 수 있으며, 비슷한 규모의 조직 또는 산업과 구현을 비교할 수 있습니다.

![ID 보안 점수](media/azure-ad/azure-ad-sec-steps0.png)

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>시작하기 전에 MFA를 통해 권한 있는 계정 보호

이 검사 목록을 시작하기 전에 이 검사 목록을 읽는 도중에 손상되지 않도록 해야 합니다. 먼저 권한 있는 계정을 보호해야 합니다.

공격자가 권한 있는 계정의 제어권을 확보하면 엄청난 피해가 발생할 수 있으므로 이러한 계정을 보호하는 것이 무엇보다도 중요합니다. [기준 보호](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection)를 사용하여 조직의 모든 관리자가 [MFA(Azure Multi-Factor Authentication)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)를 사용하도록 설정하고 제공합니다. MFA를 구현하지 않았다면, 지금 구현하세요! 이 문제는 중요합니다.

모두 준비되었나요? 이제 검사 목록을 시작하겠습니다.

## <a name="step-1---strengthen-your-credentials"></a>1단계 - 자격 증명을 강화합니다. 

대부분의 엔터프라이즈 보안 위반은 암호 스프레이, 위반 재생 또는 피싱 같은 방법 중 하나로 손상된 계정으로 시작됩니다. 이러한 공격에 대해 자세히 알아보려면 이 비디오(45분)를 시청하세요.
> [!VIDEO https://www.youtube.com/embed/uy0j1_t5Hd4]

### <a name="make-sure-your-organization-use-strong-authentication"></a>조직에서 강력한 인증을 사용하도록 해야 함

암호가 추측되고, 피싱되고, 맬웨어로 도난 또는 재사용되는 횟수를 고려할 때 강력한 자격 증명의 형식을 갖춘 암호로 돌아가는 것은 중요합니다. [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)에 대해 자세히 알아보세요.

### <a name="start-banning-commonly-attacked-passwords-and-turn-off-traditional-complexity-and-expiration-rules"></a>자주 공격받는 암호 사용을 금지하고 기존의 복잡성 및 만료 규칙을 해제합니다.

많은 조직에서는 (특수 문자, 숫자, 대문자 및 소문자가 필요한) 기존의 복잡성 및 암호 만료 규칙을 사용합니다. [Microsoft 연구](https://aka.ms/passwordguidance) 결과를 보면, 이러한 정책은 사용자가 쉽게 추측할 수 있는 암호를 선택하게 만드는 것으로 나타났습니다.

Azure AD의 [동적으로 금지된 암호](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords) 기능은 현재 공격자 동작을 사용하여 사용자가 쉽게 추측할 수 있는 암호를 설정하지 못하도록 합니다. 이 기능은 클라우드에서 사용자를 만들 때 항상 사용되며, 이제는 하이브리드 조직에서 [Windows Server Active Directory용 Azure AD 암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)를 배포할 때에도 사용할 수 있습니다. Azure AD 암호 보호는 사용자가 이처럼 일반적인 암호를 선택하지 못하게 차단하며 관리자가 지정하는 사용자 지정 키워드가 포함된 암호를 차단하도록 확장할 수 있습니다. 예를 들어 사용자가 회사의 제품 이름 또는 로컬 스포츠 팀이 포함된 암호를 선택하지 못하게 막을 수 있습니다.

Microsoft에서는 [NIST 지침](https://pages.nist.gov/800-63-3/sp800-63b.html)에 따라 다음과 같은 최신 암호 정책을 도입할 것을 권장합니다.

1. 8자 이상의 암호가 필요합니다. 사용자가 예측 가능한 암호를 선택하거나, 파일에 암호를 저장하거나, 적어둘 수 있으므로 긴 암호가 반드시 더 나은 것은 아닙니다.
2. **Summer2018!** 처럼 사용자가 암호를 쉽게 추측할 수 있는 만료 규칙을 사용하지 않습니다.
3. 사용자가 암호에 예측 가능한 문자 대용을 선택할 수 있으므로 문자 조합을 요구 사항을 사용하지 않고, 사용자가 일반적으로 공격 받는 암호를 선택하지 않도록 합니다.

Azure AD에서 직접 ID를 만드는 경우 사용자의 [암호가 만료되지 않도록 PowerShell](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)을 사용할 수 있습니다. 하이브리드 조직에서는 [도메인 그룹 정책 설정](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10)) 또는 [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy)을 사용하여 이러한 정책을 구현해야 합니다.

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>유출된 자격 증명을 보호 및 작동 중단에 대비해 복원력을 추가

조직에서 통과 인증 또는 페더레이션을 사용하는 하이브리드 ID 솔루션을 이용하는 경우 다음과 같은 두 가지 이유로 암호 해시 동기화를 사용하도록 설정해야 합니다.

* Azure AD 관리의 [유출된 자격 증명이 있는 사용자](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) 보고서는 “다크 웹”에서 노출된 사용자 이름 및 암호 쌍에 대해 경고합니다. 엄청난 양의 암호가 피싱, 맬웨어 및 나중에 위반되는 타사 사이트에서 암호 재사용을 통해 유출됩니다. Microsoft는 이러한 유출된 자격 증명 중 많은 부분을 찾아 조직의 자격 증명과 일치하는 경우 이 보고서를 통해 알립니다. 단, [암호 해시 동기화를 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)한 경우에만 적용됩니다!
* 온-프레미스 장애(예: 랜섬웨어 공격)의 경우 [암호 해시 동기화를 사용하여 클라우드 인증](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)으로 전환할 수 있습니다. 이 백업 인증 방법을 사용하면 Office 365를 비롯하여 Azure Active Directory를 통한 인증을 위해 구성된 앱에 계속 액세스할 수 있습니다. 이 경우 IT 직원은 온-프레미스 중단이 해결될 때까지 개인 이메일 계정을 사용하여 데이터를 공유할 필요가 없습니다.

[암호 해시 동기화](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization) 작동 방식에 대해 자세히 알아보세요.

> [!NOTE]
> 암호 해시 동기화를 사용하도록 설정하고 Azure AD Domain services를 사용하는 경우 Kerberos(AES 256) 해시 및 필요에 따라 NTLM(RC4, 솔트 없음) 해시도 암호화된 후 Azure AD와 동기화됩니다. 

### <a name="implement-ad-fs-extranet-smart-lockout"></a>AD FS 엑스트라넷 스마트 잠금 구현

Azure AD에 직접 인증하도록 애플리케이션을 구성하는 조직은 [Azure AD 스마트 잠금](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)의 이점을 활용합니다. Windows Server 2012 R2에서 AD FS를 사용하는 경우 AD FS [엑스트라넷 잠금 보호](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)를 구현합니다. Windows Server 2016에서 AD FS를 사용하는 경우 [엑스트라넷 스마트 잠금](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016)을 구현합니다. AD FS 스마트 엑스트라넷 잠금은 AD FS를 대상으로 하는 무차별 암호 대입 공격으로부터 사용자를 보호하면서 사용자가 Active Directory에서 잠기지 않도록 방지합니다.

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>본질적으로 안전하며 사용이 더 용이한 자격 증명의 이점 활용

[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)를 사용하면 PC 및 모바일 디바이스에서 강력한 2단계 인증으로 암호를 바꿀 수 있습니다. 이 인증은 디바이스에 안전하게 연결되는 사용자 자격 증명의 새로운 유형으로 구성되며 생체 인식 또는 PIN을 사용합니다.

## <a name="step-2---reduce-your-attack-surface"></a>2단계 - 공격 노출 영역 줄이기

암호 손상의 확장성을 고려할 때 조직에서 공격 노출 영역을 최소화하는 것은 중요합니다. 오래되고 덜 안전한 프로토콜의 사용 제거, 진입점 액세스 제한 및 리소스에 대한 더 중요한 관리 액세스의 제어 실행은 공격 노출 영역을 줄이는 데 도움이 될 수 있습니다.

### <a name="block-legacy-authentication"></a>레거시 인증 차단

자체 레거시 메서드를 사용하여 Azure AD로 인증하고 회사 데이터에 액세스하는 앱은 조직에 또 다른 위험을 제기합니다. 레거시 인증을 사용한 앱으로는 POP3, IMAP4 또는 SMTP 클라이언트가 있습니다. 레거시 인증 앱은 사용자를 대신하여 인증하고 Azure AD가 고급 보안 평가를 수행하지 못하도록 방지합니다. 대체 최신 인증은 다단계 인증 및 조건부 액세스를 지원하므로 보안 위험이 줄어듭니다. 다음과 같은 세 가지 작업이 권장됩니다.

1. [AD FS를 사용하는 경우 레거시 인증](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)을 차단합니다.
2. [최신 인증을 사용하도록 SharePoint Online 및 Exchange Online](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)을 설정합니다.
3. [레거시 인증을 차단하는 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions)을 사용합니다.

### <a name="block-invalid-authentication-entry-points"></a>잘못된 인증 진입점 차단

위반이 발생할 때 가상 위반 사고방식을 사용하여 손상된 사용자 자격 증명의 영향을 줄여야 합니다. 사용자 환경의 각 앱의 경우 권한이 있는 그룹, 네트워크, 디바이스 및 기타 요소가 무엇인지 유효한 사용 사례를 고려한 다음, 나머지를 차단합니다. [Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 사용하면 정의하는 특정 조건에 따라 자신의 앱 및 리소스에 권한이 부여된 사용자가 액세스하는 방법을 제어할 수 있습니다.

### <a name="block-end-user-consent"></a>최종 사용자 동의 차단

기본적으로 Azure AD의 모든 사용자는 OAuth 2.0 및 Microsoft ID [동의 프레임워크](https://docs.microsoft.com/azure/active-directory/develop/consent-framework)를 활용하는 애플리케이션에 회사 데이터 액세스 권한을 부여할 수 있습니다. 동의를 통해 사용자는 Microsoft 365 및 Azure와 통합되는 유용한 애플리케이션을 쉽게 획득할 수 있지만, 신중하게 사용하고 모니터링하지 않으면 위험 요소로 작용할 수 있습니다. [향후 모든 사용자 동의 작업을 사용하지 않도록 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-removing-user-access)하면 노출 영역을 줄이고 위험을 완화할 수 있습니다. 최종 사용자 동의를 사용하지 않도록 설정하면 기존 동의 부여는 계속 적용되지만, 이후부터는 모든 동의 작업을 관리자가 수행해야 합니다. 이 기능을 사용하지 않도록 설정하기 전에, 사용자에게 새 애플리케이션에 대한 관리자 승인을 요청하는 방법을 이해시키는 것이 좋습니다. 이렇게 하면 사용자 마찰을 줄이고, 지원 볼륨을 최소화하고, 사용자가 비 Azure AD 자격 증명을 사용하는 애플리케이션에 등록하지 못하게 할 수 있습니다.

### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management 구현

“가정 위반”의 또 다른 영향은 손상된 계정을 권한 있는 역할로 작동할 수 있는 가능성을 최소화해야 한다는 점입니다. 

[Azure AD PIM(Privileged Identity Management)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)은 다음을 수행하는 데 유용하여 계정 권한을 최소화하는 데 도움이 됩니다.

* 관리자 역할에 할당된 사용자를 식별하고 관리합니다.
* 제거해야 하는 사용되지 않거나 과도한 권한 역할을 파악합니다.
* 권한 있는 역할이 다단계 인증으로 보호되도록 규칙을 설정합니다.
* 권한 있는 역할이 권한 있는 작업을 수행하기에 충분히 긴 기간 동안만 부여되도록 규칙을 설정합니다.

Azure AD PIM을 사용하도록 설정한 다음, 관리자 역할이 할당된 사용자를 보고, 해당 역할에서 필요 없는 계정을 제거합니다. 나머지 권한 있는 사용자의 경우 영구 상태에서 적격 상태로 이동합니다. 마지막으로, 사용자가 권한 있는 역할에 대한 액세스 권한을 얻어야 하는 경우 필요한 변경 제어를 통해 안전하게 얻을 수 있습니다.

권한 있는 계정 프로세스 배포의 일부로, 직접 잠글 경우 Azure AD에 액세스할 수 있도록 [두 개 이상의 비상 계정을 만드는 모범 사례](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)를 수행합니다.

## <a name="step-3---automate-threat-response"></a>3단계 - 위협 응답 자동화

Azure Active Directory에는 검색 및 응답 사이의 대기 시간을 제거하기 위해 자동으로 공격을 가로채는 많은 기능이 있습니다. 범죄자들이 사용자 환경에 진입하는 데 사용하는 시간을 줄이면 비용 및 위험을 줄일 수 있습니다. 다음은 이를 수행할 수 있는 구체적인 단계입니다.

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection을 사용하여 사용자 위험 보안 정책 구현

사용자 위험은 사용자의 ID가 손상되었다는 가능성을 나타내며, 사용자의 ID와 연결된 [사용자 위험 이벤트](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)를 기반으로 계산됩니다. 사용자 위험 정책은 특정 사용자 또는 그룹에 대해 위험 수준을 평가하는 조건부 액세스 정책입니다. 낮음, 보통, 높음 위험 수준에 따라 액세스를 차단하거나 다단계 인증을 사용한 보안 암호 변경을 요구하도록 정책을 구성할 수 있습니다. Microsoft의 권장 사항은 높은 위험 수준에 있는 사용자의 경우 보안 암호 변경이 필요합니다.

![위험에 대한 플래그가 지정된 사용자](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection을 사용하여 로그인 위험 정책 구현

로그인 위험은 계정 소유자가 아닌 누군가가 ID를 사용하여 로그인을 시도할 가능성을 말합니다. [로그인 위험 정책](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 특정 사용자 또는 그룹에 대해 위험 수준을 평가하는 조건부 액세스 정책입니다. 위험 수준(높음/보통/낮음)에 따라 정책은 액세스를 차단하거나 다단계 인증을 강제 적용하도록 구성할 수 있습니다. 보통 이상의 위험 로그인에서는 다단계 인증을 받도록 해야 합니다.

![익명 IP 주소에서 로그인](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>4단계 - 인식률 높이기

보안 관련 이벤트의 감사와 로깅 및 관련 경고는 효과적인 보호 전략의 중요한 구성 요소입니다. 보안 로그 및 보고서는 의심스러운 활동에 대한 전자 기록을 제공하며, 네트워크의 외부 침투와 내부 공격의 시도 또는 성공을 나타낼 수 있는 패턴을 검색할 수 있도록 도움을 줍니다. 감사를 사용하여 사용자 활동을 모니터링하고, 규제 준수를 문서화하며, 법정 분석 등을 수행할 수 있습니다. 경고는 보안 이벤트의 알림을 제공합니다.

### <a name="monitor-azure-ad"></a>Azure AD 모니터링

Microsoft Azure 서비스 및 기능은 구성 가능한 보안 감사 및 로깅 옵션을 제공하여 보안 정책과 메커니즘의 차이를 식별하고 이러한 차이를 해결하여 위반을 방지할 수 있게 합니다. [Azure 로깅 및 감사](https://docs.microsoft.com/azure/security/azure-log-audit)와 [Azure Active Directory 포털의 감사 작업 보고서](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)를 사용할 수 있습니다.

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>하이브리드 환경에서 Azure AD Connect Health 모니터링

[Azure AD Connect Health를 통한 AD FS 모니터링](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)은 AD FS 인프라에서 잠재적인 문제 및 공격의 시각화에 대한 통찰력을 높여줍니다. Azure AD Connect Health는 세부 정보, 해결 단계 및 인증 트래픽에 관련된 여러 가지 메트릭에 대한 사용 현황 분석, 성능 모니터링 및 보고서와 같은 관련된 문서에 대한 링크를 제공합니다.

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD ID Identity Protection 이벤트 모니터링

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)은 조직의 ID에 영향을 주는 잠재적 취약성을 검색하는 데 사용할 수 있는 알림, 모니터링 및 보고 도구입니다. 유출된 자격 증명, 불가능한 이동 및 감염된 디바이스, 익명 IP 주소, 의심스러운 활동 및 알 수 없는 위치에 연결된 IP 주소에서의 로그인과 같은 위험 이벤트를 검색합니다. 위험한 사용자의 이메일을 수신 및/또는 주 단위 요약 이메일을 수신하는 알림 경고를 사용하도록 설정합니다.

Azure AD Identity Protection은 매일 모니터링해야 하는 두 가지 중요한 보고서를 제공합니다.
1. 위험한 로그인 보고서는 로그인한 사람이 정당한 소유자가 아닐 가능성이 있으므로 조사가 필요한 사용자 로그인 활동을 보여줍니다.
2. 위험한 사용자 보고서는 유출된 자격 증명 발견, 여러 위치에서 로그인하여 불가능한 이동 이벤트를 발생시킨 사용자처럼 손상 가능성이 있는 사용자 계정을 보여줍니다. 

![위험에 대한 플래그가 지정된 사용자](media/azure-ad/azure-ad-sec-steps3.png)

### <a name="audit-apps-and-consented-permissions"></a>감사 앱 및 승인된 권한

손상된 웹 사이트 또는 앱으로 이동하도록 사용자를 속여서 프로필 정보나 이메일 같은 사용자 데이터를 획득할 수 있습니다. 악의적인 행위자는 획득한 승인된 권한을 사용하여 사서함 콘텐츠를 암호화하고, 사서함 데이터를 되찾으려면 몸값을 지불하라고 요구할 수 있습니다. 관리자는 사용자가 제공한 권한을 [검토 및 감사](https://blogs.technet.microsoft.com/office365security/defending-against-illicit-consent-grants/)해야 합니다.

## <a name="step-5---enable-end-user-self-help"></a>5단계 - 최종 사용자 자가 진단 사용

사용자는 최대한 생산성과 보안의 균형을 맞추고자 합니다. 결국 보안에 대한 기반을 설정하는 사고 방식을 통해 경험에 착수하는 것과 같은 방식으로 사용자에게 권한을 부여하면서 한편으론 경계를 게을리하지 않으면 조직에서 마찰을 없앨 수 있습니다. 

### <a name="implement-self-service-password-reset"></a>셀프 서비스 암호 재설정 구현

IT 관리자는 Azure의 [SSPR(셀프 서비스 암호 재설정)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)을 사용하여 사용자에게 해당 암호 또는 계정을 관리자 작업 없이 재설정하거나 잠금 해제할 수 있도록 할 수 있습니다. 이 시스템에는 오용 또는 남용에 대해 경고하는 알림과 함께 사용자가 언제 시스템에 액세스하는지 추적하는 구체적인 보고서가 포함되어 있습니다. 

### <a name="implement-self-service-group-management"></a>셀프 서비스 그룹 관리 구현

Azure AD는 보안 그룹 및 Office 365 그룹을 사용하여 리소스에 대한 액세스를 관리하는 기능을 제공합니다. 이러한 그룹은 IT 관리자 대신 그룹 소유자가 관리할 수 있습니다. [셀프 서비스 그룹 관리](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)로 알려진 이 기능을 사용하면 관리자가 해당 요청을 처리하길 의존하지 않고도 관리 역할에 할당되지 않은 그룹 소유자가 그룹을 만들고 관리할 수 있습니다.

### <a name="implement-azure-ad-access-reviews"></a>Azure AD 액세스 검토 구현

[Azure AD 액세스 검토](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)를 사용하면 필요하지 않은 경우 연장된 기간 동안 사용자에게 액세스 권한을 부여하지 않는 보안 표준을 유지하도록 그룹 구성원, 엔터프라이즈 애플리케이션에 대한 액세스 및 권한 있는 역할 할당을 관리할 수 있습니다.

## <a name="summary"></a>요약

보안 ID 인프라에 대한 여러 측면이 있지만 이 5가지 검사 목록은 더 안전한 ID 인프라를 신속하게 달성하는 데 도움이 됩니다.

* 자격 증명을 강화합니다.
* 공격 노출 영역을 줄입니다.
* 위협 응답을 자동화합니다.
* 감사 및 모니터링의 인식률을 높입니다.
* 자가 진단으로 더 예측 가능하고 완벽한 최종 사용자 보안을 사용하도록 설정합니다.

ID 보안에 대해 진지하게 고려해 주신 것에 감사를 드리며, 이 문서가 여러분 조직의 더 안전한 태세를 위한 유용한 로드맵이 되길 바랍니다.

## <a name="next-steps"></a>다음 단계
권장 사항의 계획 및 배포에 대한 도움이 필요한 경우 [Azure AD 프로젝트 배포 계획](https://aka.ms/deploymentplans)을 참조하세요.
