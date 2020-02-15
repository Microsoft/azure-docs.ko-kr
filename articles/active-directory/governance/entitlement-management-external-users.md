---
title: Azure AD 자격 관리에서 외부 사용자에 대 한 액세스 제어-Azure Active Directory
description: Azure Active Directory 자격 관리에서 외부 사용자에 대 한 액세스를 제어 하도록 지정할 수 있는 설정에 대해 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/26/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf7e4837aaf65b0df28ea4a07fb485948309bc7c
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185570"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리에서 외부 사용자에 대 한 액세스 제어

Azure AD 자격 관리는 [AZURE AD b2b (기업 간)](../b2b/what-is-b2b.md) 를 활용 하 여 다른 디렉터리에서 조직 외부 사용자와 공동 작업 합니다. Azure AD B2B를 사용 하는 경우 외부 사용자는 홈 디렉터리에 대해 인증 하지만 디렉터리에는 표시 됩니다. 디렉터리의 표현을 사용 하면 사용자에 게 리소스에 대 한 액세스 권한을 할당할 수 있습니다.

이 문서에서는 외부 사용자에 대 한 액세스를 제어 하기 위해 지정할 수 있는 설정을 설명 합니다.

## <a name="how-entitlement-management-can-help"></a>권한 관리가 어떻게 도움이 될 수 있나요?

[AZURE AD B2B](../b2b/what-is-b2b.md) 초대 환경을 사용 하는 경우 리소스 디렉터리로 가져오고 작업할 외부 게스트 사용자의 메일 주소를 이미 알고 있어야 합니다. 이러한 방식은 규모가 작거나 단기 프로젝트에서 작업 하 고 있는 모든 참가자를 이미 알고 있는 경우에 유용 하지만, 작업 하려는 사용자가 많고 시간이 지남에 따라 참가자가 변경 되는 경우에는 관리 하기가 어렵습니다.  예를 들어 다른 조직에 대해 작업 하 고 있으며 해당 조직과의 연락 지점이 하나 있지만 시간이 지남에 따라 해당 조직의 추가 사용자도 액세스 해야 합니다.

권한 관리를 통해 사용자가 지정한 조직의 사용자가 액세스 패키지를 직접 요청할 수 있도록 허용 하는 정책을 정의할 수 있습니다. 승인이 필요한 지 여부와 액세스에 대 한 만료 날짜를 지정할 수 있습니다. 승인이 필요한 경우에는 외부 조직에서 하나 이상의 사용자를 디렉터리에 초대 하 고 해당 조직에서 액세스 해야 하는 외부 사용자를 알 가능성이 있으므로 승인자로 지정할 수도 있습니다. 액세스 패키지를 구성한 후에는 외부 조직의 사용자 (스폰서)에 게 액세스 패키지의 링크를 보낼 수 있습니다. 해당 연락처는 외부 조직의 다른 사용자와 공유할 수 있으며,이 링크를 사용 하 여 액세스 패키지를 요청할 수 있습니다. 디렉터리에 이미 초대 된 해당 조직의 사용자는 해당 링크를 사용할 수도 있습니다.

요청이 승인 되 면 자격 관리에서 사용자에 게 필요한 액세스를 프로 비전 합니다. 여기에는 사용자에 게 디렉터리에 아직 없는 경우 사용자 초대를 포함할 수 있습니다. Azure AD는이에 대 한 B2B 게스트 계정을 자동으로 만듭니다. 관리자는 [B2B 허용 또는 거부 목록을](../b2b/allow-deny-list.md) 다른 조직에 대 한 초대를 허용 하거나 차단 하도록 설정 하 여 공동 작업을 허용 하는 조직을 이전에 제한 했을 수 있습니다.  허용 또는 차단 목록에서 사용자를 허용 하지 않으면 해당 사용자는 초대 되지 않습니다.

외부 사용자의 액세스를 마지막으로 영원히 유지 하지 않으려면 정책에서 180 일 등의 만료 날짜를 지정 합니다. 180 일 후 액세스 권한이 확장 되지 않으면 권한 관리에서 해당 액세스 패키지와 연결 된 모든 액세스 권한을 제거 합니다. 기본적으로, 자격 관리를 통해 초대 된 사용자에 게 다른 액세스 패키지 할당이 없는 경우 마지막 할당이 손실 되 면 해당 게스트 계정은 30 일간 로그인이 차단 되 고 이후에 제거 됩니다. 이렇게 하면 불필요 한 계정의 확산을 방지할 수 있습니다. 다음 섹션에서 설명 하는 것 처럼 이러한 설정은 구성할 수 있습니다.

