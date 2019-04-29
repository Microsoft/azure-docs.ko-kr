---
title: Azure Cosmos DB에 대한 SQL 쿼리
description: Azure Cosmos DB에 대한 SQL 구문, 데이터베이스 개념 및 SQL 쿼리를 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mjbrown
ms.openlocfilehash: 04a88558e3aea33c6d99bd0e4f1354c4316f5529
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61054125"
---
# <a name="sql-query-examples-for-azure-cosmos-db"></a>Azure Cosmos DB에 대한 SQL 쿼리 예제

Azure Cosmos DB SQL API 계정 언어 SQL (구조적 쿼리)를 사용 하 여 JSON 쿼리 언어로 쿼리 항목을 지원 합니다. Azure Cosmos DB 쿼리 언어의 디자인 목표는 다음과 같습니다.

* 새 쿼리 언어를 고안 하는 대신 가장 익숙하고 많이 사용 쿼리 언어 중 하나로 SQL을 지원 합니다. JSON 항목에 대해 풍부한 쿼리를 위한 공식 프로그래밍 모델을 제공 하는 SQL 합니다.  

* 쿼리 언어에 대 한 기초로 JavaScript의 프로그래밍 모델을 사용 합니다. JavaScript의 형식 시스템, 식 평가 및 함수 호출에는 SQL API의 루트 이며 이러한 루트 관계형 프로젝션, JSON 항목에서 계층적 탐색, 자체 조인, 공간 쿼리 및 전적으로 JavaScript로 작성 된 사용자 정의 함수 (Udf)를 호출 하는 같은 기능에 대 한 자연 스러운 프로그래밍 모델을 제공 합니다.

이 문서에서는 간단한 JSON 항목의 일부 예제에서는 SQL 쿼리를 통해 설명 합니다. Azure Cosmos DB SQL 언어 구문에 대 한 자세한 내용은 참조 하세요 [SQL 구문 참조](sql-api-query-reference.md)합니다.

## <a id="GettingStarted"></a>SQL 쿼리를 사용 하 여 시작

SQL API Cosmos DB 계정에서 컨테이너를 만듭니다 `Families`합니다. 컨테이너에 두 개의 간단한 JSON 항목을 만들고에 대해 몇 가지 간단한 쿼리를 실행 합니다.

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

다음 쿼리는 항목을 반환 합니다. 여기서는 `id` 일치 필드 `AndersenFamily`합니다. 이므로 `SELECT *` 쿼리, 쿼리의 출력은 완전 한 JSON 항목입니다. SELECT 구문에 대 한 자세한 내용은 참조 하세요. [SELECT 문의](sql-api-query-reference.md#select-query)합니다. 

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

다음 쿼리 제품군에서 자식의 모든 지정 된 이름을 반환 합니다. 해당 `id` 일치 `WakefieldFamily`등급으로 정렬 된 합니다.

```sql
    SELECT c.givenName
    FROM Families f
    JOIN c IN f.children
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.grade ASC
```

결과는 다음과 같습니다.

```json
    [
      { "givenName": "Jesse" },
      { "givenName": "Lisa"}
    ]
```

앞의 예제에서는 Cosmos DB 쿼리 언어의 몇 가지 측면을 보여 줍니다.  

* SQL API JSON 값에 대해 작동 하므로 행과 열 대신 트리 모양의 엔터티를 다룹니다. 같은 임의 깊이의 트리 노드를 참조할 수 있습니다 `Node1.Node2.Node3…..Nodem`의 두 부분으로 구성 참조를 비슷하게 `<table>.<column>` ANSI SQL에서.

* 쿼리 언어 스키마 없는 데이터를 사용 하 여 작동 하므로 형식 시스템은 동적으로 바인딩해야 합니다. 항목에 따라 동일한 식이 다른 형식을 생성할 수 있습니다. 쿼리 결과 유효한 JSON 값 이지만 고정 스키마 되도록 보장 되지 않습니다.  

* Azure Cosmos DB는 엄격한 JSON 항목만 지원합니다. 형식 시스템과 식이 JSON 형식만 처리 하도록 제한 됩니다. 자세한 내용은 참조는 [JSON 사양](https://www.json.org/)합니다.  

* Cosmos DB 컬렉션은 JSON 항목의 스키마 없는 컬렉션입니다. 지역 내 및 컨테이너 항목 간 관계는 암시적으로 기본 키 및 외래 키 관계가가 아니라 포함 하 여 캡처됩니다. 이 기능은이 문서의 뒷부분에서 설명 하는 내부 항목 조인에 대 한 중요 합니다.

## <a id="SelectClause"></a>SELECT 절

모든 쿼리는 SELECT 절과 선택적 FROM 이루어져 및 ANSI SQL 표준에 따라 WHERE 절. 일반적으로 FROM 절에서 원본 열거 되 고 WHERE 절은 JSON 항목의 하위 집합을 검색 하기 위해 원본에 필터를 적용 합니다. 다음 SELECT 절은 select 목록에 요청된 된 JSON 값을 프로젝션합니다. 구문에 대 한 자세한 내용은 참조 하세요. [SELECT 문의](sql-api-query-reference.md#select-query)합니다.

다음 SELECT 쿼리 예제에서는 반환 `address` 에서 `Families` 인 `id` 일치 `AndersenFamily`:

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

## <a id="EscapingReservedKeywords"></a>따옴표 붙은 속성 접근자
따옴표 붙은 속성 연산자 사용 하 여 속성에 액세스할 수 있습니다. 예를 들어 `SELECT c.grade` and `SELECT c["grade"]` 와 동일합니다. 이 구문은 공백, 특수 문자가 포함 되어 있거나 SQL 키워드 또는 예약어와 이름이 같은 속성을 이스케이프 하는 데 유용 합니다.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

## <a name="nested-properties"></a>중첩 속성

다음 예제에서는 두 개의 중첩 된 속성을 프로젝션 `f.address.state` 고 `f.address.city`입니다.

```sql
    SELECT f.address.state, f.address.city
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "state": "WA",
      "city": "Seattle"
    }]
```

## <a name="json-expressions"></a>JSON 식

프로젝션은 다음 예와에서 같이도 JSON 식도 지원:

```sql
    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle",
        "name": "AndersenFamily"
      }
    }]
```

앞의 예제에서 SELECT 절 JSON 개체를 만드는 데 필요한 및 암시적 인수 변수 이름을 절을 사용 하 여 키가 없습니다.에서 제공 하는 샘플 이므로 `$1`합니다. 다음 쿼리는 두 개의 암시적 인수 변수를 반환 합니다. `$1` 고 `$2`입니다.

```sql
    SELECT { "state": f.address.state, "city": f.address.city },
           { "name": f.id }
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "$1": {
        "state": "WA",
        "city": "Seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="ValueKeyword"></a>VALUE 키워드

VALUE 키워드에만 JSON 값을 반환 하는 방법을 제공 합니다. 예를 들어 아래 표시 된 쿼리 반환 스칼라 식 `"Hello World"` 대신 `{$1: "Hello World"}`:

```sql
    SELECT VALUE "Hello World"
```

다음 쿼리는 반환 하지 않고 JSON 값을 `address` 레이블:

```sql
    SELECT VALUE f.address
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan",
        "city": "NY"
      }
    ]
```

다음 예제에서는 JSON 기본 값 (JSON 트리 리프 수준)를 반환 하는 방법을 보여 줍니다.


```sql
    SELECT VALUE f.address.state
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      "WA",
      "NY"
    ]
