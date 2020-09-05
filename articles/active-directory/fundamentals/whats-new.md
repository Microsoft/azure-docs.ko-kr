---
title: 새로운 기능은 무엇입니까? 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 새로운 기능에 대해 알아봅니다. 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용 되지 않는 기능 및 예정 된 변경 사항 등이 있습니다.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d8ce5f7ec59df16b71a1fbaf51950ee4c31872a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268947"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`를 ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 복사하고 붙어넣어 업데이트를 위해 이 페이지를 다시 방문해야 할 때 알림을 받습니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6 개월 보다 오래 된 항목을 찾는 경우 [Azure Active Directory의 새로운 기능에 대 한 보관](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="august-2020"></a>2020년 8월 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Azure Multi-Factor Authentication 서버 방화벽 요구 사항에 대 한 업데이트

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
2020 년 10 월 1 일부 터 Azure MFA 서버 방화벽 요구 사항에는 추가 IP 범위가 필요 합니다.

조직에서 아웃 바운드 방화벽 규칙을 사용 하는 경우 MFA 서버가 필요한 모든 IP 범위와 통신할 수 있도록 규칙을 업데이트 합니다. IP 범위는 [Azure Multi-Factor Authentication 서버 방화벽 요구 사항](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)에 설명 되어 있습니다.

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Id 보안 점수의 사용자 환경에 대 한 예정 된 변경 내용

**유형:** 변경 계획  
**서비스 범주:** Id 보호 **제품 기능:** id 보안 & 보호

Microsoft 보안 점수의 [새로운 릴리스에](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)도입 된 변경 내용에 맞게 Id 보안 점수 포털을 업데이트 하 고 있습니다. 

변경 내용이 포함 된 미리 보기 버전은 9 월부터 사용할 수 있습니다. 미리 보기 버전의 변경 내용은 다음과 같습니다.
- Microsoft 보안 점수를 사용 하 여 브랜드 맞춤을 위해 "id 보안 점수"의 이름을 "Id 보안 점수"로 바꿨습니다.
- 표준 배율으로 정규화 되 고 점이 아닌 백분율로 보고 되는 점수

이 미리 보기에서는 고객이 기존 환경과 새 환경을 전환할 수 있습니다. 이 미리 보기는 11 월 2020이 끝날 때까지 지속 됩니다. 미리 보기 후에는 고객이 자동으로 새로운 UX 환경으로 이동 됩니다.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Azure AD에서 제한 된 새 게스트 액세스 권한-공개 미리 보기

**유형:** 새로운 기능  
**서비스 범주:** Access Control   
**제품 기능:** 사용자 관리

게스트 사용자에 대 한 디렉터리 수준 권한을 업데이트 했습니다. 이러한 사용 권한을 통해 관리자는 외부 게스트 사용자 액세스에 대 한 추가 제한 사항 및 제어를 요구할 수 있습니다. 이제 관리자는 외부 게스트의 사용자 및 그룹의 프로필 및 멤버 자격 정보에 대 한 추가 제한을 추가할 수 있습니다. 이 공개 미리 보기 기능을 사용 하면 고객은 게스트 사용자가 자신이 속한 그룹의 멤버 자격을 볼 수 없도록 제한 하는 것을 포함 하 여 난독 처리 그룹 멤버 자격을 통해 규모에 따라 외부 사용자 액세스를 관리할 수 있습니다.

자세히 알아보려면 [제한 된 게스트 액세스 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-restrict-guest-permissions) 및 [사용자 기본 권한](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)을 참조 하세요.
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>서비스 사용자에 대 한 델타 쿼리의 일반 가용성

**유형:** 새로운 기능  
**서비스 범주:** MS 그래프  
**제품 기능:** 개발자 환경
 
이제 Microsoft Graph 델타 쿼리가 v 1.0의 리소스 형식을 지원 합니다.
- 서비스 주체

