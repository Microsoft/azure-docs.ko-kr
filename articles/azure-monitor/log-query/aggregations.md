---
title: Azure Monitor 로그 쿼리의 집계 | Microsoft Docs
description: 데이터를 분석하는 유용한 방법을 제공하는 Azure Monitor 로그 쿼리의 집계 함수에 대해 설명합니다.
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
ms.openlocfilehash: 864e9586082ed95bf17135414ec4b879e3034ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589617"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리의 집계

> [!NOTE]
> 이 단원을 완료하기 전에 [Analytics 포털 시작](get-started-portal.md) 및 [쿼리 시작](get-started-queries.md)을 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 문서에서는 데이터를 분석하는 유용한 방법을 제공하는 Azure Monitor 로그 쿼리의 집계 함수에 대해 설명합니다. 이러한 함수는 입력 테이블의 집계된 결과로 테이블을 생성하는 `summarize` 연산자를 모두 사용합니다.

## <a name="counts"></a>Counts

### <a name="count"></a>count
필터가 적용된 후, 결과 집합의 행 수를 계산합니다. 다음 예제에서는 지난 30분 동안 _Perf_ 테이블의 총 행 수를 반환합니다. 이 결과는 특정 이름을 지정하지 않는 한, *count_* 열에 반환됩니다.


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

시간에 따른 추세를 보는 데는 시간 차트 시각화가 유용할 수 있습니다.

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

이 예제의 출력은 5분 간격으로 perf 레코드 수 추세선을 표시합니다.

![개수 추세](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
`dcount` 및 `dcountif`를 사용하여 특정 열의 고유 값을 계산합니다. 다음 쿼리는 지난 시간에 하트비트를 전송한 고유 컴퓨터 수를 평가합니다.

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

하트비트를 전송한 Linux 컴퓨터 수만 계산하려면 `dcountif`를 사용합니다.

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>하위 그룹 평가
데이터의 하위 그룹에 대해 개수 또는 다른 집계를 수행하려면 `by` 키워드를 사용합니다. 예를 들어, 각 국가에 하트비트를 전송한 고유한 Linux 컴퓨터의 수를 계산하려면 다음을 입력합니다.

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|미국    | 19                  |
|캐나다           | 3                   |
|아일랜드          | 0                   |
|영국   | 0                   |
|네덜란드      | 2                   |


데이터의 좀 더 작은 하위 그룹을 분석하려면 `by` 섹션에 추가 열 이름을 추가합니다. 예를 들어, OSType별로 각 국가의 고유 컴퓨터 수를 계산하려면 다음을 입력합니다.

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>백분위수 및 분산
숫자 값을 평가할 때 일반적인 방법은 `summarize avg(expression)`을 사용하여 평균을 계산하는 것입니다. 평균은 소수의 사례만 특징화하는 극한 값의 영향을 받습니다. 이러한 문제를 해결하기 위해 `median` 또는 `variance`와 같은 덜 민감한 함수를 사용할 수 있습니다.

### <a name="percentile"></a>백분위수
중간 값을 찾으려면 값에 `percentile` 함수를 사용하여 백분위수를 지정합니다.

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

또한 각각에 대해 다른 백분위수를 지정하여 집계된 결과를 가져올 수도 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

이 경우 일부 컴퓨터 CPU는 비슷한 중간 값을 갖지만, 일부는 중간 값 수준을 유지하고, 일부는 훨씬 더 낮거나 훨씬 더 높은 CPU 값(스파이크)을 갖는 것으로 보고되었습니다.

### <a name="variance"></a>Variance
값의 분산을 직접 평가하려면 다음과 같이 표준 편차 및 분산 메서드를 사용합니다.

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

CPU 사용량의 안정성을 분석하는 적절한 방법은 중앙값 계산에 표준 편차를 결합하는 것입니다.

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Azure Monitor 로그 데이터에 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하는 방법에 대한 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [고급 쿼리 작성](advanced-query-writing.md)
- [조인](joins.md)
- [차트](charts.md)
