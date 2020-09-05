---
title: '자습서: ASP.NET Core에서 App Configuration 동적 구성 사용'
titleSuffix: Azure App Configuration
description: 이 자습서에서는 ASP.NET Core 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봄
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: lcozzens
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 217c564a6bdb340ec15262c1eaf54a75bbffc833
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585018"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>자습서: ASP.NET Core 앱에서 동적 구성 사용

ASP.NET Core에는 다양한 원본에서 구성 데이터를 읽을 수 있는 플러그형 구성 시스템이 있습니다. 애플리케이션을 다시 시작하지 않고 변경 내용을 동적으로 처리할 수 있습니다. ASP.NET Core는 강력한 형식의 .NET 클래스에 대한 구성 설정 바인딩을 지원합니다. 다양한 `IOptions<T>` 패턴을 사용하여 코드에 삽입합니다. 기본 데이터가 변경되면 특히 이러한 패턴 중 하나인 `IOptionsSnapshot<T>`는 애플리케이션의 구성을 자동으로 다시 로드합니다. 애플리케이션의 컨트롤러에 `IOptionsSnapshot<T>`을 삽입하여 Azure App Configuration에 저장된 최신 구성에 액세스할 수 있습니다.

미들웨어를 사용하여 구성 설정 세트를 동적으로 새로 고치도록 App Configuration ASP.NET Core 클라이언트 라이브러리를 설정할 수도 있습니다. 웹앱에서 요청을 받는 동안에는 구성 설정이 매번 구성 저장소로 업데이트됩니다.

App Configuration은 구성 저장소를 너무 많이 호출하지 않도록 각 설정을 자동으로 캐시합니다. 구성 저장소에서 값이 변경되는 경우에도 새로 고침 작업은 설정의 캐시된 값이 만료될 때까지 기다린 후에 해당 설정을 업데이트합니다. 기본 캐시 만료 시간은 30초입니다. 필요한 경우 이 만료 시간을 재정의할 수 있습니다.

이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * App Configuration 저장소의 변경에 따라 해당 구성을 업데이트하도록 애플리케이션을 설정합니다.
> * 애플리케이션의 컨트롤러에 최신 구성을 삽입합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 수행하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료합니다.

## <a name="add-a-sentinel-key"></a>Sentinel 키 추가

*Sentinel 키*는 구성이 변경될 때 신호를 보내는 데 사용되는 특수 키입니다. 앱은 Sentinel 키에 대한 변경을 모니터링합니다. 변경이 검색되면 모든 구성 값을 새로 고칩니다. 이 방법은 모든 키의 변경을 모니터링하는 것과 비교하여 앱의 App Configuration에 대한 전체 요청 수를 줄입니다.

1. Azure Portal에서 **구성 탐색기 > 만들기 > 키-값**을 차례로 선택합니다.

1. **키**에 대해 *TestApp:Settings:Sentinel*을 입력합니다. **값**에 대해 1을 입력합니다. **레이블** 및 **콘텐츠 형식**은 비워 둡니다.

1. **적용**을 선택합니다.

    > [!NOTE]
    > 센티널 키를 사용하지 않는 경우 감시하려는 모든 키를 수동으로 등록해야 합니다.

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드

1. 다음 명령을 실행하여 `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet 패키지에 대한 참조를 추가합니다.

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. *Program.cs*를 열고, `CreateWebHostBuilder` 메서드를 업데이트하여 `config.AddAzureAppConfiguration()` 메서드를 추가합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                                {
                                    refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                           .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                });
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
                    config.AddAzureAppConfiguration(options =>
                    {
                        options.Connect(settings["ConnectionStrings:AppConfig"])
                               .ConfigureRefresh(refresh =>
                                    {
                                        refresh.Register("TestApp:Settings:Sentinel", refreshAll: true)
                                               .SetCacheExpiration(new TimeSpan(0, 5, 0));
                                    });
                    });
                })
            .UseStartup<Startup>());
    ```
    ---

    `ConfigureRefresh` 메서드는 새로 고침 작업이 트리거될 때 App Configuration 저장소로 구성 데이터를 업데이트하는 데 사용되는 설정을 지정하는 데 사용됩니다. `Register` 메서드에 대한 `refreshAll` 매개 변수는 Sentinel 키가 변경되면 모든 구성 값을 새로 고쳐야 함을 나타냅니다.

    또한 `SetCacheExpiration` 메서드는 기본 캐시 만료 시간(30초)을 재정의하여 5분으로 대신 지정합니다. 이렇게 하면 App Configuration에 대한 요청 수가 줄어듭니다.

    > [!NOTE]
    > 테스트를 위해 캐시 만료 시간을 줄이려고 할 수도 있습니다.

    새로 고침 작업을 실제로 트리거하려면 변경이 발생할 때 애플리케이션에서 구성 데이터를 새로 고치도록 새로 고침 미들웨어를 구성해야 합니다. 이후 단계에서 이를 수행하는 방법을 살펴볼 수 있습니다.

