---
title: Azure Active Directory Single Sign-On 배포 계획
description: 조직에서 SSO를 계획, 배포 및 관리 하는 데 도움이 되는 가이드입니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e8aa5515bf8ae8e842a98656167f4198ac652981
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642387"
---
# <a name="plan-a-single-sign-on-deployment"></a>Single Sign-On 배포 계획

SSO (single sign-on)는 단일 사용자 계정을 사용 하 여 한 번만 로그인 함으로써 사용자가 필요로 하는 모든 응용 프로그램 및 리소스에 액세스 하는 것을 의미 합니다. SSO를 사용 하면 사용자는 두 번째 인증 없이 필요한 모든 응용 프로그램에 액세스할 수 있습니다.

## <a name="benefits-of-sso"></a>SSO의 이점

SSO (Single sign-on)는 사용자가 Azure Active Directory (Azure AD)에서 응용 프로그램에 로그인 할 때 보안 및 편리 함을 추가 합니다. 

대부분의 조직에서는 최종 사용자 생산성을 위해 Office 365, Box, Salesforce 등의 SaaS (software as a service) 응용 프로그램을 사용 합니다. 지금까지 IT 담당자는 각 SaaS 응용 프로그램에서 사용자 계정을 개별적으로 만들고 업데이트 해야 했으며, 사용자는 각 SaaS 응용 프로그램에 대 한 암호를 기억할 필요가 있습니다.

Azure Marketplace에는 미리 통합 된 SSO 연결을 사용 하는 3000 이상의 응용 프로그램이 있으므로 테 넌 트에 쉽게 통합할 수 있습니다.

## <a name="licensing"></a>라이선스

- **AZURE AD 라이선스** -미리 통합 된 SaaS 응용 프로그램에 대 한 SSO는 무료입니다. 그러나 디렉터리의 개체 수와 배포 하려는 기능에는 추가 라이선스가 필요할 수 있습니다. 라이선스 요구 사항에 대 한 전체 목록은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조 하세요.
- **응용 프로그램 라이선스** -비즈니스 요구 사항에 맞게 SaaS 응용 프로그램에 대 한 적절 한 라이선스가 필요 합니다. 응용 프로그램 소유자와 협력 하 여 응용 프로그램에 할당 된 사용자에 게 응용 프로그램 내에서 해당 역할에 대 한 적절 한 라이선스가 있는지 확인 합니다. Azure AD에서 역할에 따라 자동 프로 비전을 관리 하는 경우 Azure AD에서 할당 된 역할은 응용 프로그램 내에서 소유 하는 라이선스 수와 일치 해야 합니다. 응용 프로그램에서 소유 하는 라이선스 수가 잘못 되어 사용자의 프로 비전/업데이트 중에 오류가 발생할 수 있습니다.

## <a name="plan-your-sso-team"></a>SSO 팀 계획

- **올바른 관련자** 에 게 문의 하세요. 기술 프로젝트가 실패할 경우 일반적으로 영향, 결과 및 책임에 대 한 기대 수준이 일치 하지 않기 때문입니다. 이러한 문제를 방지 하려면 [올바른 관련자](https://aka.ms/deploymentplans) 와 관련자의 역할을 이해 하 고 있어야 합니다.
- **통신 계획** -통신은 모든 새 서비스의 성공에 중요 합니다. 사용자가 경험을 변경 하는 방법, 변경 시기 및 문제 발생 시 지원을 얻는 방법에 대해 사전에 사용자에 게 전달 합니다. [최종 사용자가 SSO를 사용 하는 응용 프로그램에 액세스 하는 방법](end-user-experiences.md)에 대 한 옵션을 검토 하 고 선택 항목에 맞게 통신을 만들 수 있습니다. 

## <a name="plan-your-sso-protocol"></a>SSO 프로토콜 계획

페더레이션 프로토콜을 기반으로 하는 SSO 구현은 보안, 안정성 및 최종 사용자 환경을 개선 하 고 구현 하기 쉽습니다. 많은 응용 프로그램은 Azure AD에서 단계별 [가이드](../saas-apps/tutorial-list.md)를 사용 하 여 미리 통합 되어 있습니다. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)에서 찾을 수 있습니다. 각 SSO 방법에 대 한 자세한 내용은 [Azure Active Directory의 응용 프로그램에 대 한 Single sign-on](what-is-single-sign-on.md)문서에서 찾을 수 있습니다.

