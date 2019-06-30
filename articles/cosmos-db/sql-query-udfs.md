---
title: Azure Cosmos DB에서 사용자 정의 함수 (Udf)
description: Azure Cosmos DB에서 사용자 정의 함수에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: e168e450230720f4ad78516e6edcdc3aa08ba3e1
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342554"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Azure Cosmos DB에서 사용자 정의 함수 (Udf)

SQL API는 사용자 정의 함수 (Udf)에 대 한 지원을 제공합니다. 스칼라 Udf를 사용 하 여 0 개 또는 많은 인수를 전달할 수 있으며 단일 인수 결과 반환할 수 있습니다. API에는 각 인수가 유효한 JSON 값인지 확인 합니다.  

API는 Udf를 사용 하 여 사용자 지정 응용 프로그램 논리를 지원 하기 위해 SQL 구문이 확장 됩니다. SQL API를 사용 하 여 Udf를 등록 하 고 SQL 쿼리에서 참조할 수 있습니다. 실제로 UDF는 쿼리에서 호출되도록 설계되었습니다. 그 결과, Udf 없는 저장된 프로시저 및 트리거와 같은 다른 JavaScript 형식과 마찬가지로 컨텍스트 개체에 액세스 합니다. 쿼리는 읽기 전용 및 기본 또는 보조 복제본에서 실행할 수 있습니다. Udf의 경우 다른 JavaScript 형식과 달리 보조 복제본에서 실행 하도록 설계 되었습니다.

다음 예제에서는 Cosmos DB 데이터베이스의 항목 컨테이너에서 UDF를 등록합니다. 이 예에서는 이름이 UDF 만듭니다 `REGEX_MATCH`합니다. 두 JSON 문자열 값을 받아들이는 `input` 및 `pattern`, JavaScript의를 사용 하는 경우 두 번째에서 지정 된 패턴과 일치 하는 첫 번째 항목 검사 `string.match()` 함수입니다.

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

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 소개](introduction.md)
- [시스템 함수](sql-query-system-functions.md)
- [집계](sql-query-aggregates.md)