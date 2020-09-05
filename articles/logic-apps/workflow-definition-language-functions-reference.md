---
title: 식의 함수에 대한 참조 가이드
description: Azure Logic Apps 및 Power Automate용 식의 함수에 대한 참조 가이드
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: e4f9fa554a7c0e45abe1e9686605c95bb79d1739
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88932953"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Azure Logic Apps 및 Power Automate용 식의 함수 사용에 대한 참조 가이드

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Power Automate](/flow/getting-started)의 워크플로 정의에서, 일부 [식](../logic-apps/logic-apps-workflow-definition-language.md#expressions)은 워크플로가 실행되기 시작할 때 존재하지 않을 수도 있는 런타임 작업에서 값을 가져옵니다. 식에서 이러한 값을 참조하거나 처리하려면 [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에서 제공하는 *함수*를 사용하면 됩니다.

> [!NOTE]
> 이 참조 페이지는 Azure Logic Apps 및 Power Automate에 모두 적용되지만, Azure Logic Apps 설명서에 표시됩니다. 이 페이지는 구체적으로 논리 앱을 참조하지만, 이러한 함수는 흐름과 논리 앱 모두에서 작동합니다. Power Automate의 함수 및 식에 대한 자세한 내용은 [조건에 식 사용](/flow/use-expressions-in-conditions)을 참조하세요.

예를 들어 정수 또는 부동 소수점의 합계를 원하는 경우 [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) 함수 같은 수식 함수를 사용하여 값을 계산할 수 있습니다. 다음은 함수로 수행할 수 있는 다른 작업의 예입니다.

| Task | 함수 구문 | 결과 |
| ---- | --------------- | ------ |
| 문자열을 소문자 형식으로 반환합니다. | toLower('<*text*>') <p>예를 들어: toLower('Hello') | "hello" |
| 전역적으로 고유한 식별자(GUID)를 반환합니다. | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

[일반 용도를 기반으로](#ordered-by-purpose) 함수를 찾으려면 다음 표를 참조하세요. 각 함수에 대한 자세한 내용은 [사전순 목록](#alphabetical-list)을 참조하세요.

## <a name="functions-in-expressions"></a>식의 함수

식에서 함수를 사용하는 방법을 보여주기 위해, 이 예제는 식에서 [parameters()](#parameters) 함수를 사용하여 `customerName` 매개 변수에서 값을 가져와 해당 값을 `accountName` 속성에 할당할 수 있는 방법을 보여줍니다.

```json
"accountName": "@parameters('customerName')"
```

식에서 함수를 사용할 수 있는 몇 가지 다른 일반적인 방법은 다음과 같습니다.

| Task | 식에서 함수 구문 |
| ---- | -------------------------------- |
| 함수에 해당 항목을 전달하여 항목으로 작업을 수행합니다. | "\@<*functionName*>(<*item*>)" |
| 1. 중첩된 `parameters()` 함수를 사용하여 *parameterName* 값을 가져옵니다. </br>2. 해당 값을 *functionName*에 전달하여 결과로 작업을 수행합니다. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. 중첩된 내부 함수 *functionName*에서 결과를 가져옵니다. </br>2. 외부 함수 *functionName2*에 결과를 전달합니다. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. *functionName*에서 결과를 가져옵니다. </br>2. 결과가 속성 *propertyName*이 있는 개체인 경우 해당 속성의 값을 가져옵니다. | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

예를 들어 `concat()` 함수는 두 개 이상의 문자열 값을 매개 변수로 사용할 수 있습니다. 이 함수는 이러한 문자열을 하나의 문자열로 결합합니다. 문자열 리터럴, 예를 들어 "Sophia" 및 "Owen"을 전달하여 결합된 문자열 "SophiaOwen"을 가져올 수 있습니다.

```json
"customerName": "@concat('Sophia', 'Owen')"
```

또는 매개 변수에서 문자열 값을 가져올 수 있습니다. 이 예제는 각 `concat()` 매개 변수 및 `firstName`, `lastName` 매개 변수에서 `parameters()` 함수를 사용합니다. 결과 문자열을 `concat()` 함수에 전달하면 결합된 문자열, 예를 들어 "SophiaOwen"을 가져올 수 있습니다.

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

두 예제 중 어느 경우든 결과를 `customerName` 속성에 할당합니다.

다음은 식의 함수에 대한 기타 참고 사항입니다.

* 함수 매개 변수는 왼쪽에서 오른쪽으로 계산됩니다.

* 매개 변수 정의 구문에서 매개 변수 뒤에 나오는 물음표(?)는 해당 매개 변수가 선택 사항임을 의미합니다. 예를 들어 [getFutureTime()](#getFutureTime)을 참조하세요.

다음 섹션에는 일반 용도를 기준으로 함수가 정렬되어 있으며, 이러한 함수를 [사전순](#alphabetical-list)으로 찾아볼 수도 있습니다.

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>문자열 함수

문자열로 작업하려면 이러한 문자열 함수 및 일부 [컬렉션 함수](#collection-functions)도 사용할 수 있습니다. 문자열 함수는 문자열에서만 작동합니다.

| 문자열 함수 | Task |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | 둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | 문자열이 지정된 하위 문자열로 끝나는지 여부를 확인합니다. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | 지정된 형식에 따라 숫자를 문자열로 반환합니다. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | 전역적으로 고유한 식별자(GUID)를 문자열로 생성합니다. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | 하위 문자열에 대한 시작 위치를 반환합니다. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | 마지막으로 나오는 부분 문자열의 시작 위치를 반환합니다. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | 하위 문자열을 지정된 문자열로 바꾸고 업데이트된 문자열을 반환합니다. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | 원래 문자열에 지정된 구분 기호 문자를 기준으로 하여 더 큰 문자열의 하위 문자열을 포함하는 배열(쉼표로 구분됨)을 반환합니다. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | 문자열이 특정 하위 문자열로 시작하는지 검사합니다. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | 지정된 위치에서 시작하여 문자열에서 문자를 반환합니다. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | 문자열을 소문자 형식으로 반환합니다. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | 문자열을 대문자 형식으로 반환합니다. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | 문자열에서 선행 및 후행 공백을 제거하고 업데이트된 문자열을 반환합니다. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>컬렉션 함수

컬렉션, 일반적으로 배열, 문자열 및 경우에 따라 사전으로 작업하려면 이러한 컬렉션 함수를 사용할 수 있습니다.

| 컬렉션 함수 | Task |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | 컬렉션에 특정 항목이 있는지 검사합니다. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | 컬렉션이 비어 있는지 검사합니다. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | 컬렉션에서 첫 번째 항목을 반환합니다. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | 지정한 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 배열에 대해 반복 작업 내에 있을 경우 현재 작업 반복하는 동안 배열에서 현재 항목을 반환합니다. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | 지정된 문자로 구분되는 배열에서 *모든* 항목이 들어 있는 문자열을 반환합니다. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | 컬렉션에서 마지막 항목을 반환합니다. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | 문자열 또는 배열에서 항목 수를 반환합니다. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | 컬렉션 앞의 항목을 제거하고 *다른 모든* 항목을 반환합니다. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | 컬렉션 앞에서 항목을 반환합니다. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | 지정한 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>논리 비교 함수

조건을 사용하고 값 및 식 결과를 비교하거나 다양한 종류의 논리를 평가하려면 이러한 논리 비교 함수를 사용하면 됩니다. 각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

> [!NOTE]
> 논리 함수 또는 조건을 사용 하 여 값을 비교 하는 경우 null 값은 빈 문자열 ( `""` ) 값으로 변환 됩니다. 조건의 동작은 null 값 대신 빈 문자열과 비교할 때 다릅니다. 자세한 내용은 [string () 함수](#string)를 참조 하세요. 

| 논리 비교 함수 | Task |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | 모든 식이 true인지 확인합니다. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | 두 값이 동일한지 확인합니다. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | 첫 번째 값이 두 번째 값보다 큰지 검사합니다. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | 식이 true인지 또는 false인지 검사합니다. 결과에 기반해 지정한 값을 반환합니다. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | 첫 번째 값이 두 번째 값보다 작은지 검사합니다. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | 식이 false인지 검사합니다. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | 최소 하나의 식이 true인지 검사합니다. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>변환 함수

값의 유형 또는 형식을 변경하려면 이러한 변환 함수를 사용할 수 있습니다. 예를 들어 부울에서 정수로 값을 변경할 수 있습니다. 변환하는 동안 Logic Apps가 콘텐츠 형식을 처리하는 방법에 대한 자세한 내용은 [콘텐츠 형식 처리](../logic-apps/logic-apps-content-type.md)를 참조하세요. 각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

> [!NOTE]
> Azure Logic Apps는 일부 데이터 형식 간에 값을 자동으로 변환하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

| 변환 함수 | Task |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | 단일 지정 입력에서 배열을 반환합니다. 여러 입력의 경우 [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray)를 참조합니다. |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | 문자열에 대한 base64로 인코딩된 버전을 반환합니다. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | 입력 값에 대한 이진 버전을 반환합니다. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | 입력 값에 대한 부울 버전을 반환합니다. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | 여러 입력에서 배열을 반환합니다. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | 입력 값에 대한 데이터 URI를 반환합니다. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | 데이터 URI에 대한 문자열 버전을 반환합니다. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Base64로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | 데이터 URI에 대한 이진 버전을 반환합니다. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | 디코딩된 버전으로 이스케이프 문자를 바꾸는 문자열을 반환합니다. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | URL 안전하지 않은 문자를 이스케이프 문자로 대체하는 문자열을 반환합니다. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | 입력 값에 대해 부동 소수점 숫자를 반환합니다. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | 문자열에 대한 정수 버전을 반환합니다. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | 입력 값에 대한 문자열 버전을 반환합니다. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | 이스케이프 문자로 URL 안전하지 않은 문자를 대체하여 입력 값에 대한 URI로 인코딩된 버전을 반환합니다. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | URI로 인코딩된 문자열에 대한 이진 버전을 반환합니다. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | URI로 인코딩된 문자열에 대한 문자열 버전을 반환합니다. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | 문자열에 대한 XML 버전을 반환합니다. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>암시적 데이터 형식 변환

Azure Logic Apps는 일부 데이터 형식 간에 자동으로 또는 암시적으로 변환되므로 사용자가 이러한 형식을 수동으로 변환할 필요가 없습니다. 예를 들어 입력으로 문자열이 필요한 비-문자열 값을 사용하는 경우 Logic Apps는 비-문자열 값을 문자열로 자동 변환합니다.

트리거가 숫자 값을 출력으로 반환한다고 가정해 봅시다.

`triggerBody()?['123']`

URL처럼 문자열 입력이 필요한 이 숫자 출력을 사용하는 경우 Logic Apps는 중괄호(`{}`) 표기법을 사용하여 값을 문자열로 자동 변환합니다.

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Base64 인코딩 및 디코딩

Logic Apps는 자동으로 또는 암시적으로 base64 인코딩 또는 디코딩을 수행하므로 사용자가 해당하는 식을 사용하여 이러한 작업을 수동으로 수행할 필요가 없습니다.

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> 예를 들어 사용자가 식 편집기를 사용하여 논리 앱에 이러한 식을 수동으로 추가하는 경우 논리 앱 디자이너에서 나와 디자이너로 돌아가면 디자이너에 매개 변수 값만 표시됩니다. 매개 변수 값을 편집하지 않는 경우에만 코드 보기에 식이 유지됩니다. 편집할 경우 Logic Apps가 코드 보기에서 식을 제거하고 매개 변수 값만 남겨 둡니다. 이 동작은 인코딩 또는 디코딩에는 영향을 주지 않고 식의 표시 여부에만 영향을 줍니다.

<a name="math-functions"></a>

## <a name="math-functions"></a>수치 연산 함수

정수 및 부동 소수점을 사용하려면 이러한 수식 함수를 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

| 수식 함수 | Task |
| ------------- | ---- |
| [추가](../logic-apps/workflow-definition-language-functions-reference.md#add) | 두 숫자를 더한 결과를 반환합니다. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | 두 숫자를 나눈 결과를 반환합니다. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | 숫자 또는 배열 집합에서 가장 높은 값을 반환합니다. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | 숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | 두 숫자를 나눈 나머지를 반환합니다. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | 두 숫자를 곱한 결과를 반환합니다. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | 지정된 범위에서 임의의 정수를 반환합니다. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | 지정한 정수에서 시작하는 정수 배열을 반환합니다. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>날짜 및 시간 함수

날짜 및 시간을 사용하려면 이러한 날짜 및 시간 함수를 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

| 날짜 또는 시간 함수 | Task |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | 타임스탬프에 일 수를 더합니다. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | 타임스탬프에 시간 수를 더합니다. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | 타임스탬프에 분 수를 더합니다. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | 타임스탬프에 초 수를 더합니다. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | 타임스탬프에 시간 단위 수를 더합니다. [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime)도 참조합니다. |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | UTC(Universal Time Coordinated)의 타임스탬프를 대상 표준 시간대로 변환합니다. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | 원본 표준 시간대의 타임스탬프를 대상 표준 시간대로 변환합니다. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | 원본 표준 시간대의 타임스탬프를 UTC(Universal Time Coordinated)로 변환합니다. |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | 타임 스탬프에서 월 구성 요소인 날짜를 반환합니다. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | 타임 스탬프에서 주 구성 요소인 날짜를 반환합니다. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | 타임 스탬프에서 연 구성 요소인 날짜를 반환합니다. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | 타임 스탬프에서 날짜를 반환합니다. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | 지정한 시간 단위를 더한 현재 타임스탬프를 반환합니다. [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime)도 참조합니다. |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | 지정한 시간 단위를 뺀 현재 타임스탬프를 반환합니다. [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime)도 참조합니다. |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | 타임스탬프에 대한 날의 시작을 반환합니다. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | 타임스탬프에 대한 시간의 시작을 반환합니다. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | 타임스탬프에 대한 월의 시작을 반환합니다. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | 타임스탬프에서 시간 단위 수를 뺍니다. [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime)도 함께 참조하세요. |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | 지정한 타임스탬프에 대한 `ticks` 속성 값을 반환합니다. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | 현재 타임스탬프를 문자열로 반환합니다. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>워크플로 함수

이러한 워크플로 함수는 다음을 도울 수 있습니다.

* 실행 시간에 워크플로 인스턴스에 대한 세부 정보를 가져옵니다.
* 논리 앱 또는 흐름을 인스턴스화하는 데 사용된 입력을 사용합니다.
* 트리거 및 작업에서 출력을 참조합니다.

예를 들어 한 동작에서 출력을 참조하고 이후 작업에서 해당 데이터를 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

| 워크플로 함수 | Task |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | 런타임 시 현재 작업의 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions)도 참조합니다. |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | 런타임 시 작업의 `body` 출력을 반환합니다. [body](../logic-apps/workflow-definition-language-functions-reference.md#body)도 참조합니다. |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | 런타임 시 작업의 출력을 반환합니다. [출력](../logic-apps/workflow-definition-language-functions-reference.md#outputs) 및 [작업](../logic-apps/workflow-definition-language-functions-reference.md#actions)을 참조하세요. |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | 런타임 시 작업의 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [action](../logic-apps/workflow-definition-language-functions-reference.md#action)도 참조합니다.  |
| [body](#body) | 런타임 시 작업의 `body` 출력을 반환합니다. [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody)도 참조합니다. |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | *form-data* 또는 *form-encoded* 작업 출력에서 키 이름이 일치하는 값으로 배열을 만듭니다. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | 작업의 *form-data* 또는 *form-encoded*에서 키 이름이 일치하는 단일 값을 반환합니다. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | 배열에 대해 반복 작업 내에 있을 경우 현재 작업 반복하는 동안 배열에서 현재 항목을 반환합니다. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Foreach 또는 Until 내에 있을 때, 지정된 루프의 현재 항목을 반환합니다.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Until 루프 내에 있을 때, 현재 반복의 인덱스 값을 반환합니다. 중첩된 Until 루프 내에서 이 함수를 사용할 수 있습니다. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | 트리거 또는 작업을 호출하는 "콜백 URL"을 반환합니다. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | 여러 부분으로 구성된 작업의 출력에서 특정 부분에 대한 본문을 반환합니다. |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | 런타임 시 작업의 출력을 반환합니다. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | 흐름 정의에 설명된 매개 변수의 값을 반환합니다. |
| [result](../logic-apps/workflow-definition-language-functions-reference.md#result) | `For_each`, `Until` 및 `Scope`처럼 지정된 범위 내에 있는 모든 작업의 입력과 출력을 반환합니다. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | 런타임 시 또는 다른 JSON 이름-값 쌍에서 트리거 출력을 반환합니다. [triggerOutputs](#triggerOutputs) 및 [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody)도 참조합니다. |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | 런타임 시 트리거의 `body` 출력을 반환합니다. [트리거](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 참조합니다. |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | *form-data* 또는 *form-encoded* 트리거 출력에서 키 이름과 일치하는 단일 값을 반환합니다. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Trigger의 다중 부분 출력에서 특정 부분에 대한 본문을 반환합니다. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | *form-data* 또는 *form-encoded* 트리거 출력에서 키 이름이 일치하는 값의 배열을 만듭니다. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | 런타임 시 트리거 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다. [트리거](../logic-apps/workflow-definition-language-functions-reference.md#trigger)를 참조합니다. |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | 지정한 변수에 대한 값을 반환합니다. |
| [워크플로](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | 런타임 동안 워크플로 자체에 대한 모든 세부 정보를 반환합니다. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>URI 구문 분석 기능

URI(uniform resource identifier)를 사용하고 이러한 URI에 대한 다양한 속성 값을 가져오려면 URI 구문 분석 기능을 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

| URI 구문 분석 기능 | Task |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | URI(Uniform Resource Identifier)에 대한 `host` 값을 반환합니다. |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | URI(Uniform Resource Identifier)에 대한 `path` 값을 반환합니다. |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | URI(Uniform Resource Identifier)에 대한 `path` 및 `query` 값을 반환합니다. |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | URI(Uniform Resource Identifier)에 대한 `port` 값을 반환합니다. |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | URI(Uniform Resource Identifier)에 대한 `query` 값을 반환합니다. |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | URI(Uniform Resource Identifier)에 대한 `scheme` 값을 반환합니다. |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>조작 함수: JSON 및 XML

JSON 개체와 XML 노드를 사용하려면 이러한 조작 함수를 사용할 수 있습니다.
각 함수에 대한 전체 참조는 [사전순 목록](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list)을 참조하세요.

| 조작 함수 | Task |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | JSON 개체에 속성 및 해당 값 또는 이름-값 쌍을 추가하고 업데이트된 개체를 반환합니다. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | 하나 이상의 매개 변수에서 Null이 아닌 첫 번째 값을 반환합니다. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | JSON 개체에서 속성을 제거하고 업데이트된 개체를 반환합니다. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | JSON 개체의 속성에 대한 값을 설정하고 업데이트된 개체를 반환합니다. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>모든 함수 - 사전순 목록

이 섹션에서는 사용 가능한 모든 함수가 알파벳 순서로 나열됩니다.

<a name="action"></a>

### <a name="action"></a>action

런타임 시 *현재* 작업의 출력 또는 식에 할당할 수 있는 다른 JSON 이름과 값 쌍을 반환합니다.
기본적으로 이 함수는 전체 작업 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다.
[actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions)도 함께 참조하세요.

이 자리에 `action()` 함수만 사용할 수 있습니다.

* 원래 `subscribe` 요청의 결과에 액세스할 수 있도록 하는 웹후크 작업에 대한 `unsubscribe` 속성
* 작업에 대한 `trackedProperties` 속성
* 작업에 대한 `do-until` 루프 조건

```
action()
action().outputs.body.<property>
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*property*> | 예 | String | 해당 값을 원하는 작업 개체의 속성에 대한 이름: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** 및 **clientTrackingId**. Azure Portal에서는 특정 실행 기록을 검토하여 이러한 속성을 찾을 수 있습니다. 자세한 내용은 [REST API - 워크플로 실행 작업](/rest/api/logic/workflowrunactions/get)을 참조하세요. |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*action-output*> | String | 현재 작업 또는 속성의 출력 |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

런타임 시 작업의 `body` 출력을 반환합니다.
`actions('<actionName>').outputs.body`를 줄인 표현입니다.
[body()](#body) 및 [actions()](#actions)를 참조하세요.

```
actionBody('<actionName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 원하는 작업의 `body` 출력에 대한 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | 지정한 작업의 `body` 출력 |
||||

*예제*

이 예제는 Twitter 작업 `Get user`에서 `body` 출력을 가져옵니다.

```
actionBody('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

런타임 시 작업의 출력을 반환합니다.  `actions('<actionName>').outputs`를 줄인 표현입니다. [actions()](#actions)를 참조하세요. `actionOutputs()` 함수는 논리 앱 디자이너에서 `outputs()`으로 확인되므로 `actionOutputs()` 대신 [outputs()](#outputs)를 사용하는 것이 좋습니다. 두 함수 모두 같은 방법으로 작동하지만 `outputs()`를 사용하는 것이 좋습니다.

```
actionOutputs('<actionName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 원하는 작업의 출력에 대한 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*output*> | String | 지정한 작업의 출력 |
||||

*예제*

이 예제는 Twitter 작업 `Get user`에서 출력을 가져옵니다.

```
actionOutputs('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>actions

런타임 시 작업의 출력 또는 식에 할당할 수 있는 다른 JSON 이름-값 쌍을 반환합니다. 기본적으로 이 함수는 전체 작업 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다.
줄인 표현에 대해서는 [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) 및 [body()](#body)를 참조하세요.
현재 작업에 대해서는 [action()](#action)을 참조하세요.

> [!TIP]
> `actions()`함수는 출력을 문자열로 반환 합니다. 반환 된 값을 JSON 개체로 사용 해야 하는 경우 먼저 문자열 값을 변환 해야 합니다. [Json 구문 분석 작업](logic-apps-perform-data-operations.md#parse-json-action)을 사용 하 여 문자열 값을 json 개체로 변환할 수 있습니다.

> [!NOTE]
> 이전에는 한 작업이 다른 작업의 출력을 기반으로 실행했음을 지정할 때 `actions()` 함수 또는 `conditions` 요소를 사용할 수 있었습니다. 그러나 작업 간의 종속성을 명시적으로 선언하려면 이제 종속 작업의 `runAfter` 속성을 사용해야 합니다.
> `runAfter` 속성에 대해 자세히 알아보려면 [runAfter 속성으로 실패 파악 및 처리](../logic-apps/logic-apps-workflow-definition-language.md)를 참조하세요.

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 해당 출력을 원하는 작업 개체의 이름  |
| <*property*> | 예 | String | 해당 값을 원하는 작업 개체의 속성에 대한 이름: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** 및 **clientTrackingId**. Azure Portal에서는 특정 실행 기록을 검토하여 이러한 속성을 찾을 수 있습니다. 자세한 내용은 [REST API - 워크플로 실행 작업](/rest/api/logic/workflowrunactions/get)을 참조하세요. |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*action-output*> | String | 지정한 작업 또는 속성의 출력 |
||||

*예제*

이 예제는 런타임 시 Twitter 작업 `Get user`에서 `status` 속성 값을 가져옵니다.

```
actions('Get_user').outputs.body.status
```

그리고 다음 결과를 반환합니다. `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

두 숫자를 더한 결과를 반환합니다.

```
add(<summand_1>, <summand_2>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | 예 | 정수, 부동 소수점 수 또는 혼합 | 더할 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*result-sum*> | 정수 또는 부동 소수점 수 | 지정한 숫자들을 더한 결과 |
||||

*예제*

이 예제는 지정한 숫자들을 더합니다.

```
add(1, 1.5)
```

그리고 다음 결과를 반환합니다. `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

타임스탬프에 일 수를 더합니다.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*days*> | 예 | 정수 | 더할 양수 또는 음수 일 수 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 + 지정한 일 수  |
||||

*예제 1*

이 예제는 지정한 타임스탬프에 10일을 더합니다.

```
addDays('2018-03-15T13:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-25T00:00:0000000Z"`

*예제 2*

이 예제는 지정한 타임스탬프에서 5일을 뺍니다.

```
addDays('2018-03-15T00:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

타임스탬프에 시간 수를 더합니다.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*hours*> | 예 | 정수 | 더할 양수 또는 음수 시간 수 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 + 지정한 시간 수  |
||||

*예제 1*

이 예제는 지정한 타임스탬프에 10시간을 더합니다.

```
addHours('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

*예제 2*

이 예제는 지정한 타임스탬프에서 5시간을 뺍니다.

```
addHours('2018-03-15T15:00:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

타임스탬프에 분 수를 더합니다.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*minutes*> | 예 | 정수 | 더할 양수 또는 음수 분 수 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 + 지정한 분 수 |
||||

*예제 1*

이 예제는 지정한 타임스탬프에 10분을 더합니다.

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:20:00.0000000Z"`

*예제 2*

이 예제는 지정한 타임스탬프에서 5분을 뺍니다.

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

JSON 개체에 속성 및 해당 값 또는 이름-값 쌍을 추가하고 업데이트된 개체를 반환합니다. 런타임에 속성이 이미 있는 경우 함수가 실패하고 오류를 throws합니다.

```
addProperty(<object>, '<property>', <value>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 속성을 추가할 JSON 개체 |
| <*property*> | 예 | String | 추가할 속성의 이름 |
| <*value*> | 예 | 모두 | 속성의 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 지정한 속성으로 업데이트된 JSON 개체 |
||||

기존 속성에 자식 속성을 추가하려면 다음 구문을 사용합니다.

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 속성을 추가할 JSON 개체 |
| <*parent-property*> | 예 | String | 자식 속성을 추가하려는 부모 속성의 이름입니다. |
| <*child-property*> | 예 | String | 추가할 자식 속성의 이름 |
| <*value*> | 예 | 모두 | 지정한 속성에 대해 설정할 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 해당 속성을 설정한 업데이트된 JSON 개체 |
||||

*예제 1*

이 예제에서는 `middleName` 속성을 JSON 개체에 추가합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다. 개체에는 이미 `firstName` 및 `surName` 속성이 포함되어 있습니다. 함수는 지정된 값을 새 속성에 할당하고 업데이트된 개체를 반환합니다.

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

다음은 현재 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*예제 2*

이 예제에서는 `middleName` 자식 속성을 JSON 개체의 `customerName` 속성에 추가합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다. 함수는 지정된 값을 새 속성에 할당하고 업데이트된 개체를 반환합니다.

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

다음은 현재 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

타임스탬프에 초 수를 더합니다.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*seconds*> | 예 | 정수 | 더할 양수 또는 음수 초 수 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 + 지정한 초 수  |
||||

*예제 1*

이 예제는 지정한 타임스탬프에 10초를 더합니다.

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:10.0000000Z"`

*예제 2*

이 예제는 지정한 타임스탬프에서 5초를 뺍니다.

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

타임스탬프에 시간 단위 수를 더합니다.
[getFutureTime()](#getFutureTime)도 함께 참조하세요.

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*interval*> | 예 | 정수 | 더할 지정한 시간 단위 수 |
| <*timeUnit*> | 예 | String | *interval*을 사용하는 시간 단위: “초”, “분”, “시간”, “일”, “주”, “월”, “년” |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 + 지정한 시간 단위 수  |
||||

*예제 1*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-02T00:00:00.0000000Z"`

*예제 2*

이 예제는 지정한 타임스탬프에 1일을 더합니다.

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

그리고 선택적 "D"를 사용하여 다음 결과를 반환합니다. `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>and

모든 식이 true인지 확인합니다.
모든 식이 true이면 true를 반환하거나 식 중의 적어도 개가 false이면 false를 반환합니다.

```
and(<expression1>, <expression2>, ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 예 | 부울 | 검사할 식 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| true 또는 false | 부울 | 모든 식이 true이면 true를 반환합니다. 식 중의 적어도 한 개가 false이면 false를 반환합니다. |
||||

*예제 1*

아래 예제는 지정한 부울 값이 모두 true인지 검사합니다.

```
and(true, true)
and(false, true)
and(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다.
* 두 번째 예제: 한 식이 true이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

*예제 2*

아래 예제는 지정한 식이 모두 true인지 검사합니다.

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 식이 모두 true이므로 `true`를 반환합니다.
* 두 번째 예제: 한 식이 true이므로 `false`를 반환합니다.
* 세 번째 예제: 두 식이 모두 false이므로 `false`를 반환합니다.

<a name="array"></a>

### <a name="array"></a>array

단일 지정 입력에서 배열을 반환합니다.
여러 입력의 경우 [createArray()](#createArray)를 참조하세요.

```
array('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 배열을 만드는 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | 단일 지정 입력을 포함하는 배열 |
||||

*예제*

이 예제는 "hello" 문자열에서 배열을 만듭니다.

```
array('hello')
```

그리고 다음 결과를 반환합니다. `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

문자열에 대한 base64로 인코딩된 버전을 반환합니다.

> [!NOTE]
> Azure Logic Apps는 base64 인코딩 및 디코딩을 자동으로 수행하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

```
base64('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 입력 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | 문자열에 대한 base64로 인코딩된 버전 |
||||

*예제*

이 예제는 "hello" 문자열을 base64로 인코딩된 문자열로 변환합니다.

```
base64('hello')
```

그리고 다음 결과를 반환합니다. `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Base64로 인코딩된 문자열에 대한 이진 버전을 반환합니다.

> [!NOTE]
> Azure Logic Apps는 base64 인코딩 및 디코딩을 자동으로 수행하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

```
base64ToBinary('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 base64로 인코딩된 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | Base64로 인코딩된 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 이진 문자열로 변환합니다.

```
base64ToBinary('aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

base64로 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 base64 문자열을 디코딩합니다. 더 이상 사용되지 않는 [decodeBase64()](#decodeBase64) 함수 대신 이 함수를 사용하세요.

> [!NOTE]
> Azure Logic Apps는 base64 인코딩 및 디코딩을 자동으로 수행하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

```
base64ToString('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 디코딩할 base64로 인코딩된 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | Base64로 인코딩된 문자열에 대한 문자열 버전 |
||||

*예제*

이 예제는 base64로 인코딩된 문자열 "aGVsbG8="을 단순한 문자열로 변환합니다.

```
base64ToString('aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

문자열에 대한 이진 버전을 반환합니다.

```
binary('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | 지정한 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 "hello" 문자열을 이진 문자열로 변환합니다.

```
binary('hello')
```

그리고 다음 결과를 반환합니다.

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>본문

런타임 시 작업의 `body` 출력을 반환합니다.
`actions('<actionName>').outputs.body`를 줄인 표현입니다.
[actionBody()](#actionBody) 및 [actions()](#actions)를 참조하세요.

```
body('<actionName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 원하는 작업의 `body` 출력에 대한 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*action-body-output*> | String | 지정한 작업의 `body` 출력 |
||||

*예제*

이 예제는 `Get user` Twitter 작업 `body`에서 출력을 가져옵니다.

```
body('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

값의 부울 버전을 반환합니다.

```
bool(<value>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 모두 | 부울로 변환할 값입니다. |
|||||

를 개체와 함께 사용 하는 경우 `bool()` 개체의 값은 부울로 변환할 수 있는 문자열 또는 정수 여야 합니다.

| 반환 값 | Type | 설명 |
| ------------ | ---- | ----------- |
| `true` 또는 `false` | 부울 | 지정 된 값의 부울 버전입니다. |
||||

*출력*

다음 예에서는에 대해 지원 되는 다양 한 유형의 입력을 보여 줍니다 `bool()` .

| 입력 값 | Type | 반환 값 |
| ----------- | ---------- | ---------------------- |
| `bool(1)` | 정수 | `true` |
| `bool(0)` | 정수    | `false` |
| `bool(-1)` | 정수 | `true` |
| `bool('true')` | String | `true` |
| `bool('false')` | String | `false` |

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

하나 이상의 매개 변수에서 Null이 아닌 첫 번째 값을 반환합니다.
빈 문자열, 빈 배열 및 빈 개체는 null이 아닙니다.

```
coalesce(<object_1>, <object_2>, ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | 예 | Any, 형식을 혼합할 수 있음 | Null인지 검사할 하나 이상의 항목 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | 모두 | Null이 아닌 첫 번째 항목 또는 값입니다. 모든 매개 변수가 Null이면 이 함수는 Null을 반환합니다. |
||||

*예제*

아래 예제는 지정한 값에서 Null이 아닌 첫 번째 값 또는 모든 값이 Null인 경우 Null을 반환합니다.

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `"hello"`
* 세 번째 예제: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

둘 이상의 문자열을 결합하고 결합된 문자열을 반환합니다.

```
concat('<text1>', '<text2>', ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | 예 | String | 결합할 적어도 두 개의 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | 결합된 입력 문자열에서 만든 문자열 |
||||

*예제*

이 예제는 문자열 "Hello"와 "World"를 결합합니다.

```
concat('Hello', 'World')
```

그리고 다음 결과를 반환합니다. `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

컬렉션에 특정 항목이 있는지 검사합니다.
항목이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분합니다.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

특히 이 함수는 아래 컬렉션 형식에서 작동합니다.

* *부분 문자열*을 찾기 위한 *문자열*
* *값*을 찾기 위한 *배열*
* *키*를 찾기 위한 *사전*

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열, 배열 또는 사전 | 검사할 컬렉션 |
| <*value*> | 예 | 각각 문자열, 배열 또는 사전 | 찾을 항목 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 항목이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예제 1*

이 예제는 부분 문자열 "world"에 대한 문자열 "hello world"를 검사하고 true를 반환합니다.

```
contains('hello world', 'world')
```

*예제 2*

이 예제는 부분 문자열 "universe"에 대한 문자열 "hello world"를 검사하고 false를 반환합니다.

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

UTC(Universal Time Coordinated)의 타임스탬프를 대상 표준 시간대로 변환합니다.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*destinationTimeZone*> | 예 | String | 대상 표준 시간대의 이름입니다. 표준 시간대 이름의 경우 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조하세요. 다만 표준 시간대 이름에서 모든 문장 부호를 제거해야 할 수도 있습니다. |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | 대상 표준 시간대로 변환된 타임스탬프 |
||||

*예제 1*

이 예제는 타임스탬프를 지정한 표준 시간대로 변환합니다.

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예제 2*

이 예제는 타임스탬프를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

원본 표준 시간대의 타임스탬프를 대상 표준 시간대로 변환합니다.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*sourceTimeZone*> | 예 | String | 원본 표준 시간대의 이름입니다. 표준 시간대 이름의 경우 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조하세요. 다만 표준 시간대 이름에서 모든 문장 부호를 제거해야 할 수도 있습니다. |
| <*destinationTimeZone*> | 예 | String | 대상 표준 시간대의 이름입니다. 표준 시간대 이름의 경우 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조하세요. 다만 표준 시간대 이름에서 모든 문장 부호를 제거해야 할 수도 있습니다. |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | 대상 표준 시간대로 변환된 타임스탬프 |
||||

*예제 1*

이 예제는 원본 표준 시간대를 대상 표준 시간대로 변환합니다.

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00.0000000"`

*예제 2*

이 예제는 표준 시간대를 지정한 표준 시간대 및 형식으로 변환합니다.

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

원본 표준 시간대의 타임스탬프를 UTC(Universal Time Coordinated)로 변환합니다.

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*sourceTimeZone*> | 예 | String | 원본 표준 시간대의 이름입니다. 표준 시간대 이름의 경우 [Microsoft 표준 시간대 인덱스 값](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values)을 참조하세요. 다만 표준 시간대 이름에서 모든 문장 부호를 제거해야 할 수도 있습니다. |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | UTC로 변환된 타임스탬프 |
||||

*예제 1*

이 예제는 타임스탬프를 UTC로 변환합니다.

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T08:00:00.0000000Z"`

*예제 2*

이 예제는 타임스탬프를 UTC로 변환합니다.

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

그리고 다음 결과를 반환합니다. `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

여러 입력에서 배열을 반환합니다.
단일 입력 배열의 경우 [array()](#array)를 참조하세요.

```
createArray('<object1>', '<object2>', ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | 예 | 모두, 그러나 혼합은 안 됨 | 배열을 만들 적어도 두 개의 항목 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | 모든 입력 항목에서 만든 배열 |
||||

*예제*

이 예제는 다음 입력에서 배열을 만듭니다.

```
createArray('h', 'e', 'l', 'l', 'o')
```

그리고 다음 결과를 반환합니다. `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

문자열에 대한 데이터 URI(Uniform Resource Identifier)를 반환합니다.

```
dataUri('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | 입력 문자열에 대한 데이터 URI |
||||

*예제*

이 예제는 "hello" 문자열에 대한 데이터 URI를 만듭니다.

```
dataUri('hello')
```

그리고 다음 결과를 반환합니다. `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다.
[decodeDataUri()](#decodeDataUri) 대신에 이 함수를 사용합니다.
두 함수 모두 같은 방법으로 작동하지만 `dataUriBinary()`를 사용하는 것이 좋습니다.

```
dataUriToBinary('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 데이터 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | 데이터 URI에 대한 이진 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 만듭니다.

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

데이터 URI(Uniform Resource Identifier)에 대한 문자열 버전을 반환합니다.

```
dataUriToString('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 데이터 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | 데이터 URI에 대한 문자열 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 문자열을 만듭니다.

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다. `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

타임스탬프에서 월의 날짜를 반환합니다.

```
dayOfMonth('<timestamp>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-month*> | 정수 | 지정한 타임스탬프의 월의 날짜 |
||||

*예제*

이 예제는 이 타임스탬프에서 월의 날짜에 대한 숫자를 반환합니다.

```
dayOfMonth('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

타임스탬프에서 요일을 반환합니다.

```
dayOfWeek('<timestamp>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-week*> | 정수 | 일요일이 0, 월요일이 1이라는 식으로 지정한 타임스탬프의 요일 |
||||

*예제*

이 예제는 이 타임스탬프에서 요일에 대한 숫자를 반환합니다.

```
dayOfWeek('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

타임스탬프에서 년의 날짜를 반환합니다.

```
dayOfYear('<timestamp>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*day-of-year*> | 정수 | 지정한 타임스탬프의 년의 날짜 |
||||

*예제*

이 예제는 이 타임스탬프에서 년의 날짜인 숫자를 반환합니다.

```
dayOfYear('2018-03-15T13:27:36Z')
```

그리고 다음 결과를 반환합니다. `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64(사용되지 않음)

이 함수는 더 이상 사용되지 않으므로 [base64ToString()](#base64ToString)를 대신 사용하세요.

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

데이터 URI(Uniform Resource Identifier)에 대한 이진 버전을 반환합니다. `decodeDataUri()` 대신에 [dataUriToBinary()](#dataUriToBinary)의 사용을 검토해 보세요. 두 함수 모두 같은 방법으로 작동하지만 `dataUriToBinary()`를 사용하는 것이 좋습니다.

> [!NOTE]
> Azure Logic Apps는 base64 인코딩 및 디코딩을 자동으로 수행하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

```
decodeDataUri('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 디코딩할 데이터 URI 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | 데이터 URI 문자열에 대한 이진 버전 |
||||

*예제*

이 예제는 이 데이터 URI에 대한 이진 버전을 반환합니다.

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

그리고 다음 결과를 반환합니다.

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

디코딩된 버전으로 이스케이프 문자를 바꾸는 문자열을 반환합니다.

```
decodeUriComponent('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 디코딩할 이스케이프 문자를 포함한 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | 디코딩된 이스케이프 문자로 업데이트된 문자열 |
||||

*예제*

이 예제는 이 문자열의 이스케이프 문자를 디코딩된 버전으로 바꿉니다.

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

두 숫자를 나눈 결과를 반환합니다. 나머지 결과를 얻으려면 [mod()](#mod)를 참조하세요.

```
div(<dividend>, <divisor>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 예 | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 |
| <*divisor*> | 예 | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 수, 그러나 0이 올 수 없음 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*quotient-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 나눈 결과입니다. 피제수 또는 제 수가 Float 형식인 경우 결과에는 Float 형식이 있습니다. <p><p>**참고**: float 결과를 정수로 변환 하려면 논리 앱에서 [Azure 함수를 만들고 호출](../logic-apps/logic-apps-azure-functions.md) 해 보세요. |
||||

*예제 1*

두 예제 모두 정수 형식으로이 값을 반환 합니다. `2`

```
div(10,5)
div(11,5)
```

*예제 2*

두 예제 모두 Float 형식으로이 값을 반환 합니다. `2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다. `encodeUriComponent()` 대신에 [uriComponent()](#uriComponent)의 사용을 검토해 보세요. 두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`를 사용하는 것이 좋습니다.

> [!NOTE]
> Azure Logic Apps는 base64 인코딩 및 디코딩을 자동으로 수행하므로 사용자가 이러한 변환을 수동으로 수행할 필요가 없습니다. 그러나 이렇게 하면 예기치 않은 표시 동작이 발생할 수 있으며, 이러한 동작은 실제 변환에 영향을 주지는 않고 표시 방법에만 영향을 줍니다. 자세한 내용은 [암시적 데이터 형식 변환](#implicit-data-conversions)을 참조하세요.

```
encodeUriComponent('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | URI 인코딩된 형식으로 변환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | 이스케이프 문자로 URI 인코딩된 문자열 |
||||

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
encodeUriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

컬렉션이 비어 있는지 검사합니다.
컬렉션이 비어 있으면 true를 반환하거나 비어 있지 않으면 false를 반환합니다.

```
empty('<collection>')
empty([<collection>])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열, 배열 또는 개체 | 검사할 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 컬렉션이 비어 있으면 true를 반환합니다. 비어 있지 않으면 false를 반환합니다. |
||||

*예제*

아래 예제는 지정한 컬렉션이 비어 있는지 검사합니다.

```
empty('')
empty('abc')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 비어 있는 문자열을 전달하므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 문자열 “abc”를 전달하므로 함수는 `false`를 반환합니다.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

문자열이 특정 하위 문자열로 종료하는지 검사합니다.
하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분하지 않습니다.

```
endsWith('<text>', '<searchText>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 검사할 문자열 |
| <*searchText*> | 예 | String | 찾을 끝나는 하위 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false  | 부울 | 끝나는 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예제 1*

이 예제는 "hellow world" 문자열이 "world" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `true`

*예제 2*

이 예제는 "hellow world" 문자열이 "universe" 문자열로 끝나는지 검사합니다.

```
endsWith('hello world', 'universe')
```

그리고 다음 결과를 반환합니다. `false`

<a name="equals"></a>

### <a name="equals"></a>equals

두 값, 식 또는 개체가 모두 같은지 검사합니다.
둘이 다 같으면 true를 반환하거나 같지 않으면 false를 반환합니다.

```
equals('<object1>', '<object2>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | 예 | 다양 | 비교할 값, 식 또는 개체 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 둘이 같으면 true를 반환합니다. 같지 않으면 false를 반환합니다. |
||||

*예제*

아래 예제는 지정한 입력들이 같은지 검사합니다.

```
equals(true, 1)
equals('abc', 'abcd')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 두 값이 모두 같으므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 값이 모두 같으므로 함수는 `false`를 반환합니다.

<a name="first"></a>

### <a name="first"></a>first

문자열 또는 배열에서 첫 번째 항목을 반환합니다.

```
first('<collection>')
first([<collection>])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열 또는 배열 | 첫 번째 항목을 찾을 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | 모두 | 컬렉션의 첫 번째 항목 |
||||

*예제*

아래 예제는 이러한 컬렉션의 첫 번째 항목을 찾습니다.

```
first('hello')
first(createArray(0, 1, 2))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"h"`
* 두 번째 예제: `0`

<a name="float"></a>

### <a name="float"></a>float

부동 소수점 수에 대한 문자열 버전을 실제 부동 소수점 수로 변환합니다.
이 함수는 논리 앱이나 흐름 같은 앱에 사용자 지정 매개 변수를 전달할 때만 사용할 수 있습니다.

```
float('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 유효한 부동 소수점 수를 가진 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | 지정한 문자열에 대한 부동 소수점 수 |
||||

*예제*

이 예제는 이 부동 소수점 수에 대한 문자열 버전을 만듭니다.

```
float('10.333')
```

그리고 다음 결과를 반환합니다. `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

지정한 형식의 타임스탬프를 반환합니다.

```
formatDateTime('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | 지정한 형식으로 업데이트된 타임스탬프 |
||||

*예제*

이 예제는 타임스탬프를 지정한 형식으로 변환합니다.

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

작업의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 값을 가진 배열을 반환합니다.

```
formDataMultiValues('<actionName>', '<key>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 해당 출력이 원하는 키를 가진 작업 |
| <*key*> | 예 | String | 해당 값을 원하는 키의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 지정한 키와 일치하는 모든 값을 가진 배열 |
||||

*예제*

이 예제는 지정한 작업의 양식 데이터 또는 양식 인코딩된 출력의 "제목" 키의 값에서 배열을 만듭니다.

```
formDataMultiValues('Send_an_email', 'Subject')
```

그리고 배열의 제목 텍스트를 반환, 예: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

작업의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 단일 값을 반환합니다.
함수가 일치하는 항목을 두 개 이상 찾으면 함수에서 오류가 발생합니다.

```
formDataValue('<actionName>', '<key>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 해당 출력이 원하는 키를 가진 작업 |
| <*key*> | 예 | String | 해당 값을 원하는 키의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*key-value*> | String | 지정한 키의 값  |
||||

*예제*

이 예제는 지정한 작업의 양식 데이터 또는 양식 인코딩된 출력의 "제목" 키의 값에서 문자열을 만듭니다.

```
formDataValue('Send_an_email', 'Subject')
```

그리고 제목 텍스트를 문자열로 반환, 예: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

지정된 형식에 따라 숫자를 문자열로 반환합니다.

```text
formatNumber(<number>, <format>, <locale>?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number*> | 예 | 정수 또는 Double | 서식을 지정할 값입니다. |
| <*format*> | 예 | String | 사용할 형식을 지정하는 합성 형식 문자열입니다. 지원되는 숫자 형식 문자열은 `number.ToString(<format>, <locale>)`에서 지원하는 [표준 숫자 형식 문자열](/dotnet/standard/base-types/standard-numeric-format-strings)을 참조하세요. |
| <*locale*> | 예 | String | `number.ToString(<format>, <locale>)`에서 지원하는 대로 사용할 로캘입니다. 지정하지 않으면 기본값 `en-us`입니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*formatted-number*> | String | 사용자가 지정한 형식의 문자열로 지정된 숫자입니다. 이 반환 값을 `int` 또는 `float`로 캐스팅할 수 있습니다. |
||||

*예제 1*

숫자 `1234567890`의 서식을 지정하려고 한다고 가정하겠습니다. 이 예제에서는 이 숫자의 서식을 문자열 "1,234,567,890.00"으로 지정합니다.

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*예제 2"

숫자 `1234567890`의 서식을 지정하려고 한다고 가정하겠습니다. 이 예제에서는 이 숫자의 서식을 문자열 "1.234.567.890,00"으로 지정합니다.

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*예 3*

숫자 `17.35`의 서식을 지정하려고 한다고 가정하겠습니다. 이 예제에서는 이 숫자의 서식을 문자열 "$17.35"로 지정합니다.

```
formatNumber(17.36, 'C2')
```

*예제 4*

숫자 `17.35`의 서식을 지정하려고 한다고 가정하겠습니다. 이 예제에서는 이 숫자의 서식을 문자열 "17,35 kr"로 지정합니다.

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

지정한 시간 단위를 더한 현재 타임스탬프를 반환합니다.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | 예 | 정수 | 더할 지정한 시간 단위 수 |
| <*timeUnit*> | 예 | String | *interval*을 사용하는 시간 단위: “초”, “분”, “시간”, “일”, “주”, “월”, “년” |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 현재 타임스탬프 + 지정한 시간 단위 수 |
||||

*예제 1*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 해당 타임스탬프에 5일을 더합니다.

```
getFutureTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-03-06T00:00:00.0000000Z"`

*예제 2*

현재 타임스탬프가 "2018-03-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 5일을 더하고 결과를 "D" 형식으로 변환합니다.

```
getFutureTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

지정한 시간 단위를 뺀 현재 타임스탬프를 반환합니다.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*interval*> | 예 | 정수 | 뺄 지정한 시간 단위 수 |
| <*timeUnit*> | 예 | String | *interval*을 사용하는 시간 단위: “초”, “분”, “시간”, “일”, “주”, “월”, “년” |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 현재 타임스탬프 - 지정한 시간 단위 수 |
||||

*예제 1*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 해당 타임스탬프에서 5일을 뺍니다.

```
getPastTime(5, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-27T00:00:00.0000000Z"`

*예제 2*

현재 타임스탬프가 "2018-02-01T00:00:00.0000000Z"라고 가정합니다.
이 예제는 5일을 빼고 결과를 "D" 형식으로 변환합니다.

```
getPastTime(5, 'Day', 'D')
```

그리고 다음 결과를 반환합니다. `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

첫 번째 값이 두 번째 값보다 큰지 검사합니다.
첫 번째 값이 더 크면 true를 반환하거나 더 작으면 false를 반환합니다.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 큰지 검사할 첫 번째 값 |
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 첫 번째 값이 두 번째 값보다 크면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작거나 같으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 큰지 검사합니다.

```
greater(10, 5)
greater('apple', 'banana')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다.
첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환하거나 첫 번째 값이 더 작으면 false를 반환합니다.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 크거나 같은지 검사할 첫 번째 값 |
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 첫 번째 값이 두 번째 값보다 크거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 작으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 크거나 같은지 검사합니다.

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

GUID(전역적으로 고유한 식별자)를 문자열로 생성합니다. 예, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

또한 GUID에 대해 하이픈으로 구분된 32자리인 기본 형식 "D" 이외의 다른 형식을 지정할 수 있습니다.

```
guid('<format>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | 예 | String | 반환되는 GUID에 대한 단일 [형식 지정자](/dotnet/api/system.guid.tostring?view=netcore-3.1#system_guid_tostring_system_string_)입니다. 기본적으로 형식은 "D"이지만 "N", "D", "B", "P" 또는 "X"를 사용할 수 있습니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | 임의로 생성된 GUID |
||||

*예제*

이 예제는 같은 GUID를 생성하되, 하이픈으로 구분되고 소괄호로 둘러싼 32자리로 생성합니다.

```
guid('P')
```

그리고 다음 결과를 반환합니다. `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

식이 true인지 또는 false인지 검사합니다. 결과에 기반해 지정한 값을 반환합니다. 매개 변수는 왼쪽에서 오른쪽으로 계산됩니다.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 예 | 부울 | 검사할 식 |
| <*valueIfTrue*> | 예 | 모두 | 식이 true이면 반환할 값 |
| <*valueIfFalse*> | 예 | 모두 | 식이 false이면 반환할 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | 모두 | 식이 true인지 또는 false인지에 기반해 반환하는 지정한 값 |
||||

*예제*

이 예제는 지정한 식이 true를 반환하므로 `"yes"`를 반환합니다.
그렇지 않으면 이 예제는 `"no"`를 반환합니다.

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexof

하위 문자열에 대한 시작 위치 또는 인덱스 값을 반환합니다.
이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다.

```
indexOf('<text>', '<searchText>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 찾을 하위 문자열을 가진 문자열 |
| <*searchText*> | 예 | String | 찾을 하위 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*index-value*>| 정수 | 지정한 하위 문자열에 대한 시작 위치 또는 인덱스 값 <p>문자열을 찾을 수 없는 경우 -1을 반환합니다. |
||||

*예제*

이 예제는 "hello world" 문자열의 "world" 하위 문자열에 대한 시작 인덱스 값을 찾습니다.

```
indexOf('hello world', 'world')
```

그리고 다음 결과를 반환합니다. `6`

<a name="int"></a>

### <a name="int"></a>int

문자열에 대한 정수 버전을 반환합니다.

```
int('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*integer-result*> | 정수 | 지정한 문자열에 대한 정수 버전 |
||||

*예제*

이 예제는 문자열 "10"에 대한 정수 버전을 만듭니다.

```
int('10')
```

그리고 다음 결과를 반환합니다. `10`

<a name="item"></a>

### <a name="item"></a>항목

배열에 대해 반복 작업 내에 사용하는 경우 작업의 현재 반복 중에 배열의 현재 항목을 반환합니다.
또한 해당 항목의 속성에서 값을 가져올 수 있습니다.

```
item()
```

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*current-array-item*> | 모두 | 작업의 현재 반복에 대한 배열의 현재 항목 |
||||

*예제*

이 예제는 for-each 루프의 현재 반복 내에 있는 "Send_an_email" 작업에 대한 현재 메시지에서 `body`요소를 가져옵니다.

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

for-each 루프의 각 주기에서 현재 항목을 반환합니다.
이 함수를 for-each 루프 내에 사용합니다.

```
items('<loopName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | 예 | String | for-each 루프의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*item*> | 모두 | 지정한 for-each 루프의 현재 주기의 항목 |
||||

*예제*

이 예제는 지정한 for-each 루프에서 현재 항목을 가져옵니다.

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Until 루프 내에서 현재 반복의 인덱스 값을 반환합니다. 중첩된 Until 루프 내에서 이 함수를 사용할 수 있습니다. 

```
iterationIndexes('<loopName>')
```

| 매개 변수 | 필수 | Type | Description | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | 예 | String | Until 루프의 이름 | 
||||| 

| 반환 값 | Type | Description | 
| ------------ | ---- | ----------- | 
| <*index*> | 정수 | 지정된 Until 루프 내에서 현재 반복의 인덱스 값 | 
|||| 

*예제* 

이 예제에서는 카운터 변수를 만들고, 카운터 값이 5가 될 때까지 Until 루프를 반복할 때마다 변수를 1씩 증가시킵니다. 또한 이 예제에서는 반복마다 현재 인덱스를 추적하는 변수를 만듭니다. 이 예제는 Until 루프에서 각 반복마다 카운터를 증가시키고 카운터 값을 현재 인덱스 값에 할당한 다음, 카운터를 증가시킵니다. 루프에 있는 동안 이 예제는 다음과 같이 `iterationIndexes` 함수를 사용하여 현재 반복 인덱스를 참조합니다.

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json :

JSON(JavaScript Object Notation) 형식 값 또는 문자열이나 XML에 대한 개체를 반환합니다.

```
json('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 문자열 또는 XML | 변환할 문자열 또는 XML |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*JSON-result*> | JSON 원시 형식 또는 개체 | JSON 고유 형식 값 또는 지정한 문자열이나 XML에 대한 개체입니다. 문자열이 Null이면, 함수는 빈 개체를 반환합니다. |
||||

*예제 1*

이 예제는 이 문자열을 JSON 값으로 변환합니다.

```
json('[1, 2, 3]')
```

그리고 다음 결과를 반환합니다. `[1, 2, 3]`

*예제 2*

이 예제는 이 문자열을 JSON으로 변환합니다.

```
json('{"fullName": "Sophia Owen"}')
```

그리고 다음 결과를 반환합니다.

```
{
  "fullName": "Sophia Owen"
}
```

*예 3*

이 예제는 이 XML을 JSON으로 변환합니다.

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

그리고 다음 결과를 반환합니다.

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>교집합

지정한 컬렉션에서 공통 항목*만* 있는 컬렉션을 반환합니다.
결과에 나타내려면 항목이 이 함수에 전달된 모든 컬렉션에 나타나야 합니다.
같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | 예 | 배열 또는 개체, 그러나 둘 다는 아님 | 공통 항목*만* 원하는 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*common-items*> | 각각 배열 또는 개체 | 지정한 컬렉션에 대해 공통 항목만 있는 컬렉션 |
||||

*예제*

이 예제는 이러한 배열에 대한 공통 항목을 찾습니다.

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

그리고 다음 항목*만* 가진 배열을 반환합니다. `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

배열의 모든 항목을 포함하고 각 문자가 *구분 기호*로 구분된 문자열을 반환합니다.

```
join([<collection>], '<delimiter>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | Array | 조인할 항목이 있는 배열 |
| <*delimiter*> | 예 | String | 결과 문자열의 각 문자 사이에 나타나는 구분 기호 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | 지정한 배열의 모든 항목에서 만든 결과 문자열 |
||||

*예제*

이 예제는 지정한 문자를 구분 기호로 하여 이 배열의 모든 항목에서 문자열을 만듭니다.

```
join(createArray('a', 'b', 'c'), '.')
```

그리고 다음 결과를 반환합니다. `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

컬렉션에서 마지막 항목을 반환합니다.

```
last('<collection>')
last([<collection>])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열 또는 배열 | 마지막 항목을 찾을 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | 각각 문자열 또는 배열 | 컬렉션의 마지막 항목 |
||||

*예제*

아래 예제는 이러한 컬렉션의 마지막 항목을 찾습니다.

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"d"`
* 두 번째 예제: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastindexof

마지막으로 나오는 부분 문자열의 시작 위치 또는 인덱스 값을 반환합니다. 이 함수는 대/소문자를 구분하지 않으며 인덱스는 숫자 0으로 시작합니다.

```json
lastIndexOf('<text>', '<searchText>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 찾을 하위 문자열을 가진 문자열 |
| <*searchText*> | 예 | String | 찾을 하위 문자열 |
|||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | 정수 | 마지막으로 나오는 지정된 부분 문자열의 시작 위치 또는 인덱스 값입니다. |
|||

문자열이 나 부분 문자열 값이 비어 있으면 다음과 같은 동작이 발생 합니다.

* 문자열 값이 비어 있으면 `-1` 이 반환 됩니다.

* 문자열과 부분 문자열 값이 모두 비어 있으면 `0` 이 반환 됩니다.

* 부분 문자열 값만 비어 있는 경우 다음 두 값 중 더 큰 값이 반환 됩니다.

  * `0`

  * 문자열의 길이에서 1을 뺀 값입니다.

*예*

이 예에서는 문자열에서 하위 문자열 부분 문자열의 마지막 항목에 대 한 시작 인덱스 값을 찾습니다 `world` `hello world hello world` . 반환 된 결과는 `18` 다음과 같습니다.

```json
lastIndexOf('hello world hello world', 'world')
```

이 예제에서는 부분 문자열 매개 변수가 누락 되 고 `22` 입력 문자열 ()의 값에서 `23` 1을 뺀 값이 0 보다 크므로 값을 반환 합니다.

```json
lastIndexOf('hello world hello world', '')
```

<a name="length"></a>

### <a name="length"></a>length

컬렉션의 항목 수를 반환합니다.

```
length('<collection>')
length([<collection>])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열 또는 배열 | 개수를 셀 항목을 포함하는 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*length-or-count*> | 정수 | 컬렉션의 항목 수 |
||||

*예제*

아래 예제는 이러한 컬렉션의 항목 수를 셉니다.

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

그리고 다음 결과를 반환합니다. `4`

<a name="less"></a>

### <a name="less"></a>less

첫 번째 값이 두 번째 값보다 작은지 검사합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작은지 검사할 첫 번째 값 |
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 첫 번째 값이 두 번째 값보다 작으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크거나 같으면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작은지 검사합니다.

```
less(5, 10)
less('banana', 'apple')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다.
첫 번째 값이 두 번째 값보다 작으면 true를 반환하거나 첫 번째 값이 더 크면 false를 반환합니다.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 정수, 부동 소수점 수 또는 문자열 | 두 번째 값보다 작거나 같은지 검사할 첫 번째 값 |
| <*compareTo*> | 예 | 각각 정수, 부동 소수점 수 또는 문자열 | 비교 항목 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false  | 부울 | 첫 번째 값이 두 번째 값보다 작거나 같으면 true를 반환합니다. 첫 번째 값이 두 번째 값보다 크면 false를 반환합니다. |
||||

*예제*

아래 예제는 첫 번째 값이 두 번째 값보다 작거나 같은지 검사합니다.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `true`
* 두 번째 예제: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

트리거 또는 작업을 호출하는 "콜백 URL"을 반환합니다.
이 함수는 **HttpWebhook** 및 **ApiConnectionWebhook** 커넥터 형식에 대해서만 작동하며, **Manual**, **Recurrence**, **HTTP** 및 **APIConnection** 형식에 대해서는 작동하지 않습니다.

```
listCallbackUrl()
```

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*callback-URL*> | String | 트리거 또는 작업에 대한 콜백 URL |
||||

*예제*

이 예제는 이 함수가 반환할 수 있는 샘플 콜백 URL을 보여 줍니다.

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

양끝이 포함된 숫자를 가진 목록 또는 배열에서 가장 높은 값을 반환합니다.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 예 | 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 집합 |
| [<*number1*>, <*number2*>, ...] | 예 | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 높은 값을 원하는 숫자의 배열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*max-value*> | 정수 또는 부동 소수점 수 | 지정한 배열 또는 숫자 집합의 가장 높은 값 |
||||

*예제*

아래 예제는 숫자 집합 및 배열에서 가장 높은 값을 가져옵니다.

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

그리고 다음 결과를 반환합니다. `3`

<a name="min"></a>

### <a name="min"></a>min

숫자 또는 배열 집합에서 가장 낮은 값을 반환합니다.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | 예 | 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 집합 |
| [<*number1*>, <*number2*>, ...] | 예 | 배열 - 정수, 부동 소수점 수 또는 둘 다 | 가장 낮은 값을 원하는 숫자의 배열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*min-value*> | 정수 또는 부동 소수점 수 | 지정한 숫자 집합 또는 지정한 배열의 가장 낮은 값 |
||||

*예제*

아래 예제는 숫자 집합 및 배열에서 가장 낮은 값을 가져옵니다.

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

그리고 다음 결과를 반환합니다. `1`

<a name="mod"></a>

### <a name="mod"></a>mod

두 숫자를 나눈 나머지를 반환합니다.
정수 결과를 가져오는 방법은 [div()](#div)를 참조하세요.

```
mod(<dividend>, <divisor>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | 예 | 정수 또는 부동 소수점 수 | *제수*로 나눌 숫자 |
| <*divisor*> | 예 | 정수 또는 부동 소수점 수 | *피제수*를 분할하는 숫자, 그러나 0이 올 수 없습니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*modulo-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 나눈 나머지 |
||||

*예제*

이 예제는 첫 번째 숫자를 두 번째 숫자로 나눕니다.

```
mod(3, 2)
```

그리고 다음 결과를 반환합니다. `1`

<a name="mul"></a>

### <a name="mul"></a>mul

두 숫자를 곱한 결과를 반환합니다.

```
mul(<multiplicand1>, <multiplicand2>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | 예 | 정수 또는 부동 소수점 수 | *multiplicand2*로 곱할 숫자 |
| <*multiplicand2*> | 예 | 정수 또는 부동 소수점 수 | *multiplicand1*를 곱하는 숫자 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*product-result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자를 두 번째 숫자로 곱한 결과 |
||||

*예제*

아래 예제는 첫 번째 숫자를 두 번째 숫자로 곱합니다.

```
mul(1, 2)
mul(1.5, 2)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `2`
* 두 번째 예제 `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

여러 부분으로 구성된 작업의 출력에서 특정 부분에 대한 본문을 반환합니다.

```
multipartBody('<actionName>', <index>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 여러 부분으로 구성된 출력을 가진 작업의 이름 |
| <*index*> | 예 | 정수 | 원하는 부분에 대한 인덱스 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*body*> | String | 지정한 부분에 대한 본문 |
||||

<a name="not"></a>

### <a name="not"></a>not

식이 false인지 검사합니다.
식이 false이면 true를 반환하거나 true이면 false를 반환합니다.

```json
not(<expression>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression*> | 예 | 부울 | 검사할 식 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 식이 false이면 true를 반환합니다. 식이 true이면 false를 반환합니다. |
||||

*예제 1*

아래 예제는 지정한 식이 모두 false인지 검사합니다.

```json
not(false)
not(true)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

*예제 2*

아래 예제는 지정한 식이 모두 false인지 검사합니다.

```json
not(equals(1, 2))
not(equals(1, 1))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 식이 false이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 식이 true이므로 함수는 `false`를 반환합니다.

<a name="or"></a>

### <a name="or"></a>또는

최소 하나의 식이 true인지 검사합니다.
최소 하나의 식이 true이면 true를 반환하거나 모든 식이 false이면 false를 반환합니다.

```
or(<expression1>, <expression2>, ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | 예 | 부울 | 검사할 식 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false | 부울 | 최소 하나의 식이 true이면 true를 반환합니다. 모든 식이 false이면 false를 반환합니다. |
||||

*예제 1*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```json
or(true, false)
or(false, false)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 한 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

*예제 2*

아래 예제는 최소 하나의 식이 true인지 검사합니다.

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: 최소 한 식이 true이므로 함수는 `true`를 반환합니다.
* 두 번째 예제: 두 식이 모두 false이므로 함수는 `false`를 반환합니다.

<a name="outputs"></a>

### <a name="outputs"></a>outputs

런타임에 작업의 출력을 반환합니다. `actionOutputs()` 함수 대신 논리 앱 디자이너에서 `outputs()`으로 확인되는 이 함수를 사용하세요. 두 함수 모두 같은 방법으로 작동하지만 `outputs()`를 사용하는 것이 좋습니다.

```
outputs('<actionName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | 예 | String | 원하는 작업의 출력에 대한 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | -----| ----------- |
| <*output*> | String | 지정한 작업의 출력 |
||||

*예제*

이 예제는 Twitter 작업 `Get user`에서 출력을 가져옵니다.

```
outputs('Get_user')
```

그리고 다음 결과를 반환합니다.

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>매개 변수

흐름 정의에 설명된 매개 변수의 값을 반환합니다.

```
parameters('<parameterName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | 예 | String | 해당 값을 원하는 매개 변수의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*parameter-value*> | 모두 | 지정한 매개 변수의 값 |
||||

*예제*

이 JSON 값을 가지고 있다고 가정합니다.

```json
{
  "fullName": "Sophia Owen"
}
```

이 예제는 지정한 매개 변수의 값을 가져옵니다.

```
parameters('fullName')
```

그리고 다음 결과를 반환합니다. `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

시작 끝만 포함하는 지정한 범위에서 임의의 정수를 반환합니다.

```
rand(<minValue>, <maxValue>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | 예 | 정수 | 범위의 가장 작은 정수 |
| <*maxValue*> | 예 | 정수 | 함수가 반환할 수 있는 범위에서 가장 높은 정수 다음에 오는 정수 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*random-result*> | 정수 | 지정한 범위에서 반환된 임의의 정수 |
||||

*예제*

이 예제는 최댓값을 제외하고 지정한 범위에서 임의의 정수를 가져옵니다.

```
rand(1, 5)
```

그리고 다음 숫자 중 하나를 결과로 반환합니다. `1`, `2`, `3` 또는 `4`

<a name="range"></a>

### <a name="range"></a>range

지정한 정수에서 시작하는 정수 배열을 반환합니다.

```
range(<startIndex>, <count>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | 예 | 정수 | 배열을 첫 번째 항목으로 시작하는 정수 값 |
| <*count*> | 예 | 정수 | 배열에 있는 정수의 숫자 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | 지정한 인덱스에서 시작하는 정수를 포함하는 배열 |
||||

*예제*

이 예제는 지정한 인덱스에서 시작하고 지정한 수의 정수를 가진 정수 배열을 만듭니다.

```
range(1, 4)
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

하위 문자열을 지정한 문자열로 바꾸고 결과 문자열을 반환합니다. 이 함수는 대/소문자를 구분합니다.

```
replace('<text>', '<oldText>', '<newText>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 바꿀 하위 문자열을 가진 문자열 |
| <*oldText*> | 예 | String | 바꿀 하위 문자열 |
| <*newText*> | 예 | String | 대체 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | 하위 문자열을 바꾼 후 업데이트된 문자열 <p>하위 문자열이 발견되지 않으면 원래 문자열을 반환합니다. |
||||

*예제*

이 예제는 "the old string"의 "old" 하위 문자열을 찾고 "old"를 "new"로 바꿉니다.

```
replace('the old string', 'old', 'new')
```

그리고 다음 결과를 반환합니다. `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

개체에서 속성을 제거하고 업데이트된 개체를 반환합니다. 제거하려는 속성이 없으면 함수가 원래 개체를 반환합니다.

```
removeProperty(<object>, '<property>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 속성을 제거할 JSON 개체 |
| <*property*> | 예 | String | 제거할 속성의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 지정한 속성 없이 업데이트된 JSON 개체 |
||||

기존 속성에서 자식 속성을 제거하려면 다음 구문을 사용합니다.

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 속성을 제거하려는 JSON 개체 |
| <*parent-property*> | 예 | String | 자식 속성을 제거하려는 부모 속성의 이름 |
| <*child-property*> | 예 | String | 제거할 자식 속성의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 자식 속성을 제거한 업데이트된 JSON 개체 |
||||

*예제 1*

다음 예제는 `middleName` 속성을 JSON 개체에서 제거하고 업데이트된 개체를 반환합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다.

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

다음은 현재 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*예제 2*

다음 예제는 `middleName` 자식 속성을 JSON 개체의 `customerName` 부모 속성에서 제거하고, 업데이트된 개체를 반환합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다.

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

다음은 현재 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

`For_each`, `Until` 또는 `Scope`처럼 지정된 범위 내에 있는 모든 작업의 입력과 출력을 반환합니다. 이 함수는 예외를 진단하고 처리할 수 있도록 실패한 작업의 결과를 반환할 때 유용합니다. 자세한 내용은 [실패에 대한 컨텍스트 및 결과 가져오기](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures)를 참조하세요.

```
result('<scopedActionName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | 예 | String | 모든 내부 작업의 입력과 출력을 반환할 범위가 지정된 작업의 이름입니다. |
||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*array-object*> | 배열 개체 | 지정된 작업 범위 내에서 표시되는 각 작업의 입력 및 출력 배열을 포함하는 배열 |
||||

*예제*

이 예제는 `result()` 함수를 `Compose` 작업에 사용하여 `For_each` 루프 내부에 있는 각 HTTP 작업 반복의 입력 및 출력을 반환합니다.

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

이 예제에서 반환된 배열은 다음과 비슷하며, 외부 `outputs` 개체는 `For_each` 작업 내부에 있는 각 작업 반복의 입력 및 출력을 포함하고 있습니다.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

JSON 개체의 속성 값을 설정하고 업데이트된 개체를 반환합니다. 설정하려는 속성이 없으면 속성이 개체에 추가됩니다. 새 속성을 추가하려면 [addProperty()](#addProperty) 함수를 사용합니다.

```
setProperty(<object>, '<property>', <value>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 해당 속성을 설정할 JSON 개체 |
| <*property*> | 예 | String | 설정할 기존 또는 새 속성의 이름 |
| <*value*> | 예 | 모두 | 지정한 속성에 대해 설정할 값 |
|||||

자식 개체에서 자식 속성을 설정하려면 중첩된 `setProperty()` 호출을 대신 사용합니다. 그렇지 않으면 함수는 출력으로 자식 개체만 반환합니다.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*object*> | 예 | Object | 해당 속성을 설정할 JSON 개체 |
| <*parent-property*> | 예 | String | 자식 속성을 설정하려는 부모 속성의 이름 |
| <*child-property*> | 예 | String | 설정하려는 자식 속성의 이름 |
| <*value*> | 예 | 모두 | 지정한 속성에 대해 설정할 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-object*> | Object | 해당 속성을 설정한 업데이트된 JSON 개체 |
||||

*예제 1*

이 예제에서는 JSON 개체의 `surName` 속성을 설정합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다. 이 함수는 지정된 값을 속성에 할당하고 업데이트된 개체를 반환합니다.

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

다음은 현재 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*예제 2*

이 예제에서는 JSON 개체의 `customerName` 부모 속성에 대한 `surName` 자식 속성을 설정합니다. 이 속성은 [JSON()](#json) 함수를 사용하여 문자열에서 JSON으로 변환됩니다. 이 함수는 지정된 값을 속성에 할당하고 업데이트된 개체를 반환합니다.

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

다음은 현재 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

다음은 업데이트된 JSON 개체입니다.

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

컬렉션 앞의 항목을 제거하고 *다른 모든* 항목을 반환합니다.

```
skip([<collection>], <count>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | Array | 해당 항목을 제거할 컬렉션 |
| <*count*> | 예 | 정수 | 앞에서 제거할 항목 수에 대한 양수 정수 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | 지정한 항목을 제거한 후 업데이트된 컬렉션 |
||||

*예제*

이 예제는 지정한 배열 앞에서 항목 한 개, 숫자 0을 제거합니다.

```
skip(createArray(0, 1, 2, 3), 1)
```

그리고 남은 항목을 포함하는 다음 배열을 반환합니다. `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

원래 문자열에 지정된 구분 기호 문자를 기준으로 하여 하위 문자열을 포함하는 배열(쉼표로 구분됨)을 반환합니다.

```
split('<text>', '<delimiter>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 원래 문자열에 지정된 구분 기호를 기준으로 하여 하위 문자열로 구분할 문자열입니다. |
| <*delimiter*> | 예 | String | 구분 기호로 사용할 원래 문자열의 문자입니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | 원래 문자열의 하위 문자열이 포함된 배열(쉼표로 구분됨)입니다. |
||||

*예제*

이 예제에서는 지정된 문자(구분 기호)를 기준으로 하여 지정한 문자열의 하위 문자열이 포함된 배열을 만듭니다.

```
split('a_b_c', '_')
```

그리고 `["a","b","c"]` 배열을 결과로 반환합니다.

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

타임스탬프에 대한 날의 시작을 반환합니다.

```
startOfDay('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 지정한 타임스탬프이지만 해당 날짜의 0시 마크에서 시작 |
||||

*예제*

이 예제는 이 타임스탬프에 대한 날의 시작을 찾습니다.

```
startOfDay('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

타임스탬프에 대한 시간의 시작을 반환합니다.

```
startOfHour('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 지정한 타임스탬프이지만 시에 대한 0분 마크에서 시작 |
||||

*예제*

이 예제는 이 타임스탬프에 대한 시의 시작을 찾습니다.

```
startOfHour('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

타임스탬프에 대한 월의 시작을 반환합니다.

```
startOfMonth('<timestamp>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 지정한 타임스탬프이지만 0시 마크에 있는 월의 첫 번째 날에 시작 |
||||

*예제 1*

이 예제는 이 타임스탬프에 대한 월의 시작을 반환합니다.

```
startOfMonth('2018-03-15T13:30:30Z')
```

그리고 다음 결과를 반환합니다. `"2018-03-01T00:00:00.0000000Z"`

*예제 2*

다음 예제는 이 타임스탬프에 대해 지정된 형식으로 월의 시작을 반환합니다.

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

그리고 다음 결과를 반환합니다. `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startswith

문자열이 특정 하위 문자열로 시작하는지 검사합니다.
하위 문자열이 발견되면 true를 반환하거나 발견되지 않으면 false를 반환합니다.
이 함수는 대/소문자를 구분하지 않습니다.

```
startsWith('<text>', '<searchText>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 검사할 문자열 |
| <*searchText*> | 예 | String | 찾을 시작 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| true 또는 false  | 부울 | 시작 하위 문자열이 발견되면 true를 반환합니다. 발견되지 않으면 false를 반환합니다. |
||||

*예제 1*

이 예제는 "hello world" 문자열이 "hello" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'hello')
```

그리고 다음 결과를 반환합니다. `true`

*예제 2*

이 예제는 "hello world" 문자열이 "greetings" 하위 문자열로 시작하는지 검사합니다.

```
startsWith('hello world', 'greetings')
```

그리고 다음 결과를 반환합니다. `false`

<a name="string"></a>

### <a name="string"></a>문자열

값에 대한 문자열 버전을 반환합니다.

```
string(<value>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | 모두 | 변환할 값입니다. 이 값이 null 이거나 null로 계산 되 면 값은 빈 문자열 () 값으로 변환 됩니다 `""` . <p><p>예를 들어, 연산자를 사용 하 여 액세스할 수 있는 존재 하지 않는 속성에 문자열 변수를 할당 하는 경우 `?` null 값이 빈 문자열로 변환 됩니다. 그러나 null 값을 비교 하는 것은 빈 문자열을 비교 하는 것과는 다릅니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*string-value*> | String | 지정 된 값의 문자열 버전입니다. *값* 매개 변수가 null 이거나 null로 계산 되는 경우이 값은 빈 문자열 () 값으로 반환 됩니다 `""` . |
||||





*예제 1*

이 예제는 이 숫자에 대한 문자열 버전을 만듭니다.

```
string(10)
```

그리고 다음 결과를 반환합니다. `"10"`

*예제 2*

이 예제는 지정한 JSON 개체에 대한 문자열을 만들고 백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다.

```
string( { "name": "Sophie Owen" } )
```

그리고 다음 결과를 반환합니다. `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

첫 번째 숫자에서 두 번째 숫자를 뺀 결과를 반환합니다.

```
sub(<minuend>, <subtrahend>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | 예 | 정수 또는 부동 소수점 수 | *감수*를 뺄 수 |
| <*subtrahend*> | 예 | 정수 또는 부동 소수점 수 | *피감수*에서 뺄 수 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*result*> | 정수 또는 부동 소수점 수 | 첫 번째 숫자에서 두 번째 숫자를 뺀 결과 |
||||

*예제*

이 예제는 첫 번째 숫자에서 두 번째 숫자를 뺍니다.

```
sub(10.3, .3)
```

그리고 다음 결과를 반환합니다. `10`

<a name="substring"></a>

### <a name="substring"></a>substring

지정한 위치 또는 인덱스에서 시작하여 문자열에서 문자를 반환합니다.
인덱스 값은 숫자 0으로 시작합니다.

```
substring('<text>', <startIndex>, <length>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 해당 문자를 원하는 문자열 |
| <*startIndex*> | 예 | 정수 | 시작 위치 또는 인덱스 값으로 사용하려는 0 이상의 양수 |
| <*length*> | 예 | 정수 | 하위 문자열에 원하는 문자의 양수 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | 원본 문자열의 지정한 인덱스 위치에서 시작하는 지정한 수의 문자를 포함하는 하위 문자열 |
||||

*예제*

이 예제는 인덱스 값 6에서 시작하여 지정한 문자열에서 문자 하위 문자열 5개를 만듭니다.

```
substring('hello world', 6, 5)
```

그리고 다음 결과를 반환합니다. `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

타임스탬프에서 시간 단위 수를 뺍니다.
[getPastTime](#getPastTime)도 함께 참조하세요.

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프를 포함하는 문자열 |
| <*interval*> | 예 | 정수 | 뺄 지정한 시간 단위 수 |
| <*timeUnit*> | 예 | String | *interval*을 사용하는 시간 단위: “초”, “분”, “시간”, “일”, “주”, “월”, “년” |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | 타임스탬프 - 지정한 시간 단위 수 |
||||

*예제 1*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

그리고 다음 결과를 반환합니다. `"2018-01-01T00:00:00:0000000Z"`

*예제 2*

이 예제는 이 타임스탬프에서 1일을 뺍니다.

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

그리고 선택적 "D" 형식을 사용하여 다음 결과를 반환합니다. `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

컬렉션 앞에서 항목을 반환합니다.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection*> | 예 | 문자열 또는 배열 | 해당 항목을 원하는 컬렉션 |
| <*count*> | 예 | 정수 | 앞에서 제거할 항목 수에 대한 양수 정수 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*subset*> or [<*subset*>] | 각각 문자열 또는 배열 | 원본 컬렉션의 앞에서 가져온 지정한 수의 항목을 포함하는 문자열 또는 배열 |
||||

*예제*

아래 예제는 이 컬렉션의 앞에서 지정한 수의 항목을 가져옵니다.

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

그리고 다음 결과를 반환합니다.

* 첫 번째 예제: `"abc"`
* 두 번째 예제: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

0001년 1월 1일 12:00:00 자정(또는 C#은 DateTime.Ticks)부터 지정된 타임스탬프까지 100나노초 간격으로 계산한 틱 수를 반환합니다. 자세한 내용은 [DateTime.Ticks 속성(시스템)](/dotnet/api/system.datetime.ticks?view=netframework-4.7.2#remarks) 토픽을 참조하세요.

```
ticks('<timestamp>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | 예 | String | 타임스탬프에 대한 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*ticks-number*> | 정수 | 지정한 타임스탬프 이후 틱 수 |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

문자열을 소문자 형식으로 반환합니다. 문자열의 문자에 소문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toLower('<text>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 소문자 형식으로 반환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | 소문자 형식의 원래 문자열 |
||||

*예제*

이 예제는 이 문자열을 소문자로 변환합니다.

```
toLower('Hello World')
```

그리고 다음 결과를 반환합니다. `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

문자열을 대문자 형식으로 반환합니다. 문자열의 문자에 대문자 버전이 없으면 반환된 문자열에 해당 문자가 변경되지 않고 포함됩니다.

```
toUpper('<text>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 대문자 형식으로 반환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | 대문자 형식의 원래 문자열 |
||||

*예제*

이 예제는 이 문자열을 대문자로 변환합니다.

```
toUpper('Hello World')
```

그리고 다음 결과를 반환합니다. `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>트리거

런타임 시 트리거의 출력 또는 식에 할당할 수 있는 다른 JSON 이름-값 쌍을 반환합니다.

* 트리거의 입력 내부에서 이 함수는 이전 실행의 출력을 반환합니다.

* 트리거의 조건 내부에서 이 함수는 현재 실행의 출력을 반환합니다.

기본적으로 이 함수는 전체 트리거 개체를 참조하지만, 선택적으로 해당 값을 원하는 속성을 지정할 수 있습니다.
또한 이 함수의 줄인 버전을 사용할 수 있습니다. [triggerOutputs()](#triggerOutputs) 및 [triggerBody()](#triggerBody)를 참조하세요.

```
trigger()
```

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | 런타임 시 트리거의 출력 |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

런타임 시 트리거의 `body` 출력을 반환합니다.
`trigger().outputs.body`를 줄인 표현입니다.
[trigger()](#trigger)를 참조하세요.

```
triggerBody()
```

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | String | 트리거의 `body` 출력 |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

트리거의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 값을 가진 배열을 반환합니다.

```
triggerFormDataMultiValues('<key>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*key*> | 예 | String | 해당 값을 원하는 키의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | 지정한 키와 일치하는 모든 값을 가진 배열 |
||||

*예제*

이 예제는 RSS 트리거의 양식 데이터 또는 양식 인코딩된 출력의 "feedUrl" 키 값에서 배열을 만듭니다.

```
triggerFormDataMultiValues('feedUrl')
```

그리고 이 배열을 예제 결과로 반환합니다. `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

트리거의 *양식 데이터* 또는 *양식 인코딩된* 출력의 키 이름과 일치하는 단일 값을 가진 문자열을 반환합니다.
함수가 일치하는 항목을 두 개 이상 찾으면 함수에서 오류가 발생합니다.

```
triggerFormDataValue('<key>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*key*> | 예 | String | 해당 값을 원하는 키의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*key-value*> | String | 지정한 키의 값 |
||||

*예제*

이 예제는 RSS 트리거의 양식 데이터 또는 양식 인코딩된 출력의 "feedUrl" 키 값에서 문자열을 만듭니다.

```
triggerFormDataValue('feedUrl')
```

그리고 이 문자열을 예제 결과로 반환합니다. `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

여러 부분으로 구성된 트리거의 출력에서 특정 부분에 대한 본문을 반환합니다.

```
triggerMultipartBody(<index>)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*index*> | 예 | 정수 | 원하는 부분에 대한 인덱스 값 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*body*> | String | 트리거의 다중 부분 출력에서 지정한 부분에 대한 본문 |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

런타임 시 트리거 출력 또는 다른 JSON 이름-값 쌍에서 값을 반환합니다.
`trigger().outputs`를 줄인 표현입니다.
[trigger()](#trigger)를 참조하세요.

```
triggerOutputs()
```

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*trigger-output*> | String | 런타임 시 트리거의 출력  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

문자열에서 선행 및 후행 공백을 제거하고 업데이트된 문자열을 반환합니다.

```
trim('<text>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*text*> | 예 | String | 제거할 선행 및 후행 공백이 있는 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | 선행 또는 후행 공백이 없는 원래 문자열에 대한 업데이트된 버전 |
||||

*예제*

이 예제는 문자열 " Hello World  "에서 선행 및 후행 공백을 제거합니다.

```
trim(' Hello World  ')
```

그리고 다음 결과를 반환합니다. `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

지정한 컬렉션에서 *모든* 항목이 있는 컬렉션을 반환합니다.
결과에 나타내기 위해 항목이 이 함수에 전달된 모든 컬렉션에 나타날 수 있습니다. 같은 이름을 가진 항목이 한 개 이상이면 해당 이름의 마지막 항목이 결과에 나타납니다.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | 예 | 배열 또는 개체, 그러나 둘 다는 아님 | *모든* 항목을 원하는 컬렉션 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | 각각 배열 또는 개체 | 지정한 컬렉션의 모든 항목을 포함하는 컬렉션 - 중복 없음 |
||||

*예제*

이 예제는 다음 컬렉션에서 *모든* 항목을 가져옵니다.

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

그리고 다음 결과를 반환합니다. `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

URL 안전하지 않은 문자를 이스케이프 문자로 바꾸어 문자열에 대한 URI(Uniform Resource Identifier) 인코딩된 버전을 반환합니다.
[encodeUriComponent()](#encodeUriComponent) 대신에 이 함수를 사용합니다.
두 함수 모두 같은 방법으로 작동하지만 `uriComponent()`를 사용하는 것이 좋습니다.

```
uriComponent('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | URI 인코딩된 형식으로 변환할 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | 이스케이프 문자로 URI 인코딩된 문자열 |
||||

*예제*

이 예제는 이 문자열에 대한 URI 인코딩된 버전을 만듭니다.

```
uriComponent('https://contoso.com')
```

그리고 다음 결과를 반환합니다. `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

URI(Uniform Resource Identifier) 구성 요소에 대한 이진 버전을 반환합니다.

```
uriComponentToBinary('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 URI 인코딩된 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | URI 인코딩된 문자열에 대한 이진 버전 이진 콘텐츠는 base64로 인코딩되며 `$content`에 의해 표시됩니다. |
||||

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 이진 버전을 만듭니다.

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다.

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

URI(Uniform Resource Identifier) 인코딩된 문자열에 대한 문자열 버전을 반환하며, 결과적으로 URI 인코딩된 문자열을 디코딩합니다.

```
uriComponentToString('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 디코딩할 URI 인코딩된 문자열 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | URI 인코딩된 문자열에 대한 디코딩된 버전 |
||||

*예제*

이 예제는 이 URI 인코딩된 문자열에 대한 디코딩된 버전을 만듭니다.

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

그리고 다음 결과를 반환합니다. `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

URI(Uniform Resource Identifier)에 대한 `host` 값을 반환합니다.

```
uriHost('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `host` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*host-value*> | String | 지정한 URI에 대한 `host` 값 |
||||

*예제*

이 예제는 이 URI에 대한 `host` 값을 찾습니다.

```
uriHost('https://www.localhost.com:8080')
```

그리고 다음 결과를 반환합니다. `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

URI(Uniform Resource Identifier)에 대한 `path` 값을 반환합니다.

```
uriPath('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `path` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*path-value*> | String | 지정한 URI에 대한 `path` 값입니다. `path`가 값을 포함하지 않은 경우 "/" 문자를 반환합니다. |
||||

*예제*

이 예제는 이 URI에 대한 `path` 값을 찾습니다.

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

URI(Uniform Resource Identifier)에 대한 `path` 및 `query` 값을 반환합니다.

```
uriPathAndQuery('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `path` 및 `query` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*path-query-value*> | String | 지정한 URI에 대한 `path` 및 `query` 값입니다. `path`에 값을 지정하지 않은 경우 "/" 문자를 반환합니다. |
||||

*예제*

이 예제는 이 URI에 대한 `path` 및 `query`값을 찾습니다.

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

URI(Uniform Resource Identifier)에 대한 `port` 값을 반환합니다.

```
uriPort('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `port` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*port-value*> | 정수 | 지정한 URI에 대한 `port` 값입니다. `port`에 값을 지정하지 않은 경우 프로토콜에 대한 기본 포트를 반환합니다. |
||||

*예제*

이 예제는 이 URI에 대한 `port` 값을 반환합니다.

```
uriPort('http://www.localhost:8080')
```

그리고 다음 결과를 반환합니다. `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

URI(Uniform Resource Identifier)에 대한 `query` 값을 반환합니다.

```
uriQuery('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `query` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*query-value*> | String | 지정한 URI에 대한 `query` 값 |
||||

*예제*

이 예제는 이 URI에 대한 `query` 값을 반환합니다.

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

URI(Uniform Resource Identifier)에 대한 `scheme` 값을 반환합니다.

```
uriScheme('<uri>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*uri*> | 예 | String | 해당 `scheme` 값을 원하는 URI |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*scheme-value*> | String | 지정한 URI에 대한 `scheme` 값 |
||||

*예제*

이 예제는 이 URI에 대한 `scheme` 값을 반환합니다.

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

그리고 다음 결과를 반환합니다. `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

현재 타임스탬프를 반환합니다.

```
utcNow('<format>')
```

선택적으로 <*format*> 매개 변수를 사용하여 다른 형식을 지정할 수 있습니다.


| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*format*> | 예 | String | [단일 형식 지정자](/dotnet/standard/base-types/standard-date-and-time-format-strings) 또는 [사용자 지정 형식 패턴](/dotnet/standard/base-types/custom-date-and-time-format-strings)입니다. 타임스탬프의 기본 형식은 ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings)(yyyy-MM-ddTHH:mm:ss:fffffffK)입니다. 이 형식은 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)을 준수하며 표준 시간대 정보를 보존합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | 현재 날짜 및 시간 |
||||

*예제 1*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다.
이 예제는 현재 타임스탬프를 가져옵니다.

```
utcNow()
```

그리고 다음 결과를 반환합니다. `"2018-04-15T13:00:00.0000000Z"`

*예제 2*

오늘이 2018년 4월 15일 오후 1시 00분 00초라고 가정합니다.
이 예제는 선택적 "D" 형식을 사용하여 현재 타임스탬프를 가져옵니다.

```
utcNow('D')
```

그리고 다음 결과를 반환합니다. `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variables

지정한 변수에 대한 값을 반환합니다.

```
variables('<variableName>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | 예 | String | 해당 값을 원하는 변수의 이름 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*variable-value*> | 모두 | 지정한 변수의 값 |
||||

*예제*

"numItems" 변수에 대한 현재 값이 20이라고 가정합니다.
이 예제는 이 변수에 대한 정수 값을 가져옵니다.

```
variables('numItems')
```

그리고 다음 결과를 반환합니다. `20`

<a name="workflow"></a>

### <a name="workflow"></a>워크플로

런타임 동안 워크플로 자체에 대한 모든 세부 정보를 반환합니다.

```
workflow().<property>
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*property*> | 예 | String | 해당 값을 원하는 워크플로 속성의 이름 <p>워크플로 개체는 다음 속성을 갖습니다. **name**, **type**, **id**, **location** 및 **run**. **run** 속성 값은 다음 속성을 갖는 개체이기도 합니다. **name**, **type** 및 **id**. |
|||||

*예제*

이 예제는 워크플로의 현재 실행에 대한 이름을 반환합니다.

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>Xml

JSON 개체를 포함하는 문자열에 대한 XML 버전을 반환합니다.

```
xml('<value>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*value*> | 예 | String | 변환할 JSON 개체를 가진 문자열 <p>JSON 개체는 배열일 수 없는 루트 속성 한 개만 가져야 합니다. <br>백슬래시 문자(\\)를 따옴표(")에 대한 이스케이프 문자로 사용합니다. |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-version*> | Object | 지정한 문자열 또는 JSON 개체에 대한 인코딩된 XML |
||||

*예제 1*

이 예제는 이 문자열에 대한 XML 버전을 만들며, 이 문자열은 JSON 개체를 포함합니다.

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

그리고 다음 결과 XML을 반환합니다.

```xml
<name>Sophia Owen</name>
```

*예제 2*

이 JSON 개체가 있다고 가정합니다.

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

이 예제는 이 JSON 개체를 포함하는 문자열에 대한 XML을 만듭니다.

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

그리고 다음 결과 XML을 반환합니다.

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

XPath(XML 경로 언어) 식과 일치하는 노드 또는 값에 대한 XML을 확인하고 일치하는 노드 또는 값을 반환합니다. XPath 식 또는 단순히 "XPath"는 XML 콘텐츠에서 노드를 선택하고 값을 계산할 수 있도록 XML 문서 구조를 탐색하는 데 도움이 됩니다.

```
xpath('<xml>', '<xpath>')
```

| 매개 변수 | 필수 | Type | Description |
| --------- | -------- | ---- | ----------- |
| <*xml*> | 예 | 모두 | XPath 식 값과 일치하는 노드 또는 값을 검색하기 위한 XML 문자열 |
| <*xpath*> | 예 | 모두 | 일치하는 XML 노드 또는 값을 찾는 데 사용한 XPath 식 |
|||||

| 반환 값 | Type | Description |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | 단일 노드만이 지정한 XPath 식과 일치하는 경우 XML 노드 |
| <*value*> | 모두 | 단일 값만이 지정한 XPath 식과 일치하는 경우 XML 노드의 값 |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>또는 </br>[<*value1*>, <*value2*>, ...] | Array | 지정한 XPath 식과 일치하는 XML 노드 또는 값을 가진 배열 |
||||

*예제 1*

이 XML 문자열이 있다고 가정 합니다 `'items'` . 

`"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

이 예에서는 XPath 식를 전달 `'/produce/item/name'` 하 여 XML 문자열의 노드와 일치 하는 노드를 찾고 `<name></name>` `'items'` 해당 노드 값이 포함 된 배열을 반환 합니다.

`xpath(xml(parameters('items')), '/produce/item/name')`

또한이 예제에서는 [parameters ()](#parameters) 함수를 사용 하 여 xml 문자열을 가져온 `'items'` 다음 [xml ()](#xml) 함수를 사용 하 여 문자열을 xml 형식으로 변환 합니다.

다음은 `<name></name`과 일치하는 노드를 가진 결과 배열입니다.

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*예제 2*

예제 1에 따라이 예제에서는 XPath 식를 전달 하 여 `'/produce/item/name[1]'` 요소의 자식인 첫 번째 요소를 찾습니다 `name` `item` .

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

결과는 다음과 같습니다. `Gala`

*예 3*

예제 1에서 다음 예제에서는 XPath 식를 전달 `'/produce/item/name[last()]'` 하 여 요소의 자식인 마지막 요소를 찾습니다 `name` `item` .

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

결과는 다음과 같습니다. `Honeycrisp`

*예제 4*

이 예제에서는 `items` XML 문자열에 및 속성도 포함 되어 있다고 가정 합니다 `expired='true'` `expired='false'` .

`"<?xml version="1.0"?> <produce> <item> <name expired='true'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name expired='false'>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

이 예에서는 XPath 식를 전달 `'//name[@expired]'` 하 여 특성이 있는 모든 요소를 찾습니다 `name` `expired` .

`xpath(xml(parameters('items')), '//name[@expired]')`

결과는 다음과 같습니다. `[ Gala, Honeycrisp ]`

*예제 5*

이 예에서는 `items` XML 문자열에이 특성만 포함 되어 있다고 가정 합니다 `expired = 'true'` .

`"<?xml version="1.0"?> <produce> <item> <name expired='true'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

이 예에서는 XPath 식를 전달 `'//name[@expired = 'true']'` 하 여 `name` 특성이 인 모든 요소를 찾습니다 `expired = 'true'` .

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

결과는 다음과 같습니다. `[ Gala ]`

*예제 6*

이 예에서는 `items` XML 문자열에 다음과 같은 특성도 포함 되어 있다고 가정 합니다. 

* `expired='true' price='12'`
* `expired='false' price='40'`

`"<?xml version="1.0"?> <produce> <item> <name expired='true' price='12'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name expired='false' price='40'>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

이 예에서는 XPath 식를 전달 하 여이 있는 `'//name[price>35]'` 모든 요소를 찾습니다 `name` `price > 35` .

`xpath(xml(parameters('items')), '//name[price>35]')`

결과는 다음과 같습니다. `Honeycrisp`

*예제 7*

이 예에서는 `items` XML 문자열이 예 1의 경우와 동일 하다 고 가정 합니다.

`"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

이 예에서는 노드와 일치 하는 노드를 찾은 `<count></count>` 다음 함수를 사용 하 여 해당 노드 값을 추가 합니다 `sum()` .

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

결과는 다음과 같습니다. `30`

*예제 8*

이 예제에서는 XML 문서 네임 스페이스를 포함 하는 XML 문자열을 가정 합니다 `xmlns="http://contoso.com"` .

`"<?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>"`

이러한 식은 XPath 식 `/*[name()="file"]/*[name()="location"]` 또는 `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]` 를 사용 하 여 노드와 일치 하는 노드를 찾습니다 `<location></location>` . 다음 예에서는 논리 앱 디자이너나 식 편집기에서 사용 하는 구문을 보여 줍니다.

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

다음은 `<location></location>` 노드와 일치하는 결과 노드입니다. 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> 코드 보기에서 작업 하는 경우 백슬래시 문자 ()를 사용 하 여 큰따옴표 (")를 이스케이프 합니다 \\ . 
> 예를 들어 식을 JSON 문자열로 직렬화할 때에는 이스케이프 문자를 사용해야 합니다. 
> 그러나 논리 앱 디자이너나 식 편집기에서 작업 하는 경우 백슬래시 문자는 기본 정의에 자동으로 추가 되므로 큰따옴표를 이스케이프할 필요가 없습니다. 예를 들면 다음과 같습니다.
> 
> * 코드 보기: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * 식 편집기: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*예제 9*

예 8에서 다음 예에서는 XPath 식를 사용 하 여 `'string(/*[name()="file"]/*[name()="location"])'` 노드의 값을 찾습니다 `<location></location>` .

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

결과는 다음과 같습니다. `Paris`

## <a name="next-steps"></a>다음 단계

[워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 알아보기
