---
title: 클레임 변환 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 클레임 변환 기술 프로필을 정의합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbc1d59ac401adedcf897d0c16edb6904c89acc3
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193442"
---
# <a name="define-a-claims-transformation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 클레임 변환 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

클레임 변환 기술 프로필을 사용하면 출력 클레임 변환을 호출하여 클레임 값을 조작하거나, 클레임의 유효성을 검사하거나, 출력 클레임 집합의 기본값을 설정할 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제는 클레임 변환 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 필수입니다. 기술 프로필에서 반환된 하나 이상의 출력 클레임을 제공해야 합니다. 다음 예제는 출력 클레임에서 기본값을 설정하는 방법을 보여 줍니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="ageGroup" DefaultValue="Undefined" />
  <OutputClaim ClaimTypeReferenceId="ageGroupValueChanged" DefaultValue="false" />
</OutputClaims>
```

## <a name="output-claims-transformations"></a>출력 클레임 변환

**OutputClaimsTransformations** 요소는 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다. 다음 기술 프로필은 **RemoveAlternativeSecurityIdByIdentityProvider** 클레임 변환을 호출합니다. 이 클레임 변환은 **AlternativeSecurityIds**컬렉션에서 소셜 ID를 제거합니다. 이 기술 프로필의 출력 클레임은 **으로 설정된** identityProvider2`facebook.com` 및 facebook.com ID가 제거된 후 이 사용자와 연결된 소셜 ID 목록을 포함하는 **AlternativeSecurityIds**입니다.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider"
TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="IdentityProvider2"
TransformationClaimType="identityProvider" />
      <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds"
TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
...
<TechnicalProfile Id="Facebook-OAUTH-UnLink">
    <DisplayName>Unlink Facebook</DisplayName>
    <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="identityProvider2" DefaultValue="facebook.com" AlwaysUseDefaultValue="true" />
    </OutputClaims>
    <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="RemoveAlternativeSecurityIdByIdentityProvider" />
    </OutputClaimsTransformations>
    <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

클레임 변환 기술 프로필을 사용하면 사용자 경험의 오케스트레이션 단계에서 클레임 변환을 실행할 수 있습니다. 다음 예제에서 오케스트레이션 단계는 **UnLink-Facebook-OAUTH**와 같은 링크 해제 기술 프로필 중 하나를 호출합니다. 이 기술 프로필이 호출하는 클레임 변환 기술 프로필 **RemoveAlternativeSecurityIdByIdentityProvider**는 사용자의 소셜 ID 목록이 포함된 새 **AlternativeSecurityIds2** 클레임을 생성하면서 컬렉션에서 Facebook ID를 제거합니다.

```XML
<UserJourney Id="AccountUnLink">
  <OrchestrationSteps>
    ...
    <OrchestrationStep Order="8" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="UnLinkFacebookExchange" TechnicalProfileReferenceId="UnLink-Facebook-OAUTH" />
        <ClaimsExchange Id="UnLinkMicrosoftExchange" TechnicalProfileReferenceId="UnLink-Microsoft-OAUTH" />
        <ClaimsExchange Id="UnLinkGitHubExchange" TechnicalProfileReferenceId="UnLink-GitHub-OAUTH" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ...
  </OrchestrationSteps>
</UserJourney>
```

## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 [클레임 확인](claim-resolver-overview.md) 이 기술 프로필에 포함 되는지 여부를 지정 합니다. 가능한 값은 `true`또는 `false` (기본값)입니다. 기술 프로필에서 클레임 해결 프로그램을 사용 하려면 `true`으로 설정 합니다. |

## <a name="use-a-validation-technical-profile"></a>유효성 검사 기술 프로필 사용

클레임 변환 기술 프로필을 사용하여 정보의 유효성을 검사할 수 있습니다. 다음 예제에서 [LocalAccountSignUpWithLogonEmail](self-asserted-technical-profile.md)이라는 **자체 어설션된 기술 프로필**은 사용자에게 메일을 두 번 입력하도록 요청한 다음, [Validate-Email](validation-technical-profile.md)이라는 **유효성 검사 기술 프로필**을 호출하여 메일의 유효성을 검사합니다. **Validate-Email** 기술 프로필은 클레임 변환 **AssertEmailAreEqual**을 호출하여 두 개의 클레임 **email** 및 **emailRepeat**를 비교하고, 지정된 비교에 따라 서로 같지 않은 경우 예외를 throw합니다.

```XML
<ClaimsTransformations>
  <ClaimsTransformation Id="AssertEmailAreEqual" TransformationMethod="AssertStringClaimsAreEqual">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim1" />
      <InputClaim ClaimTypeReferenceId="emailRepeat" TransformationClaimType="inputClaim2" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="stringComparison" DataType="string" Value="ordinalIgnoreCase" />
    </InputParameters>
  </ClaimsTransformation>
</ClaimsTransformations>
```

클레임 변환 기술 프로필은 **AssertEmailAreEqual** 클레임 변환을 호출하며, 이는 사용자가 제공한 메일이 같다고 어설션합니다.

```XML
<TechnicalProfile Id="Validate-Email">
  <DisplayName>Unlink Facebook</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailRepeat" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="AssertEmailAreEqual" />
  </OutputClaimsTransformations>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

자체 어설션된 기술 프로필은 유효성 검사 기술 프로필을 호출하고 **UserMessageIfClaimsTransformationStringsAreNotEqual** 메타데이터에 지정된 대로 오류 메시지를 표시할 수 있습니다.

```XML
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>User ID signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    ...
    <Item Key="UserMessageIfClaimsTransformationStringsAreNotEqual">The email addresses you provided are not the same</Item>
  </Metadata>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" />
    <OutputClaim ClaimTypeReferenceId="emailRepeat" />
    ...
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="Validate-Email" />
  </ValidationTechnicalProfiles>
</TechnicalProfile>
```
