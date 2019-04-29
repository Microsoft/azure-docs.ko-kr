---
title: Visual Studio 없이 ASP.NET Core 용 azure Application Insights | Microsoft Docs
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
ms.date: 04/03/2019
ms.author: cithomas
ms.openlocfilehash: 8243523887ec9861459b2d196126237cf89bad97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691345"
---
# <a name="application-insights-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램용 application Insights

이 문서에서는 설명에 대 한 Application Insights를 사용 하도록 설정 하는 단계는 [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2) Visual Studio IDE를 사용 하지 않고 응용 프로그램입니다. Visual Studio IDE를 설치한 경우 [이](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) 문서에 Visual Studio 특정 지침이 포함 되어 있습니다. 이 문서의 지침을 완료 하면 Application Insights는 ASP.NET Core 응용 프로그램에서 요청, 종속성, 예외, 성능 카운터, 하트 비트 및 로그 수집 시작 됩니다. 사용 되는 예제 응용 프로그램은는 [MVC 응용 프로그램](https://docs.microsoft.com/aspnet/core/tutorials/first-mvc-app/?view=aspnetcore-2.2) 대상 `netcoreapp2.2`, 이지만 여기에 지침이 모든 ASP.NET Core 응용 프로그램에 적용할 수 있습니다. 해당 하는 경우 모든 예외 명시 되어 있습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

ASP.NET Core 용 application Insights SDK (소프트웨어 개발 키트)는 응용 프로그램 실행은 어떻게에 관계 없이 응용 프로그램을 모니터링할 수 있습니다. 응용 프로그램이 실행 되 고 있고 Application Insights 서비스에 대 한 네트워크 연결, 원격 분석 수집 될 예정입니다. 이 지원을 포함 하지만 모든 운영 체제 (Windows, Linux, Mac), 호스팅 메서드 (in process vs out of process), 배포 방법 (프레임 워크 종속 및 자체 포함된), 웹 서버 (IIS, Kestrel), 플랫폼 (Azure Web Apps, Azure VM에 제한 되지 않습니다. Docker, AKS 및 등입니다.) 또는 IDE (Visual Studio, VS Code 명령 줄)

## <a name="prerequisites"></a>필수 조건

- 작동 ASP.NET Core 응용 프로그램입니다. 따릅니다 [이](https://docs.microsoft.com/aspnet/core/getting-started/) 필요한 경우 ASP.NET Core 응용 프로그램을 만들기 가이드입니다.
- 유효한 Application Insights 계측 키를 Application Insights 서비스에 모든 원격 분석을 전송 하는 데 필요한 합니다. 따릅니다 [이러한](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 목록이 필요한 경우 새 Application Insights 리소스를 만들고 계측 키를 가져오기만 하는 지침입니다.

## <a name="enabling-application-insights-server-side-telemetry"></a>Application Insights에 대 한 서버 쪽 원격 분석을 사용 하도록 설정

1. 일반 NuGet 패키지는 ASP.NET Core 용 Application Insights SDK를 설치 합니다. 항상 안정적인 최신 버전을 사용 하는 것이 좋습니다. 이 문서에서 설명 하는 기능 중 일부는 이전 버전에서 사용할 수 없습니다 수 있습니다. SDK에 대 한 전체 릴리스 정보를 찾을 수 있습니다 [여기](https://github.com/Microsoft/ApplicationInsights-aspnetcore/releases)합니다. 

다음에는 프로젝트의.csproj 파일에 추가할 변경 내용을 보여 줍니다.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
```

2. 추가 `services.AddApplicationInsightsTelemetry();` 하 `ConfigureServices()` 에서 메서드 여 `Startup` 클래스입니다. 아래 전체 예제입니다.

```csharp
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        // The following line enables Application Insights telemetry collection.
        services.AddApplicationInsightsTelemetry();

        // code adding other services for your application
        services.AddMvc();
    }
```

3. 계측 키를 설정 합니다.

   계측 키를 인수로 제공할 수 있지만 `services.AddApplicationInsightsTelemetry("putinstrumentationkeyhere");`, 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음에서 계측 키를 지정 하는 방법을 보여 줍니다 `appsettings.json`합니다. 했는지 `appsettings.json` 게시 하는 동안 응용 프로그램 루트 폴더에 복사 됩니다.

```json
    {
      "ApplicationInsights": {
        "InstrumentationKey": "putinstrumentationkeyhere"
      },
      "Logging": {
        "LogLevel": {
          "Default": "Warning"
        }
      }
    }
```

    Alternately, the instrumentation key can also be specified in either of the following environment variables.
    APPINSIGHTS_INSTRUMENTATIONKEY
    ApplicationInsights:InstrumentationKey

    Example:
    `SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`

`APPINSIGHTS_INSTRUMENTATIONKEY` Azure Web Apps에 배포 된 응용 프로그램에 대 한 계측 키를 지정 하려면 일반적으로 사용 됩니다.

> [!NOTE]
> 환경 변수를 통해 코드 wins에 지정 된 계측 키 `APPINSIGHTS_INSTRUMENTATIONKEY`, 다른 옵션과 비교 wins입니다.

4. 응용 프로그램을 실행 하 고 요청을 확인 합니다. 원격 분석이 Application Insights로 이동 하는 것을 시작 됩니다. 다음과 같은 원격 분석은 Application Insights SDK에서 자동으로 수집 됩니다.

    1. 요청 수-들어오는 웹 응용 프로그램에 요청합니다.
    1. 종속성
        1. 사용 하 여 http/Https 호출 `HttpClient`
        1. 사용 하 여 SQL 호출 `SqlClient`
        1. 사용 하 여 호출 하는 azure storage [Azure Storage 클라이언트](https://www.nuget.org/packages/WindowsAzure.Storage/)*
        1. [EventHub 클라이언트 SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) 버전 1.1.0 이상
        1. [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) 버전 3.0.0 이상

             * Azure Cosmos DB는 HTTP/HTTPS를 사용 하는 경우에 자동으로 추적 됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다.


    1. [성능 카운터](https://azure.microsoft.com/documentation/articles/app-insights-web-monitor-performance/)
        1. ASP.NET Core의 성능 카운터에 대 한 지원은 다음으로 제한
            1. SDK 버전 2.4.1 응용 프로그램은 Azure 웹 앱 (Windows)에서 실행 중인 경우 위의 성능 카운터를 수집 하 고
            1. SDK 버전 2.7.0-beta3 응용 프로그램에서 Windows를 실행 하 고 대상으로 하는 경우 위의 성능 카운터를 수집 하 고 `NETSTANDARD2.0` 이상.
            1. 전체.NET Framework를 대상으로 하는 응용 프로그램에서 성능 카운터 모든 버전의 SDK에서 지원 됩니다.

            이 문서는 Linux에서 성능 카운터 지원을 추가 될 때 업데이트 됩니다.
    1. [라이브 메트릭](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)
    1. `ILogger` 로그 심각도 `Warning` 또는 위에 자동으로 SDK 버전 2.7.0-beta3에서 캡처된 이상. [여기](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)서 자세히 알아보세요.

원격 분석 포털에서 나타날 때까지 몇 분 정도 걸릴 수 있습니다. 신속 하 게 사용 하는 모든 것이 작동 하는 경우를 확인 하려면 [라이브 메트릭](https://docs.microsoft.com/azure/application-insights/app-insights-live-stream)반면을 위해 실행 중인 응용 프로그램에 요청 합니다.

## <a name="send-ilogger-logs-to-application-insights"></a>Application Insights ILogger 로그 보내기

Application Insights ILogger를 통해 전송 된 캡처 로그를 지원 합니다. 전체 설명서를 읽을 [여기](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)합니다.

## <a name="enable-client-side-telemetry-for-web-applications"></a>웹 응용 프로그램에 대 한 클라이언트 쪽 원격 분석을 사용 하도록 설정

위의 단계는 서버 쪽 원격 분석 수집을 시작 하는 데 충분 합니다. 응용 프로그램의 클라이언트 쪽 구성 요소에 하는 경우 수집을 시작 하려면 아래 단계를 수행 [사용량 원격 분석](https://docs.microsoft.com/azure/azure-monitor/app/usage-overview) 거기서에서 합니다.

1. _ViewImports.cshtml에서 삽입을 추가 합니다.

```cshtml
    @inject Microsoft.ApplicationInsights.AspNetCore.JavaScriptSnippet JavaScriptSnippet
```

2. _Layout.cshtml, HtmlHelper의 끝에 삽입 `<head>` 섹션 다른 스크립트 전에 합니다. 이 코드 조각은 후 주입 될 페이지에서 보고 하려는 모든 사용자 지정 JavaScript 원격 분석:

```cshtml
    @Html.Raw(JavaScriptSnippet.FullScript)
    </head>
```

실제 응용 프로그램에 따라 파일 이름을 수정 합니다. 사용 되는 이름은 위의 기본 MVC 응용 프로그램 템플릿에서 합니다.

## <a name="configuring-application-insights-sdk"></a>Application Insights SDK 구성

ASP.NET Core 용 application Insights SDK는 기본 구성을 변경 하려는 사용자 지정할 수 있습니다. Application Insights ASP.NET SDK의 사용자가 사용 하 여 구성을 익숙할 `ApplicationInsights.config`를 수정 하 여 `TelemetryConfiguration.Active`입니다. ASP.NET Core에 대 한 구성은 다른 방식으로 수행 됩니다. ASP.NET Core SDK는 ASP.NET Core의 기본 제공을 사용 하 여 응용 프로그램에 추가할 [DependencyInjection](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 메커니즘 및 동일한 구성도 사용 될 수 DependencyInjection입니다. 거의 모든 구성 변경 내용을 수행 되는 `ConfigureServices()` 메서드의 `Startup.cs` 언급이 클래스입니다. 자세한 내용은 아래 섹션을 따릅니다.

> [!NOTE]
> 것에 해당 수정 구성을 수정 하 여 유의 해야 `TelemetryConfiguration.Active` ASP.NET Core 응용 프로그램에서 권장 되지 않습니다.

### <a name="configuring-using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions를 사용 하 여 구성

전달 하 여 몇 가지 일반적인 설정을 수정할 수 있기 `ApplicationInsightsServiceOptions` 에 `services.AddApplicationInsightsTelemetry();`입니다. 아래에 예제가 나와 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetry(aiOptions);
    }
```

구성 가능한 설정의 정확한 목록을 `ApplicationInsightsServiceOptions` 찾을 수 있습니다 [여기](https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/f0b8631e482d25982eb52092103b34e3ff6e5fef/src/Microsoft.ApplicationInsights.AspNetCore/Extensions/ApplicationInsightsServiceOptions.cs)합니다.

### <a name="sampling"></a>샘플링

ASP.NET Core 용 application Insights SDK는 FixedRate 및 적응 샘플링을 지원합니다. 적응 샘플링은 기본적으로 사용 됩니다. 따릅니다 [이](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) 문서를 ASP.NET Core 응용 프로그램에 대 한 샘플링을 구성 하는 방법을 알아봅니다.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

새 `TelemetryInitializer`, 아래와 같이 DependencyInjection 컨테이너에 추가 합니다. `TelemetryInitializer`s DependencyInjection 컨테이너에 추가 됩니다 수 SDK에서 자동으로 선택 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

모두 제거 하려면 기본적으로 제공 되는 특정 TelemetryInitializers 다음 샘플 코드를 사용 하거나 **한 후** 호출 `AddApplicationInsightsTelemetry()`합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // Remove a specific built-in TelemetryInitializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                         (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
    }
```

### <a name="adding-telemetryprocessors"></a>TelemetryProcessors를 추가합니다.

사용자 지정 원격 분석 프로세서를 추가할 수는 `TelemetryConfiguration` 확장 메서드를 사용 하 여 `AddApplicationInsightsTelemetryProcessor` 에서 `IServiceCollection`합니다. 다음 예제를 사용 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>구성 또는 기본 TelemetryModules 제거

다음 자동 수집 모듈 기본적으로 사용 되 고 자동으로 원격 분석을 수집 하는 일을 담당 합니다. 사용 하지 않도록 설정 및 기본 동작을 변경 하도록 구성 될 수 있습니다.

1. `RequestTrackingTelemetryModule`
1. `DependencyTrackingTelemetryModule`
1. `PerformanceCollectorModule`
1. `QuickPulseTelemetryModule`
1. `AppServicesHeartbeatTelemetryModule`
1. `AzureInstanceMetadataTelemetryModule`

모든 기본값을 구성 하려면 `TelemetryModule`, 확장 메서드를 사용 하 여 `ConfigureTelemetryModule<T>` 에서 `IServiceCollection` 아래 예와에서 같이 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following configures DependencyTrackingTelemetryModule.
        // Similarly, any other default modules can be configured.
        services.ConfigureTelemetryModule<DependencyTrackingTelemetryModule>((module, o) =>
                        {
                            module.EnableW3CHeadersInjection = true;
                        });

        // The following removes PerformanceCollectorModule to disable perf-counter collection.
        // Similarly, any other default modules can be removed.
        var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>(t => t.ImplementationType == typeof(PerformanceCollectorModule));
        if (performanceCounterService != null)
        {
         services.Remove(performanceCounterService);
        }
    }
```

### <a name="configuring-telemetry-channel"></a>원격 분석 채널 구성

사용 되는 기본 채널은는 `ServerTelemetryChannel`합니다. 다음 예제에서는 아래에서 재정의할 수 있습니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // use the following to replace the default channel with InMemoryChannel.
        // this can also be applied to ServerTelemetryChannel as well.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetry();
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

*1. 자동 수집 된 원격 분석 외에도 몇 가지 추가 원격 분석을 추적 하려고 합니다. 하 하려면 어떻게 해야 합니까?*

* 인스턴스를 가져올 `TelemetryClient` 생성자 주입을 사용 하 고 필요한 호출 하 여 `TrackXXX()` 메서드를 합니다. 새로 만들려면 권장 되지 않습니다 `TelemetryClient` ASP.NET Core 응용 프로그램에서의 singleton 인스턴스로 인스턴스 `TelemetryClient` 공유 하는 DI 컨테이너에 이미 등록 되어 `TelemetryConfiguration` 원격 분석의 나머지 부분을 사용 하 여 합니다. 새 `TelemetryClient` 인스턴스에 원격 분석의 나머지 부분에서 별도 구성 하는 경우에 권장 됩니다. 다음 예제에서는 컨트롤러에서 추가 원격 분석을 추적 하는 방법을 보여 줍니다.

```csharp
public class HomeController : Controller
{
    private TelemetryClient telemetry;

    // use constructor injection to get TelemetryClient instance
    public HomeController(TelemetryClient telemetry)
    {
        this.telemetry = telemetry;
    }

    public IActionResult Index()
    {
        // call required TrackXXX method.
        this.telemetry.TrackEvent("HomePageRequested");
        return View();
    }
```

 가리킵니다 [Application Insights 사용자 지정 메트릭 API 참조](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics/) 에 대 한 설명은 사용자 지정 데이터를 Application Insights에 보고 합니다.

*2. Application Insights를 사용 하도록 설정 하려면 IWebHostBuilder에 UseApplicationInsights() 확장 메서드를 사용 하는 몇 가지 Visual Studio 템플릿. 이 사용량이 여전히 유효한?*

* 이 메서드를 사용 하 여 Application Insights를 사용 하도록 설정 하면 유효 하 고 Visual Studio 온 보 딩을 및 Azure 웹 앱 확장도 사용 됩니다. 그러나 사용할 것 `services.AddApplicationInsightsTelemery()` 일부 구성을 제어 하기 위한 오버 로드를 제공 합니다. 두 메서드는 내부적으로 동일한 작업을 수행, 적용할 사용자 지정 구성이 없는 경우, 호출 중 하나를 합니다.

*3. 내 ASP.NET Core 응용 프로그램을 Azure Web Apps를 배포 합니다. 웹 앱에서 Application Insights 확장을 여전히 사용할 해야 있습니까?*

* 이 문서에 표시 된 대로 빌드 시 SDK가 설치 하는 경우 웹 앱 포털에서 Application Insights 확장을 사용 하려면 않아도가 됩니다. 확장이 설치 된 경우에 해당 됩니다 백오프 SDK 응용 프로그램에 이미 추가 되어 있는지 검색 하는 경우. Application Insights를 사용 하도록 설정 확장 프로그램에서 설치 하 고 응용 프로그램에 SDK를 업데이트 해제 합니다. 그러나이 문서에 따라 Application Insights를 사용 하도록 설정 하면는 아래 원인에 대 한 더 유연 합니다.
    1. Application Insights 원격 분석에서 작업을 계속 합니다.
        1. 모든 운영 체제-Windows, Linux, Mac.
        1. 모든 게시 모드-독립적인 또는 프레임 워크 종속입니다.
        1. 모든 대상 프레임 워크에서 전체.NET Framework를 포함 합니다.
        1. 모든 호스팅 옵션-Azure 웹 앱, Vm, Linux, 컨테이너, AKS, 비 Azure.
    1. Visual Studio에서 디버깅 하는 경우 원격 분석을 로컬에서 볼 수 있습니다.
    1. 사용 하 여 추가 사용자 지정 원격 분석 추적을 사용 하면 `TrackXXX()` API.
    1. 구성을 통해 모든 권한을 가집니다.

*4. Application Insights 상태 모니터와 같은 도구를 사용 하 여 모니터링을 사용할 수 있습니까?*

* 아니요. [상태 모니터](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) 예정 된 대체 [IISConfigurator](https://github.com/Microsoft/ApplicationInsights-Announcements/issues/21) 현재 ASP.NET만 지원 합니다. 문서를 업데이트할 때 ASP.NET Core 응용 프로그램 수에 대 한 지원.

*5. ASP.NET Core 2.0 응용 프로그램을 해야 하나요? Application Insights 자동에 사용할 수 없는 이러한 아무 작업도 하지 않았는데?*

* `Microsoft.AspNetCore.All` 2.0 메타 패키지는 Application Insights SDK (버전 2.1.0)를 포함 하 고 Visual Studio 디버거에서 응용 프로그램을 실행 하는 경우 Visual Studio application insights를 사용 하면 IDE 자체에서 로컬 원격 분석을 보여 줍니다. 계측 키를 명시적으로 지정 하지 않으면 Application Insights 서비스로 원격 분석 전송 되지 않았습니다. 2.0에 대해서도 Application Insights를 사용 하도록 설정 하려면이 문서의 지침을 위한 앱.

*6. Linux에서 응용 프로그램을 실행 하는 필자입니다. 모든 기능에서에서 지 Linux 에서도?*

* 예. SDK에 대 한 지원 기능 다음 예외를 사용 하 여 모든 플랫폼에서 동일 합니다.
    1. 성능 카운터는 비-Windows에서 아직 지원 되지 않습니다. 이 문서는 Linux 지원이 추가 될 때 업데이트 됩니다.
    1. 경우에 `ServerTelemetryChannel` 사용은 기본적으로 응용 프로그램은 windows에서 실행 중인 경우 채널 자동으로 만들어지지는지 않습니다 네트워크 문제가 있는 경우 원격 분석을 일시적으로 유지 하려면 로컬 저장소 폴더입니다. 이 제한은 일시적인 네트워크 또는 서버 문제 없는 경우 손실 될 원격 분석을 하면 됩니다. 이 문제를 해결 하려면 아래 표시 된 대로 채널의 로컬 폴더를 구성 하는 사용자입니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure channel to use the given folder to temporarily
        // store telemetry items during network or application insights server issues.
        // User should ensure that the given folder already exists,
        // and that application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetry();
    }
```
