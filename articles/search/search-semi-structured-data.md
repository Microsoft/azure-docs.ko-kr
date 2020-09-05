---
title: '자습서: JSON Blob의 반정형 데이터 인덱싱'
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search REST API 및 Postman을 사용하여 반정형 Azure JSON Blob을 인덱싱하고 검색하는 방법을 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 8a615dc02b78993a18a86def9d8f496ba0bba922
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929706"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>자습서: REST를 사용하여 Azure Storage에서 JSON Blob 인덱싱

Azure Cognitive Search는 반정형 데이터를 읽는 방법을 아는 [indexer](search-indexer-overview.md)를 사용하여 Azure Blob Storage의 JSON 문서와 어레이를 인덱싱할 수 있습니다. 반구조화된 데이터에는 데이터 내의 콘텐츠를 구분하는 태그 또는 표시가 포함되어 있습니다. 완전히 인덱싱해야 하는 비정형 데이터와 필드 단위를 기반으로 인덱싱할 수 있는 데이터 모델(예: 관계형 데이터베이스 스키마)을 준수하는 형식적 비정형 데이터의 차이를 구분합니다.

이 자습서에서는 Postman 및 [Search REST API](/rest/api/searchservice/)를 사용하여 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Azure Blob 컨테이너에 대한 Azure Cognitive Search 데이터 원본 구성
> * 검색 가능한 콘텐츠를 포함하는 Azure Cognitive Search 인덱스 만들기
> * 컨테이너를 읽고 Azure Blob Storage에서 검색 가능한 콘텐츠를 추출하기 위한 인덱서 구성 및 실행
> * 방금 만든 인덱스 검색

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Postman 데스크톱 앱](https://www.getpostman.com/)
+ [만들기](search-create-service-portal.md) 또는 [기존 검색 서비스 찾기](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 이 자습서에서는 체험 서비스를 사용할 수 있습니다. 체험 검색 서비스에서는 인덱스, 인덱서 및 데이터 원본이 각각 3개로 제한됩니다. 이 자습서에서는 각각을 하나씩 만듭니다. 시작하기 전에 새 리소스를 수용할 수 있는 공간이 서비스에 있는지 확인하세요.

## <a name="download-files"></a>파일 다운로드

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip)에는 이 자습서에서 사용되는 데이터가 포함되어 있습니다. 다운로드하고 해당 폴더에 이 파일의 압축을 풉니다. [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)의 데이터는 이 자습서에서 사용하기 위해 JSON으로 변환됩니다.

## <a name="1---create-services"></a>1 - 서비스 만들기

이 자습서에서는 Azure Cognitive Search를 인덱싱 및 쿼리에 사용하고 Azure Blob Storage를 사용하여 데이터를 제공합니다. 

가능하면, 근접성과 관리 효율성을 위해 두 가지 모두를 동일한 지역과 리소스 그룹에 만듭니다. 실제로 Azure Storage 계정은 모든 지역에 있을 수 있습니다.

### <a name="start-with-azure-storage"></a>Azure Storage 시작

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, **+ 리소스 만들기**를 클릭합니다.

1. *스토리지 계정*을 검색하고, Microsoft의 스토리지 계정 제품을 선택합니다.

   ![스토리지 계정 만들기](media/cognitive-search-tutorial-blob/storage-account.png "Storage 계정 만들기")

1. [기본 사항] 탭의 필수 항목은 다음과 같습니다. 다른 모든 항목에는 기본값을 적용합니다.

   + **리소스 그룹**. 기존 서비스를 선택하거나 새 서비스를 만들지만 동일한 그룹을 모든 서비스에 사용하여 전체적으로 관리할 수 있습니다.

   + **스토리지 계정 이름**. 동일한 유형의 여러 리소스가 있을 수 있다고 생각되면 유형 및 지역별로 구분할 수 있는 이름(예: *blobstoragewestus*)을 사용합니다. 

   + **위치** - 가능하면 Azure Cognitive Search 및 Cognitive Services에 사용되는 것과 동일한 위치를 선택합니다. 단일 위치에는 대역폭 요금이 부과되지 않습니다.

   + **계정 종류**. 기본값인 *StorageV2(범용 v2)* 를 선택합니다.

1. **검토 + 만들기**를 클릭하여 서비스를 만듭니다.

1. 서비스가 만들어지면 **리소스로 이동**을 클릭하여 [개요] 페이지를 엽니다.

1. **Blob** 서비스를 클릭합니다.

1. 샘플 데이터가 포함되도록 [Blob 컨테이너를 만듭니다](../storage/blobs/storage-quickstart-blobs-portal.md). 유효한 값에 대한 공용 액세스 수준을 설정할 수 있습니다.

1. 컨테이너를 만들었으면 연 다음, 명령 모음에서 **업로드**를 선택합니다.

   ![명령 모음에서 업로드](media/search-semi-structured-data/upload-command-bar.png "명령 모음에서 업로드")

1. 샘플 파일이 포함된 폴더로 이동합니다. 모두 선택한 다음, **업로드**를 클릭합니다.

   ![파일 업로드](media/search-semi-structured-data/clinicalupload.png "파일 업로드")

업로드가 완료되면 파일이 데이터 컨테이너의 하위 폴더에 나타납니다.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

다음 리소스는 [포털에서 만들](search-create-service-portal.md) 수 있는 Azure Cognitive Search입니다. 이 연습은 체험 계층을 사용하여 완료할 수 있습니다. 

Azure Blob 스토리지와 마찬가지로 잠시 시간을 내어 액세스 키를 수집합니다. 또한 요청을 구조화하기 시작할 때 각 요청을 인증하는 데 사용되는 엔드포인트 및 관리 API 키를 제공해야 합니다.

### <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="2---set-up-postman"></a>2 - Postman 설정

Postman을 시작하고 HTTP 요청을 설정합니다. 이 도구가 생소한 경우 [Postman을 사용하여 Azure Cognitive Search REST API 살펴보기](search-get-started-postman.md)를 참조하세요.

이 자습서의 모든 호출에 대한 요청 메서드는 **POST**와 **GET**입니다. 검색 서비스에 대해 API 호출을 세 번 수행하여 데이터 원본, 인덱스 및 인덱서를 만듭니다. 데이터 원본은 스토리지 계정 및 JSON 데이터에 대한 포인터를 포함합니다. 검색 서비스는 데이터를 로드할 때 연결합니다.

[헤더]에서 "Content-type"을 `application/json`으로 설정하고, `api-key`를 Azure Cognitive Search 서비스의 관리 API 키로 설정합니다. 헤더가 설정되면 이 연습의 모든 요청에 헤더를 사용할 수 있습니다.

  ![Postman 요청 URL 및 헤더](media/search-get-started-postman/postman-url.png "Postman 요청 URL 및 헤더")

URI는 api-version을 지정해야 하며, 각 호출은 **201 생성됨**을 반환해야 합니다. JSON 배열을 사용할 수 있도록 일반 공급되는 api-version은 `2020-06-30`입니다.

## <a name="3---create-a-data-source"></a>3 - 데이터 원본 만들기

[데이터 원본 API 만들기](/rest/api/searchservice/create-data-source)는 인덱싱할 데이터를 지정하는 Azure Cognitive Search 개체를 만듭니다.

1. 이 호출의 엔드포인트를 `https://[service name].search.windows.net/datasources?api-version=2020-06-30`으로 설정합니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다. 

1. 다음 JSON을 요청 본문에 복사합니다.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. 연결 문자열을 계정에 유효한 문자열로 바꿉니다.

1. "[blob container name]"을 샘플 데이터 용으로 만든 컨테이너로 바꿉니다. 

1. 요청을 보냅니다. 응답은 다음과 같아야 합니다.

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - 인덱스 만들기
    
두 번째 호출은 [인덱스 API 만들기](/rest/api/searchservice/create-index)이며 검색 가능한 모든 데이터를 저장하는 Azure Cognitive Search 인덱스를 만듭니다. 인덱스는 모든 매개 변수 및 해당 특성을 지정합니다.

1. 이 호출의 엔드포인트를 `https://[service name].search.windows.net/indexes?api-version=2020-06-30`으로 설정합니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

1. 다음 JSON을 요청 본문에 복사합니다.

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
      ]
    }
   ```

1. 요청을 보냅니다. 응답은 다음과 같아야 합니다.

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - 인덱서 만들기 및 실행

인덱서는 데이터 원본에 연결하고, 데이터를 대상 검색 인덱스로 가져오고, 필요에 따라 데이터 새로 고침을 자동화하는 일정을 제공합니다. REST API는 [인덱서 만들기](/rest/api/searchservice/create-indexer)입니다.

1. 이 호출에 대한 URI를 `https://[service name].search.windows.net/indexers?api-version=2020-06-30`으로 설정합니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

