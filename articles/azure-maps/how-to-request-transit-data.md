---
title: 전송 데이터 요청 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 지도 모바일 서비스를 사용 하 여 공용 전송 데이터를 요청 하는 방법을 설명 합니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 17fbc78b34237c6f5e1e688a88c68bb0a321884f
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209871"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps 모바일 서비스를 사용 하 여 공용 전송 데이터 요청 

이 문서에서는 Azure Maps [모바일 서비스](https://aka.ms/AzureMapsMobilityService) 를 사용 하 여 공용 전송 데이터를 요청 하는 방법을 보여 줍니다. 전송 데이터에는 전송 중지, 경로 정보 및 이동 시간 예측이 포함 됩니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

* [Metro 영역 다운로드 API](https://aka.ms/AzureMapsMobilityMetro) 를 사용 하 여 METRO 영역 ID 가져오기
* 가까운 전송 서비스 [가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit) 를 사용 하 여 근접 한 전송 요청을 중지 합니다.
* 공개 전송을 사용 하 여 경로를 계획 하는 [Get 전송 경로 API](https://aka.ms/AzureMapsMobilityTransitRoute) 를 쿼리 합니다.
* [전송 여행 일정 가져오기 API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)를 사용 하 여 경로에 대 한 전송 경로 기 하 도형 및 자세한 일정을 요청 합니다.


## <a name="prerequisites"></a>사전 요구 사항

Azure Maps 공용 전송 Api에 대 한 호출을 수행 하려면 먼저 Azure Maps 계정 및 구독 키가 있어야 합니다. 자세한 내용은 [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 의 지침에 따라 Azure Maps 계정을 만드세요. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account) 의 단계에 따라 계정에 대 한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.


이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.


## <a name="get-a-metro-area-id"></a>Metro 영역 ID 가져오기

특정 대도시 영역에 대 한 전송 정보를 요청 하려면 해당 영역의 `metroId` 필요 합니다. [Metro 영역 가져오기 API](https://aka.ms/AzureMapsMobilityMetro) 를 사용 하면 Azure Maps 모바일 서비스를 사용할 수 있는 metro 영역을 요청할 수 있습니다. 응답에는 `metroId`, `metroName`및 metro 영역 형상의 표현 (예: GeoJSON 형식)이 포함 됩니다.

시애틀-Tacoma metro 영역 ID에 대 한 Metro 영역을 가져오도록 요청 하겠습니다. Metro 영역에 대 한 ID를 요청 하려면 다음 단계를 완료 합니다.

1. Postman 앱을 열고 요청을 저장할 컬렉션을 만들어 보겠습니다. Postman 앱의 맨 위 근처에서 **새로 만들기**를 선택 합니다. **새로 만들기** 창에서 **컬렉션**을 선택 합니다.  컬렉션 이름을로 하 고 **만들기** 단추를 선택 합니다.

2. 요청을 만들려면 **새로 만들기** 를 다시 선택 합니다. **새로 만들기** 창에서 **요청**을 선택 합니다. 요청에 대 한 **요청 이름을** 입력 합니다. 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택 합니다. 그런 다음 **저장**을 선택합니다.
    
    ![Postman에서 요청 만들기](./media/how-to-request-transit-data/postman-new.png)

3. 작성기 탭에서 **get** HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 get 요청을 만듭니다. `{subscription-key}`를 Azure Maps 기본 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 요청이 성공적으로 완료 되 면 다음 응답을 받게 됩니다.

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. `metroId`를 복사 하 고 나중에 사용 해야 합니다.

## <a name="request-nearby-transit-stops"></a>가까운 전송 중지 요청

[주변 전송](https://aka.ms/AzureMapsMobilityNearbyTransit) 서비스를 Azure Maps 하 여 전송 개체를 검색할 수 있습니다.  API는 지정 된 위치에 대 한 공개 전송 정지 및 공유 bikes와 같은 전송 개체 세부 정보를 반환 합니다. 다음으로 서비스에 대 한 요청을 수행 하 여 지정 된 위치 주위의 300-미터 반경 내에서 가까운 공용 전송 중지를 검색 합니다. 요청에서 이전에 검색 한 `metroId`를 포함 해야 합니다.

[주변 전송](https://aka.ms/AzureMapsMobilityNearbyTransit)에 대 한 요청을 수행 하려면 다음 단계를 수행 합니다.

1. Postman에서 **요청 가져오기** | **새 요청** 을 클릭 하 고 **근처에서 중지**하는 이름을 클릭 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같이 표시 됩니다.

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

응답 구조를 신중 하 게 관찰 하면 각 전송 개체에 대 한 매개 변수가 포함 된 것을 알 수 있습니다. 각 전송 개체에는 `id`, `type`, `stopName`, `mainTransitType`, `mainAgencyName`및 개체의 위치 (좌표)와 같은 매개 변수가 있습니다.

학습을 위해 다음 섹션의 경로에 대 한 버스 정지의 `id`를 원본으로 사용 합니다.  


## <a name="request-a-transit-route"></a>전송 경로 요청

Azure Maps [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute) 는 여행 계획을 허용 합니다. 원본에서 대상으로 가능한 가장 적합 한 경로 옵션을 반환 합니다. 이 서비스는 탐색, biking 및 공개 전송을 비롯 한 다양 한 유형의 여행 모드를 제공 합니다. 다음으로 가장 가까운 버스 정지의 경로를 시애틀의 공간 니 들 타워로 검색 합니다.

### <a name="get-location-coordinates-for-destination"></a>대상에 대 한 위치 좌표 가져오기

니 들 탑의 위치 좌표를 얻기 위해 Azure Maps [유사 항목 Search Service](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)를 사용할 수 있습니다.

유사 항목 검색 서비스에 대 한 요청을 만들려면 다음 단계를 수행 합니다.

1. Postman에서 **새 요청** | **get 요청** 을 클릭 하 고 이름을 **location 좌표를 가져옵니다**.

2.  작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 요청 URL을 입력 한 후 **보내기**를 클릭 합니다.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. 응답을 신중 하 게 살펴보면 공간 니 들 검색 결과에 여러 위치가 포함 되어 있습니다. 각 결과 **는 위치 아래의**위치 좌표를 포함 합니다. `lat`를 복사 하 고 첫 번째 결과의 **위치** 에 `lon` 합니다.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>요청 경로

경로 요청을 만들려면 다음 단계를 완료 합니다.

1. Postman에서 **요청 가져오기** | **새 요청** 을 클릭 하 고 이름으로 **경로 정보를 가져옵니다**.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    `modeType` 및 `transitType` 매개 변수를 지정 하 여 버스에 대 한 공용 전송 경로를 요청 합니다. 요청 URL에는 이전 섹션에서 검색 된 위치가 포함 되어 있습니다. `originType`에는 이제 **Stopid**가 있습니다. `destionationType`의 경우 **위치가**있습니다.

    [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute)에 대 한 요청에서 사용할 수 있는 [URI 매개 변수 목록을](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) 참조 하세요. 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같습니다.

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. 신중 하 게 관찰 하면 응답에 여러 개의 **버스** 경로가 있습니다. 각 경로에는 고유한 일정 **ID** 와 경로의 각 다리를 설명 하는 요약이 있습니다. 경로 레그는 두 중지 waypoints 간의 경로 부분입니다. 그런 다음 응답에서 `itineraryId`를 사용 하 여 가장 빠른 경로에 대 한 세부 정보를 요청 합니다.

## <a name="request-fastest-route-itinerary"></a>가장 빠른 경로 요청 일정표

Azure Maps [전송 여행 서비스 가져오기](https://aka.ms/AzureMapsMobilityTransitItinerary) 서비스를 사용 하면 [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute) 서비스에서 반환 된 경로의 일정 **ID** 를 사용 하 여 특정 경로에 대 한 데이터를 요청할 수 있습니다. 요청을 수행 하려면 다음 단계를 완료 합니다.

1. Postman에서 **요청 가져오기** | **새 요청** 을 클릭 하 고 **전송 정보를 가져옵니다**.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 합니다. API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    응답에 경로에 대 한 공개 전송 및 단계별 탐색에 대 한 사용 중지 정보를 포함 하도록 `detailType` 매개 변수를 **geometry** 로 설정 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같습니다.

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모바일 서비스 API 설명서](https://aka.ms/AzureMapsMobilityService)

