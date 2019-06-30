---
title: Azure Cosmos DB의 SELECT 절
description: Azure Cosmos DB에 대 한 SQL SELECT 절에 알아봅니다. Azure Cosmos DB JSON 쿼리 언어로 SQL을 사용 합니다.
author: ginarobinson
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: girobins
ms.openlocfilehash: 84d0212f7f212b4554b506726e027fe51f795eea
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342593"
---
# <a name="select-clause"></a>SELECT 절

모든 쿼리는 SELECT 절 및 선택적 구성 됩니다 [FROM](sql-query-from.md) 하 고 [여기서](sql-query-where.md) ANSI SQL 표준에 따라 절. 일반적으로 FROM 절에서 원본 열거 되 고 WHERE 절은 JSON 항목의 하위 집합을 검색 하기 위해 원본에 필터를 적용 합니다. 다음 SELECT 절은 select 목록에 요청된 된 JSON 값을 프로젝션합니다.

## <a name="syntax"></a>구문

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | [DISTINCT] <object_property_list>   
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
## <a name="arguments"></a>인수
  
- `<select_specification>`  

  결과 집합에 대해 선택되는 속성 또는 값입니다.  
  
- `'*'`  

  변경하지 않고 값을 검색하도록 지정합니다. 특히 처리된 값이 개체이면 모든 속성이 검색됩니다.  
  
- `<object_property_list>`  
  
  검색할 속성의 목록을 지정합니다. 반환된 각 값은 지정된 속성이 있는 개체입니다.  
  
- `VALUE`  

  완전한 JSON 개체 대신 JSON 값을 검색하도록 지정합니다. 이것은 `<property_list>`와 달리 개체에 프로젝션된 값을 래핑하지 않습니다.  
 
- `DISTINCT`
  
  프로젝션 된 속성의 중복을 제거 해야 한다는 것을 지정 합니다.  

- `<scalar_expression>`  

  계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](sql-query-scalar-expressions.md) 섹션을 참조하세요.  

## <a name="remarks"></a>설명

`SELECT *` 구문은 FROM 절에서 정확히 하나의 별칭을 선언한 경우에만 유효합니다. `SELECT *`는 ID 프로젝션을 제공하며, 이는 프로젝션이 필요하지 않은 경우 유용할 수 있습니다. SELECT *는 FROM 절이 지정되고 단일 입력 원본만 도입된 경우에만 유효합니다.  
  
`SELECT <select_list>`와 `SELECT *`는 "syntactic sugar(구문적 설탕)"이며 다음과 같이 간단한 SELECT 문을 사용하여 표현할 수 있습니다.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   이는 다음과 동등합니다.  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   이는 다음과 동등합니다.  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>예

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

### <a name="quoted-property-accessor"></a>따옴표 붙은 속성 접근자
따옴표 붙은 속성 연산자 사용 하 여 속성에 액세스할 수 있습니다. 예를 들어 `SELECT c.grade` and `SELECT c["grade"]` 와 동일합니다. 이 구문은 공백, 특수 문자가 포함 되어 있거나 SQL 키워드 또는 예약어와 이름이 같은 속성을 이스케이프 하는 데 유용 합니다.

```sql
    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"
```

### <a name="nested-properties"></a>중첩 속성

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
### <a name="json-expressions"></a>JSON 식

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

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [WHERE 절](sql-query-where.md)