---
title: '빠른 시작: Azure Monitor Application Insights를 사용하여 Node.js 모니터링'
description: Azure Monitor Application Insights를 사용하여 모니터링하도록 Node.js 웹앱을 빠르게 설정하는 지침을 제공합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: a6d6d70336badeaa86c9982dfa977ea389ed5402
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963532"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>빠른 시작: Azure Application Insights를 사용하여 Node.js 웹 애플리케이션 모니터링 시작

이 빠른 시작에서는 기존 Node.js 웹 애플리케이션에 Node.js용 Application Insights SDK 버전 0.22를 추가합니다.

Azure Application Insights를 사용하면 웹 애플리케이션의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다. 또한 사용자가 보고할 때까지 기다리지 않고 애플리케이션의 오류를 빠르게 식별하고 진단할 수 있습니다. SDK 릴리스 버전 0.20 이상을 사용하면 MongoDB, MySQL 및 Redis를 포함하여 일반적인 타사 패키지를 모니터링할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* 작동하는 Node.js 애플리케이션입니다.

## <a name="enable-application-insights"></a>Application Insights 사용

Application Insights는 온-프레미스 또는 클라우드에서 실행되는지 여부와 상관없이 인터넷에 연결된 모든 애플리케이션에서 원격 분석 데이터를 수집할 수 있습니다. 이 데이터를 보기 시작하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. **리소스 만들기** > **개발자 도구** > **Application Insights**를 선택합니다.

   ![Azure Application Insights 리소스 추가](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Application Insights 리소스를 처음 만드는 경우 자세한 내용은 [Application Insights 리소스 만들기](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 문서를 참조하세요.

   구성 페이지가 표시되면 다음 표를 사용하여 입력 필드를 채웁니다. 

    | 설정        | 값           | Description  |
   | ------------- |:-------------|:-----|
   | **이름**      | 전역적으로 고유한 값 | 모니터링 중인 앱을 식별하는 이름 |
   | **리소스 그룹**     | myResourceGroup      | AppInsights 데이터를 호스팅할 새 리소스 그룹의 이름입니다. 새 리소스 그룹을 만들거나 기존 그룹을 사용할 수 있습니다. |
   | **위치** | 미국 동부 | 가까운 위치 또는 응용 프로그램이 호스팅되는 위치 근처를 선택합니다. |

3. **만들기**를 선택합니다.

## <a name="configure-appinsights-sdk"></a>AppInsights SDK 구성

1. **개요**를 선택하고, 애플리케이션의 **계측 키**를 복사합니다.

   ![Application Insights 계측 키 보기](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Node.js용 Application Insights SDK를 애플리케이션에 추가합니다. 응용 프로그램의 루트 폴더에서 다음을 실행합니다.

   ```bash
   npm install applicationinsights --save
   ```

3. 앱의 첫 번째 *.js* 파일을 편집하고, 스크립트의 맨 위쪽에 아래 두 줄을 추가합니다. [Node.js 빠른 시작 앱](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)을 사용하는 경우 *index.js* 파일을 수정합니다. `<instrumentation_key>`를 애플리케이션의 계측 키로 바꿉니다. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. 응용 프로그램을 다시 시작합니다.

> [!NOTE]
> 데이터가 포털에 표시되려면 3-5분 정도 걸립니다. 이 응용 프로그램이 트래픽이 적은 테스트 앱인 경우 활성 요청이나 작업이 발생하는 경우에만 대부분의 메트릭이 캡처됩니다.

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 시작

1. 이제 계측 키를 검색한 Azure Portal에서 Application Insights **개요** 페이지를 다시 열어 현재 실행 중인 애플리케이션에 대한 세부 정보를 볼 수 있습니다.

   ![Application Insights 개요 메뉴](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. 애플리케이션 구성 요소 간의 종속성 관계에 대한 시각적 레이아웃을 보려면 **애플리케이션 맵**을 선택합니다. 각 구성 요소에는 로드, 성능, 오류 및 경고와 같은 KPI가 표시됩니다.

   ![Application Insights 애플리케이션 맵](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. **앱 분석** 아이콘 ![애플리케이션 맵 아이콘](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **Analytics에서 보기**를 선택합니다.  이 작업은 Application Insights에 의해 수집된 모든 데이터를 분석하기 위한 풍부한 쿼리 언어를 제공하는 **Application Insights Analytics**를 엽니다. 이 경우 요청 수를 차트로 렌더링하는 쿼리가 생성됩니다. 사용자 고유의 쿼리를 작성하여 다른 데이터를 분석할 수 있습니다.

   ![Application Insights Analytics 그래프](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. **개요** 페이지로 돌아가서 KPI 그래프를 검사합니다.  이 대시보드는 들어오는 요청의 수, 해당 요청의 기간 및 발생한 모든 오류를 포함하여 애플리케이션 상태에 대한 통계를 제공합니다.

   ![Application Insights 상태 개요 타임라인 그래프](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   **페이지 보기 로드 시간** 차트를 **클라이언트 쪽 원격 분석** 데이터로 채우도록 하려면 이 스크립트를 추적하려는 각 페이지에 추가합니다.

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. 왼쪽에서 **메트릭**을 선택합니다. 메트릭 탐색기를 사용하여 리소스의 상태 및 사용률을 조사합니다. **새 차트 추가**를 선택하여 사용자 지정 보기를 추가로 만들거나, **편집**을 선택하여 기존 차트의 종류, 높이, 색상표, 그룹화 및 메트릭을 수정할 수 있습니다. 예를 들어 메트릭 드롭다운의 "브라우저 페이지 로드 시간"과 집계의 "Avg"를 선택하여 평균 브라우저 페이지 로드 시간을 표시하는 차트를 만들 수 있습니다. Azure 메트릭 탐색기에 대해 자세히 알아보려면 [Azure 메트릭 탐색기 시작](../../azure-monitor/platform/metrics-getting-started.md)을 방문하세요.

   ![Application Insights Server 메트릭 그래프](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Node.js 모니터링에 대한 자세한 내용은 [AppInsights Node.js 추가 설명서](../../azure-monitor/app/nodejs.md)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

테스트를 완료하면 리소스 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 이 작업을 수행하려면 다음 단계를 따르세요.

> [!NOTE]
> 기존 리소스 그룹을 사용하는 경우 아래 지침이 작동하지 않으므로 개별 Application Insights 리소스를 삭제하기만 하면 됩니다. 리소스 그룹을 삭제할 때마다 해당 그룹의 구성원인 모든 underyling 리소스가 삭제됩니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택한 다음, **myResourceGroup**을 선택합니다.
2. 리소스 그룹 페이지에서 **삭제**를 선택하고, 텍스트 상자에서 **myResourceGroup**을 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [성능 문제 찾기 및 진단](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)
