---
title: JavaScript 단일 페이지 앱 자습서 - Microsoft ID 플랫폼 | Azure
description: Azure Active Directory v2.0 엔드포인트를 통해 액세스 토큰을 요구하는 API를 JavaScript SPA 애플리케이션에서 호출하는 방법입니다.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 5657a2d2c348b371f81aed74c92e52b5199cdc61
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159883"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript SPA(단일 페이지 애플리케이션)에서 사용자 로그인 및 Microsoft Graph API 호출

이 가이드에서는 JavaScript SPA(단일 페이지 애플리케이션)에서 다음을 수행하는 방법을 보여 줍니다.
- 회사 및 학교 계정과 개인 계정으로 로그인 
- 액세스 토큰 획득
- *Microsoft ID 플랫폼 엔드포인트*에서 액세스 토큰을 요구하는 Microsoft Graph API 또는 다른 API 호출

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>자세한 정보

이 가이드에서 만든 샘플 애플리케이션을 사용하면 JavaScript SPA에서 Microsoft ID 플랫폼 엔드포인트의 토큰을 수락하는 Microsoft Graph API 또는 웹 API를 쿼리할 수 있습니다. 이 시나리오에서는 사용자가 로그인하면 권한 부여 헤더를 통해 액세스 토큰이 요청되고 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL(Microsoft 인증 라이브러리)에서 처리합니다.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript용 Microsoft 인증 라이브러리 미리 보기|

