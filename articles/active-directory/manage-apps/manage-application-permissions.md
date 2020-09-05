---
title: 사용자 및 관리자 권한 관리-Azure Active Directory | Microsoft Docs
description: Azure AD에서 응용 프로그램에 대 한 사용 권한을 검토 하 고 관리 하는 방법을 알아봅니다. 예를 들어 응용 프로그램에 부여 된 모든 사용 권한을 해지할 수 있습니다.
services: active-directory
author: mimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: mimart
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95e13cedc0cdbaedc8c00b9d855057da7e631c19
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510881"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Azure Active Directory에서 overprivileged 또는 의심 스러운 응용 프로그램에 대 한 조치를 취합니다.

응용 프로그램 사용 권한을 검토 하 고 관리 하는 방법을 알아봅니다. 이 문서에서는 시나리오에 따라 응용 프로그램을 보호 하기 위해 수행할 수 있는 다양 한 작업을 제공 합니다. 이러한 작업은 사용자 또는 관리자 동의를 통해 Azure Active Directory (Azure AD) 테 넌 트에 추가 된 모든 응용 프로그램에 적용 됩니다.

애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

다음 작업을 수행 하려면 전역 관리자, 응용 프로그램 관리자 또는 클라우드 응용 프로그램 관리자 권한으로 로그인 해야 합니다.

애플리케이션에 대한 액세스를 제한하려면 사용자 할당이 필요하며, 애플리케이션에 사용자 또는 그룹을 할당해야 합니다.  자세한 내용은 [사용자 및 그룹을 할당하는 메서드](methods-for-assigning-users-and-groups.md)를 참조하세요.

Azure AD 포털에 액세스 하 여 작업을 수행 하는 상황별 PowerShell 스크립트를 가져올 수 있습니다.
 
1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
4. **권한**을 선택합니다. 명령 모음에서 **사용 권한 검토**를 선택 합니다.

![권한 검토 창의 스크린샷](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>응용 프로그램에 대 한 액세스 제어

**사용자 할당** 설정을 켜서 응용 프로그램에 대 한 액세스를 제한 하는 것이 좋습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
4. **속성**을 선택 하 고 **필요한 사용자 요구 사항** 을 **예**로 설정 합니다.
5. **사용자 및 그룹**을 선택 하 고 응용 프로그램에 할당 된 원치 않는 사용자를 제거 합니다.
6. 응용 프로그램에 사용자 또는 그룹을 할당 합니다.

필요에 따라 PowerShell을 사용 하 여 응용 프로그램에 할당 된 모든 사용자를 제거할 수 있습니다.

## <a name="revoke-all-permissions-for-an-application"></a>응용 프로그램에 대 한 모든 권한 취소

PowerShell 스크립트를 사용 하 여이 응용 프로그램에 부여 된 모든 사용 권한을 취소 합니다.

> [!NOTE]
> 현재 부여 된 사용 권한을 취소 해도 사용자가 응용 프로그램에 다시 동의 하지 않습니다. 동의에서 사용자를 차단 하려는 경우 [응용 프로그램에 동의 하는 방법 구성](configure-user-consent.md)을 참조 하세요.

필요에 따라 응용 프로그램을 사용 하지 않도록 설정 하 여 사용자가 앱에 액세스 하지 못하도록 하 고 응용 프로그램에서 데이터에 액세스 하지 못하도록 할 수 있습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
4. **속성**을 선택한 다음 **사용자가 로그인 할** 수 있도록 설정을 **아니요**로 설정 합니다.

## <a name="investigate-a-suspicious-application"></a>의심 스러운 응용 프로그램 조사

**사용자 할당** 설정을 켜서 응용 프로그램에 대 한 액세스를 제한 하는 것이 좋습니다. 그런 다음 사용자 및 관리자가 응용 프로그램에 부여한 사용 권한을 검토 합니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
3. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
5. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
6. **속성**을 선택 하 고 **필요한 사용자 요구 사항** 을 **예**로 설정 합니다.
7. **권한**을 선택 하 고 admin 및 user 동의한 권한을 검토 합니다.

필요에 따라 PowerShell을 사용 하 여 다음을 수행할 수 있습니다.

- 할당 된 모든 사용자를 제거 하 여 응용 프로그램에 로그인 하지 않도록 합니다.
- 응용 프로그램에 대 한 액세스 권한이 있는 사용자에 대 한 새로 고침 토큰을 무효화 합니다.
- 응용 프로그램에 대 한 모든 사용 권한을 취소 합니다.

또는 사용자의 액세스를 차단 하 고 응용 프로그램의 데이터 액세스를 중지 하도록 응용 프로그램을 사용 하지 않도록 설정할 수 있습니다.


## <a name="disable-a-malicious-application"></a>악의적인 응용 프로그램 사용 안 함 

응용 프로그램을 사용 하지 않도록 설정 하 여 사용자의 액세스를 차단 하 고 응용 프로그램이 데이터에 액세스 하지 못하도록 하는 것이 좋습니다. 대신 응용 프로그램을 삭제 하는 경우 사용자가 응용 프로그램을 다시 승인 하 고 데이터에 대 한 액세스 권한을 부여할 수 있습니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
4. **속성**을 선택한 다음 개체 ID를 복사 합니다.

### <a name="powershell-commands"></a>PowerShell 명령


서비스 사용자 개체 ID를 검색 합니다.

1. 전역 관리자, 애플리케이션 관리자 또는 클라우드 애플리케이션 관리자 권한으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**을 선택 합니다.
3. 액세스를 제한 하려는 응용 프로그램을 선택 합니다.
4. **속성**을 선택한 다음 개체 ID를 복사 합니다.

```powershell
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $sp.ObjectId
```
응용 프로그램에 할당 된 모든 사용자를 제거 합니다.
 ```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get Azure AD App role assignments using objectId of the Service Principal
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

    # Remove all users and groups assigned to the application
    $assignments | ForEach-Object {
        if ($_.PrincipalType -eq "User") {
            Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        } elseif ($_.PrincipalType -eq "Group") {
            Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
        }
    }
 ```

응용 프로그램에 부여 된 사용 권한을 취소 합니다.

```powershell
    Connect-AzureAD

    # Get Service Principal using objectId
    $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

    # Get all delegated permissions for the service principal
    $spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

    # Remove all delegated permissions
    $spOAuth2PermissionsGrants | ForEach-Object {
        Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
    }

    # Get all application permissions for the service principal
    $spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

    # Remove all delegated permissions
    $spApplicationPermissions | ForEach-Object {
        Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
    }
```
새로 고침 토큰을 무효화 합니다.
```powershell
        Connect-AzureAD

        # Get Service Principal using objectId
        $sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

        # Get Azure AD App role assignments using objectID of the Service Principal
        $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

        # Revoke refresh token for all users assigned to the application
        $assignments | ForEach-Object {
            Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
        }
```
## <a name="next-steps"></a>다음 단계
- [응용 프로그램에 대 한 동의 관리 및 동의 요청 평가](manage-consent-requests.md)
- [사용자 동의 구성](configure-user-consent.md)
- [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
