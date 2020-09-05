---
title: Azure Stream Analytics의 일반적인 쿼리 패턴
description: 이 문서에서는 Azure Stream Analytics 작업에 유용한 여러 일반 쿼리 패턴 및 디자인을 설명합니다.
services: stream-analytics
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/18/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: 0eabe147563ee712e20e57aafc1029daf2a8610a
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641044"
---
# <a name="common-query-patterns-in-azure-stream-analytics"></a>Azure Stream Analytics의 일반적인 쿼리 패턴

Azure Stream Analytics에서 쿼리는 SQL 방식 쿼리 언어로 표현됩니다. 언어 구문은 [Stream Analytics 쿼리 언어 참조](/stream-analytics-query/stream-analytics-query-language-reference) 가이드에 설명되어 있습니다. 

쿼리 디자인은 이벤트 데이터를 하나의 입력 스트림에서 다른 출력 데이터 저장소로 옮기는 간단한 통과 논리를 표현하거나 풍부한 패턴 일치 및 temporal 분석을 수행하여 다양한 시간대에 걸친 집계를 계산([Stream Analytics를 사용하여 IoT 솔루션 빌드](stream-analytics-build-an-iot-solution-using-stream-analytics.md) 가이드 참조)할 수 있습니다. 여러 입력의 데이터를 조인하여 스트리밍 이벤트를 결합하고, 정적 참조 데이터를 조회하여 이벤트 값을 보강할 수 있습니다. 여러 출력에 데이터를 쓸 수도 있습니다.

이 문서에서는 실제 시나리오에 따른 몇 가지 일반적인 쿼리 패턴에 대한 솔루션을 간략하게 설명합니다.

## <a name="supported-data-formats"></a>지원되는 데이터 형식

Azure Stream Analytics는 CSV, JSON 및 Avro 데이터 형식의 이벤트 처리를 지원합니다.

JSON 및 Avro는 둘 다 중첩된 개체(레코드) 또는 배열과 같은 복합 형식을 포함할 수 있습니다. 이러한 복잡한 데이터 형식 작업에 대한 자세한 내용은 [JSON 구문 분석 및 AVRO 데이터](stream-analytics-parsing-json.md) 문서를 참조하세요.

## <a name="send-data-to-multiple-outputs"></a>데이터를 여러 출력으로 전송

여러 **SELECT** 문을 사용하여 데이터를 여러 출력 싱크로 출력할 수 있습니다. 예를 들어 하나의 **SELECT**는 임계값 기반 경고를 출력하고 다른 하나는 이벤트를 Blob Storage에 출력할 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:03.0000000Z |

**Output ArchiveOutput**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:03.0000000Z |

**Output AlertOutput**:

| 계정을 | Time | 개수 |
| --- | --- | --- |
| Make2 |2015-01-01T00:오전 12:10.0000000Z |3 |

**쿼리**:

```SQL
SELECT
    *
INTO
    ArchiveOutput
FROM
    Input TIMESTAMP BY Time

SELECT
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count]
INTO
    AlertOutput
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING
    [Count] >= 3
```

**INTO** 절은 데이터를 쓸 출력을 Stream Analytics에 알립니다. 첫 번째 **SELECT**는 입력에서 데이터를 수신하고 **ArchiveOutput**이라는 출력으로 전송하는 통과 쿼리를 정의합니다. 두 번째 쿼리는 일부 간단한 집계와 필터링을 하고 **AlertOutput**이라는 다운스트림 경고 시스템 출력에 결과를 보냅니다.

**WITH** 절을 사용하여 여러 하위 쿼리 블록을 정의할 수 있습니다. 이 옵션에는 입력 원본에 대해 더 적은 판독기를 여는 이점이 있습니다.

**쿼리**:

```SQL
WITH ReaderQuery AS (
    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
)

SELECT * INTO ArchiveOutput FROM ReaderQuery

SELECT 
    Make,
    System.TimeStamp() AS Time,
    COUNT(*) AS [Count] 
INTO AlertOutput 
FROM ReaderQuery
GROUP BY
    Make,
    TumblingWindow(second, 10)
HAVING [Count] >= 3
```

자세한 내용은 [**WITH** 절](/stream-analytics-query/with-azure-stream-analytics)을 참조하세요.

## <a name="simple-pass-through-query"></a>간단한 통과 쿼리

간단한 통과 쿼리를 사용하여 입력 스트림 데이터를 출력에 복사할 수 있습니다. 예를 들어 문자 분석을 위한 SQL 데이터베이스에 실시간 차량 정보를 포함하는 데이터 스트림을 저장해야 하는 경우 간단한 통과 쿼리로 작업을 수행합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**출력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**쿼리**:

```SQL
SELECT
    *
INTO Output
FROM Input
```

