---
title: '알려진 문제: Azure Database for MySQL에 대한 온라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service 사용 시 Azure Database for MySQL에 대한 온라인 마이그레이션과의 알려진 문제 및 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: 905a14bd78d120e27ebc4d44ede86e9f0e3783cf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437852"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Azure Database Migration Service에서 MySQL 용 Azure DB에 대한 제한 사항을 & 온라인 마이그레이션 문제

MySQL에서 Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="online-migration-configuration"></a>온라인 마이그레이션 구성


- 원본 MySQL 서버 버전은 5.6.35, 5.7.18 이상이어야 합니다.
- Azure Database for MySQL은 다음을 지원합니다.
  - MySQL 커뮤니티 버전
  - InnoDB 엔진
- 동일한 버전 마이그레이션. Azure Database for MySQL 5.7로의 MySQL 5.6 마이그레이션은 지원되지 않습니다.
- my.ini(Windows) 또는 my.cnf(Unix)로 이진 로깅을 사용
  - Server_id를 1 이상의 숫자로 설정(예: Server_id=1(MySQL 5.6에만 해당))
  - Set log-bin = \<path > (MySQL 5.6에만 해당)
  - binlog_format = row로 설정
  - Expire_logs_days = 5(권장됨 - MySQL 5.6에만 해당)
- 사용자에게 ReplicationAdmin 역할이 있어야 함.
- 원본 MySQL 데이터베이스에 대해 정의된 데이터 정렬은 대상 Azure Database for MySQL에 정의된 것과 동일합니다.
- 스키마는 원본 MySQL 데이터베이스와 Azure Database for MySQL의 대상 데이터베이스 간에 일치해야 합니다.
- 대상 Azure Database for MySQL의 스키마에는 외래 키가 없어야 합니다. 외래 키를 삭제하려면 다음 쿼리를 사용합니다.
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.
- 대상 Azure Database for MySQL의 스키마에는 트리거가 없어야 합니다. 대상 데이터베이스에서 트리거를 삭제하려면:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

- **제한 사항**: 원본 MySQL 데이터베이스에서 JSON 데이터 형식이 있는 경우 지속적인 동기화를 수행하는 동안 마이그레이션이 실패합니다.

    **해결 방법**: JSON 데이터 형식을 원본 MySQL 데이터베이스의 medium text 또는 Longtext로 수정합니다.

- **제한 사항**: 테이블에 기본 키가 없는 경우 지속적인 동기화가 실패합니다.

    **해결 방법**: 마이그레이션을 계속할 수 있도록 일시적으로 테이블에 대한 기본 키를 설정합니다. 데이터 마이그레이션이 완료된 후 기본 키를 제거할 수 있습니다.

## <a name="lob-limitations"></a>LOB 제한 사항

LOB(Large Object) 열은 크기가 커질 수 있는 열입니다. MySQL의 경우 중간 텍스트, Longtext, Blob, Mediumblob, 고도 Blob 등은 LOB의 일부 데이터 형식입니다.

- **제한 사항**: LOB 데이터 형식이 기본 키로 사용되는 경우 마이그레이션이 실패합니다.

    **해결 방법**: 기본 키를 다른 데이터 형식 또는 LOB가 아닌 열로 바꿉니다.

- **제한 사항**: LOB(Large Object) 열의 길이가 32KB보다 큰 경우 데이터는 대상에서 잘릴 수 있습니다. 이 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다.
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **해결 방법**: LOB 개체가 32 KB 보다 큰 경우 엔지니어링 팀에 문의 하 여 [Azure 데이터베이스 마이그레이션](mailto:AskAzureDatabaseMigrations@service.microsoft.com)에 문의 하세요.

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>AWS RDS MySQL에서 온라인으로 마이그레이션하는 경우의 제한 사항

AWS RDS MySQL에서 Azure Database for MySQL로 온라인 마이그레이션을 수행 하려고 할 때 다음과 같은 오류가 발생할 수 있습니다.

