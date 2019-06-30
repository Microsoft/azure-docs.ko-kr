---
title: Azure Cosmos DB에 대 한 SQL JOIN 쿼리
description: Azure Cosmos DB에 대 한 SQL 조인 구문에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 408ee11b318143b3128833a741e04dd68f3816ed
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342737"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB의 조인

관계형 데이터베이스 테이블 간 조인에는 정규화 된 스키마 설계의 필연적인 논리적 결과입니다. SQL API는 논리는 스키마 없는 항목의 정규화 되지 않은 데이터 모델을 사용 하는 반면에 해당 하는 *자체 조인을*합니다.

내부 조인은 조인에 참여하는 집합의 완전한 교차곱을 만듭니다. N 방향 조인의 결과는 N 요소 튜플의 집합이며, 여기서 튜플의 각 값은 조인에 참여하는 별칭 지정된 집합과 연결되며 다른 절에서 해당 별칭을 참조하여 액세스할 수 있습니다.

## <a name="syntax"></a>구문

언어 구문을 지 원하는 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`합니다. 이 쿼리를 사용 하 여 튜플 집합을 반환 합니다 `N` 값입니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다. 

`<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` FROM 절을 살펴보겠습니다.  
  
 각 원본에서 `input_alias1, input_alias2, …, input_aliasN`을 정의하도록 합니다. 이 FROM 절은 N 튜플(N개 값이 포함된 튜플)의 집합을 반환합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다.  
  
**예제 1** - 2개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- input_alias1을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source1> JOIN <from_source2>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**예제 2** - 3개 원본  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- `input_alias1`를 참조하는 문서 범위로 `<from_source2>`을 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `input_alias2`를 참조하는 문서 범위로 `<from_source3>`을 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = 1,`의 경우 {100, 200}  
  
    `input_alias2 = 3,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > `input_alias1`, `input_alias2`의 다른 값에 대한 튜플이 없으므로 `<from_source3>`에서 값을 반환하지 않았습니다.  
  
**예제 3** - 3개 원본  
  
- 컨테이너 범위로 <from_source1>을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- 컨테이너 범위로 `<from_source1>`을 지정하고 집합 {A, B, C}를 나타냅니다.  
  
- input_alias1을 참조하는 문서 범위로 <from_source2>를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `<from_source3>`을 `input_alias1`로 범위 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias2 = A,`의 경우 {100, 200}  
  
    `input_alias2 = C,`의 경우 {300}  
  
- `<from_source1> JOIN <from_source2> JOIN <from_source3>` FROM 절은 다음과 같은 튜플을 만듭니다.  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > 이렇게 하면 `<from_source2>`와 `<from_source3>`의 범위를 모두 동일한 `<from_source1>`로 지정했기 때문에 두 원본의 교차곱을 만들고,  이로 인해 A 값이 있는 4개(2x2) 튜플, B 값이 있는 0개(1x0) 튜플 및 C 값이 있는 2개(2x1) 튜플을 만들었습니다.  
  
## <a name="examples"></a>예

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

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [하위 쿼리](sql-query-subquery.md)