## <a name="how-access-works-for-external-users"></a>외부 사용자에 대 한 액세스 작동 방법

다음 다이어그램과 단계는 외부 사용자에 게 액세스 패키지에 대 한 액세스 권한을 부여 하는 방법에 대 한 개요를 제공 합니다.

![외부 사용자의 수명 주기를 보여 주는 다이어그램](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. 공동 작업 하려는 Azure AD 디렉터리 또는 도메인에 [연결 된 조직을 추가](entitlement-management-organization.md) 합니다.

1. 디렉터리에 [없는 사용자에 대 한](entitlement-management-access-package-create.md#for-users-not-in-your-directory)정책을 포함 하는 액세스 패키지를 디렉터리에 만듭니다.

1. 외부 조직의 사용자와 공유 하 여 액세스 패키지를 요청 하는 사용자와 공유할 수 있는 [내 액세스 포털 링크](entitlement-management-access-package-settings.md) 를 사용자에 게 보냅니다.

1. 외부 사용자 (이 예제의**경우 요청자 A** )는 내 액세스 포털 링크를 사용 하 여 액세스 패키지에 대 한 [액세스를 요청](entitlement-management-request-access.md) 합니다. 사용자가 로그인 하는 방법은 연결 된 조직에 정의 된 디렉터리 또는 도메인의 인증 유형에 따라 달라 집니다.

1. 승인자가 [요청을 승인](entitlement-management-request-approve.md) 하거나 요청이 자동 승인 됩니다.

1. 요청이 [배달 상태로](entitlement-management-process.md)전환 됩니다.

1. B2B 초대 프로세스를 사용 하 여 디렉터리에 게스트 사용자 계정이 생성 됩니다 (이 예제의 경우**요청자 a (게스트)** ). [허용 목록 또는 거부 목록을](../b2b/allow-deny-list.md) 정의 하면 목록 설정이 적용 됩니다.

1. 게스트 사용자에 게 액세스 패키지의 모든 리소스에 대 한 액세스 권한이 할당 됩니다. Azure AD 및 기타 Microsoft Online Services 또는 연결 된 SaaS 응용 프로그램에 변경 내용을 적용 하는 데 다소 시간이 걸릴 수 있습니다. 자세한 내용은 [변경 내용이 적용 되는 경우](entitlement-management-access-package-resources.md#when-changes-are-applied)를 참조 하세요.

1. 외부 사용자가 액세스를 [전달](entitlement-management-process.md)했음을 나타내는 전자 메일을 받습니다.

1. 리소스에 액세스 하기 위해 외부 사용자는 전자 메일의 링크를 클릭 하거나 디렉터리 리소스에 직접 액세스 하 여 초대 프로세스를 완료할 수 있습니다.

1. 시간 경과에 따라 정책 설정에 따라 외부 사용자에 대 한 액세스 패키지 할당이 만료 되며 외부 사용자의 액세스 권한이 제거 됩니다.

1. 외부 사용자 설정의 수명 주기에 따라 외부 사용자에 게 더 이상 액세스 패키지 할당이 없는 경우 외부 사용자의 로그인이 차단 되 고 게스트 사용자 계정이 디렉터리에서 제거 됩니다.

## <a name="settings-for-external-users"></a>외부 사용자에 대 한 설정

조직 외부 사용자가 액세스 패키지를 요청 하 고 해당 액세스 패키지의 리소스에 액세스할 수 있도록 하기 위해 확인 해야 하는 일부 설정이 제대로 구성 되어 있는지 확인 합니다.

### <a name="enable-catalog-for-external-users"></a>외부 사용자에 대해 카탈로그 사용

- 기본적으로 [새 카탈로그](entitlement-management-catalog-create.md)를 만들면 외부 사용자가 카탈로그에서 액세스 패키지를 요청할 수 있도록 설정 됩니다. **외부 사용자에 대해 사용** 이 **예**로 설정 되어 있는지 확인 합니다.

    ![카탈로그 설정 편집](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Azure AD B2B 외부 공동 작업 설정 구성

- 게스트가 다른 게스트를 디렉터리에 초대 하도록 허용 하면 게스트 초대가 자격 관리 외부에서 발생할 수 있음을 의미 합니다. 게스트가 적절 하 게 관리 되는 초대에만 **허용 하도록 설정** 하 **는** 것이 좋습니다.
- B2B 허용 목록을 사용 하는 경우 자격 관리를 사용 하 여 파트너 관계를 지정 하려는 도메인이 목록에 추가 되었는지 확인 해야 합니다. 또는 B2B 거부 목록을 사용 하는 경우 파트너 관계를 지정할 도메인이 목록에 추가 되지 않았는지 확인 해야 합니다.
- **모든 사용자** (연결 된 모든 조직 + 새 외부 사용자)에 대 한 권한 관리 정책을 만드는 경우 사용자가 보유 한 B2B 허용 또는 거부 목록 설정이 우선 적용 됩니다. 따라서이 정책에 포함 하려는 도메인을 사용 중인 경우 허용 목록에 포함 하 고, 거부 목록을 사용 하는 경우 거부 목록에서 제외 합니다.
- **모든 사용자** (연결 된 모든 조직 + 새 외부 사용자)를 포함 하는 자격 관리 정책을 만들려면 먼저 디렉터리에 대해 전자 메일 일회용 암호 인증을 사용 하도록 설정 해야 합니다. 자세한 내용은 [일회용 암호 인증 (미리 보기)](../b2b/one-time-passcode.md#opting-in-to-the-preview)을 참조 하세요.
- Azure AD B2B 외부 공동 작업 설정에 대 한 자세한 내용은 [b2b 외부 공동 작업 사용 및 게스트를 초대할 수 있는 사용자 관리](../b2b/delegate-invitations.md)를 참조 하세요.

    ![Azure AD 외부 공동 작업 설정](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>조건부 액세스 정책 검토

- 새 게스트 사용자가 자신의 디렉터리에 로그인 할 수 없도록 차단 하기 때문에 새 게스트 사용자가 충족할 수 없는 모든 조건부 액세스 정책에서 게스트를 제외 해야 합니다. 예를 들어 게스트에 등록 된 장치가 없고, 알려진 위치가 아니라, MFA (multi-factor authentication)를 다시 등록 하지 않으려는 경우, 이러한 요구 사항을 조건부 액세스 정책에 추가 하면 게스트에서 자격을 사용 하지 못하도록 차단 됩니다. wmi. 자세한 내용은 [조건부 액세스 Azure Active Directory의 조건 이란?](../conditional-access/concept-conditional-access-conditions.md)을 참조 하세요.

    ![Azure AD 조건부 액세스 정책 제외 설정](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>SharePoint Online 외부 공유 설정 검토

- 외부 사용자에 대 한 액세스 패키지에 SharePoint Online 사이트를 포함 하려면 조직 수준 외부 공유 설정이 **모든** 사용자 (로그인이 필요 하지 않음) 또는 **신규 및 기존 게스트** (게스트가 로그인 하거나 확인 코드를 제공 해야 함)로 설정 되어 있는지 확인 합니다. 자세한 내용은 [외부 공유 설정 또는 해제](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조 하세요.

- 자격 관리 외부에서 외부 공유를 제한 하려는 경우 외부 공유 설정을 **기존 게스트**로 설정할 수 있습니다. 그런 다음 자격 관리를 통해 초대 된 새 사용자만 이러한 사이트에 대 한 액세스 권한을 얻을 수 있습니다. 자세한 내용은 [외부 공유 설정 또는 해제](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조 하세요.

- 사이트 수준 설정에서 게스트 액세스를 사용할 수 있는지 확인 합니다 (이전에 나열 된 것과 동일한 옵션 선택). 자세한 내용은 [사이트의 외부 공유 설정 또는 해제](https://docs.microsoft.com/sharepoint/change-external-sharing-site)를 참조 하세요.

### <a name="review-your-office-365-group-sharing-settings"></a>Office 365 그룹 공유 설정 검토

- 외부 사용자에 대 한 액세스 패키지에 Office 365 그룹을 포함 하려는 경우 게스트 **액세스를 허용 하도록** **사용자가 조직에 새 게스트를 추가** 하도록 허용이 설정 되어 있는지 확인 합니다. 자세한 내용은 [Office 365 그룹에 대 한 게스트 액세스 관리](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups?view=o365-worldwide#manage-groups-guest-access)를 참조 하세요.

- 외부 사용자가 SharePoint Online 사이트 및 Office 365 그룹에 연결 된 리소스에 액세스할 수 있도록 하려면 SharePoint Online 외부 공유를 설정 해야 합니다. 자세한 내용은 [외부 공유 설정 또는 해제](https://docs.microsoft.com/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting)를 참조 하세요.

- PowerShell의 디렉터리 수준에서 Office 365 그룹에 대 한 게스트 정책을 설정 하는 방법에 대 한 자세한 내용은 [예제: 디렉터리 수준에서 그룹에 대 한 게스트 정책 구성](../users-groups-roles/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level)을 참조 하세요.

### <a name="review-your-teams-sharing-settings"></a>팀 공유 설정 검토

- 외부 사용자에 대 한 액세스 패키지에 팀을 포함 하려면 게스트 **액세스를 허용** 하도록 **Microsoft 팀의 게스트 액세스 허용** 이 설정 되어 있는지 확인 합니다. 자세한 내용은 [Microsoft 팀 관리 센터에서 게스트 액세스 구성](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center)을 참조 하세요.

## <a name="manage-the-lifecycle-of-external-users"></a>외부 사용자의 수명 주기 관리

승인 되는 액세스 패키지 요청을 통해 디렉터리에 초대 된 외부 사용자가 더 이상 액세스 패키지 할당을 보유 하지 않는 경우 수행할 작업을 선택할 수 있습니다. 사용자가 모든 액세스 패키지 할당을 내어 줍니다 마지막 액세스 패키지 할당이 만료 되는 경우이 문제가 발생할 수 있습니다. 기본적으로 외부 사용자에 게 더 이상 액세스 패키지 할당이 없는 경우 디렉터리에 로그인 하지 못하도록 차단 됩니다. 30 일 후에는 해당 게스트 사용자 계정이 디렉터리에서 제거 됩니다.

**필수 역할:** 전역 관리자 또는 사용자 관리자

1. Azure Portal에서 **Azure Active Directory**를 클릭한 다음, **Identity Governance**를 클릭합니다.

1. 왼쪽 메뉴의 **자격 관리** 섹션에서 **설정**을 클릭 합니다.

1. **편집**을 클릭합니다.

    ![외부 사용자의 수명 주기를 관리 하는 설정](./media/entitlement-management-external-users/settings-external-users.png)

1. **외부 사용자의 수명 주기 관리** 섹션에서 외부 사용자에 대 한 다른 설정을 선택 합니다.

1. 외부 사용자가 액세스 패키지에 대 한 마지막 할당을 잃은 후이 디렉터리에 로그인 하지 못하도록 차단 하려면 **외부 사용자가이 디렉터리에 로그인 하지 못하도록 차단** 을 **예**로 설정 합니다.

    > [!NOTE]
    > 사용자가이 디렉터리에 로그인 하지 못하도록 차단 되 면 사용자는 액세스 패키지를 다시 요청 하거나이 디렉터리에서 추가 액세스를 요청할 수 없습니다. 이후에 다른 액세스 패키지에 대 한 액세스를 요청 해야 하는 경우 로그인을 차단 하도록 구성 하지 마십시오.

1. 외부 사용자가 액세스 패키지에 대 한 마지막 할당을 잃은 후이 디렉터리에서 게스트 사용자 계정을 제거 하려면 **외부 사용자 제거** 를 **예**로 설정 합니다.

    > [!NOTE]
    > 자격 관리는 자격 관리를 통해 초대 된 계정만 제거 합니다. 또한 사용자가 패키지 할당에 액세스 하지 않은이 디렉터리의 리소스에 추가 된 경우에도 사용자가이 디렉터리에서 로그인 및 제거 되는 것을 차단 합니다. 게스트는 액세스 패키지 할당을 받기 전에이 디렉터리에 있는 경우 유지 됩니다. 그러나 액세스 패키지 할당을 통해 게스트를 초대 하 고 초대 된 후에도 비즈니스용 OneDrive 또는 SharePoint Online 사이트에 할당 된 경우에도 제거 됩니다.

1. 이 디렉터리에서 게스트 사용자 계정을 제거 하려면 제거 전 일 수를 설정할 수 있습니다. 액세스 패키지에 대 한 마지막 할당이 손실 되는 즉시 게스트 사용자 계정을 제거 하려면 **이 디렉터리에서 외부 사용자를 제거 하기 전까지 남은 일 수** 를 **0**으로 설정 합니다.

1. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

- [연결 된 조직 추가](entitlement-management-organization.md)
- [디렉터리에 없는 사용자의 경우](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [문제 해결](entitlement-management-troubleshoot.md)
