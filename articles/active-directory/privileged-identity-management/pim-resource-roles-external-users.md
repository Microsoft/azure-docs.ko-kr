---
title: PIM에서 게스트에 Azure 리소스 역할 할당-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 외부 게스트 사용자를 초대 하 고 Azure 리소스 역할을 할당 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763441d023e4a707c6a1edc09abfb6d8e5525723
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782739"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 게스트 사용자를 초대 하 고 Azure 리소스 역할을 할당 합니다.

Azure ad (Azure Active Directory) 게스트 사용자는 azure ad 내에서 B2B (기업 간) 공동 작업 기능의 일부로, 외부 게스트 사용자 및 공급 업체를 Azure AD의 게스트로 관리할 수 있습니다. B2B 공동 작업을 Azure AD Privileged Identity Management (PIM)와 결합 하는 경우 게스트에 대 한 규정 준수 및 거 버 넌 스 요구 사항을 확장할 수 있습니다. 예를 들어 게스트를 사용 하 여 Azure id 작업에 이러한 Privileged Identity Management 기능을 사용할 수 있습니다.

- 특정 Azure 리소스에 대한 액세스 할당
- Just-In-Time VM 액세스 사용
- 할당 기간 및 종료 날짜 지정
- 활성 할당 또는 활성화에 대 한 multi-factor authentication 필요
- 액세스 검토 수행
- 경고 및 감사 로그 활용

이 문서에서는 조직에 게스트를 초대 하 고 Privileged Identity Management를 사용 하 여 Azure 리소스에 대 한 액세스를 관리 하는 방법을 설명 합니다.

## <a name="when-would-you-invite-guests"></a>언제 게스트를 초대 하나요?

다음은 조직에 게스트를 초대할 수 있는 경우의 몇 가지 예입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 온-프레미스 Active Directory Federation Services를 사용하는 대규모 조직의 외부 사용자가 비용 애플리케이션에 액세스할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>B2B 게스트를 사용 하는 공동 작업은 어떻게 작동 하나요?

B2B 공동 작업을 사용 하는 경우 외부 사용자를 조직에 게스트로 초대할 수 있습니다. 게스트는 조직의 사용자로 관리 될 수 있지만, 게스트는 Azure AD 조직이 아니라 홈 조직에서 인증 되어야 합니다. 즉, 게스트가 자신의 홈 조직에 더 이상 액세스할 수 없는 경우 조직에도 액세스할 수 없게 됩니다. 예를 들어 게스트가 조직을 벗어나면 아무 작업도 수행 하지 않고도 Azure AD에서 공유 하는 리소스에 대 한 액세스 권한이 자동으로 손실 됩니다. B2B 공동 작업에 대 한 자세한 내용은 [AZURE ACTIVE DIRECTORY b2b의 게스트 사용자 액세스 란?](../external-identities/what-is-b2b.md)을 참조 하세요.

