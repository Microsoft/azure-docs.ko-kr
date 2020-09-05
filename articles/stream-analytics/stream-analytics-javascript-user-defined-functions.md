---
title: Azure Stream Analytics JavaScript 사용자 정의 함수
description: 이 문서에서는 Stream Analytics의 JavaScript 사용자 정의 함수를 소개합니다.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-javascript
ms.date: 06/16/2020
ms.openlocfilehash: 6540b35925a92ebd6a8bcced427b5457785603db
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056910"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Azure Stream Analytics에서 JavaScript 사용자 정의 함수
 
Azure Stream Analytics에서는 JavaScript로 작성된 사용자 정의 함수를 지원합니다. JavaScript에서 제공하는 풍부한 메서드 집합(**String**, **RegExp**, **Math**, **Array**, **Date**)을 통해 Stream Analytics 작업에서 복잡한 데이터 변환을 쉽게 만들 수 있게 되었습니다.

## <a name="overview"></a>개요

JavaScript 사용자 정의 함수는 외부 연결이 필요 없는 상태 비저장, 컴퓨팅 전용 스칼라 함수를 지원합니다. 함수의 반환 값은 스칼라(단일) 값만 될 수 있습니다. JavaScript 사용자 정의 함수를 작업에 추가한 후 기본 제공 스칼라 함수처럼 쿼리의 아무 곳에서나 함수를 사용할 수 있습니다.

다음은 JavaScript 사용자 정의 함수가 유용할 수 있는 몇 가지 시나리오입니다.
* 정규식 함수를 가진 문자열을 구문 분석 및 조작(예: **Regexp_Replace()** 및 **Regexp_Extract()** )
* 데이터 디코딩 및 인코딩(예: 2진을 16진으로 변환)
* JavaScript **Math** 함수를 사용하여 수학 계산을 수행
* 정렬, 조인, 찾기 및 채우기 등의 배열 작업 수행

다음은 Stream Analytics에서 JavaScript 사용자 정의 함수로 수행할 수 없는 작업입니다.
* 외부 REST 엔드포인트 호출(예: 역방향 IP 조회 수행 또는 외부 원본에서 참조 데이터 끌어오기)
* 입력/출력에서 사용자 지정 이벤트 형식 직렬화 또는 역직렬화 수행
* 사용자 지정 집계 만들기

**Date.GetDate()** 또는 **Math.random()** 과 같은 함수는 함수 정의에서 차단되지는 않지만 사용하지 않는 것이 좋습니다. 이러한 함수는 호출할 때마다 **다른** 결과를 반환하며 Azure Stream Analytics 서비스에서 함수 호출 및 반환된 결과 저널을 유지하지 않습니다. 동일한 이벤트에 대해 함수가 다른 결과를 반환하면 사용자 또는 Stream Analytics 서비스에서 작업을 다시 시작할 때 반복성이 보장되지 않습니다.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>작업에 JavaScript 사용자 정의 함수 추가

