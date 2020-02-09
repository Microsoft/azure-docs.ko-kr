---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 RelyingParty 요소를 지정하는 방법을 설명합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/02/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7659c8187f7f4763b51b09362c94dad9554ed1c0
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982843"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**RelyingParty** 요소는 현재 요청 Azure Active Directory B2C (Azure AD B2C)에 적용할 사용자 경험을 지정 합니다. 또한 발급된 토큰의 일부로 RP(신뢰 당사자) 애플리케이션에 필요한 클레임 목록도 지정합니다. 웹, 모바일, 데스크톱 애플리케이션 등의 RP 애플리케이션은 RP 정책 파일을 호출합니다. RP 정책 파일은 로그인, 암호 재설정 또는 프로필 편집과 같은 특정 작업을 실행합니다. 여러 애플리케이션이 동일한 RP 정책을 사용할 수도 있고 단일 애플리케이션이 여러 정책을 사용할 수도 있습니다. 모든 RP 애플리케이션은 클레임이 포함된 동일 토큰을 수신하며, 사용자는 같은 사용자 경험을 진행하게 됩니다.

다음 예제에서는 *B2C_1A_signup_signin* 정책 파일의 **RelyingParty** 요소를 보여 줍니다.

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

선택적 **RelyingParty** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | RP 애플리케이션의 기본 사용자 경험입니다. |
| UserJourneyBehaviors | 0:1 | 사용자 경험 동작의 범위입니다. |
| TechnicalProfile | 1:1 | RP 애플리케이션에서 지원되는 기술 프로필입니다. 기술 프로필은 RP 애플리케이션이 Azure AD B2C에 연결할 때 적용되는 계약을 제공합니다. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

`DefaultUserJourney` 요소는 일반적으로 기본 또는 확장 정책에서 정의되는 사용자 경험의 식별자에 대한 참조를 지정합니다. 다음 예제에서는 **RelyingParty** 요소에 지정된 등록 또는 로그인 사용자 경험을 보여 줍니다.

*B2C_1A_signup_signin* 정책:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* 또는 *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

**DefaultUserJourney** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ReferenceId | 예 | 정책의 사용자 경험 식별자입니다. 자세한 내용은 [사용자 경험](userjourneys.md)을 참조하세요. |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

**UserJourneyBehaviors** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | 사용자 경험의 SSO(Single Sign-On) 세션 동작 범위입니다. |
| SessionExpiryType |0:1 | 세션의 인증 동작입니다. 가능한 값은 `Rolling` 또는 `Absolute`입니다. `Rolling` 값(기본값)은 사용자가 애플리케이션에서 계속 활성 상태이면 로그인된 상태로 유지됨 을 나타냅니다. `Absolute` 값은 애플리케이션 세션 수명에 따라 지정된 기간 후 사용자가 강제로 다시 인증을 해야 함을 나타냅니다. |
| SessionExpiryInSeconds | 0:1 | 인증 성공 시 사용자 브라우저에 저장되는 Azure AD B2C 세션 쿠키의 수명(정수로 지정됨)입니다. |
| JourneyInsights | 0:1 | 사용할 Application Insights 계측 키입니다. |
| ContentDefinitionParameters | 0:1 | 콘텐츠 정의 로드 URI에 추가할 키 값 쌍의 목록입니다. |

### <a name="singlesignon"></a>SingleSignOn

