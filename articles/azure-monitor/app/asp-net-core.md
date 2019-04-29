---
title: ASP.NET Core용 Azure Application Insights | Microsoft Docs
description: ASP.NET Core 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: ae0d3658d9ae8534b1596fa7363495926cd0dfe7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693912"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.NET Core용 Application Insights

Azure Application Insights는 코드 수준까지 웹 애플리케이션의 심층 모니터링을 제공합니다. 웹 애플리케이션의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 사용자가 보고할 때까지 기다리지 않고 애플리케이션의 오류를 빠르게 식별하고 진단할 수 있습니다.

이 문서에서는 Visual Studio에서 샘플 ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) 애플리케이션을 만드는 과정을 설명합니다. 또한 Azure Application Insights를 사용하여 모니터링을 시작하는 방법도 보여줍니다.

## <a name="prerequisites"></a>필수 조건

- .NET Core 2.0.0 SDK 이상
- ASP.NET 및 웹 개발 워크로드가 있는 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 버전 15.7.3 이상입니다.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Visual Studio에서 ASP.NET Core 프로젝트 만들기

1. **Visual Studio 2017**를 마우스 오른쪽 단추로 클릭한 다음, **관리자 권한으로 실행**을 선택합니다.
2. **파일** > **새로 만들기** > **프로젝트**를 선택합니다(Ctrl+Shift+N).

   ![Visual Studio 새 프로젝트 메뉴 스크린샷](./media/asp-net-core/001-new-project.png)

3. **Visual C#** 을 확장합니다. **.NET Core** > **ASP.NET Core**를 선택합니다. 프로젝트 이름 및 솔루션 이름을 입력한 다음, **새 Git 리포지토리 만들기**를 선택합니다.

   ![Visual Studio 새 프로젝트 마법사의 스크린샷](./media/asp-net-core/002-asp-net-core-web-application.png)

