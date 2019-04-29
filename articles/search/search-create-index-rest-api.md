---
title: '빠른 시작: 만들기, 로드 및 PowerShell 및 REST API-Azure Search를 사용 하 여 인덱스 쿼리'
description: 만들기, 로드 및 PowerShell의를 사용 하 여 인덱스 쿼리 Invoke-restmethod 및 Azure Search REST API입니다.
ms.date: 04/08/2019
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 2deba4bf941d561fcef7c2dff804646732e7ce24
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817152"
---
# <a name="quickstart-create-an-azure-search-index-using-powershell-and-the-rest-api"></a>빠른 시작: PowerShell 및 REST API를 사용 하 여 Azure Search 인덱스 만들기
> [!div class="op_single_selector"]
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [C#](search-create-index-dotnet.md)
> * [Postman (REST)](search-fiddler.md)
> * [포털](search-create-index-portal.md)
> 

이 문서 만들기, 로드 하 고 Azure Search를 쿼리 하는 과정 안내 [인덱스](search-what-is-an-index.md) PowerShell을 사용 하 고 [Azure Search 서비스 REST API](https://docs.microsoft.com/rest/api/searchservice/)합니다. 인덱스 정 및 검색 가능한 콘텐츠는 올바른 형식의 JSON 콘텐츠로 요청 본문에 제공 됩니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 사용되는 서비스와 도구는 다음과 같습니다. 

[Azure Search 서비스를 만들거나](search-create-service-portal.md) 현재 구독에서 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다. 

[PowerShell 5.1 이상](https://github.com/PowerShell/PowerShell)를 사용 하 여 [Invoke-restmethod](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) 순차적이 고 대화형 단계에 대 한 합니다.

## <a name="get-a-key-and-url"></a>키 및 URL 가져오기

REST를 호출하려면 모든 요청에 대한 액세스 키와 서비스 URL이 필요합니다. 검색 서비스는 둘 모두를 사용하여 작성되므로 Azure Search를 구독에 추가한 경우 다음 단계에 따라 필요한 정보를 확보하십시오.

1. [Azure Portal에 로그인](https://portal.azure.com/)하고, 검색 서비스 **개요** 페이지에서 URL을 가져옵니다. 엔드포인트의 예는 다음과 같습니다. `https://mydemo.search.windows.net`

2. **설정** > **키**에서 서비스에 대한 모든 권한의 관리자 키를 가져옵니다. 교체 가능한 두 개의 관리자 키가 있으며, 하나를 롤오버해야 하는 경우 비즈니스 연속성을 위해 다른 하나가 제공됩니다. 개체 추가, 수정 및 삭제 요청 시 기본 또는 보조 키를 사용할 수 있습니다.

![HTTP 엔드포인트 및 액세스 키 가져오기](media/search-fiddler/get-url-key.png "HTTP 엔드포인트 및 액세스 키 가져오기")

모든 요청에서 서비스에 보내는 각 요청마다 API 키가 필요합니다. 유효한 키가 있다면 요청을 기반으로 요청을 보내는 애플리케이션과 이를 처리하는 서비스 사이에 신뢰가 쌓입니다.

## <a name="connect-to-azure-search"></a>Azure Search에 연결

PowerShell에서 만들기를 **$headers** 콘텐츠 형식 및 API 키를 저장할 개체입니다. 세션의 기간에 대 한이 헤더를 한 번 설정 해야 하지만 모든 요청에 추가 합니다. 

```powershell
$headers = @{
   'api-key' = '<your-admin-api-key>'
   'Content-Type' = 'application/json' 
   'Accept' = 'application/json' }
```

만들기는 **$url** 서비스를 지정 하는 개체 컬렉션을 인덱싱합니다. `mydemo` 서비스 이름 자리 표시자로 제공 됩니다. 이 예제 전체에서 현재 구독에서 올바른 검색 서비스를 사용 하 여이 대체 합니다.

```powershell
$url = "https://mydemo.search.windows.net/indexes?api-version=2017-11-11"
```

실행할 **Invoke-restmethod** GET 요청을 서비스에 보내고 연결을 확인 합니다. 추가 **Convertto-json** 서비스에서 다시 보낸 응답을 볼 수 있도록 합니다.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

서비스 비어에 인덱스가 없는 경우 결과 다음 예제와 비슷합니다. 그렇지 않은 경우 인덱스 정의의 JSON 표현을 표시 됩니다.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
    "value":  [

              ]
}
```

## <a name="1---create-an-index"></a>1 - 인덱스 만들기

포털을 사용 하지 않는 데이터를 로드 하려면 먼저 인덱스 서비스에 있어야 합니다. 이 단계는 인덱스를 정의 하 고 서비스에 푸시합니다. 합니다 [인덱스 만들기 (REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index) 이 단계에 사용 됩니다.

인덱스의 필수 요소에는 이름과 필드 컬렉션이 포함 됩니다. 구조를 정의 하는 필드 컬렉션을 *문서*합니다. 각 필드에는 이름, 형식 및 사용 하는 방식을 결정 하는 특성 (예를 들어 인지 전체 텍스트 검색 가능, 필터링 가능, 검색 결과에서 검색 가능). 형식의 필드를 인덱스 `Edm.String` 로 지정 해야 합니다 *키* 문서 id에 대 한 합니다.

이 인덱스 "호텔" 이라는 있고 아래 표시 된 필드 정의 합니다. 지정 된 인덱스 정의 [언어 분석기](index-add-language-analyzers.md) 에 대 한는 `description_fr` 뒷부분의 예제에 추가 하는 프랑스어 텍스트를 저장 하기 위한 것 이므로 필드.

이 예제를 만들려면 PowerShell 붙여를 **$body** 인덱스 스키마를 포함 하는 개체입니다.

```powershell
$body = @"
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
"@
```

서비스에서 인덱스 컬렉션에 URI를 설정 하며 *호텔* 인덱스입니다.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11"
```

사용 하 여 명령을 실행 **$url**를 **$headers**, 및 **$body** 서비스에서 인덱스를 만들려고 합니다. 

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```
결과 (간단히 하기 위해 처음 두 필드에 잘림) 다음과 유사 하 게 같아야 합니다.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag":  "\"0x8D6A99E2DED96B0\"",
    "name":  "hotels",
    "defaultScoringProfile":  null,
    "fields":  [
                   {
                       "name":  "hotelId",
                       "type":  "Edm.String",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  false,
                       "facetable":  false,
                       "key":  true,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
                   {
                       "name":  "baseRate",
                       "type":  "Edm.Double",
                       "searchable":  false,
                       "filterable":  true,
                       "retrievable":  true,
                       "sortable":  true,
                       "facetable":  true,
                       "key":  false,
                       "indexAnalyzer":  null,
                       "searchAnalyzer":  null,
                       "analyzer":  null,
                       "synonymMaps":  ""
                   },
. . .
```

> [!Tip]
> 확인을 위해 수도 포털에서 인덱스 목록을 확인 하거나 보려면 서비스 연결을 확인 하는 데 사용 하 여 명령을 다시 실행 합니다 *호텔* 인덱스 컬렉션에서 인덱스를 나열 합니다.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - 문서 로드

문서를 푸시 하려면 인덱스의 URL 끝점에 HTTP POST 요청을 사용 합니다. 이 작업에 대 한 REST api [추가, 업데이트 또는 삭제 문서](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)합니다.

이 예제를 만들려면 PowerShell 붙여를 **$body** 업로드 하려는 문서를 포함 하는 개체입니다. 

이 요청에는 두 개의 전체와 부분 레코드를 포함합니다. 일부 레코드는 불완전 한 문서를 업로드할 수 있습니다 하는 방법을 보여 줍니다. `@search.action` 매개 변수는 인덱싱을 수행 하는 방법을 지정 합니다. 유효한 값에는 업로드, merge, mergeOrUpload 및 삭제 포함 됩니다. MergeOrUpload 동작 하거나 hotelId에 대 한 새 문서를 만들고 = 3, 또는 이미 있는 경우 콘텐츠를 업데이트 합니다.

```powershell
$body = @"
{
    "value": [
        {
            "@search.action": "upload",
            "hotelId": "1",
            "baseRate": 199.0,
            "description": "Best hotel in town",
            "hotelName": "Fancy Stay",
            "category": "Luxury",
            "tags": ["pool", "view", "wifi", "concierge"],
            "parkingIncluded": false,
            "smokingAllowed": false,
            "lastRenovationDate": "2010-06-27T00:00:00Z",
            "rating": 5,
            "location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
        },
        {
            "@search.action": "upload",
            "hotelId": "2",
            "baseRate": 79.99,
            "description": "Cheapest hotel in town",
            "hotelName": "Roach Motel",
            "category": "Budget",
            "tags": ["motel", "budget"],
            "parkingIncluded": true,
            "smokingAllowed": true,
            "lastRenovationDate": "1982-04-28T00:00:00Z",
            "rating": 1,
            "location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        }
    ]
}
"@
```

로 끝점을 설정 합니다 *호텔* 문서 컬렉션 인덱스 작업 (인덱스/호텔/docs/index)를 포함 합니다.

```powershell
$url = "https://mydemo.search.windows.net/indexes/hotels/docs/index?api-version=2017-11-11"
```

사용 하 여 명령을 실행 **$url**를 **$headers**, 및 **$body** 호텔 인덱스에 문서를 로드 합니다.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
```
결과 다음 예와 유사 합니다. 상태 코드 201이 표시 됩니다. 모든 상태 코드 설명을 참조 하세요 [HTTP 상태 코드 (Azure Search)](https://docs.microsoft.com/rest/api/searchservice/HTTP-status-codes)합니다.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2017_11_11.IndexResult)",
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
                  }
              ]
}
```

## <a name="3---search-an-index"></a>3 - 인덱스 검색

이 단계를 사용 하 여 인덱스를 쿼리 하는 방법을 보여 줍니다.는 [검색 문서 API](https://docs.microsoft.com/rest/api/searchservice/search-documents)합니다.

로 끝점을 설정 합니다 *호텔* 문서 컬렉션 추가 **검색** 매개 변수를 쿼리 문자열을 포함 합니다. 이 문자열은 빈 검색 하 고 모든 문서는 unranked 목록을 반환 합니다.

```powershell
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*'
```

보낼 명령을 실행 합니다 **$url** 서비스입니다.

```powershell
Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
```

결과 다음 출력과 유사 합니다.

```
{
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels\u0027)/$metadata#docs(*)",
    "value":  [
                  {
                      "@search.score":  1.0,
                      "hotelId":  "1",
                      "baseRate":  199.0,
                      "description":  "Best hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Fancy Stay",
                      "category":  "Luxury",
                      "tags":  "pool view wifi concierge",
                      "parkingIncluded":  false,
                      "smokingAllowed":  false,
                      "lastRenovationDate":  "2010-06-27T00:00:00Z",
                      "rating":  5,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "2",
                      "baseRate":  79.99,
                      "description":  "Cheapest hotel in town",
                      "description_fr":  null,
                      "hotelName":  "Roach Motel",
                      "category":  "Budget",
                      "tags":  "motel budget",
                      "parkingIncluded":  true,
                      "smokingAllowed":  true,
                      "lastRenovationDate":  "1982-04-28T00:00:00Z",
                      "rating":  1,
                      "location":  "@{type=Point; coordinates=System.Object[]; crs=}"
                  },
                  {
                      "@search.score":  1.0,
                      "hotelId":  "3",
                      "baseRate":  129.99,
                      "description":  "Close to town hall and the river",
                      "description_fr":  null,
                      "hotelName":  null,
                      "category":  null,
                      "tags":  "",
                      "parkingIncluded":  null,
                      "smokingAllowed":  null,
                      "lastRenovationDate":  null,
                      "rating":  null,
                      "location":  null
                  }
              ]
}
```

구문을 이해할 수 있도록 다른 몇 가지 쿼리 예를 봅니다. 축 자 $filter 쿼리 문자열 검색을 수행, 범위, 특정 필드에 검색 결과 집합을 제한할 수 있습니다.

```powershell
# Query example 1
# Search the entire index for the term 'budget'
# Return only the `hotelName` field, "Roach hotel"
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=budget&$select=hotelName'

# Query example 2 
# Apply a filter to the index to find hotels cheaper than $150 per night
# Returns the `hotelId` and `description`. Two documents match.
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$filter=baseRate lt 150&$select=hotelId,description'

# Query example 3
# Search the entire index, order by a specific field (`lastRenovationDate`) in descending order
# Take the top two results, and show only `hotelName` and `lastRenovationDate`
$url = 'https://mydemo.search.windows.net/indexes/hotels/docs?api-version=2017-11-11&search=*&$top=2&$orderby=lastRenovationDate desc&$select=hotelName,lastRenovationDate'
```
## <a name="clean-up"></a>정리 

더 이상 필요한 경우 인덱스를 삭제 해야 합니다. 무료 서비스는 세 가지 인덱스 제한 됩니다. 사용 하지 않는 적극적으로 다른 자습서를 단계별로 실행할 수 있도록 모든 인덱스를 삭제 하려고 할 수 있습니다.

```powershell
# Set the URI to the hotel index
$url = 'https://mydemo.search.windows.net/indexes/hotels?api-version=2017-11-11'

# Delete the index
Invoke-RestMethod -Uri $url -Headers $headers -Method Delete
```

## <a name="next-steps"></a>다음 단계

인덱스에 프랑스어 설명이 추가 해 보세요. 다음 예에서는 프랑스어 문자열을 포함 하 고 추가 검색 작업을 보여 줍니다. MergeOrUpload를 사용 하 여 만들거나 기존 필드를 추가 합니다. 다음 문자열을 u t F-8로 인코딩된 해야 합니다.

```json
{
    "value": [
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "1",
            "description_fr": "Meilleur hôtel en ville"
        },
        {
            "@search.action": "merge",
            "hotelId": "2",
            "description_fr": "Hôtel le moins cher en ville"
        }
    ]
}
```
