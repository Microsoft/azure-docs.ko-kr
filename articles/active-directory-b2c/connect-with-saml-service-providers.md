---
title: Azure AD B2C를 애플리케이션의 SAML IdP로 구성
title-suffix: Azure AD B2C
description: 애플리케이션(서비스 공급자)에 SAML 프로토콜 어설션을 제공하도록 Azure AD B2C를 구성하는 방법입니다. Azure AD B2C는 SAML 애플리케이션에 단일 IdP(ID 공급자)로 사용됩니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 2bf767bd87e0df791b0efff1294f15353234ba2c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520212"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에 SAML 애플리케이션 등록

이 문서에서는 애플리케이션에 SAML(Security Assertion Markup Language) IdP(ID 공급자)로 사용되도록 Azure AD B2C(Azure Active Directory B2C)를 구성하는 방법을 알아봅니다.

## <a name="scenario-overview"></a>시나리오 개요

Azure AD B2C를 고객 ID 및 액세스 관리 솔루션으로 사용하는 조직에서는 SAML 프로토콜을 사용하여 인증하도록 구성된 ID 공급자 또는 애플리케이션과 상호 작용해야 할 수 있습니다.

Azure AD B2C는 다음 두 가지 방법 중 하나로 SAML 상호 운용성을 달성합니다.

* IdP(‘id 공급자’)로 사용하고 SAML 기반 서비스 공급자(애플리케이션)를 통해 SSO(Single Sign-On)를 수행하는 방법
* SP(‘서비스 공급자’)로 사용하고 Salesforce 및 ADFS와 같은 SAML 기반 ID 공급자와 상호 작용하는 방법

![왼쪽에 ID 공급자인 B2C가 있고 오른쪽에 서비스 공급자인 B2C가 있는 다이어그램](media/saml-identity-provider/saml-idp-diagram-01.jpg)

SAML을 사용하는 두 가지 비독점 핵심 시나리오 요약:

