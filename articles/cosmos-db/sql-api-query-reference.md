---
title: Azure Cosmos DB의 SQL 언어 구문
description: 이 문서에서는 Azure Cosmos DB에 사용된 SQL 쿼리 언어 구문, 다른 연산자 및 이 언어로 사용 가능한 키워드에 대해 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 22b03417495625ef70650a015530d6f56b32fd4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626890"
---
# <a name="sql-language-reference-for-azure-cosmos-db"></a>Azure Cosmos DB의 SQL 언어 참조 

Azure Cosmos DB는 명시적 스키마를 요구하거나 보조 인덱스를 만들지 않고 계층적 JSON 문서에 대한 문법과 같은 익숙한 SQL(구조적 쿼리 언어)을 사용하여 문서를 쿼리하는 기능을 지원합니다. 이 문서에서는 SQL API 계정에 사용 되는 SQL 쿼리 언어 구문에 대 한 설명서를 제공 합니다. 연습은 SQL 쿼리 예제를 참조 하세요 [Cosmos DB에서 SQL 쿼리 예제](how-to-sql-query.md)합니다.  
  
방문 합니다 [쿼리 실습](https://www.documentdb.com/sql/demo), Cosmos DB를 시도 하 고 수 있는 샘플 데이터 집합에 대해 SQL 쿼리를 실행 합니다.  
  
## <a name="select-query"></a>SELECT 쿼리  
ANSI-SQL 표준에 따라 모든 쿼리는 SELECT 절과 선택적 FROM 및 WHERE 절로 구성됩니다. 일반적으로 각 쿼리에 대해 FROM 절에서 원본 열거 될 다음 WHERE 절의 필터 JSON 문서의 하위 집합을 검색할 원본에 적용 됩니다. 마지막으로, SELECT 절을 사용하여 선택 목록에서 요청된 JSON 값을 프로젝션합니다. 예를 들어 [SELECT 쿼리 예제](how-to-sql-query.md#SelectClause)를 참조하세요.
  
**구문**  
  
```sql
<select_query> ::=  
SELECT <select_specification>   
    [ FROM <from_specification>]   
    [ WHERE <filter_condition> ]  
    [ ORDER BY <sort_specification> ]  
```  
  
 **주의**  
  
 각 절에 대한 자세한 내용은 다음 섹션을 참조하세요.  
  
-   [SELECT 절](#bk_select_query)    
-   [FROM 절](#bk_from_clause)    
-   [WHERE 절](#bk_where_clause)    
-   [ORDER BY 절](#bk_orderby_clause)  
  
SELECT 문의 절은 위에서 표시한 순서대로 지정해야 합니다. 선택적 절 중 하나는 생략할 수 있습니다. 그러나 선택적 절이 사용되면 올바른 순서로 표시되어야 합니다.  
  
### <a name="logical-processing-order-of-the-select-statement"></a>SELECT 문의 논리적 처리 순서  
  
절이 처리되는 순서는 다음과 같습니다.  

1.  [FROM 절](#bk_from_clause)  
2.  [WHERE 절](#bk_where_clause)  
3.  [ORDER BY 절](#bk_orderby_clause)  
4.  [SELECT 절](#bk_select_query)  

이 순서는 구문에 표시되는 순서와 다릅니다. 순서를 지정하면 처리된 절에서 도입된 새로운 모든 기호가 표시되고, 나중에 처리되는 절에서 사용될 수 있습니다. 예를 들어 FROM 절에 선언된 별칭은 WHERE 절과 SELECT 절에서 액세스할 수 있습니다.  

### <a name="whitespace-characters-and-comments"></a>공백 문자 및 주석  

따옴표가 붙은 문자열이나 식별자의 일부가 아닌 모든 공백 문자는 언어 문법의 일부가 아니며 구문 분석에서 무시됩니다.  

쿼리 언어는 다음과 같은 T-SQL 스타일 주석을 지원합니다.  

-   `-- comment text [newline]` SQL 문  

공백 문자와 주석은 문법에서 아무런 의미가 없지만 토큰을 분리하는 데 사용해야 합니다. 예를 들어 `-1e5`는 단일 숫자 토큰이지만, `: – 1 e5`는 숫자 1과 식별자 e5가 뒤에 나오는 마이너스 토큰입니다.  

##  <a name="bk_select_query"></a> SELECT 절  
SELECT 문의 절은 위에서 표시한 순서대로 지정해야 합니다. 선택적 절 중 하나는 생략할 수 있습니다. 그러나 선택적 절이 사용되면 올바른 순서로 표시되어야 합니다. 예를 들어 [SELECT 쿼리 예제](how-to-sql-query.md#SelectClause)를 참조하세요.

**구문**  

```sql
SELECT <select_specification>  

<select_specification> ::=   
      '*'   
      | <object_property_list>   
      | VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=   
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
  
```  
  
 **인수**  
  
- `<select_specification>`  

  결과 집합에 대해 선택되는 속성 또는 값입니다.  
  
- `'*'`  

  변경하지 않고 값을 검색하도록 지정합니다. 특히 처리된 값이 개체이면 모든 속성이 검색됩니다.  
  
- `<object_property_list>`  
  
  검색할 속성의 목록을 지정합니다. 반환된 각 값은 지정된 속성이 있는 개체입니다.  
  
- `VALUE`  

  완전한 JSON 개체 대신 JSON 값을 검색하도록 지정합니다. 이것은 `<property_list>`와 달리 개체에 프로젝션된 값을 래핑하지 않습니다.  
  
- `<scalar_expression>`  

  계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](#bk_scalar_expressions) 섹션을 참조하세요.  
  
**주의**  
  
`SELECT *` 구문은 FROM 절에서 정확히 하나의 별칭을 선언한 경우에만 유효합니다. `SELECT *`는 ID 프로젝션을 제공하며, 이는 프로젝션이 필요하지 않은 경우 유용할 수 있습니다. SELECT *는 FROM 절이 지정되고 단일 입력 원본만 도입된 경우에만 유효합니다.  
  
`SELECT <select_list>`와 `SELECT *`는 "syntactic sugar(구문적 설탕)"이며 다음과 같이 간단한 SELECT 문을 사용하여 표현할 수 있습니다.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   이는 다음과 동등합니다.  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   이는 다음과 동등합니다.  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
**참고 항목**  
  
[스칼라 식](#bk_scalar_expressions)  
[SELECT 절](#bk_select_query)  
  
##  <a name="bk_from_clause"></a> FROM 절  
원본 또는 조인된 원본을 지정합니다. 쿼리의 뒷부분에서 소스를 필터링/프로젝션하지 않을 경우 FROM 절은 선택 사항입니다. 이 절의 목적은 쿼리가 작동해야 하는 데이터 원본을 지정하는 것입니다. 일반적으로 전체 컨테이너가 소스이지만 컨테이너의 하위 집합을 대신 지정할 수 있습니다. 이 절을 지정하지 않으면 FROM 절에서 단일 문서를 제공하는 것처럼 다른 절도 계속 실행됩니다. 예를 들어 [FROM 절 예제](how-to-sql-query.md#FromClause)를 참조하세요.
  
**구문**  
  
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
  
**인수**  
  
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
  
**주의**  
  
`<from_source>(`에서 제공되거나 유추되는 모든 별칭은 고유해야 합니다. `<container_expression>.`property_name 구문은 `<container_expression>' ['"property_name"']'`과 같습니다. 그러나 속성 이름에 비식별자 문자가 포함되어 있으면 후자의 구문을 사용할 수 있습니다.  
  
### <a name="handling-missing-properties-missing-array-elements-and-undefined-values"></a>누락된 속성, 누락된 배열 요소, 정의되지 않은 값 처리
  
컨테이너 식에서 속성이나 배열 요소에 액세스하고 해당 값이 존재하지 않으면 이 값은 무시되고 더 이상 처리되지 않습니다.  
  
### <a name="container-expression-context-scoping"></a>컨테이너 식 컨텍스트 범위 지정  
  
컨테이너 식은 컨테이너 범위 또는 문서 범위일 수 있습니다.  
  
-   컨테이너 식의 기본 원본이 ROOT 또는 `container_name`이면 식은 컨테이너 범위입니다. 이러한 식은 컨테이너에서 직접 검색되는 문서 집합을 나타내며 다른 컨테이너 식의 처리에 종속되지 않습니다.  
  
-   컨테이너 식의 기본 원본이 쿼리의 앞 부분에 도입된 `input_alias`이면 식은 문서 범위입니다. 이러한 식은 별칭 지정된 컨테이너와 연결된 집합에 속한 각 문서의 범위에서 컨테이너 식을 평가하여 얻는 문서 집합을 나타냅니다.  결과 집합은 기본 집합에 있는 각 문서에 대해 컨테이너 식을 평가하여 얻는 집합의 합집합입니다.  
  
### <a name="joins"></a>조인 
  
현재 릴리스의 Cosmos DB에서는 내부 조인을 지원합니다. 추가 조인 기능이 곧 출시됩니다. 

내부 조인은 조인에 참여하는 집합의 완전한 교차곱을 만듭니다. N 방향 조인의 결과는 N 요소 튜플의 집합이며, 여기서 튜플의 각 값은 조인에 참여하는 별칭 지정된 집합과 연결되며 다른 절에서 해당 별칭을 참조하여 액세스할 수 있습니다. 예를 들어 [JOIN 키워드 예제](how-to-sql-query.md#Joins)를 참조하세요.
  
조인 평가는 참여하는 집합의 컨텍스트 범위에 따라 다릅니다.  
  
- 컨테이너 집합 A와 컨테이너 범위 집합 B을 조인하면 집합 A와 집합 B에 있는 모든 요소의 교차곱을 만듭니다.
  
- 집합 A와 문서 범위 집합 B를 조인하면 집합 A의 각 문서에 대해 문서 범위 집합 B를 평가하여 얻는 모든 집합의 합집합을 만듭니다.  
  
  현재 릴리스의 쿼리 프로세서에서는 컨테이너 범위가 지정된 식 하나만 지원합니다.  
  
### <a name="examples-of-joins"></a>조인 예제  
  
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
  
**참고 항목**  
  
 [SELECT 절](#bk_select_query)  
  
##  <a name="bk_where_clause"></a> WHERE 절  
 쿼리에서 반환되는 문서에 대한 검색 조건을 지정합니다. 예를 들어 [WHERE 절 예제](how-to-sql-query.md#WhereClause)를 참조하세요.
  
 **구문**  
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
 **인수**  
  
- `<filter_condition>`  
  
   반환할 문서에 대해 충족하는 조건을 지정합니다.  
  
- `<scalar_expression>`  
  
   계산할 값을 나타내는 식입니다. 자세한 내용은 [스칼라 식](#bk_scalar_expressions) 섹션을 참조하세요.  
  
  **주의**  
  
  문서를 반환하려면 필터 조건으로 지정된 식을 true로 평가해야 합니다. true 부울 값만 조건을 충족하고, 다른 값(undefined, null, false, 숫자, 배열 또는 개체)은 조건을 충족하지 않습니다.  
  
##  <a name="bk_orderby_clause"></a> ORDER BY 절  
 쿼리에서 반환되는 결과의 정렬 순서를 지정합니다. 예를 들어 [ORDER BY 절 예제](how-to-sql-query.md#OrderByClause)를 참조하세요.
  
 **구문**  
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= <scalar_expression> [ASC | DESC]  
  
```  
  
 **인수**  
  
- `<sort_specification>`  
  
   쿼리 결과 집합을 정렬할 속성이나 식을 지정합니다. 정렬 열은 이름 또는 열 별칭으로 지정할 수 있습니다.  
  
   여러 정렬 열을 지정할 수 있습니다. 열 이름은 고유해야 합니다. ORDER BY 절의 정렬 열 순서는 정렬되는 결과 집합의 구성을 정의합니다. 즉 결과 집합이 첫 번째 속성으로 정렬된 다음 정렬된 해당 목록이 두 번째 속성으로 정렬되는 등등입니다.  
  
   ORDER BY 절에서 참조되는 열 이름은 선택 목록의 열 이름 또는 FROM 절에 지정된 테이블에 정의된 열 이름과 정확히 일치해야 합니다.  
  
- `<sort_expression>`  
  
   쿼리 결과 집합을 정렬할 단일 속성 또는 식을 지정합니다.  
  
- `<scalar_expression>`  
  
   자세한 내용은 [스칼라 식](#bk_scalar_expressions) 섹션을 참조하세요.  
  
- `ASC | DESC`  
  
   지정된 열의 값을 오름차순 또는 내림차순으로 정렬하도록 지정합니다. ASC는 가장 낮은 값에서 가장 높은 값으로 정렬합니다. DESC는 가장 높은 값에서 가장 낮은 값으로 정렬합니다. ASC가 기본 정렬 순서입니다. 널 값은 가능한 가장 낮은 값으로 처리됩니다.  
  
  **주의**  
  
  쿼리 문법은 속성 기준으로 여러 가지 순서를 지원하지만, Cosmos DB 쿼리 런타임은 단일 속성에 대해서만 정렬을 지원하며, 계산된 속성이 아니라 속성 이름에 대해서만 정렬을 지원합니다. 또한 정렬하기 위해서는 인덱싱 정책에 속성에 대한 범위 인덱스와 지정된 유형이 최대 정밀도로 포함되어야 합니다. 자세한 내용은 인덱싱 정책 설명서를 참조하세요.  
  
##  <a name="bk_scalar_expressions"></a> 스칼라 식  
 스칼라 식은 단일 값을 얻기 위해 평가될 수 있는 기호와 연산자의 조합입니다. 간단한 식은 상수, 속성 참조, 배열 요소 참조, 별칭 참조 또는 함수 호출일 수 있으며, 연산자를 사용하여 복잡한 식으로 결합될 수 있습니다. 예를 들어 [스칼라 식 예제](how-to-sql-query.md#scalar-expressions)를 참조하세요.
  
 스칼라 식에 포함될 수 있는 값에 대한 자세한 내용은 [상수](#bk_constants) 섹션을 참조하세요.  
  
 **구문**  
  
```sql  
<scalar_expression> ::=  
       <constant>   
     | input_alias   
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>   
     | <create_array_expression>  
     | (<scalar_expression>)   
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```  
  
 **인수**  
  
- `<constant>`  
  
   상수 값을 나타냅니다. 자세한 내용은 [상수](#bk_constants) 섹션을 참조하세요.  
  
- `input_alias`  
  
   `FROM` 절에 도입된 `input_alias`에서 정의된 값을 나타냅니다.  
  이 값은 **undefined**가 되지 않도록 보장되며, 입력에 있는 **undefined** 값은 건너뜁니다.  
  
- `<scalar_expression>.property_name`  
  
   개체의 속성 값을 나타냅니다. 속성이 존재하지 않거나 개체가 아닌 값에서 참조되면 식이 **undefined** 값으로 평가됩니다.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   개체/배열의 `property_name` 이름이 있는 속성 또는 `array_index` 인덱스가 있는 배열 요소의 값을 나타냅니다. 속성/배열 인덱스가 존재하지 않거나 속성/배열이 아닌 값에서 참조되면 식이 undefined 값으로 평가됩니다.  
  
- `unary_operator <scalar_expression>`  
  
   단일 값에 적용되는 연산자를 나타냅니다. 자세한 내용은 [연산자](#bk_operators) 섹션을 참조하세요.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   두 값에 적용되는 연산자를 나타냅니다. 자세한 내용은 [연산자](#bk_operators) 섹션을 참조하세요.  
  
- `<scalar_function_expression>`  
  
   함수 호출의 결과로 정의되는 값을 나타냅니다.  
  
- `udf_scalar_function`  
  
   사용자 정의 스칼라 함수의 이름입니다.  
  
- `builtin_scalar_function`  
  
   기본 제공 스칼라 함수의 이름입니다.  
  
- `<create_object_expression>`  
  
   지정된 속성 및 해당 값이 포함된 새 개체를 만들어서 얻는 값을 나타냅니다.  
  
- `<create_array_expression>`  
  
   요소로 지정된 값이 포함된 새 배열을 만들어서 얻는 값을 나타냅니다.  
  
- `parameter_name`  
  
   지정된 매개 변수 이름의 값을 나타냅니다. 매개 변수 이름에는 첫 번째 문자로 \@가 하나 있어야 합니다.  
  
  **주의**  
  
  기본 제공 또는 사용자 정의 스칼라 함수를 호출할 때 모든 인수가 정의되어야 합니다. 인수 중 하나라도 정의되지 않으면 함수가 호출되지 않고 결과가 정의되지 않습니다.  
  
  개체를 만들 때 정의되지 않은 값이 할당된 속성은 건너뛰고 만든 개체에 포함되지 않습니다.  
  
  배열을 만들 때 **undefined** 값이 할당된 요소 값은 건너뛰고 만든 개체에 포함되지 않습니다. 이렇게 하면 건너뛴 인덱스가 만드는 배열에 포함되지 않는 방식으로 다음에 정의된 요소가 해당 위치로 이동합니다.  
  
##  <a name="bk_operators"></a> 연산자  
 이 섹션에서는 지원되는 연산자에 대해 설명합니다. 각 연산자는 정확히 하나의 범주에 할당할 수 있습니다.  
  
 **undefined** 값 처리 입력 값 형식 요구 사항 및 일치하지 않는 형식의 값 처리에 대한 자세한 내용은 아래의 **연산자 범주** 표를 참조하세요.  
  
 **연산자 범주**  
  
|**범주**|**세부 정보**|  
|-|-|  
|**산술**|연산자에서 입력은 숫자여야 합니다. 출력도 숫자입니다. 입력 중 하나가 **undefined**이거나 숫자 이외의 형식이면 결과는 **undefined**입니다.|  
|**비트**|연산자에서 입력은 부호 있는 32비트 정수여야 합니다. 출력도 부호 있는 32비트 정수입니다.<br /><br /> 정수가 아닌 값은 끝수를 자릅니다. 양수 값은 끝수 내림되고, 음수 값은 끝수 올림됩니다.<br /><br /> 32비트 정수 범위를 벗어나는 값은 2의 보수 표기 중 마지막 32비트를 취하여 변환됩니다.<br /><br /> 입력 중 하나가 **undefined**이거나 숫자 이외의 형식이면 결과는 **undefined**입니다.<br /><br /> **참고:** 위의 동작은 JavaScript 비트 연산자 동작과 호환됩니다.|  
|**논리**|연산자에서 입력은 부울이어야 합니다. 출력도 부울입니다.<br />입력 중 하나가 **undefined**이거나 부울 이외의 형식이면 결과는 **undefined**입니다.|  
|**비교**|연산자에서 입력은 동일한 형식이어야 하며 undefined가 아니어야 합니다. 출력은 부울입니다.<br /><br /> 입력 중 하나가 **undefined**이거나 다른 형식의 입력이면 결과는 **undefined**입니다.<br /><br /> 값 순서 지정에 대한 자세한 내용은 **비교 연산자의 값 순서 지정** 표를 참조하세요.|  
|**string**|연산자에서 입력은 문자열이어야 합니다. 출력도 문자열입니다.<br />입력 중 하나가 **undefined**이거나 문자열 이외의 형식이면 결과는 **undefined**입니다.|  
  
 **단항 연산자:**  
  
|**Name**|**연산자**|**세부 정보**|  
|-|-|-|  
|**산술**|+<br /><br /> -|숫자 값을 반환합니다.<br /><br /> 비트 부정입니다. 음수 값을 반환합니다.|  
|**비트**|~|보수입니다. 숫자 값의 보수를 반환합니다.|  
|**논리**|**다음이 아님**|부정입니다. 부정 부울 값을 반환합니다.|  
  
 **이항 연산자:**  
  
|**Name**|**연산자**|**세부 정보**|  
|-|-|-|  
|**산술**|+<br /><br /> -<br /><br /> *<br /><br /> /<br /><br /> %|더하기<br /><br /> 빼기<br /><br /> 곱하기<br /><br /> 나누기<br /><br /> Modulo 연산|  
|**비트**|&#124;<br /><br /> &<br /><br /> ^<br /><br /> <<<br /><br /> >><br /><br /> >>>|비트 OR<br /><br /> 비트 AND<br /><br /> 비트 XOR<br /><br /> 왼쪽 시프트<br /><br /> 오른쪽 시프트<br /><br /> 0 채우기 오른쪽 시프트|  
|**논리**|**및**<br /><br /> **또는**|논리 결합입니다. 두 인수가 **true**이면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 논리적 분리. 인수가 **true**이면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.|  
|**비교**|**=**<br /><br /> **!=, <>**<br /><br /> **>**<br /><br /> **>=**<br /><br /> **<**<br /><br /> **<=**<br /><br /> **??**|같음 - 두 인수가 같으면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 같지 않음 - 인수가 같지 않으면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 큼 - 첫 번째 인수가 두 번째 인수보다 크면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 크거나 같음 - 첫 번째 인수가 두 번째 인수보다 크거나 같으면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 작음 - 첫 번째 인수가 두 번째 인수보다 작으면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 작거나 같음 - 첫 번째 인수가 두 번째 인수보다 작거나 같으면 **true**를 반환하고, 그렇지 않으면 **false**를 반환합니다.<br /><br /> 병합 - 첫 번째 인수가 **undefined** 값이면 두 번째 인수를 반환합니다.|  
|**String**|**&#124;&#124;**|연결 - 두 인수의 연결을 반환합니다.|  
  
 **삼항 연산자:**  

|**Name**|**연산자**|**세부 정보**| 
|-|-|-|  
|삼항 연산자|?|첫 번째 인수가 **true**로 평가되면 두 번째 인수를 반환하고, 그렇지 않으면 세 번째 인수를 반환합니다.|  

  
 **비교 연산자의 값 순서 지정**  
  
|**형식**|**값 순서**|  
|-|-|  
|**Undefined**|비교할 수 없음|  
|**Null**|단일 값: **null**|  
|**Number**|자연수입니다.<br /><br /> Negative Infinity(음의 무한대) 값은 다른 Number 값보다 작습니다.<br /><br /> Positive Infinity(양의 무한대) 값은 다른 Number 값보다 큽니다. **NaN** 값은 비교할 수 없습니다. **NaN**과 비교하면 **undefined** 값이 됩니다.|  
|**String**|사전순 정렬|  
|**Array**|순서를 지정하지 않지만 동등하게 지정합니다.|  
|**Object**|순서를 지정하지 않지만 동등하게 지정합니다.|  
  
 **주의**  
  
 Cosmos DB에서 값 형식은 데이터베이스에서 검색될 때까지 알 수 없는 경우가 종종 있습니다. 쿼리의 효율적인 실행을 지원하기 위해 대부분의 연산자에는 엄격한 형식 요구 사항이 있습니다. 또한 연산자 자체는 암시적 변환을 수행하지 않습니다.  
  
 즉, SELECT * FROM ROOT r WHERE r.Age = 21과 같은 쿼리는 Age 속성이 21인 문서만 반환합니다. "21" = 21 식이 undefined로 평가되므로 Age 속성이 "21" 또는 "0021" 문자열과 일치하지 않는 문서는 일치하지 않는 문서가 됩니다. 이렇게 하면 특정 값(예: 숫자 21)의 조회가 무한 수의 잠재적 일치 항목(숫자 21 또는 문자열 "21", "021", "21.0"...)을 검색하는 것보다 빠르기 때문에 인덱스를 더 잘 사용할 수 있습니다. 이는 JavaScript에서 다른 형식의 값에 대해 연산자를 평가하는 방식과 다릅니다.  
  
 **배열 및 개체 같음 및 비교**  
  
 범위 연산자(>, >=, <, <=)를 사용하여 배열 또는 개체 값을 비교하면 배열 또는 개체 값에 정의된 순서가 없으므로 결과가 정의되지 않습니다. 그러나 같음/같지 않음 연산자(=, !=, <>)를 사용할 수 있으며, 이 경우 값이 구조적으로 비교됩니다.  
  
 두 배열의 요소 수가 같고 일치하는 위치의 요소도 같은 경우 배열은 같습니다. 요소 쌍을 비교할 때 정의되지 않은 결과가 나오는 경우 배열 비교의 결과는 정의되지 않습니다.  
  
 두 개체에 동일한 속성이 정의되어 있고 일치하는 속성의 값도 같은 경우 개체는 같습니다. 속성 값 쌍을 비교할 때 정의되지 않은 결과가 나오는 경우 개체 비교의 결과는 정의되지 않습니다.  
  
##  <a name="bk_constants"></a> 상수  
 리터럴 또는 스칼라 값이라고도 하는 상수는 특정 데이터 값을 나타내는 기호입니다. 상수의 형식은 나타내는 값의 데이터 형식에 따라 다릅니다.  
  
 **지원되는 스칼라 데이터 형식:**  
  
|**형식**|**값 순서**|  
|-|-|  
|**Undefined**|단일 값: **undefined**|  
|**Null**|단일 값: **null**|  
|**Boolean**|값: **false**, **true**.|  
|**Number**|IEEE 754 표준의 두 자리 부동 소수점 숫자입니다.|  
|**String**|0개 이상의 유니코드 문자 시퀀스입니다. 문자열은 작은따옴표 또는 큰 따옴표로 묶어야 합니다.|  
|**Array**|0개 이상의 요소 시퀀스입니다. 각 요소는 Undefined를 제외한 모든 스칼라 데이터 형식의 값일 수 있습니다.|  
|**Object**|순서가 지정되지 않은 0개 이상의 이름/값 쌍의 집합입니다. 이름은 유니코드 문자열이며, 값은 **Undefined**를 제외한 모든 스칼라 데이터 형식이 될 수 있습니다.|  
  
 **구문**  
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
 **인수**  
  
* `<undefined_constant>; undefined`  
  
  Undefined 형식의 undefined 값을 나타냅니다.  
  
* `<null_constant>; null`  
  
  **Null** 형식의 **null** 값을 나타냅니다.  
  
* `<boolean_constant>`  
  
  Boolean 형식의 상수를 나타냅니다.  
  
* `false`  
  
  Boolean 형식의 **false** 값을 나타냅니다.  
  
* `true`  
  
  Boolean 형식의 **true** 값을 나타냅니다.  
  
* `<number_constant>`  
  
  상수를 나타냅니다.  
  
* `decimal_literal`  
  
  10진 리터럴은 10진수 표기법 또는 과학적 표기법을 사용하여 표현되는 숫자입니다.  
  
* `hexadecimal_literal`  
  
  16진 리터럴은 '0x' 접두사 다음에 하나 이상의 16진수가 나오는 숫자입니다.  
  
* `<string_constant>`  
  
  String 형식의 상수를 나타냅니다.  
  
* `string _literal`  
  
  문자열 리터럴은 연속적인 0이상의 유니코드 문자 또는 이스케이프 시퀀스로 표현되는 유니코드 문자열입니다. 문자열 리터럴은 작은따옴표(아포스트로피: ') 또는 큰따옴표(따옴표: ")로 묶어야 합니다.  
  
  허용되는 이스케이프 시퀀스는 다음과 같습니다.  
  
|**이스케이프 시퀀스**|**설명**|**유니코드 문자**|  
|-|-|-|  
|\\'|아포스트로피(')|U+0027|  
|\\"|따옴표(")|U+0022|  
|\\\ |백슬래시(\\)|U+005C|  
|\\/|슬래시(/)|U+002F|  
|\b|백스페이스|U+0008|  
|\f|폼 피드|U+000C|  
|\n|줄 바꿈|U+000A|  
|\r|캐리지 리턴|U+000D|  
|\t|탭|U+0009|  
|\uXXXX|4자리 16진수로 정의된 유니코드 문자|U+XXXX|  
  
##  <a name="bk_query_perf_guidelines"></a> 쿼리 성능 지침  
 대형 컨테이너에 대해 쿼리를 효율적으로 실행하려면 하나 이상의 인덱스를 통해 제공될 수 있는 필터를 사용해야 합니다.  
  
 인덱스 조회에 대해 고려되는 필터는 다음과 같습니다.  
  
- 문서 경로 식 및 상수와 함께 같음 연산자(=)를 사용합니다.  
  
- 문서 경로 식 및 상수와 함께 범위 연산자(<, \<=, >, >=)를 사용합니다.  
  
- 문서 경로 식은 참조되는 데이터베이스 컨테이너에서 문서의 상수 경로를 식별하는 모든 식을 나타냅니다.  
  
  **문서 경로 식**  
  
  문서 경로 식은 데이터베이스 컨테이너 문서에서 제공하는 문서를 통해 속성 또는 배열 인덱서의 경로를 평가하는 식입니다. 이 경로는 필터에서 직접 참조되는 값의 위치를 데이터베이스 컨테이너의 문서 내에서 식별하는 데 사용될 수 있습니다.  
  
  문서 경로 식으로 간주되는 식의 조건은 다음과 같습니다.  
  
1.  컨테이너 루트를 직접 참조합니다.  
  
2.  일부 문서 경로 식의 속성 또는 상수 배열 인덱서를 참조합니다.  
  
3.  일부 문서 경로 식을 나타내는 별칭을 참조합니다.  
  
     **구문 규칙**  
  
     다음 표에서는 아래 SQL 참조의 구문을 설명하는 데 사용되는 규칙을 설명합니다.  
  
    |**규칙**|**용도**|  
    |-|-|    
    |대문자|대/소문자를 구분하지 않는 키워드|  
    |소문자|대/소문자를 구분하는 키워드|  
    |\<nonterminal>|비단말(nonterminal)이며, 개별적으로 정의됩니다.|  
    |\<nonterminal> ::=|비단말의 구문 정의|  
    |other_terminal|단말(토큰)이며, 단어로 자세히 설명됩니다.|  
    |identifier|식별자입니다. 허용되는 문자: a-z, A-Z, 0-9. 첫 번째 문자는 숫자가 아니어야 합니다.|  
    |"문자열"|따옴표가 붙은 문자열이며, 유효한 문자열이 모두 허용됩니다. string_literal에 대한 설명을 참조합니다.|  
    |'기호'|구문의 일부인 문자 기호|  
    |&#124; (세로줄)|구문 항목에 대한 대안이며, 지정된 항목 중 하나만 사용할 수 있습니다.|  
    |[ ] /(대괄호)|대괄호는 하나 이상의 선택 항목을 묶습니다.|  
    |[ ,...n ]|앞의 항목이 n번 반복될 수 있음을 나타냅니다. 각 항목은 쉼표로 구분됩니다.|  
    |[ ...n ]|앞의 항목이 n번 반복될 수 있음을 나타냅니다. 각 항목은 공백으로 구분 됩니다.|  
  
##  <a name="bk_built_in_functions"></a> 기본 제공 함수  
 Cosmos DB는 많은 기본 제공 SQL 함수를 제공합니다. 기본 제공 함수의 범주는 다음과 같습니다.  
  
|함수|설명|  
|--------------|-----------------|  
|[수치 연산 함수](#bk_mathematical_functions)|수치 연산 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.|  
|[형식 검사 함수](#bk_type_checking_functions)|형식 검사 함수를 통해 SQL 쿼리 내에서 식의 형식을 검사할 수 있습니다.|  
|[문자열 함수](#bk_string_functions)|문자열 함수는 문자열 입력 값에 대한 연산을 수행하고, 문자열, 숫자 또는 부울 값을 반환합니다.|  
|[배열 함수](#bk_array_functions)|배열 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울 또는 배열 값을 반환합니다.|  
|[공간 함수](#bk_spatial_functions)|공간 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다.|  
  
###  <a name="bk_mathematical_functions"></a> 수치 연산 함수  
 다음 함수는 각각 인수로 제공된 입력 값에 따라 계산을 수행하고 숫자 값을 반환합니다.  
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[CEILING](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[각도](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[로그](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[전원](#bk_power)|  
|[라디안](#bk_radians)|[반올림](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[정사각형](#bk_square)|[로그인](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 지정한 숫자 식의 절대(양수) 값을 반환합니다.  
  
 **구문**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 세 개의 다른 숫자에 ABS 함수를 사용한 결과를 보여 줍니다.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 코사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크코사인이라고도 합니다.  
  
 **구문**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -1의 ACOS를 반환합니다.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 사인 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크사인이라고도 합니다.  
  
 **구문**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -1의 ASIN을 반환합니다.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 탄젠트 값이 지정된 숫자 식인 라디안에서 각도를 반환합니다. 아크탄젠트라고도 합니다.  
  
 **구문**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 값의 ATAN을 반환합니다.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 y/x에 대한 아크 탄젠트의 주요 값(라디안 단위)을 반환합니다.  
  
 **구문**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 x 및 y 구성 요소에 대한 ATN2를 계산합니다.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> CEILING  
 지정한 숫자 식보다 크거나 같은 가장 작은 정수 값을 반환합니다.  
  
 **구문**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 CEILING 함수를 사용하여 양수, 음수 및 0 값을 보여 줍니다.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 지정된 식의 라디안에서 지정된 각도의 삼각 코사인을 반환합니다.  
  
 **구문**  
  
```  
COS(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 COS를 계산합니다.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 지정된 숫자 식의 라디안에서 지정된 각도의 삼각 코탄젠트를 반환합니다.  
  
 **구문**  
  
```  
COT(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 COT를 계산합니다.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> DEGREES  
 라디안에서 지정된 각도로 해당하는 각도를 반환합니다.  
  
 **구문**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI/2 라디안 각도의 각도 수를 반환합니다.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 지정한 숫자 식보다 작거나 같은 가장 큰 정수 값을 반환합니다.  
  
 **구문**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 FLOOR 함수를 사용하여 양수, 음수 및 0 값을 보여 줍니다.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 지정된 숫자 식의 지수 값을 반환합니다.  
  
 **구문**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **주의**  
  
  **e**(2.718281…) 상수는 자연 로그의 밑입니다.  
  
  숫자의 지수는 **e** 상수를 거듭제곱하는 횟수입니다. 예를 들어 EXP(1.0) = e^1.0 = 2.71828182845905이며, EXP(10) = e^10 = 22026.4657948067입니다.  
  
  숫자의 자연 로그의 지수는 숫자 자체, 즉 EXP (LOG (n)) = n입니다. 그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG (EXP (n)) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 지수 값을 반환합니다.  
  
```  
SELECT EXP(10) AS exp  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 다음 예제에서는 20의 자연 로그의 지수 값과 및 20의 지수의 자연 로그를 반환합니다. 이러한 함수는 서로 역함수이므로 두 경우 모두 부동 소수점 수치가 반올림된 반환 값은 20입니다.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> LOG  
 지정된 숫자 식의 자연 로그를 반환합니다.  
  
 **구문**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
- `base`  
  
   로그의 밑을 설정하는 선택적 숫자 인수입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **설명**  
  
  LOG()는 기본적으로 자연 로그를 반환합니다. 선택적인 base 매개 변수를 사용하여 로그의 밑을 다른 값으로 변경할 수 있습니다.  
  
  자연 로그는 **e**를 밑으로 하는 로그입니다. 여기서 **e**는 대략 2.718281828과 같은 무리 상수입니다.  
  
  그리고 숫자의 지수의 자연 로그도 숫자 자체, 즉 LOG( EXP( n ) ) = n입니다. 그리고 숫자의 자연 로그의 지수도 숫자 자체, 즉 EXP( LOG( n ) ) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (10)의 로그 값을 반환합니다.  
  
```  
SELECT LOG(10) AS log  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 다음 예제에서는 숫자의 지수에 대한 LOG를 계산합니다.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 지정한 숫자 식의 상용 로그(밑 10)를 반환합니다.  
  
 **구문**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **주의**  
  
  LOG10과 POWER 함수는 서로 역의 관계가 있습니다. 예를 들어 10 ^ LOG10(n) = n입니다.  
  
  **예**  
  
  다음 예제에서는 변수를 선언하고 지정된 변수 (100)의 LOG10 값을 반환합니다.  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 PI의 상수 값을 반환합니다.  
  
 **구문**  
  
```  
PI ()  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI의 값을 반환합니다.  
  
```  
SELECT PI() AS pi 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> POWER  
 지정된 식의 값을 지정된 거듭제곱으로 반환합니다.  
  
 **구문**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
- `y`  
  
   `numeric_expression`이 거듭제곱되는 지수입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 숫자를 3의 지수로 거듭제곱합니다(숫자의 3제곱).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> RADIANS  
 숫자 식을 단위로 입력하면 라디안을 반환합니다.  
  
 **구문**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 몇 개의 각도를 입력으로 사용하여 해당 라디안 값을 반환합니다.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 가장 가까운 정수 값으로 반올림한 숫자 값을 반환합니다.  
  
 **구문**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수로 반올림합니다.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> SIGN  
 지정한 숫자 식의 양수(+1), 0(0) 또는 음수(-1) 부호를 반환합니다.  
  
 **구문**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 -2에서 2까지의 숫자의 SIGN 값을 반환합니다.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 지정된 식의 라디안에서 지정된 각도의 삼각 사인을 반환합니다.  
  
 **구문**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 각도의 SIN을 계산합니다.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 지정된 숫자 값의 제곱근을 반환합니다.  
  
 **구문**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 숫자 1-3의 제곱근을 반환합니다.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> SQUARE  
 지정한 숫자 값의 제곱을 반환합니다.  
  
 **구문**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 1에서 3까지의 숫자의 제곱을 반환합니다.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 지정된 식에서 지정된 각도(라디안 단위)의 탄젠트를 반환합니다.  
  
 **구문**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 PI()/2의 탄젠트를 계산합니다.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC  
 가장 가까운 정수 값으로 버린 숫자 값을 반환합니다.  
  
 **구문**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **인수**  
  
- `numeric_expression`  
  
   숫자 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 첨부된 양수와 음수를 가장 가까운 정수 값으로 자릅니다.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```  
  
###  <a name="bk_type_checking_functions"></a> 형식 검사 함수  
 다음 함수는 입력 값에 대한 형식 검사를 지원하며, 각 함수마다 부울 값을 반환합니다.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 지정한 식의 형식이 배열인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_ARRAY 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 지정한 식의 형식이 부울인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_BOOL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 속성이 값을 할당할지를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 JSON 문서 내에 속성이 있는지 여부를 확인합니다. "a"가 있으므로 첫 번째 함수는 true를 반환하지만, "b"가 없으므로 두 번째 함수는 false를 반환합니다.  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 지정한 식의 형식이 널인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_NULL(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_NULL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 지정한 식의 형식이 숫자인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_NULL 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 지정한 식의 형식이 JSON 개체인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_OBJECT 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 지정한 식의 형식이 기본 형식(문자열, 부울, 숫자 또는 null)인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_PRIMITIVE 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 지정한 식의 형식이 문자열인지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
IS_STRING(<expression>)  
```  
  
 **인수**  
  
- `expression`  
  
   유효한 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 IS_STRING 함수를 사용하여 JSON 부울, number, string, null, object, array 및 undefined 형식의 개체를 확인합니다.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  
  
###  <a name="bk_string_functions"></a> 문자열 함수  
 다음 스칼라 함수는 문자열 입력 값에 대해 작업을 수행하고 문자열, 숫자 또는 부울 값을 반환합니다.  
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[CONTAINS](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[왼쪽](#bk_left)|[LENGTH](#bk_length)|  
|[낮은](#bk_lower)|[LTRIM](#bk_ltrim)|[바꾸기](#bk_replace)|  
|[복제](#bk_replicate)|[역방향](#bk_reverse)|[오른쪽](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[하위 문자열](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[위](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 둘 이상의 문자열 값을 연결한 결과인 문자열을 반환합니다.  
  
 **구문**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 값의 연결된 문자열을 반환합니다.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> CONTAINS  
 첫 번째 문자열 식이 두 번째를 포함하는지를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc"에 "ab"와 "d"가 포함되어 있는지 확인합니다.  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 첫 번째 문자열 식이 두 번째 문자열 식에서 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc"가 "b"와 "bc"로 끝나는지 여부를 나타내는 부울 값을 반환합니다.  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 지정된 첫 번째 문자열 식 내의 두 번째 문자열 식에서 첫 번째로 나타나는 시작 위치를 반환하거나 문자열을 찾을 수 없는 경우 -1을 반환합니다.  
  
 **구문**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc" 안에 다양한 부분 문자열의 인덱스를 반환합니다.  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 지정된 수의 문자로 문자열의 왼쪽 부분을 반환합니다.  
  
 **구문**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 왼쪽 부분을 반환합니다.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> LENGTH  
 지정한 문자열 식의 문자 수를 반환합니다.  
  
 **구문**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 문자열의 길이를 반환합니다.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> LOWER  
 대문자 데이터를 소문자로 변환한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
LOWER(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 LOWER를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> LTRIM  
 선행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 LTRIM을 쿼리 내에서 사용하는 방법을 보여 줍니다.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> REPLACE  
 지정된 문자열 값의 모든 항목을 다른 문자열 값으로 바꿉니다.  
  
 **구문**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REPLACE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLICATE  
 문자열 값을 지정한 횟수 만큼 반복합니다.  
  
 **구문**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다. num_expr이 음수이거나 무한 값이면 결과가 정의되지 않습니다.

  > [!NOTE]
  > 결과의 최대 길이는 10,000자입니다((length(str_expr) * num_expr) <= 10,000).
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REPLICATE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 문자열 값의 순서와 반대로 반환합니다.  
  
 **구문**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 REVERSE를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> RIGHT  
 지정된 수의 문자로 문자열의 오른쪽 부분을 반환합니다.  
  
 **구문**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   유효한 숫자 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다양한 길이 값에 대해 "abc"의 오른쪽 부분을 반환합니다.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> RTRIM  
 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리 내에서 RTRIM을 사용하는 방법을 보여 줍니다.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 첫 번째 문자열 식이 두 번째 문자열 식에서 시작하는지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 "abc" 문자열이 "b"와 "a"로 시작하는지 확인합니다.  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 배열로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToArray(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 배열 식으로 계산할 유효한 스칼라 식이입니다. Note는 중첩 된 문자열 값 유효 하려면 큰따옴표를 사용 하 여 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)
  
  **반환 형식**  
  
  배열 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToArray 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

 결과 집합은 다음과 같습니다.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

 다음은 잘못 된 입력의 예입니다. 
   
 배열 내에서 작은따옴표는 유효한 JSON이 없습니다.
쿼리 내에서 유효한 지, 경우에 이러한 구문 분석 하지 않습니다 유효한 배열입니다. 문자열 배열의 문자열 내의 이스케이프 되어야 하거나 "[\\"\\"]" 주변 따옴표는 하나 여야 합니다 또는 ' [""]'.

```
SELECT
    StringToArray("['5','6','7']")
```

 결과 집합은 다음과 같습니다.

```
[{}]
```

 잘못 된 입력의 예는 다음과 같습니다.
   
 JSON 배열로 전달 되는 식 구문 분석 됩니다. 배열 형식과 따라서 undefined를 반환 하려면 다음 평가 하지 않습니다.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

 결과 집합은 다음과 같습니다.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 부울으로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   부울 식으로 계산할 유효한 스칼라 식이입니다.  
  
  **반환 형식**  
  
  부울 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToBoolean 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
 
 유효한 입력을 사용 하는 예제는 다음과 같습니다.

 이전 또는 이후에 "true"/ "false"만 공백이 허용 됩니다.

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

 잘못 된 입력의 예는 다음과 같습니다.
 
 부울 대/소문자 구분 되며 모든 소문자 예: "true" 및 "false"를 사용 하 여 작성 해야 합니다.

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

 결과 집합은 다음과 같습니다.  
  
```  
[{}]
``` 

 전달 되는 식, 부울 식으로 구문 분석 됩니다. 부울 값을 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.

 ```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

 결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Null로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToNull(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   Null 식으로 계산할 유효한 스칼라 식이입니다.
  
  **반환 형식**  
  
  정의 되지 않은 또는 null 식을 반환 합니다.  
  
  **예**  
  
  다음 예제에서는 StringToNull 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 

 유효한 입력을 사용 하는 예제는 다음과 같습니다.
 
 이전 또는 "null" 이후에는 공백이 허용 됩니다.

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

 잘못 된 입력의 예는 다음과 같습니다.

 Null은 대/소문자 구분 및 "null" 즉, 모든 소문자 문자를 사용 하 여 작성 해야 합니다.

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

 전달 되는 식, null 식으로 구문 분석 됩니다. null을 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 숫자로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToNumber(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 숫자 식으로 계산할 유효한 스칼라 식이입니다. Json에서 숫자는 정수 또는 부동 소수점 있어야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)  
  
  **반환 형식**  
  
  정의 되지 않은 또는 숫자 식을 반환 합니다.  
  
  **예**  
  
  다음 예제에서는 StringToNumber 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 

 만 전이나 후에 공백이 허용 됩니다.
 
```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

 올바른 숫자를 이어야 합니다 하는 JSON 될 정수 또는 부동 소수점 숫자입니다.
 
```  
SELECT   
    StringToNumber("0xF")
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{}}
```  

 전달 되는 식은 후행 숫자 식입니다. 이러한 입력 번호를 입력 하 고 따라서 undefined를 반환 하는 평가 하지 않습니다. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 개체로 변환 하는 식을 반환 합니다. 식을 변환할 수 없는 경우 undefined를 반환 합니다.  
  
 **구문**  
  
```  
StringToObject(<expr>)  
```  
  
 **인수**  
  
- `expr`  
  
   JSON 개체 식으로 계산할 유효한 스칼라 식이입니다. Note는 중첩 된 문자열 값 유효 하려면 큰따옴표를 사용 하 여 작성 해야 합니다. JSON 형식에 대 한 자세한 내용은 참조 하세요. [json.org](https://json.org/)  
  
  **반환 형식**  
  
  개체 식을 반환 하거나 정의 되지 않았습니다.  
  
  **예**  
  
  다음 예제에서는 StringToObject 서로 다른 형식에서 작동 하는 방법을 보여 줍니다. 
  
 유효한 입력을 사용 하는 예제는 다음과 같습니다.
 
``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

 결과 집합은 다음과 같습니다.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```
 
 잘못 된 입력의 예는 다음과 같습니다.
쿼리 내에서 유효한 지, 경우에 이러한 구문 분석 하지 않습니다 유효한 개체입니다. 문자열 개체의 문자열 내의 이스케이프 되어야 하거나 "{\\"는\\":\\" str\\"}" 주변 따옴표는 하나 여야 합니다 또는 ' {"a": "str"}'.

 속성 이름을 둘러싸는 작은따옴표는 유효한 JSON이 없습니다.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

 결과 집합은 다음과 같습니다.

```  
[{}]
```  

 주변 따옴표 없이 속성 이름은 유효한 JSON 하지 않습니다.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

 결과 집합은 다음과 같습니다.

```  
[{}]
``` 

 잘못 된 입력의 예는 다음과 같습니다.
 
 JSON 개체로 전달 되는 식 구문 분석 됩니다. 개체를 입력 하 고 따라서 undefined를 반환 하는 이러한 입력 평가 하지 않습니다.
 
``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 결과 집합은 다음과 같습니다.

```
[{}]
```

####  <a name="bk_substring"></a> SUBSTRING  
 지정한 문자 0 기준 위치에서 시작하여 지정한 길이 또는 문자열의 끝까지에 이르는 문자열 식의 일부를 반환합니다.  
  
 **구문**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
- `num_expr`  
  
   시작 및 끝 문자를 나타내는 유효한 숫자 식입니다.    
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 1에서 시작하여 길이가 1인 "abc"의 부분 문자열을 반환합니다.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 스칼라 식의 문자열 표현을 반환합니다. 
  
 **구문**  
  
```  
ToString(<expr>)
```  
  
 **인수**  
  
- `expr`  
  
   유효한 스칼라 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 다른 형식에서 ToString이 동작하는 방법을 보여줍니다.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 다음 입력을 지정합니다.
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 다음 예제에서는 CONCAT와 같은 다른 문자열 함수를 사용하여 ToString을 사용할 수 있는 방법을 보여줍니다.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

 결과 집합은 다음과 같습니다.  
  
```  
[{"$1":"4lb" },
 {"$1":"32kg"},
 {"$1":"400g" },
 {"$1":"8999mg" }]

```  
다음 입력을 지정합니다.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
 다음 예제에서는 REPLACE와 같은 다른 문자열 함수를 사용하여 ToString을 사용할 수 있는 방법을 보여줍니다.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
 결과 집합은 다음과 같습니다.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
 ``` 
 
####  <a name="bk_trim"></a> TRIM  
 선행 및 후행 공백을 제거한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
TRIM(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리 내에서 TRIM을 사용하는 방법을 보여 줍니다.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> UPPER  
 소문자 데이터를 대문자로 변환한 후에 문자열 식을 반환합니다.  
  
 **구문**  
  
```  
UPPER(<str_expr>)  
```  
  
 **인수**  
  
- `str_expr`  
  
   유효한 문자열 식입니다.  
  
  **반환 형식**  
  
  문자열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 쿼리에서 UPPER를 사용하는 방법을 보여 줍니다.  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"upper": "ABC"}]  
```  
  
###  <a name="bk_array_functions"></a> 배열 함수  
 다음 스칼라 함수는 배열 입력 값에 대한 연산을 수행하고, 숫자, 부울, 또는 배열 값을 반환합니다.  
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 둘 이상의 배열 값을 연결한 결과인 배열을 반환합니다.  
  
 **구문**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
  **반환 형식**  
  
  배열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 두 배열을 연결하는 방법을 보여 줍니다.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
지정된 값이 배열에 포함되는지를 나타내는 부울 값을 반환합니다. 명령 내에서 부울 식을 사용하여 개체의 부분 또는 전체 일치를 확인할 수 있습니다. 

 **구문**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
- `expr`  
  
   유효한 식입니다.  

- `bool_expr`  
  
   부울 식입니다. 'true'로 설정되고 지정된 검색 값이 개체인 경우 명령은 부분 일치(검색 개체가 개체 중 하나의 하위 집합임)를 확인합니다. 'false'로 설정되면 명령은 배열 내의 모든 개체에 대해 전체 일치를 확인합니다. 지정하지 않으면 기본값 false입니다. 
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_CONTAINS를 사용하여 배열의 멤버 자격을 확인하는 방법을 보여 줍니다.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"b1": true, "b2": false}]  
```  

 다음 예제에서는 ARRAY_CONTAINS를 사용하여 JSON의 부분 일치를 확인하는 방법을 보여 줍니다.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 지정된 배열 식의 요소 수를 반환합니다.  
  
 **구문**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
  **반환 형식**  
  
  숫자 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_LENGTH를 사용하여 배열의 길이를 가져오는 방법을 보여 줍니다.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 배열 식의 일부를 반환합니다.
  
 **구문**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **인수**  
  
- `arr_expr`  
  
   유효한 배열 식입니다.  
  
- `num_expr`  
  
   배열을 시작할 0 기반 숫자 인덱스 배열의 마지막 요소에 관련된 시작 인덱스를 지정하는 데 음수 값을 사용할 수 있습니다. 즉, -1은 배열의 마지막 요소를 참조합니다.  

- `num_expr`  

   결과 배열에 있는 요소의 최대 수입니다.    

  **반환 형식**  
  
  배열 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ARRAY_SLICE를 사용하여 배열의 다른 조각을 가져오는 방법을 보여줍니다.  
  
```  
SELECT   
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
 
###  <a name="bk_spatial_functions"></a> 공간 함수  
 다음 스칼라 함수는 공간 개체 입력 값에 대한 연산을 수행하고, 숫자 또는 부울 값을 반환합니다.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 두 GeoJSON Point, Polygon 또는 LineString 식 사이의 거리를 반환합니다.  
  
 **구문**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  거리가 포함된 숫자 식을 반환합니다. 기본 참조 시스템의 경우 미터 단위로 표현됩니다.  
  
  **예**  
  
  다음 예제에서는 ST_DISTANCE 기본 제공 함수를 사용하여 지정된 위치에서 30Km 이내에 있는 모든 패밀리 문서를 반환하는 방법을 보여 줍니다. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon 또는 LineString) 내에 있는지 여부를 나타내는 부울 식을 반환합니다.  
  
 **구문**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
 
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_WITHIN을 사용하여 다각형 내의 모든 패밀리 문서를 찾는 방법을 보여 줍니다.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 첫 번째 인수에 지정된 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 인수의 GeoJSON(Point, Polygon 또는 LineString)과 교차하는지 여부를 나타내는 부울 식을 반환합니다.  
  
 **구문**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
 
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 개체 식입니다.  
  
  **반환 형식**  
  
  부울 값을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 지정된 다각형과 교차하는 모든 영역을 찾는 방법을 보여줍니다.  
  
```  
SELECT a.id   
FROM Areas a   
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한지 여부를 나타내는 부울 값을 반환합니다.  
  
 **구문**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON Point, Polygon 또는 LineString 식입니다.  
  
  **반환 형식**  
  
  부울 식을 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_VALID를 사용하여 꼭짓점이 유효한지 확인하는 방법을 보여 줍니다.  
  
  예를 들어 유효 범위[-90, 90]에 없는 위도 값이 이 꼭짓점에 있으므로 쿼리에서 false를 반환합니다.  
  
  다각형의 경우 GeoJSON 사양에서는 닫힌 도형을 만들기 위해 제공된 마지막 좌표 쌍이 첫 번째 것과 같아야 합니다. 다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
 **구문**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **인수**  
  
- `spatial_expr`  
  
   유효한 GeoJSON 꼭짓점 또는 다각형 식입니다.  
  
  **반환 형식**  
  
  지정된 GeoJSON 점 또는 다각형 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.  
  
  **예**  
  
  다음 예제에서는 ST_ISVALIDDETAILED를 사용하여 세부 정보로 유효성을 검사하는 방법을 보여 줍니다.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b  
```  
  
 결과 집합은 다음과 같습니다.  
  
```  
[{  
  "b": {   
    "valid": false,   
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  
  
## <a name="next-steps"></a>다음 단계  

- [Cosmos DB에 대한 SQL 구문 및 SQL 쿼리](how-to-sql-query.md)

- [Cosmos DB 설명서](https://docs.microsoft.com/azure/cosmos-db/)  
