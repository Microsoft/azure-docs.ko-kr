---
title: Azure Active Directory B2C에서 사용자 액세스 관리 | Microsoft Docs
description: Azure AD B2C를 사용 하 여 미성년자 미성년자를 식별 하 고, 생년월일 및 국가/지역 데이터를 수집 하 고, 응용 프로그램에서 사용 약관에 동의 하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 90be5b407708f6cca3748dd6d6fa09c28ab7fcdc
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840437"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 액세스 관리

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)를 사용 하 여 응용 프로그램에 대한 사용자 액세스를 관리 하는 방법을 설명 합니다. 애플리케이션의 액세스 관리에는 다음 항목이 포함됩니다.

- 미성년자 식별 및 애플리케이션에 대한 사용자 액세스 제어
- 애플리케이션을 사용하는 미성년자에 대한 보호자의 동의 요구
- 사용자의 생년월일 및 국가/지역 데이터를 수집 합니다.
- 사용 약관 계약 캡처 및 액세스 제한

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>미성년자 액세스 제어

애플리케이션 및 조직은 미성년자를 대상으로 하지 않는 애플리케이션 및 서비스를 미성년자가 사용하지 못하도록 차단할 수 있습니다. 또는 애플리케이션 및 조직에서 미성년자를 받아들인 후 보호자의 동의를 관리하고 비즈니스 규칙에 규정되고 규정에 따라 허용되는 경험을 미성년자에게 제공할 수 있습니다.

사용자가 미성년자로 식별되면 Azure AD B2C에서 사용자 흐름을 다음 세 가지 옵션 중 하나로 설정할 수 있습니다.

- **애플리케이션에 서명된 JWT id_token 보내기**: 사용자가 디렉터리에 등록되고, 토큰이 애플리케이션으로 반환됩니다. 그런 다음, 애플리케이션에서 비즈니스 규칙을 적용하여 진행됩니다. 예를 들어, 애플리케이션이 보호자의 동의 프로세스를 진행할 수 있습니다. 이 방법을 사용하려면 프로그램으로부터 **ageGroup** 및 **consentProvidedForMinor** 클레임을 받도록 선택합니다.

- **애플리케이션에 서명되지 않은 JSON 토큰 보내기**: Azure AD B2C에서 사용자가 미성년자임을 애플리케이션에 알리고 사용자의 보호자 동의 상태를 제공합니다. 그런 다음, 애플리케이션에서 비즈니스 규칙을 적용하여 진행됩니다. JSON 토큰이 애플리케이션 인증을 성공적으로 완료하지 못합니다. 애플리케이션은 JSON 토큰에 포함된 클레임에 따라 인증되지 않은 사용자를 처리해야 합니다. 여기에는 **name**, **email**, **ageGroup** 및 **consentProvidedForMinor**가 포함될 수 있습니다.

- 사용자 **차단**: 사용자가 부이 고 보호자 동의가 제공 되지 않은 경우 사용자에 게 차단 됨을 알릴 수 Azure AD B2C. 토큰이 발급되지 않고 액세스가 차단되고 등록 과정에서 사용자 계정이 만들어지지 않습니다. 이 알림을 구현하려면 적합한 HTML/CSS 콘텐츠 페이지를 제공하여 사용자에게 알리고 적절한 옵션을 제시합니다. 애플리케이션에서 신규 등록을 위한 추가 작업이 필요하지 않습니다.

## <a name="get-parental-consent"></a>보호자 동의 얻기

애플리케이션 규정에 따라 사용자가 성인으로 확인된 보호자의 동의를 받아야 할 수 있습니다. Azure AD B2C는 개인의 나이를 확인하여 확인된 성인이 미성년자에게 보호자 동의를 부여하는 환경을 제공하지 않습니다. 이러한 환경은 애플리케이션이나 다른 서비스 공급자가 제공해야 합니다.

다음은 보호자 동의를 수집하는 사용자 흐름의 예입니다.

1. [Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog) 작업이 사용자를 미성년자로 식별하고 서명되지 않은 JSON 토큰의 형태로 사용자 데이터를 애플리케이션에 반환합니다.

2. 응용 프로그램은 JSON 토큰을 처리 하 고 보조에 화면을 표시 하 여 보호자 동의가 필요 하 고 부모 온라인의 동의를 요청 하 고 있음을 알립니다.

