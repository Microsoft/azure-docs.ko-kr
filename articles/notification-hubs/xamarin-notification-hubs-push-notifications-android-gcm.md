---
title: Azure Notification Hubs를 사용하여 Xamarin.Android 앱에 푸시 알림 보내기 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs를 사용하여 Xamarin.Android 애플리케이션에 푸시 알림을 보내는 방법을 알아봅니다.
author: sethmanheim
manager: femila
editor: jwargo
services: notification-hubs
documentationcenter: xamarin
ms.assetid: 0be600fe-d5f3-43a5-9e5e-3135c9743e54
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 08/01/2019
ms.openlocfilehash: ce47d2ae65a4ea9e6878381ce58d62cb6c998599
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998206"
---
# <a name="tutorial-send-push-notifications-to-xamarinandroid-apps-using-notification-hubs"></a>자습서: Notification Hubs를 사용하여 Xamarin.Android 앱에 푸시 알림 보내기

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>개요

이 자습서에서는 Azure Notification Hubs를 사용하여 Xamarin.Android 애플리케이션에 푸시 알림을 보내는 방법을 보여 줍니다. FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다. 알림 허브를 사용하여 앱을 실행하는 모든 디바이스로 푸시 알림을 브로드캐스트합니다. 완성된 코드는 [NotificationHubs 앱](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/Xamarin/GetStartedXamarinAndroid) 샘플에서 사용할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Firebase 프로젝트 생성 및 Firebase Cloud Messaging 사용
> * 알림 허브 만들기
> * Xamarin.Android 앱을 생성한 후 알림 허브에 연결
> * Azure Portal에서 테스트 알림 전송

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Windows의 경우 [Xamarin이 포함된 Visual Studio] 또는 OS X의 경우 [Mac용 Visual Studio]
* 활성 Google 계정

## <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging"></a>Firebase 프로젝트 생성 및 Firebase Cloud Messaging 사용

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging-xamarin.md)]

## <a name="create-a-notification-hub"></a>알림 허브 만들기

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcmfcm-settings-for-the-notification-hub"></a>알림 허브에 대한 GCM/FCM 설정 구성

