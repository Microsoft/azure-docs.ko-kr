---
title: Azure Application Insights로 ASP.NET 웹앱 모니터링 | Microsoft Docs
description: Application Insights를 사용하여 모니터링하도록 ASP.NET 웹앱을 빠르게 설정하는 지침을 제공합니다.
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: ed7cec34b4bca1882d069997d4f89677cda32447
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88080952"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>ASP.NET 웹 애플리케이션 모니터링 시작

Azure Application Insights를 사용하면 웹 애플리케이션의 가용성, 성능 및 사용량을 쉽게 모니터링할 수 있습니다.  또한 사용자가 보고할 때까지 기다리지 않고 애플리케이션의 오류를 빠르게 식별하고 진단할 수 있습니다.  앱의 성능 및 효율성에 대한 Application Insights에서 수집하는 정보를 사용하면 애플리케이션을 유지 관리하고 개선하는 데 더 많은 정보를 갖고 선택할 수 있습니다.

이 빠른 시작에서는 애플리케이션을 분석하는 데 사용할 수 있는 다양한 방법 중 하나인 기존 ASP.NET 웹 애플리케이션에 Application Insights를 추가하고 실시간 통계 분석을 시작하는 방법을 보여줍니다. ASP.NET 웹 애플리케이션이 없는 경우 [ASP.NET 웹앱 빠른 시작 만들기](../../app-service/quickstart-dotnet-framework.md)에 따라 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작을 완료하려면 다음이 필요합니다.

- 다음 워크로드로 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)를 설치합니다.
    - ASP.NET 및 웹 개발
    - Azure 개발


Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="enable-application-insights"></a>Application Insights 사용

1. Visual Studio 2019에서 프로젝트를 엽니다.
2. 프로젝트 메뉴에서 **Application Insights 구성**을 선택합니다. Visual Studio는 애플리케이션에 Application Insights SDK를 추가합니다.

    > [!IMPORTANT]
    > Application Insights를 추가하는 프로세스는 ASP.NET 템플릿 유형에 따라 다릅니다. **빈** 템플릿이나 **Azure 모바일 앱** 템플릿을 사용하는 경우에는 **프로젝트** > **Application Insights 원격 분석 추가**를 선택합니다. 다른 모든 ASP.NET 템플릿의 경우 위의 단계에서 지침을 참조하세요. 

3. **시작**을 클릭합니다(Visual Studio의 이전 버전에는 **무료 시작** 단추가 있음).

    ![Visual Studio에 Application Insights 추가](./media/quick-monitor-portal/add-application-insights-b.png)

4. 구독을 선택하고 **등록**을 클릭합니다.

5. **프로젝트** > **NuGet 패키지 관리** > **패키지 소스: nuget.org** > **업데이트**를 선택하여 Application Insights SDK 패키지를 안정적인 최신 릴리스로 업데이트합니다.

6. **디버그** 메뉴에서 **디버깅 시작**을 선택하거나 F5 키를 눌러서 애플리케이션을 실행합니다.

## <a name="confirm-app-configuration"></a>앱 구성 확인

Application Insights는 실행 중인 위치에 관계 없이 애플리케이션에 대한 원격 분석 데이터를 수집합니다. 이 데이터를 보기 시작하려면 다음 단계를 사용합니다.

1. **보기** -> **기타 Windows** -> **Application Insights 검색**을 클릭하여 Application Insights를 엽니다.  현재 세션에서 원격 분석이 표시됩니다.<BR><br>![Visual Studio에서의 원격 분석](./media/quick-monitor-portal/telemetry-in-vs.png)

2. 목록(이 예에서 홈/인덱스 가져오기)에서 첫 번째 요청을 클릭하여 요청 정보를 확인합니다. 요청에 관한 다른 중요한 정보와 함께 상태 코드 및 응답 시간이 모두 포함됩니다.<br><br>![Visual Studio에서 응답 세부 정보](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Azure Portal에서 모니터링 시작

이제 Azure Portal에서 Application Insights를 열어 실행 중인 애플리케이션에 대한 다양한 세부 정보를 볼 수 있습니다.

1. 솔루션 탐색기에서 **연결된 서비스** 폴더(구름과 플러그 모양의 아이콘)를 확장한 후 **Application Insights** 폴더를 마우스 오른쪽 단추로 클릭하고 **Application Insights 포털 열기**를 클릭합니다.  애플리케이션에 대한 몇 가지 정보와 다양한 옵션이 표시됩니다.

    ![애플리케이션 맵](media/quick-monitor-portal/04-overview.png)

2. **애플리케이션 맵**을 클릭하여 애플리케이션 구성 요소 간의 종속 관계에 대한 시각적 레이아웃을 봅니다.  각 구성 요소에는 로드, 성능, 오류 및 경고와 같은 KPI가 표시됩니다.

    ![애플리케이션 맵](media/quick-monitor-portal/05-appmap.png)

3. 애플리케이션 구성 요소 중 하나에서 **앱 분석** 아이콘 ![애플리케이션 맵](media/quick-monitor-portal/app-viewinlogs-icon.png) **Logs(Analytics)에서 보기**를 클릭합니다. 그러면 Application Insights에서 수집한 모든 데이터를 분석하기 위한 풍부한 쿼리 언어를 제공하는 **Logs(Analytics)** 가 열립니다. 이 경우 요청 수를 차트로 렌더링하는 쿼리가 생성됩니다. 사용자 고유의 쿼리를 작성하여 다른 데이터를 분석할 수 있습니다.

    ![분석](media/quick-monitor-portal/6viewanalytics.png)

4. 왼쪽의 조사 아래에 있는 **라이브 메트릭 스트림**을 클릭합니다. 그러면 애플리케이션이 실행될 때 이에 대한 실시간 통계가 표시됩니다. 여기에는 들어오는 요청 수, 그러한 요청의 기간 및 발생하는 모든 오류와 같은 정보가 포함됩니다. 또한 프로세서 및 메모리와 같은 중요한 성능 메트릭을 검사할 수 있습니다.

    ![라이브 스트림](media/quick-monitor-portal/7livemetrics.png)

    Azure에서 애플리케이션을 호스팅할 준비가 되었으면 이제 게시할 수 있습니다. [ASP.NET 웹앱 빠른 시작 만들기](../../app-service/quickstart-dotnetcore.md#update-the-app-and-redeploy)에 설명된 단계를 따릅니다.

5. Visual Studio를 사용하여 Application Insights 모니터링을 추가할 경우 클라이언트 쪽 모니터링을 자동으로 추가할 수 있습니다. 애플리케이션에 수동으로 클라이언트 측 모니터링을 추가하려면 다음 JavaScript를 애플리케이션에 추가합니다.

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

자세한 내용은 GitHub 리포지토리의 [오픈 소스 JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리
테스트를 완료하면 리소스 그룹 및 모든 관련 리소스를 삭제할 수 있습니다. 이 작업을 수행하려면 다음 단계를 따르세요.
1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**과 **myResourceGroup**을 차례로 클릭합니다.
2. 리소스 그룹 페이지에서 **삭제**를 클릭하고 텍스트 상자에 **myResourceGroup**을 입력한 후 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure Application Insights로 모니터링하기 위해 애플리케이션을 활성화했습니다.  애플리케이션에서 통계를 모니터링하고 문제를 감지하는 방법을 알아보려면 이 자습서를 계속합니다.

> [!div class="nextstepaction"]
> [Azure Application Insights 자습서](tutorial-runtime-exceptions.md)
