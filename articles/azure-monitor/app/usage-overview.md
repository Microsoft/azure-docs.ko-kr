---
title: Azure Application Insights로 사용 분석 | Microsoft Docs
description: 어떤 사용자가 앱으로 어떤 작업을 수행하는지 이해합니다.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: f2539d5250ff436a720fe10f748f40db29b0ee25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783429"
---
# <a name="usage-analysis-with-application-insights"></a>Application Insights를 사용하여 사용량 분석

가장 인기 있는 웹 또는 모바일 앱의 기능은 무엇인가요? 사용자가 앱으로 이러한 목표를 달성하고 있나요? 특정 지점에서 나갔다가 나중에 돌아오나요?  [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 사람들이 사용자의 앱을 사용하는 방식을 잘 이해할 수 있도록 도와줍니다. 앱을 업데이트할 때마다 사용자들에게 얼마나 적절한지 평가할 수 있습니다. 이러한 지식을 바탕으로 다음 개발 주기에 대해 데이터 중심의 결정을 내릴 수 있습니다.

## <a name="send-telemetry-from-your-app"></a>앱에서 원격 분석 보내기

앱 서버 코드와 웹 페이지에 모두 Application Insights를 설치하여 최상의 환경을 얻습니다. 앱의 클라이언트 및 서버 구성 요소는 분석을 위해 Azure Portal로 원격 분석을 다시 보냅니다.

1. **서버 코드:** [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md) 또는 [기타](../../azure-monitor/app/platforms.md) 앱에 적합한 모듈을 설치합니다.

    * *서버 코드를 설치하지 않으려면 [Azure Application Insights 리소스를 만들기만](../../azure-monitor/app/create-new-resource.md ) 하면 됩니다.*

2. **웹 페이지 코드:** ``</head>``를 닫기 전에 다음 스크립트를 웹 페이지에 추가합니다. 계측 키를 Application Insights 리소스에 대한 적절한 값으로 바꿉니다.

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    웹 사이트 모니터링을 위한 고급 구성에 대해 자세히 알아보려면 [JavaScript SDK API 참조](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)를 확인하세요.

3. **모바일 앱 코드:** App Center SDK를 사용하여 앱에서 이벤트를 수집한 다음, 분석을 위해 [이 가이드에 따라](../../azure-monitor/learn/mobile-center-quickstart.md) 이러한 이벤트의 복사본을 Application Insights로 보냅니다.

4. **원격 분석 가져오기:** 몇 분 동안 디버그 모드에서 프로젝트를 실행한 다음, Application Insights의 개요 블레이드에서 결과를 찾습니다.

    앱을 게시하여 앱의 성능을 모니터링하고 사용자가 앱으로 수행하는 작업을 확인합니다.

## <a name="include-user-and-session-id-in-your-telemetry"></a>원격 분석에 사용자 및 세션 ID를 포함합니다.
시간이 지남에 따라 사용자를 추적하려면 Application Insights는 식별하는 방법이 필요합니다. 이벤트 도구는 사용자 ID 또는 세션 ID가 필요 없는 유일한 사용 도구입니다.

[이 프로세스](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)를 통해 사용자 및 세션 ID 보내기를 시작합니다.

## <a name="explore-usage-demographics-and-statistics"></a>사용 현황 인구 통계 및 통계 탐색
사람들이 사용자의 앱을 사용하는 경우, 가장 큰 관심을 갖는 페이지, 사용자가 있는 위치 및 사용하는 브라우저 및 운영 체제를 알아봅니다. 

사용자 및 세션 보고서는 페이지 또는 사용자 지정 이벤트를 기준으로 데이터를 필터링하고 위치, 환경 및 페이지 등의 속성으로 나눕니다. 필터를 직접 추가할 수도 있습니다.

![사용자](./media/usage-overview/users.png)  

오른쪽의 자세한 정보에는 데이터 집합에서 주목할 만한 패턴이 나와 있습니다.  

* **사용자** 보고서는 선택한 기간 내에 페이지에 액세스하는 고유한 사용자 수를 계산합니다. 웹앱의 경우 쿠키를 사용하여 사용자 수가 계산됩니다. 누군가가 다른 브라우저 또는 클라이언트 컴퓨터를 사용하여 사이트에 액세스하는 경우 두 번 이상 계산됩니다.
* **세션** 보고서는 사이트에 액세스하는 사용자 세션의 수를 계산합니다. 세션은 30분 이상의 비활동 기간마다 종료되는 사용자의 활동 기간입니다.

[사용자, 세션 및 이벤트 도구에 대한 추가 정보](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>재방문 주기 - 다시 찾아온 사용자는 몇 명이나 되나요?

재방문 주기는 특정 시간 버킷 동안 일부 비즈니스 작업을 수행한 사용자의 코호트를 기준으로 사용자가 해당 앱을 다시 사용하는 빈도를 이해하는 데 도움이 됩니다. 

- 사용자가 다시 찾아오게 만드는 특정 기능 이해 
- 실제 사용자 데이터에 따라 가설 세우기 
- 재방문 주기가 제품에서 문제가 되는지 여부 확인 

![보존](./media/usage-overview/retention.png) 

상단의 재방문 주기 컨트롤을 사용하여 재방문 주기를 계산할 특정 이벤트 및 시간 범위를 정의할 수 있습니다. 중간에 표시되는 그래프는 지정된 시간 범위별로 전반적인 재방문 주기 비율을 시각적으로 보여 줍니다. 하단의 그래프는 지정된 기간 내의 개별 재방문 주기를 나타냅니다. 이 수준의 세부 정보를 사용하면 사용자가 수행하는 작업과 다시 방문한 사용자에게 영향을 미칠 수 있는 요인을 좀 더 자세히 이해할 수 있습니다.  

[재방문 주기 도구에 대한 추가 정보](usage-retention.md)

## <a name="custom-business-events"></a>사용자 지정 비즈니스 이벤트

사용자가 앱으로 수행하는 작업을 명확하게 이해하려는 경우 사용자 지정 이벤트를 로깅하는 코드 줄을 삽입하면 유용합니다. 이러한 이벤트는 특정 단추 클릭과 같은 자세한 사용자 작업부터 구매하기나 게임에서 이기기 등과 같은 좀 더 중요한 비즈니스 이벤트에 이르기까지 모든 사항을 추적할 수 있습니다. 

경우에 따라 페이지 보기에 유용한 이벤트가 표시될 수 있지만 일반적으로는 그렇지 않습니다. 사용자는 제품 페이지를 열기만 하고 제품을 구입하지 않을 수 있습니다. 

특정 비즈니스 이벤트를 사용하여 사이트를 통한 사용자의 진행 상황을 차트로 나타낼 수 있습니다. 다양한 옵션에 대한 사용자의 선호도를 알아내고 사이트를 나가거나 어려움을 느끼는 경우를 알 수 있습니다. 이러한 지식을 바탕으로 개발 백로그에서 우선 순위를 결정할 수 있습니다.

이벤트는 앱의 클라이언트 쪽에서 로깅될 수 있습니다.

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

또는 서버 쪽에서 다음을 수행할 수 있습니다.

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

포털에서 검사할 때 이벤트를 필터링하거나 분할할 수 있도록 이러한 이벤트에 속성 값을 추가할 수 있습니다. 또한 익명 사용자 ID와 같은 표준 속성 집합이 각 이벤트에 추가되므로 개별 사용자의 활동 시퀀스를 추적할 수 있습니다.

[사용자 지정 이벤트](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) 및 [속성](../../azure-monitor/app/api-custom-events-metrics.md#properties)에 대해 자세히 알아보세요.

### <a name="slice-and-dice-events"></a>이벤트 분석 및 분할

사용자, 세션 및 이벤트 도구에서 사용자, 이벤트 이름 및 속성별로 사용자 지정 이벤트를 분석 및 분할할 수 있습니다.
![사용자](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>앱을 사용하여 원격 분석 디자인

앱의 각 기능을 디자인할 때 사용자를 대상으로 앱의 성공 여부를 측정하는 방법을 고려합니다. 기록해야 하는 비즈니스 이벤트를 결정하고 해당 이벤트에 대한 추적 호출을 처음부터 앱에 코딩합니다.

## <a name="a--b-testing"></a>A | B 테스트
기능의 어느 변형이 보다 성공적인지 알 수 없는 경우, 다른 사용자가 각각 접근할 수 있도록 하여 둘 다 릴리스합니다. 각각의 성공 여부를 측정한 다음 통합 버전으로 이동합니다.

이 기술의 경우 사용자 앱의 각 버전이 전송한 모든 원격 분석에 고유 속성 값을 연결합니다. 활성화된 TelemetryContext에서 속성을 정의하여 수행할 수 있습니다. 이러한 기본 속성은 사용자 지정 메시지뿐 아니라 표준 원격 분석도 애플리케이션이 전송하는 모든 원격 분석 메시지에 추가됩니다.

Application Insights 포털에서 속성 값에 대해 데이터를 필터링하고 분할하여 다른 버전과 비교할 수 있습니다.

이렇게 하려면 [원격 분석 이니셜라이저를 설정](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer)합니다.

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

Global.asax.cs 같은 웹앱 이니셜라이저에서 다음이 적용됩니다.

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

새로운 모든 TelemetryClient는 사용자가 지정하는 속성 값을 자동으로 추가합니다. 개별 원격 분석 이벤트는 기본값을 재정의할 수 있습니다.

## <a name="next-steps"></a>다음 단계
   - [사용자, 세션, 이벤트](usage-segmentation.md)
   - [깔때기](usage-funnels.md)
   - [보존](usage-retention.md)
   - [사용자 흐름](usage-flows.md)
   - [통합 문서](../../azure-monitor/app/usage-workbooks.md)
   - [사용자 컨텍스트 추가](usage-send-user-context.md)
