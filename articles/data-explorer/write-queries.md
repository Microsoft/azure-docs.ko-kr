---
title: Azure 데이터 탐색기용 쿼리 작성
description: 이 방법 문서에서는 Azure 데이터 탐색기에 대해 기본 쿼리와 고급 쿼리를 수행하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: b1a7e64cf6b85b517bc027d6541d63c9be729734
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773981"
---
# <a name="write-queries-for-azure-data-explorer"></a>Azure 데이터 탐색기용 쿼리 작성

이 문서에서는 Azure 데이터 탐색기에서 쿼리 언어를 사용하여 가장 흔히 사용되는 연산자로 기본적인 쿼리를 수행하는 방법을 알아봅니다. 또한 쿼리 언어의 몇 가지 고급 기능도 살펴봅니다.

## <a name="prerequisites"></a>필수 조건

다음의 두 가지 방식 중 하나로 이 문서의 쿼리를 실행할 수 있습니다.

- 학습 지원을 위해 설정된 Azure 데이터 탐색기 *help 클러스터*에서 쿼리 실행.
    이렇게 하려면 Azure Active Directory 구성원인 조직 전자 메일 계정을 사용하여 [클러스터에 로그인](https://dataexplorer.azure.com/clusters/help/databases/samples)합니다.

- StormEvents 샘플 데이터가 포함된 자체 클러스터에서 쿼리 실행. 자세한 내용은 [빠른 시작: Azure Data Explorer 클러스터 및 데이터베이스 만들기](create-cluster-database-portal.md) 및 [Azure Data Explorer로 샘플 데이터 수집](ingest-sample-data.md)을 참조하세요.

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>쿼리 언어의 개요

Azure 데이터 탐색기의 쿼리 언어는 데이터를 처리하고 결과를 반환하는 읽기 전용 요청입니다. 요청은 구문을 쉽게 읽고 작성하고 자동화할 수 있는 데이터 흐름 모델을 사용하여 일반 텍스트로 작성됩니다. 쿼리는 SQL과 비슷한 계층 구조로 구성된 스키마 엔터티(데이터베이스, 테이블, 열)를 사용합니다.

쿼리는 세미콜론(`;`)으로 구분된 쿼리 문 시퀀스로 구성됩니다. 여기서 문 하나 이상은 테이블 스타일 열/행 메시로 정렬된 데이터를 생성하는 테이블 형식 식 문입니다. 쿼리의 테이블 형식 식 문이 쿼리 결과를 생성합니다.

테이블 형식 식 문의 구문에는 테이블 형식 쿼리 연산자 간의 테이블 형식 데이터 흐름이 있습니다. 이 흐름은 데이터 원본(예: 데이터베이스의 테이블 또는 데이터를 생성하는 연산자)에서 시작되어 파이프(`|`) 구분 기호를 사용해 서로 결합된 데이터 변환 연산자 집합을 통과합니다.

예를 들어 다음 쿼리에는 테이블 식 문 하나가 있습니다. 이 문은 `StormEvents` 테이블에 대한 참조로 시작됩니다. 이 테이블을 호스트하는 데이터베이스는 연결 문자열의 일부인 암시적 항목입니다. 해당 테이블의 데이터(행)는 `StartTime` 열의 값과 `State` 열의 값을 기준으로 하여 차례로 필터링됩니다. 그리고 나면 쿼리가 필터링 후에 남은 행의 개수를 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

이 경우 결과는 다음과 같습니다.

|카운트|
|-----|
|   23|
| |

자세한 내용은 [쿼리 언어 참조](https://aka.ms/kustolangref)를 참조하세요.

## <a name="most-common-operators"></a>가장 흔히 사용되는 연산자

이 섹션에서 설명하는 연산자는 Azure 데이터 탐색기의 쿼리를 이해하려면 파악해야 하는 구성 요소라 할 수 있습니다. 사용자가 작성하는 대부분의 쿼리에는 이러한 연산자가 여러 개 포함될 것입니다.

help 클러스터에서 쿼리를 실행하려면 각 쿼리 위의 **쿼리를 실행하려면 클릭**을 선택합니다.

자체 클러스터에서 쿼리를 실행하려면 다음 단계를 수행합니다.

1. 웹 기본 쿼리 애플리케이션에 각 쿼리를 복사한 다음, 쿼리를 선택하거나 쿼리에 커서를 놓습니다.

1. 애플리케이션 위쪽에서 **실행**을 선택합니다.

### <a name="count"></a>count

[**count**](https://docs.microsoft.com/azure/kusto/query/countoperator): 테이블의 행 개수를 반환합니다.

다음 쿼리는 StormEvents 테이블의 행 개수를 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): 데이터 행을 지정된 수까지 반환합니다.

다음 쿼리는 StormEvents 테이블의 행 5개를 반환합니다. *limit* 키워드는 *take*의 별칭입니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> 원본 데이터를 정렬하는 경우가 아니면 어떤 레코드가 반환되는지는 보장할 수 없습니다.

### <a name="project"></a>project

[**project**](https://docs.microsoft.com/azure/kusto/query/projectoperator): 열 하위 집합을 선택합니다.

다음 쿼리는 특정 열 집합을 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>여기서,

[**where**](https://docs.microsoft.com/azure/kusto/query/whereoperator): 조건자를 만족하는 행의 부분집합으로 테이블을 필터링합니다.

다음 쿼리는 `EventType` 및 `State`를 기준으로 데이터를 필터링합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>sort

[**sort**](https://docs.microsoft.com/azure/kusto/query/sortoperator): 입력 테이블의 행을 하나 이상의 열에 의해 순서대로 정렬합니다.

다음 쿼리는 `DamageProperty`를 기준으로 하여 데이터를 내림차순으로 정렬합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> 여기서는 연산의 순서가 중요합니다. `take 5`를 `sort by` 앞에 배치하여 다른 결과가 표시되는지 확인해 보세요.

### <a name="top"></a>top

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator): 지정한 열을 기준으로 정렬된 처음  *N* 개 레코드를 반환합니다.

다음 쿼리는 연산자 수는 하나 더 적지만 위의 쿼리와 같은 결과를 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>extend

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): 파생 열을 계산합니다.

다음 쿼리는 모든 행의 값을 계산하여 새 열을 만듭니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

식은 일반적인 연산자(+, -, *, /, %)를 모두 포함할 수 있으며 여러 가지 유용한 함수를 호출할 수 있습니다.

### <a name="summarize"></a>summarize

[**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): 행 그룹을 집계합니다.

다음 쿼리는 `State`를 기준으로 이벤트 개수를 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

**summarize** 연산자는 **by** 절의 값이 같은 행을 그룹화한 다음 **count** 등의 집계 함수를 사용하여 각 그룹을 단일 행으로 결합합니다. 따라서 이 예제의 경우 각 주에 해당하는 행이 있고 그 주의 행 개수에 해당하는 열이 있습니다.

다양한 집계 함수가 있으며 이 중 여러 개를 하나의 **summarize** 연산자에 사용하여 여러 계산 열을 생성할 수 있습니다. 예를 들어 각 주의 태풍 횟수와 주당 고유한 태풍 수를 가져온 다음 **top**을 사용하여 태풍의 영향을 가장 많이 받은 주를 가져올 수 있습니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

**summarize** 연산의 결과에는 다음 항목이 포함됩니다.

- **by**에 이름이 포함되어 있는 각 열

- 각 계산 식에 해당하는 열

- by 값의 각 조합에 해당하는 행

### <a name="render"></a>render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): 결과를 그래픽 출력으로 렌더링합니다.

다음 쿼리는 세로 막대형 차트를 표시합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

다음 쿼리는 단순한 시간 차트를 표시합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

다음 쿼리는 시간 모듈로별로 1일 동안의 이벤트 개수를 계산하여 시간 단위로 구간을 지정한 다음 시간 차트를 표시합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

다음 쿼리는 시간 차트에서 여러 일별 계열을 비교합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> **render** 연산자는 엔진의 일부라기보다는 클라이언트 쪽 기능이라 할 수 있으며 쉽게 사용할 수 있도록 언어에 통합됩니다. 웹 애플리케이션은 barchart, columnchart, piechart, timechart, linechart 옵션을 지원합니다. 

## <a name="scalar-operators"></a>스칼라 연산자

이 섹션에서는 중요한 몇 가지 스칼라 연산자에 대해 설명합니다.

### <a name="bin"></a>bin()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): 값을 지정된 bin 크기의 아래쪽 정수 배로 반올림합니다.

다음 쿼리는 버킷 크기로 1일을 사용하여 개수를 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): 조건자 목록을 평가한 다음 조건자가 충족된 첫 번째 결과 식이나 마지막 **else** 식을 반환합니다. 이 연산자를 사용하여 데이터를 분류하거나 그룹화할 수 있습니다.

다음 쿼리는 새 열 `deaths_bucket`을 반환하고 수를 기준으로 사망자를 그룹화합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): 텍스트 문자열에서 정규식에 대한 일치 항목을 가져옵니다.

다음 쿼리는 추적에서 특정 특성 값을 추출합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

이 쿼리는 식에 이름(여기서는 `MyData`)을 바인딩하는 **let** 문을 사용합니다. **let** 문이 표시되는 범위의 나머지 부분(전역 범위 또는 함수 본문 범위)에서는 이름을 사용하여 해당 바인딩 값을 참조할 수 있습니다.

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): 문자열을 JSON 값으로 해석하고 값을 동적으로 반환합니다. 복합 JSON 개체의 요소를 여러 개 추출해야 하는 경우에는 **extractjson()** 함수보다 이 연산자를 사용하는 것이 더 효율적입니다.