**SingleSignOn** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 범위 | 예 | Single Sign-On 동작의 범위입니다. 가능한 값은 `Suppressed`, `Tenant`, `Application` 또는 `Policy`입니다. `Suppressed` 값은 동작이 표시되지 않음을 나타냅니다. 예를 들어 Single Sign-On 세션의 경우에는 사용자에 대해 세션이 유지되지 않으며 ID 공급자를 선택하라는 메시지가 항상 사용자에게 표시됩니다. `TrustFramework` 값은 보안 프레임워크의 모든 정책에 동작이 적용됨을 나타냅니다. 예를 들어 보안 프레임워크의 두 공용 경험 간을 이동하는 사용자에게는 ID 공급자를 선택하라는 메시지가 표시되지 않습니다. `Tenant` 값은 테넌트의 모든 정책에 동작이 적용됨을 나타냅니다. 예를 들어 테넌트의 두 공용 경험 간을 이동하는 사용자에게는 ID 공급자를 선택하라는 메시지가 표시되지 않습니다. `Application` 값은 요청을 수행하는 애플리케이션의 모든 정책에 동작이 적용됨을 나타냅니다. 예를 들어 애플리케이션의 두 공용 경험 간을 이동하는 사용자에게는 ID 공급자를 선택하라는 메시지가 표시되지 않습니다. `Policy` 값은 동작이 한 정책에만 적용됨을 나타냅니다. 예를 들어 보안 프레임워크의 두 공용 경험 간을 이동하는 사용자가 정책 간을 전환할 때 ID 공급자를 선택하라는 메시지가 표시됩니다. |
| KeepAliveInDays | 예 | 사용자가 로그인 상태로 유지되는 기간을 제어합니다. 값을 0으로 설정하면 KMSI 기능이 해제됩니다. 자세한 내용은 [로그인 유지](custom-policy-keep-me-signed-in.md)를 참조하세요. |
|EnforceIdTokenHintOnLogout| 아닙니다.|  를 사용 하 여 이전에 발급 된 ID 토큰을 클라이언트와 최종 사용자의 현재 인증 된 세션에 대한 힌트로 로그 아웃 엔드포인트에 전달 합니다. 가능한 값은 `false`(기본값) 또는 `true`입니다. 자세한 내용은 [Openid connect Connect를 사용 하 여 웹 로그인](openid-connect.md)을 참조 하세요.  |


## <a name="journeyinsights"></a>JourneyInsights

**JourneyInsights** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| TelemetryEngine | 예 | 값은 `ApplicationInsights`여야 합니다. |
| InstrumentationKey | 예 | Application Insights 요소의 계측 키를 포함하는 문자열입니다. |
| DeveloperMode | 예 | 가능한 값은 `true` 또는 `false`입니다. 값이 `true`인 경우 Application Insights는 처리 파이프라인을 통해 원격 분석을 빠르게 수행합니다. 이 설정은 개발 시에는 유용하지만 정보의 양이 많은 경우에는 제약이 있습니다. 세부 활동 로그는 사용자 정의 정책 개발 시에만 도움이 되기 때문입니다. 프로덕션에서 개발 모드를 사용하지 않습니다. 로그는 개발 중에 ID 공급자 간에 전송된 모든 클레임을 수집합니다. 프로덕션에서 사용하는 경우 개발자는 소유한 App Insights 로그에서 수집된 PII(개인적으로 식별 가능한 정보)에 대한 책임이 있다고 가정합니다. 이 값이 `true`로 설정되어 있을 때만 이와 같은 자세한 로그가 수집됩니다.|
| ClientEnabled | 예 | 가능한 값은 `true` 또는 `false`입니다. 값이 `true`인 경우 페이지 보기 및 클라이언트 쪽 오류 추적용으로 Application Insights 클라이언트 쪽 스크립트를 전송합니다. |
| ServerEnabled | 예 | 가능한 값은 `true` 또는 `false`입니다. 값이 `true`인 경우 기존 UserJourneyRecorder JSON을 사용자 지정 이벤트로 Application Insights에 전송합니다. |
| TelemetryVersion | 예 | 값은 `1.0.0`여야 합니다. |

자세한 내용은 [로그 수집](troubleshoot-with-application-insights.md)을 참조하세요.

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Azure AD B2C에서 사용자 지정 정책을 사용하면 쿼리 문자열에 매개 변수를 보낼 수 있습니다. 매개 변수를 HTML 엔드포인트로 전달하면 페이지 콘텐츠를 동적으로 변경할 수 있습니다. 예를 들어 웹 또는 모바일 애플리케이션에서 전달한 매개 변수를 기반으로 Azure AD B2C 등록 또는 로그인 페이지에서 배경 이미지를 변경할 수 있습니다. Azure AD B2C는 쿼리 문자열 매개 변수를 aspx 파일과 같은 동적 HTML 파일에 전달합니다.

다음 예제에서는 쿼리 문자열에서 값이 `hawaii`인 `campaignId` 매개 변수를 전달합니다.

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

