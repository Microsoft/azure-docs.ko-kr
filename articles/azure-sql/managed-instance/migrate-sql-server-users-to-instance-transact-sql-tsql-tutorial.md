---
title: T-SQL을 사용하여 SQL Server Windows 사용자 및 그룹을 SQL Managed Instance로 마이그레이션
description: SQL Server 인스턴스의 Windows 사용자 및 그룹을 Azure SQL Managed Instance로 마이그레이션하는 방법 알아보기
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: tutorial
author: GitHubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 10/30/2019
ms.openlocfilehash: f2dd34ab7c6ee5be26836e4abb86960605ee44ee
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84708680"
---
# <a name="tutorial-migrate-windows-users-and-groups-in-a-sql-server-instance-to-azure-sql-managed-instance-using-t-sql-ddl-syntax"></a>자습서: T-SQL DDL 구문을 사용하여 SQL Server 인스턴스의 Windows 사용자 및 그룹을 Azure SQL Managed Instance로 마이그레이션
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

> [!NOTE]
> 이 문서에서 사용자와 그룹을 SQL Managed Instance로 마이그레이션하는 데 사용되는 구문은 **공개 미리 보기** 상태입니다.

이 문서에서는 T-SQL 구문을 사용하여 SQL Server의 온-프레미스 Windows 사용자 및 그룹을 Azure SQL Managed Instance로 마이그레이션하는 프로세스를 안내합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - SQL Serve에 대한 로그인 만들기
> - 마이그레이션용 테스트 데이터베이스 만들기
> - 로그인, 사용자 및 역할 만들기
> - 데이터베이스를 SQL MI(Managed Instance)로 백업 및 복원
> - ALTER USER 구문을 사용하여 사용자를 MI로 수동 마이그레이션
> - 매핑된 새 사용자를 사용한 인증 테스트

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 조건이 적용됩니다.

- Windows 도메인은 Azure AD(Azure Active Directory)와 페더레이션됩니다.
- Active Directory에 액세스하여 사용자/그룹을 만듭니다.
- 온-프레미스 환경의 기존 SQL 서버
- 기존 SQL Managed Instance. [빠른 시작: SQL Managed Instance 만들기](instance-create-quickstart.md)를 참조하세요.
  - SQL Managed Instance의 `sysadmin`을 사용하여 Azure AD 로그인을 만들어야 합니다.
- [SQL Managed Instance에 대한 Azure AD 관리자를 만듭니다](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).
- 네트워크 내에서 SQL Managed Instance에 연결할 수 있습니다. 추가 정보는 다음 문서를 참조하세요.
  - [애플리케이션을 Azure SQL Managed Instance에 연결](connect-application-instance.md)
  - [빠른 시작: 온-프레미스에서 Azure SQL Managed Instance로의 지점 및 사이트 간 연결 구성](point-to-site-p2s-configure.md)
  - [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](public-endpoint-configure.md)

## <a name="t-sql-ddl-syntax"></a>T-SQL DDL 구문

Azure AD 인증을 사용하여 SQL Server 인스턴스의 Windows 사용자 및 그룹을 SQL Managed Instance로 마이그레이션하는 데 사용되는 T-SQL DDL 구문은 다음과 같습니다.

```sql
-- For individual Windows users with logins
ALTER USER [domainName\userName] WITH LOGIN = [loginName@domainName.com];

--For individual groups with logins
ALTER USER [domainName\groupName] WITH LOGIN=[groupName]
```

## <a name="arguments"></a>인수

_domainName_</br>
사용자의 도메인 이름을 지정합니다.

_userName_</br>
데이터베이스 내에서 식별된 사용자의 이름을 지정합니다.

_= loginName\@domainName.com_</br>
사용자를 Azure AD 로그인에 다시 매핑합니다.

_groupName_</br>
데이터베이스 내에서 식별된 그룹의 이름을 지정합니다.

## <a name="part-1-create-logins-in-sql-server-for-windows-users-and-groups"></a>1부: SQL Server에 Windows 사용자 및 그룹의 로그인 만들기

> [!IMPORTANT]
> 다음 구문은 SQL Server에서 사용자 및 그룹 로그인을 만듭니다. 아래 구문을 실행하기 전에 사용자와 그룹이 AD(Active Directory) 내에 있는지 확인해야 합니다. </br> </br>
> 사용자: testUser1, testGroupUser </br>
> 그룹: migration - testGroupUser는 AD의 migration 그룹에 속해야 합니다.

아래 예제에서는 _aadsqlmi_ 도메인에 속한 _testUser1_이라는 계정에 대한 로그인을 SQL Server에 만듭니다.

```sql
-- Sign into SQL Server as a sysadmin or a user that can create logins and databases

use master;  
go

-- Create Windows login
create login [aadsqlmi\testUser1] from windows;
go;

/** Create a Windows group login which contains one user [aadsqlmi\testGroupUser].
testGroupUser will need to be added to the migration group in Active Directory
**/
create login [aadsqlmi\migration] from windows;
go;


-- Check logins were created
select * from sys.server_principals;
go;
```

