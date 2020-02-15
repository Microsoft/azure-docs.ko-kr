---
title: Microsoft ID 플랫폼의 인증 | Azure
description: V2.0 (Microsoft identity platform)에서 인증의 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 6e14284b5d653af01631d56acf954f9c2a1f10ab
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194998"
---
# <a name="authentication-basics"></a>인증 기본 사항

## <a name="what-is-authentication"></a>인증 이란?

이 문서에서는 보호 된 웹 앱, web Api 또는 보호 된 웹 Api를 호출 하는 앱을 만들기 위해 이해 해야 하는 여러 가지 인증 개념을 설명 합니다. 익숙하지 않은 용어가 표시 되는 경우 [용어집](developer-glossary.md) 또는 기본 개념을 다루는 [Microsoft id 플랫폼 비디오](identity-videos.md) 를 사용해 보세요.

**인증은** 사용자가 누구 인지를 증명 하는 과정입니다. Authentication(인증)은 종종 AuthN으로 축약됩니다.

**권한 부여** 는 인증 된 당사자에 게 작업을 수행할 수 있는 권한을 부여 하는 행위입니다. 액세스할 수 있는 데이터와 해당 데이터로 수행할 수 있는 작업을 지정 합니다. Authorization(권한 부여)는 종종 AuthZ로 축약됩니다.

여러 앱에서 사용자를 추가 하거나 제거 해야 하는 경우 높은 관리 부담을 초래 하는 앱을 만드는 대신, 앱이 중앙 집중식 id 공급자에 게 해당 책임을 위임할 수 있습니다.

Azure AD (Azure Active Directory)는 클라우드의 중앙 집중식 id 공급자입니다. 인증 및 권한 부여를 위임 하면 사용자가 특정 위치에 있어야 하는 조건부 액세스 정책, multi-factor authentication 사용 및 사용자가 한 번 로그인 한 다음 자동으로 로그인 할 수 있도록 하는 시나리오를 사용할 수 있습니다. 동일한 중앙 디렉터리를 공유 하는 모든 웹 앱에 로그인 합니다. 이 기능을 SSO (Single Sign On) 라고 합니다.

중앙 집중식 id 공급자는 전 세계에 있는 사용자가 기업 네트워크에서 반드시 로그인 하지 않아도 되는 앱에 훨씬 더 중요 합니다. Azure AD는 사용자를 인증 하 고 액세스 토큰을 제공 합니다. [액세스 토큰](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) 은 권한 부여 서버에서 발급 하는 보안 토큰입니다. 토큰에 사용 되는 사용자 및 앱에 대 한 정보를 포함 합니다. 웹 Api 및 기타 보호 된 리소스에 액세스 하는 데 사용할 수 있습니다.

