---
title: 사용자 지정 정책을 사용 하 여 LinkedIn 계정으로 로그인 설정
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 LinkedIn 계정으로 로그인을 설정 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f4ad74104d34e7e2c3f4d6aafc05b36574f8e287
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847434"
---
# <a name="set-up-sign-in-with-a-linkedin-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 LinkedIn 계정으로 로그인하도록 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 LinkedIn 계정의 사용자에 대한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.
- LinkedIn 계정-아직 없는 경우 [계정을 만듭니다](https://www.linkedin.com/start/join).
- LinkedIn 페이지-다음 섹션에서 만든 LinkedIn 응용 프로그램과 연결할 [Linkedin 페이지가](https://www.linkedin.com/help/linkedin/answer/710/creating-a-linkedin-company-page) 필요 합니다.

## <a name="create-an-application"></a>애플리케이션 만들기

Azure AD B2C에서 ID 공급자로 LinkedIn을 사용하려면 LinkedIn 애플리케이션을 만들어야 합니다.

### <a name="create-app"></a>앱 만들기

1. LinkedIn 계정 자격 증명을 사용하여 [LinkedIn 애플리케이션 관리](https://www.linkedin.com/secure/developer?newapp=) 웹 사이트에 로그인합니다.
1. **앱 만들기**를 선택합니다.
1. **앱 이름을**입력 합니다.
1. LinkedIn 페이지 이름에 해당 하는 **회사** 이름을 입력 합니다. 아직 없는 경우 LinkedIn 페이지를 만듭니다.
1. 필드 **개인 정보 취급 방침 URL**을 입력 합니다. 이 URL은 유효한 URL 이어야 하지만 연결할 수 있는 엔드포인트이 될 필요는 없습니다.
1. **비즈니스 전자 메일**을 입력 합니다.
1. **앱 로고** 이미지를 업로드 합니다. 로고 이미지는 정사각형이 고 해당 크기는 최소 100x100 픽셀 이어야 합니다.
1. **제품** 섹션에서 기본 설정을 그대로 둡니다.
1. **약관**에 제공 된 정보를 검토 합니다. 조건에 동의 하면 확인란을 선택 합니다.
1. **앱 만들기**를 선택합니다.

### <a name="configure-auth"></a>인증 구성

1. **인증** 탭을 선택 합니다.
1. **클라이언트 ID**를 기록 합니다.
1. **클라이언트 암호**를 표시 하 고 기록 합니다.
1. **OAuth 2.0 설정**에서 다음 **리디렉션 URL**을 추가 합니다. `your-tenant`을 테넌트 이름으로 바꿉니다. Azure AD B2C에 대문자를 사용 하 여 정의 된 경우에도 **모든 소문자** 를 사용 하 여 테넌트 이름입니다.

    `https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp`

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테넌트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키** 를 선택 하 고 **추가**를 선택 합니다.
6. **옵션**으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름**을 입력합니다. `LinkedInSecret`)을 입력합니다. *B2C_1A_* 접두사는 키 이름에 자동으로 추가 됩니다.
8. **비밀**에서 이전에 기록한 클라이언트 암호를 입력 합니다.
9. **키 사용**에서 `Signature`를 선택합니다.
10. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 LinkedIn 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책 확장 파일의 **ClaimsProviders** 요소에 LinkedIn 계정을 추가 하 여 해당 계정을 클레임 공급자로 정의 합니다.

1. 편집기에서 *Socialandlocalaccounts/ **trustframeworkextensions.xml*** 파일을 엽니다. 이 파일은 필수 구성 요소 중 하나의 일부로 다운로드 한 [사용자 지정 정책 시작 팩][starter-pack] 에 있습니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAUTH">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. **Client_id** 의 값을 이전에 기록한 LinkedIn 응용 프로그램의 클라이언트 id로 바꿉니다.
1. 파일을 저장합니다.

### <a name="add-the-claims-transformations"></a>클레임 변환 추가

LinkedIn 기술 프로필을 사용 하려면 **ExtractGivenNameFromLinkedInResponse** 및 **ExtractSurNameFromLinkedInResponse** 클레임 변환을 ClaimsTransformations 목록에 추가 해야 합니다. **ClaimsTransformations** 요소가 파일에 정의 되어 있지 않은 경우 아래와 같이 부모 XML 요소를 추가 합니다. 클레임 변환에는 **Nullstringclaim**이라는 새로운 클레임 유형도 필요 합니다.

*Trustframeworkextensions.xml* 파일의 상단 근처에 **BuildingBlocks** 요소를 추가 합니다. 예는 *trustframeworkbase.xml* 을 참조 하세요.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

이제 Azure AD B2C에서 LinkedIn 계정과 통신 하는 방법을 알 수 있도록 정책이 구성 되었습니다. 정책의 확장 파일을 업로드 하 여 지금까지 문제가 발생 하지 않았는지 확인 하세요.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 id 공급자가 설정 되었지만 등록 또는 로그인 화면에는 제공 되지 않습니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 LinkedIn ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *trustframeworkbase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInLinkedIn`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. LinkedIn 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelections** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `LinkedInExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에는 Azure AD B2C가 LinkedIn 계정과 통신하여 토큰을 받는 작업을 연결합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    ```

    **TechnicalProfileReferenceId**의 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다. `LinkedIn-OAUTH`)을 입력합니다.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInLinkedIn.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInLinkedIn`)을 입력합니다.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_linkedin`으로 업데이트할 수 있습니다.
4. 새로 만든 사용자 경험의 ID(SignUpSignLinkedIn)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
6. **애플리케이션 선택** 필드에서 직접 만든 Azure AD B2C 애플리케이션이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.

## <a name="migration-from-v10-to-v20"></a>V 1.0에서 v2.0로 마이그레이션

LinkedIn [은 최근에 해당 api를 v1.0에서 v 2.0으로 업데이트 했습니다](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). 기존 구성을 새 구성으로 마이그레이션하려면 다음 섹션의 정보를 사용 하 여 기술 프로필의 요소를 업데이트 합니다.

### <a name="replace-items-in-the-metadata"></a>메타 데이터의 항목 바꾸기

**TechnicalProfile**의 기존 **메타 데이터** 요소에서 다음 **항목** 요소를 업데이트 합니다.

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

아래와 같이 변경합니다.

```XML
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>메타 데이터에 항목 추가

**TechnicalProfile**의 **메타 데이터** 에서 다음 **항목** 요소를 추가 합니다.

```XML
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>OutputClaims 업데이트 합니다.

**TechnicalProfile**의 기존 **Outputclaims** 에서 다음 **outputclaims** 요소를 업데이트 합니다.

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

아래와 같이 변경합니다.

```XML
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>새 OutputClaimsTransformation 요소 추가

**TechnicalProfile**의 **OutputClaimsTransformations** 에 다음 **OutputClaimsTransformation** 요소를 추가 합니다.

```XML
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>새 클레임 변환 및 클레임 유형 정의

마지막 단계에서 정의 해야 하는 새 클레임 변환을 추가 했습니다. 클레임 변환을 정의 하려면 **ClaimsTransformations**의 목록에 추가 합니다. **ClaimsTransformations** 요소가 파일에 정의 되어 있지 않은 경우 아래와 같이 부모 XML 요소를 추가 합니다. 클레임 변환에는 **Nullstringclaim**이라는 새로운 클레임 유형도 필요 합니다.

**BuildingBlocks** 요소는 파일의 위쪽 근처에 추가 해야 합니다. 예제를 보려면 *trustframeworkbase.xml* 를 참조 하세요.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>전자 메일 주소 가져오기

V1.0에서 v 2.0으로 마이그레이션하는 과정의 일환으로, 전자 메일 주소를 가져오려면 다른 API에 대한 추가 호출이 필요 합니다. 등록 하는 동안 전자 메일 주소를 받아야 하는 경우 다음을 수행 합니다.

1. 사용자가 로그인 할 수 있도록 LinkedIn과 페더레이션 할 Azure AD B2C 허용 하려면 위의 단계를 완료 합니다. 페더레이션의 일부로, Azure AD B2C는 LinkedIn에 대한 액세스 토큰을 받습니다.
2. LinkedIn 액세스 토큰을 클레임에 저장 합니다. [여기에서 지침을 참조](idp-pass-through-custom.md)하세요.
3. LinkedIn의 `/emailAddress` API에 요청을 수행 하는 다음과 같은 클레임 공급자를 추가 합니다. 이 요청에 권한을 부여 하려면 LinkedIn 액세스 토큰이 필요 합니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. 사용자가 LinkedIn을 사용 하 여 로그인 할 때 API 클레임 공급자가 트리거될 수 있도록 다음 오케스트레이션 단계를 사용자 경험에 추가 합니다. `Order` 수를 적절 하 게 업데이트 해야 합니다. LinkedIn 기술 프로필을 트리거하는 오케스트레이션 단계 바로 뒤에이 단계를 추가 합니다.

    ```XML
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

등록 하는 동안 LinkedIn에서 전자 메일 주소를 가져오는 것은 선택 사항입니다. LinkedIn에서 전자 메일을 받지 않지만 등록 중에는 전자 메일을 받아야 하는 경우 사용자는 수동으로 전자 메일 주소를 입력 하 고 유효성을 검사 해야 합니다.

LinkedIn id 공급자를 사용 하는 정책의 전체 샘플은 [사용자 지정 정책 시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider)을 참조 하세요.

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
