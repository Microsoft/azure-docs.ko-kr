---
title: 사용자 지정 정책을 사용 하 여 Azure AD 계정을 사용 하 여 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Azure Active Directory 계정으로 로그인하도록 설정합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 64934dd5bc591415c0bad6ac3dc6a4a2d98dd005
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136301"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Azure Active Directory 계정으로 로그인하도록 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 Azure Active Directory (Azure AD) 조직에서 사용자에 대 한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

[Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.

## <a name="register-an-application"></a>애플리케이션 등록

특정 Azure AD 조직의 사용자에 대한 로그인을 사용하도록 설정하려면 조직의 Azure AD 테넌트 내에 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 조직 Azure AD 테 넌 트를 포함 하는 디렉터리 (예: contoso.com)를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독 필터** 를 선택 하 고 Azure AD 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택한 다음, **앱 등록**을 검색하여 선택합니다.
1. **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. `Azure AD B2C App`)을 입력합니다.
1. 이 응용 프로그램에 대해서 **만이 조직 디렉터리에서** 기본 선택 된 계정을 적용 합니다.
1. **리디렉션 URI**의 경우 **웹**의 값을 그대로 사용 하 고 다음 URL을 소문자로 입력 합니다. 여기서 `your-B2C-tenant-name`은 Azure AD B2C 테 넌 트의 이름으로 바뀝니다.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`)을 입력합니다.

1. **등록**을 선택합니다. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID**를 기록합니다.
1. **인증서 & 암호**를 선택 하 고 **새 클라이언트 암호**를 선택 합니다.
1. 비밀에 대 한 **설명을** 입력 하 고 만료를 선택한 다음 **추가**를 선택 합니다. 이후 단계에서 사용할 암호의 **값** 을 기록 합니다.

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

Azure AD에서 `family_name` 및 `given_name` 클레임을 가져오려는 경우 Azure Portal UI 또는 응용 프로그램 매니페스트에서 응용 프로그램에 대 한 선택적 클레임을 구성할 수 있습니다. 자세한 내용은 [AZURE AD 앱에 선택적 클레임을 제공 하는 방법](../active-directory/develop/active-directory-optional-claims.md)을 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록**을 선택 합니다.
1. 목록에서 선택적 클레임을 구성 하려는 응용 프로그램을 선택 합니다.
1. **관리** 섹션에서 **토큰 구성 (미리 보기)** 을 선택 합니다.
1. **선택적 클레임 추가**를 선택 합니다.
1. 구성 하려는 토큰 유형을 선택 합니다.
1. 추가할 선택적 클레임을 선택 합니다.
1. **추가**를 클릭합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 애플리케이션 키를 Azure AD B2C 테넌트에 저장해야 합니다.

1. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독 필터** 를 선택 하 고 Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **정책**에서 **Id 경험 프레임 워크**를 선택 합니다.
1. **정책 키** 를 선택 하 고 **추가**를 선택 합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `ContosoAppSecret`)을 입력합니다.  접두사 `B2C_1A_` 생성 될 때 키 이름에 자동으로 추가 되므로 다음 섹션의 XML 참조는 *B2C_1A_ContosoAppSecret*하는 것입니다.
1. **비밀**에서 이전에 기록한 클라이언트 암호를 입력 합니다.
1. **키 사용**에서 `Signature`를 선택합니다.
1. **만들기**를 선택합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인하게 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 Azure AD를 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에 있는 **ClaimsProvider** 요소에 Azure AD를 추가하여 Azure AD를 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml* 파일을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="OIDC-Contoso">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **ClaimsProvider** 요소 아래의 **Domain** 값을 다른 ID 공급자와 구분하는 데 사용할 수 있는 고유한 값으로 업데이트합니다. 예: `Contoso`. 이 도메인 설정의 끝에 `.com`을 붙이지 않습니다.
5. **ClaimsProvider** 요소 아래에서 **DisplayName** 값을 클레임 공급자에게 친숙한 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Azure AD 엔드포인트에서 토큰을 가져오려면 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. 이 작업은 **ClaimsProvider**의 **TechnicalProfile** 요소 내에서 수행됩니다.

1. **TechnicalProfile** 요소의 ID를 업데이트합니다. 이 ID는 정책의 다른 부분에서이 기술 프로필을 참조 하는 데 사용 됩니다 (예: `OIDC-Contoso`).
1. **DisplayName**의 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. **설명**값을 업데이트합니다.
1. Azure AD는 OpenID Connect 프로토콜을 사용하므로 **Protocol** 값이 `OpenIdConnect`인지 확인합니다.
1. **METADATA** 값을 `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`으로 설정합니다. 여기서 `tenant-name`는 Azure AD 테넌트 이름입니다. 예를 들어 `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. **client_id**를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.
1. **CryptographicKeys**에서 **StorageReferenceId** 의 값을 앞에서 만든 정책 키의 이름으로 업데이트 합니다. `B2C_1A_ContosoAppSecret`)을 입력합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
1. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
1. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이 시점에서 id 공급자가 설정 되었지만 등록/로그인 페이지에는 아직 사용할 수 없습니다. 사용할 수 있도록 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD id 공급자도 포함 하도록 수정 합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
1. **이 포함된** UserJourney`Id="SignUpOrSignIn"` 요소를 찾아서 전체 콘텐츠를 복사합니다.
1. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
1. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInContoso`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록/로그인 페이지에서 id 공급자 단추와 유사 합니다. Azure AD에 대해 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 방문할 때 새 단추가 표시됩니다.

1. *Trustframeworkextensions.xml*에서 만든 사용자 경험에 `Order="1"`를 포함 하는 **OrchestrationStep** 요소를 찾습니다.
1. **ClaimsProviderSelections** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `ContosoExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Azure AD와 통신하여 토큰을 수신할 수 있게 됩니다. Azure AD 클레임 공급자의 기술 프로필을 연결하여 동작에 단추를 연결합니다.