Microsoft id 플랫폼을 사용 하면 id를 서비스로 제공 하 여 응용 프로그램 개발자에 대 한 인증을 간소화 하 고, [OAuth 2.0](https://oauth.net/2/) 및 [openid connect Connect](https://openid.net/connect/)와 같은 산업 표준 프로토콜을 지원 하 고, 코딩을 빠르게 시작 하는 데 도움이 되는 다양 한 플랫폼에 대 한 오픈 소스 라이브러리를 지원 합니다. 개발자는이를 통해 모든 Microsoft id를 로그인 하는 응용 프로그램을 작성 하 고, [Microsoft Graph](https://developer.microsoft.com/graph/), 호출할 토큰, 기타 microsoft api 또는 개발자가 빌드한 api를 가져올 수 있습니다. 자세한 내용은 [Microsoft id 플랫폼의 진화](about-microsoft-identity-platform.md)를 참조 하세요.

### <a name="tenants"></a>테넌트

클라우드 id 공급자는 많은 조직에 서비스를 제공 합니다. 서로 다른 조직의 사용자를 유지 하기 위해 Azure AD는 조직 마다 하나의 테 넌 트를 사용 하 여 테 넌 트로 분할 됩니다.

테 넌 트는 사용자 및 연결 된 앱을 추적 합니다. Microsoft id 플랫폼은 개인 Microsoft 계정으로 로그인 하는 사용자도 지원 합니다.

또한 Azure AD는 조직이 Google 계정과 같은 소셜 id를 사용 하 여 사용자, 일반적으로 고객에 게 로그인 할 수 있도록 Azure Active Directory B2C 제공 합니다. 자세한 내용은 [Azure Active Directory B2C 설명서](https://docs.microsoft.com/azure/active-directory-b2c) 를 참조 하세요.

### <a name="security-tokens"></a>보안 토큰

보안 토큰에는 사용자 및 앱에 대 한 정보가 포함 됩니다. Azure AD는 클레임을 포함 하는 JWTs (JSON 기반 토큰)를 사용 합니다.

클레임은 [클라이언트 응용 프로그램](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) 또는 [리소스 소유자](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)와 같은 하나의 엔터티에 대 한 어설션을 [리소스 서버](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)와 같은 다른 엔터티에 제공 합니다.

클레임은 토큰 주체에 대 한 팩트를 릴레이 하는 이름/값 쌍입니다. 예를 들어 클레임은 [권한 부여 서버](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server)에서 인증 된 보안 주체에 대 한 팩트를 포함할 수 있습니다. 지정 된 토큰에 있는 클레임은 토큰 유형, 주체를 인증 하는 데 사용 되는 자격 증명 유형, 응용 프로그램 구성 등을 비롯 한 다양 한 사항에 따라 달라 집니다.

응용 프로그램은 다음과 같은 다양 한 작업에 대해 클레임을 사용할 수 있습니다.

* 토큰 유효성 검사
* 토큰 주체의 테 넌 트 식별
* 사용자 정보 표시
* 주체의 권한 부여 확인

클레임은 다음과 같은 정보를 제공 하는 키-값 쌍으로 구성 됩니다.

* 토큰을 생성 한 보안 토큰 서버
* 토큰이 생성 된 날짜
* Subject (예: 디먼를 제외한 사용자)
* 토큰이 생성 된 앱 인 대상 그룹
* 토큰을 요청 하는 앱 (클라이언트)입니다. 웹 앱의 경우이는 대상 그룹과 동일할 수 있습니다.

자세한 클레임 정보는 [액세스 토큰](access-tokens.md) 및 [ID 토큰](id-tokens.md)을 참조 하세요.

토큰의 유효성을 검사 하기 위해 토큰을 생성 한 앱, 사용자에 게 로그인 한 웹 앱 또는 호출 되는 Web API에 따라 결정 됩니다. 토큰은 개인 키를 사용 하 여 보안 토큰 서버 (STS)에 의해 서명 됩니다. STS는 해당 공개 키를 게시 합니다. 토큰의 유효성을 검사 하기 위해 앱은 STS 공개 키를 사용 하 여 서명을 확인 하 고 개인 키를 사용 하 여 서명을 만들었는지 확인 합니다.

토큰은 제한 된 시간 동안만 유효 합니다. 일반적으로 STS는 응용 프로그램 또는 보호 된 리소스에 액세스 하기 위한 액세스 토큰과 액세스 토큰이 만료 될 때 액세스 토큰을 새로 고치는 데 사용 되는 새로 고침 토큰을 사용 하 여 토큰 쌍을 제공 합니다.

액세스 토큰은 웹 API에 `Authorization` 헤더의 전달자 토큰으로 전달 됩니다. 앱은 STS에 새로 고침 토큰을 제공할 수 있으며, 앱에 대 한 사용자 액세스가 취소 되지 않은 경우 새 액세스 토큰과 새로 고침 토큰을 다시 받게 됩니다. 이는 엔터프라이즈를 종료 하는 사람의 시나리오를 처리 하는 방법입니다. STS는 새로 고침 토큰을 받을 때 사용자에 게 더 이상 권한이 부여 되지 않은 경우 다른 유효한 액세스 토큰을 발급 하지 않습니다.

## <a name="application-model"></a>애플리케이션 모델

응용 프로그램은 사용자 자체에 로그인 하거나 id 공급자에 로그인을 위임할 수 있습니다. Azure AD에서 지 원하는 로그인 시나리오에 대해 알아보려면 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md) 를 참조 하세요.

Id 공급자가 사용자에 게 특정 앱에 대 한 액세스 권한이 있음을 알고 있는 경우 사용자와 응용 프로그램을 모두 id 공급자에 등록 해야 합니다. Azure AD에 응용 프로그램을 등록 하면 Azure AD와 통합 될 수 있도록 응용 프로그램에 대 한 id 구성을 제공 하는 것입니다. 앱을 등록 하면 다음 작업도 수행할 수 있습니다.

* 로그인 대화 상자에서 응용 프로그램의 브랜딩을 사용자 지정 합니다. 이는 사용자가 앱과 함께 사용 하는 첫 번째 환경 이기 때문에 중요 합니다.
* 사용자가 조직에 속한 경우에만 로그인 할 수 있도록 할지 결정 합니다. 이 응용 프로그램은 단일 테 넌 트 응용 프로그램입니다. 또는 사용자가 회사 또는 학교 계정을 사용 하 여 로그인 하도록 허용 합니다. 다중 테 넌 트 응용 프로그램입니다. 개인 Microsoft 계정이 나 LinkedIn, Google 등의 소셜 계정도 허용할 수 있습니다.
* 요청 범위 권한입니다. 예를 들어, 로그인 한 사용자의 프로필을 읽을 수 있는 권한을 부여 하는 "사용자 읽기" 범위를 요청할 수 있습니다.
* 웹 API에 대 한 액세스를 정의 하는 범위를 정의 합니다. 일반적으로 앱이 API에 액세스 하려는 경우 사용자가 정의 하는 범위에 대 한 권한을 요청 해야 합니다.
* Azure ad에서 앱의 id를 증명 하는 Azure AD와 암호를 공유 합니다.  이는 앱이 기밀 클라이언트 응용 프로그램 인 경우와 관련이 있습니다. 기밀 클라이언트 응용 프로그램은 자격 증명을 안전 하 게 저장할 수 있는 응용 프로그램입니다. 자격 증명을 저장 하려면 신뢰할 수 있는 백엔드 서버가 필요 합니다.

등록 된 응용 프로그램에는 토큰을 요청할 때 앱이 Azure AD와 공유 하는 고유 식별자가 제공 됩니다. 앱이 [기밀 클라이언트 응용 프로그램](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)인 경우 인증서 또는 암호의 사용 여부에 따라 비밀 또는 공개 키 *도 공유 합니다.

Microsoft id 플랫폼은 다음과 같은 두 가지 주요 함수를 사용 하는 모델을 사용 하는 응용 프로그램을 나타냅니다.

* 지 원하는 인증 프로토콜을 통해 앱 식별
* 인증에 필요한 모든 식별자, Url, 암호 및 관련 정보를 제공 합니다.

Microsoft id 플랫폼:

* 런타임에 인증을 지 원하는 데 필요한 모든 데이터를 저장 합니다.
* 앱이 액세스 하는 데 필요한 리소스를 결정 하 고 지정 된 요청을 수행 해야 하는 상황에 따라 모든 데이터를 저장 합니다.
* 앱 개발자 테 넌 트 및 다른 Azure AD 테 넌 트 내에서 앱 프로 비전을 구현 하기 위한 인프라를 제공 합니다.
* 토큰 요청 시 사용자 동의를 처리 하 고 테 넌 트 간에 앱을 동적으로 프로 비전 합니다.

동의는 리소스 소유자를 대신 하 여 클라이언트 응용 프로그램이 특정 권한으로 보호 되는 리소스에 액세스할 수 있는 권한 부여를 부여 하는 프로세스입니다. Microsoft id 플랫폼:

* 앱이 대신 리소스에 액세스하는 데 관해 사용자 및 관리자가 동적으로 권한을 부여하거나 동의를 거부할 수 있도록 설정합니다.
* 관리자가 궁극적으로 앱이 수행할 수 있는 작업, 특정 앱을 사용할 수 있는 사용자 및 디렉터리 리소스에 액세스하는 방식을 결정하도록 설정합니다.

Microsoft id 플랫폼에서 [응용 프로그램 개체](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) 는 응용 프로그램을 설명 합니다. 배포 시 Microsoft id 플랫폼은 응용 프로그램 개체를 청사진으로 사용 하 여 디렉터리 또는 테 넌 트 내의 구체적인 응용 프로그램 인스턴스를 나타내는 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)를 만듭니다. 서비스 주체는 앱이 특정 대상 디렉터리에서 실제로 수행할 수 있는 작업, 사용할 수 있는 리소스, 액세스 권한이 있는 리소스 등을 정의 합니다. Microsoft id 플랫폼은 **동의**를 통해 응용 프로그램 개체에서 서비스 주체를 만듭니다.

