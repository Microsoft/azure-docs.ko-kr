---
title: Azure Application Insights 원격 분석 상관 관계 | Microsoft 문서
description: Application Insights 원격 분석 상관 관계
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/07/2019
ms.reviewer: sergkanz
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: b48b02d20ed3d0b731f04d2c6568274bc0262e2e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933361"
---
# <a name="telemetry-correlation-in-application-insights"></a>Application Insights의 원격 분석 상관 관계

마이크로 서비스의 세계에서 모든 논리 작업은 서비스의 다양한 구성 요소에서 수행되어야 합니다. [Application Insights](../../azure-monitor/app/app-insights-overview.md)를 사용 하 여 이러한 각 구성 요소를 개별적으로 모니터링할 수 있습니다. Application Insights는 오류 또는 성능 저하를 일으키는 구성 요소를 검색하는 데 사용하는 분산된 원격 분석 상관 관계를 지원합니다.

이 문서에서는 여러 구성 요소에서 보낸 원격 분석의 상관 관계를 지정하기 위해 Application Insights에서 사용되는 데이터 모델에 대해 설명합니다. 컨텍스트 전파 기술 및 프로토콜을 다룹니다. 또한 다양 한 언어 및 플랫폼에 대 한 상관 관계 전략의 구현을 다룹니다.

## <a name="data-model-for-telemetry-correlation"></a>원격 분석 상관 관계에 대한 데이터 모델

Application Insights는 분산 원격 분석 상관 관계에 대한 [데이터 모델](../../azure-monitor/app/data-model.md)을 정의합니다. 원격 분석을 논리 작업과 연결 하기 위해 모든 원격 분석 항목에는 라는 컨텍스트 필드가 `operation_Id` 있습니다. 이 식별자는 분산 추적의 모든 원격 분석 항목에서 공유됩니다. 따라서 단일 계층에서 원격 분석이 손실 되더라도 다른 구성 요소에서 보고 된 원격 분석을 연결할 수 있습니다.

일반적으로 분산 논리 작업은 구성 요소 중 하나에서 처리 되는 요청에 해당 하는 작은 작업 집합으로 구성 됩니다. 이러한 작업은 [요청 원격 분석](../../azure-monitor/app/data-model-request-telemetry.md)에서 정의됩니다. 모든 요청 원격 분석 항목에는 `id` 고유 하 고 전역적으로 식별 하는 자체가 있습니다. 요청과 연결 된 모든 원격 분석 항목 (예: 추적 및 예외)은를 `operation_parentId` 요청 값으로 설정 해야 합니다 `id` .

모든 나가는 작업(예: 다른 구성 요소에 대한 HTTP 호출)은 [종속성 원격 분석](../../azure-monitor/app/data-model-dependency-telemetry.md)으로 표시됩니다. 종속성 원격 분석은 `id` 전역적으로 고유한 자체도 정의 합니다. 이 종속성 호출을 통해 시작된 요청 원격 분석은 이 `id`를 `operation_parentId`로 사용합니다.

`operation_Id`, `operation_parentId` 및 `request.id`를 `dependency.id`와 함께 사용하여 분산 논리 작업의 보기를 빌드할 수 있습니다. 또한 이러한 필드는 원격 분석 호출의 인과 관계 순서를 정의합니다.

마이크로 서비스 환경에서 구성 요소의 추적은 다른 스토리지 항목으로 이동할 수 있습니다. 모든 구성 요소에는 자체의 계측 키가 Application Insights에 있을 수 있습니다. 논리 작업에 대 한 원격 분석을 가져오기 위해 Application Insights는 모든 저장소 항목의 데이터를 쿼리 합니다. 저장소 항목 수가 클 경우 다음을 찾을 위치에 대 한 힌트가 필요 합니다. Application Insights 데이터 모델에서는 이 문제를 해결하기 위해 두 가지 필드, 즉 `request.source` 및 `dependency.target`을 정의합니다. 첫 번째 필드는 종속성 요청을 시작한 구성 요소를 식별 합니다. 두 번째 필드는 종속성 호출의 응답을 반환한 구성 요소를 식별 합니다.

