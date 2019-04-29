---
title: 데이터 기반 스타일 식에서 Azure Maps 웹 SDK | Microsoft Docs
description: Azure Maps 웹 SDK에서 데이터 기반 스타일 식을 사용 하는 방법입니다.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: 3b234ca37783fe557baf307f198de9636b06a382
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60904978"
---
# <a name="data-driven-style-expressions-web-sdk"></a>데이터 기반 스타일 식 (웹 SDK)

식을 사용 하면 각 셰이프를 데이터 원본에 정의 된 속성을 관찰 하는 스타일 옵션에 비즈니스 논리를 적용할 수 있습니다. 식은 데이터 원본 또는 계층에서 데이터를 필터링도 사용할 수 있습니다. 식은 if 문 같은 조건부 논리, 구성 되며;를 사용 하 여 데이터를 조작에 사용할 수도 있습니다. 문자열, 수치 연산 및 논리 연산자입니다. 

데이터 기반 스타일 스타일 관련 비즈니스 논리를 구현 하는 데 필요한 코드의 양을 줄일 수 있습니다. 레이어를 사용 하면 UI 스레드에서 비즈니스 논리를 평가에 비해 향상 된 성능을 제공 하는 별도 스레드에서 렌더링 시 식이 계산 됩니다.

다음 비디오는 Azure Maps 웹 SDK에서 데이터 기반 스타일의 개요를 제공 합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

식은은 JSON 배열로 표시 됩니다. 배열 식의 첫 번째 요소는 식 연산자의 이름을 지정 하는 문자열입니다. 예를 들어, "+" 또는 "사례"입니다. 다음 요소 (있는 경우)는 식 인수입니다. 각 인수는 리터럴 값 (문자열, 숫자, 부울, 또는 `null`), 또는 다른 식 배열입니다. 다음 의사 코드 식 기본 구조를 정의합니다. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps 웹 SDK는 다양 한 유형의 자체적으로 또는 다른 식과 함께에서 사용할 수 있는 식 지원 합니다.

