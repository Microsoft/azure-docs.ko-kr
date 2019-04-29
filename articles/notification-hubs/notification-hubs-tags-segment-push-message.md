---
title: 라우팅 및 태그 식
description: 이 문서는 Azure 알림 허브에 대한 알림 및 태그 식을 설명합니다.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0fffb3bb-8ed8-4e0f-89e8-0de24a47f644
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2019
ms.author: jowargo
ms.openlocfilehash: 31a22aabc7b0f1d51a673ef8642037103badcc02
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457807"
---
# <a name="routing-and-tag-expressions"></a>라우팅 및 태그 식

## <a name="overview"></a>개요

태그 식을 사용하면 Notification Hubs를 통해 푸시 알림을 보내는 경우에 특정한 디바이스 집합, 보다 구체적으로는 등록을 대상으로 지정할 수 있습니다.

## <a name="targeting-specific-registrations"></a>특정 등록을 대상으로 지정

특정한 알림 등록을 대상으로 지정하는 유일한 방법은 등록에 태그를 연결한 후에 해당 태그를 대상으로 지정하는 것입니다. [등록 관리](notification-hubs-push-notification-registration-management.md)의 설명처럼, 푸시 알림을 받으려면 앱은 디바이스 핸들을 알림 허브에 등록해야 합니다. 알림 허브에 등록이 생성되면, 애플리케이션 백 엔드는 등록에 푸시 알림을 보낼 수 있습니다. 애플리케이션 백 엔드는 다음과 같은 방식으로 등록이 특정 알림을 대상으로 지정하도록 선택할 수 있습니다.

1. **브로드캐스트**: 알림 허브 내의 모든 등록이 알림을 수신합니다.
2. **태그**: 지정된 태그를 포함하는 모든 등록이 알림을 수신합니다.
3. **태그 식**: 등록의 태그 집합이 지정된 식과 일치하는 모든 등록이 알림을 수신합니다.

## <a name="tags"></a>태그들

태그에는 영숫자 및 영숫자가 아닌 문자(‘_’, ‘@’, ‘#’, ‘.’, ‘:’, ‘-’)를 포함하는 모든 문자열을 최대 120자까지 사용할 수 있습니다. 다음 예제는 특정 음악 그룹에 대한 토스트 알림을 받을 수 있는 애플리케이션을 보여줍니다. 이 시나리오에서 알림을 라우팅하는 간단한 방법은 등록에 대해 아래 그림처럼 다양한 밴드를 나타내는 태그로 레이블을 붙이는 것입니다.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

이 그림에서 **Beatles**라는 태그가 지정된 메시지는 **Beatles** 태그가 등록된 태블릿에만 전송됩니다.

태그에 대한 등록을 생성하는 내용은 [등록 관리](notification-hubs-push-notification-registration-management.md)를 참조하세요.

[Microsoft Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK에 포함된 `Microsoft.Azure.NotificationHubs.NotificationHubClient` 클래스의 알림 메서드를 사용하여 태그에 알림을 보낼 수 있습니다. Node.js 또는 푸시 알림 REST API를 사용할 수도 있습니다.  다음은 SDK 사용 예입니다.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You requested a Beatles notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You requested a Wailers notification</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");
```

태그를 미리 프로비전하지 않아도 되며 특정 앱과 관련된 다양한 개념을 참조할 수 있습니다. 예를 들어, 이 예제 애플리케이션 사용자는 밴드에 대한 댓글을 추가할 수 있고 자신이 좋아하는 밴드에 대한 댓글뿐만 아니라 친구가 추가하는(어떤 밴드에 관한 댓글이든 상관없이) 댓글에 대해서도 토스트를 수신하고자 합니다. 다음 그림은 이러한 시나리오의 예를 보여줍니다.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

이 그림에서 Alice는 Beatles에 대한 최신 소식에 흥미가 있고, Bob은 Wailers에 대한 최신 소식에 흥미가 있습니다. Bob은 Charlie의 댓글에도 흥미가 있으며, Charlie는 Wailers에 흥미가 있습니다. Beatles에 대한 Charlie의 댓글에 대해 알림이 전송되면 Alice와 Bob이 알림을 수신합니다.

다양한 관심사(예: “band_Beatles” 또는 “follows_Charlie”)를 태그로 인코딩할 수 있는 반면에, 태그는 값이 있는 속성이 아니라 간단한 문자열입니다. 등록은 특정 태그의 존재 또는 부재와만 일치 여부가 확인됩니다.

태그를 사용하여 흥미가 있는 그룹에 알림을 보내는 방법에 대한 단계별 전체 자습서는 [속보](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)를 참조하세요.

> [!NOTE]
> Azure Notification Hubs는 등록당 최대 60개 태그를 지원합니다.

## <a name="using-tags-to-target-users"></a>태그를 사용하여 사용자를 대상으로 지정하기

태그를 사용하는 또 다른 방법은 특정 사용자의 모든 디바이스를 식별하는 것입니다. 등록에 대해 다음 그림과 같이 사용자 ID를 포함하는 태그를 지정할 수 있습니다.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

이 그림에서 uid: Alice 태그가 적용된 메시지는 “uid:Alice” 태그가 적용된 모든 등록에 전송되므로 Alice의 모든 디바이스에도 전송됩니다.

## <a name="tag-expressions"></a>태그 식

알림이 단일 태그가 아니라 태그에 대한 부울 식으로 식별되는 등록 집합을 대상으로 지정해야 하는 경우가 있습니다.

보스턴에 있는 모든 사람에게 Red Sox와 Cardinals의 경기에 대해 미리 알림을 보내는 스포츠 애플리케이션을 생각해 보겠습니다. 클라이언트 앱이 팀과 지역 관심사에 대한 태그를 등록하면, 알림은 Red Sox 또는 Cardinals에 관심이 있는 보스턴의 모든 사람을 대상으로 하게 됩니다. 이 조건은 다음과 같은 부울 식으로 표현될 수 있습니다.

```csharp
(follows_RedSox || follows_Cardinals) && location_Boston
```

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

태그 식은 AND(&&), OR(||), NOT(!)과 같은 부울 연산자를 모두 포함할 수 있습니다. 괄호를 포함할 수도 있습니다. 태그 식은 OR만 포함하는 경우에 태그가 20개로 제한됩니다. 그렇지 않으면 태그가 6개로 제한됩니다.

다음은 SDK를 사용하여 태그 식으로 알림을 보내는 예입니다.

```csharp
Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

String userTag = "(location_Boston && !follows_Cardinals)";

// Windows 8.1 / Windows Phone 8.1
var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

// Windows 10
toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
"You want info on the Red Sox</text></binding></visual></toast>";
outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
```