**SELECT** * 쿼리는 들어오는 이벤트의 모든 필드를 프로젝션하여 출력으로 보냅니다. 동일한 방식으로 **SELECT**를 사용하여 입력의 필수 필드만 프로젝션하는 데 사용할 수도 있습니다. 다음 예제에서는 자동차 *Make* 및 *Time*이 저장할 유일한 필수 필드인 경우 해당 필드를 **SELECT** 문에 지정할 수 있습니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |1000 |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |2000 |
| Make2 |2015-01-01T00:오전 12:04.0000000Z |1500 |

**출력**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:04.0000000Z |

**쿼리**:

```SQL
SELECT
    Make, Time
INTO Output
FROM Input
```

## <a name="string-matching-with-like-and-not-like"></a>LIKE 및 NOT LIKE를 사용한 문자열 일치

**LIKE** 및 **NOT LIKE**는 필드가 특정 패턴과 일치하는지 확인하는 데 사용할 수 있습니다. 예를 들어 문자 ‘A’로 시작하고 숫자 9로 끝나는 번호판만 반환하도록 필터를 만들 수 있습니다.

**입력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:오전 12:03.0000000Z |

**출력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| Make2 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| Make3 |ABC-369 |2015-01-01T00:오전 12:03.0000000Z |

**쿼리**:

```SQL
SELECT
    *
FROM
    Input TIMESTAMP BY Time
WHERE
    License_plate LIKE 'A%9'
```

**LIKE** 문을 사용하여 **License_plate** 필드 값을 확인합니다. 문자 ‘A’로 시작한 다음, 0개 이상의 문자열을 포함하고 숫자 9로 끝나야 합니다.

## <a name="calculation-over-past-events"></a>과거 이벤트에 대한 계산

**LAG** 함수를 사용하여 시간대 내에서 과거 이벤트를 확인하고 현재 이벤트와 비교할 수 있습니다. 예를 들어 현재 자동차 브랜드가 톨게이트를 통과했던 마지막 자동차와 다를 경우 출력될 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |

**출력**:

| 계정을 | Time |
| --- | --- |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |

**쿼리**:

```SQL
SELECT
    Make,
    Time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make
```

**LAG**를 사용하여 한 이벤트 전의 입력 스트림에 피킹하고 *Make* 값을 검색한 후 현재 이벤트의 *Make* 값을 비교하여 이벤트를 출력합니다.

자세한 내용은 [**LAG**](/stream-analytics-query/lag-azure-stream-analytics)를 참조하세요.

## <a name="return-the-last-event-in-a-window"></a>한 시간대에서 마지막 이벤트 반환

이벤트가 실시간으로 시스템에서 사용되는 경우 이벤트가 해당 시간대에서 마지막으로 도착하는 이벤트인지를 결정할 수 있는 함수가 없습니다. 이를 수행하려면 입력 스트림은 이벤트 시간이 해당 시간대의 모든 이벤트에 대한 최대 시간인 또 다른 스트림에 조인해야 합니다.

**입력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:오전 2:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:오전 5:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:오전 6:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:오전 9:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:오후 1:45.0000000Z |

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| VFE 1616 |Make2 |2015-07-27T00:오전 9:31.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:오후 1:45.0000000Z |

**쿼리**:

```SQL
WITH LastInWindow AS
(
    SELECT 
        MAX(Time) AS LastEventTime
    FROM 
        Input TIMESTAMP BY Time
    GROUP BY 
        TumblingWindow(minute, 10)
)

SELECT 
    Input.License_plate,
    Input.Make,
    Input.Time
FROM
    Input TIMESTAMP BY Time 
    INNER JOIN LastInWindow
    ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
    AND Input.Time = LastInWindow.LastEventTime
```

쿼리의 첫 번째 단계는 10분 동안 최대 타임스탬프를 찾습니다. 이 타임스탬프는 해당 시간대에서 마지막 이벤트의 타임스탬프입니다. 두 번째 단계는 각 시간대의 최신 타임스탬프와 일치하는 이벤트를 찾기 위해 원본 스트림을 사용한 첫 번째 쿼리의 결과를 조인합니다. 

