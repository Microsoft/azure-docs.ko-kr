---
title: 모니터링 Azure Cosmos DB | Microsoft Docs
description: Azure Cosmos DB의 성능 및 가용성을 모니터링 하는 방법에 대해 알아봅니다.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: c166811bbfd27691f9a01a944d304d06560b0232
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445188"
---
# <a name="monitoring-azure-cosmos-db"></a>모니터링 Azure Cosmos DB
Azure 리소스를 사용 하는 중요 한 응용 프로그램 및 비즈니스 프로세스를 사용 하는 경우 해당 리소스의 가용성, 성능 및 작업을 모니터링 하려고 합니다. 이 문서에서는 Azure Cosmos 데이터베이스에서 생성 되는 모니터링 데이터와 Azure Monitor 기능을 사용 하 여이 데이터를 분석 하 고 경고 하는 방법을 설명 합니다.

## <a name="what-is-azure-monitor"></a>Azure Monitor란?
Azure Cosmos DB는 azure에서 전체 stack 모니터링 서비스인 [Azure Monitor](../azure-monitor/overview.md) 를 사용 하 여 모니터링 데이터를 만들며,이를 통해 다른 클라우드 및 온-프레미스의 리소스 외에도 azure 리소스를 모니터링할 수 있는 완전 한 기능 집합을 제공 합니다. 

Azure 서비스를 모니터링 하는 방법을 잘 모르는 경우 다음을 설명 하는 [Azure Monitor으로 azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md) 문서부터 시작 하세요.

- Azure Monitor란?
- 모니터링과 관련 된 비용
- Azure에서 수집 된 데이터 모니터링
- 데이터 수집 구성
- 모니터링 데이터를 분석 하 고 경고 하는 Azure의 표준 도구

다음 섹션에서는 Azure Cosmos DB에서 수집한 특정 데이터를 설명 하 고 Azure tools를 사용 하 여 데이터 수집을 구성 하 고이 데이터를 분석 하기 위한 예제를 제공 하 여이 문서를 작성 합니다.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Cosmos DB에 대한 Azure Monitor (미리 보기)
[Azure Cosmos DB에 대한 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md) [Azure Monitor의 통합 문서 기능](../azure-monitor/app/usage-workbooks.md) 을 기반으로 하며 아래 섹션에 설명 된 Cosmos DB에 대해 수집 된 것과 동일한 모니터링 데이터를 사용 합니다. 이 도구를 사용 하 여 통합 된 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 오류, 용량 및 운영 상태를 확인 하 고 자세한 분석 및 경고를 위해 Azure Monitor의 다른 기능을 활용 합니다. 