1. 다음 JSON을 요청 본문에 복사합니다.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. 요청을 보냅니다. 요청은 즉시 처리됩니다. 응답이 돌아오면 전체 텍스트 검색이 가능한 인덱스가 생깁니다. 응답은 다음과 같아야 합니다.

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - JSON 파일 검색

첫 번째 문서를 로드하는 즉시 검색을 시작할 수 있습니다.

1. 동사를 **GET**으로 변경합니다.

1. 이 호출에 대한 URI를 `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true`로 설정합니다. `[service name]`을 검색 서비스의 이름으로 바꿉니다.

1. 요청을 보냅니다. 이것은 인덱스에 검색 가능으로 표시된 모든 필드를 문서 수와 함께 반환하는 지정되지 않은 전체 텍스트 검색 쿼리입니다. 응답은 다음과 같아야 합니다.

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. `$select` 쿼리 매개 변수를 추가하여 결과를 더 적은 수의 필드로 제한합니다. `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`.  이 쿼리의 경우 문서 100개가 일치하지만 기본적으로 Azure Cognitive Search는 결과에 50개만 반환합니다.

   ![매개 변수가 있는 쿼리](media/search-semi-structured-data/lastquery.png "매개 변수가 있는 쿼리")

1. 보다 복잡한 쿼리에는 `$filter=MinimumAge ge 30 and MaximumAge lt 75`를 포함할 수 있으며 이 경우 매개 변수 MinimumAge가 30 이상이고 MaximumAge가 75 미만인 결과만 반환합니다. `$select` 식을 `$filter` 식으로 바꿉니다.

   ![반구조화된 검색](media/search-semi-structured-data/metadatashort.png)

