---
title: Azure Stream Analytics의 JavaScript 사용자 정의 집계
description: 이 문서에서는 Azure Stream Analytics에서 JavaScript 사용자 정의 집계로 고급 쿼리 역학을 수행하는 방법을 설명합니다.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6663e3fc48408de83e92f39e8c8070005818852d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61479561"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics JavaScript 사용자 정의 집계(미리 보기)
 
Azure Stream Analytics는 JavaScript로 작성된 UDA(사용자 정의 집계)를 지원하고 이를 통해 복잡한 상태 저장 비즈니스 논리를 구현할 수 있습니다. UDA 내에서 상태 데이터 구조, 상태 누적, 상태 누적 처분 및 집계 결과 계산의 모든 권한을 갖고 있습니다. 문서에서는 두 개의 서로 다른 JavaScript UDA 인터페이스, UDA를 만드는 단계 및 Stream Analytics 쿼리에서 창 기반 작업으로 UDA를 사용하는 방법을 소개합니다.

## <a name="javascript-user-defined-aggregates"></a>JavaScript 사용자 정의 집계

사용자 정의 집계는 시간 창 사양을 기반으로 해당 창의 이벤트에 대해 집계하고 단일 결과 값을 생성하는 데 사용됩니다. Stream Analytics에서 현재 지원하는 두 가지 유형의 UDA 인터페이스, AccumulateOnly 및 AccumulateDeaccumulate가 있습니다. 두 유형의 UDA는 연속 창, 도약 창 및 슬라이딩 윈도우에서 사용될 수 있습니다. AccumulateDeaccumulate UDA는 도약 창 및 슬라이딩 윈도우와 함께 사용하는 경우 AccumulateOnly UDA보다 더 잘 수행합니다. 사용하는 알고리즘에 따라 두 유형 중 하나를 선택합니다.

### <a name="accumulateonly-aggregates"></a>AccumulateOnly 집계

AccumulateOnly 집계는 해당 상태에 새 이벤트만 누적할 수 있습니다. 알고리즘은 값의 누적 처분을 허용하지 않습니다. 구현할 수 없는 상태 값에서 이벤트 정보를 누적 처분하는 경우 이 집계 유형을 선택합니다. 다음은 AccumulatOnly 집계에 대한 JavaScript 템플릿입니다.

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>AccumulateDeaccumulate 집계

AccumulateDeaccumulate 집계는 상태에서 이전에 누적된 값의 누적 처분을 허용합니다. 예를 들어, 이벤트 값의 목록에서 키-값 쌍을 제거하거나 sum 집계 상태에서 값을 뺍니다. 다음은 AccumulateDeaccumulate 집계에 대한 JavaScript 템플릿입니다.

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA - JavaScript 함수 선언

각 JavaScript UDA는 함수 개체 선언에 의해 정의됩니다. 다음은 UDA 정의의 주요 요소입니다.

### <a name="function-alias"></a>함수 별칭

함수 별칭은 UDA 식별자입니다. Stream Analytics 쿼리에서 호출되는 경우 항상 “uda”와 함께 UDA 별칭을 사용합니다. 식별됩니다.

### <a name="function-type"></a>함수 유형

UDA의 경우 함수 유형은 **Javascript UDA**여야 합니다.

### <a name="output-type"></a>출력 형식

Stream Analytics 작업에서 지원한 특정 유형 또는 쿼리에서 형식을 처리하려는 경우 "Any"

### <a name="function-name"></a>함수 이름

이 함수 개체의 이름입니다. 함수 이름은 UDA 별칭과 문자 그대로 일치해야 합니다(미리 보기 동작, GA 시 익명 함수 지원 고려 중).

### <a name="method---init"></a>메서드 - init()

Init() 메서드는 집계 상태를 초기화합니다. 이 메서드는 창이 시작될 때 호출됩니다.

### <a name="method--accumulate"></a>메서드 – accumulate()

accumulate() 메서드는 이전 상태 및 현재 이벤트 값에 따라 UDA 상태를 계산합니다. 이 메서드는 이벤트가 시간 창에 진입하는 경우 호출됩니다(TUMBLINGWINDOW, HOPPINGWINDOW 또는 SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>메서드 - deaccumulate()

deaccumulate() 메서드는 이전 상태 및 현재 이벤트 값에 따라 상태를 다시 계산합니다. 이 메서드는 이벤트가 SLIDINGWINDOW를 벗어나는 경우 호출됩니다.

### <a name="method--deaccumulatestate"></a>메서드 - deaccumulateState()

deaccumulateState() 메서드는 이전 상태 및 홉의 상태에 따라 상태를 다시 계산합니다. 이 메서드는 이벤트 집합이 HOPPINGWINDOW를 벗어나는 경우 호출됩니다.

### <a name="method--computeresult"></a>메서드 - computeResult()

computeResult() 메서드는 현재 상태에 따라 집계 결과를 반환합니다. 이 메서드는 시간 창의 끝에 호출됩니다(TUMBLINGWINDOW, HOPPINGWINDOW 및 SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA 지원되는 입력 및 출력 데이터 형식
JavaScript UDA 데이터 형식의 경우 [JavaScript UDF 통합](stream-analytics-javascript-user-defined-functions.md)의 **Stream Analytics 및 JavaScript 형식 변환** 섹션을 참조하세요.

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Azure Portal에서 JavaScript UDA 추가

다음은 포털에서 UDA를 만드는 과정을 단계별로 안내합니다. 여기에서 사용하는 예제는 시간 가중치 평균을 계산합니다.

이제 단계를 따라 기존 ASA 작업에서 JavaScript UDA를 만들어 보겠습니다.

1. Azure Portal에 로그인하고 기존 Stream Analytics 작업을 찾습니다.
1. 그런 다음 **작업 토폴로지** 아래에서 함수 링크를 클릭합니다.
1. **추가** 아이콘을 클릭하여 새 함수를 추가합니다.
1. 새 함수 보기에서 함수 유형으로 **JavaScript UDA**를 선택하면 편집기에 기본 UDA 템플릿이 표시됩니다.
1. UDA 별칭으로 "TWA"를 입력하고 함수 구현을 다음과 같이 변경합니다.

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. “저장” 단추를 클릭하면 UDA가 함수 목록에 나타납니다.

1. 새 함수 “TWA”를 클릭하면 함수 정의를 확인할 수 있습니다.

## <a name="calling-javascript-uda-in-asa-query"></a>ASA 쿼리에서 JavaScript UDA 호출

Azure Portal에서 작업을 열고, 쿼리를 편집하고, 자동 인출 접두사 “uda”로 TWA() 함수를 호출합니다. 예를 들면 다음과 같습니다.

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>UDA를 사용하여 쿼리 테스트

아래 콘텐츠로 로컬 JSON 파일을 만들고, 파일을 Stream Analytics 작업에 업로드하고, 위의 쿼리를 테스트합니다.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>도움말 보기

추가 도움이 필요할 경우 [Azure Stream Analytics 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