4. **.NET Core** > **ASP.NET Core 2.0** **웹 애플리케이션** > **확인** 을 선택합니다.

    ![Visual Studio 새 프로젝트 템플릿 선택 스크린샷](./media/asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights 검색

ASP.NET Core 2+ 기반 프로젝트가 있는 Visual Studio 2015 업데이트 2 이상에서는 명시적으로 Application Insights를 사용자의 프로젝트에 추가하기 전이라도 [Application Insights 검색](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)을 활용할 수 있습니다.

이 기능을 테스트하려면

1. 앱을 실행합니다. 앱을 실행하려면 **IIS Express** 아이콘(![Visual Studio IIS Express 아이콘 스크린 샷](./media/asp-net-core/004-iis-express.png))을 선택합니다.

2. **보기** > **다른 Windows** > **Application Insights 검색**을 선택합니다.

   ![Visual Studio 진단 도구 선택 스크린샷](./media/asp-net-core/005-view-other-windows-search.png)

3. 현재 디버그 세션 원격 분석은 로컬 분석에 대해서만 사용할 수 있습니다. Application Insights를 완전히 사용하려면 상단 오른쪽 모서리에서 **원격 분석 준비**를 선택하거나 다음 섹션에 나열된 단계를 완료합니다.

   ![Visual Studio Application Insights 검색 스크린샷](./media/asp-net-core/006-search.png)

> [!NOTE]
> Application Insights를 ASP.NET Core 프로젝트에 추가하기 전에 [Application Insights 검색](../../azure-monitor/app/visual-studio.md) 및 [CodeLens](../../azure-monitor/app/visual-studio-codelens.md) 같은 기능을 Visual Studio에서 켜는 방법에 대해 자세히 알려면 [Application Insights 검색 계속](#application-insights-search-continued)을 참조하세요.

## <a name="add-application-insights-telemetry"></a>Application Insights 원격 분석 추가

1. **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다. (또는 **연결된 서비스**를 마우스 오른쪽 단추로 클릭한 다음, **연결된 서비스 추가**를 선택합니다.)

    ![Visual Studio 새 프로젝트 선택 메뉴 스크린샷](./media/asp-net-core/007-project-add-telemetry.png)

2. **시작**을 선택합니다. (Visual Studio의 버전에 따라 텍스트는 약간 달라질 수 있습니다. 일부 이전 버전에는 대신 **평가판 시작** 단추가 있습니다.)

    ![Application Insights 시작하기 단추 스크린샷](./media/asp-net-core/008-get-started.png)

3. 구독을 선택한 다음, **리소스** > **등록**을 선택합니다.

## <a name="changes-made-to-your-project"></a>프로젝트 변경 내용

Application Insights는 오버헤드가 낮습니다. Application Insights 원격 분석을 추가하여 프로젝트에 대한 변경 내용을 검토하려면

**보기** > **팀 탐색기**(Ctrl +\, Ctrl+M) > **프로젝트** > **변경**을 선택합니다.

- 총 4개의 변경 내용이 표시됩니다.

  ![Application Insights를 추가하여 변경된 파일 스크린샷](./media/asp-net-core/009-changes.png)

- 다음과 같은 1개의 새 파일이 만들어집니다.

  - _ConnectedService.json_

    ```json
    {
     "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
     "Version": "8.12.10405.1",
     "GettingStartedDocument": {
       "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
     }
    }
    ```

- 다음과 같은 3개 파일이 수정됩니다(변경 내용을 강조 표시하기 위해 추가된 추가 설명).

  - _appsettings.json_:

    ```json
    {
      "Logging": {
        "IncludeScopes": false,
        "LogLevel": {
          "Default": "Warning"
        }
      },
    // Changes to file post adding Application Insights Telemetry:
      "ApplicationInsights": {
        "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
      }
    }
    //
    ```

  - _ContosoDotNetCore.csproj_:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
      <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
        <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
    <!---->
      </PropertyGroup>
      <ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
    <!---->
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
      </ItemGroup>
      <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
      </ItemGroup>
    <!--Changes to file post adding Application Insights Telemetry:-->
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    <!---->
    </Project>
    ```

  -  _Program.cs_:

      ```csharp
      using System;
      using System.Collections.Generic;
      using System.IO;
      using System.Linq;
      using System.Threading.Tasks;
      using Microsoft.AspNetCore;
      using Microsoft.AspNetCore.Hosting;
      using Microsoft.Extensions.Configuration;
      using Microsoft.Extensions.Logging;

      namespace DotNetCore
      {
          public class Program
          {
              public static void Main(string[] args)
              {
                  BuildWebHost(args).Run();
              }

              public static IWebHost BuildWebHost(string[] args) =>
                  WebHost.CreateDefaultBuilder(args)
      // Change to file post adding Application Insights Telemetry:
                      .UseApplicationInsights()
      //
                      .UseStartup<Startup>()
                      .Build();
          }
      }
      ```

## <a name="send-ilogger-logs-to-application-insights"></a>Application Insights ILogger 로그 보내기

Application Insights ILogger를 통해 전송 된 캡처 로그를 지원 합니다. 로깅 체크 아웃 코드 샘플을 설치 하려면 [여기](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)합니다.

## <a name="synthetic-transactions-with-powershell"></a>PowerShell 사용한 가상 트랜잭션

가상 트랜잭션을 사용하여 앱에 대한 요청을 자동화하려면

1. 앱을 실행하려면 다음을 선택합니다 ![Visual Studio IIS Express 아이콘 스크린샷](./media/asp-net-core/004-iis-express.png) 아이콘.

2. 브라우저 주소 표시줄에서 URL을 복사합니다. URL의 형식은 `http://localhost:<port number>`과 같습니다.

   ![주소 표시줄의 브라우저 URL 스크린샷](./media/asp-net-core/0013-copy-url.png)

3. 테스트 앱을 사용하여 100개의 가상 트랜잭션을 만들려면 다음 PowerShell 루프를 실행합니다. `localhost:` 다음의 포트 번호가 이전 단계에서 복사한 URI와 일치하도록 수정합니다. 예를 들면 다음과 같습니다.

   ```powershell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Application Insights 포털 열기

이전 섹션에서 PowerShell 명령을 실행한 후 Application Insights를 열어 트랜잭션을 보고 데이터가 수집되고 있는지 확인합니다. 

Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다.

   ![Application Insights 개요 스크린샷](./media/asp-net-core/010-portal.png)

> [!NOTE]
> 이전 예제 스크린샷에서 **라이브 스트림**, **페이지 보기 로드 시간** 및 **실패한 요청**은 수집되지 않습니다. 다음 섹션에서는 이러한 항목 각각을 추가하기 위한 단계를 안내합니다. **라이브 스트림** 및 **페이지 보기 로드 시간**을 이미 수집하고 있는 경우 **실패한 요청**에 대한 단계만 완료합니다.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>실패한 요청, 라이브 스트림 및 페이지 보기 로드 시간 수집

### <a name="failed-requests"></a>실패한 요청

기술적으로 실패한 요청이 수집되지만 아직 실패한 요청이 발생하지는 않았습니다. 이 프로세스를 가속화하기 위해 기존 프로젝트에 실제 예외를 시뮬레이션하는 사용자 지정 예외를 추가할 수 있습니다. Visual Studio에서 앱이 여전히 실행되고 있는 경우 계속하기 전에 **디버깅 중지**(Shift+F5)를 선택합니다.

1. **솔루션 탐색기**에서 **페이지** > **About.cshtml**을 확장한 다음, *About.cshtml.cs*를 엽니다.

   ![Visual Studio 솔루션 탐색기 스크린샷](./media/asp-net-core/011-about.png)

2. ``Message=`` 아래에 예외를 추가한 다음, 파일에 변경 내용을 저장합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>라이브 스트림

ASP.NET Core 업데이트를 사용하여 Application Insights의 라이브 스트림 기능에 액세스하려면 Microsoft.ApplicationInsights.AspNetCore 2.2.0 NuGet 패키지를 업데이트합니다.

Visual Studio에서 **프로젝트** > **NuGet 패키지 관리** > **Microsoft.ApplicationInsights.AspNetCore** > (버전) **2.2.0** > **업데이트**를 선택합니다.

  ![NuGet 패키지 관리자 스크린샷](./media/asp-net-core/012-nuget-update.png)

여러 확인 메시지 표시 변경 내용에 동의하는 경우 읽고 수락하세요.

### <a name="page-view-load-time"></a>페이지 보기 로드 시간

1. Visual Studio에서 **솔루션 탐색기** > **페이지**로 이동합니다. 두 개의 파일 *Layout.cshtml* 및 *ViewImports.cshtml*을 수정해야 합니다.

2. *ViewImports.cshtml*에서 이 코드를 추가합니다.

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. *Layout.cshtml*에서 ``</head>`` 태그 및 다른 스크립트 앞에 다음 코드를 추가합니다.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>실패한 요청, 페이지 보기 로드 시간 및 라이브 스트림 테스트

모든 것이 작동하는지 테스트하고 확인하려면

1. 앱을 실행합니다. 앱을 실행하려면 다음을 선택합니다 ![Visual Studio IIS Express 아이콘 스크린샷](./media/asp-net-core/004-iis-express.png) 아이콘.

2. **정보** 페이지로 이동하여 테스트 예외를 트리거합니다. (디버그 모드인 경우 Application Insights에 예외가 표시되게 하려면 Visual Studio에서 **계속**을 선택합니다.)

3. 전에 사용한 시뮬레이션된 PowerShell 트랜잭션 스크립트를 다시 실행합니다. (스크립트에서 포트 번호를 조정해야 할 수 있습니다.)

4. Application Insights **개요** 페이지가 아직 열려 있지 않으면 Visual Studio 메뉴에서 **프로젝트** > **Application Insights** > **Application Insights 포털 열기**를 선택합니다. 

   > [!TIP]
   > 새 트래픽이 표시되지 않으면 **시간 범위**의 값을 확인한 다음, **새로 고침**을 선택합니다.

   ![개요 창 스크린샷](./media/asp-net-core/0019-overview-updated.png)

5. **라이브 스트림**을 선택합니다.

   ![라이브 메트릭 스트림 스크린샷](./media/asp-net-core/0020-live-metrics-stream.png)

   (PowerShell 스크립트가 여전히 실행 중이면 라이브 메트릭이 표시돼야 합니다. (PowerShell 스크립트가 실행을 중지한 경우 라이브 메트릭 스트림이 열린 상태에서 스크립트를 다시 실행합니다.)

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK 비교

Application Insights 제품 그룹은 [전체 .NET Framework SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)와 .Net Core SDK 간의 기능 패리티를 수행하기 위해 최선을 다하고 있습니다. Application Insights용 [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) 2.2.0 릴리스는 이러한 기능 간격을 많이 좁혔습니다.

다음 테이블에서는 [.NET 및 .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server) 간의 차이점 및 장단점을 자세히 설명합니다.

   | SDK 비교 | ASP.NET        | ASP.NET Core 2.1.0    | ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **라이브 메트릭**      | **+** |**-** | **+** |
   | **서버 원격 분석 채널** | **+** |**-** | **+**|
   |**적응 샘플링**| **+** | **-** | **+**|
   | **SQL 종속성 호출**     | **+** |**-** | **+**|
   | **성능 카운터*** | **+** | **-**| **-**|

이 컨텍스트의 성능 카운터는 프로세서, 메모리 및 디스크 사용률과 같은 [서버 쪽 성능 카운터](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters)를 나타냅니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK
[코드를 읽고 기여합니다](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Application Insights 검색 계속

이 섹션에서는 ASP.NET Core 2 프로젝트용 Visual Studio에서 Application Insights 검색이 작동하는 방법을 잘 이해할 수 있도록 도와줍니다. Application Insights NuGet 패키지를 아직 명시적으로 설치하지 않은 경우 이러한 방식으로 작동합니다. 디버그 출력을 검사하는 데 도움이 될 수도 있습니다.

출력에서 _인사이트_ 단어를 검색하는 경우 다음과 유사한 결과가 강조 표시됩니다.

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

출력에서 CoreCLR은 다음의 두 어셈블리를 로드합니다. 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

Application Insights 원격 분석의 각 인스턴스에서 _구성되지 않은_ 참조는 이 애플리케이션이 ikey와 연결되지 않았음을 나타냅니다. 앱이 실행되는 동안 생성되는 데이터는 Azure에 전송되지 않습니다. 이 데이터는 로컬 검색 및 분석에 대해서만 사용할 수 있습니다.

이 기능은 NuGet 패키지 _Microsoft.AspNetCore.All_이 종속성으로 [_Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)을 사용하므로 부분적으로 가능합니다.

![Microsoft.AspNETCore.all에 대한 NuGet 종속성 그래프의 스크린샷](./media/asp-net-core/013-dependency.png)

Visual Studio 외부에서 VSCode 또는 다른 편집기에서 ASP.NET Core 프로젝트를 편집 하는 경우 이러한 어셈블리 하지 않습니다. 자동으로 로드 디버그 하는 동안 프로젝트에 Application Insights를 명시적으로 추가 하지 않은 경우.

그러나 Visual Studio의 외부 어셈블리에서 로컬 Application Insights 기능의 점등은 [IHostingStartup 인터페이스](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1)를 사용하여 수행됩니다. 이 인터페이스는 디버그 동안 Application Insights를 동적으로 추가합니다.

[IHostingStartup을 사용한 ASP.NET Core의 외부 어셈블리](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1)에서 앱 향상 방법에 대해 자세히 알아봅니다. 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Visual Studio.NET Core 프로젝트에서 Application Insights를 사용하지 않도록 설정

Application Insights 검색 기능의 자동 점등이 유용할 수 있지만 예상치 못한 경우에 생성된 디버그 원격 분석의 확인은 혼동을 줄 수 있습니다.

원격 분석 생성을 사용하지 않는 것만으로 충분한 경우 _Startup.cs_ 파일의 **구성** 방법에 이 코드 블록을 추가할 수 있습니다.

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

CoreCLR은 _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ 및 _Microsoft.ApplicationInsights.AspNetCore.dll_을 로드하지만 이 파일은 아무 것도 수행하지 않습니다.

Visual Studio.NET Core 프로젝트에서 Application Insights를 완전히 사용하지 않도록 설정하려는 경우 선호되는 메서드는 **도구** > **옵션** >  **프로젝트 및 솔루션** > **웹 프로젝트**를 선택하는 것입니다. **ASP.Net Core 웹 프로젝트에 대해 로컬 Application Insights 사용 안 함** 확인 상자를 선택합니다. 이 기능은 Visual Studio 15.6에서 추가되었습니다.

![Visual Studio 옵션 창 웹 프로젝트 화면의 스크린샷](./media/asp-net-core/014-disable.png)

이전 버전의 Visual Studio를 실행하고, *IHostingStartup*을 통해 로드된 모든 어셈블리를 완전히 제거하려는 경우 다음 2개의 옵션이 있습니다.

* `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`을 _Program.cs_에 추가합니다.

  ```csharp
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Linq;
  using System.Threading.Tasks;
  using Microsoft.AspNetCore;
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.Configuration;
  using Microsoft.Extensions.Logging;

  namespace DotNetCore
  {
      public class Program
      {
          public static void Main(string[] args)
          {
              BuildWebHost(args).Run();
          }

          public static IWebHost BuildWebHost(string[] args) =>
              WebHost.CreateDefaultBuilder(args)
                  .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                  .UseStartup<Startup>()
                  .Build();
      }
  }
  ```

* ``"ASPNETCORE_preventHostingStartup": "True"``을 _launchSettings.json_ 환경 변수에 추가합니다.

이러한 메서드 중 하나를 사용하는 문제는 Application Insights만 비활성화하지 못한다는 것입니다. 이러한 메서드는 *IHostingStartup* 점등 기능을 사용하는 Visual Studio에서 모두를 비활성화합니다.

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>다음 단계
* [사용자 흐름을 탐색](../../azure-monitor/app/usage-flows.md)하여 사용자가 앱을 탐색하는 방식을 이해합니다.
* 예외가 throw되는 시점의 소스 코드 및 변수 상태를 확인하려면 [스냅숏 컬렉션을 구성](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger)합니다.
* [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 합니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md)를 사용하여 전 세계에서 사용자 앱을 지속적으로 확인합니다.
