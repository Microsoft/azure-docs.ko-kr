---
title: 사용자 지정 정책에 대한 정수 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF (Identity Experience Framework) 스키마에 대한 정수 클레임 변환 예입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7295e03f0a0f94b3450b99acc4d10d6ff86c92e7
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74948896"
---
# <a name="integer-claims-transformations"></a>정수 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 Id 경험 프레임 워크 스키마의 정수 클레임 변환을 사용 하는 예제를 제공 합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="convertnumbertostringclaim"></a>ConvertNumberToStringClaim

long 데이터 형식을 문자열 데이터 형식으로 변환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 참고 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | long | 문자열로 변환할 ClaimType입니다. |
| OutputClaim | outputClaim | 문자열 | 이 ClaimsTransformation이 호출된 후 생성되는 ClaimType입니다. |

이 예제에서는 값 형식이 long인 `numericUserId` 클레임이 값 형식이 문자열인 `UserId` 클레임으로 변환됩니다.

```XML
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

