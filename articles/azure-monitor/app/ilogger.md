---
title: ILogger를 사용하여 Azure Application Insights에서 .NET 추적 로그 살펴보기
description: Azure Application Insights ILogger 공급자를 사용 하 여 ASP.NET Core 및 콘솔 응용 프로그램의 샘플입니다.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: 615eaa3df7cabad72ac321978eb01d93a7bfa988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901074"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>.NET Core ILogger에 대 한 ApplicationInsightsLoggerProvider 로그

ASP.NET Core는 여러 가지 기본 제공 및 타사 로깅 공급자를 사용 하 여 작동 하는 로깅 API를 지원 합니다. Log () 또는 변형에서 호출 하 여 로깅 이루어집니다 `ILogger` 인스턴스. 이 문서에서는 사용 하는 방법을 보여 줍니다 `ApplicationInsightsLoggerProvider` 캡처할 `ILogger` 콘솔 및 ASP.NET Core 응용 프로그램에 로그인 합니다. 에이 대해서도 설명 하는 방법을 `ApplicationInsightsLoggerProvider` 다른 Application Insights 원격 분석과 통합 되어 있습니다.
Asp.Net Core의 자세한 로깅에 대해 알아보려면 [이 문서에서는](https://docs.microsoft.com/aspnet/core/fundamentals/logging)합니다.

## <a name="aspnet-core-applications"></a>ASP.NET Core 응용 프로그램

부터는 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.0-beta3 부터는 `ApplicationInsightsLoggerProvider` 중 하나는 표준 메서드-에서 사용 하 여 일반 Application Insights 모니터링을 사용 하도록 설정할 때 기본적으로 사용 됩니다 호출 `UseApplicationInsights` IWebHostBuilder 확장 메서드 또는 `AddApplicationInsightsTelemetry` IServiceCollection 확장 메서드. `ILogger` 에 의해 캡처된 로그 `ApplicationInsightsLoggerProvider` 수집 된 다른 원격 분석과 동일한 구성을 적용 됩니다. 예: 동일한 집합을 갖습니다 `TelemetryInitializer`s `TelemetryProcessor`개이면에서는 동일한 `TelemetryChannel`, 및 상관 관계가 지정 된 되며 동일한 방식으로 다른 모든 원격 분석 샘플링 합니다.  이 버전의 SDK로 또는 그 이상 있는 경우 캡처에 필요한 작업이 없습니다 `ILogger` 로그 합니다.

기본적으로 `ILogger` 로그 `Warning` 또는 위에 (모든 범주의)는 Application Insights로 전송 합니다. 표시 된 것 처럼 필터를 적용 하 여이 동작을 변경할 수 있습니다 [여기](#control-logging-level)합니다. 또한 추가 단계가 필요한 경우 `ILogger` 에서 로그 `Program.cs` 또는 `Startup.cs` 표시 된 것 처럼 캡처할 됩니다 [여기](#capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications)합니다.

Microsoft.ApplicationInsights.AspNet SDK의 이전 버전을 사용 하거나 ApplicationInsightsLoggerProvider, 사용 하려는 경우 모든 다른 Application Insights 모니터링 하지 않고 다음 단계를 수행 합니다.

1. nuget 패키지를 설치 합니다.

```xml
    <ItemGroup>
      <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
    </ItemGroup>
```

2. 수정 `Program.cs` 아래와 같이

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
            builder =>
            {
                // Providing an instrumentation key here is required if you are using
                // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                // or if you want to capture logs from early in the application startup
                // pipeline from Startup.cs or Program.cs itself.
                builder.AddApplicationInsights("ikey");

                // Optional: Apply filters to control what logs are sent to Application Insights.
                // The following configures LogLevel Information or above to be sent to
                // Application Insights for all categories.
                builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                 ("", LogLevel.Information);
            }
        );
}
```

위의 코드는 구성 `ApplicationInsightsLoggerProvider`합니다. 다음 예제를 사용 하는 컨트롤러 클래스를 보여 줍니다. `ILogger` ApplicationInsights에서 캡처되는 로그를 보내려고 합니다.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capturing-ilogger-logs-from-startupcs-programcs-in-aspnet-core-applications"></a>Asp.Net Core 응용 프로그램에서 Program.cs Startup.cs에서 ILogger 로그 캡처

새 ApplicationInsightsLoggerProvider를 사용 하 여 응용 프로그램 시작 파이프라인 초기에 로그를 캡처할 가능성이 있습니다. ApplicationInsightsLoggerProvider 기능은 자동으로 사용 Application Insights (2.7.0-beta3 이상)에서 파이프라인, 따라서 로그 컨트롤러에서 나중에 계측 키 설정이 없는/다른 클래스 캡처됩니다. 부터 모든 로그를 캡처할 `Program.cs` 고 `Startup.cs` 명시적으로 계측 키를 사용 하 여 ApplicationInsightsLoggerProvider를 사용 하도록 설정 해야 하나는 자체입니다. 중요 한 사실은 이기도 `TelemetryConfiguration` 완벽 하 게 설정 될 때를 로깅하고 있지에서 무언가 `Program.cs` 또는 `Startup.cs` 자체를 해당 로그 InMemoryChannel, 샘플링 없음 및 없는 표준 원격 분석을 사용 하는 완전 최소 구성을 사용 하므로 이니셜라이저 또는 프로세서입니다.

다음 예를 보여 줍니다 `Program.cs` 및 `Startup.cs` 이 기능을 사용 합니다.

#### <a name="example-programcs"></a>예제 Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // providing an instrumentation key here is required if you are using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>예제 Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrating-from-old-applicationinsightsloggerprovider"></a>이전 ApplicationInsightsLoggerProvider에서 마이그레이션

2.7.0-beta2 하기 전에 Microsoft.ApplicationInsights.AspNet SDK 버전에는 이제 사용 되지 않습니다는 로깅 공급자를 지원 합니다. 이 공급자는 사용 하도록 설정 되어 `AddApplicationInsights()` 확장 메서드의 `ILoggerFactory`합니다. 이 공급자는 이제 이전 및 새 공급자에 마이그레이션할 사용자가 제안 됩니다. 마이그레이션에는 두 단계가 포함 됩니다.

1. ILoggerFactory.AddApplicationInsights() 호출을 제거 `Startup.Configure()` double 로깅을 방지 하는 방법입니다.
2. 새 공급자가 적용 되지 것입니다으로 코드에서 필터링 규칙을 다시 적용 합니다. ILoggerFactory.AddApplicationInsights()의 오버 로드는 최소 LogLevel 또는 필터 함수를 걸렸습니다. 자체 로깅 프레임 워크의 일부를 필터링 새 공급자를 사용 하 여 Application Insights 공급자가 수행 되지 않습니다. 통해 제공 되는 모든 필터 `ILoggerFactory.AddApplicationInsights()` 오버 로드 해야 제거할 수 있으며, 필터링 규칙을 제공 하도록 수행 [이러한](#control-logging-level) 지침입니다. 사용 하는 경우 `appsettings.json` 로깅을 필터링 하려면이 계속 작동 새 공급자를 사용 하 여 동일한 공급자 별칭-를 사용 하는 둘 다 **ApplicationInsights**합니다.

이전 공급자를 여전히 사용할 수 있지만 (이제 사용 되지 않습니다 고 3.xx 주 버전 변경에만 제거 됩니다), 다음과 같은 이유로 가장 좋습니다 최신 공급자로 마이그레이션.

1. 이전 공급자의 지원이 없었습니다 [범위](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes)합니다. 새 공급자의 범위에서 속성은 자동으로 사용자 지정 속성으로 수집된 된 원격 분석에 추가 됩니다.
2. 로그는 이제 응용 프로그램 시작 파이프라인에서 훨씬 더 빨리 캡처할 수 있습니다. 예: 이제 프로그램 및 시작 클래스에서 로그를 캡처할 수 있습니다.
3. 새 공급자를 사용 하 여 필터링 자체 프레임 워크 수준에서 수행 됩니다. 로그 필터링 Application Insights 공급자를 콘솔에 디버그와 같은 기본 제공 공급자를 비롯 한 다른 공급자의 경우와 정확히 동일한 방식으로 수행할 수 등에입니다. 여러 공급자에 게 동일한 필터를 적용 하는 것도 가능 합니다.
4. 합니다 [것이 좋습니다](https://github.com/aspnet/Announcements/issues/255) ASP.NET Core (2.0 이상)에서 로깅을 사용 하도록 설정 하려면 것에서 ILoggingBuilder에서 확장 메서드를 사용 하 여 `Program.cs` 자체입니다.

> [!Note]
> 새 공급자가 대상 응용 프로그램에 사용할 수 있는 `NETSTANDARD2.0` 이상. 응용 프로그램의.NET Core 1.1과 같은.NET Core 버전을 대상 또는.NET Framework를 대상으로 하는 경우 계속 이전 공급자를 사용 합니다.

## <a name="console-application"></a>콘솔 애플리케이션

다음 코드는 보내도록 구성 하는 샘플 콘솔 응용 프로그램을 보여줍니다 `ILogger` Application Insights로 추적 합니다.

설치된 패키지:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

위의 예에서 독립 실행형 패키지 `Microsoft.Extensions.Logging.ApplicationInsights` 사용 됩니다. 기본적으로 이 구성은 Application Insights에 데이터를 전송하기 위해 최저 `TelemetryConfiguration`을 사용합니다. 최저는 사용되는 채널이 샘플링 및 표준 TelemetryInitializers 없는 `InMemoryChannel`임을 의미합니다. 아래 예제와 같이 콘솔 애플리케이션의 경우 이 동작을 재정의할 수 있습니다.

다음 추가 패키지를 설치합니다.

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

다음 섹션에서는 기본값을 재정의 하는 방법을 보여 줍니다 `TelemetryConfiguration` 를 사용 하 여 `services.Configure<TelemetryConfiguration>()` 메서드. 이 예제에서는 설정 `ServerTelemetryChannel`를 샘플링 하 고 사용자 지정 추가 `ITelemetryInitializer` 에 `TelemetryConfiguration`합니다.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly call Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if application terminates, telemetry is sent to the back-end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>컨트롤 로깅 수준

Asp.Net Core `ILogger` 인프라에 적용할 기본 제공 메커니즘이 [필터링](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering) 로그를 Application Insights 공급자를 비롯 한 각 등록 된 공급자에 전송 된 로그를 제어할 수 있도록 합니다. 이 필터링 가능 구성에서 중 하나 (일반적으로 사용 하 여 `appsettings.json` 파일) 또는 코드입니다. 이 기능은 자체 프레임 워크에서 제공 하며 Application Insights 공급자에 한정 되지 않습니다.

ApplicationInsightsLoggerProvider에 필터 규칙을 적용 하는 예제는 다음과 같습니다.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Appsettings.json을 사용 하 여 구성에서 필터 규칙 만들기

공급자 별칭은 ApplicationInsightsLoggerProvider에 대 한 `ApplicationInsights`합니다. 아래에 표시 된 섹션 `appsettings.json` 로그 구성 `Warning` 이상 모든 범주의 `Error` 이상 보낼 "Microsoft"로 시작 하는 범주의 `ApplicationInsightsLoggerProvider`합니다.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>코드에서 필터 규칙 만들기

아래 코드 조각은 로그를 구성 합니다 `Warning` 이상 모든 범주의 `Error` 이상 보낼 'Microsoft'로 시작 하는 범주의 `ApplicationInsightsLoggerProvider`합니다. 이 구성에서 수행 하는 위의 구성 동일 `appsettings.json`합니다.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>질문과 대답

*1. 이전 및 새 ApplicationInsightsLoggerProvider 란?*

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) ILoggerFactory를 사용 하 여 사용 하도록 설정 된 기본 제공 ApplicationInsightsLoggerProvider (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider)와 함께 제공 확장 메서드입니다. 이 공급자 2.7.0-beta2 이상에서 사용 되지 않는 표시 되 고 주 버전 변경에서 완전히 제거 됩니다. [이](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 자체 되지 않는 패키지 및 요청, 종속성 등의 모니터링을 사용 하도록 설정 해야 합니다.

* 권장 방법은 새 독립 실행형 패키지 [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights)에 향상 된 ApplicationInsightsLoggerProvider (포함 된 Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) 및 사용 하도록 설정 하는 것에 대 한 ILoggerBuilder의 확장 메서드입니다.

* [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 버전부터 위의 패키지의 종속성을 수행 하 고 사용 하도록 설정 `ILogger` 자동으로 캡처합니다.

*2. 일부 표시 `ILogger` 로그는 Application Insights에 두 번 표시 됩니다.*

* (이제 사용 되지 않음) 이전 버전의 경우이 중복 가능성이 `ApplicationInsightsLoggerProvider` 를 호출 하 여 사용 하도록 설정 `AddApplicationInsights` 에서 `ILoggerFactory`합니다. 확인 프로그램 `Configure` 메서드는 다음과 같은 및 제거 합니다.

   ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
        // ..other code.
    }
   ```

