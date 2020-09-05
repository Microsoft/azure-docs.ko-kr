---
title: REST를 사용하여 지식 저장소 만들기
titleSuffix: Azure Cognitive Search
description: REST API 및 Postman을 사용하여 AI 보강 파이프라인의 보강을 유지하기 위한 Azure Cognitive Search 지식 저장소를 만듭니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 08/02/2020
ms.openlocfilehash: 1745a2bf83cb704c8cc73e9d3bf0eba8245329b3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924268"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>REST 및 Postman을 사용하여 지식 저장소 만들기

지식 저장소에는 추후 분석 또는 기타 다운스트림 처리를 위해 Azure Cognitive Search 보강 파이프라인의 출력이 포함됩니다. AI 보강 파이프라인은 이미지 파일 또는 비정형 텍스트 파일을 수락하고, Azure Cognitive Search를 사용하여 인덱싱하고, Cognitive Services의 AI 보강(예: 이미지 분석 및 자연어 처리)을 적용한 다음, 결과를 Azure Storage의 지식 저장소에 저장합니다. Azure Portal에서 Power BI 또는 Storage Explorer와 같은 도구를 사용하여 지식 저장소를 검색할 수 있습니다.

이 문서에서는 REST API 인터페이스를 사용하여 AI 보강을 호텔 리뷰 세트에 수집, 인덱싱 및 적용합니다. 호텔 리뷰는 Azure Blob 스토리지로 가져오고, 결과는 지식 저장소로 Azure Table 스토리지에 저장됩니다.

