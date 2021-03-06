---
title: AD FS에서 Azure Active Directory로 응용 프로그램 인증 이동
description: Azure Active Directory를 사용 하 여 Active Directory Federation Services (AD FS)를 바꾸어 사용자에 게 모든 응용 프로그램에 대 한 Single Sign-On를 제공 하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: ee1d863ccb974b30213179a1aba9e27d5a3a2bda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103418470"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Active Directory Federation Services에서 Azure Active Directory로 애플리케이션 인증 이동

[Azure Active Directory (AZURE AD)](../fundamentals/active-directory-whatis.md) 는 사용자, 파트너 및 고객에 게 응용 프로그램에 액세스 하 고 모든 플랫폼과 장치에서 공동 작업 하는 단일 id를 제공 하는 범용 id 플랫폼을 제공 합니다. Azure AD에는 [완전 한 id 관리 기능이](../fundamentals/active-directory-whatis.md)포함 되어 있습니다. 응용 프로그램 인증 및 권한 부여를 Azure AD로 표준화 하면 다음과 같은 이점이 제공 됩니다.

> [!TIP]
> 이 문서는 개발자를 대상으로 작성 되었습니다. 프로젝트 관리자와 관리자가 Azure AD로 응용 프로그램을 이동할 계획인 경우 [AZURE ad로 응용 프로그램 인증을 마이그레이션하](migrate-application-authentication-to-azure-active-directory.md)는 것을 고려해 야 합니다.

## <a name="azure-ad-benefits"></a>Azure AD 혜택

사용자 계정이 포함 된 온-프레미스 디렉터리가 있는 경우 사용자가 인증 하는 여러 응용 프로그램이 있을 가능성이 높습니다. 이러한 각 앱은 사용자가 자신의 id를 사용 하 여에 액세스 하도록 구성 됩니다.

사용자가 온-프레미스 Active Directory에 직접 인증할 수도 있습니다. Active Directory Federation Services (AD FS)는 표준 기반 온-프레미스 id 서비스입니다. 사용자가 각 응용 프로그램에 개별적으로 로그인 할 필요가 없도록 트러스트 된 비즈니스 파트너 간에 SSO (Single Sign-On) 기능을 사용 하는 기능을 확장 합니다. 이를 페더레이션된 id 라고 합니다.

많은 조직에는 Microsoft 365 및 Azure AD 기반 앱과 함께 AD FS에 직접 페더레이션 된 SaaS (Software as a Service) 또는 사용자 지정 lob (기간 업무) 앱이 있습니다.

  ![온-프레미스에 직접 연결 된 응용 프로그램](media/migrate-adfs-apps-to-azure/app-integration-before-migration-1.png)

> [!Important]
> 응용 프로그램 보안을 강화 하려면 온-프레미스 및 클라우드 환경에서 단일 액세스 제어 및 정책 집합을 보유 하는 것이 목표입니다.

  ![Azure AD를 통해 연결 된 응용 프로그램](media/migrate-adfs-apps-to-azure/app-integration-after-migration-1.png)

## <a name="types-of-apps-to-migrate"></a>마이그레이션할 앱 유형

Id 및 액세스 관리를 위한 단일 제어 평면을 제공 하므로 모든 응용 프로그램 인증을 Azure AD로 마이그레이션하는 것이 가장 좋습니다.

응용 프로그램은 인증을 위해 최신 또는 레거시 프로토콜을 사용할 수 있습니다. Azure AD로의 마이그레이션을 계획 하는 경우 최신 인증 프로토콜 (예: SAML 및 Open ID Connect)을 사용 하는 앱을 먼저 마이그레이션하는 것이 좋습니다. 이러한 앱은 Azure 앱 갤러리에서 기본 제공 커넥터를 통해 또는 Azure AD에 응용 프로그램을 등록 하 여 Azure AD에서 인증 하도록 다시 구성할 수 있습니다. 이전 프로토콜을 사용 하는 앱은 응용 프로그램 프록시를 사용 하 여 통합할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Azure AD 응용 프로그램 프록시를 사용 하 여 원격 사용자에 대 한 온-프레미스 앱을 게시](what-is-application-proxy.md)합니다.
* [애플리케이션 관리란?](what-is-application-management.md)
* [응용 프로그램 활동 보고서를 AD FS 하 여 응용 프로그램을 AZURE AD로 마이그레이션합니다](migrate-adfs-application-activity.md).
* [Azure AD Connect Health를 사용 하 여 AD FS를 모니터링](../hybrid/how-to-connect-health-adfs.md)합니다.

### <a name="the-migration-process"></a>마이그레이션 프로세스

