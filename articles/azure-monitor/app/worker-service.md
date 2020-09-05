---
title: Worker 서비스 앱에 대 한 Application Insights (비 HTTP 앱)
description: Azure Monitor Application Insights를 사용 하 여 .NET Core/.NET Framework 비 HTTP 앱을 모니터링 합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.openlocfilehash: 6f31236e516e44df9f5115e3efeb48db46853e8d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933276"
---
# <a name="application-insights-for-worker-service-applications-non-http-applications"></a>Worker 서비스 응용 프로그램에 대 한 Application Insights (HTTP가 아닌 응용 프로그램)

Application Insights는 `Microsoft.ApplicationInsights.WorkerService` 메시징, 백그라운드 작업, 콘솔 응용 프로그램 등과 같은 HTTP가 아닌 작업에 가장 적합 한 새 SDK를 출시 하 고 있습니다. 이러한 유형의 응용 프로그램에는 기존의 ASP.NET/ASP.NET Core 웹 응용 프로그램과 같은 들어오는 HTTP 요청 개념이 없으므로 [ASP.NET](asp-net.md) 또는 [ASP.NET Core](asp-net-core.md) 응용 프로그램에 대 한 Application Insights 패키지를 사용 하는 것은 지원 되지 않습니다.

새 SDK는 자체적으로 원격 분석 컬렉션을 수행 하지 않습니다. 대신 [microsoft.applicationinsights.dependencycollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/), [perfcountercollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/), [ApplicationInsightsLoggingProvider](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 등과 같은 잘 알려진 다른 Application Insights 자동 수집기를 제공 합니다. 이 SDK는 `IServiceCollection` 원격 분석 컬렉션을 사용 하도록 설정 하 고 구성 하기 위해에 확장 메서드를 노출 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

[작업자 서비스에 대 한 APPLICATION INSIGHTS SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 는 응용 프로그램이 실행 되는 위치와 관계 없이 비 HTTP 응용 프로그램에 가장 적합 합니다. 응용 프로그램이 실행 중이 고 Azure에 네트워크로 연결 되어 있는 경우 원격 분석을 수집할 수 있습니다. Application Insights 모니터링은 .NET Core가 지원 되는 모든 위치에서 지원 됩니다. 이 패키지는 새로 도입 된 [.Net Core 3.0 Worker 서비스](https://devblogs.microsoft.com/aspnet/dotnet-core-workers-in-azure-container-instances), [Asp.Net Core 2.1/2.2의 백그라운드 작업](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2), 콘솔 앱 (.net Core/.NET Framework) 등에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

유효한 Application Insights 계측 키입니다. Application Insights에 원격 분석을 보내려면이 키가 필요 합니다. 계측 키를 가져오기 위해 새 Application Insights 리소스를 만들어야 하는 경우 [Application Insights 리소스 만들기](./create-new-resource.md)를 참조 하세요.

## <a name="using-application-insights-sdk-for-worker-services"></a>Worker 서비스용 Application Insights SDK 사용

1. 응용 프로그램에 [Microsoft ApplicationInsights. 서비스](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치 합니다.
   다음 코드 조각에서는 프로젝트의 파일에 추가 해야 하는 변경 내용을 보여 줍니다 `.csproj` .

```xml
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.WorkerService" Version="2.13.1" />
    </ItemGroup>
```

1. `AddApplicationInsightsTelemetryWorkerService(string instrumentationKey)`계측 키를 제공 하 여에 대 한 확장 메서드 `IServiceCollection` 를 호출 합니다. 응용 프로그램의 시작 부분에서이 메서드를 호출 해야 합니다. 정확한 위치는 응용 프로그램의 유형에 따라 달라 집니다.

1. `ILogger` `TelemetryClient` `serviceProvider.GetRequiredService<TelemetryClient>();` 을 호출 하거나 생성자 주입을 사용 하 여 DI (종속성 주입) 컨테이너에서 인스턴스 또는 인스턴스를 검색 합니다. 이 단계에서는 `TelemetryConfiguration` 및 자동 수집 모듈을 설정 하는 작업을 트리거합니다.

각 응용 프로그램 유형에 대 한 구체적인 지침은 다음 섹션에 설명 되어 있습니다.

## <a name="net-core-30-worker-service-application"></a>.NET Core 3.0 작업자 서비스 응용 프로그램

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) 에서 공유 됩니다.

1. [.Net Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0) 다운로드 및 설치
2. Visual Studio 새 프로젝트 템플릿 또는 명령줄을 사용 하 여 새 작업자 서비스 프로젝트를 만듭니다. `dotnet new worker`
3. 응용 프로그램에 [Microsoft ApplicationInsights. 서비스](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 패키지를 설치 합니다.

4. `services.AddApplicationInsightsTelemetryWorkerService();` `CreateHostBuilder()` `Program.cs` 다음 예제와 같이 클래스의 메서드에를 추가 합니다.

```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureServices((hostContext, services) =>
            {
                services.AddHostedService<Worker>();
                services.AddApplicationInsightsTelemetryWorkerService();
            });
```

5. `Worker.cs`아래 예제에 따라을 수정 합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class Worker : BackgroundService
    {
        private readonly ILogger<Worker> _logger;
        private TelemetryClient _telemetryClient;
        private static HttpClient _httpClient = new HttpClient();

        public Worker(ILogger<Worker> logger, TelemetryClient tc)
        {
            _logger = logger;
            _telemetryClient = tc;
        }

        protected override async Task ExecuteAsync(CancellationToken stoppingToken)
        {
            while (!stoppingToken.IsCancellationRequested)
            {
                _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
                {
                    _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                    _logger.LogInformation("Calling bing.com");
                    var res = await _httpClient.GetAsync("https://bing.com");
                    _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                    _telemetryClient.TrackEvent("Bing call event completed");
                }

                await Task.Delay(1000, stoppingToken);
            }
        }
    }
```

6. 계측 키를 설정 합니다.

    계측 키를에 대 한 인수로 제공할 수 있지만 `AddApplicationInsightsTelemetryWorkerService` 구성에서 계측 키를 지정 하는 것이 좋습니다. 다음 코드 샘플에서는에서 계측 키를 지정 하는 방법을 보여 줍니다 `appsettings.json` . `appsettings.json`게시 하는 동안이 응용 프로그램 루트 폴더에 복사 되었는지 확인 합니다.

```json
    {
        "ApplicationInsights":
            {
            "InstrumentationKey": "putinstrumentationkeyhere"
            },
        "Logging":
        {
            "LogLevel":
            {
                "Default": "Warning"
            }
        }
    }
```

또는 다음 환경 변수 중 하나에서 계측 키를 지정 합니다.
`APPINSIGHTS_INSTRUMENTATIONKEY` 또는 `ApplicationInsights:InstrumentationKey`

`SET ApplicationInsights:InstrumentationKey=putinstrumentationkeyhere`
디스크나 `SET APPINSIGHTS_INSTRUMENTATIONKEY=putinstrumentationkeyhere`

일반적으로 `APPINSIGHTS_INSTRUMENTATIONKEY` Web Apps에 배포 된 응용 프로그램에 대 한 계측 키를 웹 작업으로 지정 합니다.

> [!NOTE]
> 코드에 지정 된 계측 키가 `APPINSIGHTS_INSTRUMENTATIONKEY` 다른 옵션을 통해 적용 되는 환경 변수를 통해 이깁니다.

## <a name="aspnet-core-background-tasks-with-hosted-services"></a>호스팅된 서비스를 사용 하 여 백그라운드 작업 ASP.NET Core

[이](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-2.2) 문서에서는 ASP.NET Core 2.1/2.2 응용 프로그램에서 배경 작업을 만드는 방법을 설명 합니다.

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) 에서 공유 됩니다.

1. https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)응용 프로그램에 패키지를 설치 합니다.
2. `services.AddApplicationInsightsTelemetryWorkerService();` `ConfigureServices()` 다음 예제와 같이 메서드에를 추가 합니다.

```csharp
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .ConfigureAppConfiguration((hostContext, config) =>
            {
                config.AddJsonFile("appsettings.json", optional: true);
            })
            .ConfigureServices((hostContext, services) =>
            {
                services.AddLogging();
                services.AddHostedService<TimedHostedService>();

                // instrumentation key is read automatically from appsettings.json
                services.AddApplicationInsightsTelemetryWorkerService();
            })
            .UseConsoleLifetime()
            .Build();

        using (host)
        {
            // Start the host
            await host.StartAsync();

            // Wait for the host to shutdown
            await host.WaitForShutdownAsync();
        }
    }
```

다음은 `TimedHostedService` 백그라운드 작업 논리가 있는 코드입니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

    public class TimedHostedService : IHostedService, IDisposable
    {
        private readonly ILogger _logger;
        private Timer _timer;
        private TelemetryClient _telemetryClient;
        private static HttpClient httpClient = new HttpClient();

        public TimedHostedService(ILogger<TimedHostedService> logger, TelemetryClient tc)
        {
            _logger = logger;
            this._telemetryClient = tc;
        }

        public Task StartAsync(CancellationToken cancellationToken)
        {
            _logger.LogInformation("Timed Background Service is starting.");

            _timer = new Timer(DoWork, null, TimeSpan.Zero,
                TimeSpan.FromSeconds(1));

            return Task.CompletedTask;
        }

        private void DoWork(object state)
        {
            _logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

            using (_telemetryClient.StartOperation<RequestTelemetry>("operation"))
            {
                _logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                _logger.LogInformation("Calling bing.com");
                var res = httpClient.GetAsync("https://bing.com").GetAwaiter().GetResult();
                _logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                _telemetryClient.TrackEvent("Bing call event completed");
            }
        }
    }
```

3. 계측 키를 설정 합니다.
   `appsettings.json`위의 .Net Core 3.0 Worker 서비스 예제와 동일한를 사용 합니다.

## <a name="net-corenet-framework-console-application"></a>.NET Core/.NET Framework 콘솔 응용 프로그램

이 문서의 시작 부분에서 설명한 것 처럼 새 패키지를 사용 하 여 일반 콘솔 응용 프로그램 에서도 Application Insights 원격 분석을 사용 하도록 설정할 수 있습니다. 이 패키지 [`NetStandard2.0`](/dotnet/standard/net-standard) 는를 대상으로 하므로 .Net Core 2.0 이상에서 콘솔 앱에 사용할 수 있고 .NET Framework 4.7.2 이상에서 사용할 수 있습니다.

전체 예제는 [여기](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) 에서 공유 됩니다.

1. https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService)응용 프로그램에 패키지를 설치 합니다.

2. 아래 예제와 같이 Program.cs을 수정 합니다.

```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Logging;
    using System;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace WorkerSDKOnConsole
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                // Create the DI container.
                IServiceCollection services = new ServiceCollection();

                // Being a regular console app, there is no appsettings.json or configuration providers enabled by default.
                // Hence instrumentation key and any changes to default logging level must be specified here.
                services.AddLogging(loggingBuilder => loggingBuilder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>("Category", LogLevel.Information));
                services.AddApplicationInsightsTelemetryWorkerService("instrumentationkeyhere");

                // Build ServiceProvider.
                IServiceProvider serviceProvider = services.BuildServiceProvider();

                // Obtain logger instance from DI.
                ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

                // Obtain TelemetryClient instance from DI, for additional manual tracking or to flush.
                var telemetryClient = serviceProvider.GetRequiredService<TelemetryClient>();

                var httpClient = new HttpClient();

                while (true) // This app runs indefinitely. replace with actual application termination logic.
                {
                    logger.LogInformation("Worker running at: {time}", DateTimeOffset.Now);

                    // Replace with a name which makes sense for this operation.
                    using (telemetryClient.StartOperation<RequestTelemetry>("operation"))
                    {
                        logger.LogWarning("A sample warning message. By default, logs with severity Warning or higher is captured by Application Insights");
                        logger.LogInformation("Calling bing.com");                    
                        var res = await httpClient.GetAsync("https://bing.com");
                        logger.LogInformation("Calling bing completed with status:" + res.StatusCode);
                        telemetryClient.TrackEvent("Bing call event completed");
                    }

                    await Task.Delay(1000);
                }

                // Explicitly call Flush() followed by sleep is required in Console Apps.
                // This is to ensure that even if application terminates, telemetry is sent to the back-end.
                telemetryClient.Flush();
                Task.Delay(5000).Wait();
            }
        }
    }
```

또한이 콘솔 응용 프로그램은 동일한 기본값 `TelemetryConfiguration` 을 사용 하 고 이전 섹션의 예제와 동일한 방법으로 사용자 지정할 수 있습니다.

## <a name="run-your-application"></a>애플리케이션 실행

애플리케이션을 실행합니다. 위의 모든 항목의 예제 작업자는 bing.com에 대해 매 초 마다 http 호출을 수행 하 고 ILogger를 사용 하 여 몇 개의 로그를 내보냅니다. 이러한 줄은 작업을 `StartOperation` 만드는 데 사용 되는의 호출 내에 래핑됩니다 `TelemetryClient` (이 예제에서는 `RequestTelemetry` "operation"). Application Insights은 이러한 ILogger 로그 (경고 이상) 및 종속성을 수집 하 고 `RequestTelemetry` 부모-자식 관계가 있는와 상호 연결 됩니다. 상관 관계는 프로세스 간/네트워크 경계도 작동 합니다. 예를 들어 모니터링 되는 다른 구성 요소에 대 한 호출을 수행한 경우이 부모와도 상관 관계가 지정 됩니다.

의이 사용자 지정 작업 `RequestTelemetry` 은 일반적인 웹 응용 프로그램에서 들어오는 웹 요청과 동등한 것으로 간주할 수 있습니다. 작업을 사용할 필요는 없지만, 부모 작업 역할을 하는 [Application Insights 상관 관계 데이터 모델](./correlation.md) 에 가장 적합 `RequestTelemetry` 하며, 작업자 반복 내에 생성 된 모든 원격 분석은 논리적으로 동일한 작업에 속하는 것으로 처리 됩니다. 이 방법을 사용 하면 생성 된 모든 원격 분석 (자동 및 수동)도 동일 하 게 유지 됩니다 `operation_id` . 샘플링은에 기반 `operation_id` 을 두 며 샘플링 알고리즘은 단일 반복에서 모든 원격 분석을 유지 하거나 삭제 합니다.

다음은 Application Insights에 의해 자동으로 수집 되는 전체 원격 분석을 나열 합니다.

### <a name="live-metrics"></a>라이브 메트릭

[라이브 메트릭은](./live-stream.md) Application Insights 모니터링이 올바르게 구성 되었는지 여부를 신속 하 게 확인 하는 데 사용할 수 있습니다. 포털이 포털 및 분석에 표시 되기 시작 하는 데 몇 분 정도 걸릴 수 있지만, 라이브 메트릭은 거의 실시간으로 실행 중인 프로세스의 CPU 사용량을 표시 합니다. 요청, 종속성, 추적 등의 다른 원격 분석도 표시할 수 있습니다.

### <a name="ilogger-logs"></a>ILogger 로그

`ILogger`심각도 이상을 통해 내보낸 로그 `Warning` 는 자동으로 캡처됩니다. [ILogger 문서](ilogger.md#control-logging-level) 에 따라 Application Insights에서 캡처할 로그 수준을 사용자 지정할 수 있습니다.

### <a name="dependencies"></a>종속성

종속성 컬렉션은 기본적으로 사용 하도록 설정 되어 있습니다. [이](asp-net-dependencies.md#automatically-tracked-dependencies) 문서에서는 자동으로 수집 되는 종속성에 대해 설명 하 고 수동 추적을 수행 하는 단계를 포함 합니다.

### <a name="eventcounter"></a>EventCounter

`EventCounterCollectionModule` 는 기본적으로 사용 하도록 설정 되며, .NET Core 3.0 앱에서 기본 카운터 집합을 수집 합니다. [Eventcounter](eventcounters.md) 자습서에는 수집 된 카운터의 기본 집합이 나열 됩니다. 또한 목록을 사용자 지정 하는 방법에 대 한 지침도 있습니다.

### <a name="manually-tracking-additional-telemetry"></a>수동으로 추가 원격 분석 추적

SDK는 위에서 설명한 대로 원격 분석을 자동으로 수집 하지만 대부분의 경우 사용자는 Application Insights 서비스에 추가 원격 분석을 전송 해야 합니다. 추가 원격 분석을 추적 하는 권장 방법은 종속성 주입에서의 인스턴스를 가져온 `TelemetryClient` 다음 지원 되는 `TrackXXX()` [API](api-custom-events-metrics.md) 메서드 중 하나를 호출 하는 것입니다. 또 다른 일반적인 사용 사례는 [작업의 사용자 지정 추적](custom-operations-tracking.md)입니다. 이 방법은 위의 작업자 예제에 설명 되어 있습니다.

## <a name="configure-the-application-insights-sdk"></a>Application Insights SDK 구성

Worker 서비스 SDK에서 사용 하는 기본값은 `TelemetryConfiguration` ASP.NET 또는 ASP.NET Core 응용 프로그램에서 사용 되는 자동 구성과 유사 하며, 원격 분석을 보강 하는 데 사용 되는 TelemetryInitializers를 제외 합니다 `HttpContext` .

작업자 서비스에 대 한 Application Insights SDK를 사용자 지정 하 여 기본 구성을 변경할 수 있습니다. Application Insights ASP.NET Core SDK의 사용자는 ASP.NET Core 기본 제공 [종속성 주입](/aspnet/core/fundamentals/dependency-injection)을 사용 하 여 구성을 변경 하는 방법을 익힐 수 있습니다. 또한 이러한 서비스 SDK는 비슷한 원칙을 기반으로 합니다. `ConfigureServices()`아래 설명 된 대로에서 적절 한 메서드를 호출 하 여 섹션의 거의 모든 구성 변경을 수행 `IServiceCollection` 합니다.

> [!NOTE]
> 이 SDK를 사용 하는 동안 수정으로 구성을 변경 하는 것은 `TelemetryConfiguration.Active` 지원 되지 않으며 변경 내용은 반영 되지 않습니다.

### <a name="using-applicationinsightsserviceoptions"></a>ApplicationInsightsServiceOptions 사용

`ApplicationInsightsServiceOptions`다음 예와 같이에 전달 하 여 몇 가지 일반적인 설정을 수정할 수 있습니다 `AddApplicationInsightsTelemetryWorkerService` .

```csharp
    using Microsoft.ApplicationInsights.WorkerService;

    public void ConfigureServices(IServiceCollection services)
    {
        Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions aiOptions
                    = new Microsoft.ApplicationInsights.WorkerService.ApplicationInsightsServiceOptions();
        // Disables adaptive sampling.
        aiOptions.EnableAdaptiveSampling = false;

        // Disables QuickPulse (Live Metrics stream).
        aiOptions.EnableQuickPulseMetricStream = false;
        services.AddApplicationInsightsTelemetryWorkerService(aiOptions);
    }
```

`ApplicationInsightsServiceOptions`이 sdk의는 ASP.NET CORE sdk가 아닌 네임 스페이스에 `Microsoft.ApplicationInsights.WorkerService` `Microsoft.ApplicationInsights.AspNetCore.Extensions` 있습니다.

에서 일반적으로 사용 되는 설정 `ApplicationInsightsServiceOptions`

|설정 | Description | 기본값
|---------------|-------|-------
|EnableQuickPulseMetricStream | LiveMetrics 기능 사용/사용 안 함 | true
|EnableAdaptiveSampling | 적응 샘플링 사용/사용 안 함 | true
|EnableHeartbeat 비트 | 하트 비트 기능 사용/사용 안 함-주기적 (15 분 기본값)은 ' HeartBeatState ' 라는 사용자 지정 메트릭을 .NET 버전, Azure 환경 정보 (해당 하는 경우) 등의 런타임에 대 한 정보로 보냅니다. | true
|AddAutoCollectedMetricExtractor | 샘플링을 수행 하기 전에 요청/종속성에 대 한 미리 집계 된 메트릭을 전송 하는 TelemetryProcessor AutoCollectedMetrics 추출기를 사용/사용 안 함으로 설정 합니다. | true

최신 목록에 대해서는 [의 `ApplicationInsightsServiceOptions` 구성 가능한 설정을](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs) 참조 하세요.

### <a name="sampling"></a>샘플링

Application Insights SDK for Worker 서비스는 고정 비율과 적응 샘플링을 모두 지원 합니다. 적응 샘플링은 기본적으로 사용 하도록 설정 되어 있습니다. [ASP.NET Core 응용 프로그램](./sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)의 경우와 동일한 방식으로 작업자 서비스에 대 한 샘플링을 구성할 수 있습니다.

### <a name="adding-telemetryinitializers"></a>TelemetryInitializers 추가

모든 원격 분석과 함께 전송 되는 속성을 정의 하려면 [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) 를 사용 합니다.

컨테이너에 새를 추가 하면 `TelemetryInitializer` `DependencyInjection` SDK가 자동으로에 추가 됩니다 `TelemetryConfiguration` .

```csharp
    using Microsoft.ApplicationInsights.Extensibility;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<ITelemetryInitializer, MyCustomTelemetryInitializer>();
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="removing-telemetryinitializers"></a>TelemetryInitializers 제거

원격 분석 이니셜라이저는 기본적으로 제공 됩니다. 모든 또는 특정 원격 분석 이니셜라이저를 제거 하려면를 호출한 *후* 다음 샘플 코드를 사용 `AddApplicationInsightsTelemetryWorkerService()` 합니다.

```csharp
   public void ConfigureServices(IServiceCollection services)
   {
        services.AddApplicationInsightsTelemetryWorkerService();
        // Remove a specific built-in telemetry initializer
        var tiToRemove = services.FirstOrDefault<ServiceDescriptor>
                            (t => t.ImplementationType == typeof(AspNetCoreEnvironmentTelemetryInitializer));
        if (tiToRemove != null)
        {
            services.Remove(tiToRemove);
        }

        // Remove all initializers
        // This requires importing namespace by using Microsoft.Extensions.DependencyInjection.Extensions;
        services.RemoveAll(typeof(ITelemetryInitializer));
   }
```

### <a name="adding-telemetry-processors"></a>원격 분석 프로세서 추가

에서 확장 메서드를 사용 하 여 사용자 지정 원격 분석 프로세서를에 추가할 수 있습니다 `TelemetryConfiguration` `AddApplicationInsightsTelemetryProcessor` `IServiceCollection` . [고급 필터링 시나리오](./api-filtering-sampling.md#itelemetryprocessor-and-itelemetryinitializer) 에서 원격 분석 프로세서를 사용 하 여 Application Insights 서비스로 보내는 원격 분석에서 포함 되거나 제외 된 항목을 더 직접적으로 제어할 수 있습니다. 다음 예제를 사용 합니다.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
        services.AddApplicationInsightsTelemetryProcessor<MyFirstCustomTelemetryProcessor>();
        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<MySecondCustomTelemetryProcessor>();
    }
```

### <a name="configuring-or-removing-default-telemetrymodules"></a>기본 TelemetryModules 구성 또는 제거

Application Insights는 원격 분석 모듈을 사용 하 여 수동 추적 없이 특정 작업에 대 한 원격 분석을 자동으로 수집 합니다.

다음 자동 컬렉션 모듈은 기본적으로 사용 하도록 설정 되어 있습니다. 이러한 모듈은 원격 분석을 자동으로 수집 합니다. 기본 동작을 변경 하도록 사용 하지 않도록 설정 하거나 구성할 수 있습니다.

* `DependencyTrackingTelemetryModule`
* `PerformanceCollectorModule`
* `QuickPulseTelemetryModule`
* `AppServicesHeartbeatTelemetryModule` -(현재이 원격 분석 모듈과 관련 된 문제가 있습니다. 임시 해결 방법은 [GitHub 문제 1689](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1689
)을 참조 하세요.
* `AzureInstanceMetadataTelemetryModule`

기본값을 구성 하려면 `TelemetryModule` `ConfigureTelemetryModule<T>` `IServiceCollection` 다음 예제와 같이에 확장 메서드를 사용 합니다.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
    using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();

            // The following configures QuickPulseTelemetryModule.
            // Similarly, any other default modules can be configured.
            services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) =>
            {
                module.AuthenticationApiKey = "keyhere";
            });

            // The following removes PerformanceCollectorModule to disable perf-counter collection.
            // Similarly, any other default modules can be removed.
            var performanceCounterService = services.FirstOrDefault<ServiceDescriptor>
                                        (t => t.ImplementationType == typeof(PerformanceCollectorModule));
            if (performanceCounterService != null)
            {
                services.Remove(performanceCounterService);
            }
    }
```

### <a name="configuring-telemetry-channel"></a>원격 분석 채널 구성

기본 채널은 `ServerTelemetryChannel` 입니다. 다음 예제와 같이이를 재정의할 수 있습니다.

```csharp
using Microsoft.ApplicationInsights.Channel;

    public void ConfigureServices(IServiceCollection services)
    {
        // Use the following to replace the default channel with InMemoryChannel.
        // This can also be applied to ServerTelemetryChannel.
        services.AddSingleton(typeof(ITelemetryChannel), new InMemoryChannel() {MaxTelemetryBufferCapacity = 19898 });

        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

### <a name="disable-telemetry-dynamically"></a>동적 원격 분석 사용 안 함

조건부로 또는 동적으로 원격 분석을 사용 하지 않도록 설정 하려는 경우 `TelemetryConfiguration` 코드의 어느 곳에서 나 ASP.NET Core 종속성 주입 컨테이너를 사용 하 여 인스턴스를 확인 하 고 플래그를 설정할 수 있습니다 `DisableTelemetry` .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetryWorkerService();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
    {
        configuration.DisableTelemetry = true;
        ...
    }
```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-can-i-track-telemetry-thats-not-automatically-collected"></a>자동으로 수집 되지 않는 원격 분석을 추적 하려면 어떻게 해야 하나요?

`TelemetryClient`생성자 주입을 사용 하 여의 인스턴스를 가져온 다음 필요한 메서드를 호출 `TrackXXX()` 합니다. 새 인스턴스를 만드는 것은 권장 되지 않습니다 `TelemetryClient` . 의 단일 인스턴스는 `TelemetryClient` 나머지 원격 분석과 공유 하는 컨테이너에 이미 등록 되어 `DependencyInjection` `TelemetryConfiguration` 있습니다. 새 인스턴스를 만드는 것 `TelemetryClient` 은 원격 분석의 나머지 부분과 분리 된 구성이 필요한 경우에만 권장 됩니다.

### <a name="can-i-use-visual-studio-ide-to-onboard-application-insights-to-a-worker-service-project"></a>Visual Studio IDE를 사용 하 여 작업자 서비스 프로젝트에 Application Insights를 등록할 수 있나요?

Visual Studio IDE 온 보 딩은 현재 ASP.NET/ASP.NET Core 응용 프로그램에 대해서만 지원 됩니다. 이 문서는 Visual Studio에서 작업자 서비스 응용 프로그램을 온 보 딩 하는 기능이 제공 될 때 업데이트 됩니다.

### <a name="can-i-enable-application-insights-monitoring-by-using-tools-like-status-monitor"></a>상태 모니터 같은 도구를 사용 하 여 Application Insights 모니터링을 사용 하도록 설정할 수 있나요?

아니요. [상태 모니터](./monitor-performance-live-website-now.md) 및 [상태 모니터 v2](./status-monitor-v2-overview.md) 는 현재 ASP.NET 4.x만 지원 합니다.

### <a name="if-i-run-my-application-in-linux-are-all-features-supported"></a>Linux에서 응용 프로그램을 실행 하는 경우 모든 기능이 지원 되나요?

예. 이 SDK에 대 한 기능 지원은 모든 플랫폼에서 동일 하지만 다음과 같은 경우는 예외입니다.

* 성능 카운터는 라이브 메트릭에 표시 된 프로세스 CPU/메모리를 제외 하 고 Windows 에서만 지원 됩니다.
* `ServerTelemetryChannel`가 기본적으로 사용 되는 경우에도 응용 프로그램이 Linux 또는 MacOS에서 실행 되는 경우, 네트워크 문제가 있는 경우 채널이 일시적으로 원격 분석을 유지 하기 위해 로컬 저장소 폴더를 자동으로 만들지 않습니다. 이러한 제한으로 인해 임시 네트워크 또는 서버 문제가 있으면 원격 분석이 손실 됩니다. 이 문제를 해결 하려면 채널에 대 한 로컬 폴더를 구성 합니다.

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    public void ConfigureServices(IServiceCollection services)
    {
        // The following will configure the channel to use the given folder to temporarily
        // store telemetry items during network or Application Insights server issues.
        // User should ensure that the given folder already exists
        // and that the application has read/write permissions.
        services.AddSingleton(typeof(ITelemetryChannel),
                                new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
        services.AddApplicationInsightsTelemetryWorkerService();
    }
```

## <a name="sample-applications"></a>샘플 애플리케이션

[.Net Core 콘솔 응용 프로그램](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/ConsoleAppWithApplicationInsights) .NET Core (2.0 이상) 또는 .NET Framework (4.7.2 이상)로 작성 된 콘솔 응용 프로그램을 사용 하는 경우이 샘플을 사용 합니다.

[HostedServices를 사용 하는 ASP .Net Core 백그라운드 작업](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/BackgroundTasksWithHostedService) Asp.Net Core 2.1/2.2를 사용 하 고 [여기](/aspnet/core/fundamentals/host/hosted-services?view=aspnetcore-2.2) 에 공식 지침에 따라 백그라운드 작업을 만드는 경우이 샘플을 사용 합니다.

[.Net Core 3.0 작업자 서비스](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/WorkerServiceSDK/WorkerServiceSampleWithApplicationInsights) [여기](/aspnet/core/fundamentals/host/hosted-services?tabs=visual-studio&view=aspnetcore-3.0#worker-service-template) 에 공식 지침에 따라 .net Core 3.0 작업자 서비스 응용 프로그램이 있는 경우이 샘플을 사용 합니다.

## <a name="open-source-sdk"></a>오픈 소스 SDK

[코드를 읽고이에 기여](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)합니다.

## <a name="next-steps"></a>다음 단계

* [API를 사용](./api-custom-events-metrics.md) 하 여 앱의 성능 및 사용 현황에 대 한 자세한 보기에 대 한 사용자 고유의 이벤트 및 메트릭을 보냅니다.
* [자동으로 추적 되지 않는 추가 종속성을 추적](./auto-collect-dependencies.md)합니다.
* [자동 수집 된 원격 분석을 보강 하거나 필터링](./api-filtering-sampling.md)합니다.
* [ASP.NET Core 종속성 주입](/aspnet/core/fundamentals/dependency-injection).

