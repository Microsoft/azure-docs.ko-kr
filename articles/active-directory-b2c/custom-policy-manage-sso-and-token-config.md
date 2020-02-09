---
title: 사용자 지정 정책을 사용 하 여 SSO 및 토큰 사용자 지정 관리
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정을 관리하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 299ab8ec4f6f8f74cccef15720622e0638285a63
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846589"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SSO 및 토큰 사용자 지정 관리

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 토큰, 세션 및 SSO (Single Sign-On) 구성을 관리 하는 방법에 대한 정보를 제공 합니다.

## <a name="token-lifetimes-and-claims-configuration"></a>토큰 수명 및 클레임 구성

토큰 수명 설정을 변경하려면 변경할 정책의 신뢰 당사자 파일에 [ClaimsProviders](claimsproviders.md) 요소를 추가합니다.  **ClaimsProviders** 요소는 [TrustFrameworkPolicy](trustframeworkpolicy.md) 요소의 자식입니다.

BasePolicy 요소와 신뢰 당사자 파일의 RelyingParty 요소 사이에 ClaimsProviders 요소를 삽입 합니다.

이 안에 토큰 수명에 영향을 주는 정보를 입력해야 합니다. XML은 다음과 비슷합니다.

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

위의 예제에서는 다음 값이 설정됩니다.

- **액세스 토큰 수명** - **token_lifetime_secs** 메타데이터 항목을 통해 액세스 토큰 수명 값이 설정됩니다. 기본값은 3600초(60분)입니다.
- **ID 토큰 수명** - **id_token_lifetime_secs** 메타데이터 항목을 통해 ID 토큰 수명 값이 설정됩니다. 기본값은 3600초(60분)입니다.
- **새로 고침 토큰 수명** - **refresh_token_lifetime_secs** 메타데이터 항목을 통해 새로 고침 토큰 수명 값이 설정됩니다. 기본값은 1209600초(14일)입니다.
- **새로 고침 토큰 슬라이딩 윈도우 수명** - 새로 고침 토큰에 대한 슬라이딩 윈도우 수명을 설정하려면 **rolling_refresh_token_lifetime_secs** 메타데이터 항목의 값을 설정합니다. 기본값은 7776000(90일)입니다. 슬라이딩 윈도우 수명을 적용하지 않으려면 이 항목을 `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`로 바꿉니다.
- **발급자(iss) 클레임** - **IssuanceClaimPattern** 메타데이터 항목을 통해 발급자(iss) 클레임을 설정합니다. 적용 가능한 값은 `AuthorityAndTenantGuid` 및 `AuthorityWithTfp`입니다.
- **정책 ID를 나타내는 클레임 설정** - 이 값을 설정하기 위한 옵션은 `TFP`(보안 프레임워크 정책) 및 `ACR`(인증 컨텍스트 참조)입니다. 권장 값은 `TFP`입니다. `None` 값으로 **AuthenticationContextReferenceClaimPattern**을 설정하고

    **ClaimsSchema** 요소에서 이 요소를 추가합니다.

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    **OutputClaims** 요소에 다음 요소를 추가합니다.

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    ACR의 경우 **AuthenticationContextReferenceClaimPattern** 항목을 제거합니다.

- **주체(sub) 클레임** - 이 옵션은 기본적으로 ObjectID입니다. 이 설정을 `Not Supported`로 전환하려면 아래 줄을

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    다음 줄로 바꿉니다.

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>세션 동작 및 SSO

세션 동작 및 SSO 구성을 변경하려면 [RelyingParty](relyingparty.md) 요소 내에 **UserJourneyBehaviors** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 바로 뒤에 있어야 합니다. **UserJourneyBehavors** 요소 내부는 다음 예제와 같이 표시됩니다.

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

위의 예제에서는 다음 값이 구성됩니다.

- **SSO(Single Sign-On)** - **SingleSignOn**을 사용하여 Single Sign-On이 구성됩니다. 적용 가능한 값은 `Tenant`, `Application`, `Policy` 및 `Suppressed`입니다.
- **웹앱 세션 시간 제한** - **SessionExpiryType** 요소를 사용하여 웹앱 세션 시간 제한을 설정합니다. 적용 가능한 값은 `Absolute` 및 `Rolling`입니다.
- **웹 앱 세션 수명** - **SessionExpiryInSeconds** 요소를 사용 하 여 웹 앱 세션 수명을 설정 합니다. 기본값은 86400초(1440분)입니다.