1. 왼쪽 메뉴의 **설정** 섹션에서 **Google(GCM/FCM)** 을 선택합니다.
2. Google Firebase 콘솔에서 적어둔 **서버 키**를 입력합니다.
3. 도구 모음에서 **저장**을 선택합니다.

    ![Google G C M F C M 옵션이 강조 표시되고 빨간색으로 표시된 Azure Portal의 Notification Hub 스크린샷.](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

알림 허브가 FCM과 작동하도록 구성되었으며, 알림을 받고 푸시 알림을 보내기 위해 앱을 등록하는 연결 문자열이 있습니다.

## <a name="create-a-xamarinandroid-app-and-connect-it-to-notification-hub"></a>Xamarin.Android 앱 만들기 및 알림 허브에 연결

### <a name="create-visual-studio-project-and-add-nuget-packages"></a>Visual Studio 프로젝트 생성 및 NuGet 패키지 설치

> [!NOTE]
> 이 자습서에 설명된 단계는 Visual Studio 2017에 대한 것입니다. 

1. Visual Studio에서 **파일** 메뉴를 열고 **새로 만들기**를 선택한 다음, **프로젝트**를 선택합니다. **새 프로젝트** 창에서 다음 단계를 수행합니다.
    1. **설치됨**, **Visual C#** 을 확장한 후 **Android**를 클릭합니다.
    2. 목록에서 **Android 앱(Xamarin)** 을 선택합니다.
    3. 프로젝트의 **이름** 을 입력합니다.
    4. 프로젝트의 **위치** 를 선택합니다.
    5. **확인**을 선택합니다.

        ![새 프로젝트 대화 상자](./media/partner-xamarin-notification-hubs-android-get-started/new-project-dialog-new.png)
2. **새 Android 앱** 대화 상자에서 **빈 앱**을 선택하고 **확인**을 선택합니다.

    ![새 프로젝트 대화 상자](./media/partner-xamarin-notification-hubs-android-get-started/new-android-app-dialog.png)
3. **솔루션 탐색기** 창에서 **속성**을 확장하고 **AndroidManifest.xml**을 클릭합니다. Google Firebase 콘솔에서 Firebase Cloud Messaging을 프로젝트에 추가할 때 입력한 패키지 이름과 일치하도록 패키지 이름을 업데이트합니다.

    ![GCM의 패키지 이름](./media/partner-xamarin-notification-hubs-android-get-started/package-name-gcm.png)
4. 다음 단계를 수행하여 프로젝트에 대한 대상 Android 버전을 **Android 9.0(원형)** 으로 설정합니다. 
    1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 
    1. **Android 버전을 사용하여 컴파일: (대상 프레임워크)** 필드의 경우 **Android 9.0(원형)** 을 선택합니다. 
    1. 대상 프레임워크 변경을 계속하려면 메시지 상자에서 **예**를 선택합니다.
1. 다음 단계를 수행하여 필요한 NuGet 패키지를 프로젝트에 추가합니다.
    1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다.
    1. **설치됨** 탭으로 전환하고, **Xamarin.Android.Support.Design**을 선택하고, 오른쪽 창에서 **업데이트**를 선택하여 패키지를 최신 버전으로 업데이트합니다.
    1. **찾아보기** 탭으로 전환합니다. **Xamarin.GooglePlayServices.Base**를 검색합니다. 결과 목록에서 **Xamarin.GooglePlayServices.Base**를 선택합니다. 그런 후 **설치**를 선택합니다.

        ![Google Play 서비스 NuGet](./media/partner-xamarin-notification-hubs-android-get-started/google-play-services-nuget.png)
    6. **NuGet 패키지 관리자** 창에서 **Xamarin.Firebase.Messaging**을 검색합니다. 결과 목록에서 **Xamarin.Firebase.Messaging**을 선택합니다. 그런 후 **설치**를 선택합니다.
    7. 이제 **Xamarin.Azure.NotificationHubs.Android**를 검색합니다. 결과 목록에서 **Xamarin.Azure.NotificationHubs.Android**를 선택합니다. 그런 후 **설치**를 선택합니다.

### <a name="add-the-google-services-json-file"></a>Google Services JSON 파일 추가

1. Google Firebase 콘솔에서 다운로드한 `google-services.json`을 프로젝트 폴더로 복사합니다.
2. 프로젝트에 `google-services.json`을 추가합니다.
3. **솔루션 탐색기** 창에서 `google-services.json`을 선택합니다.
4. **속성** 창에서 빌드 작업을 **GoogleServicesJson**으로 설정합니다. **GoogleServicesJson**이 표시되지 않으면 Visual Studio를 닫았다가 다시 시작한 후 프로젝트를 다시 열고 다시 시도하세요.

    ![GoogleServicesJson 빌드 작업](./media/partner-xamarin-notification-hubs-android-get-started/google-services-json-build-action.png)

### <a name="set-up-notification-hubs-in-your-project"></a>프로젝트에서 알림 허브 설정

#### <a name="registering-with-firebase-cloud-messaging"></a>Firebase Cloud Messaging 등록

1. `AndroidManifest.xml` 파일을 열고 다음 `<receiver>` 요소를 `<application>` 요소로 삽입합니다.

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

2. **애플리케이션 요소 앞**에 다음 명령문을 추가합니다.

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    <uses-permission android:name="android.permission.WAKE_LOCK" />
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    ```

3. Android 앱 및 알림 허브에 대해 다음 정보를 수집합니다.

   * **연결 문자열 수신 대기**: [Azure Portal]의 대시보드에서 **연결 문자열 보기**를 선택합니다. 이 값에 대한 `DefaultListenSharedAccessSignature` 연결 문자열을 복사합니다.
   * **허브 이름**: [Azure Portal]의 허브 이름입니다. 예를 들어 *mynotificationhub2*입니다.
4. **솔루션 탐색기** 창에서 **프로젝트**를 마우스 오른쪽 단추로 클릭하고 **추가**를 선택한 다음, **클래스**를 선택합니다.
5. Xamarin 프로젝트에 대해 `Constants.cs` 클래스를 만들고 클래스에 다음 상수 값을 정의합니다. 자리 표시자는 해당 값으로 바꿉니다.

    ```csharp
    public static class Constants
    {
        public const string ListenConnectionString = "<Listen connection string>";
        public const string NotificationHubName = "<hub name>";
    }
    ```

6. 명령문을 사용하여 다음 항목을 `MainActivity.cs`에 추가합니다.

    ```csharp
    using Android.Util;
    using Android.Gms.Common;
    ```

7. 다음 속성을 MainActivity 클래스에 추가합니다. 앱 실행 중에 경고 대화 상자를 표시하는 데 사용할 TAG 변수입니다.

    ```csharp
    public const string TAG = "MainActivity";
    internal static readonly string CHANNEL_ID = "my_notification_channel";
    ```

8. 다음 메서드를 MainActivity 클래스에 추가합니다. 디바이스에서 **Google Play 서비스**를 사용할 수 있는지 확인합니다.

    ```csharp
    public bool IsPlayServicesAvailable()
    {
        int resultCode = GoogleApiAvailability.Instance.IsGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.Success)
        {
            if (GoogleApiAvailability.Instance.IsUserResolvableError(resultCode))
                Log.Debug(TAG, GoogleApiAvailability.Instance.GetErrorString(resultCode));
            else
            {
                Log.Debug(TAG, "This device is not supported");
                Finish();
            }
            return false;
        }

        Log.Debug(TAG, "Google Play Services is available.");
        return true;
    }
    ```

9. 알림 채널을 만드는 MainActivity 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private void CreateNotificationChannel()
    {
        if (Build.VERSION.SdkInt < BuildVersionCodes.O)
        {
            // Notification channels are new in API 26 (and not a part of the
            // support library). There is no need to create a notification
            // channel on older versions of Android.
            return;
        }

        var channelName = CHANNEL_ID;
        var channelDescription = string.Empty;
        var channel = new NotificationChannel(CHANNEL_ID, channelName, NotificationImportance.Default)
        {
            Description = channelDescription
        };

        var notificationManager = (NotificationManager)GetSystemService(NotificationService);
        notificationManager.CreateNotificationChannel(channel);
    }
    ```

