---
title: Azure 앱 구성 키-값 이벤트에 대응 | Microsoft Docs
description: Azure Event Grid를 사용 하 여 앱 구성 이벤트를 구독 합니다.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185293"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure 앱 구성 이벤트에 대응

Azure 앱 구성 이벤트를 사용 하면 응용 프로그램에서 키 값의 변경 내용에 반응할 수 있습니다. 이는 복잡 한 코드 또는 비용이 많이 들고 비효율적인 폴링 서비스 없이 수행 됩니다. 대신, 이벤트는 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/)를 통해 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)와 같은 구독자로 푸시되거나 사용자 지정 HTTP 수신기로도 푸시되며, 요금은 사용한 만큼만 청구됩니다.

Azure 앱 구성 이벤트는 다양 한 재시도 정책 및 배달 못 한 편지 전달을 통해 응용 프로그램에 신뢰할 수 있는 배달 서비스를 제공 하는 Azure Event Grid 전송 됩니다. 자세히 알아보려면 [Event Grid 메시지 배달 및 다시 시도](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)를 참조 하세요.

일반적인 앱 구성 이벤트 시나리오에는 응용 프로그램 구성 새로 고침, 배포 트리거 또는 모든 구성 지향 워크플로가 포함 됩니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

간단한 예제를 보려면 [사용자 지정 웹 엔드포인트에 대한 Azure 앱 구성 이벤트 경로를](./howto-app-configuration-event.md) 살펴보세요. 

![Event Grid 모델](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>사용 가능한 Azure 앱 구성 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure 앱 구성 이벤트 구독에는 다음 두 가지 유형의 이벤트가 포함 될 수 있습니다.  

> |이벤트 이름|설명|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|키-값이 만들어지거나 바뀔 때 발생 합니다.|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|키 값이 삭제 될 때 발생 합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure 앱 구성 이벤트에는 데이터 변경 내용에 응답 하는 데 필요한 모든 정보가 포함 되어 있습니다. EventType 속성이 "Microsoft AppConfiguration"로 시작 하므로 앱 구성 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 설명되어 있습니다.  

> |속성|에|설명|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |토픽|string|이벤트를 내보내는 앱 구성의 전체 Azure Resource Manager id입니다.|
> |subject|string|이벤트의 키인 키-값의 URI입니다.|
> |eventTime|string|이벤트가 생성 된 날짜/시간 (ISO 8601 형식)입니다.|
> |eventType|string|"KeyValueModified" 또는 "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|string|이 이벤트의 고유 식별자입니다.|
> |dataVersion|string|데이터 개체의 스키마 버전입니다.|
> |metadataVersion|string|최상위 속성의 스키마 버전입니다.|
> |데이터|object|Azure 앱 구성 별 이벤트 데이터의 컬렉션입니다.|
> |data.key|string|수정 되거나 삭제 된 키-값의 키입니다.|
> |data.label|string|수정 되거나 삭제 된 키-값의 레이블 (있는 경우)입니다.|
> |data.etag|string|새 키 값의 etag를 `KeyValueModified` 합니다. `KeyValueDeleted` 삭제 된 키-값의 etag입니다.|

KeyValueModified 이벤트의 예는 다음과 같습니다.
```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]

```

자세한 내용은 [Azure 앱 구성 이벤트 스키마](../event-grid/event-schema-app-configuration.md)를 참조 하세요.

## <a name="practices-for-consuming-events"></a>이벤트 사용에 관한 지침
앱 구성 이벤트를 처리 하는 응용 프로그램은 다음과 같은 몇 가지 권장 방법을 따라야 합니다.
> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하는 여러 구독을 구성할 수 있으므로 이벤트를 특정 원본에서 가져온 것으로 가정 하지 않는 것이 중요 하며, 메시지의 항목을 확인 하 여 예상 되는 앱 구성에서 온 것인지 확인 해야 합니다.
> * 마찬가지로, eventType이 본인이 처리하려는 형식인지 확인하고, 수신된 모든 이벤트가 예상하는 형식일 것이라고 간주하지 않도록 합니다.
> * 메시지가 잘못된 순서로 오거나 조금 늦게 도착할 수도 있으므로 etag 필드를 사용하여 개체에 대한 정보가 아직 최신 상태인지 여부를 확인합니다.  또한 sequencer 필드를 사용하여 특정 개체에 대한 이벤트 순서를 파악합니다.
> * 제목 필드를 사용 하 여 수정 된 키-값에 액세스 합니다.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure 앱 구성 이벤트를 사용해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure 앱 구성 이벤트를 사용자 지정 웹 엔드포인트으로 라우팅](./howto-app-configuration-event.md)