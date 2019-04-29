---
title: ASP.NET Core 앱-Azure App Service 구성 | Microsoft Docs
description: Azure App Service에서 작동 하도록 ASP.NET Core 앱을 구성 하는 방법 알아보기
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: e203877b2bc939c1d7fb9390df39f3e2451d12d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852315"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Linux를 구성 합니다. Azure App Service 용 ASP.NET Core 앱

컴파일된 이진 파일을 ASP.NET Core 앱을 배포 되어야 합니다. Visual Studio 게시 도구 솔루션 빌드하고 배포 컴파일된 이진 파일을 직접 App Service 배포 엔진 코드 리포지토리를 처음 배포 하 고 이진 파일을 컴파일합니다.

이 가이드 개발자가 기본 제공 Linux 컨테이너를 사용 하 여 App Service에서 ASP.NET Core에 대 한 주요 개념을 제공 합니다. Azure App Service를 사용한 경험이 없는 경우에 따라 합니다 [ASP.NET Core 빠른 시작](quickstart-dotnetcore.md) 하 고 [ASP.NET Core 및 SQL Database 자습서](tutorial-dotnetcore-sqldb-app.md) 첫 번째입니다.

## <a name="show-net-core-version"></a>.NET Core 버전 표시

현재.NET Core 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원 되는 모든.NET Core 버전을 표시 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core 버전 설정

다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com) .NET Core 버전 2.1을로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings)을 지정할 수 있습니다. 그런 다음 표준 ASP.NET 패턴을 사용 하 여 액세스할 수 있습니다.

```csharp
include Microsoft.Extensions.Configuration;
// retrieve App Service app setting
System.Configuration.ConfigurationManager.AppSettings["MySetting"]
// retrieve App Service connection string
Configuration.GetConnectionString("MyDbConnection")
```

App Service와 동일한 이름의 앱 설정을 구성 하는 경우 *Web.config*, App Service 값 Web.config 값 보다 우선 합니다. 로컬에서 앱을 디버그 Web.config 값 수 있지만 App Service 값 프로덕션 설정 사용 하 여 제품에서 앱 실행 수 있습니다. 연결 문자열을 동일한 방식으로 작동 합니다. 이러한 방식으로 코드 리포지토리 외부에서 응용 프로그램에서 비밀을 유지를 코드 변경 없이 적절 한 값에 액세스 합니다.

## <a name="get-detailed-exceptions-page"></a>자세한 예외 페이지

Visual Studio 디버거에서 예외를 생성 하는 ASP.NET 앱, 브라우저 자세한 예외 페이지를 표시 하지만 App Service에서 해당 페이지는 제네릭 바뀝니다 **HTTP 500** 오류 또는 **오류가 발생 하는 동안 오류가 발생 했습니다. 요청을 처리 합니다.** 메시지가 표시됩니다. App Service에서 자세한 예외 페이지를 표시할 추가 합니다 `ASPNETCORE_ENVIRONMENT` 앱 설정에서 다음 명령을 실행 하 여 앱에는 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 사용자 앱 논리를 아는 경우 사용자는 암호화를 요청 하는 경우 구성에 전달 된 헤더 미들웨어 *Startup.cs*:

- 사용 하 여 미들웨어를 구성 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 전달할 합니다 `X-Forwarded-For` 하 고 `X-Forwarded-Proto` 헤더 `Startup.ConfigureServices`.
- 미들웨어는 앱 서비스 부하 분산 장치를 신뢰할 수 있도록 알려진된 네트워크에 개인 IP 주소 범위를 추가 합니다.
- 호출 된 [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 에서 메서드 `Startup.Configure` 다른 미들웨어를 호출 하기 전에 합니다.

코드 세 요소 모두에 함께 배치 된 다음 예제와 비슷합니다.

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

자세한 내용은 [부하 분산 장치 및 프록시 서버를 사용 하 여 사용 하도록 ASP.NET Core 구성](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)합니다.

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

ASP.NET 리포지토리를 사용 하 여 배포 엔진에 배포 되는 경우는 *.csproj* 엔진 루트 디렉터리에서 파일은 프로젝트를 배포 합니다. ASP.NET 리포지토리를 사용 하 여를 배포 하는 경우는 *.sln* 첫 번째 웹 사이트 또는 웹 응용 프로그램 프로젝트를 App Service 앱으로 발견 엔진 루트 디렉터리에 파일을 선택 합니다. 프로젝트를 선택할 필요가 엔진에 대 한 것 같습니다.

다중 프로젝트 솔루션을 배포 하려면 두 가지 방법으로 App Service에서 사용 하도록 프로젝트를 지정할 수 있습니다.

### <a name="using-deployment-file"></a>.Deployment 파일을 사용 하 여

추가 된 *.deployment* 리포지토리 루트에 파일을 다음 코드를 추가 합니다.

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>앱 설정을 사용 하 여

에 <a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>, 다음 CLI 명령을 실행 하 여 App Service 앱에 앱 설정을 추가 합니다. 바꿉니다  *\<앱 이름 >* 합니다  *\<리소스 그룹 이름 >*, 및  *\<프로젝트 이름 >* 적절 한 값을 사용 하 여 .

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: SQL Database를 사용하는 ASP.NET Core 앱](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)