---
title: 사용자 지정 정책에서 전화 번호 클레임 변환
titleSuffix: Azure AD B2C
description: Azure AD B2C의 전화 번호 클레임 변환에 대 한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c5beef98f03c52ca022a7ab8047d3b392755c0bf
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212196"
---
# <a name="define-phone-number-claims-transformations-in-azure-ad-b2c"></a>Azure AD B2C에서 전화 번호 클레임 변환 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 Id 경험 프레임 워크 스키마의 전화 번호 클레임 변환을 사용 하는 방법에 대 한 참조 및 예제를 제공 합니다. 일반적인 클레임 변환에 대 한 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조 하세요.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="convertstringtophonenumberclaim"></a>ConvertStringToPhoneNumberClaim

이 클레임은 전화 번호의 형식에 대 한 유효성을 검사 합니다. 유효한 형식이 면 Azure AD B2C에서 사용 하는 표준 형식으로 변경 합니다. 제공 된 전화 번호의 형식이 잘못 된 경우 오류 메시지가 반환 됩니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumberString | 문자열 |  전화 번호에 대 한 문자열 클레임입니다. 전화 번호는 국제 형식 이어야 하며 앞에 "+"와 국가 코드를 사용 하 여 완료 해야 합니다. 입력 클레임 `country` 제공 되는 경우 전화 번호는 지역 형식 (국가 코드 제외)입니다. |
| InputClaim | country | 문자열 | 필드 ISO3166 형식의 전화 번호에 대 한 국가 코드에 대 한 문자열 클레임입니다 (2 자 ISO-3166 국가 코드). |
| OutputClaim | outputClaim | phoneNumber | 이 클레임 변환의 결과입니다. |

**ConvertStringToPhoneNumberClaim** 클레임 변환은 항상 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 또는 [디스플레이 컨트롤](display-controls.md)에 의해 호출 되는 [유효성 검사 기술 프로필](validation-technical-profile.md) 에서 실행 됩니다. **UserMessageIfClaimsTransformationInvalidPhoneNumber** 자체 어설션된 기술 프로필 메타 데이터는 사용자에 게 표시 되는 오류 메시지를 제어 합니다.

![오류 메시지 실행 경로의 다이어그램](./media/phone-authentication/assert-execution.png)

이 클레임 변환을 사용 하 여 제공 된 문자열 클레임이 올바른 전화 번호 인지 확인할 수 있습니다. 두 값이 같지 않으면 오류 메시지가 throw됩니다. 다음 예에서는 **phoneString** ClaimType이 실제로 유효한 전화 번호 인지 확인 한 다음 표준 Azure AD B2C 형식으로 전화 번호를 반환 합니다. 그렇지 않으면 오류 메시지가 throw 됩니다.

