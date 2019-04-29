---
title: 언어 및 텍스트 처리용 분석기 - Azure Search
description: 인덱스의 검색 가능한 텍스트 필드에 분석기를 할당하여 사용자 지정, 미리 정의 또는 언어 특정 대안으로 기본 표준 Lucene을 바꿉니다.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: heidist
manager: cgronlun
author: HeidiSteen
ms.custom: seodec2018
ms.openlocfilehash: e3738980206277587ca367339d75da4f3faa643a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61316832"
---
# <a name="analyzers-for-text-processing-in-azure-search"></a>Azure Search의 텍스트 처리용 분석기

*분석기*는 쿼리 문자열과 인덱싱된 문서의 텍스트를 처리하는 [전체 텍스트 검색 엔진](search-lucene-query-architecture.md)의 구성 요소입니다. 시나리오에 따라 여러 분석기가 다양한 방법으로 텍스트를 조작합니다. 언어 분석기는 검색 품질을 개선하기 위해 언어 규칙을 사용하여 텍스트를 처리하는 반면, 다른 분석기는 문자를 소문자로 변환하는 작업처럼 기본적인 작업을 수행합니다. 

언어 분석기는 가장 자주 사용되며, Azure Search 인덱스의 모든 검색 가능한 필드에 할당되는 기본 언어 분석기가 있습니다. 다음은 텍스트 분석 중에 일반적으로 수행되는 언어 변환입니다.

+ 필수적이지 않은 단어(중지 단어) 및 문장 부호가 제거됩니다.
+ 구 및 하이픈을 넣은 단어는 구성 요소 부분으로 분류됩니다.
+ 대문자 단어는 소문자가 됩니다.
+ 단어는 시제에 관계 없이 일치를 찾을 수 있도록 루트 양식으로 세분화됩니다.

언어 분석기는 입력된 텍스트를 정보 저장 및 검색 시 효율적인 원시 형태 또는 루트 형태로 변환합니다. 변환은 인덱싱 중 인덱스가 빌드될 때, 그리고 검색 중 인덱스 읽기가 수행될 때 발생합니다. 두 작업에서 동일한 분석기를 사용하면 예상되는 검색 결과를 얻을 확률이 큽니다.

## <a name="default-analyzer"></a>기본 분석기  