3. Azure AD B2C에서 사용자가 정상적으로 로그인할 수 있는 로그인 경로를 보여 주고, **legalAgeGroupClassification = “minorWithParentalConsent”** 가 포함되도록 설정된 토큰을 애플리케이션에 발급합니다. 애플리케이션에서 보호자 이메일 주소를 수집하고 보호자가 성인인지 확인합니다. 이를 위해 주민 등록증, 면허증 확인 또는 신용 카드 증명과 같은 신뢰할 수 있는 자료를 사용합니다. 성공적으로 확인되면 애플리케이션에서 미성년자에게 Azure AD B2C 사용자 흐름을 사용하여 로그인하라는 메시지를 표시합니다. 동의가 거부되면(예: **legalAgeGroupClassification = “minorWithoutParentalConsent”** ) Azure AD B2C에서 동의 프로세스를 다시 시작하기 위해 애플리케이션에 JSON 토큰(로그인 아님)을 반환합니다. 필요에 따라 미성년자 또는 성인이 등록 코드를 미성년자의 이메일 주소 또는 기록에 있는 성인의 이메일 주소로 보내서 미성년자의 계정에 다시 액세스할 수 있도록 사용자 흐름을 사용자 지정할 수도 있습니다.

4. 애플리케이션이 미성년자에게 동의 철회 옵션을 제공합니다.

5. 미성년자 또는 성인이 동의를 철회하면 Azure AD Graph API를 사용하여 **consentProvidedForMinor**를 **denied**로 변경할 수 있습니다. 또는 애플리케이션이 동의가 철회된 미성년자를 삭제하도록 선택할 수도 있습니다. 필요에 따라 인증된 미성년자(또는 미성년자의 계정을 사용하는 보호자)가 동의를 철회할 수 있도록 사용자 흐름을 사용자 지정할 수도 있습니다. Azure AD B2C가 **consentProvidedForMinor**를 **denied**로 기록합니다.

**legalAgeGroupClassification**, **consentProvidedForMinor** 및 **ageGroup**에 대한 자세한 내용은 [사용자 리소스 종류](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user)를 참조하세요. 사용자 지정 특성에 대한 자세한 내용은 [사용자 지정 특성을 사용하여 소비자에 대한 정보를 수집합니다.](user-flow-custom-attributes.md)를 참조하세요. Azure AD Graph API를 사용하여 확장된 특성을 처리하는 경우 긴 버전의 특성(예: *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*)을 사용해야 합니다.

## <a name="gather-date-of-birth-and-countryregion-data"></a>생년월일 및 국가/지역 데이터 수집

응용 프로그램은 등록 하는 동안 Azure AD B2C를 사용 하 여 모든 사용자의 생년월일 (DOB) 및 국가/지역 정보를 수집할 수 있습니다. 이 정보가 아직 없으면 애플리케이션에서 다음 인증(로그인) 프로세스 중에 사용자에게 요청할 수 있습니다. 사용자는 DOB 및 국가/지역 정보를 제공 하지 않고 진행할 수 없습니다. Azure AD B2C는 정보를 사용 하 여 해당 국가/지역의 규정 표준에 따라 개인을 부로 간주할지 여부를 결정 합니다.

사용자 지정 된 사용자 흐름은 DOB 및 국가/지역 정보를 수집 하 고 Azure AD B2C 클레임 변환을 사용 하 여 **Agegroup** 을 확인 하 고 결과를 유지 (또는 dob 및 국가/지역 정보를 직접 유지) 할 수 있습니다.

다음 단계에서는 사용자의 생년월일에서 **ageGroup**을 계산하는 데 사용되는 논리를 보여 줍니다.

1. 목록에 있는 국가 코드로 국가를 찾습니다. 해당 국가를 찾을 수 없는 경우 **기본값**으로 되돌립니다.

2. **MinorConsent** 노드가 country 요소에 있는 경우:

    a. 성인으로 간주되기 위해 사용자가 출생한 날짜를 계산합니다. 예를 들어 현재 날짜가 2015년 3월 14일이고 **MinorConsent**가 18인 경우 생년월일은 2000년 3월 14일 이전이어야 합니다.

    b. 최소 생년월일과 실제 생년월일을 비교합니다. 최소 생년월일이 사용자 생년월일 이전이면, 계산은 연령 그룹 계산으로 **Minor**를 반환합니다.

3. **MinorNoConsentRequired** 노드가 country 요소에 있으면, **MinorNoConsentRequired**의 값을 사용하여 2a 및 2b 단계를 반복합니다. 최소 생년월일이 사용자 생년월일 이전이면 2b의 출력은 **MinorNoConsentRequired**를 반환합니다.

4. 두 계산에서 모두 true를 반환하지 않으면 **Adult**를 반환합니다.

응용 프로그램에서 다른 방법으로 DOB 또는 국가/지역 데이터를 안정적으로 수집한 경우 응용 프로그램은 Graph API를 사용 하 여이 정보로 사용자 레코드를 업데이트할 수 있습니다. 예:

- 사용자가 성인으로 알려지면 디렉터리 특성인 **ageGroup**의 값을 **Adult**로 업데이트합니다.
- 사용자가 미성년자인 것으로 확인되면 **ageGroup** 디렉터리 특성을 **Minor** 값으로 업데이트하고 **consentProvidedForMinor**를 적절하게 설정합니다.

DOB 데이터 수집에 대한 자세한 내용은 [Azure AD B2C에서 나이 게이팅 사용](basic-age-gating.md)을 참조하세요.

