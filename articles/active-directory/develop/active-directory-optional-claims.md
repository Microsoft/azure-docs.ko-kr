---
title: Azure AD 앱에 선택적 클레임 제공
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 발급 한 SAML 2.0 및 JWT (JSON 웹 토큰) 토큰에 사용자 지정 또는 추가 클레임을 추가 하는 방법입니다.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: e53cf38c9544884caddfdf03c2615217c49ec3d0
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068729"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>방법: 앱에 선택적 클레임 제공

애플리케이션 개발자는 Azure AD 애플리케이션에서 선택적 클레임을 사용하여 애플리케이션으로 전송된 토큰에서 원하는 클레임을 지정할 수 있습니다.

선택적 클레임을 사용하여 다음을 수행할 수 있습니다.

- 애플리케이션에 대한 토큰에 포함할 추가 클레임을 선택합니다.
- Microsoft id 플랫폼이 토큰에서 반환 하는 특정 클레임의 동작을 변경 합니다.
- 애플리케이션에 대한 사용자 지정 클레임을 추가하고 액세스합니다.

표준 클레임 목록은 [access token](access-tokens.md) 및 [ID_token](id-tokens.md) 클레임 설명서를 참조하세요.

선택적 클레임은 v1.0 및 v2.0 형식 토큰 뿐만 아니라 SAML 토큰에서도 지원되지만, v1.0에서 v2.0으로 전환할 때 대부분의 값을 제공합니다. [v2.0 Microsoft ID 플랫폼 엔드포인트](./v2-overview.md)의 목표 중 하나는 클라이언트의 최적 성능을 보장하기 위해 토큰 크기를 좀 더 작게 유지하는 것입니다. 따라서, 이전에 액세스 및 ID 토큰에 포함되어 있던 일부 클레임이 v 2.0 토큰에는 더 이상 존재하지 않으며, 애플리케이션 기준으로 특수하게 요청되어야 합니다.

**표 1: 적용 가능성**

| 계정 유형               | v1.0 토큰 | v2.0 토큰 |
|----------------------------|-------------|-------------|
| 개인 Microsoft 계정 | 해당 없음         | 지원됨   |
| Azure AD 계정           | 지원됨   | 지원됨   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 및 v2.0 선택적 클레임 세트

