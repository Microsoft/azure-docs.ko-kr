---
title: 새로운 기능 릴리스 정보 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능, 예정된 변경 내용 등을 알아봅니다.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5bf9c5c80593066e31b1ff23b6def844f67e65
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185773"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory의 새로운 기능

>이 URL을 복사 하 고 붙여넣어 업데이트에 대 한이 페이지를 다시 방문 해야 하는 경우에 대 한 알림이 표시 됩니다. ![RSS 피드 판독기 아이콘](./media/whats-new/feed-icon-16x16.png) 피드 판독기에 `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 합니다.

Azure AD는 지속적인 향상되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 6개월 이상된 항목을 찾으려는 경우 [Azure Active Directory의 새로운 기능 아카이브](whats-new-archive.md)에서 찾을 수 있습니다.

---

## <a name="january-2020"></a>2020년 1월
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>새 내 앱 포털이 이제 일반 공급 됩니다.

**유형:** 변경 계획  
**서비스 범주:** 내 앱  
**제품 기능:** 최종 사용자 환경
 
이제 일반 공급 되는 새 내 앱 포털로 조직을 업그레이드 하세요. [내 앱 포털에서 컬렉션 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)의 새 포털 및 컬렉션에 대 한 자세한 내용을 확인 하세요.

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD의 작업 영역 이름이 컬렉션으로 바뀌었습니다.

**유형:** 변경된 기능  
**서비스 범주:** 내 앱   
**제품 기능:** 최종 사용자 환경
 
관리자가 사용자 앱을 구성 하도록 구성할 수 있는 작업 영역을 이제는 컬렉션 이라고 합니다. [내 앱 포털에서 컬렉션 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)에서 구성 하는 방법에 대 한 자세한 정보를 찾습니다.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>사용자 지정 정책을 사용 하 여 전화 등록 및 로그인 Azure AD B2C (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C
 
전화 번호 등록 및 로그인을 통해 개발자와 기업은 고객이 SMS를 통해 사용자의 전화 번호로 전송 된 일회용 암호를 사용 하 여 등록 하 고 로그인 할 수 있습니다. 또한이 기능을 통해 고객은 휴대폰에 대 한 액세스 권한을 상실 한 경우 전화 번호를 변경할 수 있습니다. 사용자 지정 정책의 강력한 기능을 통해 전화 등록 및 로그인을 통해 개발자와 기업은 페이지 사용자 지정을 통해 브랜드를 통신할 수 있습니다. [Azure AD B2C에서 사용자 지정 정책을 사용 하 여 전화 등록 및 로그인을 설정](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)하는 방법을 알아봅니다.
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 응용 프로그램 갤러리의 새로운 프로 비전 커넥터-1 월 2020

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-2020 년 1 월

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합
 
2020 년 1 월에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 33 새 앱을 추가 했습니다. 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Cloud Backup for Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [Datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TRIPACTIONS](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [Smartwork](https://www.intumit.com/english/SmartWork.html), [Dotcom-MONITOR](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Oracle E-비즈니스 제품군 용 Azure AD SSO 게이트웨이-EBS, PeopleSoft, JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [Microsoft 팀을 위한 smartdb](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient upwork Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [flow 인증](https://app-staging.pingview.io/), [프린터 논리 SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial) [, Sandwai, EZRentOut](https://app.sandwai.com/) [, AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [, Akari](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [ 가상 길잡이](https://akari.io/akari-virtual-assistant/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="two-new-identity-protection-detections"></a>두 개의 새로운 Id 보호 검색

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호
 
Id 보호에 두 가지 로그인 연결 된 검색 유형을 추가 했습니다. 의심 스러운 수신함 조작 규칙 및 불가능 한 이동입니다. 이러한 오프 라인 검색은 MCAS (Microsoft Cloud App Security)에 의해 검색 되며 Id 보호의 사용자 및 로그인 위험에 영향을 줍니다. 이러한 검색에 대 한 자세한 내용은 [로그인 위험 유형](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)을 참조 하세요.
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>호환성이 손상되는 변경: URI 조각은 로그인 리디렉션을 통해 전달 되지 않습니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증
 
2020,에서 시작 하 여 사용자를 로그인 하기 위해 login.microsoftonline.com에 요청을 보내면 서비스는 빈 조각을 요청에 추가 합니다.  이렇게 하면 브라우저에서 요청의 기존 조각을 모두 초기화 하 여 공격에 대 한 클래스를 방지할 수 있습니다. 이 동작에 대 한 종속성이 있는 응용 프로그램이 없습니다. 자세한 내용은 Microsoft id 플랫폼 설명서의 [주요 변경 내용](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) 을 참조 하세요.

---

## <a name="december-2019"></a>2019년 12월

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP SuccessFactors 프로 비전을 Azure AD 및 온-프레미스 AD에 통합 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

이제 Azure AD에서 SAP SuccessFactors를 신뢰할 수 있는 id 원본으로 통합할 수 있습니다. 이러한 통합을 통해 새 채용 또는 종료와 같은 HR 기반 이벤트를 사용 하 여 Azure AD 계정 프로 비전을 제어 하는 등 종단 간 id 수명 주기를 자동화할 수 있습니다.

Azure AD에 대 한 SAP SuccessFactors 인바운드 프로 비전을 설정 하는 방법에 대 한 자세한 내용은 [Sap SuccessFactors 자동 프로 비전 구성](https://aka.ms/SAPSuccessFactorsInboundTutorial) 자습서를 참조 하세요.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C에서 사용자 지정 된 전자 메일 지원 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 사용자가 앱을 사용 하기 위해 등록할 때 Azure AD B2C를 사용 하 여 사용자 지정 된 전자 메일을 만들 수 있습니다. DisplayControls (현재 미리 보기 상태)와 타사 전자 메일 공급자 (예: [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)또는 사용자 지정 REST API)를 사용 하 여 사용자 고유의 전자 메일 템플릿, 주소 및 제목 텍스트 **를 사용할** 수 있을 뿐만 아니라 지역화 및 사용자 지정 OTP (일회용 암호) 설정을 지원할 수 있습니다.

자세한 내용은 [Azure Active Directory B2C에서 사용자 지정 전자 메일 확인](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)을 참조 하세요.

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>보안 기본값으로 기본 정책 바꾸기

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** Id 보안 및 보호

인증을 위한 기본 보안 모델의 일부로 모든 테 넌 트에서 기존 기준 보호 정책을 제거 하 고 있습니다. 이 제거는 2 월 말에 완료 될 대상입니다. 이러한 기준 보호 정책에 대 한 대체는 보안 기본값입니다. 기준 보호 정책을 사용한 경우 새 보안 기본값 정책 또는 조건부 액세스로 이동 하도록 계획 해야 합니다. 이러한 정책을 사용 하지 않은 경우 수행할 작업은 없습니다.

새 보안 기본값에 대 한 자세한 내용은 [보안 기본값 이란?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 을 참조 하세요. 조건부 액세스 정책에 대 한 자세한 내용은 [일반적인 조건부 액세스 정책](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)을 참조 하세요.

---

## <a name="november-2019"></a>2019년 11월

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 특성 및 크롬 80에 대 한 지원

**유형:** 변경 계획  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

쿠키에 대 한 기본 보안 모델의 일부로 Chrome 80 브라우저는 `SameSite` 특성이 없는 쿠키를 처리 하는 방식을 변경 합니다. `SameSite` 특성을 지정 하지 않는 쿠키는 `SameSite=Lax`로 설정 된 것 처럼 처리 됩니다. 그러면 Chrome에서 앱이 종속 될 수 있는 특정 도메인 간 쿠키 공유 시나리오를 차단 합니다. 이전 Chrome 동작을 유지 하려면 `SameSite=None` 특성을 사용 하 고 추가 `Secure` 특성을 추가 하 여 HTTPS 연결을 통해서만 사이트 간 쿠키에 액세스할 수 있습니다. Chrome은 2020 년 2 월 4 일에이 변경을 완료 하도록 예약 되어 있습니다.

모든 개발자가이 지침을 사용 하 여 앱을 테스트 하는 것이 좋습니다.

- **보안 쿠키 사용** 설정의 기본값을 **예**로 설정 합니다.

- **SameSite** 특성의 기본값을 **None**으로 설정 합니다.

- **보안**의 추가 `SameSite` 특성을 추가 합니다.

자세한 내용은 [향후 SameSite 쿠키 변경 ASP.NET 및 ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) 및 [Chrome 버전 79 이상에서 고객 websites 및 Microsoft 제품 및 서비스에 대 한 잠재적인 중단](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)을 참조 하세요.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager (MIM) 2016 서비스 팩 2 (SP2) 용 새 핫픽스

**유형:** 수정됨  
**서비스 범주:** Microsoft ID 관리자  
**제품 기능:** ID 수명 주기 관리

핫픽스 롤업 패키지 (build 4.6.34.0)는 Microsoft Identity Manager (MIM) 2016 서비스 팩 2 (SP2)에서 사용할 수 있습니다. 이 롤업 패키지는 문제를 해결 하 고 "이 업데이트에 추가 된 문제 해결 및 개선 사항" 섹션에 설명 된 향상 된 기능을 추가 합니다.

핫픽스 패키지를 다운로드 하 고 다운로드 하는 방법에 대 한 자세한 내용은 [Microsoft Identity Manager 2016 서비스 팩 2 (build 4.6.34.0) 업데이트 롤업을 사용 가능](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)을 참조 하세요.

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>앱을 Azure AD로 마이그레이션하는 데 도움이 되는 새로운 AD FS 앱 활동 보고서 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure Portal에서 새 Active Directory Federation Services (AD FS) 앱 활동 보고서를 사용 하 여 Azure AD로 마이그레이션할 수 있는 앱을 식별 합니다. 이 보고서는 Azure AD와의 호환성을 위해 모든 AD FS 앱을 평가 하 고, 문제를 확인 하 고, 개별 앱을 마이그레이션하기 위한 준비에 대 한 지침을 제공 합니다.

자세한 내용은 [AD FS 응용 프로그램 작업 보고서를 사용 하 여 응용 프로그램을 AZURE AD로 마이그레이션](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)을 참조 하세요.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>사용자가 관리자 동의를 요청 하는 새 워크플로 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** Access Control

새 관리자 동의 워크플로를 통해 관리자는 관리자 승인이 필요한 앱에 대 한 액세스 권한을 부여할 수 있습니다. 사용자가 앱에 액세스 하려고 하지만 동의를 제공할 수 없는 경우 이제 관리자 승인에 대 한 요청을 보낼 수 있습니다. 요청은 전자 메일을 통해 전송 되 고, Azure Portal에서 액세스할 수 있는 큐에 저장 되며 검토자로 지정 된 모든 관리자에 게 적용 됩니다. 검토자가 보류 중인 요청에 대해 작업을 수행 하면 요청 하는 사용자에 게 작업에 대 한 알림이 표시 됩니다.

자세한 내용은 [관리자 동의 워크플로 구성 (미리 보기)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)을 참조 하세요.

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>선택적인 클레임을 관리 하기 위한 새로운 Azure AD 앱 등록 토큰 구성 환경 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** 개발자 환경

이제 Azure Portal의 새 **Azure AD 앱 등록 토큰 구성** 블레이드에서 앱 개발자에 게 앱에 대 한 선택적 클레임의 동적 목록을 표시 합니다. 이 새로운 환경을 통해 Azure AD 앱 마이그레이션을 간소화 하 고 선택적 클레임 잘못 된 구성을 최소화할 수 있습니다.

자세한 내용은 [AZURE AD 앱에 선택적 클레임 제공](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)을 참조 하세요.

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 자격 관리의 새로운 2 단계 승인 워크플로 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리

액세스 패키지에 대 한 사용자의 요청을 승인 하는 두 명의 승인자를 요구할 수 있는 새로운 2 단계 승인 워크플로가 도입 되었습니다. 예를 들어 요청 하는 사용자의 관리자가 먼저 승인 하도록 설정한 다음 리소스 소유자가 승인 하도록 요구할 수도 있습니다. 승인자 중 하나가 승인 되지 않으면 액세스가 허용 되지 않습니다.

자세한 내용은 [AZURE AD 자격 관리에서 액세스 패키지에 대 한 요청 및 승인 설정 변경](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)을 참조 하세요.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>새 작업 영역과 함께 내 앱 페이지에 대 한 업데이트 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** 타사 통합

이제 조직의 사용자가 새로 고쳐진 내 앱 환경을 보고 액세스 하는 방법을 사용자 지정할 수 있습니다. 이 새로운 환경에는 사용자가 앱을 보다 쉽게 찾고 구성할 수 있도록 하는 새로운 작업 영역 기능도 포함 되어 있습니다.

새 내 앱 환경 및 작업 영역 만들기에 대 한 자세한 내용은 [My apps 포털에서 작업 영역 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)를 참조 하세요.

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 공동 작업을 위한 Google 소셜 ID 지원 (일반 공급)

**유형:** 새 기능  
**서비스 범주:** B2B  
**제품 기능:** 사용자 인증

Azure AD에서 Google 소셜 Id (Gmail 계정)를 사용 하는 새로운 지원 기능을 통해 사용자와 파트너에 게 더 간단 하 게 공동 작업을 수행할 수 있습니다. 파트너가 새 Microsoft 특정 계정을 만들고 관리 하는 데 더 이상 필요 하지 않습니다. Microsoft 팀은 이제 모든 클라이언트와 공통 및 테 넌 트 관련 인증 끝점에서 Google 사용자를 완전히 지원 합니다.

자세한 내용은 [B2B 게스트 사용자에 대 한 id 공급자로 Google 추가](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)를 참조 하세요.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>조건부 액세스 및 Single Sign-on을 위한 Microsoft Edge 모바일 지원 (일반 공급)

**유형:** 새 기능  
**서비스 범주:** 조건부 액세스  
**제품 기능:** ID 보안 및 보호

IOS 및 Android의 Microsoft Edge 용 azure AD는 이제 Azure AD Single Sign-on 및 조건부 액세스를 지원 합니다.

- **SSO (Microsoft Edge Single Sign-On):** 이제 모든 Azure AD 연결 앱에 대 한 기본 클라이언트 (예: Microsoft Outlook 및 Microsoft Edge)에서 Single sign-on을 사용할 수 있습니다.

- **Microsoft Edge 조건부 액세스:** 응용 프로그램 기반 조건부 액세스 정책을 통해 사용자는 Microsoft Edge와 같은 Microsoft Intune 보호 된 브라우저를 사용 해야 합니다.

Microsoft Edge를 사용 하는 조건부 액세스 및 SSO에 대 한 자세한 내용은 [Microsoft Edge Mobile Support For 조건부 액세스 및 Single sign-on을 사용 하 여 일반적으로 사용 가능](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) 블로그 게시물을 참조 하세요. [앱 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) 또는 [장치 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)를 사용 하 여 클라이언트 앱을 설정 하는 방법에 대 한 자세한 내용은 [Microsoft Intune 정책으로 보호 된 브라우저를 사용 하 여 웹 액세스 관리](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)를 참조 하세요.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 자격 관리 (일반 공급)

**유형:** 새 기능  
**서비스 범주:** 기타  
**제품 기능:** 자격 관리

Azure AD 자격 관리는 조직에서 id 및 액세스 수명 주기를 대규모로 관리할 수 있도록 하는 새로운 id 거 버 넌 스 기능입니다. 이 새로운 기능을 통해 그룹, 앱 및 SharePoint Online 사이트에서 액세스 요청 워크플로, 액세스 할당, 검토 및 만료를 자동화할 수 있습니다.

Azure AD 자격 관리를 통해 직원 및 해당 리소스에 액세스 해야 하는 조직 외부 사용자에 대 한 액세스를 보다 효율적으로 관리할 수 있습니다.

자세한 내용은 [AZURE AD 자격 관리 란?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) 을 참조 하세요.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>새로 지원 되는 SaaS 앱에 대 한 사용자 계정 프로 비전 자동화

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합  

이제 새로 통합 되는 앱에 대 한 사용자 계정을 만들고, 업데이트 하 고, 삭제할 수 있습니다.

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [officespace software Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

자동 사용자 계정 프로 비전을 사용 하 여 조직의 보안을 강화 하는 방법에 대 한 자세한 내용은 Azure AD를 사용 하 여 [SaaS 응용 프로그램에 사용자 프로 비전 자동화](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-11 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

11 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 포함 하는 새로운 21 개 앱을 추가 했습니다.

어 위 [테이블](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [hocansuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [회원 (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial) [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [reslife 포털](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal sso (Single Sign On)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [beedle](https://teams-web.beedle.co/#/), [visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [onedesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [qmarkets 아이디어 & 혁신 관리](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [net9omuser 인증](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), 전체 [흐름 온라인](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial) [](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [Jisc 학생 동안 유권자 등록](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>새롭고 향상 된 Azure AD 응용 프로그램 갤러리

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

Azure Active Directory 테 넌 트에서 프로 비전, Openid connect 연결 및 SAML을 지 원하는 사전 통합 된 앱을 보다 쉽게 찾을 수 있도록 Azure AD 응용 프로그램 갤러리를 업데이트 했습니다.

자세한 내용은 [Azure Active Directory 테 넌 트에 응용 프로그램 추가](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)를 참조 하세요.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>앱 역할 정의 길이 제한을 120에서 240 자로 늘렸습니다.

**유형:** 변경된 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

고객 으로부터 일부 앱 및 서비스의 앱 역할 정의 값에 대 한 길이 제한이 120 자에서 너무 짧습니다. 이에 대 한 응답으로 역할 값 정의의 최대 길이를 240 자로 늘립니다.

응용 프로그램 관련 역할 정의를 사용 하는 방법에 대 한 자세한 내용은 [응용 프로그램에 앱 역할 추가 및 토큰에서 수신](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)을 참조 하세요.

---

## <a name="october-2019"></a>2019년 10월

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Azure AD ID 보호 위험 검색을 위한 identityRiskEvent API의 사용 중단  

**유형:** 변경 계획  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

개발자 피드백에 대 한 응답으로 Azure AD Premium P2 구독자는 이제 Microsoft Graph에 대해 새로운 riskDetection API를 사용 하 여 Azure AD ID 보호의 위험 검색 데이터에 대 한 복잡 한 쿼리를 수행할 수 있습니다. 기존 [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API 베타 버전은 **2020 년 1 월 10**일에 데이터를 반환 하는 것을 중지 합니다. 조직에서 identityRiskEvent API를 사용 하는 경우 새 riskDetection API로 전환 해야 합니다.

새 riskDetection API에 대 한 자세한 내용은 [위험 검색 api 참조 설명서](https://aka.ms/RiskDetectionsAPI)를 참조 하세요.

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>SameSite 특성 및 Chrome 80에 대 한 응용 프로그램 프록시 지원

**유형:** 변경 계획  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

Chrome 80 브라우저 릴리스 전에 몇 주 후에는 응용 프로그램 프록시 쿠키가 **SameSite** 특성을 처리 하는 방법을 업데이트할 계획입니다. Chrome 80의 릴리스를 사용 하 여 **SameSite** 특성을 지정 하지 않은 쿠키는 `SameSite=Lax`로 설정 된 것 처럼 처리 됩니다.

이러한 변경으로 인해 잠재적으로 부정적인 영향을 방지 하려면 다음을 수행 하 여 응용 프로그램 프록시 액세스 및 세션 쿠키를 업데이트 합니다.

- **보안 쿠키 사용** 설정의 기본값을 **예**로 설정 합니다.

- **SameSite** 특성의 기본값을 **None**으로 설정 합니다.

    >[!NOTE]
    > 응용 프로그램 프록시 액세스 쿠키는 항상 보안 채널을 통해 단독으로 전송 됩니다. 이러한 변경 내용은 세션 쿠키에만 적용 됩니다.

응용 프로그램 프록시 쿠키 설정에 대 한 자세한 내용은 Azure Active Directory에서 온 [-프레미스 응용 프로그램에 액세스 하기 위한 쿠키 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)을 참조 하세요.

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>응용 프로그램 등록 포털 (apps.dev.microsoft.com)에서 앱 등록 (레거시) 및 수렴 형 앱 관리를 더 이상 사용할 수 없습니다.

**유형:** 변경 계획  
**서비스 범주:** 해당 사항 없음  
**제품 기능:** 개발자 환경

가까운 장래에 Azure AD 계정을 사용 하는 사용자는 apps.dev.microsoft.com (응용 프로그램 등록 포털)를 사용 하 여 수렴 형 응용 프로그램을 더 이상 등록 하 고 관리할 수 없으며, 앱 등록 (레거시)에서 응용 프로그램을 등록 하 고 관리할 수 없습니다. Azure Portal의 경험

새로운 앱 등록 환경에 대해 자세히 알아보려면 [Azure Portal 학습 가이드에서 앱 등록](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)를 참조 하세요.

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>사용자가 더 이상 사용자 단위 MFA에서 조건부 액세스 기반 MFA로 마이그레이션하는 동안 다시 등록할 필요가 없습니다.

**유형:** 수정됨  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

사용자가 MFA (사용자별 Multi-Factor Authentication)에 대해 사용 하지 않도록 설정 된 후 조건부 액세스 정책을 통해 MFA에 대해 사용 하도록 설정 된 경우 사용자가 다시 등록 해야 하는 알려진 문제를 해결 했습니다.

사용자가 다시 등록 하도록 하려면 Azure AD 포털의 사용자 인증 방법에서 **필요한 MFA 다시 등록** 옵션을 선택할 수 있습니다. 사용자별 MFA에서 조건부 액세스 기반 MFA로 사용자를 마이그레이션하는 방법에 대 한 자세한 내용은 [사용자별 mfa 사용자를 조건부 액세스 기반 mfa로 변환](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)을 참조 하세요.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>SAML 토큰에서 클레임을 변환 하 고 전송 하는 새로운 기능

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** SSO

SAML 토큰에서 클레임을 사용자 지정 하 고 전송 하는 데 도움이 되는 추가 기능을 추가 했습니다. 이러한 새로운 기능은 다음과 같습니다.

- 클레임에서 전송 하는 값을 수정 하는 데 도움이 되는 추가 클레임 변환 함수

- 단일 클레임에 여러 변환을 적용할 수 있습니다.

- 사용자 유형 및 사용자가 속한 그룹을 기반으로 클레임 원본을 지정 하는 기능입니다.

이러한 새로운 기능에 대 한 자세한 내용은 [엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조 하세요.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD의 최종 사용자에 대 한 새 내 로그인 페이지

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 모니터링 및 보고

조직의 사용자가 비정상적인 활동을 확인 하는 최근 로그인 기록을 볼 수 있도록 하는 새 **내 로그인** 페이지를 추가 했습니다 (https://mysignins.microsoft.com). 이 새 페이지를 통해 사용자는 다음을 볼 수 있습니다.

- 사용자가 자신의 암호를 추측 하려고 하는 경우

- 공격자가 자신의 계정 및 위치에서 성공적으로 로그인 한 경우

- 공격자가 액세스 하려고 시도한 앱입니다.

자세한 내용은 [사용자가 이제 비정상적인 활동 블로그의 로그인 기록을 확인할 수 있습니다](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .를 참조 하세요.

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>클래식에서 Azure Resource Manager 가상 네트워크로 Azure AD Domain Services (Azure AD DS) 마이그레이션

**유형:** 새 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

기존 가상 네트워크에서 중단 된 고객에 게 좋은 소식이 있습니다. 이제 클래식 가상 네트워크에서 기존 리소스 관리자 가상 네트워크로 일회성 마이그레이션을 수행할 수 있습니다. 리소스 관리자 가상 네트워크로 이동한 후에는 세분화 된 암호 정책, 전자 메일 알림 및 감사 로그와 같은 추가 및 업그레이드 기능을 활용할 수 있습니다.

자세한 내용은 [미리 보기-클래식 가상 네트워크 모델에서 리소스 관리자로 Azure AD Domain Services 마이그레이션을](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)참조 하세요.

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 페이지 계약 레이아웃에 대 한 업데이트

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

Azure AD B2C에 대 한 페이지 계약의 버전 1.2.0에 대 한 몇 가지 새로운 변경 사항이 도입 되었습니다. 이 업데이트 된 버전에서 이제 요소의 로드 순서를 제어할 수 있습니다 .이를 통해 스타일 시트 (CSS)가 로드 될 때 발생 하는 깜박임을 중지할 수도 있습니다.

페이지 계약에 대 한 전체 변경 내용 목록은 [버전 변경 로그](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)를 참조 하세요.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>새 작업 영역과 함께 내 앱 페이지로 업데이트 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 내 앱  
**제품 기능:** Access Control

이제 조직의 사용자가 새 작업 영역 기능을 사용 하 여 앱을 보다 쉽게 찾을 수 있도록 해 주는 새로운 내 앱 환경을 보고 액세스 하는 방법을 사용자 지정할 수 있습니다. 새 작업 영역 기능은 조직의 사용자가 이미 액세스할 수 있는 앱에 대 한 필터 역할을 합니다.

새 내 앱 환경 롤아웃 및 작업 영역 만들기에 대 한 자세한 내용은 [My apps (미리 보기) 포털에서 작업 영역 만들기](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)를 참조 하세요.

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>월간 활성 사용자 기반 청구 모델에 대 한 지원 (일반 공급)

**유형:** 새 기능  
**서비스 범주:** B2C - 소비자 ID 관리  
**제품 기능:** B2B/B2C

이제 Azure AD B2C는 MAU (월간 활성 사용자) 대금 청구를 지원 합니다. MAU 요금은 일정 월 동안 인증 활동이 있는 고유한 사용자 수를 기준으로 합니다. 기존 고객은 언제 든 지이 새로운 청구 방법으로 전환할 수 있습니다.

2019 년 11 월 1 일부 터 모든 신규 고객은이 방법을 사용 하 여 자동으로 청구 됩니다. 이 청구 방법은 비용 혜택 및 미리 계획 하는 기능을 통해 고객에 게 유용 합니다.

자세한 내용은 [월간 활성 사용자 청구 모델으로 업그레이드](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)를 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-10 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

2019 년 10 월에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 35 새 앱을 추가 했습니다.

[위기](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)( [Juno](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita 현금 관리](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [ECornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [netHireVue](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), HireVue, [contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [bindtuning](https://bindtuning.com/login), [HireVue 좌표 – EU](https://www.hirevue.com/), [WittyParrot 좌표-usonly](https://www.hirevue.com/), [좌표의](https://www.hirevue.com/)경우, [ Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail teamie](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [팀을 위한 속도](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [eab 탐색](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [SCREENMEET](https://console.screenmeet.com/), [오메가](https://pi.ompnt.com/), [Intune (iphone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Office 365 Direct (iphone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care 탐색 시스템](https://ihealthnav.com/account/signin) [에 대 한 음성 메일 Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 포털의 통합 보안 메뉴 항목

**유형:** 변경된 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

이제 새 **보안** 메뉴 항목 및 **검색** 표시줄의 Azure Portal에서 사용 가능한 모든 Azure AD 보안 기능에 액세스할 수 있습니다. 또한 **보안 시작**이라는 새로운 **보안** 방문 페이지는 공개 설명서, 보안 지침 및 배포 가이드에 대 한 링크를 제공 합니다.

새 **보안** 메뉴에는 다음이 포함 됩니다.

- 조건부 액세스
- ID 보호
- Security Center
- ID 보안 점수
- 인증 방법
- MFA
- 위험 보고서-위험한 사용자, 위험한 로그인, 위험 검색
- 이 외에도 다양한 혜택이 제공됩니다!

자세한 내용은 [보안-시작](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)을 참조 하세요.

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>자동 갱신로 강화 된 Office 365 그룹 만료 정책

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** ID 수명 주기 관리

해당 구성원 들이 적극적으로 사용 중인 그룹을 자동으로 갱신 하도록 Office 365 그룹 만료 정책이 향상 되었습니다. 그룹은 Outlook, SharePoint 및 팀을 포함 하 여 모든 Office 365 앱의 사용자 활동을 기반으로 autorenewed 됩니다.

이러한 향상 된 기능은 그룹 만료 알림을 줄이고 활성 그룹을 계속 사용할 수 있도록 하는 데 도움이 됩니다. Office 365 그룹에 대 한 활성 만료 정책이 이미 있는 경우이 새 기능을 설정 하기 위해 아무것도 수행할 필요가 없습니다.

자세한 내용은 [Office 365 그룹에 대 한 만료 정책 구성](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)을 참조 하세요.

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>업데이트 된 Azure AD Domain Services (Azure AD DS) 만들기 환경

**유형:** 변경된 기능  
**서비스 범주:** Azure AD Domain Services  
**제품 기능:** Azure AD Domain Services

새로운 향상 된 생성 환경을 포함 하도록 Azure AD Domain Services (Azure AD DS)를 업데이트 하 여 세 번의 클릭 만으로 관리 되는 도메인을 만들 수 있습니다. 또한 이제 템플릿에서 Azure AD DS를 업로드 하 고 배포할 수 있습니다.

자세한 내용은 [자습서: ](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)Azure Active Directory Domain Services 인스턴스를 만들고 구성 합니다.

---

## <a name="september-2019"></a>2019년 9월

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>변경 계획: Power BI 콘텐츠 팩의 사용 중단

**유형:** 변경 계획  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

2019 년 10 월 1 일부 터 Power BI는 Azure AD Power BI 콘텐츠 팩을 비롯 한 모든 콘텐츠 팩에 대 한 사용 중단을 시작 합니다. 이 콘텐츠 팩의 대 안으로 azure ad 통합 문서를 사용 하 여 Azure AD 관련 서비스에 대 한 정보를 얻을 수 있습니다. 보고서 전용 모드의 조건부 액세스 정책에 대 한 통합 문서, 앱 동의 기반 정보 등을 비롯 한 추가 통합 문서가 제공 됩니다.

통합 문서에 대 한 자세한 내용은 [Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)을 참조 하세요. 콘텐츠 팩을 사용 하지 않는 방법에 대 한 자세한 내용은 [발표 Power BI 템플릿 앱 일반](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) 공급 블로그 게시물을 참조 하세요.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>내 프로필이 이름을 바꾸고 Microsoft Office 계정 페이지와 통합 하는 중

**유형:** 변경 계획  
**서비스 범주:** 내 프로필/계정  
**제품 기능:** 공동 작업

10 월부터 내 프로필 환경이 내 계정이 됩니다. 이 변경의 일부로 현재 표시 되는 모든 위치에서 **내 프로필이** **내 계정**으로 변경 됩니다. 이름 변경 및 일부 디자인 개선 사항에 따라 업데이트 된 환경은 Microsoft Office 계정 페이지와의 추가 통합을 제공 합니다. 특히 **개인 정보** 페이지의 office 관련 연락처 기본 설정과 함께 **개요 계정** 페이지에서 office 설치 및 구독에 액세스할 수 있습니다.

내 프로필 (미리 보기) 환경에 대 한 자세한 내용은 [내 프로필 (미리 보기) 포털 개요](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)를 참조 하세요.

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Azure AD 포털에서 CSV 파일을 사용 하 여 그룹 및 구성원 대량 관리 (공개 미리 보기)

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

Azure AD 포털에서 대량 그룹 관리 환경의 공개 미리 보기 가용성을 발표 하 게 되어 기쁘게 생각 합니다. 이제 CSV 파일 및 Azure AD 포털을 사용 하 여 다음을 비롯 한 그룹 및 멤버 목록을 관리할 수 있습니다.

- 그룹에서 구성원 추가 또는 제거

- 디렉터리에서 그룹 목록을 다운로드 하 고 있습니다.

- 특정 그룹에 대 한 그룹 구성원 목록을 다운로드 합니다.

자세한 내용은 [구성원 대량 추가](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), 구성원 [대량 제거](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [구성원 목록 대량 다운로드](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)및 [그룹 대량 다운로드 목록을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)참조 하세요.

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>이제 새 관리자 동의 끝점을 통해 동적 동의가 지원 됩니다.

**유형:** 새 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

Microsoft는 새 관리자 동의 끝점을 만들어 동적 동의를 지원 합니다 .이 끝점은 Microsoft Id 플랫폼에서 동적 승인 모델을 사용 하려는 앱에 유용 합니다.

이 새 끝점을 사용 하는 방법에 대 한 자세한 내용은 [관리자 동의 끝점 사용](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-9 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

9 월 2019에 앱 갤러리에 대 한 페더레이션 지원과 함께 이러한 29 개의 새 앱을 추가 했습니다.

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [Ethidex 규정 준수 Office™에 대 한 MS Azure SSO 액세스-Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iserver Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), [Concur 여행 및 비용](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [근무 보드](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [옛 Eflow](https://apps.yeeflow.com/), [ARC 시설](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [luware Stratus 팀](https://stratus.emea.luware.cloud/login), [광범위 한 아이디어](https://wideideas.online/wideideas/), 권한 [sma 클라우드](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [jdlt 클라이언트 허브](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [sealpath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), 권한 [sma cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [,](https://aec.cintoo.com/login) [Penneo](https://app.penneo.com/) [, Cintoo](https://app.testhtm.com/settings/email-integration) [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [HOSTED Heritage Online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [cakehr](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [coo Kai 팀 빌드](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [검색 이점 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-azure-ad-global-reader-role"></a>새 Azure AD 전역 읽기 권한자 역할

**유형:** 새 기능  
**서비스 범주:** RBAC  
**제품 기능:** Access Control

2019 년 9 월 24 일부 터 전역 읽기 권한자 라는 새로운 AD (Azure Active Directory) 역할 출시를 시작 하겠습니다. 이 출시는 프로덕션 및 GCC (Global cloud customer)부터 10 월에 전 세계에 출시 될 예정입니다.

전역 읽기 권한자 역할은 전역 관리자에 해당 하는 읽기 전용입니다. 이 역할의 사용자는 Microsoft 365 서비스에서 설정 및 관리 정보를 읽을 수 있지만 관리 작업을 수행할 수는 없습니다. 조직의 전역 관리자 수를 줄이는 데 도움이 되는 전역 읽기 권한자 역할을 만들었습니다. 전역 관리자 계정은 강력 하 고 공격에 취약 하기 때문에 전역 관리자를 5 개 미만으로 하는 것이 좋습니다. 계획, 감사 또는 조사를 위해 전역 읽기 권한자 역할을 사용 하는 것이 좋습니다. 또한 전역 관리자 역할을 요구 하지 않고 작업을 수행할 수 있도록 Exchange 관리자와 같은 제한 된 다른 관리자 역할과 함께 전역 읽기 권한자 역할을 사용 하는 것이 좋습니다.

전역 읽기 권한자 역할은 새로운 Microsoft 365 관리 센터, Exchange 관리 센터, 팀 관리 센터, Security Center, 준수 센터, Azure AD 관리 센터 및 장치 관리 관리 센터에서 작동 합니다.

>[!NOTE]
> 공개 미리 보기를 시작할 때 전역 읽기 권한자 역할은 다음에서 작동 하지 않습니다. SharePoint, Privileged Access Management, 고객 Lockbox, 민감도 레이블, 팀 수명 주기, & 호출 분석, 팀 IP 전화 장치 관리 및 팀 앱 카탈로그를 보고 하는 팀입니다. 이러한 모든 서비스는 나중에 역할을 수행 하기 위한 것입니다.

자세한 내용은 [Azure Active Directory의 관리자 역할 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)을 참조 하세요.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Azure Active Directory 응용 프로그램 프록시를 사용 하 여 Power BI Mobile 앱에서 온-프레미스 보고서 서버에 액세스

**유형:** 새 기능  
**서비스 범주:** 앱 프록시  
**제품 기능:** Access Control

Power BI 모바일 앱과 Azure AD 응용 프로그램 프록시 간의 새로운 통합을 통해 Power BI 모바일 앱에 안전 하 게 로그인 하 고 온-프레미스 Power BI Report Server에서 호스트 되는 조직의 보고서를 볼 수 있습니다.

앱을 다운로드할 수 있는 위치를 포함 하 여 Power BI Mobile 앱에 대 한 자세한 내용은 [Power BI 사이트](https://powerbi.microsoft.com/mobile/)를 참조 하십시오. Azure AD 응용 프로그램 프록시를 사용 하 여 Power BI 모바일 앱을 설정 하는 방법에 대 한 자세한 내용은 Azure AD 응용 프로그램 프록시를 사용 하 [여 Power BI Mobile에 원격 액세스 사용](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)을 참조 하세요.

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>AzureADPreview PowerShell 모듈의 새 버전을 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리

다음을 포함 하 여 Azure AD에서 사용자 지정 역할을 정의 하 고 할당 하는 데 도움이 되는 새 cmdlet이 AzureADPreview 모듈에 추가 되었습니다.

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect의 새 버전

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리

자동 업그레이드 고객을 위해 업데이트 된 버전의 Azure AD Connect를 출시 했습니다. 이 새 버전에는 몇 가지 새로운 기능, 향상 된 기능 및 버그 수정이 포함 되어 있습니다. 이 새 버전에 대 한 자세한 내용은 [Azure AD Connect를 참조 하세요. 버전 릴리스 내역](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)을 참조하세요.

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>MFA (Azure Multi-Factor Authentication) 서버, 버전 8.0.2 이제 사용 가능

**유형:** 수정됨  
**서비스 범주:** MFA  
**제품 기능:** ID 보안 및 보호

2019 년 7 월 1 일 이전에 MFA 서버를 활성화 한 기존 고객 인 경우 이제 최신 버전의 MFA 서버 (버전 8.0.2)를 다운로드할 수 있습니다. 새 버전에서는 다음을 수행 합니다.

- Azure AD sync가 사용자를 사용 안 함에서 사용으로 변경 하는 경우 사용자에 게 전자 메일이 전송 되도록 하는 문제가 해결 되었습니다.

- 태그 기능을 계속 사용 하면서 고객이 성공적으로 업그레이드할 수 있도록 문제를 해결 했습니다.

- 코소보 (+ 383) 국가 코드가 추가 되었습니다.

- Multifactorauthsvc.log에 일회성 바이패스 감사 로깅을 추가 했습니다.

- 웹 서비스 SDK에 대 한 성능이 향상 되었습니다.

- 기타 사소한 버그를 수정 했습니다.

2019 년 7 월 1 일부 터 Microsoft는 새 배포를 위한 MFA 서버 제공을 중지 했습니다. Multi-factor authentication을 요구 하는 신규 고객은 클라우드 기반 Azure Multi-Factor Authentication를 사용 해야 합니다. 자세한 내용은 [클라우드 기반 Azure Multi-Factor Authentication 배포 계획](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)을 참조 하세요.

---

## <a name="august-2019"></a>2019년 8월

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Azure AD 포털 (공개 미리 보기)에서 그룹에 대 한 향상 된 검색, 필터링 및 정렬을 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

Azure AD 포털에서 향상 된 그룹 관련 환경의 공개 미리 보기 가용성을 발표 하 게 되어 기쁘게 생각 합니다. 이러한 향상 된 기능을 통해 다음을 제공 하 여 그룹 및 멤버 목록을 보다 효율적으로 관리할 수 있습니다.

- 그룹 목록에 있는 부분 문자열 검색 등의 고급 검색 기능
- 멤버 및 소유자 목록의 고급 필터링 및 정렬 옵션입니다.
- 멤버 및 소유자 목록에 대 한 새로운 검색 기능.
- 그룹 수가 클 경우 그룹 수가 더 정확 합니다.

자세한 내용은 [Azure Portal에서 그룹 관리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)를 참조 하세요.

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>새 사용자 지정 역할을 앱 등록 관리 (공개 미리 보기)에 사용할 수 있습니다.

**유형:** 새 기능  
**서비스 범주:** RBAC  
**제품 기능:** Access Control

Azure AD P1 또는 P2 구독에서 사용할 수 있는 사용자 지정 역할은 이제 특정 권한으로 역할 정의를 만든 다음 특정 리소스에 해당 역할을 할당 하 여 세분화 된 액세스를 제공 하는 데 도움이 됩니다. 현재, 앱 등록을 관리 하는 데 필요한 권한을 사용 하 여 사용자 지정 역할을 만든 다음 특정 앱에 역할을 할당 합니다. 사용자 지정 역할에 대 한 자세한 내용은 [Azure Active Directory (미리 보기)의 사용자 지정 관리자 역할](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)을 참조 하세요.

추가 권한이 나 지원 되는 리소스 (현재 표시 되지 않음)가 필요한 경우 [Azure 사용자 의견 사이트](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) 에 사용자 의견을 보낼 수 있습니다. 그러면 요청을 업데이트 로드맵에 추가할 수 있습니다.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>새 프로 비전 로그를 통해 앱 프로 비전 배포를 모니터링 하 고 문제를 해결할 수 있습니다 (공개 미리 보기).

**유형:** 새 기능  
**서비스 범주:** 앱 프로비전  
**제품 기능:** ID 수명 주기 관리

새 프로 비전 로그를 통해 사용자 및 그룹 프로 비전 배포를 모니터링 하 고 문제를 해결할 수 있습니다. 이러한 새 로그 파일에는 다음에 대 한 정보가 포함 됩니다.

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) 에서 성공적으로 생성 된 그룹
- Amazon Web Services에서 가져온 역할 [(AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) 에서 가져온 직원

자세한 내용은 [Azure Active Directory 포털에서 보고서 프로 비전 (미리 보기)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)을 참조 하세요.

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>모든 Azure AD 관리자에 대 한 새 보안 보고서 (일반 공급)

**유형:** 새 기능  
**서비스 범주:** Identity Protection  
**제품 기능:** ID 보안 및 보호

기본적으로 모든 Azure AD 관리자는 곧 Azure AD 내에서 최신 보안 보고서에 액세스할 수 있습니다. 9 월이 끝날 때까지 최신 보안 보고서 맨 위에 있는 배너를 사용 하 여 이전 보고서로 돌아갈 수 있습니다.

최신 보안 보고서는 다음을 비롯 하 여 이전 버전의 추가 기능을 제공 합니다.

- 고급 필터링 및 정렬
- 사용자 위험 해제와 같은 대량 작업
- 손상 되거나 안전 된 엔터티 확인
- 위험 상태, 다음을 다룹니다. 위험에 노출 됨, 재구성 됨 및 확인 됨
- 새 위험 관련 검색 (Azure AD Premium 구독자가 사용할 수 있음)

자세한 내용은 [위험한 사용자](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [위험한 로그인](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)및 [위험](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)검색을 참조 하세요.

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>사용자 할당 관리 id는 Virtual Machines 및 Virtual Machine Scale Sets에 대해 제공 됩니다 (일반 공급).

**유형:** 새 기능  
**서비스 범주:** Azure 리소스에 대한 관리 ID  
**제품 기능:** 개발자 환경

사용자 할당 관리 id는 이제 Virtual Machines 및 Virtual Machine Scale Sets에 일반적으로 제공 됩니다. 이 과정에서 Azure는 사용 중인 구독에서 신뢰할 수 있는 Azure AD 테 넌 트에 id를 만들고 하나 이상의 Azure 서비스 인스턴스에 할당할 수 있습니다. 사용자 할당 관리 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 란?](https://aka.ms/azuremanagedidentity)을 참조 하세요.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>사용자가 모바일 앱 또는 하드웨어 토큰을 사용 하 여 암호를 재설정할 수 있습니다 (일반 공급).

**유형:** 변경된 기능  
**서비스 범주:** 셀프 서비스 암호 재설정  
**제품 기능:** 사용자 인증

모바일 앱을 조직에 등록 한 사용자는 이제 Microsoft Authenticator 앱에서 알림을 승인 하거나 모바일 앱 또는 하드웨어 토큰에서 코드를 입력 하 여 자신의 암호를 다시 설정할 수 있습니다.

자세한 내용은 작동 방법 [를 참조 하세요. Azure AD 셀프 서비스 암호 재설정](https://aka.ms/authappsspr)을 참조하세요. 사용자 환경에 대 한 자세한 내용은 [자신의 회사 또는 학교 암호 재설정 개요](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)를 참조 하세요.

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET는 시나리오를 대신해 MSAL.NET 공유 캐시를 무시 합니다.

**유형:** 수정됨  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 사용자 인증

Azure AD 인증 라이브러리 (ADAL.NET) 버전 5.0.0-preview부터 앱 개발자는 [웹 앱 및 웹 api에 대 한 계정 당 캐시 하나를 직렬화](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)해야 합니다. 그렇지 않으면 `UserAssertion`의 특정 사용 사례와 함께 [흐름](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)을 사용 하는 일부 시나리오에서 권한 상승이 발생할 수 있습니다. 이 취약점을 방지 하기 위해 ADAL.NET는 이제 시나리오를 대신해 MSAL.NET (Microsoft authentication library for dotnet) 공유 캐시를 무시 합니다.

이 문제에 대 한 자세한 내용은 [인증 라이브러리 권한 상승 취약점 Azure Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)을 참조 하세요.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD 앱 갤러리에서 사용할 수 있는 새 페더레이션된 앱-8 월 2019

**유형:** 새 기능  
**서비스 범주:** 엔터프라이즈 앱  
**제품 기능:** 타사 통합

8 월 2019에 앱 갤러리에 대 한 페더레이션 지원을 통해 이러한 26 개의 새 앱을 추가 했습니다.

[도심 Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [ProNovos Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport의 inativ 포털 (유럽)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [로빈](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [아카데미 참석자](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [우선 순위 행렬](https://sync.appfluence.com/pmwebng/), [Cousto myspace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [cpqsync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), delivery [. media™ Portal](https://portal.deliver.media), [Frontline 교육용](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat AD connect](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial), [ProNovos Analytics](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial), [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [색으로 보기](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [도구](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [eab 탐색 전략적 주의](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

앱에 대한 자세한 내용은 [Azure Active Directory와 SaaS 애플리케이션 통합](https://aka.ms/appstutorial)을 참조하세요. Azure AD 앱 갤러리에 애플리케이션을 나열하는 방법에 대한 자세한 내용은 [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://aka.ms/azureadapprequest)을 참조하세요.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>AzureAD PowerShell 및 AzureADPreview PowerShell 모듈의 새 버전을 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 기타  
**제품 기능:** 디렉터리

AzureAD 및 AzureAD Preview PowerShell 모듈에 대 한 새로운 업데이트를 사용할 수 있습니다.

- AzureAD 모듈의 `Get-AzureADDirectoryRole` 매개 변수에 새 `-Filter` 매개 변수가 추가 되었습니다. 이 매개 변수를 사용 하면 cmdlet에서 반환 되는 디렉터리 역할을 필터링 할 수 있습니다.
- 다음을 포함 하 여 Azure AD에서 사용자 지정 역할을 정의 하 고 할당 하는 데 도움이 되는 새 cmdlet이 AzureADPreview 모듈에 추가 되었습니다.

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure Portal에서 동적 그룹 규칙 작성기 UI의 향상 된 기능

**유형:** 변경된 기능  
**서비스 범주:** 그룹 관리  
**제품 기능:** 공동 작업

Azure Portal에서 사용할 수 있는 동적 그룹 규칙 작성기에 대 한 몇 가지 UI 기능이 향상 되어 새 규칙을 보다 쉽게 설정 하거나 기존 규칙을 변경 하는 데 도움이 됩니다. 이 디자인 개선을 통해 하나의 식 대신 최대 5 개의 식으로 규칙을 만들 수 있습니다. 또한 사용 되지 않는 장치 속성을 제거 하도록 장치 속성 목록을 업데이트 했습니다.

자세한 내용은 [동적 멤버 관리 규칙 관리](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)를 참조 하세요.

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>액세스 검토에 사용할 수 있는 새 Microsoft Graph 앱 권한

**유형:** 변경된 기능  
**서비스 범주:** 액세스 검토  
**제품 기능:** ID 거버넌스

앱에서 그룹 멤버 자격 및 앱 할당에 대 한 액세스 검토를 자동으로 만들고 검색할 수 있도록 하는 새로운 Microsoft Graph 앱 권한 `AccessReview.ReadWrite.Membership`도입 되었습니다. 로그인 한 사용자 컨텍스트를 요구 하지 않고, 예약 된 작업 또는 자동화의 일부로이 사용 권한을 사용할 수 있습니다.

자세한 내용은 [PowerShell 블로그를 사용 하 여 Microsoft Graph 앱 사용 권한을 사용 하 여 AZURE AD 액세스 검토를 만드는 방법 예제](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)를 참조 하세요.

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 활동 로그는 이제 Azure Monitor의 정부 클라우드 인스턴스에 사용할 수 있습니다.

**유형:** 변경된 기능  
**서비스 범주:** 보고  
**제품 기능:** 모니터링 및 보고

이제 Azure AD 활동 로그를 Azure Monitor의 정부 클라우드 인스턴스에 사용할 수 있다는 것을 알게 되어 기쁘게 생각 합니다. 이제 Azure AD 로그를 저장소 계정 또는 이벤트 허브로 전송 하 여 [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)및 [arcsight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)와 같은 siem 도구와 통합할 수 있습니다. 

Azure Monitor 설정에 대 한 자세한 내용은 [Azure Monitor의 AZURE AD 활동 로그](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)를 참조 하세요.

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>사용자를 새롭고 향상 된 보안 정보 환경으로 업데이트 합니다.

**유형:** 변경된 기능  
**서비스 범주:**  인증(로그인)   
**제품 기능:** 사용자 인증

2019 년 9 월 25 일부 터 사용자 보안 정보를 등록 및 관리 하 고 향상 된 새 [버전](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)을 설정 하는 데 사용할 수 있는 이전의 고급 보안 정보 환경을 끄면 됩니다. 즉, 사용자는 더 이상 이전 환경을 사용할 수 없습니다.

향상 된 보안 정보 환경에 대 한 자세한 내용은 [관리자 설명서](https://aka.ms/securityinfodocs) 및 [사용자 설명서](https://aka.ms/securityinfoguide)를 참조 하세요.

#### <a name="to-turn-on-this-new-experience-you-must"></a>이 새로운 환경을 설정 하려면 다음을 수행 해야 합니다.

1. 전역 관리자 또는 사용자 관리자 권한으로 Azure Portal에 로그인 합니다.

2. **Azure Active Directory > 사용자 설정 > 액세스 패널 미리 보기 기능에 대 한 설정 관리**로 이동 합니다.

3. **사용자가 보안 정보를 등록 하 고 관리 하기 위한 미리 보기 기능을 사용할 수 있음-고급** 영역에서 선택을 선택한 다음 사용자 그룹 **을 선택 하거나** **모두** 를 선택 하 여 테 넌 트의 모든 사용자에 대해이 기능을 설정 합니다.

4. **사용자는 보안을 등록 하 고 관리 하기 위해 미리 보기 기능을 사용할 수 있습니다** **정보** 영역에서 **없음**을 선택 합니다.

5. 설정을 저장합니다.

    설정을 저장 한 후에는 더 이상 이전 보안 정보 환경에 액세스할 수 없습니다.

>[!Important]
>2019 년 9 월 25 일 이전에 이러한 단계를 완료 하지 않으면 향상 된 환경에 대 한 Azure Active Directory 테 넌 트가 자동으로 사용 하도록 설정 됩니다. 질문이 있는 경우 registrationpreview@microsoft.com에서 문의해 주시기 바랍니다.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>사후 로그인을 사용 하는 인증 요청은 보다 엄격 하 게 유효성이 검사 됩니다.

**유형:** 변경된 기능  
**서비스 범주:** 인증(로그인)  
**제품 기능:** 표준

2019 년 9 월 2 일부 터 POST 메서드를 사용 하는 인증 요청은 HTTP 표준에 대해 보다 엄격 하 게 유효성이 검사 됩니다. 특히 공백과 큰따옴표 (")는 요청 양식 값에서 더 이상 제거 되지 않습니다. 이러한 변경으로 인해 기존 클라이언트는 중단 되지 않으며, Azure AD로 전송 되는 요청이 매번 안정적으로 처리 되는지 확인 하는 데 도움이 됩니다.

자세한 내용은 [AZURE AD 주요 변경 내용 알림](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)을 참조 하세요.

---
