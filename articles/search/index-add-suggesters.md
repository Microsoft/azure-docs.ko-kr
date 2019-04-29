---
title: -Azure Search 인덱스에 미리 입력 쿼리를 추가 합니다.
description: 확인 기를 만들고 쿼리 용어를 autosuggested 또는 자동 완성을 호출 하는 요청을 공식화 하 여 Azure Search의 미리 입력 쿼리 작업을 사용 합니다.
ms.date: 03/22/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
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
ms.openlocfilehash: a8bc86c2d3511fa04e595b8b2988d9a98bf084b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844448"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Azure Search의 미리 입력에 대 한 인덱스를 확인 기 추가

A **suggester** 의 구조 이며는 [Azure Search 인덱스](search-what-is-an-index.md) "검색---입력할 때" 환경을 지 원하는 합니다. 이 미리 입력 쿼리 입력을 사용 하도록 설정 하려는 필드의 목록을 포함 합니다. 인덱스 내에서 동일한 확인 기 이러한 두 가지 미리 입력 변형 중 하나 또는 모두를 지원: *자동 완성* 단어 또는 구를 입력 하는 완료 *제안* 결과의 짧은 목록을 제공 합니다. 

다음 스크린샷은 두 미리 입력 기능을 보여 줍니다. 이 Xbox 검색 페이지에서 자동 완성 항목을 이동할 수 해당 쿼리에 대 한 새 검색 결과 페이지 반면 제안 특정 게임에 대 한 페이지로 이동 하는 실제 결과 있습니다. 자동 완성 검색 표시줄에서 하나의 항목을 제한할 수도 있고 여기에 표시 된 것과 같은 목록을 제공할 수 있습니다. 제안 사항이 있는 경우 최상의 결과 설명 하는 문서의 일부를 발생할 수 있습니다.

