---
title: Application Insights의 이벤트 카운터 | Microsoft Docs
description: Application Insights에서 시스템 및 사용자 지정 .NET/.NET Core EventCounters 모니터링
ms.topic: conceptual
ms.date: 09/20/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3082c90f3e9f7a150206e1df8806af0de1c17024
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936489"
---
# <a name="eventcounters-introduction"></a>EventCounters 소개

`EventCounter`는 카운터 또는 통계를 게시하고 사용하는 .NET/.NET Core 메커니즘입니다. [이](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.Tracing/documentation/EventCounterTutorial.md) 문서에서는 `EventCounters`의 개요와 게시 및 사용 방법에 대한 예를 제공합니다. EventCounters는 모든 OS 플랫폼(Windows, Linux 및 macOS)에서 지원됩니다. 이는 Windows 시스템에서만 지원되는 [PerformanceCounters](/dotnet/api/system.diagnostics.performancecounter)와 달리 플랫폼 간 지원에 해당하는 것으로 볼 수 있습니다.

사용자가 요구 사항을 충족하기 위해 사용자 지정 `EventCounters`를 게시할 수 있지만 .NET Core 3.0 런타임은 기본적으로 이러한 카운터 집합을 게시합니다. 이 문서에서는 Azure Application Insights에서 `EventCounters`(시스템 정의 또는 사용자 정의)를 수집하고 확인하는 데 필요한 단계를 안내합니다.

## <a name="using-application-insights-to-collect-eventcounters"></a>Application Insights를 사용하여 EventCounters 수집

Application Insights는 새로 릴리스된 nuget 패키지 [Microsoft.ApplicationInsights.EventCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventCounterCollector)의 일부인 `EventCounterCollectionModule`을 사용한 `EventCounters` 수집을 지원합니다. `EventCounterCollectionModule`은 [AspNetCore](asp-net-core.md) 또는 [WorkerService](worker-service.md)를 사용하는 경우 자동으로 사용하도록 설정됩니다. `EventCounterCollectionModule`은 구성 불가능한 수집 빈도(60초)로 카운터를 수집합니다. EventCounters를 수집하는 데 필요한 특별 권한은 없습니다.

## <a name="default-counters-collected"></a>수집된 기본 카운터

.NET Core 3.0에서 실행되는 앱의 경우 다음 카운터가 SDK에 의해 자동으로 수집됩니다. 카운터 이름은 "범주|카운터" 형식이 됩니다.

|Category | 카운터|
|---------------|-------|
|`System.Runtime` | `cpu-usage` |
|`System.Runtime` | `working-set` |
|`System.Runtime` | `gc-heap-size` |
|`System.Runtime` | `gen-0-gc-count` |
|`System.Runtime` | `gen-1-gc-count` |
|`System.Runtime` | `gen-2-gc-count` |
|`System.Runtime` | `time-in-gc` |
|`System.Runtime` | `gen-0-size` |
|`System.Runtime` | `gen-1-size` |
|`System.Runtime` | `gen-2-size` |
|`System.Runtime` | `loh-size` |
|`System.Runtime` | `alloc-rate` |
|`System.Runtime` | `assembly-count` |
|`System.Runtime` | `exception-count` |
|`System.Runtime` | `threadpool-thread-count` |
|`System.Runtime` | `monitor-lock-contention-count` |
|`System.Runtime` | `threadpool-queue-length` |
|`System.Runtime` | `threadpool-completed-items-count` |
|`System.Runtime` | `active-timer-count` |

> [!NOTE]
> Microsoft.AspNetCore.Hosting 범주의 카운터는 ASP.NET Core 애플리케이션에서만 추가됩니다.

## <a name="customizing-counters-to-be-collected"></a>수집할 카운터 사용자 지정

다음 예에서는 카운터를 추가/제거하는 방법을 보여 줍니다. `AddApplicationInsightsTelemetry()` 또는 `AddApplicationInsightsWorkerService()`를 사용하여 Application Insights 원격 분석 수집을 사용하도록 설정한 후 애플리케이션의 `ConfigureServices` 메서드에서 이 사용자 지정을 수행합니다. ASP.NET Core 애플리케이션의 코드 예는 다음과 같습니다. 다른 유형의 애플리케이션에 대해서는 [이](worker-service.md#configuring-or-removing-default-telemetrymodules) 문서를 참조하세요.

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        // The following code shows several customizations done to EventCounterCollectionModule.
        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // This removes all default counters.
                module.Counters.Clear();

                // This adds a user defined counter "MyCounter" from EventSource named "MyEventSource"
                module.Counters.Add(new EventCounterCollectionRequest("MyEventSource", "MyCounter"));

                // This adds the system counter "gen-0-size" from "System.Runtime"
                module.Counters.Add(new EventCounterCollectionRequest("System.Runtime", "gen-0-size"));
            }
        );

        // The following code removes EventCounterCollectionModule to disable the module completely.
        var eventCounterModule = services.FirstOrDefault<ServiceDescriptor>
                    (t => t.ImplementationType == typeof(EventCounterCollectionModule));
        if (eventCounterModule != null)
        {
            services.Remove(eventCounterModule);
        }
    }
