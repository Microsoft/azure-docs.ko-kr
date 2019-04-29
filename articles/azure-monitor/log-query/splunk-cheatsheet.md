---
title: Splunk-Azure Monitor 로그 쿼리 | Microsoft Docs
description: Splunk에 익숙한 사용자를 위한 Azure Monitor 로그 쿼리 학습 도움말입니다.
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
ms.date: 08/21/2018
ms.author: bwren
ms.openlocfilehash: fb637197139001c67a4cfa773f897e6701dc1e9c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425137"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk-Azure Monitor 로그 쿼리

이 문서는 Splunk에 익숙한 사용자가 Azure Monitor에서 로그 쿼리를 작성하기 위한 Kusto 쿼리 언어를 알아보는 데 도움이 됩니다. 주요 차이점을 이해하기 위해 이 둘을 직접 비교하고, 기존 지식을 활용할 수 있는 유사점도 파악합니다.

## <a name="structure-and-concepts"></a>구조 및 개념

다음 표에서는 Splunk와 Azure Monitor 간에 개념과 데이터 구조를 비교합니다.

 | 개념  | Splunk | Azure Monitor |  주석
 | --- | --- | --- | ---
 | 배포 단위  | cluster |  cluster |  Azure Monitor를 사용하면 임의의 클러스터 간 쿼리를 수행할 수 있습니다. Splunk는 그렇지 않습니다. |
 | 데이터 캐시 |  버킷  |  캐싱 및 보존 정책 |  데이터에 대한 기간 및 캐싱 수준을 제어합니다. 이 설정은 쿼리 성능과 배포 비용에 직접적인 영향을 줍니다. |
 | 논리적 데이터 분할  |  index  |  데이터베이스  |  데이터를 논리적으로 분리할 수 있습니다. 두 구현 모두는 이러한 파티션에서 합집합(union)과 조인(join)을 허용합니다. |
 | 구조적 이벤트 메타데이터 | N/A | 테이블 |  Splunk에는 이벤트 메타데이터의 검색 언어에 대해 공개된 개념이 없습니다. Azure Monitor 로그에는 열이 있는 테이블에 대한 개념이 있습니다. 각 이벤트 인스턴스가 한 행에 매핑됩니다. |
 | 데이터 레코드 | event | 행 |  용어 변경에만 해당 |
 | 데이터 레코드 특성 | 필드 |  열 |  Azure Monitor에서는 테이블 구조의 일부로 미리 정의됩니다. Splunk에서는 각 이벤트마다 자체의 필드 집합이 있습니다. |
 | 형식 | 데이터 형식 |  데이터 형식 |  Azure Monitor 데이터 형식은 열에 설정되므로 더 명시적입니다. 둘 다 JSON 지원을 포함하여 데이터 형식 및 거의 동등한 데이터 형식 집합을 동적으로 사용할 수 있습니다. |
 | 쿼리 및 검색  | 검색 | 쿼리 |  개념은 기본적으로 Azure Monitor와 Splunk 간에 동일합니다. |
 | 이벤트 수집 시간 | 시스템 시간 | ingestion_time() |  Splunk에서 각 이벤트는 이벤트가 인덱싱된 시간의 시스템 타임스탬프를 가져옵니다. Azure Monitor에서는 ingestion_time() 함수를 통해 참조할 수 있는 시스템 열을 공개하는 ingestion_time이라는 정책을 정의할 수 있습니다. |

## <a name="functions"></a>Functions

다음 표에서는 Splunk 함수와 동일한 Azure Monitor 함수를 지정합니다.

|Splunk | Azure Monitor |주석
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> 또한 `replace()`는 두 제품에서 세 개의 매개 변수를 사용하지만, 해당 매개 변수가 서로 다릅니다. |
| substr | substring() | (1)<br>또한 Splunk는 1부터 시작하는 인덱스를 사용합니다. Azure Monitor는 0부터 시작하는 인덱스를 사용합니다. |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |  (2)  |
| regex | matches regex | Splunk에서 `regex`는 연산자입니다. Azure Monitor에서는 관계 연산자입니다. |
| searchmatch | == | Splunk에서 `searchmatch`는 일치 문자열 검색을 허용합니다.
| random | rand()<br>rand(n) | Splunk 함수는 0에서 2<sup>31</sup>-1까지의 숫자를 반환합니다. Azure Monitor는 0.0에서 1.0 사이의 숫자를 반환하거나, 매개 변수가 제공되는 경우 0에서 n-1 사이의 숫자를 반환합니다.
| now | now() | (1)
| relative_time | totimespan() | (1)<br>Azure Monitor에서 Splunk의 relative_time(datetimeVal, offsetVal)은 datetimeVal + totimespan(offsetVal)입니다.<br>예를 들어 <code>search &#124; eval n=relative_time(now(), "-1d@d")</code>는 <code>...  &#124; extend myTime = now() - totimespan("1d")</code>가 됩니다.