```

## <a name="aliasing"></a>별칭 지정

하면 명시적으로 별칭 쿼리에서 값입니다. 쿼리에 동일한 이름 가진 두 속성이 별칭 사용 하 여 프로젝션된 된 결과에서 명확 하 게 하는 되도록 속성 중 하나 또는 모두를 이름을 바꿉니다.

두 번째 값을 프로젝션 할 때 다음 예제에 표시 된 대로 별칭 지정에 사용 되는 키워드는 선택 사항이 as `NameInfo`:

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a id="FromClause"></a>FROM 절

FROM (`FROM <from_specification>`) 소스를 필터링 / 나중에 쿼리에서 프로젝션 하지 않는 한 절은 선택 사항입니다. 구문에 대 한 자세한 내용은 참조 하세요. [구문에서](sql-api-query-reference.md#bk_from_clause)합니다. 같은 쿼리 `SELECT * FROM Families` 전체를 열거 `Families` 컨테이너입니다. 컨테이너 이름을 사용 하는 대신 컨테이너에 대 한 특수 식별자 ROOT 이용할 수 있습니다.

FROM 절 쿼리당 다음 규칙을 적용 합니다.

* 컨테이너를 별칭으로 `SELECT f.id FROM Families AS f` 또는 간단히 `SELECT f.id FROM Families f`로 지정할 수 있습니다. 여기 `f` 의 별칭인 `Families`합니다. 별칭에는 선택적 키워드는 식별자는  

* 별칭으로 지정한 후 원래 원본 이름에 바인딩할 수 없습니다. 예를 들어 `SELECT Families.id FROM Families f` 구문이 잘못 된 때문에 식별자 `Families` 별칭이 지정 되었으며 더 이상 확인할 수 없습니다.  

* 모든 참조 된 속성은 정규화 된 이름, 엄격한 스키마 준수 없을 경우에서 모호한 바인딩을 방지 하려면 이어야 합니다. 예를 들어 `SELECT id FROM Families f` 구문이 잘못 된 때문에 속성 `id` 바인딩되어 있지 않은 합니다.

### <a name="get-subitems-by-using-the-from-clause"></a>FROM 절을 사용 하 여 하위 항목 가져오기

FROM 절을 더 작은 하위 집합으로 소스를 줄일 수 있습니다. 각 항목의 하위 트리만 열거 하려면 경우 다음 예와에서 같이 원본 될 수 있습니다.

```sql
    SELECT *
    FROM Families.children
```

결과는 다음과 같습니다.

```json
    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

앞의 쿼리에서 배열을 원본으로 사용 되는 하지만 원본으로 개체를 사용할 수도 있습니다. 쿼리 결과에 포함할 원본에 유효 하 고 정의 된 JSON 값을 고려합니다. 다음 예제에서는 제외 되 게 `Families` 없는 `address.state` 값입니다.

```sql
    SELECT *
    FROM Families.address.state
```

결과는 다음과 같습니다.

```json
    [
      "WA",
      "NY"
    ]
```

## <a id="WhereClause"></a>WHERE 절

