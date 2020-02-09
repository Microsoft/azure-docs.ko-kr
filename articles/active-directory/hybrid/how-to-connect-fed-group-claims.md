---
title: Azure Active Directory를 사용 하 여 응용 프로그램에 대 한 그룹 클레임 구성 | Microsoft Docs
description: Azure AD에서 사용할 그룹 클레임을 구성 하는 방법에 대 한 정보입니다.
services: active-directory
documentationcenter: ''
ms.reviewer: paulgarn
manager: daveba
ms.subservice: hybrid
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 02/27/2019
ms.author: billmath
author: billmath
ms.openlocfilehash: 3cb53656adb1dbeb5e5597d02edfe5be4dbec6a8
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170493"
---
# <a name="configure-group-claims-for-applications-with-azure-active-directory-public-preview"></a>Azure Active Directory를 사용 하 여 응용 프로그램에 대 한 그룹 클레임 구성 (공개 미리 보기)

응용 프로그램 내에서 사용 하기 위해 토큰에 사용자 그룹 멤버 자격 정보를 제공할 수 Azure Active Directory.  다음 두 가지 주요 패턴이 지원 됩니다.

- OID (Azure Active Directory 개체 식별자) 특성으로 식별 되는 그룹 (일반적으로 사용 가능)
- Active Directory (AD) 동기화 된 그룹 및 사용자에 대 한 sAMAccountName 또는 GroupSID 특성으로 식별 되는 그룹 (공개 미리 보기)

> [!IMPORTANT]
> 이 미리 보기 기능에는 다음과 같은 몇 가지 주의 사항이 있습니다.
>
>- 온-프레미스에서 동기화 된 sAMAccountName 및 SID (보안 식별자) 특성 사용에 대 한 지원은 AD FS 및 기타 id 공급자에서 기존 응용 프로그램을 이동할 수 있도록 설계 되었습니다. Azure AD에서 관리 되는 그룹은 이러한 클레임을 내보내는 데 필요한 특성을 포함 하지 않습니다.
>- 대규모 조직에서 사용자가 멤버로 속한 그룹 수는 Azure Active Directory 토큰에 추가 되는 한도를 초과할 수 있습니다. 150 SAML 토큰에 대 한 그룹 및 JWT의 경우 200입니다. 이로 인해 예기치 않은 결과가 발생할 수 있습니다. 이 문제가 잠재적 문제인 경우에는 응용 프로그램에 대 한 관련 그룹으로 클레임을 제한할 수 있도록 향상 된 기능을 추가할 때까지 대기 하는 것이 좋습니다.  
>- 새 응용 프로그램 개발의 경우 또는 응용 프로그램을 구성할 수 있는 경우와 중첩 된 그룹 지원이 필요 하지 않은 경우 앱 내 권한 부여는 그룹이 아닌 응용 프로그램 역할을 기반으로 하는 것이 좋습니다.  이렇게 하면 토큰으로 이동 해야 하는 정보의 양을 제한 하 고, 보안을 강화 하며, 앱 구성에서 사용자 할당을 구분 합니다.

## <a name="group-claims-for-applications-migrating-from-ad-fs-and-other-identity-providers"></a>AD FS 및 기타 id 공급자에서 마이그레이션하는 응용 프로그램에 대 한 그룹 클레임

AD FS 인증 하도록 구성 된 많은 응용 프로그램은 Windows AD 그룹 특성의 형태로 그룹 멤버 자격 정보를 사용 합니다.   이러한 특성은-도메인 이름으로 정규화 될 수 있는 그룹 sAMAccountName 또는 Windows 그룹 보안 식별자 (GroupSID)입니다.  응용 프로그램이 AD FS 페더레이션 되 면 AD FS TokenGroups 함수를 사용 하 여 사용자에 대 한 그룹 멤버 자격을 검색 합니다.

앱이 AD FS에서 받는 토큰을 일치 시키기 위해 그룹의 Azure Active Directory objectID 대신 도메인 정규화 된 sAMAccountName을 포함 하는 그룹 및 역할 클레임을 내보낼 수 있습니다.

그룹 클레임에 대해 지원 되는 형식은 다음과 같습니다.

- **Azure Active Directory 그룹 ObjectId** (모든 그룹에 대해 사용 가능)
- **sAMAccountName** (Active Directory에서 동기화 된 그룹에 사용 가능)
- **NetbiosDomain\sAMAccountName** (Active Directory에서 동기화 된 그룹에 사용 가능)
- **Dnsdomainname\samaccountname** (Active Directory에서 동기화 된 그룹에 사용 가능)
- 온 **-프레미스 그룹 보안 식별자** (Active Directory에서 동기화 된 그룹에 사용 가능)

