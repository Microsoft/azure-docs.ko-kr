---
title: Azure Monitor에서 로그 쿼리 최적화
description: Azure Monitor에서 로그 쿼리를 최적화 하기 위한 모범 사례입니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: efbc0ba4ef39be6a2a8598ad006cb3aea090974c
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177746"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Azure Monitor에서 로그 쿼리 최적화
Azure Monitor 로그는 [ADX (Azure 데이터 탐색기)](/azure/data-explorer/) 를 사용 하 여 로그 데이터를 저장 하 고 쿼리를 실행 하 여 해당 데이터를 분석 합니다. ADX 클러스터를 만들고, 관리 하 고, 유지 관리 하며, 로그 분석 워크 로드에 맞게 최적화 합니다. 쿼리를 실행 하면 최적화 되 고 작업 영역 데이터를 저장 하는 적절 한 ADX 클러스터로 라우팅됩니다. Azure Monitor 로그와 Azure 데이터 탐색기 모두 자동 쿼리 최적화 메커니즘을 많이 사용 합니다. 자동 최적화는 상당한 향상을 제공 하지만 쿼리 성능을 크게 향상 시킬 수 있는 경우도 있습니다. 이 문서에서는 성능 고려 사항 및 해결을 위한 몇 가지 기법을 설명 합니다.

대부분의 기술은 여기에서 설명 하는 몇 가지 고유한 Azure Monitor 로그 고려 사항이 있지만 Azure 데이터 탐색기 및 Azure Monitor 로그에서 직접 실행 되는 쿼리에 공통적입니다. Azure 데이터 탐색기 최적화 팁에 대 한 자세한 내용은 [쿼리 모범 사례](/azure/kusto/query/best-practices)를 참조 하세요.

최적화 된 쿼리는 다음과 같습니다.

- 더 빠르게 실행 하 여 쿼리 실행의 전체 지속 시간을 줄입니다.
- 제한 되거나 거부 될 가능성이 더 적습니다.

대시보드, 경고, Logic Apps, Power BI 등의 되풀이 및 버스 티 사용에 사용 되는 쿼리에 특히 주의 해야 합니다. 이러한 경우에는 비효율적인 쿼리의 영향이 상당히 중요 합니다.

## <a name="query-performance-pane"></a>쿼리 성능 창
Log Analytics에서 쿼리를 실행 한 후 쿼리 결과 위의 아래쪽 화살표를 클릭 하 여 쿼리에 대 한 여러 성능 표시기의 결과를 보여 주는 쿼리 성능 창을 확인 합니다. 이러한 성능 표시기는 다음 섹션에 설명 되어 있습니다.

