---
title: Azure SQL Managed Instance & SQL Server 간의 t-sql 차이점
description: 이 문서에서는 Azure SQL Managed Instance와 SQL Server 간의 Transact-sql (T-sql) 차이점에 대해 설명 합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova, danil
ms.date: 06/02/2020
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: d611fc7eff2efa7a632f4b5467b5829a8374b95e
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705387"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Azure SQL Managed Instance & SQL Server 간의 t-sql 차이점
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

이 문서에서는 Azure SQL Managed Instance와 SQL Server 간의 구문 및 동작 차이를 요약 하 고 설명 합니다. 


SQL Managed Instance는 SQL Server 데이터베이스 엔진과의 호환성을 제공 하며 대부분의 기능은 SQL Managed Instance에서 지원 됩니다.

![SQL Server에서 쉽게 마이그레이션](./media/transact-sql-tsql-differences-sql-server/migration.png)

SQL Managed Instance에서 도입 된 몇 가지 PaaS 제한 사항이 있으며 SQL Server에 비해 일부 동작이 변경 되었습니다. 차이점은 다음 범주로 구분됩니다. <a name="Differences"></a>

- [가용성](#availability)에는 [Always On 가용성 그룹](#always-on-availability-groups) 및 [백업](#backup)의 차이점이 포함됩니다.
- [감사](#auditing), [인증서](#certificates), [자격 증명](#credential), [암호화 공급자](#cryptographic-providers), [로그인 및 사용자](#logins-and-users), [서비스 키 및 서비스 마스터 키](#service-key-and-service-master-key)의 차이점을 비롯한 [보안](#security).
- [버퍼 풀 확장](#buffer-pool-extension), [데이터 정렬](#collation), [호환성 수준](#compatibility-levels), [데이터베이스 미러링](#database-mirroring), [데이터베이스 옵션](#database-options), [SQL Server 에이전트](#sql-server-agent), [테이블 옵션](#tables)의 차이점을 비롯한 [구성](#configuration).
- [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [분산 트랜잭션](#distributed-transactions), [확장 이벤트](#extended-events), [외부 라이브러리](#external-libraries), [파일 스트림 및 파일 테이블](#filestream-and-filetable), [전체 텍스트 의미 체계 검색](#full-text-semantic-search), [연결된 서버](#linked-servers), [PolyBase](#polybase), [복제](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker), [저장 프로시저, 함수 및 트리거](#stored-procedures-functions-and-triggers)를 비롯한 [기능](#functionalities).
- VNet 및 서브넷 구성과 같은 [환경 설정](#Environment).

이러한 기능 중 대부분은 아키텍처 제약 조건이며 서비스 기능을 나타냅니다.

SQL Managed Instance에서 검색 되 고 나중에 해결 될 수 있는 일시적인 알려진 문제는 [릴리스 정보 페이지](../database/doc-changes-updates-release-notes.md)에 설명 되어 있습니다.

## <a name="availability"></a>가용성

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Always On 가용성 그룹

[고가용성](../database/high-availability-sla.md) 은 SQL Managed Instance 기본 제공 되며 사용자가 제어할 수 없습니다. 다음 문은 지원되지 않습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="backup"></a>Backup

SQL Managed Instance에는 자동 백업이 있으므로 사용자는 전체 데이터베이스 백업을 만들 수 있습니다 `COPY_ONLY` . 차등, 로그 및 파일 스냅샷 백업은 지원되지 않습니다.

- SQL Managed Instance를 사용 하 여 Azure Blob storage 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE`, `TAPE` 및 백업 디바이스는 지원되지 않습니다.
- 대부분의 일반 `WITH` 옵션이 지원됩니다.
  - `COPY_ONLY`는 필수입니다.
  - `FILE_SNAPSHOT`는 지원되지 않습니다.
  - 테이프 옵션: `REWIND`, `NOREWIND`, `UNLOAD` 및 `NOUNLOAD`는 지원되지 않습니다.
  - 로그 관련 옵션: `NORECOVERY`, `STANDBY` 및 `NO_TRUNCATE`는 지원되지 않습니다.

제한 사항: 

- SQL Managed Instance를 사용 하면 최대 32 개의 스트라이프를 사용 하 여 백업에 인스턴스 데이터베이스를 백업할 수 있습니다. 백업 압축을 사용 하는 경우 최대 2TB의 데이터베이스에 충분 합니다.
- 서비스 관리 TDE(투명한 데이터 암호화)로 암호화된 데이터베이스에서는 `BACKUP DATABASE ... WITH COPY_ONLY`를 실행할 수 없습니다. 서비스 관리 TDE는 내부 TDE 키를 사용하여 백업을 암호화합니다. 키를 내보낼 수 없어 백업을 복원할 수 없습니다. 자동 백업 및 특정 시점 복원을 사용하거나 [고객 관리(BYOK) TDE](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key)를 대신 사용합니다. 데이터베이스에서 암호화를 사용하지 않도록 설정할 수 있습니다.
- SQL Managed Instance의 명령을 사용 하 여 최대 백업 스트라이프 크기는 `BACKUP` 최대 blob 크기인 195 GB입니다. 개별 스트라이프 크기를 줄이고 이 제한 내로 유지하려면 백업 명령에서 스트라이프 수를 늘립니다.

    > [!TIP]
    > 이 제한을 해결하기 위해 온-프레미스 환경 또는 가상 머신의 SQL Server에서 데이터베이스를 백업하는 경우 다음 작업을 수행할 수 있습니다.
    >
    > - `URL`을 백업하는 대신 `DISK`에 백업합니다.
    > - BLOB Storage에 백업 파일을 업로드합니다.
    > - SQL Managed Instance로 복원 합니다.
    >
    > `Restore`업로드 된 백업 파일의 저장소에 다른 blob 유형이 사용 되므로 SQL Managed Instance의 명령은 백업 파일의 blob 크기를 더 크게 지원 합니다.

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Microsoft Azure SQL과 SQL Server의 감사 간 주요 차이점은 다음과 같습니다.

- SQL Managed Instance를 사용 하 여 감사는 서버 수준에서 작동 합니다. `.xel` 로그 파일은 Azure BLOB Storage에 저장됩니다.
- Azure SQL Database에서 감사는 데이터베이스 수준에서 작동 합니다. `.xel` 로그 파일은 Azure BLOB Storage에 저장됩니다.
- SQL Server 온-프레미스 또는 가상 머신에서 감사는 서버 수준에서 작동 합니다. 이벤트는 파일 시스템 또는 Windows 이벤트 로그에 저장됩니다.
 
SQL Managed Instance의 XEvent 감사는 Azure Blob 저장소 대상을 지원 합니다. 파일 및 Windows 로그는 지원되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치되는 Azure Blob Storage 컨테이너의 URL을 지정하는 데 사용할 수 있습니다.
- `TO FILE`SQL Managed Instance Windows 파일 공유에 액세스할 수 없으므로 구문은 지원 되지 않습니다.

자세한 내용은 다음을 참조하세요. 

- [CREATE SERVER AUDIT](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTER SERVER AUDIT](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

SQL Managed Instance는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용 됩니다.

- `CREATE FROM`/`BACKUP TO` 파일은 인증서에서 지원되지 않습니다.
- `FILE`/`ASSEMBLY`의 `CREATE`/`BACKUP` 인증서는 지원되지 않습니다. 프라이빗 키 파일은 사용할 수 없습니다. 

[CREATE CERTIFICATE](/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요. 
 
**해결 방법**: 인증서의 백업을 만들고 백업을 복원하는 대신 [인증서 이진 콘텐츠 및 프라이빗 키를 가져와서 .sql 파일로 저장하고 이진에서 만듭니다](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database).

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

SQL Managed Instance 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins-and-users"></a>로그인 및 사용자

- `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` 및 `FROM SID`를 사용하여 만든 SQL 로그인이 지원됩니다. [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 구문 또는 [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 구문을 사용하여 만든 Azure AD(Azure Active Directory) 서버 보안 주체(로그인)가 지원됩니다. 이러한 로그인은 서버 수준에서 만들어집니다.

    SQL Managed Instance는 구문을 사용 하 여 Azure AD 데이터베이스 보안 주체를 지원 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 합니다. 이 기능을 Azure AD 포함된 데이터베이스 사용자라고도 합니다.

- `CREATE LOGIN ... FROM WINDOWS` 구문으로 만든 Windows 로그인은 지원되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- 인스턴스를 만든 Azure AD 사용자에게는 [무제한 관리자 권한](../database/logins-create-manage.md)이 있습니다.
- 관리자가 아닌 Azure AD 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용하여 만들 수 있습니다. [CREATE USER ... FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)를 참조하세요.
- Azure AD 서버 보안 주체 (로그인)는 하나의 SQL Managed Instance 내 에서만 SQL 기능을 지원 합니다. 인스턴스 간 상호 작용이 필요한 기능은 동일한 Azure AD 테넌트에 있는지 또는 다른 테넌트 내에 있는지에 관계없이 Azure AD 사용자에게 지원되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제
  - 링크 서버

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다.
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 절과 같이, 다른 Azure AD 보안 주체를 사용하여 Azure AD 서버 수준 보안 주체를 가장할 수 있습니다. EXECUTE AS 제한 사항은 다음과 같습니다.

  - 이름이 로그인 이름과 다른 경우 EXECUTE AS USER는 Azure AD 사용자에 대해 지원되지 않습니다. 예를 들어 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com]을 통해 사용자를 만들고 EXEC AS USER = _myAadUser_를 통해 가장을 시도하는 경우입니다. Azure AD 서버 보안 주체(로그인)에서 **USER**를 만들 때 **LOGIN**에서 user_name을 동일한 login_name으로 지정합니다.
  - `sysadmin` 역할에 포함된 SQL 서버 수준 보안 주체(로그인)만이 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Bacpac 파일을 사용 하는 데이터베이스 내보내기/가져오기는 [SSMS v 18.4 이상](/sql/ssms/download-sql-server-management-studio-ssms)또는 [SQLPackage.exe](/sql/tools/sqlpackage-download)를 사용 하 여 SQL Managed Instance의 Azure AD 사용자에 대해 지원 됩니다.
  - 데이터베이스 bacpac 파일을 사용하여 다음 구성이 지원됩니다. 
    - 동일한 Azure AD 도메인 내의 서로 다른 관리 인스턴스 간에 데이터베이스 내보내기/가져오기
    - SQL Managed Instance에서 데이터베이스를 내보내고 동일한 Azure AD 도메인 내에서 SQL Database으로 가져옵니다. 
    - SQL Database에서 데이터베이스를 내보내고 동일한 Azure AD 도메인 내에서 SQL Managed Instance으로 가져옵니다.
    - SQL Managed Instance에서 데이터베이스를 내보내고 SQL Server (버전 2012 이상)로 가져옵니다.
      - 이 구성에서는 모든 Azure AD 사용자가 로그인 없이 SQL Server 데이터베이스 보안 주체 (사용자)로 생성 됩니다. 사용자의 유형은로 나열 되며 `SQL` , `SQL_USER` database_principals)에 표시 됩니다. 해당 사용 권한과 역할은 SQL Server 데이터베이스 메타데이터에 남아 있으며 가장에 사용할 수 있습니다. 그러나 해당 자격 증명을 사용하여 SQL Server에 액세스하고 로그인하는 데는 사용할 수 없습니다.

- SQL Managed Instance 프로 비전 프로세스에서 생성 된 서버 수준 보안 주체 로그인, 서버 역할의 멤버 (예: `securityadmin` 또는) `sysadmin` 또는 서버 수준에서 ALTER ANY login 권한이 있는 기타 로그인만 sql Managed Instance의 master 데이터베이스에 Azure AD 서버 보안 주체 (로그인)를 만들 수 있습니다.
- 로그인이 SQL 보안 주체인 경우 `sysadmin` 역할에 포함된 로그인만 create 명령을 사용하여 Azure AD 계정에 대한 로그인을 만들 수 있습니다.
- Azure AD 로그인은 azure SQL Managed Instance에 사용 되는 것과 동일한 디렉터리 내에서 Azure AD의 멤버 여야 합니다.
- Azure AD 서버 보안 주체(로그인)는 SQL Server Management Studio 18.0 미리 보기 5부터 개체 탐색기에 표시됩니다.
- Azure AD 서버 보안 주체(로그인)를 Azure AD 관리자 계정과 겹칠 수 있습니다. Azure AD 서버 보안 주체 (로그인)는 주 서버를 확인 하 고 SQL Managed Instance에 대 한 사용 권한을 적용 하는 경우 Azure AD 관리자 보다 우선적으로 적용 됩니다.
- 인증 중에 인증 보안 주체를 확인하기 위해 적용되는 시퀀스는 다음과 같습니다.

    1. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 경우(sys.server_principals에서 "E" 형식으로), 액세스 권한을 부여하고 Azure AD 서버 보안 주체(로그인)의 사용 권한을 적용합니다.
    2. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 Azure AD 그룹의 멤버인 경우(sys.server_principals에서 "X" 형식으로), 액세스 권한을 부여하고 Azure AD 그룹 로그인의 사용 권한을 적용합니다.
    3. Azure AD 계정이 sql Managed Instance에 대 한 특별 한 포털 구성 Azure AD 관리자 인 경우 (sql Managed Instance 시스템 뷰에 존재 하지 않음) SQL Managed Instance (레거시 모드)에 대 한 Azure AD 관리자의 특수 한 고정 권한을 적용 합니다.
    4. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 직접 매핑된 경우(sys.database_principals에서 "E" 형식으로), 액세스 권한을 부여하고 Azure AD 데이터베이스 사용자의 사용 권한을 적용합니다.
    5. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 매핑된 Azure AD 그룹의 멤버인 경우(sys.database_principals에서 "X" 형식으로), 액세스 권한을 부여하고 Azure AD 그룹 로그인의 사용 권한을 적용합니다.
    6. Azure AD 사용자 계정 또는 Azure AD 그룹 계정에 매핑되어 사용자 인증으로 확인되는 Azure AD 로그인이 있는 경우, 이 Azure AD 로그인의 모든 사용 권한이 적용됩니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업](/sql/t-sql/statements/backup-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [마스터 키 복원](/sql/t-sql/statements/restore-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 백업](/sql/t-sql/statements/backup-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 복원](/sql/t-sql/statements/restore-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.

## <a name="configuration"></a>구성

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장](/sql/database-engine/configure-windows/buffer-pool-extension)은 지원되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`는 지원되지 않습니다. [ALTER SERVER CONFIGURATION](/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>데이터 정렬

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원되는 호환성 수준: 100, 110, 120, 130, 140, 150
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원된 데이터베이스의 경우 호환성 수준이 100 이상이면 변경되지 않고 그대로 유지됩니다.

[ALTER DATABASE 호환성 수준](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`는 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다. 
- 범용 인스턴스당 280개의 파일 제한이 있으며 이는 데이터베이스당 최대 280개의 파일을 의미합니다. 범용 계층의 데이터와 로그 파일은 모두 이 제한으로 계산됩니다. [중요 비즈니스용 계층은 데이터베이스당 32767개의 파일을 지원합니다](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- 데이터베이스는 파일 스트림 데이터를 포함한 파일 그룹을 포함할 수 없습니다. .bak에 `FILESTREAM` 데이터가 포함된 경우 복원이 실패합니다. 
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.

#### <a name="create-database-statement"></a>CREATE DATABASE 문

다음 제한 사항이 `CREATE DATABASE`에 적용됩니다.

- 파일 및 파일 그룹을 정의할 수 없습니다. 
- `CONTAINMENT` 옵션은 지원되지 않습니다. 
- `WITH` 옵션은 지원되지 않습니다. 
   > [!TIP]
   > 해결 방법으로, `CREATE DATABASE` 뒤에 `ALTER DATABASE`를 사용하여 파일을 추가하거나 포함을 설정하는 데이터베이스 옵션을 설정합니다. 

- `FOR ATTACH` 옵션은 지원되지 않습니다.
- `AS SNAPSHOT OF` 옵션은 지원되지 않습니다.

자세한 내용은 [CREATE DATABASE](/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 문에서 파일 경로를 지정할 수 없습니다. `FILENAME`SQL Managed Instance 자동으로 파일을 저장 하기 때문에 스크립트에서을 제거 합니다. 
- `ALTER DATABASE` 문을 사용하여 파일 이름을 변경할 수 없습니다.

기본적으로 설정되고 변경할 수 없는 옵션은 다음과 같습니다.

- `MULTI_USER`
- `ENABLE_BROKER ON`
- `AUTO_CLOSE OFF`

수정할 수 없는 옵션은 다음과 같습니다.

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

자세한 내용은 [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트

- SQL Server 에이전트 활성화 및 비활성화는 현재 SQL Managed Instance에서 지원 되지 않습니다. SQL 에이전트는 항상 실행됩니다.
- SQL Server 에이전트 설정은 읽기 전용입니다. 이 프로시저는 `sp_set_agent_properties` SQL Managed Instance에서 지원 되지 않습니다. 
- 작업
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅샷
    - 배포자
  - SSIS 작업 단계가 지원됩니다.
  - 다른 유형의 작업 단계는 현재 지원되지 않습니다.
    - 병합 복제 작업 단계는 지원되지 않습니다. 
    - 큐 판독기는 지원되지 않습니다. 
    - 명령 셸은 아직 지원되지 않습니다.
  - SQL Managed Instance는 robocopy를 통한 네트워크 공유와 같은 외부 리소스에 액세스할 수 없습니다. 
  - SQL Server Analysis Services 지원 되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 데이터베이스 메일 프로필을 구성해야 하지만 이메일 알림이 지원됩니다. SQL Server 에이전트는 데이터베이스 메일 프로필을 하나만 사용할 수 있으며 `AzureManagedInstance_dbmail_profile`이라고 해야 합니다. 
  - 호출기는 지원되지 않습니다.
  - NetSend는 지원되지 않습니다.
  - 경고는 아직 지원되지 않습니다.
  - 프록시는 지원되지 않습니다.
- EventLog는 지원되지 않습니다.

현재 지원되지 않는 SQL 에이전트 기능은 다음과 같습니다.

- 프록시
- 유휴 CPU에 대한 작업 예약
- 에이전트 사용 또는 사용 안 함
- 경고

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

다음 테이블 형식은 지원되지 않습니다.

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)(PolyBase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables)(범용 계층에서만 지원되지 않음)

테이블을 만들고 변경하는 방법에 대한 자세한 내용은 [CREATE TABLE](/sql/t-sql/statements/create-table-transact-sql) 및 [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql)을 참조하세요.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>BULK INSERT/OPENROWSET

SQL Managed Instance 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob storage에서 파일을 가져와야 합니다.

- `DATASOURCE`는 Azure BLOB Storage에서 파일을 가져오는 동안 `BULK INSERT` 명령에 필요합니다. [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- Azure Blob Storage에서 파일의 내용을 읽을 때 `OPENROWSET` 함수에 `DATASOURCE`가 필요합니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENROWSET` 을 사용 하 여 Azure SQL Database, Azure SQL Managed Instance 또는 SQL Server 인스턴스에서 데이터를 읽을 수 있습니다. Oracle Database 또는 Excel 파일 등의 다른 원본은 지원되지 않습니다.

### <a name="clr"></a>CLR

SQL Managed Instance는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용 됩니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요. 
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.

### <a name="database-mail-db_mail"></a>데이터베이스 메일(db_mail)
 - `sp_send_dbmail`이 @file_attachments 매개 변수를 사용하여 첨부 파일을 보낼 수 없습니다. 이 프로시저에서는 로컬 파일 시스템 및 외부 공유 또는 Azure BLOB Storage에 액세스할 수 없습니다.
 - `@query` 매개 변수 및 인증과 관련된 알려진 문제를 참조하세요.
 
### <a name="dbcc"></a>DBCC

SQL Server에서 사용 하도록 설정 된 문서화 되지 않은 DBCC 문은 SQL Managed Instance에서 지원 되지 않습니다.

- 제한된 수의 전역 추적 플래그만 지원됩니다. 세션 수준 `Trace flags`는 지원되지 않습니다. [추적 플래그](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)를 참조하세요.
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) 및 [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)은 제한된 수의 전역 추적 플래그와 함께 사용합니다.
- 데이터베이스를 `SINGLE_USER` 모드로 설정할 수 없으므로 REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST 및 REPAIR_REBUILD 옵션과 함께 [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql)를 사용할 수 없습니다. [ALTER DATABASE 차이점](#alter-database-statement)을 참조하세요. 잠재적 데이터베이스 손상은 Azure 지원 팀에서 처리 합니다. 데이터베이스가 손상 된 경우 Azure 지원에 문의 하세요.

### <a name="distributed-transactions"></a>분산 트랜잭션

MSDTC 및 [탄력적 트랜잭션은](../database/elastic-transactions-overview.md) 현재 SQL Managed Instance에서 지원 되지 않습니다.

### <a name="extended-events"></a>확장 이벤트

확장 이벤트(Xevent)에 대한 일부 Windows 관련 대상은 지원되지 않습니다.

- `etw_classic_sync` 대상은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [etw_classic_sync 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file` 대상은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [event_file 대상](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python 외부 라이브러리는 제한 된 공개 미리 보기에서 지원 됩니다. [AZURE SQL Managed Instance에서 Machine Learning Services (미리 보기)를](machine-learning-services-overview.md)참조 하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 FileTable

- 파일 스트림 데이터가 지원되지 않습니다.
- `FILESTREAM` 데이터가 있는 파일 그룹을 데이터베이스에 포함할 수 없습니다.
- `FILETABLE`는 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

자세한 내용은 [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버

SQL Managed Instance의 연결 된 서버는 제한 된 수의 대상을 지원 합니다.

- 지원 되는 대상은 SQL Managed Instance, SQL Database, Azure Synapse SQL 및 SQL Server 인스턴스입니다. 
- 연결된 서버는 분산 쓰기 가능 트랜잭션(MS DTC)을 지원하지 않습니다.
- 지원되지 않는 대상은 파일, Analysis Services 및 기타 RDBMS입니다. 파일 가져오기의 대안으로 `BULK INSERT` 또는 `OPENROWSET`를 사용하여 Azure Blob Storage에서 기본 CSV 가져오기를 사용합니다.

작업: 

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- `OPENROWSET` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리되는 컴퓨터, 온-프레미스 컴퓨터 또는 가상 머신 중 하나일 수 있습니다. [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENDATASOURCE` 함수는 SQL Server 인스턴스에서만 쿼리를 실행하는 데 사용할 수 있습니다. 관리되는 컴퓨터, 온-프레미스 컴퓨터 또는 가상 머신 중 하나일 수 있습니다. `SQLNCLI`, `SQLNCLI11` 및 `SQLOLEDB` 값만 공급자로 지원됩니다. 예제는 `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`입니다. [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.
- 연결된 서버를 네트워크 공유에서 파일(Excel, CSV)을 읽는 데 사용할 수 없습니다. Azure Blob Storage에서 CSV 파일을 읽는 [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file) 또는 [ OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file)을 사용해 보세요. [SQL Managed Instance 피드백 항목](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources) 에서이 요청 추적|

### <a name="polybase"></a>PolyBase

HDFS 또는 Azure Blob Storage의 파일을 참조하는 외부 테이블은 지원되지 않습니다. PolyBase에 대한 자세한 내용은 [PolyBase](/sql/relational-databases/polybase/polybase-guide)를 참조하세요.

### <a name="replication"></a>복제

- 스냅숏과 양방향 복제 형식이 지원됩니다. 병합 복제, 피어 투 피어 복제 및 업데이트할 수 있는 구독은 지원되지 않습니다.
- [트랜잭션 복제](replication-transactional-overview.md) 는 다음과 같은 몇 가지 제약 조건으로 SQL Managed Instance의 공개 미리 보기에 사용할 수 있습니다.
    - 모든 유형의 복제 참여자 (게시자, 배포자, 끌어오기 구독자 및 밀어넣기 구독자)는 SQL Managed Instance에 배치 될 수 있지만 게시자와 배포자는 모두 클라우드 또는 온-프레미스 모두에 있어야 합니다.
    - SQL Managed Instance는 최신 버전의 SQL Server와 통신할 수 있습니다. 자세한 내용은 [지원되는 버전 매트릭스](replication-transactional-overview.md#supportability-matrix)를 참조하세요.
    - 트랜잭션 복제에는 [추가 네트워킹 요구 사항](replication-transactional-overview.md#requirements)이 있습니다.

트랜잭션 복제를 구성하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.
- [SQL MI 게시자와 SQL MI 구독자 간 복제](replication-between-two-instances-configure-tutorial.md)
- [SQL MI 게시자, SQL MI 배포자 및 SQL Server 구독자 간 복제](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>RESTORE 문 

- 지원되는 구문:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원되지 않는 구문:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 원본: 
  - `FROM URL`(Azure Blob Storage)만 지원되는 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH` 옵션은 지원되지 않습니다. ,, 등을 포함 한 복원 시도 `WITH` `DIFFERENTIAL` `STATS` `REPLACE` 는 실패 합니다.
- `ASYNC RESTORE`: 클라이언트 연결이 중단되더라도 복원이 계속됩니다. 연결이 삭제된 경우 `sys.dm_operation_status` 보기에서 복원 작업 상태와 CREATE 및 DROP Database를 확인할 수 있습니다. 자세한 내용은 [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요. 

다음은 설정 또는 재정의되고 나중에 변경할 수 없는 데이터베이스 옵션입니다. 

- `NEW_BROKER`(.bak 파일에서 브로커를 사용하도록 설정하지 않은 경우) 
- `ENABLE_BROKER`(.bak 파일에서 브로커를 사용하도록 설정하지 않은 경우) 
- `AUTO_CLOSE=OFF`(.bak 파일의 데이터베이스에 `AUTO_CLOSE=ON`이 있는 경우) 
- `RECOVERY FULL`(.bak 파일의 데이터베이스에 `SIMPLE` 또는 `BULK_LOGGED` 복구 모드가 있는 경우)
- 원본 .bak 파일에 없는 경우 메모리 최적화 파일 그룹이 추가되고 XTP라고 합니다. 
- 모든 기존 메모리에 최적화된 파일 그룹은 XTP로 이름이 변경됩니다. 
- `SINGLE_USER` 및 `RESTRICTED_USER` 옵션이 `MULTI_USER`로 변환됩니다.

제한 사항: 

- 손상된 데이터베이스의 백업은 손상 유형에 따라 복원될 수 있지만 손상이 수정될 때까지 자동화된 백업은 수행되지 않습니다. `DBCC CHECKDB`원본 SQL Managed Instance에서를 실행 하 고 `WITH CHECKSUM` 이 문제를 방지 하기 위해 백업을 사용 하는지 확인 합니다.
- `.BAK`이 문서에서 설명 하는 제한 (예: 또는 개체)을 포함 하는 데이터베이스 파일의 복원은 `FILESTREAM` `FILETABLE` SQL Managed Instance에서 복원할 수 없습니다.
- 여러 백업 세트가 포함된 `.BAK` 파일은 복원할 수 없습니다. 
- 여러 로그 파일이 포함된 `.BAK` 파일은 복원할 수 없습니다.
- 8TB보다 큰 데이터베이스, 활성 메모리 내 OLTP 개체 또는 인스턴스당 280개의 파일을 초과하는 파일 수를 포함하는 백업은 범용 인스턴스에서 복원할 수 없습니다. 
- 4TB보다 큰 데이터베이스 또는 [리소스 제한](resource-limits.md)에 설명된 크기보다 전체 크기가 큰 메모리 내 OLTP 개체가 포함된 백업은 중요 비즈니스용 인스턴스에서 복원할 수 없습니다.
복원 명령문에 대한 자세한 내용은 [RESTORE 문](/sql/t-sql/statements/restore-statements-transact-sql)을 참조하세요.

 > [!IMPORTANT]
 > 기본 제공 지정 시점 복원 작업에도 동일한 제한이 적용됩니다. 예를 들어 4TB보다 큰 범용 데이터베이스는 중요 비즈니스용 인스턴스에서 복원할 수 없습니다. 메모리 내 OLTP 파일 또는 280개보다 많은 파일을 포함하는 중요 비즈니스용 데이터베이스는 범용 인스턴스에서 복원할 수 없습니다.

### <a name="service-broker"></a>Service Broker

인스턴스 간 서비스 broker는 지원되지 않습니다.

- `sys.routes`: 필수 구성 요소로 sys.routes에서 주소를 선택해야 합니다. 주소는 모든 경로에서 LOCAL이어야 합니다. [sys.routes](/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
- `CREATE ROUTE`: `LOCAL` 이외의 `ADDRESS`가 포함된 `CREATE ROUTE`는 사용할 수 없습니다. [CREATE ROUTE](/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
- `ALTER ROUTE`: `LOCAL` 이외의 `ADDRESS`가 포함된 `ALTER ROUTE`는 사용할 수 없습니다. [ALTER ROUTE](/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요. 

### <a name="stored-procedures-functions-and-triggers"></a>저장 프로시저, 함수, 트리거

- `NATIVE_COMPILATION`은 범용 계층에서 지원되지 않습니다.
- 지원되지 않는 [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다. 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`는 지원되지 않습니다. [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `Extended stored procedures`는 `sp_addextendedproc` 및 `sp_dropextendedproc`를 포함하여 지원되지 않습니다. [확장 저장 프로시저](/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)를 참조하세요.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

### <a name="system-functions-and-variables"></a>시스템 함수 및 변수

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')`에서 값 8을 반환합니다. 이 속성은 SQL Managed Instance를 고유 하 게 식별 합니다. [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `SERVERPROPERTY('InstanceName')` SQL Server에 대 한 인스턴스 개념이 SQL Managed Instance에 적용 되지 않으므로 NULL을 반환 합니다. [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`은 전체 DNS "연결 가능한" 이름을 반환합니다(예: my-managed-instance.wcus17662feb9ce98.database.windows.net). [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql)을 참조하세요. 
- `SYS.SERVERS`는 "name" 및 "data_source" 속성에 대한 전체 DNS "연결 가능한" 이름을 반환합니다(예: `myinstance.domain.database.windows.net`). [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- `@@SERVICENAME` SQL Server에 대해 존재 하는 서비스 개념이 SQL Managed Instance에 적용 되지 않으므로 NULL을 반환 합니다. [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.
- `SUSER_ID`이 지원됩니다. Azure AD 로그인이 sys. syslogins에 없으면 NULL을 반환합니다. [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요. 
- `SUSER_SID`는 지원되지 않습니다. 잘못된 데이터가 반환되며 이는 알려진 일시적 문제입니다. [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요. 

## <a name="environment-constraints"></a><a name="Environment"></a>환경 제약 조건

### <a name="subnet"></a>서브넷
-  SQL Managed Instance를 배포한 서브넷에 다른 리소스 (예: 가상 컴퓨터)를 배치할 수 없습니다. 다른 서브넷을 사용하여 이러한 리소스를 배포합니다.
- 서브넷에 사용 가능한 [IP 주소](connectivity-architecture-overview.md#network-requirements) 수가 충분해야 합니다. 최소는 16개이지만 서브넷에 IP 주소가 32개 이상 있는 것이 좋습니다.
- [서비스 끝점은 SQL Managed Instance의 서브넷에 연결할 수 없습니다](connectivity-architecture-overview.md#network-requirements). 가상 네트워크를 만들 때 서비스 엔드포인트 옵션을 사용하지 않도록 설정해야 합니다.
- 지역에 배포할 수 있는 vCore 및 인스턴스 유형 수에는 몇 가지 [제약 조건 및 제한](resource-limits.md#regional-resource-limitations)이 있습니다.
- [서브넷에 적용해야 하는 보안 규칙](connectivity-architecture-overview.md#network-requirements)이 몇 가지 있습니다.

### <a name="vnet"></a>VNET
- VNet은 리소스 모델을 사용하여 배포할 수 있으며 VNet에 대한 클래식 모델은 지원되지 않습니다.
- SQL Managed Instance를 만든 후에는 SQL Managed Instance 또는 VNet을 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
- App Service 환경, 논리 앱 및 SQL Managed Instance (지역에서 복제, 트랜잭션 복제 또는 연결 된 서버를 통해 사용)와 같은 일부 서비스는 [글로벌 피어 링](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)을 사용 하 여 해당 vnet가 연결 된 경우 다른 지역의 sql Managed Instance에 액세스할 수 없습니다. VNet 게이트웨이를 사용하여 ExpressRoute 또는 VNet 간을 통해 이러한 리소스에 연결할 수 있습니다.

### <a name="failover-groups"></a>장애 조치(failover) 그룹
시스템 데이터베이스는 장애 조치 (failover) 그룹의 보조 인스턴스에 복제 되지 않습니다. 따라서 시스템 데이터베이스의 개체에 종속 된 시나리오는 보조 인스턴스에서 개체를 수동으로 만들지 않는 한 보조 인스턴스에서는 불가능 합니다.

### <a name="failover-groups"></a>장애 조치(failover) 그룹
시스템 데이터베이스는 장애 조치 (failover) 그룹의 보조 인스턴스에 복제 되지 않습니다. 따라서 시스템 데이터베이스의 개체에 종속 된 시나리오는 보조 인스턴스에서 개체를 수동으로 만들지 않는 한 보조 인스턴스에서는 불가능 합니다.

### <a name="tempdb"></a>TEMPDB

`tempdb`의 최대 파일 크기는 범용 계층에서 코어당 24GB보다 클 수 없습니다. `tempdb`중요 비즈니스용 계층의 최대 크기는 SQL Managed Instance 저장소 크기에 의해 제한 됩니다. `Tempdb` 로그 파일 크기는 범용 계층에서 120GB로 제한됩니다. 일부 쿼리는 `tempdb`에서 코어당 24GB보다 많이 필요하거나 120GB보다 많이 로그 데이터를 생성하는 경우 오류를 반환할 수 있습니다.

### <a name="msdb"></a>MSDB

SQL Managed Instance의 다음 MSDB 스키마는 해당 하는 미리 정의 된 역할이 소유 해야 합니다.

- 일반 역할
  - TargetServersRole
- [고정 데이터베이스 역할](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [DatabaseMail 역할](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15#DBProfile):
  - DatabaseMailUserRole
- [Integration Services 역할](https://docs.microsoft.com/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15):
  - msdb
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> 고객이 미리 정의된 역할 이름, 스키마 이름 및 스키마 소유자를 변경하면 서비스의 정상적인 작동에 영향을 줍니다. 이러한 변경 내용은 정상적인 서비스 작업을 보장하기 위해 검색되는 즉시 또는 다음 최신 서비스 업데이트 시 다시 되돌려집니다.

### <a name="error-logs"></a>오류 로그

SQL Managed Instance는 오류 로그에 자세한 정보를 저장 합니다. 내부 시스템 이벤트는 오류 로그에 많이 기록됩니다. 관련이 없는 항목을 필터링하는 사용자 지정 프로시저를 사용하여 오류 로그를 읽습니다. 자세한 내용은 Azure Data Studio에 대 한 [sql Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/) 또는 [sql Managed Instance 확장 (미리 보기)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대 한 자세한 내용은 [sql Managed Instance 정의](sql-managed-instance-paas-overview.md) 를 참조 하세요.
- 기능 및 비교 목록은 [AZURE SQL Managed Instance 기능 비교](../database/features-comparison.md)를 참조 하세요.
- 릴리스 업데이트 및 알려진 문제 상태는 [SQL Managed Instance 릴리스 정보](../database/doc-changes-updates-release-notes.md) 를 참조 하세요.
- 새 SQL Managed Instance를 만드는 방법을 보여주는 빠른 시작은 [sql Managed Instance 만들기](instance-create-quickstart.md)를 참조 하세요.