선택 사항인 WHERE 절 (`WHERE <filter_condition>`) 조건을 지정 소스 JSON 항목 결과에 포함 하려면 쿼리에 대 한 충족 해야 합니다. JSON 항목을 지정된 된 조건을 평가 해야 `true` 결과 대해 고려해 야 합니다. 인덱스 계층 결과 포함 될 수 있는 소스 항목의 가장 작은 하위 집합을 확인 하려면 WHERE 절을 사용 합니다. 구문에 대 한 자세한 내용은 참조 하세요. [WHERE 구문을](sql-api-query-reference.md#bk_where_clause)합니다.

다음 쿼리를 포함 하는 요청 항목을 `id` 값이 속성의 `AndersenFamily`합니다. 제외 되지 않은 모든 항목을 `id` 속성 또는 값이 일치 하지 않습니다 `AndersenFamily`합니다.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

결과는 다음과 같습니다.

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>WHERE 절에 스칼라 식

앞의 예제는 단순한 같음 쿼리를 보여 주었습니다. SQL API도 다양 한 지원 [스칼라 식](#scalar-expressions)합니다. 가장 일반적으로 사용되는 식은 이항 및 단항 식입니다. 소스 JSON 개체의 속성 참조도 유효한 식입니다.

다음 지원 되는 이항 연산자를 사용할 수 있습니다.  

|**연산자 유형**  | **값** |
|---------|---------|
|산술 | +,-,*,/,% |
|비트    | \|, &, ^, <<, >>, >>>(0 채우기 오른쪽 시프트) |
|논리    | AND, OR, NOT      |
|비교 | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\|(연결) |

다음 쿼리는 이항 연산자를 사용합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

단항 연산자를 사용할 수도 있습니다 +,-, ~, 및 쿼리를 다음 예제에 표시 된 대로 되지 않습니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

또한 쿼리에서 속성 참조를 사용할 수 있습니다. 예를 들어 `SELECT * FROM Families f WHERE f.isRegistered` 속성을 포함 하는 JSON 항목 반환 `isRegistered` 값과 같은 `true`합니다. 와 같은 다른 값을 `false`, `null`를 `Undefined`, `<number>`를 `<string>`를 `<object>`, 또는 `<array>`, 결과에서 항목을 제외 합니다. 

### <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

와 같은 비교 연산자에 대 한 `>`, `>=`를 `!=`를 `<`, 및 `<=`, 비교 형식 간에 또는 두 개체 또는 배열을 생성 `Undefined`합니다.  

스칼라 식의 결과 이면 `Undefined`, 때문에 항목이 결과에 포함 되어 있지 `Undefined` 와 같지 않은 `true`합니다.

### <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표에서 이러한 연산자의 논리적 진위 표가 보여 줍니다.

**OR 연산자**

| 또는 | True  | 거짓 | Undefined |
| --- | --- | --- | --- |
| True  |True |True |True |
| False |True |거짓 |Undefined |
| Undefined |True  |Undefined |Undefined |

**AND 연산자**

| AND | True  | 거짓 | Undefined |
| --- | --- | --- | --- |
| True  |True |거짓 |Undefined |
| 거짓 |False |False |거짓 |
| Undefined |Undefined |거짓 |Undefined |

**NOT 연산자**

| NOT |  |
| --- | --- |
| True  |False |
| False |True  |
| Undefined |Undefined |

## <a name="between-keyword"></a>BETWEEN 키워드

ANSI SQL 에서처럼 문자열 또는 숫자 값의 범위에 대 한 쿼리를 표현 하는 BETWEEN 키워드를 사용할 수 있습니다. 예를 들어 다음 쿼리는 첫 번째 자식의 등급이 1-5를 포괄 하는 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

와 달리 ANSI sql에서 사용할 수 있습니다도 BETWEEN 절 다음 예제와 같이 FROM 절에서.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

ANSI SQL과 달리 SQL api에서 혼합 형식의 속성에 대해 범위 쿼리를 표현할 수 있습니다. 예를 들어 `grade` ' 좋아요 '를 숫자 않을 `5` 일부 항목에 같은 문자열 `grade4` 다른 합니다. 이러한 경우 JavaScript에서와 같이 두 가지 유형 간의 비교 결과 `Undefined`이므로 항목을 건너뜁니다.

> [!TIP]
> 쿼리 실행 시간을 단축 BETWEEN 절에서 필터링 하는 모든 숫자 속성 또는 경로 대 한 범위 인덱스 형식을 사용 하는 인덱싱 정책을 만듭니다.

## <a name="in-keyword"></a>IN 키워드

IN 키워드를 사용 하 여 지정된 된 값 목록에 있는 값이 일치 하는지 확인 합니다. 다음 쿼리는 모든 제품군 항목을 반환 하는 예를 들어 여기서는 `id` 됩니다 `WakefieldFamily` 또는 `AndersenFamily`합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

다음 예제에서는 상태가 지정된 된 값의 모든 항목을 반환 합니다.

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목 프로젝트입니다. 사용할 경우 프로젝션되는 유일한 필드여야 같은 쿼리 `SELECT * FROM Families f` 유효 하지만 `SELECT VALUE * FROM Families f` 고 `SELECT *, f.id FROM Families f` 올바르지 않습니다. [먼저이 문서의 쿼리](#query-the-json-items) 사용 된 * 연산자입니다. 

## <a name="-and--operators"></a>? 및?? 연산자

3 항 (?)를 사용 하 고 Coalesce (?) 연산자는 같은 프로그래밍 언어 에서처럼 조건식을 작성할 수 있습니다 C# 및 JavaScript입니다. 

사용할 수는? 즉석에서 새로운 JSON 속성을 생성 하는 연산자입니다. 예를 들어 다음 쿼리 분류에 등급 수준을 `elementary` 또는 `other`:

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

에 대 한 호출을 중첩할 수도 있습니다는? 다음 쿼리에서와 같이 연산자: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

다른 쿼리 연산자와 마찬가지로? 연산자는 참조 된 속성이 손실 또는 비교할 형식이 다른 경우 항목을 제외 합니다.

사용 된?? 반 구조화 된 또는 혼합 형식 데이터에 대해 쿼리할 때 항목의 속성을 효율적으로 확인 하는 연산자입니다. 예를 들어 다음 쿼리에서 반환 `lastName` 있는 경우 또는 `surname` 경우 `lastName` 없는 합니다.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a id="TopKeyword"></a>TOP 연산자

TOP 키워드 첫 번째 개체가 반환 `N` 정의 되지 않은 순서로 쿼리 결과의 수입니다. 모범 사례로, 상위 ORDER BY 절을 사용 하 여 첫 번째 결과 제한 하려면 사용 `N` 정렬 된 값의 수입니다. 이러한 두 개의 절을 결합 하는 유일한 방법은 예측 가능한 방식으로 위쪽에 영향을 줍니다는 행을 나타냅니다. 

다음 예제와 같이 상수 값, 또는 매개 변수가 있는 쿼리를 사용 하 여 변수 값을 사용 하 여 TOP을 사용할 수 있습니다. 자세한 내용은 참조는 [매개 변수가 있는 쿼리](#parameterized-queries) 섹션입니다.

```sql
    SELECT TOP 1 *
    FROM Families f
```

결과는 다음과 같습니다.

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

## <a id="OrderByClause"></a>ORDER BY 절

ANSI SQL 에서처럼 쿼리에 선택적 ORDER BY 절을 포함할 수 있습니다. 선택적 ASC 또는 DESC 인수를 오름차순 또는 내림차순으로 결과 검색할지 여부를 지정 합니다. ASC 기본값입니다.

예를 들어 다음과 같습니다. 오름차순 상주 하는 도시의 이름으로 가족을 검색 하는 쿼리

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

다음 쿼리는 검색 제품군 `id`의 해당 항목 만든 날짜 순으로 합니다. 항목 `creationDate` 를 나타내는 숫자를 *epoch 시간*, 또는 1970 초에서 1 월 1 일 이후 경과 된 시간입니다.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```
## <a name="scalar-expressions"></a>스칼라 식

SELECT 절에 상수, 산술 식 및 논리 식과 같은 스칼라 식도 지원합니다. 다음 쿼리는 스칼라 식:


```sql
    SELECT ((2 + 11 % 7)-2)/3
```

결과는 다음과 같습니다.

```json
    [{
      "$1": 1.33333
    }]
```

다음 쿼리를 스칼라 식의 결과 부울 값:


```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="object-and-array-creation"></a>개체 및 배열 만들기

SQL API의 주요 기능은 배열 및 개체 만들기입니다. 이전 예제에서는 새 JSON 개체를 만든 `AreFromSameCityState`합니다. 또한 다음 예와에서 같이 배열을 생성할 수 있습니다.


```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```


## <a id="Iteration"></a>반복

SQL API에 대 한 추가 FROM 소스에서 IN 키워드를 통해 새 구문을 사용 하 여 JSON 배열 반복 지원을 제공 합니다. 다음 예제에서는

```sql
    SELECT *
    FROM Families.children
```

결과는 다음과 같습니다.

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

다음 쿼리에서 반복을 수행 `children` 에 `Families` 컨테이너입니다. 출력 배열의 이전 쿼리와 다릅니다. 이 예제에서는 분할 `children`, 고 결과를 단일 배열로 평면화 합니다.  

```sql
    SELECT *
    FROM c IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

필터링 할 수 있습니다 다음 예제에 표시 된 대로 배열의 각 개별 항목에 추가 합니다.

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

결과는 다음과 같습니다.

```json
    [{
      "givenName": "Lisa"
    }]
```

배열 반복의 결과 대해 집계할 수 있습니다. 예를 들어 다음 쿼리는 모든 가족 간의 자식 수를 셉니다.

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a id="Joins"></a>조인

관계형 데이터베이스 테이블 간 조인에는 정규화 된 스키마 설계의 필연적인 논리적 결과입니다. SQL API는 논리는 스키마 없는 항목의 정규화 되지 않은 데이터 모델을 사용 하는 반면에 해당 하는 *자체 조인을*합니다.

언어 구문을 지 원하는 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`합니다. 이 쿼리를 사용 하 여 튜플 집합을 반환 합니다 `N` 값입니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다. 즉, 이 쿼리는 조인에 참여하는 세트의 전체 교차곱을 수행합니다.

다음 예제는 JOIN 절의 작동 방식을 보여 줍니다. 다음 예제에서는 원본의 각 항목의 교차곱 이후 결과 비어 있는 경우에 빈 집합이 비어 있습니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

결과는 다음과 같습니다.

```json
    [{
    }]
```

다음 예제에서는 조인에서는 두 JSON 개체를 항목 루트 간의 교차곱 `id` 하며 `children` 조인이 수행 합니다. 팩트는 `children` 단일 루트를 다루는 때문에 배열을 조인에는 효과적이 지 않습니다는 `children` 배열입니다. 결과는 배열 사용 하 여 각 항목의 교차곱 정확히 하나의 항목을 생성 하기 때문에 두 개의 결과 포함 합니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

다음 예제에서는 보다 기본적인 조인을 보여 줍니다.

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

결과는 다음과 같습니다.

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

JOIN 절을 FROM 원본이 반복기입니다. 따라서 앞의 예제의 흐름은 다음과 같습니다.  

1. 각 자식 요소를 확장 `c` 배열의 합니다.
2. 항목의 루트나 교차곱을 적용 `f` 각 자식 요소를 사용 하 여 `c` 결합 하는 첫 번째 단계입니다.
3. 마지막으로, 루트 개체를 프로젝트 `f` `id` 속성만 프로젝션 합니다.

첫 번째 항목인 `AndersenFamily`, 하나만 포함 되어 `children` 요소를 결과 집합에 단일 개체만 포함 하도록 합니다. 두 번째 항목인 `WakefieldFamily`에 두 개의 `children`교차곱 마다 하나씩 두 개의 개체를 생성 하므로 `children` 요소. 교차곱에서 예상한 대로 두 항목의 루트 필드는 동일합니다.

JOIN 절의 진정한 유용성 프로젝션 하기 어려운 있는 모양 교차곱에서 튜플을 형성할 하는 것입니다. 사용자에 의해 전체 튜플에서 충족 되는 조건을 선택할 수 있도록 튜플 조합에 대 한 필터 아래 예제입니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

결과는 다음과 같습니다.

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

위 예의 다음 확장에는 이중 조인을 수행 합니다. 다음 의사 코드로 교차곱을 볼 수 있습니다.

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` 교차곱 행이 하나씩 있도록 애완 동물 한 마리를 키우는 자식 한 명이 (1\*1\*1)이 제품군에서입니다. `WakefieldFamily` 자식이 두 사용자 중 하나 에서만 애완 동물에 했으나 해당 자식 두 마리 있습니다. 이 제품군에 대 한 교차곱 1\*1\*2 = 2 개의 행입니다.

다음 예제에서는 다른 필터에 있는지 `pet`, 않습니다 애완 동물 이름 튜플을 모두 제외는 `Shadow`합니다. 배열, 튜플 요소 중 하나에 대 한 필터에서에서 튜플을 작성 하 고 요소 조합을 프로젝션 수 있습니다.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

결과는 다음과 같습니다.

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

## <a id="UserDefinedFunctions"></a>사용자 정의 함수 (Udf)

SQL API는 사용자 정의 함수 (Udf)에 대 한 지원을 제공합니다. 스칼라 Udf를 사용 하 여 0 개 또는 많은 인수를 전달할 수 있으며 단일 인수 결과 반환할 수 있습니다. API에는 각 인수가 유효한 JSON 값인지 확인 합니다.  

API는 Udf를 사용 하 여 사용자 지정 응용 프로그램 논리를 지원 하기 위해 SQL 구문이 확장 됩니다. SQL API를 사용 하 여 Udf를 등록 하 고 SQL 쿼리에서 참조할 수 있습니다. 실제로 UDF는 쿼리에서 호출되도록 설계되었습니다. 그 결과, Udf 없는 저장된 프로시저 및 트리거와 같은 다른 JavaScript 형식과 마찬가지로 컨텍스트 개체에 액세스 합니다. 쿼리는 읽기 전용 및 기본 또는 보조 복제본에서 실행할 수 있습니다. Udf의 경우 다른 JavaScript 형식과 달리 보조 복제본에서 실행 하도록 설계 되었습니다.

다음 예제에서는 Cosmos DB 데이터베이스의 항목 컨테이너에서 UDF를 등록합니다. 이 예에서는 이름이 UDF 만듭니다 `REGEX_MATCH`합니다. 두 JSON 문자열 값을 받아들이는 `input` 및 `pattern`, JavaScript의를 사용 하는 경우 두 번째에서 지정 된 패턴과 일치 하는 첫 번째 항목 검사 `string.match()` 함수입니다.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

이제이 UDF를 사용 하 여 쿼리 프로젝션에서. 대/소문자 구분 접두사를 사용 하 여 Udf를 한 정해야 `udf.` 쿼리 내에서 호출 하는 경우.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

결과는 다음과 같습니다.

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

로 한정 된 UDF를 사용할 수는 `udf.` 다음 예제와 같이 필터 내부 접두사:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

결과는 다음과 같습니다.

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

기본적으로 Udf는 프로젝션과 필터 둘 다에서 사용할 수 있는 유효한 스칼라 식입니다.

Udf의 기능을 확장 하려면 조건부 논리를 사용 하 여 예제를 보여:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

다음 예제에서는 UDF를 실행 합니다.

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

결과는 다음과 같습니다.

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

속성 참조 매개 변수 UDF에서 JSON 값에 사용할 수 없는 경우 매개 변수 한 것으로 간주 정의 되지 않은 및 UDF 호출을 건너뜁니다. 마찬가지로, UDF 결과가 정의 되지 않으면, 결과에 하지 포함 됩니다.

앞의 예제에서는 표시 된 대로 Udf는 SQL API를 사용 하 여 JavaScript 언어의 기능을 통합 합니다. Udf는 복잡 한 절차적 조건부 논리 기본 제공 된 JavaScript 런타임 기능을 활용 하 여 작업을 수행 하는 풍부한 프로그래밍 가능 인터페이스를 제공 합니다. SQL API는 인수를 Udf 각 원본 항목에 대 한 현재 위치 또는 SELECT 절에서의 처리 단계입니다. 결과 전체 실행 파이프라인에 원활 하 게 통합 됩니다. 요약 하면 Udf는 쿼리의 일부로 복잡 한 비즈니스 논리를 수행 하기에 좋은 도구입니다.

## <a id="Aggregates"></a>집계 함수

SELECT 절에는 값 집합에서 계산을 수행 하 고 단일 값을 반환 하는 집계 함수입니다. 다음 쿼리 내에서 항목의 수를 반환 하는 예를 들어를 `Families` 컨테이너:

```sql
    SELECT COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [{
        "$1": 2
    }]
```

VALUE 키워드를 사용 하 여 집계의 스칼라 값만을 반환할 수도 있습니다. 예를 들어 다음 쿼리는 단일 숫자로 값의 수를 반환합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

결과는 다음과 같습니다.

```json
    [ 2 ]
```

또한 필터를 사용 하 여 집계를 결합할 수 있습니다. 다음 쿼리 주소 상태를 사용 하 여 항목의 수를 반환 하는 예를 들어 `WA`합니다.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

결과는 다음과 같습니다.

```json
    [ 1 ]
```

SQL API는 다음과 같은 집계 함수를 지원합니다. 숫자 값에서 작동 하는 SUM 및 AVG 및 COUNT, MIN 및 MAX 작업 숫자, 문자열, 부울 및 null 합니다.

| 함수 | 설명 |
|-------|-------------|
| 개수 | 식에서 항목 수를 반환합니다. |
| 합계   | 식에서 모든 값의 합계를 반환합니다. |
| 최소   | 식에서 최소값을 반환합니다. |
| 최대   | 식에서 최대값을 반환합니다. |
| 평균   | 식에서 평균값을 반환합니다. |

배열 반복의 결과 대해 집계할 수 있습니다. 자세한 내용은 참조는 [반복](#Iteration) 섹션입니다.

> [!NOTE]
> Azure portal의 데이터 탐색기에서 집계 쿼리 하나의 쿼리 페이지에 대해 부분 결과 집계할 수 있습니다. SDK는 모든 페이지에 걸쳐 단일 누적 값을 생성합니다. 코드를 사용 하 여 집계 쿼리를 수행 하려면.NET SDK 1.12.0,.NET Core SDK 1.1.0 또는 Java SDK 1.9.5 이상이 있습니다.
>

## <a id="BuiltinFunctions"></a>기본 제공 함수

또한 cosmos DB는 사용자 정의 함수 (Udf) 처럼 쿼리 내에서 사용할 수 있는 일반적인 작업에 대 한 다양 한 기본 제공 함수를 지원 합니다.

| 함수 그룹 | 작업 |
|---------|----------|
| 수치 연산 함수 | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| 형식 검사 함수 | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| 문자열 함수 | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| 배열 함수 | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH 및 ARRAY_SLICE |
| 공간 함수 | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

사용자 정의 함수 (UDF) 기본 제공 함수를 이제 사용할 수 있는, 현재 사용 중인 경우에 해당 기본 제공 함수를 더 빨리 실행 되 고 보다 효율적인 됩니다.

Cosmos DB 함수와 ANSI SQL 간의 주요 차이점은 Cosmos DB 함수는 스키마 없는 및 혼합 된 스키마 데이터에서 잘 작동 하도록 설계 되었다는 합니다. 예를 들어 속성이 누락 되었거나 숫자가 아닌 값과 같은 경우 `unknown`, 오류를 반환 하는 대신 항목을 건너뜁니다.

### <a name="mathematical-functions"></a>수치 연산 함수

수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다. 다음은 지원되는 기본 제공 수치 연산 함수 표입니다.

| 사용 현황 | 설명 |
|----------|--------|
| ABS (num_expr) | 지정한 숫자 식의 절대(양수) 값을 반환합니다. |
| CEILING (num_expr) | 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다. |
| FLOOR (num_expr) | 지정한 숫자 식보다 작거나 같은 가장 큰 정수 값을 반환합니다. |
| EXP (num_expr) | 지정한 숫자 식의 지수를 반환합니다. |
| LOG (num_expr, 기본) | 지정한 숫자 식 또는 지정된 된 밑을 사용 하 여 로그의 자연 로그를 반환 합니다. |
| LOG10 (num_expr) | 지정한 숫자 식의 상용 로그를 반환합니다. |
| ROUND (num_expr) | 가장 가까운 정수 값으로 반올림한 숫자 값을 반환합니다. |
| TRUNC (num_expr) | 가장 가까운 정수 값으로 버린 숫자 값을 반환합니다. |
| SQRT (num_expr) | 지정한 숫자 식의 제곱근을 반환합니다. |
| SQUARE (num_expr) | 지정한 숫자 식의 거듭제곱을 반환합니다. |
| POWER (num_expr, num_expr) | 지정한 값까지 지정한 숫자 식의 거듭제곱을 반환합니다. |
| SIGN (num_expr) | 지정한 숫자 식의 부호 값(-1, 0, 1)을 반환합니다. |
| ACOS (num_expr) | 코사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크코사인이라고도 합니다. |
| ASIN (num_expr) | 사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 이 함수를 아크사인이라고도 합니다. |
| ATAN (num_expr) | 탄젠트 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 이 함수는 아크탄젠트를 라고도 합니다. |
| ATN2 (num_expr) | x와 y가 두 지정된 float 식의 값인 양의 x축과 원점부터 (y, x) 지점의 선 사이의 라디안에서 각도를 반환합니다. |
| COS (num_expr) | 지정된 식의 라디안에서 지정된 각도의 삼각 코사인을 반환합니다. |
| COT (num_expr) | 지정된 숫자 식의 라디안에서 지정된 각도의 삼각 코탄젠트를 반환합니다. |
| DEGREES (num_expr) | 라디안에서 지정된 각도로 해당하는 각도를 반환합니다. |
| PI () | PI의 상수 값을 반환합니다. |
| RADIANS (num_expr) | 숫자 식을 단위로 입력하면 라디안을 반환합니다. |
| SIN (num_expr) | 지정된 식의 라디안에서 지정된 각도의 삼각 사인을 반환합니다. |
| TAN (num_expr) | 지정된 식에서 입력 식의 탄젠트를 반환합니다. |

다음 예제와 같이 쿼리를 실행할 수 있습니다.

```sql
    SELECT VALUE ABS(-4)
```

결과는 다음과 같습니다.

```json
    [4]
```

### <a name="type-checking-functions"></a>형식 검사 함수

형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 확인할 수 있습니다. 변수 이거나 알 수 있을 때 즉시 항목 내의 속성 형식을 결정할 형식 검사 함수를 사용할 수 있습니다. 지원 되는 기본 제공 형식 검사 함수 표는 다음과 같습니다.

| **사용 현황** | **설명** |
|-----------|------------|
| [IS_ARRAY (expr)](sql-api-query-reference.md#bk_is_array) | 값의 형식이 배열인지 여부를 나타내는 부울을 반환합니다. |
| [IS_BOOL (expr)](sql-api-query-reference.md#bk_is_bool) | 값의 형식이 부울인지 여부를 나타내는 부울을 반환합니다. |
| [IS_NULL (expr)](sql-api-query-reference.md#bk_is_null) | 값의 형식이 null인지 여부를 나타내는 부울을 반환합니다. |
| [IS_NUMBER (expr)](sql-api-query-reference.md#bk_is_number) | 값의 형식이 숫자인지 여부를 나타내는 부울을 반환합니다. |
| [IS_OBJECT (expr)](sql-api-query-reference.md#bk_is_object) | 값의 형식이 JSON 개체인지 여부를 나타내는 부울을 반환합니다. |
| [IS_STRING (expr)](sql-api-query-reference.md#bk_is_string) | 값의 형식이 문자열인지 여부를 나타내는 부울을 반환합니다. |
| [IS_DEFINED (expr)](sql-api-query-reference.md#bk_is_defined) | 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다. |
| [IS_PRIMITIVE (expr)](sql-api-query-reference.md#bk_is_primitive) | 문자열, 숫자, 부울 또는 null 값의 형식 인지 여부를 나타내는 부울을 반환 합니다. |

이러한 함수를 사용 하 여 다음 예와 같이 쿼리를 실행할 수 있습니다.

```sql
    SELECT VALUE IS_NUMBER(-4)
```

결과는 다음과 같습니다.

```json
    [true]
```

### <a name="string-functions"></a>문자열 함수

다음 스칼라 함수는 문자열 입력된 값에 대 한 작업을 수행 하 고 문자열, 숫자 또는 부울 값을 반환 합니다. 기본 제공 문자열 함수의 테이블은 다음과 같습니다.

| 사용 현황 | 설명 |
| --- | --- |
| [LENGTH (str_expr)](sql-api-query-reference.md#bk_length) | 지정한 문자열 식의 문자 수를 반환합니다. |
| [CONCAT (str_expr, str_expr [, str_expr])](sql-api-query-reference.md#bk_concat) | 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다. |
| [SUBSTRING (str_expr, num_expr, num_expr)](sql-api-query-reference.md#bk_substring) | 문자열 식의 일부를 반환합니다. |
| [STARTSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_startswith) | 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다. |
| [ENDSWITH (str_expr, str_expr)](sql-api-query-reference.md#bk_endswith) | 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다. |
| [CONTAINS (str_expr, str_expr)](sql-api-query-reference.md#bk_contains) | 첫 번째 문자열 식이 두 번째를 포함하는지를 나타내는 부울 값을 반환합니다. |
| [INDEX_OF (str_expr, str_expr)](sql-api-query-reference.md#bk_index_of) | 두 번째 첫 번째로 나타나는 시작 위치를 반환 문자열을 찾을 수 없으면-1 또는 첫 번째 지정 된 문자열 식 내에서 식 문자열입니다. |
| [LEFT (str_expr, num_expr)](sql-api-query-reference.md#bk_left) | 지정된 수의 문자로 문자열의 왼쪽 부분을 반환합니다. |
| [RIGHT (str_expr, num_expr)](sql-api-query-reference.md#bk_right) | 지정된 수의 문자로 문자열의 오른쪽 부분을 반환합니다. |
| [LTRIM (str_expr)](sql-api-query-reference.md#bk_ltrim) | 선행 공백을 제거한 후에 문자열 식을 반환합니다. |
| [RTRIM (str_expr)](sql-api-query-reference.md#bk_rtrim) | 후행 공백을 잘라낸 후에 문자열 식을 반환합니다. |
| [LOWER (str_expr)](sql-api-query-reference.md#bk_lower) | 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다. |
| [UPPER (str_expr)](sql-api-query-reference.md#bk_upper) | 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다. |
| [REPLACE (str_expr, str_expr, str_expr)](sql-api-query-reference.md#bk_replace) | 지정된 문자열 값의 모든 항목을 다른 문자열 값으로 바꿉니다. |
| [REPLICATE (str_expr, num_expr)](sql-api-query-reference.md#bk_replicate) | 문자열 값을 지정한 횟수 만큼 반복합니다. |
| [REVERSE (str_expr)](sql-api-query-reference.md#bk_reverse) | 문자열 값의 순서와 반대로 반환합니다. |

이러한 함수를 사용 하는 제품군을 반환 하는 다음과 같은 쿼리를 실행할 수 `id` 대문자:

```sql
    SELECT VALUE UPPER(Families.id)
    FROM Families
```

결과는 다음과 같습니다.

```json
    [
        "WAKEFIELDFAMILY",
        "ANDERSENFAMILY"
    ]
```

또는이 예제에서와 같은 문자열을 연결 합니다.

```sql
    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "Seattle,WA"
    }]
```

또한 필터링 하도록 WHERE 절의 결과 같은 다음 예제에서에서 문자열 함수를 사용할 수 있습니다.

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]
```

### <a name="array-functions"></a>배열 함수

다음 스칼라 함수는 배열 입력된 값에 대 한 작업을 수행 하 고 숫자, 부울 또는 배열 값을 반환 합니다. 기본 제공 배열 함수의 테이블은 다음과 같습니다.

| 사용 현황 | 설명 |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](sql-api-query-reference.md#bk_array_length) |지정된 배열 식의 요소 수를 반환합니다. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](sql-api-query-reference.md#bk_array_concat) |둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다. |
| [ARRAY_CONTAINS (arr_expr, expr [, bool_expr])](sql-api-query-reference.md#bk_array_contains) |지정된 값이 배열에 포함되는지를 나타내는 부울 값을 반환합니다. 전체 또는 부분 일치 항목인지를 지정할 수 있습니다. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](sql-api-query-reference.md#bk_array_slice) |배열 식의 일부를 반환합니다. |

배열 함수를 사용 하 여 JSON 내 배열을 조작 합니다. 예를 들어, 다음은 모든 항목을 반환 하는 쿼리입니다 `id`s 하나에 `parents` 는 `Robin Wakefield`: 

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily"
    }]
```

배열 내의 요소와 일치하는 부분 조각을 지정할 수 있습니다. 다음 쿼리는 모든 항목을 찾습니다 `id`포함 된 `parents` 사용 하 여 합니다 `givenName` 의 `Robin`:

```sql
    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin" }, true)
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily"
    }]
