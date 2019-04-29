---
title: Azure Application Insights의 종속성 추적 | Microsoft Docs
description: 사용량, 가용성 및 온-프레미스 또는 Application Insights를 사용 하 여 Microsoft Azure 웹 응용 프로그램의 성능을 분석 합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbullwin
ms.openlocfilehash: c77b5810164aef7508f717a0f75d90cf6cba2089
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691384"
---
# <a name="set-up-application-insights-dependency-tracking"></a>Application Insights 설정: 종속성 추적
*종속성*은 앱에서 호출하는 외부 구성 요소로, 일반적으로 HTTP, 데이터베이스 또는 파일 시스템을 사용하여 호출되는 서비스입니다. [Application Insights](../../azure-monitor/app/app-insights-overview.md)는 애플리케이션이 종속성을 기다리는 시간과 종속성 호출에 실패하는 빈도를 측정합니다. 특정 호출을 조사하여 요청 및 예외와 연관지을 수 있습니다.

기본적으로 종속성 모니터는 현재 다음 유형의 종속성에 대한 호출을 보고합니다.

* 서버
  * SQL 데이터베이스
  * ASP.NET 웹 및 HTTP 기반 바인딩을 사용하는 WCF 서비스
  * 로컬 또는 원격 HTTP 호출
  * Azure Cosmos DB, 테이블, Blob Storage 및 큐 
* 웹 페이지
  * AJAX 호출

