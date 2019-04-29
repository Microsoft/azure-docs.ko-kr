---
title: Azure Application Insights를 사용하여 Node.js 서비스 모니터링 | Microsoft Docs
description: Application Insights를 사용하여 Node.js 서비스의 성능을 모니터링하고 문제를 진단합니다.
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: mbullwin
ms.openlocfilehash: 13379111706eaa816a8fa16cfe72711b7bf4d739
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576479"
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Application Insights를 사용하여 Node.js 서비스 및 앱 모니터링

백 엔드 서비스 및 구성 요소를 배포한 후 [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)로 모니터링하여 [성능 및 기타 문제를 신속하게 발견하고 진단](../../azure-monitor/app/detect-triage-diagnose.md)할 수 있습니다. 데이터 센터, Azure VM 및 웹앱, 다른 공용 클라우드에서도 호스팅되는 Node.js 서비스에 대한 Application Insights를 사용할 수 있습니다.

모니터링 데이터를 수신, 저장 및 탐색하려면 코드에 SDK를 포함하고 Azure에서 해당 Application Insights 리소스를 설정합니다. SDK는 추가 분석 및 탐색을 위해 해당 리소스로 데이터를 보냅니다.

Node.js SDK는 들어오고 나가는 HTTP 요청, 예외 및 여러 시스템 메트릭을 자동으로 모니터링할 수 있습니다. 0.20 버전부터 SDK는 MongoDB, MySQL 및 Redis와 같은 몇 가지 일반적인 타사 패키지를 모니터링할 수 있습니다. 들어오는 HTTP 요청과 관련된 모든 이벤트는 좀 더 빠른 문제 해결을 위해 상호 관계가 지정됩니다.

TelemetryClient API를 사용하여 앱 및 시스템의 추가적인 여러 측면을 수동으로 계측하고 모니터링 할 수 있습니다. TelemetryClient API는 이 문서의 뒷부분에 더 자세히 설명합니다.

## <a name="get-started"></a>시작하기

앱 또는 서비스에 대한 모니터링을 설정하려면 다음 작업을 완료합니다.

### <a name="prerequisites"></a>필수 조건

시작하기 전에 Azure 구독이 있는지 확인하여 없는 경우 [무료 계정을 새로 만듭니다][azure-free-offer]. 조직에 이미 Azure 구독이 있으면 관리자가 [다음 지침][add-aad-user]에 따라 사용자를 구독에 추가할 수 있습니다.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Application Insights 리소스 설정


1. [Azure Portal][portal]에 로그인합니다.
2. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다. 리소스에는 원격 분석 데이터를 수신하기 위한 엔드포인트, 이 데이터 저장소, 저장된 보고서 및 대시보드, 규칙 및 경고 구성이 포함됩니다.

3. 리소스 만들기 페이지의 **애플리케이션 유형** 상자에서 **Node.js Application**을 선택합니다. 앱 유형에 따라 생성되는 기본 대시보드 및 보고서가 결정됩니다. (모든 Application Insights 리소스는 모든 언어 및 플랫폼에서 데이터를 수집할 수 있습니다.)

### <a name="sdk"></a> Node.js SDK 설정

데이터를 수집할 수 있도록 앱에 SDK를 포함합니다. 

1. Azure Portal에서 리소스의 계측 키(*ikey*라고도 함)를 복사합니다. Application Insights는 ikey를 사용하여 Azure 리소스에 데이터를 매핑합니다. SDK가 ikey를 사용하려면 먼저 환경 변수 또는 코드에 ikey를 지정해야 합니다.  

   ![계측 키 복사](./media/nodejs/instrumentation-key-001.png)

2. package.json을 통해 Node.js SDK 라이브러리를 앱의 종속성에 추가합니다. 앱의 루트 폴더에서 다음을 실행합니다.

   ```bash
   npm install applicationinsights --save
   ```

