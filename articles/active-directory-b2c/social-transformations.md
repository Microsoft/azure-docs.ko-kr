---
title: 사용자 지정 정책에 대한 소셜 계정 클레임 변환 예제
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 IEF (Identity Experience Framework) 스키마에 대한 소셜 계정 클레임 변환 예입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9df00eea79b5dedc3211de02b17fe8f396d7b8a5
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951074"
---
# <a name="social-accounts-claims-transformations"></a>소셜 계정 클레임 변환

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C)에서 소셜 계정 id는 **alternativeSecurityIdCollection** 클레임 유형의 `userIdentities` 특성에 저장 됩니다. **alternativeSecurityIdCollection**의 각 항목은 발급자(ID 공급자 이름, 예: facebook.com) 및 발급자의 고유한 사용자 식별자인 `issuerUserId`를 지정합니다.

```JSON
"userIdentities": [{
    "issuer": "google.com",
    "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"
  },
  {
    "issuer": "facebook.com",
    "issuerUserId": "MTIzNDU="
  }]
```

이 문서에서는 Azure AD B2C에서 ID 경험 프레임워크 스키마의 소셜 계정 클레임 변환을 사용하는 예를 제공합니다. 자세한 내용은 [ClaimsTransformations](claimstransformations.md)를 참조하세요.

## <a name="createalternativesecurityid"></a>CreateAlternativeSecurityId

