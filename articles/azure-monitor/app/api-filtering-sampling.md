---
title: Application Insights SDK에서 필터링 및 전처리 | Microsoft Docs
description: SDK에 대 한 원격 분석 프로세서 및 원격 분석 이니셜라이저를 작성 하 여 원격 분석을 Application Insights 포털로 보내기 전에 데이터에 속성을 필터링 하거나 추가 합니다.
ms.topic: conceptual
ms.date: 11/23/2016
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: c42b3a79e1c816e92c71e41a738bbb116a39aee1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936557"
---
# <a name="filter-and-preprocess-telemetry-in-the-application-insights-sdk"></a>Application Insights SDK에서 원격 분석 필터링 및 전처리

Application Insights SDK에 대 한 플러그 인을 작성 하 고 구성 하 여 원격 분석을 Application Insights 서비스로 보내기 전에 보강 하 고 처리할 수 있는 방법을 사용자 지정할 수 있습니다.

* [샘플링](sampling.md) 통계를 왜곡하지 않고 원격 분석의 양을 줄입니다. 문제를 진단할 때 탐색할 수 있도록 관련 데이터 요소를 함께 유지 합니다. 포털에서는 샘플링을 보완하기 위해 총 개수를 곱합니다.
* 원격 분석 프로세서를 사용 하 여 필터링 하면 SDK가 서버에 전송 되기 전에 SDK에서 원격 분석을 필터링 할 수 있습니다. 예를 들어 로봇의 요청을 제외하여 원격 분석의 양을 줄일 수 있습니다. 필터링은 샘플링 보다 트래픽을 줄이는 보다 기본적인 방법입니다. 전송 된 항목을 더 자세히 제어할 수 있지만 통계에 영향을 줍니다. 예를 들어 성공한 모든 요청을 필터링 할 수 있습니다.
* [원격 분석 이니셜라이저](#add-properties) 앱에서 전송 된 원격 분석에 속성을 추가 하거나 수정 합니다. 여기에는 표준 모듈의 원격 분석이 포함 됩니다. 예를 들어 포털에서 데이터를 필터링 하는 데 사용할 계산 된 값 또는 버전 번호를 추가할 수 있습니다.
* [SDK API](./api-custom-events-metrics.md) 사용자 지정 이벤트 및 메트릭을 보내는 데 사용됩니다.

시작하기 전에

* 응용 프로그램에 적합 한 SDK를 설치 합니다. [ASP.NET](asp-net.md), [ASP.NET Core](asp-net-core.md), [.net/.Net CORE에 대 한 비 HTTP/작업자](worker-service.md)또는 [JavaScript](javascript.md).

<a name="filtering"></a>

## <a name="filtering"></a>Filtering

이 기법을 사용 하면 원격 분석 스트림에서 포함 되거나 제외 되는 항목을 직접 제어할 수 있습니다. 필터링을 사용 하 여 Application Insights에 보낼 원격 분석 항목을 삭제할 수 있습니다. 샘플링 또는 별도로 필터링을 사용할 수 있습니다.

원격 분석을 필터링 하려면 원격 분석 프로세서를 작성 하 고에 등록 `TelemetryConfiguration` 합니다. 모든 원격 분석은 프로세서를 거칩니다. 스트림에서 해당 개체를 삭제 하거나 체인의 다음 프로세서에이를 제공 하도록 선택할 수 있습니다. HTTP 요청 수집기 및 종속성 수집기와 같은 표준 모듈의 원격 분석 및 사용자가 직접 추적 한 원격 분석이 포함 됩니다. 예를 들어 로봇 또는 성공한 종속성 호출의 요청에 대 한 원격 분석을 필터링 할 수 있습니다.

> [!WARNING]
> 프로세서를 사용 하 여 SDK에서 전송 된 원격 분석을 필터링 하면 포털에 표시 되는 통계를 왜곡 하 고 관련 항목을 따르기 어려울 수 있습니다.
>
> 대신 [샘플링](./sampling.md)사용을 고려하세요.
>
>

### <a name="create-a-telemetry-processor-c"></a>원격 분석 프로세서 만들기(C#)

1. 필터를 만들려면를 구현 `ITelemetryProcessor` 합니다.

    원격 분석 프로세서는 처리 체인을 구성 합니다. 원격 분석 프로세서를 인스턴스화하면 체인에서 다음 프로세서에 대 한 참조가 제공 됩니다. 원격 분석 데이터 요소를 프로세스 메서드에 전달 하는 경우 해당 작업을 수행한 다음 체인에서 다음 원격 분석 프로세서를 호출 하거나 호출 하지 않습니다.

    ```csharp
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
    {
        private ITelemetryProcessor Next { get; set; }

        // next will point to the next TelemetryProcessor in the chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }

        public void Process(ITelemetry item)
        {
            // To filter out an item, return without calling the next processor.
            if (!OKtoSend(item)) { return; }

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }
    }
    ```

2. 프로세서를 추가 합니다.

ASP.NET **앱**

ApplicationInsights.config에서이 코드 조각을 삽입 합니다.

```xml
<TelemetryProcessors>
  <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
     <!-- Set public property -->
     <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
  </Add>
</TelemetryProcessors>
```

클래스에서 명명된 공용 속성을 제공하여 .config 파일의 문자열 값을 전달할 수 있습니다.

> [!WARNING]
> .config 파일의 형식 이름 및 모든 속성 이름이 코드의 클래스 및 속성 이름과 일치하는지 주의하여 확인해야 합니다. .Config 파일이 존재 하지 않는 형식 또는 속성을 참조 하는 경우 SDK가 자동으로 원격 분석을 보내지 못할 수 있습니다.
>

또는 코드에서 필터를 초기화할 수 있습니다. 적절 한 초기화 클래스 (예: AppStart)에서 `Global.asax.cs` 프로세서를 체인에 삽입 합니다.

```csharp
var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
builder.Use((next) => new SuccessfulDependencyFilter(next));

// If you have more processors:
builder.Use((next) => new AnotherProcessor(next));

builder.Build();
```

이 시점 이후에 만들어진 원격 분석 클라이언트는 사용자의 프로세서를 사용 합니다.

ASP.NET **Core/Worker 서비스 앱**

> [!NOTE]
> 또는를 사용 하 여 프로세서를 추가 하는 `ApplicationInsights.config` `TelemetryConfiguration.Active` 것은 ASP.NET Core 응용 프로그램에 유효 하지 않거나, Microsoft-applicationinsights.

[ASP.NET Core](asp-net-core.md#adding-telemetry-processors) 또는 작업 [서비스](worker-service.md#adding-telemetry-processors)를 사용 하 여 작성 된 앱의 경우에는 `AddApplicationInsightsTelemetryProcessor` 표시 된 대로의 확장 메서드를 사용 하 여 새 원격 분석 프로세서를 추가 `IServiceCollection` 합니다. 이 메서드는 `ConfigureServices` 클래스의 메서드에서 호출 됩니다 `Startup.cs` .

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...
        services.AddApplicationInsightsTelemetry();
        services.AddApplicationInsightsTelemetryProcessor<SuccessfulDependencyFilter>();

        // If you have more processors:
        services.AddApplicationInsightsTelemetryProcessor<AnotherProcessor>();
    }
```

### <a name="example-filters"></a>예제 필터

#### <a name="synthetic-requests"></a>가상 요청

보트 및 웹 테스트를 필터링합니다. 메트릭 탐색기는 가상 원본을 필터링 하는 옵션을 제공 하지만이 옵션을 사용 하면 SDK 자체에서 트래픽을 필터링 하 여 트래픽 및 수집 크기를 줄일 수 있습니다.

```csharp
public void Process(ITelemetry item)
{
  if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

  // Send everything else:
  this.Next.Process(item);
}
```

#### <a name="failed-authentication"></a>실패한 인증

"401" 응답을 사용하여 요청을 필터링합니다.

```csharp
public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, return without calling the next processor.
        return;
    }

    // Send everything else
    this.Next.Process(item);
}
```

#### <a name="filter-out-fast-remote-dependency-calls"></a>빠른 원격 종속성 호출을 필터링합니다.

속도가 느려지는 호출만 진단 하려면 빠른 호출을 필터링 합니다.

> [!NOTE]
> 이 필터링은 포털에 표시 되는 통계를 왜곡 합니다.
>
>

```csharp
public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}
```

#### <a name="diagnose-dependency-issues"></a>종속성 문제 진단

[이 블로그](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) 에서는 종속성으로 정규 ping을 자동으로 전송하여 종속성 문제를 진단하는 프로젝트에 대해 설명합니다.

<a name="add-properties"></a>

### <a name="javascript-web-applications"></a>JavaScript 웹 응용 프로그램

**ITelemetryInitializer를 사용 하 여 필터링**

1. 원격 분석 이니셜라이저 콜백 함수를 만듭니다. 콜백 함수는 `ITelemetryItem` 처리 중인 이벤트 인 매개 변수로를 사용 합니다. `false`이 콜백에서 반환 하면 원격 분석 항목이 필터링 됩니다.

   ```JS
   var filteringFunction = (envelope) => {
     if (envelope.data.someField === 'tobefilteredout') {
         return false;
     }
  
     return true;
   };
   ```

2. 원격 분석 이니셜라이저 콜백을 추가 합니다.

   ```JS
   appInsights.addTelemetryInitializer(filteringFunction);
   ```

## <a name="addmodify-properties-itelemetryinitializer"></a>속성 추가/수정: ITelemetryInitializer

원격 분석 이니셜라이저를 사용 하 여 추가 정보를 통해 원격 분석을 보강 하거나 표준 원격 분석 모듈에 의해 설정 된 원격 분석 속성을 재정의 합니다.

예를 들어 웹 패키지에 대 한 Application Insights는 HTTP 요청에 대 한 원격 분석을 수집 합니다. 기본적으로 응답 코드가 >= 400 인 요청을 실패 한 것으로 플래그 합니다. 그러나 400를 성공으로 처리 하려는 경우 success 속성을 설정 하는 원격 분석 이니셜라이저를 제공할 수 있습니다.

원격 분석 이니셜라이저를 제공 하는 경우 Track * () 메서드가 호출 될 때마다 호출 됩니다. 여기에는 `Track()` 표준 원격 분석 모듈에 의해 호출 되는 메서드가 포함 됩니다. 규칙에 따라 이러한 모듈은 이미 이니셜라이저에서 설정 된 속성을 설정 하지 않습니다. 원격 분석 이니셜라이저는 원격 분석 프로세서를 호출 하기 전에 호출 됩니다. 따라서 이니셜라이저가 수행 하는 모든 강화은 프로세서에 표시 됩니다.

**이니셜라이저 정의**

*C#*

```csharp
using System;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MvcWebRole.Telemetry
{
  /*
   * Custom TelemetryInitializer that overrides the default SDK
   * behavior of treating response codes >= 400 as failed requests
   *
   */
  public class MyTelemetryInitializer : ITelemetryInitializer
  {
    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        // Is this a TrackRequest() ?
        if (requestTelemetry == null) return;
        int code;
        bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
        if (!parsed) return;
        if (code >= 400 && code < 500)
        {
            // If we set the Success property, the SDK won't change it:
            requestTelemetry.Success = true;

            // Allow us to filter these requests in the portal:
            requestTelemetry.Properties["Overridden400s"] = "true";
        }
        // else leave the SDK to set the Success property
    }
  }
}
```

ASP.NET **apps: 이니셜라이저 로드**

ApplicationInsights.config에서:

```xml
<ApplicationInsights>
  <TelemetryInitializers>
    <!-- Fully qualified type name, assembly name: -->
    <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
    ...
  </TelemetryInitializers>
