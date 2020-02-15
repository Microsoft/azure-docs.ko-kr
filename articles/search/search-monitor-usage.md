---
title: 작업 및 작업 모니터링
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 서비스에서 로깅을 사용 하도록 설정 하 고, 쿼리 작업 메트릭, 리소스 사용량 및 기타 시스템 데이터를 가져옵니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: c4a787362089dabf9c4eda9681358e7a70d8e78a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210547"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Azure Cognitive Search의 작업 및 활동 모니터링

이 문서에서는 작업 수준 (쿼리 및 인덱싱)에서 서비스 (리소스) 수준 모니터링을 소개 하 고 사용자 액세스를 모니터링 하기 위한 프레임 워크를 제안 합니다.

스펙트럼에서 기본 제공 인프라와 Azure Monitor 같은 기본 서비스와 통계, 개수 및 상태를 반환 하는 서비스 Api의 조합을 사용 합니다. 기능 범위를 이해 하면 문제에 대 한 사전 대응 응답을 위한 효과적인 통신 시스템을 구성 하거나 만드는 데 도움이 됩니다.

## <a name="use-azure-monitor"></a>Azure Monitor 사용

Azure Cognitive Search을 비롯 한 많은 서비스는 경고, 메트릭 및 로깅 진단 데이터에 대 한 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/) 활용 합니다. Azure Cognitive Search의 경우 기본 제공 모니터링 인프라는 주로 리소스 수준 모니터링 (서비스 상태) 및 [쿼리 모니터링](search-monitor-queries.md)에 사용 됩니다.

다음 스크린샷은 포털에서 Azure Monitor 기능을 찾는 데 도움이 됩니다.

+ 기본 개요 페이지에 있는 **모니터링** 탭은 주요 메트릭을 한 눈에 표시 합니다.
+ **활동 로그**는 개요, 리소스 수준 작업에 대 한 보고서: 서비스 상태 및 API 키 요청 알림입니다.
+ **모니터링**은 목록 아래쪽에서 구성 가능한 경고, 메트릭 및 진단 로그를 제공 합니다. 필요할 때 이러한 항목을 만듭니다. 데이터를 수집 하 고 저장 하면 정보를 쿼리하거나 시각화할 수 있습니다.

![검색 서비스의 Azure Monitor 통합](./media/search-monitor-usage/azure-monitor-search.png
 "검색 서비스의 Azure Monitor 통합")

### <a name="precision-of-reported-numbers"></a>보고 된 숫자의 전체 자릿수

포털 페이지는 몇 분 마다 새로 고쳐집니다. 이와 같이 포털에서 보고 되는 숫자는 대략적으로 시스템에서 요청을 얼마나 잘 처리 하는지에 대 한 일반적인 의미를 제공 하기 위한 것입니다. 초당 쿼리 수 (QPS)와 같은 실제 메트릭은 페이지에 표시 되는 숫자 보다 높거나 낮을 수 있습니다.

## <a name="activity-logs-and-service-health"></a>활동 로그 및 서비스 상태

[**활동 로그**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) 는 Azure Resource Manager에서 정보를 수집 하 고 서비스 상태에 대 한 변경 내용을 보고 합니다. 서비스 상태와 관련 된 위험, 오류 및 경고 조건에 대 한 활동 로그를 모니터링할 수 있습니다.

쿼리, 인덱싱 또는 개체 생성과 같은 서비스 내 태스크의 경우 *관리자 키 가져오기* 및 각 요청에 대 한 *쿼리 키 가져오기* 와 같은 일반 정보 알림이 표시 되지만 특정 작업 자체는 표시 되지 않습니다. 이 수준에 대 한 자세한 내용은 진단 로깅을 구성 해야 합니다.

**활성 로그**는 왼쪽 탐색 창, 위쪽 창 명령 모음의 알림 또는 **진단 및 문제 해결** 페이지에서 액세스할 수 있습니다.

## <a name="monitor-storage"></a>저장소 모니터링

개요 페이지에 기본 제공 되는 탭 페이지는 리소스 사용량에 대해 보고 합니다. 이 정보는 구성 없이 서비스 사용을 시작 하는 즉시 사용할 수 있게 되며, 페이지는 몇 분 마다 새로 고쳐집니다. 

[프로덕션 워크로드에 사용할 계층](search-sku-tier.md) 또는 [활성 복제본 및 파티션의 수를 조정할지 여부](search-capacity-planning.md)를 최종적으로 결정하는 경우 이러한 메트릭은 리소스 사용 속도 및 현재 구성의 기존 부하 처리에 대한 효율성을 보여줌으로써 이러한 결정을 내리는 데 도움이 됩니다.

현재 저장소와 관련 된 경고를 사용할 수 없습니다. 저장소 소비는 집계 되거나 **Azuremetrics**기록 되지 않습니다. 리소스 관련 알림을 받으려면 사용자 지정 솔루션을 만들어야 합니다.

포털에서 **사용량** 탭은 서비스 계층에 의해 적용 되는 현재 한 [도](search-limits-quotas-capacity.md) 를 기준으로 리소스 가용성을 보여 줍니다. 

다음 그림에서는 각 형식의 3개 개체와 50MB의 스토리지로 제한된 체험 서비스를 보여 줍니다. 기본 또는 표준 서비스의 제한이 더 높으며, 파티션 수를 늘리면 최대 스토리지 크기도 비례하여 증가합니다.