**ContentDefinitionParameters** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | 콘텐츠 정의 로드 URI의 쿼리 문자열에 추가되는 키 값 쌍을 포함하는 문자열입니다. |

**ContentDefinitionParameter** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 이름 | 예 | 키 값 쌍의 이름입니다. |

자세한 내용은 [사용자 지정 정책을 사용하여 동적 콘텐츠로 UI 구성](custom-policy-ui-customization-dynamic.md)을 참조하세요.

## <a name="technicalprofile"></a>TechnicalProfile

**TechnicalProfile** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| Id | 예 | 값은 `PolicyProfile`여야 합니다. |

**TechnicalProfile**에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| DisplayName | 1:1 | 기술 프로필의 이름을 포함 하는 문자열입니다. |
| Description | 0:1 | 기술 프로필에 대한 설명이 포함 된 문자열입니다. |
| 프로토콜 | 1:1 | 페더레이션에 사용되는 프로토콜입니다. |
| 메타데이터 | 0:1 | 신뢰 당사자와 기타 커뮤니티 참가자 간의 상호 작용을 구성하기 위한 트랜잭션 과정에서 엔드포인트와의 통신을 위해 프로토콜에서 사용하는 키/값 쌍의 *Item* 컬렉션입니다. |
| OutputClaims | 1:1 | 기술 프로필의 출력으로 가져오는 클레임 형식 목록입니다. 이러한 각 요소는 **ClaimsSchema** 섹션 또는 이 정책 파일이 상속을 하는 정책에 이미 정의되어 있는 **ClaimType**에 대한 참조를 포함합니다. |
| SubjectNamingInfo | 1:1 | 토큰에 사용되는 주체 이름입니다. |

**Protocol** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 이름 | 예 | 기술 프로필의 일부로 사용되는 Azure AD B2C에서 지원하는 유효한 프로토콜의 이름입니다. 가능한 값은 `OpenIdConnect` 또는 `SAML2`입니다. `OpenIdConnect` 값은 OpenID Foundation 사양에 따른 OpenID Connect 1.0 프로토콜 표준을 나타냅니다. `SAML2`는 OASIS 사양에 따른 SAML 2.0 프로토콜 표준을 나타냅니다. 프로덕션 환경에서는 SAML 토큰을 사용하지 마세요. |

## <a name="outputclaims"></a>OutputClaims

**OutputClaims** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | 신뢰 당사자가 구독하는 정책에 대해 지원되는 목록에 필요한 클레임 형식의 이름입니다. 이 클레임은 기술 프로필의 출력으로 사용됩니다. |

**OutputClaim** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | 예 | 정책 파일의 **ClaimsSchema** 섹션에 이미 정의되어 있는 **ClaimType**에 대한 참조입니다. |
| DefaultValue | 아닙니다. | 클레임 값이 비어 있는 경우 사용할 수 있는 기본값입니다. |
| PartnerClaimType | 아닙니다. | ClaimType 정의에 구성되어 있는 다른 이름으로 클레임을 보냅니다. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

**SubjectNameingInfo** 요소를 사용하여 토큰 제목의 값을 제어합니다.
- **JWT 토큰** -`sub` 클레임입니다. 애플리케이션 사용자 등 토큰에서 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어 토큰을 사용해 리소스에 액세스할 때 이 값을 사용하면 안전하게 권한 부여 확인을 수행할 수 있습니다. 기본적으로 주체 클레임은 디렉터리에 있는 사용자의 개체 ID로 채워집니다. 자세한 내용은 [토큰, 세션 및 Single Sign-On 구성](session-behavior.md)을 참조하세요.
- **SAML 토큰** - subject 요소를 식별하는 `<Subject><NameID>` 요소입니다.

**SubjectNamingInfo** 요소에는 다음과 같은 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| ClaimType | 예 | 출력 클레임의 **PartnerClaimType**에 대한 참조입니다. 신뢰 당사자 정책 **OutputClaims** 컬렉션에서 출력 클레임을 정의해야 합니다. |

다음 예제에서는 Openid connect Connect 신뢰 당사자를 정의 하는 방법을 보여 줍니다. 주체 이름 정보는 `objectId`로 구성됩니다.

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
JWT 토큰에는 사용자 objectId가 들어 있는 `sub` 클레임이 포함됩니다.

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```
