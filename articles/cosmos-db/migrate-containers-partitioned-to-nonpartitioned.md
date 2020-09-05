---
title: 분할 되지 않은 Azure Cosmos 컨테이너를 분할 된 컨테이너로 마이그레이션
description: 분할 되지 않은 기존의 모든 컨테이너를 분할 된 컨테이너로 마이그레이션하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: f2defeb2c417e94109721d32d8d4b611e2e46569
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003221"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>분할 되지 않은 컨테이너를 분할 된 컨테이너로 마이그레이션

Azure Cosmos DB는 파티션 키 없이 컨테이너를 만드는 것을 지원 합니다. 현재는 2. x 보다 작거나 같은 버전의 Azure CLI 및 Azure Cosmos DB Sdk (.Net, Java, NodeJs)를 사용 하 여 분할 되지 않은 컨테이너를 만들 수 있습니다. Azure Portal를 사용 하 여 분할 되지 않은 컨테이너를 만들 수 없습니다. 그러나 이러한 분할 되지 않은 컨테이너는 탄력적이 지 않으며 저장소 용량은 20gb이 고 처리량 제한은 10K r u/초입니다.

분할 되지 않은 컨테이너는 레거시 컨테이너 이므로 분할 되지 않은 기존 컨테이너를 분할 된 컨테이너로 마이그레이션하여 저장소 및 처리량을 확장 해야 합니다. Azure Cosmos DB은 분할 되지 않은 컨테이너를 분할 된 컨테이너로 마이그레이션하기 위한 시스템 정의 메커니즘을 제공 합니다. 이 문서에서는 기존의 분할 되지 않은 모든 컨테이너를 분할 된 컨테이너로 자동 마이그레이션하는 방법에 대해 설명 합니다. 모든 언어에서 V3 버전의 Sdk를 사용 하는 경우에만 자동 마이그레이션 기능을 사용할 수 있습니다.

> [!NOTE]
> 현재이 문서에 설명 된 단계를 사용 하 여 Azure Cosmos DB MongoDB 및 Gremlin API 계정을 마이그레이션할 수 없습니다.

## <a name="migrate-container-using-the-system-defined-partition-key"></a>시스템 정의 파티션 키를 사용 하 여 컨테이너 마이그레이션

마이그레이션을 지원 하기 위해 Azure Cosmos DB는 `/_partitionkey` 파티션 키가 없는 모든 컨테이너에서 라는 시스템 정의 파티션 키를 제공 합니다. 컨테이너를 마이그레이션한 후에는 파티션 키 정의를 변경할 수 없습니다. 예를 들어 분할 된 컨테이너로 마이그레이션되는 컨테이너에 대 한 정의는 다음과 같습니다.

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

컨테이너를 마이그레이션한 후에는 `_partitionKey` 문서의 다른 속성과 함께 속성을 채워서 문서를 만들 수 있습니다. `_partitionKey`속성은 문서의 파티션 키를 나타냅니다.

프로 비전 된 처리량을 최적으로 활용 하려면 올바른 파티션 키를 선택 해야 합니다. 자세한 내용은 [파티션 키를 선택 하는 방법](partitioning-overview.md) 문서를 참조 하세요.

> [!NOTE]
> 모든 언어에서 최신/V3 버전의 Sdk를 사용 하는 경우에만 시스템 정의 파티션 키를 활용할 수 있습니다.

다음 예제에서는 시스템 정의 파티션 키로 문서를 만들고 해당 문서를 읽는 샘플 코드를 보여 줍니다.

**문서의 JSON 표현**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

전체 샘플은 [.net 샘플][1] GitHub 리포지토리를 참조 하세요.
                      
## <a name="migrate-the-documents"></a>문서 마이그레이션

컨테이너 정의는 파티션 키 속성을 사용 하 여 향상 되지만 컨테이너 내의 문서는 자동으로 마이그레이션되지 않습니다. 즉, 시스템 파티션 키 속성 `/_partitionKey` 경로가 기존 문서에 자동으로 추가 되지 않습니다. 파티션 키 없이 만든 문서를 읽고 문서에서 속성을 사용 하 여 다시 작성 하 여 기존 문서를 다시 분할 해야 합니다 `_partitionKey` .

## <a name="access-documents-that-dont-have-a-partition-key"></a>파티션 키가 없는 문서에 액세스

응용 프로그램은 "PartitionKey" 라는 특수 시스템 속성을 사용 하 여 파티션 키가 없는 기존 문서에 액세스할 수 있습니다 .이는 마이그레이션되지 않은 문서의 값입니다. 모든 CRUD 및 쿼리 작업에서이 속성을 사용할 수 있습니다. 다음 예제에서는 NonePartitionKey에서 단일 문서를 읽는 샘플을 보여 줍니다. 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

문서를 다시 분할 하는 방법에 대 한 전체 샘플은 [.net 샘플][1] GitHub 리포지토리를 참조 하세요. 

## <a name="compatibility-with-sdks"></a>Sdk와의 호환성

이전 버전의 Azure Cosmos DB Sdk (예: V2. x. x) 및 V1. x. x는 시스템 정의 파티션 키 속성을 지원 하지 않습니다. 따라서 이전 SDK에서 컨테이너 정의를 읽으면 파티션 키 정의가 포함 되지 않으며 이러한 컨테이너는 이전과 정확히 동일 하 게 동작 합니다. 이전 버전의 Sdk를 사용 하 여 빌드된 응용 프로그램은 변경 없이 그대로 분할 된 상태에서 계속 작동 합니다. 

마이그레이션된 컨테이너가 SDK의 최신/V3 버전에서 사용 되 고 새 문서 내에서 시스템 정의 파티션 키를 채우기 시작 하면 이전 Sdk에서 이러한 문서에 대 한 액세스 (읽기, 업데이트, 삭제, 쿼리)를 더 이상 사용할 수 없습니다.

## <a name="known-issues"></a>알려진 문제

**V3 SDK를 사용 하 여 파티션 키 없이 삽입 된 항목 수에 대 한 쿼리는 더 높은 처리량 사용을 포함할 수 있습니다.**

V2 sdk를 사용 하 여 삽입 된 항목 또는 매개 변수를 사용 하 여 V3 sdk를 사용 하 여 삽입 된 항목에 대해 V3 SDK에서 쿼리 하는 경우 `PartitionKey.None` `PartitionKey.None` FeedOptions에서 매개 변수가 제공 되는 경우 count 쿼리는 추가/초를 소비할 수 있습니다. `PartitionKey.None`다른 항목이 파티션 키로 삽입 되지 않은 경우에는 매개 변수를 제공 하지 않는 것이 좋습니다.

파티션 키에 대해 다른 값을 사용 하 여 새 항목을 삽입 하는 경우에서 적절 한 키를 전달 하 여 이러한 항목 수를 쿼리하면 `FeedOptions` 문제가 발생 하지 않습니다. 파티션 키를 사용 하 여 새 문서를 삽입 한 후에는 파티션 키 값을 사용 하지 않고 문서 개수만 쿼리해야 하는 경우 일반 분할 된 컬렉션과 비슷한 방식으로 쿼리를 다시 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](set-throughput.md)
* [Azure Cosmos 계정 작업](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration
