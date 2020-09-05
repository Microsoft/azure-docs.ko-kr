---
title: 콘솔 애플리케이션용 Azure Application Insights | Microsoft Docs
description: 애플리케이션의 가용성, 성능 및 사용 현황을 모니터링합니다.
ms.topic: conceptual
ms.date: 05/21/2020
ms.custom: devx-track-csharp
ms.reviewer: lmolkova
ms.openlocfilehash: 98f1ea8a4994515b437841174ae95563ea53ed88
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933344"
---
# <a name="application-insights-for-net-console-applications"></a>.NET 콘솔 애플리케이션용 Application Insights

[Application Insights](./app-insights-overview.md)를 사용하여 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링할 수 있습니다.

[Microsoft Azure](https://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, Xbox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다. 팀에서 Azure를 단체 구독할 수도 있습니다. 소유자에게 Microsoft 계정을 사용하여 추가해 달라고 요청하세요.

> [!NOTE]
> 콘솔 응용 프로그램에 대해서는 [여기에 나와](./worker-service.md) 있는 관련 지침과 [Microsoft applicationinsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WorkerService) 를 사용 하는 것이 *좋습니다* . 이 패키지 [`NetStandard2.0`](/dotnet/standard/net-standard) 는를 대상으로 하므로 .Net Core 2.1 이상에서 사용할 수 있으며 .NET Framework 4.7.2 이상에서 사용할 수 있습니다.

## <a name="getting-started"></a>시작

* [Azure Portal](https://portal.azure.com)에서 [Application Insights 리소스를 만듭니다](./create-new-resource.md). 애플리케이션 유형으로 **일반**을 선택합니다.
* 계측 키를 복사합니다. 만든 새 리소스의 **필수** 드롭다운에서 키를 찾습니다.
* 최신 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) 패키지를 설치합니다.
* 원격 분석을 추적하기 전에 코드에서 계측 키를 설정합니다(또는 APPINSIGHTS_INSTRUMENTATIONKEY 환경 변수 설정). 그런 다음, 수동으로 원격 분석을 추적하고 Azure Portal에서 확인할 수 있습니다.

```csharp
// you may use different options to create configuration as shown later in this article
TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
configuration.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient(configuration);
telemetryClient.TrackTrace("Hello World!");
```

> [!NOTE]
> 원격 분석은 즉시 전송되지 않습니다. 원격 분석 항목은 ApplicationInsights SDK에 의해 일괄 처리되고 전송됩니다. `Track()` 메서드를 호출한 직후에 종료되는 콘솔 앱에서는 이 문서의 뒷부분에 있는 [전체 예제](#full-example)에 표시된 것처럼 앱이 종료되기 전에 `Flush()` 및 `Sleep`/`Delay`가 수행되지 않는 한 원격 분석을 보낼 수 없습니다. `InMemoryChannel`을 사용하는 경우 `Sleep`은 필요하지 않습니다. 여기에서 추적되는 `Sleep`에 대한 요구 사항과 관련된 문제가 있습니다. [ApplicationInsights-dotnet/issues/407](https://github.com/microsoft/ApplicationInsights-dotnet/issues/407)


* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) 패키지의 최신 버전을 설치합니다. 이 패키지는 HTTP, SQL 또는 기타 외부 종속성 호출을 자동으로 추적합니다.

코드를 통해 또는 `ApplicationInsights.config` 파일을 사용하여 Application Insights를 초기화하고 구성할 수 있습니다. 가능한 한 조기에 초기화를 수행해야 합니다. 

> [!NOTE]
> **ApplicationInsights.config**에 대한 지침은 .NET Framework를 대상으로 하는 앱에만 적용되고 .NET Core 애플리케이션에는 적용되지 않습니다.

### <a name="using-config-file"></a>구성 파일 사용

기본적으로 Application Insights SDK는 `TelemetryConfiguration`을 만들 때 작업 디렉터리에서 `ApplicationInsights.config` 파일을 찾습니다.

```csharp
TelemetryConfiguration config = TelemetryConfiguration.Active; // Reads ApplicationInsights.config file if present
```

구성 파일의 경로를 지정할 수도 있습니다.

```csharp
using System.IO;
TelemetryConfiguration configuration = TelemetryConfiguration.CreateFromConfiguration(File.ReadAllText("C:\\ApplicationInsights.config"));
var telemetryClient = new TelemetryClient(configuration);
```

자세한 내용은 [구성 파일 참조](configuration-with-applicationinsights-config.md)를 참조하세요.

[Microsoft.ApplicationInsights.WindowsServer](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer) 패키지의 최신 버전을 설치하면 구성 파일의 전체 예를 확인할 수 있습니다. 다음은 코드 예제와 동일한 종속 컬렉션의 **최소** 구성입니다.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>Your Key</InstrumentationKey>
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.HttpDependenciesParsingTelemetryInitializer, Microsoft.AI.DependencyCollector"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">
      <ExcludeComponentCorrelationHttpHeadersOnDomains>
        <Add>core.windows.net</Add>
        <Add>core.chinacloudapi.cn</Add>
        <Add>core.cloudapi.de</Add>
        <Add>core.usgovcloudapi.net</Add>
        <Add>localhost</Add>
        <Add>127.0.0.1</Add>
      </ExcludeComponentCorrelationHttpHeadersOnDomains>
      <IncludeDiagnosticSourceActivities>
        <Add>Microsoft.Azure.ServiceBus</Add>
        <Add>Microsoft.Azure.EventHubs</Add>
      </IncludeDiagnosticSourceActivities>
    </Add>
  </TelemetryModules>
  <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel"/>
</ApplicationInsights>

```

### <a name="configuring-telemetry-collection-from-code"></a>코드에서 원격 분석 컬렉션 구성
> [!NOTE]
> .NET Core에서는 구성 파일 읽기가 지원되지 않습니다. [ASP.NET Core용 Application Insights SDK](./asp-net-core.md)를 사용하는 것을 고려할 수 있습니다.

* 애플리케이션이 시작하는 동안 `DependencyTrackingTelemetryModule` 인스턴스를 만들고 구성합니다. 이 인스턴스는 싱글톤이어야 하며 애플리케이션 수명 동안 유지되어야 합니다.

```csharp
var module = new DependencyTrackingTelemetryModule();

// prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
//...

// enable known dependency tracking, note that in future versions, we will extend this list. 
// please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");
//....

// initialize the module
module.Initialize(configuration);
```

* 공용 원격 분석 이니셜라이저 추가

```csharp
// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

일반 `TelemetryConfiguration()` 생성자를 사용하여 구성을 만든 경우 상관 관계 지원을 추가로 사용하도록 설정해야 합니다. `TelemetryConfiguration.CreateDefault()` 또는 `TelemetryConfiguration.Active`를 사용하여 파일에서 구성을 읽는 경우에는 **필요하지 않습니다**.

```csharp
configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
```

* [여기](https://apmtips.com/posts/2017-02-13-enable-application-insights-live-metrics-from-code/)에 설명된 대로 성능 카운터 수집기 모듈을 설치하고 초기화할 수도 있습니다.


#### <a name="full-example"></a>전체 예제

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DependencyCollector;
using Microsoft.ApplicationInsights.Extensibility;
using System.Net.Http;
using System.Threading.Tasks;

namespace ConsoleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient(configuration);
            using (InitializeDependencyTracking(configuration))
            {
                // run app...

                telemetryClient.TrackTrace("Hello World!");

                using (var httpClient = new HttpClient())
                {
                    // Http dependency is automatically tracked!
                    httpClient.GetAsync("https://microsoft.com").Wait();
                }

            }

            // before exit, flush the remaining data
            telemetryClient.Flush();

            // flush is not blocking when not using InMemoryChannel so wait a bit. There is an active issue regarding the need for `Sleep`/`Delay`
            // which is tracked here: https://github.com/microsoft/ApplicationInsights-dotnet/issues/407
            Task.Delay(5000).Wait();

        }

        static DependencyTrackingTelemetryModule InitializeDependencyTracking(TelemetryConfiguration configuration)
        {
            var module = new DependencyTrackingTelemetryModule();

            // prevent Correlation Id to be sent to certain endpoints. You may add other domains as needed.
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.windows.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.chinacloudapi.cn");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.cloudapi.de");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("core.usgovcloudapi.net");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("localhost");
            module.ExcludeComponentCorrelationHttpHeadersOnDomains.Add("127.0.0.1");

            // enable known dependency tracking, note that in future versions, we will extend this list. 
            // please check default settings in https://github.com/microsoft/ApplicationInsights-dotnet-server/blob/develop/WEB/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.ServiceBus");
            module.IncludeDiagnosticSourceActivities.Add("Microsoft.Azure.EventHubs");

            // initialize the module
            module.Initialize(configuration);

            return module;
        }
    }
}

```

## <a name="next-steps"></a>다음 단계
* [종속성을 모니터링](./asp-net-dependencies.md)하여 REST, SQL 또는 다른 외부 리소스의 속도가 느려지는지 확인합니다.
* [API를 사용](./api-custom-events-metrics.md) 합니다.

