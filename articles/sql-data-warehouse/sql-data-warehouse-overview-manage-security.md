---
title: 데이터베이스 보안
description: SQL Analytics의 SQL 풀 리소스에서 데이터베이스를 보호 하 고 솔루션을 개발 하기 위한 팁입니다.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26cdbb1fc2899d1b03fea6199074467623706c63
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153284"
---
# <a name="secure-a-database-in-sql-data-warehouse"></a>SQL Data Warehouse에서 데이터베이스 보호
> [!div class="op_single_selector"]
> * [보안 개요](sql-data-warehouse-overview-manage-security.md)
> * [인증](sql-data-warehouse-authentication.md)
> * [암호화(포털)](sql-data-warehouse-encryption-tde.md)
> * [암호화(T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

이 문서에서는 SQL 분석 내에서 SQL 풀을 보호 하기 위한 기본 사항을 안내 합니다. 특히이 문서에서는 SQL 풀을 사용 하 여 프로 비전 된 데이터베이스에 대 한 액세스를 제한 하 고, 데이터를 보호 하 고, 작업을 모니터링 하기 위한 리소스를 시작 합니다.

## <a name="connection-security"></a>연결 보안
연결 보안은 방화벽 규칙 및 연결 암호화를 사용하여 데이터베이스에 대한 연결을 제한하고 보호하는 방법을 가리킵니다.

서버와 데이터베이스 모두에서 방화벽 규칙을 사용 하 여 명시적으로 허용 목록 되지 않은 IP 주소에서의 연결 시도를 거부 합니다. 애플리케이션 또는 클라이언트 컴퓨터의 공용 IP 주소에서 연결할 수 있도록 허용하려면 먼저 Azure Portal, REST API 또는 PowerShell을 사용하여 서버 수준 방화벽 규칙을 만들어야 합니다. 

서버 방화벽을 통해 허용되는 IP 주소 범위를 최대한 많이 제한하는 것이 좋습니다.  로컬 컴퓨터에서 SQL 풀에 액세스 하려면 네트워크와 로컬 컴퓨터의 방화벽이 TCP 포트 1433에서 나가는 통신을 허용 하는지 확인 합니다.  

Azure Synapse Analytics는 서버 수준 IP 방화벽 규칙을 사용 합니다. 데이터베이스 수준 IP 방화벽 규칙은 지원 하지 않습니다. 자세한 내용은 [Azure SQL Database 방화벽 규칙](../sql-database/sql-database-firewall-configure.md) 을 참조 하세요.

SQL 풀에 대 한 연결은 기본적으로 암호화 됩니다.  암호화를 사용하지 않도록 연결 설정을 수정해도 무시됩니다.

## <a name="authentication"></a>인증
인증은 데이터베이스에 연결할 때 사용자의 ID를 증명하는 방법을 가리킵니다. SQL 풀은 현재 사용자 이름 및 암호를 사용 하는 SQL Server 인증과 Azure Active Directory를 지원 합니다. 

데이터베이스의 논리 서버를 만들 때 사용자 이름 및 암호를 사용하여 "서버 관리자" 로그인을 지정했습니다. 이러한 자격 증명을 사용하면, SQL Server 인증을 통해 해당 서버의 모든 데이터베이스에 데이터베이스 소유자 또는 "dbo"로 인증할 수 있습니다.

그러나 조직의 사용자는 다른 계정으로 인증하는 것이 좋습니다. 이렇게 하면 애플리케이션에 부여되는 사용 권한을 제한하여 애플리케이션 코드가 SQL 삽입 공격에 취약한 경우 악의적인 활동의 위험을 줄일 수 있습니다. 

SQL Server 인증 사용자를 만들려면 서버 관리자 로그인을 사용하여 서버의 **master** 데이터베이스에 연결하고 새 서버 로그인을 만듭니다.  Master 데이터베이스 Azure Synapse 사용자에도 사용자를 만드는 것이 좋습니다. 마스터에서 사용자를 만들면 데이터베이스 이름을 지정하지 않아도 사용자가 SSMS 등의 도구를 사용하여 로그인할 수 있습니다.  또한 개체 탐색기를 사용하여 SQL server의 모든 데이터베이스를 볼 수 있습니다.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

그런 다음 서버 관리자 로그인을 사용 하 여 **SQL 풀 데이터베이스** 에 연결 하 고 사용자가 만든 서버 로그인에 따라 데이터베이스 사용자를 만듭니다.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

로그인 만들기 또는 새 데이터베이스 만들기 등 추가 작업을 수행하도록 사용자에게 권한을 부여하려면 마스터 데이터베이스에서 `Loginmanager` 및 `dbmanager` 역할에 사용자를 할당합니다. 

이러한 추가 역할 및 SQL Database에 대 한 인증에 대 한 자세한 내용은 [Azure SQL Database에서 데이터베이스 및 로그인 관리](../sql-database/sql-database-manage-logins.md)를 참조 하세요.  Azure Active Directory 사용 하 여 연결 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 인증을 사용 하 여 연결](sql-data-warehouse-authentication.md)을 참조 하세요.

## <a name="authorization"></a>권한 부여
권한 부여는 인증 되 고 연결 된 후 데이터베이스 내에서 수행할 수 있는 작업을 나타냅니다. 권한 부여 권한은 역할 멤버 자격과 권한에 따라 결정됩니다. 사용자에게 필요한 최소한의 권한을 부여하는 것이 가장 좋습니다. 역할을 관리하기 위해 다음 저장 프로시저를 사용할 수 있습니다.

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

연결 중인 서버 관리자 계정은 데이터베이스 내에서 작업을 수행할 권한이 있는 db_owner의 구성원입니다. 스키마 업그레이드 및 기타 관리 작업을 배포하기 위해서는 이 계정을 저장합니다. 애플리케이션에서 해당 애플리케이션에 필요한 최소한의 권한이 있는 데이터베이스에 연결하려면 보다 제한된 사용 권한을 가진 "ApplicationUser" 계정을 사용합니다.

사용자가 데이터 웨어하우스 내에서 수행할 수 있는 작업을 추가로 제한할 수 있는 방법은 다음과 같습니다.

* 세부적인 [사용 권한을](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine?view=sql-server-ver15) 통해 개별 열, 테이블, 뷰, 스키마, 프로시저 및 데이터베이스의 다른 개체에서 수행할 수 있는 작업을 제어할 수 있습니다. 세분화된 사용 권한을 사용하여 최대한으로 제어하고 최소한의 필요 권한을 부여합니다. 
* db_datareader 및 db_datawriter 이외의 [데이터베이스 역할](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles?view=sql-server-ver15)은 더 강력한 애플리케이션 사용자 계정이나 덜 강력한 관리 계정을 만드는 데 사용할 수 있습니다. 기본 제공되는 고정 데이터베이스 역할은 사용 권한을 부여하는 쉬운 방법을 제공하지만 필요한 것보다 많은 사용 권한이 부여될 수 있습니다.
* [저장 프로시저](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine?redirectedfrom=MSDN&view=sql-server-ver15) 는 데이터베이스에서 수행할 수 있는 작업을 제한하는 데 사용할 수 있습니다.

다음 예제에서는 사용자 정의 스키마에 대한 읽기 권한을 부여합니다.
```sql
--CREATE SCHEMA Test
GRANT SELECT ON SCHEMA::Test to ApplicationUser
```

Azure Portal에서 또는 Azure Resource Manager API를 사용하여 데이터베이스 및 논리 서버를 관리하는 작업은 포털 사용자 계정의 역할 할당에 의해 제어됩니다. 자세한 내용은 [Azure Portal의 역할 기반 액세스 제어](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure)를 참조 하세요.

## <a name="encryption"></a>암호화
TDE (투명한 데이터 암호화)는 미사용 데이터를 암호화 하 고 암호를 해독 하 여 악의적인 활동의 위협 으로부터 보호 하는 데 도움이 됩니다. 데이터베이스를 암호화할 때, 애플리케이션을 변경할 필요 없이 연관된 백업 및 트랜잭션 로그 파일이 암호화됩니다. TDE는 데이터베이스 암호화 키라는 대칭 키를 사용하여 전체 데이터베이스의 스토리지를 암호화합니다. 

SQL Database에서 데이터베이스 암호화 키는 기본 제공 서버 인증서에 의해 보호됩니다. 기본 제공 서버 인증서는 각 SQL Database 서버에 대해 고유합니다. Microsoft는 적어도 90일마다 이러한 인증서를 자동으로 회전시킵니다. 사용 되는 암호화 알고리즘은 AES-256입니다. TDE에 대한 일반적인 설명은 [투명한 데이터 암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15)를 참조하세요.

[Azure Portal](sql-data-warehouse-encryption-tde.md) 또는 [t-sql](sql-data-warehouse-encryption-tde-tsql.md)을 사용 하 여 데이터베이스를 암호화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다른 프로토콜을 사용 하 여 웨어하우스에 연결 하는 방법에 대 한 자세한 내용과 예제는 [SQL 풀에 연결](sql-data-warehouse-connect-overview.md)을 참조 하세요.