다음 다이어그램에는 동의를 통해 구동되는 간소화된 Microsoft ID 플랫폼 프로비저닝 흐름이 나와 있습니다. 두 개의 테 넌 트 a와 B가 표시 됩니다. 테 넌 트 A는 응용 프로그램을 소유 합니다. 테 넌 트 B는 서비스 주체를 통해 응용 프로그램을 인스턴스화합니다.  

![동의를 통해 구동되는 간소화된 프로비전 흐름](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

이 프로비전 흐름에서

1. 테넌트 B의 사용자가 앱에 로그인하려고 시도하면 권한 부여 엔드포인트에서 애플리케이션의 토큰을 요청합니다.
1. 사용자 자격 증명을 획득 하 여 인증을 확인 합니다.
1. 사용자에 게 테 넌 트 B에 대 한 액세스 권한을 얻기 위해 앱에 대 한 동의를 제공 하 라는 메시지가 표시 됩니다.
1. Microsoft id 플랫폼은 테 넌 트 B에서 서비스 주체를 만들기 위한 청사진으로 테 넌 트 A의 응용 프로그램 개체를 사용 합니다.
1. 사용자가 요청 된 토큰을 받습니다.

추가 테 넌 트에 대해이 프로세스를 반복할 수 있습니다. 테넌트 A에서 앱(애플리케이션 개체)에 대한 청사진을 유지합니다. 앱이 승인 된 다른 모든 테 넌 트의 사용자 및 관리자는 응용 프로그램이 각 테 넌 트의 해당 서비스 주체 개체를 통해 수행할 수 있는 작업을 계속 제어 합니다. 자세한 내용은 [Microsoft ID 플랫폼의 애플리케이션 및 서비스 주체 개체](app-objects-and-service-principals.md)를 참조하세요.

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Azure AD를 사용 하 여 웹 앱 로그인 흐름

사용자가 브라우저에서 웹 앱으로 이동 하면 다음 작업이 수행 됩니다.

* 웹 앱은 사용자가 인증 되었는지 여부를 확인 합니다.
* 사용자가 인증 되지 않은 경우 웹 앱은 사용자를 로그인 하기 위해 Azure AD에 위임 합니다. 로그인은 조직의 정책을 준수 하 게 됩니다 .이는 사용자에 게 사용자에 게 자격 증명을 입력 하도록 요청 하거나 (예: Windows Hello 사용), 암호를 사용 하지 않고 multi-factor authentication을 사용 하 여 사용자에 게 요청 하는 것을 의미할 수 있습니다.
* 사용자에 게 클라이언트 앱에 필요한 액세스에 동의 하 라는 메시지가 표시 됩니다. 이 때문에 azure AD에서 사용자가 동의한 액세스를 나타내는 토큰을 제공할 수 있도록 Azure AD에 클라이언트 앱을 등록 해야 합니다.

사용자가 성공적으로 인증 된 경우:

* Azure AD는 웹 앱에 토큰을 보냅니다.
* 쿠키는 브라우저의 쿠키 jar에 있는 사용자의 id를 포함 하는 Azure AD의 도메인과 연결 되어 저장 됩니다. 다음에 앱이 브라우저를 사용 하 여 Azure AD 권한 부여 끝점으로 이동 하면 브라우저는 사용자가 다시 로그인 하지 않아도 되도록 쿠키를 표시 합니다. 이는 SSO를 구현 하는 방법 이기도 합니다. 쿠키는 Azure AD에서 생성 되며 Azure AD 에서만 이해할 수 있습니다.
* 그러면 웹 앱에서 토큰의 유효성을 검사 합니다. 유효성 검사가 성공 하면 웹 앱은 보호 된 페이지를 표시 하 고 세션 쿠키를 브라우저의 쿠키 jar에 저장 합니다. 사용자가 다른 페이지로 이동 하면 웹 앱은 세션 쿠키에 따라 사용자가 인증 되었음을 인식 합니다.

다음 시퀀스 다이어그램은 이러한 상호 작용을 요약 합니다.

![웹 앱 인증 프로세스](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>웹 앱에서 사용자 인증 여부를 결정 하는 방법

웹 앱 개발자는 모든 또는 특정 페이지에 인증이 필요한 지 여부를 나타낼 수 있습니다. 예를 들어 ASP.NET/ASP.NET Core에서는 컨트롤러 작업에 `[Authorize]` 특성을 추가 하 여이 작업을 수행 합니다. 

이 특성을 사용 하면 ASP.NET는 사용자의 id가 포함 된 세션 쿠키의 존재 여부를 확인 합니다. 쿠키가 없는 경우 ASP.NET는 인증을 지정 된 id 공급자로 리디렉션합니다. Id 공급자가 Azure AD 인 경우 웹 앱은 인증을 `https://login.microsoftonline.com`으로 리디렉션하고 로그인 대화 상자를 표시 합니다.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>웹 앱이 Azure AD에 로그인을 위임 하 고 토큰을 가져오는 방법

브라우저를 통해 사용자 인증이 수행 됩니다. Openid connect 프로토콜은 표준 HTTP 프로토콜 메시지를 사용 합니다.
* 웹 앱은 Azure AD를 사용 하기 위해 HTTP 302 (리디렉션)을 브라우저로 보냅니다.
* 사용자가 인증 되 면 Azure AD는 브라우저를 통해 리디렉션을 사용 하 여 웹 앱에 토큰을 보냅니다.
* 리디렉션은 웹 앱에서 리디렉션 URI 형식으로 제공 됩니다. 이 리디렉션 URI는 Azure AD 응용 프로그램 개체에 등록 됩니다. 응용 프로그램을 여러 Url에 배포할 수 있기 때문에 여러 리디렉션 Uri가 있을 수 있습니다. 따라서 웹 앱은 사용할 리디렉션 URi도 지정 해야 합니다.
* Azure AD는 웹 앱에서 보낸 리디렉션 URI가 앱에 대해 등록 된 리디렉션 uri 중 하나 인지 확인 합니다.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Azure AD를 사용 하는 데스크톱 및 모바일 앱 로그인 흐름

위에서 설명한 흐름은 데스크톱 및 모바일 응용 프로그램에 약간의 차이가 있습니다.

데스크톱 및 모바일 응용 프로그램은 인증을 위해 포함 된 웹 컨트롤 또는 시스템 브라우저를 사용할 수 있습니다. 다음 다이어그램에서는 데스크톱 또는 모바일 앱이 MSAL (Microsoft 인증 라이브러리)을 사용 하 여 액세스 토큰을 획득 하 고 web Api를 호출 하는 방법을 보여 줍니다.

![데스크톱 앱이 표시 되는 방식](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL은 브라우저를 사용 하 여 토큰을 가져옵니다. 웹 앱과 마찬가지로 인증은 Azure AD에 위임 됩니다.

Azure AD는 웹 앱과 동일한 id 쿠키를 브라우저에 저장 하므로 네이티브 또는 모바일 앱이 시스템 브라우저를 사용 하는 경우 해당 웹 앱을 사용 하 여 SSO를 즉시 가져옵니다.

기본적으로 MSAL은 시스템 브라우저를 사용 합니다. 단, 포함 된 컨트롤을 사용 하 여 보다 통합 된 사용자 환경을 제공 하는 데스크톱 응용 프로그램 .NET Framework는 예외입니다.

## <a name="next-steps"></a>다음 단계

* 일반적인 용어에 대해 알아 보려면 [Microsoft id 플랫폼 개발자 용어집](developer-glossary.md) 을 참조 하세요.
* Microsoft id 플랫폼에서 지원 되는 사용자를 인증 하는 다른 시나리오에 대해 자세히 알아보려면 [인증 흐름 및 앱 시나리오](authentication-flows-app-scenarios.md) 를 참조 하세요.
* Microsoft 계정, Azure AD 계정 및 Azure AD B2C 사용자를 모두 간소화 된 단일 프로그래밍 모델로 사용 하는 응용 프로그램을 개발 하는 데 도움이 되는 Microsoft 라이브러리에 대해 알아보려면 [Msal 라이브러리](msal-overview.md) 를 참조 하세요.
* App Service 앱에 대 한 인증을 구성 하는 방법을 알아보려면 [Microsoft id 플랫폼과 통합 App Service](/azure/app-service/configure-authentication-provider-aad) 을 참조 하세요.
