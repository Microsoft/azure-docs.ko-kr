---
title: Azure Notification Hubs 및 Firebase SDK 버전 1.0.0-preview1을 사용하여 Android에 푸시 알림 보내기
description: 이 자습서에서는 Azure Notification Hubs 및 Google Firebase Cloud Messaging을 사용하여 Android 디바이스에 푸시 알림을 보내는 방법을 알아봅니다.
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d2dfaf7d83d3b2323812f637bddd91b9a2ea2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018232"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>자습서: Firebase SDK 버전 1.0.0-preview1을 사용하여 Android 디바이스에 푸시 알림 보내기

이 자습서에서는 Azure Notification Hubs 및 업데이트된 버전의 FCM(Firebase Cloud Messaging) SDK(버전 1.0.0-preview1)를 사용하여 Android 애플리케이션에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.

이 자습서의 완성된 코드는 [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh)에서 다운로드할 수 있습니다.

이 자습서에서 다루는 단계는 다음과 같습니다.

- Android Studio 프로젝트를 만듭니다.
- Firebase Cloud Messaging을 지원하는 Firebase 프로젝트를 만듭니다.
- 알림 허브를 만듭니다.
- 허브에 앱을 연결합니다.
- 앱을 테스트합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/free/)을 참조하세요.

또한 다음 항목이 필요합니다.

- 최신 버전의  [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)를 권장합니다.
- 최소 지원 버전은 API 레벨 16입니다.

## <a name="create-an-android-studio-project"></a>Android Studio 프로젝트 만들기

첫 번째 단계는 Android Studio에서 프로젝트를 만드는 것입니다.

1. Android Studio를 시작합니다.

2.  **File**을 선택한 다음,  **New**, **New Project**를 차례로 선택합니다.

3.  **Choose your project** 페이지에서  **Empty Activity**를 선택한 후  **Next**를 선택합니다.

4.  **Configure your project**  페이지에서 다음을 수행합니다.
   1. 애플리케이션의 이름을 입력합니다.
   2. 프로젝트 파일을 저장할 위치를 지정합니다.
   3.  **Finish**를 선택합니다.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="프로젝트 구성":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM을 지원하는 Firebase 프로젝트 만들기

