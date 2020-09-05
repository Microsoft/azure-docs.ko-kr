---
title: Application Insights에서 .NET 추적 로그 탐색
description: 추적, NLog 또는 Log4Net에서 생성된 로그를 검색합니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/08/2019
ms.openlocfilehash: c17f1f4eb8d2fb680f31a42009247b25c74941c9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936455"
---
# <a name="explore-netnet-core-and-python-trace-logs-in-application-insights"></a>Application Insights에서 .NET/.NET Core 및 Python 추적 로그 검색

ILogger, NLog, log4Net 또는 System.Diagnostics.Trace의 ASP.NET/ASP.NET Core 애플리케이션에 대한 진단 추적 로그를 [Azure 애플리케이션 Insights][start]로 보냅니다. Python 애플리케이션의 경우 Azure Monitor용 OpenCensus Python에서 AzureLogHandler를 사용하여 진단 추적 로그를 보냅니다. 그런 다음, 탐색하고 검색할 수 있습니다. 해당 로그가 애플리케이션의 다른 로그 파일과 병합되므로 각 사용자 요청과 연결된 추적을 식별하고 다른 이벤트 및 예외 보고서와 상호 연결할 수 있습니다.

> [!NOTE]
> 로그 캡처 모듈이 필요한가요? 타사 로거에 대한 유용한 어댑터입니다. 그러나 NLog, log4Net 또는 System.Diagnostics.Trace를 사용하지 않는 경우 [**Application Insights TrackTrace()** ](./api-custom-events-metrics.md#tracktrace)를 직접 호출하는 것이 좋습니다.
>
>
## <a name="install-logging-on-your-app"></a>앱에 대한 로깅 설치
프로젝트에 선택한 로깅 프레임워크를 설치합니다. 그러면 app.config 또는 web.config에 항목이 생성됩니다.

```XML
 <configuration>
  <system.diagnostics>
    <trace>
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
</configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>로그를 수집하도록 Application Insights 구성
[프로젝트에 Application Insights를 추가](./asp-net.md)하지 않은 경우 지금 추가합니다. 로그 수집기를 포함하는 옵션이 나타납니다.

또는 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하여 **Application Insights를 구성**합니다. **추적 컬렉션 구성** 옵션을 선택합니다.

> [!NOTE]
> Application Insights 메뉴 또는 로그 수집기 옵션이 없습니까? [문제 해결](#troubleshooting)을 시도해 보세요.

## <a name="manual-installation"></a>수동 설치
프로젝트 형식이 Application Insights 설치 관리자에서 지원되지 않는 경우(예: Windows 데스크톱 프로젝트) 이 방법을 사용합니다.

1. Log4Net 또는 NLog를 사용하려는 경우 프로젝트에 설치합니다.
2. 솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다.
3. "Application Insights"를 검색합니다.
4. 다음 패키지 중 하나를 선택합니다.

   - ILogger의 경우: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - NLog의 경우: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Log4Net의 경우: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - System.Diagnostics의 경우: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

NuGet 패키지는 필요한 어셈블리를 설치하고 해당하는 경우 web.config 또는 app.config를 수정합니다.

## <a name="ilogger"></a>ILogger

콘솔 애플리케이션 및 ASP.NET Core와 함께 Application Insights ILogger 구현을 사용하는 예제는 [ApplicationInsightsLoggerProvider for .NET Core ILogger logs](ilogger.md)를 참조하세요.

## <a name="insert-diagnostic-log-calls"></a>진단 로그 호출 삽입
System.Diagnostics.Trace를 사용하는 경우 일반적인 호출 방법은 다음과 같습니다.

```csharp
System.Diagnostics.Trace.TraceWarning("Slow response - database01");
```

log4net 또는 NLog를 원하는 경우 다음을 사용합니다.

```csharp
    logger.Warn("Slow response - database01");
```

## <a name="use-eventsource-events"></a>EventSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.Tracing.EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1) 이벤트를 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EventSourceListener` NuGet 패키지를 설치합니다. 그런 다음 [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 파일의 `TelemetryModules` 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **Name**은 수집할 EventSource의 이름을 지정합니다.
 * **Level**은 수집할 로깅 수준을 지정합니다. *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* 또는 *Warning*
 * **Keywords**(선택 사항)는 사용할 키워드 정수 값 조합을 지정합니다.

## <a name="use-diagnosticsource-events"></a>DiagnosticSource 이벤트 사용
Application Insights에 추적으로 보낼 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) 이벤트를 구성할 수 있습니다. 먼저 [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener) NuGet 패키지를 설치합니다. 그런 다음, [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 파일의 "TelemetryModules" 섹션을 편집합니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

추적하려는 각 DiagnosticSource에 대해 **Name** 특성 세트가 포함된 항목을 DiagnosticSource 이름에 추가합니다.

## <a name="use-etw-events"></a>ETW 이벤트 사용
추적으로 Application Insights에 전송될 ETW(Windows용 이벤트 추적) 이벤트를 구성할 수 있습니다. 먼저 `Microsoft.ApplicationInsights.EtwCollector` NuGet 패키지를 설치합니다. 그런 다음, [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) 파일의 "TelemetryModules" 섹션을 편집합니다.

> [!NOTE] 
> ETW 이벤트는 SDK를 호스트하는 프로세스가 성능 로그 사용자 또는 관리자의 구성원인 ID에서 실행되는 경우에만 수집할 수 있습니다.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

각 원본에 대해 다음 매개 변수를 설정할 수 있습니다.
 * **ProviderName**은 수집할 ETW 공급자의 이름입니다.
 * **ProviderGuid**는 수집할 ETW 공급자의 GUID를 지정합니다. `ProviderName` 대신 사용할 수 있습니다.
 * **Level**은 수집할 로깅 수준을 설정합니다. *Critical*, *Error*, *Informational*, *LogAlways*, *Verbose* 또는 *Warning*일 수 있습니다.
 * **Keywords**(선택 사항)는 사용할 키워드 정수 값 조합을 설정합니다.

## <a name="use-the-trace-api-directly"></a>직접 추적 API 사용
Application Insights 추적 API를 직접 호출할 수 있습니다. 로깅 어댑터는 이 API를 사용합니다.

다음은 그 예입니다.

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow response - database01");
```

TrackTrace의 장점은 메시지에 상대적으로 긴 데이터를 넣을 수 있습니다. 예를 들어, POST 데이터를 인코딩할 수 있습니다.

메시지에 심각도 수준을 추가할 수도 있습니다. 또한 다른 원격 분석처럼, 다른 추적 세트에 대해 필터링 또는 검색하는 데 도움이 되는 속성 값을 추가할 수 있습니다. 다음은 그 예입니다.

  ```csharp
  var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
  telemetry.TrackTrace("Slow database response",
                 SeverityLevel.Warning,
                 new Dictionary<string,string> { {"database", db.ID} });
  ```

이를 통해 [Search][diagnostic]에서 특정 데이터베이스와 관련된 특정 심각도 수준의 모든 메시지를 쉽게 필터링할 수 있습니다.

## <a name="azureloghandler-for-opencensus-python"></a>OpenCensus Python용 AzureLogHandler
Azure Monitor 로그 처리기를 사용하여 Python 로그를 Azure Monitor로 내보낼 수 있습니다.

Azure Monitor에 대한 [OpenCensus Python SDK](./opencensus-python.md)를 사용하여 애플리케이션을 계측합니다.

이 예제에서는 Azure Monitor에 경고 수준 로그를 보내는 방법을 보여 줍니다.

```python
import logging

from opencensus.ext.azure.log_exporter import AzureLogHandler

logger = logging.getLogger(__name__)
logger.addHandler(AzureLogHandler(connection_string='InstrumentationKey=<your-instrumentation_key-here>'))
logger.warning('Hello, World!')
```

## <a name="explore-your-logs"></a>로그 탐색
디버그 모드에서 앱을 실행하거나 라이브로 배포합니다.

[Application Insights 포털][portal]의 앱 개요 창에서 [검색][diagnostic]을 선택합니다.

예를 들어 다음을 수행할 수 있습니다.

* 특정 속성이 있는 로그 추적 또는 항목을 필터링합니다.
* 특정 항목을 자세히 검사합니다.
* 동일한 사용자 요청에 관련된 다른 시스템 로그 데이터를 찾습니다(동일한 OperationId가 있음).
* 페이지의 구성을 즐겨찾기로 저장합니다.

> [!NOTE]
>애플리케이션이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에 대해 Application Insights SDK를 사용하는 경우 *적응 샘플링* 기능이 작동하여 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](./sampling.md)
>

## <a name="troubleshooting"></a>문제 해결
### <a name="how-do-i-do-this-for-java"></a>Java의 경우 이 작업을 어떻게 수행하나요?
Java 코드리스 계측(권장)에서 로그는 기본적으로 수집되고 [Java 3.0 에이전트](./java-in-process-agent.md)를 사용합니다.

Java SDK를 사용하는 경우 [Java 로그 어댑터](./java-trace-logs.md)를 사용합니다.

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>프로젝트 상황에 맞는 메뉴에 Application Insights 옵션이 없습니다.
* Developer Analytics Tools가 개발 머신에 설치되어 있는지 확인합니다. Visual Studio **도구** > **확장 및 업데이트**에서 **Developer Analytics Tools**를 찾습니다. **설치됨** 탭에 없는 경우 **온라인** 탭을 열고 설치합니다.
* Developer Analytics Tools에서 지원하지 않는 프로젝트 형식일 수 있습니다. [수동 설치](#manual-installation)를 사용합니다.

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>구성 도구에 로그 어댑터 옵션이 없습니다.
* 먼저 로깅 프레임워크를 설치합니다.
* System.Diagnostics.Trace를 사용하는 경우 [*web.config*에서 구성](/dotnet/api/system.diagnostics.eventlogtracelistener?view=dotnet-plat-ext-3.1)했는지 확인합니다.
* 최신 버전의 Application Insights가 설치되어 있는지 확인합니다. Visual Studio에서 **도구** > **확장 및 업데이트**로 이동하고, **업데이트** 탭을 엽니다. **Developer Analytics Tools**가 있으면 선택하여 업데이트합니다.

### <a name="i-get-the-instrumentation-key-cannot-be-empty-error-message"></a><a name="emptykey"></a>"계측 키는 비워 둘 수 없습니다." 오류 메시지가 표시됨
Application Insights를 설치하지 않고 로깅 어댑터 Nuget 패키지를 설치했을 수 있습니다. 솔루션 탐색기에서 *ApplicationInsights.config*를 마우스 오른쪽 단추로 클릭하고, **Application Insights 업데이트**를 선택합니다. Azure에 로그인하고 Application Insights 리소스를 만들거나 기존 리소스를 다시 사용하라는 메시지가 표시됩니다. 이렇게 하면 문제가 해결됩니다.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>진단 검색에 추적은 보이지만 다른 이벤트가 보이지 않음
모든 이벤트와 요청이 파이프라인을 통과할 때까지 시간이 걸릴 수 있습니다.

### <a name="how-much-data-is-retained"></a><a name="limits"></a>얼마나 많은 데이터가 보존되나요?
여러 가지 요인이 보관되는 데이터의 양에 영향을 줍니다. 자세한 내용은 고객 이벤트 메트릭 페이지의 [제한](./api-custom-events-metrics.md#limits) 섹션을 참조하세요.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>예상한 일부 로그 항목이 표시되지 않음
애플리케이션이 대량의 데이터를 전송하고 ASP.NET 버전 2.0.0-beta3 또는 그 이상에 대해 Application Insights SDK를 사용하는 경우 적응 샘플링 기능이 작동하여 원격 분석의 일부만 보낼 수 있습니다. [샘플링에 대해 자세히 알아봅니다.](./sampling.md)

## <a name="next-steps"></a><a name="add"></a>다음 단계

* [ASP.NET의 실패 및 예외 진단][exceptions]
* [검색에 대한 자세한 정보][diagnostic]
* [가용성 및 응답성 테스트 설정][availability]
* [문제 해결][qna]

<!--Link references-->

[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[start]: ./app-insights-overview.md

