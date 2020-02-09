---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 사용자 지정 정책의 TrustFrameworkPolicy 요소를 지정합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b49c873112358db4fec2992ef3d2d61161e8b373
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982418"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

사용자 지정 정책은 계층적 체인에서 서로를 참조하는 하나 이상의 XML 형식 파일로 표시됩니다. XML 요소는 클레임 스키마, 클레임 변환, 콘텐츠 정의, 클레임 공급자, 기술 프로필, 사용자 경험 및 오케스트레이션 단계와 같은 정책의 요소를 정의합니다. 각 정책 파일은 정책 파일의 최상위 수준 **TrustFrameworkPolicy** 요소 내에서 정의됩니다.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
|---------- | -------- | ----------- |
| PolicySchemaVersion | 예 | 정책을 실행하는 데 사용할 스키마 버전입니다. 값은 `0.3.0.0`이어야 합니다. |
| TenantObjectId | 아닙니다. | Azure Active Directory B2C (Azure AD B2C) 테넌트의 고유 개체 식별자입니다. |
| TenantId | 예 | 이 정책이 속한 테넌트의 고유 식별자입니다. |
| PolicyId | 예 | 정책의 고유 식별자입니다. 이 식별자 앞에는 *B2C_1A_* 가 접두사로 추가되어야 합니다. |
| PublicPolicyUri | 예 | 테넌트 ID와 정책 ID의 조합인 정책 URI입니다. |
| DeploymentMode | 아닙니다. | 가능한 값은 `Production`또는 `Development`입니다. 기본값은 `Production`입니다. 정책을 디버그하려면 이 특성을 사용하세요. 자세한 내용은 [로그 수집](troubleshoot-with-application-insights.md)을 참조하세요. |
| UserJourneyRecorderEndpoint | 아닙니다. | **DeploymentMode**를 `Development`로 설정한 경우 사용되는 엔드포인트입니다. 값은 `urn:journeyrecorder:applicationinsights`여야 합니다. 자세한 내용은 [로그 수집](troubleshoot-with-application-insights.md)을 참조하세요. |


다음 예제는 **TrustFrameworkPolicy** 요소를 지정하는 방법을 보여 줍니다.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>상속 모델

다음 유형의 정책 파일은 일반적으로 사용자 경험에서 사용됩니다.

- **기본 파일** - 대부분의 정의를 포함합니다. 문제 해결 및 정책의 장기적인 유지 관리에 도움이 되도록 이 파일의 변경 횟수를 최소화하는 것이 좋습니다.
- **확장 파일** - 테넌트에 대한 고유한 구성 변경 내용을 포함합니다. 이 정책 파일은 기본 파일에서 파생됩니다. 이 파일을 사용하여 새 기능을 추가하거나 기존 기능을 재정의할 수 있습니다. 예를 들어 이 파일을 사용하여 새 ID 공급자와 페더레이션합니다.
- **RP(신뢰 당사자)** 파일 - 웹, 모바일 또는 데스크톱 애플리케이션과 같은 신뢰 당사자 애플리케이션이 직접 호출하는 단일 작업 중심 파일입니다. 등록 또는 로그인, 암호 재설정 또는 프로필 편집과 같은 고유한 작업마다 자체 RP 정책 파일이 필요합니다. 이 정책 파일은 확장 파일에서 파생됩니다.

신뢰 당사자 애플리케이션은 RP 정책 파일을 호출하여 특정 작업을 실행합니다. 예를 들어 로그인 흐름을 시작합니다. Azure AD B2C의 ID 경험 프레임워크는 기본 파일, 확장 파일, RP 정책 파일의 모든 요소를 차례로 추가하여 현재 적용 중인 정책을 어셈블합니다. RP 파일에 있는 동일한 형식 및 이름의 요소가 확장에 있는 요소를 재정의하고, 확장이 기본을 재정의합니다. 다음 다이어그램은 정책 파일과 신뢰 당사자 애플리케이션 간의 관계를 보여 줍니다.

