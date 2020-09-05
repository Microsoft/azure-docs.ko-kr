---
title: 정적 그룹 멤버 자격을 동적으로 변경-Azure AD | Microsoft Docs
description: 그룹을 자동으로 채우는 멤버 자격 규칙과 규칙 참조를 만드는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 04/29/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ee5af5d7489835f125319050d291a89ab252a6
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797125"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Azure Active Directory에서 정적 그룹 멤버 자격을 동적으로 변경

그룹의 멤버 자격은 Azure AD(Azure Active Directory)를 통해 정적에서 동적으로(또는 그 반대로) 변경할 수 있습니다. Azure AD는 시스템에서 동일한 그룹 이름과 ID를 유지하므로 그룹에 대한 모든 기존 참조가 여전히 유효합니다. 대신 새 그룹을 만들면 해당 참조를 업데이트해야 합니다. 동적 그룹 멤버 자격은 사용자의 추가 및 제거에 따른 관리 오버헤드를 제거합니다. 이 문서에서는 Azure AD 관리 센터 또는 PowerShell cmdlet을 사용하여 기존 그룹을 정적 멤버 자격에서 동적 멤버 자격으로 변환하는 방법에 대해 설명합니다.

> [!WARNING]
> 기존 정적 그룹을 동적 그룹으로 변경하면 기존의 모든 멤버가 그룹에서 제거된 다음, 새 멤버를 추가하도록 멤버 자격 규칙이 처리됩니다. 그룹이 앱 또는 리소스에 대한 액세스를 제어하는 데 사용되는 경우 멤버 자격 규칙이 완전히 처리될 때까지 원래 멤버가 액세스 권한을 잃을 수 있습니다.
>
> 그룹의 새 멤버 자격이 예상대로 유지되는지 확인하기 위해 새 멤버 자격 규칙을 미리 테스트하는 것이 좋습니다.

## <a name="change-the-membership-type-for-a-group"></a>그룹에 대한 멤버 자격 유형 변경

1. Azure ad 조직의 전역 관리자 또는 사용자 관리자 인 계정으로 [AZURE ad 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
2. **그룹**을 선택합니다.
3. **모든 그룹** 목록에서 변경하려는 그룹을 엽니다.
4. **속성**을 선택합니다.
5. 해당 그룹의 **속성**에서, 원하는 멤버 자격 종류에 따라 **멤버 자격 유형**을 할당됨(정적), 동적 사용자 또는 동적 디바이스 중에 선택합니다. 동적 멤버 자격의 경우 규칙 작성기를 사용하여 간단한 규칙에 대한 옵션을 선택하거나 멤버 자격 규칙을 직접 작성할 수 있습니다. 

다음 단계는 사용자 무리에 대한 그룹을 정적 멤버 자격에서 동적 멤버 자격으로 변경하는 예제입니다.

1. 선택한 그룹의**속성** 페이지에서 **멤버 자격 유형**을 **동적 사용자**로 선택한 다음, 그룹 멤버의 변경 내용을 설명하는 대화 상자에서 [예]를 선택하여 계속 진행합니다. 
  
   ![멤버 자격 유형을 동적 사용자로 선택](./media/groups-change-type/select-group-to-convert.png)
  
2. **동적 쿼리 추가**를 선택하고 규칙을 입력합니다.
  
   ![동적 그룹에 대 한 규칙을 입력 합니다.](./media/groups-change-type/enter-rule.png)
  
3. 규칙을 만든 후 페이지 맨 아래에서 **쿼리 추가**를 선택합니다.
4. 해당 그룹의 **속성** 페이지에서 **저장**을 선택하여 변경 내용을 저장합니다. 그룹 목록에서 해당 그룹의 **멤버 자격 유형**이 즉시 업데이트됩니다.

> [!TIP]
> 입력한 멤버 자격 규칙이 올바르지 않으면 그룹 변환이 실패할 수 있습니다. 포털의 오른쪽 상단 구석에 알림이 표시됩니다. 알림에는 시스템에서 규칙을 수락할 수 없는 이유에 대한 설명이 포함되어 있습니다. 이 알림을 신중하게 읽고 규칙을 올바르게 수정하는 방법을 이해하세요. 규칙 구문의 예제와 멤버 자격 규칙에 지원되는 속성, 연산자 및 값의 전체 목록은 [Azure Active Directory의 그룹에 대한 동적 멤버 자격 규칙](groups-dynamic-membership.md)을 참조하세요.

## <a name="change-membership-type-for-a-group-powershell"></a>그룹에 대한 멤버 자격 유형 변경(PowerShell)

> [!NOTE]
> 동적 그룹 속성을 변경하려면 **Azure AD PowerShell 버전 2의 ** [미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)에서 cmdlet을 사용해야 합니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureADPreview)에서 미리 보기를 설치할 수 있습니다.

기존 그룹에서 멤버 자격 관리를 전환하는 기능의 예는 다음과 같습니다. 이 예제에서는 GroupTypes 속성을 정확하게 조작하고 동적 멤버 자격과 무관한 다른 기존 값을 그대로 유지하기 위해 주의가 필요합니다.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
그룹을 고정으로 지정하려면:

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

그룹을 동적으로 지정하려면:

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

* [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
* [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)