---
title: Azure Monitor의 로그 경고 쿼리 | Microsoft Docs
description: Azure Monitor 업데이트에서 로그 경고용으로 효율적인 쿼리를 작성하는 방법 관련 권장 사항과 기존 쿼리를 변환하는 프로세스에 대해 설명합니다.
author: yossi-y
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: bwren
ms.subservice: alerts
ms.openlocfilehash: 429770b7651a93473c03f5e386d8f7b72692c161
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995975"
---
# <a name="log-alert-queries-in-azure-monitor"></a>Azure Monitor의 로그 경고 쿼리
[Azure Monitor 로그를 기반으로 하는 경고 규칙](alerts-unified-log.md)은 일정한 간격으로 실행되므로 오버헤드와 대기 시간을 최소화할 수 있도록 규칙을 작성해야 합니다. 이 문서에서는 로그 경고용으로 효율적인 쿼리를 작성하는 방법과 관련된 권장 사항과 기존 쿼리를 변환하는 프로세스를 제공합니다. 

## <a name="types-of-log-queries"></a>로그 쿼리의 유형
[Azure Monitor에서 로그 쿼리](../log-query/log-query-overview.md) 테이블을 사용 하 여 시작 또는 [검색](/azure/kusto/query/searchoperator) 하거나 [union](/azure/kusto/query/unionoperator) 연산자입니다.

예를 들어 범위가 _SecurityEvent_ 테이블로 지정된 다음 쿼리는 특정 이벤트 ID를 검색합니다. 쿼리는 이 테이블만 처리하면 됩니다.

``` Kusto
SecurityEvent | where EventID == 4624 
```

`search` 또는 `union`으로 시작하는 쿼리를 사용하면 한 테이블의 여러 열이나 여러 테이블을 검색할 수 있습니다. 다음 예제에는 _Memory_라는 단어를 검색하는 여러 가지 방법이 나와 있습니다.

```Kusto
search "Memory"
search * | where == "Memory"
search ObjectName: "Memory"
search ObjectName == "Memory"
union * | where ObjectName == "Memory"
```

`search` 및 `union`은 데이터를 탐색하면서 전체 데이터 모델에서 용어를 검색할 때는 유용하지만 여러 테이블을 검사해야 하므로 테이블 하나를 사용하는 방식보다는 효율성이 낮습니다. 경고 규칙의 쿼리는 일정한 간격으로 실행되므로 오버헤드가 과도하게 발생하여 경고 대기 시간이 길어질 수 있습니다. 이러한 오버헤드를 감안할 때, 명확한 범위를 정의하려면 Azure의 로그 경고 규칙에 대한 쿼리는 항상 테이블 하나로 시작되어야 합니다. 그러면 쿼리 성능과 결과의 관련성이 모두 개선됩니다.

## <a name="unsupported-queries"></a>지원되지 않는 쿼리
2019년 1월 11일부터는 Azure Portal에서 `search` 또는 `union` 연산자를 사용하는 로그 경고 규칙을 만들거나 수정할 수 없습니다. 경고 규칙에서 이러한 연산자를 사용하면 오류 메시지가 반환됩니다. 기존 경고 규칙 및 Log Analytics API를 사용하여 만들고 편집한 경고 규칙은 이 변경의 영향을 받지 않습니다. 하지만 이러한 유형의 쿼리를 사용하는 경고 규칙을 변경하여 효율성을 높이는 것이 좋습니다.  

[리소스 간 쿼리](../log-query/cross-workspace-query.md)를 사용하는 로그 경고 규칙은 이 변경의 영향을 받지 않습니다. 리소스 간 쿼리에서는 쿼리 범위를 특정 리소스로 제한하는 `union`을 사용하기 때문입니다. 이 연산자는 사용이 불가능한 `union *` 연산자와는 다릅니다.  즉, 다음 예제는 올바른 로그 경고 규칙입니다.

