---
title: Azure Mobile Apps용 Apache Cordova 플러그인 사용 방법
description: Azure Mobile Apps용 Apache Cordova 플러그인 사용 방법
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 3c22aab20a9260bfd21869f0b327211e2f3d8894
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109579"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps용 Apache Cordova 클라이언트 라이브러리를 사용하는 방법
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure Mobile Apps용 Apache Cordova 플러그 인]을 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure Mobile Apps를 처음 접하는 경우 먼저 [Azure Mobile Apps 빠른 시작] 을 완료하여 백 엔드를 만들고, 테이블을 만든 다음 미리 빌드된 Apache Cordova 프로젝트를 다운로드합니다. 이 가이드에서는 클라이언트 쪽 Apache Cordova 플러그 인에 초점을 둡니다.

## <a name="supported-platforms"></a>지원되는 플랫폼
이 SDK는 iOS, Android 및 Windows 디바이스에서 Apache Cordova v6.0.0 이상을 지원합니다.  지원되는 플랫폼은 다음과 같습니다.

* Android API 19-24(Nougat를 통한 KitKat)
* iOS 버전 8.0 이상.
* Windows Phone 8.1
* 범용 Windows 플랫폼

## <a name="Setup"></a>설정 및 필수 조건
이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다. 또한 이 가이드는 사용자 코드에 Apache Cordova 플러그 인을 추가했다고 가정합니다.  아직 추가하지 않은 경우 명령줄에서 프로젝트에 Apache Cordova 플러그 인을 추가할 수 있습니다.

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

[첫 번째 Apache Cordova 앱]을 만드는 방법에 대한 자세한 내용은 해당 설명서를 참조하세요.

## <a name="ionic"></a>Ionic v2 앱 설정

Ionic v2 프로젝트를 올바르게 구성하려면 먼저 기본 앱을 만들고 Cordova 플러그 인을 추가합니다.

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

`app.component.ts`에 다음 줄을 추가하여 클라이언트 개체를 만듭니다.

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

이제 프로젝트를 빌드한 후 브라우저에서 실행할 수 있습니다.

```
ionic platform add browser
ionic run browser
```

Azure Mobile Apps Cordova 플러그 인은 Ionic v1 및 v2 앱을 모두 지원합니다.  Ionic v2 앱에만 `WindowsAzure` 개체에 대한 추가 선언이 필요합니다.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>방법: 사용자 인증
Azure App Service는 Facebook, Google, Microsoft 계정 및 Twitter와 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [인증 시작] 자습서를 참조하세요.

Apache Cordova 앱에 인증을 사용할 때는 다음 Cordova 플러그 인을 사용할 수 있어야 합니다.

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

두 가지의 인증 흐름, 즉 서버 흐름과 클라이언트 흐름이 지원됩니다.  서버 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 흐름의 경우 공급자 특정 디바이스별 SDK를 사용하므로 Single Sign-On과 같은 디바이스 특정 기능을 통해 깊이 있는 통합이 가능합니다.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>방법: 외부 리디렉션 URL에 대해 모바일 App Service 구성
여러 가지 유형의 Apache Cordova 애플리케이션은 루프백 기능을 사용하여 OAuth UI 흐름을 처리합니다.  기본적으로 인증 서비스만 사용자 서비스의 활용 방법을 알기 때문에 localhost의 OAuth UI 흐름에 문제가 발생합니다.  다음은 문제가 있는 OAuth UI 흐름의 예입니다.

* Ripple 에뮬레이터.
* Ionic을 사용하여 라이브 다시 로드.
* 로컬에서 모바일 백엔드 실행
* 인증을 제공하지 않는 다른 Azure App Service에서 모바일 백 엔드 실행.

구성에 로컬 설정을 추가하려면 다음 지침을 따릅니다.

1. [Azure Portal]에 로그인
2. **모든 리소스** 또는 **App Services**를 선택한 후 모바일 앱의 이름을 클릭합니다.
3. **도구** 클릭
4. 관찰 메뉴에서 **리소스 Explorer**를 클릭한 다음 **이동**을 클릭합니다.  새 창 또는 탭이 열립니다.
5. 왼쪽 탐색 창에서 사이트에 대한 **config**, **authsettings** 노드를 확장합니다.
6. **편집**을 클릭합니다.
7. "allowedExternalRedirectUrls" 요소를 찾습니다.  null 또는 일련의 값으로 설정되었을 수 있습니다.  이 값을 다음 값으로 변경합니다.

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    URL을 서비스의 URL로 바꿉니다.  예를 들면 `http://localhost:3000` (Node.js 샘플 서비스)에 대해 또는 `http://localhost:4400` (Ripple 서비스)에 대 한 합니다.  그러나 이러한 URL은 예제일 뿐입니다. 예에서 언급된 서비스를 포함하여 사용자의 상황이 다를 수 있습니다.
8. 화면 오른쪽 위에 있는 **읽기/쓰기** 단추를 클릭합니다.
9. 녹색 **배치** 단추를 클릭합니다.

이 시점에서 설정이 저장됩니다.  설정 저장이 완료될 때까지 브라우저 창을 닫지 않도록 합니다.
또한 App Service에 대한 CORS 설정에 이러한 루프백 URL을 추가합니다.

1. [Azure Portal]에 로그인
2. **모든 리소스** 또는 **App Services**를 선택한 후 모바일 앱의 이름을 클릭합니다.
3. 설정 블레이드가 자동으로 열립니다.  자동으로 열리지 않으면 **모든 설정**을 클릭합니다.
4. API 메뉴에서 **CORS**를 클릭합니다.
5. 제공된 입력란에 추가할 URL을 입력하고 Enter 키를 누릅니다.
6. 필요에 따라 추가 URL을 입력합니다.
7. **저장**을 클릭하여 설정을 저장합니다.

새 설정이 적용되는 데는 약 10~15초가 걸립니다.

## <a name="register-for-push"></a>방법: 푸시 알림 등록
푸시 알림을 처리하기 위해 [phonegap-plugin-push]를 설치합니다.  이 플러그인은 명령줄에서 `cordova plugin add` 명령을 사용하거나 Visual Studio 내에서 Git 플러그 인 설치 관리자를 통해 쉽게 추가할 수 있습니다.  Apache Cordova 앱에서 다음 코드는 푸시 알림을 위해 디바이스를 등록합니다.

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Notification Hubs SDK를 사용하여 서버에서 푸시 알림을 보냅니다.  클라이언트에서 직접 푸시 알림을 보내지 마세요. 이렇게 하면 Notification Hubs 또는 PNS에 대한 서비스 거부 공격을 트리거하는 데 이용될 수 있습니다.  이러한 공격의 결과로 PNS가 트래픽을 차단할 수 있습니다.

## <a name="more-information"></a>자세한 정보

우리의 [API 설명서](https://azure.github.io/azure-mobile-apps-js-client/)에서 자세한 API 세부 정보를 찾을 수 있습니다.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com
[Azure Mobile Apps 빠른 시작]: app-service-mobile-cordova-get-started.md
[인증 시작]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps용 Apache Cordova 플러그 인]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[첫 번째 Apache Cordova 앱]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx
