---
title: 사용자 지정 정책을 사용 하 여 Google 계정으로 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 LinkedIn 계정으로 로그인하도록 설정하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: ed11fcb24f06eae1b2baa6975a3cd8016042813e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847460"
---
# <a name="set-up-sign-in-with-a-google-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Google 계정으로 로그인하도록 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 Google 계정을 사용 하 여 사용자에 대한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](custom-policy-get-started.md)에 있는 단계를 완료합니다.
- Google 계정이 아직 없으면 [Google 계정 만들기](https://accounts.google.com/SignUp)에서 하나 만듭니다.

## <a name="register-the-application"></a>애플리케이션 등록

사용자가 Google 계정에서 로그인하도록 설정하려면 Google 애플리케이션 프로젝트를 만들어야 합니다.

1. 계정 자격 증명을 사용하여 [Google 개발자 콘솔](https://console.developers.google.com/)에 로그인합니다.
2. **프로젝트 이름**을 입력하고 **만들기**를 클릭한 다음 새 프로젝트를 사용 중인지 확인합니다.
3. 왼쪽 메뉴에서 **자격 증명**을 선택하고 **자격 증명 만들기 > Oauth 클라이언트 ID**를 선택합니다.
4. **동의 화면 구성**을 선택합니다.
5. 선택하거나 유효한 **이메일 주소**를 제공는 **제품 이름** 입력 사용자에 게 표시 `b2clogin.com` 에서 **도메인 권한이**를 클릭 하 고  **저장**합니다.
6. **애플리케이션 형식**에서 **웹 애플리케이션**을 선택합니다.
7. 애플리케이션의 **이름**을 입력합니다.
8. **권한이 부여된 JavaScript 원본**에 `https://your-tenant-name.b2clogin.com`을 입력하고 **권한이 부여된 리디렉션 URI**에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. your-tenant-name을 테넌트 이름으로 바꿉니다. Azure AD B2C에서 테넌트가 대문자로 정의되어 있더라도 테넌트 이름을 입력할 때는 소문자만 사용해야 합니다.
8. **만들기**를 클릭합니다.
9. **클라이언트 ID** 및 **클라이언트 비밀** 값을 복사합니다. 테넌트에서 Google을 ID 공급자로 구성하려면 둘 모두가 필요합니다. 클라이언트 암호는 중요한 보안 자격 증명입니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테넌트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키**, **추가**를 차례로 선택합니다.
6. **옵션**으로는 `Manual`을 선택합니다.
7. 정책 키의 **이름**을 입력합니다. `GoogleSecret`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록해 두었던 클라이언트 비밀을 **비밀**에 입력합니다.
9. **키 사용**에서 `Signature`를 선택합니다.
10. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Google 계정을 사용하여 로그인하게 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Google 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>google.com</Domain>
      <DisplayName>Google</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Google-OAUTH">
          <DisplayName>Google</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">google</Item>
            <Item Key="authorization_endpoint">https://accounts.google.com/o/oauth2/auth</Item>
            <Item Key="AccessTokenEndpoint">https://accounts.google.com/o/oauth2/token</Item>
            <Item Key="ClaimsEndpoint">https://www.googleapis.com/oauth2/v1/userinfo</Item>
            <Item Key="scope">email profile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Google application ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_GoogleSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="google.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. **client_id**를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
5. 파일을 저장합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자도 포함되도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInGoogle`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록/로그인 화면의 ID 공급자 단추와 비슷합니다. Google 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `GoogleExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Google 계정과 통신하여 토큰을 수신할 수 있게 됩니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. **Targetclaimsexchangeid**에 사용한 ID에 동일한 값을 사용 하도록 하는 다음 **claim이상 변경** 요소를 추가 합니다.

    ```XML
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAuth" />
    ```

    **TechnicalProfileReferenceId**의 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다. `Google-OAuth`)을 입력합니다.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어, 파일 이름을 *SignUpSignInGoogle.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInGoogle`)을 입력합니다.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_google`으로 업데이트할 수 있습니다.
4. 새로 만든 사용자 경험의 ID(SignUpSignInADFS)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
6. **애플리케이션 선택** 필드에서 직접 만든 Azure AD B2C 애플리케이션이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.
