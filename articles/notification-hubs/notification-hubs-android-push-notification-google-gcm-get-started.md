---
title: Azure Notification Hubs 및 Google Cloud Messaging을 사용하여 Android로 푸시 알림 보내기 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs 및 Google Firebase Cloud Messaging을 사용하여 Android 디바이스로 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: android
keywords: 푸시 알림,푸시알림,android 푸시 알림
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc, devx-track-java
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 8c97710202a448c613ab685932cb335bbaed4953
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832657"
---
# <a name="tutorial-send-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-cloud-messaging-deprecated"></a>자습서: Azure Notification Hubs 및 Google Cloud Messaging을 사용하여 Android 디바이스에 푸시 알림 보내기(더 이상 사용되지 않음)

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

> [!WARNING]
> 2018년 4월 10일 기준으로 Google은 GCM(Google Cloud Messaging)을 더 이상 지원하지 않습니다. GCM 서버 및 클라이언트 API는 더 이상 사용되지 않으며 2019년 5월 29일에 제거될 예정입니다. 자세한 내용은 [GCM 및 FCM 질문과 대답](https://developers.google.com/cloud-messaging/faq)을 참조하세요.

## <a name="overview"></a>개요

이 자습서에서는 Azure Notification Hubs를 사용하여 Android 애플리케이션에 푸시 알림을 보내는 방법을 보여 줍니다.
GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.

> [!IMPORTANT]
> GCM(Google Cloud Messaging)은 더 이상 사용되지 않으며 [곧](https://developers.google.com/cloud-messaging/faq) 제거될 예정입니다.

> [!IMPORTANT]
> 이 항목에서는 GCM(Google Cloud Messaging)을 사용한 푸시 알림을 보여 줍니다. Google의 FCM(Firebase Cloud Messaging)을 사용하는 경우 [Azure Notification Hubs 및 FCM을 사용하여 Android에 푸시 알림 보내기](notification-hubs-android-push-notification-google-fcm-get-started.md)를 참조하세요.

이 자습서에 대해 완료된 코드는 GitHub의 [여기](https://github.com/Azure/azure-notificationhubs-android/tree/master/FCMTutorialApp)서 다운로드할 수 있습니다.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * Google Cloud Messaging을 지원하는 프로젝트 만들기
> * 알림 허브 만들기
> * 알림 허브에 앱 연결
> * 앱 테스트

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정을 만듭니다](https://azure.microsoft.com/free/).
* [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797).

## <a name="creating-a-project-that-supports-google-cloud-messaging"></a>Google Cloud Messaging을 지원하는 프로젝트 만들기

[!INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

## <a name="create-a-notification-hub"></a>알림 허브 만들기

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-gcm-setting-for-the-notification-hub"></a>알림 허브에 대한 GCM 설정 구성

1. **알림 설정**에서 **Google(GCM)** 을 선택합니다.
2. Google Cloud Console에서 얻은 **API 키**를 입력합니다.
3. 도구 모음에서 **저장**을 선택합니다.

    ![Azure Notification Hubs - Google(GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

이제 알림 허브가 GCM과 작동하도록 구성되었으며, 푸시 알림을 받고 보내도록 앱을 등록하기 위한 연결 문자열이 있습니다.

## <a name="connect-your-app-to-the-notification-hub"></a><a id="connecting-app"></a>알림 허브에 앱 연결

### <a name="create-a-new-android-project"></a>새 Android 프로젝트 만들기

1. Android Studio에서 새 Android Studio 프로젝트를 시작합니다.

   ![Android Studio - 새 프로젝트][13]
2. **휴대폰 및 태블릿** 폼 팩터와 지원할 **최소 SDK**를 선택합니다. 그런 후 **Next** 를 클릭합니다.

   ![Android Studio - 프로젝트 만들기 워크플로][14]
3. **빈 활동**을 기본 활동으로 선택하고 **다음**, **마침**을 차례로 클릭합니다.

### <a name="add-google-play-services-to-the-project"></a>프로젝트에 Google Play Services 추가

[!INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

### <a name="adding-azure-notification-hubs-libraries"></a>Azure Notification Hubs 라이브러리 추가

1. **앱**의 `Build.Gradle` 파일에서 **종속성** 섹션에 다음 줄을 추가합니다.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'
    ```

2. **종속성** 섹션 뒤에 다음 리포지토리를 추가합니다.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="updating-the-projects-androidmanifestxml"></a>프로젝트의 AndroidManifest.xml 업데이트

1. GCM을 지원하기 위해, 코드에 [Google 인스턴스 ID API](https://developers.google.com/instance-id/)를 사용하여 [등록 토큰 가져오기](https://developers.google.com/cloud-messaging/)에 사용되는 인스턴스 ID 수신기 서비스를 구현합니다. 이 자습서에서 클래스 이름은 `MyInstanceIDService`입니다.

    AndroidManifest.xml 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다. `<your package>` 자리 표시자를 `AndroidManifest.xml` 파일의 맨 위에 표시된 실제 패키지 이름으로 바꿉니다.
  
    ```xml
    <service android:name="<your package>.MyInstanceIDService" android:exported="false">
        <intent-filter>
            <action android:name="com.google.android.gms.iid.InstanceID"/>
        </intent-filter>
    </service>
    ```

2. 애플리케이션이 인스턴스 ID API에서 GCM 등록 토큰을 수신하면 토큰을 사용하여 [Azure Notification Hub에 등록](notification-hubs-push-notification-registration-management.md)합니다. 등록은 `RegistrationIntentService`라는 `IntentService`를 사용하여 백그라운드에서 수행됩니다. 이 서비스는 [GCM 등록 토큰을 새로 고칩니다](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    AndroidManifest.xml 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다. `<your package>` 자리 표시자를 `AndroidManifest.xml` 파일의 맨 위에 표시된 실제 패키지 이름으로 바꿉니다.

    ```xml
    <service
        android:name="<your package>.RegistrationIntentService"
        android:exported="false">
    </service>
    ```

3. 알림을 수신할 수신기를 정의합니다. 다음 수신기 정의를 `<application>` 태그 안의 AndroidManifest.xml 파일에 추가합니다. `<your package>` 자리 표시자를 `AndroidManifest.xml` 파일의 맨 위에 표시된 실제 패키지 이름으로 바꿉니다.

    ```xml
    <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
        android:permission="com.google.android.c2dm.permission.SEND">
        <intent-filter>
            <action android:name="com.google.android.c2dm.intent.RECEIVE" />
            <category android:name="<your package name>" />
        </intent-filter>
    </receiver>
    ```

4. `<application>` 태그 아래에 다음 필수 GCM 권한을 추가합니다. `<your package>`를 `AndroidManifest.xml` 파일 맨 위에 있는 패키지 이름으로 바꿉니다.

    이러한 권한에 대한 자세한 내용은 [Android용 GCM 클라이언트 앱 설정](https://developers.google.com/cloud-messaging/)을 참조하세요.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

    <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
    <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>
    ```

### <a name="adding-code"></a>코드 추가

1. 프로젝트 뷰에서 **앱** > **src** > **기본** > **java**를 확장합니다. **java** 아래의 패키지 폴더를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**, **Java 클래스**를 차례로 클릭합니다. `NotificationSettings`(이)라는 새 클래스를 추가합니다.

    ![Android Studio - 새 Java 프로젝트][6]

    `NotificationSettings` 클래스에 대한 다음 코드에서 세 개의 자리 표시자를 업데이트합니다.

   * `SenderId`: 이전에 [Google 클라우드 콘솔](https://cloud.google.com/console)에서 얻은 프로젝트 번호입니다.
   * `HubListenConnectionString`: 허브의 **DefaultListenAccessSignature** 연결 문자열입니다. [Azure Portal]에 있는 허브의 **설정** 페이지에서 **액세스 정책**을 클릭하여 이 연결 문자열을 복사할 수 있습니다.
   * `HubName`: [Azure Portal]의 허브 페이지에 표시되는 알림 허브 이름을 사용합니다.

     `NotificationSettings` 코드:

     ```java
     public class NotificationSettings {
        public static String SenderId = "<Your project number>";
        public static String HubName = "<Your HubName>";
        public static String HubListenConnectionString = "<Your default listen connection string>";
     }
     ```

2. `MyInstanceIDService`라는 또 다른 새 클래스를 추가합니다. 이 클래스는 인스턴스 ID 수신기 서비스 구현입니다.

    이 클래스에 대한 코드는 `IntentService`를 호출하여 백그라운드에서 [FCM 토큰을 새로 고칩니다](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

    ```java
    import android.content.Intent;
    import android.util.Log;
    import com.google.android.gms.iid.InstanceIDListenerService;

    public class MyInstanceIDService extends InstanceIDListenerService {

        private static final String TAG = "MyInstanceIDService";

        @Override
        public void onTokenRefresh() {

            Log.i(TAG, "Refreshing GCM Registration Token");

            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    };
    ```

3. `RegistrationIntentService`라는 프로젝트에 다른 새 클래스를 추가합니다. 이 클래스는 [FCM 토큰 새로 고침](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) 및 [알림 허브 등록](notification-hubs-push-notification-registration-management.md)을 처리하는 `IntentService`를 구현합니다.

    이 클래스에 대해 다음 코드를 사용합니다.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;

    import com.google.android.gms.gcm.GoogleCloudMessaging;
    import com.google.android.gms.iid.InstanceID;
    import com.microsoft.windowsazure.messaging.NotificationHub;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";

        private NotificationHub hub;

        public RegistrationIntentService() {
            super(TAG);
        }

        @Override
        protected void onHandleIntent(Intent intent) {
            SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
            String resultString = null;
            String regID = null;

            try {
                InstanceID instanceID = InstanceID.getInstance(this);
                String token = instanceID.getToken(NotificationSettings.SenderId,
                        GoogleCloudMessaging.INSTANCE_ID_SCOPE);
                Log.i(TAG, "Got GCM Registration Token: " + token);

                // Storing the registration id that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server,
                // otherwise your server should have already received the token.
                if ((regID=sharedPreferences.getString("registrationID", null)) == null) {
                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.i(TAG, "Attempting to register with NH using token : " + token);

                    regID = hub.register(token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                    resultString = "Registered Successfully - RegId : " + regID;
                    Log.i(TAG, resultString);
                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                } else {
                    resultString = "Previously Registered Successfully - RegId : " + regID;
                }
            } catch (Exception e) {
                Log.e(TAG, resultString="Failed to complete token refresh", e);
                // If an exception happens while fetching the new token or updating the registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

4. `MainActivity` 클래스에서 클래스 시작 부분에 다음 `import` 문을 추가합니다.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import com.google.android.gms.gcm.*;
    import com.microsoft.windowsazure.notifications.NotificationsManager;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    import android.content.Intent;
    ```

5. 클래스의 맨 위에 다음과 같은 프라이빗 멤버를 추가합니다. 이 코드는 [Google 권장 사항에 따라 Google Play Services의 가용성을 확인](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)합니다.

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private GoogleCloudMessaging gcm;
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    private static final String TAG = "MainActivity";
    ```

6. `MainActivity` 클래스에서 Google Play Services 가용성에 다음 메서드를 추가합니다.

    ```java
    /**
        * Check the device to make sure it has the Google Play Services APK. If
        * it doesn't, display a dialog that allows users to download the APK from
        * the Google Play Store or enable it in the device's system settings.
        */
    private boolean checkPlayServices() {
        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
        if (resultCode != ConnectionResult.SUCCESS) {
            if (apiAvailability.isUserResolvableError(resultCode)) {
                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                        .show();
            } else {
                Log.i(TAG, "This device is not supported by Google Play Services.");
                ToastNotify("This device is not supported by Google Play Services.");
                finish();
            }
            return false;
        }
        return true;
    }
    ```

7. `MainActivity` 클래스에서 `IntentService`를 호출하기 전에 Google Play 서비스를 확인하는 다음 코드를 추가하여 FCM 등록 토큰을 가져오고 알림 허브에 등록합니다.

    ```java
    public void registerWithNotificationHubs()
    {
        Log.i(TAG, " Registering with Notification Hubs");

        if (checkPlayServices()) {
            // Start IntentService to register this application with GCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

8. `MainActivity` 클래스의 `OnCreate` 메서드에서 활동이 생성되면 등록 프로세스를 시작하는 다음 코드를 추가합니다.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
        registerWithNotificationHubs();
    }
    ```

9. `MainActivity` 에 이러한 추가 메서드를 추가하여 앱 상태를 확인하고 앱에서 상태를 보고합니다.

    ```java
    @Override
    protected void onStart() {
        super.onStart();
        isVisible = true;
    }

    @Override
    protected void onPause() {
        super.onPause();
        isVisible = false;
    }

    @Override
    protected void onResume() {
        super.onResume();
        isVisible = true;
    }

    @Override
    protected void onStop() {
        super.onStop();
        isVisible = false;
    }

    public void ToastNotify(final String notificationMessage) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                TextView helloText = (TextView) findViewById(R.id.text_hello);
                helloText.setText(notificationMessage);
            }
        });
    }
    ```

10. `ToastNotify` 메서드는 *"Hello World"* `TextView` 컨트롤을 사용하여 앱에서 영구적으로 상태 및 알림을 보고합니다. activity_main.xml 레이아웃에서 해당 컨트롤에 대한 다음 ID를 추가합니다.

    ```xml
    android:id="@+id/text_hello"
    ```

11. AndroidManifest.xml에서 정의한 수신기에 대한 하위 클래스를 추가합니다. `MyHandler`라는 프로젝트에 또 다른 새 클래스를 추가합니다.

12. 그런 다음 `MyHandler.java`의 맨 위에 다음 import 문을 추가합니다.

    ```java
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.os.Bundle;
    import android.support.v4.app.NotificationCompat;
    import com.microsoft.windowsazure.notifications.NotificationsHandler;
    import android.net.Uri;
    import android.media.RingtoneManager;
    ```

13. `MyHandler` 클래스에 다음 코드를 추가하여 `com.microsoft.windowsazure.notifications.NotificationsHandler`의 하위 클래스로 만듭니다.

    이 코드는 `OnReceive` 메서드를 재정의하여 처리기에서 받은 알림을 보고합니다. 또한 처리기는 `sendNotification()` 메서드를 사용하여 Android 알림 관리자에 푸시 알림을 보냅니다. `sendNotification()` 메서드는 앱이 실행되지 않을 때, 알림이 수신될 때 실행해야 합니다.

    ```java
    public class MyHandler extends NotificationsHandler {
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

        @Override
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String nhMessage = bundle.getString("message");
            sendNotification(nhMessage);
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
        }

        private void sendNotification(String msg) {

            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);

            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder mBuilder =
                    new NotificationCompat.Builder(ctx)
                            .setSmallIcon(R.mipmap.ic_launcher)
                            .setContentTitle("Notification Hub Demo")
                            .setStyle(new NotificationCompat.BigTextStyle()
                                    .bigText(msg))
                            .setSound(defaultSoundUri)
                            .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }
    }
    ```

14. Android Studio의 메뉴 모음에서 **빌드** > **프로젝트 다시 빌드**를 클릭하여 코드에 오류가 없는지 확인합니다.

## <a name="testing-your-app"></a>앱 테스트

### <a name="run-the-mobile-application"></a>모바일 애플리케이션 실행

1. 앱을 실행하고 등록에 성공한 경우 등록 ID가 보고되는지 확인합니다.

      ![Android에서 테스트 - 채널 등록][18]
2. 허브에 등록된 모든 Android 디바이스로 보낼 알림 메시지를 입력합니다.

      ![Android에서 테스트 - 메시지 보내기][19]

3. **알림 보내기**를 누릅니다. 앱을 실행 중인 모든 디바이스에는 푸시 알림 메시지가 있는 `AlertDialog` 인스턴스가 표시됩니다. 앱이 실행되지는 않지만 이전에 푸시 알림이 등록된 디바이스는 Android 알림 관리자에서 알림을 받습니다. 왼쪽 위 모서리에서 아래로 살짝 밀어 알림 메시지를 볼 수 있습니다.

      ![Android에서 테스트 - 알림][21]

### <a name="test-send-push-notifications-from-the-azure-portal"></a>Azure Portal에서 푸시 알림 보내기 테스트

[Azure Portal]을 통해 푸시 알림을 전송하여 앱에서 푸시 알림 받기를 테스트할 수 있습니다.

1. **문제 해결** 섹션에서 **테스트 보내기**를 선택합니다.
2. **플랫폼**에 대해 **Android**를 선택합니다.
3. **보내기**를 선택하여 테스트 알림을 보냅니다.
4. Android 디바이스에서 알림 메시지가 표시되는지 확인합니다.

    ![Azure Notification Hubs - 전송 테스트](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

#### <a name="push-notifications-in-the-emulator"></a>에뮬레이터의 푸시 알림

에뮬레이터 내부에서 푸시 알림을 테스트하려는 경우 에뮬레이터 이미지가 앱에 대해 선택한 Google API 수준을 지원하는지 확인합니다. 이미지에서 네이티브 Google API를 지원하지 않으면 **SERVICE\_NOT\_AVAILABLE** 예외가 발생합니다.

또한 **설정** > **계정**에서 실행 중인 에뮬레이터에 Google 계정을 추가했는지 확인합니다. 그렇지 않으면 GCM 등록 시 **AUTHENTICATION\_FAILED** 예외가 발생할 수 있습니다.

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(선택 사항) 앱에서 바로 푸시 알림 보내기

일반적으로, 백 엔드 서버를 사용하여 알림을 보냅니다. 경우에 따라, 클라이언트 애플리케이션에서 직접 푸시 알림을 보낼 수 있기를 원하기도 합니다. 이 섹션은 [Azure 알림 허브 REST API](/previous-versions/azure/reference/dn223264(v=azure.100))를 사용하여 클라이언트에서 알림을 보내는 방법을 설명합니다.

1. Android Studio 프로젝트 뷰에서 **앱** > **src** > **기본** > **자원** > **레이아웃**을 확장합니다. `activity_main.xml` 레이아웃 파일을 열고 **텍스트** 탭을 클릭하여 파일의 텍스트 내용을 업데이트합니다. 아래 코드로 업데이트하여 알림 허브에 푸시 알림 메시지를 보내는 새 `Button` 및 `EditText` 컨트롤을 추가합니다. 이 코드를 맨 아래의 `</RelativeLayout>`바로 앞에 추가합니다.

    ```xml
    <Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/send_button"
    android:id="@+id/sendbutton"
    android:layout_centerVertical="true"
    android:layout_centerHorizontal="true"
    android:onClick="sendNotificationButtonOnClick" />

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:id="@+id/editTextNotificationMessage"
    android:layout_above="@+id/sendbutton"
    android:layout_centerHorizontal="true"
    android:layout_marginBottom="42dp"
    android:hint="@string/notification_message_hint" />
    ```

2. Android Studio 프로젝트 뷰에서 **앱** > **src** > **기본** > **자원** > **값**을 확장합니다. `strings.xml` 파일을 열고 새 `Button` 및 `EditText` 컨트롤에서 참조하는 문자열 값을 추가합니다. 파일 맨 아래에서 `</resources>` 바로 앞에 다음 줄을 추가합니다.

    ```xml
    <string name="send_button">Send Notification</string>
    <string name="notification_message_hint">Enter notification message text</string>
    ```

3. `NotificationSetting.java` 파일에서 `NotificationSettings` 클래스에 다음 설정을 추가합니다.

    허브에 **DefaultFullSharedAccessSignature** 연결 문자열을 사용하여 `HubFullAccess`을 업데이트합니다. 알림 허브에 대한 **설정** 페이지에서 **액세스 정책**을 클릭하여 [Azure Portal]에서 이 연결 문자열을 복사할 수 있습니다.

    ```java
    public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";
    ```

4. `MainActivity.java` 파일에서 시작 부분에 다음 `import` 문을 추가합니다.

    ```java
    import java.io.BufferedOutputStream;
    import java.io.BufferedReader;
    import java.io.InputStreamReader;
    import java.io.OutputStream;
    import java.net.HttpURLConnection;
    import java.net.URL;
    import java.net.URLEncoder;
    import javax.crypto.Mac;
    import javax.crypto.spec.SecretKeySpec;
    import android.util.Base64;
    import android.view.View;
    import android.widget.EditText;
    ```

5. `MainActivity.java` 파일에서 다음 멤버를 `MainActivity` 클래스 위에 추가합니다.

    ```java
    private String HubEndpoint = null;
    private String HubSasKeyName = null;
    private String HubSasKeyValue = null;
    ```

6. 알림 허브로 메시지를 보낼 POST 요청을 인증하기 위해 SaS(공유 액세스 서명) 토큰을 만듭니다. 연결 문자열에서 키 데이터를 구문 분석한 다음, [일반적인 개념](/previous-versions/azure/reference/dn495627(v=azure.100)) REST API 참조에 설명된 대로 SaS 토큰을 만듭니다. 다음 코드는 구현 예제입니다.

    `MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 연결 문자열의 구문을 분석합니다.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx
        * to parse the connection string so a SaS authentication token can be
        * constructed.
        *
        * @param connectionString This must be the DefaultFullSharedAccess connection
        *                         string for this example.
        */
    private void ParseConnectionString(String connectionString)
    {
        String[] parts = connectionString.split(";");
        if (parts.length != 3)
            throw new RuntimeException("Error parsing connection string: "
                    + connectionString);

        for (int i = 0; i < parts.length; i++) {
            if (parts[i].startsWith("Endpoint")) {
                this.HubEndpoint = "https" + parts[i].substring(11);
            } else if (parts[i].startsWith("SharedAccessKeyName")) {
                this.HubSasKeyName = parts[i].substring(20);
            } else if (parts[i].startsWith("SharedAccessKey")) {
                this.HubSasKeyValue = parts[i].substring(16);
            }
        }
    }
    ```

7. `MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 SaS 인증 토큰을 만듭니다.

    ```java
    /**
        * Example code from https://msdn.microsoft.com/library/azure/dn495627.aspx to
        * construct a SaS token from the access key to authenticate a request.
        *
        * @param uri The unencoded resource URI string for this operation. The resource
        *            URI is the full URI of the Service Bus resource to which access is
        *            claimed. For example,
        *            "http://<namespace>.servicebus.windows.net/<hubName>"
        */
    private String generateSasToken(String uri) {

        String targetUri;
        String token = null;
        try {
            targetUri = URLEncoder
                    .encode(uri.toString().toLowerCase(), "UTF-8")
                    .toLowerCase();

            long expiresOnDate = System.currentTimeMillis();
            int expiresInMins = 60; // 1 hour
            expiresOnDate += expiresInMins * 60 * 1000;
            long expires = expiresOnDate / 1000;
            String toSign = targetUri + "\n" + expires;

            // Get an hmac_sha1 key from the raw key bytes
            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

            // Get an hmac_sha1 Mac instance and initialize with the signing key
            Mac mac = Mac.getInstance("HmacSHA256");
            mac.init(signingKey);

            // Compute the hmac on input data bytes
            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            // Using android.util.Base64 for Android Studio instead of
            // Apache commons codec
            String signature = URLEncoder.encode(
                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

            // Construct authorization string
            token = "SharedAccessSignature sr=" + targetUri + "&sig="
                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
        } catch (Exception e) {
            if (isVisible) {
                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
            }
        }

        return token;
    }
    ```

8. `MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 **알림 보내기** 단추 클릭을 처리하고 내장된 REST API를 사용하여 허브에 푸시 알림 메시지를 보냅니다.

    ```java
    /**
        * Send Notification button click handler. This method parses the
        * DefaultFullSharedAccess connection string and generates a SaS token. The
        * token is added to the Authorization header on the POST request to the
        * notification hub. The text in the editTextNotificationMessage control
        * is added as the JSON body for the request to add a GCM message to the hub.
        *
        * @param v
        */
    public void sendNotificationButtonOnClick(View v) {
        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
        final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";

        new Thread()
        {
            public void run()
            {
                try
                {
                    // Based on reference documentation...
                    // https://msdn.microsoft.com/library/azure/dn223273.aspx
                    ParseConnectionString(NotificationSettings.HubFullAccess);
                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                            "/messages/?api-version=2015-01");

                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();

                    try {
                        // POST request
                        urlConnection.setDoOutput(true);

                        // Authenticate the POST request with the SaS token
                        urlConnection.setRequestProperty("Authorization",
                            generateSasToken(url.toString()));

                        // Notification format should be GCM
                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");

                        // Include any tags
                        // Example below targets 3 specific tags
                        // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags",
                        //        "tag1 || tag2 || tag3");

                        // Send notification message
                        urlConnection.setFixedLengthStreamingMode(json.length());
                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                        bodyStream.write(json.getBytes());
                        bodyStream.close();

                        // Get response
                        urlConnection.connect();
                        int responseCode = urlConnection.getResponseCode();
                        if ((responseCode != 200) && (responseCode != 201)) {
                            BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                            String line;
                            StringBuilder builder = new StringBuilder("Send Notification returned " +
                                    responseCode + " : ")  ;
                            while ((line = br.readLine()) != null) {
                                builder.append(line);
                            }

                            ToastNotify(builder.toString());
                        }
                    } finally {
                        urlConnection.disconnect();
                    }
                }
                catch(Exception e)
                {
                    if (isVisible) {
                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                    }
                }
            }
        }.start();
    }
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 백 엔드에 등록된 모든 Android 디바이스로 브로드캐스트 알림을 보냈습니다. 특정 Android 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.  

 > [!div class="nextstepaction"]
 > [특정 디바이스에 알림 푸시](notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png
[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png
[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png
[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png
[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Azure Portal]: https://portal.azure.com
