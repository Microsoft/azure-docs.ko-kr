---
title: Event Grid를 사용하여 Azure Maps 이벤트에 대응 | Microsoft Docs
description: Event Grid를 사용하여 Azure Maps 이벤트에 대응하는 방법을 알아봅니다.
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: a70011b934398ac4e7f74bb67013e93bb5e86e4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799165"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid를 사용하여 Azure Maps 이벤트에 대응 

Azure Maps는 이벤트 알림을 다른 서비스에 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합됩니다. 이 문서는 안전하고 안정적이며 확장성 있는 방식으로 중요한 이벤트에 대응할 수 있도록 비즈니스 애플리케이션의 Azure Maps 이벤트 수신 대기를 구성하는 작업을 지원하기 위한 것입니다. 예를 들어 데이터베이스 업데이트, 티켓 만들기, 디바이스가 지오펜스 안으로 들어올 때마다 메일 알림 제공 등의 여러 작업을 수행하는 애플리케이션을 빌드합니다.

Azure Event Grid는 게시-구독 모델을 사용하는 완전 관리형 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 및 [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)와 같은 Azure 서비스에 대한 기본 제공 지원이 있어 웹후크를 사용하여 외부 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](https://docs.microsoft.com/azure/event-grid/overview)를 참조하세요.


![Azure Event Grid 기능 모델](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 이벤트 형식

Event Grid는 [이벤트 구독](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure Maps 계정은 다음과 같은 이벤트 유형을 내보냅니다. 

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 수신된 좌표가 지정된 지오펜스 외부에서 내부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceExited | 수신된 좌표가 지정된 지오펜스 내부에서 외부로 이동한 경우에 발생합니다. |
| Microsoft.Maps.GeofenceResult | 상태와 관계없이 지오펜싱 쿼리에서 결과가 반환될 때마다 발생합니다. |

## <a name="event-schema"></a>이벤트 스키마

다음 예제에서는 GeofenceResult의 스키마를 보여 줍니다.

```JSON
{   
   "id":"451675de-a67d-4929-876c-5c2bf0b2c000", 
   "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}", 
   "subject":"/spatial/geofence/udid/{udid}/id/{eventId}", 
   "data":{   
      "geometries":[   
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"1", 
            "distance":999.0, 
            "nearestLat":47.609833, 
            "nearestLon":-122.148274 
         }, 
         {   
            "deviceId":"device_1", 
            "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169", 
            "geometryId":"2", 
            "distance":999.0, 
            "nearestLat":47.621954, 
            "nearestLon":-122.131841 
         } 
      ], 
      "expiredGeofenceGeometryId":[   
      ], 
      "invalidPeriodGeofenceGeometryId":[   
      ] 
   }, 
   "eventType":"Microsoft.Maps.GeofenceResult", 
   "eventTime":"2018-11-08T00:52:08.0954283Z", 
   "metadataVersion":"1", 
   "dataVersion":"1.0" 
}
```

## <a name="tips-for-consuming-events"></a>이벤트 사용하기 위한 팁

Azure Maps 지오펜스 이벤트를 처리하는 애플리케이션은 다음과 같은 몇 가지 권장 지침을 따라야 합니다.

* 이벤트를 동일한 이벤트 처리기로 라우팅하도록 여러 구독을 구성할 수 있습니다. 이벤트가 특정 원본에서 제공되었다고 가정하지 않는 것이 중요합니다. 항상 메시지 항목을 확인하여 예상한 원본에서 제공된 것인지 확인합니다.
* 메시지는 지연 후 또는 잘못된 순서로 도착할 수 있습니다. 응답 헤더의 `X-Correlation-id` 필드를 사용하여 개체 정보가 최신 상태인지 파악합니다.
* mode 매개 변수를 `EnterAndExit`로 설정하여 Get 및 POST 지오펜스 API를 호출하는 경우 상태가 이전 지오펜스 API 호출과 다르게 변경된 지오펜스의 각 기하 도형에 대해 Enter 또는 Exit 이벤트가 생성됩니다.

## <a name="next-steps"></a>다음 단계

지오펜싱을 사용하여 공사 현장의 작업을 제어하는 방법을 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"] 
> [Azure Maps를 사용하여 지오펜스 설정](tutorial-geofence.md)