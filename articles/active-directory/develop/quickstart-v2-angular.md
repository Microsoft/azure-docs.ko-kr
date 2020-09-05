---
title: Angular 단일 페이지 앱에서 사용자 로그인 - Azure
titleSuffix: Microsoft identity platform
description: Angular 앱이 Microsoft ID 플랫폼을 사용하여 액세스 토큰이 필요한 API를 호출하는 방법을 알아봅니다.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-javascript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: bf921ce4ce3ad65f0787ccab2f0a064da8973af1
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88115240"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>빠른 시작: Angular 단일 페이지 애플리케이션에서 사용자 로그인 및 액세스 토큰 가져오기

이 빠른 시작에서는 코드 샘플을 사용하여 Angular SPA(단일 페이지 애플리케이션)에서 개인 Microsoft 계정, 회사 계정 또는 학교 계정을 갖고 있는 사용자를 로그인하는 방법에 대해 알아봅니다. 또한 Angular SPA는 Microsoft Graph API 또는 웹 API를 호출하는 액세스 토큰을 가져올 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 동작합니다. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* 프로젝트 파일 편집에 사용할 [Visual Studio Code](https://code.visualstudio.com/download) 또는 프로젝트를 실행할 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> 빠른 시작 앱을 시작하려면 다음 옵션 중 하나를 사용합니다.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>옵션 1(기본): 앱을 등록하고 자동으로 구성한 다음, 코드 샘플을 다운로드합니다.
>
> 1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
> 1. 계정에서 두 개 이상의 테넌트에 액세스할 수 있는 경우 오른쪽 위에 있는 계정을 선택한 다음, 사용할 Azure AD(Azure Active Directory) 테넌트로 포털 세션을 설정합니다.
> 1. Azure Portal에서 새 [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) 창을 엽니다.
> 1. 애플리케이션 이름을 입력한 다음, **등록**을 선택합니다.
> 1. 빠른 시작 창으로 이동하여 Angular 빠른 시작을 확인합니다. 지침에 따라 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>옵션 2(수동): 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-the-application"></a>1단계: 애플리케이션 등록
>
> 1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
> 1. 계정에서 두 개 이상의 테넌트에 액세스할 수 있는 경우 오른쪽 위에서 계정을 선택한 다음, 사용할 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 지침에 따라 Azure Portal에서 [단일 페이지 애플리케이션을 등록](./scenario-spa-app-registration.md)합니다.
> 1. 앱 등록의 **인증** 창에서 새 플랫폼을 추가하고 리디렉션 URI(`http://localhost:4200/`)를 등록합니다.
> 1. 이 빠른 시작에서는 [암시적 권한 부여 흐름](v2-oauth2-implicit-grant-flow.md)을 사용합니다. **ID 토큰** 및 **액세스 토큰**에 대한 **암시적 권한 부여** 설정을 선택합니다. 이 앱은 사용자를 로그인하고 API를 호출하므로 ID 토큰 및 액세스 토큰이 필요합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작의 코드 샘플이 작동하려면 리디렉션 URI를 **http://localhost:4200/** 으로 추가하고 **암시적 허용**을 사용하도록 설정해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [이러한 변경 내용 적용]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-javascript/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-the-code-sample"></a>2단계: 코드 샘플 다운로드
>[!div renderon="docs"]
>Node.js를 사용하여 웹 서버에서 프로젝트를 실행하려면 [샘플 리포지토리를 복제](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)하거나 [주요 프로젝트 파일을 다운로드](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)합니다. Visual Studio Code와 같은 편집기를 사용하여 파일을 엽니다.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [코드 샘플 다운로드](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>3단계: JavaScript 앱 구성
>
> *src/app* 폴더에서 *app.module.ts* 파일을 편집하고 `MsalModule.forRoot` 아래에서 `clientId` 및 `authority` 값을 설정합니다.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> 다음 값을 바꿉니다.
>
>|값 이름|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|이 값은 애플리케이션 등록의 **개요** 페이지에서 **애플리케이션(클라이언트) ID** 값입니다. |
>|Enter_the_Cloud_Instance_Id_Here|Azure 클라우드의 인스턴스입니다. 기본 또는 글로벌 Azure 클라우드의 경우 **https://login.microsoftonline.com** 을 입력합니다. 국가별 클라우드(예: 중국)의 경우 [국가별 클라우드](./authentication-national-cloud.md)를 참조하세요.|
>|Enter_the_Tenant_Info_Here| 다음 옵션 중 하나로 설정합니다. 애플리케이션이 *이 조직 디렉터리의 계정*을 지원하는 경우 이 값을 디렉터리(테넌트) ID 또는 테넌트 이름(예: **contoso.microsoft.com**)으로 바꿉니다. 애플리케이션이 *모든 조직 디렉터리의 계정*을 지원하는 경우 이 값을 **organizations**으로 바꿉니다. 애플리케이션에서 *모든 조직 디렉터리의 계정 및 개인 Microsoft 계정*을 지원하는 경우 이 값을 **common**으로 바꿉니다. *개인 Microsoft 계정만* 지원하도록 제한하려면 이 값을 **consumers**로 바꿉니다. |
>|Enter_the_Redirect_Uri_Here|**http://localhost:4200** 으로 바꿉니다.|
>|cacheLocation  | (선택 사항) 인증 상태에 대한 브라우저 스토리지를 설정합니다. 기본값은 **sessionStorage**입니다.   |
>|storeAuthStateInCookie  | (선택 사항) 인증 요청 상태를 저장하는 라이브러리를 식별합니다. 이 상태는 브라우저 쿠키에서 인증 흐름의 유효성을 검사하는 데 필요합니다. 이 쿠키는 두 브라우저를 수용하기 위해 Internet Explorer와 Edge용으로 설정되어 있습니다. 자세한 내용은 [알려진 문제](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)를 참조하세요. |
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **지원되는 계정 유형**의 값을 찾아보려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.

사용할 수 있는 구성 가능한 옵션에 대한 자세한 내용은 [클라이언트 애플리케이션 초기화](msal-js-initializing-client-applications.md)를 참조하세요.

GitHub의 [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) 리포지토리에서 MSAL 라이브러리의 소스 코드를 찾을 수 있습니다.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>3단계: 프로젝트 실행

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4단계: 프로젝트 실행

Node.js를 사용하는 경우:

1. 프로젝트 디렉터리에서 다음 명령을 실행하여 서버를 시작합니다.

   ```console
   npm install
   npm start
   ```

1. **http://localhost:4200/** 으로 이동합니다.
1. **로그인**을 선택합니다.
1. **프로필**을 선택하여 Microsoft Graph를 호출합니다.

브라우저가 애플리케이션을 로드한 후에는 **로그인**을 선택합니다. 처음 로그인할 때 애플리케이션이 사용자 프로필에 액세스하고 로그인할 수 있도록 동의하라는 메시지가 표시됩니다. 성공적으로 로그인한 후 **프로필**을 선택하면 사용자 프로필 정보가 페이지에 표시됩니다.

## <a name="how-the-sample-works"></a>샘플 작동 방법

![이 빠른 시작에서 샘플 앱의 작동 방식을 보여주는 다이어그램](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>다음 단계

다음으로, Angular 자습서에서 사용자를 로그인하고 토큰을 획득하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Angular 자습서](./tutorial-v2-angular.md)