---
title: Azure Active Directory B2C를 사용하여 직접 로그인 설정 | Microsoft Docs
description: 로그인 이름을 미리 채우거나 소셜 ID 공급자에 직접 리디렉션하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 35e8efa269ab72477b06e86824d368d0a3dced03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103197319"
---
# <a name="set-up-direct-sign-in-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 직접 로그인 설정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD(Active Directory) B2C를 사용하여 애플리케이션에 대한 로그인을 설정하는 경우 로그인 이름을 미리 채우거나 Facebook, LinkedIn, Microsoft 계정 같은 특정 소셜 ID 공급자로 직접 로그인할 수 있습니다.

## <a name="prepopulate-the-sign-in-name"></a>로그인 이름 미리 채우기

로그인 사용자 과정 동안, 신뢰 당사자 애플리케이션은 특정 사용자 또는 도메인 이름을 대상으로 지정할 수 있습니다. 사용자를 대상으로 지정할 경우 애플리케이션은 권한 부여 요청에 `login_hint` 쿼리 매개 변수와 사용자 로그인 이름을 지정할 수 있습니다. 사용자는 암호를 제공하기만 하면 되지만, Azure AD B2C는 로그인 이름을 자동으로 채웁니다.

![URL에 강조 표시된 login_hint 쿼리 매개 변수를 사용하여 로그인 페이지 등록](./media/direct-signin/login-hint.png)

사용자는 로그인 텍스트 상자에서 해당 값을 변경할 수 있습니다.

::: zone pivot="b2c-custom-policy"

로그인 힌트 매개 변수를 지원하려면 `SelfAsserted-LocalAccountSignin-Email` 기술 프로필을 재정의합니다. `<InputClaims>` 섹션에서 signInName 클레임의 , DefaultValue를 `{OIDC:LoginHint}`로 설정합니다. `{OIDC:LoginHint}` 변수는 `login_hint` 매개 변수의 값을 포함합니다. Azure AD B2C는 signInName 클레임의 값을 읽고, signInName 텍스트 상자를 미리 채웁니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <InputClaims>
        <!-- Add the login hint value to the sign-in names claim type -->
        <InputClaim ClaimTypeReferenceId="signInName" DefaultValue="{OIDC:LoginHint}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

## <a name="redirect-sign-in-to-a-social-provider"></a>소셜 공급자로 로그인 리디렉션

Facebook, LinkedIn 또는 Google 등과 같은 소셜 계정을 포함하도록 애플리케이션에 대한 로그인 과정을 구성한 경우 `domain_hint` 매개 변수를 지정할 수 있습니다. 이 쿼리 매개 변수는 로그인에 사용해야 하는 소셜 ID 공급자에 대한 힌트를 Azure AD B2C에 제공합니다. 예를 들어, 애플리케이션이 `domain_hint=facebook.com`을 지정하는 경우 로그인을 수행하면 Facebook 로그인 페이지로 직접 이동됩니다.

![URL에 강조 표시된 domain_hint 쿼리 매개 변수를 사용하여 로그인 페이지 등록](./media/direct-signin/domain-hint.png)

::: zone pivot="b2c-user-flow"

도메인 힌트 쿼리 문자열 매개 변수는 다음 도메인 중 하나로 설정할 수 있습니다.

- amazon.com
- facebook.com
- github.com
- google.com
- linkedin.com
- microsoft.com
- qq.com
- twitter.com
- wechat.com
- weibo.com 
- [일반 OpenID Connect](identity-provider-generic-openid-connect.md)의 경우 [도메인 힌트](identity-provider-generic-openid-connect.md#response-mode)를 참조하세요.

::: zone-end

::: zone pivot="b2c-custom-policy"

도메인 힌트 매개 변수를 지원하기 위해 모든 `<ClaimsProvider>`의 `<Domain>domain name</Domain>` XML 요소를 사용하여 도메인 이름을 구성할 수 있습니다.

```xml
<ClaimsProvider>
    <!-- Add the domain hint value to the claims provider -->
    <Domain>facebook.com</Domain>
    <DisplayName>Facebook</DisplayName>
    <TechnicalProfiles>
    ...
```

::: zone-end