> [!NOTE]
> sAMAccountName 및 온-프레미스 그룹 SID 특성은 Active Directory에서 동기화 된 그룹 개체 에서만 사용할 수 있습니다.   Azure Active Directory 또는 Office365에서 만든 그룹에 대해서는 사용할 수 없습니다.   동기화 된 온-프레미스 그룹 특성을 가져오도록 Azure Active Directory에서 구성 된 응용 프로그램은 동기화 된 그룹에 대해서만 가져옵니다.

## <a name="options-for-applications-to-consume-group-information"></a>그룹 정보를 사용 하는 응용 프로그램에 대 한 옵션

응용 프로그램에서 그룹 정보를 가져오는 한 가지 방법은 인증 된 사용자에 대 한 그룹 멤버 자격을 검색 하기 위해 Graph 그룹 엔드포인트을 호출 하는 것입니다. 이 호출을 통해 많은 수의 그룹이 포함 된 경우에도 사용자가 멤버로 속한 모든 그룹을 사용할 수 있으며,이 경우 응용 프로그램은 사용자가 멤버로 속한 모든 그룹을 열거 해야 합니다.  그런 다음 그룹 열거는 토큰 크기 제한에 독립적입니다.

그러나 기존 응용 프로그램에서 수신 하는 토큰의 클레임을 통해 그룹 정보를 이미 사용할 것으로 예상 하는 경우에는 응용 프로그램의 요구 사항에 맞게 다양 한 클레임 옵션을 사용 하 여 Azure Active Directory를 구성할 수 있습니다.  이때 다음 옵션을 사용할 수 있습니다.

- 응용 프로그램에서 권한 부여를 위해 그룹 멤버 자격을 사용 하는 경우 Azure Active Directory에서 고유 하 고 고유 하지 않으며 모든 그룹에서 사용할 수 있는 그룹 ObjectID를 사용 하는 것이 좋습니다.
- 권한 부여를 위해 온-프레미스 그룹 sAMAccountName을 사용 하는 경우 정규화 된 도메인 이름을 사용 합니다.  이름 충돌로 인해 발생 하는 상황에 대 한 가능성이 줄어듭니다. sAMAccountName은 Active Directory 도메인 내에서 고유할 수 있지만 둘 이상의 Active Directory 도메인이 Azure Active Directory 테넌트와 동기화 되는 경우 두 개 이상의 그룹이 동일한 이름을 가질 가능성이 있습니다.
- [응용 프로그램 역할](../../active-directory/develop/howto-add-app-roles-in-azure-ad-apps.md) 을 사용 하 여 그룹 멤버 자격과 응용 프로그램 간의 간접 참조 계층을 제공 하는 것이 좋습니다.   그런 다음 응용 프로그램은 토큰의 역할 clams를 기반으로 하 여 내부 권한 부여 결정을 내립니다.
- Active Directory에서 동기화 되는 그룹 특성을 가져오도록 응용 프로그램을 구성 하 고 그룹에 해당 특성이 포함 되지 않은 경우 클레임에 포함 되지 않습니다.
- 토큰의 그룹 클레임은 중첩 된 그룹을 포함 합니다.   사용자가 GroupB의 멤버이 고 GroupB가 GroupA의 멤버인 경우 사용자에 대 한 그룹 클레임은 GroupA와 GroupB를 모두 포함 합니다. 중첩 그룹을 많이 사용 하는 조직 및 그룹 멤버 자격이 많은 사용자의 경우 토큰에 나열 된 그룹 수가 토큰 크기를 늘릴 수 있습니다.   Azure Active Directory는 토큰에 내보낼 그룹 수를 SAML 어설션에 대 한 150로 제한 하 고 JWT의 경우 200를 통해 토큰이 너무 커지지 않도록 합니다.  사용자가 한도 보다 많은 그룹의 멤버인 경우 그룹 정보를 가져오기 위해 그래프 엔드포인트에 대 한 링크와 함께 그룹이 내보내집니다.

> Active Directory에서 동기화 된 그룹 특성을 사용 하기 위한 필수 구성 요소:   Azure AD Connect를 사용 하 여 Active Directory에서 그룹을 동기화 해야 합니다.

