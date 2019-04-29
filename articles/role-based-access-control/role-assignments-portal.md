---
title: RBAC 및 Azure portal을 사용 하 여 Azure 리소스에 대 한 액세스 관리 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어) 및 Azure Portal을 사용하여 사용자, 그룹, 서비스 주체 및 관리형 ID의 Azure 리소스 액세스를 관리하는 방법을 알아봅니다. 여기에는 액세스 권한을 나열, 부여 및 제거하는 방법이 포함됩니다.
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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: bb23cbc275e01eab5361504c547c020b0a29f4c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60533172"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-azure-portal"></a>RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스 관리

[RBAC(역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 문서에서는 Azure portal을 사용 하 여 액세스를 관리 하는 방법을 설명 합니다. Azure Active Directory에 대 한 액세스를 관리 하는 경우 참조 [Azure Active Directory에서 관리자 역할 할당 및 보기](../active-directory/users-groups-roles/directory-manage-roles-portal.md)합니다.

## <a name="prerequisites"></a>필수 조건

추가 역할 할당 제거 있어야 합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한, 같은 [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner)

## <a name="overview-of-access-control-iam"></a>액세스 제어 (IAM) 개요

**액세스 제어 (IAM)** 블레이드를 사용 하면 Azure 리소스에 대 한 액세스를 관리할입니다. Id 및 액세스 관리 라고도 하 고 Azure portal의 여러 위치에 표시 됩니다. 다음 구독에 대한 액세스 제어(IAM) 블레이드의 예를 보여줍니다.

![구독의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/access-control-numbers.png)

다음 표에서 어떤 요소 중 일부는 사용:

| # | 요소 | 용도 대해 |
| --- | --- | --- |
| 1 | 액세스 제어 (IAM)의 인스턴스가 열려 있는 리소스 | 범위 (이 예제의 구독)를 식별 합니다. |
| 2 | **추가** 단추 | 역할 할당 추가 |
| 3 | **액세스 권한 확인** 탭 | 단일 사용자에 대 한 역할 할당 보기 |
| 4 | **역할 할당** 탭 | 현재 범위에서 역할 할당 보기 |
| 5 | **역할** 탭 | 모든 역할 및 사용 권한 보기 |

액세스 제어 (IAM) 블레이드를 사용 하 여 가장 효과적인 되도록 하면 도움이 됩니다 액세스를 관리 하려는 경우에 다음 세 가지 질문을 파악할 수 있습니다.

1. **액세스는 누가 있습니까?**

    사용자, 그룹, 서비스 주체 또는 관리 되는 id를 참조합니다. 또한이 호출 됩니다는 *보안 주체*합니다.

1. **어떤 권한이 필요는?**

    사용 권한은 역할에 함께 그룹화 됩니다. 몇 가지 기본 제공 역할 목록에서 선택할 수 있습니다.

1. **여기서 액세스할 필요가 있습니까?**

    위치에 대 한 액세스를 적용 되는 리소스의 집합을 나타냅니다. 관리 그룹, 구독, 리소스 그룹 또는 저장소 계정과 같은 단일 리소스를 수 있습니다. 이 호출 되는 *범위*합니다.

## <a name="open-access-control-iam"></a>액세스 제어(IAM) 열기

액세스 제어 (IAM) 블레이드를 열려면 먼저 결정 해야입니다. 에 대 한 액세스를 관리 하려는 리소스에 따라 다릅니다. 리소스 그룹 또는 단일 리소스에서 모든 구독에서 모든 관리 그룹의 모든 항목에 대 한 액세스를 관리 하 시겠습니까?

1. Azure 포털에서 클릭 **모든 서비스** 다음 범위를 선택 합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭 합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음 구독에 대한 액세스 제어(IAM) 블레이드의 예를 보여줍니다. 여기에서 모든 액세스 제어 변경 한다면 적용 하는 것 전체 구독에 있습니다.

    ![구독의 액세스 제어(IAM) 블레이드](./media/role-assignments-portal/access-control-subscription.png)

## <a name="view-roles-and-permissions"></a>역할 및 권한 보기

역할 정의는 역할 할당에 사용되는 권한 컬렉션입니다. Azure는 [Azure 리소스에 대한 기본 제공 역할](built-in-roles.md)을 70개 넘게 보유하고 있습니다. 사용 가능한 역할 및 사용 권한을 보려면 다음이 단계를 수행 합니다.

1. 오픈 **액세스 제어 (IAM)** 모든 범위에 있습니다.

1. **역할** 탭을 클릭하여 모든 기본 제공 역할 및 사용자 지정 역할 목록을 봅니다.

   사용자 및 현재 범위에서 각 역할에 할당 된 그룹의 수를 볼 수 있습니다.

   ![역할 목록](./media/role-assignments-portal/roles-list.png)

1. 개별 역할을 클릭하여 이 역할에 할당된 사람을 보거나 해당 역할에 대한 권한을 봅니다.

   ![역할 할당](./media/role-assignments-portal/role-assignments.png)

## <a name="view-role-assignments"></a>역할 할당 보기

액세스 권한이 있는 사용자는 사용 권한 이란 알고 싶은 액세스를 관리 하는 경우 어떤 범위입니다. 사용자, 그룹, 서비스 주체 또는 관리 서비스 id에 대 한 목록 액세스는 역할 할당을 봅니다.

### <a name="view-role-assignments-for-a-single-user"></a>단일 사용자의 역할 할당 보기

다음 단계에 따라 특정 범위에서 단일 사용자, 그룹, 서비스 주체 또는 관리 ID의 액세스 권한을 볼 수 있습니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 보려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-portal/access-control-check-access.png)

1. **찾기** 목록에서 액세스 권한을 확인하려는 보안 주체의 유형을 선택합니다.

1. 검색 상자에서 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/role-assignments-portal/check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    ![할당 창](./media/role-assignments-portal/check-access-assignments.png)

    이 창에서 선택한 보안 주체 및 범위에 할당된 역할을 볼 수 있습니다. 이 범위 또는 이 범위로 상속된 거부 할당이 있는 경우 목록으로 표시됩니다.

### <a name="view-all-role-assignments-at-a-scope"></a>특정 범위의 모든 역할 할당 보기

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 보려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-portal/access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스**이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="add-a-role-assignment"></a>역할 할당 추가

RBAC에서 액세스 권한을 부여하려면 사용자, 그룹, 서비스 주체 또는 관리 ID에 역할을 할당합니다. 다음 단계를 수행하여 서로 다른 범위에서 액세스 권한을 부여합니다.

### <a name="assign-a-role-at-a-scope"></a>특정 범위에서 역할 할당

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 부여하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

1. **추가** > **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **선택** 목록에서 사용자, 그룹, 서비스 주체 또는 관리 ID르 선택합니다. 목록에 보안 주체가 보이지 않으면 **선택** 상자에 직접 입력하여 표시 이름, 이메일 주소 및 개체 식별자에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

### <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독 관리자로서 사용자 할당

다른 사용자를 Azure 구독의 관리자로 추가하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. 소유자 역할은 다른 사용자에게 액세스를 위임할 수 있는 권한을 비롯한 구독의 리소스에 대한 모든 권한을 사용자에게 부여할 수 있습니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 **모든 서비스**, **구독**을 차례로 클릭합니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. **추가** > **역할 할당 추가**를 클릭하여 역할 할당 추가 창을 엽니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 메뉴](./media/role-assignments-portal/add-menu.png)

   ![역할 할당 추가 창](./media/role-assignments-portal/add-role-assignment.png)

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

1. **저장**을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="remove-role-assignments"></a>역할 할당 제거

RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다. 다음 단계에 따라 액세스 권한을 제거합니다.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 액세스 권한을 제거하려는 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서, 제거할 역할 할당이 있는 보안 주체 옆에 확인란을 추가합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-select.png)

1. **제거**를 클릭합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예**를 클릭합니다.

    상속된 역할 할당은 제거할 수 없습니다. 상속된 역할 할당을 제거하려면 역할 할당이 만들어진 범위에서 역할 할당을 제거해야 합니다. **범위** 열에서 **(상속됨)** 옆쪽에 이 역할이 할당된 범위로 이동하는 링크가 있습니다. 나열된 범위로 이동하여 역할 할당을 제거합니다.

   ![역할 할당 제거 메시지](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>다음 단계

* [자습서: RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](quickstart-assign-role-user-portal.md)
* [자습서: RBAC 및 Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)
* [Azure 리소스에 대한 RBAC 문제 해결](troubleshooting.md)
* [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/index.md)
