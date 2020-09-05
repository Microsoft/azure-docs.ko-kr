---
title: 제안기 만들기
titleSuffix: Azure Cognitive Search
description: 자동 완성 또는 자동 제안 쿼리 용어를 호출 하는 확인 기 및 공식화 요청을 만들어 Azure Cognitive Search에서 미리 입력 쿼리 작업을 사용 하도록 설정 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4889e73e851e285c84d5d4429298e9a7cdacc140
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014390"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>쿼리에서 자동 완성 및 제안 된 결과를 사용 하도록 설정 하는 확인 기 만들기

Azure Cognitive Search에서 "검색 형식"은 [검색 인덱스](search-what-is-an-index.md)에 추가 된 **확인 기** 구문을 통해 사용 하도록 설정 됩니다. 확인 기는 두 가지 환경을 지원 합니다. *자동 완성*은 전체 용어 쿼리를 위한 부분 입력을 완료 하 고,는 클릭을 통해 특정 일치 항목에 초대 하는 *제안을* 지원 합니다. 자동 완성 기능은 쿼리를 생성 합니다. 제안 사항은 일치 하는 문서를 생성 합니다.

[C #에서 첫 번째 앱 만들기](tutorial-csharp-type-ahead-and-suggestions.md) 의 다음 스크린샷은 두 가지를 모두 보여 줍니다. 자동 완성 기능을 사용 하는 경우 "내"에서 "휴먼"를 마무리 하는 잠재적인 용어 제안 사항은 최소 검색 결과입니다. 호텔 이름과 같은 필드는 인덱스에서 일치 하는 호텔 검색 문서를 나타냅니다. 제안 사항을 위해 설명 정보를 제공 하는 모든 필드를 표시할 수 있습니다.

![자동 완성 및 제안 된 쿼리를 시각적으로 비교](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "자동 완성 및 제안 된 쿼리를 시각적으로 비교")

이러한 기능은 개별적으로 또는 함께 사용할 수 있습니다. Azure Cognitive Search에서 이러한 동작을 구현 하기 위해 인덱스 및 쿼리 구성 요소가 있습니다. 

+ 인덱스에서 확인 기를 인덱스에 추가 합니다. 포털, [REST API](/rest/api/searchservice/create-index)또는 [.net SDK](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)를 사용할 수 있습니다. 이 문서의 나머지 부분에서는 확인 기를 만드는 방법에 대해 집중적으로 설명 합니다.

+ 쿼리 요청에서 [아래에 나열 된 api](#how-to-use-a-suggester)중 하나를 호출 합니다.

검색 형식 지원은 문자열 필드에 대 한 필드 기준으로 설정 됩니다. 스크린샷에 표시 된 것과 비슷한 환경을 원할 경우 동일한 검색 솔루션 내에서 형식 미리 동작을 구현할 수 있습니다. 두 요청은 특정 인덱스의 *문서* 컬렉션을 대상으로 하 고 사용자가 적어도 3 개의 문자 입력 문자열을 제공한 후 응답이 반환 됩니다.

## <a name="what-is-a-suggester"></a>확인 기 무엇 인가요?

확인 기는 부분 쿼리에 일치 하는 접두사를 저장 하 여 검색 형식 동작을 지 원하는 내부 데이터 구조입니다. 토큰화 된 용어와 마찬가지로 접두사는 확인 기 fields 컬렉션에 지정 된 각 필드에 대해 하나씩 반전 된 인덱스에 저장 됩니다.

## <a name="define-a-suggester"></a>확인 기 정의

확인 기를 만들려면 [인덱스 스키마](/rest/api/searchservice/create-index) 에 하나를 추가 하 고 [각 속성을 설정](#property-reference)합니다. 확인 기를 만드는 가장 좋은 시기는이를 사용할 필드를 정의 하는 경우입니다.

+ 문자열 필드만 사용

+ 필드에 기본 표준 Lucene 분석기 ( `"analyzer": null` ) 또는 [언어 분석기](index-add-language-analyzers.md) (예:)를 사용 합니다. `"analyzer": "en.Microsoft"`

### <a name="choose-fields"></a>필드 선택

확인 기에는 여러 속성이 있지만 주로 검색 기능을 사용 하도록 설정 하는 문자열 필드 모음이 있습니다. 각 인덱스 마다 하나의 확인 기 있습니다. 따라서 확인 기 목록에는 제안과 자동 완성 모두에 콘텐츠를 제공 하는 모든 필드가 포함 되어야 합니다.

추가 콘텐츠는 장기적으로 완료 될 수 있으므로 더 큰 필드 풀에서 자동 완성 혜택을 얻을 수 있습니다.

반면에 제안 사항은 필드를 선택 하는 경우 더 나은 결과를 생성 합니다. 제안에는 검색 문서에 대 한 프록시가 있으므로 단일 결과를 가장 잘 나타내는 필드를 사용할 수 있습니다. 여러 일치 항목을 구분 하는 이름, 제목 또는 기타 고유 필드가 가장 잘 작동 합니다. 필드가 반복 되는 값으로 구성 된 경우 제안 사항은 동일한 결과로 구성 되며 사용자는 어떤 항목을 클릭할 지 알지 못합니다.

검색에 사용 되는 환경을 모두 만족 시키려면 자동 완성에 필요한 모든 필드를 추가한 다음 **$select**, **$top**, **$filter**및 **searchfields** 를 사용 하 여 제안에 대 한 결과를 제어 합니다.

### <a name="choose-analyzers"></a>분석기 선택

분석기를 선택 하면 필드를 토큰화 하 고 그 다음에 접두사를 붙일 방법이 결정 됩니다. 예를 들어 "상황에 맞는"와 같은 하이픈을 사용 하는 문자열의 경우 언어 분석기를 사용 하면 "컨텍스트", "중요", "상황에 맞는" 등의 토큰 조합이 발생 합니다. 표준 Lucene 분석기를 사용 했지만 하이픈을 넣은 문자열이 존재 하지 않습니다. 

분석기를 평가할 때는 [텍스트 분석 API](/rest/api/searchservice/test-analyzer) 를 사용 하 여 용어가 토큰화 되는 방법에 대 한 통찰력을 얻고 이후 접두사를 접두사로 사용 하는 방법을 고려 인덱스를 작성 한 후에는 문자열에 대해 다양 한 분석기를 시도 하 여 토큰 출력을 볼 수 있습니다.

[사용자 지정 분석기](index-add-custom-analyzers.md) 또는 [미리 정의 된 분석기](index-add-custom-analyzers.md#predefined-analyzers-reference) (표준 Lucene 제외)를 사용 하는 필드는 잘못 된 결과를 방지 하기 위해 명시적으로 허용 되지 않습니다.

> [!NOTE]
> 분석기 제약 조건을 해결 해야 하는 경우, 예를 들어 특정 쿼리 시나리오에 키워드 또는 ngram 분석기가 필요한 경우 동일한 콘텐츠에 대해 두 개의 개별 필드를 사용 해야 합니다. 이렇게 하면 필드 중 하나에 확인 기를 사용할 수 있고 다른 하나는 사용자 지정 분석기 구성으로 설정할 수 있습니다.

### <a name="when-to-create-a-suggester"></a>확인 기를 만들어야 하는 경우

확인 기를 만드는 가장 좋은 시기는 필드 정의를 만드는 것입니다.

기존 필드를 사용 하 여 확인 기를 만들려고 하면 API에서이를 허용 하지 않습니다. 두 개 이상의 문자 조합에서 부분 용어를 전체 용어와 함께 토큰화 하는 경우 인덱싱을 수행 하는 동안 접두사가 생성 됩니다. 기존 필드가 이미 토큰화 된 경우 확인 기에 추가 하려면 인덱스를 다시 작성 해야 합니다. 자세한 내용은 [Azure Cognitive Search 인덱스를 다시 작성 하는 방법](search-howto-reindex.md)을 참조 하세요.

## <a name="create-using-rest"></a>REST를 사용 하 여 만들기

REST API에서 [Create index](/rest/api/searchservice/create-index) 또는 [Update index](/rest/api/searchservice/update-index)를 통해 확인 기를 추가 합니다. 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>.NET을 사용 하 여 만들기

C #에서 [확인 기 개체](/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)를 정의 합니다. `Suggesters` 는 컬렉션 이지만 하나의 항목만 사용할 수 있습니다. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>속성 참조

|속성      |설명      |
|--------------|-----------------|
|`name`        |제안기의 이름입니다.|
|`searchMode`  |후보 구를 검색하는 데 사용되는 전략입니다. 현재 지원 되는 모드는 `analyzingInfixMatching` 현재 단어의 시작 부분에서 일치 하는입니다.|
|`sourceFields`|제안 내용의 원본인 하나 이상의 필드 목록입니다. 필드는 및 형식 이어야 `Edm.String` 합니다 `Collection(Edm.String)` . 필드에 분석기를 지정 하는 경우 사용자 지정 분석기가 아닌 [이 목록](/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) 에서 명명 된 분석기 여야 합니다.<p/> 검색 표시줄이 나 드롭다운 목록에서 완성 된 문자열 인지 여부에 관계 없이 필요한 적절 한 응답에 대해 자신을 지 원하는 필드만 지정 하는 것이 가장 좋습니다.<p/>호텔 이름은 전체 자릿수가 있으므로 좋은 후보입니다. 설명 및 주석과 같은 자세한 정보 필드에는 너무 조밀 하 게 표시 됩니다. 마찬가지로 범주 및 태그와 같은 반복적인 필드도 효과적이 지 않습니다. 예제에는 여러 필드를 포함할 수 있음을 보여 주는 "category"가 포함 되어 있습니다. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>확인 기 사용

확인 기가 쿼리에 사용 됩니다. 확인 기을 만든 후에는 다음과 같은 Api 중 하나를 호출 하 여 검색을 입력 합니다.

+ [제안 REST API](/rest/api/searchservice/suggestions) 
+ [자동 완성 REST API](/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync 메서드](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync 메서드](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

검색 응용 프로그램에서 클라이언트 코드는 [JQUERY UI 자동 완성](https://jqueryui.com/autocomplete/) 같은 라이브러리를 활용 하 여 부분 쿼리를 수집 하 고 일치 하는 항목을 제공 해야 합니다. 이 작업에 대 한 자세한 내용은 [클라이언트 코드에 자동 완성 또는 제안 된 결과 추가](search-autocomplete-tutorial.md)를 참조 하세요.

API 사용법은 다음 REST API 자동 완성 호출에 설명 되어 있습니다. 이 예제에서는 두 가지 내용 있습니다. 첫째, 모든 쿼리와 마찬가지로 작업은 인덱스의 문서 컬렉션에 대해 수행 되 고 쿼리는 **검색** 매개 변수를 포함 하며이 경우 부분 쿼리를 제공 합니다. 두 번째로, 요청에 **suggesterName** 를 추가 해야 합니다. 인덱스에 확인 기가 정의 되어 있지 않으면 자동 완성 또는 제안에 대 한 호출이 실패 합니다.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>예제 코드

+ [C #에서 첫 번째 앱 만들기 (3 단원-검색 형식 추가)](tutorial-csharp-type-ahead-and-suggestions.md) 샘플은 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 이 코드 샘플은 샌드박스 Azure Cognitive Search 서비스에서 실행 되며 미리 로드 된 호텔 인덱스를 사용 하므로 F5 키를 눌러 응용 프로그램을 실행 하면 됩니다. 구독 또는 로그인이 필요 하지 않습니다.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) 는 c # 및 Java 코드를 모두 포함 하는 이전 샘플입니다. 또한 확인 기 생성, 제안 된 쿼리, 자동 완성 및 패싯 탐색을 보여 줍니다. 이 코드 샘플에서는 호스팅된 [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 샘플 데이터를 사용 합니다. 

## <a name="next-steps"></a>다음 단계

요청을 공식화 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [클라이언트 코드에 자동 완성 및 제안 추가](search-autocomplete-tutorial.md)