---
title: 컨트롤 참조 표시
titleSuffix: Azure AD B2C
description: Azure AD B2C 표시 컨트롤에 대 한 참조입니다. 표시 컨트롤을 사용 하 여 사용자 지정 정책에 정의 된 사용자 경험 사용자 지정 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7cbd088ed7b4f6ae242cce2067e52def2dad61c9
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136333"
---
# <a name="display-controls"></a>컨트롤 표시

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**표시 컨트롤** 은 특수 기능이 있고 Azure Active Directory B2C (Azure AD B2C) 백 엔드 서비스와 상호 작용 하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 [유효성 검사 기술 프로필](validation-technical-profile.md) 을 호출 하는 페이지에서 작업을 수행할 수 있습니다. 표시 컨트롤은 페이지에 표시 되며 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)에서 참조 됩니다.

다음 이미지는 기본 및 보조 전자 메일 주소의 유효성을 검사 하는 두 개의 표시 컨트롤이 있는 자체 어설션된 등록 페이지를 보여 줍니다.

![렌더링 된 디스플레이 컨트롤 예제](media/display-controls/display-control-email.png)

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>필수 조건

 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [메타 데이터](self-asserted-technical-profile.md#metadata) 섹션에서 참조 된 [contentdefinition](contentdefinitions.md) 에 `DataUri` 페이지 계약 버전 2.0.0 이상으로 설정 되어 있어야 합니다. 예를 들면 다음과 같습니다.

```XML
<ContentDefinition Id="api.selfasserted">
  <LoadUri>~/tenant/default/selfAsserted.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:selfasserted:2.0.0</DataUri>
  ...
```

## <a name="defining-display-controls"></a>표시 컨트롤 정의

**DisplayControl** 요소는 다음 특성을 포함 합니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | 예 | 표시 컨트롤에 사용 되는 식별자입니다. [참조할](#referencing-display-controls)수 있습니다. |
| UserInterfaceControlType | 예 | 표시 컨트롤의 형식입니다. 현재 지원 되는 [VerificationControl](display-control-verification.md) |

**DisplayControl** 요소에는 다음 요소가 포함 됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| InputClaims | 0:1 | **Inputclaims** 은 사용자 로부터 수집할 클레임 값을 미리 채우는 데 사용 됩니다. |
| DisplayClaims | 0:1 | **DisplayClaims** 는 사용자 로부터 수집할 클레임을 나타내는 데 사용 됩니다. |
| OutputClaims | 0:1 | **Outputclaims** 은이 **DisplayControl**에 대해 일시적으로 저장 되는 클레임을 나타내는 데 사용 됩니다. |
| 작업 | 0:1 | **작업** 은 프런트 엔드에서 발생 하는 사용자 작업에 대해 호출할 유효성 검사 기술 프로필을 나열 하는 데 사용 됩니다. |

### <a name="input-claims"></a>입력 클레임

표시 컨트롤에서 **inputclaims** 요소를 사용 하 여 페이지의 사용자 로부터 수집할 클레임 값을 미리 채울 수 있습니다. 이 표시 컨트롤을 참조 하는 자체 어설션된 기술 프로필에 모든 **InputClaimsTransformations** 을 정의할 수 있습니다.

다음 예에서는 주소가 이미 있는 것으로 확인 되는 전자 메일 주소를 인시던트의 합니다.

```XML
<DisplayControl Id="emailControl" UserInterfaceControlType="VerificationControl">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="emailAddress" />
  </InputClaims>
  ...
```

### <a name="display-claims"></a>클레임 표시

표시 컨트롤의 각 형식에는 다른 표시 클레임, [출력 클레임](#output-claims)및 수행할 [작업](#display-control-actions) 집합이 필요 합니다.

[자체 어설션된 기술 프로필](self-asserted-technical-profile.md#display-claims)에 정의 된 **표시 클레임** 과 마찬가지로 표시 클레임은 표시 컨트롤 내에서 사용자 로부터 수집할 클레임을 나타냅니다. 참조 된 **ClaimType** 요소는 `TextBox` 또는 `DropdownSingleSelect`와 같이 Azure AD B2C에서 지 원하는 사용자 입력 유형에 대해 **userinputtype** 요소를 지정 해야 합니다. **작업**에서 표시 클레임 값을 요구 하는 경우 **필수** 특성을 `true` 설정 하 여 사용자가 특정 표시 클레임에 대 한 값을 제공 하도록 합니다.

특정 유형의 표시 컨트롤에 대 한 특정 표시 클레임이 필요 합니다. 예를 들어 **VerificationControl**형식의 표시 컨트롤에는 **VerificationCode** 가 필요 합니다. 특성 **Controlclaimtype** 을 사용 하 여 해당 필수 클레임에 지정 된 DisplayClaim를 지정 합니다. 예를 들면 다음과 같습니다.

```XML
<DisplayClaim ClaimTypeReferenceId="otpCode" ControlClaimType="VerificationCode" Required="true" />
```

### <a name="output-claims"></a>출력 클레임

표시 컨트롤의 **출력 클레임** 은 다음 오케스트레이션 단계로 보내지지 않습니다. 현재 표시 컨트롤 세션에 대해서만 일시적으로 저장 됩니다. 이러한 임시 클레임은 동일한 디스플레이 컨트롤의 서로 다른 작업 간에 공유할 수 있습니다.

출력 클레임을 다음 오케스트레이션 단계로 버블링 하려면이 표시 컨트롤을 참조 하는 실제 자체 어설션된 기술 프로필의 **Outputclaims** 을 사용 합니다.

### <a name="display-control-actions"></a>컨트롤 동작 표시

표시 컨트롤의 **작업** 은 사용자가 클라이언트 쪽 (브라우저)에서 특정 작업을 수행할 때 Azure AD B2C 백 엔드에서 발생 하는 프로시저입니다. 예를 들어 사용자가 페이지에서 단추를 선택할 때 수행할 유효성 검사입니다.

작업은 **유효성 검사 기술 프로필**의 목록을 정의 합니다. 표시 컨트롤의 일부 또는 모든 표시 클레임의 유효성을 검사 하는 데 사용 됩니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사 하 고 사용자에 게 오류를 반환할 수 있습니다. 자체 어설션된 기술 프로필의 [유효성 검사 기술 프로필](validation-technical-profile.md) 에서 사용 되는 것과 유사한 컨트롤 표시 작업에서 **ContinueOnError**, **ContinueOnSuccess**및 **사전 조건을** 사용할 수 있습니다.

다음 예에서는 **mfaType** 클레임의 사용자 선택에 따라 전자 메일 또는 SMS에서 코드를 보냅니다.

```XML
<Action Id="SendCode">
  <ValidationClaimsExchange>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AzureMfa-SendSms">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>email</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
    <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="AadSspr-SendEmail">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>mfaType</Value>
          <Value>phone</Value>
          <Action>SkipThisValidationTechnicalProfile</Action>
        </Precondition>
      </Preconditions>
    </ValidationClaimsExchangeTechnicalProfile>
  </ValidationClaimsExchange>
</Action>
```

## <a name="referencing-display-controls"></a>표시 컨트롤 참조

표시 컨트롤은 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)의 [표시 클레임](self-asserted-technical-profile.md#display-claims) 에서 참조 됩니다.

예를 들면 다음과 같습니다.

```XML
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
  ...
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="PhoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
```