Azure Search는 [Apache Lucene 표준 분석기(표준 Lucene)](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)를 기본값으로 사용하며, ["유니코드 텍스트 구분"](https://unicode.org/reports/tr29/) 규칙에 따라 텍스트를 요소로 분리합니다. 또한 표준 분석기에서는 모든 문자를 소문자 형식으로 변환합니다. 인덱싱 및 쿼리 처리 중에는 인덱싱된 문서와 검색 용어 둘 다에 대해 분석을 수행합니다.  

모든 검색 가능 필드에 자동으로 사용됩니다. 필드별로 기본 분석기를 재정의할 수 있습니다. [언어 분석기](index-add-language-analyzers.md), [사용자 지정 분석기](index-add-custom-analyzers.md) 또는 [사용 가능한 분석기 목록](index-add-custom-analyzers.md#AnalyzerTable)의 미리 정의된 분석기를 대신 사용할 수 있습니다.


## <a name="types-of-analyzers"></a>분석기 유형

다음 목록에서는 Azure Search에서 사용 가능한 분석기를 설명합니다.

| Category | 설명 |
|----------|-------------|
| [표준 Lucene 분석기](https://lucene.apache.org/core/4_0_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) | 기본값 사양 또는 구성이 필요하지 않습니다. 이 범용 분석기는 대부분의 언어와 시나리오에서 잘 작동합니다.|
| 미리 정의된 분석기 | 있는 그대로 사용되는 완제품으로 제공됩니다. <br/>특수 및 언어와 같은 두 가지 형식이 있습니다. "미리 정의된"이라는 수식어가 붙은 이유는 구성 또는 사용자 지정 없이 이름으로 참조하기 때문입니다. <br/><br/>[특수(언어 중립적) 분석기](index-add-custom-analyzers.md#AnalyzerTable)는 텍스트 입력에 특수 처리 또는 최소한의 처리가 필요할 때 사용됩니다. 미리 정의된 비언어 분석기는 **Asciifolding**, **키워드**, **패턴**, **단순**, **중지**, **공백**을 포함합니다.<br/><br/>[언어 분석기](index-add-language-analyzers.md)는 개별 언어에 대해 풍부한 언어 지원이 필요할 때 사용됩니다. Azure Search는 35개의 Lucene 언어 분석기 및 50개의 Microsoft 자연어 처리 분석기를 지원합니다. |
|[사용자 지정 분석기](https://docs.microsoft.com/rest/api/searchservice/Custom-analyzers-in-Azure-Search) | 기존 요소를 결합한 사용자 정의 구성을 말하며, 토크나이저(필수) 하나와 여러 선택적 필터(char 또는 토큰)로 구성됩니다.|

**패턴** 또는 **중지** 같은 일부 미리 정의된 분석기는 제한된 구성 옵션 세트를 지원합니다. 이러한 옵션을 설정하려면 미리 정의된 분석기와 [미리 정의된 분석기 참조](index-add-custom-analyzers.md#AnalyzerTable)에 설명된 대체 옵션 중 하나로 구성되는 사용자 지정 분석기를 실질적으로 만들어야 합니다. 다른 사용자 지정 구성과 마찬가지로, Lucene 패턴 분석기와 구분할 수 있도록 구성의 이름(예: *myPatternAnalyzer*)을 입력합니다.

## <a name="how-to-specify-analyzers"></a>분석기를 지정하는 방법

1. (사용자 지정 분석기만 해당) 인덱스 정의에 **analyzer**라는 섹션을 만듭니다. 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 및 [사용자 지정 분석기 추가](index-add-custom-analyzers.md)를 참조하세요.

2. 인덱스의 [필드 정의](https://docs.microsoft.com/rest/api/searchservice/create-index)에서, 필드의 **analyzer** 속성을 대상 분석기의 이름으로 설정합니다(예: `"analyzer" = "keyword"`). 유효한 값에는 미리 정의된 분석기, 언어 분석기 또는 인덱스 스키마에 정의된 사용자 지정 분석기의 이름이 포함됩니다. 서비스에 인덱스를 만들기 전에, 인덱스 정의 단계에서 분석기 할당 계획을 수립합니다.

3. 필요에 따라 한 **analyzer** 속성 대신 **indexAnalyzer** 및 **searchAnalyzer** 필드 매개 변수를 사용하여 인덱싱 및 쿼리용으로 다른 분석기를 설정할 수 있습니다. 이러한 작업 중 하나에서 다른 작업에는 필요 없는 특정 변환이 필요한 경우 데이터 준비 및 검색에 다른 분석기를 사용할 것입니다.

이미 물리적으로 생성된 필드에 **analyzer** 또는 **indexAnalyzer**를 할당하는 것은 허용되지 않습니다. 이해 되지 않는 부분이 있으면 다시 작성이 필요한 작업과 그 이유를 분류해 놓은 다음 표를 검토하세요.
 
 | 시나리오 | 영향 | 단계 |
 |----------|--------|-------|
 | 새 필드 추가 | 최소 | 스키마에 아직 필드가 존재하지 않으면 인덱스에 실제로 존재하는 필드가 없는 것이므로 변경할 필드가 없습니다. [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)를 사용하여 기존 인덱스에 새 필드를 추가하고, [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 채웁니다.|
 | 기존의 인덱싱된 필드에 **analyzer** 또는 **indexAnalyzer**를 추가합니다. | [다시 작성](search-howto-reindex.md) | 해당 필드에 대해 반전된 인덱스를 처음부터 다시 만들고 이 필드에 대한 콘텐츠를 다시 인덱싱해야 합니다. <br/> <br/>개발 중인 인덱스의 경우 인덱스를 [삭제하고](https://docs.microsoft.com/rest/api/searchservice/delete-index) [만들어](https://docs.microsoft.com/rest/api/searchservice/create-index) 새 필드 정의를 선택합니다. <br/> <br/>프로덕션 환경의 인덱스인 경우 수정된 정의를 제공하는 새 필드를 만들어서 기존 필드 대신 사용하면 다시 작성을 미룰 수 있습니다. [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)를 사용하여 새 필드를 통합하고 [mergeOrUpload](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 사용하여 새 필드를 채웁니다. 나중에 계획된 인덱스 서비스의 일환으로 인덱스를 정리하여 오래된 필드를 제거할 수 있습니다. |

## <a name="when-to-add-analyzers"></a>분석기를 추가하는 시기

분석기를 추가하고 할당하기에 가장 좋은 시기는 인덱스를 삭제하고 다시 만드는 작업이 일상적으로 진행되는 능동적 개발 단계입니다.

인덱스 정의가 완성될수록 인덱스에 새 분석 구조를 추가할 수 있지만, **allowIndexDowntime** 플래그를 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)에 전달해야만 다음 오류를 방지할 수 있습니다.

*"가동 중지 시간이 발생하므로 인덱스 업데이트가 허용되지 않습니다. 기존 인덱스에 새 분석기, 토크나이저, 토큰 필터 또는 문자 필터를 추가하려면 인덱스 업데이트 요청에서 'allowIndexDowntime' 쿼리 매개 변수를 'true'로 설정하세요. 이 작업 중에 인덱스가 몇 초 이상 오프라인 상태가 되면 인덱싱 및 쿼리 요청이 실패합니다. 인덱스의 성능 및 쓰기 가용성은 인덱스를 업데이트한 후 몇 분 동안, 인덱스가 아주 큰 경우에는 더 긴 시간 동안 제대로 작동하지 않을 수 있습니다."*

분석기를 필드에 할당할 때에도 마찬가지입니다. 분석기는 필드 정의의 필수적인 부분이므로 필드를 만들 때에만 추가할 수 있습니다. 기존 필드에 분석기를 추가하려면 인덱스를 [삭제하고 다시 작성](search-howto-reindex.md)하거나 원하는 분석기를 사용하여 새 필드를 추가해야 합니다.

앞서 언급했듯이, **searchAnalyzer** 변형은 예외입니다. 분석기를 지정하는 세 가지 방법(**analyzer**, **indexAnalyzer**, **searchAnalyzer**) 중 **searchAnalyzer** 특성만 기존 필드에서 변경할 수 있습니다.

## <a name="recommendations-for-working-with-analyzers"></a>분석기 사용에 대한 추천

이 섹션에서는 분석기 작동 방식에 대한 조언을 제공합니다.

### <a name="one-analyzer-for-read-write-unless-you-have-specific-requirements"></a>특정 요구 사항이 없는 경우 읽기 쓰기에 대해 하나의 분석기

Azure Search는 추가적인 **indexAnalyzer** 및 **searchAnalyzer** 필드 매개 변수를 통해 인덱싱 및 검색에 서로 다른 분석기를 지정할 수 있습니다. 분석기를 지정하지 않으면 **analyzer** 속성을 통해 설정된 분석기가 인덱싱과 검색에 모두 사용됩니다. `analyzer`를 지정하지 않으면 기본 표준 Lucene 분석기가 사용됩니다.

특정 요구 사항이 따로 지정되지 않으면 인덱싱 및 쿼리 모두에 대해 동일한 분석기를 사용하는 것이 일반적인 규칙입니다. 철저하게 테스트해야 합니다. 검색 및 인덱싱 시간에서 텍스트 처리가 다른 경우 검색 및 인덱싱 분석기 구성이 일치하지 않으면 쿼리 용어와 인덱싱된 용어 사이에 불일치가 발생할 위험을 떠안게 됩니다.

### <a name="test-during-active-development"></a>활성 개발 중 테스트

표준 분석기를 재정의하려면 인덱스를 다시 빌드해야 합니다. 가능하면 프로덕션 환경에 인덱스를 이동하기 전에 활성 개발 중 사용할 분석기를 결정합니다.

### <a name="inspect-tokenized-terms"></a>토큰화된 용어 검사

검색에서 예상되는 결과가 반환되지 않은 경우, 쿼리에 입력된 용어와 인덱스의 토큰화된 용어의 토큰이 같지 않기 때문일 가능성이 큽니다. 토큰이 같지 않으면 일치하는 결과가 반환되지 않습니다. 토크나이저 출력을 검사할 때는 조사 도구로 [분석 API](https://docs.microsoft.com/rest/api/searchservice/test-analyzer)를 사용하는 것이 좋습니다. 응답은 해당 분석기에 의해 생성되는 토큰으로 구성됩니다.

### <a name="compare-english-analyzers"></a>영어 분석기 비교

[검색 분석기 데모](https://alice.unearth.ai/)는 표준 Lucene 분석기, Lucene의 영어 분석기 및 Microsoft의 영어 자연어 프로세서를 나란히 비교하여 보여 주는 타사 데모 앱입니다. 인덱스가 수정되었습니다. 잘 알려진 이야기의 텍스트가 포함되어 있습니다. 사용자가 입력한 각 검색 입력에 대해 각 분석기의 결과가 인접한 창에 표시되어 분석기별로 동일한 문자열을 어떤 식으로 처리하는지 살펴볼 수 있습니다. 

<a name="examples"></a>

## <a name="rest-examples"></a>REST 예제

다음 예제에서는 몇 가지 주요 시나리오에 대한 분석기 정의를 보여줍니다.

+ [사용자 지정 분석기 예제](#Custom-analyzer-example)
+ [필드에 분석기 할당 예제](#Per-field-analyzer-assignment-example)
+ [인덱싱 및 검색에 분석기 혼용](#Mixing-analyzers-for-indexing-and-search-operations)
+ [언어 분석기 예제](#Language-analyzer-example)

<a name="Custom-analyzer-example"></a>

### <a name="custom-analyzer-example"></a>사용자 지정 분석기 예제

이 예제에서는 사용자 지정 옵션을 사용하는 분석기 정을 보여줍니다. char 필터, 토크나이저 및 토큰 필터에 대한 사용자 지정 옵션은 명명된 구문으로 개별 지정되고 분석기 정의에서 참조됩니다. 미리 정의된 요소도 그대로 사용되며 이름별로 참조하기만 하면 됩니다.

이 예제를 진행합니다.

* 분석기는 검색 가능한 필드에 대한 필드 클래스의 속성입니다.
* 사용자 지정 분석기는 인덱스 정의의 일부입니다. 가볍게 사용자 지정(예: 한 필터에서 단일 옵션 사용자 지정)되거나 여러 위치에서 사용자 지정될 수도 있습니다.
* 이 경우에 사용자 지정 분석기는 "my_analyzer"이며, 사용자 지정된 표준 토크나이저 "my_standard_tokenizer" 및 소문자 필터와 사용자 지정된 asciifolding 필터 "my_asciifolding"라는 두 개의 토큰 필터를 차례로 사용합니다.
* 또한 2개의 사용자 지정 문자 필터 "map_dash" 및 "remove_whitespace"를 정의합니다. 첫 번째 필터는 모든 대시를 밑줄로 바꾸고 두 번째 필터는 모든 공백을 제거합니다. 공백은 매핑 규칙에서 UTF-8로 인코딩되어야 합니다. 문자 필터는 토큰화 전에 적용되며 결과 토큰에 영향을 미칩니다(표준 토크나이저는 대시 및 공백으로 분리되지만 밑줄은 사용하지 않습니다).

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"my_analyzer"
        }
     ],
     "analyzers":[
        {
           "name":"my_analyzer",
           "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
           "charFilters":[
              "map_dash",
              "remove_whitespace"
           ],
           "tokenizer":"my_standard_tokenizer",
           "tokenFilters":[
              "my_asciifolding",
              "lowercase"
           ]
        }
     ],
     "charFilters":[
        {
           "name":"map_dash",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["-=>_"]
        },
        {
           "name":"remove_whitespace",
           "@odata.type":"#Microsoft.Azure.Search.MappingCharFilter",
           "mappings":["\\u0020=>"]
        }
     ],
     "tokenizers":[
        {
           "name":"my_standard_tokenizer",
           "@odata.type":"#Microsoft.Azure.Search.StandardTokenizerV2",
           "maxTokenLength":20
        }
     ],
     "tokenFilters":[
        {
           "name":"my_asciifolding",
           "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
           "preserveOriginal":true
        }
     ]
  }
~~~~

<a name="Per-field-analyzer-assignment-example"></a>

### <a name="per-field-analyzer-assignment-example"></a>필드별 분석기 할당 예제

표준 분석기는 기본값입니다. 패턴 분석기와 같은 다른 미리 정의된 분석기로 기본값을 바꾸려 한다고 가정합니다. 사용자 지정 옵션을 설정하지 않은 경우 필드 정의에서 이름으로 지정하기만 하면 됩니다.

"analyzer" 요소에서 필드 기준으로 표준 분석기를 재정의합니다. 전역 재정의가 없습니다. 이 예제에서 `text1`는 패턴 분석기를 사용하고 분석기를 지정하지 않는 `text2`는 기본값을 사용합니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text1",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"pattern"
        },
        {
           "name":"text2",
           "type":"Edm.String",
           "searchable":true
        }
     ]
  }
~~~~

<a name="Mixing-analyzers-for-indexing-and-search-operations"></a>

### <a name="mixing-analyzers-for-indexing-and-search-operations"></a>인덱싱 및 검색 작업에 분석기 혼용

API는 인덱싱 및 검색에 대해 다른 분석기를 지정하기 위한 추가 인덱스 특성을 포함합니다. **searchAnalyzer** 및 **indexAnalyzer** 특성은 쌍으로 지정해야 하며, 단일 **analyzer** 특성을 대체합니다.


~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
     ],
  }
~~~~

<a name="Language-analyzer-example"></a>

### <a name="language-analyzer-example"></a>언어 분석기 예제

다른 언어의 문자열을 포함하는 필드는 언어 분석기를 사용할 수 있지만 다른 필드는 기본값을 그대로 둡니다(또는 다른 미리 정의되거나 사용자 지정된 분석기 사용). 언어 분석기를 사용하는 경우 인덱싱 및 검색 작업에 사용해야 합니다. 언어 분석기를 사용하는 필드는 인덱싱 및 검색에 다른 분석기를 가질 수 없습니다.

~~~~
  {
     "name":"myindex",
     "fields":[
        {
           "name":"id",
           "type":"Edm.String",
           "key":true,
           "searchable":false
        },
        {
           "name":"text",
           "type":"Edm.String",
           "searchable":true,
           "indexAnalyzer":"whitespace",
           "searchAnalyzer":"simple"
        },
        {
           "name":"text_fr",
           "type":"Edm.String",
           "searchable":true,
           "analyzer":"fr.lucene"
        }
     ],
  }
~~~~

## <a name="c-examples"></a>C#예제

.NET SDK 코드 샘플을 사용 하는 경우 사용 하 여 또는 분석기를 구성 하도록 이러한 예제를 추가할 수 있습니다.

+ [기본 제공 분석기를 할당 합니다.](#Assign-a-language-analyzer)
+ [분석기를 구성 합니다.](#Define-a-custom-analyzer)

<a name="Assign-a-language-analyzer"></a>

### <a name="assign-a-language-analyzer"></a>언어 분석기를 할당 합니다.

로 사용 되는 모든 분석기-를 구성 하지 않고도, 필드 정의에 지정 됩니다. 분석기 구문을 만들기 위한 요건은 없습니다. 

이 예제는 설명 필드에 Microsoft 영어 및 프랑스어 분석기를 할당합니다. 것이 더 큰 hotels.cs 파일에는 호텔 클래스를 사용 하 여 만드는 호텔 인덱스 정의에서 가져온 코드 조각을 합니다 [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) 샘플입니다.

호출 [분석기](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet)을 지정 하 고는 [AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) Azure Search에서 지원 되는 텍스트 분석기를 제공 하는 형식입니다.

```csharp
    public partial class Hotel
    {
       . . . 

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        [JsonProperty("description")]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrLucene)]
        [JsonProperty("description_fr")]
        public string DescriptionFr { get; set; }

      . . .
    }
```
<a name="Define-a-custom-analyzer"></a>

### <a name="define-a-custom-analyzer"></a>사용자 지정 분석기를 정의 합니다.

사용자 지정 또는 구성이 필요한 경우 인덱스는 분석기 구문 추가 해야 합니다. 이 정의 하 고 나면 추가할 수 있습니다 필드 정의 앞의 예제에서 설명한 것 처럼 합니다.

만들기는 [CustomAnalyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.customanalyzer?view=azure-dotnet) 개체입니다. 더 많은 예제를 참조 하세요 [CustomAnalyzerTests.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/src/SDKs/Search/DataPlane/Search.Tests/Tests/CustomAnalyzerTests.cs)합니다.

```csharp
{
   var definition = new Index()
   {
         Name = "hotels",
         Fields = FieldBuilder.BuildForType<Hotel>(),
         Analyzers = new[]
            {
               new CustomAnalyzer()
               {
                     Name = "url-analyze",
                     Tokenizer = TokenizerName.UaxUrlEmail,
                     TokenFilters = new[] { TokenFilterName.Lowercase }
               }
            },
   };

   serviceClient.Indexes.Create(definition);
```

## <a name="next-steps"></a>다음 단계

+ [전체 텍스트 검색이 Azure Search에서 작동하는 방식](search-lucene-query-architecture.md)의 포괄적인 설명을 검토하세요. 이 문서에서는 화면에서 직관적이지 않은 것처럼 보일 수 있는 동작을 설명하는 예제를 사용합니다.

+ 포털의 Search 탐색기에서 [문서 검색](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples) 예제 섹션 또는 [단순 쿼리 구문](query-simple-syntax.md)의 추가 쿼리 구문을 사용해 보세요.

+ [언어별 어휘 분석기](index-add-language-analyzers.md)를 적용하는 방법을 알아보세요.

+ 개별 필드에 대해 최소한의 처리 또는 특수한 처리를 수행하려면 [사용자 지정 분석기를 구성](index-add-custom-analyzers.md)하세요.

+ 이 데모 웹 사이트의 인접한 창에서 [표준 및 영어 분석기를 나란히 비교](https://alice.unearth.ai/)하세요. 

## <a name="see-also"></a>참고 항목

 [문서 검색 REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents) 

 [단순 쿼리 구문](query-simple-syntax.md) 

 [전체 Lucene 쿼리 구문](query-lucene-syntax.md) 
 
 [검색 결과 처리](search-pagination-page-layout.md)

<!--Image references-->
[1]: ./media/search-lucene-query-architecture/architecture-diagram2.png
