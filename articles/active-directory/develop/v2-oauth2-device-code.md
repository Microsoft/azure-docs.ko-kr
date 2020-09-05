---
title: OAuth 2.0 장치 코드 흐름 | Microsoft
titleSuffix: Microsoft identity platform
description: 브라우저 없이 사용자 로그인 장치 권한 부여를 사용 하 여 포함 된 브라우저 없는 인증 흐름을 빌드합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272453"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft id 플랫폼 및 OAuth 2.0 장치 권한 부여 흐름

Microsoft id 플랫폼은 사용자가 스마트 TV, IoT 장치 또는 프린터와 같은 입력 제한 장치에 로그인 할 수 있도록 하는 [장치 권한](https://tools.ietf.org/html/rfc8628)부여를 지원 합니다.  이 흐름을 사용하도록 설정하기 위해 디바이스에서 사용자가 다른 디바이스의 브라우저에 있는 웹 페이지를 방문하여 로그인하도록 합니다.  사용자가 로그인하면 디바이스에서 액세스 토큰을 가져오고 필요에 따라 해당 토큰을 새로 고칠 수 있습니다.

이 문서에서는 애플리케이션에서 프로토콜에 대해 직접 프로그래밍을 수행하는 방법을 설명합니다.  가능하다면 [토큰을 획득하고 보안 웹 API를 호출](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)하는 대신, 지원되는 MSAL(Microsoft 인증 라이브러리)을 사용하는 것이 좋습니다.  [MSAL을 사용하는 샘플 앱](sample-v2-code.md)도 살펴봅니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

전체 디바이스 코드 흐름은 다음 다이어그램과 비슷합니다. 이 문서의 뒷부분에서 각 단계에 대해 설명합니다.

![디바이스 코드 흐름](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>디바이스 권한 부여 요청

클라이언트에서 먼저 인증을 시작하는 데 사용되는 디바이스 및 사용자 코드에 대한 인증 서버를 확인해야 합니다. 클라이언트는 `/devicecode` 엔드포인트에서 이 요청을 수집합니다. 이 요청에서 클라이언트는 사용자로부터 획득해야 하는 권한도 포함해야 합니다. 이 요청이 전송되는 순간부터, 사용자는 15분 동안 로그인(`expires_in`의 일반적인 값)해야 하므로 사용자가 로그인할 준비가 되었을 때 이 요청을 작성하세요.

> [!TIP]
> Postman에서 이 요청을 실행해 보세요.
> [![Postman에서 이 요청을 실행해 보세요](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | /공용/소비자 또는/organizations. 일 수 있습니다.  GUID 또는 친숙 한 이름 형식의 사용 권한을 요청 하려는 디렉터리 테 넌 트가 될 수도 있습니다.  |
| `client_id` | 필수 | [Azure Portal - 앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 환경이 앱에 할당한 **애플리케이션(클라이언트) ID**입니다. |
| `scope` | 필수 | 사용자가 동의하게 할 공백으로 구분된 [범위](v2-permissions-and-consent.md) 목록입니다.  |

### <a name="device-authorization-response"></a>디바이스 권한 부여 응답

성공적인 응답은 사용자가 로그인할 수 있게 하는 데 필요한 정보가 포함된 JSON 개체입니다.

| 매개 변수 | 형식 | Description |
| ---              | --- | --- |
|`device_code`     | String | 클라이언트와 권한 부여 서버 간의 세션을 확인하는 데 사용되는 긴 문자열입니다. 클라이언트에서 이 매개 변수를 사용하여 권한 부여 서버의 액세스 토큰을 요청합니다. |
|`user_code`       | String | 보조 디바이스의 세션을 식별하는 데 사용되어 사용자에게 표시되는 짧은 문자열입니다.|
|`verification_uri`| URI | 로그인하기 위해 사용자가 `user_code`을(를) 사용하여 이동하는 URI입니다. |
|`expires_in`      | int | `device_code` 및 `user_code`의 만료 전 시간(초)입니다. |
|`interval`        | int | 클라이언트에서 폴링 요청 간에 가 대기해야 하는 시간(초)입니다. |
| `message`        | String | 사용자를 위한 지침이 포함된 사람이 읽을 수 있는 문자열입니다. 이는 `?mkt=xx-XX` 양식의 요청에 **쿼리 매개 변수**를 포함하고 적절한 언어 문화권 코드를 채워서 지역화할 수 있습니다. |

> [!NOTE]
> `verification_uri_complete`응답 필드는 현재 포함 되어 있지 않거나 지원 되지 않습니다.  [표준](https://tools.ietf.org/html/rfc8628) 에 따라 `verification_uri_complete` 장치 코드 흐름 표준의 선택적 부분으로 나열 되는 것을 볼 수 있기 때문에이를 언급 합니다.

## <a name="authenticating-the-user"></a>사용자 인증

및를 받은 `user_code` 후 `verification_uri` 클라이언트는 사용자에 게 이러한 항목을 표시 하 여 휴대폰 또는 PC 브라우저를 사용 하 여 로그인 하도록 지시 합니다.

사용자가 개인 계정으로 인증 하는 경우 (/공통 또는/또는 소비자) 인증 상태를 장치로 전송 하기 위해 다시 로그인 하 라는 메시지가 표시 됩니다.  또한 부여 되는 사용 권한을 인식 하도록 동의를 제공 하 라는 메시지가 표시 됩니다.  이는 인증에 사용 되는 회사 또는 학교 계정에는 적용 되지 않습니다.

사용자가 `verification_uri`에서 인증하는 동안 클라이언트는 `device_code`을(를) 사용하여 요청된 토큰에 대한 `/token` 엔드포인트를 폴링해야 합니다.

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| 매개 변수 | 필수 | 설명|
| -------- | -------- | ---------- |
| `tenant`  | 필수 | 초기 요청에 사용 되는 것과 동일한 테 넌 트 또는 테 넌 트 별칭입니다. |
| `grant_type` | 필수 | `urn:ietf:params:oauth:grant-type:device_code`이어야 합니다.|
| `client_id`  | 필수 | 초기 요청에 사용된 `client_id`과(와) 일치해야 합니다. |
| `device_code`| 필수 | 디바이스 권한 요청에서 반환된 `device_code`입니다.  |

### <a name="expected-errors"></a>예상 오류

장치 코드 흐름은 사용자가 인증을 마치기 전에 클라이언트에서 오류를 수신 해야 하는 폴링 프로토콜입니다.

| 오류 | Description | 클라이언트 작업 |
| ------ | ----------- | -------------|
| `authorization_pending` | 사용자가 인증을 마치지 않았지만 흐름을 취소 하지 않았습니다. | 최소 `interval`초 후에 요청을 반복하세요. |
| `authorization_declined` | 일반 사용자가 권한 요청을 거부했습니다.| 폴링을 중지하고 인증되지 않은 상태로 되돌립니다.  |
| `bad_verification_code`| `device_code`끝점으로 전송 된를 `/token` 인식할 수 없습니다. | 클라이언트가 요청에서 올바른 `device_code`을(를) 보내는지 확인하세요. |
| `expired_token` | `expires_in`초 이상이 경과했으며 이 `device_code`을(를) 사용하여 더 이상 인증할 수 없습니다. | 폴링을 중지 하 고 인증 되지 않은 상태로 되돌립니다. |

### <a name="successful-authentication-response"></a>성공적인 인증 응답

성공적인 토큰 응답은 다음과 같습니다.

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| 매개 변수 | 형식 | Description |
| --------- | ------ | ----------- |
| `token_type` | String| 항상 Bearer입니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우 이 매개 변수는 액세스 토큰의 유효 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효하게 될 때까지의 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다.  |
| `id_token`   | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다.  |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다.  |

새로 고침 토큰을 사용 하 여 [OAuth 코드 흐름 설명서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에 설명 된 것과 같은 흐름을 사용 하 여 새 액세스 토큰을 획득 하 고 토큰을 새로 고칠 수 있습니다.
