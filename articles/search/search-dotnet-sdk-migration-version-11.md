---
title: .NET SDK 버전 11로 업그레이드
titleSuffix: Azure Cognitive Search
description: 이전 버전에서 Azure Cognitive Search .NET SDK 버전 11로 코드를 마이그레이션합니다. 새로운 기능과 필요한 코드 변경 내용을 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/20/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 5a72f864d16ff89636b77709e439dd04134b64b7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002728"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-11"></a>Azure Cognitive Search .NET SDK 버전 11로 업그레이드

버전 10.0 또는 이전 버전의 [.NET SDK](/dotnet/api/overview/azure/search)를 사용 하는 경우이 문서를 참조 하 여 버전 11로 업그레이드할 수 있습니다.

버전 11은 Azure SDK development 팀에서 릴리스된 완전히 다시 디자인 된 클라이언트 라이브러리입니다 (이전 버전은 Azure Cognitive Search development 팀에서 생성 됨). 라이브러리는 다른 Azure 클라이언트 라이브러리와의 일관성을 유지 하기 위해 다시 디자인 되었으며, [azure](/dotnet/api/azure.core) 에 대 한 종속성을 가져오고, [System.Text.Js](/dotnet/api/system.text.json)하 고, 일반적인 작업을 위한 친숙 한 접근 방식을 구현 합니다.

새 버전에서 확인할 수 있는 몇 가지 주요 차이점은 다음과 같습니다.

+ 여러 패키지가 아닌 패키지 및 라이브러리 하나
+ 새 패키지 이름입니다 ( `Azure.Search.Documents` 대신) `Microsoft.Azure.Search` .
+ 2 대신 세 개의 클라이언트: `SearchClient` , `SearchIndexClient` , `SearchIndexerClient`
+ 일부 작업을 단순화 하는 다양 한 Api 및 작은 구조적 차이로 인 한 명명의 차이점

> [!NOTE]
> .NET SDK 버전 11의 변경 내용에 대 한 자세한 내용은 [**변경 로그**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) 를 검토 하세요.

## <a name="package-and-library-consolidation"></a>패키지 및 라이브러리 통합

버전 11은 여러 패키지와 라이브러리를 하나로 통합 합니다. 마이그레이션 후에는 관리할 라이브러리 수를 줄일 수 있습니다.

+ [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)

+ [클라이언트 라이브러리에 대 한 API 참조](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)

## <a name="client-differences"></a>클라이언트 차이점

다음 표에서는 해당 하는 경우 두 버전 간의 클라이언트 라이브러리를 매핑합니다.

| 작업 범위 | V10 (Microsoft Azure Search &nbsp; ) | Azure.Search.Documents &nbsp; (v11) |
|---------------------|------------------------------|------------------------------|
| 쿼리에 사용 되 고 인덱스를 채우는 데 사용 되는 클라이언트입니다. | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) | [SearchClient](/dotnet/api/azure.search.documents.searchclient) |
| 인덱스, 분석기, 동의어 맵에 사용 되는 클라이언트 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) |
| 인덱서, 데이터 원본, 기술력과에 사용 되는 클라이언트 | [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) | [SearchIndexerClient (**신규**)](/dotnet/api/azure.search.documents.indexes.searchindexerclient) |

> [!Important]
> `SearchIndexClient` 는 두 버전에 모두 존재 하지만 다른 작업을 지원 합니다. 버전 10에서 `SearchIndexClient` 인덱스 및 기타 개체를 만듭니다. 버전 11에서는 `SearchIndexClient` 기존 인덱스와 함께 작동 합니다. 코드를 업데이트할 때 혼동을 피하려면 클라이언트 참조가 업데이트 되는 순서에 주의 해야 합니다. [업그레이드 단계](#UpgradeSteps) 에서 순서를 따라 문자열 대체 문제를 완화할 수 있습니다.

<a name="naming-differences"></a>

## <a name="naming-and-other-api-differences"></a>이름 지정 및 기타 API 차이점

이전에 언급 했 고 여기서 생략 된 클라이언트 차이점 외에도 여러 다른 Api의 이름이 변경 되었으며 경우에 따라 다시 디자인 되었습니다. 클래스 이름 차이는 아래에 요약 되어 있습니다. 이 목록은 포괄적이 지 않지만 특정 코드 블록에 대 한 수정 작업에 도움이 될 수 있는 작업에의 한 그룹 API 변경 작업을 수행 합니다. API 업데이트의 항목별 목록은 GitHub의에 대 한 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) 를 참조 `Azure.Search.Documents` 하세요.