![계층 제한과 관련 하 여 사용 상태](./media/search-monitor-usage/usage-tab.png
 "계층 제한과 관련 하 여 사용 상태")

## <a name="monitor-workloads"></a>워크 로드 모니터링

기록 된 이벤트에는 인덱싱 및 쿼리와 관련 된 이벤트가 포함 됩니다. Log Analytics의 **Azure 진단** 테이블은 쿼리 및 인덱싱과 관련 된 작업 데이터를 수집 합니다.

기록 된 대부분의 데이터는 읽기 전용 작업에 대 한 것입니다. 로그에 캡처되지 않은 기타 만들기-업데이트-삭제 작업의 경우 검색 서비스에서 시스템 정보를 쿼리할 수 있습니다.

| OperationName | Description |
|---------------|-------------|
| ServiceStats | 이 작업은 로드 되거나 새로 고쳐질 때 포털 개요 페이지를 채우도록 직접 또는 암시적으로 호출 되는 [서비스 통계를 가져오는](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)루틴 호출입니다. |
| 쿼리. 검색 |  인덱스에 대 한 쿼리 요청은 쿼리 [모니터링](search-monitor-queries.md) 에서 기록 된 쿼리에 대 한 정보를 확인 합니다.|
| 인덱싱. 인덱스  | 이 작업은 [문서를 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)하는 호출입니다. |
| 인덱스만. 프로토타입에서 | 데이터 가져오기 마법사에서 만든 인덱스입니다. |
| 인덱서. 만들기 | 데이터 가져오기 마법사를 통해 명시적으로 또는 암시적으로 인덱서를 만듭니다. |
| 인덱서. Get | 인덱서가 실행 될 때마다 인덱서의 이름을 반환 합니다. |
| 인덱서. 상태 | 인덱서가 실행 될 때마다 인덱서 상태를 반환 합니다. |
| 데이터 원본. Get | 인덱서가 실행 될 때마다 데이터 원본의 이름을 반환 합니다.|
| 인덱스. Get | 인덱서가 실행 될 때마다 인덱스 이름을 반환 합니다. |

### <a name="kusto-queries-about-workloads"></a>작업에 대 한 kusto 쿼리

로깅을 사용 하도록 설정한 경우 서비스에서 실행 된 작업 목록 및 시기에 대해 **Azurediagnostics** 를 쿼리할 수 있습니다. 작업을 상호 연결 하 여 성능 변화를 조사할 수도 있습니다.

#### <a name="example-list-operations"></a>예: 작업 나열 

작업 목록과 각 작업의 수를 반환 합니다.

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>예: 작업 상관 관계

쿼리 요청을 인덱싱 작업과 상호 연결 하 고 시간 차트에서 데이터 요소를 렌더링 하 여 작업이 일치 하는지 확인 합니다.

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>검색 Api 사용

Azure Cognitive Search REST API 및 .NET SDK는 서비스 메트릭, 인덱스 및 인덱서 정보 및 문서 수에 대 한 프로그래밍 방식의 액세스를 제공 합니다.

+ [서비스 통계 가져오기](/rest/api/searchservice/get-service-statistics)
+ [인덱스 통계 가져오기](/rest/api/searchservice/get-index-statistics)
+ [문서 개수 가져오기](/rest/api/searchservice/count-documents)
+ [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>사용자 액세스 모니터링

검색 인덱스는 더 큰 클라이언트 응용 프로그램의 구성 요소 이므로 인덱스에 대 한 액세스를 제어 하는 기본 제공 사용자 단위 방법론은 없습니다. 요청은 관리자 또는 쿼리 요청에 대해 클라이언트 응용 프로그램에서 가져온 것으로 간주 됩니다. 관리자 읽기/쓰기 작업에는 전체 서비스에서 개체를 만들고, 업데이트 하 고, 삭제 하는 작업이 포함 됩니다. 읽기 전용 작업은 단일 인덱스로 범위가 지정 된 documents 컬렉션에 대 한 쿼리입니다. 

따라서 로그에 표시 되는 항목은 관리 키 또는 쿼리 키를 사용 하는 호출에 대 한 참조입니다. 적절 한 키는 클라이언트 코드에서 시작 되는 요청에 포함 됩니다. 서비스는 id 토큰 또는 가장을 처리할 수 없습니다.

사용자 단위 권한 부여에 대 한 비즈니스 요구 사항이 있는 경우 권장 사항은 Azure Active Directory와 통합 됩니다. $Filter 및 사용자 id를 사용 하 여 사용자가 볼 수 없는 문서의 [검색 결과를 지울](search-security-trimming-for-azure-search-with-aad.md) 수 있습니다. 

$Filter 매개 변수를 포함 하는 쿼리 문자열과 별도로이 정보를 기록할 수 있는 방법은 없습니다. 보고 쿼리 문자열에 대 한 자세한 내용은 [쿼리 모니터링](search-monitor-queries.md) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Cognitive Search와 같은 리소스를 포함 하 여 Azure 서비스를 감독 하는 데 Azure Monitor 능숙가 필요 합니다. Azure Monitor에 익숙하지 않은 경우 리소스와 관련 된 문서를 검토 하는 시간을 소요 합니다. 자습서 외에도 다음 문서를 시작 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Azure Monitor를 사용 하 여 Azure 리소스 모니터링](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
