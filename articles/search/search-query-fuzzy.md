---
title: 유사 항목 검색
titleSuffix: Azure Cognitive Search
description: 철자가 잘못 된 용어 또는 오타를 자동으로 수정 하는 검색 환경을 구현 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: faa98f1c52cfe2dd0e19f085f4d33dedb6f01851
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934891"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>철자 및 오타를 수정 하는 유사 항목 검색

Azure Cognitive Search는 입력 문자열에서 오타가 나 철자가 틀린 용어를 보정 하는 쿼리 유형인 유사 항목 검색을 지원 합니다. 비슷한 컴퍼지션이 있는 용어를 검색 하 여이를 수행 합니다. 거의 일치 하는 항목을 처리 하기 위해 검색을 확장 하면 일치 하지 않는 일부 문자만 포함 하는 경우에는 오타가 자동으로 수정 됩니다. 

## <a name="what-is-fuzzy-search"></a>유사 항목 검색 이란?

비슷한 컴퍼지션이 있는 용어와 일치 하는 항목을 생성 하는 확장 연습입니다. 유사 항목 검색이 지정 된 경우 엔진은 쿼리의 모든 용어에 대해 유사 하 게 구성 된 용어의 [automaton](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)을 기반으로 그래프를 작성 합니다. 예를 들어 쿼리에 세 가지 용어 "워싱턴"이 포함 되어 있는 경우 쿼리의 모든 용어에 대해 그래프가 생성 됩니다 `search=university~ of~ washington~` . 유사 항목 검색에서 중지 단어를 제거 하지 않으므로 "의"는 그래프를 가져옵니다.

그래프는 프로세스에서 정확 하 고 잘못 된 변형을 모두 캡처하기 위해 각 용어의 최대 50 확장 또는 순열으로 구성 됩니다. 그런 다음 엔진은 응답에서 가장 관련성이 높은 일치 항목을 반환 합니다. 

"대학"과 같은 용어의 경우 그래프에 "unversty, universty, 대학, universe, 역"이 있을 수 있습니다. 그래프의 항목과 일치 하는 모든 문서는 결과에 포함 됩니다. 텍스트를 분석 하 여 동일한 단어의 다양 한 형태 ("마우스" 및 "마우스")를 처리 하는 다른 쿼리와 달리 유사 항목 쿼리에서의 비교는 텍스트에 대 한 언어 분석 없이 표면 값으로 수행 됩니다. 구문적으로 서로 다른 "Universe" 및 "역"은 구문상 차이가 작기 때문에 일치 합니다.

불일치가 두 개 이하의 편집으로 제한 되는 경우 일치가 성공 합니다. 여기서 편집은 삽입, 삭제, 대체 또는 위치가 바뀐 문자입니다. 차등을 지정 하는 문자열 수정 알고리즘은 [Damerau-Levenshtein distance](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) 메트릭이 며, 한 단어를 다른 단어를 변경 하는 데 필요한 최소 작업 수 (삽입, 삭제, 대체 또는 두 개의 인접 문자의 transpositions)로 설명 됩니다. 

Azure Cognitive Search에서 다음을 수행 합니다.

+ 유사 항목 쿼리는 전체 용어에 적용 되지만 및 구문을 통해 구를 지원할 수 있습니다. 예를 들어 "Unviersty ~ of ~" Wshington ~ "는" 대학 대학 "과 일치 합니다.

+ 편집의 기본 거리는 2입니다. 값은 `~0` 확장을 의미 하지 않으며 (정확히 일치 하는 경우에만 일치 하는 것으로 간주 됨) `~1` 하나의 차이 또는 하나의 편집을 지정할 수 있습니다. 

+ 유사 항목 쿼리는 최대 50 개의 추가 순열의 용어를 확장할 수 있습니다. 이 한도는 구성할 수 없지만, 편집 거리를 1로 줄여서 확장 수를 효과적으로 줄일 수 있습니다.

