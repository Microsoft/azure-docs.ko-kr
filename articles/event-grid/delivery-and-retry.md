---
title: Azure Event Grid 배달 및 다시 시도
description: Azure Event Grid에서 이벤트를 배달하는 방법 및 배달되지 않은 메시지를 처리하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: 6dfa84eff8dcc104ae6f9c16262f3b1c697df6c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60562001"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid 메시지 배달 및 다시 시도

이 문서에서는 배달이 승인되지 않는 경우 Azure Event Grid에서 이벤트를 처리하는 방법을 설명합니다.

Event Grid는 지속성이 있는 배달을 제공합니다. 각 메시지를 각 구독에 대해 최소 한 번 배달합니다. 이벤트는 각 구독에 등록된 엔드포인트로 즉시 전송됩니다. 엔드포인트가 이벤트 수신을 확인하지 않는 경우 Event Grid는 이벤트 전달을 재시도합니다.

현재 Event Grid는 각 이벤트를 개별적으로 구독자에게 보냅니다. 구독자는 단일 이벤트로 배열을 받습니다.

## <a name="retry-schedule-and-duration"></a>예약 및 기간 재시도

Event Grid는 이벤트 배달에 대해 지수 백오프 재시도 정책을 사용합니다. 끝점이 응답 하지 않거나 오류 코드가 반환, Event Grid를 최상의 기준으로 다음 일정에 따라 배달을 다시 시도 합니다.

1. 10초
1. 30초
1. 1분
1. 5분
1. 10분
1. 30분
1. 1시간
1. 최대 24 시간 동안 1 시간 마다

Event Grid는 약간의 불규칙을 모든 재시도 단계를 추가 및 끝점 긴 기간 동안 다운 일관 되 게 정상 되지 않거나 무력화 될 것 이므로 경우에 선택적으로 특정 재시도 건너뛸 수 있습니다.

결정적 동작에 대 한 라이브 이벤트 시간을 설정 하 고 최대 배달 시도에 [구독 다시 시도 정책](manage-event-delivery.md)합니다.

기본적으로 Event Grid는 24시간 이내에 배달되지 않는 모든 이벤트를 만료합니다. 이벤트 구독을 만들 때 [재시도 정책을 사용자 지정](manage-event-delivery.md)할 수 있습니다. 최대 배달 시도 횟수(기본값: 30)와 이벤트 TTL(Time to Live, 기본값: 1440분)을 제공합니다.

## <a name="dead-letter-events"></a>배달 못한 편지 이벤트

Event Grid에서 이벤트를 전송할 수 없는 경우, 전송되지 않은 이벤트를 저장소 계정으로 보낼 수 있습니다. 이 프로세스를 배달 못한 편지라고 합니다. 기본적으로 Event Grid에서는 배달 못한 편지를 켜지 않습니다. 이 기능을 사용하려면 이벤트 구독을 만들 때 전송되지 않은 이벤트를 보유할 저장소 계정을 지정해야 합니다. 이 저장소 계정에서 이벤트를 끌어와 전송을 해결합니다.

Event Grid가 모든 재시도 시도 횟수를 시도한 경우 이벤트를 배달 못한 편지로 보냅니다. Event Grid가 400(잘못된 요청) 또는 413(요청 엔터티가 너무 큼) 응답 코드를 수신하는 경우, 즉시 이벤트를 배달 못한 편지 엔드포인트로 보냅니다. 이 응답 코드는 이벤트 전달이 실패하지 않음을 나타냅니다.

이벤트를 배달하기 위한 마지막 시도 및 배달 못한 편지 위치로 이벤트를 배달하는 경우, 그 사이에 5분의 지연이 발생합니다. 이 지연은 Blob Storage 작업 수를 줄이기 위함입니다. 배달 못한 편지 위치를 4시간 동안 사용할 수 없는 경우 이벤트가 삭제됩니다.

배달 못한 편지 위치를 설정하기 전에 컨테이너를 포함하는 저장소 계정이 있어야 합니다. 이벤트 구독을 만들 때 이 컨테이너에 대한 엔드포인트를 제공합니다. 엔드포인트는 다음과 같은 형식입니다. `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

이벤트가 배달 못한 편지 위치로 전송된 경우 알림을 받을 수 있습니다. Event Grid를 사용하여 전송되지 않은 이벤트에 응답하려면 배달 못한 편지 Blob Storage에 대한 [이벤트 구독을 만듭니다](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json). 배달 못한 편지 Blob Storage가 전송되지 않은 이벤트를 수신할 때마다 Event Grid에서 처리기에 알립니다. 처리기는 전송되지 않은 이벤트를 조정하기 위해 수행할 작업으로 응답합니다.

배달 못한 편지 위치를 설정하는 예제는 [배달 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.

## <a name="message-delivery-status"></a>메시지 배달 상태

Event Grid는 HTTP 응답 코드를 사용하여 이벤트의 수신을 승인합니다. 

### <a name="success-codes"></a>성공 코드

다음 HTTP 응답 코드는 이벤트가 사용자 웹후크에 성공적으로 전달되었음을 나타냅니다. Event Grid는 배달이 완료되었다고 간주합니다.

- 200 정상
- 202 수락됨

### <a name="failure-codes"></a>실패 코드

다음 HTTP 응답 코드는 이벤트 배달 시도가 실패했음을 나타냅니다.

- 400 잘못된 요청
- 401 권한 없음
- 404 찾을 수 없음
- 408 요청 시간 초과
- 413 요청 엔터티가 너무 큼
- 414 URI가 너무 김
- 429 요청이 너무 많음
- 500 내부 서버 오류
- 503 서비스를 사용할 수 없음
- 504 게이트웨이 시간 초과

## <a name="next-steps"></a>다음 단계

* 이벤트 배달 상태를 보려면 [Event Grid 메시지 배달 모니터링](monitor-event-delivery.md)을 참조하세요.
* 이벤트 전달 옵션을 사용자 지정하려면 [전송 못한 편지 및 재시도 정책](manage-event-delivery.md)을 참조하세요.