</ApplicationInsights>
```

또는 Global.aspx.cs와 같은 코드에서 이니셜라이저를 인스턴스화할 수 있습니다.

```csharp
protected void Application_Start()
{
    // ...
    TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
}
```

[이 샘플](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)을 참조 하세요.

ASP.NET **Core/Worker 서비스 앱: 이니셜라이저 로드**

> [!NOTE]
> 또는를 사용 하 여 이니셜라이저를 추가 하는 `ApplicationInsights.config` `TelemetryConfiguration.Active` 것은 ASP.NET Core 응용 프로그램에 유효 하지 않거나, Microsoft-applicationinsights.

[ASP.NET Core](asp-net-core.md#adding-telemetryinitializers) 또는 작업 [서비스](worker-service.md#adding-telemetryinitializers)를 사용 하 여 작성 된 앱의 경우 표시 된 것 처럼 새 원격 분석 이니셜라이저 추가는 종속성 주입 컨테이너에 추가 하 여 수행 됩니다. 이 작업은 메서드에서 수행 됩니다 `Startup.ConfigureServices` .

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```
### <a name="javascript-telemetry-initializers"></a>JavaScript 원격 분석 이니셜라이저
*JavaScript*

포털에서 가져온 초기화 코드 바로 뒤에 원격 분석 이니셜라이저를 삽입합니다.

