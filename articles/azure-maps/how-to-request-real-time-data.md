---
title: Azure Maps에서 실시간 데이터를 요청 하는 방법 | Microsoft Docs
description: Azure Maps 모바일 서비스를 사용 하 여 실시간 데이터를 요청 합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: aaab5ef4d8fc3d60a12f9e9f85f2846695fd1ab4
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329669"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Azure Maps Mobility Service를 사용 하 여 실시간 데이터를 요청 합니다.

이 문서에서는 Azure Maps를 사용 하는 방법을 보여 줍니다 [Mobility Service](https://aka.ms/AzureMapsMobilityService) 실시간 전송 데이터를 요청 합니다.

이 문서에서는 알아봅니다 방법:


 * 지정 된 중지에 도착 하는 모든 줄에 대 한 다음 실시간으로 도착 요청
 * 지정 된 자전거 도킹 스테이션에 대 한 실시간 정보를 요청 합니다.


## <a name="prerequisites"></a>필수 조건

Azure Maps 공용 전송 Api에 대 한 모든 호출을 하려면 맵 계정 및 키 필요 합니다. 계정 만들기 및 키 검색에 대한 자세한 내용은 [Azure Maps 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.


## <a name="request-real-time-arrivals-for-a-stop"></a>실시간으로 도착 중지에 대 한 요청

특정 공용 전송 중지에 대 한 실시간으로 도착 데이터를 요청 하려면 요청을 수행 해야 합니다 [실시간 도착 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) Azure Maps의 [Mobility Service](https://aka.ms/AzureMapsMobilityService)합니다. 해야 합니다 **metroID** 및 **stopID** 요청을 완료할 수 있습니다. 이러한 매개 변수를 요청 하는 방법에 대 한 자세한 내용은 방법 가이드를 참조 하세요 [공용 전송 경로 요청](https://aka.ms/AMapsHowToGuidePublicTransitRouting)합니다. 

우리의 metro로 "522"를 사용해 보겠습니다 ID입니다. 중지 ID "2060603"를 사용 하 여 "Bellevue – Tacoma-시애틀, WA" 영역에 bus ID에서 중지 metro "Ne 427 St & 162nd a Ne, Bellevue WA"입니다. 이 위치에 다음 모든 라이브 도착에 대 한 다음 5 개 실시간으로 도착 데이터를 요청 하려면 다음 단계를 수행 합니다.

1. 요청을 저장할 컬렉션을 만듭니다. Postman 앱 선택 **새로 만들기**합니다. 에 **새로 만들기** 창에서 **컬렉션**합니다. 컬렉션 이름을 지정 하 고 선택 합니다 **만들기** 단추입니다.

2. 요청을 만들려면 선택 **새로 만들기** 다시 합니다. 에 **새로 만들기** 창에서 **요청**합니다. 입력 한 **요청 이름** 요청에 대 한 요청을 저장 하 고 선택한 위치와 이전 단계에서 만든 컬렉션을 선택 합니다 **저장**합니다.

    ![Postman에서 요청을 만듭니다](./media/how-to-request-transit-data/postman-new.png)

3. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 GET 요청을 만들려면 다음 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=2060603&transitType=bus
    ```

4. 요청이 성공 후 다음과 같은 응답을 받게 됩니다.  해당 매개 변수에 'scheduleType' 정의 예상된 도착 시간 실시간 또는 정적 데이터 기반 인지 여부를 확인 합니다.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 4,
                "scheduleType": "realTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 30,
                "scheduleType": "scheduledTime",
                "patternId": 3860436,
                "line": {
                    "lineId": 2756599,
                    "lineGroupId": 666063,
                    "direction": "forward",
                    "agencyId": 5872,
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": 2060603,
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>자전거 도킹 스테이션에 대 한 실시간 데이터

합니다 [전송 도킹 정보 API 가져오기](https://aka.ms/AzureMapsMobilityTransitDock) Azure Maps Mobility 서비스 지정된 자전거 또는 스쿠터 도킹 스테이션에 대 한 공석 정보와 가용성 같은 정적 및 실시간 정보를 요청할 수 있습니다. 자전거 도킹 스테이션에 대 한 실시간 데이터를 가져오려는 요청을 해 드립니다.

전송 도킹 정보 가져오기 API를 요청 하기 위해 필요 합니다 **dockId** 해당 스테이션에 대 한 합니다. 검색 요청을 수행 하 여 도킹 ID를 가져올 수 있습니다는 [주변 전송 API 가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit) 설정 하는 **objectType** "bikeDock" 매개 변수입니다. 자전거 도킹 스테이션의 실시간 데이터를 가져오려면 다음 단계를 수행 합니다.


### <a name="get-dock-id"></a>도킹 ID 가져오기

가져오려는 **dockID**, 주변 전송 API 가져오기 요청을 수행 하려면 다음 단계를 수행 합니다.

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **Get 도킹 ID**합니다.

2.  작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 다음 요청 URL을 입력 하 고 클릭 **보낼**합니다.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. 요청이 성공 후 다음과 같은 응답을 받게 됩니다. 이제는 합니다 **id** 응답에 사용할 수 있는 나중 전송 도킹 정보 가져오기 API 요청에 쿼리 매개 변수로 합니다.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 30,
                    "lastUpdated": "2019-05-21T20:06:59-04:00",
                    "operatorInfo": {
                        "id": "80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>실시간 자전거 도킹 상태를 가져옵니다.

가져오기 전송 도킹 정보 API 선택한 도킹 스테이션에 대 한 실시간 데이터를 가져오려는 요청을 수행 하려면 다음 단계를 수행 합니다.

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **도킹 실시간 데이터를 가져올**합니다.

2.  작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 다음 요청 URL을 입력 하 고 클릭 **보낼**합니다.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. 요청이 성공 하면 후 다음 구조체의 응답을 받게 됩니다.

    ```JSON
    {
        "availableVehicles": 1,
        "vacantLocations": 29,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-05-21T20:26:47-04:00",
        "operatorInfo": {
            "id": "80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 전송 중인 데이터를 요청 하는 방법에 알아봅니다.

> [!div class="nextstepaction"]
> [전송 중 데이터를 요청 하는 방법](how-to-request-transit-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Service API 설명서](https://aka.ms/AzureMapsMobilityService)
