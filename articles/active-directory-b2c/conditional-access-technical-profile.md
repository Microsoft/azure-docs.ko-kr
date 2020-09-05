---
title: 사용자 지정 정책에서 조건부 액세스 기술 프로필
titleSuffix: Azure AD B2C
description: Azure AD B2C의 조건부 액세스 기술 프로필에 대 한 사용자 지정 정책 참조입니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/01/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 080b5a224f3d4a720d8009933ddd9161f56dba0a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270107"
---
# <a name="define-a-conditional-access-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 조건부 액세스 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD (Azure Active Directory) 조건부 액세스는 Azure AD B2C에서 신호를 함께 가져오고, 결정을 내리고, 조직 정책을 적용 하기 위해 사용 하는 도구입니다. 정책 조건을 사용하여 위험 평가를 자동화하면 위험한 로그인을 한 번에 식별하여 수정하거나 차단할 수 있습니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. **Handler** 특성은 Azure AD B2C에서 사용 하는 프로토콜 처리기 어셈블리의 정규화 된 이름을 포함 해야 합니다.

```
Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null
```

다음 예제에서는 조건부 액세스 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
```

## <a name="conditional-access-evaluation"></a>조건부 액세스 평가

모든 로그인에 대해 Azure AD B2C 모든 정책을 평가 하 고 사용자 액세스 권한을 부여 하기 전에 모든 요구 사항을 충족 하는지 확인 합니다. "액세스 차단"은 다른 모든 구성 설정을 재정의 합니다. 조건부 액세스 기술 프로필의 **평가** 모드는 로컬 계정으로 로그인 하는 동안 Azure AD B2C에 의해 수집 된 신호를 평가 합니다. 조건부 액세스 기술 프로필의 결과는 조건부 액세스 평가의 결과로 생성 되는 클레임 집합입니다. Azure AD B2C 정책은 다음 오케스트레이션 단계에서 이러한 클레임을 사용 하 여 사용자를 차단 하거나 multi-factor authentication에서 사용을 시도 하는 등의 작업을 수행 합니다. 이 모드에 대해 다음과 같은 옵션을 구성할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| OperationType | 예 | **평가**해야 합니다.  |

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 조건부 액세스에 보낼 클레임 목록을 포함 합니다. 또한 클레임의 이름을 조건부 액세스 기술 프로필에 정의 된 이름에 매핑할 수 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| UserId | 예 | 문자열 | 로그인 한 사용자의 식별자입니다. |
| AuthenticationMethodsUsed | 예 |stringCollection | 사용자가 로그인 하는 데 사용한 방법의 목록입니다. 가능한 값은 `Password` , 및 `OneTimePasscode` 입니다. |
| IsFederated | 예 |boolean | 사용자가 페더레이션된 계정으로 로그인 했는지 여부를 나타냅니다. 값은 `false`여야 합니다. |
| IsMfaRegistered | 예 |boolean | 사용자가 multi-factor authentication에 전화 번호를 이미 등록 했는지 여부를 나타냅니다. |


**InputClaimsTransformations** 요소에는 입력 클레임을 수정 하거나 조건부 액세스 서비스로 보내기 전에 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션이 포함 될 수 있습니다.

### <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소에는 ConditionalAccessProtocolProvider에서 생성 된 클레임 목록이 포함 됩니다. 클레임의 이름을 아래 정의 된 이름에 매핑할 수도 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| 과제 | 예 |stringCollection | 식별 된 위협을 수정할 작업 목록입니다. 가능한 값: `block` |
| MultiConditionalAccessStatus | 예 | stringCollection |  |

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="example-evaluation"></a>예: Evaluation

다음 예제에서는 로그인 위협을 평가 하는 데 사용 되는 조건부 액세스 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="ConditionalAccessEvaluation">
  <DisplayName>Conditional Access Provider</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="OperationType">Evaluation</Item>
  </Metadata>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="IsMfaRegistered" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="UserId" />
    <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" />
    <InputClaim ClaimTypeReferenceId="IsFederated" DefaultValue="false" />
    <InputClaim ClaimTypeReferenceId="IsMfaRegistered" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="Challenges" />
    <OutputClaim ClaimTypeReferenceId="ConditionalAccessStatus" PartnerClaimType="MultiConditionalAccessStatus" />
  </OutputClaims>
</TechnicalProfile>
```

## <a name="remediation"></a>수정

조건부 액세스 기술 프로필의 **수정** 모드는 로그인 식별 된 위협이 재구성 됨을 Azure AD B2C에 게 알립니다. 다음 옵션은 재구성 모드에 대해 구성할 수 있습니다.

