---
title: 진단 로그 설정 - Azure Event Hub | Microsoft Docs
description: Azure에서 이벤트 허브의 활동 로그 및 진단 로그를 설정하는 방법을 배웁니다.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c8f2dba8ff30ceae4085d96640623a01b6784b1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822321"
---
# <a name="set-up-diagnostic-logs-for-an-azure-event-hub"></a>Azure 이벤트 허브에 대한 진단 로그 설정

Azure Event Hubs에 대해 다음 두 가지 유형의 로그를 볼 수 있습니다.

* **[활동 로그](../azure-monitor/platform/activity-logs-overview.md)**: 이러한 로그에는 작업에 대해 수행된 작업 관련 정보가 포함됩니다. 로그는 항상 켜져 있습니다.
* **[진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md)**: 작업에서 발생하는 모든 상황을 보다 잘 이해할 수 있도록 진단 로그를 구성할 수 있습니다. 진단 로그는 업데이트 및 작업이 실행 중일 때 발생하는 활동을 비롯하여 작업이 만들어질 때부터 삭제될 때까지의 모든 활동을 포함합니다.

## <a name="enable-diagnostic-logs"></a>진단 로그 활성화

진단 로그는 기본적으로 해제되어 있습니다. 진단 로그를 활성화하려면 다음 단계를 수행합니다.

1.  [Azure Portal](https://portal.azure.com)의 **모니터링 + 관리**에서 **진단 로그**를 클릭합니다.

    ![진단 로그에 대한 창 탐색](./media/event-hubs-diagnostic-logs/image1.png)

2.  모니터링하려는 리소스를 클릭합니다.

3.  **진단 켜기**를 클릭합니다.

    ![진단 로그 설정](./media/event-hubs-diagnostic-logs/image2.png)

4.  **상태**에서 **켜기**를 클릭합니다.

    ![진단 로그의 상태 변경](./media/event-hubs-diagnostic-logs/image3.png)

5.  원하는 보관 대상을 설정합니다 예를 들어 저장소 계정, event hub 또는 Azure Monitor를 기록합니다.

6.  새 진단 설정을 저장합니다.

새 설정은 약 10분 후에 적용됩니다. 그런 다음 구성된 보관 대상의 **진단 로그** 창에 로그가 나타납니다.

진단 구성에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/diagnostic-logs-overview.md)를 참조하세요.

## <a name="diagnostic-logs-categories"></a>진단 로그 범주

Event Hubs는 다음 두 가지 범주에 대한 진단 로그를 캡처합니다.

* **보관 로그:** Event Hubs 보관, 특히 보관 오류와 관련된 로그입니다.
* **작업 로그:** Event Hubs 작업 중에 발생하는 정보, 특히 이벤트 허브 만들기, 사용된 리소스 및 작업 상태와 같은 작업 유형입니다.

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마

모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 섹션에 설명된 형식을 사용하는 문자열 필드가 있습니다.

### <a name="archive-logs-schema"></a>보관 로그 스키마

보관 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

이름 | 설명
------- | -------
TaskName | 실패한 작업에 대한 설명입니다.
ActivityId | 추적에 사용되는 내부 ID입니다.
trackingId | 추적에 사용되는 내부 ID입니다.
resourceId | Azure Resource Manager 리소스 ID입니다.
eventHub | 이벤트 허브 전체 이름(네임스페이스 이름 포함)입니다.
partitionId | 데이터가 기록되는 이벤트 허브 파티션입니다.
archiveStep | ArchiveFlushWriter
startTime | 실패 시작 시간입니다.
failures | 실패가 발생한 횟수입니다.
durationInSeconds | 실패 기간입니다.
Message | 오류 메시지입니다.
category | ArchiveLogs

다음 코드는 보관 로그 JSON 문자열에 대한 예입니다.

```json
{
   "TaskName": "EventHubArchiveUserError",
   "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
   "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
   "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
   "partitionId": "1",
   "archiveStep": "ArchiveFlushWriter",
   "startTime": "9/22/2016 5:11:21 AM",
   "failures": 3,
   "durationInSeconds": 360,
   "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
   "category": "ArchiveLogs"
}
```

### <a name="operational-logs-schema"></a>작업 로그 스키마

작업 로그 JSON 문자열에는 다음 표에 나열된 요소가 포함되어 있습니다.

이름 | 설명
------- | -------
ActivityId | 추적 목적에 사용되는 내부 ID입니다.
EventName | 작업 이름입니다.  
resourceId | Azure Resource Manager 리소스 ID입니다.
SubscriptionId | 구독 ID가 표시됩니다.
EventTimeString | 작업 시간입니다.
EventProperties | 작업 속성입니다.
상태 | 작업 상태입니다.
Caller | 작업 호출자(Azure Portal 또는 관리 클라이언트)입니다.
category | OperationalLogs

다음 코드는 작업 로그 JSON 문자열에 대한 예입니다.

```json
Example:
{
   "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
   "EventName": "Create EventHub",
   "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
   "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
   "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
   "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
   "Status": "Succeeded",
   "Caller": "ServiceBus Client",
   "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>다음 단계
* [Event Hubs 소개](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 개요](event-hubs-api-overview.md)
* [Event Hubs 시작](event-hubs-dotnet-standard-getstarted-send.md)