Active Directory 그룹에 대 한 그룹 이름을 내보내도록 Azure Active Directory를 구성 하는 단계는 두 단계로 구성 됩니다.

1. **Active Directory에서 그룹 이름 동기화** 그룹 또는 역할 클레임에서 그룹 이름 또는 온-프레미스 그룹 SID를 Azure Active Directory 내보내려면 먼저 필요한 특성을 Active Directory에서 동기화 해야 합니다.  Azure AD Connect 버전 1.2.70 이상 버전을 실행 해야 합니다.   버전 1.2.70 Azure AD Connect 이전에는 Active Directory에서 그룹 개체를 동기화 하지만 기본적으로 필요한 그룹 이름 특성은 포함 하지 않습니다.  현재 버전으로 업그레이드 해야 합니다.

2. **토큰에 그룹 클레임을 포함 하도록 Azure Active Directory에서 응용 프로그램 등록 구성** 그룹 클레임은 갤러리 또는 비 갤러리 SAML SSO 응용 프로그램에 대 한 포털의 엔터프라이즈 응용 프로그램 섹션에서 구성 하거나 응용 프로그램 등록 섹션에서 응용 프로그램 매니페스트를 사용 하 여 구성할 수 있습니다.  응용 프로그램 매니페스트에서 그룹 클레임을 구성 하려면 아래의 "그룹 특성에 대 한 Azure Active Directory 응용 프로그램 등록 구성"을 참조 하세요.

## <a name="configure-group-claims-for-saml-applications-using-sso-configuration"></a>SSO 구성을 사용 하 여 SAML 응용 프로그램에 대 한 그룹 클레임 구성

갤러리 또는 비 갤러리 SAML 응용 프로그램에 대 한 그룹 클레임을 구성 하려면 엔터프라이즈 응용 프로그램을 열고 목록에서 응용 프로그램을 클릭 한 후 Single Sign On 구성을 선택 합니다.

"토큰에서 반환 된 그룹" 옆의 편집 아이콘을 선택 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-1.png)

라디오 단추를 사용 하 여 토큰에 포함 해야 하는 그룹을 선택 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-2.png)

| 선택 | 설명 |
|----------|-------------|
| **모든 그룹** | 보안 그룹 및 배포 목록을 내보냅니다.   또한 사용자가 할당 된 디렉터리 역할을 ' wids ' 클레임에 할당 하 고 사용자가 할당 된 모든 응용 프로그램 역할을 역할 클레임에서 내보내도록 합니다. |
| **보안 그룹** | 사용자가 구성원 인 보안 그룹을 그룹 클레임에 내보냅니다. |
| **메일 그룹** | 사용자가 구성원 인 메일 그룹을 내보냅니다. |
| **디렉터리 역할** | 사용자에 게 디렉터리 역할이 할당 된 경우 해당 사용자는 ' wids ' 클레임으로 내보내집니다. 그룹 클레임은 내보내지 않습니다. |

예를 들어 사용자가 구성원으로 속한 모든 보안 그룹을 내보내려면 보안 그룹을 선택 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-3.png)

Azure AD Objectid 대신 Active Directory에서 동기화 된 Active Directory 특성을 사용 하 여 그룹을 내보내려면 드롭다운에서 필요한 형식을 선택 합니다.  이는 클레임의 개체 ID를 그룹 이름을 포함 하는 문자열 값으로 바꿉니다.   Active Directory에서 동기화 된 그룹만 클레임에 포함 됩니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-4.png)

### <a name="advanced-options"></a>고급 옵션

그룹 클레임을 내보내는 방법은 고급 옵션 아래의 설정으로 수정할 수 있습니다.

그룹 클레임의 이름을 사용자 지정 합니다.  이를 선택 하면 그룹 클레임에 대해 다른 클레임 형식을 지정할 수 있습니다.   이름 필드에 클레임 유형을 입력 하 고 네임 스페이스 필드에 클레임에 대 한 선택적 네임 스페이스를 입력 합니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-5.png)

일부 응용 프로그램에는 ' 역할 ' 클레임에 표시 되는 그룹 멤버 자격 정보가 필요 합니다. ' 그룹에 역할 클레임 내보내기 ' 상자를 선택 하 여 필요에 따라 사용자 그룹을 역할로 내보낼 수 있습니다.

![클레임 UI](media/how-to-connect-fed-group-claims/group-claims-ui-6.png)

