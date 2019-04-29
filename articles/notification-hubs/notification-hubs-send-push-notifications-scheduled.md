---
title: 예약된 알림을 보내는 방법 | Microsoft Docs
description: 이 항목에서는 Azure Notification Hubs로 예약된 알림 사용에 대해 설명합니다.
services: notification-hubs
documentationcenter: .net
keywords: 푸시 알림, 푸시 알림, 푸시 알림 예약
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
origin.date: 04/14/2018
ms.date: 02/25/2019
ms.author: v-biyu
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457688"
---
# <a name="how-to-send-scheduled-notifications"></a>방법: 예약된 알림 보내기

이후 특정 시점에 알림을 보내려는 시나리오가 있지만 알림을 보내는 백 엔드 코드를 다시 시작하는 간편한 방법이 없습니다. 표준 계층 알림 허브는 이후 최대 7일까지 알림을 예약할 수 있는 기능을 지원합니다.


## <a name="schedule-your-notifications"></a>알림 예약
알림을 보내는 경우 다음 예제와 같이 Notification Hubs SDK에서 [`ScheduledNotification` 클래스](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx)를 사용합니다.

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>예약된 알림 취소
또한 해당 notificationId를 사용하여 이전에 예약된 알림을 취소할 수 있습니다.

```c#
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