10. `MainActivity.cs`에서 다음 코드를 `base.OnCreate(savedInstanceState)` 다음에 있는 `OnCreate`에 추가합니다.

    ```csharp
    if (Intent.Extras != null)
    {
        foreach (var key in Intent.Extras.KeySet())
        {
            if(key!=null)
            {
                var value = Intent.Extras.GetString(key);
                Log.Debug(TAG, "Key: {0} Value: {1}", key, value);
            }
        }
    }

    IsPlayServicesAvailable();
    CreateNotificationChannel();
    ```

15. `MyFirebaseMessagingService`라는 클래스를 프로젝트에 추가합니다. 
16. 명령문을 사용하여 다음 항목을 `MyFirebaseMessagingService.cs`에 추가합니다.

    ```csharp
    using Android.Util;
    using Firebase.Messaging;
    using Android.Support.V4.App;    
    using WindowsAzure.Messaging;
    ```

17. 클래스 선언 위에 다음을 추가하고, 클래스가 `FirebaseMessagingService`에서 상속되도록 합니다.

    ```csharp
    [Service]
    [IntentFilter(new[] { "com.google.firebase.MESSAGING_EVENT" })]
    [IntentFilter(new[] { "com.google.firebase.INSTANCE_ID_EVENT" })]
    public class MyFirebaseMessagingService : FirebaseMessagingService
    ```

18. `MyFirebaseMessagingService` 클래스 내에 다음 코드를 추가하여 수신된 메시지를 처리합니다. 

    ```csharp
        const string TAG = "MyFirebaseMsgService";
        NotificationHub hub;
    
        public override void OnMessageReceived(RemoteMessage message)
        {
            Log.Debug(TAG, "From: " + message.From);
            if (message.GetNotification() != null)
            {
                //These is how most messages will be received
                Log.Debug(TAG, "Notification Message Body: " + message.GetNotification().Body);
                SendNotification(message.GetNotification().Body);
            }
            else
            {
                //Only used for debugging payloads sent from the Azure portal
                SendNotification(message.Data.Values.First());
    
            }
        }
    
        void SendNotification(string messageBody)
        {
            var intent = new Intent(this, typeof(MainActivity));
            intent.AddFlags(ActivityFlags.ClearTop);
            var pendingIntent = PendingIntent.GetActivity(this, 0, intent, PendingIntentFlags.OneShot);
    
            var notificationBuilder = new NotificationCompat.Builder(this, MainActivity.CHANNEL_ID);
    
            notificationBuilder.SetContentTitle("FCM Message")
                        .SetSmallIcon(Resource.Drawable.ic_launcher)
                        .SetContentText(messageBody)
                        .SetAutoCancel(true)
                        .SetShowWhen(false)
                        .SetContentIntent(pendingIntent);
    
            var notificationManager = NotificationManager.FromContext(this);
    
            notificationManager.Notify(0, notificationBuilder.Build());
        }
    ```

19. 다음 메서드를MyFirebaseMessagingService 클래스(이전 단계에서 추가된 코드 바로 아래)에 추가하여 FCM 등록 토큰을 받고 Notification Hubs 인스턴스(허브)로 보냅니다. 

    ```csharp
        public override void OnNewToken(string token)
        {
            Log.Debug(TAG, "FCM token: " + token);
            SendRegistrationToServer(token);
        }

        void SendRegistrationToServer(string token)
        {
            // Register with Notification Hubs
            hub = new NotificationHub(Constants.NotificationHubName,
                                        Constants.ListenConnectionString, this);

            var tags = new List<string>() { };
            var regID = hub.Register(token, tags.ToArray()).RegistrationId;

            Log.Debug(TAG, $"Successful registration of ID {regID}");
        }
    ```
1. 프로젝트를 **빌드**합니다.
1. 디바이스 또는 로드된 에뮬레이터에서 앱을 **실행**합니다.

## <a name="send-test-notification-from-the-azure-portal"></a>Azure Portal에서 테스트 알림 전송

[Azure Portal]에서 **테스트 보내기** 옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림이 디바이스로 전송됩니다.

![Azure Portal - 보내기 테스트](media/partner-xamarin-notification-hubs-android-get-started/send-test-notification.png)

푸시 알림은 일반적으로 Mobile Services 또는 ASP.NET과 같은 백 엔드 서비스에서 호환되는 라이브러리를 통해 보내집니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 백 엔드에 등록된 모든 Android 디바이스로 브로드캐스트 알림을 보냈습니다. 특정 Android 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
>[특정 디바이스에 알림 푸시](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png
[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png
[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png
[24]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-xamarin-android-app-options.png
[25]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-google-services-json.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-test-send.png

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Xamarin이 포함된 Visual Studio]: /visualstudio/install/install-visual-studio
[Mac용 Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/
[Azure Portal]: https://portal.azure.com/
[wns object]: https://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Android]: /previous-versions/azure/dn282661(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[GitHub]: https://github.com/Azure/azure-notificationhubs-android
