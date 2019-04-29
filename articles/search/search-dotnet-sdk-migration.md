---
title: Azure Search .NET SDK 버전 3으로 업그레이드 - Azure Search
description: 이전 버전에서 Azure Search .NET SDK 버전 3으로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4acf609ca1f81e69babfa1a319b43e20e84a8395
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61291538"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Azure Search .NET SDK 버전 3으로 업그레이드
버전 2.0-preview 또는 이전 버전의 [Azure Search .NET SDK](https://aka.ms/search-sdk)를 사용하는 경우 이 문서를 통해 버전 3으로 애플리케이션을 업그레이드할 수 있습니다.

예제를 비롯하여 SDK에 대한 보다 일반적인 연습은 [.NET 애플리케이션에서 Azure Search를 사용하는 방법](search-howto-dotnet-sdk.md)을 참조하세요.

Azure Search .NET SDK 버전 3에는 이전 버전에서 변경된 사항이 일부 포함되어 있습니다. 대부분 소소한 변경이므로 코드를 변경하는 데 최소한의 작업만 필요합니다. 새 SDK 버전을 사용하는 코드를 변경하는 방법에 대한 지침은 [업그레이드 단계](#UpgradeSteps) 를 참조하세요.

> [!NOTE]
> 1.0.2-preview 또는 이전 버전을 사용하는 경우 먼저 버전 1.1로 업그레이드한 후 버전 3으로 업그레이드해야 합니다. 지침은 [Azure Search .NET SDK 버전 1.1로 업그레이드](search-dotnet-sdk-migration-version-1.md)를 참조하세요.
>
> Azure Search 서비스 인스턴스는 최신 버전을 포함한 여러 REST API 버전을 지원합니다. 더 이상 최신 버전이 아닌 버전을 계속 사용할 수는 있지만 코드를 마이그레이션하여 최신 버전을 사용하는 것이 좋습니다. REST API를 사용하는 경우 api-version 매개 변수를 통해 모든 요청에 API 버전을 지정해야 합니다. .NET SDK를 사용하는 경우 사용 중인 SDK의 버전에 따라 해당하는 REST API 버전이 결정됩니다. 이전 버전의 SDK를 사용하는 경우 최신 API 버전을 지원하기 위해 서비스가 업그레이드된 경우에도 변경 내용 없이 해당 코드를 계속 실행할 수 있습니다.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>버전 3의 새로운 기능
Azure Search .NET SDK 버전 3은 Azure Search REST API의 최신 일반 공급 버전, 특히 2016-09-01을 대상으로 합니다. 이 버전이 있으면 다음을 비롯한 많은 Azure Search의 새 기능을 .NET 애플리케이션에서 사용할 수 있습니다.

* [사용자 지정 분석기](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) 및 [Azure Table Storage](search-howto-indexing-azure-tables.md) 인덱서 지원
*  [필드 매핑](search-indexer-field-mappings.md)
* 인덱스 정의, 인덱서 및 데이터 원본의 안전한 동시 업데이트를 가능하게 하는 Etag 지원
* 모델 클래스를 데코레이트하고 새 `FieldBuilder` 클래스를 사용하여 인덱스 필드 정의를 선언적으로 지원
* .NET Core와 .NET 이식 가능 프로필 111에 대한 지원

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계
먼저, NuGet 패키지 관리자 콘솔을 사용하거나 Visual Studio에서 프로젝트 참조를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리..."를 선택하여 `Microsoft.Azure.Search` 에 대한 NuGet 참조를 업데이트합니다.

NuGet에서 새 패키지와 해당 종속성을 다운로드했으면 프로젝트를 다시 빌드합니다. 코드가 구조화된 방식에 따라 다시 빌드할 수 있습니다. 다시 빌드할 수 있으면 배포할 준비가 된 것입니다.

빌드가 실패하면 다음과 같은 빌드 오류가 표시됩니다.

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

다음은 이러한 빌드 오류를 수정하는 단계입니다. 오류의 원인 및 해결 방법에 대한 자세한 내용은 [버전 3의 주요 변경 내용](#ListOfChanges)을 참조하세요.

사용되지 않은 메서드 또는 속성과 관련된 추가 빌드 경고가 표시될 수 있습니다. 경고에는 사용되지 않는 기능 대신 사용해야 하는 항목에 대한 지침이 포함됩니다. 예를 들어 애플리케이션이 `IndexingParameters.Base64EncodeKeys` 속성을 사용하는 경우 `"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`와 같은 경고가 표시됩니다.

모든 빌드 오류를 수정했다면 원하는 새 기능을 활용하도록 애플리케이션을 변경할 수 있습니다. SDK의 새로운 기능에 대한 자세한 내용은 [버전 3의 새로운 기능](#WhatsNew)에 나와 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>버전 3의 주요 변경 내용
애플리케이션을 다시 빌드하는 것 외에도, 버전 3에는 코드 변경이 필요할 수 있는 몇 가지 주요 변경 내용이 있습니다.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient 반환 형식
`Indexes.GetClient` 메서드에는 새 반환 형식이 있습니다. 이전에는 `SearchIndexClient`를 반환했으나 버전 2.0-preview에서는 `ISearchIndexClient`로 변경되었으며 이 변경 내용이 버전 3에도 제공됩니다. 따라서 `ISearchIndexClient`의 모의 구현을 반환하여 단위 테스트를 위한 `GetClient` 메서드를 모의할 수 있습니다.

#### <a name="example"></a>예
코드는 다음과 같습니다.

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName, DataType 등은 더 이상 암시적으로 문자열로 변환될 수 없습니다.
Azure Search .NET SDK에는 `ExtensibleEnum`에서 파생된 여러 형식이 있습니다. 이전에는 이러한 형식이 모두 `string` 형식으로 암시적으로 변환될 수 있었습니다. 그러나 이러한 클래스의 `Object.Equals` 구현에서 버그가 발견되었으며 버그 수정을 위해 이러한 암시적 변환을 사용하지 않도록 설정해야 했습니다. `string`로의 명시적 변환은 여전히 허용됩니다.

#### <a name="example"></a>예
코드는 다음과 같습니다.

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

빌드 오류를 수정하기 위해 다음으로 변경할 수 있습니다.

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>더 이상 사용되지 않는 멤버가 제거되었습니다.

버전 2.0-preview에서 사용되지 않는 것으로 표시되고 이후에 버전 3에서 제거된 메서드 및 속성 관련 빌드 오류가 확인될 수 있습니다. 이러한 오류가 발생하는 경우 해결 방법은 다음과 같습니다.

- `ScoringParameter(string name, string value)` 구문을 사용한 경우 `ScoringParameter(string name, IEnumerable<string> values)`을 대신 사용합니다.
- `ScoringParameter.Value` 속성을 사용한 경우 `ScoringParameter.Values` 속성 또는 `ToString` 메서드를 대신 사용합니다.
- `SearchRequestOptions.RequestId` 속성을 사용한 경우 `ClientRequestId` 속성을 대신 사용합니다.

### <a name="removed-preview-features"></a>제거된 미리 보기 기능

버전 2.0-preview에서 버전 3으로 업그레이드하는 경우 Blob 인덱서의 JSON 및 CSV 구문 분석 지원은 미리 보기에는 계속 포함되지만 버전 3에서는 제거됩니다. 특히, `IndexingParametersExtensions` 클래스의 다음 메서드가 제거되었습니다.

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

애플리케이션이 이러한 기능에 강력하게 종속될 경우 Azure Search .NET SDK 버전 3으로 업그레이드하지 못할 수 있습니다. 버전 2.0-preview는 계속 사용할 수 있습니다. 하지만 **프로덕션 애플리케이션에서는 미리 보기 SDK를 사용하지 않는 것이 좋습니다**. 미리 보기 기능은 평가용일 뿐이며 변경될 수 있습니다.

## <a name="conclusion"></a>결론
Azure Search .NET SDK 사용에 대한 자세한 내용은 [.NET 방법](search-howto-dotnet-sdk.md)을 참조하세요.

SDK에 대한 귀하의 피드백을 환영합니다! 문제가 발생하면 [Azure Search MSDN 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)을 통해 자유롭게 도움을 요청하세요. 버그를 발견하는 경우 [Azure .NET SDK GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/issues)에 문제를 제출할 수 있습니다. 문제 제목에 "[Azure Search]"라는 접두사를 지정해야 합니다.

Azure Search를 이용해 주셔서 감사합니다!
