---
title: 권한 있는 액세스 그룹에 적격 소유자 및 구성원 할당-Azure Active Directory
description: Azure AD Privileged Identity Management (PIM)에서 역할 할당 가능 그룹의 적격 소유자 또는 구성원을 할당 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06e61bb966313265f7e3772dbbf6017a03a42386
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869659"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Privileged Identity Management에서 권한 있는 액세스 그룹 (미리 보기)에 대 한 자격 할당

Azure Active Directory (Azure AD) Privileged Identity Management (PIM)은 Azure AD에서 권한 있는 액세스 그룹에 대 한 할당의 자격 및 활성화를 관리 하는 데 도움이 됩니다. 그룹의 멤버 또는 소유자에 게 자격을 할당할 수 있습니다.

>[!NOTE]
>권한 있는 액세스 그룹의 구성원 이거나 소유권을 받을 수 있는 모든 사용자에 게 Azure AD Premium P2 라이선스가 있어야 합니다. 자세한 내용은 [Privileged Identity Management 사용할 라이선스 요구 사항](subscription-requirements.md)을 참조 하세요.

## <a name="assign-an-owner-or-member-of-a-group"></a>그룹의 소유자 또는 구성원 할당

사용자가 권한 있는 액세스 그룹의 멤버 또는 소유자가 될 수 있도록 하려면 다음 단계를 수행 합니다.

1. 전역 관리자 또는 그룹 소유자 권한을 사용 하 여 [AZURE AD에 로그인](https://aad.portal.azure.com) 합니다.
1. **그룹** 을 선택 하 고 관리 하려는 역할 할당 가능 그룹을 선택 합니다. 목록을 검색 하거나 필터링 할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 그룹을 열고 권한 있는 **액세스 (미리 보기)** 를 선택 합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-assign-member-owner/groups-discover-groups.png)

1. **할당 추가**를 선택 합니다.

    ![새 할당 창](./media/groups-assign-member-owner/groups-add-assignment.png)

1. 권한 있는 액세스 그룹에 적격 할 구성원 또는 소유자를 선택 합니다.

    ![멤버 또는 그룹 선택 창](./media/groups-assign-member-owner/add-assignments.png)

1. **다음** 을 선택 하 여 멤버 자격 또는 소유권 기간을 설정 합니다.

    ![멤버 또는 그룹 선택 창](./media/groups-assign-member-owner/assignment-duration.png)

1. **할당 유형** 목록에서 **적격** 또는 **활성**을 선택합니다. 권한 있는 액세스 그룹은 다음과 같은 두 가지 고유한 할당 유형을 제공 합니다.

    - **적격** 할당을 사용 하려면 역할의 멤버가 역할을 사용 하는 작업을 수행 해야 합니다. 작업은 MFA(Multi-Factor Authentication) 검사를 수행하고, 비즈니스 근거를 제공하거나 지정된 승인자의 승인을 요청하는 과정을 포함할 수 있습니다.

    - **활성** 할당에는 멤버가 역할을 사용 하는 작업을 수행 하지 않아도 됩니다. 활성으로 할당된 멤버에게는 항상 역할에 할당된 권한이 있습니다.

1. 영구적으로 적격 하거나 영구적으로 할당 된 할당 인 경우 **영구적** 으로 확인란을 선택 합니다. 조직의 설정에 따라 확인란이 표시 되지 않거나 편집 하지 못할 수 있습니다.

1. 완료 되 면 **할당**을 선택 합니다.

1. 새 역할 할당을 만들려면 **추가**를 선택 합니다. 상태 알림이 표시됩니다.

    ![새 할당 - 알림](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>기존 역할 할당 업데이트 또는 제거

기존 역할 할당을 업데이트하거나 제거하려면 다음 단계를 수행합니다.

1. 전역 관리자 또는 그룹 소유자 권한을 사용 하 여 [AZURE AD에 로그인](https://aad.portal.azure.com) 합니다.
1. **그룹** 을 선택 하 고 관리 하려는 역할 할당 가능 그룹을 선택 합니다. 목록을 검색 하거나 필터링 할 수 있습니다.

    ![PIM에서 관리할 역할 할당 가능 그룹 찾기](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. 그룹을 열고 권한 있는 **액세스 (미리 보기)** 를 선택 합니다.

    ![Privileged Identity Management 환경 열기](./media/groups-assign-member-owner/groups-discover-groups.png)

1. 업데이트 또는 제거하려는 역할을 선택합니다.

1. **적격 역할** 또는 **활성 역할** 탭에서 역할 할당을 찾습니다.

    ![역할 할당 업데이트 또는 제거](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. **업데이트** 또는 **제거**를 선택하여 역할 할당을 업데이트하거나 제거합니다.

    역할 할당을 확장 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 확장 또는 갱신](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management에서 Azure AD 역할 할당](pim-how-to-add-role-to-user.md)