| 시나리오 | Azure AD B2C 역할 | 방법 |
| -------- | ----------------- | ------- |
| 애플리케이션은 인증을 완료하는 데 SAML 어설션이 필요합니다. | **Azure AD B2C가 IdP(ID 공급자)로 사용됨**<br />Azure AD B2C가 애플리케이션의 SAML IdP로 사용됩니다. | 이 문서의 내용: |
| 사용자는 ADFS, Salesforce 또는 Shibboleth와 같은 SAML 규격 ID 공급자를 사용하는 Single Sign-On이 필요합니다.  | **Azure AD B2C가 SP(서비스 공급자)로 사용됨**<br />Azure AD B2C가 SAML ID 공급자에 연결할 때 서비스 공급자로 사용됩니다. 애플리케이션과 SAML ID 공급자 간 페더레이션 프록시입니다.  | <ul><li>[사용자 지정 정책을 통해 ADFS를 SAML IdP로 사용하여 로그인 설정](identity-provider-adfs2016-custom.md)</li><li>[사용자 지정 정책을 사용하여 Salesforce SAML 공급자로 로그인 설정](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>사전 요구 사항

* [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료합니다. 문서에서 설명하는 사용자 지정 정책 시작 팩의 *SocialAndLocalAccounts* 사용자 지정 정책이 필요합니다.
* SAML(Security Assertion Markup Language) 프로토콜에 관한 기본적인 이해.
* SAML SP(서비스 공급자)로 구성된 웹 애플리케이션. 이 자습서에서는 Microsoft에서 제공하는 [SAML 테스트 애플리케이션][samltest]을 사용할 수 있습니다.

## <a name="components-of-the-solution"></a>솔루션의 구성 요소

이 시나리오에는 다음과 같은 세 가지 주요 구성 요소가 필요합니다.

* SAML 요청을 전송하고 Azure AD B2C에서 SAML 어설션을 수신, 디코드 및 응답하는 기능이 있는 SAML **서비스 공급자**. 이를 신뢰 당사자라고도 합니다.
* 서비스 공급자에 공개적으로 사용할 수 있는 SAML **메타데이터 엔드포인트**.
* [Azure AD B2C 테넌트](tutorial-create-tenant.md)

SAML 서비스 공급자와 연결된 메타데이터 엔드포인트가 아직 없는 경우 Microsoft에서 테스트용으로 제공한 이 샘플 SAML 애플리케이션을 사용할 수 있습니다.

[SAML 테스트 애플리케이션][samltest]

## <a name="1-set-up-certificates"></a>1. 인증서 설정

서비스 공급자와 Azure AD B2C 간 신뢰 관계를 빌드하려면 웹앱 X509 인증서를 제공해야 합니다.

* **서비스 공급자 인증서**
  * 프라이빗 키가 웹앱에 저장된 인증서입니다. 이 인증서는 서비스 공급자가 Azure AD B2C에 전송되는 SAML 요청에 서명하는 데 사용됩니다. Azure AD B2C는 서비스 공급자 메타데이터에서 퍼블릭 키를 읽어서 서명의 유효성을 검사합니다.
  * (선택 사항) 프라이빗 키가 웹앱에 저장된 인증서입니다. Azure AD B2C는 서비스 공급자 메타데이터에서 퍼블릭 키를 읽어서 SAML 어설션을 암호화합니다. 그러면 서비스 공급자는 프라이빗 키를 사용하여 어설션을 암호 해독합니다.
* **Azure AD B2C 인증서**
  * 프라이빗 키가 Azure AD B2C에 있는 인증서입니다. 이 인증서는 Azure AD B2C가 서비스 공급자에게 전송되는 SAML 응답에 서명하는 데 사용됩니다. 서비스 공급자는 Azure AD B2C 메타데이터 퍼블릭 키를 읽어서 SAML 응답 서명의 유효성을 검사합니다.

퍼블릭 인증 기관에서 발급된 인증서를 사용하거나 이 자습서의 경우 자체 서명된 인증서를 사용할 수 있습니다.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 자체 서명된 인증서 준비

인증서가 아직 없는 경우 이 자습서에서는 자체 서명된 인증서를 사용할 수 있습니다. Windows에서는 PowerShell의 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성할 수 있습니다.

1. 이 PowerShell 명령을 실행하여 자체 서명된 인증서를 생성합니다. `-Subject` 인수를 애플리케이션 및 Azure AD B2C 테넌트 이름에 적절하게 수정합니다. `-NotAfter` 날짜를 조정하여 인증서에 다른 만료 날짜를 지정할 수도 있습니다.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. **사용자 인증서 관리** > **현재 사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com*을 엽니다.
1. 인증서 > **작업** > **모든 작업** > **내보내기**를 선택합니다.
1. **예** > **다음** > **예, 프라이빗 키를 내보냅니다.**  > **다음**을 선택합니다.
1. **내보내기 파일 형식**의 기본값을 적용합니다.
1. 인증서의 암호를 제공합니다.

### <a name="12-upload-the-certificate"></a>1.2 인증서 업로드

다음으로, SAML 어설션 및 응답 서명 인증서를 Azure AD B2C에 업로드합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure AD B2C 테넌트로 이동합니다.
1. **정책**에서 **Identity Experience Framework**, **정책 키**를 차례로 선택합니다.
1. **추가**를 선택한 다음, **옵션** > **업로드**를 선택합니다.
1. **이름**을 입력합니다(예: *SamlIdpCert*). 키의 이름에 *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. 업로드 파일 제어를 사용하여 인증서를 업로드합니다.
1. 인증서의 암호를 입력합니다.
1. **만들기**를 선택합니다.
1. 키가 예상대로 표시되는지 확인합니다. 예: *B2C_1A_SamlIdpCert*.

## <a name="2-prepare-your-policy"></a>2. 정책 준비

### <a name="21-create-the-saml-token-issuer"></a>2.1 SAML 토큰 발급자 만들기

이제 [SAML 토큰 발급자](saml-issuer-technical-profile.md) 및 [SAML 세션 공급자](custom-policy-reference-sso.md#samlssosessionprovider) 기술 프로필을 사용하여 테넌트가 SAML 토큰을 발급하는 기능을 추가합니다.

사용자 지정 정책 시작 팩에서 `SocialAndLocalAccounts\`**`TrustFrameworkExtensions.xml`** 을 엽니다.

`<ClaimsProviders>` 섹션을 찾고 다음 XML 코드 조각을 추가합니다.

`IssuerUri` 메타데이터의 값을 변경할 수 있습니다. 이것은 Azure AD B2C에서 SAML 응답으로 반환되는 발급자 URI입니다. SAML 어설션 유효성 검사 중 발급자 URI를 수락하도록 신뢰 당사자 애플리케이션을 구성해야 합니다.

```xml
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      <Metadata>
        <!-- The issuer contains the policy name; it should be the same name as configured in the relying party application. B2C_1A_signup_signin_SAML is used below. -->
        <!--<Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>-->
      </Metadata>
      <CryptographicKeys>
        <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
      </CryptographicKeys>
      <InputClaims/>
      <OutputClaims/>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. SAML 신뢰 당사자 정책 추가

이제 테넌트가 SAML 어설션을 발급할 수 있으므로 SAML 신뢰 당사자 정책을 만들고 JWT 대신 SAML 어설션을 발급하도록 사용자 경험을 수정해야 합니다.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 가입 또는 로그인 정책 만들기

1. 시작 팩 작업 디렉터리에 *SignUpOrSignin.xml* 파일의 복사본을 만들고 새 이름으로 저장합니다. 예: *SignUpOrSigninSAML.xml*. 이 파일은 신뢰 당사자 정책 파일입니다.

1. 원하는 편집기에서 *SignUpOrSigninSAML.xml* 파일을 엽니다.

1. 아래와 같이 정책의 `PolicyId` 및 `PublicPolicyUri`를 _B2C_1A_signup_signin_saml_ 및 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml`로 변경합니다.

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. `<RelyingParty>` 요소 바로 앞에 다음 XML 코드 조각을 추가합니다. 이 XML은 _SignUpOrSignIn_ 사용자 경험의 오케스트레이션 단계 번호 7을 덮어씁니다. 시작 팩의 다른 폴더에서 시작했거나 오케스트레이션 단계를 추가 또는 제거하여 사용자 경험을 사용자 지정한 경우 `order` 요소의 번호가 토큰 발급자 단계의 사용자 경험에 지정된 번호와 일치하는지 확인합니다. 예를 들어 다른 시작 팩 폴더에서 해당 단계 번호는 `LocalAccounts`의 경우 4, `SocialAccounts`의 경우 6, `SocialAndLocalAccountsWithMfa`의 9입니다.

    ```xml
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. `<RelyingParty>` 요소의 전체 `<TechnicalProfile>` 요소를 다음 기술 프로필 XML로 바꿉니다.

    ```xml
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
    ```

1. `tenant-name`을 Azure AD B2C 테넌트의 이름으로 업데이트합니다.

최종 신뢰 당사자 정책 파일은 다음과 같이 표시됩니다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">

  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <UserJourneys>
    <UserJourney Id="SignUpOrSignIn">
      <OrchestrationSteps>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
      </OrchestrationSteps>
    </UserJourney>
  </UserJourneys>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="objectId"/>
      </OutputClaims>
      <SubjectNamingInfo ClaimType="objectId" ExcludeAsClaim="true"/>
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 정책 메타데이터 업로드 및 테스트

변경 내용을 저장하고 새 정책 파일을 업로드합니다. 두 정책(확장 및 신뢰 당사자 파일)을 모두 업로드한 후에 웹 브라우저를 열고 정책 메타데이터로 이동합니다.

Azure AD B2C 정책 IDP 메타데이터는 SAML 프로토콜에서 SAML ID 공급자의 구성을 공개하는 데 사용되는 정보입니다. 메타데이터는 로그인/로그아웃, 인증서, 로그인 방법 등과 같은 서비스의 위치를 정의합니다. Azure AD B2C 정책 메타데이터는 다음 URL에서 사용할 수 있습니다. `tenant-name`을 Azure AD B2C 테넌트의 이름으로 바꾸고, `policy-name`을 정책의 이름(ID)으로 바꿉니다.

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

이제 사용자 지정 정책 및 Azure AD B2C 테넌트가 준비되었습니다. 다음으로, Azure AD B2C에서 애플리케이션 등록을 만듭니다.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Azure AD B2C 디렉터리에서 애플리케이션 설정

### <a name="41-register-your-application-in-azure-ad-b2c"></a>4.1 Azure AD B2C에 응용 프로그램 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록**을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예: *SAMLApp1*.
1. **지원되는 계정 유형**에서 **이 조직 디렉터리의 계정만**을 선택합니다.
1. **리디렉션 URI**에서 **웹**을 선택한 다음, `https://localhost`를 입력합니다. 이 값은 나중에 애플리케이션 등록 매니페스트에서 수정합니다.
1. **등록**을 선택합니다.

### <a name="42-update-the-app-manifest"></a>4.2 앱 매니페스트 업데이트

SAML 앱의 경우 애플리케이션 등록 매니페스트에서 구성해야 하는 여러 가지 속성이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서, 이전 섹션에서 만든 애플리케이션 등록으로 이동합니다.
1. **관리**에서 **매니페스트**를 선택하여 매니페스트 편집기를 엽니다. 다음 섹션에서 몇 가지 속성을 수정합니다.

#### <a name="identifieruris"></a>identifierUris

`identifierUris`는 Azure AD B2C 테넌트 내에서 웹앱을 고유하게 식별하는 사용자 정의 URI를 포함하는 문자열 컬렉션입니다. 서비스 공급자는 SAML 요청의 `Issuer` 요소에서 이 값을 설정해야 합니다.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

이 속성은 서비스 공급자의 공개적으로 사용할 수 있는 메타데이터 URL을 나타냅니다. 메타데이터 URL은 Blob Storage와 같이 익명으로 액세스할 수 있는 엔드포인트에 업로드된 메타데이터 파일을 가리킬 수 있습니다.

메타데이터는 서비스 공급자와 같은 SAML 당사자의 구성을 고액하기 위해 SAML 프로토콜에서 사용되는 정보입니다. 메타데이터는 로그인/로그아웃, 인증서, 로그인 방법 등 같은 서비스의 위치를 정의합니다. Azure AD B2C는 서비스 공급자 메타데이터를 읽고 이에 따라 동작합니다. 메타데이터가 필요하지 않습니다. 앱 매니페스트에서 직접 회신 URI 및 로그아웃 URI 같은 일부 특성을 지정할 수도 있습니다.

SAML 메타데이터 URL 및 애플리케이션 등록 매니페스트에 *둘 다* 지정된 속성은 **병합**됩니다. 메타데이터 URL에 지정된 속성이 먼저 처리되고 우선 적용됩니다.

SAML 테스트 애플리케이션을 사용하는 이 자습서에서는 `samlMetadataUrl`에 대해 다음 값을 사용합니다.

```json
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType(선택 사항)

메타데이터 URI를 제공하지 않는 경우 회신 URL을 명시적으로 지정할 수 있습니다. 이 선택적 속성은 `AssertionConsumerServiceUrl`(서비스 공급자 메타데이터의 `SingleSignOnService` URL)을 나타내며 `BindingType`은 `HTTP POST`로 간주됩니다.

서비스 공급자 메타데이터를 사용하지 않고 애플리케이션 매니페스트에서 회신 URL 및 로그아웃 URL을 구성하도록 선택하면 Azure AD B2C는 SAML 요청 서명의 유효성을 검사하지 않고 SAML 응답도 암호화하지 않습니다.

SAML 테스트 애플리케이션을 사용하는 이 자습서에서는 `replyUrlsWithType`의 `url` 속성을 다음 JSON 코드 조각에 표시된 값으로 설정합니다.

```json
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>logoutUrl(선택 사항)

이 선택적 속성은 `Logout` URL(신뢰 당사자 메타데이터의 `SingleLogoutService` URL)을 나타내며 이 속성의 `BindingType`은 `Http-Redirect`로 간주됩니다.

SAML 테스트 애플리케이션을 사용하는 이 자습서에서는 `logoutUrl`을 `https://samltestapp2.azurewebsites.net/logout`으로 설정된 대로 유지합니다.

```json
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 애플리케이션 코드 업데이트

마지막 단계에서는 SAML 신뢰 당사자 애플리케이션에서 Azure AD B2C를 SAML IdP로 사용하도록 설정합니다. 각 애플리케이션은 서로 다르며 수행하는 단계가 매우 달라집니다. 자세한 내용은 앱 설명서를 참조하세요.

일반적으로 다음 중 일부 또는 전부가 필요합니다.

* **메타데이터**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **발급자**:   메타데이터 파일에서 entityID 사용
* **로그인 URL/SAML 엔드포인트/SAML URL**: 메타데이터 파일에서 값 확인
* **인증서**: *B2C_1A_SamlIdpCert*이며, 프라이빗 키가 없습니다. 인증서의 퍼블릭 키를 가져오려면:

    1. 위에서 지정한 메타데이터 URL로 이동합니다.
    1. `<X509Certificate>` 요소에서 값을 복사합니다.
    1. 텍스트 파일에 붙여넣습니다.
    1. 텍스트 파일을 *.cer* 파일로 저장합니다.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 SAML 테스트 앱을 사용하여 테스트(선택 사항)

[SAML 테스트 애플리케이션][samltest]을 사용하여 이 자습서를 완료하려면:

* 테넌트 이름 업데이트
* 정책 이름 업데이트(예: *B2C_1A_signup_signin_saml*)
* 이 발급자 URI 지정: `https://contoso.onmicrosoft.com/app-name`

**로그인**을 선택하면 사용자 로그인 화면이 표시됩니다. 로그인할 때 SAML 어설션이 다시 애플리케이션 예제에 발급됩니다.

## <a name="enable-encypted-assertions"></a>(어설션 사용
서비스 공급자에 게 다시 전송 된 SAML 어설션을 암호화 하기 위해 Azure AD B2C는 서비스 공급자 공개 키 인증서를 사용 합니다. ' Encryption '을 사용 하는 KeyDescriptor로 위의 ["samlMetadataUrl"](#samlmetadataurl) 에 설명 된 SAML 메타 데이터에 공개 키가 있어야 합니다.

다음은 암호화로 설정 된 사용을 포함 하는 SAML metadata KeyDescriptor의 예입니다.

```xml
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

Azure AD B2C에서 암호화 된 어설션을 보내도록 설정 하려면 아래와 같이 신뢰 당사자 기술 프로필에서 **WantsEncryptedAssertion** 메타 데이터 항목을 true로 설정 합니다.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="http://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="contoso.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin_saml"
  PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_saml">
 ..
 ..
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="SAML2"/>
      <Metadata>
          <Item Key="WantsEncryptedAssertions">true</Item>
      </Metadata>
     ..
     ..
     ..
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="sample-policy"></a>샘플 정책

Microsoft에서는 SAML 테스트 앱을 사용하여 테스트하는 데 사용할 수 있는 전체 샘플 정책을 제공합니다.

1. [SAML-SP 시작 로그인 샘플 정책](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) 다운로드
1. 테넌트 이름과 일치하도록 `TenantId` 업데이트(예: *contoso.b2clogin.com*)
1. *B2C_1A_SAML2_signup_signin*의 정책 이름 유지

## <a name="supported-and-unsupported-saml-modalities"></a>지원되는 SAML 형식 및 지원되지 않는 SAML 형식

다음 SAML RP(신뢰 당사자) 시나리오는 고유한 메타데이터 엔드포인트를 통해 지원됩니다.

* 애플리케이션/서비스 주체 개체에서 여러 로그아웃 URL 또는 로그아웃 URL의 POST 바인딩.
* 애플리케이션/서비스 주체 개체에서 RP 요청을 확인하기 위한 서명 키를 지정합니다.
* 애플리케이션/서비스 주체 개체에서 토큰 암호화 키를 지정합니다.
* Id 공급자가 Azure AD B2C 되는 id 공급자 시작 로그인입니다.

다음 SAML 신뢰 당사자 (RP) 시나리오는 현재 지원 되지 않습니다.
* Id 공급자가 시작한 로그온입니다. 여기서 Id 공급자는 외부 Id 공급자 (예: ADFS)입니다.

## <a name="next-steps"></a>다음 단계

- [OASIS 웹 사이트에서 SAML 프로토콜](https://www.oasis-open.org/)에 관해 자세히 알아볼 수 있습니다.
- [Azure AD B2C GitHub 커뮤니티 리포지토리](https://github.com/azure-ad-b2c/saml-sp-tester)에서 SAML 테스트 웹앱을 가져옵니다.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