> [!NOTE]
> 이러한 단계는 클라우드에서 실행하도록 구성된 Stream Analytics 작업에서 작동합니다. Stream Analytics 작업을 Azure IoT Edge에서 실행되도록 구성한 경우, Visual Studio를 대신 사용하고 [C#을 사용하여 사용자 정의 함수를 작성](stream-analytics-edge-csharp-udf.md)합니다.

Stream Analytics 작업에 JavaScript 사용자 정의 함수를 추가하려면 **작업 토폴로지**에서 **함수**를 선택합니다. 그런 다음, **+추가** 드롭다운 메뉴에서 **JavaScript UDF**를 선택합니다. 

![JavaScript UDF 추가](./media/javascript/stream-analytics-jsudf-add.png)

그런 다음 속성을 제공하고 **저장**을 선택해야 합니다.

|속성|Description|
|--------|-----------|
|함수 별칭|쿼리에서 함수를 호출할 이름을 입력합니다.|
|출력 형식|JavaScript 사용자 정의 함수가 Stream Analytics 쿼리에 반환할 형식입니다.|
|함수 정의|쿼리에서 UDF가 호출될 때마다 실행될 JavaScript 함수 구현입니다.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>JavaScript UDF 테스트 및 문제 해결 

모든 브라우저에서 JavaScript UDF 논리를 테스트하고 디버깅할 수 있습니다. 이러한 사용자 정의 함수의 논리 디버깅 및 테스트는 현재 Stream Analytics 포털에서 지원되지 않습니다. 이 함수가 예상대로 작동하면 위에서 설명한 대로 Stream Analytics 작업에 추가한 다음, 쿼리에서 직접 호출할 수 있습니다. [Stream Analytics Tools for Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install)를 사용하여 JavaScript UDF로 쿼리 논리를 테스트할 수 있습니다.

JavaScript 런타임 오류는 치명적인 것으로 간주되고 활동 로그를 통해 표시됩니다. Azure Portal에서 로그를 검색하려면 작업으로 이동하고 **활동 로그**를 선택합니다.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>쿼리에서 JavaScript 사용자 정의 함수 호출

**udf** 접두사로 시작하는 함수 별칭을 사용하여 쿼리에서 JavaScript 함수를 쉽게 호출할 수 있습니다. 다음은 16진수 값을 Stream Analytics 쿼리에서 호출되는 정수로 변환하는 JavaScript UDF의 예입니다.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>지원되는 JavaScript 개체

Azure Stream Analytics JavaScript 사용자 정의 함수는 표준인 기본 제공 JavaScript 개체를 지원합니다. 이러한 개체의 목록은 [전역 개체](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)를 참조하세요.

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics 및 JavaScript 형식 변환

Stream Analytics 쿼리 언어와 JavaScript가 지원하는 형식 간에는 차이가 있습니다. 이 테이블에는 둘 간의 변환 매핑 목록이 나열되어 있습니다.

Stream Analytics | JavaScript
--- | ---
bigint | Number(JavaScript에서는 정확히 최대 2^53의 정수만 표현할 수 있음)
DateTime | Date(JavaScript에서는 밀리초만 지원)
double | Number
nvarchar(MAX) | String
레코드 | Object
Array | Array
NULL | Null

다음은 JavaScript-Stream Analytics 변환입니다.

JavaScript | Stream Analytics
--- | ---
Number | Bigint(숫자를 반올림하여 long.MinValue와 long.MaxValue 사이에 있는 경우, 그렇지 않으면 double임)
Date | DateTime
String | nvarchar(MAX)
Object | 레코드
Array | Array
Null, Undefined | NULL
기타 다른 형식(예: 함수 또는 오류) | 지원되지 않음(런타임 오류 발생)

JavaScript 언어는 대/소문자를 구분하며 JavaScript 코드에서 개체 필드의 대/소문자는 들어오는 데이터의 필드 대/소문자와 일치해야 합니다. 호환성 수준이 1.0인 작업은 SQL SELECT 문의 필드를 소문자로 변환합니다. 호환성 수준 1.1 이상에서 SELECT 문의 필드에는 SQL 쿼리에 지정된 것과 동일한 대/소문자가 있습니다.

## <a name="other-javascript-user-defined-function-patterns"></a>기타 JavaScript 사용자 정의 함수 패턴

### <a name="write-nested-json-to-output"></a>중첩된 JSON을 출력에 작성

Stream Analytics 작업 출력을 입력으로 사용하는 후속 처리 단계가 있고 이것이 JSON 형식을 요구하는 경우 JSON 문자열을 출력에 작성할 수 있습니다. 다음 예에서는 **JSON.stringify()** 함수를 호출하여 입력의 모든 이름/값 쌍을 묶고 출력에 단일 문자열 값으로 작성합니다.

**JavaScript 사용자 정의 함수 정의:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**샘플 쿼리:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

### <a name="cast-string-to-json-object-to-process"></a>처리할 JSON 개체에 문자열 캐스팅

JSON인 문자열 필드가 있고 JavaScript UDF에서 처리하기 위해 JSON 개체로 변환하려는 경우 **JSON.parse()** 함수를 사용하여 이후에 사용할 수 있는 JSON 개체를 만들 수 있습니다.

**JavaScript 사용자 정의 함수 정의:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**샘플 쿼리:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>오류 처리를 위해 try/catch 사용

Try/catch 블록은 JavaScript UDF에 전달되는 잘못된 형식의 입력 데이터 문제를 식별하는 데 도움이 될 수 있습니다.

**JavaScript 사용자 정의 함수 정의:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**샘플 쿼리: 오류가 있는 경우 반환될 수 있도록 전체 레코드를 첫 번째 매개 변수로 전달합니다.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

## <a name="next-steps"></a>다음 단계

* [Machine Learning UDF](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [C# UDF](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
