---
title: Azure Database for MariaDB에 데이터를 복제하도록 입력 데이터 복제를 구성합니다.
description: 이 문서에서는 Azure Database for MariaDB에 대해 입력 데이터 복제를 설정하는 방법을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3897c402e45962836880ccebbeb252d189188d3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61038588"
---
# <a name="how-to-configure-azure-database-for-mariadb-data-in-replication"></a>Azure Database for MariaDB 입력 데이터 복제를 구성하는 방법

이 문서에서는 마스터 서버와 복제본 서버를 구성하여 Azure Database for MariaDB 서비스에서 입력 데이터 복제를 설정하는 방법을 알아봅니다. 입력 데이터 복제를 사용하면 다른 클라우드 공급자가 호스팅하는 가상 머신 또는 데이터베이스 서비스에서 온-프레미스를 실행하는 마스터 MariaDB 서버의 데이터를 Azure Database for MariaDB 서비스에 있는 복제본으로 동기화할 수 있습니다. 

이 문서에서는 이전에 MariaDB 서버 및 데이터베이스를 사용한 경험이 몇 번이라도 있다고 가정합니다.

## <a name="create-a-mariadb-server-to-be-used-as-replica"></a>복제본으로 사용할 MariaDB 서버 만들기

1. Azure Database for MariaDB 서버 만들기

   새 MariaDB 서버(예: "replica.mariadb.database.azure.com")를 만듭니다. 서버를 만드는 방법은 [Azure Portal을 사용하여 Azure Database for MariaDB 서버 만들기](quickstart-create-mariadb-server-database-using-azure-portal.md)를 참조하세요. 이 서버는 데이터 내부 복제의 “복제본” 서버입니다.

   > [!IMPORTANT]
   > Azure Database for MariaDB 서버는 범용 또는 메모리 최적화 가격 책정 계층에 생성되어야 합니다.
   > 

2. 동일한 사용자 계정 및 해당 권한 만들기

   사용자 계정은 마스터 서버에서 복제본 서버로 복제되지 않습니다. 복제본 서버에 대한 액세스 권한을 사용자에게 제공하려는 경우, 새로 만든 이 Azure Database for MariaDB 서버에서 모든 계정과 해당 권한을 수동으로 만들어야 합니다.

## <a name="configure-the-master-server"></a>마스터 서버 구성
다음 단계에서는 입력 데이터 복제를 위해 다른 클라우드 공급자가 호스팅하는 가상 머신 또는 데이터베이스 서비스에서 온-프레미스 호스팅 MariaDB 서버를 준비하고 구성합니다. 이 서버는 데이터 내부 복제의 “마스터” 서버입니다. 

1. 이진 로깅 켜기

   다음 명령을 실행하여 마스터 서버에서 이진 로깅을 사용할 수 있는지 확인합니다. 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) 변수가 “ON” 값으로 반환되면 서버에서 이진 로깅을 사용할 수 있는 것입니다. 

   `log_bin`이 “OFF” 값으로 반환되는 경우 `log_bin=ON`으로 my.cnf 파일을 편집하여 이진 로깅을 켜고 서버를 다시 시작하여 변경 내용을 적용합니다.

