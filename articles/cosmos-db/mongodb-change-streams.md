---
title: Azure Cosmos DB의 API for MongoDB에서 변경 스트림
description: Azure Cosmos DB의 API for MongoDB에서 변경 스트림을 사용하여 데이터에 대한 변경 내용을 가져오는 방법에 대해 알아봅니다.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/04/2020
ms.author: rosouz
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: 47f1fb4414b26ca9edbd992826a383ca65772ae7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003816"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB의 API for MongoDB에서 변경 스트림

Azure Cosmos DB의 API for MongoDB의 [변경 피드](change-feed.md) 지원은 변경 스트림 API를 사용하여 제공됩니다. 변경 스트림 API를 사용하여 애플리케이션에서 컬렉션 또는 단일 분할된 데이터베이스의 항목에 대한 변경 내용을 가져올 수 있습니다. 나중에 결과에 따라 추가 작업을 수행할 수 있습니다. 컬렉션의 항목에 대한 변경 내용은 수정 시간 순서대로 캡처되고 분할된 데이터베이스 키당 정렬 순서가 보장됩니다.

> [!NOTE]
> 변경 스트림을 사용하려면 Azure Cosmos DB의 API for MongoDB 버전 3.6 이상을 사용하여 계정을 만듭니다. 이전 버전에 대해 변경 스트림 예제를 실행하는 경우 `Unrecognized pipeline stage name: $changeStream` 오류가 표시될 수 있습니다.

## <a name="examples"></a>예

다음 예에서는 컬렉션의 모든 항목에 대한 변경 스트림을 가져오는 방법을 보여 줍니다. 이 예에서는 항목이 삽입, 업데이트 또는 교체될 때 항목을 감시하는 커서를 만듭니다. 변경 스트림을 가져오려면 `$match` 단계, `$project` 단계 및 `fullDocument` 옵션이 필요합니다. 변경 스트림을 사용한 삭제 작업에 대한 감시는 현재 지원되지 않습니다. 이 문제를 해결하려면 삭제할 항목에 소프트 마커를 추가하면 됩니다. 예를 들어 “삭제됨”이라는 항목에 특성을 추가할 수 있습니다. 항목을 삭제하려면 “삭제됨”을 `true`로 설정하고 항목에 대한 TTL을 설정합니다. “삭제됨”을 `true`로 업데이트하는 것은 업데이트 작업이므로 변경 스트림에 이 변경 내용이 표시됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 Java에서 스트림 변경 기능을 사용 하는 방법을 보여 줍니다. 전체 예제는이 [GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-changestream/blob/master/mongostream/src/main/java/com/azure/cosmos/mongostream/App.java)를 참조 하세요. 또한이 예제에서는 메서드를 사용 하 여 `resumeAfter` 마지막 읽기부터 모든 변경 내용을 검색 하는 방법을 보여 줍니다. 

```java
Bson match = Aggregates.match(Filters.in("operationType", asList("update", "replace", "insert")));

// Pick the field you are most interested in
Bson project = Aggregates.project(fields(include("_id", "ns", "documentKey", "fullDocument")));

// This variable is for second example
BsonDocument resumeToken = null;

// Now time to build the pipeline
List<Bson> pipeline = Arrays.asList(match, project);

//#1 Simple example to seek changes

// Create cursor with update_lookup
MongoChangeStreamCursor<ChangeStreamDocument<org.bson.Document>> cursor = collection.watch(pipeline)
        .fullDocument(FullDocument.UPDATE_LOOKUP).cursor();

Document document = new Document("name", "doc-in-step-1-" + Math.random());
collection.insertOne(document);

while (cursor.hasNext()) {
    // There you go, we got the change document.
    ChangeStreamDocument<Document> csDoc = cursor.next();

    // Let is pick the token which will help us resuming
    // You can save this token in any persistent storage and retrieve it later
    resumeToken = csDoc.getResumeToken();
    //Printing the token
    System.out.println(resumeToken);
    
    //Printing the document.
    System.out.println(csDoc.getFullDocument());
    //This break is intentional but in real project feel free to remove it.
    break;
}

cursor.close();

```
---

## <a name="changes-within-a-single-shard"></a>단일 분할된 데이터베이스가 포함된 변경 내용

다음 예에서는 단일 분할된 데이터베이스 내의 항목에 대한 변경 내용을 가져오는 방법을 보여 줍니다. 이 예에서는 분할 키가 “a”와 같고 분할된 데이터베이스 키 값은 “1”과 같은 항목의 변경 내용을 가져옵니다. 다른 클라이언트에서 다른 분할된 데이터베이스의 변경 내용을 병렬로 읽을 수 있습니다.

```javascript
var cursor = db.coll.watch(
    [
        { 
            $match: { 
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="current-limitations"></a>현재 제한 사항

변경 스트림을 사용하는 경우 다음과 같은 제한이 적용될 수 있습니다.

* `operationType` 및 `updateDescription` 속성은 출력 문서에서 아직 지원되지 않습니다.
* 현재 `insert`, `update` 및 `replace` 작업 유형이 지원됩니다. 그러나 삭제 작업 또는 기타 이벤트는 아직 지원 되지 않습니다.

해당 제한 사항으로 인해 이전 예제에 나와 있는 것처럼 $match 단계, $project 단계 및 fullDocument 옵션이 필요합니다.

Azure Cosmos DB의 SQL API에 있는 변경 피드와 달리, 변경 스트림을 사용할 별도의 [변경 피드 프로세서 라이브러리](change-feed-processor.md) 또는 임대 컨테이너를 사용할 필요성이 없습니다. 현재 변경 스트림을 처리하기 위한 [Azure Functions 트리거](change-feed-functions.md)는 지원되지 않습니다.

## <a name="error-handling"></a>오류 처리

변경 스트림을 사용할 때 지원되는 오류 코드와 메시지는 다음과 같습니다.

* **HTTP 오류 코드 16500** - 변경 스트림이 제한되면 빈 페이지를 반환합니다.

* **NamespaceNotFound**(OperationType 무효화) - 존재하지 않는 컬렉션에서 변경 스트림을 실행하거나 컬렉션을 삭제하면 `NamespaceNotFound` 오류가 반환됩니다. `operationType` 속성은 `operationType Invalidate` 오류 대신 출력 문서에서 반환될 수 없으므로 `NamespaceNotFound` 오류가 반환됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB의 API for MongoDB에서 TTL(Time to live)을 사용하여 자동으로 데이터 만료](mongodb-time-to-live.md)
* [Azure Cosmos DB의 API for MongoDB에서 인덱싱](mongodb-indexing.md)
