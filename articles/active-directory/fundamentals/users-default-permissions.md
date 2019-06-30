---
title: 기본 사용자 권한 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 사용할 수 있는 다른 사용자 권한에 대해 알아봅니다.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: lizross
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206e501860691cccc0578a0df4eec2b161b99b4c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341357"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Azure Active Directory의 기본 사용자 권한이란?
Azure AD(Azure Active Directory)의 모든 사용자에게는 기본 권한 집합이 부여됩니다. 사용자 액세스는 사용자 유형, [역할 할당](active-directory-users-assign-role-azure-portal.md) 및 개별 개체의 소유권으로 구성됩니다. 이 문서에서는 이 기본 권한을 설명하며 멤버와 게스트 사용자 기본값을 비교합니다. 기본 사용자 권한은 Azure AD의 사용자 설정에서만 변경할 수 있습니다.

## <a name="member-and-guest-users"></a>멤버 및 게스트 사용자
제공되는 기본 권한 세트는 사용자가 테넌트의 기본 멤버(멤버 사용자)인지 아니면 B2B 협업 게스트(게스트 사용자)로 다른 디렉터리에서 전달되었는지에 따라 달라집니다. 게스트 사용자 추가에 대한 자세한 내용은 [Azure AD B2B 협업이란?](../b2b/what-is-b2b.md)을 참조하세요.
* 멤버 사용자는 애플리케이션을 등록하고, 자신의 프로필 사진과 휴대전화 번호를 관리하며, 자신의 암호를 변경하고, B2B 게스트를 초대할 수 있습니다. 또한 몇 가지 예외가 있지만 모든 디렉터리 정보를 읽을 수 있습니다. 
* 게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 예를 들어 게스트 사용자는 자신의 프로필 정보 밖의 테넌트에서 정보를 찾아볼 수 없습니다. 그러나 게스트 사용자는 사용자 계정 이름이나 objectId를 제공하여 다른 사용자 정보를 검색할 수 있습니다. 게스트 사용자는 **게스트 사용자 권한이 제한됨** 설정과 관계없이 그룹 멤버 자격을 비롯한 소속 그룹 속성을 읽을 수 있습니다. 게스트는 다른 테넌트 개체에 대한 정보를 볼 수 없습니다.

게스트에 대한 기본 권한은 기본적으로 제한적입니다. 게스트를 관리자 역할에 추가할 수 있고, 이 경우 해당 역할에 포함된 전체 읽기 쓰기 권한이 부여됩니다. 게스트가 다른 사용자를 초대하는 기능에도 추가적인 제한이 있습니다. **게스트가 초대할 수 있음**을 **아니요**로 설정하면 게스트가 다른 게스트를 초대할 수 없습니다. 방법은 [B2B 협업에 대한 초대 위임](../b2b/delegate-invitations.md)을 참조하세요. 게스트 사용자에게 기본적으로 멤버 사용자와 같은 권한을 부여하려면 **게스트 사용자 권한이 제한됨**을 **아니요**로 설정합니다. 이 설정은 기본적으로 게스트 사용자에게 모든 멤버 사용자 권한을 부여하며 게스트가 관리자 역할에 추가되는 것도 허용합니다.

## <a name="compare-member-and-guest-default-permissions"></a>멤버 및 게스트 기본 권한 비교

