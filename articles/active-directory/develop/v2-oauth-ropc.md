---
title: 리소스 소유자 암호 자격 증명 (ROPC) 권한 부여를 사용 하 여 사용자 로그인을 사용 하 여 Microsoft id 플랫폼 | Azure
description: 리소스 소유자 암호 자격 증명 권한 부여를 사용하여 브라우저 없는 인증 흐름을 지원합니다.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cfa28cae87c8a9a97e1c64b96f75ae4c6eab08d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62112292"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-resource-owner-password-credential"></a>Microsoft id 플랫폼 및 OAuth 2.0 리소스 소유자 암호 자격 증명

Microsoft identity 플랫폼에서 지 원하는 [리소스 소유자 암호 자격 증명 (ROPC) 부여](https://tools.ietf.org/html/rfc6749#section-4.3), 응용 프로그램 사용자가 암호를 직접 처리 하 여 로그인 할 수 있습니다. ROPC 흐름은 높은 수준의 신뢰 및 사용자 노출 하며 다른, 보다 안전한 흐름을 사용할 수 없는 경우이 흐름에만 사용 해야 합니다.

> [!IMPORTANT]
>
> * Microsoft id 플랫폼 끝점은 Azure AD 테 넌 트에 없습니다 개인 계정에 대 한 ROPC만 지원합니다. 이는 테넌트별 엔드포인트(`https://login.microsoftonline.com/{TenantId_or_Name}`) 또는 `organizations` 엔드포인트를 사용해야 함을 의미합니다.
> * Azure AD 테넌트에 초대된 개인 계정은 ROPC를 사용할 수 없습니다.
> * 암호가 없는 계정은 ROPC를 통해 로그인할 수 없습니다. 이 경우 앱에 다른 흐름을 사용하는 것이 좋습니다.
> * 사용자가 MFA(Multi-Factor Authentication)를 사용하여 애플리케이션에 로그인해야 하는 경우 사용자가 차단됩니다.

## <a name="protocol-diagram"></a>프로토콜 다이어그램

다음 다이어그램은 ROPC 흐름을 보여 줍니다.

![ROPC 흐름](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>권한 부여 요청

ROPC 흐름은 단일 요청임 &mdash; 클라이언트 식별 및 사용자의 자격 증명을 IDP에 보내 다음, 반환으로 토큰을 수신합니다. 클라이언트는 작업을 수행하기 전에 사용자의 메일 주소(UPN) 및 암호를 요청해야 합니다. 요청이 성공한 즉시 클라이언트는 메모리에서 사용자의 자격 증명을 안전하게 해제해야 합니다. 이 자격 증명을 저장하면 안 됩니다.

> [!TIP]
> Postman에서 이 요청을 실행해 보세요.
> [![Postman에서 실행](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
// Line breaks and spaces are for legibility only.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| 매개 변수 | 조건 | 설명 |
| --- | --- | --- |
| `tenant` | 필수 | 사용자를 로그인할 디렉터리 테넌트입니다. 이는 GUID 또는 친숙한 이름 형식일 수 있습니다. 이 매개 변수는 `common` 또는 `consumers`로 설정할 수 없고 `organizations`로 설정할 수 있습니다. |
| `grant_type` | 필수 | `password`로 설정해야 합니다. |
| `username` | 필수 | 사용자의 메일 주소입니다. |
| `password` | 필수 | 사용자 암호입니다. |
| `scope` | 권장 | 앱에 필요한 [범위](v2-permissions-and-consent.md) 또는 권한의 공백으로 구분된 목록입니다. 대화형 흐름을 사용자나 관리자가 사전에 이러한 범위에 동의 해야 합니다. |

### <a name="successful-authentication-response"></a>성공적인 인증 응답

다음 예제에서는 성공적인 토큰 응답을 보여 줍니다.

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

| 매개 변수 | 형식 | 설명 |
| --------- | ------ | ----------- |
| `token_type` | String | 항상 `Bearer`로 설정합니다. |
| `scope` | 공백으로 구분된 문자열 | 액세스 토큰이 반환된 경우 이 매개 변수는 액세스 토큰이 유효한 범위를 나열합니다. |
| `expires_in`| int | 포함된 액세스 토큰이 유효한 시간(초)입니다. |
| `access_token`| 불투명 문자열 | 요청된 [범위](v2-permissions-and-consent.md)에 대해 발급되었습니다. |
| `id_token` | JWT | 원래 `scope` 매개 변수에 `openid` 범위가 포함된 경우에 발급됩니다. |
| `refresh_token` | 불투명 문자열 | 원래 `scope` 매개 변수에 `offline_access`가 포함된 경우에 발급됩니다. |

새로 고침 토큰은 [OAuth 코드 흐름 문서](v2-oauth2-auth-code-flow.md#refresh-the-access-token)에 설명된 동일한 흐름을 사용하여 새 액세스 토큰과 새로 고침 토큰을 가져올 수 있습니다.

### <a name="error-response"></a>오류 응답

사용자가 올바른 사용자 이름 또는 암호를 입력하지 않았거나 클라이언트가 요청된 동의를 수신하지 못한 경우 인증에 실패합니다.

| 오류 | 설명 | 클라이언트 작업 |
|------ | ----------- | -------------|
| `invalid_grant` | 인증에 실패했습니다. | 자격 증명이 올바르지 않거나 클라이언트에 요청된 범위에 대한 동의가 없습니다. 범위를 부여 하지 하는 경우는 `consent_required` 오류가 반환 됩니다. 이 오류가 발생하면 클라이언트는 WebView 또는 브라우저를 사용하여 대화형 프롬프트로 사용자를 전송해야 합니다. |
| `invalid_request` | 요청이 잘못 구성되었습니다. | 권한 부여 유형이 지원 되지 않습니다 합니다 `/common` 또는 `/consumers` 인증 컨텍스트.  대신 `/organizations`를 사용하세요. |
| `invalid_client` | 앱이 잘못 설정되었습니다. | 이 경우에 발생할 수 있습니다는 `allowPublicClient` 속성으로 설정 되지 true 합니다 [응용 프로그램 매니페스트](reference-app-manifest.md)합니다. ROPC 권한 부여에 리디렉션 URI가 없으므로 `allowPublicClient` 속성이 필요합니다. 속성이 설정되지 않으면 Azure AD는 앱이 공용 클라이언트 애플리케이션 또는 기밀 클라이언트 애플리케이션인지 확인할 수 없습니다. ROPC 공용 클라이언트 앱 에서만 지원 됩니다. |

## <a name="learn-more"></a>자세한 정보

* [샘플 콘솔 애플리케이션](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)을 사용하여 직접 ROPC를 사용해 보세요.
* V2.0 끝점을 사용 해야 하는지 여부를 결정할에 대해 알아보세요 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.
