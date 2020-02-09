---
title: Azure Cosmos DB에서 LINQ to SQL 변환
description: 지원 되는 LINQ 연산자 및 LINQ 쿼리가 Azure Cosmos DB의 SQL 쿼리에 매핑되는 방법에 대해 알아봅니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: d43f95b91df7d0c9c442339de51936200f4688e2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441247"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL 변환

Azure Cosmos DB 쿼리 공급자는 LINQ 쿼리에서 Cosmos DB SQL 쿼리로 매핑을 수행 하는 데 가장 적합 한 작업을 수행 합니다. 다음 설명에서는 LINQ에 대한 기본적인 지식이 있다고 가정 합니다.

쿼리 공급자 유형 시스템은 JSON 기본 유형인 numeric, Boolean, string 및 null만 지원 합니다.

쿼리 공급자는 다음과 같은 스칼라 식을 지원 합니다.

- 쿼리 평가 시 기본 데이터 형식의 상수 값을 포함 하는 상수 값입니다.
  
- 개체 또는 배열 요소의 속성을 참조 하는 속성/배열 인덱스 식입니다. 예:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 숫자 및 부울 값에 대한 일반적인 산술 식을 포함 하는 산술 식 전체 목록은 [AZURE COSMOS DB SQL 사양을](https://go.microsoft.com/fwlink/p/?LinkID=510612)참조 하십시오.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 문자열 값을 일부 상수 문자열 값과 비교 하는 문자열 비교 식입니다.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 복합 값 형식 또는 무명 형식의 개체 또는 이러한 개체의 배열을 반환 하는 개체/배열 생성 식입니다. 이러한 값은 중첩할 수 있습니다.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>지원 되는 LINQ 연산자

SQL .NET SDK에 포함 된 LINQ 공급자는 다음과 같은 연산자를 지원 합니다.

- **Select**: 개체 생성을 포함 하 여 SQL select로 프로젝션 변환을 선택 합니다.
- **Where**: 필터는 sql로 변환 하 고 `&&`, `||`및 `!`에서 sql 연산자로의 변환을 지원 합니다.
- **SelectMany**: SQL JOIN 절에 대한 배열 해제를 허용합니다. 를 사용 하 여 배열 요소를 필터링 하는 식을 연결 하거나 중첩 합니다.
- **OrderBy** 및 **OrderByDescending**: ASC 또는 DESC를 사용 하 여 ORDER by로 변환 합니다.
- 집계를 위한 **Count**, **Sum**, **Min**, **Max** 및 **Average** 연산자와 해당 비동기 동급 연산자 **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** 및 **AverageAsync**
- **CompareTo**: 범위 비교로 변환합니다. .NET에서는 비교할 수 없기 때문에 일반적으로 문자열에 사용 됩니다.
- **Skip** 및 **TAKE**: SQL 오프셋으로 변환 하 고 쿼리 결과를 제한 하 고 페이지 매김을 수행 합니다.
- **수치 연산 함수**: .net `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`, `Cos`, `Exp`, `Floor`, `Log`, `Log10`, `Pow`, `Round`, `Sign`, `Sin`, `Sqrt`에서 해당 하는 SQL 기본 제공 함수로의 변환을 지원 합니다.
- **문자열 함수**: .net `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`, `Replace`, `Reverse`, `StartsWith`, `SubString`, `ToLower`, `ToUpper`, `TrimEnd`및 `TrimStart`에서 해당 하는 SQL 기본 제공 함수로의 변환을 지원 합니다.
- **배열 함수**: .net `Concat`, `Contains`및 `Count`에서 해당 하는 SQL 기본 제공 함수로의 변환을 지원 합니다.
- **지리 공간적 확장 함수**: 스텁 메서드 `Distance`, `IsValid`, `IsValidDetailed`및 `Within`에서 해당 하는 SQL 기본 제공 함수로의 변환을 지원 합니다.
- **사용자 정의 함수 확장 함수**: 스텁 메서드 `UserDefinedFunctionProvider.Invoke`에서 해당 하는 사용자 정의 함수로의 변환을 지원 합니다.
- **기타**: `Coalesce` 및 조건부 연산자의 변환을 지원 합니다. 는 컨텍스트에 따라 `Contains`를 String CONTAINS, ARRAY_CONTAINS 또는 SQL로 변환할 수 있습니다.

## <a name="examples"></a>예시

다음 예에서는 표준 LINQ 쿼리 연산자 중 일부를 Cosmos DB 쿼리로 변환 하는 방법을 보여 줍니다.

### <a name="select-operator"></a>Select 연산자

구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다.

**Select 연산자, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Select 연산자, 예 2:** 

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Select 연산자, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL** 
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>SelectMany 연산자

구문은 `input.SelectMany(x => f(x))`입니다. 여기서 `f`는 컨테이너 형식을 반환하는 스칼라 식입니다.

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Where 연산자

구문은 `input.Where(x => f(x))`입니다. 여기서 `f`는 부울 값을 반환하는 스칼라 식입니다.

**Where 연산자, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where 연산자, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>복합 SQL 쿼리

위의 연산자를 작성 하 여 보다 강력한 쿼리를 만들 수 있습니다. Cosmos DB에서 중첩 된 컨테이너를 지원 하기 때문에 컴퍼지션을 연결 하거나 중첩할 수 있습니다.

### <a name="concatenation"></a>연결

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결 된 쿼리는 선택적 `SelectMany` 쿼리로 시작 하 고 그 다음에 여러 `Select` 또는 `Where` 연산자를 사용할 수 있습니다.

**연결, 예 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**연결, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**연결, 예 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**연결, 예 4:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>중첩

구문은 `input.SelectMany(x=>x.Q())` `Q` `Select`, `SelectMany`또는 `Where` 연산자입니다.

중첩 쿼리는 외부 컨테이너의 각 요소에 내부 쿼리를 적용 합니다. 한 가지 중요 한 기능은 내부 쿼리가 자체 조인과 같이 외부 컨테이너에 있는 요소의 필드를 참조할 수 있다는 것입니다.

**중첩, 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**중첩, 예 2:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**중첩, 예제 3:**

- **LINQ 람다 식**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```


## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [모델 문서 데이터](modeling-data.md)