다음 쿼리는 배열에서 JSON 요소를 추출합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

다음 쿼리는 JSON 요소를 추출합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

다음 쿼리는 동적 데이터 형식의 JSON 요소를 추출합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): 현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다.

다음 쿼리는 지난 12시간 동안의 데이터를 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): 오프셋만큼 이동된 날짜를 포함하는 주의 시작일을 반환합니다.

다음 쿼리는 각기 다른 오프셋이 적용된 주의 시작일을 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

이 쿼리는 값의 단일 열 테이블을 생성하는 **range** 연산자를 사용합니다. [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction) 및 [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction)도 참조하세요.

### <a name="between"></a>between()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): 포함 범위 내의 입력과 일치합니다.

다음 쿼리는 지정된 날짜 범위를 기준으로 데이터를 필터링합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

다음 쿼리는 지정된 날짜 범위를 기준으로 데이터를 필터링합니다. 단, 범위가 시작 날짜에서 3일(`3d`) 후부터 시작되도록 약간 변형되어 있습니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>테이블 형식 연산자

Kusto에는 여러 가지 테이블 형식 연산자가 있습니다. 이 문서의 다른 섹션에도 그 중 몇 가지가 나와 있습니다. 여기서는 **parse**를 중점적으로 설명하겠습니다. 

