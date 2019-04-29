---
title: Azure Monitor 로그에서 쿼리 검색 | Microsoft Docs
description: 이 문서에서는 Azure Monitor 로그 쿼리에서 검색 사용을 시작하기 위한 자습서를 제공합니다.
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
ms.date: 08/06/2018
ms.author: bwren
ms.openlocfilehash: 2df4cf994e118fef9048504daf40fabc1625c375
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61425914"
---
# <a name="search-queries-in-azure-monitor-logs"></a>Azure Monitor 로그에서 쿼리 검색

> [!NOTE]
> 이 단원을 완료하기 전에 [Azure Monitor 로그 쿼리 시작](get-started-queries.md)을 완료해야 합니다.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Azure Monitor 로그 쿼리는 테이블 이름 또는 검색 명령을 사용하여 시작할 수 있습니다. 이 자습서는 검색 기반 쿼리를 다룹니다. 각 메서드에 대한 이점이 있습니다.

테이블 기반 쿼리는 쿼리의 범위를 지정하여 시작하므로 검색 쿼리보다 효율적인 경향이 있습니다. 검색 쿼리는 덜 구조적이며 이로 인해 열 또는 테이블에서 특정 값을 검색할 때 더 좋은 선택입니다. **search**는 지정된 값에 대해 지정된 테이블 또는 모든 테이블에서 모든 열을 검색할 수 있습니다. 처리되는 데이터의 양은 매우 클 수 있으며 이는 이러한 쿼리를 완료하는 데 시간이 더 오래 걸릴 수 있는 이유이며 매우 큰 결과 집합을 반환할 수 있습니다.

## <a name="search-a-term"></a>용어 검색
**search** 명령은 일반적으로 특정 용어를 검색하는 데 사용됩니다. 다음 예제에서 모든 테이블의 모든 열은 "error"라는 용어에 대해 검색됩니다.

```Kusto
search "error"
| take 100
```

쉽게 사용할 수 있는 반면 위에 표시된 것처럼 범위가 지정되지 않은 쿼리는 효율적이지 않으며 관련이 없는 많은 결과를 반환할 가능성이 높습니다. 관련 테이블에서 또는 특정 열을 검색하는 것이 좋습니다.

### <a name="table-scoping"></a>테이블 범위 지정
특정 테이블에서 용어를 검색하려면 **search** 연산자 바로 뒤에 `in (table-name)`을 추가합니다.

```Kusto
search in (Event) "error"
| take 100
```

또는 여러 테이블에서:
```Kusto
search in (Event, SecurityEvent) "error"
| take 100
```

### <a name="table-and-column-scoping"></a>테이블 및 열 범위 지정
기본적으로 **search**는 데이터 집합의 모든 열을 평가합니다. 특정 열만을 검색하려면 이 구문을 사용합니다.

```Kusto
search in (Event) Source:"error"
| take 100
```

> [!TIP]
> `:` 대신 `==`를 사용하는 경우 결과는 이 대/소문자 구분에서 *Source* 열에 정확한 값 "error"가 있는 레코드를 포함합니다. ':'을 사용하면 *Source*에 "오류 코드 404" 또는 "Error"와 같은 값이 있는 레코드를 포함하지 않습니다.

## <a name="case-sensitivity"></a>대/소문자 구분
기본적으로 용어 검색은 대/소문자를 구분하지 않으므로 "dns"를 검색하면 "DNS", "dns" 또는 "Dns"와 같은 결과가 발생할 수 있습니다. 대/소문자를 구분하여 검색하려면 `kind` 옵션을 사용합니다.

```Kusto
search kind=case_sensitive in (Event) "DNS"
| take 100
```

## <a name="use-wild-cards"></a>와일드 카드 사용
**search** 명령은 용어의 시작, 끝 또는 중간에서 와일드 카드를 지원합니다.

"win"으로 시작하는 용어를 검색하려면:
```Kusto
search in (Event) "win*"
| take 100
```

".com"으로 끝나는 용어를 검색하려면:
```Kusto
search in (Event) "*.com"
| take 100
```

"www"를 포함하는 용어를 검색하려면:
```Kusto
search in (Event) "*www*"
| take 100
```

"corp"로 시작하고 "corp.mydomain.com"과 같은 ".com"으로 종료하는 용어를 검색하려면:

```Kusto
search in (Event) "corp*.com"
| take 100
```

와일드 카드 `search in (Event) *`만을 사용하여 테이블에서 모든 것을 가져올 수도 있지만 `Event`를 작성하는 것과 동일합니다.

> [!TIP]
> `search *`를 사용하여 모든 테이블에서 모든 열을 가져올 수 있지만 항상 특정 테이블로 쿼리의 범위를 지정하는 것이 좋습니다. 범위가 지정되지 않은 쿼리는 완료하는 데 시간이 걸릴 수 있으며 너무 많은 결과를 반환할 수 있습니다.

## <a name="add-and--or-to-search-queries"></a>*and* / *or*를 추가하여 쿼리 검색
**and**를 사용하여 여러 용어를 포함하는 레코드를 검색합니다.

```Kusto
search in (Event) "error" and "register"
| take 100
```

**or**를 사용하여 하나 이상의 용어를 포함하는 레코드를 가져옵니다.

```Kusto
search in (Event) "error" or "register"
| take 100
```

여러 검색 조건이 있는 경우 괄호를 사용하여 동일한 쿼리로 결합할 수 있습니다.

```Kusto
search in (Event) "error" and ("register" or "marshal*")
| take 100
```

이 예제의 결과는 용어 "error"를 포함하고 "register" 또는 "marshal"로 시작하는 것도 포함하는 레코드입니다.

## <a name="pipe-search-queries"></a>검색 쿼리 파이프
다른 명령과 마찬가지로 **search**는 파이프될 수 있으므로 검색 결과는 필터링, 정렬 및 집계될 수 있습니다. 예를 들어 "win"을 포함하는 *Event* 레코드의 수를 가져오려면:

```Kusto
search in (Event) "win"
| count
```




## <a name="next-steps"></a>다음 단계

- [ 쿼리 언어 사이트](/azure/kusto/query/)에서 추가 자습서를 참조하세요.