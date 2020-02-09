---
title: Azure Cosmos DB에 대한 SQL 쿼리 연산자
description: Azure Cosmos DB에서 지 원하는 같음, 비교 및 논리 연산자와 같은 SQL 연산자에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870941"
---
# <a name="operators-in-azure-cosmos-db"></a>Azure Cosmos DB 연산자

이 문서에서는 Azure Cosmos DB에서 지 원하는 다양 한 연산자에 대해 자세히 설명 합니다.

## <a name="equality-and-comparison-operators"></a>같음 및 비교 연산자

다음 표는 SQL API에서 두 JSON 형식 간의 같음 비교 결과를 보여 줍니다.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Null** | 정의되지 않음 | **Ok** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Boolean** | 정의되지 않음 | 정의되지 않음 | **Ok** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **Number** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **Ok** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 |
| **String** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **Ok** | 정의되지 않음 | 정의되지 않음 |
| **Object** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **Ok** | 정의되지 않음 |
| **Array** | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | 정의되지 않음 | **Ok** |

`>`, `>=`, `!=`, `<`, `<=`등의 비교 연산자의 경우 형식 간 비교 또는 두 개체 또는 배열 간의 비교는 `Undefined`을 생성 합니다.  

스칼라 식의 결과가 `Undefined`경우에는 `Undefined` `true`같지 않으므로 항목은 결과에 포함 되지 않습니다.

## <a name="logical-and-or-and-not-operators"></a>논리(AND, OR 및 NOT) 연산자

논리 연산자는 부울 값에 작동합니다. 다음 표에서는 이러한 연산자에 대한 논리적 진위 테이블을 보여 줍니다.

**OR 연산자**

| 또는 | 참 | 거짓 | 정의되지 않음 |
| --- | --- | --- | --- |
| 참 |참 |참 |참 |
| 거짓 |참 |거짓 |정의되지 않음 |
| 정의되지 않음 |참 |정의되지 않음 |정의되지 않음 |

**AND 연산자**

| AND | 참 | 거짓 | 정의되지 않음 |
| --- | --- | --- | --- |
| 참 |참 |거짓 |정의되지 않음 |
| 거짓 |거짓 |거짓 |거짓 |
| 정의되지 않음 |정의되지 않음 |거짓 |정의되지 않음 |

**NOT 연산자**

| NOT |  |
| --- | --- |
| 참 |거짓 |
| 거짓 |참 |
| 정의되지 않음 |정의되지 않음 |


## <a name="-operator"></a>* 연산자

특수 연산자 *는 전체 항목을 있는 그대로 프로젝션 합니다. 사용할 경우 프로젝션되는 유일한 필드여야 `SELECT * FROM Families f` 같은 쿼리는 올바르지만 `SELECT VALUE * FROM Families f` 및 `SELECT *, f.id FROM Families f` 유효 하지 않습니다.

## <a name="-and--operators"></a>? ? 연산자

C# 및 JavaScript와 같은 프로그래밍 언어에서와 같이 삼항 (?) 및 병합 (??) 연산자를 사용 하 여 조건식을 작성할 수 있습니다. 

?를 사용 하 여 새 JSON 속성을 즉시 생성 하는 연산자입니다. 예를 들어 다음 쿼리는 등급 수준을 `elementary` 또는 `other`분류 합니다.

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

에 대한 호출을 중첩 시킬 수도 있습니다. 다음 쿼리와 같이 연산자를 적용 합니다. 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

다른 쿼리 연산자와 마찬가지로? 참조 된 속성이 없거나 비교할 형식이 다른 경우 연산자는 항목을 제외 합니다.

??를 사용 하 여 반 구조화 된 데이터 또는 혼합 형식 데이터에 대해 쿼리할 때 항목에서 속성을 효율적으로 확인 하는 연산자입니다. 예를 들어 다음 쿼리는 있는 경우 `lastName`를 반환 하 고 `lastName` 없는 경우 `surname`을 반환 합니다.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [키워드](sql-query-keywords.md)
- [SELECT 절](sql-query-select.md)