사용자가 앱에 Single Sign-On 하는 데 사용할 수 있는 두 가지 기본 방법이 있습니다.

- **페더레이션된 Single Sign-On 사용** Azure AD는 Azure AD 계정을 사용 하 여 응용 프로그램에 대해 사용자를 인증 합니다. 이 메서드는 SAML 2.0, WS-FEDERATION 또는 Openid connect Connect와 같은 프로토콜을 지 원하는 응용 프로그램에 대해 지원 되며, Single Sign-On의 가장 다양 한 모드입니다. 응용 프로그램에서 지 원하는 경우 암호 기반 SSO 및 ADFS 대신 페더레이션된 SSO를 Azure AD와 함께 사용 하는 것이 좋습니다.

- **암호 기반 Single Sign-On** 사용자는 처음으로 사용자 이름과 암호를 사용 하 여 응용 프로그램에 로그인 합니다. 첫 번째 로그온 후에는 Azure AD가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 암호 기반 Single Sign-On을 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 스토리지하고 재생할 수 있습니다. 이 옵션은 응용 프로그램에서 제공 하는 기존 로그인 프로세스를 활용 하 여 관리자가 암호를 관리할 수 있도록 하 고 사용자가 암호를 알 필요가 없습니다.

### <a name="considerations-for-federation-based-sso"></a>페더레이션 기반 SSO에 대 한 고려 사항

