---
title: Azure Cosmos DB에서 SQL 쿼리를 사용 하 여 시작
description: SQL 쿼리 소개
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 87b275806c06443e37e9e92c35a38b4cde378322
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342509"
---
# <a name="getting-started-with-sql-queries"></a>SQL 쿼리를 사용 하 여 시작

Azure Cosmos DB SQL API 계정 언어 SQL (구조적 쿼리)를 사용 하 여 JSON 쿼리 언어로 쿼리 항목을 지원 합니다. Azure Cosmos DB 쿼리 언어의 디자인 목표는 다음과 같습니다.

* 새 쿼리 언어를 고안 하는 대신 가장 익숙하고 많이 사용 쿼리 언어 중 하나로 SQL을 지원 합니다. JSON 항목에 대해 풍부한 쿼리를 위한 공식 프로그래밍 모델을 제공 하는 SQL 합니다.  

* 쿼리 언어에 대 한 기초로 JavaScript의 프로그래밍 모델을 사용 합니다. JavaScript의 형식 시스템, 식 평가 및 함수 호출에는 SQL API의 루트 이며 이러한 루트 관계형 프로젝션, JSON 항목에서 계층적 탐색, 자체 조인, 공간 쿼리 및 전적으로 JavaScript로 작성 된 사용자 정의 함수 (Udf)를 호출 하는 같은 기능에 대 한 자연 스러운 프로그래밍 모델을 제공 합니다.

## <a name="upload-sample-data"></a>샘플 데이터 업로드

SQL API Cosmos DB 계정에서 컨테이너를 만듭니다 `Families`합니다. 컨테이너에 두 개의 간단한 JSON 항목을 만듭니다. 이 데이터 집합을 사용 하 여 Azure Cosmos DB 쿼리 문서에서 대부분의 샘플 쿼리를 실행할 수 있습니다.

### <a name="create-json-items"></a>JSON 항목 만들기

다음 코드는 제품군에 대 한 두 개의 간단한 JSON 항목을 만듭니다. Andersen와 Wakefield 제품군에 대 한 간단한 JSON 항목 해당 부모, 자식 및 애완 동물, 주소 및 등록 정보를 포함 합니다. 첫 번째 항목에는 문자열, 숫자, 부울, 배열 및 중첩 된 속성에 있습니다.


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

두 번째 항목 사용 `givenName` 하 고 `familyName` 대신 `firstName` 및 `lastName`합니다.

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

Azure Cosmos DB SQL 쿼리 언어의 핵심적인 측면을 이해 하려면 JSON 데이터에 대 한 몇 가지 쿼리를 시도 합니다.

다음 쿼리는 항목을 반환 합니다. 여기서는 `id` 일치 필드 `AndersenFamily`합니다. 이므로 `SELECT *` 쿼리, 쿼리의 출력은 완전 한 JSON 항목입니다. SELECT 구문에 대 한 자세한 내용은 참조 하세요. [SELECT 문의](sql-query-select.md)합니다. 

```sql
    SELECT *
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

쿼리 결과 같습니다. 

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

다음 쿼리는 JSON 출력의 서식을 다시 지정을 다른 모양으로 합니다. 쿼리 프로젝션 새 JSON `Family` 두 개의 선택한 필드인을 사용 하 여 개체 `Name` 및 `City`때 주소 도시 상태와 같습니다. 이 경우를 일치 하는 "NY, NY" 합니다.

```sql
    SELECT {"Name":f.id, "City":f.address.city} AS Family
    FROM Families f
    WHERE f.address.city = f.address.state
```

쿼리 결과 같습니다.

```json
    [{
        "Family": {
            "Name": "WakefieldFamily",
            "City": "NY"
        }
    }]
```

다음 쿼리 제품군에서 자식의 모든 지정 된 이름을 반환 합니다. 해당 `id` 일치 `WakefieldFamily`도시별으로 정렬 된 합니다.

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

앞의 예제에서는 Cosmos DB 쿼리 언어의 몇 가지 측면을 보여 줍니다.  

* SQL API JSON 값에 대해 작동 하므로 행과 열 대신 트리 모양의 엔터티를 다룹니다. 같은 임의 깊이의 트리 노드를 참조할 수 있습니다 `Node1.Node2.Node3…..Nodem`의 두 부분으로 구성 참조를 비슷하게 `<table>.<column>` ANSI SQL에서.

* 쿼리 언어 스키마 없는 데이터를 사용 하 여 작동 하므로 형식 시스템은 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과 유효한 JSON 값 이지만 고정 스키마 되도록 보장 되지 않습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템과 식이 JSON 형식만 처리 하도록 제한 됩니다. 자세한 내용은 참조는 [JSON 사양](https://www.json.org/)합니다.  

* Cosmos DB 컬렉션은 JSON 항목의 스키마 없는 컬렉션입니다. 지역 내 및 컨테이너 항목 간 관계는 암시적으로 기본 키 및 외래 키 관계가가 아니라 포함 하 여 캡처됩니다. 이 기능은이 문서의 뒷부분에서 설명 하는 내부 항목 조인에 대 한 중요 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [SELECT 절](sql-query-select.md)
