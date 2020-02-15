---
title: 로그 데이터 수집
titleSuffix: Azure Cognitive Search
description: 진단 설정을 사용 하도록 설정 하 여 로그 데이터를 수집 하 고 분석 한 다음, Kusto 쿼리 언어를 사용 하 여 결과를 탐색 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211179"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 로그 데이터 수집 및 분석

진단 또는 운영 로그는 Azure Cognitive Search의 자세한 작업에 대 한 통찰력을 제공 하 고 서비스 및 작업 프로세스를 모니터링 하는 데 유용 합니다. 내부적으로 로그는 지원 티켓을 제출하는 경우 조사 및 분석에 충분한 정도의 짧은 시간 동안 백 엔드에 있습니다. 그러나 운영 데이터에 대 한 자체 방향이 필요한 경우 로깅 정보를 수집할 위치를 지정 하는 진단 설정을 구성 해야 합니다. 

로그 설정은 진단 및 운영 기록을 유지 하는 데 유용 합니다. 로깅을 사용 하도록 설정한 후에는 쿼리를 실행 하거나 구조화 된 분석을 위해 보고서를 작성할 수 있습니다.

다음 표에서는 데이터를 수집 하 고 유지 하기 위한 옵션을 열거 합니다.

| 리소스 | 사용 대상 |
|----------|----------|
| [Log Analytics 작업 영역으로 보내기](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 아래 스키마에 따라 기록 된 이벤트 및 쿼리 메트릭입니다. 이벤트는 Log Analytics 작업 영역에 기록 됩니다. Log Analytics를 사용 하 여 쿼리를 실행 하 여 자세한 정보를 반환할 수 있습니다. 자세한 내용은 [Azure Monitor 로그 시작](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) 을 참조 하세요. |
| [Blob storage를 사용 하 여 보관](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 아래 스키마에 따라 기록 된 이벤트 및 쿼리 메트릭입니다. 이벤트는 Blob 컨테이너에 기록되고 JSON 파일로 저장됩니다. 로그는 특정 인시던트를 조사 하는 데 유용 하지만 열기 종료 조사에는 유용 하지 않을 수 있습니다 (시간/분). JSON 편집기를 사용 하 여 로그 파일을 볼 수 있습니다.|
| [이벤트 허브로 스트림](https://docs.microsoft.com/azure/event-hubs/) | 기록된 이벤트 및 쿼리 메트릭은 이 문서에서 설명하는 스키마를 기반으로 합니다. 이 서비스는 매우 큰 로그에 대한 대체 데이터 수집 서비스로 선택합니다. |

Azure Monitor 로그와 Blob 저장소는 모두 무료 서비스로 제공 되므로 Azure 구독의 수명 동안 무료로 사용해 볼 수 있습니다. 애플리케이션 데이터 크기가 특정 제한을 초과하지 않으면 Application Insights 평가판을 등록하여 사용할 수 있습니다(자세한 내용은 [가격 페이지](https://azure.microsoft.com/pricing/details/monitor/) 참조).

## <a name="prerequisites"></a>사전 요구 사항

Log Analytics 또는 Azure Storage를 사용 하는 경우 리소스를 미리 만들 수 있습니다.

+ [Log analytics 작업 영역 만들기](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ 로그 보관이 필요한 경우 [저장소 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) .

## <a name="create-a-log"></a>로그 만들기

진단 설정에서 데이터 컬렉션을 정의 합니다. 설정은 및 수집 되는 방법을 지정 합니다. 

1. **모니터링** 아래에서 **진단 설정**을 선택합니다.

   ![진단 설정](./media/search-monitor-usage/diagnostic-settings.png "진단 설정")

1. **+ 진단 설정 추가** 를 선택 합니다.

1. 내보낼 데이터를 선택합니다(로그, 메트릭 또는 둘 다). 저장소 계정 또는 log analytics 작업 영역에서 데이터를 수집 하거나 이벤트 허브로 스트리밍할 수 있습니다.

   포털에서 작업 영역을 쿼리할 수 있으므로 Log analytics를 권장 합니다.

   또한 Blob storage를 사용 하는 경우 로그 데이터를 내보낼 때 필요에 따라 컨테이너와 blob이 만들어집니다.

   ![데이터 컬렉션 구성](./media/search-monitor-usage/configure-storage.png "데이터 수집 구성")

1. 설정을 저장 합니다.

1. 개체를 만들거나 삭제 (로그 이벤트 생성) 하 고 쿼리를 전송 하 여 (메트릭 생성) 테스트 합니다. 

Blob 저장소에서 컨테이너는 로그 또는 측정할 활동이 있는 경우에만 생성 됩니다. 데이터가 스토리지 계정에 복사되면 해당 데이터가 JSON 형식으로 다음 두 컨테이너에 배치됩니다.

* insights-logs-operationlogs: 검색 트래픽 로그인 경우
* insights-metrics-pt1m: 메트릭인 경우

**컨테이너가 Blob 저장소에 표시 되기 전에 1 시간이 걸립니다. 컨테이너 마다 시간당 하나의 blob이 있습니다.**

작업을 수행 하는 매시간 로그를 보관 합니다. 다음 경로는 오전 9:00에 12 2020 년 1 월에 생성 된 로그 파일 하나에 대 한 예입니다. 각 `/` 폴더: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>로그 스키마

Azure Cognitive Search 로그 데이터를 포함 하는 데이터 구조는 아래 스키마를 준수 합니다. 

Blob 저장소의 경우 각 blob에는 로그 개체의 배열을 포함 하는 **레코드** 라는 하나의 루트 개체가 있습니다. 각 Blob에는 동일한 시간 동안 발생한 모든 작업에 대한 레코드가 포함됩니다.

다음 표는 진단 로깅에 공통적인 필드의 일부 목록입니다.

| 속성 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| timeGenerated |Datetime |"2018-12-07T00:00:43.6872559Z" |작업 타임스탬프 |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |ResourceId |
| operationName |문자열 |"Query.Search" |작업 이름 |
| operationVersion |문자열 |"2019-05-06" |사용된 api-version |
| category |문자열 |"OperationLogs" |constant |
| resultType |문자열 |"Success" |가능한 값: Success 또는 Failure |
| resultSignature |int |200 |HTTP 결과 코드 |
| durationMS |int |50 |밀리초 단위의 작업 기간 |
| properties |object |다음 테이블 참조 |데이터별 작업을 포함하는 개체 |

### <a name="properties-schema"></a>속성 스키마

아래 속성은 Azure Cognitive Search에만 적용 됩니다.

| 속성 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| Description_s |문자열 |"GET /indexes('content')/docs" |작업의 엔드포인트 |
| Documents_d |int |42 |처리된 문서 수 |
| IndexName_s |문자열 |"테스트 인덱스" |작업과 연결된 인덱스의 이름 |
| Query_s |문자열 |"?search=AzureSearch&$count=true&api-version=2019-05-06" |쿼리 매개 변수 |

## <a name="metrics-schema"></a>메트릭 스키마

메트릭은 쿼리 요청에 대해 캡처되고 1 분 간격으로 측정 됩니다. 각 메트릭은 분당 최소, 최대 및 평균 값을 표시합니다. 자세한 내용은 [쿼리 요청 모니터링](search-monitor-queries.md)을 참조 하세요.

| 속성 | Type | 예제 | 메모 |
| --- | --- | --- | --- |
| resourceId |문자열 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |사용자의 리소스 ID |
| metricName |문자열 |"Latency" |메트릭 이름 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |작업의 타임스탬프 |
| average |int |64 |메트릭에 따라 메트릭 시간 간격에 있는 원시 샘플의 평균 값 (초 또는 백분율 단위)입니다. |
| minimum |int |37 |메트릭 시간 간격에서 원시 샘플의 최소값 (초 단위)입니다. |
| maximum |int |78 |메트릭 시간 간격에서 원시 샘플의 최대값 (초 단위)입니다.  |
| total |int |258 |메트릭 시간 간격에서 원시 샘플의 합계 값 (초 단위)입니다.  |
| count |int |4 |1 분 간격 내에 노드에서 로그로 내보낸 메트릭 수입니다.  |
| timegrain |문자열 |"PT1M" |ISO 8601에 있는 메트릭의 시간 수준입니다. |

쿼리가 실행 되는 데 일반적으로 밀리초 단위로 계산 되므로 초 단위로 측정 되는 쿼리만 QPS와 같은 메트릭에 표시 됩니다.

**초당 검색 쿼리** 메트릭의 경우 최소값은 해당 분 동안 등록 된 검색 쿼리의 가장 낮은 값입니다. 최대값도 마찬가지입니다. 평균은 전체 분에 대한 집계입니다. 예를 들어 1 분 이내에 SearchQueriesPerSecond에 대 한 최대값 인 높은 부하의 1 초, 평균 부하의 58 초, 마지막으로 하나의 쿼리 (최소 1 초)가 있는 것과 같은 패턴이 있을 수 있습니다.

**제한 된 검색 쿼리 백분율**, 최소, 최대, 평균 및 합계의 경우 1 분 동안 검색 쿼리의 총 수에서 제한 된 검색 쿼리의 비율입니다.

## <a name="view-log-files"></a>로그 파일 보기

Blob 저장소는 로그 파일을 보관 하는 데 사용 됩니다. JSON 편집기를 사용하여 로그 파일을 볼 수 있습니다. 없는 경우 [Visual Studio Code](https://code.visualstudio.com/download)를 사용하는 것이 좋습니다.

1. Azure Portal에서 Storage 계정을 엽니다. 

2. 왼쪽 탐색 창에서 **Blob**을 클릭합니다. **sights-logs-operationlogs** 및 **inights-metrics-pt1m**이 표시됩니다. 이러한 컨테이너는 로그 데이터를 Blob storage로 내보낼 때 Azure Cognitive Search에 의해 만들어집니다.

3. .json 파일에 도달할 때까지 폴더 계층 구조를 따라 아래로 클릭합니다.  상황에 맞는 메뉴를 사용하여 파일을 다운로드합니다.

파일이 다운로드되면 JSON 편집기에서 파일을 열어 내용을 봅니다.

## <a name="next-steps"></a>다음 단계

아직 수행 하지 않은 경우 검색 서비스 모니터링의 기본 사항을 검토 하 여 모든 감독 기능에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cognitive Search에서 작업 및 작업 모니터링](search-monitor-usage.md)