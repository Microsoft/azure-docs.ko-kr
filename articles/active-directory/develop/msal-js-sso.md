---
title: Single sign-on (MSAL .js) | Microsoft
titleSuffix: Microsoft identity platform
description: JavaScript 용 Microsoft 인증 라이브러리 (MSAL)를 사용 하 여 Single Sign-On 환경을 빌드하는 방법에 대해 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695877"
---
# <a name="single-sign-on-with-msaljs"></a>MSAL.js를 사용한 Single Sign-On

SSO (Single Sign-on)를 사용 하면 사용자가 로그인 하기 위해 자격 증명을 한 번 입력 하 고 다시 인증 하지 않고도 여러 응용 프로그램에서 다시 사용할 수 있는 세션을 설정할 수 있습니다. 이를 통해 사용자에 게 원활한 환경을 제공 하 고 자격 증명에 대 한 반복적인 메시지를 줄일 수 있습니다.

Azure AD는 사용자가 처음으로 인증할 때 세션 쿠키를 설정 하 여 응용 프로그램에 SSO 기능을 제공 합니다. MSAL .js 라이브러리를 사용 하면 응용 프로그램이 몇 가지 방법으로이를 활용할 수 있습니다.

## <a name="sso-between-browser-tabs"></a>브라우저 탭 간 SSO

응용 프로그램이 여러 탭에서 열리고 먼저 한 탭에서 사용자가 로그인 하는 경우 사용자는 메시지를 표시 하지 않고 다른 탭 에서도 로그인 됩니다. MSAL는 브라우저 `localStorage` 사용자의 ID 토큰을 캐시 하 고 열려 있는 다른 탭의 응용 프로그램에 사용자를 로그인 합니다.

기본적으로 MSAL는 탭 간에 세션을 공유할 수 없는 `sessionStorage`를 사용 합니다. 탭 간에 SSO를 가져오려면 아래와 같이 MSAL의 `cacheLocation`를 `localStorage`으로 설정 해야 합니다.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>앱 간 SSO

사용자가 인증 하면 세션 쿠키가 브라우저의 Azure AD 도메인에 설정 됩니다. MSAL는이 세션 쿠키를 사용 하 여 서로 다른 응용 프로그램 간에 사용자를 위한 SSO를 제공 합니다. 또한 MSAL는 응용 프로그램 도메인당 브라우저 저장소에서 사용자의 ID 토큰 및 액세스 토큰을 캐시 합니다. 따라서 SSO 동작은 사례 마다 다릅니다.  

### <a name="applications-on-the-same-domain"></a>동일한 도메인의 응용 프로그램

응용 프로그램이 동일한 도메인에서 호스트 되는 경우 사용자는 앱에 한 번 로그인 한 다음 프롬프트 없이 다른 앱에 인증을 받을 수 있습니다. MSAL는 도메인의 사용자에 대해 캐시 된 토큰을 활용 하 여 SSO를 제공 합니다.

### <a name="applications-on-different-domain"></a>다른 도메인의 응용 프로그램

응용 프로그램이 서로 다른 도메인에서 호스트 되는 경우 도메인 B의 MSAL에서 도메인 A에 캐시 된 토큰에 액세스할 수 없습니다.

즉, 도메인 A에 로그인 한 사용자가 도메인 B의 응용 프로그램으로 이동 하면 해당 사용자는 Azure AD 페이지를 사용 하 여 리디렉션되고 메시지가 표시 됩니다. Azure AD에는 사용자 세션 쿠키가 있지만 사용자에 게 로그인 되므로 자격 증명을 다시 입력 하지 않아도 됩니다. 사용자에 게 Azure AD를 사용 하 여 세션에 여러 사용자 계정이 있는 경우 사용자에 게 로그인 할 관련 계정을 선택 하 라는 메시지가 표시 됩니다.

### <a name="automatically-select-account-on-azure-ad"></a>Azure AD에서 자동으로 계정 선택

경우에 따라 응용 프로그램은 사용자의 인증 컨텍스트에 액세스할 수 있으며 여러 계정에 로그인 할 때 Azure AD 계정 선택 프롬프트를 방지 하려고 합니다.  이 작업은 몇 가지 다른 방법으로 수행할 수 있습니다.

**세션 ID (SID) 사용**

