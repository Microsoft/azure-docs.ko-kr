---
title: '자습서: 지오펜스 생성 및 맵에서 디바이스 추적 | Microsoft Azure Maps'
description: Microsoft Azure Maps 공간 서비스를 사용하여 지오펜스를 설정하고, 지오펜스와 관련된 디바이스를 추적하는 방법을 알아봅니다.
author: walsehgal
ms.author: v-musehg
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 932dfb9624177c299997c4f9f184dc5c973d0fa0
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76899222"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>자습서: Azure Maps를 사용하여 지오펜스 설정

이 자습서에서는 Azure Maps를 사용하여 지오펜스를 설정하는 기본 단계를 안내합니다. 이 시나리오에서는 건설 Site Manager에서 잠재적인 위험 장비를 모니터링해야 합니다. 관리자는 선택한 전체 건설 지역에서 장비가 유지되도록 해야 합니다. 이 전체 건설 지역은 하드 매개 변수입니다. 규정에 따라 장비가 이 매개 변수 내에 있어야 하며 위반 사항은 Operations Manager에 보고됩니다.  

데이터 업로드 API를 사용하여 지오펜스를 저장하고, 지오펜스 API를 사용하여 지오펜스와 관련된 장비 위치를 확인합니다. 데이터 업로드 API와 지오펜스 API는 모두 Azure Maps에서 제공합니다. 또한 Azure Event Grid를 사용하여 지오펜스 결과를 스트림하고, 이 결과를 기반으로 하는 알림을 설정합니다. Event Grid에 대한 자세한 내용은 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview)를 참조하세요.

이 자습서에서는 다음 작업을 수행하는 방법에 대해 설명합니다.

> [!div class="checklist"]
> * 데이터 업로드 API를 사용하여 Azure Maps, 데이터 서비스에서 지오펜스 영역을 업로드합니다.
> *   지오펜스 이벤트를 처리하도록 Event Grid를 설정합니다.
> *   지오펜스 이벤트 처리기를 설정합니다.
> *   Logic Apps를 사용하여 경고를 지오펜스 이벤트에 대한 응답으로 설정합니다.
> *   Azure Maps 지오펜스 서비스 API를 사용하여 건설 자산이 건설 현장에 있는지 여부를 추적합니다.


## <a name="prerequisites"></a>사전 요구 사항

### <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기 

[계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps)의 지침에 따라 S1 가격 책정 계층을 사용하여 Azure Maps 계정 구독을 만듭니다. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account)의 단계에서는 계정의 기본 키를 검색하는 방법을 보여 줍니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

## <a name="upload-geofences"></a>지오펜스 업로드

주 지오펜스가 subsite1이며 여기에는 만료 기간이 설정되어 있다고 가정합니다. 요구 사항에 따라 중첩된 지오펜스를 더 많이 만들 수 있습니다. 이러한 펜스 세트를 사용하여 전체 건설 지역 내의 여러 건설 지역을 추적하는 데 사용할 수 있습니다. 예를 들어 subsite1에서는 일정 중 1~4주 동안 작업을 수행하고, subsite2에서는 5~7주 동안 작업을 수행할 수 있습니다. 이러한 모든 펜스는 프로젝트를 시작할 때 단일 데이터 세트로 로드할 수 있으며, 시간과 공간을 기준으로 규칙을 추적하는 데 사용됩니다. 

