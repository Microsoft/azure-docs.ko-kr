---
title: Azure Active Directory 인증 구성
description: Azure AD를 구성한 후 Azure Active Directory 인증을 사용 하 여 SQL Database, 관리 되는 인스턴스 및 SQL Data Warehouse에 연결 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 01/07/2020
ms.openlocfilehash: dc2661bbc443201d6a2da4b5efb7ecdc2caad444
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732572"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>SQL을 사용하여 Azure Active Directory 인증 구성 및 관리

이 문서에서는 azure AD를 만들고 채운 다음 azure [SQL Database](sql-database-technical-overview.md), [관리 되는 인스턴스](sql-database-managed-instance.md)및 [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에서 azure ad를 사용 하는 방법을 보여 줍니다. 개요는 [Azure Active Directory 인증](sql-database-aad-authentication.md)을 참조하세요.

> [!NOTE]
> 이 문서는 Azure SQL 서버 및 Azure SQL 서버에서 생성된 SQL Database와 SQL Data Warehouse 데이터베이스에 적용됩니다. 간단히 하기 위해 SQL Database는 SQL Database와 SQL Data Warehouse를 참조할 때 사용됩니다.

> [!IMPORTANT]  
> Azure VM에서 실행되는 SQL Server에 연결하는 경우 Azure Active Directory 계정은 사용할 수 없습니다. 대신 도메인 Active Directory 계정을 사용합니다.

## <a name="create-and-populate-an-azure-ad"></a>Azure AD 만들기 및 채우기

Azure AD를 만들고 사용자 및 그룹으로 채웁니다. Azure AD는 초기 Azure AD 관리되는 도메인일 수 있습니다. Azure AD는 Azure AD와 페더레이션된 온-프레미스 Active Directory Domain Services일 수도 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../active-directory/hybrid/whatis-hybrid-identity.md), [Azure AD에 고유한 도메인 이름 추가](../active-directory/active-directory-domains-add-azure-portal.md), [이제 Microsoft Azure에서 Windows Server Active Directory와의 페더레이션 지원](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Azure AD 디렉터리 관리](../active-directory/fundamentals/active-directory-administer.md), [Windows PowerShell을 사용한 Azure AD 관리](/powershell/azure/overview) 및 [포트 및 프로토콜이 필요한 하이브리드 ID](../active-directory/hybrid/reference-connect-ports.md)를 참조하세요.

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Azure Active Directory에 Azure 구독 연결 또는 추가

1. 해당 디렉터리를 데이터베이스를 호스트하는 Azure 구독에서 신뢰할 수 있는 디렉터리로 만들어 Azure Active Directory에 데이터베이스를 연결합니다. 자세한 내용은 [Azure 구독과 Azure AD의 연관 관계](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)를 참조하세요.

2. Azure Portal의 디렉터리 전환기를 사용하여 도메인과 연결된 구독으로 전환합니다.

   > [!IMPORTANT]
   > 모든 Azure 구독은 Azure AD 인스턴스와 트러스트 관계가 있습니다. 이는 Azure 구독이 사용자, 서비스, 디바이스를 인증하는 해당 디렉터리를 신뢰함을 의미합니다. 여러 구독에서 동일한 디렉터리를 신뢰할 수 있지만 구독은 하나의 디렉터리만 신뢰합니다. 구독이 디렉터리와 갖는 이 트러스트 관계는 구독이 Azure의 다른 모든 리소스(웹 사이트, 데이터베이스 등)와 갖는 관계와 다르며 구독의 하위 리소스와 더 유사합니다. 구독이 만료되면 구독과 연결된 다른 리소스에 대한 액세스도 중지됩니다. 하지만 디렉터리는 Azure에 남아 있으며 해당 디렉터리와 다른 구독을 연결하여 디렉터리 사용자를 계속 관리할 수 있습니다. 리소스에 대한 자세한 내용은 [Azure의 리소스 액세스 이해](../active-directory/active-directory-b2b-admin-add-users.md)를 참조하세요. 이러한 신뢰 관계에 대한 자세한 내용은 [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)을 참조하세요.

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Azure SQL Server에 대한 Azure AD 관리자 만들기

각각의 Azure SQL Server(SQL Database 또는 SQL Data Warehouse를 호스트하는)는 전체 Azure SQL Server의 관리자인 단일 서버 관리자 계정으로 시작됩니다. Azure AD 계정인 두 번째 SQL Server 관리자를 만들어야 합니다. 이 주체는 마스터 데이터베이스에 포함된 데이터베이스 사용자로 생성됩니다. 관리자인 서버 관리자 계정은 모든 사용자 데이터베이스에서 **db_owner** 역할의 멤버이며 각 사용자 데이터베이스에 **dbo** 사용자로 들어갑니다. 서버 관리자 계정에 대한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.

Azure Active Directory와 함께 지역에서 복제를 사용할 때 Azure Active Directory 관리자는 주 서버와 보조 서버 모두에 대해 구성되어야 합니다. 서버에 Azure Active Directory 관리자가 없는 경우 다음 Azure Active Directory 로그인 및 사용자는 서버에 "연결할 수 없음" 오류를 수신합니다.

