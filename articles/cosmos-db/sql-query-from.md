---
title: Azure Cosmos DB의 FROM 절
description: SQL 구문 및 Azure Cosmos DB의 FROM 절에 대한 예제를 알아봅니다. 또한이 문서에서는 결과 범위를 표시 하는 예제를 보여 주고 FROM 절을 사용 하 여 하위 항목을 가져옵니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 4f6d7580ea7ff0e8968c0c3ce4b3ca6111c86ac8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873372"
---
# <a name="from-clause-in-azure-cosmos-db"></a>Azure Cosmos DB의 FROM 절

FROM (`FROM <from_specification>`) 절은 쿼리에서 해당 소스가 필터링 되거나 나중에 투영 되지 않는 한 선택 사항입니다. `SELECT * FROM Families`와 같은 쿼리는 전체 `Families` 컨테이너를 열거 합니다. 컨테이너 이름을 사용 하는 대신 컨테이너에 대해 특수 식별자 루트를 사용할 수도 있습니다.

FROM 절은 쿼리당 다음 규칙을 적용 합니다.

* 컨테이너를 별칭으로 `SELECT f.id FROM Families AS f` 또는 간단히 `SELECT f.id FROM Families f`로 지정할 수 있습니다. 다음 `f` `Families`의 별칭입니다. AS는 식별자의 [별칭](sql-query-aliasing.md) 을 지정 하는 선택적 키워드입니다.  

* 별칭을 지정 하면 원래 원본 이름을 바인딩할 수 없습니다. 예를 들어, 식별자 `Families` 별칭이 지정 되었으므로 더 이상 확인할 수 없기 때문에 `SELECT Families.id FROM Families f` 구문상 잘못 되었습니다.  

* 엄격한 스키마 준수가 없는 경우 모호한 바인딩을 방지 하려면 모든 참조 된 속성을 정규화 해야 합니다. 예를 들어 `SELECT id FROM Families f`는 속성이 `id` 바인딩되지 않으므로 구문이 잘못 되었습니다.

## <a name="syntax"></a>구문
  
```sql  
FROM <from_specification>  
  
<from_specification> ::=   
        <from_source> {[ JOIN <from_source>][,...n]}  
  
<from_source> ::=   
          <container_expression> [[AS] input_alias]  
        | input_alias IN <container_expression>  
  
<container_expression> ::=   
        ROOT   
     | container_name  
     | input_alias  
     | <container_expression> '.' property_name  
     | <container_expression> '[' "property_name" | array_index ']'  
```  
  
## <a name="arguments"></a>인수
  
- `<from_source>`  
  
  별칭이 있거나 없는 데이터 원본을 지정합니다. 별칭을 지정하지 않으면 다음 규칙을 사용하여 `<container_expression>`에서 유추됩니다.  
  
  -  식이 container_name이면 container_name이 별칭으로 사용됩니다.  
  
  -  식이 `<container_expression>`이면 property_name이 별칭으로 사용됩니다. 식이 container_name이면 container_name이 별칭으로 사용됩니다.  
  
- AS `input_alias`  
  
  `input_alias`가 기본 컨테이너 식에서 반환되는 값 집합임을 지정합니다.  
 
- `input_alias` IN  
  
  `input_alias`가 기본 컨테이너 식에서 반환되는 각 배열의 모든 배열 요소를 반복하여 얻는 값 집합을 나타내도록 지정합니다. 배열이 아닌 기본 컨테이너 식에서 반환되는 값은 무시됩니다.  
  
- `<container_expression>`  
  
  문서를 검색하는 데 사용할 컨테이너 식을 지정합니다.  
  
- `ROOT`  
  
  현재 연결된 기본 컨테이너에서 문서를 검색하도록 지정합니다.  
  
- `container_name`  
  
  제공된 컨테이너에서 문서를 검색하도록 지정합니다. 컨테이너의 이름은 현재 연결된 컨테이너의 이름과 일치해야 합니다.  
  
- `input_alias`  
  
  제공된 별칭으로 정의된 다른 원본에서 문서를 검색하도록 지정합니다.  
  
- `<container_expression> '.' property_`  
  
  지정된 컨테이너 식으로 검색된 모든 문서에 대해 `property_name` 속성 또는 array_index 배열 요소에 액세스하여 문서를 검색하도록 지정합니다.  
  
- `<container_expression> '[' "property_name" | array_index ']'`  
  
  지정된 컨테이너 식으로 검색된 모든 문서에 대해 `property_name` 속성 또는 array_index 배열 요소에 액세스하여 문서를 검색하도록 지정합니다.  
  
## <a name="remarks"></a>설명
  
`<from_source>(`에서 제공되거나 유추되는 모든 별칭은 고유해야 합니다. `<container_expression>.`property_name 구문은 `<container_expression>' ['"property_name"']'`과 같습니다. 그러나 속성 이름에 비식별자 문자가 포함되어 있으면 후자의 구문을 사용할 수 있습니다.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>누락된 속성, 누락된 배열 요소, 정의되지 않은 값 처리
  
컨테이너 식에서 속성이나 배열 요소에 액세스하고 해당 값이 존재하지 않으면 이 값은 무시되고 더 이상 처리되지 않습니다.  
  
### <a name="container-expression-context-scoping"></a>컨테이너 식 컨텍스트 범위 지정  
  
컨테이너 식은 컨테이너 범위 또는 문서 범위일 수 있습니다.  
  
-   컨테이너 식의 기본 원본이 ROOT 또는 `container_name`이면 식은 컨테이너 범위입니다. 이러한 식은 컨테이너에서 직접 검색되는 문서 집합을 나타내며 다른 컨테이너 식의 처리에 종속되지 않습니다.  
  
-   컨테이너 식의 기본 원본이 쿼리의 앞 부분에 도입된 `input_alias`이면 식은 문서 범위입니다. 이러한 식은 별칭 지정된 컨테이너와 연결된 집합에 속한 각 문서의 범위에서 컨테이너 식을 평가하여 얻는 문서 집합을 나타냅니다.  결과 집합은 기본 집합에 있는 각 문서에 대해 컨테이너 식을 평가하여 얻는 집합의 합집합입니다. 

## <a name="examples"></a>예시

### <a name="get-subitems-by-using-the-from-clause"></a>FROM 절을 사용 하 여 하위 항목 가져오기

FROM 절을 통해 소스를 더 작은 하위 집합으로 줄일 수 있습니다. 각 항목의 하위 트리를 열거 하는 경우 다음 예제와 같이 subroot는 원본이 될 수 있습니다.

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

앞의 쿼리에서는 배열을 소스로 사용 했지만 개체를 원본으로 사용할 수도 있습니다. 이 쿼리는 결과에 포함 될 원본에서 정의 된 유효한 모든 JSON 값을 고려 합니다. 다음 예에서는 `address.state` 값이 없는 `Families`를 제외 합니다.

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

## <a name="next-steps"></a>다음 단계

- [시작](sql-query-getting-started.md)
- [SELECT 절](sql-query-select.md)
- [WHERE 절](sql-query-where.md)