모니터링은 select 메서드 중심의 [바이트 코드 계측](https://msdn.microsoft.com/library/z9z62c29.aspx)을 사용하거나 .NET Framework의 DiagnosticSource 콜백(최신 .NET SDK)을 기준으로 작동합니다. 성능 오버 헤드가 최소화됩니다.

[TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)를 사용하여 클라이언트 및 서버 코드 모두에서 다른 종속성을 모니터링하는 사용자 고유의 SDK 호출을 작성할 수도 있습니다.

> [!NOTE]
> Azure Cosmos DB는 [HTTP/HTTPS](../../cosmos-db/performance-tips.md#networking)를 사용하는 경우에만 자동으로 추적됩니다. Application Insights에서는 TCP 모드가 캡처되지 않습니다.

## <a name="set-up-dependency-monitoring"></a>종속성 모니터링 설정
부분 종속성 정보는 [Application Insights SDK](asp-net.md)에서 자동으로 수집됩니다. 전체 데이터를 가져오려면 호스트 서버에 대한 적절한 에이전트를 설치합니다.

| 플랫폼 | 설치 |
| --- | --- |
| IIS 서버 |[상태 모니터를 서버에 설치](../../azure-monitor/app/monitor-performance-live-website-now.md) 또는 [애플리케이션을 .NET Framework 4.6 이상으로 업그레이드](https://go.microsoft.com/fwlink/?LinkId=528259)하고 앱에 [Application Insights SDK](asp-net.md)를 설치합니다. |
| Azure 웹앱 |웹앱 제어판에서 [Application Insights 블레이드를 열고](../../azure-monitor/app/azure-web-apps.md) 메시지가 표시되면 설치를 선택합니다. |
| Azure 클라우드 서비스 |[시작 작업 사용](../../azure-monitor/app/cloudservices.md) 또는 [.NET Framework 4.6+ 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md) |

## <a name="where-to-find-dependency-data"></a>종속성 데이터를 찾을 수 있는 위치
* [애플리케이션 맵](#application-map)은 앱과 인접 구성 요소 간의 종속성을 시각화합니다.
* [성능, 브라우저 및 실패 블레이드](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-performance): 서버 종속성 데이터를 표시합니다.
* [브라우저 블레이드](#ajax-calls): 사용자 브라우저에서의 AJAX 호출을 표시합니다.
* 느리거나 실패한 요청 클릭: 해당 종속성 호출을 확인합니다.
* [분석](#analytics): 종속성 데이터를 쿼리하는 데 사용됩니다.

## <a name="application-map"></a>애플리케이션 맵
애플리케이션 맵은 애플리케이션의 구성 요소 간에 종속성을 검색하는 시각화 보조 도구의 역할을 합니다. 앱의 원격 분석에서 자동으로 생성됩니다. 이 예제에서는 두 외부 서비스에 대한 브라우저 스크립트에서의 AJAX 호출과 서버 앱에서의 REST 호출을 보여 줍니다.

![애플리케이션 맵](./media/asp-net-dependencies/cloud-rolename.png)

* **상자에서 관련 종속성 및 기타 차트로 이동**합니다.
* [대시보드](../../azure-monitor/app/app-insights-dashboards.md)에 **맵을 고정**하면 완벽하게 작동될 수 있습니다.

[자세히 알아보세요](../../azure-monitor/app/app-map.md)을 확인하세요.

## <a name="performance-and-failure-blades"></a>성능 및 실패 블레이드
성능 블레이드에는 서버 앱에 실행한 종속성 호출 기간이 표시됩니다.

**실패 횟수**는 **실패** 블레이드에 표시됩니다. 실패는 200~399 범위에 없거나 알 수 없는 반환 코드입니다.

> [!NOTE]
> **100% 실패?** - 이는 부분 종속성 데이터만 가져옴을 의미할 수 있습니다. [플랫폼에 적절한 종속성 모니터링을 설정](#set-up-dependency-monitoring)해야 합니다.
>
>

## <a name="ajax-calls"></a>AJAX 호출
브라우저 블레이드에는 [웹 페이지의 JavaScript](../../azure-monitor/app/javascript.md)에서 실행한 AJAX 호출의 기간 및 실패율이 표시됩니다. 이는 종속성으로 표시됩니다.

## <a name="diagnosis"></a> 느린 요청 진단
각 요청 이벤트는 종속성 호출, 예외 및 앱은 요청을 처리 하는 동안 추적 되는 다른 이벤트와 연결 합니다. 따라서 일부 요청이 잘못 수행되는 경우 종속성의 느린 응답 때문인지 여부를 확인할 수 있습니다.

### <a name="profile-your-live-site"></a>라이브 사이트 프로파일링

시간에 따른 위치를 알 수 없나요? 합니다 [Application Insights profiler](../../azure-monitor/app/profiler.md) 추적 HTTP 라이브 사이트에 대 한 호출 및 함수 코드에서 가장 오래 걸린 보여 줍니다.

## <a name="analytics"></a>분석
[Kusto 쿼리 언어](/azure/kusto/query/)에서 종속성을 추적할 수 있습니다. 다음은 몇 가지 예제입니다.

* 실패한 종속성 호출을 찾습니다.

```

    dependencies | where success != "True" | take 10
```

* AJAX 호출을 찾습니다.

```

    dependencies | where client_Type == "Browser" | take 10
```

* 요청과 연관된 종속성 호출을 찾습니다.

```

    dependencies
    | where timestamp > ago(1d) and  client_Type != "Browser"
    | join (requests | where timestamp > ago(1d))
      on operation_Id  
```


* 페이지 보기와 관련된 AJAX 호출을 찾습니다.

```

    dependencies
    | where timestamp > ago(1d) and  client_Type == "Browser"
    | join (browserTimings | where timestamp > ago(1d))
      on operation_Id
```



## <a name="custom-dependency-tracking"></a>사용자 지정 종속성 추적
표준 종속성 추적 모듈은 데이터베이스 및 REST API와 같은 외부 종속성을 자동으로 검색합니다. 하지만 일부 추가 구성 요소를 동일한 방식으로 취급할 수도 있습니다.

표준 모듈에 의해 사용되는 동일한 [TrackDependency API](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency) 를 사용하여 종속성 정보를 보내는 코드를 작성할 수 있습니다.

예를 들면, 사용자가 직접 작성하지 않은 어셈블리 코드를 작성하는 경우, 응답 시간 기여도를 알아보기 위해 모든 호출의 시간을 잴 수 있습니다. Application Insights에서 종속성 차트에 표시되는 이 데이터를 가지려면, `TrackDependency`을 사용하여 이것을 보냅니다.

```csharp

            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
                // The call above has been made obsolete in the latest SDK. The updated call follows this format:
                // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
            }
```

표준 종속성 추적 모듈을 해제하려는 경우, [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md)에서 DependencyTrackingTelemetryModule에 대한 참조를 삭제합니다.

## <a name="troubleshooting"></a>문제 해결
*종속성 성공 플래그는 항상 true 또는 false로 표시됩니다.*

*SQL 쿼리가 일부만 표시됩니다.*

애플리케이션에 대한 종속성 모니터링을 사용하도록 설정하려면 올바른 구성을 선택했는지 확인하고 아래 표를 참조하십시오.

| 플랫폼 | 설치 |
| --- | --- |
| IIS 서버 |[서버에 상태 모니터 설치](../../azure-monitor/app/monitor-performance-live-website-now.md)하거나 [애플리케이션을 .NET Framework 4.6 이상으로 업그레이드](https://go.microsoft.com/fwlink/?LinkId=528259)하고 앱에 [Application Insights SDK](asp-net.md)를 설치합니다. |
| IIS Express |대신 IIS 서버를 사용합니다. |
| Azure 웹앱 |웹앱 제어판에서 [Application Insights 블레이드를 열고](../../azure-monitor/app/azure-web-apps.md) 메시지가 표시되면 설치를 선택합니다. |
| Azure 클라우드 서비스 |[시작 작업을 사용](../../azure-monitor/app/cloudservices.md)하거나 [.NET Framework 4.6 이상을 설치](../../cloud-services/cloud-services-dotnet-install-dotnet.md)합니다. |

## <a name="next-steps"></a>다음 단계
* [예외](../../azure-monitor/app/asp-net-exceptions.md)
* [사용자 및 페이지 데이터](../../azure-monitor/app/javascript.md)
* [Availability](../../azure-monitor/app/monitor-web-app-availability.md)
