---
title: 고급 Azure Monitor에서 쿼리 | Microsoft Docs
description: 이 문서에서는 Analytics 포털을 사용하여 Azure Monitor에서 쿼리를 작성하는 것에 대한 자습서를 제공합니다.
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
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 65713ed9c2d0635e776a7a7e5f205b6d55438ed4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589582"
---
# <a name="writing-advanced-queries-in-azure-monitor"></a>Azure Monitor에서 고급 쿼리 작성

> [!NOTE]
> 완료 해야 [Azure Monitor Log Analytics를 사용 하 여 시작](get-started-portal.md) 하 고 [쿼리로 시작](get-started-queries.md) 이 단원을 완료 하기 전에 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

## <a name="reusing-code-with-let"></a>let을 사용하여 코드를 다시 사용
`let`을 사용하여 변수에 결과를 할당하고 나중에 쿼리에서 참조할 수 있습니다.

```Kusto
// get all events that have level 2 (indicates warning level)
let warning_events=
Event
| where EventLevel == 2;
// count the number of warning events per computer
warning_events
| summarize count() by Computer 
```

또한 변수에 상수 값을 할당할 수도 있습니다. 이렇게 하면 쿼리를 실행할 때마다 변경해야 하는 필드에 대해 매개 변수를 설정할 수 있습니다. 필요에 따라 이러한 매개 변수를 수정합니다. 예를 들어 지정된 기간에 사용 가능한 디스크 공간 및 사용 가능한 메모리(백분위수)를 계산하려는 경우 다음을 입력합니다.

```Kusto
let startDate = datetime(2018-08-01T12:55:02);
let endDate = datetime(2018-08-02T13:21:35);
let FreeDiskSpace =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Logical Disk" and CounterName=="Free Megabytes"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
let FreeMemory =
Perf
| where TimeGenerated between (startDate .. endDate)
| where ObjectName=="Memory" and CounterName=="Available MBytes Memory"
| summarize percentiles(CounterValue, 50, 75, 90, 99);
union FreeDiskSpace, FreeMemory
```

이렇게 하면 쿼리를 실행하는 다음 번에 시작 시간 및 종료 시간을 쉽게 변경할 수 있습니다.

### <a name="local-functions-and-parameters"></a>로컬 함수 및 매개 변수
`let` 문을 사용하여 같은 쿼리에서 사용할 수 있는 함수를 만듭니다. 예를 들어 날짜/시간 필드(UTC 형식)를 사용하는 함수를 정의한 후 표준 미국 형식으로 변환합니다. 

```Kusto
let utc_to_us_date_format = (t:datetime)
{
  strcat(getmonth(t), "/", dayofmonth(t),"/", getyear(t), " ",
  bin((t-1h)%12h+1h,1s), iff(t%24h<12h, "AM", "PM"))
};
Event 
| where TimeGenerated > ago(1h) 
| extend USTimeGenerated = utc_to_us_date_format(TimeGenerated)
| project TimeGenerated, USTimeGenerated, Source, Computer, EventLevel, EventData 
```

## <a name="print"></a>Print
`print`는 단일 열 및 단일 행이 있는 테이블을 반환하며, 계산 결과를 표시합니다. 이 간단한 계산을 해야 하는 경우에 종종 사용 됩니다. 예를 들어, PST로 현재 시간을 확인한 후 EST를 사용하여 열에 추가하려면 다음을 입력합니다.

```Kusto
print nowPst = now()-8h
| extend nowEst = nowPst+3h
```

## <a name="datatable"></a>Datatable
`datatable`을 사용하면 데이터 집합을 정의할 수 있습니다. 스키마 및 값 집합을 제공한 후, 해당 테이블을 다른 쿼리 요소에 파이프합니다. 예를 들어, RAM 사용량 테이블을 만든 후 시간당 평균 값을 계산하려면 다음을 입력합니다.

```Kusto
datatable (TimeGenerated: datetime, usage_percent: double)
[
  "2018-06-02T15:15:46.3418323Z", 15.5,
  "2018-06-02T15:45:43.1561235Z", 20.2,
  "2018-06-02T16:16:49.2354895Z", 17.3,
  "2018-06-02T16:46:44.9813459Z", 45.7,
  "2018-06-02T17:15:41.7895423Z", 10.9,
  "2018-06-02T17:44:23.9813459Z", 24.7,
  "2018-06-02T18:14:59.7283023Z", 22.3,
  "2018-06-02T18:45:12.1895483Z", 25.4
]
| summarize avg(usage_percent) by bin(TimeGenerated, 1h)
```

Datatable 구문은 조회 테이블을 만들 때도 매우 유용합니다. 예를 들어, _SecurityEvent_ 테이블의 이벤트 ID와 같은 테이블 데이터를 다른 위치에 나열된 이벤트 형식으로 매핑하고, `datatable`을 사용하여 이벤트 형식의 조회 테이블을 만들고, 이 데이터 테이블을  _SecurityEvent_ 데이터에 조인하려면 다음을 입력합니다.

```Kusto
let eventCodes = datatable (EventID: int, EventType:string)
[
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4672, "Access",
    4624, "Account activity",
    4799, "Access management",
    4798, "Access management",
    5059, "Key operation",
    4648, "A logon was attempted using explicit credentials",
    4768, "Access management",
    4662, "Other",
    8002, "Process action",
    4904, "Security event management",
    4905, "Security event management",
];
SecurityEvent
| where TimeGenerated > ago(1h) 
| join kind=leftouter (
  eventCodes
) on EventID
| project TimeGenerated, Account, AccountType, Computer, EventType
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
