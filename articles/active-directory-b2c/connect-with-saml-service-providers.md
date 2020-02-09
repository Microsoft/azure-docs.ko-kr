---
title: Azure AD B2C를 응용 프로그램에 대한 SAML IdP 구성
title-suffix: Azure AD B2C
description: 응용 프로그램 (서비스 공급자)에 SAML 프로토콜 어설션을 제공 하도록 Azure AD B2C를 구성 하는 방법입니다. Azure AD B2C는 SAML 응용 프로그램에 대한 단일 id 공급자 (IdP) 역할을 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 09c704237e3c1fde8a7591d610d1b801dd016c46
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836663"
---
# <a name="register-a-saml-application-in-azure-ad-b2c"></a>Azure AD B2C에서 SAML 응용 프로그램 등록

이 문서에서는 응용 프로그램에 대한 SAML (Security Assertion Markup Language) id 공급자 (IdP) 역할을 수행 하도록 Azure Active Directory B2C (Azure AD B2C)를 구성 하는 방법에 대해 알아봅니다.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="scenario-overview"></a>시나리오 개요

Azure AD B2C를 고객 id 및 액세스 관리 솔루션으로 사용 하는 조직에서는 SAML 프로토콜을 사용 하 여 인증 하도록 구성 된 id 공급자 또는 응용 프로그램과의 상호 작용이 필요할 수 있습니다.

Azure AD B2C은 다음 두 가지 방법 중 하나로 SAML 상호 운용성을 달성 합니다.

* IdP ( *id 공급자* ) 역할을 하 고 SAML 기반 서비스 공급자 (응용 프로그램)를 사용 하 여 sso (single sign-on)를 달성 합니다.
* SP ( *서비스 공급자* ) 역할을 하 고 SALESFORCE 및 ADFS와 같은 SAML 기반 id 공급자와 상호 작용

![왼쪽의 id 공급자로 서 B2C를 사용 하는 다이어그램 및 B2C as a service 공급자](media/saml-identity-provider/saml-idp-diagram-01.jpg)

SAML을 사용 하 여 두 가지 비 배타 코어 시나리오 요약:

| 시나리오 | Azure AD B2C 역할 | 방법 |
| -------- | ----------------- | ------- |
| 응용 프로그램은 인증을 완료 하는 데 SAML 어설션이 필요 합니다. | **Azure AD B2C는 IdP (id 공급자) 역할을 합니다.**<br />Azure AD B2C는 응용 프로그램에 대한 SAML IdP 역할을 합니다. | 이 문서의 내용: |
| 내 사용자는 ADFS, Salesforce 또는 Shibboleth와 같은 SAML 호환 id 공급자를 사용 하는 single sign-on이 필요 합니다.  | **Azure AD B2C SP (서비스 공급자) 역할을 합니다.**<br />Azure AD B2C는 SAML id 공급자에 연결할 때 서비스 공급자 역할을 합니다. 응용 프로그램과 SAML id 공급자 간의 페더레이션 프록시입니다.  | <ul><li>[사용자 지정 정책을 사용 하 여 SAML IdP ADFS로 로그인 설정](identity-provider-adfs2016-custom.md)</li><li>[사용자 지정 정책을 사용 하 여 Salesforce SAML 공급자로 로그인 설정](identity-provider-salesforce-custom.md)</li></ul> |

## <a name="prerequisites"></a>필수 조건

* [Azure AD B2C에서 사용자 지정 정책 시작](custom-policy-get-started.md)의 단계를 완료 합니다. 이 문서에서 설명 하는 사용자 지정 정책 시작 팩의 *Socialandlocalaccounts* 사용자 지정 정책이 필요 합니다.
* SAML (Security Assertion Markup Language) 프로토콜에 대한 기본적인 이해
* SAML SP (서비스 공급자)로 구성 된 웹 응용 프로그램입니다. 이 자습서에서는 사용자가 제공 하는 [SAML 테스트 응용 프로그램][samltest] 을 사용할 수 있습니다.

## <a name="components-of-the-solution"></a>솔루션의 구성 요소

이 시나리오에는 다음과 같은 세 가지 주요 구성 요소가 필요 합니다.