**영역** | **멤버 사용자 권한** | **게스트 사용자 권한**
------------ | --------- | ----------
사용자 및 연락처 | 사용자 및 연락처의 모든 공용 속성 읽기<br>게스트 초대<br>자신의 암호 변경<br>휴대전화 번호 관리<br>자신의 사진 관리<br>자신의 새로 고침 토큰 무효화 | 자신의 속성 읽기<br>표시 된 이름을 읽도록, 전자 메일, 이름, 사진, 사용자 계정 이름 및 다른 사용자 및 연락처의 사용자 형식 속성에 로그인<br>자신의 암호 변경
그룹 | 보안 그룹 만들기<br>Office 365 그룹 만들기<br>모든 그룹 속성 읽기<br>숨겨지지 않은 그룹 멤버 자격 읽기<br>조인된 그룹의 숨겨진 Office 365의 그룹 멤버 자격 읽기<br>사용자가 소유한 그룹의 속성, 소유권 및 멤버 자격 관리<br>게스트를 소유 그룹에 추가<br>동적 멤버 자격 설정 관리<br>소유 그룹 삭제<br>소유한 Office 365 그룹 복원 | 모든 그룹 속성 읽기<br>숨겨지지 않은 그룹 멤버 자격 읽기<br>조인된 그룹의 숨겨진 Office 365 그룹 멤버 자격 읽기<br>소유 그룹 관리<br>게스트를 소유 그룹에 추가(허용된 경우)<br>소유 그룹 삭제<br>소유한 Office 365 그룹 복원<br>멤버 자격을 포함한 소속 그룹의 속성 읽기
애플리케이션 | 새 애플리케이션 등록(만들기)<br>등록 및 엔터프라이즈 애플리케이션의 속성 읽기<br>소유 애플리케이션의 애플리케이션 속성, 할당 및 자격 증명 관리<br>사용자에 대한 애플리케이션 암호 만들기 또는 삭제<br>소유 애플리케이션 삭제<br>소유 애플리케이션 복원 | 등록 및 엔터프라이즈 애플리케이션의 속성 읽기<br>소유 애플리케이션의 애플리케이션 속성, 할당 및 자격 증명 관리<br>소유 애플리케이션 삭제<br>소유 애플리케이션 복원
디바이스 | 모든 디바이스 속성 읽기<br>소유한 디바이스의 모든 속성 읽기<br> | 권한 없음<br>소유 디바이스 삭제<br>
디렉터리 | 모든 회사 정보 읽기<br>모든 도메인 읽기<br>모든 파트너 계약 읽기 | 표시 이름 및 확인된 도메인 읽기
역할 및 범위 | 모든 관리 역할 및 멤버 자격 읽기<br>관리 단위의 모든 속성 및 멤버 자격 읽기 | 권한 없음 
구독 | 모든 구독 읽기<br>서비스 계획 멤버를 사용하도록 설정 | 권한 없음
정책 | 모든 정책 속성 읽기<br>소유한 정책의 모든 속성 읽기 | 권한 없음

## <a name="to-restrict-the-default-permissions-for-member-users"></a>멤버 사용자에 대한 기본 권한을 제한하려면

멤버 사용자의 기본 권한은 다음 방식으로 제한할 수 있습니다.

사용 권한 | 설정 설명
---------- | ------------
응용 프로그램을 등록할 수 있습니다. | 이 옵션을 아니요로 설정 하면을 응용 프로그램 등록을 만들 수 없습니다. 기능 응용 프로그램 개발자 역할을 추가 하 여 특정 개인에 게 부여할 수 있습니다.
사용자가 LinkedIn으로 회사 또는 학교 계정에 연결하도록 허용 | 이 옵션을 아니요로 설정 하면을 회사 또는 학교 계정으로 LinkedIn 계정으로 연결할 수 없습니다. 자세한 내용은 [LinkedIn 계정 연결 데이터 공유 및 동의](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)합니다.
보안 그룹 만들기 기능 | 이 옵션을 아니요로 설정하면 사용자가 보안 그룹을 만들 수 없습니다. 전역 관리자 및 사용자 관리자 보안 그룹을 만들 여전히 수 있습니다. 자세한 내용은 [그룹 설정 구성을 위한 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-cmdlets.md)을 참조하세요.
Office 365 그룹을 만드는 기능 | 이 옵션을 아니요로 설정하면 사용자가 Office 365 그룹을 만들 수 없습니다. 이 옵션을 일부로 설정하면 Office 365 그룹을 만들 수 있는 사용자 집합을 선택할 수 있습니다. 전역 관리자 및 사용자 관리자 Office 365 그룹을 만들 수 수 있습니다. 자세한 내용은 [그룹 설정 구성을 위한 Azure Active Directory cmdlet](../users-groups-roles/groups-settings-cmdlets.md)을 참조하세요.
Azure AD 관리 포털에 대한 액세스 제한 | 이 옵션을 예로 설정 사용자를 Azure portal을 통해 Azure Active Directory에 액세스할 수 없습니다.
다른 사용자를 읽을 수 있는 기능 | 이 설정은 PowerShell에서만 사용할 수 있습니다. $False에이 플래그를 설정 하면 모든 비관리자 디렉터리에서 사용자 정보를 읽을 수 없습니다. 이 플래그는 하지 않는 Exchange Online과 같은 다른 Microsoft 서비스에서 사용자 정보를 읽는 중입니다. 이 설정은 특별 한 경우에 사용 되며 $false에이 플래그를 설정 합니다. 권장 되지 않습니다.

