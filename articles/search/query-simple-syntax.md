---
title: 단순 쿼리 구문
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 전체 텍스트 검색 쿼리에 사용 되는 단순 쿼리 구문에 대 한 참조입니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: fc1eb1836badc3ced688750bbc7c7a164773d022
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152672"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Azure Cognitive Search의 단순 쿼리 구문

Azure Cognitive Search는 두 가지 Lucene 기반 쿼리 언어 즉, [단순 쿼리 파서와](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) [lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 구현 합니다. Azure Cognitive Search에서는 단순 쿼리 구문에서 유사 항목/slop 옵션을 제외 합니다.  

> [!NOTE]
> 단순 쿼리 구문은 [검색 문서](https://docs.microsoft.com/rest/api/searchservice/search-documents) api의 **검색** 매개 변수에 전달 된 쿼리 식에 사용 되며, 해당 api의 [$filter](search-filters.md) 매개 변수에 사용 되는 [OData 구문과](query-odata-filter-orderby-syntax.md) 혼동 되지 않습니다. 이러한 다른 구문에는 쿼리를 생성 하 고 문자열을 이스케이프 처리 하는 데 사용할 수 있는 고유 규칙이 있습니다.
>
> Azure Cognitive Search는 **검색** 매개 변수에서 보다 복잡 한 쿼리에 대 한 대체 [전체 Lucene 쿼리 구문을](query-lucene-syntax.md) 제공 합니다. 쿼리 구문 분석 아키텍처와 각 구문의 이점에 대 한 자세한 내용은 [Azure Cognitive Search에서 전체 텍스트 검색의 작동 방식](search-lucene-query-architecture.md)을 참조 하세요.

## <a name="how-to-invoke-simple-parsing"></a>단순 구문 분석 호출 방법

단순 구문은 기본값입니다. 호출은 전체 구문에서 단순 구문으로 다시 설정하는 경우에만 필요합니다. 구문을 명시적으로 설정하려면 `queryType` 검색 매개 변수를 사용합니다. 유효한 값에는 기본값이 `simple|full`인 `simple`과 Lucene용 `full`이 포함됩니다. 

## <a name="query-behavior-anomalies"></a>쿼리 동작 변칙

하나 이상의 단어를 포함하는 모든 텍스트는 쿼리 실행의 유효한 시작점으로 간주됩니다. Azure Cognitive Search는 텍스트 분석 중에 발견 된 변형을 비롯 하 여 모든 용어 또는 모든 용어를 포함 하는 문서와 일치 합니다. 

이와 같이 간단 하 게 하기 때문에 Azure Cognitive Search에서 예기치 않은 결과를 발생 *시킬 수* 있는 쿼리 실행의 한 가지 측면은 입력 문자열에 추가 된 용어 및 연산자가 추가 될 때 검색 결과를 줄이는 대신 향상 됩니다. 이러한 증가가 실제로 발생하는지 여부는 NOT이 AND 또는 OR 동작과 관련해서 해석되는 방식을 결정하는 `searchMode` 매개 변수와 NOT 연산자를 포함하는지에 따라 좌우됩니다. 기본값인 `searchMode=Any`와 NOT 연산자를 지정할 경우 연산은 OR 작업으로 계산되므로 `"New York" NOT Seattle`은 시애틀이 아닌 모든 도시를 반환합니다.  

일반적으로는 콘텐츠를 검색하는 애플리케이션의 사용자 상호 작용 패턴에서 이러한 동작이 나타나기 쉽습니다. 이 경우 사용자는 기본 제공 방식의 탐색 구조를 갖는 전자 상거래 사이트와 달리, 쿼리에 연산자를 포함할 가능성이 높습니다. 자세한 내용은 [NOT 연산자](#not-operator)를 참조하세요. 

## <a name="boolean-operators-and-or-not"></a>부울 연산자 (AND, OR, NOT) 

쿼리 문자열에 연산자를 포함 하 여 일치 하는 문서를 찾을 수 있는 다양 한 조건 집합을 작성할 수 있습니다. 

### <a name="and-operator-"></a>AND 연산자 `+`

AND 연산자는 더하기 기호입니다. 예를 들어, `wifi+luxury`는 `wifi`와 `luxury`를 둘 다 포함하는 문서를 검색합니다.

### <a name="or-operator-"></a>OR 연산자 `|`

OR 연산자는 세로줄 또는 파이프 문자입니다. 예를 들어, `wifi | luxury`는 `wifi` 또는 `luxury` 중 하나를 포함하는 문서를 검색합니다.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT 연산자 `-`

NOT 연산자는 빼기 기호입니다. 예를 들어, `wifi –luxury`는 `wifi` 용어를 포함 및/또는 `luxury`를 포함하지 않는 문서를 검색합니다(및/또는은 `searchMode`로 제어).

> [!NOTE]  
>  `searchMode` 옵션은 `+` 또는 `|` 연산자가 없는 경우 NOT 연산자가 적용되는 용어가 쿼리의 다른 용어와 AND로 연결되는지 아니면 OR로 연결되는지를 제어합니다. `searchMode`는 `any`(기본값) 또는 `all`로 설정할 수 있습니다. `any`를 사용하는 경우 이 연산자는 더 많은 결과를 포함하여 쿼리 회수를 증가시키고 기본적으로 `-`는 "OR NOT"으로 해석됩니다. 예를 들어, `wifi -luxury`는 용어 `wifi`를 포함하는 문서 또는 용어 `luxury`를 포함하지 않는 문서를 검색합니다. `all`을 사용하면 이 연산자는 더 적은 결과를 포함하여 쿼리의 정확도를 높이고 기본적으로 -는 "AND NOT"으로 해석됩니다. 예를 들어 `wifi -luxury`는 용어 `wifi`를 포함하고 용어 "luxury"를 포함하지 않는 문서를 검색합니다. 이는 확실히 `-` 연산자에 비해 더욱 직관적인 동작입니다. 따라서 재현율이 아니라 정확도에 따라 최적화하려고 `searchMode=all`하며`searchMode=any` 사용자가 검색에서 *연산자를 자주 사용하는 경우* 대신 `-`을 사용하는 것이 좋습니다.

## <a name="suffix-operator"></a>Suffix 연산자

접미사 연산자는 별표 `*`입니다. 예를 들어, `lux*`는 대/소문자를 무시하고 `lux`로 시작하는 용어가 포함된 문서를 검색합니다.  

## <a name="phrase-search-operator"></a>구 검색 연산자

구 연산자는 `" "`따옴표로 묶습니다. 예를 들어, `Roach Motel`(따옴표 제외)은 순서 및 위치에 관계없이 `Roach` 및/또는 `Motel`을 포함하는 문서를 검색하지만, `"Roach Motel"`(따옴표 포함)은 해당 전체 구를 해당 순서로 포함하는 문서만 검색합니다(텍스트 분석은 계속 적용됨).

## <a name="precedence-operator"></a>선행 연산자

선행 연산자는 문자열을 괄호로 묶습니다 `( )`합니다. 예를 들어 `motel+(wifi | luxury)`은 motel term을 포함 하는 문서를 검색 하 고 `wifi` 또는 `luxury` (또는 둘 다)를 검색 합니다.  

## <a name="escaping-search-operators"></a>검색 연산자 이스케이프  

 위의 기호를 실제 검색 텍스트 부분으로 사용하려면 백슬래시를 접두사로 추가하여 이스케이프해야 합니다. 예를 들어, `luxury\+hotel`은 용어 `luxury+hotel`이 됩니다. 일반적인 검색을 간편하게 수행할 수 있도록, 이 규칙에는 이스케이프가 필요하지 않은 두 가지 예외가 적용됩니다.  

- NOT 연산자 `-`는 공백 뒤의 첫 번째 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어, `wi-fi`는 단일 단어이지만 GUID(예: `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`)는 단일 토큰으로 처리됩니다.
- 접미사 연산자 `*`는 공백 앞의 마지막 문자인 경우에만 이스케이프되고, 단어 중간에 있는 경우에는 이스케이프되지 않아야 합니다. 예를 들어, `wi*fi`는 단일 토큰으로 처리됩니다.

> [!NOTE]  
>  이스케이프는 토큰을 유지하지만, 분석 모드에 따라 텍스트 분석 시 토큰이 분할될 수 있습니다. 자세한 내용은 [언어 &#40;지원 Azure Cognitive Search&#41; REST API](index-add-language-analyzers.md) 를 참조 하세요.  

## <a name="see-also"></a>참고 항목  

+ [문서 &#40;검색 Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene 쿼리 구문](query-lucene-syntax.md)
+ [OData 식 구문](query-odata-filter-orderby-syntax.md) 
