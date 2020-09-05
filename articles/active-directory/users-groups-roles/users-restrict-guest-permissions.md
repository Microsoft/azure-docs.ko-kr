---
title: 게스트 사용자 액세스 권한 제한-Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 Azure Portal, PowerShell 또는 Microsoft Graph를 사용 하 여 게스트 사용자 액세스 권한 제한
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 08/20/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28125ff55fe6ab3e68d56dc26a074d0498c2b413
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798434"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Azure Active Directory에서 게스트 액세스 권한 (미리 보기) 제한

Azure ad (Azure Active Directory)를 사용 하면 외부 게스트 사용자가 Azure AD의 조직에서 볼 수 있는 작업을 제한할 수 있습니다. 게스트 사용자는 기본적으로 Azure AD에서 제한 된 권한 수준으로 설정 되 고 멤버 사용자의 기본값은 전체 기본 사용자 권한 집합입니다. 이는 더 제한적인 액세스를 위해 Azure AD 조직의 외부 공동 작업 설정에서 새로운 게스트 사용자 권한 수준을 미리 볼 수 있도록 하기 때문에 이제 게스트 액세스를 선택할 수 있습니다.

권한 수준         | 액세스 수준
----------------         | ------------
멤버 사용자와 동일     | 게스트는 구성원 사용자와 동일한 Azure AD 리소스에 액세스할 수 있습니다.
제한 된 액세스 (기본값) | 게스트가 숨겨진 모든 그룹의 멤버 자격을 볼 수 있습니다.
**제한 된 액세스 (신규)**  | **게스트가 그룹의 멤버 자격을 볼 수 없습니다.**

게스트 액세스가 제한 된 경우 게스트는 자신의 사용자 프로필만 볼 수 있습니다. 게스트가 사용자 계정 이름 또는 objectId를 검색 하는 경우에도 다른 사용자를 볼 수 있는 권한은 허용 되지 않습니다. 또한 제한 된 액세스를 통해 게스트 사용자가 속한 그룹의 멤버 자격을 볼 수 없도록 제한 합니다. 게스트 사용자 권한을 비롯 한 전체 기본 사용자 권한에 대 한 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한](../fundamentals/users-default-permissions.md)을 참조 하십시오.

## <a name="permissions-and-licenses"></a>권한 및 라이선스

외부 공동 작업 설정을 구성 하려면 전역 관리자 역할에 있어야 합니다. 게스트 액세스를 제한 하는 추가 라이선스 요구 사항은 없습니다.

## <a name="update-in-the-azure-portal"></a>Azure Portal 업데이트

게스트 사용자 권한을 위해 기존 Azure Portal 컨트롤을 변경 했습니다.

