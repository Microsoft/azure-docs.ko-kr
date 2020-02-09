---
title: Azure SignalR Service 이벤트에 대응
description: Azure Event Grid를 사용 하 여 Azure SignalR Service 이벤트를 구독 합니다. 이러한 이벤트는 다른 다운스트림 서비스를 트리거할 수 있습니다.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158201"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Azure SignalR Service 이벤트에 대응

Azure SignalR Service 이벤트를 사용 하면 응용 프로그램이 최신 서버를 사용 하지 않는 아키텍처를 사용 하 여 연결 되거나 연결이 끊어진 클라이언트 연결에 반응할 수 복잡한 코드나 비용이 많이 들고 비효율적인 폴링 서비스가 없어도 이렇게 할 수 있습니다.  대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자로 푸시되거나 사용자 지정 HTTP 수신기로도 푸시되며, 요금은 사용한 만큼만 청구됩니다.

Azure SignalR Service 이벤트는 풍부한 재시도 정책 및 배달 못 한 편지 전달을 통해 응용 프로그램에 신뢰할 수 있는 배달 서비스를 제공 하는 Event Grid 서비스로 안정적으로 전송 됩니다. 자세히 알아보려면 [Event Grid 메시지 배달 및 다시 시도](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)를 참조 하세요.

![Event Grid 모델](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>서버 리스 상태
Azure SignalR Service 이벤트는 클라이언트 연결이 서버를 사용 하지 않는 상태인 경우에만 활성화 됩니다. 일반적으로 클라이언트는 허브 서버로 라우팅되지 않는 경우 서버를 사용 하지 않는 상태로 전환 됩니다. 클래식 모드는 클라이언트 연결에 연결 된 허브에 허브 서버가 없는 경우에만 작동 합니다. 그러나 일부 문제를 방지 하려면 서버를 사용 하지 않는 모드를 사용 하는 것이 좋습니다. 서비스 모드에 대한 자세한 정보 [는 서비스 모드를 선택 하는 방법](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)을 참조 하세요.

## <a name="available-azure-signalr-service-events"></a>사용 가능한 Azure SignalR 서비스 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure SignalR Service 이벤트 구독은 다음과 같은 두 가지 유형의 이벤트를 지원 합니다.  

|이벤트 이름|설명|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|클라이언트 연결이 연결 될 때 발생 합니다.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|클라이언트 연결의 연결이 끊어질 때 발생 합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure SignalR Service 이벤트는 데이터의 변경 내용에 응답 하는 데 필요한 모든 정보를 포함 합니다. "SignalRService"로 시작 하는 eventType 속성을 사용 하 여 Azure SignalR Service 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 설명 되어 있습니다.  

클라이언트 연결 연결 이벤트의 예는 다음과 같습니다.
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

자세한 내용은 [SignalR Service events schema](../event-grid/event-schema-azure-signalr.md)항목을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure SignalR 서비스 이벤트를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure SignalR Service
> 와 통합 Event Grid 샘플을 사용해 보세요](./signalr-howto-event-grid-integration.md) [Event Grid](../event-grid/overview.md)
