---
title: PIM을 관리하기 위한 액세스 권한 부여 - Azure Active Directory | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)을 관리하기 위해 다른 관리 기관에 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba46ee5632f7411c433e4bba29201c59c552f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835206"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Privileged Identity Management에 대한 액세스 권한 위임

PIM(Privileged Identity Management)에 대한 액세스 권한을 위임하기 위해 전역 관리자는 권한 있는 역할 관리자 역할에 다른 사용자를 할당할 수 있습니다. 기본적으로 보안 관리자 및 보안 읽기 권한자는 Privileged Identity Management에 대해 읽기 전용 액세스 권한을 갖습니다. PIM에 대한 액세스 권한을 부여하기 위해 첫 번째 사용자가 다른 사용자를 **권한 있는 역할 관리자** 역할에 할당할 수 있습니다. 권한 있는 역할 관리자 역할은 Azure AD 역할을 관리하는 데만 필요합니다. Azure 리소스에 대한 설정을 관리하는 데는 권한 있는 역할 관리자 권한이 필요하지 않습니다.

> [!NOTE]
> Privileged Identity Management를 관리하려면 Azure AD Multi-Factor Authentication이 필요합니다. Microsoft 계정은 Azure AD Multi-Factor Authentication에 등록할 수 없으므로 Microsoft 계정을 사용하여 로그인하는 사용자는 Privileged Identity Management에 액세스할 수 없습니다.

한 명의 사용자가 잠긴 경우 또는 해당 계정이 삭제된 경우 최소한 두 명의 사용자가 항상 권한 있는 역할 관리자 역할에 있어야 합니다.

## <a name="delegate-access-to-manage-pim"></a>PIM을 관리하기 위한 액세스 권한 위임

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. Azure AD에서 **Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택합니다.

    ![Privileged Identity Management Azure AD 역할 - 역할](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. **권한 있는 역할 관리자** 역할을 선택하여 멤버 페이지를 엽니다.

    ![권한 있는 역할 관리자 - 멤버](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. **멤버 추가** 를 선택하여 **관리되는 멤버 추가** 창을 엽니다.

1. **멤버 선택** 을 선택하여 **멤버 선택** 창을 엽니다.

    ![권한 있는 역할 관리자 - 멤버 선택](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. 멤버를 선택한 다음 **선택** 을 클릭합니다.

1. **확인** 을 클릭하여 해당 멤버가 **권한 있는 역할 관리자** 역할에 적합하도록 지정합니다.

    Privileged Identity Management의 다른 사람에게 새 역할을 할당하면 자동으로 해당 역할을 활성화할 **자격** 이 있는 것으로 구성됩니다.

1. 멤버를 영구적으로 지정하려면 권한 있는 역할 관리자 멤버 목록에서 해당 사용자를 선택합니다.

1. **자세한**, **영구 상태로 만들기** 를 차례로 선택하여 할당을 영구로 만듭니다.

    ![권한 있는 역할 관리자 - 영구 상태로 만들기](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. [Privileged Identity Management 사용 시작](pim-getting-started.md) 링크를 사용자에게 보냅니다.

## <a name="remove-access-to-manage-pim"></a>PIM을 관리하기 위한 액세스 권한 제거

권한 있는 역할 관리자 역할에서 사용자를 제거하기 전에 항상 두 명 이상의 사용자가 이 역할에 할당되어 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. **Azure AD Privileged Identity Management** 를 엽니다.

1. **Azure AD 역할** 을 선택합니다.

1. **역할** 을 선택합니다.

1. **권한 있는 역할 관리자** 역할을 선택하여 멤버 페이지를 엽니다.

1. 제거하려는 사용자 옆에 있는 확인란을 선택하고 **멤버 제거** 를 선택합니다.

    ![권한 있는 역할 관리자 - 멤버 제거](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. 역할에서 멤버를 제거할 것인지 확인하는 메시지가 표시되면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management 사용 시작](pim-getting-started.md)
