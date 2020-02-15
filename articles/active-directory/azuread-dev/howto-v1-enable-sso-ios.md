---
title: ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정하는 방법 | Microsoft Docs
description: ADAL SDK의 기능을 사용하여 애플리케이션에서 Single Sign On을 활성화하는 방법입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 7ea65b64e5a812b717f065c1d8cc6208e0c0ba69
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164566"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>방법: ADAL을 사용하여 iOS에서 앱 간 SSO를 사용하도록 설정

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SSO(Single Sign-On)를 사용하면 사용자가 자격 증명을 한 번만 입력하고 게시자에 관계없이 해당 자격 증명이 애플리케이션 전체 및 다른 애플리케이션이 사용할 수 있는 플랫폼 전체에서 자동으로 적용되도록 할 수 있습니다(예: Microsoft 계정 또는 Microsoft 365의 회사 계정).

Microsoft의 ID 플랫폼을 SDK와 함께 사용하여 사용자 고유의 앱 제품군 내에서 또는 broker 기능 및 Authenticator 애플리케이션과 함께 사용하여 전체 디바이스에서 SSO를 쉽게 사용하도록 설정할 수 있습니다.

이 방법에서는 고객에게 SSO를 제공하도록 애플리케이션 내에서 SDK를 구성하는 방법을 알아보겠습니다.

이 방법은 다음에 적용됩니다.

