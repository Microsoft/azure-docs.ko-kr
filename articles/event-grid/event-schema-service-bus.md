---
title: Azure Event Grid Service Bus 이벤트 스키마
description: Azure Event Grid를 사용하여 Service Bus 이벤트에 제공되는 속성을 설명합니다.
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: babanisa
ms.openlocfilehash: f44d2c1c5be6ac895b6f5ea9feca29c0f8ed09f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561764"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Service Bus에 대한 Azure Event Grid 이벤트 스키마

이 문서에서는 Service Bus 이벤트에 대한 속성 및 스키마를 제공합니다. 이벤트 스키마에 대한 소개는 [Azure Event Grid 이벤트 스키마](event-schema.md)를 참조하세요.

샘플 스크립트 및 자습서 목록은 [Service Bus 이벤트 원본](event-sources.md#service-bus)을 참조하세요.

## <a name="available-event-types"></a>사용할 수 있는 이벤트 유형

Service Bus는 다음과 같은 이벤트 유형을 내보냅니다.

| 이벤트 유형 | 설명 |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | 큐 또는 구독에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 발생합니다. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | 배달 못한 편지 큐에 활성 메시지가 있고 수신 대기 중인 수신기가 없는 경우 발생합니다. |

## <a name="example-event"></a>예제 이벤트

다음 예제에서는 수신기 이벤트가 없는 활성 메시지의 스키마를 보여줍니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

배달 못한 편지 큐 이벤트에 대한 스키마는 다음과 같이 유사합니다.

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>이벤트 속성

이벤트에는 다음과 같은 최상위 데이터가 있습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| 토픽 | 문자열 | 이벤트 원본에 대한 전체 리소스 경로입니다. 이 필드는 쓸 수 없습니다. Event Grid는 이 값을 제공합니다. |
| 제목 | 문자열 | 게시자가 정의한 이벤트 주체에 대한 경로입니다. |
| eventType | 문자열 | 이 이벤트 원본에 대해 등록된 이벤트 유형 중 하나입니다. |
| eventTime | 문자열 | 공급자의 UTC 시간을 기준으로 이벤트가 생성되는 시간입니다. |
| id | 문자열 | 이벤트에 대한 고유 식별자입니다. |
| 데이터 | object | Blob Storage 이벤트 데이터입니다. |
| dataVersion | 문자열 | 데이터 개체의 스키마 버전입니다. 게시자가 스키마 버전을 정의합니다. |
| metadataVersion | 문자열 | 이벤트 메타데이터의 스키마 버전입니다. Event Grid는 최상위 속성의 스키마를 정의합니다. Event Grid는 이 값을 제공합니다. |

데이터 개체의 속성은 다음과 같습니다.

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| namespaceName | 문자열 | 리소스가 있는 Service Bus 네임스페이스입니다. |
| requestUri | 문자열 | 이벤트를 내보내는 특정 큐 또는 구독의 URI입니다. |
| entityType | 문자열 | 이벤트를 내보내는 Service Bus 엔터티의 유형(큐 또는 구독)입니다. |
| queueName | 문자열 | 큐를 구독하는 경우 활성 메시지가 있는 큐입니다. 토픽/구독을 사용하는 경우 값이 null입니다. |
| topicName | 문자열 | 활성 메시지가 있는 Service Bus 구독이 속하는 토픽입니다. 큐를 사용하는 경우 null 값입니다. |
| subscriptionName | 문자열 | 활성 메시지가 있는 Service Bus 구독입니다. 큐를 사용하는 경우 null 값입니다. |

## <a name="next-steps"></a>다음 단계

* Azure Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.
* Azure Event Grid 구독을 만드는 방법에 대한 자세한 내용은 [Event Grid 구독 스키마](subscription-creation-schema.md)를 참조하세요.
* Service Bus에서 Azure Event Grid를 사용하여 방법에 대한 자세한 내용은 [Service Bus와 Event Grid 간의 통합 개요](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md)를 참조하세요.
* [Functions 및 Logic Apps를 사용하여 Service Bus 이벤트 수신 대기](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json)를 시도합니다.