```

수를 가져오는 ARRAY_LENGTH를 사용 하는 또 다른 예가 `children` 패밀리당:

```sql
    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]
```

### <a name="spatial-functions"></a>공간 함수

Cosmos DB는 지리 공간 쿼리를 위한 다음 Open Geospatial Consortium (OGC) 기본 제공 함수를 지원 합니다. 

| 사용 현황 | 설명 |
| --- | --- |
| ST_DISTANCE (point_expr, point_expr) | 두 GeoJSON 사이의 거리를 반환 `Point`하십시오 `Polygon`, 또는 `LineString` 식입니다. |
| T_WITHIN (point_expr, polygon_expr) | 나타내는 부울 식을 반환 여부를 첫 번째 GeoJSON 개체 (`Point`, `Polygon`, 또는 `LineString`)가 두 번째 GeoJSON 개체 내에 (`Point`, `Polygon`, 또는 `LineString`). |
| ST_INTERSECTS(spatial_expr, spatial_expr) | 두 GeoJSON 개체를 지정 하는지 여부를 나타내는 부울 식을 반환 합니다 (`Point`, `Polygon`, 또는 `LineString`) 교차 합니다. |
| ST_ISVALID | 나타내는 부울 값을 반환 하는지 여부를 지정 된 GeoJSON `Point`, `Polygon`, 또는 `LineString` 식이 유효 합니다. |
| ST_ISVALIDDETAILED | 경우 부울 값이 포함 된 JSON 값을 반환 합니다. 지정 된 GeoJSON `Point`, `Polygon`, 또는 `LineString` 식이 유효 유효 하지 않은 경우 및 그 이유는 문자열 값으로. |

공간 데이터에 대 한 근접 쿼리를 수행할 공간 함수를 사용할 수 있습니다. 예를 들어, 다음은 30km ST_DISTANCE 기본 제공 함수를 사용 하 여 지정된 된 위치 내에 있는 모든 제품군 항목을 반환 하는 쿼리입니다.

```sql
    SELECT f.id
    FROM Families f
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000
```

결과는 다음과 같습니다.

```json
    [{
      "id": "WakefieldFamily"
    }]
