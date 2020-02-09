---
title: JavaScript 웹 앱에 대 한 Azure 애플리케이션 정보
description: 페이지 보기 및 세션 수, 웹 클라이언트 데이터, SPA (단일 페이지 응용 프로그램)를 가져오고 사용 패턴을 추적 합니다. JavaScript 웹 페이지의 예외 및 성능 문제를 감지합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: c98feda62b7e5de5551b02d6189a1142ca8c5f88
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276774"
---
# <a name="application-insights-for-web-pages"></a>웹 페이지용 Application Insights

웹 페이지 또는 앱의 성능 및 사용량 현황에 대해 알아봅니다. 페이지 스크립트에 [Application Insights](app-insights-overview.md) 을 추가 하면 페이지 로드 및 ajax 호출의 타이밍, 브라우저 예외 및 ajax 오류에 대 한 세부 정보 뿐만 아니라 사용자 및 세션 수가 표시 됩니다. 이러한 모든 요소를 페이지, 클라이언트 OS 및 브라우저 버전, 지리적 위치 및 기타 차원으로 분할할 수 있습니다. 실패 횟수 또는 느린 페이지 로딩에 대한 경고를 설정할 수도 있습니다. 또한 JavaScript 코드에 추적 호출을 삽입하여 웹 페이지 애플리케이션의 다양한 기능 사용 방법을 추적할 수 있습니다.

Application Insights는 다른 웹 페이지와 함께 사용할 수 있습니다. 간단한 JavaScript만 추가하면 됩니다. 웹 서비스가 [Java](java-get-started.md) 또는 [ASP.NET](asp-net.md)인 경우 클라이언트 쪽 JavaScript sdk와 함께 서버 쪽 sdk를 사용 하 여 응용 프로그램의 성능에 대 한 종단 간 이해를 얻을 수 있습니다.

## <a name="adding-the-javascript-sdk"></a>JavaScript SDK 추가

