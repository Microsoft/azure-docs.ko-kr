---
title: Apache Cordova 앱에 푸시 알림 추가
description: Mobile Apps를 사용하여 Apache Cordova 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다.
ms.assetid: 92c596a9-875c-4840-b0e1-69198817576f
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 6ec214c0f1a4f8333bf88790de8d2936fce39002
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668907"
---
# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Apache Cordova 앱에 푸시 알림 추가

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

## <a name="overview"></a>개요

이 자습서에서는 푸시 알림을 [Apache Cordova 빠른][5] 시작 프로젝트에 추가 하 여 레코드가 삽입 될 때마다 장치에 푸시 알림이 전송 됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Mobile Apps에 대한 .net 백 엔드 서버 SDK 사용][1]을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서에서는 Visual Studio 2015를 사용하여 개발 된 Apache Cordova 애플리케이션이 있다고 가정합니다. 이 디바이스는 Google Android Emulator, Android 디바이스, Windows 디바이스 또는 iOS 디바이스에서 실행해야 합니다.

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio Community 2015][2] 이상이 있는 PC
* [Visual Studio Tools for Apache Cordova][4]
* [활성 Azure 계정][3]
* 완료 된 [Apache Cordova 빠른][5] 시작 프로젝트
* 용 확인 된 전자 메일 주소를 사용 하는 [Google 계정][6]
* Io [Apple 개발자 프로그램 멤버 자격][7] 및 ios 장치 (ios 시뮬레이터는 푸시 알림을 지원 하지 않음)
* Windows [Microsoft Store 개발자 계정][8] 및 Windows 10 장치

## <a name="configure-hub"></a>알림 허브 구성

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[이 섹션의 단계를 보여 주는 비디오를 시청][9]하세요.

## <a name="update-the-server-project"></a>서버 프로젝트 업데이트

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="add-push-to-app"></a>Cordova 앱 수정

Apache Cordova 앱 프로젝트가 푸시 알림을 처리할 준비가 되었는지 확인하려면 Cordova 푸시 플러그 인 및 플랫폼별 푸시 서비스를 설치합니다.

#### <a name="update-the-cordova-version-in-your-project"></a>프로젝트에서 Cordova 버전을 업데이트합니다.

프로젝트에서 v6.1.1보다 이전 버전의 Apache Cordova가 사용되는 경우 클라이언트 프로젝트를 업데이트합니다. 프로젝트를 업데이트 하려면 다음 단계를 수행합니다.

* 구성 디자이너를 열려면 `config.xml`을 마우스 오른쪽 단추로 클릭합니다.
* **플랫폼** 탭을 선택합니다.
* **Cordova CLI** 텍스트 상자에서 **6.1.1**을 선택합니다. 
* 프로젝트를 업데이트 하려면 **빌드**를 선택한 다음, **솔루션 빌드**를 선택합니다.

#### <a name="install-the-push-plugin"></a>푸시 플러그 인 설치

Apache Cordova 애플리케이션에서는 기본적으로 디바이스 또는 네트워크 기능을 처리하지 않습니다.  이러한 기능은 [npm][10] 또는 GitHub에 게시된 플러그 인에서 제공됩니다. `phonegap-plugin-push` 플러그 인은 네트워크 푸시 알림을 처리합니다.

다음 방법 중 하나로 푸시 플러그 인을 설치할 수 있습니다.

**명령 프롬프트에서:**

다음 명령 실행:

    cordova plugin add phonegap-plugin-push

**Visual Studio 내에서**

1. 솔루션 탐색기에서 `config.xml` 파일을 엽니다. 다음으로, **플러그 인** > **사용자 지정**을 선택합니다. 그런 다음, **Git**를 설치 원본으로 선택합니다.

2. 소스로 `https://github.com/phonegap/phonegap-plugin-push`을 입력합니다.

    ![솔루션 탐색기에서 config.xml 파일을 엽니다.][img1]

3. 설치 원본 옆의 화살표를 선택합니다.