```

Cosmos DB의 지리 공간 지원에 대한 자세한 내용은 [Azure Cosmos DB에서 지리 공간 데이터 작업](geospatial.md)을 참조하세요. 

## <a name="parameterized-queries"></a>매개 변수가 있는 쿼리

Cosmos DB는 익숙한 @ 표기법으로 표현 된 매개 변수가 있는 쿼리를 지원 합니다. 매개 변수가 있는 SQL 강력한 처리 및 사용자 입력의 이스케이프를 제공 하 고 SQL 주입을 통해 데이터가 실수로 노출 되는 것을 금지 합니다.

예를 들어, 사용 하는 쿼리를 작성할 수 있습니다 `lastName` 하 고 `address.state` 매개 변수로 다양 한 값에 대해 실행 `lastName` 및 `address.state` 사용자 입력을 기반으로 합니다.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

그런 다음 다음과 같은 매개 변수가 있는 JSON 쿼리로 Cosmos DB에이 요청을 보낼 수 있습니다.

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

다음 예제에서는 매개 변수가 있는 쿼리를 사용 하 여 상위 인수를 설정합니다. 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

매개 변수 값은 유효한 모든 JSON 수: 문자열, 숫자, 부울, null, 짝수 배열 또는 중첩 된 JSON입니다. Cosmos DB는 스키마 이므로 모든 형식에 대해 매개 변수의 유효성이 검사 되지 않습니다.

## <a id="JavaScriptIntegration"></a>JavaScript 통합

Azure Cosmos DB는 저장된 프로시저 및 트리거를 사용 하 여 컨테이너에 대해 직접 JavaScript 기반 응용 프로그램 논리를 실행 하는 것에 대 한 프로그래밍 모델입니다. 이 모델을 지원합니다.

* 고성능 트랜잭션 CRUD 작업 및 데이터베이스 엔진 내에서 JavaScript 런타임이 전체 통합 되므로 컨테이너의 항목에 대해 쿼리 합니다.
* 제어 흐름, 변수 범위 지정 및 할당 및 통합 데이터베이스 트랜잭션과 예외 처리 기본 형식의 자연 스러운 모델링 합니다. 

Azure Cosmos DB JavaScript 통합에 대 한 자세한 내용은 참조는 [JavaScript 서버 쪽 API](#JavaScriptServerSideApi) 섹션입니다.

### <a name="operator-evaluation"></a>연산자 평가

Cosmos DB는 JSON 데이터베이스 이므로 기능을 사용 하면 JavaScript 연산자 및 평가 의미 체계와 유사 합니다. Cosmos DB JSON 지원 측면에서 JavaScript 의미 체계를 유지 하려고 하지만 연산 평가가 다른 경우도 있습니다.

SQL API와 달리 기존 sql과 값의 형식은 종종 알 수 없는 API 데이터베이스에서 값을 검색할 때까지. 쿼리를 효율적으로 실행하기 위해 대부분의 연산자에 강력한 형식 요구 사항이 있습니다.

SQL API는 JavaScript와 달리 암시적 변환을 수행 하지 않습니다. 예를 들어, 같은 쿼리 `SELECT * FROM Person p WHERE p.Age = 21` 포함 된 항목을 일치 하는 `Age` 속성 값인 `21`합니다. 모든 일치 하지 않는 다른 항목입니다 `Age` 속성이 같은 무한 변형과 일치 `twenty-one`, `021`, 또는 `21.0`합니다. 이 예를 들어 연산자에 따라 숫자를 암시적으로 캐스팅 하는 문자열 값 여기서 JavaScript를 사용 하 여 대조 됩니다: `==`합니다. 이 SQL API 동작은 효율적인 인덱스 일치 하는 것이 중요 합니다.

## <a id="ExecutingSqlQueries"></a>SQL 쿼리 실행

HTTP/HTTPS 요청을 수행할 수 있는 임의의 언어로 Cosmos DB REST API를 호출할 수 있습니다. Cosmos DB는 또한.NET, Node.js, JavaScript 및 Python 프로그래밍 언어를 위한 프로그래밍 라이브러리를 제공합니다. REST API 및 라이브러리를 모든 SQL를 통해 쿼리를 지원 하 고.NET SDK도 지원 [LINQ 쿼리에](#Linq)합니다.

다음 예제에서는 쿼리를 만들고 Cosmos DB 데이터베이스 계정에 대해 제출하는 방법을 보여 줍니다.

### <a id="RestAPI"></a>REST API

Cosmos DB는 HTTP를 통해 개방형 RESTful 프로그래밍 모델을 제공합니다. 리소스 모델의 단일 데이터베이스 계정 아래에 있는 리소스 집합으로 구성 하는 Azure 구독 프로 비전 합니다. 데이터베이스 계정은 구성 집합이 *데이터베이스*, 여러 개 포함할 수 있으며 각 *컨테이너*에 *항목*, Udf 및 기타 리소스 유형이 합니다. 각 Cosmos DB 리소스는 논리적이 고 안정적인 URI를 사용 하 여 주소를 지정할 수 있습니다. 리소스 집합을 호출 되는 *피드*합니다. 

이러한 리소스를 사용한 기본 조작 모델은 HTTP 동사 `GET`, `PUT`를 `POST`, 및 `DELETE`, 해당 표준 해석을 사용 하 여 합니다. 사용 하 여 `POST` 새 리소스를 만들려면 저장된 프로시저를 실행 하거나 Cosmos DB 쿼리를 실행 합니다. 쿼리는 항상 파생 작업이 없는 읽기 전용 작업입니다.

다음 예제에 나온은 `POST` 샘플 항목에 대 한 SQL API 쿼리에 대 한 합니다. 쿼리는 JSON에 대 한 간단한 필터 `name` 속성입니다. 합니다 `x-ms-documentdb-isquery` 및 Content-type: `application/query+json` 헤더 작업이 쿼리 임을 나타냅니다. 대체 `mysqlapicosmosdb.documents.azure.com:443` Cosmos DB 계정에 대 한 URI를 사용 합니다.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

결과는 다음과 같습니다.

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

다음으로 더 복잡 한 쿼리는 조인에서 여러 결과 반환합니다.

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": [] 
    }
```

