---
title: Azure ID 및 액세스 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 기본 제공 Azure 기능을 사용한 ID 관리 및 액세스 제어에 대한 몇 가지 모범 사례를 제공합니다.
services: security
documentationcenter: na
author: barclayn
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: barclayn
ms.openlocfilehash: 52ef3a9b1df058d5d2e954b424094f9dbaeba15b
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053352"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Azure Identity Management 및 액세스 제어 보안 모범 사례

이 문서에서는 Azure ID 관리 및 액세스 제어 보안 모범 사례 컬렉션에 대해 설명합니다. 이러한 모범 사례는 [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md)에 대한 Microsoft의 경험 그리고 여러분 같은 고객의 경험에서 얻은 것입니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

* 각 모범 사례
* 해당 모범 사례를 사용해야 하는 이유
* 해당 모범 사례를 사용하지 않을 경우에 발생할 수 있는 결과
* 해당 모범 사례를 대체할 수 있는 대안
* 해당 모범 사례를 사용하는 방법을 알아보는 방법

이 Azure ID 관리 및 액세스 제어 보안 모범 사례 문서는 이 문서가 작성될 당시의 합의된 의견과 Azure 플랫폼 기능 및 특징 집합을 기반으로 합니다.

이 문서를 작성 하는 목적은 "[id 인프라를 보호 하는 5 단계](steps-secure-identity.md)" 검사 목록에서 배포한 후 보다 강력한 보안 상태에 대 한 일반 로드맵을 제공 하는 것입니다 .이를 통해 핵심 기능 중 일부를 안내 합니다. 서비스.

이 문서는 시간이 지남에 따라 변화하는 의견 및 기술을 반영하도록 주기적으로 업데이트 됩니다.

이 문서에서 설명하는 Azure Identity Management 및 액세스 제어 보안 모범 사례는 다음과 같습니다.

* 기본 보안 경계로 ID 처리
* ID 관리 중앙 집중화
* 연결 된 테넌트 관리
* Single Sign-On 사용
* 조건부 액세스 설정
* 정기 보안 강화 계획
* 암호 관리 사용
* 사용자에 대한 다단계 인증 적용
* 역할 기반 액세스 제어 사용
* 권한 있는 계정의 낮은 노출
* 리소스가 있는 위치 제어
* 저장소 인증에 Azure AD 사용

## <a name="treat-identity-as-the-primary-security-perimeter"></a>기본 보안 경계로 ID 처리