1. 사용자 경험에서 **가 포함된** OrchestrationStep`Order="2"`을 찾습니다.
1. 다음 **ClaimsExchange** 요소를 추가합니다. **Id**에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="OIDC-Contoso" />
    ```

    **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 **ID**로 업데이트합니다. `OIDC-Contoso`)을 입력합니다.

1. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테 넌 트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어, 파일 이름을 *SignUpSignInContoso.xml*로 바꿉니다.
1. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInContoso`)을 입력합니다.
1. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. `http://contoso.com/B2C_1A_signup_signin_contoso`)을 입력합니다.
1. **Defaultuserjourney** 에서 **ReferenceId** 특성의 값을 이전에 만든 사용자 경험의 ID와 일치 하도록 업데이트 합니다. 예를 들면 *SignUpSignInContoso*입니다.
1. 변경 내용을 저장하고 파일을 업로드합니다.
1. **사용자 지정 정책**아래의 목록에서 새 정책을 선택 합니다.
1. **응용 프로그램 선택** 드롭다운에서 이전에 만든 Azure AD B2C 응용 프로그램을 선택 합니다. 예를 들면 *testapp1-development*입니다.
1. **지금 실행 끝점** 을 복사 하 여 개인 브라우저 창에서 엽니다 (예: Google Chrome의 Incognito 모드 또는 Microsoft Edge의 InPrivate 창). 개인 브라우저 창에서 열면 현재 캐시 된 Azure AD 자격 증명을 사용 하지 않고 전체 사용자 경험을 테스트할 수 있습니다.
1. Azure AD 로그인 단추 (예: *Contoso Employee*)를 선택 하 고 azure ad 조직 테 넌 트의 사용자에 대 한 자격 증명을 입력 합니다. 응용 프로그램에 권한을 부여 하 라는 메시지가 표시 되 면 프로필에 대 한 정보를 입력 합니다.

로그인 프로세스가 정상적으로 완료 되 면 브라우저가 `https://jwt.ms`으로 리디렉션되고 Azure AD B2C에서 반환 된 토큰의 내용을 표시 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 정책을 사용 하는 경우 개발 하는 동안 정책의 문제를 해결할 때 추가 정보가 필요할 수 있습니다.

문제를 진단 하는 데 도움이 되도록 정책을 일시적으로 "개발자 모드"에 배치 하 고 Azure 애플리케이션 Insights로 로그를 수집할 수 있습니다. [Azure Active Directory B2C: 로그 수집](troubleshoot-with-application-insights.md)방법을 알아보세요.
