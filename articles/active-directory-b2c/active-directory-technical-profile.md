---
title: 사용자 지정 정책에서 Azure AD 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 Azure Active Directory 기술 프로필을 정의합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9f00bebfbab7b3726930e212893ae9dd2f5c17c8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193480"
---
# <a name="define-an-azure-active-directory-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 Azure Active Directory 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) Azure Active Directory 사용자 관리를 지원 합니다. 이 문서에서는 이 표준 프로토콜을 지원하는 클레임 공급자와 상호 작용하기 위한 기술 프로필에 대해 구체적으로 설명합니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **handler** 특성은 프로토콜 처리기 어셈블리의 정규화된 이름 `Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`을 포함해야 합니다.

모든 Azure AD 기술 프로필은 **AAD-Common** 기술 프로필을 포함합니다. 프로토콜이 **AAD-Common** 기술 프로필에 구성되어 있으므로 다음 기술 프로필은 프로토콜을 지정하지 않습니다.

- **AAD-UserReadUsingAlternativeSecurityId** 및 **AAD-UserReadUsingAlternativeSecurityId-NoError** - 디렉터리에서 소셜 계정을 조회합니다.
- **AAD-UserWriteUsingAlternativeSecurityId** - 새 소셜 계정을 만듭니다.
- **AAD-UserReadUsingEmailAddress** - 디렉터리에서 로컬 계정을 조회합니다.
- **AAD-UserWriteUsingLogonEmail** - 새 로컬 계정을 만듭니다.
- **AAD-UserWritePasswordUsingObjectId** - 로컬 계정의 암호를 업데이트합니다.
- **AAD-UserWriteProfileUsingObjectId** - 로컬 또는 소셜 계정의 사용자 프로필을 업데이트합니다.
- **AAD-UserReadUsingObjectId** - 로컬 또는 소셜 계정의 사용자 프로필을 읽습니다.
- **AAD-UserWritePhoneNumberUsingObjectId** - 로컬 또는 소셜 계정의 MFA 전화 번호를 씁니다.

다음 예제는 **AAD-Common** 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="AAD-Common">
  <DisplayName>Azure Active Directory</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>

  <!-- We need this here to suppress the SelfAsserted provider from invoking SSO on validation profiles. -->
  <IncludeInSso>false</IncludeInSso>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="input-claims"></a>입력 클레임

다음 기술 프로필은 소셜 및 로컬 계정에 대한 **InputClaims**를 포함합니다.

- 소셜 계정 기술 프로필 **AAD-UserReadUsingAlternativeSecurityId** 및 **AAD-UserWriteUsingAlternativeSecurityId**는 **AlternativeSecurityId** 클레임을 포함합니다. 이 클레임은 소셜 계정 사용자 식별자를 포함합니다.
- 로컬 계정 기술 프로필 **AAD-UserReadUsingEmailAddress** 및 **AAD-UserWriteUsingLogonEmail**은 **email** 클레임을 포함합니다. 이 클레임은 로컬 계정의 로그인 이름을 포함합니다.
- 통합(로컬 및 소셜) 기술 프로필 **AAD-UserReadUsingObjectId**, **AAD-UserWritePasswordUsingObjectId**, **AAD-UserWriteProfileUsingObjectId** 및 **AAD-UserWritePhoneNumberUsingObjectId**는 **objectId** 클레임을 포함합니다. 계정의 고유 식별자입니다.

**InputClaimsTransformations** 요소는 입력 클레임을 수정하거나 새 클레임을 생성하는 데 사용되는 **InputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소는 Azure AD 기술 프로필에서 반환된 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 Azure Active Directory에서 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, Azure Active Directory에서 반환되지 않은 클레임을 포함할 수도 있습니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

예를 들어 **AAD-UserWriteUsingLogonEmail** 기술 프로필은 로컬 계정을 만들고 다음 클레임을 반환합니다.

- **objectId** - 새 계정의 식별자입니다.
- **newUser** - 새 사용자인지 여부를 나타냅니다.
- **authenticationSource** - 인증을 `localAccountAuthentication`으로 설정합니다.
- **userPrincipalName** - 새 계정의 사용자 계정 이름입니다.
- **signInNames.emailAddress** - **email** 입력 클레임과 유사한 계정 로그인 이름입니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="objectId" />
  <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
  <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
</OutputClaims>
```

## <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 요소는 정책의 ClaimsSchema 섹션에 이미 정의된 클레임 유형과 Azure AD 특성 이름 간에 가능한 매핑 정보를 사용하여 Azure AD에서 저장해야 하는 모든 값을 포함합니다.

새 로컬 계정을 만드는 **AAD-UserWriteUsingLogonEmail** 기술 프로필은 다음 클레임을 저장합니다.

```XML
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
```

Azure AD 특성 이름을 포함하는 **PartnerClaimType** 특성이 지정되지 않은 경우 클레임 이름은 Azure AD 특성 이름입니다.

## <a name="requirements-of-an-operation"></a>작업 요구사항

- 모든 Azure AD 기술 프로필의 클레임 모음에 정확히 하나의 **InputClaim** 요소가 있어야 합니다.
- 작업이 `Write` 또는 `DeleteClaims`이면 **PersistedClaims** 요소에도 표시되어야 합니다.
- **userPrincipalName** 클레임의 값은 `user@tenant.onmicrosoft.com` 형식이어야 합니다.
- **displayName** 클레임은 필수이며 빈 문자열일 수 없습니다.

## <a name="azure-ad-technical-provider-operations"></a>Azure AD 기술 공급자 작업

### <a name="read"></a>읽기

**Read** 작업은 단일 사용자 계정에 대한 데이터를 읽습니다. 사용자 데이터를 읽으려면 **objectId**, **userPrincipalName**, **signInNames**(모든 유형, 사용자 이름 및 메일 기반 계정) 또는 **alternativeSecurityId**와 같은 키를 입력 클레임으로 제공해야 합니다.

다음 기술 프로필은 사용자의 objectId를 사용하여 사용자 계정에 대한 데이터를 읽습니다.

```XML
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>

    <!-- Required claims -->
    <OutputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />

    <!-- Optional claims -->
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="write"></a>쓰기

