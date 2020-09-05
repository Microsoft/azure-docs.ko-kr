---
title: Azure AD 그룹에 디렉터리 읽기 권한자 역할 할당 및 역할 할당 관리
description: 이 문서에서는 Azure SQL Database, Azure SQL Managed Instance 및 Azure Synapse Analytics에서 Azure AD 역할 할당을 관리할 수 있도록 Azure AD 그룹을 사용하여 디렉터리 읽기 권한자 역할을 사용하도록 설정하는 방법을 안내합니다.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: ca330357e88ff6f4824c74a6048769638542cc29
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556107"
---
# <a name="tutorial-assign-directory-readers-role-to-an-azure-ad-group-and-manage-role-assignments"></a>자습서: Azure AD 그룹에 디렉터리 읽기 권한자 역할 할당 및 역할 할당 관리

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> 이 문서에서 설명하는 그룹에 **디렉터리 읽기 권한자** 역할 할당은 현재 **공개 미리 보기**로 제공됩니다. 

이 문서에서는 Azure AD(Azure Active Directory)에서 그룹을 만들고 해당 그룹에 [**디렉터리 읽기 권한자**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 역할을 할당하는 방법을 안내합니다. 그룹 소유자는 디렉터리 읽기 권한자 권한을 통해 [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) 및 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)의 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) 같은 그룹에 멤버를 추가할 수 있습니다. 이렇게 하면 [전역 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)가 테넌트의 Azure SQL 논리 서버 ID마다 디렉터리 읽기 권한자 역할을 직접 할당할 필요가 없습니다.

이 자습서에서는 [클라우드 그룹을 사용하여 Azure Active Directory에서 역할 할당 관리](../../active-directory/users-groups-roles/roles-groups-concept.md)에 도입된 기능을 사용합니다. 

