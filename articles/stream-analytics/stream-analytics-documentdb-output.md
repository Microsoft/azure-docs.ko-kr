---
title: Azure Cosmos DB에 Azure Stream Analytics 출력
description: 이 문서에서는 비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 위해 Azure Stream Analytics를 사용하여 JSON 출력에 대한 Azure Cosmos DB에 출력을 저장하는 방법을 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: dbeb1305a64fcace0be527708bc9122a4ffb931d
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870836"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Cosmos DB에 Azure Stream Analytics 출력  
비구조화된 JSON 데이터에 대한 데이터 보관 및 짧은 대기 시간 쿼리를 사용하기 위해 Azure Stream Analytics에서 JSON 출력의 대상을 [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/)로 지정할 수 있습니다. 이 문서에서는 이 구성을 구현하기 위한 몇 가지 모범 사례를 설명합니다.

Azure Cosmos DB에 익숙하지 않은 상태에서 시작하려면 [Azure Cosmos DB 설명서](https://docs.microsoft.com/azure/cosmos-db/)를 참조하세요. 

> [!Note]
> 현재 Stream Analytics는 *SQL API*를 통해서만 Azure Cosmos DB에 연결하도록 지원합니다.
> 다른 Azure Cosmos DB API는 아직 지원되지 않습니다. Stream Analytics를 다른 API로 만든 Azure Cosmos DB 계정에 지정한 경우 데이터는 올바르게 저장되지 않을 수도 있습니다. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>출력 대상으로서 Azure Cosmos DB의 기본 사항
Stream Analytics의 Azure Cosmos DB 출력을 사용하면 스트림 처리 결과를 Azure Cosmos DB 컨테이너에 JSON 출력으로 쓸 수 있습니다. 

Stream Analytics는 데이터베이스에 컨테이너를 만들지 않습니다. 대신, 미리 만들어야 합니다. 그런 다음, Azure Cosmos DB 컨테이너의 청구 비용을 제어할 수 있습니다. [Azure Cosmos DB API](https://msdn.microsoft.com/library/azure/dn781481.aspx)를 사용하여 컨테이너의 성능, 일관성 및 용량을 직접 조정할 수도 있습니다.

> [!Note]
> Azure Cosmos DB 방화벽에서 허용되는 IP 목록에 0.0.0.0을 추가해야 합니다.

다음 섹션에서는 Azure Cosmos DB에 대한 몇 가지 컨테이너 옵션을 자세히 설명합니다.

## <a name="tuning-consistency-availability-and-latency"></a>일관성, 가용성 및 대기 시간 조정
Azure Cosmos DB를 사용하면 애플리케이션 요구 사항에 맞게 데이터베이스 및 컨테이너를 미세 조정하고, 일관성, 가용성, 대기 시간 및 처리량 간에 균형을 유지할 수 있습니다. 

시나리오에서 읽기 및 쓰기 대기 시간에 대해 필요로 하는 읽기 일관성 수준에 따라 데이터베이스 계정에 대한 일관성 수준을 선택할 수 있습니다. 컨테이너에서 RU(요청 단위)를 확장하여 처리량을 향상시킬 수 있습니다. 

또한 기본적으로 Azure Cosmos DB는 컨테이너에 대한 각 CRUD 작업에서 동기 인덱싱을 지원합니다. 이는 Azure Cosmos DB에서 쓰기/읽기 성능을 제어하는 또 다른 유용한 옵션입니다. 

자세한 내용은 [데이터베이스 및 쿼리 일관성 수준 변경](../cosmos-db/consistency-levels.md) 문서를 검토하세요.

## <a name="upserts-from-stream-analytics"></a>Stream Analytics에서 Upsert
Stream Analytics를 Azure Cosmos DB와 통합하면 지정된 **문서 ID** 열에 따라 컨테이너에 레코드를 삽입하거나 업데이트할 수 있습니다. 이를 *upsert*라고도 합니다.

Stream Analytics는 낙관적 upsert 방법을 사용합니다. 문서 ID 충돌로 인해 삽입이 실패할 경우에만 업데이트가 수행됩니다. 

호환성 수준 1.0에서는 Stream Analytics가 업데이트를 패치 작업으로 수행하므로 문서를 부분적으로 업데이트할 수 있습니다. Stream Analytics는 새 속성을 추가하거나 기존 속성을 증분 방식으로 바꿉니다. 그러나 JSON 문서에서 배열 속성 값을 변경하면 전체 배열을 덮어씁니다. 즉, 배열이 병합되지 않습니다. 

1\.2를 사용하는 경우 문서를 삽입하거나 바꾸도록 upsert 동작이 수정됩니다. 호환성 수준 1.2에 대한 이후 섹션에서는 이 동작을 자세히 설명합니다.

들어오는 JSON 문서에 기존 ID 필드가 있는 경우 해당 필드는 Azure Cosmos DB에서 자동으로 **문서 ID** 열로 사용됩니다. 후속 쓰기는 이에 따라 처리되고 다음과 같은 상황 중 하나로 이어집니다.

- 고유한 ID가 삽입으로 이어집니다.
- 중복 ID 및 **ID**로 설정된 **문서 ID**가 upsert로 이어집니다.
- 첫 문서 이후 중복 ID 및 설정되지 않은 **문서 ID**가 오류로 이어집니다.

중복 ID가 있는 문서를 포함하여 *모든* 문서를 저장하려면 **AS** 키워드로 사용하여 쿼리에서 ID 필드의 이름을 바꿉니다. **AS** 키워드를 사용하거나 **문서 ID** 설정을 사용하여 Azure Cosmos DB에서 ID 필드를 만들거나 ID를 다른 열 값으로 바꾸도록 합니다.

## <a name="data-partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 분할
Azure Cosmos DB는 워크로드에 따라 파티션 크기를 자동으로 조정합니다. 따라서 데이터를 분할하는 방법으로 [무제한](../cosmos-db/partition-data.md) 컨테이너를 사용하는 것이 좋습니다. Stream Analytics는 무제한 컨테이너에 쓸 때 이전 쿼리 단계 또는 입력 분할 구성표만큼 많은 병렬 기록기를 사용합니다.

> [!NOTE]
> Azure Stream Analytics는 최상위 수준에서 파티션 키를 사용하여 무제한 컨테이너만 지원합니다. 예를 들어 `/region`이 지원됩니다. 중첩된 파티션 키(예: `/region/name`)는 지원되지 않습니다. 

선택한 파티션 키에 따라 다음과 같은 _경고_가 표시될 수 있습니다.

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

여러 고유 값을 갖는 파티션 키 속성을 선택하여 이러한 값에서 워크로드를 균일하게 분산하도록 하는 것이 중요합니다. 자연스러운 분할 아티팩트로서, 동일한 파티션 키를 포함하는 요청이 단일 파티션의 최대 처리량으로 제한됩니다. 

동일한 파티션 키에 속하는 문서의 스토리지 크기는 20GB로 제한됩니다. 이상적인 파티션 키는 쿼리에서 자주 필터로 표시되며 솔루션 확장에 충분한 카디널리티가 있는 키입니다.

파티션 키는 Azure Cosmos DB의 저장 프로시저 및 트리거에서 트랜잭션에 대한 경계이기도 합니다. 트랜잭션에서 함께 발생하는 문서가 동일한 파티션 키 값을 공유하도록 파티션 키를 선택해야 합니다. [Azure Cosmos DB의 분할](../cosmos-db/partitioning-overview.md) 문서에서는 파티션 키 선택에 대한 자세한 정보를 제공합니다.

고정된 Azure Cosmos DB 컨테이너의 경우, Stream Analytics에서는 컨테이너가 꽉 찰 때 강화하거나 스케일 아웃할 방법이 없습니다. 10GB 및 10,000RU/s의 처리량 상한 값이 적용됩니다. 고정 컨테이너에서 무제한 컨테이너(최소 1,000RU/s 처리량 및 파티션 키가 있는 하나의 컨테이너)로 데이터를 마이그레이션하려면 [데이터 마이그레이션 도구](../cosmos-db/import-data.md) 또는 [변경 피드 라이브러리](../cosmos-db/change-feed.md)를 사용합니다.

여러 고정 컨테이너에 쓰는 기능은 더 이상 사용되지 않습니다. Stream Analytics 작업을 스케일 아웃하는 것은 권장되지 않습니다.

## <a name="improved-throughput-with-compatibility-level-12"></a>호환성 수준이 1.2인 향상된 처리량
호환성 수준 1.2를 사용하면 Stream Analytics는 Azure Cosmos DB로의 대량 쓰기를 위해 네이티브 통합을 지원합니다. 이러한 통합을 통해 처리량을 최대화하고 제한 요청을 효율적으로 처리하면서 Azure Cosmos DB를 효과적으로 쓸 수 있습니다. 

향상된 쓰기 메커니즘은 upsert 동작의 차이 때문에 새 호환성 수준에서 사용할 수 있습니다. 1\.2 이전 수준에서 upsert 동작은 문서를 삽입하거나 병합하는 것입니다. 1\.2를 사용하는 경우 문서를 삽입하거나 바꾸도록 upsert 동작이 수정됩니다.

1\.2 이전 수준에서 Stream Analytics는 사용자 지정 저장 프로시저를 사용하여 파티션 키별로 문서를 Azure Cosmos DB에 대량으로 upsert합니다. 여기서 하나의 일괄 처리는 하나의 트랜잭션으로 기록됩니다. 단일 레코드에서 일시적인 오류(제한)가 발생하면 전체 일괄 처리를 다시 시도해야 합니다. 이렇게 하면 적절한 제한이 비교적 느리게 작용하는 시나리오를 구현할 수 있습니다.

다음 예제에서는 동일한 Azure Event Hubs 입력에서 읽는 두 개의 동일한 Stream Analytics 작업을 보여 줍니다. 두 Stream Analytics 작업은 모두 통과 쿼리를 사용하여 [완전 분할](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)되고, 동일한 Azure Cosmos DB 컨테이너에 씁니다. 왼쪽에 있는 메트릭은 호환성 수준 1.0으로 구성된 작업에서 가져온 것입니다. 오른쪽의 메트릭은 1.2로 구성된 것입니다. Azure Cosmos DB 컨테이너의 파티션 키는 입력 이벤트에서 가져온 고유한 GUID입니다.

![Stream Analytics 메트릭 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Event Hubs의 들어오는 이벤트 속도는 Azure Cosmos DB 컨테이너(2만 RU)가 수신하도록 구성된 것보다 2배 더 높기 때문에 Azure Cosmos DB에서 제한이 예상됩니다. 그러나 1.2를 사용하는 작업은 더 높은 처리량(분당 출력 이벤트), 더 낮은 평균 SU% 사용률로 일관되게 씁니다. 사용자 환경에서 이 차이점은 몇 가지 추가 요소에 따라 좌우됩니다. 이러한 요소에는 선택한 이벤트 형식, 입력 이벤트/메시지 크기, 파티션 키 및 쿼리가 포함됩니다.

![Azure Cosmos DB 메트릭 비교](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

1\.2를 사용하는 경우 Stream Analytics는 Azure Cosmos DB의 사용 가능한 처리량을 100% 활용하면서, 제한 또는 속도 제한에 따라 더 낮은 제출 작업을 유지합니다. 이를 통해 컨테이너에서 동시에 실행되는 쿼리 등의 다른 워크로드에 더 나은 환경을 제공합니다. Stream Analytics가 초당 1,000~10,000개 메시지의 싱크로 Azure Cosmos DB에서 스케일 아웃되는 방법을 확인하려면 [이 Azure 샘플 프로젝트](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)를 사용해 보세요.

Azure Cosmos DB 출력의 처리량은 1.0일 때와 1.1일 때 동일합니다. Azure Cosmos DB에서 Stream Analytics의 호환성 수준 1.2를 사용하도록 *강력히 권장*됩니다.

## <a name="azure-cosmos-db-settings-for-json-output"></a>JSON 출력에 대한 Azure Cosmos DB 설정

Azure Cosmos DB를 Stream Analytics의 출력으로 사용하면 아래와 같은 정보를 묻는 메시지가 생성됩니다.

![Azure Cosmos DB 출력 스트림에 대한 정보 필드](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|필드           | Description|
|-------------   | -------------|
|출력 별칭    | Stream Analytics 쿼리에서 이 출력을 참조할 별칭입니다.|
|Subscription    | Azure 구독입니다.|
|계정 ID      | Azure Cosmos DB 계정의 이름 또는 엔드포인트 URI입니다.|
|계정 키     | Azure Cosmos DB 계정에 대한 공유 액세스 키입니다.|
|데이터베이스        | Azure Cosmos DB 데이터베이스 이름입니다.|
|컨테이너 이름 | 컨테이너 이름(예: `MyContainer`)입니다. `MyContainer`라는 컨테이너 하나가 있어야 합니다.  |
|문서 ID     | (선택 사항) 삽입 또는 업데이트 작업의 기준으로 사용해야 하는 고유 키로 사용되는 출력 이벤트의 열 이름입니다. 이 이름을 비워두면 업데이트 옵션 없이 모든 이벤트가 삽입됩니다.|

Azure Cosmos DB 출력을 구성한 후에는 쿼리에서 [INTO 문](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)의 대상으로 사용할 수 있습니다. 이렇게 Azure Cosmos DB 출력을 사용하는 경우 [파티션 키를 명시적으로 설정해야 합니다](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). 

출력 레코드는 Azure Cosmos DB의 파티션 키 이름으로 명명한 대/소문자 구분 열을 포함해야 합니다. 더 많은 병렬 처리를 위해 이 문에 동일한 열을 사용하는 [PARTITION BY 절](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs)이 필요할 수 있습니다.

샘플 쿼리는 다음과 같습니다.

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>오류 처리 및 재시도

Stream Analytics에서 Azure Cosmos DB으로 이벤트를 전송하는 동안 일시적인 오류, 서비스 불가 또는 제한이 발생하는 경우 Stream Analytics는 작업을 성공적으로 완료하기 위해 무한정 재시도합니다. 그러나 다음 오류가 발생할 경우에는 재시도하지 않습니다.

- Unauthorized(HTTP 오류 코드 401)
- NotFound(HTTP 오류 코드 404)
- Forbidden(HTTP 오류 코드 403)
- BadRequest(HTTP 오류 코드 400)