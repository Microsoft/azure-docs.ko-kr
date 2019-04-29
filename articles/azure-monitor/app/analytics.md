---
title: Analytics - Azure Application Insights의 강력한 검색 및 쿼리 도구 | Microsoft Docs
description: 'Application Insights의 강력한 진단 검색 도구인 분석의 개요입니다. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/02/2019
ms.author: mbullwin
ms.openlocfilehash: 4c3ecdd01106cc8d305764206bc75535fa4dac3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691952"
---
# <a name="analytics-in-application-insights"></a>Application Insights의 분석
Analytics는 [Application Insights](app-insights-overview.md)의 강력한 검색 및 쿼리 도구입니다. Analytics는 웹 도구이므로 설치가 필요하지 않습니다.
앱 중 하나에 대해 Application Insights를 이미 구성한 경우 앱의 [개요 블레이드](app-insights-dashboards.md)에서 Analytics를 열어 앱의 데이터를 분석할 수 있습니다.

![portal.azure.com에서 Application Insights 리소스를 열고 분석을 클릭합니다.](./media/analytics/001.png)

여러 샘플 데이터가 있는 무료 데모 환경인 [Analytics 실습](https://go.microsoft.com/fwlink/?linkid=859557)을 사용할 수도 있습니다.
<br>
<br>
> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 

## <a name="relation-to-azure-monitor-logs"></a>Azure Monitor 로그와의 관계
Application Insights 분석은 Azure Monitor 로그와 같은 [Azure Data Explorer](/azure/data-explorer)를 기반으로 하며 [Kusto 쿼리 언어](/azure/kusto/query)도 사용합니다. Azure Monitor 로그와 동일한 [로그 분석 포털](../log-query/get-started-portal.md)을 사용하지만 데이터는 별도의 파티션에 저장됩니다.

Application Insights 분석에서 Log Analytics 작업 영역의 데이터에 직접 액세스하거나 로그 분석에서 애플리케이션 데이터에 직접 액세스할 수는 없습니다. 두 데이터 세트를 함께 쿼리하려면 [로그 분석에서 쿼리](../log-query/log-query-overview.md)를 작성하고 [app() 식](../log-query/app-expression.md)을 사용하여 애플리케이션 데이터에 액세스합니다.


## <a name="query-data-in-analytics"></a>Analytics의 쿼리 데이터
일반적인 쿼리는 테이블 이름으로 시작하며 그 뒤에 `|`로 구분된 일련의 *연산자*가 있습니다.
예를 들어, 지난 3시간 동안 앱이 다른 국가에서 받은 요청의 개수를 살펴보겠습니다.
```AIQL
requests
| where timestamp > ago(3h)
| summarize count() by client_CountryOrRegion
| render piechart
```

테이블 이름 *requests*로 시작하고 필요에 따라 파이프된 요소를 추가합니다.  먼저 시간 필터를 정의하여 지난 3시간의 레코드를 검토합니다.
그런 다음 국가당 레코드 수를 계산합니다(열 *client_CountryOrRegion*에서 해당 데이터를 찾을 수 있음). 마지막으로 원형 차트에서 결과를 렌더링합니다.
<br>

![쿼리 결과](./media/analytics/030.png)

언어에 다음과 같은 많은 유용한 기능이 있습니다.

* [필터링](/azure/kusto/query/whereoperator) 합니다.
* 여러 테이블 [조인](/azure/kusto/query/joinoperator) - 요청을 페이지 뷰, 종속성 호출, 예외 및 로그 추적과 상호 연결합니다.
* 강력한 통계 [집계](/azure/kusto/query/summarizeoperator)기능이 있습니다.
* 즉각적이고 강력하게 시각화합니다.
* 예를 들어 PowerShell에서 프로그래밍 방식으로 쿼리를 실행하는 데 사용할 수 있는 [REST API](https://dev.applicationinsights.io/)가 있습니다.

[전체 언어 참조](https://go.microsoft.com/fwlink/?linkid=856079)는 지원되는 모든 명령을 자세히 설명하고 정기적으로 업데이트합니다.

## <a name="next-steps"></a>다음 단계
* [Analytics 포털](https://go.microsoft.com/fwlink/?linkid=856587) 시작
* [쿼리 작성](https://go.microsoft.com/fwlink/?linkid=856078) 시작
* 가장 일반적인 관용구의 번역에 대한 [SQL 사용자 치트 시트](https://aka.ms/sql-analytics)를 검토합니다.
* 앱이 아직 데이터를 Application Insights로 전송하지 않은 경우, [실습](https://analytics.applicationinsights.io/demo)에 대한 Analytics를 테스트합니다.
* [소개 비디오](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)를 봅니다.