Azure Active Directory 호출에서 사용할 수 있는, 사용자의 alternativeSecurityId 속성에 대한 JSON 표현을 만듭니다. 자세한 내용은 [AlternativeSecurityId의 스키마](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#alternativesecurityid-type)를 참조하세요.

| 항목 | TransformationClaimType | 데이터 형식 | 참고 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | key | 문자열 | 소셜 ID 공급자가 사용하는 고유한 사용자 식별자를 지정하는 ClaimType입니다. |
| InputClaim | identityProvider | 문자열 | 소셜 계정 ID 공급자 이름(예: facebook.com)을 지정하는 ClaimType입니다. |
| OutputClaim | alternativeSecurityId | 문자열 | ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. 소셜 계정 사용자의 ID에 대한 정보를 포함합니다. **issuer**는 `identityProvider` 클레임의 값입니다. **issuerUserId**는 base64 형식인 `key` 클레임의 값입니다. |

이 클레임 변환을 사용하여 `alternativeSecurityId` ClaimType을 생성할 수 있습니다. 모든 소셜 ID 공급자 기술 프로필(예: `Facebook-OAUTH`)에서 사용됩니다. 다음 클레임 변환은 사용자 소셜 계정 ID와 ID 공급자 이름을 수신합니다. 이 기술 프로필의 출력은 Azure AD 디렉터리 서비스에서 사용할 수 있는 JSON 문자열 형식입니다.

```XML
<ClaimsTransformation Id="CreateAlternativeSecurityId" TransformationMethod="CreateAlternativeSecurityId">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="issuerUserId" TransformationClaimType="key" />
    <InputClaim ClaimTypeReferenceId="identityProvider" TransformationClaimType="identityProvider" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="alternativeSecurityId" TransformationClaimType="alternativeSecurityId" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **key**: 12334
    - **identityProvider**: Facebook.com
- 출력 클레임:
    - **alternativeSecurityId**: { "issuer": "facebook.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw"}

## <a name="additemtoalternativesecurityidcollection"></a>AddItemToAlternativeSecurityIdCollection

`alternativeSecurityIdCollection` 클레임에 `AlternativeSecurityId`를 추가합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 참고 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | 항목 | 문자열 | 출력 클레임에 추가할 ClaimType입니다. |
| InputClaim | collection | alternativeSecurityIdCollection | 정책에 사용 가능한 경우 클레임 변환에서 사용하는 ClaimType입니다. 제공된 경우 클레임 변환은 컬렉션의 끝에 `item`을 추가합니다. |
| OutputClaim | collection | alternativeSecurityIdCollection | 이 ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. 입력 `collection` 및 `item`의 항목을 둘 다 포함하는 새 컬렉션입니다. |

다음 예제는 새 소셜 ID를 기존 계정에 연결합니다. 새 소셜 ID를 연결하려면
1. **AAD-UserReadUsingAlternativeSecurityId** 및 **AAD-UserReadUsingObjectId** 기술 프로필에서 사용자의 **alternativeSecurityIds** 클레임을 출력합니다.
1. 사용자에게 이 사용자와 연결되지 않은 ID 공급자 중 하나로 로그인하도록 요청합니다.
1. **CreateAlternativeSecurityId** 클레임 변환을 사용하여 이름이 `AlternativeSecurityId2`인 새 **alternativeSecurityId** 클레임 유형을 만듭니다.
1. **AddItemToAlternativeSecurityIdCollection** 클레임 변환을 호출하여 **AlternativeSecurityId2** 클레임을 기존 **AlternativeSecurityIds** 클레임에 추가합니다.
1. **alternativeSecurityIds** 클레임을 사용자 계정에 저장합니다.

```XML
<ClaimsTransformation Id="AddAnotherAlternativeSecurityId" TransformationMethod="AddItemToAlternativeSecurityIdCollection">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityId2" TransformationClaimType="item" />
    <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
  </OutputClaims>
</ClaimsTransformation>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **item**: { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" }
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
- 출력 클레임:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]

## <a name="getidentityprovidersfromalternativesecurityidcollectiontransformation"></a>GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation

**alternativeSecurityIdCollection** 클레임의 발급자 목록을 새 **stringCollection** 클레임에 반환합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 참고 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | alternativeSecurityIdCollection | alternativeSecurityIdCollection | ID 공급자(발급자) 목록을 가져오는 데 사용할 ClaimType입니다. |
| OutputClaim | identityProvidersCollection | stringCollection | 이 ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. alternativeSecurityIdCollection 입력 클레임과 연결된 ID 공급자 목록 |

다음 클레임 변환은 사용자 **alternativeSecurityIds** 클레임을 읽고 해당 계정과 연결된 ID 공급자 이름 목록을 추출합니다. 출력 **identityProvidersCollection**을 사용하여 계정과 연결된 ID 공급자 목록을 사용자에게 표시합니다. 또는 ID 공급자 선택 페이지에서 출력 **identityProvidersCollection** 클레임을 기준으로 ID 공급자 목록을 필터링합니다. 따라서 사용자는 아직 계정과 연결되지 않은 새 소셜 ID를 연결하도록 선택할 수 있습니다.

```XML
<ClaimsTransformation Id="ExtractIdentityProviders" TransformationMethod="GetIdentityProvidersFromAlternativeSecurityIdCollectionTransformation">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityIds" TransformationClaimType="alternativeSecurityIdCollection" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="identityProviders" TransformationClaimType="identityProvidersCollection" />
  </OutputClaims>
</ClaimsTransformation>
```

- 입력 클레임:
    - **alternativeSecurityIdCollection**: [ { "issuer": "google.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- 출력 클레임:
    - **identityProvidersCollection**: [ "facebook.com", "google.com" ]

## <a name="removealternativesecurityidbyidentityprovider"></a>RemoveAlternativeSecurityIdByIdentityProvider

**alternativeSecurityIdCollection** 클레임에서 **AlternativeSecurityId**를 제거합니다.

| 항목 | TransformationClaimType | 데이터 형식 | 참고 |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | identityProvider | 문자열 | 컬렉션에서 제거할 ID 공급자 이름을 포함하는 ClaimType입니다. |
| InputClaim | collection | alternativeSecurityIdCollection | 클레임 변환에서 사용하는 ClaimType입니다. 클레임 변환은 컬렉션에서 identityProvider를 제거합니다. |
| OutputClaim | collection | alternativeSecurityIdCollection | 이 ClaimsTransformation을 호출한 후 생성되는 ClaimType입니다. 컬렉션에서 identityProvider가 제거된 후의 새 컬렉션입니다. |

다음 예제는 기존 계정과 소셜 ID 중 하나의 연결을 해제합니다. 소셜 ID의 연결을 해제하려면
1. **AAD-UserReadUsingAlternativeSecurityId** 및 **AAD-UserReadUsingObjectId** 기술 프로필에서 사용자의 **alternativeSecurityIds** 클레임을 출력합니다.
2. 사용자에게 이 사용자와 연결된 ID 공급자 목록에서 제거할 소셜 계정을 선택하도록 요청합니다.
3. ID 공급자 이름을 사용하여 선택한 소셜 ID를 제거한 **RemoveAlternativeSecurityIdByIdentityProvider** 클레임 변환을 호출하는 클레임 변환 기술 프로필을 호출합니다.
4. **alternativeSecurityIds** 클레임을 사용자 계정에 저장합니다.

```XML
<ClaimsTransformation Id="RemoveAlternativeSecurityIdByIdentityProvider" TransformationMethod="RemoveAlternativeSecurityIdByIdentityProvider">
    <InputClaims>
        <InputClaim ClaimTypeReferenceId="secondIdentityProvider" TransformationClaimType="identityProvider" />
        <InputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="AlternativeSecurityIds" TransformationClaimType="collection" />
    </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformations>
```

### <a name="example"></a>예제

- 입력 클레임:
    - **identityProvider**: facebook.com
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" }, { "issuer": "facebook.com", "issuerUserId": "MTIzNDU=" } ]
- 출력 클레임:
    - **collection**: [ { "issuer": "live.com", "issuerUserId": "MTA4MTQ2MDgyOTI3MDUyNTYzMjcw" } ]
