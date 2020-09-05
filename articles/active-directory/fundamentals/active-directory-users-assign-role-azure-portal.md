---
title: 사용자에 게 Azure AD 역할 할당-Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할을 할당하는 방법에 대한 지침입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e28da81400a97216836cfd9eccf0d4859ce0762f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89228225"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory를 사용하여 사용자에게 관리자 및 비관리자 역할 할당

Azure AD (Azure Active Directory)에서 사용자 중 한 명에 게 Azure AD 리소스를 관리할 수 있는 권한이 필요한 경우 필요한 권한을 제공 하는 역할에 할당 해야 합니다. Azure 리소스를 관리 하는 역할과 Azure AD 리소스를 관리 하는 역할에 대 한 정보는 [클래식 구독 관리자 역할, azure 역할 및 AZURE ad 역할](../../role-based-access-control/rbac-and-directory-admin-roles.md)을 참조 하세요.

사용 가능한 Azure AD 역할에 대 한 자세한 내용은 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)을 참조 하세요. 사용자를 추가 하려면 [Azure Active Directory에 새 사용자 추가](add-users-azure-active-directory.md)를 참조 하세요.

## <a name="assign-roles"></a>역할 할당

사용자에 게 Azure AD 역할을 할당 하는 일반적인 방법은 사용자에 **게 할당 된 역할** 페이지를 사용 하는 것입니다. 또한 PIM (Privileged Identity Management)을 사용 하 여 사용자 자격을 역할로 승격 되도록 구성할 수 있습니다. PIM을 사용 하는 방법에 대 한 자세한 내용은 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)를 참조 하세요.

> [!Note]
> Azure AD Premium P2 라이선스 계획이 있고 PIM을 이미 사용 하는 경우 모든 역할 관리 작업은 [Privileged Identity Management 환경](../users-groups-roles/directory-manage-roles-portal.md)에서 수행 됩니다. 이 기능은 현재 한 번에 하나의 역할만 할당 하도록 제한 되어 있습니다. 현재 여러 역할을 선택 하 고 사용자에 게 한 번에 할당할 수는 없습니다.
>
> ![이미 PIM을 사용 하 고 프리미엄 P2 라이선스가 있는 사용자를 위해 PIM에서 관리 되는 Azure AD 역할](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>사용자에 역할 할당

1. [Azure Portal](https://portal.azure.com/) 로 이동 하 여 디렉터리에 대 한 전역 관리자 계정을 사용 하 여 로그인 합니다.

2. **Azure Active Directory**를 검색하고 선택합니다.

      ![Azure Active Directory에 대한 Azure Portal 검색](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. **사용자**를 선택합니다.

4. 역할 할당을 가져올 사용자를 검색 하 고 선택 합니다. 예를 들어 _Alain Charon_입니다.

      ![모든 사용자 페이지-사용자를 선택 합니다.](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. **Alain Charon 프로필** 페이지에서 **할당 된 역할**을 선택 합니다.

    **Alain Charon-관리 역할** 페이지가 나타납니다.

6. **할당 추가**를 선택 하 고, alain (예: _응용 프로그램 관리자_)에 할당할 역할을 선택한 후 **선택**을 선택 합니다.

    ![할당 된 역할 페이지-선택한 역할을 표시 합니다.](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    응용 프로그램 관리자 역할은 Alain Charon에 할당 되 고 **Alain charon-관리 역할** 페이지에 표시 됩니다.

## <a name="remove-a-role-assignment"></a>역할 할당 제거

사용자의 역할 할당을 제거 해야 하는 경우에는 **Alain Charon-관리 역할** 페이지에서 수행할 수도 있습니다.

### <a name="to-remove-a-role-assignment-from-a-user"></a>사용자에서 역할 할당을 제거하려면

1. **Azure Active Directory**를 선택하고, **사용자**를 선택한 후, 역할 할당을 제거할 사용자를 검색하여 선택합니다. 예를 들어 _Alain Charon_입니다.

2. **할당 된 역할**을 선택 하 고 **응용 프로그램 관리자**를 선택한 후 **할당 제거**를 선택 합니다.

    ![할당 된 역할 페이지, 선택한 역할 및 제거 옵션 표시](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    응용 프로그램 관리자 역할은 Alain Charon에서 제거 되 고 **Alain charon-관리 역할** 페이지에 더 이상 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [사용자 추가 또는 삭제](add-users-azure-active-directory.md)

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [다른 디렉터리에서 게스트 사용자 추가](../b2b/what-is-b2b.md)

체크 아웃할 수 있는 다른 사용자 관리 작업은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)에서 제공 됩니다.
