---
title: 배달 및 다시 시도-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge에서 Event Grid를 배달 하 고 다시 시도 합니다.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7df283b12a0d04d2b785c13a2f12b03115581e79
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841715"
---
# <a name="delivery-and-retry"></a>배달 및 다시 시도

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지에 대해 일치 하는 각 구독에 대해 한 번 이상 배달 하려고 합니다. 구독자의 엔드포인트이 이벤트 수신을 승인 하지 않거나 오류가 발생 한 경우 고정 된 **다시 시도 일정** 및 **재시도 정책**에 따라 배달을 다시 시도 Event Grid.  기본적으로 Event Grid 모듈은 구독자에 게 한 번에 하나의 이벤트를 제공 합니다. 그러나 페이로드는 단일 이벤트를 포함 하는 배열입니다. 출력 일괄 처리 기능을 사용 하도록 설정 하 여 모듈에서 한 번에 둘 이상의 이벤트를 전달 하도록 할 수 있습니다. 이 기능에 대 한 자세한 내용은 [출력 일괄 처리](delivery-output-batching.md)를 참조 하세요.  

> [!IMPORTANT]
>이벤트 데이터에 대 한 지 속성은 지원 되지 않습니다. 즉, Event Grid 모듈을 다시 배포 하거나 다시 시작 하면 아직 배달 되지 않은 이벤트가 모두 손실 됩니다.

## <a name="retry-schedule"></a>다시 시도 일정

Event Grid는 메시지를 전달한 후 응답에 대해 최대 60 초까지 대기 합니다. 구독자의 엔드포인트이 응답을 ACK 하지 않으면 메시지는 이후 재시도를 위해 백오프 큐 중 하나에 큐에 삽입 됩니다.

다시 시도가 시도 되는 일정을 결정 하는 두 개의 미리 구성 된 백오프 큐가 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

| 일정 | Description |
| ---------| ------------ |
| 1분 | 여기에서 종료 되는 메시지는 1 분 마다 시도 됩니다.
| 10분 | 여기에서 종료 되는 메시지는 10 분 마다 시도 됩니다.

### <a name="how-it-works"></a>작동 원리

1. 메시지가 Event Grid 모듈에 도착 합니다. 이를 즉시 제공 하려고 합니다.
1. 배달이 실패 하면 메시지는 1 분 큐로 큐에 배치 되 고 1 분 후에 다시 시도 됩니다.
1. 배달이 계속 실패할 경우 메시지가 10 분 큐에 큐에 배치 되 고 10 분 마다 다시 시도 됩니다.
1. 성공 또는 다시 시도 정책 제한에 도달할 때까지 배달을 시도 합니다.

## <a name="retry-policy-limits"></a>재시도 정책 제한

재시도 정책을 결정 하는 두 가지 구성이 있습니다. 아래에 이 계정과 키의 예제가 나와 있습니다.

* 최대 시도 횟수
* 이벤트 TTL (time to live)

재시도 정책의 한도에 도달 하면 이벤트가 삭제 됩니다. 다시 시도 일정은 다시 시도 일정 섹션에 설명 되어 있습니다. 이러한 제한 구성은 모든 구독자 또는 구독 별로 수행할 수 있습니다. 다음 섹션에서는 각 항목에 대해 자세히 설명 합니다.

## <a name="configuring-defaults-for-all-subscribers"></a>모든 구독자에 대 한 기본값 구성

`brokers__defaultMaxDeliveryAttempts` 및 `broker__defaultEventTimeToLiveInSeconds`에는 Event Grid 배포의 일부로 구성할 수 있는 두 가지 속성이 있습니다 .이 속성은 모든 구독자의 다시 시도 정책 기본값을 제어 합니다.

| 속성 이름 | Description |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | 이벤트를 전달할 최대 시도 횟수입니다. 기본값은 30입니다.
| `broker__defaultEventTimeToLiveInSeconds` | 이벤트가 전달 되지 않은 경우 삭제 되는 이벤트 TTL (초)입니다. 기본값: **7200** 초

## <a name="configuring-defaults-per-subscriber"></a>구독자 당 기본값 구성

구독 별로 재시도 정책 제한을 지정할 수도 있습니다.
구독자 마다 기본값을 구성 하는 방법에 대 한 자세한 내용은 [API 설명서](api.md) 를 참조 하세요. 구독 수준 기본값은 모듈 수준 구성을 재정의 합니다.

## <a name="examples"></a>예시

다음 예제에서는 maxNumberOfAttempts = 3 및 이벤트 TTL 30 분을 사용 하 여 Event Grid 모듈에서 재시도 정책을 설정 합니다.

```json
{
  "Env": [
    "broker__defaultMaxDeliveryAttempts=3",
    "broker__defaultEventTimeToLiveInSeconds=1800"
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

다음 예제에서는 maxNumberOfAttempts = 3 및 30 분의 이벤트 TTL을 사용 하 여 웹 후크 구독을 설정 합니다.

```json
{
 "properties": {
  "destination": {
   "endpointType": "WebHook",
   "properties": {
    "endpointUrl": "<your_webhook_url>",
    "eventDeliverySchema": "eventgridschema"
   }
  },
  "retryPolicy": {
   "eventExpiryInMinutes": 30,
   "maxDeliveryAttempts": 3
  }
 }
}
```
