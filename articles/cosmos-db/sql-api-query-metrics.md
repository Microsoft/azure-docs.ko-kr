---
title: Azure Cosmos DB SQL API에 대한 SQL 쿼리 메트릭
description: Azure Cosmos DB 요청의 SQL 쿼리 성능을 계측 및 디버깅하는 방법에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: sngun
ms.openlocfilehash: c7b62f66830e17fd8f6607e0a629307a9ab6fc78
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546423"
---
# <a name="tuning-query-performance-with-azure-cosmos-db"></a>Azure Cosmos DB와 함께 쿼리 성능 튜닝

Azure Cosmos DB에서는 스키마 또는 보조 인덱스를 요구하지 않고도 [데이터를 쿼리하기 위한 SQL API](how-to-sql-query.md)를 제공합니다. 이 문서에서는 개발자를 위한 다음 정보를 제공합니다.

* Azure Cosmos DB의 SQL 쿼리 실행 작동 방식에 대한 대략적인 정보
* 쿼리 요청 및 응답 헤더, 클라이언트 SDK 옵션에 대한 세부 정보
* 쿼리 성능에 대한 팁 및 모범 사례
* 쿼리 성능을 디버깅하는 데 SQL 실행 통계를 활용하는 방법의 예

## <a name="about-sql-query-execution"></a>SQL 쿼리 실행 정보

Azure Cosmos DB에는 컨테이너에 데이터를 저장하여 [저장소 크기 또는 요청 처리량](partition-data.md)에 맞게 확장할 수 있습니다. Azure Cosmos DB는 데이터 증가 또는 프로비전된 처리량 증가를 처리하도록 데이터를 실제 파티션 간에 원활하게 조정합니다. REST API 또는 지원되는 [SQL SDK](sql-api-sdk-dotnet.md) 중 하나를 사용하여 모든 컨테이너에 대해 SQL 쿼리를 실행할 수 있습니다.

분할에 대한 간략한 개요: 데이터를 실제 파티션 간에 분할하는 방법을 결정하는 파티션 키(예: “city”)를 정의합니다. 단일 파티션 키에 속하는 데이터(예를 들어 "city" == "Seattle")는 실제 파티션 내에 저장되지만 일반적으로 실제 파티션 하나는 여러 파티션 키를 포함합니다. 파티션이 저장소 크기에 도달하면 서비스는 파티션을 두 개의 새로운 파티션으로 원활하게 분할하고 파티션 키를 이러한 파티션 간에 균등하게 나눕니다. 파티션은 일시적이므로 API는 파티션 키 해시의 범위를 나타내는 “파티션 키 범위”의 추상화를 사용합니다. 

Azure Cosmos DB에 쿼리를 실행하는 경우 SDK는 다음 논리 단계를 수행합니다.

* SQL 쿼리를 구문 분석하여 쿼리 실행 계획을 확인합니다. 
* 쿼리에 파티션 키에 대한 필터가 포함되는 경우(예: `SELECT * FROM c WHERE c.city = "Seattle"`) 단일 파티션으로 라우팅됩니다. 쿼리에 파티션 키에 대한 필터가 포함되지 않는 경우 모든 파티션에서 쿼리가 실행되고 결과는 클라이언트 쪽에 병합됩니다.
* 쿼리는 클라이언트 구성에 따라 각 파티션 내에서 연속으로 또는 병렬로 실행됩니다. 각 파티션 내에서 쿼리는 쿼리 복잡성, 구성된 페이지 크기 및 컬렉션의 프로비전된 처리량에 따라 한 번 이상 왕복할 수 있습니다. 실행마다 쿼리 실행에서 사용한 [요청 단위](request-units.md) 수 및 실행 통계(필요할 경우)를 반환합니다. 
* SDK는 파티션 간 쿼리 결과의 요약을 수행합니다. 예를 들어 쿼리에 파티션 간 ORDER BY가 있을 경우 개별 파티션의 결과는 병합 및 정렬되어 전역적으로 정렬된 순서로 결과를 반환합니다. 쿼리가 `COUNT`와 같은 집계인 경우, 개별 파티션 수가 합산되어 전체 수가 생성됩니다.

