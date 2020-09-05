---
title: ASP.NET Core에 기능 플래그를 추가하기 위한 빠른 시작
description: ASP.NET Core 앱에 기능 플래그를 추가하고 Azure App Configuration을 사용하여 관리
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: lcozzens
ms.openlocfilehash: 12b66dc173a8d3f93f97fb369ce03533299a65d7
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235267"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>빠른 시작: ASP.NET Core 앱에 기능 플래그를 추가합니다.

이 빠른 시작에서는 Azure App Configuration을 사용하여 ASP.NET Core 애플리케이션에서 기능 관리의 엔드투엔드 구현을 만듭니다. App Configuration 서비스를 사용하여 중앙에서 모든 기능 플래그를 저장하고 상태를 제어합니다. 

.NET Core 기능 관리 라이브러리는 포괄적인 기능 플래그 지원을 통해 프레임워크를 확장합니다. 이 라이브러리는 .NET Core 구성 시스템을 기반으로 빌드됩니다. 또한 해당 .NET Core 구성 공급자를 통해 App Configuration과 원활하게 통합됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **기능 관리자** >  **+추가**를 선택하여 `Beta`라는 기능 플래그를 추가합니다.

    > [!div class="mx-imgBorder"]
    > ![Beta라는 기능 플래그 사용](media/add-beta-feature-flag.png)

    지금은 `label`을 정의하지 않은 상태로 둡니다. **적용**을 선택하여 새 기능 플래그를 저장합니다.

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

[.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다. Visual Studio 대신 .NET Core CLI를 사용할 때 얻을 수 있는 장점은 Windows, macOS 및 Linux 플랫폼에서 .NET Core CLI를 사용할 수 있다는 것입니다.

1. 프로젝트에 대한 새 폴더를 만듭니다. 이 빠른 시작에서는 이 폴더 이름을 *TestFeatureFlags*로 지정합니다.

1. 새 폴더에서 다음 명령을 실행하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다.

   ```    
   dotnet new mvc --no-https
   ```

## <a name="add-secret-manager"></a>비밀 관리자 추가

비밀 관리자를 사용하려면 *.csproj* 파일에 `UserSecretsId` 요소를 추가합니다.

1. *.csproj* 파일을 엽니다.

1.  여기에 표시된 대로 `UserSecretsId` 요소를 추가합니다. 같은 GUID를 사용해도 되고, 이 값을 원하는 값으로 바꿔도 됩니다.

    > [!IMPORTANT]
    > .NET Core 3.0에서 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다.  사용자 환경에 따라 올바른 구문을 선택합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>

    </Project>
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>

    </Project>
    ```
    ---

1. *.csproj* 파일을 저장합니다.

암호 관리자 도구는 개발 작업에 대한 중요한 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드 내에서 앱 암호를 실수로 공유하는 경우를 방지할 수 있습니다.

> [!TIP]
> 비밀 관리자에 대한 자세한 내용은 [ASP.NET Core에서 개발 중인 앱 비밀 보안 스토리지](https://docs.microsoft.com/aspnet/core/security/app-secrets)를 참조하세요.

## <a name="connect-to-an-app-configuration-store"></a>App Configuration 저장소에 연결

1. 다음 명령을 실행하여 `Microsoft.Azure.AppConfiguration.AspNetCore` 및 `Microsoft.FeatureManagement.AspNetCore` NuGet 패키지에 대한 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

    ```dotnetcli
    dotnet restore
    ```

1. **ConnectionStrings:AppConfig**라는 비밀을 비밀 관리자에 추가합니다.

    이 비밀에는 App Configuration 저장소에 액세스하기 위한 연결 문자열이 포함되어 있습니다. 다음 명령의 `<your_connection_string>` 값을 App Configuration 저장소의 연결 문자열로 바꿉니다. 기본 읽기 전용 키 연결 문자열은 Azure Portal의 **액세스 키**에서 찾을 수 있습니다.

    이 명령은 *.csproj* 파일이 있는 동일한 디렉터리에서 실행해야 합니다.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    비밀 관리자는 웹앱을 로컬로 테스트하는 용도로만 사용됩니다. 예를 들어 [Azure App Service](https://azure.microsoft.com/services/app-service)에 앱을 배포할 때 비밀 관리자를 사용하여 연결 문자열을 저장하는 대신 **Connection Strings**라는 애플리케이션 설정을 사용합니다.

    App Configuration API를 사용하여 이 비밀에 액세스할 수 있습니다. 콜론(:)은 지원되는 모든 플랫폼에서 App Configuration API를 통해 구성 이름에서 작동합니다. [환경별 구성](https://docs.microsoft.com/aspnet/core/fundamentals/configuration)을 참조하세요.

1. *Program.cs*에서 `config.AddAzureAppConfiguration()` 메서드를 호출하여 App Configuration을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다.

    > [!IMPORTANT]
    > .NET Core 3.0에서 `CreateHostBuilder`는 `CreateWebHostBuilder`를 대체합니다.  사용자 환경에 따라 올바른 구문을 선택합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                        .UseFeatureFlags();
                });
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(options => {
                options.Connect(settings["ConnectionStrings:AppConfig"])
                    .UseFeatureFlags();
            });
        })
        .UseStartup<Startup>());
    ```
    ---

1. *Startup.cs*를 열고 .NET Core 기능 관리자에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. `services.AddFeatureManagement()` 메서드를 호출하여 기능 플래그 지원을 추가하도록 `ConfigureServices` 메서드를 업데이트합니다. 필요에 따라 `services.AddFeatureFilter<FilterType>()`을 호출하여 기능 플래그에 사용할 필터를 포함할 수 있습니다 :

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)
    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);        
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }

    ---