Azure SQL에 대한 Azure AD 그룹에 디렉터리 읽기 권한자 역할을 할당하여 얻을 수 있는 이점에 대한 자세한 내용은 [Azure SQL용 Azure Active Directory의 디렉터리 읽기 권한자 역할](authentication-aad-directory-readers-role.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure AD 인스턴스. 자세한 내용은 [Azure SQL에서 Azure AD 인증 구성 및 관리](authentication-aad-configure.md)를 참조하세요.
- SQL Database, SQL Managed Instance 또는 Azure Synapse.

## <a name="directory-readers-role-assignment-using-the-azure-portal"></a>Azure Portal을 사용하여 디렉터리 읽기 권한자 역할 할당

### <a name="create-a-new-group-and-assign-owners-and-role"></a>새 그룹을 만들고 소유자 및 역할 할당

1. 처음 설치하는 경우 [전역 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) 권한이 있는 사용자가 필요합니다.
1. 권한 있는 사용자가 [Azure Portal](https://portal.azure.com)에 로그인하도록 합니다.
1. **Azure Active Directory** 리소스로 이동합니다. **관리**에서 **그룹**으로 이동합니다. **새 그룹**을 선택하여 새 그룹을 만듭니다.
1. 그룹 유형으로 **보안**을 선택하고, 나머지 필드를 입력합니다. **Azure AD 역할을 그룹에 할당할 수 있음(미리 보기)** 을 **예**로 전환합니다. 그런 다음, Azure AD **디렉터리 읽기 권한자** 역할을 그룹에 할당합니다.
1. 만든 그룹에 Azure AD 사용자를 소유자로 할당합니다. Azure AD 관리 역할이 할당되지 않은 일반 AD 사용자도 그룹 소유자가 될 수 있습니다. 소유자는 SQL Database, SQL Managed Instance 또는 Azure Synapse를 관리하는 사용자여야 합니다.

   :::image type="content" source="media/authentication-aad-directory-readers-role/new-group.png" alt-text="새-그룹-추가":::

1. **만들기**를 선택합니다.

### <a name="checking-the-group-that-was-created"></a>생성된 그룹 확인

> [!NOTE]
> **그룹 유형**이 **보안**인지 확인합니다. Azure SQL은 *Microsoft 365* 그룹을 지원하지 않습니다.

그룹이 만들어졌는지 확인하고 관리하려면 Azure Portal에서 **그룹** 창으로 돌아가서 해당 그룹 이름을 검색합니다. 그룹을 선택한 후 **관리** 설정의 **소유자** 및 **구성원** 메뉴에서 소유자 및 구성원을 추가할 수 있습니다. 그룹에 **할당된 역할**을 검토할 수도 있습니다.

:::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-group-created.png" alt-text="azure-ad-group-created":::

### <a name="add-azure-sql-managed-identity-to-the-group"></a>그룹에 Azure SQL 관리 ID 추가

> [!NOTE]
> 이 예제에서는 SQL Managed Instance를 사용하지만, SQL Database 또는 Azure Synapse에도 유사한 단계를 적용하여 같은 결과를 얻을 수 있습니다.

후속 단계에서는 전역 관리자 또는 권한 있는 역할 관리자 사용자가 더 이상 필요 없습니다.

1. SQL Managed Instance를 관리하는 사용자이자 앞에서 만든 그룹의 소유자로 Azure Portal에 로그인합니다.

1. Azure Portal에서 **SQL 관리형 인스턴스** 리소스 이름을 찾습니다.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance.png" alt-text="azure-ad-managed-instance":::

   SQL Managed Instance를 만드는 동안 인스턴스에 대한 Azure ID가 생성되었습니다. 생성된 ID의 이름은 SQL Managed Instance 이름의 접두사와 동일합니다. 다음 단계에 따라 Azure AD 애플리케이션으로 생성된 SQL Managed Instance ID의 서비스 주체를 찾을 수 있습니다.

    - **Azure Active Directory** 리소스로 이동합니다. **관리** 설정에서 **엔터프라이즈 애플리케이션**을 선택합니다. **개체 ID**는 인스턴스의 ID입니다.
    
    :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-managed-instance-service-principal.png" alt-text="azure-ad-managed-instance-service-principal":::

1. **Azure Active Directory** 리소스로 이동합니다. **관리**에서 **그룹**으로 이동합니다. 앞에서 만든 그룹을 선택합니다. 그룹의 **관리** 설정에서 **구성원**을 선택합니다. **구성원 추가**를 선택하고, 위에서 찾은 이름을 검색하여 SQL Managed Instance 서비스 주체를 그룹 구성원으로 추가합니다.

   :::image type="content" source="media/authentication-aad-directory-readers-role/azure-ad-add-managed-instance-service-principal.png" alt-text="azure-ad-add-managed-instance-service-principal":::

> [!NOTE]
> Azure 시스템을 통해 서비스 주체 권한을 전파하고 Azure AD Graph API에 대한 액세스를 허용하는 데 몇 분 정도 걸릴 수 있습니다. SQL Managed Instance에 대한 Azure AD 관리자를 프로비저닝하려면 몇 분 정도 기다려야 할 수 있습니다.

### <a name="remarks"></a>설명

SQL Database 및 Azure Synapse의 경우 Azure SQL 논리 서버를 만드는 동안 또는 서버를 만든 후 서버 ID를 만들 수 있습니다. SQL Database 또는 Azure Synapse에서 서버 ID를 만들거나 설정하는 방법에 대한 자세한 내용은 [서비스 주체를 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users)를 참조하세요.

SQL Managed Instance의 경우 [관리형 인스턴스에 대한 Azure AD 관리자를 설정](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)하려면 먼저 관리형 인스턴스 ID에 **디렉터리 읽기 권한자** 역할을 할당해야 합니다.

논리 서버에 대한 Azure AD 관리자를 설정할 때에는 SQL Database 또는 Azure Synapse에서 서버 ID에 **디렉터리 읽기 권한자** 역할을 할당할 필요가 없습니다. 그러나 Azure AD 애플리케이션 대신 SQL Database 또는 Azure Synapse에서 Azure AD 개체 만들기를 사용하려면 **디렉터리 읽기 권한자** 역할이 필요합니다. SQL 논리 서버 ID에 역할이 할당되지 않으면 Azure SQL에서 Azure AD 사용자를 만들 수 없습니다. 자세한 내용은 [Azure SQL을 사용하는 Azure Active Directory 서비스 주체](authentication-aad-service-principal.md)를 참조하세요.

## <a name="directory-readers-role-assignment-using-powershell"></a>PowerShell을 사용하여 디렉터리 읽기 권한자 역할 할당

> [!IMPORTANT]
> [전역 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 또는 [권한 있는 역할 관리자](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)는 다음과 같은 초기 단계를 실행해야 합니다. PowerShell 외에도, Azure AD는 [Azure AD에서 역할 할당 가능 그룹을 만드는](../../active-directory/users-groups-roles/roles-groups-create-eligible.md#using-microsoft-graph-api) Microsoft Graph API를 제공합니다.

1. 다음 명령을 사용하여 Azure AD Preview PowerShell 모듈을 다운로드합니다. 관리자 권한으로 PowerShell을 실행해야 할 수도 있습니다.

    ```powershell
    Install-Module azureadpreview
    Import-Module azureadpreview
    #To verify that the module is ready to use, use the following command:
    Get-Module azureadpreview
    ```

1. Azure AD 테넌트에 연결합니다.

    ```powershell
    Connect-AzureAD
    ```

1. **디렉터리 읽기 권한자** 역할을 할당하는 보안 그룹을 만듭니다.

    - `DirectoryReaderGroup`, `Directory Reader Group` 및 `DirRead`를 원하는 대로 변경할 수 있습니다.

    ```powershell
    $group = New-AzureADMSGroup -DisplayName "DirectoryReaderGroup" -Description "Directory Reader Group" -MailEnabled $False -SecurityEnabled $true -MailNickName "DirRead" -IsAssignableToRole $true
    $group
    ```

1. 그룹에 **디렉터리 읽기 권한자** 역할을 할당합니다.

    ```powershell
    # Displays the Directory Readers role information
    $roleDefinition = Get-AzureADMSRoleDefinition -Filter "displayName eq 'Directory Readers'" 
    $roleDefinition
    ```

    ```powershell
    # Assigns the Directory Readers role to the group
    $roleAssignment = New-AzureADMSRoleAssignment -ResourceScope '/' -RoleDefinitionId $roleDefinition.Id -PrincipalId $group.Id
    $roleAssignment
    ```

1. 그룹에 소유자를 할당합니다.

    - `<username>`을 이 그룹을 소유할 사용자로 바꿉니다. 이러한 단계를 반복하여 여러 소유자를 추가할 수 있습니다.

    ```powershell
    $RefObjectID = Get-AzureADUser -ObjectId "<username>"
    $RefObjectID
    ```

    ```powershell
    $GrOwner = Add-AzureADGroupOwner -ObjectId $group.ID -RefObjectId $RefObjectID.ObjectID
    ```

    다음 명령을 사용하여 그룹의 소유자를 확인합니다.

    ```powershell
    Get-AzureADGroupOwner -ObjectId $group.ID
    ```

    [Azure Portal](https://portal.azure.com)에서 그룹 소유자를 확인할 수도 있습니다. [생성된 그룹 확인](#checking-the-group-that-was-created)의 단계를 따릅니다.

### <a name="assigning-the-service-principal-as-a-member-of-the-group"></a>서비스 주체를 그룹의 구성원으로 할당

후속 단계에서는 전역 관리자 또는 권한 있는 역할 관리자 사용자가 더 이상 필요 없습니다.

1. Azure SQL 리소스도 관리하는 그룹의 소유자를 사용하여 Azure AD에 연결하는 다음 명령을 실행합니다.

    ```powershell
    Connect-AzureAD
    ```

1. 서비스 주체를 앞에서 만든 그룹의 구성원으로 할당합니다.

    - `<ServerName>`을 Azure SQL 논리 서버 이름 또는 Managed Instance 이름으로 바꿉니다. 자세한 내용은 [그룹에 Azure SQL 서비스 ID 추가](#add-azure-sql-managed-identity-to-the-group) 섹션을 참조하세요.

    ```powershell
    # Returns the service principal of your Azure SQL resource
    $miIdentity = Get-AzureADServicePrincipal -SearchString "<ServerName>"
    $miIdentity
    ```

    ```powershell
    # Adds the service principal to the group as a member
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId 
    ```

    다음 명령은 그룹에 추가되었음을 나타내는 서비스 주체 개체 ID를 반환합니다.

    ```powershell
    Add-AzureADGroupMember -ObjectId $group.ID -RefObjectId $miIdentity.ObjectId
    ```

## <a name="next-steps"></a>다음 단계

- [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)
- [자습서: Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기](authentication-aad-service-principal-tutorial.md)
- [Azure SQL에서 Azure AD 인증 구성 및 관리](authentication-aad-configure.md)