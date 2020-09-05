---
title: JavaScript 단일 페이지 앱 자습서 | Azure
titleSuffix: Microsoft identity platform
description: 이 자습서에서는 JavaScript SPA(단일 페이지 앱)가 Microsoft ID 플랫폼에서 발급한 액세스 토큰을 필요로 하는 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/06/2020
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-javascript
ms.openlocfilehash: 71516104ce5711f716b6af9d37ba96b431749fa3
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118198"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>JavaScript SPA(단일 페이지 애플리케이션)에서 사용자 로그인 및 Microsoft Graph API 호출

이 가이드에서는 JavaScript SPA(단일 페이지 애플리케이션)에서 다음을 수행하는 방법을 보여 줍니다.
- 회사 및 학교 계정과 개인 계정으로 로그인
- 액세스 토큰 획득
- *Microsoft ID 플랫폼 엔드포인트*에서 액세스 토큰을 요구하는 Microsoft Graph API 또는 다른 API 호출

>[!TIP]
> 이 자습서에서는 단일 페이지 애플리케이션에 대한 암시적 권한 부여 흐름을 사용하도록 제한되는 MSAL.js v1.x를 사용합니다. 대신 모든 새 애플리케이션에서 [MSAL.js 2.x 및 PKCE, CORS를 사용한 인증 코드 흐름](tutorial-v2-javascript-auth-code.md) 지원을 사용하는 것이 좋습니다.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

### <a name="more-information"></a>자세한 정보

이 가이드에서 만든 샘플 애플리케이션을 사용하면 JavaScript SPA에서 Microsoft ID 플랫폼 엔드포인트의 토큰을 수락하는 Microsoft Graph API 또는 웹 API를 쿼리할 수 있습니다. 이 시나리오에서는 사용자가 로그인하면 권한 부여 헤더를 통해 액세스 토큰이 요청되고 HTTP 요청에 추가됩니다. 이 토큰은 **MS Graph API**를 통해 사용자의 프로필과 메일을 가져오는 데 사용됩니다. 토큰 획득 및 갱신은 **JavaScript용 MSAL(Microsoft 인증 라이브러리)** 에서 처리합니다.

### <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript용 Microsoft 인증 라이브러리|

## <a name="set-up-your-web-server-or-project"></a>웹 서버 또는 프로젝트 설정

