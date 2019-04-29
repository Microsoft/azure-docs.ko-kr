---
title: Azure Cosmos DB에서 요청 단위 및 쿼리 실행 비용 최적화
description: 쿼리의 요청 단위 요금을 평가하고 성능 및 비용 측면에서 쿼리를 최적화하는 방법을 알아봅니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 80c9cd91efd14e3d4b4214bde089f73692568f76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929201"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 쿼리 비용 최적화

Azure Cosmos DB는 컨테이너 내의 항목에 작동하는 관계형 쿼리 및 계층형 쿼리를 비롯한 다양한 데이터베이스 작업 세트를 제공합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신, 요청을 처리하기 위한 다양한 데이터베이스 작업을 수행하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다. 이 문서에서는 쿼리에 대한 요청 단위 요금을 평가하고 성능 및 비용 측면에서 쿼리를 최적화하는 방법을 설명합니다. 

Azure Cosmos DB의 쿼리는 일반적으로 처리량 측면에서 가장 빠르고/가장 효율적인 것부터 더 느리고/덜 효율적인 순서로 정렬됩니다.  

* 단일 파티션 키 및 항목 키에 대한 GET 작업

* 단일 파티션 키 내에 필터 절이 있는 쿼리

* 속성에 대해 같음 또는 범위 필터 절이 없는 쿼리

* 필터가 없는 쿼리

하나 이상의 파티션에서 데이터를 읽는 쿼리는 대기 시간이 더 길며 더 많은 수의 요청 단위를 사용합니다. 각 파티션에는 모든 속성에 대한 자동 인덱싱을 포함하므로 인덱스에서 쿼리를 효율적으로 제공할 수 있습니다. 병렬 처리 옵션을 사용하여 여러 파티션을 사용하는 쿼리를 보다 신속하게 만들 수 있습니다. 분할 및 파티션 키에 대해 자세히 알아보려면 [Azure Cosmos DB의 분할](partitioning-overview.md)을 참조하세요.

## <a name="evaluate-request-unit-charge-for-a-query"></a>쿼리의 요청 단위 요금 평가

일부 데이터를 Azure Cosmos 컨테이너에 저장한 경우 Azure Portal의 Data Explorer를 사용하여 쿼리를 생성하고 실행할 수 있습니다. 또한 Data Explorer를 사용하여 쿼리 비용을 확인할 수도 있습니다. 이 방법을 사용하면 시스템에서 지원하는 일반 쿼리 및 작업과 관련된 실제 요금을 파악할 수 있습니다.

또한 SDK를 사용하여 쿼리 비용을 프로그래밍 방식으로 확인할 수도 있습니다. 만들기, 업데이트 또는 삭제와 같은 작업의 오버헤드를 측정하려면 REST API를 사용하여 `x-ms-request-charge` 헤더를 조사합니다. .NET 또는 Java SDK를 사용 하는 경우는 `RequestCharge` 속성이 요청 요금을 가져올 해당 속성 및이 속성은 ResourceResponse 또는 FeedResponse 내에 존재 합니다.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>쿼리의 요청 단위 요금에 영향을 주는 요인

쿼리의 요청 단위는 여러 가지 요인에 따라 달라집니다. 쿼리 컴파일 인덱스에 대 한 조회 수 등 세부 정보를 예를 들어 시간 Azure Cosmos 항목 수가 로드/반환 합니다. Azure Cosmos DB는 동일한 데이터에 대해 실행될 경우 동일한 쿼리가 항상 반복 실행에서 동일한 요청 단위 수를 사용하도록 보장합니다. 쿼리 실행 메트릭을 사용하는 쿼리 프로필은 요청 단위가 소비되는 적절한 방식을 제시합니다.  

일부 경우에 페이징된 쿼리 실행에서 200개 및 429개의 응답 시퀀스와 가변 요청 단위를 볼 수 있습니다. 쿼리가 사용 가능한 RU에 따라 가능한 한 빠르게 실행되기 때문입니다. 쿼리 실행이 서버와 클라이언트 간에 여러 페이지/왕복으로 구분되는 것을 볼 수도 있습니다. 예를 들어, 10,000개 항목이 여러 페이지로 반환될 수 있으며, 해당 페이지에 대해 계산 기준으로 요금이 부과됩니다. 이러한 페이지에서 합계를 계산하는 경우 전체 쿼리의 경우와 동일한 수의 RU를 가져와야 합니다.  

## <a name="metrics-for-troubleshooting"></a>문제 해결을 위한 메트릭

성능과 쿼리 및 UDF(사용자 정의 함수)에서 소비하는 처리량은 주로 함수 본문에 따라 달라집니다. UDF에서 쿼리 실행에 사용되는 시간 및 소비되는 RU 수를 확인하는 가장 쉬운 방법은 쿼리 메트릭을 사용하도록 설정하는 것입니다. .NET SDK를 사용 하는 경우 SDK에서 반환 되는 샘플 쿼리 메트릭은 다음과 같습니다.

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>쿼리 최적화 비용에 대한 모범 사례 

비용에 대한 쿼리를 최적화하는 경우 다음 모범 사례를 고려합니다.

* **여러 엔터티 형식을 공동으로 배치**

   단일 컨테이너 또는 소수의 컨테이너 내에 여러 엔터티 형식을 공동으로 배치합니다. 이 방법은 가격 책정 관점에서 뿐만 아니라 쿼리 실행 및 트랜잭션 측면에서도 이점이 있습니다. 쿼리의 범위는 단일 컨테이너로 지정되고, 저장 프로시저/트리거를 통해 여러 레코드에서 수행되는 원자성 트랜잭션의 범위는 단일 컨테이너 내의 파티션 키로 지정됩니다. 같은 컨테이너 내에서 엔터티를 공동으로 배치하면 레코드 간 관계를 해석하는 데 필요한 네트워크 왕복 수를 줄일 수 있습니다. 이를 통해 엔드-투-엔드 성능이 향상되고, 더 큰 데이터 세트의 여러 레코드에 대해 원자성 트랜잭션이 활성화되며 결과적으로 비용이 절감됩니다. 일반적으로 기존 애플리케이션을 마이그레이션하며 코드를 변경하고 싶지 않기 때문에 단일 컨테이너 또는 더 작은 수의 컨테이너에 여러 엔터티 형식을 공동으로 배치하는 것이 어려우면 데이터베이스 수준에서 처리량을 프로비전하는 것이 좋습니다.  

* **낮은 요청 단위/초 사용량 측정 및 튜닝**

   쿼리의 복잡성은 작업에 사용되는 RU(요청 단위)의 수에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 세트의 크기 등의 모든 요소는 쿼리 작업의 비용에 영향을 줍니다. 

   요청 헤더에 반환된 요청 비용은 지정된 쿼리의 비용을 나타냅니다. 예를 들어, 쿼리가 1000개의 1KB 항목을 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md) 문서와 요청 단위 계산기를 참조하세요. 

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [Azure Cosmos 가격 책정 방식](how-pricing-works.md)에 대한 자세한 정보
* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
* [Azure Cosmos DB 예약 용량](cosmos-db-reserved-capacity.md)에 대한 자세한 정보