이제 클라이언트는 해당 리소스에 대 한 변경 내용을 효율적으로 추적할 수 있으며, 해당 리소스에 대 한 변경 내용을 로컬 데이터 저장소와 동기화 하는 최상의 솔루션을 제공 합니다. 쿼리에서 이러한 리소스를 구성 하는 방법을 알아보려면 [델타 쿼리를 사용 하 여 Microsoft Graph 데이터의 변경 내용 추적](https://docs.microsoft.com/graph/delta-query-overview)을 참조 하세요.
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>OAuth2PermissionGrant에 대 한 델타 쿼리의 일반 가용성

**유형:** 새로운 기능  
**서비스 범주:** MS 그래프  
**제품 기능:** 개발자 환경

이제 Microsoft Graph 델타 쿼리가 v 1.0의 리소스 형식을 지원 합니다.
- OAuth2PermissionGrant

클라이언트는 이제 해당 리소스에 대 한 변경 내용을 효율적으로 추적할 수 있으며, 로컬 데이터 저장소와 이러한 리소스의 변경 내용을 동기화 하는 최상의 솔루션을 제공 합니다. 쿼리에서 이러한 리소스를 구성 하는 방법을 알아보려면 [델타 쿼리를 사용 하 여 Microsoft Graph 데이터의 변경 내용 추적](https://docs.microsoft.com/graph/delta-query-overview)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-8 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

8 월 2020에 앱 갤러리에 페더레이션 지원이 포함 된 다음과 같은 26 개의 새 응용 프로그램이 추가 되었습니다.

[Backup365](https://portal.backup365.io/login), [soapbox](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [alma SIS](https://almau.getalma.com/), [enlyft Dynamics 365 커넥터](http://enlyft.com/), [serraview 공간 사용률 소프트웨어 솔루션](https://docs.microsoft.com/azure/active-directory/saas-apps/serraview-space-utilization-software-solutions-tutorial), [최대 q](https://web.uniq.app/), [가시적](https://docs.microsoft.com/azure/active-directory/saas-apps/visibly-tutorial), [ZYSTA](https://docs.microsoft.com/azure/active-directory/saas-apps/zylo-tutorial), [Edmentum-교육용 평가 정확한 경로](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira hrm](https://docs.microsoft.com/azure/active-directory/saas-apps/altamira-hrm-tutorial), [WireWheel](https://docs.microsoft.com/azure/active-directory/saas-apps/wirewheel-tutorial), [Zix 준수 및 캡처](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-enterprise-business-controls-platform-tutorial), [genetec 클리어런스](https://www.clearance.network/), [Isams](https://docs.microsoft.com/azure/active-directory/saas-apps/isams-tutorial), [VERASMART](https://docs.microsoft.com/azure/active-directory/saas-apps/verasmart-tutorial), [amiko](https://amiko.web.rivero.app/), [twingate](https://auth.twingate.com/signup), [깔때기형 임대](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/), [Bpanda](https://goto.bpanda.com/login), [vivun 일정 연결](https://app.vivun.com/dashboard/calendar/connect), [rstudio 서버 Pro](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-server-pro-tutorial), [fortigate SSL VPN](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial), [Wandera 최종 사용자](https://www.wandera.com/)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다. https://aka.ms/AppsTutorial

Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 읽으십시오. https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>이제 Azure AD DS에 리소스 포리스트 사용 가능 

**유형:** 새 기능 **서비스 범주:** Azure AD Domain Services   
**제품 기능:** Azure AD Domain Services
 
Azure AD Domain Services에서 리소스 포리스트의 기능이 이제 일반 공급 됩니다. 이제 스마트 카드 권한 부여를 비롯 하 여 Azure AD Domain Services를 사용 하기 위해 암호 해시 동기화 없이 권한 부여를 사용 하도록 설정할 수 있습니다. 자세한 내용은 [Azure Active Directory Domain Services에 대 한 복제본 집합 개념 및 기능 (미리 보기)](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets)을 참조 하세요.
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>이제 Azure AD DS 관리 되는 도메인에 대 한 지역 복제본 지원이 제공 됩니다.

**유형:** 새로운 기능   
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services
 
관리되는 도메인을 확장하여 Azure AD 테넌트마다 복제본 세트를 2개 이상 포함할 수 있습니다. Azure AD Domain Services를 지 원하는 모든 Azure 지역의 피어 링 가상 네트워크에 복제본 집합을 추가할 수 있습니다. 다른 Azure 지역에 있는 추가 복제본 세트는 Azure 지역이 오프라인 상태가 될 때 레거시 애플리케이션에 대한 지리적 재해 복구를 제공합니다. 자세한 내용은 [Azure Active Directory Domain Services에 대 한 복제본 집합 개념 및 기능 (미리 보기)](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets)을 참조 하세요.

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Azure AD 내 로그인의 일반 공급

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 최종 사용자 환경
 
Azure AD 내 로그인은 엔터프라이즈 사용자가 로그인 기록을 검토 하 여 비정상적인 활동을 확인할 수 있도록 하는 새로운 기능입니다. 또한이 기능을 사용 하면 최종 사용자가 의심 스러운 활동에 대해 "This in This" 또는 "This was"를 보고할 수 있습니다. 이 기능을 사용 하는 방법에 대해 자세히 알아보려면 [내 로그인 페이지에서 최근 로그인 활동 보기 및 검색](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-sign-ins-page#confirm-unusual-activity)을 참조 하세요.
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Azure AD에 대 한 SAP SuccessFactors HR 기반 사용자 프로비저닝이 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이제 Azure AD를 사용 하 여 SAP SuccessFactors를 신뢰할 수 있는 id 원본으로 통합 하 고 새 채용 및 종료와 같은 HR 이벤트를 사용 하 여 종단 간 id 수명 주기를 자동화 하 여 Azure AD에서 계정 프로 비전 및 프로 비전 해제를 수행할 수 있습니다. 

Azure AD에 대 한 SAP SuccessFactors 인바운드 프로 비전을 구성 하는 방법에 대 한 자세한 내용은 [사용자 프로 비전을 ACTIVE DIRECTORY Sap SuccessFactors 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-successfactors-inbound-provisioning-tutorial)자습서를 참조 하세요.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Azure AD B2C에 대 한 사용자 지정 Open ID Connect MS Graph API 지원

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
이전에는 사용자 지정 Open ID Connect 공급자를 Azure Portal 통해서만 추가 하거나 관리할 수 있었습니다. 이제 Azure AD B2C 고객이 Microsoft Graph Api 베타 버전을 통해 추가 하 고 관리할 수 있습니다. Api를 사용 하 여이 리소스를 구성 하는 방법을 알아보려면 [identityProvider 리소스 형식](https://docs.microsoft.com/graph/api/resources/identityprovider?view=graph-rest-beta)을 참조 하세요.
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>클라우드 그룹에 Azure AD 기본 제공 역할 할당

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어

이제이 새로운 기능을 사용 하 여 Azure AD 기본 제공 역할을 클라우드 그룹에 할당할 수 있습니다. 예를 들어 SharePoint 관리자 역할을 Contoso_SharePoint_Admins 그룹에 할당할 수 있습니다. 또한 PIM을 사용 하 여 그룹을 적절 한 액세스 권한을 부여 하는 대신 역할의 적격 멤버로 만들 수 있습니다. 이 기능을 구성 하는 방법을 알아보려면 [클라우드 그룹을 사용 하 여 Azure Active Directory에서 역할 할당 관리 (미리 보기)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-groups-concept)를 참조 하세요.
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Insights 비즈니스 리더 기본 제공 역할을 이제 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
Insights 비즈니스 리더 역할의 사용자는 [M365 insights 응용 프로그램](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)을 통해 대시보드 및 통찰력 집합에 액세스할 수 있습니다. 여기에는 모든 대시보드에 대 한 모든 액세스 권한 및 제공 되는 정보 및 데이터 탐색 기능이 포함 됩니다. 그러나이 역할의 사용자는 정보 관리자 역할을 담당 하는 제품 구성 설정에 액세스할 수 없습니다. 이 역할에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#insights-business-leader) 을 참조 하세요.
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>이제 Insights 관리자 기본 제공 역할을 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
Insights 관리자 역할의 사용자는 [M365 Insights 응용 프로그램](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)의 전체 관리 기능 집합에 액세스할 수 있습니다. 이 역할의 사용자는 디렉터리 정보를 읽고, 서비스 상태를 모니터링 하 고, 파일 지원 티켓을 확인 하 고, 정보 관리자 설정 측면에 액세스할 수 있습니다. 이 역할에 대 한 자세한 내용은 [Azure Active Directory의 관리자 역할 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#insights-administrator) 을 참조 하세요.
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자는 응용 프로그램의 확장 속성을 관리할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
이전에는 전역 관리자만 [확장 속성](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http)을 관리할 수 있었습니다. 이제 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자에 게이 기능을 사용 하도록 설정 하는 중입니다.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>MIM 2016 SP2 핫픽스 4.6.263.0 및 커넥터 1.1.1301.0

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** Id 수명 주기 관리

[핫픽스 롤업 패키지 (build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) 는 MICROSOFT IDENTITY MANAGER (MIM) 2016 서비스 팩 2 (SP2)에서 사용할 수 있습니다. 이 롤업 패키지에는 MIM CM, MIM 동기화 관리자 및 PAM 구성 요소에 대 한 업데이트가 포함 되어 있습니다. 또한 MIM 일반 커넥터 1.1.1301.0 빌드에는 Graph 커넥터에 대 한 업데이트가 포함 되어 있습니다.

---
 
## <a name="july-2020"></a>2020년 7월

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>IT 관리자는 조건부 액세스를 사용 하 여 클라이언트 앱을 대상으로 합니다.

**유형:** 변경 계획   
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 
조건부 액세스에서 클라이언트 앱 조건의 GA 릴리스를 사용 하면 이제 모든 클라이언트 응용 프로그램에 새 정책이 기본적으로 적용 됩니다. 여기에는 레거시 인증 클라이언트가 포함 됩니다. 기존 정책은 변경 되지 않고 그대로 유지 되지만, 정책에 따라 적용 되는 클라이언트 앱을 쉽게 확인 하기 위해 기존 정책에서 *예/아니요 구성* 토글이 제거 됩니다. 

새 정책을 만들 때 여전히 레거시 인증을 사용 하는 사용자 및 서비스 계정을 제외 해야 합니다. 그렇지 않으면 차단 됩니다. [자세한 정보를 알아보세요](https://aka.ms/caclientapps).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>예정 된 SCIM 준수 픽스

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 응용 프로그램과 통합 하기 위해 SCIM 표준을 활용 합니다. SCIM 표준의 구현은 진화 하 고 있으며, 패치 작업을 수행 하는 방법에 대 한 동작을 변경 하 고 리소스에 "active" 속성을 설정 하는 것이 좋습니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 관리 포털의 그룹 소유자 설정이 변경 됨

**유형:** 변경 계획  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

그룹에 대 한 소유자 설정 일반 설정 페이지는 Azure 관리 포털 및 액세스 패널에서 소유자 할당 권한을 제한 된 사용자 그룹으로 제한 하도록 구성할 수 있습니다. 이러한 두 UX 포털 뿐만 아니라 그룹 소유자 권한을 할당 하는 기능이 곧 제공 될 예정 이며, PowerShell 및 Microsoft Graph 같은 끝점에서 일관 된 동작을 제공 하기 위해 백 엔드에서 정책을 적용할 수 있습니다. 

Microsoft는이를 사용 하지 않는 고객에 대 한 현재 설정을 사용 하지 않도록 설정 하 고, 향후 몇 개월 동안 그룹 소유자 권한으로 사용자의 범위를 설정 하는 옵션을 제공 합니다. 그룹 설정 업데이트에 대 한 지침은 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)를 사용 하 여 그룹 정보 편집을 참조 하세요.

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 등록 서비스가 TLS 1.0 및 1.1에 대 한 지원을 종료 합니다.

**유형:** 변경 계획  
**서비스 범주:** 장치 등록 및 관리  
**제품 기능:** 플랫폼

TLS (전송 계층 보안) 1.2 및 업데이트 서버와 클라이언트는 Azure Active Directory Device Registration 서비스와 곧 통신할 수 있습니다. Azure AD Device Registration service와 통신 하는 TLS 1.0 및 1.1에 대 한 지원은 사용 중지 됩니다.
- 모든 소 버린 클라우드의 2020 년 8 월 31 일 (GCC High, DoD 등)
- 모든 상용 클라우드의 2020 년 10 월 30 일

Azure AD 등록 서비스에 대 한 TLS 1.2에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2) .

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD 로그인 로그에 표시 되는 비즈니스용 Windows Hello 로그인

**유형:** 고정  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
비즈니스용 windows Hello를 통해 최종 사용자는 제스처 (예: PIN 또는 생체 인식)를 사용 하 여 Windows 컴퓨터에 로그인 할 수 있습니다. Azure AD 관리자는 조직의 경험을 통해 다른 Windows 로그인의 비즈니스용 Windows Hello 로그인을 암호 없는 인증으로 구분할 수 있습니다. 

이제 관리자는 Azure Portal의 Azure AD 로그인 블레이드에서 Windows 로그인 이벤트에 대 한 인증 세부 정보 탭을 확인 하 여 Windows 인증에서 비즈니스용 Windows Hello를 사용 하는지 여부를 확인할 수 있습니다. 비즈니스용 Windows Hello 인증에는 인증 방법 필드에 "WindowsHelloForBusiness"가 포함 됩니다. 로그인 로그를 해석 하는 방법에 대 한 자세한 내용은 [로그인 로그 설명서](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)를 참조 하세요.
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>그룹 삭제 동작 및 성능 향상에 대 한 수정

**유형:** 고정  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이전에는 그룹이 "범위 내"에서 "범위를 벗어났습니다."로 변경 되 고 관리자가 변경을 완료 하기 전에 다시 시작을 클릭 했을 때 그룹 개체를 삭제 하지 않았습니다. 이제 범위를 벗어나면 (사용 안 함, 삭제 됨, 할당 되지 않음 또는 범위 지정 필터를 전달 하지 않음) 그룹 개체가 대상 응용 프로그램에서 삭제 됩니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>공개 미리 보기: 이제 관리자가 액세스 검토를 만들 때 검토자에 게 전자 메일의 사용자 지정 콘텐츠를 추가할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** Id 거 버 넌 스
 
새 액세스 검토가 만들어지면 검토자는 액세스 검토를 완료 하도록 요청 하는 전자 메일을 받습니다. 대부분의 고객은 전자 메일에 사용자 지정 콘텐츠를 추가 하는 기능 (예: 연락처 정보 또는 기타 추가 지원 콘텐츠)을 요청 하 여 검토자를 안내 합니다. 

이제 공개 미리 보기로 제공 되는 관리자는 Azure AD 액세스 검토의 "고급" 섹션에서 콘텐츠를 추가 하 여 검토자에 게 보내는 전자 메일의 사용자 지정 콘텐츠를 지정할 수 있습니다. 액세스 검토를 만드는 방법에 대 한 지침은 [AZURE AD 액세스 검토에서 그룹 및 응용 프로그램에 대 한 액세스 검토 만들기](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)를 참조 하세요.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>단일 페이지 앱에 대 한 인증 코드 흐름 사용 가능

**유형:** 새로운 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 개발자 환경
 
최신 브라우저에서 Safari ITP와 같은 타사 쿠키 제한 사항 때문에, SPAs는 SSO를 유지 관리 하기 위해 암시적 흐름이 아닌 권한 부여 코드 흐름을 사용 해야 하 고 MSAL.js v 2. x는 이제 인증 코드 흐름을 지원 합니다. 

Azure Portal에 대 한 해당 업데이트가 있으므로 SPA를 "spa" 형식으로 업데이트 하 고 인증 코드 흐름을 사용할 수 있습니다. 추가 지침은 [인증 코드 흐름을 사용 하 여 사용자 로그인 및 JAVASCRIPT SPA에서 액세스 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code) 를 참조 하세요.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>이제 Azure AD 응용 프로그램 프록시 원격 데스크톱 서비스 웹 클라이언트를 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 응용 프로그램 프록시  
**제품 기능:** 액세스 제어

이제 Azure AD 응용 프로그램 프록시는 RDS (원격 데스크톱 서비스) 웹 클라이언트를 지원 합니다. 사용자는 RDS 웹 클라이언트를 사용 하 여 Microsoft Edge, Internet Explorer 11, Google Chrome 등의 HTLM5 지원 브라우저를 통해 원격 데스크톱 인프라에 액세스할 수 있습니다. 사용자는 어디서 나 로컬 장치를 사용 하는 것 처럼 원격 앱 또는 데스크톱과 상호 작용할 수 있습니다. Azure AD 응용 프로그램 프록시을 사용 하 여 모든 유형의 리치 클라이언트 앱에 대 한 사전 인증 및 조건부 액세스 정책을 적용 하 여 RDS 배포의 보안을 강화할 수 있습니다. 지침은 [Azure AD 응용 프로그램 프록시를 사용 하 여 원격 데스크톱 게시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services)를 참조 하세요.
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>공개 미리 보기로 제공 되는 다음 세대 Azure AD B2C 사용자 흐름

**유형:** 새로운 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
간소화 된 사용자 흐름 환경에는 미리 보기 기능을 제공 하는 기능 패리티가 제공 되며 새로운 모든 기능을 위한 홈입니다. 사용자는 동일한 사용자 흐름 내에서 새로운 기능을 사용 하도록 설정할 수 있으므로 모든 새 기능 릴리스에서 여러 버전을 만들 필요가 없습니다. 마지막으로, 사용자에 게 친숙 한 새 UX를 사용 하면 사용자 흐름을 간편 하 게 선택 하 고 만들 수가 있습니다. 이제 [사용자 흐름을 만들어](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)사용해 보세요. 

사용자 흐름에 대 한 자세한 내용은 [Azure Active Directory B2C 사용자 흐름 버전](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-7 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
7 월 2020에 앱 갤러리에 페더레이션 지원과 함께 다음 55 새 응용 프로그램을 추가 했습니다.

[사용자의 손을 ap](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor 자격 증명 모음](https://inexto.com/inexto-suite/inextor) [Beekast](https://my.beekast.com/), [Templafy openid connect Connect](https://app.templafy.com/), [PeterConnects 접수원](https://msteams.peterconnects.com/), a [Hac-coa](https://appfusions.alohacloud.com/auth), [Control 타워](https://bpm.tnxcorp.com/sso/microsoft), [cocoom](https://start.cocoom.com/), [동전 건설 클라우드](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial), [Wootric](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), OpenText  [Directory Services](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial), [Coite](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial), [blogin](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial), [IntSights](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial), [Kpifire](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial), [Textline](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial), [Cloud 아카데미-SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial), [Community Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial), [작업](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial), [cloudsign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial), [C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial), [SmartHR](https://smarthr.jp/), [numlyengage](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial), [Michigan Data hubs Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial), [송신](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial), [Sendsafely](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial), [Eletive](https://app.eletive.com/), [Right 사이버 보안 ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial), [Lenses.io](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial), [Momenta](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial), [uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [cloudsign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial), [TellMe 봇](https://tellme365liteweb.azurewebsites.net/), [영감](https://app.inspiresoftware.com/), [maverics id Orchestrator SAML 커넥터](https://www.strata.io/identity-fabric/), [smartschool (school Management System)](https://smart-schoolapp.com/frmLoginForm), [Zepto-Intelligent 계측](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [plan](http://www.trackplanfm.com/), [Skedda](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial) [, WhosOnLocation,](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial) [Coggle](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial), [kemp loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [browserstack Single sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다. https://aka.ms/AppsTutorial

Azure AD 앱 갤러리에 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요. https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-7 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합

이제 새로 통합 된 앱 [LinkedIn Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial)에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조하세요.

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>모든 범위에서 역할 할당을 확인 하 고 csv 파일로 다운로드할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** Azure AD 역할  
**제품 기능:** 액세스 제어
 
이제 Azure AD 포털의 "역할 및 관리자" 탭에서 역할에 대 한 모든 범위에서 역할 할당을 볼 수 있습니다. 각 역할에 대 한 역할 할당을 CSV 파일로 다운로드할 수도 있습니다. 역할 할당을 보고 추가 하는 방법에 대 한 지침은 [Azure Active Directory에서 관리자 역할 보기 및 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)을 참조 하세요.
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication 소프트웨어 개발 (Azure MFA SDK) 사용 중단

**유형:** 사용되지 않음  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 
Azure MFA SDK (Multi-Factor Authentication 소프트웨어 개발)는 11 월 2017에 처음 발표 된 2018 년 11 월 14 일에 종료 되었습니다. Microsoft는 2020 년 9 월 30 일에 적용 되는 SDK 서비스를 종료 합니다. SDK에 대 한 모든 호출이 실패 합니다.

조직에서 Azure MFA SDK를 사용 하는 경우 2020 년 9 월 30 일까 지 마이그레이션 해야 합니다.
- MIM 용 Azure MFA SDK: MIM에서 SDK를 사용 하는 경우 다음 [지침](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim)에 따라 Azure mfa 서버로 마이그레이션하고 PAM (Privileged Access Management)을 활성화 해야 합니다.   
- 사용자 지정 된 앱 용 azure MFA SDK: Azure AD에 앱을 통합 하 고 조건부 액세스를 사용 하 여 MFA를 적용 하는 것이 좋습니다. 시작 하려면이 [페이지](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)를 검토 합니다. 

---

## <a name="june-2020"></a>2020년 6월 

### <a name="user-risk-condition-in-conditional-access-policy"></a>조건부 액세스 정책의 사용자 위험 조건

**유형:** 변경 계획  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 

Azure AD 조건부 액세스 정책에서 사용자 위험 지원을 통해 여러 사용자 위험 기반 정책을 만들 수 있습니다. 서로 다른 사용자 및 앱에 대해 다른 최소 사용자 위험 수준이 필요할 수 있습니다. 사용자 위험에 따라 액세스를 차단 하는 정책을 만들거나, multi-factor authentication을 요구 하거나, 보안 암호 변경을 요구 하거나, Microsoft Cloud App Security로 리디렉션하여 추가 감사와 같은 세션 정책을 적용할 수 있습니다.

사용자 위험 조건에는 P2를 제공 하는 Azure Id 보호를 사용 하기 때문에 Azure AD Premium P2가 필요 합니다. 조건부 액세스에 대 한 자세한 내용은 [AZURE AD 조건부 액세스 설명서](https://docs.microsoft.com/azure/active-directory/conditional-access/)를 참조 하세요.

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>이제 SAML SSO는 요청 시 SPNameQualifier를 설정 해야 하는 앱을 지원 합니다.

**유형:** 고정  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
일부 SAML 응용 프로그램의 경우 요청 시 어설션 주체가 SPNameQualifier을 반환 해야 합니다. 이제 SPNameQualifier이 요청 NameID 정책에서 요청 되 면 Azure AD가 올바르게 응답 합니다. SP가 시작한 로그인에도 적용 되 고 IdP 시작 된 로그인이 수행 됩니다.  Azure Active Directory에서 SAML 프로토콜에 대해 자세히 알아보려면 [Single SIGN-ON saml 프로토콜](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)을 참조 하세요.

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 공동 작업은 Azure Government 테 넌 트의 MSA 및 Google 사용자 초대를 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

B2B 공동 작업 기능을 사용 하는 테 넌 트가 이제 Microsoft 또는 Google 계정이 있는 사용자를 초대할 수 있습니다. Azure Government 테 넌 트가 이러한 기능을 사용할 수 있는지 확인 하려면 [AZURE 미국 정부 테 넌 트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있나요?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant) 의 지침을 따르세요.

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>이제 MS Graph v1의 사용자 개체에 externalUserState 및 externalUserStateChangedDateTime 속성이 포함 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 

ExternalUserState 및 externalUserStateChangedDateTime 속성을 사용 하 여 초대를 수락 하지 않은 초대 된 B2B 게스트 뿐만 아니라 며칠 후에 초대를 수락 하지 않은 사용자를 삭제 하는 등의 빌드 자동화를 찾을 수 있습니다. 이제 MS Graph v1에서 이러한 속성을 사용할 수 있습니다. 이러한 속성을 사용 하는 방법에 대 한 지침은 [사용자 리소스 종류](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)를 참조 하세요.
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Azure AD에서 인증 세션 관리 조건부 액세스가 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호
 
인증 세션 관리 기능을 사용 하면 사용자가 로그인 자격 증명을 제공 해야 하는 빈도 및 사용자 환경에서 더 많은 보안과 유연성을 제공 하기 위해 브라우저를 닫았다가 다시 연 후 자격 증명을 제공 해야 하는지 여부를 구성할 수 있습니다.
 
또한 Azure AD 조인, 하이브리드 Azure AD 조인 및 Azure AD 등록 장치에 대 한 첫 번째 단계 인증에만 적용 되는 인증 세션 관리가 사용 됩니다. 이제 인증 세션 관리가 MFA에도 적용 됩니다. 자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-6 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
6 월 2020에 앱 갤러리에 페더레이션 지원이 포함 된 다음 29 개의 새로운 응용 프로그램이 추가 되었습니다.

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [mailgates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [부문](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [Ai 감사자](https://www.mindbridge.ai/products/ai-auditor/), [popoco Ca Connect](https://app.pobu.ca/), [Proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [게이트 키퍼](https://www.gatekeeperhq.com/), [Hub 플래너](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [ansira-파트너 이동 도구 상자](https://ansira.com/technology/channel-engagement), [클라우드에서 IBM Digital Business Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [Kisi 물리적 보안](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [viewpointone](https://team.viewpoint.com/), [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [Zero](https://www.teamzero.com/), [ation](https://instation.invillia.com/), [edX FOR Business SAML 2.0 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [mooc Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning platform](https://platform.pkisigning.nl/), [siteintel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [Field iD](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), [과정 SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [perforce Helix Core-Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), [mycompliance 클라우드](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다 https://aka.ms/AppsTutorial . Azure AD 앱 갤러리에서 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요 https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>외부 Id에 대 한 API 커넥터 셀프 서비스 등록은 현재 공개 미리 보기로 제공 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
외부 Id API 커넥터를 사용 하면 웹 Api를 활용 하 여 셀프 서비스 등록을 외부 클라우드 시스템과 통합할 수 있습니다. 즉, 이제 웹 Api를 등록 흐름에서 특정 단계로 호출 하 여 클라우드 기반 사용자 지정 워크플로를 트리거할 수 있습니다. 예를 들어 API 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- 사용자 지정 승인 워크플로와 통합 합니다.
- Id 교정 수행
- 사용자 입력 데이터 유효성 검사
- 사용자 특성 덮어쓰기
- 사용자 지정 비즈니스 논리 실행

API 커넥터에서 사용할 수 있는 모든 환경에 대 한 자세한 내용은 [api 커넥터를 사용 하 여 셀프 서비스 등록 사용자 지정 및 확장](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)또는 [웹 api 통합을 사용 하 여 외부 id 셀프 서비스 등록 사용자 지정](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)을 참조 하세요.
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>요청 시 프로 비전 하 고 몇 초 내에 사용자 앱에 사용자 가져오기

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 현재 순환 방식으로 작동 합니다. 서비스는 40 분 마다 실행 됩니다. [주문형 프로 비전 기능](https://aka.ms/provisionondemand) 을 사용 하면 사용자를 선택 하 고 몇 초만에 프로 비전 할 수 있습니다. 이 기능을 사용 하면 다시 시작을 수행 하 여 프로 비전 주기를 다시 시작 하지 않고도 프로 비전 문제를 신속 하 게 해결할 수 있습니다. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Graph에서 Azure AD 자격 관리를 사용 하기 위한 새 권한

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리
 
새 위임 된 권한 EntitlementManagement는 이제 Microsoft Graph beta의 자격 관리 API에서 사용할 수 있습니다. 사용 가능한 Api에 대해 자세히 알아보려면 [AZURE AD 자격 관리 Api 작업](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)을 참조 하세요.

---

### <a name="identity-protection-apis-available-in-v10"></a>V 1.0에서 사용할 수 있는 id 보호 Api

**유형:** 새로운 기능  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호
 
RiskyUsers 및 riskDetections Microsoft Graph Api는 이제 일반 공급 됩니다. 이제 v2.0 끝점에서 사용할 수 있으므로 프로덕션 환경에서 사용할 수 있도록 초대 합니다. 자세한 내용은 [Microsoft Graph 문서](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)를 확인 하세요.
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Microsoft 365 그룹에 정책을 적용 하기 위한 민감도 레이블 이제 일반 공급

**유형:** 새로운 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업
 

이제 민감도 레이블을 만들고 레이블 설정을 사용 하 여 개인 정보 (공용 또는 개인) 및 외부 사용자 액세스 정책을 포함 하 여 Microsoft 365 그룹에 정책을 적용할 수 있습니다. 개인 정보 취급 방침을 포함 하는 레이블을 만들고, 게스트 사용자를 추가 하는 것을 허용 하지 않는 외부 사용자 액세스 정책을 만들 수 있습니다. 사용자가이 레이블을 그룹에 적용 하는 경우 그룹은 비공개 이며 게스트 사용자를 그룹에 추가할 수 없습니다. 

민감도 레이블은 비즈니스에 중요 한 데이터를 보호 하 고 규정을 준수 하 고 안전한 방식으로 대규모 그룹을 관리할 수 있도록 하는 데 중요 합니다. 민감도 레이블 사용에 대 한 지침은 [Azure Active Directory (미리 보기)에서 Office 365 그룹에 민감도 레이블 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels)을 참조 하세요.
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Azure AD Premium 고객에 대 한 Microsoft Identity Manager 지원 업데이트

**유형:** 변경된 기능  
**서비스 범주:** Microsoft Identity Manager  
**제품 기능:** Id 수명 주기 관리
 
이제 Microsoft Identity Manager 2016의 Azure AD 통합 구성 요소에 대 한 azure 지원이 Microsoft Identity Manager 2016에 대 한 연장 지원 종료를 통해 제공 됩니다. [Microsoft Identity Manager를 사용 하는 Azure AD Premium 고객에 대 한 지원 업데이트](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)를 참조 하세요.

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>SSO 클레임 구성에서 그룹 멤버 자격 조건의 사용이 증가 합니다.

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이전에는 단일 응용 프로그램 구성 내의 그룹 멤버 자격을 기준으로 클레임을 조건부로 변경할 때 사용할 수 있는 그룹의 수가 10 개로 제한 되었습니다. SSO 클레임 구성에서 그룹 멤버 자격 조건의 사용이 이제 최대 50 그룹으로 늘어났습니다. 클레임을 구성 하는 방법에 대 한 자세한 내용은 [엔터프라이즈 응용 프로그램 SSO 클레임 구성](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions)을 참조 하세요. 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>회사 브랜딩의 로그인 페이지 텍스트 구성 요소에서 기본 서식 지정을 사용 하도록 설정 합니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
Azure AD/Microsoft 365 로그인 환경의 회사 브랜딩 기능이 업데이트 되어 고객이 굵은 글꼴, 밑줄, 기울임꼴 등의 간단한 서식 지정 및 하이퍼링크를 추가할 수 있습니다. 이 기능을 사용 하는 방법에 대 한 지침은 [조직의 Azure Active Directory 로그인 페이지에 브랜딩 추가](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)를 참조 하세요.

---

### <a name="provisioning-performance-improvements"></a>프로 비전 성능 향상

**유형:** 변경된 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
프로 비전 서비스가 [증분 주기가](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) 완료 될 때까지 걸리는 시간을 줄이기 위해 업데이트 되었습니다. 즉, 사용자와 그룹이 이전 보다 더 빠르게 응용 프로그램에 프로 비전 됩니다. 6/10/2020 이후에 생성 되는 모든 새로운 프로 비전 작업은 성능 향상을 통해 자동으로 향상 됩니다. 6/10/2020 이전의 프로 비전을 위해 구성 된 모든 응용 프로그램은 성능 개선을 활용 하기 위해 6/10/2020 후에 한 번 다시 시작 해야 합니다. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>ADAL 및 MS Graph 패리티 사용 중단 발표

**유형:** 사용되지 않음  
**서비스 범주:** 해당 없음  
**제품 기능:** 장치 수명 주기 관리

이제 MSAL (Microsoft 인증 라이브러리)을 사용할 수 있기 때문에 더 이상 ADAL (Azure Active Directory Authentication library)에 새 기능을 추가 하지 않으며 2022 년 6 월 30 일에 보안 패치를 종료 합니다. MSAL으로 마이그레이션하는 방법에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리)로 응용 프로그램 마이그레이션](https://docs.microsoft.com/azure/active-directory/develop/msal-migration)을 참조 하세요.

또한 MS Graph를 통해 모든 Azure AD Graph 기능을 사용할 수 있도록 하는 작업을 완료 했습니다. 따라서 Azure AD Graph Api는 2022 년 6 월 30 일까 서 수정 및 보안 픽스만 받습니다. 자세한 내용은 [Microsoft 인증 라이브러리를 사용 하도록 응용 프로그램 업데이트 및 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) 를 참조 하세요.
 
---
 
## <a name="may-2020"></a>2020년 5월

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>SignIns, riskyUsers 및 riskDetections Api에서 속성 사용 중지

**유형:** 변경 계획  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호

현재 열거 형식은 riskDetections API와 riskyUserHistoryItem (미리 보기)에서 riskType 속성을 나타내는 데 사용 됩니다. 또한 signIns API의 riskEventTypes 속성에도 열거 형식이 사용 됩니다. 앞으로 이러한 속성을 문자열로 나타냅니다. 

고객은 beta riskDetections 및 riskyUserHistoryItem API에서 riskEventType 속성으로 전환 하 고, 베타 signIns API에서 2020 9 월 9 일까지 속성을 riskEventTypes_v2 해야 합니다. 이 날짜에 현재 riskType 및 riskEventTypes 속성을 사용 하지 않을 것입니다. 자세한 내용은 [Microsoft Graph에서 위험 이벤트 속성 및 Id 보호 api](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)에 대 한 변경 내용을 참조 하세요.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph의 signIns v1.0 API에서 riskEventTypes 속성의 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** Id 보안 & 보호

2020 9 월 Microsoft Graph에서 위험 이벤트 속성을 나타내는 경우 열거 된 형식이 문자열 형식으로 전환 됩니다. 미리 보기 Api에 영향을 주는 것 외에도이 변경 내용은 프로덕션 내 서명 API에도 영향을 줍니다.

새 riskEventsTypes_v2 (문자열) 속성이 signIns v1.0 API에 도입 되었습니다. Microsoft Graph 사용 중단 정책에 따라 2022 년 6 월 11 일에 현재 riskEventTypes (enum) 속성을 사용 중지 합니다. 고객은 2022 년 6 월 11 일에 해당 하는 v 1.0 signIns API의 riskEventTypes_v2 속성으로 전환 해야 합니다. 자세한 내용은 [Microsoft Graph에서 signIns의 riskEventTypes 속성의](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)사용 중단을 참조 하세요.

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA 전자 메일 알림의 예정 된 변경 내용

**유형:** 변경 계획  
**서비스 범주:** MFA  
**제품 기능:** Id 보안 & 보호
 

Cloud MFA에 대 한 전자 메일 알림을 다음과 같이 변경 합니다.

전자 메일 알림은 및 주소에서 전송 됩니다. azure-noreply@microsoft.com msonlineservicesteam@microsoftonline.com 사기 행위 경고 전자 메일의 콘텐츠를 업데이트 하 여 사용 차단을 해제 하는 데 필요한 단계를 더 잘 나타낼 수 있습니다.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Microsoft 팀에 액세스할 수 없는 페더레이션 도메인의 사용자에 대 한 새 셀프 서비스 등록은 Azure Active Directory에 동기화 되지 않기 때문입니다.

**유형:** 변경 계획  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 

현재 Azure AD에 페더레이션된 도메인에 있지만 테 넌 트에 동기화 되지 않은 사용자는 팀에 액세스할 수 없습니다. 6 월 말부터이 새로운 기능을 사용 하면 기존 전자 메일 확인 등록 기능을 확장 하 여이 작업을 수행할 수 있습니다. 그러면 페더레이션 IdP에 로그인 할 수 있지만 Azure ID에 사용자 개체가 아직 없는 사용자 개체를 자동으로 만들고 팀에 대해 인증할 수 있는 사용자를 사용할 수 있습니다. 해당 사용자 개체는 "셀프 서비스 등록"으로 표시 됩니다. 이는 관리 되는 도메인의 사용자가 수행할 수 있고 동일한 플래그를 사용 하 여 제어할 수 있는 전자 메일을 확인 하는 기존 기능을 확장 한 것입니다. 이 변경 내용은 다음 두 달 동안 롤아웃 완료 됩니다. [여기](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)에서 설명서 업데이트를 시청 하세요.
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>예정 된 해결 방법: Azure Government 클라우드에 대 한 OIDC 검색 문서가 올바른 그래프 끝점을 참조 하도록 업데이트 되 고 있습니다.

**유형:** 변경 계획  
**서비스 범주:** 소버린 클라우드  
**제품 기능:** 사용자 인증
 
6 월부터 OIDC 검색 문서 [Microsoft identity platform And Openid connect Connect protocol](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) for the login.microsoftonline.us ( [클라우드](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) 끝점) Azure Government에서 제공 된 테 넌 트에 따라 올바른 [국가 클라우드 그래프](https://docs.microsoft.com/graph/deployments) 끝점 (또는)을 반환 하기 시작 합니다 https://graph.microsoft.us https://dod-graph.microsoft.us) .  현재는 잘못 된 그래프 끝점 (graph.microsoft.com) "msgraph_host" 필드를 제공 합니다.  

이 버그 수정은 약 2 개월 동안 점진적으로 출시 될 예정입니다.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government 사용자가 login.microsoftonline.com에서 더 이상 로그인 할 수 없습니다.

**유형:** 변경 계획  
**서비스 범주:** 소버린 클라우드  
**제품 기능:** 사용자 인증
 
2018 6 월 1 일에 Azure Government의 AAD (공식 Azure Active Directory) 기관이에서로 변경 되었습니다 https://login-us.microsoftonline.com https://login.microsoftonline.us . Azure Government 테 넌 트 내에서 응용 프로그램을 소유 하 고 있는 경우 us 끝점에서 사용자에 게 로그인 하도록 응용 프로그램을 업데이트 해야 합니다.

5 월 5 일부터 Azure AD는 사용자가 공용 끝점 (microsoftonline.com)을 사용 하 여 Azure Government 테 넌 트에서 호스트 되는 앱에 로그인 하지 못하도록 Azure Government 차단 하는 끝점 변경을 적용 하기 시작 합니다. 영향을 받는 앱이 AADSTS900439 오류를 표시 하기 시작 합니다. 

모든 앱에서 6 월 2020에 완료 될 것으로 예상 되는 적용으로 이러한 변경의 점진적 출시가 발생 합니다. 자세한 내용은 [Azure Government 블로그 게시물](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)을 참조 하세요.

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>이제 SAML 단일 로그 아웃 요청이 올바른 형식으로 NameID를 전송 합니다.

**유형:** 고정  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
사용자가 로그 아웃 (예: MyApps 포털)을 클릭 하면 Azure AD는 사용자 세션에서 활성 상태이 고 로그 아웃 URL이 구성 된 각 앱에 SAML 단일 로그 아웃 메시지를 보냅니다. 이러한 메시지는 영구 형식의 NameID를 포함 합니다.

원본 SAML 로그인 토큰이 NameID에 대해 다른 형식 (예: 메일/m s)을 사용 하는 경우 SAML 앱은 로그 아웃 메시지의 NameID와 기존 세션의 상관 관계를 지정할 수 없습니다 .이로 인해 SAML 앱 및 사용자가 로그인 상태를 유지 하기 위해 로그 아웃 메시지를 삭제 하 게 됩니다. 이 수정은 응용 프로그램에 대해 구성 된 NameID와의 로그 아웃 메시지 일치를 수행 합니다.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>이제 클라우드 프로 비전에서 하이브리드 Id 관리자 역할을 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** Azure AD 클라우드 프로 비전  
**제품 기능:** Id 수명 주기 관리
 
IT 관리자는 Azure ADConnect 클라우드 프로 비전을 설정 하는 데 필요한 최소 권한 역할로 새로운 "하이브리드 관리자" 역할을 사용 하 여 시작할 수 있습니다. 이 새로운 역할을 사용 하는 경우 더 이상 전역 관리자 역할을 사용 하 여 클라우드 프로 비전을 설정 하 고 구성할 필요가 없습니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 응용 프로그램 갤러리에서 사용할 수 있는 새로운 페더레이션된 앱-2020 년 5 월

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2020 년 5 월에 앱 갤러리에 페더레이션 지원이 포함 된 다음 36 새 응용 프로그램이 추가 되었습니다.

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell 팀](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC 기록 정보](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B 개입](https://b-engaged.se/), [Competella Contact Center WORKGROUP](http://www.competella.com/), [asite](http://www.asite.com/), ImageSoft [Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX 보증 Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [스마트 글로벌 거 버 넌 스](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), Whimsical, Trelica, [Whimsical](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Confluence](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial) [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii, Axiad,](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [Humanage Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial), [Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial), [colortokens ztna](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial), [cch tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial), [sharevault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial), [Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial), [textexpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial), [누구나 Home CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial), [askspoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial), [ice 센터](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

여기에서 모든 응용 프로그램의 설명서를 찾을 수도 있습니다 https://aka.ms/AppsTutorial .

Azure AD 앱 갤러리에 응용 프로그램을 나열 하려면 여기에서 세부 정보를 참조 하세요 https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>조건부 액세스에 대 한 보고서 전용 모드가 이제 일반 공급 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

[AZURE AD 조건부 액세스에 대 한 보고서 전용 모드](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) 를 통해 액세스 제어를 적용 하지 않고 정책의 결과를 평가할 수 있습니다. 조직 전체에서 보고서 전용 정책을 테스트 하 고 사용 하도록 설정 하기 전에 영향을 파악 하 여 배포를 더 안전 하 고 쉽게 만들 수 있습니다. 지난 몇 달 동안 보고서 전용 모드를 강력 하 게 채택 했습니다. 26M 사용자는 이미 보고서 전용 정책의 범위에 있습니다. 오늘 공지를 사용 하면 기본적으로 새 Azure AD 조건부 액세스 정책이 보고서 전용 모드에서 생성 됩니다. 즉, 정책을 만든 순간부터 정책의 영향을 모니터링할 수 있습니다. MS Graph Api를 사용 하는 사용자의 경우에도 [프로그래밍 방식으로 보고서 전용 정책을 관리할](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) 수 있습니다. 

---

### <a name="self-service-sign-up-for-guest-users"></a>게스트 사용자에 대 한 셀프 서비스 등록

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
Azure AD의 외부 Id를 사용 하 여 조직 외부 사용자가 앱과 리소스에 액세스 하도록 허용 하는 동시에 원하는 모든 id를 사용 하 여 로그인 할 수 있습니다. 외부 사용자와 애플리케이션을 공유하는 경우 애플리케이션에 액세스해야 하는 사용자를 항상 미리 알 수 있는 것은 아닙니다. [셀프 서비스 등록](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)을 사용 하면 게스트 사용자가 LOB (기간 업무) 앱에 등록 하 고 게스트 계정을 확보할 수 있습니다. 등록 흐름을 만들고 사용자 지정 하 여 Azure AD 및 소셜 id를 지원할 수 있습니다. 등록 하는 동안 사용자에 대 한 추가 정보를 수집할 수도 있습니다.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>조건부 액세스 정보 및 보고 통합 문서를 일반적으로 사용할 수 있음

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

[Insights 및 보고 통합 문서](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) 는 관리자에 게 테 넌 트의 Azure AD 조건부 액세스에 대 한 요약 보기를 제공 합니다. 관리자는 개별 정책을 선택 하는 기능을 사용 하 여 각 정책에서 수행 하는 작업을 더 잘 이해 하 고 실시간으로 변화를 모니터링할 수 있습니다. 통합 문서는 Azure Monitor에 저장 된 데이터를 스트리밍하 며 몇 분 내에 [이러한 지침에 따라](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)설정할 수 있습니다. 대시보드를 더 쉽게 검색할 수 있도록 Azure AD 조건부 액세스 메뉴 내의 새 정보 및 보고 탭으로 이동 했습니다.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>조건부 액세스에 대 한 정책 세부 정보 블레이드가 공개 미리 보기 상태입니다.

**유형:** 새로운 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** Id 보안 & 보호

새 [정책 세부 정보 블레이드에](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) 는 조건부 액세스 정책을 평가 하는 동안 충족 된 할당, 조건 및 컨트롤이 표시 됩니다. 로그인 정보의 조건부 액세스 또는 보고서 전용 탭에서 행을 선택 하 여 블레이드에 액세스할 수 있습니다.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph의 디렉터리 개체에 대 한 새로운 쿼리 기능은 공개 미리 보기로 제공 됩니다.

**유형:** 새로운 기능  
**서비스 범주:** MS Graph **제품 기능:** 개발자 환경

Microsoft Graph Directory 개체 Api에 대 한 새로운 기능이 도입 되어 개수, 검색, 필터 및 정렬 작업을 가능 하 게 합니다. 이를 통해 개발자는 메모리 내 필터링 및 정렬과 같은 해결 방법 없이 디렉터리 개체를 신속 하 게 쿼리할 수 있습니다. 이 [블로그 게시물](https://aka.ms/CountFilterMSGraphAAD)에서 자세히 알아보세요.

현재 공개 미리 보기 상태 이며 피드백을 찾고 있습니다. 이 [간단한 설문 조사](https://aka.ms/MsGraphAADSurveyDocs)를 통해 의견을 보내 주시기 바랍니다.

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Microsoft Graph API (베타)를 사용 하 여 SAML 기반 Single Sign-On 구성

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이제 베타에서 MS Graph Api를 사용 하 여 Azure AD 갤러리에서 응용 프로그램을 만들고 구성 하는 기능을 사용할 수 있습니다. 응용 프로그램의 여러 인스턴스에 대해 SAML 기반 Single Sign-On를 설정 해야 하는 경우 Microsoft Graph Api를 사용 하 여 [saml 기반 Single Sign-On의 구성을 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api)하는 시간을 절약 합니다.
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-2020 년 5 월

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** 타사 통합
 
다음과 같은 통합된 새 앱에 대한 사용자 계정을 만들고, 업데이트하고, 삭제하는 작업을 자동화할 수 있습니다.

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [조직별 새 Relic](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

자동화된 사용자 계정 프로비저닝을 사용하여 조직의 보안을 강화하는 방법에 대한 자세한 내용은 [Azure AD를 사용하여 SaaS 애플리케이션에 대한 사용자 프로비저닝 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조하세요.

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 토큰 암호화를 일반적으로 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
[Saml 토큰 암호화](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) 를 사용 하면 암호화 된 saml 어설션을 받도록 응용 프로그램을 구성할 수 있습니다. 이 기능은 이제 모든 클라우드에서 일반적으로 사용할 수 있습니다.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>응용 프로그램 토큰의 그룹 이름 클레임이 일반적으로 사용 가능 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO
 
이제 토큰에서 발급 된 그룹 클레임을 응용 프로그램에 할당 된 그룹 으로만 제한할 수 있습니다.  이는 사용자가 많은 그룹의 구성원이 고 토큰 크기 제한을 초과할 위험이 있는 경우에 특히 중요 합니다. 이 새로운 기능을 사용 하는 경우 [토큰에 그룹 이름을 추가](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) 하는 기능이 일반적으로 제공 됩니다.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>이제 Workday 쓰기 저장에서 회사 전화 번호 특성 설정을 지원 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
이제 Workday 쓰기 저장 프로 비전 앱이 회사 전화 번호 및 모바일 번호 특성의 쓰기 저장을 지원 하도록 향상 되었습니다. 전자 메일 및 사용자 이름 외에도 이제 Workday 쓰기 저장 프로 비전 앱을 Azure AD에서 Workday로 이동 하는 전화 번호 값을 구성할 수 있습니다. 전화 번호 쓰기 저장을 구성 하는 방법에 대 한 자세한 내용은 [Workday 쓰기 저장](https://aka.ms/WorkdayWriteback) 앱 자습서를 참조 하세요. 

---

### <a name="publisher-verification-preview"></a>게시자 확인 (미리 보기)

**유형:** 새로운 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경
 
게시자 확인 (미리 보기)은 관리자와 최종 사용자가 Microsoft id 플랫폼과 통합 되는 응용 프로그램 개발자의 신뢰성을 이해 하는 데 도움이 됩니다. 자세한 내용은 [게시자 확인 (미리 보기)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview)을 참조 하세요.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>단일 페이지 앱에 대 한 인증 코드 흐름

**유형:** 변경 된 기능 **서비스 범주:** 인증 **제품 기능:** 개발자 환경

최신 브라우저에서 [Safari ITP와 같은 타사 쿠키 제한](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas)때문에, SPAS는 SSO를 유지 하기 위해 암시적 흐름이 아닌 권한 부여 코드 흐름을 사용 해야 합니다. MSAL.js v 2. x는 이제 인증 코드 흐름을 지원 합니다. Azure Portal에 대 한 해당 업데이트가 있으므로 SPA를 "spa" 형식으로 업데이트 하 고 인증 코드 흐름을 사용할 수 있습니다. 지침은 [빠른 시작: 인증 사용자 로그인 및 인증 코드 흐름을 사용 하 여 JAVASCRIPT SPA에서 액세스 토큰 가져오기](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)를 참조 하세요.

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>공개 미리 보기로 제공 되는 장치에 대 한 필터링 향상

**유형:** 변경 된 기능   
**서비스 범주:** 장치 관리 **제품 기능:** 장치 수명 주기 관리
 
이전에는 사용할 수 있는 유일한 필터는 "사용" 및 "작업 날짜"입니다. 이제 OS 유형, 조인 유형, 규정 준수 등을 비롯 한 [더 많은 속성을 통해 장치 목록을 필터링](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview)할 수 있습니다. 이러한 추가 기능을 통해 특정 장치를 쉽게 찾을 것입니다.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>이제 Azure AD B2C의 새로운 앱 등록 환경이 출시 되었습니다.

**유형:** 변경 된 기능   
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** Id 수명 주기 관리
 
이제 Azure AD B2C에 대 한 새로운 앱 등록 환경이 출시 되었습니다. 

이전에는 레거시 ' 응용 프로그램 ' 환경을 사용 하 여 응용 프로그램의 나머지 부분과 별도로 B2C 소비자 지향 응용 프로그램을 관리 해야 했습니다. Azure의 여러 위치에서 다양 한 앱 만들기 환경을 의미 합니다.

새 환경에서는 모든 B2C 앱 등록 및 Azure AD 앱 등록을 한 곳에 표시 하 고 일관 된 방식으로 관리 하는 방법을 제공 합니다. Azure AD B2C 리소스를 프로그래밍 방식으로 관리 하기 위해 Microsoft Graph에 대 한 액세스 권한이 있는 앱 또는 고객 관련 앱을 관리 해야 하는지 여부에 관계 없이 작업을 수행 하는 한 가지 방법만 배워야 합니다.

Azure AD B2C 서비스를 탐색 하 고 앱 등록 블레이드를 선택 하 여 새 환경에 연결할 수 있습니다. 환경에도 Azure Active Directory 서비스에서 액세스할 수 있습니다.

Azure AD B2C 앱 등록 환경은 Azure AD 테 넌 트에 대 한 일반 [앱 등록 환경을](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 기반으로 하지만 Azure AD B2C에 맞게 조정 됩니다. 레거시 "응용 프로그램" 환경은 향후 더 이상 사용 되지 않습니다.

자세한 내용은 [Azure AD B2C에 대 한 새로운 앱 등록 환경](https://aka.ms/b2cappregtraining)을 참조 하세요.

---

## <a name="april-2020"></a>2020년 4월

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>결합 된 보안 정보 등록 환경이 이제 일반 공급 됩니다.

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** Id 보안 & 보호

Multi-Factor Authentication (MFA) 및 셀프 서비스 암호 재설정 (SSPR)에 대 한 결합 된 등록 환경이 이제 일반 공급 됩니다. 이 새로운 등록 환경을 통해 사용자는 단일 단계별 프로세스에서 MFA 및 SSPR에 등록할 수 있습니다. 조직의 새 환경을 배포 하는 경우 사용자는 더 짧은 시간에 더 많은 작업을 등록할 수 있습니다. 블로그 [게시물을 확인 하세요.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>연속 액세스 평가

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** Id 보안 & 보호

연속 액세스 평가는 Azure AD에서 이벤트가 발생 하는 경우 (예: 사용자 계정 삭제) Azure AD 액세스 토큰을 사용 하는 신뢰 당사자에 대해 거의 실시간으로 정책을 적용할 수 있는 새로운 보안 기능입니다. 팀과 Outlook 클라이언트를 위해이 기능을 먼저 롤링 합니다. 자세한 내용은 [블로그](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) 및  [설명서](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)를 참조 하세요.

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS 로그인: Firstline Worker는 전화 번호를 사용 하 고 암호 없이 Azure AD 지원 응용 프로그램에 로그인 할 수 있습니다.

**유형:** 새로운 기능

**서비스 범주:** 인증 (로그인)

**제품 기능:** 사용자 인증

Office는 기존 이외의 조직에서 사용할 수 있는 일련의 모바일 중심 비즈니스 앱과 주 통신 방법으로 전자 메일을 사용 하지 않는 대기업의 직원을 시작 합니다. 이러한 앱은 frontline 직원, deskless 작업자, 현장 에이전트 또는 소매점에서 전자 메일 주소를 얻지 못할 수도 있고, 컴퓨터에 대 한 액세스 권한이 있거나, 소매 직원을 대상으로 합니다. 이 프로젝트는 전화 번호를 입력 하 고 코드를 왕복 하 여 이러한 직원이 비즈니스 응용 프로그램에 로그인 할 수 있도록 합니다. 자세한 내용은 [관리자 설명서](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) 및 [최종 사용자 설명서](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)를 참조 하세요.

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>내부 사용자에 게 B2B 공동 작업을 사용 하도록 초대

**유형:** 새로운 기능

**서비스 범주:** B2B

**제품 기능:**

Microsoft는 향후 b2b 공동 작업 자격 증명을 사용 하도록 기존 내부 계정을 초대할 수 있도록 B2B 초대 기능을 확장 하 고 있습니다. 초대 된 전자 메일 주소와 같은 일반적인 매개 변수와 함께 사용자 개체를 초대 API에 전달 하 여이 작업을 수행 합니다. 사용자의 개체 ID, UPN, 그룹 멤버 자격, 앱 할당 등은 그대로 유지 되지만 앞으로는 B2B를 사용 하 여 초대 전에 사용한 내부 자격 증명이 아닌 해당 홈 테 넌 트 자격 증명을 사용 하 여 인증 합니다. 자세한 내용은 [설명서](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)를 참조 하세요.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>조건부 액세스에 대 한 보고서 전용 모드가 이제 일반 공급 됩니다.

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

[AZURE AD 조건부 액세스에 대 한 보고서 전용 모드](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) 를 통해 액세스 제어를 적용 하지 않고 정책의 결과를 평가할 수 있습니다. 조직 전체에서 보고서 전용 정책을 테스트 하 고 사용 하도록 설정 하기 전에 영향을 파악 하 여 배포를 더 안전 하 고 쉽게 만들 수 있습니다. 지난 몇 달 동안 보고서 전용 정책의 범위에 이미 있는 26M 사용자를 포함 하 여 보고서 전용 모드를 강력 하 게 채택 했습니다. 이 공지를 사용 하면 기본적으로 새 Azure AD 조건부 액세스 정책이 보고서 전용 모드에서 생성 됩니다. 즉, 정책을 만든 순간부터 정책의 영향을 모니터링할 수 있습니다. MS Graph Api를 사용 하는 사용자의 경우에는 [프로그래밍 방식으로 보고서 전용 정책을 관리할](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)수도 있습니다. 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>조건부 액세스 정보 및 보고 통합 문서를 일반적으로 사용할 수 있음

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

조건부 액세스 [정보 및 보고 통합 문서](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) 는 관리자에 게 테 넌 트의 Azure AD 조건부 액세스에 대 한 요약 보기를 제공 합니다. 관리자는 개별 정책을 선택 하는 기능을 사용 하 여 각 정책에서 수행 하는 작업을 더 잘 이해 하 고 실시간으로 변화를 모니터링할 수 있습니다. 통합 문서는 Azure Monitor에 저장 된 데이터를 스트리밍하 며 몇 분 내에 [이러한 지침에 따라](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)설정할 수 있습니다. 대시보드를 더 쉽게 검색할 수 있도록 Azure AD 조건부 액세스 메뉴 내의 새 정보 및 보고 탭으로 이동 했습니다.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>조건부 액세스에 대 한 정책 세부 정보 블레이드가 공개 미리 보기 상태입니다.

**유형:** 새로운 기능

**서비스 범주:** 조건부 액세스

**제품 기능:** Id 보안 & 보호

새 [정책 세부 정보 블레이드](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) 는 조건부 액세스 정책 평가 중에 충족 된 할당, 조건 및 컨트롤을 표시 합니다. 로그인 정보의 **조건부 액세스** 또는 **보고서 전용** 탭에서 행을 선택 하 여 블레이드에 액세스할 수 있습니다.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-4 월 2020

**유형:** 새로운 기능

**서비스 범주:** 엔터프라이즈 앱

**제품 기능:** 타사 통합

4 월 2020에 앱 갤러리에 대 한 페더레이션 지원과 함께 이러한 31 개의 새 앱을 추가 했습니다. 

[SincroPool Apps](https://www.sincropool.com/), [smartdb](https://hibiki.dreamarts.co.jp/smartdb/trial/), [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [iwt-9j-u8d 조달 Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/),, [Jira에 대 한](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial) [가상 교육 아카데미](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki 대시보드](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365](https://app.mover.io/login)이동 기 [, 발표자 참여](https://speakerengage.com/login.php), [솔직히](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [동맹](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [alertmedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pendo](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [highground](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [조화](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling 솔루션](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet 클릭](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [역량](https://www.made-in-office.com/en/), [fortes 자료 변경 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [groupcoa](https://recorder.grouptalk.com/), Frontify, [MongoDB cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS 배우기](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [nitro 생산성 도구](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [(Trend 마이크로 웹 보안)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>공개 미리 보기에 사용할 수 있는 oAuth2PermissionGrant에 대 한 델타 쿼리 지원 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

OAuth2PermissionGrant에 대 한 델타 쿼리는 공개 미리 보기에서 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링할 필요 없이 변경 내용을 추적할 수 있습니다. [자세한 정보](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>조직 연락처에 대 한 Microsoft Graph 델타 쿼리 지원 일반 공급

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

조직 연락처에 대 한 델타 쿼리를 일반적으로 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링 하지 않고도 프로덕션 앱에서 변경 내용을 추적할 수 있습니다. OrgContact 데이터를 지속적으로 폴링하는 기존 코드를 델타 쿼리로 대체 하 여 성능을 크게 향상 시킵니다. [자세한 정보](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>응용 프로그램에 대 한 델타 쿼리 지원이 일반 공급 되는 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

응용 프로그램에 대 한 델타 쿼리를 일반적으로 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링 하지 않고도 프로덕션 앱에서 변경 내용을 추적할 수 있습니다. 응용 프로그램 데이터를 지속적으로 폴링하는 기존 코드를 델타 쿼리로 대체 하 여 성능을 크게 향상 시킵니다. [자세한 정보](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>공개 미리 보기에 사용할 수 있는 관리 단위에 대 한 델타 쿼리 지원 Microsoft Graph

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 관리 단위에 대 한 개발자 환경 델타 쿼리는 공개 미리 보기에서 사용할 수 있습니다. 이제 Microsoft Graph를 지속적으로 폴링할 필요 없이 변경 내용을 추적할 수 있습니다. [자세한 정보](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>새 Microsoft Graph 베타 Api에서 인증 전화 번호 등을 관리 합니다.

**유형:** 새로운 기능

**서비스 범주:** MS 그래프

**제품 기능:** 개발자 환경

이러한 Api는 사용자의 인증 방법을 관리 하기 위한 핵심 도구입니다. 이제 MFA 및 SSPR (셀프 서비스 암호 재설정)에 사용 되는 인증자를 프로그래밍 방식으로 미리 등록 하 고 관리할 수 있습니다. 이는 Azure MFA, SSPR 및 Microsoft Graph 공간에서 가장 많이 요청 되는 기능 중 하나입니다. 이 wave에서 릴리스된 새 Api는 다음을 수행할 수 있는 기능을 제공 합니다.

- 사용자의 인증 전화를 읽고, 추가 하 고, 업데이트 하 고, 제거 합니다.
- 사용자의 암호 다시 설정
- SMS 로그인 설정 및 해제

자세한 내용은 [AZURE AD 인증 방법 API 개요](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)를 참조 하세요.

---

### <a name="administrative-units-public-preview"></a>관리 단위 공개 미리 보기

**유형:** 새로운 기능

**서비스 범주:** Azure AD 역할

**제품 기능:** 액세스 제어

관리 단위를 사용하면 정의하는 조직의 부서, 지역 또는 기타 세그먼트로 제한된 관리자 권한을 부여할 수 있습니다. 관리 단위를 사용하여 지역 관리자에게 권한을 위임하거나 세부적인 수준에서 정책을 설정할 수 있습니다. 예를 들어 사용자 계정 관리자는 프로필 정보를 업데이트하고, 암호를 다시 설정하고, 자신의 관리 단위에 포함된 사용자에게만 라이선스를 할당할 수 있습니다.

중앙 관리자는 관리 단위를 사용 하 여 다음과 같은 작업을 할 수 있습니다.

- 리소스의 분산 관리를 위한 관리 단위 만들기
- 관리 단위의 Azure AD 사용자만 관리 권한을 사용 하 여 역할 할당
- 필요에 따라 사용자 및 그룹으로 관리 단위 채우기

자세한 내용은 [Azure Active Directory의 관리 단위 관리 (미리 보기)](https://aka.ms/AdminUnitsDocs)를 참조 하세요.

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>프린터 관리자 및 프린터 기술자 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** Azure AD 역할

**제품 기능:** 액세스 제어

**프린터 관리자**:이 역할의 사용자는 프린터를 등록 하 고 유니버설 인쇄 커넥터 설정을 포함 하 여 Microsoft 유니버설 인쇄 솔루션의 모든 프린터 구성의 모든 측면을 관리할 수 있습니다. 모든 위임된 인쇄 권한 요청에 동의할 수 있습니다. 또한 프린터 관리자는 보고서를 인쇄할 수 있습니다. 

**Printer 기술자**:이 역할의 사용자는 Microsoft 유니버설 인쇄 솔루션에서 프린터를 등록 하 고 프린터 상태를 관리할 수 있습니다. 또한 모든 커넥터 정보를 읽을 수 있습니다. 프린터 기술 자가 할 수 없는 주요 작업은 프린터 및 공유 프린터에 대 한 사용자 권한을 설정 하는 것입니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>하이브리드 Id 관리 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** Azure AD 역할

**제품 기능:** 액세스 제어

이 역할의 사용자는 Azure AD에서 하이브리드 ID 사용 설정과 관련된 서비스 및 설정을 사용하도록 설정하고, 구성하고, 관리할 수 있습니다. 이 역할은 Azure AD를 PHS (암호 해시 동기화), PTA (통과 인증) 또는 페더레이션 (AD FS 또는 타사 페더레이션 공급자) &#8212;에서 지원 되는 세 가지 인증&#8212;방법 중 하나로 구성 하 고,이를 사용 하도록 설정 하는 데 관련 온-프레미스 인프라를 배포 하는 기능을 제공 합니다. 온-프레미스 인프라에는 프로 비전 및 PTA 에이전트가 포함 됩니다. 이 역할은 S-SSO(Seamless Single Sign-On)를 사용하도록 설정하여 Windows 10 이외의 디바이스 또는 Windows Server 2016 이외의 컴퓨터에서 원활한 인증이 가능하게 하는 기능을 부여합니다. 또한이 역할은 로그인 로그를 확인 하 고 모니터링 및 문제 해결을 위한 상태 및 분석에 액세스할 수 있는 기능을 부여 합니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>네트워크 관리자 기본 제공 역할

**유형:** 새로운 기능

**서비스 범주:** Azure AD 역할

**제품 기능:** 액세스 제어

이 역할을 가진 사용자는 Microsoft의 네트워크 경계 아키텍처 권장 사항을 검토 하 여 사용자 위치에서 네트워크 원격 분석을 기반으로 할 수 있습니다. Office 365의 네트워크 성능은 일반적으로 사용자 위치와 관련 된 신중한 엔터프라이즈 고객 네트워크 경계 아키텍처를 사용 합니다. 이 역할이 있으면 검색된 사용자 위치와 해당 위치의 네트워크 매개 변수 구성을 편집하여 원격 분석 측정 및 디자인 권장 사항을 쉽게 개선할 수 있습니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 관리 포털 환경에서 대량 작업 및 다운로드

**유형:** 새로운 기능

**서비스 범주:** 사용자 관리

**제품 기능:** 디렉터리나

이제 Azure AD 관리 포털 환경에서 CSV 파일을 업로드 하 여 Azure AD의 사용자 및 그룹에 대 한 대량 작업을 수행할 수 있습니다. 사용자를 만들고, 사용자를 삭제 하 고, 게스트 사용자를 초대할 수 있습니다. 그리고 그룹에서 구성원을 추가 하 고 제거할 수 있습니다.

Azure AD 관리 포털 환경에서 Azure AD 리소스 목록을 다운로드할 수도 있습니다. 디렉터리, 디렉터리의 그룹 목록 및 특정 그룹의 구성원에 대 한 사용자 목록을 다운로드할 수 있습니다.

자세한 내용은 다음을 확인 하세요.

- [사용자 만들기](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) 또는 [게스트 사용자 초대](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [사용자 삭제](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) 또는 [삭제 된 사용자 복원](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [사용자 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) 또는 [그룹 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- 그룹에 대 한 멤버 [추가 (가져오기)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) 또는 구성원 [제거](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) 또는 [구성원 목록 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>내 직원 위임 된 사용자 관리

**유형:** 새로운 기능

**서비스 범주:** 사용자 관리

**제품 기능:**

직원 들이 자신의 직원 구성원이 자신의 Azure AD 계정에 액세스할 수 있도록 내 직원은 저장소 관리자와 같은 Firstline 관리자를 사용 하도록 설정 합니다. 조직에서는 중앙 기술 지원팀에 의존 하는 대신 암호 재설정 또는 전화 번호 변경과 같은 일반적인 작업을 Firstline Manager로 위임할 수 있습니다. 직원 들이 자신의 계정에 액세스할 수 없는 사용자는 기술 지원팀 또는 IT 담당자가 필요 하지 않은 두 번의 클릭 만으로 액세스 권한을 다시 얻을 수 있습니다. 자세한 내용은 내 [직원 (미리 보기)을 사용](https://aka.ms/MyStaffAdminDocs) 하 여 사용자 관리 및 [내 직원 (미리 보기)을 사용 하 여 사용자 관리 위임 (미리 보기)](https://aka.ms/MyStaffUserDocs)을 참조 하세요.

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>액세스 검토에서 업그레이드 된 최종 사용자 환경

**유형:** 변경된 기능

**서비스 범주:** 액세스 검토

**제품 기능:** Id 거 버 넌 스

내 앱 포털에서 Azure AD 액세스 검토에 대 한 검토자 환경을 업데이트 했습니다. 4 월 말에 Azure AD 액세스를 검토 하는 검토자에 게는 사용자의 액세스에 업데이트 된 환경을 사용해 볼 수 있는 배너가 표시 됩니다. 업데이트 된 액세스 검토 환경은 현재 환경과 동일한 기능을 제공 하지만 사용자가 생산성을 높일 수 있도록 새로운 기능을 기반으로 하는 향상 된 사용자 인터페이스를 제공 합니다. [업데이트 된 환경에 대 한 자세한 내용은 여기에서 확인할 수](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)있습니다. 이 공개 미리 보기는 7 월 2020이 끝날 때까지 마지막으로 발생 합니다. 7 월 말에 미리 보기 환경을 옵트인 하지 않은 검토자는 액세스 검토를 수행 하기 위해 자동으로 내 액세스로 전달 됩니다. 검토자가 내 액세스의 미리 보기 환경으로 영구적으로 전환 되도록 하려면 [여기에서 요청 하세요](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 인바운드 사용자 프로 비전 및 쓰기 저장 앱은 이제 최신 버전의 Workday 웹 서비스 API를 지원 합니다.

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** 

이제 고객의 의견에 따라 엔터프라이즈 앱 갤러리에서 Workday 인바운드 사용자 프로 비전 및 쓰기 저장 앱을 업데이트 하 여 최신 버전의 WWS (Workday 웹 서비스) API를 지원 합니다. 이러한 변경을 통해 고객은 연결 문자열에서 사용할 WWS API 버전을 지정할 수 있습니다. 이를 통해 고객은 Workday 릴리스에서 제공 되는 더 많은 HR 특성을 검색할 수 있습니다. Workday 쓰기 저장 앱은 이제 권장 Change_Work_Contact_Info Workday 웹 서비스를 사용 하 여 Maintain_Contact_Info의 제한을 극복 합니다.

연결 문자열에 버전이 지정 되지 않은 경우 기본적으로 Workday 인바운드 프로 비전 앱은 WWS v 21.1를 사용 하 여 인바운드 사용자 프로 비전을 위한 최신 Workday Api로 전환 하 고, 고객은 [자습서에 설명 된](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) 대로 연결 문자열을 업데이트 하 고 [workday 특성 참조 가이드](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)에 설명 된 대로 workday 특성에 사용 되는 xpath도 업데이트 해야 합니다. 

쓰기 저장 (writeback)을 위해 새 API를 사용 하려면 Workday 쓰기 저장 프로 비전 앱에 필요한 변경 내용이 없습니다. Workday 쪽에서 Workday (Workday 통합 시스템 사용자) 계정에 자습서 섹션인 [비즈니스 프로세스 보안 정책 권한 구성](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)에 설명 된 대로 Change_Work_Contact 비즈니스 프로세스를 호출할 수 있는 권한이 있는지 확인 합니다. 

새 API 버전 지원이 반영 되도록 [자습서 가이드](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) 를 업데이트 했습니다.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>기본 액세스 역할이 있는 사용자는 현재 프로 비전 범위에 있습니다.

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** Id 수명 주기 관리

지금까지 기본 액세스 역할이 있는 사용자는 프로 비전 범위를 벗어났습니다. 이 역할을 가진 사용자가 프로 비전 범위에 있는 것을 고객에 게 제공할 수 있는 피드백을 받았습니다. 2020 년 4 월 16 일부 터 모든 새 프로 비전 구성을 사용 하면 기본 액세스 역할이 있는 사용자를 프로 비전 할 수 있습니다. 점진적으로 기존 프로 비전 구성의 동작을 변경 하 여이 역할을 사용 하는 사용자 프로 비전을 지원 합니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>업데이트 된 프로 비전 UI

**유형:** 변경된 기능

**서비스 범주:** 앱 프로비전

**제품 기능:** Id 수명 주기 관리

프로 비전 환경을 새로 고쳐 더 집중 된 관리 보기를 만들었습니다. 이미 구성 된 엔터프라이즈 응용 프로그램의 프로 비전 블레이드로 이동 하면 프로 비전 시작, 중지 및 다시 시작 등의 작업을 프로 비전 하 고 관리 하는 과정을 쉽게 모니터링할 수 있습니다. [자세한 정보](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>동적 그룹 규칙 유효성 검사는 이제 공개 미리 보기로 제공 됩니다.

**유형:** 변경된 기능

**서비스 범주:** 그룹 관리

**제품 기능:** 협업

이제 Azure Active Directory (Azure AD)에서 동적 그룹 규칙의 유효성을 검사 하는 방법을 제공 합니다. **규칙 유효성 검사** 탭에서 샘플 그룹 멤버에 대해 동적 규칙의 유효성을 검사 하 여 규칙이 예상 대로 작동 하는지 확인할 수 있습니다. 관리자는 동적 그룹 규칙을 만들거나 업데이트할 때 사용자 또는 장치가 그룹의 구성원 인지 여부를 확인 하려고 합니다. 이를 통해 사용자 또는 장치가 규칙 기준을 충족 하는지 여부를 평가 하 고, 멤버 자격이 필요 하지 않을 때 문제 해결을 지원 합니다.

자세한 내용은 [동적 그룹 멤버 자격 규칙 유효성 검사 (미리 보기)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)를 참조 하세요.

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Id 보안 점수-보안 기본값 및 MFA 개선 작업 업데이트

**유형:** 변경된 기능

**서비스 범주:** 해당 없음

**제품 기능:** Id 보안 & 보호

**AZURE AD 개선 작업의 보안 기본값 지원:** Microsoft Secure 점수는 [AZURE AD의 보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)을 지원 하도록 개선 작업을 업데이트 하 여 일반적인 공격에 대해 미리 구성 된 보안 설정으로 조직을 보호 하는 작업을 더 쉽게 수행할 수 있게 해줍니다. 이는 다음과 같은 개선 작업에 영향을 줍니다.

- 모든 사용자가 보안 액세스를 위해 multi-factor authentication을 완료할 수 있는지 확인
- 관리 역할에 대 한 MFA 필요
- 정책을 사용 하 여 레거시 인증 차단
 
**MFA 개선 작업 업데이트:** 비즈니스에 사용 되는 정책을 적용 하는 동시에 비즈니스의 보안을 보장 하기 위한 비즈니스 요구 사항을 반영 하기 위해 Microsoft 보안 점수는 다단계 인증 중심의 세 가지 개선 작업을 제거 하 고 2를 추가 했습니다.

제거 되는 향상 작업:

- Multi-factor authentication에 대 한 모든 사용자 등록
- 모든 사용자용 MFA 필요
- Azure AD 권한 있는 역할에 대 한 MFA 필요

추가 된 개선 작업:

- 모든 사용자가 보안 액세스를 위해 multi-factor authentication을 완료할 수 있는지 확인
- 관리 역할에 대 한 MFA 필요

이러한 새로운 향상 된 작업을 수행 하려면 디렉터리에서 MFA (multi-factor authentication)에 대해 사용자 또는 관리자를 등록 하 고 조직의 요구에 맞는 올바른 정책 집합을 설정 해야 합니다. 주요 목표는 모든 사용자와 관리자가 여러 요소나 위험 기반 id 확인 프롬프트를 사용 하 여 인증할 수 있도록 하는 동시에 유연성을 유지 하는 것입니다. 이는 범위가 지정 된 결정을 적용 하는 여러 정책이 있거나, Microsoft에서 MFA에 대 한 사용자의 시도 시기를 결정 하는 보안 기본값 (3 월 16 일 기준)을 설정 하는 형태를 사용할 수 있습니다. [Microsoft 보안 점수의 새로운 기능에 대해 자세히](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)알아보세요.

---

## <a name="march-2020"></a>2020년 3월

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>3 월에 대 한 B2B 업데이트의 관리 되지 않는 Azure Active Directory 계정 2021

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
**2021 년 3 월 31 일부 터**MICROSOFT는 B2B 공동 작업 시나리오에 대해 관리 되지 않는 Azure Active Directory (Azure AD) 계정 및 테 넌 트를 만들어 더 이상 초대 상환을 지원 하지 않습니다. 이를 위해 준비 하는 [동안 일회용 암호 인증을 전자 메일로](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)보내도록 옵트인 하는 것이 좋습니다.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>기본 액세스 역할이 있는 사용자는 프로 비전 범위에 포함 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
지금까지 기본 액세스 역할이 있는 사용자는 프로 비전 범위를 벗어났습니다. 이 역할을 가진 사용자가 프로 비전 범위에 있는 것을 고객에 게 제공할 수 있는 피드백을 받았습니다. 모든 새 프로 비전 구성에서 기본 액세스 역할이 있는 사용자를 프로 비전 할 수 있도록 변경 내용을 배포 하기 위해 노력 하 고 있습니다. 점진적으로 기존 프로 비전 구성의 동작을 변경 하 여이 역할의 사용자 프로 비전을 지원 합니다. 고객 동작은 필요 하지 않습니다. 이 변경 내용이 적용 되 면 [설명서](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) 에 대 한 업데이트를 게시 합니다.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 공동 작업은 21Vianet (Azure 중국 21Vianet) 테 넌 트에서 운영 하는 Microsoft Azure에서 사용할 수 있습니다.

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
Azure AD B2B 공동 작업 기능은 21Vianet (Azure 중국 21Vianet) 테 넌 트가 운영 하는 Microsoft Azure에서 사용할 수 있으며, Azure 중국 21Vianet 테 넌 트의 사용자가 다른 Azure 중국 21Vianet 테 넌 트의 사용자와 원활 하 게 공동 작업을 수행할 수 있도록 합니다. [AZURE AD B2B 공동 작업에 대해 자세히 알아보세요](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 공동 작업 초대 전자 메일 다시 디자인

**유형:** 변경 계획  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
사용자를 디렉터리에 초대 하기 위해 Azure AD B2B 공동 작업 초대 서비스에서 보낸 [전자 메일](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) 은 초대 정보 및 사용자의 다음 단계를 명확 하 게 하기 위해 다시 디자인 됩니다.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Homerealmdiscovery.aspx 정책 변경이 감사 로그에 표시 됩니다.

**유형:** 고정  
**서비스 범주:** 감사  
**제품 기능:** 모니터링 및 보고
 
[Homerealmdiscovery.aspx 정책](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 에 대 한 변경 내용이 감사 로그에 포함 되지 않은 버그를 수정 했습니다. 이제 정책을 변경 하는 시기와 방법 및 해당 사용자를 확인할 수 있습니다. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-3 월 2020

**유형:** 새로운 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
3 월 2020에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 51 새 앱을 추가 했습니다. 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One 중국](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML 앱](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [IPoint Service Provider](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai 구에](https://contexxt-sphere.com/login), [지혜가 By Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [플레어 DIGITAL Signage](https://spark-dev.pixelnebula.com/login), Logz.io [-Cloud 관찰성 for 엔지니어](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MarketSignShare](http://www.signshare.com/), [COMPAS Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [netvision compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM HUB](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [리브 A/S byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [Datadog, Zscaler](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [B2B 사용자 포털](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [리프트](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams, 별표](https://www.devfinition.com/), [기술 워크플로](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [노드](https://admin.nodeinsight.com/AADLogin.aspx)정보, [IP 플랫폼](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [INVISION](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [Pipedrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [전시 워크숍](https://app.showcaseworkshop.com/), [Greenlight Integration Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [Greenlight 규격 액세스 관리](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore Online](https://login.online.miradore.com/), [Khoros 주의](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [askTruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial) [, Smartwaiver,](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial) [Bizagi](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [insuiteX Studio for Digital Process Automation](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [sybo](https://www.insuite.jp/), [Britive](https://www.systexsoftware.com.tw/), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [, Kollective](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-일](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Witivio SDN](https://portal.kollective.app/login), [Playvox](https://app.witivio.com/), [한쪽](https://my.playvox.com/login), [Korn 카페 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [캠퍼스 catch 지점](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial) [, Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial), [Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) [Aster](https://demo.asterapp.io/login)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure Government 테 넌 트에서 사용할 수 있는 Azure AD B2B 공동 작업

**유형:** 새로운 기능  
**서비스 범주:** B2B  
**제품 기능:** B2B/B2C
 
이제 Azure AD B2B 공동 작업 기능을 일부 Azure Government 테 넌 트 간에 사용할 수 있습니다.  테 넌 트가 이러한 기능을 사용할 수 있는지 확인 하려면 [AZURE 미국 정부 테 넌 트에서 B2B 공동 작업을 사용할 수 있는지 어떻게 알 수 있나요?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)를 참조 하세요.

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>이제 Azure Government에서 Azure 로그에 대 한 Azure Monitor 통합을 사용할 수 있습니다.

**유형:** 새로운 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
이제 Azure Government에서 Azure AD 로그와 Azure Monitor 통합을 사용할 수 있습니다. Azure AD 로그 (감사 및 로그인 로그)를 저장소 계정, 이벤트 허브 및 Log Analytics로 라우팅할 수 있습니다. Azure AD 시나리오에 대 한 [보고 및 모니터링에 대 한](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) [자세한 설명서](https://aka.ms/aadlogsinamd) 및 배포 계획을 확인 하세요.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure Government에서 id 보호 새로 고침

**유형:** 새로운 기능  
**서비스 범주:** Id 보호  
**제품 기능:** Id 보안 & 보호

이제 [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   [Microsoft Azure Government 포털](https://portal.azure.us/)에서 새로 고친 Azure AD ID 보호 환경을 출시 했습니다. 자세한 내용은 [발표 블로그 게시물](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)을 참조 하세요.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>재해 복구: 프로 비전 구성을 다운로드 하 고 저장 합니다.

**유형:** 새로운 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** Id 수명 주기 관리
 
Azure AD 프로 비전 서비스는 다양 한 구성 기능 집합을 제공 합니다. 고객은 나중에 참조 하거나 알려진 올바른 버전으로 롤백할 수 있도록 구성을 저장할 수 있어야 합니다. 프로 비전 구성을 JSON 파일로 다운로드 하 고 필요할 때 업로드 하는 기능을 추가 했습니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (셀프 서비스 암호 재설정)은 이제 21Vianet에서 운영 하는 Microsoft Azure의 관리자를 위한 두 개의 게이트 (Azure 중국 21Vianet)가 필요 합니다. 

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** Id 보안 & 보호
 
이전에는 21Vianet (Azure 중국 21Vianet)에서 운영 하는 Microsoft Azure에서 SSPR (셀프 서비스 암호 재설정)를 사용 하 여 자신의 암호를 재설정 하는 관리자는 본인의 id를 증명 하기 위해 하나의 "게이트" (챌린지)만 필요 했습니다. 공용 및 기타 국가에서 관리자는 일반적으로 SSPR를 사용 하는 경우 두 개의 게이트를 사용 하 여 id를 증명 해야 합니다. 그러나 Azure 중국 21Vianet에서 SMS 또는 전화 통화를 지원 하지 않았기 때문에 관리자가 한 게이트 암호 재설정을 수행할 수 있습니다.

Azure 중국 21Vianet과 공용 클라우드 간에 SSPR 기능 패리티를 만들고 있습니다. 앞으로 관리자는 SSPR를 사용할 때 두 개의 게이트를 사용 해야 합니다. SMS, 전화 통화 및 인증자 앱 알림 및 코드가 지원 됩니다. [자세한 정보를 알아보세요](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>암호 길이는 256 자로 제한 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증 (로그인)  
**제품 기능:** 사용자 인증
 
Azure AD 서비스의 안정성을 보장 하기 위해 이제 사용자 암호의 길이는 256 자로 제한 됩니다. 이 보다 긴 암호를 사용 하는 사용자는 관리자에 게 문의 하거나 셀프 서비스 암호 재설정 기능을 사용 하 여 후속 로그인 시 암호를 변경 하 라는 메시지가 표시 됩니다.

이 변경 내용은 오전 10 월 13 2020 일 오전 10 시 (18:00 UTC)에 사용 하도록 설정 되었으며 오류는 AADSTS 50052, InvalidPasswordExceedsMaxLength입니다. 자세한 내용은 [주요 변경 공지](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) 를 참조 하세요.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>이제 Azure Portal를 통해 Azure AD 로그인 로그를 모든 사용 가능한 테 넌 트에 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고
 
지금부터 무료 테 넌 트가 있는 고객은 최대 7 일 동안 [Azure Portal에서 AZURE AD 로그인 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) 에 액세스할 수 있습니다. 이전에는 Azure Active Directory Premium 라이선스를 사용 하는 고객 에게만 로그인 로그를 사용할 수 있었습니다. 이러한 변경을 통해 모든 테 넌 트는 포털을 통해 이러한 로그에 액세스할 수 있습니다.

> [!NOTE]
> 고객은 Microsoft Graph API 및 Azure Monitor를 통해 로그인 로그에 액세스 하기 위한 프리미엄 라이선스 (Azure Active Directory Premium P1 또는 P2)도 필요 합니다.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>그룹의 디렉터리 전체 그룹 옵션 사용 중단 Azure Portal의 일반 설정

**유형:** 사용되지 않음  
**서비스 범주:** 그룹 관리  
**제품 기능:** 협업

고객이 자신의 요구에 가장 적합 한 디렉터리 전체 그룹을 만들 수 있는 보다 유연한 방법을 제공 하기 위해 Azure Portal의 **그룹**일반 설정에서 동적 그룹 설명서에 대 한 링크를 사용 하 여 **디렉터리 전체 그룹** 옵션을 대체 했습니다  >  **General** [dynamic group documentation](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). 관리자가 게스트 사용자를 포함 하거나 제외 하는 모든 사용자 그룹을 만들 수 있도록 더 많은 지침을 포함 하도록 설명서를 개선 했습니다.

---