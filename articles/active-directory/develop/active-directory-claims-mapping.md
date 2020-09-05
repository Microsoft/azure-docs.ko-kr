---
title: Azure AD 테넌트 앱 클레임 사용자 지정(PowerShell)
titleSuffix: Microsoft identity platform
description: 이 페이지에서는 Azure Active Directory 클레임 매핑을 설명합니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e1c931b37cbe155d62aaffe47e36d84afa547638
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068646"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>방법: 테넌트의 특정 앱용 토큰에 내보내는 클레임 사용자 지정(미리 보기)

> [!NOTE]
> 이 기능은 현재 포털을 통해 제공되는 [클레임 사용자 지정](active-directory-saml-claims-customization.md)을 바꾸고 대체합니다. 이 문서에 자세히 설명된 Graph/PowerShell 방법과 함께 포털을 사용하여 동일한 애플리케이션에서 클레임을 사용자 지정하는 경우 해당 애플리케이션용으로 발급된 토큰은 포털의 구성을 무시합니다. 이 문서에 설명된 방법을 통해 만들어진 구성은 포털에서 반영되지 않습니다.

이 기능은 테넌트 관리자가 테넌트의 특정 애플리케이션에 대한 토큰에 내보내지는 클레임을 사용자 지정하는 데 사용됩니다. 클레임 매핑 정책을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 토큰에 포함할 클레임을 선택합니다.
- 아직 존재하지 않는 클레임 형식을 만듭니다.
- 특정 클레임에 내보내지는 데이터 원본을 선택하거나 변경합니다.

> [!NOTE]
> 이 기능은 현재 공개 미리 보기로 제공되고 있습니다. 변경 내용을 되돌리거나 제거할 준비를 해야 합니다. 이 기능은 공개 미리 보기 동안 모든 Azure AD(Azure Active Directory) 구독에서 사용할 수 있습니다. 그러나 기능이 일반 공급되면 일부 기능에는 Azure AD Premium 구독이 필요할 수도 있습니다. 이 기능은 WS-Fed, SAML, OAuth 및 OpenID Connect 프로토콜용 클레임 매핑 정책 구성을 지원합니다.

## <a name="claims-mapping-policy-type"></a>클레임 매핑 정책 유형

Azure AD에서 **정책** 개체는 조직에 있는 개별 애플리케이션 또는 모든 애플리케이션에 적용되는 규칙 집합을 나타냅니다. 각 유형의 정책에는 할당된 개체에 적용되는 속성 집합이 포함된 고유한 구조가 있습니다.

클레임 매핑 정책은 특정 애플리케이션에 대해 발급된 토큰에 내보내지는 클레임을 수정하는 **정책** 개체의 종류입니다.

## <a name="claim-sets"></a>클레임 집합

토큰에 사용되는 시기와 방법을 정의하는 특정 클레임 집합이 있습니다.

| 클레임 집합 | Description |
|---|---|
| 핵심 클레임 집합 | 정책에 관계없이 모든 토큰에 포함됩니다. 또한 이러한 클레임은 제한된 것으로 간주되며 수정할 수 없습니다. |
| 기본 클레임 집합 | 핵심 클레임 집합뿐 아니라 토큰에서 기본적으로 내보내지는 클레임을 포함합니다. 클레임 매핑 정책을 사용하여 기본 클레임을 생략하거나 수정할 수 있습니다. |
| 제한된 클레임 집합 | 정책을 사용하여 수정할 수 없습니다. 데이터 원본을 변경할 수 없으며, 이러한 클레임을 생성할 때 변환이 적용됩니다. |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>테이블 1: JWT(JSON Web Token) 제한된 클레임 집합

| 클레임 형식(이름) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appId |
| appidacr |
| 어설션 |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| 코드 |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceId |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| 이메일 |
| 엔드포인트(endpoint) |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| 역할(role) |
| roles |
| scope |
| scp |
| sid |
| 서명 |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| 사용자 이름 |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>표 2: SAML 제한된 클레임 집합

