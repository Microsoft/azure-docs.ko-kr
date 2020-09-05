---
title: B2B 외부 협업 설정 사용 - Azure AD
description: Active Directory B2B 외부 협업을 사용하도록 설정하고 게스트 사용자를 초대할 수 있는 사용자를 관리하는 방법을 알아봅니다. 게스트 초대자 역할을 사용하여 초대를 위임합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8bb66141e4cc4e67f1502b208cf519d37c0374
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88706002"
---
# <a name="enable-b2b-external-collaboration-and-manage-who-can-invite-guests"></a>B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리

이 문서에서는 Azure ad (Azure Active Directory) B2B 공동 작업을 사용 하도록 설정 하 고, 게스트를 초대할 수 있는 사람을 지정 하 고, 게스트 사용자에 게 Azure AD의 사용 권한을 결정 하는 방법을 설명 합니다. 

기본적으로 디렉터리에 있는 모든 사용자와 게스트는 관리자 역할에 할당되지 않은 경우에도 게스트를 초대할 수 있습니다. 외부 협업 설정을 사용하여 조직에서 다양한 유형의 사용자에 대해 게스트 초대를 설정하거나 해제할 수 있습니다. 또한 게스트 초대를 허용하는 역할을 할당하여 개별 사용자에게 초대를 위임할 수도 있습니다.

Azure AD를 사용 하면 외부 게스트 사용자가 Azure AD 디렉터리에서 볼 수 있는 항목을 제한할 수 있습니다. 기본적으로 게스트 사용자는 제한 된 권한 수준으로 설정 되므로 사용자, 그룹 또는 기타 디렉터리 리소스를 열거 하는 것을 차단 하지 않지만 숨겨진 그룹의 멤버 자격을 볼 수 있습니다. 새 미리 보기 설정을 사용 하 여 게스트 액세스를 추가로 제한할 수 있으므로 게스트가 자신의 프로필 정보만 볼 수 있습니다. 

## <a name="configure-b2b-external-collaboration-settings"></a>B2B 외부 협업 설정 구성

Azure AD B2B 협업을 사용하면 테넌트 관리자가 다음 초대 정책을 설정할 수 있습니다.

- 초대 해제
- 관리자 및 게스트 초대자 역할의 사용자만 초대 가능
- 관리자, 게스트 초대자 역할 및 구성원은 초대 가능
- 게스트를 포함한 모든 사용자가 초대를 수행할 수 있습니다.

기본적으로 게스트를 포함한 모든 사용자가 게스트 사용자를 초대할 수 있습니다.

### <a name="to-configure-external-collaboration-settings"></a>외부 협업 설정을 구성하려면 다음을 수행합니다.

1. 테넌트 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**를 선택합니다.
3. **외부 ID** > **외부 협업 설정**을 선택합니다.

