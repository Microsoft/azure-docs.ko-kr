---
title: Android MSAL 구성 파일 | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory에서 애플리케이션의 구성을 나타내는 Android MSAL(Microsoft 인증 라이브러리) 구성 파일에 대한 개요입니다.
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
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92275603"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft 인증 라이브러리 구성 파일

Android MSAL(Microsoft 인증 라이브러리)은 [기본 구성 JSON 파일](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)과 함께 제공됩니다. 이 파일을 사용자 지정하여 기본 인증 기관과 같은 항목에 대한 퍼블릭 클라이언트 앱의 동작을 정의할 수 있습니다.

이 문서는 구성 파일의 다양한 설정과 MSAL 기반 앱에서 사용할 구성 파일을 지정하는 방법을 이해하는 데 도움이 됩니다.

## <a name="configuration-settings"></a>구성 설정

### <a name="general-settings"></a>일반 설정

| 속성 | 데이터 형식 | 필수 | 메모 |
|-----------|------------|-------------|-------|
| `client_id` | String | 예 | [애플리케이션 등록 페이지](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 에서 앱의 클라이언트 ID |
| `redirect_uri`   | String | 예 | [애플리케이션 등록 페이지](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) 에서 앱의 리디렉션 URI |
| `broker_redirect_uri_registered` | 부울 | 예 | 가능한 값: `true`, `false` |
| `authorities` | List\<Authority> | 예 | 앱에 필요한 권한 목록 |
| `authorization_user_agent` | AuthorizationAgent (enum) | 예 | 가능한 값: `DEFAULT`, `BROWSER`, `WEBVIEW` |
| `http` | HttpConfiguration | 예 | `HttpUrlConnection` `connect_timeout` 및 `read_timeout` 구성 |
| `logging` | LoggingConfiguration | 예 | 로깅 세부 정보 수준을 지정합니다. 선택적 구성에는 부울 값을 사용하는 `pii_enabled`와 `ERROR`, `WARNING`, `INFO` 또는 `VERBOSE`를 사용하는 `log_level`이 있습니다. |

### <a name="client_id"></a>client_id

애플리케이션을 등록할 때 만든 클라이언트 ID 또는 앱 ID입니다.

### <a name="redirect_uri"></a>redirect_uri

애플리케이션을 등록할 때 등록한 리디렉션 URI입니다. broker 앱에 대한 리디렉션 URI인 경우 broker 앱에 올바른 리디렉션 URI 형식을 사용하고 있는지 확인하려면 [퍼블릭 클라이언트 앱의 리디렉션 URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)를 참조하세요.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

조정된 인증을 사용하려는 경우 `broker_redirect_uri_registered` 속성을 `true`로 설정해야 합니다. 조정된 인증 시나리오에서 애플리케이션이 [퍼블릭 클라이언트 앱의 리디렉션 URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)에 설명된 대로 broker와 통신하기 위한 올바른 형식이 아닌 경우 애플리케이션은 리디렉션 URI의 유효성을 검사하고 시작 시 예외를 throw합니다.

### <a name="authorities"></a>인증 기관

개발자가 알고 신뢰하는 인증 기관 목록입니다. 여기에 나열된 인증 기관 외에도 MSAL은 Microsoft에 알려진 클라우드 및 기관의 목록을 가져오도록 Microsoft에 쿼리합니다. 이 인증 기관 목록에서 인증 기관 유형과 선택적인 추가 매개 변수(예: `"audience"`)를 지정합니다. 이 매개 변수는 앱의 등록을 기반으로 하여 앱의 대상 그룹과 맞아야 합니다. 다음은 인증 기관 목록의 예입니다.

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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD 인증 기관 및 대상 그룹을 Microsoft ID 플랫폼 엔드포인트에 매핑

| 유형 | 사용자 | 테넌트 ID | Authority_Url | 결과 엔드포인트 | 참고 |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`은 계정의 테넌트 별칭입니다. 예: 특정 Azure Active Directory 테넌트 또는 Microsoft 계정 시스템. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Contoso.com에 있는 계정만 토큰을 획득할 수 있습니다. 확인된 도메인 또는 테넌트 GUID는 테넌트 ID로 사용될 수 있습니다. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | 이 엔드포인트에는 Azure Active Directory 계정만 사용할 수 있습니다. Microsoft 계정은 조직의 구성원일 수 있습니다. 조직에서 리소스의 Microsoft 계정을 사용하여 토큰을 얻으려면 토큰을 가져올 조직 테넌트를 지정합니다. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Microsoft 계정만 이 엔드포인트를 사용할 수 있습니다. |
| B2C | | | 결과 엔드포인트 보기 | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | contoso.onmicrosoft.com 테넌트에 있는 계정만 토큰을 획득할 수 있습니다. 이 예제에서 B2C 정책은 인증 기관 URL 경로의 일부입니다. |

> [!NOTE]
> MSAL에서는 인증 기관 유효성 검사를 사용하거나 사용하지 않도록 설정할 수 없습니다.
> 인증 기관은 구성을 통해 지정된 것처럼 또는 메타데이터를 통해 Microsoft에 알려진 것처럼 개발자로 알려집니다.
> MSAL에서 알 수 없는 인증 기관에 대한 토큰 요청을 수신하는 경우 `UnknownAuthority` 형식의 `MsalClientException`이 반환됩니다.
> Azure AD B2C에 대해 조정된 인증이 작동하지 않습니다.

#### <a name="authority-properties"></a>인증 기관 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|-----------|--------|
| `type` | String | 예 | 앱이 대상으로 하는 대상 그룹 또는 계정 유형을 미러링합니다. 가능한 값: `AAD`, `B2C` |
| `audience` | Object | 예 | 유형이 `AAD`인 경우에만 적용됩니다. 앱이 대상으로 하는 ID를 지정합니다. 앱 등록의 값 사용 |
| `authority_url` | String | 예 | 유형이 `B2C`인 경우에만 필요합니다. 앱에서 사용해야 하는 인증 기관 URL 또는 정책을 지정합니다.  |
| `default` | boolean | 예 | 하나 이상의 인증 기관이 지정된 경우 단일 `"default":true`가 필요합니다. |

#### <a name="audience-properties"></a>대상 그룹 속성

| 속성 | 데이터 형식  | 필수 | 메모 |
|-----------|-------------|------------|-------|
| `type` | String | 예 | 앱이 대상으로 지정하려는 대상 그룹을 지정합니다. 가능한 값: `AzureADandPersonalMicrosoftAccount`, `PersonalMicrosoftAccount`, `AzureADMultipleOrgs`, `AzureADMyOrg` |
| `tenant_id` | String | 예 | `"type":"AzureADMyOrg"`인 경우에만 필요합니다. 다른 `type` 값의 경우 선택 사항입니다. 테넌트 도메인(예: `contoso.com`) 또는 테넌트 ID(예: `72f988bf-86f1-41af-91ab-2d7cd011db46`)일 수 있습니다. |

### <a name="authorization_user_agent"></a>authorization_user_agent

계정에 로그인하거나 리소스에 대한 액세스 권한을 부여할 때 디바이스에서 포함된 웹 보기 또는 기본 브라우저를 사용할지 여부를 나타냅니다.

가능한 값은 다음과 같습니다.
- `DEFAULT`: 시스템 브라우저를 선호합니다. 디바이스에서 브라우저를 사용할 수 없는 경우 포함된 웹 보기를 사용합니다.
- `WEBVIEW`: 포함된 웹 보기를 사용합니다.
- `BROWSER`: 디바이스에서 기본 브라우저를 사용합니다.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

여러 국가 클라우드를 지원하는 클라이언트의 경우 `true`를 지정합니다. Microsoft ID 플랫폼은 권한 부여, 토큰 상환 중 올바른 국가 클라우드로 자동으로 리디렉션됩니다. `AuthenticationResult`와 연결된 인증 기관을 검사하여 로그인한 계정의 국가 클라우드를 확인할 수 있습니다. `AuthenticationResult`는 토큰을 요청하는 리소스의 국가 클라우드 관련 엔드포인트 주소를 제공하지 않습니다.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Microsoft ID broker와 호환되는 broker 내 리디렉션 URI를 사용하고 있는지 여부를 나타내는 부울입니다. 앱 내에서 broker를 사용하지 않으려면 `false`로 설정합니다.

대상 그룹이 `"MicrosoftPersonalAccount"`로 설정된 AAD 기관을 사용하는 경우 broker가 사용되지 않습니다.

### <a name="http"></a>http

HTTP 시간 제한에 대해 다음과 같은 전역 설정을 구성합니다.

| 속성 | 데이터 형식 | 필수 | 참고 |
| ---------|-----------|------------|--------|
| `connect_timeout` | Int | 아니요 | 시간(밀리초) |
| `read_timeout` | Int | 아니요 | 시간(밀리초) |

### <a name="logging"></a>로깅

로깅에 대한 전역 설정은 다음과 같습니다.

| 속성 | 데이터 형식  | 필수 | 참고 |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | 예 | 개인 데이터를 내보낼지 여부를 지정합니다. |
| `log_level`   | 문자열 | No | 어떤 로그 메시지를 출력할지 지정합니다. 지원되는 로그 수준은 `ERROR`,`WARNING`,`INFO`, `VERBOSE`입니다. |
| `logcat_enabled` | boolean | 예 | 로깅 인터페이스 외에 로그 범주에 출력할지 여부를 지정합니다. |

### <a name="account_mode"></a>account_mode

앱 내에서 한 번에 사용할 수 있는 계정 수를 지정합니다. 가능한 값은 다음과 같습니다.

- `MULTIPLE`(기본값)
- `SINGLE`

이 설정과 일치하지 않는 계정 모드를 사용하여 `PublicClientApplication`을 생성하면 예외가 발생합니다.

단일 계정과 여러 계정 간의 차이점에 대한 자세한 내용은 [단일 및 여러 계정 앱](single-multi-account.md)을 참조하세요.

### <a name="browser_safelist"></a>browser_safelist

MSAL과 호환되는 브라우저의 허용 목록입니다. 이러한 브라우저는 사용자 지정 의도에 대한 리디렉션을 올바르게 처리합니다. 이 목록에 추가할 수 있습니다. 기본값은 아래에 표시된 기본 구성에서 제공됩니다.
``
## <a name="the-default-msal-configuration-file"></a>기본 MSAL 구성 파일

MSAL과 함께 제공되는 기본 MSAL 구성은 아래와 같습니다. [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)에서 최신 버전을 확인할 수 있습니다.

이 구성은 개발자가 제공하는 값으로 보완됩니다. 개발자가 제공하는 값은 기본값을 재정의합니다.

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
## <a name="example-basic-configuration"></a>기본 구성 예제

다음 예제에서는 클라이언트 ID, 리디렉션 URI, broker 리디렉션이 등록되었는지 여부, 인증 기관 목록을 지정하는 기본 구성을 보여 줍니다.

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

## <a name="how-to-use-a-configuration-file"></a>구성 파일 사용 방법

1. 구성 파일을 만듭니다. `res/raw/auth_config.json`에서 사용자 지정 구성 파일을 만드는 것이 좋습니다. 하지만 원하는 위치에 배치할 수 있습니다.
2. `PublicClientApplication`을 생성할 때 MSAL에서 구성을 찾을 위치를 지정합니다. 예:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