## <a name="object-ownership"></a>개체 소유권

### <a name="application-registration-owner-permissions"></a>애플리케이션 등록 소유자 권한
사용자가 애플리케이션을 등록하면 해당 사용자가 자동으로 해당 애플리케이션의 소유자로 추가됩니다. 소유자는 앱이 요청하는 이름 및 권한 등, 애플리케이션의 메타데이터를 관리할 수 있습니다. 또한 SSO 구성 및 사용자 할당 등, 애플리케이션의 테넌트 특정 구성도 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와 달리 소유자는 자신의 소유한 애플리케이션만 관리할 수 있습니다.

### <a name="enterprise-application-owner-permissions"></a>엔터프라이즈 응용 프로그램 소유자 권한
새 엔터프라이즈 응용 프로그램을 추가 하는 사용자를 소유자로 자동으로 추가 됩니다. 소유자는 응용 프로그램 SSO 구성, 프로 비전 및 사용자 할당 등의 테 넌 트 별 구성을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자와 달리 소유자는 자신이 소유한 응용 프로그램만 관리할 수 있습니다.

### <a name="group-owner-permissions"></a>그룹 소유자 권한
사용자가 그룹을 만들면 자동으로 해당 그룹의 소유자로 추가됩니다. 소유자는 이름 등의 그룹 속성과 그룹 멤버 자격을 관리할 수 있습니다. 소유자는 다른 소유자를 추가하거나 제거할 수도 있습니다. 전역 관리자 및 사용자 관리자와 달리 소유자는 자신이 소유한 그룹만 관리할만 있습니다. 그룹 소유자를 할당하려면 [그룹 소유자 관리](active-directory-accessmanagement-managing-group-owners.md)를 참조하세요.

### <a name="ownership-permissions"></a>소유권 사용 권한
다음 표에서 멤버 사용자 개체를 소유 하는 Azure Active Directory에서 특정 사용 권한에 대해 설명 합니다. 사용자는 자신이 소유 하는 개체에 이러한 권한을 있습니다.

#### <a name="owned-application-registrations"></a>소유 응용 프로그램 등록
사용자는 소유 응용 프로그램 등록에서 다음 작업을 수행할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/applications/audience/update | Azure Active Directory에서 applications.audience 속성을 업데이트합니다. |
| microsoft.directory/applications/authentication/update | Azure Active Directory에서 applications.authentication 속성을 업데이트합니다. |
| microsoft.directory/applications/basic/update | Azure Active Directory에서 애플리케이션의 기본 속성을 업데이트합니다. |
| microsoft.directory/applications/credentials/update | Azure Active Directory에서 applications.credentials 속성을 업데이트합니다. |
| microsoft.directory/applications/delete | Azure Active Directory에서 애플리케이션을 삭제합니다. |
| microsoft.directory/applications/owners/update | Azure Active Directory에서 applications.owners 속성을 업데이트합니다. |
| microsoft.directory/applications/permissions/update | Azure Active Directory에서 applications.permissions 속성을 업데이트합니다. |
| microsoft.directory/applications/policies/update | Azure Active Directory에서 applications.policies 속성을 업데이트합니다. |
| microsoft.directory/applications/restore | Azure Active Directory에서 애플리케이션을 복원합니다. |

