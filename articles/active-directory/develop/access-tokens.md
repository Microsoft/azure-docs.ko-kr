---
title: Microsoft id 플랫폼 액세스 토큰 참조 | Microsoft
description: Azure AD v1.0 및 v2.0 (Microsoft identity platform) 끝점에서 내보내는 액세스 토큰에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: 7d596292a823b4d912204f5cfbe8623ab7429fa3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161395"
---
# <a name="microsoft-identity-platform-access-tokens"></a>Microsoft id 플랫폼 액세스 토큰

액세스 토큰을 사용하면 Azure에서 보호되는 API를 클라이언트가 안전하게 호출할 수 있습니다. Microsoft id 플랫폼 액세스 토큰은 Azure에서 서명 된 [Jwts](https://tools.ietf.org/html/rfc7519), BASE64 인코딩 JSON 개체입니다. 토큰의 내용은 리소스를 위한 것이므로 클라이언트는 액세스 토큰을 불투명 문자열로 처리해야 합니다. 유효성 검사 및 디버깅 목적으로, 개발자가 [jwt.ms](https://jwt.ms)와 같은 사이트를 사용하여 JWT를 디코딩할 수 있습니다. 클라이언트는 다양 한 프로토콜을 사용 하 여 v 1.0 끝점 또는 v2.0 끝점에서 액세스 토큰을 가져올 수 있습니다.

클라이언트가 액세스 토큰을 요청 하면 Azure AD는 앱 사용에 대 한 액세스 토큰에 대 한 일부 메타 데이터도 반환 합니다. 이 정보에는 액세스 토큰의 만료 시간 및 유효 범위가 포함되어 있습니다. 이 데이터를 사용하면 앱에서 액세스 토큰 자체를 구문 분석하지 않고도 액세스 토큰의 인텔리전트 캐싱을 수행할 수 있습니다.

애플리케이션이 클라이언트가 액세스를 요청할 수 있는 리소스(웹 API)인 경우 액세스 토큰이 인증 및 권한 부여에 사용할 사용자, 클라이언트, 발급자, 권한 등의 유용한 정보를 제공합니다.

리소스의 유효성을 검사하고 액세스 토큰 내에서 클레임을 사용하는 방법에 대해 알아보려면 다음 섹션을 참조하세요.

> [!IMPORTANT]
> 액세스 토큰은 토큰의 *대상* 에 따라 생성 되며, 토큰의 범위를 소유 하는 응용 프로그램을 의미 합니다.  이는 `2`에 대 한 [앱 매니페스트에](reference-app-manifest.md#manifest-reference) 리소스 설정을 `accessTokenAcceptedVersion` 하는 방법으로, v2.0 끝점을 호출 하는 클라이언트가 v2.0 액세스 토큰을 받을 수 있도록 합니다.  마찬가지로, 클라이언트에 대 한 액세스 토큰 [선택적 클레임](active-directory-optional-claims.md) 을 변경 해도 MS Graph 리소스에서 소유 하는 `user.read`에 대해 토큰이 요청 될 때 받은 액세스 토큰은 변경 되지 않습니다.  
> 동일한 이유로 개인 계정 (예: hotmail.com 또는 outlook.com)을 사용 하 여 클라이언트 응용 프로그램을 테스트 하는 동안 클라이언트에서 받은 액세스 토큰이 불투명 문자열 임을 알 수 있습니다. 이는 액세스 되는 리소스가 암호화 된 레거시 MSA (Microsoft 계정) 티켓을 요청 하 여 클라이언트에서 인식할 수 없기 때문입니다.

## <a name="sample-tokens"></a>샘플 토큰

v1.0 및 v2.0 토큰은 유사하며 많은 동일한 클레임을 포함합니다. 각각의 예는 다음과 같습니다.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd
```

[JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiJlZjFkYTlkNC1mZjc3LTRjM2UtYTAwNS04NDBjM2Y4MzA3NDUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTUyMjIyOS8iLCJpYXQiOjE1MzcyMzMxMDYsIm5iZiI6MTUzNzIzMzEwNiwiZXhwIjoxNTM3MjM3MDA2LCJhY3IiOiIxIiwiYWlvIjoiQVhRQWkvOElBQUFBRm0rRS9RVEcrZ0ZuVnhMaldkdzhLKzYxQUdyU091TU1GNmViYU1qN1hPM0libUQzZkdtck95RCtOdlp5R24yVmFUL2tES1h3NE1JaHJnR1ZxNkJuOHdMWG9UMUxrSVorRnpRVmtKUFBMUU9WNEtjWHFTbENWUERTL0RpQ0RnRTIyMlRJbU12V05hRU1hVU9Uc0lHdlRRPT0iLCJhbXIiOlsid2lhIl0sImFwcGlkIjoiNzVkYmU3N2YtMTBhMy00ZTU5LTg1ZmQtOGMxMjc1NDRmMTdjIiwiYXBwaWRhY3IiOiIwIiwiZW1haWwiOiJBYmVMaUBtaWNyb3NvZnQuY29tIiwiZmFtaWx5X25hbWUiOiJMaW5jb2xuIiwiZ2l2ZW5fbmFtZSI6IkFiZSAoTVNGVCkiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMjIyNDcvIiwiaXBhZGRyIjoiMjIyLjIyMi4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJvaWQiOiIwMjIyM2I2Yi1hYTFkLTQyZDQtOWVjMC0xYjJiYjkxOTQ0MzgiLCJyaCI6IkkiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJzdWIiOiJsM19yb0lTUVUyMjJiVUxTOXlpMmswWHBxcE9pTXo1SDNaQUNvMUdlWEEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6ImFiZWxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJGVnNHeFlYSTMwLVR1aWt1dVVvRkFBIiwidmVyIjoiMS4wIn0.D3H6pMUtQnoJAGq6AHd)에서 이 v1.0 토큰을 확인합니다.

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt
```

[JWT.ms](https://jwt.ms/#access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9.eyJhdWQiOiI2ZTc0MTcyYi1iZTU2LTQ4NDMtOWZmNC1lNjZhMzliYjEyZTMiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3L3YyLjAiLCJpYXQiOjE1MzcyMzEwNDgsIm5iZiI6MTUzNzIzMTA0OCwiZXhwIjoxNTM3MjM0OTQ4LCJhaW8iOiJBWFFBaS84SUFBQUF0QWFaTG8zQ2hNaWY2S09udHRSQjdlQnE0L0RjY1F6amNKR3hQWXkvQzNqRGFOR3hYZDZ3TklJVkdSZ2hOUm53SjFsT2NBbk5aY2p2a295ckZ4Q3R0djMzMTQwUmlvT0ZKNGJDQ0dWdW9DYWcxdU9UVDIyMjIyZ0h3TFBZUS91Zjc5UVgrMEtJaWpkcm1wNjlSY3R6bVE9PSIsImF6cCI6IjZlNzQxNzJiLWJlNTYtNDg0My05ZmY0LWU2NmEzOWJiMTJlMyIsImF6cGFjciI6IjAiLCJuYW1lIjoiQWJlIExpbmNvbG4iLCJvaWQiOiI2OTAyMjJiZS1mZjFhLTRkNTYtYWJkMS03ZTRmN2QzOGU0NzQiLCJwcmVmZXJyZWRfdXNlcm5hbWUiOiJhYmVsaUBtaWNyb3NvZnQuY29tIiwicmgiOiJJIiwic2NwIjoiYWNjZXNzX2FzX3VzZXIiLCJzdWIiOiJIS1pwZmFIeVdhZGVPb3VZbGl0anJJLUtmZlRtMjIyWDVyclYzeERxZktRIiwidGlkIjoiNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3IiwidXRpIjoiZnFpQnFYTFBqMGVRYTgyUy1JWUZBQSIsInZlciI6IjIuMCJ9.pj4N-w_3Us9DrBLfpCt)에서 이 v2.0 토큰을 확인합니다.

## <a name="claims-in-access-tokens"></a>액세스 토큰의 클레임

JWT는 세 부분으로 분할됩니다.

* **헤더** - 토큰 유형과 서명 방법에 대한 정보를 포함하여 [토큰의 유효성을 검사](#validating-tokens)하는 방법에 대한 정보를 제공합니다.
* **페이로드** - 서비스를 호출하려고 하는 사용자 또는 앱에 대한 모든 중요 데이터가 포함되어 있습니다.
* **서명** - 토큰의 유효성을 검사하는 데 사용되는 원시 자료입니다.

각 부분은 마침표(`.`)로 구분되며 개별적으로 Base64로 인코딩됩니다.

클레임은 채울 값이 존재하는 경우에만 제공됩니다. 따라서 앱은 존재 하는 클레임에 대 한 종속성을 사용 하지 않아야 합니다. 예를 들면 `pwd_exp` (모든 테 넌 트가 암호를 요구 하지 않음) 또는 `family_name` (클라이언트 자격 증명 ([v1.0, v2.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) [) 흐름](v2-oauth2-client-creds-grant-flow.md)은 이름이 없는 응용 프로그램을 대신 함)입니다. 액세스 토큰 유효성 검사에 사용되는 클레임은 항상 제공됩니다.

> [!NOTE]
> 일부 클레임은 Azure AD에서 토큰을 다시 사용하는 경우 안전하게 보호하는 데 사용됩니다. 이러한 클레임은 설명에 "불투명"으로 되어 공개 사용되지 않는 것으로 표시됩니다. 이러한 클레임은 토큰에 표시되거나 표시되지 않을 수 있으며, 통지 없이 새로 추가될 수도 있습니다.

### <a name="header-claims"></a>헤더 클레임

|클레임 | 형식 | Description |
|--------|--------|-------------|
| `typ` | 문자열 - 항상 "JWT" | 토큰이 JWT임을 나타냅니다.|
| `nonce` | String | 토큰 재생 공격으로부터 보호하기 위해 사용되는 고유 식별자입니다. 리소스에서 이 값을 기록하면 재생을 방지할 수 있습니다. |
| `alg` | String | 토큰에 서명하는 데 사용된 알고리즘을 나타냅니다(예: "RS256"). |
| `kid` | String | 이 토큰에 서명하는 데 사용되는 공개 키의 지문을 지정합니다. v1.0 및 v2.0 액세스 토큰으로 내보냅니다. |
| `x5t` | String | 사용 및 값에서 `kid`와 동일하게 작동합니다. `x5t`는 호환성을 위해 v1.0 액세스 토큰으로만 내보내는 레거시 클레임입니다. |

### <a name="payload-claims"></a>페이로드 클레임

| 클레임 | 형식 | Description |
|-----|--------|-------------|
| `aud` | 문자열, 앱 ID URI | 토큰의 의도한 수신자를 식별합니다. Id 토큰에서 대상 그룹은 앱의 응용 프로그램 ID 이며 Azure Portal에서 앱에 할당 됩니다. 앱은 이 값의 유효성을 검사하고 값이 일치하지 않을 경우 토큰을 거부해야 합니다. |
| `iss` | 문자열, STS URI | 토큰을 생성하고 반환하는 STS(보안 토큰 서비스) 및 사용자가 인증된 Azure AD 테넌트를 식별합니다. 발급된 토큰은 v2.0 토큰이며(`ver` 클레임 참조), URI는 `/v2.0`에서 종료됩니다. 사용자가 Microsoft 계정의 소비자 사용자임을 나타내는 GUID는 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. 앱은 클레임의 GUID 부분을 사용하여 앱에 로그인할 수 있는 테넌트 집합을 제한할 수 있습니다(해당되는 경우). |
|`idp`| 문자열, 대개 STS URI입니다. | 토큰의 주체를 인증한 ID 공급자를 기록합니다. 이 값은 사용자 계정이 발행자(예: 게스트)와 동일한 테넌트에 속하지 않는 경우 발행자 클레임의 값과 동일합니다. 클레임이 없는 경우에는 `iss` 값을 대신 사용할 수 있음을 의미 합니다.  조직 컨텍스트(예: Azure AD 테넌트에 초대된 개인 계정)에서 사용되는 개인 계정의 경우, `idp` 클레임은 ‘live.com’ 또는 Microsoft 계정 테넌트 `9188040d-6c67-4c5b-b112-36a304b66dad`가 포함된 STS URI일 수 있습니다. |  
| `iat` | int, UNIX 타임스탬프 | "발급 시간"은 이 토큰에 대한 인증이 발생한 시간을 나타냅니다. |
| `nbf` | int, UNIX 타임스탬프 | "nbf"(not before) 클레임은 JWT가 그 이전에는 처리를 허용하지 않아야 하는 시간을 식별합니다. |
| `exp` | int, UNIX 타임스탬프 | "exp"(만료 시간) 클레임은 JWT가 그 이후에는 처리를 허용하지 않아야 하는 만료 시간을 식별합니다. 인증 변경이 필요한 경우나 토큰 해지가 감지된 경우에도 이 시간 전에는 리소스가 토큰을 거부할 수 있습니다. |
| `aio` | 불투명 문자열 | Azure AD에서 토큰을 다시 사용하기 위해 데이터를 기록하는 데 사용하는 내부 클레임입니다. 리소스에서는 이 클레임을 사용하지 않아야 합니다. |
| `acr` | 문자열, "0" 또는 "1" | v1.0 토큰에만 제공됩니다. “인증 컨텍스트 클래스” 클레임. 값 "0"은 최종 사용자 인증이 ISO/IEC 29115 요구 사항을 충족하지 못했다는 뜻입니다. |
| `amr` | 문자열의 JSON 배열 | v1.0 토큰에만 제공됩니다. 토큰의 주체가 인증된 방법을 식별합니다. 자세한 내용은 [amr 클레임 섹션](#the-amr-claim)을 참조하세요. |
| `appid` | 문자열, GUID | v1.0 토큰에만 제공됩니다. 토큰을 사용하는 클라이언트의 애플리케이션 ID입니다. 애플리케이션은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 애플리케이션 ID는 일반적으로 애플리케이션 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다. |
| `appidacr` | "0", "1" 또는 "2" | v1.0 토큰에만 제공됩니다. 클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값은 "0"입니다. 클라이언트 ID 및 클라이언트 비밀이 사용되는 경우 값은 "1"입니다. 클라이언트 인증서가 인증에 사용된 경우 값은 "2"입니다. |
| `azp` | 문자열, GUID | `appid`에 대 한 대체 인 v2.0 토큰에만 제공 됩니다. 토큰을 사용하는 클라이언트의 애플리케이션 ID입니다. 애플리케이션은 자체적으로 작동할 수도 있고 사용자를 대신하여 작동할 수도 있습니다. 애플리케이션 ID는 일반적으로 애플리케이션 개체를 나타내지만 Azure AD의 서비스 사용자 개체를 나타낼 수도 있습니다. |
| `azpacr` | "0", "1" 또는 "2" | `appidacr`에 대 한 대체 인 v2.0 토큰에만 제공 됩니다. 클라이언트가 인증된 방법을 나타냅니다. 공용 클라이언트의 경우 값은 "0"입니다. 클라이언트 ID 및 클라이언트 비밀이 사용되는 경우 값은 "1"입니다. 클라이언트 인증서가 인증에 사용된 경우 값은 "2"입니다. |
| `preferred_username` | String | 사용자를 나타내는 기본 사용자 이름입니다. 메일 주소, 전화 번호 또는 지정된 형식이 없는 일반 사용자 이름일 수 있습니다. 해당 값은 변경 가능하며 시간이 지남에 따라 변경될 수 있습니다. 해당 값은 변경 가능하므로 권한 부여 결정을 내리는 데 사용되지 않아야 합니다.  사용자 이름 힌트에 사용할 수 있습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| `name` | String | 사람이 인식할 수 있으며 토큰의 주체를 식별하는 값을 제공합니다. 이 값은 반드시 고유한 것은 아니며 변경 가능하고 표시 용도로만 사용하도록 디자인되었습니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| `scp` | 문자열, 공백으로 구분된 범위 목록 | 클라이언트 애플리케이션이 동의를 요청(및 수신)한 애플리케이션에 의해 노출된 범위 집합입니다. 앱은 이러한 범위가 앱에 의해 노출된 유효한 범위인지 확인하고 이러한 범위의 값을 기반으로 권한 부여 결정을 해야 합니다. [사용자 토큰](#user-and-application-tokens)에 대해서만 포함됩니다. |
| `roles` | 문자열 배열, 사용 권한 목록 | 응용 프로그램에서 요청 하는 응용 프로그램 또는 사용자에 게 호출 권한이 부여 된 사용 권한 집합입니다. [응용 프로그램 토큰](#user-and-application-tokens)의 경우 사용자 범위 대신 클라이언트 자격 증명[흐름 (v1.0](../azuread-dev/v1-oauth2-client-creds-grant-flow.md), v2.0 [) 중](v2-oauth2-client-creds-grant-flow.md)에 사용 됩니다.  [사용자 토큰](#user-and-application-tokens) 의 경우 대상 응용 프로그램에서 사용자가 할당 된 역할로 채워집니다. |
| `wids` | [RoleTemplateID](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids) guid의 배열 | [관리 역할 페이지](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#role-template-ids)의 역할 섹션에서이 사용자에 게 할당 된 테 넌 트 전체 역할을 나타냅니다.  이 클레임은 응용 프로그램 [매니페스트의](reference-app-manifest.md)`groupMembershipClaims` 속성을 통해 응용 프로그램 별로 구성 됩니다.  "모두" 또는 "DirectoryRole"로 설정 해야 합니다.  토큰 길이 문제로 인해 암시적 흐름을 통해 가져온 토큰에는 없을 수 있습니다. |
| `groups` | GUID의 JSON 배열 | 주체의 그룹 멤버 자격을 나타내는 개체 ID를 제공합니다. 이러한 값은 고유하며(개체 ID 참조) 리소스 액세스 시 강제로 인증하게 하는 경우처럼 액세스 관리에 안전하게 사용할 수 있습니다. 그룹 클레임에 포함된 그룹은 `groupMembershipClaims`애플리케이션 매니페스트[의 ](reference-app-manifest.md) 속성을 통해 애플리케이션별로 구성됩니다. Null 값은 모든 그룹을 제외하고, "SecurityGroup" 값은 Active Directory 보안 그룹 멤버 자격만 포함하고, "All" 값은 보안 그룹과 Office 365 메일 그룹을 모두 포함합니다. <br><br>암시적 권한 부여가 있는 `hasgroups` 클레임 사용에 대한 자세한 내용은 아래 `groups` 클레임을 참조하세요. <br>다른 흐름의 경우 사용자가 있는 그룹 수가 한도 (SAML의 경우 150, JWT의 경우 200)를 초과 하면 사용자의 그룹 목록이 포함 된 AAD Graph 끝점을 가리키는 클레임 원본에 초과분 클레임이 추가 됩니다. |
| `hasgroups` | 부울 | 있는 경우 항상 `true`로, 사용자 하나 이상의 그룹에 있음을 나타냅니다. 전체 그룹 클레임이 URI 조각을 URL 길이 한도(현재 6개 이상 그룹)를 초과하여 확장할 경우 암시적 권한 부여 흐름에서 JWT에 대해 `groups` 클레임 대신 사용됩니다. 클라이언트가 Graph를 사용하여 사용자 그룹(`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`)을 결정해야 함을 표시합니다. |
| `groups:src1` | JSON 개체 | 길이는 제한되지 않으나(위의 `hasgroups` 참조) 토큰에게는 너무 큰 토큰 요청의 경우 사용자의 전체 그룹 목록에 대한 링크가 포함됩니다. 분산된 클레임으로서의 JWT인 경우 SAML이 `groups` 클레임 대신에 새 클레임이 됩니다. <br><br>**JWT 값 예제**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }` |
| `sub` | 문자열, GUID | 앱 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어, 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행하고 데이터베이스 테이블에서 키로 사용할 수 있습니다. Azure AD에서 발급하는 토큰에는 항상 주체가 있기 때문에 이 값을 일반 용도의 인증 시스템에 사용하는 것이 좋습니다. 그러나 주체는 쌍으로 된 식별자이며 특정 애플리케이션 ID에 고유합니다. 따라서 단일 사용자가 두 개의 다른 클라이언트 ID를 사용하여 두 개의 다른 앱에 로그인하는 경우 해당 앱은 주체 클레임에 두 개의 다른 값을 받게 됩니다. 아키텍처 및 개인 정보 보호 요구 사항에 따라 적합할 수도 있고 적합하지 않을 수도 있습니다. 또한 테 넌 트 내의 앱에서 동일 하 게 유지 되는 `oid` 클레임을 참조 하세요. |
| `oid` | 문자열, GUID | Microsoft ID 플랫폼에 있는 개체의 변경할 수 없는 식별자로, 이 경우 사용자 계정입니다. 데이터베이스 테이블의 키로써 안전하게 권한 부여 검사를 수행하는 데 사용할 수도 있습니다. 이 ID는 애플리케이션에서 사용자를 고유하게 식별합니다. 동일한 사용자가 로그인한 두 개의 다른 애플리케이션은 `oid` 클레임에서 동일한 값을 받습니다. 즉, Microsoft Graph와 같은 Microsoft 온라인 서비스에 대한 쿼리를 수행할 때 `oid`를 사용할 수 있습니다. Microsoft Graph는 지정 된 [사용자 계정](/graph/api/resources/user)에 대 한 `id` 속성으로이 ID를 반환 합니다. `oid`를 사용하면 여러 앱에서 사용자의 상관 관계를 지정하기 때문에 이 클레임을 수신하기 위해 `profile` 범위가 필요합니다. 단일 사용자가 여러 테넌트에 존재하는 경우 사용자는 각 테넌트에서 다른 개체 ID를 포함합니다. 사용자가 동일한 자격 증명으로 각 계정에 로그인하더라도 서로 다른 계정으로 간주됩니다. |
| `tid` | 문자열, GUID | 사용자가 속해 있는 Azure AD 테넌트를 나타냅니다. 회사 및 학교 계정의 경우 GUID는 사용자가 속해 있는 조직의 변경이 불가능한 테넌트 ID입니다. 개인 계정의 경우 이 값은 `9188040d-6c67-4c5b-b112-36a304b66dad`입니다. `profile` 범위는 이 클레임을 받기 위해 필요합니다. |
| `unique_name` | String | v1.0 토큰에만 제공됩니다. 토큰의 주체를 식별하는, 사람이 인식할 수 있는 값을 제공합니다. 이 값은 테넌트 내에서 반드시 고유한 것은 아니며 표시 용도로만 사용해야 합니다. |
| `uti` | 불투명 문자열 | Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 리소스는이 클레임을 사용 하지 않아야 합니다. |
| `rh` | 불투명 문자열 | Azure에서 토큰의 유효성을 다시 검사하기 위해 사용하는 내부 클레임입니다. 리소스에서는 이 클레임을 사용하지 않아야 합니다. |
| `ver` | 문자열, `1.0` 또는 `2.0` | 액세스 토큰의 버전을 나타냅니다. |


> [!NOTE]
> **그룹 초과분 클레임**
>
> 토큰 크기가 HTTP 헤더 크기 제한을 초과 하지 않도록 하기 위해 Azure AD는 그룹 클레임에 포함 된 개체 Id의 수를 제한 합니다. 사용자가 초과분 제한 (SAML 토큰의 경우 150, JWT 토큰의 경우 200) 보다 많은 그룹의 멤버인 경우 Azure AD는 토큰의 그룹 클레임을 내보내지 않습니다. 대신 사용자의 그룹 멤버 자격을 검색 하는 Graph API을 쿼리하도록 응용 프로그램을 나타내는 토큰의 초과분 클레임이 포함 됩니다.
  ```csharp
  {
    ...
    "_claim_names": {
     "groups": "src1"
      },
      {
    "_claim_sources": {
      "src1": {
          "endpoint":"[Graph Url to get this user's group membership from]"
          }
         }
       }
    ...
   }
   ```
> [앱 생성 스크립트](https://github.com/Azure-Samples/active-directory-dotnet-webapp-groupclaims/blob/master/AppCreationScripts/) 폴더에 제공 된 `BulkCreateGroups.ps1`를 사용 하 여 초과분 시나리오를 테스트할 수 있습니다.

#### <a name="v10-basic-claims"></a>v1.0 기본 클레임

다음 클레임은 (해당 하는 경우) v 1.0 토큰에 포함 되지만, 기본적으로 v2.0 토큰에는 포함 되지 않습니다. V 2.0을 사용 하 고 이러한 클레임 중 하나가 필요한 경우 [선택적 클레임](active-directory-optional-claims.md)을 사용 하 여 요청 합니다.

| 클레임 | 형식 | Description |
|-----|--------|-------------|
| `ipaddr`| String | 사용자가 인증된 IP 주소입니다. |
| `onprem_sid`| 문자열, [SID 형식](https://docs.microsoft.com/windows/desktop/SecAuthZ/sid-components) | 사용자에게 온-프레미스 인증이 있는 경우 이 클레임이 해당 SID를 제공합니다. 레거시 애플리케이션에서 권한 부여에 `onprem_sid`를 사용할 수 있습니다.|
| `pwd_exp`| int, UNIX 타임스탬프 | 사용자의 암호가 만료되는 시기를 나타냅니다. |
| `pwd_url`| String | 암호를 재설정하도록 사용자에게 보낼 수 있는 URL입니다. |
| `in_corp`| boolean | 클라이언트가 회사 네트워크에서 로그인하는 경우 알립니다. 그렇지 않으면 클레임이 포함 되지 않습니다. |
| `nickname`| String | 이름 및 성과는 별개인 사용자의 추가 이름입니다.|
| `family_name` | String | 사용자 개체에 정의된 대로 사용자의 성을 제공합니다. |
| `given_name` | String | 사용자 개체에 설정된 대로 사용자의 이름을 제공합니다. |
| `upn` | String | 사용자의 사용자 이름입니다. 전화 번호, 이메일 주소 또는 형식이 지정되지 않은 문자열일 수 있습니다. 표시 목적 및 재인증 시나리오에서 사용자 이름 힌트를 제공하기 위해서만 사용해야 합니다. |

#### <a name="the-amr-claim"></a>`amr` 클레임

Microsoft id는 응용 프로그램과 관련이 있을 수 있는 다양 한 방법으로 인증할 수 있습니다. `amr` 클레임은 암호 및 Authenticator 앱을 모두 사용하는 인증을 위해 여러 항목(예: `["mfa", "rsa", "pwd"]`)을 포함할 수 있는 배열입니다.

| 값 | Description |
|-----|-------------|
| `pwd` | 암호 인증으로, 사용자의 Microsoft 암호 또는 앱의 클라이언트 비밀 중 하나입니다. |
| `rsa` | 인증은 [Microsoft Authenticator 앱](https://aka.ms/AA2kvvu) 등을 사용하여 RSA 키 증명을 기반으로 수행되었습니다. 여기에는 서비스 소유의 X509 인증서를 사용 하 여 자체 서명 된 JWT에서 인증이 수행 된 경우이 포함 됩니다. |
| `otp` | 이메일 또는 문자 메시지를 사용하는 OTP(일회성 암호)입니다. |
| `fed` | 페더레이션 인증 어설션(예: JWT 또는 SAML)이 사용되었습니다. |
| `wia` | Windows 통합 인증 |
| `mfa` | 다단계 인증이 사용되었습니다. 이 인증이 있는 경우 다른 인증 방법도 포함됩니다. |
| `ngcmfa` | `mfa`와 동일하며 특정 고급 자격 증명 유형의 프로비전에 사용됩니다. |
| `wiaormfa`| 사용자가 Windows 또는 MFA 자격 증명을 사용하여 인증했습니다. |
| `none` | 인증이 수행 되지 않았습니다. |

## <a name="validating-tokens"></a>토큰 유효성 검사

id_token 또는 access_token의 유효성을 검사하려면 앱이 토큰의 서명과 클레임의 유효성을 모두 검사해야 합니다. 액세스 토큰의 유효성을 검사 하기 위해 앱은 발급자, 대상 그룹 및 서명 토큰의 유효성도 검사 해야 합니다. OpenID 검색 문서에 있는 값에 대해 유효성 검사를 수행해야 합니다. 예를 들어 테 넌 트 독립적 버전의 문서는 [https://login.microsoftonline.com/common/.well-known/openid-configuration](https://login.microsoftonline.com/common/.well-known/openid-configuration)에 있습니다.

Azure AD 미들웨어에는 액세스 토큰의 유효성을 검사하는 기본 제공 기능이 있으며 [샘플](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples)을 탐색하여 원하는 언어로 찾을 수 있습니다. JWT 토큰의 유효성 검사를 명시적으로 수행하는 방법은 [수동 JWT 유효성 검사 샘플](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation)을 참조하세요.

토큰 유효성 검사를 쉽게 처리하는 방법을 보여 주는 라이브러리와 코드 샘플이 제공됩니다. 아래 정보는 기본 프로세스를 이해하고자 하는 사용자에게 제공됩니다. JWT 유효성 검사에 사용할 수 있는 여러 타사 오픈 소스 라이브러리도 있습니다. 거의 모든 플랫폼 및 언어에 대 한 옵션은 하나 이상 있습니다. Azure AD 인증 라이브러리 및 코드 샘플에 대한 자세한 내용은 [v1.0 인증 라이브러리](../azuread-dev/active-directory-authentication-libraries.md) 및 [v2.0 인증 라이브러리](reference-v2-libraries.md)를 참조하세요.

### <a name="validating-the-signature"></a>서명 유효성 검사

JWT에는 `.` 문자로 구분된 세 개의 세그먼트가 포함되어 있습니다. 첫 번째 세그먼트는 **헤더**, 두 번째 세그먼트는 **본문**, 세 번째 세그먼트는 **서명**이라고 합니다. 서명 세그먼트는 앱이 신뢰할 수 있도록 토큰의 신뢰성이 유효한지 검사하는 데 사용할 수 있습니다.

Azure AD에서 발급된 토큰은 RSA 256 등의 업계 표준 비대칭 암호화 알고리즘을 사용하여 서명됩니다. JWT의 헤더에는 토큰 서명에 사용된 키 및 암호화 방법에 대한 정보가 들어 있습니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk",
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

`alg` 클레임은 토큰을 서명 하는 데 사용 된 알고리즘을 나타내며 `kid` 클레임은 토큰의 유효성을 검사 하는 데 사용 된 특정 공개 키를 나타냅니다.

특정 시점에 Azure AD는퍼블릭-프라이빗 키 쌍의 특정 집합 중 하나를 사용하여 id_token에 서명할 수 있습니다. Azure AD는 주기적으로 가능한 키 집합을 순환하므로 이러한 키 변경을 자동으로 처리하도록 앱을 작성해야 합니다. Azure AD에서 사용된 공개 키에 대한 업데이트를 확인하는 적절한 빈도는 24시간마다입니다.

다음 위치에 있는 [Openid connect Connect 메타 데이터 문서](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document) 를 사용 하 여 서명의 유효성을 검사 하는 데 필요한 서명 키 데이터를 가져올 수 있습니다.

```
https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration
```

> [!TIP]
> 브라우저에서 이 [URL](https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration)을 사용해 보세요!

이 메타데이터 문서는 다음과 같은 특징이 있습니다.

* 는 Openid connect Connect 인증을 수행 하는 데 필요한 다양 한 끝점의 위치와 같은 몇 가지 유용한 정보를 포함 하는 JSON 개체입니다.
* 토큰 서명에 사용되는 공개 키 집합의 위치를 제공하는 `jwks_uri`를 포함합니다. `jwks_uri`에 있는 JSON 웹 키 (JWK)는 특정 시점에 사용 중인 공개 키 정보를 모두 포함 합니다.  JWK 형식은 [RFC 7517](https://tools.ietf.org/html/rfc7517)에 설명 되어 있습니다.  앱은 JWT 헤더에 `kid` 클레임을 사용하여 토큰 서명에 사용된 공개 키를 이 문서에서 선택할 수 있습니다. 그런 다음 올바른 공개 키와 표시 된 알고리즘을 사용 하 여 서명 유효성 검사를 수행할 수 있습니다.

> [!NOTE]
> v1.0 엔드포인트는 `x5t` 및 `kid` 클레임을 둘 다 반환하지만, v2.0 끝점은 `kid` 클레임으로만 응답합니다. 앞으로는 `kid` 클레임을 사용하여 토큰의 유효성을 검사하는 것이 좋습니다.

서명 유효성 검사는이 문서의 범위를 벗어납니다. 필요한 경우 이러한 작업을 수행 하는 데 사용할 수 있는 많은 오픈 소스 라이브러리가 있습니다.  그러나 Microsoft Id 플랫폼은 표준 사용자 지정 서명 키에 대 한 하나의 토큰 서명 확장을 포함 합니다.  

[클레임 매핑](active-directory-claims-mapping.md) 기능을 사용한 결과로 앱에 사용자 지정 서명 키가 있는 경우 앱 ID가 포함 된 `appid` 쿼리 매개 변수를 추가 하 여 앱의 서명 키 정보를 가리키는 `jwks_uri`를 가져와야 합니다 .이 정보는 유효성 검사에 사용 해야 합니다. 예를 들어 `https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration?appid=6731de76-14a6-49ae-97bc-6eba6914391e`에는 `https://login.microsoftonline.com/{tenant}/discovery/keys?appid=6731de76-14a6-49ae-97bc-6eba6914391e``jwks_uri` 포함 되어 있습니다.

### <a name="claims-based-authorization"></a>클레임 기반 권한 부여

애플리케이션의 비즈니스 논리에 따라 이 단계가 결정되며 몇 가지 일반적인 권한 부여 방법이 아래에 나와 있습니다.

* `scp` 또는 `roles` 클레임을 확인 하 여 현재 모든 범위가 API에 의해 노출 된 범위와 일치 하는지 확인 하 고 클라이언트가 요청 된 작업을 수행할 수 있도록 합니다.
* 호출하는 클라이언트가 `appid` 클레임을 사용하여 API를 호출할 수 있는지 확인합니다.
* `appidacr`를 사용 하 여 호출 클라이언트의 인증 상태를 확인 합니다. 공용 클라이언트에서 API 호출을 허용 하지 않는 경우 0이 아니어야 합니다.
* 이전 `nonce` 클레임 목록을 확인 하 여 토큰이 재생 되 고 있지 않은지 확인 합니다.
* `tid`가 API를 호출할 수 있는 테넌트와 일치하는지 확인합니다.
* 사용자가 MFA를 수행했는지 확인하려면 `acr` 클레임을 사용합니다. [조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)를 사용 하 여 적용 해야 합니다.
* 액세스 토큰에서 `roles` 또는 `groups` 클레임을 요청한 경우이 작업을 수행할 수 있는 그룹에 사용자가 있는지 확인 합니다.
  * 암시적 흐름을 사용하여 검색한 토큰의 경우 종종 너무 커서 토큰에 맞지 않으므로 이 데이터에 대해 [Microsoft Graph](https://developer.microsoft.com/graph/)를 쿼리해야 할 수 있습니다.

## <a name="user-and-application-tokens"></a>사용자 및 애플리케이션 토큰

응용 프로그램은 사용자 (일반적인 흐름)를 대신 하 여 [토큰을 받거나](v2-oauth2-client-creds-grant-flow.md)클라이언트 자격 증명 흐름 (v1.0, v2.0)을 통해 응용 프로그램에서 직접[토큰을 받을](../azuread-dev/v1-oauth2-client-creds-grant-flow.md)수 있습니다. 이러한 앱 전용 토큰은 이 호출이 애플리케이션에서 발생하며 사용자가 이를 지원하는 것이 아님을 나타냅니다. 이러한 토큰은 대체로 동일하게 처리되며 약간의 차이점이 있습니다.

* 앱 전용 토큰에는 `scp` 클레임이 없으며 대신 `roles` 클레임이 있을 수 있습니다. 애플리케이션 권한(위임된 권한과 반대)이 기록되는 위치입니다. 위임 된 권한 및 응용 프로그램 권한에 대 한 자세한 내용은 사용 권한 및[동의 (](../azuread-dev/v1-permissions-consent.md)v1.0 [, v2.0](v2-permissions-and-consent.md))를 참조 하세요.
* `name` 또는 `upn`와 같이 많은 사용자 관련 클레임이 누락 됩니다.
* `sub` 및 `oid` 클레임은 동일 합니다. 

## <a name="token-revocation"></a>토큰 해지

새로 고침 토큰은 다양 한 이유로 언제 든 지 무효화 되거나 취소 될 수 있습니다. 이러한 이유는 시간 제한 및 해지 등 두 가지 주요 범주로 나뉩니다.

### <a name="token-timeouts"></a>토큰 제한 시간

* MaxInactiveTime: MaxInactiveTime에 의해 결정 된 시간 내에 새로 고침 토큰을 사용 하지 않은 경우 새로 고침 토큰이 더 이상 유효 하지 않게 됩니다.
* MaxSessionAge: MaxAgeSessionMultiFactor 또는 MaxAgeSessionSingleFactor가 (해지될 때까지) 기본값 이외의 값으로 설정된 경우 MaxAgeSession*에 설정된 시간이 경과된 후에 다시 인증해야 합니다.
* 예제:
  * 테 넌 트는 MaxInactiveTime을 5 일로 설정 하 고, 사용자가 일주일 동안 휴가를 시도 했으므로 Azure AD는 7 일 동안 사용자의 새 토큰 요청을 본 적이 없습니다. 다음 번에 사용자가 새 토큰을 요청 하면 새로 고침 토큰이 해지 된 것을 확인 하 고 자격 증명을 다시 입력 해야 합니다.
  * 중요 한 응용 프로그램에는 1 일의 MaxAgeSessionSingleFactor가 있습니다. 사용자가 월요일에 로그인 하 고 화요일에 (25 시간이 경과한 후) 다시 인증 해야 합니다.

### <a name="revocation"></a>해지

|   | 암호 기반 쿠키 | 암호 기반 토큰 | 비암호 기반 쿠키 | 비암호 기반 토큰 | 기밀 클라이언트 토큰 |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| 암호 만료 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자에 의해 암호가 변경됨 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 SSPR 수행 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 관리자가 암호 재설정 | 해지됨 | 해지됨 | 활성 상태 | 활성 상태 | 활성 상태 |
| 사용자가 [PowerShell을 통해](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) 새로 고침 토큰 해지 | 해지됨 | 해지됨 | 해지됨 | 해지됨 | 해지됨 |
| 관리자가 [PowerShell을 통해](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) 테넌트에 대한 모든 새로 고침 토큰 해지 | 해지됨 | 해지됨 |해지됨 | 해지됨 | 해지됨 |
| 웹의 Single[sign-on (v1.0](../azuread-dev/v1-protocols-openid-connect-code.md#single-sign-out) [, v2.0](v2-protocols-oidc.md#single-sign-out) ) | 해지됨 | 활성 상태 | 해지됨 | 활성 상태 | 활성 상태 |

> [!NOTE]
> "비 암호 기반" 로그인은 사용자가 가져오도록 암호를 입력하지 않은 위치입니다. 예를 들어 Windows Hello, FIDO2 키 또는 PIN을 사용 하 여 얼굴을 사용 합니다.
>
> Windows 10의 기본 새로 고침 토큰 (PRT)은 자격 증명에 따라 분리 됩니다. 예를 들어 Windows Hello 및 암호에는 서로 격리 된 개별 PRTs가 있습니다. 사용자가 Hello 자격 증명 (PIN 또는 생체 인식)을 사용 하 여 로그인 한 후 암호를 변경 하면 이전에 얻은 암호 기반 PRT가 해지 됩니다. 암호를 사용 하 여 다시 로그인 하면 이전 PRT가 무효화 되 고 새 PRT가 요청 됩니다.
>
> 새로 고침 토큰은 새 액세스 토큰과 새로 고침 토큰을 가져오는 데 사용 될 때 무효화 되거나 취소 되지 않습니다.  

## <a name="next-steps"></a>다음 단계

* [Azure AD에서 `id_tokens`](id-tokens.md)에 대해 알아봅니다.
* 사용 권한 및 [동의 (v1.0](../azuread-dev/v1-permissions-consent.md) [, v2.0](v2-permissions-and-consent.md))에 대해 알아봅니다.
