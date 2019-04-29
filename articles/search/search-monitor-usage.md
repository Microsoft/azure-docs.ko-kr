---
title: 검색 서비스에 대한 리소스 사용량 및 메트릭 쿼리 모니터링 - Azure Search
description: Azure Search 서비스에서 로깅을 사용하도록 설정하고, 쿼리 작업 메트릭, 리소스 사용량 및 기타 시스템 데이터를 가져옵니다.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f4a0cba18f27c9cabfc03d1934469e6899c5cd18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564785"
---
# <a name="monitor-resource-consumption-and-query-activity-in-azure-search"></a>Azure Search에서 리소스 사용량 및 쿼리 작업 모니터링

Azure Search 서비스의 개요 페이지에서 리소스 사용량, 쿼리 메트릭 및 더 많은 인덱스, 인덱서 및 데이터 원본을 만드는 데 사용할 수 있는 할당량에 대한 시스템 데이터를 볼 수 있습니다. 또한 포털을 사용하여 로그 분석 또는 영구 데이터 수집에 사용되는 다른 리소스를 구성할 수도 있습니다. 

로그 설정은 자체 진단 및 작업 기록 유지에 유용합니다. 내부적으로 로그는 지원 티켓을 제출하는 경우 조사 및 분석에 충분한 정도의 짧은 시간 동안 백 엔드에 있습니다. 로그 정보를 제어하고 액세스할 수 있도록 하려면 이 문서에서 설명하는 솔루션 중 하나를 설정해야 합니다.

이 문서에서는 모니터링 옵션, 로깅과 로그 스토리지를 사용하도록 설정하는 방법 및 로그 콘텐츠를 보는 방법에 대해 알아봅니다.

## <a name="metrics-at-a-glance"></a>한눈에 메트릭 보기

개요 페이지에 기본 제공되는 **사용량** 및 **모니터링** 섹션에서는 리소스 사용량 및 쿼리 실행 메트릭을 보고합니다. 이 정보는 구성할 필요 없이 서비스 사용을 시작하는 즉시 사용할 수 있습니다. 이 페이지는 몇 분마다 새로 고쳐집니다. [프로덕션 워크로드에 사용할 계층](search-sku-tier.md) 또는 [활성 복제본 및 파티션의 수를 조정할지 여부](search-capacity-planning.md)를 최종적으로 결정하는 경우 이러한 메트릭은 리소스 사용 속도 및 현재 구성의 기존 부하 처리에 대한 효율성을 보여줌으로써 이러한 결정을 내리는 데 도움이 됩니다.

**사용량** 탭에는 현재 [제한](search-limits-quotas-capacity.md) 기준의 리소스 가용성이 표시됩니다. 다음 그림에서는 각 형식의 3개 개체와 50MB의 스토리지로 제한된 체험 서비스를 보여 줍니다. 기본 또는 표준 서비스의 제한이 더 높으며, 파티션 수를 늘리면 최대 스토리지 크기도 비례하여 증가합니다.

![유효 제한 대비 사용량](./media/search-monitor-usage/usage-tab.png
 "유효 제한 대비 사용량")

## <a name="queries-per-second-qps-and-other-metrics"></a>QPS(초당 쿼리 수) 및 기타 메트릭

**모니터링** 탭에는 검색 *QPS*(초당 쿼리 수)와 같이 분 단위로 집계된 메트릭에 대한 이동 평균이 표시됩니다. 
*검색 대기 시간*은 검색 서비스에서 검색 쿼리를 처리하는 데 필요한 분 단위로 집계된 시간입니다. *제한된 검색 쿼리 백분율*(표시되지 않음)은 제한된 검색 쿼리에 대해 분 단위로 집계된 백분율입니다.

이러한 숫자는 근사값이며, 시스템의 요청 처리 상태를 일반적으로 파악하기 위한 것입니다. 실제 QPS는 포털에 보고된 수보다 높거나 낮을 수 있습니다.

![초당 쿼리 수 활동](./media/search-monitor-usage/monitoring-tab.png "초당 쿼리 수 활동")

## <a name="activity-logs"></a>활동 로그

**활동 로그**는 Azure Resource Manager에서 정보를 수집합니다. 활동 로그에 있는 정보의 예로 서비스 만들기 또는 삭제, 리소스 그룹 업데이트, 이름 가용성 확인, 요청을 처리하기 위한 서비스 액세스 키 가져오기 등이 있습니다. 

**활성 로그**는 왼쪽 탐색 창, 위쪽 창 명령 모음의 알림 또는 **진단 및 문제 해결** 페이지에서 액세스할 수 있습니다.

인덱스 만들기 또는 데이터 원본 삭제와 같은 서비스 내 작업의 경우 각 요청에 대해 "관리자 키 가져오기"와 같은 일반 알림이 표시되지만 특정 작업 자체는 표시되지 않습니다. 이 수준의 정보를 얻으려면 추가 기능 모니터링 솔루션을 사용하도록 설정해야 합니다.

## <a name="add-on-monitoring-solutions"></a>추가 기능 모니터링 솔루션

