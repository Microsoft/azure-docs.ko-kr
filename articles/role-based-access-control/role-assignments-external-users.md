---
title: RBAC 및 Azure Portal를 사용 하 여 외부 사용자에 대 한 역할 할당 추가 또는 제거
description: Azure 역할 기반 액세스 제어 (RBAC)를 사용 하 여 조직 외부 사용자를 위해 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138372"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC 및 Azure Portal를 사용 하 여 외부 게스트 사용자에 대 한 역할 할당 추가 또는 제거

[Azure 역할 기반 access control (RBAC)](overview.md) 을 사용 하면 사용자 환경의 특정 리소스에 액세스 해야 하지만 전체 인프라 또는 청구 관련 범위에 대 한 액세스를 필요로 하는 외부 협력자, 공급 업체 또는 프리랜서에서 작업 하는 중소기업 및 중소기업에 대해 더 나은 보안 관리가 가능 합니다. [AZURE ACTIVE DIRECTORY B2B](../active-directory/b2b/what-is-b2b.md) 의 기능을 사용 하 여 외부 게스트 사용자와 공동 작업을 수행할 수 있으며 RBAC를 사용 하 여 사용자 환경에서 게스트 사용자에 게 필요한 권한만 부여할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="when-would-you-invite-guest-users"></a>언제 게스트 사용자를 초대 하나요?

다음은 조직에 게스트 사용자를 초대 하 고 권한을 부여할 수 있는 몇 가지 예제 시나리오입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 외부 파트너가 특정 리소스 또는 전체 구독을 관리할 수 있도록 허용 합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="permission-differences-between-member-users-and-guest-users"></a>멤버 사용자와 게스트 사용자 간의 권한 차이

디렉터리의 네이티브 멤버 (멤버 사용자)는 다른 디렉터리에서 B2B 공동 작업 게스트 (게스트 사용자)로 초대한 사용자와는 다른 권한을 갖습니다. 예를 들어, 멤버 사용자는 게스트 사용자가 디렉터리 권한을 제한 하는 동안 거의 모든 디렉터리 정보를 읽을 수 있습니다. 멤버 사용자 및 게스트 사용자에 대 한 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한은 무엇입니까?](../active-directory/fundamentals/users-default-permissions.md)를 참조 하십시오.

## <a name="add-a-guest-user-to-your-directory"></a>디렉터리에 게스트 사용자 추가

Azure Active Directory 페이지를 사용 하 여 디렉터리에 게스트 사용자를 추가 하려면 다음 단계를 수행 합니다.

1. 게스트 초대를 허용 하도록 조직의 외부 공동 작업 설정이 구성 되어 있는지 확인 합니다. 자세한 내용은 [B2B 외부 공동 작업 사용 및 게스트를 초대할 수 있는 사용자 관리](../active-directory/b2b/delegate-invitations.md)를 참조 하세요.

1. Azure Portal에서 **Azure Active Directory** > **사용자** > **새 게스트 사용자**를 클릭 합니다.

    ![Azure Portal의 새 게스트 사용자 기능](./media/role-assignments-external-users/invite-guest-user.png)

1. 새 게스트 사용자를 추가 하는 단계를 수행 합니다. 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory)를 참조 하세요.

게스트 사용자를 디렉터리에 추가한 후에 게스트 사용자에게 공유 앱에 대한 직접 링크를 보낼 수 있습니다. 또는 게스트 사용자는 초대 이메일에서 상환 URL을 클릭할 수 있습니다.

![게스트 사용자 초대 전자 메일](./media/role-assignments-external-users/invite-email.png)

게스트 사용자가 디렉터리에 액세스할 수 있도록 초대 프로세스를 완료 해야 합니다.

![게스트 사용자 초대 검토 권한](./media/role-assignments-external-users/invite-review-permissions.png)

초대 프로세스에 대 한 자세한 내용은 [B2B 공동 작업 초대 상환 Azure Active Directory](../active-directory/b2b/redemption-experience.md)를 참조 하세요.

## <a name="add-a-role-assignment-for-a-guest-user"></a>게스트 사용자에 대 한 역할 할당 추가

