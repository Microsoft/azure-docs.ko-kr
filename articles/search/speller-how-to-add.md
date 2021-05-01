---
title: 맞춤법 검사 추가
titleSuffix: Azure Cognitive Search
description: 쿼리를 실행하기 전에 쿼리 용어의 오타를 수정하려면 쿼리 파이프라인에 맞춤법 교정 기능을 연결합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/26/2021
ms.custom: references_regions
ms.openlocfilehash: 52ac3ee4ea2f71e285d21c7b6d082e84fa090da1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625911"
---
# <a name="add-spell-check-to-queries-in-cognitive-search"></a>Cognitive Search에서 쿼리에 대한 맞춤법 검사 추가

> [!IMPORTANT]
> 맞춤법 교정 기능은 공개 미리 보기로 제공되며 미리 보기 REST API를 통해서만 이용할 수 있습니다. 미리 보기 기능은 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 있는 그대로 제공됩니다. 처음 미리 보기를 시작할 때에는 맞춤법 검사기 요금이 청구되지 않습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

검색 엔진에 도달하기 전에 개별 검색 쿼리 용어의 맞춤법을 교정하여 재현율을 개선할 수 있습니다. **맞춤법 검사기** 매개 변수는 모든 쿼리 유형([단순](query-simple-syntax.md), [전체](query-lucene-syntax.md), 현재 공개 미리 보기로 제공되는 새로운 [의미 체계](semantic-how-to-query-request.md) 옵션)에서 지원됩니다.

## <a name="prerequisites"></a>필수 구성 요소

+ 영어 콘텐츠를 포함하는 기존 검색 인덱스입니다. 현재 맞춤법 수정 기능이 [동의어](search-synonyms.md)를 사용하여 작동하지는 않습니다. 모든 필드 정의에서 동의어 맵을 지정하는 인덱스에는 사용하지 마세요.

+ 쿼리를 보내기 위한 검색 클라이언트

  검색 클라이언트는 쿼리 요청에 대한 미리 보기 REST API를 지원해야 합니다. 미리 보기 API에 대한 REST 호출을 수행하도록 수정한 [Postman](search-get-started-rest.md), [Visual Studio Code](search-get-started-vs-code.md)또는 코드를 사용할 수 있습니다.

+ 맞춤법 수정 기능을 사용하는 [쿼리 요청](/rest/api/searchservice/preview-api/search-documents)에는 "api-version=2020-06-30-Preview", "speller=lexicon", "queryLanguage=en-us"가 있습니다.

  맞춤법 검사기에는 queryLanguage가 필요하며, 현재 "en-us"만 유효한 값입니다.

> [!Note]
> 맞춤법 검사기 매개 변수는 의미 체계 검색을 제공하는 동일한 지역의 모든 계층에서 사용할 수 있습니다. 이 미리 보기 기능에 대한 액세스 권한을 등록할 필요는 없습니다. 자세한 내용은 [가용성 및 가격 책정](semantic-search-overview.md#availability-and-pricing)을 참조하세요.

## <a name="spell-correction-with-simple-search"></a>단순 검색으로 맞춤법 수정

다음 예제에는 기본 제공 hotels-sample 인덱스를 사용하여 간단한 자유 형식 텍스트 쿼리에 대한 맞춤법을 수정하는 과정이 나와 있습니다. 맞춤법 수정 항목이 없으면 쿼리에서 결과를 0개 반환합니다. 수정 항목이 있으면 쿼리에서 Johnson의 가족 지향 재정렬 결과를 1개 반환합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "famly acitvites",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "queryType": "simple",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="spell-correction-with-full-lucene"></a>전체 Lucene으로 맞춤법 수정

분석을 수행하는 개별 쿼리 용어의 맞춤법을 수정하므로, 일부 Lucene 쿼리에서는 맞춤법 검사기 매개 변수를 사용할 수 있지만 다른 쿼리에서는 사용할 수 없습니다.

+ 텍스트 분석을 우회하는 비호환 쿼리 양식에는 와일드카드, regex, 퍼지가 포함됩니다.
+ 호환되는 쿼리 양식에는 필드 지정 검색, 근접, 용어 상승이 포함됩니다.

이 예제에서는 전체 Lucene 구문과 맞춤법이 틀린 쿼리 용어를 사용하여 범주 필드에 대한 필드 지정 검색을 사용합니다. 맞춤법 검사기를 포함하면 "Suiite"의 오타가 수정되고 쿼리에 성공합니다.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview
{
    "search": "Category:(Resort and Spa) OR Category:Suiite",
    "queryType": "full",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "select": "Category",
    "count": true
}
```

## <a name="spell-correction-with-semantic-search"></a>의미 체계 검색으로 맞춤법 수정

이 쿼리는 한 가지 용어를 제외한 모든 용어의 오타를 사용하여 관련 결과를 반환하도록 맞춤법을 수정합니다. 자세한 내용은 [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)를 참조하세요.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview     
{
    "search": "hisotoric hotell wiht great restrant nad wiifi",
    "queryType": "semantic",
    "speller": "lexicon",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Tags,Description",
    "select": "HotelId,HotelName,Description,Category,Tags",
    "count": true
}
```

## <a name="language-considerations"></a>언어 관련 고려 사항

맞춤법 검사기에 필요한 queryLanguage 매개 변수는 인덱스 스키마의 필드 정의에 할당된 [언어 분석기](index-add-language-analyzers.md)와 일치해야 합니다. 

+ queryLanguage는 맞춤법 검사에 사용되는 lexicons를 결정합니다. 또한 "queryType=semantic"을 사용하는 경우 [의미 체계 순위 알고리즘](semantic-answers.md)에 대한 입력 정보로 사용됩니다.

+ 언어 분석기는 인덱싱 및 쿼리 실행 중에 검색 인덱스에서 일치하는 문서를 찾는 데 사용됩니다. 언어 분석기를 사용하는 필드 정의의 예제로는 `"name": "Description", "type": "Edm.String", "analyzer": "en.microsoft"`이(가) 있습니다.

맞춤법 검사기를 사용할 때 최상의 결과를 얻으려면 queryLanguage가 "en-us"인 경우 언어 분석기도 영어 변형("en.microsoft" 또는 "en.lucene")이어야 합니다.

> [!NOTE]
> 언어와 관계없는 분석기(예: 키워드, 단순, 표준, 중지, 공백 또는 `standardasciifolding.lucene`)는 queryLanguage 설정과 충돌하지 않습니다.

쿼리 요청에서 설정하는 queryLanguage는 맞춤법 검사기, 답변 및 캡션에 동일하게 적용됩니다. 개별 파트에 대해 재정의하지 않습니다.

검색 인덱스의 콘텐츠는 여러 언어로 구성될 수 있지만, 쿼리 입력은 한 가지 언어로 작성될 가능성이 높습니다. 검색 엔진에서는 queryLanguage, 언어 분석기 및 콘텐츠가 구성된 언어의 호환성을 확인하지 않으므로 잘못된 결과가 생성되지 않도록 적절히 쿼리 범위를 지정해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [의미 체계 쿼리 만들기](semantic-how-to-query-request.md)
+ [기본 쿼리 만들기](search-query-create.md)
+ [전체 Lucene 쿼리 구문 사용](query-Lucene-syntax.md)
+ [단순 쿼리 구문 사용](query-simple-syntax.md)
+ [의미 체계 검색 개요](semantic-search-overview.md)