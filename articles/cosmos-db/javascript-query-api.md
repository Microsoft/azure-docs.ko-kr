---
title: Azure Cosmos DB에서 JavaScript LINQ(Language-Integrated Query) API 작업
description: 이 문서에서는 Azure Cosmos DB에서 저장 프로시저 및 트리거를 만들기 위한 JavaScript LINQ(Language-Integrated Query) API의 개념을 소개합니다.
author: rockboyfor
ms.service: cosmos-db
ms.topic: conceptual
origin.date: 12/08/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.reviewer: sngun
ms.openlocfilehash: 101b5382eaa01ed87f05d83c82002fa1b93144b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043239"
---
# <a name="javascript-query-api-in-azure-cosmos-db"></a>Azure Cosmos DB의 JavaScript 쿼리 API

Azure Cosmos DB의 SQL API를 사용하여 쿼리를 발급하는 방식과 더불어 [Cosmos DB 서버 쪽 SDK](https://azure.github.io/azure-cosmosdb-js-server/)를 사용하면 JavaScript 인터페이스를 통해 최적화된 쿼리를 수행할 수 있습니다. 이 JavaScript 인터페이스를 사용하기 위해 SQL 언어를 알 필요는 없습니다. JavaScript 쿼리 API를 사용하면 조건자 함수를 ECMAScript5의 배열 기본 제공 항목과 익숙한 구문 및 Lodash와 같은 인기 있는 JavaScript 라이브러리가 포함된 함수 호출 시퀀스에 전달하여 쿼리를 프로그래밍 방식으로 작성할 수 있습니다. 쿼리는 JavaScript 런타임으로 구문 분석되고 Azure Cosmos DB 인덱스를 사용하여 효율적으로 실행됩니다.

## <a name="supported-javascript-functions"></a>지원되는 JavaScript 함수

| **Function** | **설명** |
|---------|---------|
|`chain() ... .value([callback] [, options])`|value()로 종료되어야 하는 연결된 호출을 시작합니다.|
|`filter(predicateFunction [, options] [, callback])`|출력 문서를 결과 집합으로 필터링하기 위해 true/false를 반환하는 조건자 함수를 사용하여 입력을 필터링합니다. 이 기능은 SQL의 WHERE 절과 유사하게 작동합니다.|
|`flatten([isShallow] [, options] [, callback])`|각 입력 항목의 배열을 단일 배열로 결합하고 평면화합니다. 이 기능은 LINQ의 SelectMany와 유사하게 작동합니다.|
|`map(transformationFunction [, options] [, callback])`|각 입력 항목을 JavaScript 개체 또는 값에 매핑하는 변환 함수에 대해 프로젝션을 적용합니다. 이 기능은 SQL의 SELECT 절과 유사하게 작동합니다.|
|`pluck([propertyName] [, options] [, callback])`|이 기능은 각 입력 항목에서 단일 속성의 값을 추출하는 맵에 대한 바로 가기입니다.|
|`sortBy([predicate] [, options] [, callback])`|주어진 조건자를 사용하여 입력 문서 스트림의 문서를 오름차순으로 정렬하여 새 문서 집합을 생성합니다. 이 기능은 SQL의 ORDER BY 절과 유사하게 작동합니다.|
|`sortByDescending([predicate] [, options] [, callback])`|주어진 조건자를 사용하여 입력 문서 스트림의 문서를 내림차순으로 정렬하여 새 문서 집합을 생성합니다. 이 기능은 SQL의 ORDER BY x DESC 절과 유사하게 작동합니다.|
|`unwind(collectionSelector, [resultSelector], [options], [callback])`|내부 배열에서 셀프 조인을 수행하고 양쪽의 결과를 결과 프로젝션에 튜플로 추가합니다. 예를 들어, person 문서를 person.pets에 조인하면 [person, pet] 튜플이 생성됩니다. 이것은 .NET LINK의 SelectMany와 유사합니다.|

조건자 및/또는 선택기 함수 안에 포함된 경우 다음과 같은 JavaScript 구문이 Azure Cosmos DB 인덱스에서 직접 실행하도록 자동으로 최적화됩니다.

- 단순 연산자: `=` `+` `-` `*` `/` `%` `|` `^` `&` `==` `!=` `===` `!===` `<` `>` `<=` `>=` `||` `&&` `<<` `>>` `>>>!` `~`
- 개체 리터럴을 포함하는 리터럴: {}
- var, 반환

다음 JavaScript 구문은 Azure Cosmos DB 인덱스에 대해 최적화되지 않습니다.

- 제어 흐름(예: if, for, while)
- 함수 호출

자세한 내용은 [Cosmos DB 서버 쪽 JavaScript 설명서](https://azure.github.io/azure-cosmosdb-js-server/)를 참조하세요.

## <a name="sql-to-javascript-cheat-sheet"></a>SQL-JavaScript 치트 시트

다음 표에서 다양한 SQL 쿼리 및 해당 JavaScript 쿼리를 표시합니다. SQL 쿼리와 마찬가지로 속성(예: item.id)은 대/소문자를 구분합니다.

> [!NOTE]
> JavaScript 쿼리 API를 사용하는 경우 `__`(이중 밑줄)은 `getContext().getCollection()`에 대한 별칭입니다.

|**SQL**|**JavaScript Query API**|**설명**|
|---|---|---|
|SELECT *<br>FROM docs| __.map(function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;return doc;<br>});|모든 문서(연속 토큰과 함께 페이지가 매겨진)의 결과는 있는 그대로입니다.|
|SELECT <br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg,<br>&nbsp;&nbsp;&nbsp;docs.actions <br>FROM docs|__.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;actions:doc.actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|모든 문서에서 id, message(msg로 별칭이 지정됨) 및 action을 프로젝션합니다.|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>});|조건자: id = "X998\_Y998"을 사용하여 문서를 쿼리합니다.|
|SELECT *<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;ARRAY_CONTAINS(docs.Tags, 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|Tags 속성이 있는 문서를 쿼리합니다. Tags는 123 값을 포함하는 배열입니다.|
|SELECT<br>&nbsp;&nbsp;&nbsp;docs.id,<br>&nbsp;&nbsp;&nbsp;docs.message AS msg<br>FROM docs<br>WHERE<br>&nbsp;&nbsp;&nbsp;docs.id="X998_Y998"|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.id ==="X998_Y998";<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.map(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;id: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|조건자 id = "X998_Y998"을 사용하여 문서를 쿼리한 다음 id와 message(msg로 별칭이 지정됨)를 프로젝션합니다.|
|SELECT VALUE tag<br>FROM docs<br>JOIN tag IN docs.Tags<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc.Tags && Array.isArray(doc.Tags);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|배열 속성 Tags가 있는 문서를 필터링하고 _ts 타임스탬프 시스템 속성으로 결과 문서를 정렬한 다음, Tags 배열을 프로젝션 및 평면화합니다.|

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 작성하고 사용하는 개념 및 방법을 알아봅니다.

- [JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거를 작성하는 방법](how-to-write-javascript-query-api.md)
- [Azure Cosmos DB 저장 프로시저, 트리거 및 사용자 정의 함수 작업](stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수를 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md)
- [Azure Cosmos DB JavaScript 서버 쪽 API 참조](https://azure.github.io/azure-cosmosdb-js-server)
- [JavaScript ES6(ECMA 2015)](https://www.ecma-international.org/ecma-262/6.0/)

<!-- Update_Description: update meta properties -->