---
title: '자습서: Azure Cosmos DB에서 SQL을 사용하여 쿼리하는 방법'
description: '자습서: 쿼리 플레이그라운드를 통해 Azure Cosmos DB에서 SQL 쿼리를 사용하여 쿼리하는 방법 알아보기'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.openlocfilehash: 2a6033ef1d2b7dda04b1510d42fa49141e0b79b4
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136000"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-sql-api"></a>자습서: SQL API를 사용하여 Azure Cosmos DB 쿼리

Azure Cosmos DB [SQL API](documentdb-introduction.md)는 SQL을 사용하여 문서를 쿼리할 수 있도록 지원합니다. 이 문서에서는 샘플 문서 및 두 가지 샘플 SQL 쿼리와 결과를 제공합니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * SQL을 사용한 데이터 쿼리

## <a name="sample-document"></a>샘플 문서

이 문서의 SQL 쿼리에서는 다음과 같은 샘플 문서를 사용합니다.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="where-can-i-run-sql-queries"></a>SQL 쿼리를 실행할 수 있는 위치

Azure Portal의 [데이터 탐색기]를 사용하여 [REST API 및 SDK](sql-api-sdk-dotnet.md)를 통해 쿼리를 실행할 수 있으며, 기존 샘플 데이터 집합에 대해 쿼리를 실행하는 [쿼리 실습](https://www.documentdb.com/sql/demo)도 사용할 수 있습니다.

SQL 쿼리에 대한 자세한 내용은 다음을 참조하세요.
* [SQL 쿼리 및 SQL 구문](sql-query-getting-started.md)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 Azure Cosmos DB 계정과 컬렉션이 있다고 가정합니다. 이러한 리소스가 없나요? [5분 빠른 시작](create-cosmosdb-resources-portal.md)을 완료하세요.

## <a name="example-query-1"></a>예제 쿼리 1

위의 샘플 가족 문서를 고려해 볼 때 다음 SQL 쿼리에서는 ID 필드가 `WakefieldFamily`와 일치하는 문서를 반환합니다. `SELECT *` 문이므로 쿼리 결과는 완전한 JSON 문서입니다.

**쿼리**

```sql
    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"
```

**결과**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>예제 쿼리 2

다음 쿼리에서는 ID가 `WakefieldFamily`와 일치하는 가족의 지정된 모든 자식 이름을 반환합니다.

**쿼리**

```sql
    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
```

**결과**

```
[
    {
        "givenName": "Jesse"
    },
    {
        "givenName": "Lisa"
    }
]
```


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행했습니다.

> [!div class="checklist"]
> * SQL을 사용하여 쿼리하는 방법  

이제 전 세계로 데이터를 배포하는 방법을 알아보려면 다음 자습서로 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [전 세계로 데이터 배포](tutorial-global-distribution-sql-api.md)