> [!IMPORTANT]
> 잠시 동안 게스트 사용자 권한에 대 한 새 포털 컨트롤은 URL을 사용 하는 경우에만 표시 됩니다 [https://aka.ms/AADRestrictedGuestAccess](https://aka.ms/AADRestrictedGuestAccess) . PowerShell 및 Microsoft Graph는 아직 컨트롤을 설정 하는 데 사용할 수 있으며, 변경 내용은 포털에서 적용 됩니다.

1. 전역 관리자 권한으로 [AZURE AD 관리 센터](https://aka.ms/AADRestrictedGuestAccess) 에 로그인 합니다.
1. 조직의 **Azure Active Directory** 개요 페이지에서 **사용자 설정**을 선택 합니다.
1. **외부 사용자**에서 **외부 공동 작업 설정 관리**를 선택 합니다.
1. **외부 공동 작업 설정** 페이지에서 **게스트 사용자 액세스는 고유한 디렉터리 개체의 속성 및 멤버 자격으로 제한 됨** 옵션을 선택 합니다.

    ![Azure AD 외부 공동 작업 설정 페이지](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. **저장**을 선택합니다. 변경 내용은 게스트 사용자에 게 적용 되는 데 최대 15 분이 걸릴 수 있습니다.

## <a name="update-with-the-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 업데이트

Azure AD 조직에서 게스트 권한을 구성 하는 새로운 Microsoft Graph API를 추가 했습니다. 다음 API 호출을 사용 하 여 모든 권한 수준을 할당할 수 있습니다. 여기에 사용 되는 guestUserRoleId 값은 가장 제한 된 게스트 사용자 설정을 보여 주기 위한 것입니다. Microsoft Graph 사용 하 여 게스트 권한을 설정 하는 방법에 대 한 자세한 내용은 [Authorizationpolicy 리소스 종류](/graph/api/resources/authorizationpolicy)를 참조 하세요.

### <a name="configuring-for-the-first-time"></a>처음으로 구성

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

응답은 성공 204 이어야 합니다.

### <a name="updating-the-existing-value"></a>기존 값 업데이트

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

응답은 성공 204 이어야 합니다.

### <a name="view-the-current-value"></a>현재 값 보기

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

예제 응답:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>PowerShell cmdlet을 사용 하 여 업데이트

이 기능을 사용 하 여 PowerShell v2 cmdlet을 통해 제한 된 사용 권한을 구성 하는 기능을 추가 했습니다. 2.0.2.85 버전에 게시 된 PowerShell cmdlet을 가져오고 설정 합니다.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get 명령: AzureADMSAuthorizationPolicy

예제:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set 명령: AzureADMSAuthorizationPolicy

예제:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 요청 될 때 authorizationPolicy를 ID로 입력 해야 합니다.

## <a name="supported-microsoft-365-services"></a>지원 되는 Microsoft 365 서비스

### <a name="supported-services"></a>지원되는 서비스

지원 됨에 따라 환경이 예상 대로 작동 하는 것을 의미 합니다. 특히이는 현재 게스트 환경과 동일 합니다.

- Teams
- Outlook (OWA)
- SharePoint

### <a name="services-currently-not-supported"></a>현재 지원 되지 않는 서비스

현재 지원 하지 않는 서비스에는 새 게스트 제한 설정과의 호환성 문제가 있을 수 있습니다.

- 양식
- 팀의 플래너
- Planner 앱
- 프로젝트
- Yammer

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

질문 | Answer
-------- | ------
이러한 사용 권한은 어디에 적용 되나요? | 이러한 디렉터리 수준 권한은 Microsoft Graph, PowerShell v2, Azure Portal 및 My Apps 포털을 포함 하 여 Azure AD 서비스 및 포털 전체에 적용 됩니다. 공동 작업 시나리오를 위해 Office 365 그룹을 활용 하는 Microsoft 365 서비스는 특히 Outlook, Microsoft 팀 및 SharePoint에도 영향을 줍니다.
이 기능에 영향을 주는 내 앱 포털의 부분은 무엇입니까? | 내 앱 포털의 그룹 기능에는 이러한 새 사용 권한이 적용 됩니다. 여기에는 내 앱의 그룹 목록 및 그룹 멤버 자격을 볼 수 있는 모든 경로가 포함 됩니다. 그룹 타일 가용성이 변경 되지 않았습니다. 그룹 타일 가용성은 여전히 Azure 관리 포털의 기존 그룹 설정에 의해 제어 됩니다.
이러한 권한은 SharePoint 또는 Microsoft 팀 게스트 설정 보다 우선 합니다. | 아니요. 이러한 기존 설정은 여전히 이러한 응용 프로그램의 경험과 액세스를 제어 합니다. 예를 들어 SharePoint에서 문제가 표시 되는 경우 외부 공유 설정을 다시 확인 합니다.
Planner 및 Yammer에서 알려진 호환성 문제는 무엇 인가요? | <li>사용 권한이 ' 제한 됨 '으로 설정 되 면 Planner 앱에 로그인 하거나 Microsoft 팀의 Planner에 액세스 하면 해당 계획 또는 작업에 액세스할 수 없습니다.<li>사용 권한이 ' 제한 됨 '으로 설정 된 경우 Yammer에 로그인 한 게스트는 그룹을 벗어날 수 없습니다.
내 테 넌 트에서 기존 게스트 권한이 변경 됩니까? | 현재 설정이 변경 되지 않았습니다. 기존 설정과의 호환성을 유지 합니다. 변경 하려는 경우를 결정 합니다.
이러한 사용 권한은 기본적으로 설정 됩니까? | 아니요. 기존 기본 사용 권한은 변경 되지 않은 상태로 유지 됩니다. 필요에 따라 사용 권한을 더 제한적인 것으로 설정할 수 있습니다.
이 기능에 대 한 라이선스 요구 사항이 있나요? | 아니요,이 기능을 사용 하는 새로운 라이선스 요구 사항은 없습니다.

## <a name="next-steps"></a>다음 단계

- Azure AD의 기존 게스트 사용 권한에 대 한 자세한 내용은 [Azure Active Directory?의 기본 사용자 권한](../fundamentals/users-default-permissions.md)을 참조 하세요.
- 게스트 액세스를 제한 하는 Microsoft Graph API 메서드를 보려면 [Authorizationpolicy 리소스 유형](/graph/api/resources/authorizationpolicy)을 참조 하세요.
- 사용자에 대 한 모든 액세스를 취소 하려면 [AZURE AD에서 사용자 액세스 취소](users-revoke-access.md)를 참조 하세요.