+ 응답은 관련 일치 항목을 포함 하는 문서로 구성 됩니다 (최대 50).

전체적으로 그래프는 인덱스의 토큰에 대해 일치 조건으로 제출 됩니다. 짐작할 수 있듯이 유사 항목 검색은 다른 쿼리 양식 보다 기본적으로 느립니다. 인덱스의 크기와 복잡성은 해당 혜택이 응답의 대기 시간을 오프셋할 만큼 충분 한지 여부를 결정할 수 있습니다.

> [!NOTE]
> 유사 항목 검색은 속도가 느릴 수 있으므로 n-영문법 인덱싱 등의 대안을 조사 하는 것이 유용할 수 있습니다 (bigram 및 trigram 토큰에 대 한 2 ~ 3 개의 문자 시퀀스). 언어 및 쿼리 화면에 따라 n-영문법을 통해 성능이 향상 될 수 있습니다. 절충은 n-영문법 인덱싱이 저장소 집약적이 고 훨씬 큰 인덱스를 생성 한다는 것입니다.
>
> 가장 황당한 사례를 처리 하려는 경우에만 고려할 수 있는 다른 대안은 [동의어 맵](search-synonyms.md)입니다. 예를 들어 "search"를 "검색, serch, sarch" 또는 "검색"을 "검색"으로 매핑합니다.

## <a name="indexing-for-fuzzy-search"></a>유사 항목 검색 인덱싱

분석기는 쿼리 처리 중에 확장 그래프를 만드는 데 사용 되지 않지만, 퍼지 검색 시나리오에서는 분석기가 무시 되어야 한다는 것을 의미 하지는 않습니다. 모든 작업 후에는 인덱싱 중에 쿼리를 사용 하 여 일치를 수행 하는 토큰을 만들 수 있습니다. 즉, 쿼리가 자유 형식 인지, 필터링 된 검색 인지, 그래프를 입력으로 사용 하는 유사 항목 검색입니다. 

일반적으로 필드 단위로 분석기를 할당 하는 경우 분석 체인을 미세 조정 하는 결정은 유사 항목 검색과 같은 특수 쿼리 양식이 아닌 기본 사용 사례 (필터 또는 전체 텍스트 검색)를 기반으로 합니다. 따라서 유사 항목 검색에 대 한 특정 분석기 권장 사항은 없습니다. 

그러나 테스트 쿼리가 예상과 일치 하는 항목을 생성 하지 않는 경우 인덱싱 분석기를 다양 하 게 설정 하 여 [언어 분석기](index-add-language-analyzers.md)로 설정 하 여 더 나은 결과를 얻을 수 있습니다. 일부 언어, 특히 모음 변경이를 사용 하는 언어는 Microsoft 자연어 프로세서에 의해 생성 된 변 곡점 및 불규칙 단어 양식의 이점을 누릴 수 있습니다. 경우에 따라 올바른 언어 분석기를 사용 하면 사용자가 제공 하는 값과 호환 되는 방식으로 용어가 토큰화 되는지 여부가 달라질 수 있습니다.

## <a name="how-to-use-fuzzy-search"></a>유사 항목 검색을 사용 하는 방법

유사 항목 쿼리는 [lucene 쿼리 파서](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)를 호출 하 여 full Lucene 쿼리 구문을 사용 하 여 생성 됩니다.

1. 쿼리에서 전체 Lucene 파서를 설정 `queryType=full` 합니다 ().

1. 필요에 따라이 매개 변수 ()를 사용 하 여 요청 범위를 특정 필드로 지정 `searchFields=<field1,field2>` 합니다. 

1. `~`전체 용어 ()의 끝에 물결표 () 연산자를 추가 `search=<string>~` 합니다.

   편집 거리 ()를 지정 하려는 경우 0과 2 사이의 숫자 (기본값)를 포함 하는 선택적 매개 변수를 포함 `~1` 합니다. 예를 들어, "blue~" 또는 "blue~1"은 "blue", "blues" 및 "glue"를 반환합니다.