![보안 프레임 워크 정책 상속 모델을 보여 주는 다이어그램](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

상속 모델은 다음과 같습니다.

- 부모 정책과 자식 정책은 동일한 스키마입니다.
- 모든 수준의 자식 정책은 부모 정책에서 상속하고 새 요소를 추가하여 확장할 수 있습니다.
- 수준 수에는 제한이 없습니다.

자세한 내용은 [사용자 지정 정책 시작](custom-policy-get-started.md)을 참조하세요.

## <a name="base-policy"></a>기본 정책

다른 정책에서 정책을 상속하려면 **BasePolicy** 요소를 정책 파일의 **TrustFrameworkPolicy** 요소 아래에서 선언해야 합니다. **BasePolicy** 요소는 이 정책이 파생된 기본 정책에 대한 참조입니다.

**BasePolicy** 요소에는 다음 요소가 포함됩니다.

| 요소 | 발생 수 | Description |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C 테넌트의 식별자입니다. |
| PolicyId | 1:1 | 부모 정책의 식별자입니다. |


다음 예제는 기본 정책을 지정하는 방법을 보여 줍니다. 이 **B2C_1A_TrustFrameworkExtensions** 정책은 **B2C_1A_TrustFrameworkBase** 정책에서 파생됩니다.

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>정책 실행

웹, 모바일 또는 데스크톱 애플리케이션과 같은 신뢰 당사자 애플리케이션은 [RP(신뢰 당사자) 정책](relyingparty.md)을 호출합니다. RP 정책 파일은 로그인, 암호 재설정 또는 프로필 편집과 같은 특정 작업을 실행합니다. RP 정책은 신뢰 당사자 애플리케이션이 발급된 토큰의 일부로 수신하는 클레임 목록을 구성합니다. 여러 애플리케이션이 동일한 정책을 사용할 수 있습니다. 모든 응용 프로그램은 클레임을 사용 하 여 동일한 토큰을 받고 사용자는 동일한 사용자 경험을 거칩니다. 단일 애플리케이션이 여러 개의 정책을 사용할 수 있습니다.

RP 정책 파일 내에서 [UserJourney](userjourneys.md)를 가리키는 **DefaultUserJourney** 요소를 지정합니다. 일반적으로 사용자 경험은 기본 또는 확장 정책에서 정의됩니다.

B2C_1A_signup_signin 정책:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase 또는 B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

사용자 경험은 사용자가 진행하는 과정의 비즈니스 논리를 정의합니다. 각 사용자 경험은 인증 및 정보 수집의 측면에서 순서대로 일련의 작업을 수행하는 오케스트레이션 단계 집합입니다.

[스타터 팩](custom-policy-get-started.md#custom-policy-starter-pack)의 **SocialAndLocalAccounts** 정책 파일은 SignUpOrSignIn, ProfileEdit, PasswordReset 사용자 경험을 포함합니다. 전자 메일 주소를 변경 하거나 소셜 계정의 연결 및 연결을 해제 하는 등의 다른 시나리오에 더 많은 사용자 경험를 추가할 수 있습니다.

오케스트레이션 단계는 [기술 프로필](technicalprofiles.md)을 호출할 수 있습니다. 기술 프로필은 다른 유형의 당사자와 통신하기 위한 기본 제공 메커니즘이 있는 프레임워크를 제공합니다. 예를 들어 기술 프로필은 다음 작업을 수행할 수 있습니다.

- 사용자 경험을 렌더링합니다.
- 사용자가 Facebook, Microsoft 계정, Google, Salesforce 또는 기타 ID 공급자와 같은 소셜 또는 엔터프라이즈 계정으로 로그인할 수 있도록 합니다.
- MFA를 위한 전화 검증을 설정합니다.
- Azure AD B2C ID 저장소에서 데이터를 읽고 씁니다.
- 사용자 지정 Restful API 서비스를 호출합니다.

![정책 실행 흐름을 보여 주는 다이어그램](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** 요소에는 다음 요소가 포함됩니다.

- 위에 지정된 BasePolicy
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
