---
title: Azure Event Grid Azure SignalR 이벤트 스키마
description: Azure Event Grid를 사용 하 여 Azure SignalR 이벤트에 제공 되는 속성을 설명 합니다.
services: event-grid
author: chenyl
ms.service: event-grid
ms.topic: reference
ms.date: 06/11/2019
ms.author: chenyl
ms.openlocfilehash: 3b072ff2b680ad6d144c7441190ab2df9870f5d0
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789073"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>SignalR 서비스에 대한 azure Event Grid 이벤트 스키마

이 문서는 SignalR Service 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.


## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

SignalR Service는 다음 이벤트 유형을 내보냅니다.

| 이벤트 유형 | Description |
| ---------- | ----------- |
| Microsoft.SignalRService.ClientConnectionConnected | 클라이언트 연결을 연결 하는 경우 발생 합니다. |
| Microsoft.SignalRService.ClientConnectionDisconnected | 연결이 끊어진 클라이언트 연결이 발생 합니다. |

## <a name="example-event"></a>예제 이벤트

다음 예제에서는 연결의 연결 된 이벤트는 클라이언트의 스키마를 보여줍니다. 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

연결이 끊어진된 클라이언트 연결 이벤트에 대한 스키마가 비슷합니다. 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| topic | string | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| subject | string | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | string | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | string | 이벤트에 대한 고유 식별자입니다. |
| data | 개체(object) | SignalR Service 이벤트 데이터입니다. |
| dataVersion | string | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | string | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 속성 | 형식 | Description |
| -------- | ---- | ----------- |
| timestamp | string | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| hubName | string | 클라이언트 연결에 속한는 허브입니다. |
| connectionId | string | 클라이언트 연결에 대한 고유 식별자입니다. |
| userId | string | 클레임에 정의 된 사용자 식별자입니다. |
| errorMessage | string | 연결이 끊긴 연결 시키는 오류입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
