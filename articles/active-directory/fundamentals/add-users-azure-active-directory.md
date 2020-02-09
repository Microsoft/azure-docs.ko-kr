---
title: 사용자 추가 또는 삭제 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory를 사용하여 새 사용자를 추가하거나 기존 사용자를 삭제하는 방법에 대한 지침입니다.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073488"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Azure Active Directory를 사용한 사용자 추가 또는 삭제

새 사용자를 추가 하거나 Azure Active Directory (Azure AD) 조직에서 기존 사용자를 삭제 합니다. 사용자를 추가 하거나 삭제 하려면 사용자 관리자 또는 전역 관리자 여야 합니다.

## <a name="add-a-new-user"></a>새 사용자 추가

Azure Active Directory 포털을 사용하여 새 사용자를 만들 수 있습니다.

새 사용자를 추가 하려면 다음 단계를 수행 합니다.

1. 조직의 사용자 관리자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색 하 고 선택 합니다.

1. **사용자**를 선택한 다음 **새 사용자**를 선택 합니다.

    ![사용자를 통해 사용자 추가-Azure AD의 모든 사용자](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. **사용자** 페이지에서이 사용자에 대 한 정보를 입력 합니다.

   - **이름**. 필수 요소. 새 사용자의 이름 및 성입니다. *Mary Parker*를 예를 들겠습니다.

   - **사용자 이름**입니다. 필수 요소. 새 사용자의 사용자 이름입니다. 예: `mary@contoso.com`.

     사용자 이름의 도메인 부분에는 초기 기본 도메인 이름,\<도메인 이름 *>* 를 사용 하거나 사용자 지정 도메인 이름 (예: *contoso.com*) 중 하나를 사용 해야 합니다. 사용자 지정 도메인 이름을 만드는 방법에 대 한 자세한 내용은 [Azure Active Directory 포털을 사용 하 여 사용자 지정 도메인 이름 추가](add-custom-domain.md)를 참조 하세요.

   - **그룹** - 원할 경우, 하나 이상의 기존 그룹에 사용자를 추가할 수 있습니다. 사용자를 나중에 그룹에 추가할 수도 있습니다. 그룹에 사용자를 추가 하는 방법에 대 한 자세한 내용은 [기본 그룹 만들기 및 Azure Active Directory를 사용 하 여 구성원 추가](active-directory-groups-create-azure-portal.md)를 참조 하세요.

   - **디렉터리 역할**: 사용자에 대 한 azure ad 관리 권한이 필요한 경우 azure ad 역할에 추가할 수 있습니다. 사용자를 전역 관리자 또는 Azure AD의 제한 된 관리자 역할 중 하나 이상으로 할당할 수 있습니다. 역할 할당에 대한 자세한 내용은 [사용자에게 역할을 할당하는 방법](active-directory-users-assign-role-azure-portal.md)을 참조하세요.

   - **작업 정보**: 여기에서 사용자에 대 한 추가 정보를 추가 하거나 나중에 수행할 수 있습니다. 사용자 정보 추가에 대한 자세한 내용은 [사용자 프로필 정보를 추가하거나 변경하는 방법](active-directory-users-profile-azure-portal.md)을 참조하세요.

1. **암호** 상자에 제공 된 자동 생성 된 암호를 복사 합니다. 사용자가 처음으로 로그인 하려면이 암호를 제공 해야 합니다.

1. **만들기**를 선택합니다.

사용자가 만들어지고 Azure AD 조직에 추가 됩니다.

## <a name="add-a-new-guest-user"></a>새 게스트 사용자 추가

새 **사용자** 페이지에서 **사용자 초대** 를 선택 하 여 새 게스트 사용자를 조직과 공동 작업 하도록 초대할 수도 있습니다. 회사의 외부 공동 작업 설정이 게스트 초대를 허용 하도록 구성 된 경우 사용자는 공동 작업을 시작 하는 데 동의 해야 하는 초대를 메일로 받게 됩니다. B2B 공동 작업 사용자를 초대 하는 방법에 대 한 자세한 내용은 [Azure Active Directory에 b2b 사용자 초대](../b2b/add-users-administrator.md) 를 참조 하세요.

## <a name="add-a-consumer-user"></a>소비자 사용자 추가

Azure Active Directory B2C (Azure AD B2C) 디렉터리에서 수동으로 소비자 계정을 만들려는 시나리오가 있을 수 있습니다. 소비자 계정을 만드는 방법에 대 한 자세한 내용은 [Azure AD B2C에서 소비자 사용자 만들기 및 삭제](../../active-directory-b2c/manage-users-portal.md)를 참조 하세요.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>하이브리드 환경 내에 새 사용자 추가

Azure Active Directory(클라우드)와 Windows Server Active Directory(온-프레미스)를 모두 사용하는 환경이 있는 경우 기존 사용자 계정 데이터를 동기화하여 새 사용자를 추가할 수 있습니다. 하이브리드 환경에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 디렉터리 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

## <a name="delete-a-user"></a>사용자 삭제

Azure Active Directory 포털을 사용하여 기존 사용자를 삭제할 수 있습니다.

사용자를 삭제 하려면 다음 단계를 수행 합니다.

1. 조직의 사용자 관리자 계정을 사용 하 여 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. 모든 페이지에서 *Azure Active Directory* 를 검색 하 고 선택 합니다.

1. Azure AD 테넌트에서 삭제할 사용자를 검색 하 고 선택 합니다. _Mary Parker_를 예를 들겠습니다.

1. **사용자 삭제**를 선택합니다.

    ![사용자 - 사용자 삭제가 강조 표시된 모든 사용자 페이지](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

사용자가 삭제되고 **사용자 - 모든 사용자** 페이지에 더 이상 표시되지 않습니다. 이 사용자는 다음 30일 동안 **삭제된 사용자** 페이지에서 볼 수 있으며 해당 기간 복원할 수 있습니다. 사용자를 복원 하는 방법에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 최근에 삭제 된 사용자 복원 또는 제거](active-directory-users-restore.md)를 참조 하세요.

사용자를 삭제 하면 해당 사용자가 사용 하는 모든 라이선스를 다른 사용자가 사용할 수 있게 됩니다.

>[!Note]
>Windows server Active Directory를 사용 하 여 인증 원본이 Windows Server Active Directory 인 사용자에 대 한 id, 연락처 정보 또는 작업 정보를 업데이트 해야 합니다. 업데이트를 완료하면 변경 내용이 표시되기 전에 다음 동기화 주기가 완료될 때까지 기다려야 합니다.

## <a name="next-steps"></a>다음 단계

사용자를 추가한 후에는 다음과 같은 기본 프로세스를 수행할 수 있습니다.

- [프로필 정보 추가 또는 변경](active-directory-users-profile-azure-portal.md)

- [사용자에게 역할 할당](active-directory-users-assign-role-azure-portal.md)

- [기본 그룹 만들기 및 멤버 추가](active-directory-groups-create-azure-portal.md)

- [동적 그룹 및 사용자와 함께 작업](../users-groups-roles/groups-create-rule.md)

또는 [다른 디렉터리에서 게스트 사용자를 추가](../b2b/what-is-b2b.md) 하거나 [삭제 된 사용자를 복원](active-directory-users-restore.md)하는 등의 다른 사용자 관리 작업을 수행할 수 있습니다. 사용 가능한 다른 작업에 대한 자세한 내용은 [Azure Active Directory 사용자 관리 설명서](../users-groups-roles/index.yml)를 참조하세요.