> [!NOTE]
> Azure AD 계정을 기반으로 하지 않는 사용자(Azure SQL Server 관리자 계정 포함)는 Azure AD에서 제안된 데이터베이스 사용자에 대한 유효성 검사 권한이 없으므로 Azure AD 기반 사용자를 만들 수 없습니다.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>관리 되는 인스턴스에 대 한 Azure Active Directory 관리자 프로 비전

> [!IMPORTANT]
> 관리 되는 인스턴스를 프로 비전 하는 경우에만 다음 단계를 수행 합니다. 이 작업은 글로벌/회사 관리자 또는 Azure AD의 권한 있는 역할 관리자만 실행할 수 있습니다. 다음 단계에서는 디렉터리에서 서로 다른 권한을 가진 사용자에 대해 사용 권한을 부여하는 프로세스를 설명합니다.

> [!NOTE]
> GA 이전에 생성 된 Azure AD 관리자의 경우에는 GA 이전에는 계속 작동 하지만 기존 동작에는 기능이 변경 되지 않습니다. 자세한 내용은 [MI에 대 한 새 AZURE AD 관리 기능](#new-azure-ad-admin-functionality-for-mi) 섹션을 참조 하세요.

관리 되는 인스턴스에는 보안 그룹 구성원 자격 또는 새 사용자 만들기를 통한 사용자 인증 등의 작업을 성공적으로 수행 하기 위해 Azure AD를 읽을 수 있는 권한이 필요 합니다. 이 작업을 수행 하려면 관리 되는 인스턴스에 Azure AD를 읽을 수 있는 권한을 부여 해야 합니다. 이 작업은 포털 및 PowerShell의 두 가지 방법으로 수행할 수 있습니다. 다음 단계에서는 두 방법을 모두 안내합니다.

1. Azure Portal의 상단 오른쪽 끝에서 해당 연결을 선택하여 가능한 Active Directory 목록을 드롭다운합니다.

2. 정확한 Active Directory를 기본 Azure AD로 선택합니다.

   이 단계는 Active Directory와 연결된 구독을 Managed Instance와 연결하여 동일한 구독이 Azure AD 및 Managed Instance 둘 다에 사용되도록 합니다.

3. Managed Instance로 이동한 후 Azure AD 통합에 사용할 인스턴스를 선택합니다.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Active Directory 관리자 페이지 위쪽에서 배너를 선택하고 현재 사용자에게 권한을 부여합니다. Azure AD에서 전역/회사 관리자 권한으로 로그인 한 경우에는 Azure Portal에서 또는 아래의 스크립트를 사용 하 여 PowerShell을 사용 하 여이 작업을 수행할 수 있습니다.

    ![권한 부여-포털](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null) {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1)) {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null) {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. 작업이 성공적으로 완료되면 다음과 같은 알림이 오른쪽 위 모서리에 표시됩니다.

    ![성공](./media/sql-database-aad-authentication/success.png)

6. 이제 관리 되는 인스턴스에 대해 Azure AD 관리자를 선택할 수 있습니다. 이에 대해 Active Directory 관리자 페이지에서 **관리자 설정** 명령을 선택합니다.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. AAD 관리 페이지에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택**을 선택합니다.

   Active Directory 관리 페이지에 해당 Active Directory에 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. [Azure AD 기능 및 제한 사항](sql-database-aad-authentication.md#azure-ad-features-and-limitations) 에서 지원되는 관리자 목록을 참조하세요. RBAC(역할 기반 액세스 제어)는 Azure Portal에만 적용되며 SQL Server에 전파되지 않습니다.

    ![Azure Active Directory 관리자 추가](./media/sql-database-aad-authentication/add-azure-active-directory-admin.png)

8. Active directory 관리자 페이지 위쪽에서 **저장**을 선택합니다.

    ![저장](./media/sql-database-aad-authentication/save.png)

    관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 Active Directory 관리자 상자에 표시됩니다.

관리 되는 인스턴스에 대해 Azure AD 관리자를 프로 비전 한 후 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a> 구문을 사용 하 여 azure ad 서버 보안 주체 (로그인) 만들기를 시작할 수 있습니다. 자세한 내용은 [관리 되는 인스턴스 개요](sql-database-managed-instance.md#azure-active-directory-integration)를 참조 하세요.

> [!TIP]
> 나중에 관리자를 제거하려면, Active Directory 관리자 페이지 위쪽에서 **관리자 제거**를 선택한 다음, **저장**을 선택합니다.

### <a name="new-azure-ad-admin-functionality-for-mi"></a>MI 용 새 Azure AD 관리 기능

아래 표에는 Azure AD 로그인을 위해 GA와 함께 제공 되는 공개 미리 보기 Azure AD 로그인 관리자의 기능 및 새 기능이 요약 되어 있습니다.

| 공개 미리 보기로 제공 되는 MI 용 Azure AD 로그인 관리자 | MI 용 Azure AD 관리자에 대 한 GA 기능 |
| --- | ---|
| 는 azure ad 인증을 가능 하 게 하는 SQL Database에 대 한 Azure AD 관리자와 비슷한 방식으로 작동 하지만 Azure ad 관리자는 MI 용 master db에 Azure AD 또는 SQL 로그인을 만들 수 없습니다. | Azure AD 관리자는 sysadmin 권한을 가지 며 MI 용 master db에서 AAD 및 SQL 로그인을 만들 수 있습니다. |
| 은 (는) server_principals 뷰에 없습니다. | 는 sys. server_principals 뷰에 있습니다. |
| 개별 Azure AD 게스트 사용자를 Azure AD admin for MI로 설정할 수 있습니다. 자세한 내용은 [Azure Portal에서 B2B 공동 작업 사용자 추가 Azure Active Directory](../active-directory/b2b/add-users-administrator.md)를 참조 하세요. | 이 그룹을 MI 용 Azure AD 관리자로 설정 하려면 게스트 사용자를 구성원으로 사용 하 여 Azure AD 그룹을 만들어야 합니다. 자세한 내용은 [AZURE AD business to business 지원](sql-database-ssms-mfa-authentication.md#azure-ad-business-to-business-support)을 참조 하세요. |

GA 이전에 만든 MI에 대해 기존 Azure AD 관리자를 사용 하는 것이 가장 좋은 방법으로, 동일한 Azure AD 사용자 또는 그룹에 대 한 "관리자 제거" 및 "관리자 설정" 옵션 Azure Portal 사용 하 여 Azure AD 관리자를 다시 설정 합니다.

### <a name="known-issues-with-the-azure-ad-login-ga-for-mi"></a>MI 용 Azure AD 로그인 GA의 알려진 문제

- Azure AD 로그인이 T-sql 명령 `CREATE LOGIN [myaadaccount] FROM EXTERNAL PROVIDER`를 사용 하 여 만든 MI의 master 데이터베이스에 있는 경우 MI에 대 한 Azure AD 관리자로 설정할 수 없습니다. Azure AD 로그인을 만드는 데 Azure Portal, PowerShell 또는 CLI 명령을 사용 하 여 Azure AD 관리자로 로그인을 설정 하는 동안 오류가 발생 합니다.
  - 계정을 Azure AD 관리자로 만들려면 먼저 명령을 `DROP LOGIN [myaadaccount]`사용 하 여 master 데이터베이스에서 로그인을 삭제 해야 합니다.
  - `DROP LOGIN` 성공한 후 Azure Portal에서 Azure AD 관리자 계정을 설정 합니다. 
  - Azure AD 관리자 계정을 설정할 수 없는 경우 로그인에 대 한 관리 되는 인스턴스의 master 데이터베이스를 확인 합니다. `SELECT * FROM sys.server_principals` 명령을 사용합니다.
  - MI에 대 한 Azure AD 관리자를 설정 하면이 계정의 master 데이터베이스에 로그인이 자동으로 생성 됩니다. Azure AD 관리자를 제거 하면 해당 로그인이 master 데이터베이스에서 자동으로 삭제 됩니다.

- 개별 Azure AD 게스트 사용자는 MI 용 Azure AD 관리자로 지원 되지 않습니다. 게스트 사용자는 azure AD 관리자로 설정할 Azure AD 그룹의 일부 여야 합니다. 현재 Azure Portal 블레이드에서 다른 Azure AD에 대 한 게스트 사용자를 회색으로 표시 하지 않으므로 사용자가 관리자 설치를 계속할 수 있습니다. 게스트 사용자를 Azure AD 관리자로 저장 하면 설치가 실패 합니다.
  - 게스트 사용자에 게 MI에 대 한 Azure AD 관리자를 만들려면 Azure AD 그룹에 게스트 사용자를 포함 하 고이 그룹을 Azure AD 관리자로 설정 합니다.

### <a name="powershell-for-sql-managed-instance"></a>SQL 관리 되는 인스턴스의 PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요.

> [!IMPORTANT]
> Azure SQL Database에서 RM (PowerShell Azure Resource Manager) 모듈을 계속 사용할 수 있지만 향후의 모든 개발은 Az. Sql 모듈에 대 한 것입니다. AzureRM 모듈은 12 월 2020 일까 때까지 버그 수정을 계속 받습니다.  Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다. 호환성에 대 한 자세한 내용은 [새 Azure PowerShell Az Module 소개](/powershell/azure/new-azureps-module-az)를 참조 하세요.

Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

- 연결 AzAccount
- Select-AzSubscription

SQL 관리 되는 인스턴스에 대해 Azure AD 관리자를 프로 비전 하 고 관리 하는 데 사용 되는 cmdlet:

| Cmdlet 이름 | Description |
| --- | --- |
| [AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |현재 구독에서 SQL 관리 되는 인스턴스에 대 한 Azure AD 관리자를 프로 비전 합니다. (현재 구독에서 가져와야 함)|
| [AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |현재 구독에서 SQL 관리 되는 인스턴스의 Azure AD 관리자를 제거 합니다. |
| [AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |현재 구독에서 SQL 관리 되는 인스턴스의 Azure AD 관리자에 대 한 정보를 반환 합니다.|

다음 명령은 ResourceGroup01 이라는 리소스 그룹과 연결 된 ManagedInstance01 이라는 관리 되는 인스턴스의 Azure AD 관리자에 대 한 정보를 가져옵니다.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

다음 명령은 ManagedInstance01 라는 관리 되는 인스턴스에 대해 Dba 라는 Azure AD 관리자 그룹을 프로 비전 합니다. 이 서버는 리소스 그룹 ResourceGroup01와 연결 되어 있습니다.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

다음 명령을 사용 하 여 리소스 그룹 ResourceGroup01에 연결 된 ManagedInstanceName01 라는 관리 되는 인스턴스의 Azure AD 관리자를 제거 합니다.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 호출 하 여 SQL 관리 되는 인스턴스에 대 한 Azure AD 관리자를 프로 비전 할 수도 있습니다.

| 명령 | Description |
| --- | --- |
|[az sql mi ad-admin create](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) | SQL 관리 되는 인스턴스에 대 한 Azure Active Directory 관리자를 프로 비전 합니다. (현재 구독에서 가져와야 함) |
|[az sql mi ad-admin delete](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) | SQL 관리 되는 인스턴스의 Azure Active Directory 관리자를 제거 합니다. |
|[az sql mi ad-admin list](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) | 현재 SQL 관리 되는 인스턴스에 대해 구성 된 Azure Active Directory 관리자에 대 한 정보를 반환 합니다. |
|[az sql mi ad-admin update](/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) | SQL 관리 되는 인스턴스의 Active Directory 관리자를 업데이트 합니다. |

CLI 명령에 대 한 자세한 내용은 [az sql mi](/cli/azure/sql/mi)를 참조 하십시오.

* * *

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Azure SQL Database 서버에 대한 Azure Active Directory 관리자를 프로비전합니다

> [!IMPORTANT]
> Azure SQL Database 서버 또는 Data Warehouse를 프로비전하는 경우 다음 단계를 수행합니다.

다음 두 절차는 Azure Portal에서나 PowerShell을 사용하여 Azure SQL Server에 대한 Azure Active Directory 관리자를 프로비전하는 방법을 보여 줍니다.

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com/)의 상단 오른쪽 끝에서 해당 연결을 선택하여 가능한 Active Directory 목록을 드롭다운합니다. 정확한 Active Directory를 기본 Azure AD로 선택합니다. 이 단계는 구독 연결 Active Directory를 Azure SQL Server와 연결하여 동일한 구독이 두 Azure AD 및 SQL Server에 사용되게 합니다. (Azure SQL Server는 Azure SQL Database 또는 Azure SQL Data Warehouse에서 호스트할 수 있습니다.)

    ![choose-ad][8]

2. **SQL server**를 검색 하 고 선택 합니다.

    ![SQL server 검색 및 선택](media/sql-database-aad-authentication/search-for-and-select-sql-servers.png)

    >[!NOTE]
    > 이 페이지에서 **SQL Server**를 선택하기 전에 이름 옆에 있는 **별모양**을 선택하면 범주를 *즐겨 찾기에 추가*하고 왼쪽 탐색 모음에 **SQL Server**를 추가할 수 있습니다.

3. **SQL Server** 페이지에서 **Active Directory 관리자**를 선택합니다.

4. **Active Directory 관리자** 페이지에서 **관리자 설정**을 선택합니다.

    ![Active Directory 관리자를 설정 하는 SQL server](./media/sql-database-aad-authentication/sql-servers-set-active-directory-admin.png)  

5. **관리자 추가** 페이지에서 사용자를 검색하고 관리자가 될 사용자 또는 그룹을 선택한 다음, **선택**을 선택합니다. Active Directory 관리자 페이지에는 Active Directory의 모든 멤버와 그룹이 표시됩니다. 회색으로 표시된 사용자나 그룹은 Azure AD 관리자로 지원되지 않기 때문에 선택할 수 없습니다. [SQL Database 또는 SQL Data Warehouse 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)의 **Azure AD 기능 및 제한 사항** 섹션에서 지원 되는 관리자 목록을 참조 하세요. RBAC (역할 기반 액세스 제어)는 포털에만 적용 되 고 SQL Server로 전파 되지 않습니다.

    ![Azure Active Directory 관리자 선택](./media/sql-database-aad-authentication/select-azure-active-directory-admin.png)  

6. **Active directory 관리자** 페이지 위쪽에서 **저장**을 선택합니다.

    ![관리자 저장](./media/sql-database-aad-authentication/save-admin.png)

관리자 변경 과정에는 몇 분 정도 소요될 수 있습니다. 그런 다음 새 관리자가 **Active Directory 관리자** 상자에 표시됩니다.

   > [!NOTE]
   > Azure AD 관리자를 설정하는 경우, 새 관리자 이름(사용자 또는 그룹)이 SQL Server 인증 사용자로 가상 master 데이터베이스에 있으면 안 됩니다. 새 관리자 이름이 있으면 Azure AD 관리자 설정은 실패하며, 생성 작업을 롤백하고 해당 관리자(이름)이 이미 존재한다는 것을 나타냅니다. SQL Server 인증 사용자는 Azure AD에 속하지 않기 때문에 Azure AD 인증을 사용하여 서버에 연결하려는 작업은 모두 실패합니다.

나중에 관리자를 제거하려면 **Active Directory 관리자** 페이지 위쪽에서 **관리자 제거**를 선택한 다음, **저장**을 선택합니다.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL Database 및 Azure SQL Data Warehouse에 대 한 PowerShell

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell cmdlet을 실행하려면 Azure powershell을 설치하고 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/overview)을 참조하세요. Azure AD 관리자를 프로비전하려면 다음 Azure PowerShell 명령을 실행합니다.

- 연결 AzAccount
- Select-AzSubscription

Azure SQL Database 및 Azure SQL Data Warehouse에 대 한 Azure AD 관리자를 프로 비전 하 고 관리 하는 데 사용 되는 cmdlet:

| Cmdlet 이름 | Description |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독에서 가져와야 함) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 제거합니다. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |현재 Azure SQL Server 또는 Azure SQL Data Warehouse에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |

PowerShell 명령 get-help를 사용 하 여 이러한 각 명령에 대 한 자세한 정보를 확인 합니다. `get-help Set-AzSqlServerActiveDirectoryAdministrator`)을 입력합니다.

다음 스크립트는 리소스 그룹 **Group-23**에서 **demo_server** 서버에 대해 이름이 **DBA_Group**(개체 ID `40b79501-b343-44ed-9ce7-da4c8cc7353f`)인 Azure AD 관리자 그룹을 프로비전합니다.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" -DisplayName "DBA_Group"
```

**DisplayName** 입력 매개 변수에는 Azure AD 표시 이름이나 사용자 계정 이름이 허용됩니다. 예를 들어 ``DisplayName="John Smith"`` 또는 ``DisplayName="johns@contoso.com"``입니다. Azure AD 그룹에는 Azure AD 표시 이름만 지원됩니다.

> [!NOTE]
> Azure PowerShell 명령 ```Set-AzSqlServerActiveDirectoryAdministrator```는 지원되지 않는 사용자에 대한 Azure AD 관리자 프로비전을 차단하지 않습니다. 지원되지 않는 사용자를 프로비전할 수는 있지만 데이터베이스에 연결할 수는 없습니다.

다음 예제에서는 **ObjectID**옵션을 사용합니다.

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" `
    -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> **DisplayName**이 고유하지 않을 경우 Azure AD **ObjectID**가 필수입니다. **ObjectID** 및 **DisplayName** 값을 검색하려면 Azure 클래식 포털의 Active Directory 섹션을 사용하고 사용자 또는 그룹의 속성을 확인합니다.

다음 예제에서는 Azure SQL Server에 대해 현재 Azure AD 관리자 정보를 반환합니다.

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

다음 예제에서는 Azure AD 관리자를 제거합니다.

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 CLI 명령을 호출 하 여 Azure AD 관리자를 프로 비전 할 수 있습니다.

| 명령 | Description |
| --- | --- |
|[az sql server ad-admin create](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) | Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 프로비전합니다. (현재 구독에서 가져와야 함) |
|[az sql server ad-admin delete](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) | Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 제거합니다. |
|[az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) | 현재 Azure SQL Server 또는 Azure SQL Data Warehouse에 대해 구성된 Azure Active Directory 관리자에 대한 정보를 반환합니다. |
|[az sql server ad-admin update](/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) | Azure SQL Server 또는 Azure SQL Data Warehouse에 대한 Azure Active Directory 관리자를 업데이트합니다. |

CLI 명령에 대 한 자세한 내용은 [az sql server](/cli/azure/sql/server)를 참조 하세요.

* * *

> [!NOTE]
> REST API를 사용하여 Azure Active Directory 관리자를 프로비전할 수도 있습니다. 자세한 내용은 [서비스 관리 REST API 참조 및 Azure SQL Database에 대한 작업](/rest/api/sql/)을 참조하세요.

## <a name="configure-your-client-computers"></a>클라이언트 컴퓨터 구성

모든 클라이언트 머신에서 Azure AD를 사용하여 Azure SQL Database 또는 Azure SQL Data Warehouse에 연결하는 애플리케이션 또는 사용자를 통해 다음 소프트웨어를 설치해야 합니다.

- [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx)에서 .NET Framework 4.6 이상
- ADAL (SQL Server 인증 라이브러리를 Azure Active Directory*합니다. DLL*). ADAL을 포함 하는 최신 SSMS, ODBC 및 OLE DB 드라이버를 설치 하는 다운로드 링크는 다음과 같습니다 *. DLL* 라이브러리.
    1. [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
    1. [ODBC Driver 17 for SQL Server](https://www.microsoft.com/download/details.aspx?id=56567)
    1. [SQL Server에 대 한 OLE DB 드라이버 18](https://www.microsoft.com/download/details.aspx?id=56730)

다음을 통해 이러한 요구 사항을 충족할 수 있습니다.

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) 또는 [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) 최신 버전을 설치 하면 .NET Framework 4.6 요구 사항이 충족 됩니다.
    - SSMS는 ADAL의 x86 버전을 설치 합니다 *. DLL*.
    - SSDT은 ADAL의 amd64 버전을 설치 합니다 *. DLL*.
    - [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 의 최신 visual studio는 .NET Framework 4.6 요구 사항을 충족 하지만 ADAL의 필수 amd64 버전은 설치 하지 않습니다 *. DLL*.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Azure AD ID에 매핑된 데이터베이스에서 포함된 데이터베이스 사용자 만들기

> [!IMPORTANT]
> 이제 관리 되는 인스턴스는 azure ad 사용자, 그룹 또는 응용 프로그램에서 로그인을 만들 수 있는 Azure AD 서버 보안 주체 (로그인)를 지원 합니다. Azure AD 서버 보안 주체 (로그인)는 데이터베이스 사용자를 포함 된 데이터베이스 사용자로 만들 필요 없이 관리 되는 인스턴스에 대해 인증 하는 기능을 제공 합니다. 자세한 내용은 [관리 되는 인스턴스 개요](sql-database-managed-instance.md#azure-active-directory-integration)를 참조 하세요. Azure AD 서버 보안 주체(로그인)를 만드는 구문은 <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>을 참조하세요.

Azure Active Directory 인증에는 포함된 데이터베이스 사용자로 만들 데이터베이스 사용자가 필요합니다. Azure AD ID를 기반으로 하는 포함된 데이터베이스 사용자는 마스터 데이터베이스에 로그인이 없는 데이터베이스 사용자이며, 데이터베이스와 연결된 Azure AD 디렉터리의 ID에 매핑됩니다. Azure AD ID는 개별 사용자 계정 또는 그룹일 수 있습니다. 포함된 데이터베이스 사용자에 대한 자세한 내용은 [포함된 데이터베이스 사용자 - 데이터베이스를 이식 가능하게 만들기](https://msdn.microsoft.com/library/ff929188.aspx)를 참조하세요.

> [!NOTE]
> 데이터베이스 사용자(관리자 예외)는 Azure Portal을 사용하여 만들 수 없습니다. RBAC 역할은 SQL Server, SQL Database 또는SQL Data Warehouse에 전파되지 않습니다. Azure RBAC 역할은 Azure 리소스 관리에 사용되며 데이터베이스 사용 권한에는 적용되지 않습니다. 예를 들어 **SQL Server 참여자** 역할은 SQL Database 또는 SQL Data Warehouse에 연결 권한을 부여하지 않습니다. TRANSACT-SQL 문을 사용하여 데이터베이스에 직접 액세스 권한을 부여해야 합니다.

> [!WARNING]
> 콜론(`:`) 또는 앰퍼샌드(`&`) 같은 특수 문자는 T-SQL CREATE LOGIN 및 CREATE USER 문의 사용자 이름에 포함할 수 없습니다.

Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 만들려면 **ALTER ANY USER** 이상의 권한이 있는 사용자인 Azure AD ID를 통해 데이터베이스에 연결합니다. 그런 다음 아래 TRANSACT-SQL 구문을 사용합니다.

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name*은 Azure AD 사용자의 사용자 계정 이름이거나 Azure AD 그룹의 표시 이름일 수 있습니다.

**예:** Azure AD 페더레이션 또는 관리 도메인 사용자를 나타내는 포함된 데이터베이스 사용자를 만드는 방법

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Azure AD 또는 페더레이션 도메인 그룹을 나타내는 포함된 데이터베이스 사용자를 만들려면 보안 그룹의 표시 이름을 제공하십시오.

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Azure AD 토큰을 사용하여 연결할 애플리케이션을 나타내는 포함된 데이터베이스 사용자를 만들려면 다음을 실행합니다.

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> 이 명령을 사용 하려면 로그인 한 사용자를 대신 하 여 SQL에서 Azure AD ("외부 공급자")에 액세스 해야 합니다. 경우에 따라 Azure AD에서 SQL로 예외를 다시 반환 하는 상황이 발생 합니다. 이러한 경우 사용자에 게 AAD 관련 오류 메시지를 포함 하는 SQL 오류 33134이 표시 됩니다. 대부분의 경우 오류는 액세스가 거부 되었거나 사용자가 MFA에 등록 하 여 리소스에 액세스 하거나, preauthorization을 통해 자사 응용 프로그램 간의 액세스를 처리 해야 한다는 것을 말합니다. 처음 두 경우의 문제는 일반적으로 사용자의 AAD 테넌트에 설정 된 조건부 액세스 정책에 의해 발생 합니다. 사용자가 외부 공급자에 액세스 하지 못하도록 합니다. 응용 프로그램 ' 00000002-0000-0000-c000-000000000000 ' (AAD Graph API의 응용 프로그램 ID)에 대 한 액세스를 허용 하도록 CA 정책을 업데이트 하면 문제를 해결 해야 합니다. Preauthorization를 통해 자사 응용 프로그램 간의 액세스를 처리 해야 하는 경우에는 사용자가 서비스 주체로 로그인 했기 때문에 문제가 발생 합니다. 이 명령은 사용자가 대신 실행 하는 경우 성공 해야 합니다.

> [!TIP]
> Azure 구독과 연결된 Azure Active Directory 이외의 Azure Active Directory에서 사용자를 직접 만들 수 없습니다. 그러나 연결된 Active Directory에서 가져온 사용자(외부 사용자로 알려짐)인 다른 Active Directory의 멤버는 테넌트 Active Directory의 Active Directory 그룹에 추가할 수 있습니다. 해당 AD 그룹에 대해 포함된 데이터베이스 사용자를 만들면 외부 Active Directory의 사용자는 SQL Database에 액세스할 수 있습니다.

Azure Active Directory 기반의 포함된 데이터베이스 사용자 만들기와 관련한 자세한 내용은 [CREATE USER(Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx)를 참조하세요.

> [!NOTE]
> Azure SQL Server에 대한 Azure Active Directory 관리자를 제거하면 Azure AD 인증 사용자가 서버에 연결되지 않도록 할 수 있습니다. 필요한 경우 사용할 수 없는 Azure AD 사용자는 SQL Database 관리자가 수동으로 삭제할 수 있습니다.

> [!NOTE]
> **연결 시간 초과 만료됨**을 수신하면 연결 문자열의 `TransparentNetworkIPResolution` 매개 변수를 false로 설정하지 않아도 됩니다. 자세한 내용은 [.NET Framework 4.6.1의 연결 시간 초과 문제 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/)을 참조하세요.

데이터베이스 사용자를 만들 때 해당 사용자는 **연결** 권한을 부여 받으며 **공용** 역할의 멤버로서 해당 데이터베이스에 연결할 수 있습니다. 처음에 이 사용자에게 제공되는 권한은 **공용** 역할에 부여된 권한이거나 사용자가 속해 있는 Azure AD 그룹에 부여된 권한뿐입니다. Azure AD 기반의 포함된 데이터베이스 사용자를 프로비전한 후에는 다른 사용자 유형에 권한을 부여하는 것과 같은 방식으로 이 사용자에게 추가 권한을 부여할 수 있습니다. 일반적으로 데이터베이스 역할에 권한을 부여하고 역할에 사용자를 추가합니다. 자세한 내용은 [데이터베이스 엔진의 권한 기초](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx)를 참조하세요. 특수 SQL Database 역할에 대한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](sql-database-manage-logins.md)를 참조하세요.
관리되는 도메인에 외부 사용자로 가져온 페더레이션된 도메인 사용자 계정은 관리되는 도메인 ID를 사용해야 합니다.

> [!NOTE]
> Azure AD 사용자는 데이터베이스 메타데이터에서 E 형식(EXTERNAL_USER) 및 그룹의 경우 X 형식(EXTERNAL_GROUPS)으로 표시됩니다. 자세한 내용은 [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)를 참조하세요.

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 사용자 데이터베이스 또는 데이터 웨어하우스에 연결  

Azure AD 관리자가 제대로 설정되었는지 확인하려면 Azure AD 관리자 계정을 사용하여 **master** 데이터베이스에 연결합니다.
Azure AD 기반의 포함된 데이터베이스 사용자(데이터베이스를 소유한 서버 관리자 아님)를 프로비전하려면 해당 데이터베이스에 대한 액세스가 있는 Azure AD ID로 데이터베이스에 연결합니다.

> [!IMPORTANT]
> Azure Active Directory 인증에 대한 지원은 [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) 및 Visual Studio 2015의 [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx)에서 사용 가능합니다. SSMS의 2016년 8월 릴리스에는 관리자가 전화 통화, 문자 메시지, PIN이 있는 스마트 카드 또는 모바일 앱 알림을 사용하여 Multi-Factor Authentication을 요구할 수 있도록 하는 Active Directory 유니버설 인증도 포함되어 있습니다.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>SSMS 또는 SSDT를 사용하여 연결하는 데 Azure AD ID 사용

다음 절차에서는 SQL Server Management Studio 또는 SQL Server Database Tools를 사용하여 SQL Database를 Azure AD ID에 연결하는 방법을 보여 줍니다.

### <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

페더레이션된 도메인의 Azure Active Directory 자격 증명을 사용하여 Windows에 로그인한 경우 이 방법을 사용합니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진 연결**) 대화 상자의 **인증** 상자에서 **Active Directory 통합**을 선택합니다. 연결에 대한 기존 자격 증명이 있으므로 암호 입력이 필요하지 않습니다.

    ![AD 통합 인증 선택][11]

2. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. **AD 도메인 이름 또는 테넌트 ID** 옵션은 **MFA 연결 옵션이 있는 유니버설**에서만 지원되며 그 밖의 경우는 회색으로 표시됩니다.  

    ![데이터베이스 이름 선택][13]

## <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

Azure AD 관리 도메인을 사용하여 Azure AD 사용자 이름과 연결할 때 이 방법을 사용합니다. 원격 작업 등, 도메인 액세스 없이 페더레이션된 계정에도 이 방법을 사용할 수 있습니다.

이 방법을 사용하면 네이티브 또는 페더레이션된 Azure AD 사용자에 대해 Azure AD로 SQL DB/DW에 인증할 수 있습니다. 기본 사용자는 Azure AD에서 명시적으로 만들어지고 사용자 이름 및 암호를 사용하여 인증되지만, 페더레이션된 사용자는 도메인이 Azure AD와 함께 페더레이션된 Windows 사용자입니다. 후자의 방법(사용자 및 암호 사용)은 사용자가 자신의 Windows 자격 증명을 사용하려고 하지만 자신의 로컬 머신이 도메인에 가입되어 있지 않은 경우(예: 원격 액세스 사용) 사용할 수 있습니다. 이 경우 Windows 사용자는 자신의 도메인 계정과 암호를 표시할 수 있으며 페더레이션된 자격 증명을 사용하여 SQL DB/DW에 대해 인증할 수 있습니다.

1. Management Studio 또는 Data Tools를 시작하고, **서버에 연결**(또는 **데이터베이스 엔진 연결**) 대화 상자의 **인증** 상자에서 **Active Directory - 암호**를 선택합니다.

2. **사용자 이름** 상자에 Azure Active Directory 사용자 이름을 **username\@domain.com** 형식으로 입력합니다. 사용자 이름은 Azure Active Directory의 계정이거나, Azure Active Directory와 페더레이션된 도메인의 계정이어야 합니다.

3. **암호** 상자에 Azure Active Directory 계정이나 페더레이션된 도메인 계정의 사용자 암호를 입력합니다.

    ![AD 암호 인증 선택][12]

4. **옵션** 단추를 선택하고 **연결 속성** 페이지의 **데이터베이스에 연결** 상자에서 연결하려는 사용자 데이터베이스의 이름을 입력합니다. (이전 옵션의 그래픽을 참조하세요.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>클라이언트 애플리케이션에서 연결하는 데 Azure AD ID 사용

다음 절차에서는 클라이언트 애플리케이션에서 SQL Database를 Azure AD ID에 연결하는 방법을 보여 줍니다.

### <a name="active-directory-integrated-authentication"></a>Active Directory 통합 인증

Windows 통합 인증을 사용하려면 도메인의 Active Directory를 Azure Active Directory와 페더레이션해야 합니다. 데이터베이스에 연결되는 클라이언트 애플리케이션(또는 서비스)은 사용자의 도메인 자격 증명으로 도메인에 가입된 컴퓨터에서 실행되어야 합니다.

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 데이터베이스 연결 문자열의 인증 키워드가 Active Directory 통합으로 설정되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```csharp
string ConnectionString = @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

연결 문자열 키워드 `Integrated Security=True`는 Azure SQL Database 연결에 지원되지 않습니다. ODBC 연결을 설정할 때는 공백을 제거하고 인증을 'ActiveDirectoryIntegrated'로 설정해야 합니다.

### <a name="active-directory-password-authentication"></a>Active Directory 암호 인증

통합 인증 및 Azure AD ID를 사용하여 데이터베이스에 연결하려면 인증 키워드가 Active Directory 암호로 설정되어 있어야 합니다. 연결 문자열에는 사용자 ID/UID 및 암호/PWD 키워드와 값이 포함되어 있어야 합니다. 다음 C# 코드 예제에서는 ADO.NET을 사용합니다.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

[Azure AD 인증 GitHub 데모](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth)에서 사용할 수 있는 데모 코드 샘플을 사용한 Azure AD 인증 방법에 대해 자세히 알아보세요.

## <a name="azure-ad-token"></a>Azure AD 토큰

이 인증 방법을 사용하면 AAD(Azure Active Directory)에서 토큰을 가져와 Azure SQL Database 또는 Azure SQL Data Warehouse에 연결할 수 있습니다. 이는 인증서 기반 인증을 비롯한 정교한 시나리오를 지원합니다. Azure AD 토큰 인증을 사용하려면 다음 네 가지 기본 단계를 완료해야 합니다.

1. Azure Active Directory를 사용 하 여 응용 프로그램을 등록 하 고 코드에 대 한 클라이언트 ID를 가져옵니다.
2. 애플리케이션을 나타내는 데이터베이스 사용자를 만듭니다(이전 6단계에서 완료).
3. 애플리케이션을 실행하는 클라이언트 컴퓨터에서 인증서를 만듭니다.
4. 인증서를 애플리케이션의 키로 추가합니다.

샘플 연결 문자열:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

자세한 내용은 [SQL Server 보안 블로그](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/)를 참조하세요. 인증서 추가에 대한 정보는 [Azure Active Directory에서 인증서 기반 인증 시작](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)을 참조하세요.

### <a name="sqlcmd"></a>sqlcmd

다음 문은 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=53591)에서 사용할 수 있는 sqlcmd 버전 13.1을 사용하여 연결합니다.

> [!NOTE]
> `-G` 명령이 있는 `sqlcmd`는 시스템 id에서 작동 하지 않으며 사용자 계정 로그인이 필요 합니다.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="troubleshooting-azure-ad-authentication"></a>문제 해결 Azure AD 인증

Azure AD 인증 문제를 해결 하는 방법에 대 한 지침은 다음 블로그를 참조 하세요. <https://techcommunity.microsoft.com/t5/azure-sql-database/troubleshooting-problems-related-to-azure-ad-authentication-with/ba-p/1062991>

## <a name="next-steps"></a>다음 단계

- SQL Database의 액세스 및 제어에 대한 개요는 [SQL Database 액세스 및 제어](sql-database-control-access.md)를 참조하세요.
- SQL Database의 로그인, 사용자 및 데이터베이스 역할에 대한 개요는 [로그인, 사용자 및 데이터베이스 역할](sql-database-manage-logins.md)을 참조하세요.
- 데이터베이스 보안 주체에 대한 자세한 내용은 [보안 주체](https://msdn.microsoft.com/library/ms181127.aspx)를 참조하세요.
- 데이터베이스 역할에 대한 자세한 내용은 [데이터베이스 역할](https://msdn.microsoft.com/library/ms189121.aspx)을 참조하세요.
- SQL Database의 방화벽 규칙에 대한 자세한 내용은 [SQL Database 방화벽 규칙](sql-database-firewall-configure.md)을 참조하세요.

<!--Image references-->
[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