> [!NOTE]
> 그룹 데이터를 역할로 내보내는 옵션이 사용 되 면 그룹만 역할 클레임에 표시 됩니다.  사용자가 할당 된 모든 응용 프로그램 역할은 역할 클레임에 표시 되지 않습니다.

## <a name="configure-the-azure-ad-application-registration-for-group-attributes"></a>그룹 특성에 대 한 Azure AD 응용 프로그램 등록 구성

또한 그룹 클레임은 [응용 프로그램 매니페스트의](../../active-directory/develop/reference-app-manifest.md) [선택적 클레임](../../active-directory/develop/active-directory-optional-claims.md) 섹션에서 구성할 수 있습니다.

1. 포털-> Azure Active Directory-> 응용 프로그램 등록-> 응용 프로그램 > 매니페스트를 선택 합니다.

2. GroupMembershipClaim를 변경 하 여 그룹 멤버 자격 클레임을 사용 하도록 설정

   유효한 값은 다음과 같습니다.

   - 모두가
   - "SecurityGroup"
   - DistributionList
   - "DirectoryRole"

   예를 들어 다음과 같은 가치를 제공해야 합니다.

   ```json
   "groupMembershipClaims": "SecurityGroup"
   ```

   기본적으로 group Objectid은 그룹 클레임 값에 내보내집니다.  온-프레미스 그룹 특성을 포함 하도록 클레임 값을 수정 하거나 클레임 유형을 역할로 변경 하려면 다음과 같이 OptionalClaims 구성을 사용 합니다.

3. 그룹 이름 구성 선택적 클레임을 설정 합니다.

   토큰의 그룹이 선택적 클레임 섹션에서 온-프레미스 AD 그룹 특성을 포함 하도록 하려면 선택적 클레임을 적용 해야 하는 토큰 유형, 요청 된 선택적 클레임 이름 및 필요한 추가 속성을 지정 합니다.  여러 토큰 유형이 나열 될 수 있습니다.

   - OIDC ID 토큰에 대 한 idToken
   - OAuth/OIDC 액세스 토큰에 대 한 accessToken
   - SAML 토큰에 대 한 Saml2Token.

   > [!NOTE]
   > Saml2Token 형식은 SAML 1.1 및 SAML 2.0 형식 토큰에 모두 적용 됩니다.

   관련 된 각 토큰 유형에 대해 그룹 클레임을 수정 하 여 매니페스트의 OptionalClaims 섹션을 사용 합니다. OptionalClaims 스키마는 다음과 같습니다.

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
   | **name:** | "그룹" 이어야 합니다. |
   | **원본** | 사용되지 않습니다. Null 생략 또는 지정 |
   | **essential:** | 사용되지 않습니다. False를 생략 하거나 지정 합니다. |
   | **AdditionalProperties** | 추가 속성의 목록입니다.  유효한 옵션은 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles"입니다. |

   AdditionalProperties에서 "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name" 중 하나만 필요 합니다.  둘 이상의가 있는 경우 첫 번째가 사용 되 고 나머지는 무시 됩니다.

   일부 응용 프로그램에는 역할 클레임의 사용자에 대 한 그룹 정보가 필요 합니다.  클레임 유형을 그룹 클레임에서 역할 클레임으로 변경 하려면 추가 속성에 "emit_as_roles"를 추가 합니다.  그룹 값은 역할 클레임에 내보내집니다.

   > [!NOTE]
   > "Emit_as_roles"를 사용 하는 경우 사용자가 할당 된 것으로 구성 된 응용 프로그램 역할은 역할 클레임에 표시 되지 않습니다.

### <a name="examples"></a>예

Dnsdomainnamenameformat 형식의 OAuth 액세스 토큰에서 그룹을 그룹 이름으로 내보냅니다.

```json
"optionalClaims": {
    "accessToken": [{
        "name": "groups",
        "additionalProperties": ["dns_domain_and_sam_account_name"]
    }]
}
 ```

SAML 및 OIDC ID 토큰에서 역할 클레임으로 반환 될 그룹 이름을 netbiosDomain\samAccountName 형식으로 내보내려면 다음을 수행 합니다.

```json
"optionalClaims": {
    "saml2Token": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }],

    "idToken": [{
        "name": "groups",
        "additionalProperties": ["netbios_name_and_sam_account_name", "emit_as_roles"]
    }]
 }
 ```

## <a name="next-steps"></a>다음 단계

[하이브리드 ID란?](whatis-hybrid-identity.md)