```

## <a name="event-counters-in-metric-explorer"></a>메트릭 탐색기의 이벤트 카운터

[메트릭 탐색기](../platform/metrics-charts.md)에서 EventCounter 메트릭을 보려면 Application Insights 리소스를 선택하고 로그 기반 메트릭을 메트릭 네임스페이스로 선택합니다. 그러면 EventCounter 메트릭이 사용자 지정 범주에 표시됩니다.

> [!div class="mx-imgBorder"]
> ![Application Insights에서 보고하는 이벤트 카운터](./media/event-counters/metrics-explorer-counter-list.png)

## <a name="event-counters-in-analytics"></a>Analytics의 이벤트 카운터

[Analytics](../log-query/log-query-overview.md)에서 **customMetrics** 테이블의 이벤트 카운터 보고서를 검색하고 표시할 수도 있습니다.

예를 들어 다음 쿼리를 실행하여 수집된 카운터 및 쿼리에 사용할 수 있는 카운터를 확인합니다.

```Kusto
customMetrics | summarize avg(value) by name
```

> [!div class="mx-imgBorder"]
> ![Application Insights에서 보고하는 이벤트 카운터](./media/event-counters/analytics-event-counters.png)

최근 기간 동안 특정 카운터 차트(예: `ThreadPool Completed Work Item Count`)를 가져오려면 다음 쿼리를 실행합니다.

```Kusto
customMetrics 
| where name contains "System.Runtime|ThreadPool Completed Work Item Count"
| where timestamp >= ago(1h)
| summarize  avg(value) by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```
> [!div class="mx-imgBorder"]
> ![Application Insights에서 단일 카운터의 채팅](./media/event-counters/analytics-completeditems-counters.png)

다른 원격 분석과 마찬가지로 **customMetrics**에도 앱이 실행되는 호스트 서버 인스턴스의 ID를 나타내는 `cloud_RoleInstance` 열이 있습니다. 위의 쿼리는 인스턴스당 카운터 값을 표시하며 다른 서버 인스턴스의 성능을 비교하는 데 사용할 수 있습니다.

## <a name="alerts"></a>경고
다른 메트릭과 마찬가지로 이벤트 카운터에서 지정한 제한을 벗어나는 경우 경고 메시지를 표시하도록 [경고를 설정](../platform/alerts-log.md)할 수 있습니다. [경고] 창을 열고 [경고 추가]를 클릭합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-see-eventcounters-in-live-metrics"></a>라이브 메트릭의 EventCounters를 볼 수 있나요?

라이브 메트릭은 현재 EventCounters를 표시하지 않습니다. 원격 분석을 보려면 메트릭 탐색기 또는 Analytics를 사용합니다.

### <a name="which-platforms-can-i-see-the-default-list-of-net-core-30-counters"></a>.NET Core 3.0 카운터의 기본 목록을 볼 수 있는 플랫폼은 무엇인가요?

EventCounter는 특별한 사용 권한이 필요하지 않으며 모든 플랫폼에서 지원되며 .NET Core 3.0도 지원됩니다. 다음 내용이 포함됩니다.

* **운영 체제**: Windows, Linux 또는 macOS
* **호스팅 메서드**: In process 또는 out of process
* **배포 방법**: 프레임워크 종속 또는 자체 포함
* **웹 서버**: IIS(Internet Information Server) 또는 Kestrel
* **호스팅 플랫폼**: Azure App Service, Azure VM, Docker, AKS(Azure Kubernetes Service) 등의 Web Apps 기능

### <a name="i-have-enabled-application-insights-from-azure-web-app-portal-but-i-cant-see-eventcounters"></a>Azure Web App 포털에서 Application Insights를 사용하도록 설정했습니다. 하지만 EventCounters를 볼 수 없습니다.

 ASP.NET Core용 [Application Insights 확장](./azure-web-apps.md)은 아직 이 기능을 지원하지 않습니다. 이 기능이 지원되면 이 문서가 업데이트됩니다.

## <a name="next-steps"></a><a name="next"></a>다음 단계

* [종속성 추적](./asp-net-dependencies.md)

