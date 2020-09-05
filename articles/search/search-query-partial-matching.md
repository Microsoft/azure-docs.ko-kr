---
title: 부분 용어, 패턴 및 특수 문자
titleSuffix: Azure Cognitive Search
description: 와일드 카드, regex 및 prefix 쿼리를 사용 하 여 Azure Cognitive Search 쿼리 요청에서 전체 또는 부분 용어를 일치 시킵니다. 특수 문자를 포함 하는 일치 패턴은 전체 쿼리 구문 및 사용자 지정 분석기를 사용 하 여 확인할 수 있습니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 357f44149cb17976556c1e4609f6f2af531b80ee
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935775"
---
# <a name="partial-term-search-and-patterns-with-special-characters-wildcard-regex-patterns"></a>특수 문자를 포함 하는 부분 용어 검색 및 패턴 (와일드 카드, regex, 패턴)

*부분 용어 검색* 은 용어 조각으로 구성 된 쿼리를 의미 합니다. 여기서 전체 용어 대신 시작, 중간 또는 끝 (접두사, 중 위 또는 접미사 쿼리 라고도 함)을 사용할 수 있습니다. 부분 용어 검색에는 쿼리 문자열의 일부인 대시 또는 슬래시와 같은 특수 문자를 사용 하는 조각 조합이 포함 될 수 있습니다. 일반적인 사용 사례에는 전화 번호, URL, 코드 또는 하이픈으로 구분 된 복합 단어의 일부가 포함 됩니다.