> 이 샘플의 프로젝트를 다운로드하고 싶으세요? [프로젝트 파일을 다운로드합니다](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> 코드 샘플을 먼저 구성한 후에 실행하려면 [구성 단계](#register-your-application)로 건너뜁니다.

## <a name="prerequisites"></a>필수 구성 요소

* 이 자습서를 실행하려면 [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) 또는 IIS Express와 같은 로컬 웹 서버가 [Visual Studio 2017](https://www.visualstudio.com/downloads/)과 연결되어 있어야 합니다.

* 이 가이드의 지침은 Node.js에서 빌드된 웹 서버를 기반으로 합니다. [Visual Studio Code](https://code.visualstudio.com/download)를 IDE(통합 개발 환경)로 사용하는 것이 좋습니다.

* 최신 웹 브라우저. 이 JavaScript 샘플은 [ES6](http://www.ecma-international.org/ecma-262/6.0/) 규칙을 사용하므로 **Internet Explorer**를 지원하지 **않습니다**.

## <a name="create-your-project"></a>프로젝트 만들기

[Node.js](https://nodejs.org/en/download/)가 설치되어 있는지 확인한 다음, 애플리케이션을 호스팅할 폴더를 만듭니다. 여기서는 `index.html` 파일을 제공하는 간단한 [Express](https://expressjs.com/) 웹 서버를 구현합니다.

1. 터미널(예: Visual Studio Code 통합 터미널)을 사용하여 프로젝트 폴더를 찾은 후, 다음을 입력합니다.

   ```console
   npm init
   ```

2. 그런 다음, 필수 종속성을 설치합니다.

   ```console
   npm install express --save
   npm install morgan --save
   ```

1. 이제 `index.js`라는 .js 파일을 만든 후, 다음 코드를 추가합니다.

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');

   //initialize express.
   const app = express();

   // Initialize variables.
   const port = 3000; // process.env.PORT || 3000;

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use(express.static('JavaScriptSPA'))

   // Set up a route for index.html.
   app.get('*', function (req, res) {
       res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log('Listening on port ' + port + '...');
   ```

이제 SPA를 제공하는 간단한 서버가 있습니다. 이 자습서의 끝부분에 있는 의도된 폴더 구조는 다음과 같습니다.

![의도된 SPA 폴더 구조의 텍스트 표현](./media/tutorial-v2-javascript-spa/single-page-application-folder-structure.png)

## <a name="create-the-spa-ui"></a>SPA UI 만들기

1. JavaScript SPA에 대한 `index.html` 파일을 만듭니다. 이 파일은 **Bootstrap 4 Framework**를 사용하여 빌드된 UI를 구현하고 구성, 인증 및 API 호출을 위한 스크립트 파일을 가져옵니다.

   `index.html` 파일에서 다음 코드를 추가합니다.

   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
       <title>Quickstart | MSAL.JS Vanilla JavaScript SPA</title>

       <!-- msal.js with a fallback to backup CDN -->
       <script type="text/javascript" src="https://alcdn.msauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
       <script type="text/javascript">
         if(typeof Msal === 'undefined')document.write(unescape("%3Cscript src='https://alcdn.msftauth.net/lib/1.2.1/js/msal.js' type='text/javascript' integrity='sha384-m/3NDUcz4krpIIiHgpeO0O8uxSghb+lfBTngquAo2Zuy2fEF+YgFeP08PWFo5FiJ' crossorigin='anonymous'%3E%3C/script%3E"));
       </script>

       <!-- adding Bootstrap 4 for UI components  -->
       <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
     </head>
     <body>
       <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
         <a class="navbar-brand" href="/">MS Identity Platform</a>
         <div class="btn-group ml-auto dropleft">
           <button type="button" id="signIn" class="btn btn-secondary" onclick="signIn()">Sign In</button>
           <button type="button" id="signOut" class="btn btn-success d-none" onclick="signOut()">Sign Out</button>
       </div>
       </nav>
       <br>
       <h5 class="card-header text-center">Vanilla JavaScript SPA calling MS Graph API with MSAL.JS</h5>
       <br>
       <div class="row" style="margin:auto" >
       <div id="card-div" class="col-md-3 d-none">
       <div class="card text-center">
         <div class="card-body">
           <h5 class="card-title" id="welcomeMessage">Please sign-in to see your profile and read your mails</h5>
           <div id="profile-div"></div>
           <br>
           <br>
           <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
           <br>
           <br>
           <button class="btn btn-primary d-none" id="readMail" onclick="readMail()">Read Mails</button>
         </div>
       </div>
       </div>
       <br>
       <br>
         <div class="col-md-4">
           <div class="list-group" id="list-tab" role="tablist">
           </div>
         </div>
         <div class="col-md-5">
           <div class="tab-content" id="nav-tabContent">
           </div>
         </div>
       </div>
       <br>
       <br>

       <!-- importing bootstrap.js and supporting js libraries -->
       <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
       <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
       <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

       <!-- importing app scripts (load order is important) -->
       <script type="text/javascript" src="./authConfig.js"></script>
       <script type="text/javascript" src="./graphConfig.js"></script>
       <script type="text/javascript" src="./ui.js"></script>

       <!-- replace next line with authRedirect.js if you would like to use the redirect flow -->
       <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
       <script type="text/javascript" src="./authPopup.js"></script>
       <script type="text/javascript" src="./graph.js"></script>
     </body>
   </html>
   ```

   > [!TIP]
   > 위 스크립트의 MSAL.js 버전을 최근에 출시된 [MSAL.js 릴리스](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) 버전으로 바꿀 수 있습니다.

2. 이제 DOM 요소에 액세스하여 업데이트하는 `ui.js`라는 .js 파일을 만들고, 다음 코드를 추가합니다.

   ```JavaScript
   // Select DOM elements to work with
   const welcomeDiv = document.getElementById("welcomeMessage");
   const signInButton = document.getElementById("signIn");
   const signOutButton = document.getElementById('signOut');
   const cardDiv = document.getElementById("card-div");
   const mailButton = document.getElementById("readMail");
   const profileButton = document.getElementById("seeProfile");
   const profileDiv = document.getElementById("profile-div");

   function showWelcomeMessage(account) {
     // Reconfiguring DOM elements
     cardDiv.classList.remove('d-none');
     welcomeDiv.innerHTML = `Welcome ${account.name}`;
     signInButton.classList.add('d-none');
     signOutButton.classList.remove('d-none');
   }

   function updateUI(data, endpoint) {
     console.log('Graph API responded at: ' + new Date().toString());

     if (endpoint === graphConfig.graphMeEndpoint) {
       const title = document.createElement('p');
       title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
       const email = document.createElement('p');
       email.innerHTML = "<strong>Mail: </strong>" + data.mail;
       const phone = document.createElement('p');
       phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
       const address = document.createElement('p');
       address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
       profileDiv.appendChild(title);
       profileDiv.appendChild(email);
       profileDiv.appendChild(phone);
       profileDiv.appendChild(address);

     } else if (endpoint === graphConfig.graphMailEndpoint) {
         if (data.value.length < 1) {
           alert("Your mailbox is empty!")
         } else {
           const tabList = document.getElementById("list-tab");
           tabList.innerHTML = ''; // clear tabList at each readMail call
           const tabContent = document.getElementById("nav-tabContent");

           data.value.map((d, i) => {
             // Keeping it simple
             if (i < 10) {
               const listItem = document.createElement("a");
               listItem.setAttribute("class", "list-group-item list-group-item-action")
               listItem.setAttribute("id", "list" + i + "list")
               listItem.setAttribute("data-toggle", "list")
               listItem.setAttribute("href", "#list" + i)
               listItem.setAttribute("role", "tab")
               listItem.setAttribute("aria-controls", i)
               listItem.innerHTML = d.subject;
               tabList.appendChild(listItem)

               const contentItem = document.createElement("div");
               contentItem.setAttribute("class", "tab-pane fade")
               contentItem.setAttribute("id", "list" + i)
               contentItem.setAttribute("role", "tabpanel")
               contentItem.setAttribute("aria-labelledby", "list" + i + "list")
               contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
               tabContent.appendChild(contentItem);
             }
           });
         }
     }
   }
   ```

## <a name="register-your-application"></a>애플리케이션 등록

인증을 계속 진행하기 전에 애플리케이션을 **Azure Active Directory**에 등록합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 계정에서 둘 이상의 테넌트에 대한 액세스를 제공하는 경우 오른쪽 위에 있는 계정을 선택한 다음, 포털 세션을 사용하려는 Azure AD 테넌트로 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **애플리케이션 등록** 페이지가 나타나면 애플리케이션의 이름을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정**을 선택합니다.
1. **리디렉션 URI** 섹션의 드롭다운 목록에서 **웹** 플랫폼을 선택한 다음, 값을 웹 서버에 기반한 애플리케이션 URL로 설정합니다.
1. **등록**을 선택합니다.
1. 나중에 사용할 수 있도록 앱 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 기록해 둡니다.
1. 이 빠른 시작에서는 [암시적 허용 흐름](v2-oauth2-implicit-grant-flow.md)을 사용하도록 설정해야 합니다. 등록된 애플리케이션의 왼쪽 창에서 **인증**을 선택합니다.
1. **고급 설정**의 **암시적 허용**에서 **ID 토큰** 및 **액세스 토큰** 확인란을 선택합니다. 이 앱은 사용자를 로그인하고 API를 호출해야 하므로 ID 토큰 및 액세스 토큰이 필요합니다.
1. **저장**을 선택합니다.

> ### <a name="set-a-redirect-url-for-nodejs"></a>Node.js에 대한 리디렉션 URL 설정
>
> Node.js의 경우 *index.js* 파일에서 웹 서버 포트를 설정할 수 있습니다. 이 자습서에서는 3000 포트를 사용하지만 사용 가능한 다른 포트도 사용할 수 있습니다.
>
> 애플리케이션 등록 정보에 리디렉션 URL을 설정하려면, **애플리케이션 등록** 창으로 다시 전환하고 다음 중 하나를 수행합니다.
>
> - *`http://localhost:3000/`* 를 **리디렉션 URL**로 설정합니다.
> - 사용자 지정 TCP 포트를 사용하는 경우에는 *`http://localhost:<port>/`* (여기서 *\<port>* 는 사용자 지정 TCP 포트 번호임)를 사용합니다.
>   1. **URL** 값을 복사합니다.
>   1. **애플리케이션 등록** 창으로 다시 전환한 후, 복사한 값을 **리디렉션 URL**로 붙여넣습니다.
>

### <a name="configure-your-javascript-spa"></a>JavaScript SPA 구성

인증을 위한 구성 매개 변수를 포함하는 `authConfig.js`라는 새 .js 파일을 만들고, 다음 코드를 추가합니다.

```javascript
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

  // Add here scopes for id token to be used at MS Identity Platform endpoints.
  const loginRequest = {
    scopes: ["openid", "profile", "User.Read"]
  };

  // Add here scopes for access token to be used at MS Graph API endpoints.
  const tokenRequest = {
    scopes: ["Mail.Read"]
  };
```

 위치:
 - *\<Enter_the_Application_Id_Here>* 는 등록한 애플리케이션의 **애플리케이션(클라이언트) ID**입니다.
 - *\<Enter_the_Cloud_Instance_Id_Here>* 는 Azure 클라우드의 인스턴스입니다. 주 또는 글로벌 Azure 클라우드의 경우 *https://login.microsoftonline.com* 을 입력하면 됩니다. **국가별** 클라우드(예제: 중국)의 경우 [국가별 클라우드](./authentication-national-cloud.md)를 참조하세요.
 - *\<Enter_the_Tenant_info_here>* 는 다음 옵션 중 하나로 설정됩니다.
   - 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: *contoso.microsoft.com*)으로 바꿉니다.
   - 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**으로 바꿉니다.
   - 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다.


## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>사용자 로그인에 MSAL(Microsoft 인증 라이브러리) 사용

인증 및 토큰 획득 논리를 포함하는 `authPopup.js`라는 새 .js 파일을 만들고, 다음 코드를 추가합니다.

   ```JavaScript
   const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   function signIn() {
     myMSALObj.loginPopup(loginRequest)
       .then(loginResponse => {
         console.log('id_token acquired at: ' + new Date().toString());
         console.log(loginResponse);

         if (myMSALObj.getAccount()) {
           showWelcomeMessage(myMSALObj.getAccount());
         }
       }).catch(error => {
         console.log(error);
       });
   }

   function signOut() {
     myMSALObj.logout();
   }

   function callMSGraph(theUrl, accessToken, callback) {
       var xmlHttp = new XMLHttpRequest();
       xmlHttp.onreadystatechange = function () {
           if (this.readyState == 4 && this.status == 200) {
              callback(JSON.parse(this.responseText));
           }
       }
       xmlHttp.open("GET", theUrl, true); // true for asynchronous
       xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
       xmlHttp.send();
   }

   function getTokenPopup(request) {
     return myMSALObj.acquireTokenSilent(request)
       .catch(error => {
         console.log(error);
         console.log("silent token acquisition fails. acquiring token using popup");

         // fallback to interaction when silent call fails
           return myMSALObj.acquireTokenPopup(request)
             .then(tokenResponse => {
               return tokenResponse;
             }).catch(error => {
               console.log(error);
             });
       });
   }

   function seeProfile() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(loginRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
           profileButton.classList.add('d-none');
           mailButton.classList.remove('d-none');
         }).catch(error => {
           console.log(error);
         });
     }
   }

   function readMail() {
     if (myMSALObj.getAccount()) {
       getTokenPopup(tokenRequest)
         .then(response => {
           callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
         }).catch(error => {
           console.log(error);
         });
     }
   }
   ```

### <a name="more-information"></a>자세한 정보

사용자가 **로그인** 단추를 처음 선택하면 `signIn` 메서드에서 `loginPopup`을 호출하여 사용자를 로그인합니다. 이 메서드는 *Microsoft ID 플랫폼 엔드포인트*가 있는 팝업 창을 열어 사용자의 자격 증명을 묻고 유효성을 검사합니다. 성공적으로 로그인하면 사용자가 원래 *index.html* 페이지로 다시 리디렉션됩니다. `msal.js`에서 토큰을 받고 처리하며, 토큰에 포함된 정보가 캐시됩니다. 이 토큰은 *ID 토큰*이라고 하며 사용자 표시 이름과 같은 사용자에 대한 기본 정보를 포함합니다. 이 토큰에서 제공하는 데이터를 어떤 용도로든 사용할 계획이면 백 엔드 서버에서 이 토큰의 유효성을 검사하여 토큰이 애플리케이션의 유효한 사용자에게 발급되었음을 보장하는지 확인해야 합니다.

이 가이드에서 생성하는 SPA는 `acquireTokenSilent` 및/또는 `acquireTokenPopup`를 호출하여 사용자 프로필 정보에 대해 Microsoft Graph API를 쿼리하는 데 사용하는 *액세스 토큰*을 가져옵니다. ID 토큰의 유효성을 검사하는 샘플이 필요한 경우 GitHub에서 [이](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "GitHub active-directory-javascript-singlepageapp-dotnet-webapi-v2 샘플") 샘플 애플리케이션을 살펴보세요. 이 샘플에서는 ASP.NET 웹 API를 토큰 유효성 검사에 사용합니다.

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

초기 로그인 후에는 리소스에 액세스하기 위해 토큰을 요청해야 할 때마다 사용자에게 다시 인증하도록 요청하지 않으려고 합니다. 따라서 토큰을 획득하는 대부분의 경우 *acquireTokenSilent*를 사용해야 합니다. 그러나 사용자가 Microsoft ID 플랫폼 엔드포인트와 강제로 상호 작용해야 하는 경우도 있습니다. 다음은 이러한 템플릿의 예입니다.

- 암호가 만료되어 사용자가 해당 자격 증명을 다시 입력해야 합니다.
- 애플리케이션에서 리소스에 액세스하도록 요청하고 있으며 사용자의 동의가 필요합니다.
- 2단계 인증이 필요합니다.

*acquireTokenPopup*을 호출하면 팝업 창이 열립니다(또는 *acquireTokenRedirect*에서 사용자를 Microsoft ID 플랫폼 엔드포인트로 리디렉션함). 이 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 동의하거나, 2단계 인증을 수행하여 상호 작용해야 합니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginPopup`(또는 `loginRedirect`)이 처음 실행되면 `acquireTokenSilent`가 이후 호출에서 보호된 리소스에 액세스하는 데 사용되는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다. (토큰을 요청하거나 갱신하기 위한 호출은 자동으로 수행됩니다.) `acquireTokenSilent`는 경우에 따라 실패할 수 있습니다. 예를 들어 사용자의 암호가 만료되었을 수 있습니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

1. 즉시 `acquireTokenPopup`을 호출합니다. 그러면 사용자 로그인 프롬프트가 트리거됩니다. 이 패턴은 애플리케이션에 사용자가 사용할 수 있는 인증되지 않은 콘텐츠가 없는 온라인 애플리케이션에서 일반적으로 사용됩니다. 이 설정 안내에서 생성하는 예제는 이 패턴을 사용합니다.

1. 또한 애플리케이션에서는 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 보여줍니다. 따라서 사용자가 로그인할 적절한 시간을 선택하거나 이후에 애플리케이션이 `acquireTokenSilent`를 다시 시작할 수 있습니다. 이는 일반적으로 사용자가 중단 없이 애플리케이션의 다른 기능을 사용할 수 있는 경우에 사용됩니다. 예를 들어 애플리케이션에서 사용할 수 있는 인증되지 않은 콘텐츠가 있을 수 있습니다. 이 경우 사용자는 보호된 리소스에 액세스하거나 오래된 정보를 새로 고치기 위해 로그인하려는 시기를 결정할 수 있습니다.

> [!NOTE]
> 이 빠른 시작에서는 기본적으로 `loginPopup` 및 `acquireTokenPopup` 메서드를 사용합니다. Internet Explorer를 브라우저로 사용하는 경우 Internet Explorer에서 팝업 창을 처리하는 방법과 관련된 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)로 인해 `loginRedirect` 및 `acquireTokenRedirect` 메서드를 사용하는 것이 좋습니다. `Redirect methods`를 사용하여 동일한 결과를 얻는 방법을 알아보려면 [여기를 참조하세요](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js).

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-acquired"></a>방금 획득한 토큰을 사용하여 Microsoft Graph API 호출

1. 먼저 REST 엔드포인트를 저장하는 `graphConfig.js`라는 .js 파일을 만듭니다. 다음 코드를 추가합니다.

   ```JavaScript
      const graphConfig = {
        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
        graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
      };
   ```

   위치:
   - *\<Enter_the_Graph_Endpoint_Here>* 는 MS Graph API의 인스턴스입니다. 글로벌 MS Graph API 엔드포인트의 경우 이 문자열을 `https://graph.microsoft.com`으로 바꾸기만 하면 됩니다. 국가별 클라우드 배포는 [Graph API 설명서](/graph/deployments)를 참조하세요.

1. 다음으로, Microsoft Graph API에 대한 REST 호출을 수행하는 `graph.js`라는 .js 파일을 만들고, 다음 코드를 추가합니다.

   ```javascript
   function callMSGraph(endpoint, token, callback) {
     const headers = new Headers();
     const bearer = `Bearer ${token}`;

     headers.append("Authorization", bearer);

     const options = {
         method: "GET",
         headers: headers
     };

     console.log('request made to Graph API at: ' + new Date().toString());

     fetch(endpoint, options)
       .then(response => response.json())
       .then(response => callback(response, endpoint))
       .catch(error => console.log(error))
   }
   ```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 가이드에서 만든 샘플 애플리케이션에서 `callMSGraph()` 메서드는 토큰을 요구하는 보호된 리소스에 대한 HTTP `GET` 요청을 수행하는 데 사용됩니다. 그러면 요청에서 콘텐츠를 호출자에 반환합니다. 이 메서드는 *HTTP 인증 헤더*에 획득된 토큰을 추가합니다. 이 가이드에서 만든 샘플 애플리케이션의 경우 리소스는 사용자의 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.

## <a name="test-your-code"></a>코드 테스트

1. *index.html* 파일의 위치를 기반으로 하는 TCP 포트에서 수신 대기하도록 서버를 구성합니다. Node.js의 경우 애플리케이션 폴더의 명령줄 프롬프트에서 다음 명령을 실행하여 포트를 수신 대기하도록 웹 서버를 시작합니다.

   ```bash
   npm install
   npm start
   ```
1. 브라우저에서 **http://localhost:3000** 또는 **http://localhost:{port}** 를 입력합니다. 여기서 *port*는 웹 서버에서 수신 대기하는 포트입니다. *index.html* 파일과 **로그인** 단추의 내용을 확인해야 합니다.

## <a name="test-your-application"></a>애플리케이션 테스트

브라우저에서 *index.html* 파일이 로드되면 **로그인**을 선택합니다. Microsoft ID 플랫폼 엔드포인트로 로그인하라는 메시지가 표시됩니다.

![JavaScript SPA 계정 로그인 창](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 프로필에 대한 액세스 권한을 부여하고 로그인하라는 메시지가 표시됩니다.

!["사용 권한 요청됨" 창](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>애플리케이션 결과 보기

로그인하면 표시되는 Microsoft Graph API 응답에 사용자 프로필 정보가 반환됩니다.

![Microsoft Graph API 호출의 결과](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 등록 포털에서 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자의 메일을 나열하기 위해 *Mail.Read* 범위가 필요합니다.

> [!NOTE]
> 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]