### <a name="parse"></a>parse

[**parse**](https://docs.microsoft.com/azure/kusto/query/parseoperator): 문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 구문 분석 방식에는 simple(기본값), regex, relaxed의 세 가지가 있습니다.

다음 쿼리는 기본값인 단순 구문 분석을 사용하여 추적을 구문 분석하고 관련 값을 추출합니다. StringConstant라고도 하는 식은 정규 문자열 값이며 엄격한 일치가 적용됩니다. 즉, 확장 열이 필요한 형식과 일치해야 합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

다음 쿼리는 `kind = regex`를 사용하여 추적을 구문 분석하고 관련 값을 추출합니다. StringConstant는 정규식일 수 있습니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

다음 쿼리는 `kind = relaxed`를 사용하여 추적을 구문 분석하고 관련 값을 추출합니다. StringConstant는 정규 문자열 값이며 낮은 수준의 일치가 적용됩니다. 즉, 확장 열이 필요한 형식과 일부분만 일치해도 됩니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>시계열 분석

### <a name="make-series"></a>make-series

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): [summarize](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)와 마찬가지로 행 그룹을 함께 집계하되 by 값의 각 조합별로 시계열 벡터를 생성합니다.

다음 쿼리는 일별 태풍 이벤트 개수에 대한 시계열 집합을 반환합니다. 이 쿼리는 각 주에 대해 3개월의 기간 동안 실행되며 누락된 구간에는 상수 0을 채웁니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

