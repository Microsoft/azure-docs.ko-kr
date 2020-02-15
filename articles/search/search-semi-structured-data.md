---
title: '자습서: JSON blob의 반 strutured 데이터 인덱싱'
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API 및 Postman을 사용하여 반정형 Azure JSON Blob을 인덱싱하고 검색하는 방법을 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251994"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>REST 자습서: Azure에서 반 구조화 된 데이터 (JSON blob) 인덱싱 및 검색 Cognitive Search

Azure Cognitive Search는 반정형 데이터를 읽는 방법을 아는 [indexer](search-indexer-overview.md)를 사용하여 Azure Blob Storage의 JSON 문서와 어레이를 인덱싱할 수 있습니다. 반구조화된 데이터에는 데이터 내의 콘텐츠를 구분하는 태그 또는 표시가 포함되어 있습니다. 완전히 인덱싱해야 하는 비정형 데이터와 필드 단위를 기반으로 인덱싱할 수 있는 데이터 모델(예: 관계형 데이터베이스 스키마)을 준수하는 형식적 비정형 데이터의 차이를 구분합니다.

이 자습서에서는 다음 작업을 수행하는 [Azure Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/) 및 REST 클라이언트를 사용합니다.

> [!div class="checklist"]
> * Azure Blob 컨테이너에 대한 Azure Cognitive Search 데이터 원본 구성
> * 검색 가능한 콘텐츠를 포함하는 Azure Cognitive Search 인덱스 만들기
> * 컨테이너를 읽고 Azure Blob Storage에서 검색 가능한 콘텐츠를 추출하기 위한 인덱서 구성 및 실행
> * 방금 만든 인덱스 검색

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에서 사용되는 서비스, 도구 및 데이터는 다음과 같습니다. 

[Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 

샘플 데이터를 저장하기 위한 [Azure 스토리지 계정을 만듭니다](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

요청을 Azure Cognitive Search에 보내는 [Postman 데스크톱 앱](https://www.getpostman.com/)을 가져옵니다.

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)에는 이 자습서에서 사용되는 데이터가 포함되어 있습니다. 다운로드하고 해당 폴더에 이 파일의 압축을 풉니다. [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)의 데이터는 이 자습서에서 사용하기 위해 JSON으로 변환됩니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="prepare-sample-data"></a>샘플 데이터 준비

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Storage 계정으로 이동한 후 **Blobs**를 클릭하고 **+ 컨테이너**를 클릭합니다.

1. 샘플 데이터가 포함되도록 [Blob 컨테이너를 만듭니다](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다.

   ![명령 모음에서 업로드](media/search-semi-structured-data/upload-command-bar.png "명령 모음에서 업로드")

1. 샘플 파일이 포함된 폴더로 이동합니다. 모두 선택한 다음, **업로드**를 클릭합니다.

   ![파일 업로드](media/search-semi-structured-data/clinicalupload.png "파일 업로드")

업로드가 완료되면 파일이 데이터 컨테이너의 하위 폴더에 나타납니다.

## <a name="set-up-postman"></a>Postman 설정

Postman을 시작하고 HTTP 요청을 설정합니다. 이 도구가 생소한 경우 [Postman을 사용하여 Azure Cognitive Search REST API 살펴보기](search-get-started-postman.md)를 참조하세요.

이 자습서의 모든 호출에 대한 요청 메서드는 **POST**입니다. 헤더 키는 "Content-type" 및 "api-key"입니다. 헤더 키의 값은 각각 "application/json"과 "관리자 키"(관리자 키는 검색 기본 키의 자리 표시자임)입니다. 본문은 호출의 실제 콘텐츠가 배치되는 위치입니다. 사용 중인 클라이언트에 따라 쿼리를 구성하는 방법에 약간의 차이가 있을 수 있지만 일반적으로 기본 사항입니다.

  ![반구조화된 검색](media/search-semi-structured-data/postmanoverview.png)

Postman을 통해 데이터 원본, 인덱스 및 인덱서를 만들기 위해 검색 서비스에 대한 API 호출을 세 번 수행합니다. 데이터 원본은 스토리지 계정 및 JSON 데이터에 대한 포인터를 포함합니다. 검색 서비스는 데이터를 로드할 때 연결합니다.

쿼리 문자열은 api-version을 지정해야 하며, 각 호출에서 **201 생성됨**을 반환해야 합니다. JSON 배열을 사용할 수 있도록 일반 공급되는 api-version은 `2019-05-06`입니다.

REST 클라이언트에서 다음 세 가지 API 호출을 실행합니다.

## <a name="create-a-data-source"></a>데이터 소스 만들기

[데이터 원본 만들기 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 는 인덱싱할 데이터를 지정 하는 Azure Cognitive Search 개체를 만듭니다.

이 호출의 엔드포인트는 `https://[service name].search.windows.net/datasources?api-version=2019-05-06`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다. 

이 호출의 경우 요청 본문에 스토리지 계정 이름, 스토리지 계정 키 및 blob 컨테이너 이름을 포함해야 합니다. 스토리지 계정 키는 Azure Portal에서 스토리지 계정의 **액세스 키** 내에서 찾을 수 있습니다. 위치가 다음 그림에 표시됩니다.

  ![반구조화된 검색](media/search-semi-structured-data/storagekeys.png)

호출 본문에서 `[storage account name]`, `[storage account key]` 및 `[blob container name]`을 바꾼 다음, 호출을 실행해야 합니다.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>인덱스 만들기
    
두 번째 호출은 [인덱스 API 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)이며 검색 가능한 모든 데이터를 저장하는 Azure Cognitive Search 인덱스를 만듭니다. 인덱스는 모든 매개 변수 및 해당 특성을 지정합니다.

이 호출에 대한 URL은 `https://[service name].search.windows.net/indexes?api-version=2019-05-06`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

먼저 URL을 바꿉니다. 그런 후 다음 코드를 복사하여 본문에 붙여넣고 쿼리를 실행합니다.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>인덱서 만들기 및 실행

인덱서는 데이터 원본을 연결하고, 데이터를 대상 검색 인덱스로 가져오고, 필요에 따라 데이터 새로 고침을 자동화하는 일정을 제공합니다. REST API는 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)입니다.

이 호출에 대한 URL은 `https://[service name].search.windows.net/indexers?api-version=2019-05-06`입니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

먼저 URL을 바꿉니다. 그런 다음, 다음 코드를 복사하여 본문에 붙여넣고 요청을 전송합니다. 요청은 즉시 처리됩니다. 응답이 돌아오면 전체 텍스트 검색이 가능한 인덱스가 생깁니다.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

응답은 다음과 같아야 합니다.

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>JSON 파일 검색

첫 번째 문서를 로드하는 즉시 검색을 시작할 수 있습니다. 이 작업의 경우 포털에서 [**검색 탐색기**](search-explorer.md)를 사용합니다.

Azure Portal에서 검색 서비스 **개요** 페이지를 열고 **인덱스** 목록에서 만든 인덱스를 찾습니다.

방금 만든 인덱스를 선택했는지 확인합니다. 

  ![구조화되지 않은 검색](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>사용자 정의 메타데이터 검색

이전과 마찬가지로 전체 텍스트 검색, 시스템 속성 또는 사용자 정의 메타데이터 등 여러 가지 방법으로 데이터를 쿼리할 수 ​​있습니다. 시스템 속성 및 사용자 정의 메타데이터는 대상 인덱스를 만드는 동안 `$select`retrievable**로 표시되어 있는 경우에만**  매개 변수로 검색할 수 있습니다. 인덱스의 매개 변수를 만든 후에는 변경할 수 없습니다. 그러나 매개 변수를 더 추가할 수는 있습니다.

기본 쿼리의 예로 `$select=Gender,metadata_storage_size`를 들 수 있으며 이는 반환 값을 두 매개 변수로 제한합니다.

  ![반구조화된 검색](media/search-semi-structured-data/lastquery.png)

보다 복잡한 쿼리의 예로 `$filter=MinimumAge ge 30 and MaximumAge lt 75`를 들 수 있으며 이는 매개 변수 MinimumAge가 30 이상이고 MaximumAge가 75 미만인 결과만 반환합니다.

  ![반구조화된 검색](media/search-semi-structured-data/metadatashort.png)

직접 몇 가지 쿼리를 더 실험해 보고 싶다면 자유롭게 수행해 봅니다. 논리 연산자(and, or) 및 비교 연산자(eq, ne, gt, lt, ge, le)를 사용할 수 있습니다. 문자열 비교는 대/소문자를 구분합니다.

`$filter` 매개 변수는 인덱스를 만들 때 필터링 가능으로 표시된 메타데이터에서만 작동합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서를 마친 후 정리하는 가장 빠른 방법은 Azure Cognitive Search 서비스를 포함하고 있는 리소스 그룹을 삭제하는 것입니다. 리소스 그룹을 삭제하여 이제 리소스 그룹 내의 모든 항목을 영구 삭제할 수 있습니다. 포털에서 리소스 그룹 이름은 Azure Cognitive Search 서비스의 개요 페이지에 있습니다.

## <a name="next-steps"></a>다음 단계

JSON Blob을 인덱싱하는 데 사용할 수 있는 방법 및 옵션에는 여러 가지가 있습니다. 다음 단계로 다양한 옵션을 검토하고 테스트하여 자신의 시나리오에 가장 적합한 옵션을 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cognitive Search Blob 인덱서를 사용하여 JSON Blob을 인덱싱하는 방법](search-howto-index-json-blobs.md)