결과는 다음과 같습니다. 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

REST API를 통해 연속 토큰도 반환 쿼리 결과 단일 페이지에 맞출 수 없습니다, 하는 경우는 `x-ms-continuation-token` 응답 헤더입니다. 클라이언트는 후속 결과에 헤더를 포함 하 여 결과 페이지를 매길 수 있습니다. 통해 페이지당 결과 수를 제어할 수도 있습니다는 `x-ms-max-item-count` 번호 머리글입니다. 

쿼리 수와 같은 집계 함수에 있는 경우 쿼리 페이지 결과 하나의 페이지에 대해 부분적으로 집계 된 값을 반환할 수 있습니다. 클라이언트는 최종 결과 생성 하기 위해 이러한 결과 대해 두 번째 수준 집계를 수행 해야 합니다. 예를 들어, 총 개수를 반환 하는 개별 페이지에서 반환 된 개수 합계입니다.

쿼리에 대 한 데이터 일관성 정책을 관리 하려면 사용 된 `x-ms-consistency-level` 모든 REST API 요청 처럼 헤더입니다. 세션 일관성 해야 최신 에코 `x-ms-session-token` 쿼리 요청의 쿠키 헤더입니다. 쿼리된 컨테이너의 인덱싱 정책이 쿼리 결과의 일관성에 영향을 줄 수도 있습니다. 기본 컨테이너에 대 한 정책 인덱싱, 인덱스는 항목의 내용을 사용 하 여 현재 항상와 쿼리 결과 데이터에 대해 선택한 일관성과 일치 합니다. 자세한 내용은 [Azure Cosmos DB 일관성 수준][consistency-levels]합니다.

