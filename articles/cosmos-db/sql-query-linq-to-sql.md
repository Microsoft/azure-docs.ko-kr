---
title: LINQ to SQL에서 Azure Cosmos DB는 변환
description: Azure Cosmos DB SQL 쿼리에 LINQ 쿼리를 매핑합니다.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: 057614da8fd29e1208c2788049c5d6d1a985eed5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342587"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL 변환

Azure Cosmos DB 쿼리 공급자는 LINQ 쿼리에서 Cosmos DB SQL 쿼리로 매핑하기 위해 최대한 노력을 수행합니다. 다음 설명에 LINQ 사용 하 여에 대 한 기본 지식이 있다고 가정합니다.

쿼리 공급자 형식 시스템에서는 JSON 기본 형식만 지원 합니다: 숫자, 부울, 문자열 및 null입니다.

쿼리 공급자는 다음 스칼라 식이 지원합니다.

- 쿼리 평가 시 기본 데이터 형식의 상수 값을 포함 하 여 상수 값입니다.
  
- 개체 또는 배열 요소 속성을 참조 하는 속성/배열 인덱스 식입니다. 예를 들면 다음과 같습니다.
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- 산술 식-숫자 및 부울 값에 대 한 일반 산술 식을 포함 합니다. 전체 목록은 참조를 [Azure Cosmos DB SQL 사양](https://go.microsoft.com/fwlink/p/?LinkID=510612)합니다.
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- 상수 문자열 값은 문자열 값 비교를 포함 하는 문자열 비교 식입니다.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- 복합 값 형식 또는 무명 형식의 개체나 이러한 개체의 배열을 반환 하는 개체/배열 만들기 식입니다. 이러한 값을 중첩할 수 있습니다.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a id="SupportedLinqOperators"></a>지원 되는 LINQ 연산자

SQL.NET SDK에 포함 된 LINQ 공급자는 다음 연산자를 지원 합니다.

- **Select**: 프로젝션 개체 생성을 포함 하 여, SQL SELECT로 변환 합니다.
- **Where**: 필터는 SQL WHERE로 변환 지원과 간의 변환을 `&&`, `||`, 및 `!` SQL 연산자
- **SelectMany**: SQL JOIN 절에 대한 배열 해제를 허용합니다. 연결 또는 중첩 배열 요소를 필터링 하는 식을 사용 합니다.
- **OrderBy** 하 고 **OrderByDescending**: ASC 또는 DESC를 사용 하 여 ORDER BY로 변환 합니다.
- 집계를 위한 **Count**, **Sum**, **Min**, **Max** 및 **Average** 연산자와 해당 비동기 동급 연산자 **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** 및 **AverageAsync**
- **CompareTo**: 범위 비교로 변환합니다. 일반적으로.NET에서 비교 불가능 하므로 문자열에 대해 사용 합니다.
- **Take**: 쿼리 결과 제한 하기 위해 SQL TOP으로 변환 됩니다.
- **수치 연산 함수**: .NET의 변환을 지원 `Abs`, `Acos`, `Asin`, `Atan`, `Ceiling`를 `Cos`, `Exp`를 `Floor`, `Log`를 `Log10`, `Pow`, `Round`, `Sign`, `Sin`를 `Sqrt`를 `Tan`, 및 `Truncate` 동등한 SQL 기본 제공 함수입니다.
- **문자열 함수**: .NET의 변환을 지원 `Concat`, `Contains`, `Count`, `EndsWith`,`IndexOf`를 `Replace`, `Reverse`를 `StartsWith`, `SubString`를 `ToLower`, `ToUpper`, `TrimEnd`, 및 `TrimStart` 동등한 SQL 기본 제공 함수입니다.
- **배열 함수**: .NET의 변환을 지원 `Concat`, `Contains`, 및 `Count` 동등한 SQL 기본 제공 함수입니다.
- **지리 공간 확장 함수**: 스텁 메서드의 변환을 지원 `Distance`, `IsValid`를 `IsValidDetailed`, 및 `Within` 동등한 SQL 기본 제공 함수입니다.
- **사용자 정의 함수 확장 함수**: 스텁 메서드의의 변환을 지원 `UserDefinedFunctionProvider.Invoke` 해당 사용자 정의 함수입니다.
- **기타**: 변환을 지원 `Coalesce` 및 조건부 연산자입니다. 변환할 수 있습니다 `Contains` 문자열 CONTAINS, ARRAY_CONTAINS 또는 SQL IN, 컨텍스트에 따라를 합니다.

## <a name="examples"></a>예

다음 예제에서는 표준 LINQ 쿼리 연산자 중 일부가 Cosmos DB 쿼리로 변환 하는 방법을 보여 줍니다.

### <a name="select-operator"></a>Select 연산자

구문은 `input.Select(x => f(x))`입니다. 여기서 `f`는 스칼라 식입니다.

**예제 1 연산자를 선택 합니다.**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**예제 2 연산자를 선택 합니다.** 

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**예제 3 연산자를 선택 합니다.**

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

**여기서 연산자, 예제 1:**

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
  
**여기서 연산자, 예제 2:**

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

위의 연산자는 보다 강력한 쿼리를 작성할 수 있습니다. Cosmos DB는 중첩 된 컨테이너를 지원 하므로 연결 하거나 컴퍼지션 중첩할 수 있습니다.

### <a name="concatenation"></a>연결

구문은 `input(.|.SelectMany())(.Select()|.Where())*`입니다. 연결 된 쿼리를 선택적으로 시작할 수 있습니다 `SelectMany` 뒤에 여러 쿼리에서 `Select` 또는 `Where` 연산자입니다.

**연결 예제 1:**

- **LINQ 람다 식**
  
  ```csharp
      input.Select(family=>family.parents[0])
          .Where(familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**연결 예제 2:**

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

**연결 예 3:**

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

**연결 예 4:**

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

구문은 `input.SelectMany(x=>x.Q())` 여기서 `Q` 되는 `Select`, `SelectMany`, 또는 `Where` 연산자.

중첩된 쿼리는 외부 컨테이너의 각 요소에 내부 쿼리에 적용 됩니다. 하나의 중요 한 기능은 자체 조인 처럼 외부 컨테이너의 요소 필드 내부 쿼리에서 참조할 수는 것입니다.

**중첩 예제 1:**

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

**중첩 예제 2:**

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

**중첩 예제 3:**

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

- [Azure Cosmos DB .NET 샘플](https://github.com/Azure/azure-cosmosdb-dotnet)
- [문서 데이터 모델](modeling-data.md)