4. **게스트 사용자 액세스 제한 (미리 보기)** 아래에서 게스트 사용자에 게 부여할 액세스 수준을 선택 합니다.

   > [!IMPORTANT]
   > 짧은 시간 동안 게스트 사용자 권한에 대 한 이러한 새로운 포털 컨트롤은 URL을 사용 하는 경우에만 표시 됩니다 [https://aka.ms/AADRestrictedGuestAccess](https://aka.ms/AADRestrictedGuestAccess) . 자세한 내용은 [게스트 액세스 권한 제한 (미리 보기)](https://aka.ms/exid-users-restrict-guest-permissions)을 참조 하세요.

   - **게스트 사용자는 구성원 (가장 포함)과 동일한 액세스 권한을 가집니다**.이 옵션을 사용 하면 게스트가 Azure AD 리소스 및 디렉터리 데이터에 대 한 액세스를 구성원 사용자로 동일 하 게 할 수 있습니다.

   - **게스트 사용자는 디렉터리 개체의 속성 및 멤버 자격에 대 한 액세스를 제한**합니다. (기본값)이 설정은 사용자, 그룹 또는 기타 디렉터리 리소스를 열거 하는 등의 특정 디렉터리 작업에서 게스트를 차단 합니다. 게스트가 숨겨진 모든 그룹의 멤버 자격을 볼 수 있습니다.

   - **게스트 사용자 액세스는 고유한 디렉터리 개체의 속성 및 멤버 자격으로 제한 됩니다 (가장 제한적)**.이 설정을 사용 하면 게스트가 자신의 프로필에만 액세스할 수 있습니다. 게스트는 다른 사용자의 프로필, 그룹 또는 그룹 멤버 자격을 볼 수 없습니다.
  
    ![게스트 사용자 액세스 제한 설정](./media/delegate-invitations/guest-user-access.png)

5. **게스트 초대 설정**에서 적절 한 설정을 선택 합니다.

   - **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음**: "게스트 초대자" 역할의 관리자와 사용자가 게스트를 초대하도록 허용하려면 이 정책을 **예**로 설정합니다.

   - **구성원이 초대할 수 있음**: 디렉터리의 관리자가 아닌 구성원이 게스트를 초대하는 것을 허용하려면 이 정책을 **예**로 설정합니다.

   - **게스트가 초대할 수 있음**: 게스트가 다른 게스트를 초대하도록 허용하려면 이 정책을 **예**로 설정합니다.

   - **게스트에 대해 전자 메일 일회용 암호 사용 (미리 보기)**: 일회용 암호 기능에 대 한 자세한 내용은 [전자 메일 일회용 암호 인증 (미리 보기)](one-time-passcode.md)을 참조 하세요.

   - **사용자 흐름을 통해 게스트 셀프 서비스 등록 사용 (미리 보기)**:이 설정에 대 한 자세한 내용은 [앱에 셀프 서비스 등록 사용자 흐름 추가 (미리 보기)](self-service-sign-up-user-flow.md)를 참조 하세요.

   > [!NOTE]
   > **구성원이 초대할 수 있음** 을 **아니요** 로 설정 하 고 **관리자 및 사용자의 게스트 초대자 역할에서 초대 가능** 이 **예**로 설정 된 경우 **게스트 초대자** 역할의 사용자는 게스트를 계속 초대할 수 있습니다.

    ![게스트 초대 설정](./media/delegate-invitations/guest-invite-settings.png)

6. **공동 작업 제한**에서 지정 하는 도메인에 대 한 초대를 허용할지 또는 거부할지를 선택 합니다. 자세한 내용은 [특정 조직의 B2B 사용자 초대 허용 또는 차단](allow-deny-list.md)을 참조하세요.

## <a name="assign-the-guest-inviter-role-to-a-user"></a>사용자에게 게스트 초대자 역할 할당

게스트 초대자 역할을 사용하면 전역 관리자 또는 다른 관리자 역할을 할당하지 않고도 개별 사용자에게 게스트를 초대할 수 있는 기능을 제공할 수 있습니다. 개인에게 게스트 초대자 역할을 할당합니다. 그런 다음, **관리자 및 게스트 초대자 역할의 사용자가 초대할 수 있음**을 **예**로 설정합니다.

다음은 PowerShell을 사용하여 게스트 초대자 역할에 사용자를 추가하는 방법을 보여주는 예입니다.

```
Add-MsolRoleMember -RoleObjectId 95e79109-95c0-4d8e-aee3-d01accf2d47b -RoleMemberEmailAddress <RoleMemberEmailAddress>
```

## <a name="next-steps"></a>다음 단계

Azure AD B2B 협업에 대한 다음 문서를 살펴보세요.

- [Azure AD B2B 협업이란?](what-is-b2b.md)
- [초대 없이 B2B Collaboration 게스트 사용자 추가](add-user-without-invite.md)
- [역할에 B2B 협업 사용자 추가](add-guest-to-role.md)

