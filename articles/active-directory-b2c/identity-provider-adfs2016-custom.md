---
title: 사용자 지정 정책을 사용 하 여 SAML id 공급자로 ADFS 추가
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 SAML 프로토콜 및 사용자 지정 정책을 사용하 여 ADFS 2016 설정
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 684e4a410ac8624066c897b4078ea4044a965357
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848916"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 SAML ID 공급자로 ADFS 추가

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [사용자 지정 정책을](custom-policy-overview.md) 사용 하 여 ADFS 사용자 계정에 대한 로그인을 사용 하도록 설정 하는 방법을 보여 줍니다. 사용자 지정 정책에 [SAML 기술 프로필](saml-technical-profile.md)을 추가하여 로그인할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작](custom-policy-get-started.md)의 단계를 완료합니다.
- 프라이빗 키가 포함된 인증서 .pfx 파일에 액세스할 수 있는지 확인합니다. 자체 서명된 인증서를 생성하고 Azure AD B2C에 업로드할 수 있습니다. Azure AD B2C는 이 인증서를 사용하여 SAML ID 공급자에 보낸 SAML 요청에 서명합니다.
- Azure에서 .pfx 파일 암호를 수락 하도록 하려면 AES256와는 달리 Windows 인증서 저장소 내보내기 유틸리티의 TripleDES-SHA1 옵션을 사용 하 여 암호를 암호화 해야 합니다.

## <a name="create-a-policy-key"></a>정책 키 만들기

Azure AD B2C 테넌트에 인증서를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 테넌트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
5. **정책 키**, **추가**를 차례로 선택합니다.
6. **옵션**으로는 `Upload`을 선택합니다.
7. 정책 키의 **이름**을 입력합니다. `SamlCert`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 프라이빗 키가 있는 인증서 .pfx 파일을 찾아 선택합니다.
9. **만들기**를 클릭합니다.

## <a name="add-a-claims-provider"></a>클레임 공급자 추가

사용자가 ADFS 계정을 사용하여 로그인하도록 하려는 경우 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 계정을 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 사용하는 일련의 클레임을 제공하여 특정 사용자가 인증했는지 확인합니다.

정책의 확장 파일에서 **ClaimsProviders** 요소에 ADFS 계정을 추가하여 해당 계정을 클레임 공급자로 정의할 수 있습니다.

1. *TrustFrameworkExtensions.xml*을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 해당 요소가 없으면 루트 요소 아래에 추가합니다.
3. 다음과 같이 새 **ClaimsProvider**를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
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

4. `your-ADFS-domain`은 ADFS 도메인의 이름으로 바꾸고 **identityProvider** 출력 클레임의 값은 DNS(도메인을 나타내는 임의 값)로 바꿉니다.
5. 파일을 저장합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

이제 Azure AD B2C에서 ADFS 계정과 통신하는 방법을 알 수 있도록 정책이 구성되었습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다.

1. Azure AD B2C 테넌트의 **사용자 지정 정책** 페이지에서 **업로드 정책**을 선택합니다.
2. **정책이 있는 경우 덮어쓰기**를 사용하도록 설정하고 *TrustFrameworkExtensions.xml* 파일을 찾아서 선택합니다.
3. **업로드**를 클릭합니다.

> [!NOTE]
> Visual Studio code B2C 확장은 "socialIdpUserId"를 사용 합니다. ADFS에도 소셜 정책이 필요 합니다.
>

## <a name="register-the-claims-provider"></a>클레임 공급자 등록

이제 ID 공급자는 설정되었지만 등록 또는 로그인 화면에서 사용할 수는 없는 상태입니다. ID 공급자를 사용할 수 있게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 ADFS ID 공급자도 포함하도록 수정합니다.

1. 시작 팩에서 *TrustFrameworkBase.xml* 파일을 엽니다.
2. `Id="SignUpOrSignIn"`이 포함된 **UserJourney** 요소를 찾아서 전체 콘텐츠를 복사합니다.
3. *TrustFrameworkExtensions.xml*을 열어 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 사용자 경험 ID의 이름을 바꿉니다. `SignUpSignInADFS`)을 입력합니다.

### <a name="display-the-button"></a>단추 표시

**ClaimsProviderSelection** 요소는 등록 또는 로그인 화면의 ID 공급자 단추와 비슷합니다. ADFS 계정에 **ClaimsProviderSelection** 요소를 추가하면 사용자가 페이지를 열 때 새 단추가 표시됩니다.

1. 만든 사용자 경험에서 `Order="1"`이 포함된 **OrchestrationStep** 요소를 찾습니다.
2. **ClaimsProviderSelections** 아래에 다음 요소를 추가합니다. **TargetClaimsExchangeId** 값을 적절한 값(예: `ContosoExchange`)으로 설정합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>작업에 단추 연결

이제 단추가 준비되었으므로 동작에 연결해야 합니다. 여기서는 Azure AD B2C가 ADFS 계정과 통신하여 토큰을 수신하는 작업을 연결합니다.

1. 사용자 경험에서 `Order="2"`가 포함된 **OrchestrationStep**을 찾습니다.
2. 다음 **ClaimsExchange** 요소를 추가합니다. ID에는 **TargetClaimsExchangeId**에 사용한 것과 같은 값을 사용해야 합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    **TechnicalProfileReferenceId**의 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다. `Contoso-SAML2`)을 입력합니다.

3. *TrustFrameworkExtensions.xml* 파일을 저장하고 확인을 위해 다시 업로드합니다.