용어 및 거리 (최대 2 개) 외에 Azure Cognitive Search에는 쿼리에 대해 설정할 추가 매개 변수가 없습니다.

> [!NOTE]
> 쿼리를 처리 하는 동안 유사 항목 쿼리는 [어휘 분석](search-lucene-query-architecture.md#stage-2-lexical-analysis)을 거치지 않습니다. 쿼리 입력은 쿼리 트리에 직접 추가 되어 용어 그래프를 만들기 위해 확장 됩니다. 유일 하 게 수행 되는 변환은 대/소문자를 구분 합니다.

## <a name="testing-fuzzy-search"></a>유사 항목 검색 테스트

간단한 테스트를 위해 쿼리 식을 반복 하기 위해 [검색 탐색기](search-explorer.md) 또는 [postman](search-get-started-postman.md) 을 권장 합니다. 두 도구 모두 대화형 이므로 여러 용어를 빠르게 단계별로 실행 하 고 다시 발생 하는 응답을 평가할 수 있습니다.

결과가 모호한 경우 적중 항목 [강조 표시](search-pagination-page-layout.md#hit-highlighting) 는 응답에서 일치 항목을 식별 하는 데 도움이 될 수 있습니다. 

> [!Note]
> 적중 항목을 식별 하기 위해 적중 항목 강조 표시를 사용 하는 경우 제한 사항이 있으며 기본 유사 항목 검색에만 작동 합니다. 인덱스에 점수 매기기 프로필이 있는 경우 또는 추가 구문을 사용 하 여 쿼리를 계층화 하는 경우 적중 항목 강조 표시는 일치 항목을 식별 하지 못할 수 있습니다. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>예제 1: 정확한 단어를 사용 하 여 유사 항목 검색

검색 문서의 필드에 다음 문자열이 있다고 가정 합니다 `"Description"` . `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

"특수"에서 유사 항목 검색을 시작 하 고 설명 필드에 적중 항목 강조 표시를 추가 합니다.

```console
search=special~&highlight=Description
```

응답에서 적중 항목 강조 표시를 추가 했으므로 서식 지정이 일치 하는 용어로 "특수"에 적용 됩니다.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

몇 가지 문자 ("pe")를 수행 하 여 요청을 다시 시도 하 고 "특수" 철자를 확인 합니다.

```console
search=scial~&highlight=Description
```

지금까지 응답이 변경 되지 않았습니다. 기본값인 2도 거리를 사용 하 여 "특수"에서 두 문자 "pe"를 제거 하면 해당 용어에 대 한 성공적인 일치를 계속 해 서 수행할 수 있습니다.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

하나 이상의 요청을 시도한 후 총 세 개의 삭제 ("특수"에서 "배율"로)로 마지막 문자 하나를 수행 하 여 검색 단어를 추가로 수정 합니다.

```console
search=scal~&highlight=Description
```

동일한 응답이 반환 되지만 "특수"에 일치 하는 것이 아니라 "SQL"에서 유사 항목 일치가 발생 합니다.

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

이 확장 된 예제는 적중 항목 강조 표시에서 모호한 결과를 가져올 수 있는 명확성을 보여 주기 위한 것입니다. 모든 경우에 동일한 문서가 반환 됩니다. 문서 Id에 의존 하 여 일치 하는 항목을 확인 했으므로 "특수"에서 "SQL"로의 이동이 누락 되었을 수 있습니다.

## <a name="see-also"></a>참조

+ [Azure Cognitive Search에서 전체 텍스트 검색이 작동 하는 방식 (쿼리 구문 분석 아키텍처)](search-lucene-query-architecture.md)
+ [검색 탐색기](search-explorer.md)
+ [.NET에서 쿼리를 수행하는 방법](./search-get-started-dotnet.md)
+ [REST에서 쿼리를 수행하는 방법](./search-get-started-powershell.md)