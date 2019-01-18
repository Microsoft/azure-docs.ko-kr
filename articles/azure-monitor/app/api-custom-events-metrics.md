---
title: 사용자 지정 이벤트 및 메트릭용 Application Insights API | Microsoft Docs
description: 디바이스 또는 데스크톱 앱, 웹 페이지, 서비스에 코드를 몇 줄 삽입하여 사용 및 진단 문제를 추적할 수 있습니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2018
ms.author: mbullwin
ms.openlocfilehash: aac5010ca6b0ed958a849bf203f1d2f80bcdb81c
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119825"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>사용자 지정 이벤트 및 메트릭용 Application Insights API

애플리케이션에 몇 줄의 코드를 삽입하여 사용자가 해당 애플리케이션으로 어떤 작업을 하는지 살펴보거나 진단 문제를 지원할 수 있습니다. 디바이스 및 데스크톱 앱, 웹 클라이언트, 웹 서버에서 원격 분석을 보낼 수 있습니다. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) 코어 원격 분석 API를 사용하여 사용자 지정 이벤트 및 메트릭 그리고 고유한 버전의 표준 원격 분석을 보냅니다. 이 API는 표준 Application Insights 데이터 수집기에서 사용하는 동일한 API입니다.

> [!NOTE]
> `TrackMetric()`은 더 이상 .NET 기반 애플리케이션에 대한 사용자 지정 메트릭을 보내기 위해 선호되는 메서드가 아닙니다. Application Insights .NET SDK [버전 2.60-베타 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3)에서는 새로운 메서드인 [`TelemetryClient.GetMetric()`](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet)이 도입되었습니다. Application Insights .NET SDK [버전 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet)를 기준으로 이 기능은 안정적인 릴리스에 포함됩니다.

## <a name="api-summary"></a>API 요약

핵심 API는 `GetMetric`(.NET만 해당)과 같은 사소한 차이를 제외하고 모든 플랫폼에서 동일합니다.

