---
title: Azure Functions 모니터링
description: Azure Functions에서 Azure 애플리케이션 Insights를 사용 하 여 함수 실행을 모니터링 하는 방법에 대해 알아봅니다.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: dda62e3041d04d5becc9179fff1c56d0c587ba1e
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76292929"
---
# <a name="monitor-azure-functions"></a>Azure Functions 모니터링

[Azure Functions](functions-overview.md) 는 함수를 모니터링 하기 위한 [Azure 애플리케이션 Insights](../azure-monitor/app/app-insights-overview.md) 와의 기본 제공 통합을 제공 합니다. 이 문서에서는 Application Insights에 시스템 생성 로그 파일을 보내도록 Azure Functions를 구성 하는 방법을 보여 줍니다.

로그, 성능 및 오류 데이터를 수집 하기 때문에 Application Insights를 사용 하는 것이 좋습니다. 성능 변칙을 자동으로 감지 하 고, 문제를 진단 하 고 함수 사용 방법을 이해 하는 데 도움이 되는 강력한 분석 도구를 포함 합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다. 로컬 함수 앱 프로젝트 개발 중에도 Application Insights을 사용할 수 있습니다. 자세한 내용은 [Application Insights 란?](../azure-monitor/app/app-insights-overview.md)을 참조 하세요.

필요한 Application Insights 계측은 Azure Functions에 기본 제공 되므로 함수 앱을 Application Insights 리소스에 연결 하는 데 유효한 계측 키만 있으면 됩니다.

## <a name="application-insights-pricing-and-limits"></a>Application Insights 가격 책정 및 제한

무료로 함수 앱과 Application Insights 통합을 사용해 볼 수 있습니다. 무료로 처리할 수 있는 데이터의 양에 대한 일일 제한이 있습니다. 테스트 하는 동안이 제한에 도달할 수 있습니다. Azure는 일일 한도에 가까워지면 포털 및 이메일 알림을 제공합니다. 이러한 경고를 놓친 후 한도에 도달 하면 Application Insights 쿼리에 새 로그가 표시 되지 않습니다. 불필요 한 문제 해결 시간을 방지 하기 위해 제한 사항을 알고 있어야 합니다. 자세한 내용은 [Application Insights에서 가격 책정 및 데이터 볼륨 관리](../azure-monitor/app/pricing.md)를 참조하세요.

함수 앱에 사용할 수 있는 Application Insights 기능의 전체 목록은 [지원 되는 Azure Functions 기능에 대해 Application Insights](../azure-monitor/app/azure-functions-supported-features.md)에 자세히 설명 되어 있습니다.

## <a name="enable-application-insights-integration"></a>Application Insights 통합 사용

함수 앱이 Application Insights로 데이터를 보내려면 Application Insights 리소스의 계측 키를 알고 있어야 합니다. 이 키는 **APPINSIGHTS_INSTRUMENTATIONKEY**라는 앱 설정에 있어야 합니다.

### <a name="new-function-app-in-the-portal"></a>포털의 새 함수 앱

[Azure Portal에서 함수 앱을 만들](functions-create-first-azure-function.md)때 Application Insights 통합은 기본적으로 사용 하도록 설정 됩니다. Application Insights 리소스는 함수 앱과 동일한 이름을 가지 며 동일한 지역 또는 가장 가까운 지역에 생성 됩니다.