### <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| OperationType | 예 | **수정**해야 합니다.  |

### <a name="input-claims"></a>입력 클레임

**Inputclaims** 요소는 조건부 액세스에 보낼 클레임 목록을 포함 합니다. 또한 클레임의 이름을 조건부 액세스 기술 프로필에 정의 된 이름에 매핑할 수 있습니다.

| ClaimReferenceId | 필수 | 데이터 형식 | 설명 |
| --------- | -------- | ----------- |----------- |
| ChallengesSatisfied | 예 | stringCollection| 평가 모드에서 반환 되는 것으로 확인 된 위협을 해결 하는 데 충족 된 과제의 목록으로, 과제가 청구 됩니다.|


**InputClaimsTransformations** 요소는 조건부 액세스 서비스를 호출 하기 전에 입력 클레임을 수정 하거나 새 항목을 생성 하는 데 사용 되는 **InputClaimsTransformation** 요소의 컬렉션을 포함할 수 있습니다.

### <a name="output-claims"></a>출력 클레임

조건부 액세스 프로토콜 공급자는 **outputclaims**반환 되지 않으므로 출력 클레임을 지정할 필요가 없습니다. 그러나 특성을 설정 하는 동안에는 조건부 액세스 프로토콜 공급자에서 반환 하지 않은 클레임을 포함할 수 있습니다 `DefaultValue` .

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.


### <a name="example-remediation"></a>예: 재구성

다음 예제에서는 식별 된 위협을 수정 하는 데 사용 되는 조건부 액세스 기술 프로필을 보여 줍니다.

```XML
<TechnicalProfile Id="ConditionalAccessRemediation">
  <DisplayName>Conditional Access Remediation</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ConditionalAccessProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
  <Metadata>
    <Item Key="OperationType">Remediation</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" PartnerClaimType="ChallengesSatisfied" />
  </InputClaims>
</TechnicalProfile>
```

예제를 사용 하려면 다음 예제에 표시 된 클레임을 확장 파일의 ClaimsSchema 섹션에 추가 합니다.

```XML
<!-- Conditional Access claims  -->
   <ClaimType Id="conditionalAccessClaimCollection">
      <DisplayName>Conditional Access claims</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of claims which are the result of CA check</AdminHelpText>
   </ClaimType>
   <ClaimType Id="ConditionalAccessStatus">
      <DisplayName>The status of CA evaluation</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The status of CA evaluation</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodsUsed">
      <DisplayName>Authentication methods used</DisplayName>
      <DataType>stringCollection</DataType>
      <AdminHelpText>The list of authentication methods used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="AuthenticationMethodUsed">
      <DisplayName>Authentication method used</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>The authentication method used</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsFederated">
      <DisplayName>IsFederated</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user authenticated via an external identity provider</AdminHelpText>
    </ClaimType>
    <ClaimType Id="IsMfaRegistered">
      <DisplayName>IsMfaRegistered</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Is user registered for MFA</AdminHelpText>
    </ClaimType> 
    <ClaimType Id="CAChallengeIsMfa">
      <DisplayName>CAChallengeIsMfa</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsChgPwd">
      <DisplayName>CAChallengeIsChgPwd</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="CAChallengeIsBlock">
      <DisplayName>CAChallengeIsBlock</DisplayName>
      <DataType>boolean</DataType>
    </ClaimType>
    <ClaimType Id="responseMsg">
      <DisplayName>responseMsg</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A claim responsible for holding response messages to send to the relying party</AdminHelpText>
      <UserHelpText>A claim responsible for holding response messages to send to the relying party</UserHelpText>
      <UserInputType>Paragraph</UserInputType>
    </ClaimType>
<!-- End of Conditional Access claims -->
```

다음 클레임 변환을 추가 합니다.

```xml
<!--Conditional Access Transformations-->
  <ClaimsTransformation Id="AddToAuthenticationMethodsUsed" TransformationMethod="AddItemToStringCollection">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="item" />
      <InputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodsUsed" TransformationClaimType="collection" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreatePasswordAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="Password" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="CreateOneTimePasscodeAuthenticationMethodClaim" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="OneTimePasscode" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="AuthenticationMethodUsed" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsMfaRegisteredCT" TransformationMethod="DoesClaimExist">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" TransformationClaimType="inputClaim" />
    </InputClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="IsMfaRegistered" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="SetCAChallengeIsMfa" TransformationMethod="StringCollectionContains">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="item" DataType="string" Value="mfa" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" TransformationClaimType="outputClaim" />
    </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsChgPwd" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="chg_pwd" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="SetCAChallengeIsBlock" TransformationMethod="StringCollectionContains">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" TransformationClaimType="inputClaim" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="item" DataType="string" Value="block" />
        <InputParameter Id="ignoreCase" DataType="string" Value="true" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
```