- **오류:** '{0}' 데이터베이스의 대상에 외래 키가 있습니다. 대상을 수정하고 새로운 데이터 마이그레이션 작업을 시작하세요. 대상에서 아래 스크립트를 실행 하 여 외래 키를 나열 합니다.

  **제한**사항: 스키마에 외래 키가 있는 경우 마이그레이션의 초기 로드 및 연속 동기화가 실패 합니다.
  **해결 방법**: MySQL 워크 벤치에서 다음 스크립트를 실행 하 여 drop foreign key 스크립트를 추출 하 고 외래 키 스크립트를 추가 합니다.

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **오류:** 데이터베이스 '{0}'이 (가) 서버에 없습니다. 제공된 MySQL 원본 서버는 대/소문자를 구분합니다. 데이터베이스 이름을 확인하세요.

  **제한**사항: CLI (명령줄 인터페이스)를 사용 하 여 MySQL 데이터베이스를 Azure로 마이그레이션하는 경우 사용자에 게이 오류가 발생할 수 있습니다. 서비스가 원본 서버에서 데이터베이스를 찾을 수 없습니다 .이는 잘못 된 데이터베이스 이름을 제공 했을 수도 있고 나열 된 서버에 데이터베이스가 존재 하지 않기 때문일 수 있습니다. 참고 데이터베이스 이름은 대/소문자를 구분 합니다.

  **해결 방법**: 정확한 데이터베이스 이름을 입력 한 후 다시 시도 하십시오.

- **오류:** ' {Database} ' 데이터베이스에 이름이 같은 테이블이 있습니다. Azure Database for MySQL은 대/소문자 구분 테이블을 지원하지 않습니다.

  **제한**사항:이 오류는 원본 데이터베이스에 이름이 같은 테이블이 두 개 있을 때 발생 합니다. Azure Database for MySQL는 대/소문자를 구분 하는 테이블을 지원 하지 않습니다.

  **해결 방법**: 테이블 이름을 고유한 이름으로 업데이트 한 후 다시 시도 하십시오.

- **오류:** 대상 데이터베이스 {database}이 (가) 비어 있습니다. 스키마를 마이그레이션하세요.

  **제한**사항:이 오류는 대상 Azure Database for MySQL 데이터베이스에 필요한 스키마가 없는 경우에 발생 합니다. 대상으로 데이터를 마이그레이션할 수 있도록 하려면 스키마 마이그레이션이 필요 합니다.

  **해결 방법**: 원본 데이터베이스에서 대상 데이터베이스로 [스키마를 마이그레이션합니다](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) .

## <a name="other-limitations"></a>기타 제한 사항

- 암호 문자열의 시작과 끝에 여는 중괄호 및 닫는 중괄호 {  }가 있는 암호 문자열은 지원되지 않습니다. 이 제한 사항은 원본 MySQL 및 대상 Azure Database for MySQL에 모두 적용됩니다.
- 다음 DDL은 지원되지 않습니다.
  - 모든 파티션 DDL
  - 테이블 삭제
  - 테이블 이름 변경
- *alter table <table_name> add column <column_name>* 문을 사용하여 열을 테이블의 시작 또는 중간에 추가하는 것은 지원되지 않습니다. *alter table <table_name> add column <column_name>* 은 열을 테이블의 끝에 추가합니다.
- 열 데이터의 일부에서만 생성된 인덱스는 지원되지 않습니다. 다음 문은 열 데이터의 일부만 사용하여 인덱스를 만드는 예제입니다.

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- Azure Database Migration Service 하나의 단일 마이그레이션 작업에서 마이그레이션할 데이터베이스의 제한은 4 개입니다.

- **오류:** 행 크기가 너무 깁니다 (> 8126). 일부 열을 텍스트 또는 BLOB으로 변경 하면 도움이 될 수 있습니다. 현재 행 형식에서 0 바이트의 BLOB 접두사는 인라인으로 저장 됩니다.

  **제한**사항:이 오류는 InnoDB 저장소 엔진을 사용 하 여 Azure Database for MySQL로 마이그레이션할 때 테이블 행 크기가 너무 큰 경우에 발생 합니다 (> 8126 bytes).

  **해결 방법**: 행 크기가 8126 바이트 보다 큰 테이블의 스키마를 업데이트 합니다. 데이터가 잘릴 수 있으므로 strict 모드를 변경 하지 않는 것이 좋습니다. Page_size 변경은 지원 되지 않습니다.
