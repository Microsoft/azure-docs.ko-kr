---
title: 사용자 지정 정책의 Azure MFA 기술 프로필
titleSuffix: Azure AD B2C
description: Azure AD B2C의 MFA (Azure Multi-Factor Authentication) 기술 프로필에 대한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a8aaea6b2afb4d89e6e667edba0eeba2f4ddcca8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480217"
---
# <a name="define-an-azure-mfa-technical-profile-in-an-azure-ad-b2c-custom-policy"></a>Azure AD B2C 사용자 지정 정책에서 Azure MFA 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)는 MFA (Azure Multi-Factor Authentication)를 사용 하 여 전화 번호를 확인 하는 기능을 제공 합니다. 이 기술 프로필을 사용 하 여 코드를 생성 하 고 전화 번호로 보낸 다음 코드를 확인 합니다.

Azure MFA 기술 프로필은 오류 메시지를 반환할 수도 있습니다. **유효성 검사 기술 프로필**을 사용 하 여 Azure MFA와의 통합을 디자인할 수 있습니다. 유효성 검사 기술 프로필은 Azure MFA 서비스를 호출 합니다. 유효성 검사 기술 프로필은 사용자 경험이 계속되기 전에 사용자가 제공한 데이터의 유효성을 검사합니다. 유효성 검사 기술 프로필을 사용 하면 자체 어설션된 페이지에 오류 메시지가 표시 됩니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.

```
Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제에서는 Azure MFA 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    ...
```

## <a name="send-sms"></a>SMS 보내기

이 기술 프로필의 첫 번째 모드는 코드를 생성 하 고 보내는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 Azure MFA로 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 MFA 기술 프로필에 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- |
| userPrincipalName | 예 | 전화 번호를 소유 하는 사용자의 식별자입니다. |
| phoneNumber | 예 | SMS 코드를 보낼 전화 번호입니다. |
| companyName | 아닙니다. |SMS의 회사 이름입니다. 제공 하지 않으면 응용 프로그램 이름이 사용 됩니다. |
| locale | 아닙니다. | SMS의 로캘입니다. 제공 하지 않으면 사용자의 브라우저 로캘이 사용 됩니다. |

**InputClaimsTransformations** 요소는 Azure MFA 서비스로 보내기 전에 입력 클레임을 수정 하거나 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure MFA 프로토콜 공급자는 **outputclaims**반환 되지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 `DefaultValue` 특성을 설정 하는 동안에는 Azure MFA id 공급자에서 반환 되지 않은 클레임을 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 작업 | 예 | **OneWaySMS**이어야 합니다.  |
| Usermessageifin유효한 형식 | 아닙니다. | 제공 된 전화 번호가 올바른 전화 번호가 아닌 경우 사용자 지정 오류 메시지 |
| UserMessageIfCouldntSendSms | 아닙니다. | 제공 된 전화 번호에 SMS를 사용할 수 없는 경우 사용자 지정 오류 메시지 |
| UserMessageIfServerError | 아닙니다. | 서버에 내부 오류가 발생 한 경우 사용자 지정 오류 메시지 |

### <a name="return-an-error-message"></a>오류 메시지 반환

[메타 데이터](#metadata)에 설명 된 대로 사용자에 게 표시 되는 오류 메시지를 사용자 지정 하 여 다른 오류 사례를 확인할 수 있습니다. 이러한 메시지는 로캘에 접두사로 추가 하 여 지역화할 수 있습니다. 예:

```XML
<Item Key="en.UserMessageIfInvalidFormat">Invalid phone number.</Item>
```

### <a name="example-send-an-sms"></a>예: SMS 보내기

다음 예제에서는 SMS를 통해 코드를 보내는 데 사용 되는 Azure MFA 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AzureMfa-SendSms">
    <DisplayName>Send Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">OneWaySMS</Item>
    </Metadata>
    <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="CombinePhoneAndCountryCode" />
        <InputClaimsTransformation ReferenceId="ConvertStringToPhoneNumber" />
    </InputClaimsTransformations>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="fullPhoneNumber" PartnerClaimType="phoneNumber" />
    </InputClaims>
</TechnicalProfile>
```

## <a name="verify-code"></a>코드 확인

이 기술 프로필의 두 번째 모드는 코드를 확인 하는 것입니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 Azure MFA로 보낼 클레임 목록을 포함 합니다. 클레임의 이름을 MFA 기술 프로필에 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | Description |
| --------- | -------- | ----------- | ----------- |
| phoneNumber| 예 | 이전에 코드를 전송 하는 데 사용한 것과 동일한 전화 번호입니다. 전화 확인 세션을 찾는 데도 사용 됩니다. |
| verificationCode  | 예 | 사용자가 제공 하는 확인 코드를 확인 합니다. |

**InputClaimsTransformations** 요소에는 Azure MFA 서비스를 호출 하기 전에 입력 클레임을 수정 하거나 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션이 포함 될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

Azure MFA 프로토콜 공급자는 **outputclaims**반환 되지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 `DefaultValue` 특성을 설정 하는 동안에는 Azure MFA id 공급자에서 반환 되지 않은 클레임을 포함할 수 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 작업 | 예 | **확인** 해야 함 |
| Usermessageifin유효한 형식 | 아닙니다. | 제공 된 전화 번호가 올바른 전화 번호가 아닌 경우 사용자 지정 오류 메시지 |
| UserMessageIfWrongCodeEntered | 아닙니다. | 확인을 위해 입력 한 코드가 잘못 된 경우 사용자 지정 오류 메시지 |
| UserMessageIfMaxAllowedCodeRetryReached | 아닙니다. | 사용자가 확인 코드를 너무 많이 시도 하는 경우 사용자 지정 오류 메시지 |
| UserMessageIfThrottled | 아닙니다. | 사용자가 제한 되는 경우 사용자 지정 오류 메시지 |
| UserMessageIfServerError | 아닙니다. | 서버에 내부 오류가 발생 한 경우 사용자 지정 오류 메시지 |

### <a name="return-an-error-message"></a>오류 메시지 반환

[메타 데이터](#metadata)에 설명 된 대로 사용자에 게 표시 되는 오류 메시지를 사용자 지정 하 여 다른 오류 사례를 확인할 수 있습니다. 이러한 메시지는 로캘에 접두사로 추가 하 여 지역화할 수 있습니다. 예:

```XML
<Item Key="en.UserMessageIfWrongCodeEntered">Wrong code has been entered.</Item>
```

### <a name="example-verify-a-code"></a>예: 코드 확인

다음 예제에서는 코드를 확인 하는 데 사용 되는 Azure MFA 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AzureMfa-VerifySms">
    <DisplayName>Verify Sms</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureMfaProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <Metadata>
        <Item Key="Operation">Verify</Item>
    </Metadata>
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="phoneNumber" PartnerClaimType="phoneNumber" />
        <InputClaim ClaimTypeReferenceId="verificationCode" />
    </InputClaims>
</TechnicalProfile>
```