컨테이너에 구성 된 인덱싱 정책이 지정된 된 쿼리를 지원할 수 없습니다, 하는 경우 Azure Cosmos DB 서버 400 "잘못 된 요청"을 반환 합니다. 쿼리에 대 한 인덱싱에서 명시적으로 제외 된 경로 사용 하 여이 오류 메시지를 반환 합니다. 지정할 수는 `x-ms-documentdb-query-enable-scan` 인덱스를 사용할 수 없는 경우 검사를 수행 하려면 쿼리를 허용 하는 헤더입니다.

설정 하 여 쿼리 실행에 대 한 자세한 메트릭을 가져올 수 있습니다 합니다 `x-ms-documentdb-populatequerymetrics` 헤더를 `true`입니다. 자세한 내용은 [SQL query metrics for Azure Cosmos DB](sql-api-query-metrics.md)(Azure Cosmos DB에 대한 SQL 쿼리 메트릭)를 참조하세요.

### <a id="DotNetSdk"></a>C#(.NET SDK)

.NET SDK는 LINQ 및 SQL 쿼리를 둘 다 지원합니다. 다음 예제에서는.NET을 사용 하 여 이전 필터 쿼리를 수행 하는 방법을 보여 줍니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 각 항목 내의 같음에 대 한 두 속성을 비교 하 고 익명 프로젝션을 사용 합니다.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

다음 예제에서는 LINQ를 통해 표현 된 조인을 `SelectMany`합니다.

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

