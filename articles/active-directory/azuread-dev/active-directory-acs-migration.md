---
title: Azure Access Control Service에서 마이그레이션 | Microsoft Docs
description: Azure ACS(Access Control Service)에서 앱 및 서비스를 이동하기 위한 옵션에 대해 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ms.openlocfilehash: 7b009a6e2f540dc076340a6803679a541e60adc7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77165346"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>방법: Azure Access Control Service에서 마이그레이션

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure AD(Azure Active Directory) 서비스의 하나인 Azure ACS(Access Control)는 2018년 11월 7일부터 사용 중지됩니다. 현재 Access Control을 사용하는 애플리케이션 및 서비스는 그때까지 다른 인증 메커니즘으로 완전히 마이그레이션되어야 합니다. 이 문서에서는 Access Control의 사용 중지를 계획 중인 기존 고객에게 몇 가지 권장 사항을 안내합니다. Access Control을 사용하지 않는 경우 어떤 조치도 취할 필요가 없습니다.

## <a name="overview"></a>개요

Access Control은 사용자가 웹 애플리케이션 및 서비스에 액세스할 수 있도록 인증하고 권한을 부여하는 간편한 방법을 제공하는 클라우드 인증 서비스입니다. 많은 인증 및 권한 부여 기능을 코드에서 제외할 수 있습니다. Access Control은 Microsoft .NET 클라이언트, ASP.NET 웹 애플리케이션 및 Windows Communication Foundation(WCF) 웹 서비스 개발자 및 설계자가 주로 사용합니다.

Access Control의 용도는 다음과 같은 세 가지 범주로 나누어집니다.

- Azure Service Bus 및 Dynamics CRM을 비롯한 특정 Microsoft 클라우드 서비스를 인증합니다. 클라이언트 애플리케이션은 Access Control로부터 획득한 토큰으로 이러한 서비스를 인증하고 다양한 작업을 수행합니다.
- 사용자 지정 및 미리 패키지된 웹 애플리케이션(예: SharePoint)에 인증을 추가합니다. Access Control "수동" 인증을 사용하면 Microsoft 계정(이전의 Live ID)은 물론, Google, Facebook, Yahoo, Azure AD 및 AD FS(Active Directory Federation Services) 계정으로도 웹 애플리케이션에 로그인할 수 있습니다.
- Access Control에서 발행하는 토큰으로 사용자 지정 웹 서비스를 보호합니다. 웹 서비스는 “능동” 인증을 사용하여 Access Control에서 인증한 클라이언트의 액세스만 허용할 수 있습니다.

이어지는 섹션에서는 이러한 용도와 각각의 권장 마이그레이션 전략을 설명합니다.

> [!WARNING]
> 대부분의 경우 기존 앱과 서비스를 신기술로 마이그레이션할 때는 코드를 대폭 수정해야 합니다. 시스템 중단이나 가동 중지 시간을 방지하기 위해서는 가능한 한 빨리 마이그레이션을 계획하고 이행하는 것이 좋습니다.

Access Control의 구성 요소는 다음과 같습니다.

- 보안 토큰 서비스(STS): 인증 요청을 받고 요청에 대한 응답으로서 보안 토큰을 발행합니다.
- 클래식 Azure Portal: Access Control 네임스페이스를 생성, 삭제, 활성화 및 비활성화합니다.
- 별도의 Access Control 관리 포털: Access Control 네임스페이스를 사용자 지정하고 구성합니다.
- 관리 서비스: 포털의 기능을 자동화하는 데 사용할 수 있습니다.
- 토큰 변환 규칙 엔진: Access Control에서 발행하는 토큰의 출력 형식을 조작하는 복잡한 로직을 정의하는 데 사용할 수 있습니다.

이러한 구성 요소를 사용하려면 하나 이상의 Access Control 네임스페이스를 만들어야 합니다. *네임스페이스*는 애플리케이션 및 서비스가 통신하는 Access Control의 전용 인스턴스입니다. 네임스페이스는 다른 모든 Access Control 고객으로부터 격리됩니다. 다른 Access Control 고객은 자신의 네임스페이스를 사용합니다. Access Control의 네임스페이스에는 다음과 같은 전용 URL이 있습니다.

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

이 URL에서 STS 및 관리 작업과의 모든 통신이 이루어집니다. 용도별로 서로 다른 경로를 갖습니다. 애플리케이션 또는 서비스에서 Access Control을 사용하는지 확인하려면 https://&lt;namespace&gt;.accesscontrol.windows.net으로의 트래픽을 모니터링합니다. 이 URL로 전달되는 모든 트래픽은 Access Control에서 처리하는 것으로, 사용 중지 계획을 세워야 합니다. 

