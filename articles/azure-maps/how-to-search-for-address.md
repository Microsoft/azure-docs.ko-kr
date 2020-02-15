---
title: Azure Maps Search services를 사용 하 여 위치 검색 | Microsoft Azure 맵
description: 이 문서에서는 지 오 코딩 및 역방향 지 오 코딩에 대 한 Microsoft Azure Maps Search Service를 사용 하 여 위치를 검색 하는 방법을 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 40066f24fec00610a1efd10b2cb874b1100acdee
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209888"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Azure Maps Search services를 사용 하 여 위치 검색

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) 는 개발자가 주소, 위치, 비즈니스 목록을 이름 또는 범주별로 검색 하 고 기타 지리적 정보를 검색할 수 있도록 설계 된 RESTful api 집합입니다. 기존 지 오 코딩 지원 이외에도 서비스는 위도 및 경도를 기반으로 하는 역방향 geocode 주소 및 교차 거리를 설정할 수 있습니다. 검색에서 반환 된 위도 및 경도 값은 [경로](https://docs.microsoft.com/rest/api/maps/route) 및 [날씨](https://docs.microsoft.com/rest/api/maps/weather) 서비스와 같은 다른 Azure Maps 서비스에서 매개 변수로 사용할 수 있습니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

* [검색 주소 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 를 사용 하 여 주소 (geocode 주소 위치)에 대 한 위도 및 경도 좌표 요청
* [유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 를 사용 하 여 주소나 관심 지점 (poi) 검색
* 속성 및 좌표와 함께 주소 검색
* [역 주소 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 을 통해 좌표 위치를 주소로 변환 합니다.
* [검색 주소 역방향 교차 주소 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet) 를 사용 하 여 교차 주소 검색

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료 하려면 먼저 Azure Maps 계정을 만들고이 계정 구독 키로 매핑 하세요. 계정 [만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 의 지침에 따라 Azure Maps 계정 구독을 만들고 [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account) 의 단계에 따라 계정에 대 한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>주소에 대 한 위도 및 경도 요청 (지 오 코딩)

이 예제에서는 Azure Maps [가져오기 검색 주소 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 를 사용 하 여 주소를 위도 및 경도 좌표로 변환 합니다. 전체 또는 부분으로 된 주소를 API에 전달 하 고, 주소, 우편 번호, 국가/지역 등의 상세 주소 속성 뿐만 아니라 위도 및 경도의 위치 값을 포함 하는 응답을 받을 수 있습니다.

Geocode에 대 한 주소 집합이 있는 경우 [사후 검색 주소 일괄 처리 api](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatch) 를 사용 하 여 단일 API 호출로 쿼리 일괄 처리를 보낼 수 있습니다.

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 이름을 **주소 검색**으로 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력한 다음 권한 부여 프로토콜이 있는 경우 해당 프로토콜을 선택합니다.

![주소 검색](./media/how-to-search-for-address/address_search_url.png)

| 매개 변수 | 제안 값 |
|---------------|------------------------------------------------| 
| HTTP 메서드 | GET |
| 요청 URL | [https://atlas.microsoft.com/search/address/json?](https://atlas.microsoft.com/search/address/json?) | 
| 권한 부여 | 인증 없음 |

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다. 

![주소 검색](./media/how-to-search-for-address/address_search_params.png) 

| 키 | 값 | 
|------------------|-------------------------| 
| api-version | 1.0 | 
| 구독 키 | \<사용자의 Azure 맵 키\> | 
| Query | 400 한 광범위 한 St, 시애틀, WA 98109 | 

4. **보내기**를 클릭하고 응답 본문을 검토합니다. 

이 경우 전체 주소 쿼리를 지정했으며 응답 본문에 단일 결과를 받습니다. 

5. 매개 변수에 다음 값에 대한 쿼리 문자열을 편집합니다. 

    ```plaintext 
        400 Broad, Seattle 
    ``` 

6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다. 

| 키 | 값 | 
|-----|------------| 
| typeahead | true | 

**typeahead** 플래그는 주소 검색 API에 쿼리를 부분 입력으로 처리하고 예측 값의 배열을 반환하도록 지시합니다.

## <a name="using-fuzzy-search-api"></a>유사 항목 검색 API 사용

검색 쿼리에 대 한 사용자 입력 정보를 모를 경우에는[ 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 를 사용 하 여 사용 하는 것이 좋습니다. Azure Maps 이 API는 POI (Point Point) 검색 및 지 오 코딩을 정식 ' 한 줄 검색 '로 결합 합니다. 예를 들어 API는 모든 주소 또는 POI 토큰 조합의 입력을 처리할 수 있습니다. 또한 API는 컨텍스트 위치(위도/경도 쌍)에 가중치를 부여하거나, 좌표와 반경으로 완전히 제한하거나, 지리적으로 편향된(geo biasing) 앵커 지점 없이 더 일반적으로 실행할 수도 있습니다.

대부분의 검색 쿼리는 성능을 달성하고 비정상적인 결과를 줄이기 위해 기본적으로 `maxFuzzyLevel=1`로 설정됩니다. 이 기본값은 요청에 따라 필요한 경우 쿼리 매개 변수에 `maxFuzzyLevel=2` 또는 `3`을 전달하여 재정의할 수 있습니다.

### <a name="search-for-an-address-using-fuzzy-search"></a>유사 항목 검색을 사용하여 주소 검색

1. Postman 앱을 열고 새로 만들기 새로 만들기를 클릭하고 **GET 요청**을 선택합니다. **유사 항목 검색**의 요청 이름을 입력하고 이를 저장할 컬렉션 또는 폴더를 선택하고 **저장**을 클릭합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_url.png)

    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/fuzzy/json?](https://atlas.microsoft.com/search/fuzzy/json?) |
    | 권한 부여 | 인증 없음 |

    URL 경로의 **json** 특성은 응답 형식을 결정합니다. 이 문서에서는 사용 편의성과 가독성을 위해 json을 사용 합니다. 사용 가능한 응답 형식은 **Maps 기능 API 참조**의 [유사 항목 검색 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 정의에서 찾을 수 있습니다.

3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_params.png)

    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 피자 |

4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    "피자"의 모호한 쿼리 문자열은 "피자" 및 "식당" 범주에서 10 개의 [이율 결과](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi#searchpoiresponse) (poi)를 반환 했습니다. 각 결과는 해당 위치에 대 한 주소, 위도 및 경도 값, 뷰 포트 및 진입점을 반환 합니다.
  
    결과는 특정 기준 위치에 연결되지 않고 이 쿼리에 대해 변화됩니다. **CountrySet** 매개 변수를 사용 하 여 응용 프로그램에 적용 해야 하는 국가/지역만 지정할 수 있습니다. 기본 동작은 전체 세계를 검색 하 여 불필요 한 결과를 반환 하는 것입니다.

5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |------------------|-------------------------|
    | countrySet | US |
  
    이제 결과는 국가 코드로 경계가 구분되며 쿼리는 미국의 피자 음식점을 반환합니다.
  
    위치에 대한 결과를 제공하려면 관심 지점을 쿼리하고 반환된 위도와 경도 값을 유사 항목 검색 서비스에 대한 호출에 사용할 수 있습니다. 이 경우 검색 서비스를 사용하여 시애틀의 스페이스 니들(Space Needle) 탑의 위치를 반환하고 위도 / 경도를 사용했습니다. 검색 방향을 지정하는 값입니다.
  
6. 매개 변수에서 다음 키 / 값 쌍을 입력하고 **보내기**를 클릭합니다.

    ![유사 항목 검색](./media/how-to-search-for-address/fuzzy_search_latlon.png)
  
    | 키 | 값 |
    |-----|------------|
    | lat | 47.620525 |
    | lon | -122.349274 |


## <a name="search-for-a-street-address-using-reverse-address-search"></a>역방향 주소 검색을 사용하여 주소 검색

Azure Maps [검색 주소 역방향 API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 를 사용 하면 좌표를 사람이 이해할 수 있는 주소 (예: 37.786505,-122.3862)로 변환할 수 있습니다. 일반적으로 장치 또는 자산에서 GPS 피드를 받고 좌표가 위치한 주소를 알고 싶은 응용 프로그램을 추적 하는 데 필요 합니다.
역방향 geocode를 위한 좌표 위치 집합이 있는 경우 [사후 검색 주소 역방향 BATCH API](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatch) 를 사용 하 여 단일 API 호출로 쿼리 일괄 처리를 보낼 수 있습니다.


1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 검색 URL](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/json?](https://atlas.microsoft.com/search/address/reverse/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    ![역 주소 검색 매개 변수](./media/how-to-search-for-address/reverse_address_search_params.png)
  
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

    응답에는 Safeco Field에 대한 주요 주소 정보가 포함됩니다.
  
5. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | number | true |

    요청을 사용 하 여 [number](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수를 보내는 경우 응답에는 거리 (왼쪽 또는 오른쪽)와 해당 숫자의 오프셋 위치가 포함 될 수 있습니다.
  
6. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnSpeedLimit | true |
  
    [ReturnSpeedLimit](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수를 설정 하면 응답에서 게시 된 속도 제한을 반환 합니다.

7. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | returnRoadUse | true |

    [returnRoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수가 설정되면 응답에서 도로 수준의 역방향 지역 코드에 대한 도로 사용 배열을 반환합니다.

8. **Params** 섹션에 다음 키/값 쌍을 추가하고 **보내기**를 클릭합니다.

    | 키 | 값 |
    |-----|------------|
    | roadUse | true |

    [RoadUse](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) 쿼리 매개 변수를 사용 하 여 역방향 geocode 쿼리를 특정 유형의 이동으로 제한할 수 있습니다.
  
## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>역 주소 교차 번 검색을 사용 하 여 교차 주소 검색

1. Postman에서 **새 요청** | **GET 요청**을 클릭하고 **역 주소 교차로 검색**으로 이름 지정합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택하고 API 엔드포인트에 대한 요청 URL을 입력합니다.
  
    ![역 주소 교차로 검색](./media/how-to-search-for-address/reverse_address_search_url.png)
  
    | 매개 변수 | 제안 값 |
    |---------------|------------------------------------------------|
    | HTTP 메서드 | GET |
    | 요청 URL | [https://atlas.microsoft.com/search/address/reverse/crossstreet/json?](https://atlas.microsoft.com/search/address/reverse/crossstreet/json?) |
    | 권한 부여 | 인증 없음 |
  
3. **Params**를 클릭하고 요청 URL에 쿼리 또는 경로 매개 변수로 사용할 다음 키 / 값 쌍을 입력합니다.
  
    | 키 | 값 |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | 구독 키 | \<사용자의 Azure 맵 키\> |
    | Query | 47.591180,-122.332700 |
  
4. **보내기**를 클릭하고 응답 본문을 검토합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Maps 검색 서비스](https://docs.microsoft.com/rest/api/maps/search) API 설명서를 살펴봅니다.
- 모범 [사례](https://docs.microsoft.com/azure/azure-maps/how-to-use-best-practices-for-search)에 대해 알아보세요.