---
title: Azure Maps Search Service를 사용 하 여 효율적으로 검색 Microsoft Azure 맵
description: Microsoft Azure Maps를 사용 하 여 Search Service에 대 한 모범 사례를 적용 하는 방법을 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 82e0339e02fa2fb27e7b2ca24f65934e3ce4fe23
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209803"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure Maps Search Service에 대 한 모범 사례

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) 에는 다양 한 기능을 제공 하는 api가 포함 되어 있습니다. 예를 들어 검색 주소 API는 특정 위치 주변의 POI (관심 지점) 또는 데이터를 찾을 수 있습니다. 

이 문서에서는 Azure Maps Search Service에서 데이터를 호출할 때 소리 정보를 적용 하는 방법을 설명 합니다. 이 문서에서 배울 내용은 다음과 같습니다.

* 관련 일치 항목을 반환 하는 쿼리를 작성 합니다.
* 검색 결과를 제한 합니다.
* 결과 형식 간의 차이점을 알아봅니다.
* 주소 검색-응답 구조를 읽습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Maps 서비스 Api를 호출 하려면 Azure Maps 계정 및 키가 필요 합니다. 자세한 내용은 [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 및 [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)를 참조 하세요. 

Azure Maps의 인증에 대 한 자세한 내용은 [Azure Maps 인증 관리](./how-to-manage-authentication.md)를 참조 하세요.

> [!TIP]
> Search Service를 쿼리하려면 [Postman 앱](https://www.getpostman.com/apps) 을 사용 하 여 REST 호출을 빌드할 수 있습니다. 또는 선호 하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="best-practices-to-geocode-addresses"></a>Geocode 주소에 대 한 모범 사례

Azure Maps Search Service를 사용 하 여 전체 또는 부분 주소를 검색 하는 경우 API는 검색 쿼리에서 키워드를 읽습니다. 그런 다음 주소의 경도 및 위도 좌표를 반환 합니다. 이 프로세스를 *지 오 코딩*라고 합니다. 

한 국가의 지역에서 코드를 사용 하는 기능은 도로의 데이터의 가용성과 지 오 코딩 서비스의 전체 자릿수에 따라 달라 집니다. 국가 또는 지역별 Azure Maps 지 오 코딩 기능에 대 한 자세한 내용은 [지 오 코딩 적용](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)을 참조 하세요.

### <a name="limit-search-results"></a>검색 결과 제한

 Azure Maps 검색 API를 통해 검색 결과를 적절 하 게 제한할 수 있습니다. 사용자에 게 관련 데이터를 표시할 수 있도록 결과를 제한 합니다.

> [!NOTE]
> 검색 Api는이 문서에서 설명 하는 것 보다 많은 매개 변수를 지원 합니다.

#### <a name="geobiased-search-results"></a>Geobiased 검색 결과

사용자에 대 한 관련 영역에 ias 결과를 추가 하려면 항상 가능한 한 많은 위치 정보를 추가 합니다. 일부 입력 유형을 지정 하 여 검색 결과를 제한 하는 것이 좋습니다.

* `countrySet` 매개 변수를 설정 합니다. 예를 들어 `US,FR`로 설정할 수 있습니다. 기본적으로 API는 전체 세계를 검색 하므로 불필요 한 결과를 반환할 수 있습니다. 쿼리에 `countrySet` 매개 변수가 없으면 검색에서 부정확 한 결과가 반환 될 수 있습니다. 예를 들어 *Bellevue* 이라는 도시를 검색 하면 두 국가에 *Bellevue*라는 도시가 포함 되기 때문에 USA와 프랑스에서 얻은 결과가 반환 됩니다.

* `btmRight` 및 `topleft` 매개 변수를 사용 하 여 경계 상자를 설정할 수 있습니다. 이러한 매개 변수는 맵의 특정 영역으로 검색을 제한 합니다.

* 결과의 관련성 영역에 영향을 주기 위해 `lat` 및 `lon` 좌표 매개 변수를 정의 합니다. `radius` 매개 변수를 사용 하 여 검색 영역의 반경을 설정 합니다.


#### <a name="fuzzy-search-parameters"></a>유사 항목 검색 매개 변수

검색 쿼리에 대 한 사용자 입력을 모를 경우 Azure Maps [검색 유사 항목 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 를 사용 하는 것이 좋습니다. 이 API는 POI 검색 및 지 오 코딩를 정식 *단일 줄 검색*으로 결합 합니다. 

* `minFuzzyLevel` 및 `maxFuzzyLevel` 매개 변수는 쿼리 매개 변수가 사용자가 원하는 정보와 정확히 일치 하지 않는 경우에도 관련 일치 항목을 반환 하는 데 도움이 됩니다. 성능을 최대화 하 고 비정상적인 결과를 줄이려면 검색 쿼리를 `minFuzzyLevel=1`의 기본값으로 설정 하 고 `maxFuzzyLevel=2`합니다. 

    예를 들어 `maxFuzzyLevel` 매개 변수를 2로 설정 하면 검색 용어 *restrant* *식당*에 일치 합니다. 필요할 때 기본 유사 항목 수준을 재정의할 수 있습니다. 

* `idxSet` 매개 변수를 사용 하 여 정확한 결과 형식 집합의 우선 순위를 지정 합니다. 정확한 결과 집합의 우선 순위를 지정 하려면 쉼표로 구분 된 인덱스 목록을 제출 하면 됩니다. 목록에서 항목 순서는 중요 하지 않습니다. Azure Maps는 다음 인덱스를 지원 합니다.

    * `Addr` - **주소 범위**: 주소의 시작과 끝에서 보간된 주소 지점입니다. 이러한 요소는 주소 범위로 표시 됩니다.
    * `Geo` - **지역**: 행정의 행정 부서입니다. 지리는 국가, 주 또는 도시 일 수 있습니다.
    * `PAD` - **지점 주소**: 주소 및 번호를 포함 하는 주소입니다. 인덱스에서 Point 주소를 찾을 수 있습니다. 예제는 *So El Dr 2501*입니다. 지점 주소는 주소에 사용할 수 있는 가장 높은 수준의 정확도를 제공 합니다.  
    * `POI` - **관심 분야**: 지도에서 주목 해야 하는 것으로 간주 되거나 관심을 가질 수 있는 위치를 가리킵니다. [검색 주소 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 는 poi을 반환 하지 않습니다.  
    * `Str` - **거리**: 지도에 거리를 설정 합니다.
    * 두 거리를 교차 하는 **거리 또는 교차점**을 `XStr` - .


#### <a name="usage-examples"></a>사용 예

* `idxSet=POI`-Poi만 검색 합니다. 

* `idxSet=PAD,Addr`-주소만 검색 합니다. `PAD`는 포인트 주소를 나타내고 `Addr`는 주소 범위를 나타냅니다.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>지리 엔터티 형식에 대 한 역방향 geocode 및 필터

[검색 주소 역방향 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)에서 역방향 geocode 검색을 수행 하는 경우 서비스는 관리 영역에 대 한 다각형을 반환할 수 있습니다. 특정 지리 엔터티 형식에 대 한 검색 범위를 좁히려면 요청에 `entityType` 매개 변수를 포함 합니다. 

결과 응답은 지리 ID와 일치 된 엔터티 형식을 포함 합니다. 둘 이상의 엔터티를 제공 하는 경우 끝점은 *사용 가능한 가장 작은 엔터티*를 반환 합니다. 반환 된 geometry ID를 사용 하 여 [검색 Polygon 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)를 통해 지리 기 하 도형을 가져올 수 있습니다.

#### <a name="sample-request"></a>샘플 요청

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>결과 언어 설정

`language` 매개 변수를 사용 하 여 반환 된 검색 결과에 대 한 언어를 설정 합니다. 요청에서 언어를 설정 하지 않으면 기본적으로 Search Service 국가 또는 지역에서 가장 많이 사용 되는 언어를 사용 합니다. 지정 된 언어로 사용할 수 있는 데이터가 없으면 기본 언어가 사용 됩니다. 

자세한 내용은 [지원 되는 언어 Azure Maps](https://docs.microsoft.com/azure/azure-maps/supported-languages)를 참조 하세요.


### <a name="use-predictive-mode-automatic-suggestions"></a>예측 모드 사용 (자동 제안)

부분 쿼리와 일치 하는 항목을 더 찾으려면 `typeahead` 매개 변수를 `true`로 설정 합니다. 이 쿼리는 부분 입력으로 해석 되며 검색은 예측 모드로 전환 됩니다. `typeahead` 매개 변수를 `true`로 설정 하지 않으면 서비스에서는 모든 관련 정보가 전달 된 것으로 간주 합니다.

다음 샘플 쿼리에서는 검색 주소 서비스를 *마이크로*로 쿼리 합니다. 여기에서 `typeahead` 매개 변수는 `true`로 설정 됩니다. 응답은 검색 서비스가 쿼리를 부분 쿼리로 해석 함을 보여 줍니다. 응답에는 자동으로 제안 된 쿼리에 대 한 결과가 포함 됩니다.

#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>특수 문자를 처리 하는 URI 인코딩 

주소 간 주소를 찾으려면 URI를 인코딩하여 주소에서 특수 문자를 처리 해야 합니다. 이 주소 예: *첫 번째 통로 & 공용 연합 주소, 시애틀*. 여기에서 요청을 보내기 전에 앰퍼샌드 문자 (`&`)를 인코딩합니다. 

URI에서 문자 데이터를 인코딩하는 것이 좋습니다. URI에서 백분율 기호 (`%`)와 문자 ' UTF-8 코드에 해당 하는 두 자리 16 진수 값을 사용 하 여 모든 문자를 인코딩합니다.

#### <a name="usage-examples"></a>사용 예

다음 주소로 시작:

```
query=1st Avenue & E 111th St, New York
```

주소 인코딩:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

다음 방법을 사용할 수 있습니다.

JavaScript 또는 TypeScript:
```Javascript
encodeURIComponent(query)
```

C#또는 Visual Basic:
```csharp
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

채워쓰기
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

으로
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>POI 검색에 대 한 모범 사례

POI 검색에서 이름으로 POI 결과를 요청할 수 있습니다. 예를 들어 이름을 기준으로 비즈니스를 검색할 수 있습니다. 

`countrySet` 매개 변수를 사용 하 여 응용 프로그램에 적용 해야 하는 국가를 지정 하는 것이 좋습니다. 기본 동작은 전체 세계를 검색 하는 것입니다. 이러한 광범위 한 검색은 불필요 한 결과를 반환할 수 있으며 검색에는 시간이 오래 걸릴 수 있습니다.

### <a name="brand-search"></a>브랜드 검색

응답의 결과와 정보의 관련성을 개선 하기 위해 POI 검색 응답에는 브랜드 정보가 포함 됩니다. 이 정보를 사용 하 여 응답을 추가로 분석할 수 있습니다.

요청에서 쉼표로 구분 된 브랜드 이름 목록을 제출할 수 있습니다. `brandSet` 매개 변수를 설정 하 여 결과를 특정 브랜드로 제한 하려면 목록을 사용 합니다. 목록에서 항목 순서는 중요 하지 않습니다. 여러 브랜드 목록을 제공 하는 경우 반환 되는 결과는 하나 이상의 목록에 속해야 합니다.

브랜드 검색을 살펴보기 위해 [Poi 범주 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) 요청을 만들어 보겠습니다. 다음 예제에서는 Redmond, 워싱턴의 Microsoft 캠퍼스 근처의 가스를 찾습니다. 응답은 반환 된 각 POI에 대 한 브랜드 정보를 표시 합니다.


#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>공항 검색

검색 POI API를 사용 하 여 공식 코드를 사용 하 여 공항을 찾을 수 있습니다. 예를 들어 *바다* 를 사용 하 여 시애틀-Tacoma 국제 공항을 찾을 수 있습니다. 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>주변 검색

특정 위치에 대 한 POI 결과를 검색 하려면 [주변 API 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)을 사용해 볼 수 있습니다. 끝점은 POI 결과만 반환 합니다. 검색 쿼리 매개 변수를 사용 하지 않습니다. 

결과를 제한 하려면 radius를 설정 하는 것이 좋습니다.

## <a name="understanding-the-responses"></a>응답 이해

Azure Maps Search Service에 주소 검색 요청을 만들어 시애틀의 주소를 찾습니다. 다음 요청 URL에서 `countrySet` 매개 변수를 `US`으로 설정 하 여 USA의 주소를 검색 합니다.

### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>지원 되는 결과 형식

* **점 주소**: 주소와 숫자가 있는 특정 주소가 있는 지도를 가리킵니다. Point Address는 주소에 대해 가장 높은 수준의 정확도를 제공 합니다. 

* **주소 범위**: 거리의 시작과 끝에서 보간된 주소 지점의 범위입니다.  

* **지리**: 국가, 주 또는 도시와 같은 육지의 관리 부서를 나타내는 지도의 영역입니다. 

* **Poi**: 주목 하 고 관심 있는 지도를 가리킵니다.

* **거리**: 지도의 거리입니다. 주소는 주소를 포함 하는 번 지의 위도 및 경도 좌표로 확인 됩니다. 집 번호가 처리 되지 않을 수 있습니다. 

* **교차 주소**: 교집합. 교차 거리는 두 거리를 교차 하는 교차점을 나타냅니다.

### <a name="response"></a>응답

응답 구조를 살펴보겠습니다. 다음 응답에서 결과 개체의 형식이 서로 다릅니다. 신중히 살펴보면 다음과 같은 세 가지 유형의 결과 개체가 표시 됩니다.

* 점 주소
* Street
* 교차 주소

주소 검색에서 Poi를 반환 하지 않습니다.  

각 응답 개체에 대 한 `Score` 매개 변수는 일치 점수가 동일한 응답의 다른 개체의 점수와 어떻게 관련 되는지를 나타냅니다. 응답 개체 매개 변수에 대 한 자세한 내용은 [검색 주소 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)를 참조 하세요.

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>geometry

*기 하 도형* 에 대 한 응답 형식에는 `geometry` 및 `id`의 `dataSources` 개체에서 반환 되는 기 하 도형 ID가 포함 될 수 있습니다. 예를 들어, [검색 Polygon 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) 를 사용 하 여 GeoJSON 형식으로 geometry 데이터를 요청할 수 있습니다. 이 형식을 사용 하 여 엔터티 집합에 대 한 도시나 공항 개요를 얻을 수 있습니다. 그런 다음이 경계 데이터를 사용 하 여 지 [오를 설정](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) 하거나 [기 하 도형 내에서 poi을 검색할](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)수 있습니다.


[검색 주소](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) Api 또는 [검색 유사 항목](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) api에 대 한 응답에는 `geometry` 및 `id`의 `dataSources` 개체에서 반환 되는 geometry ID가 포함 될 수 있습니다.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>다음 단계

* [Azure Maps Search Service 요청을 빌드하는 방법을](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)알아봅니다.
* Azure Maps [SEARCH SERVICE API 설명서](https://docs.microsoft.com/rest/api/maps/search)를 탐색 합니다. 
