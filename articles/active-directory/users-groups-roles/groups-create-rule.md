---
title: 동적 그룹 만들기 또는 편집 및 상태 가져오기-Azure AD | Microsoft Docs
description: Azure Portal에서 그룹 멤버 자격 규칙을 만들거나 업데이트 하는 방법 및 해당 처리 상태를 확인 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5c44f72021fa46352ae6ded8fe671ea4f1a4d60
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797176"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Azure Active Directory에서 동적 그룹 만들기 또는 업데이트

Azure Active Directory (Azure AD)에서 규칙을 사용 하 여 사용자 또는 장치 속성에 따라 그룹 멤버 자격을 확인할 수 있습니다. 이 문서에서는 Azure Portal에서 동적 그룹에 대 한 규칙을 설정 하는 방법을 설명 합니다.
동적 멤버 자격은 보안 그룹 또는 Microsoft 365 그룹에 대해 지원 됩니다. 그룹 멤버 자격 규칙을 적용 하는 경우 사용자 및 장치 특성은 멤버 자격과 일치 하는지 평가 됩니다. 사용자 또는 장치에 대 한 특성이 변경 되 면 조직의 모든 동적 그룹 규칙이 멤버 자격 변경에 대해 처리 됩니다. 사용자 및 장치는 그룹에 대 한 조건을 충족 하는 경우 추가 되거나 제거 됩니다. 보안 그룹은 장치 또는 사용자에 대해 사용할 수 있지만 Microsoft 365 그룹은 사용자 그룹만 될 수 있습니다.

## <a name="rule-builder-in-the-azure-portal"></a>Azure Portal의 규칙 작성기

Azure AD는 중요한 규칙을 더 신속하게 만들고 업데이트하는 규칙 작성기를 제공합니다. 규칙 작성기는 최대 5개의 식을 생성하는 작업을 지원합니다. 규칙 작성기를 사용하면 몇 가지 간단한 식으로 하나의 규칙을 보다 쉽게 만들 수 있으며, 다만 모든 규칙을 재현하는 데 규칙 작성기를 사용할 수는 없습니다. 사용자가 만들려는 규칙을 규칙 작성기에서 지원하지 않는 경우, 텍스트 상자를 사용할 수 있습니다.

텍스트 상자를 사용하여 생성하는 것이 바람직한 고급 규칙 또는 구문의 일부 예는 다음과 같습니다.

