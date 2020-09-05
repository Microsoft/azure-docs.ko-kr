---
title: 게시, 클라우드 Azure Event Grid IoT Edge에서 이벤트 구독 Microsoft Docs
description: 게시, Event Grid에서 웹 후크를 사용 하 여 클라우드에서 이벤트 구독 IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 12bcb54f4bfdf17209324febeba380ff7789fc0f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998988"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>자습서: 게시, 클라우드에서 이벤트 구독

이 문서에서는 IoT Edge에서 Event Grid를 사용 하 여 이벤트를 게시 하 고 구독 하는 데 필요한 모든 단계를 안내 합니다. 이 자습서에서는 및 Azure Function을 이벤트 처리기로 사용 합니다. 추가 대상 형식은 [이벤트 처리기](event-handlers.md)를 참조 하세요.

진행 하기 전에 Event Grid 토픽 및 구독을 이해 하려면 [Event Grid 개념](concepts.md) 을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소 
이 자습서를 완료하려면 다음과 같은 요건이 필요합니다.

* **Azure 구독** -아직 없는 경우 [무료 계정](https://azure.microsoft.com/free) 을 만듭니다. 
* **Azure IoT Hub 및 IoT Edge 장치** - [Linux](../../iot-edge/quickstart-linux.md) 또는 [Windows 장치](../../iot-edge/quickstart.md) 에 대 한 빠른 시작 (아직 없는 경우)의 단계를 따릅니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Azure Portal에서 Azure 함수를 만듭니다.

[자습서](../../azure-functions/functions-create-first-azure-function.md) 에 설명 된 단계에 따라 Azure 함수를 만듭니다. 

코드 조각을 다음 코드로 바꿉니다.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

새 함수에서 오른쪽 상단의 **함수 URL 가져오기** 를 선택 하 고 기본값 (**함수 키**)을 선택한 다음 **복사**를 선택 합니다. 자습서의 뒷부분에서 함수 URL 값을 사용 합니다.

> [!NOTE]
> EventGrid 이벤트 트리거를 사용 하 여 이벤트에 반응 하는 방법에 대 한 추가 샘플과 자습서는 [Azure Functions](../../azure-functions/functions-overview.md) 설명서를 참조 하세요.

## <a name="create-a-topic"></a>토픽 만들기

이벤트의 게시자는 event grid 토픽을 만들어야 합니다. 항목은 게시자가 이벤트를 보낼 수 있는 끝점을 가리킵니다.

1. 다음 콘텐츠를 사용 하 여 topic2.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 다음 명령을 실행 하 여 토픽을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 다음 명령을 실행 하 여 항목이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   샘플 출력:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

구독자는 토픽에 게시 된 이벤트를 등록할 수 있습니다. 모든 이벤트를 수신 하려면 구독자가 관심 있는 토픽에서 Event grid 구독을 만들어야 합니다.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 다음 콘텐츠를 사용 하 여 subscription2.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **Endpointtype** 은 구독자가 Webhook 임을 지정 합니다.  **Endpointurl** 은 구독자가 이벤트를 수신 대기 하는 url을 지정 합니다. 이 URL은 이전에 설정한 Azure Function 샘플에 해당 합니다.
2. 다음 명령을 실행 하 여 구독을 만듭니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 다음 명령을 실행 하 여 구독이 성공적으로 만들어졌는지 확인 합니다. HTTP 상태 코드 200을 반환 해야 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    샘플 출력:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>이벤트 게시

1. 다음 콘텐츠를 사용 하 여 event2.js를 만듭니다. 페이로드에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요.

    ```json
        [
          {
            "id": "eventId-func-1",
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
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>이벤트 배달 확인

함수의 **모니터** 옵션 아래 Azure Portal에서 배달 된 이벤트를 볼 수 있습니다.

## <a name="cleanup-resources"></a>리소스 정리

* 다음 명령을 실행 하 여 토픽 및 모든 해당 구독을 삭제 합니다.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Azure Portal에서 만든 Azure 함수를 삭제 합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 event grid 토픽, 구독 및 게시 된 이벤트를 만들었습니다. 기본 단계를 배웠으므로 이제 다음 문서를 참조 하세요.

* IoT Edge에서 Azure Event Grid를 사용 하 여 발생 하는 문제를 해결 하려면 [문제 해결 가이드](troubleshoot.md)를 참조 하세요.
* [필터](advanced-filtering.md)를 사용 하 여 구독을 만들거나 업데이트 합니다.
* [Linux](persist-state-linux.md) 또는 [Windows](persist-state-windows.md) 에서 Event Grid 모듈의 지 속성 설정
* [설명서](configure-client-auth.md) 에 따라 클라이언트 인증 구성
* 이 [자습서](forward-events-event-grid-cloud.md) 를 수행 하 여 클라우드에서 Azure Event Grid로 이벤트 전달
* [Edge에서 토픽 및 구독 모니터링](monitor-topics-subscriptions.md)
