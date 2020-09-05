---
title: JavaScript 단일 페이지 앱에서 사용자 로그인 | Azure
titleSuffix: Microsoft identity platform
description: JavaScript 앱이 Microsoft ID 플랫폼을 사용하여 액세스 토큰이 필요한 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.openlocfilehash: 1e537c6f61a7c461b2a9edb4097fba95f5c66a6f
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120527"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>빠른 시작: JavaScript SPA에서 사용자 로그인 및 액세스 토큰 가져오기

이 빠른 시작에서는 코드 샘플을 사용하여 JavaScript SPA(단일 페이지 애플리케이션)에서 개인 계정, 회사 및 학교 계정의 사용자에 로그인하는 방법을 알아봅니다. 또한 JavaScript SPA는 Microsoft Graph API 또는 웹 API를 호출하는 액세스 토큰을 가져올 수 있습니다. (자세한 내용은 [샘플 작동 방식 ](#how-the-sample-works)을 참조하세요.)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험용 Azure 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.JS](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download)(프로젝트 파일 편집)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>빠른 시작 애플리케이션 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하려면 다음 옵션 중 하나를 사용합니다.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1(Express): 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD(Azure Active Directory) 테넌트로 포털 세션을 설정합니다.
> 1. 새 [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) 창으로 이동합니다.
> 1. 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
> 1. **등록**을 선택합니다.
> 1. 지침에 따라 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2(수동): 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
>
> 1. [Azure Portal](https://portal.azure.com)에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
>
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
> 1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
> 1. **등록**을 선택합니다. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
> 1. 이 빠른 시작에서는 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
> 1. **플랫폼 구성**에서 **플랫폼 추가**를 선택합니다. 왼쪽에 패널이 열립니다. 여기에서 **웹 애플리케이션** 영역을 선택합니다.
> 1. 왼쪽에서 **리디렉션 URI** 값을 `http://localhost:3000/`으로 설정합니다. 그런 다음, **액세스 토큰** 및 **ID 토큰**을 선택합니다.
> 1. **구성**을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하도록 하려면 `redirectUri`를 `http://localhost:3000/`으로 추가하고 **암시적 허용**을 사용하도록 설정해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-javascript/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-project"></a>2단계: 프로젝트를 다운로드합니다.

> [!div renderon="docs"]
> Node.js를 사용하여 웹 서버로 프로젝트를 실행하려면 [주요 프로젝트 파일을 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)합니다.

> [!div renderon="portal"]
> Node.js를 사용하여 웹 서버에서 프로젝트 실행

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3단계: JavaScript 앱 구성
>
> *JavaScriptSPA* 폴더에서 *authConfig.js*를 편집하고, `msalConfig`의 `clientID`, `authority` 및 `redirectUri` 값을 설정합니다.
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> 위치:
> - *\<Enter_the_Application_Id_Here>* 는 등록한 애플리케이션의 **애플리케이션(클라이언트) ID**입니다.
> - *\<Enter_the_Cloud_Instance_Id_Here>* 는 Azure 클라우드의 인스턴스입니다. 주 또는 글로벌 Azure 클라우드의 경우 *https://login.microsoftonline.com* 을 입력하면 됩니다. **국가별** 클라우드(예제: 중국)의 경우 [국가별 클라우드](./authentication-national-cloud.md)를 참조하세요.
> - *\<Enter_the_Tenant_info_here>* 는 다음 옵션 중 하나로 설정됩니다.
>    - 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: *contoso.microsoft.com*)으로 바꿉니다.
>    - 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**으로 바꿉니다.
>    - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다.
>
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **지원되는 계정 유형**의 값을 찾아보려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3단계: 앱이 구성되었고 실행할 준비가 되었습니다.
> 앱 속성 값을 사용하여 프로젝트를 구성했습니다.

> [!div renderon="docs"]
>
> 그런 다음, 동일한 폴더에 있는 *graphConfig.js* 파일을 편집하여 `apiConfig` 개체에 대한 `graphMeEndpoint` 및 `graphMeEndpoint`를 설정합니다.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> 위치:
> - *\<Enter_the_Graph_Endpoint_Here>* 는 API 호출이 수행될 엔드포인트입니다. 주 또는 글로벌 Microsoft Graph API 서비스의 경우 `https://graph.microsoft.com`을 입력하기만 하면 됩니다. 자세한 내용은 [국가별 클라우드 배포](/graph/deployments)를 참조하세요.
>
> #### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

[Node.js](https://nodejs.org/en/download/)를 사용하여 웹 서버에서 프로젝트 실행:

1. 서버를 시작하려면 프로젝트의 디렉터리에서 다음 명령을 실행합니다.
    ```batch
    npm install
    npm start
    ```
1. 웹 브라우저를 열고 `http://localhost:3000/`로 이동합니다.

1. **로그인**을 선택하여 로그인을 시작한 다음, Microsoft Graph API를 호출합니다.

브라우저가 애플리케이션을 로드한 후에는 **로그인**을 선택합니다. 사용자가 처음으로 로그인하면 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인한 후에는 사용자 프로필 정보가 페이지에 표시되어야 합니다.

## <a name="more-information"></a>자세한 정보

### <a name="how-the-sample-works"></a>샘플 작동 방법

![샘플 JavaScript SPA 작동 방법: 1. SPA가 로그인을 시작합니다. 2. SPA는 Microsoft ID 플랫폼에서 ID 토큰을 획득합니다. 3. SPA는 획득 토큰을 호출합니다. 4. Microsoft ID 플랫폼은 SPA에 액세스 토큰을 반환합니다. 5. SPA는 Microsoft Graph API에 대한 액세스 토큰을 사용하여 HTTP GET 요청을 수행합니다. 6. Graph API는 SPA에 HTTP 응답을 반환합니다.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

MSAL 라이브러리는 사용자를 로그인하고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청합니다. 빠른 시작의 *index.html* 파일에는 라이브러리에 대한 참조가 포함됩니다.

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> 이전 버전을 [MSAL.js 릴리스](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)에서 최근에 릴리스된 버전으로 바꿀 수 있습니다.

또는 Node.js가 설치된 경우 다음과 같이 Node.js 패키지 관리자(npm)를 통해 최신 버전을 다운로드할 수 있습니다.

```batch
npm install msal
```

### <a name="msal-initialization"></a>MSAL 초기화

빠른 시작 코드에서는 MSAL 라이브러리를 초기화하는 방법도 보여줍니다.

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Where  | Description |
> |---------|---------|
> |`clientId`     | Azure Portal에 등록된 애플리케이션의 애플리케이션 ID.|
> |`authority`    | (선택 사항) 위의 구성 섹션에 설명된 대로 계정 유형을 지원하는 기관 URL입니다. 기본 기관은 `https://login.microsoftonline.com/common`입니다. |
> |`redirectUri`     | 애플리케이션 등록의 구성된 회신/redirectUri입니다. 이 예제의 경우 `http://localhost:3000/`입니다. |
> |`cacheLocation`  | (선택 사항) 인증 상태에 사용되는 브라우저 스토리지를 설정합니다. 기본값은 sessionStorage입니다.   |
> |`storeAuthStateInCookie`  | (선택 사항) 브라우저 쿠키에서 인증 흐름의 유효성을 검사하는 데 필요한 인증 요청 상태를 저장하는 라이브러리입니다. 이 쿠키는 특정한 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)를 완화하기 위해 IE 및 Edge 브라우저에 대해 설정됩니다. |

사용할 수 있는 구성 가능한 옵션에 대한 자세한 내용은 [클라이언트 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

### <a name="sign-in-users"></a>사용자 로그인

다음 코드 조각에서는 사용자에게 로그인하는 방법을 보여줍니다.

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Where  | Description |
> |---------|---------|
> | `scopes`   | (선택 사항) 로그인 시 사용자 동의를 요청하는 범위가 포함됩니다. 예를 들어 Microsoft Graph의 경우 `[ "user.read" ]` 또는 사용자 지정 웹 API의 경우 `[ "<Application ID URL>/scope" ]`(즉, `api://<Application ID>/access_as_user`)입니다. |

> [!TIP]
> 또는 `loginRedirect` 메서드를 사용하여 현재 페이지를 팝업 창 대신 로그인 페이지로 리디렉션할 수 있습니다.

### <a name="request-tokens"></a>토큰 요청

MSAL은 토큰을 획득하는 데 `acquireTokenRedirect`, `acquireTokenPopup` 및 `acquireTokenSilent`의 세 가지 메서드를 사용합니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginRedirect` 또는 `loginPopup` 메서드가 처음으로 실행된 후에 후속 호출 시 보호되는 리소스에 액세스하는 데 사용되는 토큰을 가져오는 데 일반적으로 사용되는 메서드가 `acquireTokenSilent`입니다. 요청 또는 갱신 토큰에 대한 호출은 자동으로 수행됩니다.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Where  | Description |
> |---------|---------|
> | `scopes`   | API에 대한 액세스 토큰에서 반환되도록 요청되는 범위가 포함됩니다. 예를 들어 Microsoft Graph의 경우 `[ "mail.read" ]` 또는 사용자 지정 웹 API의 경우 `[ "<Application ID URL>/scope" ]`(즉, `api://<Application ID>/access_as_user`)입니다.|

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

상황에 따라 사용자가 Microsoft ID 플랫폼 엔드포인트와 상호 작용해야 하는 경우도 있습니다. 예를 들면 다음과 같습니다.
* 암호가 만료되었으므로 사용자가 자격 증명을 다시 입력해야 할 수도 있습니다.
* 애플리케이션이 사용자 동의가 필요한 추가 리소스 범위에 대한 액세스를 요청하고 있습니다.
* 2단계 인증이 필요합니다.

대부분의 애플리케이션에 대해 권장되는 일반적인 패턴은 먼저 `acquireTokenSilent`를 호출하고, 예외를 catch한 다음, `acquireTokenPopup`(또는 `acquireTokenRedirect`)을 호출하여 대화형 요청을 시작하는 것입니다.

`acquireTokenPopup`을 호출하면 로그인을 위한 팝업 창이 표시됩니다. (또는 `acquireTokenRedirect`는 Microsoft ID 플랫폼 엔드포인트로 사용자를 리디렉션합니다.) 해당 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 대한 동의를 제공하거나, 2단계 인증을 완료하여 상호 작용해야 합니다.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> 이 빠른 시작에서는 Internet Explorer의 팝업 창 처리와 관련하여 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) 때문에 Microsoft Internet Explorer로 `loginRedirect` 및 `acquireTokenRedirect` 메서드를 사용합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에 대한 애플리케이션 빌드에 대한 더 자세한 단계별 가이드는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [MS Graph에 로그인하고 호출하는 자습서](./tutorial-v2-javascript-spa.md)

설명서, FAQ, 문제 등에 대한 MSAL 리포지토리를 찾아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [MSAL.js GitHub 리포지토리](https://github.com/AzureAD/microsoft-authentication-library-for-js)