## <a name="configure-an-adfs-relying-party-trust"></a>ADFS 신뢰 당사자 트러스트 구성

Azure AD B2C에서 ADFS를 ID 공급자로 사용하려면 Azure AD B2C SAML 메타데이터를 사용하여 ADFS 신뢰 당사자 트러스트를 만들어야 합니다. 다음 예제에서는 Azure AD B2C 기술 프로필의 SAML 메타데이터에 대한 URL 주소를 보여 줍니다.

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

다음 값을 바꿉니다.

- **your-tenant**를 테넌트 이름(예: your-tenant.onmicrosoft.com)으로
- **your-policy**를 정책 이름으로. 예를 들어 B2C_1A_signup_signin_adfs로 바꿉니다.
- **-기술 프로필** 은 SAML id 공급자 기술 프로필의 이름입니다. 예를 들어 Contoso-SAML2로 바꿉니다.

브라우저를 열고 URL로 이동합니다. 올바른 URL을 입력했는지와 XML 메타데이터 파일에 대한 액세스 권한이 있는지 확인합니다. ADFS 관리 스냅인을 사용하여 새 신뢰 당사자 트러스트를 추가하고 설정을 수동으로 구성하려면 페더레이션 서버에서 다음 절차를 수행합니다. 로컬 컴퓨터에서 **관리자**또는 동등한 멤버 자격은 최소한 이 절차를 완료해야 합니다.

1. 서버 관리자에서 **도구**를 선택하고 **ADFS 관리**를 선택합니다.
2. **신뢰 당사자 트러스트 추가**를 선택합니다.
3. **시작** 페이지에서 **클레임 인식**을 클릭하고 **시작**을 선택합니다.
4. **데이터 원본 선택** 페이지에서 **Import data about the relying party publish online or on a local network**(신뢰 당사자 온라인 또는 로컬 네트워크 게시에 대한 정보 가져오기)를 선택하고 Azure AD B2C 메타데이터 URL을 제공한 후 **다음**을 클릭합니다.
5. **표시 이름 지정** 페이지에서 **표시 이름**을 입력하고 **메모** 아래에 이 신뢰 당사자 트러스트에 대한 설명을 입력한 후 **다음**을 클릭합니다.
6. **액세스 제어 정책 선택** 페이지에서 정책을 선택하고 **다음**을 클릭합니다.
7. **트러스트 추가 준비 완료** 페이지에서 설정을 검토하고 **다음**을 클릭하여 신뢰 당사자 트러스트 정보를 저장합니다.
8. **마침** 페이지에서 **닫기**를 클릭하면 이 작업이 **클레임 규칙 편집** 대화 상자를 자동으로 표시합니다.
9. **규칙 추가**를 선택합니다.
10. **클레임 규칙 템플릿**에서 **LDAP 특성을 클레임으로 전송**을 선택합니다.
11. **클레임 규칙 이름**을 제공합니다. **특성 저장소**에서 **Active Directory 선택**을 선택합니다. 다음과 같은 클레임을 추가한 후 **마침** 및 **확인**을 클릭합니다.

    | LDAP 특성 | 나가는 클레임 형식 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | Surname | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | 이메일 |
    | Display-Name | name |

    이러한 이름은 나가는 클레임 유형 드롭다운에서 표시 되지 않습니다. 수동으로 입력 해야 합니다. (드롭다운은 실제로 편집 가능 합니다.)

12.  인증서 유형에 따라 해시 알고리즘을 설정해야 할 수 있습니다. 신뢰 당사자 트러스트(B2C 데모) 속성 창에서 **고급** 탭을 선택하고 **보안 해시 알고리즘**을 `SHA-256`으로 변경하고 **확인**을 클릭합니다.
13. 서버 관리자에서 **도구**를 선택하고 **ADFS 관리**를 선택합니다.
14. 만든 신뢰 당사자 트러스트를 선택하고 **페더레이션 메타데이터에서 업데이트**를 선택한 후 **업데이트**를 클릭합니다.

## <a name="create-an-azure-ad-b2c-application"></a>Azure AD B2C 애플리케이션 만들기

Azure AD B2C와의 통신은 B2C 테넌트에 등록 하는 응용 프로그램을 통해 발생 합니다. 이 섹션에는 아직 만들지 않은 경우 테스트 애플리케이션을 만들기 위해 완료할 수 있는 선택적 단계가 나와 있습니다.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

### <a name="update-and-test-the-relying-party-file"></a>신뢰 당사자 파일 업데이트 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에서 *SignUpOrSignIn.xml*의 복사본을 만들고 이름을 바꿉니다. 예를 들어 파일 이름을 *SignUpSignInADFS.xml*로 바꿉니다.
2. 새 파일을 열고 **TrustFrameworkPolicy**의 **PolicyId** 특성 값을 고유 값으로 업데이트합니다. `SignUpSignInADFS`)을 입력합니다.
3. **PublicPolicyUri** 값을 정책의 URI로 업데이트합니다. 예를 들어 `http://contoso.com/B2C_1A_signup_signin_adfs`으로 업데이트할 수 있습니다.
4. 새로 만든 사용자 경험의 ID(SignUpSignInADFS)와 일치하도록 **DefaultUserJourney**의 **ReferenceId** 특성을 업데이트합니다.
5. 변경 내용을 저장하고 파일을 업로드한 다음, 목록에서 새 정책을 선택합니다.
6. **애플리케이션 선택** 필드에서 직접 만든 Azure AD B2C 애플리케이션이 선택되어 있는지 확인하고 **지금 실행**을 클릭하여 테스트를 진행합니다.

