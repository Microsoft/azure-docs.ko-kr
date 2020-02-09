---
title: TechnicalProfiles
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책의 TechnicalProfiles 요소를 지정합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ad6b84323ac49713506bc61bd0051421e0234a94
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982282"
---
# <a name="technicalprofiles"></a>TechnicalProfiles

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**TechnicalProfiles** 요소는 클레임 공급자가 지원하는 기술 프로필 집합을 포함합니다. 모든 클레임 공급자에는 엔드포인트를 결정하는 하나 이상의 기술 프로필 및 클레임 공급자와 통신하는 데 필요한 프로토콜이 있어야 합니다. 하나의 클레임 공급자가 여러 개의 기술 프로필을 포함할 수 있습니다.

```XML
<ClaimsProvider>
  <DisplayName>Display name</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Technical profile identifier">
      <DisplayName>Display name of technical profile</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">URL of service</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
      </Metadata>
      <InputTokenFormat>JWT</InputTokenFormat>
      <OutputTokenFormat>JWT</OutputTokenFormat>
      <CryptographicKeys>
        <Key ID="Key identifier" StorageReferenceId="Storage key container identifier"/>
        ...
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="givenName" DefaultValue="givenName" PartnerClaimType="firstName" />
        ...
      </PersistedClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="Claims transformation identifier" />
        ...
      <OutputClaimsTransformations>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="Technical profile identifier" />
        ...
      </ValidationTechnicalProfiles>
      <SubjectNamingInfo ClaimType="Claim type identifier" />
      <IncludeTechnicalProfile ReferenceId="Technical profile identifier" />
      <UseTechnicalProfileForSessionManagement ReferenceId="Technical profile identifier" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**TechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
|---------|---------|---------|
| Id | 예 | 기술 프로필의 고유 식별자입니다. 정책 파일의 다른 요소에서 이 식별자를 사용하여 기술 프로필을 참조할 수 있습니다. 예를 들어 **OrchestrationSteps** 및 **ValidationTechnicalProfile**입니다. |

**TechnicalProfile**에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 도메인 | 0:1 | 기술 프로필의 도메인 이름입니다. 예를 들어 기술 프로필이 Facebook ID 공급자를 지정하는 경우 도메인 이름은 Facebook.com입니다. |
| DisplayName | 1:1 | 사용자에게 표시할 수 있는 기술 프로필의 이름입니다. |
| Description | 0:1 | 사용자에게 표시할 수 있는 기술 프로필에 대한 설명입니다. |
| 프로토콜 | 0:1 | 다른 당사자와의 통신에 사용되는 프로토콜입니다. |
| 메타데이터 | 0:1 | 트랜잭션 과정에서 엔드포인트와 통신하기 위해 프로토콜에서 사용하는 키/값 쌍의 컬렉션입니다. |
| InputTokenFormat | 0:1 | 입력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 또는 `SAML2`입니다. `JWT` 값은 IETF 사양을 기준으로 JSON Web Token을 나타냅니다. `SAML11` 값은 OASIS 사양을 기준으로 SAML 1.1 보안 토큰을 나타냅니다.  `SAML2` 값은 OASIS 사양을 기준으로 SAML 2.0 보안 토큰을 나타냅니다. |
| OutputTokenFormat | 0:1 | 출력 토큰의 형식입니다. 가능한 값은 `JSON`, `JWT`, `SAML11` 또는 `SAML2`입니다. |
| CryptographicKeys | 0:1 | 기술 프로필에 사용되는 암호화 키 목록입니다. |
| InputClaimsTransformations | 0:1 | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| InputClaims | 0:1 | 기술 프로필에서 입력으로 사용되는 클레임 유형에 대한 이전에 정의된 참조 목록입니다. |
| PersistedClaims | 0:1 | 기술 프로필과 관련된 클레임 공급자가 저장하는 클레임 유형에 대한 이전에 정의된 참조 목록입니다. |
| DisplayClaims | 0:1 | [자체 어설션된 기술 프로필과](self-asserted-technical-profile.md)관련 된 클레임 공급자가 제공 하는 클레임 유형에 대한 이전에 정의 된 참조 목록입니다. DisplayClaims 기능은 현재 **미리 보기**상태입니다. |
| OutputClaims | 0:1 | 기술 프로필에서 출력으로 사용되는 클레임 유형에 대한 이전에 정의된 참조 목록입니다. |
| OutputClaimsTransformations | 0:1 | 클레임 공급자로부터 클레임이 수신된 후에 실행해야 하는 클레임 변환에 대한 이전에 정의된 참조 목록입니다. |
| ValidationTechnicalProfiles | 0:n | 기술 프로필이 유효성 검사를 위해 사용하는 다른 기술 프로필에 대한 참조 목록입니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 참조하세요.|
| SubjectNamingInfo | 0:1 | 주체 이름이 클레임과 별도로 지정되는 토큰에서 주체 이름 생성을 제어합니다. 예를 들어 OAuth 또는 SAML입니다.  |
| IncludeInSso | 0:1 |  이 기술 프로필을 사용 하 여 세션에 대해 SSO (Single Sign-On) 동작을 적용 해야 하는지, 아니면 명시적으로 상호 작용 해야 하는지 여부입니다. 이 요소는 유효성 검사 기술 프로필 내에서 사용 되는 SelfAsserted 프로필에만 사용할 수 있습니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| IncludeClaimsFromTechnicalProfile | 0:1 | 모든 입력 및 출력 클레임을 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. 참조된 기술 프로필은 동일한 정책 파일에 정의되어야 합니다. |
| IncludeTechnicalProfile |0:1 | 모든 데이터를 이 기술 프로필에 추가하려는 기술 프로필의 식별자입니다. 참조된 기술 프로필은 동일한 정책 파일에 있어야 합니다. |
| UseTechnicalProfileForSessionManagement | 0:1 | 세션 관리에 사용할 다른 기술 프로필입니다. |
|EnabledForUserJourneys| 0:1 |사용자 경험에서 기술 프로필을 실행할지 여부를 제어합니다.  |

## <a name="protocol"></a>프로토콜

**Protocol** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 이름 | 예 | 기술 프로필의 일부로 사용되는 Azure AD B2C에서 지원하는 유효한 프로토콜의 이름입니다. 가능한 값: `OAuth1`, `OAuth2`, `SAML2`, `OpenIdConnect`, `Proprietary`, `session management`, `self-asserted`또는 `None`. |
| Handler | 아닙니다. | 프로토콜 이름이 `Proprietary`로 설정된 경우 Azure AD B2C에서 프로토콜 처리기를 확인하는 데 사용하는 어셈블리의 정규화된 이름을 지정합니다. |

## <a name="metadata"></a>메타데이터

**Metadata** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 항목 | 0:n | 기술 프로필과 관련된 메타데이터입니다. 기술 프로필 유형마다 다른 메타데이터 항목 집합이 있습니다. 자세한 내용은 기술 프로필 유형 섹션을 참조하세요. |

### <a name="item"></a>항목

**Metadata** 요소의 **Item** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 키 | 예 | 메타데이터 키입니다. 메타데이터 항목 목록은 각 기술 프로필 유형을 참조하세요. |

## <a name="cryptographickeys"></a>CryptographicKeys

**CryptographicKeys** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| 키 | 1:n | 이 기술 프로필에서 사용되는 암호화 키입니다. |

### <a name="key"></a>키

**Key** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| Id | 아닙니다. | 정책 파일의 다른 요소에서 참조되는 특정 키 쌍의 고유 식별자입니다. |
| StorageReferenceId | 예 | 정책 파일의 다른 요소에서 참조되는 스토리지 키 컨테이너의 식별자입니다. |

## <a name="inputclaimstransformations"></a>InputClaimsTransformations

**InputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| InputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="inputclaimstransformation"></a>InputClaimsTransformation

**InputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

## <a name="inputclaims"></a>InputClaims

**InputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| InputClaim | 1:n | 필요한 입력 클레임 유형입니다. |

### <a name="inputclaim"></a>InputClaim

**InputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 아닙니다. | ClaimTypeReferenceId로 표시된 클레임이 없는 경우 결과 클레임이 기술 프로필에서 InputClaim로 사용될 수 있도록 클레임을 만드는 데 사용할 기본값입니다. |
| PartnerClaimType | 아닙니다. | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

## <a name="displayclaims"></a>DisplayClaims

**DisplayClaims** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| DisplayClaim | 1:n | 필요한 입력 클레임 유형입니다. |

DislayClaims 기능은 현재 **미리 보기**상태입니다.

### <a name="displayclaim"></a>DisplayClaim

**DisplayClaim** 요소는 다음 특성을 포함 합니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 아닙니다. | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DisplayControlReferenceId | 아닙니다. | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의 되어 있는 [표시 컨트롤](display-controls.md) 의 식별자입니다. |
| 필수 | 아닙니다. | 표시 클레임이 필요한 지 여부를 나타냅니다. |

**DisplayClaim** `ClaimTypeReferenceId` 또는 `DisplayControlReferenceId`를 지정 해야 합니다.

### <a name="persistedclaims"></a>PersistedClaims

**PersistedClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| PersistedClaim | 1:n | 저장할 클레임 유형입니다. |

### <a name="persistedclaim"></a>PersistedClaim

**PersistedClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 아닙니다. | ClaimTypeReferenceId로 표시된 클레임이 없는 경우 결과 클레임이 기술 프로필에서 InputClaim로 사용될 수 있도록 클레임을 만드는 데 사용할 기본값입니다. |
| PartnerClaimType | 아닙니다. | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 1:n | 필요한 출력 클레임 유형입니다. |

### <a name="outputclaim"></a>OutputClaim

**OutputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일 또는 부모 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |
| DefaultValue | 아닙니다. | ClaimTypeReferenceId로 표시된 클레임이 없는 경우 결과 클레임이 기술 프로필에서 InputClaim로 사용될 수 있도록 클레임을 만드는 데 사용할 기본값입니다. |
|AlwaysUseDefaultValue |아닙니다. |기본값을 강제로 사용합니다.  |
| PartnerClaimType | 아닙니다. | 지정한 정책 클레임 유형이 매핑되는 외부 파트너의 클레임 유형 식별자입니다. PartnerClaimType 특성이 지정되지 않은 경우 지정한 정책 클레임 유형이 동일한 이름의 파트너 클레임 유형에 매핑됩니다. 클레임 유형 이름이 다른 당사자와 다른 경우에 이 속성을 사용합니다. 예를 들어 첫 번째 클레임 이름은 ‘givenName’이고 파트너는 first_name’이라는 클레임을 사용합니다. |

## <a name="outputclaimstransformations"></a>OutputClaimsTransformations

**OutputClaimsTransformations** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| OutputClaimsTransformation | 1:n | 클레임 공급자 또는 신뢰 당사자에게 클레임이 전송되기 전에 실행해야 하는 클레임 변환의 식별자입니다. 클레임 변환을 사용하여 기존 ClaimsSchema 클레임을 수정하거나 새 클레임을 생성할 수 있습니다. |

### <a name="outputclaimstransformation"></a>OutputClaimsTransformation

**OutputClaimsTransformation** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 클레임 변환의 식별자입니다. |

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1:n | 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용되는 기술 프로필의 식별자입니다. 참조된 기술 프로필의 모든 입력 클레임이 참조 기술 프로필의 출력 클레임에 표시되어야 합니다. |

### <a name="validationtechnicalprofile"></a>ValidationTechnicalProfile

**ValidationTechnicalProfile** 요소에 포함되는 특성은 다음과 같습니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNamingInfo**에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimType | 예 | 정책 파일의 ClaimsSchema 섹션에 이미 정의된 클레임 유형의 식별자입니다. |

## <a name="includetechnicalprofile"></a>IncludeTechnicalProfile

**IncludeTechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="usetechnicalprofileforsessionmanagement"></a>UseTechnicalProfileForSessionManagement

**UseTechnicalProfileForSessionManagement** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책 파일 또는 부모 정책 파일에 이미 정의된 기술 프로필의 식별자입니다. |

## <a name="enabledforuserjourneys"></a>EnabledForUserJourneys

사용자 경험의 **ClaimsProviderSelections**는 클레임 공급자 선택 옵션과 해당 순서를 정의합니다. **EnabledForUserJourneys** 요소를 사용하여 사용자에게 제공되는 클레임 공급자를 필터링합니다. **EnabledForUserJourneys** 요소에는 다음 값 중 하나가 포함됩니다.

- **Always** - 항상 기술 프로필을 실행합니다.
- **Never** - 기술 프로필을 건너뜁니다.
- **OnClaimsExistence** - 기술 프로필에 지정된 특정 클레임이 있는 경우에만 실행합니다.
- **OnItemExistenceInStringCollectionClaim** - 문자열 컬렉션 클레임에 항목이 있는 경우에만 실행합니다.
- **OnItemAbsenceInStringCollectionClaim** - 문자열 컬렉션 클레임에 항목이 없는 경우에만 실행합니다.

**OnClaimsExistence**, **OnItemExistenceInStringCollectionClaim** 또는 **OnItemAbsenceInStringCollectionClaim**을 사용하려면 다음 메타데이터를 제공해야 합니다. **ClaimTypeOnWhichToEnable**은 평가할 클레임 유형을 지정하고, **ClaimValueOnWhichToEnable**은 비교할 값을 지정합니다.

다음 기술 프로필은 **identityProviders** 문자열 컬렉션에 `facebook.com` 값이 포함된 경우에만 실행됩니다.

```XML
<TechnicalProfile Id="UnLink-Facebook-OAUTH">
  <DisplayName>Unlink Facebook</DisplayName>
...
    <Metadata>
      <Item Key="ClaimTypeOnWhichToEnable">identityProviders</Item>
      <Item Key="ClaimValueOnWhichToEnable">facebook.com</Item>
    </Metadata>
...
  <EnabledForUserJourneys>OnItemExistenceInStringCollectionClaim</EnabledForUserJourneys>
</TechnicalProfile>
```
