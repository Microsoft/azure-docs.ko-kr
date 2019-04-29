---
title: Azure Maps 검색 서비스를 사용 하 여 효율적으로 검색 하는 방법 | Microsoft Docs
description: Azure Maps 검색 서비스를 사용 하 여 검색에 대 한 모범 사례를 사용 하는 방법 알아보기
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: e60b202a32745062d2078d9721dfeba7aba69bda
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763894"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Azure Maps 검색 서비스를 사용 하는 모범 사례

Azure Maps [Search](https://docs.microsoft.com/rest/api/maps/search) 특정 위치 주변 관심 지점 (POI) 데이터를 검색 주소 검색에서 예를 들어, 다양 한 기능을 사용 하 여 Api를 포함 되어 있습니다. 이 문서에서는 Azure Maps 검색 서비스를 통해 데이터를 호출 하는 최선의 방법을 공유 합니다. 다음 방법을 알게 됩니다.

* 관련 된 일치 항목을 반환 하는 쿼리를 작성 합니다.
* 검색 결과 제한
* 다양 한 결과 형식 간의 차이점에 알아봅니다.
* 주소 검색 응답 구조를 읽기


## <a name="prerequisites"></a>필수 조건

맵 서비스 API에 대한 호출을 생성하려면 맵 계정 및 키가 필요합니다. 계정 만들기 및 키 검색에 대한 자세한 내용은 [Azure Maps 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요.

> [!Tip]
> 검색 서비스를 쿼리를 사용할 수 있습니다 합니다 [Postman 앱](https://www.getpostman.com/apps) REST 빌드를 호출 하거나 사용할 수 있습니다 선호 하는 모든 API 개발 환경입니다.


## <a name="best-practices-for-geocoding"></a>지 오 코딩에 대 한 모범 사례

Azure Maps 검색 서비스를 사용 하 여 전체 또는 일부 주소를 검색할 때 검색 용어를 사용 하 고 주소의 경도 및 위도 좌표를 반환 합니다. 이 프로세스를 지 오 코딩을 이라고 합니다. 국가의 지오코딩하는 기능은 도로 데이터 검사 및 지오코딩 서비스의 지오코딩 정밀도에 따라 달라집니다.

참조 [지 오 코딩 검사](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) 국가/지역에서 Azure Maps 지 오 코딩 기능에 대 한 자세한 내용을 보려면.

### <a name="limit-search-results"></a>검색 결과 제한

   이 섹션에서는 Azure Maps 검색 Api를 사용 하 여 검색 결과 제한 하는 방법을 배웁니다. 

   > [!Note]
   > 모든 검색 Api 아래에 나열 된 매개 변수 완전 하 게 지원

   **지역 바이어스 검색 결과**

   지역 바이어스 순서에서와 관련 된 사용자에 게 영역의 결과가 항상 추가 해야 자세한 가능한 최대 위치 입력 합니다. 검색 결과 제한 하려면 다음 입력된 유형을 추가 하는 것이 좋습니다.

   1. 설정 된 `countrySet` 매개 변수를 예를 들어 "미국, FR"입니다. 기본 검색 동작을 잠재적으로 불필요 한 결과 반환 하는 전체 세계를 검색 하는 것입니다. 쿼리에 포함 되어 있지 않으면 `countrySet` 매개 변수를 검색 부정확 한 결과 반환할 수 있습니다. 예를 들어 명명 도시를 검색할 **Bellevue** 라는 도시가 많습니다 되므로 미국 및 프랑스에서 결과가 반환 됩니다 **Bellevue** 미국 및 프랑스에서.

   2. 사용할 수는 `btmRight` 및 `topleft` 경계를 설정 하는 매개 변수 상자 검색 맵에서 특정 영역을 제한 합니다.

   3. 결과 관련성이 높은 영역에 영향을 줄을 정의할 수 있습니다 합니다 `lat`및 `lon` 매개 변수를 조정 하 고 사용 하 여 검색 영역 radius 설정를 `radius` 매개 변수입니다.


   **유사 항목 검색 매개 변수**

   1. `minFuzzyLevel` 및 `maxFuzzyLevel`, 쿼리 매개 변수는 원하는 정보에 해당 정확 하 게 수행 하는 경우에 관련 된 일치 항목을 반환 하는 데 도움이 됩니다. 대부분의 검색 쿼리는 기본적으로 `minFuzzyLevel=1` 고 `maxFuzzyLevel=2` 성능을 얻고 이상한 결과 줄이는 하 합니다. 예로 검색 단어 "restrant", "음식점"에 때 일치는 `maxFuzzyLevel` 2로 설정 됩니다. 요청에 따라 기본 유사 항목 수준은 재정의할 수 있습니다. 

   2. 사용 하 여 반환 될 결과 형식의 정확한 집합을 지정할 수도 있습니다는 `idxSet` 매개 변수입니다. 이 목적을 위해 인덱스의 쉼표로 구분 된 목록에 제출할 수 있습니다, 항목 순서는 중요 하지 않습니다. 다음은 지원 되는 인덱스입니다.

       * `Addr` - **주소 범위**: 일부 거리에 대 한 가지 거리; 시작과 끝에서 보간됩니다 주소 지점 주소 범위와 해당 지점에 표시 됩니다.
       * `Geo` - **지역**: 즉는 토지 관리 부서를 나타내는 지도, 국가, 상태, city의 영역입니다.
       * `PAD` - **Point Address**:  거리 이름 및 번호를 사용 하 여 특정 주소 찾을 수 있는 인덱스의 예를 들어 Soquel Dr 2501 맵에서 가리킵니다. 것이 가장 높은 수준의 정확도 주소에 대해 사용할 수 있습니다.  
       * `POI` - **관심 지점**: 주의 만한 하는 흥미로운 주제가 되겠지만 맵에서 가리킵니다.  [검색 주소를 가져올](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) poi로 향하는 반환 되지 않습니다.  
       * `Str` - **거리가**: 지도에서 거리의 표현입니다.
       * `XStr` - **거리가/교집합 간**:  교차점;의 표현 두 거리가 교차 하는 위치입니다.


       **사용법 예제**:

       * idxSet POI (관심 지점 검색) = 

       * idxSet 패드 Addr = (검색 주소를 통해서만, 패드 Point Address, Addr = = 주소 범위)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>역방향 geocode와 geography 엔터티 형식 필터

사용 하 여 역방향 geocode 검색을 수행할 때 [검색 주소 역방향 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse), 서비스에 관리 영역에 대 한 다각형을 반환 하는 기능입니다. 매개 변수를 제공 하 여 `entityType` 요청에 지정 된 geography 엔터티 형식에 대 한 검색을 좁힐 수 있습니다. Geography ID 뿐 아니라 일치 하는 엔터티 형식에 대 한 결과 응답 포함 됩니다. 둘 이상의 엔터티를 제공한 끝점은 반환 되는 **사용 가능한 가장 작은 엔터티**합니다. 기 하 도형 ID를 통해 해당 지리의 기 하 도형을 가져오는 데 사용할 수 있습니다 반환 [다각형 가져오기 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)합니다.

**샘플 요청:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**응답:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>검색 결과 언어

`language` 매개 변수 설정할 수 있는 언어 검색에서 결과 반환할 수 있습니다. 요청에 언어를 설정 하지 않으면, search 서비스는 가장 일반적인 언어 국가/지역에 자동으로 기본값은입니다. 또한 지정된 된 언어의 데이터를 사용할 수 없는 경우에 기본 언어가 사용 됩니다. 참조 [지원 되는 언어](https://docs.microsoft.com/azure/azure-maps/supported-languages) 국가/지역에서 Azure Maps 서비스와 관련 하 여 지원 되는 언어 목록은 합니다.


### <a name="predictive-mode-auto-suggest"></a>예측 모드 (자동 제안)

일부 쿼리에 대 한 더 많은 일치 항목을 찾으려면 `typeahead` 매개 변수 'true'로 설정 해야 합니다. 쿼리를 부분 입력으로 해석 됩니다 하 고 검색 예측 모드로 전환 됩니다. 그렇지 않은 경우 서비스는 모든 관련 정보에 전달 된 가정 합니다.

샘플에서 쿼리 아래 볼 수 있습니다 "Microso"에 대 한 주소 서비스 검색 쿼리를 사용 하 여 합니다 `typeahead` 매개 변수 설정 **true**합니다. 응답에 관찰 되는 경우에 검색 서비스 부분 쿼리로 쿼리를 해석 하 고 자동으로 제안 된 쿼리에 대 한 결과 포함 하는 응답을 볼 수 있습니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**응답:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>특수 문자를 처리 하도록 인코딩 URI 

주소, 즉, "첫 번째 셈이 & Union Street, Seattle", 교차 특수 문자를 찾으려면 요청을 보내기 전에 인코딩해야 '&' 해야 합니다. '%' 문자를 사용 하 여 모든 문자는 인코딩됩니다 여기서 및는 utf-8 문자에 해당 하는 두 자리 16 진수 값을 URI에 문자 데이터를 인코딩 것이 좋습니다.

**사용법 예제**:

검색 주소를 가져옵니다.

```
query=1st Avenue & E 111th St, New York
```

 로 인코딩할 수 됩니다.

```
query"=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


다른 언어에 사용할 다른 메서드는 다음과 같습니다. 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#/VB:
```C#
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Ruby:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

이동 합니다.
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>지점 검색에 대 한 모범 사례

지점 (POI) 관련 검색을 사용 하면 이름별, 이름별 검색 비즈니스 예를 들어, POI 결과 요청할 수 있습니다. 좋습니다 사용 하는 `countrySet` 매개 변수를 기본 동작을 잠재적으로 불필요 한 결과 반환 하는 전체 세계를 검색 하거나 검색 시간이 더 길어지게 됩니다 하는 대로 응용 프로그램, 검사 해야 하는 국가 지정 합니다.

### <a name="brand-search"></a>브랜드 검색

응답의 정보와 결과의 관련성을 향상 시키려면 관심 지점 (POI) 검색 응답에는 추가 응답을 구문 분석에 사용할 수 있는 브랜드 정보를 포함 합니다.

확인을 [POI 범주 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) Microsoft 캠퍼스 (미국 워싱턴 레드먼드) 가까운 주유소에 대 한 요청입니다. 응답에 관찰 되는 경우 반환 된 각 지점에 대 한 브랜드 정보를 볼 수 있습니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**응답:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>공항 검색

POI Search 공항 공식 공항 코드를 사용 하 여 검색을 지원 합니다. 예를 들어 **바다** (시애틀 Tacoma 국제 공항). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>근접 검색

특정 위치 주변 POI 결과만 검색 하는 [근처의 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) 올바른 선택 일 수 있습니다. 이 끝점 POI 결과만 반환 됩니다 및 검색 쿼리 매개 변수에서 사용 하지 않습니다. 결과 제한 하려면 반지름을 설정 하는 것이 좋습니다.

## <a name="understanding-the-responses"></a>응답 이해

Azure Maps로 주소 검색 요청을 만들어 보겠습니다 [search 서비스](https://docs.microsoft.com/rest/api/maps/search) 시애틀에서 주소입니다. 설정한 아래 요청 URL을 신중 하 게 보면를 `countrySet` 매개 변수를 **미국** 는 미국 주소에 대 한 검색 합니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

추가로 아래 응답 구조에서 보도록 하겠습니다. 응답에서 결과 개체의 결과 형식이 서로 다릅니다. 관찰 신중 하 게 세 가지 유형의 결과 개체 했습니다 보면 하는 경우 "Point Address", "Street" 및 "교차 Street". 주소를 검색 하는 지점 반환 하지 않는 알 수 있습니다. `Score` 각 응답 개체에 대 한 매개 변수는 상대 일치 점수가 같은 응답에서 다른 개체의 점수를 나타냅니다. 참조 [검색 주소 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 응답 개체 매개 변수에 대 한 자세한 정보를 알아야 합니다.

**결과의 지원 되는 형식:**

* **지점 주소:** 거리 이름 및 번호를 사용 하 여 특정 주소를 사용 하 여 맵에서 가리킵니다. 가장 높은 수준의 정확도 주소에 대해 사용할 수 있습니다. 

* **주소 범위:**  일부 거리에 대 한 가지 거리; 시작과 끝에서 보간됩니다 주소 지점 주소 범위와 해당 지점에 표시 됩니다. 

* **Geography:** 즉는 토지 관리 부서를 나타내는 지도, 국가, 상태, city의 영역입니다. 

* **-POI (관심 지점):** 주의 만한 하는 흥미로운 주제가 되겠지만 맵에서 가리킵니다.

* **주소:** 지도에서 거리의 표현입니다. 주소는 주소를 포함 하는 거리 위도/경도 좌표를 확인 합니다. 번지는 처리되지 않을 수 있습니다. 

* **교차로:** 교차 합니다. 교차점;의 표현 두 거리가 교차 하는 위치입니다.

**응답:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>기하 도형

응답 형식이 면 **기 하 도형**, 반환 되는 기 하 도형 ID를 포함할 수 있습니다는 **dataSources** "geometry" 및 "id" 아래에 있는 개체입니다. 예를 들어 [다각형 가져오기 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) 엔터티 집합에 대 한 도시 또는 공항 개요 등 GeoJSON 형식으로의 기 하 도형 데이터를 요청할 수 있습니다. 에 대해이 경계 데이터를 사용할 수 있습니다 [지 오 펜싱](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) 하거나 [기 하 도형 내에서 검색 poi로 향하는](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)합니다.


[주소를 검색할](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 또는 [유사 항목 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API 응답에 포함 될 수 있습니다 합니다 **기 하 도형 ID** "geometry" 및 "id" 아래에 있는 데이터 원본 개체에서 반환 되는 합니다.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>다음 단계

* 에 대해 알아봅니다 [Azure Maps 검색 서비스 요청을 작성 하는 방법을](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)합니다.
* Azure Maps 살펴보기 [검색 서비스 API 설명서](https://docs.microsoft.com/rest/api/maps/search)합니다. 