1. 먼저 Application Insights 리소스가 필요 합니다. 리소스 및 계측 키가 아직 없는 경우 [새 리소스 만들기 지침](create-new-resource.md)을 따르세요.
2. JavaScript 원격 분석을 전송 하려는 리소스에서 계측 키를 복사 합니다.
3. 다음 두 옵션 중 하나를 통해 웹 페이지 또는 앱에 Application Insights JavaScript SDK를 추가 합니다.
    * [npm 설정](#npm-based-setup)
    * [JavaScript 코드 조각](#snippet-based-setup)

> [!IMPORTANT]
> JavaScript SDK를 응용 프로그램에 추가 하려면 메서드를 하나만 사용 합니다. NPM 설치 프로그램을 사용 하는 경우 코드 조각을 사용 하지 말고 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> NPM 설치 프로그램은 JavaScript SDK를 프로젝트에 대 한 종속성으로 설치 하 고 IntelliSense를 사용 하도록 설정 하는 반면 코드 조각은 런타임에 SDK를 인출 합니다. 둘 다 동일한 기능을 지원 합니다. 그러나 더 많은 사용자 지정 이벤트 및 구성을 원하는 개발자는 일반적으로 NPM 설치를 선택 하는 반면, 사용자는 코드 조각에 대 한 기본 웹 분석 opt를 신속 하 게 사용할 수 있습니다.

### <a name="npm-based-setup"></a>npm 기반 설정

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>코드 조각 기반 설정

앱에서 npm를 사용 하지 않는 경우 각 페이지의 맨 위에이 코드 조각을 붙여넣어 Application Insights를 사용 하 여 웹 페이지를 직접 계측할 수 있습니다. 모든 종속성과 관련 된 잠재적인 문제를 모니터링할 수 있도록 `<head>` 섹션의 첫 번째 스크립트 여야 합니다. Blazor Server 앱을 사용 하는 경우 `<head>` 섹션에서 `_Host.cshtml` 파일의 맨 위에 코드 조각을 추가 합니다.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("addTelemetryInitializer"),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),t.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Azure Portal 원격 분석 보내기

기본적으로 JavaScript SDK Application Insights는 응용 프로그램의 상태와 기본 사용자 환경을 결정 하는 데 도움이 되는 여러 원격 분석 항목을 자동으로 수집 합니다. 해당 지역은 다음과 같습니다.

- 에 대 한 정보를 포함 하 여 앱의 Catch 되지 않은 **예외**
    - 스택 추적
    - 오류와 함께 예외 정보 및 메시지
    - 줄 & 열 오류 수
    - 오류가 발생 한 URL
- 앱에서 생성 한 **네트워크 종속성 요청** ( **xhr** 및 **인출** (fetch collection은 기본적으로 사용 하지 않도록 설정 됨) 요청, 정보 포함
    - 종속성 원본의 Url
    - 종속성을 요청 하는 데 사용 되는 명령 & 메서드입니다.
    - 요청 기간
    - 요청에 대 한 결과 코드 및 성공 상태
    - 요청을 수행 하는 사용자의 ID (있는 경우)
    - 요청을 수행 하는 상관 관계 컨텍스트 (있는 경우)
- **사용자 정보** (예: 위치, 네트워크, IP)
- **장치 정보** (예: 브라우저, OS, 버전, 언어, 해상도, 모델)
- **세션 정보**

### <a name="telemetry-initializers"></a>원격 분석 이니셜라이저
원격 분석 이니셜라이저는 사용자의 브라우저에서 전송 되기 전에 수집 된 원격 분석의 내용을 수정 하는 데 사용 됩니다. 또한 `false`을 반환 하 여 특정 원격 분석이 전송 되는 것을 방지 하는 데 사용할 수 있습니다. 여러 원격 분석 이니셜라이저를 Application Insights 인스턴스에 추가할 수 있으며 이러한 이니셜라이저를 추가 하기 위해 실행 됩니다.

`addTelemetryInitializer`에 대 한 입력 인수는 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 인수로 사용 하 고 `boolean` 또는 `void`를 반환 하는 콜백입니다. `false`반환 하는 경우 원격 분석 항목이 전송 되지 않고, 다른 원격 분석 이니셜라이저 (있는 경우)로 진행 되거나 원격 분석 컬렉션 엔드포인트으로 전송 됩니다.

원격 분석 이니셜라이저를 사용 하는 예제:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>구성
대부분의 구성 필드의 이름은 기본적으로 false로 설정 될 수 있습니다. `instrumentationKey`를 제외 하 고 모든 필드는 선택 사항입니다.

| 이름 | 기본값 | Description |
|------|---------|-------------|
| instrumentationKey | null | **필수**<br>Azure Portal에서 가져온 계측 키입니다. |
| accountId | null | 앱이 사용자를 계정으로 그룹화 하는 경우 계정 ID (선택 사항)입니다. 공백, 쉼표, 세미콜론, 같음 또는 세로 막대가 없습니다. |
| sessionRenewalMs | 180만 | 사용자가이 시간 (밀리초) 동안 비활성 상태 이면 세션이 기록 됩니다. 기본값은 30분입니다. |
| sessionExpirationMs | 8640만 | 이 시간 (밀리초) 동안 계속 되 면 세션이 기록 됩니다. 기본값은 24 시간입니다. |
| maxBatchSizeInBytes | 10000 | 원격 분석 일괄 처리의 최대 크기입니다. 일괄 처리가이 제한을 초과 하면 즉시 전송 되 고 새 일괄 처리가 시작 됩니다. |
| maxBatchInterval | 15000 | 보내기 전에 원격 분석을 일괄 처리 하는 시간 (밀리초) |
| disableExceptionTracking | false | True 이면 예외가 자동으로 수집 되지 않습니다. 기본값은 false입니다. |
| disableTelemetry | false | True 이면 원격 분석이 수집 되거나 전송 되지 않습니다. 기본값은 false입니다. |
| enableDebug | false | True 이면 SDK 로깅 설정에 관계 없이 **내부** 디버깅 데이터가 기록 되는 **대신** 예외로 throw 됩니다. 기본값은 false입니다. <br>***참고:*** 이 설정을 사용 하도록 설정 하면 내부 오류가 발생할 때마다 원격 분석이 삭제 됩니다. 이는 SDK의 구성 또는 사용과 관련 된 문제를 신속 하 게 식별 하는 데 유용할 수 있습니다. 디버깅 하는 동안 원격 분석을 잃지 않으려면 `enableDebug`대신 `consoleLoggingLevel` 또는 `telemetryLoggingLevel`를 사용 하는 것이 좋습니다. |
| loggingLevelConsole | 0 | **내부** Application Insights 오류를 콘솔에 기록 합니다. <br>0: off, <br>1: 심각한 오류만, <br>2: 모든 항목 (오류 & 경고) |
| loggingLevelTelemetry | 1 | **내부** Application Insights 오류를 원격 분석으로 보냅니다. <br>0: off, <br>1: 심각한 오류만, <br>2: 모든 항목 (오류 & 경고) |
| diagnosticLogInterval | 10000 | 사내 내부 로깅 큐의 폴링 간격 (밀리초) |
| samplingPercentage | 100 | 전송 될 이벤트의 백분율입니다. 기본값은 100입니다. 즉, 모든 이벤트가 전송 됩니다. 대규모 응용 프로그램에 대 한 데이터 캡을 유지 하려는 경우에 설정 합니다. |
| autoTrackPageVisitTime | false | True 이면 페이지 보기에서 이전에 계측 된 페이지의 뷰 시간이 추적 되어 원격 분석으로 전송 되 고 현재 페이지 보기에 대해 새 타이머가 시작 됩니다. 기본값은 false입니다. |
| disableAjaxTracking | false | True 이면 Ajax 호출이 자동으로 수집 되지 않습니다. 기본값은 false입니다. |
| disableFetchTracking | true | True 이면 인출 요청이 자동으로 수집 되지 않습니다. 기본값은 true입니다. |
| overridePageViewDuration | false | True 이면 trackPageView의 기본 동작이 trackPageView가 호출 될 때 페이지 보기 기간 간격의 끝을 기록 하도록 변경 됩니다. False이 고 trackPageView에 사용자 지정 기간이 제공 되지 않으면 탐색 타이밍 API를 사용 하 여 페이지 보기 성능이 계산 됩니다. 기본값은 false입니다. |
| maxAjaxCallsPerView | 500 | 기본 500-페이지 보기 당 모니터링할 Ajax 호출 수를 제어 합니다. 페이지에서 모든 (무제한) Ajax 호출을 모니터링 하려면-1로 설정 합니다. |
| disableDataLossAnalysis | true | False 이면 내부 원격 분석 보낸 사람 버퍼를 시작할 때 아직 보내지 않은 항목을 확인 합니다. |
| disableCorrelationHeaders | false | False 이면 SDK에서 모든 종속성 요청에 두 개의 헤더 (' 요청 Id ' 및 ' 요청 컨텍스트 ')를 추가 하 여 서버 쪽의 해당 요청과 상관 관계를 설정 합니다. 기본값은 false입니다. |
| correlationHeaderExcludedDomains |  | 특정 도메인에 대 한 상관 관계 헤더 사용 안 함 |
| correlationHeaderDomains |  | 특정 도메인에 대 한 상관 관계 헤더 사용 |
| disableFlushOnBeforeUnload | false | 기본값은 false입니다. True 이면 onBeforeUnload 이벤트 트리거가 발생할 때 flush 메서드가 호출 되지 않습니다. |
| enableSessionStorageBuffer | true | 기본값은 true입니다. True 이면 모든 보내지 않은 원격 분석을 포함 하는 버퍼가 세션 저장소에 저장 됩니다. 페이지가 로드 되 면 버퍼가 복원 됩니다. |
| isCookieUseDisabled | false | 기본값은 false입니다. True 이면 SDK가 쿠키의 데이터를 저장 하거나 읽지 않습니다.|
| cookieDomain | null | 사용자 지정 쿠키 도메인입니다. 이 기능은 하위 도메인에서 Application Insights 쿠키를 공유 하려는 경우에 유용 합니다. |
| isRetryDisabled | false | 기본값은 false입니다. False 인 경우 206 (부분 성공), 408 (시간 제한), 429 (너무 많은 요청), 500 (내부 서버 오류), 503 (서비스를 사용할 수 없음), 0 (오프 라인, 검색 된 경우에만)을 다시 시도 합니다. |
| isStorageUseDisabled | false | True 이면 SDK가 로컬 및 세션 저장소에서 데이터를 저장 하거나 읽지 않습니다. 기본값은 false입니다. |
| isBeaconApiDisabled | true | False 이면 SDK에서 [신호 API](https://www.w3.org/TR/beacon) 를 사용 하 여 모든 원격 분석을 보냅니다. |
| onunloadDisableBeacon | false | 기본값은 false입니다. 탭이 닫히면 SDK는 [신호 API](https://www.w3.org/TR/beacon) 를 사용 하 여 나머지 모든 원격 분석을 보냅니다. |
| sdkExtension | null | Sdk 확장 이름을 설정 합니다. 영문자 문자만 사용할 수 있습니다. 확장 이름이 ' sdkVersion ' 태그에 접두사로 추가 됩니다 (예: ' ext_javascript: 2.0.0 '). 기본값은 null입니다. |
| isBrowserLinkTrackingEnabled | false | 기본값은 false입니다. True 이면 SDK에서 모든 [브라우저 링크](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) 요청을 추적 합니다. |
| appId | null | AppId는 서버측 요청과 함께 클라이언트 쪽에서 발생 하는 AJAX 종속성 간의 상관 관계에 사용 됩니다. 신호 API를 사용 하는 경우 자동으로 사용할 수 없지만 구성에서 수동으로 설정할 수 있습니다. 기본값은 null입니다. |
| enableCorsCorrelation | false | True 이면 SDK는 나가는 AJAX 종속성을 서버 쪽의 해당 요청과 상관 관계를 지정 하기 위해 모든 CORS 요청에 두 개의 헤더 (' 요청 Id ' 및 ' 요청-컨텍스트 ')를 추가 합니다. 기본값은 false입니다. |
| namePrefix | 정의되지 않음 | LocalStorage 및 쿠키 이름에 대해 이름 후 위로 사용할 선택적 값입니다.
| enableAutoRouteTracking | false | SPA (단일 페이지 응용 프로그램)의 경로 변경 내용을 자동으로 추적 합니다. True 이면 각 경로 변경 시 Application Insights에 새 페이지 보기 전송 됩니다. 해시 경로 변경 (`example.com/foo#bar`)도 새 페이지 뷰로 기록 됩니다.
| enableRequestHeaderTracking | false | True 이면 AJAX & 인출 요청 헤더가 추적 되 고 기본값은 false입니다.
| enableResponseHeaderTracking | false | True 이면 AJAX & Fetch 요청의 응답 헤더가 추적 되 고 기본값은 false입니다.
| distributedTracingMode | `DistributedTracingModes.AI` | 분산 추적 모드를 설정 합니다. AI_AND_W3C 모드 또는 W3C 모드가 설정 된 경우 W3C 추적 컨텍스트 헤더 (traceparent/tracestate)가 생성 되 고 나가는 모든 요청에 포함 됩니다. AI_AND_W3C은 레거시 Application Insights 계측 된 서비스와의 이전 버전과의 호환성을 위해 제공 됩니다.

## <a name="single-page-applications"></a>단일 페이지 응용 프로그램

기본적으로이 SDK는 단일 페이지 응용 프로그램에서 발생 하는 상태 기반 경로 변경을 처리 **하지** 않습니다. 단일 페이지 응용 프로그램에 대 한 자동 경로 변경 내용 추적을 사용 하도록 설정 하려면 설치 구성에 `enableAutoRouteTracking: true`를 추가 하면 됩니다.

현재이 SDK를 사용 하 여 초기화할 수 있는 별도의 [반응 플러그 인](#react-extensions) 을 제공 합니다. 또한 사용자에 대 한 경로 변경 추적을 수행 하 고 [다른 반응 특정 원격 분석](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)을 수집 합니다.

## <a name="react-extensions"></a>확장에 대응

| 확장 |
|---------------|
| [React](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>브라우저/클라이언트 쪽 데이터 탐색

**메트릭** 으로 이동 하 고 관심 있는 개별 메트릭을 추가 하 여 브라우저/클라이언트 쪽 데이터를 볼 수 있습니다. 

![](./media/javascript/page-view-load-time.png)

포털의 브라우저 경험을 통해 JavaScript SDK에서 데이터를 볼 수도 있습니다.

**브라우저** 를 선택한 다음 **실패** 또는 **성능**을 선택 합니다.

![](./media/javascript/browser.png)

### <a name="performance"></a>성능 중심 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>종속성

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>분석기능 

JavaScript SDK에 의해 수집 된 원격 분석을 쿼리하려면 **로그 (분석)에서 보기** 단추를 선택 합니다. `client_Type == "Browser"``where` 문을 추가 하면 JavaScript SDK의 데이터만 볼 수 있고 다른 Sdk에서 수집한 서버 쪽 원격 분석은 제외 됩니다.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>원본 맵 지원

예외 원격 분석의 매우 많은 호출 스택이 Azure Portal에서 확인할 수 없습니다. 예외 세부 정보 패널의 모든 기존 통합은 새로 적용 되지 않은 호출 스택에 적용 됩니다. 끌어서 놓기 소스 맵 unminifying는 모든 기존 및 향후 JS Sdk (+ NODE.JS)를 지원 하므로 SDK 버전을 업그레이드할 필요가 없습니다. 취소할 수 없는 호출 스택을 보려면
1. Azure Portal에서 예외 원격 분석 항목을 선택 하 여 "종단 간 트랜잭션 정보"를 확인 합니다.
2. 이 호출 스택에 해당 하는 소스 맵을 식별 합니다. 소스 맵은 스택 프레임의 소스 파일과 일치 해야 하지만 `.map` 접미사가 붙습니다.
3. 소스 맵을 Azure Portal의 호출 스택으로 끌어 놓습니다 ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights 웹 기본

간단한 경험을 위해의 기본 버전을 대신 설치할 수 있습니다 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
이 버전은 최소한의 기능과 기능을 제공 하며, 적합 한 것으로 빌드에 의존 합니다. 예를 들어 autocollection (catch 되지 않은 예외, AJAX 등)을 수행 합니다. `trackTrace`, `trackException`등의 특정 원격 분석 유형을 전송 하는 Api는이 버전에 포함 되지 않으므로 고유한 래퍼를 제공 해야 합니다. 유일 하 게 사용할 수 있는 API는 `track`입니다. [샘플](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) 은 여기에 있습니다.

## <a name="examples"></a>예시

실행 가능한 예제는 [Application Insights JAVASCRIPT SDK 샘플](https://github.com/topics/applicationinsights-js-demo) 을 참조 하세요.

## <a name="upgrading-from-the-old-version-of-application-insights"></a>이전 버전의 Application Insights에서 업그레이드

SDK V2 버전의 주요 변경 내용:
- 더 나은 API 서명을 허용 하기 위해 trackPageView, 기능 예외 등의 일부 API 호출이 업데이트 되었습니다. IE8 또는 낮은 버전의 브라우저에서 실행 되는 것은 지원 되지 않습니다.
- 원격 분석 봉투 (envelope)에는 데이터 스키마 업데이트로 인 한 필드 이름 및 구조 변경 내용이 있습니다.
- `context.operation`을 `context.telemetryTrace`로 이동 했습니다. 일부 필드도 변경 되었습니다 (`operation.id` --> `telemetryTrace.traceID`).
  - 현재 페이지 보기 ID (예: SPA 앱)를 수동으로 새로 고치려면 `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`를 사용 하 여이 작업을 수행할 수 있습니다.

현재 application insights PRODUCTION SDK (1.0.20)를 사용 하 고 새 SDK가 런타임에 작동 하는지 확인 하려는 경우 현재 SDK 로드 시나리오에 따라 URL을 업데이트 합니다.

- CDN 시나리오를 통해 다운로드: 현재 다음 URL을 가리키는 데 사용 하는 코드 조각을 업데이트 합니다.
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 시나리오: `downloadAndSetup`를 호출 하 여 CDN에서 전체 ApplicationInsights 스크립트를 다운로드 하 고 계측 키를 사용 하 여 초기화 합니다.

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

내부 환경에서 테스트를 수행 하 여 모니터링 원격 분석이 예상 대로 작동 하는지 확인 합니다. 모두 작동 하는 경우 API를 SDK V2 버전으로 적절 하 게 업데이트 하 고 프로덕션 환경에 배포 합니다.

## <a name="sdk-performanceoverhead"></a>SDK 성능/오버 헤드

25kb gzipped에서 초기화 하는 데 ~ 15 밀리초만 소요 되는 Application Insights은 웹 사이트에 무시 되는 크기의 loadtime을 추가 합니다. 코드 조각을 사용 하면 라이브러리의 최소 구성 요소가 빠르게 로드 됩니다. 한편 전체 스크립트는 백그라운드에서 다운로드 됩니다.

스크립트를 CDN에서 다운로드 하는 동안 페이지의 모든 추적이 큐에 대기 됩니다. 다운로드 한 스크립트가 비동기적으로 초기화 되 면 큐에 대기 된 모든 이벤트가 추적 됩니다. 따라서 페이지의 전체 수명 주기 중에는 원격 분석이 손실 되지 않습니다. 이 설치 프로세스를 통해 사용자에 게 보이지 않는 원활한 분석 시스템을 페이지에 제공 합니다.

> 요약:
> - **25kb** gzipped
> - **15 밀리초** 전체 초기화 시간
> - 페이지 수명 주기 동안 **0** 추적이 누락 되었습니다.

## <a name="browser-support"></a>브라우저 지원

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 최신 ✔ |  Firefox 최신 ✔ | IE 9 + & Edge ✔ | 최신 ✔ Opera | Safari 최신 ✔ |

## <a name="open-source-sdk"></a>오픈 소스 SDK

JavaScript SDK Application Insights는 소스 코드를 보거나 프로젝트에 참여 하 여 [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-JS)를 방문 하는 오픈 소스입니다.

## <a name="next"></a> 다음 단계
* [사용 현황 추적](usage-overview.md)
* [사용자 지정 이벤트 및 메트릭](api-custom-events-metrics.md)
* [빌드 - 측정 - 학습](usage-overview.md)
