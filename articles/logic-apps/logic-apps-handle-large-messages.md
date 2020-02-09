---
title: 청크를 사용 하 여 많은 메시지 처리
description: 를 사용 하 여 만든 자동화 된 작업 및 워크플로에서 청크를 사용 하 여 많은 메시지 크기를 처리 하는 방법에 대해 알아봅니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: shae-hurst
ms.author: shhurst
ms.topic: article
ms.date: 12/03/2019
ms.openlocfilehash: 81e7c12b04c1ebd9691c11d76f387f7d42490180
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456562"
---
# <a name="handle-large-messages-with-chunking-in-azure-logic-apps"></a>Azure Logic Apps에서 청크 분할을 사용하여 큰 메시지 처리

메시지를 처리할 때 Logic Apps는 메시지 콘텐츠를 최대 크기로 제한합니다. 이 제한은 큰 메시지를 저장하고 처리하여 생성되는 오버헤드를 줄이는 데 도움이 됩니다. 이 제한보다 큰 메시지를 처리하기 위해 Logic Apps는 큰 메시지를 더 작은 메시지로 *청크 분할*합니다. 그러면 특정 조건에 따라 Logic Apps를 사용하여 큰 파일을 전송할 수도 있습니다. 커넥터 또는 HTTP를 통해 다른 서비스와 통신할 때 Logic Apps는 큰 메시지를 사용할 수 있지만 *청크*로만 사용합니다. 이 조건은 커넥터에서 청크 분할을 지원해야 하거나, Logic Apps와 이러한 서비스 간의 기본 HTTP 메시지 교환에서 청크 분할을 사용해야 한다는 것을 의미합니다.

이 문서에서는 한도보다 큰 메시지를 처리하는 작업에 대해 청크를 설정하는 방법을 보여 줍니다. 여러 메시지를 교환하는 오버헤드로 인해 논리 앱 트리거는 청크를 지원하지 않습니다. 

## <a name="what-makes-messages-large"></a>메시지를 "큰 메시지"로 만드는 요인은 무엇인가요?