논리 연산자(and, or, not) 및 비교 연산자(eq, ne, gt, lt, ge, le)를 사용할 수도 있습니다. 문자열 비교는 대/소문자를 구분합니다. 자세한 내용과 예제는 [간단한 쿼리 만들기](search-query-simple-examples.md)를 참조하세요.

> [!NOTE]
> `$filter` 매개 변수는 인덱스를 만들 때 필터링 가능으로 표시된 메타데이터에서만 작동합니다.

## <a name="reset-and-rerun"></a>다시 설정하고 다시 실행

개발의 초기 실험 단계에서 설계 반복에 대한 가장 실용적인 방법은 Azure Cognitive Search에서 개체를 삭제하고 코드에서 이를 다시 작성하도록 허용하는 것입니다. 리소스 이름은 고유합니다. 개체를 삭제하면 동일한 이름을 사용하여 개체를 다시 만들 수 있습니다.

포털을 사용하여 인덱스, 인덱서 및 데이터 원본을 삭제할 수 있습니다. 또는 **DELETE**를 사용하고 각 개체에 대한 URL을 제공합니다. 다음 명령은 인덱서를 삭제합니다.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

삭제 성공 시 상태 코드 204가 반환됩니다.

## <a name="clean-up-resources"></a>리소스 정리

사용자 고유의 구독에서 작업하는 경우 프로젝트의 끝에서 더 이상 필요하지 않은 리소스를 제거하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 [모든 리소스] 또는 [리소스 그룹] 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Blob 인덱싱의 기본 사항을 익혔으면 Azure Storage의 JSON Blob에 대한 인덱서 구성에 대해 자세히 살펴보겠습니다.

> [!div class="nextstepaction"]
> [JSON Blob 인덱싱 구성](search-howto-index-json-blobs.md)