2. 새 `Settings` 클래스를 정의하고 구현하는 *Settings.cs* 파일을 추가합니다.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. *Startup.cs*를 열고 `ConfigureServices` 메서드에서 `IServiceCollection.Configure<T>`를 사용하여 구성 데이터를 `Settings` 클래스에 바인딩합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));
        services.AddControllersWithViews();
    }
    ```
    ---

    > [!TIP]
    > 구성 값을 읽을 때 옵션 패턴에 대해 자세히 알아보려면  [ASP.NET Core의 옵션 패턴](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options?view=aspnetcore-3.1)을 참조하세요.

4. `Configure` 메서드를 업데이트하여 ASP.NET Core 웹앱이 요청을 계속 받는 동안 새로 고침을 위해 등록된 구성 설정을 업데이트할 수 있도록 `UseAzureAppConfiguration` 미들웨어를 추가합니다.


    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        app.UseMvc();
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                app.UseExceptionHandler("/Home/Error");
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            // Add the following line:
            app.UseAzureAppConfiguration();

            app.UseHttpsRedirection();
            
            app.UseStaticFiles();

            app.UseRouting();

            app.UseAuthorization();

            app.UseEndpoints(endpoints =>
            {
                endpoints.MapControllerRoute(
                    name: "default",
                    pattern: "{controller=Home}/{action=Index}/{id?}");
            });
    }
    ```
    ---
    
    미들웨어는 `Program.cs`의 `AddAzureAppConfiguration` 메서드에 지정된 새로 고침 구성을 사용하여 ASP.NET Core 웹앱에서 받은 각 요청에 대해 새로 고침을 트리거합니다. 각 요청에 대해 새로 고침 작업이 트리거되고 클라이언트 라이브러리에서 등록된 구성 설정의 캐시된 값이 만료되었는지 확인합니다. 만료되면 새로 고쳐집니다.

## <a name="use-the-latest-configuration-data"></a>최신 구성 데이터 사용

1. 컨트롤러 디렉터리에서 *HomeController.cs*를 열고, `Microsoft.Extensions.Options` 패키지의 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 종속성 주입을 통해 `Settings`를 받도록 `HomeController` 클래스를 업데이트하고 해당 값을 사용합니다.

    #### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        private readonly ILogger<HomeController> _logger;

        public HomeController(ILogger<HomeController> logger, IOptionsSnapshot<Settings> settings)
        {
            _logger = logger;
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }

        // ...
    }
    ```
    ---



3. 보기 > 홈 디렉터리에서 *Index.cshtml*을 열고, 해당 콘텐츠를 다음 스크립트로 바꿉니다.

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"]px;
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

```console
        dotnet build
```

1. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

```console
        dotnet run
```

1. 브라우저 창을 열고, `dotnet run` 출력에 표시된 URL로 이동합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local-before.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택하고, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기**를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | 이제 라이브 업데이트를 사용하여 Azure App Configuration 데이터 업데이트! |
    | TestApp:Settings:Sentinel | 2 |

1. 새 구성 설정을 확인하려면 브라우저 페이지를 새로 고칩니다. 변경 내용을 반영하려면 두 번 이상 새로 고쳐야 할 수 있습니다.

    ![로컬로 업데이트된 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local-after.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 ASP.NET Core 웹앱을 사용하도록 설정하여 App Configuration에서 구성 설정을 동적으로 새로 고칩니다. Azure 관리 ID를 사용하여 App Configuration에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