SDK는 쿼리 실행을 위한 다양한 옵션을 제공합니다. 예를 들어 .NET에서는 이러한 옵션을 `FeedOptions` 클래스에서 사용할 수 있습니다. 다음 표에서 이러한 옵션 및 쿼리 실행 시간이 어떻게 영향을 주는지 설명합니다. 

| 옵션 | 설명 |
| ------ | ----------- |
| `EnableCrossPartitionQuery` | 둘 이상의 파티션 간에 실행되어야 하는 쿼리에 대해 true로 설정해야 합니다. 개발 시간 중에 성능을 고려하여 균형을 유지할 수 있는 명시적인 플래그입니다. |
| `EnableScanInQuery` | 인덱싱을 옵트아웃(opt out)했지만 검색을 통해 쿼리를 실행하려는 경우 true로 설정해야 합니다. 요청된 필터 경로에 인덱싱이 사용되지 않는 경우에만 적용 가능합니다. | 
| `MaxItemCount` | 왕복당 서버에 반환할 최대 항목 수입니다. -1로 설정하면 서버에서 항목 수를 관리하도록 할 수 있습니다. 또는 이 값을 낮춰서 왕복당 적은 수의 항목만 검색할 수 있습니다. 
| `MaxBufferedItemCount` | 클라이언트 쪽 옵션이므로 파티션 간 ORDER BY를 수행할 때 메모리 사용량을 제한하는 데 사용합니다. 값이 높을수록 파티션 간 정렬 대기 시간이 줄어듭니다. |
| `MaxDegreeOfParallelism` | Azure Cosmos DB 데이터베이스 서비스에서 병렬 쿼리를 실행하는 동안 클라이언트 쪽에서 실행되는 동시 작업 수를 가져오거나 설정합니다. 양수 속성 값은 동시 작업 수를 설정 값으로 제한합니다. 0보다 작은 값으로 설정할 경우 시스템이 실행할 동시 작업 수를 자동으로 결정합니다. |
| `PopulateQueryMetrics` | 컴파일 시간, 인덱스 루프 시간 및 문서 로드 시간과 같은 다양한 쿼리 실행 단계에서 소요한 시간에 대한 통계 정보를 자세히 로깅할 수 있습니다. 쿼리 통계의 출력을 Azure 지원과 공유하여 쿼리 성능 문제를 진단할 수 있습니다. |
| `RequestContinuation` | 쿼리에서 반환한 불투명 연속 토큰을 전달하여 쿼리 실행을 다시 시작할 수 있습니다. 연속 토큰은 쿼리 실행에 필요한 모든 상태를 캡슐화합니다. |
| `ResponseContinuationTokenLimitInKb` | 서버에서 반환한 연속 토큰의 최대 크기를 제한할 수 있습니다. 애플리케이션 호스트에 응답 헤더 크기에 대한 제한이 있는 경우 설정해야 합니다. 이 옵션을 설정하면 해당 쿼리에 사용된 전체 기간 및 RU를 늘릴 수 있습니다.  |

예를 들어, 파티션 키로 `/city`를 포함하고 100,000 RU/s의 처리량으로 프로비전된 컬렉션에 대해 요청된 파티션 키에 대한 쿼리 예제를 살펴보겠습니다. .NET에서 다음과 같이 `CreateDocumentQuery<T>`를 사용하여 이 쿼리를 요청합니다.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
        MaxItemCount = -1, 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();