이 테스트에 사용할 데이터베이스를 만듭니다.

```sql
-- Create a database called [migration]
create database migration
go
```

## <a name="part-2-create-windows-users-and-groups-then-add-roles-and-permissions"></a>2부: Windows 사용자 및 그룹을 만든 다음, 역할 및 권한 추가

다음 구문을 사용하여 테스트 사용자를 만듭니다.

```sql
use migration;  
go

-- Create Windows user [aadsqlmi\testUser1] with login
create user [aadsqlmi\testUser1] from login [aadsqlmi\testUser1];
go
```

사용자 권한을 확인합니다.

```sql
-- Check the user in the Metadata
select * from sys.database_principals;
go

-- Display the permissions – should only have CONNECT permissions
select user_name(grantee_principal_id), * from sys.database_permissions;
go
```

역할을 만들고, 테스트 사용자를 이 역할에 할당합니다.

```sql
-- Create a role with some permissions and assign the user to the role
create role UserMigrationRole;
go

grant CONNECT, SELECT, View DATABASE STATE, VIEW DEFINITION to UserMigrationRole;
go

alter role UserMigrationRole add member [aadsqlmi\testUser1];
go
```

다음 쿼리를 사용하여 특정 역할에 할당된 사용자 이름을 표시합니다.

```sql
-- Display user name assigned to a specific role
SELECT DP1.name AS DatabaseRoleName,
   isnull (DP2.name, 'No members') AS DatabaseUserName
 FROM sys.database_role_members AS DRM
 RIGHT OUTER JOIN sys.database_principals AS DP1
   ON DRM.role_principal_id = DP1.principal_id
 LEFT OUTER JOIN sys.database_principals AS DP2
   ON DRM.member_principal_id = DP2.principal_id
WHERE DP1.type = 'R'
ORDER BY DP1.name;
```

다음 구문을 사용하여 그룹을 만듭니다. 그런 다음, 이 그룹을 `db_owner` 역할에 추가합니다.

```sql
-- Create Windows group
create user [aadsqlmi\migration] from login [aadsqlmi\migration];
go

-- ADD 'db_owner' role to this group
sp_addrolemember 'db_owner', 'aadsqlmi\migration';
go

--Check the db_owner role for 'aadsqlmi\migration' group
select is_rolemember('db_owner', 'aadsqlmi\migration')
go
-- Output  ( 1 means YES)
```

다음 구문을 사용하여 테스트 테이블을 만들고 일부 데이터를 추가합니다.

```sql
-- Create a table and add data
create table test ( a int, b int);
go

insert into test values (1,10)
go

-- Check the table values
select * from test;
go
```

## <a name="part-3-backup-and-restore-the-individual-user-database-to-sql-managed-instance"></a>3부: 개별 사용자 데이터베이스를 SQL Managed Instance로 백업 및 복원

[백업 및 복원을 사용하여 데이터베이스 복사](/sql/relational-databases/databases/copy-databases-with-backup-and-restore) 문서를 사용하여 마이그레이션 데이터베이스의 백업을 만들거나 다음 구문을 사용합니다.

```sql
use master;
go
backup database migration to disk = 'C:\Migration\migration.bak';
go
```

[빠른 시작: 데이터베이스를 SQL Managed Instance로 복원](restore-sample-database-quickstart.md)을 참조하세요.

## <a name="part-4-migrate-users-to-sql-managed-instance"></a>4부. 사용자를 SQL Managed Instance로 마이그레이션

ALTER USER 명령을 실행하여 SQL Managed Instance에서 마이그레이션 프로세스를 완료합니다.

1. SQL Managed Instance의 Azure AD 관리자 계정을 사용하여 SQL Managed Instance에 로그인합니다. 그런 다음, 다음 구문을 사용하여 SQL Managed Instance에 Azure AD 로그인을 만듭니다. 자세한 내용은 [자습서: Azure SQL Database에서 Azure AD 서버 보안 주체(로그인)를 사용하는 SQL Managed Instance 보안](aad-security-configure-tutorial.md)을 참조하세요.

    ```sql
    use master
    go

    -- Create login for AAD user [testUser1@aadsqlmi.net]
    create login [testUser1@aadsqlmi.net] from external provider
    go

    -- Create login for the Azure AD group [migration]. This group contains one user [testGroupUser@aadsqlmi.net]
    create login [migration] from external provider
    go

    --Check the two new logins
    select * from sys.server_principals
    go
    ```

1. 마이그레이션에서 올바른 데이터베이스, 테이블 및 보안 주체가 있는지 확인합니다.

    ```sql
    -- Switch to the database migration that is already restored for MI
    use migration;
    go

    --Check if the restored table test exist and contain a row
    select * from test;
    go

    -- Check that the SQL on-premises Windows user/group exists  
    select * from sys.database_principals;
    go
    -- the old user aadsqlmi\testUser1 should be there
    -- the old group aadsqlmi\migration should be there
    ```

