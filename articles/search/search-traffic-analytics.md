---
title: 검색 트래픽 분석을 위한 원격 분석
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대한 검색 트래픽 분석을 사용하도록 설정하고 Application Insights를 사용하여 원격 분석 및 사용자 시작 이벤트를 수집한 다음, Power BI 보고서에서 결과를 분석합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 1e7f832faffc09cb7bbbcca73763b09f58cbb412
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019796"
---
# <a name="collect-telemetry-data-for-search-traffic-analytics"></a>검색 트래픽 분석을 위한 원격 분석 데이터 수집

검색 트래픽 분석은 사용자가 시작한 클릭 이벤트 및 키보드 입력과 같이 Azure Cognitive Search 애플리케이션과의 사용자 상호 작용에 대한 원격 분석을 수집하기 위한 패턴입니다. 이 정보를 사용하면 인기 있는 검색어, 클릭 광고 비율 및 결과가 0인 쿼리 입력을 비롯한 검색 솔루션의 효율성을 확인할 수 있습니다.

이 패턴은 사용자 데이터를 수집하는 [Application Insights](../azure-monitor/app/app-insights-overview.md)([Azure Monitor](../azure-monitor/index.yml)의 기능)에 의존합니다. 이 문서에 설명된 대로 클라이언트 코드에 계측을 추가해야 합니다. 마지막으로 데이터를 분석하는 보고 메커니즘이 필요합니다. Power BI가 권장되지만, Application Insights에 연결되는 도구 또는 Application Dashboard를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에 설명된 패턴은 고급 시나리오 및 클라이언트에 추가하는 코드에서 생성된 클릭 동향 데이터를 위한 것입니다. 이와 대조적으로 서비스 로그는 설정하기 쉬우며, 다양한 메트릭을 제공하고, 포털에서 코드 없이 수행할 수 있습니다. 모든 시나리오에 대해 로깅을 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [로그 데이터 수집 및 분석](search-monitor-logs.md)을 참조하세요.

## <a name="identify-relevant-search-data"></a>관련 검색 데이터 식별

검색 트래픽 검색의 유용한 메트릭을 확보하려면 검색 애플리케이션의 사용자로부터 일부 신호를 기록해야 합니다. 이러한 신호는 사용자가 관심을 보이고 관련이 있다고 생각하는 콘텐츠를 나타냅니다. 검색 트래픽 분석의 경우에는 다음이 포함됩니다.

+ 사용자 생성 검색 이벤트: 사용자가 시작한 검색 쿼리에만 주목합니다. 패싯, 추가 콘텐츠 또는 내부 정보를 채우는 데 사용되는 검색 요청은 중요하지 않으며 결과를 왜곡하고 편견을 갖게 만듭니다.

+ 사용자 생성 클릭 이벤트: 검색 결과 페이지에서 클릭 이벤트는 일반적으로 문서가 특정 검색 쿼리와 관련된 결과라는 의미입니다.

상관 관계 ID를 사용하여 검색 및 클릭 이벤트를 연결하면 애플리케이션의 검색 기능이 얼마나 잘 수행되는지 더 자세히 파악할 수 있습니다.

## <a name="add-search-traffic-analytics"></a>트래픽 분석 검색 추가

