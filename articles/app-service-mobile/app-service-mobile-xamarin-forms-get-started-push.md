---
title: Xamarin.Forms 앱에 푸시 알림 추가 | Microsoft Docs
description: Azure 서비스를 사용하여 Xamarin.Forms 앱에 다중 플랫폼 푸시 알림을 전송하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 99f2d9fb7c9a74e57eff3cd0b007fcee459cab88
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098729"
---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Xamarin.Forms 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>개요

이 자습서에서는 [Xamarin.Forms 빠른 시작](app-service-mobile-xamarin-forms-get-started.md)으로 인해 발생한 모든 프로젝트에 푸시 알림을 추가합니다. 즉, 레코드가 삽입될 때마다 모든 플랫폼 간 클라이언트로 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

iOS의 경우 [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/) 및 실제 iOS 디바이스가 필요합니다. [iOS 시뮬레이터는 푸시 알림을 지원하지 않습니다](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="configure-hub"></a>알림 허브 구성

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Android 프로젝트 구성 및 실행(선택 사항)

이 섹션을 완료하여 Android용 Xamarin.Forms Droid 프로젝트에 대한 푸시 알림을 사용하도록 설정합니다.

### <a name="enable-firebase-cloud-messaging-fcm"></a>FCM(Firebase Cloud Messaging) 사용

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>FCM을 사용하여 푸시 요청을 보내도록 Mobile Apps 백 엔드 구성

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Android 프로젝트에 푸시 알림 추가

FCM를 사용하여 백 엔드를 구성한 경우 FCM에 등록할 클라이언트에 구성 요소 및 코드를 추가할 수 있습니다. 또한 Mobile Apps 백 엔드를 통해 Azure Notification Hubs에 푸시 알림을 등록하고 알림을 수신할 수도 있습니다.

1. **Droid** 프로젝트에서 **참조 > NuGet 패키지 관리...** 를 마우스 오른쪽 단추로 클릭합니다.
1. NuGet 패키지 관리자 창에서 **Xamarin.Firebase.Messaging** 패키지를 검색하고 프로젝트에 추가합니다.
1. **Droid** 프로젝트에 대한 프로젝트 속성에서 앱을 Android 버전 7.0 이상을 사용하여 컴파일하도록 설정합니다.
1. Firebase 콘솔에서 다운로드한 **google-services.json** 파일을 **Droid** 프로젝트의 루트에 추가하고 해당 빌드 작업을 **GoogleServicesJson**으로 설정합니다. 자세한 내용은 [Google 서비스 JSON 파일 추가](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/#Add_the_Google_Services_JSON_File)를 참조하세요.

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging 등록

1. **AndroidManifest.xml** 파일을 열고 다음 `<receiver>` 요소를 `<application>` 요소로 삽입합니다.

    ```xml
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdInternalReceiver" android:exported="false" />
    <receiver android:name="com.google.firebase.iid.FirebaseInstanceIdReceiver" android:exported="true" android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
        <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name="${applicationId}" />
        </intent-filter>
    </receiver>
    ```

#### <a name="implementing-the-firebase-instance-id-service"></a>Firebase 인스턴스 ID 서비스 구현

1. 새 클래스를 `FirebaseRegistrationService`로 명명된 **Droid** 프로젝트에 추가하고 다음 `using` 문이 파일 맨 위에 있는지 확인합니다.

    ```csharp
    using System.Threading.Tasks;
    using Android.App;
    using Android.Util;
    using Firebase.Iid;
    using Microsoft.WindowsAzure.MobileServices;
    ```

1. 빈 `FirebaseRegistrationService` 클래스를 다음 코드로 바꿉니다.

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class FirebaseRegistrationService : FirebaseInstanceIdService
    {
        const string TAG = "FirebaseRegistrationService";

        public override void OnTokenRefresh()
        {
            var refreshedToken = FirebaseInstanceId.Instance.Token;
            Log.Debug(TAG, "Refreshed token: " + refreshedToken);
            SendRegistrationTokenToAzureNotificationHub(refreshedToken);
        }

        void SendRegistrationTokenToAzureNotificationHub(string token)
        {
            // Update notification hub registration
            Task.Run(async () =>
            {
                await AzureNotificationHubService.RegisterAsync(TodoItemManager.DefaultManager.CurrentClient.GetPush(), token);
            });
        }
    }
    ```

    `FirebaseRegistrationService` 클래스는 FCM에 액세스하는 애플리케이션을 인증하는 보안 토큰 생성을 담당합니다. 애플리케이션이 FCM에서 등록 토큰을 수신하는 경우 `OnTokenRefresh` 메서드가 호출됩니다. 메서드는 FCM에서 비동기적으로 업데이트되는 `FirebaseInstanceId.Instance.Token` 속성에서 토큰을 검색합니다. 토큰은 애플리케이션을 설치하거나 제거할 때, 사용자가 애플리케이션 데이터를 삭제할 때, 애플리케이션이 인스턴스 ID를 지울 때 또는 토큰의 보안이 손상되었을 때만 업데이트되므로 `OnTokenRefresh` 메서드는 드물게 호출됩니다. 또한 FCM 인스턴스 ID 서비스는 애플리케이션에서 해당 토큰을 정기적으로, 일반적으로 6개월마다 새로 고치도록 요청합니다.

    `OnTokenRefresh` 메서드는 또한 사용자의 등록 토큰을 Azure 알림 허브에 연결하는 데 사용되는 `SendRegistrationTokenToAzureNotificationHub` 메서드를 호출합니다.

#### <a name="registering-with-the-azure-notification-hub"></a>Azure 알림 허브 등록

1. 새 클래스를 `AzureNotificationHubService`로 명명된 **Droid** 프로젝트에 추가하고 다음 `using` 문이 파일 맨 위에 있는지 확인합니다.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Android.Util;
    using Microsoft.WindowsAzure.MobileServices;
    using Newtonsoft.Json.Linq;
    ```

1. 빈 `AzureNotificationHubService` 클래스를 다음 코드로 바꿉니다.

    ```csharp
    public class AzureNotificationHubService
    {
        const string TAG = "AzureNotificationHubService";

        public static async Task RegisterAsync(Push push, string token)
        {
            try
            {
                const string templateBody = "{\"data\":{\"message\":\"$(messageParam)\"}}";
                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBody}
                };

                await push.RegisterAsync(token, templates);
                Log.Info("Push Installation Id: ", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                Log.Error(TAG, "Could not register with Notification Hub: " + ex.Message);
            }
        }
    }
    ```

    `RegisterAsync` 메서드는 JSON으로 간단한 알림 메시지 템플릿을 만들고, Firebase 등록 토큰을 사용하여 알림 허브에서 템플릿 알림을 수신하도록 등록합니다. 이렇게 하면 Azure 알림 허브에서 보낸 알림은 등록 토큰이 나타내는 디바이스를 대상으로 합니다.

#### <a name="displaying-the-contents-of-a-push-notification"></a>푸시 알림의 콘텐츠 표시

1. 새 클래스를 `FirebaseNotificationService`로 명명된 **Droid** 프로젝트에 추가하고 다음 `using` 문이 파일 맨 위에 있는지 확인합니다.

    ```csharp
    using Android.App;
    using Android.Content;
    using Android.Media;
    using Android.Support.V7.App;
    using Android.Util;
    using Firebase.Messaging;
    ```

1. 빈 `FirebaseNotificationService` 클래스를 다음 코드로 바꿉니다.

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    public class FirebaseNotificationService : FirebaseMessagingService
    {
        const string TAG = "FirebaseNotificationService";

        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);

            // Pull message body out of the template
            var messageBody = message.Data["message"];
            if (string.IsNullOrWhiteSpace(messageBody))
                return;

            Log.Debug(TAG, "Notification message body: " + messageBody);
            SendNotification(messageBody);
        }

        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);

            var notificationBuilder = new NotificationCompat.Builder(this)
                .SetSmallIcon(Resource.Drawable.ic_stat_ic_notification)
                .SetContentTitle("New Todo Item")
                .SetContentText(messageBody)
                .SetContentIntent(pendingIntent)
                .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))
                .SetAutoCancel(true);

            var notificationManager = NotificationManager.FromContext(this);
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    }
    ```

    애플리케이션이 FCM에서 알림을 받을 때 호출되는 `OnMessageReceived` 메서드는 메시지 콘텐츠를 추출하고 `SendNotification` 메서드를 호출합니다. 이 메서드는 메시지 콘텐츠를 알림 영역에 표시되는 알림과 함께 애플리케이션이 실행되는 동안 시작되는 로컬 알림으로 변환합니다.

이제 Android 디바이스 또는 에뮬레이터에서 실행 중인 앱에서 푸시 알림을 테스트할 준비가 되었습니다.

### <a name="test-push-notifications-in-your-android-app"></a>Android 앱에서 푸시 알림 테스트

처음 두 단계는 에뮬레이터에서 테스트할 때만 필요합니다.

1. Google Play 서비스를 사용하여 구성된 디바이스 또는 에뮬레이터에서 배포하거나 디버깅하는지를 확인합니다. **재생** 앱이 디바이스 또는 에뮬레이터에 설치되었는지 보고 확인할 수 있습니다.
2. **앱** > **설정** > **계정 추가**를 클릭하여 Android 디바이스에 Google 계정을 추가합니다. 그런 후 화면 지시에 따라 디바이스에 기존 Google 계정을 추가하거나 새 계정을 만듭니다.
3. Visual Studio 또는 Xamarin Studio에서 **Droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.
4. **실행**을 클릭하여 프로젝트를 빌드하고 Android 디바이스 또는 에뮬레이터에서 앱을 시작합니다.
5. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.
6. 항목이 추가될 때 알림을 받았는지 확인합니다.

## <a name="configure-and-run-the-ios-project-optional"></a>iOS 프로젝트 구성 및 실행(선택 사항)

이 섹션에서는 iOS 디바이스용 Xamarin iOS 프로젝트를 실행합니다. iOS 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

[!INCLUDE [Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>APNS에 대한 알림 허브 구성

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

다음에는 Xamarin Studio 또는 Visual Studio에서 iOS 프로젝트 설정을 구성합니다.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>iOS 앱에 푸시 알림 추가

1. **iOS** 프로젝트에서 AppDelegate.cs를 열고 다음 문을 코드 파일의 맨 위에 추가합니다.

    ```csharp
    using Newtonsoft.Json.Linq;
    ```

2. 또한 **AppDelegate** 클래스에서 **RegisteredForRemoteNotifications** 이벤트 재정의를 추가하여 알림을 등록합니다.

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application,
        NSData deviceToken)
    {
        const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

        // Register for push with your mobile app
        Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
        push.RegisterAsync(deviceToken, templates);
    }
    ```