시계열 집합을 만든 후에는 계열 함수를 적용하여 비정상적인 형태, 계절별 패턴 등의 다양한 정보를 검색할 수 있습니다.

다음 쿼리는 특정일에 이벤트가 가장 많이 발생한 상위 3개 주를 추출합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

자세한 내용을 확인하려면 [계열 함수](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)의 전체 목록을 검토하세요.

## <a name="advanced-aggregations"></a>고급 집계

이 문서의 앞부분에서는 **count**, **summarize** 등의 기본적인 집계를 살펴보았습니다. 이 섹션에서는 고급 옵션을 소개합니다.

### <a name="top-nested"></a>top-nested

[**top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): 각 수준이 이전 수준 값을 기준으로 하는 드릴다운인 계층적 상위 결과를 생성합니다.

이 연산자는 대시보드 시각화 시나리오 또는 "K1의 상위 N개 값 찾기(일부 집계 사용); 각각에 대해 K2의 상위 M개 값 찾기(다른 집계 사용); ..."과 같은 질문에 답변해야 할 때 유용합니다.

다음 쿼리는 최상위 수준에 `State`가 있고 그 다음에는 `Sources`가 있는 는 계층적 테이블을 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() plugin

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): 입력 테이블의 한 열에 있는 고유 값을 출력 테이블의 여러 값으로 바꾸는 방식으로 테이블을 회전합니다. 이 연산자는 최종 출력의 나머지 열 값에 대해 필요한 위치에서 집계를 수행합니다.

다음 쿼리는 필터를 적용하고 행을 열로 피벗합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): 그룹에 있는 식의 고유 값 수 추정치를 반환합니다. 모든 값의 개수를 계산하려면 [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator)를 사용합니다.

다음 쿼리는 `State`를 기준으로 고유 `Source` 개수를 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): 조건자가 true로 평가되는 행에 대해 식의 고유 값 수 추정치를 반환합니다.

다음 쿼리는 `DamageProperty < 5000`인 `Source`의 고유 값 개수를 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction):  [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) 또는 [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction)에 의해 생성된 HyperLogLog 결과에서 **dcount**를 계산합니다.

다음 쿼리는 HLL 알고리즘을 사용하여 개수를 생성합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): 그룹에서 식의 값이 가장 커지는 행을 찾은 다음 다른 식의 값을 반환합니다(전체 행을 반환하려면 * 사용).

다음 쿼리는 각 주의 마지막 홍수 보고 시간을 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): 식이 그룹에서 가져오는 고유 값 집합의 동적(JSON) 배열을 반환합니다.

다음 쿼리는 각 주에서 홍수가 보고된 모든 시간을 반환하고 고유 값 집합에서 배열을 만듭니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-확장

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): 컬렉션의 각 값이 개별 행에 포함되도록 동적 형식 열에서 다중값 컬렉션을 확장합니다. 확장된 행의 다른 열은 모두 중복됩니다. 즉, 이 연산자는 makelist의 반대 개념이라 할 수 있습니다.