### <a name="authentication-and-encryption"></a>인증 및 암호화

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [SearchCredentials](/dotnet/api/microsoft.azure.search.searchcredentials) | [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) |
| `EncryptionKey` ( [미리 보기 SDK](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) 에 일반 기능으로 제공 됨) | [SearchResourceEncryptionKey](/dotnet/api/azure.search.documents.indexes.models.searchresourceencryptionkey) |

### <a name="indexes-analyzers-synonym-maps"></a>인덱스, 분석기, 동의어 맵

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [Index](/dotnet/api/microsoft.azure.documents.index) | [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) |
| [필드](/dotnet/api/microsoft.azure.search.models.field) | [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) |
| [DataType](/dotnet/api/microsoft.azure.search.models.datatype) | [SearchFieldDataType](/dotnet/api/azure.search.documents.indexes.models.searchfielddatatype) |
| [ItemError](/dotnet/api/microsoft.azure.search.models.itemerror) | [SearchIndexerError](/dotnet/api/azure.search.documents.indexes.models.searchindexererror) |
| [분석기나](/dotnet/api/microsoft.azure.search.models.analyzer) | [LexicalAnalyzer](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzer) (도 `AnalyzerName` `LexicalAnalyzerName` ) |
| [AnalyzeRequest](/dotnet/api/microsoft.azure.search.models.analyzerequest) | [AnalyzeTextOptions](/dotnet/api/azure.search.documents.indexes.models.analyzetextoptions) |
| [StandardAnalyzer](/dotnet/api/microsoft.azure.search.models.standardanalyzer) | [LuceneStandardAnalyzer](//dotnet/api/azure.search.documents.indexes.models.lucenestandardanalyzer) |
| [StandardTokenizer](/dotnet/api/microsoft.azure.search.models.standardtokenizer) | [LuceneStandardTokenizer](/dotnet/api/azure.search.documents.indexes.models.lucenestandardtokenizer) (도 `StandardTokenizerV2` `LuceneStandardTokenizerV2` ) |
| [TokenInfo](/dotnet/api/microsoft.azure.search.models.tokeninfo) | [AnalyzedTokenInfo](/dotnet/api/azure.search.documents.indexes.models.analyzedtokeninfo) |
| [토크](/dotnet/api/microsoft.azure.search.models.tokenizer) | [LexicalTokenizer](/dotnet/api/azure.search.documents.indexes.models.lexicaltokenizer) (도 `TokenizerName` `LexicalTokenizerName` ) |
| [SynonymMap](/dotnet/api/microsoft.azure.search.models.synonymmap.format) | 없음 에 대 한 참조를 제거 `Format` 합니다. |

필드 정의는 간소화 됩니다. [Searchablefield](/dotnet/api/azure.search.documents.indexes.models.searchablefield), [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield), [complexfield](/dotnet/api/azure.search.documents.indexes.models.complexfield) 는 필드 정의를 만들기 위한 새로운 api입니다.

### <a name="indexers-datasources-skillsets"></a>인덱서, 데이터 원본, 기술력과

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [인덱서](/dotnet/api/microsoft.azure.search.models.indexer) | [SearchIndexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer) |
| [DataSource](/dotnet/api/microsoft.azure.search.models.datasource) | [SearchIndexerDataSourceConnection](//dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) |
| [레벨](/dotnet/api/microsoft.azure.search.models.skill) | [SearchIndexerSkill](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [기술 집합](/dotnet/api/microsoft.azure.search.models.skillset) | [SearchIndexerSkillset](/dotnet/api/azure.search.documents.indexes.models.searchindexerskill) |
| [DataSourceType](/dotnet/api/microsoft.azure.search.models.datasourcetype) | [SearchIndexerDataSourceType](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) |

### <a name="data-import"></a>데이터 가져오기

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [IndexAction](/dotnet/api/microsoft.azure.search.models.indexaction) | [Index문서 동작](/dotnet/api/azure.search.documents.models.indexdocumentsaction) |
| [IndexBatch](/dotnet/api/microsoft.azure.search.models.indexbatch) | [Index문서 일괄 처리](/dotnet/api/azure.search.documents.models.indexdocumentsbatch) |

### <a name="query-definitions-and-results"></a>쿼리 정의 및 결과

| 버전 10 | 버전 11 동급 |
|------------|-----------------------|
| [DocumentSearchResult](/dotnet/api/microsoft.azure.search.models.documentsearchresult-1) | [SearchResult](/dotnet/api/azure.search.documents.models.searchresult-1) 또는 [searchresults](/dotnet/api/azure.search.documents.models.searchresults-1)는 결과가 단일 문서 인지 또는 여러 인지에 따라 달라 집니다. |
| [DocumentSuggestResult](/dotnet/api/microsoft.azure.search.models.documentsuggestresult-1) | [SuggestResults](/dotnet/api/azure.search.documents.models.suggestresults-1) |
| [SearchParameters](/dotnet/api/microsoft.azure.search.models.searchparameters) |  [Searchoptions 플래그가](/dotnet/api/azure.search.documents.searchoptions)  |

<a name="WhatsNew"></a>

## <a name="whats-in-version-11"></a>버전 11의 기능

Azure Cognitive Search 클라이언트 라이브러리의 각 버전은 해당 하는 버전의 REST API를 대상으로 합니다. REST API은 서비스에 대 한 기본으로 간주 되며 개별 Sdk는 REST API 버전을 래핑합니다. .NET 개발자는 특정 개체 또는 작업에 대 한 더 많은 배경을 원하는 경우 [REST API 설명서](/rest/api/searchservice/) 를 검토 하는 것이 도움이 될 수 있습니다.

버전 11은 [2020-06-30 검색 서비스](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/search/data-plane/Azure.Search/preview/2020-06-30/searchservice.json)를 대상으로 합니다. 버전 11은 처음부터 새로 빌드된 클라이언트 라이브러리 이기도 하기 때문에 대부분의 개발 작업에는 버전 10과 동등 하 게 중점을 두고 있지만 일부 REST API 기능 지원이 보류 중입니다.

버전 11.0는 다음 개체 및 작업을 완벽 하 게 지원 합니다.

+ 인덱스 생성 및 관리
+ 동의어 맵 만들기 및 관리
+ 모든 쿼리 유형 및 구문 (지리적 공간 필터 제외)
+ 데이터 원본 및 기술력과를 포함 하 여 Azure 데이터 원본을 인덱싱하는 인덱서 개체 및 작업

버전 11.1에는 다음이 추가 되었습니다.

+ [Fieldbuilder](/dotnet/api/azure.search.documents.indexes.fieldbuilder) (11.1에 추가 됨)
+ 사용자 지정 serialization을 지원 하기 위해 [Serializer 속성](/dotnet/api/azure.search.documents.searchclientoptions.serializer) (11.1에 추가 됨)

### <a name="pending-features"></a>보류 중인 기능

다음 버전 10 기능은 버전 11에서 아직 사용할 수 없습니다. 이러한 기능이 필요한 경우 지원 될 때까지 마이그레이션을 보류 합니다.

+ 지리 공간적 형식
+ [지식 저장소](knowledge-store-concept-intro.md)

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>업그레이드 단계

다음 단계는 특히 클라이언트 참조와 관련 하 여 필요한 작업의 첫 번째 집합을 탐색 하 여 코드 마이그레이션을 시작 하는 방법을 알아봅니다.

1. 프로젝트 참조를 마우스 오른쪽 단추로 클릭 하 고 "NuGet 패키지 관리 ..."를 선택 하 여 [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/) 를 설치 합니다. Visual Studio에서

1. Microsoft. Azure에 대 한 using 지시문을 바꿉니다. 다음을 사용 하 여 검색 합니다.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. JSON serialization이 필요한 클래스의 경우을 `using Newtonsoft.Json` 로 바꿉니다 `using System.Text.Json.Serialization` .

1. 클라이언트 인증 코드를 수정 합니다. 이전 버전에서는 클라이언트 개체의 속성을 사용 하 여 API 키 (예: [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) 속성)를 설정 합니다. 현재 버전에서는 [Azurekeycredential](/dotnet/api/azure.azurekeycredential) 클래스를 사용 하 여 키를 자격 증명으로 전달 합니다. 따라서 필요한 경우 새 클라이언트 개체를 만들지 않고도 API 키를 업데이트할 수 있습니다.

   클라이언트 속성은 `Endpoint` , `ServiceName` 및 (해당 하는 경우)로 간소화 되었습니다 `IndexName` . 다음 예제에서는 시스템 [Uri](/dotnet/api/system.uri) 클래스를 사용 하 여 키 값에서 읽을 끝점 및 [환경](/dotnet/api/system.environment) 클래스를 제공 합니다.

   ```csharp
   Uri endpoint = new Uri(Environment.GetEnvironmentVariable("SEARCH_ENDPOINT"));
   AzureKeyCredential credential = new AzureKeyCredential(
      Environment.GetEnvironmentVariable("SEARCH_API_KEY"));
   SearchIndexClient indexClient = new SearchIndexClient(endpoint, credential);
   ```

1. 인덱서 관련 개체에 대 한 새 클라이언트 참조를 추가 합니다. 인덱서, 데이터 원본 또는 기술력과를 사용 하는 경우 [Searchindexerclient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)에 대 한 클라이언트 참조를 변경 합니다. 이 클라이언트는 버전 11에서 새로 되었으며 선행 작업이 없습니다.

1. 쿼리 및 데이터 가져오기에 대 한 클라이언트 참조를 업데이트 합니다. [Searchindexclient](/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스는 [searchclient](/dotnet/api/azure.search.documents.searchclient)로 변경 해야 합니다. 이름 혼동을 방지 하려면 다음 단계로 진행 하기 전에 모든 인스턴스를 catch 해야 합니다.

1. 인덱스, 인덱서, 동의어 맵 및 분석기 개체에 대 한 클라이언트 참조를 업데이트 합니다. [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) 인스턴스는 [searchindexclient](/dotnet/api/microsoft.azure.search.searchindexclient)로 변경 해야 합니다. 

1. 가능 하면 클래스, 메서드 및 속성을 업데이트 하 여 새 라이브러리의 Api를 사용 합니다. [명명 차이점](#naming-differences) 섹션은 시작할 장소 이지만 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md)를 검토할 수도 있습니다.

   동일한 Api를 찾는 데 문제가 있는 경우 [https://github.com/MicrosoftDocs/azure-docs/issues](https://github.com/MicrosoftDocs/azure-docs/issues) 설명서를 개선 하거나 문제를 조사할 수 있도록 문제를 로깅하는 것이 좋습니다.

1. 솔루션을 다시 빌드합니다. 빌드 오류 또는 경고를 수정한 후에는 응용 프로그램에 대 한 추가 변경을 수행 하 여 [새로운 기능](#WhatsNew)을 이용할 수 있습니다.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-11"></a>버전 11의 주요 변경 내용

라이브러리 및 Api에 대 한 변경 사항이 있는 경우 버전 11로 업그레이드 하는 것은 간단 하지 않으며 코드는 더 이상 이전 버전 10과 호환 되지 않는다는 점에서 주요 변경 사항을 구성 합니다. 차이점을 철저 하 게 검토 하려면의 [변경 로그](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/CHANGELOG.md) 를 참조 하세요 `Azure.Search.Documents` .

버전 11의 코드 변경이 기존 기능과 관련이 있고 (Api의 리팩터링이 아니라) 서비스 버전 업데이트를 사용 하는 경우 다음과 같은 동작이 변경 될 수 있습니다.

+ [BM25 순위 알고리즘](index-ranking-similarity.md) 은 이전 순위 알고리즘을 최신 기술로 대체 합니다. 새 서비스는이 알고리즘을 자동으로 사용 합니다. 기존 서비스의 경우 새 알고리즘을 사용 하도록 매개 변수를 설정 해야 합니다.

+ 이 버전에서 null 값에 대 한 [순서가 지정 된 결과가](search-query-odata-orderby.md) 변경 되었습니다. 정렬이 인 경우에는 null 값이 먼저 표시 되 고 정렬이 이면 `asc` 마지막으로 나타납니다 `desc` . Null 값을 정렬 하는 방법을 처리 하는 코드를 작성 한 경우 더 이상 필요 하지 않은 경우 해당 코드를 검토 하 고 잠재적으로 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure.Search.Documents 패키지](https://www.nuget.org/packages/Azure.Search.Documents/)
+ [GitHub 샘플](https://github.com/azure/azure-sdk-for-net/tree/Azure.Search.Documents_11.0.0/sdk/search/Azure.Search.Documents/samples)
+ [Azure.Search.Document API 참조](/dotnet/api/overview/azure/search.documents-readme?view=azure-dotnet)