1. ALTER USER 구문을 사용하여 온-프레미스 사용자를 Azure AD 로그인에 매핑합니다.

    ```sql
    /** Execute the ALTER USER command to alter the Windows user [aadsqlmi\testUser1]
    to map to the Azure AD user testUser1@aadsqlmi.net
    **/
    alter user [aadsqlmi\testUser1] with login = [testUser1@aadsqlmi.net];
    go

    -- Check the principal
    select * from sys.database_principals;
    go
    -- New user testUser1@aadsqlmi.net should be there instead
    --Check new user permissions  - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    -- Check a specific role
    -- Display Db user name assigned to a specific role
    SELECT DP1.name AS DatabaseRoleName,
    isnull (DP2.name, 'No members') AS DatabaseUserName
    FROM sys.database_role_members AS DRM
    RIGHT OUTER JOIN sys.database_principals AS DP1
    ON DRM.role_principal_id = DP1.principal_id
    LEFT OUTER JOIN sys.database_principals AS DP2
    ON DRM.member_principal_id = DP2.principal_id
    WHERE DP1.type = 'R'
    ORDER BY DP1.name;
    ```

1. ALTER USER 구문을 사용하여 온-프레미스 그룹을 Azure AD 로그인에 매핑합니다.

    ```sql
    /** Execute ALTER USER command to alter the Windows group [aadsqlmi\migration]
    to the Azure AD group login [migration]
    **/
    alter user [aadsqlmi\migration] with login = [migration];
    -- old group migration is changed to Azure AD migration group
    go

    -- Check the principal
    select * from sys.database_principals;
    go

    --Check the group permission - should only have CONNECT permissions
    select user_name(grantee_principal_id), * from sys.database_permissions;
    go

    --Check the db_owner role for 'aadsqlmi\migration' user
    select is_rolemember('db_owner', 'migration')
    go
    -- Output 1 means 'YES'
    ```

## <a name="part-5-testing-azure-ad-user-or-group-authentication"></a>5부: Azure AD 사용자 또는 그룹 인증 테스트

이전에 ALTER USER 구문으로 Azure AD 로그인에 매핑된 사용자를 사용하여 SQL Managed Instance에 대한 인증을 테스트합니다.

1. Azure SQL Managed Instance 구독을 `aadsqlmi\testUser1`로 사용하여 페더레이션된 VM에 로그인
1. SSMS(SQL Server Management Studio)를 사용하는 경우 **Active Directory 통합** 인증을 사용하여 SQL Managed Instance에 로그인하고 `migration` 데이터베이스에 연결합니다.
    1. **Active Directory - MFA 지원을 통한 유니버설 인증** SSMS 옵션과 함께 testUser1@aadsqlmi.net 자격 증명을 사용하여 로그인할 수도 있습니다. 그러나 이 경우 Single Sign-On 메커니즘을 사용할 수 없으며 암호를 입력해야 합니다. 페더레이션된 VM을 사용하여 SQL Managed Instance에 로그인할 필요는 없습니다.
1. **SELECT** 역할 멤버의 일부로 `test` 테이블에서 선택할 수 있습니다.

    ```sql
    Select * from test  --  and see one row (1,10)
    ```

Windows 그룹 `migration`의 멤버를 사용하여 SQL Managed Instance에 대한 인증을 테스트합니다. 마이그레이션하기 전에 `aadsqlmi\testGroupUser` 사용자를 `migration` 그룹에 추가해야 합니다.

1. Azure SQL Managed Instance 구독을 `aadsqlmi\testGroupUser`로 사용하여 페더레이션된 VM에 로그인
1. **Active Directory 통합** 인증과 함께 SSMS를 사용하여 Azure SQL Managed Instance 서버와 `migration` 데이터베이스에 연결
    1. **Active Directory - MFA 지원을 통한 유니버설 인증** SSMS 옵션과 함께 testGroupUser@aadsqlmi.net 자격 증명을 사용하여 로그인할 수도 있습니다. 그러나 이 경우 Single Sign-On 메커니즘을 사용할 수 없으며 암호를 입력해야 합니다. 페더레이션된 VM을 사용하여 SQL Managed Instance에 로그인할 필요는 없습니다.
1. `db_owner` 역할의 일부로 새 테이블을 만들 수 있습니다.

    ```sql
    -- Create table named 'new' with a default schema
    Create table dbo.new ( a int, b int)
    ```

> [!NOTE]
> 알려진 Azure SQL Database 디자인 문제로 인해 그룹의 멤버로 실행된 테이블 만들기 문이 실패하고 다음 오류가 발생합니다. </br> </br>
> `Msg 2760, Level 16, State 1, Line 4
The specified schema name "testGroupUser@aadsqlmi.net" either does not exist or you do not have permission to use it.` </br> </br>
> 현재 해결 방법은 위 <dbo.new>의 경우에서 기존 스키마가 있는 테이블을 만드는 것입니다.

## <a name="next-steps"></a>다음 단계

[자습서: DMS를 사용하여 SQL Server를 Azure SQL Managed Instance로 오프라인 마이그레이션](../../dms/tutorial-sql-server-to-managed-instance.md?toc=/azure/sql-database/toc.json)
