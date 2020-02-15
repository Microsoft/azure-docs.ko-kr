---
title: 검색 트래픽 분석 데이터에 대 한 보고서
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에 대 한 검색 트래픽 분석을 사용 하도록 설정 하 고 Application Insights를 사용 하 여 원격 분석 및 사용자 시작 이벤트를 수집한 다음 Power BI 보고서에서 결과를 분석 합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 84e60b0a942bad94d8e36eb20b5be8e3f55af80a
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190953"
---
# <a name="implement-search-traffic-analytics-in-azure-cognitive-search"></a>Azure Cognitive Search에서 검색 트래픽 분석 구현

검색 트래픽 분석은 Search 서비스에 대한 피드백 루프를 구현하기 위한 패턴입니다. 목표는 사용자가 시작한 클릭 이벤트와 키보드 입력에 대 한 원격 분석을 수집 하는 것입니다. 이 정보를 사용 하 여 인기 있는 검색 용어, 클릭 광고 요금 및 결과가 0 인 쿼리 입력을 포함 하 여 검색 솔루션의 효율성을 확인할 수 있습니다.

이 패턴은 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) ( [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)의 기능)에 의존 하 여 사용자 데이터를 수집 합니다. 또한이 문서에 설명 된 대로 클라이언트 코드에 계측을 추가 해야 합니다. 마지막으로 데이터를 분석 하는 보고 메커니즘이 필요 합니다. Power BI 하는 것이 좋지만 Application Insights에 연결 된 모든 도구를 사용할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명 하는 패턴은 클라이언트에서 생성 된 고급 시나리오 및 클릭 스트림 데이터를 위한 것입니다. 또는 검색 서비스에 의해 생성 된 로그 정보를 보고할 수 있습니다. 서비스 로그 보고서에 대 한 자세한 내용은 [리소스 소비 모니터링 및 쿼리 작업](search-monitor-usage.md)을 참조 하세요.

## <a name="identify-relevant-search-data"></a>관련 검색 데이터 식별

유용한 검색 메트릭을 확보하려면 검색 애플리케이션의 사용자로부터 일부 신호를 기록해야 합니다. 이러한 신호는 사용자가 관심을 보이고 자신의 요구 사항에 관련이 있다고 생각하는 콘텐츠를 나타냅니다.

검색 트래픽 분석에는 다음 두 신호가 필요합니다.

+ 사용자 생성 검색 이벤트: 사용자가 시작한 검색 쿼리만 흥미롭습니다. 패싯, 추가 콘텐츠 또는 내부 정보를 채우는 데 사용되는 검색 요청은 중요하지 않으며 결과를 왜곡하고 편견을 갖게 만듭니다.

+ 사용자가 생성 한 클릭 이벤트:이 문서를 클릭 하면 검색 쿼리에서 반환 된 특정 검색 결과를 선택 하는 사용자를 참조 합니다. 클릭은 일반적으로 문서가 특정 검색 쿼리와 관련된 결과라는 의미입니다.

상관 관계 ID를 사용 하 여 검색 및 클릭 이벤트를 연결 하면 응용 프로그램의 사용자 동작을 분석할 수 있습니다. 검색 트래픽 로그만으로는 이처럼 검색 관련 고급 정보를 얻을 수 없습니다.

## <a name="add-search-traffic-analytics"></a>트래픽 분석 검색 추가

사용자는 검색 애플리케이션과 상호 작용하므로 검색 애플리케이션에서 이전 섹션에 언급된 신호를 수집해야 합니다. Application Insights는 확장 가능한 모니터링 솔루션으로, 여러 플랫폼에 사용할 수 있으며 유연한 계측 옵션을 제공합니다. Application Insights를 사용 하면 Azure Cognitive Search에서 만든 Power BI 검색 보고서를 활용 하 여 데이터 분석을 보다 쉽게 수행할 수 있습니다.

