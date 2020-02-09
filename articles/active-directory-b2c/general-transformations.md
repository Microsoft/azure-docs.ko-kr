---
title: 사용자 지정 정책에 대한 일반 클레임 변환 예
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF (Identity Experience Framework) 스키마에 대한 일반 클레임 변환 예입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d9730168764f0ba683a246f9ac224c13d3bf31
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982809"
---
# <a name="general-claims-transformations"></a>일반 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 Id 경험 프레임 워크 스키마의 일반 클레임 변환을 사용 하는 예제를 제공 합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="copyclaim"></a>CopyClaim

클레임의 값을 다른 값으로 복사 합니다. 두 클레임 모두 동일한 유형 이어야 합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | 문자열, int | 복사할 클레임 유형입니다. |
| OutputClaim | outputClaim | 문자열, int | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

이 클레임 변환을 사용 하 여 문자열 또는 숫자 클레임의 값을 다른 클레임으로 복사할 수 있습니다. 다음 예에서는 externalEmail 클레임 값을 전자 메일 클레임에 복사 합니다.

```XML
<ClaimsTransformation Id="CopyEmailAddress" TransformationMethod="CopyClaim"> 
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="externalEmail" TransformationClaimType="inputClaim"/>
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" TransformationClaimType="outputClaim"/>
  </OutputClaims>         
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
    - **inputClaim**: bob@contoso.com
- 출력 클레임:
    - **outputClaim**: bob@contoso.com 

## <a name="doesclaimexist"></a>DoesClaimExist

**inputClaim** 유무를 확인하고 확인 결과에 따라 **outputClaim**를 true 또는 false로 설정합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim |모두 | 유무를 확인해야 하는 입력 클레임입니다. |
| OutputClaim | outputClaim | boolean | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

특정 클레임이 있는지 여부나 값을 포함하는지 여부를 확인하려면 다음 클레임 변환을 사용합니다. 반환 값은 클레임이 있는지 여부를 나타내는 부울입니다. 다음 예제에서는 전자 메일 주소가 있는지 확인합니다.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **inputClaim**: someone@contoso.com
- 출력 클레임:
  - **outputClaim**: true

## <a name="hash"></a>Hash

솔트와 비밀을 사용하여 제공된 입력 텍스트를 해시합니다. 사용 되는 해시 알고리즘은 SHA-256입니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | 문자열 | 암호화할 입력 클레임입니다. |
| InputClaim | salt | 문자열 | 솔트 매개 변수입니다. `CreateRandomString` 클레임 변환을 사용하여 임의 값을 만들 수 있습니다. |
| InputParameter | randomizerSecret | 문자열 | 기존 Azure AD B2C **정책 키**를 가리킵니다. 새 정책 키를 만들려면: Azure AD B2C 테넌트에서 **관리**아래에서 **Id 경험 프레임 워크**를 선택 합니다. 테넌트에서 사용할 수 있는 키를 보려면 **정책 키** 를 선택 합니다. **추가**를 선택합니다. **옵션**에서 **수동**을 선택합니다. 이름 (접두사 *B2C_1A_* 자동으로 추가 될 수 있음)을 입력 합니다. **비밀** 텍스트 상자에 사용 하려는 비밀 (예: 1234567890)을 입력 합니다. **키 사용**으로는 **서명**을 선택합니다. **만들기**를 선택합니다. |
| OutputClaim | hash | 문자열 | 이 클레임 변환을 호출하고 나면 생성되는 ClaimType입니다. `plaintext` inputClaim에구성 된 클레임입니다. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예

- 입력 클레임:
  - **plaintext**: MyPass@word1
  - **salt**: 487624568
  - **randomizerSecret**: B2C_1A_AccountTransformSecret
- 출력 클레임:
  - **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=