* Azure Active Directory(Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory 조건부 액세스

## <a name="prerequisites"></a>사전 요구 사항

이 방법에서는 다음 작업을 수행하는 방법을 알고 있다고 가정합니다.

* Azure AD에 대한 레거시 포털을 사용하여 앱 프로비전 자세한 내용은 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 을 참조 하세요.
* [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc)와 애플리케이션 통합

## <a name="single-sign-on-concepts"></a>Single Sign-On 개념

### <a name="identity-brokers"></a>ID broker

Microsoft는 다른 공급업체의 애플리케이션 전체에서 자격 증명의 브리징을 허용하고 자격 증명의 유효성을 검사할 단일 보안 위치가 필요한 향상된 기능을 허용하는 애플리케이션을 모든 모바일 플랫폼에 대해 제공합니다. 이를 **broker**라고 합니다.

iOS 및 Android에서 broker는 고객이 독립적으로 설치하는 다운로드 가능한 애플리케이션을 통해 제공되거나 해당 직원에 대한 일부 또는 모든 디바이스를 관리하는 회사에 의해 디바이스에 푸시됩니다. broker는 IT 관리자 구성에 따라 일부 애플리케이션에 대해서만 또는 전체 디바이스에 대한 보안 관리를 지원합니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>모바일 디바이스에 로그인하기 위한 패턴

디바이스에서 자격 증명에 대한 액세스는 두 가지 기본 패턴을 따릅니다.

* 비 브로커 지원 로그인
* 브로커 지원 로그인

#### <a name="non-broker-assisted-logins"></a>비 브로커 지원 로그인

비 브로커 지원 로그인은 애플리케이션과 함께 인라인을 발생하고 해당 애플리케이션에 대한 디바이스에서 로컬 스토리지를 사용하는 로그인 환경입니다. 이 스토리지는 애플리케이션 간에 공유될 수 있지만 자격 증명은 해당 자격 증명을 사용하는 앱 또는 앱의 제품군에 밀접하게 바인딩됩니다. 애플리케이션 자체 내에서 사용자 이름 및 암호를 입력하는 경우 많은 모바일 애플리케이션에서 이를 경험할 가능성이 가장 큽니다.

이러한 로그인은 다음과 같은 이점이 있습니다.

* 사용자 환경은 애플리케이션 내에 완전히 존재합니다.
* 애플리케이션 제품군에 Single Sign-On 환경을 제공하여 동일한 인증서로 서명하는 애플리케이션에서 자격 증명을 공유할 수 있습니다.
* 로그인 환경 주위의 제어는 로그인 이전 및 이후에 애플리케이션에 제공됩니다.

이러한 로그인은 다음과 같은 단점이 있습니다.

* 사용자는 Microsoft ID를 사용하는 모든 앱이 아닌 애플리케이션이 구성한 해당 Microsoft ID 간에서만 Single Sign-On을 경험할 수 있습니다.
* 조건부 액세스와 같은 고급 비즈니스 기능과 함께 애플리케이션을 사용하거나 Intune 제품군을 사용할 수 없습니다.
* 애플리케이션은 비즈니스 사용자에 대한 인증서 기반 인증을 지원할 수 없습니다.

SDK가 애플리케이션의 공유 스토리지와 작업하여 SSO를 활성화하는 방법에 대한 표현은 다음과 같습니다.

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>브로커 지원 로그인

broker 지원 로그인은 broker 애플리케이션 내에서 발생하고, broker의 스토리지와 보안을 사용하여 ID 플랫폼을 적용하는 디바이스의 모든 애플리케이션에서 자격 증명을 공유하는 로그인 환경입니다. 즉, 애플리케이션은 사용자가 로그인하도록 브로커를 사용합니다. iOS 및 Android에서 해당 브로커는 고객이 독립적으로 설치하거나 사용자에 대한 디바이스를 관리하는 회사에서 디바이스에 푸시할 수 있는 다운로드 가능한 애플리케이션을 통해 제공됩니다. 이 애플리케이션 유형의 예는 iOS에서 Microsoft Authenticator 애플리케이션입니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다.

환경은 플랫폼별로 다르며 올바르게 관리되지 않는 경우 사용자에게 작업 중단이 발생할 수 있습니다. Facebook 애플리케이션을 설치하고 다른 애플리케이션에서 Facebook Connect를 사용하는 경우 아마도 이 패턴과 가장 친숙할 것입니다. ID 플랫폼은 동일한 패턴을 사용합니다.

iOS의 경우 이는 Microsoft Authenticator 애플리케이션이 사용자가 로그인하려는 계정을 선택하도록 포그라운드로 오는 동안 애플리케이션이 백그라운드로 전송되는 "전환" 애니메이션이 됩니다.

Android 및 Windows의 경우 계정 선택기가 사용자에게 덜 방해가 되는 애플리케이션 맨 위에 표시됩니다.

#### <a name="how-the-broker-gets-invoked"></a>브로커를 호출하는 방법

호환되는 broker가 디바이스에 설치된 경우, Microsoft Authenticator 애플리케이션과 마찬가지로 SDK는 사용자가 ID 플랫폼의 계정을 사용하여 로그인하려는 것을 나타내는 경우 broker 호출 작업을 자동으로 수행합니다. 이 계정은 개인 Microsoft 계정, 회사 또는 학교 계정 또는 B2C 및 B2B 제품을 사용하여 Azure에 제공 및 호스트하는 계정일 수 있습니다.

#### <a name="how-we-ensure-the-application-is-valid"></a>애플리케이션이 유효한지 확인하는 방법

브로커를 호출하는 애플리케이션의 ID를 확인하는 것은 브로커 지원 로그인에 제공하는 보안에 중요합니다. iOS와 Android 모두 제공된 애플리케이션에만 유효한 고유한 식별자를 적용하지 않으므로 악의적 애플리케이션은 합법적인 애플리케이션의 ID를 "스푸핑"하고 합법적인 애플리케이션을 의미하는 토큰을 받을 수도 있습니다. 런타임 시 적절한 애플리케이션으로 항상 통신하고 있음을 확인하기 위해 Microsoft에 해당 애플리케이션을 등록할 때 개발자에게 사용자 지정 redirectURI를 요청합니다. 개발자가 이 리디렉션 URI를 만드는 방법은 아래에 자세히 설명되어 있습니다. 이 사용자 지정 redirectURI는 애플리케이션의 번들 ID를 포함하고 Apple 앱 스토어에서 애플리케이션에 고유하도록 보장됩니다. 애플리케이션이 브로커를 호출하면 브로커는 iOS 운영 체제에 브로커를 호출한 번들 ID를 제공하도록 요청합니다. 브로커는 ID 시스템에 대한 호출에서 Microsoft에 이 번들 ID를 제공합니다. 애플리케이션의 번들 ID가 등록하는 동안 개발자가 제공한 번들 ID와 일치하지 않는 경우 애플리케이션이 요청하는 리소스를 위한 토큰에 대한 액세스를 거부합니다. 이러한 확인을 통해 개발자가 등록한 애플리케이션만 토큰을 받습니다.

**개발자는 SDK가 브로커를 호출할지 비 브로커 지원 흐름을 사용할지 여부를 선택할 수 있습니다.** 그러나 개발자가 브로커 지원 흐름을 사용하지 않도록 선택하는 경우 사용자가 디바이스에 이미 추가했으며 조건부 액세스, Intune 관리 기능 및 인증서 기반 인증과 같은 Microsoft가 고객에게 제공하는 비즈니스 기능으로 애플리케이션이 사용되는 것을 예방하는 SSO 자격 증명 사용의 이점을 잃게 됩니다.

이러한 로그인은 다음과 같은 이점이 있습니다.

* 사용자는 공급 업체에 관계 없이 모든 애플리케이션에서 SSO를 경험합니다.
* 애플리케이션은 조건부 액세스와 같은 고급 비즈니스 기능을 사용하거나 Intune 제품군을 사용할 수 있습니다.
* 애플리케이션은 비즈니스 사용자에 대한 인증서 기반 인증을 지원할 수 있습니다.
* 애플리케이션 및 사용자의 ID로서 더 안전한 로그인 환경은 추가 보안 알고리즘 및 암호화로 브로커 애플리케이션에 의해 확인됩니다.

이러한 로그인은 다음과 같은 단점이 있습니다.

* iOS에서 사용자는 자격 증명을 선택하는 동안 애플리케이션의 환경 밖으로 전환됩니다.
* 애플리케이션 내에서 고객에 대한 로그인 환경을 관리하는 기능이 손실됩니다.

SDK가 broker 애플리케이션과 함께 작동하여 SSO를 사용하도록 설정하는 방법은 다음과 같습니다.

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>ADAL을 사용하여 앱 간 SSO 활성화

여기에서 ADAL iOS SDK를 사용하여 다음 작업을 수행합니다.

* 앱 제품군에 대한 비 브로커 지원 SSO 설정
* 브로커 지원 SSO에 대한 지원 설정

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>비 브로커 지원 SSO에 대한 SSO 설정

애플리케이션에서 비 브로커 지원 SSO의 경우 SDK는 SSO의 복잡성을 관리합니다. 캐시에서 올바른 사용자를 찾고 쿼리할 로그인된 사용자의 목록을 유지 관리하는 것을 포함합니다.

소유하고 있는 애플리케이션에서 SSO를 활성화하려면 다음을 수행해야 합니다.

1. 모든 응용 프로그램이 동일한 클라이언트 ID 또는 응용 프로그램 ID를 사용 하는지 확인 합니다.
2. 모든 애플리케이션이 키 집합을 공유할 수 있도록 Apple에서 동일한 서명 인증서를 공유하는지 확인합니다.
3. 각 애플리케이션에 대한 동일한 키 집합 권한 부여를 요청합니다.
4. SDK에 사용했으면 하는 공유 키 집합을 제공합니다.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>앱 제품군에서 모든 애플리케이션에 대한 동일한 클라이언트 ID / 애플리케이션 ID 사용

ID 플랫폼이 애플리케이션에서 토큰을 공유하도록 허용하는지 알려면 각 애플리케이션은 동일한 클라이언트 ID 또는 애플리케이션 ID를 공유해야 합니다. 포털에 첫 번째 애플리케이션을 등록했던 경우에 제공된 고유 식별자입니다.

리디렉션 URI를 통해 동일한 애플리케이션 ID를 사용하는 경우 다른 앱을 Microsoft ID 서비스에 식별할 수 있습니다. 각 애플리케이션에는 등록 포털에 등록한 여러 개의 리디렉션 URI가 있을 수 있습니다. 제품의 각 앱은 다른 리디렉션 URI를 갖습니다. 표시되는 모양의 예는 다음과 같습니다.

App1 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 리디렉션 URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

이는 동일한 클라이언트 ID / 애플리케이션 ID 아래에 중첩되며 SDK 구성에서 우리에게 반환하는 리디렉션 URI에 따라 조회됩니다.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

이러한 리디렉션 URI의 형식은 아래에 설명되어 있습니다. 브로커를 지원하려고 하는 한 모든 리디렉션 URI를 사용할 수 있습니다. 이 경우 위와 같이 표시되어야 합니다.*

#### <a name="create-keychain-sharing-between-applications"></a>애플리케이션 간에 공유되는 키 집합 만들기

키 집합 공유 활성화는 이 문서의 범위를 벗어나며 Apple에서 해당 문서 [기능 추가](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html)에 포함됩니다. 중요한 것은 사용자가 키 집합이 불리는 이름을 결정하는 것과 모든 애플리케이션에 해당 기능을 추가하는 것입니다.

올바르게 설정된 권한 부여를 가진 경우 다음처럼 보이는 항목이 포함된 `entitlements.plist` 의 자격 있는 프로젝트 디렉터리에 파일이 보입니다.

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

각 응용 프로그램에서 키 집합 자격을 사용 하도록 설정 하 고 SSO를 사용할 준비가 되 면 다음 설정을 사용 하 여 `ADAuthenticationSettings`에서 다음 설정을 사용 하 여 id SDK에 키 집합을 알려 줍니다.

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> 애플리케이션에서 키 집합을 공유하는 경우 모든 애플리케이션은 사용자를 삭제하거나 더 심한 경우 애플리케이션에서 모든 토큰을 삭제할 수 있습니다. 백그라운드 작업을 하기 위해 토큰을 사용하는 애플리케이션이 있는 경우에 특히 미치는 영향이 커집니다. 키 집합을 공유하면 ID SDK를 통한 모든 제거 작업에 특히 주의해야 합니다.

이것으로 끝입니다. 이제 SDK는 모든 애플리케이션에서 자격 증명을 공유합니다. 사용자 목록도 애플리케이션 인스턴스 간에 공유됩니다.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>브로커 지원 SSO에 대한 SSO 설정

디바이스에 설치되어 있는 모든 브로커를 사용하는 애플리케이션에 대한 기능은 **기본적으로 해제되어**있습니다. 브로커와 함께 애플리케이션을 사용하려면 몇 가지 추가 구성을 수행하고 애플리케이션에 코드를 추가해야 합니다.

수행할 단계는 다음과 같습니다.

1. MS SDK에 대한 애플리케이션 코드의 호출에서 브로커 모드를 활성화합니다.
2. 새 리디렉션 URI를 설정하고 앱과 앱 등록에 이를 제공합니다.
3. URL 구성표 등록.
4. info.plist 파일에 권한을 추가합니다.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1단계: 애플리케이션에서 브로커 모드 활성화

"컨텍스트" 또는 인증 개체의 초기 설정을 만들 때 브로커를 사용하는 애플리케이션에 대한 기능은 설정되어 있습니다. 코드에서 자격 증명 형식을 설정하여 이 작업을 수행합니다.

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO` 설정을 사용하면 SDK는 브로커에 호출하려고 시도할 수 있으며 `AD_CREDENTIALS_EMBEDDED`는 SDK가 브로커에 호출할 수 없도록 합니다.

#### <a name="step-2-registering-a-url-scheme"></a>2단계: URL 구성표 등록

ID 플랫폼은 URL을 사용하여 브로커를 호출한 다음, 애플리케이션에 제어를 다시 반환합니다. 해당 왕복을 완료하려면 ID 플랫폼이 아는 애플리케이션에 등록된 URL 구성표가 필요합니다. 이전에 애플리케이션에 이미 등록했을 수 있는 다른 앱 구성표 외에도 될 수 있습니다.

> [!WARNING]
> 다른 앱이 동일한 URL 구성표를 사용할 가능성을 최소화하기 위해 URL 구성표를 매우 고유하게 만드는 것이 좋습니다. Apple은 앱 스토어에 등록된 URL 구성표의 고유성을 적용하지 않습니다.

다음은 프로젝트 구성에 나타나는 예입니다. 또한 XCode에서도 이를 수행할 수 있습니다.

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>3단계: URL 구성표와 함께 새 리디렉션 URI 설정

올바른 애플리케이션에 자격 증명 토큰을 항상 반환하는지 확인하려면 iOS 운영 체제에서 확인할 수 있는 방식으로 애플리케이션에 다시 호출하는지 확인해야 합니다. iOS 운영 체제는 호출하는 애플리케이션의 번들 ID를 Microsoft 브로커 애플리케이션에 보고합니다. 불량 애플리케이션에서 이를 스푸핑할 수 없습니다. 따라서 토큰이 올바른 애플리케이션에 반환되는지 확인하도록 브로커 애플리케이션의 URI와 함께 활용합니다. 이 고유한 리디렉션 URI를 애플리케이션 및 개발자 포털에서 리디렉션 URI로 설정해야 합니다.

리디렉션 URI는 다음의 적절한 형식이어야 합니다.

`<app-scheme>://<your.bundle.id>`

예: *x-msauth-mytestiosapp://com.myapp.mytestapp*

[Azure Portal](https://portal.azure.com/)을 사용하여 앱 등록에 이 리디렉션 URI를 지정해야 합니다. Azure AD 앱 등록에 대한 자세한 내용은 [Azure Active Directory와 통합](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>3a단계: 인증서 기반 인증을 지원하도록 앱 및 개발자 포털에 리디렉션 URI 추가

인증서 기반 인증을 지원하기 위해 애플리케이션에 해당 지원을 추가하려는 경우 두 번째 “msauth”를 애플리케이션 및 [Azure Portal](https://portal.azure.com/)에 등록하여 인증서 인증을 처리해야 합니다.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

예: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>4단계: 앱에 구성 매개 변수 추가

ADAL은 canOpenURL을 사용하여 브로커가 디바이스에 설치되어 있는지 확인합니다. iOS 9에서 Apple은 애플리케이션에서 쿼리할 수 있는 구성표를 잠갔습니다. `info.plist file`의 LSApplicationQueriesSchemes 섹션에 "Msauth"를 추가해야 합니다.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>SSO를 구성했습니다.

이제 ID SDK는 애플리케이션 전체에서 자동으로 자격 증명을 공유하고, 디바이스에 있는 경우 broker를 호출합니다.

## <a name="next-steps"></a>다음 단계

* [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)에 대해 자세히 알아보기
