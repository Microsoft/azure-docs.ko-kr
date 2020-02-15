---
title: 사용자 지정 정책의 기술 프로필 개요
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 기술 프로필을 사용 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f3a9265c1f9a5c6c63931798718e4d0679cd126b
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136249"
---
# <a name="about-technical-profiles-in-azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C 사용자 지정 정책의 기술 프로필 정보

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

기술 프로필은 Azure Active Directory B2C (Azure AD B2C)의 사용자 지정 정책을 사용 하 여 다른 종류의 당사자와 통신 하는 기본 제공 메커니즘을 프레임 워크에 제공 합니다. 기술 프로필은 Azure AD B2C 테넌트와 통신하거나, 사용자를 만들거나, 사용자 프로필을 읽는 데 사용됩니다. 기술 프로필은 사용자와 상호 작용이 가능하도록 자체 어설션될 수 있습니다. 예를 들어, 로그인할 사용자의 자격 증명을 수집한 후 등록 페이지 또는 암호 재설정 페이지를 렌더링합니다.

## <a name="type-of-technical-profiles"></a>기술 프로필 유형

기술 프로필은 다음 유형의 시나리오를 가능하게 합니다.

- [Azure Active Directory](active-directory-technical-profile.md) - Azure Active Directory B2C 사용자 관리를 지원합니다.
- [JWT 토큰 발급자](jwt-issuer-technical-profile.md) - 다시 신뢰 당사자 애플리케이션으로 반환되는 JWT 토큰을 내보냅니다.
- **Phone Factor 공급자** - 다단계 인증입니다.
- [OAuth1](oauth1-technical-profile.md) - OAuth 1.0 프로토콜 ID 공급자와 페더레이션됩니다.
- [OAuth2](oauth2-technical-profile.md) - OAuth 2.0 프로토콜 ID 공급자와 페더레이션됩니다.
- [Openid connect](openid-connect-technical-profile.md) 를 모든 openid connect connect 프로토콜 id 공급자와 연결 합니다.
- [클레임 변환](claims-transformation-technical-profile.md) - 출력 클레임 변환을 호출하여 클레임 값을 조작하거나, 클레임의 유효성을 검사하거나, 출력 클레임 집합의 기본값을 설정합니다.
- [RESTful provider](restful-technical-profile.md) -사용자 입력의 유효성을 검사 하거나, 사용자 데이터를 보강 하거나, lob (기간 업무) 응용 프로그램과 통합 하는 등 REST API 서비스에 대 한 호출입니다.
- [SAML2](saml-technical-profile.md) - SAML 프로토콜 ID 공급자와 페더레이션됩니다.
- [자체 어설션](self-asserted-technical-profile.md) - 사용자와 상호 작용합니다. 예를 들어, 로그인할 사용자의 자격 증명을 수집하고 등록 페이지 또는 암호 재설정을 렌더링합니다.
- [세션 관리](custom-policy-reference-sso.md) - 여러 유형의 세션을 처리합니다.
- **Application Insights**
- 일회용 [암호](one-time-password-technical-profile.md) -일회용 암호의 생성 및 유효성 검사를 관리 하는 기능을 제공 합니다. 

## <a name="technical-profile-flow"></a>기술 프로필 흐름

모든 유형의 기술 프로필은 동일한 개념을 공유합니다. 입력 클레임을 보내고, 클레임 변환을 실행하고, ID 공급자, REST API 또는 Azure AD 디렉터리 서비스와 같은 구성된 당사자와 통신합니다. 프로세스가 완료 된 후 기술 프로필은 출력 클레임을 반환 하 고 출력 클레임 변환을 실행할 수 있습니다. 다음 다이어그램은 기술 프로필에서 참조되는 변환 및 매핑이 처리되는 방법을 보여 줍니다. 기술 프로필이 상호 작용하는 당사자와 관계없이 모든 클레임 변환이 실행된 후 기술 프로필의 출력 클레임은 즉시 클레임 모음에 저장됩니다.

![기술 프로필 흐름을 보여 주는 다이어그램](./media/technical-profiles-overview/technical-profile-idp-saml-flow.png)
 
