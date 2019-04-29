---
title: Azure Monitor 로그 쿼리에서 차트 및 다이어그램 만들기 | Microsoft Docs
description: 로그 데이터를 여러 다른 방법으로 표시하기 위한 Azure Monitor의 다양한 시각화에 대해 설명합니다.
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
ms.openlocfilehash: 07d0866bd697587da170a00e8077a57035989d32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594082"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Azure Monitor 로그 쿼리에서 차트 및 다이어그램 만들기

> [!NOTE]
> 이 단원을 완료하기 전에 [Azure Monitor 로그 쿼리의 고급 집계](advanced-aggregations.md)를 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

이 문서에서는 로그 데이터를 여러 다른 방법으로 표시하기 위한 Azure Monitor의 다양한 시각화에 대해 설명합니다.

## <a name="charting-the-results"></a>결과 차트로 작성
먼저 지난 시간 동안의 운영 체제별 컴퓨터 수를 검토합니다.

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

기본적으로 결과는 테이블로 표시됩니다.

![테이블](media/charts/table-display.png)

더 나은 보기를표시 하려면 **차트**를 선택하고 **원형** 옵션을 선택하여 결과를 시각화합니다.

![원형 차트](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>시간 차트
1시간 bin의 프로세서 시간에 대한 평균, 50번째 및 95번째 백분위수를 표시합니다. 이 쿼리는 여러 계열을 생성하며, 사용자는 시간 차트에 표시할 계열을 선택할 수 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

**꺾은선형** 차트 표시 옵션을 선택합니다.

![꺾은선형 차트](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>참조선

참조선은 메트릭이 특정 임계값을 초과하는 경우를 쉽게 식별하는 데 도움이 됩니다. 차트에 선을 추가하려면 데이터 세트를 상수 열로 확장합니다.

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![참조선](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>여러 차원
`summarize`의 `by` 절에 포함된 여러 식은 결과에 값의 각 조합에 대해 하나씩 여러 행을 만듭니다.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

결과를 차트로 표시하면 `by` 절의 첫 번째 열이 사용됩니다. 다음 예제에서는 _EventID_를 사용하여 누적 세로 막대형 차트를 보여 줍니다. 차원은 `string` 형식이어야 하므로, 이 예제에서 _EventID_는 문자열로 캐스팅됩니다. 

![가로 막대형 차트 EventID](media/charts/charts-and-diagrams-multiDimension1.png)

열 이름 드롭다운을 선택하여 전환할 수 있습니다. 

![가로 막대형 차트 AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>다음 단계
Azure Monitor 로그 데이터에 [Kusto 쿼리 언어](/azure/kusto/query/)를 사용하는 방법에 대한 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [JSON 및 데이터 구조](json-data-structures.md)
- [고급 쿼리 작성](advanced-query-writing.md)
- [조인](joins.md)