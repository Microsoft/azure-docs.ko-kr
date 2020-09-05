---
title: '빠른 시작: REST API를 사용하여 PowerShell에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: 이 REST API 빠른 시작에서 PowerShell의 Invoke-RestMethod 및 Azure Cognitive Search REST API를 사용하여 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 알아봅니다.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f803532e7d08d0de21541cb5d1b52639b623bb90
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078302"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>빠른 시작: REST API를 사용하여 PowerShell에서 Azure Cognitive Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [PowerShell(REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [Postman(REST)](search-get-started-postman.md)
> * [Python](search-get-started-python.md)
> * [포털](search-get-started-portal.md)
> 

이 문서에서는 PowerShell 및 [Azure Cognitive Search REST API](/rest/api/searchservice/)를 사용하여 Azure Cognitive Search 인덱스를 만들고, 로드하고, 쿼리하는 과정을 안내합니다. 이 문서에서는 PowerShell 명령을 대화형으로 실행하는 방법에 대해 설명합니다. 또는 동일한 작업을 수행하는 [PowerShell 스크립트를 다운로드하여 실행](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart)할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에 필요한 서비스와 도구는 다음과 같습니다. 

+ [PowerShell 5.1 이상](https://github.com/PowerShell/PowerShell)(순차적 및 대화형 단계에 [Invoke-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) 사용)

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-cognitive-search"></a>Azure Cognitive Search에 연결

1. PowerShell에서 콘텐츠 형식 및 API 키를 저장하는 **$headers** 개체를 만듭니다. 관리 API 키(YOUR-ADMIN-API-KEY)를 검색 서비스에 유효한 키로 바꿉니다. 이 헤더는 세션 기간 동안 한 번만 설정하면 되지만 모든 요청에 추가됩니다. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. 서비스의 indexes 컬렉션을 지정하는 **$url** 개체를 만듭니다. 서비스 이름(YOUR-SEARCH-SERVICE-NAME)을 유효한 검색 서비스로 바꿉니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. **Invoke-RestMethod**를 실행하여 GET 요청을 서비스에 보내고 연결을 확인합니다. 서비스에서 다시 보낸 응답을 볼 수 있도록 **ConvertTo-Json**을 추가합니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   서비스가 비어 있고 인덱스가 없는 경우 결과는 다음 예제와 비슷합니다. 그렇지 않으면 인덱스 정의의 JSON 표현이 표시됩니다.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용하지 않는 경우 데이터를 로드하려면 먼저 서비스에 인덱스가 있어야 합니다. 이 단계에서는 인덱스를 정의하고 서비스로 푸시합니다. 이 단계에는 [인덱스 만들기](/rest/api/searchservice/create-index) REST API가 사용됩니다.

인덱스의 필수 요소에는 name 및 fields 컬렉션이 포함됩니다. fields 컬렉션은 *문서*의 구조를 정의합니다. 각 필드에는 사용되는 방법(예: 검색 결과에서 전체 텍스트 검색 가능, 필터링 가능 또는 조회 가능 여부)을 결정하는 이름, 형식 및 속성이 있습니다. 인덱스 내에서 `Edm.String` 형식의 필드 중 하나는 문서 ID에 대한 *key*로 지정해야 합니다.

이 인덱스의 이름은 "hotels-quickstart"이며 아래에 표시된 필드 정의가 있습니다. 다른 연습 문서에서 사용되는 더 큰 [Hotels 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)의 하위 집합입니다. 이 빠른 시작에서 필드 정의를 잘라 간결하게 만들었습니다.

1. 다음 예제를 PowerShell에 붙여넣어 인덱스 스키마가 포함된 **$body** 개체를 만듭니다.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. URI를 서비스의 indexes 컬렉션 및 *hotels-quickstart* 인덱스로 설정합니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. **$url**, **$headers** 및 **$body**가 포함된 명령을 실행하여 서비스에 대한 인덱스를 만듭니다. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    결과는 다음과 비슷합니다(간단히 하기 위해 처음 두 필드로 잘림).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> 확인을 위해 포털에서 인덱스 목록을 확인할 수도 있습니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시하려면 인덱스의 URL 엔드포인트에 대한 HTTP POST 요청을 사용합니다. 이 작업의 REST API는 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)입니다.

1. 다음 예제를 PowerShell에 붙여넣어 로드하려는 문서가 포함된 **$body** 개체를 만듭니다. 

    이 요청에는 두 개의 전체 레코드와 하나의 부분 레코드가 포함됩니다. 부분 레코드는 불완전한 문서를 업로드할 수 있음을 보여 줍니다. `@search.action` 매개 변수는 인덱싱을 수행하는 방법을 지정합니다. 유효한 값은 pload, merge, mergeOrUpload 및 delete입니다. mergeOrUpload 동작은 hotelId = 3에 대한 새 문서를 만들거나, 이미 있는 경우 내용을 업데이트합니다.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            } 
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. 엔드포인트를 *hotels-quickstart* docs 컬렉션으로 설정하고, 인덱스 작업(indexes/hotels-quickstart/docs/index)을 포함합니다.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. **$url**, **$headers** 및 **$body**가 포함된 명령을 실행하여 문서를 hotels-quickstart 인덱스에 로드합니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    결과는 다음 예제와 비슷합니다. [201 상태 코드](/rest/api/searchservice/HTTP-status-codes)가 표시됩니다.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계에서는 [문서 검색 API](/rest/api/searchservice/search-documents)를 사용하여 인덱스를 쿼리하는 방법을 보여 줍니다.

$url 검색에서는 작은따옴표를 사용해야 합니다. 쿼리 문자열에는 **$** 문자가 포함되며, 전체 문자열을 작은따옴표로 묶으면 이스케이프 처리를 생략할 수 있습니다.

1. 엔드포인트를 *hotels-quickstart* docs 컬렉션으로 설정하고, 쿼리 문자열을 전달하는 **search** 매개 변수를 추가합니다. 
  
   이 문자열은 빈 검색(search=*)을 실행하여 순위가 없는 임의 문서 목록(검색 점수 = 1.0)을 반환합니다. 기본적으로 Azure Cognitive Search는 한 번에 50개의 일치 항목을 반환합니다. 구조적으로 이 쿼리는 전체 문서 구조와 값을 반환합니다. **$count=true**를 추가하여 결과에 있는 모든 문서의 수를 가져옵니다.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. 다음 명령을 실행하여 **$url**을 서비스에 보냅니다.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    결과는 다음 출력과 비슷합니다.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

구문을 이해하기 위해 몇 가지 다른 쿼리 예제를 시도해봅니다. 문자열 검색, 축자 $filter 쿼리를 수행하고, 결과 세트를 제한하고, 검색 범위를 특정 필드로 지정하는 등의 작업을 수행할 수 있습니다.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>리소스 정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 PowerShell을 사용하여 Azure Cognitive Search에서 콘텐츠를 만들고 액세스하기 위한 기본 워크플로를 단계별로 실행했습니다. 이러한 개념을 염두에 두고 Azure 데이터 원본의 인덱싱과 같은 고급 시나리오로 이동하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [REST 자습서: Azure Cognitive Search에서 반정형 데이터(JSON Blob) 인덱싱 및 검색](search-semi-structured-data.md)
