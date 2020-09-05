---
title: Android MSAL 구성 파일 | Microsoft
titleSuffix: Microsoft identity platform
description: Azure Active Directory의 응용 프로그램 구성을 나타내는 MSAL (Android Microsoft Authentication Library) 구성 파일에 대 한 개요입니다.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: f5950347fff380fcfbaa89834407ff5f497a9719
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854910"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft 인증 라이브러리 구성 파일

Android MSAL (Microsoft 인증 라이브러리)은 기본 [구성 JSON 파일](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) 을 제공 하며,이 파일을 사용 하 여 기본 기관과 같은 항목에 대 한 공용 클라이언트 앱의 동작을 정의 합니다.

이 문서는 구성 파일의 다양 한 설정과 MSAL 기반 앱에서 사용할 구성 파일을 지정 하는 방법을 이해 하는 데 도움이 됩니다.

## <a name="configuration-settings"></a>구성 설정

### <a name="general-settings"></a>일반 설정

| 속성 | 데이터 형식 | 필수 | 메모 |
|-----------|------------|-------------|-------|
| `client_id` | String | 예 | [응용 프로그램 등록 페이지](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 에서 앱의 클라이언트 ID |
| `redirect_uri`   | String | 예 | [응용 프로그램 등록 페이지](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 에서 앱의 리디렉션 URI |
| `authorities` | 목록\<Authority> | 아니요 | 앱에 필요한 권한 목록 |
| `authorization_user_agent` | AuthorizationAgent (enum) | 아니요 | 가능한 값: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | 아니요 | 구성 `HttpUrlConnection` `connect_timeout` 및 `read_timeout` |
| `logging` | LoggingConfiguration | 아니요 | 로깅 세부 정보 수준을 지정 합니다. 선택적 구성에는 `pii_enabled` 부울 값을 사용 하는 및,, `log_level` `ERROR` `WARNING` `INFO` 또는를 사용 `VERBOSE` 하는가 포함 됩니다. |

### <a name="client_id"></a>client_id

응용 프로그램을 등록할 때 만든 클라이언트 ID 또는 응용 프로그램 ID입니다.

### <a name="redirect_uri"></a>redirect_uri

응용 프로그램을 등록할 때 등록 한 리디렉션 URI입니다. Broker 응용 프로그램에 대 한 리디렉션 uri 인 경우 broker 앱에 대 한 올바른 리디렉션 URI 형식을 사용 하 고 있는지 확인 하려면 [공용 클라이언트 앱에 대 한 리디렉션 uri](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) 를 참조 하세요.

### <a name="authorities"></a>권한

사용자가 인식 하 고 신뢰 하는 기관 목록입니다. 여기에 나열 된 인증 기관 외에도, MSAL은 microsoft에 알려진 클라우드 및 기관의 목록을 가져오도록 Microsoft를 쿼리 합니다. 이 권한 목록에서 인증 유형 및와 같은 추가 선택적 매개 변수를 지정 합니다 .이 매개 변수 `"audience"` 는 앱의 등록을 기반으로 하 여 앱의 대상 그룹과 일치 해야 합니다. 다음은 기관의 예제 목록입니다.

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD authority & 대상을 Microsoft identity platform 끝점에 매핑

| 형식 | 사용자 | 테넌트 ID | Authority_Url | 결과 끝점 | 참고 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` 는 계정이 인에 대 한 테 넌 트 별칭입니다. 예: 특정 Azure Active Directory 테 넌 트 또는 Microsoft 계정 시스템 |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Contoso.com에 있는 계정만 토큰을 획득할 수 있습니다. 확인 된 도메인 또는 테 넌 트 GUID는 테 넌 트 ID로 사용 될 수 있습니다. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | 이 끝점에는 Azure Active Directory 계정만 사용할 수 있습니다. Microsoft 계정은 조직의 멤버일 수 있습니다. 조직에서 리소스에 대 한 Microsoft 계정를 사용 하 여 토큰을 얻으려면 토큰을 원하는 조직 테 넌 트를 지정 합니다. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Microsoft 계정만이 끝점을 사용할 수 있습니다. |
| B2C | | | 결과 끝점 보기 | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Contoso.onmicrosoft.com 테 넌 트에 있는 계정만 토큰을 획득할 수 있습니다. 이 예제에서 B2C 정책은 인증 기관 URL 경로의 일부입니다. |

> [!NOTE]
> MSAL에서는 인증 기관 유효성 검사를 사용 하거나 사용 하지 않도록 설정할 수 없습니다.
> 구성 또는 메타 데이터를 통해 Microsoft에 알려진 권한을 개발자에 게 사용자에 게 알려 두는 것입니다.
> MSAL에서 토큰에 대 한 요청을 알 수 없는 기관에 수신 하는 경우 `MsalClientException` 형식의이 `UnknownAuthority` 반환 됩니다.

#### <a name="authority-properties"></a>인증 기관 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|-----------|--------|
| `type` | String | 예 | 앱이 대상으로 하는 대상 또는 계정 유형을 미러링합니다. 가능한 값: `AAD` , `B2C` |
| `audience` | Object | 아니요 | Type = 인 경우에만 적용 됩니다 `AAD` . 앱이 대상으로 하는 id를 지정 합니다. 앱 등록의 값 사용 |
| `authority_url` | String | 예 | Type = 인 경우에만 필요 `B2C` 합니다. 앱에서 사용 해야 하는 기관 URL 또는 정책을 지정 합니다.  |
| `default` | boolean | 예 | 하나 이상의 `"default":true` 기관이 지정 된 경우 단일가 필요 합니다. |

#### <a name="audience-properties"></a>대상 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|------------|-------|
| `type` | String | 예 | 앱이 대상으로 지정 하려는 대상 그룹을 지정 합니다. 가능한 값: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | String | 예 | 인 경우에만 필요 `"type":"AzureADMyOrg"` 합니다. 다른 값의 경우 선택 사항 `type` 입니다. 이 도메인은와 같은 테 넌 트 도메인 `contoso.com` 이거나와 같은 테 넌 트 ID 일 수 있습니다. `72f988bf-86f1-41af-91ab-2d7cd011db46` |

### <a name="authorization_user_agent"></a>authorization_user_agent

계정에 로그인 하거나 리소스에 대 한 액세스 권한을 부여할 때 장치에서 포함 된 웹 보기 또는 기본 브라우저를 사용할지 여부를 나타냅니다.

가능한 값은 다음과 같습니다.
- `DEFAULT`: 시스템 브라우저를 선호 합니다. 장치에서 브라우저를 사용할 수 없는 경우 포함 된 웹 보기를 사용 합니다.
- `WEBVIEW`: 포함 된 웹 뷰를 사용 합니다.
- `BROWSER`: 장치의 기본 브라우저를 사용 합니다.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

여러 국가 클라우드를 지 원하는 클라이언트의 경우를 지정 `true` 합니다. Microsoft id 플랫폼은 권한 부여 및 토큰 상환 중에 올바른 국가 클라우드로 자동으로 리디렉션됩니다. 와 연결 된 기관을 검사 하 여 로그인 한 계정의 국가별 클라우드를 확인할 수 있습니다 `AuthenticationResult` . 는 `AuthenticationResult` 토큰을 요청 하는 리소스의 국가별 클라우드 관련 끝점 주소를 제공 하지 않습니다.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Microsoft Identity broker와 호환 되는 브로커 리디렉션 URI를 사용 하 고 있는지 여부를 나타내는 부울입니다. `false`앱 내에서 broker를 사용 하지 않으려면로 설정 합니다.

대상 그룹이로 설정 된 AAD 기관을 사용 하는 경우 `"MicrosoftPersonalAccount"` broker가 사용 되지 않습니다.

### <a name="http"></a>http

HTTP 시간 제한에 대해 다음과 같은 전역 설정을 구성 합니다.

| 속성 | 데이터 형식 | 필수 | 참고 |
| ---------|-----------|------------|--------|
| `connect_timeout` | Int | 아니요 | 시간 (밀리초) |
| `read_timeout` | Int | 아니요 | 시간 (밀리초) |

### <a name="logging"></a>로깅

로깅에 대 한 전역 설정은 다음과 같습니다.

| 속성 | 데이터 형식  | 필수 | 참고 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 아니요 | 개인 데이터를 내보낼지 여부 |
| `log_level`   | 문자열 | No | 출력할 로그 메시지입니다. 지원 되는 로그 수준 `ERROR` 에는,, 및가 `WARNING` `INFO` `VERBOSE` 있습니다. |
| `logcat_enabled` | boolean | 아니요 | 로깅 인터페이스 외에도 cat을 기록 하도록 출력할지 여부 |

### <a name="account_mode"></a>account_mode

앱 내에서 한 번에 사용할 수 있는 계정 수를 지정 합니다. 가능한 값은 다음과 같습니다.

- `MULTIPLE`(기본값)
- `SINGLE`

`PublicClientApplication`이 설정과 일치 하지 않는 계정 모드를 사용 하 여를 생성 하면 예외가 발생 합니다.

단일 계정과 여러 계정 간의 차이점에 대 한 자세한 내용은 [단일 및 여러 계정 앱](single-multi-account.md)을 참조 하세요.

### <a name="browser_safelist"></a>browser_safelist

MSAL과 호환 되는 브라우저의 허용 목록입니다. 이러한 브라우저는 사용자 지정 의도에 대 한 리디렉션을 올바르게 처리 합니다. 이 목록에를 추가할 수 있습니다. 기본값은 아래에 표시 된 기본 구성에서 제공 됩니다.
``
## <a name="the-default-msal-configuration-file"></a>기본 MSAL 구성 파일

MSAL과 함께 제공 되는 기본 MSAL 구성은 아래와 같습니다. [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)에서 최신 버전을 확인할 수 있습니다.

이 구성은 사용자가 제공 하는 값으로 보완 됩니다. 사용자가 제공 하는 값은 기본값을 재정의 합니다.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>기본 구성 예

다음 예제에서는 클라이언트 ID, 리디렉션 URI, broker 리디렉션이 등록 되었는지 여부 및 기관 목록을 지정 하는 기본 구성을 보여 줍니다.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>구성 파일을 사용 하는 방법

1. 구성 파일을 만듭니다. 에서 사용자 지정 구성 파일을 만드는 것이 좋습니다 `res/raw/auth_config.json` . 하지만 원하는 위치에 배치할 수 있습니다.
2. 을 생성할 때 구성을 찾을 위치를 MSAL에 알려 주십시오 `PublicClientApplication` . 예를 들어:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