(1) Splunk에서 함수는 `eval` 연산자를 사용하여 호출됩니다. Azure Monitor에서는 `extend` 또는 `project`의 일부로 사용됩니다.<br>(2) Splunk에서 함수는 `eval` 연산자를 사용하여 호출됩니다. Azure Monitor에서는 `where` 연산자와 함께 사용할 수 있습니다.


## <a name="operators"></a>연산자

다음 섹션에서는 Splunk와 Azure Monitor 간에 서로 다른 연산자를 사용하는 예제를 보여 줍니다.

> [!NOTE]
> 다음 예제에서는 _rule_ Splunk 필드가 Azure Monitor의 테이블에 매핑되고, Splunk의 기본 타임스탬프가 Log Analytics의 _ingestion_time()_ 열에 매핑됩니다.

### <a name="search"></a>검색
Splunk에서는 `search` 키워드를 생략하고 따옴표가 없는 문자열을 지정할 수 있습니다. Azure Monitor에서는 `find`를 사용하여 각 쿼리를 시작해야 하며, 따옴표가 없는 문자열은 열 이름이고, 조회 값은 따옴표가 붙은 문자열이어야 합니다. 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Azure Monitor | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>Filter
Azure Monitor 로그 쿼리는 필터가 있는 테이블 형식 결과 집합에서 시작합니다. Splunk에서 필터링은 현재 인덱스에 대한 기본 작업입니다. 또한 Splunk에서 `where` 연산자도 사용할 수 있지만 권장되지 않습니다.

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Azure Monitor | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>검사를 위한 n개 이벤트/행 가져오기 
Azure Monitor 로그 쿼리는 `take`도 `limit`의 별칭으로 지원합니다. Splunk에서 결과가 정렬되면 `head`에서 처음 n개의 결과를 반환합니다. Azure Monitor에서 limit는 정렬되지 않지만 검색한 처음 n개의 행을 반환합니다.

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Azure Monitor | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>필드/열 기준으로 정렬된 처음 n개 이벤트/행 가져오기
아래 결과를 위해 Splunk에서 `tail`을 사용합니다. Azure Monitor에서는 `asc`를 사용하여 정렬 방향을 지정할 수 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Azure Monitor | **top** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>새 필드/열을 사용하여 결과 집합 확장
Splunk에는 `eval` 연산자를 사용하여 비교할 수 없는 `eval` 함수가 있습니다. Splunk의 `eval` 연산자와 Azure Monitor의 `extend` 연산자는 둘 다 스칼라 함수와 산술 연산자만 지원합니다.

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>이름 바꾸기 
Azure Monitor는 동일한 연산자를 사용하여 이름을 바꾸고 새 필드를 만듭니다. Splunk에는 별도의 두 연산자인 `eval`과 `rename`이 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Azure Monitor | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>형식 결과/프로젝션
Splunk에는 `project-away`와 비슷한 연산자가 없는 것 같습니다. UI를 사용하여 필드를 필터링할 수 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **테이블** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Azure Monitor | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>집계
다양한 집계 함수는 [Azure Monitor 로그 쿼리의 집계](aggregations.md)를 참조하세요.

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Azure Monitor | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Splunk의 조인에는 중요한 제한이 있습니다. 하위 쿼리에는 배포 구성 파일에 설정된 10,000개의 결과 제한이 있으며 제한된 수의 조인 특성이 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Azure Monitor | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>정렬
Splunk에서 오름차순으로 정렬하려면 `reverse` 연산자를 사용해야 합니다. 또한 Azure Monitor에서는 null을 배치할 위치를 처음이나 끝으로 정의할 수 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Azure Monitor | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>다중값 확장
Splunk와 Azure Monitor 둘 다에서 비슷한 연산자입니다.

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Azure Monitor | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>결과 패싯, 관심 있는 필드
Azure Portal의 Log Analytics에서는 첫 번째 열만 공개됩니다. 모든 열은 API를 통해 사용할 수 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Azure Monitor | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>중복 제거
`summarize arg_min()`을 대신 사용하여 레코드가 선택되는 순서를 바꿀 수 있습니다.

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Azure Monitor | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>다음 단계

- [Azure Monitor에서 로그 쿼리 작성](get-started-queries.md) 단원을 계속 진행합니다.
