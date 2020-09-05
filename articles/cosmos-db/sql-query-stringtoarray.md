---
title: Azure Cosmos DB 쿼리 언어의 StringToArray
description: Azure Cosmos DB의 SQL 시스템 함수 StringToArray에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b00fe7a515d1d27ce9be2ab62a96c719d5e045a5
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798587"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 배열로 변환 된 식을 반환 합니다. 식을 변환할 수 없는 경우는 undefined를 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 배열 식으로 구문 분석할 문자열 식입니다. 
  
## <a name="return-types"></a>반환 형식
  
  배열 식을 반환 하거나 정의 되지 않은를 반환 합니다. 
  
## <a name="remarks"></a>설명
  중첩 된 문자열 값은 큰따옴표를 사용 하 여 유효한 JSON으로 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 [json.org](https://json.org/)를 참조 하세요. 이 시스템 함수는 인덱스를 활용 하지 않습니다.
  
## <a name="examples"></a>예제
  
  다음 예제에서는가 `StringToArray` 서로 다른 형식에서 동작 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

결과 집합은 다음과 같습니다.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

다음은 잘못 된 입력의 예입니다. 
   
 배열 내의 작은따옴표는 유효한 JSON이 아닙니다.
쿼리 내에서 유효 하더라도 올바른 배열로 구문 분석 되지 않습니다. 배열 문자열 내의 문자열은 "[" "]"로 이스케이프 되어야 합니다 \\ \\ . 그렇지 않으면 주변 따옴표가 단일 ' [""] ' 여야 합니다.

```sql
SELECT
    StringToArray("['5','6','7']")
```

결과 집합은 다음과 같습니다.

```json
[{}]
```

다음은 잘못 된 입력의 예입니다.
   
 전달 된 식은 JSON 배열로 구문 분석 됩니다. 다음은 형식 배열로 계산 되지 않으므로 undefined로 반환 됩니다.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

결과 집합은 다음과 같습니다.

```json
[{}]
```

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
