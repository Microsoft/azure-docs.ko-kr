---
title: Azure Cosmos DB 모니터링 데이터 참조 | Microsoft Docs
description: Azure Cosmos DB의 모니터링 데이터에 대한 로그 및 메트릭 참조입니다.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.subservice: logs
ms.openlocfilehash: d131523e3031f55a818bb1919f39119bf073cb75
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456522"
---
# <a name="azure-cosmos-db-monitoring-data-reference"></a>Azure Cosmos DB 모니터링 데이터 참조
이 문서에서는 Azure Cosmos DB의 성능 및 가용성을 분석 하기 위해 수집 된 로그 및 메트릭 데이터에 대한 참조를 제공 합니다. Azure Cosmos DB 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [Cosmos DB 모니터링](monitor-cosmos-db.md) 을 참조 하세요.


## <a name="resource-logs"></a>리소스 로그
다음 표에서는 Azure Monitor 로그 또는 Azure Storage에서 수집 될 때 Azure Cosmos DB 리소스 로그의 속성을 나열 합니다. Azure Monitor 로그에서는 **ResourceProvider** 값이 MICROSOFT 인 **azurediagnostics** 테이블에서 수집 됩니다 *. DOCUMENTDB*. 

| Azure Storage 필드 또는 속성 | Azure Monitor Logs 속성 | 설명 |
| --- | --- | --- |
| **time** | **TimeGenerated** | 작업이 발생한 날짜 및 시간(UTC)입니다. |
| **resourceId** | **리소스** | 로그가 사용하도록 설정된 Azure Cosmos DB 계정입니다.|
| **category** | **범주** | Azure Cosmos DB 로그, **DataPlaneRequests**, **MongoRequests**, **queryruntimestatistics**, **파티션**, 파티션, **파티션**, **ControlPlaneRequests** 는 사용 가능한 로그 형식입니다. |
| **operationName** | **OperationName** | 작업의 이름입니다. 이 값은 Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed 또는 Upsert 작업 중 하나일 수 있습니다.   |
| **properties** | n/a | 이 필드의 내용은 다음 행에 설명되어 있습니다. |
| **activityId** | **activityId_g** | 기록된 작업의 고유 GUID입니다. |
| **userAgent** | **userAgent_s** | 요청을 수행하는 클라이언트 사용자 에이전트를 지정하는 문자열입니다. 형식은 {사용자 에이전트 이름}/{버전}입니다.|
| **requestResourceType** | **requestResourceType_s** | 액세스한 리소스 유형입니다. 이 값은 Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction 또는 Offer 같은 리소스 유형 중 하나일 수 있습니다. |
| **statusCode** | **statusCode_s** | 작업의 응답 상태입니다. |
| **requestResourceId** | **ResourceId** | 요청에 관련된 리소스 ID입니다. 값은 수행된 작업에 따라 databaseRid, collectionRid 또는 documentRid를 가리킬 수 있습니다.|
| **clientIpAddress** | **clientIpAddress_s** | 클라이언트의 IP 주소입니다. |
| **requestCharge** | **requestCharge_s** | 작업에서 사용된 RU 수입니다. |
| **collectionRid** | **collectionId_s** | 컬렉션의 고유 ID입니다.|
| **duration** | **duration_s** | 작업의 기간 (밀리초)입니다. |
| **requestLength** | **requestLength_s** | 요청 길이(바이트)입니다. |
| **responseLength** | **responseLength_s** | 길이 (바이트)에서 응답입니다.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | 이 값은 인증에 [리소스 토큰](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens)이 사용될 경우 비어 있지 않습니다. 값은 사용자의 리소스 ID를 가리킵니다. |

모든 Azure Monitor 로그 범주 및 관련 스키마에 대한 링크 목록은 [Azure Monitor Logs categories and 스키마](../azure-monitor/platform/diagnostic-logs-schema.md)를 참조 하세요. 

## <a name="metrics"></a>metrics
다음 표에서는 Azure CosmOS DB에 대해 수집 된 플랫폼 메트릭을 나열 합니다. 모든 메트릭은 **표준 메트릭에 Cosmos DB**네임 스페이스에 저장 됩니다.

모든 Azure Monitor 지원 메트릭 (CosmosDB 포함)의 목록은 [지원 되는 메트릭 Azure Monitor](../azure-monitor/platform/metrics-supported.md)을 참조 하세요. 

#### <a name="request-metrics"></a>요청 메트릭
            
|메트릭 (메트릭 표시 이름)|Unit (집계 형식) |설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| TotalRequests (총 요청) | 개수 (개수) | 요청 수| DatabaseName, CollectionName, 지역, StatusCode| 모두 | TotalRequests, Http 2xx, Http 3xx, Http 400, Http 401, Internal Server error, Service Unavailable, Throttled Requests, Average Requests per Second | 상태 코드의 요청을 분 단위로 모니터링 하는 데 사용 됩니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다. |
| MetadataRequests (메타 데이터 요청) |개수 (개수) | 메타데이터 요청 수. Azure Cosmos DB는 각 계정에 대한 시스템 메타 데이터 컨테이너를 유지 관리 하며,이를 통해 컬렉션, 데이터베이스 등의 기능 및 해당 구성을 무료로 열거할 수 있습니다. | DatabaseName, CollectionName, 지역, StatusCode| 모두| |메타데이터 요청으로 인한 제한을 모니터링하는 데 사용합니다.|
| MongoRequests (Mongo 요청) | 개수 (개수) | 생성된 Mongo 요청 수 | DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Rate, Mongo Update Request Rate, Mongo Delete Request Rate, Mongo Insert Request Rate, Mongo Count Request Rate| Mongo 요청 오류, 명령 유형별 사용량을 모니터링하는 데 사용합니다. |

