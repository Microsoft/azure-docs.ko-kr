---
title: 래스터 맵에서 사용자 지정 데이터 렌더링 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure Maps 정적 이미지 서비스를 사용 하 여 래스터 맵에 사용자 지정 데이터를 렌더링 하는 방법을 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 78068a8deda68434daaeb18cc994099d74d9ccdf
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209956"
---
# <a name="render-custom-data-on-a-raster-map"></a>래스터 맵에서 사용자 지정 데이터 렌더링

이 문서에서는 이미지 컴퍼지션 기능과 함께 [정적 이미지 서비스](https://docs.microsoft.com/rest/api/maps/render/getmapimage)를 사용 하 여 래스터 맵 위에 오버레이를 허용 하는 방법을 설명 합니다. 이미지 컴포지션에는 사용자 지정 압정, 레이블 및 기 하 오버레이와 같은 추가 데이터를 사용 하 여 래스터 타일을 다시 가져오는 기능이 포함 되어 있습니다.

사용자 지정 압정, 레이블 및 기 하 도형 오버레이를 렌더링 하기 위해 Postman 응용 프로그램을 사용할 수 있습니다. Azure Maps [데이터 서비스 api](https://docs.microsoft.com/rest/api/maps/data) 를 사용 하 여 오버레이를 저장 하 고 렌더링할 수 있습니다.

> [!Tip]
> Azure Maps Web SDK를 사용 하 여 웹 페이지에 정적 이미지 서비스를 사용 하는 것 보다 간단한 지도를 표시 하는 것이 훨씬 더 비용 효율적인 경우가 종종 있습니다. 웹 SDK는 지도 타일을 사용 하며 사용자가 맵을 계획 하 고이를 확대/축소 하는 경우가 아니면 맵 부하 당 트랜잭션의 일부만 생성 합니다. Azure Maps 웹 SDK에는 패닝 및 확대/축소를 사용 하지 않도록 설정 하는 옵션이 있습니다. 또한 Azure Maps 웹 SDK는 정적 지도 웹 서비스와는 다른 다양 한 데이터 시각화 옵션 집합을 제공 합니다.  

## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

이 문서의 절차를 완료 하려면 먼저 Azure Maps 계정을 만들고 Maps 계정 키를 가져와야 합니다. 계정 [만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 의 지침에 따라 Azure Maps 계정 구독을 만들고 [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account) 의 단계에 따라 계정에 대 한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>레이블 및 사용자 지정 이미지를 사용 하 여 압정 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 S0 또는 S1의 Azure Maps 계정이 필요 합니다.

Azure Maps 계정 S0 계층은 `pins` 매개 변수의 단일 인스턴스만 지원 합니다. URL 요청에 지정 된 사용자 지정 이미지를 사용 하 여 최대 5 개의 압정을 렌더링할 수 있습니다.

레이블 및 사용자 지정 이미지를 사용 하 여 압정를 렌더링 하려면 다음 단계를 완료 합니다.

1. 요청을 저장할 컬렉션을 만듭니다. Postman 앱에서 **새로 만들기**를 선택 합니다. **새로 만들기** 창에서 **컬렉션**을 선택 합니다. 컬렉션 이름을로 하 고 **만들기** 단추를 선택 합니다. 

2. 요청을 만들려면 **새로 만들기** 를 다시 선택 합니다. **새로 만들기** 창에서 **요청**을 선택 합니다. 압정에 대 한 **요청 이름을** 입력 합니다. 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택 합니다. 그런 다음 **저장**을 선택합니다.
    
    ![Postman에서 요청 만들기](./media/how-to-render-custom-data/postman-new.png)

3. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 GET 요청을 만듭니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    결과 이미지는 다음과 같습니다.

    ![레이블이 있는 사용자 지정 압정](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure Maps 데이터 스토리지에서 데이터 가져오기

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.

[데이터 업로드 API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)를 사용 하 여 경로 및 pin 위치 정보를 가져올 수도 있습니다. 경로 및 핀 데이터를 업로드하려면 아래 단계를 수행합니다.

1. Postman 앱에서 이전 섹션에서 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 POST HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 POST 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. **매개 변수** 탭에서 POST 요청 URL에 사용 되는 다음 키/값 쌍을 입력 합니다. `subscription-key` 값을 Azure Maps 구독 키로 바꿉니다.
    
    ![Postman의 키/값 매개 변수](./media/how-to-render-custom-data/postman-key-vals.png)

3. **본문** 탭에서 원시 입력 형식을 선택 하 고 드롭다운 목록에서 JSON을 입력 형식으로 선택 합니다. 업로드할 데이터로이 JSON 제공:
    
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

4. **보내기** 를 선택 하 고 응답 헤더를 검토 합니다. 요청이 성공적으로 완료 되 면 Location 헤더는 상태 URI를 포함 하 여 업로드 요청의 현재 상태를 확인 합니다. 상태 URI는 다음과 같은 형식입니다.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. 상태 URI를 복사 하 고 Azure Maps 계정 구독 키 값을 사용 하 여 구독 키 매개 변수를 추가 합니다. 데이터를 업로드 하는 데 사용한 것과 동일한 계정 구독 키를 사용 합니다. 상태 URI 형식은 아래와 같습니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. UdId을 가져오려면 Postman 앱에서 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 합니다. 상태 URI에서 GET 요청을 수행 합니다. 데이터 업로드가 성공적으로 완료 되 면 응답 본문에 udId가 표시 됩니다. UdId를 복사 합니다.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. 데이터 업로드 API에서 받은 `udId` 값을 사용 하 여 맵의 기능을 렌더링 합니다. 이렇게 하려면 이전 섹션에서 만든 컬렉션에서 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 {subscription-key} 및 {udId}을 값으로 바꾸고,이 URL을 입력 하 여 GET 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    응답 이미지는 다음과 같습니다.

    ![Azure Maps 데이터 스토리지에서 데이터 가져오기](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>색 및 불투명도를 사용 하 여 다각형 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.


[경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)와 스타일 한정자를 사용하여 다각형의 모양을 수정할 수 있습니다.

1. Postman 앱에서 이전에 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 색 및 불투명도를 사용 하 여 다각형을 렌더링 하도록 GET 요청을 구성 합니다.
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![불투명 다각형 렌더링](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>사용자 지정 레이블을 사용 하 여 원 및 압정 렌더링

> [!Note]
> 이 섹션의 절차에는 가격 책정 계층 s 1의 Azure Maps 계정이 필요 합니다.


스타일 한정자를 추가 하 여 핀의 모양을 수정할 수 있습니다. 예를 들어 압정 및 해당 레이블을 더 크게 만들려면 `sc` "눈금 스타일" 한정자를 사용 합니다. 이 한정자는 0 보다 큰 값을 사용 합니다. 값 1이 표준 배율입니다. 값이 1보다 크면 핀이 확대되고, 값이 1보다 작으면 축소됩니다. 스타일 한정자에 대 한 자세한 내용은 [정적 이미지 서비스 경로 매개 변수](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)를 참조 하세요.


사용자 지정 레이블을 사용 하 여 원과 압정를 렌더링 하려면 다음 단계를 따르세요.

1. Postman 앱에서 이전에 만든 컬렉션의 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 GET 요청을 수행 합니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    응답 이미지는 다음과 같습니다.

    ![사용자 지정 압정를 사용 하 여 원 렌더링](./media/how-to-render-custom-data/circle-custom-pins.png)

2. 마지막 단계에서 압정 색을 변경 하려면 "co" 스타일 한정자를 변경 합니다. `pins=default|la15+50|al0.66|lc003C62|co002D62|`를 살펴보면 현재 색이 CSS에서 #002D62로 지정 됩니다. #41d42a로 변경 하려는 경우를 가정해 보겠습니다. `pins=default|la15+50|al0.66|lc003C62|co41D42A|`와 같이 "co" 지정자 뒤에 새 색 값을 씁니다. 새 GET 요청을 만듭니다.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    Pin 색을 변경한 후의 응답 이미지는 다음과 같습니다.

    ![업데이트 된 압정가 있는 원 렌더링](./media/how-to-render-custom-data/circle-updated-pins.png)

마찬가지로 다른 스타일 한정자를 변경, 추가 및 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계


* [Azure Maps 지도 이미지 가져오기 API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 문서를 살펴봅니다.
* Azure Maps Data Service에 대 한 자세한 내용은 [서비스 설명서](https://docs.microsoft.com/rest/api/maps/data)를 참조 하세요.