| 식의 형식 | 설명 |
|---------------------|-------------|
| [부울 식](#boolean-expressions) | 부울 식에 부울 비교를 평가 하기 위한 부울 연산자 식의 집합을 제공 합니다. |
| [색 식](#color-expressions) | 색 식 쉽게 만들고 색 값을 조작 합니다. |
| [조건식](#conditional-expressions) | 조건식 if 문 같은 논리 작업을 제공 합니다. |
| [데이터 식](#data-expressions) | 속성의 데이터를 기능에 대 한 액세스를 제공합니다. |
| [보간 및 단계 식](#interpolate-and-step-expressions) | 보간 및 단계 식은 보간된 곡선 또는 함수를 함께 값을 계산 하려면 사용할 수 있습니다. |
| [계층의 특정 식](#layer-specific-expressions) | 단일 계층에만 적용 되는 특별 한 식입니다. |
| [수학 식](#math-expressions) | 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다. |
| [문자열 연산자 식](#string-operator-expressions) | 문자열 연산자 식 문자열을 연결 하는 대/소문자 변환 등의 변환 작업을 수행 합니다. |
| [Type 식](#type-expressions) | Type 식 테스트 및 문자열, 숫자 및 부울 값과 같은 다른 데이터 형식 변환에 대 한 도구를 제공 합니다. |
| [변수 바인딩 식](#variable-binding-expressions) | 변수 바인딩 식의 계산 결과를 변수에 저장할 수 있도록 및 다른 곳에서 참조 식에 여러 번 저장 된 값을 다시 계산할 필요 없이 합니다. |
| [확대/축소 식](#zoom-expression) | 렌더링 시 지도의 현재 확대/축소 수준을 검색합니다. |

이 문서의 모든 예제에서는 여러 유형의 식 사용할 수는 다양 한 방법으로 보여 주기 위해 다음 기능을 사용 합니다. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {     
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red"
    }
}
```

## <a name="data-expressions"></a>데이터 식

데이터 식은 속성 데이터를 기능에서에 대 한 액세스를 제공합니다. 

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['at', number, array]` | object | 배열에서 항목을 검색 합니다. |
| `['geometry-type']` | 문자열 | 기 하 도형 형식을 기능을 가져옵니다. Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon. |
| `['get', string]` | 값 | 현재 기능의 속성에서 속성 값을 가져옵니다. 요청된 된 속성이 없는 경우 null을 반환 합니다. |
| `['get', string, object]` | 값 | 지정된 된 개체의 속성에서 속성 값을 가져옵니다. 요청된 된 속성이 없는 경우 null을 반환 합니다. |
| `['has', string]` | 부울 | 기능 속성 속성이 지정 된 경우를 결정 합니다. |
| `['has', string, object]` | 부울 | 개체의 속성을 지정된 된 속성이 있는지 확인 합니다. |
| `['id']` | 값 | 있는 경우 기능의 ID를 가져옵니다. |
| `['length', string | array]` | number | 문자열 또는 배열의 길이 가져옵니다. |

**예**

사용 하 여 식에 직접 기능 속성에 액세스할 수 있습니다는 `get` 식입니다. 다음 예제에서는 거품형 계층의 색 속성을 지정 하려면 기능 "zoneColor" 값을 사용 합니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

모든 지점 기능이 있는 경우 위의 예제는 정상적으로 작동 합니다 `zoneColor` 속성에 해당 하지 않을 경우, 하지만 색 것으로 대체 됩니다 "black"입니다. 대체 (fallback) 색을 수정 하는 `case` 식과 함께에서 사용할 수는 `has` 속성이 있으면이 고 대신 대체 색을 반환 하지 않는 경우를 확인할 식입니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

거품형 및 기호 레이어는 기본적으로 데이터 원본의 모든 셰이프의 좌표를 렌더링 합니다. 선 또는 다각형의 꼭 짓 점을 강조 표시 하려면이 수행할 수 있습니다. `filter` 기 하 도형 형식을 사용 하 여 렌더링 기능을 제한 하는 계층의 옵션을 사용할 수 있습니다를 `['geometry-type']` 식을 부울 식입니다. 다음 예제에서는 제한 되도록 거품형 계층 `Point` 기능 렌더링 됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

다음 예제를 사용 하면 둘 다 `Point` 고 `MultiPoint` 렌더링 되어야 하는 기능입니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

마찬가지로, 다각형의 개요 줄 계층에서 렌더링 됩니다. 선 계층에서이 동작을 사용 하지 않으려면만 허용 하는 필터를 추가 `LineString` 고 `MultiLineString` 기능입니다.  

## <a name="math-expressions"></a>수학 식

수학 식을 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다.

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 지정된 된 숫자의 합계를 계산 합니다. |
| `['-', number]` | number | 지정된 된 수 만큼 0을 뺍니다. |
| `['-', number, number]` | number | 두 번째 숫자에서 첫 번째 숫자를 뺍니다. |
| `['*', number, number, …]` | number | 함께 지정된 된 숫자를 곱합니다. |
| `['/', number, number]` | number | 두 번째 숫자 첫 번째 숫자를 나눕니다. |
| `['%', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눌 때 나머지를 계산 합니다. |
| `['^', number, number]` | number | 두 번째 숫자의 거듭제곱을 첫 번째 값의 값을 계산 합니다. |
| `['abs', number]` | number | 지정된 된 숫자의 절대값을 계산 합니다. |
| `['acos', number]` | number | 지정된 된 숫자의 아크코사인을 계산합니다. |
| `['asin', number]` | number | 지정된 된 숫자의 아크사인을 계산합니다. |
| `['atan', number]` | number | 지정된 된 숫자의 아크탄젠트를 계산 합니다. |
| `['ceil', number]` | number | 전체를 다음 정수로 반올림 합니다. |
| `['cos', number]` | number | 지정된 된 숫자의 cos를 계산합니다. |
| `['e']` | number | 수학 상수를 반환 합니다 `e`합니다. |
| `['floor', number]` | number | 이전 정수는 숫자를 반올림합니다. |
| `['ln', number]` | number | 지정된 된 숫자의 자연 로그를 계산 합니다. |
| `['ln2']` | number | 수학 상수를 반환 합니다 `ln(2)`합니다. |
| `['log10', number]` | number | 지정된 된 숫자의 base 10 로그를 계산 합니다. |
| `['log2', number]` | number | 지정된 된 숫자의 기본 두 로그를 계산 합니다. |
| `['max', number, number, …]` | number | 숫자의 지정된 된 집합의 최대 수를 계산합니다. |
| `['min', number, number, …]` | number | 숫자의 지정된 된 집합의 최소 수를 계산합니다. |
| `['pi']` | number | 수학 상수를 반환 합니다 `PI`합니다. |
| `['round', number]` | number | 가장 가까운 정수로 반올림 합니다. 중간 값은 0에서 멀어지는 쪽 반올림 됩니다. 예를 들어 `['round', -1.5]` -2로 계산 합니다. |
| `['sin', number]` | number | 지정된 된 숫자의 사인을 계산 합니다. |
| `['sqrt', number]` | number | 지정된 된 숫자의 제곱근을 계산 합니다. |
| `['tan', number]` | number | 지정된 된 숫자의 탄젠트를 계산 합니다. |
## <a name="boolean-expressions"></a>부울 식

부울 식에 부울 비교를 평가 하기 위한 부울 연산자 식의 집합을 제공 합니다.

값을 비교할 때 비교는 엄격 하 게 형식화 됩니다. 다른 형식의 값은 항상 동일 하지 않은 간주 됩니다. 형식을 구문 분석 시 다를 수에 알려져 있는 경우 잘못 된 것으로 간주 됩니다 및 구문 분석 오류를 생성 합니다. 

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['! ', boolean]` | 부울 | 논리 부정입니다. 반환 `true` 입력이 `false`, 및 `false` 입력이 `true`합니다. |
| `['!= ', value, value]` | 부울 | 반환 `true` 입력된 값은 같지 않으면 `false` 그렇지 않은 경우. |
| `['<', value, value]` | 부울 | 반환 `true` 첫 번째 입력이 두 번째 보다 엄격 하 게 작으면 `false` 그렇지 않은 경우. 인수는 문자열 및/또는 두 숫자를 할 필요가 있습니다. |
| `['<=', value, value]` | 부울 | 반환 `true` 첫 번째 입력에서 두 번째 보다 작거나 같은 경우 `false` 그렇지 않은 경우. 인수는 문자열 및/또는 두 숫자를 할 필요가 있습니다. |
| `['==', value, value]` | 부울 | 반환 `true` 입력된 값이 같으면, `false` 그렇지 않은 경우. 인수는 문자열 및/또는 두 숫자를 할 필요가 있습니다. |
| `['>', value, value]` | 부울 | 반환 `true` 첫 번째 입력이 두 번째 보다 엄격 하 게 크면 `false` 그렇지 않은 경우. 인수는 문자열 및/또는 두 숫자를 할 필요가 있습니다. |
| `['>=' value, value]` | 부울 | 반환 `true` 첫 번째 입력이 두 번째 보다 크거나 `false` 그렇지 않은 경우. 인수는 문자열 및/또는 두 숫자를 할 필요가 있습니다. |
| `['all', boolean, boolean, …]` | 부울 | 반환 `true` 모든 입력 되 면 `true`, `false` 그렇지 않은 경우. |
| `['any', boolean, boolean, …]` | 부울 | 반환 `true` 입력 인지 `true`, `false` 그렇지 않은 경우. |

## <a name="conditional-expressions"></a>조건부 식

조건식 if 문 같은 논리 작업을 제공 합니다.

다음 식이 입력된 데이터에 대해 조건부 논리 작업을 수행 합니다. 예를 들어, 합니다 `case` 식으로 제공 하는 동안 "/ 다음/else" 논리를 `match` "switch 문이" 같은 식은 합니다. 

### <a name="case-expression"></a>Case 식

`case` 식은 if (/ 다음/else) 논리 등을 제공 하는 조건식의 형식입니다. 이 유형의 식 부울 조건 목록을 단계별로 안내 하 고 true 인 첫 번째 부울 조건의 출력 값을 반환 합니다.

다음 의사 코드의 구조를 정의 합니다 `case` 식입니다. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**예제**

찾을 때까지 계산 되는 다음 예제에서는 다양 한 부울 조건을 안내 `true`, 및 관련 값을 반환 합니다. 에 부울 조건이 없는 경우 `true`, 대체 (fallback) 값이 반환 됩니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>일치 식

`match` 식은 switch 같은 논리를 제공 하는 조건식의 형식입니다. 입력 모든 식일 수 있습니다와 같은 `['get', 'entityType']` 문자열 또는 숫자를 반환 하는 합니다. 각 레이블에 단일 리터럴 값 또는 모든 문자열이 나 모든 숫자 값 이어야 합니다 하는 리터럴 값의 배열 이어야 합니다. 입력에 일치 하는 배열에에서 있는 값의 경우와 일치 합니다. 각 레이블에 고유 해야 합니다. 입력된 형식에는 레이블의 형식과 일치 하지 않습니다, 경우 결과 대체 값이 됩니다.

다음 의사 코드의 구조를 정의 합니다 `match` 식입니다. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**예**

다음 예제에서는 살펴봅니다는 `entityType` 거품형 계층의 지점 기능 속성 일치 항목을 검색 합니다. 일치 하는 항목을 찾으면는 지정 대체 값을 반환 하거나 값이 반환 됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

다음 예제에서는 동일한 값을 모두 반환 해야 하는 레이블 집합을 나열 하려면 배열을 사용 합니다. 각 레이블에 개별적으로 나열 하는 보다 훨씬 더 효율적입니다. 이 예제의 경우는 `entityType` 속성이 "음식점" 또는 "grocery_store" 인 "빨강" 반환 됩니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

### <a name="coalesce-expression"></a>Coalesce 식

`coalesce` 식을 첫 번째 null이 아닌 값을 가져오고 해당 값을 반환 될 때까지 식의 집합을 단계별로 안내 합니다. 

다음 의사 코드의 구조를 정의 합니다 `coalesce` 식입니다. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**예제**

다음 예에서는 `coalesce` 설정 하는 식의 `textField` 기호 계층의 옵션입니다. 경우는 `title` 속성이 기능 또는 집합에서 누락 되었습니다 `null`, 식 찾고자 시도 됩니다는 `subtitle` 속성인 경우 해당 누락 또는 `null`, 빈 문자열로 다음 대체 됩니다. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

## <a name="type-expressions"></a>Type 식

Type 식 테스트 및 문자열, 숫자 및 부울 값과 같은 다른 데이터 형식 변환에 대 한 도구를 제공 합니다.

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 배열 \| 개체 | 배열 또는 개체 리터럴 값을 반환합니다. 이 식을 사용 하 여 배열 또는 개체를 식으로 평가 되지 않도록 합니다. 이 변수 배열 또는 개체를 식에서 반환 해야 하는 경우에 필요 합니다. |
| `['to-boolean', value]` | 부울 | 입력된 값을 부울으로 변환 합니다. 결과 `false` 입력이 빈 문자열인 경우 `0`를 `false`합니다 `null`, 또는 `NaN`이 고 그렇지 않으면 해당 `true`합니다. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | 색 | 입력 색으로 변환합니다. 여러 값을 제공 하는 경우 첫 번째 성공적인 변환 가져올 때까지 각 순서 대로 평가 됩니다. 입력을 변환할 수 있는, 경우 식 오류입니다. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 가능한 경우 입력된 값을 숫자로 변환합니다. 입력이 `null` 또는 `false`, 결과 0입니다. 입력이 `true`, 결과 1입니다. 입력이 문자열을 사용 하 여 숫자 변환 됩니다 합니다 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) ECMAScript 언어 사양 함수는 문자열입니다. 여러 값을 제공 하는 경우 첫 번째 성공적인 변환 가져올 때까지 각 순서 대로 평가 됩니다. 입력을 변환할 수 있는, 경우 식 오류입니다. |
| `['to-string', value]` | 문자열 | 입력된 값을 문자열로 변환합니다. 입력이 `null`, 결과 `""`합니다. 결과 부울 입력을 사용 하는 경우 `"true"` 또는 `"false"`합니다. 입력이 숫자를 사용 하 여 문자열로 변환 됩니다 합니다 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) ECMAScript 언어 사양 함수 번호입니다. 입력 색 이면 변환할 CSS RGBA 색 문자열 `"rgba(r,g,b,a)"`합니다. 입력이 사용 하 여 문자열로 변환 하는 고, 그렇지 합니다 [JSON.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) ECMAScript 언어 사양 함수입니다. |
| `['typeof', value]` | 문자열 | 지정된 된 값의 형식을 설명 하는 문자열을 반환 합니다. |

> [!TIP]
> 라는 오류 메시지가 나타나는 경우 `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` 나타나는 브라우저 콘솔에서 해당 문자열의 첫 번째 값에 대 한 되지 않은 배열을 포함 하는 코드 내의 식 임을 의미 합니다. 배열을 반환 하도록 식을 원한다 면 배열을 래핑하는 `literal` 식입니다. 아이콘을 설정 하는 다음 예제에서는 `offset` 를 사용 하 여 두 숫자를 포함 하는 배열 이어야 하는 기호 계층의 옵션을 `match` 오프셋된 두 값 중에서 선택 하는 식의 값에 따라는 `entityType` 요소의 속성 기능입니다.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If there is no title, try getting the subtitle. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>색 식

색 식 쉽게 만들고 색 값을 조작 합니다.

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | 색 | 색 값을 만듭니다 *빨간색*, *녹색*, 및 *파란색* 사이의 구성 요소 `0` 고 `255`, 알파구성요소및`1`. 모든 구성 요소 범위를 벗어난 경우 식 오류입니다. |
| `['rgba', number, number, number, number]` | 색 | 색 값을 만듭니다 *빨간색*, *녹색*를 *파란색* 사이의 구성 요소 `0` 및 `255`, 및의 범위 내 알파 구성 요소 `0` 고 `1`입니다. 모든 구성 요소 범위를 벗어난 경우 식 오류입니다. |
| `['to-rgba']` | \[number, number, number, number\] | 입력된 색의 포함 된 4 개 요소 배열을 반환 *빨간색*를 *녹색*를 *파란색*, 및 *알파* 해당 순서 대로 구성 요소입니다. |

**예제**

다음 예제에서는 만들고 있는 RGB 색상 값을 *빨간색* 의 값 `255`, 및 *녹색* 하 고 *파란색* 를곱하여계산되는값`2.5` 의 값으로는 `temperature` 속성입니다. 다양 한 음영으로 색 바뀝니다 온도 변경 되 면 *빨간색*합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>문자열 연산자 식

문자열 연산자 식 문자열을 연결 하는 대/소문자 변환 등의 변환 작업을 수행 합니다. 

| 식 | 반환 형식 | 설명 |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 문자열 | 여러 문자열을 함께 연결합니다. 각 값은 문자열 이어야 합니다. 사용 된 `to-string` 필요한 경우 다른 값 형식을 문자열로 변환 하려면 식을 입력 합니다. |
| `['downcase', string]` | 문자열 | 지정된 문자열을 소문자로 변환합니다. |
| `['upcase', string]` | 문자열 | 지정된 문자열을 대문자로 변환합니다. |

**예제**

다음 예제에서는 변환 된 `temperature` 지점의 속성을 문자열로 기능과 다음의 끝에 "° F"를 연결 합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

위의 식은 텍스트 "64 ° F" 아래 이미지에 나와 있는 것 처럼 그 위에 오버레이 사용 하 여 맵에 핀을 렌더링 합니다.

<center>

![문자열 연산자 식 예](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>보간 및 단계 식

보간 및 단계 식은 보간된 곡선 또는 함수를 함께 값을 계산 하려면 사용할 수 있습니다. 예를 들어 숫자 값을 해당 입력을 반환 하는 식에서 이러한 식은 수행 `['get',  'temperature']`합니다. 입력된 값 쌍의 입력 및 출력 값을 "중지" 보간된 곡선 또는 함수에 가장 적합 한 값을 확인 하려면 호출에 대해 평가 됩니다. 각 중지에 대 한 입력된 값 수와 오름차순으로 정렬 되어야 합니다. 출력 값은 숫자 및 숫자의 배열 또는 색 이어야 합니다.

### <a name="interpolate-expression"></a>보간 식

`interpolate` 식을 사용 하 여 중지 값 사이 보간하여 지속적으로 부드러운 값 집합이 계산할 수 있습니다. `interpolate` 색 값을 반환 하는 식 결과의 값에서 선택 색 그라데이션을 생성 합니다.

보간 방법에서 사용할 수 있는 세 가지 유형의 키를 가지는 `interpolate` 식:
 
* `['linear']` -중지 쌍 간의 선형으로 보간합니다.
* `['exponential', base]` -위치 사이의 기하급수적으로 보간합니다. `base` 는 출력 증가 속도 제어 하는 값입니다. 값이 높을수록 더 높은 범위 끝 방향으로 증가 하는 출력을 확인 합니다. `base` 값 1에 가까울수록 더 선형적으로 증가 하는 출력을 생성 합니다.
* `['cubic-bezier', x1, y1, x2, y2]` -를 사용 하 여 보간합니다를 [입방 형 3 차원 곡선](https://developer.mozilla.org/docs/Web/CSS/timing-function) 지정된 제어 지점으로 정의 합니다.

이러한 다양 한 보간 모양을의 예는 다음과 같습니다. 

| 선형  | 지수 | 입방 형 3 차원 |
|---------|-------------|--------------|
| ![선형 보간 그래프](media/how-to-expressions/linear-interpolation.png) | ![지 수 백오프 보간 그래프](media/how-to-expressions/exponential-interpolation.png) | ![입방 형 3 차원 곡선 보간 그래프](media/how-to-expressions/bezier-curve-interpolation.png) |

다음 의사 코드의 구조를 정의 합니다 `interpolate` 식입니다. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**예제**

다음 예제에서는 `linear interpolate` 식을 설정 하는 `color` 거품형 계층의 속성에 따라는 `temperature` 지점 기능 속성입니다. 경우는 `temperature` 값이 60, "blue" 60 사이 70 미만일 노란색 반환할 70 및 80 보다 낮으므로 간에 "주황색"는 반환 될 경우, 80 이상인 경우 "red"를 반환할 경우 경우 반환 되는 보다 작습니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

다음 그림은 위의 식에 대 한 색은 선택 하는 방법을 보여 줍니다.
 
<center>

![보간 식 예제](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>단계 식

A `step` 식을 평가 하 여 불연속, 단계별 결과 값을 계산 하려면 사용할 수는 [구분 적 상수 함수](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) 중지 하 여 정의 합니다. 

다음 의사 코드의 구조를 정의 합니다 `step` 식입니다. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

단계 식의 첫 번째 보다 작은 값을 입력된 하거나 입력이 첫 번째 입력된 값 직전 중지 출력 값을 반환 합니다. 

**예제**

다음 예제에서는 `step` 식을 설정 하는 `color` 거품형 계층의 속성에 따라는 `temperature` 지점 기능 속성입니다. 경우는 `temperature` 값이 60 사이 70 미만일 "노란색"는 반환 될 경우, 70 및 80 보다 낮으므로 간에 "주황색"는 반환 될 경우, 80 이상인 경우 "red"를 반환할 경우 60, "blue" 반환할 보다 작습니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

다음 그림은 위의 식에 대 한 색은 선택 하는 방법을 보여 줍니다.
 
<center>

![단계 식 예](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>계층의 특정 식

특정 계층에만 적용 되는 특별 한 식입니다.

### <a name="heat-map-density-expression"></a>열 지도 밀도 식

열 지도 밀도 식 열 지도 계층의 각 픽셀에 대 한 열 지도 밀도 값을 검색 하 고 란 `['heatmap-density']`합니다. 이 값은 사이의 숫자 `0` 및 `1` 와 함께에서 사용 되는 `interpolation` 또는 `step` 색 그라데이션 열 지도 색을 지정 하는 데 사용을 정의 하는 식을 합니다. 이 식 에서만 사용할 수는 [옵션을 색](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) 열 지도 계층의 합니다.

> [!TIP]
> 인덱스 보간 식에서 0에 있는 색 또는 단계 색을의 기본 색상인 영역의 색을 정의 합니다. 데이터가 없는 경우 배경색을 정의 하려면 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다. 

**예제**

이 예제에서는 열 지도 렌더링 하는 것에 대 한 부드러운 색 그라데이션을 만드는 개체로 보간 식입니다. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

색상을 지정 하는 열 지도 부드러운 그라데이션이 사용 하는 것 외에도 색 내에서 지정할 수 범위 집합을 사용 하 여를 `step` 식입니다. 사용 하는 `step` 윤곽선 또는 방사형 스타일 맵을 더 유사한 범위로 시각적 밀도를 분할 하 여 식의 열 지도 색을 지정 합니다.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

자세한 내용은 참조는 [열 지도 계층 추가](map-add-heat-map-layer.md) 설명서.

### <a name="line-progress-expression"></a>선 진행률 식

선 진행률 식 선 계층에 그라데이션 선 따라 진행 상태를 검색 하 고 란 `['line-progress']`합니다. 이 값은 0과 1 사이의 숫자 및 함께 사용 되는 `interpolation` 또는 `step` 식입니다. 사용 하 여이 식 에서만 사용할 수 있습니다 합니다 [strokeGradient 옵션]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) 선 계층의 합니다. 

> [!NOTE]
> `strokeGradient` 선 계층의 옵션을 사용 하려면 합니다 `lineMetrics` 로 설정 되는 데이터 원본 옵션 `true`합니다.

**예제**

다음 예제에서는 `['line-progress']` 줄의 스트로크에 색 그라데이션을 적용 하는 식입니다.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[라이브 예제를 참조 하세요.](map-add-shape.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>텍스트 필드 형식 지정 식

텍스트 필드 형식 지정 식 사용 될 수 있습니다 합니다 `textField` 기호 계층의 옵션 `textOptions` 혼합된 텍스트 서식을 제공 하는 속성입니다. 이 식은 입력된 문자열 집합과를 서식 지정 옵션을 지정할 수 있습니다. 이 식에서 각 입력된 문자열에 대해 다음 옵션을 지정할 수 있습니다.

 * `'font-scale'` -글꼴 크기에 대 한 배율 인수를 지정합니다. 이 값은 재정의 지정 하는 경우는 `size` 의 속성을 `textOptions` 개별 문자열에 대 한 합니다.
 * `'text-font'` -이 문자열에 대 한 사용 해야 하는 하나 이상의 글꼴 패밀리를 지정 합니다. 이 값은 재정의 지정 하는 경우는 `font` 의 속성을 `textOptions` 개별 문자열에 대 한 합니다.

다음 의사 코드는 텍스트 필드 형식 지정 식의 구조를 정의 합니다. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] 
    },
    …
]
```

**예제**

다음 예제에서는 굵은 글꼴을 추가 하 고 글꼴 크기를 확장 하 여 텍스트 필드의 서식을 `title` 기능 속성입니다. 이 예제에서는 또한 추가 `subtitle` 확장을 사용 하 여 줄 바꿈의 기능 속성 글꼴 크기를 축소 합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 'font-scale': 0.75 }
        ]
    }
});
```

이 계층 아래 이미지와 같이 지점 기능을 렌더링 됩니다.
 
<center>

![서식 있는 텍스트 필드를 사용 하 여 지점 기능의 이미지](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>숫자 형식 지정 식

합니다 `number-format` 사용 하 여 식 에서만 사용할 수 있습니다는 `textField` 기호 계층의 옵션입니다. 이 식은 제공된 된 번호 서식이 지정 된 문자열로 변환 됩니다. 이 식에는 JavaScript의 래핑합니다 [Number.toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수 및 옵션의 다음 집합을 지원 합니다.

 * `locale` -지정된 된 언어를 사용 하 여 정렬 하는 방식으로 문자열을 숫자 변환에 대해이 옵션을 지정 합니다. 전달 된 [BCP 47 언어 태그로](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) 이 옵션을 합니다.
 * `currency` -변환할 수는 통화를 나타내는 문자열입니다. 가능한 값은는 [ISO 4217 통화 코드](https://en.wikipedia.org/wiki/ISO_4217)"USD" 미국 달러, 유로에 대 한 "EUR" 또는 "CNY" 중국어 RMB에 대 한 예입니다.
 * `'min-fraction-digits'` -숫자의 문자열 버전에 포함 하는 소수 자릿수의 최소 수를 지정 합니다.
 * `'max-fraction-digits'` -숫자의 문자열 버전에 포함 하는 소수 자릿수의 최대 수를 지정 합니다.

다음 의사 코드는 텍스트 필드 형식 지정 식의 구조를 정의 합니다. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**예제**

다음 예에서는 `number-format` 수정 하는 식 하는 방법을 `revenue` 지점 기능의 속성에서 렌더링 되는 `textField` 기호 옵션 계층 미국 달러 값을 표시 합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency’: 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

이 계층 아래 이미지와 같이 지점 기능을 렌더링 됩니다.

<center>

![숫자 형식 식 예](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>확대/축소 식

A `zoom` 식 렌더링 시 지도의 현재 확대/축소 수준 검색 되 고으로 정의 됩니다 `['zoom']`합니다. 이 식은 지도의 최소 및 최대 확대/축소 수준 범위 사이의 숫자를 반환합니다. 이 식을 사용 하 여 스타일을 지도의 확대/축소 수준을 변경 될 때마다 동적으로 수정할 수 있습니다. 합니다 `zoom` 사용 하 여 식만 사용할 수 있습니다 `interpolate` 및 `step` 식입니다.

**예제**

기본적으로 열 지도 계층에 렌더링 하는 데이터 요소의 반지름 모든 확대/축소 수준에 대 한 고정된 픽셀 반지름을 적용 합니다. 지도 확대/축소 하는 대로 함께 데이터 집계와 열 지도 계층에 다르게 보입니다. `zoom` 식에서는 맵의 동일한 물리적 영역을 각 데이터 요소는 각 확대/축소 수준에 대 한 radius 확장 데 사용할 수 있습니다. 더 정적이 고 일관 된 표시 열 지도 계층을 확인 합니다. 맵의 각 확대/축소 수준에 배의 픽셀 세로 및 가로로 이전 확대/축소 수준으로 각 확대/축소 수준으로 두 배로 만듭니다는 반지름 크기 조정 모든 확대/축소 수준에서 일관 된 보이는 열 지도를 만듭니다. 이 사용 하 여 수행할 수 있습니다 합니다 `zoom` 식을 사용 하 여를 `base 2 exponential interpolation` 아래와 같이 식입니다. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 10,000 pixels.
        19, 10000
    ]
};
```

[라이브 예제를 참조 하세요.](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>변수 바인딩 식

변수 바인딩 식을 참조할 수 있습니다 다른 곳에서 식에 여러 번 다시 계산 하지 않아도 되도록 계산의 결과 변수에 저장 합니다. 이 많은 계산을 포함 하는 식에 대 한 유용한 최적화

| 식 | 반환 형식 | 설명 |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;'let',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: 문자열<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: 있는 경우<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: 문자열<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: 있는 경우<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 변수 사용에 대 한 하나 이상의 값을 저장 합니다 `var` 결과 반환 하는 하위 식에 있습니다. |
| `['var', name: string]` | 모든 | 변수를 사용 하 여 만든 참조는 `let` 식입니다. |

**예제**

온도 비율 사용 하 여 다음을 기준으로 수익을 계산 하는 식을 사용 하 여이 예제는 `case` 다른 부울 연산에서이 값을 계산할 식입니다. 합니다 `let` 식은 한 번 계산 하기만 하면 되도록 온도 비율을 기준으로 수익을 저장 하는데 사용 됩니다 및 `var` 식을 다시 계산 하지 않고 필요한 만큼이 변수를 참조 합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>다음 단계

식을 구현 하는 코드 샘플 더 보기는 다음 문서를 참조 하세요.

> [!div class="nextstepaction"] 
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"] 
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"] 
> [셰이프 추가](map-add-shape.md)

> [!div class="nextstepaction"] 
> [열 지도 계층 추가](map-add-heat-map-layer.md)

식을 지 원하는 계층 옵션에 대 한 자세한 정보:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest) 