#### <a name="request-unit-metrics"></a>요청 단위 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| MongoRequestCharge (Mongo 요청 요금) | 개수 (합계) |사용된 Mongo 요청 단위| DatabaseName, CollectionName, 지역, CommandName, ErrorCode| 모두 |Mongo Query Request Charge, Mongo Update Request Charge, Mongo Delete Request Charge, Mongo Insert Request Charge, Mongo Count Request Charge| Mongo 리소스 RU(분)를 모니터링하는 데 사용합니다.|
| TotalRequestUnits (총 요청 단위)| 개수 (합계) | 사용된 요청 단위| DatabaseName, CollectionName, 지역, StatusCode |모두| TotalRequestUnits| 분 단위로 총 RU 사용량을 모니터링하는 데 사용합니다. 초당 사용한 평균 RU를 가져오려면 합계 집계(분)를 사용한 후 60으로 나눕니다.|
| ProvisionedThroughput (프로 비전 된 처리량)| 개수 (최대값) |컨테이너 세분성에서 프로 비전 된 처리량| DatabaseName, ContainerName| 5M| | 컨테이너 당 프로 비전 된 처리량을 모니터링 하는 데 사용 됩니다.|

#### <a name="storage-metrics"></a>Storage 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|설명|차원| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---| ---|
| 사용 가능한 저장소 (사용 가능한 저장소) |바이트 (합계) | 지역별 5 분 단위로 보고 된 총 사용 가능한 저장소| DatabaseName, CollectionName, Region| 5M| 사용 가능한 스토리지| 사용 가능한 스토리지 용량을 모니터링하는 데 사용합니다(고정 스토리지 컬렉션에만 해당). 최소 단위는 5분이어야 합니다.| 
| DataUsage (데이터 사용) |바이트 (합계) |지역별 5 분 단위로 보고 된 총 데이터 사용량| DatabaseName, CollectionName, Region| 5M |데이터 크기 | 컨테이너 및 지역에서 총 데이터 사용량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|
| IndexUsage (인덱스 사용) | 바이트 (합계) |지역별 5 분 단위로 보고 된 총 인덱스 사용량| DatabaseName, CollectionName, Region| 5M| 인덱스 크기| 컨테이너 및 지역에서 총 데이터 사용량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다. |
| DocumentQuota (문서 할당량) | 바이트 (합계) | 지역별 5 분 단위로 보고 된 총 저장소 할당량입니다.| DatabaseName, CollectionName, Region| 5M |스토리지 용량| 컨테이너 및 지역에서 총 할당량을 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|
| DocumentCount (문서 수) | 개수 (합계) |지역별 5 분 단위로 보고 된 총 문서 수| DatabaseName, CollectionName, Region| 5M |문서 수|컨테이너 및 지역에서 문서 수를 모니터링 하는 데 사용 되는 최소 세분성은 5 분입니다.|

#### <a name="latency-metrics"></a>대기 시간 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|설명|차원| 시간 단위| 사용법 |
|---|---|---|---| ---| ---|
| ReplicationLatency (복제 대기 시간)| 밀리초 (최소, 최대, 평균) | 지역 사용 계정에 대한 원본 및 대상 지역의 P99 복제 대기 시간| SourceRegion, TargetRegion| 모두 | 지역에서 복제된 계정에 대한 두 지역 간 P99 복제 대기 시간을 모니터링하는 데 사용합니다. |


#### <a name="availability-metrics"></a>가용성 메트릭

|메트릭 (메트릭 표시 이름) |Unit (집계 형식)|설명| 시간 단위| 레거시 메트릭 매핑 | 사용법 |
|---|---|---|---| ---| ---|
| ServiceAvailability (서비스 가용성)| 백분율 (최소, 최대) | 1시간 단위의 계정 요청 가용성| 1H | 서비스 가용성 | 전달 된 총 요청 비율을 나타냅니다. 요청은 상태 코드가 410, 500 또는 503인경 우 시스템 오류로 인해 실패한 것으로 간주됩니다. 시간 단위로 계정의 가용성을 모니터링하는 데 사용합니다. |


#### <a name="cassandra-api-metrics"></a>Cassandra API 메트릭

|메트릭 (메트릭 표시 이름)|Unit (집계 형식)|설명|차원| 시간 단위| 사용법 |
|---|---|---|---| ---| ---|
| CassandraRequests (Cassandra 요청) | 개수 (개수) | 수행된 Cassandra API 요청 수| DatabaseName, CollectionName, ErrorCode, Region, OperationType, ResourceType| 모두| 분 단위로 Cassandra 요청을 모니터링하는 데 사용합니다. 초당 평균 요청 수를 가져오려면 개수 집계(분)를 사용한 후 60으로 나눕니다.|
| CassandraRequestCharges (Cassandra 요청 요금) | 개수 (합계, 최소값, 최대값, 평균) | Cassandra API 요청에 사용된 요청 단위| DatabaseName, CollectionName, Region, OperationType, ResourceType| 모두| Cassandra API 계정에서 분당 사용된 RU를 모니터링하는 데 사용합니다.|
| CassandraConnectionClosures (Cassandra 연결 클로저) |개수 (개수) |닫힌 Cassandra 연결 수| ClosureReason, Region| 모두 | 클라이언트와 Azure Cosmos DB Cassandra API 간 연결을 모니터링하는 데 사용합니다.|

## <a name="see-also"></a>참고 항목

- Azure Cosmos DB 모니터링에 대한 설명은 [monitoring Azure Cosmos DB](monitor-cosmos-db.md) 를 참조 하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용 하 여 azure 리소스 모니터링](../azure-monitor/insights/monitor-azure-resource.md) 을 참조 하세요.