4. **SENDER_ID**에서 Google 개발자 콘솔 프로젝트에 대해 숫자 프로젝트 ID가 이미 지정된 경우 여기에 추가할 수 있습니다. 그렇지 않으면 자리 표시자 값(예: 777777)을 입력합니다. Android를 대상으로 하는 경우 나중에 config.xml 파일에서 이 값을 업데이트할 수 있습니다.

    >[!NOTE]
    >버전 2.0.0부터 google-services.json은 보낸 사람 ID를 구성하는 프로젝트의 루트 폴더에 설치돼야 합니다. 자세한 내용은 [설치 설명서](https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md)를 참조하세요.

5. **추가**를 선택합니다.

이제 푸시 플러그 인이 설치되었습니다.

#### <a name="install-the-device-plugin"></a>디바이스 플러그 인 설치

푸시 플러그 인을 설치할 때 사용한 것과 동일한 절차를 따릅니다. 코어 플러그 인 목록에서 디바이스 플러그 인을 추가합니다. (이를 찾으려면 **플러그 인** > **Core**를 선택 합니다.) 플랫폼 이름을 가져오려면이 플러그 인이 필요 합니다.

#### <a name="register-your-device-when-the-application-starts"></a>애플리케이션 시작 시 디바이스 등록 

처음에 Android에 대한 최소한의 코드를 포함합니다. 나중에 iOS 또는 Windows 10에서 실행되도록 앱을 수정할 수 있습니다.

1. 로그인 프로세스용 콜백 동안 **registerForPushNotifications**에 호출을 추가하십시오. 또는 **onDeviceReady** 메서드의 맨 아래에 추가할 수 있습니다.

    ```javascript
    // Log in to the service.
    client.login('google')
        .then(function () {
            // Create a table reference.
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems.
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item.
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

                // Added to register for push notifications.
            registerForPushNotifications();

        }, handleError);
    ```

    이 예제에서는 인증 성공 후에 **registerForPushNotifications**를 호출하는 경우를 보여줍니다. 필요한 만큼 자주 `registerForPushNotifications()`를 호출할 수 있습니다.

2. 다음과 같이 새 **registerForPushNotifications** 메서드를 추가합니다.

    ```javascript
    // Register for push notifications. Requires that phonegap-plugin-push be installed.
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

    // Handle the registration event.
    pushRegistration.on('registration', function (data) {
        // Get the native platform of the device.
        var platform = device.platform;
        // Get the handle returned during registration.
        var handle = data.registrationId;
        // Set the device-specific message template.
        if (platform == 'android' || platform == 'Android') {
            // Register for GCM notifications.
            client.push.register('gcm', handle, {
                mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'iOS') {
            // Register for notifications.
            client.push.register('apns', handle, {
                mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
            });
        } else if (device.platform === 'windows') {
            // Register for WNS notifications.
            client.push.register('wns', handle, {
                myTemplate: {
                    body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                    headers: { 'X-WNS-Type': 'wns/toast' } }
            });
        }
    });

    pushRegistration.on('notification', function (data, d2) {
        alert('Push Received: ' + data.message);
    });

    pushRegistration.on('error', handleError);
    }
    ```
3. 용 위의 코드에서 `Your_Project_ID`를 [Google 개발자 콘솔][18]의 앱에 대한 숫자 프로젝트 ID로 바꿉니다.

## <a name="optional-configure-and-run-the-app-on-android"></a>(선택 사항) Android에서 앱 구성 및 실행

이 섹션을 완료하여 Android에 대한 푸시 알림을 사용하도록 설정합니다.

#### <a name="enable-gcm"></a>Firebase Cloud Messaging 사용

처음에는 Google Android 플랫폼을 대상으로 하므로 Firebase Cloud Messaging을 사용하도록 설정해야 합니다.

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

#### <a name="configure-backend"></a>FCM을 사용하여 푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

#### <a name="configure-your-cordova-app-for-android"></a>Android용 Cordova 앱 구성

Cordova 앱에서 **config.xml**을 엽니다. 그런 다음 `Your_Project_ID`를 [Google 개발자 콘솔][18]의 앱에 대한 숫자 프로젝트 ID로 바꿉니다.

```xml
<plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
    <variable name="SENDER_ID" value="Your_Project_ID" />
</plugin>
```

**index.js**를 엽니다. 그런 다음, 숫자 프로젝트 ID를 사용하도록 코드를 업데이트합니다.

