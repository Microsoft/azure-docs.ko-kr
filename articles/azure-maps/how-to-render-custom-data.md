---
title: Azure Maps에서 래스터 지도에 사용자 지정 데이터를 렌더링 하는 방법 | Microsoft Docs
description: Azure Maps에서 래스터 지도에 사용자 지정 데이터를 렌더링 합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 46f08aaa33563f620e7a011620730249e903f7b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794618"
---
# <a name="render-custom-data-on-a-raster-map"></a>래스터 지도에 사용자 지정 데이터를 렌더링 합니다.

이 문서를 사용 하는 방법에 설명 합니다 [정적 이미지 서비스](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 래스터 지도 위에 오버레이 허용 하도록 이미지 혼합 기능을 사용 하 여 합니다. 이미지 구성에 사용자 지정 압정, 레이블 및 기 하 도형 오버레이 같은 추가 데이터는 래스터 타일을 다시 시작 하는 기능 포함 됩니다.

사용자 지정 압정, 레이블 및 기 하 도형 오버레이 렌더링 하려면 Postman 응용 프로그램을 사용할 수 있습니다. Azure Maps를 사용할 수 있습니다 [데이터 서비스 Api](https://docs.microsoft.com/rest/api/maps/data) 저장 및 오버레이 렌더링 합니다.


## <a name="prerequisites"></a>필수 조건

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료 하려면 먼저 해야 [Azure Maps 계정 만들기](how-to-manage-account-keys.md) S1 가격 책정 계층에서입니다.

## <a name="render-pushpins-with-labels-and-a-custom-image"></a>압정 레이블 및 사용자 지정 이미지를 사용 하 여 렌더링

> [!Note]
> 이 섹션의 절차에는 S0 또는 S1 가격 책정 계층에는 Azure Maps 계정이 필요합니다.

Azure Maps 계정 S0 계층 지원의 단일 인스턴스만 `pins` 매개 변수입니다. 사용자 지정 이미지를 사용 하 여 URL 요청에 지정 된 최대 5 개의 압정을 렌더링할 수 있습니다.

레이블 및 사용자 지정 이미지를 사용 하 여 압정을 렌더링 하려면 이러한 단계를 완료 합니다.

1. 요청을 저장할 컬렉션을 만듭니다. Postman 앱 선택 **새로 만들기**합니다. 에 **새로 만들기** 창에서 **컬렉션**합니다. 컬렉션 이름을 지정 하 고 선택 합니다 **만들기** 단추입니다. 

2. 요청을 만들려면 선택 **새로 만들기** 다시 합니다. 에 **새로 만들기** 창에서 **요청**합니다. 입력 한 **요청 이름** 압정에 대 한 요청을 저장 하 고 선택한 위치와 이전 단계에서 만든 컬렉션을 선택 합니다 **저장**합니다.
    
    ![Postman에서 요청을 만듭니다](./media/tutorial-geofence/postman-new.png)

3. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 GET 요청을 만들려면 다음 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    결과 이미지는 다음과 같습니다.

    ![레이블이 있는 압정을 사용자 지정](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps 데이터 스토리지에서 데이터 가져오기

> [!Note]
> 이 섹션의 절차는 Azure Maps 계정 가격 책정 계층 S1에에서 필요합니다.

사용 하 여 경로 및 pin 위치 정보를 얻을 수도 있습니다는 [데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)합니다. 경로 및 핀 데이터를 업로드하려면 아래 단계를 수행합니다.

1. Postman 앱에서 이전 섹션에서 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 POST HTTP 메서드를 선택 하 고 POST 요청을 확인 하려면 다음 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. 에 **Params** 탭을 POST 요청 URL에 사용 되는 다음 키/값 쌍을 입력 합니다. 대체는 `subscription-key` Azure Maps 구독 키를 사용 하 여 값입니다.
    
    ![Postman에서 키/값 매개 변수](./media/how-to-render-custom-data/postman-key-vals.png)

3. 에 **본문** 탭 원시 입력된 형식을 선택 하 고 드롭다운 목록에서 입력된 형식으로 JSON을 선택 합니다. 이 JSON으로 업로드할 데이터를 제공 합니다.
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. 선택 **보낼** 응답 헤더를 검토 합니다. Location 헤더에 액세스 하거나 나중에 사용할 데이터를 다운로드 하는 데 사용 하는 URI를 포함 합니다. 업로드된 데이터에 대한 고유한 `udId`도 포함됩니다.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{udId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

5. 사용 된 `udId` 맵에서 기능을 렌더링 하는 데이터 업로드 API에서 받은 값입니다. 이 작업을 수행 하려면 이전 섹션에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 GET 요청을 수행 하려면이 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

6. 결과 이미지는 다음과 같습니다.

    ![Azure Maps 데이터 스토리지에서 데이터 가져오기](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>색상과 불투명도 사용 하 여 다각형 렌더링

> [!Note]
> 이 섹션의 절차는 Azure Maps 계정 가격 책정 계층 S1에에서 필요합니다.


[경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)와 스타일 한정자를 사용하여 다각형의 모양을 수정할 수 있습니다.

1. Postman 앱에서 이전에 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 색상과 불투명도 사용 하 여 다각형을 렌더링 하는 GET 요청을 구성 하려면 다음 URL을 입력 합니다.
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription--key}
    ```

결과 이미지는 다음과 같습니다.

![불투명 한 다각형을 렌더링 합니다.](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>원 및 사용자 지정 레이블이 있는 압정 렌더링

> [!Note]
> 이 섹션의 절차는 Azure Maps 계정 가격 책정 계층 S1에에서 필요합니다.


가능 압정 및 해당 레이블 크게 또는 작게를 사용 하 여는 `sc` 확장 스타일 한정자입니다. 이 한정자에는 0 보다 큰 값을 사용 합니다. 값 1이 표준 배율입니다. 값이 1보다 크면 핀이 확대되고, 값이 1보다 작으면 축소됩니다. 스타일 한정자에 대 한 자세한 내용은 참조 하세요. [정적 이미지 서비스에 대 한 경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)합니다.


원 및 사용자 지정 레이블이 있는 압정을 렌더링 하려면 다음이 단계를 수행 합니다.

1. Postman 앱에서 이전에 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 GET 요청을 수행 하려면이 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

결과 이미지는 다음과 같습니다.

![사용자 지정 압정을 사용 하 여 원을 렌더링합니다](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>다음 단계


* [Azure Maps 지도 이미지 가져오기 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 문서를 살펴봅니다.
* Azure Maps 데이터 서비스에 대 한 자세한 내용은 참조는 [service 설명서](https://docs.microsoft.com/rest/api/maps/data)합니다.

