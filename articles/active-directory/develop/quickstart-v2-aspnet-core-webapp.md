---
title: ASP.NET Core 웹앱에 Microsoft로 로그인 추가 - Microsoft ID 플랫폼 | Azure
description: OpenID Connect를 사용하여 ASP.NET Core 웹앱에서 Microsoft 로그인을 구현하는 방법을 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c11f7daf68585d63d19fca282ef2f4a306303ac7
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160732"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>빠른 시작: ASP.NET Core 웹앱에 Microsoft로 로그인 추가

이 빠른 시작에서는 ASP.NET Core 웹앱이 모든 Azure AD(Azure Active Directory) 인스턴스에서 개인 계정(hotmail.com, outlook.com, 기타)과 회사 및 학교 계정에 로그인하는 방법을 배웁니다.

![이 빠른 시작에서 생성된 샘플 앱의 작동 방식 표시](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>빠른 시작 앱 등록 및 다운로드
> 빠른 시작 애플리케이션을 시작하는 옵션은 두 가지가 있습니다.
> * [기본] [옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [수동] [옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>옵션 1: 앱을 등록하고 자동 구성한 다음, 코드 샘플 다운로드
>
> 1. [Azure Portal - 앱 등록](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)으로 이동합니다.
> 1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
> 1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>옵션 2: 애플리케이션 및 코드 샘플을 등록하고 수동으로 구성
>
> #### <a name="step-1-register-your-application"></a>1단계: 애플리케이션 등록
> 애플리케이션을 등록하고 앱의 등록 정보를 솔루션에 수동으로 추가하려면 다음 단계를 따르세요.
>
> 1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
> 1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
> 1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
> 1. **새 등록**을 선택합니다.
> 1. **애플리케이션 등록** 페이지가 표시되면 애플리케이션의 등록 정보를 입력합니다.
>    - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `AspNetCore-Quickstart`)을 입력합니다.
>    - **리디렉션 URI**에 `https://localhost:44321/`을 추가하고 **등록**을 선택합니다.
> 1. **인증** 메뉴를 선택한 후 다음 정보를 추가합니다.
>    - **리디렉션 URI**에 `https://localhost:44321/signin-oidc`를 추가하고 **저장**을 선택합니다.
>    - **고급 설정** 섹션에서 **로그아웃 URL**을 `https://localhost:44321/signout-oidc`으로 설정합니다.
>    - **암시적 권한 부여**에서 **ID 토큰**을 선택합니다.
>    - **저장**을 선택합니다.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 이 빠른 시작용 코드 샘플이 작동하려면 회신 URL을 `https://localhost:44321/` 및 `https://localhost:44321/signin-oidc`로 추가하고 로그아웃 URL을 `https://localhost:44321/signout-oidc`로 추가하며 권한 부여 엔드포인트에서 발행할 ID 토큰을 요청해야 합니다.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [자동 변경]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.

#### <a name="step-2-download-your-aspnet-core-project"></a>2단계: ASP.NET Core 프로젝트 다운로드

- [Visual Studio 2019 솔루션 다운로드](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore2-2.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3단계: Visual Studio 프로젝트 구성

1. Zip 파일을 루트 폴더 안의 로컬 폴더(예: **C:\Azure-Samples**)로 추출합니다.
1. Visual Studio 2019를 사용하는 경우 Visual Studio에서 솔루션을 엽니다(선택 사항).
1. **appsettings.json** 파일을 편집합니다. `ClientId`를 찾아 등록한 애플리케이션의 **애플리케이션(클라이언트) ID** 값으로 `ClientId` 값을 업데이트합니다. 

    ```json
    "ClientId": "Enter_the_Application_Id_here"
    "TenantId": "Enter_the_Tenant_Info_Here"
    ```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > 이 빠른 시작에서는 Enter_the_Supported_Account_Info_Here를 지원합니다.

> [!div renderon="docs"]
> 위치:
> - `Enter_the_Application_Id_here` - Azure Portal에 등록된 애플리케이션의 **애플리케이션(클라이언트) ID**입니다. 앱의 **개요** 페이지에서 **애플리케이션(클라이언트) ID**를 찾을 수 있습니다.
> - `Enter_the_Tenant_Info_Here` - 다음 옵션 중 하나입니다.
>   - 애플리케이션이 **이 조직 디렉터리의 계정만** 지원하는 경우 이 값을 **테넌트 ID** 또는 **테넌트 이름**(예: contoso.microsoft.com)으로 바꿉니다.
>   - 애플리케이션이 **모든 조직 디렉터리의 계정**을 지원하는 경우 이 값을 `organizations`로 바꾸세요.
>   - 애플리케이션이 **모든 Microsoft 계정 사용자**를 지원하는 경우 이 값을 `common`으로 바꾸세요.
>
> > [!TIP]
> > **애플리케이션(클라이언트) ID**, **디렉터리(테넌트) ID** 및 **지원되는 계정 유형**의 값을 찾아보려면 Azure Portal에서 앱의 **개요** 페이지로 이동합니다.

## <a name="more-information"></a>자세한 정보

이 섹션에서는 로그인 사용자에 필요한 코드에 대한 개요를 제공합니다. 이 개요는 코드가 작동하는 방식과 기본 인수를 이해하려는 경우뿐만 아니라 기존 ASP.NET Core 애플리케이션에 로그인을 추가하려는 경우에도 유용할 수 있습니다.

### <a name="startup-class"></a>시작 클래스

*Microsoft.AspNetCore.Authentication* 미들웨어는 호스팅 프로세스가 초기화될 때 실행되는 시작 클래스를 사용합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
  services.Configure<CookiePolicyOptions>(options =>
  {
    // This lambda determines whether user consent for non-essential cookies is needed for a given request.
    options.CheckConsentNeeded = context => true;
    options.MinimumSameSitePolicy = SameSiteMode.None;
  });

  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
          .AddAzureAD(options => Configuration.Bind("AzureAd", options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
    options.Authority = options.Authority + "/v2.0/";         // Microsoft identity platform

    options.TokenValidationParameters.ValidateIssuer = false; // accept several tenants (here simplified)
  });

  services.AddMvc(options =>
  {
     var policy = new AuthorizationPolicyBuilder()
                     .RequireAuthenticatedUser()
                     .Build();
     options.Filters.Add(new AuthorizeFilter(policy));
  })
  .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
}
```

`AddAuthentication` 메서드는 브라우저 시나리오에 사용되는 쿠키 기반 인증을 추가하고 OpenID Connect에 대한 챌린지를 설정하도록 서비스를 구성합니다. 

`.AddAzureAd`가 포함된 줄은 애플리케이션에 Microsoft ID 플랫폼 인증을 추가합니다. 그런 다음, Microsoft ID 플랫폼 엔드포인트를 사용하여 로그인하도록 구성됩니다.

> |Where  |  |
> |---------|---------|
> | clientid  | Azure Portal에 등록된 애플리케이션의 애플리케이션(클라이언트) ID입니다. |
> | Authority | 사용자가 인증하는 STS 엔드포인트. 일반적으로 퍼블릭 클라우드의 경우 <https://login.microsoftonline.com/{tenant}/v2.0>입니다. 여기서 {tenant}는 테넌트 이름, 테넌트 ID, 또는 공통 엔드포인트(다중 테넌트 애플리케이션에 사용)에 대한 참조인 경우 *common*입니다. |
> | TokenValidationParameters | 토큰 유효성 검사에 대한 매개 변수 목록입니다. 이 경우 `ValidateIssuer`는 개인, 회사 또는 학교 계정의 로그인을 수락할 수 있음을 나타내기 위해 `false`로 설정됩니다. |


> [!NOTE]
> 이 빠른 시작을 간단하게 수행할 수 있도록 `ValidateIssuer = false`로 설정했습니다. 실제 애플리케이션에서는 발급자의 유효성을 검사해야 합니다.
> 자세한 방법은 샘플을 참조하세요.

### <a name="protect-a-controller-or-a-controllers-method"></a>컨트롤러 또는 컨트롤러 메서드 보호

`[Authorize]` 특성을 사용하여 컨트롤러 또는 컨트롤러 메서드를 보호할 수 있습니다. 이 특성은 인증된 사용자만 허용하여 컨트롤러에 대한 액세스를 제한합니다. 즉, 사용자가 인증되지 않은 경우 인증 질문을 시작하여 컨트롤러에 액세스할 수 있습니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>다음 단계

새 ASP.NET Core 웹 애플리케이션에 인증을 추가하고, Microsoft Graph 및 다른 Microsoft API를 호출하고, 사용자 고유의 API를 호출하고, 권한 부여를 추가하고, 국가별 클라우드에서 또는 소셜 ID를 사용하여 사용자를 로그인하는 방법에 대한 지침을 포함하여 이 ASP.NET Core 자습서에 대한 자세한 내용은 GitHub 리포지토리를 확인하세요.

> [!div class="nextstepaction"]
> [ASP.NET Core 웹앱 자습서](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)

Microsoft ID 플랫폼을 개선할 수 있도록 도와주세요. 간단한 두 가지 설문 조사를 완료하여 의견을 알려주세요.

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)