인덱스에 필요한 형식의 토큰이 없으면 특수 문자를 포함 하는 부분 용어 검색 및 쿼리 문자열에 문제가 있을 수 있습니다. 인덱싱의 [어휘 분석 단계](search-lucene-query-architecture.md#stage-2-lexical-analysis) (기본 표준 분석기로 가정) 중에 특수 문자가 삭제 되 고, 복합 단어가 분할 되 고, 공백이 삭제 됩니다. 일치 하는 항목이 없으면 쿼리가 실패할 수 있습니다. 예를 들어 `+1 (425) 703-6214` (토큰화 as,,,)와 같은 전화 번호 `"1"` `"425"` `"703"` `"6214"` `"3-62"` 는 실제로 인덱스에 존재 하지 않기 때문에 쿼리에 표시 되지 않습니다. 

이 솔루션은 쿼리 문자열에 공백과 문자를 포함할 수 있도록 필요한 경우 공백과 특수 문자를 포함 하 여 전체 문자열을 유지 하는 인덱싱 중에 분석기를 호출 하는 것입니다. 마찬가지로, 더 작은 부분으로 토큰화 되지 않는 전체 문자열을 사용 하면 "시작" 또는 "종료" 쿼리로 패턴 일치가 가능 합니다. 여기서 제공 하는 패턴은 어휘 분석에서 변환 되지 않은 용어에 대해 평가할 수 있습니다. 그대로 유지 되는 문자열에 대 한 추가 필드를 만들고, 전체 용어 토큰을 내보내는 콘텐츠 유지 분석기를 사용 하는 경우 패턴 일치를 위한 솔루션과 특수 문자를 포함 하는 쿼리 문자열을 일치 시킬 수 있습니다.

> [!TIP]
> Postman 및 REST Api에 익숙한 경우 [쿼리 예제 컬렉션을 다운로드](https://github.com/Azure-Samples/azure-search-postman-samples/) 하 여이 문서에서 설명 하는 부분 용어와 특수 문자를 쿼리 합니다.

## <a name="what-is-partial-term-search-in-azure-cognitive-search"></a>Azure Cognitive Search의 부분 용어 검색 이란?

Azure Cognitive Search는 인덱스에서 전체 토큰화 된 용어를 검색 하 고, 와일드 카드 자리 표시자 연산자 ( `*` 및 `?` )를 포함 하거나 쿼리 형식을 정규식으로 지정 하지 않는 한 부분 용어에 대 한 일치 항목을 찾을 수 없습니다. 부분 용어는 다음 기술을 사용 하 여 지정 됩니다.

+ [정규식 쿼리](query-lucene-syntax.md#bkmk_regex) 는 Apache Lucene에서 유효한 정규식이 될 수 있습니다. 

+ [접두사 일치를 사용 하는 와일드 카드 연산자](query-simple-syntax.md#prefix-search) 는 용어 시작을 포함 하는 일반적으로 인식 되는 패턴, `*` `?` `search=cap*` "Cap'n 잭 's 해안 I n n" 또는 "gacc 대문자"와 일치 하는 or 접미사 연산자를 참조 합니다. 접두사 일치는 단순 및 전체 Lucene 쿼리 구문에서 지원 됩니다.

+ 중 [위 및 접미사 일치가 있는 와일드 카드](query-lucene-syntax.md#bkmk_wildcard) 는 `*` 및 `?` 연산자를 용어의 시작 부분에 배치 하 고 정규식 구문 (식 앞에 슬래시가 있는 경우)을 사용 해야 합니다. 예를 들어 쿼리 문자열 ( `search=/.*numeric*./` )은 "영숫자" 및 "사전순"에 대 한 결과를 접미사 및 중 위 일치로 반환 합니다.

부분 용어 또는 패턴 검색의 경우 및 유사 항목 검색과 같은 몇 가지 다른 쿼리 형태는 쿼리 시에 사용 되지 않습니다. 파서가 연산자와 구분 기호를 사용 하 여 검색 하는 이러한 쿼리 폼의 경우 쿼리 문자열은 어휘 분석 없이 엔진에 전달 됩니다. 이러한 쿼리 폼의 경우 필드에 지정 된 분석기는 무시 됩니다.

> [!NOTE]
> 부분 쿼리 문자열에 URL 조각의 슬래시와 같은 문자가 포함 된 경우 이스케이프 문자를 추가 해야 할 수 있습니다. JSON에서 슬래시는 역슬래시 `/` 로 이스케이프 됩니다 `\` . 따라서 `search=/.*microsoft.com\/azure\/.*/` 는 URL 조각 "microsoft.com/azure/"에 대 한 구문입니다.

## <a name="solving-partialpattern-search-problems"></a>부분/패턴 검색 문제 해결

조각 또는 패턴 또는 특수 문자를 검색 해야 하는 경우 더 간단한 토큰화 규칙에 따라 작동 하는 사용자 지정 분석기로 기본 분석기를 재정의 하 여 인덱스에 전체 문자열을 유지할 수 있습니다. 다시 한 단계를 수행 하는 방법은 다음과 같습니다.

+ 그대로 유지 되는 버전의 문자열을 저장할 필드 정의 (쿼리 시 분석 및 분석 되지 않은 텍스트를 원하는 경우)
+ 적절 한 세분성 수준에서 토큰을 내보내는 다양 한 분석기를 평가 하 고 선택 합니다.
+ 필드에 분석기를 할당 합니다.
+ 인덱스 작성 및 테스트

> [!TIP]
> 분석기 평가는 자주 인덱스를 다시 작성 해야 하는 반복적인 프로세스입니다. Postman을 사용 하 여이 단계를 더 쉽게 수행할 수 있습니다. [인덱스 만들기](/rest/api/searchservice/create-index), [인덱스 삭제](/rest/api/searchservice/delete-index),[문서 로드](/rest/api/searchservice/addupdate-or-delete-documents)및 [문서 검색](/rest/api/searchservice/search-documents)을 위한 REST api입니다. 문서 로드의 경우 요청 본문에는 테스트 하려는 작은 대표 데이터 집합 (예: 전화 번호 또는 제품 코드를 포함 하는 필드)이 포함 되어야 합니다. 동일한 Postman 컬렉션에서 이러한 Api를 사용 하 여 이러한 단계를 빠르게 순환할 수 있습니다.

## <a name="duplicate-fields-for-different-scenarios"></a>여러 시나리오에 대 한 중복 필드

분석기는 인덱스에서 용어를 토큰화 하는 방법을 결정 합니다. 분석기는 필드 단위로 할당 되므로 다양 한 시나리오에 맞게 최적화 하기 위해 인덱스에 필드를 만들 수 있습니다. 예를 들어 첫 번째에 대 한 일반 전체 텍스트 검색을 지원 하 고 두 번째에 고급 패턴 일치를 지원 하기 위해 "featureCode" 및 "featureCodeRegex"를 정의할 수 있습니다. 각 필드에 할당 된 분석기는 각 필드의 내용이 인덱스에서 토큰화 되는 방법을 결정 합니다.  

```json
{
  "name": "featureCode",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": null
},
{
  "name": "featureCodeRegex",
  "type": "Edm.String",
  "retrievable": true,
  "searchable": true,
  "analyzer": "my_custom_analyzer"
},
```

## <a name="choose-an-analyzer"></a>분석기 선택

전체 용어 토큰을 생성 하는 분석기를 선택할 때 다음 분석기를 선택 하는 것이 일반적입니다.

| 분석기 | 동작 |
|----------|-----------|
| [언어 분석기](index-add-language-analyzers.md) | 복합 단어 또는 문자열, 모음 변경이 및 동사 폼에서 하이픈을 유지 합니다. 쿼리 패턴에 대시가 포함 되어 있으면 언어 분석기를 사용 하는 것으로 충분할 수 있습니다. |
| [키워드로](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) | 전체 필드의 콘텐츠는 단일 용어로 토큰화 됩니다. |
| [공백](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html) | 공백만을 구분 합니다. 대시 또는 다른 문자를 포함 하는 용어는 단일 토큰으로 처리 됩니다. |
| [사용자 지정 분석기](index-add-custom-analyzers.md) | 바람직하지 사용자 지정 분석기를 만들면 토크 및 token 필터를 모두 지정할 수 있습니다. 이전 분석기를 있는 그대로 사용 해야 합니다. 사용자 지정 분석기를 사용 하 여 사용할 토크 나이저 및 토큰 필터를 선택할 수 있습니다. <br><br>권장 되는 조합은 [소문자 토큰 필터](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)를 사용 하는 [키워드 토크](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html) 를 사용 하는 것입니다. 미리 정의 된 [키워드인 analyzer](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) 는 단독으로 소문자 텍스트를 따르지 않으므로 쿼리가 실패할 수 있습니다. 사용자 지정 분석기는 소문자 토큰 필터를 추가 하는 메커니즘을 제공 합니다. |

Postman과 같은 웹 API 테스트 도구를 사용 하는 경우 [테스트 분석기 REST 호출](/rest/api/searchservice/test-analyzer) 을 추가 하 여 토큰화 된 출력을 검사할 수 있습니다.

작업 하려면 채우기 인덱스가 있어야 합니다. 기존 인덱스 및 대시 또는 부분 용어를 포함 하는 필드를 지정 하는 경우 특정 조건에 대해 다양 한 분석기를 시도 하 여 어떤 토큰을 내보낼지 확인할 수 있습니다.  

1. 먼저 표준 분석기를 확인 하 여 용어가 기본적으로 토큰화 되는 방법을 확인 합니다.

   ```json
   {
   "text": "SVP10-NOR-00",
   "analyzer": "standard"
   }
    ```

1. 응답을 평가 하 여 인덱스 내에서 텍스트가 토큰화 되는 방법을 확인 합니다. 각 용어는 소문자와 구분 되는 방식을 확인 합니다. 이러한 토큰과 일치 하는 쿼리만이 문서를 결과에 반환 합니다. "10-또는"을 포함 하는 쿼리는 실패 합니다.

    ```json
    {
        "tokens": [
            {
                "token": "svp10",
                "startOffset": 0,
                "endOffset": 5,
                "position": 0
            },
            {
                "token": "nor",
                "startOffset": 6,
                "endOffset": 9,
                "position": 1
            },
            {
                "token": "00",
                "startOffset": 10,
                "endOffset": 12,
                "position": 2
            }
        ]
    }
    ```
1. 이제 또는 분석기를 사용 하도록 요청을 수정 합니다 `whitespace` `keyword` .

    ```json
    {
    "text": "SVP10-NOR-00",
    "analyzer": "keyword"
    }
    ```

1. 이제 응답은 문자열의 일부로 유지 되는 대시가 포함 된 단일 토큰 (대문자)으로 구성 됩니다. 패턴 또는 부분 용어 (예: "10-또는")를 검색 해야 하는 경우 쿼리 엔진은 이제 일치 항목을 찾기 위한 기준을 갖습니다.


    ```json
    {

        "tokens": [
            {
                "token": "SVP10-NOR-00",
                "startOffset": 0,
                "endOffset": 12,
                "position": 0
            }
        ]
    }
    ```
> [!Important]
> 쿼리 트리를 작성할 때 쿼리 파서가 검색 식에서 소문자를 사용 하는 경우가 종종 있습니다. 인덱싱하는 동안 소문자 텍스트 입력을 사용 하지 않는 분석기를 사용 하는 경우 예상 되는 결과가 발생 하지 않는 이유 때문일 수 있습니다. 이 솔루션은 아래의 "사용자 지정 분석기 사용" 섹션에 설명 된 대로 소문자 토큰 필터를 추가 하는 것입니다.

## <a name="configure-an-analyzer"></a>분석기 구성
 
분석기를 평가 하거나 특정 구성으로 전환 하는 경우에는 필드 정의에 분석기를 지정 해야 하며, 기본 제공 분석기를 사용 하지 않는 경우 분석기 자체를 구성 해야 합니다. 분석기를 교환 하는 경우 일반적으로 인덱스를 다시 작성 해야 합니다 (삭제, 다시 만들기 및 다시 로드). 

### <a name="use-built-in-analyzers"></a>기본 제공 분석기 사용

기본 제공 되거나 미리 정의 된 분석기는 `analyzer` 인덱스에 추가 구성이 필요 없는 필드 정의의 속성에 이름으로 지정할 수 있습니다. 다음 예에서는 필드에서 분석기를 설정 하는 방법을 보여 줍니다 `whitespace` . 

다른 시나리오와 기타 기본 제공 분석기에 대 한 자세한 내용은 [미리 정의 된 분석기 목록](/azure/search/index-add-custom-analyzers#predefined-analyzers-reference)을 참조 하십시오. 

```json
    {
      "name": "phoneNumber",
      "type": "Edm.String",
      "key": false,
      "retrievable": true,
      "searchable": true,
      "analyzer": "whitespace"
    }
```

### <a name="use-custom-analyzers"></a>사용자 지정 분석기 사용

[사용자 지정 분석기](index-add-custom-analyzers.md)를 사용 하는 경우, 토큰 필터의 사용자 정의 조합 (가능한 구성 설정)을 사용 하 여 인덱스에이를 정의 합니다. 그런 다음 기본 제공 분석기와 마찬가지로 필드 정의에서 참조 합니다.

목표가 전체 용어 토큰화 인 경우 **키워드 토크** 및 **소문자 토큰 필터로** 구성 된 사용자 지정 분석기를 권장 합니다.

+ 키워드 토크는 필드의 전체 내용에 대 한 단일 토큰을 만듭니다.
+ 소문자 토큰 필터는 대문자를 소문자 텍스트로 변환 합니다. 일반적으로 쿼리 파서는 대문자 텍스트 입력을 모두 소문자로 바꿉니다. 대/소문자를 구분 하면 토큰화 된 용어를 사용 하 여 입력을 homogenizes 합니다.

다음 예제에서는 키워드 토크 및 소문자 토큰 필터를 제공 하는 사용자 지정 분석기를 보여 줍니다.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": []
```

> [!NOTE]
> `keyword_v2`토크 및 `lowercase` token 필터는 시스템에서 인식 되며 기본 구성을 사용 하 여이를 먼저 정의 하지 않고도 이름으로 참조할 수 있습니다.

## <a name="build-and-test"></a>빌드 및 테스트

시나리오를 지 원하는 분석기 및 필드 정의를 사용 하 여 인덱스를 정의한 후에는 부분 문자열 쿼리를 테스트할 수 있도록 대표 문자열이 있는 문서를 로드 합니다. 

이전 섹션에서는 논리에 대해 설명 했습니다. 이 섹션에서는 솔루션을 테스트할 때 호출 해야 하는 각 API를 단계별로 안내 합니다. 앞에서 설명한 것 처럼 Postman과 같은 대화형 웹 테스트 도구를 사용 하는 경우 이러한 작업을 신속 하 게 단계별로 수행할 수 있습니다.

+ [인덱스 삭제](/rest/api/searchservice/delete-index) 는 다시 만들 수 있도록 동일한 이름의 기존 인덱스를 제거 합니다.

+ [Create index](/rest/api/searchservice/create-index) 분석기 사양을 사용 하 여 분석기 정의 및 필드를 포함 하 여 검색 서비스에 인덱스 구조를 만듭니다.

+ [문서 로드](/rest/api/searchservice/addupdate-or-delete-documents) 는 인덱스와 동일한 구조를 가진 문서 및 검색 가능한 콘텐츠를 가져옵니다. 이 단계를 수행한 후에는 인덱스를 쿼리하거나 테스트할 준비가 된 것입니다.

+ [분석기 선택](#choose-an-analyzer)에서 [테스트 분석기](/rest/api/searchservice/test-analyzer) 가 도입 되었습니다. 다양 한 분석기를 사용 하 여 인덱스의 일부 문자열을 테스트 하 여 용어를 토큰화 하는 방법을 이해 합니다.

+ [문서 검색](/rest/api/searchservice/search-documents) 에서는 와일드 카드 및 정규식에 대 한 [단순 구문](query-simple-syntax.md) 또는 [전체 Lucene 구문을](query-lucene-syntax.md) 사용 하 여 쿼리 요청을 생성 하는 방법을 설명 합니다.

  "+ 1 (425) 703-6214"에서 일치 항목을 찾기 위해 "3-6214"을 쿼리 하는 등의 부분 용어 쿼리를 사용 하는 경우 간단한 구문을 사용할 수 `search=3-6214&queryType=simple` 있습니다.

  "Num" 쿼리 또는 "영숫자"의 일치 항목 찾기를 위한 숫자 쿼리 등의 중 위 및 접미사 쿼리를 사용 하는 경우 전체 Lucene 구문과 정규식을 사용 합니다. `search=/.*num.*/&queryType=full`

## <a name="tune-query-performance"></a>쿼리 성능 조정

Keyword_v2 토크 및 소문자 토큰 필터를 포함 하는 권장 구성을 구현 하는 경우 인덱스의 기존 토큰에 대 한 추가 토큰 필터 처리로 인해 쿼리 성능이 저하 되는 것을 알 수 있습니다. 

다음 예에서는 접두사를 더 빠르게 일치 시키는 [EdgeNGramTokenFilter](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html) 을 추가 합니다. 문자를 포함 하는 2-25 문자 조합에에 대 한 추가 토큰이 생성 됩니다 (예: MS, MSF, MSFT, MSFT/, MSFT/S, MSFT/SQ, MSFT/SQL). 

짐작할 수 있듯이 추가 토큰화 결과는 더 큰 인덱스입니다. 큰 인덱스를 수용할 수 있는 용량이 충분 한 경우 응답 시간이 더 빠른이 방법이 더 나은 솔루션 일 수 있습니다.

```json
{
"fields": [
  {
  "name": "accountNumber",
  "analyzer":"myCustomAnalyzer",
  "type": "Edm.String",
  "searchable": true,
  "filterable": true,
  "retrievable": true,
  "sortable": false,
  "facetable": false
  }
],

"analyzers": [
  {
  "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
  "name":"myCustomAnalyzer",
  "charFilters":[],
  "tokenizer":"keyword_v2",
  "tokenFilters":["lowercase", "my_edgeNGram"]
  }
],
"tokenizers":[],
"charFilters": [],
"tokenFilters": [
  {
  "@odata.type":"#Microsoft.Azure.Search.EdgeNGramTokenFilterV2",
  "name":"my_edgeNGram",
  "minGram": 2,
  "maxGram": 25,
  "side": "front"
  }
]
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 분석기에서 쿼리 문제를 해결 하 고 쿼리 문제를 해결 하는 방법을 설명 합니다. 다음 단계로 인덱싱 및 쿼리 처리에 대 한 분석기의 영향을 자세히 살펴보겠습니다. 특히 분석기가 인덱스에 대해 만드는 작업을 정확히 확인할 수 있도록 분석 텍스트 API를 사용 하 여 토큰화 된 출력을 반환 하는 것이 좋습니다.

+ [언어 분석기](search-language-support.md)
+ [Azure Cognitive Search에서 텍스트 처리를 위한 분석기](search-analyzers.md)
+ [텍스트 API 분석 (REST)](/rest/api/searchservice/test-analyzer)
+ [전체 텍스트 검색의 작동 방식 (쿼리 아키텍처)](search-lucene-query-architecture.md)