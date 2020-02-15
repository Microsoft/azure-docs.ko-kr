---
title: Azure AD 앱 SAML 토큰 클레임 사용자 지정
titleSuffix: Microsoft identity platform
description: Azure AD의 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임을 사용자 지정하는 방법을 알아보세요.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: article
ms.date: 10/22/2019
ms.author: ryanwi
ms.reviewer: luleon, paulgarn, jeedes
ms.custom: aaddev
ms.openlocfilehash: 52779b7ffea0f33676426f145a700c7181cf0bf1
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161259"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>방법: 엔터프라이즈 응용 프로그램에 대 한 SAML 토큰에서 발급 된 클레임 사용자 지정

현재 Azure Active Directory (Azure AD)는 Azure AD 앱 갤러리에 사전 통합 된 응용 프로그램과 사용자 지정 응용 프로그램을 포함 하 여 대부분의 엔터프라이즈 응용 프로그램에서 SSO (Single Sign-On)를 지원 합니다. 사용자가 SAML 2.0 프로토콜을 사용하여 Azure AD를 통해 애플리케이션을 인증하면 Azure AD는 (HTTP POST를 통해) 애플리케이션에 토큰을 보냅니다. 그런 다음 애플리케이션이 토큰의 유효성을 검사하고 사용하여 사용자 이름과 암호를 묻는 대신 사용자를 로그인합니다. 이러한 SAML 토큰에는 *클레임*이라는 사용자에 대 한 정보가 포함 되어 있습니다.