다음 쿼리 집합을 만들고 다음을 보여 주기 위해 사용 하 여 샘플 데이터를 생성 합니다 **mv-확장** 기능입니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): 식으로 정의된 모집단의 지정된 [**근사 순위 백분위수**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) 추정치를 반환합니다. 정확도는 백분위수 지역의 인구 밀도에 따라 달라집니다.  [**summarize**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

다음 쿼리는 태풍 기간에 대한 백분위수를 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

다음 쿼리는 주별 태풍 기간에 대한 백분위수를 계산하고 5분 구간(`5m`)으로 데이터를 정규화합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>데이터 세트 간 요소

이 섹션에서는 더 복잡한 쿼리를 작성하고, 테이블 간에 데이터를 조인하고, 여러 데이터베이스와 클러스터를 쿼리하는 데 사용할 수 있는 요소에 대해 설명합니다.

### <a name="let"></a>let

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): 모듈성과 재사용 과정을 개선합니다. **let** 문을 사용하면 복잡해질 수 있는 문을 각각 이름에 바인딩되는 여러 부분으로 분리하고 해당 부분을 함께 작성할 수 있습니다. 또한 **let** 문은 사용자 정의 함수와 보기(결과가 새 테이블처럼 표시되는 테이블에 대한 식)를 만드는 데 사용할 수도 있습니다. **let** 문으로 바인딩된 식은 스칼라 형식, 테이블 형식 또는 사용자 정의 함수(람다)일 수 있습니다.

다음 예제에서는 테이블 형식 변수를 만들어 후속 식에 사용합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>join

