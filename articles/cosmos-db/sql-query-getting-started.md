---
title: Azure Cosmos DB에서 SQL 쿼리 시작
description: SQL 쿼리를 사용 하 여 Azure Cosmos DB에서 데이터를 쿼리 하는 방법에 대해 알아봅니다. Azure Cosmos DB의 컨테이너에 샘플 데이터를 업로드 하 고 쿼리할 수 있습니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 1d24261edea843fa928ad00e3ce7babcb84acd3b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873338"
---
# <a name="getting-started-with-sql-queries"></a>SQL 쿼리 시작

Azure Cosmos DB SQL API 계정은 구조적 쿼리 언어 (SQL)를 사용 하 여 항목을 JSON 쿼리 언어로 쿼리 하는 것을 지원 합니다. Azure Cosmos DB 쿼리 언어의 디자인 목표는 다음과 같습니다.

* 새 쿼리 언어를 고안 하는 대신 가장 친숙 하 고 인기 있는 쿼리 언어 중 하나인 SQL을 지원 합니다. SQL은 JSON 항목에 대한 풍부한 쿼리를 위한 공식 프로그래밍 모델을 제공 합니다.  

* JavaScript의 프로그래밍 모델을 쿼리 언어의 기반으로 사용 합니다. JavaScript의 형식 시스템, 식 평가 및 함수 호출은 SQL API의 루트입니다. 이러한 루트는 관계형 프로젝션, JSON 항목에 대한 계층적 탐색, 자체 조인, 공간 쿼리, 완전히 JavaScript로 작성 된 Udf (사용자 정의 함수) 호출 등의 기능에 대한 자연 스러운 프로그래밍 모델을 제공 합니다.

## <a name="upload-sample-data"></a>예제 데이터 업로드

SQL API Cosmos DB 계정에서 `Families`라는 컨테이너를 만듭니다. 컨테이너에 두 개의 간단한 JSON 항목을 만듭니다. 이 데이터 집합을 사용 하 여 Azure Cosmos DB 쿼리 문서에서 대부분의 샘플 쿼리를 실행할 수 있습니다.

### <a name="create-json-items"></a>JSON 항목 만들기

다음 코드는 패밀리에 대해 두 개의 간단한 JSON 항목을 만듭니다. Andersen 및 Wakefield 제품군에 대한 간단한 JSON 항목에는 부모, 자식, 애완 동물, 주소 및 등록 정보가 포함 됩니다. 첫 번째 항목에는 문자열, 숫자, 부울, 배열 및 중첩 속성이 있습니다.


```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

두 번째 항목은 `firstName` 및 `lastName`대신 `givenName` 및 `familyName`를 사용 합니다.

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
        "gender": "female",
        "grade": 1,
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

### <a name="query-the-json-items"></a>JSON 항목 쿼리

JSON 데이터에 대한 몇 가지 쿼리를 수행 하 여 Azure Cosmos DB의 SQL 쿼리 언어에 대한 몇 가지 주요 측면을 이해 합니다.

다음 쿼리는 `id` 필드가 `AndersenFamily`와 일치 하는 항목을 반환 합니다. `SELECT *` 쿼리 이므로 쿼리의 출력은 전체 JSON 항목입니다. SELECT 구문에 대한 자세한 내용은 [select 문](sql-query-select.md)을 참조 하십시오. 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

쿼리 결과는 다음과 같습니다. 

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

다음 쿼리는 JSON 출력을 다른 모양으로 다시 포맷 합니다. 이 쿼리는 두 개의 선택 된 필드인 `Name` 및 `City`를 사용 하 여 새 JSON `Family` 개체를 프로젝션 합니다. "서 면"은 이러한 경우와 일치 합니다.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

쿼리 결과는 다음과 같습니다.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

다음 쿼리는 `id`이 `WakefieldFamily`와 일치 하는 가족의 지정 된 모든 이름을 도시별로 정렬 하 여 반환 합니다.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC
```

결과는 다음과 같습니다.

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

## <a name="remarks"></a>설명

위의 예에서는 Cosmos DB 쿼리 언어의 몇 가지 측면을 보여 줍니다.  

* SQL API는 JSON 값에 대해 작동 하므로 행과 열 대신 트리 모양의 엔터티를 처리 합니다. ANSI SQL의 `<table>.<column>`에 대한 두 부분으로 된 참조와 마찬가지로 `Node1.Node2.Node3…..Nodem`와 같은 임의의 깊이에서 트리 노드를 참조할 수 있습니다.

* 쿼리 언어는 스키마가 있는 데이터와 함께 작동 하므로 유형 시스템을 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과는 유효한 JSON 값 이지만 고정 스키마가 아닐 수도 있습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템 및 식은 JSON 형식만 처리 하도록 제한 됩니다. 자세한 내용은 [JSON 사양](https://www.json.org/)을 참조 하세요.  

* Cosmos 컨테이너는 JSON 항목의 스키마 없는 컬렉션입니다. 컨테이너 항목 간의 관계는 기본 키 및 외래 키 관계가 아닌 포함을 통해 암시적으로 캡처됩니다. 이 기능은이 문서의 뒷부분에서 설명 하는 항목 내 조인에 중요 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT 절](sql-query-select.md)