기본적으로 애플리케이션에서 사용할 수 있는 선택적 클레임의 집합은 아래와 같습니다. 애플리케이션에 대한 선택적 사용자 지정 클레임을 추가하려면 아래의 [디렉터리 확장](#configuring-directory-extension-optional-claims)을 참조하세요. **액세스 토큰**에 클레임을 추가하면 클레임은 애플리케이션(Web API)*이* 요청한 클레임이 아니라 애플리케이션*용으로* 요청한 액세스 토큰에 적용됩니다. 클라이언트가 어떤 방식으로 API에 액세스하는지에 관계없이 클라이언트가 API에 인증을 하는 데 사용하는 액세스 토큰에는 항상 올바른 데이터가 포함됩니다.

> [!NOTE]
> 이러한 클레임 대부분은 토큰 유형 열에 명시된 경우를 제외하고 SAML 토큰이 아닌 v1.0 및 v2.0 토큰에 대한 JWT에 포함될 수 있습니다. 소비자 계정은 "사용자 유형" 열에 표시된 이러한 클레임의 일부를 지원합니다.  나열된 많은 클레임은 소비자 사용자에게 적용되지 않습니다(테넌트가 없으므로 `tenant_ctry`에 값이 없음).

**표 2: v1.0 및 v2.0 선택적 클레임 세트**

| Name                       |  Description   | 토큰 형식 | 사용자 유형 | 메모  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | 사용자가 마지막으로 인증받은 시간입니다. OpenID Connect 사양을 참조하세요.| JWT        |           |  |
| `tenant_region_scope`      | 리소스 테넌트의 지역입니다. | JWT        |           | |
| `sid`                      | 세션 단위 사용자 로그아웃에 사용되는 세션 ID | JWT        |  개인 및 Azure AD 계정   |         |
| `verified_primary_email`   | 사용자의 PrimaryAuthoritativeEmail에서 소싱됩니다.      | JWT        |           |         |
| `verified_secondary_email` | 사용자의 SecondaryAuthoritativeEmail에서 소싱됩니다.   | JWT        |           |        |
| `vnet`                     | VNET 지정자 정보입니다. | JWT        |           |      |
| `fwd`                      | IP 주소입니다.| JWT    |   | 요청 클라이언트의 원래 IPv4 주소를 추가합니다(VNET 내에 있는 경우). |
| `ctry`                     | 사용자의 국가/지역 | JWT, SAML |  | Azure AD는 `ctry` 선택적 클레임 (있는 경우)을 반환 하 고 필드의 값은 fr-fr, JP, SZ 등의 표준 2 자로 된 국가/지역 코드입니다. |
| `tenant_ctry`              | 리소스 테 넌 트의 국가 | JWT | | `ctry`관리자가 테 넌 트 수준에서 설정 하는 것과 동일 합니다.  또한은 표준 두 문자로 된 값 이어야 합니다. |
| `xms_pdl`             | 기본 설정 데이터 위치   | JWT | | 다중 지역 테넌트의 경우 기본 데이터 위치는 사용자가 거주하는 지리적 지역을 나타내는 세 문자로 된 코드입니다. 자세한 내용은 [기본 설정 데이터 위치에 대한 Azure AD Connect 설명서](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md)를 참조합니다.<br/>예를 들어 아시아 태평양의 경우 `APC`입니다. |
| `xms_pl`                   | 사용자 기본 설정 언어  | JWT ||설정되는 경우 사용자의 기본 설정 언어입니다. 게스트 액세스 시나리오에서 해당 홈 테넌트의 원본 위치입니다. 형식이 지정된 LL-CC("en-us")입니다. |
| `xms_tpl`                  | 테넌트 기본 설정 언어| JWT | | 설정된 경우 리소스 테넌트의 기본 설정 언어입니다. 형식이 지정된 LL("en")입니다. |
| `ztdid`                    | 무인 배포 ID | JWT | | [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot)에 사용된 디바이스 ID |
| `email`                    | 사용자가 있는 경우 이 사용자에 대한 이메일 주소를 지정할 수 있습니다.  | JWT, SAML | MSA, Azure AD | 이 값은 사용자가 테넌트의 게스트인 경우 기본적으로 포함됩니다.  관리되는 사용자(테넌트 내부 사용자)의 경우 이 선택적 클레임 또는 v2.0에서만 OpenID 범위를 통해 요청해야 합니다.  관리되는 사용자의 경우 이메일 주소는 [Office 관리 포털](https://portal.office.com/adminportal/home#/users)에서 설정해야 합니다.|
| `acct`                | 테 넌 트의 사용자 계정 상태 | JWT, SAML | | 사용자가 테넌트의 구성원인 경우 값은 `0`입니다. 게스트인 경우 값은 `1`입니다. |
| `groups`| 그룹 클레임에 대한 선택적 서식 지정 |JWT, SAML| |함께 설정해야 하는 [애플리케이션 매니페스트](reference-app-manifest.md)의 GroupMembershipClaims 설정과 함께 사용됩니다. 자세한 내용은 아래 [그룹 클레임](#configuring-groups-optional-claims)을 참조하세요. 그룹 클레임에 대한 자세한 내용은 [그룹 클레임을 구성하는 방법](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.
| `upn`                      | UserPrincipalName | JWT, SAML  |           | 이 클레임은 자동으로 포함되지만, 추가 속성을 연결하여 게스트 사용자 사례에서 해당 동작을 수정하기 위해 선택적 클레임으로 지정할 수 있습니다.  |
| `idtyp`                    | 토큰 형식   | JWT 액세스 토큰 | 특수: 앱 전용 액세스 토큰에만 |  `app`토큰이 앱 전용 토큰 인 경우 값은입니다. 이것은 API에서 토큰이 앱 토큰 인지 앱 + 사용자 토큰 인지를 확인 하는 가장 정확한 방법입니다.|

## <a name="v20-specific-optional-claims-set"></a>v2.0 특정 선택적 클레임 세트

이러한 클레임은 항상 v1.0 Azure AD 토큰에 포함되지만, 요청되지 않을 경우 v2.0 토큰에 포함되지 않습니다. 이러한 클레임은 JWT(ID 토큰 및 액세스 토큰)에 대해서만 적용 가능합니다.

**표 3: v2.0 전용 선택적 클레임**

| JWT 클레임     | Name                            | 설명                                | 메모 |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP 주소                      | 클라이언트가 로그인한 IP 주소입니다.   |       |
| `onprem_sid`  | 온-프레미스 보안 식별자 |                                             |       |
| `pwd_exp`     | 암호 만료 시간        | 암호가 만료되는 날짜/시간입니다. |       |
| `pwd_url`     | 암호 변경 URL             | 사용자가 암호 변경을 위해 방문할 수 있는 URL입니다.   |   |
| `in_corp`     | 기업 네트워크 내부        | 클라이언트가 회사 네트워크에서 로그인하는 경우 알립니다. 그러지 않으면 클레임이 포함되지 않습니다.   |  MFA의 [신뢰할 수 있는 IP](../authentication/howto-mfa-mfasettings.md#trusted-ips)를 기반으로 합니다.    |
| `family_name` | 성                       | 사용자 개체에 정의된 사용자의 성을 제공합니다. <br>"family_name":"Miller" | MSA 및 Azure AD에서 지원됩니다. `profile` 범위가 필요합니다.   |
| `given_name`  | 이름                      | 사용자 개체에 설정된 대로 사용자의 이름 또는 "성"을 제공합니다.<br>"given_name": "Frank"                   | MSA 및 Azure AD에서 지원됩니다.  `profile` 범위가 필요합니다. |
| `upn`         | 사용자 계정 이름 | username_hint 매개 변수와 함께 사용할 수 있는 사용자에 식별자입니다.  사용자에 대한 지속형 식별자가 아니며 키 데이터에 사용할 수 없습니다. | 클레임의 구성에 대해서는 아래 [추가 속성](#additional-properties-of-optional-claims)을 참조하세요. `profile` 범위가 필요합니다.|

### <a name="additional-properties-of-optional-claims"></a>선택적 클레임의 추가 속성

일부 선택적 클레임은 클레임이 반환되는 방식을 변경하도록 구성할 수 있습니다. 대개 이러한 추가 속성을 사용하면 다른 데이터 기대가 포함된 온-프레미스 애플리케이션을 마이그레이션하는 데 도움이 됩니다(예: `include_externally_authenticated_upn_without_hash`는 UPN에서 해시 표시(`#`)를 처리할 수 없는 클라이언트에 유용).

**표 4: 선택적 클레임 구성을 위한 값**

| 속성 이름  | 추가 속성 이름 | Description |
|----------------|--------------------------|-------------|
| `upn`          |                          | SAML 및 JWT 응답과 v1.0 및 v2.0 토큰 모두에 사용할 수 있습니다. |
|                | `include_externally_authenticated_upn`  | 리소스 테넌트에 저장된 게스트 UPN을 포함합니다. 예를 들어 `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | 해시 표시(`#`)가 밑줄(`_`)로 바뀐다는 점 외에는 위와 같습니다(예: `foo_hometenant.com_EXT_@resourcetenant.com`). |

#### <a name="additional-properties-example"></a>추가 속성 예제

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

이 OptionalClaims 개체를 통해 ID 토큰이 클라이언트에 반환되어 추가 홈 테넌트 및 리소스 테넌트 정보와 함께 UPN 클레임을 포함하게 됩니다. 사용자가 인증에 대해 다른 IDP를 사용하는 테넌트의 게스트인 경우 토큰에서 `upn` 클레임만 변경됩니다.

## <a name="configuring-optional-claims"></a>선택적 클레임 구성

> [!IMPORTANT]
> 액세스 토큰은 **항상** 클라이언트가 아닌 리소스의 매니페스트를 사용하여 생성됩니다.  따라서 요청 `...scope=https://graph.microsoft.com/user.read...`에서 리소스는 Microsoft Graph API입니다.  따라서 액세스 토큰은 클라이언트의 매니페스트가 아닌 Microsoft Graph API 매니페스트를 사용하여 생성됩니다.  애플리케이션에 대한 매니페스트를 변경해도 Microsoft Graph API에 대한 토큰이 다르게 보이지 않습니다.  `accessToken` 변경 내용이 적용되는지 확인하기 위해 다른 앱이 아닌 애플리케이션에 대한 토큰을 요청합니다.

UI 또는 애플리케이션 매니페스트를 통해 애플리케이션에 대한 선택적 클레임을 구성할 수 있습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다. **Azure Active Directory**를 검색하고 선택합니다.
1. **관리** 섹션에서 **앱 등록**을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.

**UI를 통해 선택적 클레임 구성:**

[![UI를 사용하여 선택적 클레임을 구성하는 방법을 보여 줍니다](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png).

1. **관리** 섹션에서 **토큰 구성**을 선택합니다.
1. **선택적 클레임 추가**를 선택합니다.
1. 구성하려는 토큰 유형을 선택합니다.
1. 추가할 선택적 클레임을 선택합니다.
1. **추가**를 선택합니다.

**애플리케이션 매니페스트를 통해 선택적 클레임 구성:**

[![앱 매니페스트를 사용하여 선택적 클레임을 구성하는 방법을 보여 줍니다](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png).

1. **관리** 섹션에서 **매니페스트**를 선택합니다. 웹 기반 매니페스트 편집기가 열리면 매니페스트를 편집할 수 있습니다. 필요에 따라 **다운로드**를 선택하고 로컬로 매니페스트를 편집하고 **업로드**를 사용하여 애플리케이션에 다시 적용할 수 있습니다. 애플리케이션 매니페스트에 대한 자세한 내용은 [Azure AD 애플리케이션 매니페스트 이해 문서](reference-app-manifest.md)를 참조하세요.

    다음 애플리케이션 매니페스트 항목은 ID, 액세스 및 SAML 토큰에 auth_time, ipaddr 및 upn 선택적 클레임을 추가합니다.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. 작업을 마쳤으면 **저장**을 선택합니다. 이제 지정된 선택적 클레임이 애플리케이션의 토큰에 포함됩니다.

### <a name="optionalclaims-type"></a>OptionalClaims 형식

애플리케이션에서 요청한 선택적 클레임을 선언합니다. 애플리케이션은 선택적 클레임이 보안 토큰 서비스에서 수신할 수 있는 3가지 토큰 유형(ID 토큰, 액세스 토큰, SAML 2 토큰)으로 반환되도록 구성할 수 있습니다. 애플리케이션은 다른 선택적 클레임 집합이 각 토큰 유형으로 반환되도록 구성할 수 있습니다. Application 엔터티의 OptionalClaims 속성은 OptionalClaims 개체입니다.

**표 5: OptionalClaims 형식 속성**

| Name          | 유형                       | Description                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | 컬렉션(OptionalClaim) | ID JWT 토큰에서 반환된 선택적 클레임입니다.     |
| `accessToken` | 컬렉션(OptionalClaim) | JWT 액세스 토큰에서 반환된 선택적 클레임입니다. |
| `saml2Token`  | 컬렉션(OptionalClaim) | SAML 토큰에서 반환된 선택적 클레임입니다.       |

### <a name="optionalclaim-type"></a>OptionalClaim 형식

애플리케이션 또는 서비스 사용자와 연결된 선택적 클레임을 포함합니다. [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) 형식의 idToken, accessToken 및 saml2Token 속성은 OptionalClaim의 컬렉션입니다.
특정 클레임에서 지원될 경우, AdditionalProperties 필드를 사용하여 OptionalClaim의 동작을 수정할 수도 있습니다.

**표 6: OptionalClaim 형식 속성**

| Name                   | 유형                    | Description                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 선택적 클레임의 이름입니다.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 클레임의 원본(디렉터리 개체)입니다. 확장 속성에서 가져온 미리 정의된 클레임 및 사용자 정의 클레임이 있습니다. 원본 값이 null이면 클레임은 미리 정의된 선택적 클레임입니다. 원본 값이 user이면 name 속성의 값은 user 개체의 확장 속성입니다. |
| `essential`            | Edm.Boolean             | 이 값이 True이면 클라이언트가 지정한 클레임은 최종 사용자가 요청된 특정 태스크에 대한 원활한 권한 부여 환경을 보장하는 데 필요합니다. 기본값은 False입니다.                                                                                                                 |
| `additionalProperties` | 컬렉션(Edm.String) | 클레임의 추가 속성입니다. 속성이 이 컬렉션에 있으면 name 속성에 지정된 선택적 클레임의 동작을 수정합니다.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>디렉터리 확장 선택적 클레임 구성

표준 선택적 클레임 세트 외에도 확장을 포함하도록 토큰을 구성할 수 있습니다. 자세한 내용은 [Microsoft Graph extensionProperty 설명서](/graph/api/resources/extensionproperty?view=graph-rest-1.0)를 참조하세요.

스키마 및 오픈 확장은 선택적 클레임에서 지원되지 않으며, AAD-Graph 스타일 디렉터리 확장만 지원됩니다. 이 기능은 앱이 사용할 수 있는 추가 사용자 정보(예: 추가 식별자 또는 사용자가 설정한 중요 구성 옵션)를 추가하는 데 유용합니다. 예제를 보려면 페이지의 맨 아래를 참조하세요.

> [!NOTE]
> 디렉터리 스키마 확장은 Azure AD 전용 기능입니다. 프로그램 매니페스트가 사용자 지정 확장을 요청하고, MSA 사용자가 앱에 로그인하는 경우 이러한 확장이 반환되지 않습니다.

### <a name="directory-extension-formatting"></a>디렉터리 확장 서식 지정

애플리케이션 매니페스트를 사용하여 디렉터리 확장 선택적 클레임을 구성하는 경우 확장의 전체 이름(`extension_<appid>_<attributename>` 형식)을 사용합니다. `<appid>`는 클레임을 요청하는 애플리케이션의 ID와 일치해야 합니다.

JWT 내에서 이러한 클레임은 `extn.<attributename>` 이름 형식을 사용하여 내보내집니다.

SAML 토큰 내에서 이러한 클레임은 `http://schemas.microsoft.com/identity/claims/extn.<attributename>` URI 형식을 사용하여 내보내집니다.

## <a name="configuring-groups-optional-claims"></a>그룹 선택적 클레임 구성

   > [!NOTE]
   > 온-프레미스에서 동기화된 사용자 및 그룹의 그룹 이름을 내보내는 기능은 퍼블릭 미리 보기입니다.

이 섹션에서는 그룹 클레임에서 사용되는 그룹 특성을 기본 그룹 objectID에서 온-프레미스 Windows Active Directory로 동기화되는 특성으로 변경하기 위한 선택적 클레임의 구성 옵션에 대해 설명합니다. UI 또는 애플리케이션 매니페스트를 통해 애플리케이션에 대한 그룹 선택적 클레임을 구성할 수 있습니다.

> [!IMPORTANT]
> 온-프레미스 특성의 그룹 클레임에 대한 퍼블릭 미리 보기 관련 중요 주의 사항을 비롯한 자세한 내용은 [Azure AD를 사용하여 애플리케이션에 대한 그룹 클레임 구성](../hybrid/how-to-connect-fed-group-claims.md)을 참조하세요.

**UI를 통해 그룹 선택적 클레임 구성:**

1. [Azure 포털](https://portal.azure.com)
1. 인증한 후에 페이지의 오른쪽 위 모서리에서 Azure AD 테넌트를 선택합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.
1. **관리** 섹션에서 **앱 등록**을 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **토큰 구성**을 선택합니다.
1. **그룹 클레임 추가**를 선택합니다.
1. 반환할 그룹 유형(**모든 그룹**, **SecurityGroup**또는 **DirectoryRole**)을 선택합니다. **모든 그룹** 옵션에는 **SecurityGroup**, **DirectoryRole** 및 **DistributionList**가 포함됩니다.
1. 선택 사항: 특정 토큰 유형 속성을 선택하여 온-프레미스 그룹 특성을 포함하도록 그룹 클레임 값을 수정하거나 클레임 유형을 역할로 변경합니다.
1. **저장**을 선택합니다.

**애플리케이션 매니페스트를 통해 그룹 선택적 클레임 구성:**

1. [Azure 포털](https://portal.azure.com)
1. 인증한 후에 페이지의 오른쪽 위 모서리에서 Azure AD 테넌트를 선택합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 선택합니다.
1. **관리** 섹션에서 **매니페스트**를 선택합니다.
1. 매니페스트 편집기를 사용하여 다음 항목을 추가합니다.

   유효한 값은

   - "All"(includes SecurityGroup, DirectoryRole 및 DistributionList 포함)
   - "SecurityGroup"
   - "DirectoryRole"

   예를 들면 다음과 같습니다.

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   기본적으로 그룹 ObjectID는 그룹 클레임 값으로 내보내집니다.  온-프레미스 그룹 특성을 포함하도록 클레임 값을 수정하거나 클레임 유형을 역할로 변경하려면 다음과 같이 OptionalClaims 구성을 사용합니다.

1. 그룹 이름 구성 선택적 클레임을 설정합니다.

   토큰의 그룹이 선택적 클레임 섹션에 온-프레미스 AD 그룹 특성을 포함하도록 지정하려는 경우에는 선택적 클레임을 적용해야 하는 토큰 유형, 요청된 선택적 클레임의 이름 및 필요한 추가 속성을 지정합니다.  여러 토큰 유형이 나열될 수 있습니다.

   - OIDC ID 토큰에 대한 idToken
   - OAuth 액세스 토큰에 대한 accessToken
   - SAML 토큰에 대한 Saml2Token

   > [!NOTE]
   > Saml2Token 형식은 SAML 1.1 및 SAML 2.0 형식 토큰에 모두 적용됩니다.

   관련된 각 토큰 유형에 대해 매니페스트에 OptionalClaims 섹션을 사용하도록 그룹 클레임을 수정합니다. OptionalClaims 스키마는 다음과 같습니다.

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | 선택적 클레임 스키마 | 값 |
   |----------|-------------|
   | **name:** | "groups"여야 합니다. |
   | **source:** | 사용되지 않습니다. 생략하거나 Null을 지정합니다. |
   | **essential:** | 사용되지 않습니다. 생략하거나 false를 지정합니다. |
   | **additionalProperties:** | 추가 속성 목록입니다.  유효한 옵션은 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles"입니다. |

   AdditionalProperties에서는 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" 중 하나만 필요합니다.  두 가지 이상의 속성이 있으면 첫 번째 속성이 사용되고 나머지는 무시됩니다.

   일부 애플리케이션에는 역할 클레임에 사용자에 대한 그룹 정보가 필요합니다.  클레임 유형을 그룹 클레임에서 역할 클레임으로 변경하려면 추가 속성에 "emit_as_roles"를 추가합니다.  그룹 값은 역할 클레임에 내보내집니다.

   > [!NOTE]
   > "emit_as_roles"를 사용하는 경우 사용자가 할당되어 있는 모든 구성된 애플리케이션 역할이 역할 클레임에 표시되지 않습니다.

**예:**

1) 그룹을 dnsDomainName\sAMAccountName 형식을 사용하여 OAuth 액세스 토큰의 그룹 이름으로 내보냅니다.

    **UI 구성:**

    [![UI를 사용하여 선택적 클레임을 구성하는 방법을 보여 줍니다](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png).

    **애플리케이션 매니페스트 항목:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) netbiosDomain\sAMAccountName 형식으로 반환될 그룹 이름을 SAML 및 OIDC ID 토큰의 역할 클레임으로 내보냅니다.

    **UI 구성:**

    [![UI를 사용하여 선택적 클레임을 구성하는 방법을 보여 줍니다](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png).

    **애플리케이션 매니페스트 항목:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>선택적 클레임 예제

이 섹션에서는 시나리오를 진행하면서 애플리케이션에 대한 선택적 클레임 기능을 사용하는 방법을 확인할 수 있습니다.
애플리케이션 ID 구성에서 속성을 업데이트하여 선택적 클레임을 사용하도록 설정하고 구성하는 데 여러 옵션을 사용할 수 있습니다.

- **토큰 구성** UI를 사용할 수 있습니다(아래 예제 참조).
- **매니페스트**를 사용할 수 있습니다(아래 예제 참조). 먼저 매니페스트를 소개하는 [Azure AD 애플리케이션 매니페스트 이해 문서](./reference-app-manifest.md)를 읽으세요.
- 또한 [Microsoft Graph API](/graph/use-the-api?context=graph%2fapi%2f1.0&view=graph-rest-1.0)를 사용하는 애플리케이션을 작성하여 애플리케이션을 업데이트할 수 있습니다. Microsoft Graph API 참조 가이드의 [OptionalClaims](/graph/api/resources/optionalclaims?view=graph-rest-1.0) 형식은 선택적 클레임을 구성하는 데 유용할 수 있습니다.

**예:**

아래 예제에서는 **토큰 구성** UI 및 **매니페스트**를 사용하여 애플리케이션용 액세스, ID 및 SAML 토큰에 선택적 클레임을 추가합니다. 애플리케이션에서 수신할 수 있는 각 토큰 유형에 서로 다른 선택적 클레임이 추가되었습니다.

- 이제 ID 토큰에는 페더레이션 사용자의 UPN이 전체 형식으로 포함됩니다(`<upn>_<homedomain>#EXT#@<resourcedomain>`).
- 다른 클라이언트가 이 애플리케이션용으로 요청하는 액세스 토큰에는 이제 auth_time 클레임이 포함됩니다.
- SAML 토큰은 skypeId 디렉터리 스키마 확장을 포함합니다(이 예제에서 이 앱의 앱 ID는 ab603c56068041afb2f6832e2a17e237임). SAML 토큰은 Skype ID를 `extension_skypeId`로 노출합니다.

**UI 구성:**

1. [Azure 포털](https://portal.azure.com)

1. 인증한 후에 페이지의 오른쪽 위 모서리에서 Azure AD 테넌트를 선택합니다.

1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.

1. **관리** 섹션에서 **앱 등록**을 선택합니다.

1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 찾아서 선택합니다.

1. **관리** 섹션에서 **토큰 구성**을 선택합니다.

1. **선택적 클레임 추가**를 선택하고 **ID** 토큰 유형을 선택한 다음, 클레임 목록에서 **upn**을 선택하고 **추가**를 선택합니다.

1. **선택적 클레임 추가**를 선택하고 **액세스** 토큰 유형을 선택한 다음, 클레임 목록에서 **auth_time**을 선택하고 **추가**를 선택합니다.

1. 토큰 구성 개요 화면에서 **upn** 옆에 있는 연필 아이콘을 선택하고, **외부적으로 인증됨** 토글을 선택한 후 **저장**을 선택합니다.

1. **선택적 클레임 추가**를 선택하고, **SAML** 토큰 유형을 선택한 후 클레임 목록에서 **extn.skypeID**를 선택하고(skypeID라는 Azure AD 사용자 개체를 만든 경우에만 해당) **추가**를 선택합니다.

    [![UI를 사용하여 선택적 클레임을 구성하는 방법을 보여 줍니다](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png).

**매니페스트 구성:**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 인증한 후에 페이지의 오른쪽 위 모서리에서 Azure AD 테넌트를 선택합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory**를 선택합니다.
1. 목록에서 선택적 클레임을 구성하려는 애플리케이션을 찾아서 선택합니다.
1. **관리** 섹션에서 **매니페스트**를 선택하여 인라인 매니페스트 편집기를 엽니다.
1. 이 편집기를 사용하여 매니페스트를 직접 편집할 수 있습니다. 매니페스트는 [애플리케이션 엔터티](./reference-app-manifest.md)의 스키마를 따르며, 저장된 매니페스트의 서식을 자동으로 지정합니다. 새 요소가 `OptionalClaims` 속성에 추가됩니다.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. 매니페스트 업데이트가 끝나면 **저장**을 선택하여 매니페스트를 저장합니다.

## <a name="next-steps"></a>다음 단계

Azure AD에서 제공하는 표준 클레임에 대해 자세히 알아봅니다.

- [ID 토큰](id-tokens.md)
- [액세스 토큰](access-tokens.md)