**DATEDIFF**는 두 개의 DateTime 필드 간에 시간 차이를 비교하고 반환하는 날짜 관련 함수입니다. 자세한 내용은 [날짜 함수](https://docs.microsoft.com/stream-analytics-query/date-and-time-functions-azure-stream-analytics)를 참조하세요.

스트림 조인에 대한 자세한 내용은 [**JOIN**](/stream-analytics-query/join-azure-stream-analytics)을 참조하세요.

## <a name="data-aggregation-over-time"></a>시간별 데이터 집계

시간대에 대한 정보를 계산하기 위해 데이터를 함께 집계할 수 있습니다. 이 예에서는 특정 자동차를 만들 때마다 지난 10 초 동안 카운트를 계산 합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |1000 |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |2000 |
| Make2 |2015-01-01T00:오전 12:04.0000000Z |1500 |

**출력**:

| 계정을 | 개수 |
| --- | --- |
| Make1 | 2 |
| Make2 | 1 |

**쿼리**:

```SQL
SELECT
    Make,
    COUNT(*) AS Count
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

이 집계는 *Make*별로 자동차를 그룹화하고 10초마다 계산합니다. 출력에는 톨게이트를 통과한 차량의 *Make* 및 *Count*가 있습니다.

TumblingWindow는 이벤트를 그룹화하는 데 사용되는 기간 이동 함수입니다. 그룹화된 모든 이벤트에 대해 집계를 적용할 수 있습니다. 자세한 내용은 [기간 이동 함수](stream-analytics-window-functions.md)를 참조하세요.

집계에 대한 자세한 내용은 [집계 함수](/stream-analytics-query/aggregate-functions-azure-stream-analytics)를 참조하세요.

## <a name="periodically-output-values"></a>정기적으로 값 출력

비정상적인 이벤트 또는 누락된 이벤트의 경우 좀 더 드문 데이터 입력에서 정기적인 간격의 출력이 생성될 수 있습니다. 예를 들어, 가장 최근 표시된 데이터 지점을 보고하도록 5초마다 이벤트를 생성합니다.

**입력**:

| Time | 값 |
| --- | --- |
| "2014-01-01T06:01:00" |1 |
| "2014-01-01T06:오전 1:05" |2 |
| "2014-01-01T06:오전 1:10" |3 |
| "2014-01-01T06:오전 1:15" |4 |
| "2014-01-01T06:오전 1:30" |5 |
| "2014-01-01T06:오전 1:35" |6 |

**출력(처음 10개 행)** :

| Window_end | Last_event.Time | Last_event.Value |
| --- | --- | --- |
| 2014-01-01T14:오전 1:00.000Z |2014-01-01T14:오전 1:00.000Z |1 |
| 2014-01-01T14:오전 1:05.000Z |2014-01-01T14:오전 1:05.000Z |2 |
| 2014-01-01T14:오전 1:10.000Z |2014-01-01T14:오전 1:10.000Z |3 |
| 2014-01-01T14:오전 1:15.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:20.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:25.000Z |2014-01-01T14:오전 1:15.000Z |4 |
| 2014-01-01T14:오전 1:30.000Z |2014-01-01T14:오전 1:30.000Z |5 |
| 2014-01-01T14:오전 1:35.000Z |2014-01-01T14:오전 1:35.000Z |6 |
| 2014-01-01T14:오전 1:40.000Z |2014-01-01T14:오전 1:35.000Z |6 |
| 2014-01-01T14:오전 1:45.000Z |2014-01-01T14:오전 1:35.000Z |6 |

**쿼리**:

```SQL
SELECT
    System.Timestamp() AS Window_end,
    TopOne() OVER (ORDER BY Time DESC) AS Last_event
FROM
    Input TIMESTAMP BY Time
GROUP BY
    HOPPINGWINDOW(second, 300, 5)
```

이 쿼리는 5초마다 이벤트를 생성하고 이전에 수신했던 마지막 이벤트를 출력합니다. **HOPPINGWINDOW** 기간은 쿼리가 과거의 어느 시점까지 최신 이벤트를 찾는지를 결정합니다.

자세한 내용은 [도약 창](/stream-analytics-query/hopping-window-azure-stream-analytics)을 참조하세요.

## <a name="correlate-events-in-a-stream"></a>스트림의 이벤트 상관 관계

**LAG** 함수를 사용하여 과거 이벤트를 살펴보면 동일한 스트림의 이벤트 상관 관계를 지정할 수 있습니다. 예를 들어 최근 90초 동안 동일한 ‘브랜드’의 차량이 두 대 연속으로 톨게이트를 통과할 때 출력을 생성할 수 있습니다.

**입력**:

| 계정을 | License_plate | Time |
| --- | --- | --- |
| Make1 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |
| Make1 |AAA-999 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |DEF-987 |2015-01-01T00:오전 12:03.0000000Z |
| Make1 |GHI-345 |2015-01-01T00:오전 12:04.0000000Z |

**출력**:

| 계정을 | Time | Current_car_license_plate | First_car_license_plate | First_car_time |
| --- | --- | --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |AAA-999 |ABC-123 |2015-01-01T00:오전 12:01.0000000Z |

**쿼리**:

```SQL
SELECT
    Make,
    Time,
    License_plate AS Current_car_license_plate,
    LAG(License_plate, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_license_plate,
    LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS First_car_time
FROM
    Input TIMESTAMP BY Time
WHERE
    LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make
```

**LAG** 함수는 한 이벤트 전의 입력 스트림을 다시 확인하고 *Make* 값을 검색하여 현재 이벤트의 *Make* 값과 비교할 수 있습니다.  조건을 충족하면 이전 이벤트의 데이터를 **SELECT** 문의 **LAG**를 사용하여 프로젝션할 수 있습니다.

자세한 내용은 [LAG](/stream-analytics-query/lag-azure-stream-analytics)를 참조하세요.

## <a name="detect-the-duration-between-events"></a>이벤트 간 기간 검색

이벤트의 기간은 End 이벤트가 수신되면 마지막 Start 이벤트를 확인하여 계산할 수 있습니다. 이 쿼리는 사용자가 페이지 또는 기능에 소비한 시간을 확인하는 데 유용할 수 있습니다.

**입력**:  

| 사용자 | 기능 | 이벤트 | Time |
| --- | --- | --- | --- |
| user@location.com |RightMenu |시작 |2015-01-01T00:오전 12:01.0000000Z |
| user@location.com |RightMenu |끝 |2015-01-01T00:오전 12:08.0000000Z |

**출력**:  

| 사용자 | 기능 | Duration |
| --- | --- | --- |
| user@location.com |RightMenu |7 |

**쿼리**:

```SQL
SELECT
    [user],
    feature,
    DATEDIFF(
        second,
        LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'),
        Time) as duration
FROM input TIMESTAMP BY Time
WHERE
    Event = 'end'
```

**LAST** 함수는 특정 조건 내에서 마지막 이벤트를 검색하는 데 사용할 수 있습니다. 이 예제에서 조건은 Start 형식의 이벤트이며 **PARTITION BY** 사용자 및 기능을 사용하여 검색을 분할합니다. 이렇게 하면 모든 사용자 및 기능이 Start 이벤트를 검색할 때 독립적으로 처리됩니다. **LIMIT DURATION**은 End 이벤트와 Start 이벤트 간에 1시간 전으로 검색을 제한합니다.

## <a name="count-unique-values"></a>고유 값 카운트

**COUNT** 및 **DISTINCT**를 사용하여 시간대 내에 스트림에 나타나는 고유 필드 값의 수를 계산할 수 있습니다. 2초 안에 톨게이트 요금소를 통과한 자동차의 고유한 ‘브랜드’는 몇 개인지 계산하는 쿼리를 생성할 수 있습니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:03.0000000Z |

**출력:**

| Count_make | Time |
| --- | --- |
| 2 |2015-01-01T00:오전 12:02.000Z |
| 1 |2015-01-01T00:오전 12:04.000Z |

**쿼리:**

```SQL
SELECT
     COUNT(DISTINCT Make) AS Count_make,
     System.TIMESTAMP() AS Time
FROM Input TIMESTAMP BY TIME
GROUP BY 
     TumblingWindow(second, 2)
```

**COUNT(DISTINCT Make)** 는 시간대 내에서 **Make** 열의 고유한 값의 수를 반환합니다.
자세한 내용은 [**COUNT** 집계 함수](/stream-analytics-query/count-azure-stream-analytics)를 참조하세요.

## <a name="retrieve-the-first-event-in-a-window"></a>창에서 첫 번째 이벤트 검색

**IsFirst**는 시간대에서 첫 번째 이벤트를 검색하는 데 사용할 수 있습니다. 예를 들어 10분 간격으로 첫 번째 자동차 정보를 출력합니다.

**입력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:오전 2:17.0000000Z |
| RMV 8282 |Make1 |2015-07-27T00:오전 5:01.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:오전 6:00.0000000Z |
| VFE 1616 |Make2 |2015-07-27T00:오전 9:31.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:오후 1:45.0000000Z |

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:오전 12:05.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:오후 12:02.0000000Z |

**쿼리**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) = 1
```

**IsFirst**는 데이터를 분할하고 각 특정 자동차 ‘브랜드’에 대한 첫 번째 이벤트를 10분 간격으로 계산할 수 있습니다.

**출력**:

| License_plate | 계정을 | Time |
| --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:오전 12:05.0000000Z |
| YZK 5704 |Make3 |2015-07-27T00:오전 2:17.0000000Z |
| YHN 6970 |Make2 |2015-07-27T00:오전 6:00.0000000Z |
| QYF 9358 |Make1 |2015-07-27T00:오후 12:02.0000000Z |
| MDR 6128 |Make4 |2015-07-27T00:오후 1:45.0000000Z |

**쿼리**:

```SQL
SELECT 
    License_plate,
    Make,
    Time
FROM 
    Input TIMESTAMP BY Time
WHERE 
    IsFirst(minute, 10) OVER (PARTITION BY Make) = 1
```

자세한 내용은 [**IsFirst**](/stream-analytics-query/isfirst-azure-stream-analytics)를 참조하세요.

## <a name="remove-duplicate-events-in-a-window"></a>창에서 중복된 이벤트 제거

지정된 시간 범위에서 이벤트에 대한 평균 계산과 같은 작업을 수행하는 경우 중복 이벤트를 필터링해야 합니다. 다음 예제에서 두 번째 이벤트는 첫 번째 이벤트와 중복됩니다.

**입력**:  

| deviceId | Time | attribute | 값 |
| --- | --- | --- | --- |
| 1 |2018-07-27T00:00:01.0000000Z |온도 |50 |
| 1 |2018-07-27T00:00:01.0000000Z |온도 |50 |
| 2 |2018-07-27T00:00:01.0000000Z |온도 |40 |
| 1 |2018-07-27T00:00:05.0000000Z |온도 |60 |
| 2 |2018-07-27T00:00:05.0000000Z |온도 |50 |
| 1 |2018-07-27T00:00:10.0000000Z |온도 |100 |

**출력**:  

| AverageValue | deviceId |
| --- | --- |
| 70 | 1 |
|45 | 2 |

**쿼리**:

```SQL
With Temp AS (
SELECT
    COUNT(DISTINCT Time) AS CountTime,
    Value,
    DeviceId
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Value,
    DeviceId,
    SYSTEM.TIMESTAMP()
)

SELECT
    AVG(Value) AS AverageValue, DeviceId
INTO Output
FROM Temp
GROUP BY DeviceId,TumblingWindow(minute, 5)
```

**COUNT(DISTINCT Time)** 는 시간 범위 내에서 Time 열의 고유한 값 수를 반환합니다. 그러면 중복 항목을 삭제하여 디바이스당 평균을 계산하는 데 이 단계의 출력을 사용할 수 있습니다.

자세한 내용은 [COUNT(DISTINCT Time)](/stream-analytics-query/count-azure-stream-analytics)를 참조하세요.

## <a name="specify-logic-for-different-casesvalues-case-statements"></a>다른 사례/값(CASE 문)에 대한 논리를 지정합니다.

**CASE** 문에서는 특정 기준에 따라 서로 다른 필드에 대해 다른 계산을 제공할 수 있습니다. 예를 들어 차선 ‘A’를 *Make1*의 자동차에 할당하고 차선 ‘B’를 다른 브랜드에 할당합니다.

**입력**:

| 계정을 | Time |
| --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |
| Make2 |2015-01-01T00:오전 12:03.0000000Z |

**출력**:

| 계정을 |Dispatch_to_lane | Time |
| --- | --- | --- |
| Make1 |“A” |2015-01-01T00:오전 12:01.0000000Z |
| Make2 |“B” |2015-01-01T00:오전 12:02.0000000Z |

**솔루션**:

```SQL
SELECT
    Make
    CASE
        WHEN Make = "Make1" THEN "A"
        ELSE "B"
    END AS Dispatch_to_lane,
    System.TimeStamp() AS Time
FROM
    Input TIMESTAMP BY Time
```

**CASE** 식은 하나의 식을 일련의 간단한 식과 비교하여 그 결과를 결정합니다. 이 예제에서 *Make1*의 차량은 차선 ‘A’로 보내고 다른 모든 브랜드의 자동차에는 차선 ‘B’가 할당됩니다.

자세한 내용은 [CASE 식](/stream-analytics-query/case-azure-stream-analytics)을 참조하세요.

## <a name="data-conversion"></a>데이터 변환

**CAST** 메서드를 사용하여 실시간으로 데이터를 캐스팅할 수 있습니다. 예를 들어 자동차 중량은 **nvarchar(max)** 형식에서 **bigint** 형식으로 변환하여 숫자 계산에 사용할 수 있습니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |"1000" |
| Make1 |2015-01-01T00:오전 12:02.0000000Z |"2000" |

**출력**:

| 계정을 | 무게 |
| --- | --- |
| Make1 |3000 |

**쿼리**:

```SQL
SELECT
    Make,
    SUM(CAST(Weight AS BIGINT)) AS Weight
FROM
    Input TIMESTAMP BY Time
GROUP BY
    Make,
    TumblingWindow(second, 10)
```

**CAST** 문을 사용하여 데이터 형식을 지정합니다. [데이터 형식(Azure Stream Analytics)](/stream-analytics-query/data-types-azure-stream-analytics)에서 지원되는 데이터 형식의 목록을 참조하세요.

자세한 내용은 [데이터 변환 함수](/stream-analytics-query/conversion-functions-azure-stream-analytics)를 참조하세요.

## <a name="detect-the-duration-of-a-condition"></a>조건의 기간 감지

여러 이벤트를 사용하는 조건의 경우 **LAG** 함수를 사용하여 해당 조건의 기간을 파악할 수 있습니다. 예를 들어 버그로 인해 모든 자동차의 중량이 잘못되었고(20.000파운드 이상) 버그의 지속 시간을 계산해야 한다고 가정합니다.

**입력**:

| 계정을 | Time | 무게 |
| --- | --- | --- |
| Make1 |2015-01-01T00:오전 12:01.0000000Z |2000 |
| Make2 |2015-01-01T00:오전 12:02.0000000Z |25000 |
| Make1 |2015-01-01T00:오전 12:03.0000000Z |26000 |
| Make2 |2015-01-01T00:오전 12:04.0000000Z |25000 |
| Make1 |2015-01-01T00:오전 12:05.0000000Z |26000 |
| Make2 |2015-01-01T00:오전 12:06.0000000Z |25000 |
| Make1 |2015-01-01T00:오전 12:07.0000000Z |26000 |
| Make2 |2015-01-01T00:오전 12:08.0000000Z |2000 |

**출력**:

| Start_fault | End_fault |
| --- | --- |
| 2015-01-01T00:오전 12:02.000Z |2015-01-01T00:오전 12:07.000Z |

**쿼리**:

```SQL
WITH SelectPreviousEvent AS
(
SELECT
    *,
    LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previous_time,
    LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previous_weight
FROM input TIMESTAMP BY [time]
)

SELECT 
    LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previous_weight < 20000 ) [Start_fault],
    previous_time [End_fault]
FROM SelectPreviousEvent
WHERE
    [weight] < 20000
    AND previous_weight > 20000
```
첫 번째 **SELECT** 문은 현재 중량 측정치와 이전 측정치의 상관 관계를 계산하여 현재 측정치와 함께 프로젝션합니다. 두 번째 **SELECT**는 *previous_weight*가 20,000보다 작고 현재 중량이 20,000보다 작으며 현재 이벤트의 *previous_weight*가 20,000보다 컸던 마지막 이벤트를 검토합니다.

End_fault는 이전 이벤트에 오류가 발생했던 현재 오류가 발생하지 않은 이벤트이며, Start_fault는 그 이전에 오류가 발생하지 않은 마지막 이벤트입니다.

## <a name="process-events-with-independent-time-substreams"></a>독립된 시간으로 이벤트 처리(하위 스트림)

이벤트 생성자 간의 클록 스큐, 파티션 간 클럭 스큐 또는 네트워크 대기 시간으로 인해 이벤트가 늦게 도착하거나 순서대로 도착하지 못할 수 있습니다.
예를 들어 *TollID* 2의 디바이스 클록은 *TollID* 1보다 5초 늦고, *TollID* 3의 디바이스 클록은 *TollID* 1보다 10초 늦습니다. 각 톨게이트를 대상으로 계산이 독립적으로 수행될 수 있고 자체 클록 데이터만 타임스탬프로 간주합니다.

**입력**:

| LicensePlate | 계정을 | Time | TollId |
| --- | --- | --- | --- |
| DXE 5291 |Make1 |2015-07-27T00:00:01.0000000Z | 1 |
| YHN 6970 |Make2 |2015-07-27T00:오전 12:05.0000000Z | 1 |
| QYF 9358 |Make1 |2015-07-27T00:00:01.0000000Z | 2 |
| GXF 9462 |Make3 |2015-07-27T00:00:04.0000000Z | 2 |
| VFE 1616 |Make2 |2015-07-27T00:00:10.0000000Z | 1 |
| RMV 8282 |Make1 |2015-07-27T00:00:03.0000000Z | 3 |
| MDR 6128 |Make3 |2015-07-27T00:00:11.0000000Z | 2 |
| YZK 5704 |Make4 |2015-07-27T00:00:07.0000000Z | 3 |

**출력**:

| TollId | 개수 |
| --- | --- |
| 1 | 2 |
| 2 | 2 |
| 1 | 1 |
| 3 | 1 |
| 2 | 1 |
| 3 | 1 |

**쿼리**:

```SQL
SELECT
      TollId,
      COUNT(*) AS Count
FROM input
      TIMESTAMP BY Time OVER TollId
GROUP BY TUMBLINGWINDOW(second, 5), TollId
```

**TIMESTAMP BY OVER** 절은 하위 스트림을 사용하여 각 디바이스 타임라인을 독립적으로 살펴봅니다. 각 *TollID*에 대한 출력 이벤트는 계산될 때 생성됩니다. 즉, 모든 디바이스가 동일한 시계를 사용하는 것처럼 순서가 재배열되는 대신 이벤트가 각 *TollID*에 관해 순서대로 처리됩니다.

자세한 내용은 [TIMESTAMP BY OVER](/stream-analytics-query/timestamp-by-azure-stream-analytics#over-clause-interacts-with-event-ordering)를 참조하세요.

## <a name="session-windows"></a>세션 창

세션 창은 이벤트 발생 시 계속 확장되고 특정 시간 이후 이벤트가 수신되지 않거나 창이 최대 지속 시간에 도달한 경우 계산을 위해 닫히는 창입니다.
이 창은 사용자 상호 작용 데이터를 계산할 때 특히 유용합니다. 사용자가 시스템과의 상호 작용을 시작하면 창이 시작되고 더 이상 이벤트가 관찰되지 않는 경우(즉, 사용자가 상호 작용을 중지한 경우) 창이 닫힙니다.
예를 들어 클릭 수가 기록되는 웹 페이지에서 사용자가 상호 작용하는 경우 세션 창을 사용하여 사용자가 사이트와 상호 작용한 시간을 확인할 수 있습니다.

**입력**:

| User_id | Time | URL |
| --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | "www.example.com/a.html" |
| 0 | 2017-01-26T00:00:20.0000000Z | "www.example.com/b.html" |
| 1 | 2017-01-26T00:00:55.0000000Z | "www.example.com/c.html" |
| 0 | 2017-01-26T00:01:10.0000000Z | "www.example.com/d.html" |
| 1 | 2017-01-26T00:01:15.0000000Z | "www.example.com/e.html" |

**출력**:

| User_id | StartTime | EndTime | Duration_in_seconds |
| --- | --- | --- | --- |
| 0 | 2017-01-26T00:00:00.0000000Z | 2017-01-26T00:01:10.0000000Z | 70 |
| 1 | 2017-01-26T00:00:55.0000000Z | 2017-01-26T00:01:15.0000000Z | 20 |

**쿼리**:

``` SQL
SELECT
    user_id,
    MIN(time) as StartTime,
    MAX(time) as EndTime,
    DATEDIFF(second, MIN(time), MAX(time)) AS duration_in_seconds
FROM input TIMESTAMP BY time
GROUP BY
    user_id,
    SessionWindow(minute, 1, 60) OVER (PARTITION BY user_id)
```

**SELECT**는 상호 작용 기간과 함께 사용자 상호 작용과 관련된 데이터를 프로젝션합니다. 1분 이내에 상호 작용이 발생하지 않는 경우 닫히는 **SessionWindow**(최대 창 크기 60분)와 사용자를 기준으로 데이터를 그룹화합니다.

**SessionWindow**에 대한 자세한 내용은 [세션 창](/stream-analytics-query/session-window-azure-stream-analytics)을 참조하세요.

## <a name="language-extensibility-with-user-defined-function-in-javascript-and-c"></a>JavaScript 및 C#에서 사용자 정의 함수가 포함된 언어 확장성

JavaScript 또는 C# 언어로 작성된 사용자 지정 함수로 Azure Stream Analytics 쿼리 언어를 확장할 수 있습니다. UDF(사용자 정의 함수)는 **SQL** 언어를 사용하여 쉽게 표현할 수 없는 사용자 지정/복잡한 계산입니다. 이러한 UDF를 한 번 정의하고 쿼리 내에서 여러 번 사용할 수 있습니다. 예를 들어 UDF를 사용하여 16진수 *nvarchar(max)* 값을 *bigint* 값으로 변환할 수 있습니다.

**입력**:

| Device_id | HexValue |
| --- | --- |
| 1 | "B4" |
| 2 | "11B" |
| 3 | "121" |

**출력**:

| Device_id | Decimal |
| --- | --- |
| 1 | 180 |
| 2 | 283 |
| 3 | 289 |

```JavaScript
function hex2Int(hexValue){
    return parseInt(hexValue, 16);
}
```

```C#
public static class MyUdfClass {
    public static long Hex2Int(string hexValue){
        return int.Parse(hexValue, System.Globalization.NumberStyles.HexNumber);
    }
}
```

```SQL
SELECT
    Device_id,
    udf.Hex2Int(HexValue) AS Decimal
From
    Input
```

사용자 정의 함수는 사용되는 모든 이벤트에 대해 HexValue의 *bigint* 값을 계산합니다.

자세한 내용은 [JavaScript](/azure/stream-analytics/stream-analytics-javascript-user-defined-functions) 및 [C#](/azure/stream-analytics/stream-analytics-edge-csharp-udf)을 참조하세요.

## <a name="advanced-pattern-matching-with-match_recognize"></a>MATCH_RECOGNIZE를 사용한 고급 패턴 일치

**MATCH_RECOGNIZE**는 잘 정의된 정규식 패턴에 이벤트 시퀀스를 일치시키는 데 사용할 수 있는 고급 패턴 일치 메커니즘입니다.
예를 들어 ATM은 실시간으로 고장이 모니터링되며 ATM 작동 중에 두 개의 연속된 경고 메시지가 있는 경우 관리자에게 알려야 합니다.

**입력**:

| ATM_id | Operation_id | Return_Code | Time |
| --- | --- | --- | --- |
| 1 | "Entering Pin" | "Success" | 2017-01-26T00:10:00.0000000Z |
| 2 | "Opening Money Slot" | "Success" | 2017-01-26T00:10:07.0000000Z |
| 2 | "Closing Money Slot" | "Success" | 2017-01-26T00:10:11.0000000Z |
| 1 | "Entering Withdraw Quantity" | "Success" | 2017-01-26T00:10:08.0000000Z |
| 1 | "Opening Money Slot" | "Warning" | 2017-01-26T00:10:14.0000000Z |
| 1 | "Printing Bank Balance" | "Warning" | 2017-01-26T00:10:19.0000000Z |

**출력**:

| ATM_id | First_Warning_Operation_id | Warning_Time |
| --- | --- | --- |
| 1 | "Opening Money Slot" | 2017-01-26T00:10:14.0000000Z |

```SQL
SELECT *
FROM input TIMESTAMP BY time OVER ATM_id
MATCH_RECOGNIZE (
    LIMIT DURATION(minute, 1)
    PARTITON BY ATM_id
    MEASURES
        First(Warning.ATM_id) AS ATM_id,
        First(Warning.Operation_Id) AS First_Warning_Operation_id,
        First(Warning.Time) AS Warning_Time
    AFTER MATCH SKIP TO NEXT ROW
    PATTERN (Success* Warning{2,})
    DEFINE
        Success AS Succes.Return_Code = 'Success',
        Failure AS Warning.Return_Code <> 'Success'
) AS patternMatch
```

이 쿼리는 두 개 이상의 연속된 고장 이벤트를 일치시키고 조건이 충족되면 경보를 생성합니다.
**PATTERN**은 일치에 사용할 정규식을 정의합니다. 이 경우에는 성공한 작업 수와 관계없이 두 번 이상 연속 고장이 발생하는 것입니다.
성공 및 실패는 Return_Code 값을 사용하여 정의되고, 조건이 충족되면 **MEASURES**는 첫 번째 경고 작업 및 첫 번째 경고 시간인 *ATM_id*로 프로젝션됩니다.

자세한 내용은 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics)를 참조하세요.

## <a name="geofencing-and-geospatial-queries"></a>지오펜싱 및 지리 공간적 쿼리

Azure Stream Analytics는 차량 관리, 차량 공유, 연결된 자동차 및 자산 추적과 같은 시나리오를 구현하는 데 사용할 수 있는 기본 제공 지리 공간적 함수를 제공합니다.
지리 공간적 데이터는 이벤트 스트림 또는 참조 데이터의 일부로 GeoJSON 또는 WKT 형식으로 수집할 수 있습니다.
예를 들어 여권 인쇄용 머신 제조 전문 회사는 정부 및 영사관에 머신을 임대합니다. 이러한 머신의 위치는 잘못된 배치 및 여권 위조에 사용할 가능성을 방지하기 위해 엄격하게 제어됩니다. 각 머신에는 GPS 추적기가 장착되고 해당 정보는 Azure Stream Analytics 작업에 다시 릴레이됩니다.
제조업체는 이와 같은 머신의 위치를 추적하고 머신 중 하나가 승인된 영역을 벗어나면 경고를 받고자 합니다. 이렇게 함으로써 사용하지 않도록 원격으로 설정하고 당국에 경고를 보내며 장비를 검색할 수 있습니다.

**입력**:

| Equipment_id | Equipment_current_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13288797982818 47.64082002051315)" | 2017-01-26T00:10:00.0000000Z |
| 1 | "POINT(-122.13307252987875 47.64081350934929)" | 2017-01-26T00:11:00.0000000Z |
| 1 | "POINT(-122.13308862313283 47.6406508603241)" | 2017-01-26T00:12:00.0000000Z |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

**참조 데이터 입력**:

| Equipment_id | Equipment_lease_location |
| --- | --- |
| 1 | "POLYGON((-122.13326028450979 47.6409833866794,-122.13261655434621 47.6409833866794,-122.13261655434621 47.64061471602751,-122.13326028450979 47.64061471602751,-122.13326028450979 47.6409833866794))" |

**출력**:

| Equipment_id | Equipment_alert_location | Time |
| --- | --- | --- |
| 1 | "POINT(-122.13341048821462 47.64043760861279)" | 2017-01-26T00:13:00.0000000Z |

```SQL
SELECT
    input.Equipment_id AS Equipment_id,
    input.Equipment_current_location AS Equipment_current_location,
    input.Time AS Time
FROM input TIMESTAMP BY time
JOIN
    referenceInput 
    ON input.Equipment_id = referenceInput.Equipment_id
    WHERE 
        ST_WITHIN(input.Equipment_currenct_location, referenceInput.Equipment_lease_location) = 1
```

이 쿼리를 사용하면 제조업체에서 머신 위치를 자동으로 모니터링하여 머신이 허용된 지오펜스를 벗어날 때 경고를 받을 수 있습니다. 사용자는 기본 제공되는 지리 공간적 함수를 사용하여 타사 라이브러리 없이 쿼리 내에서 GPS 데이터를 사용할 수 있습니다.

자세한 내용은 [Azure Stream Analytics를 사용한 지오펜싱 및 지리 공간적 집계 시나리오](geospatial-scenarios.md) 문서를 참조하세요.

## <a name="get-help"></a>도움말 보기

추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)를 참조해 보세요.

## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