**Write** 작업은 단일 사용자 계정을 만들거나 업데이트합니다. 사용자 계정을 쓰려면 **objectId**, **userPrincipalName**, **signInNames.emailAddress** 또는 **alternativeSecurityId**와 같은 키를 입력 클레임으로 제공해야 합니다.

다음 기술 프로필은 새 소셜 계정을 만듭니다.

```XML
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="CreateOtherMailsFromEmail" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId" PartnerClaimType="alternativeSecurityId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <PersistedClaim ClaimTypeReferenceId="userPrincipalName" />
    <PersistedClaim ClaimTypeReferenceId="mailNickName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />

    <!-- Optional claims -->
    <PersistedClaim ClaimTypeReferenceId="otherMails" />
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="deleteclaims"></a>DeleteClaims

**DeleteClaims** 작업은 제공된 클레임 목록에서 정보를 지웁니다. 클레임에서 정보를 삭제하려면 **objectId**, **userPrincipalName**, **signInNames.emailAddress** 또는 **alternativeSecurityId**와 같은 키를 입력 클레임으로 제공해야 합니다.

다음 기술 프로필은 클레임을 삭제합니다.

```XML
<TechnicalProfile Id="AAD-DeleteClaimsUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaims</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <PersistedClaim ClaimTypeReferenceId="objectId" />
    <PersistedClaim ClaimTypeReferenceId="Verified.strongAuthenticationPhoneNumber" PartnerClaimType="strongAuthenticationPhoneNumber" />
  </PersistedClaims>
  <OutputClaims />
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

### <a name="deleteclaimsprincipal"></a>DeleteClaimsPrincipal

**DeleteClaimsPrincipal** 작업은 디렉터리에서 단일 사용자 계정을 삭제합니다. 사용자 계정을 삭제하려면 **objectId**, **userPrincipalName**, **signInNames.emailAddress** 또는 **alternativeSecurityId**와 같은 키를 입력 클레임으로 제공해야 합니다.

다음 기술 프로필은 사용자 계정 이름을 사용하여 디렉터리에서 사용자 계정을 삭제합니다.

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingObjectId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

다음 기술 프로필은 **alternativeSecurityId**를 사용하여 소셜 사용자 계정을 삭제합니다.

```XML
<TechnicalProfile Id="AAD-DeleteUserUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="Operation">DeleteClaimsPrincipal</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" Required="true" />
  </InputClaims>
  <OutputClaims/>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```
## <a name="metadata"></a>메타데이터

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 작업(Operation) | yes | 수행할 작업입니다. 가능한 값은 `Read`, `Write`, `DeleteClaims` 또는 `DeleteClaimsPrincipal`입니다. |
| RaiseErrorIfClaimsPrincipalDoesNotExist | 예 | 사용자 개체가 디렉터리에 없는 경우 오류가 발생합니다. 가능한 값은 `true` 또는 `false`입니다. |
| UserMessageIfClaimsPrincipalDoesNotExist | 예 | 오류가 발생해야 한다면(RaiseErrorIfClaimsPrincipalDoesNotExist 특성 설명 참조), 사용자 개체가 없는 경우 사용자에게 표시할 메시지를 지정합니다. 값을 [지역화](localization.md)할 수 있습니다.|
| RaiseErrorIfClaimsPrincipalAlreadyExists | 예 | 사용자 개체가 이미 있는 경우 오류가 발생합니다. 가능한 값은 `true` 또는 `false`입니다.|
| UserMessageIfClaimsPrincipalAlreadyExists | 예 | 오류가 발생해야 한다면(RaiseErrorIfClaimsPrincipalAlreadyExists 특성 설명 참조), 사용자 개체가 이미 있는 경우 사용자에게 표시할 메시지를 지정합니다. 값을 [지역화](localization.md)할 수 있습니다.|
| ApplicationObjectId | 예 | 확장 특성에 대한 애플리케이션 개체 ID입니다. 값은 애플리케이션의 ObjectId입니다. 자세한 내용은 [사용자 지정 프로필 편집 정책에서 사용자 지정 특성 사용](custom-policy-custom-attributes.md)을 참조하세요. |
| clientid | 예 | 테넌트에 제3자로 액세스하기 위한 클라이언트 식별자입니다. 자세한 내용은 [사용자 지정 프로필 편집 정책에서 사용자 지정 특성 사용](custom-policy-custom-attributes.md)을 참조하세요. |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 [클레임 확인](claim-resolver-overview.md) 이 기술 프로필에 포함 되는지 여부를 지정 합니다. 가능한 값은 `true`또는 `false` (기본값)입니다. 기술 프로필에서 클레임 해결 프로그램을 사용 하려면 `true`으로 설정 합니다. |