![쿼리 성능 창](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>쿼리 성능 표시기

다음 쿼리 성능 표시기는 실행 되는 모든 쿼리에 사용할 수 있습니다.

- [총 CPU](#total-cpu): 모든 계산 노드에서 쿼리를 처리 하는 데 사용 되는 전체 계산입니다. 계산, 구문 분석 및 데이터 페치에 사용 되는 시간을 나타냅니다. 

- [처리 된 쿼리에 사용 되는 데이터](#data-used-for-processed-query): 쿼리를 처리 하기 위해 액세스 한 전체 데이터입니다. 대상 테이블 크기, 사용 된 시간 범위, 적용 된 필터 및 참조 되는 열 수의 영향을 받습니다.

- [처리 된 쿼리의 시간 범위](#time-span-of-the-processed-query): 쿼리를 처리 하기 위해 액세스 된 최신 데이터와 가장 오래 된 데이터 사이의 간격입니다. 쿼리에 대해 지정 된 명시적 시간 범위의 영향을 받습니다.

- [처리 된 데이터의 사용 기간](#age-of-processed-data): 현재와 쿼리를 처리 하기 위해 액세스 한 가장 오래 된 데이터 사이의 간격입니다. 데이터 인출의 효율성에 영향을 미칩니다.

- [작업 영역 수](#number-of-workspaces): 암시적 또는 명시적 선택으로 인해 쿼리를 처리 하는 동안 액세스 된 작업 영역 수입니다.

- [지역 수](#number-of-regions): 작업 영역을 암시적으로 선택 하거나 명시적으로 선택 하 여 쿼리를 처리 하는 동안 액세스 된 영역 수입니다. 다중 지역 쿼리는 훨씬 효율적이 고 성능 표시기는 부분 검사를 제공 합니다.

- [병렬 처리](#parallelism): 여러 노드에서이 쿼리를 실행할 수 있었던 시스템 크기를 나타냅니다. 높은 CPU 사용량이 있는 쿼리에만 해당 됩니다. 특정 함수 및 연산자를 사용 하 여 영향을 받습니다.


## <a name="total-cpu"></a>총 CPU
모든 쿼리 처리 노드에서이 쿼리를 처리 하는 데 투자 된 실제 계산 CPU입니다. 대부분의 쿼리는 많은 수의 노드에서 실행 되므로 일반적으로 쿼리를 실행 하는 데 걸린 기간 보다 훨씬 더 큽니다. 

100 초를 초과 하는 CPU를 활용 하는 쿼리는 과도 한 리소스를 사용 하는 쿼리로 간주 됩니다. 1000 초를 초과 하는 CPU를 활용 하는 쿼리는 악성 쿼리로 간주 되어 제한 될 수 있습니다.

쿼리 처리 시간은 다음에 소요 됩니다.
- 데이터 검색 – 이전 데이터를 검색 하면 최근 데이터를 검색 하는 것 보다 시간이 더 많이 걸립니다.
- 데이터 처리 – 데이터의 논리 및 평가. 

쿼리 처리 노드에 소요 된 시간 외에도, 사용자를 인증 하 고,이 데이터에 대 한 액세스를 허용 하는지 확인 하 고, 데이터 저장소를 찾고, 쿼리를 구문 분석 하 고, 쿼리 처리 노드를 할당 하는 데 사용 Azure Monitor 되는 추가 시간이 있습니다. 이 시간은 쿼리 총 CPU 시간에 포함 되지 않습니다.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>높은 CPU 함수를 사용 하기 전에 레코드 조기 필터링

일부 쿼리 명령 및 함수는 CPU 사용량이 크게 증가 합니다. JSON 및 XML을 구문 분석 하거나 복잡 한 정규식을 추출 하는 명령의 경우 특히 그렇습니다. 이러한 구문 분석은 동적 열을 참조할 때 [parse_json ()](/azure/kusto/query/parsejsonfunction) 또는 [parse_xml ()](/azure/kusto/query/parse-xmlfunction) 함수를 통해 명시적으로 또는 암시적으로 수행 될 수 있습니다.

이러한 함수는 처리 하는 행 수에 비례하여 CPU를 사용 합니다. 가장 효율적인 최적화는 CPU를 많이 사용 하는 함수를 실행 하기 전에 가능한 한 많은 레코드를 필터링 할 수 있는 where 조건을 쿼리 초기에 추가 하는 것입니다.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성 하지만 두 번째 쿼리는 구문 분석 전의 [where](/azure/kusto/query/whereoperator) 조건에서 많은 레코드를 제외 하므로 가장 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>평가 된 where 절 사용 방지

데이터 집합에 실제로 있는 열이 아닌 계산 된 열에 [where](/azure/kusto/query/whereoperator) 절이 포함 된 쿼리는 효율성을 잃게 됩니다. 계산 된 열에 대 한 필터링은 많은 양의 데이터 집합이 처리 될 때 일부 시스템 최적화를 방지 합니다.
예를 들어 다음 쿼리는 정확히 동일한 결과를 생성 하지만 두 번째 쿼리는 [where](/azure/kusto/query/whereoperator) 조건이 기본 제공 열을 참조 하는 것 보다 더 효율적입니다.

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count(), make_set(IPRegion) by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count(), make_set(IPRegion) by Computer
```

### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>요약 및 조인에서 효과적인 집계 명령 및 차원 사용

[Max ()](/azure/kusto/query/max-aggfunction), [sum (](/azure/kusto/query/sum-aggfunction)), [count ()](/azure/kusto/query/count-aggfunction)및 [avg ()](/azure/kusto/query/avg-aggfunction) 와 같은 일부 집계 명령은 논리로 인 한 CPU 영향은 낮지만, 다른 일부는 더 복잡 하며 효율적으로 실행 될 수 있도록 추론 및 추정치를 포함 합니다. 예를 들어 [dcount ()](/azure/kusto/query/dcount-aggfunction) 는 HyperLogLog 알고리즘을 사용 하 여 각 값을 실제로 계산 하지 않고 대량 데이터 집합의 고유 개수에 대 한 종가를 제공 합니다. 백분위 수 함수는 가장 가까운 순위 백분위 수 알고리즘을 사용 하 여 유사한 근사치를 수행 합니다. 몇 가지 명령에는 영향을 줄이기 위한 선택적 매개 변수가 있습니다. 예를 들어 [makeset ()](/azure/kusto/query/makeset-aggfunction) 함수에는 CPU 및 메모리에 크게 영향을 주는 최대 집합 크기를 정의 하는 선택적 매개 변수가 있습니다.

[조인](/azure/kusto/query/joinoperator?pivots=azuremonitor) 및 [요약](/azure/kusto/query/summarizeoperator) 명령을 사용 하면 큰 데이터 집합을 처리할 때 CPU 사용률이 높아질 수 있습니다. 복잡성은 *cardinality* `by` 요약 또는 조인 특성으로를 사용 하는 열의 가능한 값 (카디널리티 라고 함)의 수와 직접적으로 관련이 있습니다. 조인 및 요약의 설명 및 최적화에 대 한 자세한 내용은 해당 설명서 문서 및 최적화 팁을 참조 하세요.

예를 들어, **Counterpath** 는 항상 **CounterName** 및 **ObjectName**에 매핑되어야 하므로 다음 쿼리는 정확히 동일한 결과를 생성 합니다. 두 번째 방법은 집계 차원이 더 작기 때문에 더 효율적입니다.

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

CPU 소비는 집약적 컴퓨팅이 필요한 조건 또는 확장 된 열의 경우에도 영향을 받을 수 있습니다. [Equal = =](/azure/kusto/query/datatypes-string-operators) 및 [startswith](/azure/kusto/query/datatypes-string-operators) 와 같은 모든 trivial 문자열 비교는 고급 텍스트 일치에 더 많은 영향을 주는 반면 CPU 영향은 거의 동일 합니다. 특히 contains 연산자 [는](/azure/kusto/query/datatypes-string-operators) [contains](/azure/kusto/query/datatypes-string-operators) 연산자 보다 더 효율적입니다. 문자열 처리 기법 때문에 짧은 문자열 보다 4 자 보다 긴 문자열을 찾는 것이 더 효율적입니다.

예를 들어 다음 쿼리는 컴퓨터 명명 정책에 따라 유사한 결과를 생성 하지만 두 번째 쿼리는 더 효율적입니다.

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> 이 지표는 즉각적인 클러스터의 CPU만 제공 합니다. 다중 지역 쿼리에서는 지역 중 하나만 표시 합니다. 다중 작업 영역 쿼리에서는 일부 작업 영역을 포함 하지 않을 수 있습니다.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>문자열 구문 분석이 작동할 때 전체 XML 및 JSON 구문 분석 방지
XML 또는 JSON 개체의 전체 구문 분석에는 높은 CPU 및 메모리 리소스가 사용 될 수 있습니다. 대부분의 경우에는 매개 변수를 한 개 또는 두 개만 사용 하 고 XML 또는 JSON 개체를 간단 하 게 사용 하는 경우 [구문 분석 연산자나](/azure/kusto/query/parseoperator) 기타 [텍스트 구문 분석 기법](./parse-text.md)을 사용 하 여 문자열로 구문 분석 하는 것이 더 쉽습니다. XML 또는 JSON 개체의 레코드 수가 증가할수록 성능 향상이 더 중요 합니다. 레코드 수가 수십 억 개에 도달 하는 경우 반드시 필요 합니다.

예를 들어 다음 쿼리는 전체 XML 구문 분석을 수행 하지 않고 위의 쿼리와 정확히 동일한 결과를 반환 합니다. 이 파일은 FileHash 뒤에 오는 파일 경로와 같은 XML 파일 구조에 대해 몇 가지 가정을 하 고 특성을 포함 하지 않습니다. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>처리 된 쿼리에 사용 되는 데이터

쿼리를 처리 하는 데 중요 한 요소는 검색 되어 쿼리 처리에 사용 되는 데이터의 양입니다. Azure 데이터 탐색기는 다른 데이터 플랫폼과 비교 하 여 데이터 볼륨을 크게 줄이는 적극적인 최적화를 사용 합니다. 쿼리에는 사용 되는 데이터 볼륨에 영향을 줄 수 있는 중요 한 요인이 있습니다.

2 개 이상의 데이터를 처리 하는 쿼리는 과도 한 리소스를 사용 하는 쿼리로 간주 됩니다. 20 개가 넘는 데이터를 처리 하는 쿼리는 악성 쿼리로 간주 되어 제한 될 수 있습니다.

Azure Monitor 로그에서 **Timegenerated** 열은 데이터를 인덱싱하는 방법으로 사용 됩니다. **Timegenerated** 값을 가능한 한 범위를 좁히기 때문에 처리 해야 하는 데이터의 양을 크게 제한 하 여 쿼리 성능이 크게 향상 됩니다.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>불필요 한 search 및 union 연산자 사용 방지

처리 중인 데이터를 늘리는 또 다른 요인은 많은 테이블을 사용 하는 것입니다. 이는 일반적으로 `search *` 및 `union *` 명령을 사용 하는 경우에 발생 합니다. 이러한 명령을 통해 시스템은 작업 영역에 있는 모든 테이블의 데이터를 평가 하 고 검색 합니다. 경우에 따라 작업 영역에 수백 개의 테이블이 있을 수 있습니다. "검색 *" 또는 특정 테이블에 대 한 범위를 지정 하지 않고 검색을 사용 하 여 가능한 한 많이 방지 해 보세요.

예를 들어 다음 쿼리는 정확히 동일한 결과를 생성 하지만 마지막 쿼리는 가장 효율적입니다.

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>쿼리에 초기 필터 추가

데이터 볼륨을 줄이는 또 다른 방법은 쿼리의 초기에 [조건을 포함 하는 것입니다](/azure/kusto/query/whereoperator) . Azure 데이터 탐색기 플랫폼은 특정 where 조건과 관련 된 데이터를 포함 하는 파티션을 확인할 수 있도록 하는 캐시를 포함 합니다. 예를 들어 쿼리에가 포함 된 경우 일치 하는 `where EventID == 4624` 이벤트를 포함 하는 파티션을 처리 하는 노드에만 쿼리를 배포 합니다.

다음 예제 쿼리는 정확히 동일한 결과를 생성 하지만 두 번째 쿼리는 더 효율적입니다.

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>조건부 집계 함수 및 구체화 함수를 사용 하 여 동일한 원본 데이터의 여러 검색 방지
쿼리에 join 또는 union 연산자를 사용 하 여 병합 된 하위 쿼리가 여러 개 있는 경우 각 하위 쿼리는 전체 원본을 개별적으로 검색 한 다음 결과를 병합 합니다. 매우 큰 데이터 집합에서 데이터를 검사 하는 데 중요 한 요소 수를 곱한 값입니다.

이를 방지 하는 방법은 조건부 집계 함수를 사용 하는 것입니다. 요약 연산자에 사용 되는 대부분의 [집계 함수](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) 에는 여러 조건에서 단일 요약 연산자를 사용할 수 있도록 하는 조건 화 된 버전이 있습니다. 

예를 들어 다음 쿼리는 로그인 이벤트의 수와 각 계정에 대 한 프로세스 실행 이벤트의 수를 보여 줍니다. 동일한 결과를 반환 하지만 첫 번째는 데이터를 두 번 검색 하 고 두 번째는 한 번만 검색 하는 것입니다.

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

하위 쿼리를 불필요 하 게 사용 하는 또 다른 경우는 특정 패턴과 일치 하는 레코드만 처리 되도록 [구문 분석 연산자](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) 를 미리 필터링 합니다. 이는 구문 분석 연산자와 기타 유사한 연산자가 패턴이 일치 하지 않는 경우 빈 결과를 반환 하기 때문에 필요 하지 않습니다. 다음은 두 번째 쿼리가 데이터를 한 번만 검색 하는 동안 정확히 동일한 결과를 반환 하는 두 개의 쿼리입니다. 두 번째 쿼리에서는 각 구문 분석 명령만 해당 이벤트와 관련이 있습니다. 확장 연산자는 이후에 빈 데이터 상황을 참조 하는 방법을 보여 줍니다.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

위에서 하위 쿼리를 사용 하지 않도록 하는 것이 허용 되지 않는 경우 다른 방법은 [구체화 () 함수](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor)를 사용 하 여 각각의 원본 데이터를 사용 하는 쿼리 엔진에 대 한 힌트입니다. 이는 쿼리 내에서 여러 번 사용 되는 함수에서 원본 데이터를 가져오는 경우에 유용 합니다.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>검색 되는 열 수 줄이기

Azure 데이터 탐색기은 칼럼 형식 데이터 저장소 이므로 모든 열을 검색 하는 것은 다른 열과는 별개입니다. 검색 되는 열 수는 전체 데이터 볼륨에 직접 영향을 미칩니다. 결과를 [요약](/azure/kusto/query/summarizeoperator) 하거나 특정 열을 [프로젝션](/azure/kusto/query/projectoperator) 하는 데 필요한 출력에만 열을 포함 해야 합니다. Azure 데이터 탐색기에는 검색 된 열 수를 줄이기 위한 여러 최적화 기능이 있습니다. 열이 필요 하지 않은 것으로 확인 된 경우 (예: [요약](/azure/kusto/query/summarizeoperator) 명령에서 참조 하지 않는 경우) 검색 하지 않습니다.

예를 들어 두 번째 쿼리는 한 열이 아니라 세 개의 열을 인출 해야 하므로 세 배 더 많은 데이터를 처리할 수 있습니다.

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>처리 된 쿼리의 시간 범위

Azure Monitor 로그의 모든 로그는 **Timegenerated** 열에 따라 분할 됩니다. 액세스 되는 파티션 수는 시간 범위와 직접적으로 관련이 있습니다. 시간 범위를 줄이는 것은 프롬프트 쿼리 실행을 보장 하는 가장 효율적인 방법입니다.

시간 범위가 15 일을 초과 하는 쿼리는 과도 한 리소스를 사용 하는 쿼리로 간주 됩니다. 시간 범위가 90 일을 초과 하는 쿼리는 악성 쿼리로 간주 되어 제한 될 수 있습니다.

시간 범위는 [Azure Monitor Log Analytics의 로그 쿼리 범위 및 시간 범위](scope.md#time-range)에 설명 된 대로 Log Analytics 화면의 시간 범위 선택기를 사용 하 여 설정할 수 있습니다. 선택한 시간 범위는 쿼리 메타 데이터를 사용 하 여 백 엔드로 전달 되기 때문에 권장 되는 방법입니다. 

또 다른 방법은 **Timegenerated** 에서 쿼리에서 생성 된 [where](/azure/kusto/query/whereoperator) 조건을 명시적으로 포함 하는 것입니다. 쿼리가 다른 인터페이스에서 사용 되는 경우에도 시간 범위가 고정 되도록 하려면이 메서드를 사용 해야 합니다.
쿼리의 모든 부분에 **Timegenerated** 필터가 있는지 확인 해야 합니다. 쿼리가 여러 테이블 또는 동일한 테이블에서 데이터를 인출 하는 하위 쿼리를 포함 하는 경우 각각에 고유한 [where](/azure/kusto/query/whereoperator) 조건을 포함 해야 합니다.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>모든 하위 쿼리가 TimeGenerated 필터를 사용 하는지 확인

예를 들어 다음 쿼리에서는 **성능** 테이블이 마지막 일 동안만 검색 되는 반면, **하트 비트** 테이블은 최대 2 년이 될 수 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

이러한 실수가 발생 하는 일반적인 경우는 [arg_max ()](/azure/kusto/query/arg-max-aggfunction) 를 사용 하 여 가장 최근에 발생 한 항목을 찾는 경우입니다. 예:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

이는 내부 쿼리에서 시간 필터를 추가 하 여 쉽게 수정할 수 있습니다.

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

이 오류에 대 한 또 다른 예는 여러 테이블에 대 한 [공용 구조체](/azure/kusto/query/unionoperator?pivots=azuremonitor) 바로 다음 시간 범위 필터링을 수행 하는 경우입니다. Union을 수행할 때 각 하위 쿼리는 범위를 지정 해야 합니다. [Let](/azure/kusto/query/letstatement) 문을 사용 하 여 범위 일관성을 보장할 수 있습니다.

예를 들어 다음 쿼리는 지난 1 일 뿐만 아니라 *하트 비트* 및 *Perf* 테이블의 모든 데이터를 검색 합니다.

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

이 쿼리는 다음과 같이 수정 해야 합니다.

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>시간 범위 측정 제한

측정은 항상 지정 된 실제 시간 보다 큽니다. 예를 들어, 쿼리에 대 한 필터가 7 일 이면 시스템은 7.5 또는 8.1 일을 검사할 수 있습니다. 이는 시스템에서 데이터를 가변 크기의 청크로 분할 하기 때문입니다. 모든 관련 레코드가 검색 되도록 하기 위해 여러 시간 및 하루 이상 처리할 수 있는 전체 파티션을 검색 합니다.

시스템에서 시간 범위에 대 한 정확한 측정을 제공할 수 없는 여러 가지 경우가 있습니다. 이는 쿼리의 범위가 하루 이하인 경우 또는 다중 작업 영역 쿼리에서 발생 합니다.


> [!IMPORTANT]
> 이 지표는 즉각적인 클러스터에서 처리 된 데이터만 표시 합니다. 다중 지역 쿼리에서는 지역 중 하나만 표시 합니다. 다중 작업 영역 쿼리에서는 일부 작업 영역을 포함 하지 않을 수 있습니다.

## <a name="age-of-processed-data"></a>처리 된 데이터의 보존 기간
Azure 데이터 탐색기는 메모리 내, 로컬 SSD 디스크 및 훨씬 느린 Azure Blob의 여러 저장소 계층을 사용 합니다. 최신 데이터는 더 높은 데이터를 더 높은 성능의 계층에 저장 하 여 쿼리 지속 시간 및 CPU를 줄일 수 있습니다. 데이터 자체 외에도 시스템은 메타 데이터에 대 한 캐시를 포함 합니다. 데이터의 오래 된 데이터는 캐시에 있는 메타 데이터의 확률을 줄일 수 있습니다.

14 일 보다 오래 된 데이터를 처리 하는 쿼리는 과도 한 리소스를 소비 하는 쿼리로 간주 됩니다.


일부 쿼리에서는 오래 된 데이터를 사용 해야 하지만 오래 된 데이터가 실수로 사용 되는 경우가 있습니다. 이는 메타 데이터의 시간 범위를 제공 하지 않고 쿼리를 실행 하 고 모든 테이블 참조에 **Timegenerated** 열에 대 한 필터를 포함 하지 않는 경우에 발생 합니다. 이 경우 시스템은 해당 테이블에 저장 된 모든 데이터를 검색 합니다. 데이터 보존이 길면 긴 시간 범위를 포함할 수 있으므로 데이터 보존 기간으로 오래 된 데이터입니다.

예를 들면 다음과 같습니다.

- 제한 되지 않는 하위 쿼리를 사용 하 여 Log Analytics 시간 범위를 설정 하지 않습니다. 위 예제를 참조하세요.
- 시간 범위 옵션 매개 변수 없이 API를 사용 합니다.
- Power BI 커넥터와 같은 시간 범위를 적용 하지 않는 클라이언트를 사용 합니다.

이 경우에도 관련이 있으므로 이전 섹션의 예제 및 메모를 참조 하세요.

## <a name="number-of-regions"></a>영역 수
여러 지역에서 단일 쿼리가 실행 될 수 있는 여러 가지 상황이 있습니다.

- 여러 작업 영역이 명시적으로 나열 되 고 서로 다른 지역에 있습니다.
- 리소스 범위 쿼리가 데이터를 인출 하 고 데이터가 다른 지역에 있는 여러 작업 영역에 저장 되어 있는 경우

영역 간 쿼리를 실행 하려면 시스템에서 일반적으로 쿼리 최종 결과 보다 훨씬 큰 백 엔드 대량의 중간 데이터를 직렬화 하 고 전송 해야 합니다. 또한 최적화, 추론 및 캐시 활용을 수행 하는 시스템의 기능을 제한 합니다.
이러한 지역을 모두 검색할 수 있는 실질적인 이유가 없으면 범위를 조정 하 여 더 작은 영역을 포함 하도록 해야 합니다. 리소스 범위를 최소화 하지만 여전히 많은 지역을 사용 하는 경우 잘못 된 구성으로 인해 발생할 수 있습니다. 예를 들어 감사 로그 및 진단 설정이 다른 지역의 다른 작업 영역으로 전송 되거나 여러 진단 설정 구성이 있습니다. 

3 개 이상의 지역에 걸쳐 있는 쿼리는 과도 한 리소스를 사용 하는 쿼리로 간주 됩니다. 6 개 이상의 지역에 걸친 쿼리는 악성 쿼리로 간주 되어 제한 될 수 있습니다.

> [!IMPORTANT]
> 쿼리가 여러 지역에서 실행 되는 경우 CPU 및 데이터 측정이 정확 하지 않으며 지역 중 하나에만 해당 하는 측정값을 나타냅니다.

## <a name="number-of-workspaces"></a>작업 영역 수
작업 영역은 로그 데이터를 분리 하 고 관리 하는 데 사용 되는 논리적 컨테이너입니다. 백 엔드는 선택한 지역 내에서 실제 클러스터의 작업 영역 배치를 최적화 합니다.

여러 작업 영역을 사용 하면 다음과 같은 결과가 발생할 수 있습니다. 

- 여러 작업 영역이 명시적으로 나열 됩니다.
- 리소스 범위 쿼리가 데이터를 인출 하 고 데이터가 여러 작업 영역에 저장 되는 경우
 
하위 지역 및 클러스터 간 쿼리를 실행 하려면 시스템에서 일반적으로 쿼리 최종 결과 보다 훨씬 큰 백 엔드 대량의 중간 데이터를 직렬화 하 고 전송 해야 합니다. 또한 최적화를 수행 하 고, 추론을 수행 하 고, 캐시를 활용 하는 시스템 기능을 제한 합니다.

5 개 이상의 작업 영역에 걸친 쿼리는 과도 한 리소스를 사용 하는 쿼리로 간주 됩니다. 쿼리가 100 개 이상의 작업 영역에 걸쳐 있을 수 없습니다.

> [!IMPORTANT]
> 일부 다중 작업 영역 시나리오에서 CPU 및 데이터 측정은 정확 하지 않으며 작업 영역 중 일부에만 해당 하는 측정값을 나타냅니다.

## <a name="parallelism"></a>병렬 처리
Azure Monitor 로그는 Azure 데이터 탐색기의 대규모 클러스터를 사용 하 여 쿼리를 실행 하 고, 이러한 클러스터는 규모에 따라 달라 지 며, 잠재적으로 수십 개의 계산 노드를 가져올 수 있습니다. 시스템은 작업 영역 배치 논리 및 용량에 따라 클러스터 크기를 자동으로 조정 합니다.

쿼리를 효율적으로 실행 하기 위해 데이터를 처리 하는 데 필요한 데이터를 기반으로 분할 하 고 계산 노드에 배포 합니다. 시스템에서이 작업을 효율적으로 수행할 수 없는 경우도 있습니다. 이로 인해 쿼리 시간이 길어질 수 있습니다. 

병렬 처리를 줄일 수 있는 쿼리 동작은 다음과 같습니다.

- 직렬화 [연산자](/azure/kusto/query/serializeoperator), [next ()](/azure/kusto/query/nextfunction), [prev ()](/azure/kusto/query/prevfunction)및 [row](/azure/kusto/query/rowcumsumfunction) 함수와 같은 직렬화 및 창 함수를 사용 합니다. 이러한 경우에는 시계열 및 사용자 분석 함수를 사용할 수 있습니다. 쿼리 끝에 [범위](/azure/kusto/query/rangeoperator), [정렬](/azure/kusto/query/sortoperator), [순서](/azure/kusto/query/orderoperator), [위쪽](/azure/kusto/query/topoperator), [hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator)와 같은 연산자를 사용 하는 경우 비효율적인 serialization이 발생할 수도 있습니다.
-    [Dcount ()](/azure/kusto/query/dcount-aggfunction) 집계 함수를 사용 하면 시스템에 고유 값의 중앙 복사본이 적용 됩니다. 데이터의 소수 자릿수가 높으면 dcount 함수 선택적인 매개 변수를 사용 하 여 정확도를 줄이는 것이 좋습니다.
-    대부분의 경우 [조인](/azure/kusto/query/joinoperator?pivots=azuremonitor) 연산자는 전반적인 병렬 처리를 줄입니다. 성능이 문제가 되는 경우 무작위 조인을 검사 합니다.
-    리소스 범위 쿼리에서 매우 많은 수의 Azure 역할 할당이 있는 경우 실행 전 RBAC 검사가 지연 될 수 있습니다. 이로 인해 병렬 처리의 낮은 검사가 길어질 수 있습니다. 예를 들어 쿼리는 수천 개의 리소스가 있고 각 리소스는 구독 또는 리소스 그룹이 아니라 리소스 수준에서 많은 역할 할당을 보유 하는 구독에 대해 실행 됩니다.
-    쿼리가 작은 데이터 청크를 처리 하는 경우에는 시스템에서 여러 계산 노드에 분산 되지 않으므로 병렬 처리 속도가 낮습니다.



## <a name="next-steps"></a>다음 단계

- [Kusto 쿼리 언어에 대 한 참조 설명서](/azure/kusto/query/)입니다.