- **Openid connect Connect 및 Oauth 사용** -연결 중인 응용 프로그램에서 지원 되는 경우 oidc/oauth 2.0 메서드를 사용 하 여 해당 응용 프로그램에 SSO를 사용 하도록 설정 합니다. 이 방법을 사용 하는 경우 더 작은 구성이 필요 하며 더 풍부한 사용자 환경을 지원 합니다. 자세한 내용은 [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [openid connect Connect 1.0](../develop/v2-protocols-oidc.md)및 [Azure Active Directory 개발자 가이드](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)를 참조 하세요.
- **Saml 기반 SSO에 대 한 끝점 구성** -saml을 사용 하는 경우 응용 프로그램을 구성 하기 전에 개발자에 게 특정 정보가 필요 합니다. 자세한 내용은 [SAML 기반 Single Sign-On 구성](configure-saml-single-sign-on.md)을 참조 하세요.
- **SAML 기반 sso에 대 한 인증서 관리** -응용 프로그램에 대해 페더레이션된 sso를 사용 하도록 설정 하면 Azure AD에서는 기본적으로 3 년 동안 유효한 인증서를 만듭니다. 필요한 경우 해당 인증서의 만료 날짜를 사용자 지정할 수 있습니다. 만료 되기 전에 인증서를 갱신 하는 프로세스가 준비 되었는지 확인 합니다. 자세히 알아보려면 [AZURE AD 인증서 관리](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)를 참조 하세요.

### <a name="considerations-for-password-based-sso"></a>암호 기반 SSO에 대 한 고려 사항

암호 기반 SSO에 Azure AD를 사용 하려면 자격 증명을 안전 하 게 검색 하 고 로그인 양식을 작성 하는 브라우저 확장을 배포 해야 합니다. [지원 되는 브라우저](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 사용 하 여 확장을 대규모로 배포 하는 메커니즘을 정의 합니다. 다음 옵션을 사용할 수 있습니다.

- [Internet Explorer에 대 한 그룹 정책](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Internet Explorer에 대 한 Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Chrome, Firefox, Microsoft Edge 또는 IE에 대 한 사용자 구동 다운로드 및 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

자세한 내용은 [암호 single sign-on을 구성 하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery)을 참조 하세요.

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>갤러리에 없는 응용 프로그램에 대 한 로그인 폼 메타 데이터 캡처

Microsoft는 암호 보관을 위해 웹 응용 프로그램에 대 한 메타 데이터 캡처를 지원 합니다 (사용자 이름 및 암호 필드 캡처). Forms 메타 데이터를 캡처하기 위해 응용 프로그램을 구성 하는 과정에서 로그인 URL로 이동 합니다. 응용 프로그램 소유자에 게 정확한 로그인 URL을 요청 합니다. 이 정보는 로그온 프로세스 중에 사용 되며, 로그온 하는 동안 Azure AD 자격 증명을 응용 프로그램에 매핑합니다.

자세히 알아보려면 [AZURE AD를 사용한 응용 프로그램 액세스 및 SSO 란? – 암호 기반 sso](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)를 참조 하세요.

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>폼의 메타 데이터를 탈환 해야 함을 나타냅니다.

응용 프로그램에서 HTML 레이아웃을 변경할 때 변경 내용을 조정 하기 위해 메타 데이터를 다시 캡처 해야 할 수도 있습니다. HTML 레이아웃에 변경 내용이 있음을 나타내는 일반적인 증상은 다음과 같습니다.

- 응용 프로그램을 클릭 하면 로그인 페이지에서 "중지 됨"을 보고 하는 사용자
- 사용자 이름 또는 암호가 채워지지 않는 사용자 보고

#### <a name="shared-accounts"></a>공유 계정

로그인 관점에서 공유 계정이 있는 응용 프로그램은 개별 사용자에 대해 암호 SSO를 사용 하는 갤러리 응용 프로그램과는 다릅니다. 그러나 공유 계정을 사용 하는 응용 프로그램을 계획 하 고 구성 하는 경우 몇 가지 추가 단계가 필요 합니다.

1. 응용 프로그램 비즈니스 사용자와 협력 하 여 다음을 문서화 합니다.
   1. 응용 프로그램을 사용 하는 조직의 사용자 집합
   1. 사용자 집합과 연결 된 응용 프로그램의 기존 자격 증명 집합 
1. 사용자 집합과 자격 증명의 각 조합에 대해 요구 사항에 따라 클라우드 또는 온-프레미스에서 보안 그룹을 만듭니다.
1. 공유 자격 증명을 다시 설정 합니다. 앱이 Azure AD에 배포 되 면 개인은 공유 계정의 암호를 필요로 하지 않습니다. Azure AD는 암호를 저장 하므로 매우 길고 복잡 하 게 설정 하는 것이 좋습니다. 
1. 응용 프로그램에서 지 원하는 경우 암호의 자동 롤오버를 구성 합니다. 이렇게 하면 초기 설치를 수행한 관리자도 공유 계정의 암호를 알 수 있습니다. 

## <a name="plan-your-authentication-method"></a>인증 방법 계획

따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 클라우드에서 사용자를 프로비저닝하기도 하는 Azure AD Connect를 사용하여 구성되는 인증 방법을 구현하세요.

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다. 특정 시나리오와 가장 일치 하는 항목을 선택 해야 합니다. 자세한 내용은 [Azure Active Directory 하이브리드 id 솔루션에 적합 한 인증 방법 선택](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)을 참조 하세요.

## <a name="plan-your-security-and-governance"></a>보안 및 거 버 넌 스 계획 

Network 경계는 점점 더 많은 ou가 되 고 BYOD 장치와 클라우드 응용 프로그램을 폭발 하는 것이 더 효율적이 지 않기 때문에 id는 보안 주의 및 투자에 대 한 새로운 기본 피벗입니다. 

### <a name="plan-access-reviews"></a>액세스 검토 계획

[액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) 를 통해 조직은 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 사용자 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 해야 합니다.

액세스 검토를 설정 하는 동안 계획할 수 있는 몇 가지 주요 항목은 다음과 같습니다.

1. 비즈니스 요구에 맞는 액세스 검토에 대 한 흐름을 식별 합니다. 이는 한 주, 매월, 매년 또는 요청 시 실행으로 빈번 하 게 사용할 수 있습니다.

1. 앱 액세스 보고서의 검토자를 나타내는 그룹을 만듭니다. 앱과 해당 대상 사용자 및 사용 사례에 가장 익숙한 이해 관계자가 액세스 검토의 참여자 인지 확인 해야 합니다.

1. 액세스 검토를 완료 하는 데는 더 이상 액세스할 필요가 없는 사용자에 대 한 앱 액세스 권한을 제공 하는 작업이 포함 됩니다. 거부 된 사용자의 잠재적 지원 요청 처리를 계획 합니다. 삭제 된 사용자는 30 일 동안 Azure AD에서 삭제 된 상태로 유지 되며, 필요한 경우 관리자가 복원할 수 있습니다. 30일 후 해당 사용자가 영구적으로 삭제됩니다. 전역 관리자는 Azure Active Directory 포털을 사용 하 여 해당 기간에 도달할 때까지 최근에 삭제 된 사용자를 명시적으로 삭제할 수 있습니다.

### <a name="plan-auditing"></a>계획 감사

Azure AD는 [기술 및 비즈니스 통찰력을 포함 하는 보고서](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/)를 제공 합니다. 

보안 및 작업 보고서를 모두 사용할 수 있습니다. 보안 보고서에는 위험 플래그가 지정 된 사용자 및 위험한 로그인이 표시 됩니다. 활동 보고서는 로그인 활동을 자세히 설명 하 고 모든 로그인의 감사 내역을 제공 하 여 조직의 사용자 동작을 이해 하는 데 도움이 됩니다. 보고서를 사용 하 여 위험을 관리 하 고 생산성을 높이고 규정 준수를 모니터링할 수 있습니다.

| 보고서 종류 | 액세스 검토 | 보안 보고서 | 로그인 보고서 |
|-------------|---------------|------------------|----------------|
| 을 검토 하는 데 사용 | 응용 프로그램 사용 권한 및 사용. | 잠재적으로 손상 된 계정 | 응용 프로그램에 액세스 하는 사용자 |
| 잠재적 작업 | 감사 액세스 권한 해지 | 액세스 취소 강제 보안 다시 설정 | 액세스 취소 |

Azure AD는 대부분의 감사 데이터를 30 일 동안 유지 하 고 Azure 관리 포털 또는 API를 통해 분석 시스템으로 다운로드 하는 데 사용할 수 있는 데이터를 만듭니다.

### <a name="consider-using-microsoft-cloud-application-security"></a>Microsoft 클라우드 응용 프로그램 보안을 사용 하는 것이 좋습니다.

MCAS (Microsoft Cloud App Security)는 CASB (Cloud Access Security Broker) 솔루션입니다. 클라우드 앱 및 서비스에 대 한 가시성을 제공 하 고, 사이버 위협 대를 식별 하 고 공격 하는 정교한 분석을 제공 하며, 데이터 이동 방식을 제어할 수 있습니다.

MCAS를 배포 하면 다음 작업을 수행할 수 있습니다.

- Cloud Discovery를 사용 하 여 조직에서 사용 중인 클라우드 환경 및 클라우드 앱을 매핑하고 식별 합니다.
- 클라우드에서 앱 권한 부여 및 사용 권한 부여
- 공급자 Api를 활용 하는 배포 하기 쉬운 앱 커넥터를 사용 하 여 연결 하는 앱의 표시 유형 및 거 버 넌 스
- 조건부 액세스 앱 제어 보호를 사용 하 여 클라우드 앱 내에서 액세스 및 활동을 실시간으로 표시 하 고 제어할 수 있습니다.
- 정책을 설정 하 고 지속적으로 미세 조정 하 여 지속적으로 제어할 수 있습니다.

MCAS (Microsoft 클라우드 응용 프로그램 보안) 세션 컨트롤은 모든 운영 체제의 모든 주요 플랫폼에 있는 모든 브라우저에서 사용할 수 있습니다. 모바일 앱과 데스크톱 앱도 차단되거나 허용될 수 있습니다. 기본적으로 Azure AD와 통합 하면 [몇 가지 주요 앱](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)을 포함 하 여 SAML 또는 azure ad의 Single Sign-On에 대 한 Open ID Connect 앱을 사용 하 여 구성 된 앱을 지원할 수 있습니다.

MCAS에 대 한 자세한 내용은 [Microsoft Cloud App Security 개요](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)를 참조 하세요. MCAS는 사용자 기반 구독 서비스입니다. [Mcas 라이선스 데이터 시트](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)에서 라이선스 정보를 검토할 수 있습니다.

### <a name="use-conditional-access"></a>조건부 액세스 사용

조건부 액세스를 사용 하 여 클라우드 앱에 대 한 기준 기반 액세스 제어 결정을 자동화할 수 있습니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 따라서 조건부 액세스는 DoS (서비스 거부) 공격과 같은 시나리오에 대 한 첫 번째 방어선은 아니며 이러한 이벤트의 신호를 사용 하 여 액세스를 확인할 수 있습니다. 예를 들어 로그인 위험 수준, 요청 위치 등을 사용할 수 있습니다. 조건부 액세스에 대 한 자세한 내용은 [개요](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) 및 [배포 계획](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access)을 참조 하세요.

## <a name="azure-sso-technical-requirements"></a>Azure SSO 기술 요구 사항

다음 섹션에서는 필요한 환경, 끝점, 클레임 매핑, 필수 특성, 인증서 및 사용 되는 프로토콜을 포함 하 여 특정 응용 프로그램을 구성 하기 위한 요구 사항에 대해 자세히 설명 합니다. [AZURE AD 포털](https://portal.azure.com/)에서 SSO를 구성 하려면이 정보가 필요 합니다.

### <a name="authentication-mechanism-details"></a>인증 메커니즘 세부 정보

사전 통합 된 모든 SaaS 앱에 대해 Microsoft는 자습서를 제공 하며,이 정보는 필요 하지 않습니다. 응용 프로그램이 응용 프로그램 마켓플레이스/갤러리에 없는 경우 다음 데이터를 수집 해야 할 수 있습니다.

- **응용 프로그램이 SSO에 대해 사용 하는 현재 id 공급자** (예: AD FS, 공동 페더레이션, okta)
- **대상 응용 프로그램에서 지 원하는 프로토콜** (예: SAML 2.0, openid connect Connect, OAuth, 폼 기반 인증, Ws-신뢰
- **AZURE AD를 사용 하 여 구성 되는 프로토콜** (예: SAML 2.0 또는 1.1, openid connect Connect, OAuth, Forms 기반, WS-급지됨)

### <a name="attribute-requirements"></a>특성 요구 사항

Azure AD 사용자 개체와 각 SaaS 앱의 사용자 개체 사이에는 미리 구성된 특성 및 특성 매핑 세트가 있습니다. 일부 앱은 그룹과 같은 다른 유형의 개체를 관리 합니다. Azure AD에서 응용 프로그램에 대 한 사용자 특성 매핑을 계획 하 고 비즈니스 요구에 따라 [기본 특성 매핑을 사용자 지정](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 합니다.

### <a name="certificate-requirements"></a>인증서 요구 사항

응용 프로그램에 대 한 인증서를 최신 상태로 유지 해야 합니다. 그렇지 않으면 사용자가 응용 프로그램에 액세스할 수 없는 위험이 있습니다. 대부분의 SaaS 응용 프로그램 인증서는 36 개월 동안 적합 합니다. 응용 프로그램 블레이드에서 해당 인증서 기간을 변경 합니다. 만료 사항을 문서화 하 고 인증서 갱신을 관리 하는 방법을 알고 있어야 합니다. 

인증서를 관리 하는 방법에는 두 가지가 있습니다. 

- **자동 인증서 롤오버** -Microsoft는 [Azure AD에서 서명 키 롤오버를](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)지원 합니다. 인증서를 관리 하는 데 선호 되는 방법 이지만 모든 ISV가이 시나리오를 지 원하는 것은 아닙니다.

- **수동으로 업데이트** -모든 응용 프로그램에는 정의 된 방식에 따라 만료 되는 자체 인증서가 있습니다. 응용 프로그램의 인증서가 만료 되기 전에 새 인증서를 만들어 ISV에 보냅니다. 이 정보는 페더레이션 메타 데이터에서 가져올 수 있습니다. [페더레이션 메타 데이터에 대 한 자세한 내용은 여기를 참조 하세요.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>SSO 구현

다음 단계를 사용 하 여 조직에서 솔루션을 계획 하 고 배포 합니다.

### <a name="user-configuration-for-sso"></a>SSO에 대 한 사용자 구성

- **테스트 사용자 식별**

   앱 소유자에 게 연락 하 여 응용 프로그램 내에서 최소 3 개의 테스트 사용자를 만들도록 요청 합니다. 기본 식별자로 사용할 정보가 올바르게 채워지고 Azure AD에서 사용할 수 있는 특성과 일치 하는지 확인 합니다. 대부분의 경우이는 SAML 기반 응용 프로그램의 "NameID"에 매핑됩니다. JWT 토큰의 경우 "preferred_username"입니다.
   
   클라우드 기반 사용자로 수동으로 Azure AD에서 사용자를 만들거나 Azure AD Connect 동기화 엔진을 사용 하 여 온-프레미스에서 사용자를 동기화 합니다. 정보가 응용 프로그램으로 전송 되는 클레임과 일치 하는지 확인 합니다.

- **SSL 구성**

   응용 프로그램 [목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)에서 응용 프로그램에 대 한 SSO 자습서를 찾아 연 다음 자습서의 단계에 따라 SaaS 응용 프로그램을 성공적으로 구성 합니다.

   응용 프로그램을 찾을 수 없는 경우 [사용자 지정 응용 프로그램 설명서](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)를 참조 하세요. Azure AD 갤러리에 없는 응용 프로그램을 추가 하는 방법에 대해 안내 합니다.

   필요에 따라 [Microsoft의 지침 설명서](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)를 사용 하 여 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임을 사용할 수 있습니다. 응용 프로그램에 대 한 SAML 응답에서 받아야 하는 항목에 매핑되는지 확인 합니다. 구성 하는 동안 문제가 발생 하 [는 경우 SSO 통합을 디버그 하는 방법](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)에 대 한 지침을 따르십시오.

### <a name="provide-sso-change-communications-to-end-users"></a>최종 사용자에 게 SSO 변경 통신 제공

통신 계획을 구현 합니다. 최종 사용자에 게 변경이 발생 하는 시점, 도착 시기, 지금 수행할 작업 및 지원을 찾는 방법을 알려 줍니다.

### <a name="verify-end-user-scenarios-for-sso"></a>SSO에 대 한 최종 사용자 시나리오 확인

다음 테스트 사례를 사용 하 여 회사 소유의 장치 및 개인 장치에서 테스트를 수행 하 여 SSO 구성이 예상 대로 작동 하는지 확인할 수 있습니다. 아래 시나리오는 사용자가 응용 프로그램 URL로 이동 하 고 서비스 공급자 (SP가 시작한 인증 흐름)에서 시작 하는 인증 흐름을 거치는 것으로 가정 합니다.

| 시나리오 | 사용자가 SP에서 시작한 인증 흐름의 예상 결과 |
|----------|---------------------------------------------------|
| Corpnet에서 IE를 사용 하 여 응용 프로그램에 로그인 합니다. | IWA (Windows 통합 인증)는 추가 프롬프트 없이 발생 합니다. |
| 새 로그인을 시도 하는 동안 IE를 사용 하 여 응용 프로그램에 로그인 corpnet. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 브라우저 프롬프트를 표시 합니다. |
| 현재 세션을 사용 하 여 corpnet 해제 하 고 MFA를 수행 하지 않은 상태에서 IE를 사용 하 여 응용 프로그램에 로그인 합니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 받지 않습니다. 사용자가 MFA에 대 한 프롬프트를 받습니다. |
| 현재 세션을 사용 하 여 corpnet을 해제 하 고이 세션에서 이미 MFA를 수행한 상태에서 IE를 사용 하 여 응용 프로그램에 로그인 합니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 받지 않습니다. 사용자가 MFA를 수신 하지 않습니다. 응용 프로그램에 사용자를 로그인 합니다. |
| 현재 세션에서 corpnet을 해제 하 고이 세션에서 이미 MFA를 수행 하는 동안 Chrome/Firefox/Safari를 사용 하 여 응용 프로그램에 로그인 합니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 받지 않습니다. 사용자가 MFA를 수신 하지 않습니다. 사용자 SSO의 응용 프로그램입니다. |
| 새 로그인 시도로 corpnet 하는 동안 Chrome/Firefox/Safari를 사용 하 여 응용 프로그램에 로그인 합니다. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가 성공적으로 로그인 하 고 MFA에 대 한 브라우저 프롬프트를 표시 합니다. |
| 현재 세션을 사용 하는 회사 네트워크에서 Chrome/Firefox를 사용 하 여 응용 프로그램에 로그인 합니다. | 사용자는 첫 번째 요소에 대 한 프롬프트를 받지 않습니다. 사용자가 MFA를 수신 하지 않습니다. 사용자 SSO의 응용 프로그램입니다. |
| 새 로그인 시도로 응용 프로그램 모바일 앱으로 응용 프로그램에 로그인 합니다. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가에 성공적으로 로그인 하 여 MFA에 대 한 ADAL 클라이언트 프롬프트가 표시 됩니다. |
| 인증 되지 않은 사용자가 로그인 URL을 사용 하 여 응용 프로그램에 로그인 하려고 합니다. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가 첫 번째 요소를 사용 하 여 로그인 하지 못했습니다. |
| 권한 있는 사용자가 로그인을 시도 하지만 잘못 된 암호를 입력 합니다. | 사용자가 응용 프로그램 URL로 이동 하 고 잘못 된 사용자 이름/암호 오류를 수신 합니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 하 고 이미 인증 되었습니다. | 사용자가 URL을 클릭 하면 추가 프롬프트 없이 응용 프로그램에 로그인 됩니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 했지만 아직 인증 되지 않았습니다. | 사용자가 URL을 클릭 하면 첫 번째 요소를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. |
| 권한 있는 사용자가 새 로그인 시도로 응용 프로그램 모바일 앱 (SP 시작)을 사용 하 여 응용 프로그램에 로그인 합니다. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가에 성공적으로 로그인 하 여 MFA에 대 한 ADAL 클라이언트 프롬프트가 표시 됩니다. |
| 인증 되지 않은 사용자가 로그인 URL (SP 시작)을 사용 하 여 응용 프로그램에 로그인 하려고 합니다. | AD FS Server의 폼 기반 프롬프트입니다. 사용자가 첫 번째 요소를 사용 하 여 로그인 하지 못했습니다. |
| 권한 있는 사용자가 로그인을 시도 하지만 잘못 된 암호를 입력 합니다.| 사용자가 응용 프로그램 URL로 이동 하 고 잘못 된 사용자 이름/암호 오류를 수신 합니다. |
| 권한 있는 사용자가 로그 아웃 한 다음 다시 로그인 합니다. | 로그 아웃 URL이 구성 된 경우 사용자는 모든 서비스에서 로그 아웃 하 고 인증 하 라는 메시지를 표시 합니다. |
| 권한 있는 사용자가 로그 아웃 한 다음 다시 로그인 합니다. | 로그 아웃 URL이 구성 되지 않은 경우 사용자는 기존 Azure AD browser 세션의 기존 토큰을 사용 하 여 자동으로 다시 로그인 됩니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 하 고 이미 인증 되었습니다. | 사용자가 URL을 클릭 하면 추가 프롬프트 없이 응용 프로그램에 로그인 됩니다. |
| 권한 있는 사용자가 전자 메일의 링크를 클릭 했지만 아직 인증 되지 않았습니다. | 사용자가 URL을 클릭 하면 첫 번째 요소를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. |

## <a name="manage-sso"></a>SSO 관리

이 섹션에서는 SSO를 관리 하기 위한 요구 사항 및 권장 사항에 대해 간략하게 설명 합니다.

### <a name="required-administrative-roles"></a>필요한 관리 역할

항상 사용 가능한 가장 적은 권한을 가진 역할을 사용 하 여 Azure Active Directory 내에서 필요한 작업을 수행 합니다. [사용 가능한 다른 역할을 검토](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 하 고 올바른 역할을 선택 하 여이 응용 프로그램의 각 가상 사용자에 대 한 요구 사항을 해결 하는 것이 좋습니다. 일부 역할은 배포를 완료 한 후 임시로 적용 해야 할 수도 있습니다.

| Persona| 역할 | Azure AD 역할 (필요한 경우) |
|--------|-------|-----------------------------|
| 지원 센터 관리자 | 계층 1 지원 | None |
| Id 관리 | 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그 | 글로벌 관리자 |
| 응용 프로그램 관리자 | 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성 | None |
| 인프라 관리자 | 인증서 롤오버 소유자 | 글로벌 관리자 |
| 비즈니스 소유자/관련자 | 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성 | None |

디렉터리 사용 권한을 가진 사용자에 대 한 추가 감사, 제어 및 액세스 검토를 제공 하기 위해 PIM ( [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) )을 사용 하 여 역할을 관리 하는 것이 좋습니다.

### <a name="sso-certificate-lifecycle-management"></a>SSO 인증서 수명 주기 관리

Azure AD와 Single Sign-On를 사용 하 여 구성 되는 응용 프로그램 간의 서명 인증서 수명 주기를 관리 하는 데 필요한 올바른 역할 및 메일 배포 목록을 확인 하는 것이 중요 합니다. 다음은 권장 되는 주요 역할 중 일부입니다.

- 응용 프로그램에서 사용자 속성을 업데이트 하기 위한 소유자
- 응용 프로그램 중단/수정 지원에 대 한 소유자 호출
- 인증서 관련 변경 알림에 대 한 면밀 하 게 모니터링 되는 메일 배포 목록

인증서의 최대 수명은 3 년입니다. Azure AD와 응용 프로그램 간의 인증서 변경을 처리 하는 방법에 대 한 프로세스를 설정 하는 것이 좋습니다. 이를 통해 인증서가 만료 되거나 인증서 롤오버를 강제로 실행 하 여 중단을 방지 하거나 최소화할 수 있습니다.

### <a name="rollback-process"></a>롤백 프로세스

테스트 사례를 기반으로 테스트를 완료 한 후에는 응용 프로그램을 사용 하 여 프로덕션으로 전환할 수 있습니다. 프로덕션으로 이동 하는 것은 프로덕션 테 넌 트에서 계획 및 테스트 된 구성을 구현 하 고 사용자에 게 배포 하는 것을 의미 합니다. 그러나 배포가 계획 대로 진행 되지 않는 경우 수행할 작업을 계획 하는 것이 중요 합니다. 배포 하는 동안 SSO 구성이 실패 하는 경우 중단을 완화 하 고 사용자에 대 한 영향을 줄이는 방법을 이해 해야 합니다.

응용 프로그램 내에서 인증 방법의 가용성에 따라 가장 적합 한 전략이 결정 됩니다. 배포에 문제가 발생 하는 경우 언제 든 지 원래 로그인 구성 상태로 다시 전환 하는 방법에 대 한 자세한 내용은 앱 소유자에 게 문의 하세요.

- **앱이 여러 id 공급자**(예: LDAP 및 AD FS 및 Ping)를 지 원하는 경우 롤아웃 중에 기존 SSO 구성을 삭제 하지 마세요. 대신 나중에 다시 전환 해야 하는 경우 마이그레이션 중에 사용 하지 않도록 설정 합니다. 

- **앱이 여러 IDPs를 지원 하지** 않지만 사용자가 양식 기반 인증 (사용자 이름/암호)을 사용 하 여 로그인 할 수 있는 경우 새 SSO 구성 롤아웃에 실패 하는 경우 사용자가이 방법으로 대체할 수 있습니다.

### <a name="access-management"></a>액세스 관리

리소스에 대 한 액세스를 관리할 때 크기 조정 된 접근 방식을 선택 하는 것이 좋습니다. 일반적인 방법으로는 Azure AD Connect를 통해 동기화 하거나, [사용자 특성에 따라 AZURE ad에서 동적 그룹을 만들거나](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), 리소스 소유자가 관리 하는 azure ad에서 [셀프 서비스 그룹](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) 을 만드는 방식으로 온-프레미스 그룹을 활용 합니다.

### <a name="monitor-security"></a>보안 모니터링

SaaS 앱 보안의 다양 한 측면을 검토 하 고 필요한 수정 작업을 수행 하는 정기적인 흐름을 설정 하는 것이 좋습니다.

### <a name="troubleshooting"></a>문제 해결

다음 링크는 문제 해결 시나리오를 제공 합니다. 이러한 시나리오와 이러한 시나리오를 해결 하는 단계를 통합 하는 지원 직원을 위한 특정 가이드를 만들 수 있습니다.

#### <a name="consent-issues"></a>동의 문제

- [예기치 않은 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [사용자 동의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>로그인 문제

- [사용자 지정 포털에서 로그인 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [내 앱에서 로그인 할 때 발생 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [애플리케이션 로그인 페이지의 오류](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Microsoft 응용 프로그램에 로그인 하는 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Azure 애플리케이션 갤러리에 나열 된 응용 프로그램에 대 한 SSO 문제

- [Azure 애플리케이션 갤러리에 나열 된 응용 프로그램에 대 한 암호 SSO와 관련 된 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Azure 애플리케이션 갤러리에 나열 된 응용 프로그램의 페더레이션된 SSO에 대 한 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Azure 애플리케이션 갤러리에 나열 되지 않은 응용 프로그램에 대 한 SSO 문제

- [Azure 애플리케이션 갤러리에 나열 되지 않은 응용 프로그램에 대 한 암호 SSO와 관련 된 문제](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Azure 애플리케이션 갤러리에 나열 되지 않은 응용 프로그램의 페더레이션된 SSO에 문제가 있습니다.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>다음 단계

[SAML 기반 SSO 디버그](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[PowerShell을 통해 앱에 대 한 클레임 매핑](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Single Sign-on SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Single Sign-Out SAML 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (파트너 및 공급 업체와 같은 외부 사용자의 경우)

[Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Id 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[SSO 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[응용 프로그램 SSO 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

[백서 다운로드-Single sign-on 배포 계획](https://aka.ms/SSODeploymentPlan)