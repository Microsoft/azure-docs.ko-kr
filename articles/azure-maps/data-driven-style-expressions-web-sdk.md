---
title: Azure Maps 웹 SDK의 데이터 기반 스타일 식 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 웹 SDK에서 데이터 기반 스타일 식을 사용 하는 방법에 대해 알아봅니다.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen
ms.openlocfilehash: e3e8476d09541518d964bfaff4dabad47755eeb9
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189659"
---
# <a name="data-driven-style-expressions-web-sdk"></a>데이터 기반 스타일 식 (웹 SDK)

식을 사용 하면 데이터 원본의 각 셰이프에 정의 된 속성을 관찰 하는 옵션에 대 한 스타일 지정 옵션에 비즈니스 논리를 적용할 수 있습니다. 식은 데이터 원본 또는 계층의 데이터를 필터링 할 수 있습니다. 식은 if 문과 같은 조건부 논리로 구성 될 수 있습니다. 또한 문자열 연산자, 논리 연산자, 수학 연산자를 사용 하 여 데이터를 조작 하는 데 사용할 수 있습니다.

데이터 기반 스타일은 스타일 지정에 대 한 비즈니스 논리를 구현 하는 데 필요한 코드의 양을 줄여 줍니다. 계층에서 사용 하는 경우 별도의 스레드에서 렌더링 시 식이 계산 됩니다. 이 기능은 UI 스레드에서 비즈니스 논리를 평가 하는 것과 비교 하 여 향상 된 성능을 제공 합니다.

이 비디오는 Azure Maps 웹 SDK의 데이터 기반 스타일에 대 한 개요를 제공 합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

식은 JSON 배열로 표시 됩니다. 배열에서 식의 첫 번째 요소는 식 연산자의 이름을 지정 하는 문자열입니다. 예를 들어 "+" 또는 "case"입니다. 다음 요소 (있는 경우)는 식에 대 한 인수입니다. 각 인수는 리터럴 값 (문자열, 숫자, 부울 또는 `null`) 이거나 다른 식 배열입니다. 다음 의사 코드에서는 식의 기본 구조를 정의 합니다. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Azure Maps 웹 SDK는 다양 한 형식의 식을 지원 합니다. 식을 자체적으로 사용 하거나 다른 식과 함께 사용할 수 있습니다.

