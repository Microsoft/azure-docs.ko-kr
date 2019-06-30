---
title: Azure Cosmos DB에 대 한 SQL 쿼리 연산자
description: Azure Cosmos DB에 대 한 SQL 연산자에 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: eecc1522f8c260286c7dd7fc4c2e58d5d8caa8fb
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342614"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB의 연산자

이 문서에서는 Azure Cosmos DB에서 지 원하는 다양 한 연산자를 설명 합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **개체** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Boolean** | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined | Undefined |
| **Number** | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined | Undefined |
| **String** | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined | Undefined |
| **개체** | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **Ok** |

와 같은 비교 연산자에 대 한 `>`, `>=`를 `!=`를 `<`, 및 `<=`, 비교 형식 간에 또는 두 개체 또는 배열을 생성 `Undefined`합니다.  

스칼라 식의 결과 이면 `Undefined`, 때문에 항목이 결과에 포함 되어 있지 `Undefined` 와 같지 않은 `true`합니다.

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표에서 이러한 연산자의 논리적 진위 표가 보여 줍니다.

**OR 연산자**

| 또는 | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

**AND 연산자**

| AND | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

**NOT 연산자**

| NOT |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |


## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목 프로젝트입니다. 사용할 경우 프로젝션되는 유일한 필드여야 같은 쿼리 `SELECT * FROM Families f` 유효 하지만 `SELECT VALUE * FROM Families f` 고 `SELECT *, f.id FROM Families f` 올바르지 않습니다.

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

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [키워드](sql-query-keywords.md)
- [SELECT 절](sql-query-select.md)
