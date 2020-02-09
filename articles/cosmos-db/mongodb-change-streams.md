---
title: MongoDB에 대한 Azure Cosmos DB의 API에서 스트림 변경
description: MongoDB에 대한 변경 스트림 n Azure Cosmos DB의 API를 사용 하 여 데이터에 대한 변경 내용을 가져오는 방법에 대해 알아봅니다.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: fbfce1c107fcf4b6f7d0b5f590a8ddfa64e69190
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184734"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>MongoDB에 대한 Azure Cosmos DB의 API에서 스트림 변경

변경 스트림 API를 사용 하 여 Azure Cosmos DB의 MongoDB API에서 [변경 피드](change-feed.md) 지원을 사용할 수 있습니다. 응용 프로그램은 변경 스트림 API를 사용 하 여 컬렉션 또는 단일 분할 된 항목의 항목에 대한 변경 내용을 가져올 수 있습니다. 나중에 결과에 따라 추가 작업을 수행할 수 있습니다. 컬렉션의 항목에 대한 변경 내용은 수정 시간 순서 대로 캡처되고 분할 키 당 정렬 순서가 보장 됩니다.

다음 예제에서는 컬렉션의 모든 항목에 대한 변경 스트림을 가져오는 방법을 보여 줍니다. 이 예제에서는 삽입, 업데이트 또는 교체 될 때 항목을 감시 하는 커서를 만듭니다. 변경 스트림을 가져오려면 $match 단계, $project 단계 및 fullDocument 옵션이 필요 합니다. 변경 스트림을 사용 하 여 삭제 작업을 감시 하는 작업은 현재 지원 되지 않습니다. 이 문제를 해결 하려면 삭제할 항목에 소프트 마커를 추가 하면 됩니다. 예를 들어 "deleted" 라는 항목에 특성을 추가 하 고이를 "true"로 설정 하 고 항목에 대한 TTL을 설정 하 여 자동으로 삭제 하 고 추적할 수 있습니다.

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

다음 예에서는 단일 분할 된 항목의 항목에 대한 변경 내용을 가져오는 방법을 보여 줍니다. 이 예제에서는 분할 키가 "a"이 고 분할 키 값이 "1"과 같은 항목의 변경 내용을 가져옵니다.

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

변경 스트림을 사용할 때는 다음과 같은 제한 사항이 적용 됩니다.

* `operationType` 및 `updateDescription` 속성은 출력 문서에서 아직 지원 되지 않습니다.
* 현재 `insert`, `update`및 `replace` 작업 유형이 지원 됩니다. 삭제 작업 또는 기타 이벤트는 아직 지원 되지 않습니다.

이러한 제한 사항으로 인해 이전 예제에 나와 있는 것 처럼 $match 단계, $project 단계 및 fullDocument 옵션이 필요 합니다.

## <a name="error-handling"></a>오류 처리

변경 스트림을 사용할 때 지원 되는 오류 코드와 메시지는 다음과 같습니다.

* **HTTP 오류 코드 429** -변경 스트림이 제한 되 면 빈 페이지를 반환 합니다.

* **NamespaceNotFound (OperationType 무효화)** -존재 하지 않는 컬렉션에서 변경 스트림을 실행 하거나 컬렉션을 삭제 하면 `NamespaceNotFound` 오류가 반환 됩니다. `operationType` 속성은 `operationType Invalidate` 오류 대신 출력 문서에서 반환 될 수 없으므로 `NamespaceNotFound` 오류가 반환 됩니다.

## <a name="next-steps"></a>다음 단계

* [Time to live를 사용 하 여 MongoDB에 대한 Azure Cosmos DB의 API에서 자동으로 데이터 만료](mongodb-time-to-live.md)
* [MongoDB에 대한 Azure Cosmos DB의 API에서 인덱싱](mongodb-indexing.md)