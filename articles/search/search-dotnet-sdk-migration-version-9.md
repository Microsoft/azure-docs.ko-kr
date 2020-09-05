---
title: Azure Search .NET SDK 버전 9로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Search .NET SDK 버전 9로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7726877efcb72caf5bb4c92e2e1c65e5df68f270
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002711"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Azure Search .NET SDK 버전 9로 업그레이드

버전 7.0-preview 또는 이전 버전의 [Azure Search .NET SDK](/dotnet/api/overview/azure/search)를 사용 하는 경우이 문서는 버전 9를 사용 하도록 응용 프로그램을 업그레이드 하는 데 도움이 됩니다.

> [!NOTE]
> 버전 8.0-preview를 사용 하 여 일반적으로 사용할 수 없는 기능을 평가 하려는 경우이 문서의 지침에 따라 이전 버전에서 8.0-preview로 업그레이드할 수도 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search .NET SDK 버전 9에는 이전 버전과의 많은 변경 내용이 포함 되어 있습니다. 이러한 변경 내용 중 일부는 주요 변경 내용 이지만 코드를 비교적 약간만 변경 해야 합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 버전 4.0-preview 또는 이전 버전을 사용 하는 경우 먼저 버전 5로 업그레이드 한 후 버전 9로 업그레이드 해야 합니다. 지침은 [Azure Search .NET SDK 버전 5로 업그레이드를](search-dotnet-sdk-migration-version-5.md) 참조 하세요.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>버전 9의 새로운 기능
Azure Search .NET SDK 버전 9는 다음 기능을 포함 하는 2019-05-06 버전의 Azure Search REST API를 대상으로 합니다.

* [AI 보강](cognitive-search-concept-intro.md) 는 이미지, blob 및 기타 구조화 되지 않은 데이터 원본에서 텍스트를 추출 하는 기능으로, Azure Search 인덱스에서 보다 검색 하기 쉽도록 콘텐츠를 보강 합니다.
* [복합 형식을](search-howto-complex-data-types.md) 지원 하면 Azure Search 인덱스에서 거의 모든 중첩 된 JSON 구조를 모델링할 수 있습니다.
* [자동 완성](search-autocomplete-tutorial.md) 은 검색 형식 동작을 구현 하기 위한 **제안** API에 대 한 대안을 제공 합니다. 자동 완성은 사용자가 현재 입력하고 있는 단어 또는 구를 "완성"합니다.
* Azure Blob 인덱싱의 일부인 [JsonLines 구문 분석 모드](search-howto-index-json-blobs.md)는 JSON 엔터티 마다 줄 바꿈으로 구분 된 하나의 검색 문서를 만듭니다.

### <a name="new-preview-features-in-version-80-preview"></a>버전 8.0-미리 보기의 새로운 미리 보기 기능
버전 8.0-Azure Search .NET SDK 대상 API 버전 2017-11-11-Preview의 미리 보기입니다. 이 버전에는 다음과 같은 버전 9의 모든 기능이 포함 되어 있습니다.

