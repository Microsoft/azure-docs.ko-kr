---
title: Azure Notification Hubs를 사용 하는 Swift iOS 앱에 알림 푸시 | Microsoft Docs
description: Azure Notification Hubs를 사용 하는 Swift iOS 앱에 알림을 푸시하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340397"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>자습서: Notification Hubs REST API를 사용 하는 Swift iOS 앱에 알림 푸시

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

이 자습서를 사용 하 여 Azure Notification Hubs iOS Swift 기반 응용 프로그램에 푸시 알림을 사용 하 여 합니다 [REST API](/rest/api/notificationhubs/)합니다. 사용 하 여 푸시 알림을 받는 빈 iOS 앱을 만들어야 합니다 [Apple Push Notification 서비스 (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)합니다.

이 자습서에서는 다음 단계를 안내합니다.

> [!div class="checklist"]
> * 인증서 서명 요청 파일을 생성 합니다.
> * 푸시 알림에 대해 앱을 요청 합니다.
> * 앱 용 프로비저닝 프로필을 만듭니다.
> * 알림 허브를 만듭니다.
> * APNs 정보로 알림 허브를 구성 합니다.
> * 알림 허브에 iOS 앱을 연결 합니다.
> * 솔루션을 테스트 합니다.

## <a name="prerequisites"></a>필수 조건

필수 조건을 충족하려면 다음이 필요합니다.

- 통해 이동할 [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md) 서비스에 익숙하지 않은 경우.
- 에 대해 자세히 알아보려면 [등록 및 설치](notification-hubs-push-notification-registration-management.md)합니다.
- 활성 [Apple 개발자 계정](https://developer.apple.com)합니다.
- Xcode keychain에 설치 된 유효한 개발자 인증서와 함께 실행 하는 Mac.
- 물리적 iPhone 장치를 실행 하 고 시뮬레이터를 사용 하 여 푸시 알림을 테스트할 수 없는 대로를 디버그할 수 있습니다.
- 물리적 iPhone 장치에 등록 합니다 [Apple 포털](https://developer.apple.com) 인증서를 사용 하 여 연결.
- [Azure 구독](https://portal.azure.com) 만들고 리소스를 관리 하는 위치입니다.

IOS 개발을 사용 하 여 이전에 경험이 없다고가 하는 경우에이 첫 번째 원칙은 예제를 만드는 단계를 따라 할 해야 합니다. 그러나 다음과 같은 개념 사용 경험에서 성능을 높일 수 있습니다.

- Xcode 및 Swift를 사용 하 여 iOS 앱을 빌드합니다.
- 구성 된 [Azure 알림 허브](notification-hubs-ios-apple-push-notification-apns-get-started.md) iOS에 대 한 합니다.
- 합니다 [Apple Developer 포털](https://developer.apple.com) 하며 [Azure portal](https://portal.azure.com)합니다.

> [!NOTE]
> 알림 허브는 사용 하도록 구성 합니다 **샌드박스** 인증 모드에만 해당 합니다. 프로덕션 워크 로드에 대 한이 인증 모드를 사용 하지 해야 합니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>알림 허브에 iOS 앱 연결

이 섹션에서는 알림 허브에 연결 하는 iOS 앱을 빌드합니다.  

### <a name="create-an-ios-project"></a>IOS 프로젝트 만들기

1. Xcode에서 새 iOS 프로젝트를 만들고 **애플리케이션 단일 보기** 템플릿을 선택합니다.

1. 새 프로젝트에 대 한 옵션을 설정 하는 경우:

   1. 지정 된 **제품 이름** (PushDemo) 및 **조직 식별자** (`com.<organization>`) 설정 하는 경우는 **번들 식별자** Apple Developer 포털에서 합니다.

   1. 선택 합니다 **팀** 는 **앱 ID** 등록 설정 되었습니다.

   1. 설정 된 **언어** 하 **Swift**합니다.

   1. **다음**을 선택합니다.

1. 라는 새 폴더를 만듭니다 **SupportingFiles**합니다.

1. 라는 새 p 목록 파일을 만듭니다 **devsettings.plist** 에 **SupportingFiles** 폴더입니다. 이 폴더에 추가 해야 하 **gitignore** git 리포지토리를 사용 하 여 작업할 때 커밋된 되지 파일입니다. 프로덕션 앱의 경우에 가능성이 수 조건부로 설정 하 게 이러한 비밀 자동화 된 빌드 프로세스의 일부로. 이러한 설정은이 연습에서 다루지 않습니다.

1. 업데이트 **devsettings.plist** 프로 비전 하는 알림 허브에서 고유한 값을 사용 하 여 다음 구성 항목을 포함 하려면:

   | 키                            | Type                     | 값                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | String                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | String                   | \<hubName>                 |
   | notificationHubNamespace       | String                   | \<hubNamespace>            |

   Azure portal에서 알림 허브 리소스를 이동 하 여 필요한 값을 찾을 수 있습니다. 특히 합니다 **notificationHubName** 및 **notificationHubNamespace** 의 오른쪽 위 모서리에 있는 값은 합니다 **Essentials** 합니다 내요약**개요** 페이지입니다.

   ![Notification Hubs Essentials 요약](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   찾을 수도 있습니다는 **notificationHubKeyName** 하 고 **notificationHubKey** 로 이동 하 여 값 **액세스 정책을** 각각 선택 하 고  **액세스 정책**와 같은 `DefaultFullSharedAccessSignature`합니다. 그 후에서 복사 합니다 **기본 연결 문자열** 값을 접두사로 `SharedAccessKeyName=` 에 대 한 `notificationHubKeyName` 값 접두사가 붙은 `SharedAccessKey=` 에 대 한는 `notificationHubKey`합니다.

   연결 문자열 형식 이어야 합니다.

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   간단히 말하자면, 지정 `DefaultFullSharedAccessSignature` 토큰을 사용 하 여 알림을 보낼 수 있도록 합니다. 실제로 `DefaultListenSharedAccessSignature` 만 하려는 알림을 수신 하는 상황에 보다 적합할 것입니다.

1. 아래 **Project Navigator**를 선택 합니다 **프로젝트 이름** 선택한 후는 **일반** 탭.

1. 찾을 **Identity** 설정한 후 합니다 **번들 식별자** 일치 시킬 수 있도록 값 `com.<organization>.PushDemo`, 값에 사용 되는 합니다 **앱 ID** 이전 단계에서 합니다.

1. 찾을 **서명**를 선택한 다음 적절 한 **팀** 에 대 한 프로그램 **Apple 개발자 계정**합니다. 합니다 **팀** 값에는 인증서 및 프로필 생성 하는 것과 일치 해야 합니다.

1. Xcode이 자동으로 적절 한 아래로 끌어오기 **프로 비전 프로필** 값에 기반 합니다 **번들 식별자**합니다. 새 표시 되지 않으면 **프로 비전 프로필** 값에 대 한 프로필을 새로 고쳐 보세요 합니다 **서명 Id** 를 선택 하 여 **Xcode**  >  **기본 설정** > **계정** > **정보를 볼**합니다. 선택 **서명 Id**를 선택한 후 합니다 **새로 고침** 프로필을 다운로드 하려면 맨 아래 오른쪽에 있는 단추입니다.

1. 선택 합니다 **기능** 탭을 확인 **푸시 알림** 활성화 됩니다.

1. 오픈 프로그램 **AppDelegate.swift** 구현 하는 파일을 **UNUserNotificationCenterDelegate** 프로토콜 및 클래스의 맨 위에 다음 코드를 추가:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    이러한 멤버는 나중에 사용할 수 있습니다. 특히 사용 하 여는 **태그** 하 고 **genericTemplate** 멤버 등록의 일환으로 합니다. 태그에 대 한 자세한 내용은 참조 하세요. [등록에 대 한 태그](notification-hubs-tags-segment-push-message.md) 하 고 [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md)합니다.

1. 동일한 파일에 다음 코드를 추가 합니다 **didFinishLaunchingWithOptions** 함수:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    이 코드에서 설정 값을 검색 **devsettings.plist**를 설정 합니다 **AppDelegate** 클래스를 **UNUserNotificationCenter** 대리자 요청 권한 부여 푸시 알림, 한 다음 호출 **registerForRemoteNotifications**합니다.

    간단히 말하자면, 코드를 지 원하는 *iOS 10 이상만 해당*합니다. 조건에 따라 사용 하 여 해당 Api 및 접근 방식을 마찬가지로 일반적으로 이전 OS 버전에 대 한 지원을 추가할 수 있습니다.

1. 동일한 파일에서 다음 함수를 추가 합니다.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    코드를 사용 하는 **installationId** 하 고 **pushChannel** 알림 허브를 사용 하 여 등록 하는 값입니다. 사용 하는 예에서 **UIDevice.current.identifierForVendor** 장치와 다음 서식 지정을 식별 하는 고유 값을 제공 하는 **deviceToken** 원하는 있도록  **pushChannel** 값입니다. 합니다 **showAlert** 단순히 데모를 위해 일부 메시지 텍스트를 표시할 함수가 있습니다.

1. 여전히 합니다 **AppDelegate.swift** 파일을 추가 합니다 **willPresent** 하 고 **didReceive** 함수를 **UNUserNotificationCenterDelegate**. 이러한 함수는 각각 응용 프로그램이 포그라운드 또는 백그라운드에서 실행 되는 알림을 받을 때 경고를 표시 합니다.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Print 문은 맨 아래에 추가 합니다 **didRegisterForRemoteNotificationsWithDeviceToken** 되었는지 확인 하는 함수 **installationId** 하 고 **pushChannel** 되 할당 된 값입니다.

1. 만들기는 **모델**를 **Services**, 및 **유틸리티** 기본적인 구성 요소에 대 한 폴더를 추가할 프로젝트에 나중입니다.

1. 프로젝트가 빌드되고 물리적 장치에서 실행 되는지 확인 합니다. 시뮬레이터를 사용 하 여 푸시 알림은 테스트할 수 없습니다.

### <a name="create-models"></a>모델 만들기

이 단계에서는 모델을 나타내는 집합을 만든 합니다 [Notification Hubs REST API](/rest/api/notificationhubs/) 페이로드 및 저장할 필요한 공유 액세스 서명 (SAS) 토큰 데이터입니다.

1. 이라는 새 Swift 파일 추가 **PushTemplate.swift** 에 **모델** 폴더입니다. 이 모델은 나타내는 구조체를 제공 합니다 **본문** 의 일환으로 개별 템플릿의 합니다 **DeviceInstallation** 페이로드입니다.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. 이라는 새 Swift 파일 추가 **DeviceInstallation.swift** 에 **모델** 폴더입니다. 이 파일을 만들거나 업데이트 하기 위한 페이로드를 나타내는 구조체 정의 **장치 설치**합니다. 파일에 다음 코드를 추가합니다.

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. 이라는 새 Swift 파일 추가 **TokenData.swift** 에 **모델** 폴더입니다. 이 모델의 만료와 함께 SAS 토큰을 저장 됩니다.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>SAS 토큰 생성

Notification Hubs는 Azure Service Bus와 동일한 보안 인프라를 사용합니다. REST API를 호출 하려면 해야 [프로그래밍 방식으로 SAS 토큰을 생성할](/rest/api/eventhub/generate-sas-token) 에서 사용할 수 있는 합니다 **권한 부여** 요청의 헤더입니다.  

결과 토큰 형식은 됩니다.

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

프로세스 자체에 동일한 6 개의 주요 단계가 포함 됩니다.  

1. 만료 컴퓨팅 [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time) 자정 협정 세계시 1970 년 1 월 1 일 이후 경과 된 시간 (초) 수를 의미 하는 형식입니다.
1. 서식 지정 합니다 **ResourceUrl** 백분율로 인코딩된 대 / 소문자 있기 때문에 액세스 하려는 리소스를 나타내는입니다. 합니다 **ResourceUrl** 형식은 `'https://<namespace>.servicebus.windows.net/<hubName>'`합니다.
1. 준비 합니다 **StringToSign**, 형식은 `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`합니다.
1. 컴퓨팅 및 Base64 인코딩을 합니다 **서명을** 의 HMAC-SHA256 해시를 사용 하 여는 **StringToSign** 값입니다. 해시 값이 사용 됩니다 합니다 **키** 부분을 **연결 문자열** 각각에 대 한 **권한 부여 규칙**합니다.
1. Base64로 인코딩된 서식을 **서명을** 이므로 % 인코딩.
1. 사용 하 여 예상된 형식에 따라 토큰을 생성 합니다 **UrlEncodedSignature**를 **ExpiryEpoch**를 **KeyName**, 및 **UrlEncodedResourceUrl** 값입니다.

참조 된 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md) 공유 액세스 서명 및 Azure Service Bus 및 Notification Hubs 사용 하는 방법을 보다 철저 한 개요입니다.

Swift이 예에서는 Apple의 오픈 소스를 사용 하려는 **CommonCrypto** 라이브러리를 사용 하 여 서명의 해시 하는 데 도움이 됩니다. C 라이브러리 이므로 즉시 Swift에서 액세스할 수 없습니다. 브리징 헤더를 사용 하 여 라이브러리를 사용할 수 있습니다.

추가 합니다 브리징 헤더를 구성 하려면:

1. Xcode에서 선택 **파일** > **새로 만들기** > **파일** > **헤더 파일**합니다. 헤더 파일 이름을 **BridgingHeader.h**합니다.

1. 가져올 파일을 편집 **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 대상의 업데이트 **빌드 설정** 합니다 브리징 헤더를 참조 합니다.

   1. 열기는 **구성 설정** 까지 아래로 스크롤하여 탭 합니다 **Swift 컴파일러** 섹션입니다.

   1. 있는지 확인 합니다 **Objective-c 호환성 헤더 설치** 옵션을 설정 **예**합니다.

   1. 파일 경로 입력 `'<ProjectName>/BridgingHeader.h'` 에 **Objective-c 브리징 헤더** 옵션입니다. 우리의 브리징 헤더 파일 경로입니다.

   이러한 옵션을 찾을 수 없으면 했는지 확인 합니다 **모든** 선택한 보기 대신 **기본** 또는 **사용자 지정 된**합니다.

   여러 타사 오픈 소스 래퍼 라이브러리를 사용할 수 있습니다 사용 하 여 만들 수 있는 **CommonCrypto** 과정은 비교적 간단 합니다. 그러나 이러한 라이브러리 설명은이 문서의 범위를 벗어납니다.

1. 라는 새 Swift 파일을 추가 **TokenUtility.swift** 내에서 **유틸리티** 폴더 다음 코드를 추가 합니다.

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   이 유틸리티는 SAS 토큰을 생성 하는 일을 담당 하는 논리를 캡슐화 합니다.

   이전에 설명 된 대로 합니다 **getSasToken** 함수 토큰을 준비 하는 데 필요한 대략적인 단계를 조정 합니다. 함수는이 자습서의 뒷부분에 나오는 설치 서비스에서 호출 됩니다.

   나머지 두 함수에서 호출 하는 합니다 **getSasToken** 함수: **sha256HMac** 서명을 계산 및 **urlEncodedString** 관련된 URL 인코딩에 문자열입니다. 합니다 **urlEncodedString** 기본 제공을 사용 하 여 필요한 출력을 얻을 수 없는 함수는 필요한 **addingPercentEncoding** 함수입니다.

   합니다 [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 목표 C에서 이러한 작업에 접근 하는 방법에는 가장 좋은 예 Azure Service Bus SAS 토큰에 대 한 자세한 내용은에서 찾을 수 있습니다 합니다 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md)합니다.

### <a name="verify-the-sas-token"></a>SAS 토큰을 확인 합니다.

클라이언트에서 설치 서비스를 구현 하기 전에 앱은 선택한 HTTP 유틸리티를 사용 하 여 SAS 토큰을 생성 올바르게 확인 합니다. 이 자습서에서는 선택한 도구인 됩니다 **Postman**합니다.

에 적절 하 게 배치 된 print 문 또는 중단점을 사용 합니다 **installationId** 하며 **토큰** 앱에서 생성 되는 값입니다.

다음과 같이 호출 하 여 **설치** API:

1. **Postman**, 새 탭을 엽니다.

1. 요청을 설정 **가져올** 다음 주소를 지정 합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 요청 헤더를 다음과 같이 구성 합니다.

   | 키           | 값            |
   | ------------- | ---------------- |
   | 콘텐츠 형식  | application/json |
   | 권한 부여 | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. 선택 합니다 **코드** 아래에서 오른쪽 상단에 표시 되는 단추를 **저장** 단추입니다. 다음 예제 요청 유사 합니다.

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. 선택 된 **보낼** 단추입니다.

지정 된 등록이 없거나 존재 **installationId** 이 시점에서. "401 권한 없음" 응답 하지 않고 "404 찾을 수 없음" 응답 확인을 유발 합니다. 이 결과 SAS 토큰을 수락 했는지 확인 해야 합니다.

### <a name="implement-the-installation-service-class"></a>설치 서비스 클래스를 구현 합니다.

다음이 기본 래퍼를 구현 하는 [설치 REST API](/rest/api/notificationhubs/create-overwrite-installation)합니다.  

라는 새 Swift 파일을 추가 **NotificationRegistrationService.swift** 아래 합니다 **Services** 폴더를이 파일에 다음 코드를 추가:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

필수 세부 정보는 초기화의 일부로 제공 됩니다. 태그와 템플릿을에 선택적으로 전달 되는 **등록** 부분을 형성 하는 함수는 **장치 설치** JSON 페이로드입니다.  

합니다 **등록** 함수 요청을 준비 하는 다른 개인 함수를 호출 합니다. 응답을 받은 후 완료 호출 되 고 등록이 성공 했는지 여부를 나타냅니다.  

요청 끝점에서 생성 되는 **getBaseAddress** 함수입니다. 알림 허브 매개 변수를 사용 하는 생성 *네임 스페이스* 하 고 *이름* 초기화 하는 동안 제공 된는 합니다.  

합니다 **getSasToken** 함수는 현재 저장된 된 토큰이 유효한 지 여부를 확인 합니다. 이 함수를 호출 하는 토큰이 유효 하지 않으면 **TokenUtility** 새 토큰을 생성 하는 값을 반환 하기 전에 저장 합니다.

마지막으로, **encodeToJson** 요청 본문의 일부로 해당 모델 개체를 JSON으로 변환 합니다.

### <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API를 호출 합니다.

마지막 단계는 업데이트 **AppDelegate** 사용 하려면 **NotificationRegistrationService** 등록할 우리의 **NotificationHub**합니다.

1. 오픈 **AppDelegate.swift** 에 대 한 참조를 저장 하는 클래스 수준 변수를 추가 합니다 **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. 동일한 파일을 업데이트 합니다 **didRegisterForRemoteNotificationsWithDeviceToken** 초기화 하는 함수는 **NotificationRegistrationService** 호출한 다음 확인 하 고 필수 매개 변수를 사용 하 여를 **등록** 함수입니다.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    간단히 말하자면, print 문은 가지를 사용 하 여 결과 사용 하 여 출력 창 업데이트 하려는 합니다 **등록** 작업 합니다.

1. 이제 빌드 및 물리적 장치에서 앱을 실행 합니다. "Registered" 출력 창에 표시 됩니다.

## <a name="test-the-solution"></a>솔루션 테스트

이 단계에서이 앱을 등록 **NotificationHub** 푸시 알림을 받을 수 있습니다. Xcode에서 디버거를 중지 하 고 현재 실행 중인 경우 앱을 닫습니다. 다음으로 확인 합니다 **장치 설치** 푸시 알림 세부 정보는 예상 대로 및 앱 받을 수 있습니다.  

### <a name="verify-the-device-installation"></a>장치 설치를 확인 합니다.

사용 하 여 이전에 수행한 것 처럼 현재 같은 요청을 만들 수 있는 **Postman** 에 대 한 [SAS 토큰을 확인 하](#verify-the-sas-token)합니다. SAS 토큰 만료 되지 않았으면, 가정 응답 템플릿 등 태그에 제공 하 고 설치 정보를 이제 포함 되어야 합니다.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>(Azure portal) 테스트 알림 보내기

이제 알림을 받을 수 있습니다를 테스트 하는 가장 빠른 방법은 다음과 같습니다. Azure portal에서 알림 허브로 이동 하려면

1. Azure portal에서 찾은 합니다 **개요** 알림 허브에서 탭 합니다.

1. 선택 **테스트 보내기**, 위에 합니다 **Essentials** 포털 창의 왼쪽 위 요약:

    ![Notification Hubs Essentials 요약의 테스트 보내기 단추](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. 선택할 **사용자 지정 템플릿을** 에서 합니다 **플랫폼** 목록입니다.

1. 입력 **12345** 에 대 한 합니다 **태그 식으로 보내기**합니다. 이 태그는 설치에서 이전에 지정한 했습니다.

1. 필요에 따라 편집 합니다 **메시지** JSON 페이로드의:

    ![Notification Hubs 테스트 보내기](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. **보내기**를 선택합니다. 포털에는 알림이 장치에 성공적으로 전송 된 여부를 나타내야 합니다.

    ![Notification Hubs 테스트 보내기 결과](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    앱이 포그라운드에서 실행 되지 가정에 알림이 표시 해야 합니다 **알림 센터** 장치의 합니다. 알림을 탭 앱을 열고 하 고 경고를 표시 해야 합니다.

    ![알림이 수신 예제](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>테스트 알림을 (메일 통신 회사)

통해 알림을 보낼 수 있습니다 합니다 [REST API](/rest/api/notificationhubs/) 를 사용 하 여 **Postman**, 더 편리 하 게 테스트할 수 있습니다.

1. 새 탭을 엽니다 **Postman**합니다.

1. 요청을 설정 **POST**, 다음 주소를 입력 합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 요청 헤더를 다음과 같이 구성 합니다.

   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | 콘텐츠 형식                   | application/json;charset=utf-8 |
   | 권한 부여                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | 태그들                           | "12345"                        |

1. 요청을 구성 **본문** 사용 하려면 **RAW-JSON (application.json)** 다음 JSON 페이로드를 사용 하 여:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 선택 합니다 **코드** 아래에 있는 단추를 **저장** 창의 오른쪽 상단에서 단추. 다음 예제 요청 유사 합니다.

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. 선택 된 **보낼** 단추입니다.

성공 상태 코드 및 클라이언트 장치에 알림이 표시 됩니다.

## <a name="next-steps"></a>다음 단계
이제을 통해 알림 허브에 연결 하는 기본적인 iOS Swift 앱은 [REST API](/rest/api/notificationhubs/) 및 보내고 알림을 받을 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업용 notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Notification Hubs SDK GitHub에서](https://github.com/Azure/azure-notificationhubs)
- [응용 프로그램 백 엔드를 사용 하 여 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 사용](notification-hubs-tags-segment-push-message.md) 
- [사용자 지정 템플릿 사용](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명 사용한 Service Bus 액세스 제어](../service-bus-messaging/service-bus-sas.md)
- [프로그래밍 방식으로 SAS 토큰을 생성 합니다.](/rest/api/eventhub/generate-sas-token)
- [Apple 보안: 일반 암호화](https://developer.apple.com/security/)
- [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