3. 코드에서 라이브러리를 명시적으로 로드합니다. SDK는 여러 다른 라이브러리에 계측 값을 삽입합니다. 따라서 최대한 신속하게, 심지어 다른 `require` 문보다도 먼저 라이브러리를 로드합니다. 

   첫 번째 .js 파일의 맨 위에 다음 코드를 추가합니다. `setup` 메서드는 기본적으로 모든 추적 항목에 사용할 ikey(따라서 Azure 리소스까지)를 구성합니다.

   ```javascript
   const appInsights = require("applicationinsights");
   appInsights.setup("<instrumentation_key>");
   appInsights.start();
   ```
   
   ikey를 `setup()` 또는 `new appInsights.TelemetryClient()`에 수동으로 전달하는 대신 APPINSIGHTS\_INSTRUMENTATIONKEY 환경 변수를 통해 제공할 수도 있습니다. 이렇게 하면 ikey가 커밋된 소스 코드의 영향을 받지 않으며 다른 환경에 다른 ikey를 지정할 수 있습니다.

   추가 구성 옵션의 경우 다음 섹션을 참조하세요.

   `appInsights.defaultClient.config.disableAppInsights = true`를 설정하면 원격 분석을 전송하지 않고 SDK를 사용해 볼 수 있습니다.

### <a name="monitor"></a> 앱 모니터링

SDK는 Node.js 런타임 및 일부 일반적인 타사 모듈에 대한 원격 분석을 자동으로 수집합니다. 애플리케이션을 사용하여 이 데이터를 생성합니다.

그런 다음, [Azure Portal][portal]에서 이전에 만든 Application Insights 리소스로 이동합니다. **타임라인 개요**에서 먼저 몇 가지 데이터 요소를 찾습니다. 더 자세한 데이터를 보려면 차트에서 다른 구성 요소를 선택합니다.

앱에 대해 검색된 토폴로지를 보려면 **애플리케이션 맵** 단추를 선택합니다. 자세한 내용을 보려면 맵에서 구성 요소를 선택합니다.

![간단한 앱 맵](./media/nodejs/application-map-002.png)

앱에 대해 자세히 알아보고 문제를 해결하려면 **조사** 섹션에서 사용 가능한 다른 보기를 선택합니다.

![조사 섹션](./media/nodejs/007-investigate-pane.png)

#### <a name="no-data"></a>데이터가 없나요?

SDK는 제출할 데이터를 일괄 처리하기 때문에 항목이 포털에 표시될 때까지 지연될 수 있습니다. 리소스에 데이터가 보이지 않으면 다음 해결 방법 중 몇 가지를 시도해 보세요.

* 애플리케이션을 계속 사용합니다. 더 많은 작업을 수행하여 더 많은 원격 분석을 생성합니다.
* 포털 리소스 보기에서 **새로 고침**을 클릭합니다. 차트는 자체에서 주기적으로 새로 고치지만, 수동으로 새로 고침하면 즉시 새로 고쳐집니다.
* [필요한 발신 포트](../../azure-monitor/app/ip-addresses.md)가 열려 있는지 확인합니다.
* [검색](../../azure-monitor/app/diagnostic-search.md)을 사용하여 특정 이벤트를 찾습니다.
* [FAQ][FAQ]를 확인합니다.


## <a name="sdk-configuration"></a>SDK 구성

SDK의 구성 메서드 및 기본 값은 다음 코드 예제에 나열됩니다.

서비스에서 이벤트의 상관 관계를 완전하게 지정하려면 `.setAutoDependencyCorrelation(true)`을 설정합니다. 이 옵션을 설정하면 SDK가 Node.js의 비동기 콜백에서 컨텍스트를 추적할 수 있습니다.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient API

TelemetryClient API에 대한 전체 설명은 [사용자 지정 이벤트 및 메트릭용 Application Insights API](../../azure-monitor/app/api-custom-events-metrics.md)를 참조하세요.

Application Insights Node.js SDK를 사용하여 모든 요청, 이벤트, 메트릭 또는 예외를 추적할 수 있습니다. 다음 코드 예제에는 사용할 수 있는 몇 가지 API가 나와 있습니다.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>종속성 추적

다음 코드를 사용하여 종속성을 추적합니다.

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>모든 이벤트에 사용자 지정 속성 추가

다음 코드를 사용하여 모든 이벤트에 사용자 지정 속성을 추가합니다.

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>HTTP GET 요청 추적

다음 코드를 사용하여 HTTP GET 요청을 추적합니다.

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>서버 시작 시간 추적

다음 코드를 사용하여 서버 시작 시간을 추적합니다.

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>다음 단계

* [포털에서 원격 분석 모니터링](../../azure-monitor/app/app-insights-dashboards.md)
* [원격 분석에 분석 쿼리 작성](../../azure-monitor/log-query/get-started-portal.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: ../../azure-monitor/app/troubleshoot-faq.md

