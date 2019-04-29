---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c15d695e072e72c6e7be6dcf49f3ea049a9b70b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560484"
---
템플릿 알림을 보내는 경우 속성 집합을 제공하기만 하면 됩니다. 이 시나리오에서 속성 집합은 현재 뉴스의 지역화된 버전을 포함합니다.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>C# 콘솔 앱을 사용한 알림 보내기

이 섹션에서는 콘솔 앱을 사용하여 알림을 보내는 방법을 보여 줍니다. 코드는 Windows 스토어 및 iOS 디바이스 모두에 알림을 전파합니다. 이전에 다음 코드를 사용하여 만든 콘솔 앱에서 `SendTemplateNotificationAsync` 메서드를 수정합니다.

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

SendTemplateNotificationAsync 메서드는 플랫폼에 관계 없이 **모든** 사용자 디바이스에 지역화된 뉴스를 제공합니다. 알림 허브는 올바른 네이티브 페이로드를 빌드하여 특정 태그를 구독하는 모든 디바이스에 제공합니다.

### <a name="sending-notification-with-mobile-services"></a>Mobile Services로 알림 보내기

Mobile Services 스케줄러에서 다음 스크립트를 사용합니다.

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
