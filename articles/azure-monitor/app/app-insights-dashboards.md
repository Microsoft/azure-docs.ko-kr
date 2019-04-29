---
title: Azure Application Insights의 대시보드 및 탐색 | Microsoft Docs
description: 키 APM 차트 및 쿼리의 뷰를 만듭니다.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 39b0701b-2fec-4683-842a-8a19424f67bd
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: afbf2bc32aa737eb5f6dde41035b206d6e260252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60767561"
---
# <a name="navigation-and-dashboards-in-the-application-insights-portal"></a>Application Insights 포털에서 탐색 및 대시보드
[프로젝트에서 Application Insights를 설정](../../azure-monitor/app/app-insights-overview.md)하면 앱의 성능 및 사용에 대한 원격 분석 데이터가 [Azure Portal](https://portal.azure.com)에서 프로젝트의 Application Insights 리소스에 나타납니다.

## <a name="find-your-telemetry"></a>원격 분석 찾기
[Azure Portal](https://portal.azure.com)에 로그인하고 앱에 대해 만든 Application Insights 리소스를 찾습니다.

![찾아보기를 클릭하고 Application Insights를 선택한 후 앱을 선택합니다.](./media/app-insights-dashboards/00-start.png)

앱의 개요 블레이드(페이지)는 앱의 주요 진단 메트릭 요약을 보여 주며 포털의 다른 기능에 대한 게이트웨이입니다.

![원격 분석을 보기 위한 주요 경로](./media/app-insights-dashboards/010-oview.png)

차트 및 표를 사용자 지정하고 이를 대시보드에 고정할 수 있습니다. 이런 방식으로 중앙 대시보드에 있는 여러 다른 앱의 주요 원격 분석을 함께 불러올 수 있습니다.

## <a name="dashboards"></a>대시보드
[Microsoft Azure 포털](https://portal.azure.com) 에 로그인한 후 가장 먼저 표시되는 것이 대시보드입니다. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)의 원격 분석을 포함하여, 모든 Azure 리소스에서 가장 중요한 차트를 이곳에 한데 모을 수 있습니다.

![사용자 지정 대시보드입니다.](./media/app-insights-dashboards/31.png)

1. Application Insights의 앱과 같이 **특정 리소스로 이동**합니다. 왼쪽 막대를 사용합니다.
2. **현재 대시보드도 돌아가거나** 최근의 다른 뷰로 전환합니다. 왼쪽 위의 드롭다운 메뉴를 사용합니다.
3. **대시보드 전환**: 대시보드 제목에 있는 드롭다운 메뉴를 사용합니다.
4. 대시보드 도구 모음을 사용하여 **대시보드를 만들고, 편집하고, 공유**합니다.
5. **대시보드 편집**: 타일을 포인터로 가리킨 다음 그 위쪽 막대를 사용하여 타일을 이동, 사용자 지정, 또는 제거합니다.

## <a name="add-to-a-dashboard"></a>대시보드 추가
특히 흥미로운 블레이드나 차트를 보는 경우 그 사본을 대시보드에 고정할 수 있습니다. 이 경우 다음에 대시보드로 돌아가면 해당 블레이드나 차트가 표시됩니다.

![차트를 고정하려면 차트 위에 마우스를 놓고 헤더에서 "..."를 클릭합니다.](./media/app-insights-dashboards/33.png)

1. 대시보드에 차트를 고정합니다. 차트의 복사본이 대시보드에 나타납니다.
2. 전체 블레이드를 대시보드에 고정합니다 - 대시보드에서 클릭할 수 있는 타일로 나타납니다.
3. 현재 대시보드 돌아가려면 왼쪽 위의 모퉁이를 클릭합니다. 그런 다음 현재 보기로 돌아가려면 드롭다운 메뉴를 사용할 수 있습니다.

차트는 타일로 그룹화되고 타일은 둘 이상의 차트를 포함할 수 있습니다. 타일 전체를 대시보드에 고정합니다.

차트는 차트의 시간 범위에 따라 달라지는 빈도로 자동으로 새로 고쳐집니다.

* 시간 범위 최대 1시간: 5분마다 새로 고침
* 시간 범위 1-24시간: 15분마다 새로 고침
* 시간 범위 24시간 초과: (시간 범위)/60

### <a name="pin-any-query-in-analytics"></a>분석에서 모든 쿼리를 고정
공유 대시보드에 [분석 차트를 고정](../../azure-monitor/log-query/get-started-portal.md)할 수도 있습니다. 그러면 임의 쿼리의 차트를 표준 메트릭과 함께 추가할 수 있습니다. 

결과는 1시간마다 자동으로 다시 계산됩니다. 즉시 다시 계산하려면 새로 고침 아이콘을 클릭합니다. 브라우저 새로 고침은 다시 계산하지 않습니다.

## <a name="adjust-a-tile-on-the-dashboard"></a>대시보드에서 타일을 조정합니다.
타일이 대시보드에 있으면, 조정할 수 있습니다.

![차트를 편집하려면 차트를 마우스로 가리킵니다.](./media/app-insights-dashboards/36.png)

1. 타일에 차트 추가
2. 메트릭, group-by 차원 및 차트의 스타일(테이블, 그래프)를 설정합니다.
3. 끌어서 다이어그램을 확대합니다. timespan을 다시 설정하려면 실행 취소 단추를 클릭합니다. 타일에서 차트에 대한 필터 속성을 설정합니다.
4. 타일 제목을 설정합니다.

메트릭 탐색기 블레이드에서 고정된 타일에는 개요 블레이드에서 고정된 타일보다 편집 옵션이 많이 있습니다.

고정해 놓은 원래 타일은 편집의 영향을 받지 않습니다.

## <a name="switch-between-dashboards"></a>대시보드 사이 전환
둘 이상의 대시보드를 저장하고 해당 대시보드 간에 전환할 수 있습니다. 차트나 블레이드를 고정하면 현재 대시보드에 추가됩니다.

![대시보드 간에 전환하려면 대시보드를 클릭하고 저장된 대시보드를 선택합니다. 새 대시보드를 만들고 저장하려면 새로 만들기를 클릭합니다. 다시 정렬하려면 편집을 클릭합니다.](./media/app-insights-dashboards/32.png)

예를 들어 단체방에서 전체 화면을 표시하는 대시보드와 일반 개발용 대시보드를 유지할 수 있습니다.

대시보드에서 블레이드는 타일로 표시됩니다. 이를 클릭하면 블레이드로 이동합니다. 차트는 원래 위치에 있는 차트를 복제합니다.

![타일이 나타내는 블레이드를 열려면 타일을 클릭합니다.](./media/app-insights-dashboards/35.png)

## <a name="share-dashboards"></a>대시보드 공유
대시보드를 만들면 다른 사용자와 공유할 수 있습니다.

![대시보드 헤더에서 공유를 클릭합니다.](./media/app-insights-dashboards/41.png)

[역할 및 액세스 제어](../../azure-monitor/app/resources-roles-access-control.md)에 대해 알아보세요.

## <a name="create-dashboards-programmatically"></a>대시보드를 프로그래밍 방식으로 만들기
[Azure Resource Manager](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-create-programmatically) 및 간단한 JSON 편집기를 사용하여 대시보드 생성을 자동화할 수 있습니다.

## <a name="app-navigation"></a>앱 탐색
개요 블레이드는 앱에 대한 자세한 정보를 제공하는 게이트웨이입니다.

* **모든 차트 또는 타일** - 원하는 타일 또는 차트를 클릭하여 표시되는 내용에 대한 자세한 정보를 확인합니다.

### <a name="overview-blade-buttons"></a>개요 블레이드 단추
![개요 블레이드 위쪽 탐색 모음](./media/app-insights-dashboards/app-overview-top-nav.png)

* [**메트릭 탐색기**](../../azure-monitor/app/metrics-explorer.md) - 성능 및 사용에 대한 사용자 고유의 차트를 만듭니다.
* [**검색**](../../azure-monitor/app/diagnostic-search.md) - 요청, 예외 또는 로그 추적과 같은 이벤트의 특정 인스턴스를 조사할 수 있습니다.
* [**분석**](../../azure-monitor/app/analytics.md) - 원격 분석을 통해 강력한 쿼리를 합니다.
* **시간 범위** - 블레이드의 모든 차트에서 표시되는 범위를 조정합니다.
* **삭제** - 앱에 대한 Application Insights 리소스를 삭제합니다. 또한 앱 코드에서 Application Insights 패키지를 제거하거나 앱에서 [계측 키](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key)를 편집하여 다른 Application Insights 리소스에 원격 분석을 지시합니다.

### <a name="essentials-tab"></a>Essentials 탭
* [계측 키](../../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) - 이 앱 리소스를 식별합니다.

### <a name="app-navigation-bar"></a>앱 탐색 모음
![왼쪽 탐색 모음](./media/app-insights-dashboards/app-left-nav-bar.png)

* **개요** - 앱 개요 블레이드로 돌아갑니다.
* **활동 로그** - 경고 및 Azure 관리 이벤트입니다.
* [**액세스 제어**](../../azure-monitor/app/resources-roles-access-control.md) - 팀 멤버 및 다른 사용자에 대한 액세스를 제공합니다.
* [**태그**](../../azure-resource-manager/resource-group-using-tags.md) - 태그를 사용하여 다른 사용자와 앱을 그룹화합니다.

조사

* [**애플리케이션 맵**](app-map.md) - 종속성 정보에서 파생된 애플리케이션의 구성 요소를 표시하는 활성 맵입니다.
* [**스마트 감지**](../../azure-monitor/app/proactive-diagnostics.md) - 최근 성능 경고를 검토합니다.
* [**라이브 스트림**](../../azure-monitor/app/live-stream.md) - 거의 즉각적인 고정된 메트릭 집합을 제공하며, 새 빌드를 배포 또는 디버깅할 때 유용합니다.
* [**가용성/웹 테스트**](../../azure-monitor/app/monitor-web-app-availability.md) - 전세계에서 웹앱에 일반 요청을 전송합니다.*
* [**오류, 성능**](../../azure-monitor/app/web-monitor-performance.md) - 사용자의 앱에 대한 요청 및 사용자 앱의 [종속성](../../azure-monitor/app/asp-net-dependencies.md)에 대한 요청의 예외, 실패율 및 응답 시간입니다.
* [**성능**](../../azure-monitor/app/web-monitor-performance.md) - 응답 시간, 종속성 응답 시간입니다.
* [서버](../../azure-monitor/app/web-monitor-performance.md) - 성능 카운터입니다. [상태 모니터를 설치하면](../../azure-monitor/app/monitor-performance-live-website-now.md)사용할 수 있습니다.
* **브라우저** - 페이지 뷰 및 AJAX 성능입니다. [웹 페이지를 계측할 때](../../azure-monitor/app/javascript.md)사용할 수 있습니다.
* **사용** - 페이지 조회수, 사용자 및 세션 수입니다. [웹 페이지를 계측할 때](../../azure-monitor/app/javascript.md)사용할 수 있습니다.

구성

* **시작하기** - 인라인 자습서입니다.
* **속성** - 계측 키, 구독 및 리소스 ID입니다.
* [경고](../../azure-monitor/app/alerts.md) -메트릭 경고 구성입니다.
* [연속 내보내기](../../azure-monitor/app/export-telemetry.md) - Azure Storage에 대한 원격 분석 내보내기를 구성합니다.
* [성능 테스트](../../azure-monitor/app/monitor-web-app-availability.md#performance-tests) -웹 사이트에 대한 종합 부하를 설정 합니다.
* [할당량 및 가격 책정](../../azure-monitor/app/pricing.md)과 [수집 샘플링](../../azure-monitor/app/sampling.md)입니다.
* **API 액세스** - [릴리스 주석](annotations.md)을 작성하고 데이터 액세스 API에 사용됩니다.
* [**작업 항목**](../../azure-monitor/app/diagnostic-search.md#create-work-item) - 작업 추적 시스템과 연결하여 원격 분석을 검사하는 동안 버그를 만들 수 있습니다.

설정

* [**잠금**](../../azure-resource-manager/resource-group-lock-resources.md) - Azure 리소스를 잠급니다.
* [**스크립트 Automation**](../../azure-monitor/app/powershell.md) - Azure 리소스의 정의를 내보내서 새로운 리소스의 템플릿으로 사용합니다.


## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>다음 단계

|  |  |
| --- | --- |
| [메트릭 탐색기](../../azure-monitor/app/metrics-explorer.md)<br/>필터 및 세그먼트 메트릭 |![검색 예제](./media/app-insights-dashboards/64.png) |
| [진단 검색](../../azure-monitor/app/diagnostic-search.md)<br/>이벤트 찾기 및 검사, 관련 이벤트, 버그 만들기 |![검색 예제](./media/app-insights-dashboards/61.png) |
| [분석](../../azure-monitor/app/analytics.md)<br/>강력한 쿼리 언어 |![검색 예제](./media/app-insights-dashboards/63.png) |