다수가 ID를 보안에 대한 기본 경계라고 간주합니다. 기존 네트워크 보안에서 집중한 부분에서 전환되었습니다. 네트워크 경계에 더 많은 더 구멍이 생기고, 해당 경계 방어가 [BYOD](https://aka.ms/byodcg) 디바이스 및 클라우드 애플리케이션이 급증하기 전처럼 효과적일 수 없습니다.

[Azure AD(Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md)는 ID 및 액세스 관리를 위한 Azure 솔루션입니다. Azure AD는 Microsoft의 다중 테넌트 클라우드 기반 디렉터리 및 ID 관리 서비스입니다. 여기에서는 핵심 디렉터리 서비스, 애플리케이션 액세스 관리 및 ID 보호를 하나의 솔루션으로 결합합니다.

다음 섹션에서는 Azure AD를 사용하여 ID 및 액세스 보안에 대한 모범 사례를 나열합니다.

**모범 사례**: 사용자 및 서비스 id를 중심으로 보안 제어 및 검색을 중심으로 합니다.
**세부 정보**: Azure AD를 사용 하 여 컨트롤 및 id를 배치 합니다.

## <a name="centralize-identity-management"></a>ID 관리 중앙 집중화

[하이브리드 ID](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) 시나리오에서 온-프레미스와 클라우드 디렉터리를 통합하는 것이 좋습니다. 통합을 통해 IT 팀은 계정 생성 위치에 관계 없이 한 위치에서 계정을 관리할 수 있습니다. 또한 통합을 사용 하면 클라우드 및 온-프레미스 리소스 모두에 액세스할 수 있는 공통 id를 제공 하 여 사용자의 생산성을 높일 수 있습니다.

**모범 사례**: 단일 Azure AD 인스턴스를 설정 합니다. 일관성과 신뢰할 수 있는 단일 소스는 명확성을 높이고 사용자 오류 및 구성 복잡성의 보안 위험을 줄입니다.
**세부 정보**: 단일 Azure AD 디렉터리를 회사 및 조직 계정에 대 한 신뢰할 수 있는 원본으로 지정 합니다.

**모범 사례**: Azure AD와 온-프레미스 디렉터리 통합  
**세부 정보**: [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect)를 사용하여 온-프레미스 디렉터리를 클라우드 디렉터리와 동기화합니다.

> [!Note]
> [Azure AD Connect의 성능에 영향을 주는 요인이](../../active-directory/hybrid/plan-connect-performance-factors.md)있습니다. 실적이 떨어지는 시스템이 방해할 보안 및 생산성을 유지 하는 데 충분 한 용량이 Azure AD Connect 확인 합니다. 크고 복잡 한 조직 (10만 개 이상의 개체를 프로 비전 하는 조직)은 [권장 사항을](../../active-directory/hybrid/whatis-hybrid-identity.md) 따라야 Azure AD Connect 구현을 최적화할 수 있습니다.

**모범 사례**: 기존 Active Directory 인스턴스에서 높은 권한이 있는 Azure AD와 계정을 동기화 하지 않습니다.
**세부 정보**: 이러한 계정을 필터링 하는 기본 [Azure AD Connect 구성을](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) 변경 하지 마세요. 이 구성은 악의적 사용자에서 온-프레미스 자산으로의 피벗 (주요 인시던트를 만들 수 있음)의 위험을 완화 합니다.

**모범 사례**: 암호 해시 동기화 설정  
**세부 정보**: 암호 해시 동기화는 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 사용자 암호 해시를 동기화 하는 데 사용 되는 기능입니다. 이 동기화는 이전 공격 으로부터 재생 되는 누출 자격 증명을 보호 하는 데 도움이 됩니다.

AD FS(Active Directory Federation Service) 또는 다른 ID 공급자에서 페더레이션을 사용하도록 결정한 경우에도 온-프레미스 서버가 실패하거나 일시적으로 사용할 수 없게 되므로 필요에 따라 암호 해시 동기화를 백업으로 설정할 수 있습니다. 이 동기화를 사용 하면 사용자가 온-프레미스 Active Directory 인스턴스에 로그인 하는 데 사용 하는 것과 동일한 암호를 사용 하 여 서비스에 로그인 할 수 있습니다. 또한 사용자가 Azure AD에 연결 되지 않은 다른 서비스에서 동일한 메일 주소와 암호를 사용 하는 경우 동기화 된 암호 해시를 손상 된 것으로 알려진 암호와 비교 하 여 Id 보호가 손상 된 자격 증명을 검색할 수 있습니다.

자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)을 참조하세요.

**모범 사례**: 새 응용 프로그램 개발의 경우 인증에 Azure AD를 사용 합니다.
**세부 정보**: 인증을 지 원하는 데 올바른 기능을 사용 합니다.

  - 직원을 위한 Azure AD
  - 게스트 사용자 및 외부 파트너를 위한 [AZURE AD B2B](../../active-directory/b2b/index.yml)
  - 응용 프로그램을 사용할 때 고객이 자신의 프로필을 등록, 로그인 및 관리 하는 방법을 제어 [Azure AD B2C](../../active-directory-b2c/index.yml)

클라우드 ID와 해당 온-프레미스 ID를 통합하지 않는 조직은 계정을 관리하는 데 추가 오버헤드가 발생할 수 있습니다. 이 오버헤드는 실수 및 보안 위반의 가능성을 증가시킵니다.

> [!Note]
> 중요 한 계정이 상주할 디렉터리와 사용 되는 관리자 워크스테이션이 새 클라우드 서비스 또는 기존 프로세스에서 관리 되는지 여부를 선택 해야 합니다. 기존 관리 및 id 프로 비전 프로세스를 사용 하면 일부 위험을 줄일 수 있지만 공격자가 온-프레미스 계정을 손상 하 고 클라우드로 피벗 하는 위험을 만들 수도 있습니다. 다른 역할에 대해 다른 전략을 사용 하는 것이 좋습니다 (예: IT 관리자 및 비즈니스 단위 관리자). 다음 두 가지 옵션을 사용할 수 있습니다. 첫 번째 옵션은 온-프레미스 Active Directory 인스턴스와 동기화 되지 않은 Azure AD 계정을 만드는 것입니다. 관리자 워크스테이션을 Azure AD에 조인 합니다 .이는 Microsoft Intune을 사용 하 여 관리 하 고 패치할 수 있습니다. 두 번째 옵션은 온-프레미스 Active Directory 인스턴스와 동기화 하 여 기존 관리자 계정을 사용 하는 것입니다. 관리 및 보안을 위해 Active Directory 도메인에서 기존 워크스테이션을 사용 합니다.

## <a name="manage-connected-tenants"></a>연결 된 테넌트 관리
보안 조직에서는 위험을 평가 하 고 조직의 정책 및 규정 요구 사항을 준수 하는지 여부를 확인 하기 위한 가시성을 요구 합니다. [Azure express](../../expressroute/expressroute-introduction.md) 경로 또는 [사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)을 통해 프로덕션 환경 및 네트워크에 연결 된 모든 구독을 보안 조직에서 볼 수 있는지 확인 해야 합니다. Azure AD의 [전역 관리자/회사 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) 는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할에 대 한 액세스 권한을 상승 시키고 사용자 환경에 연결 된 모든 구독 및 관리 그룹을 볼 수 있습니다.

사용자 및 보안 그룹이 사용자 환경에 연결 된 모든 구독 또는 관리 그룹을 볼 수 있도록 [액세스 권한 상승을 참조 하 여 모든 Azure 구독 및 관리 그룹을 관리](../../role-based-access-control/elevate-access-global-admin.md) 합니다. 위험을 평가한 후이 상승 된 액세스 권한을 제거 해야 합니다.

## <a name="enable-single-sign-on"></a>Single Sign-On 사용

사용자가 어디서나 언제나 생산성을 높일 수 있도록 모바일 우선, 클라우드 우선 환경에서 디바이스, 앱 및 서비스에 SSO(Single Sign-On)를 사용할 수 있도록 합니다. 관리할 ID 솔루션이 여러 개인 경우 이는 IT뿐만 아니라 여러 암호를 기억해야 하는 사용자에게도 관리 문제가 될 수 있습니다.

모든 앱 및 리소스에 동일한 ID 솔루션을 사용하여 SSO를 설정할 수 있습니다. 사용자가 리소스가 온-프레미스 또는 클라우드에 있는지와 관계 없이 동일한 자격 증명 집합을 사용하여 로그인하고 필요한 리소스에 액세스할 수 있습니다.

**모범 사례**: SSO 사용하도록 설정  
**세부 정보**: Azure AD는 [온-프레미스 Active Directory](/azure/active-directory/connect/active-directory-aadconnect)를 클라우드로 확장합니다. 사용자가 작업을 수행하는 데 필요한 도메인에 조인된 디바이스, 회사 리소스 및 모든 웹 및 SaaS 애플리케이션에 대해 해당하는 기본 회사 또는 학교 계정을 사용할 수 있습니다. 사용자는 여러 사용자 이름과 암호들을 기억할 필요가 없고, 사용자의 애플리케이션 액세스는 조직 그룹 구성원 혹은 구성원의 상태에 따라 자동으로 프로비전되거나 프로비전이 해제될 수 있습니다. 그리고 [Azure AD Application Proxy](/azure/active-directory/active-directory-application-proxy-get-started)를 통해 개발 및 게시된 사용자 고유의 온-프레미스 앱이나 갤러리 앱에 대한 액세스를 제어할 수 있습니다.

SSO를 사용하여 사용자가 Azure AD에서 회사 또는 학교 계정을 기반으로 해당 [SaaS 애플리케이션](/azure/active-directory/active-directory-appssoaccess-whatis)에 액세스할 수 있습니다. 이 방법은 Microsoft SaaS 앱뿐만 아니라 [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) 및 [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial) 등 다른 앱에도 적용할 수 있습니다. Azure AD를 [SAML 기반 ID](/azure/active-directory/fundamentals-identity) 공급자로 사용하도록 애플리케이션을 구성할 수 있습니다. 보안 컨트롤인 Azure AD는 Azure AD를 통해 액세스 권한을 부여한 경우가 아니면 사용자가 애플리케이션에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 직접적으로 또는 사용자가 멤버인 그룹을 통해 액세스를 부여할 수 있습니다.

사용자 및 애플리케이션에 대해 SSO를 설정하는 일반 ID를 만들지 않는 조직은 사용자에게 여러 개의 암호가 있는 시나리오에 더 많이 노출됩니다. 이러한 시나리오에서는 사용자가 암호를 다시 사용하거나 취약한 암호를 사용할 가능성이 높아집니다.

## <a name="turn-on-conditional-access"></a>조건부 액세스 설정

사용자가 다양한 디바이스와 앱을 사용하여 어디서나 조직의 리소스에 액세스할 수 있습니다. IT 관리자는 이러한 장치가 보안 및 규정 준수에 대 한 표준을 충족 하는지 확인 하려고 합니다. 리소스에 액세스할 수 있는 사용자에게만 초점을 맞추는 것은 더 이상 충분하지 않습니다.

보안과 생산성을 분산 하기 위해 액세스 제어에 대 한 결정을 내리기 전에 리소스에 액세스 하는 방법을 고려해 야 합니다. Azure AD 조건부 액세스를 사용 하 여이 요구 사항을 해결할 수 있습니다. 조건부 액세스를 사용 하면 클라우드 앱에 액세스 하는 조건을 기반으로 자동화 된 액세스 제어 결정을 내릴 수 있습니다.

**모범 사례**: 회사 리소스에 대한 액세스 관리 및 제어  
**세부 정보**: SaaS 앱 및 azure ad 연결 앱에 대 한 그룹, 위치 및 응용 프로그램 민감도에 따라 Azure Ad [조건부 액세스](/azure/active-directory/active-directory-conditional-access-azure-portal) 를 구성 합니다.

**모범 사례**: 레거시 인증 프로토콜 차단
**세부 정보**: 공격자는 매일 이전 프로토콜의 취약점을 악용 합니다. 특히 암호 스프레이 공격을 위해 공격 합니다. 레거시 프로토콜을 차단 하도록 조건부 액세스를 구성 합니다. 자세한 내용은 [AZURE AD: Do 및 일과](https://www.youtube.com/watch?v=wGk0J4z90GI) 비디오를 참조 하세요.

## <a name="plan-for-routine-security-improvements"></a>정기 보안 강화 계획

보안은 항상 진화 하 고 있으며 환경을 보호 하는 새로운 방법을 검색 하는 방법으로 클라우드 및 id 관리 프레임 워크를 구축 하는 것이 중요 합니다.

Id 보안 점수는 Microsoft에서 게시 하는 권장 보안 컨트롤의 집합으로, 보안 상태를 객관적으로 측정 하 고 향후 보안 기능을 계획 하는 데 도움이 되는 숫자 점수를 제공 합니다. 다른 산업의 점수와 시간에 따른 고유한 추세를 비교 하 여 점수를 볼 수도 있습니다.

**모범**사례: 업계의 모범 사례에 따라 일상적인 보안 검토 및 개선 사항을 계획 합니다.
**세부 정보**: Id 보안 점수 기능을 사용 하 여 시간에 따라 향상 된 기능을 순위를 설정 합니다.

## <a name="enable-password-management"></a>암호 관리 사용

테넌트가 여럿이거나 사용자가 [자신의 암호를 재설정](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)할 수 있도록 하려는 경우 남용을 방지하기 위해 적절한 보안을 사용해야 합니다.

**모범 사례**: 사용자에 대해 SSPR(셀프 서비스 암호 재설정) 설정  
**세부 정보**: Azure AD [셀프 서비스 암호 재설정](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) 기능을 사용합니다.

**모범 사례**: SSPR을 실제로 사용하는 방법 또는 그러한 경우 모니터링  
**세부 정보**: Azure AD [암호 재설정 등록 작업 보고서](/azure/active-directory/active-directory-passwords-get-insights)를 사용하여 등록하는 사용자를 모니터링합니다. Azure AD에서 제공하는 보고 기능은 미리 작성된 보고서를 사용하여 질문에 대답하도록 도와줍니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다.

**모범 사례**: 온-프레미스 인프라로 클라우드 기반 암호 정책을 확장 합니다.
**세부 정보**: 클라우드 기반 암호 변경을 수행 하는 것과 동일한 방식으로 온-프레미스 암호 변경에 대 한 동일한 검사를 수행 하 여 조직의 암호 정책을 향상 시킵니다. 온-프레미스에서 Windows Server Active Directory 에이전트에 대 한 [AZURE AD 암호 보호](/azure/active-directory/authentication/concept-password-ban-bad) 를 설치 하 여 차단 되지 않은 암호 목록을 기존 인프라로 확장 합니다. 온-프레미스에서 암호를 변경, 설정 또는 다시 설정 하는 사용자 및 관리자는 클라우드 전용 사용자와 동일한 암호 정책을 준수 해야 합니다.

## <a name="enforce-multi-factor-verification-for-users"></a>사용자에 대한 다단계 인증 적용

모든 사용자에 대해 2단계 인증을 요구하는 것이 좋습니다. 해당 계정이 노출될 경우 상당한 영향을 미칠 수 있는 조직의 관리자 및 다른 사용자가 포함됩니다(예: 재무 책임자).

2단계 인증을 요구하는 여러 옵션이 있습니다. 최상의 옵션은 목표, 실행하는 Azure AD 버전 및 라이선스 프로그램에 따라 달라집니다. [사용자에 대해 2단계 인증을 요구하는 방법](/azure/active-directory/authentication/howto-mfa-userstates)을 참조하여 최상의 옵션을 결정합니다. 라이선스 및 가격 책정에 대한 자세한 내용은 [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) 및 [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) 가격 책정 페이지를 참조하세요.

2단계 인증을 사용하도록 설정하는 옵션 및 혜택은 다음과 같습니다.

**옵션 1**: Azure AD 보안 기본값 **혜택**을 사용 하 여 모든 사용자 및 로그인 메서드에 대해 mfa를 사용 하도록 설정:이 옵션을 사용 하면 엄격한 정책을 통해 사용자 환경의 모든 사용자에 대해 mfa를 쉽고 빠르게 적용할 수 있습니다.

* 챌린지 관리 계정 및 관리자 로그온 메커니즘
* 모든 사용자에 대해 Microsoft Authenticator를 통해 MFA 챌린지 필요
* 레거시 인증 프로토콜을 제한 합니다.

이 방법은 모든 라이선스 계층에 사용할 수 있지만 기존 조건부 액세스 정책과는 혼합할 수 없습니다. Azure AD 보안 기본값에서 자세한 정보를 찾을 수 있습니다.

**옵션 2**: [사용자 상태를 변경 하 여 Multi-Factor Authentication를 사용 하도록 설정](../../active-directory/authentication/howto-mfa-userstates.md)합니다.   
**혜택**: 2단계 인증을 요구하는 기존 방법입니다. 이 기능은 [클라우드의 Azure Multi-Factor Authentication 및 Azure Multi-Factor Authentication 서버](/azure/active-directory/authentication/concept-mfa-whichversion)에서 작동합니다. 이 방법을 사용 하려면 사용자가 로그인 할 때마다 2 단계 인증을 수행 하 고 조건부 액세스 정책을 재정의 해야 합니다.

Multi-Factor Authentication를 사용 하도록 설정 해야 하는 위치를 확인 하려면 [조직에 적합 한 AZURE MFA 버전](/azure/active-directory/authentication/concept-mfa-whichversion)을 확인 하세요.

**옵션 3**: [조건부 액세스 정책을 사용 하 여 Multi-Factor Authentication 사용](/azure/active-directory/authentication/howto-mfa-getstarted)
**혜택**:이 옵션을 사용 하면 [조건부 액세스](/azure/active-directory/active-directory-conditional-access-azure-portal)를 사용 하 여 특정 조건에서 2 단계 인증을 요청할 수 있습니다. 특정 조건이란 위험한 것으로 간주하는 다른 위치, 신뢰할 수 없는 디바이스 또는 애플리케이션에서 사용자 로그인이 될 수 있습니다. 2단계 인증이 필요한 특정 조건을 정의하면 번거로운 사용자 환경일 수 있는 지속적인 메시지를 사용자에게 표시하지 않도록 할 수 있습니다.

이것이 사용자에게 2단계 인증을 사용하도록 설정하는 가장 유연한 방법입니다. 조건부 액세스 정책을 사용 하도록 설정 하는 것은 클라우드의 Azure Multi-Factor Authentication에만 적용 되며 Azure AD의 프리미엄 기능입니다. [클라우드 기반 Azure Multi-factor Authentication 배포](/azure/active-directory/authentication/howto-mfa-getstarted)에서 이 방법에 대한 자세한 내용을 확인할 수 있습니다.

**옵션 4**: [Azure AD ID 보호](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa)의 사용자 및 로그인 위험을 평가 하 여 조건부 액세스 정책을 통해 Multi-Factor Authentication을 사용 하도록 설정 합니다.   
**혜택**: 이 옵션을 사용하면 다음을 수행할 수 있습니다.

* 조직의 ID에 영향을 미치는 잠재적인 취약점 검색
* 조직 ID와 관련된 검색된 의심스러운 작업에 대한 자동화된 응답 구성
* 의심스러운 인시던트 조사 및 해결할 적절한 작업 수행

이 방법은 Azure AD Identity Protection 위험 평가를 사용하여 모든 클라우드 애플리케이션에 대한 사용자 및 로그인 위험에 따라 2단계 인증이 필요한지 결정합니다. 이 방법에는 Azure Active Directory P2 라이선스가 필요합니다. [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview)에서 이 방법에 대한 자세한 내용을 찾을 수 있습니다.

> [!Note]
> 옵션 1, 사용자 상태를 변경 하 여 Multi-Factor Authentication를 사용 하도록 설정 하는 것은 조건부 액세스 정책을 무시 합니다. 옵션 2와 3은 조건부 액세스 정책을 사용 하므로 옵션 1을 사용할 수 없습니다.

2단계 인증과 같은 추가적인 ID 보호 계층을 추가하지 않는 조직은 자격 증명 도난 공격에 취약합니다. 자격 증명 도난 공격으로 인해 데이터 손상이 발생할 수 있습니다.

## <a name="use-role-based-access-control"></a>역할 기반 액세스 제어 사용

클라우드 리소스에 대 한 액세스 관리는 클라우드를 사용 하는 모든 조직에 중요 합니다. [RBAC (역할 기반 액세스 제어)](/azure/role-based-access-control/overview)를 통해 Azure 리소스에 대 한 액세스 권한이 있는 사용자, 해당 리소스에서 수행할 수 있는 작업 및 해당 리소스에 액세스할 수 있는 영역을 관리할 수 있습니다.

Azure의 특정 기능을 담당 하는 그룹이 나 개별 역할을 지정 하면 보안상 위험할 수 있는 사용자 및 자동화 오류가 발생할 수 있는 혼동을 피할 수 있습니다. [알아야 할 사항](https://en.wikipedia.org/wiki/Need_to_know) 및 [최소 권한](https://en.wikipedia.org/wiki/Principle_of_least_privilege) 보안 원칙을 기반으로 액세스를 제한하는 것은 데이터 액세스에 보안 정책을 적용하고자 하는 조직의 경우 필수입니다.

보안 팀은 위험을 평가 하 고 치료 하기 위해 Azure 리소스를 볼 수 있어야 합니다. 보안 팀에 운영 책임이 있는 경우 해당 작업을 수행 하려면 추가 권한이 필요 합니다.

[RBAC](/azure/role-based-access-control/overview) 를 사용 하 여 특정 범위에서 사용자, 그룹 및 응용 프로그램에 사용 권한을 할당할 수 있습니다. 역할 할당의 범위는 구독, 리소스 그룹 또는 단일 리소스일 수 있습니다.

**모범 사례**: 팀 내에서 업무를 분리 하 고 사용자에 게 해당 작업을 수행 하는 데 필요한 만큼의 권한만 부여 합니다. Azure 구독 또는 리소스에서 모든 사람에 게 무제한 권한을 부여 하는 대신 특정 범위의 특정 작업만 허용 합니다.
**세부 정보**: Azure에서 [기본 제공 RBAC 역할](/azure/role-based-access-control/built-in-roles) 을 사용 하 여 사용자에 게 권한을 할당 합니다.

> [!Note]
> 특정 사용 권한은 불필요 한 복잡성 및 혼동을 만들기 때문에 문제를 해결할 필요 없이 수정 하기 어려운 "레거시" 구성에 누적 됩니다. 리소스별 사용 권한을 사용하지 않습니다. 대신 관리 그룹을 사용 하 여 구독 내의 사용 권한에 대 한 엔터프라이즈 수준의 사용 권한 및 리소스 그룹을 사용 합니다. 사용자 특정 권한을 사용 하지 않습니다. 대신, Azure AD의 그룹에 액세스 권한을 할당 합니다.

**모범 사례**: 보안 팀에 azure 책임에 대 한 액세스 권한을 부여 하 여 위험을 평가 하 고 수정할 수 있도록 azure 리소스를 확인 합니다.
**세부 정보**: 보안 팀에 RBAC [보안 읽기 권한자](/azure/role-based-access-control/built-in-roles#security-reader) 역할을 부여 합니다. 루트 관리 그룹 또는 세그먼트 관리 그룹은 책임의 범위에 따라 다음과 같이 사용할 수 있습니다.

* 모든 엔터프라이즈 리소스를 담당 하는 팀의 **루트 관리 그룹**
* 범위가 제한 된 팀에 대 한 **세그먼트 관리 그룹** (일반적으로 규정 또는 기타 조직 경계로 인해)

**모범 사례**: 직접 운영 책임이 있는 보안 팀에 적절 한 사용 권한을 부여 합니다.
**세부 정보**: 적절 한 역할 할당에 대 한 RBAC 기본 제공 역할을 검토 합니다. 기본 제공 역할이 조직의 특정 요구를 충족 하지 않는 경우 [Azure 리소스에 대 한 사용자 지정 역할](/azure/role-based-access-control/custom-roles)을 만들 수 있습니다. 기본 제공 역할과 마찬가지로, 구독, 리소스 그룹 및 리소스 범위에서 사용자, 그룹 및 서비스 사용자에 게 사용자 지정 역할을 할당할 수 있습니다.

**모범 사례**: 필요한 보안 역할에 대 한 Azure Security Center 액세스 권한을 부여 합니다. Security Center를 사용 하면 보안 팀에서 위험을 신속 하 게 식별 하 고 수정할 수 있습니다.
**세부 정보**: 이러한 요구 사항이 포함 된 보안 팀을 RBAC [보안 관리자](/azure/role-based-access-control/built-in-roles#security-admin) 역할에 추가 하 여 보안 정책을 보고, 보안 상태를 보고, 보안 정책을 편집 하 고, 경고 및 권장 사항을 확인 하 고, 경고 및 권장 사항을 해제할 수 있습니다. 책임의 범위에 따라 루트 관리 그룹 또는 세그먼트 관리 그룹을 사용 하 여이 작업을 수행할 수 있습니다.

RBAC와 같은 기능을 사용 하 여 데이터 액세스 제어를 적용 하지 않는 조직은 사용자에 게 필요한 것 보다 많은 권한을 부여할 수 있습니다. 이로 인해 사용자가 보유 하지 않은 데이터 형식 (예: 높은 비즈니스 영향)에 액세스할 수 있으므로 데이터가 손상 될 수 있습니다.

## <a name="lower-exposure-of-privileged-accounts"></a>권한 있는 계정의 낮은 노출

권한 있는 액세스 보안은 비즈니스 자산 보호를 위해 중요한 첫 번째 단계입니다. 보안 정보 또는 리소스에 액세스할 수 있는 사용자의 수를 최소화하면 악의적인 사용자가 해당 액세스 권한을 얻거나 권한이 있는 사용자가 실수로 중요한 리소스에 영향을 줄 가능성이 감소합니다.

권한 있는 계정은 IT 시스템을 운영하고 관리하는 계정입니다. 사이버 공격자는 조직의 데이터와 시스템에 대한 액세스 권한을 얻기 위해 이러한 계정을 대상으로 지정합니다. 권한 있는 액세스를 보호하려면 계정과 시스템을 악의적 사용자에게 노출될 위험으로부터 격리해야 합니다.

사이버 공격자로부터 권한 있는 액세스를 보호하기 위한 로드맵을 개발하고 따르는 것이 좋습니다. Azure AD, Microsoft Azure, Office 365 및 기타 클라우드 서비스에서 관리되거나 보고되는 보안 ID 및 액세스에 대한 자세한 로드맵을 만드는 방법에 대한 자세한 내용은 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)을 검토하세요.

다음에서는 [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)에 있는 모범 사례를 요약합니다.

**모범 사례**: 권한 있는 계정에 대한 액세스 관리, 제어 및 모니터링   
**세부 정보**: [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access)를 설정합니다. Privileged Identity Management가 설정되면 권한 있는 액세스 역할 변경에 대한 알림 이메일 메시지를 받게 됩니다. 이러한 알림에서는 디렉터리에서 권한이 높은 역할에 추가 사용자가 추가될 때 조기 경고를 제공합니다.

**모범 사례**: 모든 중요 한 관리자 계정이 관리 되는 Azure AD 계정 인지 확인 합니다.
**세부 정보**: 중요 한 관리자 역할 (예: hotmail.com, live.com, outlook.com 등의 Microsoft 계정)에서 모든 소비자 계정을 제거 합니다.

**모범 사례**: 관리 권한이 손상 되는 피싱 및 기타 공격을 방지 하기 위해 모든 중요 관리자 역할에 관리 작업을 위한 별도의 계정이 있는지 확인 합니다.
**세부 정보**: 관리 작업을 수행 하는 데 필요한 권한이 할당 된 별도의 관리자 계정을 만듭니다. Microsoft Office 365 전자 메일 또는 임의 웹 검색과 같은 일상적인 생산성 도구에 대해 이러한 관리 계정을 사용 하지 못하도록 차단 합니다.

**모범 사례**: 권한이 높은 역할이 있는 계정 식별 및 분류   
**세부 정보**: Azure AD Privileged Identity Management를 설정하면 글로벌 관리자, 권한 있는 역할 관리자 및 기타 권한이 높은 관리자에 속한 사용자를 확인합니다. 이러한 역할에 더 이상 필요하지 않은 계정을 제거하고 관리자 역할에 할당된 나머지 계정은 다음과 같이 분류합니다.

* 관리 사용자에게 개별적으로 할당되고, 비관리 용도(예: 개인 이메일)로 사용될 수도 있습니다.
* 관리 사용자에게 개별적으로 할당되고, 관리 용도로만 지정됩니다.
* 여러 사용자 간에 공유됩니다.
* 응급 액세스 시나리오용
* 자동화된 스크립트용
* 외부 사용자용

**모범 사례**: "JIT(Just-In-Time)" 액세스 권한을 구현하여 권한의 노출 시간을 줄이고 권한 있는 계정의 사용에 대한 가시성 증가   
**세부 정보**: Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

* 사용자가 해당 권한 JIT에서만 사용하도록 제한합니다.
* 권한을 자동으로 해지하는 짧은 기간 동안 자신 있게 역할을 할당합니다.

**모범 사례**: 두 개 이상의 응급 액세스 계정 정의   
**세부 정보**: 응급 액세스 계정을 사용하면 조직이 기존 Azure Active Directory 환경 내에서 권한 있는 액세스를 제한할 수 있습니다. 이러한 계정은 높은 권한을 부여받으며 특정 개인에게 할당되지 않습니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 시나리오로 제한됩니다. 조직에서는 응급 계정의 사용량을 필요한 기간으로만 제한해야 합니다.

전역 관리자 역할에 할당되었거나 적합한 계정을 평가합니다. `*.onmicrosoft.com` 도메인을 사용하여 클라우드 전용 계정(응급 액세스용)이 표시되지 않으면 해당 계정을 만듭니다. 자세한 내용은 [Azure AD에서 응급 액세스 관리 계정의 관리](/azure/active-directory/users-groups-roles/directory-emergency-access)를 참조하세요.

**모범**사례: 비상 시 "고장" 프로세스를 준비 해야 합니다.
**세부 정보**: [Azure AD에서 하이브리드 및 클라우드 배포에 대 한 권한 있는 액세스 보안](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)의 단계를 따르세요.

**모범 사례**: 모든 중요 한 관리자 계정이 암호 없는 (기본 설정) 또는 Multi-Factor Authentication 요구 해야 합니다.
**세부 정보**: [Microsoft Authenticator 앱](/azure/active-directory/authentication/howto-authentication-phone-sign-in) 을 사용 하 여 암호를 사용 하지 않고 Azure AD 계정에 로그인 합니다. [비즈니스용 Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification)와 마찬가지로 Microsoft Authenticator는 키 기반 인증을 사용 하 여 장치에 연결 된 사용자 자격 증명을 사용 하도록 설정 하 고 생체 인식 인증 또는 PIN을 사용 합니다.

하나 이상의 Azure AD 관리자 역할 (전역 관리자, 권한 있는 역할 관리자, Exchange Online 관리자 및 SharePoint Online)에 영구적으로 할당 된 모든 개별 사용자에 대해 Azure Multi-Factor Authentication 로그인이 필요 합니다. 관리자나. [관리자 계정에 대해 Multi-Factor Authentication](/azure/active-directory/authentication/howto-mfa-userstates) 를 사용 하도록 설정 하 고 관리자 계정 사용자가 등록 했는지 확인 합니다.

**모범 사례**: 중요 한 관리자 계정의 경우 프로덕션 작업이 허용 되지 않는 관리자 워크스테이션 (예: 검색 및 전자 메일)을 포함 합니다. 이렇게 하면 검색 및 전자 메일을 사용 하는 공격 벡터에서 관리자 계정을 보호 하 고 중요 한 인시던트의 위험을 크게 낮춥니다.
**세부 정보**: 관리 워크스테이션을 사용 합니다. 워크스테이션 보안 수준을 선택 합니다.

- 매우 안전한 생산성 장치는 검색 및 기타 생산성 작업을 위한 고급 보안을 제공 합니다.
- [Paw (권한 있는 액세스 워크스테이션)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) 는 중요 한 작업에 대 한 인터넷 공격 및 위협 벡터 로부터 보호 되는 전용 운영 체제를 제공 합니다.

**모범 사례**: 직원이 조직을 떠날 때 관리자 계정 프로 비전 해제
**세부 정보**: 직원이 조직을 떠날 때 관리자 계정을 사용 하지 않도록 설정 하거나 삭제 하는 프로세스를 준비 합니다.

**모범 사례**: 최신 공격 기법을 사용 하 여 관리자 계정을 정기적으로 테스트 합니다.
**세부 정보**: Office 365 공격 시뮬레이터 또는 타사 제품을 사용 하 여 조직에서 현실적인 공격 시나리오를 실행 합니다. 이를 통해 실제 공격이 발생 하기 전에 취약 한 사용자를 찾을 수 있습니다.

**모범 사례**: 가장 자주 공격 받은 기술을 완화하는 단계 수행  
**세부 정보**: [직장 또는 학교 계정으로 전환해야 하는 관리 역할의 Microsoft 계정을 식별합니다.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[글로벌 관리자 계정에 대해 별도의 사용자 계정 및 메일 전달 보장](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[관리 계정의 암호가 최근에 변경되었는지 확인](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[암호 해시 동기화 설정](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[모든 권한이 있는 역할의 사용자 및 노출된 사용자에 대해 Multi-Factor Authentication 요구](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Office 365 보안 점수 가져오기(Office 365를 사용하는 경우)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Office 365 보안 및 규정 준수 지침 검토(Office 365를 사용하는 경우)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Office 365 작업 모니터링 구성(Office 365를 사용하는 경우)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[인시던트/비상 대응 계획 소유자 설정](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[권한 있는 온-프레미스 관리 계정 보호](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

권한 있는 액세스를 보호하지 않는 경우 권한이 높은 역할에 너무 많은 사용자가 포함되면 공격에 더 취약할 수 있습니다. 사이버 공격자를 포함한 악의적인 행위자는 자격 증명 도난을 사용하여 중요한 데이터 및 시스템에 대해 액세스하는 권한 있는 액세스의 관리자 계정 및 기타 요소를 대상으로 지정하는 경우가 많습니다.

## <a name="control-locations-where-resources-are-created"></a>리소스를 만든 위치 제어

클라우드 운영자가 조직의 리소스를 관리하는 데 필요한 규칙을 위반하지 않으면서도 작업을 수행할 수 있도록 하는 것이 아주 중요합니다. 리소스가 만들어지는 위치를 제어하고자 하는 조직은 이러한 위치를 하드 코딩해야 합니다.

[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)를 사용하여 명시적으로 거부된 작업 또는 리소스를 설명하는 정의가 포함된 보안 정책을 만들 수 있습니다. 구독, 리소스 그룹 또는 개별 리소스와 같이 원하는 범위에서 해당 정책 정의를 할당합니다.

> [!NOTE]
> 보안 정책은 RBAC와 동일하지 않습니다. 실제로 RBAC를 사용하여 해당 리소스를 만드는 사용자에게 권한을 부여합니다.
>
>

리소스를 만드는 방법을제어하지 않는 조직은 사용자가 필요 이상으로 많은 리소스를 만들어 서비스를 남용할 수 있는 상황에 직면할 가능성이 더 높습니다. 리소스 만들기 프로세스의 강화는 다중 테넌트 시나리오를 보호하기 위한 중요한 단계입니다.

## <a name="actively-monitor-for-suspicious-activities"></a>의심스러운 활동을 적극적으로 모니터링

활성 ID 모니터링 시스템은 의심스러운 동작을 신속하게 검색하고 추가로 조사하기 위해 경고를 트리거할 수 있습니다. 다음 표에서는 조직이 해당 ID를 모니터링할 수 있도록 하는 두 개의 Azure AD 기능을 보여줍니다.

**모범 사례**: 식별할 방법 보유

- [추적되지 않고](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources) 로그인하려고 시도합니다.
- [무차별 암호 대입 공격](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures)은 특정 계정에 대한 공격입니다.
- 여러 위치에서 로그인을 시도합니다.
- [감염된 디바이스](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices)에서 로그인합니다.
- 의심스러운 IP 주소

**세부 정보**: Azure AD Premium [비정상 보고서](/azure/active-directory/active-directory-view-access-usage-reports)를 사용합니다. IT 관리자가 매일 또는 필요 시(대개 사고 대응 시나리오에서) 이러한 보고서를 실행하도록 하는 프로세스 및 절차를 시행합니다.

**모범 사례**: 위험을 알려주고 위험 수준(높음, 중간 또는 낮음)을 비즈니스 요구 사항에 맞게 조정할 수 있는 활성 모니터링 시스템   
**세부 정보**: [Azure AD ID Protection](/azure/active-directory/active-directory-identityprotection)을 사용하여 고유한 대시보드에서 현재 위험의 플래그를 지정하고 이메일을 통해 일일 요약 알림을 보냅니다. 조직의 ID를 보호하려면 지정된 위험 수준에 도달했을 때 검색된 문제에 자동으로 대응하는 위험 기반 정책을 구성할 수 있습니다.

해당 ID 시스템을 적극적으로 모니터링하지 않는 조직은 사용자 자격 증명이 손상될 위험에 직면합니다. 이러한 자격 증명을 통해 의심스러운 활동이 일어나고 있다는 것을 알아야 이 형식의 위협을 완화시킬 수 있습니다.

## <a name="use-azure-ad-for-storage-authentication"></a>저장소 인증에 Azure AD 사용
[Azure Storage](/azure/storage/common/storage-auth-aad) 는 Blob Storage 및 Queue storage에 대해 Azure AD를 사용 하 여 인증 및 권한 부여를 지원 합니다. Azure AD 인증을 사용 하면 Azure 역할 기반 액세스 제어를 사용 하 여 사용자, 그룹 및 응용 프로그램에 대 한 특정 권한을 개별 blob 컨테이너 또는 큐의 범위에 부여할 수 있습니다.

[저장소에 대 한 액세스를 인증](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)하는 데 Azure AD를 사용 하는 것이 좋습니다.

## <a name="next-step"></a>다음 단계

[Azure 보안 모범 사례 및 패턴](best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.