```

위에 표시된 SDK 코드 조각은 다음 REST API 요청에 해당합니다.

```
POST https://arramacquerymetrics-westus.documents.azure.com/dbs/db/colls/sample/docs HTTP/1.1
x-ms-continuation: 
x-ms-documentdb-isquery: True
x-ms-max-item-count: -1
x-ms-documentdb-query-enablecrosspartition: True
x-ms-documentdb-query-parallelizecrosspartitionquery: True
x-ms-documentdb-query-iscontinuationexpected: True
x-ms-documentdb-populatequerymetrics: True
x-ms-date: Tue, 27 Jun 2017 21:52:18 GMT
authorization: type%3dmaster%26ver%3d1.0%26sig%3drp1Hi83Y8aVV5V6LzZ6xhtQVXRAMz0WNMnUuvriUv%2b4%3d
x-ms-session-token: 7:8,6:2008,5:8,4:2008,3:8,2:2008,1:8,0:8,9:8,8:4008
Cache-Control: no-cache
x-ms-consistency-level: Session
User-Agent: documentdb-dotnet-sdk/1.14.1 Host/32-bit MicrosoftWindowsNT/6.2.9200.0
x-ms-version: 2017-02-22
Accept: application/json
Content-Type: application/query+json
Host: arramacquerymetrics-westus.documents.azure.com
Content-Length: 52
Expect: 100-continue