#### <a name="owned-enterprise-applications"></a>소유 엔터프라이즈 응용 프로그램
사용자는 소유 엔터프라이즈 응용 프로그램에서 다음 작업을 수행할 수 있습니다. 엔터프라이즈 응용 프로그램 서비스 주체, 하나 이상의 응용 프로그램 정책 및 경우에 따라 서비스 주체는 동일한 테 넌 트에 응용 프로그램 개체의 구성 됩니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Azure Active Directory에서 auditLogs에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |
| microsoft.directory/policies/basic/update | Azure Active Directory에서 정책의 기본 속성을 업데이트합니다. |
| microsoft.directory/policies/delete | Azure Active Directory에서 정책을 삭제합니다. |
| microsoft.directory/policies/owners/update | Azure Active Directory에서 policies.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory에서 servicePrincipals.appRoleAssignedTo 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory에서 users.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/audience/update | Azure Active Directory에서 servicePrincipals.audience 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/authentication/update | Azure Active Directory에서 servicePrincipals.authentication 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/basic/update | Azure Active Directory에서 servicePrincipals의 기본 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/credentials/update | Azure Active Directory에서 servicePrincipals.credentials 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/delete | Azure Active Directory에서 servicePrincipals를 삭제합니다. |
| microsoft.directory/servicePrincipals/owners/update | Azure Active Directory에서 servicePrincipals.owners 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/permissions/update | Azure Active Directory에서 servicePrincipals.permissions 속성을 업데이트합니다. |
| microsoft.directory/servicePrincipals/policies/update | Azure Active Directory에서 servicePrincipals.policies 속성을 업데이트합니다. |
| microsoft.directory/signInReports/allProperties/read | Azure Active Directory에서 signInReports에 대한 모든 속성(권한 있는 속성 포함)을 읽습니다. |

#### <a name="owned-devices"></a>소유 장치
사용자 소유 장치에서 다음 작업을 수행할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Azure Active Directory에서 devices.bitLockerRecoveryKeys 속성을 읽습니다. |
| microsoft.directory/devices/disable | Azure Active Directory에서 디바이스를 사용하지 않도록 설정합니다. |

#### <a name="owned-groups"></a>소유 그룹
사용자는 소유 그룹에서 다음 작업을 수행할 수 있습니다.

| **actions** | **설명** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Azure Active Directory에서 groups.appRoleAssignments 속성을 업데이트합니다. |
| microsoft.directory/groups/basic/update | Azure Active Directory에서 그룹의 기본 속성을 업데이트합니다. |
| microsoft.directory/groups/delete | Azure Active Directory에서 그룹을 삭제합니다. |
| microsoft.directory/groups/dynamicMembershipRule/update | Azure Active Directory에서 groups.dynamicMembershipRule 속성을 업데이트합니다. |
| microsoft.directory/groups/members/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/owners/update | Azure Active Directory에서 groups.members 속성을 업데이트합니다. |
| microsoft.directory/groups/restore | Azure Active Directory에서 그룹을 복원합니다. |
| microsoft.directory/groups/settings/update | Azure Active Directory에서 groups.settings 속성을 업데이트합니다. |

## <a name="next-steps"></a>다음 단계

* Azure AD 관리자 역할을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md) 참조
* Microsoft Azure에서 리소스 액세스를 제어하는 방법을 자세히 알아보려면 [Azure에서 리소스 액세스 이해](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure 구독에 Azure Active Directory가 연결되는 방법에 대한 자세한 내용은 [Azure 구독을 Azure Active Directory에 연결하는 방법](active-directory-how-subscriptions-associated-directory.md)
* [사용자 관리](add-users-azure-active-directory.md)