| 식 형식 | Description |
|---------------------|-------------|
| [집계 식](#aggregate-expression) | 데이터 집합에 대해 처리 되 고 `DataSource`의 `clusterProperties` 옵션과 함께 사용할 수 있는 계산을 정의 하는 식입니다. |
| [부울 식](#boolean-expressions) | 부울 식은 부울 비교를 평가 하기 위한 부울 연산자 식 집합을 제공 합니다. |
| [색 식](#color-expressions) | 색 식을 사용 하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다. |
| [조건식](#conditional-expressions) | 조건식은 if 문과 같은 논리 연산을 제공 합니다. |
| [데이터 식](#data-expressions) | 기능에서 속성 데이터에 대 한 액세스를 제공 합니다. |
| [보간 및 단계 식](#interpolate-and-step-expressions) | 보간 및 단계 식은 보간된 곡선이 나 step 함수를 따라 값을 계산 하는 데 사용할 수 있습니다. |
| [계층 관련 식](#layer-specific-expressions) | 단일 계층에만 적용 되는 특수 식입니다. |
| [수학 식](#math-expressions) | 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다. |
| [문자열 연산자 식](#string-operator-expressions) | 문자열 연산자 식은 사례를 연결 하 고 변환 하는 등의 문자열에 대해 변환 작업을 수행 합니다. |
| [형식 식](#type-expressions) | 형식 식은 문자열, 숫자, 부울 값 등의 다양 한 데이터 형식을 테스트 하 고 변환 하는 도구를 제공 합니다. |
| [변수 바인딩 식](#variable-binding-expressions) | 변수 바인딩 식은 계산 결과를 변수에 저장 하 고 저장 된 값을 다시 계산할 필요 없이 식의 다른 위치에서 여러 번 참조 합니다. |
| [확대/축소 식](#zoom-expression) | 렌더링 시 지도의 현재 확대/축소 수준을 검색 합니다. |

이 문서의 모든 예제에서는 다음 기능을 사용 하 여 다양 한 형식의 식을 사용할 수 있는 여러 가지 방법을 보여 줍니다. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
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

데이터 식은 기능에서 속성 데이터에 대 한 액세스를 제공 합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['at', number, array]` | object | 배열에서 항목을 검색 합니다. |
| `['geometry-type']` | 문자열 | 기능의 기 하 도형 유형인 Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon을 가져옵니다. |
| `['get', string]` | 값 | 현재 기능의 속성에서 속성 값을 가져옵니다. 요청 된 속성이 없는 경우 null을 반환 합니다. |
| `['get', string, object]` | 값 | 제공 된 개체의 속성에서 속성 값을 가져옵니다. 요청 된 속성이 없는 경우 null을 반환 합니다. |
| `['has', string]` | boolean | 기능의 속성에 지정 된 속성이 있는지 여부를 확인 합니다. |
| `['has', string, object]` | boolean | 개체의 속성에 지정 된 속성이 있는지 여부를 확인 합니다. |
| `['id']` | 값 | 기능 ID가 있는 경우 해당 ID를 가져옵니다. |
| `['length', string | array]` | number | 문자열이 나 배열의 길이를 가져옵니다. |

**예**

기능의 속성은 식에서 `get` 식을 사용 하 여 직접 액세스할 수 있습니다. 이 예에서는 기능의 "zoneColor" 값을 사용 하 여 거품형 계층의 color 속성을 지정 합니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

위의 예는 모든 point 기능에 `zoneColor` 속성이 있는 경우 제대로 작동 합니다. 그렇지 않으면 색이 "black"으로 대체 될 수 있습니다. 대체 색을 수정 하려면 `case` 식을 `has` 식과 함께 사용 하 여 속성이 있는지 확인 합니다. 속성이 없으면 대체 (fallback) 색을 반환 합니다.

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

거품형 및 기호 계층은 기본적으로 데이터 원본에 있는 모든 셰이프의 좌표를 렌더링 합니다. 이 동작은 다각형의 꼭 짓 점 또는 선을 강조 표시할 수 있습니다. 계층의 `filter` 옵션은 부울 식 내에서 `['geometry-type']` 식을 사용 하 여 렌더링 하는 기능의 기 하 도형 형식을 제한 하는 데 사용할 수 있습니다. 다음 예에서는 `Point` 기능만 렌더링 되도록 거품형 계층을 제한 합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

다음 예에서는 `Point` 및 `MultiPoint` 기능을 모두 렌더링할 수 있습니다. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

마찬가지로 다각형의 윤곽선이 선 계층에서 렌더링 됩니다. 선 계층에서이 동작을 사용 하지 않도록 설정 하려면 `LineString` 및 `MultiLineString` 기능만 허용 하는 필터를 추가 합니다.  

## <a name="math-expressions"></a>수학 식

수학 식은 식 프레임 워크 내에서 데이터 기반 계산을 수행 하는 수치 연산자를 제공 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['+', number, number, …]` | number | 지정 된 숫자의 합계를 계산 합니다. |
| `['-', number]` | number | 지정 된 수 만큼 0을 뺍니다. |
| `['-', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 뺍니다. |
| `['*', number, number, …]` | number | 지정 된 숫자를 곱합니다. |
| `['/', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눕니다. |
| `['%', number, number]` | number | 첫 번째 숫자를 두 번째 숫자로 나눌 때 나머지를 계산 합니다. |
| `['^', number, number]` | number | 두 번째 숫자의 거듭제곱으로 발생 한 첫 번째 값의 값을 계산 합니다. |
| `['abs', number]` | number | 지정된 된 숫자의 절대값을 계산 합니다. |
| `['acos', number]` | number | 지정 된 숫자의 아크코사인을 계산 합니다. |
| `['asin', number]` | number | 지정 된 숫자의 아크사인을 계산 합니다. |
| `['atan', number]` | number | 지정 된 숫자의 아크탄젠트를 계산 합니다. |
| `['ceil', number]` | number | 숫자를 다음 정수 정수로 반올림 합니다. |
| `['cos', number]` | number | 지정 된 수의 cos를 계산 합니다. |
| `['e']` | number | 수학 상수 `e`를 반환 합니다. |
| `['floor', number]` | number | 숫자를 이전 정수 정수로 내림 합니다. |
| `['ln', number]` | number | 지정 된 숫자의 자연 로그를 계산 합니다. |
| `['ln2']` | number | 수학 상수 `ln(2)`를 반환 합니다. |
| `['log10', number]` | number | 지정 된 숫자의 밑이 10 인 로그를 계산 합니다. |
| `['log2', number]` | number | 지정 된 숫자의 밑이 2 인 로그를 계산 합니다. |
| `['max', number, number, …]` | number | 지정 된 숫자 집합의 최대 수를 계산 합니다. |
| `['min', number, number, …]` | number | 지정 된 숫자 집합의 최소 수를 계산 합니다. |
| `['pi']` | number | 수학 상수 `PI`를 반환 합니다. |
| `['round', number]` | number | 숫자를 가장 가까운 정수로 반올림 합니다. 중간 값은 0에서 먼 쪽으로 반올림 됩니다. 예를 들어 `['round', -1.5]`은-2로 계산 됩니다. |
| `['sin', number]` | number | 지정 된 숫자의 사인을 계산 합니다. |
| `['sqrt', number]` | number | 지정된 된 숫자의 제곱근을 계산 합니다. |
| `['tan', number]` | number | 지정 된 숫자의 탄젠트를 계산 합니다. |

## <a name="aggregate-expression"></a>집계 식

집계 식은 데이터 집합에 대해 처리 되 고 `DataSource`의 `clusterProperties` 옵션과 함께 사용할 수 있는 계산을 정의 합니다. 이러한 식의 출력은 숫자 또는 부울 이어야 합니다. 

집계 식은 연산자 값과 초기 값 및 데이터의 각 기능에서 속성을 검색 하는 식을 사용 하 여 집계 연산을 적용할 수 있는 세 가지 값을 사용 합니다. 이 식은 다음과 같은 형식입니다.

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: 클러스터의 각 지점에 대해 `mapExpression`에서 계산 되는 모든 값에 대해에 적용 되는 식 함수입니다. 지원 되는 연산자: 
    - 숫자: `+`, `*`, `max`, `min`
    - 부울: `all`, `any`
- initialValue: 첫 번째 계산 된 값이 집계 되는 초기 값입니다.
- mapExpression: 데이터 집합의 각 지점에 대해 적용 되는 식입니다.

**예**

데이터 집합의 모든 기능에 `revenue` 속성이 있으면이 고, 그렇지 않으면입니다. 그런 다음 데이터 집합에서 만들어진 클러스터의 모든 점에 대 한 총 수익을 계산할 수 있습니다. 이 계산은 다음 집계 식을 사용 하 여 수행 됩니다. `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>부울 식

부울 식은 부울 비교를 평가 하기 위한 부울 연산자 식 집합을 제공 합니다.

값을 비교할 때 비교는 엄격 하 게 형식화 됩니다. 다른 형식의 값은 항상 동일 하지 않은 것으로 간주 됩니다. 구문 분석 시 형식이 다른 것으로 알려진 사례는 잘못 된 것으로 간주 되며 구문 분석 오류를 생성 합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['! ', boolean]` | boolean | 논리 부정. 입력이 `false`되 면 `true`을 반환 하 고 입력이 `true`되 면을 `false` 합니다. |
| `['!= ', value, value]` | boolean | 입력 값이 같지 않으면 `true`을 반환 하 고, 그렇지 않으면 `false` 합니다. |
| `['<', value, value]` | boolean | 첫 번째 입력 값이 두 번째 값 보다 엄격 하 게 작으면 `true`을 반환 하 고 `false` 그렇지 않으면를 반환 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `['<=', value, value]` | boolean | 첫 번째 입력 값이 두 번째 값 보다 작거나 같으면 `true`을 반환 하 고, 그렇지 않으면 `false`을 반환 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `['==', value, value]` | boolean | 입력 값이 같으면 `true`을 반환 하 고, 그렇지 않으면 `false` 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `['>', value, value]` | boolean | 첫 번째 입력 값이 두 번째 값 보다 엄격 하 게 크면 `true`을 반환 하 고, 그렇지 않으면 `false` 반환 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `['>=' value, value]` | boolean | 첫 번째 입력 값이 두 번째 값 보다 크거나 같으면 `true`을 반환 하 고, 그렇지 않으면를 `false` 합니다. 인수는 문자열 이거나 둘 다 숫자 여야 합니다. |
| `['all', boolean, boolean, …]` | boolean | 모든 입력이 `true`되 면 `true`을 반환 `false` 하 고, 그렇지 않으면를 반환 합니다. |
| `['any', boolean, boolean, …]` | boolean | 입력이 `true`되 면 `true`을 반환 하 고, 그렇지 않으면를 `false` 합니다. |

## <a name="conditional-expressions"></a>조건부 식

조건식은 if 문과 같은 논리 연산을 제공 합니다.

다음 식은 입력 데이터에 대해 조건부 논리 연산을 수행 합니다. 예를 들어 `case` 식은 "if/then/else" 논리를 제공 하는 반면 `match` 식은 "switch 문"과 유사 합니다. 

### <a name="case-expression"></a>Case 식

`case` 식은 "if/then/else" 논리를 제공 하는 조건식의 유형입니다. 이 유형의 식은 부울 조건 목록을 통해 단계를 진행 합니다. True로 평가할 첫 번째 부울 조건의 출력 값을 반환 합니다.

다음 의사 코드는 `case` 식의 구조를 정의 합니다. 

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

다음 예에서는 `true`로 계산 된 값을 찾은 다음 연결 된 값을 반환 하기 전까지 다양 한 부울 조건을 단계별로 안내 합니다. 부울 조건이 `true`로 평가 되지 않으면 대체 (fallback) 값이 반환 됩니다. 

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

`match` 식은 논리와 같이 switch 문을 제공 하는 조건식의 유형입니다. 입력은 문자열이 나 숫자를 반환 하는 `['get', 'entityType']`와 같은 식일 수 있습니다. 각 레이블은 값이 모든 문자열 또는 모든 숫자 여야 하는 단일 리터럴 값 또는 리터럴 값의 배열 이어야 합니다. 배열의 값이 일치 하는 경우 입력이 일치 합니다. 각 레이블은 고유 해야 합니다. 입력 유형이 레이블의 유형과 일치 하지 않는 경우 결과는 대체 (fallback) 값이 됩니다.

다음 의사 코드는 `match` 식의 구조를 정의 합니다. 

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

다음 예에서는 거품형 계층에서 Point 기능의 `entityType` 속성을 검색 하 여 일치 하는 항목을 찾습니다. 일치 하는 항목이 발견 되 면 지정 된 값이 반환 되거나 대체 (fallback) 값이 반환 됩니다.

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

다음 예제에서는 배열을 사용 하 여 모두 동일한 값을 반환 해야 하는 레이블 집합을 나열 합니다. 이 방법은 각 레이블을 개별적으로 나열 하는 것 보다 훨씬 효율적입니다. 이 경우 `entityType` 속성이 "식당" 또는 "grocery_store" 이면 색 "red"가 반환 됩니다.

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

다음 예에서는 match 식을 사용 하 여 "in array" 또는 "array contains" 형식 필터를 수행 합니다. 이 경우 식은 ID 값이 허용 되는 id 목록에 있는 데이터를 필터링 합니다. 필터를 사용 하는 식을 사용 하는 경우 결과는 부울 값 이어야 합니다.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>병합 식

`coalesce` 식에서는 첫 번째 null이 아닌 값을 가져오고 해당 값을 반환할 때까지 식 집합을 단계별로 진행 합니다. 

다음 의사 코드는 `coalesce` 식의 구조를 정의 합니다. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**예제**

다음 예에서는 `coalesce` 식을 사용 하 여 기호 계층의 `textField` 옵션을 설정 합니다. `title` 속성이 기능에서 누락 되거나 `null`로 설정 된 경우에는 식에서 `subtitle` 속성을 검색 한 후 누락 되거나 `null`되 면 빈 문자열로 대체 합니다. 

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

다음 예제에서는 `coalesce` 식을 사용 하 여 지정 된 이미지 이름 목록에서 map sprite에 사용할 수 있는 첫 번째 사용 가능한 이미지 아이콘을 검색 합니다.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>형식 식

형식 식은 문자열, 숫자, 부울 값 등의 다양 한 데이터 형식을 테스트 하 고 변환 하는 도구를 제공 합니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | 배열 \| 개체 | 리터럴 배열 또는 개체 값을 반환 합니다. 배열이 나 개체가 식으로 계산 되지 않도록 하려면이 식을 사용 합니다. 배열 또는 개체를 식에서 반환 해야 하는 경우이 작업이 필요 합니다. |
| `['image', string]` | 문자열 | 지정 된 이미지 ID가 맵 이미지 스프라이트에 로드 되는지 확인 합니다. 인 경우 ID가 반환 되 고, 그렇지 않으면 null이 반환 됩니다. |
| `['to-boolean', value]` | boolean | 입력 값을 부울로 변환 합니다. 입력이 빈 문자열, `0`, `false`, `null`또는 `NaN`이면 결과가 `false` 됩니다. 그렇지 않으면 `true`합니다. |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | 색 | 입력 값을 색으로 변환 합니다. 여러 값이 제공 되는 경우 첫 번째 변환이 성공적으로 수행 될 때까지 각 값이 순서 대로 평가 됩니다. 입력을 변환할 수 없는 경우 식이 오류입니다. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | number | 가능한 경우 입력 값을 숫자로 변환 합니다. 입력이 `null` 또는 `false`이면 결과는 0입니다. 입력이 `true`이면 결과는 1입니다. 입력이 문자열이 면 ECMAScript 언어 사양의 [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) string 함수를 사용 하 여 숫자로 변환 됩니다. 여러 값이 제공 되는 경우 첫 번째 변환이 성공적으로 수행 될 때까지 각 값이 순서 대로 평가 됩니다. 입력을 변환할 수 없는 경우 식이 오류입니다. |
| `['to-string', value]` | 문자열 | 입력 값을 문자열로 변환 합니다. 입력이 `null`이면 결과가 `""`됩니다. 입력이 부울 이면 결과가 `"true"` 되거나 `"false"`됩니다. 입력이 숫자 이면 ECMAScript 언어 사양의 [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) number 함수를 사용 하 여 문자열로 변환 됩니다. 입력이 색 이면 CSS RGBA 색 문자열 `"rgba(r,g,b,a)"`로 변환 됩니다. 그렇지 않으면 ECMAScript 언어 사양의 [json.stringify](https://tc39.github.io/ecma262/#sec-json.stringify) 함수를 사용 하 여 입력이 문자열로 변환 됩니다. |
| `['typeof', value]` | 문자열 | 지정 된 값의 형식을 설명 하는 문자열을 반환 합니다. |

> [!TIP]
> `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].`와 유사한 오류 메시지가 브라우저 콘솔에 표시 되는 경우 코드에 첫 번째 값에 대 한 문자열이 없는 배열이 있는 식이 있음을 의미 합니다. 식이 배열을 반환 하도록 하려면 배열을 `literal` 식으로 래핑합니다. 다음 예에서는 `match` 식을 사용 하 여 두 개의 숫자를 포함 하는 배열인 기호 계층의 아이콘 `offset` 옵션을 설정 합니다 .이 옵션은 point 기능의 `entityType` 속성 값을 기반으로 두 오프셋 값 중 하나를 선택 하는 데 사용 됩니다.
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
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>색 식

색 식을 사용 하면 색 값을 보다 쉽게 만들고 조작할 수 있습니다.

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | 색 | `0`와 `255`사이에 있어야 하는 *빨간색*, *녹색*및 *파랑* 구성 요소와 `1`의 알파 구성 요소에서 색 값을 만듭니다. 구성 요소가 범위를 벗어난 경우 식에 오류가 발생 합니다. |
| `['rgba', number, number, number, number]` | 색 | `0`와 `255`사이에 있어야 하는 *빨간색*, *녹색*, *파랑* 구성 요소에서 색 값을 만들고 `0` 및 `1`범위 내의 알파 구성 요소를 만듭니다. 구성 요소가 범위를 벗어난 경우 식에 오류가 발생 합니다. |
| `['to-rgba']` | \[번호, 숫자, 숫자, 숫자\] | 입력 색의 *빨간색*, *녹색*, *파랑*및 *알파* 구성 요소를 포함 하는 4 개 요소 배열을 해당 순서로 반환 합니다. |

**예제**

다음 예에서는 `255`*빨강* 값이 있는 RGB 색 값을 만들고 `temperature` 속성의 값에 `2.5`를 곱하여 계산 되는 *녹색* 및 *파랑* 값을 만듭니다. 온도가 변경 됨에 따라 색은 *빨간색*의 다른 음영으로 변경 됩니다.

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

문자열 연산자 식은 사례를 연결 하 고 변환 하는 등의 문자열에 대해 변환 작업을 수행 합니다. 

| 식 | 반환 형식 | Description |
|------------|-------------|-------------|
| `['concat', string, string, …]` | 문자열 | 여러 문자열을 연결 합니다. 각 값은 문자열 이어야 합니다. 필요한 경우 `to-string` 형식 식을 사용 하 여 다른 값 형식을 문자열로 변환 합니다. |
| `['downcase', string]` | 문자열 | 지정된 문자열을 소문자로 변환합니다. |
| `['upcase', string]` | 문자열 | 지정된 문자열을 대문자로 변환합니다. |

**예제**

다음 예에서는 point 기능의 `temperature` 속성을 문자열로 변환 하 고 "° F"을 끝에 연결 합니다.

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

위의 식은 아래 이미지에 표시 된 것 처럼 텍스트 "64 ° F"이 맨 위에 겹쳐서 표시 된 상태로 맵에 핀을 렌더링 합니다.

<center>

![문자열 연산자 식 예제](media/how-to-expressions/string-operator-expression.png) </center>

## <a name="interpolate-and-step-expressions"></a>보간 및 단계 식

보간 및 단계 식은 보간된 곡선이 나 step 함수를 따라 값을 계산 하는 데 사용할 수 있습니다. 이러한 식은 숫자 값을 입력으로 반환 하는 식 (예: `['get',  'temperature']`을 사용 합니다. 입력 값은 입력 및 출력 값 쌍에 대해 계산 되어 보간된 커브나 단계 함수에 가장 적합 한 값을 결정 합니다. 출력 값을 "중지" 라고 합니다. 각 중지의 입력 값은 숫자 여야 하며 오름차순 이어야 합니다. 출력 값은 숫자, 숫자 배열 또는 색 이어야 합니다.

### <a name="interpolate-expression"></a>보간 식

보간 식을 사용 하 여 중지 값 사이에 값의 연속 되는 부드러운 값 집합을 계산할 수 있습니다. `interpolate` 색 값을 반환 하는 `interpolate` 식은 결과 값이에서 선택 되는 색 그라데이션을 생성 합니다.

`interpolate` 식에서 사용할 수 있는 보간 방법에는 다음 세 가지 유형이 있습니다.
 
* `['linear']`-두 중지점 사이의 선형으로 보간합니다.
* `['exponential', base]`-중지 사이에 지를 보간합니다. `base` 값은 출력이 늘어나는 속도를 제어 합니다. 값이 높을수록 출력이 범위의 높은 쪽 끝에서 증가 합니다. 1에 가까운 `base` 값은 보다 선형적으로 향상 되는 출력을 생성 합니다.
* `['cubic-bezier', x1, y1, x2, y2]`-지정 된 제어점에서 정의 하는 [입방 형 3 차원 곡선](https://developer.mozilla.org/docs/Web/CSS/timing-function) 을 사용 하 여 보간합니다.

다음은 이러한 여러 유형의 보간의 예입니다. 

| 선형  | 지수 | 입방 형 3 차원 |
|---------|-------------|--------------|
| ![선형 보간 그래프](media/how-to-expressions/linear-interpolation.png) | ![지 수 보간 그래프](media/how-to-expressions/exponential-interpolation.png) | ![입방 형 3 차원 보간 그래프](media/how-to-expressions/bezier-curve-interpolation.png) |

다음 의사 코드는 `interpolate` 식의 구조를 정의 합니다. 

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

다음 예에서는 `linear interpolate` 식을 사용 하 여 point 기능의 `temperature` 속성에 따라 거품 계층의 `color` 속성을 설정 합니다. `temperature` 값이 60 보다 작은 경우 "blue"가 반환 됩니다. 60 ~ 70 보다 작은 경우에는 노란색이 반환 됩니다. 70 ~ 80 보다 작은 경우 "주황색"이 반환 됩니다. 80 이상인 경우 "red"가 반환 됩니다.

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

다음 이미지는 위의 식에서 색을 선택 하는 방법을 보여 줍니다.
 
<center>

![보간 식 예제](media/how-to-expressions/interpolate-expression-example.png) </center>

### <a name="step-expression"></a>Step 식

`step` 식은 중지로 정의 된 [부분 일정 분포 상수 함수](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) 를 평가 하 여 불연속의 단계별 결과 값을 계산 하는 데 사용할 수 있습니다. 

다음 의사 코드는 `step` 식의 구조를 정의 합니다. 

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

단계 식은 입력 값 바로 앞에 있는 stop의 출력 값 또는 입력이 첫 번째 중지 보다 작은 경우 첫 번째 입력 값을 반환 합니다. 

**예제**

다음 예에서는 `step` 식을 사용 하 여 point 기능의 `temperature` 속성에 따라 거품 계층의 `color` 속성을 설정 합니다. `temperature` 값이 60 보다 작은 경우 "blue"가 반환 됩니다. 60 ~ 70 보다 작은 경우 "노란색"이 반환 됩니다. 70 ~ 80 보다 작은 경우 "주황색"이 반환 됩니다. 80 이상인 경우 "red"가 반환 됩니다.

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

다음 이미지는 위의 식에서 색을 선택 하는 방법을 보여 줍니다.
 
<center>

![Step 식 예제](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>계층 관련 식

특정 레이어에만 적용 되는 특수 식입니다.

### <a name="heat-map-density-expression"></a>열 지도 밀도 식

열 지도 밀도 식은 열 지도 계층의 각 픽셀에 대 한 열 지도 밀도 값을 검색 하 고 `['heatmap-density']`으로 정의 됩니다. 이 값은 `0`와 `1`사이의 숫자입니다. 열 지도를 색으로 지정 하는 데 사용 되는 색 그라데이션을 정의 하기 위해 `interpolation` 또는 `step` 식과 함께 사용 됩니다. 이 식은 열 지도 계층의 [색 옵션](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest#color) 에만 사용할 수 있습니다.

> [!TIP]
> 보간 식의 인덱스 0에 있는 색 또는 단계 색의 기본 색은 데이터가 없는 영역의 색을 정의 합니다. 인덱스 0에 있는 색은 배경색을 정의 하는 데 사용할 수 있습니다. 대부분 이 값을 투명 또는 반투명 검은색으로 설정하는 것을 선호합니다.

**예제**

이 예제에서는 liner 보간 식을 사용 하 여 열 지도를 렌더링 하기 위한 부드러운 색 그라데이션을 만듭니다. 

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

부드러운 그라데이션을 사용 하 여 열 지도를 색상화 하는 것 외에도 `step` 식을 사용 하 여 범위 집합 내에서 색을 지정할 수 있습니다. 열 지도를 색으로 하는 데 `step` 식을 사용 하면 색을 컨투어 또는 방사형 스타일 맵과 유사한 범위로 시각적으로 나눕니다.  

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

자세한 내용은 [열 지도 계층 추가](map-add-heat-map-layer.md) 설명서를 참조 하세요.

### <a name="line-progress-expression"></a>줄 진행률 식

줄 진행률 식은 선 계층에서 그라데이션 선을 따라 진행률을 검색 하 고 `['line-progress']`로 정의 됩니다. 이 값은 0에서 1 사이의 숫자입니다. `interpolation` 또는 `step` 식과 함께 사용 됩니다. 이 식은 선 계층의 [strokeGradient 옵션]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest#strokegradient) 에만 사용할 수 있습니다. 

> [!NOTE]
> 선 계층의 `strokeGradient` 옵션을 사용 하려면 데이터 원본의 `lineMetrics` 옵션을 `true`으로 설정 해야 합니다.

**예제**

이 예제에서는 `['line-progress']` 식을 사용 하 여 선의 스트로크에 색 그라데이션을 적용 합니다.

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

[라이브 예제 참조](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>텍스트 필드 형식 식

텍스트 필드 형식 식은 기호 계층 `textOptions` 속성의 `textField` 옵션과 함께 사용 하 여 혼합 텍스트 서식을 제공할 수 있습니다. 이 식을 사용 하면 입력 문자열과 서식 옵션의 집합을 지정할 수 있습니다. 이 식의 각 입력 문자열에 대해 다음과 같은 옵션을 지정할 수 있습니다.

 * `'font-scale'`-글꼴 크기의 배율 인수를 지정 합니다. 지정 된 경우이 값은 개별 문자열의 `textOptions` `size` 속성을 재정의 합니다.
 * `'text-font'`-이 문자열에 사용 해야 하는 글꼴 패밀리를 하나 이상 지정 합니다. 지정 된 경우이 값은 개별 문자열의 `textOptions` `font` 속성을 재정의 합니다.
 * `'text-color'`-렌더링할 때 텍스트에 적용할 색을 지정 합니다. 

다음 의사 코드는 텍스트 필드 형식 식의 구조를 정의 합니다. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**예제**

다음 예에서는 굵은 글꼴을 추가 하 고 기능의 `title` 속성 글꼴 크기를 확장 하 여 텍스트 필드의 서식을 지정 합니다. 이 예제에서는 또한 줄 바꿈에 기능의 `subtitle` 속성을 추가 하 고 크기를 축소 하 여 글꼴 크기와 빨강으로 색을 지정 합니다.

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
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

이 계층은 아래 이미지와 같이 point 기능을 렌더링 합니다.
 
<center>

서식이 지정 된 텍스트 필드가 있는 Point 기능 이미지를 ![](media/how-to-expressions/text-field-format-expression.png) </center>

### <a name="number-format-expression"></a>숫자 형식 식

`number-format` 식은 기호 계층의 `textField` 옵션에만 사용할 수 있습니다. 이 식은 제공 된 숫자를 서식이 지정 된 문자열로 변환 합니다. 이 식은 JavaScript의 [Number. toLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 함수를 래핑하고 다음 옵션 집합을 지원 합니다.

 * `locale`-지정 된 언어로 정렬 되는 방식으로 숫자를 문자열로 변환 하기 위한이 옵션을 지정 합니다. 이 옵션에 [BCP 47 언어 태그](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) 를 전달 합니다.
 * `currency`-숫자를 통화를 나타내는 문자열로 변환 합니다. 가능한 값은 미국 달러의 경우 "USD", 유로화의 경우 "EUR", 중국어 RMB의 경우 "CNY"와 같은 [ISO 4217 통화 코드](https://en.wikipedia.org/wiki/ISO_4217)입니다.
 * `'min-fraction-digits'`-숫자의 문자열 버전에 포함할 최소 소수 자릿수를 지정 합니다.
 * `'max-fraction-digits'`-숫자의 문자열 버전에 포함할 최대 소수 자릿수를 지정 합니다.

다음 의사 코드는 텍스트 필드 형식 식의 구조를 정의 합니다. 

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

다음 예에서는 `number-format` 식을 사용 하 여 요소 기능의 `revenue` 속성이 미국 달러 값으로 표시 되도록 기호 계층의 `textField` 옵션에서 렌더링 되는 방식을 수정 합니다.

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

이 계층은 아래 이미지와 같이 point 기능을 렌더링 합니다.

<center>

![번호 형식 식 예제](media/how-to-expressions/number-format-expression.png) </center>

## <a name="zoom-expression"></a>확대/축소 식

`zoom` 식은 렌더링 시 지도의 현재 확대/축소 수준을 검색 하는 데 사용 되 고 `['zoom']`으로 정의 됩니다. 이 식은 지도의 최소 및 최대 확대/축소 수준 범위 사이의 숫자를 반환 합니다. 웹 및 Android에 대 한 대화형 맵 컨트롤 Azure Maps 25 개의 확대/축소 수준 (0-24)을 지원 합니다. `zoom` 식을 사용 하면 지도의 확대/축소 수준을 변경할 때 스타일을 동적으로 수정할 수 있습니다. `zoom` 식은 `interpolate` 및 `step` 식 에서만 사용할 수 있습니다.

**예제**

기본적으로 열 지도 계층에 렌더링 되는 데이터 요소의 반지름에는 모든 확대/축소 수준에 대 한 고정 픽셀 반지름이 있습니다. 지도가 확대 되 면 데이터가 함께 집계 되 고 열 지도 계층이 다르게 보입니다. 각 데이터 요소가 지도의 동일한 실제 영역을 포함 하도록 각 확대/축소 수준에 대 한 반지름의 크기를 조정 하는 데 `zoom` 식을 사용할 수 있습니다. 열 지도 계층을 더 정적이 고 일관 되 게 보이게 합니다. 지도의 각 확대/축소 수준에는 이전 확대/축소 수준과 가로 및 세로로 두 배의 픽셀이 있습니다. 각 확대/축소 수준에서 두 배가 되도록 반경 크기를 조정 하면 모든 확대/축소 수준에서 일치 하는 열 지도를 만듭니다. 아래와 같이 `base 2 exponential interpolation` 식으로 `zoom` 식을 사용 하 여이를 수행할 수 있습니다. 

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

[라이브 예제 참조](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>변수 바인딩 식

변수 바인딩 식은 계산 결과를 변수에 저장 합니다. 따라서 계산 결과를 식의 다른 위치에서 여러 번 참조할 수 있습니다. 많은 계산을 포함 하는 식에 대 한 유용한 최적화입니다.

| 식 | 반환 형식 | Description |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;' let ',<br/>&nbsp;&nbsp;&nbsp;&nbsp;name1: string,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;name2: 문자열,<br/>&nbsp;&nbsp;&nbsp;&nbsp;value2: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;...<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | 결과를 반환 하는 자식 식의 `var` 식에서 사용할 변수로 하나 이상의 값을 저장 합니다. |
| `['var', name: string]` | any | `let` 식을 사용 하 여 만든 변수를 참조 합니다. |

**예제**

이 예에서는 온도 비율에 상대적인 수익을 계산 하는 식을 사용한 다음 `case` 식을 사용 하 여이 값에 대 한 다양 한 부울 연산을 계산 합니다. `let` 식은 온도 비율을 기준으로 한 번만 계산 되도록 수익을 저장 하는 데 사용 됩니다. `var` 식은이 변수를 다시 계산할 필요 없이 자주 참조 합니다.

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

식을 구현 하는 더 많은 코드 샘플은 다음 문서를 참조 하세요.

> [!div class="nextstepaction"] 
> [기호 계층 추가](map-add-pin.md)

> [!div class="nextstepaction"] 
> [거품형 계층 추가](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [선 계층 추가](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [다각형 계층 추가](map-add-shape.md)

> [!div class="nextstepaction"] 
> [열 지도 계층 추가](map-add-heat-map-layer.md)

식을 지 원하는 계층 옵션에 대해 자세히 알아보세요.

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
