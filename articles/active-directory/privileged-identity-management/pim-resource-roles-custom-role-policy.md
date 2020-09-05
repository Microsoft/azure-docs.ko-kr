---
title: PIM에서 Azure 사용자 지정 역할 사용-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 Azure 사용자 지정 역할을 사용 하는 방법에 대해 알아봅니다.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24b7845ec66a85e6ced4f1df9caec409a94016bf
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782603"
---
# <a name="use-azure-custom-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure 사용자 지정 역할 사용

Azure Active Directory (Azure AD) 조직에서 권한 있는 역할의 일부 사용자에 게 PIM (strict Privileged Identity Management) 설정을 적용 하 고 다른 사용자에 게 더 많은 자율성을 제공 해야 할 수 있습니다. 예를 들어 조직에서 여러 계약을 고용 하 여 Azure 구독에서 실행 되는 응용 프로그램을 개발 하는 데 도움이 되는 시나리오를 고려해 보세요.

리소스 관리자로서 직원이 승인 없이 액세스할 수 있도록 하려고 합니다. 그러나 조직의 리소스에 대한 액세스를 요청할 때 모든 계약 당사자는 승인을 받아야 합니다.

다음 섹션에 설명 된 단계에 따라 Azure 리소스 역할에 대 한 대상 Privileged Identity Management 설정을 설정 합니다.

## <a name="create-the-custom-role"></a>사용자 지정 역할 만들기

리소스에 대 한 사용자 지정 역할을 만들려면 [Azure 사용자 지정 역할](../../role-based-access-control/custom-roles.md)에 설명 된 단계를 수행 합니다.

사용자 지정 역할을 만들 때 복제하려는 기본 제공 역할을 쉽게 기억할 수 있도록 설명이 포함된 이름을 사용합니다.

> [!NOTE]
> 사용자 지정 역할이 복제하려는 기본 제공 역할의 복제본이 되고, 해당 범위는 기본 제공 역할과 일치하도록 합니다.

## <a name="apply-pim-settings"></a>PIM 설정 적용

Azure AD 조직에서 역할을 만든 후 Azure Portal에서 **Privileged Identity Management-Azure 리소스** 페이지로 이동 합니다. 역할을 적용하는 리소스를 선택합니다.

![“Privileged Identity Management - Azure 리소스” 창](media/pim-resource-roles-custom-role-policy/aadpim-manage-azure-resource-some-there.png)

역할의 이러한 멤버에 적용 해야 하는 [Privileged Identity Management 역할 설정을 구성](pim-resource-roles-configure-role-settings.md) 합니다.

마지막으로 이러한 설정으로 대상으로 할 멤버의 고유 그룹에 [역할을 할당](pim-resource-roles-assign-roles.md)합니다.

## <a name="next-steps"></a>다음 단계

- [Privileged Identity Management에서 Azure 리소스 역할 설정 구성](pim-resource-roles-configure-role-settings.md)
- [Azure의 사용자 지정 역할](../../role-based-access-control/custom-roles.md)