- 6개 이상의 식이 있는 규칙
- 직접 보고 규칙
- [연산자 선행 규칙](groups-dynamic-membership.md#operator-precedence) 설정
- [복잡한 식이 있는 규칙](groups-dynamic-membership.md#rules-with-complex-expressions)(예: `(user.proxyAddresses -any (_ -contains "contoso"))`)

> [!NOTE]
> 규칙 작성기가 텍스트 상자에 생성된 일부 규칙을 표시하지 못할 수도 있습니다. 규칙 작성기에서 규칙을 표시할 수 없을 때 메시지가 표시될 수 있습니다. 규칙 작성기는 지원되는 구문, 유효성 검사 또는 동적 그룹 규칙의 처리를 어떤 식으로든 변경하지 않습니다.

![동적 그룹 멤버 관리 규칙 추가](./media/groups-create-rule/update-dynamic-group-rule.png)

멤버 자격 규칙에 대 한 구문, 지원 되는 속성, 연산자 및 값의 예는 [Azure Active Directory의 그룹에 대 한 동적 멤버 자격 규칙](groups-dynamic-membership.md)을 참조 하세요.

## <a name="to-create-a-group-membership-rule"></a>그룹 멤버 자격 규칙 만들기

1. Azure ad 조직의 전역 관리자, Intune 관리자 또는 사용자 관리자 역할에 있는 계정을 사용 하 여 [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹**을 검색 하 고 선택 합니다.
1. **모든 그룹**을 선택하고 **새 그룹**을 선택합니다.

   ![새 그룹을 추가 하는 명령 선택](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. **그룹** 페이지에서 새 그룹에 대 한 이름 및 설명을 입력 합니다. 사용자 또는 장치 중 하나에 대 한 **멤버 자격 유형을** 선택한 다음 **동적 쿼리 추가**를 선택 합니다. 규칙 작성기는 최대 5 개의 식을 지원 합니다. 5 개 이상의 식을 추가 하려면 텍스트 상자를 사용 해야 합니다.

   ![동적 그룹 멤버 관리 규칙 추가](./media/groups-create-rule/add-dynamic-group-rule.png)

1. 멤버 자격 쿼리에 사용할 수 있는 사용자 지정 확장 속성을 보려면 다음을 수행 합니다.
   1. **사용자 지정 확장 속성 가져오기** 선택
   1. 응용 프로그램 ID를 입력 하 고 **속성 새로 고침**을 선택 합니다.
1. 규칙을 만든 후 **저장**을 선택 합니다.
1. **새 그룹** 페이지에서 **만들기** 를 선택 하 여 그룹을 만듭니다.

입력 한 규칙이 유효 하지 않은 경우에는 포털에서 Azure 알림에 규칙이 처리 되지 않는 이유에 대 한 설명이 표시 됩니다. 규칙을 수정 하는 방법을 이해 하려면 신중 하 게 읽어 보세요.

## <a name="to-update-an-existing-rule"></a>기존 규칙을 업데이트 하려면

1. Azure ad 조직의 전역 관리자, 그룹 관리자, Intune 관리자 또는 사용자 관리자 역할에 있는 계정을 사용 하 여 [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹**  >  **모든 그룹**을 선택 합니다.
1. 그룹을 선택 하 여 해당 프로필을 엽니다.
1. 그룹에 대 한 프로필 페이지에서 **동적 멤버 자격 규칙**을 선택 합니다. 규칙 작성기는 최대 5 개의 식을 지원 합니다. 5 개 이상의 식을 추가 하려면 텍스트 상자를 사용 해야 합니다.

   ![동적 그룹 멤버 관리 규칙 추가](./media/groups-create-rule/update-dynamic-group-rule.png)

1. 멤버 자격 규칙에 사용할 수 있는 사용자 지정 확장 속성을 보려면 다음을 수행 합니다.
   1. **사용자 지정 확장 속성 가져오기** 선택
   1. 응용 프로그램 ID를 입력 하 고 **속성 새로 고침**을 선택 합니다.
1. 규칙을 업데이트 한 후 **저장**을 선택 합니다.

## <a name="turn-on-or-off-welcome-email"></a>환영 이메일 켜기 또는 끄기

새 Microsoft 365 그룹이 만들어지면 해당 그룹에 추가 된 사용자에 게 환영 전자 메일 알림이 전송 됩니다. 나중에 사용자 또는 장치의 특성이 변경 되 면 조직의 모든 동적 그룹 규칙이 멤버 자격 변경에 대해 처리 됩니다. 그런 다음, 추가된 사용자도 환영 알림을 받습니다. 이 동작은 [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps)에서 해제할 수 있습니다.

## <a name="check-processing-status-for-a-rule"></a>규칙에 대 한 처리 상태 확인

그룹에 대한 **개요** 페이지에서 멤버 자격 처리 상태 및 마지막으로 업데이트한 날짜를 확인할 수 있습니다.
  
  ![동적 그룹 상태 표시](./media/groups-create-rule/group-status.png)

**멤버 자격 처리** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

- **평가 중**: 그룹 변경 내용이 수신되었으며 업데이트가 평가 중입니다.
- **처리 중**: 업데이트가 처리 중입니다.
- **업데이트 완료**: 처리가 완료되었으며 적용 가능한 모든 업데이트가 수행되었습니다.
- **처리 오류**: 멤버 자격 규칙을 평가 하는 동안 오류가 발생 하 여 처리를 완료할 수 없습니다.
- **업데이트 일시 중지됨**: 관리자가 동적 멤버 자격 규칙 업데이트를 일시 중지했습니다. MembershipRuleProcessingState가 “일시 중지됨”으로 설정됩니다.

**마지막으로 업데이트된 멤버 자격** 상태에 대해 다음 상태 메시지가 표시될 수 있습니다.

- &lt;**날짜 및 시간** &gt; : 멤버 자격을 마지막으로 업데이트 한 시간입니다.
- **진행 중**: 업데이트가 현재 진행 중입니다.
- **알 수 없음**: 마지막 업데이트 시간을 검색할 수 없습니다. 새 그룹 일 수 있습니다.

특정 그룹에 대한 멤버 자격 규칙을 처리하는 동안 오류가 발생하면 해당 그룹에 대한 **개요 페이지** 맨 위에 경고가 표시됩니다. 24 시간 넘게 조직 내의 모든 그룹에 대해 보류 중인 동적 구성원 업데이트를 처리할 수 없는 경우 **모든 그룹**의 맨 위에 경고가 표시 됩니다.

![오류 메시지 경고 처리](./media/groups-create-rule/processing-error.png)

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

- [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)