![Cosmos DB에 대한 Azure Monitor](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Azure Cosmos DB에서 수집한 데이터 모니터링

Azure Cosmos DB는 [azure 리소스의 데이터 모니터링](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)에 설명 된 다른 azure 리소스와 동일한 종류의 모니터링 데이터를 수집 합니다. Azure Cosmos DB에서 만든 로그 및 메트릭에 대한 자세한 내용은 [Azure Cosmos DB 모니터링 데이터 참조](monitor-cosmos-db-reference.md) 를 참조 하세요.

각 Azure Cosmos 데이터베이스에 대한 Azure Portal의 **개요** 페이지에는 요청 및 시간별 청구 사용량을 포함 하 여 데이터베이스 사용에 대한 간략 한 보기가 포함 되어 있습니다. 이 정보는 유용 하지만 적은 양의 모니터링 데이터만 사용할 수 있습니다. 일부 구성에서 추가 데이터 수집을 사용 하도록 설정 하는 동안 데이터베이스를 만드는 즉시 이러한 데이터 중 일부가 자동으로 수집 되 고 분석에 사용할 수 있습니다.

![개요 페이지](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>메트릭 데이터 분석

Azure Cosmos DB는 메트릭을 사용 하기 위한 사용자 지정 환경을 제공 합니다. 이 경험을 사용 하 고 다양 한 Azure Cosmos DB 시나리오를 분석 하는 방법에 대한 자세한 내용은 [Azure Monitor에서 Azure Cosmos DB 메트릭 모니터링 및 디버그](cosmos-db-azure-monitor-metrics.md) 를 참조 하세요.

**Azure Monitor** 메뉴에서 **메트릭을 열어 메트릭 탐색기를 사용** 하 여 다른 Azure 서비스의 메트릭과 함께 Azure Cosmos DB에 대한 메트릭을 분석할 수 있습니다. 이 도구 사용에 대한 자세한 내용은 [Azure 메트릭 탐색기 시작](../azure-monitor/platform/metrics-getting-started.md) 을 참조 하세요. Azure Cosmos DB에 대한 모든 메트릭은 **표준 메트릭에 Cosmos DB**네임 스페이스에 있습니다. 차트에 필터를 추가할 때 이러한 메트릭에 다음 차원을 사용할 수 있습니다.

- CollectionName
- DatabaseName
- OperationType
- 지역
- StatusCode


## <a name="analyzing-log-data"></a>로그 데이터 분석
Azure Monitor 로그의 데이터는 각각 고유한 속성 집합이 있는 테이블에 저장 됩니다. Azure Cosmos DB은 다음 테이블에 데이터를 저장 합니다.

| Table | Description |
|:---|:---|
| AzureDiagnostics | 여러 서비스에서 리소스 로그를 저장 하는 데 사용 하는 공통 테이블입니다. Azure Cosmos DB의 리소스 로그는 `MICROSOFT.DOCUMENTDB`를 통해 식별할 수 있습니다.   |
| AzureActivity    | 활동 로그의 모든 레코드를 저장 하는 공통 테이블입니다. 


> [!IMPORTANT]
> Azure Cosmos DB 메뉴에서 **로그** 를 선택 하면 쿼리 범위가 현재 Azure Cosmos 데이터베이스로 설정 된 Log Analytics 열립니다. 즉, 로그 쿼리에는 해당 리소스의 데이터만 포함 됩니다. 다른 데이터베이스의 데이터 또는 다른 Azure 서비스의 데이터를 포함 하는 쿼리를 실행 하려면 **Azure Monitor** 메뉴에서 **로그** 를 선택 합니다. 자세한 내용은 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](../azure-monitor/log-query/scope.md) 를 참조 하세요.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Log Analytics 쿼리를 Azure Cosmos DB Azure Monitor

다음은 Azure Cosmos 컨테이너를 모니터링 하는 데 도움이 되는 **로그 검색** 검색 표시줄에 입력할 수 있는 몇 가지 쿼리입니다. 이러한 쿼리는 [새 언어](../log-analytics/log-analytics-log-search-upgrade.md)에서 작동합니다.

다음은 Azure Cosmos 데이터베이스를 모니터링 하는 데 사용할 수 있는 쿼리입니다.

* 지정된 기간 동안 Azure Cosmos DB의 모든 진단 로그를 쿼리하려면

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* 가장 최근에 로깅된 10개 이벤트를 쿼리하려면

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* 모든 작업을 쿼리하고 작업 유형별로 그룹화하려면

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* 모든 작업에 대해 쿼리하려면 자원별로 그룹화 합니다.

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* 모든 사용자 작업을 쿼리하고 리소스별로 그룹화하려면

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* **DataPlaneRequests** 및 **queryruntimestatistics**의 데이터로 조인 되는 100 RUs 보다 큰 모든 쿼리를 가져옵니다.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* 3밀리초보다 오래 소요되는 작업을 쿼리하려면

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* 작업을 실행 중인 에이전트를 쿼리하려면

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* 장기 실행 작업이 수행된 시점을 쿼리하려면

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* 파티션 키 통계를 가져와서 데이터베이스 계정에 대한 상위 3 개 파티션 간의 오차를 평가 하려면:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>프로그래밍 방식으로 Azure Cosmos DB 모니터링
포털에서 제공되는 계정 수준 메트릭(예: 계정 스토리지 사용 및 총 요청)은 SQL API를 통해 사용할 수 없습니다. 그러나 SQL API를 사용하여 컬렉션 수준에서 사용량 현황 데이터를 검색할 수 있습니다. 컬렉션 수준 데이터를 검색하려면 다음을 수행합니다.

* REST API를 사용하려면 [컬렉션에 대해 GET을 수행](https://msdn.microsoft.com/library/mt489073.aspx)합니다. 컬렉션에 대한 할당량 및 사용량 정보는 응답의 x-ms-resource-quota 및 x-ms-resource-usage 헤더에서 반환됩니다.
* .NET SDK를 사용하려면 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 메서드를 사용합니다. 이 메서드는 **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** 등의 여러 사용량 속성이 포함된 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)를 반환합니다.

추가 메트릭에 액세스하려면 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)를 사용하세요. 가용 메트릭 정의는 다음을 호출하면 검색할 수 있습니다.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

개별 메트릭을 검색하는 쿼리는 다음 형식을 사용합니다.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>다음 단계

- Azure Cosmos DB에서 만든 로그 및 메트릭에 대한 참조는 [Azure Cosmos DB 모니터링 데이터 참조](monitor-cosmos-db-reference.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md) 을 참조 하세요.
