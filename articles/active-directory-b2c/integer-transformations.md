---
title: 사용자 지정 정책에 대 한 정수 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF (Identity Experience Framework) 스키마에 대 한 정수 클레임 변환 예입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 066a6489e6244369453ec5d9f21d5e1e83fcd6c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85201753"
---
# <a name="integer-claims-transformations"></a>정수 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 Id 경험 프레임 워크 스키마의 정수 클레임 변환을 사용 하는 예제를 제공 합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

long 데이터 형식을 문자열 데이터 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 메모 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 문자열로 변환할 ClaimType입니다. |
| OutputClaim | outputClaim | 문자열 | 이 ClaimsTransformation이 호출된 후에 생성되는 ClaimType입니다. |

이 예제에서는 값 형식이 long인 `numericUserId` 클레임이 값 형식이 문자열인 `UserId` 클레임으로 변환됩니다.

```xml
<ClaimsTransformation Id="CreateUserId" TransformationMethod="ConvertNumberToStringClaim">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="numericUserId" TransformationClaimType="inputClaim" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="UserId" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **inputClaim**: 12334(long)
- 출력 클레임:
    - **outputClaim**: "12334"(문자열)

