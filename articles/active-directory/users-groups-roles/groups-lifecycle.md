---
title: Microsoft 365 그룹에 대 한 만료 설정-Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 Microsoft 365 그룹에 대 한 만료를 설정 하는 방법
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ed286cf2b11429d5f0f56b3a85f173c2a39bc81
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799301"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Microsoft 365 그룹에 대 한 만료 정책 구성

이 문서에서는 만료 정책을 설정 하 여 Microsoft 365 그룹의 수명 주기를 관리 하는 방법을 설명 합니다. Azure Active Directory (Azure AD)의 Microsoft 365 그룹에 대해서만 만료 정책을 설정할 수 있습니다.

만료할 그룹을 설정한 경우 다음이 적용됩니다.

- 사용자 활동이 있는 그룹은 만료가 임박 하면 자동으로 갱신 됩니다.
- 그룹이 자동 갱신 되지 않은 경우 그룹 소유자에 게 그룹을 갱신 하 라는 알림이 표시 됩니다.
- 갱신 되지 않은 모든 그룹은 삭제 됩니다.
- 삭제 된 모든 Microsoft 365 그룹은 그룹 소유자 또는 관리자에 의해 30 일 이내에 복원 될 수 있습니다.

현재 Azure AD 조직의 모든 Microsoft 365 그룹에 대해 만료 정책을 하나만 구성할 수 있습니다.

> [!NOTE]
> Microsoft 365 그룹에 대 한 만료 정책을 구성 하 고 사용 하려면 만료 정책이 적용 되는 모든 그룹의 구성원에 대 한 Azure AD Premium 라이선스를 소유 하 고 반드시 할당 해야 하는 것은 아닙니다.

Azure AD PowerShell cmdlet을 다운로드하여 설치하는 방법에 대한 내용은 [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137)을 참조하세요.

## <a name="activity-based-automatic-renewal"></a>활동 기준 자동 갱신

이제 Azure AD intelligence를 사용 하 여 그룹이 최근에 사용 되었는지 여부에 따라 자동으로 갱신 됩니다. 이 기능은 Outlook, SharePoint 또는 팀과 같은 Office 365 서비스에서 그룹의 사용자 작업을 기반으로 하기 때문에 그룹 소유자가 수동 작업을 수행할 필요가 없습니다. 예를 들어 소유자 또는 그룹 구성원이 SharePoint에서 문서를 업로드 하거나 팀 채널을 방문 하거나 Outlook의 그룹으로 전자 메일을 보내면 그룹이 자동으로 갱신 되 고 소유자에 게 갱신 알림이 수신 되지 않습니다.

### <a name="activities-that-automatically-renew-group-expiration"></a>그룹 만료를 자동으로 갱신 하는 작업

다음 사용자 동작은 자동 그룹 갱신을 발생 시킵니다.

- SharePoint: 파일 보기, 편집, 다운로드, 이동, 공유 또는 업로드
- Outlook: 그룹의 그룹 메시지 (예: Outlook 웹 액세스)와 같은 그룹 공간에서 그룹 메시지 읽기/쓰기
- 팀: 팀 채널 방문

### <a name="auditing-and-reporting"></a>감사 및 보고

관리자는 Azure AD의 작업 감사 로그에서 자동으로 갱신 된 그룹 목록을 가져올 수 있습니다.