RBAC에서 액세스 권한을 부여하기 위해 역할을 할당합니다. 게스트 사용자에 대 한 역할 할당을 추가 하려면 멤버 사용자, 그룹, 서비스 주체 또는 관리 id와 [동일한 단계](role-assignments-portal.md#add-a-role-assignment) 를 수행 합니다. 다른 범위에서 게스트 사용자에 대 한 역할 할당을 추가 하려면 다음 단계를 따르세요.

1. Azure Portal에서 **모든 서비스**를 클릭합니다.

1.  액세스가 적용 되는 리소스 집합 (범위 라고도 함)을 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음 스크린샷은 리소스 그룹에 대 한 액세스 제어 (IAM) 블레이드의 예를 보여 줍니다. 여기에서 액세스 제어를 변경 하는 경우 리소스 그룹에만 적용 됩니다.

    ![리소스 그룹의 액세스 제어(IAM) 블레이드](./media/role-assignments-external-users/access-control-resource-group.png)

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. **추가** > **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

    역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

    ![추가 메뉴](./media/role-assignments-external-users/add-menu.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 게스트 사용자를 선택 합니다. 목록에 사용자가 표시 되지 않으면 **선택** 상자에를 입력 하 여 표시 이름, 전자 메일 주소 및 개체 식별자에 대 한 디렉터리를 검색할 수 있습니다.

   ![역할 할당 추가 창](./media/role-assignments-external-users/add-role-assignment.png)

1. **저장** 을 클릭 하 여 선택한 범위에서 역할을 할당 합니다.

    ![가상 컴퓨터 참가자에 대 한 역할 할당](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>디렉터리에 아직 없는 게스트 사용자에 대 한 역할 할당 추가

게스트 사용자에 대 한 역할 할당을 추가 하려면 멤버 사용자, 그룹, 서비스 주체 또는 관리 id와 [동일한 단계](role-assignments-portal.md#add-a-role-assignment) 를 수행 합니다.

게스트 사용자가 디렉터리에 아직 없는 경우 역할 할당 추가 창에서 직접 사용자를 초대할 수 있습니다.

1. Azure Portal에서 **모든 서비스**를 클릭합니다.

1.  액세스가 적용 되는 리소스 집합 (범위 라고도 함)을 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. **추가** > **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

    ![추가 메뉴](./media/role-assignments-external-users/add-menu.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 초대 하려는 사람의 전자 메일 주소를 입력 하 고 해당 사용자를 선택 합니다.

   ![역할 할당 추가 창에서 게스트 사용자 초대](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. **저장** 을 클릭 하 여 게스트 사용자를 디렉터리에 추가 하 고, 역할을 할당 하 고, 초대를 보냅니다.

    잠시 후에 역할 할당에 대 한 알림과 초대에 대 한 정보를 볼 수 있습니다.

    ![역할 할당 및 초대 받은 사용자 알림](./media/role-assignments-external-users/invited-user-notification.png)

1. 게스트 사용자를 수동으로 초대 하려면 마우스 오른쪽 단추를 클릭 하 고 알림에서 초대 링크를 복사 합니다. 초대 프로세스를 시작 하기 때문에 초대 링크를 클릭 하지 마세요.

    초대 링크에는 다음과 같은 형식이 있습니다.

    `https://invitations.microsoft.com/redeem/...`

1. 초대 프로세스를 완료 하기 위해 게스트 사용자에 게 초대 링크를 보냅니다.

    초대 프로세스에 대 한 자세한 내용은 [B2B 공동 작업 초대 상환 Azure Active Directory](../active-directory/b2b/redemption-experience.md)를 참조 하세요.

## <a name="remove-a-guest-user-from-your-directory"></a>디렉터리에서 게스트 사용자 제거

디렉터리에서 게스트 사용자를 제거 하기 전에 먼저 해당 게스트 사용자에 대 한 역할 할당을 제거 해야 합니다. 디렉터리에서 게스트 사용자를 제거 하려면 다음 단계를 수행 합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스와 같이 게스트 사용자에 게 역할 할당이 있는 범위에서 **액세스 제어 (IAM)** 를 엽니다.

1. 역할 **할당** 탭을 클릭 하 여 모든 역할 할당을 표시 합니다.

1. 역할 할당 목록에서 제거 하려는 역할 할당을 사용 하 여 게스트 사용자 옆에 있는 확인 표시를 추가 합니다.

   ![역할 할당 제거](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-external-users/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

1. 왼쪽 탐색 모음에서 **Azure Active Directory** > **사용자**를 클릭 합니다.

1. 제거 하려는 게스트 사용자를 클릭 합니다.

1. **삭제**를 클릭합니다.

   ![게스트 사용자 삭제](./media/role-assignments-external-users/delete-guest-user.png)

1. 삭제 메시지가 표시 되 면 **예**를 클릭 합니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="guest-user-cannot-browse-the-directory"></a>게스트 사용자가 디렉터리를 찾을 수 없습니다.

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 예를 들어 게스트 사용자는 디렉터리를 검색할 수 없으며 그룹이 나 응용 프로그램을 검색할 수 없습니다. 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한은 무엇입니까?](../active-directory/fundamentals/users-default-permissions.md)를 참조 하세요.

![게스트 사용자가 디렉터리의 사용자를 찾을 수 없음](./media/role-assignments-external-users/directory-no-users.png)

게스트 사용자에 게 디렉터리에서 추가 권한이 필요한 경우 게스트 사용자에 게 디렉터리 역할을 할당할 수 있습니다. 게스트 사용자에 게 디렉터리에 대 한 전체 읽기 액세스 권한을 원하는 경우 Azure AD의 [디렉터리 독자](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Active Directory 테 넌 트의 파트너 조직에서 사용자에 게 권한 부여](../active-directory/b2b/add-guest-to-role.md)를 참조 하세요.

![디렉터리 판독기 역할 할당](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>게스트 사용자가 역할을 할당 하는 사용자, 그룹 또는 서비스 사용자를 찾을 수 없습니다.

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 게스트 사용자가 범위에서 [소유자](built-in-roles.md#owner) 인 경우에도 다른 사용자에 게 액세스 권한을 부여 하는 역할 할당을 추가 하려고 하면 사용자, 그룹 또는 서비스 사용자의 목록을 검색할 수 없습니다.

![게스트 사용자가 보안 주체를 검색 하 여 역할을 할당할 수 없음](./media/role-assignments-external-users/directory-no-browse.png)

게스트 사용자가 디렉터리에서 사람의 정확한 로그인 이름을 알고 있는 경우 액세스 권한을 부여할 수 있습니다. 게스트 사용자에 게 디렉터리에 대 한 전체 읽기 액세스 권한을 원하는 경우 Azure AD의 [디렉터리 독자](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Active Directory 테 넌 트의 파트너 조직에서 사용자에 게 권한 부여](../active-directory/b2b/add-guest-to-role.md)를 참조 하세요.

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>게스트 사용자가 응용 프로그램을 등록 하거나 서비스 사용자를 만들 수 없음

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 게스트 사용자가 응용 프로그램을 등록 하거나 서비스 사용자를 만들 수 있어야 하는 경우 Azure AD의 [응용 프로그램 개발자](../active-directory/users-groups-roles/directory-assign-admin-roles.md) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Active Directory 테 넌 트의 파트너 조직에서 사용자에 게 권한 부여](../active-directory/b2b/add-guest-to-role.md)를 참조 하세요.

![게스트 사용자가 응용 프로그램을 등록할 수 없음](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>게스트 사용자에 게 새 디렉터리가 표시 되지 않습니다.

게스트 사용자에 게 디렉터리에 대 한 액세스 권한이 부여 되었지만 **디렉터리 + 구독** 창에서 전환 하려고 할 때 Azure Portal에 나열 된 새 디렉터리가 표시 되지 않으면 게스트 사용자가 초대 프로세스를 완료 했는지 확인 합니다. 초대 프로세스에 대 한 자세한 내용은 [B2B 공동 작업 초대 상환 Azure Active Directory](../active-directory/b2b/redemption-experience.md)를 참조 하세요.

### <a name="guest-user-does-not-see-resources"></a>게스트 사용자가 리소스를 볼 수 없음

게스트 사용자에 게 디렉터리에 대 한 액세스 권한이 부여 되었지만 해당 사용자에 게 Azure Portal에 대 한 액세스 권한이 부여 된 리소스가 표시 되지 않으면 게스트 사용자가 올바른 디렉터리를 선택 했는지 확인 합니다. 게스트 사용자는 여러 디렉터리에 액세스할 수 있습니다. 디렉터리를 전환 하려면 왼쪽 위에서 **디렉터리 + 구독**을 클릭 한 다음 해당 디렉터리를 클릭 합니다.

![Azure Portal의 디렉터리 + 구독 창](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure Active Directory B2B Collaboration 사용자 추가](../active-directory/b2b/add-users-administrator.md)
- [Azure Active Directory B2B 협업 사용자 속성](../active-directory/b2b/user-properties.md)
- [B2B 공동 작업 초대 전자 메일의 요소-Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [게스트 사용자를 공동 관리자로 추가](classic-administrators.md#add-a-guest-user-as-a-co-administrator)