```javascript
pushRegistration = PushNotification.init({
    android: { senderID: 'Your_Project_ID' },
    ios: { alert: 'true', badge: 'true', sound: 'true' },
    wns: {}
});
```

#### <a name="configure-device"></a>USB 디버깅을 위해 Android 디바이스 구성

Android 디바이스에 애플리케이션을 배포하려면 먼저 USB 디버깅을 사용하도록 설정해야 합니다. Android 휴대폰에서 다음 단계를 수행합니다.

1. **설정** > **전화 정보**로 이동합니다. 그런 다음, 개발자 모드가 사용하도록 설정될 때까지 **빌드 번호**를 누릅니다(약 7회).
2. 다시 **설정** > **개발자 옵션**에서 **USB 디버깅**을 활성화합니다. 그런 다음, Android 휴대폰을 USB 케이블을 사용해 개발 PC에 연결합니다.

이 방법은 Android 6.0(Marshmallow)을 실행하는 Google Nexus 5X를 사용하여 테스트되었습니다. 그러나 기술은 모든 최신 Android 릴리스에서 공통적입니다.

#### <a name="install-google-play-services"></a>Google Play Services 설치

푸시 플러그 인은 푸시 알림용으로 Google Play Services를 사용합니다.

1. Visual Studio에서 **도구** > **Android** > **Android SDK Manager**를 선택합니다. 그런 다음, **추가** 폴더를 확장합니다. 다음 SDK 각각이 설치되어 있는지 확인하기 위해 적절 한 상자를 확인합니다.

   * Android 2.3 이상
   * Google Repository 개정 27 이상
   * Google Play Services 9.0.2 이상

2. **Install Packages**를 선택합니다. 그런 다음, 설치가 완료될 때까지 기다립니다.

현재 필요한 라이브러리가 [phonegap-plugin-push 설치 설명서][19]에 나와 있습니다.

#### <a name="test-push-notifications-in-the-app-on-android"></a>Android 앱에서 푸시 알림 테스트

이제 앱을 실행하고 TodoItem 테이블에 항목을 삽입하여 푸시 알림을 테스트할 수 있습니다. 동일한 백 엔드를 사용하는 한, 같은 디바이스에서 테스트해도 되고 두 번째 디바이스에서 테스트해도 됩니다. 다음 방법 중 하나로 Android 플랫폼에서 Cordova 앱을 테스트합니다.

* *실제 디바이스에서:* USB 케이블을 사용하여 Android 디바이스를 개발 컴퓨터에 연결합니다.  **Google Android Emulator** 대신 **디바이스**를 선택합니다. Visual Studio에서 디바이스에 애플리케이션을 배포하고 애플리케이션을 실행합니다. 이제 디바이스에서 애플리케이션과 상호 작용할 수 있습니다.

  [Mobizen][20] 같은 화면 공유 응용 프로그램은 Android 응용 프로그램을 개발 하는 데 도움이 될 수 있습니다. Mobizen은 PC의 웹 브라우저에 Android 화면을 투영합니다.