1. Update the `Configure` method to add a middleware to allow the feature flag values to be refreshed at a recurring interval while the ASP.NET Core web app continues to receive requests.

    #### [.NET Core 2.x](#tab/core2x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }

            app.UseStaticFiles();
            app.UseCookiePolicy();
            app.UseAzureAppConfiguration();
            app.UseMvc(routes =>
            {
                routes.MapRoute(
                    name: "default",
                    template: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)
    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
            }
            app.UseStaticFiles();
            app.UseRouting();
            app.UseAuthorization();
            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
            app.UseAzureAppConfiguration();
    }
    ```
    ---

1. *MyFeatureFlags.cs* 파일을 추가합니다.

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. *BetaController.cs*를 *Controllers* 디렉터리에 추가합니다.

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager)
            {
                _featureManager = featureManager;
            }

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index()
            {
                return View();
            }
        }
    }
    ```

1. *Views* 디렉터리에서 *_ViewImports.cshtml*을 열고, 다음과 같이 기능 관리자 태그 도우미를 추가합니다.

    ```html
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

1. *Views*\\*Shared* 디렉터리에서 *_Layout.cshtml*을 열고, `<body>` > `<header>` 아래의 `<nav>` 바코드를 다음 코드로 바꿉니다.

    ```html
    <nav class="navbar navbar-expand-sm navbar-toggleable-sm navbar-light bg-white border-bottom box-shadow mb-3">
        <div class="container">
            <a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">TestFeatureFlags</a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target=".navbar-collapse" aria-controls="navbarSupportedContent"
            aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="navbar-collapse collapse d-sm-inline-flex flex-sm-row-reverse">
                <ul class="navbar-nav flex-grow-1">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Index">Home</a>
                    </li>
                    <feature name="Beta">
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Beta" asp-action="Index">Beta</a>
                    </li>
                    </feature>
                    <li class="nav-item">
                        <a class="nav-link text-dark" asp-area="" asp-controller="Home" asp-action="Privacy">Privacy</a>
                    </li>
                </ul>
            </div>
        </div>
    </nav>
    ```

1. *Views* 아래에 *Beta* 디렉터리를 만들고 이 디렉터리에 *Index.cshtml*을 추가합니다.

    ```html
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>
        This is the beta website.
    </h1>
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

    ```
    dotnet build
    ```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

    ```
    dotnet run
    ```

1. 브라우저 창을 열고, 로컬로 호스팅되는 웹앱에 대한 기본 URL인 `https://localhost:5000`으로 이동합니다.
    Azure Cloud Shell에서 작업하는 경우 *웹 미리 보기* 단추와 *구성*을 차례로 선택합니다.  메시지가 표시되면 포트 5000을 선택합니다.

    ![웹 미리 보기 단추 찾기](./media/quickstarts/cloud-shell-web-preview.png)

    브라우저에는 아래 이미지와 비슷한 페이지가 표시되어야 합니다.
    ![로컬로 앱 실행 빠른 시작](./media/quickstarts/aspnet-core-feature-flag-local-before.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **기능 관리자**를 선택하고, **Beta** 키의 상태를 **On**으로 변경합니다.

1. 명령 프롬프트로 돌아가서 `Ctrl-C`를 눌러 실행 중인 `dotnet` 프로세스를 취소합니다.  `dotnet run`을 사용하여 애플리케이션을 다시 시작합니다.

1. 새 구성 설정을 확인하려면 브라우저 페이지를 새로 고칩니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 App Configuration 저장소를 만든 후 [기능 관리 라이브러리](https://go.microsoft.com/fwlink/?linkid=2074664)를 통해 ASP.NET Core 웹앱에서 기능을 관리하는 데 사용했습니다.

- [기능 관리](./concept-feature-management.md)에 대한 자세한 정보
- [기능 플래그 관리](./manage-feature-flags.md)
- [ASP.NET Core 앱에서 기능 플래그 사용](./use-feature-flags-dotnet-core.md)
- [ASP.NET Core 앱에서 동적 구성 사용](./enable-dynamic-configuration-aspnet-core.md)
