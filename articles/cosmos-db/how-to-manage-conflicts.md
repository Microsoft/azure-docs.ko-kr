---
title: Azure Cosmos DB에서 지역 간의 충돌 관리
description: 마지막-쓰기-wins 또는 사용자 지정 충돌 해결 정책을 만들어 Azure Cosmos DB에서 충돌을 관리 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mjbrown
ms.openlocfilehash: 6d364f1a9974d6d638bb0f824e88ed3866644c15
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441805"
---
# <a name="manage-conflict-resolution-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 충돌 해결 정책 관리

다중 영역 쓰기를 사용하면 여러 클라이언트가 동일한 항목에 쓸 때 충돌이 발생할 수 있습니다. 충돌이 발생할 때 여러 충돌 해결 정책을 사용하여 충돌을 해결할 수 있습니다. 이 문서에서는 충돌 해결 정책을 관리하는 방법을 설명합니다.

## <a name="create-a-last-writer-wins-conflict-resolution-policy"></a>최종 작성자 인정(last-writer-wins) 충돌 해결 정책 만들기

다음 샘플에서는 최종 작성자 인정 충돌 해결 정책을 사용하여 컨테이너를 설정하는 방법을 보여 줍니다. 최종 작성자 인정(last-writer-wins)의 기본 경로는 타임스탬프 필드 또는 `_ts` 속성입니다. SQL API의 경우, 숫자 형식의 사용자 정의 경로로 설정할 수도 있습니다. 충돌이 발생할 경우 가장 높은 값이 적용됩니다. 경로가 설정되지 않았거나 잘못된 경우 기본값인 `_ts`로 설정됩니다. 이 정책을 통해 해결된 충돌은 충돌 피드에 표시되지 않습니다. 이 정책은 모든 API에서 사용할 수 있습니다.

### <a id="create-custom-conflict-resolution-policy-lww-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection lwwCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.lwwCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.LastWriterWins,
          ConflictResolutionPath = "/myCustomId",
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-lww-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.lwwCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.LastWriterWins,
            ResolutionPath = "/myCustomId",
        }
    });
```

### <a id="create-custom-conflict-resolution-policy-lww-java-async"></a>Java 비동기 SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-lww-java-sync"></a>Java 동기 SDK

```java
DocumentCollection lwwCollection = new DocumentCollection();
lwwCollection.setId(this.lwwCollectionName);
ConflictResolutionPolicy lwwPolicy = ConflictResolutionPolicy.createLastWriterWinsPolicy("/myCustomId");
lwwCollection.setConflictResolutionPolicy(lwwPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, lwwCollection);
```

### <a id="create-custom-conflict-resolution-policy-lww-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: lwwContainer } = await database.containers.createIfNotExists(
  {
    id: this.lwwContainerName,
    conflictResolutionPolicy: {
      mode: "LastWriterWins",
      conflictResolutionPath: "/myCustomId"
    }
  }
);
```

### <a id="create-custom-conflict-resolution-policy-lww-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'LastWriterWins',
        'conflictResolutionPath': '/myCustomId'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

## <a name="create-a-custom-conflict-resolution-policy-using-a-stored-procedure"></a>저장 프로시저를 사용하여 사용자 지정 충돌 해결 정책 만들기

다음 샘플은 저장 프로시저가 포함된 사용자 지정 충돌 해결 정책을 사용하여 충돌을 해결하는 컨테이너 설정 방법을 보여줍니다. 저장 프로시저에 오류가 없으면 이러한 충돌이 충돌 피드에 표시되지 않습니다. 컨테이너를 사용하여 정책을 만든 후에는 저장 프로시저를 만들어야 합니다. 아래의 .NET SDK 샘플은 예를 보여줍니다. 이 정책은 Core(SQL) Api에서만 지원됩니다.

### <a name="sample-custom-conflict-resolution-stored-procedure"></a>사용자 지정 충돌 해결 저장 프로시저 샘플