세션 ID는 ID 토큰에서 구성할 수 있는 [선택적 클레임](active-directory-optional-claims.md) 입니다. 이 클레임을 통해 응용 프로그램은 사용자의 계정 이름이 나 사용자 이름과 상관 없이 사용자의 Azure AD 세션을 식별할 수 있습니다. 요청 매개 변수의 SID를 `acquireTokenSilent` 호출에 전달할 수 있습니다. 이렇게 하면 Azure AD에서 계정 선택을 우회할 수 있습니다. SID는 세션 쿠키에 바인딩되고 브라우저 컨텍스트를 교차 하지 않습니다.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Sial .js에서 `acquireTokenSilent` 호출로 만든 자동 인증 요청에만 SID를 사용할 수 있습니다.
응용 프로그램 매니페스트에서 선택적 클레임을 구성 하는 단계는 [여기](active-directory-optional-claims.md)에서 찾을 수 있습니다.

**Login 힌트 사용**

구성 된 SID 클레임이 없거나, 대화형 인증 호출에서 계정 선택 프롬프트를 무시 해야 하는 경우 요청 매개 변수에 `login_hint`를 제공 하 고, MSAL의 대화형 방법 (`loginPopup`, `loginRedirect`, `acquireTokenPopup` 및 `acquireTokenRedirect`)에서 필요에 따라 `domain_hint`을 `extraQueryParameters` 하 여이 작업을 수행할 수 있습니다. 예:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

사용자에 대 한 ID 토큰에서 반환 된 클레임을 읽어 login_hint 및 domain_hint에 대 한 값을 가져올 수 있습니다.

* **loginHint** 는 ID 토큰의 `preferred_username` 클레임으로 설정 해야 합니다.

* **domain_hint** 는/ss 인증 기관을 사용 하는 경우에만 전달 해야 합니다. 도메인 힌트는 tid (테넌트 ID)에 의해 결정 됩니다.  ID 토큰의 `tid` 클레임이 `9188040d-6c67-4c5b-b112-36a304b66dad` 인 경우 소비자입니다. 그렇지 않으면 조직입니다.

로그인 힌트 및 도메인 힌트에 대 한 값에 대 한 자세한 내용은 [여기](v2-oauth2-implicit-grant-flow.md) 를 참조 하세요.

> [!Note]
> SID와 login_hint를 동시에 전달할 수 없습니다. 이로 인해 오류 응답이 발생 합니다.

## <a name="sso-without-msaljs-login"></a>MSAL .js 로그인을 사용 하지 않는 SSO

기본적으로 MSAL는 Api에 대 한 토큰을 가져오기 전에 사용자 컨텍스트를 설정 하기 위해 login 메서드를 호출 해야 합니다. 로그인 메서드는 대화형 이므로 사용자에 게 프롬프트가 표시 됩니다.

응용 프로그램이 다른 응용 프로그램에서 시작 된 인증을 통해 인증 된 사용자의 컨텍스트 또는 ID 토큰에 액세스할 수 있으며, 먼저 MSAL를 통해 로그인 하지 않고도 SSO를 활용 하 여 토큰을 획득 하려는 경우가 있습니다.

이에 대 한 예는 사용자가 추가 기능 또는 플러그 인으로 실행 되는 다른 JavaScript 응용 프로그램을 호스트 하는 부모 웹 응용 프로그램에 로그인 하는 경우입니다.

이 시나리오의 SSO 환경은 다음과 같이 달성할 수 있습니다.

사용 가능한 경우 `sid`를 전달 하 고, 다음과 같이 MSAL .js `acquireTokenSilent`에 대 한 요청 매개 변수로 `login_hint` 하 고 선택적으로 `domain_hint`합니다.

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>ADAL의 SSO에서 MSAL .js 업데이트

MSAL는 Azure AD 인증 시나리오에 대해 ADAL을 사용 하 여 기능 패리티를 제공 합니다. ADAL에서 MSAL로 쉽게 마이그레이션할 수 있도록 하 고 사용자에 게 다시 로그인 하 라는 메시지를 표시 하지 않도록 라이브러리는 ADAL의 사용자 세션을 나타내는 ID 토큰을 읽고 MSAL의 사용자에 게 원활 하 게 로그인 합니다.  

ADAL에서 업데이트할 때 SSO (Single Sign-On) 동작을 활용 하려면 라이브러리에서 토큰을 캐시 하는 데 `localStorage`를 사용 하 고 있는지 확인 해야 합니다. 다음과 같이 초기화할 때 MSAL와 ADAL 구성에서 `cacheLocation`을 `localStorage`로 설정 합니다.


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

이 구성을 구성한 후에는 MSAL에서 ADAL의 인증 된 사용자에 대 한 캐시 된 상태를 읽고이를 사용 하 여 MSAL에 SSO를 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD의 [Single Sign-On 세션 및 토큰 수명](active-directory-configurable-token-lifetimes.md) 값에 대해 자세히 알아보세요.