## <a name="capture-terms-of-use-agreement"></a>사용 약관 규약 포착

애플리케이션을 개발할 때 일반적으로 애플리케이션 내에서 사용자 디렉터리에 개입하지 않거나 약간만 개입하여 사용 약관에 대한 사용자의 동의를 캡처합니다. 하지만 Azure AD B2C 사용자 흐름을 사용하여 사용 약관에 대한 사용자의 동의를 수집하고, 동의가 승인되지 않은 경우 액세스를 제한하며, 마지막 승인 날짜 및 사용 약관의 최신 버전 날짜에 따라 이후의 사용 약관에 대한 변경 내용을 수락하도록 적용할 수 있습니다.

**사용 약관**에는 “타사와 데이터 공유에 대한 동의”를 포함할 수도 있습니다. 지역 규정 및 비즈니스 규칙에 따라 두 조건을 결합한 사용자의 동의를 수집하거나 사용자가 두 조건 중 하나를 수락하도록 허용할 수 있습니다.

다음 단계에서는 사용 약관을 관리하는 방법에 대해 설명합니다.

1. Graph API 및 확장 특성을 사용하여 사용 약관에 대한 수락 및 수락 날짜를 기록합니다. 기본 제공 및 사용자 지정 사용자 흐름을 모두 사용하여 이 작업을 수행할 수 있습니다. **extension_termsOfUseConsentDateTime** 및 **extension_termsOfUseConsentVersion** 특성을 만들어 사용하는 것이 좋습니다.

2. "사용 약관 동의"라는 필수 확인란을 만들고, 등록하는 동안 결과를 기록합니다. 기본 제공 및 사용자 지정 사용자 흐름을 모두 사용하여 이 작업을 수행할 수 있습니다.

3. Azure AD B2C에서 사용 약관 계약 및 사용자의 동의를 저장합니다. Graph API를 사용하여 응답을 기록하는 데 사용되는 확장 특성을 읽어서 사용자의 상태(예: **termsOfUseTestUpdateDateTime**)를 쿼리할 수 있습니다. 기본 제공 및 사용자 지정 사용자 흐름을 모두 사용하여 이 작업을 수행할 수 있습니다.

4. 수락 날짜와 사용 약관의 최신 버전 날짜를 비교하여 업데이트된 사용 약관에 대한 동의를 요구합니다. 사용자 지정 사용자 흐름을 사용하는 경우에만 날짜를 비교할 수 있습니다. **extension_termsOfUseConsentDateTime** 확장 특성을 사용하고, 이 값을 **termsOfUseTextUpdateDateTime** 클레임과 비교합니다. 수락 날짜가 오래되었으면 자체 어설션된 화면을 표시하여 새로운 수락을 강제합니다. 그렇지 않으면 정책 논리를 사용하여 액세스를 차단합니다.

5. 수락 버전 번호를 마지막으로 수락한 버전 번호와 비교하여 업데이트된 사용 약관에 대한 동의를 요구합니다. 사용자 지정 사용자 흐름을 사용하는 경우에만 버전 번호를 비교할 수 있습니다. **extension_termsOfUseConsentDateTime** 확장 특성을 사용하고, 이 값을 **extension_termsOfUseConsentVersion** 클레임과 비교합니다. 수락 날짜가 오래되었으면 자체 어설션된 화면을 표시하여 새로운 수락을 강제합니다. 그렇지 않으면 정책 논리를 사용하여 액세스를 차단합니다.

다음 시나리오에서는 사용 약관 동의를 캡처할 수 있습니다.

- 새로운 사용자가 등록 중입니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.
- 사용자가 이전에 계약의 최신 또는 활성 사용 약관에 동의한 사용자로 로그인합니다. 사용 약관이 표시되지 않습니다.
- 사용자가 최신 또는 활성 사용 약관에 아직 동의하지 않은 사용자로 로그인합니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.
- 사용자가 이전 버전의 사용 약관에 이미 동의한 사용자가 로그인하고, 이 사용 약관은 이제 최신 버전으로 업데이트됩니다. 사용 약관이 표시되고 동의 결과가 저장됩니다.

다음 이미지에서는 권장되는 사용자 흐름을 보여줍니다.

![권장 승인 사용자 흐름을 보여 주는 순서도 다이어그램](./media/manage-user-access/user-flow.png)

아래는 클레임의 DateTime 기반 사용 약관 동의의 예입니다.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

아래는 클레임의 Version 기반 사용 약관 동의의 예입니다.

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>다음 단계

- 사용자 데이터를 삭제하고 내보내는 방법을 알아보려면 [사용자 데이터 관리](manage-user-data.md)를 참조하세요.
- 사용 약관 프롬프트를 구현 하는 예제 사용자 지정 정책에 대한 자세한 내용은 [B2C IEF 사용자 지정 정책-등록 하 고 ' 사용 약관 ' 프롬프트를 사용 하 여 로그인](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou)을 참조 하세요.
