---
title: Azure AD 포털에서 사용자 지정 역할 할당 보기 | Microsoft Docs
description: 이제 Azure AD 관리 센터에서 Azure AD 관리자 역할의 멤버를 보고 관리할 수 있습니다.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb64a84388a370e77f39369f78c3086ec1cd00ec
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798349"
---
# <a name="view-custom-role-assignments-in-azure-active-directory"></a>Azure Active Directory에서 사용자 지정 역할 할당 보기

이 문서에서는 Azure Active Directory (Azure AD)에서 할당 한 사용자 지정 역할을 보는 방법을 설명 합니다. Azure AD (Azure Active Directory)에서 역할은 조직 전체의 범위 또는 단일 응용 프로그램 범위에서 할당 될 수 있습니다.

- 조직 전체의 범위에서 역할 할당은에 추가 되 고 단일 응용 프로그램 역할 할당 목록에서 볼 수 있습니다.
- 단일 응용 프로그램 범위의 역할 할당은에 추가 되지 않으며 조직 차원의 범위 할당 목록에 표시 되지 않습니다.

## <a name="view-role-assignments-in-the-azure-portal"></a>Azure Portal에서 역할 할당 보기

이 절차에서는 조직 전체 범위에서 역할의 할당을 확인 하는 방법을 설명 합니다.

1. Azure AD 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로  [Azure AD 관리 센터](https://aad.portal.azure.com) 에 로그인합니다.
1. **Azure Active Directory**를 선택 하 고 **역할 및 관리자**를 선택한 다음 역할을 선택 하 여 열고 해당 속성을 확인 합니다.
1. **할당** 을 선택 하 여 역할에 대 한 할당을 확인 합니다.

    ![목록에서 역할을 열 때 역할 할당 및 사용 권한 보기](./media/roles-view-assignments/role-assignments.png)

## <a name="view-role-assignments-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용 하 여 역할 할당 보기

이 섹션에서는 조직 전체 범위에서 역할의 할당을 보는 방법을 설명 합니다. 이 문서에서는 [Azure Active Directory PowerShell 버전 2](/powershell/module/azuread/?view=azureadps-2.0#directory_roles) 모듈을 사용 합니다. PowerShell을 사용 하 여 단일 응용 프로그램 범위 할당을 보려면 PowerShell을 사용 하 [여 사용자 지정 역할 할당](roles-assign-powershell.md)에서 cmdlet을 사용할 수 있습니다.

### <a name="prepare-powershell"></a>PowerShell 준비

먼저 [AZURE AD Preview PowerShell 모듈을 다운로드](https://www.powershellgallery.com/packages/AzureAD/)해야 합니다.

Azure AD PowerShell 모듈을 설치하려면 다음 명령을 사용합니다.

``` PowerShell
install-module azureadpreview
import-module azureadpreview
```

모듈을 사용할 수 있는지 확인하려면 다음 명령을 사용합니다.

``` PowerShell
get-module azuread
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

### <a name="view-the-assignments-of-a-role"></a>역할 할당 보기

역할 할당 보기의 예입니다.

``` PowerShell
# Fetch list of all directory roles with object ID
Get-AzureADDirectoryRole

# Fetch a specific directory role by ID
$role = Get-AzureADDirectoryRole -ObjectId "5b3fe201-fa8b-4144-b6f1-875829ff7543"

# Fetch role membership for a role
Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId | Get-AzureADUser
```

## <a name="view-role-assignments-using-microsoft-graph-api"></a>Microsoft Graph API를 사용 하 여 역할 할당 보기

이 섹션에서는 조직 전체 범위에서 역할의 할당을 보는 방법을 설명 합니다.  Graph API를 사용 하 여 단일 응용 프로그램 범위 할당을 보려면 Graph API를 사용 하 [여 사용자 지정 역할 할당](roles-assign-graph.md)에서 작업을 사용할 수 있습니다.

지정된 역할 정의에 대한 역할 할당을 가져오기 위한 HTTP 요청입니다.

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

응답

``` HTTP
HTTP/1.1 200 OK
{
    "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
    "resourceScopes":["/"]
}
```

## <a name="view-assignments-of-single-application-scope"></a>단일 응용 프로그램 범위의 할당 보기

이 섹션에서는 단일 응용 프로그램 범위를 사용 하는 역할의 할당을 보는 방법을 설명 합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.

1. Azure AD 조직에서 권한 있는 역할 관리자 또는 전역 관리자 권한으로  [Azure AD 관리 센터](https://aad.portal.azure.com) 에 로그인합니다.
1. **앱 등록**를 선택 하 고 앱 등록을 선택 하 여 해당 속성을 확인 합니다. Azure AD 조직에서 앱 등록의 전체 목록을 보려면 **모든 애플리케이션**을 선택해야 할 수도 있습니다.

    ![앱 등록 페이지에서 앱 등록 만들기 또는 편집](./media/roles-create-custom/appreg-all-apps.png)

1. 앱 등록에서 **역할 및 관리자**를 선택한 다음 해당 속성을 볼 역할을 선택 합니다.

    ![앱 등록 페이지에서 앱 등록 역할 할당 보기](./media/roles-view-assignments/appreg-assignments.png)

1. **할당** 을 선택 하 여 역할에 대 한 할당을 확인 합니다. 앱 등록 내에서 할당 보기를 열면이 Azure AD 리소스로 범위가 지정 된 할당이 표시 됩니다.

    ![앱 등록의 속성에서 앱 등록 역할 할당 보기](./media/roles-view-assignments/appreg-assignments-2.png)

## <a name="next-steps"></a>다음 단계

* [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
* 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
* 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.