```JS
<script type="text/javascript">
    // ... initialization code
    ...({
        instrumentationKey: "your instrumentation key"
    });
    window.appInsights = appInsights;


    // Adding telemetry initializer.
    // This is called whenever a new telemetry item
    // is created.

    appInsights.queue.push(function () {
        appInsights.context.addTelemetryInitializer(function (envelope) {
            var telemetryItem = envelope.data.baseData;

            // To check the telemetry items type - for example PageView:
            if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                // this statement removes url from all page view documents
                telemetryItem.url = "URL CENSORED";
            }

            // To set custom properties:
            telemetryItem.properties = telemetryItem.properties || {};
            telemetryItem.properties["globalProperty"] = "boo";

            // To set custom metrics:
            telemetryItem.measurements = telemetryItem.measurements || {};
            telemetryItem.measurements["globalMetric"] = 100;
        });
    });

    // End of inserted code.

    appInsights.trackPageView();
</script>
```

원격 분석 항목에서 사용할 수 있는 noncustom 속성에 대 한 요약은 [데이터 모델 Application Insights 내보내기](./export-data-model.md)를 참조 하세요.

이니셜라이저를 원하는 수만큼 추가할 수 있습니다. 추가 된 순서 대로 호출 됩니다.

### <a name="opencensus-python-telemetry-processors"></a>OpenCensus Python 원격 분석 프로세서