> [!NOTE]
> *msal.js*는 Microsoft ID 플랫폼 엔드포인트를 대상으로 하며, 이를 통해 개인, 학교 및 회사 계정으로 로그인하여 토큰을 획득할 수 있습니다. Microsoft ID 플랫폼 엔드포인트에는 [몇 가지 제한 사항](../azuread-dev/azure-ad-endpoint-comparison.md#limitations)이 있습니다.
> v1.0 및 v2.0 엔드포인트 간의 차이점을 이해하려면 [엔드포인트 비교 가이드](../azuread-dev/azure-ad-endpoint-comparison.md)를 참조하세요.

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>웹 서버 또는 프로젝트 설정

> 이 샘플의 프로젝트를 다운로드하고 싶으세요? 다음 중 하나를 수행합니다.
> 
> - Node.js와 같은 로컬 웹 서버를 사용하여 프로젝트를 실행하려면 [프로젝트 파일을 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)합니다.
>
> - (선택 사항) Microsoft IIS(인터넷 정보 서비스) 서버를 사용하여 프로젝트를 실행하려면 [Visual Studio 프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)합니다.
>
> 코드 샘플을 먼저 구성한 후에 실행하려면 [구성 단계](#register-your-application)로 건너뜁니다.

## <a name="prerequisites"></a>사전 요구 사항

* 이 자습서를 실행하려면 [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) 또는 IIS Express와 같은 로컬 웹 서버가 [Visual Studio 2017](https://www.visualstudio.com/downloads/)과 연결되어 있어야 합니다.

* 프로젝트를 실행하는 데 Node.js를 사용하는 경우 [Visual Studio Code](https://code.visualstudio.com/download)와 같은 IDE(통합 개발 환경)를 설치하여 프로젝트 파일을 편집합니다.

* 이 가이드의 지침은 Node.js 및 Visual Studio 2017 둘 다를 기준으로 하지만 다른 개발 환경 또는 웹 서버를 사용할 수 있습니다.

## <a name="create-your-project"></a>프로젝트 만들기

> ### <a name="option-1-nodejs-or-other-web-servers"></a>옵션 1: Node.js 또는 다른 웹 서버
> [Node.js](https://nodejs.org/en/download/)가 설치되어 있는지 확인한 다음, 애플리케이션을 호스팅할 폴더를 만듭니다.
>
> ### <a name="option-2-visual-studio"></a>옵션 2: Visual Studio
> Visual Studio를 사용하고 새 프로젝트를 만드는 경우 다음 단계를 수행합니다.
> 1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
> 1. **Visual C#\Web**에서 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다.
> 1. 애플리케이션 이름을 입력한 다음, **확인**을 선택합니다.
> 1. **새 ASP.NET 웹 애플리케이션**에서 **비어 있음**을 선택합니다.

## <a name="create-the-spa-ui"></a>SPA UI 만들기
1. JavaScript SPA에 대한 *index.html* 파일을 만듭니다. Visual Studio를 사용하는 경우 프로젝트(프로젝트 루트 폴더)를 선택합니다. 마우스 오른쪽 단추를 클릭하고, **추가** > **새 항목** > **HTML 페이지**를 차례로 선택하고, 파일 이름을 *index.html*로 지정합니다.

1. *index.html* 파일에서 다음 코드를 추가합니다.

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 위 스크립트의 MSAL.js 버전을 최근에 출시된 [MSAL.js 릴리스](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 버전으로 바꿀 수 있습니다.

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>사용자 로그인에 MSAL(Microsoft 인증 라이브러리) 사용

`index.html` 파일의 `<script></script>` 태그 내에 다음 코드를 추가합니다.

   ```JavaScript
   var msalConfig = {
       auth: {
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
       },
       cache: {
           cacheLocation: "localStorage",
           storeAuthStateInCookie: true
       }
   };

   var graphConfig = {
       graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
   };

   // this can be used for login or token request, however in more complex situations
   // this can have diverging options
   var requestObj = {
        scopes: ["user.read"]
   };

   var myMSALObj = new Msal.UserAgentApplication(msalConfig);
   // Register Callbacks for redirect flow
   myMSALObj.handleRedirectCallback(authRedirectCallBack);


   function signIn() {

       myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
           //Login Success
           showWelcomeMessage();
           acquireTokenPopupAndCallMSGraph();
       }).catch(function (error) {
           console.log(error);
       });
   }

   function acquireTokenPopupAndCallMSGraph() {
       //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
       myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
       }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
       });
   }


   function graphAPICallback(data) {
       document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
   }


   function showWelcomeMessage() {
       var divWelcome = document.getElementById('WelcomeMessage');
       divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
       var loginbutton = document.getElementById('SignIn');
       loginbutton.innerHTML = 'Sign Out';
       loginbutton.setAttribute('onclick', 'signOut();');
   }


   //This function can be removed if you do not need to support IE
   function acquireTokenRedirectAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenRedirect
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenRedirect(requestObj);
            }
        });
   }


   function authRedirectCallBack(error, response) {
       if (error) {
           console.log(error);
       }
       else {
           if (response.tokenType === "access_token") {
               callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
           } else {
               console.log("token type is:" + response.tokenType);
           }
       }
   }

   function requiresInteraction(errorCode) {
       if (!errorCode || !errorCode.length) {
           return false;
       }
       return errorCode === "consent_required" ||
           errorCode === "interaction_required" ||
           errorCode === "login_required";
   }

   // Browser check variables
   var ua = window.navigator.userAgent;
   var msie = ua.indexOf('MSIE ');
   var msie11 = ua.indexOf('Trident/');
   var msedge = ua.indexOf('Edge/');
   var isIE = msie > 0 || msie11 > 0;
   var isEdge = msedge > 0;
   //If you support IE, our recommendation is that you sign-in using Redirect APIs
   //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
   // can change this to default an experience outside browser use
   var loginType = isIE ? "REDIRECT" : "POPUP";

   if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
   }
   else if (loginType === 'REDIRECT') {
       document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
       };
       if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
   } else {
       console.error('Please set a valid login type');
   }
   ```

<!--start-collapse-->
### <a name="more-information"></a>자세한 정보

사용자가 **로그인** 단추를 처음 선택하면 `signIn` 메서드에서 `loginPopup`을 호출하여 사용자를 로그인합니다. 이 메서드는 *Microsoft ID 플랫폼 엔드포인트*가 있는 팝업 창을 열어 사용자의 자격 증명을 묻고 유효성을 검사합니다. 성공적으로 로그인하면 사용자가 원래 *index.html* 페이지로 다시 리디렉션됩니다. `msal.js`에서 토큰을 받고 처리하며, 토큰에 포함된 정보가 캐시됩니다. 이 토큰은 *ID 토큰*이라고 하며 사용자 표시 이름과 같은 사용자에 대한 기본 정보를 포함합니다. 이 토큰에서 제공하는 데이터를 어떤 용도로든 사용할 계획이면 백 엔드 서버에서 이 토큰의 유효성을 검사하여 토큰이 애플리케이션의 유효한 사용자에게 발급되었음을 보장하는지 확인해야 합니다.

이 가이드에서 생성하는 SPA는 `acquireTokenSilent` 및/또는 `acquireTokenPopup`를 호출하여 사용자 프로필 정보에 대해 Microsoft Graph API를 쿼리하는 데 사용하는 *액세스 토큰*을 가져옵니다. ID 토큰의 유효성을 검사하는 샘플이 필요한 경우 GitHub에서 [이](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 샘플") 샘플 애플리케이션을 살펴보세요. 이 샘플에서는 ASP.NET Web API를 토큰 유효성 검사에 사용합니다.

#### <a name="getting-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

초기 로그인 후에는 리소스에 액세스하기 위해 토큰을 요청해야 할 때마다 사용자에게 다시 인증하도록 요청하지 않으려고 합니다. 따라서 토큰을 획득하는 대부분의 경우 *acquireTokenSilent*를 사용해야 합니다. 그러나 사용자가 Microsoft ID 플랫폼 엔드포인트와 강제로 상호 작용해야 하는 경우도 있습니다. 다음은 이러한 템플릿의 예입니다.

- 암호가 만료되어 사용자가 해당 자격 증명을 다시 입력해야 합니다.
- 애플리케이션에서 리소스에 액세스하도록 요청하고 있으며 사용자의 동의가 필요합니다.
- 2단계 인증이 필요합니다.

*acquireTokenPopup*을 호출하면 팝업 창이 열립니다(또는 *acquireTokenRedirect*에서 사용자를 Microsoft ID 플랫폼 엔드포인트로 리디렉션함). 이 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 동의하거나, 2단계 인증을 수행하여 상호 작용해야 합니다.

#### <a name="getting-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginPopup`(또는 `loginRedirect`)이 처음 실행되면 `acquireTokenSilent`가 이후 호출에서 보호된 리소스에 액세스하는 데 사용되는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다. (토큰을 요청하거나 갱신하기 위한 호출은 자동으로 수행됩니다.) `acquireTokenSilent`는 경우에 따라 실패할 수 있습니다. 예를 들어 사용자의 암호가 만료되었을 수 있습니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1. 즉시 `acquireTokenPopup`을 호출합니다. 그러면 사용자 로그인 프롬프트가 트리거됩니다. 이 패턴은 애플리케이션에 사용자가 사용할 수 있는 인증되지 않은 콘텐츠가 없는 온라인 애플리케이션에서 일반적으로 사용됩니다. 이 설정 안내에서 생성하는 예제는 이 패턴을 사용합니다.

2. 또한 애플리케이션에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 애플리케이션이 `acquireTokenSilent`를 다시 시작할 수 있습니다. 이는 일반적으로 사용자가 중단 없이 애플리케이션의 다른 기능을 사용할 수 있는 경우에 사용됩니다. 예를 들어 애플리케이션에서 사용할 수 있는 인증되지 않은 콘텐츠가 있을 수 있습니다. 이 경우 사용자는 보호된 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인하려는 시기를 결정할 수 있습니다.

> [!NOTE]
> 이 빠른 시작에서는 Internet Explorer가 사용되는 브라우저인 경우 `loginRedirect` 및 `acquireTokenRedirect` 메서드를 사용합니다. Internet Explorer에서 팝업 창을 처리하는 방법과 관련된 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)로 인해 이 방법을 따릅니다.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>방금 획득한 토큰을 사용하여 Microsoft Graph API 호출

`index.html` 파일의 `<script></script>` 태그 내에 다음 코드를 추가합니다.

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 가이드에서 만든 샘플 애플리케이션에서 `callMSGraph()` 메서드는 토큰을 요구하는 보호된 리소스에 대한 HTTP `GET` 요청을 수행하는 데 사용됩니다. 그러면 요청에서 콘텐츠를 호출자에 반환합니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 가이드에서 만든 샘플 애플리케이션의 경우 리소스는 사용자의 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

`index.html` 파일의 `<script></script>` 태그 내에 다음 코드를 추가합니다.

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>애플리케이션 등록

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 계정에서 둘 이상의 테넌트에 대한 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 포털 세션을 사용하려는 Azure AD 테넌트로 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **리디렉션 URI** 섹션의 드롭다운 목록에서 **웹** 플랫폼을 선택한 다음, 값을 웹 서버에 기반한 애플리케이션 URL로 설정합니다.

   Node.js 및 Visual Studio에 대한 리디렉션 URL을 설정하고 가져오는 방법에 대한 자세한 내용은 다음에 나오는 "Node.js에 대한 리디렉션 URL 설정" 섹션 및 [Visual Studio에 대한 리디렉션 URL 설정](#set-a-redirect-url-for-visual-studio)을 참조하세요.

1. **등록**을 선택합니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 빠른 시작에서는 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 선택합니다. 이 앱은 사용자를 로그인하고 API를 호출해야 하므로 ID 토큰 및 액세스 토큰이 필요합니다.
1. **저장**을 선택합니다.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Node.js에 대한 리디렉션 URL 설정
> Node.js의 경우 *server.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서에서는 30662 포트를 사용하지만 사용 가능한 다른 포트도 사용할 수 있습니다.
>
> 애플리케이션 등록 정보에 리디렉션 URL을 설정하려면, **애플리케이션 등록** 창으로 다시 전환하고 다음 중 하나를 수행합니다.
>
> - *`http://localhost:30662/`* 를 **리디렉션 URL**로 설정합니다.
> - 사용자 지정 TCP 포트를 사용하는 경우에는, *`http://localhost:<port>/`* (여기서 *\<port>* 는 사용자 지정 TCP 포트 번호임)를 사용합니다.
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Visual Studio에 대한 리디렉션 URL 설정
> Visual Studio에 대한 리디렉션 URL를 가져오려면 다음 단계를 수행합니다.
> 1. 솔루션 탐색기에서 프로젝트를 선택합니다.
>
>    **속성** 창이 열립니다. 열리지 않으면 F4 키를 누릅니다.
>
>    ![JavaScriptSPA 프로젝트 속성 창](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. **URL** 값을 복사합니다.
> 1. **애플리케이션 등록** 창으로 다시 전환한 후, 복사한 값을 **리디렉션 URL**로 붙여넣습니다.

#### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

1. 프로젝트 설정 중에 생성한 *index.html* 파일에서 애플리케이션 등록 정보를 추가합니다. 파일 맨 위에서 `<script></script>` 태그 안에 다음 코드를 추가합니다.

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    위치:
    - *\<Enter_the_Application_Id_here>* 는 등록한 애플리케이션에 대한 **애플리케이션(클라이언트) ID**입니다.
    - *\<Enter_the_Tenant_info_here>* 는 다음 옵션 중 하나로 설정됩니다.
       - 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: *contoso.microsoft.com*)으로 바꿉니다.
       - 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**로 바꿉니다.
       - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다.

## <a name="test-your-code"></a>코드 테스트

다음 환경 중 하나를 사용하여 코드를 테스트합니다.

### <a name="test-with-nodejs"></a>Node.js를 사용하여 테스트

Visual Studio를 사용하지 않는 경우 웹 서버가 시작되었는지 확인합니다.

1. *index.html* 파일의 위치를 기반으로 하는 TCP 포트에서 수신 대기하도록 서버를 구성합니다. Node.js의 경우 애플리케이션 폴더의 명령줄 프롬프트에서 다음 명령을 실행하여 포트를 수신 대기하도록 웹 서버를 시작합니다.

    ```bash
    npm install
    node server.js
    ```
1. 브라우저에서 **http://\<span>\</span>localhost:30662** 또는 **http://\<span>\</span>localhost:{port}** 를 입력합니다. 여기서 *port*는 웹 서버가 수신 대기 중인 포트입니다. *index.html* 파일과 **로그인** 단추의 내용을 확인해야 합니다.

### <a name="test-with-visual-studio"></a>Visual Studio를 사용한 테스트

Visual Studio를 사용하는 경우 프로젝트 솔루션을 선택한 다음, F5 키를 눌러 프로젝트를 실행합니다. 브라우저가 열리고 http://<span></span>localhost:{port} 위치로 이동합니다. **로그인** 단추가 표시됩니다.

## <a name="test-your-application"></a>애플리케이션 테스트

브라우저에서 *index.html* 파일이 로드되면 **로그인**을 선택합니다. Microsoft ID 플랫폼 엔드포인트로 로그인하라는 메시지가 표시됩니다.

![JavaScript SPA 계정 로그인 창](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 프로필에 대한 액세스 권한을 부여하고 로그인하라는 메시지가 표시됩니다.

!["사용 권한 요청됨" 창](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>애플리케이션 결과 보기

로그인하면 표시되는 Microsoft Graph API 응답에 사용자 프로필 정보가 반환됩니다.

![Microsoft Graph API 호출의 결과](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 등록 포털에서 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자 일정을 나열하기 위해 *Calendars.Read* 범위가 필요합니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다.

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요하지 않은 경우(추천되지 않음) 토큰을 획득하기 위한 호출에서 *clientId*를 범위로 사용할 수 있습니다.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID 플랫폼을 개선할 수 있도록 도와주세요. 간단한 두 가지 설문 조사를 완료하여 의견을 알려주세요.

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
