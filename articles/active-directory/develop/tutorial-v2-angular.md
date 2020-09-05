---
title: Angular 단일 페이지 앱 자습서 - Azure
titleSuffix: Microsoft identity platform
description: Angular SPA 애플리케이션에서 Microsoft ID 플랫폼 엔드포인트의 액세스 토큰이 필요한 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-javascript
ms.openlocfilehash: a58da8b11876d662173ae83de43d8ed74ab43e93
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118300"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>자습서: Angular 단일 페이지 애플리케이션에서 사용자 로그인 및 Microsoft Graph API 호출

이 자습서에서는 Angular SPA(단일 페이지 애플리케이션)에서 다음을 수행하는 방법을 보여줍니다.
- 개인 계정, 회사 계정 또는 학교 계정에 로그인
- 액세스 토큰 획득
- *Microsoft ID 플랫폼 엔드포인트*에서 액세스 토큰을 요구하는 Microsoft Graph API 또는 다른 API 호출

>[!NOTE]
>이 자습서에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 새 Angular SPA를 만드는 방법을 안내합니다. 샘플 앱을 다운로드하려면 [빠른 시작](quickstart-v2-angular.md)을 참조하세요.

## <a name="how-the-sample-app-works"></a>샘플 앱의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식을 보여주는 다이어그램](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

### <a name="more-information"></a>자세한 정보

이 자습서에서 만드는 샘플 애플리케이션을 사용하면 Angular SPA에서 Microsoft ID 플랫폼 엔드포인트의 토큰을 수락하는 Microsoft Graph API 또는 웹 API를 쿼리할 수 있습니다. Angular용 MSAL 라이브러리는 핵심 MSAL.js 라이브러리의 래퍼입니다. 이 라이브러리를 사용하면 Angular(6+) 애플리케이션에서 Microsoft Azure Active Directory, Microsoft 계정 사용자 및 소셜 ID 사용자(Facebook, Google, LinkedIn 등)를 사용하여 엔터프라이즈 사용자를 인증할 수 있습니다. 또한 이 라이브러리를 사용하면 애플리케이션에서 Microsoft 클라우드 서비스 또는 Microsoft Graph에 액세스할 수 있습니다.

이 시나리오에서는 사용자가 로그인하면 권한 부여 헤더를 통해 액세스 토큰이 요청되고 HTTP 요청에 추가됩니다. 토큰 획득 및 갱신은 MSAL에서 처리합니다.

### <a name="libraries"></a>라이브러리

이 자습서에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|JavaScript Angular 래퍼용 Microsoft 인증 라이브러리|

GitHub의 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 리포지토리에서 MSAL 라이브러리의 소스 코드를 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 실행하려면 다음이 필요합니다.

