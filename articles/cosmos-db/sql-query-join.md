---
title: Azure Cosmos DB에 대한 SQL 조인 쿼리
description: Azure Cosmos DB에서 여러 테이블을 조인 하 여 데이터를 쿼리 하는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 38e80f1597a08b8db7cbfa852d1bcf38ac768b1f
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74871145"
---
# <a name="joins-in-azure-cosmos-db"></a>Azure Cosmos DB의 조인

관계형 데이터베이스에서 테이블 간의 조인은 정규화 된 스키마를 디자인 하는 논리적 필연적인 결과로입니다. 반면에 SQL API는 *자체 조인과*논리적으로 동일한 스키마 없는 항목의 비 정규화 된 데이터 모델을 사용 합니다.

내부 조인은 조인에 참여하는 집합의 완전한 교차곱을 만듭니다. N 방향 조인의 결과는 N 요소 튜플의 집합이며, 여기서 튜플의 각 값은 조인에 참여하는 별칭 지정된 집합과 연결되며 다른 절에서 해당 별칭을 참조하여 액세스할 수 있습니다.

## <a name="syntax"></a>구문

이 언어는 `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`구문을 지원 합니다. 이 쿼리는 `N` 값이 포함 된 튜플 집합을 반환 합니다. 각 튜플은 해당 집합에 모든 컨테이너 별칭을 반복하여 생성된 값을 포함합니다. 

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
  
- `input_alias1`을 참조하는 문서 범위로 `<from_source2>`를 지정하고 다음 집합을 나타냅니다.  
  
    `input_alias1 = A,`의 경우 {1, 2}  
  
    `input_alias1 = B,`의 경우 {3}  
  
    `input_alias1 = C,`의 경우 {4, 5}  
  
- `input_alias2`을 참조하는 문서 범위로 `<from_source3>`를 지정하고 다음 집합을 나타냅니다.  
  
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
  
## <a name="examples"></a>예시

다음 예제는 JOIN 절의 작동 방식을 보여 줍니다. 이러한 예제를 실행 하기 전에 샘플 [제품군 데이터](sql-query-getting-started.md#upload-sample-data)를 업로드 합니다. 다음 예제에서는 소스와 빈 집합의 각 항목에 대한 교차곱이 비어 있기 때문에 결과가 비어 있습니다.

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

다음 예제에서 조인은 두 JSON 개체 (항목 루트 `id`와 `children` subroot 간의 교차곱입니다. `children`는 배열이 `children` 배열인 단일 루트를 처리 하기 때문에 조인에는 적용 되지 않습니다. 배열에 있는 각 항목의 교차곱이 정확히 하나의 항목만 생성 하기 때문에 결과에는 두 개의 결과만 포함 됩니다.

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

JOIN 절의 FROM 소스가 반복기입니다. 따라서 앞의 예제에서 흐름은 다음과 같습니다.  

1. 배열에서 각 자식 요소 `c`를 확장 합니다.
2. 첫 번째 단계가 결합 된 `c` 각 자식 요소와 함께 `f` 항목의 루트와 교차곱을 적용 합니다.
3. 마지막으로, 루트 개체 `f` `id` 속성만을 프로젝션 합니다.

첫 번째 항목인 `AndersenFamily`는 하나의 `children` 요소만 포함 하므로 결과 집합에는 단일 개체만 포함 됩니다. 두 번째 항목인 `WakefieldFamily`는 두 개의 `children`를 포함 하므로 교차곱은 각 `children` 요소에 대해 하나씩 두 개의 개체를 생성 합니다. 교차곱에서 예상한 대로 두 항목의 루트 필드는 동일합니다.

JOIN 절의 실제 유틸리티는 프로젝트에 어려움이 있는 셰이프를 통해 외적에서 튜플을 형성 하는 것입니다. 아래 예제에서는 사용자가 튜플이 전체적으로 충족 하는 조건을 선택할 수 있도록 하는 튜플의 조합을 필터링 합니다.

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

앞의 예제에서 다음과 같은 확장은 이중 조인을 수행 합니다. 다음 의사 코드로 교차곱을 볼 수 있습니다.

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

`AndersenFamily`에는 애완 동물을 가진 자식이 하나 있으므로 교차곱은이 제품군에서 한 행 (1\*1\*1)을 생성 합니다. `WakefieldFamily`에는 두 개의 자식이 있는데, 그 중 하나에는 애완 동물이 있지만 해당 자식에는 애완 동물 2 개가 있습니다. 이 패밀리의 교차곱은 1\*1\*2 = 2 행을 생성 합니다.

다음 예제에는 `pet`에 대한 추가 필터가 있습니다 .이 필터는 애완 동물 이름이 `Shadow`되지 않은 모든 튜플을 제외 합니다. 배열에서 튜플을 빌드하고, 튜플의 요소를 필터링 하 고, 요소 조합을 프로젝션 할 수 있습니다.

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