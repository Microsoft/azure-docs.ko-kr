---
title: IoTHub-Azure Event Grid IoT Edge에 Event Grid 이벤트 전달 Microsoft Docs
description: IoTHub에 Event Grid 이벤트 전달
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844703"
---
# <a name="tutorial-forward-events-to-iothub"></a>자습서: IoTHub로 이벤트 전달

이 문서에서는 경로를 사용 하 여 IoTHub IoT Edge 모듈로 Event Grid 이벤트를 전달 하는 데 필요한 모든 단계를 안내 합니다. 다음과 같은 이유로이 작업을 수행 하는 것이 좋습니다.

* EdgeHub의 라우팅으로 이미 있는 기존 투자를 계속 사용 합니다.
* IoT Hub를 통해서만 장치에서 모든 이벤트를 라우팅하는 것이 좋습니다.

이 자습서를 완료 하려면 다음 개념을 이해 해야 합니다.

- [Event Grid 개념](concepts.md)
- [IoT Edge 허브](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>필수 조건 
이 자습서를 완료 하려면 다음이 필요 합니다.

* **Azure 구독** -아직 없는 경우 [무료 계정](https://azure.microsoft.com/free) 을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치](../../iot-edge/quickstart.md) 에 대 한 빠른 시작 (아직 없는 경우)의 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>토픽 만들기

이벤트의 게시자는 event grid 토픽을 만들어야 합니다. 항목은 게시자가 이벤트를 보낼 수 있는 엔드포인트을 참조 합니다.

1. 다음 콘텐츠를 사용 하 여 topic4를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 다음 명령을 실행 하 여 토픽을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행 하 여 항목이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시 된 이벤트를 등록할 수 있습니다. 이벤트를 수신 하려면 관심 있는 토픽에서 Event grid 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 아래 콘텐츠를 사용 하 여 subscription4를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > `endpointType` 구독자를 `edgeHub`하도록 지정 합니다. `outputName` Event Grid 모듈이이 구독과 일치 하는 이벤트를 edgeHub로 라우팅하는 출력을 지정 합니다. 예를 들어 위의 구독과 일치 하는 이벤트는 `/messages/modules/eventgridmodule/outputs/sampleSub4`에 기록 됩니다.
2. 다음 명령을 실행 하 여 구독을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행 하 여 구독이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Edge 허브 경로 설정

다음과 같이 IoTHub에 전달할 이벤트 구독의 이벤트를 전달 하도록 edge 허브의 경로를 업데이트 합니다.

1. [Azure 포털](https://ms.portal.azure.com)
1. **IoT Hub**로 이동 합니다.
1. 메뉴에서 **IoT Edge** 선택
1. 장치 목록에서 대상 장치의 ID를 선택 합니다.
1. **모듈 설정**을 선택합니다.
1. 경로 섹션에서 **다음** 을 선택 합니다.
1. 경로에 새 경로를 추가 합니다.

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  예를 들면 다음과 같습니다.

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 위의 경로는이 구독과 일치 하는 모든 이벤트를 전달 하 여 IoT hub에 전달 합니다. [Edge 허브 라우팅](../../iot-edge/module-composition.md) 기능을 사용 하 여 추가로 필터링 하 고 Event Grid 이벤트를 다른 IoT Edge 모듈로 라우팅할 수 있습니다.

## <a name="setup-iot-hub-route"></a>설정 IoT Hub 경로

Event Grid 모듈에서 전달 된 이벤트를 볼 수 있도록 IoT Hub에서 경로를 설정 하는 [IoT Hub 라우팅 자습서](../../iot-hub/tutorial-routing.md) 를 참조 하세요. 쿼리에 `true`를 사용 하 여 자습서를 단순하게 유지 합니다.  



## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠를 사용 하 여 event4를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        [
          {
            "id": "eventId-iothub-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```

1. 다음 명령을 실행 하 여 이벤트를 게시 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 배달 확인

이벤트를 확인 하는 단계는 IoT Hub [라우팅 자습서](../../iot-hub/tutorial-routing.md) 를 참조 하세요.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행 하 여 edge에서 토픽 및 모든 해당 구독을 삭제 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 클라우드에서 IoTHub routing을 설정 하는 동안 만들어진 모든 리소스를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 event grid 토픽, edge hub 구독 및 게시 된 이벤트를 만들었습니다. Edge 허브로 전달 하는 기본 단계를 배웠으므로 이제 다음 문서를 참조 하세요.

* IoT Edge에서 Azure Event Grid를 사용 하 여 발생 하는 문제를 해결 하려면 [문제 해결 가이드](troubleshoot.md)를 참조 하세요.
* [Edge 허브](../../iot-edge/module-composition.md) 경로 필터를 사용 하 여 이벤트 분할
* [Linux](persist-state-linux.md) 또는 [Windows](persist-state-windows.md) 에서 Event Grid 모듈의 지 속성 설정
* [설명서](configure-client-auth.md) 에 따라 클라이언트 인증 구성
* 이 [자습서](forward-events-event-grid-cloud.md) 를 수행 하 여 클라우드에서 Azure Event Grid로 이벤트 전달
* [Edge에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)