---
title: Azure ID 및 액세스 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 ID 관리 및 액세스 제어에 대한 몇 가지 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: f872c61ad0597d2307cd244668fdfc258f7a45cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611244"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management 및 액세스 제어 보안 모범 사례

많은 사람이 ID를 기존 네트워크 중심 관점에서 역할을 대신하는 보안에 대한 새로운 경계 계층으로 생각하고 있습니다. 보안 주의 및 투자를 중심으로 하는 이러한 진화는 네트워크 경계에 점점 더 많은 구멍이 생기고 경계 방어가 [BYOD](https://aka.ms/byodcg) 장치 및 클라우드 애플리케이션이 급증하기 전만큼 효과적일 수 없다는 사실에 기인합니다.

이 문서에서는 Azure ID 관리 및 액세스 제어 보안 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 [Azure AD](../active-directory/fundamentals/active-directory-whatis.md)에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure ID 관리 및 액세스 제어 보안 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다. 이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 설명하는 Azure Identity Management 및 액세스 제어 보안 모범 사례는 다음과 같습니다.

* 기본 보안 경계로 ID 처리
* ID 관리 중앙 집중화
* Single Sign-On 사용
* 조건부 액세스 설정
* 암호 관리 사용
* 사용자에 대한 다단계 인증 적용
* 역할 기반 액세스 제어 사용
* 권한 있는 계정의 낮은 노출
* 리소스가 있는 위치 제어

## <a name="treat-identity-as-the-primary-security-perimeter"></a>기본 보안 경계로 ID 처리

다수가 ID를 보안에 대한 기본 경계라고 간주합니다. 기존 네트워크 보안에서 집중한 부분에서 전환되었습니다. 네트워크 경계에 더 많은 더 구멍이 생기고, 해당 경계 방어가 [BYOD](https://aka.ms/byodcg) 디바이스 및 클라우드 애플리케이션이 급증하기 전처럼 효과적일 수 없습니다.
[Azure AD(Azure Active Directory)](../active-directory/active-directory-whatis.md)는 ID 및 액세스 관리를 위한 Azure 솔루션입니다. Azure AD는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 여기에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호를 하나의 솔루션으로 결합합니다.

다음 섹션에서는 Azure AD를 사용하여 ID 및 액세스 보안에 대한 모범 사례를 나열합니다.

## <a name="centralize-identity-management"></a>ID 관리 중앙 집중화

[하이브리드 ID](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) 시나리오에서 온-프레미스와 클라우드 디렉터리를 통합하는 것이 좋습니다. 통합을 사용하면 IT 팀이 계정을 만든 위치에 관계 없이 하나의 단일 위치에서 계정을 관리할 수 있습니다. 통합을 통해 온-프레미스 및 클라우드 리소스 모두에 액세스하기 위한 일반적인 ID를 제공하여 사용자가 더 생산성을 높일 수 있습니다.


**모범 사례**: Azure AD와 온-프레미스 디렉터리를 통합합니다.  
**세부 정보**: [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md)를 사용하여 온-프레미스 디렉터리를 클라우드 디렉터리와 동기화합니다.

**모범 사례**: 암호 해시 동기화를 켭니다.  
**세부 정보**: 암호 해시 동기화는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화하는 데 사용되는 기능입니다.

AD FS(Active Directory Federation Service) 또는 다른 ID 공급자에서 페더레이션을 사용하도록 결정한 경우에도 온-프레미스 서버가 실패하거나 일시적으로 사용할 수 없게 되므로 필요에 따라 암호 해시 동기화를 백업으로 설정할 수 있습니다. 이렇게 하면 사용자가 온-프레미스 Active Directory 인스턴스에 로그인하는 데 사용하는 것과 동일한 암호를 사용하여 서비스에 로그인할 수 있습니다. 또한 사용자가 Azure AD에 연결되지 않은 다른 서비스에서 동일한 이메일 주소와 암호를 사용하면 Identity Protection에서 암호 해시와 손상된 것으로 알려진 암호를 비교하여 손상된 자격 증명을 검색할 수 있습니다.

자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)을 참조하세요.

클라우드 ID와 해당 온-프레미스 ID를 통합하지 않는 조직은 계정을 관리하는 데 추가 오버헤드가 발생할 수 있습니다. 이 오버헤드는 실수 및 보안 위반의 가능성을 증가시킵니다.

## <a name="enable-single-sign-on"></a>Single Sign-On 사용

사용자가 어디서나 언제나 생산성을 높일 수 있도록 모바일 우선, 클라우드 우선 환경에서 디바이스, 앱 및 서비스에 SSO(Single Sign-On)를 사용할 수 있도록 합니다. 관리할 ID 솔루션이 여러 개인 경우 이는 IT뿐만 아니라 여러 암호를 기억해야 하는 사용자에게도 관리 문제가 될 수 있습니다.

모든 앱 및 리소스에 동일한 ID 솔루션을 사용하여 SSO를 설정할 수 있습니다. 사용자가 리소스가 온-프레미스 또는 클라우드에 있는지와 관계 없이 동일한 자격 증명 집합을 사용하여 로그인하고 필요한 리소스에 액세스할 수 있습니다.

**모범 사례**: SSO를 사용하도록 설정합니다.  
**세부 정보**: Azure AD는 [온-프레미스 Active Directory](../active-directory/connect/active-directory-aadconnect.md)를 클라우드로 확장합니다. 사용자가 작업을 수행하는 데 필요한 도메인에 조인된 디바이스, 회사 리소스 및 모든 웹 및 SaaS 애플리케이션에 대해 해당하는 기본 회사 또는 학교 계정을 사용할 수 있습니다. 사용자는 여러 사용자 이름과 암호들을 기억할 필요가 없고, 사용자의 애플리케이션 액세스는 조직 그룹 구성원 혹은 구성원의 상태에 따라 자동으로 프로비전되거나 프로비전이 해제될 수 있습니다. 그리고 [Azure AD Application Proxy](../active-directory/active-directory-application-proxy-get-started.md)를 통해 개발 및 게시된 사용자 고유의 온-프레미스 앱이나 갤러리 앱에 대한 액세스를 제어할 수 있습니다.

SSO를 사용하여 사용자가 Azure AD에서 회사 또는 학교 계정을 기반으로 해당 [SaaS 애플리케이션](../active-directory/active-directory-appssoaccess-whatis.md)에 액세스할 수 있습니다. 이 방법은 Microsoft SaaS 앱뿐만 아니라 [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) 및 [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md) 등 다른 앱에도 적용할 수 있습니다. Azure AD를 [SAML 기반 ID](../active-directory/fundamentals-identity.md) 공급자로 사용하도록 애플리케이션을 구성할 수 있습니다. 보안 컨트롤인 Azure AD는 Azure AD를 통해 액세스 권한을 부여한 경우가 아니면 사용자가 애플리케이션에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 직접적으로 또는 사용자가 멤버인 그룹을 통해 액세스를 부여할 수 있습니다.

사용자 및 애플리케이션에 대해 SSO를 설정하는 일반 ID를 만들지 않는 조직은 사용자에게 여러 개의 암호가 있는 시나리오에 더 많이 노출됩니다. 이러한 시나리오에서는 사용자가 암호를 다시 사용하거나 취약한 암호를 사용할 가능성이 높아집니다.

## <a name="turn-on-conditional-access"></a>조건부 액세스 설정

사용자가 다양한 디바이스와 앱을 사용하여 어디서나 조직의 리소스에 액세스할 수 있습니다. IT 관리자로서 이러한 디바이스가 보안 및 규정 준수에 대한 표준을 충족하는지 확인하려고 합니다. 리소스에 액세스할 수 있는 사용자에게만 초점을 맞추는 것은 더 이상 충분하지 않습니다.

보안과 생산성을 조정하려면 액세스 제어를 결정하기 전에 리소스에 액세스하는 방법에 대해 고려해야 합니다. Azure AD 조건부 액세스를 사용하면 이 요구 사항을 처리할 수 있습니다. 조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자를 결정하는 자동 액세스 제어 결정 시스템을 만들 수 있습니다.

**모범 사례**: 회사 리소스에 대한 액세스를 관리 및 제어합니다.  
**세부 정보**: SaaS 앱 및 Azure AD 연결 앱에 대한 그룹, 위치 및 애플리케이션 민감도에 따라 Azure AD [조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)를 구성합니다.

## <a name="enable-password-management"></a>암호 관리 사용

테넌트가 여럿이거나 사용자가 [자신의 암호를 재설정](../active-directory/active-directory-passwords-update-your-own-password.md)할 수 있도록 하려는 경우 남용을 방지하기 위해 적절한 보안을 사용해야 합니다.

**모범 사례**: 사용자에 대해 SSPR(셀프 서비스 암호 재설정)을 설정합니다.  
**세부 정보**: Azure AD [셀프 서비스 암호 재설정](../active-directory-b2c/active-directory-b2c-reference-sspr.md) 기능을 사용합니다.

**모범 사례**: SSPR을 실제로 사용하는 방법 또는 그러한 경우를 모니터링합니다.  
**세부 정보**: Azure AD [암호 재설정 등록 작업 보고서](../active-directory/active-directory-passwords-get-insights.md)를 사용하여 등록하는 사용자를 모니터링합니다. Azure AD에서 제공하는 보고 기능은 미리 작성된 보고서를 사용하여 질문에 대답하도록 도와줍니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다.

## <a name="enforce-multi-factor-verification-for-users"></a>사용자에 대한 다단계 인증 적용

모든 사용자에 대해 2단계 인증을 요구하는 것이 좋습니다. 해당 계정이 노출될 경우 상당한 영향을 미칠 수 있는 조직의 관리자 및 다른 사용자가 포함됩니다(예: 재무 책임자).

2단계 인증을 요구하는 여러 옵션이 있습니다. 최상의 옵션은 목표, 실행하는 Azure AD 버전 및 라이선스 프로그램에 따라 달라집니다. [사용자에 대해 2단계 인증을 요구하는 방법](../active-directory/authentication/howto-mfa-userstates.md)을 참조하여 최상의 옵션을 결정합니다. 라이선스 및 가격 책정에 대한 자세한 내용은 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 및 [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 가격 책정 페이지를 참조하세요.

2단계 인증을 사용하도록 설정하는 옵션 및 혜택은 다음과 같습니다.

**옵션 1**: [사용자 상태를 변경하여 Multi-Factor Authentication을 사용하도록 설정합니다](../active-directory/authentication/howto-mfa-userstates.md).   
**혜택**: 2단계 인증을 요구하는 기존 메서드입니다. 이 기능은 [클라우드의 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버](../active-directory/authentication/concept-mfa-whichversion.md)에서 작동합니다. 이 방법을 사용할 경우 사용자는 로그인할 때마다 2단계 인증을 수행해야 하며, 조건부 액세스 정책을 재정의합니다.

**옵션 2**: [조건부 액세스 정책을 사용하여 Multi-Factor Authentication을 사용하도록 설정합니다](../active-directory/authentication/howto-mfa-getstarted.md).
**혜택**: 이 옵션을 사용하면 [조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)를 사용하여 특정 조건에서 2단계 인증을 묻는 메시지를 표시할 수 있습니다. 특정 조건이란 위험한 것으로 간주하는 다른 위치, 신뢰할 수 없는 디바이스 또는 애플리케이션에서 사용자 로그인이 될 수 있습니다. 2단계 인증이 필요한 특정 조건을 정의하면 번거로운 사용자 환경일 수 있는 지속적인 메시지를 사용자에게 표시하지 않도록 할 수 있습니다.

이것이 사용자에게 2단계 인증을 사용하도록 설정하는 가장 유연한 방법입니다. 조건부 액세스 정책을 사용하도록 설정하는 방법은 클라우드의 Azure Multi-Factor Authentication에서만 가능하며, Azure AD의 프리미엄 기능입니다. [클라우드 기반 Azure Multi-factor Authentication 배포](../active-directory/authentication/howto-mfa-getstarted.md)에서 이 방법에 대한 자세한 내용을 확인할 수 있습니다.

**옵션 3**: [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md)의 사용자 및 로그인 위험을 평가하여 조건부 액세스 정책에서 Multi-Factor Authentication을 사용하도록 설정합니다.   
**혜택**: 이 옵션을 사용하면 다음을 수행할 수 있습니다.

- 조직의 ID에 영향을 미치는 잠재적인 취약점 검색
- 조직 ID와 관련된 검색된 의심스러운 작업에 대한 자동화된 응답 구성
- 의심스러운 인시던트 조사 및 해결할 적절한 작업 수행

이 방법은 Azure AD Identity Protection 위험 평가를 사용하여 모든 클라우드 애플리케이션에 대한 사용자 및 로그인 위험에 따라 2단계 인증이 필요한지 결정합니다. 이 방법에는 Azure Active Directory P2 라이선스가 필요합니다. [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md)에서 이 방법에 대한 자세한 내용을 찾을 수 있습니다.

> [!Note]
> 옵션 1, 사용자 상태를 변경하여 Multi-Factor Authentication을 사용하면 조건부 액세스 정책이 재정의됩니다. 옵션 2 및 3이 조건부 액세스 정책을 사용하기 때문에 여기에서 옵션 1을 사용할 수 없습니다.

2단계 인증과 같은 추가적인 ID 보호 계층을 추가하지 않는 조직은 자격 증명 도난 공격에 취약합니다. 자격 증명 도난 공격으로 인해 데이터 손상이 발생할 수 있습니다.

## <a name="use-role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어) 사용

[알아야 할 사항](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙을 기반으로 액세스를 제한하는 것은 데이터 액세스에 보안 정책을 적용하고자 하는 조직의 경우 필수입니다. [RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하여 특정 범위에서 사용자, 그룹 및 애플리케이션에 사용 권한을 할당할 수 있습니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다.

Azure의 [기본 제공 RBAC](../role-based-access-control/built-in-roles.md) 역할을 사용하여 사용자에게 권한을 할당할 수 있습니다. RBAC와 같은 기능을 사용하여 데이터 액세스 제어를 적용하지 않는 조직은 해당 사용자에게 필요 이상으로 많은 권한을 부여하게 될 수 있습니다. 이로 인해 사용자가 없어야 했던 특정 형식의 데이터(예: 높은 비즈니스 영향)에 액세스할 수 있도록 함으로써 데이터 손상을 초래할 수 있습니다.

## <a name="lower-exposure-of-privileged-accounts"></a>권한 있는 계정의 낮은 노출

권한 있는 액세스 보안은 비즈니스 자산 보호를 위해 중요한 첫 번째 단계입니다. 보안 정보 또는 리소스에 액세스할 수 있는 사용자의 수를 최소화하면 악의적인 사용자가 해당 액세스 권한을 얻거나 권한이 있는 사용자가 실수로 중요한 리소스에 영향을 줄 가능성이 감소합니다.

권한 있는 계정은 IT 시스템을 운영하고 관리하는 계정입니다. 사이버 공격자는 조직의 데이터와 시스템에 대한 액세스 권한을 얻기 위해 이러한 계정을 대상으로 지정합니다. 권한 있는 액세스를 보호하려면 계정과 시스템을 악의적 사용자에게 노출될 위험으로부터 격리해야 합니다.

사이버 공격자로부터 권한 있는 액세스를 보호하기 위한 로드맵을 개발하고 따르는 것이 좋습니다. Azure AD, Microsoft Azure, Office 365 및 기타 클라우드 서비스에서 관리되거나 보고되는 보안 ID 및 액세스에 대한 자세한 로드맵을 만드는 방법에 대한 자세한 내용은 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../active-directory/users-groups-roles/directory-admin-roles-secure.md)을 검토하세요.

다음에서는 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](../active-directory/users-groups-roles/directory-admin-roles-secure.md)에 있는 모범 사례를 요약합니다.

**모범 사례**: 권한 있는 계정에 대한 액세스를 관리, 제어 및 모니터링합니다.   
**세부 정보**: [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md)를 켭니다. Privileged Identity Management가 설정되면 권한 있는 액세스 역할 변경에 대한 알림 이메일 메시지를 받게 됩니다. 이러한 알림에서는 디렉터리에서 권한이 높은 역할에 추가 사용자가 추가될 때 조기 경고를 제공합니다.

**모범 사례**: 권한이 높은 역할이 있는 계정을 식별 및 분류합니다.   
**세부 정보**: Azure AD Privileged Identity Management를 설정하면 글로벌 관리자, 권한 있는 역할 관리자 및 기타 권한이 높은 관리자에 속한 사용자를 확인합니다. 이러한 역할에 더 이상 필요하지 않은 계정을 제거하고 관리자 역할에 할당된 나머지 계정은 다음과 같이 분류합니다.

- 관리 사용자에게 개별적으로 할당되고, 비관리 용도(예: 개인 이메일)로 사용될 수도 있습니다.
- 관리 사용자에게 개별적으로 할당되고, 관리 용도로만 지정됩니다.
- 여러 사용자 간에 공유됩니다.
- 응급 액세스 시나리오용
- 자동화된 스크립트용
- 외부 사용자용

**모범 사례**: "JIT(Just-In-Time)" 액세스 권한을 구현하여 권한의 노출 시간을 줄이고 권한 있는 계정의 사용에 대한 가시성을 높입니다.   
**세부 정보**: Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

- 사용자가 해당 권한 JIT에서만 사용하도록 제한합니다.
- 권한을 자동으로 해지하는 짧은 기간 동안 자신 있게 역할을 할당합니다.

**모범 사례**: 둘 이상의 응급 액세스 계정을 정의합니다.   
**세부 정보**: 응급 액세스 계정을 사용하면 조직이 기존 Azure Active Directory 환경 내에서 권한 있는 액세스를 제한할 수 있습니다. 이러한 계정은 높은 권한을 부여받으며 특정 개인에게 할당되지 않습니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 시나리오로 제한됩니다. 조직에서는 응급 계정의 사용량을 필요한 기간으로만 제한해야 합니다.

전역 관리자 역할에 할당되었거나 적합한 계정을 평가합니다. `*.onmicrosoft.com` 도메인을 사용하여 클라우드 전용 계정(응급 액세스용)이 표시되지 않으면 해당 계정을 만듭니다. 자세한 내용은 Azure AD에서 응급 액세스 관리 계정의 관리를 참조하세요.

**모범 사례**: Multi-Factor Authentication을 켜고, 권한이 높고 페더레이션되지 않은 다른 모든 단일 사용자 관리자 계정을 등록합니다.  
**세부 정보**: 로그인 시 Azure AD 관리자 역할(글로벌 관리자, 권한 있는 역할 관리자, Exchange Online 관리자 및 SharePoint Online 관리자) 중 하나 이상에 영구적으로 할당된 모든 개별 사용자에 대해 Azure Multi-Factor Authentication을 요구합니다. 이 가이드를 사용하여 [관리자 계정에 대해 Multi-Factor Authentication](../active-directory/authentication/howto-mfa-userstates.md)을 사용하도록 설정하고, 해당 사용자가 모두 [등록](https://aka.ms/mfasetup)되었는지 확인합니다.

**모범 사례**: 가장 많이 사용되는 공격 기술을 완화하는 단계를 수행합니다.  
**세부 정보**: [직장 또는 학교 계정으로 전환해야 하는 관리 역할의 Microsoft 계정 식별](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[글로벌 관리자 계정에 대해 별도의 사용자 계정 및 메일 전달 보장](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[관리 계정의 암호가 최근에 변경되었는지 확인](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[암호 해시 동기화 설정](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[모든 권한이 있는 역할의 사용자 및 노출된 사용자에 대해 Multi-Factor Authentication 요구](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Office 365 보안 점수 가져오기(Office 365를 사용하는 경우)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Office 365 보안 및 규정 준수 지침 검토(Office 365를 사용하는 경우)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365 작업 모니터링 구성(Office 365를 사용하는 경우)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[인시던트/비상 대응 계획 소유자 설정](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[권한 있는 온-프레미스 관리 계정 보호](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

권한 있는 액세스를 보호하지 않는 경우 권한이 높은 역할에 너무 많은 사용자가 포함되면 공격에 더 취약할 수 있습니다. 사이버 공격자를 포함한 악의적인 행위자는 자격 증명 도난을 사용하여 중요한 데이터 및 시스템에 대해 액세스하는 권한 있는 액세스의 관리자 계정 및 기타 요소를 대상으로 지정하는 경우가 많습니다.

## <a name="control-locations-where-resources-are-created"></a>리소스를 만든 위치 제어

클라우드 운영자가 조직의 리소스를 관리하는 데 필요한 규칙을 위반하지 않으면서도 작업을 수행할 수 있도록 하는 것이 아주 중요합니다. 리소스가 만들어지는 위치를 제어하고자 하는 조직은 이러한 위치를 하드 코딩해야 합니다.

[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)를 사용하여 명시적으로 거부된 작업 또는 리소스를 설명하는 정의가 포함된 보안 정책을 만들 수 있습니다. 구독, 리소스 그룹 또는 개별 리소스와 같이 원하는 범위에서 해당 정책 정의를 할당합니다.

> [!NOTE]
> 보안 정책은 RBAC와 동일하지 않습니다. 실제로 RBAC를 사용하여 해당 리소스를 만드는 사용자에게 권한을 부여합니다.
>
>

리소스를 만드는 방법을제어하지 않는 조직은 사용자가 필요 이상으로 많은 리소스를 만들어 서비스를 남용할 수 있는 상황에 직면할 가능성이 더 높습니다. 리소스 만들기 프로세스의 강화는 다중 테넌트 시나리오를 보호하기 위한 중요한 단계입니다.

## <a name="actively-monitor-for-suspicious-activities"></a>의심스러운 활동을 적극적으로 모니터링

활성 ID 모니터링 시스템은 의심스러운 동작을 신속하게 검색하고 추가로 조사하기 위해 경고를 트리거할 수 있습니다. 다음 표에서는 조직이 해당 ID를 모니터링할 수 있도록 하는 두 개의 Azure AD 기능을 보여줍니다.

**모범 사례**: 식별할 방법을 보유합니다.

- [추적되지 않고](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md) 로그인하려고 시도합니다.
- [무차별 암호 대입 공격](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md)은 특정 계정에 대한 공격입니다.
- 여러 위치에서 로그인을 시도합니다.
- [감염된 디바이스](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md)에서 로그인합니다.
- 의심스러운 IP 주소

**세부 정보**: Azure AD Premium [비정상 보고서](../active-directory/active-directory-view-access-usage-reports.md)를 사용합니다. IT 관리자가 매일 또는 필요 시(대개 사고 대응 시나리오에서) 이러한 보고서를 실행하도록 하는 프로세스 및 절차를 시행합니다.

**모범 사례**: 위험을 알려주고 위험 수준(높음, 중간 또는 낮음)을 비즈니스 요구 사항에 맞게 조정할 수 있는 활성 모니터링 시스템이 있습니다.   
**세부 정보**: [Azure AD ID Protection](../active-directory/active-directory-identityprotection.md)을 사용하여 자체 대시보드에 현재 위험을 플래그로 표시하고 이메일을 통해 일일 요약 알림을 보냅니다. 조직의 ID를 보호하려면 지정된 위험 수준에 도달했을 때 검색된 문제에 자동으로 대응하는 위험 기반 정책을 구성할 수 있습니다.

해당 ID 시스템을 적극적으로 모니터링하지 않는 조직은 사용자 자격 증명이 손상될 위험에 직면합니다. 이러한 자격 증명을 통해 의심스러운 활동이 일어나고 있다는 것을 알아야 이 형식의 위협을 완화시킬 수 있습니다.

## <a name="next-step"></a>다음 단계

Azure를 사용하여 클라우드 솔루션을 설계/배포/관리하는 경우 사용할 수 있는 더 많은 보안 모범 사례는 [Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)을 참조하세요.
