---
title: ADAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정하는 방법 | Microsoft Docs
description: ADAL SDK의 기능을 사용하여 애플리케이션 전체에서 Single Sign-On을 사용하도록 설정하는 방법입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 37055d9b59d49091261109e3553f99bcc03d8e14
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77164579"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>방법: ADAL을 사용하여 Android에서 앱 간 SSO를 사용하도록 설정

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SSO(Single Sign-On)를 사용하면 사용자가 자격 증명을 한 번만 입력하고 게시자에 관계없이 해당 자격 증명이 애플리케이션 전체 및 다른 애플리케이션이 사용할 수 있는 플랫폼 전체에서 자동으로 적용되도록 할 수 있습니다(예: Microsoft 계정 또는 Microsoft 365의 회사 계정).

Microsoft의 ID 플랫폼을 SDK와 함께 사용하여 사용자 고유의 앱 제품군 내에서 또는 broker 기능 및 Authenticator 애플리케이션과 함께 사용하여 전체 디바이스에서 SSO를 쉽게 사용하도록 설정할 수 있습니다.

이 방법에서는 고객에게 SSO를 제공하도록 애플리케이션 내에서 SDK를 구성하는 방법을 알아보겠습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법에서는 다음 작업을 수행하는 방법을 알고 있다고 가정합니다.

- Azure AD(Azure Active Directory)에 대한 레거시 포털을 사용하여 앱 프로비전. 자세한 내용은 [앱 등록](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) 을 참조 하세요.
- [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android)와 애플리케이션 통합

## <a name="single-sign-on-concepts"></a>Single Sign-On 개념

### <a name="identity-brokers"></a>ID broker

Microsoft는 다른 공급업체의 애플리케이션 전체에서 자격 증명의 브리징을 허용하고 자격 증명의 유효성을 검사할 단일 보안 위치가 필요한 향상된 기능을 허용하는 애플리케이션을 모든 모바일 플랫폼에 대해 제공합니다. 이를 **broker**라고 합니다.

iOS 및 Android에서 broker는 고객이 독립적으로 설치하는 다운로드 가능한 애플리케이션을 통해 제공되거나 해당 직원에 대한 일부 또는 모든 디바이스를 관리하는 회사에 의해 디바이스에 푸시됩니다. broker는 IT 관리자 구성에 따라 일부 애플리케이션에 대해서만 또는 전체 디바이스에 대한 보안 관리를 지원합니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다.

#### <a name="broker-assisted-login"></a>broker 지원 로그인

broker 지원 로그인은 broker 애플리케이션 내에서 발생하고, broker의 스토리지와 보안을 사용하여 ID 플랫폼을 적용하는 디바이스의 모든 애플리케이션에서 자격 증명을 공유하는 로그인 환경입니다. 애플리케이션은 사용자가 로그인할 수 있도록 브로커를 사용합니다. iOS 및 Android에서 해당 브로커는 고객이 독립적으로 설치하거나 사용자에 대한 디바이스를 관리하는 회사에서 디바이스에 푸시할 수 있는 다운로드 가능한 애플리케이션을 통해 제공됩니다. 이 애플리케이션 유형의 예는 iOS에서 Microsoft Authenticator 애플리케이션입니다. Windows에서 이 기능은 기술적으로 웹 인증 브로커로 알려진 운영 체제에 기본적으로 제공된 계정 선택기로 제공됩니다.
환경은 플랫폼별로 다르며 올바르게 관리되지 않는 경우 사용자에게 작업 중단이 발생할 수 있습니다. Facebook 애플리케이션을 설치하고 다른 애플리케이션에서 Facebook Connect를 사용하는 경우 아마도 이 패턴과 가장 친숙할 것입니다. ID 플랫폼은 동일한 패턴을 사용합니다.

Android에서 계정 선택기는 사용자에게 덜 방해가 되는 애플리케이션 맨 위에 표시됩니다.

#### <a name="how-the-broker-gets-invoked"></a>브로커를 호출하는 방법

호환되는 broker가 디바이스에 설치된 경우, Microsoft Authenticator 애플리케이션과 마찬가지로 Microsoft ID SDK는 사용자가 ID 플랫폼의 계정을 사용하여 로그인하려는 것을 나타내는 경우 broker 호출 작업을 자동으로 수행합니다.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Microsoft가 애플리케이션이 유효한지 확인하는 방식