.NET 클라이언트에서 쿼리 결과의 모든 페이지를 자동으로 반복 된 `foreach` 앞의 예제와 같이 차단 합니다. 에 도입 된 쿼리 옵션을 [REST API](#RestAPI) 섹션도.NET SDK에서 사용할 수 있습니다 사용 하 여는 `FeedOptions` 및 `FeedResponse` 의 클래스는 `CreateDocumentQuery` 메서드. 사용 하 여 페이지의 수를 제어할 수는 `MaxItemCount` 설정 합니다.

만들어 페이징을 명시적으로 제어할 수 있습니다 `IDocumentQueryable` 를 사용 하 여는 `IQueryable` 읽으면 다음 개체를 `ResponseContinuationToken` 값 전달 하는 것으로 다시 `RequestContinuationToken` 에서 `FeedOptions`합니다. 설정할 수 있습니다 `EnableScanInQuery` 쿼리가 구성 된 인덱싱 정책이에서 지원 되지 않는 경우 검색을 사용 하도록 설정 합니다. 분할 된 컨테이너에 대해 사용할 수 있습니다 `PartitionKey` 를 Azure Cosmos DB 자동으로 추출할 수이 쿼리 텍스트에서 있지만 단일 파티션에 대해 쿼리를 실행 합니다. 사용할 수 있습니다 `EnableCrossPartitionQuery` 여러 파티션에 대해 쿼리를 실행할 수 있습니다.

쿼리를 사용 하 여 자세한.NET 샘플에 대 한 참조를 [Azure Cosmos DB.NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet) GitHub에서.

### <a id="JavaScriptServerSideApi"></a>JavaScript 서버 쪽 API

Cosmos DB는 저장된 프로시저 및 트리거를 사용 하 여 컨테이너에 대해 직접 JavaScript 기반 응용 프로그램 논리를 실행 하는 것에 대 한 프로그래밍 모델을 제공 합니다. 그런 다음 컨테이너 수준에서 등록 된 JavaScript 논리가 앰비언트 ACID 트랜잭션에 래핑됩니다 지정된 된 컨테이너의 항목에 대 한 데이터베이스 작업을 발행할 수 있습니다.

다음 예제에서는 사용 하는 방법을 보여 줍니다 `queryDocuments` JavaScript 서버에서 쿼리를 수행 하는 API에서 저장 프로시저와 트리거 내부:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a id="Linq"></a>SQL API에서 LINQ

LINQ는 개체 스트림에 대 한 쿼리로 계산을 표현 하는.NET 프로그래밍 모델입니다. Cosmos DB는 JSON 및 .NET 개체 간의 변환과 LINQ 쿼리 하위 집합에서 Cosmos DB 쿼리로의 매핑을 용이하게 하여 LINQ와 인터페이스할 클라이언트 쪽 라이브러리를 제공합니다.

다음 다이어그램은 Cosmos DB를 사용 하 여 LINQ 쿼리를 지 원하는 아키텍처를 보여 줍니다. Cosmos DB 클라이언트를 사용 하면 만들 수는 `IQueryable` 직접 Cosmos DB 쿼리 공급자를 쿼리 및 LINQ 쿼리를 Cosmos DB 쿼리로 변환 하는 개체입니다. 다음 JSON 형식으로 결과 집합을 검색 하는 Cosmos DB 서버로 쿼리를 전달 합니다. JSON 역직렬 변환기가 클라이언트 쪽.NET 개체 스트림으로 결과 변환합니다.

![SQL API를 사용한 LINQ 쿼리를 지원하는 아키텍처 - SQL 구문, JSON 쿼리 언어, 데이터베이스 개념 및 SQL 쿼리][1]

### <a name="net-and-json-mapping"></a>.NET 및 JSON 매핑

.NET 개체와 JSON 항목 간의 매핑은 자연 스러운입니다. 각 데이터 멤버 필드가 필드 이름이 매핑되는 JSON 개체에 매핑됩니다를 *키* 개체 및 값 재귀적으로 매핑되는 *값* 개체의 일부입니다. 다음 코드 맵 합니다 `Family` 클래스를 JSON 항목을 만들고 다음을 `Family` 개체:

```csharp
    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };
```

앞의 예제는 다음 JSON 항목을 만듭니다.

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
              "grade": 8
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };
```

### <a name="linq-to-sql-translation"></a>LINQ to SQL 변환

Cosmos DB 쿼리 공급자는 LINQ 쿼리에서 Cosmos DB SQL 쿼리로 매핑하기 위해 최대한 노력합니다. 다음 설명에 LINQ 사용 하 여에 대 한 기본 지식이 있다고 가정합니다.

쿼리 공급자 형식 시스템에서는 JSON 기본 형식만 지원 합니다: 숫자, 부울, 문자열 및 null입니다. 

쿼리 공급자는 다음 스칼라 식이 지원합니다.

- 쿼리 평가 시 기본 데이터 형식의 상수 값을 포함 하 여 상수 값입니다.
  
- 개체 또는 배열 요소 속성을 참조 하는 속성/배열 인덱스 식입니다. 예를 들면 다음과 같습니다.
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 산술 식-숫자 및 부울 값에 대 한 일반 산술 식을 포함 합니다. 전체 목록은 참조를 [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)합니다.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 상수 문자열 값은 문자열 값 비교를 포함 하는 문자열 비교 식입니다.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 복합 값 형식 또는 무명 형식의 개체나 이러한 개체의 배열을 반환 하는 개체/배열 만들기 식입니다. 이러한 값을 중첩할 수 있습니다.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

### <a id="SupportedLinqOperators"></a>지원 되는 LINQ 연산자

SQL.NET SDK에 포함 된 LINQ 공급자는 다음 연산자를 지원 합니다.

- **Select**: 프로젝션 개체 생성을 포함 하 여, SQL SELECT로 변환 합니다.
- **Where**: 필터는 SQL WHERE로 변환 지원과 간의 변환을 `&&`, `||`, 및 `!` SQL 연산자
- **SelectMany**: SQL JOIN 절에 대한 배열 해제를 허용합니다. 연결 또는 중첩 배열 요소를 필터링 하는 식을 사용 합니다.
- **OrderBy** 하 고 **OrderByDescending**: ASC 또는 DESC를 사용 하 여 ORDER BY로 변환 합니다.
- 집계를 위한 **Count**, **Sum**, **Min**, **Max** 및 **Average** 연산자와 해당 비동기 동급 연산자 **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** 및 **AverageAsync**
- **CompareTo**: 범위 비교로 변환합니다. 일반적으로.NET에서 비교 불가능 하므로 문자열에 대해 사용 합니다.
- **Take**: 쿼리 결과 제한 하기 위해 SQL TOP으로 변환 됩니다.
- **수치 연산 함수**: .NET의 변환을 지원 `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`를 `Cos`, `Exp`를 `Floor`, `Log`를 `Log10`, `Pow`, `Round`, `Sign`, `Sin`를 `Sqrt`를 `Tan`, 및 `Truncate` 동등한 SQL 기본 제공 함수입니다.
- **문자열 함수**: .NET의 변환을 지원 `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`를 `Replace`, `Reverse`를 `StartsWith`, `SubString`를 `ToLower`, `ToUpper`, `TrimEnd`, 및 `TrimStart` 동등한 SQL 기본 제공 함수입니다.
- **배열 함수**: .NET의 변환을 지원 `Concat`, `Contains`, 및 `Count` 동등한 SQL 기본 제공 함수입니다.
- **지리 공간 확장 함수**: 스텁 메서드의 변환을 지원 `Distance`, `IsValid`를 `IsValidDetailed`, 및 `Within` 동등한 SQL 기본 제공 함수입니다.
- **사용자 정의 함수 확장 함수**: 스텁 메서드의의 변환을 지원 `UserDefinedFunctionProvider.Invoke` 해당 사용자 정의 함수입니다.
- **기타**: 변환을 지원 `Coalesce` 및 조건부 연산자입니다. 변환할 수 있습니다 `Contains` 문자열 CONTAINS, ARRAY_CONTAINS 또는 SQL IN, 컨텍스트에 따라를 합니다.

### <a name="sql-query-operators"></a>SQL 쿼리 연산자

다음 예제에서는 표준 LINQ 쿼리 연산자 중 일부가 Cosmos DB 쿼리로 변환 하는 방법을 보여 줍니다.

#### <a name="select-operator"></a>Select 연산자

구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다.

**예제 1 연산자를 선택 합니다.**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**예제 2 연산자를 선택 합니다.** 

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**예제 3 연산자를 선택 합니다.**

- **LINQ 람다 식**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

#### <a name="selectmany-operator"></a>SelectMany 연산자

구문은 `input.SelectMany(x => f(x))`입니다. 여기서 `f`는 컨테이너 형식을 반환하는 스칼라 식입니다.

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

#### <a name="where-operator"></a>Where 연산자

구문은 `input.Where(x => f(x))`입니다. 여기서 `f`는 부울 값을 반환하는 스칼라 식입니다.

**여기서 연산자, 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**여기서 연산자, 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

### <a name="composite-sql-queries"></a>복합 SQL 쿼리

위의 연산자는 보다 강력한 쿼리를 작성할 수 있습니다. Cosmos DB는 중첩 된 컨테이너를 지원 하므로 연결 하거나 컴퍼지션 중첩할 수 있습니다.

#### <a name="concatenation"></a>연결

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결 된 쿼리를 선택적으로 시작할 수 있습니다 `SelectMany` 뒤에 여러 쿼리에서 `Select` 또는 `Where` 연산자입니다.

**연결 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**연결 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**연결 예 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**연결 예 4:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

#### <a name="nesting"></a>중첩

구문은 `input.SelectMany(x=>x.Q())` 여기서 `Q` 되는 `Select`, `SelectMany`, 또는 `Where` 연산자.

중첩된 쿼리는 외부 컨테이너의 각 요소에 내부 쿼리에 적용 됩니다. 하나의 중요 한 기능은 자체 조인 처럼 외부 컨테이너의 요소 필드 내부 쿼리에서 참조할 수는 것입니다.

**중첩 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**중첩 예제 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**중첩 예제 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a id="References"></a>참조

- [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)
- [ANSI SQL 2011](https://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
- [JSON](https://json.org/)
- [Javascript 사양](https://www.ecma-international.org/publications/standards/Ecma-262.htm) 
- [LINQ](/previous-versions/dotnet/articles/bb308959(v=msdn.10)) 
- Graefe, Goetz 합니다. [쿼리 평가 기법 큰 데이터베이스에 대 한](https://dl.acm.org/citation.cfm?id=152611)합니다. *설문 조사 컴퓨팅 ACM* 25 없습니다. 2 (1993).
- Graefe, 7. "단계적 프레임 워크 쿼리 최적화에 대 한 합니다." *IEEE 데이터 eng. 강세 합니다.* 18, 없습니다. 3 (1995).
- Lu Ooi, Tan 합니다. "쿼리를 병렬 관계형 데이터베이스 시스템에서 처리 합니다." *IEEE Computer Society 키를 눌러* (1994).
- Olston, Christopher, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar 및 Andrew Tomkins 합니다. "Pig Latin: Not 하므로 외래 언어 데이터 처리용입니다. " *SIGMOD* (2008).

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개][introduction]
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Azure Cosmos DB 일관성 수준][consistency-levels]

[1]: ./media/how-to-sql-query/sql-query1.png
[introduction]: introduction.md
[consistency-levels]: consistency-levels.md