‘클레임’은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. [SAML 토큰 ](https://en.wikipedia.org/wiki/SAML_2.0)에서 이러한 데이터는 일반적으로 SAML 특성 문에 포함됩니다. 사용자 고유의 ID는 대개 이름 식별자라고도 하는 SAML Subject에 나타납니다.

기본적으로 Azure ad는 사용자를 고유 하 게 식별할 수 있는 사용자의 사용자 이름 (사용자 계정 이름이 라고도 함) 값이 포함 된 `NameIdentifier` 클레임이 포함 된 응용 프로그램에 대 한 SAML 토큰을 발급 합니다. 또한 SAML 토큰에는 사용자의 메일 주소, 이름 및 성을 포함하는 추가 클레임이 포함됩니다.

SAML 토큰이 애플리케이션에 발급한 클레임을 보거나 편집하려면 Azure Portal에서 애플리케이션을 엽니다. 그런 다음 **클레임 & 사용자 특성** 섹션을 엽니다.

![Azure Portal에서 클레임 & 사용자 특성 섹션을 엽니다.](./media/active-directory-saml-claims-customization/sso-saml-user-attributes-claims.png)

SAML 토큰에 발급된 클레임을 편집해야 할만한 두 가지 이유는 다음과 같습니다.

* 응용 프로그램을 사용 하려면 `NameIdentifier` 또는 NameID 클레임이 Azure AD에 저장 된 사용자 이름 (또는 사용자 계정 이름) 이외의 항목 이어야 합니다.
* 애플리케이션이 다른 클레임 URI 또는 클레임 값 집합을 요구하도록 작성되었습니다.

## <a name="editing-nameid"></a>NameID 편집

NameID (name identifier value)를 편집 하려면 다음을 수행 합니다.

1. **이름 식별자 값** 페이지를 엽니다.
1. 특성에 적용 하려는 특성 또는 변환을 선택 합니다. 필요에 따라 NameID 클레임에 포함할 형식을 지정할 수 있습니다.

   ![NameID (이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/saml-sso-manage-user-claims.png)

### <a name="nameid-format"></a>NameID 형식

SAML 요청에 특정 형식의 NameIDPolicy 요소가 포함 된 경우 Azure AD는 요청에 형식을 적용 합니다.

SAML 요청에 NameIDPolicy에 대 한 요소가 포함 되지 않은 경우 Azure AD는 사용자가 지정한 형식으로 NameID를 발급 합니다. 형식이 지정 되지 않은 경우 Azure AD는 선택한 클레임 원본과 연결 된 기본 원본 형식을 사용 합니다.

**이름 식별자 형식 선택** 드롭다운 목록에서 다음 옵션 중 하나를 선택할 수 있습니다.

| NameID 형식 | Description |
|---------------|-------------|
| **기본값** | Azure AD는 기본 원본 형식을 사용 합니다. |
| **지속적** | Azure AD는 NameID 형식으로 영구를 사용 합니다. |
| **EmailAddress** | Azure AD는 EmailAddress을 NameID 형식으로 사용 합니다. |
| **지정 되지 않은** | Azure AD는 NameID 형식으로 지정 되지 않은를 사용 합니다. |
| **Windows 도메인 정규화 된 이름** | Azure AD는 WindowsDomainQualifiedName을 NameID 형식으로 사용 합니다. |

임시 NameID도 지원 되지만 드롭다운에서 사용할 수 없으며 Azure 측에서 구성할 수 없습니다. NameIDPolicy 특성에 대해 자세히 알아보려면 [Single SIGN-ON SAML 프로토콜](single-sign-on-saml-protocol.md)을 참조 하세요.

### <a name="attributes"></a>특성

`NameIdentifier`(또는 NameID) 클레임의 원하는 소스를 선택합니다. 다음 옵션 중에서 선택할 수 있습니다.

| 속성 | Description |
|------|-------------|
| Email | 사용자의 이메일 주소입니다. |
| userprincipalName | 사용자의 UPN (사용자 계정 이름) |
| onpremisessamaccount | 온-프레미스 Azure AD에서 동기화된 SAM 계정 이름입니다. |
| objectid | Azure AD에서 사용자의 Objectid |
| employeeid | 사용자의 직원 ID |
| 디렉터리 확장 | [Azure AD Connect 동기화를 사용하여 온-프레미스 Active Directory에서 동기화되는](../hybrid/how-to-connect-sync-feature-directory-extensions.md) 디렉터리 확장입니다. |
| 확장 특성 1-15 | Azure AD 스키마를 확장하는 데 사용되는 온-프레미스 확장 특성입니다. |

자세한 내용은 [표 3: 원본 당 유효한 ID 값](active-directory-claims-mapping.md#table-3-valid-id-values-per-source)을 참조 하세요.

또한 Azure AD에서 정의한 모든 클레임에 상수 (정적) 값을 할당할 수 있습니다. 상수 값을 할당 하려면 다음 단계를 수행 하세요.

1. [Azure Portal](https://portal.azure.com/)의 **사용자 특성 & 클레임** 섹션에서 **편집** 아이콘을 클릭 하 여 클레임을 편집 합니다.

1. 수정 하려는 필수 클레임을 클릭 합니다.

1. 조직 마다 **원본 특성** 에 따옴표 없이 상수 값을 입력 하 고 **저장**을 클릭 합니다.

    ![Azure Portal에서 클레임 & 사용자 특성 섹션을 엽니다.](./media/active-directory-saml-claims-customization/organization-attribute.png)

1. 상수 값은 아래와 같이 표시 됩니다.

    ![Azure Portal에서 클레임 & 사용자 특성 섹션을 엽니다.](./media/active-directory-saml-claims-customization/edit-attributes-claims.png)

### <a name="special-claims---transformations"></a>특수 클레임-변환

클레임 변환 함수를 사용할 수도 있습니다.

| 함수 | Description |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거 합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join ()** | 확인된 도메인에 특성을 조인합니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |

## <a name="adding-application-specific-claims"></a>응용 프로그램 관련 클레임 추가

응용 프로그램 관련 클레임을 추가 하려면:

1. **클레임 & 사용자 특성**에서 **새 클레임 추가** 를 선택 하 여 **사용자 클레임 관리** 페이지를 엽니다.
1. 클레임의 **이름을** 입력 합니다. 값은 SAML 사양에 따라 URI 패턴을 따를 필요가 없습니다. URI 패턴이 필요한 경우 **네임 스페이스** 필드에 추가할 수 있습니다.
1. 클레임에서 값을 검색할 **원본을** 선택 합니다. 원본 특성 드롭다운에서 사용자 특성을 선택 하거나 클레임으로 내보내기 전에 사용자 특성에 변환을 적용할 수 있습니다.

### <a name="claim-transformations"></a>클레임 변환

사용자 특성에 변환을 적용 하려면 다음을 수행 합니다.

1. **클레임 관리**에서 클레임 원본으로 *변환* 을 선택 하 여 **변환 관리** 페이지를 엽니다.
2. 변환 드롭다운에서 함수를 선택 합니다. 선택한 함수에 따라 변환에서 평가할 상수 값과 매개 변수를 제공 해야 합니다. 사용 가능한 함수에 대 한 자세한 내용은 아래 표를 참조 하세요.
3. 여러 변환을 적용 하려면 **변환 추가**를 클릭 합니다. 클레임에 최대 2 개의 변환을 적용할 수 있습니다. 예를 들어 `user.mail`의 전자 메일 접두사를 먼저 추출할 수 있습니다. 그런 다음 문자열을 대문자로 만듭니다.

   ![NameID (이름 식별자) 값 편집](./media/active-directory-saml-claims-customization/sso-saml-multiple-claims-transformation.png)

다음 함수를 사용 하 여 클레임을 변환할 수 있습니다.

| 함수 | Description |
|----------|-------------|
| **ExtractMailPrefix()** | 전자 메일 주소 또는 사용자 계정 이름에서 도메인 접미사를 제거 합니다. 그러면 전달되는 사용자 이름의 첫 부분만 추출됩니다(예: joe_smith@contoso.com 대신 "joe_smith"). |
| **Join ()** | 두 특성을 조인 하 여 새 값을 만듭니다. 필요에 따라 두 특성 사이에 구분 기호를 사용할 수 있습니다. NameID 클레임 변환의 경우 조인은 확인 된 도메인으로 제한 됩니다. 선택한 사용자 식별자 값에 도메인이 있으면 사용자 이름을 추출하여 선택한 확인된 도메인을 추가합니다. 예를 들어, 사용자 식별자 값으로 이메일(joe_smith@contoso.com)을 선택하고 확인된 도메인으로 contoso.onmicrosoft.com을 선택하면 joe_smith@contoso.onmicrosoft.com이 됩니다. |
| **ToLower()** | 선택한 특성의 문자를 소문자로 변환합니다. |
| **ToUpper()** | 선택한 특성의 문자를 대문자로 변환합니다. |
| **Contains ()** | 입력이 지정 된 값과 일치 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 "@contoso.com" 도메인을 포함 하는 경우 값이 사용자의 전자 메일 주소인 클레임을 내보내려면이 고, 그렇지 않으면 사용자 계정 이름을 출력 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. email<br/>*값*: "@contoso.com"<br/>매개 변수 2 (출력): user. email<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. userprincipalname |
| **EndWith ()** | 입력이 지정 된 값으로 끝나는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 직원 ID가 "000"으로 끝나는 경우 값이 사용자의 직원 ID 인 클레임을 내보내려면이 고, 그렇지 않으면 확장 특성을 출력 하는 것입니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. employeeid<br/>*값*: "000"<br/>매개 변수 2 (출력): user. employeeid<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. extensionattribute1 |
| **StartWith ()** | 입력이 지정 된 값으로 시작 하는 경우 특성 또는 상수를 출력 합니다. 그렇지 않으면 일치 하는 항목이 없는 경우 다른 출력을 지정할 수 있습니다.<br/>예를 들어 국가/지역이 "US"로 시작 하는 경우 값이 사용자의 직원 ID 인 클레임을 내보내려면이 고, 그렇지 않으면 확장 특성을 출력 하는 것입니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>*매개 변수 1 (입력)* : user. country<br/>*값*: "US"<br/>매개 변수 2 (출력): user. employeeid<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. extensionattribute1 |
| **Extract ()-일치 후** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "Finance_BSimon" 이면 일치 하는 값은 "Finance_"이 고 클레임의 출력은 "따르면"입니다. |
| **Extract ()-일치 하기 전에** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "BSimon_US" 이면 일치 하는 값은 "_US"이 고 클레임의 출력은 "따르면"입니다. |
| **Extract ()-일치 항목 사이** | 지정 된 값과 일치 하는 부분 문자열을 반환 합니다.<br/>예를 들어 입력 값이 "Finance_BSimon_US" 이면 첫 번째 일치 값이 "Finance_"이 고 두 번째 일치 값은 "_US"이 고 클레임의 출력은 "따르면"입니다. |
| **ExtractAlpha ()-접두사** | 문자열의 접두사 사전순 부분을 반환 합니다.<br/>예를 들어 입력 값이 "BSimon_123" 인 경우 "따르면"를 반환 합니다. |
| **ExtractAlpha ()-접미사** | 문자열의 접미사 사전순 부분을 반환 합니다.<br/>예를 들어 입력 값이 "123_Simon" 인 경우 "Simon"를 반환 합니다. |
| **ExtractNumeric ()-접두사** | 문자열의 접두사 숫자 부분을 반환 합니다.<br/>예를 들어 입력 값이 "123_BSimon" 이면 "123"를 반환 합니다. |
| **ExtractNumeric ()-접미사** | 문자열의 접미사 숫자 부분을 반환 합니다.<br/>예를 들어 입력 값이 "BSimon_123" 이면 "123"를 반환 합니다. |
| **IfEmpty ()** | 입력이 null 이거나 비어 있는 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어 지정 된 사용자에 대 한 직원 ID가 비어 있는 경우 extensionattribute에 저장 된 특성을 출력 하려고 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>매개 변수 1 (입력): user. employeeid<br/>매개 변수 2 (출력): extensionattribute1<br/>매개 변수 3 (일치 하지 않는 경우 출력): user. employeeid |
| **IfNotEmpty ()** | 입력이 null 이거나 비어 있지 않은 경우 특성 또는 상수를 출력 합니다.<br/>예를 들어 지정 된 사용자에 대 한 직원 ID가 비어 있지 않은 경우 extensionattribute에 저장 된 특성을 출력 하려고 합니다. 이렇게 하려면 다음 값을 구성 합니다.<br/>매개 변수 1 (입력): user. employeeid<br/>매개 변수 2 (출력): extensionattribute1 |

추가 변환이 필요한 경우 [AZURE AD의 사용자 의견 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=160599) 에서 *SaaS 응용 프로그램* 범주에 따라 아이디어를 제출 합니다.

## <a name="emitting-claims-based-on-conditions"></a>조건에 따라 클레임 내보내기

사용자 유형 및 사용자가 속한 그룹을 기반으로 하는 클레임의 원본을 지정할 수 있습니다. 

사용자 형식은 다음과 같을 수 있습니다.
- **Any**: 모든 사용자가 응용 프로그램에 액세스할 수 있습니다.
- **멤버**: 테 넌 트의 네이티브 멤버
- **모든 게스트**: Azure AD를 사용 하거나 사용 하지 않고 외부 조직에서 사용자를 가져옵니다.
- **AAD 게스트**: 게스트 사용자는 Azure AD를 사용 하는 다른 조직에 속합니다.
- **외부 게스트**: 게스트 사용자는 Azure AD가 없는 외부 조직에 속합니다.


이에 대 한 한 가지 시나리오는 클레임의 원본이 응용 프로그램에 액세스 하는 게스트 및 직원과 다른 경우입니다. 사용자가 직원 인 경우 NameID가 user. email에서 원본으로 사용 되도록 지정 하는 것이 좋습니다. 그러나 사용자가 게스트가 면 NameID가 extensionattribute1에서 원본으로 사용 됩니다.

클레임 조건을 추가 하려면 다음을 수행 합니다.

1. **클레임 관리**에서 클레임 조건을 확장 합니다.
2. 사용자 유형을 선택 합니다.
3. 사용자가 속해야 하는 그룹을 선택 합니다. 지정 된 응용 프로그램에 대 한 모든 클레임에서 최대 10 개의 고유 그룹을 선택할 수 있습니다. 
4. 클레임에서 값을 검색할 **원본을** 선택 합니다. 원본 특성 드롭다운에서 사용자 특성을 선택 하거나 클레임으로 내보내기 전에 사용자 특성에 변환을 적용할 수 있습니다.

조건을 추가 하는 순서는 중요 합니다. Azure AD는 클레임에서 내보낼 값을 결정 하기 위해 위에서 아래로의 조건을 평가 합니다. 

예를 들어 Brita Simon는 Contoso 테 넌 트의 게스트 사용자입니다. 또한 Azure AD를 사용 하는 다른 조직에 속합니다. Fabrikam 응용 프로그램에 대 한 아래 구성을 고려 하 여 Brita가 Fabrikam에 로그인 하려고 하면 Azure AD는 다음과 같이 조건을 평가 합니다.

첫째, Azure AD는 Brita의 사용자 형식이 `All guests`인지 확인 합니다. 이는 true 이므로 Azure AD는 클레임에 대 한 소스를 `user.extensionattribute1`에 할당 합니다. 두 번째로, Azure AD는 Brita의 사용자 형식이 `AAD guests`인지 확인 합니다 .이는 true 이기도 하므로 Azure AD는 클레임에 대 한 소스를 `user.mail`할당 합니다. 마지막으로 클레임은 Brita의 값 `user.email`로 내보내집니다.

![클레임 조건부 구성](./media/active-directory-saml-claims-customization/sso-saml-user-conditional-claims.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure AD 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SAML 기반 Single Sign-On 문제 해결](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