* Double 로깅 Visual Studio에서 디버그할 때 발생 하는 경우 다음과 같이 설정 하 여 Application Insights를 사용 하는 데 사용 된 코드를 수정 합니다 `EnableDebugLogger` false 여야 합니다. 이 중복 문제 및 수정 프로그램은 응용 프로그램을 디버깅할 때만 관련 있습니다.

   ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
        options.EnableDebugLogger = false;
        services.AddApplicationInsightsTelemetry(options);
        // ..other code.
    }
   ```

*3. 업데이트 했지만 [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 버전 2.7.0-beta3 고 이제 표시에서 기록 `ILogger` 자동으로 캡처됩니다. 하려면 어떻게 할까요이 기능을 완전히?*

* 참조 [이](../../azure-monitor/app/ilogger.md#control-logging-level) 필터링 하는 방법을 알고 섹션 일반적 기록 합니다. 전원 끄기 ApplicationInsightsLoggerProvider 사용 `LogLevel.None` 에 대 한 합니다.

  코드에서

    ```csharp
        builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                          ("", LogLevel.None);
    ```

  구성에서

    ```json
    {
      "Logging": {
        "ApplicationInsights": {
          "LogLevel": {
            "Default": "None"
          }
    }
    ```

*4. 일부 표시 `ILogger` 로그 동일한 속성을 다른 사용자가 필요 하지?*

* 응용 프로그램 정보 캡처 및 보냅니다 `ILogger` 동일한를 사용 하 여 로그 `TelemetryConfiguration` 다른 모든 원격 분석에 사용 합니다. 이 규칙에 예외가 있습니다. 기본값 `TelemetryConfiguration` 완벽 하 게 설정 될 때를 로깅하고 있지에서 무언가 `Program.cs` 또는 `Startup.cs` 이러한 위치에서 로그를 기본 구성으로 없고 따라서 실행 되지 모든 있도록 그 자체를 `TelemetryInitializer`s 및 `TelemetryProcessor`s입니다.

*5. 독립 실행형 패키지 Microsoft.Extensions.Logging.ApplicationInsights 합니다 그런가요 및 일부 추가 사용자 지정 원격 분석을 수동으로 로그인 하려고 하는 경우. 어떻게 해야 합니까?*

* 독립 실행형 패키지를 사용 하는 경우 `TelemetryClient` 는 삽입 되지 않습니다. DI 컨테이너에 사용자의 새 인스턴스를 만들고 해야 하므로 `TelemetryClient` 아래와 같이 거 공급자에서 사용 되는 동일한 구성을 사용 하 여 합니다. 이렇게 하면 동일한 구성은 모든 사용자 지정 원격 분석, 뿐아니라 ILogger에서 캡처한 사용 됩니다.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> 참고는 패키지 Microsoft.ApplicationInsights.AspNetCore 패키지를 Application Insights를 사용 하도록 설정 하려면 사용 하는 경우 다음 위의 예제 수정할지를 가져오려는 `TelemetryClient` 생성자에서 직접. 참조 [이](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions) 전체 예제입니다.


*6. Application Insights 원격 분석 유형을에서 생성 됩니다 `ILogger` 로그? 또는 볼 수 있는 `ILogger` Application Insights에서 로그?*

* ApplicationInsightsLoggerProvider 캡처합니다 `ILogger` 만들고 로그 `TraceTelemetry` 에서. 예외 개체에 전달 되 면 log () 메서드 ILogger에 대 한 다음 대신 `TraceTelemetry`, `ExceptionTelemetry` 만들어집니다. 이러한 원격 분석 항목을 다른와 동일한 위치에서 찾을 수 있습니다 `TraceTelemetry` 또는 `ExceptionTelemetry` 포털, 분석 또는 로컬 Visual Studio 디버거를 포함 하 여 Application Insights에 대 한 합니다.
항상 전송 하려는 경우 `TraceTelemetry`, 다음 코드 조각을 사용 하 여 ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```입니다.

*7. 저 한테는 SDK 설치 및 Azure 웹 앱 확장 내 Asp.Net Core 응용 프로그램에 대 한 Application Insights를 사용 하도록 설정 하려면 사용 합니다. 새 공급자를 사용 하는 방법*

* 이전 공급자를 사용 하는 Azure 웹 앱에서 application Insights 확장 합니다. 필터링 규칙에서 수정할 수 있습니다 `appsettings.json` 응용 프로그램에 대 한 합니다. 새 공급자를 활용 하려는 경우 SDK에서 nuget 종속성을 수행 하 여 빌드 시간 계측을 사용 합니다. 이 문서는 확장 새 공급자를 사용 하도록 전환 하는 경우 업데이트 됩니다.

*8. Microsoft.Extensions.Logging.ApplicationInsights, 독립 실행형 패키지를 사용 하 고 호출 작성기에서 Application Insights 공급자를 사용 하도록 설정 합니다. AddApplicationInsights("ikey") 합니다. 구성에서 계측 키를 가져오려면 옵션 있나요?*


* 수정할 `Program.cs` 고 `appsettings.json` 아래와 같이 합니다.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>()
            .ConfigureLogging((hostingContext, logging) =>
            {
                // hostingContext.HostingEnvironment can be used to determine environments as well.
                var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                logging.AddApplicationInsights(appInsightKey);
            });
}
```

관련 섹션 `appsettings.json`

```json
{
  "myikeyfromconfig": "putrealikeyhere"
}
```

위의 코드는 독립 실행형 로깅 공급자를 사용 하는 경우에 필요 합니다. 일반 Application Insights 모니터링에 대 한 계측 키가 자동으로 로드 구성 경로에서 `ApplicationInsights:Instrumentationkey` 고 `appsettings.json` 다음과 같이 표시 됩니다.

```json
{
  "ApplicationInsights":
    {
        "Instrumentationkey":"putrealikeyhere"
    }
}
```

## <a name="next-steps"></a>다음 단계

다음에 대해 자세히 알아봅니다.

* [Asp.Net Core에 로그인](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Application Insights에서.NET 추적 로그](../../azure-monitor/app/asp-net-trace-logs.md)
