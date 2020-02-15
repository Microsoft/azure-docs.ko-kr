---
title: Event Grid를 사용 하 여 맵 이벤트에 대응 Microsoft Azure 맵
description: 이 문서에서는 Event Grid를 사용 하 여 Microsoft Azure 맵 이벤트에 반응 하는 방법에 대해 설명 합니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: f7e5be8d4aca85c9574db18ec9df8a7f30850d06
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210109"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Event Grid를 사용하여 Azure Maps 이벤트에 대응 

Azure Maps는 사용자가 다른 서비스로 이벤트 알림을 보내고 다운스트림 프로세스를 트리거할 수 있도록 Azure Event Grid와 통합 됩니다. 이 문서의 목적은 Azure Maps 이벤트를 수신 하도록 비즈니스 응용 프로그램을 구성 하는 데 도움이 됩니다. 이를 통해 사용자는 안정적이 고 확장 가능 하 고 안전한 방식으로 중요 한 이벤트에 대응할 수 있습니다. 예를 들어, 사용자가 응용 프로그램을 빌드하여 데이터베이스를 업데이트 하 고, 티켓을 만들고, 장치를 지 오로 가져갈 때마다 전자 메일 알림을 배달할 수 있습니다.

Azure Event Grid는 게시-구독 모델을 사용 하는 완전히 관리 되는 이벤트 라우팅 서비스입니다. Event Grid에는 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) 및 [Azure Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-overview)와 같은 Azure 서비스에 대 한 지원이 기본적으로 제공 됩니다. 웹 후크를 사용 하 여 비 Azure 서비스에 이벤트 경고를 제공할 수 있습니다. Azure Event Grid가 지원하는 이벤트 처리기의 전체 목록은 [Azure Event Grid 소개](https://docs.microsoft.com/azure/event-grid/overview)를 참조하세요.


![Azure Event Grid 기능 모델](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Azure Maps 이벤트 형식

Event Grid는 [이벤트 구독](https://docs.microsoft.com/azure/event-grid/concepts#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure Maps 계정은 다음과 같은 이벤트 유형을 내보냅니다. 

| 이벤트 유형 | Description |
| ---------- | ----------- |
| Microsoft.Maps.GeofenceEntered | 받은 좌표가 지정 된 지 오의 외부에서 내부로 이동 했을 때 발생 합니다. |
| Microsoft.Maps.GeofenceExited | 받은 좌표가 지정 된 지 오의 내에서 바깥쪽으로 이동 했을 때 발생 합니다. |
| Microsoft.Maps.GeofenceResult | 상태와 관계없이 지오펜싱 쿼리에서 결과가 반환될 때마다 발생합니다. |

## <a name="event-schema"></a>이벤트 스키마

다음 예제에서는 GeofenceResult에 대 한 스키마를 보여 줍니다.

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

* 여러 구독을 구성 하 여 이벤트를 동일한 이벤트 처리기로 라우팅합니다. 이벤트가 특정 원본에서 제공되었다고 가정하지 않는 것이 중요합니다. 항상 메시지 항목을 확인 하 여 메시지가 원하는 원본에서 온 것인지 확인 합니다.
* 응답 헤더의 `X-Correlation-id` 필드를 사용 하 여 개체에 대 한 정보가 최신 상태 인지 여부를 파악할 수 있습니다. 메시지는 지연 후 또는 잘못된 순서로 도착할 수 있습니다.
* `EnterAndExit`로 설정 된 mode 매개 변수를 사용 하 여 지 오 API의 GET 또는 POST 요청을 호출 하면 이전에 지 오 라 API 호출에서 상태가 변경 된 지 오 라의 각 기 하 도형에 대해 Enter 또는 Exit 이벤트가 생성 됩니다.

## <a name="next-steps"></a>다음 단계

지오펜싱을 사용하여 공사 현장의 작업을 제어하는 방법을 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"] 
> [Azure Maps를 사용하여 지오펜스 설정](tutorial-geofence.md)
