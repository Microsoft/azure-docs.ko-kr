---
title: Azure Monitor 로그에서 텍스트 데이터 구문 분석 | Microsoft Docs
description: 데이터가 수집될 때와 쿼리에서 검색될 때 Azure Monitor 레코드의 로그 데이터를 구문 분석하기 위한 다양한 옵션을 설명하고, 각각의 상대적인 이점을 비교합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: ad4839a1b9e951a2bb206518254826a066330000
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61426734"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Azure Monitor 로그에서 텍스트 데이터 구문 분석
Azure Monitor에서 수집된 일부 데이터에는 하나의 속성에 여러 개의 정보 부분이 포함되어 있습니다. 이 데이터를 여러 속성으로 구문 분석하면 쿼리에서 더 쉽게 사용할 수 있습니다. 일반적인 예로, 여러 값이 있는 전체 로그 항목을 단일 속성으로 수집하는 [사용자 지정 로그](../../log-analytics/log-analytics-data-sources-custom-logs.md)가 있습니다. 서로 다른 값에 대해 별도의 속성을 만들면 각각을 검색하고 집계할 수 있습니다.

이 문서에서는 데이터가 수집될 때와 쿼리에서 검색될 때 Azure Monitor 레코드의 로그 데이터를 구문 분석하기 위한 다양한 옵션을 설명하고, 각각의 상대적인 이점을 비교합니다.


## <a name="parsing-methods"></a>구문 분석 방법
데이터를 수집하는 수집 시 또는 쿼리를 통해 데이터를 분석하는 쿼리 시에 데이터를 구문 분석할 수 있습니다. 각 전략에는 아래에서 설명한 대로 고유한 이점이 있습니다.

### <a name="parse-data-at-collection-time"></a>수집 시 데이터 구문 분석
수집 시 데이터를 구문 분석하는 경우 테이블에 새 속성을 만드는 [사용자 지정 필드](../../log-analytics/log-analytics-custom-fields.md)를 구성합니다. 쿼리는 구문 분석 논리를 포함할 필요가 없으며 이러한 속성을 테이블의 다른 필드로 사용하기만 하면 됩니다.

이 방법의 장점은 다음과 같습니다.

- 구문 분석 명령을 쿼리에 포함시킬 필요가 없으므로 수집된 데이터를 쉽게 쿼리할 수 있습니다.
- 쿼리에서 구문 분석을 수행할 필요가 없으므로 쿼리 성능이 향상됩니다.
 
이 방법의 단점은 다음과 같습니다.

- 미리 정의되어야 합니다. 이미 수집된 데이터는 포함할 수 없습니다.
- 구문 분석 논리를 변경하면 새 데이터에만 적용됩니다.
- 구문 분석 옵션이 쿼리에서 사용할 수 있는 것보다 더 적습니다.
- 데이터 수집에 대한 대기 시간이 늘어납니다.
- 오류는 처리하기 어려울 수 있습니다.


### <a name="parse-data-at-query-time"></a>쿼리 시 데이터 구문 분석
쿼리 시 데이터를 구문 분석하는 경우 쿼리에 논리를 포함하여 데이터를 여러 필드로 구문 분석합니다. 실제 테이블 자체는 수정되지 않았습니다.

이 방법의 장점은 다음과 같습니다.

- 이미 수집된 데이터를 포함하여 모든 데이터에 적용됩니다.
- 변경된 논리는 모든 데이터에 즉시 적용될 수 있습니다.
- 특정 데이터 구조에 대해 미리 정의된 논리를 포함하여 구문 분석 옵션이 유연합니다.
 
이 방법의 단점은 다음과 같습니다.

