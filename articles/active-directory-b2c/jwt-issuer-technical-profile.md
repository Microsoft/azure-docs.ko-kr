---
title: 사용자 지정 정책에서 JWT 발급자의 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 JWT (JSON web token) 발급자에 대한 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f0adbac14c2ae886bc002ae56ab0784b608d1e5d
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841985"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 JWT 토큰 발급자의 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 각 인증 흐름을 처리할 때 여러 유형의 보안 토큰을 내보냅니다. JWT 토큰 발급자의 기술 프로필은 다시 신뢰 당사자 애플리케이션으로 반환되는 JWT 토큰을 내보냅니다. 일반적으로 이 기술 프로필은 사용자 경험의 마지막 오케스트레이션 단계입니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. **OutputTokenFormat** 요소를 `JWT`로 설정합니다.

다음 예제는 `JwtIssuer`의 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>입력, 출력 및 유지 클레임

**InputClaims**, **OutputClaims** 및 **PersistClaims** 요소가 비어 있거나 없습니다. **InutputClaimsTransformations** 및 **OutputClaimsTransformations** 요소도 없습니다.

## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | 예 | OAuth2 인증 코드 및 새로 고침 토큰 내에서 사용자 ID 클레임으로 사용해야 하는 클레임입니다. 기본적으로 다른 SubjectNamingInfo 클레임 형식을 지정하지 않는 한 `objectId`로 설정해야 합니다. |
| SendTokenResponseBodyWithJsonNumbers | 아닙니다. | 항상 `true`로 설정합니다. 숫자 값이 JSON 번호 대신 문자열로 제공되는 레거시 형식의 경우 `false`로 설정합니다. 이 특성은 해당 속성을 문자열로 반환한 이전 구현에서 종속성을 사용한 클라이언트에 필요합니다. |
| token_lifetime_secs | 아닙니다. | 액세스 토큰 수명입니다. 보호된 리소스에 대한 액세스 권한을 얻는 데 사용되는 OAuth 2.0 전달자 토큰의 수명입니다. 기본값은 3,600초(1시간)입니다. 최소값(포함)은 300초(5분)입니다. 최댓값(포함)은 86,400초(24시간)입니다. |
| id_token_lifetime_secs | 아닙니다. | ID 토큰 수명입니다. 기본값은 3,600초(1시간)입니다. 최소값(포함)은 300초(5분)입니다. 최댓값(포함)은 86,400초(24시간)입니다. |
| refresh_token_lifetime_secs | 아닙니다. | 새로 고침 토큰 수명입니다. 애플리케이션에 offline_access 범위가 부여된 경우 새 액세스 토큰을 획득하는 데 새로 고침 토큰을 사용할 수 있기 전까지의 최대 기간입니다. 기본값은 120,9600초(14일)입니다. 최소값(포함)은 86,400초(24시간)입니다. 최댓값(포함)은 7,776,000초(90일)입니다. |
| rolling_refresh_token_lifetime_secs | 아닙니다. | 새로 고침 토큰 슬라이딩 윈도우 수명입니다. 이 기간이 경과하면 애플리케이션이 획득한 대부분의 최근 새로 고침 토큰의 유효 기간에 관계없이 사용자는 강제로 다시 인증을 받게 됩니다. 슬라이딩 윈도우 수명을 적용하지 않으려면 allow_infinite_rolling_refresh_token 값을 `true`로 설정합니다. 기본값은 7,776,000초(90일)입니다. 최소값(포함)은 86,400초(24시간)입니다. 최댓값(포함)은 31,536,000초(365일)입니다. |
| allow_infinite_rolling_refresh_token | 아닙니다. | `true`로 설정하면 새로 고침 토큰 슬라이딩 윈도우 수명이 만료되지 않습니다. |
| IssuanceClaimPattern | 아닙니다. | 발급자(iss) 클레임을 제어합니다. 다음 값 중 하나입니다.<ul><li>AuthorityAndTenantGuid-iss 클레임은 `login.microsoftonline` 또는 `tenant-name.b2clogin.com`와 같은 도메인 이름과 테넌트 식별자 https:\//login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/를 포함 합니다.</li><li>AuthorityWithTfp - iss 클레임에는 도메인 이름(예: `login.microsoftonline` 또는 `tenant-name.b2clogin.com`), 테넌트 식별자 및 신뢰 당사자 정책 이름이 포함됩니다. https:\//login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> 기본값: AuthorityAndTenantGuid |
| AuthenticationContextReferenceClaimPattern | 아닙니다. | `acr` 클레임 값을 제어합니다.<ul><li>None - Azure AD B2C가 acr 클레임을 발급하지 않습니다.</li><li>PolicyId - `acr` 클레임에 정책 이름이 포함됩니다.</li></ul>이 값을 설정하기 위한 옵션은 TFP(보안 프레임워크 정책) 및 ACR(인증 컨텍스트 참조)입니다. 이 값을 TFP로 설정하는 것이 좋습니다. 값을 설정하려면 `Key="AuthenticationContextReferenceClaimPattern"`과 함께 `<Item>`이 존재하고 값이 `None`인지 확인합니다. 신뢰 당사자 정책에서 `<OutputClaims>` 항목을 추가하고 이 요소 `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`를 추가합니다. 또한 정책에 클레임 형식 `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>`이 포함되어 있는지 확인합니다. |

## <a name="cryptographic-keys"></a>암호화 키

CryptographicKeys 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| issuer_secret | 예 | JWT 토큰에 서명하는 데 사용할 X509 인증서(RSA 키 집합)입니다. [사용자 지정 정책 시작`B2C_1A_TokenSigningKeyContainer`에서 구성한 ](custom-policy-get-started.md) 키입니다. |
| issuer_refresh_token_key | 예 | 새로 고침 토큰을 암호화하는 데 사용할 X509 인증서(RSA 키 집합)입니다. [사용자 지정 정책 시작`B2C_1A_TokenEncryptionKeyContainer`에서 ](custom-policy-get-started.md) 키를 구성했습니다. |














