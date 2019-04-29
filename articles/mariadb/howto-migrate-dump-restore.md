---
title: Azure Database for MariaDB에서 덤프 및 복원을 사용하여 MariaDB 데이터베이스 마이그레이션
description: 이 문서에서는 mysqldump, MySQL Workbench 및 PHPMyAdmin과 같은 도구를 사용하여 Azure Database for MariaDB에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bcb76fcbba02bf53b48cc462e3dad8f264db02ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745952"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>덤프 및 복원을 사용하여 MariaDB Database를 Azure Database for MariaDB로 마이그레이션
이 문서에서는 Azure Database for MariaDB에서 데이터베이스를 백업 및 복원하는 2가지 일반적인 방법에 대해 설명
- 명령줄에서 덤프 및 복원(mysqldump 사용) 
- PHPMyAdmin을 사용하여 덤프 및 복원

## <a name="before-you-begin"></a>시작하기 전에
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for MariaDB 서버 만들기 - Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) 명령줄 유틸리티가 컴퓨터에 설치되어 있어야 함
- 명령 덤프 및 복원을 수행할 MySQL Workbench [MySQL Workbench 다운로드](https://dev.mysql.com/downloads/workbench/), Toad, Navicat 또는 타사 MySQL 도구

## <a name="use-common-tools"></a>일반 도구 사용
MySQL Workbench, mysqldump, Toad 또는 Navicat과 같은 일반 유틸리티 및 도구를 사용하여 Azure Database for MariaDB에 원격으로 연결하고 데이터를 복원합니다. 인터넷에 연결된 클라이언트 머신에서 이러한 도구를 사용하여 Azure Database for MariaDB에 연결합니다. 최상의 보안을 위해 SSL 암호화 연결을 사용하려면 [Azure Database for MariaDB에서 SSL 연결 구성](concepts-ssl-connection-security.md)도 참조하세요. Azure Database for MariaDB로 마이그레이션할 때 덤프 파일을 특수한 클라우드 위치로 이동할 필요가 없습니다. 

## <a name="common-uses-for-dump-and-restore"></a>덤프 및 복원을 위한 일반적인 사용
몇 가지 일반적인 시나리오에서 mysqldump 및 mysqlpump와 같은 MySQL 유틸리티를 사용하여 데이터베이스를 Azure Database for MariaDB로 덤프 및 로드할 수 있습니다. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- 전체 데이터베이스를 마이그레이션할 때 데이터베이스 덤프를 사용합니다. 많은 양의 데이터를 이동하거나 실시간 사이트 또는 애플리케이션에 대한 서비스 중단을 최소화하려는 경우 이 권장 사항이 유지됩니다. 
-  데이터베이스의 모든 테이블이 데이터를 Azure Database for MariaDB로 로드할 때 InnoDB 저장소 엔진을 사용해야 합니다. Azure Database for MariaDB는 InnoDB 저장소 엔진만을 지원하므로 대체 저장소 엔진을 지원하지 않습니다. 테이블이 다른 저장소 엔진으로 구성된 경우 Azure Database for MariaDB로 마이그레이션하기 전에 해당 테이블을InnoDB 엔진 형식으로 변환합니다.
   예를 들어 MyISAM 테이블을 사용하는 WordPress 또는 WebApp이 있는 경우 Azure Database for MariaDB로 복원하기 전에 InnoDB 형식으로 마이그레이션하여 먼저 해당 테이블을 변환합니다. `ENGINE=InnoDB` 절을 사용하여 새 테이블을 만들 때 사용되는 엔진을 설정한 다음 복원 전에 데이터를 호환되는 테이블로 전송합니다. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- 호환성 문제를 방지하려면 데이터베이스를 덤프할 때 동일한 버전의 MariaDB가 원본 및 대상 시스템에서 사용되는지 확인합니다. 예를 들어 기존 MariaDB 서버가 버전 10.2이면 버전 10.2를 실행하도록 구성된 Azure Database for MariaDB로 마이그레이션해야 합니다. `mysql_upgrade` 명령은 Azure Database for MariaDB 서버에서 작동하지 않으며 지원되지 않습니다. MariaDB 버전 간에 업그레이드해야 하는 경우 먼저 사용자 환경에서 낮은 버전의 데이터베이스를 더 높은 버전의 MariaDB로 덤프하거나 내보냅니다. 그런 다음, Azure Database for MariaDB로 마이그레이션을 시도하기 전에 `mysql_upgrade`를 실행합니다.

## <a name="performance-considerations"></a>성능 고려 사항
성능을 최적화하려면 큰 데이터베이스를 덤프할 때 이러한 고려 사항을 숙지합니다.
-   데이터베이스를 덤프할 때 mysqldump에서 `exclude-triggers` 옵션을 사용합니다. 데이터 복원 중 발생하는 트리거 명령을 방지하기 위해 덤프 파일에서 트리거를 제외합니다. 
-   `single-transaction` 옵션을 사용하여 트랜잭션 격리 모드를 REPEATABLE READ로 설정하고 데이터를 덤프하기 전에 START TRANSACTION SQL 문을 서버로 보냅니다. 단일 트랜잭션 내에서 많은 테이블을 덤프하면 복원 중 저장소가 추가로 소비됩니다. LOCK TABLES는 보류 중인 트랜잭션을 암시적으로 커밋되도록 하기 때문에 `single-transaction` 옵션과 `lock-tables` 옵션은 상호 배타적입니다. 대형 테이블을 덤프하려면 `single-transaction` 옵션을 `quick` 옵션과 결합합니다. 
-   여러 VALUE 목록을 포함하는 `extended-insert` 여러 행 구문을 사용합니다. 그러면 덤프 파일이 작아지고 파일을 다시 로드할 때 삽입 속도가 빨라집니다.
-  데이터가 기본 키 순서로 스크립팅되도록 데이터베이스를 덤프할 때 mysqldump에서 `order-by-primary` 옵션을 사용합니다.
-   로드 전에 외래 키 제약 조건을 비활성화하려면 데이터를 덤프할 때 mysqldump에서 `disable-keys` 옵션을 사용합니다. 외래 키 검사 비활성화는 성능 향상을 제공합니다. 제약 조건을 활성화하고 참조 무결성을 확인하도록 로드 후 데이터를 확인합니다.
-   적절한 경우 분할된 테이블을 사용합니다.
-   병렬로 데이터를 로드합니다. 리소스 제한에 도달하도록 하는 너무 많은 병렬 처리를 피하고 Azure Portal에서 사용할 수 있는 메트릭을 사용하여 리소스를 모니터링합니다. 
-   테이블 데이터가 로드된 후 인덱스 생성이 발생하도록 데이터베이스를 덤프할 때 mysqlpump에서 `defer-table-indexes` 옵션을 사용합니다.
-   Azure blob/저장소에 백업 파일을 복사하고, 인터넷을 통해 복원을 수행할 때보다 훨씬 더 빨리 수행할 수 있는 위치에서 복원을 수행합니다.

## <a name="create-a-backup-file"></a>Backup 파일 만들기
로컬 온-프레미스 서버 또는 가상 머신에 기존 MariaDB 데이터베이스를 백업하려면 mysqldump을 사용하여 다음 명령을 실행합니다. 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

제공할 매개 변수:
- [uname] 데이터베이스 사용자 이름 
- [pass] 데이터베이스에 대한 암호(-p와 암호 사이를 띄어쓰지 말 것) 
- [dbname] 데이터베이스의 이름 
- [backupfile.sql] 데이터베이스 백업의 파일 이름 
- [-opt] mysqldump 옵션 

예를 들어 사용자 이름이 'testuser'이고 암호가 없는 MariaDB 서버에서 'testdb'라는 데이터베이스를 파일 testdb_backup.sql에 백업하려면 다음 명령을 사용합니다. 명령은 데이터베이스를 다시 만드는 데 필요한 모든 SQL 문을 포함하는 `testdb_backup.sql`이라는 파일로 `testdb` 데이터베이스를 백업합니다. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
백업할 데이터베이스의 특정 테이블을 선택하려면 테이블 이름을 공백으로 구분해서 나열합니다. 예를 들어 'testdb'에서 table1 및 table2 테이블만 백업하려면 다음 예제를 따릅니다. 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
한 번에 둘 이상의 데이터베이스를 백업하려면 --database 스위치를 사용하고 데이터베이스 이름을 공백으로 구분합니다. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
서버의 모든 데이터베이스를 한 번에 백업하려면 --all-databases 옵션을 사용해야 합니다.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>대상 서버에서 데이터베이스 만들기
데이터를 마이그레이션하려는 대상 Azure Database for MariaDB 서버에서 빈 데이터베이스를 만듭니다. MySQL Workbench, Toad 또는 Navicat과 같은 도구를 사용하여 데이터베이스를 만듭니다. 이 데이터베이스는 덤프된 데이터를 포함하는 데이터베이스와 이름이 같을 수 있고 다른 이름의 데이터베이스를 만들 수도 있습니다.

연결하려면 Azure Database for MariaDB의 **개요**에서 연결 정보를 찾습니다.

![Azure Portal에서 연결 정보 찾기](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

MySQL Workbench에 연결 정보를 추가합니다.

![MySQL Workbench 연결 문자열](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>MariaDB 데이터베이스 복원
대상 데이터베이스를 만든 후에는 mysql 명령 또는 MySQL Workbench를 사용하여 덤프 파일에서 새로 만든 특정 데이터베이스로 데이터를 복원할 수 있습니다.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
이 예제에서는 대상 Azure Database for MariaDB 서버에서 새로 만든 데이터베이스로 데이터를 복원합니다.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 내보내기
내보내려면 환경에 로컬로 이미 설치했을 수 있는 일반 도구 phpMyAdmin을 사용할 수 있습니다. PHPMyAdmin을 사용하여 MariaDB 데이터베이스를 내보내려면
1. phpMyAdmin을 엽니다.
2. 데이터베이스를 선택합니다. 왼쪽 목록에서 데이터베이스 이름을 클릭합니다. 
3. **내보내기** 링크를 클릭합니다. 데이터베이스의 덤프를 보는 새 페이지가 나타납니다.
4. 내보내기 영역에서 **모두 선택** 링크를 클릭하여 데이터베이스의 테이블을 선택합니다. 
5. SQL 옵션 영역에서 적절한 옵션을 클릭합니다. 
6. **파일로 저장** 옵션 및 해당 압축 옵션을 클릭하고 **이동** 단추를 클릭합니다. 파일을 로컬로 저장할지 묻는 대화 상자가 나타납니다.

## <a name="import-using-phpmyadmin"></a>PHPMyAdmin을 사용하여 가져오기
데이터베이스 가져오기는 내보내기와 비슷합니다. 다음 작업을 수행합니다.
1. phpMyAdmin을 엽니다. 
2. phpMyAdmin 설치 페이지에서 **추가**를 클릭하여 Azure Database for MariaDB 서버를 추가합니다. 연결 정보 및 로그인 정보를 제공합니다.
3. 적절하게 명명된 데이터베이스를 만들고 화면 왼쪽에서 선택합니다. 기존 데이터베이스를 다시 작성하려면 데이터베이스 이름을 클릭하고 테이블 이름 옆에 있는 모든 확인란을 선택하고 **삭제**를 선택하여 기존 테이블을 삭제합니다. 
4. **SQL** 링크를 클릭하여 SQL 명령을 입력하거나 SQL 파일을 업로드할 수 있는 페이지를 표시합니다. 
5. **찾아보기** 단추를 사용하여 데이터베이스 파일을 찾습니다. 
6. **이동** 단추를 클릭하여 백업을 내보내고 SQL 명령을 실행하고, 데이터베이스를 다시 만듭니다.

## <a name="next-steps"></a>다음 단계
- [Azure Database for MariaDB에 애플리케이션을 연결](./howto-connection-string.md)합니다.
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