사용자 지정 충돌 해결 저장 프로시저는 아래에 표시된 함수 시그니처를 사용하여 구현해야 합니다. 함수 이름은 저장 프로시저를 컨테이너에 등록할 때 사용한 이름과 일치하지 않아도 되지만, 이 방법이 간단합니다. 다음은 이 저장 프로시저에 대해 구현해야 하는 매개 변수에 대한 설명입니다.

- **incomingItem**: 충돌을 생성 하는 커밋에서 삽입 또는 업데이트 되는 항목입니다. 삭제 작업에 대해 Null입니다.
- **existingitem**: 현재 커밋된 항목입니다. 이 값은 업데이트에서는 null이 아니고, 삽입 또는 삭제에서는 null입니다.
- **Istombstone**: incomingItem가 이전에 삭제 한 항목과 충돌 하는지 여부를 나타내는 부울입니다. true이면 existingItem도 Null입니다.
- **conflictingItems**: 컨테이너에서 incomingItem에 대한 ID 또는 기타 고유 인덱스 속성과 충돌 하는 모든 항목의 커밋된 버전 배열입니다.

> [!IMPORTANT]
> 저장 프로시저와 마찬가지로, 사용자 지정 충돌 해결 프로시저는 파티션 키가 동일한 모든 데이터에 액세스하여 충돌 해결을 위한 삽입, 업데이트 또는 삭제 작업을 수행할 수 있습니다.

이 샘플 저장 프로시저는 `/myCustomId` 경로에서 가장 낮은 값을 선택하여 충돌을 해결합니다.

```javascript
function resolver(incomingItem, existingItem, isTombstone, conflictingItems) {
  var collection = getContext().getCollection();

  if (!incomingItem) {
      if (existingItem) {

          collection.deleteDocument(existingItem._self, {}, function (err, responseOptions) {
              if (err) throw err;
          });
      }
  } else if (isTombstone) {
      // delete always wins.
  } else {
      if (existingItem) {
          if (incomingItem.myCustomId > existingItem.myCustomId) {
              return; // existing item wins
          }
      }

      var i;
      for (i = 0; i < conflictingItems.length; i++) {
          if (incomingItem.myCustomId > conflictingItems[i].myCustomId) {
              return; // existing conflict item wins
          }
      }

      // incoming item wins - clear conflicts and replace existing with incoming.
      tryDelete(conflictingItems, incomingItem, existingItem);
  }

  function tryDelete(documents, incoming, existing) {
      if (documents.length > 0) {
          collection.deleteDocument(documents[0]._self, {}, function (err, responseOptions) {
              if (err) throw err;

              documents.shift();
              tryDelete(documents, incoming, existing);
          });
      } else if (existing) {
          collection.replaceDocument(existing._self, incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      } else {
          collection.createDocument(collection.getSelfLink(), incoming,
              function (err, documentCreated) {
                  if (err) throw err;
              });
      }
  }
}
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection udpCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.udpCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
          ConflictResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver"),
      },
  });

//Create the stored procedure
await clients[0].CreateStoredProcedureAsync(
UriFactory.CreateStoredProcedureUri(this.databaseName, this.udpCollectionName, "resolver"), new StoredProcedure
{
    Id = "resolver",
    Body = File.ReadAllText(@"resolver.js")
});
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.udpCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom,
            ResolutionProcedure = string.Format("dbs/{0}/colls/{1}/sprocs/{2}", this.databaseName, this.udpCollectionName, "resolver")
        }
    });

await container.Scripts.CreateStoredProcedureAsync(
    new StoredProcedureProperties("resolver", File.ReadAllText(@"resolver.js"))
);
```

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-async"></a>Java 비동기 SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy("resolver");
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

컨테이너가 만들어지면 `resolver` 저장 프로시저를 만들어야 합니다.

### <a id="create-custom-conflict-resolution-policy-stored-proc-java-sync"></a>Java 동기 SDK