{"query":"SELECT * FROM c WHERE c.city = 'Seattle'"}
```

각 쿼리 실행 페이지는 `Accept: application/query+json` 헤더가 있는 REST API `POST`와 본문의 SQL 쿼리에 해당합니다. 각 쿼리는 서버로 한 번 이상의 왕복을 수행하는데, 클라이언트 및 서버 간에 `x-ms-continuation` 토큰이 화면에 표시되면 실행을 다시 시작합니다. FeedOptions의 구성 옵션은 서버에 요청 헤더 형태로 전달됩니다. 예를 들어 `MaxItemCount`는 `x-ms-max-item-count`에 해당합니다. 

요청은 다음(가독성을 위해 자름) 응답을 반환합니다.

```
HTTP/1.1 200 Ok
Cache-Control: no-store, no-cache
Pragma: no-cache
Transfer-Encoding: chunked
Content-Type: application/json
Server: Microsoft-HTTPAPI/2.0
Strict-Transport-Security: max-age=31536000
x-ms-last-state-change-utc: Tue, 27 Jun 2017 21:01:57.561 GMT
x-ms-resource-quota: documentSize=10240;documentsSize=10485760;documentsCount=-1;collectionSize=10485760;
x-ms-resource-usage: documentSize=1;documentsSize=884;documentsCount=2000;collectionSize=1408;
x-ms-item-count: 2000
x-ms-schemaversion: 1.3
x-ms-alt-content-path: dbs/db/colls/sample
x-ms-content-path: +9kEANVq0wA=
x-ms-xp-role: 1
x-ms-documentdb-query-metrics: totalExecutionTimeInMs=33.67;queryCompileTimeInMs=0.06;queryLogicalPlanBuildTimeInMs=0.02;queryPhysicalPlanBuildTimeInMs=0.10;queryOptimizationTimeInMs=0.00;VMExecutionTimeInMs=32.56;indexLookupTimeInMs=0.36;documentLoadTimeInMs=9.58;systemFunctionExecuteTimeInMs=0.00;userFunctionExecuteTimeInMs=0.00;retrievedDocumentCount=2000;retrievedDocumentSize=1125600;outputDocumentCount=2000;writeOutputTimeInMs=18.10;indexUtilizationRatio=1.00
x-ms-request-charge: 604.42
x-ms-serviceversion: version=1.14.34.4
x-ms-activity-id: 0df8b5f6-83b9-4493-abda-cce6d0f91486
x-ms-session-token: 2:2008
x-ms-gatewayversion: version=1.14.33.2
Date: Tue, 27 Jun 2017 21:59:49 GMT
```

쿼리에서 반환된 키 응답 헤더는 다음을 포함합니다.

| 옵션 | 설명 |
| ------ | ----------- |
| `x-ms-item-count` | 응답에 반환된 항목 수입니다. 제공된 `x-ms-max-item-count`, 최대 응답 페이로드 크기 내에 적합할 수 있는 항목 수, 프로비전된 처리량 및 쿼리 실행 시간에 종속적입니다. |  
| `x-ms-continuation:` | 추가 결과가 제공되는 경우 쿼리의 실행을 다시 시작하기 위한 연속 토큰입니다. | 
| `x-ms-documentdb-query-metrics` | 실행에 대한 쿼리 통계입니다. 다양한 쿼리 실행 단계에서 소요한 시간에 대한 통계 정보를 포함하는 구분된 문자열입니다. `x-ms-documentdb-populatequerymetrics`가 `True`로 설정되면 반환됩니다. | 
| `x-ms-request-charge` | 쿼리에서 사용한 [요청 단위](request-units.md) 수입니다. | 

REST API 요청 헤더 및 옵션에 대한 자세한 내용은 [Querying resources using the REST API](https://docs.microsoft.com/rest/api/cosmos-db/querying-cosmosdb-resources-using-the-rest-api)(REST API를 사용하여 리소스 쿼리)를 참조하세요.

## <a name="best-practices-for-query-performance"></a>쿼리 성능에 대한 모범 사례
Azure Cosmos DB 쿼리 성능에 영향을 주는 가장 일반적인 요소는 다음과 같습니다. 이 문서에서 이러한 각 항목에 대해 자세히 살펴보겠습니다.

| 비율 | 팁 | 
| ------ | -----| 
| 프로비전된 처리량 | 쿼리당 RU를 측정하고 사용자 쿼리에 필요한 프로비전된 처리량이 있는지 확인합니다. | 
| 분할 및 파티션 키 | 짧은 대기 시간을 위해 필터 절에 파티션 키가 있는 쿼리를 선호합니다. |
| SDK 및 쿼리 옵션 | 직접 연결 같은 SDK 모범 사례를 따르고 클라이언트 쪽 쿼리 실행 옵션을 조정합니다. |
| 네트워크 대기 시간 | 측정에서 네트워크 오버 헤드를 고려하고 가장 가까운 지역에서 읽기 위해 멀티 호밍 API를 사용합니다. |
| 인덱싱 정책 | 쿼리에 필요한 인덱싱 경로/정책이 있는지 확인합니다. |
| 쿼리 실행 메트릭 | 쿼리 실행 메트릭을 분석하여 쿼리 및 데이터 셰이프의 재작성 가능성을 파악합니다.  |

### <a name="provisioned-throughput"></a>프로비전된 처리량
Cosmos DB에서 각각 초당 RU(요청 단위)로 표현된 예약된 처리량을 포함하는 데이터의 컨테이너를 만듭니다. 1KB 문서의 읽기는 1RU이고 쿼리를 포함한 모든 작업은 해당 복잡성에 따라 고정된 RU로 정규화됩니다. 예를 들어 사용자 컨테이너에 대해 프로비전된 1000 RU/s가 있고 5RU를 사용하는 `SELECT * FROM c WHERE c.city = 'Seattle'`과 같은 쿼리가 있는 경우 이러한 쿼리는 초당 (1000 RU/s) / (5 RU/query) = 200 query/s를 수행할 수 있습니다. 

초당 200개가 넘는 쿼리를 제출하는 경우 서비스는 200/s를 초과하는 들어오는 요청에 대해 속도 제한을 시작합니다. SDK는 백오프/재시도를 수행하여 이러한 사례를 처리하므로 이러한 쿼리에 대해서는 대기 시간이 더 높게 나타날 수 있습니다. 필요한 값에 대해 프로비전된 처리량을 높이면 쿼리 대기 시간 및 처리량이 향상됩니다. 

요청 단위에 대해 알아보려면 [요청 단위](request-units.md)를 참조하세요.

### <a name="partitioning-and-partition-keys"></a>분할 및 파티션 키
Azure Cosmos DB에서 쿼리는 일반적으로 가장 빠른/가장 효율적인 것부터 가장 느리고/덜 효율적인 순서로 수행합니다. 

* 단일 파티션 키 및 항목 키에 대해 GET 수행
* 단일 파티션 키에 대해 필터 절이 있는 쿼리
* 속성에 대해 같음 또는 범위 필터 절이 없는 쿼리
* 필터가 없는 쿼리

모든 파티션을 참조해야 하는 쿼리는 높은 대기 시간이 필요하며 더 높은 RU를 사용할 수 있습니다. 각 파티션에는 모든 속성에 대한 자동 인덱싱을 포함하므로 이 경우 인덱스에서 쿼리를 효율적으로 제공할 수 있습니다. 병렬 처리 옵션을 사용하여 파티션 간의 쿼리를 보다 신속하게 만들 수 있습니다.

분할 및 파티션 키에 대해 자세히 알아보려면 [Azure Cosmos DB의 분할](partition-data.md)을 참조하세요.

### <a name="sdk-and-query-options"></a>SDK 및 쿼리 옵션
Azure Cosmos DB에서 가장 좋은 클라이언트 쪽 성능을 얻는 방법은 [성능 팁](performance-tips.md) 및 [성능 테스트](performance-testing.md)를 참조하세요. 여기에는 최신 SDK 사용, 기본 연결 수와 같은 플랫폼별 구성, 가비지 수집 빈도 및 직접/TCP와 같은 경량의 연결 옵션 사용이 포함됩니다. 


#### <a name="max-item-count"></a>최대 항목 수
쿼리의 경우 `MaxItemCount`의 값은 종단 간 쿼리 시간에 상당한 영향을 미칠 수 있습니다. 서버에 대한 각 왕복은 `MaxItemCount`의 항목 수 이상을 반환하지 않습니다(기본값은 100개 항목). 이를 더 높은 값으로 설정하면(-1이 최대이자 권장됨) 서버와 클라이언트 간 왕복 횟수를 제한되어 쿼리 기간이 전체적으로 개선됩니다. 특히 대규모 결과 집합의 쿼리의 경우가 해당됩니다.

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxItemCount = -1, 
    }).AsDocumentQuery();
```