3. **AppDelegate**에서 **DidReceiveRemoteNotification** 이벤트 처리기에 대한 다음 재정의도 추가합니다.

    ```csharp
    public override void DidReceiveRemoteNotification(UIApplication application,
        NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
    {
        NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

        string alert = string.Empty;
        if (aps.ContainsKey(new NSString("alert")))
            alert = (aps[new NSString("alert")] as NSString).ToString();

        //show alert
        if (!string.IsNullOrEmpty(alert))
        {
            UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
            avAlert.Show();
        }
    }
    ```

    이 메서드는 앱을 실행하는 동안 들어오는 알림을 처리합니다.

4. **AppDelegate** 클래스에서 다음 코드를 **FinishedLaunching** 메서드에 추가합니다.

    ```csharp
    // Register for push notifications.
    var settings = UIUserNotificationSettings.GetSettingsForTypes(
        UIUserNotificationType.Alert
        | UIUserNotificationType.Badge
        | UIUserNotificationType.Sound,
        new NSSet());

    UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications();
    ```

    이를 통해 원격 알림을 지원하고 푸시 등록을 요청합니다.

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

#### <a name="test-push-notifications-in-your-ios-app"></a>iOS 앱에서 푸시 알림 테스트

1. iOS 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 눌러 프로젝트를 빌드하고 iOS 디바이스에서 앱을 시작합니다. 그런 다음 **확인**을 클릭하여 푸시 알림을 허용하도록 합니다.

   > [!NOTE]
   > 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.
