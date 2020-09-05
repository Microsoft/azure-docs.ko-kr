---
title: ASP.NET Core 앱 구성
description: Azure App Service에서 네이티브 Windows 인스턴스 또는 미리 빌드된 Linux 컨테이너의 ASP.NET Core 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 3456adc2b143f1f51115183fe4873938d067d267
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961672"
---
# <a name="configure-an-aspnet-core-app-for-azure-app-service"></a>Azure App Service에 대 한 ASP.NET Core 앱 구성

> [!NOTE]
> .NET Framework의 ASP.NET [Azure App Service에 대 한 ASP.NET 앱 구성](configure-language-dotnet-framework.md) 을 참조 하세요.

ASP.NET Core 앱을 컴파일된 이진 파일로 Azure App Service에 배포 해야 합니다. Visual Studio 게시 도구는 솔루션을 빌드한 다음 컴파일된 이진 파일을 직접 배포 하는 반면, App Service 배포 엔진은 코드 리포지토리를 먼저 배포한 다음 이진 파일을 컴파일합니다.

이 가이드는 ASP.NET Core 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service를 사용한 적이 없는 경우 [ASP.NET Core 빠른](quickstart-dotnetcore.md) 시작 및 [ASP.NET Core SQL Database 자습서](tutorial-dotnetcore-sqldb-app.md) 를 먼저 수행 합니다.

::: zone pivot="platform-windows"  

## <a name="show-supported-net-core-runtime-versions"></a>지원 되는 .NET Core 런타임 버전 표시

App Service Windows 인스턴스에는 지원 되는 모든 .NET Core 버전이 이미 설치 되어 있습니다. 사용할 수 있는 .NET Core 런타임 및 SDK 버전을 표시 하려면로 이동 하 여 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 브라우저 기반 콘솔에서 다음 명령을 실행 합니다.

```azurecli-interactive
dotnet --info
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="show-net-core-version"></a>.NET Core 버전 표시

현재 .NET Core 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 .NET Core 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

::: zone-end

## <a name="set-net-core-version"></a>.NET Core 버전 설정

::: zone pivot="platform-windows"  

프로젝트 파일에서 ASP.NET Core 프로젝트에 대 한 대상 프레임 워크를 설정 합니다. 자세한 내용은 .NET Core 설명서에서 [사용할 .Net core 버전 선택](/dotnet/core/versions/selection) 을 참조 하세요.

::: zone-end

::: zone pivot="platform-linux"

[Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 .net Core 버전을 3.1로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|3.1"
```

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 설정된 상태에서 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 시퀀스를 통해 자동화 단계를 빌드합니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.
1. `dotnet restore`NuGet 종속성을 복원 하려면를 실행 합니다.
1. `dotnet publish`를 실행 하 여 프로덕션을 위한 이진 파일을 빌드합니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정된 경우 사용자 지정 스크립트를 실행합니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`는 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행하려면 `PRE_BUILD_COMMAND`를 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`를 정의합니다.

다음 예제에서는 일련의 명령에 대한 두 변수를 쉼표로 구분하여 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조하세요.

App Service를 실행 하 고 Linux에서 ASP.NET Core 앱을 빌드하는 방법에 대 한 자세한 내용은 [Oryx 설명서: .Net Core 앱이 검색 되 고 빌드되는 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)을 참조 하세요.

