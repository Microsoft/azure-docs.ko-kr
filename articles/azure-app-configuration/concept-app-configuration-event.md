---
title: Azure 앱 구성 키-값 이벤트에 대응
description: Azure Event Grid를 사용 하 여 앱 구성 이벤트를 구독할 수 있습니다 .이를 통해 응용 프로그램은 복잡 한 코드 없이 키-값의 변경에 대응할 수 있습니다.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: ae3417f991c0d810d8946cdaf358218ebbe4f6a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590033"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Azure 앱 구성 이벤트에 대응

Azure 앱 구성 이벤트를 사용 하면 응용 프로그램에서 키 값의 변경 내용에 반응할 수 있습니다. 이는 복잡 한 코드 또는 비용이 많이 들고 비효율적인 폴링 서비스 없이 수행 됩니다. 대신, 이벤트는 [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), 사용자 지정 http 수신기에 대 한 [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) 를 통해 구독자에 게 푸시됩니다. 매우, 사용한 만큼만 요금을 지불 하면 됩니다.

Azure 앱 구성 이벤트는 다양 한 재시도 정책 및 배달 못 한 편지 전달을 통해 응용 프로그램에 신뢰할 수 있는 배달 서비스를 제공 하는 Azure Event Grid 전송 됩니다. 자세히 알아보려면 [Event Grid 메시지 배달 및 다시 시도](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)를 참조 하세요.

일반적인 앱 구성 이벤트 시나리오에는 응용 프로그램 구성 새로 고침, 배포 트리거 또는 모든 구성 지향 워크플로가 포함 됩니다. 변경 빈도가 낮더라도 즉각적인 대응이 필요한 시나리오에서는 이벤트 기반 아키텍처가 특히 효율적일 수 있습니다.

간단한 예제를 보려면 [사용자 지정 웹 끝점에 대 한 Azure 앱 구성 이벤트 경로를](./howto-app-configuration-event.md) 살펴보세요. 

![Event Grid 모델](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>사용 가능한 Azure 앱 구성 이벤트
Event Grid는 [이벤트 구독](../event-grid/concepts.md#event-subscriptions)을 사용하여 이벤트 메시지를 구독자에게 라우팅합니다. Azure 앱 구성 이벤트 구독에는 다음 두 가지 유형의 이벤트가 포함 될 수 있습니다.  

> |이벤트 이름|Description|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|키-값이 만들어지거나 바뀔 때 발생 합니다.|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|키 값이 삭제 될 때 발생 합니다.|

## <a name="event-schema"></a>이벤트 스키마
Azure 앱 구성 이벤트에는 데이터 변경 내용에 응답 하는 데 필요한 모든 정보가 포함 되어 있습니다. EventType 속성이 "Microsoft AppConfiguration"로 시작 하므로 앱 구성 이벤트를 식별할 수 있습니다. Event Grid 이벤트 속성 사용에 대한 추가 정보는 [Event Grid 이벤트 스키마](../event-grid/event-schema.md)에 설명되어 있습니다.  

> |속성|Type|Description|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |토픽|문자열|이벤트를 내보내는 앱 구성의 전체 Azure Resource Manager id입니다.|
> |subject|문자열|이벤트의 키인 키-값의 URI입니다.|
> |eventTime|문자열|이벤트가 생성 된 날짜/시간 (ISO 8601 형식)입니다.|
> |eventType|문자열|"KeyValueModified" 또는 "Microsoft. AppConfiguration. KeyValueDeleted".|
> |Id|문자열|이 이벤트의 고유 식별자입니다.|
> |dataVersion|문자열|데이터 개체의 스키마 버전입니다.|
> |metadataVersion|문자열|최상위 속성의 스키마 버전입니다.|
> |데이터|object|Azure 앱 구성 별 이벤트 데이터의 컬렉션입니다.|
> |data. key|문자열|수정 되거나 삭제 된 키-값의 키입니다.|
> |데이터. 레이블|문자열|수정 되거나 삭제 된 키-값의 레이블 (있는 경우)입니다.|
> |data. etag|문자열|`KeyValueModified`새 키 값의 etag에 대 한입니다. `KeyValueDeleted`삭제 된 키-값의 etag에 대 한입니다.|

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
앱 구성 이벤트를 처리 하는 응용 프로그램은 다음과 같은 권장 방법을 따라야 합니다.
> [!div class="checklist"]
> * 이벤트를 동일한 이벤트 처리기로 라우팅하는 여러 구독을 구성할 수 있으므로 이벤트를 특정 원본에서 가져온 것으로 가정 하지 마십시오. 대신, 메시지의 항목을 확인 하 여 응용 프로그램 구성 인스턴스가 이벤트를 전송 하는지 확인 합니다.
> * EventType을 확인 하 고 수신 하는 모든 이벤트가 원하는 형식이 될 것 이라고 가정 하지 마십시오.
> * Etag 필드를 사용 하 여 개체에 대 한 정보가 아직 최신 상태 인지 파악 합니다.  
> * Sequencer 필드를 사용 하 여 특정 개체에 대 한 이벤트의 순서를 이해할 수 있습니다.
> * 제목 필드를 사용 하 여 수정 된 키-값에 액세스 합니다.


## <a name="next-steps"></a>다음 단계

Event Grid에 대해 자세히 알아보고 Azure 앱 구성 이벤트를 사용해 보세요.

- [Event Grid 정보](../event-grid/overview.md)
- [Azure 앱 구성 이벤트를 사용자 지정 웹 끝점으로 라우팅](./howto-app-configuration-event.md)