* Saml **서비스 공급자** 는 saml 요청을 전송 하 고 Azure AD B2C에서 saml 어설션을 수신, 디코드 및 대응 하는 기능을 제공 합니다. 이를 신뢰 당사자 라고도 합니다.
* 서비스 공급자에 대해 공개적으로 사용할 수 있는 SAML **메타 데이터 엔드포인트** 입니다.
* [Azure AD B2C 테넌트](tutorial-create-tenant.md)

SAML 서비스 공급자와 연결 된 메타 데이터 엔드포인트이 아직 없는 경우 테스트에 사용할 수 있도록 만든이 샘플 SAML 응용 프로그램을 사용할 수 있습니다.

[SAML 테스트 응용 프로그램][samltest]

## <a name="1-set-up-certificates"></a>1. 인증서 설정

서비스 공급자와 Azure AD B2C 간의 트러스트 관계를 구축 하려면 X509 인증서 및 해당 개인 키를 제공 해야 합니다.

* **서비스 공급자 인증서**
  * 개인 키가 있는 인증서를 웹 앱에 저장 합니다. 이 인증서는 서비스 공급자가 Azure AD B2C에 전송 된 SAML 요청에 서명 하는 데 사용 됩니다. Azure AD B2C는 서명의 유효성을 검사 하기 위해 서비스 공급자 메타 데이터에서 공개 키를 읽습니다.
  * 필드 개인 키가 있는 인증서를 웹 앱에 저장 합니다. Azure AD B2C는 SAML 어설션을 암호화 하기 위해 서비스 공급자 메타 데이터에서 공개 키를 읽습니다. 그러면 서비스 공급자는 개인 키를 사용 하 여 어설션을 해독 합니다.
* **인증서 Azure AD B2C**
  * Azure AD B2C 개인 키가 있는 인증서입니다. 이 인증서는 Azure AD B2C에서 서비스 공급자에 게 전송 된 SAML 응답에 서명 하는 데 사용 됩니다. 서비스 공급자는 Azure AD B2C metadata 공개 키를 읽어 SAML 응답 서명의 유효성을 검사 합니다.

공용 인증 기관에서 발급 한 인증서 또는이 자습서의 경우 자체 서명 된 인증서를 사용할 수 있습니다.

### <a name="11-prepare-a-self-signed-certificate"></a>1.1 자체 서명 된 인증서 준비