다음 기술 프로필을 추가 합니다.

```xml
<TechnicalProfile Id="GenerateCAClaimFlags">
  <DisplayName>GenerateCAClaimFlags</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsChgPwd" />
    <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
  </OutputClaims>
  <OutputClaimsTransformations>
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsMfa" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsChgPwd" />
    <OutputClaimsTransformation ReferenceId="SetCAChallengeIsBlock" />
  </OutputClaimsTransformations>
</TechnicalProfile>

<!--This is a self-asserted technical profile that we will use to show a block screen-->
<TechnicalProfile Id="ShowBlockPage">
  <DisplayName>Show Block message</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
    <Item Key="TokenLifeTimeInSeconds">3600</Item>
    <Item Key="AllowGenerationOfClaimsWithNullValues">true</Item>
    <Item Key="setting.showContinueButton">false</Item>
    <Item Key="setting.showCancelButton">false</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="responseMsg" DefaultValue="The user is blocked due to conditional access check." />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="responseMsg" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
  <EnabledForUserJourneys>Always</EnabledForUserJourneys>
</TechnicalProfile>

```

TrustFrameworkPolicy 요소에서 다음 예제와 같이 이러한 SubJourneys를 추가 합니다.

```xml
  <SubJourneys>
    <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>

    <SubJourney Id="ConditionalAccess_Remediation" Type="Call">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>conditionalAccessClaimCollection</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ConditionalAccessRemediation" TechnicalProfileReferenceId="ConditionalAccessRemediation" />
          </ClaimsExchanges>
        </OrchestrationStep>
      </OrchestrationSteps>
    </SubJourney>

```

다음 예와 같이 새 클레임을 사용 하는 사용자 경험을 추가 합니다.

```xml
  <UserJourneys>
    <UserJourney Id="SignUpOrSignInWithCA">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninsam">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />

          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Check if the user has selected to sign in using one of the social providers -->
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

        <!-- For social IDP authentication, attempt to find the user account in the directory. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>authenticationSource</Value>
              <Value>socialIdpAuthentication</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="4" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="UserJourneyContext" TechnicalProfileReferenceId="SimpleUJContext" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="5" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
          </JourneyList>
        </OrchestrationStep>

        <!--MFA based on Conditional Access-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsMfa</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>CAChallengeIsMfa</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--Save MFA phone number: The precondition verifies whether the user provided a new number in the previous step. If so, the phone number is stored in the directory for future authentication requests.-->
        <OrchestrationStep Order="7" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newPhoneNumberEntered</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserWriteWithObjectId" TechnicalProfileReferenceId="AAD-UserWritePhoneNumberUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <OrchestrationStep Order="8" Type="ClaimsExchange" >
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
              <Value>CAChallengeIsBlock</Value>
              <Value>true</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="ShowBlockPage" TechnicalProfileReferenceId="ShowBlockPage" />
          </ClaimsExchanges>
        </OrchestrationStep>

        <!--If a user has reached this point, this means a remediation was applied-->
        <!--  You can add a precondition here to call remediation only if a Conditional Access challenge was issued-->
        <OrchestrationStep Order="9" Type="InvokeSubJourney">
          <JourneyList>
            <Candidate SubJourneyReferenceId="ConditionalAccess_Remediation" />
          </JourneyList>
        </OrchestrationStep>
        <OrchestrationStep Order="10" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>

```

다음은이 UserJourney를 참조 하는 신뢰 당사자 파일의 예입니다.

```xml
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
                      xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
                      xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
                      PolicySchemaVersion="0.3.0.0" TenantId="contoso.onmicrosoft.com" 
                      PolicyId="ha-sam-signup_signin-CA" 
                      PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_signup_signin_CA"
                      DeploymentMode="Development"
                      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  <BasePolicy>
    <TenantId>contoso.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignInWithCA" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>604800</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="<add your app insights instrumentation key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="signInName" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsMfa" />
        <OutputClaim ClaimTypeReferenceId="CAChallengeIsBlock" />
        <OutputClaim ClaimTypeReferenceId="conditionalAccessClaimCollection" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```
