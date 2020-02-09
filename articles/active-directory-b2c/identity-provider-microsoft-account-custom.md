---
title: 사용자 지정 정책을 사용 하 여 Microsoft 계정 계정으로 로그인 설정
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용 하 여 OIDC (Openid connect Connect) 프로토콜을 사용 하는 id 공급자로 MSA (Microsoft 계정)를 사용 하도록 설정 하는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b1489ce6bee2ce25ffb268ef20cc8fa587664619
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848929"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Microsoft 계정을 통한 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 Microsoft 계정에서 사용자에 대한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.
- Microsoft 계정이 아직 없으면 [https://www.live.com/](https://www.live.com/)에서 만듭니다.

## <a name="add-an-application"></a>애플리케이션 추가

Microsoft 계정 사용자에 대한 로그인을 사용 하도록 설정 하려면 Azure AD 테넌트 내에서 응용 프로그램을 등록 해야 합니다. Azure AD 테넌트는 Azure AD B2C 테넌트와 다릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 azure ad 테넌트가 포함 된 디렉터리를 선택 하 여 azure ad 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들어 *MSAapp1* 등을 입력합니다.
1. **지원 되는 계정 유형**에서 **조직 디렉터리와 개인 Microsoft 계정 (예: Skype, Xbox, Outlook.com)의 계정을**선택 합니다.
1. **URI 리디렉션 (선택 사항)** 에서 **웹** 을 선택 하 고 텍스트 상자에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`을 입력 합니다. `your-tenant-name`를 Azure AD B2C 테넌트 이름으로 바꿉니다.
1. **등록** 선택
1. 응용 프로그램 개요 페이지에 표시 된 **응용 프로그램 (클라이언트) ID** 를 기록 합니다. 이후 섹션에서 클레임 공급자를 구성할 때 필요 합니다.
1. **인증서 & 암호** 선택
1. **새 클라이언트 암호** 를 클릭 합니다.
1. 암호에 대한 **설명** (예: *MSA 응용 프로그램 클라이언트 암호*)을 입력 한 다음 **추가**를 클릭 합니다.
1. **값** 열에 표시 된 응용 프로그램 암호를 기록 합니다. 다음 섹션에서이 값을 사용 합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

이제 Azure AD 테넌트에서 응용 프로그램을 만들었으므로 Azure AD B2C 테넌트에 해당 응용 프로그램의 클라이언트 암호를 저장 해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테넌트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**, **추가**를 차례로 선택합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `MSASecret`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. **암호**에 이전 섹션에서 기록한 클라이언트 암호를 입력 합니다.
1. **키 사용**에서 `Signature`를 선택합니다.
1. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Microsoft 계정를 사용 하 여 로그인 할 수 있도록 하려면 엔드포인트을 통해 통신할 수 있는 Azure AD B2C 클레임 공급자로 계정을 정의 해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *Trustframeworkextensions.xml* 정책 파일을 엽니다.
1. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
1. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="live.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="email" />
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

1. **Client_id** 의 값을 이전에 기록한 Azure AD 응용 프로그램의 *응용 프로그램 (클라이언트) id* 로 바꿉니다.
1. 파일을 저장합니다.

이제 Azure AD B2C에서 Azure AD의 Microsoft 계정 응용 프로그램과 통신 하는 방법을 알 수 있도록 정책을 구성 했습니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

계속 하기 전에 수정 된 정책을 업로드 하 여 지금까지 문제가 발생 하지 않았는지 확인 합니다.

1. Azure Portal에서 Azure AD B2C 테넌트로 이동 하 여 **Id 경험 프레임 워크**를 선택 합니다.
1. **사용자 지정 정책** 페이지에서 **사용자 지정 정책 업로드**를 선택 합니다.
1. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
1. **업로드**를 클릭합니다.

포털에 오류가 표시 되지 않으면 다음 섹션을 계속 진행 합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 id 공급자를 설정 했지만 등록 또는 로그인 화면에서 아직 사용할 수 없는 상태입니다. 사용할 수 있도록 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Microsoft 계정 id 공급자도 갖도록 수정 합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInMSA`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. Microsoft 계정에 대한 **ClaimsProviderSelection** 요소를 추가 하면 사용자가 페이지를 이동할 때 새 단추가 표시 됩니다.

1. 앞에서 만든 사용자 경험의 `Order="1"`이 포함된 **OrchestrationStep** 요소를 *TrustFrameworkExtensions.xml* 파일에서 찾습니다.
1. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `MicrosoftAccountExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우 작업은 Azure AD B2C Microsoft 계정와 통신 하 여 토큰을 수신 하는 데 사용 됩니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
1. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    이전에 추가한 클레임 공급자의 **TechnicalProfile** 요소에 있는 `Id` 값의 값과 일치 하도록 **TechnicalProfileReferenceId** 의 값을 업데이트 합니다. `MSA-OIDC`)을 입력합니다.

1. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInMSA.xml*로 바꿉니다.
1. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInMSA`)을 입력합니다.
1. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_msa`으로 업데이트할 수 있습니다.
1. **Defaultuserjourney** 에서 **ReferenceId** 특성의 값을 이전에 만든 사용자 경험의 ID (SignUpSignInMSA)와 일치 하도록 업데이트 합니다.
1. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
1. **응용 프로그램 선택** 필드에 이전 섹션에서 만든 Azure AD B2C 응용 프로그램 (또는 *webapp1* 또는 *testapp1-development*와 같은 필수 구성 요소를 완료)이 선택 되어 있는지 확인 한 다음 지금 실행을 클릭 하 여 테스트 합니다.
1. **Microsoft 계정** 단추를 선택 하 고 로그인 합니다.

    로그인 작업이 성공 하면 다음과 같이 디코딩된 토큰을 표시 하는 `jwt.ms`으로 리디렉션됩니다.

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
