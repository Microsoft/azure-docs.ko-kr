---
title: JavaScript 웹 앱에 대 한 Azure 애플리케이션 정보
description: 페이지 보기 및 세션 수, 웹 클라이언트 데이터, SPA (단일 페이지 응용 프로그램)를 가져오고 사용 패턴을 추적 합니다. JavaScript 웹 페이지의 예외 및 성능 문제를 감지합니다.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 3acb7379644b5bfcb22ed86b6bde7031095fef24
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224856"
---
# <a name="application-insights-for-web-pages"></a>웹 페이지용 Application Insights

웹 페이지 또는 앱의 성능 및 사용량 현황에 대해 알아봅니다. 페이지 스크립트에 [Application Insights](app-insights-overview.md) 을 추가 하면 페이지 로드 및 ajax 호출의 타이밍, 브라우저 예외 및 ajax 오류에 대 한 세부 정보 뿐만 아니라 사용자 및 세션 수가 표시 됩니다. 이러한 모든 요소를 페이지, 클라이언트 OS 및 브라우저 버전, 지리적 위치 및 기타 차원으로 분할할 수 있습니다. 실패 횟수 또는 느린 페이지 로딩에 대한 경고를 설정할 수도 있습니다. 또한 JavaScript 코드에 추적 호출을 삽입하여 웹 페이지 애플리케이션의 다양한 기능 사용 방법을 추적할 수 있습니다.

Application Insights는 다른 웹 페이지와 함께 사용할 수 있습니다. 간단한 JavaScript만 추가하면 됩니다. 웹 서비스가 [Java](java-get-started.md) 또는 [ASP.NET](asp-net.md)인 경우 클라이언트 쪽 JavaScript sdk와 함께 서버 쪽 sdk를 사용 하 여 응용 프로그램의 성능에 대 한 종단 간 이해를 얻을 수 있습니다.

## <a name="adding-the-javascript-sdk"></a>JavaScript SDK 추가