인증서가 아직 없는 경우이 자습서에 대해 자체 서명 된 인증서를 사용할 수 있습니다. Windows에서는 PowerShell의 [new-selfsignedcertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용 하 여 인증서를 생성할 수 있습니다.

1. 이 PowerShell 명령을 실행 하 여 자체 서명 된 인증서를 생성 합니다. 응용 프로그램에 적절 한 `-Subject` 인수를 수정 하 고 테넌트 이름을 Azure AD B2C 합니다. `-NotAfter` 날짜를 조정 하 여 인증서에 대해 다른 만료를 지정할 수도 있습니다.

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

1. **사용자 인증서 관리** > **현재 사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com* 을 엽니다.
1. **모든 작업** > **내보내기** > 인증서 > **작업** 을 선택 합니다.
1. **예** > **다음** > **예, 개인 키를 내보냅니다** > **다음** 을 선택 합니다.
1. **내보내기 파일 형식** 에 대한 기본값 적용
1. 인증서에 대한 암호를 제공 합니다.

### <a name="12-upload-the-certificate"></a>1.2 인증서 업로드

그런 다음 Azure AD B2C에 SAML 어설션 및 응답 서명 인증서를 업로드 합니다.

1. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 Azure AD B2C 테넌트로 이동 합니다.
1. **정책**에서 **Id 경험 프레임 워크** 를 선택 하 고 **정책 키**를 선택 합니다.
1. **추가**를 선택한 다음 **옵션** > **업로드**를 선택 합니다.
1. **이름**(예: *SamlIdpCert*)을 입력 합니다. 키의 이름에 *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. 파일 업로드 제어를 사용 하 여 인증서를 업로드 합니다.
1. 인증서의 암호를 입력 합니다.
1. **만들기**를 선택합니다.
1. 키가 예상 대로 표시 되는지 확인 합니다. 예를 들어 *B2C_1A_SamlIdpCert*합니다.

## <a name="2-prepare-your-policy"></a>2. 정책 준비

### <a name="21-create-the-saml-token-issuer"></a>2.1 SAML 토큰 발급자 만들기

이제 테넌트가 SAML 토큰을 발급 하는 기능을 추가 합니다.

사용자 지정 정책 시작 팩에서 `SocialAndLocalAccounts\` **`TrustFrameworkExtensions.xml`** 를 엽니다.

`<ClaimsProviders>` 섹션을 찾아 다음 XML 코드 조각을 추가 합니다.

`IssuerUri` 메타 데이터의 값을 변경할 수 있습니다. Azure AD B2C에서 SAML 응답으로 반환 되는 발급자 URI입니다. SAML 어설션 유효성 검사 중 발급자 URI를 허용 하도록 신뢰 당사자 응용 프로그램을 구성 해야 합니다.

```XML
<ClaimsProvider>
  <DisplayName>Token Issuer</DisplayName>
  <TechnicalProfiles>

    <!-- SAML Token Issuer technical profile -->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>Token Issuer</DisplayName>
      <Protocol Name="None"/>
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
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml"/>
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
    </TechnicalProfile>

  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="3-add-the-saml-relying-party-policy"></a>3. SAML 신뢰 당사자 정책을 추가 합니다.

이제 테넌트가 SAML 어설션을 발급할 수 있으므로 SAML 신뢰 당사자 정책을 만들고 JWT 대신 SAML 어설션을 발급 하도록 사용자 경험을 수정 해야 합니다.

### <a name="31-create-sign-up-or-sign-in-policy"></a>3.1 등록 또는 로그인 정책 만들기

1. 시작 팩 작업 디렉터리에 *Signuto signin .xml* 파일의 복사본을 만들고 새 이름으로 저장 합니다. 예를 들면 *SignUpOrSigninSAML*입니다. 이는 신뢰 당사자 정책 파일입니다.

1. 원하는 편집기에서 *SignUpOrSigninSAML* 파일을 엽니다.

1. 정책의 `PolicyId` 및 `PublicPolicyUri` 변경 하 여 아래와 같이 _B2C_1A_signup_signin_saml_ 하 고 `http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml` 합니다.

    ```XML
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="tenant-name.onmicrosoft.com"
    PolicyId="B2C_1A_signup_signin_saml"
    PublicPolicyUri="http://tenant-name.onmicrosoft.com/B2C_1A_signup_signin_saml">
    ```

1. `<RelyingParty>` 요소 바로 앞에 다음 XML 코드 조각을 추가 합니다. 이 XML은 _Signu폰_ 사용자 경험의 오케스트레이션 단계 번호 7을 덮어씁니다. 시작 팩의 다른 폴더에서 시작한 경우 또는 오케스트레이션 단계를 추가 또는 제거 하 여 사용자 경험을 사용자 지정 하는 경우 `order` 요소에 있는 번호는 토큰 발급자 단계에 대한 사용자 경험에 지정 된 번호와 일치 하는지 확인 합니다. 예를 들어 다른 스타터 팩 폴더에는 `LocalAccounts`에 대한 4 단계 4, `SocialAccounts`의 경우 6, `SocialAndLocalAccountsWithMfa`9)가 표시 됩니다.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignIn">
        <OrchestrationSteps>
          <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="Saml2AssertionIssuer"/>
        </OrchestrationSteps>
      </UserJourney>
    </UserJourneys>
    ```

1. `<RelyingParty>` 요소의 전체 `<TechnicalProfile>` 요소를 다음 기술 프로필 XML로 바꿉니다.

    ```XML
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

1. `tenant-name`를 Azure AD B2C 테넌트의 이름으로 업데이트 합니다.

최종 신뢰 당사자 정책 파일은 다음과 같습니다.

