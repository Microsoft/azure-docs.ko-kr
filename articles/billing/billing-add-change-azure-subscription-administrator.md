---
title: Azure 구독 관리자 추가 또는 변경 | Microsoft Docs
description: RBAC(역할 기반 액세스 제어)를 사용하여 Azure 구독 관리자를 추가하거나 변경하는 방법을 설명합니다.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 6cc965f8e775e02e9dec9f610516739a9a2c1936
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127720"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure 구독 관리자 추가 또는 변경

Azure 리소스에 대한 액세스를 관리하려면 적절한 관리자 역할이 있어야 합니다. Azure에는 선택할 수 있는 몇 가지 기본 제공 역할이 있는 RBAC(역할 기반 액세스 제어)라는 인증 시스템이 있습니다. 이러한 역할은 관리 그룹, 구독 또는 리소스 그룹과 같은 다른 범위에서 할당할 수 있습니다.

RBAC를 사용하여 리소스에 대한 액세스를 관리하는 것이 좋습니다. 그러나 여전히 클래식 배포 모델을 사용 중이고, [Azure 서비스 관리 PowerShell 모듈](https://docs.microsoft.com/en-us/powershell/module/servicemanagement/azure)을 사용하여 클래식 리소스를 관리하는 경우에는 클래식 관리자를 사용해야 합니다. 

> [!TIP]
> Azure Portal을 통해서만 클래식 리소스를 관리하는 경우 클래식 관리자를 사용할 필요가 없습니다.

자세한 내용은 [Azure Resource Manager 및 클래식 배포](../azure-resource-manager/resource-manager-deployment-model.md) 및 [Azure 클래식 구독 관리자](../role-based-access-control/classic-administrators.md)를 참조하세요.

이 문서에서는 구독 범위에서 RBAC를 사용하여 사용자에 대한 관리자 역할을 추가하거나 변경하는 방법을 설명합니다.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>구독 관리자로서 사용자 할당

특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](../role-based-access-control/built-in-roles.md#owner) 역할(RBAC 역할)을 할당합니다. 소유자 역할을 할당하면 다른 사용자에게 액세스를 위임할 수 있는 권한을 비롯한 구독의 리소스에 대한 모든 권한이 사용자에게 제공됩니다. 이러한 단계는 다른 역할 할당과 동일합니다.

1. Azure Portal에서 [구독](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)을 엽니다.

1. 액세스 권한을 부여하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

    ![역할 할당을 보여주는 스크린샷](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. **추가** > **역할 할당 추가**를 클릭하여 **역할 할당 추가** 창을 엽니다.

    역할을 할당할 수 있는 권한이 없으면 옵션이 비활성화됩니다.

1. **역할** 드롭다운 목록에서 **소유자** 역할을 선택합니다.

1. **선택** 목록에서 사용자를 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 직접 입력하여 표시 이름 및 이메일 주소에 대한 디렉터리를 검색할 수 있습니다.

    ![선택한 소유자 역할을 보여 주는 스크린샷](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. **저장**을 클릭하여 역할을 할당합니다.

    몇 분이 지나면 사용자에게는 구독 범위의 소유자 역할이 할당됩니다.

## <a name="next-steps"></a>다음 단계

* [RBAC(역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)
* [Azure의 다양한 역할 이해](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [방법: Azure Active Directory에 Azure 구독 연결 또는 추가](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory의 관리자 역할 사용 권한](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

다른 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
