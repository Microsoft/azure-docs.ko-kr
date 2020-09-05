---
title: Android에서 조정 된 인증 | Microsoft
titlesuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 Android 용 조정 된 인증 & 권한 부여에 대 한 개요
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 9042318d29b9a7fc8c2064bdf845d6f0d5a4f3e8
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853864"
---
# <a name="brokered-authentication-in-android"></a>Android에서 조정 된 인증

Microsoft 인증 브로커 중 하나를 사용 하 여 장치 차원의 SSO (Single Sign-on)에 참여 하 고 조직 조건부 액세스 정책을 충족 해야 합니다. Broker와 통합 하면 다음과 같은 이점이 있습니다.

- 장치 Single Sign-On
- 다음에 대 한 조건부 액세스:
  - Intune 앱 보호
  - 장치 등록 (Workplace Join)
  - Mobile Device Management
- 장치 수준 계정 관리
  -  Android AccountManager를 통해 & 계정 설정
  - "회사 계정"-사용자 지정 계정 유형

Android에서 Microsoft 인증 브로커는 [Microsoft Authenticator 앱](https://play.google.com/store/apps/details?id=com.azure.authenticator) 과 함께 제공 되는 구성 요소 [Intune 회사 포털](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Broker를 호스트 하는 응용 프로그램은 한 번에 하나씩만 broker로 활성화 됩니다. Broker로 활성화 되는 응용 프로그램은 장치에서 설치 순서에 따라 결정 됩니다. 처음에 설치 하거나 장치에 마지막으로 표시 되는가 활성 broker가 됩니다.

다음 다이어그램은 앱, MSAL (Microsoft 인증 라이브러리) 및 Microsoft 인증 브로커 간의 관계를 보여 줍니다.

![Broker 배포 다이어그램](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Broker를 호스트 하는 앱 설치

Broker 호스팅 앱은 언제 든 지 앱 스토어 (일반적으로 Google Play 스토어)에서 장치 소유자에 의해 설치 될 수 있습니다. 그러나 일부 Api (리소스)는 장치를 사용 해야 하는 조건부 액세스 정책에 의해 보호 됩니다.

- 등록 됨 (작업 공간에 연결 됨) 및/또는
- 장치 관리에 등록 됨 또는
- Intune 앱 보호에 등록 됨

장치에 아직 broker 앱이 설치 되어 있지 않은 경우 MSAL은 앱에서 토큰을 대화형으로 가져오려고 할 때 즉시 설치 하도록 사용자에 게 지시 합니다. 그러면 앱이 사용자에 게 필요한 정책을 준수 하도록 장치를 설정 하는 단계를 안내 합니다.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Broker 설치 및 제거의 효과

### <a name="when-a-broker-is-installed"></a>Broker가 설치 된 경우

Broker가 장치에 설치 되 면 모든 후속 대화형 토큰 요청 (에 대 한 호출 `acquireToken()` )은 MSAL에 의해 로컬이 아닌 broker에서 처리 됩니다. 이전에 MSAL에서 사용할 수 있는 모든 SSO 상태를 broker에서 사용할 수 없습니다. 따라서 사용자가 다시 인증 하거나 장치에 알려진 기존 계정 목록에서 계정을 선택 해야 합니다.

Broker를 설치 하는 경우에는 사용자가 다시 로그인 할 필요가 없습니다. 사용자가를 확인 해야 하는 경우에만 `MsalUiRequiredException` 다음 요청이 broker로 이동 합니다. `MsalUiRequiredException` 는 여러 가지 이유로 throw 되며 대화형으로 해결 해야 합니다. 몇 가지 일반적인 이유는 다음과 같습니다.

- 사용자가 계정과 연결 된 암호를 변경 했습니다.
- 사용자 계정이 더 이상 조건부 액세스 정책을 충족 하지 않습니다.
- 사용자가 앱의 계정에 연결 하는 데 대 한 동의를 취소 했습니다.

### <a name="when-a-broker-is-uninstalled"></a>Broker가 제거 될 때

Broker 호스팅 앱이 하나만 설치 되어 있고 제거 된 경우에는 사용자가 다시 로그인 해야 합니다. 활성 broker를 제거 하면 장치에서 계정 및 연결 된 토큰이 제거 됩니다.

Intune 회사 포털 설치 되어 있고 활성 broker로 작동 하 고 Microsoft Authenticator도 설치 된 경우에는 Intune 회사 포털 (활성 broker)가 제거 되 면 사용자가 다시 로그인 해야 합니다. 다시 로그인 하면 Microsoft Authenticator 앱이 활성 broker가 됩니다.

## <a name="integrating-with-a-broker"></a>Broker와 통합

### <a name="generating-a-redirect-uri-for-a-broker"></a>Broker에 대 한 리디렉션 URI 생성

Broker와 호환 되는 리디렉션 URI를 등록 해야 합니다. 브로커의 리디렉션 URI에는 앱의 패키지 이름 뿐만 아니라 앱의 서명에 대 한 b a s e 64로 인코딩된 표현이 포함 되어야 합니다.

리디렉션 URI의 형식은 다음과 같습니다. `msauth://<yourpackagename>/<base64urlencodedsignature>`

앱의 서명 키를 사용 하 여 Base64 url 인코딩된 서명을 생성 합니다. 디버그 서명 키를 사용 하는 몇 가지 예제 명령은 다음과 같습니다.

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

앱에 서명 하는 방법에 대 한 자세한 내용은 [앱에 서명](https://developer.android.com/studio/publish/app-signing) 을 참조 하세요.

> [!IMPORTANT]
> 프로덕션 버전의 앱에 대 한 프로덕션 서명 키를 사용 합니다.

### <a name="configure-msal-to-use-a-broker"></a>Broker를 사용 하도록 MSAL 구성

앱에서 broker를 사용 하려면 broker 리디렉션을 구성 했는지를 증명 해야 합니다. 예를 들어 broker 사용 리디렉션 URI를 모두 포함 하 고, MSAL 구성 파일에 다음을 포함 하 여 등록 했음을 표시 합니다.

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> 새 Azure Portal 앱 등록 UI는 broker 리디렉션 URI를 생성 하는 데 도움이 됩니다. 이전 환경을 사용 하 여 앱을 등록 했거나 Microsoft 앱 등록 포털을 사용 하 여 앱을 등록 한 경우 리디렉션 URI를 생성 하 고 포털에서 리디렉션 Uri 목록을 수동으로 업데이트 해야 할 수 있습니다.

### <a name="broker-related-exceptions"></a>Broker 관련 예외

MSAL은 다음 두 가지 방법으로 broker와 통신 합니다.

- Broker 바운드 서비스
- Android AccountManager

이 서비스를 호출 하려면 Android 권한이 필요 하지 않으므로 MSAL은 broker 바운드 서비스를 먼저 사용 합니다. 바인딩된 서비스에 대 한 바인딩이 실패 하면 MSAL은 Android AccountManager API를 사용 합니다. MSAL은 앱에 이미 권한이 부여 된 경우에만이를 수행 `"READ_CONTACTS"` 합니다.

오류가 발생 하는 경우 `MsalClientException` `"BROKER_BIND_FAILURE"` 다음 두 가지 옵션을 사용할 수 있습니다.

- 사용자에 게 Microsoft Authenticator 앱 및 Intune 회사 포털에 대 한 전원 최적화를 사용 하지 않도록 설정 합니다.
- 사용자에 게 권한을 부여 하도록 요청 `"READ_CONTACTS"`

## <a name="verifying-broker-integration"></a>Broker 통합 확인

Broker 통합이 작동 한다는 것을 즉시 알 수는 없지만 다음 단계를 사용 하 여 확인할 수 있습니다.

1. Android 장치에서 broker를 사용 하 여 요청을 완료 합니다.
1. Android 장치의 설정에서 인증 한 계정에 해당 하는 새로 만든 계정을 찾습니다. 계정은 *회사 계정*형식 이어야 합니다.

테스트를 반복 하려는 경우 설정에서 계정을 제거할 수 있습니다.
