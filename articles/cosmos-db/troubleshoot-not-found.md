---
title: 찾을 수 없는 예외 Azure Cosmos DB 문제 해결
description: 찾을 수 없는 예외를 진단 하 고 해결 하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 1d778b4330389d23b0fe7179a005abfbd7d66d5c
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871108"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>찾을 수 없는 예외 Azure Cosmos DB 진단 및 문제 해결
HTTP 상태 코드 404는 리소스가 더 이상 존재하지 않음을 나타냅니다.

## <a name="expected-behavior"></a>예상 동작
응용 프로그램에서 코드 404을 예상 하 고 시나리오를 올바르게 처리 하는 다양 한 시나리오를 사용할 수 있습니다.

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>존재 하거나 존재 해야 하는 항목에 대해 찾을 수 없는 예외가 반환 되었습니다.
항목이 존재 하거나 존재 해야 하는 경우 상태 코드 404이 반환 되는 이유는 다음과 같습니다.

### <a name="race-condition"></a>경합 조건
여러 SDK 클라이언트 인스턴스가 있으며 쓰기 전에 읽기가 발생 했습니다.

#### <a name="solution"></a>해결 방법:
1. Azure Cosmos DB에 대 한 기본 계정 일관성은 세션 일관성입니다. 항목이 만들어지거나 업데이트 되 면 응답은 읽기 요청이 해당 변경 내용으로 복제본에서 읽기를 보장 하기 위해 SDK 인스턴스 간에 전달 될 수 있는 세션 토큰을 반환 합니다.
1. [일관성 수준을](consistency-levels-choosing.md) [더 강력한 수준](consistency-levels-tradeoffs.md)으로 변경 합니다.

### <a name="invalid-partition-key-and-id-combination"></a>파티션 키 및 ID 조합이 잘못 되었습니다.
파티션 키와 ID 조합이 잘못 되었습니다.

#### <a name="solution"></a>해결 방법:
잘못 된 조합을 유발 하는 응용 프로그램 논리를 수정 합니다. 

### <a name="invalid-character-in-an-item-id"></a>항목 ID에 잘못 된 문자가 있습니다.
항목 ID에 [잘못 된 문자가](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks) 있는 Azure Cosmos DB에 항목이 삽입 되었습니다.

#### <a name="solution"></a>해결 방법:
ID를 특수 문자를 포함 하지 않는 다른 값으로 변경 합니다. ID를 변경 하는 옵션이 아닌 경우 ID를 Base64로 인코딩하여 특수 문자를 이스케이프할 수 있습니다.

ID에 대 한 컨테이너에 이미 삽입 된 항목은 이름 기반 참조 대신 RID 값을 사용 하 여 바꿀 수 있습니다.
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>Ttl (Time to Live) 제거
항목의 [TTL (Time To Live)](https://docs.microsoft.com/azure/cosmos-db/time-to-live) 속성이 설정 되었습니다. TTL 속성이 만료 되어 항목이 제거 되었습니다.

#### <a name="solution"></a>해결 방법:
항목이 제거 되지 않도록 TTL 속성을 변경 합니다.

### <a name="lazy-indexing"></a>지연 인덱싱
[지연 인덱싱이](index-policy.md#indexing-mode) catch 되지 않았습니다.

#### <a name="solution"></a>해결 방법:
인덱싱이 인덱싱 정책을 catch 하거나 변경할 때까지 기다립니다.

### <a name="parent-resource-deleted"></a>부모 리소스 삭제 됨
항목이 존재 하는 데이터베이스 또는 컨테이너가 삭제 되었습니다.

#### <a name="solution"></a>해결 방법:
1. 부모 리소스를 [복원](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period) 하거나 리소스를 다시 만듭니다.
1. 삭제 된 리소스를 대체할 새 리소스를 만듭니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용 하는 경우 문제를 [진단 하 고 해결](troubleshoot-dot-net-sdk.md) 합니다.
* [.Net v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.net v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.