![자동 완성 및 제안 된 쿼리 비교 visual](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual 자동 완성 및 제안 된 쿼리 비교")

Azure Search에서 이러한 동작을 구현 하는 인덱스 및 쿼리 구성 요소입니다. 

+ 인덱스 구성 요소는 확인 기는 합니다. Suggester를 만들려면 포털, REST API 또는.NET SDK를 사용할 수 있습니다. 

+ 쿼리 구성 요소에는 쿼리 요청 (제안 또는 자동 완성 작업)에 지정 된 동작입니다. 

> [!Important]
> 자동 완성은 현재 미리 보기 REST Api에서에서 사용할 수 있는 미리 보기 및.NET SDK입니다. 프로덕션 응용 프로그램에는 것이 아닙니다. 

검색---입력할 때 지원 필드 별로에 사용 됩니다. 원하는 환경을 스크린샷에 표시 된 것과 비슷한 경우 동일한 검색 솔루션 내에서 모두 미리 입력 동작을 구현할 수 있습니다. 두 요청 대상 합니다 *문서* 특정 인덱스 및 응답의 컬렉션을 사용자가 최소한 세 개의 입력된 문자열을 제공한 후에 반환 됩니다.

## <a name="create-a-suggester"></a>제안기 만들기

Suggester에 몇 가지 속성으로 주로를 사용할 수 있도록 하는 미리 입력 환경을 필드의 컬렉션입니다. 예를 들어 여행 앱은 목적지, 도시 및 명소에 대한 자동 완성(typeahead) 검색을 사용하도록 설정할 수도 있습니다. 따라서 fields 컬렉션에는 세 개의 필드가 모두가 이동 됩니다.

Suggester를 만들려면 인덱스 스키마에 하나를 추가 합니다. 인덱스에서 하나의 확인 기를 사용할 수 있습니다 (특히, suggesters 컬렉션에 suggester). 

### <a name="use-the-rest-api"></a>REST API 사용

REST API를 통해 확인 기를 추가할 수 있습니다 [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) 하거나 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index)합니다. 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```
Suggester를 만든 후 추가 합니다 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 기능을 호출 하 여 쿼리 논리에 있습니다.

### <a name="use-the-net-sdk"></a>.NET SDK 사용

C#에서 정의 [Suggester 개체](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)합니다. `Suggesters` 컬렉션 이지만 하나씩만 사용할 수 있습니다. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>속성 참조

확인 기에 대 한 주의할 점이 점 (확인 기는 요청에 대 한 이름으로 참조 됩니다.) 이름는 searchMode (현재 하나만, "analyzingInfixMatching"), 및 미리 입력이 사용 되는 필드의 목록입니다. 

확인기를 정의하는 속성은 다음과 같습니다.

|자산      |설명      |
|--------------|-----------------|
|`name`        |확인 기의 이름입니다. 호출할 때 suggester의 이름을 사용 합니다 [제안 REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [REST API (미리 보기) 자동 완성](https://docs.microsoft.com/rest/api/searchservice/autocomplete)합니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원되는 모드는 `analyzingInfixMatching`뿐입니다. 이 모드에서는 문장 시작 부분이나 중간에서 유동적 구 일치를 수행합니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 형식이 `Edm.String` 및 `Collection(Edm.String)`인 필드만 제안 원본으로 사용할 수 있습니다. 또한 사용자 지정 언어 분석기가 설정되지 않은 필드만 사용할 수 있습니다.<p/>드롭다운 목록 또는 검색 표시줄에서 완료 된 문자열 인지를 예상 하 고 적절 한 응답을 하는 필드에만 지정 합니다.<p/>호텔 이름 정밀도 있기 때문에 적합 합니다. 설명 및 설명 같은 세부 정보 표시 필드는 답답한입니다. 마찬가지로, 범주 / 태그 등의 반복 필드는 떨어집니다. 예제에서는 포함 "category" 그래도 여러 필드를 포함할 수 있는지를 보여 줍니다. |

## <a name="when-to-create-a-suggester"></a>Suggester를 만들어야 하는 경우

인덱스 다시 작성, 확인 기는 및에서 지정 된 필드를 방지 하려면 `sourceFields` 동시에 만들어야 합니다.

기존 필드에 포함 된 여기서 기존 인덱스에 확인 기를 추가 하는 경우 `sourceFields`, 필드 정의 근본적으로 변경 및 다시 빌드가 필요 합니다. 자세한 내용은 [Azure Search 인덱스를 다시 작성 하는 방법을](search-howto-reindex.md)합니다.

## <a name="how-to-use-a-suggester"></a>Suggester를 사용 하는 방법

에서 설명한 대로 제안 된 쿼리, 자동 완성, 또는 둘 다 suggester를 사용할 수 있습니다. 

Suggester 작업에서는 요청에 대해 참조 됩니다. 예를 들어 위한 GET REST 호출에서 하나를 지정 `suggest` 또는 `autocomplete` 문서 컬렉션에 있습니다. Rest의 경우 suggester를 만든 후 사용 합니다 [제안 API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 또는 [자동 완성 API (미리 보기)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 쿼리 논리에서입니다.

For.NET을 사용 하 여 [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) 하거나 [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet)합니다.

두 요청을 보여 주는 예제를 참조 하세요 [Azure Search에서 자동 완성 기능 및 제안을 추가 하는 것에 대 한 예제](search-autocomplete-tutorial.md)합니다.

## <a name="sample-code"></a>샘플 코드

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 샘플 모두 포함 C# 및 Java 코드는 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 

샌드박스 Azure Search 서비스를 사용 하며 미리 로드 된 인덱스를 갖도록 모든 작업을 수행 하는 실행 하려면 F5 키를 누릅니다. 구독 또는 로그인 필요 없습니다.

## <a name="next-steps"></a>다음 단계

다음 예제를 요청은 작성 하는 방법을 참조 하세요.를 사용 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [제안 사항 및 자동 완성 예제](search-autocomplete-tutorial.md) 