* *Android 에뮬레이터에서:* 에뮬레이터를 사용할 때 필요한 추가 구성 단계가 있습니다.

    아래와 같이 AVD(Android 가상 디바이스) 관리자에서 대상으로 설정된 Google API가 있는 가상 디바이스에 배포해야 합니다.

    ![Android Virtual Device Manager](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    더 빠른 x86 에뮬레이터를 사용 하려는 경우 [HAXM 드라이버를 설치][11]하 고 에뮬레이터를 사용 하도록 구성 합니다.

    **앱** > **설정** > **계정 추가**를 선택하여 Android 디바이스에 Google 계정을 추가합니다. 그런 다음, 표시되는 메시지를 따릅니다.

    ![Android 디바이스에 Google 계정 추가](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    이전처럼 todolist 앱을 실행하고 새 todo 항목을 삽입합니다. 이때 알림 아이콘이 알림 영역에 표시됩니다. 알림 서랍을 열어서 전체 알림 텍스트를 볼 수 있습니다.

    ![알림 보기](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

## <a name="optional-configure-and-run-on-ios"></a>(선택 사항) iOS에서 구성 및 실행

이 섹션은 iOS 디바이스에서 Cordova 프로젝트를 실행하기 위한 것입니다. iOS 디바이스로 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="install-and-run-the-ios-remote-build-agent-on-a-mac-or-cloud-service"></a>Mac 또는 클라우드 서비스에서 iOS remotebuild 에이전트를 설치하고 실행합니다.

Visual Studio를 사용 하 여 iOS에서 Cordova 앱을 실행 하려면 먼저 [ios 설치 가이드][12] 의 단계를 진행 하 여 원격 빌드 에이전트를 설치 하 고 실행 합니다.

IOS용 앱을 빌드할 수 있는지 확인합니다. 설치 가이드의 단계는 Visual Studio에서 iOS용 앱을 빌드하는 데 필요합니다. Mac이 없는 경우 MacInCloud와 같은 서비스에서 remotebuild 에이전트를 사용하여 iOS를 빌드할 수 있습니다. 자세한 내용은 [클라우드에서 iOS 앱 실행][21]을 참조 하세요.

> [!NOTE]
> Xcode 7 이상에서는 iOS에 대한 푸시 플러그 인을 사용해야 합니다.

#### <a name="find-the-id-to-use-as-your-app-id"></a>앱 ID로 사용할 ID 찾기

푸시 알림을 위해 앱을 등록하기 전에 Cordova 앱에서 config.xml을 열고, 위젯 요소에서 `id` 특성 값을 찾은 후 나중에 사용할 수 있도록 복사합니다. 다음 XML에서 ID는 `io.cordova.myapp7777777`입니다.

```xml
<widget defaultlocale="en-US" id="io.cordova.myapp7777777"
    version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="https://www.w3.org/ns/widgets"
    xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">
```

나중에 Apple 개발자 포털에서 앱 ID를 만들 때 이 식별자를 사용합니다. 개발자 포털에서 다른 앱 ID를 만들 경우 이 자습서의 뒷부분에 나오는 몇 가지 추가 단계를 수행해야 합니다. 위젯 요소의 ID는 개발자 포털의 앱 ID와 일치해야 합니다.

#### <a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Apple 개발자 포털의 푸시 알림에 대한 앱 등록

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[유사한 단계를 보여 주는 비디오 보기](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

#### <a name="configure-azure-to-send-push-notifications"></a>푸시 알림을 전송하도록 Azure 구성

[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

#### <a name="verify-that-your-app-id-matches-your-cordova-app"></a>앱 ID가 Cordova 앱과 일치하는지 확인

Apple 개발자 계정에서 만든 앱 ID가 config.xml 파일의 위젯 요소 ID와 일치할 경우 이 단계를 건너뛸 수 있습니다. 그러나 ID가 일치하지 않는 경우 다음 단계를 수행합니다.

1. 프로젝트에서 플랫폼 폴더를 삭제합니다.
2. 프로젝트에서 플러그 인 폴더를 삭제합니다.
3. 프로젝트에서 node_modules 폴더를 삭제합니다.
4. Apple 개발자 계정에서 만든 앱 ID를 사용하도록 config.xml 파일에 있는 위젯 요소의 ID 특성을 업데이트합니다.
5. 프로젝트를 다시 빌드합니다.

##### <a name="test-push-notifications-in-your-ios-app"></a>iOS 앱에서 푸시 알림 테스트

1. Visual Studio에서 **iOS**가 배포 대상으로 선택되도록 확인합니다. 다음 선택 **디바이스**를 선택해 연결된 iOS 디바이스에서 푸시 알림을 실행합니다.

    ITunes를 사용해 PC에 연결된 iOS 디바이스에서 푸시 알림을 실행할 수 있습니다. iOS 시뮬레이터는 푸시 알림을 지원하지 않습니다.

2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 선택해 프로젝트를 빌드하고 iOS 디바이스에서 앱을 시작합니다. 그런 다음, **확인**을 선택하여 푸시 알림을 허용하도록 합니다.

   > [!NOTE]
   > 앱은 첫 번째 실행 동안 푸시 알림에 대한 확인을 요청합니다.

3. 앱에서 작업을 입력하고 더하기 **(+)** 아이콘을 선택합니다.
4. 알림을 받았는지 확인합니다. 그런 다음, **확인**을 선택해 알림을 해제합니다.

## <a name="optional-configure-and-run-on-windows"></a>(선택 사항) Windows에서 구성 및 실행

이 섹션은 Windows 10 디바이스에서 Apache Cordova 앱 프로젝트를 실행하는 방법을 설명하기 위해 작성되었습니다(PhoneGap 푸시 플러그 인은 Windows 10에서 지원됨). Windows 디바이스로 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="register-your-windows-app-for-push-notifications-with-wns"></a>WNS를 사용하여 푸시 알림에 대해 Windows 앱 등록

Visual Studio에서 저장소 옵션을 사용하려면 솔루션 플랫폼 목록에서 Windows 대상(예: **Windows-x64** 또는 **Windows-x86**)을 선택합니다. (푸시 알림에 대해 **Windows AnyCPU** 선택 안 함.)

[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[유사한 단계를 보여 주는 비디오 보기][13]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS에 대한 알림 허브 구성

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Windows 푸시 알림을 지원하도록 Cordova 앱 구성

구성 디자이너를 열려면 **config.xml**을 마우스 오른쪽 단추로 클릭합니다. 그런 다음, **뷰 디자이너**를 선택합니다. 다음으로, **Windows** 탭을 선택하고 **Windows 대상 버전** 아래에서 **Windows 10**을 선택합니다.

기본(디버그) 빌드에서 푸시 알림을 지원하려면 **build.json** 파일을 엽니다. 그런 다음, "릴리스" 구성을 디버그 구성에 복사합니다.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
    }
}
```

업데이트 후에 **build.json** 파일에는 다음 코드가 포함됩니다.

```json
"windows": {
    "release": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        },
    "debug": {
        "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
        "publisherId": "CN=yourpublisherID"
        }
    }
```

앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 모바일 앱 백 엔드에서 알림을 등록해야 합니다. 솔루션의 모든 Windows 프로젝트에 대해 이 섹션을 반복합니다.

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows 앱에서 푸시 알림 테스트

Visual Studio에서 Windows 플랫폼이 배포 대상(예: **Windows-x64** 또는 **Windows-x86**)으로 선택되어 있는지 확인합니다. Visual Studio를 호스트하는 Windows 10 PC에서 앱을 실행하려면 **로컬 컴퓨터**를 선택합니다.

1. **실행** 단추를 선택해 프로젝트를 빌드하고 앱을 시작합니다.

2. 앱에서 새 todoitem에 대한 이름을 입력한 다음, 더하기 **(+)** 아이콘을 선택하여 추가합니다.

항목이 추가될 때 알림을 받았는지 확인합니다.

## <a name="next-steps"></a>다음 단계

* 푸시 알림에 대한 자세한 내용은 [Notification Hubs][17]를 참조하세요.
* 아직 수행 하지 않은 경우 Apache Cordova 앱에 [인증을 추가][14] 하 여 자습서를 계속 합니다.

다음 SDK를 사용하는 방법을 알아봅니다.

* [Apache Cordova SDK][15]
* [ASP.NET 서버 SDK][1]
* [Node.js 서버 SDK][16]

<!-- Images -->
[img1]: ./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png

<!-- URLs -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: https://www.visualstudio.com/
[3]: https://azure.microsoft.com/pricing/free-trial/
[4]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[5]: app-service-mobile-cordova-get-started.md
[6]: https://go.microsoft.com/fwlink/p/?LinkId=268302
[7]: https://developer.apple.com/programs/
[8]: https://developer.microsoft.com/en-us/store/register
[9]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub
[10]: https://www.npmjs.com/
[11]: https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM
[12]: https://taco.visualstudio.com/en-us/docs/ios-guide/
[13]: https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push
[14]: app-service-mobile-cordova-get-started-users.md
[15]: app-service-mobile-cordova-how-to-use-client-library.md
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[17]: ../notification-hubs/notification-hubs-push-notification-overview.md
[18]: https://console.developers.google.com/home/dashboard
[19]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[20]: https://www.mobizen.com/
[21]: https://taco.visualstudio.com/en-us/docs/build_ios_cloud/