Azure Search는 관리하는 개체 이외의 데이터를 저장하지 않으므로 로그 데이터는 외부에 저장해야 합니다. 로그 데이터를 유지하려는 경우 아래의 리소스를 구성할 수 있습니다. 

다음 표에서는 로그를 저장하고 Application Insights를 통해 서비스 작업 및 쿼리 워크로드에 대한 심층적인 모니터링을 추가하는 옵션을 비교합니다.

| 리소스 | 용도 |
|----------|----------|
| [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) | 기록된 된 이벤트 및 메트릭을 쿼리 아래 스키마를 기반으로 상관 관계가 지정 된 앱에서 사용자 이벤트와 함께 합니다. 이는 애플리케이션 코드를 통해 제출된 필터 요청과는 달리 사용자 작업 또는 신호를 고려하여 사용자 시작 검색의 이벤트를 매핑하는 유일한 솔루션입니다. 이 방법을 사용하려면 요청 정보를 Application Insights로 라우팅하는 계측 코드를 원본 파일에 복사하여 붙여넣습니다. 자세한 내용은 [트래픽 분석 검색](search-traffic-analytics.md)을 참조하세요. |
| [Azure Monitor 로그](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) | 기록된 된 이벤트 및 메트릭을 쿼리 아래 스키마를 기반으로 합니다. Log Analytics 작업 영역에 이벤트가 기록 됩니다. 작업 영역에 대해 쿼리를 실행하여 로그에서 자세한 정보를 반환할 수 있습니다. 자세한 내용은 참조 하세요. [Azure Monitor 로그로 시작](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 기록된 된 이벤트 및 메트릭을 쿼리 아래 스키마를 기반으로 합니다. 이벤트는 Blob 컨테이너에 기록되고 JSON 파일로 저장됩니다. JSON 편집기를 사용하여 파일 내용을 봅니다.|
| [이벤트 허브](https://docs.microsoft.com/azure/event-hubs/) | 기록된 이벤트 및 쿼리 메트릭은 이 문서에서 설명하는 스키마를 기반으로 합니다. 이 서비스는 매우 큰 로그에 대한 대체 데이터 수집 서비스로 선택합니다. |

Azure Monitor 로그와 Blob storage는 Azure 구독의의 수명 동안 무료로 사용해 보세요 수 있도록 무료 공유 서비스로 사용할 수 있습니다. 애플리케이션 데이터 크기가 특정 제한을 초과하지 않으면 Application Insights 평가판을 등록하여 사용할 수 있습니다(자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/monitor/) 참조).

다음 섹션에서는 Azure Blob 스토리지를 사용하도록 설정하고 이를 사용하여 Azure Search 작업에서 만든 로그 데이터를 수집하고 액세스하는 단계를 안내합니다.

## <a name="enable-logging"></a>로깅 사용

인덱싱 및 쿼리 작업에 대한 로깅은 기본적으로 해제되어 있으며, 로깅 인프라와 장기 외부 스토리지 모두에 대한 추가 기능 솔루션에 따라 달라집니다. Azure Search에서 만들고 관리하는 개체는 자체적으로 유지되는 유일한 데이터이므로 로그는 다른 위치에 저장해야 합니다.

이 섹션에서는 Blob 스토리지를 사용하여 기록된 이벤트와 메트릭 데이터를 저장하는 방법에 대해 알아봅니다.

1. 스토리지 계정이 아직 없는 경우 [스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 이 연습에 사용된 모든 리소스를 삭제하려면 나중에 정리를 간소화하기 위해 Azure Search와 동일한 리소스 그룹에 배치할 수 있습니다.

2. 검색 서비스의 [개요] 페이지를 엽니다. 왼쪽 탐색 창에서 **모니터링**까지 아래로 스크롤하고 **모니터 사용**을 클릭합니다.

   ![모니터링 사용](./media/search-monitor-usage/enable-monitoring.png "모니터링 사용")

3. 내보낼 데이터를 선택합니다(로그, 메트릭 또는 둘 다). 저장소 계정에 복사 하, 이벤트 허브로 전송 하거나 Azure Monitor 로그로 내보낼 수 있습니다.

   Blob 스토리지에 보관하려면 스토리지 계정만 있어야 합니다. 로그 데이터를 내보낼 때 컨테이너와 Blob이 만들어집니다.

   ![Blob 스토리지 보관 구성](./media/search-monitor-usage/configure-blob-storage-archive.png "Blob 스토리지 보관 구성")

4. 프로필을 저장합니다.

5. 개체를 만들거나 삭제하고(로그 이벤트 만들기) 쿼리를 제출(메트릭 생성)하여 로깅을 테스트합니다. 

프로필이 저장되면 로깅을 사용하도록 설정됩니다. 기록하거나 측정하는 활동이 있는 경우에만 컨테이너가 만들어집니다. 데이터가 스토리지 계정에 복사되면 해당 데이터가 JSON 형식으로 다음 두 컨테이너에 배치됩니다.

* insights-logs-operationlogs: 검색 트래픽 로그인 경우
* insights-metrics-pt1m: 메트릭인 경우

**걸리는 시간이 1 시간 전에 컨테이너는 Blob 저장소에 표시 됩니다. 시간당 컨테이너의 blob 하나 있습니다.**

[Visual Studio Code](#download-and-open-in-visual-studio-code) 또는 다른 JSON 편집기를 사용하여 파일을 볼 수 있습니다. 

### <a name="example-path"></a>예제 경로

```
resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2018/m=12/d=25/h=01/m=00/name=PT1H.json
```

## <a name="log-schema"></a>로그 스키마
검색 서비스 트래픽 로그가 포함된 Blob은 이 섹션에서 설명한 대로 구성됩니다. 각 Blob에는 로그 개체의 배열이 포함된 **records**라는 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

| 이름 | Type | 예 | 메모 |
| --- | --- | --- | --- |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |작업 타임스탬프 |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId |
| operationName |문자열 |"Query.Search" |작업 이름 |
| operationVersion |문자열 |"2017-11-11" |사용된 api-version |
| category |문자열 |"OperationLogs" |constant |
| resultType |문자열 |"Success" |가능한 값은 다음과 같습니다. Success 또는 Failure |
| resultSignature |int |200 |HTTP 결과 코드 |
| durationMS |int |50 |밀리초 단위의 작업 기간 |
| properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체 |

**속성 스키마**

| 이름 | Type | 예 | 메모 |
| --- | --- | --- | --- |
| 설명 |문자열 |"GET /indexes('content')/docs" |작업의 엔드포인트 |
| 쿼리 |문자열 |"?search=AzureSearch&$count=true&api-version=2017-11-11" |쿼리 매개 변수 |
| 문서 |int |42 |처리된 문서 수 |
| IndexName |문자열 |"testindex" |작업과 연결된 인덱스의 이름 |

## <a name="metrics-schema"></a>메트릭 스키마

쿼리 요청에 대한 메트릭이 캡처됩니다.

| 이름 | Type | 예 | 메모 |
| --- | --- | --- | --- |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |리소스 ID |
| metricName |문자열 |"Latency" |메트릭 이름 |
| 실시간 |Datetime |"2018-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
| average |int |64 |메트릭 시간 간격에 원시 샘플의 평균 값 |
| minimum |int |37 |메트릭 시간 간격에 원시 샘플의 최소 값 |
| maximum |int |78 |메트릭 시간 간격에 원시 샘플의 최대 값 |
| total |int |258 |메트릭 시간 간격에 원시 샘플의 총 값 |
| count |int |4 |메트릭을 생성하는 데 사용되는 원시 샘플 수 |
| timegrain |문자열 |"PT1M" |ISO 8601에서 메트릭의 시간 조직 |

모든 메트릭은 1 분 간격으로 보고됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다.

SearchQueriesPerSecond 메트릭의 경우, 최소값은 해당 분 동안 등록된 초당 검색 쿼리 수에 대한 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다.
1분 동안 이 시나리오에 관한 생각: SearchQueriesPerSecond에 대한 최대값 만큼 부하가 매우 높은 1초가 지난 후 58초 동안 중간 부하 상태가 계속되다가 마지막으로 최소값인 쿼리 한 개만 있는 1초가 이어집니다.

ThrottledSearchQueriesPercentage의 경우, 최소값, 최대값, 평균 및 합계가 모두 같은 값이며, 이 값은 1분 동안 총 검색 쿼리 수에서 제한된 검색 쿼리의 비율입니다.

## <a name="download-and-open-in-visual-studio-code"></a>Visual Studio 코드에서 다운로드 및 열기

JSON 편집기를 사용하여 로그 파일을 볼 수 있습니다. 없는 경우 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하는 것이 좋습니다.

1. Azure Portal에서 Storage 계정을 엽니다. 

2. 왼쪽 탐색 창에서 **Blob**을 클릭합니다. **sights-logs-operationlogs** 및 **inights-metrics-pt1m**이 표시됩니다. 이러한 컨테이너는 로그 데이터를 Blob 스토리지로 내보낼 때 Azure Search에서 만들어집니다.

3. .json 파일에 도달할 때까지 폴더 계층 구조를 따라 아래로 클릭합니다.  상황에 맞는 메뉴를 사용하여 파일을 다운로드합니다.

파일이 다운로드되면 JSON 편집기에서 파일을 열어 내용을 봅니다.

## <a name="use-system-apis"></a>시스템 API 사용
Azure Search REST API와 .NET SDK는 둘 다 서비스 메트릭, 인덱스와 인덱서 정보 및 문서 수에 대한 액세스를 프로그래밍 방식으로 제공합니다.

* [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)
* [인덱스 통계 가져오기](/rest/api/searchservice/get-index-statistics)
* [문서 수 계산](/rest/api/searchservice/count-documents)
* [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)

PowerShell 또는 Azure CLI를 사용하려면 [여기](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

서비스 관리에 대한 자세한 내용은 [Microsoft Azure에서 Search 서비스 관리](search-manage.md)를 참조하고, 튜닝 지침은 [성능 및 최적화](search-performance-optimization.md)를 참조하세요.