Azure Cognitive Search 서비스에 대 한 [포털](https://portal.azure.com) 페이지에서 트래픽 분석 검색 페이지에는이 원격 분석 패턴에 따라 다음과 같은 참고 자료 시트가 포함 되어 있습니다. 또한 한 장소에서 Application Insights 리소스를 선택하거나 만들고, 필요한 데이터를 볼 수 있습니다.

![검색 트래픽 분석 지침][1]

## <a name="1---select-a-resource"></a>1 - 리소스 선택

사용할 Application Insights 리소스를 선택하거나 리소스가 없는 경우 직접 만들어야 합니다. 이미 사용 중인 리소스를 사용하여 필요한 사용자 지정 이벤트를 기록할 수 있습니다.

새 Application Insights 리소스를 만들 때 모든 애플리케이션 유형은 이 시나리오에 유효합니다. 사용 중인 플랫폼에 가장 적합한 것을 선택합니다.

애플리케이션의 원격 분석 클라이언트를 만들기 위한 계측 키가 필요합니다. Application Insights 포털 대시보드에서 가져올 수도 있고, 검색 트래픽 분석 페이지에서 가져온 후에 사용할 인스턴스를 선택해도 됩니다.

## <a name="2---add-instrumentation"></a>2 - 계측 추가

이 단계에서는 위의 단계에서 만든 Application Insights 리소스를 사용 하 여 사용자 고유의 검색 응용 프로그램을 계측 합니다. 이 프로세스에는 다음과 같은 네 단계가 있습니다.

**1 단계: 원격 분석 클라이언트 만들기**

Application Insights 리소스에 이벤트를 보내는 개체입니다.

*C#*

    private TelemetryClient telemetryClient = new TelemetryClient();
    telemetryClient.InstrumentationKey = "<YOUR INSTRUMENTATION KEY>";

*JavaScript*

    <script type="text/javascript">var appInsights=window.appInsights||function(config){function r(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s=u.createElement(o),i,f;s.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(s);try{t.cookie=u.cookie}catch(h){}for(t.queue=[],i=["Event","Exception","Metric","PageView","Trace","Dependency"];i.length;)r("track"+i.pop());return r("setAuthenticatedUserContext"),r("clearAuthenticatedUserContext"),config.disableExceptionTracking||(i="onerror",r("_"+i),f=e[i],e[i]=function(config,r,u,e,o){var s=f&&f(config,r,u,e,o);return s!==!0&&t["_"+i](config,r,u,e,o),s}),t}
    ({
    instrumentationKey: "<YOUR INSTRUMENTATION KEY>"
    });
    window.appInsights=appInsights;
    </script>

다른 언어 및 플랫폼은 전체 [목록](https://docs.microsoft.com/azure/application-insights/app-insights-platforms)을 참조하세요.

**2 단계: 상관 관계에 대 한 검색 ID 요청**

검색 요청을 클릭과 상호 연결 하려면 이러한 두 개의 개별 이벤트를 연결 하는 상관 관계 ID가 필요 합니다. Azure Cognitive Search는 헤더를 사용 하 여 요청할 때 검색 ID를 제공 합니다.

*C#*

    // This sample uses the .NET SDK https://www.nuget.org/packages/Microsoft.Azure.Search

    var client = new SearchIndexClient(<SearchServiceName>, <IndexName>, new SearchCredentials(<QueryKey>)
    var headers = new Dictionary<string, List<string>>() { { "x-ms-azs-return-searchid", new List<string>() { "true" } } };
    var response = await client.Documents.SearchWithHttpMessagesAsync(searchText: searchText, searchParameters: parameters, customHeaders: headers);
    IEnumerable<string> headerValues;
    string searchId = string.Empty;
    if (response.Response.Headers.TryGetValues("x-ms-azs-searchid", out headerValues)){
     searchId = headerValues.FirstOrDefault();
    }

*JavaScript*

    request.setRequestHeader("x-ms-azs-return-searchid", "true");
    request.setRequestHeader("Access-Control-Expose-Headers", "x-ms-azs-searchid");
    var searchId = request.getResponseHeader('x-ms-azs-searchid');

**3 단계: 로그 검색 이벤트**

사용자는 검색 요청을 실행할 때마다 Application Insights 사용자 지정 이벤트에서 다음 스키마로 해당 검색 요청을 검색 이벤트로 기록해야 합니다.

**Searchservicename**: (문자열) 검색 서비스 이름 **searchservicename**: (guid) 검색 쿼리의 고유 식별자 **IndexName**: (문자열) 검색 서비스 인덱스: (문자열) 사용자의 **resultcount**에서 입력 한 (문자열) 검색 **용어: (** int) 반환 된 문서 수 (int) **ScoringProfile**: (문자열) 사용 된 점수 매기기 프로필의 이름입니다 (있는 경우).

> [!NOTE]
> 검색 쿼리에 $count=true를 추가하여 사용자가 생성한 쿼리 수를 요청합니다. 자세한 내용은 [여기](/rest/api/searchservice/search-documents#counttrue--false)를 참조 하세요.
>

> [!NOTE]
> 사용자가 생성한 검색 쿼리만 기록해야 합니다.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search Id>},
    {"IndexName", <index name>},
    {"QueryTerms", <search terms>},
    {"ResultCount", <results count>},
    {"ScoringProfile", <scoring profile used>}
    };
    telemetryClient.TrackEvent("Search", properties);

*JavaScript*

    appInsights.trackEvent("Search", {
    SearchServiceName: <service name>,
    SearchId: <search id>,
    IndexName: <index name>,
    QueryTerms: <search terms>,
    ResultCount: <results count>,
    ScoringProfile: <scoring profile used>
    });

**4 단계: 클릭 이벤트 기록**

사용자가 문서를 클릭할 때마다 검색 분석을 위해 신호를 기록해야 합니다. Application Insights 사용자 지정 이벤트를 사용하여 다음 스키마로 이러한 이벤트를 기록합니다.

**ServiceName**: (문자열) 검색 서비스 이름 **SearchId**: (guid) 관련 검색 쿼리의 고유 식별자 **DocId**: (문자열) 문서 식별자 **Position**: (정수) 검색 결과 페이지의 문서 순위

> [!NOTE]
> 위치는 애플리케이션의 카디널 순서를 참조합니다. 이 숫자가 항상 같다면 이 숫자를 자유롭게 설정하여 비교할 수 있습니다.
>

*C#*

    var properties = new Dictionary <string, string> {
    {"SearchServiceName", <service name>},
    {"SearchId", <search id>},
    {"ClickedDocId", <clicked document id>},
    {"Rank", <clicked document position>}
    };
    telemetryClient.TrackEvent("Click", properties);

*JavaScript*

    appInsights.trackEvent("Click", {
        SearchServiceName: <service name>,
        SearchId: <search id>,
        ClickedDocId: <clicked document id>,
        Rank: <clicked document position>
    });

## <a name="3---analyze-in-power-bi"></a>3 - Power BI에서 분석

앱을 계측 하 고 응용 프로그램이 Application Insights에 올바르게 연결 되었는지 확인 한 후 미리 정의 된 보고서 템플릿을 다운로드 하 여 Power BI desktop에서 데이터를 분석 합니다. 보고서에는 검색 트래픽 분석을 위해 캡처된 추가 데이터를 분석 하는 데 유용한 미리 정의 된 차트와 테이블이 포함 되어 있습니다. 

1. Azure Cognitive Search 대시보드 왼쪽 탐색 창의 **설정**에서 **트래픽 분석 검색**을 클릭 합니다.

2. **트래픽 분석 검색** 페이지의 3단계에서 **Power BI Desktop 가져오기**를 클릭하여 Power BI를 설치합니다.

   ![Power BI 보고서 가져오기](./media/search-traffic-analytics/get-use-power-bi.png "Power BI 보고서 가져오기")

2. 동일한 페이지에서 **Power BI 보고서 다운로드**를 클릭 합니다.

3. Power BI Desktop에서 보고서가 열리고 Application Insights에 연결 하 고 자격 증명을 제공 하 라는 메시지가 표시 됩니다. Application Insights 리소스에 대 한 Azure Portal 페이지에서 연결 정보를 찾을 수 있습니다. 자격 증명에는 포털 로그인에 사용 하는 것과 동일한 사용자 이름 및 암호를 제공 합니다.

   ![Application Insights에 연결](./media/search-traffic-analytics/connect-to-app-insights.png "Application Insights에 연결")

4. **로드**를 클릭합니다.

이 보고서에는 검색 성능과 관련성을 향상시키기 위해 정보에 기반한 합리적 의사 결정을 내리는 데 도움이 되는 차트와 테이블이 포함되어 있습니다.

메트릭에 포함되는 항목은 다음과 같습니다.

* 검색 볼륨 및 가장 인기 있는 용어-문서 쌍: 같은 문서를 클릭 하 여 클릭을 기준으로 정렬 합니다.
* 클릭 없이 검색: 클릭을 등록하지 않는 상위 쿼리를 일컫는 용어

다음 스크린샷에서는 트래픽 분석 검색을 분석하기 위한 기본 제공 보고서와 차트를 보여 줍니다.

![Azure Cognitive Search에 대 한 Power BI 대시보드](./media/search-traffic-analytics/azuresearch-powerbi-dashboard.png "Azure Cognitive Search에 대 한 Power BI 대시보드")

## <a name="next-steps"></a>다음 단계
검색 애플리케이션을 계측하여 검색 서비스에 대한 강력하고 통찰력 있는 데이터를 가져옵니다.

[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)에 대한 자세한 내용을 확인하고, [가격 페이지](https://azure.microsoft.com/pricing/details/application-insights/)를 방문하여 다양한 각 서비스 계층에 대해 자세히 알아볼 수 있습니다.

놀라운 보고서 만들기에 대해 자세히 알아보세요. 자세한 내용은 [Power BI Desktop 시작을](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) 참조 하세요.

<!--Image references-->
[1]: ./media/search-traffic-analytics/azuresearch-trafficanalytics.png
[2]: ./media/search-traffic-analytics/azuresearch-appinsightsdata.png
[3]: ./media/search-traffic-analytics/azuresearch-pbitemplate.png