데이터 업로드 API를 사용하여 건설 현장에 대한 지오펜스를 업로드하기 위해 Postman 애플리케이션을 사용합니다. [Postman 애플리케이션](https://www.getpostman.com/)을 설치하고, 체험 계정을 만듭니다. 

Postman 앱이 설치되면 다음 단계에 따라 Azure Maps, 데이터 업로드 API를 사용하여 건설 현장 지오펜스를 업로드합니다.

1. Postman 앱을 열고, 새로 만들기 | 새로 만들기를 차례로 클릭하고, [요청]을 선택합니다. 지오펜스 데이터 업로드에 대한 요청 이름을 입력하고, 저장할 컬렉션 또는 폴더를 선택한 다음, [저장]을 클릭합니다.

    ![Postman을 사용하여 지오펜스 업로드](./media/tutorial-geofence/postman-new.png)

2. 작성기 탭에서 POST HTTP 메서드를 선택하고, 다음 URL을 입력하여 POST 요청을 합니다.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL 경로의 GEOJSON 매개 변수는 업로드되는 데이터의 데이터 형식을 나타냅니다.

3. **매개 변수**를 클릭하고 POST 요청 URL에 사용할 다음 키/값 쌍을 입력합니다. {subscription-key}를 기본 키라고도 하는 Azure Maps 구독 키로 바꿉니다.
   
    ![Postman의 업로드 데이터(지오펜스)에 대한 매개 변수](./media/tutorial-geofence/postman-key-vals.png)

4. **Body(본문)** 를 클릭하고, 원시 입력 형식을 선택한 다음, 드롭다운 목록에서 입력 형식으로 JSON을 선택합니다. 다음 JSON을 업로드할 데이터로 제공합니다.

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. 보내기를 클릭하고 응답 헤더를 검토합니다. 요청이 성공하면 **Location(위치)** 헤더에 상태 URI가 포함됩니다. 상태 URI의 형식은 다음과 같습니다. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. 상태 URI를 복사하고 구독 키를 추가합니다. 상태 URI 형식은 아래와 같아야 합니다. 아래 형식에서 {subscription-key}({ } 포함)를 구독 키로 변경합니다.

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId`를 가져오려면 Postman 앱에서 새 탭을 열고, 작성기 탭에서 GET HTTP 메서드를 선택합니다. 이전 단계의 상태 URI에서 GET 요청을 수행합니다. 데이터 업로드가 성공하면 응답 본문에서 udId를 받게 됩니다. 나중에 사용할 수 있도록 udId를 복사합니다.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```

## <a name="set-up-an-event-handler"></a>이벤트 처리기 설정

이 섹션에서는 알림을 받는 이벤트 처리기를 만듭니다. 이 이벤트 처리기는 모든 장비의 투입 및 철수 이벤트에 대해 Operations Manager에 알려야 합니다.

이벤트를 처리, 입력 및 종료하기 위해 두 개의 [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) 서비스를 만듭니다. Logic Apps의 이벤트가 트리거되면 더 많은 이벤트가 순서대로 트리거됩니다. 경고(이 경우 이메일)를 Operations Manager에 보내는 것이 좋습니다. 다음 그림에서는 지오펜스 입장 이벤트에 대한 Logic App 만들기를 보여 줍니다. 마찬가지로 퇴장 이벤트에 대한 다른 이벤트를 만들 수 있습니다. 자세한 내용은 [지원되는 모든 이벤트 처리기](https://docs.microsoft.com/azure/event-grid/event-handlers)에서 확인할 수 있습니다.

1. Azure Portal에서 Logic App 만들기

   ![지오펜스 이벤트를 처리하는 Azure Logic Apps 만들기](./media/tutorial-geofence/logic-app.png)

2. Logic App에 대한 설정 메뉴에서 **Logic Apps 디자이너**로 이동합니다.

3. HTTP 요청 트리거를 선택한 다음, "새 단계"를 선택합니다. Outlook 커넥터에서 작업으로 "이메일 보내기"를 선택합니다.
  
   ![Logic Apps 스키마](./media/tutorial-geofence/logic-app-schema.png)

4. 이메일을 보내기 위한 필드를 채웁니다. HTTP URL을 그대로 둡니다. 그런 다음, "저장"을 클릭하면 자동으로 생성됩니다.

   ![Logic Apps 엔드포인트 생성](./media/tutorial-geofence/logic-app-endpoint.png)

5. 논리 앱을 저장하여 HTTP URL 엔드포인트를 생성하고 HTTP URL을 복사합니다.

## <a name="create-an-azure-maps-events-subscription"></a>Azure Maps 이벤트 구독 만들기

Azure Maps는 세 가지 이벤트 유형을 지원합니다. Azure Maps에서 지원하는 이벤트 유형은 [여기](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)서 확인할 수 있습니다. 투입 이벤트와 철수 이벤트에 대해 각각 하나씩 두 개의 서로 다른 이벤트 구독이 필요합니다.

다음 단계에 따라 지오펜스 입장 이벤트에 대한 이벤트 구독을 만듭니다. 비슷한 방식으로 지오펜스 퇴장 이벤트도 구독할 수 있습니다.

1. Azure Maps 계정으로 이동합니다. 대시보드에서 [구독]을 선택합니다. 구독 이름을 클릭하고, 설정 메뉴에서 **이벤트**를 선택합니다.

   ![Azure Maps 계정 이벤트로 이동](./media/tutorial-geofence/events-tab.png)

2. 이벤트 구독을 만들려면 이벤트 페이지에서 [이벤트 구독]을 선택합니다.

   ![Azure Maps 이벤트 구독 만들기](./media/tutorial-geofence/create-event-subscription.png)

3. 이벤트 구독의 이름을 지정하고, [입장] 이벤트 유형을 구독합니다. 이제 "엔드포인트 유형"으로 [웹후크]를 선택합니다. "엔드포인트 선택"을 클릭하고, Logic App HTTP URL 엔드포인트를 "{Endpoint}"에 복사합니다.

   ![Azure Maps 이벤트 구독 세부 정보](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>지오펜스 API 사용

지오펜스 API를 사용하여 **디바이스**(이 경우 장비)가 지오펜스 내부 또는 외부에 있는지 확인할 수 있습니다. 시간이 지남에 따라 특정 장비가 이동한 여러 위치에 대해 지오펜스 GET API를 쿼리해 보겠습니다. 다음 그림에서는 5개의 건설 장비가 있는 5개의 위치를 보여 줍니다. 

> [!Note]
> 시나리오와 동작은 동일한 **디바이스 ID**를 기반으로 하므로 아래 그림과 같이 5개의 다른 위치를 반영하고 있습니다.

"deviceId"는 해당 위치를 쿼리할 때 GET 요청에서 디바이스에 제공하는 고유 ID입니다. **지오펜스 검색 - GET API**에 대한 비동기 요청을 수행하면 "deviceId"가 지정된 지오펜스를 기준으로 해당 디바이스에 대한 지오펜스 이벤트를 게시하는 데 도움이 됩니다. 이 자습서에서는 고유한 "deviceId"를 사용하여 API에 대한 비동기 요청을 수행했습니다. 자습서의 요청은 다이어그램과 같이 시간순으로 수행됩니다. 응답의 "isEventPublished" 속성은 디바이스가 지오펜스에 들어가거나 나올 때마다 게시됩니다. 이 자습서를 수행하기 위해 디바이스를 등록할 필요가 없습니다.

다이어그램에서 다시 살펴보겠습니다. 이러한 5개 위치는 각각 펜스에 대한 지오펜스 입장 및 퇴장 상태 변경을 평가하는 데 사용됩니다. 상태가 변경되면 지오펜스 서비스에서 이벤트를 트리거하고, Event Grid에서 이 이벤트를 Logic App으로 보냅니다. 결과적으로 작업 관리자는 이메일을 통해 해당 투입 또는 철수 알림을 받습니다.

![Azure Maps의 지오펜스 맵](./media/tutorial-geofence/geofence.png)

Postman 앱에서, 위에서 만든 컬렉션에 새 탭을 엽니다. 작성기 탭에서 GET HTTP 메서드를 선택합니다.

다음은 장비의 위치 좌표가 서로 다른 5개의 HTTP GET 지오펜싱 API 요청입니다. 좌표는 시간순으로 관찰됩니다. 각 요청 뒤에는 응답 본문이 나옵니다.
 
1. 위치 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![지오펜스 쿼리 1](./media/tutorial-geofence/geofence-query1.png)

   위의 응답에서 주 지오펜스로부터의 음의 거리는 해당 장비가 지오펜스 내부에 있음을 의미합니다. 하위 사이트 지오펜스로부터의 양의 거리는 해당 장비가 하위 사이트 지오펜스 외부에 있음을 의미합니다. 

2. 위치 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![지오펜스 쿼리 2](./media/tutorial-geofence/geofence-query2.png)

   위의 JSON 응답을 주의 깊게 살펴보면 장비가 하위 사이트 외부에 있지만 주 펜스 내부에 있습니다. 이벤트가 트리거되지 않으며, 이메일이 전송되지 않습니다.

3. 위치 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![지오펜스 쿼리 3](./media/tutorial-geofence/geofence-query3.png)

   상태가 변경되었으며, 이제 장비가 주 및 하위 사이트 지오펜스 모두의 내부에 있습니다. 이 변경으로 인해 이벤트가 게시되고 알림 이메일이 Operations Manager로 전송됩니다.

4. 위치 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![지오펜스 쿼리 4](./media/tutorial-geofence/geofence-query4.png)

   해당 응답을 주의 깊게 관찰하면, 장비가 하위 사이트 지오펜스에서 나간 경우에도 여기서는 이벤트를 게시하지 않는다는 것을 알 수 있습니다. GET 요청에서 사용자가 지정한 시간을 살펴보면 하위 사이트 지오펜스가 이 시간 동안 만료되었음을 알 수 있습니다. 장비는 여전히 주 지오펜스에 있습니다. 응답 본문의 `expiredGeofenceGeometryId` 아래에서 하위 사이트 지오펜스의 기하 도형 ID도 확인할 수 있습니다.


5. 위치 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![지오펜스 쿼리 5](./media/tutorial-geofence/geofence-query5.png)

   장비가 주 건설 현장 지오펜스에서 떠났음을 알 수 있습니다. 이벤트가 게시되고 경고 이메일이 Operations Manager로 전송됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 데이터 업로드 API를 사용하여 Azure Maps 및 데이터 서비스에 업로드하여 지오펜스를 설정하는 방법을 알아보았습니다. 또한 Azure Maps Events Grid를 사용하여 지오펜스 이벤트를 구독하고 처리하는 방법도 알아보았습니다. 

* 더 복잡한 논리를 작성하기 위해 Logic Apps를 사용하여 JSON을 구문 분석하는 방법을 알아보려면 [Azure Logic Apps에서 콘텐츠 형식 처리](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)를 참조하세요.
* Event Grid의 이벤트 처리기에 대한 자세한 내용은 [Event Grid에서 지원되는 이벤트 처리기](https://docs.microsoft.com/azure/event-grid/event-handlers)를 참조하세요.