* [Node.js](https://nodejs.org/en/download/) 같은 로컬 웹 서버. 이 자습서의 지침은 Node.js 기준입니다.
* 프로젝트 파일을 편집하기 위한 [Visual Studio Code](https://code.visualstudio.com/download) 같은 IDE(통합 개발 환경)

## <a name="create-your-project"></a>프로젝트 만들기

다음 npm 명령을 사용하여 새 Angular 애플리케이션을 생성합니다.

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>애플리케이션 등록

지침에 따라 Azure Portal에서 [단일 페이지 애플리케이션을 등록](./scenario-spa-app-registration.md)합니다.

나중에 사용할 수 있도록 등록의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값을 적어 둡니다.

**리디렉션 URI** 값을 **http://localhost:4200/** 으로 등록하고 암시적 권한 부여 설정을 사용하도록 설정합니다.

## <a name="configure-the-application"></a>애플리케이션 구성

1. 아래와 같이 *src/app* 폴더에서 *app.module.ts*를 편집하고 `imports` 및 `isIE` 상수에 `MSALModule`을 추가합니다.

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    다음 값을 바꿉니다.

    |값 이름|정보|
    |---------|---------|
    |Enter_the_Application_Id_Here|애플리케이션 등록의 **개요** 페이지에서 이는 **애플리케이션(클라이언트) ID** 값입니다. |
    |Enter_the_Cloud_Instance_Id_Here|Azure 클라우드의 인스턴스입니다. 기본 또는 글로벌 Azure 클라우드의 경우 **https://login.microsoftonline.com** 을 입력합니다. 국가별 클라우드(예: 중국)의 경우 [국가별 클라우드](./authentication-national-cloud.md)를 참조하세요.|
    |Enter_the_Tenant_Info_Here| 다음 옵션 중 하나로 설정합니다. 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 디렉터리(테넌트) ID 또는 테넌트 이름(예: **contoso.microsoft.com**)으로 바꿉니다. 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**으로 바꿉니다. 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다. |
    |Enter_the_Redirect_Uri_Here|**http://localhost:4200** 으로 바꿉니다.|

    사용할 수 있는 구성 가능한 옵션에 대한 자세한 내용은 [클라이언트 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

2. 같은 파일의 맨 위에 다음 import 문을 추가합니다.

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. 다음 import 문을 `src/app/app.component.ts` 맨 위에 추가합니다.

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>사용자 로그인

다음 코드를 `AppComponent`에 추가하여 사용자를 로그인합니다.

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Internet Explorer 사용자에게는 `loginRedirect`를 사용하는 것이 좋습니다.

## <a name="acquire-a-token"></a>토큰 획득

### <a name="angular-interceptor"></a>Angular 인터셉터

MSAL Angular는 알려진 보호된 리소스에 Angular `http` 클라이언트를 사용하는 발신 요청에 대한 토큰을 자동으로 획득하는 `Interceptor` 클래스를 제공합니다.

먼저 `Interceptor` 클래스를 애플리케이션에 공급자로 포함합니다.

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

다음으로, 보호된 리소스의 맵을 `MsalModule.forRoot()`에 `protectedResourceMap`으로 제공하고 해당 범위를 `consentScopes`에 포함합니다.

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

마지막으로, 다음과 같이 HTTP 요청을 사용하여 사용자의 프로필을 검색합니다.

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
MSAL은 토큰을 획득하는 데 `acquireTokenRedirect`, `acquireTokenPopup` 및 `acquireTokenSilent`의 세 가지 메서드를 사용합니다. 그러나 이전 섹션에서 설명했듯이 Angular 앱에는 `MsalInterceptor` 클래스를 사용하는 것이 좋습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`acquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `loginRedirect` 또는 `loginPopup` 메서드가 처음으로 실행되면 이후 호출에서는 주로 `acquireTokenSilent` 메서드를 사용하여 보호된 리소스에 액세스하는 데 필요한 토큰을 가져옵니다. 요청 또는 갱신 토큰에 대한 호출은 자동으로 수행됩니다.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

해당 코드에서 `scopes`는 API에 대한 액세스 토큰에서 반환되도록 요청되는 범위를 포함합니다.

다음은 그 예입니다.

* Microsoft Graph의 `["user.read"]`
* 사용자 지정 웹 API의 `["<Application ID URL>/scope"]`(즉, `api://<Application ID>/access_as_user`)

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

사용자가 Microsoft ID 플랫폼 엔드포인트와 상호 작용해야 하는 경우가 가끔 있습니다. 다음은 그 예입니다.

* 암호가 만료되었으므로 사용자가 자격 증명을 다시 입력해야 할 수도 있습니다.
* 애플리케이션이 사용자 동의가 필요한 추가 리소스 범위에 대한 액세스를 요청하고 있습니다.
* 2단계 인증이 필요합니다.

대부분의 애플리케이션에 권장되는 패턴은 먼저 `acquireTokenSilent`를 호출하고 예외를 catch한 다음, `acquireTokenPopup`(또는 `acquireTokenRedirect`)을 호출하여 대화형 요청을 시작하는 것입니다.

`acquireTokenPopup`을 호출하면 팝업 로그인 창이 나타납니다. 또는 `acquireTokenRedirect`가 사용자를 Microsoft ID 플랫폼 엔드포인트로 리디렉션합니다. 이 창에서 사용자는 자격 증명을 확인하거나, 필요한 리소스에 동의하거나, 2단계 인증을 완료해야 합니다.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> 이 빠른 시작에서는 Internet Explorer의 팝업 창 처리와 관련하여 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) 때문에 Microsoft Internet Explorer에서 `loginRedirect` 및 `acquireTokenRedirect` 메서드를 사용합니다.

## <a name="log-out"></a>로그아웃

사용자를 로그아웃하는 다음 코드를 추가합니다.

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>UI 추가
Angular 재질 구성 요소 라이브러리를 사용하여 UI를 추가하는 방법에 대한 예제는 [샘플 애플리케이션](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)을 참조하세요.

## <a name="test-your-code"></a>코드 테스트

1.  애플리케이션 폴더의 명령줄 프롬프트에서 다음 명령을 실행하여 포트를 수신 대기하도록 웹 서버를 시작합니다.

    ```bash
    npm install
    npm start
    ```
1. 브라우저에서 **http://localhost:4200** 또는 **http://localhost:{port}** 를 입력합니다. 여기서 *port*는 웹 서버에서 수신 대기하는 포트입니다.


### <a name="provide-consent-for-application-access"></a>애플리케이션 액세스에 대한 동의 제공

애플리케이션에 처음으로 로그인하면 프로필에 대한 액세스 권한을 부여하고 사용자를 로그인할 수 있도록 허용하라는 메시지가 표시됩니다.

!["사용 권한 요청됨" 창](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>범위 및 위임된 권한 추가

Microsoft Graph API는 *user.read* 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 기본적으로 이 범위는 등록 포털에서 등록된 모든 애플리케이션에 자동으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 Microsoft Graph API에는 사용자 일정을 나열하기 위해 *Calendars.Read* 범위가 필요합니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 *Calendars.Read* 위임 권한을 추가합니다. 그런 다음 `acquireTokenSilent` 호출에 *Calendars.Read* 범위를 추가합니다.

>[!NOTE]
>범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

백 엔드 API에 범위가 필요하지 않은 경우(추천되지 않음) 토큰을 획득하기 위한 호출에서 *clientId*를 범위로 사용할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

ID 및 액세스 관리를 처음 접하는 경우 [인증 및 권한 부여](authentication-vs-authorization.md)를 시작으로 최신 인증 개념을 배우는 데 도움이 되는 몇 가지 문서가 있습니다.

Microsoft ID 플랫폼에서 단일 페이지 애플리케이션 개발에 대해 자세히 알아보려면 일련의 다중 파트 [시나리오: 단일 페이지 애플리케이션](scenario-spa-overview.md) 문서를 통해 시작하면 됩니다.