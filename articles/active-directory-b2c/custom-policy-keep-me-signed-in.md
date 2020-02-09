---
title: Azure Active Directory B2C 로그인 상태 유지
description: Azure Active Directory B2C에서 KMSI(로그인 유지)를 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 052e1bc4e9a14b34e21b0bfeb4193fbd0b2b1a22
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848903"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 KMSI(로그인 유지) 사용

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 디렉터리에 로컬 계정이 있는 웹 및 네이티브 응용 프로그램 사용자에 대해 로그인 유지 (KMSI) 기능을 사용 하도록 설정할 수 있습니다. 이 기능은 사용자의 사용자 이름과 암호를 다시 입력 하 라는 메시지를 표시 하지 않고 응용 프로그램으로 돌아가는 사용자에 게 액세스 권한을 부여 합니다. 사용자가 로그아웃하면 이 액세스 권한이 철회됩니다.

사용자는 공용 컴퓨터에서 이 옵션을 사용하면 안됩니다.

![로그인 상태 유지 확인란을 표시 하는 등록 로그인 페이지의 예](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>필수 조건

로컬 계정 로그인을 허용 하도록 구성 된 Azure AD B2C 테넌트입니다. 외부 id 공급자 계정에는 KMSI가 지원 되지 않습니다.

테넌트가 없으면 [자습서: Azure Active Directory B2C 테넌트 만들기](tutorial-create-tenant.md)의 단계를 사용하여 테넌트를 만들 수 있습니다.

## <a name="add-a-content-definition-element"></a>콘텐츠 정의 요소 추가

확장 파일의 **BuildingBlocks** 요소 아래에 **ContentDefinitions** 요소를 추가합니다.

1. **ContentDefinitions** 요소 아래에 식별자가 `api.signuporsigninwithkmsi`인 **ContentDefinition** 요소를 추가합니다.
2. **ContentDefinition** 요소 아래에 **LoadUri**, **RecoveryUri** 및 **DataUri** 요소를 추가합니다. **DataUri** 요소의 `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` 값은 로그인 페이지에서 KMSI 확인란을 표시하고 머신에서 이해할 수 있는 식별자입니다. 이 값은 변경하면 안됩니다.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>로컬 계정에 대한 로그인 클레임 공급자 추가

정책의 확장 파일에 있는 **ClaimsProvider** 요소를 사용하여 로컬 계정 로그인을 클레임 공급자로 정의할 수 있습니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 파일을 엽니다.
2. **ClaimsProviders** 요소를 찾습니다. 없는 경우 루트 요소 아래에 이 요소를 추가합니다. [시작 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)에는 로컬 계정 로그인 클레임 공급자가 포함되어 있습니다.
3. 다음 예제와 같이 **DisplayName** 및 **TechnicalProfile**이 있는 **ClaimsProvider** 요소를 추가합니다.

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>사용자 지정 정책에 애플리케이션 식별자 추가

*TrustFrameworkExtensions.xml* 파일에 애플리케이션 식별자를 추가합니다.

1. *TrustFrameworkExtensions.xml* 파일에서 식별자가 `login-NonInteractive`인 **TechnicalProfile** 요소와 식별자가 `login-NonInteractive-PasswordChange`인 **TechnicalProfile** 요소를 찾고, 모두 [시작](custom-policy-get-started.md)에서 설명한 대로 `IdentityExperienceFrameworkAppId`의 모든 값을 ID 경험 프레임워크 애플리케이션의 애플리케이션 식별자로 바꿉니다.

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. [시작](custom-policy-get-started.md)에서 설명한 대로 `ProxyIdentityExperienceFrameworkAppId`의 모든 값을 프록시 ID 경험 프레임워크의 애플리케이션 식별자로 바꿉니다.
3. 확장 파일을 저장합니다.

## <a name="create-a-kmsi-enabled-user-journey"></a>KMSI 지원 사용자 경험 만들기

사용자 경험에 로컬 계정에 대한 로그인 클레임 공급자를 추가합니다.

1. 정책의 기본 파일을 엽니다(예: *TrustFrameworkBase.xml*).
2. **UserJourneys** 요소를 찾고, `SignUpOrSignIn` 식별자를 사용하는 **UserJourney** 요소의 전체 내용을 복사합니다.
3. 확장 파일을 엽니다(예: *TrustFrameworkExtensions.xml*). 그리고 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
4. **UserJourney** 요소 전체를 **UserJourneys** 요소의 자식으로 붙여넣습니다.
5. 새 사용자 경험에 대한 식별자 값을 변경합니다(예: `SignUpOrSignInWithKmsi`)을 입력합니다.
6. 마지막으로, 첫 번째 오케스트레이션 단계에서 **ContentDefinitionReferenceId** 값을 `api.signuporsigninwithkmsi`로 변경합니다. 이 값을 설정하면 사용자 경험에서 확인란이 활성화됩니다.
7. 확장 파일을 저장 및 업로드하고, 모든 유효성 검사가 성공했는지 확인합니다.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>신뢰 당사자 파일 만들기

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 작업 디렉터리에 *SignUpOrSignIn.xml* 파일의 복사본을 만든 다음, 이름을 바꿉니다(예: *SignUpOrSignInWithKmsi.xml*).
2. 새 파일을 열고, **TrustFrameworkPolicy**의 **PolicyId** 특성을 고유 값으로 업데이트합니다. 이 특성은 정책의 이름입니다(예: `SignUpOrSignInWithKmsi`)을 입력합니다.
3. 만든 새 사용자 경험의 식별자와 일치하도록 **DefaultUserJourney** 요소의 **ReferenceId** 특성을 변경합니다(예: `SignUpOrSignInWithKmsi`)을 입력합니다.

    KMSI는 첫 번째 자식 요소로 **SingleSignOn**, **SessionExpiryType** 및 **SessionExpiryInSeconds**를 사용하는 **UserJourneyBehaviors** 요소를 통해 구성됩니다. **KeepAliveInDays** 특성은 사용자가 로그인 상태로 유지되는 기간을 제어합니다. 다음 예제에서 KMSI 세션은 사용자가 자동 인증을 수행하는 빈도에 관계없이 `7`일 후에 자동으로 만료됩니다. **KeepAliveInDays** 값을 `0`으로 설정하면 KMSI 기능이 해제됩니다. 이 값은 기본적으로 `0`입니다. **SessionExpiryType** 값이 `Rolling`이면 사용자가 자동 인증을 수행할 때마다 KMSI 세션이 `7`일 연장됩니다.  `Rolling`을 선택하면 일 수를 최소로 유지해야 합니다.

    **SessionExpiryInSeconds** 값은 SSO 세션의 만료 시간을 나타냅니다. 이 값은 Azure AD B2C에서 KMSI 세션이 만료되었는지 여부를 확인하기 위해 내부적으로 사용됩니다. **KeepAliveInDays** 값은 웹 브라우저에서 SSO 쿠키의 만료/최대 기간 값을 결정합니다. **SessionExpiryInSeconds**와 달리 **KeepAliveInDays**는 브라우저가 닫힐 때 쿠키를 지우지 않도록 방지하는 데 사용됩니다. 사용자는 **KeepAliveInDays**로 제어되지만 **SessionExpiryInSeconds**로 만료되지 않도록 제어되는 SSO 세션 쿠키가 있는 경우에만 자동으로 로그인할 수 있습니다.

    사용자가 가입 및 로그인 페이지에서 **로그인 유지**를 활성화하지 않는 경우 세션은 **SessionExpiryInSeconds**에서 표시된 시간이 경과하거나 브라우저를 닫은 후에 만료됩니다. 사용자가 **로그인 유지**를 활성화하는 경우 **KeepAliveInDays**의 값은 **SessionExpiryInSeconds**의 값을 재정의하고 세션 만료 시간을 결정합니다. 사용자가 브라우저를 닫고 다시 열더라도 **KeepAliveInDays**의 시간 내에 있는 한 여전히 자동으로 로그인할 수 있습니다. **KeepAliveInDays**의 값을 아래 예제에 표시된 것과 같이 비교적 오랜 기간(7일)으로 설정할 수 있는 반면 **SessionExpiryInSeconds**의 값을 짧은 기간(1200초)으로 설정하는 것이 좋습니다.

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. 변경 내용을 저장한 다음, 파일을 업로드합니다.
5. Azure Portal에서 업로드한 사용자 지정 정책을 테스트하려면 정책 페이지로 이동한 다음, **지금 실행**을 선택합니다.

[여기](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)에서 샘플 정책을 찾을 수 있습니다.