이 경우 해당 메시지를 처리하는 서비스를 기반으로 하는 "큰 메시지"입니다. 큰 메시지의 정확한 크기 제한은 Logic Apps 및 커넥터에 따라 다릅니다. Logic Apps와 커넥터는 모두 큰 메시지를 직접 사용할 수 없으며, 큰 메시지는 청크 분할해야 합니다. Logic Apps 메시지 크기 제한은 [Logic Apps 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요.
각 커넥터의 메시지 크기 제한은 [커넥터의 특정 기술 세부 정보](../connectors/apis-list.md)를 참조하세요.

### <a name="chunked-message-handling-for-logic-apps"></a>Logic Apps에 대한 청크 분할 메시지 처리

Logic Apps는 메시지 크기 제한보다 큰 청크 분할 메시지의 출력을 직접 사용할 수 없습니다. 청크 분할을 지원하는 작업만 이러한 출력의 메시지 콘텐츠에 액세스할 수 있습니다. 따라서 큰 메시지를 처리하는 작업은 다음 기준 중 *하나*를 충족해야 합니다.

* 해당 작업이 커넥터에 속한 경우 기본적으로 청크 분할을 지원합니다. 
* 해당 작업의 런타임 구성에 사용하도록 설정된 청크 분할 지원이 있습니다. 

그렇지 않으면 큰 콘텐츠 출력에 액세스하려고 할 때 런타임 오류가 발생합니다. 청크 분할을 사용하도록 설정하려면 [청크 분할 지원 설정](#set-up-chunking)을 참조하세요.

### <a name="chunked-message-handling-for-connectors"></a>커넥터에 대한 청크 분할 메시지 처리

Logic Apps와 통신하는 서비스에는 자체의 메시지 크기 제한이 있을 수 있습니다. 이러한 제한은 종종 Logic Apps 앱 제한보다 작습니다. 예를 들어 커넥터에서 청크 분할을 지원한다고 가정하면 커넥터는 30MB 메시지를 큰 메시지로 간주할 수 있지만 Logic Apps는 그렇지 않습니다. 이 커넥터의 제한을 준수하기 위해 Logic Apps는 30MB를 초과하는 모든 메시지를 더 작은 청크로 분할합니다.

청크 분할을 지원하는 커넥터의 경우 기본 청크 분할 프로토콜은 최종 사용자에게 표시되지 않습니다. 그러나 모든 커넥터에서 청크 분할을 지원하지 것은 아니기 때문에 들어오는 메시지가 커넥터의 크기 제한을 초과하면 이러한 커넥터에서 런타임 오류가 발생합니다.

> [!NOTE]
> 청크를 사용 하는 동작의 경우 트리거 본문을 전달 하거나 이러한 작업에서 `@triggerBody()?['Content']`와 같은 식을 사용할 수 없습니다. 대신, 텍스트 또는 JSON 파일 콘텐츠의 경우 [ **작성** 작업](../logic-apps/logic-apps-perform-data-operations.md#compose-action) 을 사용 하거나 해당 콘텐츠를 처리 하는 [변수를 만들](../logic-apps/logic-apps-create-variables-store-values.md) 수 있습니다. 트리거 본문에 미디어 파일 등의 다른 콘텐츠 형식이 포함 되어 있으면 해당 콘텐츠를 처리 하는 다른 단계를 수행 해야 합니다.

<a name="set-up-chunking"></a>

## <a name="set-up-chunking-over-http"></a>HTTP를 통한 청크 분할 설정

일반적인 HTTP 시나리오에서는 논리 앱과 엔드포인트에서 큰 메시지를 교환할 수 있도록 HTTP를 통해 큰 콘텐츠의 다운로드 및 업로드를 분할할 수 있습니다. 그러나 Logic Apps에 필요한 방식으로 메시지를 청크 분할해야 합니다. 

엔드포인트에서 다운로드 또는 업로드에 대한 청크 분할을 사용하도록 설정한 경우 논리 앱의 HTTP 작업에서는 큰 메시지를 자동으로 청크 분할합니다. 그렇지 않으면 엔드포인트에서 청크 분할을 지원하도록 설정해야 합니다. 엔드포인트 또는 커넥터를 소유하거나 제어하지 않으면 청크 분할을 설정할 수 있는 옵션이 없을 수 있습니다.

또한 HTTP 작업에서 아직 청크 분할을 사용하도록 설정하지 않은 경우 작업의 `runTimeConfiguration` 속성에서 청크 분할을 설정해야 합니다. 이 속성은 작업 내에 설정할 수 있습니다. 나중에 설명하는 대로 코드 보기 편집기에서 직접 수행하거나, 여기서 설명하는 대로 논리 앱 디자이너에서 수행할 수 있습니다.

1. HTTP 작업의 오른쪽 위 모서리에서 줄임표 단추 ( **...** )를 선택한 다음, **설정**을 선택합니다.

   ![작업에서 설정 메뉴를 엽니다.](./media/logic-apps-handle-large-messages/http-settings.png)

2. **콘텐츠 전송**에서 **청크 허용**을 **켜기**로 설정합니다.

   ![청크 분할 켜기](./media/logic-apps-handle-large-messages/set-up-chunking.png)

3. 다운로드 또는 업로드에 대한 청크 분할 설정을 계속하려면 다음 섹션으로 계속 진행하세요.

<a name="download-chunks"></a>

## <a name="download-content-in-chunks"></a>청크로 콘텐츠 다운로드

많은 엔드포인트에서 HTTP GET 요청을 통해 다운로드할 때 큰 메시지를 청크로 자동으로 보냅니다. HTTP를 통해 엔드포인트에서 청크 분할 메시지를 다운로드하려면 엔드포인트에서 부분 콘텐츠 요청 또는 *청크 분할 다운로드*를 지원해야 합니다. 논리 앱에서 콘텐츠를 다운로드하기 위해 엔드포인트에 HTTP GET 요청을 보내고 엔드포인트에서 "206" 상태 코드로 응답하면, 응답에 청크 분할된 콘텐츠가 포함됩니다. Logic Apps는 엔드포인트에서 부분 요청을 지원하는지 여부를 제어할 수 없습니다. 그러나 논리 앱에서 첫 번째 "206" 응답을 받으면 논리 앱이 모든 콘텐츠를 다운로드하기 위해 여러 요청을 자동으로 보냅니다.

엔드포인트에서 부분 콘텐츠를 지원할 수 있는지 여부를 확인하려면 HEAD 요청을 보냅니다. 이 요청은 응답에 `Accept-Ranges` 헤더가 포함되어 있는지 여부를 결정하는 데 도움이 됩니다. 이러한 방식으로 엔드포인트에서 청크 분할 콘텐츠를 지원하지만 청크 분할 콘텐츠를 보내지 않으면, HTTP GET 요청에 `Range` 헤더를 설정하여 이 옵션을 *제안*할 수 있습니다. 

다음 단계에서는 Logic Apps가 청크 분할 콘텐츠를 엔드포인트에서 논리 앱으로 다운로드하는 데 사용하는 자세한 프로세스를 설명합니다.

1. 논리 앱에서 HTTP GET 요청을 엔드포인트에 보냅니다.

   필요에 따라 요청 헤더에는 콘텐츠 청크를 요청하기 위한 바이트 범위를 설명하는 `Range` 필드가 포함될 수 있습니다.

2. 엔드포인트는 "206" 상태 코드와 HTTP 메시지 본문으로 응답합니다.

    이 청크의 콘텐츠에 대한 세부 정보는 Logic Apps에서 청크의 시작과 끝을 결정하는 데 도움이 되는 정보와 청크 분할 이전의 전체 콘텐츠에 대한 총 크기를 포함하여 응답의 `Content-Range` 헤더에 표시됩니다.

3. 논리 앱에서 후속 HTTP GET 요청을 자동으로 보냅니다.

    논리 앱에서는 전체 콘텐츠를 검색할 때까지 후속 GET 요청을 보냅니다.

예를 들어 다음 작업 정의에서는 `Range` 헤더를 설정하는 HTTP GET 요청을 보여 줍니다. 헤더는 엔드포인트에서 청크 분할 콘텐츠로 응답해야 한다고 *제안*합니다.

```json
"getAction": {
    "inputs": {
        "headers": {
            "Range": "bytes=0-1023"
        },
       "method": "GET",
       "uri": "http://myAPIendpoint/api/downloadContent"
    },
    "runAfter": {},
    "type": "Http"
}
```

GET 요청은 "Range" 헤더를 바이트 범위인 "bytes=0-1023"으로 설정합니다. 엔드포인트에서 부분 콘텐츠 요청을 지원하면, 엔드포인트는 요청된 범위의 콘텐츠 청크로 응답합니다. 엔드포인트에 따라 "Range" 헤더 필드에 대한 정확한 형식이 다를 수 있습니다.

<a name="upload-chunks"></a>

## <a name="upload-content-in-chunks"></a>청크로 콘텐츠 업로드

HTTP 작업에서 청크 분할 콘텐츠를 업로드하려면 작업의 `runtimeConfiguration` 속성을 통해 청크 분할 지원을 사용하도록 설정되어 있어야 합니다. 이 설정을 통해 작업에서 청크 분할 프로토콜을 시작할 수 있습니다. 그런 다음, 논리 앱에서 초기 POST 또는 PUT 메시지를 대상 엔드포인트에 보낼 수 있습니다. 엔드포인트에서 제안된 청크 크기로 응답하면 논리 앱에서 콘텐츠 청크가 포함된 HTTP PATCH 요청을 보내서 처리합니다.

다음 단계에서는 Logic Apps가 청크 분할 콘텐츠를 논리 앱에서 엔드포인트로 업로드하는 데 사용하는 자세한 프로세스를 설명합니다.

1. 논리 앱에서 빈 메시지 본문이 있는 초기 HTTP POST 또는 PUT 요청을 보냅니다. 요청 헤더에는 논리 앱에서 청크로 업로드하려는 콘텐츠에 대한 다음 정보가 포함됩니다.

   | Logic Apps 요청 헤더 필드 | 값 | 유형 | Description |
   |---------------------------------|-------|------|-------------|
   | **x-ms-transfer-mode** | chunked | String | 콘텐츠가 청크로 업로드됨을 나타냅니다. |
   | **x-ms-content-length** | <*content-length*> | 정수 | 청크 분할 이전의 전체 콘텐츠 크기(바이트)입니다. |
   ||||

2. 엔드포인트에서 "200" 성공 상태 코드와 다음과 같은 선택적 정보로 응답합니다.

   | 엔드포인트 응답 헤더 필드 | 유형 | 필수 | Description |
   |--------------------------------|------|----------|-------------|
   | **x-ms-chunk-size** | 정수 | 아닙니다. | 제안된 청크 크기(바이트)입니다. |
   | **위치** | String | 예 | HTTP PATCH 메시지를 보낼 URL 위치입니다. |
   ||||

3. 논리 앱에서 각각 다음 정보가 포함된 후속 HTTP PATCH 메시지를 만들어 보냅니다.

   * **x-ms-chunk-size** 또는 총 **x-ms-content-length** 크기의 모든 콘텐츠가 순차적으로 업로드될 때까지 일부 내부적으로 계산된 크기 기준의 콘텐츠 청크

   * 각 PATCH 메시지에 전송되는 콘텐츠 청크에 대한 다음과 같은 헤더 세부 정보:

     | Logic Apps 요청 헤더 필드 | 값 | 유형 | Description |
     |---------------------------------|-------|------|-------------|
     | **Content-Range** | <*range*> | String | 시작 값, 끝 값 및 전체 콘텐츠 크기를 포함하여 현재 콘텐츠 청크에 대한 바이트 범위입니다(예: "bytes=0-1023/10100"). |
     | **Content-Type** | <*content-type*> | String | 청크 분할 콘텐츠의 형식입니다. |
     | **Content-Length** | <*content-length*> | String | 현재 청크의 길이(바이트 크기)입니다. |
     |||||

4. 각 패치 요청 후 엔드포인트은 "200" 상태 코드 및 다음 응답 헤더를 사용 하 여 각 청크를 확인 합니다.

   | 엔드포인트 응답 헤더 필드 | 유형 | 필수 | Description |
   |--------------------------------|------|----------|-------------|
   | **Range** | String | 예 | 엔드포인트에서 받은 콘텐츠의 바이트 범위입니다 (예: "bytes = 0-1023"). |   
   | **x-ms-chunk-size** | 정수 | 아닙니다. | 제안된 청크 크기(바이트)입니다. |
   ||||

예를 들어 다음 작업 정의에서는 청크 분할 콘텐츠를 엔드포인트에 업로드하기 위한 HTTP POST 요청을 보여 줍니다. 작업의 `runTimeConfiguration` 속성에서 `contentTransfer` 속성은 `transferMode`를 `chunked`로 설정합니다.

```json
"postAction": {
    "runtimeConfiguration": {
        "contentTransfer": {
            "transferMode": "chunked"
        }
    },
    "inputs": {
        "method": "POST",
        "uri": "http://myAPIendpoint/api/action",
        "body": "@body('getAction')"
    },
    "runAfter": {
    "getAction": ["Succeeded"]
    },
    "type": "Http"
}
```