2. 마스터 서버 설정

   데이터 내부 복제를 사용하려면 마스터 서버와 복제본 서버 간에 `lower_case_table_names` 매개 변수가 일치해야 합니다. Azure Database for MariaDB에서 이 매개 변수는 기본적으로 1입니다. 

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. 새 복제 역할 만들기 및 권한 설정

   마스터 서버에서 복제 권한으로 구성된 사용자 계정을 만듭니다. 이 작업은 MySQL Workbench와 같은 도구 또는 SQL 명령을 통해 수행할 수 있습니다. 사용자를 만들 때 지정해야 하므로 SSL을 사용하여 복제할지 여부를 고려합니다. 마스터 서버에서 [사용자 계정을 추가](https://mariadb.com/kb/en/library/create-user/)하는 방법을 이해하려면 MariaDB 설명서를 참조하세요. 

   아래 명령에서 새로 생성된 복제 역할은 마스터 서버 자체를 호스트하는 머신뿐 아니라 모든 머신에서 마스터 서버에 액세스할 수 있습니다. 이 작업은 create user 명령에 “syncuser\@’%’”를 지정하여 수행합니다. [계정 이름 지정](https://mariadb.com/kb/en/library/create-user/#account-names)에 대한 자세한 내용은 MariaDB 설명서를 참조하세요.

   **SQL 명령**

   *SSL을 사용한 복제*

   모든 사용자 연결에 SSL이 필요하도록 설정하려면 다음 명령을 사용하여 사용자를 만듭니다. 

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *SSL 없이 복제*

   일부 연결에 SSL이 필요하지 않은 경우 다음 명령을 사용하여 사용자를 만듭니다.

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   MySQL Workbench에서 복제 역할을 만들려면 **관리** 패널에서 **사용자 및 권한** 패널을 엽니다. 그런 다음, **계정 추가**를 클릭합니다. 
 
   ![사용자 및 권한](./media/howto-data-in-replication/users_privileges.png)

   **로그인 이름** 필드에 사용자 이름을 입력합니다. 

   ![사용자 동기화](./media/howto-data-in-replication/syncuser.png)
 
   **관리 역할** 패널을 클릭하고 **전역 권한** 목록에서 **복제 슬레이브**를 선택합니다. 그런 다음, **적용** 을 클릭하여 복제 역할을 만듭니다.

   ![복제 슬레이브](./media/howto-data-in-replication/replicationslave.png)


4. 마스터 서버를 읽기 전용 모드로 설정

   데이터베이스를 덤프하기 전에 서버를 읽기 전용 모드로 설정해야 합니다. 읽기 전용 모드에서는 마스터 서버가 쓰기 트랜잭션을 처리할 수 없습니다. 비즈니스에 미치는 영향을 평가하고, 필요한 경우 사용량이 적은 시간에 읽기 전용 창을 예약합니다.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. 이진 로그 파일 이름 및 오프셋 가져오기

   [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) 명령을 실행하여 현재 이진 로그 파일 이름 및 오프셋을 확인합니다.
    
   ```sql
   show master status;
   ```
   결과는 다음과 같아야 합니다. 이후 단계에서 사용되므로 이진 파일 이름을 적어 두세요.

   ![마스터 상태 결과](./media/howto-data-in-replication/masterstatus.png)
 
## <a name="dump-and-restore-master-server"></a>마스터 서버 덤프 및 복원

1. 마스터 서버에서 모든 데이터베이스 덤프

   mysqldump를 사용하여 마스터 서버에서 데이터베이스를 덤프할 수 있습니다. 자세한 내용은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조하세요. MySQL 라이브러리 및 테스트 라이브러리는 덤프할 필요가 없습니다.

2. 마스터 서버를 읽기/쓰기 모드로 설정

   데이터베이스가 덤프되면 마스터 MariaDB 서버를 다시 읽기/쓰기 모드로 변경합니다.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. 덤프 파일을 새 서버로 복원

   덤프 파일을 Azure Database for MariaDB 서비스에서 생성된 서버로 복원합니다. 덤프 파일을 MariaDB 서버로 복원하는 방법은 [덤프 및 복원](howto-migrate-dump-restore.md)을 참조하세요. 덤프 파일이 큰 경우, 복제본 서버와 동일한 지역 내의 Azure 가상 머신에 업로드합니다. 가상 머신에서 Azure Database for MariaDB 서버로 복원합니다.

## <a name="link-master-and-replica-servers-to-start-data-in-replication"></a>마스터 서버와 복제본 서버를 연결하여 데이터 내부 복제 시작

1. 마스터 서버 설정

   모든 데이터 내부 복제 기능은 저장 프로시저에 의해 수행됩니다. [데이터 내부 복제 저장 프로시저](reference-data-in-stored-procedures.md)에서 모든 프로시저를 확인할 수 있습니다. 저장 프로시저는 MySQL 셸 또는 MySQL Workbench에서 실행할 수 있습니다.

   두 서버를 연결하고 복제를 시작하려면 Azure DB for MariaDB 서비스에서 대상 복제본 서버에 로그인하고 외부 인스턴스를 마스터 서버로 설정합니다. 이 작업은 Azure DB for MariaDB 서버의 `mysql.az_replication_change_master` 저장 프로시저를 사용하여 수행합니다.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: 마스터 서버의 호스트 이름
   - master_user: 마스터 서버의 사용자 이름
   - master_password: 마스터 서버의 암호
   - master_log_file: 실행 중인 `show master status`의 이진 로그 파일 이름
   - master_log_pos: 실행 중인 `show master status`의 이진 로그 위치
   - master_ssl_ca: CA 인증서의 컨텍스트. SSL을 사용하지 않는 경우 빈 문자열을 전달합니다.
       - 이 매개 변수를 변수로 전달하는 것이 좋습니다. 자세한 내용은 다음 예제를 참조하세요.

   **예**

   *SSL을 사용한 복제*

   `@cert` 변수는 다음 명령을 실행하여 만들어집니다. 

   ```sql
   SET @cert = '-----BEGIN CERTIFICATE-----
   PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
   -----END CERTIFICATE-----'
   ```

   SSL을 사용한 복제는 “companya.com” 도메인에 호스트된 마스터 서버와 Azure Database for MariaDB에 호스트된 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다. 

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
   ```
   *SSL 없이 복제*

   SSL을 사용하지 않는 복제는 “companya.com” 도메인에 호스트된 마스터 서버와 Azure Database for MariaDB에 호스트된 복제본 서버 간에 설정됩니다. 이 저장 프로시저는 복제본에서 실행됩니다.

   ```sql
   CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
   ```

2. 복제 시작

   `mysql.az_replication_start` 저장 프로시저를 호출하여 복제를 시작합니다.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. 복제 상태 확인

   복제본 서버에서 [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) 명령을 호출하여 복제 상태를 확인합니다.
    
   ```sql
   show slave status;
   ```

   `Slave_IO_Running` 및 `Slave_SQL_Running`의 상태가 “yes”이고 `Seconds_Behind_Master`의 값이 “0”이면 복제가 제대로 작동 중인 것입니다. `Seconds_Behind_Master`는 복제본이 얼마나 지연되었는지를 나타냅니다. 값이 “0”이 아니면 복제본이 업데이트를 처리 중인 것입니다. 

## <a name="other-stored-procedures"></a>기타 저장 프로시저

### <a name="stop-replication"></a>복제 중지

마스터 서버와 복제본 서버 간의 복제를 중지하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>복제 관계 제거

마스터 서버와 복제본 서버 간의 관계를 제거하려면 다음 저장 프로시저를 사용합니다.

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>복제 오류 건너뛰기

복제 오류를 건너뛰고 복제를 계속 진행하려면 다음 저장 프로시저를 사용합니다.
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>다음 단계
- Azure Database for MariaDB를 위한 [입력 데이터 복제](concepts-data-in-replication.md)에 대해 자세히 알아보세요.