::: zone-end

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](configure-common.md#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 ASP.NET Core 종속성 주입 패턴을 사용 하 여 모든 클래스에서 액세스할 수 있습니다.

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve nested App Service app setting
            var myHierarchicalConfig = _configuration["My:Hierarchical:Config:Data"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

예를 들어 App Service 및 *appsettings.js*에서 동일한 이름을 사용 하 여 앱 설정을 구성 하는 경우 App Service 값이 *appsettings.js* 값 보다 우선 적용 됩니다. 로컬 *appsettings.json* value를 사용 하면 앱을 로컬로 디버그할 수 있지만 App Service 값을 사용 하면 프로덕션 설정으로 제품에서 앱을 실행할 수 있습니다. 연결 문자열은 동일한 방식으로 작동 합니다. 이러한 방식으로 코드를 변경 하지 않고 응용 프로그램 비밀을 코드 리포지토리 외부에 유지 하 고 적절 한 값에 액세스할 수 있습니다.

> [!NOTE]
> 참고 *appsettings.js* 의 [계층적 구성 데이터](/aspnet/core/fundamentals/configuration/#hierarchical-configuration-data) 는 `:` .net Core에 대 한 표준 구분 기호를 사용 하 여 액세스할 수 있습니다. App Service에서 특정 계층적 구성 설정을 재정의 하려면 앱 설정 이름을 키에서 동일 하 게 분리 된 형식으로 설정 합니다. [Cloud Shell](https://shell.azure.com)에서 다음 예제를 실행할 수 있습니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings My:Hierarchical:Config:Data="some value"
```

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

Visual Studio 솔루션에 여러 프로젝트가 포함 된 경우 Visual Studio 게시 프로세스에는 배포할 프로젝트 선택이 이미 포함 되어 있습니다. 빌드 자동화가 설정 된 상태에서 Git 또는 ZIP deploy를 사용 하 여 App Service 배포 엔진에 배포 하는 경우 App Service 배포 엔진은 App Service 앱으로 찾은 첫 번째 웹 사이트 또는 웹 응용 프로그램 프로젝트를 선택 합니다. 앱 설정을 지정 하 여 사용 해야 하는 프로젝트 App Service 지정할 수 있습니다 `PROJECT` . 예를 들어 [Cloud Shell](https://shell.azure.com)에서 다음을 실행 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

ASP.NET Core는 [App Service에 대 한 기본 제공 로깅 공급자](/aspnet/core/fundamentals/logging/#azure-app-service)를 제공 합니다. 프로젝트의 *Program.cs* 에서 `ConfigureLogging` 다음 예제와 같이 확장 메서드를 통해 응용 프로그램에 공급자를 추가 합니다.

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureLogging(logging =>
        {
            logging.AddAzureWebAppDiagnostics();
        })
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        });
```

그런 다음 [표준 .Net Core 패턴](/aspnet/core/fundamentals/logging)으로 로그를 구성 하 고 생성할 수 있습니다.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

App Service에서 ASP.NET Core 앱 문제를 해결 하는 방법에 대 한 자세한 내용은 [Azure App Service 및 IIS의 ASP.NET Core 문제 해결](/aspnet/core/test/troubleshoot-azure-iis) 을 참조 하세요.

## <a name="get-detailed-exceptions-page"></a>자세한 예외 페이지 가져오기

ASP.NET Core 앱이 Visual Studio 디버거에서 예외를 생성 하는 경우 브라우저는 자세한 예외 페이지를 표시 하지만 해당 페이지가 일반 **HTTP 500** 오류로 대체 되거나 **요청을 처리 하는 동안 오류가 발생** 한 App Service. 반환됩니다. App Service에서 자세한 예외 페이지를 표시 하려면 `ASPNETCORE_ENVIRONMENT` <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 실행 하 여 앱 설정을 앱에 추가 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 사용자 요청이 암호화 되었는지 여부를 응용 프로그램 논리에서 알고 있어야 하는 경우 *Startup.cs*에서 전달 된 헤더 미들웨어를 구성 합니다.

- `Startup.ConfigureServices`에서 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)를 사용하여 미들웨어를 구성합니다.
- 미들웨어가 App Service 부하 분산 장치를 신뢰할 수 있도록 개인 IP 주소 범위를 알려진 네트워크에 추가 합니다.
- 다른 미들웨어를 호출 하기 전에에서 [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 메서드를 호출 합니다 `Startup.Configure` .

세 요소를 모두 함께 배치 하면 코드는 다음 예제와 같습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](/aspnet/core/host-and-deploy/proxy-load-balancer)을 참조하세요.

::: zone pivot="platform-linux"

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: SQL Database를 사용하는 ASP.NET Core 앱](tutorial-dotnetcore-sqldb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [App Service Linux FAQ](faq-app-service-linux.md)

::: zone-end