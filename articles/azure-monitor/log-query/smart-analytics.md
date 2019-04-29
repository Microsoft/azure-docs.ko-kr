---
title: Log Analytics 스마트 분석 예제 | Microsoft Docs
description: Log Analytics의 스마트 분석 기능을 사용하여 사용자 활동을 분석하는 예제입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/15/2019
ms.author: bwren
ms.openlocfilehash: f6617a504bbda666ce9ece018ccb0cf02635c360
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425843"
---
# <a name="log-analytics-smart-analytics-examples"></a>Log Analytics 스마트 분석 예제
이 문서에는 Log Analytics의 스마트 분석 기능을 사용하여 사용자 활동을 분석하는 예제가 포함되어 있습니다. 이 예제를 사용하여 Application Insights에서 모니터링되는 사용자 고유의 애플리케이션을 분석하거나, 다른 데이터에 대한 유사한 분석을 위해 이러한 쿼리의 개념을 사용할 수 있습니다. 

이러한 샘플에서 사용되는 다양한 키워드에 대한 자세한 내용은 [Kusto 언어 참조](https://docs.microsoft.com/azure/kusto/query/)를 참조하세요. Log Analytics를 처음 사용하는 경우에는 [쿼리 작성 단원](get-started-queries.md)을 진행하세요.

## <a name="cohorts-analytics"></a>코호트 분석

코호트 분석은 코호트라고 하는 특정 사용자 그룹의 활동을 추적합니다. 재방문 사용자 비율을 측정하여 서비스의 선호도를 측정하려고 합니다. 사용자는 처음에 서비스를 사용한 시간별로 그룹화됩니다. 코호트를 분석할 때 첫 번째 추적된 기간에는 활동이 감소할 것으로 예상합니다. 각 코호트에는 해당 멤버가 처음 관찰된 주를 기준으로 이름이 지정됩니다.

다음 예제에서는 처음 서비스를 사용한 이후 5주 동안 사용자가 수행하는 활동 수를 분석합니다.

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```
이 예제는 다음 출력을 생성합니다.

![코호트 분석 출력](media/smart-analytics/cohorts.png)

## <a name="rolling-monthly-active-users-and-user-stickiness"></a>롤링 월간 활성 사용자 및 사용자 연결 유지
다음 예제에서는 슬라이딩 윈도우 계산을 수행할 수 있는 [series_fir](/azure/kusto/query/series-firfunction) 함수와 함께 시계열 분석을 사용합니다. 모니터링되는 샘플 애플리케이션은 사용자 지정 이벤트를 통해 사용자 활동을 추적하는 온라인 상점입니다. 쿼리는 두 가지 유형의 사용자 활동(_AddToCart_ 및 _Checkout_)을 추적하고 _활성 사용자_를 지정된 날에 체크 아웃을 한 번 이상 수행한 사용자로 정의합니다.



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

이 예제는 다음 출력을 생성합니다.

![롤링 월간 사용자 출력](media/smart-analytics/rolling-mau.png)

다음 예제에서는 위의 쿼리를 재사용 가능한 함수로 전환하고 이 함수를 사용하여 롤링 사용자 연결 유지를 계산합니다. 이 쿼리의 활성 사용자는 지정된 날에 체크 아웃을 한 번 이상 수행한 사용자로만 정의됩니다.

``` Kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mvexpand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

이 예제는 다음 출력을 생성합니다.

![사용자 연결 유지 출력](media/smart-analytics/user-stickiness.png)

## <a name="regression-analysis"></a>회귀 분석
이 예제에서는 애플리케이션의 추적 로그만 기준으로 해서 서비스 중단의 자동화된 탐지기를 만드는 방법을 보여줍니다. 탐지기는 애플리케이션에서 오류 및 경고 추적의 상대적 양이 비정상적으로 갑자기 증가하는 경우를 검색합니다.

추적 로그 데이터를 기준으로 서비스 상태를 평가하기 위해 다음 두 가지 기술이 사용됩니다.

- [make-series](/azure/kusto/query/make-seriesoperator)를 사용하여 반구조적 텍스트 추적 로그를 양수 및 음수 추적 라인 간 비율을 나타내는 메트릭으로 변환합니다.
- [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) 및 [series_fit_line](/azure/kusto/query/series-fit-linefunction)을 사용하여 2 라인 선형 회귀와 함께 시계열 분석을 통해 고급 단계 점프를 검색합니다.

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>다음 단계

- [Data Explorer 언어 참조](/azure/kusto/query)에서 언어에 대한 자세한 내용을 참조합니다.
- [Log Analytics에서 쿼리 작성 단원](get-started-queries.md)을 진행합니다.