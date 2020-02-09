---
title: Azure 진단 설정을 사용 하 여 Azure Cosmos DB 데이터 모니터링
description: Azure 진단 설정을 사용 하 여에 저장 된 데이터의 성능 및 가용성을 모니터링 하는 방법에 대해 알아봅니다 Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 670797eb833b0a145a18e20c6bba711ca11609bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483285"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure에서 진단 설정을 사용 하 여 Azure Cosmos DB 데이터 모니터링

Azure의 진단 설정은 리소스 로그를 수집 하는 데 사용 됩니다. Azure 리소스 로그는 리소스에서 내보내며, 해당 리소스의 작업에 대한 풍부 하 고 빈번한 데이터를 제공 합니다. 이러한 로그는 요청당 캡처되고 "데이터 평면 로그" 라고도 합니다. 데이터 평면 작업의 몇 가지 예로는 delete, insert 및 readFeed 있습니다. 이러한 로그의 내용은 리소스 종류에 따라 달라집니다.

플랫폼 메트릭과 활동 로그는 자동으로 수집 되지만 리소스 로그를 수집 하거나 Azure Monitor 외부로 전달 하려면 진단 설정을 만들어야 합니다. 다음 단계를 사용 하 여 Azure Cosmos 계정에 대한 진단 설정을 켤 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. Azure Cosmos 계정으로 이동합니다. **진단 설정** 창을 열고 **진단 설정 추가** 옵션을 선택 합니다.

1. **진단 설정** 창에서 다음 세부 정보로 양식을 채웁니다. 

    * **이름**: 만들 로그에 대한 이름을 입력합니다.

    * **저장소 계정에 보관**하는 로그를 저장 하 고, **이벤트 허브에 스트림** 하거나, **로 보낼** 수 있습니다 Log Analytics

1. 진단 설정을 만들 때 수집할 로그 범주를 지정 합니다. Azure Cosmos DB에서 지 원하는 로그의 범주는 아래에 나열 된 샘플 로그와 함께 아래에 나열 되어 있습니다.

 * **DataPlaneRequests**: AZURE COSMOS DB의 SQL, Graph, MongoDB, Cassandra 및 Table API 계정을 포함 하는 모든 api에 백 엔드 요청을 기록 하려면이 옵션을 선택 합니다. 유의 해야 할 주요 속성은 `Requestcharge`, `statusCode`, `clientIPaddress`및 `partitionID`입니다.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: MongoDB에 대한 AZURE COSMOS DB의 api에 대한 요청을 처리 하기 위해 프런트 엔드에서 사용자가 시작한 요청을 기록 하려면이 로그 유형을 다른 API 계정에 사용할 수 없습니다. MongoDB 요청은 MongoRequests 및 DataPlaneRequests에도 표시 됩니다. 주의 해야 할 주요 속성은 `Requestcharge`, `opCode`입니다.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **Queryruntimestatistics**: 실행 된 쿼리 텍스트를 기록 하려면이 옵션을 선택 합니다. 이 로그 유형은 SQL API 계정에만 사용할 수 있습니다.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* 파티션 **키 통계를**기록 하려면이 옵션을 선택 합니다. 이는 현재 파티션 키의 저장소 크기 (KB)로 표시 됩니다. 이 문서의 [Azure 진단 쿼리를 사용 하 여 문제 해결](#diagnostic-queries) 섹션을 참조 하세요. 예를 들어 "파티션"을 사용 하는 쿼리를 사용 합니다. 대부분의 데이터 저장소를 차지 하는 처음 세 개의 파티션 키에 대해 로그를 내보냅니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키, 저장소 크기 (KB) 등의 데이터가 포함 됩니다.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* 분할:이 로그는 파티션 키의 초당 집계 된 사용량을 보고 **합니다.** 현재 Azure Cosmos DB은 SQL API 계정에 대한 파티션 키와 지점 읽기/쓰기 및 저장 프로시저 작업에 대한 파티션 키를 보고 합니다. 다른 Api 및 작업 유형은 지원 되지 않습니다. 다른 Api의 경우 진단 로그 테이블의 파티션 키 열이 비어 있게 됩니다. 이 로그에는 구독 ID, 지역 이름, 데이터베이스 이름, 컬렉션 이름, 파티션 키, 작업 유형, 요청 요금 등의 데이터가 포함 됩니다. 이 문서의 [Azure 진단 쿼리를 사용 하 여 문제 해결](#diagnostic-queries) 섹션을 참조 하세요. 예를 들어 "파티션"을 사용 하는 쿼리를 사용 합니다. 

* **ControlPlaneRequests**:이 로그에는 계정 만들기, 지역 추가 또는 제거, 계정 복제 설정 업데이트 등의 제어 평면 작업에 대한 세부 정보가 포함 됩니다. 이 로그 유형은 SQL (Core), MongoDB, Gremlin, Cassandra, Table API를 포함 하는 모든 API 유형에 사용할 수 있습니다.

* **요청**: Azure Cosmos DB에서 진단 설정의 대상으로 메트릭 데이터를 수집 하려면이 옵션을 선택 합니다. Azure 메트릭에 의해 자동으로 수집 되는 동일한 데이터입니다. 리소스 로그를 사용 하 여 메트릭 데이터를 수집 하 여 두 종류의 데이터를 함께 분석 하 고 Azure Monitor 외부에서 메트릭 데이터를 보냅니다.

Azure Portal, CLI 또는 PowerShell을 사용 하 여 진단 설정을 만드는 방법에 대한 자세한 내용은 [Azure에서 플랫폼 로그 및 메트릭을 수집 하는 진단 설정 만들기](../azure-monitor/platform/diagnostic-settings.md) 문서를 참조 하세요.


## <a id="diagnostic-queries"></a>진단 쿼리와 관련 된 문제 해결

1. 비용이 많이 드는 쿼리에 대한 요청 요금을 가져오는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. 많은 작업을 수행 하는 작업을 찾는 방법

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. 여러 작업에 대한 배포를 가져오는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. 파티션에서 제공 하는 최대 처리량은 무엇 인가요?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. 초당 파티션 키/s 사용에 대한 정보를 가져오는 방법

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. 특정 파티션 키에 대한 요청 요금을 가져오는 방법

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. 특정 기간에 사용 되는 대부분의 o s/s를 사용 하 여 상위 파티션 키를 가져오는 방법 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. 저장소 크기가 8gb 보다 큰 파티션 키에 대한 로그를 가져오는 방법

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. 데이터베이스 계정에 대한 상위 3 개 파티션 간의 기울기를 평가 하는 파티션 키 통계를 가져오는 방법

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에 대한 Azure Monitor](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Azure Cosmos DB에서 메트릭을 사용하여 모니터링 및 디버그](use-metrics.md)
