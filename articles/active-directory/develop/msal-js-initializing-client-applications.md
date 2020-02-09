---
title: MSAL .js 클라이언트 앱 초기화 | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft 인증 라이브러리 (MSAL .js)를 사용 하 여 클라이언트 응용 프로그램을 초기화 하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a0a2c5fc971c3f1f3283d95c5617bdf1e88a6a58
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77084036"
---
# <a name="initialize-client-applications-using-msaljs"></a>MSAL를 사용 하 여 클라이언트 응용 프로그램 초기화
이 문서에서는 사용자 에이전트 응용 프로그램의 인스턴스에서 JavaScript 용 Microsoft 인증 라이브러리 (MSAL)를 초기화 하는 방법을 설명 합니다. 사용자 에이전트 응용 프로그램은 클라이언트 코드가 웹 브라우저와 같은 사용자 에이전트에서 실행 되는 공용 클라이언트 응용 프로그램의 형태입니다. 브라우저 컨텍스트는 공개적으로 액세스할 수 있기 때문에 이러한 클라이언트는 비밀을 저장 하지 않습니다. 클라이언트 응용 프로그램 유형 및 응용 프로그램 구성 옵션에 대 한 자세한 내용은 [개요](msal-client-applications.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항
응용 프로그램을 초기화 하려면 먼저 앱이 Microsoft id 플랫폼과 통합 될 수 있도록 [Azure Portal에 등록](scenario-spa-app-registration.md) 해야 합니다. 등록 후에는 다음 정보가 필요할 수 있습니다 (Azure Portal 참조).

- 클라이언트 ID (응용 프로그램의 GUID를 나타내는 문자열)
- 응용 프로그램에 대 한 id 공급자 URL (인스턴스 이름) 및 로그인 대상이 됩니다. 이러한 두 매개 변수를 통칭 하 여 기관 이라고 합니다.
- 조직 전용 lob (기간 업무) 응용 프로그램을 작성 하는 경우 (단일 테넌트 응용 프로그램이 라고도 하는) 테넌트 ID입니다.
- 웹 앱의 경우 id 공급자가 보안 토큰을 사용 하 여 응용 프로그램에 반환 하는 redirectUri도 설정 해야 합니다.

## <a name="initializing-applications"></a>응용 프로그램 초기화

일반 JavaScript/Typescript 응용 프로그램에서 다음과 같이 MSAL를 사용할 수 있습니다. 구성 개체를 사용 하 여 `UserAgentApplication`를 인스턴스화하여 MSAL 인증 컨텍스트를 초기화 합니다. MSAL를 초기화 하는 데 필요한 최소 구성은 응용 프로그램 등록 포털에서 가져와야 하는 응용 프로그램의 clientID입니다.

리디렉션 흐름 (`loginRedirect` 및 `acquireTokenRedirect`)을 사용 하는 인증 방법의 경우에는 `handleRedirectCallback()` 메서드를 통해 성공 또는 오류에 대 한 콜백을 명시적으로 등록 해야 합니다. 이는 팝업 환경을 사용 하는 메서드를 사용 하 여 리디렉션 흐름이 약속을 반환 하지 않기 때문에 필요 합니다.

```javascript
// Configuration object constructed
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    }
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

// (optional when using redirect methods) register redirect call back for Success or Error
myMSALObj.handleRedirectCallback(authCallback);
```

MSAL는 단일 인스턴스 및 단일 인증 컨텍스트를 나타내는 `UserAgentApplication` 구성을 갖도록 설계 되었습니다. 여러 인스턴스는 브라우저에서 충돌 하는 캐시 항목 및 동작을 야기 하므로 권장 되지 않습니다.

## <a name="configuration-options"></a>구성 옵션

MSAL에는 `UserAgentApplication`인스턴스를 만드는 데 사용할 수 있는 구성 가능한 옵션의 그룹화를 제공 하는 아래에 표시 된 구성 개체가 있습니다.

```javascript
type storage = "localStorage" | "sessionStorage";

// Protocol Support
export type AuthOptions = {
    clientId: string;
    authority?: string;
    validateAuthority?: boolean;
    redirectUri?: string | (() => string);
    postLogoutRedirectUri?: string | (() => string);
    navigateToLoginRequestUrl?: boolean;
};

// Cache Support
export type CacheOptions = {
    cacheLocation?: CacheLocation;
    storeAuthStateInCookie?: boolean;
};

// Library support
export type SystemOptions = {
    logger?: Logger;
    loadFrameTimeout?: number;
    tokenRenewalOffsetSeconds?: number;
    navigateFrameWait?: number;
};

// Developer App Environment Support
export type FrameworkOptions = {
    isAngular?: boolean;
    unprotectedResources?: Array<string>;
    protectedResourceMap?: Map<string, Array<string>>;
};

// Configuration Object
export type Configuration = {
    auth: AuthOptions,
    cache?: CacheOptions,
    system?: SystemOptions,
    framework?: FrameworkOptions
};
```

다음은 구성 개체에서 현재 지원 되는 구성 가능한 옵션의 전체 집합입니다.

- **clientID**: 필수 항목입니다. 응용 프로그램의 clientID는 응용 프로그램 등록 포털에서 가져와야 합니다.

- **authority**: 선택 사항입니다. MSAL에서 토큰을 요청할 수 있는 디렉터리를 나타내는 URL입니다. 기본값은 `https://login.microsoftonline.com/common`입니다.
    * Azure AD에서 https://&lt;인스턴스&gt;/&lt;대상&gt;&lt;인스턴스&gt;는 id 공급자 도메인 (예: `https://login.microsoftonline.com`)이 고 &lt;대상 그룹은 로그인 대상을 나타내는 식별자입니다.&gt; 다음 값을 사용할 수 있습니다.
        * `https://login.microsoftonline.com/<tenant>`은 테넌트에 연결 된 도메인 (예: contoso.onmicrosoft.com) 이거나 특정 조직의 사용자 로그인에만 사용 되는 디렉터리의 `TenantID` 속성을 나타내는 GUID입니다.
        * `https://login.microsoftonline.com/common`-회사 및 학교 계정이 나 Microsoft 개인 계정으로 사용자를 로그인 하는 데 사용 됩니다.
        * `https://login.microsoftonline.com/organizations/`-회사 및 학교 계정으로 사용자를 로그인 하는 데 사용 됩니다.
        * `https://login.microsoftonline.com/consumers/`-개인 Microsoft 계정 (live)만 사용 하 여 사용자를 로그인 하는 데 사용 됩니다.
    * Azure AD B2C에서는 `https://<instance>/tfp/<tenant>/<policyName>/`형식입니다. 여기서 인스턴스는 Azure AD B2C 도메인입니다. 예를 들어, {policyName}. b2clogin는 테넌트가 Azure AD B2C 테넌트의 이름입니다 (예: {}. onmicrosoft,는 적용할 정책의 이름입니다.


- **Validateauthority**: 선택 사항입니다.  토큰 발급자의 유효성을 검사 합니다. 기본값은 `true`입니다. B2C 응용 프로그램의 경우 기관 값이 알려져 있고 정책 마다 다를 수 있으므로 기관 유효성 검사가 작동 하지 않으며 `false`로 설정 되어야 합니다.

- **Redirecturi**: 선택 사항입니다.  앱이 인증 응답을 보내고 받을 수 있는 앱의 리디렉션 URI입니다. 포털에 등록 한 리디렉션 Uri 중 하 나와 정확히 일치 해야 합니다. 기본값은 `window.location.href`입니다.

- **Postlogoutredirecturi**: 선택 사항입니다.  사용자를 로그 아웃 한 후 `postLogoutRedirectUri`으로 리디렉션합니다. 기본값은 `redirectUri`입니다.

- **navigateToLoginRequestUrl**: 선택 사항입니다. 로그인 후 시작 페이지로의 기본 탐색을 해제할 수 있습니다. 기본값은 true입니다. 이는 리디렉션 흐름에만 사용 됩니다.

- **Cachelocation**: 선택 사항입니다.  브라우저 저장소를 `localStorage` 또는 `sessionStorage`로 설정 합니다. 기본값은 `sessionStorage`입니다.

- 창 **: 선택 사항입니다.**  이 플래그는 Microsoft Internet Explorer 및 Microsoft Edge의 [인증 루프 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#1-issues-due-to-security-zones) 에 대 한 수정으로 msal .js v 0.2.2에서 도입 되었습니다. 이 수정 사항을 활용 하려면 플래그 `storeAuthStateInCookie` true로 설정 합니다. 이 기능을 사용 하는 경우 MSAL는 브라우저 쿠키의 인증 흐름에 대 한 유효성 검사에 필요한 인증 요청 상태를 저장 합니다. 기본적으로이 플래그는 `false`로 설정 됩니다.

- **로 거**: 선택 사항입니다.  사용자 지정 방식으로 로그를 사용 하 고 게시 하기 위해 개발자가 제공할 수 있는 콜백 인스턴스가 있는로 거 개체입니다. 로 거 개체를 전달 하는 방법에 대 한 자세한 내용은 [msal를 사용 하 여 로깅](msal-logging.md)을 참조 하세요.

- **Load프레임 시간 제한**: 선택 사항입니다.  Azure AD에서 토큰 갱신 응답을 시간 초과로 간주 하기 전까지 비활성 시간 (밀리초)입니다. 기본값은 6 초입니다.

- **tokenRenewalOffsetSeconds**: 선택 사항입니다. 만료 전에 토큰을 갱신 하는 데 필요한 오프셋 창을 설정 하는 시간 (밀리초)입니다. 기본값은 300 밀리초입니다.

- **navigateFrameWait**: 선택 사항입니다. 숨겨진 iframe에서 대상으로 이동 하기 전 까지의 대기 시간을 설정 하는 시간 (밀리초)입니다. 기본값은 500 밀리초입니다.

이러한 작업은 MSAL 각도 래퍼 라이브러리에서 전달 되는 경우에만 적용 됩니다.
- **unprotectedResources**: 선택 사항입니다.  보호 되지 않는 리소스인 Uri의 배열입니다. MSAL은 이러한 URI가 있는 보내는 요청에 토큰을 연결 하지 않습니다. 기본값은 `null`입니다.

- **protectedResourceMap**: 선택 사항입니다.  이는 web API 호출에서 액세스 토큰을 자동으로 연결 하기 위해 MSAL에서 사용 하는 범위에 대 한 리소스 매핑입니다. 리소스에 대해 단일 액세스 토큰을 가져옵니다. 따라서 다음과 같이 특정 리소스 경로를 매핑할 수 있습니다. {"https://graph.microsoft.com/v1.0/me", ["user. read"]} 또는 리소스의 앱 URL ({"https://graph.microsoft.com/", ["user. read", "mail. send"]}). CORS 호출에 필요 합니다. 기본값은 `null`입니다.