```java
DocumentCollection udpCollection = new DocumentCollection();
udpCollection.setId(this.udpCollectionName);
ConflictResolutionPolicy udpPolicy = ConflictResolutionPolicy.createCustomPolicy(
        String.format("dbs/%s/colls/%s/sprocs/%s", this.databaseName, this.udpCollectionName, "resolver"));
udpCollection.setConflictResolutionPolicy(udpPolicy);
DocumentCollection createdCollection = this.tryCreateDocumentCollection(createClient, database, udpCollection);
```

컨테이너가 만들어지면 `resolver` 저장 프로시저를 만들어야 합니다.

### <a id="create-custom-conflict-resolution-policy-stored-proc-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const { container: udpContainer } = await database.containers.createIfNotExists(
  {
    id: this.udpContainerName,
    conflictResolutionPolicy: {
      mode: "Custom",
      conflictResolutionProcedure: `dbs/${this.databaseName}/colls/${
        this.udpContainerName
      }/sprocs/resolver`
    }
  }
);
```

컨테이너가 만들어지면 `resolver` 저장 프로시저를 만들어야 합니다.

### <a id="create-custom-conflict-resolution-policy-stored-proc-python"></a>Python SDK

```python
udp_collection = {
    'id': self.udp_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom',
        'conflictResolutionProcedure': 'dbs/' + self.database_name + "/colls/" + self.udp_collection_name + '/sprocs/resolver'
    }
}
udp_collection = self.try_create_document_collection(
    create_client, database, udp_collection)
```

컨테이너가 만들어지면 `resolver` 저장 프로시저를 만들어야 합니다.

## <a name="create-a-custom-conflict-resolution-policy"></a>사용자 지정 충돌 해결 정책 만들기

다음 샘플은 사용자 지정 충돌 해결 정책을 사용하여 컨테이너를 설정하는 방법을 보여줍니다. 이러한 충돌은 충돌 피드에 표시됩니다.

### <a id="create-custom-conflict-resolution-policy-dotnet"></a>.NET SDK V2

```csharp
DocumentCollection manualCollection = await createClient.CreateDocumentCollectionIfNotExistsAsync(
  UriFactory.CreateDatabaseUri(this.databaseName), new DocumentCollection
  {
      Id = this.manualCollectionName,
      ConflictResolutionPolicy = new ConflictResolutionPolicy
      {
          Mode = ConflictResolutionMode.Custom,
      },
  });
```

### <a id="create-custom-conflict-resolution-policy-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = await createClient.GetDatabase(this.databaseName)
    .CreateContainerIfNotExistsAsync(new ContainerProperties(this.manualCollectionName, "/partitionKey")
    {
        ConflictResolutionPolicy = new ConflictResolutionPolicy()
        {
            Mode = ConflictResolutionMode.Custom
        }
    });
```

### <a id="create-custom-conflict-resolution-policy-java-async"></a>Java 비동기 SDK

```java
DocumentCollection collection = new DocumentCollection();
collection.setId(id);
ConflictResolutionPolicy policy = ConflictResolutionPolicy.createCustomPolicy();
collection.setConflictResolutionPolicy(policy);
DocumentCollection createdCollection = client.createCollection(databaseUri, collection, null).toBlocking().value();
```

### <a id="create-custom-conflict-resolution-policy-java-sync"></a>Java 동기 SDK

```java
DocumentCollection manualCollection = new DocumentCollection();
manualCollection.setId(this.manualCollectionName);
ConflictResolutionPolicy customPolicy = ConflictResolutionPolicy.createCustomPolicy(null);
manualCollection.setConflictResolutionPolicy(customPolicy);
DocumentCollection createdCollection = client.createCollection(database.getSelfLink(), collection, null).getResource();
```