| 클레임 형식(URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>클레임 매핑 정책 속성

내보내는 클레임 및 데이터의 출처를 제어하려면 클레임 매핑 정책의 속성을 사용합니다. 정책을 설정하지 않으면 시스템은 핵심 클레임 집합, 기본 클레임 집합 및 애플리케이션이 수신하도록 선택한 [선택적 클레임](active-directory-optional-claims.md)이 포함된 토큰을 발급합니다.

### <a name="include-basic-claim-set"></a>기본 클레임 집합 포함

**문자열:** IncludeBasicClaimSet

**데이터 형식:** 부울(True 또는 False)

**요약:** 이 속성은 기본 클레임 집합이 이 정책의 영향을 받는 토큰에 포함되는지 여부를 결정합니다.

- True로 설정된 경우 기본 클레임 집합의 모든 클레임이 정책의 영향을 받는 토큰에 내보내집니다.
- False로 설정된 경우 기본 클레임 집합의 클레임은 동일한 정책의 클레임 스키마 속성에 개별적으로 추가되지 않는 한 토큰에 포함되지 않습니다.

> [!NOTE]
> 핵심 클레임 집합의 클레임은 이 속성의 설정값에 관계없이 모든 토큰에 표시됩니다.

### <a name="claims-schema"></a>클레임 스키마

**문자열:** ClaimsSchema

**데이터 형식:** 하나 이상의 클레임 스키마 항목이 있는 JSON Blob입니다.

**요약:** 이 속성은 기본 클레임 집합 및 핵심 클레임 집합 외에도 정책의 영향을 받는 토큰에 포함될 클레임을 정의합니다.
이 속성에 정의된 각 클레임 스키마 항목의 경우 특정 정보가 필요합니다. 데이터를 가져올 위치를 지정 합니다 (**값**, **원본/i d 쌍**또는 **원본/i d id 쌍**). 그리고 데이터를 내보낼 클레임 (**클레임 유형**)을 지정 합니다.

### <a name="claim-schema-entry-elements"></a>클레임 스키마 항목 요소

**Value:** Value 요소는 클레임에 내보내질 데이터로 정적 값을 정의합니다.

**Source/ID 쌍:** : Source 및 ID 요소는 클레임의 데이터가 제공되는 위치를 정의합니다.

**원본/ExtensionID 쌍:** 원본 및 ExtensionID 요소는 클레임의 데이터가 원본으로 지정 되는 디렉터리 스키마 확장 특성을 정의 합니다. 자세한 내용은 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조 하세요.

Source 요소는 다음 중 하나로 설정합니다.

- “user”: 클레임의 데이터가 User 개체의 속성입니다.
- “application”: 클레임의 데이터가 애플리케이션(클라이언트) 서비스 주체의 속성입니다.
- “resource”: 클레임의 데이터가 리소스 서비스 주체의 속성입니다.
- “audience”: 클레임의 데이터가 토큰의 대상 그룹인 서비스 주체(클라이언트 또는 리소스 서비스 주체)의 속성입니다.
- “company”: 클레임의 데이터가 리소스 테넌트에 대한 Company 개체의 속성입니다.
- “transformation”: 클레임의 데이터가 클레임 변환에서 제공됩니다(이 문서의 뒷부분에 있는 “클레임 변환” 섹션 참조).

원본이 변환인 경우 **TransformationID** 요소도 이 클레임 정의에 포함되어야 합니다.

ID 요소는 클레임의 값을 제공할 원본의 속성을 식별합니다. 다음 표는 각 Source 값에 유효한 ID 값을 나열합니다.

#### <a name="table-3-valid-id-values-per-source"></a>표 3: 원본별 유효한 ID 값

| 원본 | ID | Description |
|-----|-----|-----|
| 사용자 | surname | 성 |
| 사용자 | givenname | 이름 |
| 사용자 | displayname | 표시 이름 |
| 사용자 | objectid | ObjectID |
| 사용자 | mail | 메일 주소 |
| 사용자 | userprincipalname | 사용자 계정 이름 |
| 사용자 | department|department|
| 사용자 | onpremisessamaccountname | 온-프레미스 SAM 계정 이름 |
| 사용자 | netbiosname| NetBios 이름 |
| 사용자 | dnsdomainname | DNS 도메인 이름 |
| 사용자 | onpremisesecurityidentifier | 온-프레미스 보안 식별자 |
| 사용자 | companyname| 조직 이름 |
| 사용자 | streetaddress | 주소 |
| 사용자 | postalcode | 우편 번호 |
| 사용자 | preferredlanguange | 기본 설정 언어 |
| 사용자 | onpremisesuserprincipalname | 온-프레미스 UPN |*
| 사용자 | mailNickname | 메일 애칭 |
| 사용자 | extensionattribute1 | 확장 특성 1 |
| 사용자 | extensionattribute2 | 확장 특성 2 |
| 사용자 | extensionattribute3 | 확장 특성 3 |
| 사용자 | extensionattribute4 | 확장 특성 4 |
| 사용자 | extensionattribute5 | 확장 특성 5 |
| 사용자 | extensionattribute6 | 확장 특성 6 |
| 사용자 | extensionattribute7 | 확장 특성 7 |
| 사용자 | extensionattribute8 | 확장 특성 8 |
| 사용자 | extensionattribute9 | 확장 특성 9 |
| 사용자 | extensionattribute10 | 확장 특성 10 |
| 사용자 | extensionattribute11 | 확장 특성 11 |
| 사용자 | extensionattribute12 | 확장 특성 12 |
| 사용자 | extensionattribute13 | 확장 특성 13 |
| 사용자 | extensionattribute14 | 확장 특성 14 |
| 사용자 | extensionattribute15 | 확장 특성 15 |
| 사용자 | othermail | 다른 메일 |
| 사용자 | country | 국가/지역 |
| 사용자 | city | City |
| 사용자 | state | 시스템 상태 |
| 사용자 | jobtitle | 직위 |
| 사용자 | employeeid | 직원 ID |
| 사용자 | facsimiletelephonenumber | 팩스 번호 |
| 사용자 | assignedroles | 사용자에 게 할당 된 앱 역할의 목록입니다.|
| 애플리케이션, 리소스, 대상 그룹 | displayname | 표시 이름 |
| 애플리케이션, 리소스, 대상 그룹 | objectid | ObjectID |
| 애플리케이션, 리소스, 대상 그룹 | tags | 서비스 주체 태그 |
| 회사 | tenantcountry | 테넌트의 국가/지역 |

**TransformationID:** TransformationID 요소는 Source 요소가 “transformation”으로 설정된 경우에만 제공해야 합니다.

- 이 요소는 이 클레임에 대한 데이터가 생성되는 방식을 정의하는 **ClaimsTransformation** 속성에서 변환 항목의 ID 요소와 일치해야 합니다.

**클레임 유형:** **JwtClaimType** 및 **SamlClaimType** 요소는 이 클레임 스키마 항목이 참조하는 클레임을 정의합니다.

- JwtClaimType에는 JWT로 내보낼 클레임 이름이 포함되어야 합니다.
- SamlClaimType에는 SAML 토큰으로 내보낼 클레임 URI가 포함되어야 합니다.

* **onPremisesUserPrincipalName 특성:** 대체 ID를 사용 하는 경우 온-프레미스 특성 userPrincipalName는 Azure AD 특성 onPremisesUserPrincipalName와 동기화 됩니다. 이 특성은 대체 ID가 구성 된 경우에만 사용할 수 있지만 MS Graph Beta를 통해 사용할 수도 있습니다 https://graph.microsoft.com/beta/me/ .

> [!NOTE]
> 제한된 클레임 집합에 있는 클레임의 이름 및 URI는 클레임 형식 요소에 사용할 수 없습니다. 자세한 내용은 이 문서의 뒷부분에 나오는 "예외 및 제한 사항" 섹션을 참조하세요.

### <a name="claims-transformation"></a>클레임 변환

**문자열:** ClaimsTransformation

**데이터 형식:** 하나 이상의 변환 항목이 있는 JSON Blob입니다.

**요약:** 클레임 스키마에 지정된 클레임에 대한 출력 데이터를 생성하기 위해 이 속성을 사용하여 원본 데이터에 일반 변환을 적용합니다.

**ID:** ID 요소를 사용하여 TransformationID 클레임 스키마 항목에서 이 변환 항목을 참조합니다. 이 값은 이 정책 내에서 각 변환 항목에 고유해야 합니다.

**TransformationMethod:** TransformationMethod 요소는 클레임에 대한 데이터를 생성하기 위해 수행할 작업을 식별합니다.

선택한 방법에 따라 입력 및 출력 집합이 예상됩니다. **InputClaims**, **InputParameters** 및 **OutputClaims** 요소를 사용하여 입력과 출력을 정의합니다.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>표 4: 변환 메서드와 예상 입력 및 출력

|TransformationMethod|예상 입력|예상 출력|Description|
|-----|-----|-----|-----|
|Join|string1, string2, 구분 기호|outputClaim|구분 기호를 사용하여 입력 문자열을 조인합니다. 예: string1:"foo@bar.com" , string2:"sandbox" , separator:"."는 outputClaim:"foo@bar.com.sandbox"를 생성합니다.|
|ExtractMailPrefix|전자 메일 또는 UPN|추출 된 문자열|ExtensionAttributes 1-15 또는 사용자에 대 한 UPN 또는 전자 메일 주소 값을 저장 하는 기타 스키마 확장 (예:) johndoe@contoso.com 메일 주소의 로컬 부분을 추출합니다. 예: mail:"foo@bar.com"은 outputClaim:"foo"를 생성합니다. \@ 기호가 없으면 원본 입력 문자열이 현재 그대로 반환됩니다.|

**InputClaims:** InputClaims 요소를 사용하여 클레임 스키마 항목의 데이터를 변환에 전달합니다. 이 요소에는 두 개의 특성인 **ClaimTypeReferenceId** 및 **TransformationClaimType**이 있습니다.

- **ClaimTypeReferenceId**가 클레임 스키마 항목의 ID 요소와 조인되어 적절한 입력 클레임을 찾습니다.
- **TransformationClaimType**은 이 입력에 고유 이름을 지정하는 데 사용됩니다. 이 이름은 변환 방법에 대한 예상 입력 중 하나와 일치해야 합니다.

**InputParameters:** InputParameters 요소를 사용하여 상수 값을 변환에 전달합니다. 이 요소에는 두 개의 특성인 **Value** 및 **ID**가 있습니다.

- **Value**는 전달할 실제 상수 값입니다.
- **ID**는 입력에 고유 이름을 지정하는 데 사용됩니다. 이름은 변환 방법에 필요한 입력 중 하나와 일치해야 합니다.

**OutputClaims:** OutputClaims 요소를 사용하여 변환에 의해 생성된 데이터를 보관하고 클레임 스키마 항목에 연결합니다. 이 요소에는 두 개의 특성인 **ClaimTypeReferenceId** 및 **TransformationClaimType**이 있습니다.

- **ClaimTypeReferenceId**가 클레임 스키마 항목의 ID와 조인되어 적절한 출력 클레임을 찾습니다.
- **TransformationClaimType**은 출력에 고유 이름을 지정하는 데 사용됩니다. 이름은 변환 방법에 필요한 출력 중 하나와 일치해야 합니다.

### <a name="exceptions-and-restrictions"></a>예외 및 제한 사항

**SAML NameID 및 UPN:** : NameID 및 UPN 값이 제공되는 특성과 허용되는 클레임 변환은 제한됩니다. 허용되는 값을 확인하려면 표 5와 6을 참조하세요.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>표 5: SAML NameID에 대한 데이터 원본으로 허용되는 특성

|원본|ID|Description|
|-----|-----|-----|
| 사용자 | mail|메일 주소|
| 사용자 | userprincipalname|사용자 계정 이름|
| 사용자 | onpremisessamaccountname|온-프레미스 SAM 계정 이름|
| 사용자 | employeeid|직원 ID|
| 사용자 | extensionattribute1 | 확장 특성 1 |
| 사용자 | extensionattribute2 | 확장 특성 2 |
| 사용자 | extensionattribute3 | 확장 특성 3 |
| 사용자 | extensionattribute4 | 확장 특성 4 |
| 사용자 | extensionattribute5 | 확장 특성 5 |
| 사용자 | extensionattribute6 | 확장 특성 6 |
| 사용자 | extensionattribute7 | 확장 특성 7 |
| 사용자 | extensionattribute8 | 확장 특성 8 |
| 사용자 | extensionattribute9 | 확장 특성 9 |
| 사용자 | extensionattribute10 | 확장 특성 10 |
| 사용자 | extensionattribute11 | 확장 특성 11 |
| 사용자 | extensionattribute12 | 확장 특성 12 |
| 사용자 | extensionattribute13 | 확장 특성 13 |
| 사용자 | extensionattribute14 | 확장 특성 14 |
| 사용자 | extensionattribute15 | 확장 특성 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>표 6: SAML NameID에 허용되는 변환 메서드

| TransformationMethod | 제한 |
| ----- | ----- |
| ExtractMailPrefix | None |
| Join | 조인되는 접미사는 리소스 테넌트의 확인된 도메인이어야 합니다. |

### <a name="custom-signing-key"></a>사용자 지정 서명 키

클레임 매핑 정책을 적용하려면 사용자 지정 서명 키를 서비스 사용자 개체에 할당해야 합니다. 이렇게 하면 클레임 매핑 정책의 생성자가 토큰을 수정한 것을 인정하게 되며, 악의적인 행위자가 만든 클레임 매핑 정책을 사용하지 않도록 애플리케이션을 보호할 수 있습니다. 사용자 지정 서명 키를 추가하기 위해 Azure PowerShell cmdlet `new-azureadapplicationkeycredential`을 사용하여 애플리케이션 개체에 대한 대칭 키 자격 증명을 만들 수 있습니다. 이 Azure PowerShell cmdlet에 대한 자세한 내용은 [New-AzureADApplicationKeyCredential](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential?view=azureadps-2.0)을 참조하세요.

클레임 매핑을 사용하도록 설정된 앱은 [OpenID Connect 메타데이터 요청](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)에 `appid={client_id}`를 추가하여 토큰 서명 키의 유효성을 검사해야 합니다. 사용해야 하는 OpenID Connect 메타데이터 문서의 형식은 다음과 같습니다.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>교차 테넌트 시나리오

클레임 매핑 정책이 게스트 사용자에게 적용되지 않습니다. 게스트 사용자가 해당 서비스 주체에 클레임 매핑 정책이 할당된 애플리케이션에 액세스하려고 하면 기본 토큰이 발급됩니다(정책이 적용되지 않음).

## <a name="claims-mapping-policy-assignment"></a>클레임 매핑 정책 할당

클레임 매핑 정책은 서비스 사용자 개체에만 할당할 수 있습니다.

### <a name="example-claims-mapping-policies"></a>예제 클레임 매핑 정책

Azure AD에서 특정 서비스 주체에 대해 토큰에 내보내지는 클레임을 사용자 지정할 수 있는 경우 많은 시나리오가 가능합니다. 이 섹션에서는 클레임 매핑 정책 형식을 사용하는 방법을 이해하는 데 도움이 되는 몇 가지 일반적인 시나리오를 설명합니다.

> [!NOTE]
> 클레임 매핑 정책을 만들 때 토큰의 디렉터리 스키마 확장 특성에서 클레임을 내보낼 수도 있습니다. 요소에서 *ID* 대신 확장 특성에 *extensionid* 를 사용 `ClaimsSchema` 합니다.  확장 특성에 대 한 자세한 내용은 [디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조 하세요.

#### <a name="prerequisites"></a>사전 요구 사항

다음 예제에서는 서비스 주체에 대한 정책을 만들고, 업데이트, 연결 및 삭제합니다. Azure AD를 처음 접하는 분들은 [Azure AD 테넌트를 가져오는 방법](quickstart-create-new-tenant.md)을 살펴본 후 예제를 진행하는 것이 좋습니다.

시작하려면 다음 단계 중 하나를 수행합니다.

1. 최신 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다.
1. Connect 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 조직에서 만든 모든 정책을 확인하려면 다음 명령을 실행합니다. 다음 시나리오에서 대부분의 작업 후에 이 명령을 실행하여 정책이 예상대로 생성되는지 확인하는 것이 좋습니다.

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>예제: 서비스 주체에 발급된 토큰에서 기본 클레임을 생략하는 정책 만들기 및 할당

이 예제에서는 연결된 서비스 주체에 발급된 토큰에서 기본 클레임 집합을 제거하는 정책을 만듭니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에서 기본 클레임 집합을 제거합니다.
   1. 정책을 만들려면 이 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>예제: 서비스 주체에 발급된 토큰에서 EmployeeID 및 TenantCountry를 클레임으로 포함하는 정책 만들기 및 할당

이 예제에서는 연결된 서비스 주체에 발급된 토큰에 EmployeeID 및 TenantCountry를 추가하는 정책을 만듭니다. EmployeeID는 SAML 토큰 및 JWT 둘 다에서 이름 클레임 형식으로 내보내집니다. TenantCountry는 SAML 토큰 및 JWT 둘 다에서 국가/지역 클레임 형식으로 내보내집니다. 이 예제에서는 토큰에 기본 클레임 집합을 계속 포함합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>예제: 서비스 주체에 발급된 토큰에 클레임 변환을 사용하는 정책 만들기 및 할당

이 예제에서는 연결된 서비스 주체에 발급된 JWT에 사용자 지정 클레임 “JoinedData”를 내보내는 정책을 만듭니다. 이 클레임에는 사용자 개체의 extensionattribute1 특성에 저장된 데이터와 “.sandbox”를 조인하여 만든 값이 포함됩니다. 이 예제에서는 토큰에 기본 클레임 집합을 제외합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>참고 항목

- Azure Portal을 통해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대한 자세한 내용은 [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](active-directory-saml-claims-customization.md)을 참조하세요.
- 확장 특성에 대해 자세히 알아보려면 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조 하세요.
