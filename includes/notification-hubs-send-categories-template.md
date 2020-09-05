---
title: 포함 파일
description: 포함 파일
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 48907713082ebb1008ad963121671b36af7f2731
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74228166"
---
이 섹션에서는 .NET 콘솔 앱에서 태그가 지정된 템플릿 알림으로 속보를 보냅니다.

1. Visual Studio에서 다음과 같이 새로운 Visual C# 콘솔 애플리케이션을 만듭니다.
    1. 메뉴에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
    1. **새 프로젝트 만들기**의 템플릿 목록에서 C#용 **콘솔 앱(.NET Framework)** 을 선택하고 **다음**을 선택합니다.
    1. 앱의 이름을 입력합니다.
    1. **솔루션**의 경우 **솔루션에 추가**를 선택하고 **만들기**를 선택하여 프로젝트를 만듭니다.

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택한 다음, 콘솔 창에서 다음 명령을 실행합니다.

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   이 작업은 [Microsoft.Azure.NotificationHubs] 패키지를 사용하여 Azure Notification Hubs SDK에 대한 참조를 추가합니다.

1. *Program.cs* 파일을 열고 다음 `using` 문을 추가합니다.

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. `Program` 클래스에서 다음 메서드를 추가하거나 이미 있으면 바꿉니다.

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   이 코드는 문자열 배열에 있는 6개의 각 태그에 대한 템플릿 알림을 보냅니다. 태그를 사용하면 등록된 범주의 알림만 디바이스에서 받습니다.

1. 위의 코드에서 `<hub name>` 및 `<connection string with full access>` 자리 표시자를 알림 허브 이름과 알림 허브의 대시보드에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다.

1. `Main()` 메서드에 다음 줄을 추가합니다.

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. 콘솔 앱을 시작합니다.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: https://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