브로커를 호출하는 애플리케이션의 ID를 확인하는 것은 브로커 지원 로그인에서 제공된 보안에 중요합니다. iOS와 Android는 제공된 애플리케이션에만 유효한 고유한 식별자를 적용하지 않으므로 악의적 애플리케이션은 합법적인 애플리케이션의 ID를 “스푸핑”하고 합법적인 애플리케이션을 의미하는 토큰을 받을 수 있습니다. 런타임 시 Microsoft가 적절한 애플리케이션과 항상 통신하고 있음을 확인하려면 Microsoft에 해당 애플리케이션을 등록할 때 개발자에게 사용자 지정 redirectURI를 요청합니다. **개발자가 이 리디렉션 URI를 만드는 방법은 아래에 자세히 설명되어 있습니다.** 이 사용자 지정 redirectURI는 애플리케이션의 인증서 지문을 포함하고 Google Play 스토어에서 애플리케이션에 고유하도록 보장됩니다. 애플리케이션이 브로커를 호출하면 브로커는 Android 운영 체제에 브로커를 호출한 인증서 지문을 제공하도록 요청합니다. 브로커는 ID 시스템에 대한 호출에서 Microsoft에 이 인증서 지문을 제공합니다. 애플리케이션의 인증서 지문이 등록하는 동안 개발자가 제공한 인증서 지문과 일치하지 않는 경우 애플리케이션이 요청하는 리소스를 위한 토큰에 대한 액세스가 거부됩니다. 이러한 확인을 통해 개발자가 등록한 애플리케이션만 토큰을 받습니다.

조정된 SSO 로그인은 다음과 같은 이점이 있습니다.

* 사용자는 공급 업체에 관계 없이 모든 애플리케이션에서 SSO를 경험합니다.
* 애플리케이션은 조건부 액세스와 같은 고급 비즈니스 기능을 사용하고 Intune 시나리오를 지원합니다.
* 애플리케이션은 비즈니스 사용자에 대한 인증서 기반 인증을 지원할 수 있습니다.
* 애플리케이션 및 사용자의 ID로서 더 안전한 로그인 환경은 추가 보안 알고리즘 및 암호화로 브로커 애플리케이션에 의해 확인됩니다.

SDK가 broker 애플리케이션과 함께 작동하여 SSO를 사용하도록 설정하는 방법은 다음과 같습니다.

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>브로커 지원 SSO에 대한 SSO 설정

애플리케이션이 디바이스에 설치된 broker를 사용할 수 있는 기능은 기본적으로 해제되어 있습니다. 브로커와 함께 애플리케이션을 사용하려면 몇 가지 추가 구성을 수행하고 애플리케이션에 코드를 추가해야 합니다.

수행할 단계는 다음과 같습니다.

1. MS SDK에 대한 애플리케이션 코드의 호출에서 브로커 모드를 활성화합니다.
2. 새 리디렉션 URI 설정 및 앱과 앱 등록에 이를 제공
3. Android 매니페스트에서 올바른 사용 권한 설정

#### <a name="step-1-enable-broker-mode-in-your-application"></a>1단계: 애플리케이션에서 브로커 모드 활성화

"설정" 또는 인증 인스턴스의 초기 설정을 만들 때 브로커를 사용하는 애플리케이션에 대한 기능은 설정되어 있습니다. 앱에서 이를 수행하려면

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>2단계: URL 구성표와 함께 새 리디렉션 URI 설정

올바른 응용 프로그램이 자격 증명 토큰을 반환 하는지 확인 하기 위해 Android 운영 체제에서 확인할 수 있는 방식으로 응용 프로그램을 다시 호출 하는지 확인 해야 합니다. Android 운영 체제는 Google Play 스토어에서 인증서의 해시를 사용합니다. 불량 애플리케이션에서 인증서의 이 해시를 스푸핑할 수 없습니다. 브로커 애플리케이션의 URI와 함께 Microsoft는 토큰이 올바른 애플리케이션에 반환되는지 확인합니다. 고유한 리디렉션 URI는 애플리케이션에 등록돼야 합니다.

리디렉션 URI는 다음의 적절한 형식이어야 합니다.

`msauth://packagename/Base64UrlencodedSignature`

예: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

[Azure Portal](https://portal.azure.com/)을 사용하여 앱 등록에 이 리디렉션 URI를 지정할 수 있습니다. Azure AD 앱 등록에 대한 자세한 내용은 [Azure Active Directory와 통합](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조하세요.

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>3단계: 애플리케이션에 올바른 사용 권한 설정

Android에서 브로커 애플리케이션은 Android OS의 계정 관리자 기능을 사용하여 애플리케이션 간에 자격 증명을 관리합니다. Android에서 브로커를 사용하려면 앱 매니페스트에 AccountManager 계정을 사용할 수 있는 권한이 있어야 합니다. 이러한 사용 권한은 [여기의 계정 관리자에 대한 Google 설명서](https://developer.android.com/reference/android/accounts/AccountManager.html)에 자세히 설명돼 있습니다.

특히 이러한 사용 권한은 다음과 같습니다.

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>SSO를 구성했습니다.

이제 ID SDK는 애플리케이션 전체에서 자동으로 자격 증명을 공유하고, 디바이스에 있는 경우 broker를 호출합니다.

## <a name="next-steps"></a>다음 단계

* [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)에 대해 자세히 알아보기