```Kusto
union 
app('Contoso-app1').requests, 
app('Contoso-app2').requests, 
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
>로그 경고의 [리소스 간 쿼리](../log-query/cross-workspace-query.md)는 새 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)에서 지원됩니다. [레거시 Log Analytics 경고 API](api-alerts.md)에서 전환하지 않는 한, Azure Monitor는 기본적으로 [레거시 Log Analytics 경고 API](alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api)를 사용하여 Azure Portal에서 새 로그 경고 규칙을 만듭니다. 전환 후에는 새 API가 Azure Portal에서 새 경고 규칙의 기본값이 되며, 해당 API를 사용하여 리소스 간 쿼리 로그 경고 규칙을 만들 수 있습니다. [scheduledQueryRules API에 대한 ARM 템플릿](alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template)을 사용하면 전환하지 않고 [리소스 간 쿼리](../log-query/cross-workspace-query.md) 로그 경고 규칙을 만들 수 있지만, 이 경고 규칙은 Azure Portal이 아닌 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 통해 관리할 수 있습니다.

## <a name="examples"></a>예
`search` 및 `union`을 사용하는 로그 쿼리가 포함된 다음 예제에서는 경고 규칙에 사용할 수 있도록 이러한 쿼리를 수정하기 위해 수행할 수 있는 단계를 제공합니다.

### <a name="example-1"></a>예 1
`search`로 성능 정보를 검색하는 다음 쿼리를 사용하여 로그 경고 규칙을 만들려고 합니다. 

``` Kusto
search * | where Type == 'Perf' and CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```
  

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search * | where CounterName == '% Free Space'
| summarize by $table
```
 

이 쿼리의 결과에는 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다. 

이 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다.

``` Kusto
Perf 
| where CounterName == '% Free Space' 
| where CounterValue < 30 
| summarize count()
```


### <a name="example-2"></a>예 2
`search`로 성능 정보를 검색하는 다음 쿼리를 사용하여 로그 경고 규칙을 만들려고 합니다. 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"  
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
  

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 속성이 속한 테이블을 식별합니다.

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize by $table 
```
 

이 쿼리의 결과에는 _ObjectName_ 및 _CounterName_ 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다. 

이 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다.

``` Kusto
Perf 
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use" 
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer 
| where Avg_Memory_Usage between(90 .. 95)  
| count 
```
 

### <a name="example-3"></a>예 3

`search` 및 `union`으로 성능 정보를 검색하는 다음 쿼리를 사용하여 로그 경고 규칙을 만들려고 합니다. 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| where Computer !in ((union * | where CounterName == "% Processor Utility" | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer|  count  
```
 

이 쿼리를 수정하려면 먼저 다음 쿼리를 사용해 쿼리의 첫 부분에 있는 속성이 속한 테이블을 식별합니다. 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")  
| summarize by $table 
```

이 쿼리의 결과에는 이러한 모든 속성이 _Perf_ 테이블에 포함되어 있었음이 표시됩니다. 

이제 `withsource` 명령에서 `union`을 사용하여 각 행을 제공한 원본 테이블을 식별합니다.

``` Kusto
union withsource=table * | where CounterName == "% Processor Utility" 
| summarize by table 
```
 

이 쿼리의 결과에는 이러한 속성 역시 _Perf_ 테이블에 포함되어 있었음이 표시됩니다. 

이러한 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다. 

``` Kusto
Perf 
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total" 
| where Computer !in ( 
    (Perf 
    | where CounterName == "% Processor Utility" 
    | summarize by Computer)) 
| summarize Avg_Idle_Time = avg(CounterValue) by Computer 
| count 
``` 

### <a name="example-4"></a>예제 4
두 `search` 쿼리의 결과를 조인하는 다음 쿼리를 사용하여 로그 경고 규칙을 만들려고 합니다.

```Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by Computer, Hour = bin(TimeGenerated, 1h) 
| join kind = leftouter ( 
    search in (Heartbeat) OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```
 

쿼리를 수정하려면 먼저 다음 쿼리를 사용하여 조인 왼쪽의 속성이 포함된 테이블을 식별합니다. 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625' 
| summarize by $table 
```
 

결과에는 조인 왼쪽의 속성이 _SecurityEvent_ 테이블에 속한다는 내용이 표시됩니다. 

이제 다음 쿼리를 사용하여 조인 오른쪽의 속성이 포함된 테이블을 식별합니다. 

 
``` Kusto
search in (Heartbeat) OSType == 'Windows' 
| summarize by $table 
```

 
결과에는 조인 오른쪽의 속성이 Heartbeat 테이블에 속한다는 내용이 표시됩니다. 

이러한 결과를 사용하여 경고 규칙에 사용할 다음 쿼리를 작성할 수 있습니다. 


``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat  
    | where OSType == 'Windows' 
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h) 
    | project Hour , Computer  
)  
on Hour 
| count 
```

## <a name="next-steps"></a>다음 단계
- Azure Monitor의 [로그 경고](alerts-log.md)에 대해 알아봅니다.
- [로그 쿼리](../log-query/log-query-overview.md)에 대해 알아봅니다.