```XML
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

### <a name="32-upload-and-test-your-policy-metadata"></a>3.2 정책 메타 데이터 업로드 및 테스트

변경 내용을 저장 하 고 새 정책 파일을 업로드 합니다. 두 정책 (확장 및 신뢰 당사자 파일)을 업로드 한 후에는 웹 브라우저를 열고 정책 메타 데이터로 이동 합니다.

Azure AD B2C 정책 메타 데이터는 다음 URL에서 사용할 수 있습니다. `tenant-name` `policy-name`를 Azure AD B2C 테넌트의 이름으로 바꾸고,을 정책의 이름 (ID)으로 바꿉니다.

`https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`

이제 사용자 지정 정책 및 Azure AD B2C 테넌트가 준비 되었습니다. 다음으로 Azure AD B2C에서 응용 프로그램 등록을 만듭니다.

## <a name="4-setup-application-in-the-azure-ad-b2c-directory"></a>4. Azure AD B2C 디렉터리에 응용 프로그램을 설치 합니다.

### <a name="41-register-your-application-in-azure-active-directory"></a>4.1 Azure Active Directory에 응용 프로그램 등록

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure AD B2C**를 선택합니다. 또는 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **앱 등록(미리 보기)** 을 선택한 다음, **새 등록**을 선택합니다.
1. 애플리케이션의 **이름**을 입력합니다. 예를 들면 *SAMLApp1*입니다.
1. **지원 되는 계정 유형**에서 **이 조직 디렉터리에만 계정** 을 선택 합니다.
1. **URI 리디렉션**에서 **웹**을 선택 하 고 `https://localhost`를 입력 합니다. 이 값은 나중에 응용 프로그램 등록의 매니페스트에서 수정할 수 있습니다.
1. **Openid connect 및 offline_access 권한에 대해 관리자 동의 부여를**선택 합니다.
1. **등록**을 선택합니다.

### <a name="42-update-the-app-manifest"></a>4.2 응용 프로그램 매니페스트 업데이트

SAML 앱의 경우 응용 프로그램 등록의 매니페스트에서 구성 해야 하는 여러 가지 속성이 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 이전 섹션에서 만든 응용 프로그램 등록으로 이동 합니다.
1. **관리**아래에서 **매니페스트** 를 선택 하 여 매니페스트 편집기를 엽니다. 다음 섹션에서 몇 가지 속성을 수정 합니다.

#### <a name="identifieruris"></a>identifierUris

`identifierUris`은 Azure AD B2C 테넌트 내에서 웹 앱을 고유 하 게 식별 하는 사용자 정의 URI를 포함 하는 문자열 컬렉션입니다. 서비스 공급자는 SAML 요청의 `Issuer` 요소에이 값을 설정 해야 합니다.

#### <a name="samlmetadataurl"></a>samlMetadataUrl

이 속성은 서비스 공급자가 공개적으로 사용할 수 있는 메타 데이터 URL을 나타냅니다. 메타 데이터 URL은 blob storage와 같이 익명으로 액세스할 수 있는 엔드포인트에 업로드 된 메타 데이터 파일을 가리킬 수 있습니다.

메타 데이터는 saml 프로토콜에서 서비스 공급자와 같은 SAML 파티의 구성을 노출 하는 데 사용 되는 정보입니다. 메타 데이터는 로그인 및 로그 아웃, 인증서, 로그인 방법 등의 서비스 위치를 정의 합니다. Azure AD B2C는 서비스 공급자 메타 데이터를 읽고 그에 따라 동작 합니다. 메타 데이터는 필요 하지 않습니다. 응용 프로그램 매니페스트에서 직접 회신 URI 및 로그 아웃 URI와 같은 일부 특성을 지정할 수도 있습니다.

SAML 메타 데이터 URL과 응용 프로그램 등록의 매니페스트에 *모두* 지정 된 속성이 있으면 **병합**됩니다. 메타 데이터 URL에 지정 된 속성이 먼저 처리 되 고 우선 순위가 적용 됩니다.

SAML 테스트 응용 프로그램을 사용 하는이 자습서의 경우 `samlMetadataUrl`에 다음 값을 사용 합니다.

```JSON
"samlMetadataUrl":"https://samltestapp2.azurewebsites.net/Metadata",
```

#### <a name="replyurlswithtype-optional"></a>replyUrlsWithType (선택 사항)

메타 데이터 URI를 제공 하지 않는 경우 회신 URL을 명시적으로 지정할 수 있습니다. 이 선택적 속성은 `AssertionConsumerServiceUrl` (서비스 공급자 메타 데이터의`SingleSignOnService` URL)을 나타내며 `BindingType` `HTTP POST`것으로 간주 됩니다.

서비스 공급자 메타 데이터를 사용 하지 않고 응용 프로그램 매니페스트에서 회신 URL 및 로그 아웃 URL을 구성 하도록 선택 하는 경우 Azure AD B2C는 SAML 요청 서명의 유효성을 검사 하지 않고 SAML 응답을 암호화 하지 않습니다.

SAML 테스트 응용 프로그램을 사용 하는이 자습서에서는 `replyUrlsWithType`의 `url` 속성을 다음 JSON 코드 조각에 표시 된 값으로 설정 합니다.

