---
title: 그룹에 민감도 레이블 할당-Azure AD | Microsoft Docs
description: 그룹을 자동으로 채우는 멤버 자격 규칙과 규칙 참조를 만드는 방법입니다.
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
ms.openlocfilehash: 46d692b81d24b6c5088ffc42644ed1dd7f45b2d2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795319"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Azure Active Directory의 Microsoft 365 그룹에 민감도 레이블 할당

Azure Active Directory (Azure AD)는 [Microsoft 365 준수 센터](https://sip.protection.office.com/homepage) 에서 게시 한 민감도 레이블을 Microsoft 365 그룹에 적용 하도록 지원 합니다. 민감도 레이블은 Outlook, Microsoft 팀, SharePoint 등의 서비스에서 그룹에 적용 됩니다. 이 기능은 현재 공개 GA에 있습니다. Office 365 앱 지원에 대 한 자세한 내용은 [민감도 레이블에 대 한 office 365 지원](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels)을 참조 하세요.

> [!IMPORTANT]
> 이 기능을 구성 하려면 Azure AD 조직에 활성 Azure Active Directory Premium P1 라이선스가 하나 이상 있어야 합니다.

## <a name="enable-sensitivity-label-support-in-powershell"></a>PowerShell에서 민감도 레이블 지원 사용

게시 된 레이블을 그룹에 적용 하려면 먼저 기능을 사용 하도록 설정 해야 합니다. 이러한 단계는 Azure AD의 기능을 사용 하도록 설정 합니다.

1. 컴퓨터에서 Windows PowerShell 창을 엽니다. 높은 권한이 없으면 이 창이 열리지 않습니다.
1. 다음 명령을 실행하여 cmdlet 실행을 준비합니다.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    **계정에 로그인** 페이지에서 관리자 계정과 암호를 입력 하 여 서비스에 연결 하 고 **로그인**을 선택 합니다.
1. Azure AD 조직에 대 한 현재 그룹 설정을 가져옵니다.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > 이 Azure AD 조직에 대해 그룹 설정이 생성 되지 않은 경우 먼저 설정을 만들어야 합니다. [그룹 설정을 구성 하는 Azure Active Directory cmdlet](./groups-settings-cmdlets.md) 의 단계에 따라이 Azure AD 조직에 대 한 그룹 설정을 만듭니다.

1. 다음으로, 현재 그룹 설정을 표시 합니다.

    ```PowerShell
    $Setting.Values
    ```

1. 그런 다음 기능을 사용 하도록 설정 합니다.

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. 그런 다음 변경 내용을 저장 하 고 설정을 적용 합니다.

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

이것으로 끝입니다. 기능을 사용 하도록 설정 했으며 게시 된 레이블을 그룹에 적용할 수 있습니다.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Azure Portal에서 새 그룹에 레이블 할당

1. [AZURE AD 관리 센터](https://aad.portal.azure.com)에 로그인 합니다.
1. **그룹**을 선택 하 고 **새 그룹**을 선택 합니다.
1. **새 그룹** 페이지에서 **Office 365**을 선택한 다음 새 그룹에 대 한 필수 정보를 입력 하 고 목록에서 민감도 레이블을 선택 합니다.

   ![새 그룹 페이지에서 민감도 레이블 할당](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. 변경 내용을 저장 하 고 **만들기**를 선택 합니다.

그룹이 만들어지고 선택한 레이블과 연결 된 사이트 및 그룹 설정이 자동으로 적용 됩니다.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Azure Portal에서 기존 그룹에 레이블 할당

1. 그룹 관리자 계정 또는 그룹 소유자로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹**을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 지정할 그룹을 선택 합니다.
1. 선택한 그룹의 페이지에서 **속성** 을 선택 하 고 목록에서 민감도 레이블을 선택 합니다.

   ![그룹에 대 한 개요 페이지에서 민감도 레이블 할당](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. **저장**을 선택하여 변경 내용을 저장합니다.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Azure Portal의 기존 그룹에서 레이블 제거

1. 전역 관리자 또는 그룹 관리자 계정 또는 그룹 소유자로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹**을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 제거 하려는 그룹을 선택 합니다.
1. **그룹** 페이지에서 **속성**을 선택 합니다.
1. **제거**를 선택합니다.
1. **저장**을 선택하여 변경 내용을 적용합니다.

## <a name="using-classic-azure-ad-classifications"></a>클래식 Azure AD 분류 사용

이 기능을 사용 하도록 설정 하면 그룹에 대 한 "클래식" 분류는 기존 그룹 및 사이트에만 표시 되며 민감도 레이블을 지원 하지 않는 앱에서 그룹을 만드는 경우에만 새 그룹에 사용 해야 합니다. 관리자는 필요한 경우 나중에 민감도 레이블로 변환할 수 있습니다. 클래식 분류는 `ClassificationList` AZURE AD PowerShell의 설정에 대 한 값을 정의 하 여 설정한 이전 분류입니다. 이 기능을 사용 하도록 설정 하면 그룹에 해당 분류가 적용 되지 않습니다.

## <a name="troubleshooting-issues"></a>문제 해결

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>그룹에서 민감도 레이블을 할당 하는 데 사용할 수 없습니다.

민감도 레이블 옵션은 다음 조건이 모두 충족 될 경우에만 그룹에 대해 표시 됩니다.

1. 레이블은이 Azure AD 조직에 대 한 Microsoft 365 준수 센터에 게시 됩니다.
1. 이 기능을 사용 하도록 설정 하면 PowerShell에서 EnableMIPLabels가 True로 설정 됩니다.
1. 그룹이 Microsoft 365 그룹입니다.
1. 조직에 활성 Azure Active Directory Premium P1 라이선스가 있습니다.
1. 현재 로그인 한 사용자에 게는 레이블을 할당할 수 있는 충분 한 권한이 있습니다. 사용자는 전역 관리자, 그룹 관리자 또는 그룹 소유자 여야 합니다.

그룹에 레이블을 할당 하기 위해 모든 조건을 충족 하는지 확인 하세요.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>할당 하려는 레이블이 목록에 없습니다.

찾고 있는 레이블이 목록에 없는 경우 다음 이유 중 하나 때문일 수 있습니다.

- 레이블이 Microsoft 365 준수 센터에 게시 되지 않을 수 있습니다. 이는 더 이상 게시 되지 않은 레이블에도 적용할 수 있습니다. 자세한 내용은 관리자에 게 문의 하세요.
- 그러나 레이블은 게시 될 수 있지만 로그인 한 사용자는 사용할 수 없습니다. 레이블에 대 한 액세스 권한을 얻는 방법에 대 한 자세한 내용은 관리자에 게 문의 하세요.

### <a name="how-to-change-the-label-on-a-group"></a>그룹의 레이블을 변경 하는 방법

다음과 같이 레이블을 기존 그룹에 할당 하는 것과 동일한 단계를 사용 하 여 언제 든 지 레이블을 바꿀 수 있습니다.

1. 전역 또는 그룹 관리자 계정이 나 그룹 소유자로 [AZURE AD 관리 센터](https://aad.portal.azure.com) 에 로그인 합니다.
1. **그룹**을 선택합니다.
1. **모든 그룹** 페이지에서 레이블을 지정할 그룹을 선택 합니다.
1. 선택한 그룹의 페이지에서 **속성** 을 선택 하 고 목록에서 새 민감도 레이블을 선택 합니다.
1. **저장**을 선택합니다.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>게시 된 레이블의 그룹 설정 변경 내용이 그룹에서 업데이트 되지 않음

레이블을 그룹에 적용 한 후에는 레이블에 대 한 그룹 설정을 변경 하지 않는 것이 좋습니다. [Microsoft 365 준수 센터](https://sip.protection.office.com/homepage)에서 게시 된 레이블과 연결 된 그룹 설정을 변경 하는 경우 해당 정책 변경 내용은 영향을 받는 그룹에 자동으로 적용 되지 않습니다.

변경 해야 하는 경우 [AZURE AD PowerShell 스크립트](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) 를 사용 하 여 영향을 받는 그룹에 업데이트를 수동으로 적용 합니다. 이 방법을 사용 하면 모든 기존 그룹이 새 설정을 적용 하도록 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft 팀, Microsoft 365 그룹 및 SharePoint 사이트에서 민감도 레이블 사용](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Azure AD PowerShell 스크립트를 사용 하 여 레이블 정책을 수동으로 변경한 후 그룹 업데이트](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [그룹 설정 편집](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [PowerShell 명령을 사용하여 그룹 관리](./groups-settings-v2-cmdlets.md)