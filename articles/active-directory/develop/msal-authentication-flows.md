---
title: MSAL 인증 흐름 | Microsoft
titleSuffix: Microsoft identity platform
description: MSAL (Microsoft 인증 라이브러리)에서 사용 하는 인증 흐름 및 부여에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/30/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ace636152f6a0c9bf3896860eb17cc291bef2887
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085119"
---
# <a name="authentication-flows"></a>인증 흐름

이 문서에서는 MSAL (Microsoft Authentication Library)에서 제공 하는 다양 한 인증 흐름을 설명 합니다.  이러한 흐름은 다양 한 응용 프로그램 시나리오에서 사용할 수 있습니다.

| 흐름 | Description | 사용 대상|  
| ---- | ----------- | ------- | 
| [대화형](#interactive) | 브라우저 또는 팝업 창을 통해 사용자에 게 자격 증명을 묻는 메시지를 표시 하는 대화형 프로세스를 통해 토큰을 가져옵니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md) |
| [암시적 권한 부여](#implicit-grant) | 백 엔드 서버 자격 증명 교환을 수행 하지 않고도 앱에서 토큰을 가져올 수 있습니다. 이렇게 하면 앱에서 사용자에 게 로그인 하 고, 세션을 유지 관리 하 고, 다른 web Api에 대 한 토큰을 가져올 수 있습니다.| [SPA (단일 페이지 응용 프로그램)](scenario-spa-overview.md) |
| [인증 코드](#authorization-code) | 웹 Api와 같은 보호 된 리소스에 대 한 액세스 권한을 얻기 위해 장치에 설치 된 앱에서 사용 됩니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md), [웹 앱](scenario-web-app-call-api-overview.md) | 
| [대리](#on-behalf-of) | 응용 프로그램은 다른 서비스 또는 web API를 호출 해야 하는 서비스 또는 웹 API를 호출 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 사용자와 즉각적인 상호 작용 없이 백그라운드에서 실행 해야 하는 서버 간 상호 작용에 일반적으로 사용 됩니다. | [디먼 앱](scenario-daemon-overview.md) |
| [디바이스 코드](#device-code) | 사용자가 스마트 TV, IoT 장치 또는 프린터와 같은 입력 제한 장치에 로그인 할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [Windows 통합 인증](scenario-desktop-acquire-token.md#integrated-windows-authentication) | 도메인 또는 Azure Active Directory (Azure AD)에 연결 된 컴퓨터의 응용 프로그램에서 사용자의 UI 상호 작용 없이 토큰을 자동으로 획득할 수 있습니다.| [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |
| [사용자 이름/암호](scenario-desktop-acquire-token.md#username-and-password) | 응용 프로그램에서 직접 암호를 처리 하 여 사용자에 게 로그인 할 수 있도록 합니다. 이 흐름을 권장 하지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username-and-password) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법
 
클라이언트를 구축 하는 방법에 따라 Microsoft id 플랫폼에서 지원 되는 인증 흐름 중 하나 (또는 여러 개)를 사용할 수 있습니다.  이러한 흐름은 다양 한 토큰 (id_tokens, 새로 고침 토큰, 액세스 토큰) 뿐만 아니라 인증 코드를 생성 하 고 다른 토큰을 사용 하 여 작동 하도록 할 수 있습니다. 이 차트에서는 다음 개요를 제공 합니다.
 
|흐름 | 위해서는 | id_token | 액세스 토큰 | 토큰 새로 고침 | 인증 코드 | 
|-----|----------|----------|--------------|---------------|--------------------|
|[인증 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[토큰 상환 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 토큰 새로 고침 | x | x | x| |
|[On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[장치 코드 흐름](v2-oauth2-device-code.md) | | x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x (앱 전용)| | |
 
암시적 모드를 통해 발급 된 토큰은 URL (`response_mode` `query` 또는 `fragment`)을 통해 브라우저에 다시 전달 되기 때문에 길이 제한이 있습니다.  일부 브라우저는 브라우저 표시줄에 배치할 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패할 수 있습니다.  따라서 이러한 토큰은 `groups` 또는 `wids` 클레임을 포함 하지 않습니다.

## <a name="interactive"></a>대화형

MSAL은 사용자에 게 로그인에 대 한 자격 증명을 대화형으로 요청 하 고 해당 자격 증명을 사용 하 여 토큰을 얻을 수 있는 기능을 지원 합니다.

![대화형 흐름 다이어그램](media/msal-authentication-flows/interactive.png)

MSAL.NET를 사용 하 여 특정 플랫폼에서 대화형으로 토큰을 얻는 방법에 대 한 자세한 내용은 다음을 참조 하세요.
- [Xamarin Android](msal-net-xamarin-android-considerations.md)
- [Xamarin iOS](msal-net-xamarin-ios-considerations.md)
- [유니버설 Windows 플랫폼](msal-net-uwp-considerations.md)

MSAL의 대화형 호출에 대 한 자세한 내용은 [MSAL .js 대화형 요청의 프롬프트 동작](msal-js-prompt-behavior.md)을 참조 하세요.

## <a name="implicit-grant"></a>암시적 부여

MSAL은 앱이 백 엔드 서버 자격 증명 교환을 수행 하지 않고도 Microsoft id 플랫폼에서 토큰을 가져올 수 있도록 하는 [OAuth 2 암시적 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md)을 지원 합니다. 이렇게 하면 앱에서 사용자에 게 로그인 하 고, 세션을 유지 관리 하 고, 다른 web Api에 대 한 토큰을 가져올 수 있습니다.

![암시적 권한 부여 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

대부분의 최신 웹 응용 프로그램은 JavaScript를 사용 하 여 작성 된 단일 페이지 응용 프로그램, 그리고 단일 페이지 응용 프로그램으로 구성 되어 있습니다. 이러한 응용 프로그램은 웹 브라우저에서 실행 되며 기존 서버 쪽 웹 응용 프로그램과는 다른 인증 특징이 있습니다. Microsoft id 플랫폼을 사용 하면 단일 페이지 응용 프로그램에서 사용자에 게 로그인 하 고 암시적 부여 흐름을 사용 하 여 백 엔드 서비스 또는 web Api에 액세스 하는 토큰을 가져올 수 있습니다. 암시적 흐름을 사용 하면 응용 프로그램에서 ID 토큰을 가져와서 인증 된 사용자를 나타낼 수 있으며, 보호 된 Api를 호출 하는 데 필요한 토큰에도 액세스할 수 있습니다.

이 인증 흐름에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요 하기 때문에 전자 및 반응-네이티브와 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하는 응용 프로그램 시나리오가 포함 되지 않습니다.

## <a name="authorization-code"></a>인증 코드

MSAL은 [OAuth 2 인증 코드 부여](v2-oauth2-auth-code-flow.md)를 지원 합니다. 이 부여는 웹 Api와 같은 보호 된 리소스에 대 한 액세스 권한을 얻기 위해 장치에 설치 된 앱에서 사용할 수 있습니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. 

사용자가 웹 응용 프로그램 (웹 사이트)에 로그인 하면 웹 응용 프로그램은 인증 코드를 수신 합니다.  인증 코드는 웹 Api를 호출 하는 토큰을 획득 하기 위해 호출 됩니다. ASP.NET 및 ASP.NET Core web apps에서 `AcquireTokenByAuthorizationCode`의 유일한 목표는 토큰 캐시에 토큰을 추가 하는 것입니다. 그러면 응용 프로그램에서 토큰을 사용할 수 있습니다 (일반적으로 컨트롤러에서 `AcquireTokenSilent`를 사용 하 여 API에 대 한 토큰만 가져오기).

![권한 부여 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

위의 다이어그램에서 응용 프로그램은 다음과 같습니다.

1. 액세스 토큰에 대해 사용 되는 권한 부여 코드를 요청 합니다.
2. 액세스 토큰을 사용 하 여 web API를 호출 합니다.

### <a name="considerations"></a>고려 사항

- 인증 코드를 한 번만 사용 하 여 토큰을 교환할 수 있습니다. 동일한 인증 코드를 사용 하 여 토큰을 여러 번 얻으려고 시도 하지 않습니다 (프로토콜 표준 사양에서 명시적으로 금지 됨). 코드를 여러 번 의도적으로 수행 하는 경우 또는 프레임 워크 에서도 사용자에 게이를 수행 하는 것을 인식 하지 못하기 때문에 다음과 같은 오류가 발생 합니다. `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

- ASP.NET 또는 ASP.NET Core 응용 프로그램을 작성 하는 경우 이미 권한 부여 코드를 사용한 것으로 프레임 워크에 지시 하지 않으면이 문제가 발생할 수 있습니다. 이를 위해 `AuthorizationCodeReceived` 이벤트 처리기의 `context.HandleCodeRedemption()` 메서드를 호출 해야 합니다.

- 액세스 토큰을 ASP.NET와 공유 하지 않는 것이 좋습니다 .이로 인해 증분 동의가 올바르게 실행 되지 않을 수 있습니다. 자세한 내용은 [issue #693](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/693)를 참조 하세요.

## <a name="on-behalf-of"></a>대리

MSAL은 [OAuth 2 just-in-time 인증 흐름](v2-oauth2-on-behalf-of-flow.md)을 지원 합니다.  이 흐름은 응용 프로그램이 서비스 또는 웹 API를 호출 하는 경우에 사용 되며,이를 통해 다른 서비스 또는 web API를 호출 해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. 중간 계층 서비스에서 다운스트림 서비스로 인증 된 요청을 수행 하려면 사용자를 대신 하 여 Microsoft id 플랫폼에서 액세스 토큰을 보호 해야 합니다.

![흐름에 대 한 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램에서:

1. 응용 프로그램은 web API에 대 한 액세스 토큰을 획득 합니다.
2. 클라이언트 (웹, 데스크톱, 모바일 또는 단일 페이지 응용 프로그램)는 HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 추가 하 여 보호 된 웹 API를 호출 합니다. Web API는 사용자를 인증 합니다.
3. 클라이언트에서 web API를 호출 하면 웹 API는 사용자를 대신해 다른 토큰을 요청 합니다.  
4. 보호 된 웹 API는이 토큰을 사용 하 여 사용자 대신 다운스트림 웹 API를 호출 합니다.  Web API는 나중에 다른 다운스트림 Api에 대 한 토큰을 요청할 수도 있습니다 (동일한 사용자를 대신 하 여).

## <a name="client-credentials"></a>클라이언트 자격 증명

MSAL은 [OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md)을 지원 합니다. 이 흐름을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 응용 프로그램은 종종 디먼 또는 서비스 계정 이라고 합니다. 

클라이언트 자격 증명 부여 흐름은 사용자를 가장 하는 대신 다른 웹 서비스를 호출할 때 웹 서비스 (기밀 클라이언트)가 자체 자격 증명을 사용 하 여 인증 하도록 허용 합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 기밀 클라이언트 흐름은 공용 클라이언트 응용 프로그램만 지원 하기 때문에 모바일 플랫폼 (UWP, Xamarin.ios 및 Xamarin. Android)에서 사용할 수 없습니다. 공용 클라이언트 응용 프로그램은 Id 공급자에 대 한 응용 프로그램 id를 증명 하는 방법을 모릅니다. 웹 앱 또는 웹 API 백 엔드에서 인증서를 배포 하 여 보안 연결을 수행할 수 있습니다.

MSAL.NET는 두 가지 유형의 클라이언트 자격 증명을 지원 합니다. 이러한 클라이언트 자격 증명은 Azure AD에 등록 해야 합니다. 자격 증명은 코드에서 기밀 클라이언트 응용 프로그램의 생성자에 전달 됩니다.

### <a name="application-secrets"></a>애플리케이션 비밀

![암호를 사용 하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

위의 다이어그램에서 응용 프로그램은 다음과 같습니다.

1. 응용 프로그램 비밀 또는 암호 자격 증명을 사용 하 여 토큰을 가져옵니다.
2. 토큰을 사용 하 여 리소스에 대 한 요청을 만듭니다.

### <a name="certificates"></a>인증서

![인증서가 있는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

위의 다이어그램에서 응용 프로그램은 다음과 같습니다.

1. 인증서 자격 증명을 사용 하 여 토큰을 가져옵니다.
2. 토큰을 사용 하 여 리소스에 대 한 요청을 만듭니다.

이러한 클라이언트 자격 증명은 다음과 같아야 합니다.
- Azure AD에 등록 됩니다.
- 코드에서 기밀 클라이언트 응용 프로그램을 생성할 때 전달 됩니다.

## <a name="device-code"></a>장치 코드

MSAL은 사용자가 스마트 TV, IoT 장치 또는 프린터와 같은 입력 제한 장치에 로그인 할 수 있도록 하는 [OAuth 2 장치 코드 흐름](v2-oauth2-device-code.md)을 지원 합니다. Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요 합니다. 장치 코드 흐름을 사용 하면 사용자가 다른 장치 (예: 다른 컴퓨터 또는 휴대폰)를 사용 하 여 대화형으로 로그인 할 수 있습니다 .이 경우 장치 또는 운영 체제에서 웹 브라우저를 제공 하지 않습니다.

응용 프로그램은 장치 코드 흐름을 사용 하 여 이러한 장치 또는 운영 체제용으로 특별히 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 IoT 장치 또는 CLI (명령줄 도구)에서 실행 되는 응용 프로그램이 있습니다. 

![장치 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램에서:

1. 사용자 인증이 필요한 경우 앱은 코드를 제공 하 고, 다른 장치 (예: 인터넷에 연결 된 스마트폰)를 사용 하 여 URL로 이동 하도록 사용자에 게 요청 합니다 (예: https://microsoft.com/devicelogin)). 그런 다음 사용자에 게 코드를 입력 하 라는 메시지가 표시 되 고, 필요한 경우 승인 프롬프트 및 multi-factor authentication을 비롯 한 일반 인증 환경을 통해 진행 됩니다.

2. 인증에 성공 하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신 하 고이를 사용 하 여 필요한 웹 API 호출을 수행 합니다.

### <a name="constraints"></a>제약 조건

- 장치 코드 흐름은 공용 클라이언트 응용 프로그램 에서만 사용할 수 있습니다.
- 공용 클라이언트 응용 프로그램을 구성할 때 전달 된 기관은 다음 중 하나 여야 합니다.
  - 테넌트 (형식 `https://login.microsoftonline.com/{tenant}/` `{tenant}`. 여기서,은 테넌트 ID 또는 테넌트와 연결 된 도메인을 나타내는 GUID입니다.
  - 모든 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`)
- Microsoft 개인 계정은 Azure AD v2.0 엔드포인트에서 아직 지원 되지 않습니다 (`/common` 또는 `/consumers` 테넌트를 사용할 수 없음).

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

MSAL은 도메인에 가입 되어 있거나 Azure AD에 가입 된 Windows 컴퓨터에서 실행 되는 데스크톱 또는 모바일 응용 프로그램에 대해 IWA (Windows 통합 인증)를 지원 합니다. 이러한 응용 프로그램은 IWA를 사용 하 여 사용자의 UI 상호 작용 없이 토큰을 자동으로 가져올 수 있습니다. 

![Windows 통합 인증 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

위의 다이어그램에서 응용 프로그램은 다음과 같습니다.

1. Windows 통합 인증을 사용 하 여 토큰을 가져옵니다.
2. 토큰을 사용 하 여 리소스에 대 한 요청을 만듭니다.

### <a name="constraints"></a>제약 조건

IWA는 페더레이션 사용자만 지원 합니다. 즉, Active Directory에서 만들어지고 Azure AD에서 지원 됩니다. Azure AD에서 직접 만든 사용자는 (관리 되는 사용자) Active Directory를 지원 하지 않고이 인증 흐름을 사용할 수 없습니다. 이 제한은 [사용자 이름/암호 흐름](#usernamepassword)에 영향을 주지 않습니다.

IWA는 .NET Framework, .NET Core 및 유니버설 Windows 플랫폼 플랫폼용으로 작성 된 앱에 대 한 것입니다.

IWA multi-factor authentication을 바이패스 하지 않습니다. Multi-factor authentication이 구성 된 경우 multi-factor authentication 챌린지가 필요한 경우 IWA가 실패할 수 있습니다. Multi-factor authentication을 사용 하려면 사용자 조작이 필요 합니다.

Id 공급자가 2 단계 인증을 요청 하는 시간을 제어 하지 않습니다. 테넌트 관리자가 수행 합니다. 일반적으로 다른 국가에서 로그인 할 때, VPN을 통해 회사 네트워크에 연결 되어 있지 않은 경우, VPN을 통해 연결 된 경우에도 2 단계 인증이 필요 합니다. Azure AD는 AI를 사용 하 여 2 단계 인증이 필요한 경우 계속 학습 합니다. IWA이 실패 하면 [대화형 사용자 프롬프트] (#interactive)로 대체 해야 합니다.

공용 클라이언트 응용 프로그램을 구성할 때 전달 된 기관은 다음 중 하나 여야 합니다.
- 테넌트 (형식 `https://login.microsoftonline.com/{tenant}/` `tenant`. 여기서,은 테넌트 ID 또는 테넌트와 연결 된 도메인을 나타내는 guid입니다.
- 모든 회사 및 학교 계정 (`https://login.microsoftonline.com/organizations/`) Microsoft 개인 계정은 지원 되지 않습니다 (`/common` 또는 `/consumers` 테넌트를 사용할 수 없음).

IWA는 자동 흐름 이므로 다음 중 하나가 true 여야 합니다.
- 응용 프로그램의 사용자에 게는 응용 프로그램을 사용 하기 위해 이전에 동의한 있어야 합니다. 
- 테넌트 관리자는 응용 프로그램을 사용 하기 위해 테넌트의 모든 사용자에 게 이전에 동의한 해야 합니다.

이는 다음 중 하나에 해당 하는 것을 의미 합니다.
- 개발자가 Azure Portal에 대 한 **권한 부여** 를 선택 했습니다.
- 테넌트 관리자가 응용 프로그램 등록의 **API 권한** 탭에서 **{테넌트 도메인}에 대 한 Grant/revoke 관리자 동의** 를 선택 했습니다 ( [웹 api에 대 한 액세스 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)참조).
- 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다 ( [개별 사용자 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent)참조).
- 테넌트 관리자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다 ( [관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)참조).

IWA flow는 .NET 데스크톱, .NET Core 및 Windows 유니버설 플랫폼 앱에 대해 사용 하도록 설정 됩니다. .Net core는 운영 체제에서 사용자 이름을 가져올 수 없기 때문에 IWA에 대 한 사용자 이름을 제공 해야 합니다.
  
승인에 대 한 자세한 내용은 v2.0 [사용 권한 및 동의](v2-permissions-and-consent.md)를 참조 하세요.

## <a name="usernamepassword"></a>사용자 이름/암호

MSAL은 응용 프로그램이 암호를 직접 처리 하 여 사용자에 게 로그인 할 수 있도록 하는 [OAuth 2 리소스 소유자 암호 자격 증명 부여](v2-oauth-ropc.md)를 지원 합니다. 데스크톱 응용 프로그램에서 사용자 이름/암호 흐름을 사용 하 여 토큰을 자동으로 가져올 수 있습니다. 응용 프로그램을 사용 하는 경우 UI가 필요 하지 않습니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

위의 다이어그램에서 응용 프로그램은 다음과 같습니다.

1. 사용자 이름 및 암호를 id 공급자에 게 보내 토큰을 가져옵니다.
2. 토큰을 사용 하 여 web API를 호출 합니다.

> [!WARNING]
> 이 흐름을 권장 하지 않습니다. 높은 수준의 신뢰 및 사용자 노출이 필요 합니다.  다른, 더 안전한 흐름을 사용할 수 없는 경우에만이 흐름을 사용 해야 합니다. 자세한 내용은 [암호의 증가 하는 문제에 대 한 해결책은 무엇 인가요?를](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)참조 하세요. 

Windows 도메인 가입 컴퓨터에서 자동으로 토큰을 획득 하는 기본 흐름은 [Windows 통합 인증](#integrated-windows-authentication)입니다. 그렇지 않으면 [장치 코드 흐름](#device-code)을 사용할 수도 있습니다.

이는 일부 경우에 유용 하지만 (DevOps 시나리오) 사용자 고유의 UI를 제공 하는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우에는 피해 야 합니다. 사용자 이름/암호 사용:
- Multi-factor authentication을 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
- 사용자가 Single Sign-On을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

[Windows 통합 인증 제약 조건](#integrated-windows-authentication)외에도 다음과 같은 제약 조건이 적용 됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 multi-factor authentication과 호환 되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행 되는 경우이 흐름을 사용할 수 없습니다. 많은 조직에서이 작업을 수행 합니다.
- 회사 및 학교 계정 (Microsoft 계정 아님)에 대해서만 작동 합니다.
- 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 유니버설 Windows 플랫폼에서는 사용할 수 없습니다.

### <a name="azure-ad-b2c-specifics"></a>Azure AD B2C 세부 정보

MSAL.NET 및 Azure AD B2C를 사용 하는 방법에 대 한 자세한 내용은 [Azure AD B2C에서 ROPC 사용 (MSAL.NET)](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc-with-azure-ad-b2c)을 참조 하세요.