```JSON
"replyUrlsWithType":[
  {
    "url":"https://samltestapp2.azurewebsites.net/SP/AssertionConsumer",
    "type":"Web"
  }
],
```

#### <a name="logouturl-optional"></a>LogoutUrl (옵션)

이 선택적 속성은 `Logout` URL (신뢰 당사자 메타 데이터의`SingleLogoutService` URL)을 나타내며이에 대한 `BindingType` `Http-Redirect`것으로 간주 됩니다.

SAML 테스트 응용 프로그램을 사용 하는이 자습서의 경우 `logoutUrl`를 `https://samltestapp2.azurewebsites.net/logout`으로 설정 된 상태로 둡니다.

```JSON
"logoutUrl": "https://samltestapp2.azurewebsites.net/logout",
```

## <a name="5-update-your-application-code"></a>5. 응용 프로그램 코드 업데이트

마지막 단계는 SAML 신뢰 당사자 응용 프로그램에서 SAML IdP Azure AD B2C를 사용 하도록 설정 하는 것입니다. 각 응용 프로그램은 서로 다르며이 작업을 수행 하는 단계는 다양 합니다. 자세한 내용은 앱 설명서를 참조 하세요.

일반적으로 다음 중 일부 또는 모두 필요 합니다.

* **메타 데이터**: `https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/policy-name/Samlp/metadata`
* **발급자**: `https://tenant-name.onmicrosoft.com/policy-name`
* **로그인 Url/saml 엔드포인트/Saml Url**: 메타 데이터 파일에서 값을 확인 합니다.
* **Certificate**: 개인 키가 없는 *B2C_1A_SamlIdpCert*입니다. 인증서의 공개 키를 가져오려면:

    1. 위에서 지정한 메타 데이터 URL로 이동 합니다.
    1. `<X509Certificate>` 요소에서 값을 복사 합니다.
    1. 텍스트 파일에 붙여 넣습니다.
    1. 텍스트 파일을 *.cer* 파일로 저장 합니다.

### <a name="51-test-with-the-saml-test-app-optional"></a>5.1 SAML 테스트 앱을 사용 하 여 테스트 (선택 사항)

[SAML 테스트 응용 프로그램][samltest]을 사용 하 여이 자습서를 완료 하려면:

* 테넌트 이름 업데이트
* 정책 이름 업데이트 (예: *B2C_1A_signup_signin_saml*
* 이 발급자 URI 지정: `https://contoso.onmicrosoft.com/app-name`

**로그인** 을 선택 하면 최종 사용자 로그인 화면이 표시 됩니다. 로그인 할 때 SAML 어설션이 샘플 응용 프로그램으로 다시 발급 됩니다.

## <a name="sample-policy"></a>샘플 정책

SAML 테스트 앱을 사용 하 여 테스트 하는 데 사용할 수 있는 전체 샘플 정책을 제공 합니다.

1. [SAML SP 시작 로그인 샘플 정책](https://github.com/azure-ad-b2c/saml-sp/tree/master/policy/SAML-SP-Initiated) 다운로드
1. 테넌트 이름과 일치 하도록 `TenantId`를 업데이트 합니다 (예: *contoso.b2clogin.com* ).
1. *B2C_1A_SAML2_signup_signin* 의 정책 이름을 유지 합니다.

## <a name="supported-and-unsupported-saml-modalities"></a>지원 되거나 지원 되지 않는 SAML 소프트웨어나

다음 SAML 신뢰 당사자 (RP) 시나리오는 사용자 고유의 메타 데이터 엔드포인트을 통해 지원 됩니다.

* 응용 프로그램/서비스 사용자 개체의 로그 아웃 URL에 대한 여러 로그 아웃 url 또는 POST 바인딩입니다.
* 응용 프로그램/서비스 사용자 개체에서 RP 요청을 확인할 서명 키를 지정 합니다.
* 응용 프로그램/서비스 사용자 개체에 토큰 암호화 키를 지정 합니다.
* Id 공급자가 시작한 로그인은 미리 보기 릴리스에서 현재 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

SAML 프로토콜에 대한 자세한 내용은 [OASIS 웹 사이트에서](https://www.oasis-open.org/)확인할 수 있습니다.

<!-- LINKS - External -->
[samltest]: https://aka.ms/samltestapp
