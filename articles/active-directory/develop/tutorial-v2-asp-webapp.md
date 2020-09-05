---
title: Microsoft ID 플랫폼 ASP.NET 웹앱에 로그인 추가
titleSuffix: Microsoft identity platform
description: 기존 웹 브라우저 기반 애플리케이션 및 OpenID Connect 표준을 사용하여 ASP.NET 솔루션에서 Microsoft 로그인을 구현합니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 740d62136393cf0c9cf31d367735bffed1c05276
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165586"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Microsoft에 로그인을 ASP.NET 웹앱에 추가

이 가이드에서는 기존의 웹 브라우저 기반 애플리케이션과 OpenID Connect를 사용하여 ASP.NET MVC 솔루션을 통해 Microsoft에 로그인을 구현하는 방법을 보여 줍니다.

이 가이드를 완료하면 애플리케이션이 outlook.com 및 live.com의 좋아요에서 개인 계정의 로그인을 허용할 수 있습니다. 또한 Microsoft ID 플랫폼과 통합된 회사 또는 조직의 회사 및 학교 계정에서 앱에 로그인할 수 있습니다.

> 이 가이드에는 Microsoft Visual Studio 2019가 필요합니다.  이 프로그램이 아직 설치되어 있지 않나요?  [체험용 Visual Studio 2019를 다운로드](https://www.visualstudio.com/downloads/)합니다.

>[!NOTE]
> Microsoft ID 플랫폼을 처음 사용하는 경우 [ASP.NET 웹앱에 Microsoft ID 플랫폼 로그인 추가](quickstart-v2-aspnet-webapp.md)를 시작하는 것이 좋습니다.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

만든 샘플 애플리케이션은 브라우저를 사용하여 로그인 단추를 통해 인증하라는 메시지를 사용자에게 표시하는 ASP.NET 웹 사이트에 액세스하는 시나리오를 기반으로 합니다. 이 시나리오에서는 웹 페이지를 렌더링하는 작업의 대부분이 서버 쪽에서 발생합니다.

## <a name="libraries"></a>라이브러리

이 가이드에서는 다음 라이브러리를 사용합니다.

|라이브러리|Description|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|애플리케이션이 인증에 OpenIdConnect를 사용할 수 있게 해주는 미들웨어입니다.|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|애플리케이션에서 쿠키를 사용하여 사용자 세션을 유지 관리할 수 있도록 하는 미들웨어입니다.|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|OWIN 기반 애플리케이션에서 ASP.NET 요청 파이프라인을 사용하여 IIS(인터넷 정보 서비스)에서 실행될 수 있도록 하는 미들웨어입니다.|

## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 OpenID Connect를 사용하여 ASP.NET 프로젝트에서 OWIN 미들웨어를 통해 인증 파이프라인을 설치하고 구성하는 방법에 대해 설명합니다.

> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)하고, [애플리케이션 등록](#register-your-application)으로 건너뛰어 코드 샘플을 구성한 후에 실행합니다.

### <a name="create-your-aspnet-project"></a>ASP.NET 프로젝트 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**로 차례로 이동합니다.
2. **Visual C#\Web**에서 **ASP.NET 웹 애플리케이션(.NET Framework)** 을 선택합니다.
3. 애플리케이션의 이름을 지정하고 **확인**을 선택합니다.
4. **비어 있음**을 선택한 다음, 확인란을 선택하여 **MVC** 참조를 추가합니다.

## <a name="add-authentication-components"></a>인증 구성 요소 추가

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**로 차례로 이동합니다.
2. [패키지 관리자 콘솔] 창에 다음 내용을 입력하여 *OWIN 미들웨어 NuGet 패키지* 를 추가합니다.

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

### <a name="about-these-libraries"></a>이러한 라이브러리 정보
이러한 라이브러리는 쿠키 기반 인증을 통해 OpenID Connect를 사용하여 SSO(Single Sign-On)를 사용하도록 설정합니다. 인증이 완료되고 사용자를 나타내는 토큰이 애플리케이션으로 전송되면 OWIN 미들웨어가 세션 쿠키를 생성합니다. 그러면 브라우저가 이후 요청에서 이 쿠키를 사용하므로 사용자가 암호를 다시 입력할 필요가 없으며 추가 확인이 필요하지 않습니다.

## <a name="configure-the-authentication-pipeline"></a>인증 파이프라인 구성

다음 단계는 OpenID Connect 인증을 구성하는 OWIN 미들웨어 Startup 클래스를 만드는 데 사용됩니다. 이 클래스는 IIS 프로세스 시작 시 자동으로 실행됩니다.

> [!TIP]
> 프로젝트의 루트 폴더에 `Startup.cs` 파일이 없는 경우:
> 1. 마우스 오른쪽 단추로 프로젝트의 루트 폴더를 클릭한 다음, **추가** > **새 항목** > **OWIN Startup 클래스**를 차례로 선택합니다.<br/>
> 2. 이름을 **Startup.cs**로 지정합니다.
>
>> 선택한 클래스가 표준 C# 클래스가 아니라 OWIN Startup 클래스인지 확인합니다. 이를 확인하려면 네임스페이스 위에 [assembly: OwinStartup(typeof({NameSpace}.Startup))]가 표시되는지 확인합니다.

1. *OWIN* 및 *Microsoft.IdentityModel* 참조를 Startup.cs에 추가합니다.

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Startup 클래스를 다음 코드로 바꿉니다.

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect
        /// </summary>
        /// <param name="app"></param>
        public void Configuration(IAppBuilder app)
        {
            app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

            app.UseCookieAuthentication(new CookieAuthenticationOptions());
            app.UseOpenIdConnectAuthentication(
                new OpenIdConnectAuthenticationOptions
                {
                    // Sets the ClientId, authority, RedirectUri as obtained from web.config
                    ClientId = clientId,
                    Authority = authority,
                    RedirectUri = redirectUri,
                    // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
                    PostLogoutRedirectUri = redirectUri,
                    Scope = OpenIdConnectScope.OpenIdProfile,
                    // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                    ResponseType = OpenIdConnectResponseType.IdToken,
                    // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                    // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                    // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
                    TokenValidationParameters = new TokenValidationParameters()
                    {
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> 이 빠른 시작을 간단하게 수행할 수 있도록 `ValidateIssuer = false`로 설정했습니다. 실제 애플리케이션에서는 발급자의 유효성을 검사해야 합니다.
> 이 작업을 수행하는 방법을 알아보려면 샘플을 참조하세요.

### <a name="more-information"></a>자세한 정보

*OpenIDConnectAuthenticationOptions*에 제공하는 매개 변수는 애플리케이션이 Microsoft ID 플랫폼과 통신하기 위한 좌표로 사용됩니다. OpenID Connect 미들웨어는 백그라운드에서 쿠키를 사용하므로 이전 코드와 같이 쿠키 인증도 설정해야 합니다. *ValidateIssuer* 값은 OpenIdConnect에서 특정 조직에 대한 액세스를 제한하지 않도록 지시합니다.

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>로그인 및 로그아웃 요청을 처리하는 컨트롤러 추가

로그인 및 로그아웃 메서드를 공개하는 새 컨트롤러를 만들려면 다음 단계를 수행합니다.

1.  마우스 오른쪽 단추로 **Controllers** 폴더를 클릭하고, **추가** > **컨트롤러**를 차례로 선택합니다.
2.  **MVC(.NET 버전) 컨트롤러 - 비어 있음**을 선택합니다.
3.  **추가**를 선택합니다.
4.  이름을 **HomeController**로 지정한 다음, **추가**를 선택합니다.
5.  OWIN 참조를 클래스에 추가합니다.

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. 인증 질문을 시작하여 로그인 및 로그아웃을 처리하는 다음 두 가지 메서드를 컨트롤러에 추가합니다.

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }

    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>사용자 로그인을 위한 앱 홈페이지 만들기

Visual Studio에서 로그인 단추를 추가하고 인증 후 사용자 정보를 표시하는 새 보기를 만듭니다.

1.  **Views\Home** 폴더를 마우스 오른쪽 단추로 클릭하고 **보기 추가**를 선택합니다.
2.  새 보기 이름을 **Index**로 지정합니다.
3.  로그인 단추를 포함하는 다음 HTML을 파일에 추가합니다.

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

### <a name="more-information"></a>자세한 정보
이 페이지는 SVG 형식으로 검은색 배경의 로그인 단추를 추가합니다.<br/>![Microsoft로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> 추가 로그인 단추는 [브랜딩 지침](./howto-add-branding-in-azure-ad-apps.md "브랜딩 지침")으로 이동하세요.

## <a name="add-a-controller-to-display-users-claims"></a>사용자의 클레임을 표시하는 컨트롤러 추가
이 컨트롤러는 컨트롤러를 보호하는 `[Authorize]` 특성의 사용을 보여 줍니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러에 대한 액세스를 제한합니다. 다음 코드에서는 특성을 사용하여 로그인의 일부로 검색된 사용자 클레임을 표시합니다.

1.  마우스 오른쪽 단추로 **Controllers** 폴더를 클릭한 다음, **추가** > **컨트롤러**를 차례로 선택합니다.
2.  **MVC{버전} 컨트롤러 - 비어 있음**을 선택합니다.
3.  **추가**를 선택합니다.
4.  이름을 **ClaimsController**로 지정합니다.
5.  컨트롤러 클래스의 코드를 다음 코드로 바꿉니다. 그러면 `[Authorize]` 특성이 클래스에 추가됩니다.

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;

            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;

            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;

            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;

            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;

            return View();
        }
    }
    ```

### <a name="more-information"></a>자세한 정보
`[Authorize]` 특성을 사용하므로 이 컨트롤러의 모든 메서드는 사용자가 인증된 경우에만 실행할 수 있습니다. 인증되지 않은 사용자가 컨트롤러에 액세스하려고 하면 OWIN에서 인증 질문을 시작하고 사용자에게 인증하도록 강제합니다. 위의 코드는 사용자의 ID 토큰에 포함된 특정 사용자 특성에 대한 클레임 목록을 살펴봅니다. 이러한 특성에는 사용자의 전체 이름과 사용자 이름 및 전역 사용자 식별자 주체가 포함됩니다. 사용자의 조직에 대한 ID를 나타내는 *테넌트 ID*도 포함됩니다.

## <a name="create-a-view-to-display-the-users-claims"></a>사용자의 클레임을 표시하는 보기 만들기

Visual Studio에서 새 보기를 만들어 사용자의 클레임을 웹 페이지에 표시합니다.

1.  마우스 오른쪽 단추로 **Views\Claims** 폴더를 클릭한 다음, **보기 추가**를 선택합니다.
2.  새 보기 이름을 **Index**로 지정합니다.
3.  다음 HTML을 파일에 추가합니다.

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>애플리케이션 등록

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하는 두 가지 옵션이 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

애플리케이션을 빠르게 등록하려면 다음 단계를 수행합니다.

1. 새 [Azure Portal - 앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) 창으로 이동합니다.
1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
1. 지침에 따라 한 번의 클릭으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.

1. Visual Studio를 열고 다음을 수행합니다.
   1. 솔루션 탐색기에서 프로젝트를 선택하고, [속성] 창을 봅니다([속성] 창이 표시되지 않으면 F4 키를 누름).
   1. SSL 사용을 `True`로 변경합니다.
   1. Visual Studio에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **속성**, **웹** 탭을 차례로 선택합니다. **서버** 섹션에서 **프로젝트 URL** 설정을 **SSL URL**로 변경합니다.
   1. SSL URL을 복사합니다. 다음 단계에 나오는 등록 포털의 리디렉션 URL 목록에서 이 URL을 리디렉션 URL 목록에 추가합니다.<br/><br/>![프로젝트 속성](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. 회사 또는 학교 계정을 사용하거나 개인 Microsoft 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 계정에서 둘 이상의 테넌트에 액세스할 수 있는 경우 오른쪽 위 모서리에서 계정을 선택하고 포털 세션을 원하는 Azure AD 테넌트로 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
   1. **이름** 섹션에서 앱의 사용자에게 표시될 의미 있는 애플리케이션 이름(예: **ASPNET-Tutorial**)을 입력합니다.
   1. 1단계에서 Visual Studio로부터 복사한 SSL URL(예: `https://localhost:44368/`)을 **회신 URL**에 추가하고, **등록**을 선택합니다.
1. **인증** 메뉴를 선택하고, **암시적 허용** 아래에서 **ID 토큰**을 선택한 다음, **저장**을 선택합니다.
1. 루트 폴더에 있는 web.config 파일의 `configuration\appSettings` 섹션에 다음을 추가합니다.

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. `ClientId`를 방금 등록한 애플리케이션 ID로 바꿉니다.
1. `redirectUri`를 프로젝트의 SSL URL로 바꿉니다.

## <a name="test-your-code"></a>코드 테스트

Visual Studio에서 애플리케이션을 테스트하려면 F5 키를 눌러 프로젝트를 실행합니다. 브라우저가 http://<span></span>localhost:{port} 위치에서 열립니다. 그러면 **Microsoft에 로그인** 단추가 표시됩니다. 단추를 선택하여 로그인 프로세스를 시작합니다.

테스트를 실행할 준비가 되면 Azure AD 계정(회사 또는 학교 계정) 또는 개인 Microsoft 계정(<span>live.</span>com 또는 <span>outlook.</span>com)을 사용하여 로그인합니다.

![Microsoft로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft 계정으로 로그인](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Microsoft ID 플랫폼 엔드포인트의 권한 및 동의

Microsoft ID 플랫폼과 통합되는 애플리케이션은 사용자와 관리자가 데이터 액세스 방법을 제어할 수 있는 권한 부여 모델을 따릅니다. 사용자가 이 애플리케이션에 액세스하기 위해 Microsoft ID 플랫폼을 사용하여 인증하면 애플리케이션에서 요청한 권한("기본 프로필 보기" 및 "사용자가 액세스 권한을 부여한 데이터에 대한 액세스 유지")에 동의하라는 메시지가 표시됩니다. 이러한 권한에 동의한 사용자는 애플리케이션 결과를 계속 진행할 수 있습니다. 그러나 다음 중 하나가 발생하면 사용자에게 **관리자 동의 필요** 페이지가 대신 표시될 수 있습니다.

- 애플리케이션 개발자가 **관리자 동의**가 필요한 추가 권한을 추가합니다.
- 또는 사용자가 자신을 대신하여 회사 데이터에 액세스하는 앱에 동의할 수 없는 테넌트가 **엔터프라이즈 애플리케이션 -> 사용자 설정**에 구성되어 있습니다.

자세한 내용은 [Microsoft ID 플랫폼 엔드포인트의 권한 및 동의](./v2-permissions-and-consent.md)를 참조하세요.

### <a name="view-application-results"></a>애플리케이션 결과 보기

로그인한 후 사용자는 웹 사이트의 홈 페이지로 리디렉션됩니다. 홈페이지는 Microsoft 애플리케이션 등록 포털의 애플리케이션 등록 정보에 지정된 HTTPS URL입니다. 홈페이지에는 *"Hello \<user>"* 시작 메시지, 로그아웃하기 위한 링크 및 사용자의 클레임을 보기 위한 링크가 포함되어 있습니다. 사용자의 클레임에 대한 링크는 앞에서 만든 Claims 컨트롤러에 연결됩니다.

### <a name="view-the-users-claims"></a>사용자의 클레임 보기

사용자의 클레임을 보려면 링크를 선택하여 인증된 사용자만 사용할 수 있는 컨트롤러 보기로 이동합니다.

#### <a name="view-the-claims-results"></a>클레임 결과 보기

컨트롤러 뷰로 이동 후 사용자를 위한 기본 속성이 담긴 테이블을 확인해야 합니다.

|속성 |값 |Description |
|---|---|---|
|**이름** |사용자의 전체 이름 | 사용자의 이름과 성
|**사용자 이름** |user<span>@domain.com</span> | 사용자를 식별하는 데 사용되는 사용자 이름|
|**Subject** |제목 |웹에서 사용자를 고유하게 식별하는 문자열|
|**테넌트 ID** |Guid | 사용자의 Azure AD 조직을 고유하게 나타내는 **guid**|

또한 인증 요청에 있는 모든 클레임의 테이블이 표시됩니다. 자세한 내용은 [ID 토큰에 있는 클레임 목록](./id-tokens.md)을 참조하세요.

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 특성이 있는 메서드 액세스 테스트(선택 사항)

`Authorize` 특성으로 보호되는 컨트롤러에 익명 사용자로 액세스하는 것을 테스트하려면 다음 단계를 수행합니다.

1. 사용자를 로그아웃하는 링크를 선택하고, 로그아웃 프로세스를 완료합니다.
2. 브라우저에서 http://<span></span>localhost:{port}/claims를 입력하여 `Authorize` 특성으로 보호되는 컨트롤러에 액세스합니다.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>보호된 컨트롤러에 액세스한 후 예상된 결과

보호된 컨트롤러 뷰를 사용하기 위해 인증을 묻는 메시지가 나타납니다.

## <a name="advanced-options"></a>고급 옵션

### <a name="protect-your-entire-website"></a>전체 웹 사이트 보호

전체 웹 사이트를 보호하려면 **Global.asax** 파일에서 `Application_Start` 메서드의 `GlobalFilters` 필터에 `AuthorizeAttribute` 특성을 추가합니다.

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```

### <a name="restrict-who-can-sign-in-to-your-application"></a>애플리케이션에 로그인할 수 있는 사용자 제한

기본적으로 이 가이드에서 만든 애플리케이션을 빌드하면 애플리케이션에서 개인 계정(outlook.com, live.com 등 포함) 및 Microsoft ID 플랫폼과 통합된 모든 회사 또는 조직의 회사 및 학교 계정의 로그인을 수락합니다. SaaS 애플리케이션에는 이 옵션을 권장합니다.

애플리케이션에 대한 사용자 로그인 액세스를 제한하기 위해 여러 가지 옵션을 사용할 수 있습니다.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>옵션 1: 한 조직의 Active Directory 인스턴스에 속한 사용자만 애플리케이션에 로그인하도록 제한(단일 테넌트)

이 옵션은 *LOB 애플리케이션*에 자주 사용됩니다. 애플리케이션에서 특정 Azure AD 인스턴스에 속한 계정의 로그인만 수락하도록 하려면(해당 인스턴스의 *게스트 계정* 포함) 다음 단계를 수행합니다.

1. web.config 파일에서 `Tenant` 매개 변수의 값을 `Common`에서 조직의 테넌트 이름(예: `contoso.onmicrosoft.com`)으로 변경합니다.
2. [OWIN 스타트업 클래스](#configure-the-authentication-pipeline)에서 `ValidateIssuer` 인수를 `true`로 설정합니다.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>옵션 2: 특정 목록의 사용자에 대한 액세스 제한

로그인 액세스를 허용된 조직의 목록에 있는 Azure AD 조직의 사용자 계정으로만 제한할 수 있습니다.
1. [OWIN 스타트업 클래스](#configure-the-authentication-pipeline)에서 `ValidateIssuer` 인수를 `true`로 설정합니다.
2. `ValidIssuers` 매개 변수의 값을 허용된 조직 목록으로 설정합니다.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>옵션 3: 사용자 지정 메서드를 사용하여 발급자 유효성 검사

**IssuerValidator** 매개 변수를 사용하여 발급자의 유효성을 검사하는 사용자 지정 메서드를 구현할 수 있습니다. 이 매개 변수를 사용하는 방법에 관한 자세한 내용은 [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters) 클래스를 참조하세요.

## <a name="next-steps"></a>다음 단계

웹앱에서 웹 API를 호출하는 방법을 알아봅니다.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>이 빠른 시작에서 사용되는 애플리케이션을 만드는 방법 알아보기

Microsoft ID 플랫폼을 사용하여 웹앱에서 웹 API를 호출하는 방법을 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [앱 API를 호출하는 웹앱](scenario-web-app-sign-user-overview.md)

Microsoft Graph를 호출하는 웹앱을 빌드하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET 자습서](/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]