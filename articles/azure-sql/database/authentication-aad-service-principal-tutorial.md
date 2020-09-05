---
title: 서비스 주체를 사용하여 Azure AD 사용자 만들기
description: 이 자습서에서는 Azure SQL Database 및 Azure Synapse Analytics에서 Azure AD 애플리케이션(서비스 주체)을 사용하여 Azure AD 사용자를 만드는 과정을 안내합니다.
ms.service: sql-database
ms.subservice: security
ms.custom: azure-synapse
ms.topic: tutorial
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/17/2020
ms.openlocfilehash: 61cb5384fd4d935ef4038c18b391b5da5fbc96b1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88516693"
---
# <a name="tutorial-create-azure-ad-users-using-azure-ad-applications"></a>자습서: Azure AD 애플리케이션을 사용하여 Azure AD 사용자 만들기

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

> [!NOTE]
> 이 문서는 **공개 미리 보기**에 있습니다. 자세한 내용은 [Azure SQL을 사용하는 Azure Active Directory 서비스 주체](authentication-aad-service-principal.md)를 참조하세요. 이 문서에서는 Azure SQL Database를 사용하여 필요한 자습서 단계를 설명하지만, [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)에도 비슷하게 적용할 수 있습니다.

이 문서는 Azure 서비스 주체(Azure AD 애플리케이션)를 사용하여 Azure SQL Database에서 Azure AD 사용자를 만드는 과정을 안내합니다. 이 기능은 Azure SQL Managed Instance에 이미 있지만 이제 Azure SQL Database 및 Azure Synapse Analytics에 도입되었습니다. 이 시나리오를 지원하려면 Azure AD ID를 생성하여 Azure SQL 논리 서버에 할당해야 합니다.

Azure SQL의 Azure AD 인증에 대한 자세한 내용은 [Azure Active Directory 인증 사용](authentication-aad-overview.md) 문서를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - Azure SQL 논리 서버에 ID 할당
> - SQL 논리 서버 ID에 디렉터리 읽기 권한자 권한 할당
> - Azure AD에서 서비스 주체(Azure AD 애플리케이션) 만들기
> - Azure SQL Database에서 서비스 주체 사용자 만들기
> - Azure AD 서비스 주체 사용자를 사용하여 SQL Database에서 다른 Azure AD 사용자 만들기

## <a name="prerequisites"></a>필수 구성 요소

