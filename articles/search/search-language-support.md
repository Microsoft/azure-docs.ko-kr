---
title: 영어가 아닌 검색 쿼리에 대 한 다중 언어 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search는 Lucene의 언어 분석기 및 Microsoft의 자연어 처리 기술을 활용 하 여 56 언어를 지원 합니다.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: c7d574bf172a792c59e4b00ea9ad0366ad1f17ad
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922857"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Azure Cognitive Search에서 여러 언어에 대 한 인덱스를 만드는 방법

인덱스는 여러 언어의 콘텐츠를 포함 하는 필드를 포함할 수 있습니다. 예를 들어 언어별 문자열의 개별 필드를 만들 수 있습니다. 인덱싱 및 쿼리 중에 최상의 결과를 위해 적절 한 언어 규칙을 제공 하는 언어 분석기를 할당 합니다. 

Azure Cognitive Search는 Lucene 및 Microsoft에서 Analyzer 속성을 사용 하 여 개별 필드에 할당할 수 있는 많은 언어 분석기를 제공 합니다. 이 문서에 설명 된 대로 포털에서 언어 분석기를 지정할 수도 있습니다.

## <a name="add-analyzers-to-fields"></a>필드에 분석기 추가

언어 분석기는 필드가 생성 될 때 지정 됩니다. 기존 필드 정의에 분석기를 추가 하려면 인덱스를 덮어쓰거나 다시 로드 하거나 원래와 동일 하지만 분석기 할당을 사용 하 여 새 필드를 만들어야 합니다. 그러면 사용 하지 않는 필드를 편리 하 게 삭제할 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 고 검색 서비스를 찾습니다.
1. 서비스 대시보드 위쪽의 명령 모음에 있는 **인덱스 추가** 를 클릭하여 새 인덱스를 시작하거나, 기존 인덱스를 여러 기존 인덱스에 추가하는 새 필드에 대해 분석기를 설정합니다.
1. 이름을 제공 하 여 필드 정의를 시작 합니다.
1. Edm. String 데이터 형식을 선택 합니다. 문자열 필드만 전체 텍스트 검색 가능입니다.
1. **검색 가능한** 특성을 설정 하 여 Analyzer 속성을 사용 하도록 설정 합니다. 언어 분석기를 사용 하려면 필드가 텍스트를 기반으로 해야 합니다.
1. 사용 가능한 분석기 중 하나를 선택 합니다. 

![필드 정의 중 언어 분석기 할당](media/search-language-support/select-analyzer.png "필드 정의 중 언어 분석기 할당")

기본적으로 검색 가능한 모든 필드는 언어와 무관 한 [표준 Lucene 분석기](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) 를 사용 합니다. 지원 되는 분석기의 전체 목록을 보려면 [Azure Cognitive Search 인덱스에 언어 분석기 추가](index-add-language-analyzers.md)를 참조 하세요.

포털에서 분석기는 그대로 사용 하기 위한 것입니다. 사용자 지정 또는 필터 및 토크 나이저의 특정 구성이 필요한 경우 코드에서 [사용자 지정 분석기를 만들어야](index-add-custom-analyzers.md) 합니다. 포털에서 사용자 지정 분석기를 선택 하거나 구성 하는 기능을 지원 하지 않습니다.

## <a name="query-language-specific-fields"></a>쿼리 언어별 필드

필드에 대해 언어 분석기를 선택한 후에는 해당 필드의 모든 인덱싱 및 검색 요청에 해당 분석기를 사용합니다. 여러 분석기를 사용 하 여 여러 필드에 대해 쿼리를 실행 하는 경우 쿼리는 각 필드에 대해 할당 된 분석기에 의해 독립적으로 처리 됩니다.

쿼리를 실행한 에이전트의 언어를 아는 경우 검색 요청의 범위를 **searchFields** 쿼리 매개 변수를 사용하여 특정 필드로 지정할 수 있습니다. 다음 쿼리는 폴란드어로 된 설명에 대해서만 실행됩니다.

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2020-06-30`

[**검색 탐색기**](search-explorer.md) 를 사용 하 여 위에서 설명한 것과 비슷한 쿼리를 붙여넣어 포털에서 인덱스를 쿼리할 수 있습니다.

## <a name="boost-language-specific-fields"></a>언어별 필드 향상

간혹 쿼리를 실행한 에이전트의 언어를 알지 못할 수 있습니다. 이 경우 모든 필드에 대해 동시에 쿼리를 실행할 수 있습니다. 필요한 경우 [평가 프로필](index-add-scoring-profiles.md)을 사용하여 특정 언어로 된 결과에 대한 우선 순위를 정의할 수 있습니다. 아래 예에서는 영어 설명에서 찾은 일치 항목이 폴란드 및 프랑스어 항목 일치보다 상대적으로 높은 점수를 받게 됩니다.

```http
    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]
```

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2020-06-30`

## <a name="next-steps"></a>다음 단계

.NET 개발자 인 경우 [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) 및 [Analyzer](/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) 속성을 사용 하 여 언어 분석기를 구성할 수 있습니다.