1. Sso **(Single sign-on) 세션 관리** - [sso 세션 관리](custom-policy-reference-sso.md)를 사용 하 여 기술 프로필의 세션 상태를 복원 합니다. 
1. **입력 클레임 변환** -모든 입력 [클레임 변환](claimstransformations.md) 의 입력 클레임이 클레임 모음에서 선택 됩니다.  입력 클레임 변환의 출력 클레임이 후속 입력 클레임 변환의 입력 클레임이 될 수 있습니다.
1. **입력 클레임** -클레임 모음에서 클레임을 선택 하 고 기술 프로필에 사용 됩니다. 예를 들어, [자체 어설션 기술 프로필](self-asserted-technical-profile.md)은 입력 클레임을 사용하여 사용자가 제공하는 출력 클레임을 미리 채웁니다. REST API 기술 프로필은 입력 클레임을 사용하여 입력 매개 변수를 REST API 엔드포인트에 보냅니다. Azure Active Directory는 입력 클레임을 고유 식별자로 사용하여 계정을 읽거나, 업데이트하거나, 삭제합니다.
1. **기술 프로필 실행** - 기술 프로필이 클레임을 구성된 당사자와 교환합니다. 예를 들면 다음과 같습니다.
    - 사용자를 ID 공급자로 리디렉션하여 로그인을 완료합니다. 로그인에 성공하면 사용자가 다시 돌아가고 기술 프로필 실행이 계속됩니다.
    - 매개 변수를 InputClaims로 보내고 정보를 다시 OutputClaims로 가져오는 동안 REST API를 호출합니다.
    - 사용자 계정을 만들거나 업데이트합니다.
    - MFA 텍스트 메시지를 보내고 확인합니다.
1. **유효성 검사 기술 프로필** - [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 호출할 수 있습니다. 유효성 검사 기술 프로필은 사용자가 프로파일링한 데이터의 유효성을 검사하고 출력 클레임이 있으면 오류 메시지를, 없으면 확인을 반환합니다. 예를 들어, Azure AD B2C는 새 계정을 만들기 전에 사용자가 이미 디렉터리 서비스에 있는지 확인합니다. REST API 기술 프로필을 호출하여 고유한 비즈니스 논리를 추가할 수 있습니다.<p>유효성 검사 기술 프로필의 출력 클레임 범위는 유효성 검사 기술 프로필을 호출 하는 기술 프로필로 제한 됩니다. 동일한 기술 프로필에 있는 기타 유효성 검사 기술 프로필. 다음 오케스트레이션 단계에서 출력 클레임을 사용하려면 유효성 검사 기술 프로필을 호출하는 기술 프로필에 출력 클레임을 추가해야 합니다.
1. **출력 클레임** -클레임이 클레임 모음으로 다시 반환 됩니다. 다음 오케스트레이션 단계 또는 출력 클레임 변환에서 해당 클레임을 사용할 수 있습니다.
1. **출력 클레임 변환** -모든 출력 [클레임 변환](claimstransformations.md) 의 입력 클레임이 클레임 모음에서 선택 됩니다. 이전 단계에서 기술 프로필의 출력 클레임은 출력 클레임 변환의 입력 클레임이 될 수 있습니다. 실행 후 출력 클레임이 다시 청구 모음에 저장됩니다. 출력 클레임 변환의 출력 클레임이 후속 출력 클레임 변환의 입력 클레임이 될 수도 있습니다.
1. Sso **(Single sign-on) 세션 관리** - [sso 세션 관리](custom-policy-reference-sso.md)를 사용 하 여 세션에 대 한 기술 프로필의 데이터를 유지 합니다.


## <a name="technical-profile-inclusion"></a>기술 프로필 포함

기술 프로필에는 다른 기술 프로필을 포함 하 여 설정을 변경 하거나 새 기능을 추가할 수 있습니다.  `IncludeTechnicalProfile` 요소는 기술 프로필이 파생 되는 기본 기술 프로필에 대 한 참조입니다. 수준 수에는 제한이 없습니다. 

예를 들어, **AAD-UserReadUsingAlternativeSecurityId-NoError** 기술 프로필에는 **AAD-UserReadUsingAlternativeSecurityId**가 포함됩니다. 이 기술 프로필은 `RaiseErrorIfClaimsPrincipalDoesNotExist` 메타 데이터 항목을 `true`설정 하 고, 소셜 계정이 디렉터리에 없는 경우 오류를 발생 시킵니다. **UserReadUsingAlternativeSecurityId-NoError** 는이 동작을 재정의 하 고 해당 오류 메시지를 비활성화 합니다.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId-NoError">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">false</Item>
  </Metadata>
  <IncludeTechnicalProfile ReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
</TechnicalProfile>
```

**AAD-UserReadUsingAlternativeSecurityId**에는 `AAD-Common` 기술 프로필이 포함됩니다.

```XML
<TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">User does not exist. Please sign up before you can sign in.</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

**UserReadUsingAlternativeSecurityId-NoError** 와 **UserReadUsingAlternativeSecurityId** 는 모두 **aad 일반** 기술 프로필에 지정 되어 있으므로 필수 **프로토콜** 요소를 지정 하지 않습니다.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
</TechnicalProfile>
```
