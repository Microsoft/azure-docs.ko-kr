---
title: AD FS에서 Azure Active Directory로 응용 프로그램 인증 이동
description: 이 문서는 조직에서 페더레이션된 SaaS 애플리케이션에 중점을 두고 애플리케이션을 Azure AD로 이동하는 방법을 이해할 수 있도록 돕기 위한 것입니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9d2f295394d89432f3c6dd99585cc4363d4ff74
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641367"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Active Directory Federation Services에서 Azure Active Directory로 응용 프로그램 인증 이동

[Azure Active Directory (AZURE AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) 는 사용자, 파트너 및 고객에 게 응용 프로그램에 액세스 하 고 모든 플랫폼과 장치에서 공동 작업 하는 단일 id를 제공 하는 범용 id 플랫폼을 제공 합니다. Azure AD에는 [완전 한 id 관리 기능이](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)포함 되어 있습니다. 응용 프로그램 (앱) 인증 및 권한 부여를 Azure AD로 표준화 하면 이러한 기능이 제공 하는 이점을 누릴 수 있습니다. 

> [!NOTE]
> 이 문서에서는 온-프레미스 Active Directory에서 응용 프로그램 인증을 이동 하 고 Azure AD로 Active Directory Federation Services 하는 방법을 집중적으로 설명 합니다. 이 이동 계획에 대 한 개요는 [응용 프로그램 인증을 AZURE AD로 마이그레이션](https://aka.ms/migrateapps/whitepaper) 백서를 참조 하세요. 백서는 마이그레이션, 테스트 및 정보를 계획 하는 방법을 설명 합니다.

## <a name="introduction"></a>소개

사용자 계정이 포함 된 온-프레미스 디렉터리가 있는 경우 사용자가 인증 하는 여러 응용 프로그램이 있을 가능성이 높습니다. 이러한 각 앱은 사용자가 자신의 id를 사용 하 여에 액세스 하도록 구성 됩니다. 


사용자가 온-프레미스 Active Directory에 직접 인증할 수도 있습니다. Active Directory Federation Services (AD FS)는 온-프레미스 id 서비스를 기반으로 하는 표준입니다. AD FS은 사용자가 각 응용 프로그램에 개별적으로 로그인 하도록 요구 하지 않고 신뢰할 수 있는 비즈니스 파트너 간에 SSO (Single Sign-On) 기능을 사용 하는 기능을 확장 합니다. 이를 페더레이션 이라고 합니다.

많은 조직에는 Office 365 및 Azure AD 기반 앱과 함께 AD FS에 직접 페더레이션 된 SaaS (Software as a Service) 또는 사용자 지정 LOB (기간 업무) 앱이 있습니다. 

![온-프레미스에 직접 연결 된 응용 프로그램](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**응용 프로그램 보안을 강화 하려면 온-프레미스 및 클라우드 환경에서 단일 액세스 제어 및 정책 집합을 보유 하는 것이 목표입니다**. 

![Azure AD를 통해 연결 된 응용 프로그램](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>마이그레이션할 앱 유형

Id 및 액세스 관리를 위한 단일 제어 평면을 제공 하므로 모든 응용 프로그램 인증을 Azure AD로 마이그레이션하는 것이 가장 좋습니다.

응용 프로그램은 인증을 위해 최신 또는 레거시 프로토콜을 사용할 수 있습니다. 최신 인증 프로토콜을 사용 하는 응용 프로그램을 먼저 마이그레이션하는 것이 좋습니다 (예: SAML 및 Open ID Connect). 앱 갤러리의 기본 제공 커넥터를 통해 또는 Azure AD에 응용 프로그램을 등록 하 여 Azure AD에서 인증 하도록 이러한 앱을 다시 구성할 수 있습니다. [응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy)를 사용 하 여 이전 프로토콜을 사용 하는 앱을 통합할 수 있습니다. 

자세한 내용은 [어떤 종류의 응용 프로그램을 AZURE AD와 통합할 수](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)있나요?를 참조 하세요.

[Azure AD Connect Health 사용 하도록 설정](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)된 경우 [AD FS 응용 프로그램 활동 보고서를 사용 하 여 응용 프로그램을 Azure AD로 마이그레이션할](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) 수 있습니다. 

### <a name="the-migration-process"></a>마이그레이션 프로세스

앱 인증을 Azure AD로 이동 하는 과정에서 앱 및 구성을 적절 하 게 테스트 합니다. 프로덕션 환경으로 이동 하는 마이그레이션 테스트에는 기존 테스트 환경을 계속 사용 하는 것이 좋습니다. 테스트 환경을 현재 사용할 수 없는 경우 응용 프로그램의 아키텍처에 따라 [Azure App Service](https://azure.microsoft.com/services/app-service/) 또는 [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)를 사용 하 여 설정할 수 있습니다.

앱 구성을 개발할 때 사용할 개별 테스트 Azure AD 테 넌 트를 설정 하도록 선택할 수 있습니다. 

마이그레이션 프로세스는 다음과 같습니다.

**1 단계 – 현재 상태: AD FS을 사용 하 여 프로덕션 앱 인증**

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**2 단계 – 선택 사항: Azure 테 넌 트 테스트를 가리키는 앱의 인스턴스 테스트**

앱의 테스트 인스턴스가 Azure AD 테 넌 트 테스트를 가리키도록 구성을 업데이트 하 고 필요한 변경 작업을 수행 합니다. Azure AD 테 넌 트에서 사용자를 사용 하 여 앱을 테스트할 수 있습니다. 개발 프로세스 중에 [Fiddler](https://www.telerik.com/fiddler) 와 같은 도구를 사용 하 여 요청 및 응답을 비교 하 고 확인할 수 있습니다.

별도의 테스트 테 넌 트를 설정 하는 것이 불가능 한 경우이 단계를 건너뛰고 앱의 테스트 인스턴스를 만들고 아래 3 단계에 설명 된 대로 프로덕션 Azure AD 테 넌 트를 가리키도록 합니다.

![마이그레이션 단계 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**3 단계 – 프로덕션 Azure 테 넌 트를 가리키는 테스트 앱**

앱의 테스트 인스턴스를 Azure의 프로덕션 인스턴스로 가리키도록 구성을 업데이트 합니다. 이제 프로덕션 인스턴스에서 사용자로 테스트할 수 있습니다. 필요한 경우 사용자 전환에 대 한이 문서의 섹션을 검토 합니다.

![마이그레이션 단계 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**4 단계 – 프로덕션 AD 테 넌 트를 가리키는 프로덕션 앱**

프로덕션 응용 프로그램의 구성을 업데이트 하 여 프로덕션 Azure 테 넌 트를 가리키도록 합니다.

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS를 사용 하 여 인증 하는 앱은 사용 권한에 대해 Active Directory 그룹을 사용할 수 있습니다. 마이그레이션을 시작 하기 전에 [Azure AD Connect sync](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 를 사용 하 여 온-프레미스 환경과 Azure AD 간의 id 데이터를 동기화 합니다. 응용 프로그램이 마이그레이션될 때 동일한 사용자에 게 액세스 권한을 부여할 수 있도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인 합니다.

### <a name="line-of-business-lob-apps"></a>LOB (기간 업무) 앱

LOB 앱은 조직에서 내부적으로 개발 하거나 데이터 센터에 설치 된 표준 패키지 제품으로 사용할 수 있습니다. 예제에는 Windows Identity Foundation 및 SharePoint 앱 (SharePoint Online이 아님)을 기반으로 하는 앱이 포함 됩니다. 

OAuth 2.0, Openid connect Connect 또는 WS-FEDERATION을 사용 하는 LOB 앱은 [앱 등록](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)으로 Azure AD와 통합할 수 있습니다. [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 응용 프로그램 페이지에서 SAML 2.0 또는 ws-federation을 [비 갤러리 응용 프로그램](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) 으로 사용 하는 사용자 지정 앱을 통합 합니다.

## <a name="saml-based-single-sign-on"></a>SAML 기반 single sign-on

Saml 기반 [Single Sign-On](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SAML 기반 SSO)에 대해 saml 2.0를 사용 하는 앱은 인증에 대해 구성할 수 있습니다. [Saml 기반 SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)를 사용 하면 사용자가 saml 클레임에서 정의한 규칙에 따라 특정 응용 프로그램 역할에 사용자를 매핑할 수 있습니다. 

SAML 기반 Single Sign-On에 맞게 SaaS 애플리케이션을 구성하려면 [SAML 기반 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)을 참조하세요. 

![SSO SAML 사용자 스크린샷 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
많은 SaaS 응용 프로그램에는 SAML 기반 Single Sign-On의 구성을 단계별로 안내 하는 [응용 프로그램별 자습서](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 가 있습니다.

![앱 자습서](media/migrate-adfs-apps-to-azure/app-tutorial.png)

일부 앱은 쉽게 마이그레이션할 수 있습니다. 사용자 지정 클레임과 같이 더 복잡한 요구 사항이 있는 앱의 경우 Azure AD 및/또는 Azure AD Connect에서 추가로 구성해야 할 수도 있습니다. 지원 되는 클레임 매핑에 대 한 자세한 내용은 [Azure Active Directory에서 클레임 매핑](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)을 참조 하세요.

특성을 매핑할 때 다음 제한 사항에 유의 하세요.

* AD FS에서 실행할 수 있는 모든 특성은 해당 특성이 동기화 된 경우에도 Azure AD에서 SAML 토큰으로 내보낼 특성으로 표시 됩니다. 특성을 편집 하는 경우 값 드롭다운 목록에 Azure AD에서 사용할 수 있는 다양 한 특성이 표시 됩니다. [Azure AD Connect 동기화](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 구성을 확인 하 여 필요한 특성 (예: samAccountName)이 Azure AD와 동기화 되는지 확인 합니다. 확장 특성을 사용 하 여 Azure AD에서 표준 사용자 스키마의 일부가 아닌 클레임을 내보낼 수 있습니다.

* 가장 일반적인 시나리오에서는 NameID 클레임 및 다른 일반 사용자 식별자 클레임만 앱에 필요합니다. 추가 클레임이 필요한 지 확인 하려면 AD FS에서 발급 하는 클레임을 확인 합니다.

* 일부 클레임은 Azure AD에서 보호 되므로 모든 클레임이 문제가 될 수 있는 것은 아닙니다. 

* 암호화 된 SAML 토큰을 사용 하는 기능은 현재 미리 보기로 제공 됩니다. [방법: 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정을](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)참조 하세요.

 

### <a name="software-as-a-service-saas-apps"></a>SaaS (Software as a service) 앱

사용자가 Salesforce, ServiceNow 또는 Workday와 같은 SaaS 앱에 로그인 하 고 AD FS와 통합 된 경우 SaaS 앱에 대해 페더레이션 로그온을 사용 하 게 됩니다. 

대부분의 SaaS 응용 프로그램은 Azure AD에서 이미 구성할 수 있습니다. Microsoft에는  [AZURE AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서 SaaS 앱에 대 한 미리 구성 된 연결이 많기 때문에 더 쉽게 전환할 수 있습니다. SAML 2.0 응용 프로그램은 Azure AD 앱 갤러리를 통하거나 [비 갤러리 응용 프로그램](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app)으로 azure ad와 통합할 수 있습니다. 

OAuth 2.0 또는 Openid connect Connect를 사용 하는 앱은 [앱 등록과](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users)유사 하 게 Azure AD와 통합할 수 있습니다. 레거시 프로토콜을 사용 하는 앱은 azure [AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 를 사용 하 여 azure AD에 인증할 수 있습니다.

SaaS 앱을 등록 하는 데 문제가 있는 경우 [Saas 응용 프로그램 통합 지원 별칭](mailto:SaaSApplicationIntegrations@service.microsoft.com)에 문의할 수 있습니다.

**Sso에 대 한 SAML 서명 인증서**: 서명 인증서는 sso 배포의 중요 한 부분입니다. Azure AD는 서명 인증서를 만들어 SaaS 응용 프로그램에 대 한 SAML 기반 페더레이션된 SSO를 설정 합니다. 갤러리 또는 비 갤러리 응용 프로그램을 추가한 후에는 페더레이션된 SSO 옵션을 사용 하 여 추가 된 응용 프로그램을 구성 합니다. [Azure Active Directory에서 페더레이션된 Single Sign-On에 대 한 인증서 관리](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on)를 참조 하세요.

### <a name="apps-and-configurations-that-can-be-moved-today"></a>오늘 이동할 수 있는 앱 및 구성

지금 쉽게 이동할 수 있는 앱에는 표준 구성 요소 및 클레임 집합을 사용 하는 SAML 2.0 앱이 포함 됩니다.

* 사용자 계정 이름

* 전자 메일 주소

* 이름

* Surname

* SAML **NameID**로 대체되는 특성(Azure AD 메일 특성, 메일 접두사, 직원 ID, 확장 특성(1-15) 또는 온-프레미스 **SamAccountName** 특성 포함). 자세한 내용은 [NameIdentifier 클레임 편집](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)을 참조하세요.

* 사용자 지정 클레임.

다음에는 Azure AD로 마이그레이션하기 위한 추가 구성 단계가 필요 합니다.

* AD FS의 MFA (사용자 지정 권한 부여 또는 Multi-Factor Authentication) 규칙입니다. [AZURE AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) 기능을 사용 하 여 구성 합니다.

* 회신 URL 끝점이 여러 개인 앱 PowerShell 또는 Azure Portal 인터페이스를 사용 하 여 Azure AD에서 구성 합니다.

* SAML 버전 1.1 토큰이 필요한 WS-Federation 앱(예: SharePoint 앱). PowerShell을 사용 하 여 수동으로 구성할 수 있습니다. 갤러리에서 SharePoint 및 SAML 1.1 응용 프로그램에 대 한 사전 통합 된 일반 템플릿을 추가할 수도 있습니다. SAML 2.0 프로토콜을 지원 합니다.

* 복잡 한 클레임 발급 변환 규칙. 지원 되는 클레임 매핑에 대 한 자세한 내용은 다음을 참조 하세요.
   *  [Azure Active Directory의 클레임 매핑](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Azure Active Directory의 엔터프라이즈 애플리케이션에 SAML 토큰에서 발급된 클레임 사용자 지정](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>현재 Azure AD에서 지원되지 않는 앱 및 구성

다음 기능이 필요한 앱은 지금 마이그레이션할 수 없습니다.

**프로토콜 기능**

* WS-TRUST ActAs 패턴에 대 한 지원

* SAML 아티팩트 확인

* 서명 된 SAML 요청의 서명 확인  
서명 된 요청은 수락 되지만 서명이 확인 되지 않습니다.  
Azure AD가 응용 프로그램에서 미리 구성 된 끝점에만 토큰을 반환 하도록 지정 하면 대부분의 경우 서명 확인이 필요 하지 않을 수 있습니다.

**토큰 기능의 클레임**

* 데이터가 Azure AD에 동기화 되지 않은 경우 Azure AD 디렉터리가 아닌 특성 저장소의 클레임입니다. 자세한 내용은 [AZURE AD 동기화 API 개요](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)를 참조 하세요.

* 디렉터리 다중 값 특성의 발급 예를 들어 지금은 프록시 주소에 대해 다중값 클레임을 실행할 수 없습니다.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 앱 설정 매핑

마이그레이션은 온-프레미스에서 애플리케이션을 구성하는 방법을 평가하고 해당 구성을 Azure AD에 매핑하는 것으로 시작됩니다. AD FS와 Azure AD는 비슷하게 작동 하므로 트러스트, 로그인 및 로그 아웃 Url 및 식별자를 구성 하는 개념은 두 경우 모두에 적용 됩니다. Azure AD에서 쉽게 구성할 수 있도록 응용 프로그램의 AD FS 구성 설정을 문서화 합니다.

### <a name="map-app-configuration-settings"></a>응용 프로그램 구성 설정 매핑

다음 표에서는 Azure AD Enterprise 응용 프로그램에 대 한 AD FS 신뢰 당사자 트러스트 간의 설정에 대해 가장 일반적인 몇 가지 매핑을 설명 합니다.

* AD FS – 앱에 대 한 AD FS 신뢰 당사자 트러스트에서 설정을 찾습니다. 신뢰 당사자를 마우스 오른쪽 단추로 클릭 하 고 속성을 선택 합니다. 

* Azure AD – 설정은 각 응용 프로그램의 Single sign-on 속성에서 [Azure Portal](https://portal.azure.com/) 내에 구성 됩니다.

| 구성 설정| AD FS| Azure AD에서을 구성 하는 방법| SAML 토큰 |
| - | - | - | - |
| **앱 로그온 URL** <p>SP (서비스 공급자)가 시작한 SAML 흐름에서 앱에 로그인 하는 데 사용할 수 있는 URL입니다.| 해당 없음| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 회신 URL** <p>Id 공급자 (IdP)의 관점에서 앱의 URL입니다. IdP 사용자가 IdP에 로그인 한 후 사용자 및 토큰을 여기에 보냅니다.  이를 **SAML assertion consumer 엔드포인트**라고도 합니다.| **끝점** 탭을 선택 합니다.| SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 Destination 요소입니다. 예제 값: `https://contoso.my.salesforce.com` |
| **앱 로그아웃 URL** <p>사용자가 앱에서 로그 아웃할 때 "로그 아웃 정리" 요청을 전송 하는 URL입니다. IdP는 다른 모든 앱에서 사용자를 로그 아웃 하는 요청을 보냅니다.| **끝점** 탭을 선택 합니다.| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 식별자** <p>IdP의 관점에서 가져온 앱 식별자입니다. 로그인 URL 값은 종종 식별자에 사용 되지만 항상 그렇지는 않습니다.  앱에서 "엔터티 ID"를 호출 하는 경우도 있습니다.| **식별자** 탭 선택|SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 **대상** 요소에 매핑됩니다. |
| **앱 페더레이션 메타 데이터** <p>앱의 페더레이션 메타 데이터의 위치입니다. IdP에서 엔드포인트 또는 암호화 인증서와 같은 특정 구성 설정을 자동으로 업데이트하는 데 사용합니다.| **모니터링** 탭을 선택 합니다.| 해당 없음. Azure AD는 응용 프로그램 페더레이션 메타 데이터를 직접 사용 하도록 지원 하지 않습니다. 페더레이션 메타 데이터를 수동으로 가져올 수 있습니다.| 해당 없음 |
| **사용자 id/이름 ID** <p>Azure AD 또는 AD FS의 사용자 ID를 앱에 고유하게 표시하는 데 사용되는 특성입니다.  이 특성은 일반적으로 사용자의 UPN 또는 이메일 주소입니다.| 클레임 규칙. 대부분의 경우 클레임 규칙은 NameIdentifier로 끝나는 형식의 클레임을 발급 합니다.| **사용자 특성 및 클레임**헤더에서 식별자를 찾을 수 있습니다. 기본적으로 UPN이 사용 됩니다.| SAML 토큰의 **NameID** 요소에 매핑됩니다. |
| **기타 클레임** <p>IdP에서 앱으로 일반적으로 전송 되는 다른 클레임 정보의 예로는 이름, 성, 전자 메일 주소, 그룹 멤버 자격이 있습니다.| AD FS에서는 신뢰 당사자에 대한 다른 클레임 규칙으로 찾을 수 있습니다.| **클레임 & 사용자 특성**헤더 아래에서 식별자를 찾을 수 있습니다. **보기**를 선택하고 다른 모든 사용자 특성을 편집합니다.| 해당 없음 |


### <a name="map-identity-provider-idp-settings"></a>IdP (지도 Id 공급자) 설정

응용 프로그램이 SSO에 대 한 Azure AD와 AD FS를 가리키도록 구성 합니다. 여기서는 SAML 프로토콜을 사용 하는 SaaS 앱에 집중 하 고 있습니다. 그러나이 개념은 사용자 지정 LOB 앱으로도 확장 됩니다.

> [!NOTE]
> Azure AD의 구성 값은 Azure 테 넌 트 ID가 {테 넌 트 id}를 바꾸고 응용 프로그램 ID가 {응용 프로그램 id}를 대체 하는 패턴을 따릅니다. 이 정보는 Azure Active Directory > 속성의 [Azure Portal](https://portal.azure.com/) 에서 찾을 수 있습니다. 

* 디렉터리 ID를 선택 하 여 테 넌 트 ID를 확인 합니다. 

* 응용 프로그램 id를 선택 하 여 응용 프로그램 id를 확인 합니다.

 개략적인 수준에서 다음 키 SaaS 앱 구성 요소를 Azure AD에 매핑합니다. 

 

| 요소| 구성 값 |
| - | - |
| Id 공급자 발급자| https: \/ /sts.windows.net/{tenant-id}/ |
| Id 공급자 로그인 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Id 공급자 로그 아웃 URL| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| 페더레이션 메타 데이터 위치| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>SaaS 앱에 대 한 SSO 설정 매핑

SaaS 앱은 인증 요청을 보내는 위치와 받은 토큰의 유효성을 검사 하는 방법을 알고 있어야 합니다. 다음 표에서는 앱의 SSO 설정을 구성 하는 요소, AD FS 및 Azure AD 내의 해당 값 또는 위치를 설명 합니다.

| 구성 설정| AD FS| Azure AD에서을 구성 하는 방법 |
| - | - | - |
| **IdP Sign-on URL** <p>앱의 관점에서 IdP의 로그인 URL입니다 (사용자가 로그인을 위해 리디렉션 됨).| AD FS sign-on URL은 AD FS 페더레이션 서비스 이름 뒤에 "/adfs/ls/."가 옵니다. <p>예: `https://fs.contoso.com/adfs/ls/`| {Tenant-id}를 테 넌 트 ID로 바꿉니다. <p> SAML-P 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>WS-FEDERATION 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 로그 아웃 URL**<p>앱의 관점에서 IdP의 로그 아웃 URL (사용자가 앱에서 로그 아웃 하도록 선택할 때 리디렉션되는 위치)입니다.| 로그 아웃 URL은 로그온 URL과 동일 하거나 "wa = wsignout1.0 1.0"이 추가 된 URL과 동일 합니다. 예: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| {Tenant-id}를 테 넌 트 ID로 바꿉니다.<p>SAML-P 프로토콜을 사용 하는 앱의 경우:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> WS-FEDERATION 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **토큰 서명 인증서**<p>IdP는 인증서의 개인 키를 사용 하 여 발급 된 토큰에 서명 합니다. 앱이 신뢰하도록 구성된 것과 동일한 IdP에서 토큰이 제공되었는지 확인합니다.| AD FS 토큰 서명 인증서는 AD FS 관리의 **인증서** 아래에 있습니다.| **SAML 서명 인증서**헤더의 응용 프로그램 **Single sign-on 속성** 에 있는 Azure Portal에서 찾습니다. 여기서는 앱에 업로드할 인증서를 다운로드할 수 있습니다.  <p>응용 프로그램에 둘 이상의 인증서가 있는 경우 페더레이션 메타 데이터 XML 파일에서 모든 인증서를 찾을 수 있습니다. |
| **식별자/"issuer"**<p>앱의 관점에서 IdP의 식별자입니다 ("발급자 ID" 라고도 함).<p>SAML 토큰에서 값은 Issuer 요소로 표시 됩니다.| AD FS에 대 한 식별자는 일반적으로 AD FS 관리에서 **서비스 > 편집 페더레이션 서비스 속성**에 있는 페더레이션 서비스 식별자입니다. 예: `http://fs.contoso.com/adfs/services/trust`| {Tenant-id}를 테 넌 트 ID로 바꿉니다.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **IdP 페더레이션 메타 데이터**<p>IdP의 공개적으로 사용할 수 있는 페더레이션 메타 데이터의 위치입니다. (일부 앱은 URL, 식별자 및 토큰 서명 인증서를 개별적으로 구성하는 관리자 대신 연합 메타데이터를 사용합니다.)| **서비스 > > > 끝점**아래의 AD FS 관리에서 페더레이션 메타 데이터 URL AD FS 찾습니다. 예: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD에 대 한 해당 값은 패턴을 따릅니다 [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . {TenantDomainName}을 "contoso.onmicrosoft.com" 형식의 테 넌 트 이름으로 바꿉니다.   <p>자세한 내용은 [페더레이션 메타데이터](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata)를 참조하세요. |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD에서 AD FS 보안 정책을 나타냅니다.

앱 인증을 Azure AD로 이동 하는 경우 기존 보안 정책에서 Azure AD에서 사용할 수 있는 동등한 또는 대체 변형으로 매핑을 만듭니다. 앱 소유자에 게 필요한 보안 표준을 충족 하는 동안 이러한 매핑을 수행할 수 있도록 하면 앱 마이그레이션의 나머지 부분을 훨씬 쉽게 만들 수 있습니다.

각 규칙 유형 및 해당 예제에 대해 AD FS, AD FS 규칙 언어와 동일한 코드 및 Azure AD의이 맵 방식에서 규칙의 모양에 대 한 것이 좋습니다.

### <a name="map-authorization-rules"></a>매핑 권한 부여 규칙

다음은 AD FS의 권한 부여 규칙 유형 및 Azure AD에 매핑할 수 있는 방법의 예입니다.

#### <a name="example-1-permit-access-to-all-users"></a>예제 1: 모든 사용자에 대 한 액세스 허용

모든 사용자에 대 한 액세스 허용은 AD FS에서와 같습니다. 

![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


이는 다음 방법 중 하나로 Azure AD에 매핑됩니다.

[Azure Portal](https://portal.azure.com/)에서 다음을 수행 합니다.
* 옵션 1: 사용자 할당 필요를 아니요로 설정 ![SaaS 앱에 대 한 액세스 제어 정책 편집 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    사용자 할당 필요를 예로 설정 하려면 응용 프로그램에 사용자를 할당 하 여 액세스 권한을 얻어야 합니다. 아니요로 설정 하면 모든 사용자가 액세스할 수 있습니다. 이 스위치는 내 앱 환경에서 사용자에 게 표시 되는 항목을 제어 하지 않습니다. 

 
* 옵션 2: 사용자 및 그룹 탭에서 "모든 사용자" 자동 그룹에 응용 프로그램을 할당 합니다. <p>
기본 ' 모든 사용자 ' 그룹을 사용할 수 있도록 하려면 Azure AD 테 넌 트에서 [동적 그룹을 사용 하도록 설정](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) 해야 합니다.

   ![Azure AD의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>예제 2: 그룹을 명시적으로 허용

AD FS에서 명시적 그룹 권한 부여:


![발급 권한 부여 규칙 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


규칙이 Azure AD에 매핑되는 방식입니다.

[Azure Portal](https://portal.azure.com/)에서 먼저 AD FS의 사용자 그룹에 해당 하는 [사용자 그룹을 만든](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 다음, 해당 그룹에 앱 사용 권한을 할당 합니다.

![할당 추가 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>예 3: 특정 사용자 권한 부여

AD FS에서 명시적 사용자 권한 부여:

![발급 권한 부여 규칙 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

규칙이 Azure AD에 매핑되는 방식입니다.

[Azure Portal](https://portal.azure.com/)에서 아래와 같이 앱의 할당 추가 탭을 통해 사용자를 앱에 추가 합니다.

![Azure의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Multi-Factor Authentication 규칙 매핑 

[Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) 및 AD FS의 온-프레미스 배포는 AD FS 페더레이션 되었으므로 마이그레이션 후에도 계속 작동 합니다. 그러나 Azure AD의 조건부 액세스 워크플로에 연결 된 Azure의 기본 제공 MFA 기능으로 마이그레이션하는 것이 좋습니다. 

다음은 AD FS의 MFA 규칙 유형 및 다양 한 조건에 따라 Azure AD에 매핑할 수 있는 방법의 예입니다.

AD FS의 MFA 규칙 설정:

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>예제 1: 사용자/그룹에 따라 MFA 적용

사용자/그룹 선택기는 그룹 단위 (그룹 SID) 또는 사용자별 (주 SID)를 기준으로 MFA를 적용할 수 있는 규칙입니다. 사용자/그룹 할당 외에도 AD FS MFA 구성 UI의 모든 추가 확인란은 사용자/그룹 규칙이 적용 된 후 평가 되는 추가 규칙으로 작동 합니다. 


Azure AD에서 사용자 또는 그룹에 대 한 MFA 규칙 지정:

1. [새 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

2. **할당**을 선택합니다. MFA를 적용 하려는 사용자 또는 그룹을 추가 합니다.

3. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.  
‎

![AAD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>예제 2: 등록 되지 않은 장치에 대 한 MFA 적용

Azure AD에서 등록 되지 않은 장치에 대 한 MFA 규칙 지정:

1. [새 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

2. **모든 사용자**에 게 **할당** 을 설정 합니다.

3. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.  
‎

![AAD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
선택 된 컨트롤 중 하나를 요구 하도록 여러 컨트롤에 대해 옵션을 설정 하는 경우 해당 확인란에 지정 된 조건 중 하나가 사용자에 의해 충족 되는 경우 앱에 대 한 액세스 권한이 부여 됩니다.

#### <a name="example-3-enforce-mfa-based-on-location"></a>예제 3: 위치에 따라 MFA 적용

Azure AD에서 사용자의 위치에 따라 MFA 규칙을 지정 합니다.

1. [새 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)만듭니다.

1. **모든 사용자**에 게 **할당** 을 설정 합니다.

1. [AZURE AD의 명명 된 위치를 구성](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) 합니다. 그렇지 않으면 회사 네트워크 내부의 페더레이션을 신뢰할 수 있습니다. 

1. **조건 규칙** 을 구성 하 여 MFA를 적용할 위치를 지정 합니다.

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.


![액세스 제어 정책 매핑](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>지도 내보내기 특성을 클레임 규칙으로

AD FS에서 특성이 매핑되는 방법의 예는 다음과 같습니다.

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


규칙이 Azure AD에 매핑되는 방식입니다.

[Azure Portal](https://portal.azure.com/)에서 **엔터프라이즈 응용 프로그램**, **Single sign-on**을 선택 하 고 아래와 같이 **SAML 토큰 특성** 을 추가 합니다.

![Azure AD MFA 설정](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>기본 제공 액세스 제어 정책 매핑

AD FS 2016에는 다음 중에서 선택할 수 있는 몇 가지 기본 제공 액세스 제어 정책이 있습니다.

![Azure AD 기본 제공 access control](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Azure AD에서 기본 제공 정책을 구현 하려면 [새 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) 사용 하 고 액세스 제어를 구성 하거나 AD FS 2016의 사용자 지정 정책 디자이너를 사용 하 여 액세스 제어 정책을 구성할 수 있습니다. 규칙 편집기에는 모든 종류의 순열을 만드는 데 도움이 되는 모든 허용 및 제외 옵션 목록이 있습니다.

![Azure AD 액세스 제어 정책](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



이 테이블에는 몇 가지 유용한 허용 및 제외 옵션과 Azure AD에 매핑되는 방법이 나와 있습니다. 


| 옵션 | Azure AD에서 허용 옵션을 구성 하는 방법| Azure AD에서 Except 옵션을 구성 하는 방법 |
| - | - | - |
| 특정 네트워크| Azure AD의 [명명 된 위치](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) 에 매핑됩니다.| [신뢰할 수 있는 위치](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) 에 **제외** 옵션 사용 |
| 특정 그룹| [사용자/그룹 할당 설정](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| 사용자 및 그룹에서 **제외** 옵션 사용 |
| 특정 신뢰 수준이 있는 장치에서| 할당-> 조건 아래의 ' 장치 상태 ' 컨트롤에서이를 설정 합니다.| 장치 상태 조건에서 **제외** 옵션을 사용 하 고 **모든 장치** 포함 |
| 요청에서 특정 클레임 사용| 이 설정은 마이그레이션할 수 없습니다.| 이 설정은 마이그레이션할 수 없습니다. |


Azure Portal에서 신뢰할 수 있는 위치에 대 한 제외 옵션을 구성 하는 방법의 예는 다음과 같습니다.

![액세스 제어 정책 매핑 스크린샷](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>사용자를 AD FS에서 Azure AD로 전환

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD에서 AD FS 그룹 동기화

권한 부여 규칙을 매핑할 때 AD FS를 사용 하 여 인증 하는 앱은 사용 권한에 대해 Active Directory 그룹을 사용할 수 있습니다. 이러한 경우 응용 프로그램을 마이그레이션하기 전에 [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) 를 사용 하 여 이러한 그룹을 Azure AD와 동기화 합니다. 응용 프로그램을 마이그레이션할 때 동일한 사용자에 게 액세스 권한을 부여할 수 있도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인 해야 합니다.

자세한 내용은 [Active Directory에서 동기화 된 그룹 특성을 사용 하기 위한 필수 조건](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)을 참조 하세요.

### <a name="setup-user-self-provisioning"></a>사용자 자동 프로 비전 설정 

일부 SaaS 응용 프로그램은 사용자가 응용 프로그램에 처음 로그인 할 때 사용자를 자동으로 프로 비전 하는 기능을 지원 합니다. Azure AD(Azure Active Directory)에서 앱 프로비저닝이라는 용어는 사용자가 액세스해야 하는 클라우드([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 애플리케이션에서 사용자 ID와 역할을 자동으로 만드는 것을 의미합니다. 마이그레이션된 사용자는 이미 SaaS 응용 프로그램에 계정이 있습니다. 마이그레이션 후에 추가 된 모든 새 사용자를 프로 비전 해야 합니다. 응용 프로그램이 마이그레이션되면 [SaaS 앱 프로 비전](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) 을 테스트 합니다.

### <a name="sync-external-users-in-azure-ad"></a>Azure AD에서 외부 사용자 동기화

기존 외부 사용자는 AD FS 내에서 다음과 같은 두 가지 주요 방법으로 설정할 수 있습니다.

#### <a name="external-users-with-a-local-account-within-your-organization"></a>조직 내 로컬 계정을 사용 하는 외부 사용자

내부 사용자 계정이 작동 하는 것과 동일한 방식으로 이러한 계정을 계속 사용할 수 있습니다. 이러한 외부 사용자 계정에는 조직 내에서의 계정 이름이 있습니다. 단, 계정 메일은 외부에서 가리킬 수 있습니다. 마이그레이션을 진행 하면서 이러한 id를 사용할 수 있는 경우 해당 사용자를 마이그레이션하여 자신의 회사 id를 사용 하도록 하 여 [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) 에서 제공 하는 이점을 활용할 수 있습니다. 이렇게 하면 해당 사용자에 대 한 로그인 프로세스가 간단해 집니다. 이러한 사용자는 회사 로그온을 사용 하 여 로그인 하는 경우가 많습니다. 외부 사용자에 대 한 계정을 더 이상 관리할 필요 없이 조직의 관리도 줄어들 됩니다.

#### <a name="federated-external-identities"></a>페더레이션된 외부 Id

현재 외부 조직과 페더레이션 하는 경우 몇 가지 방법을 사용할 수 있습니다.

* [Azure Portal에 AZURE ACTIVE DIRECTORY B2B 공동 작업 사용자를 추가](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator)합니다. Azure AD 관리 포털의 B2B 공동 작업 초대를 Azure AD 관리 포털에서 파트너 조직에 사전에 전송 하 여 사용 되는 앱 및 자산을 계속 사용할 수 있습니다. 

* B2B 초대 API를 사용 하 여 파트너 조직에서 개별 사용자에 대 한 요청을 생성 하는 [셀프 서비스 b2b 등록 워크플로를 만듭니다](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) .

기존 외부 사용자를 구성 하는 방법에 관계 없이 그룹 멤버 자격 또는 특정 권한에서 해당 계정과 연결 된 사용 권한이 있을 수 있습니다. 이러한 사용 권한을 마이그레이션하거나 정리 해야 하는지 여부를 평가 합니다. 외부 사용자를 나타내는 조직 내의 계정은 사용자가 외부 id로 마이그레이션된 후에 사용 하지 않도록 설정 해야 합니다. 리소스에 연결 하는 기능이 중단 될 수 있으므로 마이그레이션 프로세스는 비즈니스 파트너와 논의 해야 합니다.

## <a name="migrate-and-test-your-apps"></a>앱 마이그레이션 및 테스트

이 문서에서 자세히 설명 하는 마이그레이션 프로세스를 따르세요.

그런 다음 [Azure Portal](https://aad.portal.azure.com/) 로 이동 하 여 마이그레이션이 성공 했는지 테스트 합니다. 아래의 지침을 따르세요.
1. **엔터프라이즈 응용 프로그램**  >  **모든 응용** 프로그램을 선택 하 고 목록에서 앱을 찾습니다.

1. **Manage**  >  **사용자 및** 그룹 관리를 선택 하 여 하나 이상의 사용자 또는 그룹을 앱에 할당 합니다.

1. **Manage**  >  **조건부 액세스**관리를 선택 합니다. 정책 목록을 검토 하 고 [조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)사용 하 여 응용 프로그램에 대 한 액세스를 차단 하지 않는지 확인 합니다.

앱을 구성 하는 방법에 따라 SSO가 제대로 작동 하는지 확인 합니다. 

| 인증 유형| 테스트 |
| - | - |
| OAuth/Openid connect Connect| **엔터프라이즈 응용 프로그램 > 권한** 을 선택 하 고 앱에 대 한 사용자 설정에서 조직에 사용할 응용 프로그램에 대 한 동의한 있는지 확인 합니다.  
‎ |
| SAML 기반 SSO| **Single sign-on**아래에 있는 [SAML 설정 테스트](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) 단추를 사용 합니다.  
‎ |
| 암호 기반 SSO| [Myapps 보안 로그인](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)확장을 다운로드 하 여 설치 합니다 [-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction) [in Extension](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction). 이 확장은 SSO 프로세스를 사용 해야 하는 조직의 클라우드 앱을 시작 하는 데 도움이 됩니다.  
‎ |
| 애플리케이션 프록시| 커넥터가 실행 중이 고 응용 프로그램에 할당 되었는지 확인 합니다. 자세한 내용은 [응용 프로그램 프록시 문제 해결 가이드](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot) 를 참조 하세요.  
‎ |

> [!NOTE]
> 이전 AD FS 환경의 쿠키는 계속 해 서 사용자 컴퓨터에서 지속 됩니다. 이러한 쿠키는 사용자가 이전 AD FS 로그인 환경 및 새 Azure AD 로그인으로 이동할 수 있으므로 마이그레이션과 관련 된 문제를 일으킬 수 있습니다. 사용자 브라우저 쿠키를 수동으로 지우거 나 스크립트를 사용 하 여 지워야 할 수 있습니다. System Center Configuration Manager 또는 유사한 플랫폼을 사용할 수도 있습니다.

### <a name="troubleshoot"></a>문제 해결

마이그레이션된 응용 프로그램의 테스트에서 오류가 발생 하는 경우 기존 AD FS 신뢰 당사자에 게 다시 대체 하기 전에 문제 해결이 첫 번째 단계 일 수 있습니다. [Azure Active Directory의 응용 프로그램에 대 한 SAML 기반 Single Sign-On 디버그 하는 방법을](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues)참조 하세요.

### <a name="rollback-migration"></a>마이그레이션 롤백

마이그레이션이 실패 하면 AD FS 서버에 기존 신뢰 당사자를 그대로 두고 신뢰 당사자에 대 한 액세스 권한을 제거 하는 것이 좋습니다. 이렇게 하면 배포 중에 필요한 경우 빠른 대체를 수행할 수 있습니다.

### <a name="end-user-communication"></a>최종 사용자 통신

계획 된 가동 중단 창의 경우에는 최소화 하는 것이 가능 하지만, AD FS Azure AD로의 전환을 수행 하는 동안 이러한 기간을 직원에 게 사전에 전달 하는 계획을 세워야 합니다. 앱 환경에 피드백 단추가 있는지 확인 하거나 기술 지원팀에 문의 하 여 문제를 해결 합니다.

배포가 완료 되 면 성공적인 배포를 사용자에 게 알리는 통신을 전송 하 고 수행 해야 하는 새로운 단계를 알릴 수 있습니다.

* 사용자가 [내 앱](https://myapps.microsoft.com) 을 사용 하 여 마이그레이션된 모든 응용 프로그램에 액세스 하도록 지시 합니다. 

* 사용자에 게 MFA 설정을 업데이트 해야 할 수 있는 사용자를 알려 줍니다. 

* 셀프 서비스 암호 재설정을 배포 하는 경우 사용자는 인증 방법을 업데이트 하거나 확인 해야 할 수 있습니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조 하세요.

외부 사용자와의 통신:이 사용자 그룹은 일반적으로 문제의 경우 가장 심각한 영향을 받습니다. 보안 상태에서 외부 파트너에 대 한 조건부 액세스 규칙이 나 위험 프로필의 다른 집합을 지정 하는 경우 특히 그렇습니다. 외부 파트너는 클라우드 마이그레이션 일정을 인식 하 고 외부 공동 작업에 고유한 모든 흐름을 테스트 하는 파일럿 배포에 참여 하는 것이 좋습니다. 마지막으로, 문제가 발생할 경우 기술 지원팀에 액세스할 수 있는 방법이 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계
[AZURE AD로 응용 프로그램 인증 마이그레이션](https://aka.ms/migrateapps/whitepaper) 읽기<p>
[조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 및 [MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 설정
