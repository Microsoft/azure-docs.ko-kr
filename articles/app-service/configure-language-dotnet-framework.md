---
title: ASP.NET apps 구성
description: Azure App Service에서 ASP.NET 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 30fddaec9ca5d0439beadedf7c5ca6b6c7d51d83
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961706"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Azure App Service에 대 한 ASP.NET 앱 구성

> [!NOTE]
> ASP.NET Core [Azure App Service에 대 한 ASP.NET Core 앱 구성](configure-language-dotnetcore.md) 을 참조 하세요.

ASP.NET apps를 컴파일된 이진 파일로 Azure App Service에 배포 해야 합니다. Visual Studio 게시 도구는 솔루션을 빌드한 다음 컴파일된 이진 파일을 직접 배포 하는 반면, App Service 배포 엔진은 코드 리포지토리를 먼저 배포한 다음 이진 파일을 컴파일합니다.

이 가이드에서는 ASP.NET 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [ASP.NET 퀵 스타트](quickstart-dotnet-framework.md) 및 [ASP.NET with SQL Database 자습서](app-service-web-tutorial-dotnet-sqldatabase.md) 를 따르세요.

## <a name="show-supported-net-framework-runtime-versions"></a>지원 되는 .NET Framework 런타임 버전 표시

App Service Windows 인스턴스에는 지원 되는 모든 .NET Framework 버전이 이미 설치 되어 있습니다. 사용할 수 있는 .NET Framework 런타임 및 SDK 버전을 표시 하려면 `https://<app-name>.scm.azurewebsites.net/DebugConsole` 브라우저 기반 콘솔에서 적절 한 명령을 찾아 실행 합니다.

CLR 4 런타임 버전 (.NET Framework 4 이상)의 경우:

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

최신 .NET Framework 버전을 즉시 사용할 수 없는 경우도 있습니다.

CLR 2 런타임 버전의 경우 (.NET Framework 3.5 및 아래):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>현재 .NET Framework 런타임 버전 표시

[Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

값은 `v4.0` 최신 CLR 4 버전 (.NET Framework 4.x)이 사용 됨을 의미 합니다. 값은 `v2.0` CLR 2 버전 (.NET Framework 3.5)이 사용 됨을 의미 합니다.

## <a name="set-net-framework-runtime-version"></a>.NET Framework 런타임 버전 설정

기본적으로 App Service은 지원 되는 최신 .NET Framework 버전을 사용 하 여 ASP.NET 앱을 실행 합니다. 대신 .NET Framework 3.5를 사용 하 여 앱을 실행 하려면 [Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 합니다 (v 2.0은 CLR 2를 나타냅니다).

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서 앱 코드 외부에서 [앱 설정](configure-common.md#configure-app-settings) 및 연결 문자열을 설정할 수 있습니다. 그런 다음 표준 ASP.NET 패턴을 사용 하 여 모든 클래스에서 액세스할 수 있습니다.

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

App Service 및 *web.config*에서 동일한 이름을 사용 하 여 앱 설정을 구성 하는 경우 App Service 값이 *web.config* 값 보다 우선적으로 적용 됩니다. 로컬 *web.config* 값을 사용 하면 응용 프로그램을 로컬로 디버그할 수 있지만 App Service 값을 사용 하면 프로덕션 설정으로 제품에서 앱을 실행할 수 있습니다. 연결 문자열은 동일한 방식으로 작동 합니다. 이러한 방식으로 코드를 변경 하지 않고 응용 프로그램 비밀을 코드 리포지토리 외부에 유지 하 고 적절 한 값에 액세스할 수 있습니다.

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

Visual Studio 솔루션에 여러 프로젝트가 포함 된 경우 Visual Studio 게시 프로세스에는 배포할 프로젝트 선택이 이미 포함 되어 있습니다. 빌드 자동화가 설정 된 상태에서 Git 또는 ZIP deploy를 사용 하 여 App Service 배포 엔진에 배포 하는 경우 App Service 배포 엔진은 App Service 앱으로 찾은 첫 번째 웹 사이트 또는 웹 응용 프로그램 프로젝트를 선택 합니다. 앱 설정을 지정 하 여 사용 해야 하는 프로젝트 App Service 지정할 수 있습니다 `PROJECT` . 예를 들어 [Cloud Shell](https://shell.azure.com)에서 다음을 실행 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>자세한 예외 페이지 가져오기

ASP.NET 앱이 Visual Studio 디버거에서 예외를 생성 하는 경우 브라우저는 자세한 예외 페이지를 표시 하지만이 페이지는 일반 오류 메시지로 대체 App Service. App Service에서 자세한 예외 페이지를 표시 하려면 *Web.config* 파일을 열고 요소 아래에 요소를 추가 `<customErrors mode="Off"/>` `<system.web>` 합니다. 예를 들어:

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

업데이트 된 *Web.config*를 사용 하 여 앱을 다시 배포 합니다. 이제 동일한 자세한 예외 페이지가 표시 됩니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[진단을](/dotnet/api/system.diagnostics.trace)사용 하 여 응용 프로그램 코드에 진단 메시지를 추가할 수 있습니다. 예를 들면 다음과 같습니다. 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)