이에 대한 예외는 모든 `https://accounts.accesscontrol.windows.net` 트래픽입니다. 이 URL로 전달되는 트래픽은 다른 서비스에 의해 처리되기 때문에 Access Control 사용 중지의 영향을 받지 **않습니다**. 

Access Control에 대한 자세한 내용은 [Access Control Service 2.0(보관)](https://msdn.microsoft.com/library/hh147631.aspx)을 참조하세요.

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>어떤 앱이 영향을 받을지 확인합니다.

이 섹션의 단계에 따라 ACS 사용 중지에 의해 어떤 앱이 영향을 받을지 확인십시오.

### <a name="download-and-install-acs-powershell"></a>ACS PowerShell 다운로드 및 설치

1. PowerShell 갤러리로 이동하여 [Acs.Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)를 다운로드합니다.
1. 실행하여 모듈 설치

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

1. 실행하여 가능한 모든 명령 목록 가져오기

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    특정 명령에 대한 도움말을 보려면 다음을 실행합니다.

    ```
     Get-Help [Command-Name] -Full
    ```
    
    여기서 `[Command-Name]`은 ACS 명령의 이름입니다.

### <a name="list-your-acs-namespaces"></a>ACS 네임스페이스 나열

1. **Connect-AcsAccount** cmdlet을 사용하여 ACS에 연결합니다.
  
    명령을 실행하기 전에 `Set-ExecutionPolicy -ExecutionPolicy Bypass`를 실행해야 하며 해당 명령을 실행하려면 해당 구독의 관리자여야 합니다.

1. **Get-AcsSubscription** cmdlet을 사용하여 사용 가능한 Azure 구독을 나열합니다.
1. **Get-AcsNamespace** cmdlet을 사용하여 ACS 네임스페이스를 나열합니다.

### <a name="check-which-applications-will-be-impacted"></a>어떤 애플리케이션이 영향을 받는지 확인합니다.

1. 이전 단계에서 해당 네임스페이스를 사용하고 `https://<namespace>.accesscontrol.windows.net`으로 이동합니다.

    예를 들어, 네임스페이스 중 하나가 contoso-test이면 `https://contoso-test.accesscontrol.windows.net`으로 이동합니다.

1. **신뢰 관계**에서 **신뢰 당사자 애플리케이션**을 선택하여 ACS 사용 중지에 의해 영향을 받는 애플리케이션 목록을 확인합니다.
1. 가지고 있는 다른 모든 ACS 네임스페이스에 대해 1-2 단계를 반복합니다.

## <a name="retirement-schedule"></a>사용 중지 일정

2017년 11월 현재, 모든 Access Control 구성 요소가 지원되고 정상 작동됩니다. 유일한 제한 사항은 [클래식 Azure Portal을 통해 새 Access Control 네임스페이스를 만들 수 없다](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)는 것입니다.

Access Control 구성 요소의 사용 중지 일정은 다음과 같습니다.

- **2017년 11월**: 클래식 Azure Portal의 Azure AD 관리자 환경이 [사용 중지됩니다](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). 현재 새로운 전용 URL `https://manage.windowsazure.com?restoreClassic=true`에서 Access Control에 대한 네임스페이스를 관리할 수 있습니다. 필요한 경우 이 URl를 사용하여 기존 네임스페이스를 보고, 네임스페이스를 사용하거나 사용하지 않도록 설정하고, 네임스페이스를 삭제합니다.
- **2018년 4월 2일**: Azure 클래식 포털이 완전히 사용 중지됩니다. 즉, 더 이상 URL을 통해 Access Control 네임스페이스 관리를 사용할 수 없습니다. 이 시점에서는 Access Control을 사용 또는 사용하지 않도록 설정 하거나, 삭제하거나, 열거할 수 없습니다. 하지만 `https://\<namespace\>.accesscontrol.windows.net`에서는 Access Control 관리 포털이 완벽하게 작동됩니다. Access Control의 다른 모든 구성 요소는 계속해서 정상적으로 작동합니다.
- **2018년 11월 7일**: 모든 Access Control 구성 요소가 영구적으로 종료됩니다. 즉, Access Control 관리 포털, 관리 서비스, STS, 토큰 변환 규칙 엔진이 종료됩니다. 이 시점에서 Access Control(\<namespace\>.accesscontrol.windows.net에 위치)로 전송된 모든 요청이 실패합니다. 이 시점 전까지 기존 앱과 서비스를 다른 기술로 모두 마이그레이션 완료해야 합니다.

> [!NOTE]
> 정책은 일정 기간 동안 토큰을 요청하지 않은 네임스페이스를 사용하지 않도록 설정합니다. 2018년 9월초 현재 해당 기간은 비활성 기간 14일이지만 몇 주 후부터는 비활성 기간 7일로 단축될 예정입니다. 현재 사용하지 않도록 설정된 액세스 제어 네임스페이스가 있다면 [ACS PowerShell을 다운로드 및 설치](#download-and-install-acs-powershell)하여 해당 네임스페이스를 다시 사용하도록 설정할 수 있습니다.

## <a name="migration-strategies"></a>마이그레이션 전략

이어지는 섹션에서는 Access Control에서 다른 Microsoft 기술로 마이그레이션하기 위한 대략적인 권장 사항을 안내합니다.

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 클라우드 서비스의 클라이언트

Access Control에서 발행하는 토큰을 이용하는 각 Microsoft 클라우드 서비스는 현재 하나 이상의 대체 인증 수단을 지원합니다. 각 서비스마다 올바른 인증 메커니즘이 달라집니다. 공식적인 지침은 각 서비스에 대한 특정 설명서를 참조하는 것이 좋습니다. 참고를 위해 아래에서 각 서비스의 설명서를 안내하고 있습니다.

| 서비스 | 지침 |
| ------- | -------- |
| Azure Service Bus | [공유 액세스 서명으로 마이그레이션](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Service Bus Relay | [공유 액세스 서명으로 마이그레이션](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Managed Cache | [Azure Cache for Redis로 마이그레이션](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Cognitive Services API로 마이그레이션](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Azure App Service의 Logic Apps 기능으로 마이그레이션](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Azure AD 인증으로 마이그레이션](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Azure Backup 에이전트 업그레이드](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint 고객

SharePoint 2013, 2016 및 SharePoint Online 고객은 클라우드, 온-프레미스 및 하이브리드 시나리오에서 인증 목적으로 사용 되는 ACS가 오래 되었습니다. 일부 SharePoint 기능 및 사용 사례는 ACS 사용 중지의 영향을 받을 것이고, 일부는 그렇지 않습니다. 아래 표에는 ACS를 활용하는 가장 인기 있는 SharePoint의 일부 기능에 대한 마이그레이션 지침이 요약되어 있습니다.

| 기능 | 지침 |
| ------- | -------- |
| Azure AD에서 사용자 인증 | 이전에는 Azure AD가 인증을 위해 SharePoint에서 요구하는 SAML 1.1 토큰을 지원하지 않았으며, SharePoint를 Azure AD 토큰 형식과 호환되도록 만들기 위해 ACS 토큰을 중간자로 사용했습니다. 이제 [온-프레미스 앱의 Azure AD 앱 갤러리 sharepoint를 사용 하 여 sharepoint를 AZURE AD에 직접 연결할](https://docs.microsoft.com/azure/active-directory/saas-apps/sharepoint-on-premises-tutorial)수 있습니다. |
| [SharePoint 온-프레미스에서 서버 간 인증 & 앱 인증](https://technet.microsoft.com/library/jj219571(v=office.16).aspx) | ACS 사용 중지의 영향을 받지 않으므로 변경할 필요가 없습니다. | 
| [SharePoint 추가 기능에 대한 낮은 신뢰 권한 부여(호스팅된 공급자 및 호스팅된 SharePoint)](https://docs.microsoft.com/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | ACS 사용 중지의 영향을 받지 않으므로 변경할 필요가 없습니다. |
| [SharePoint 클라우드 하이브리드 검색](https://blogs.msdn.microsoft.com/spses/2015/09/15/cloud-hybrid-search-service-application/) | ACS 사용 중지의 영향을 받지 않으므로 변경할 필요가 없습니다. |

### <a name="web-applications-that-use-passive-authentication"></a>수동 인증을 사용하는 웹 애플리케이션

사용자 인증에 Access Control을 사용하는 웹 애플리케이션의 경우 Access Control은 웹 애플리케이션 개발자 및 설계자에게 다음과 같은 기능을 제공합니다.

- WIF(Windows Identity Foundation)와의 심층적인 통합.
- Google, Facebook, Yahoo, Azure Active Directory 및 AD FS 계정, Microsoft 계정과의 페더레이션.
- OAuth 2.0 초안 13, Ws-Trust, WS-Federation(Web Services Federation) 인증 프로토콜 지원.
- JWT(JSON 웹 토큰), SAML 1.1, SAML 2.0, SWT(단순 웹 토큰) 토큰 형식 지원.
- 사용자가 로그인할 계정 유형을 직접 선택할 수 있는, WIF에 통합된 홈 영역 검색 환경. 이 환경은 웹 애플리케이션에 의해 호스팅되며 원하는 대로 사용자 지정이 가능합니다.
- 웹 애플리케이션이 Access Control로부터 수신한 클레임을 다음과 같이 사용자 지정할 수 있는 토큰 변환:
    - ID 공급자의 클레임 전달.
    - 사용자 지정 클레임 추가.
    - 특정 조건이 충족되면 클레임을 발행하는 단순한 if-then 로직.

안타깝지만 위의 모든 기능을 제공하는 단일한 서비스는 없습니다. Access Control의 기능 중 어느 기능이 필요한지 검토한 다음 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)(Azure AD B2C) 또는 그 밖의 클라우드 인증 서비스 중 어느 것을 사용할지 선택해야 합니다.

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory로 마이그레이션

앱과 서비스를 Azure AD에 직접 통합하는 방법도 있습니다. Azure AD는 Microsoft 회사 또는 학교 계정을 위한 클라우드 기반 ID 공급자입니다. Azure AD는 Office 365, Azure 등을 위한 ID 공급자입니다. Azure AD는 Access Control과 비슷한 페더레이션 인증 기능을 제공하긴 하나, Access Control의 모든 기능을 지원하지는 않습니다. 

페더레이션은 주로 Facebook, Google, Yahoo와 같은 소셜 ID 공급자에 대해 실시됩니다. 사용자가 이러한 유형의 자격 증명을 이용하여 로그인하는 경우가 많다면 Azure AD는 적합한 솔루션이 아닙니다. 

또한 Azure AD가 Access Control과 정확하게 동일한 인증 프로토콜을 반드시 지원하지는 않습니다. 예를 들어, Access Control과 Azure AD가 모두 OAuth를 지원하지만 구현에는 각각 약간의 차이가 있습니다. 다른 구현은 마이그레이션의 일환으로 코드를 수정해야 합니다.

한편 Azure AD는 Access Control 고객에게 몇 가지 이점을 제공합니다. Azure AD는 클라우드 호스팅된 Microsoft 회사 및 학교 계정을 지원합니다. Microsoft 회사 및 학교 계정은 Access Control 고객이 일반적으로 사용하는 계정이기도 합니다. 

Azure AD 테넌트는 AD FS를 통해 하나 이상의 온-프레미스 Active Directory 인스턴스로 페더레이션될 수 있습니다. 이 방식으로 앱이 클라우드 기반 사용자와 온-프레미스로 호스트된 사용자를 인증할 수 있습니다. Ws-Federation 프로토콜도 지원하기 때문에 WIF를 사용하여 웹 애플리케이션과 비교적 간단하게 통합할 수 있습니다.

다음 표에서는 웹 애플리케이션과 관련 있는 Access Control의 기능과 Azure AD에서 제공되는 기능을 비교합니다. 

대략적으로 봤을 때 *사용자가 Microsoft 회사 및 학교 계정으로만 로그인하는 경우에는 Azure Active Directory를 선택하는 것이 좋습니다*.

| 기능 | Access Control 지원 | Azure AD 지원 |
| ---------- | ----------- | ---------------- |
| **계정 유형** | | |
| Microsoft 회사 또는 학교 계정 | 지원됨 | 지원됨 |
| Windows Server Active Directory 및 AD FS의 계정 |- Azure AD 테넌트와의 페더레이션을 통해 지원 <br />- AD FS와의 직접 페더레이션을 통해 지원 | Azure AD 테넌트와의 페더레이션을 통해서만 지원 | 
| 다른 엔터프라이즈 ID 관리 시스템의 계정 |- Azure AD 테넌트와의 페더레이션을 통해 지원 <br />- 직접 페더레이션을 통해 지원 | Azure AD 테넌트와의 페더레이션을 통해 지원 |
| 개인용 Microsoft 계정 | 지원됨 | Azure AD v2.0 OAuth 프로토콜을 통해 지원(다른 프로토콜은 지원 불가) | 
| Facebook, Google, Yahoo 계정 | 지원됨 | 지원되지 않음 |
| **프로토콜 및 SDK 호환성** | | |
| WIF | 지원됨 | 지원됨(제한된 명령만 사용 가능) |
| WS-Federation | 지원됨 | 지원됨 |
| OAuth 2.0 | 초안 13 지원 | 최신 사양인 RFC 6749 지원 |
| WS-Trust | 지원됨 | 지원되지 않음 |
| **토큰 형식** | | |
| JWT | 베타에서 지원 | 지원됨 |
| SAML 1.1 | 지원됨 | 미리 보기 |
| SAML 2.0 | 지원됨 | 지원됨 |
| SWT | 지원됨 | 지원되지 않음 |
| **사용자 지정** | | |
| 사용자 지정 가능한 홈 영역 검색/계정 선택 UI | 코드를 다운로드하여 앱에 통합 | 지원되지 않음 |
| 사용자 지정 토큰 서명 인증서 업로드 | 지원됨 | 지원됨 |
| 토큰의 클레임 사용자 지정 |- ID 공급자의 입력 클레임 전달<br />- ID 공급자의 액세스 토큰을 클레임으로서 가져오기<br />- 입력 클레임의 값을 바탕으로 출력 클레임 발행<br />- 상수 값을 사용하여 출력 클레임 발행 |- 페더레이션된 ID 공급자의 클레임 전달 불가<br />- ID 공급자의 액세스 토큰을 클레임으로서 가져오기 불가<br />- 입력 클레임의 값을 바탕으로 출력 클레임 발행 불가<br />- 상수 값을 사용하여 출력 클레임 발행 가능<br />- Azure AD에 동기화된 사용자의 속성을 바탕으로 출력 클레임 발행 가능 |
| **Automation** | | |
| 구성 및 관리 작업 자동화 | Access Control 관리 서비스를 통해 지원 | Microsoft Graph 및 Azure AD Graph API를 통해 지원 |

Azure AD가 애플리케이션 및 서비스에 적합한 마이그레이션 경로라고 판단한 경우, 앱에 Azure AD를 통합하는 두 가지 방법을 모두 알고 있어야 합니다.

WS-Federation 또는 WIF를 사용하여 Azure AD와 통합하려면 [비갤러리 애플리케이션에 대한 페더레이션된 Single Sign-On 구성](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)에 설명된 방법을 따르는 것이 좋습니다. 이 문서에서는 SAML 기반 Single Sign-On을 위해 Azure AD를 구성하는 방법을 설명하고 있지만, Ws-Federation을 구성할 때도 마찬가지 방법을 사용할 수 있습니다. 이 방법을 사용하려면 Azure AD Premium 라이선스가 필요합니다. 이 방법에는 두 가지 이점이 있습니다.

- Azure AD 토큰 사용자 지정의 유연성이 제공됩니다. Access Control에서 발행한 클레임과 일치하도록 Azure AD에서 발행한 클레임을 사용자 지정할 수 있습니다. 특히 사용자 ID 또는 이름 식별자 클레임이 포함됩니다. 기술을 변경한 다음에도 사용자들이 전과 동일하게 사용자 식별자를 받기 위해서는 Azure AD에서 발행한 사용자 ID가 Access Control에서 발생한 사용자 ID와 일치해야 합니다.
- 사용자가 직접 수명을 제어하는 애플리케이션에 맞게 토큰 서명 인증서를 구성할 수 있습니다.

> [!NOTE]
> 이 방법을 사용하려면 Azure AD Premium 라이선스가 필요합니다. Access Control 고객이고 애플리케이션에 대한 Single Sign-On을 설정하기 위해 Premium 라이선스가 필요한 경우 저희에게 문의하세요. 사용할 수 있는 개발자 라이선스를 제공합니다.

또 다른 방법은 [이 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)을 따르는 것입니다. 코드 샘플에서는 Ws-Federation을 설정하는 다른 방법을 설명합니다. 코드 샘플에서는 WIF가 아닌 ASP.NET 4.5 OWIN 미들웨어를 사용하지만 WIF를 사용하는 앱에서도 유효한 앱 등록 방법을 사용할 수 있으며, 이때 Azure AD Premium 라이선스가 필요하지 않습니다. 

이 방법을 선택한 경우에는 [Azure AD의 서명 키 롤오버](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)에 대해 이해해야 합니다. 이 방법에서는 Azure AD 글로벌 서명 키를 사용하여 토큰을 발행합니다. 기본적으로 WIF는 자동으로 서명 키를 업데이트하지 않습니다. Azure AD가 글로벌 서명 키를 교대시킬 때 변화된 사항이 적용될 수 있도록 WIF 구현이 준비가 되어 있어야 합니다. 자세한 내용은 [Azure AD의 서명 키 롤오버에 대한 중요 정보](https://msdn.microsoft.com/library/azure/dn641920.aspx)를 참조하세요.

OpenID Connect 또는 OAuth 프로토콜을 통해 Azure AD와 통합할 수 있다면 그렇게 하는 것이 좋습니다. [Azure AD 개발자 가이드](https://aka.ms/aaddev)에서 웹 애플리케이션에 Azure AD를 통합하는 방법에 대한 방대한 설명서와 자료를 확인할 수 있습니다.

#### <a name="migrate-to-azure-active-directory-b2c"></a>Azure Active Directory B2C로 마이그레이션

또 다른 마이그레이션 방법으로 Azure AD B2C가 있습니다. Azure AD B2C는 Access Control처럼 개발자들이 인증 및 ID 관리 로직을 클라우드 서비스로 아웃소싱할 수 있도록 해 주는 클라우드 인증 서비스입니다. Azure AD B2C는 엄청나게 많은 활성 사용자를 갖는 소비자용 애플리케이션을 위해 설계된 유료 서비스로, 무료 등급과 프리미엄 등급이 제공됩니다.

Azure AD B2C는 Access Control과 마찬가지로 다양한 계정 유형을 지원합니다. Azure AD B2C로 Microsoft 계정이나 Facebook, Google, LinkedIn, GitHub 또는 Yahoo 계정 등을 사용하여 사용자가 로그인하도록 할 수 있습니다. Azure AD B2C는 사용자가 특정 애플리케이션을 위해 만드는 사용자 이름과 암호, 즉 “로컬 계정”도 지원합니다. 또한 Azure AD B2C는 로그인 흐름을 사용자 지정할 수 있도록 확장성도 제공합니다. 

그러나 Azure AD B2C는 Access Control 고객에게 필요할 수도 있는 다양한 인증 프로토콜과 토큰 형식을 지원하지 않습니다. 또한 ID 공급자, Microsoft 등으로부터 토큰을 받고 사용자에 관한 추가 정보를 쿼리하는 데도 Azure AD B2C를 사용할 수 없습니다.

다음 표에서는 웹 애플리케이션과 관련 있는 Access Control의 기능과 Azure AD B2C에서 제공되는 기능을 비교합니다. 대략적으로 봤을 때 *애플리케이션이 소비자용이거나 다양한 계정 유형을 지원하는 경우 Azure AD B2C를 선택하는 것이 좋습니다.*

| 기능 | Access Control 지원 | Azure AD B2C 지원 |
| ---------- | ----------- | ---------------- |
| **계정 유형** | | |
| Microsoft 회사 또는 학교 계정 | 지원됨 | 사용자 지정 정책을 통한 지원  |
| Windows Server Active Directory 및 AD FS의 계정 | AD FS를 사용한 직접 페더레이션을 통해 지원 | 사용자 지정 정책을 사용하여 SAML 페더레이션을 통해 지원 |
| 다른 엔터프라이즈 ID 관리 시스템의 계정 | Ws-Federation을 바탕으로 직접 페더레이션을 통해 지원 | 사용자 지정 정책을 사용하여 SAML 페더레이션을 통해 지원 |
| 개인용 Microsoft 계정 | 지원됨 | 지원됨 | 
| Facebook, Google, Yahoo 계정 | 지원됨 | Facebook 및 Google은 기본적으로 지원, Yahoo는 사용자 지정 정책을 사용하여 OpenID Connect 페더레이션을 통해 지원 |
| **프로토콜 및 SDK 호환성** | | |
| WIF(Windows Identity Foundation) | 지원됨 | 지원되지 않음 |
| WS-Federation | 지원됨 | 지원되지 않음 |
| OAuth 2.0 | 초안 13 지원 | 최신 사양인 RFC 6749 지원 |
| WS-Trust | 지원됨 | 지원되지 않음 |
| **토큰 형식** | | |
| JWT | 베타에서 지원 | 지원됨 |
| SAML 1.1 | 지원됨 | 지원되지 않음 |
| SAML 2.0 | 지원됨 | 지원되지 않음 |
| SWT | 지원됨 | 지원되지 않음 |
| **사용자 지정** | | |
| 사용자 지정 가능한 홈 영역 검색/계정 선택 UI | 코드를 다운로드하여 앱에 통합 | 사용자 지정 CSS를 이용하여 UI 사용자 지정 가능 |
| 사용자 지정 토큰 서명 인증서 업로드 | 지원됨 | 사용자 정책을 통해 사용자 지정 서명 키 지원(인증서는 지원 불가) |
| 토큰의 클레임 사용자 지정 |- ID 공급자의 입력 클레임 전달<br />- ID 공급자의 액세스 토큰을 클레임으로서 가져오기<br />- 입력 클레임의 값을 바탕으로 출력 클레임 발행<br />- 상수 값을 사용하여 출력 클레임 발행 |- ID 공급자의 클레임 전달 가능. 일부 클레임에는 사용자 지정 정책 필요<br />- ID 공급자의 액세스 토큰을 클레임으로서 가져오기 불가<br />- 사용자 지정 정책을 통해 입력 클레임의 값을 바탕으로 출력 클레임 발행 가능<br />- 사용자 지정 정책을 통해 상수 값을 바탕으로 출력 클레임 발행 가능 |
| **Automation** | | |
| 구성 및 관리 작업 자동화 | Access Control 관리 서비스를 통해 지원 |- Azure AD Graph API를 통한 사용자 생성 허용<br />- B2C 테넌트, 애플리케이션 또는 정책을 프로그래밍 방식으로 생성 불가 |

Azure AD B2C가 애플리케이션 및 서비스에 적합한 마이그레이션 경로라고 판단한 경우, 다음 리소스를 살펴보세요.

- [Azure AD B2C 설명서](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping ID 또는 Auth0로 마이그레이션

경우에 따라 주요 코드를 변경하지 않고 웹 애플리케이션에서 Access Control을 교체하는 데 Azure AD와 Azure AD B2C만으로 충분하지 않을 수 있습니다. 다음과 같은 몇 가지 일반적인 예가 있습니다.

- Google 또는 Facebook 등과 같은 소셜 ID 공급자에서 WIF 또는 WS-Federation을 로그인에 사용하는 웹 애플리케이션.
- WS-Federation 프로토콜을 통해 엔터프라이즈 ID 공급자에 대한 직접 페더레이션을 수행하는 웹 애플리케이션.
- Access Control에서 발행한 토큰의 클레임으로 소셜 ID 공급자(예: Google 또는 Facebook)에서 발행한 액세스 토큰이 필요한 웹 애플리케이션.
- Azure AD 또는 Azure AD B2C가 재현할 수 없는 복잡한 토큰 변환 규칙을 사용하는 웹 애플리케이션.
- 많은 ID 공급자에 대한 페더레이션을 중앙에서 ACS를 사용하여 관리하는 다중 테넌트 웹 애플리케이션

이러한 경우 웹 애플리케이션을 다른 클라우드 인증 서비스로 마이그레이션하는 것을 고려해 보는 것이 좋습니다. 다음 옵션에 대해 알아보세요. 다음 옵션은 Access Control과 비슷한 기능을 제공합니다.

|     |     |
| --- | --- |
| ![이 이미지는 좋아하게 되었습니다 로고를 보여줍니다.](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs)은 [Access Control 고객을 위한 대략적인 마이그레이션 지침](https://auth0.com/acs)에 따라 만들어진 유동 클라우드 ID 서비스이며 ACS가 지원하는 거의 모든 기능을 지원합니다. |
| ![이 이미지는 Ping Id 로고를 표시 합니다.](./media/active-directory-acs-migration/rsz-ping.png) | [Ping ID](https://www.pingidentity.com)는 ACS와 유사한 두 가지 솔루션을 제공합니다. 하나는 ACS와 동일한 많은 기능을 지 원하는 클라우드 id 서비스이 고, 서비스는 더 많은 유연성을 제공 하는 온-프레미스 id 제품에서 유사 합니다. 이러한 제품의 사용에 대한 자세한 내용은 [Ping의 ACS 사용 중지 지침](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)을 참조하세요. |

Ping ID와 Auth0으로 작업하는 목적은 모든 Access Control 고객이 앱 및 서비스를 Access Control에서 이전하는 데 필요한 작업 양을 최소화하는 마이그레이션 경로를 갖도록 하는 것입니다.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>능동 인증을 사용하는 웹 서비스

Access Control에서 발행한 토큰으로 보호되는 웹 서비스의 경우 Access Control은 다음과 같은 기능 및 특성을 제공합니다.

- Access Control 네임스페이스에 하나 이상의 *서비스 ID* 등록. 서비스 ID는 토큰을 요청하는 데 사용할 수 있습니다.
- 다음과 같은 자격 증명 유형을 사용하여 토큰 요청 시 OAuth WRAP 및 OAuth 2.0 초안 13 프로토콜 지원:
    - 서비스 ID를 위해 만든 단순한 암호
    - 대칭 키 또는 X509 인증서를 사용하여 서명된 SWT
    - 신뢰할 수 있는 ID 공급자(주로 AD FS 인스턴스)가 발행한 SAML 토큰
- JWT, SAML 1.1, SAML 2.0 및 SWT 토큰 형식 지원.
- 단순 토큰 변환 규칙.

Access Control의 서비스 ID는 일반적으로 서버-투-서버(S2S) 인증을 구현하는 데 사용됩니다. 

#### <a name="migrate-to-azure-active-directory"></a>Azure Active Directory로 마이그레이션

이러한 인증 흐름에 대해 Microsoft에서 권장하는 방법은 [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/)로 마이그레이션하는 것입니다. Azure AD는 Microsoft 회사 또는 학교 계정을 위한 클라우드 기반 ID 공급자입니다. Azure AD는 Office 365, Azure 등을 위한 ID 공급자입니다. 

또한 Azure AD는 OAuth 클라이언트 자격 증명 부여에 대한 Azure AD의 구현을 사용하여 서버-투-서버 인증에도 사용할 수 있습니다. 다음 표에서는 서버-투-서버 인증과 관련 있는 Access Control의 기능과 Azure AD의 기능을 비교하고 있습니다.

| 기능 | Access Control 지원 | Azure AD 지원 |
| ---------- | ----------- | ---------------- |
| 웹 서비스를 등록하는 방법 | Access Control 관리 포털에서 신뢰 당사자 만들기 | Azure Portal에서 Azure AD 웹 애플리케이션 만들기 |
| 클라이언트를 등록하는 방법 | Access Control 관리 포털에서 서비스 ID 만들기 | Azure Portal에서 또 다른 Azure AD 웹 애플리케이션 만들기 |
| 사용되는 프로토콜 |- OAuth WRAP 프로토콜<br />- OAuth 2.0 초안 13 클라이언트 자격 증명 부여 | OAuth 2.0 클라이언트 자격 증명 부여 |
| 클라이언트 인증 방법 |- 단순한 암호<br />- 서명된 SWT<br />- 페더레이션 ID 공급자로부터 전달된 SAML 토큰 |- 단순한 암호<br />- 서명된 JWT |
| 토큰 형식 |- JWT<br />- SAML 1.1<br />- SAML 2.0<br />- SWT<br /> | JWT만 |
| 토큰 변환 |- 사용자 지정 클레임 추가<br />- 단순한 if-then 클레임 발행 로직 | 사용자 지정 클레임 추가 | 
| 구성 및 관리 작업 자동화 | Access Control 관리 서비스를 통해 지원 | Microsoft Graph 및 Azure AD Graph API를 통해 지원 |

서버-투-서버 시나리오를 구현하는 방법은 아래의 리소스를 참조하세요.

- [Azure AD 개발자 가이드](https://aka.ms/aaddev)의 서비스-투-서비스 섹션
- [단순한 암호 클라이언트 자격 증명을 사용한 디먼 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [인증서 클라이언트 자격 증명을 사용한 디먼 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Ping ID 또는 Auth0로 마이그레이션

경우에 따라 Azure AD 클라이언트 자격 증명 및 OAuth 부여의 구현만으로는 주요 코드를 변경하지 않고 해당 아키텍처에서 Access Control을 교체하는 데 충분하지 않을 수 있습니다. 다음과 같은 몇 가지 일반적인 예가 있습니다.

- JWT 이외의 토큰 형식을 사용하는 서버-투-서버 인증.
- 외부 ID 공급자가 제공하는 입력 토큰을 사용하는 서버-투-서버 인증.
- Azure AD가 재현할 수 없는 토큰 변환 규칙을 사용하는 서버-투-서버 인증.

이런 경우 웹 애플리케이션을 다른 클라우드 인증 서비스로 마이그레이션하는 것을 고려해 볼 수 있습니다. 다음 옵션에 대해 알아보세요. 다음 옵션은 Access Control과 비슷한 기능을 제공합니다.

|     |     |
| --- | --- |
| ![이 이미지는 좋아하게 되었습니다 로고를 보여줍니다.](./media/active-directory-acs-migration/rsz-auth0.png) | [Auth0](https://auth0.com/acs)은 [Access Control 고객을 위한 대략적인 마이그레이션 지침](https://auth0.com/acs)에 따라 만들어진 유동 클라우드 ID 서비스이며 ACS가 지원하는 거의 모든 기능을 지원합니다. |
| ![이 이미지는 Ping Id 로고를 표시 합니다.](./media/active-directory-acs-migration/rsz-ping.png) | [Ping ID](https://www.pingidentity.com)는 ACS와 유사한 두 가지 솔루션을 제공합니다. 하나는 ACS와 동일한 많은 기능을 지 원하는 클라우드 id 서비스이 고, 서비스는 더 많은 유연성을 제공 하는 온-프레미스 id 제품에서 유사 합니다. 이러한 제품의 사용에 대한 자세한 내용은 [Ping의 ACS 사용 중지 지침](https://www.pingidentity.com/en/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html)을 참조하세요. |

Ping ID와 Auth0으로 작업하는 목적은 모든 Access Control 고객이 앱 및 서비스를 Access Control에서 이전하는 데 필요한 작업 양을 최소화하는 마이그레이션 경로를 갖도록 하는 것입니다.

#### <a name="passthrough-authentication"></a>통과 인증

임의 토큰 변환을 사용하는 통과 인증에 해당하는 Microsoft의 ACS 기술은 없습니다. 이러한 인증을 사용해야 하는 고객은 가장 근접한 솔루션을 제공하는 Auth0을 사용할 수 있습니다.

## <a name="questions-concerns-and-feedback"></a>질문, 우려 사항 및 피드백

많은 Access Control 고객이 이 문서를 읽은 후에도 확실한 마이그레이션 경로를 찾지 못할 것임을 압니다. 올바른 계획을 결정하기 위해서는 지원이나 지침이 필요할 수 있습니다. 마이그레이션 시나리오에 대해 상의하고 궁금한 점을 물어보려면 이 페이지에 댓글을 남겨 주세요.