앱 인증을 Azure AD로 이동 하는 프로세스 중에 앱 및 구성을 테스트 합니다. 프로덕션 환경으로 이동할 때 마이그레이션 테스트에 기존 테스트 환경을 계속 사용 하는 것이 좋습니다. 테스트 환경을 현재 사용할 수 없는 경우 응용 프로그램의 아키텍처에 따라 [Azure App Service](https://azure.microsoft.com/services/app-service/) 또는 [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)를 사용 하 여 설정할 수 있습니다.

앱 구성을 개발할 별도의 Azure AD 테 넌 트를 설정 하도록 선택할 수 있습니다.

마이그레이션 프로세스는 다음과 같습니다.

#### <a name="stage-1--current-state-the-production-app-authenticates-with-ad-fs"></a>1 단계 – 현재 상태: 프로덕션 앱이 AD FS으로 인증

  ![마이그레이션 단계 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

#### <a name="stage-2--optional-point-a-test-instance-of-the-app-to-the-test-azure-ad-tenant"></a>2 단계 – (선택 사항) 앱의 테스트 인스턴스가 Azure AD 테 넌 트 테스트로 가리키기

앱의 테스트 인스턴스가 Azure AD 테 넌 트 테스트를 가리키도록 구성을 업데이트 하 고 필요한 변경 작업을 수행 합니다. Azure AD 테 넌 트에서 사용자를 사용 하 여 앱을 테스트할 수 있습니다. 개발 프로세스 중에 [Fiddler](https://www.telerik.com/fiddler) 와 같은 도구를 사용 하 여 요청 및 응답을 비교 하 고 확인할 수 있습니다.

별도의 테스트 테 넌 트를 설정 하는 것이 불가능 한 경우 아래 3 단계에 설명 된 대로이 단계를 건너뛰고 앱의 테스트 인스턴스를 프로덕션 Azure AD 테 넌 트로 가리킵니다.

  ![마이그레이션 단계 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

#### <a name="stage-3--point-a-test-instance-of-the-app-to-the-production-azure-ad-tenant"></a>3 단계 – 앱의 테스트 인스턴스를 프로덕션 Azure AD 테 넌 트로 가리키기

앱의 테스트 인스턴스가 프로덕션 Azure AD 테 넌 트에 가리키도록 구성을 업데이트 합니다. 이제 프로덕션 테 넌 트에서 사용자로 테스트할 수 있습니다. 필요한 경우 사용자 전환에 대 한이 문서의 섹션을 검토 합니다.

  ![마이그레이션 단계 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

#### <a name="stage-4--point-the-production-app-to-the-production-azure-ad-tenant"></a>4 단계 – 프로덕션 앱을 프로덕션 Azure AD 테 넌 트로 가리키기

프로덕션 앱의 구성을 업데이트 하 여 프로덕션 Azure AD 테 넌 트를 가리키도록 합니다.

  ![마이그레이션 4 단계 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 AD FS를 사용 하 여 인증 하는 앱은 사용 권한에 대해 Active Directory 그룹을 사용할 수 있습니다. 마이그레이션을 시작 하기 전에 [Azure AD Connect sync](../hybrid/how-to-connect-sync-whatis.md) 를 사용 하 여 온-프레미스 환경과 Azure AD 간의 id 데이터를 동기화 합니다. 응용 프로그램이 마이그레이션될 때 동일한 사용자에 게 액세스 권한을 부여할 수 있도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인 합니다.

### <a name="line-of-business-apps"></a>Lob (기간 업무) 앱

Lob (기간 업무) 앱은 조직에서 개발한 앱 또는 표준 패키지 제품인 제품입니다. 예제에는 Windows Identity Foundation 및 SharePoint 앱 (SharePoint Online이 아님)을 기반으로 하는 앱이 포함 됩니다.

OAuth 2.0, Openid connect Connect 또는 WS-Federation를 사용 하는 lob (기간 업무) 앱은 [앱 등록](../develop/quickstart-register-app.md)으로 Azure AD와 통합할 수 있습니다. [Azure Portal](https://portal.azure.com/)의 엔터프라이즈 응용 프로그램 페이지에서 SAML 2.0 또는 WS-Federation를 [비 갤러리 응용 프로그램](add-application-portal.md) 으로 사용 하는 사용자 지정 앱을 통합 합니다.

## <a name="saml-based-single-sign-on"></a>SAML 기반 Single Sign-On

인증에 SAML 2.0을 사용 하는 앱은 [saml 기반](what-is-single-sign-on.md) SSO (Single Sign-On)에 대해 구성할 수 있습니다. SAML 기반 SSO를 사용 하면 사용자가 SAML 클레임에서 정의한 규칙에 따라 특정 응용 프로그램 역할에 사용자를 매핑할 수 있습니다.

SAML 기반 SSO에 대 한 SaaS 응용 프로그램을 구성 하려면 [빠른 시작: saml 기반 Single Sign-On 설정](add-application-portal-setup-sso.md)을 참조 하세요.

  ![SSO SAML 사용자 스크린샷 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

많은 SaaS 응용 프로그램에는 SAML 기반 SSO에 대 한 구성을 단계별로 안내 하는 [응용 프로그램별 자습서](../saas-apps/tutorial-list.md) 가 있습니다.

  ![앱 자습서](media/migrate-adfs-apps-to-azure/app-tutorial.png)

일부 앱은 쉽게 마이그레이션할 수 있습니다. 사용자 지정 클레임과 같이 더 복잡 한 요구 사항이 있는 앱의 경우 Azure AD 및/또는 Azure AD Connect에서 추가 구성이 필요할 수 있습니다. 지원 되는 클레임 매핑에 대 한 자세한 내용은 [방법: 테 넌 트의 특정 앱에 대 한 토큰에서 내보낸 클레임 사용자 지정 (미리 보기)](../develop/active-directory-claims-mapping.md)을 참조 하세요.

특성을 매핑할 때 다음 제한 사항에 유의 하세요.

* AD FS에서 실행할 수 있는 모든 특성은 해당 특성이 동기화 된 경우에도 Azure AD에서 SAML 토큰으로 내보낼 특성으로 표시 되지 않습니다. 특성을 편집 하는 경우 **값** 드롭다운 목록에 Azure AD에서 사용할 수 있는 다양 한 특성이 표시 됩니다. 필수 특성 (예: **samAccountName**)이 Azure AD와 동기화 되도록 [Azure AD Connect 동기화 항목](../hybrid/how-to-connect-sync-whatis.md) 구성을 확인 합니다. 확장 특성을 사용 하 여 Azure AD에서 표준 사용자 스키마의 일부가 아닌 클레임을 내보낼 수 있습니다.
* 가장 일반적인 시나리오에서는 **NameID** 클레임 및 다른 일반 사용자 식별자 클레임만 앱에 필요합니다. 추가 클레임이 필요한 지 확인 하려면 AD FS에서 발급 하는 클레임을 확인 합니다.
* 일부 클레임은 Azure AD에서 보호 되므로 모든 클레임이 발급 될 수 있는 것은 아닙니다.
* 암호화 된 SAML 토큰을 사용 하는 기능은 현재 미리 보기로 제공 됩니다. [방법: 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정을](../develop/active-directory-saml-claims-customization.md)참조 하세요.

### <a name="software-as-a-service-saas-apps"></a>SaaS (Software as a service) 앱

사용자가 Salesforce, ServiceNow 또는 Workday와 같은 SaaS 앱에 로그인 하 고 AD FS와 통합 된 경우 SaaS 앱에 대해 페더레이션된 로그온을 사용 하 게 됩니다.

Azure AD에서 대부분의 SaaS 응용 프로그램을 구성할 수 있습니다. Microsoft에는  [AZURE AD 앱 갤러리](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)에서 SaaS 앱에 대 한 미리 구성 된 연결이 많기 때문에 더 쉽게 전환할 수 있습니다. SAML 2.0 응용 프로그램은 Azure AD 앱 갤러리를 통하거나 [비 갤러리 응용 프로그램](add-application-portal.md)으로 azure ad와 통합할 수 있습니다.

OAuth 2.0 또는 Openid connect Connect를 사용 하는 앱은 Azure AD와 유사 하 게 [앱 등록](../develop/quickstart-register-app.md)으로 통합할 수 있습니다. 레거시 프로토콜을 사용 하는 앱은 azure [AD 응용 프로그램 프록시](application-proxy.md) 를 사용 하 여 azure AD에 인증할 수 있습니다.

SaaS 앱을 등록 하는 데 문제가 있는 경우 [Saas 응용 프로그램 통합 지원 별칭](mailto:SaaSApplicationIntegrations@service.microsoft.com)에 문의할 수 있습니다.

### <a name="saml-signing-certificates-for-sso"></a>SSO 용 SAML 서명 인증서

서명 인증서는 SSO 배포의 중요 한 부분입니다. Azure AD는 서명 인증서를 만들어 SaaS 응용 프로그램에 대 한 SAML 기반 페더레이션된 SSO를 설정 합니다. 갤러리 또는 비 갤러리 응용 프로그램을 추가한 후에는 페더레이션된 SSO 옵션을 사용 하 여 추가 된 응용 프로그램을 구성 합니다. [Azure Active Directory에서 페더레이션된 Single Sign-On에 대 한 인증서 관리](manage-certificates-for-federated-single-sign-on.md)를 참조 하세요.

### <a name="saml-token-encryption"></a>SAML 토큰 암호화

AD FS와 Azure AD는 모두 토큰 암호화를 제공 하며, 응용 프로그램으로 이동 하는 SAML 보안 어설션을 암호화 하는 기능입니다. 어설션은 공개 키를 사용 하 여 암호화 되 고, 일치 하는 개인 키를 사용 하 여 수신 응용 프로그램에 의해 암호가 해독 됩니다. 토큰 암호화를 구성 하는 경우 x.509 인증서 파일을 업로드 하 여 공개 키를 제공 합니다.

Azure AD SAML 토큰 암호화 및 구성 방법에 대 한 자세한 내용은 [방법: AZURE AD saml 토큰 암호화 구성](howto-saml-token-encryption.md)을 참조 하세요.  

> [!NOTE]
> 토큰 암호화는 Azure AD(Azure Active Directory) 프리미엄 기능입니다. Azure AD 버전, 기능 및 가격 책정에 대한 자세한 내용은 [Azure AD 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

### <a name="apps-and-configurations-that-can-be-moved-today"></a>오늘 이동할 수 있는 앱 및 구성

현재 쉽게 이동할 수 있는 앱에는 표준 구성 요소 및 클레임 집합을 사용하는 SAML 2.0 앱이 포함됩니다. 이러한 표준 항목은 다음과 같습니다.

* 사용자 계정 이름
* 메일 주소
* 이름
* Surname
* SAML **NameID** 로 대체되는 특성(Azure AD 메일 특성, 메일 접두사, 직원 ID, 확장 특성(1-15) 또는 온-프레미스 **SamAccountName** 특성 포함). 자세한 내용은 [NameIdentifier 클레임 편집](../develop/active-directory-saml-claims-customization.md)을 참조하세요.
* 사용자 지정 클레임.

다음에는 Azure AD로 마이그레이션하기 위한 추가 구성 단계가 필요 합니다.

* AD FS의 사용자 지정 권한 부여 또는 MFA (multi-factor authentication) 규칙입니다. [AZURE AD 조건부 액세스](../conditional-access/overview.md) 기능을 사용 하 여 구성 합니다.
* 회신 URL 끝점이 여러 개인 앱 PowerShell 또는 Azure Portal 인터페이스를 사용 하 여 Azure AD에서 구성 합니다.
* SAML 버전 1.1 토큰이 필요한 WS-Federation 앱(예: SharePoint 앱). PowerShell을 사용 하 여 수동으로 구성할 수 있습니다. 갤러리에서 SharePoint 및 SAML 1.1 응용 프로그램에 대 한 사전 통합 된 일반 템플릿을 추가할 수도 있습니다. SAML 2.0 프로토콜을 지원 합니다.
* 복잡 한 클레임 발급 변환 규칙. 지원 되는 클레임 매핑에 대 한 자세한 내용은 다음을 참조 하세요.
   *  [Azure Active Directory에서 클레임 매핑](../develop/active-directory-claims-mapping.md)
   * [Azure Active Directory에서 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임을 사용자 지정](../develop/active-directory-saml-claims-customization.md)합니다.

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>현재 Azure AD에서 지원되지 않는 앱 및 구성

현재 특정 기능을 필요로 하는 앱은 마이그레이션할 수 없습니다.

#### <a name="protocol-capabilities"></a>프로토콜 기능

다음 프로토콜 기능을 필요로 하는 앱은 지금 마이그레이션할 수 없습니다.

* WS-Trust ActAs 패턴에 대 한 지원
* SAML 아티팩트 확인
* 서명 된 SAML 요청의 서명 확인
  > [!Note]
  > 서명 된 요청은 수락 되지만 서명이 확인 되지 않습니다.

  Azure AD가 응용 프로그램에서 미리 구성 된 끝점에만 토큰을 반환 하도록 지정 하면 대부분의 경우 서명 확인이 필요 하지 않을 수 있습니다.

#### <a name="claims-in-token-capabilities"></a>토큰 기능의 클레임

토큰 기능에서 다음과 같은 클레임을 요구 하는 앱은 지금 마이그레이션할 수 없습니다.

* 데이터가 Azure AD에 동기화 되지 않은 경우 Azure AD 디렉터리가 아닌 특성 저장소의 클레임입니다. 자세한 내용은 [AZURE AD 동기화 API 개요](/graph/api/resources/synchronization-overview?view=graph-rest-beta)를 참조 하세요.
* 디렉터리 다중 값 특성의 발급 예를 들어 지금은 프록시 주소에 대해 다중값 클레임을 실행할 수 없습니다.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>AD FS에서 Azure AD로 앱 설정 매핑

마이그레이션은 응용 프로그램을 온-프레미스로 구성 하는 방법을 평가한 다음 해당 구성을 Azure AD에 매핑하는 것을 요구 합니다. AD FS와 Azure AD는 비슷하게 작동하므로 신뢰, 로그온 및 로그아웃 URL 및 식별자를 구성하는 개념이 두 경우 모두에 적용됩니다. Azure AD에서 쉽게 구성할 수 있도록 응용 프로그램의 AD FS 구성 설정을 문서화 합니다.

### <a name="map-app-configuration-settings"></a>응용 프로그램 구성 설정 매핑

다음 표에서는 Azure AD Enterprise 응용 프로그램에 대 한 AD FS 신뢰 당사자 트러스트 간의 설정에 대해 가장 일반적인 몇 가지 매핑을 설명 합니다.

* AD FS-앱에 대 한 AD FS 신뢰 당사자 트러스트에서 설정을 찾습니다. 신뢰 당사자를 마우스 오른쪽 단추로 클릭 하 고 속성을 선택 합니다.
* Azure AD-설정은 각 응용 프로그램의 SSO 속성의 [Azure Portal](https://portal.azure.com/) 내에서 구성 됩니다.

| 구성 설정| AD FS| Azure AD에서을 구성 하는 방법| SAML 토큰 |
| - | - | - | - |
| **앱 로그온 URL** <p>SP (서비스 공급자)가 시작한 SAML 흐름에서 앱에 로그인 하는 데 사용할 사용자의 URL입니다.| 해당 없음| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 회신 URL** <p>IdP (id 공급자)의 관점에서 가져온 앱의 URL입니다. IdP 사용자가 IdP에 로그인 한 후 사용자 및 토큰을 여기에 보냅니다.  이를 **SAML assertion consumer 엔드포인트** 라고도 합니다.| **끝점** 탭을 선택 합니다.| SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 Destination 요소입니다. 예제 값: `https://contoso.my.salesforce.com` |
| **앱 로그아웃 URL** <p>사용자가 앱에서 로그 아웃할 때 로그 아웃 정리 요청이 전송 되는 URL입니다. IdP는 다른 모든 앱에서 사용자를 로그 아웃 하는 요청을 보냅니다.| **끝점** 탭을 선택 합니다.| SAML 기반 로그온에서 기본 SAML 구성 열기| 해당 없음 |
| **앱 식별자** <p>IdP의 관점에서 가져온 앱 식별자입니다. 로그온 URL 값은 종종 식별자에 사용되지만 항상 그렇지는 않습니다.  앱에서 "엔터티 ID"를 호출 하는 경우도 있습니다.| **식별자** 탭 선택|SAML 기반 로그온에서 기본 SAML 구성 열기| SAML 토큰의 **대상** 요소에 매핑됩니다. |
| **앱 페더레이션 메타 데이터** <p>앱의 페더레이션 메타 데이터의 위치입니다. IdP에서 엔드포인트 또는 암호화 인증서와 같은 특정 구성 설정을 자동으로 업데이트하는 데 사용합니다.| **모니터링** 탭을 선택 합니다.| 해당 없음. Azure AD는 응용 프로그램 페더레이션 메타 데이터를 직접 사용 하도록 지원 하지 않습니다. 페더레이션 메타 데이터를 수동으로 가져올 수 있습니다.| 해당 없음 |
| **사용자 id/이름 ID** <p>Azure AD 또는 AD FS의 사용자 ID를 앱에 고유하게 표시하는 데 사용되는 특성입니다.  이 특성은 일반적으로 사용자의 UPN 또는 이메일 주소입니다.| 클레임 규칙. 대부분의 경우 클레임 규칙은 **NameIdentifier** 로 끝나는 형식의 클레임을 발급 합니다.| **사용자 특성 및 클레임** 헤더에서 식별자를 찾을 수 있습니다. 기본적으로 UPN이 사용 됩니다.| SAML 토큰의 **NameID** 요소에 매핑됩니다. |
| **기타 클레임** <p>IdP에서 앱으로 일반적으로 전송 되는 다른 클레임 정보의 예로는 이름, 성, 전자 메일 주소, 그룹 멤버 자격이 있습니다.| AD FS에서는 신뢰 당사자에 대한 다른 클레임 규칙으로 찾을 수 있습니다.| **클레임 & 사용자 특성** 헤더 아래에서 식별자를 찾을 수 있습니다. **보기** 를 선택하고 다른 모든 사용자 특성을 편집합니다.| 해당 없음 |

### <a name="map-identity-provider-idp-settings"></a>IdP (지도 Id 공급자) 설정

응용 프로그램이 SSO에 대 한 Azure AD와 AD FS를 가리키도록 구성 합니다. 여기서는 SAML 프로토콜을 사용 하는 SaaS 앱에 집중 하 고 있습니다. 그러나이 개념은 사용자 지정 lob (기간 업무) 앱으로도 확장 됩니다.

> [!NOTE]
> Azure AD의 구성 값은 Azure 테 넌 트 ID가 {테 넌 트 id}를 바꾸고 응용 프로그램 ID가 {응용 프로그램 id}를 대체 하는 패턴을 따릅니다. 이 정보는 **Azure Active Directory > 속성** 의 [Azure Portal](https://portal.azure.com/) 에서 찾을 수 있습니다.

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
| **IdP Sign-on URL** <p>앱의 관점에서 IdP의 로그인 URL입니다 (사용자가 로그인을 위해 리디렉션 됨).| AD FS sign-on URL은 AD FS 페더레이션 서비스 이름 뒤에 "/adfs/ls/."가 옵니다. <p>`https://fs.contoso.com/adfs/ls/`| {Tenant-id}를 테 넌 트 ID로 바꿉니다. <p> SAML-P 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>WS-Federation 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **IdP 로그 아웃 URL**<p>앱의 관점에서 IdP의 로그 아웃 URL (사용자가 앱에서 로그 아웃 하도록 선택할 때 리디렉션되는 위치)입니다.| 로그 아웃 URL은 로그온 URL과 동일 하거나 "wa = wsignout1.0 1.0"이 추가 된 URL과 동일 합니다. `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| {Tenant-id}를 테 넌 트 ID로 바꿉니다.<p>SAML-P 프로토콜을 사용 하는 앱의 경우:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> WS-Federation 프로토콜을 사용 하는 앱의 경우: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **토큰 서명 인증서**<p>IdP는 인증서의 개인 키를 사용 하 여 발급 된 토큰에 서명 합니다. 앱이 신뢰하도록 구성된 것과 동일한 IdP에서 토큰이 제공되었는지 확인합니다.| AD FS 토큰 서명 인증서는 AD FS 관리의 **인증서** 아래에 있습니다.| **SAML 서명 인증서** 헤더의 응용 프로그램 **Single sign-on 속성** 에 있는 Azure Portal에서 찾습니다. 여기서는 앱에 업로드할 인증서를 다운로드할 수 있습니다.  <p>응용 프로그램에 둘 이상의 인증서가 있는 경우 페더레이션 메타 데이터 XML 파일에서 모든 인증서를 찾을 수 있습니다. |
| **식별자/"issuer"**<p>앱의 관점에서 IdP의 식별자입니다 ("발급자 ID" 라고도 함).<p>SAML 토큰에서 값은 Issuer 요소로 표시 됩니다.| AD FS에 대 한 식별자는 일반적으로 AD FS 관리에서 **서비스 > 편집 페더레이션 서비스 속성** 에 있는 페더레이션 서비스 식별자입니다. `http://fs.contoso.com/adfs/services/trust`| {Tenant-id}를 테 넌 트 ID로 바꿉니다.<p>https: \/ /sts.windows.net/{tenant-id}/ |
| **IdP 페더레이션 메타 데이터**<p>IdP의 공개적으로 사용할 수 있는 페더레이션 메타 데이터의 위치입니다. (일부 앱은 URL, 식별자 및 토큰 서명 인증서를 개별적으로 구성하는 관리자 대신 연합 메타데이터를 사용합니다.)| **서비스 > > > 끝점** 아래의 AD FS 관리에서 페더레이션 메타 데이터 URL AD FS 찾습니다. `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Azure AD에 대 한 해당 값은 패턴을 따릅니다 [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . {TenantDomainName}을 "contoso.onmicrosoft.com" 형식의 테 넌 트 이름으로 바꿉니다.   <p>자세한 내용은 [페더레이션 메타데이터](../azuread-dev/azure-ad-federation-metadata.md)를 참조하세요. |

## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Azure AD에서 AD FS 보안 정책을 나타냅니다.

앱 인증을 Azure AD로 이동 하는 경우 기존 보안 정책에서 Azure AD에서 사용할 수 있는 동등한 또는 대체 변형으로 매핑을 만듭니다. 앱 소유자에 게 필요한 보안 표준을 충족 하는 동시에 이러한 매핑을 수행할 수 있도록 하면 나머지 앱 마이그레이션을 훨씬 쉽게 수행할 수 있습니다.

각 규칙 예제에는 AD FS, AD FS 규칙 언어와 동일한 코드 및이가 Azure AD에 매핑되는 방법에 대 한 규칙이 표시 됩니다.

### <a name="map-authorization-rules"></a>매핑 권한 부여 규칙

다음은 AD FS의 다양 한 유형의 권한 부여 규칙과 Azure AD에 매핑하는 방법의 예입니다.

#### <a name="example-1-permit-access-to-all-users"></a>예제 1: 모든 사용자에 대 한 액세스 허용

AD FS의 모든 사용자에 대 한 액세스 허용:

  ![SAML 대화 상자를 사용 하 여 단일 Sign-On 설정을 보여 주는 스크린샷](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-1.png)

이는 다음 방법 중 하나로 Azure AD에 매핑됩니다.

1. **사용자 할당 필요** 를 **아니요** 로 설정 합니다.

    ![SaaS 앱에 대 한 액세스 제어 정책 편집 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    > [!Note]
    > **사용자 할당** 을 **예** 에 설정 하려면 응용 프로그램에 사용자를 할당 하 여 액세스 권한을 얻어야 합니다. **아니요** 로 설정 하면 모든 사용자가 액세스할 수 있습니다. 이 스위치는 **내 앱** 환경에서 사용자에 게 표시 되는 항목을 제어 하지 않습니다.

1. **사용자 및 그룹 탭** 에서 **모든 사용자** 자동 그룹에 응용 프로그램을 할당 합니다. 기본 **모든 사용자** 그룹을 사용할 수 있도록 하려면 Azure AD 테 넌 트에서 [동적 그룹을 사용 하도록 설정](../enterprise-users/groups-create-rule.md) 해야 합니다.

    ![Azure AD의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)

#### <a name="example-2-allow-a-group-explicitly"></a>예제 2: 그룹을 명시적으로 허용

AD FS에서 명시적 그룹 권한 부여:

  ![스크린샷 도메인 관리자 클레임 허용 규칙에 대 한 규칙 편집 대화 상자를 표시 합니다.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)

이 규칙을 Azure AD에 매핑하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 AD FS 사용자 그룹에 해당 하는 [사용자 그룹을 만듭니다](../fundamentals/active-directory-groups-create-azure-portal.md) .
1. 그룹에 앱 사용 권한 할당:

    ![할당 추가 ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)

#### <a name="example-3-authorize-a-specific-user"></a>예 3: 특정 사용자 권한 부여

AD FS에서 명시적 사용자 권한 부여:

  ![스크린샷 들어오는 클레임 유형이 주 S D 인 특정 사용자 클레임 규칙 허용 규칙에 대 한 규칙 편집 대화 상자를 표시 합니다.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

이 규칙을 Azure AD에 매핑하려면 다음을 수행 합니다.

* [Azure Portal](https://portal.azure.com/)에서 아래와 같이 앱의 할당 추가 탭을 통해 사용자를 앱에 추가 합니다.

    ![Azure의 내 SaaS 앱 ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

### <a name="map-multi-factor-authentication-rules"></a>Multi-factor authentication 규칙 매핑

온-프레미스 [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) 배포 및 AD FS는 AD FS에 페더레이션 되기 때문에 마이그레이션 후에도 계속 작동 합니다. 그러나 Azure AD의 조건부 액세스 워크플로에 연결 된 Azure의 기본 제공 MFA 기능으로 마이그레이션하는 것이 좋습니다.

다음은 AD FS의 MFA 규칙 유형 및 다양 한 조건에 따라 Azure AD에 매핑할 수 있는 방법의 예입니다.

AD FS의 MFA 규칙 설정:

  ![스크린샷 Azure Portal의 Azure A D에 대 한 조건을 보여 줍니다.](media/migrate-adfs-apps-to-azure/mfa-settings-common-for-all-examples.png)

#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>예제 1: 사용자/그룹에 따라 MFA 적용

사용자/그룹 선택기는 그룹 단위 (그룹 SID) 또는 사용자별 (주 SID)를 기준으로 MFA를 적용할 수 있는 규칙입니다. 사용자/그룹 할당 외에도 AD FS MFA 구성 UI의 모든 추가 확인란은 사용자/그룹 규칙이 적용 된 후 평가 되는 추가 규칙으로 작동 합니다.

Azure AD에서 사용자 또는 그룹에 대 한 MFA 규칙 지정:

1. [새 조건부 액세스 정책을](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)만듭니다.
1. **할당** 을 선택합니다. MFA를 적용 하려는 사용자 또는 그룹을 추가 합니다.
1. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.

    ‎![액세스 권한을 부여할 수 있는 권한 부여 창이 스크린샷에 표시 됩니다.](media/migrate-adfs-apps-to-azure/mfa-users-groups.png)

 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>예제 2: 등록 되지 않은 장치에 대 한 MFA 적용

Azure AD에서 등록 되지 않은 장치에 대 한 MFA 규칙 지정:

1. [새 조건부 액세스 정책을](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)만듭니다.
1. **모든 사용자** 에 게 **할당** 을 설정 합니다.
1. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.

    ![액세스 권한을 부여 하 고 기타 제한을 지정할 수 있는 권한 부여 창을 보여 주는 스크린샷](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

**선택 된 컨트롤 중 하나를 요구** 하도록 **여러 컨트롤에 대해** 옵션을 설정 하면 사용자가이 확인란에 지정 된 조건 중 하나가 충족 되는 경우 사용자에 게 앱에 대 한 액세스 권한이 부여 됩니다.

#### <a name="example-3-enforce-mfa-based-on-location"></a>예제 3: 위치에 따라 MFA 적용

Azure AD에서 사용자의 위치에 따라 MFA 규칙을 지정 합니다.

1. [새 조건부 액세스 정책을](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)만듭니다.
1. **모든 사용자** 에 게 **할당** 을 설정 합니다.
1. [AZURE AD에서 명명 된 위치를 구성](../reports-monitoring/quickstart-configure-named-locations.md)합니다. 그렇지 않으면 회사 네트워크 내부의 페더레이션을 신뢰할 수 있습니다.
1. **조건 규칙** 을 구성 하 여 MFA를 적용할 위치를 지정 합니다.

    ![스크린샷 조건 규칙의 위치 창을 보여 줍니다.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

1. 아래와 같이 **액세스 제어** 옵션을 구성 합니다.

    ![액세스 제어 정책 매핑](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

### <a name="map-emit-attributes-as-claims-rule"></a>지도 내보내기 특성을 클레임 규칙으로

AD FS에서 클레임 규칙으로 특성을 내보냅니다.

  ![스크린샷 클레임으로 특성 내보내기에 대 한 규칙 편집 대화 상자를 표시 합니다.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-1.png)

규칙을 Azure AD에 매핑하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 **엔터프라이즈 응용 프로그램** 을 선택 하 고 **Single SIGN-ON** 을 선택 하 여 SAML 기반 로그온 구성을 확인 합니다.

    ![스크린샷 엔터프라이즈 응용 프로그램에 대 한 Single sign-on 페이지를 표시 합니다.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-2.png)

1. **편집** (강조 표시 됨)을 선택 하 여 특성을 수정 합니다.

    ![사용자 특성 및 클레임을 편집 하는 페이지입니다.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claims-rule-3.png)

### <a name="map-built-in-access-control-policies"></a>기본 제공 액세스 제어 정책 매핑

AD FS 2016의 기본 제공 액세스 제어 정책:

  ![Azure AD 기본 제공 access control](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-1.png)

Azure AD에서 기본 제공 정책을 구현 하려면 [새 조건부 액세스 정책을](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) 사용 하 고 액세스 제어를 구성 하거나 AD FS 2016의 사용자 지정 정책 디자이너를 사용 하 여 액세스 제어 정책을 구성 합니다. 규칙 편집기에는 모든 종류의 순열을 만드는 데 도움이 되는 모든 허용 및 제외 옵션 목록이 있습니다.

  ![Azure AD 액세스 제어 정책](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-2.png)

이 테이블에는 몇 가지 유용한 허용 및 제외 옵션과 Azure AD에 매핑되는 방법이 나와 있습니다.

| 옵션 | Azure AD에서 허용 옵션을 구성 하는 방법| Azure AD에서 Except 옵션을 구성 하는 방법 |
| - | - | - |
| 특정 네트워크| Azure AD의 [명명 된 위치](../reports-monitoring/quickstart-configure-named-locations.md) 에 매핑됩니다.| [신뢰할 수 있는 위치](../conditional-access/location-condition.md) 에 **제외** 옵션 사용 |
| 특정 그룹| [사용자/그룹 할당 설정](assign-user-or-group-access-portal.md)| 사용자 및 그룹에서 **제외** 옵션 사용 |
| 특정 신뢰 수준이 있는 장치에서| 할당-> 조건 아래의 **장치 상태** 컨트롤에서이를 설정 합니다.| 장치 상태 조건에서 **제외** 옵션을 사용 하 고 **모든 장치** 포함 |
| 요청에서 특정 클레임 사용| 이 설정은 마이그레이션할 수 없습니다.| 이 설정은 마이그레이션할 수 없습니다. |

Azure Portal에서 신뢰할 수 있는 위치에 대 한 제외 옵션을 구성 하는 방법의 예는 다음과 같습니다.

  ![액세스 제어 정책 매핑 스크린샷](media/migrate-adfs-apps-to-azure/map-built-in-access-control-policies-3.png)

## <a name="transition-users-from-ad-fs-to-azure-ad"></a>사용자를 AD FS에서 Azure AD로 전환

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Azure AD에서 AD FS 그룹 동기화

권한 부여 규칙을 매핑할 때 AD FS를 사용 하 여 인증 하는 앱은 사용 권한에 대해 Active Directory 그룹을 사용할 수 있습니다. 이러한 경우 응용 프로그램을 마이그레이션하기 전에 [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) 를 사용 하 여 이러한 그룹을 Azure AD와 동기화 합니다. 응용 프로그램을 마이그레이션할 때 동일한 사용자에 게 액세스 권한을 부여할 수 있도록 마이그레이션하기 전에 해당 그룹 및 멤버 자격을 확인 해야 합니다.

자세한 내용은 [Active Directory에서 동기화 된 그룹 특성을 사용 하기 위한 필수 조건](../hybrid/how-to-connect-fed-group-claims.md)을 참조 하세요.

### <a name="set-up-user-self-provisioning"></a>사용자 자동 프로 비전 설정

일부 SaaS 응용 프로그램은 사용자가 응용 프로그램에 처음 로그인 할 때 사용자를 자동으로 프로 비전 하는 기능을 지원 합니다. Azure AD에서 앱 프로 비전은 사용자가 액세스 해야 하는 클라우드 ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) 응용 프로그램에서 사용자 id 및 역할을 자동으로 만드는 것을 의미 합니다. 마이그레이션된 사용자는 이미 SaaS 응용 프로그램에 계정이 있습니다. 마이그레이션 후에 추가 된 모든 새 사용자를 프로 비전 해야 합니다. 응용 프로그램이 마이그레이션되면 [SaaS 앱 프로 비전](../app-provisioning/user-provisioning.md) 을 테스트 합니다.

### <a name="sync-external-users-in-azure-ad"></a>Azure AD에서 외부 사용자 동기화

기존 외부 사용자는 AD FS에서 다음 두 가지 방법으로 설정할 수 있습니다.

* **조직 내 로컬 계정을 사용 하는 외부 사용자**-내부 사용자 계정이 작동 하는 것과 동일한 방식으로 이러한 계정을 계속 사용할 수 있습니다. 이러한 외부 사용자 계정에는 조직 내에서의 계정 이름이 있습니다. 단, 계정 메일은 외부에서 가리킬 수 있습니다. 마이그레이션을 진행 하면서 이러한 id를 사용할 수 있는 경우 해당 사용자를 마이그레이션하여 자신의 회사 id를 사용 하도록 하 여 [AZURE AD B2B](../external-identities/what-is-b2b.md) 에서 제공 하는 이점을 활용할 수 있습니다. 이렇게 하면 해당 사용자에 대 한 로그인 프로세스가 간소화 됩니다. 이러한 사용자는 회사 로그온을 사용 하 여 로그인 하는 경우가 많습니다. 외부 사용자에 대 한 계정을 관리 하지 않고도 조직의 관리를 더 쉽게 수행할 수 있습니다.
* **페더레이션된 외부 id**-현재 외부 조직과 페더레이션 하는 경우 몇 가지 방법을 사용할 수 있습니다.
  * [Azure Portal에 AZURE ACTIVE DIRECTORY B2B 공동 작업 사용자를 추가](../external-identities/add-users-administrator.md)합니다. Azure AD 관리 포털의 B2B 공동 작업 초대를 Azure AD 관리 포털에서 개별 구성원이 사용 되는 앱 및 자산을 계속 사용할 수 있도록 파트너 조직에 사전에 보낼 수 있습니다.
  * B2B 초대 API를 사용 하 여 파트너 조직에서 개별 사용자에 대 한 요청을 생성 하는 [셀프 서비스 b2b 등록 워크플로를 만듭니다](../external-identities/self-service-portal.md) .

기존 외부 사용자를 구성 하는 방법에 관계 없이 그룹 멤버 자격 또는 특정 권한에서 해당 계정과 연결 된 사용 권한이 있을 수 있습니다. 이러한 사용 권한을 마이그레이션하거나 정리 해야 하는지 여부를 평가 합니다. 외부 사용자를 나타내는 조직 내의 계정은 사용자가 외부 id로 마이그레이션된 후에 사용 하지 않도록 설정 해야 합니다. 리소스에 연결 하는 기능이 중단 될 수 있으므로 마이그레이션 프로세스는 비즈니스 파트너와 논의 해야 합니다.

## <a name="migrate-and-test-your-apps"></a>앱 마이그레이션 및 테스트

이 문서에서 자세히 설명 하는 마이그레이션 프로세스를 따르세요. 그런 다음 [Azure Portal](https://aad.portal.azure.com/) 로 이동 하 여 마이그레이션이 성공 했는지 테스트 합니다.

다음 지침을 따릅니다.

1. **엔터프라이즈 응용 프로그램**  >  **모든 응용** 프로그램을 선택 하 고 목록에서 앱을 찾습니다.
1.   >  **사용자 및** 그룹 관리를 선택 하 여 하나 이상의 사용자 또는 그룹을 앱에 할당 합니다.
1.   >  **조건부 액세스** 관리를 선택 합니다. 정책 목록을 검토 하 고 [조건부 액세스 정책을](../conditional-access/overview.md)사용 하 여 응용 프로그램에 대 한 액세스를 차단 하지 않는지 확인 합니다.

앱을 구성 하는 방법에 따라 SSO가 제대로 작동 하는지 확인 합니다.

| 인증 유형| 테스트 |
| :- | :- |
| OAuth/Openid connect Connect| **엔터프라이즈 응용 프로그램 > 권한** 을 선택 하 고 앱에 대 한 사용자 설정에서 응용 프로그램에 동의한 확인 합니다.|
| SAML 기반 SSO | **Single sign-on** 아래에 있는 [SAML 설정 테스트](debug-saml-sso-issues.md) 단추를 사용 합니다. |
| Password-Based SSO |  [Myapps 보안 로그인](../user-help/my-apps-portal-end-user-access.md)확장을 다운로드 하 여 설치 합니다 [-](../user-help/my-apps-portal-end-user-access.md) [](../user-help/my-apps-portal-end-user-access.md). 이 확장은 SSO 프로세스를 사용 해야 하는 조직의 클라우드 앱을 시작 하는 데 도움이 됩니다. |
| 애플리케이션 프록시 | 커넥터가 실행 중이 고 응용 프로그램에 할당 되었는지 확인 합니다. 자세한 내용은 [응용 프로그램 프록시 문제 해결 가이드](application-proxy-troubleshoot.md) 를 참조 하세요. |

> [!NOTE]
> 이전 AD FS 환경의 쿠키는 사용자 컴퓨터에 유지 됩니다. 사용자가 이전 AD FS 로그인 환경 및 새 Azure AD 로그인으로 이동할 수 있으므로 이러한 쿠키는 마이그레이션 문제를 일으킬 수 있습니다. 사용자 브라우저 쿠키를 수동으로 지우거 나 스크립트를 사용 하 여 지워야 할 수 있습니다. System Center Configuration Manager 또는 유사한 플랫폼을 사용할 수도 있습니다.

### <a name="troubleshoot"></a>문제 해결

마이그레이션된 응용 프로그램의 테스트에서 오류가 발생 하는 경우 기존 AD FS 신뢰 당사자에 게 다시 대체 하기 전에 문제 해결이 첫 번째 단계 일 수 있습니다. [Azure Active Directory의 응용 프로그램에 대 한 SAML 기반 Single Sign-On 디버그 하는 방법을](debug-saml-sso-issues.md)참조 하세요.

### <a name="rollback-migration"></a>마이그레이션 롤백

마이그레이션이 실패 하면 AD FS 서버에 기존 신뢰 당사자를 그대로 두고 신뢰 당사자에 대 한 액세스 권한을 제거 하는 것이 좋습니다. 이렇게 하면 배포 중에 필요한 경우 빠른 대체를 수행할 수 있습니다.

### <a name="employee-communication"></a>직원 통신

계획 된 가동 중단 창 자체는 최소화할 수 있지만, AD FS에서 Azure AD로 전환 하는 동안 이러한 기간을 직원 들에 게 사전에 전달 하는 계획을 세워야 합니다. 앱 환경에 피드백 단추가 있는지 확인 하거나 기술 지원팀에 문의 하 여 문제를 해결 합니다.

배포가 완료 되 면 성공적인 배포를 사용자에 게 알리고 수행 해야 하는 모든 단계를 알릴 수 있습니다.

* 사용자가 [내 앱](https://myapps.microsoft.com) 을 사용 하 여 마이그레이션된 모든 응용 프로그램에 액세스 하도록 지시 합니다.
* 사용자에 게 MFA 설정을 업데이트 해야 할 수 있는 사용자를 알려 줍니다.
* Self-Service 암호 재설정을 배포 하는 경우 사용자는 인증 방법을 업데이트 하거나 확인 해야 할 수 있습니다. [MFA](https://aka.ms/mfatemplates) 및 [SSPR](https://aka.ms/ssprtemplates) 최종 사용자 통신 템플릿을 참조 하세요.

### <a name="external-user-communication"></a>외부 사용자 통신

일반적으로이 사용자 그룹은 문제의 경우 가장 심각한 영향을 받습니다. 보안 상태에서 외부 파트너에 대 한 조건부 액세스 규칙이 나 위험 프로필의 다른 집합을 지정 하는 경우 특히 그렇습니다. 외부 파트너는 클라우드 마이그레이션 일정을 인식 하 고 외부 공동 작업에 고유한 모든 흐름을 테스트 하는 파일럿 배포에 참여 하는 것이 좋습니다. 마지막으로 문제가 있는 경우 기술 지원팀에 액세스할 수 있는 방법이 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

* [AZURE AD로 응용 프로그램 인증 마이그레이션을](https://aka.ms/migrateapps/whitepaper)참조 하세요.
* [조건부 액세스](../conditional-access/overview.md) 및 [MFA](../authentication/concept-mfa-howitworks.md)를 설정 합니다.
* 단계별 코드 샘플:[개발자를 위한 AZURE AD application migration 플레이 북에 AD FS](https://aka.ms/adfsplaybook)합니다.