* 서비스 쪽 암호화에 대 한 [고객 관리 암호화 키는](search-security-manage-encryption-keys.md) 새로운 미리 보기 기능입니다. Microsoft에서 관리 하는 기본 제공 암호화 외에도 키의 유일한 소유자 인 추가 암호화 계층을 적용할 수 있습니다.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드에 실패 하는 경우 각 빌드 오류를 수정 해야 합니다. 잠재적 빌드 오류를 해결 하는 방법에 대 한 자세한 내용은 [버전 9의 주요 변경 내용](#ListOfChanges) 을 참조 하세요.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어 응용 프로그램에서 속성을 사용 하는 경우 `DataSourceType.DocumentDb` "이 멤버가 사용 되지 않습니다. 대신 CosmosDb를 사용 하십시오.

모든 빌드 오류 또는 경고가 수정되면 원하는 경우 새로운 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능은 [버전 9의 새로운](#WhatsNew)기능에 자세히 설명 되어 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>버전 9의 주요 변경 내용

버전 9에는 응용 프로그램을 다시 빌드하는 것 외에도 코드를 변경 해야 할 수 있는 몇 가지 주요 변경 사항이 있습니다.

> [!NOTE]
> 아래 변경 목록은 완전 하지 않습니다. 일부 변경 내용으로 인해 빌드 오류가 발생 하지는 않지만, 이전 버전의 Azure Search .NET SDK 어셈블리에 종속 된 어셈블리와의 이진 호환성이 중단 되기 때문에 기술적으로 중단 됩니다. 이러한 변경 내용은 아래에 나열 되어 있지 않습니다. 이진 호환성 문제를 방지 하려면 버전 9로 업그레이드할 때 응용 프로그램을 다시 빌드 하세요.

### <a name="immutable-properties"></a>변경할 수 없는 속성

이제 여러 모델 클래스의 공용 속성을 변경할 수 없습니다. 테스트를 위해 이러한 클래스의 사용자 지정 인스턴스를 만들어야 하는 경우 새 매개 변수가 있는 생성자를 사용할 수 있습니다.

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>필드에 대 한 변경 내용

`Field`클래스가 복합 필드를 나타낼 수도 있으므로 이제 변경 되었습니다.

이제 다음 `bool` 속성은 nullable입니다.

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

이러한 속성은 이제 `null` 복잡 한 필드의 경우에 필요 하기 때문입니다. 이러한 속성을 읽는 코드가 있는 경우를 처리 하도록 준비 해야 `null` 합니다. 의 다른 모든 속성은 항상 발생 하 `Field` 고 null을 허용 해야 하며, 그 중 일부는 `null` 복합 필드의 경우, 특히 다음과 같습니다.

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

의 매개 변수가 없는 생성자 `Field` 가 생성 되었습니다 `internal` . 지금부터의 모든에는 `Field` 생성 시 명시적인 이름 및 데이터 형식이 필요 합니다.

### <a name="simplified-batch-and-results-types"></a>단순화 된 일괄 처리 및 결과 형식

7.0-preview 및 이전 버전에서는 문서 그룹을 캡슐화 하는 다양 한 클래스가 병렬 클래스 계층 구조로 구성 되었습니다.

  -  `DocumentSearchResult` 다음 `DocumentSearchResult<T>` 에서 상속 됨 `DocumentSearchResultBase`
  -  `DocumentSuggestResult` 다음 `DocumentSuggestResult<T>` 에서 상속 됨 `DocumentSuggestResultBase`
  -  `IndexAction` 다음 `IndexAction<T>` 에서 상속 됨 `IndexActionBase`
  -  `IndexBatch` 다음 `IndexBatch<T>` 에서 상속 됨 `IndexBatchBase`
  -  `SearchResult` 다음 `SearchResult<T>` 에서 상속 됨 `SearchResultBase`
  -  `SuggestResult` 다음 `SuggestResult<T>` 에서 상속 됨 `SuggestResultBase`

제네릭 형식 매개 변수 없이 파생 된 형식은 "동적으로 형식화 된" 시나리오에서 사용 되며 형식의 사용을 가정 `Document` 합니다.

8.0-preview 버전부터 기본 클래스 및 제네릭이 아닌 파생 클래스가 모두 제거 되었습니다. 동적으로 형식화 된 시나리오의 경우, 등을 사용할 수 있습니다 `IndexBatch<Document>` `DocumentSearchResult<Document>` .
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum 제거 됨

`ExtensibleEnum` 기본 클래스가 제거되었습니다. 예를 들어, 여기에서 파생 된 모든 클래스는 이제 구조체 ( `AnalyzerName` 예:, 및)입니다 `DataType` `DataSourceType` . 해당 `Create` 메서드도 제거 되었습니다. `Create`이러한 형식은 문자열에서 암시적으로 변환할 수 있기 때문에에 대 한 호출을 제거 하면 됩니다. 이로 인해 컴파일러 오류가 발생 하는 경우 캐스트를 통해 변환 연산자를 명시적으로 호출 하 여 형식을 구분할 수 있습니다. 예를 들어 다음과 같은 코드를 변경할 수 있습니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

아래와 같이 변환합니다.

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

이러한 형식의 선택적 값을 보유 하는 속성은 이제 nullable로 명시적으로 형식화 되므로 선택적으로 계속 사용할 수 있습니다.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults 및 HitHighlights 제거 됨

`FacetResults`및 `HitHighlights` 클래스가 제거 되었습니다. 패싯 결과는 이제로 형식화 `IDictionary<string, IList<FacetResult>>` 되며는로 강조 표시 됩니다 `IDictionary<string, IList<string>>` . 이러한 변경으로 인해 발생 하는 빌드 오류를 신속 하 게 해결 하는 방법은 `using` 제거 된 형식을 사용 하는 각 파일의 맨 위에 별칭을 추가 하는 것입니다. 예를 들어:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>SynonymMap으로 변경 

`SynonymMap` 생성자에는 더 이상 `SynonymMapFormat`에 대한 `enum` 매개 변수가 없습니다. 이 열거형에는 하나의 값만 있으므로 중복되었습니다. 이로 인해 빌드 오류가 표시되면 `SynonymMapFormat` 매개 변수에 대한 참조만 제거하면 됩니다.

### <a name="miscellaneous-model-class-changes"></a>기타 모델 클래스 변경 내용

`AutocompleteMode`의 속성은 `AutocompleteParameters` 더 이상 null을 허용 하지 않습니다. 이 속성을에 할당 하는 코드가 있는 경우 해당 속성을 `null` 제거 하기만 하면 속성이 자동으로 기본값으로 초기화 됩니다.

생성자에 대 한 매개 변수 순서가 `IndexAction` 변경 되어 이제이 생성자가 자동으로 생성 되었습니다. 생성자를 사용 하는 대신 팩터리 메서드,, 등을 사용 하는 것이 좋습니다 `IndexAction.Upload` `IndexAction.Merge` .

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 8.0-preview에서 버전 9로 업그레이드 하는 경우이 기능은 아직 미리 보기 상태 이므로 고객 관리 키로 암호화가 제거 된 것을 알고 있어야 합니다. 특히 `EncryptionKey` 및의 속성이 `Index` `SynonymMap` 제거 되었습니다.

응용 프로그램에이 기능에 대 한 하드 종속성이 있는 경우 Azure Search .NET SDK 버전 9로 업그레이드할 수 없습니다. 버전 8.0-미리 보기를 계속 사용할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

> [!NOTE]
> SDK 버전 8.0-preview를 사용 하 여 암호화 된 인덱스나 동의어 맵을 만든 경우에도 해당 암호화 상태에 부정적인 영향을 주지 않고 SDK 버전 9를 사용 하 여이를 사용 하 고 해당 정의를 수정할 수 있습니다. SDK 버전 9는 속성을 REST API 전송 하지 않으며 `encryptionKey` , 그 결과 REST API는 리소스의 암호화 상태를 변경 하지 않습니다. 

### <a name="behavioral-change-in-data-retrieval"></a>데이터 검색의 동작 변경

형식의 인스턴스를 반환 하는 "동적으로 형식화 된", 또는 api를 사용 하는 경우 `Search` `Suggest` 이제는 `Get` `Document` 대신 빈 JSON 배열을로 deserialize `object[]` `string[]` 합니다.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생 하는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)에 대 한 도움을 요청 하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!