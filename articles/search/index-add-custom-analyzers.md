---
title: 사용자 지정 분석기 추가 - Azure Search
description: Azure Search 전체 텍스트 검색 쿼리에서 사용되는 텍스트 토크나이저 및 문자 필터를 수정합니다.
ms.date: 02/14/2019
services: search
ms.service: search
ms.topic: conceptual
author: Yahnoosh
ms.author: jlembicz
ms.manager: cgronlun
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
ms.openlocfilehash: 9a15078c953c1fab40ad521eff079a623c93b9d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60914911"
---
# <a name="add-custom-analyzers-to-an-azure-search-index"></a>Azure Search 인덱스에 사용자 지정 분석기 추가

*사용자 지정 분석기*는 기존 토크나이저 및 선택적 필터의 사용자 정의 조합으로 구성된 특정 유형의 [텍스트 분석기](search-analyzers.md)입니다. 토크나이저와 필터를 새로운 방식으로 결합하면 특정 결과를 달성하도록 검색 엔진에서 처리하는 텍스트를 사용자 지정할 수 있습니다. 예를 들어, *문자 필터*로 사용자 지정 분석기를 만들어 텍스트 입력이 토큰화되기 전에 HTML 태그를 제거할 수 있습니다.

 필터의 조합을 다양하게 만드는 여러 사용자 지정 분석기를 정의할 수 있지만 각 필드는 분석을 인덱싱하는 분석기 하나와 검색 분석에 대한 분석기 하나만을 사용할 수 있습니다. 고객 분석기의 모양은 [사용자 지정 분석기 예제](search-analyzers.md#Custom-analyzer-example)를 참조하세요.

## <a name="overview"></a>개요

 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)의 역할은 간단히 말해서 효율적인 쿼리 및 검색이 가능하도록 문서를 처리하고 저장하는 것입니다. 상위 수준에서 볼 때는 문서에서 중요한 단어를 추출하고, 인덱스에 추가한 후, 해당 인덱스를 사용하여 지정된 쿼리의 단어와 일치하는 문서를 찾는 것이 핵심입니다. 설명서 및 검색 쿼리에서 단어를 추출하는 프로세스를 *어휘 분석*이라고 합니다. 어휘 분석을 수행하는 구성 요소를 *분석기*라고 합니다.

 Azure Search에서는 [분석기](#AnalyzerTable) 테이블의 미리 정의된 언어 중립적 분석기 세트 또는 [언어 분석기 &#40;Azure Search Service REST API&#41;](index-add-language-analyzers.md)에 나열된 언어별 분석기 세트에서 선택할 수 있습니다. 사용자 고유의 사용자 지정 분석기를 정의하는 옵션도 제공됩니다.  

 사용자 지정 분석기를 사용하여 텍스트를 인덱싱 가능하고 검색 가능한 토큰으로 변환하는 프로세스를 제어할 수 있습니다 이것은 미리 정의된 단일 토크나이저, 하나 이상의 토큰 필터 및 하나 이상의 문자 필터로 구성된 사용자 정의 구성입니다. 토크나이저는 텍스트를 토큰과 토크나이저에서 내보낸 토큰을 수정하는 토큰 필터로 구분합니다. 문자 필터는 토크나이저에서 처리되기 전의 입력 텍스트를 준비하기 위해 적용됩니다. 예를 들어, 문자 필터는 특정 문자 또는 기호를 대신할 수 있습니다.

 사용자 지정 분석기에서 사용된 인기 있는 시나리오는 다음과 같습니다.  

- 음성 검색. 음성 필터를 추가하여 단어의 철자가 아닌 소리를 기반으로 검색을 사용합니다.  

- 어휘 분석 사용 안 함. 키워드 분석기를 사용하여 분석되지 않은 검색 가능한 필드를 만듭니다  

- 빠른 접두사/접미사 검색. 단어의 인덱스 접두사에 N-gram 토큰 필터를 추가하여 빠른 접두사 일치를 사용합니다. 역방향 토큰 필터와 결합하여 접미사 일치를 수행합니다.  

- 사용자 지정 토큰화. 예를 들어 공백을 구분 기호로 사용하여 토큰에 문장을 분리하도록 공백 토크나이저를 사용합니다.  

- ASCII 접기. 표준 ASCII 접기 필터를 추가하여 검색 용어에서 ö 또는 ê와 같은 분음 부호를 정규화합니다.  

  이 페이지는 지원되는 분석기, 토크나이저, 토큰 필터 및 문자 필터의 목록을 제공합니다. 사용 예제에서 인덱스 정의에 대한 변경 설명을 확인할 수도 있습니다. Azure Search 구현에 활용되는 기본 기술에 대한 자세한 배경 정보는 [분석 패키지 요약(Lucene)](https://lucene.apache.org/core/4_10_0/core/org/apache/lucene/codecs/lucene410/package-summary.html)을 참조하세요. 분석기 구성의 예제는 [Azure Search의 분석기 추가](search-analyzers.md#examples)를 참조하세요.

## <a name="validation-rules"></a>유효성 검사 규칙  
 분석기, 토크나이저, 토큰 필터 및 문자 필터의 이름은 고유해야 하며 미리 정의된 분석기, 토크나이저, 토큰 필터 또는 문자 필터와 같을 수 없습니다. 이미 사용 중인 이름에 대해서는 [속성 참조](#PropertyReference)를 참조하세요.

## <a name="create-custom-analyzers"></a>사용자 지정 분석기 만들기
 인덱스를 만들 때 사용자 지정 분석기를 정의할 수 있습니다. 사용자 지정 분석기를 지정하는 구문은 이 섹션에 설명되어 있습니다. 또한 [Azure Search의 분석기 추가](search-analyzers.md#examples)에 샘플 정의를 검토하여 구문을 숙지할 수도 있습니다.  

 분석기 정의에는 토큰화 후 처리를 위한 이름, 형식, 하나 이상의 문자 필터, 최대 하나의 토크나이저가 포함됩니다. 문자 필터는 토큰화 전에 적용됩니다. 토큰 필터 및 문자 필터는 왼쪽에서 오른쪽으로 적용됩니다.

 `tokenizer_name` 토크 나이저를의 이름인 `token_filter_name_1` 및 `token_filter_name_2` 토큰 필터의 이름 및 `char_filter_name_1` 및 `char_filter_name_2` char 이름을 필터가 (참조를 [토크 나이저](#Tokenizers), [ 토큰 필터](#TokenFilters) Char 유효한 값에 대 한 테이블을 필터링).

분석기 정의는 더 큰 인덱스의 일부입니다. 나머지 인덱스에 대한 내용은 [인덱스 만들기 API](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

```
"analyzers":(optional)[
   {
      "name":"name of analyzer",
      "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
      "charFilters":[
         "char_filter_name_1",
         "char_filter_name_2"
      ],
      "tokenizer":"tokenizer_name",
      "tokenFilters":[
         "token_filter_name_1",
         "token_filter_name_2"
      ]
   },
   {
      "name":"name of analyzer",
      "@odata.type":"#analyzer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"charFilters":(optional)[
   {
      "name":"char_filter_name",
      "@odata.type":"#char_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenizers":(optional)[
   {
      "name":"tokenizer_name",
      "@odata.type":"#tokenizer_type",
      "option1":value1,
      "option2":value2,
      ...
   }
],
"tokenFilters":(optional)[
   {
      "name":"token_filter_name",
      "@odata.type":"#token_filter_type",
      "option1":value1,
      "option2":value2,
      ...
   }
]
```

> [!NOTE]  
>  만든 사용자 지정 분석기는 Azure 포털에서 노출되지 않습니다. 사용자 지정 분석기를 추가하는 유일한 방법은 인덱스를 정의할 때 API를 호출하는 코드를 통해서입니다.  

 인덱스 정의 내에서 인덱스 만들기 요청 본문의 아무 위치에나 이 섹션을 배치할 수 있지만 일반적으로는 맨 끝에 배치합니다.  

```
{
  "name": "name_of_index",
  "fields": [ ],
  "suggesters": [ ],
  "scoringProfiles": [ ],
  "defaultScoringProfile": (optional) "...",
  "corsOptions": (optional) { },
  "analyzers":(optional)[ ],
  "charFilters":(optional)[ ],
  "tokenizers":(optional)[ ],
  "tokenFilters":(optional)[ ]
}
```

문자 필터, 토크나이저 및 토큰 필터의 정의는 사용자 지정 옵션을 설정하는 경우에만 인덱스에 추가됩니다. 기존 필터 또는 토크나이저를 있는 그대로 사용하려면 분석기 정의에서 이름으로 지정합니다.

<a name="Testing custom analyzers"></a>

## <a name="test-custom-analyzers"></a>사용자 지정 분석기 테스트

[REST API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)에서 **분석기 작업 테스트**를 사용하여 분석기가 지정된 텍스트를 토큰으로 분할하는 방법을 확인할 수 있습니다.

**요청**
```
  POST https://[search service name].search.windows.net/indexes/[index name]/analyze?api-version=[api-version]
  Content-Type: application/json
    api-key: [admin key]

  {
     "analyzer":"my_analyzer",
     "text": "Vis-à-vis means Opposite"
  }
```
**응답**
```
  {
    "tokens": [
      {
        "token": "vis_a_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "vis_à_vis",
        "startOffset": 0,
        "endOffset": 9,
        "position": 0
      },
      {
        "token": "means",
        "startOffset": 10,
        "endOffset": 15,
        "position": 1
      },
      {
        "token": "opposite",
        "startOffset": 16,
        "endOffset": 24,
        "position": 2
      }
    ]
  }
```

## <a name="update-custom-analyzers"></a>사용자 지정 분석기 업데이트

분석기, 토크나이저, 토큰 필터 또는 문자 필터는 일단 정의한 후에는 수정할 수 없습니다. 인덱스 업데이트 요청에서 `allowIndexDowntime` 플래그가 true로 설정된 경우에만 이러한 새 항목을 기존 인덱스에 추가할 수 있습니다.

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

이 작업 중에 인덱스가 몇 초 이상 오프라인 상태가 되면 인덱싱 및 쿼리 요청이 실패합니다. 인덱스의 성능 및 쓰기 가용성은 인덱스를 업데이트한 후 몇 분 동안, 인덱스가 아주 큰 경우에는 더 긴 시간 동안 제대로 작동하지 않을 수 있습니다. 그렇지만 이러한 효과는 일시적이며 결과적으로는 자체 해결됩니다.

 <a name="ReferenceIndexAttributes"></a>

## <a name="analyzer-reference"></a>분석기 참조

아래 표에는 인덱스 정의의 분석기, 토크나이저, 토큰 필터 및 문자 필터 섹션에 대한 구성 속성이 나와 있습니다. 인덱스의 분석기, 토크나이저 또는 필터의 구조는 이러한 특성으로 구성됩니다. 값 할당 정보에 대해서는 [속성 참조](#PropertyReference)를 참조하세요.

### <a name="analyzers"></a>분석기

분석기의 경우, 인덱스 특성은 미리 정의된 분석기를 사용하는지 또는 사용자 지정 분석기를 사용하는지에 따라 다릅니다.

#### <a name="predefined-analyzers"></a>미리 정의된 분석기

|||  
|-|-|  
|이름|문자, 숫자, 공백, 대시 또는 밑줄만 사용해야 하며 영숫자 문자로만 시작하고 끝낼 수 있습니다. 길이는 128자로 제한됩니다.|  
|Type|지원되는 분석기 목록에 포함된 분석기 형식입니다. 아래의 [분석기](#AnalyzerTable) 표에서 **analyzer_type** 열을 참조하세요.|  
|옵션|아래의 [분석기](#AnalyzerTable) 표에 나열된 미리 정의된 분석기의 유효한 옵션이어야 합니다.|  

#### <a name="custom-analyzers"></a>사용자 지정 분석기

|||  
|-|-|  
|이름|문자, 숫자, 공백, 대시 또는 밑줄만 사용해야 하며 영숫자 문자로만 시작하고 끝낼 수 있습니다. 길이는 128자로 제한됩니다.|  
|Type|"#Microsoft.Azure.Search.CustomAnalyzer"여야 합니다.|  
|CharFilters|[문자 필터](#CharFilter) 표에 나열된 미리 정의된 문자 필터 중 하나 또는 인덱스 정의에 지정된 사용자 지정 문자 필터로 설정합니다.|  
|토크나이저|필수 사항입니다. 아래의 [토크나이저](#Tokenizers) 표에 나열된 미리 정의된 토크나이저 중 하나 또는 인덱스 정의에 지정된 사용자 지정 토크나이저로 설정합니다.|  
|TokenFilters|[토큰 필터](#TokenFilters) 표에 나열된 미리 정의된 토큰 필터 중 하나 또는 인덱스 정의에 지정된 사용자 지정 토큰 필터로 설정합니다.|  

<a name="CharFilter"></a>

### <a name="char-filters"></a>문자 필터

 문자 필터는 토크나이저에서 처리되기 전의 입력 텍스트를 준비하는 데 사용됩니다. 예를 들어, 특정 문자 또는 기호를 대신할 수 있습니다. 하나의 사용자 지정 분석기에서 여러 토큰 필터를 사용할 수 있습니다. 토큰 필터는 나열된 순서로 실행됩니다.  

|||  
|-|-|  
|이름|문자, 숫자, 공백, 대시 또는 밑줄만 사용해야 하며 영숫자 문자로만 시작하고 끝낼 수 있습니다. 길이는 128자로 제한됩니다.|  
|Type|지원되는 문자 필터 목록의 문자 필터 형식입니다. [문자 필터](#CharFilter) 표의 **char_filter_type** 열을 참조하세요.|  
|옵션|지정된 [문자 필터](#CharFilter) 형식의 유효한 옵션이어야 합니다.|  

### <a name="tokenizers"></a>토크나이저

 문장을 단어로 분리하는 것처럼 토크나이저는 연속된 텍스트를 토큰의 시퀀스로 분리합니다.  

 사용자 지정 분석기당 하나의 토크나이저를 지정할 수 있습니다. 둘 이상의 토크나이저가 필요한 경우 여러 사용자 지정 분석기를 만들고 인덱스 스키마에서 필드 별로 할당할 수 있습니다.  
사용자 지정 분석기는 기본 또는 사용자 지정된 옵션 중 하나로 미리 정의된 토크나이저를 사용할 수 있습니다.  

|||  
|-|-|  
|이름|문자, 숫자, 공백, 대시 또는 밑줄만 사용해야 하며 영숫자 문자로만 시작하고 끝낼 수 있습니다. 길이는 128자로 제한됩니다.|  
|Type|지원되는 토크나이저 목록의 토크나이저 이름입니다. [토크나이저](#Tokenizers) 표의 **tokenizer_type** 열을 참조하세요.|  
|옵션|아래의 [토크나이저](#Tokenizers) 표에 나열된 지정된 토크나이저 형식의 유효한 옵션이어야 합니다.|  

### <a name="token-filters"></a>토큰 필터

 토큰 필터는 토크나이저에서 생성된 토큰을 필터링하거나 수정하는 데 사용됩니다. 예를 들어, 모든 문자를 소문자로 변환하는 소문자 필터를 지정할 수 있습니다.   
사용자 지정 분석기에서 여러 토큰 필터를 사용할 수 있습니다. 토큰 필터는 나열된 순서로 실행합니다.  

|||  
|-|-|  
|이름|문자, 숫자, 공백, 대시 또는 밑줄만 사용해야 하며 영숫자 문자로만 시작하고 끝낼 수 있습니다. 길이는 128자로 제한됩니다.|  
|Type|지원되는 토큰 필터 목록의 토큰 필터 이름입니다. [토큰 필터](#TokenFilters) 표의 **token_filter_type** 열을 참조하세요.|  
|옵션|지정된 토큰 필터 형식의 [토큰 필터](#TokenFilters)여야 합니다.|  

<a name="PropertyReference"></a>  

## <a name="property-reference"></a>속성 참조

이 섹션에서는 인덱스의 사용자 지정 분석기, 토크나이저, 문자 필터 또는 토큰 필터 정의에 지정된 특성의 유효한 값을 제공합니다. Apache Lucene을 사용하여 구현한 분석기, 토크나이저 및 필터에는 Lucene API 설명서에 대한 링크가 있습니다.

<a name="AnalyzerTable"></a>

###  <a name="predefined-analyzers-reference"></a>미리 정의된 분석기 참조

|**analyzer_name**|**analyzer_type**  <sup>1</sup>|**설명 및 옵션**|  
|-|-|-|  
|[keyword](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)| (형식은 옵션이 사용 가능할 때만 적용됨) |필드의 전체 콘텐츠를 단일 토큰으로 처리합니다. 우편 번호, ID 및 일부 제품 이름과 같은 데이터에 유용합니다.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)|PatternAnalyzer|정규식 패턴을 통해 텍스트를 용어로 유연하게 구분합니다.<br /><br /> **옵션**<br /><br /> lowercase (type: bool) - 용어를 소문자로 처리할지를 결정합니다. 기본값은 true입니다.<br /><br /> [pattern](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) (type: string) - 일치하는 토큰 구분 기호를 검색하기 위한 정규식 패턴입니다. 기본값은 \w+입니다.<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: string) - 정규식 플래그입니다. 기본값은 빈 문자열입니다. 허용되는 값은 다음과 같습니다. CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> stopwords (type: string array) - 중지 단어 목록입니다. 기본값은 빈 목록입니다.|  
|[간단한](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)|(형식은 옵션이 사용 가능할 때만 적용됨) |비문자에서 텍스트를 나눈 후 소문자로 변환합니다. |  
|[standard](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) <br />(standard.lucene라고도 함)|StandardAnalyzer|표준 토크나이저, 소문자 필터 및 중지 필터로 구성된 표준 Lucene 분석기입니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. 기본값은 255입니다. 최대 길이보다 긴 토큰은 분할됩니다. 사용할 수 있는 최대 토큰 길이는 300자입니다.<br /><br /> stopwords (type: string array) - 중지 단어 목록입니다. 기본값은 빈 목록입니다.|  
|standardasciifolding.lucene|(형식은 옵션이 사용 가능할 때만 적용됨) |ASCII 접기 필터가 있는 표준 분석기입니다. |  
|[stop](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)|StopAnalyzer|비문자에서 텍스트를 나누고, 소문자 및 중지 단어 토큰 필터를 적용합니다.<br /><br /> **옵션**<br /><br /> stopwords (type: string array) - 중지 단어 목록입니다. 기본값은 미리 정의된 영어 목록입니다. |  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)|(형식은 옵션이 사용 가능할 때만 적용됨) |공백 토크나이저를 사용하는 분석기입니다. 255자보다 긴 토큰은 분할됩니다.|  

 <sup>1</sup> 분석기 형식은 코드에서 항상 접두사로 "#Microsoft.Azure.Search"가 붙습니다. 따라서 "PatternAnalyzer"는 실제로 "#Microsoft.Azure.Search.PatternAnalyzer"로 지정됩니다. 간단히 나타내기 위해 접두사를 제거했지만 코드에서는 필요합니다. 
 
사용자 지정할 수 있는 분석기에만 analyzer_type이 제공됩니다. 키워드 분석기를 사용할 때처럼 옵션이 없는 경우 연결된 #Microsoft.Azure.Search 형식도 없습니다.


<a name="CharFilter"></a>

###  <a name="char-filters-reference"></a>문자 필터 참조

아래 표에서 Apache Lucene을 사용하여 구현한 문자 필터는 Lucene API 설명서로 연결됩니다.

|**char_filter_name**|**char_filter_type** <sup>1</sup>|**설명 및 옵션**|  
|-|-|-|
|[html_strip](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/HTMLStripCharFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |HTML 구문을 제거하려고 하는 문자 필터입니다.|  
|[mapping](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/charfilter/MappingCharFilter.html)|MappingCharFilter|매핑 옵션을 사용하여 정의된 매핑을 적용하는 문자 필터입니다. 일치는 greedy 방식을 따릅니다(지정된 지점에서 가장 긴 패턴 일치가 검색됨). 빈 문자열로 대체될 수 있습니다.<br /><br /> **옵션**<br /><br /> mappings (type: string array) - "a=>b" 형식의 매핑 목록입니다(문자 "a"가 나오는 모든 경우는 문자 "b"로 바뀜). 필수 사항입니다.|  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceCharFilter.html)|PatternReplaceCharFilter|입력 문자열에서 문자를 대체하는 문자 필터입니다. 정규식을 사용하여 보존할 문자 시퀀스를 식별하고, 대체 패턴을 사용하여 대체할 문자를 식별합니다. 예를 들어, 입력 텍스트 = "aa  bb aa bb", 패턴 ="(aa)\\\s+(bb)" 대체 ="$1#$2", 결과 = "aa#bb aa#bb"와 같습니다.<br /><br /> **옵션**<br /><br /> pattern (type: string) - 필수 항목입니다.<br /><br /> replacement (type: string) - 필수 항목입니다.|  

 <sup>1</sup> 문자 필터 형식은 코드에서 항상 접두사로 "#Microsoft.Azure.Search"가 붙습니다. 따라서 "MappingCharFilter"는 실제로 "#Microsoft.Azure.Search.MappingCharFilter"로 지정됩니다. 테이블 너비를 줄이기 위해 이 접두사를 제거했지만 코드에는 포함해야 합니다. 사용자 지정할 수 있는 필터에만 char_filter_type이 제공됩니다. html_strip을 사용할 때처럼 옵션이 없는 경우 연결된 #Microsoft.Azure.Search 형식도 없습니다.

<a name="Tokenizers"></a>

###  <a name="tokenizers-reference"></a>토크나이저 참조

아래 표에서 Apache Lucene을 사용하여 구현한 토크나이저는 Lucene API 설명서로 연결됩니다.

|**tokenizer_name**|**tokenizer_type** <sup>1</sup>|**설명 및 옵션**|  
|-|-|-|  
|[기존](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)|ClassicTokenizer|대부분의 유럽 언어 문서 처리에 적합한 문법 기반 토크나이저입니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. Default: 255, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다.|  
|[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)|EdgeNGramTokenizer|에지의 입력을 지정된 크기의 n-gram으로 토큰화합니다.<br /><br /> **옵션**<br /><br /> minGram (type: int) - 기본값: 1, 최대: 300.<br /><br /> maxGram(type: int) - 기본값: 2, 최대: 300. minGram보다 커야 합니다.<br /><br /> tokenChars (type: string array) - 토큰에 유지할 문자 클래스입니다. 허용되는 값은 다음과 같습니다. <br />"letter", "digit", "whitespace", "punctuation", "symbol". 기본값은 빈 배열로, 모든 문자를 유지합니다. |  
|[keyword_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)|KeywordTokenizerV2|전체 입력을 단일 토큰으로 내보냅니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. Default: 256, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다.|  
|[letter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |비문자에서 텍스트를 나눕니다. 255자보다 긴 토큰은 분할됩니다.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |비문자에서 텍스트를 나눈 후 소문자로 변환합니다. 255자보다 긴 토큰은 분할됩니다.|  
| microsoft_language_tokenizer| MicrosoftLanguageTokenizer| 언어별 규칙을 사용하여 텍스트를 구분합니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. 기본값: 255, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다. 300자보다 긴 토큰은 먼저 300자 길이의 토큰으로 분할된 후 각 토큰은 설정된 maxTokenLength를 기준으로 분할됩니다.<br /><br />isSearchTokenizer (type: bool) - 검색 토크나이저로 사용될 경우 true로 설정되고, 인덱싱 토크나이저로 사용될 경우 false로 설정됩니다. <br /><br /> language (type: string) - 사용할 언어로, 기본값은 "english"입니다. 허용되는 값은 다음과 같습니다.<br />"bangla", "bulgarian", "catalan", "chineseSimplified",  "chineseTraditional", "croatian", "czech", "danish", "dutch", "english",  "french", "german", "greek", "gujarati", "hindi", "icelandic", "indonesian", "italian", "japanese", "kannada", "korean", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovenian", "spanish", "swedish", "tamil", "telugu", "thai", "ukrainian", "urdu", "vietnamese" |
| microsoft_language_stemming_tokenizer | MicrosoftLanguageStemmingTokenizer| 언어별 규칙을 사용하여 텍스트를 구분하고, 단어를 기본 형식으로 줄입니다.<br /><br /> **옵션**<br /><br />maxTokenLength (type: int) - 최대 토큰 길이입니다. 기본값: 255, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다. 300자보다 긴 토큰은 먼저 300자 길이의 토큰으로 분할된 후 각 토큰은 설정된 maxTokenLength를 기준으로 분할됩니다.<br /><br /> isSearchTokenizer (type: bool) - 검색 토크나이저로 사용될 경우 true로 설정되고, 인덱싱 토크나이저로 사용될 경우 false로 설정됩니다.<br /><br /> language (type: string) - 사용할 언어로, 기본값은 "english"입니다. 허용되는 값은 다음과 같습니다.<br />"arabic", "bangla", "bulgarian", "catalan", "croatian", "czech", "danish", "dutch", "english", "estonian", "finnish", "french", "german", "greek", "gujarati", "hebrew", "hindi", "hungarian", "icelandic", "indonesian", "italian", "kannada", "latvian", "lithuanian", "malay", "malayalam", "marathi", "norwegianBokmaal", "polish", "portuguese", "portugueseBrazilian", "punjabi", "romanian", "russian", "serbianCyrillic", "serbianLatin", "slovak", "slovenian", "spanish", "swedish", "tamil", "telugu", "turkish", "ukrainian", "urdu" |
|[nGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)|NGramTokenizer|입력을 지정된 크기의 n-gram으로 토큰화합니다.<br /><br /> **옵션**<br /><br /> minGram (type: int) - 기본값: 1, 최대: 300.<br /><br /> maxGram(type: int) - 기본값: 2, 최대: 300. minGram보다 커야 합니다. <br /><br /> tokenChars (type: string array) - 토큰에 유지할 문자 클래스입니다. 허용되는 값: "letter", "digit", "whitespace", "punctuation", "symbol". 기본값은 빈 배열로, 모든 문자를 유지합니다. |  
|[path_hierarchy_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)|PathHierarchyTokenizerV2|경로와 같은 계층 구조의 토크나이저입니다.<br /><br /> **옵션**<br /><br /> delimiter (type: string) - 기본값: '/.<br /><br /> replacement (type: string) - 설정하면 구분 기호 문자를 바꿉니다. 기본값은 구분 기호 값과 동일합니다.<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. Default: 300, 최대: 300. maxTokenLength보다 긴 경로는 무시됩니다.<br /><br /> reverse (type: bool) - true인 경우 역순으로 토큰을 생성합니다. 기본값: false입니다.<br /><br /> skip (type: bool) - 건너뛸 초기 토큰입니다. 기본값은 0입니다.|  
|[pattern](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)|PatternTokenizer|이 토크나이저는 정규식 패턴 일치를 사용하여 고유 토큰을 생성합니다.<br /><br /> **옵션**<br /><br /> pattern (type: string) - 정규식 패턴입니다. 기본값은 \w+입니다.<br /><br /> [flags](https://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) (type: string) - 정규식 플래그입니다. 기본값은 빈 문자열입니다. 허용되는 값은 다음과 같습니다. CANON_EQ, CASE_INSENSITIVE, COMMENTS, DOTALL, LITERAL, MULTILINE, UNICODE_CASE, UNIX_LINES<br /><br /> group (type: int) - 토큰으로 추출할 그룹입니다. 기본값은 -1(분할)입니다.|  
|[standard_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)|StandardTokenizerV2|[유니코드 텍스트 구분 규칙](https://unicode.org/reports/tr29/)에 따라 텍스트를 구분합니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. Default: 255, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다.|  
|[uax_url_email](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)|UaxUrlEmailTokenizer|URL과 이메일을 하나의 토큰으로 토큰화합니다.<br /><br /> **옵션**<br /><br /> maxTokenLength (type: int) - 최대 토큰 길이입니다. Default: 255, 최대: 300. 최대 길이보다 긴 토큰은 분할됩니다.|  
|[whitespace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)|(형식은 옵션이 사용 가능할 때만 적용됨) |공백에서 텍스트를 나눕니다. 255자보다 긴 토큰은 분할됩니다.|  

 <sup>1</sup> 토크나이저 형식은 코드에서 항상 접두사로 "#Microsoft.Azure.Search"가 붙습니다. 따라서 "ClassicTokenizer"는 실제로 "#Microsoft.Azure.Search.ClassicTokenizer"로 지정됩니다. 테이블 너비를 줄이기 위해 이 접두사를 제거했지만 코드에는 포함해야 합니다. 사용자 지정할 수 있는 토크나이저에만 tokenizer_type이 제공됩니다. 문자 토크나이저를 사용할 때처럼 옵션이 없는 경우 연결된 #Microsoft.Azure.Search 형식도 없습니다.

<a name="TokenFilters"></a>

###  <a name="token-filters-reference"></a>토큰 필터 참조

아래 표에서 Apache Lucene을 사용하여 구현한 토큰 필터는 Lucene API 설명서로 연결됩니다.

|**token_filter_name**|**token_filter_type** <sup>1</sup>|**설명 및 옵션**|  
|-|-|-|  
|[arabic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |아랍어 노멀라이저를 적용하여 표기법을 정규화하는 토큰 필터입니다.|  
|[apostrophe](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |아포스트로피 다음의 모든 문자를 제거합니다(아포스트로피 자체도 포함). |  
|[asciifolding](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)|AsciiFoldingTokenFilter|처음 127개의 ASCII 문자("기본 라틴" 유니코드 블록)에 포함되지 않는 영문자, 숫자 및 기호 유니코드 문자를 해당 ASCII 문자로 변환합니다(있는 경우).<br /><br /> **옵션**<br /><br /> preserveOriginal (type: bool) - true인 경우 원래 토큰이 유지됩니다. 기본값은 false입니다.|  
|[cjk_bigram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)|CjkBigramTokenFilter|StandardTokenizer에서 생성되는 CJK 용어의 바이그램을 형성합니다.<br /><br /> **옵션**<br /><br /> ignoreScripts (type: string array) - 무시할 스크립트입니다. 사용할 수 있는 값은 "han", "hiragana", "katakana", "hangul"입니다. 기본값은 빈 목록입니다.<br /><br /> outputUnigrams (type: bool) - 항상 유니그램과 바이그램 둘 다를 출력하려는 경우 true로 설정합니다. 기본값은 false입니다.|  
|[cjk_width](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |CJK 너비 차이를 정규화합니다. 전자 ASCII 변형을 동급의 기본 라틴으로 접고 반자 가타카나 변형을 동급의 가나로 접습니다. |  
|[기존](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |머리 글자어에서 영어 소유격과 점을 제거합니다. |  
|[common_grams](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)|CommonGramTokenFilter|인덱싱 동안 자주 발생하는 용어에 대한 바이그램을 생성합니다. 단일 단어도 바이그램이 오버레이된 상태로 계속 인덱싱됩니다.<br /><br /> **옵션**<br /><br /> commonWords (type: string array) - 일반적인 단어 세트입니다. 기본값은 빈 목록입니다. 필수 사항입니다.<br /><br /> ignoreCase (type: bool) - true인 경우 대/소문자를 구분하지 않고 검색합니다. 기본값은 false입니다.<br /><br /> queryMode (type: bool) - 바이그램을 생성한 후 일반 단어와 그 뒤에 나오는 단일 단어를 제거합니다. 기본값은 false입니다.|  
|[dictionary_decompounder](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)|DictionaryDecompounderTokenFilter|많은 게르만어파에 있는 복합 단어를 분해합니다.<br /><br /> **옵션**<br /><br /> wordList (type: string array) - 일치하는지 검색할 단어의 목록입니다. 기본값은 빈 목록입니다. 필수 사항입니다.<br /><br /> minWordSize (type: int) - 이보다 긴 단어만 처리합니다. 기본값은 5입니다.<br /><br /> minSubwordSize (type: int) - 이보다 긴 부분 단어만 출력합니다. 기본값은 2입니다.<br /><br /> maxSubwordSize (type: int) - 이보다 짧은 부분 단어만 출력합니다. 기본값은 15입니다.<br /><br /> onlyLongestMatch (type: bool) - 가장 긴 일치 부분 단어만 출력에 추가합니다. 기본값은 false입니다.|  
|[edgeNGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)|EdgeNGramTokenFilterV2|입력 토큰의 맨 앞 또는 맨 뒤에서 시작해서 지정된 크기의 n-gram을 생성합니다.<br /><br /> **옵션**<br /><br /> minGram (type: int) - 기본값: 1, 최대: 300.<br /><br /> maxGram(type: int) - 기본값: 2, 최대는 300입니다. minGram보다 커야 합니다.<br /><br /> side (type: string) - n-gram 생성을 시작할 입력의 부분을 지정합니다. 사용할 수 있는 값은 "front", "back"입니다. |  
|[elision](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)|ElisionTokenFilter|생략 발음을 제거합니다. 예를 들어, "l'avion"(그 비행기)은 "avion"(비행기)으로 변환됩니다.<br /><br /> **옵션**<br /><br /> articles (type: string array) - 제거할 문서 세트입니다. 기본값은 빈 목록입니다. 문서 세트 목록이 없으면 기본적으로 모든 프랑스어 문서가 제거됩니다.|  
|[german_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |[German2 스노우볼 알고리즘](https://snowballstem.org/algorithms/german2/stemmer.html)의 추론에 따라 독일어 문자를 정규화합니다.|  
|[hindi_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |힌디어의 텍스트를 정규화하여 철자 변형의 차이를 제거합니다. |  
|[indic_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)|IndicNormalizationTokenFilter|인도어 텍스트의 유니코드 표현을 정규화합니다.
|[keep](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)|KeepTokenFilter|지정된 단어 목록에 포함된 텍스트가 있는 토큰만 유지하는 토큰 필터입니다.<br /><br /> **옵션**<br /><br /> keepWords (type: string array) - 유지할 단어 목록입니다. 기본값은 빈 목록입니다. 필수 사항입니다.<br /><br /> keepWordsCase (type: bool) - true이면 먼저 모든 단어를 소문자로 지정합니다. 기본값은 false입니다.|  
|[keyword_marker](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)|KeywordMarkerTokenFilter|용어를 키워드로 표시합니다.<br /><br /> **옵션**<br /><br /> keywords (type: string array) - 키워드로 표시할 단어 목록입니다. 기본값은 빈 목록입니다. 필수 사항입니다.<br /><br /> ignoreCase (type: bool) - true이면 먼저 모든 단어를 소문자로 지정합니다. 기본값은 false입니다.|  
|[keyword_repeat](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |들어오는 각 토큰을 한 번은 키워드로, 다른 한 번은 비키워드로 내보냅니다. |  
|[kstem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |영어에 대한 고성능 kstem 필터입니다. |  
|[length](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)|LengthTokenFilter|너무 길거나 너무 짧은 단어를 제거합니다.<br /><br /> **옵션**<br /><br /> min (type: int) - 최소 수입니다. Default: 0, 최대: 300.<br /><br /> max (type: int) - 최대 수입니다. Default: 300, 최대: 300.|  
|[limit](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)|Microsoft.Azure.Search.LimitTokenFilter|인덱싱을 할 때 토큰 수를 제한합니다.<br /><br /> **옵션**<br /><br /> maxTokenCount (type: int) - 생성할 최대 토큰 수입니다. 기본값은 1입니다.<br /><br /> consumeAllTokens (type: bool) - maxTokenCount에 도달한 경우에도 입력의 모든 토큰을 사용해야 하는지 여부입니다. 기본값은 false입니다.|  
|[lowercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |토큰 텍스트를 소문자로 정규화합니다. |  
|[nGram_v2](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)|NGramTokenFilterV2|지정된 크기의 n-gram을 생성합니다.<br /><br /> **옵션**<br /><br /> minGram (type: int) - 기본값: 1, 최대: 300.<br /><br /> maxGram(type: int) - 기본값: 2, 최대는 300입니다. minGram보다 커야 합니다.|  
|[pattern_capture](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)|PatternCaptureTokenFilter|Java 정규식을 사용하여 여러 토큰을 내보냅니다. 하나 이상의 패턴을 갖는 캡처 그룹마다 하나씩 전달합니다.<br /><br /> **옵션**<br /><br /> patterns (type: string array) - 각 토큰에 대해 일치하는지 검색할 패턴 목록입니다. 필수 사항입니다.<br /><br /> preserveOriginal (type: bool) - 패턴 중 하나가 일치하더라도 원래 토큰을 반환하려면 true로 설정합니다. 기본값은 true입니다. |  
|[pattern_replace](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternReplaceFilter.html)|PatternReplaceTokenFilter|패턴을 스트림의 각 토큰에 적용하고 일치하는 항목을 지정된 대체 문자열로 바꾸는 토큰 필터입니다.<br /><br /> **옵션**<br /><br /> pattern (type: string) - 필수 항목입니다.<br /><br /> replacement (type: string) - 필수 항목입니다.|  
|[persian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨) |페르시아어에 정규화를 적용합니다. |  
|[phonetic](https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)|PhoneticTokenFilter|음성 일치 항목에 대한 토큰을 만듭니다.<br /><br /> **옵션**<br /><br /> encoder (type: string) - 사용할 음성 인코더입니다. 사용할 수 있는 값은 "metaphone", "doubleMetaphone", "soundex", "refinedSoundex", "caverphone1", "caverphone2", "cologne", "nysiis", "koelnerPhonetik", "haasePhonetik", "beiderMorse"입니다. 기본값: "metaphone". 기본값은 metaphone입니다.<br /><br /> 자세한 내용은 [encoder](https://commons.apache.org/proper/commons-codec/archives/1.10/apidocs/org/apache/commons/codec/language/package-frame.html)를 참조하세요.<br /><br /> replace (type: bool) - 인코딩한 토큰이 원래 토큰을 대체해야 하면 true이고, 동의어로 추가되어야 하면 false입니다. 기본값은 true입니다.|  
|[porter_stem](https://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |[Porter 형태소 분석 알고리즘](https://tartarus.org/~martin/PorterStemmer/)에 따라 토큰 스트림을 변환합니다. |  
|[reverse](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |토큰 문자열을 반대로 바꿉니다. |  
|[scandinavian_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |교환 가능한 스칸디나비아어 문자의 사용을 정규화합니다. |  
|[scandinavian_folding](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |스칸디나비아어 문자 åÅäæÄÆ를 a로, öÖøØ를 o로 접습니다. 또한 이중 모음 aa, ae, ao, oe 및 oo의 사용과 구분하고 첫 번째 모음만 남겨둡니다. |  
|[shingle](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)|ShingleTokenFilter|토큰의 조합을 단일 토큰으로 만듭니다.<br /><br /> **옵션**<br /><br /> maxShingleSize (type: int) - 기본값은 2입니다.<br /><br /> minShingleSize (type: int) - 기본값은 2입니다.<br /><br /> outputUnigrams (type: bool) - true인 경우 출력 스트림은 shingle 뿐만 아니라 입력 토큰(유니그램)을 포함합니다. 기본값은 true입니다.<br /><br /> outputUnigramsIfNoShingles (type: bool) - true이면 사용할 수 있는 shingle이 없는 경우 outputUnigrams==false의 동작을 재정의합니다. 기본값은 false입니다.<br /><br /> tokenSeparator (type: string) - 인접한 토큰을 조인하여 shingle을 만들 때 사용할 문자열입니다. 기본값은 " "입니다.<br /><br /> filterToken (type: string) - 토큰이 없는 각 위치에 삽입할 문자열입니다. 기본값은 "_"입니다.|  
|[snowball](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)|SnowballTokenFilter|스노우볼 토큰 필터입니다.<br /><br /> **옵션**<br /><br /> language (type: string) - 사용할 수 있는 값은 "armenian", "basque", "catalan", "danish", "dutch", "english", "finnish", "french", "german", "german2", "hungarian", "italian", "kp", "lovins", "norwegian", "porter", "portuguese", "romanian", "russian", "spanish", "swedish", "turkish"입니다.|  
|[sorani_normalization](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)|SoraniNormalizationTokenFilter|소라니어 텍스트의 유니코드 표현을 정규화합니다.<br /><br /> **옵션**<br /><br /> 없음.|  
|stemmer|StemmerTokenFilter|언어별 형태소 분석 필터입니다.<br /><br /> **옵션**<br /><br /> language (type: string) - 다음 값을 사용할 수 있습니다. <br /> -   ["arabic"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html)<br />-   ["armenian"](https://snowballstem.org/algorithms/armenian/stemmer.html)<br />-   ["basque"](https://snowballstem.org/algorithms/basque/stemmer.html)<br />-   ["brazilian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html)<br />-   "bulgarian"<br />-   ["catalan"](https://snowballstem.org/algorithms/catalan/stemmer.html)<br />-   ["czech"](https://portal.acm.org/citation.cfm?id=1598600)<br />-   ["danish"](https://snowballstem.org/algorithms/danish/stemmer.html)<br />-   ["dutch"](https://snowballstem.org/algorithms/dutch/stemmer.html)<br />-   ["dutchKp"](https://snowballstem.org/algorithms/kraaij_pohlmann/stemmer.html)<br />-   ["english"](https://snowballstem.org/algorithms/porter/stemmer.html)<br />-   ["lightEnglish"](https://ciir.cs.umass.edu/pubfiles/ir-35.pdf)<br />-   ["minimalEnglish"](https://www.researchgate.net/publication/220433848_How_effective_is_suffixing)<br />-   ["possessiveEnglish"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html)<br />-   ["porter2"](https://snowballstem.org/algorithms/english/stemmer.html)<br />-   ["lovins"](https://snowballstem.org/algorithms/lovins/stemmer.html)<br />-   ["finnish"](https://snowballstem.org/algorithms/finnish/stemmer.html)<br />-   "lightFinnish"<br />-   ["french"](https://snowballstem.org/algorithms/french/stemmer.html)<br />-   ["lightFrench"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   ["minimalFrench"](https://dl.acm.org/citation.cfm?id=318984)<br />-   "galician"<br />-   "minimalGalician"<br />-   ["german"](https://snowballstem.org/algorithms/german/stemmer.html)<br />-   ["german2"](https://snowballstem.org/algorithms/german2/stemmer.html)<br />-   ["lightGerman"](https://dl.acm.org/citation.cfm?id=1141523)<br />-   "minimalGerman"<br />-   ["greek"](https://sais.se/mthprize/2007/ntais2007.pdf)<br />-   "hindi"<br />-   ["hungarian"](https://snowballstem.org/algorithms/hungarian/stemmer.html)<br />-   ["lightHungarian"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["indonesian"](https://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf)<br />-   ["irish"](https://snowballstem.org/otherapps/oregan/)<br />-   ["italian"](https://snowballstem.org/algorithms/italian/stemmer.html)<br />-   ["lightItalian"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["sorani"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html)<br />-   ["latvian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html)<br />-   ["norwegian"](https://snowballstem.org/algorithms/norwegian/stemmer.html)<br />-   ["lightNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNorwegian"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["lightNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html)<br />-   ["minimalNynorsk"](https://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html)<br />-   ["portuguese"](https://snowballstem.org/algorithms/portuguese/stemmer.html)<br />-   ["lightPortuguese"](https://dl.acm.org/citation.cfm?id=1141523&dl=ACM&coll=DL&CFID=179095584&CFTOKEN=80067181)<br />-   ["minimalPortuguese"](https://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf)<br />-   ["portugueseRslp"](https://www.inf.ufrgs.br//~viviane/rslp/index.htm)<br />-   ["romanian"](https://snowballstem.org/otherapps/romanian/)<br />-   ["russian"](https://snowballstem.org/algorithms/russian/stemmer.html)<br />-   ["lightRussian"](https://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf)<br />-   ["spanish"](https://snowballstem.org/algorithms/spanish/stemmer.html)<br />-   ["lightSpanish"](https://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf)<br />-   ["swedish"](https://snowballstem.org/algorithms/swedish/stemmer.html)<br />-   "lightSwedish"<br />-   ["turkish"](https://snowballstem.org/algorithms/turkish/stemmer.html)|  
|[stemmer_override](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)|StemmerOverrideTokenFilter|모든 사전 형태소 용어를 키워드로 표시하여 체인이 형태소로 분석되지 않도록 합니다. 형태소 분석 필터 전에 배치해야 합니다.<br /><br /> **옵션**<br /><br /> rules (type: string array) - "word => stem" 형식의 형태소 분석 규칙입니다(예: "ran => run"). 기본값은 빈 목록입니다.  필수 사항입니다.|  
|[stopwords](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)|StopwordsTokenFilter|토큰 스트림에서 중지 단어를 제거합니다. 기본적으로 필터는 영어의 경우 미리 정의된 중지 단어 목록을 사용합니다.<br /><br /> **옵션**<br /><br /> stopwords (type: string array) - 중지 단어 목록입니다. stopwordsList를 지정한 경우에는 지정할 수 없습니다.<br /><br /> stopwordsList (type: string) - 미리 정의한 중지 단어 목록입니다. stopwords를 지정한 경우에는 지정할 수 없습니다. 사용할 수 있는 값은 "arabic", "armenian", "basque", "brazilian", "bulgarian", "catalan", "czech", "danish", "dutch", "english", "finnish", "french", "galician", "german", "greek", "hindi", "hungarian", "indonesian", "irish", "italian", "latvian", "norwegian", "persian", "portuguese", "romanian", "russian", "sorani", "spanish", "swedish", "thai", "turkish", default: "english"입니다. stopwords를 지정한 경우에는 지정할 수 없습니다. <br /><br /> ignoreCase (type: bool) - true이면 모든 단어를 먼저 소문자로 나타냅니다. 기본값은 false입니다.<br /><br /> removeTrailing (type: bool) - true이면 마지막 검색 단어가 중지 단어인 경우 무시합니다. 기본값은 true입니다.
|[synonym](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)|SynonymTokenFilter|토큰 스트림에서 일치하는 단일 또는 다중 단어 동의어를 검색합니다.<br /><br /> **옵션**<br /><br /> synonyms (type: string array) - 필수 항목입니다. 다음 두 가지 형식 중 하나를 갖는 동의어 목록입니다.<br /><br /> -incredible, unbelievable, fabulous => amazing - => 기호 왼쪽의 모든 용어는 오른쪽의 모든 용어로 바뀝니다.<br /><br /> -incredible, unbelievable, fabulous, amazing - 쉼표로 구분한 동일 단어 목록입니다. 이 목록을 해석하는 방식을 변경하려면 확장 옵션을 설정합니다.<br /><br /> ignoreCase (type: bool) - 일치하는 경우 입력의 대/소문자를 접습니다. 기본값은 false입니다.<br /><br /> expand (type: bool) - true이면 동의어 목록의 모든 단어(=> 표기법을 사용하지 않을 경우)가 서로 매핑합니다. <br />incredible, unbelievable, fabulous, amazing 목록은 incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing과 같습니다.<br /><br />- false이면 incredible, unbelievable, fabulous, amazing 목록은 incredible, unbelievable, fabulous, amazing => incredible과 같습니다.|  
|[trim](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |토큰에서 선행 및 후행 공백을 제거합니다. |  
|[truncate](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)|TruncateTokenFilter|용어를 특정 길이로 자릅니다.<br /><br /> **옵션**<br /><br /> length (type: int) - 기본값: 300, 최대: 300. 필수 사항입니다.|  
|[unique](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)|UniqueTokenFilter|이전 토큰과 동일한 텍스트를 포함하는 토큰을 필터링합니다.<br /><br /> **옵션**<br /><br /> onlyOnSamePosition (type: bool) - 설정하면 같은 위치에서만 중복 항목을 제거합니다. 기본값은 true입니다.|  
|[uppercase](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)|(형식은 옵션이 사용 가능할 때만 적용됨)  |토큰 텍스트를 대문자로 정규화합니다. |  
|[word_delimiter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)|WordDelimiterTokenFilter|단어를 부분 단어로 분할하고 부분 단어 그룹에 대해 선택적 변환을 수행합니다.<br /><br /> **옵션**<br /><br /> generateWordParts (type: bool) - 단어 부분을 생성합니다. 예를 들어 "AzureSearch"는 "Azure" "Search"가 됩니다. 기본값은 true입니다.<br /><br /> generateNumberParts (type: bool) - 숫자 하위 부분을 생성합니다. 기본값은 true입니다.<br /><br /> catenateWords (type: bool) - 단어 부분의 최대 실행을 연결합니다. 예를 들어 "Azure-Search"는 "AzureSearch"가 됩니다. 기본값은 false입니다.<br /><br /> catenateNumbers (type: bool) - 숫자 부분의 최대 실행을 연결합니다. 예를 들어 “1-2”는 “12”가 됩니다. 기본값은 false입니다.<br /><br /> catenateAll (type: bool) - 모든 단어 부분을 연결합니다. 예를 들어 "Azure-Search-1"은 "AzureSearch1"이 됩니다. 기본값은 false입니다.<br /><br /> splitOnCaseChange (type: bool) - true인 경우 caseChange에서 단어를 분할합니다. 예를 들어 "AzureSearch"는 "Azure" "Search"가 됩니다. 기본값은 true입니다.<br /><br /> preserveOriginal - 원래 단어를 유지하고 부분 단어 목록에 추가합니다. 기본값은 false입니다.<br /><br /> splitOnNumerics (type: bool) - true이면 숫자에서 분할합니다. 예를 들어 "Azure1Search"는 "Azure" "1" "Search"가 됩니다. 기본값은 true입니다.<br /><br /> stemEnglishPossessive (type: bool) - 각 부분 단어에서 후행 "s"를 제거합니다. 기본값은 true입니다.<br /><br /> protectedWords (type: string array) - 구분되지 않도록 보호할 토큰입니다. 기본값은 빈 목록입니다.|  

 <sup>1</sup> 토큰 필터 형식은 코드에서 항상 접두사로 "#Microsoft.Azure.Search"가 붙습니다. 따라서 "ArabicNormalizationTokenFilter"는 실제로 "#Microsoft.Azure.Search.ArabicNormalizationTokenFilter"로 지정됩니다.  테이블 너비를 줄이기 위해 이 접두사를 제거했지만 코드에는 포함해야 합니다.  

> [!NOTE]
> 사용자 지정 분석기가 300자보다 긴 토큰을 생성하지 않도록 구성해야 합니다. 이러한 토큰이 있는 문서는 인덱싱이 실패합니다. 자르거나 무시하려면 **TruncateTokenFilter** 및 **LengthTokenFilter**를 각각 사용합니다.


## <a name="see-also"></a>참고 항목  
 [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice/)   
 [Azure Search의 분석기 > 예제](search-analyzers.md#examples)    
 [인덱스 만들기 &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  
