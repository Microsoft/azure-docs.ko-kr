---
title: MongoDB 용 Azure Cosmos DB API로 데이터를 마이그레이션하기 위한 마이그레이션 전 단계
description: 이 문서에서는 MongoDB에서 Cosmos DB로 데이터를 마이그레이션하기 위한 필수 구성 요소에 대한 개요를 제공 합니다.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: lbosq
ms.openlocfilehash: 73ac1a6ffd5fc2b2d52f169e1e0332044638f9f7
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942088"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대한 MongoDB에서 Azure Cosmos DB의 API로 데이터 마이그레이션을 위한 마이그레이션 전 단계

MongoDB (온-프레미스 또는 클라우드)에서 MongoDB의 API Azure Cosmos DB로 데이터를 마이그레이션하기 전에 다음을 수행 해야 합니다.

1. [MongoDB에 대한 Azure Cosmos DB API 사용에 대한 주요 고려 사항을 읽으십시오.](#considerations)
2. [데이터를 마이그레이션하는 옵션 선택](#options)
3. [워크 로드에 필요한 처리량 예측](#estimate-throughput)
4. [데이터의 최적 파티션 키 선택](#partitioning)
5. [데이터에 대해 설정할 수 있는 인덱싱 정책 이해](#indexing)

위의 마이그레이션 필수 구성 요소를 이미 완료 한 경우에 [는 Azure Database Migration Service를 사용 하 여 MongoDB 데이터 Azure Cosmos DB를 MONGODB API로 마이그레이션할](../dms/tutorial-mongodb-cosmos-db.md)수 있습니다. 또한 계정을 만들지 않은 경우 계정을 만드는 단계를 [보여 주는 빠른 시작을 찾아볼](create-mongodb-dotnet.md) 수 있습니다.

## <a id="considerations"></a>MongoDB에 대한 Azure Cosmos DB API 사용 시 고려 사항

다음은 Azure Cosmos DB의 MongoDB API에 대한 특정 특성입니다.

- **용량 모델**: Azure Cosmos DB의 데이터베이스 용량은 처리량 기반 모델을 기반으로 합니다. 이 모델은 초 단위로 컬렉션에 대해 실행할 수 있는 데이터베이스 작업의 수를 나타내는 단위인 [초당 요청 단위](request-units.md)를 기반으로 합니다. 이 용량은 [데이터베이스 또는 컬렉션 수준](set-throughput.md)에서 할당 될 수 있으며, 할당 모델에서 프로 비전 하거나 [AutoPilot 모델](provision-throughput-autopilot.md)을 사용 하 여 프로 비전 할 수 있습니다.

- **요청 단위**: 모든 데이터베이스 작업에는 Azure Cosmos DB 관련 된 RUs (요청 단위) 비용이 포함 됩니다. 이를 실행 하면 지정 된 초에 사용 가능한 요청 단위 수준에서 뺍니다. 요청에 현재 할당 된 r u/초 보다 많은 RUs가 필요한 경우 문제를 해결 하는 두 가지 옵션이 있습니다. RUs의 용량을 늘리거나 다음 초가 시작 될 때까지 기다린 후 작업을 다시 시도 하세요.

- **탄력적 용량**: 지정 된 컬렉션 또는 데이터베이스에 대한 용량은 언제 든 지 변경할 수 있습니다. 이를 통해 데이터베이스는 워크 로드의 처리량 요구 사항에 맞게 탄력적으로 수 있습니다.

- **자동 분할**: Azure Cosmos DB 분할 (또는 파티션 키)만 필요한 자동 분할 시스템을 제공 합니다. [자동 분할 메커니즘](partition-data.md) 은 모든 Azure Cosmos DB api에서 공유 되며, 원활한 데이터를 허용 하 고 수평 분산을 통해 전체 확장 될 수 있습니다.

## <a id="options"></a>MongoDB 용 Azure Cosmos DB API에 대한 마이그레이션 옵션

[MongoDB 용 AZURE COSMOS DB API에 대한 Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 는 완전히 관리 되는 호스팅 플랫폼, 마이그레이션 모니터링 옵션 및 자동 제한 처리를 제공 하 여 데이터 마이그레이션을 간소화 하는 메커니즘을 제공 합니다. 옵션의 전체 목록은 다음과 같습니다.

|**마이그레이션 유형**|**해결 방법**|**고려 사항**|
|---------|---------|---------|
|오프라인|[데이터 마이그레이션 도구](https://docs.microsoft.com/azure/cosmos-db/import-data)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>&bull;는 대량 데이터 집합에 적합 하지 않습니다.|
|오프라인|[Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-cosmos-db)|&bull; 쉽게 설정 하 고 여러 소스를 지원 합니다. <br/>&bull;에서 Azure Cosmos DB bulk executor 라이브러리를 사용 합니다. <br/>대량 데이터 집합에 적합 한 &bull; <br/>검사점 부족 &bull; 마이그레이션 과정 중에 발생 하는 모든 문제에 대해 전체 마이그레이션 프로세스를 다시 시작 해야 함을 의미 합니다.<br/>배달 못 한 편지 큐가 없으면 몇 개의 잘못 된 파일이 전체 마이그레이션 프로세스를 중지할 수 있습니다. &bull; <br/>특정 데이터 원본에 대한 읽기 처리량을 늘리려면 사용자 지정 코드가 필요 &bull;|
|오프라인|[기존 Mongo 도구 (mongodump, mongorestore, Studio3T)](https://azure.microsoft.com/resources/videos/using-mongodb-tools-with-azure-cosmos-db/)|&bull; 쉽게 설정 하 고 통합할 수 있습니다. <br/>제한에 대한 사용자 지정 처리를 요구 하 &bull;|
|온라인|[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db-online.md)|&bull; 완전히 관리 되는 마이그레이션 서비스입니다.<br/>&bull;는 마이그레이션 작업에 대한 호스팅 및 모니터링 솔루션을 제공 합니다. <br/>대량 데이터 집합에 적합 하며 라이브 변경 내용 복제를 처리 하는 &bull; <br/>&bull;는 다른 MongoDB 원본 에서만 작동 합니다.|


## <a id="estimate-throughput"></a>워크 로드에 필요한 처리량 예상

Azure Cosmos DB 처리량은 사전에 프로 비전 되며 초당 (요청 단위)로 측정 됩니다. Vm 또는 온-프레미스 서버와 달리 RUs는 언제 든 지 쉽게 확장 및 축소할 수 있습니다. 프로 비전 된 RUs의 수를 즉시 변경할 수 있습니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

[Azure Cosmos DB 용량 계산기](https://cosmos.azure.com/capacitycalculator/) 를 사용 하 여 데이터베이스 계정 구성, 데이터 양, 문서 크기, 초당 필요한 읽기 및 쓰기 수를 기반으로 하는 요청 단위 크기를 결정할 수 있습니다.

다음은 필수 RUs의 수에 영향을 주는 주요 요소입니다.
- **문서 크기**: 항목/문서의 크기가 늘어나면 항목/문서를 읽거나 쓰는 데 사용 된 RUs의 수도 늘어납니다.

- **문서 속성 수**: 문서를 만들거나 업데이트 하는 데 사용 된 RUs의 수는 해당 속성의 수, 복잡성 및 길이와 관련이 있습니다. [인덱싱된 속성 수를 제한](mongodb-indexing.md)하 여 쓰기 작업에 대한 요청 단위 사용량을 줄일 수 있습니다.

- **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용 되는 요청 단위 수에 영향을 줍니다. 

쿼리 비용을 이해 하는 가장 좋은 방법은 Azure Cosmos DB에서 샘플 데이터를 사용 하 고 `getLastRequestStastistics` 명령을 사용 하 여 [MongoDB 셸에서 샘플 쿼리를 실행](connect-mongodb-account.md) 하 여 요청 요금을 가져오는 것입니다. 그러면 사용 된 RUs 수가 출력 됩니다.

`db.runCommand({getLastRequestStatistics: 1})`

이 명령은 다음과 유사한 JSON 문서를 출력 합니다.

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

[진단 설정을](cosmosdb-monitor-resource-logs.md) 사용 하 여 Azure Cosmos DB에 대해 실행 되는 쿼리의 빈도와 패턴을 파악할 수도 있습니다. 진단 로그의 결과를 저장소 계정, EventHub 인스턴스 또는 [Azure Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)로 보낼 수 있습니다.  

## <a id="partitioning"></a>파티션 키 선택
분할 (분할이 라고도 함)은 데이터를 마이그레이션하기 전에 고려해 야 할 주요 요소입니다. Azure Cosmos DB는 완전히 관리 되는 분할을 사용 하 여 저장소 및 처리량 요구 사항에 맞게 데이터베이스의 용량을 늘립니다. 이 기능에는 라우팅 서버를 호스팅하거나 구성할 필요가 없습니다.   

이와 비슷한 방식으로 분할 기능이 자동으로 용량을 추가 하 고 그에 따라 데이터의 균형을 다시 조정 합니다. 데이터의 올바른 파티션 키를 선택 하는 방법에 대한 자세한 내용과 권장 사항은 [파티션 키 선택 문서](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)를 참조 하세요. 

## <a id="indexing"></a>데이터 인덱싱
기본적으로 Azure Cosmos DB는 삽입 된 모든 데이터에 대한 자동 인덱싱을 제공 합니다. Azure Cosmos DB에서 제공 하는 인덱싱 기능에는 복합 인덱스, 고유 인덱스 및 TTL (time-to-live) 인덱스를 추가 하는 작업이 포함 됩니다. 인덱스 관리 인터페이스는 `createIndex()` 명령에 매핑됩니다. [MongoDB에 대한 AZURE COSMOS DB API의 인덱싱을](mongodb-indexing.md)자세히 알아보세요.

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 는 고유 인덱스를 사용 하 여 MongoDB 컬렉션을 자동으로 마이그레이션합니다. 그러나 마이그레이션 전에 고유 인덱스를 만들어야 합니다. 컬렉션에 이미 데이터가 있는 경우 Azure Cosmos DB는 고유 인덱스 생성을 지원 하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Database Migration Service를 사용 하 여 MongoDB 데이터를 Cosmos DB으로 마이그레이션합니다.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 컨테이너 및 데이터베이스에 대한 처리량 프로 비전](set-throughput.md)
* [Azure Cosmos DB에서 분할](partition-data.md)
* [Azure Cosmos DB의 전역 배포](distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
