---
title: Microsoft ID 플랫폼 인증서 자격 증명
titleSuffix: Microsoft identity platform
description: 이 문서에서는 애플리케이션 인증을 위한 인증서 자격 증명의 등록 및 사용에 대해 설명합니다.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6330621aac78d5e9df52f2cd3ad9c3968bb0120d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853375"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft ID 플랫폼 애플리케이션 인증 인증서 자격 증명

Microsoft id 플랫폼을 사용 하면 응용 프로그램에서 인증에 자체 자격 증명을 사용할 수 있습니다. 예를 들어 OAuth 2.0  [클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md) 흐름 및 obo ( [on of](v2-oauth2-on-behalf-of-flow.md) ) 흐름에서 사용할 수 있습니다.

응용 프로그램이 인증에 사용할 수 있는 자격 증명의 한 가지 형태는 응용 프로그램이 소유 하는 인증서로 서명 된 JWT ( [JSON Web Token](./security-tokens.md#json-web-tokens-jwts-and-claims) ) 어설션입니다.

## <a name="assertion-format"></a>어설션 형식

어설션을 계산 하기 위해 원하는 언어의 많은 JWT 라이브러리 중 하나를 사용할 수 있습니다. 이 정보는 [헤더](#header), [클레임](#claims-payload)및 [시그니처의](#signature)토큰에 의해 전달 됩니다.

### <a name="header"></a>헤더

| 매개 변수 |  설명 |
| --- | --- |
| `alg` | **RS256**이어야 함 |
| `typ` | **JWT**여야 함 |
| `x5t` | Base64 문자열 값으로 인코드된 x.509 인증서 해시의 (인증서의 SHA-1 *지문이*라고도 함) 16 진수 표현입니다. 예를 들어 x.509 인증서 해시 `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (16 진수)가 지정 된 경우 클레임은 `x5t` `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64)가 됩니다. |

### <a name="claims-payload"></a>클레임(페이로드)

| 매개 변수 |  설명 |
| --- | --- |
| `aud` | 대상: `https://login.microsoftonline.com/<your-tenant-id>/oauth2/token` |
| `exp` | 만료 날짜: 토큰이 만료 되는 날짜입니다. 시간은 1970년 1월 1일(1970-01-01T0:0:0Z) UTC부터 토큰의 유효 기간이 만료될 때까지의 시간(초)으로 표시됩니다. 짧은 만료 시간 (10 분 ~ 1 시간)을 사용 하는 것이 좋습니다.|
| `iss` | 발급자: 클라이언트 서비스의 client_id (*응용 프로그램 (클라이언트) id* ) 여야 합니다. |
| `jti` | GUID: JWT ID |
| `nbf` | 이전이 아님: 토큰을 사용할 수 없는 날짜입니다. 시간은 어설션이 생성 될 때까지 1970 년 1 월 1 일 (1970-01-01T0:0: 0Z) UTC의 초 수로 표시 됩니다. |
| `sub` | Subject:의 경우 `iss` , client_id (클라이언트 서비스의*응용 프로그램 (클라이언트) id* ) 여야 합니다. |

### <a name="signature"></a>서명

시그니처는 [JSON WEB TOKEN RFC7519 사양](https://tools.ietf.org/html/rfc7519)에 설명 된 대로 인증서를 적용 하 여 계산 됩니다.

## <a name="example-of-a-decoded-jwt-assertion"></a>디코딩된 JWT 어설션 예제

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>인코딩된 JWT 어설션 예제

다음 문자열은 인코딩된 어설션의 예입니다. 신중히 살펴보면 다음 세 개의 섹션을 점 ()으로 구분 하는 것을 알 수 있습니다 `.` .

* 첫 번째 섹션은 *헤더* 를 인코딩합니다.
* 두 번째 섹션은 *클레임* (페이로드)을 인코딩합니다.
* 마지막 섹션은 처음 두 섹션의 콘텐츠에서 인증서를 사용 하 여 계산 되는 *서명* 입니다.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Microsoft ID 플랫폼에 인증서 등록

다음 방법 중 하나를 사용하여 Azure Portal을 통해 Microsoft ID 플랫폼에서 클라이언트 애플리케이션과 인증서 자격 증명을 연결할 수 있습니다.

### <a name="uploading-the-certificate-file"></a>인증서 파일 업로드

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **인증서 및 비밀**을 선택합니다.
2. **인증서 업로드**를 클릭하고 업로드할 인증서 파일을 선택합니다.
3. **추가**를 클릭합니다.
  인증서가 업로드되고 지문, 시작 날짜 및 만료 값이 표시됩니다.

### <a name="updating-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

인증서가 있을 때 다음을 컴퓨팅해야 합니다.

- `$base64Thumbprint` -B a s e 64로 인코딩된 인증서 해시 값
- `$base64Value` -B a s e 64로 인코딩된 인증서 원시 데이터 값

애플리케이션 매니페스트에서 키를 식별하는 GUID도 제공해야 합니다(`$keyId`).

클라이언트 애플리케이션에 대한 Azure 앱 등록에서:
1. **매니페스트**를 선택하여 애플리케이션 매니페스트를 엽니다.
2. 다음 스키마를 사용해서 *keyCredentials* 속성을 새 인증서 정보로 바꿉니다.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. 애플리케이션 매니페스트에 편집 내용을 저장한 다음, 매니페스트를 Microsoft ID 플랫폼에 업로드합니다.

   `keyCredentials` 속성은 다중 값이므로 풍부한 키 관리를 위해 여러 인증서를 업로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

GitHub의 [Microsoft identity platform code 샘플을 사용 하는 .Net Core 디먼 콘솔 응용 프로그램](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) 은 응용 프로그램에서 인증에 자체 자격 증명을 사용 하는 방법을 보여 줍니다. PowerShell cmdlet을 사용 하 여 [자체 서명 된 인증서를 만드는](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) 방법도 보여 줍니다 `New-SelfSignedCertificate` . 샘플 리포지토리의 [앱 생성 스크립트](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) 를 사용 하 여 인증서를 만들고 지문을 계산 하는 등의 작업도 수행할 수 있습니다.