- 기존 [Azure SQL Database](single-database-create-quickstart.md) 또는 [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 배포. 이 자습서에서는 작업 SQL Database가 있다고 가정합니다.
- 이미 있는 Azure Active Directory에 대한 액세스
- [Az.Sql 2.9.0](https://www.powershellgallery.com/packages/Az.Sql/2.9.0) 모듈 이상(PowerShell을 사용하여 개별 Azure AD 애플리케이션을 Azure SQL에 대한 Azure AD 관리자로 설정할 때 필요함). 최신 모듈로 업그레이드해야 합니다.

## <a name="assign-an-identity-to-the-azure-sql-logical-server"></a>Azure SQL 논리 서버에 ID 할당

1. Azure Active Directory에 연결합니다. 테넌트 ID를 찾아야 합니다. 이는 [Azure Portal](https://portal.azure.com), **Azure Active Directory** 리소스로 차례로 이동하여 찾을 수 있습니다. **개요** 창에**테넌트 ID**가 표시됩니다. 다음 PowerShell 명령을 실행합니다.

    - `<TenantId>`를 **테넌트 ID**로 바꿉니다.

    ```powershell
    Connect-AzAccount -Tenant <TenantId>
    ```

    나중에 이 자습서에서 사용하기 위해 `TenantId`를 기록합니다.

1. Azure AD ID를 생성하여 Azure SQL 논리 서버에 할당합니다. 다음 PowerShell 명령을 실행합니다.

    - `<resource group>` 및 `<server name>`을 리소스로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<server name>`을 `myserver`로 바꿉니다.

    ```powershell
    Set-AzSqlServer -ResourceGroupName <resource group> -ServerName <server name> -AssignIdentity
    ```

    자세한 내용은 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 명령을 참조하세요.

    > [!IMPORTANT]
    > Azure SQL 논리 서버에 대해 Azure AD ID가 설정된 경우 [**디렉터리 읽기 권한자**](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) 권한을 해당 ID에 부여해야 합니다. 이 단계는 다음 섹션에서 안내합니다. Azure AD 인증의 작동이 중지되므로 이 단계를 건너뛰지 **마세요**.

    - 이전에 `AssignIdentity` 매개 변수와 함께 [New-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlserver) 명령을 사용하여 새 SQL 서버를 만든 경우 나중에 [Set-AzSqlServer](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserver) 명령을 별도의 명령으로 실행하여 Azure 패브릭에서 이 속성을 사용하도록 설정해야 합니다.

1. 서버 ID가 성공적으로 할당되었는지 확인합니다. 다음 PowerShell 명령을 실행합니다.

    - `<resource group>` 및 `<server name>`을 리소스로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<server name>`을 `myserver`로 바꿉니다.
    
    ```powershell
    $xyz = Get-AzSqlServer  -ResourceGroupName <resource group> -ServerName <server name>
    $xyz.identity
    ```

    출력에 `PrincipalId`, `Type` 및 `TenantId`가 표시됩니다. 할당된 ID는 `PrincipalId`입니다.

1. 이 ID는 [Azure Portal](https://portal.azure.com)로 이동하여 확인할 수도 있습니다.

    - **Azure Active Directory** 리소스 아래에서 **엔터프라이즈 애플리케이션**으로 이동합니다. SQL 논리 서버의 이름을 입력합니다. **개체 ID**가 리소스에 연결되어 있음을 알 수 있습니다.
    
    :::image type="content" source="media/authentication-aad-service-principals-tutorial/enterprise-applications-object-id.png" alt-text="object-id":::

## <a name="assign-directory-readers-permission-to-the-sql-logical-server-identity"></a>SQL 논리 서버 ID에 디렉터리 읽기 권한자 권한 할당

Azure AD에서 할당한 ID가 Azure SQL에 대해 제대로 작동할 수 있도록 하려면 Azure AD `Directory Readers` 권한을 서버 ID에 부여해야 합니다.

이 필수 권한을 부여하려면 다음 스크립트를 실행합니다.

> [!NOTE] 
> 이 스크립트는 Azure AD `Global Administrator` 또는 `Privileged Roles Administrator` 권한으로 실행해야 합니다.
>
> **공개 미리 보기**에서 Azure AD의 그룹에 `Directory Readers` 역할을 할당할 수 있습니다. 그런 다음, 그룹 소유자는 관리 ID를 이 그룹의 멤버로 추가할 수 있습니다. 그러면 `Global Administrator` 또는 `Privileged Roles Administrator`에서 `Directory Readers` 역할을 부여할 필요가 없습니다. 이 기능에 대한 자세한 내용은 [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)을 참조하세요.

- `<TenantId>`를 이전에 수집한 `TenantId`로 바꿉니다.
- `<server name>`을 SQL 논리 서버 이름으로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<server name>`을 `myserver`로 바꿉니다.

```powershell
# This script grants Azure “Directory Readers” permission to a Service Principal representing the Azure SQL logical server
# It can be executed only by a "Global Administrator" or “Privileged Roles Administrator” type of user.
# To check if the “Directory Readers" permission was granted, execute this script again

Import-Module AzureAd
connect-azuread -TenantId "<TenantId>"     #Enter your actual TenantId
 $AssignIdentityName = "<server name>"     #Enter Azure SQL logical server name
 
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
$roleMember = Get-AzureADServicePrincipal -SearchString $AssignIdentityName
$roleMember.Count
if ($roleMember -eq $null)
{
    Write-Output "Error: No Service Principals with name '$    ($AssignIdentityName)', make sure that AssignIdentityName parameter was     entered correctly."
    exit
}
if (-not ($roleMember.Count -eq 1))
{
    Write-Output "Error: More than one service principal with name pattern '$    ($AssignIdentityName)'"
    Write-Output "Dumping selected service principals...."
    $roleMember
    exit
}
 
# Check if service principal is already member of readers role
$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
$selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}
 
if ($selDirReader -eq $null)
{
    # Add principal to readers role
    Write-Output "Adding service principal '$($msName)' to     'Directory Readers' role'..."
    Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
    Write-Output "'$($AssignIdentityName)' service principal added to     'Directory Readers' role'..."
 
    #Write-Output "Dumping service principal '$($AssignIdentityName)':"
    #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
}
else
{
    Write-Output "Service principal '$($AssignIdentityName)' is already member of 'Directory Readers' role'."
}
```

> [!NOTE]
> 위 스크립트의 출력은 디렉터리 읽기 권한자 권한이 ID에 부여되었는지 여부를 나타냅니다. 권한이 부여되었는지 확실하지 않으면 스크립트를 다시 실행할 수 있습니다.

SQL Managed Instance에 대한 **디렉터리 읽기 권한자** 권한을 설정하는 방법에 대한 비슷한 방법은 [Azure AD 관리자(SQL Managed Instance) 프로비저닝](authentication-aad-configure.md#powershell)을 참조하세요.

## <a name="create-a-service-principal-an-azure-ad-application-in-azure-ad"></a>Azure AD에서 서비스 주체(Azure AD 애플리케이션) 만들기

1. [앱 등록 및 권한 설정](active-directory-interactive-connect-azure-sql-db.md#register-your-app-and-set-permissions)의 지침을 따릅니다.

    **애플리케이션 권한** 및 **위임된 권한**을 추가해야 합니다.

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-apps.png" alt-text="aad-apps":::

    :::image type="content" source="media/authentication-aad-service-principals-tutorial/aad-app-registration-api-permissions.png" alt-text="api-permissions":::

2. 또한 로그인을 위해 클라이언트 암호를 만들어야 합니다. [로그인을 위한 인증서 업로드 또는 비밀 만들기](../../active-directory/develop/howto-create-service-principal-portal.md#upload-a-certificate-or-create-a-secret-for-signing-in)의 지침을 따릅니다.

3. 애플리케이션 등록에서 다음을 기록합니다. **개요** 창에서 사용할 수 있어야 합니다.
    - **애플리케이션 ID**
    - **테넌트 ID** - 이전과 동일해야 합니다.

이 자습서에서는 *AppSP*를 기본 서비스 주체로 사용하고, *myapp*을 Azure SQL에서 *AppSP*를 통해 만들 두 번째 서비스 주체 사용자로 사용합니다. *AppSP* 및 *myapp*의 두 애플리케이션을 만들어야 합니다.

Azure AD 애플리케이션을 만드는 방법에 대한 자세한 내용은 [방법: 포털을 사용하여 리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md) 문서를 참조하세요.


## <a name="create-the-service-principal-user-in-azure-sql-database"></a>Azure SQL Database에서 서비스 주체 사용자 만들기

Azure AD에서 서비스 주체가 만들어지면 SQL Database에서 사용자를 만듭니다. 데이터베이스에서 사용자를 만들 수 있는 권한이 있는 유효한 로그인을 사용하여 SQL Database에 연결해야 합니다.

1. 다음 T-SQL 명령을 사용하여 SQL Database에서 *AppSP* 사용자를 만듭니다.

    ```sql
    CREATE USER [AppSP] FROM EXTERNAL PROVIDER
    GO
    ```

2. 사용자가 데이터베이스에서 다른 Azure AD 사용자를 만들 수 있도록 하는 `db_owner` 권한을 *AppSP*에 부여합니다.

    ```sql
    EXEC sp_addrolemember 'db_owner', [AppSP]
    GO
    ```

    자세한 내용은 [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)를 참조하세요.

    또는 `db_owner` 역할을 부여하는 대신 `ALTER ANY USER` 권한을 부여할 수 있습니다. 이렇게 하면 서비스 주체가 다른 Azure AD 사용자를 추가할 수 있습니다.

    ```sql
    GRANT ALTER ANY USER TO [AppSp]
    GO
    ```

    > [!NOTE]
    > *AppSP*가 서버에 대한 Azure AD 관리자로 설정된 경우 위의 설정이 필요하지 않습니다. 서비스 주체를 SQL 논리 서버에 대한 AD 관리자로 설정하려면 Azure Portal, PowerShell 또는 Azure CLI 명령을 사용하면 됩니다. 자세한 내용은 [Azure AD 관리자(SQL Database) 프로비저닝](authentication-aad-configure.md?tabs=azure-powershell#powershell-for-sql-database-and-azure-synapse)을 참조하세요.

## <a name="create-an-azure-ad-user-in-sql-database-using-an-azure-ad-service-principal"></a>Azure AD 서비스 주체를 사용하여 SQL Database에서 Azure AD 사용자 만들기

> [!IMPORTANT]
> SQL Database에 로그인하는 데 사용되는 서비스 주체에는 클라이언트 암호가 있어야 합니다. 없는 경우 [Azure AD에서 서비스 주체(Azure AD 애플리케이션) 만들기](#create-a-service-principal-an-azure-ad-application-in-azure-ad)의 2단계를 따릅니다. 이 클라이언트 암호는 아래 스크립트에서 입력 매개 변수로 추가해야 합니다.

1. 다음 스크립트를 사용하여 *AppSP* 서비스 주체 사용자를 사용하는 *myapp* Azure AD 서비스 주체 사용자를 만듭니다.

    - `<TenantId>`를 이전에 수집한 `TenantId`로 바꿉니다.
    - `<ClientId>`를 이전에 수집한 `ClientId`로 바꿉니다.
    - `<ClientSecret>`을 이전에 만든 클라이언트 암호로 바꿉니다.
    - `<server name>`을 SQL 논리 서버 이름으로 바꿉니다. 서버 이름이 `myserver.database.windows.net`인 경우 `<server name>`을 `myserver`로 바꿉니다.
    - `<database name>`을 SQL Database 이름으로 바꿉니다.

    ```powershell
    # PowerShell script for creating a new SQL user called myapp using application AppSP with secret

    $tenantId = "<TenantId>"   #  tenantID (Azure Directory ID) were AppSP resides
    $clientId = "<ClientId>"   #  AppID also ClientID for AppSP     
    $clientSecret = "<ClientSecret>"   #  client secret for AppSP 
    $Resource = "https://database.windows.net/"
    
    $adalPath  = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\5.8.3"
    # To install the latest AzureRM.profile version execute  -Install-Module -Name AzureRM.profile
    $adal      = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "$adalPath\Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll"
    [System.Reflection.Assembly]::LoadFrom($adal) | Out-Null
      $resourceAppIdURI = 'https://database.windows.net/'

      # Set Authority to Azure AD Tenant
      $authority = 'https://login.windows.net/' + $tenantId

      $ClientCred = [Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential]::new($clientId, $clientSecret)
      $authContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]::new($authority)
      $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI,$ClientCred)
      $Tok = $authResult.Result.CreateAuthorizationHeader()
      $Tok=$Tok.Replace("Bearer ","")
      Write-host "token"
      $Tok
      Write-host  " "

    $SQLServerName = "<server name>"    # Azure SQL logical server name 
    Write-Host "Create SQL connectionstring"
    $conn = New-Object System.Data.SqlClient.SQLConnection 
    $DatabaseName = "<database name>"     # Azure SQL database name
    $conn.ConnectionString = "Data Source=$SQLServerName.database.windows.net;Initial Catalog=$DatabaseName;Connect Timeout=30"
    $conn.AccessToken = $Tok
    
    Write-host "Connect to database and execute SQL script"
    $conn.Open() 
    $ddlstmt = 'CREATE USER [myapp] FROM EXTERNAL PROVIDER;'
    Write-host " "
    Write-host "SQL DDL command"
    $ddlstmt
    $command = New-Object -TypeName System.Data.SqlClient.SqlCommand($ddlstmt, $conn)       
    
    Write-host "results"
    $command.ExecuteNonQuery()
    $conn.Close()  
    ``` 

    또는 [SQL DB에 대한 Azure AD 서비스 주체 인증 - 코드 샘플](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467) 블로그의 코드 샘플을 사용할 수 있습니다. `CREATE USER [myapp] FROM EXTERNAL PROVIDER` DDL 문을 실행하도록 스크립트를 수정합니다. 동일한 스크립트를 사용하여 SQL Database에서 일반 Azure AD 사용자 그룹을 만들 수 있습니다.

    > [!NOTE]
    > AzureRM.profile 모듈을 설치해야 하는 경우 관리자 권한으로 PowerShell을 열어야 합니다. 다음 명령을 사용하여 최신 AzureRM.profile 버전을 자동으로 설치하고 위 스크립트에 대해 `$adalpath`를 설정할 수 있습니다.
    > 
    > ```powershell
    > Install-Module AzureRM.profile -force
    > Import-Module AzureRM.profile
    > $version = (Get-Module -Name AzureRM.profile).Version.toString()
    > $adalPath = "${env:ProgramFiles}\WindowsPowerShell\Modules\AzureRM.profile\${version}"
    > ```
    
2. 다음 명령을 실행하여 *myapp* 사용자가 데이터베이스에 있는지 확인합니다.

    ```sql
    SELECT name, type, type_desc, CAST(CAST(sid as varbinary(16)) as uniqueidentifier) as appId from sys.database_principals WHERE name = 'myapp'
    GO
    ```

    다음과 비슷한 출력이 표시됩니다.

    ```output
    name    type    type_desc   appId
    myapp   E   EXTERNAL_USER   6d228f48-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

## <a name="next-steps"></a>다음 단계

- [Azure SQL을 사용하는 Azure Active Directory 서비스 주체](authentication-aad-service-principal.md)
- [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)
- [App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../../app-service/overview-managed-identity.md)
- [SQL DB에 대한 Azure AD 서비스 주체 인증 - 코드 샘플](https://techcommunity.microsoft.com/t5/azure-sql-database/azure-ad-service-principal-authentication-to-sql-db-code-sample/ba-p/481467)
- [Azure Active Directory의 애플리케이션 및 서비스 주체 개체](../../active-directory/develop/app-objects-and-service-principals.md)
- [Azure PowerShell을 사용하여 Azure 서비스 주체 만들기](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
- [Azure SQL용 Azure Active Directory의 Directory Readers 역할](authentication-aad-directory-readers-role.md)