- 더 복잡한 쿼리가 필요합니다. [함수를 사용하여 테이블을 시뮬레이션](#use-function-to-simulate-a-table)하면 이 문제를 완화할 수 있습니다.
- 여러 쿼리에 구문 분석 논리를 복제해야 합니다. 함수를 통해 몇 가지 논리를 공유할 수 있습니다.
- 매우 큰 레코드 세트(수십억 개의 레코드)에 대해 복잡한 논리를 실행할 때 오버헤드를 만들 수 있습니다.

## <a name="parse-data-as-its-collected"></a>수집된 데이터 구문 분석
데이터를 수집할 때 구문 분석하는 방법에 대한 자세한 내용은 [Azure Monitor에서 사용자 지정 필드 만들기](../platform/custom-fields.md)를 참조하세요. 이렇게 하면 다른 속성처럼 쿼리에서 사용할 수 있는 사용자 지정 속성이 테이블에 만들어집니다.

## <a name="parse-data-in-query-using-patterns"></a>패턴을 사용하여 쿼리의 데이터 구문 분석
구문 분석하려는 데이터를 레코드 간에 반복되는 패턴으로 식별할 수 있는 경우 [Kusto 쿼리 언어](/azure/kusto/query/)의 다양한 연산자를 사용하여 데이터의 특정 부분을 하나 이상의 새 속성으로 추출할 수 있습니다.

### <a name="simple-text-patterns"></a>간단한 텍스트 패턴

쿼리에서 [parse](/azure/kusto/query/parseoperator) 연산자를 사용하여 문자열 식에서 추출할 수 있는 하나 이상의 사용자 지정 속성을 만듭니다. 식별할 패턴과 만들 속성의 이름을 지정합니다. 이는 _key=value_와 비슷한 형식의 키-값 문자열이 있는 데이터에 특히 유용합니다.

다음 형식의 데이터가 있는 사용자 지정 로그를 살펴봅니다.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

다음 쿼리는 이 데이터를 개별 속성으로 분석합니다. 사용자 지정 로그의 전체 항목을 포함하는 단일 속성인 _RawData_가 아니라 계산된 속성만 반환하기 위해 _project_가 있는 줄이 추가됩니다.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

다음은 _AzureActivity_ 테이블에서 UPN의 사용자 이름을 분리하는 또 다른 예제입니다.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>정규식
정규식을 사용하여 데이터를 식별할 수 있는 경우 [정규식을 사용하는 함수](/azure/kusto/query/re2)를 통해 개별 값을 추출할 수 있습니다. 다음 예제에서는 [extract](/azure/kusto/query/extractfunction)를 사용하여 _AzureActivity_ 레코드에서 _UPN_ 필드를 분리한 다음, 고유한 사용자를 반환합니다.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

효율적인 대량 구문 분석을 사용하도록 설정하기 위해 Azure Monitor는 다른 정규식 변형 중 일부와 비슷하지만 동일하지 않은 re2 버전의 정규식을 사용합니다. 자세한 내용은 [re2 식 구문](https://aka.ms/kql_re2syntax)을 참조하세요.


## <a name="parse-delimited-data-in-a-query"></a>쿼리에서 구분 기호로 분리된 데이터 구문 분석
구분 기호로 분리된 데이터는 필드를 CSV 파일의 쉼표와 같은 일반적인 문자로 구분합니다. 지정한 구분 기호를 사용하여 구분 기호로 분리된 데이터를 구문 분석하려면 [split](/azure/kusto/query/splitfunction) 함수를 사용합니다. 데이터의 모든 필드를 반환하거나 출력에 포함할 개별 필드를 지정하려면 이 함수를 [extend](/azure/kusto/query/extendoperator) 연산자와 함께 사용합니다.

> [!NOTE]
> split 함수는 동적 개체를 반환하므로 결과는 연산자와 필터에 사용할 문자열과 같은 데이터 형식에 명시적으로 캐스팅되어야 합니다.

다음 CSV 형식의 데이터가 있는 사용자 지정 로그를 살펴봅니다.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

이 데이터를 구문 분석하고 두 개의 계산된 속성으로 요약하는 쿼리는 다음과 같습니다. 첫 번째 줄은 _RawData_ 속성을 문자열 배열로 분할합니다. 다음 각 줄은 개별 속성에 이름을 지정하고, 함수를 사용하여 출력에 추가하여 적절한 데이터 형식으로 변환합니다.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>쿼리에서 미리 정의된 구조체 구문 분석
데이터 형식이 알려진 구조체로 지정된 경우 [Kusto 쿼리 언어](/azure/kusto/query/)의 함수 중 하나를 사용하여 미리 정의된 구조체를 구문 분석할 수 있습니다.

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL](/azure/kusto/query/parseurlfunction)
- [URL 쿼리](/azure/kusto/query/parseurlqueryfunction)
- [파일 경로](/azure/kusto/query/parsepathfunction)
- [사용자 에이전트](/azure/kusto/query/parse-useragentfunction)
- [버전 문자열](/azure/kusto/query/parse-versionfunction)

다음 예제 쿼리에서는 JSON으로 구조화된 _AzureActivity_ 테이블의 _Properties_ 필드를 구문 분석합니다. 이 쿼리는 JSON에서 명명된 개별 값을 포함하는 _parsedProp_라는 동적 속성에 결과를 저장합니다. 이러한 값은 쿼리 결과를 필터링하고 요약하는 데 사용됩니다.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

이러한 구문 분석 함수는 프로세서를 집약적으로 사용할 수 있으므로 형식이 지정된 데이터의 여러 속성을 쿼리에서 사용하는 경우에만 사용해야 합니다. 그렇지 않으면 간단한 패턴 일치 처리가 빨라집니다.

다음 예제에서는 도메인 컨트롤러 TGT Preauth 형식의 분석을 보여 줍니다. 형식은 EventData XML 문자열 필드에만 존재하지만 이 필드의 다른 데이터는 필요하지 않습니다. 이 경우 [parse](/azure/kusto/query/parseoperator)를 사용하여 필요한 데이터 조각을 추출합니다.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>함수를 사용하여 테이블 시뮬레이션
특정 테이블에 대해 동일한 구문 분석을 수행하는 쿼리가 여러 개 있을 수 있습니다. 이 경우 각 쿼리에서 구문 분석 논리를 복제하는 대신 구문 분석된 데이터를 반환하는 [함수를 만듭니다](functions.md). 그러면 다른 쿼리에서 원래 테이블 대신 함수 별칭을 사용할 수 있습니다.

쉼표로 구분된 위의 사용자 지정 로그 예제를 살펴봅니다. 여러 쿼리에서 구문 분석된 데이터를 사용하기 위해 다음 쿼리를 사용하여 함수를 만들고 _MyCustomCSVLog_ 별칭을 사용하여 저장합니다.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

이제 다음과 같은 쿼리에서 실제 테이블 이름 대신 _MyCustomCSVLog_ 별칭을 사용할 수 있습니다.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>다음 단계
* 데이터 원본 및 솔루션에서 수집한 데이터를 분석하는 [로그 쿼리](log-query-overview.md)에 대해 알아봅니다.