Azure Cognitive Search 서비스의 [포털](https://portal.azure.com) 페이지에 있는 검색 트래픽 분석 페이지에는 이 원격 분석 패턴을 따르는 치트 시트를 포함하고 있습니다. 이 페이지에서 Application Insights 리소스를 선택하거나 만들고, 계측 키를 가져오고, 솔루션에 맞게 조정할 수 있는 코드 조각을 복사하고, 패턴에 반영된 스키마를 통해 빌드된 Power BI 보고서를 다운로드할 수 있습니다.

![포털의 검색 트래픽 분석 페이지](media/search-traffic-analytics/azuresearch-trafficanalytics.png "포털의 검색 트래픽 분석 페이지")

## <a name="1---set-up-application-insights"></a>1 - Application Insights 설정

기존 Application Insights 리소스를 선택하거나 리소스가 없는 경우 [하나 만듭니다](../azure-monitor/app/create-new-resource.md). 검색 트래픽 분석 페이지를 사용하는 경우 애플리케이션에서 Application Insights에 연결하는 데 필요한 계측 키를 복사할 수 있습니다.

Application Insights 리소스가 있으면 [지원되는 언어 및 플랫폼에 대한 지침](../azure-monitor/app/platforms.md)에 따라 앱을 등록할 수 있습니다. 등록은 Application Insights의 계측 키를 사용자 코드에 추가하기만 하면 연결이 설정됩니다. 기존 리소스를 선택할 때 검색 트래픽 분석 페이지에서 또는 포털에서 키를 찾을 수 있습니다.

일부 Visual Studio 프로젝트 형식에 대해 작동하는 바로 가기는 다음 단계에서 반영됩니다. 리소스가 생성되고, 몇 번 클릭하기만 하면 앱이 등록됩니다.

1. Visual Studio 및 ASP.NET 개발의 경우 솔루션을 열고 **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다.

1. **시작**을 클릭합니다.

1. Microsoft 계정, Azure 구독 및 Application Insights 리소스를 제공하여 앱을 등록합니다(새 리소스는 기본값). **등록**을 클릭합니다.

이 시점에서 애플리케이션은 애플리케이션 모니터링에 대해 설정됩니다. 즉, 모든 페이지 로드가 기본 메트릭을 사용하여 추적됩니다. 이전 단계에 대한 자세한 내용은 [Application Insights 서버 쪽 원격 분석 사용](../azure-monitor/app/asp-net-core.md#enable-application-insights-server-side-telemetry-visual-studio)을 참조하세요.

## <a name="2---add-instrumentation"></a>2 - 계측 추가

이 단계에서는 위의 단계에서 만든 Application Insights 리소스를 사용하여 사용자 고유의 검색 애플리케이션을 계측합니다. 이 프로세스에는 원격 분석 클라이언트 만들기부터 네 단계가 있습니다.

### <a name="step-1-create-a-telemetry-client"></a>1단계: 원격 분석 클라이언트 만들기

Application Insights에 이벤트를 보내는 개체를 만듭니다. 브라우저에서 실행되는 서버 쪽 애플리케이션 코드 또는 클라이언트 쪽 코드에 계측을 추가할 수 있습니다. 여기에는 C# 및 JavaScript 변형이 표시됩니다(다른 언어는 [지원되는 플랫폼과 프레임워크](../azure-monitor/app/platforms.md)의 전체 목록 참조). 원하는 수준의 정보를 제공하는 방법을 선택합니다.

서버 쪽 원격 분석은 애플리케이션 계층에서 메트릭을 캡처합니다. 예를 들어 클라우드의 웹 서비스로 실행되는 애플리케이션 또는 회사 네트워크의 온-프레미스 앱에서 메트릭을 캡처합니다. 서버 쪽 원격 분석은 검색 및 클릭 이벤트, 결과의 문서 위치 및 쿼리 정보를 캡처합니다. 단, 데이터 컬렉션의 범위는 해당 계층에서 사용할 수 있는 모든 정보로 한정됩니다.

클라이언트에서 쿼리 입력을 조작하거나, 탐색을 추가하거나, 컨텍스트를 포함하는 추가 코드(예: 홈 페이지나 제품 페이지에서 시작된 쿼리)가 있을 수 있습니다. 솔루션을 설명하는 경우 원격 분석에서 추가 세부 정보를 반영하도록 클라이언트 쪽 계측을 선택할 수 있습니다. 이 추가 세부 정보가 수집되는 방법은 이 패턴의 범위를 벗어나지만, [웹 페이지의 Application Insights](../azure-monitor/app/javascript.md#explore-browserclient-side-data)에서 세부 지침을 검토할 수 있습니다. 

**C# 사용**

C#에서는 프로젝트가 ASP.NET인 경우 appsettings.json과 같은 애플리케이션 구성에서 **InstrumentationKey**를 찾을 수 있습니다. 키 위치를 잘 모르는 경우 등록 지침을 다시 참조하세요.

```csharp
private static TelemetryClient _telemetryClient;

// Add a constructor that accepts a telemetry client:
public HomeController(TelemetryClient telemetry)
{
    _telemetryClient = telemetry;
}
```

**JavaScript 사용**

```javascript
<script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
({
instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
});
window.appInsights=appInsights;
</script>
```

### <a name="step-2-request-a-search-id-for-correlation"></a>2단계: 상관 관계에 대한 검색 ID 요청

검색 요청과 클릭의 상관 관계를 지정하려면 이러한 두 개의 고유한 이벤트에 연관된 상관 관계 ID가 필요합니다. 사용자가 HTTP 헤더를 사용하여 검색 ID를 요청하면 Azure Cognitive Search가 검색 ID를 제공합니다.

검색 ID는 요청 자체에 대해 Azure Cognitive Search에서 내보낸 메트릭과 Application Insights에 로깅하는 사용자 지정 메트릭의 상관 관계를 허용합니다.  

**C# 사용**

```csharp
// This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)

// Use HTTP headers so that you can get the search ID from the response
var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
string searchId = string.Empty;
if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out IEnumerable<string> headerValues))
{
    searchId = headerValues.FirstOrDefault();
}
```

**JavaScript 사용(REST API 호출)**

```javascript
request.setRequestHeader("x-ms-azs-return-searchid", "true");
request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
var searchId = request.getResponseHeader('x-ms-azs-searchid');
```

### <a name="step-3-log-search-events"></a>3단계: 로그 검색 이벤트

사용자는 검색 요청을 실행할 때마다 Application Insights 사용자 지정 이벤트에 다음 스키마를 사용하여 검색 이벤트로 기록해야 합니다. 사용자가 생성한 검색 쿼리만 기록해야 합니다.

+ **SearchServiceName**: (문자열) 검색 서비스 이름
+ **SearchId**: (guid) 검색 쿼리의 고유 식별자(검색 응답에 제공됨)
+ **IndexName**: (문자열) 쿼리할 검색 서비스 인덱스
+ **QueryTerms**: (문자열) 사용자가 입력한 검색어
+ **ResultCount**: (int) 반환된 문서의 수(검색 응답에 제공됨)
+ **ScoringProfile**: (문자열) 사용된 점수 매기기 프로필의 이름(있는 경우).

> [!NOTE]
> 검색 쿼리에 $count=true를 추가하여 사용자가 생성한 쿼리 수를 요청합니다. 자세한 내용은 [문서 검색(REST)](/rest/api/searchservice/search-documents#counttrue--false)을 참조하세요.
>

**C# 사용**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
};
_telemetryClient.TrackEvent("Search", properties);
```

**JavaScript 사용**

```javascript
appInsights.trackEvent("Search", {
  SearchServiceName: <service name>,
  SearchId: <search id>,
  IndexName: <index name>,
  QueryTerms: <search terms>,
  ResultCount: <results count>,
  ScoringProfile: <scoring profile used>
});
```

### <a name="step-4-log-click-events"></a>4단계: 클릭 이벤트 기록

사용자가 문서를 클릭할 때마다 검색 분석을 위해 신호를 기록해야 합니다. Application Insights 사용자 지정 이벤트를 사용하여 다음 스키마로 이러한 이벤트를 기록합니다.

+ **ServiceName**: (문자열) 검색 서비스 이름
+ **SearchId**: (guid) 관련 검색 쿼리의 고유 식별자
+ **DocId**: (문자열) 문서 식별자
+ **Position**: (int) 검색 결과 페이지의 문서 순위

> [!NOTE]
> 위치는 애플리케이션의 카디널 순서를 참조합니다. 이 숫자가 항상 같다면 이 숫자를 자유롭게 설정하여 비교할 수 있습니다.
>

**C# 사용**

```csharp
var properties = new Dictionary <string, string> 
{
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
};
_telemetryClient.TrackEvent("Click", properties);
```

**JavaScript 사용**

```javascript
appInsights.trackEvent("Click", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    ClickedDocId: <clicked document id>,
    Rank: <clicked document position>
});
```

## <a name="3---analyze-in-power-bi"></a>3 - Power BI에서 분석

앱을 계측하고 애플리케이션이 Application Insights에 올바르게 연결되었는지 확인했으면 작성한 미리 정의된 보고서 템플릿을 다운로드하여 Power BI Desktop에서 데이터를 분석합니다. 보고서에는 검색 트래픽 분석을 위해 캡처된 추가 데이터를 분석하는 데 유용한 미리 정의된 차트와 테이블이 포함되어 있습니다.

1. Azure Cognitive Search 대시보드의 왼쪽 탐색 창에 있는 **설정** 아래에서 **검색 트래픽 분석**을 클릭합니다.

1. **트래픽 분석 검색** 페이지의 3단계에서 **Power BI Desktop 가져오기**를 클릭하여 Power BI를 설치합니다.

   ![Power BI 보고서 가져오기](./media/search-traffic-analytics/get-use-power-bi.png "Power BI 보고서 가져오기")

1. 동일한 페이지에서 **Power BI 보고서 다운로드**를 클릭합니다.

1. 보고서가 Power BI Desktop에서 열리고 Application Insights에 연결하고 자격 증명을 제공하라는 메시지가 표시됩니다. 연결 정보는 Application Insights 리소스에 대한 Azure Portal 페이지에서 찾을 수 있습니다. 자격 증명의 경우 포털 로그인에 사용하는 것과 동일한 사용자 이름 및 암호를 제공합니다.

   ![Application Insights에 연결](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights에 연결")

1. **로드**를 클릭합니다.

이 보고서에는 검색 성능과 관련성을 향상시키기 위해 정보에 기반한 합리적 의사 결정을 내리는 데 도움이 되는 차트와 테이블이 포함되어 있습니다.

메트릭에 포함되는 항목은 다음과 같습니다.

+ 볼륨 및 가장 인기 있는 용어-문서 쌍 검색: 동일한 문서를 클릭하게 되는 용어로, 클릭 수를 기준으로 정렬됩니다.
+ 클릭 없이 검색: 클릭을 등록하지 않는 상위 쿼리를 일컫는 용어

다음 스크린샷에서는 모든 스키마 요소를 사용한 경우 기본 제공 보고서가 어떻게 표시되는지 보여 줍니다.

![Azure Cognitive Search에 대한 Power BI 대시보드](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure Cognitive Search에 대한 Power BI 대시보드")

## <a name="next-steps"></a>다음 단계

검색 애플리케이션을 계측하여 검색 서비스에 대한 강력하고 통찰력 있는 데이터를 가져옵니다.

[Application Insights](../azure-monitor/app/app-insights-overview.md)에 대한 자세한 내용을 확인하고, [가격 페이지](https://azure.microsoft.com/pricing/details/application-insights/)를 방문하여 다양한 각 서비스 계층에 대해 자세히 알아볼 수 있습니다.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작](/power-bi/fundamentals/desktop-getting-started)을 참조하세요.