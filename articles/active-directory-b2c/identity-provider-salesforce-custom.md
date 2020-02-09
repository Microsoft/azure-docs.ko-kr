---
title: 사용자 지정 정책을 사용 하 여 Salesforce SAML 공급자로 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Salesforce SAML 공급자로 로그인 설정
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0a745f83dcceef25634032cbe6fdb971f4f533ce
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847421"
---
# <a name="set-up-sign-in-with-a-salesforce-saml-provider-by-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 Salesforce SAML 공급자로 로그인 설정

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 Salesforce 조직의 사용자에 대한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다. 사용자 지정 정책에 [SAML 기술 프로필](saml-technical-profile.md)을 추가하여 로그인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.
- [Developer Edition 평가판 계정](https://developer.salesforce.com/signup)을 아직 등록하지 않았으면 등록합니다. 이 문서에서는 [Salesforce Lightning 환경](https://developer.salesforce.com/page/Lightning_Experience_FAQ)을 사용합니다.
- Salesforce 조직에 [내 도메인을 설정](https://help.salesforce.com/articleView?id=domain_name_setup.htm&language=en_US&type=0)합니다.

### <a name="set-up-salesforce-as-an-identity-provider"></a>Salesforce를 ID 공급자로 설정

1. [Salesforce에 로그인](https://login.salesforce.com/)합니다.
2. 왼쪽 메뉴의 **설정** 아래에서 **ID**를 확장하고 **ID 공급자**를 선택합니다.
3. **ID 공급자 사용**을 선택합니다.
4. **인증서 선택** 아래에서 Salesforce가 Azure AD B2C와 통신할 때 사용할 인증서를 선택합니다. 기본 인증서를 사용할 수 있습니다.
5. **저장**을 클릭합니다.

### <a name="create-a-connected-app-in-salesforce"></a>Salesforce에서 연결된 앱 만들기

1. **Id 공급자** 페이지에서 **서비스 공급자가 이제 연결 된 앱을 통해 생성 됨을 선택 합니다. 여기를 클릭 하세요.**
2. **기본 정보** 아래에서 연결된 앱에 대한 필수 값을 입력합니다.
3. **웹앱 설정** 아래에서 **SAML 사용** 확인란을 선택합니다.
4. **엔터티 ID** 필드에 다음 URL을 입력합니다. `your-tenant` 값을 Azure AD B2C 테넌트의 이름으로 바꾸어야 합니다.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase
      ```

6. **ACS URL** 필드에 다음 URL을 입력합니다. `your-tenant` 값을 Azure AD B2C 테넌트의 이름으로 바꾸어야 합니다.

      ```
      https://your-tenant.b2clogin.com/your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase/samlp/sso/assertionconsumer
      ```
7. 목록 아래쪽으로 스크롤한 다음 **저장**을 클릭합니다.

### <a name="get-the-metadata-url"></a>메타데이터 URL 가져오기

1. 연결된 앱의 개요 페이지에서 **관리**를 클릭합니다.
2. **메타데이터 검색 엔드포인트** 값을 복사하고 저장합니다. 이 문서의 뒷부분에서 사용합니다.

### <a name="set-up-salesforce-users-to-federate"></a>Salesforce 사용자가 페더레이션하도록 설정

1. 연결된 앱의 **관리** 페이지에서 **프로필 관리**를 클릭합니다.
2. Azure AD B2C와 페더레이션하려는 프로필(또는 사용자 그룹)을 선택합니다. 시스템 관리자 권한으로 Salesforce 계정을 사용하여 페더레이션할 수 있도록 **시스템 관리자** 확인란을 선택합니다.

## <a name="generate-a-signing-certificate"></a>서명 인증서 생성

Azure AD B2C는 Salesforce에 전송되는 요청에 서명해야 합니다. 서명 인증서를 생성하려면 Azure PowerShell을 열고 다음 명령을 실행합니다.

> [!NOTE]
> 맨 위 두 줄에서 테넌트 이름 및 암호를 업데이트해야 합니다.

```powershell
$tenantName = "<YOUR TENANT NAME>.onmicrosoft.com"
$pwdText = "<YOUR PASSWORD HERE>"

$Cert = New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName "SamlIdp.$tenantName" -Subject "B2C SAML Signing Cert" -HashAlgorithm SHA256 -KeySpec Signature -KeyLength 2048

$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText

Export-PfxCertificate -Cert $Cert -FilePath .\B2CSigningCert.pfx -Password $pwd
```

## <a name="create-a-policy-key"></a>정책 키 만들기

만든 ADFS 인증서를 Azure AD B2C 테넌트에 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 선택하고, 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키**, **추가**를 차례로 선택합니다.
6. **옵션**으로는 `Upload`을 선택합니다.
7. 정책에 대한 **이름**을 입력합니다. 예: SAMLSigningCert 키의 이름에 `B2C_1A_` 접두사가 자동으로 추가됩니다.
8. 만든 B2CSigningCert.pfx 인증서로 이동한 후 선택합니다.
9. 인증서의 **암호**를 입력합니다.
3. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 Salesforce 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 Salesforce 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```XML
    <ClaimsProvider>
      <Domain>salesforce</Domain>
      <DisplayName>Salesforce</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="salesforce">
          <DisplayName>Salesforce</DisplayName>
          <Description>Login with your Salesforce account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="WantsSignedAssertions">false</Item>
            <Item Key="PartnerEntity">https://contoso-dev-ed.my.salesforce.com/.well-known/samlidp.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SAMLSigningCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userId"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="username"/>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="SAMLIdp" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **PartnerEntity** 값을 앞에서 복사한 Salesforce 메타데이터 URL로 업데이트합니다.
5. **StorageReferenceId**의 두 인스턴스 값을 서명 인증서의 키 이름으로 업데이트합니다. 예: B2C_1A_SAMLSigningCert.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Salesforce 계정과 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이제 ID 공급자는 설정되었지만 등록 또는 로그인 화면에서 사용할 수는 없는 상태입니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Salesforce ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInSalesforce`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. LinkedIn 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 방금 만든 사용자 경험의 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelects** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `SalesforceExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="SalesforceExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Salesforce 계정과 통신하여 토큰을 수신할 수 있게 됩니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. **Id**에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="SalesforceExchange" TechnicalProfileReferenceId="salesforce" />
    ```

    **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 **ID**로 업데이트합니다. `LinkedIn-OAUTH`)을 입력합니다.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

방금 만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInSalesforce.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInSalesforce`)을 입력합니다.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_salesforce`으로 업데이트할 수 있습니다.
4. 새로 만든 사용자 경험의 ID(SignUpSignInSalesforce)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
6. **애플리케이션 선택** 필드에서 직접 만든 Azure AD B2C 애플리케이션이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.