OpenCensus Python의 원격 분석 프로세서는 단순히 원격 분석을 처리 하기 위해 호출 하는 콜백 함수입니다. 콜백 함수는 [봉투 (envelope](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py#L86) ) 데이터 형식을 매개 변수로 허용 해야 합니다. 원격 분석을 내보내지 않도록 필터링 하려면 콜백 함수가를 반환 하는지 확인 `False` 합니다. [GitHub](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/protocol.py)의 봉투 (envelope)에서 Azure Monitor 데이터 형식에 대 한 스키마를 볼 수 있습니다.

> [!NOTE]
> `cloud_RoleName`필드의 특성을 변경 하 여를 수정할 수 있습니다 `ai.cloud.role` `tags` .

```python
def callback_function(envelope):
    envelope.tags['ai.cloud.role'] = 'new_role_name'
```

```python
# Example for log exporter
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)

# Callback function to append '_hello' to each log message telemetry
def callback_function(envelope):
    envelope.data.baseData.message += '_hello'
    return True

handler = AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>')
handler.add_telemetry_processor(callback_function)
logger.addHandler(handler)
logger.warning('Hello, World!')
```
```python
# Example for trace exporter
import requests

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])

# Callback function to add os_type: linux to span properties
def callback_function(envelope):
    envelope.data.baseData.properties['os_type'] = 'linux'
    return True

exporter = AzureExporter(
    connection_string='InstrumentationKey=<your-instrumentation-key-here>'
)
exporter.add_telemetry_processor(callback_function)
tracer = Tracer(exporter=exporter, sampler=ProbabilitySampler(1.0))
with tracer.span(name='parent'):
response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit')
```

```python
# Example for metrics exporter
import time

from opencensus.ext.azure import metrics_exporter
from opencensus.stats import aggregation as aggregation_module
from opencensus.stats import measure as measure_module
from opencensus.stats import stats as stats_module
from opencensus.stats import view as view_module
from opencensus.tags import tag_map as tag_map_module

stats = stats_module.stats
view_manager = stats.view_manager
stats_recorder = stats.stats_recorder

CARROTS_MEASURE = measure_module.MeasureInt("carrots",
                                            "number of carrots",
                                            "carrots")
CARROTS_VIEW = view_module.View("carrots_view",
                                "number of carrots",
                                [],
                                CARROTS_MEASURE,
                                aggregation_module.CountAggregation())

# Callback function to only export the metric if value is greater than 0
def callback_function(envelope):
    return envelope.data.baseData.metrics[0].value > 0

def main():
    # Enable metrics
    # Set the interval in seconds in which you want to send metrics
    exporter = metrics_exporter.new_metrics_exporter(connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    exporter.add_telemetry_processor(callback_function)
    view_manager.register_exporter(exporter)

    view_manager.register_view(CARROTS_VIEW)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    mmap.measure_int_put(CARROTS_MEASURE, 1000)
    mmap.record(tmap)
    # Default export interval is every 15.0s
    # Your application should run for at least this amount
    # of time so the exporter will meet this interval
    # Sleep can fulfill this
    time.sleep(60)

    print("Done recording metrics")

if __name__ == "__main__":
    main()
```
원하는 수 만큼 프로세서를 추가할 수 있습니다. 추가 된 순서 대로 호출 됩니다. 한 프로세서가 예외를 throw 하는 경우 다음 프로세서에 영향을 주지 않습니다.

### <a name="example-telemetryinitializers"></a>예 TelemetryInitializers

#### <a name="add-a-custom-property"></a>사용자 지정 속성 추가

다음 샘플 이니셜라이저는 추적 된 모든 원격 분석에 사용자 지정 속성을 추가 합니다.

```csharp
public void Initialize(ITelemetry item)
{
  var itemProperties = item as ISupportProperties;
  if(itemProperties != null && !itemProperties.Properties.ContainsKey("customProp"))
    {
        itemProperties.Properties["customProp"] = "customValue";
    }
}
```

#### <a name="add-a-cloud-role-name"></a>클라우드 역할 이름 추가

다음 샘플 이니셜라이저는 클라우드 역할 이름을 추적 된 모든 원격 분석으로 설정 합니다.

```csharp
public void Initialize(ITelemetry telemetry)
{
    if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
    {
        telemetry.Context.Cloud.RoleName = "MyCloudRoleName";
    }
}
```

#### <a name="add-information-from-httpcontext"></a>HttpContext에서 정보 추가

다음 샘플 이니셜라이저는에서 데이터를 읽고 [`HttpContext`](/aspnet/core/fundamentals/http-context?view=aspnetcore-3.1) 이를 인스턴스에 추가 합니다 `RequestTelemetry` . 는 `IHttpContextAccessor` 생성자 종속성 주입을 통해 자동으로 제공 됩니다.

```csharp
public class HttpContextRequestTelemetryInitializer : ITelemetryInitializer
{
    private readonly IHttpContextAccessor httpContextAccessor;

    public HttpContextRequestTelemetryInitializer(IHttpContextAccessor httpContextAccessor)
    {
        this.httpContextAccessor =
            httpContextAccessor ??
            throw new ArgumentNullException(nameof(httpContextAccessor));
    }

    public void Initialize(ITelemetry telemetry)
    {
        var requestTelemetry = telemetry as RequestTelemetry;
        if (requestTelemetry == null) return;

        var claims = this.httpContextAccessor.HttpContext.User.Claims;
        Claim oidClaim = claims.FirstOrDefault(claim => claim.Type == "oid");
        requestTelemetry.Properties.Add("UserOid", oidClaim?.Value);
    }
}
```

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor 및 ITelemetryInitializer

원격 분석 프로세서 및 원격 분석 이니셜라이저 간의 차이는 무엇인가요?

* 이러한 작업으로 수행할 수 있는 작업에는 약간의 차이가 있습니다. 이러한 용도로 이니셜라이저를 사용 하는 것이 좋지만 원격 분석의 속성을 추가 하거나 수정 하는 데 사용할 수 있습니다.
* 원격 분석 이니셜라이저는 항상 원격 분석 프로세서 이전에 실행 됩니다.
* 원격 분석 이니셜라이저는 두 번 이상 호출 될 수 있습니다. 규칙에 따라 이미 설정 된 속성은 설정 하지 않습니다.
* 원격 분석 프로세서를 사용 하면 원격 분석 항목을 완전히 바꾸거나 삭제할 수 있습니다.
* 모든 등록 된 원격 분석 이니셜라이저가 모든 원격 분석 항목에 대해 호출 되도록 보장 됩니다. 원격 분석 프로세서의 경우 SDK는 첫 번째 원격 분석 프로세서 호출을 보장 합니다. 나머지 프로세서의 호출 여부는 이전 원격 분석 프로세서에 의해 결정 됩니다.
* 원격 분석 이니셜라이저를 사용 하 여 추가 속성을 사용 하 여 원격 분석을 보강 하거나 기존 항목을 재정의 합니다. 원격 분석 프로세서를 사용 하 여 원격 분석을 필터링 합니다.

## <a name="troubleshoot-applicationinsightsconfig"></a>문제 해결 ApplicationInsights.config

* 정규화된 형식 이름 및 어셈블리 이름이 올바른지 확인합니다.
* applicationinsights.config 파일이 출력 디렉터리에 있고 최근 변경 내용을 포함하는지 확인합니다.

## <a name="reference-docs"></a>참조 문서

* [API 개요](./api-custom-events-metrics.md)
* [ASP.NET 참조](/previous-versions/azure/dn817570(v=azure.100))

## <a name="sdk-code"></a>SDK 코드

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>다음 단계
* [검색 이벤트 및 로그](./diagnostic-search.md)
* [견본](./sampling.md)
* [문제 해결](../faq.md)