#### <a name="max-degree-of-parallelism"></a>병렬 처리의 최대 수준
쿼리의 경우 `MaxDegreeOfParallelism`을 조정하여 사용자 애플리케이션에 가장 적합한 구성을 식별합니다. 특히 파티션 키 값에 대한 필터 없이 파티션 간 쿼리를 수행하는 경우 해당됩니다. `MaxDegreeOfParallelism`은 병렬로 방문할 최대 파티션 수와 같이 최대 병렬 작업 수를 제어합니다. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        MaxDegreeOfParallelism = -1, 
        EnableCrossPartitionQuery = true 
    }).AsDocumentQuery();
```

다음을 가정해보세요.
* D = 기본 최대 병렬 작업 수(= 클라이언트 컴퓨터의 총 프로세서 수)
* P = 사용자 지정 최대 병렬 작업 수
* N = 쿼리 응답을 위해 방문해야 하는 파티션 수

다음은 병렬 쿼리가 P의 여러 다른 값에 대해 어떻게 작동하는지를 나타냅니다.
* (P == 0) => 직렬 모드
* (P == 1) => 한 작업의 최대
* (P > 1) => 최소(P, N) 병렬 작업 
* (P < 1) => 최소(N, D) 병렬 작업

SDK 릴리스 정보 및 구현된 클래스와 메서드에 대한 자세한 내용은 [SQL SDK](sql-api-sdk-dotnet.md)를 참조하세요.

### <a name="network-latency"></a>네트워크 대기 시간
글로벌 분포를 설정하고 가장 가까운 지역에 연결하는 방법은 [Azure Cosmos DB 글로벌 분포](tutorial-global-distribution-sql-api.md)를 참조하세요. 네트워크 대기 시간은 여러 왕복을 수행하거나 쿼리에서 큰 결과 집합을 검색해야 하는 경우 쿼리 성능에 상당한 영향을 미칩니다. 

쿼리 실행 메트릭에 관한 섹션은 쿼리의 서버 실행 시간을 검색하는 방법을 설명합니다(`totalExecutionTimeInMs`). 따라서 쿼리 실행에 걸린 시간과 네트워크 전송에 소요된 시간을 구별할 수 있습니다.

### <a name="indexing-policy"></a>인덱싱 정책
인덱싱 경로, 종류, 모드 및 쿼리 실행에 어떻게 영향을 주는지에 대한 내용은 [인덱싱 정책 구성](index-policy.md)을 참조하세요. 기본적으로 인덱싱 정책은 같음 쿼리에는 유효하지만 범위 쿼리/order by 쿼리에는 유효하지 않은 문자열용 해시 인덱싱을 사용합니다. 문자열에 대해 범위 쿼리가 필요한 경우 모든 문자열에 대해 범위 인덱스 유형을 지정하는 것이 좋습니다. 

기본적으로 Azure Cosmos DB 자동 인덱싱 모든 데이터에 적용 됩니다. 고성능을 위한 시나리오를 삽입, 이것은 각 삽입 작업에 대 한 RU 비용을 절감 하는 대로 경로 제외 하는 것이 좋습니다. 

## <a name="query-execution-metrics"></a>쿼리 실행 메트릭
선택 사항인 `x-ms-documentdb-populatequerymetrics` 헤더(.NET SDK에서 `FeedOptions.PopulateQueryMetrics`)에 전달하여 쿼리 실행에 대한 자세한 메트릭을 얻을 수 있습니다. `x-ms-documentdb-query-metrics`에서 반환된 값은 쿼리 실행의 고급 문제 해결을 위한 다음 키-값 쌍을 포함합니다. 

```cs
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
    "SELECT * FROM c WHERE c.city = 'Seattle'", 
    new FeedOptions 
    { 
        PopulateQueryMetrics = true, 
    }).AsDocumentQuery();

FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;

```

| 메트릭 | 단위 | 설명 | 
| ------ | -----| ----------- |
| `totalExecutionTimeInMs` | 밀리초 | 쿼리 실행 시간 | 
| `queryCompileTimeInMs` | 밀리초 | 쿼리 컴파일 시간  | 
| `queryLogicalPlanBuildTimeInMs` | 밀리초 | 논리 쿼리 계획을 작성하는 시간 | 
| `queryPhysicalPlanBuildTimeInMs` | 밀리초 | 실제 쿼리 계획을 작성하는 시간 | 
| `queryOptimizationTimeInMs` | 밀리초 | 쿼리 최적화에 소요된 시간 | 
| `VMExecutionTimeInMs` | 밀리초 | 쿼리 런타임에 소요된 시간 | 
| `indexLookupTimeInMs` | 밀리초 | 실제 인덱스 계층에 소요된 시간 | 
| `documentLoadTimeInMs` | 밀리초 | 문서를 로드하는 데 소요된 시간  | 
| `systemFunctionExecuteTimeInMs` | 밀리초 | 시스템(기본 제공) 함수를 실행하는 데 소요된 총 시간(밀리초)  | 
| `userFunctionExecuteTimeInMs` | 밀리초 | 사용자 정의 함수를 실행하는 데 소요된 총 시간(밀리초) | 
| `retrievedDocumentCount` | count | 검색된 총 문서 수  | 
| `retrievedDocumentSize` | 바이트 | 검색된 총 문서 크기(바이트)  | 
| `outputDocumentCount` | count | 출력 문서 수 | 
| `writeOutputTimeInMs` | 밀리초 | 쿼리 실행 시간(밀리초) | 
| `indexUtilizationRatio` | 비율(<=1) | 로드된 문서 수에 대한 필터에 의해 일치하는 문서 수의 비율  | 

클라이언트 SDK는 각 파티션 내부에 쿼리를 제공하기 위해 여러 쿼리 작업을 내부적으로 만들 수 있습니다. 클라이언트는 총 결과 수가 `x-ms-max-item-count`를 초과하거나, 쿼리가 파티션에 대해 프로비전된 처리량을 초과하거나, 쿼리 페이로드가 페이지당 최대 크기에 도달하거나, 쿼리가 시스템 할당된 시간 제한에 도달하는 경우 파티션당 두 개 이상의 호출을 수행합니다. 부분적 쿼리 실행은 각각 해당 페이지에 대해 `x-ms-documentdb-query-metrics`를 반환합니다. 

몇 가지 샘플 쿼리 및 쿼리 실행에서 반환된 일부 메트릭을 해석하는 방법은 다음과 같습니다. 

| 쿼리 | 샘플 메트릭 | 설명 | 
| ------ | -----| ----------- |
| `SELECT TOP 100 * FROM c` | `"RetrievedDocumentCount": 101` | 검색된 문서 수는 TOP 절에 맞게 100+1입니다. 쿼리 시간은 검색이므로 대부분 `WriteOutputTime` 및 `DocumentLoadTime`에서 소요됩니다. | 
| `SELECT TOP 500 * FROM c` | `"RetrievedDocumentCount": 501` | 이제 RetrievedDocumentCount가 더 높아집니다(TOP 절에 맞게 500+1). | 
| `SELECT * FROM c WHERE c.N = 55` | `"IndexLookupTime": "00:00:00.0009500"` | 키 조회를 위한 IndexLookupTime은 `/N/?`에 대한 인덱스 조회이므로 약 0.9ms가 소요됩니다. | 
| `SELECT * FROM c WHERE c.N > 55` | `"IndexLookupTime": "00:00:00.0017700"` | 범위 검색을 통한 IndexLookupTime에는 `/N/?`에 대한 인덱스 조회이므로 약간 더 많은 시간(1.7ms)이 소요됩니다. | 
| `SELECT TOP 500 c.N FROM c` | `"IndexLookupTime": "00:00:00.0017700"` | `DocumentLoadTime`에는 이전 쿼리와 같은 시간이 소요되지만 하나의 속성만 프로젝션하므로 `WriteOutputTime`은 더 짧습니다. | 
| `SELECT TOP 500 udf.toPercent(c.N) FROM c` | `"UserDefinedFunctionExecutionTime": "00:00:00.2136500"` | `c.N`의 각 값에서 UDF를 실행하는 `UserDefinedFunctionExecutionTime`에는 약 213ms가 소요됩니다. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(c.Name, 'Den')` | `"IndexLookupTime": "00:00:00.0006400", "SystemFunctionExecutionTime": "00:00:00.0074100"` | `/Name/?`의 `IndexLookupTime`에는 약 0.6ms가 소요됩니다. `SystemFunctionExecutionTime`에서 대부분의 쿼리 실행 시간은 약 7ms입니다. |
| `SELECT TOP 500 c.Name FROM c WHERE STARTSWITH(LOWER(c.Name), 'den')` | `"IndexLookupTime": "00:00:00", "RetrievedDocumentCount": 2491,  "OutputDocumentCount": 500` | 쿼리는 `LOWER`를 사용하므로 검색으로 수행되고 검색된 문서 2491개 중 500개가 반환됩니다. |


## <a name="next-steps"></a>다음 단계
* 지원되는 SQL 쿼리 연산자 및 키워드에 대해 알아보려면 [SQL 쿼리](how-to-sql-query.md)를 참조하세요. 
* 요청 단위에 대해 알아보려면 [요청 단위](request-units.md)를 참조하세요.
* 인덱싱 정책에 대해 알아보려면 [인덱싱 정책](index-policy.md)을 참조하세요. 


