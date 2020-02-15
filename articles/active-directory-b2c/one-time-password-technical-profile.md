---
title: OTP (일회용 암호) 확인 사용
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책을 사용 하 여 OTP (일회용 암호) 시나리오를 설정 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9becb91cfffd4553b2b8aa1a2d616963eae92ab0
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114061"
---
# <a name="define-a-one-time-password-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 일회성 암호 기술 프로필을 정의 합니다.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 일회성 암호의 생성 및 유효성 검사를 관리 하는 기능을 제공 합니다. 기술 프로필을 사용 하 여 코드를 생성 한 다음 나중에 해당 코드를 확인 합니다.

일회용 암호 기술 프로필은 코드 확인 중에 오류 메시지를 반환할 수도 있습니다. **유효성 검사 기술 프로필**을 사용 하 여 일회용 암호와의 통합을 설계 합니다. 유효성 검사 기술 프로필은 일회용 암호 기술 프로필을 호출 하 여 코드를 확인 합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용 하면 자체 어설션된 페이지에 오류 메시지가 표시 됩니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.

```XML
Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예에서는 일회성 암호 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="VerifyCode">
  <DisplayName>Validate user input verification code</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="generate-code"></a>코드 생성

이 기술 프로필의 첫 번째 모드는 코드를 생성 하는 것입니다. 이 모드에 대해 구성할 수 있는 옵션은 다음과 같습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 일회용 암호 프로토콜 공급자에 게 보내는 데 필요한 클레임 목록을 포함 합니다. 클레임의 이름을 아래 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| identifier | yes | 코드를 나중에 확인 해야 하는 사용자를 식별 하는 식별자입니다. 일반적으로 전자 메일 주소 또는 전화 번호와 같이 코드가 전달 되는 대상의 식별자로 사용 됩니다. |

**InputClaimsTransformations** 요소에는 일회용 암호 프로토콜 공급자로 보내기 전에 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션이 포함 될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 일회용 암호 프로토콜 공급자가 생성 하는 클레임 목록을 포함 합니다. 클레임의 이름을 아래 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| otpGenerated | yes | Azure AD B2C에서 세션을 관리 하는 생성 된 코드입니다. |

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정을 사용 하 여 코드 생성 및 유지 관리를 구성할 수 있습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| CodeExpirationInSeconds | 예 | 코드 만료 까지의 시간 (초)입니다. 최소: `60`; 최대값: `1200`; 기본값: `600`. |
| CodeLength | 예 | 코드의 길이입니다. 기본값은 `6`입니다. |
| CharacterSet | 예 | 정규식에서 사용 하기 위해 형식이 지정 된 코드에 대 한 문자 집합입니다. `a-z0-9A-Z`)을 입력합니다. 기본값은 `0-9`입니다. 문자 집합은 지정 된 집합에 최소 10 개의 다른 문자를 포함 해야 합니다. |
| NumRetryAttempts | 예 | 코드가 잘못 된 것으로 간주 되기 전의 확인 시도 횟수입니다. 기본값은 `5`입니다. |
| 작업(Operation) | yes | 수행할 작업입니다. 가능한 값은 `GenerateCode`또는 `VerifyCode`입니다. |
| ReuseSameCode | 예 | 지정 된 코드가 만료 되지 않고 여전히 유효한 경우 새 코드를 생성 하는 대신 중복 코드를 지정 해야 하는지 여부입니다. 기본값은 `false`입니다. |

### <a name="returning-error-message"></a>오류 메시지 반환

코드 생성 모드에 대해 반환 되는 오류 메시지는 없습니다.

### <a name="example"></a>예제

다음 예제 `TechnicalProfile`는 코드를 생성 하는 데 사용 됩니다.

```XML
<TechnicalProfile Id="GenerateCode">
    <DisplayName>Generate Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">600</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="NumRetryAttempts">5</Item>
        <Item Key="ReuseSameCode">false</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpGenerated" />
    </OutputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인 하는 것입니다. 이 모드에 대해 구성할 수 있는 옵션은 다음과 같습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 일회용 암호 프로토콜 공급자에 게 보내는 데 필요한 클레임 목록을 포함 합니다. 클레임의 이름을 아래 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| identifier | yes | 이전에 코드를 생성 한 사용자를 식별 하는 식별자입니다. 일반적으로 전자 메일 주소 또는 전화 번호와 같이 코드가 전달 되는 대상의 식별자로 사용 됩니다. |
| otpToVerify | yes | 사용자가 제공한 확인 코드입니다. |

**InputClaimsTransformations** 요소에는 일회용 암호 프로토콜 공급자로 보내기 전에 입력 클레임을 수정 하거나 새로 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션이 포함 될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

이 프로토콜 공급자의 코드를 확인 하는 동안 제공 된 출력 클레임이 없습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

다음 설정을 사용 하 여 코드 확인 실패 시 표시 되는 오류 메시지를 구성할 수 있습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| UserMessageIfSessionDoesNotExist | 예 | 코드 확인 세션이 만료 된 경우 사용자에 게 표시할 메시지입니다. 코드가 만료 되었거나 지정 된 식별자에 대 한 코드가 생성 되지 않았습니다. |
| UserMessageIfMaxRetryAttempted | 예 | 허용 되는 최대 확인 시도 횟수를 초과 하는 경우 사용자에 게 표시할 메시지입니다. |
| Usermessageifin유효한 코드 | 예 | 잘못 된 코드를 제공한 경우 사용자에 게 표시할 메시지입니다. |

### <a name="returning-error-message"></a>오류 메시지 반환

[메타 데이터](#metadata)에 설명 된 대로 사용자에 게 표시 되는 오류 메시지를 사용자 지정 하 여 다른 오류 사례를 확인할 수 있습니다. 다음과 같이 로캘에 접두사를 추가 하 여 해당 메시지를 추가로 지역화할 수 있습니다.

```XML
<Item Key="en.UserMessageIfInvalidCode">Wrong code has been entered.</Item>
```

### <a name="example"></a>예제

다음 예제 `TechnicalProfile`는 코드를 확인 하는 데 사용 됩니다.

```XML
<TechnicalProfile Id="VerifyCode">
    <DisplayName>Verify Code</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">VerifyCode</Item>
        <Item Key="UserMessageIfInvalidCode">Wrong code has been entered.</Item>
        <Item Key="UserMessageIfSessionDoesNotExist">Code has expired.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You've tried too many times.</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="identifier" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="otpGenerated" PartnerClaimType="otpToVerify" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="next-steps"></a>다음 단계

사용자 지정 전자 메일 확인과 함께 일회성 암호 technial 프로필을 사용 하는 예는 다음 문서를 참조 하세요.

- [Azure Active Directory B2C에서 사용자 지정 전자 메일 확인](custom-email.md)