[**join**](https://docs.microsoft.com/azure/kusto/query/joinoperator): 두 테이블의 행을 병합하여 각 테이블에서 지정된 열의 값을 일치시키는 방식으로 새 테이블을 만듭니다. Justo에서는 전체 조인 형식 범위(**fullouter**, **inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**)를 지원합니다.

다음 예제에서는 내부 조인을 사용하여 두 테이블을 조인합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> **where** 및 **project** 연산자를 사용하면 조인 전에 입력 테이블의 행과 열 수를 줄일 수 있습니다. 한 테이블이 언제나 다른 테이블보다 더 작으면 해당 테이블을 조인의 좌변(파이프된)으로 사용합니다. 조인 일치에 대한 열은 같은 이름을 가져야 합니다. 필요한 경우 **project** 연산자를 사용하여 테이블 중 하나의 열 이름을 바꿉니다.

### <a name="serialize"></a>serialize

[**serialize**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): **row_number()** 와 같이 직렬화된 데이터가 필요한 함수를 사용할 수 있도록 행 집합을 직렬화합니다.

데이터가 직렬화되어 있으므로 다음 쿼리는 정상적으로 실행됩니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

직렬화의 결과 이면 행 집합으로 간주 됩니다. **정렬**, **위쪽**, 또는 **범위** 연산자 뒤에 선택적으로 **프로젝트**, **프로젝트 자리 비움**, **확장할**, **여기서**, **구문 분석**, **mv-확장**, 또는 **걸릴** 연산자입니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>데이터베이스 간/클러스터 간 쿼리

[데이터베이스 간/클러스터 간 쿼리](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): 동일한 클러스터의 데이터베이스는 `database("MyDatabase").MyTable`로 참조하면 쿼리할 수 있습니다. 원격 클러스터의 데이터베이스는 `cluster("MyCluster").database("MyDatabase").MyTable`로 참조하면 쿼리할 수 있습니다.

다음 쿼리는 특정 클러스터에서 호출되어 `MyCluster` 클러스터의 데이터를 쿼리합니다. 이 쿼리를 실행하려면 사용자의 클러스터 이름과 데이터베이스 이름을 사용하세요.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>사용자 분석

이 섹션에서는 Kusto에서 사용자 동작 분석을 매우 쉽게 수행할 수 있음을 보여 주는 요소와 쿼리를 소개합니다.

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics 플러그 인

[**activity_counts_metrics 플러그 인**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): 유용한 활동 메트릭(총 개수 값, 고유 개수 값, 새 값의 고유 개수, 집계된 고유 개수)을 계산합니다. 메트릭은 각 기간에 대해 계산된 다음 비교 과정을 거쳐 모든 이전 기간과 함께 집계됩니다.

다음 쿼리는 일별 활동 개수를 계산하여 사용자 채택을 분석합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activityengagement-plugin"></a>activity_engagement 플러그 인

[**activity_engagement 플러그 인**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): 슬라이딩 타임라인 윈도우에서 ID 열을 기준으로 하여 활동 참여 비율을 계산합니다. **activity_engagement 플러그 인**을 사용하면 DAU, WAU 및 MAU(매일/매주/매월 활성 사용자)를 계산할 수 있습니다.

다음 쿼리는 이동하는 7일 윈도우에서 매주 애플리케이션을 사용하는 총 고유 사용자 수와 비교한 매일 애플리케이션을 사용하는 총 고유 사용자 비율을 반환합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> DAU/MAU를 계산할 때는 종료 데이터 및 이동 윈도우 기간(OuterActivityWindow)를 변경하세요.

### <a name="activitymetrics-plugin"></a>activity_metrics 플러그 인

[**activity_metrics 플러그 인**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): 이전 기간과 비교한 현재 기간을 기준으로 하여 유용한 활동 메트릭(고유 개수 값, 새 값의 고유 개수, 보유율, 변동률)을 계산합니다.

다음 쿼리는 지정된 데이터 세트의 변동률과 보유율을 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>new_activity_metrics 플러그 인

[**new_activity_metrics 플러그 인**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): 새 사용자 코호트에 대해 유용한 활동 메트릭(고유 개수 값, 새 값의 고유 개수, 보유율, 변동률)을 계산합니다. 이 플러그 인은 개념상으로는 [**activity_metrics 플러그 인**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)과 비슷하지만 새 사용자만을 대상으로 합니다.

다음 쿼리는 새 사용자 코호트(첫 주에 도착한 사용자)에 대해 1주 동안의 보유율과 변동률을 계산합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>session_count 플러그 인

[**session_count 플러그 인**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): 타임라인에 대해 ID 열을 기준으로 하여 세션 개수를 계산합니다.

다음 쿼리는 세션 개수를 반환합니다. 시간 슬롯 100회에 해당하는 시간 프레임 동안 사용자 ID가 1회 이상 표시되면 세션은 활성으로 간주됩니다. 세션 루프백 기간은 시간 슬롯 41회입니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnelsequence-plugin"></a>funnel_sequence 플러그 인

[**funnel_sequence 플러그 인**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): 특정 상태 시퀀스를 거친 사용자의 고유 개수를 계산하고, 해당 시퀀스로 후나 전에 발생한 이전 및 다음 상태의 분산을 표시합니다.

다음 쿼리는 2007년의 모든 Tornado 이벤트 전과 후에 발생한 이벤트를 표시합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>funnel_sequence_completion 플러그 인

[**funnel_sequence_completion 플러그 인**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): 서로 다른 기간 내에서 완료된 시퀀스 단계의 경로를 계산합니다.

다음 쿼리는 1시간/4시간/1일의 “전체” 시간(`[1h, 4h, 1d]`) 동안  `Hail -> Tornado -> Thunderstorm -> Wind` 시퀀스의 완료 경로를 확인합니다.

**\[**[**쿼리를 실행하려면 클릭**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

이 섹션에서는 서버에 저장되는 재사용 가능 쿼리인 [**함수**](https://docs.microsoft.com/azure/kusto/query/functions)에 대해 설명합니다. 쿼리와 다른 함수를 통해 함수를 호출할 수 있습니다. 재귀 함수는 지원되지 않습니다.

> [!NOTE]
> 읽기 전용인 help 클러스터에서는 함수를 만들 수 없습니다. 함수를 만들려면 자체 테스트 클러스터를 사용하세요.

다음 예제에서는 주 이름(`MyState`)을 인수로 가져오는 함수를 만듭니다.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

다음 예제에서는 Texas 주의 데이터를 가져오는 함수를 호출합니다.

```Kusto
MyFunction ("Texas")
| summarize count()
```

다음 예제에서는 첫 단계에서 만든 함수를 삭제합니다.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>다음 단계

[Kusto 쿼리 언어 참조](https://aka.ms/kustolangref)