![홈 디렉터리에서 게스트 사용자를 인증 하는 방법을 보여 주는 다이어그램](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>게스트 공동 작업 설정 확인

게스트를 조직에 초대할 수 있도록 하려면 게스트 공동 작업 설정을 확인 해야 합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다.

1. **Azure Active Directory**  >  **사용자 설정**을 선택 합니다.

1. **외부 공동 작업 설정 관리**를 선택 합니다.

    ![권한, 초대 및 공동 작업 제한 설정이 표시 되는 외부 공동 작업 설정 페이지](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. **게스트 초대자 역할의 관리자 및 사용자가 초대 가능** 스위치를 **예**로 설정 했는지 확인 합니다.

## <a name="invite-a-guest-and-assign-a-role"></a>게스트를 초대 하 고 역할 할당

Privileged Identity Management를 사용 하 여 게스트를 초대 하 고 Azure 리소스 역할에 적합 하 게 만들 수 있습니다.

1. [권한 있는 역할 관리자](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 또는 [사용자 관리자](../users-groups-roles/directory-assign-admin-roles.md#user-administrator) 역할의 구성원 인 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.

1. **Azure AD Privileged Identity Management**를 엽니다.

1. **Azure 리소스**를 선택 합니다.

1. **리소스 필터**를 사용하여 관리되는 리소스의 목록을 필터링합니다.

1. 리소스, 리소스 그룹, 구독 또는 관리 그룹과 같이 관리 하려는 리소스를 선택 합니다.

    이 범위는 게스트에 필요한 것 으로만 설정 해야 합니다.

1. 관리 아래에서 **역할** 을 선택 하 여 Azure 리소스에 대 한 역할 목록을 표시 합니다.

    ![활성 및 적격 사용자 수를 보여 주는 Azure 리소스 역할 목록](./media/pim-resource-roles-external-users/resources-roles.png)

1. 사용자에 게 필요한 최소 역할을 선택 합니다.

    ![선택한 역할 페이지에 해당 역할의 현재 멤버가 나열 됩니다.](./media/pim-resource-roles-external-users/selected-role.png)

1. 역할 페이지에서 **구성원 추가** 를 선택 하 여 새 할당 창을 엽니다.

1. **멤버 또는 그룹 선택**을 클릭합니다.

    ![새 할당-초대 옵션과 함께 사용자 및 그룹을 나열 하는 멤버 또는 그룹 창을 선택 합니다.](./media/pim-resource-roles-external-users/select-member-group.png)

1. 게스트를 초대 하려면 **초대**를 클릭 합니다.

    ![상자와 함께 게스트 페이지를 초대 하 여 전자 메일 주소를 입력 하 고 개인 메시지를 지정 합니다.](./media/pim-resource-roles-external-users/invite-guest.png)

1. 게스트를 선택한 후 **초대**를 클릭 합니다.

    게스트를 선택한 멤버로 추가 해야 합니다.

1. **멤버 또는 그룹 선택** 창에서 **선택**을 클릭 합니다.

1. **멤버 자격 설정** 창에서 할당 유형 및 기간을 선택 합니다.

    ![할당 유형, 시작 날짜 및 종료 날짜를 지정 하는 옵션이 있는 새 할당-멤버 자격 설정 페이지](./media/pim-resource-roles-external-users/membership-settings.png)

1. 할당을 완료 하려면 **완료** , **추가**를 차례로 선택 합니다.

    게스트 역할 할당이 역할 목록에 표시 됩니다.

    ![적합 한 게스트를 나열 하는 역할 페이지](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>게스트로 역할 활성화

외부 사용자 인 경우 초대를 Azure AD 조직에서 게스트로 수락 하 고 역할 할당을 활성화 해야 합니다.

1. 초대와 함께 전자 메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![디렉터리 이름, 개인 메시지 및 시작 링크를 사용 하 여 메일 초대](./media/pim-resource-roles-external-users/email-invite.png)

1. 전자 메일에서 **시작** 링크를 선택 합니다.

1. 권한을 검토한 후 **수락**을 클릭합니다.

    ![조직에서 검토할 권한 목록을 사용 하 여 브라우저의 사용 권한 페이지를 검토 합니다.](./media/pim-resource-roles-external-users/invite-accept.png)

1. 사용 약관에 동의 하 라는 메시지가 표시 되 면 로그인 상태를 유지할지 여부를 지정 해야 할 수 있습니다. Azure Portal에서 역할에 대 한 *자격이* 있는 경우 리소스에 대 한 액세스 권한이 아직 없는 것입니다.

1. 역할 할당을 활성화 하려면 역할 활성화 링크를 사용 하 여 전자 메일을 엽니다. 이메일은 다음과 비슷합니다.

    ![역할 활성화 링크를 사용 하 여 역할에 대 한 자격이 있는지를 나타내는 전자 메일](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. **역할 활성화** 를 선택 하 여 Privileged Identity Management에서 적합 한 역할을 엽니다.

    ![적격 역할을 나열 Privileged Identity Management 내 역할 페이지](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. 작업 아래에서 **활성화** 링크를 선택 합니다.

    역할 설정에 따라 역할을 활성화하려면 일부 정보를 지정해야 합니다.

1. 역할에 대한 설정을 지정했으면 **활성화**를 클릭하여 역할을 활성화합니다.

    ![페이지 목록 범위 및 옵션을 활성화 하 여 시작 시간, 기간 및 이유 지정](./media/pim-resource-roles-external-users/activate-role.png)

    요청 승인에 관리자가 필요한 경우 외에는 지정된 리소스에 액세스할 수 있습니다.

## <a name="view-activity-for-a-guest"></a>게스트에 대 한 작업 보기

감사 로그를 확인 하 여 게스트에서 수행 하는 작업을 추적할 수 있습니다.

1. 관리자 권한으로 Privileged Identity Management을 열고 공유 된 리소스를 선택 합니다.

1. **리소스 감사** 를 선택 하 여 해당 리소스에 대 한 활동을 확인 합니다. 다음은 리소스 그룹에 대한 작업의 예를 보여줍니다.

    ![Azure 리소스-시간, 요청자 및 작업을 나열 하는 리소스 감사 페이지](./media/pim-resource-roles-external-users/audit-resource.png)

1. 게스트에 대 한 작업을 보려면 **Azure Active Directory**  >  **사용자**  >  *게스트 이름*을 선택 합니다.

1. **감사 로그** 를 선택 하 여 조직에 대 한 감사 로그를 확인 합니다. 필요한 경우 필터를 지정할 수 있습니다.

    ![날짜, 대상, 시작 및 작업을 나열 하는 디렉터리 감사 로그](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure AD 관리자 역할 할당](pim-how-to-add-role-to-user.md)
- [Azure AD B2B 공동 작업에서 게스트 사용자 액세스 란 무엇 인가요?](../external-identities/what-is-b2b.md)