## <a name="example"></a>예

예제를 살펴보겠습니다. 주식 가격 이라는 응용 프로그램은 Stock 이라는 외부 API를 사용 하 여 재고의 현재 시장 가격을 보여 줍니다. 주식 가격 응용 프로그램에는를 사용 하 여 클라이언트 웹 브라우저에서 열리는 스톡 페이지 라는 페이지가 있습니다 `GET /Home/Stock` . 응용 프로그램은 HTTP 호출을 사용 하 여 스톡 API를 쿼리 합니다 `GET /api/stock/value` .

쿼리를 실행하여 결과 원격 분석을 분석할 수 있습니다.

```kusto
(requests | union dependencies | union pageViews)
| where operation_Id == "STYz"
| project timestamp, itemType, name, id, operation_ParentId, operation_Id
```

결과에서 모든 원격 분석 항목은 루트 `operation_Id`를 공유합니다. 페이지에서 Ajax 호출이 수행 되 면 새 고유 ID ( `qJSXU` )가 종속성 원격 분석에 할당 되 고 페이지 보기의 id가로 사용 됩니다 `operation_ParentId` . 그러면 서버 요청에서 Ajax ID를 `operation_ParentId`로 사용합니다.

| itemType   | name                      | ID           | operation_ParentId | operation_Id |
|------------|---------------------------|--------------|--------------------|--------------|
| pageView   | Stock page                |              | STYz               | STYz         |
| dependency | GET /Home/Stock           | qJSXU        | STYz               | STYz         |
| request    | GET Home/Stock            | KqKwlrSt9PA= | qJSXU              | STYz         |
| dependency | GET /api/stock/value      | bBrf2L7mm2g= | KqKwlrSt9PA=       | STYz         |

외부 서비스를 호출 하는 경우 `GET /api/stock/value` 필드를 적절 하 게 설정할 수 있도록 해당 서버의 id를 알고 있어야 합니다 `dependency.target` . 외부 서비스에서 모니터링을 지원하지 않는 경우 `target`은 서비스의 호스트 이름으로 설정됩니다(예: `stock-prices-api.com`). 그러나 서비스에서 미리 정의 된 HTTP 헤더를 반환 하 여 자신을 식별 하는 경우 `target` 는 해당 서비스에서 원격 분석을 쿼리하여 분산 추적을 작성할 Application Insights 수 있는 서비스 id를 포함 합니다.

## <a name="correlation-headers"></a>상관 관계 헤더

