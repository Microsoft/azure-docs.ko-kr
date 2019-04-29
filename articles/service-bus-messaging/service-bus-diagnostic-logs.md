---
title: Azure Service Bus 진단 로그 | Microsoft Docs
description: Azure에서 Service Bus에 대한 진단 로그를 설정하는 방법을 알아봅니다.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7d4cb8e55c5d1561c09cf85122550a66e3671f17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60714142"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus 진단 로그

Azure Service Bus에 대해 다음 두 가지 유형의 로그를 볼 수 있습니다.
* **[활동 로그](../azure-monitor/platform/activity-logs-overview.md)**. 이러한 로그에는 작업에서 수행된 작업 관련 정보가 포함됩니다. 로그는 항상 켜져 있습니다.
* **[진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md)**. 작업에서 발생하는 모든 상황을 보다 잘 이해할 수 있도록 진단 로그를 구성할 수 있습니다. 진단 로그는 업데이트 및 작업이 실행 중일 때 발생하는 활동을 비롯하여 작업이 만들어질 때부터 삭제될 때까지의 모든 활동을 포함합니다.

## <a name="turn-on-diagnostic-logs"></a>진단 로그 설정

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)의 **모니터링 + 관리**에서 **진단 로그**를 클릭합니다.

    ![진단 로그에 대한 블레이드 탐](./media/service-bus-diagnostic-logs/image1.png)

2. 모니터링하려는 리소스를 클릭합니다.  

3.  **진단 켜기**를 클릭합니다.

    ![진단 로그 사용](./media/service-bus-diagnostic-logs/image2.png)

4.  **상태**에서 **켜기**를 클릭합니다.

    ![진단 로그 상태 변경](./media/service-bus-diagnostic-logs/image3.png)

5.  원하는 보관 대상을 설정합니다 예를 들어 저장소 계정, event hub 또는 Azure Monitor를 기록합니다.

6.  새 진단 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 블레이드에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)를 참조하세요.

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

## <a name="operational-logs-schema"></a>작업 로그 스키마

**OperationalLogs** 범주의 로그는 Service Bus 작업 중 발생하는 일을 캡처합니다. 특히 이러한 로그는 큐 생성, 사용된 리소스 및 작업 상태를 비롯한 작업 형식을 캡처합니다.

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

이름 | 설명
------- | -------
ActivityId | 추적에 사용되는 내부 ID
EventName | 작업 이름           
ResourceId | Azure Resource Manager 리소스 ID
SubscriptionId | 구독 ID
EventTimeString | 작업 시간
EventProperties | 작업 속성
상태 | 작업 상태
Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트)
카테고리 | OperationalLogs

작업 로그 JSON 문자열 예제는 다음과 같습니다.

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 자세한 내용을 보려면 다음 링크를 참조하세요.

* [Service Bus 소개](service-bus-messaging-overview.md)
* [Service Bus 시작](service-bus-dotnet-get-started-with-queues.md)
