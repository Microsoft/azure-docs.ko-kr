---
title: Azure SQL Database 관리 되는 인스턴스 T-SQL 차이점 | Microsoft Docs
description: 이 문서에서는 Azure SQL Database의 Managed Instance 및 SQL Server 간의 T-SQL 차이점을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 03/13/2019
ms.custom: seoapril2019
ms.openlocfilehash: 5f476aa571ba2827cbe6f4e4f258545b5e9d3ba1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106364"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Azure SQL Database Managed Instance 및 SQL Server 간의 T-SQL 차이점

이 문서에서는 요약 하 고 구문과 동작에 Azure SQL Database 관리 되는 인스턴스 및 온-프레미스 SQL Server 데이터베이스 엔진 간의 차이점을 설명 합니다. <a name="Differences"></a>

- [Always-On](#always-on-availability) 및 [백업](#backup)의 차이점을 비롯한 [가용성](#availability),
- [감사](#auditing), [인증서](#certificates), [자격 증명](#credential), [암호화 공급자](#cryptographic-providers), [로그인 / 사용자](#logins--users), [서비스 키 및 서비스 마스터 키](#service-key-and-service-master-key)의 차이점을 비롯한 [보안](#security),
- [버퍼 풀 확장](#buffer-pool-extension), [데이터 정렬](#collation), [호환성 수준](#compatibility-levels),[데이터베이스 미러링](#database-mirroring), [데이터베이스 옵션](#database-options), [SQL Server 에이전트](#sql-server-agent), [테이블 옵션](#tables)의 차이점을 비롯한 [구성](#configuration),
- [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [분산 트랜잭션](#distributed-transactions), [확장 이벤트](#extended-events), [외부 라이브러리](#external-libraries), [파일 스트림 및 파일 테이블](#filestream-and-filetable), [전체 텍스트 의미 체계 검색](#full-text-semantic-search), [연결된 서버](#linked-servers), [Polybase](#polybase), [복제](#replication), [복원](#restore-statement), [Service Broker](#service-broker), [저장 프로시저, 함수 및 트리거](#stored-procedures-functions-triggers)를 비롯한 [기능](#functionalities),
- [Managed Instance에서 동작이 다른 기능](#Changes)
- [임시 제한 사항 및 알려진 문제](#Issues)

Managed Instance 배포 옵션은 온-프레미스 SQL Server 데이터베이스 엔진과의 높은 호환성을 제공합니다. 대부분의 SQL Server 데이터베이스 엔진 기능은 Managed Instance에서 지원됩니다.

![마이그레이션](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>가용성

### <a name="always-on-availability"></a>Always On

[고가용성](sql-database-high-availability.md)은 Managed Instance에 기본적으로 제공되며 사용자가 제어할 수 없습니다. 지원되지 않는 명령문은 다음과 같습니다.

- [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CREATE AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTER AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DROP AVAILABILITY GROUP](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql) 문의 [SET HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) 절

### <a name="backup"></a>Backup

관리되는 인스턴스에는 자동 백업이 있으며, 사용자가 전체 데이터베이스 `COPY_ONLY` 백업을 만들 수 있습니다. 차등, 로그 및 파일 스냅숏 백업은 지원되지 않습니다.

- Managed Instance를 사용하는 경우 Azure Blob Storage 계정에만 인스턴스 데이터베이스를 백업할 수 있습니다.
  - `BACKUP TO URL`만 지원됩니다.
  - `FILE`, `TAPE` 및 백업 디바이스는 지원되지 않습니다.  
- 대부분의 일반 `WITH` 옵션이 지원됩니다.
  - `COPY_ONLY`는 필수입니다.
  - `FILE_SNAPSHOT`은 지원되지 않습니다.
  - 테이프 옵션: `REWIND`, `NOREWIND`, `UNLOAD` 및 `NOUNLOAD`는 지원되지 않습니다.
  - 로그 관련 옵션: `NORECOVERY`, `STANDBY` 및 `NO_TRUNCATE`는 지원되지 않습니다.

 제한 사항:  

- Managed Instance를 사용하는 경우 최대 32개의 스트라이프가 있는 백업에 인스턴스 데이터베이스를 백업할 수 있습니다. 이 개수는 백업 압축을 사용할 경우 최대 4TB의 데이터베이스에 충분합니다.
- 사용 하 여 최대 백업 스트라이프 크기는 `BACKUP` 관리 되는 인스턴스에서 명령에는 195GB (최대 blob 크기)입니다. 백업 명령에서 스트라이프 수를 늘려 개별 스트라이프 크기를 줄이고 이 제한 내에서 유지합니다.

    > [!TIP]
    > 가상 머신 또는 온-프레미스 환경에서 SQL Server에서 데이터베이스를 백업 하는 경우이 제한을 해결 하려면 다음을 수행할 수 있습니다.
    >
    > - 에 대 한 백업 `DISK` 백업 하는 대신 `URL`
    > - Blob 저장소에 백업 파일 업로드
    > - 관리 되는 인스턴스로 복원 합니다.
    >
    > `Restore` 명령에는 관리 되는 인스턴스 저장소 업로드 된 백업 파일에 대 한 다른 blob 유형이 사용 되기 때문에 백업 파일에서 더 큰 blob 크기를 지원 합니다.

T-SQL을 사용하는 백업에 대한 자세한 내용은 [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql)을 참조하세요.

## <a name="security"></a>보안

### <a name="auditing"></a>감사

Azure SQL Database의 데이터베이스 및 SQL Server의 데이터베이스에서 감사 간의 주요 차이점은 다음과 같습니다.

- Azure SQL Database의 Managed Instance 배포 옵션을 사용하는 경우 감사는 서버 수준에서 작동하며 `.xel` 로그 파일이 Azure Blob 스토리지에 저장됩니다.
- Azure SQL Database의 단일 데이터베이스 및 탄력적 풀 배포 옵션을 사용하면 감사는 데이터베이스 수준에서 작동합니다.
- SQL Server 온-프레미스/가상 머신에서 감사는 서버 수준에서 작동하지만 파일 시스템/Windows 이벤트 로그에 이벤트를 저장합니다.
  
Managed Instance의 XEvent 감사는 Azure Blob Storage 대상을 지원합니다. 파일 및 Windows 로그는 지원되지 않습니다.

Azure Blob Storage에 대한 감사에서 `CREATE AUDIT` 구문의 주요 차이점은 다음과 같습니다.

- 새 `TO URL` 구문이 제공되고 `.xel` 파일이 배치될 Azure Blob Storage 컨테이너의 URL을 지정할 수 있습니다
- Managed Instance에서 Windows 파일 공유에 액세스할 수 없으므로 `TO FILE` 구문은 지원되지 않습니다.

자세한 내용은 다음을 참조하세요.  

- [CREATE SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)  
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [감사](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>인증서

Managed Instance는 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다.

- `CREATE FROM`/`BACKUP TO` 파일은 인증서에서 지원되지 않습니다.
- `FILE`/`ASSEMBLY`의 `CREATE`/`BACKUP` 인증서는 지원되지 않습니다. 개인 키 파일은 사용할 수 없습니다.  

[CREATE CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) 및 [BACKUP CERTIFICATE](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql)를 참조하세요.  
  
**해결 방법**: 다음과 같이 인증서/개인 키 스크립트를 작성하고, .sql 파일로 저장하고 이진 파일에서 만듭니다.

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>자격 증명

Azure Key Vault 및 `SHARED ACCESS SIGNATURE` ID만 지원됩니다. Windows 사용자는 지원되지 않습니다.

[CREATE CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) 및 [ALTER CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql)을 참조하세요.

### <a name="cryptographic-providers"></a>암호화 공급자

Managed Instance는 파일에 액세스할 수 없으므로 암호화 공급자를 만들 수 없습니다.

- `CREATE CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [CREATE CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql)를 참조하세요.
- `ALTER CRYPTOGRAPHIC PROVIDER`는 지원되지 않습니다. [ALTER CRYPTOGRAPHIC PROVIDER](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql)를 참조하세요.

### <a name="logins--users"></a>로그인/사용자

- `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` 및 `FROM SID`에서 만든 SQL 로그인이 지원됩니다. [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql)을 참조하세요.
- [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) 구문 또는 [CREATE USER FROM LOGIN [Azure AD Login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) 구문을 사용하여 만든 Azure AD(Azure Active Directory) 서버 보안 주체(로그인)가 지원됩니다(**공개 미리 보기**). 이 보안 주체는 서버 수준에서 만든 로그인입니다.

    Managed Instance는 `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` 구문을 사용하여 Azure AD 데이터베이스 보안 주체를 지원합니다. 이 보안 주체를 Azure AD 포함된 데이터베이스 사용자라고도 합니다.

- `CREATE LOGIN ... FROM WINDOWS` 구문으로 만든 Windows 로그인은 지원되지 않습니다. Azure Active Directory 로그인 및 사용자를 사용합니다.
- 인스턴스를 만든 Azure AD 사용자에게는 [무제한 관리자 권한](sql-database-manage-logins.md#unrestricted-administrative-accounts)이 있습니다.
- 관리자가 아닌 Azure AD(Azure Active Directory) 데이터베이스 수준 사용자는 `CREATE USER ... FROM EXTERNAL PROVIDER` 구문을 사용하여 만들 수 있습니다. [CREATE USER ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users)를 참조하세요.
- Azure AD 서버 보안 주체(로그인)는 단일 MI 인스턴스 내에서만 SQL 기능을 지원합니다. 동일한 Azure AD 테넌트든, 다른 테넌트든 관계없이 인스턴스 간 상호 작용이 필요한 기능은 Azure AD 사용자에 대해 지원되지 않습니다. 이러한 기능의 예는 다음과 같습니다.

  - SQL 트랜잭션 복제 및
  - 링크 서버

- Azure AD 그룹에 매핑된 Azure AD 로그인을 데이터베이스 소유자로 설정할 수 없습니다.
- [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql) 절과 같이, 다른 Azure AD 보안 주체를 사용하여 Azure AD 서버 수준 보안 주체를 가장할 수 있습니다. EXECUTE AS 제한 사항:

  - 이름이 로그인 이름과 다른 경우 EXECUTE AS USER는 Azure AD 사용자에 대해 지원되지 않습니다. 예를 들어 CREATE USER [myAadUser] FROM LOGIN [john@contoso.com]을 통해 사용자를 만들고 EXEC AS USER = _myAadUser_를 통해 가장을 시도하는 경우입니다. Azure AD 서버 보안 주체(로그인)에서 **USER**를 만들 때 **LOGIN**에서 user_name을 동일한 login_name으로 지정합니다.
  - `sysadmin` 역할에 포함된 SQL 서버 수준 보안 주체(로그인)만이 Azure AD 보안 주체를 대상으로 하는 다음 작업을 실행할 수 있습니다.

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Azure AD 서버 보안 주체(로그인)에 대한 **공개 미리 보기** 제한 사항:

  - Managed Instance에 대한 Active Directory 관리자 제한 사항:

    - Managed Instance를 설정하는 데 사용된 Azure AD 관리자는 Managed Instance 내에 Azure AD 서버 보안 주체(로그인)를 만드는 데 사용할 수 없습니다. `sysadmin` SQL Server 계정을 사용하여 첫 번째 Azure AD 서버 보안 주체(로그인)를 만들어야 합니다. 이는 Azure AD 서버 보안 주체(로그인)가 GA(일반 공급)되면 제거될 일시적인 제한입니다. Azure AD 관리자 계정을 사용하여 로그인을 만들려고 시도하면 `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.` 오류가 표시됩니다.
      - 현재 master DB에 생성되는 첫 번째 Azure AD 로그인은 `sysadmin`인 표준 SQL Server 계정(Azure AD 아님)이 [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) FROM EXTERNAL PROVIDER를 사용해서 만들어야 합니다. GA 후에는 이 제한 사항이 제거되며, Managed Instance에 대한 Active Directory 관리자가 초기 Azure AD 로그인을 만들 수 있습니다.
    - SSMS(SQL Server Management Studio) 또는 SqlPackage에서 사용되는 DacFx(내보내기/가져오기)는 Azure AD 로그인에 대해 지원되지 않습니다. 이 제한 사항은 Azure AD 서버 보안 주체(로그인)가 GA되면 제거됩니다.
    - SSMS에서 Azure AD 서버 보안 주체(로그인) 사용

      - Azure AD 로그인(인증된 로그인 사용)을 스크립팅할 수 없습니다.
      - IntelliSense에서 **CREATE LOGIN FROM EXTERNAL PROVIDER** 문을 인식할 수 없어 빨간색 밑줄을 표시합니다.

- 서버 수준 보안 주체 로그인(Managed Instance 프로비저닝 프로세스를 통해 생성됨), 서버 역할의 멤버(`securityadmin` 또는 `sysadmin`) 또는 서버 수준에서 ALTER ANY LOGIN 권한이 있는 기타 로그인만 Managed Instance에 대한 Azure AD 서버 보안 주체(로그인)를 master 데이터베이스에 만들 수 있습니다.
- 로그인이 SQL 보안 주체인 경우 `sysadmin` 역할에 포함된 로그인만 create 명령을 사용하여 Azure AD 계정에 대한 로그인을 만들 수 있습니다.
- Azure AD 로그인은 Azure SQL Managed Instance에 사용되는 것과 동일한 디렉터리 내의 Azure AD 멤버여야 합니다.
- Azure AD 서버 보안 주체(로그인)는 SSMS 18.0 미리 보기 5부터 개체 탐색기에 표시됩니다.
- Azure AD 서버 보안 주체(로그인)를 Azure AD 관리자 계정과 겹칠 수 있습니다. 보안 주체를 확인하고 Managed Instance에 대한 사용 권한을 적용할 때 Azure AD 서버 보안 주체(로그인)가 Azure AD 관리자보다 우선합니다.
- 인증 중에 인증 보안 주체를 확인하기 위해 적용되는 시퀀스는 다음과 같습니다.

    1. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 경우(sys.server_principals에서 ‘E’ 형식으로), 액세스 권한을 부여하고 Azure AD 서버 보안 주체(로그인)의 사용 권한을 적용합니다.
    2. Azure AD 계정이 Azure AD 서버 보안 주체(로그인)에 직접 매핑된 Azure AD 그룹의 멤버인 경우(sys.server_principals에서 ‘X’ 형식으로), 액세스 권한을 부여하고 Azure AD 그룹 로그인의 사용 권한을 적용합니다.
    3. Azure AD 계정이 포털에서 구성된 Managed Instance에 대한 특수 Azure AD 관리자인 경우(Managed Instance 시스템 뷰에 없음), Managed Instance(레거시 모드)에 대해 Azure AD 관리자의 수정된 특수 사용 권한을 적용합니다.
    4. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 직접 매핑된 경우(sys.database_principals에서 ‘E’ 형식으로), 액세스 권한을 부여하고 Azure AD 데이터베이스 사용자의 사용 권한을 적용합니다.
    5. Azure AD 계정이 데이터베이스의 Azure AD 사용자에 매핑된 Azure AD 그룹의 멤버인 경우(sys.database_principals에서 ‘X’ 형식으로), 액세스 권한을 부여하고 Azure AD 그룹 로그인의 사용 권한을 적용합니다.
    6. Azure AD 사용자 계정 또는 Azure AD 그룹 계정에 매핑되어 사용자 인증으로 확인되는 Azure AD 로그인이 있는 경우, 이 Azure AD 로그인의 모든 사용 권한이 적용됩니다.

### <a name="service-key-and-service-master-key"></a>서비스 키 및 서비스 마스터 키

- [마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 백업](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.
- [서비스 마스터 키 복원](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql)(SQL Database 서비스에서 관리)은 지원되지 않습니다.

## <a name="configuration"></a>구성

### <a name="buffer-pool-extension"></a>버퍼 풀 확장

- [버퍼 풀 확장](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension)은 지원되지 않습니다.
- `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`은 지원되지 않습니다. [ALTER SERVER CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql)을 참조하세요.

### <a name="collation"></a>Collation

기본 인스턴스 데이터 정렬은 `SQL_Latin1_General_CP1_CI_AS`이며 생성 매개 변수로 지정할 수 있습니다. [데이터 정렬](https://docs.microsoft.com/sql/t-sql/statements/collations)을 참조하세요.

### <a name="compatibility-levels"></a>호환성 수준

- 지원되는 호환성 수준: 100, 110, 120, 130, 140  
- 100 미만의 호환성 수준은 지원되지 않습니다.
- 새 데이터베이스의 기본 호환성 수준은 140입니다. 복원된 데이터베이스의 경우 호환성 수준이 100 이상이면 변경되지 않고 그대로 유지됩니다.

[ALTER DATABASE 호환성 수준](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level)을 참조하세요.

### <a name="database-mirroring"></a>데이터베이스 미러링

데이터베이스 미러링은 지원되지 않습니다.

- `ALTER DATABASE SET PARTNER` 및 `SET WITNESS` 옵션은 지원되지 않습니다.
- `CREATE ENDPOINT … FOR DATABASE_MIRRORING`은 지원되지 않습니다.

자세한 내용은 [ALTER DATABASE SET PARTNER 및 SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) 및 [CREATE ENDPOINT … FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)을 참조하세요.

### <a name="database-options"></a>데이터베이스 옵션

- 다중 로그 파일은 지원되지 않습니다.
- 메모리 내 개체는 범용 서비스 계층에서 지원되지 않습니다.  
- 데이터베이스당 최대 280 개 파일을 의미 하는 범용 인스턴스당 280 개 파일의 제한이 됩니다. 데이터와 로그 파일 일반적 용도 계층은이 제한에 대해 계산 됩니다. [중요 비즈니스용 계층 데이터베이스 별로 32,767 파일 지원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)합니다.
- 파일 스트림 데이터가 있는 파일 그룹을 데이터베이스에 포함할 수 없습니다.  .bak에 `FILESTREAM` 데이터가 포함되어 있으면 복원에 실패합니다.  
- 모든 파일은 Azure Blob Storage에 배치됩니다. 파일별 IO 및 처리량은 각 개별 파일의 크기에 따라 달라집니다.  

#### <a name="create-database-statement"></a>CREATE DATABASE 문

`CREATE DATABASE` 제한 사항은 다음과 같습니다.

- 파일 및 파일 그룹을 정의할 수 없습니다.  
- `CONTAINMENT` 옵션은 지원되지 않습니다.  
- `WITH` 옵션은 지원되지 않습니다.  
   > [!TIP]
   > 해결 방법으로, `CREATE DATABASE` 뒤에 `ALTER DATABASE`를 사용하여 파일을 추가하거나 포함을 설정하는 데이터베이스 옵션을 설정합니다.  

- `FOR ATTACH` 옵션은 지원되지 않습니다.
- `AS SNAPSHOT OF` 옵션은 지원되지 않습니다.

자세한 내용은 [CREATE DATABASE](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql)를 참조하세요.

#### <a name="alter-database-statement"></a>ALTER DATABASE 문

일부 파일 속성은 설정하거나 변경할 수 없습니다.

- `ALTER DATABASE ADD FILE (FILENAME='path')` T-SQL 문에서 파일 경로를 지정할 수 없습니다. Managed Instance에서 파일을 자동으로 저장하기 때문에 스크립트에서 `FILENAME`을 제거합니다.  
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

자세한 내용은 [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options)를 참조하세요.

### <a name="sql-server-agent"></a>SQL Server 에이전트

- SQL 에이전트 설정은 읽기 전용입니다. `sp_set_agent_properties` 프로시저는 Managed Instance에서 지원되지 않습니다.  
- 교육
  - T-SQL 작업 단계가 지원됩니다.
  - 다음 복제 작업이 지원됩니다.
    - 트랜잭션 로그 판독기
    - 스냅숏
    - 배포자
  - SSIS 작업 단계가 지원됩니다.
  - 다음을 포함한 다른 유형의 작업 단계는 현재 지원되지 않습니다.
    - 병합 복제 작업 단계는 지원되지 않습니다.  
    - 큐 판독기는 지원되지 않습니다.  
    - 명령 셸은 아직 지원되지 않습니다.
  - Managed Instance는 외부 리소스(예: robocopy를 통한 네트워크 공유)에 액세스할 수 없습니다.  
  - Analysis Services는 지원되지 않습니다.
- 알림은 부분적으로 지원됩니다.
- 이메일 알림이 지원되며, 데이터베이스 메일 프로필을 구성해야 합니다. SQL 에이전트는 하나의 데이터베이스 메일 프로필을 사용할 수 있으며 호출 해야 `AzureManagedInstance_dbmail_profile`합니다.  
  - 호출기는 지원되지 않습니다.  
  - NetSend는 지원되지 않습니다.
  - 경고는 아직 지원되지 않습니다.
  - 프록시는 지원되지 않습니다.  
- Eventlog는 지원되지 않습니다.

현재 지원되지 않지만 나중에 사용할 수 있을 예정인 기능은 다음과 같습니다.

- 프록시
- 유휴 CPU에 대한 작업 예약
- 에이전트 사용 설정/해제
- 경고

SQL Server 에이전트에 대한 자세한 내용은 [SQL Server 에이전트](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent)를 참조하세요.

### <a name="tables"></a>테이블

지원되지 않는 항목은 다음과 같습니다.

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED`  

테이블 만들기 및 변경에 대한 자세한 내용은 [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 및 [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)을 참조하세요.

## <a name="functionalities"></a>기능

### <a name="bulk-insert--openrowset"></a>대량 삽입/openrowset

Managed Instance에서 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 Azure Blob Storage에서 파일을 가져와야 합니다.

- Azure Blob Storage에서 파일을 가져오는 동안 `BULK INSERT` 명령에 `DATASOURCE`가 필요합니다. [BULK INSERT](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql)를 참조하세요.
- Azure Blob Storage에서 파일의 내용을 읽을 때 `OPENROWSET` 함수에 `DATASOURCE`가 필요합니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.

### <a name="clr"></a>CLR

Managed Instance에서 파일 공유 및 Windows 폴더에 액세스할 수 없으므로 다음과 같은 제약 조건이 적용됩니다.

- `CREATE ASSEMBLY FROM BINARY`만 지원됩니다. [CREATE ASSEMBLY FROM BINARY](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)를 참조하세요.  
- `CREATE ASSEMBLY FROM FILE`는 지원되지 않습니다. [CREATE ASSEMBLY FROM FILE](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql)을 참조하세요.
- `ALTER ASSEMBLY`는 파일을 참조할 수 없습니다. [ALTER ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql)를 참조하세요.

### <a name="dbcc"></a>DBCC

SQL Server에서 사용하도록 설정되었지만 문서화되지 않은 DBCC 문은 Managed Instance에서 지원되지 않습니다.

- `Trace Flags`는 지원되지 않습니다. [추적 플래그](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql)를 참조하세요.
- `DBCC TRACEOFF`는 지원되지 않습니다. [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql)를 참조하세요.
- `DBCC TRACEON`은 지원되지 않습니다. [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)을 참조하세요.

### <a name="distributed-transactions"></a>분산 트랜잭션

MSDTC도, [탄력적 트랜잭션](sql-database-elastic-transactions-overview.md)도 현재 Managed Instance에서 지원되지 않습니다.

### <a name="extended-events"></a>확장 이벤트

XEvent에 대한 일부 Windows 관련 대상은 지원되지 않습니다.

- `etw_classic_sync target`은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [etw_classic_sync 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target)을 참조하세요.
- `event_file target`은 지원되지 않습니다. Azure Blob Storage에 `.xel` 파일을 저장합니다. [event_file 대상](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target)을 참조하세요.

### <a name="external-libraries"></a>외부 라이브러리

데이터베이스 내 R 및 Python 외부 라이브러리는 아직 지원되지 않습니다. [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services)를 참조하세요.

### <a name="filestream-and-filetable"></a>Filestream 및 Filetable

- 파일 스트림 데이터가 지원되지 않습니다.
- `FILESTREAM` 데이터가 있는 파일 그룹을 데이터베이스에 포함할 수 없습니다.
- `FILETABLE`은 지원되지 않습니다.
- 테이블에 `FILESTREAM` 형식을 사용할 수 없습니다.
- 지원되지 않는 함수는 다음과 같습니다.
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

자세한 내용은 [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) 및 [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server)를 참조하세요.

### <a name="full-text-semantic-search"></a>전체 텍스트 의미 체계 검색

[의미 체계 검색](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server)은 지원되지 않습니다.

### <a name="linked-servers"></a>연결된 서버

Managed Instance의 연결된 서버는 제한된 개수의 대상을 지원합니다.

- 지원되는 대상: SQL Server 및 SQL Database
- 지원되지 않는 대상: 파일, Analysis Services 및 기타 RDBMS

작업

- 인스턴스 간 쓰기 트랜잭션은 지원되지 않습니다.
- `sp_dropserver`는 연결된 서버를 삭제하는 데 지원됩니다. [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql)를 참조하세요.
- `OPENROWSET` 함수는 SQL Server 인스턴스(관리되는 인스턴스, 온-프레미스 인스턴스 또는 Virtual Machines 중 하나)에서만 쿼리를 실행하는 데 사용할 수 있습니다. [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)를 참조하세요.
- `OPENDATASOURCE` 함수는 SQL Server 인스턴스(관리되는 인스턴스, 온-프레미스 인스턴스 또는 Virtual Machines 중 하나)에서만 쿼리를 실행하는 데 사용할 수 있습니다. `SQLNCLI`, `SQLNCLI11` 및 `SQLOLEDB` 값만 공급자로 지원됩니다. 예: `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee` [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)를 참조하세요.

### <a name="polybase"></a>Polybase

HDFS 또는 Azure Blob Storage의 파일을 참조하는 외부 테이블은 지원되지 않습니다. Polybase에 대한 자세한 내용은 [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)를 참조하세요.

### <a name="replication"></a>복제

복제는 Managed Instance의 공개 미리 보기에 사용할 수 있습니다. 복제에 대한 자세한 내용은 [SQL Server 복제](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance)를 참조하세요.

### <a name="restore-statement"></a>RESTORE 문

- 지원되는 구문
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- 지원되지 않는 구문
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- 원본  
  - `FROM URL`(Azure Blob Storage)만 지원되는 옵션입니다.
  - `FROM DISK`/`TAPE`/백업 디바이스는 지원되지 않습니다.
  - 백업 세트는 지원되지 않습니다.
- `WITH` 옵션은 지원되지 않습니다(`DIFFERENTIAL`, `STATS` 등 없음).
- `ASYNC RESTORE` - 클라이언트 연결이 중단되더라도 복원이 계속됩니다. 연결이 삭제된 경우 `sys.dm_operation_status` 보기에서 복원 작업의 상태를 확인할 수 있습니다(CREATE 및 DROP 데이터베이스의 경우도 동일함). 자세한 내용은 [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database)를 참조하세요.  

설정/재정의되고 나중에 변경할 수 없는 데이터베이스 옵션은 다음과 같습니다.  

- `NEW_BROKER`(.bak 파일에서 broker를 사용할 수 없는 경우)  
- `ENABLE_BROKER`(.bak 파일에서 broker를 사용할 수 없는 경우)  
- `AUTO_CLOSE=OFF`(.bak 파일의 데이터베이스에 `AUTO_CLOSE=ON`이 있는 경우)  
- `RECOVERY FULL`(.bak 파일의 데이터베이스에 `SIMPLE` 또는 `BULK_LOGGED` 복구 모드가 있는 경우)
- 원본 .bak 파일에 없는 경우 메모리 최적화 파일 그룹이 추가되고 XTP라고 합니다.  
- 기존의 모든 메모리 최적화 파일 그룹의 이름이 XTP로 바뀝니다.  
- `SINGLE_USER` 및 `RESTRICTED_USER` 옵션이 `MULTI_USER`로 변환됩니다.

 제한 사항:  

- 여러 백업 세트가 있는 `.BAK` 파일은 복원할 수 없습니다.
- 여러 로그 파일이 있는 `.BAK` 파일은 복원할 수 없습니다.
- .bak에 `FILESTREAM` 데이터가 포함되어 있으면 복원에 실패합니다.
- 일반 목적 인스턴스의 활성 메모리 내 개체에 있는 데이터베이스를 포함 하는 백업은 복원할 수 없습니다.  
복원 명령문에 대한 자세한 내용은 [RESTORE 문](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)을 참조하세요.

### <a name="service-broker"></a>Service Broker

인스턴스 간 서비스 broker는 지원되지 않습니다.

- `sys.routes` - 필수 조건: sys.routes에서 주소를 선택합니다. 주소는 모든 경로에서 LOCAL이어야 합니다. [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql)를 참조하세요.
- `CREATE ROUTE` - `LOCAL` 이외의 `ADDRESS`가 포함된 `CREATE ROUTE`는 사용할 수 없습니다. [CREATE ROUTE](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql)를 참조하세요.
- `ALTER ROUTE`는 `LOCAL` 이외의 `ADDRESS`가 포함된 `ALTER ROUTE`를 수행할 수 없습니다. [ALTER ROUTE](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql)를 참조하세요.  

### <a name="stored-procedures-functions-triggers"></a>저장 프로시저, 함수, 트리거

- `NATIVE_COMPILATION` 일반적인 용도 계층에서 지원 되지 않습니다.
- 지원되지 않는 [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql) 옵션은 다음과 같습니다.
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- `sp_execute_external_scripts`는 지원되지 않습니다. [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples)를 참조하세요.
- `xp_cmdshell`은 지원되지 않습니다. [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql)을 참조하세요.
- `sp_addextendedproc` 및 `sp_dropextendedproc`를 포함하여 `Extended stored procedures`는 지원되지 않습니다. [확장 저장 프로시저](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql)를 참조하세요.
- `sp_attach_db`, `sp_attach_single_file_db` 및 `sp_detach_db`는 지원되지 않습니다. [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) 및 [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql)를 참조하세요.

## <a name="Changes"></a> 동작 변경

다른 결과를 반환하는 변수, 함수 및 뷰는 다음과 같습니다.

- `SERVERPROPERTY('EngineEdition')`는 8 값을 반환합니다. 이 속성은 Managed Instance를 고유하게 식별합니다. [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- SQL Server에 대한 인스턴스 개념은 Managed Instance에 적용되지 않으므로 `SERVERPROPERTY('InstanceName')`에서 NULL을 반환합니다. [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql)를 참조하세요.
- `@@SERVERNAME`은 전체 DNS '연결 가능한' 이름을 반환합니다(예: my-managed-instance.wcus17662feb9ce98.database.windows.net). [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql)을 참조하세요.  
- `SYS.SERVERS` - 'name' 및 'data_source' 속성에 대한 전체 DNS '연결 가능한' 이름을 반환합니다(예: `myinstance.domain.database.windows.net`). [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql)를 참조하세요.
- SQL Server에 대한 서비스 개념은 Managed Instance에 적용되지 않으므로 `@@SERVICENAME`에서 NULL을 반환합니다. [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql)을 참조하세요.
- `SUSER_ID`가 지원됩니다. Azure AD 로그인이 sys.syslogins에 없는 경우 NULL을 반환합니다. [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql)를 참조하세요.  
- `SUSER_SID`는 지원되지 않습니다. 잘못된 데이터를 반환합니다(알려진 임시 문제). [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql)를 참조하세요.

## <a name="Issues"> </a> 알려진 문제 및 제한 사항

### <a name="tempdb-size"></a>TEMPDB 크기

최대 파일 크기인 `tempdb` 일반적인 용도 계층에서 24GB/core 보다 클 수 없습니다. 최대 `tempdb` 중요 비즈니스 계층의 크기는 인스턴스 저장소 크기를 제한 합니다. `tempdb` 항상 12 데이터 파일로 분할 됩니다. 이 파일당 최대 크기는 변경할 수 없으며, 새 파일을 `tempdb`에 추가할 수 있습니다. 24GB 이상 필요한 경우 일부 쿼리는 오류를 반환할 수 있습니다에 핵심 / `tempdb`합니다.

### <a name="cannot-restore-contained-database"></a>포함 된 데이터베이스를 복원할 수 없습니다.

관리 되는 인스턴스를 복원할 수 없습니다 [포함 된 데이터베이스](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases)합니다. 관리 되는 인스턴스에서 기존 포함 된 데이터베이스의 지정 시간 복원이 작동 하지 않습니다. 이 문제는 곧 제거 될 예정 및 그동안 containment 옵션 관리 되는 인스턴스에서 배치 되 고 프로덕션 데이터베이스에 대 한 포함 옵션을 사용 하지 않으면 데이터베이스에서 제거할 것이 좋습니다.

### <a name="exceeding-storage-space-with-small-database-files"></a>작은 데이터베이스 파일이 포함된 저장소 공간 초과

`CREATE DATABASE`하십시오 `ALTER DATABASE ADD FILE`, 및 `RESTORE DATABASE` 인스턴스는 Azure 저장소 용량 한도 도달 수 있기 때문에 문이 실패할 수 있습니다.

각 일반 용도 관리 되는 인스턴스가 최대 35TB의 저장소가 Azure Premium 디스크 공간을 예약 하 고 각 데이터베이스 파일은 별도 물리적 디스크에 배치 합니다. 디스크 크기는 128GB, 256GB, 512GB, 1TB 또는 4TB일 수 있습니다. 디스크의 사용되지 않는 공간은 변경될 수 있지만 Azure Premium 디스크 크기의 총 합계는 35TB를 초과할 수 없습니다. 경우에 따라 총 8TB가 필요 없는 Managed Instance는 내부 조각화로 인해 저장소 크기에 대한 35TB Azure 제한을 초과할 수 있습니다.

예를 들어, 일반 용도 관리 되는 인스턴스 하나가 있을 수 있습니다 1.2TB 4TB 디스크에 배치 되는 크기의 248 개의 파일 (각 크기가 1GB) 별도 128GB 디스크에 배치 되는 파일입니다. 이 예제에서:

- 전체 할당된 디스크 스토리지 크기는 1x4TB + 248x128GB = 35TB입니다.
- 인스턴스에서 데이터베이스에 대해 예약된 총 공간은 1x1.2TB + 248x1GB = 1.4TB입니다.

여기서는 특정 상황에서 구체적인 파일의 배포로 인해 Managed Instance는 예상치 못한 연결된 Azure Premium Disk에 대해 예약된 35TB 용량에 도달할 수 있음을 보여줍니다.

이 예제에서 기존 데이터베이스는 계속 작동하며, 새 파일이 추가되지 않으면 문제 없이 커질 수 있습니다. 그러나 모든 데이터베이스의 총 크기가 인스턴스 크기 제한에 도달하지 않더라도 새 디스크 드라이브에 대한 충분한 공간이 없기 때문에 새 데이터베이스를 만들거나 복원할 수 없습니다. 이 경우 반환되는 오류가 명확하지 않습니다.

할 수 있습니다 [나머지 파일 수가 식별](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) 시스템 뷰를 사용 하 여 합니다. 이 제한 하려고 도달 하는 경우 [빈 데이터 요소 및 일부 DBCC SHRINKFILE 문을 사용 하 여 더 작은 파일을 삭제](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) 하거나 전환 [이 제한 되지 않은 중요 비즈니스 계층](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics)합니다.

### <a name="incorrect-configuration-of-sas-key-during-database-restore"></a>데이터베이스 복원 중 잘못된 SAS 키 구성

.bak 파일을 읽는 `RESTORE DATABASE`는 .bak 파일을 읽으려고 계속 시도하고, `CREDENTIAL`의 공유 액세스 서명이 올바르지 않으면 오랜 시간이 지난 후에 오류를 반환할 수 있습니다. SAS 키가 올바른지 확인하기 위해 데이터베이스를 복원하기 전에 RESTORE HEADERONLY를 실행합니다.
Azure Portal을 사용하여 생성된 SAS 키에서 선행 `?`를 제거했는지 확인합니다.

### <a name="tooling"></a>도구

Managed Instance에 액세스하는 동안 SSMS(SQL Server Management Studio) 및 SSDT(SQL Server Data Tools)에서 몇 가지 문제가 발생했을 수 있습니다.

- 현재 SSDT에서는 Azure AD 서버 보안 주체(로그인) 및 사용자(**공개 미리 보기**)를 사용할 수 없습니다.
- Azure AD 서버 보안 주체(로그인) 및 사용자에 대한 스크립팅(**공개 미리 보기**)은 SSMS에서 지원되지 않습니다.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>일부 뷰, 로그 및 메시지에 잘못된 데이터베이스 이름이 있음

몇 가지 시스템 뷰, 성능 카운터, 오류 메시지, XEvent 및 오류 로그 항목에는 실제 데이터베이스 이름 대신 GUID 데이터베이스 식별자가 표시됩니다. 이러한 GUID 식별자는 나중에 실제 데이터베이스 이름으로 바뀔 수 있으므로 사용하지 마세요.

### <a name="database-mail"></a>데이터베이스 메일

`@query` 매개 변수 [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) 프로시저 작동 하지 않습니다.

### <a name="database-mail-profile"></a>데이터베이스 메일 프로필

SQL 에이전트에서 사용 하는 데이터베이스 메일 프로필을 호출 해야 `AzureManagedInstance_dbmail_profile`합니다. 다른 데이터베이스 메일 프로필 이름에 대 한 제한이 있습니다.

### <a name="error-logs-are-not-persisted"></a>오류 로그가 유지되지 않음

Managed Instance에서 사용할 수 있는 오류 로그는 유지되지 않으며, 해당 크기는 최대 스토리지 한도에 포함되지 않습니다. 오류 로그는 장애 조치 시 자동으로 지워질 수 있습니다.

### <a name="error-logs-are-verbose"></a>오류 로그에 자세한 정보가 표시됨

Managed Instance는 자세한 정보를 오류 로그에 배치하지만 대부분은 관련이 없습니다. 오류 로그의 정보 양은 앞으로 줄어듭니다.

**해결 방법**: 관련 없는 일부 항목을 필터링한 오류 로그를 읽는 사용자 지정 프로시저를 사용합니다. 자세한 내용은 [Managed Instance – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/)를 참조하세요.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>동일한 인스턴스 내의 두 데이터베이스에 대한 트랜잭션 범위가 지원되지 않음

`TransactionScope` .net에서 클래스는 동일한 트랜잭션 범위에서 같은 인스턴스 내에서 두 데이터베이스에 두 개의 쿼리를 보내는 경우 작동 하지 않습니다.

```C#
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

이 코드는 동일한 인스턴스 내의 데이터를 사용하지만 MSDTC가 필요합니다.

**해결 방법**: [SqlConnection.ChangeDatabase(String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase)를 사용하여 두 연결을 사용하는 대신 연결 컨텍스트에서 다른 데이터베이스를 사용합니다.

### <a name="clr-modules-and-linked-servers-sometime-cant-reference-local-ip-address"></a>CLR 모듈 및 연결된 서버에서 로컬 IP 주소를 참조할 수 없는 경우가 있음

현재 인스턴스를 참조하는 Managed Instance 및 연결된 서버/분산 쿼리에 배치된 CLR 모듈에서 로컬 인스턴스의 IP를 확인할 수 없는 경우가 있습니다. 이 오류는 일시적인 문제입니다.

**해결 방법**: 가능한 경우 CLR 모듈에서 컨텍스트 연결을 사용합니다.

### <a name="tde-encrypted-databases-with-service-managed-key-dont-support-user-initiated-backups"></a>서비스 관리 키를 사용 하 여 TDE로 암호화 된 데이터베이스 사용자가 시작한 백업을 지원 하지 않습니다.

실행할 수 없습니다 `BACKUP DATABASE ... WITH COPY_ONLY` 서비스 관리 투명 한 데이터 암호화 (TDE) 암호화 된 데이터베이스입니다. 서비스 관리 TDE 백업을 내부 TDE 키를 사용 하 여 암호화 되도록 하 고 백업을 복원할 수 없습니다 있도록 키를 내보낼 수 없습니다.

**해결 방법**: 자동 백업 및 지정 시간 복원을 사용 하거나 사용 하 여 [고객 관리 (BYOK) TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) 대신 또는 데이터베이스에서 암호화를 사용 하지 않도록 설정 합니다.

## <a name="next-steps"></a>다음 단계

- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- 기능 및 비교 목록은 [SQL 일반 기능](sql-database-features.md)을 참조하세요.
- 새 Managed Instance를 만드는 방법을 보여 주는 빠른 시작은 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)를 참조하세요.
