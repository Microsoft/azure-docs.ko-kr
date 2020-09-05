---
title: '빠른 시작: REST API를 사용하여 Python에서 검색 인덱스 만들기'
titleSuffix: Azure Cognitive Search
description: Python, Jupyter Notebook 및 Azure Cognitive Search REST API를 사용하여 인덱스를 만들고, 데이터를 로드하고, 쿼리를 실행하는 방법을 설명합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 08/20/2020
ms.custom: devx-track-python
ms.openlocfilehash: e4141bc4887a166876d1fc4590b73f382abd0b95
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936676"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebooks"></a>빠른 시작: Jupyter Notebook을 사용하여 Python에서 Azure Cognitive Search 인덱스 만들기

> [!div class="op_single_selector"]
> * [Python(REST)](search-get-started-python.md)
> * [PowerShell(REST)](./search-get-started-powershell.md)
> * [C#](./search-get-started-dotnet.md)
> * [Postman(REST)](search-get-started-postman.md)
> * [포털](search-get-started-portal.md)
> 

Python 및 [Azure Cognitive Search REST API](/rest/api/searchservice/)를 사용하여 Azure Cognitive Search 인덱스를 만들고, 로드하고, 쿼리하는 Jupyter Notebook을 빌드합니다. 이 문서에서는 Notebook을 단계별로 빌드하는 방법에 대해 설명합니다. 또는 [완성된 Jupyter Python Notebook을 다운로드하여 실행](https://github.com/Azure-Samples/azure-search-python-samples)할 수 있습니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작에 필요한 서비스와 도구는 다음과 같습니다. 

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section) - Python 3.x 및 Jupyter Notebook 제공

+ [Azure Cognitive Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 계층을 사용할 수 있습니다. 

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Cognitive Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 가져옵니다.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

1. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-get-started-postman/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-cognitive-search"></a>Azure Cognitive Search에 연결

이 작업에서는 Jupyter Notebook을 시작하고, Azure Cognitive Search에 연결할 수 있는지 확인합니다. 이 작업은 서비스에서 인덱스 목록을 요청하여 수행합니다. Anaconda3이 있는 Windows에서 Anaconda Navigator를 사용하여 Notebook을 시작할 수 있습니다.

1. 새 Python3 Notebook을 만듭니다.

1. 첫 번째 셀에서 JSON 작업 및 HTTP 요청 작성에 사용되는 라이브러리를 로드합니다.

   ```python
   import json
   import requests
   from pprint import pprint
   ```

1. 두 번째 셀에서 모든 요청에 상수로 사용할 요청 요소를 입력합니다. 검색 서비스 이름(YOUR-SEARCH-SERVICE-NAME)과 관리자 API 키(YOUR-ADMIN-API-KEY)를 유효한 값으로 바꿉니다. 

   ```python
   endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
   api_version = '?api-version=2020-06-30'
   headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>' }
   ```

   ConnectionError `"Failed to establish a new connection"`을 가져오는 경우 api-key가 기본 또는 보조 관리자 키이고 모든 선행 및 후행 문자(`?` 및 `/`)가 있는지 확인합니다.

1. 세 번째 셀에서 요청을 작성합니다. 이 GET 요청은 검색 서비스의 indexes 컬렉션을 대상으로 하고, 기존 인덱스의 name 속성을 선택합니다.

   ```python
   url = endpoint + "indexes" + api_version + "&$select=name"
   response  = requests.get(url, headers=headers)
   index_list = response.json()
   pprint(index_list)
   ```

1. 각 단계를 실행합니다. 인덱스가 있으면 응답에 인덱스 이름 목록이 포함됩니다. 아래 스크린샷에서 서비스에는 이미 azureblob-index 및 realestate-us-sample 인덱스가 있습니다.

   ![Azure Cognitive Search에 대한 HTTP 요청이 포함된 Jupyter Notebook의 Python 스크립트](media/search-get-started-python/connect-azure-search.png "Azure Cognitive Search에 대한 HTTP 요청이 포함된 Jupyter Notebook의 Python 스크립트")

   반대로, 빈 인덱스 컬렉션은 다음 응답을 반환합니다. `{'@odata.context': 'https://mydemo.search.windows.net/$metadata#indexes(name)', 'value': []}`

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용하지 않는 경우 데이터를 로드하려면 먼저 서비스에 인덱스가 있어야 합니다. 이 단계에서는 [인덱스 만들기 REST API](/rest/api/searchservice/create-index)를 사용하여 인덱스 스키마를 서비스로 푸시합니다.

인덱스의 필수 요소에는 name, fields 컬렉션 및 key가 포함됩니다. fields 컬렉션은 *문서*의 구조를 정의합니다. 각 필드에는 필드가 사용되는 방법(예: 검색 결과에서 전체 텍스트 검색 가능, 필터링 가능 또는 조회 가능 여부)을 결정하는 이름, 형식 및 속성이 있습니다. 인덱스 내에서 `Edm.String` 형식의 필드 중 하나는 문서 ID에 대한 *key*로 지정해야 합니다.

이 인덱스의 이름은 "hotels-quickstart"이며 아래에 표시된 필드 정의가 있습니다. 다른 연습에서 사용되는 더 큰 [Hotels 인덱스](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON)의 하위 세트입니다. 이 빠른 시작에서는 간단히 하기 위해 다듬었습니다.

1. 다음 셀에서 다음 예제를 셀에 붙여넣어 스키마를 제공합니다. 

    ```python
    index_schema = {
       "name": "hotels-quickstart",  
       "fields": [
         {"name": "HotelId", "type": "Edm.String", "key": "true", "filterable": "true"},
         {"name": "HotelName", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "true", "facetable": "false"},
         {"name": "Description", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "en.lucene"},
         {"name": "Description_fr", "type": "Edm.String", "searchable": "true", "filterable": "false", "sortable": "false", "facetable": "false", "analyzer": "fr.lucene"},
         {"name": "Category", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Tags", "type": "Collection(Edm.String)", "searchable": "true", "filterable": "true", "sortable": "false", "facetable": "true"},
         {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Rating", "type": "Edm.Double", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Address", "type": "Edm.ComplexType", 
         "fields": [
         {"name": "StreetAddress", "type": "Edm.String", "filterable": "false", "sortable": "false", "facetable": "false", "searchable": "true"},
         {"name": "City", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "StateProvince", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "PostalCode", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"},
         {"name": "Country", "type": "Edm.String", "searchable": "true", "filterable": "true", "sortable": "true", "facetable": "true"}
        ]
       }
      ]
    }
    ```

2. 다른 셀에서 요청을 작성합니다. 이 POST 요청은 검색 서비스의 indexes 컬렉션을 대상으로 하고, 이전 셀에서 제공한 인덱스 스키마에 기반한 인덱스를 만듭니다.

   ```python
   url = endpoint + "indexes" + api_version
   response  = requests.post(url, headers=headers, json=index_schema)
   index = response.json()
   pprint(index)
   ```

3. 각 단계를 실행합니다.

   응답에는 스키마의 JSON 표현이 포함됩니다. 다음 스크린샷에서는 응답의 일부만 보여 줍니다.

    ![인덱스 만들기 요청](media/search-get-started-python/create-index.png "인덱스 만들기 요청")

> [!Tip]
> 인덱스 만들기를 확인하는 또 다른 방법은 포털에서 인덱스 목록을 확인하는 것입니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시하려면 인덱스의 URL 엔드포인트에 대한 HTTP POST 요청을 사용합니다. REST API는 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)입니다. 문서는 GitHub의 [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON)에서 시작됩니다.

1. 새 셀에서 인덱스 스키마를 준수하는 4개의 문서를 제공합니다. 각 문서에 대한 업로드 작업을 지정합니다.

    ```python
    documents = {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
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
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    ```   

2. 다른 셀에서 요청을 작성합니다. 이 POST 요청은 hotels-quickstart 인덱스의 docs 컬렉션을 대상으로 하며 이전 단계에서 제공된 문서를 푸시합니다.

   ```python
   url = endpoint + "indexes/hotels-quickstart/docs/index" + api_version
   response  = requests.post(url, headers=headers, json=documents)
   index_content = response.json()
   pprint(index_content)
   ```

3. 각 단계를 실행하여 문서를 검색 서비스의 인덱스로 푸시합니다. 결과는 다음 예제와 비슷합니다. 

    ![인덱스로 문서 보내기](media/search-get-started-python/load-index.png "인덱스로 문서 보내기")

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계에서는 [문서 검색 REST API](/rest/api/searchservice/search-documents)를 사용하여 인덱스를 쿼리하는 방법을 보여 줍니다.

1. 셀에서 빈 검색(search=*)을 실행하는 쿼리 식을 제공하여 임의 문서의 순위 없는 목록(검색 점수 = 1.0)을 반환합니다. 기본적으로 Azure Cognitive Search는 한 번에 50개의 일치 항목을 반환합니다. 구조적으로 이 쿼리는 전체 문서 구조와 값을 반환합니다. $count=true를 추가하여 결과에 있는 모든 문서의 수를 가져옵니다.

   ```python
   searchstring = '&search=*&$count=true'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 새 셀에서 다음 예제를 제공하여 "hotels" 및 "wifi"라는 용어를 검색합니다. $select를 추가하여 검색 결과에 포함할 필드를 지정합니다.

   ```python
   searchstring = '&search=hotels wifi&$count=true&$select=HotelId,HotelName'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)   
   ```

   결과는 다음 출력과 비슷합니다. 

    ![인덱스 검색](media/search-get-started-python/search-index.png "인덱스 검색")

1. 다음으로, 등급이 4보다 큰 호텔만 선택하는 $filter 식을 적용합니다. 

   ```python
   searchstring = '&search=*&$filter=Rating gt 4&$select=HotelId,HotelName,Description,Rating'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)     
   ```

1. 기본적으로 검색 엔진은 상위 50개의 문서를 반환하지만 top 및 skip을 사용하여 페이지 매김을 추가하고 각 결과에서 문서 수를 선택할 수 있습니다. 이 쿼리는 각 결과 집합에 두 개의 문서를 반환합니다.

   ```python
   searchstring = '&search=boutique&$top=2&$select=HotelId,HotelName,Description'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

1. 이 마지막 예제에서는 $orderby를 사용하여 도시별로 결과를 정렬합니다. 이 예제에는 주소 컬렉션의 필드가 포함됩니다.

   ```python
   searchstring = '&search=pool&$orderby=Address/City&$select=HotelId, HotelName, Address/City, Address/StateProvince'

   url = endpoint + "indexes/hotels-quickstart/docs" + api_version + searchstring
   response  = requests.get(url, headers=headers, json=searchstring)
   query = response.json()
   pprint(query)
   ```

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단히 하기 위해 단축된 버전의 Hotels 인덱스를 사용합니다. 전체 버전을 만들어 더 흥미로운 쿼리를 사용해 볼 수 있습니다. 전체 버전과 50개 문서를 모두 가져오려면 **데이터 가져오기** 마법사를 실행하여 기본 제공 데이터 원본 샘플에서 *hotels-sample*을 선택합니다.

> [!div class="nextstepaction"]
> [빠른 시작: Azure Portal에서 인덱스 만들기](search-get-started-portal.md)