1.  [Firebase 콘솔](https://firebase.google.com/console/)에 로그인합니다. 아직 없는 경우 새 Firebase 프로젝트를 만듭니다.

2. 프로젝트를 만든 후  **Android 앱에 Firebase 추가**를 선택합니다.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Firebase 추가":::

3.  **Android 앱에 Firebase 추가**  페이지에서 다음을 수행합니다.

   1.  **Android 패키지 이름**에서 애플리케이션의 **build.gradle** 파일에 있는 **applicationId** 값을 복사합니다. 이 예에서는  `com.fabrikam.fcmtutorial1app`입니다.

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="패키지 이름 지정":::

   2.  **앱 등록**을 선택합니다.

4.  **google-services.json 다운로드**를 선택하고 프로젝트의  **app** 폴더에 파일을 저장한 후  **다음**을 선택합니다.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Google 서비스 다운로드":::

5. Firebase 콘솔에서 프로젝트의 톱니바퀴를 선택합니다. 그런 후  **프로젝트 설정**을 선택합니다.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="프로젝트 설정":::

6. Android Studio 프로젝트의  **app** 폴더에 **google-services.json** 파일을 다운로드하지 않은 경우 이 페이지에서 다운로드할 수 있습니다.

7.  **클라우드 메시징** 탭으로 전환합니다.

8. 나중에 사용하기 위해  **서버 키**를 복사하고 저장합니다. 이 값을 사용하여 허브를 구성합니다.

## <a name="configure-a-notification-hub"></a>알림 허브 구성

1.  [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 메뉴에서  **모든 서비스**를 선택한 다음,  **모바일** 섹션에서  **Notification Hubs**를 선택합니다. 서비스 이름 옆의 별표 아이콘을 선택하여 서비스를 왼쪽 메뉴의  **즐겨찾기** 섹션에 추가합니다.  **Notification Hubs**를   **즐겨찾기**에 추가한 후 왼쪽 메뉴에서 선택합니다.

3.  **Notification Hubs** 페이지의 도구 모음에서 **추가**를 선택합니다.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="허브 추가":::

4.  **Notification Hubs** 페이지에서 다음을 수행합니다.

   1.  **알림 허브**에 이름을 입력합니다.

   2.  **새 네임스페이스 만들기**에 이름을 입력합니다. 네임스페이스에는 하나 이상의 허브가 포함되어 있습니다.

   3.  **위치** 드롭다운에서 값을 선택합니다. 이 값은 허브를 만들려는 위치를 지정합니다.

   4.  **리소스 그룹**에서 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.

   5.  **만들기**를 선택합니다.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="허브 만들기":::

5.  **알림**(벨 아이콘)을 선택하고,  **리소스로 이동**을 선택합니다.  **Notification Hubs** 페이지의 목록을 새로 고치고 허브를 선택할 수도 있습니다.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="허브 선택":::

6. 목록에서  **액세스 정책**을 선택합니다. 사용할 수 있는 두 가지 연결 문자열을 적어 둡니다. 나중에 푸시 알림을 처리하는 데 필요합니다.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="액세스 정책":::

   > [!IMPORTANT]
   > 애플리케이션에서  **DefaultFullSharedAccessSignature** 정책을 사용하지 마세요. 이 정책은 앱 백 엔드에서만 사용됩니다.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>허브에 대한 Firebase Cloud Messaging 설정 구성

1.  **설정** 아래의 왼쪽 창에서  **Google(GCM/FCM)** 을 선택합니다.

2. 앞에서 저장한 FCM 프로젝트의  **서버 키**를 입력합니다.

3. 도구 모음에서  **저장**을 선택합니다.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="서버 키":::

4. Azure Portal에서 허브가 업데이트되었다는 메시지를 표시합니다.  **저장** 단추를 사용할 수 없습니다.

이제 알림 허브가 Firebase Cloud Messaging과 함께 작동하도록 구성되었습니다. 디바이스에 알림을 보내고, 앱을 등록하여 알림을 수신하는 데 필요한 연결 문자열도 있습니다.

## <a name="connect-your-app-to-the-notification-hub"></a>알림 허브에 앱 연결

### <a name="add-google-play-services-to-the-project"></a>프로젝트에 Google Play Services 추가

1. Android Studio의 메뉴에서  **도구**를 선택하고,  **SDK Manager**를 선택합니다.

2. 프로젝트에서 사용되는 Android SDK의 대상 버전을 선택합니다. 그런 다음,  **패키지 세부 정보 표시**를 선택합니다.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="SDK 관리자":::

3.  **Google API**를 선택합니다(아직 설치되지 않은 경우).

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="API":::

4.  **SDK Tools** 탭으로 전환합니다. Google Play 서비스를 아직 설치하지 않은 경우 다음 이미지처럼  **Google Play 서비스**를 선택합니다. 그런 다음, **적용**을 선택하여 설치합니다. 이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Play 서비스":::

5.  **변경 확인** 대화 상자가 나타나면 **확인**을 선택합니다. 구성 요소 설치 관리자가 요청된 구성 요소를 설치합니다. 구성 요소가 설치되면  **완료**를 선택합니다.

6.  **확인**을 선택하여  **새 프로젝트 설정** 대화 상자를 닫습니다.

### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs 라이브러리 추가

1. 앱의 **build.gradle** 파일에서 dependencies 섹션에 다음 줄을 추가합니다.

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk:1.0.0-preview1@aar'
   implementation 'androidx.appcompat:appcompat:1.0.0'

   implementation 'com.google.firebase:firebase-messaging:20.1.5'

   implementation 'com.android.volley:volley:1.1.1'
   ```

2. dependencies 섹션 뒤에 다음 리포지토리를 추가합니다.

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Google Firebase 지원 추가

1. 파일 끝에 다음 플러그 인이 아직 없는 경우 추가합니다.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. 도구 모음에서  **지금 동기화**를 선택합니다.

### <a name="add-code"></a>코드 추가

1. Azure Notification Hubs에서 메시지 가로채기를 처리하는 **NotificationHubListener** 개체를 만듭니다.

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override

      public void onNotificationReceived(Context context, NotificationMessage message) {

      /* The following notification properties are available. */

      String title = message.getTitle();
      String message = message.getMessage();
      Map<String, String> data = message.getData();

      if (message != null) {
         Log.d(TAG, "Message Notification Title: " + title);
         Log.d(TAG, "Message Notification Body: " + message);
         }
      }
   }
   ```

2.  `MainActivity` 클래스의  `OnCreate` 메서드에서 활동이 생성되면 Notification Hubs 초기화 프로세스를 시작하는 다음 코드를 추가합니다.

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. Android Studio의 메뉴 모음에서  **빌드**, **프로젝트 다시 빌드**를 선택하여 코드에 오류가 없는지 확인합니다.  **ic_launcher** 아이콘에 대한 오류가 발생하는 경우 AndroidManifest.xml 파일에서 다음 명령문을 제거합니다.

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. 앱을 실행하기 위한 가상 디바이스가 있는지 확인합니다. 없으면 다음과 같이 추가합니다.

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="디바이스 관리자":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="가상 디바이스":::
   3. 선택한 디바이스에서 앱을 실행하고 허브를 사용하여 성공적으로 등록되는지 확인합니다.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="디바이스 등록":::

      > [!NOTE]
      > 인스턴스 ID 서비스의 `onTokenRefresh()` 메서드가 호출될 때까지 초기 시작 시 등록이 실패할 수 있습니다. 새로 고치면 알림 허브에 대한 성공적인 등록이 시작됩니다.

## <a name="send-a-test-notification"></a>테스트 알림 보내기

다음과 같이  [Azure Portal](https://portal.azure.com/)에서 알림 허브로 푸시 알림을 보낼 수 있습니다.

1. Azure Portal에서 허브에 대한 알림 허브 페이지의 **문제 해결** 섹션에서 **테스트 보내기**를 선택합니다.

2.  **플랫폼**에서  **Android**를 선택합니다.

3.  **보내기**를 선택합니다. Android 디바이스에서 알림이 아직 보이지 않으면 모바일 앱을 실행하지 않았기 때문입니다. 모바일 앱을 실행한 후  **보내기** 단추를 다시 선택하여 알림 메시지를 표시합니다.

4. 포털 페이지의 맨 아래에 있는 목록에 작업의 결과가 표시됩니다.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="테스트 알림 보내기":::

5. 디바이스에 알림 메시지가 표시됩니다.

푸시 알림은 일반적으로 호환 라이브러리를 사용하는 Mobile Apps 또는 ASP.NET과 같은 백 엔드 서비스에서 전송됩니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

## <a name="run-the-mobile-app-on-emulator"></a>에뮬레이터에서 모바일 앱 실행

에뮬레이터 내부에서 푸시 알림을 테스트하기 전에 에뮬레이터 이미지가 앱에 대해 선택한 Google API 수준을 지원하는지 확인합니다. 이미지에서 네이티브 Google API를 지원하지 않으면  **SERVICE_NOT_AVAILABLE** 예외가 발생할 수 있습니다.

또한 **설정** > **계정**에서 실행 중인 에뮬레이터에 Google 계정을 추가했는지 확인합니다. 그렇지 않으면 FCM 등록 시 **AUTHENTICATION_FAILED** 예외가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Firebase Cloud Messaging을 사용하여 서비스에 등록된 모든 Android 디바이스로 알림을 브로드캐스트했습니다. 특정 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
>[자습서: 특정 사용자에게 알림 보내기](push-notifications-android-specific-users-firebase-cloud-messaging.md)

알림 보내기에 대한 다른 자습서 목록은 다음과 같습니다.

- Azure Mobile Apps: Notification Hubs와 통합된 Mobile Apps 백 엔드에서 알림을 보내는 방법에 대한 예제는 [iOS 앱에 푸시 알림 추가](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)를 참조하세요.

- ASP.NET: [Notification Hubs를 사용하여 사용자에게 푸시 알림 보내기](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)를 참조하세요.

- Azure Notification Hubs Java SDK: Java에서 알림을 보내는 방법은 [Java에서 Notification Hubs를 사용하는 방법](notification-hubs-java-push-notification-tutorial.md)을 참조하세요. 이는 Eclipse for Android Development에서 테스트되었습니다.

- PHP: [PHP에서 Notification Hubs를 사용하는 방법](notification-hubs-php-push-notification-tutorial.md)을 참조하세요.