![작업 기반 그룹 자동 갱신](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>역할 및 권한

다음은 Azure AD에서 Microsoft 365 그룹에 대해 만료를 구성 하 고 사용할 수 있는 역할입니다.

역할 | 사용 권한
-------- | --------
전역 관리자, 그룹 관리자 또는 사용자 관리자 | Microsoft 365 그룹 만료 정책 설정을 생성, 읽기, 업데이트 또는 삭제할 수 있습니다.<br>모든 Microsoft 365 그룹을 갱신할 수 있음
사용자 | 소유 하 고 있는 Microsoft 365 그룹을 갱신할 수 있음<br>소유 하 고 있는 Microsoft 365 그룹을 복원할 수 있음<br>만료 정책 설정을 읽을 수 있음

삭제 된 그룹을 복원 하는 권한에 대 한 자세한 내용은 [Azure Active Directory에서 삭제 된 Microsoft 365 그룹 복원](groups-restore-deleted.md)을 참조 하세요.

## <a name="set-group-expiration"></a>그룹 만료 설정

1. Azure ad 조직의 전역 관리자 인 계정으로 [AZURE ad 관리 센터](https://aad.portal.azure.com) 를 엽니다.

2. **그룹**을 선택한 다음 **만료** 를 선택 하 여 만료 설정을 엽니다.
  
   ![그룹에 대 한 만료 설정](./media/groups-lifecycle/expiration-settings.png)

3. **만료** 페이지에서 다음을 수행할 수 있습니다.

    - 일 단위로 그룹 수명을 설정합니다. 미리 설정 된 값 중 하나를 선택 하거나 사용자 지정 값 (30 일 이상 이어야 함)을 선택할 수 있습니다.
    - 그룹에 소유자가 없는 경우 갱신 및 만료 알림이 전송되어야 하는 전자 메일 주소를 지정합니다.
    - 만료 되는 Microsoft 365 그룹을 선택 합니다. 다음에 대 한 만료를 설정할 수 있습니다.
      - **모두** Microsoft 365 그룹
      - **선택한** Microsoft 365 그룹 목록
      - 모든 그룹에 대 한 만료를 제한 하는 **없음**
    - 완료되면 **저장**을 선택하여 설정을 저장합니다.

> [!NOTE]
> - 만료 시간 간격 보다 오래 된 그룹을 처음으로 설정 하는 경우 그룹을 자동으로 갱신 하거나 소유자가 갱신 하지 않는 한 만료 될 때까지 35 일로 설정 됩니다.
> - 동적 그룹을 삭제 및 복원 하는 경우 새 그룹으로 표시 되 고 규칙에 따라 다시 채워집니다. 이 프로세스는 최대 24시간이 소요될 수 있습니다.
> - 팀에서 사용 하는 그룹에 대 한 만료 알림은 팀 소유자 피드에 표시 됩니다.

## <a name="email-notifications"></a>이메일 알림

그룹이 자동으로 갱신 되지 않은 경우이 그룹의 만료 전에 30 일, 15 일 및 1 일의 Microsoft 365 그룹 소유자에 게 전자 메일 알림이 전송 됩니다. 전자 메일의 언어는 그룹 소유자의 기본 설정 언어 또는 Azure AD 언어 설정에 따라 결정 됩니다. 그룹 소유자가 기본 설정 언어를 정의했거나 여러 소유자가 동일한 기본 설정 언어를 사용하는 경우 해당 언어가 사용됩니다. 다른 모든 경우에는 Azure AD 언어 설정이 사용 됩니다.

![만료 전자 메일 알림](./media/groups-lifecycle/expiration-notification.png)

그룹 **갱신** 알림 전자 메일에서 그룹 소유자는 [액세스 패널](https://account.activedirectory.windowsazure.com/r#/applications)의 그룹 세부 정보 페이지에 직접 액세스할 수 있습니다. 여기에 사용자는 그룹에 대해 마지막 갱신 시간, 예상 만료 시간, 그룹 갱신 능력 등에 대한 자세한 정보를 얻을 수 있습니다. 그룹 세부 정보 페이지에는 이제 그룹 소유자가 그룹의 내용과 작업을 편리 하 게 볼 수 있도록 Microsoft 365 그룹 리소스에 대 한 링크도 포함 되어 있습니다.

그룹이 만료되면 그룹은 만료 날짜 1일 후에 삭제됩니다. 이와 같은 전자 메일 알림은 Microsoft 365 그룹 소유자에 게 전송 되어 해당 Microsoft 365 그룹의 만료 및 후속 삭제를 알려 줍니다.

![그룹 삭제 전자 메일 알림](./media/groups-lifecycle/deletion-notification.png)

[Azure Active Directory에서 삭제 된 Microsoft 365 그룹 복원](groups-restore-deleted.md)에 설명 된 대로 그룹 **복원** 을 선택 하거나 PowerShell cmdlet을 사용 하 여 30 일 내에 그룹을 복원할 수 있습니다. 30일 그룹 복원 기간은 사용자 지정할 수 없습니다.

복원하는 그룹에 문서, SharePoint 사이트 또는 기타 영구 개체가 포함된 경우 그룹 및 해당 내용을 완전히 복원하는 데 최대 24시간이 걸릴 수 있습니다.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Microsoft 365 그룹 만료 날짜를 검색 하는 방법

사용자가 만료 날짜 및 마지막으로 갱신 된 날짜를 비롯 한 그룹 정보를 볼 수 있는 액세스 패널 외에도 Microsoft 365 그룹의 만료 날짜는 Microsoft Graph REST API Beta에서 검색할 수 있습니다. Microsoft Graph Beta에서는 그룹 속성으로 expirationDateTime를 사용할 수 있습니다. GET 요청을 사용 하 여 검색할 수 있습니다. 자세한 내용은 [이 예제](/graph/api/group-get?view=graph-rest-beta#example)를 참조 하세요.

> [!NOTE]
> 액세스 패널에서 그룹 멤버 자격을 관리 하려면 Azure Active Directory 그룹 일반 설정에서 "액세스 패널의 그룹에 대 한 액세스 제한"을 "아니요"로 설정 해야 합니다.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>법적 보류의 사서함에서 Microsoft 365 그룹 만료가 작동 하는 방식

그룹에 만료되고 삭제되면 삭제되고 30일 후에 Planner, Sites 또는 Teams 같은 앱의 그룹 데이터가 영구히 삭제되지만, 법적 보존 상태인 그룹 사서함은 영구적으로 삭제되지 않습니다. 관리자는 Exchange cmdlet을 사용하여 사서함을 복원한 후 데이터를 인출합니다.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>보존 정책에서 Microsoft 365 그룹 만료가 작동 하는 방식

보존 정책은 보안 및 준수 센터를 통해 구성됩니다. Microsoft 365 그룹에 대 한 보존 정책을 설정한 경우 그룹이 만료 되 고 삭제 되 면 그룹 사서함의 그룹 대화와 그룹 사이트의 파일이 보존 정책에 정의 된 특정 일 수 동안 보존 컨테이너에 보존 됩니다. 사용자는 만료된 후 그룹 또는 해당 콘텐츠를 볼 수 없지만, eDiscovery를 통해 사이트 및 사서함 데이터를 복구할 수 있습니다.

## <a name="powershell-examples"></a>PowerShell 예제

PowerShell cmdlet을 사용 하 여 Azure AD 조직에서 Microsoft 365 그룹에 대 한 만료 설정을 구성 하는 방법의 예는 다음과 같습니다.

1. PowerShell v 2.0 모듈을 설치 하 고 PowerShell 프롬프트에서 로그인 합니다.

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. 만료 설정 구성 AzureADMSGroupLifecyclePolicy cmdlet을 사용 하 여 Azure AD 조직의 모든 Microsoft 365 그룹에 대 한 수명을 365 일로 설정 합니다. 소유자가 없는 Microsoft 365 그룹에 대 한 갱신 알림이 ' ' (으)로 전송 됩니다. emailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. 기존 정책 가져오기 AzureADMSGroupLifecyclePolicy:이 cmdlet은 구성 된 현재 Microsoft 365 그룹 만료 설정을 검색 합니다. 이 예제에서는 다음을 확인할 수 있습니다.

   - 정책 ID
   - Azure AD 조직에 있는 모든 Microsoft 365 그룹의 수명은 365 일로 설정 됩니다.
   - 소유자가 없는 Microsoft 365 그룹에 대 한 갱신 알림은 ' ' (으)로 전송 됩니다 emailaddress@contoso.com .
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. 기존 정책 업데이트 Set-AzureADMSGroupLifecyclePolicy: 이 cmdlet은 기존 정책을 업데이트하는 데 사용됩니다. 아래 예제에서 기존 정책의 그룹 수명은 365일에서 180일로 변경됩니다.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. 정책에 특정 그룹 추가 Add-AzureADMSLifecyclePolicyGroup: 이 cmdlet은 수명 주기 정책에 그룹을 추가합니다. 예를 들어
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. 기존 정책 제거 AzureADMSGroupLifecyclePolicy:이 cmdlet은 Microsoft 365 그룹 만료 설정을 삭제 하지만 정책 ID가 필요 합니다. 이 cmdlet은 Microsoft 365 그룹에 대 한 만료를 사용 하지 않도록 설정 합니다.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
다음 cmdlet은 정책을 보다 자세히 구성하는 데 사용할 수 있습니다. 자세한 내용은 [PowerShell 설명서](/powershell/module/azuread/?branch=master&view=azureadps-2.0-preview#groups)를 참조 하세요.

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure AD 그룹에 대한 추가 정보를 제공합니다.

- [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹의 멤버 관리](../fundamentals/active-directory-groups-members-azure-portal.md)
- [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-dynamic-membership.md)