생성 되는 Application Insights 리소스를 검토 하려면 해당 리소스를 선택 하 여 **Application Insights** 창을 확장 합니다. **새 리소스 이름을** 변경 하거나 데이터를 저장 하려는 [Azure 지리](https://azure.microsoft.com/global-infrastructure/geographies/) 에서 다른 **위치** 를 선택할 수 있습니다.

![함수 앱을 만들 때 Application Insights 사용](media/functions-monitoring/enable-ai-new-function-app.png)

**만들기**를 선택 하면 응용 프로그램 설정에서 `APPINSIGHTS_INSTRUMENTATIONKEY` 설정 된 함수 앱을 사용 하 여 Application Insights 리소스가 생성 됩니다. 모든 것이 준비 되었습니다.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>기존 함수 앱에 추가 

[Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md)또는 [Visual Studio Code](functions-create-first-function-vs-code.md)를 사용 하 여 함수 앱을 만들 때 Application Insights 리소스를 만들어야 합니다. 그런 다음 함수 앱에서 응용 프로그램 설정으로 해당 리소스의 계측 키를 추가할 수 있습니다.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

초기 버전의 함수에서는 기본 제공 모니터링을 사용 했으며이는 더 이상 권장 되지 않습니다. 이러한 함수 앱에 대한 Application Insights 통합을 사용 하도록 설정 하는 경우 [기본 제공 로깅도 사용 하지 않도록 설정](#disable-built-in-logging)해야 합니다.  

## <a name="view-telemetry-in-monitor-tab"></a>모니터 탭에서 원격 분석 보기

[Application Insights 통합을 사용 하도록 설정](#enable-application-insights-integration)하면 **모니터** 탭에서 원격 분석 데이터를 볼 수 있습니다.

1. 함수 앱 페이지에서 Application Insights 구성 된 후 한 번 이상 실행 된 함수를 선택 합니다. 그런 다음 **모니터** 탭을 선택 합니다.

   ![모니터 탭 선택](media/functions-monitoring/monitor-tab.png)

1. 함수 호출 목록이 나타날 때까지 주기적으로 **새로 고침** 을 선택 합니다.

   원격 분석 클라이언트가 서버로 전송 하기 위해 데이터를 일괄 처리 하는 동안 목록이 표시 되는 데 최대 5 분 정도 걸릴 수 있습니다. 지연은 [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)에는 적용 되지 않습니다. 해당 서비스는 페이지를 로드할 때 함수 호스트에 연결되므로 로그가 페이지에 직접 스트리밍됩니다.)

   ![호출 목록](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. 특정 함수 호출에 대한 로그를 보려면 해당 호출에 대한 **날짜** 열 링크를 선택합니다.

   ![호출 정보 링크](media/functions-monitoring/invocation-details-link-ai.png)

   해당 호출에 대한 로깅 출력이 새 페이지에 나타납니다.

   ![호출 정보](media/functions-monitoring/invocation-details-ai.png)

두 페이지에 데이터를 검색 하는 Application Insights Analytics 쿼리에 대한 **실행 Application Insights** 링크가 있음을 볼 수 있습니다.

![Application Insights에서 실행](media/functions-monitoring/run-in-ai.png)

다음 쿼리가 표시 됩니다. 쿼리 결과가 최근 30 일 (`where timestamp > ago(30d)`)으로 제한 되는 것을 볼 수 있습니다. 또한 결과에 20 개 이하의 행 (`take 20`)이 표시 됩니다. 이와 대조적으로 함수에 대한 호출 세부 정보 목록은 지난 30 일 동안 제한 없이 사용할 수 있습니다.

![Application Insights 분석 호출 목록](media/functions-monitoring/ai-analytics-invocation-list.png)

자세한 내용은 이 문서의 뒷부분에 나오는 [원격 분석 데이터 쿼리](#query-telemetry-data)를 참조하세요.

## <a name="view-telemetry-in-application-insights"></a>Application Insights에서 원격 분석 보기

Azure Portal의 함수 앱에서 Application Insights를 열려면 함수 앱의 **개요** 페이지로 이동 합니다. **구성 된 기능**아래에서 **Application Insights**를 선택 합니다.

![함수 앱 개요 페이지에서 Application Insights를 엽니다.](media/functions-monitoring/ai-link.png)

Application Insights 사용 방법에 대한 자세한 내용은 [Application Insights 설명서](https://docs.microsoft.com/azure/application-insights/)를 참조하세요. 이 섹션에서는 Application Insights에서 데이터를 보는 방법에 대한 몇 가지 예를 보여줍니다. Application Insights에 이미 익숙한 경우 [원격 분석 데이터를 구성 하 고 사용자 지정 하는 방법에 대한 섹션](#configure-categories-and-log-levels)으로 바로 이동할 수 있습니다.

![Application Insights 개요 탭](media/functions-monitoring/metrics-explorer.png)

함수의 동작, 성능 및 오류를 평가할 때 다음과 같은 Application Insights 영역을 유용 하 게 사용할 수 있습니다.

| 탭 | Description |
| ---- | ----------- |
| **[있어](../azure-monitor/app/asp-net-exceptions.md)** |  함수 오류 및 서버 예외를 기반으로 차트와 경고를 만듭니다. **작업 이름**은 함수 이름입니다. 종속성에 대한 사용자 지정 원격 분석을 구현 하지 않으면 종속성의 실패가 표시 되지 않습니다. |
| **[성능도](../azure-monitor/app/performance-counters.md)** | 성능 문제를 분석 합니다. |
| **서버** | 서버당 리소스 사용률 및 처리량을 확인 합니다. 이 데이터는 함수 때문에 기본 리소스가 정체되는 시나리오를 디버깅하는 데 유용할 수 있습니다. 서버를 **클라우드 역할 인스턴스**라고도 합니다. |
| **[메트릭](../azure-monitor/app/metrics-explorer.md)** | 메트릭을 기반으로 하는 차트 및 경고를 만듭니다. 메트릭에는 함수 호출 수, 실행 시간 및 성공률이 포함 됩니다. |
| **[라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)** | 거의 실시간으로 생성 된 메트릭 데이터를 봅니다. |

## <a name="query-telemetry-data"></a>원격 분석 데이터 쿼리

[Application Insights 분석](../azure-monitor/app/analytics.md) 을 사용 하면 데이터베이스의 테이블 형식으로 모든 원격 분석 데이터에 액세스할 수 있습니다. Analytics는 데이터를 추출, 조작 및 시각화하는 쿼리 언어를 제공합니다.

![Analytics 선택](media/functions-monitoring/select-analytics.png)

![Analytics 예제](media/functions-monitoring/analytics-traces.png)

다음 쿼리 예제는 지난 30분간의 작업자당 요청 분포를 보여줍니다.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

사용할 수 있는 테이블이 왼쪽의 **스키마** 탭에 표시 됩니다. 다음 테이블에서 함수 호출에 의해 생성된 데이터를 찾을 수 있습니다.

| Table | Description |
| ----- | ----------- |
| **traces** | 런타임과 함수 코드에 의해 생성 된 로그입니다. |
| **requests** | 각 함수 호출에 대해 하나의 요청 |
| **exceptions** | 런타임에 의해 throw 되는 예외입니다. |
| **customMetrics** | 성공 및 실패 한 호출 수, 성공률 및 기간입니다. |
| **customEvents** | 런타임에 의해 추적 되는 이벤트 (예: 함수를 트리거하는 HTTP 요청)입니다. |
| **performanceCounters** | 함수가 실행 되 고 있는 서버의 성능에 대한 정보입니다. |

다른 테이블은 가용성 테스트와 클라이언트 및 브라우저 원격 분석에 대한 것입니다. 사용자 지정 원격 분석을 구현하여 테이블에 데이터를 추가할 수 있습니다.

각 테이블 내에서 일부 Functions 관련 데이터는 `customDimensions` 필드에 있습니다.  예를 들어 다음 쿼리는 로그 수준이 `Error`인 모든 추적을 검색합니다.

```
traces 
| where customDimensions.LogLevel == "Error"
```

런타임은 `customDimensions.LogLevel` 및 `customDimensions.Category` 필드를 제공 합니다. 함수 코드에 작성 하는 로그에 추가 필드를 제공할 수 있습니다. 이 문서 뒷부분의 [구조적 로깅](#structured-logging)을 참조하세요.

## <a name="configure-categories-and-log-levels"></a>범주 및 로그 수준 구성

사용자 지정 구성 없이 Application Insights를 사용할 수 있습니다. 기본 구성으로 인해 많은 양의 데이터가 생성 될 수 있습니다. Visual Studio Azure 구독을 사용하는 경우 Application Insights에 대한 데이터 제한에 도달할 수 있습니다. 이 문서의 뒷부분에서는 함수가 Application Insights로 보내는 데이터를 구성 하 고 사용자 지정 하는 방법에 대해 알아봅니다. 함수 앱의 경우 로깅은 [호스트 json] 파일에 구성 됩니다.

### <a name="categories"></a>범주

Azure Functions 로거에는 모든 로그에 대한 *범주*가 포함되어 있습니다. 범주는 런타임 코드 또는 함수 코드의 어느 부분이 로그를 작성했는지를 나타냅니다. 

함수 런타임은 "Host"로 시작 하는 범주를 사용 하 여 로그를 만듭니다. 버전 1.x에서 `function started`, `function executed`및 `function completed` 로그에는 `Host.Executor`범주가 있습니다. 2\.x 버전부터 이러한 로그에는 `Function.<YOUR_FUNCTION_NAME>`범주가 있습니다.

함수 코드에서 로그를 작성 하는 경우 범주는 함수 런타임의 버전 1.x에서 `Function` 됩니다. 버전 2.x에서 범주는 `Function.<YOUR_FUNCTION_NAME>.User`입니다.

### <a name="log-levels"></a>로그 수준

또한 Azure Functions로 거에는 모든 로그와 *로그 수준이* 포함 됩니다. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel)은 열거형이며, 정수 코드는 상대적 중요도를 나타냅니다.

|LogLevel    |코드|
|------------|---|
|추적       | 0 |
|디버그       | 1 |
|정보 | 2 |
|경고     | 3 |
|오류       | 4 |
|위험    | 5 |
|없음        | 6 |

로그 수준 `None`은 다음 섹션에 설명되어 있습니다. 

### <a name="log-configuration-in-hostjson"></a>호스트의 로그 구성

[호스트 json] 파일은 함수 앱이 Application Insights로 보내는 로깅의 양을 구성합니다. 각 범주에 대해 보낼 최소 로그 수준을 나타낼 수 있습니다. 두 가지 예를 들면, 첫 번째 예제에서는 버전 2.x를 대상으로 [하](functions-versions.md#version-2x) 고 .net Core를 사용 하 여 .net Core를 사용 하 고 두 번째 예제는 버전 1.x 런타임에 대한 것입니다.

### <a name="version-2x-and-higher"></a>2\.x 이상 버전

버전 v2. x 이상 버전의 함수 런타임은 [.Net Core 로깅 필터 계층 구조](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)를 사용 합니다. 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>버전 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

이 예제에서는 다음 규칙을 설정합니다.

* 범주가 `Host.Results` 또는 `Function`인 로그의 경우 `Error` 수준 이상만 Application Insights로 보냅니다. `Warning` 수준 이하 로그는 무시됩니다.
* `Host.Aggregator` 범주의 로그는 모든 로그를 Application Insights로 보냅니다. `Trace` 로그 수준은 `Verbose`를 호출하는 일부 로거와 동일하지만, [호스트 json] 파일의 `Trace`를 사용합니다.
* 그 외의 로그는 `Information` 수준 이상만 Application Insights로 보냅니다.

[호스트 json]의 범주 값은 같은 값으로 시작하는 모든 범주에 대한 로깅을 제어합니다. 호스트의 `Host` `Host.General`, `Host.Executor`, `Host.Results`등에 대한 로깅을 제어 합니다 [호스트 json]

[호스트 json]에 동일한 문자열로 시작되는 여러 범주가 포함된 경우 길이가 더 긴 범주가 먼저 일치합니다. `Host.Aggregator`를 제외 하 고 런타임에서 모든 항목을 `Error` 수준으로 기록 하려고 하지만 `Host.Aggregator` `Information` 수준으로 기록 하려는 경우를 가정해 보겠습니다.

### <a name="version-2x-and-later"></a>버전 2.x 이상

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>버전 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

한 범주의 모든 로그를 표시하지 않으려면 로그 수준 `None`을 사용하면 됩니다. 해당 범주를 사용 하 여 작성 된 로그가 없으며 그 위에 로그 수준이 없습니다.

다음 섹션에서는 런타임에서 만드는 주요 로그 범주에 대해 설명합니다. 

### <a name="category-hostresults"></a>범주 Host.Results

이러한 로그는 Application Insights에 "requests"로 표시됩니다. 함수의 성공 또는 실패를 나타냅니다.

![요청 차트](media/functions-monitoring/requests-chart.png)

이러한 로그는 모두 `Information` 수준으로 작성 됩니다. `Warning` 이상에서 필터링 하는 경우에는이 데이터를 볼 수 없습니다.

### <a name="category-hostaggregator"></a>범주 Host.Aggregator

이러한 로그는 [구성 가능한](#configure-the-aggregator) 기간 동안의 함수 호출 수 및 평균을 제공합니다. 기본 기간은 30초 또는 결과 1,000개 중 먼저 도착하는 것입니다. 

로그는 Application Insights의 **customMetrics** 테이블에서 제공합니다. 예는 실행 수, 성공률 및 기간입니다.

![customMetrics 쿼리](media/functions-monitoring/custom-metrics-query.png)

이러한 로그는 모두 `Information` 수준으로 작성 됩니다. `Warning` 이상에서 필터링 하는 경우에는이 데이터를 볼 수 없습니다.

### <a name="other-categories"></a>기타 범주

이미 나열된 범주 이외의 범주에 대한 모든 로그는 Application Insights의 **traces** 테이블에서 제공합니다.

![추적 쿼리](media/functions-monitoring/analytics-traces.png)

`Host`로 시작 하는 범주가 있는 모든 로그는 함수 런타임에 의해 작성 됩니다. "Function started" 및 "Function completed" 로그의 범주 `Host.Executor`있습니다. 성공적인 실행의 경우 이러한 로그는 `Information` 수준입니다. 예외는 `Error` 수준에서 기록 됩니다. 런타임에서 `Warning` 수준 로그도 작성하며, 포이즌 큐로 전송된 큐 메시지를 예로 들 수 있습니다.

함수 코드에서 작성 한 로그에는 범주 `Function` 있으며 모든 로그 수준이 될 수 있습니다.

## <a name="configure-the-aggregator"></a>수집기 구성

이전 섹션에서 언급했듯이, 런타임은 일정 기간 동안 함수 실행에 대한 데이터를 집계합니다. 기본 기간은 30초 또는 실행 1,000개 중 먼저 도착하는 것입니다. [호스트 json] 파일에서 이 설정을 구성할 수 있습니다.  예를 들면 다음과 같습니다.

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>샘플링 구성

Application Insights에는 최대 부하가 발생 했을 때 완료 된 실행에 너무 많은 원격 분석 데이터를 생성 하는 것을 방지할 수 있는 [샘플링](../azure-monitor/app/sampling.md) 기능이 있습니다. 들어오는 실행의 비율이 지정 된 임계값을 초과 하는 경우 Application Insights는 들어오는 실행의 일부를 임의로 무시 하기 시작 합니다. 초당 최대 실행 수에 대한 기본 설정은 20 (버전 1.x의 5 개)입니다. [호스트 json]에서 샘플링을 구성할 수 있습니다.  예를 들면 다음과 같습니다.

### <a name="version-2x-and-later"></a>버전 2.x 이상

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>버전 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [샘플링](../azure-monitor/app/sampling.md)은 기본적으로 사용하도록 설정됩니다. 데이터가 누락 된 것으로 나타나는 경우 특정 모니터링 시나리오에 맞게 샘플링 설정을 조정 해야 할 수 있습니다.

## <a name="write-logs-in-c-functions"></a>C# 함수로 로그 작성

Application Insights에서 traces로 표시되는 로그를 함수 코드로 작성할 수 있습니다.

### <a name="ilogger"></a>ILogger

함수에 `TraceWriter` 매개 변수 대신 [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 매개 변수를 사용합니다. `TraceWriter`를 사용 하 여 만든 로그는 Application Insights으로 이동 하지만, `ILogger`를 사용 하 여 [구조적 로깅을](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)수행할 수 있습니다.

`ILogger` 개체를 사용 하 여 [ILogger에서 `Log<level>` 확장 메서드](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) 를 호출 하 여 로그를 만듭니다. 다음 코드는 "Function. < YOUR_FUNCTION_NAME > `Information` 로그를 작성 합니다. 사용자. "

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>구조적 로깅

이름이 아닌 자리 표시자는 로그 메시지에 사용되는 매개 변수를 결정합니다. 다음 코드가 있다고 가정 합니다.

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

메시지 문자열을 동일하게 유지하고 매개 변수의 순서를 반대로 바꾸면 그 결과 메시지 텍스트가 잘못된 위치에서 값을 갖습니다.

구조적 로깅을 수행할 수 있도록 자리 표시자는 이러한 방식으로 처리됩니다. Application Insights 매개 변수 이름-값 쌍 및 메시지 문자열을 저장 합니다. 그 결과로 메시지 인수는 사용자가 쿼리할 수 있는 필드가 됩니다.

로 거 메서드 호출이 이전 예제와 같은 경우에는 `customDimensions.prop__rowKey`필드를 쿼리할 수 있습니다. 런타임에서 추가 하는 필드와 함수 코드에서 추가 하는 필드가 서로 충돌 하지 않도록 `prop__` 접두사가 추가 됩니다.

`customDimensions.prop__{OriginalFormat}` 필드를 참조하여 원래 메시지 문자열을 쿼리할 수도 있습니다.  

다음은 `customDimensions` 데이터의 샘플 JSON 표현입니다.

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>사용자 지정 메트릭 로깅

C# 스크립트 함수에서, `ILogger`에 `LogMetric` 확장 메서드를 사용하여 Application Insights에 사용자 지정 메트릭을 만들 수 있습니다. 다음은 샘플 메서드 호출입니다.

```csharp
logger.LogMetric("TestMetric", 1234);
```

이 코드는 .NET 용 Application Insights API를 사용 하 여 `TrackMetric`를 호출 하는 대신 사용할 수 있습니다.

## <a name="write-logs-in-javascript-functions"></a>JavaScript 함수로 로그 작성

Node.js 함수에서는 `context.log`를 사용하여 로그를 작성합니다. 구조적 로깅은 사용 되지 않습니다.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>사용자 지정 메트릭 로깅

함수 런타임의 [버전](functions-versions.md#creating-1x-apps) 1.x에서를 실행 하는 경우 node.js 함수는 `context.log.metric` 메서드를 사용 하 여 Application Insights에서 사용자 지정 메트릭을 만들 수 있습니다. 이 메서드는 현재 버전 2.x 이상에서 지원 되지 않습니다. 다음은 샘플 메서드 호출입니다.

```javascript
context.log.metric("TestMetric", 1234);
```

이 코드는 Application Insights 용 node.js SDK를 사용 하 여 `trackMetric`를 호출 하는 대신 사용할 수 있습니다.

## <a name="log-custom-telemetry-in-c-functions"></a>함수에서 C# 사용자 지정 원격 분석 로깅

[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet 패키지를 사용하여 Application Insights로 사용자 지정 원격 분석 데이터를 보낼 수 있습니다. 다음 C# 예제에는 [사용자 지정 원격 분석 API](../azure-monitor/app/api-custom-events-metrics.md)가 사용됩니다. 이 예제는 .NET 클래스 라이브러리용이지만 Application Insights 코드는 C# 스크립트와 동일합니다.

### <a name="version-2x-and-later"></a>버전 2.x 이상

버전 2.x 이상 버전의 런타임에서는 Application Insights의 최신 기능을 사용 하 여 원격 분석을 현재 작업과 자동으로 상호 연결 합니다. 작업 `Id`, `ParentId`또는 `Name` 필드를 수동으로 설정 하지 않아도 됩니다.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>버전 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

함수 호출에 대한 중복 요청이 표시 되므로 `TrackRequest` 또는 `StartOperation<RequestTelemetry>`를 호출 하지 마세요.  Functions 런타임에서 자동으로 요청을 추적합니다.

`telemetryClient.Context.Operation.Id`를 설정하지 마십시오. 여러 함수가 동시에 실행 되는 경우이 전역 설정으로 인해 잘못 된 상관 관계가 발생 합니다. 대신 새로운 원격 분석 인스턴스(`DependencyTelemetry`, `EventTelemetry`)를 만들고 해당하는 `Context` 속성을 수정합니다. 그런 다음 `TelemetryClient` (`TrackDependency()`, `TrackEvent()`, `TrackMetric()`)의 해당 `Track` 메서드에 원격 분석 인스턴스를 전달 합니다. 이 메서드는 원격 분석에 현재 함수 호출에 대한 올바른 상관 관계 정보가 있는지 확인 합니다.

## <a name="log-custom-telemetry-in-javascript-functions"></a>JavaScript 함수에서 사용자 지정 원격 분석 로깅

다음은 [Application Insights NODE.JS SDK](https://github.com/microsoft/applicationinsights-node.js)를 사용 하 여 사용자 지정 원격 분석을 전송 하는 샘플 코드 조각입니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

`tagOverrides` 매개 변수는 `operation_Id`를 함수의 호출 ID로 설정 합니다. 이 설정을 사용하면 특정 함수 호출에 대해 자동으로 생성된 모든 원격 분석 데이터와 사용자 지정 원격 분석의 상관 관계를 지정할 수 있습니다.

## <a name="dependencies"></a>종속성

함수 v2는 HTTP 요청, ServiceBus, EventHub 및 SQL에 대한 종속성을 자동으로 수집 합니다.

종속성을 표시 하는 사용자 지정 코드를 작성할 수 있습니다. 예제는 [ C# 사용자 지정 원격 분석 섹션](#log-custom-telemetry-in-c-functions)의 샘플 코드를 참조 하세요. 샘플 코드는 다음 이미지와 같은 Application Insights의 *응용 프로그램 맵을* 생성 합니다.

![애플리케이션 맵](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>문제 보고

Application Insights의 Functions 통합 문제를 보고하거나 제안 사항 또는 요청 사항을 보내려면 [GitHub에서 문제를 만듭니다](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>스트리밍 로그

응용 프로그램을 개발 하는 동안 Azure에서 실행 될 때 거의 실시간으로 로그에 기록 되는 항목을 확인 하는 것이 좋습니다.

함수 실행에 의해 생성 되는 로그 파일의 스트림을 보는 방법에는 두 가지가 있습니다.

* **기본 제공 로그 스트리밍**: App Service 플랫폼을 사용 하면 응용 프로그램 로그 파일의 스트림을 볼 수 있습니다. 이는 [로컬 개발](functions-develop-local.md) 중에 또는 포털에서 **테스트** 탭을 사용할 때 표시 되는 출력과 동일 합니다. 모든 로그 기반 정보가 표시 됩니다. 자세한 내용은 [스트림 로그](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)를 참조 하세요. 이 스트리밍 방법은 단일 인스턴스만 지원 하며 소비 계획에서 Linux에서 실행 되는 앱과 함께 사용할 수 없습니다.

* **라이브 메트릭 스트림**: 함수 앱이 [Application Insights에 연결](#enable-application-insights-integration)된 경우 [라이브 메트릭 스트림](../azure-monitor/app/live-stream.md)를 사용 하 여 Azure Portal에서 거의 실시간으로 로그 데이터 및 기타 메트릭을 볼 수 있습니다. 소비 계획의 여러 인스턴스 또는 Linux에서 실행 되는 함수를 모니터링 하는 경우이 방법을 사용 합니다. 이 메서드는 [샘플링 된 데이터](#configure-sampling)를 사용 합니다.

로그 스트림은 포털 및 대부분의 로컬 개발 환경에서 볼 수 있습니다. 

### <a name="portal"></a>포털

포털에서 두 유형의 로그 스트림을 모두 볼 수 있습니다.

#### <a name="built-in-log-streaming"></a>기본 제공 로그 스트리밍

포털에서 스트리밍 로그를 보려면 함수 앱에서 **플랫폼 기능** 탭을 선택 합니다. 그런 다음 **모니터링**에서 **스트리밍 로그**를 선택 합니다.

![포털에서 스트리밍 로그 사용](./media/functions-monitoring/enable-streaming-logs-portal.png)

그러면 앱이 로그 스트리밍 서비스에 연결 되 고 응용 프로그램 로그는 창에 표시 됩니다. **응용 프로그램 로그** 와 **웹 서버 로그**를 전환할 수 있습니다.  

![포털에서 스트리밍 로그 보기](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>라이브 메트릭 스트림

앱에 대한 라이브 메트릭 스트림를 보려면 함수 앱의 **개요** 탭을 선택 합니다. Application Insights 사용 하도록 설정 하면 **구성 된 기능**아래에 **Application Insights** 링크가 표시 됩니다. 이 링크를 누르면 앱에 대한 Application Insights 페이지로 이동 합니다.

Application Insights에서 **라이브 메트릭 스트림**를 선택 합니다. [샘플링 된 로그 항목](#configure-sampling) 은 **샘플 원격 분석**아래에 표시 됩니다.

![포털에서 라이브 메트릭 스트림 보기](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>핵심 도구

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli)를 사용 하 여 스트리밍 로그를 사용 하도록 설정할 수 있습니다. 다음 명령을 사용 하 여 로그인 하 고, 구독을 선택 하 고, 로그 파일을 스트리밍합니다.

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/overview)를 사용 하 여 스트리밍 로그를 사용 하도록 설정할 수 있습니다. PowerShell의 경우 다음 명령을 사용 하 여 Azure 계정을 추가 하 고, 구독을 선택 하 고, 로그 파일을 스트리밍합니다.

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>기본 제공 로깅을 사용하지 않도록 설정

Application Insights를 사용 하도록 설정 하는 경우 Azure Storage를 사용 하는 기본 제공 로깅을 사용 하지 않도록 설정 합니다. 기본 제공 로깅은 간단한 워크 로드를 사용 하 여 테스트 하는 데 유용 하지만, 고성능 프로덕션 용도로는 사용 되지 않습니다. 프로덕션 모니터링의 경우 Application Insights 하는 것이 좋습니다. 프로덕션에서 기본 제공 로깅이 사용 되는 경우 Azure Storage 제한으로 인해 로깅 레코드가 불완전할 수 있습니다.

기본 제공 로깅을 사용하지 않도록 설정하려면 `AzureWebJobsDashboard` 앱 설정을 삭제합니다. Azure Portal에서 앱 설정을 삭제하는 방법에 대한 자세한 내용은 [함수 앱을 관리하는 방법](functions-how-to-use-azure-function-app-settings.md#settings)의 **애플리케이션 설정** 섹션을 참조하세요. 앱 설정을 삭제 하기 전에 동일한 함수 앱에 있는 기존 함수가 Azure Storage 트리거 또는 바인딩에 대해이 설정을 사용 하지 않도록 해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core 로깅](/aspnet/core/fundamentals/logging/)

[호스트 json]: functions-host-json.md