지식 저장소가 만들어지면 [Storage Explorer](knowledge-store-view-storage-explorer.md) 또는 [Power BI](knowledge-store-connect-power-bi.md)를 사용하여 이 지식 저장소에 액세스하는 방법을 알아볼 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!TIP]
> 이 문서에서는 [Postman 데스크톱 앱](https://www.getpostman.com/)을 권장합니다. 이 문서의 [소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)에는 모든 요청이 포함된 Postman 컬렉션이 포함되어 있습니다. 

## <a name="create-services-and-load-data"></a>서비스 만들기 및 데이터 로드

이 빠른 시작에서는 Azure Cognitive Search, Azure Blob 스토리지 및 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 AI에 사용합니다. 

워크로드가 너무 작으므로 매일 최대 20개의 트랜잭션을 무료로 제공하기 위해 백그라운드에 탭으로 처리됩니다. 데이터 세트가 너무 작으므로 Cognitive Services 리소스 만들기 또는 연결을 건너뛸 수 있습니다.

1. [HotelReviews_Free.csv를 다운로드합니다](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). 이 데이터는 CSV 파일로 저장된 호텔 리뷰 데이터이며(Kaggle.com에서 가져온 데이터) 단일 호텔에 대한 19개 고객 피드백을 포함하고 있습니다. 

1. [Azure 스토리지 계정을 만들거나](../storage/common/storage-account-create.md?tabs=azure-portal) 현재 구독에서 [기존 계정을 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 가져올 원시 콘텐츠와 최종 결과인 지식 저장소 둘 모두에 Azure 스토리지를 사용할 것입니다.

   **StorageV2(범용 V2)** 계정 유형을 선택합니다.

1. Blob 서비스 페이지를 열고 *hotel-reviews*라는 컨테이너를 만듭니다.

1. **업로드**를 클릭합니다.

    ![데이터 업로드](media/knowledge-store-create-portal/upload-command-bar.png "호텔 리뷰 업로드")

1. 첫 번째 단계에서 다운로드한 **HotelReviews-Free.csv** 파일을 선택합니다.

    ![Azure Blob 컨테이너 만들기](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob 컨테이너 만들기")

1. 이 리소스가 거의 완료되었지만, 이 페이지를 나가기 전에 왼쪽 탐색 창의 링크를 사용하여 **액세스 키** 페이지를 엽니다. Blob 스토리지에서 데이터를 검색할 연결 문자열을 가져옵니다. 연결 문자열은 `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` 예제와 비슷한 현식입니다.

1. 여전히 포털에서 Azure Cognitive Search로 전환합니다. [새 서비스를 만들](search-create-service-portal.md)거나 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 연습에서는 체험 서비스를 사용할 수 있습니다.

## <a name="configure-postman"></a>Postman 구성

Postman을 설치하고 설정합니다.

### <a name="download-and-install-postman"></a>Postman 다운로드 및 설치

1. [Postman 컬렉션 소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)를 다운로드합니다.
1. **파일** > **가져오기**를 차례로 선택하여 소스 코드를 Postman으로 가져옵니다.
1. **컬렉션** 탭, **...** (줄임표) 단추를 차례로 선택합니다.
1. **편집**을 선택합니다. 
   
   ![탐색 결과를 표시하는 Postman 앱](media/knowledge-store-create-rest/postman-edit-menu.png "Postman의 [편집] 메뉴로 이동")
1. **편집** 대화 상자에서 **변수** 탭을 선택합니다. 

**변수** 탭에서 이중 중괄호 안에 특정 변수가 있을 때마다 Postman에서 교환하는 값을 추가할 수 있습니다. 예를 들어 Postman에서 `{{admin-key}}` 기호를 `admin-key`에 설정한 현재 값으로 바꿉니다. Postman은 URL, 헤더, 요청 본문 등에서 이 대체를 수행합니다. 

`admin-key`에 대한 값을 가져오려면 Azure Cognitive Search 서비스로 이동하여 **키** 탭을 선택합니다. `search-service-name` 및 `storage-account-name`을 [서비스 만들기](#create-services-and-load-data)에서 선택한 값으로 변경합니다. 스토리지 계정의 **액세스 키** 탭에 있는 값을 사용하여 `storage-connection-string`을 설정합니다. 다른 값에는 기본값을 그대로 둘 수 있습니다.

![Postman 앱 변수 탭](media/knowledge-store-create-rest/postman-variables-window.png "Postman의 변수 창")


| 변수    | 가져올 수 있는 위치 |
|-------------|-----------------|
| `admin-key` | Azure Cognitive Search 서비스의 **키** 페이지.  |
| `api-version` | **2020-06-30**으로 둡니다. |
| `datasource-name` | **hotel-reviews-ds**로 유지 | 
| `indexer-name` | **hotel-reviews-ixr**로 유지 | 
| `index-name` | **hotel-reviews-ix**로 유지 | 
| `search-service-name` | Azure Cognitive Search 서비스의 이름. URL은 `https://{{search-service-name}}.search.windows.net`입니다. | 
| `skillset-name` | **hotel-reviews-ss**로 유지 | 
| `storage-account-name` | 스토리지 계정 이름입니다. | 
| `storage-connection-string` | 스토리지 계정의 **액세스 키** 탭에서 **key1** > **연결 문자열**을 차례로 선택합니다. | 
| `storage-container-name` | **hotel-reviews**로 유지 | 

### <a name="review-the-request-collection-in-postman"></a>Postman에서 요청 컬렉션 검토

지식 저장소를 만드는 경우 다음 네 가지 HTTP 요청을 발급해야 합니다. 

- **인덱스를 만들기 위한 PUT 요청**: 이 인덱스는 Azure Cognitive Search에서 사용하고 반환하는 데이터를 포함합니다.
- **데이터 원본을 만들기 위한 POST 요청**: 이 데이터 원본은 Azure Cognitive Search 동작을 데이터 및 지식 저장소의 스토리지 계정에 연결합니다. 
- **기술 세트를 만들기 위한 PUT 요청**: 이 기술 세트는 데이터 및 지식 저장소의 구조에 적용되는 보강을 지정합니다.
- **인덱서를 만들기 위한 PUT 요청**: 인덱서를 실행하면 데이터를 읽고, 기술 세트를 적용하고, 결과를 저장합니다. 이 요청은 마지막으로 실행해야 합니다.

[소스 코드](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)에는 이러한 4개의 요청이 있는 Postman 컬렉션이 있습니다. 요청을 발급하려면 Postman에서 해당 요청에 대한 탭을 선택합니다. 그런 다음, `api-key` 및 `Content-Type` 요청 헤더를 추가합니다. `api-key` 값을 `{{admin-key}}`로 설정합니다. `Content-type` 값을 `application/json`으로 설정합니다. 

![헤더에 대한 Postman의 인터페이스를 보여 주는 스크린샷](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> `api-key` 및 `Content-type` 헤더는 모든 요청에서 설정해야 합니다. Postman에서 변수를 인식하면 이전 스크린샷의 `{{admin-key}}`와 같이 변수가 주황색 텍스트로 표시됩니다. 변수의 철자가 틀리면 빨간색 텍스트로 표시됩니다.
>

## <a name="create-an-azure-cognitive-search-index"></a>Azure Cognitive Search 인덱스 만들기

향상된 기능을 검색, 필터링 및 적용하는 데 관심이 있는 데이터를 나타내는 Azure Cognitive Search 인덱스를 만듭니다. `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`에 대한 PUT 요청을 발급하여 인덱스를 만듭니다. Postman에서 이중 중괄호로 묶인 기호(예: `{{search-service-name}}`, `{{index-name}}` 및 `{{api-version}}`)를 [Postman 구성](#configure-postman)에서 설정한 값으로 바꿉니다. 다른 도구를 사용하여 REST 명령을 실행하는 경우 이러한 변수를 직접 바꿔야 합니다.

Azure Cognitive Search 인덱스의 구조를 요청 본문에 설정합니다. `api-key` 및 `Content-type` 헤더가 설정되면 Postman에서 요청의 **본문** 창으로 이동합니다. 다음 JSON이 표시됩니다. 그렇지 않으면 **원시** > **JSON(애플리케이션/json)** 을 차례로 선택하고, 다음 코드를 본문으로 붙여넣습니다.

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

이 인덱스 정의는 사용자에게 제공하려는 데이터(호텔 이름, 리뷰 내용, 날짜), 검색 메타데이터 및 AI 보강 데이터(Sentiment, Keyphrases 및 Language)의 조합입니다.

**보내기**를 선택하여 PUT 요청을 발급합니다. `201 - Created` 상태가 표시됩니다. 다른 상태가 표시되면 **본문** 창에서 오류 메시지가 포함된 JSON 응답을 찾습니다. 

## <a name="create-the-datasource"></a>데이터 원본 만들기

다음으로, Azure Cognitive Search를 Blob 스토리지에 저장된 호텔 데이터에 연결합니다. 데이터 원본을 만들려면 POST 요청을 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`에 보냅니다. 앞에서 설명한 대로 `api-key` 및 `Content-Type` 헤더를 설정해야 합니다. 

Postman에서 **데이터 원본 만들기** 요청, **본문** 창으로 차례로 이동합니다. 다음 코드가 표시됩니다.

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

**보내기**를 선택하여 POST 요청을 발급합니다. 

## <a name="create-the-skillset"></a>기술 세트 만들기 

다음 단계에서는 적용되는 향상된 기능 및 결과가 저장되는 지식 저장소를 모두 지정하는 기술 세트를 지정합니다. Postman에서 **기술 세트 만들기** 탭을 선택합니다. 이 요청은 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`에 대한 PUT을 보냅니다. 이전과 같이 `api-key` 및 `Content-type` 헤더를 설정합니다. 

`skills` 및 `knowledgeStore`라는 두 개의 큰 최상위 개체가 있습니다. `skills` 개체 내의 각 개체는 보강 서비스입니다. 각 보강 서비스에는 `inputs` 및 `outputs`가 있습니다. `LanguageDetectionSkill`에는 `Language`의 `targetName` 출력이 있습니다. 이 노드의 값은 대부분의 다른 기술에서 입력으로 사용합니다. 원본은 `document/Language`입니다. 한 노드의 출력을 다른 노드의 입력으로 사용하는 기능은 데이터가 지식 저장소의 테이블로 이동하는 방법을 지정하는 `ShaperSkill`에서 더 명확하게 드러납니다.

`knowledge_store` 개체는 `{{storage-connection-string}}` Postman 변수를 통해 스토리지 계정에 연결합니다. `knowledge_store`에는 향상된 문서와 지식 저장소의 테이블 및 열 간의 매핑 세트가 있습니다. 

기술 세트를 생성하려면 Postman에서 **보내기** 단추를 선택하여 요청에 대한 PUT을 수행합니다.

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>인덱서 만들기

마지막 단계는 인덱서를 만드는 것입니다. 인덱서는 데이터를 읽고 기술 세트를 활성화합니다. Postman에서 **인덱서 만들기** 요청을 선택한 다음, 본문을 검토합니다. 인덱서의 정의는 이미 만든 몇 가지 다른 리소스(데이터 원본, 인덱스 및 기술 세트)를 참조합니다. 

`parameters/configuration` 개체는 인덱서에서 데이터를 수집하는 방법을 제어합니다. 여기서 입력 데이터는 헤더 줄 및 쉼표로 구분된 값이 있는 단일 문서에 있습니다. 문서 키는 문서의 고유 식별자입니다. 인코딩하기 전에 문서 키는 원본 문서의 URL입니다. 마지막으로, 기술 세트 출력 값(예: 언어 코드, 감정 및 핵심 구)이 문서의 해당 위치에 매핑됩니다. `Language`에는 단일 값이 있지만, `Sentiment`는 `pages` 배열의 각 요소에 적용됩니다. `Keyphrases`는 `pages` 배열의 각 요소에도 적용되는 배열입니다.

`api-key` 및 `Content-type` 헤더를 설정하고 요청의 본문이 다음 소스 코드와 비슷한지 확인한 후에 Postman에서 **보내기**를 선택합니다. Postman에서 PUT 요청을 `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`에 보냅니다. Azure Cognitive Search에서 인덱서를 만들고 실행합니다. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>인덱서 실행 

Azure Portal에서 Azure Cognitive Search 서비스의 **개요** 페이지로 이동합니다. **인덱서** 탭을 선택한 다음, **hotels-reviews-ixr**을 선택합니다. 인덱서가 아직 실행되지 않은 경우 **실행**을 선택합니다. 인덱싱 작업에서 언어 인식과 관련된 몇 가지 경고가 발생할 수 있습니다. 데이터에는 인지 기술에서 아직 지원하지 않는 언어로 작성된 몇 가지 리뷰가 포함되어 있습니다. 

## <a name="next-steps"></a>다음 단계

이제 Cognitive Services를 사용하여 데이터를 보강하고 결과를 지식 저장소에 프로젝션했으므로 Storage Explorer 또는 Power BI를 사용하여 보강된 데이터 세트를 검색할 수 있습니다.

Storage Explorer를 사용하여 이 지식 저장소를 검색하는 방법을 알아보려면 다음 연습을 참조하세요.

> [!div class="nextstepaction"]
> [Storage Explorer를 사용하여 보기](knowledge-store-view-storage-explorer.md)

이 지식 저장소를 Power BI에 연결하는 방법을 알아보려면 다음 연습을 참조하세요.

> [!div class="nextstepaction"]
> [Power BI와 연결](knowledge-store-connect-power-bi.md)

이 연습을 반복하거나 다른 AI 보강 연습을 수행하려면 **hotel-reviews-idxr** 인덱서를 삭제합니다. 인덱서를 삭제하면 사용 가능한 일별 트랜잭션 카운터가 0으로 다시 설정됩니다.