Application Insights를 정의 하는 [W3C 추적-컨텍스트로](https://w3c.github.io/trace-context/)전환 하는 중입니다.

- `traceparent`: 전역 고유 작업 ID와 호출의 고유 식별자를 전달 합니다.
- `tracestate`: 시스템 특정 추적 컨텍스트를 전달 합니다.

최신 버전의 Application Insights SDK는 추적 컨텍스트 프로토콜을 지원 하지만이를 옵트인 (opt in) 해야 할 수도 있습니다. Application Insights SDK에서 지 원하는 이전 상관 관계 프로토콜과의 이전 버전과의 호환성이 유지 됩니다.

[요청 Id 라고도 하는 상관 관계 HTTP 프로토콜](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)은 더 이상 사용 되지 않습니다. 이 프로토콜은 두 가지 헤더를 정의 합니다.

- `Request-Id`: 호출의 guid (globally unique ID)를 전달 합니다.
- `Correlation-Context`: 분산 추적 속성의 이름-값 쌍 컬렉션을 전달 합니다.

또한 Application Insights은 상관 관계 HTTP 프로토콜에 대 한 [확장](https://github.com/lmolkova/correlation/blob/master/http_protocol_proposal_v2.md) 을 정의 합니다. `Request-Context` 이름-값 쌍을 사용하여 즉각적인 호출자 또는 호출 수신자에서 사용하는 속성의 컬렉션을 전파합니다. Application Insights SDK는이 헤더를 사용 하 여 `dependency.target` 및 필드를 설정 합니다 `request.source` .

### <a name="enable-w3c-distributed-tracing-support-for-classic-aspnet-apps"></a>클래식 ASP.NET 앱에 W3C 분산 추적 지원을 사용하도록 설정
 
  > [!NOTE]
  >  및 부터는 `Microsoft.ApplicationInsights.Web` `Microsoft.ApplicationInsights.DependencyCollector` 구성이 필요 하지 않습니다.

W3C 추적-컨텍스트 지원은 이전 버전과 호환 되는 방식으로 구현 됩니다. 상관 관계는 W3C를 지원 하지 않는 이전 버전의 SDK를 사용 하 여 계측 된 응용 프로그램에서 작동 합니다.

레거시 프로토콜을 계속 사용 하려는 경우 `Request-Id` 다음 구성을 사용 하 여 추적 컨텍스트를 사용 하지 않도록 설정할 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행 하는 경우 추적을 업데이트 하거나 다음 구성을 적용 하 여 추적 컨텍스트를 사용 하도록 설정 하는 것이 좋습니다.
이 기능은 `Microsoft.ApplicationInsights.Web` 및 `Microsoft.ApplicationInsights.DependencyCollector` 패키지에서 2.8.0-beta1 버전부터 사용할 수 있습니다.
기본적으로 사용하지 않도록 설정되어 있습니다. 이 기능을 사용 하도록 설정 하려면 `ApplicationInsights.config` 다음과 같이 변경 합니다.

- 에서 `RequestTrackingTelemetryModule` 요소를 추가 하 `EnableW3CHeadersExtraction` 고 해당 값을로 설정 `true` 합니다.
- 에서 `DependencyTrackingTelemetryModule` 요소를 추가 하 `EnableW3CHeadersInjection` 고 해당 값을로 설정 `true` 합니다.
- `W3COperationCorrelationTelemetryInitializer`아래 `TelemetryInitializers` 에를 추가 합니다. 이 예는 다음과 같습니다.

```xml
<TelemetryInitializers>
  <Add Type="Microsoft.ApplicationInsights.Extensibility.W3C.W3COperationCorrelationTelemetryInitializer, Microsoft.ApplicationInsights"/>
   ...
</TelemetryInitializers>
```

### <a name="enable-w3c-distributed-tracing-support-for-aspnet-core-apps"></a>ASP.NET Core 앱에 W3C 분산 추적 지원을 사용하도록 설정

 > [!NOTE]
  > `Microsoft.ApplicationInsights.AspNetCore`버전 2.8.0부터 구성이 필요 하지 않습니다.
 
W3C 추적-컨텍스트 지원은 이전 버전과 호환 되는 방식으로 구현 됩니다. 상관 관계는 W3C를 지원 하지 않는 이전 버전의 SDK를 사용 하 여 계측 된 응용 프로그램에서 작동 합니다.

레거시 프로토콜을 계속 사용 하려는 경우 `Request-Id` 다음 구성을 사용 하 여 추적 컨텍스트를 사용 하지 않도록 설정할 수 있습니다.

```csharp
  Activity.DefaultIdFormat = ActivityIdFormat.Hierarchical;
  Activity.ForceDefaultIdFormat = true;
```

이전 버전의 SDK를 실행 하는 경우 추적을 업데이트 하거나 다음 구성을 적용 하 여 추적 컨텍스트를 사용 하도록 설정 하는 것이 좋습니다.

이 기능은 `Microsoft.ApplicationInsights.AspNetCore` 버전 2.5.0-beta1 및 `Microsoft.ApplicationInsights.DependencyCollector` 버전 2.8.0-beta1에 있으며,
기본적으로 사용하지 않도록 설정되어 있습니다. 사용하도록 설정하려면 `ApplicationInsightsServiceOptions.RequestCollectionOptions.EnableW3CDistributedTracing`을 `true`로 설정합니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddApplicationInsightsTelemetry(o => 
        o.RequestCollectionOptions.EnableW3CDistributedTracing = true );
    // ....
}
```

### <a name="enable-w3c-distributed-tracing-support-for-java-apps"></a>Java 앱에 W3C 분산 추적 지원을 사용하도록 설정

#### <a name="java-30-agent"></a>Java 3.0 에이전트

  Java 3.0 에이전트는 기본으로 W3C를 지원 하며 추가 구성은 필요 하지 않습니다. 

#### <a name="java-sdk"></a>Java SDK
- **들어오는 구성**

  - Java EE 앱의 경우 ApplicationInsights.xml의 태그에 다음을 추가 합니다 `<TelemetryModules>` .

    ```xml
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule>
       <Param name = "W3CEnabled" value ="true"/>
       <Param name ="enableW3CBackCompat" value = "true" />
    </Add>
    ```
    
  - 스프링 부팅 앱의 경우 다음 속성을 추가 합니다.

    - `azure.application-insights.web.enable-W3C=true`
    - `azure.application-insights.web.enable-W3C-backcompat-mode=true`

- **나가는 구성**

  AI-Agent.xml에 다음을 추가합니다.

  ```xml
  <Instrumentation>
    <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
    </BuiltIn>
  </Instrumentation>
  ```

  > [!NOTE]
  > 이전 버전과의 호환성 모드는 기본적으로 사용하도록 설정되어 있으며, `enableW3CBackCompat` 매개 변수는 선택 사항입니다. 이전 버전과의 호환성을 해제하려는 경우에만 사용하세요.
  >
  > 모든 서비스가 W3C 프로토콜을 지원하는 최신 버전의 SDK로 업데이트되었으면 이 기능을 해제하는 것이 가장 좋습니다. 가능한 한 빨리 최신 SDK로 이동하는 것이 좋습니다.

> [!IMPORTANT]
> 들어오고 나가는 구성이 정확히 동일한 지 확인 합니다.

### <a name="enable-w3c-distributed-tracing-support-for-web-apps"></a>웹 앱에 대해 W3C distributed tracing 지원 사용

이 기능은에 `Microsoft.ApplicationInsights.JavaScript` 있습니다. 기본적으로 사용하지 않도록 설정되어 있습니다. 사용 하도록 설정 하려면 config를 사용 `distributedTracingMode` 합니다. AI_AND_W3C은 Application Insights에서 계측 하는 레거시 서비스와 이전 버전과의 호환성을 위해 제공 됩니다.

- **npm 설치 (조각 설치를 사용 하는 경우 무시)**

  ```javascript
  import { ApplicationInsights, DistributedTracingModes } from '@microsoft/applicationinsights-web';

  const appInsights = new ApplicationInsights({ config: {
    instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
    distributedTracingMode: DistributedTracingModes.W3C
    /* ...other configuration options... */
  } });
  appInsights.loadAppInsights();
  ```
  
- **조각 설치 (npm 설치를 사용 하는 경우 무시)**

  ```
  <script type="text/javascript">
  var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){i[e]=function(){var n=arguments;i.queue.push(function(){i[e].apply(i,n)})}}var i={config:e};i.initialize=!0;var a=document,t=window;setTimeout(function(){var n=a.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",a.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{i.cookie=a.cookie}catch(e){}i.queue=[],i.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var o="Track"+r[0];if(n("start"+o),n("stop"+o),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var s=t[r];t[r]=function(e,n,a,t,o){var c=s&&s(e,n,a,t,o);return!0!==c&&i["_"+r]({message:e,url:n,lineNumber:a,columnNumber:t,error:o}),c},e.autoExceptionInstrumented=!0}return i}
  (
    {
      instrumentationKey:"INSTRUMENTATION_KEY",
      distributedTracingMode: 2 // DistributedTracingModes.W3C
      /* ...other configuration options... */
    }
  );
  window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
  </script>
  ```

## <a name="opentracing-and-application-insights"></a>OpenTracing 및 Application Insights

[OpenTracing 데이터 모델 사양](https://opentracing.io/)과 Application Insights 데이터 모델은 다음과 같은 방식으로 매핑됩니다.

| Application Insights                   | OpenTracing                                        |
|------------------------------------    |-------------------------------------------------    |
| `Request`, `PageView`                  | `Span`(`span.kind = server` 사용)                    |
| `Dependency`                           | `Span`(`span.kind = client` 사용)                    |
| `Request` 및 `Dependency`의 `Id`     | `SpanId`                                            |
| `Operation_Id`                         | `TraceId`                                           |
| `Operation_ParentId`                   | `ChildOf` 유형의 `Reference`(상위 범위)     |

자세한 내용은 [Application Insights 원격 분석 데이터 모델](../../azure-monitor/app/data-model.md)을 참조 하세요.

OpenTracing 개념에 대 한 정의는 OpenTracing [사양](https://github.com/opentracing/specification/blob/master/specification.md) 및 [의미 체계 규칙](https://github.com/opentracing/specification/blob/master/semantic_conventions.md)을 참조 하세요.

## <a name="telemetry-correlation-in-opencensus-python"></a>OpenCensus Python의 원격 분석 상관 관계

OpenCensus Python은 `OpenTracing` 앞에서 설명한 데이터 모델 사양을 따릅니다. 또한 구성을 요구 하지 않고 [W3C 추적 컨텍스트를](https://w3c.github.io/trace-context/) 지원 합니다.

### <a name="incoming-request-correlation"></a>들어오는 요청 상관 관계

OpenCensus Python은 요청 자체에서 생성 된 범위에 대 한 들어오는 요청에서 W3C 추적 컨텍스트 헤더와 상관 관계를 합니다. OpenCensus는 이러한 인기 있는 웹 응용 프로그램 프레임 워크 (Flask, Django 및 피라미드형)의 통합을 통해이 작업을 자동으로 수행 합니다. W3C 추적 컨텍스트 헤더를 [올바른 형식](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format) 으로 채운 다음 요청을 통해 보내야 합니다. 다음은이를 보여 주는 샘플 Flask 응용 프로그램입니다.

```python
from flask import Flask
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.ext.flask.flask_middleware import FlaskMiddleware
from opencensus.trace.samplers import ProbabilitySampler

app = Flask(__name__)
middleware = FlaskMiddleware(
    app,
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(rate=1.0),
)

@app.route('/')
def hello():
    return 'Hello World!'

if __name__ == '__main__':
    app.run(host='localhost', port=8080, threaded=True)
```

이 코드는 로컬 컴퓨터에서 샘플 Flask 응용 프로그램을 실행 하 고 포트를 수신 대기 `8080` 합니다. 추적 컨텍스트의 상관 관계를 위해 끝점에 요청을 보냅니다. 이 예제에서는 명령을 사용할 수 있습니다 `curl` .
```
curl --header "traceparent: 00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01" localhost:8080
```
[추적 컨텍스트 헤더 형식을](https://www.w3.org/TR/trace-context/#trace-context-http-headers-format)살펴보면 다음 정보를 얻을 수 있습니다.

`version`: `00`

`trace-id`: `4bf92f3577b34da6a3ce929d0e0e4736`

`parent-id/span-id`: `00f067aa0ba902b7`

`trace-flags`: `01`

Azure Monitor 전송 된 요청 항목을 살펴보면 추적 헤더 정보를 사용 하 여 채워진 필드를 볼 수 있습니다. Azure Monitor Application Insights 리소스의 로그 (분석)에서이 데이터를 찾을 수 있습니다.

![로그에서 원격 분석 요청 (분석)](./media/opencensus-python/0011-correlation.png)

`id`필드는 형식으로 되어 `<trace-id>.<span-id>` 있습니다. 여기서는 `trace-id` 요청에 전달 된 추적 헤더에서 가져오고는 `span-id` 이 범위에 대해 생성 된 8 바이트 배열입니다.

`operation_ParentId`필드는 형식입니다 `<trace-id>.<parent-id>` `trace-id` . 여기서 및는 `parent-id` 요청에 전달 된 추적 헤더에서 가져옵니다.

### <a name="log-correlation"></a>로그 상관 관계

OpenCensus Python을 사용 하면 로그 레코드에 추적 ID, 범위 ID 및 샘플링 플래그를 추가 하 여 로그의 상관 관계를 지정할 수 있습니다. OpenCensus [로깅 통합](https://pypi.org/project/opencensus-ext-logging/)을 설치 하 여 이러한 특성을 추가 합니다. 다음 특성은 Python `LogRecord` 개체 `traceId` , `spanId` 및에 추가 `traceSampled` 됩니다. 이는 통합 후에 생성 된로 거에 대해서만 적용 됩니다.

이를 보여 주는 샘플 응용 프로그램은 다음과 같습니다.

```python
import logging

from opencensus.trace import config_integration
from opencensus.trace.samplers import AlwaysOnSampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['logging'])
logging.basicConfig(format='%(asctime)s traceId=%(traceId)s spanId=%(spanId)s %(message)s')
tracer = Tracer(sampler=AlwaysOnSampler())

logger = logging.getLogger(__name__)
logger.warning('Before the span')
with tracer.span(name='hello'):
    logger.warning('In the span')
logger.warning('After the span')
```
이 코드가 실행 되 면 콘솔에 다음이 출력 됩니다.
```
2019-10-17 11:25:59,382 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 Before the span
2019-10-17 11:25:59,384 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=70da28f5a4831014 In the span
2019-10-17 11:25:59,385 traceId=c54cb1d4bbbec5864bf0917c64aeacdc spanId=0000000000000000 After the span
```
`spanId`범위 내에 있는 로그 메시지에 대 한가 존재 합니다. 이는 `spanId` 이라는 범위에 속하는 것과 같습니다 `hello` .

를 사용 하 여 로그 데이터를 내보낼 수 있습니다 `AzureLogHandler` . 자세한 내용은 [이 문서](./opencensus-python.md#logs)를 참조하세요.

## <a name="telemetry-correlation-in-net"></a>.NET의 원격 분석 상관 관계

시간이 지남에 따라 .NET은 원격 분석 및 진단 로그를 상호 연결 하는 여러 방법을 정의 했습니다.

- `System.Diagnostics.CorrelationManager`[Logicaloperationstack 및 활동](/dotnet/api/system.diagnostics.correlationmanager?view=netcore-3.1)id를 추적할 수 있습니다.
- `System.Diagnostics.Tracing.EventSource` 및 ETW(Windows용 이벤트 추적)는 [SetCurrentThreadActivityId](/dotnet/api/system.diagnostics.tracing.eventsource.setcurrentthreadactivityid?view=netcore-3.1#overloads) 메서드를 정의합니다.
- `ILogger`[로그 범위](/aspnet/core/fundamentals/logging#log-scopes)를 사용 합니다.
- WCF(Windows Communication Foundation) 및 HTTP는 "현재" 컨텍스트 전파를 연결합니다.

그러나 이러한 메서드는 자동 분산 추적 지원을 사용 하지 않았습니다. `DiagnosticSource` 자동 컴퓨터 간 상관 관계를 지원 합니다. .NET 라이브러리는 `DiagnosticSource` HTTP와 같은 전송을 통해 상관 관계 컨텍스트의 자동 크로스 컴퓨터 전파를 지원 하 고 허용 합니다.

의 [활동 사용자 가이드](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) 에서는 `DiagnosticSource` 추적 활동의 기본 사항을 설명 합니다.

ASP.NET Core 2.0에서는 HTTP 헤더 추출을 지원 하 고 새 작업을 시작할 수 있습니다.

`System.Net.Http.HttpClient`4.1.0 버전부터는 상관 관계 HTTP 헤더의 자동 삽입 및 HTTP 호출을 활동으로 추적 하는 기능을 지원 합니다.

클래식 ASP.NET에는 새로운 HTTP 모듈인 [TelemetryCorrelation](https://www.nuget.org/packages/Microsoft.AspNet.TelemetryCorrelation/)이 있습니다. 이 모듈은 `DiagnosticSource`를 사용하여 원격 분석 상관 관계를 구현하며, 들어오는 요청 헤더에 기반한 활동을 시작합니다. 또한 모든 인터넷 정보 서비스 (IIS) 처리가 다른 관리 되는 스레드에서 실행 되는 경우에도 요청 처리의 여러 단계에서 원격 분석의 상관 관계를 설정 합니다.

Application Insights SDK는 버전 2.4.0-beta1부터 `DiagnosticSource` 및 `Activity`를 사용하여 원격 분석을 수집하고 현재 활동과 연결합니다.

<a name="java-correlation"></a>
## <a name="telemetry-correlation-in-java"></a>Java의 원격 분석 상관 관계

Java [에이전트](./java-in-process-agent.md) 및 [java SDK](../../azure-monitor/app/java-get-started.md) 버전 2.0.0 이상에서는 원격 분석의 자동 상관 관계를 지원 합니다. `operation_id`요청 범위 내에서 실행 되는 모든 원격 분석 (예: 추적, 예외, 사용자 지정 이벤트)을 자동으로 채웁니다. 또한 [JAVA SDK 에이전트가](../../azure-monitor/app/java-agent.md) 구성 된 경우 HTTP를 통해 서비스 간 호출에 대 한 상관 관계 헤더 (앞에서 설명한)를 전파 합니다.

> [!NOTE]
> Application Insights Java 에이전트는 JMS, Kafka, Netty/Webflux 등에 대 한 요청 및 종속성을 자동으로 수집 합니다. Java SDK의 경우 Apache HttpClient를 통해 수행 된 호출만 상관 관계 기능에 대해 지원 됩니다. 메시징 기술 (예: Kafka, RabbitMQ 및 Azure Service Bus) 간 자동 컨텍스트 전파는 SDK에서 지원 되지 않습니다. 

> [!NOTE]
> 사용자 지정 원격 분석을 수집 하려면 Java 2.6 SDK를 사용 하 여 응용 프로그램을 계측 해야 합니다. 

### <a name="role-names"></a>역할 이름

[응용 프로그램 맵에](../../azure-monitor/app/app-map.md)구성 요소 이름이 표시 되는 방식을 사용자 지정할 수 있습니다. 이렇게 하려면 `cloud_RoleName` 다음 작업 중 하나를 수행 하 여를 수동으로 설정할 수 있습니다.

- Application Insights Java agent 3.0의 경우 클라우드 역할 이름을 다음과 같이 설정 합니다.

    ```json
    {
      "instrumentationSettings": {
        "preview": {
          "roleName": "my cloud role name"
        }
      }
    }
    ```
    환경 변수를 사용 하 여 클라우드 역할 이름을 설정할 수도 있습니다 `APPLICATIONINSIGHTS_ROLE_NAME` .

- Application Insights Java SDK 2.5.0 이상에서는 `cloud_RoleName` ApplicationInsights.xml 파일에를 추가 하 여를 지정할 수 있습니다 `<RoleName>` .

  ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
     <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
     <RoleName>** Your role name **</RoleName>
     ...
  </ApplicationInsights>
  ```

- Application Insights 스프링 부팅 스타터를 사용 하 여 스프링 부팅을 사용 하는 경우 응용 프로그램에 대 한 사용자 지정 이름을 응용 프로그램의 속성 파일에 설정 하기만 하면 됩니다.

  `spring.application.name=<name-of-app>`

  스프링 부팅 스타터는 `cloudRoleName` 속성에 대해 입력 하는 값에 자동으로 할당 `spring.application.name` 됩니다.

## <a name="next-steps"></a>다음 단계

- [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md)을 작성 합니다.
- ASP.NET Core 및 ASP.NET의 고급 상관 관계 시나리오는 [사용자 지정 작업 추적](custom-operations-tracking.md)을 참조 하세요.
- 다른 SDK의 [cloud_RoleName 설정](./app-map.md#set-or-override-cloud-role-name)에 대해 자세히 알아봅니다.
- Application Insights에서 마이크로 서비스의 모든 구성 요소를 온보딩합니다. [지원되는 플랫폼](./platforms.md)을 확인합니다.
- Application Insights 유형에 대한 [데이터 모델](./data-model.md)을 참조합니다.
- [원격 분석을 확장 및 필터링](./api-filtering-sampling.md)하는 방법을 알아봅니다.
- [Application Insights 구성 참조](configuration-with-applicationinsights-config.md)를 검토합니다.
