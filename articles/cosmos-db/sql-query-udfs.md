---
title: Azure Cosmos DB에서 Udf (사용자 정의 함수)
description: Azure Cosmos DB의 사용자 정의 함수에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: b67202da7293ef55cfe3390ca676f7944da80fba
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614322"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB에서 Udf (사용자 정의 함수)

SQL API는 Udf (사용자 정의 함수)를 지원 합니다. 스칼라 Udf를 사용 하면 0 개 이상의 인수를 전달 하 고 단일 인수 결과를 반환할 수 있습니다. API는 각 인수를 올바른 JSON 값으로 확인 합니다.  

이 API는 Udf를 사용 하 여 사용자 지정 응용 프로그램 논리를 지원 하도록 SQL 구문을 확장 합니다. SQL API에 Udf를 등록 하 고 SQL 쿼리에서 참조할 수 있습니다. 실제로 UDF는 쿼리에서 호출되도록 설계되었습니다. 필연적인 결과로 저장 프로시저 및 트리거와 같은 다른 JavaScript 형식과 마찬가지로 Udf는 컨텍스트 개체에 액세스할 수 없습니다. 쿼리는 읽기 전용 이며 주 복제본 또는 보조 복제본에서 실행할 수 있습니다. 다른 JavaScript 형식과 달리 Udf는 보조 복제본에서 실행 되도록 설계 되었습니다.

다음 예에서는 Cosmos 데이터베이스의 항목 컨테이너 아래에 UDF를 등록 합니다. 이 `REGEX_MATCH`예제에서는 이름이 인 UDF를 만듭니다. 이 클래스는 및 `input` `pattern`의 두 JSON 문자열 값을 허용 하 고, 첫 번째가 JavaScript의 `string.match()` 함수를 사용 하 여 두 번째에 지정 된 패턴과 일치 하는지 확인 합니다.

## <a name="examples"></a>예

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

이제 쿼리 프로젝션에서이 UDF를 사용 합니다. 쿼리 내에서 호출 하는 경우 대/소문자 `udf.` 를 구분 하는 접두사로 udf를 정규화 해야 합니다.

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

다음 예제와 같이 필터 내에서 `udf.` 접두사로 정규화 된 UDF를 사용할 수 있습니다.

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

기본적으로 Udf는 프로젝션이 나 필터에서 사용할 수 있는 유효한 스칼라 식입니다.

Udf의 기능을 확장 하려면 조건부 논리를 사용 하 여 다른 예를 살펴보세요.

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

다음 예제에서는 UDF를 연습 합니다.

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

UDF 매개 변수에서 참조 하는 속성을 JSON 값에서 사용할 수 없는 경우 매개 변수가 정의 되지 않은 것으로 간주 되어 UDF 호출을 건너뜁니다. 마찬가지로 UDF의 결과가 정의 되지 않은 경우에는 결과에 포함 되지 않습니다.

앞의 예제에서 볼 때 Udf는 JavaScript 언어의 기능을 SQL API와 통합 합니다. Udf는 기본 제공 JavaScript 런타임 기능을 통해 복잡 한 절차적 조건부 논리를 수행 하는 풍부한 프로그래밍 가능 인터페이스를 제공 합니다. SQL API는 처리의 현재 WHERE 또는 SELECT 절 단계에서 각 소스 항목에 대한 Udf에 인수를 제공 합니다. 결과는 전체 실행 파이프라인에서 원활 하 게 통합 됩니다. 요약 하자면, Udf는 쿼리의 일부로 복잡 한 비즈니스 논리를 수행 하는 데 유용한 도구입니다.

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [집계](sql-query-aggregates.md)