| 방법 | 용도 |
| --- | --- |
| [`TrackPageView`](#page-views) |페이지, 화면, 블레이드 또는 양식. |
| [`TrackEvent`](#trackevent) |사용자 작업 및 기타 이벤트. 사용자 동작을 추적하거나 성능을 모니터링하는 데 사용됩니다. |
| [`GetMetric`](#getmetric) |0과 다차원 메트릭, 중앙 집중식으로 구성된 집계, C#만 해당. |
| [`TrackMetric`](#trackmetric) |특정 이벤트와 관련이 없는 큐 길이와 같은 성능 측정. |
| [`TrackException`](#trackexception) |진단 예외를 기록합니다. 다른 이벤트와 관련하여 발생 위치를 추적하고 스택 추적을 검사합니다. |
| [`TrackRequest`](#trackrequest) |성능 분석에 대한 서버 요청 빈도 및 기간을 기록합니다. |
| [`TrackTrace`](#tracktrace) |진단 로그 메시지. 타사 로그도 캡처할 수 있습니다. |
| [`TrackDependency`](#trackdependency) |기간 및 빈도 앱이 종속된 외부 구성 요소에 대한 호출을 기록합니다. |

이러한 대부분의 원격 분석 호출에 [속성 및 메트릭을 연결](#properties) 할 수 있습니다.

## <a name="prep"></a>시작하기 전에

Application Insights SDK에 대한 참조가 아직 없는 경우:

* 프로젝트에 Application Insights SDK 추가:

  * [ASP.NET 프로젝트](../../azure-monitor/app/asp-net.md)
  * [Java 프로젝트](../../azure-monitor/app/java-get-started.md)
  * [Node.js 프로젝트](../../azure-monitor/app/nodejs.md)
  * [각 웹 페이지의 JavaScript](../../azure-monitor/app/javascript.md) 
* 디바이스 또는 웹 서버 코드에 다음을 포함합니다.

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>TelemetryClient 인스턴스 가져오기

`TelemetryClient`의 인스턴스 가져오기(웹 페이지의 JavaScript는 제외):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.JS*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient는 스레드로부터 안전합니다.

ASP.NET 및 Java 프로젝트의 경우 들어오는 HTTP 요청은 자동으로 캡처됩니다. 앱의 다른 모듈에 대한 TelemetryClient의 추가 인스턴스를 만들 수도 있습니다. 예를 들어 비즈니스 논리 이벤트를 보고하는 미들웨어 클래스에 TelemetryClient 인스턴스 하나가 있을 수 있습니다. 컴퓨터를 식별하려면 UserId 및 DeviceId 등의 속성을 설정할 수 있습니다. 이 정보는 인스턴스에서 보내는 모든 이벤트에 연결됩니다. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Node.js 프로젝트에서 `new applicationInsights.TelemetryClient(instrumentationKey?)`를 사용하여 새 인스턴스를 만들 수 있지만 단일 항목 `defaultClient`와 격리된 구성이 필요한 시나리오에만 권장됩니다.

## <a name="trackevent"></a>TrackEvent

Application Insights에서 *사용자 지정 이벤트*는 [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)에 집계된 개수로 표시하고 [진단 검색](../../azure-monitor/app/diagnostic-search.md)에 개별 항목으로 표시할 수 있는 데이터 요소입니다. MVC 또는 다른 프레임워크 "이벤트"와 관련이 없습니다.

다양한 이벤트를 계산하기 위해 코드에 `TrackEvent`를 삽입합니다. 사용자가 특정 기능을 얼마나 자주 선택하는지, 특정 목표를 얼마나 자주 달성하는지 또는 특정 유형의 실수를 얼마나 자주 하는지를 계산할 수 있습니다.

예를 들어 게임 앱은 사용자가 이길 때마다 이벤트를 보냅니다.

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.JS*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>분석의 사용자 지정 이벤트

[Application Insights 분석](analytics.md)의 `customEvents` 테이블에서 원격 분석을 사용할 수 있습니다. 각 행은 앱의 `trackEvent(..)` 호출을 나타냅니다.

[샘플링](../../azure-monitor/app/sampling.md)이 작동 중이면 itemCount 속성에 1보다 큰 값이 표시됩니다. 예를 들어 itemCount==10은 trackEvent()에 대한 10개 호출의 샘플링을 의미하며 샘플링 프로세스는 이 중 하나만 전송했습니다. 따라서 정확한 사용자 지정 이벤트 수를 가져오려면 `customEvents | summarize sum(itemCount)`와 같은 코드를 사용해야 합니다.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>예

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using TraceSeveretyLevel = Microsoft.ApplicationInsights.DataContracts.SeverityLevel;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .Net pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", TraceSeveretyLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric은 .NET SDK에서 더 이상 사용되지 않습니다. 메트릭은 전송되기 전에 항상 미리 집계되어야 합니다. GetMetric(..) 오버로드 중 하나를 사용하여 SDK 사전 집계 기능에 액세스하기 위한 메트릭 개체를 가져옵니다. 사용자 고유의 사전 집계 논리를 구현하는 경우 Track(ITelemetry metricTelemetry) 메서드를 사용하여 결과 집계를 보낼 수 있습니다. 애플리케이션이 시간에 따라 집계하지 않고 모든 경우에 별도의 원격 분석 항목을 전송해야 하는 경우 이벤트 원격 분석에 대한 사용 사례가 있을 수 있습니다. TelemetryClient.TrackEvent(Microsoft.Applicationlnsights.DataContracts.EventTelemetry)를 참조하세요.

Application Insights에서는 특정 이벤트에 연결되지 않은 메트릭을 차트로 작성할 수 있습니다. 예를 들어 정기적으로 큐 길이를 모니터링할 수 있습니다. 메트릭을 사용할 경우 개별 측정값보다 변형 및 추세에 좀 더 관심을 갖게 되며 통계 차트가 유용합니다.

Application Insights로 메트릭을 보내려면 `TrackMetric(..)` API를 사용할 수 있습니다. 메트릭을 전송하는 방법에는 다음 두 가지가 있습니다.

* 단일 값. 애플리케이션에서 측정을 수행할 때마다 Application Insights에 해당 값을 보냅니다. 예를 들어 컨테이너의 항목 수를 설명하는 메트릭이 있다고 가정합니다. 특정 기간 동안 먼저 컨테이너에 3개 항목을 추가한 다음 2개 항목을 제거합니다. 따라서 `TrackMetric`을 두 번 호출합니다. 처음에는 값 `3`을 전달하고 그 다음에는 값 `-2`를 전달합니다. Application Insights는 두 값을 자동으로 저장합니다.

* 집계. 메트릭을 사용하여 작업하는 경우 모든 단일 측정값은 거의 유용하지 않습니다. 대신 특정 기간 동안 발생한 내용의 요약이 중요합니다. 이러한 요약을 _집계_ 라고 합니다. 위의 예에서는 해당 기간에 대한 집계 메트릭 합계는 `1`이고 메트릭 값의 개수는 `2`입니다. 집계 방법을 사용할 때는 `TrackMetric`을 기간당 한 번만 호출하고 집계 값을 보냅니다. 이렇게 하면 모든 관련 정보를 수집하는 동안 더 적은 데이터 요소를 Application Insights로 보냄으로써 비용 및 성능 오버헤드를 상당히 줄일 수 있기 때문에 권장되는 방법입니다.

### <a name="examples"></a>예

#### <a name="single-values"></a>단일 값

단일 메트릭 값을 전송하려면

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.JS*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>분석의 사용자 지정 메트릭

[Application Insights 분석](analytics.md)의 `customMetrics` 테이블에서 원격 분석을 사용할 수 있습니다. 각 행은 앱의 `trackMetric(..)` 호출을 나타냅니다.

* `valueSum` - 측정값의 합계입니다. 평균 값을 가져오려면 `valueCount`로 나눕니다.
* `valueCount` - 이 `trackMetric(..)` 호출로 집계된 측정값의 수입니다.

## <a name="page-views"></a>페이지 보기

디바이스 또는 웹 페이지 앱에서 각 화면 또는 페이지가 로드되면 기본적으로 페이지 보기 원격 분석이 전송됩니다. 하지만 추가 시간에 또는 다른 시간에 페이지 보기를 추적하도록 변경할 수 있습니다. 예를 들어 탭 또는 블레이드를 표시하는 앱에서 사용자가 새 블레이드를 열 때마다 "페이지"를 추적할 수 있습니다.

사용자 및 세션 데이터는 페이지 보기와 함께 속성으로 전송됩니다. 따라서 페이지 보기 원격 분석이 있으면 사용자 및 세션 차트가 실시간으로 표시됩니다.

### <a name="custom-page-views"></a>사용자 지정 페이지 보기

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

여러 다른 HTML 페이지 내에 여러 탭이 있으면 URL도 지정할 수 있습니다.

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>페이지 보기 시간

기본적으로 시간은 브라우저가 요청을 보낼 때부터 브라우저의 페이지 로드 이벤트를 호출할 때까지 측정되는 **페이지 보기 로드 시간**으로 보고됩니다.

대신, 다음을 수행할 수 있습니다.

* [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) 호출에서 명시적 기간을 설정합니다. `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* 페이지 보기 시간 호출 `startTrackPage` 및 `stopTrackPage`를 사용합니다.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

첫 번째 매개 변수로 사용하는 이름은 시작 및 중지 호출을 연결합니다. 기본적으로 현재 페이지 이름이 지정됩니다.

메트릭 탐색기에 표시된 결과 페이지 로드 기간은 시작 및 중지 호출 사이의 간격에서 파생됩니다. 실제로 걸리는 시간 간격에 달려 있습니다.

### <a name="page-telemetry-in-analytics"></a>분석의 페이지 원격 분석

[분석](analytics.md)에서 두 테이블이 브라우저 작업의 데이터를 보여 줍니다.

* URL 및 페이지 제목에 대한 데이터가 포함된 `pageViews` 테이블
* 들어오는 데이터를 처리하는 데 걸리는 시간 등의 클라이언트 성능에 대한 데이터가 포함된 `browserTimings` 테이블

브라우저가 다른 페이지를 처리하는 데 걸리는 시간을 보려면

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

서로 다른 브라우저의 인기도를 검색하려면

```kusto
pageViews
| summarize count() by client_Browser
```

페이지 보기를 AJAX 호출과 상호 연결하려면(종속성과 조인)

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

서버 SDK는 TrackRequest를 사용하여 HTTP 요청을 기록합니다.

실행 중인 웹 서비스 모듈이 없는 상황에서 요청을 시뮬레이션하고 싶다면 사용자가 직접 호출할 수도 있습니다.

그러나 요청 원격 분석을 전송하는 권장 방법은 요청이 <a href="#operation-context">작업 컨텍스트</a>로 작동하는 경우입니다.

## <a name="operation-context"></a>작업 컨텍스트

원격 분석 항목을 작업 컨텍스트와 연결하여 상호 연결할 수 있습니다. 표준 요청 추적 모듈은 예외 및 HTTP 요청이 처리되는 동안 전송되는 다른 이벤트에 대해 이를 수행합니다. [검색](../../azure-monitor/app/diagnostic-search.md) 및 [분석](analytics.md)에서 작업 ID를 사용하여 요청과 연결된 모든 이벤트를 쉽게 찾을 수 있습니다.

상관 관계에 대한 자세한 내용은 [Application Insights의 원격 분석 상관 관계](../../azure-monitor/app/correlation.md)를 참조하세요.

원격 분석을 수동으로 추적할 경우 다음 패턴을 사용하여 원격 분석 상관 관계를 가장 쉽게 유지할 수 있습니다.

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

작업 컨텍스트 설정과 함께 `StartOperation`은 지정하는 유형의 원격 분석 항목을 만듭니다. 작업을 삭제할 때 또는 명시적으로 `StopOperation`을 호출하는 경우 원격 분석 항목을 보냅니다. 원격 분석 형식으로 `RequestTelemetry`를 사용하는 경우 해당 기간은 시작 및 중지 사이의 시간 제한 간격으로 설정됩니다.

작업의 범위 내에서 보고되는 원격 분석 항목은 이러한 작업의 '자식'이 됩니다. 작업 컨텍스트는 중첩될 수 있습니다.

검색에서 작업 컨텍스트는 **관련 항목** 목록을 만드는 데 사용됩니다.

![관련 항목](./media/api-custom-events-metrics/21.png)

사용자 지정 작업 추적에 대한 자세한 내용은 [Application Insights .NET SDK를 통한 사용자 지정 작업 추적](../../azure-monitor/app/custom-operations-tracking.md)을 참조하세요.

### <a name="requests-in-analytics"></a>분석의 요청

[Application Insights 분석](analytics.md)에서 요청은 `requests` 테이블에 표시됩니다.

[샘플링](../../azure-monitor/app/sampling.md)이 작동 중이면 itemCount 속성에 1보다 큰 값이 표시됩니다. 예를 들어 itemCount==10은 trackRequest()에 대한 10개 호출의 샘플링을 의미하며 샘플링 프로세스는 이 중 하나만 전송했습니다. 요청 이름별로 분할된 정확한 요청 수 및 평균 기간을 가져오려면 다음과 같은 코드를 사용합니다.

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Application Insights로 예외를 보냅니다.

* 문제의 빈도 표시로 [계산](../../azure-monitor/app/metrics-explorer.md)하려면
* [개별 항목을 검사](../../azure-monitor/app/diagnostic-search.md)하려면

보고서는 스택 추적을 포함합니다.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.JS*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

SDK에서 대부분의 예외를 자동으로 catch하므로 항상 TrackException을 명시적으로 호출할 필요는 없습니다.

* ASP.NET: [예외를 catch하는 코드 작성](../../azure-monitor/app/asp-net-exceptions.md).
* J2EE: [예외가 자동으로 catch됨](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: 예외가 자동으로 catch됨. 자동 수집을 사용하지 않도록 설정하려면 웹 페이지에 삽입하는 코드 조각에 다음 한 줄을 추가합니다.

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>분석의 예외

[Application Insights 분석](analytics.md)에서 예외는 `exceptions` 테이블에 표시됩니다.

[샘플링](../../azure-monitor/app/sampling.md)이 작동 중이면 `itemCount` 속성에 1보다 큰 값이 표시됩니다. 예를 들어 itemCount==10은 trackException()에 대한 10개 호출의 샘플링을 의미하며 샘플링 프로세스는 이 중 하나만 전송했습니다. 예외 유형별로 분할된 정확한 예외 수를 가져오려면 다음과 같은 코드를 사용합니다.

```kusto
exceptions
| summarize sum(itemCount) by type
```

대부분의 중요한 스택 정보는 이미 별도 변수로 추출되지만 좀 더 자세한 정보를 위해 `details` 구조를 분리할 수 있습니다. 이 구조는 동적이므로 원하는 유형으로 결과를 캐스트해야 합니다. 예: 

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

예외를 관련 요청과 연결하려면 다음과 같이 조인을 사용합니다.

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

이 TrackTrace를 사용하여 Application Insights에 '이동 경로 트레일'을 전송하면 문제 진단에 도움이 됩니다. 진단 데이터의 청크를 보내고 [진단 검색](../../azure-monitor/app/diagnostic-search.md)에서 검사할 수 있습니다.

.NET에서 [로그 어댑터](../../azure-monitor/app/asp-net-trace-logs.md)는 이 API를 사용하여 포털에 타사 로그를 보냅니다.

[Log4J, Logback 같은 표준 로거](../../azure-monitor/app/java-trace-logs.md)용 Java에서 Application Insights Log4j 또는 Logback Appenders를 사용하여 타사 로그를 포털로 전송합니다.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.JS*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

메시지 내용을 검색할 수 있지만 속성 값과는 달리 필터링할 수는 없습니다.

`message`의 크기 제한이 속성의 크기 제한보다 훨씬 높습니다.
TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.  

또한 메시지에 심각도 수준을 추가할 수 있습니다. 또 다른 원격 분석처럼, 다른 추적 집합에 대해 필터링 또는 검색하는 데 도움이 되는 속성 값을 추가할 수 있습니다. 예: 

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

[검색](../../azure-monitor/app/diagnostic-search.md)에서 특정 데이터베이스와 관련된 특정 심각도 수준의 모든 메시지를 쉽게 필터링할 수 있습니다.

### <a name="traces-in-analytics"></a>분석의 추적

[Application Insights 분석](analytics.md)에서 TrackTrace에 대한 호출은 `traces` 테이블에 표시됩니다.

[샘플링](../../azure-monitor/app/sampling.md)이 작동 중이면 itemCount 속성에 1보다 큰 값이 표시됩니다. 예를 들어 itemCount==10은 `trackTrace()`에 대한 10개 호출 샘플링을 의미하며 샘플링 프로세스는 이 중 하나만 전송했습니다. 따라서 정확한 추적 호출 수를 가져오려면 `traces | summarize sum(itemCount)`와 같은 코드를 사용해야 합니다.

## <a name="trackdependency"></a>TrackDependency

TrackDependency 호출을 사용하여 응답 시간과 외부 코드 부분에 대한 호출의 성공률을 추적합니다. 포털에서 종속성 차트에 결과가 나타납니다.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
     // The call above has been made obsolete in the latest SDK. The updated call follows this format:
     // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

서버 SDK는 특정 종속성 호출(예: 데이터베이스 및 REST API)을 자동으로 검색하고 추적하는 [종속성 모듈](../../azure-monitor/app/asp-net-dependencies.md)을 포함합니다. 모듈 작업을 만들기 위해 서버에 에이전트를 설치해야 합니다. 

Java에서 특정 종속성 호출은 [Java 에이전트](../../azure-monitor/app/java-agent.md)를 사용하여 자동으로 추적할 수 있습니다.

자동화된 추적에서 포착하지 않는 호출을 추적하려는 경우 또는 에이전트를 설치하지 않으려는 경우, 이 호출을 사용합니다.

C#에서 표준 종속성 추적 모듈을 해제하려면 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)를 편집하고 `DependencyCollector.DependencyTrackingTelemetryModule`에 대한 참조를 삭제합니다. 표준 종속성을 자동으로 수집하지 않으려는 경우 Java에서 Java 에이전트를 설치하지 마십시오.

### <a name="dependencies-in-analytics"></a>분석의 종속성

[Application Insights 분석](analytics.md)에서 trackDependency 호출은 `dependencies` 테이블에 표시됩니다.

[샘플링](../../azure-monitor/app/sampling.md)이 작동 중이면 itemCount 속성에 1보다 큰 값이 표시됩니다. 예를 들어 itemCount==10은 trackDependency()에 대한 10개 호출의 샘플링을 의미하며 샘플링 프로세스는 이 중 하나만 전송했습니다. 대상 구성 요소별로 분할된 정확한 종속 수를 가져오려면 다음과 같은 코드를 사용합니다.

```kusto
dependencies
| summarize sum(itemCount) by target
```

종속성을 관련 요청과 연결하려면 다음과 같이 조인을 사용합니다.

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>데이터 플러시

일반적으로 SDK는 사용자에 미치는 영향을 최소화하기 위해 선택한 시간에 데이터를 보냅니다. 그러나 버퍼를 플러시하려는 경우가 있습니다. 종료되는 애플리케이션에서 SDK를 사용하는 경우를 예로 들 수 있습니다.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.JS*

```javascript
telemetry.flush();
```

함수는 [서버 원격 분석 채널](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/)에 대해 비동기입니다.

이상적으로 애플리케이션의 종료 작업에서 플러시() 메서드를 사용해야 합니다.

## <a name="authenticated-users"></a>인증된 사용자

웹앱에서 사용자는 기본적으로 쿠키로 식별됩니다. 사용자가 다른 컴퓨터 또는 브라우저에서 앱에 액세스하거나 쿠키를 삭제하는 경우 두 번 이상 계산될 수 있습니다.

사용자가 앱에 로그인하면 브라우저 코드에서 인증된 사용자 ID를 설정하여 보다 정확한 개수를 얻을 수 있습니다.

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

ASP.NET 웹 MVC 애플리케이션에서의 예:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

사용자의 실제 로그인 이름을 사용할 필요는 없습니다. 해당 사용자에게 고유한 ID이기만 하면 됩니다. 공백이나 `,;=|` 문자를 포함할 수 없습니다.

사용자 ID도 세션 쿠키에 설정되고 서버로 전송됩니다. 서버 SDK가 설치된 경우 인증된 사용자 ID가 클라이언트 및 서버 원격 분석 둘 다에 대한 컨텍스트 속성의 일부로 전송됩니다. 필터링하고 검색할 수 있습니다.

앱이 사용자를 계정으로 그룹화하는 경우 계정 식별자를 전달할 수도 있습니다(동일한 문자 제한이 적용됨).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

[메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)에서 **사용자, 인증** 및 **사용자 계정**을 계산하는 차트를 만들 수 있습니다.

특정 사용자 이름과 계정으로 클라이언트 데이터 지점을 [검색](../../azure-monitor/app/diagnostic-search.md)할 수도 있습니다.

## <a name="properties"></a>속성을 사용하여 데이터를 필터링, 검색 및 세분화

이벤트에(그리고 메트릭, 페이지 보기, 예외 및 기타 원격 분석 데이터에) 속성 및 측정을 연결할 수 있습니다.

*속성*은 사용 현황 보고서에서 원격 분석을 필터링하는 데 사용할 수 있는 문자열 값입니다. 예를 들어 앱이 여러 게임을 제공하는 경우 각 이벤트에 게임 이름을 연결하여 인기가 더 많은 게임을 확인할 수 있습니다.

문자열 길이는 8192로 제한됩니다. 많은 양의 데이터를 보내려면 메시지 매개 변수 [TrackTrace](#track-trace)를 사용하세요.

*메트릭* 은 그래픽으로 표시할 수 있는 숫자 값입니다. 예를 들어 게이머의 획득 점수가 점진적으로 증가하는지 확인할 수 있습니다. 여러 게임에 대한 별도의 그래프 또는 누적 그래프를 볼 수 있도록 이벤트와 함께 전송된 속성을 사용하여 그래프를 분할할 수 있습니다.

메트릭 값을 올바르게 표시하려면 0보다 크거나 같아야 합니다.

[속성 수, 속성 값 및 메트릭에 사용 가능한 제한](#limits) 이 몇 가지 있습니다.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.JS*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> 속성에 개인 식별이 가능한 정보를 기록하지 않도록 주의해야 합니다.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>속성 및 메트릭을 설정하는 또 다른 방법

이벤트 매개 변수를 별도의 개체에 수집하는 방법이 더 편하다면 이 방법을 사용해도 됩니다.

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Track*()을 여러 번 호출하기 위해 같은 원격 분석 항목 인스턴스(이 예에서 `event`)를 다시 사용하지 않습니다. 그러면 원격 분석을 잘못된 구성과 함께 보낼 수 있습니다.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>분석의 사용자 지정 측정 및 속성

[분석](analytics.md)에서 사용자 지정 메트릭 및 속성은 각 원격 분석 레코드의 `customMeasurements` 및 `customDimensions` 특성에 표시됩니다.

예를 들어 요청 원격 분석에 "game"이라는 속성을 추가한 경우 다음 쿼리는 "game"의 값이 다를 때마다의 횟수를 계산하여 사용자 지정 메트릭 "score"의 평균을 표시합니다.

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

다음에 유의합니다.

* customDimensions 또는 customMeasurements JSON에서 값을 추출하면 동적 유형이므로 `tostring` 또는 `todouble`로 캐스트해야 합니다.
* [샘플링](../../azure-monitor/app/sampling.md)의 가능성을 고려하려면 `count()`가 아닌 `sum(itemCount)`을 사용해야 합니다.

## <a name="timed"></a> 타이밍 이벤트

작업을 수행하는 데 걸리는 시간을 차트로 표시하고 싶은 경우가 있습니다. 예를 들어 게임에서 사용자가 옵션을 선택하는 데 걸리는 시간을 알고 싶을 수 있습니다. 이를 위해 측정 매개 변수를 사용할 수 있습니다.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>사용자 지정 원격 분석에 대한 기본 속성

작성하는 사용자 정의 이벤트의 일부에 대해 기본 속성 값을 설정하려는 경우 TelemetryClient 인스턴스에서 설정할 수 있습니다. 설정된 값은 해당 클라이언트에서 보낸 모든 원격 분석 항목에 연결됩니다.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.JS*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

개별 원격 분석 호출이 자신의 속성 사전에 있는 기본값을 재정의할 수 있습니다.

*JavaScript 웹 클라이언트의 경우*[JavaScript 원격 분석 이니셜라이저를 사용합니다](#js-initializer).

표준 컬렉션 모듈의 데이터를 포함하여 *모든 원격 분석에 속성을 추가하려면* [`ITelemetryInitializer`를 구현합니다](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>원격 분석 샘플링, 필터링 및 처리

SDK에서 전송하기 전에 원격 분석을 처리하는 코드를 작성할 수 있습니다. 처리는 HTTP 요청 컬렉션 및 종속성 컬렉션과 같은 표준 원격 분석 모듈에서 전송된 데이터를 포함합니다.

`ITelemetryInitializer`를 구현하여 원격 분석에 [속성을 추가](../../azure-monitor/app/api-filtering-sampling.md#add-properties)합니다. 예를 들어 다른 속성에서 계산된 버전 번호 또는 값을 추가할 수 있습니다.

`ITelemetryProcesor`를 구현하여 원격 분석이 SDK에서 전송되기 전에 [필터링](../../azure-monitor/app/api-filtering-sampling.md#filtering)을 통해 원격 분석을 수정 또는 삭제할 수 있습니다. 전송 또는 삭제될 대상을 제어하지만 메트릭에 미치는 영향을 고려해야 합니다. 항목 삭제 방법에 따라 관련된 항목 사이를 이동하는 기능이 손실될 수 있습니다.

[샘플링](../../azure-monitor/app/api-filtering-sampling.md)은 앱에서 포털로 전송되는 데이터의 양을 줄이는 패키지 솔루션입니다. 표시된 메트릭에 영향을 주지 않습니다. 예외, 요청 및 페이지 뷰와 같은 관련된 항목 간을 이동하여 문제를 진단하는 기능에 영향을 주지 않습니다.

[자세히 알아보기](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>원격 분석 사용 안 함

원격 분석의 컬렉션 및 전송을 *동적으로 중지 및 시작하려면* :

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

*선택한 표준 수집기(예: 성능 카운터, HTTP 요청 또는 종속성)를 사용하지 않도록 설정*하려면 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서 관련 줄을 삭제하거나 주석으로 처리합니다. 사용자 고유의 TrackRequest 데이터를 전송하려는 경우를 예로 들 수 있습니다.

*Node.JS*

```javascript
telemetry.config.disableAppInsights = true;
```

*선택한 표준 수집기를 사용하지 않도록 설정*(예를 들어 성능 카운터, HTTP 요청 또는 종속성)하려면 초기화 시 구성 메서드를 SDK 초기화 코드로 체이닝합니다.

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

초기화 후에 이러한 수집기를 사용하지 않도록 설정하려면 구성 개체 `applicationInsights.Configuration.setAutoCollectRequests(false)`를 사용합니다.

## <a name="debug"></a>개발자 모드

디버깅하는 동안 결과를 즉시 볼 수 있도록 파이프라인을 통해 원격 분석을 신속하게 처리할 때 유용합니다. 또한 원격 분석과 관련된 모든 문제를 추적하는 데 도움이 되는 추가 메시지가 제공됩니다. 앱이 느려질 수 있으므로 프로덕션 환경에서는 끄는 것이 좋습니다.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

## <a name="ikey"></a> 선택한 사용자 지정 원격 분석에 대해 계측 키 설정

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> 동적 계측 키

개발, 테스트 및 프로덕션 환경에서 원격 분석이 섞이지 않게 방지하려면 [별도의 Application Insights 리소스를 만들고](../../azure-monitor/app/create-new-resource.md ) 환경에 따라 키를 변경하세요.

구성 파일에서 계측 키를 가져오는 대신 코드에서 설정할 수 있습니다. ASP.NET 서비스의 global.aspx.cs 같은 초기화 메서드에서 키를 설정합니다.

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

웹 페이지에서 문자 그대로 스크립트에 코딩하는 대신 웹 서버의 상태를 이용하여 설정할 수 있습니다. 예를 들어 ASP.NET 앱에서 생성된 웹 페이지에서 다음과 같이 설정합니다.

*Razor에서 JavaScript*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient에는 컨텍스트 속성이 있고, 이 속성은 모든 원격 분석 데이터와 함께 전송되는 값을 포함하고 있습니다. 일반적으로 표준 원격 분석 모듈에 의해 설정되지만 사용자가 직접 설정할 수도 있습니다. 예: 

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

이러한 값을 직접 설정하는 경우 사용자의 값과 표준 값이 혼동되지 않도록 [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서 관련 줄을 제거해야 합니다.

* **Component**: 앱 및 앱 버전입니다.
* **Device**: 앱이 실행되는 디바이스에 대한 데이터입니다. (웹앱에서 원격 분석이 전송되는 서버 또는 클라이언트 디바이스입니다.)
* **InstrumentationKey**: Azure에서 원격 분석이 표시되는 Application Insights 리소스입니다. 일반적으로 ApplicationInsights.config에서 선택합니다.
* **Location**: 디바이스의 지리적 위치입니다.
* **Operation**: 웹앱에서 현재 HTTP 요청입니다. 다른 유형의 앱에서는 이 값을 설정하여 이벤트를 그룹화할 수 있습니다.
  * **Id**: 진단 검색의 이벤트를 검사할 때 관련 항목을 찾을 수 있도록 여러 이벤트를 상호 연결하는 생성된 값입니다.
  * **Name**: 식별자이며, 일반적으로 HTTP 요청의 URL입니다.
  * **SyntheticSource**: null이거나 비어 있지 않다면 요청의 원본이 로봇 또는 웹 테스트로 확인되었음을 나타내는 문자열입니다. 기본적으로 메트릭 탐색기의 계산에서 제외됩니다.
* **Properties**: 모든 원격 분석 데이터와 함께 전송되는 속성입니다. 개별 Track* 호출에서 재정의될 수 있습니다.
* **세션**: 사용자의 세션입니다. ID는 생성된 값으로 설정되며, 사용자가 잠시 동안 비활성 상태이면 값이 변경됩니다.
* **User**: 사용자 정보입니다.

## <a name="limits"></a>제한

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

데이터 속도 제한에 도달하지 않도록 하려면 [샘플링](../../azure-monitor/app/sampling.md)을 사용합니다.

데이터 유지 기간을 결정하려면 [데이터 보존 및 개인 정보](../../azure-monitor/app/data-retention-privacy.md)를 참조하세요.

## <a name="reference-docs"></a>참조 문서

* [ASP.NET 참조](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java 참조](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK 코드

* [ASP.NET 핵심 SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server 패키지](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [모든 플랫폼](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>질문

* *Track_() 호출에서 발생할 수 있는 예외는 무엇인가요?*

    없음. try-catch 절에 래핑할 필요가 없습니다. SDK에 문제가 발생하는 경우 디버그 콘솔 출력에 메시지를 작성하고 메시지가 완료되는 경우 진단 검색에 표시됩니다.
* *포털에서 데이터를 가져오는 REST API가 있나요?*

    예, [데이터 액세스 API](https://dev.applicationinsights.io/)가 있습니다. 데이터를 추출하는 다른 방법에는 [Analytics에서 Power BI로 내보내기](../../azure-monitor/app/export-power-bi.md ) 및 [연속 내보내기](../../azure-monitor/app/export-telemetry.md)가 있습니다.

## <a name="next"></a>다음 단계

* [검색 이벤트 및 로그](../../azure-monitor/app/diagnostic-search.md)
* [문제 해결](../../azure-monitor/app/troubleshoot-faq.md)
