---
title: Azure Active Directory란? - Azure Active Directory | Microsoft Docs
description: 용어, 사용 가능한 라이선스, 자세한 정보를 제공하는 링크가 포함된 관련 기능 목록을 포함하여 Azure Active Directory에 대한 개요 및 개념 정보를 제공합니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.date: 06/05/2020
ms.author: ajburnle
ms.custom: it-pro, seodec18, seo-update-azuread-jan, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91e89f274615e75fec818ec9e3ecf785aa6e804f
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262961"
---
# <a name="what-is-azure-active-directory"></a>Azure Active Directory란?

Azure AD(Azure Active Directory)는 직원들이 로그인하여 다음 리소스에 액세스할 수 있게 해주는 Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스입니다.

- Microsoft Office 365, Azure Portal, 수천 개의 기타 SaaS 애플리케이션을 비롯한 외부 리소스.

- 조직에서 자체 개발한 클라우드 앱과 함께 회사 네트워크와 인트라넷의 앱 같은 내부 리소스. 조직의 테넌트를 만드는 방법에 대한 자세한 내용은 [빠른 시작을 참조하세요. Azure Active Directory에서 새 테넌트 만들기](active-directory-access-create-new-tenant.md)

Azure AD와 Active Directory Domain Services 간의 차이점에 대한 자세한 내용은 [Active Directory와 Azure Active Directory 비교](active-directory-compare-azure-ad-to-ad.md)를 참조하세요. 다양한 [Microsoft Cloud for Enterprise Architects 시리즈](https://docs.microsoft.com/microsoft-365/solutions/cloud-architecture-models?view=o365-worldwide) 포스터를 사용하여 Azure, Azure AD 및 Office 365의 핵심 ID 서비스를 보다 잘 이해할 수 있습니다.

## <a name="who-uses-azure-ad"></a>누가 Azure AD를 사용하나요?

Azure AD의 대상은 다음과 같습니다.

- **IT 관리자.** IT 관리자는 Azure AD를 사용하여 비즈니스 요구 사항에 따라 앱 및 앱 리소스에 대한 액세스를 제어할 수 있습니다. 예를 들어 Azure AD를 사용하여 중요한 조직 리소스에 액세스할 때에는 다단계 인증을 요구할 수 있습니다. 또한 Azure AD를 사용하여 기존 Windows Server AD와 Office 365를 비롯한 클라우드 앱 간에 사용자 프로비전을 자동화할 수 있습니다. 마지막으로, Azure AD는 자동으로 사용자 ID 및 자격 증명을 보호하고 액세스 거버넌스 요구 사항을 충족하는 강력한 도구를 제공합니다. 시작하려면 [30일 Azure Active Directory Premium 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)에 등록하세요.

- **앱 개발자.** 앱 개발자는 앱에 SSO(Single Sign-On)를 추가하여 사용자의 기존 자격 증명과 함께 사용할 수 있는 표준 기반 접근 방식으로 Azure AD를 사용할 수 있습니다. Azure AD는 조직의 기존 데이터를 사용하여 맞춤형 앱 환경을 빌드할 수 있는 API도 제공합니다. 시작하려면 [30일 Azure Active Directory Premium 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)에 등록하세요. 자세한 내용은 [개발자용 Azure Active Directory](../develop/index.yml)를 참조하세요.

- **Microsoft 365, Office 365, Azure 또는 Dynamics CRM Online 구독.** 여러분은 구독자로서 이미 Azure AD를 사용하고 있습니다. 각 Microsoft 365, Office 365, Azure 및 Dynamics CRM Online 테넌트는 자동으로 Azure AD 테넌트입니다. 통합 클라우드 앱에 대한 액세스를 즉시 관리할 수 있습니다.

## <a name="what-are-the-azure-ad-licenses"></a>Azure AD 라이선스란?

Office 365 또는 Microsoft Azure 같은 Microsoft Online 비즈니스 서비스는 로그인 및 ID 보호를 위해 Azure AD가 필요합니다. Microsoft Online 비즈니스 서비스를 구독하는 경우 모든 무료 기능을 사용할 수 있는 Azure AD가 자동으로 제공됩니다.

Azure AD 구현을 향상하기 위해 Azure Active Directory Premium P1 또는 Premium P2 라이선스로 업그레이드하여 유료 기능을 추가할 수도 있습니다. Azure AD 유료 라이선스는 기존 무료 디렉터리 위에 빌드되며, 돌아다니면서 일하는 직원에게 셀프 서비스, 향상된 모니터링, 보안 보고 및 보안 액세스를 제공합니다.

>[!Note]
>이러한 라이선스의 가격 책정 옵션은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.
>
>Azure Active Directory Premium P1 및 Premium P2는 현재 중국에서 지원되지 않습니다. Azure AD 가격 책정에 대한 자세한 내용은 [Azure Active Directory 포럼](https://azure.microsoft.com/support/community/?product=active-directory)에 문의하세요.

- **Azure Active Directory Free.** Azure, Office 365 및 여러 인기 SaaS 앱에 사용자 및 그룹 관리, 온-프레미스 디렉터리 동기화, 기본 보고서, 클라우드 사용자를 위한 셀프 서비스 암호 변경 및 Single Sign-On을 제공합니다.

- **Azure Active Directory Premium P1.** P1은 Free 기능 외에도 하이브리드 사용자에게 온-프레미스 및 클라우드 리소스에 대한 액세스를 제공합니다. 또한 온-프레미스 사용자에 대한 셀프 서비스 암호 재설정을 허용하는 동적 그룹, 셀프 서비스 그룹 관리, Microsoft Identity Manager(온-프레미스 ID 및 액세스 관리 도구 모음), 클라우드 쓰기 저장 기능 등의 고급 관리를 지원합니다.

- **Azure Active Directory Premium P2.** P2는 Free 및 P1 기능 외에도 앱 및 중요한 회사 데이터에 대한 위험 기반 조건부 액세스를 제공하는 [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)과 관리자의 리소스 액세스를 검색, 제한, 모니터링하고 필요할 때 적시에 액세스를 제공할 수 있도록 도와주는 [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)를 제공합니다.

- **"종량제" 기능 라이선스.** Azure Active Directory B2C(Business-to-Customer) 같은 추가 기능 라이선스도 얻을 수 있습니다. B2C는 고객용 앱의 ID 및 액세스 관리 솔루션을 제공하는 데 도움이 될 수 있습니다. 자세한 내용은 [Azure Active Directory B2C 설명서](../../active-directory-b2c/index.yml)를 참조하세요.

Azure 구독을 Azure AD에 연결하는 방법에 대한 자세한 내용은 [Azure Active Directory에 Azure 구독 연결 또는 추가](active-directory-how-subscriptions-associated-directory.md)를 참조하고, 사용자에게 라이선스를 할당하는 방법에 대한 자세한 내용은 [방법:을 참조하세요. Azure Active Directory 라이선스 할당 또는 제거](license-users-groups.md)를 참조하세요.

## <a name="which-features-work-in-azure-ad"></a>Azure AD에서 작동하는 기능

Azure AD 라이선스를 선택하면 조직에서 사용 가능한 다음 기능 중 일부 또는 전부에 대한 액세스 권한을 얻게 됩니다.

|범주|Description|
|-------|-----------|
|애플리케이션 관리|애플리케이션 프록시, Single Sign-On, 내 앱 포털(액세스 패널이라고도 함) 및 SaaS(Software as a Service) 앱을 사용하여 클라우드 및 온-프레미스 앱을 관리합니다. 자세한 내용은 [온-프레미스 애플리케이션에 대한 보안 원격 액세스를 제공하는 방법](../manage-apps/application-proxy.md) 및 [애플리케이션 관리 설명서](../manage-apps/index.yml)를 참조하세요.|
|인증|Azure Active Directory 셀프 서비스 암호 재설정, Multi-Factor Authentication, 사용자 지정 금지된 암호 목록 및 스마트 잠금을 관리합니다. 자세한 내용은 [Azure AD 인증 설명서](../authentication/index.yml)를 참조하세요.|
|개발자용 Azure Active Directory|모든 Microsoft ID를 로그인하고 Microsoft Graph, 기타 Microsoft API 또는 사용자 지정 API를 호출하는 토큰을 가져오는 앱을 빌드합니다. 자세한 내용은 [Microsoft ID 플랫폼(개발자용 Azure Active Directory)](../develop/index.yml)를 참조하세요.|
|B2B(Business to Business)|회사 데이터에 대한 제어를 유지하면서도 게스트 사용자 및 외부 파트너를 관리합니다. 자세한 내용은 [Azure Active Directory B2B 설명서](../b2b/index.yml)를 참조하세요.|
|B2C(Business to Consumer)|사용자가 앱을 사용할 때 프로필을 등록, 로그인 및 관리하는 방법을 사용자 지정하고 제어합니다. 자세한 내용은 [Azure Active Directory B2C 설명서](../../active-directory-b2c/index.yml)를 참조하세요.|
|조건부 액세스|클라우드 앱에 대한 액세스 관리 자세한 내용은 [Azure AD 조건부 액세스 설명서](../conditional-access/index.yml)를 참조하세요.|
|디바이스 관리|클라우드 또는 온-프레미스 디바이스가 회사 데이터에 액세스하는 방법을 관리합니다. 자세한 내용은 [Azure AD 디바이스 관리 설명서](../devices/index.yml)를 참조하세요.|
|도메인 서비스|도메인 컨트롤러를 사용하지 않고 도메인에 Azure 가상 머신을 조인합니다. 자세한 내용은 [Azure AD Domain Services 설명서](../../active-directory-domain-services/index.yml)를 참조하세요.|
|Enterprise 사용자|그룹 및 관리자 역할을 사용하여 라이선스를 할당하고, 앱에 액세스하고, 대리자를 설정합니다. 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.|
|하이브리드 ID|Azure Active Directory Connect 및 Connect Health를 사용하여 위치(클라우드 또는 온-프레미스)에 관계 없이 모든 리소스를 인증하고 권한을 부여할 수 있는 단일 사용자 ID를 제공합니다. 자세한 내용은 [하이브리드 ID 설명서](../hybrid/index.yml)를 참조하세요.|
|ID 거버넌스|직원, 비즈니스 파트너, 공급업체, 서비스 및 앱 액세스 컨트롤을 통해 조직의 ID를 관리합니다. 액세스 검토를 수행할 수도 있습니다. 자세한 내용은 [Azure AD ID 거버넌스 설명서](../governance/identity-governance-overview.md) 및 [Azure AD 액세스 검토](../governance/access-reviews-overview.md)를 참조하세요.|
|ID 보호|조직의 ID에 영향을 미치는 잠재적 취약점을 검색하고, 의심스러운 작업에 대응하는 정책을 구성하고, 문제를 해결하기 위한 적절한 조치를 취합니다. 자세한 내용은 [Azure AD ID 보호](../identity-protection/index.yml)를 참조하세요.|
|Azure 리소스에 대한 관리 ID|Key Vault를 포함하여 Azure AD에서 지원되는 모든 인증 서비스를 인증할 수 있는 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요.|
|PIM(Privileged Identity Management)|조직 내 액세스를 관리, 제어 및 모니터링합니다. 이 기능에는 Azure AD, Azure 및 기타 Microsoft Online Services(예: Office 365 또는 Intune)의 리소스에 대한 액세스 권한이 포함되어 있습니다. 자세한 내용은 [Azure AD Privileged Identity Management](../privileged-identity-management/index.yml)를 참조하세요.|
|보고 및 모니터링|환경의 보안과 사용 패턴에 대한 인사이트를 얻을 수 있습니다. 자세한 내용은 [Azure Active Directory 보고서 및 모니터링](../reports-monitoring/index.yml)를 참조하세요.|

## <a name="terminology"></a>용어

Azure AD 및 설명서를 보다 정확하게 이해하려면 다음 용어를 검토하는 것이 좋습니다.

|용어 또는 개념|Description|
|---------------|-----------|
|ID| 인증을 받을 수 있는 대상입니다. ID는 사용자 이름과 암호를 사용하는 사용자일 수 있습니다. ID에는 비밀 키나 인증서를 통해 인증을 필요로 할 수 있는 다른 서버나 애플리케이션도 포함됩니다.|
|계정| 연결된 데이터가 있는 ID입니다. ID가 없는 계정은 있을 수 없습니다.|
|Azure AD 계정| Azure AD 또는 Office 365 같은 다른 Microsoft 클라우드 서비스를 통해 만들어진 ID입니다. ID는 Azure AD에 저장되고 조직의 클라우드 서비스 구독에 액세스할 수 있습니다. 이 계정을 회사 또는 학교 계정이라고도 합니다.|
|계정 관리자|이 클래식 구독 관리자 역할은 개념적으로 구독의 청구 소유자입니다. 이 역할은 [Azure 계정 센터](https://account.azure.com/Subscriptions)에 액세스할 수 있으며 계정의 모든 구독을 관리할 수 있습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.|
|서비스 관리자|이 클래식 구독 관리자 역할은 액세스를 포함하여 모든 Azure 리소스를 관리할 수 있습니다. 이 역할은 구독 범위에서 소유자 역할이 할당된 사용자와 동일한 액세스 권한을 갖습니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.|
|소유자|이 역할은 액세스를 포함하여 모든 Azure 리소스를 관리할 수 있습니다. 이 역할은 Azure 리소스에 대한 세밀한 액세스 관리를 제공하는 RBAC(역할 기반 액세스 제어)라고 하는 최신 권한 부여 시스템 기반입니다. 자세한 내용은 [클래식 구독 관리자 역할, Azure 역할 및 Azure AD 관리자 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조하세요.|
|Azure AD 전역 관리자|이 관리자 역할은 Azure AD 테넌트를 만든 모든 사람에게 자동으로 할당됩니다. 글로벌 관리자는 Exchange Online, SharePoint Online, 비즈니스용 Skype Online 등의 서비스에 페더레이션되는 서비스 및 Azure AD에 대한 모든 관리 기능을 수행할 수 있습니다. 글로벌 관리자를 여러 명 둘 수 있지만, 글로벌 관리자만이 사용자에게 관리자 역할을 할당(다른 글로벌 관리자 할당 포함)할 수 있습니다. 이 관리자 역할은 Azure Portal에서 전역 관리자라고 하지만 Microsoft Graph API 및 Azure AD PowerShell에서 **회사 관리자**라고 합니다. 다양한 관리자 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할 사용 권한](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.|
|Azure 구독| Azure 클라우드 서비스 요금을 지불하는 데 사용됩니다. 여러 구독을 한 신용 카드에 연결할 수 있습니다.|
|Azure 테넌트| 조직이 Microsoft Azure, Microsoft Intune 또는 Office 365 같은 Microsoft 클라우드 서비스 구독에 등록할 때 자동으로 생성되는 Azure AD의 신뢰할 수 있는 전용 인스턴스입니다. 한 Azure 테넌트는 단일 조직을 나타냅니다.|
|단일 테넌트| 전용 환경의 다른 서비스에 액세스하는 Azure 테넌트는 단일 테넌트로 간주됩니다.|
|다중 테넌트| 여러 조직에서 공유하는 환경의 다른 서비스에 액세스하는 Azure 테넌트는 다중 테넌트로 간주됩니다.|
|Azure AD Directory|Azure 테넌트마다 신뢰할 수 있는 전용 Azure AD 디렉터리가 있습니다. Azure AD 디렉터리는 테넌트의 사용자, 그룹 및 앱을 포함하며 테넌트 리소스에 대한 ID 및 액세스 관리를 수행하는 데 사용됩니다.|
|사용자 지정 도메인|모든 새 Azure AD 디렉터리는 domainname.onmicrosoft.com이라는 초기 도메인 이름으로 제공됩니다. 이 초기 이름 외에도, 고객이 비즈니스를 수행하는 데 사용되고 사용자가 조직 리소스에 액세스하는 데 사용되는 이름을 포함하는 조직의 도메인 이름을 목록에 추가할 수 있습니다. 사용자 지정 도메인 이름을 추가하면 alain@contoso.com처럼 사용자에게 친숙한 사용자 이름을 만들 수 있습니다.|
|Microsoft 계정(MSA라고도 함)|Outlook, OneDrive, Xbox LIVE 또는 Office 365 같은 소비자 지향 Microsoft 제품 및 클라우드 서비스에 대한 액세스 권한을 제공하는 개인 계정입니다. Microsoft 계정은 Microsoft에서 실행하는 Microsoft 소비자 ID 계정 시스템에 생성되고 저장됩니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory Premium에 등록](active-directory-get-started-premium.md)

- [Azure Active Directory에 Azure 구독 연결](active-directory-how-subscriptions-associated-directory.md)

- [Azure Active Directory Premium P2 기능 배포 검사 목록](active-directory-deployment-checklist-p2.md)
