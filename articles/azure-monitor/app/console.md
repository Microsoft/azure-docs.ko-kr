---
title: 콘솔 애플리케이션용 Azure Application Insights | Microsoft Docs
description: 애플리케이션의 가용성, 성능 및 사용 현황을 모니터링합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/30/2019
ms.reviewer: lmolkova
ms.author: mbullwin
ms.openlocfilehash: 602cd9696271931babad9aa962638c5b646c80ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60901852"
---
# <a name="application-insights-for-net-console-applications"></a>.NET 콘솔 애플리케이션용 Application Insights
[Application Insights](../../azure-monitor/app/app-insights-overview.md)를 사용하여 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링할 수 있습니다.

[Microsoft Azure](https://azure.com)를 구독해야 합니다. Microsoft 계정으로 로그인합니다. Windows, Xbox Live 또는 기타 Microsoft 클라우드 서비스의 계정을 사용할 수 있습니다. 팀에서 Azure를 단체 구독할 수도 있습니다. 소유자에게 Microsoft 계정을 사용하여 추가해 달라고 요청하세요.

## <a name="getting-started"></a>시작

* [Azure Portal](https://portal.azure.com)에서 [Application Insights 리소스를 만듭니다](../../azure-monitor/app/create-new-resource.md ). 애플리케이션 유형으로 **일반**을 선택합니다.
* 계측 키를 복사합니다. 만든 새 리소스의 **필수** 드롭다운에서 키를 찾습니다. 
* 최신 [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) 패키지를 설치합니다.
* 원격 분석을 추적하기 전에 코드에서 계측 키를 설정합니다(또는 APPINSIGHTS_INSTRUMENTATIONKEY 환경 변수 설정). 그런 다음, 수동으로 원격 분석을 추적하고 Azure Portal에서 확인할 수 있습니다.

```csharp
TelemetryConfiguration.Active.InstrumentationKey = " *your key* ";
var telemetryClient = new TelemetryClient();
telemetryClient.TrackTrace("Hello World!");
```

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
// stamps telemetry with correlation identifiers
TelemetryConfiguration.Active.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());

// ensures proper DependencyTelemetry.Type is set for Azure RESTful API calls
TelemetryConfiguration.Active.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());
```

* .NET Framework Windows 앱의 경우 [여기](https://apmtips.com/blog/2017/02/13/enable-application-insights-live-metrics-from-code/)에 설명된 대로 성능 카운터 수집기 모듈을 설치하고 초기화할 수도 있습니다.

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
            TelemetryConfiguration configuration = TelemetryConfiguration.Active;

            configuration.InstrumentationKey = "removed";
            configuration.TelemetryInitializers.Add(new OperationCorrelationTelemetryInitializer());
            configuration.TelemetryInitializers.Add(new HttpDependenciesParsingTelemetryInitializer());

            var telemetryClient = new TelemetryClient();
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

            // flush is not blocking so wait a bit
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
            // please check default settings in https://github.com/Microsoft/ApplicationInsights-dotnet-server/blob/develop/Src/DependencyCollector/DependencyCollector/ApplicationInsights.config.install.xdt

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
* [종속성을 모니터링](../../azure-monitor/app/asp-net-dependencies.md)하여 REST, SQL 또는 다른 외부 리소스의 속도가 느려지는지 확인합니다.
* [API를 사용](../../azure-monitor/app/api-custom-events-metrics.md) 합니다.
