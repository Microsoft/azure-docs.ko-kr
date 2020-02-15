---
title: RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거
description: Azure RBAC (역할 기반 액세스 제어) 및 Azure Portal를 사용 하 여 사용자, 그룹, 서비스 주체 또는 관리 되는 id에 대 한 Azure 리소스에 대 한 액세스 권한을 부여 하는 방법에 대해 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e2458b07602bca3b6f12b2f486b509c11d705461
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138340"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 추가 또는 제거

이 문서 [!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)] Azure Portal를 사용 하 여 역할을 할당 하는 방법을 설명 합니다.

Azure Active Directory에서 관리자 역할을 할당 해야 하는 경우 [Azure Active Directory에서 관리자 역할 보기 및 할당](../active-directory/users-groups-roles/directory-manage-roles-portal.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

역할 할당을 추가 하거나 제거 하려면 다음을 수행 해야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="access-control-iam"></a>액세스 제어(IAM)

**액세스 제어 (IAM)** 는 Azure 리소스에 대 한 액세스 권한을 부여 하기 위해 역할을 할당 하는 데 사용 하는 블레이드입니다. Id 및 액세스 관리 라고도 하며 Azure Portal의 여러 위치에 표시 됩니다. 다음 구독에 대한 액세스 제어(IAM) 블레이드의 예를 보여줍니다.

![구독의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/access-control-subscription.png)

액세스 제어 (IAM) 블레이드에서 가장 효과적으로 사용 하려면 역할을 할당 하려고 할 때 다음 세 가지 질문에 답할 수 있습니다.

1. **누가 액세스 해야 하나요?**

    사용자, 그룹, 서비스 주체 또는 관리 id를 참조 하는 사용자 이를 *보안 주체*라고도 합니다.

1. **어떤 역할이 필요 한가요?**

    사용 권한은 역할에 함께 그룹화 됩니다. 여러 [기본 제공 역할](built-in-roles.md) 목록에서 선택 하거나 고유한 사용자 지정 역할을 사용할 수 있습니다.

1. **어디서 액세스 해야 하나요?**

    여기서는 액세스가 적용 되는 리소스 집합을 나타냅니다. 여기서는 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스 (예: 저장소 계정) 일 수 있습니다. 이를 *범위*라고 합니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 부여 하려면 역할 할당을 추가 합니다. 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭 하 고 액세스 권한을 부여할 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 해당 범위에 대 한 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. 이 범위에서 역할 할당을 보려면 **역할 할당** 탭을 클릭 합니다.

    ![액세스 제어 (IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. **추가** > **역할 할당 추가**를 클릭 합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

    ![역할 할당 추가 저장 됨](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독 관리자로서 사용자 할당

특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할은 사용자에 게 다른 사용자에 게 액세스 권한을 부여할 수 있는 권한을 포함 하 여 구독에 있는 모든 리소스에 대 한 모든 권한을 부여 합니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭 하 여이 구독에 대 한 역할 할당을 확인 합니다.

    ![액세스 제어 (IAM) 및 역할 할당 탭](./media/role-assignments-portal/role-assignments.png)

1. **추가** > **역할 할당 추가**를 클릭 합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

Azure RBAC에서 Azure 리소스에 대 한 액세스 권한을 제거 하려면 역할 할당을 제거 합니다. 역할 할당을 제거 하려면 다음 단계를 수행 합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

    상속 된 역할 할당을 제거할 수 없다는 메시지가 표시 되 면 자식 범위에서 역할 할당을 제거 하려고 시도 하는 것입니다. 역할이 할당 된 범위에서 액세스 제어 (IAM)를 열고 다시 시도 해야 합니다. 올바른 범위에서 액세스 제어 (IAM)를 빠르게 여는 것은 **범위** 열을 확인 하 고 **(상속 됨)** 옆의 링크를 클릭 하는 것입니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

- [Azure RBAC 및 Azure Portal를 사용 하 여 역할 할당 나열](role-assignments-list-portal.md)
- [자습서: RBAC 및 Azure Portal를 사용 하 여 Azure 리소스에 대 한 사용자 액세스 권한 부여](quickstart-assign-role-user-portal.md)
- [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)
- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)
