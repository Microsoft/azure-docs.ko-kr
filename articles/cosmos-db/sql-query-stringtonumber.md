---
title: Azure Cosmos DB 쿼리 언어의 StringToNumber
description: Azure Cosmos DB의 SQL 시스템 함수 StringToNumber에 대해 알아봅니다.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b9596738d9b02fa26f9c363287323b905654a1f
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349233"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 숫자로 변환 된 식을 반환 합니다. 식을 변환할 수 없는 경우는 undefined를 반환 합니다.  
  
## <a name="syntax"></a>구문
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>인수
  
*str_expr*  
   JSON 숫자 식으로 구문 분석할 문자열 식입니다. JSON의 숫자는 정수 이거나 부동 소수점 이어야 합니다. JSON 형식에 대한 자세한 내용은 [json.org](https://json.org/) 를 참조 하세요.  
  
## <a name="return-types"></a>반환 유형
  
  숫자 식을 반환 하거나 정의 되지 않은를 반환 합니다.  
  
## <a name="examples"></a>예
  
  다음 예제에서는 `StringToNumber`이 서로 다른 형식에서 동작 하는 방식을 보여 줍니다. 

공백은 숫자 앞 이나 뒤에만 사용할 수 있습니다.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

JSON에서 유효한 숫자는 정수 이거나 부동 소수점 숫자 여야 합니다.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{}}
```  

전달 된 식은 숫자 식으로 구문 분석 됩니다. 이러한 입력은 Number 형식으로 계산 되지 않으므로 undefined로 반환 됩니다. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 결과 집합은 다음과 같습니다.  
  
```json
{{}}
```  

## <a name="next-steps"></a>다음 단계

- [문자열 함수 Azure Cosmos DB](sql-query-string-functions.md)
- [시스템 함수 Azure Cosmos DB](sql-query-system-functions.md)
- [Azure Cosmos DB 소개](introduction.md)
