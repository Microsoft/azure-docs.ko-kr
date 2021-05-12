---
title: PIM에서 Azure AD 역할에 대한 액세스 검토 - Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 Azure Active Directory 역할의 액세스를 검토하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 04/24/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dd0b63e08f44167b89b6e2d491722f3708b4036
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90055438"
---
# <a name="review-access-to-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 액세스 검토

PIM(Privileged Identity Management)은 기업이 Azure AD(Active Directory)와 Microsoft 365 또는 Microsoft Intune 같은 Microsoft 온라인 서비스 리소스에 대한 권한 있는 액세스를 관리하는 방법을 단순화합니다. 이 문서의 단계에 따라 할당된 역할을 자체 검토합니다.

관리 역할에 할당된 경우 조직의 권한 있는 역할 관리자가 작업에 해당 역할이 여전히 필요한지 정기적으로 확인하도록 요청할 수 있습니다. 링크가 포함된 이메일을 받거나 [Azure Portal](https://portal.azure.com)로 바로 이동하여 시작할 수 있습니다.

액세스 검토에 관심이 있는 권한 있는 역할 관리자 또는 전역 관리자인 경우 [액세스 검토를 시작하는 방법](pim-how-to-start-security-review.md)에서 자세한 내용을 참조하세요.

## <a name="add-a-pim-dashboard-tile"></a>PIM 대시보드 타일 추가

Azure Portal의 대시보드에 고정된 Privileged Identity Management 서비스가 없는 경우 다음 단계에 따라 시작하세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal의 오른쪽 위에서 사용자 이름을 선택하고 작동할 Azure AD 조직을 선택합니다.
3. **모든 서비스** 를 선택하고 필터 텍스트 상자를 사용하여 **Azure AD Privileged Identity Management** 를 검색합니다.
4. **대시보드에 고정** 옵션을 선택하고 **만들기** 를 클릭합니다. Privileged Identity Management 애플리케이션이 열립니다.

## <a name="approve-or-deny-access"></a>액세스 승인 또는 거부

액세스를 승인하거나 거부할 때, 단지 검토자에게 이 역할을 사용할지를 알리는 것입니다. 역할을 유지하려면 **승인** 을, 또는 더 이상 액세스를 필요로 하지 않는 경우 **거부** 를 선택합니다. 검토자가 결과를 적용할 때까지는 사용자의 상태가 바로 변경되지 않습니다.
액세스 검토를 찾아 완료하려면 다음 단계를 수행합니다.

1. Privileged Identity Management 서비스에서 **권한 있는 액세스 검토** 를 선택합니다. 보류 중인 액세스 검토가 있으면 Azure AD **액세스 검토** 페이지에 표시됩니다.
2. 완료할 검토를 선택합니다.
3. 검토를 만들지 않은 한, 검토에서 유일한 사용자로 나타납니다. 사용자 이름 옆에 있는 확인 표시를 선택합니다.
4. **승인** 또는 **거부** 를 선택합니다. **이유 설명** 텍스트 상자에 결정 이유를 포함해야 합니다.  
5. **Review Azure AD roles** (Azure AD 역할 검토)를 닫습니다.

## <a name="next-steps"></a>다음 단계

- [PIM에서 Azure 리소스 역할에 대한 액세스 검토 수행](pim-resource-roles-perform-access-review.md)
