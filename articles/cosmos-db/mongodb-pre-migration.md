---
title: 마이그레이션 전 단계 데이터 마이그레이션에 대 한 MongoDB에서 Azure Cosmos DB의 MongoDB 용 API
description: 이 문서는 Cosmos DB에 MongoDB에서 데이터 마이그레이션 위한 필수 구성 요소 개요를 제공합니다.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: roaror
ms.openlocfilehash: 476a143555323bbb5058541000a5b1a26d23b71a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330867"
---
# <a name="pre-migration-steps-for-data-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>마이그레이션 전 단계 데이터 마이그레이션에 대 한 MongoDB에서 Azure Cosmos DB의 MongoDB 용 API

MongoDB에서 데이터를 마이그레이션하기 전에 (온-프레미스 또는 클라우드 (IaaS)) MongoDB에 대 한 Azure Cosmos DB API에 수행 해야 합니다.

1. [Azure Cosmos DB 계정 만들기](#create-account)
2. [워크 로드에 필요한 처리량을 추정](#estimate-throughput)
3. [데이터에 대 한 최적의 파티션 키를 선택 합니다.](#partitioning)
4. [데이터에 대해 설정할 수 있는 인덱싱 정책을 이해합니다](#indexing)

이미 마이그레이션에 대 한 위의 필수 조건을 완료를 표시 합니다 [MongoDB 마이그레이션 데이터를 Azure Cosmos DB의 MongoDB 용 API](../dms/tutorial-mongodb-cosmos-db.md) 실제 데이터 마이그레이션 지침. 그렇지 않은 경우이 문서에서는 이러한 필수 구성 요소를 처리 하는 지침을 제공 합니다. 

## <a id="create-account"></a> Azure Cosmos DB 계정 만들기 

마이그레이션을 시작 하기 전에 해야 [MongoDB에 대 한 Azure Cosmos DB API를 사용 하 여 Azure Cosmos 계정을 만들고](create-mongodb-dotnet.md)합니다. 

계정 만들기에 대 한 설정을 선택할 수 있습니다 [전역적으로 배포](distribute-data-globally.md) 데이터입니다. 다중 지역 쓰기 사용 (또는 다중 마스터 구성) 하는 옵션을 모두 쓰기를 읽기 지역에 지역 각각 수도 있습니다.

![계정 만들기](./media/mongodb-pre-migration/account-creation.png)

## <a id="estimate-throughput"></a> 워크 로드에 대 한 처리량 요구를 예측 합니다.

사용 하 여 마이그레이션을 시작 하기 전에 합니다 [DMS Database Migration Service ()](../dms/dms-overview.md), Azure Cosmos 데이터베이스 및 컬렉션에 대 한 프로 비전 하는 처리량을 예측 해야 합니다.

처리량을 프로비저닝할 수 있습니다.

- 컬렉션

- 데이터베이스

> [!NOTE]
> 위 값의 조합 수 있습니다, 그리고 일부 컬렉션 데이터베이스에 할당 될 수 있는 처리량을 프로 비전 및 다른 처리량을 공유할 수 있습니다. 세부 정보를 참조 하십시오 합니다 [데이터베이스 및 컨테이너의 처리량 설정](set-throughput.md) 페이지입니다.
>

먼저 데이터베이스 또는 둘의 조합 컬렉션 수준 처리량을 프로 비전 할 것인지 여부를 결정 해야 합니다. 일반적으로 컬렉션 수준에서 전용된 처리량을 구성 하는 것이 좋습니다. 데이터베이스 수준에서 프로 비전 처리량 프로 비전된 된 처리량을 공유 하 여 데이터베이스의 컬렉션을 수 있습니다. 그러나 공유 처리량을 사용 하 여 각 개별 컬렉션에 특정 처리량에 대 한 보장이 및 특정 컬렉션에 예측 가능한 성능을 얻지 합니다.

각 개별 컬렉션에 얼마나 많은 처리량 전용 사용 해야 하는 방법에 대 한 확실 하지 않은, 경우에 데이터베이스 수준 처리량을 선택할 수 있습니다. 생각할 수 있습니다 프로 비전된 된 처리량의 MongoDB VM 또는 물리적 서버에서의 계산 용량을 해당 하는 논리적으로 Azure Cosmos 데이터베이스에서 구성 되었지만 효율적인 탄력적으로 확장할 수 있습니다. 자세한 내용은 [Azure Cosmos 컨테이너 및 데이터베이스에 대 한 프로 비전 처리량](set-throughput.md)합니다.

데이터베이스 수준에서 처리량을 프로 비전 하는 경우 해당 데이터베이스 내에서 만든 모든 컬렉션 파티션/분할 키를 사용 하 여 만들어야 합니다. 분할에 대 한 자세한 내용은 참조 하세요. [분할 및 Azure Cosmos DB에서 수평](partition-data.md)합니다. Azure Database Migration Service를 사용 하 여 분할 된 데이터베이스 키 필드를 자동으로 채웁니다 마이그레이션하는 동안 파티션/분할 키를 지정 하지 않으면 경우는 *_id* 각 문서에 대해 자동으로 생성 되는 특성.

### <a name="optimal-number-of-request-units-rus-to-provision"></a>프로 비전 할 요청 단위 (Ru) 최적의 수

Azure Cosmos db에서 처리량을 미리 프로 비전 되 고 초당 요청 단위 (RU) 단위로 측정 됩니다. 워크 로드 VM 또는 온-프레미스 MongoDB를 실행 하는 경우 생각할 RU의 물리적 리소스에 대 한 간단한 추상화와 같은 VM 또는 프레미스는 서버 및 예를 들어 소유 리소스의 크기에 대 한 메모리, CPU, IOPs. 

Vm 또는 온-프레미스 서버와는 달리 Ru는 언제 든 지 확장 및 축소할 쉽습니다. 초 내에 프로 비전 된 ru 수를 변경할 수 및 최대 수가 지정 된 1 시간 동안 프로 비전 하는 Ru에 대해서만 요금이 청구 됩니다. 자세한 내용은 [Azure Cosmos DB의 요청 단위](request-units.md)를 참조하세요.

다음은 필요한 Ru 수가 영향을 주는 주요 요소입니다.
- **항목 (즉, 문서) 크기**: 항목/문서의 크기 증가, Ru 수입니다. 읽기 또는 쓰기 항목/문서도 증가를 사용 합니다.
- **항목 속성 개수**: 가정 합니다 [기본 인덱싱을](index-overview.md) 항목 속성 수가 증가 함에 따라 증가 하는 항목을 작성 하는 데 사용 된 Ru 수입니다. 모든 속성에 대해 합니다. 쓰기 작업에 대 한 요청 단위 소비를 줄일 수 있습니다 [인덱싱된 속성 수를 제한](index-policy.md)합니다.
- **동시 작업**: 사용 된 단위에 따라 달라 집니다 (예: 쓰기, 읽기, 업데이트, 삭제)는 다양 한 CRUD 작업 빈도 및 더 복잡 한 쿼리를 실행을 요청 합니다. 사용할 수 있습니다 [mongostat](https://docs.mongodb.com/manual/reference/program/mongostat/) 현재 MongoDB 데이터 동시성 요구를 출력 합니다.
- **쿼리 패턴**: 쿼리의 복잡성은 쿼리에서 사용 되는 요청 단위의 영향을 줍니다.

사용 하 여 JSON 파일을 내보내면 [하면 mongoexport](https://docs.mongodb.com/manual/reference/program/mongoexport/) 얼마나 많은 쓰기, 읽기, 업데이트 및 삭제 발생 하는 초당 이해, 사용할 수는 [Azure Cosmos DB는 capacity planner](https://www.documentdb.com/capacityplanner) 예측 하는 초기 프로 비전 할 Ru 수입니다. Capacity planner 더 복잡 한 쿼리 비용에 영향 받지 않습니다. 따라서 복잡 한 쿼리 데이터에 있는 경우 추가 Ru 사용 됩니다. 계산기는 또한 모든 필드 인덱싱되지 않고 세션 일관성에 사용 되는 가정 합니다. 쿼리의 비용을 Azure Cosmos DB로 데이터 (또는 샘플 데이터)를 마이그레이션하는 것을 이해 하는 가장 좋은 방법은 [Cosmos DB의 끝점에 연결할](connect-mongodb-account.md) 사용 하 여 MongoDB 셸에서 샘플 쿼리를 실행 합니다 `getLastRequestStastistics` 가져오려면 명령을 요청 요금-Ru 수를 출력 합니다.

`db.runCommand({getLastRequestStatistics: 1})`

이 명령은 다음과 유사한 JSON 문서가 출력 됩니다.

```{  "_t": "GetRequestStatisticsResponse",  "ok": 1,  "CommandName": "find",  "RequestCharge": 10.1,  "RequestDurationInMilliSeconds": 7.2}```

쿼리에서 사용 되는 Ru 수를 이해 하 고 해당 쿼리에 대 한 동시성 요구를 프로 비전 된 Ru 수를 조정할 수 있습니다. 일회성 이벤트 아닙니다 Ru 최적화-지속적으로 최적화 하거나 있는지에 따라 됩니다 하지 작업량이 달리 사용될지를 예상 데이터를 가져오는 프로 비전 된 Ru를 확장 해야 합니다.

## <a id="partitioning"></a>파티션 키를 선택 합니다.
분할은 Azure Cosmos DB와 같은 전역적으로 분산된 된 데이터베이스를 마이그레이션하기 전에 고려해 야의 핵심입니다. Azure Cosmos DB를 사용 하 여 분할 응용 프로그램의 확장성 및 성능 요구 사항에 맞게 데이터베이스의 개별 컨테이너를 확장 합니다. 분할에서 컨테이너의 항목을 논리적 파티션 이라는 고유 하위 집합으로 구분 됩니다. 세부 정보 및 권장 사항 데이터에 대 한 올바른 파티션 키를 선택 하십시오 합니다 [파티션 키 섹션을 선택](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)합니다. 

## <a id="indexing"></a>데이터 인덱스
기본적으로 Azure Cosmos DB는 수집 시 모든 데이터 필드를 인덱싱합니다. 수정할 수는 [인덱싱 정책](index-policy.md) 언제 든 지 Azure Cosmos DB에서. 실제로 데이터를 마이그레이션하는 경우 인덱싱을 해제 하 여 다음 다시 설정할 경우 데이터가 이미 Cosmos DB에서 좋습니다 경우가 많습니다. 인덱싱에 대 한 자세한 내용은 대 한 자세한 내용은 합니다 [Azure Cosmos DB의 인덱싱은](index-overview.md) 섹션. 

[Azure Database Migration Service](../dms/tutorial-mongodb-cosmos-db.md) 고유 인덱스를 사용 하 여 MongoDB 컬렉션을 자동으로 마이그레이션합니다. 그러나 마이그레이션 전에 고유 인덱스를 만들어야 합니다. Azure Cosmos DB는 이미 있을 때 데이터 컬렉션에서 고유 인덱스 만들기를 지원 하지 않습니다. 자세한 내용은 [Azure Cosmos DB의 고유 키](unique-keys.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Database Migration Service를 사용 하 여 Cosmos DB로 MongoDB 데이터를 마이그레이션하세요.](../dms/tutorial-mongodb-cosmos-db.md) 
* [Azure Cosmos 컨테이너 및 데이터베이스에 대 한 프로 비전 처리량](set-throughput.md)
* [Azure Cosmos DB에서 분할](partition-data.md)
* [Azure Cosmos DB의에서 전역 배포](distribute-data-globally.md)
* [Azure Cosmos DB의 인덱싱](index-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
