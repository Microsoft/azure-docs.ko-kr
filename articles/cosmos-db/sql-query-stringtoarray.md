---
title: Azure Cosmos DB 쿼리 언어의 StringToArray
description: Azure Cosmos DB의 SQL 시스템 함수 StringToArray에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349286"
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
  
## <a name="return-types"></a>반환 유형
  
  배열 식을 반환 하거나 정의 되지 않은를 반환 합니다. 
  
## <a name="remarks"></a>설명
  중첩 된 문자열 값은 큰따옴표를 사용 하 여 유효한 JSON으로 작성 해야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/) 를 참조 하세요.
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToArray`이 서로 다른 형식에서 동작 하는 방식을 보여 줍니다. 
  
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
쿼리 내에서 유효 하더라도 올바른 배열로 구문 분석 되지 않습니다. 배열 문자열 내의 문자열은 "[\\" \\ "]"로 이스케이프 해야 합니다. 그렇지 않으면 주변 따옴표는 단일 ' [""] ' 여야 합니다.

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