```XML
<ClaimsTransformation Id="ConvertStringToPhoneNumber" TransformationMethod="ConvertStringToPhoneNumberClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneString" TransformationClaimType="phoneNumberString" />
    <InputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="country" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

이 클레임 변환이 포함 된 유효성 검사 기술 프로필을 호출 하는 자체 어설션된 기술 프로필은 오류 메시지를 정의할 수 있습니다.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfClaimsTransformationInvalidPhoneNumber">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>예 1

- 입력 클레임:
  - **phoneNumberString**: 045 456-7890
  - **국가**: 진한
- 출력 클레임:
  - **Outputclaim**: + 450546148120

### <a name="example-2"></a>예제 2

- 입력 클레임:
  - **phoneNumberString**: + 1 (123) 456-7890
- 출력 클레임: 
  - **Outputclaim**: + 11234567890

## <a name="getnationalnumberandcountrycodefromphonenumberstring"></a>GetNationalNumberAndCountryCodeFromPhoneNumberString

입력 클레임에서 국가 코드와 국가 번호를 추출 하 고, 제공 된 전화 번호가 올바르지 않은 경우 필요에 따라 예외를 throw 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | phoneNumber | 문자열 | 전화 번호의 문자열 클레임입니다. 전화 번호는 국제 형식 이어야 하며 앞에 "+"와 국가 코드를 사용 하 여 완료 해야 합니다. |
| InputParameter | throwExceptionOnFailure | boolean | 필드 전화 번호가 잘못 된 경우 예외가 throw 되는지 여부를 나타내는 매개 변수입니다. 기본값은 False입니다. |
| InputParameter | countryCodeType | 문자열 | 필드 출력 클레임의 국가 코드 형식을 나타내는 매개 변수입니다. 사용 가능한 값은 **Callingcode** (국가에 대 한 국제 호출 코드 (예: + 1) 또는 **ISO3166** (2 자 ISO-3166 country 코드)입니다. |
| OutputClaim | nationalNumber | 문자열 | 전화 번호의 국가별 번호에 대 한 문자열 클레임입니다. |
| OutputClaim | countryCode | 문자열 | 전화 번호의 국가 코드에 대 한 문자열 클레임입니다. |


**GetNationalNumberAndCountryCodeFromPhoneNumberString** 클레임 변환이 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 또는 [디스플레이 제어 작업](display-controls.md#display-control-actions)에 의해 호출 되는 [유효성 검사 기술 프로필](validation-technical-profile.md) 에서 실행 되는 경우 **UserMessageIfPhoneNumberParseFailure** 자체 어설션된 기술 프로필 메타 데이터는 사용자에 게 표시 되는 오류 메시지를 제어 합니다.

![오류 메시지 실행 경로의 다이어그램](./media/phone-authentication/assert-execution.png)

이 클레임 변환을 사용 하 여 전체 전화 번호를 국가 코드와 국가별 번호로 분할할 수 있습니다. 제공 된 전화 번호가 잘못 된 경우 오류 메시지를 throw 하도록 선택할 수 있습니다.

다음 예에서는 전화 번호를 국가별 번호 및 국가 코드로 분할 하려고 시도 합니다. 전화 번호가 유효한 경우 전화 번호는 국가별 번호로 재정의 됩니다. 전화 번호가 잘못 된 경우 예외가 throw 되지 않으며 전화 번호에 원래 값이 남아 있습니다.

```XML
<ClaimsTransformation Id="GetNationalNumberAndCountryCodeFromPhoneNumberString" TransformationMethod="GetNationalNumberAndCountryCodeFromPhoneNumberString">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="phoneNumber" TransformationClaimType="phoneNumber" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="throwExceptionOnFailure" DataType="boolean" Value="false" />
    <InputParameter Id="countryCodeType" DataType="string" Value="ISO3166" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="nationalNumber" TransformationClaimType="nationalNumber" />
    <OutputClaim ClaimTypeReferenceId="countryCode" TransformationClaimType="countryCode" />
  </OutputClaims>
</ClaimsTransformation>
```

이 클레임 변환이 포함 된 유효성 검사 기술 프로필을 호출 하는 자체 어설션된 기술 프로필은 오류 메시지를 정의할 수 있습니다.

```XML
<TechnicalProfile Id="SelfAsserted-LocalAccountSignup-Phone">
  <Metadata>
    <Item Key="UserMessageIfPhoneNumberParseFailure">Custom error message if the phone number is not valid.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

### <a name="example-1"></a>예 1

- 입력 클레임:
  - **phoneNumber**: + 49 (123) 456-7890
- 입력 매개 변수:
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: ISO3166
- 출력 클레임:
  - **nationalNumber**: 1234567890
  - **countryCode**: DE

### <a name="example-2"></a>예제 2

- 입력 클레임:
  - **phoneNumber**: + 49 (123) 456-7890
- 입력 매개 변수
  - **throwExceptionOnFailure**: false
  - **countryCodeType**: callingcode
- 출력 클레임:
  - **nationalNumber**: 1234567890
  - **countryCode**: + 49