1. 먼저 Application Insights 리소스가 필요 합니다. 리소스 및 계측 키가 아직 없는 경우 [새 리소스 만들기 지침](create-new-resource.md)을 따르세요.
2. 1 단계에서 JavaScript 원격 분석을 전송 하려는 리소스에 대 한 _계측 키_ ("ikey" 라고도 함)를 복사 합니다. `instrumentationKey` Application Insights JAVASCRIPT SDK의 설정에 추가 합니다.
3. 다음 두 옵션 중 하나를 통해 웹 페이지 또는 앱에 Application Insights JavaScript SDK를 추가 합니다.
    * [npm 설정](#npm-based-setup)
    * [JavaScript 코드 조각](#snippet-based-setup)

> [!IMPORTANT]
> JavaScript SDK를 응용 프로그램에 추가 하려면 메서드를 하나만 사용 합니다. NPM 설치 프로그램을 사용 하는 경우 코드 조각을 사용 하지 말고 그 반대의 경우도 마찬가지입니다.

> [!NOTE]
> NPM 설치 프로그램은 JavaScript SDK를 프로젝트에 대 한 종속성으로 설치 하 고 IntelliSense를 사용 하도록 설정 하는 반면 코드 조각은 런타임에 SDK를 인출 합니다. 둘 다 동일한 기능을 지원 합니다. 그러나 더 많은 사용자 지정 이벤트 및 구성을 원하는 개발자는 일반적으로 NPM 설치를 선택 하는 반면, 사용자는 코드 조각에 대 한 기본 웹 분석 opt를 신속 하 게 사용할 수 있습니다.

### <a name="npm-based-setup"></a>npm 기반 설정

NPM를 통해 설치 합니다.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **이 패키지에는 제공이 포함 되어**있으므로 별도의 제공 패키지를 설치할 필요가 **없습니다** .
    
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

앱에서 npm를 사용 하지 않는 경우 각 페이지의 맨 위에이 코드 조각을 붙여넣어 Application Insights를 사용 하 여 웹 페이지를 직접 계측할 수 있습니다. `<head>`모든 종속성 및 선택적으로 JavaScript 오류와 관련 된 잠재적인 문제를 모니터링할 수 있도록 섹션에서 첫 번째 스크립트 여야 합니다. Blazor Server 앱을 사용 하는 경우 섹션에서 파일의 맨 위에 코드 조각을 추가 `_Host.cshtml` `<head>` 합니다.

응용 프로그램에서 사용 하는 코드 조각의 버전을 추적 하는 데 도움이 되도록 2.5.5 버전부터 페이지 보기 이벤트에는 식별 된 조각 버전을 포함 하는 새 태그 "ai. 코드 조각"이 포함 됩니다.

현재 코드 조각 (아래에 나열 됨)은 버전 "3"으로 식별 됩니다.

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,u="script",k="instrumentationKey",D="ingestionendpoint",C="disableExceptionTracking",E="ai.device.",I="toLowerCase",b="crossOrigin",w="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"4",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[I](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,p,l,u;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][I]()]=i[1])}if(!e[D]){var r=e.endpointsuffix,o=r?e.location:null;e[D]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[k]||d[k]||"",p=s[D],l=p?p+"/v2/track":config.endpointUrl,(u=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=l,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),u.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,l)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:w,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(w,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(u,l))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(u);n.src=h;var e=y[b];return!e&&""!==e||"undefined"==n[b]||(n[b]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(u)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[C]&&!0!==s[C]){method="onerror",t(["_"+method]);var c=T[method];T[method]=function(e,t,n,a,i){var r=c&&c(e,t,n,a,i);return!0!==r&&m["_"+method]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);(T[t]=n).queue&&0===n.queue.length&&n.trackPageView({})}(window,document,{
src: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js", // The SDK URL Source
//name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
//ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
//useXhr: 1, // Use XHR instead of fetch to report failures (if available),
//crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag 
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> 가독성을 높이고 가능한 JavaScript 오류를 줄이기 위해 가능한 모든 구성 옵션은 위의 코드 조각 코드에서 새 줄에 나열 됩니다. 주석 처리 된 줄의 값을 변경 하지 않으려는 경우 제거할 수 있습니다.


#### <a name="reporting-script-load-failures"></a>스크립트 로드 오류 보고

이 버전의 코드 조각은 CDN에서 SDK를 Azure Monitor 포털에 대 한 예외로 로드 하는 경우 오류를 감지 하 고 보고 합니다 (오류 &gt; 예외 &gt; 브라우저에서) .이 예외는 응용 프로그램이 예상 대로 원격 분석 (또는 기타 예외)을 보고 하지 않는다는 것을 알 수 있도록이 형식의 오류에 대 한 가시성을 제공 합니다. 이 신호는 SDK가 로드 또는 초기화 되지 않아 다음을 수행할 수 있기 때문에 원격 분석이 손실 되었음을 이해 하는 데 중요 한 측정 단위입니다.
- 사용자가 사이트를 사용 하거나 사용을 시도 하는 방법을 보고 합니다.
- 최종 사용자가 사이트를 사용 하는 방법에 대 한 원격 분석이 누락 됨
- 최종 사용자가 사이트를 사용 하 여 성공적으로 차단 될 수 있는 JavaScript 오류가 누락 되었습니다.

이 예외에 대 한 자세한 내용은 [SDK 로드 실패](javascript-sdk-load-failure.md) 문제 해결 페이지를 참조 하세요.

포털에 대 한 예외로이 오류를 보고 하는 것은 application insights 구성의 구성 옵션을 사용 하지 않기 ```disableExceptionTracking``` 때문에이 오류가 발생 하는 경우에도 창이 사용 하지 않도록 설정 된 경우에도 코드 조각에서 항상 보고 됩니다.

SDK 로드 오류에 대 한 보고는 특히 IE 8에서 지원 되지 않습니다. 이를 통해 대부분의 환경이 단독 IE 8이 아닌 것으로 가정 하 여 코드 조각의 크기를 줄이는 데 도움이 됩니다. 이러한 요구 사항을 충족 하 고 이러한 예외를 수신 하려는 경우 fetch poly fill을 포함 하거나 대신에서 사용 하는 코드 조각 버전을 만들어야 ```XDomainRequest``` ```XMLHttpRequest``` 합니다. 제공 된 코드 [조각 소스 코드](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) 를 시작 지점으로 사용 하는 것이 좋습니다.

> [!NOTE]
> 이전 버전의 코드 조각을 사용 하는 경우 이전에 보고 되지 않은 문제를 수신 하도록 최신 버전으로 업데이트 하는 것이 좋습니다.

#### <a name="snippet-configuration-options"></a>코드 조각 구성 옵션

이제 SDK를 로드 하지 못하는 경우를 제외 하 고 오류 보고를 사용 하지 않도록 설정 하는 JavaScript 오류가 실수로 도입 되는 것을 방지 하기 위해 모든 구성 옵션이 스크립트 끝으로 이동 되었습니다.

각 구성 옵션은 위에 표시 된 항목의 기본값을 [옵션]으로 재정의 하지 않으려는 경우 반환 된 페이지의 결과 크기를 최소화 하기 위해 해당 줄을 제거할 수 있습니다.

사용 가능한 구성 옵션은 

| 속성 | Type | 설명
|------|------|----------------
| src | 문자열 **[필수]** | SDK를 로드할 위치의 전체 URL입니다. 이 값은 동적으로 추가 된 스크립트/태그의 "src" 특성에 사용 됩니다 &lt; &gt; . 공용 CDN 위치나 개인적으로 호스트 된 항목을 사용할 수 있습니다.
| name | 문자열 *[선택 사항]* | 초기화 된 SDK에 대 한 전역 이름 `appInsights` 입니다. 기본값은입니다. 는 ```window.appInsights``` 초기화 된 인스턴스에 대 한 참조입니다. 참고: 이름 값을 제공 하거나 이전 인스턴스가 할당 된 것으로 나타나는 경우 (전역 이름 appInsightsSDK을 통해)이 이름 값도 전역 네임 스페이스에 정의 됩니다 .이 이름 값은 ```window.appInsightsSDK=<name value>``` SDK 초기화 코드에서 올바른 코드 조각 구조 및 프록시 메서드를 초기화 하 고 업데이트 하는 데 필요 합니다.
| ld | 시간 (밀리초 *) [선택 사항]* | SDK 로드를 시도 하기 전에 대기 하는 로드 지연 시간을 정의 합니다. 기본값은 0ms 같고 이며, 음수 값은 페이지의 헤드 영역에 스크립트 태그를 즉시 추가 합니다 &lt; &gt; . 그러면 스크립트가 로드 되거나 실패할 때까지 페이지 로드 이벤트가 차단 됩니다.
| useXhr | 부울 *[선택 사항]* | 이 설정은 보고 SDK 로드 오류에만 사용 됩니다. 보고는 사용 가능한 경우 fetch ()를 먼저 사용 하려고 시도 하 고 XHR로 대체 합니다 .이 값을 true로 설정 하면 인출 확인만 무시 됩니다. 반입이 오류 이벤트를 보내지 못하는 환경에서 응용 프로그램을 사용 하는 경우에만이 값을 사용 해야 합니다.
| 위치 원점 | 문자열 *[선택 사항]* | 이 설정을 포함 하 여 SDK를 다운로드 하는 데 추가 된 스크립트 태그에는이 문자열 값을 포함 하는 간 원본 특성이 포함 됩니다. 정의 되지 않은 경우 (기본값) 간 원본 특성이 추가 되지 않습니다. 권장 값은 정의 되지 않습니다 (기본값). ""; 또는 "anonymous" (모든 유효한 값의 경우 [HTML 특성: `crossorigin` ](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin) 문서 참조)
| cfg | 개체 **[필수]** | 초기화 하는 동안 Application Insights SDK에 전달 되는 구성입니다.

### <a name="sending-telemetry-to-the-azure-portal"></a>Azure Portal 원격 분석 보내기

기본적으로 JavaScript SDK Application Insights는 응용 프로그램의 상태와 기본 사용자 환경을 결정 하는 데 도움이 되는 여러 원격 분석 항목을 자동으로 수집 합니다. 이러한 개체는 다음과 같습니다.

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
- **장치 정보** (예: 브라우저, OS, 버전, 언어, 모델)
- **세션 정보**

### <a name="telemetry-initializers"></a>원격 분석 이니셜라이저
원격 분석 이니셜라이저는 사용자의 브라우저에서 전송 되기 전에 수집 된 원격 분석의 내용을 수정 하는 데 사용 됩니다. 또한를 반환 하 여 특정 원격 분석이 전송 되는 것을 방지 하는 데 사용할 수 있습니다 `false` . 여러 원격 분석 이니셜라이저를 Application Insights 인스턴스에 추가할 수 있으며 이러한 이니셜라이저를 추가 하기 위해 실행 됩니다.

에 대 한 입력 인수는 `addTelemetryInitializer` 를 인수로 사용 하 [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) 고 또는를 반환 하는 `boolean` 콜백입니다 `void` . 을 반환 하는 경우 `false` 원격 분석 항목이 전송 되지 않고, 다른 원격 분석 이니셜라이저 (있는 경우)로 진행 되거나 원격 분석 컬렉션 끝점으로 전송 됩니다.

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
대부분의 구성 필드의 이름은 기본적으로 false로 설정 될 수 있습니다. 을 제외한 모든 필드는 선택 사항 `instrumentationKey` 입니다.

| 속성 | 기본값 | Description |
|------|---------|-------------|
| instrumentationKey | null | **필수**<br>Azure Portal에서 가져온 계측 키입니다. |
| accountId | null | 앱이 사용자를 계정으로 그룹화 하는 경우 계정 ID (선택 사항)입니다. 공백, 쉼표, 세미콜론, 같음 또는 세로 막대가 없습니다. |
| sessionRenewalMs | 180만 | 사용자가이 시간 (밀리초) 동안 비활성 상태 이면 세션이 기록 됩니다. 기본값은 30분입니다. |
| sessionExpirationMs | 8640만 | 이 시간 (밀리초) 동안 계속 되 면 세션이 기록 됩니다. 기본값은 24 시간입니다. |
| maxBatchSizeInBytes | 10000 | 원격 분석 일괄 처리의 최대 크기입니다. 일괄 처리가이 제한을 초과 하면 즉시 전송 되 고 새 일괄 처리가 시작 됩니다. |
| maxBatchInterval | 15000 | 보내기 전에 원격 분석을 일괄 처리 하는 시간 (밀리초) |
| disableExceptionTracking | false | True 이면 예외가 자동으로 수집 되지 않습니다. 기본값은 false입니다. |
| disableTelemetry | false | True 이면 원격 분석이 수집 되거나 전송 되지 않습니다. 기본값은 false입니다. |
| enableDebug | false | True 이면 SDK 로깅 설정에 관계 없이 **내부** 디버깅 데이터가 기록 되는 **대신** 예외로 throw 됩니다. 기본값은 false입니다. <br>***참고:*** 이 설정을 사용 하도록 설정 하면 내부 오류가 발생할 때마다 원격 분석이 삭제 됩니다. 이는 SDK의 구성 또는 사용과 관련 된 문제를 신속 하 게 식별 하는 데 유용할 수 있습니다. 디버깅 하는 동안 원격 분석을 잃지 않으려면 대신 또는를 사용 하는 것이 좋습니다 `consoleLoggingLevel` `telemetryLoggingLevel` `enableDebug` . |
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
| isBrowserLinkTrackingEnabled | false | 기본값은 false입니다. True 이면 SDK에서 모든 [브라우저 링크](/aspnet/core/client-side/using-browserlink) 요청을 추적 합니다. |
| appId | null | AppId는 서버측 요청과 함께 클라이언트 쪽에서 발생 하는 AJAX 종속성 간의 상관 관계에 사용 됩니다. 신호 API를 사용 하는 경우 자동으로 사용할 수 없지만 구성에서 수동으로 설정할 수 있습니다. 기본값은 null입니다. |
| enableCorsCorrelation | false | True 이면 SDK는 나가는 AJAX 종속성을 서버 쪽의 해당 요청과 상관 관계를 지정 하기 위해 모든 CORS 요청에 두 개의 헤더 (' 요청 Id ' 및 ' 요청-컨텍스트 ')를 추가 합니다. 기본값은 false입니다. |
| namePrefix | 정의되지 않음 | LocalStorage 및 쿠키 이름에 대해 이름 후 위로 사용할 선택적 값입니다.
| enableAutoRouteTracking | false | SPA (단일 페이지 응용 프로그램)의 경로 변경 내용을 자동으로 추적 합니다. True 이면 각 경로 변경 시 Application Insights에 새 페이지 보기 전송 됩니다. 해시 경로 변경 ( `example.com/foo#bar` ) 또한 새 페이지 뷰로 기록 됩니다.
| enableRequestHeaderTracking | false | True 이면 AJAX & 인출 요청 헤더가 추적 되 고 기본값은 false입니다.
| enableResponseHeaderTracking | false | True 이면 AJAX & Fetch 요청의 응답 헤더가 추적 되 고 기본값은 false입니다.
| distributedTracingMode | `DistributedTracingModes.AI` | 분산 추적 모드를 설정 합니다. AI_AND_W3C 모드 또는 W3C 모드가 설정 된 경우 W3C 추적 컨텍스트 헤더 (traceparent/tracestate)가 생성 되 고 나가는 모든 요청에 포함 됩니다. AI_AND_W3C은 레거시 Application Insights 계측 된 서비스와의 이전 버전과의 호환성을 위해 제공 됩니다. 예제는 [여기](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps)를 참조 하세요.
| enableAjaxErrorStatusText | false | 기본값은 false입니다. True 이면 실패 한 AJAX 요청에 대 한 종속성 이벤트에 응답 오류 데이터 텍스트를 포함 합니다.
| enableAjaxPerfTracking | false | 기본값은 false입니다. 추가 브라우저 창을 조회 하 고 포함 하는 데 사용할 플래그입니다. 보고 된 `ajax` (xhr 및 fetch)의 성능 타이밍이 보고 된 메트릭을 보고 합니다.
| maxAjaxPerfLookupAttempts | 3 | 기본값은 3입니다. 창을 찾을 수 있는 최대 횟수입니다. 성능 타이밍 (사용할 수 있는 경우)은 모든 브라우저에서 창을 채우지 않기 때문에 필요 합니다. XHR 요청의 끝을 보고 하기 전의 성능 및 인출 요청에 대 한 성능이 완료 된 후에 추가 됩니다.
| ajaxPerfLookupDelay | 25 | 기본값은 25 밀리초입니다. Windows 성능 타이밍을 다시 시도 하기 전에 대기 하는 시간입니다. 요청에 대 한 성능 타이밍 ( `ajax` 밀리초 단위)이 고 setTimeout ()에 직접 전달 됩니다.
| enableUnhandledPromiseRejectionTracking | false | True 이면 처리 되지 않은 약속 거부는 자동으로 수집 되 고 JavaScript 오류로 보고 됩니다. DisableExceptionTracking이 true 이면 (예외를 추적 하지 않음) 구성 값이 무시 되 고 처리 되지 않은 약속 거부는 보고 되지 않습니다.

## <a name="single-page-applications"></a>단일 페이지 응용 프로그램

기본적으로이 SDK는 단일 페이지 응용 프로그램에서 발생 하는 상태 기반 경로 변경을 처리 **하지** 않습니다. 단일 페이지 응용 프로그램에 대해 자동 경로 변경 추적을 사용 하도록 설정 하려면 `enableAutoRouteTracking: true` 설치 구성에를 추가 하면 됩니다.

현재이 SDK를 사용 하 여 초기화할 수 있는 별도의 [반응 플러그 인](#react-extensions)을 제공 합니다. 또한 사용자에 대 한 경로 변경 추적을 수행 하 고 [다른 반응 특정 원격 분석](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)을 수집 합니다.

> [!NOTE]
> `enableAutoRouteTracking: true`반응 플러그 인을 사용 **하지 않는** 경우에만를 사용 합니다. 두 가지 모두 경로가 변경 될 때 새 PageViews를 보낼 수 있습니다. 둘 다 사용 하도록 설정 된 경우 중복 PageViews를 보낼 수 있습니다.

## <a name="configuration-autotrackpagevisittime"></a>구성: autoTrackPageVisitTime

를 설정 하면 `autoTrackPageVisitTime: true` 각 페이지에서 사용자가 소비한 시간이 추적 됩니다. 새 페이지 보기에서 *이전* 페이지에서 사용자가 소비한 시간은 이라는 [사용자 지정 메트릭으로](../platform/metrics-custom-overview.md) 전송 됩니다 `PageVisitTime` . 이 사용자 지정 메트릭은 [메트릭 탐색기](../platform/metrics-getting-started.md) "로그 기반 메트릭"으로 볼 수 있습니다.

## <a name="react-extensions"></a>확장에 대응

| 확장 |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|

## <a name="correlation"></a>상관 관계

클라이언트와 서버 쪽 간 상관 관계는 다음에 대해 지원 됩니다.

- XHR/AJAX 요청 
- 인출 요청 

클라이언트와 서버 쪽 간 상관 관계는 및 요청에 대해 **지원 되지 않습니다** `GET` `POST` .

### <a name="enable-cross-component-correlation-between-client-ajax-and-server-requests"></a>클라이언트 AJAX와 서버 요청 간에 구성 요소 간 상관 관계 사용

상관 관계를 사용 하도록 설정 하려면 `CORS` 클라이언트에서 두 개의 추가 요청 헤더 `Request-Id` 와를 전송 해야 `Request-Context` 하며, 서버 쪽에서 해당 헤더와의 연결을 허용할 수 있어야 합니다. JavaScript SDK 구성 내에서를 설정 하 여 이러한 헤더를 보낼 수 `enableCorsCorrelation: true` 있습니다. 

`Access-Control-Allow-Headers`서버 쪽의 구성에 따라 및를 수동으로 추가 하 여 서버 쪽 목록을 확장 해야 하는 경우가 많습니다 `Request-Id` `Request-Context` .

액세스 제어-허용 헤더: `Request-Id` , `Request-Context` , `<your header>`

클라이언트에서 통신 하는 타사 서버에서 및 헤더를 수락할 수 없고 해당 서버 `Request-Id` 에서 `Request-Context` 구성을 업데이트할 수 없는 경우 구성 속성을 통해 제외 목록에 해당 서버를 배치 해야 `correlationHeaderExcludeDomains` 합니다. 이 속성은 와일드 카드를 지원 합니다.

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    enableCorsCorrelation: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

## <a name="explore-browserclient-side-data"></a>브라우저/클라이언트 쪽 데이터 탐색

**메트릭** 으로 이동 하 고 관심 있는 개별 메트릭을 추가 하 여 브라우저/클라이언트 쪽 데이터를 볼 수 있습니다.

![웹 응용 프로그램에 대 한 메트릭 데이터의 그래픽 표시를 보여 주는 Application Insights 메트릭 페이지의 스크린샷](./media/javascript/page-view-load-time.png)

포털의 브라우저 경험을 통해 JavaScript SDK에서 데이터를 볼 수도 있습니다.

**브라우저** 를 선택한 다음 **실패** 또는 **성능**을 선택 합니다.

![웹 응용 프로그램에 대해 볼 수 있는 메트릭에 브라우저 오류 또는 브라우저 성능을 추가 하는 방법을 보여 주는 Application Insights 브라우저 페이지의 스크린샷](./media/javascript/browser.png)

### <a name="performance"></a>성능

![웹 응용 프로그램에 대 한 작업 메트릭의 그래픽 표시를 보여 주는 Application Insights의 성능 페이지 스크린샷](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>종속성

![웹 응용 프로그램에 대 한 종속성 메트릭의 그래픽 표시를 보여 주는 Application Insights의 성능 페이지 스크린샷](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>분석

JavaScript SDK에 의해 수집 된 원격 분석을 쿼리하려면 **로그 (분석)에서 보기** 단추를 선택 합니다. 문을 추가 하 여 `where` `client_Type == "Browser"` JavaScript SDK의 데이터만 볼 수 있으며 다른 sdk에서 수집 된 서버 쪽 원격 분석은 제외 됩니다.
 
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

예외 원격 분석의 매우 많은 호출 스택이 Azure Portal에서 확인할 수 없습니다. 예외 세부 정보 패널의 모든 기존 통합은 새로 적용 되지 않은 호출 스택에 적용 됩니다.

#### <a name="link-to-blob-storage-account"></a>Blob storage 계정에 연결

사용자 고유의 Azure Blob Storage 컨테이너에 Application Insights 리소스를 연결 하 여 호출 스택을 자동으로 취소할 수 있습니다. 시작 하려면 [자동 소스 맵 지원](./source-map-support.md)을 참조 하세요.

### <a name="drag-and-drop"></a>끌어서 놓기

1. Azure Portal에서 예외 원격 분석 항목을 선택 하 여 "종단 간 트랜잭션 정보"를 확인 합니다.
2. 이 호출 스택에 해당 하는 소스 맵을 식별 합니다. 소스 맵은 스택 프레임의 소스 파일과 일치 해야 하지만 다음에는 접미사가 붙습니다. `.map`
3. 소스 맵을 ![ Azure Portal의 호출 스택 창에서 빌드 폴더의 소스 맵 파일을 끌어서 놓는 방법을 보여 주는 애니메이션 이미지 Azure Portal의 호출 스택으로 끌어 놓습니다.](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights 웹 기본

간단한 경험을 위해의 기본 버전을 대신 설치할 수 있습니다 Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
이 버전은 최소한의 기능과 기능을 제공 하며, 적합 한 것으로 빌드에 의존 합니다. 예를 들어 autocollection (catch 되지 않은 예외, AJAX 등)을 수행 합니다. 특정 원격 분석 유형 (예:, 등)을 전송 하는 Api는 `trackTrace` `trackException` 이 버전에 포함 되지 않으므로 고유한 래퍼를 제공 해야 합니다. 유일 하 게 사용할 수 있는 API는 `track` 입니다. [샘플](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) 은 여기에 있습니다.

## <a name="examples"></a>예제

실행 가능한 예제는 [Application Insights JAVASCRIPT SDK 샘플](https://github.com/topics/applicationinsights-js-demo) 을 참조 하세요.

## <a name="upgrading-from-the-old-version-of-application-insights"></a>이전 버전의 Application Insights에서 업그레이드

SDK V2 버전의 주요 변경 내용:
- 더 나은 API 서명을 허용 하기 위해 trackPageView 및 기능 예외와 같은 API 호출 중 일부는 업데이트 되었습니다. Internet Explorer 8 및 이전 버전의 브라우저에서를 실행 하는 것은 지원 되지 않습니다.
- 데이터 스키마 업데이트로 인해 원격 분석 봉투 (envelope)에 필드 이름 및 구조 변경 내용이 있습니다.
- `context.operation`로 이동 `context.telemetryTrace` 했습니다. 일부 필드도 변경 되었습니다 ( `operation.id`  -->  `telemetryTrace.traceID` ).
  - 현재 페이지 보기 ID (예: SPA 앱)를 수동으로 새로 고치려면를 사용 `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()` 합니다.
    > [!NOTE]
    > 이전에를 사용 하 여 추적 ID를 고유 하 게 유지 하려면 `Util.newId()` 이제를 사용 `Util.generateW3CId()` 합니다. 결국 모두 작업 ID가 됩니다.

현재 application insights PRODUCTION SDK (1.0.20)를 사용 하 고 새 SDK가 런타임에 작동 하는지 확인 하려는 경우 현재 SDK 로드 시나리오에 따라 URL을 업데이트 합니다.

- CDN 시나리오를 통해 다운로드: 현재 다음 URL을 가리키는 데 사용 하는 코드 조각을 업데이트 합니다.
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm 시나리오: `downloadAndSetup` 를 호출 하 여 CDN에서 전체 ApplicationInsights 스크립트를 다운로드 하 고 계측 키를 사용 하 여 초기화 합니다.

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

내부 환경에서 테스트를 수행 하 여 모니터링 원격 분석이 예상 대로 작동 하는지 확인 합니다. 모두 작동 하는 경우 API를 SDK V2 버전으로 적절 하 게 업데이트 하 고 프로덕션 환경에 배포 합니다.

## <a name="sdk-performanceoverhead"></a>SDK 성능/오버 헤드

36 KB gzipped에서 초기화 하는 데 Application Insights ~ 15 밀리초만 소요 되는 경우에는 웹 사이트에 무시 되는 크기의 loadtime이 추가 됩니다. 코드 조각을 사용 하면 라이브러리의 최소 구성 요소가 빠르게 로드 됩니다. 한편 전체 스크립트는 백그라운드에서 다운로드 됩니다.

스크립트를 CDN에서 다운로드 하는 동안 페이지의 모든 추적이 큐에 대기 됩니다. 다운로드 한 스크립트가 비동기적으로 초기화 되 면 큐에 대기 된 모든 이벤트가 추적 됩니다. 따라서 페이지의 전체 수명 주기 중에는 원격 분석이 손실 되지 않습니다. 이 설치 프로세스를 통해 사용자에 게 보이지 않는 원활한 분석 시스템을 페이지에 제공 합니다.

> 요약:
> - ![npm 버전](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![gzip 압축 크기](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 밀리초** 전체 초기화 시간
> - 페이지 수명 주기 동안 **0** 추적이 누락 되었습니다.

## <a name="browser-support"></a>브라우저 지원

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome 최신 ✔ |  Firefox 최신 ✔ | IE 9 + & Edge ✔<br>IE 8 호환 | 최신 ✔ Opera | Safari 최신 ✔ |

## <a name="es3ie8-compatibility"></a>ES3/IE8 호환성

SDK로는 고객이 사용 하는 브라우저를 제어할 수 없는 수많은 사용자가 있습니다. 따라서이 SDK가 계속 "작동" 하 고 이전 브라우저에서 로드 될 때 JS 실행을 중단 하지 않도록 해야 합니다. IE8 및 이전 세대 (ES3) 브라우저를 지원 하지 않는 것이 이상적 이지만, 계속 해 서 페이지를 "작업" 해야 하며, 최종 사용자가 사용 하도록 선택 하는 브라우저를 제어할 수 있는지 여부를 제어할 수 없는 수많은 많은 고객/사용자가 있습니다.

이는 ES3 코드 호환성을 유지 해야 하 고 새로운 기능을 추가할 때, ES3 JavaScript 구문 분석을 중단 하 고 선택적 기능으로 추가 하지 않는 방식으로 추가 해야 한다는 것을 의미 하는 것은 아닙니다.

[IE8 지원에 대 한 자세한 내용은 GitHub를 참조 하세요.](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>오픈 소스 SDK

JavaScript SDK Application Insights는 소스 코드를 보거나 프로젝트에 참여 하 여 [공식 GitHub 리포지토리](https://github.com/Microsoft/ApplicationInsights-JS)를 방문 하는 오픈 소스입니다. 

최신 업데이트 및 버그 수정에 대해서는 [릴리스 정보를 참조](./release-notes.md)하세요.

## <a name="next-steps"></a><a name="next"></a> 다음 단계
* [사용 현황 추적](usage-overview.md)
* [사용자 지정 이벤트 및 메트릭](api-custom-events-metrics.md)
* [빌드 - 측정 - 학습](usage-overview.md)
* [SDK 로드 오류 문제 해결](javascript-sdk-load-failure.md)
