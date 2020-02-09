---
title: 로컬로 이벤트 게시, 구독 Azure Event Grid IoT Edge | Microsoft Docs
description: 게시, Event Grid에서 웹 후크를 사용 하 여 로컬로 이벤트 구독 구독 IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086644"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>자습서: 로컬로 이벤트 게시, 구독

이 문서에서는 IoT Edge에서 Event Grid를 사용 하 여 이벤트를 게시 하 고 구독 하는 데 필요한 모든 단계를 안내 합니다.

> [!NOTE]
> Azure Event Grid 토픽 및 구독에 대 한 자세한 내용은 [Event Grid 개념](concepts.md)을 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** -아직 없는 경우 [무료 계정](https://azure.microsoft.com/free) 을 만듭니다. 
* **장치 Azure IoT Hub 및 IoT Edge** - [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치](../../iot-edge/quickstart.md) 에 대 한 빠른 시작 (아직 없는 경우)의 단계를 따릅니다.

## <a name="deploy-event-grid-iot-edge-module"></a>Event Grid IoT Edge 모듈 배포

IoT Edge 장치에 모듈을 배포 하는 방법에는 여러 가지가 있으며 모든 장치는 IoT Edge에서 Azure Event Grid에 대해 작동 합니다. 이 문서에서는 Azure Portal에서 IoT Edge에 Event Grid를 배포 하는 단계를 설명 합니다.

>[!NOTE]
> 이 자습서에서는 지 속성 없이 Event Grid 모듈을 배포 합니다. 이는 모듈을 다시 배포할 때이 자습서에서 만든 토픽 및 구독이 삭제 됨을 의미 합니다. 지 속성을 설정 하는 방법에 대 한 자세한 내용은 [Linux에서 상태 유지](persist-state-linux.md) 또는 [Windows에서 지속 상태](persist-state-windows.md)문서를 참조 하세요. 프로덕션 워크 로드의 경우 지 속성을 사용 하 여 Event Grid 모듈을 설치 하는 것이 좋습니다.


### <a name="select-your-iot-edge-device"></a>IoT Edge 장치 선택

1. [Azure 포털](https://portal.azure.com)
1. IoT Hub로 이동합니다.
1. **자동 장치 관리** 섹션의 메뉴에서 **IoT Edge** 를 선택 합니다. 
1. 장치 목록에서 대상 장치의 ID를 클릭 합니다.
1. **모듈 설정**을 선택합니다. 페이지를 열어 둡니다. 다음 섹션의 단계를 계속 진행 합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 JSON 문서를 수동으로 빌드하는 대신 배포 매니페스트를 만드는 과정을 안내 하는 마법사가 있습니다.  **모듈 추가**, **경로 지정** 및 **배포 검토** 등 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 선택 합니다.
1. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
1. 컨테이너의 이름, 이미지, 컨테이너 만들기 옵션을 제공 합니다.

   * **이름**: eventgridmodule
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **컨테이너 만들기 옵션**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. 페이지 맨 아래에 있는 **저장**
 1. 다음 섹션을 계속 진행 하 여 배포 하기 전에 Azure Event Grid 구독자 모듈을 추가 합니다.

    >[!IMPORTANT]
    > 이 자습서에서는 클라이언트 인증을 사용 하지 않도록 설정 하 여 Event Grid 모듈을 배포 합니다. 프로덕션 워크 로드의 경우 클라이언트 인증을 사용 하도록 설정 하는 것이 좋습니다. Event Grid 모듈을 안전 하 게 구성 하는 방법에 대 한 자세한 내용은 [보안 및 인증](security-authentication.md)을 참조 하세요.
    > 
    > Azure VM을에 지 장치로 사용 하는 경우 포트 4438에서 인바운드 트래픽을 허용 하는 인바운드 포트 규칙을 추가 합니다. 규칙을 추가 하는 방법에 대 한 지침은 [VM에 포트를 여는 방법](../../virtual-machines/windows/nsg-quickstart-portal.md)을 참조 하세요.
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Event Grid 구독자 IoT Edge 모듈 배포

이 섹션에서는 이벤트를 전달할 수 있는 이벤트 처리기 역할을 하는 다른 IoT 모듈을 배포 하는 방법을 보여 줍니다.

### <a name="add-modules"></a>모듈 추가

1. **배포 모듈** 섹션에서 **추가** 를 다시 선택 합니다. 
1. 드롭다운 목록의 모듈 형식에서 **IoT Edge 모듈** 을 선택 합니다.
1. 컨테이너의 이름, 이미지 및 컨테이너 만들기 옵션을 제공 합니다.

   * **이름**: 구독자
   * **이미지 URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **컨테이너 만들기 옵션**: 없음
1. 페이지 맨 아래에 있는 **저장**
1. **다음** 을 클릭 하 여 경로 섹션으로 이동 합니다.

 ### <a name="setup-routes"></a>설정 경로

기본 경로를 유지 하 고 **다음** 을 선택 하 여 검토 섹션으로 이동 합니다.

### <a name="submit-the-deployment-request"></a>배포 요청 제출

1. 검토 섹션에는 이전 섹션에서 선택한 항목에 따라 만든 JSON 배포 매니페스트가 표시 됩니다. JSON에 나열 된 **eventgridmodule** 및 **subscriber** 모듈이 모두 표시 되는지 확인 합니다. 
1. 배포 정보를 검토한 다음 **제출**을 선택합니다. 배포를 제출한 후에는 **장치** 페이지로 돌아갑니다.
1. **모듈 섹션**에서 **event grid** 와 **구독자** 모듈이 모두 나열 되는지 확인 합니다. 그리고 **배포에 지정** 된 및 **장치 열에서 보고** 된가 **예**로 설정 되어 있는지 확인 합니다.

    모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="create-a-topic"></a>토픽 만들기

이벤트의 게시자는 event grid 토픽을 만들어야 합니다. Azure Event Grid에서 항목은 게시자가 이벤트를 보낼 수 있는 엔드포인트을 참조 합니다.

1. 다음 콘텐츠를 사용 하 여 토픽을 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md)를 참조 하세요.

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. 다음 명령을 실행 하 여 event grid 토픽을 만듭니다. HTTP 상태 코드가 `200 OK`인지 확인 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. 다음 명령을 실행 하 여 항목이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시 된 이벤트를 등록할 수 있습니다. 이벤트를 수신 하려면 관심 있는 토픽에 대 한 Event Grid 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠를 사용 하 여 구독을 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > **Endpointtype** 속성은 구독자가 **Webhook**임을 지정 합니다.  **Endpointurl** 은 구독자가 이벤트를 수신 대기 하는 url을 지정 합니다. 이 URL은 이전에 배포한 Azure 구독자 샘플에 해당 합니다.
2. 항목에 대 한 구독을 만들려면 다음 명령을 실행 합니다. HTTP 상태 코드가 `200 OK`인지 확인 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행 하 여 구독이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠를 사용 하 여 이벤트 json을 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md)를 참조 하세요.

    ```json
        [
          {
            "id": "eventId-func-0",
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
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 배달 확인

1. IoT Edge VM에 대 한 SSH 또는 RDP.
1. 구독자 로그를 확인 합니다.

    Windows에서 다음 명령을 실행 합니다.

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Linux에서 다음 명령을 실행 합니다.

    ```sh
    sudo docker logs subscriber
    ```

    샘플 출력:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행 하 여 토픽 및 모든 해당 구독을 삭제 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* IoT Edge 장치에서 구독자 모듈을 삭제 합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 event grid 토픽, 구독 및 게시 된 이벤트를 만들었습니다. 기본 단계를 배웠으므로 이제 다음 문서를 참조 하세요. 

- IoT Edge에서 Azure Event Grid를 사용 하 여 발생 하는 문제를 해결 하려면 [문제 해결 가이드](troubleshoot.md)를 참조 하세요.
- [필터](advanced-filtering.md)를 사용 하 여 구독을 만들거나 업데이트 합니다.
- [Linux](persist-state-linux.md) 또는 [Windows](persist-state-windows.md) 에서 Event Grid 모듈의 지 속성 사용
- [설명서](configure-client-auth.md) 에 따라 클라이언트 인증 구성
- 이 [자습서](pub-sub-events-webhook-cloud.md) 를 수행 하 여 클라우드에서 Azure Functions로 이벤트 전달
- [IoT Edge에서 Blob Storage 이벤트에 대응](react-blob-storage-events-locally.md)
- [Edge에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)

