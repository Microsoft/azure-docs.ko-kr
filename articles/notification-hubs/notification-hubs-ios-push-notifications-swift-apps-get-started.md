---
title: Azure Notification Hubs을 사용 하는 Swift iOS 앱에 알림 푸시 Microsoft Docs
description: Azure Notification Hubs을 사용 하는 Swift iOS 앱에 알림을 푸시하는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: femila
editor: jwargo
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.reviewer: jowargo
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 8dae5bcc082ba5dd0953e3e97f609e4031547a35
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030648"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>자습서: Notification Hubs REST API를 사용 하는 Swift iOS 앱에 알림 푸시

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

이 자습서에서는 [REST API](/rest/api/notificationhubs/)를 사용 하 여 Swift 기반 iOS 응용 프로그램에 알림을 푸시하는 Azure Notification Hubs를 사용 합니다. 또한 [APNs (Apple Push Notification service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1)를 사용 하 여 푸시 알림을 받는 빈 iOS 앱을 만듭니다.

이 자습서에서는 다음 단계를 수행 합니다.

> [!div class="checklist"]
> * 인증서 서명 요청 파일을 생성 합니다.
> * 푸시 알림을 위해 앱을 요청 합니다.
> * 앱에 대 한 프로 비전 프로필을 만듭니다.
> * 알림 허브를 만듭니다.
> * APNs 정보를 사용 하 여 알림 허브를 구성 합니다.
> * IOS 앱을 알림 허브에 연결 합니다.
> * 솔루션을 테스트 합니다.

## <a name="prerequisites"></a>선행 조건

필수 조건을 충족하려면 다음이 필요합니다.

- 서비스에 익숙하지 않은 경우 [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md) 를 참조 하세요.
- [등록 및 설치](notification-hubs-push-notification-registration-management.md)에 대해 자세히 알아보세요.
- 활성 [Apple 개발자 계정](https://developer.apple.com).
- 키 집합에 설치 된 유효한 개발자 인증서와 함께 Xcode를 실행 하는 Mac입니다.
- 시뮬레이터를 사용 하 여 푸시 알림을 테스트할 수 없으므로를 실행 하 고 디버그할 수 있는 실제 iPhone 장치입니다.
- [Apple 포털](https://developer.apple.com) 에 등록 되어 있고 인증서와 연결 된 실제 iPhone 장치입니다.
- 리소스를 만들고 관리할 수 있는 [Azure 구독](https://portal.azure.com) .

이전에 iOS 개발 경험이 없는 경우에도이 첫 번째 원칙 예제를 만들기 위한 단계를 따를 수 있습니다. 그러나 다음 개념에 대해 잘 알고 있어야 합니다.

- Xcode 및 Swift를 사용 하 여 iOS 앱을 빌드하는 중입니다.
- IOS에 대 한 [Azure 알림 허브](notification-hubs-ios-apple-push-notification-apns-get-started.md) 구성.
- [Apple 개발자 포털](https://developer.apple.com) 및 [Azure Portal](https://portal.azure.com)입니다.

> [!NOTE]
> 알림 허브는 **Sandbox** 인증 모드만 사용 하도록 구성 됩니다. 프로덕션 작업에는이 인증 모드를 사용 하지 않아야 합니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>알림 허브에 iOS 앱 연결

이 섹션에서는 알림 허브에 연결 되는 iOS 앱을 빌드합니다.  

### <a name="create-an-ios-project"></a>IOS 프로젝트 만들기

1. Xcode에서 새 iOS 프로젝트를 만들고 **애플리케이션 단일 보기** 템플릿을 선택합니다.

1. 새 프로젝트에 대 한 옵션을 설정 하는 경우:

   1. Apple 개발자 포털에서 **번들 식별자** 를 설정할 때 사용한 **제품 이름** (Pushdemo) 및 **조직 식별자** (`com.<organization>`)를 지정 합니다.

   1. **앱 ID** 가 설정 된 **팀** 을 선택 합니다.

   1. **언어** 를 **Swift**로 설정 합니다.

   1. **다음**을 선택합니다.

1. **Supportingfiles**라는 새 폴더를 만듭니다.

1. **Supportingfiles** 폴더에 **info.plist** 라는 새 p 목록 파일을 만듭니다. Git 리포지토리로 작업할 때 커밋되지 않도록 **.gitignore** 파일에이 폴더를 추가 해야 합니다. 프로덕션 앱에서 이러한 암호를 자동화 된 빌드 프로세스의 일부로 조건부로 설정 하는 경우가 많습니다. 이러한 설정은이 연습에서 다루지 않습니다.

1. 프로 비전 한 알림 허브의 고유한 값을 사용 하 여 다음 구성 항목을 포함 하도록 info.plist를 업데이트 **합니다** .

   | 키                            | 형식                     | 값                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | 문자열                   | \<hubKey>                  |
   | notificationHubKeyName         | 문자열                   | \<hubKeyName>              |
   | notificationHubName            | 문자열                   | \<hubName>                 |
   | notificationHubNamespace       | 문자열                   | \<hubNamespace>            |

   Azure Portal에서 알림 허브 리소스로 이동 하 여 필요한 값을 찾을 수 있습니다. 특히 **notificationHubName** 및 **NotificationHubNamespace** 값은 **개요** 페이지 내에서 **Essentials** 요약의 오른쪽 위 모퉁이에 있습니다.

   ![Notification Hubs Essentials 요약](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   **NotificationHubKeyName** 및 **NotificationHubKey** 값은 **액세스 정책** 으로 이동 하 고 해당 **액세스 정책**(예: `DefaultFullSharedAccessSignature`)을 선택 하 여 찾을 수도 있습니다. 그런 다음 `notificationHubKeyName`에 대 한 `SharedAccessKeyName=` 접두사가 접두사로 추가 된 값을 **기본 연결 문자열** 에서 복사 하 고 `notificationHubKey`에 대 한 `SharedAccessKey=` 접두사가 접두사로 추가 합니다.

   연결 문자열은 다음 형식 이어야 합니다.

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   간단 하 게 유지 하려면 토큰을 사용 하 여 알림을 보낼 수 있도록 `DefaultFullSharedAccessSignature`를 지정 합니다. 실제로 `DefaultListenSharedAccessSignature` 알림을 받으려는 경우에는이 옵션을 선택 하는 것이 좋습니다.

1. **프로젝트 탐색기**에서 **프로젝트 이름을** 선택한 다음 **일반** 탭을 선택 합니다.

1. **Id** 를 찾은 다음 **번들 식별자** 값이 이전 단계의 **앱 ID** 에 사용 되는 값인 `com.<organization>.PushDemo`와 일치 하도록 설정 합니다.

1. **서명**을 찾은 다음 **Apple Developer 계정**에 적절 한 **팀** 을 선택 합니다. **팀** 값은 인증서와 프로필을 생성 한 값과 일치 해야 합니다.

1. Xcode는 **번들 식별자**를 기반으로 적절 한 **프로 비전 프로필** 값을 자동으로 끌어옵니다. 새 **프로 비전 프로필** 값이 표시 되지 않는 경우 **Xcode** > **기본 설정** > **계정** > **자세히 보기**를 선택 하 여 **서명 id** 에 대 한 프로필을 새로 고쳐 보세요. **서명 id**를 선택 하 고 오른쪽 아래에 있는 **새로 고침** 단추를 선택 하 여 프로필을 다운로드 합니다.

1. **기능** 탭을 선택 하 고 **푸시 알림이** 활성화 되어 있는지 확인 합니다.

1. **AppDelegate swift** 파일을 열어 **Unusernotificationcenter 대리자** 프로토콜을 구현 하 고 클래스의 맨 위에 다음 코드를 추가 합니다.

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

    이러한 멤버는 나중에 사용 합니다. 특히 등록의 일부로 **태그** 및 **generictemplate** 멤버를 사용 합니다. 태그에 대 한 자세한 내용은 등록 및 [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md) [에 대 한 태그](notification-hubs-tags-segment-push-message.md) 를 참조 하세요.

1. 동일한 파일에서 **didFinishLaunchingWithOptions** 함수에 다음 코드를 추가 합니다.

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

    이 코드는 **info.plist**에서 설정 값을 검색 하 고, **AppDelegate** 클래스를 **unusernotificationcenter** 대리자로 설정 하 고, 푸시 알림에 대 한 권한 부여를 요청 하 고, **registerForRemoteNotifications**를 호출 합니다.

    이 코드는 간단 하 게 유지 하기 위해 *iOS 10 이상만*지원 합니다. 일반적인 방법으로 각 Api 및 방법을 사용 하 여 조건에 따라 이전 OS 버전에 대 한 지원을 추가할 수 있습니다.

1. 동일한 파일에 다음 함수를 추가 합니다.

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

    이 코드는 **installationId** 및 **pushchannel** 값을 사용 하 여 알림 허브에 등록 합니다. 이 경우 **identifierForVendor** 를 사용 하 여 장치를 식별 하는 고유 값을 제공한 다음 **deviceToken** 를 포맷 하 여 원하는 **pushchannel** 값을 제공 합니다. **Showalert** 함수는 데모용으로 메시지 텍스트를 표시 하는 데만 존재 합니다.

1. **AppDelegate swift** 파일에서 **willPresent** 및 **DidReceive** 함수를 **unusernotificationcenter 대리자**에 추가 합니다. 이러한 함수는 응용 프로그램이 포그라운드 또는 백그라운드에서 각각 실행 되 고 있음을 알리는 경고를 표시 합니다.

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

1. **DidRegisterForRemoteNotificationsWithDeviceToken** 함수 아래쪽에 print 문을 추가 하 여 **InstallationId** 및 **pushchannel** 에 값이 할당 되었는지 확인 합니다.

1. 나중에 프로젝트에 추가할 기본 구성 요소에 대 한 **모델**, **서비스**및 **유틸리티** 폴더를 만듭니다.

1. 프로젝트가 물리적 장치에서 빌드되고 실행 되는지 확인 합니다. 시뮬레이터를 사용 하 여 푸시 알림을 테스트할 수 없습니다.

### <a name="create-models"></a>모델 만들기

이 단계에서는 [Notification Hubs REST API](/rest/api/notificationhubs/) 페이로드를 나타내고 필요한 SAS (공유 액세스 서명) 토큰 데이터를 저장 하는 모델 집합을 만듭니다.

1. **Swift** 라는 새 Swift 파일을 **모델** 폴더에 추가 합니다. 이 모델은 **Deviceinstallation** 페이로드의 일부로 개별 템플릿의 **본문** 을 나타내는 구조체를 제공 합니다.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Swift 라는 새 Swift 파일을 **모델** 폴더에 추가 합니다 **.** 이 파일은 **장치 설치**를 만들거나 업데이트 하기 위한 페이로드를 나타내는 구조체를 정의 합니다. 파일에 다음 코드를 추가합니다.

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

1. **TokenData Swift** 라는 새 Swift 파일을 **모델** 폴더에 추가 합니다. 이 모델은 SAS 토큰을 만료와 함께 저장 하는 데 사용 됩니다.

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

Azure Service Bus와 동일한 보안 인프라를 사용 Notification Hubs 합니다. REST API를 호출 하려면 요청의 **권한 부여** 헤더에 사용할 수 있는 [SAS 토큰을 프로그래밍 방식으로 생성](/rest/api/eventhub/generate-sas-token) 해야 합니다.  

결과 토큰은 다음과 같은 형식입니다.

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

프로세스 자체에는 다음과 같은 6 가지 주요 단계가 포함 됩니다.  

1. [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time) 형식의 만료 시간을 계산 하는 것은 1970 년 1 월 1 일 자정 utc 이후 경과 된 시간 (초)입니다.
1. 액세스 하려고 하는 리소스를 나타내는 **Resourceurl** 의 형식을 지정 합니다 .이 url은 백분율 인코딩 및 소문자입니다. **Resourceurl** 의 형식은 `'https://<namespace>.servicebus.windows.net/<hubName>'`입니다.
1. `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`형식으로 지정 된 **Stringtosign**을 준비 하는 중입니다.
1. **Stringtosign** 값의 HMAC-SHA256 해시를 사용 하 여 **서명을** 계산 하 고 Base64 인코딩을 사용 합니다. 해시 값은 각 **권한 부여 규칙**에 대 한 **연결 문자열** 의 **키** 부분과 함께 사용 됩니다.
1. % 인코딩 되도록 Base64 인코딩된 **서명의** 서식을 지정 합니다.
1. **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**및 **UrlEncodedResourceUrl** 값을 사용 하 여 필요한 형식으로 토큰을 생성 합니다.

공유 액세스 서명에 대 한 자세한 개요와 Azure Service Bus 및 Notification Hubs 사용 하는 방법은 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md) 를 참조 하세요.

이 Swift 예제의 목적을 위해 Apple의 오픈 소스 **CommonCrypto** 라이브러리를 사용 하 여 서명의 해시를 도와 드리겠습니다. C 라이브러리 이기 때문에 Swift에서 액세스할 수 없습니다. 브리징 헤더를 사용 하 여 라이브러리를 사용할 수 있도록 설정할 수 있습니다.

브리징 헤더를 추가 하 고 구성 하려면 다음을 수행 합니다.

1. Xcode에서 **파일** > **새** > **파일** > **헤더 파일**을 선택 합니다. 헤더 파일의 이름을 **BridgingHeader**로 합니다.

1. 파일을 편집 하 여 **CommonHMAC**를 가져옵니다.

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. 브리징 헤더를 참조 하도록 대상의 **빌드 설정을** 업데이트 합니다.

   1. **빌드 설정** 탭을 열고 **Swift 컴파일러** 섹션으로 스크롤합니다.

   1. **설치 목표-C 호환성 헤더** 옵션이 **예**로 설정 되어 있는지 확인 합니다.

   1. **목적-C 브리징 헤더** 옵션에 `'<ProjectName>/BridgingHeader.h'` 파일 경로를 입력 합니다. 이는 브리징 헤더의 파일 경로입니다.

   이러한 옵션을 찾을 수 없는 경우 **기본** 또는 **사용자 지정**이 아닌 **모든** 보기가 선택 되어 있는지 확인 합니다.

   **CommonCrypto** 를 좀 더 쉽게 사용할 수 있는 여러 타사 오픈 소스 래퍼 라이브러리를 사용할 수 있습니다. 그러나 이러한 라이브러리에 대 한 설명은이 문서의 범위를 벗어나는 것입니다.

1. **Utilities** **폴더 내에** Swift 라는 새 파일을 추가 하 고 다음 코드를 추가 합니다.

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

   이 유틸리티는 SAS 토큰 생성을 담당 하는 논리를 캡슐화 합니다.

   앞에서 설명한 대로 **getSasToken** 함수는 토큰을 준비 하는 데 필요한 개략적인 단계를 오케스트레이션 합니다. 함수는이 자습서의 뒷부분에 나오는 설치 서비스에 의해 호출 됩니다.

   다른 두 함수는 **getSasToken** 함수에 의해 호출 됩니다. **sha256HMac** 는 서명 및 **urlEncodedString** 를 계산 하 여 연결 된 URL 문자열을 인코딩합니다. **UrlEncodedString** 함수는 기본 제공 **addingPercentEncoding** 함수를 사용 하 여 필요한 출력을 달성할 수 없기 때문에 필요 합니다.

   [Azure Storage IOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) 는 이러한 작업을 목적-C에서 접근 하는 방법의 좋은 예입니다. Azure Service Bus SAS 토큰에 대 한 자세한 내용은 [Azure Service Bus 설명서](../service-bus-messaging/service-bus-sas.md)에서 찾을 수 있습니다.

### <a name="verify-the-sas-token"></a>SAS 토큰 확인

클라이언트에서 설치 서비스를 구현 하기 전에 선택한 HTTP 유틸리티를 사용 하 여 앱이 SAS 토큰을 올바르게 생성 하는지 확인 합니다. 이 자습서의 목적에 따라 선택 하는 도구는 **Postman**입니다.

적절 한 위치에 배치 된 인쇄 문이나 중단점을 사용 하 여 **installationId** 및 응용 프로그램에서 생성 되는 **토큰** 값을 확인 합니다.

**설치** API를 호출 하려면 다음 단계를 수행 합니다.

1. **Postman**에서 새 탭을 엽니다.

1. **GET** 요청을 설정 하 고 다음 주소를 지정 합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 다음과 같이 요청 헤더를 구성 합니다.

   | 키           | 값            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | 권한 부여 | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. **저장** 단추 오른쪽 위에 표시 되는 **코드** 단추를 선택 합니다. 요청은 다음 예제와 유사 하 게 표시 됩니다.

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. **보내기** 단추를 선택합니다.

이 시점에서 지정 된 **installationId** 에 대 한 등록이 존재 하지 않습니다. 확인 하는 동안 "401 권한 없음" 응답이 아닌 "404 Not Found" 응답이 발생 합니다. 이 결과는 SAS 토큰이 수락 되었는지 확인 해야 합니다.

### <a name="implement-the-installation-service-class"></a>설치 서비스 클래스 구현

그런 다음 [REST API 설치](/rest/api/notificationhubs/create-overwrite-installation)에 대 한 기본 래퍼를 구현 합니다.  

**Services** 폴더 아래에 **notificationregistrationservice** 라는 새 Swift 파일을 추가 하 고이 파일에 다음 코드를 추가 합니다.

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

필수 세부 정보는 초기화의 일부로 제공 됩니다. 태그 및 템플릿은 선택적으로 **register** 함수에 전달 되어 **장치 설치** JSON 페이로드의 일부를 구성 합니다.  

**Register** 함수는 다른 전용 함수를 호출 하 여 요청을 준비 합니다. 응답이 수신 되 면 완료가 호출 되 고 등록이 성공 했는지 여부를 나타냅니다.  

요청 엔드포인트은 **getBaseAddress** 함수에 의해 생성 됩니다. 생성에서는 초기화 중에 제공 된 알림 허브 매개 변수 *네임 스페이스* 와 *이름을* 사용 합니다.  

**GetSasToken** 함수는 현재 저장 된 토큰이 유효한 지 여부를 확인 합니다. 토큰이 유효 하지 않은 경우 함수는 **Tokenutility** 를 호출 하 여 새 토큰을 생성 한 다음 값을 반환 하기 전에 저장 합니다.

마지막으로 **encodeToJson** 는 요청 본문의 일부로 사용 하기 위해 각 모델 개체를 JSON으로 변환 합니다.

### <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs를 호출 REST API

마지막 단계는 **Notificationregistrationservice** 를 사용 하 여 **notificationhub**에 등록 하도록 **AppDelegate** 을 업데이트 하는 것입니다.

1. **AppDelegate swift** 를 열고 클래스 수준 변수를 추가 하 여 **notificationregistrationservice**에 대 한 참조를 저장 합니다.

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. 동일한 파일에서 **didRegisterForRemoteNotificationsWithDeviceToken** 함수를 업데이트 하 여 필수 매개 변수를 사용 하 여 **notificationregistrationservice** 를 초기화 한 다음 **register** 함수를 호출 합니다.

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

    간단 하 게 유지 하기 위해 **출력 작업의** 결과로 출력 창을 업데이트 하는 몇 가지 print 문을 사용 합니다.

1. 이제 물리적 장치에서 앱을 빌드하고 실행 합니다. 출력 창에 "등록 됨"이 표시 됩니다.

## <a name="test-the-solution"></a>솔루션 테스트

이 단계의 앱은 **Notificationhub** 에 등록 되며 푸시 알림을 받을 수 있습니다. Xcode에서 디버거를 중지 하 고 현재 실행 중인 경우 응용 프로그램을 닫습니다. 그런 다음 **장치 설치** 세부 정보가 예상 대로 작동 하 고 앱이 푸시 알림을 받을 수 있는지 확인 합니다.  

### <a name="verify-the-device-installation"></a>장치 설치 확인

이제 [SAS 토큰을 확인](#verify-the-sas-token)하기 위해 **postman** 을 사용 하 여 이전에 했던 것과 동일한 요청을 수행할 수 있습니다. SAS 토큰이 만료 되지 않는다고 가정 하 고, 현재 제공 된 설치 세부 정보 (예: 템플릿 및 태그)를 응답에 포함 해야 합니다.

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

### <a name="send-a-test-notification-azure-portal"></a>테스트 알림 보내기 (Azure Portal)

이제 알림을 받을 수 있는지 테스트 하는 가장 빠른 방법은 Azure Portal에서 알림 허브로 이동 하는 것입니다.

1. Azure Portal에서 알림 허브의 **개요** 탭으로 이동 합니다.

1. 포털 창의 왼쪽 위에서 **Essentials** 요약 위에 있는 **테스트 보내기**를 선택 합니다.

    ![Notification Hubs Essentials 요약 테스트 보내기 단추](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. **플랫폼** 목록에서 **사용자 지정 템플릿** 을 선택 합니다.

1. **태그에 보내기 식**에 **12345** 을 입력 합니다. 이전에이 태그를 설치에서 지정 했습니다.

1. 필요에 따라 JSON 페이로드의 **메시지** 를 편집 합니다.

    ![Notification Hubs 테스트 보내기](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. **보내기**를 선택합니다. 포털에서 알림이 장치에 성공적으로 전송 되었는지 여부를 표시 해야 합니다.

    ![Notification Hubs 테스트 결과 보내기](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    응용 프로그램이 포그라운드에서 실행 되지 않는다고 가정 하면 장치의 **알림 센터** 에도 알림이 표시 되어야 합니다. 알림을 탭 하면 앱이 열리고 경고가 표시 됩니다.

    ![알림 수신 예](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>테스트 알림 보내기 (메일 운송 업체)

**Postman**을 사용 하 여 [REST API](/rest/api/notificationhubs/) 를 통해 알림을 보낼 수 있습니다 .이는 테스트 하는 데 더 편리한 방법입니다.

1. **Postman**에서 새 탭을 엽니다.

1. 요청을 **POST**로 설정 하 고 다음 주소를 입력 합니다.

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 다음과 같이 요청 헤더를 구성 합니다.

   | 키                            | 값                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | 권한 부여                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | 템플릿                       |
   | Tags                           | "12345"                        |

1. 다음 JSON 페이로드를 사용 하 여 **원시 json (응용 프로그램 json)** 을 사용 하도록 요청 **본문** 을 구성 합니다.

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. 창의 오른쪽 위에 있는 **저장** 단추 아래에 있는 **코드** 단추를 선택 합니다. 요청은 다음 예제와 유사 하 게 표시 됩니다.

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

1. **보내기** 단추를 선택합니다.

성공 상태 코드를 받고 클라이언트 장치에서 알림을 받습니다.

## <a name="next-steps"></a>다음 단계
이제 [REST API](/rest/api/notificationhubs/) 를 통해 알림 허브에 연결 된 기본 iOS Swift 앱이 있고 알림을 보내고 받을 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Azure Notification Hubs 개요](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [백 엔드 작업에 대 한 Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub의 Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
- [응용 프로그램 백 엔드에 등록](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [태그 작업](notification-hubs-tags-segment-push-message.md) 
- [사용자 지정 템플릿 사용](notification-hubs-templates-cross-platform-push-messages.md)
- [공유 액세스 서명을 사용 하 여 액세스 제어 Service Bus](../service-bus-messaging/service-bus-sas.md)
- [프로그래밍 방식으로 SAS 토큰 생성](/rest/api/eventhub/generate-sas-token)
- [Apple 보안: 일반적인 암호화](https://developer.apple.com/security/)
- [UNIX Epoch 시간](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
