---
title: Azure App Service를 사용하여 Xamarin.iOS 앱에 푸시 알림 추가
description: Azure App Service를 사용하여Xamarin.iOS 앱에 푸시 알림을 전송하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 2921214a-49f8-45e1-a306-a85ce21defca
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: de24b64ecd95eec79d7508f978acda9f0ae5a8d6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097543"
---
# <a name="add-push-notifications-to-your-xamarinios-app"></a>Xamarin.iOS 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>개요

이 자습서에서는 푸시 알림을 [Xamarin.iOS 빠른 시작](app-service-mobile-xamarin-ios-get-started.md) 프로젝트에 추가하여 레코드가 삽입될 때마다 디바이스에 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [Xamarin.iOS 빠른 시작 자습서](app-service-mobile-xamarin-ios-get-started.md) 를 완료합니다.
* 실제 iOS 디바이스. 푸시 알림은 iOS 시뮬레이터에서 지원되지 않습니다.

## <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple 개발자 포털의 푸시 알림에 대한 앱 등록

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-your-mobile-app-to-send-push-notifications"></a>푸시 알림을 전송하도록 모바일 앱 구성

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-your-xamarinios-project"></a>Xamarin.iOS 프로젝트 구성

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

## <a name="add-push-notifications-to-your-app"></a>앱에 푸시 알림 추가

1. **QSTodoService**에서 **AppDelegate**가 모바일 클라이언트를 가져올 수 있도록 다음 속성을 추가합니다.

    ```csharp
    public MobileServiceClient GetClient {
        get
        {
            return client;
        }
        private set
        {
            client = value;
        }
    }
    ```

2. 다음 `using` 문을 **AppDelegate.cs** 파일의 맨 위에 추가합니다.

    ```csharp
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

3. **AppDelegate**에서 **FinishedLaunching** 이벤트를 재정의합니다.

   ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // registers for push for iOS8
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

        return true;
    }
    ```

4. 동일한 파일에서 `RegisteredForRemoteNotifications` 이벤트를 재정의합니다. 이 코드에서는 서버에서 지원하는 모든 플랫폼에서 전송되는 간단한 템플릿 알림을 등록하게 됩니다.

    Notification Hubs를 사용하는 템플릿에 대한 자세한 내용은 [템플릿](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)을 참조하세요.

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        MobileServiceClient client = QSTodoService.DefaultService.GetClient;

        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyAPNS}
        };

        // Register for push with your mobile app
        var push = client.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

5. 그런 다음 **DidReceivedRemoteNotification** 이벤트를 재정의합니다.

   ```csharp
    public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps [new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## <a name="test"></a>앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 디바이스에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

   > [!NOTE]
   > 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

2. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.
3. 알림이 수신되는지 확인하고, **확인** 을 클릭하여 알림을 해제합니다.
4. 2단계를 반복하여 앱을 즉시 닫은 후 알림이 표시되는지 확인합니다.

이 자습서를 성공적으로 완료했습니다.