4. 알림이 수신되는지 확인한 다음 **확인**을 클릭하여 알림을 해제합니다.

## <a name="configure-and-run-windows-projects-optional"></a>Windows 프로젝트 구성 및 실행(선택 사항)

이 섹션에서는 Windows 디바이스용 Xamarin.Forms WinApp 및 WinPhone81 프로젝트를 실행합니다. 이 단계에서는 UWP(범용 Windows 플랫폼) 프로젝트도 지원합니다. Windows 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>WNS(Windows 알림 서비스)를 통한 푸시 알림에 Windows 앱 등록

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS에 대한 알림 허브 구성

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Windows 앱에 푸시 알림 추가

1. Visual Studio의 Windows 프로젝트에서 **App.xaml.cs**를 열고 다음 문을 추가합니다.

    ```csharp
    using Newtonsoft.Json.Linq;
    using Microsoft.WindowsAzure.MobileServices;
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    using <your_TodoItemManager_portable_class_namespace>;
    ```

    `<your_TodoItemManager_portable_class_namespace>`를 `TodoItemManager` 클래스가 들어 있는 이식 가능 프로젝트의 네임스페이스로 바꿉니다.

2. App.xaml.cs에서 다음 **InitNotificationsAsync** 메서드를 추가합니다.

    ```csharp
    private async Task InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        const string templateBodyWNS =
            "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        JObject headers = new JObject();
        headers["X-WNS-Type"] = "wns/toast";

        JObject templates = new JObject();
        templates["genericMessage"] = new JObject
        {
            {"body", templateBodyWNS},
            {"headers", headers} // Needed for WNS.
        };

        await TodoItemManager.DefaultManager.CurrentClient.GetPush()
            .RegisterAsync(channel.Uri, templates);
    }
    ```

    이 메서드는 푸시 알림 채널을 가져오고 알림 허브에서 템플릿 알림을 수신하기 위해 템플릿을 등록합니다. *messageParam*을 지원하는 템플릿 알림이 이 클라이언트에 전달됩니다.