### <a id="create-custom-conflict-resolution-policy-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const database = client.database(this.databaseName);
const {
  container: manualContainer
} = await database.containers.createIfNotExists({
  id: this.manualContainerName,
  conflictResolutionPolicy: {
    mode: "Custom"
  }
});
```

### <a id="create-custom-conflict-resolution-policy-python"></a>Python SDK

```python
database = client.ReadDatabase("dbs/" + self.database_name)
manual_collection = {
    'id': self.manual_collection_name,
    'conflictResolutionPolicy': {
        'mode': 'Custom'
    }
}
manual_collection = client.CreateContainer(database['_self'], collection)
```

## <a name="read-from-conflict-feed"></a>충돌 피드에서 읽기

다음 샘플은 컨테이너의 충돌 피드에서 읽는 방법을 보여줍니다. 충돌이 자동으로 해결되지 않았거나 사용자 지정 충돌 정책을 사용하는 경우에만 충돌 피드에 충돌이 표시됩니다.

### <a id="read-from-conflict-feed-dotnet"></a>.NET SDK V2

```csharp
FeedResponse<Conflict> conflicts = await delClient.ReadConflictFeedAsync(this.collectionUri);
```

### <a id="read-from-conflict-feed-dotnet-v3"></a>.NET SDK V3

```csharp
FeedIterator<ConflictProperties> conflictFeed = container.Conflicts.GetConflictQueryIterator();
while (conflictFeed.HasMoreResults)
{
    FeedResponse<ConflictProperties> conflicts = await conflictFeed.ReadNextAsync();
    foreach (ConflictProperties conflict in conflicts)
    {
        // Read the conflicted content
        MyClass intendedChanges = container.Conflicts.ReadConflictContent<MyClass>(conflict);
        MyClass currentState = await container.Conflicts.ReadCurrentAsync<MyClass>(conflict, new PartitionKey(intendedChanges.MyPartitionKey));

        // Do manual merge among documents
        await container.ReplaceItemAsync<MyClass>(intendedChanges, intendedChanges.Id, new PartitionKey(intendedChanges.MyPartitionKey));

        // Delete the conflict
        await container.Conflicts.DeleteAsync(conflict, new PartitionKey(intendedChanges.MyPartitionKey));
    }
}
```

### <a id="read-from-conflict-feed-java-async"></a>Java 비동기 SDK

```java
FeedResponse<Conflict> response = client.readConflicts(this.manualCollectionUri, null)
                    .first().toBlocking().single();
for (Conflict conflict : response.getResults()) {
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-java-sync"></a>Java 동기 SDK

```java
Iterator<Conflict> conflictsIterator = client.readConflicts(this.collectionLink, null).getQueryIterator();
while (conflictsIterator.hasNext()) {
    Conflict conflict = conflictsIterator.next();
    /* Do something with conflict */
}
```

### <a id="read-from-conflict-feed-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
const container = client
  .database(this.databaseName)
  .container(this.lwwContainerName);

const { result: conflicts } = await container.conflicts.readAll().toArray();
```

### <a id="read-from-conflict-feed-python"></a>Python

```python
conflicts_iterator = iter(client.ReadConflicts(self.manual_collection_link))
conflict = next(conflicts_iterator, None)
while conflict:
    # Do something with conflict
    conflict = next(conflicts_iterator, None)
```

## <a name="next-steps"></a>다음 단계

다음 Azure Cosmos DB 개념에 대해 자세히 알아봅니다.

- [전역 분산 - 내부 살펴보기](global-dist-under-the-hood.md)
- [애플리케이션에서 다중 마스터를 구성하는 방법](how-to-multi-master.md)
- [클라이언트 멀티 호밍 구성](how-to-manage-database-account.md#configure-multiple-write-regions)
- [Azure Cosmos DB 계정에서 지역 추가 또는 제거](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
- [애플리케이션에서 다중 마스터를 구성하는 방법](how-to-multi-master.md)
- [분할 및 데이터 배포](partition-data.md)
- [Azure Cosmos DB의 인덱싱](indexing-policies.md)
