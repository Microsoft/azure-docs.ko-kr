---
title: Azure Monitor 로그 쿼리의 고급 집계 | Microsoft Docs
description: Azure Monitor 로그 쿼리를 사용할 수 있는 고급 집계 옵션 중 일부를 설명합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 56e87da0353a41504035a070d4c10bab0dda2279
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551756"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리의 고급 집계

> [!NOTE]
> 이 단원을 완료하기 전에 [Azure Monitor 쿼리의 집계](./aggregations.md)를 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 문서에서는 Azure Monitor 쿼리를 사용할 수 있는 고급 집계 옵션 중 일부를 설명합니다.

## <a name="generating-lists-and-sets"></a>목록 및 집합 생성
`makelist`를 사용하여 특정 열의 값 순서를 기준으로 데이터를 피벗할 수 있습니다. 예를 들어, 컴퓨터에 발생한 가장 일반적인 정렬 이벤트를 탐색할 수 있습니다. 기본적으로 각 컴퓨터에서 Eventid 순서를 기준으로 데이터를 피벗할 수 있습니다. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`는 데이터가 전달된 순서로 목록을 생성합니다. 이벤트를 가장 오래된 것부터 최신 순서로 정렬하려면 order 문에 `desc` 대신 `asc`를 사용합니다. 

고유 값 목록만 만들어도 유용합니다. 이것을 _집합_이라고 하며, `makeset`을 사용하여 생성할 수 있습니다.

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`와 마찬가지로, `makeset`도 정렬된 데이터를 사용하며, 전달되는 행 순서를 기준으로 배열을 생성합니다.

## <a name="expanding-lists"></a>목록 확장
`makelist` 또는 `makeset`의 역 연산은 값 목록을 별도 행으로 확장하는 `mvexpand`입니다. 제한없는 수의 동적 열(JSON 및 배열)로 확장될 수 있습니다. 예를 들어, 지난 시간에 하트비트를 전송한 컴퓨터에서 데이터를 전송하는 솔루션에 대해서는 *하트비트* 테이블을 확인할 수 있습니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | 솔루션 | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

`mvexpand`를 사용하여 각 값을 쉼표로 구분된 목록 대신, 별도 행에 표시합니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | 솔루션 | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


그런 후 `makelist`를 다시 사용하여 항목을 함께 그룹화 합니다. 이번에는 솔루션당 컴퓨터 목록이 표시됩니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|솔루션 | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>누락된 bin 처리
`mvexpand`는 누락된 bin에 대해 기본값을 채워야 할 경우에 사용하면 유용합니다. 예를 들어, 하트비트를 탐색하여 특정 컴퓨터의 가동 시간을 찾는다고 가정합니다. _범주_ 열에 있는 하드비트의 원본을 볼 수도 있습니다. 일반적으로 간단한 summerize 문을 다음과 같이 사용할 수 있습니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 직접 에이전트 | 2017-06-06T17:00:00Z | 15 |
| 직접 에이전트 | 2017-06-06T18:00:00Z | 60 |
| 직접 에이전트 | 2017-06-06T20:00:00Z | 55 |
| 직접 에이전트 | 2017-06-06T21:00:00Z | 57 |
| 직접 에이전트 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

그렇지만 이러한 결과에서 해당 시간 동안 하트비트 데이터가 없으므로 "2017-06-06T19:00:00Z"와 연결된 버킷은 누락됩니다. `make-series` 함수를 사용하여 빈 버킷에 기본값을 할당합니다. 이 경우 각 범주에 대해 값을 위한 열, 일치하는 시간 버킷을 위한 열, 모두 2개의 추가 배열 열을 포함하는 행이 생성됩니다.

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| 직접 에이전트 | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

*count_* 배열의 세 번째 요소는 예상대로 0이며, _TimeGenerated_ 배열에는 일치하는 타임스탬프 "2017-06-06T19:00:00.0000000Z"가 있습니다. 그렇지만 이 배열 형식은 읽기 어렵습니다. `mvexpand`를 사용하여 배열을 확장하고 `summarize`에 의해 생성되는 것과 동일한 형식 출력을 생성합니다.

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| 직접 에이전트 | 2017-06-06T17:00:00Z | 15 |
| 직접 에이전트 | 2017-06-06T18:00:00Z | 60 |
| 직접 에이전트 | 2017-06-06T19:00:00Z | 0 |
| 직접 에이전트 | 2017-06-06T20:00:00Z | 55 |
| 직접 에이전트 | 2017-06-06T21:00:00Z | 57 |
| 직접 에이전트 | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>결과의 범위를 다음 요소의 집합으로 축소: `let`, `makeset`, `toscalar`, `in`
일반적인 시나리오는 조건의 집합에 따라 일부 특정 엔터티의 이름을 선택한 다음, 다른 데이터 집합을 해당 엔터티 집합으로 필터링하는 것입니다. 예를 들어, 업데이트가 누락된 것으로 알려진 컴퓨터를 찾고, 이러한 컴퓨터 호출에 사용되는 IP를 식별할 수 있습니다.


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>다음 단계

Azure Monitor 로그 데이터에 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하는 방법에 대한 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [조인](joins.md)
- [차트](charts.md)