3. App.xaml.cs에서 `async` 한정자를 추가하여 **OnLaunched** 이벤트 처리기 메서드 정의를 업데이트합니다. 그런 후 다음 코드 줄을 메서드의 끝에 추가합니다.

    ```csharp
    await InitNotificationsAsync();
    ```

    이렇게 하면 앱이 시작될 때마다 푸시 알림 등록이 생성되거나 새로 고쳐집니다. WNS 푸시 채널이 항상 활성화되도록 이 작업을 수행하는 것이 중요합니다.  

4. Visual Studio용 솔루션 탐색기에서 **Package.appxmanifest** 파일을 열고 **알림**에서 **알림 가능**을 **예**로 설정합니다.
5. 앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 Mobile Apps 백 엔드에서 템플릿 알림을 등록해야 합니다. 솔루션의 모든 Windows 프로젝트에 대해 이 섹션을 반복합니다.

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows 앱에서 푸시 알림 테스트

1. Visual Studio에서 Windows 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다.
3. 앱에서 새 todoitem에 대한 이름을 입력한 다음 더하기(**+**) 아이콘을 클릭하여 추가합니다.
4. 항목이 추가될 때 알림을 받았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

푸시 알림에 대해 자세히 알아봅니다.

* [Azure Mobile Apps에서 푸시 알림 보내기](https://developer.xamarin.com/guides/xamarin-forms/cloud-services/push-notifications/azure/)
* [Firebase Cloud Messaging](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/firebase-cloud-messaging/)
* [Firebase Cloud Messaging을 사용하여 원격 알림](https://developer.xamarin.com/guides/android/data-and-cloud-services/google-messaging/remote-notifications-with-fcm/)
* [푸시 알림 문제 진단](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  디바이스에서 알림이 삭제되거나 끝나지 않는 다양한 이유가 있습니다. 이 항목에서는 푸시 알림 실패의 근본 원인을 분석 및 파악하는 방법을 보여 줍니다.

다음 자습서 중 하나를 계속 진행할 수도 있습니다.

* [앱에 인증 추가](app-service-mobile-xamarin-forms-get-started-users.md)  
   ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: https://go.microsoft.com/fwlink/p/?LinkId=272333
