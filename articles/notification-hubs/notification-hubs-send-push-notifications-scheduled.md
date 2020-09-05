---
title: 예약된 알림을 보내는 방법 | Microsoft Docs
description: 이 항목에서는 Azure Notification Hubs로 예약된 알림 사용에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
keywords: 푸시 알림, 푸시 알림, 푸시 알림 예약
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 56eedda7f79fedce1e34ad837c92006e5cd8f191
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998274"
---
# <a name="how-to-send-scheduled-notifications"></a>방법: 예약된 알림 보내기

이후 특정 시점에 알림을 보내려는 시나리오가 있지만 알림을 보내는 백 엔드 코드를 다시 시작하는 간편한 방법이 없습니다. 표준 계층 알림 허브는 이후 최대 7일까지 알림을 예약할 수 있는 기능을 지원합니다.


## <a name="schedule-your-notifications"></a>알림 예약
알림을 보낼 때 다음 예제와 같이 Notification Hubs SDK의 [ `ScheduledNotification` 클래스](/dotnet/api/microsoft.azure.notificationhubs.schedulednotification?view=azure-dotnet#microsoft_azure_notificationhubs_schedulednotification) 를 사용 하면 됩니다.

```csharp
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>예약된 알림 취소
또한 해당 notificationId를 사용하여 이전에 예약된 알림을 취소할 수 있습니다.

```csharp
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

보낼 수 있는 예약된 알림 수에는 제한이 없습니다.

## <a name="next-steps"></a>다음 단계

다음 자습서를 참조하세요.

 - [등록된 모든 디바이스에 알림 푸시](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [특정 디바이스에 알림 푸시](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [지역화된 알림 푸시](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [특정 사용자에 알림 푸시](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [위치 기반 알림 푸시](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
