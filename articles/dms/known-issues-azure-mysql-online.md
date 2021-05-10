---
title: '알려진 문제: Azure Database for MySQL로 온라인 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용할 때 Azure Database for MySQL로의 온라인 마이그레이션 시 알려진 문제와 마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 11659bcbdf77d04c0f4e6f8bc7aca30c716fc924
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606892"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Azure Database Migration Service의 Azure DB for MySQL에 대한 온라인 마이그레이션 문제 및 제한 사항

MySQL에서 Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항은 다음 섹션에 설명되어 있습니다.

## <a name="online-migration-configuration"></a>온라인 마이그레이션 구성


- 원본 MySQL 서버 버전은 5.6.35, 5.7.18 이상이어야 합니다.
- Azure Database for MySQL은 다음을 지원합니다.
  - MySQL 커뮤니티 버전
  - InnoDB 엔진
- 동일한 버전 마이그레이션. Azure Database for MySQL 5.7로의 MySQL 5.6 마이그레이션은 지원되지 않습니다. MySQL 8.0으로의 마이그레이션은 지원되지 않습니다.
- my.ini(Windows) 또는 my.cnf(Unix)로 이진 로깅을 사용
  - Server_id를 1 이상의 숫자로 설정(예: Server_id=1(MySQL 5.6에만 해당))
  - log-bin = \<path>로 설정(MySQL 5.6에만 해당)
  - binlog_format = row로 설정
  - Expire_logs_days = 5(권장됨 - MySQL 5.6에만 해당)
- 사용자에게 ReplicationAdmin 역할이 있어야 함.
- 원본 MySQL 데이터베이스에 대해 정의된 데이터 정렬은 대상 Azure Database for MySQL에 정의된 것과 동일합니다.
- 스키마는 원본 MySQL 데이터베이스와 Azure Database for MySQL의 대상 데이터베이스 간에 일치해야 합니다.
- 대상 Azure Database for MySQL의 스키마에는 외래 키가 없어야 합니다. 외래 키를 삭제하려면 다음 쿼리를 사용합니다.
    ```sql
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

LOB(Large Object) 열은 크기가 커질 수 있는 열입니다. MySQL의 경우 Medium text, Longtext, Blob, Mediumblob, Longblob 등은 LOB의 일부 데이터 형식입니다.

- **제한 사항**: LOB 데이터 형식이 기본 키로 사용되는 경우 마이그레이션이 실패합니다.

    **해결 방법**: 기본 키를 다른 데이터 형식 또는 LOB가 아닌 열로 바꿉니다.

- **제한 사항**: LOB(Large Object) 열의 길이가 "LOB 크기 제한" 매개 변수(64KB보다 크지 않아야 함)보다 큰 경우 데이터가 대상에서 잘릴 수 있습니다. 이 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다.
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **해결 방법**: LOB 개체가 64KB보다 큰 경우 "무제한 Lob 크기 허용" 매개 변수를 사용합니다. "무제한 LOB 크기 허용" 매개 변수를 사용하는 마이그레이션은 "LOB 크기 제한" 매개 변수를 사용하는 마이그레이션보다 속도가 더 느립니다.

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>AWS RDS MySQL에서 온라인으로 마이그레이션할 때의 제한 사항

AWS RDS MySQL에서 Azure Database for MySQL로 온라인 마이그레이션을 수행하려고 할 때 다음 오류가 발생할 수 있습니다.

- **오류:** 데이터베이스 '{0}'의 대상에 외래 키가 있습니다. 대상을 수정하고 새로운 데이터 마이그레이션 작업을 시작하세요. 대상에서 아래 스크립트를 실행하여 외래 키를 나열하세요.

  **제한 사항**: 스키마에 외래 키가 있으면 마이그레이션의 초기 로드 및 지속적인 동기화가 실패합니다.
  **해결 방법**: MySQL Workbench에서 다음 스크립트를 실행하여 외래 키 삭제 스크립트를 추출하고 외래 키 스크립트를 추가합니다.

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **오류:** 데이터베이스 '{0}'이(가) 서버에 없습니다. 제공된 MySQL 원본 서버는 대/소문자를 구분합니다. 데이터베이스 이름을 확인하세요.

  **제한 사항**: CLI(명령줄 인터페이스)를 사용하여 MySQL 데이터베이스를 Azure로 마이그레이션할 때 이 오류가 발생할 수 있습니다. 서비스가 원본 서버에서 데이터베이스를 찾을 수 없습니다. 잘못된 데이터베이스 이름을 제공했거나 나열된 서버에 데이터베이스가 존재하지 않기 때문일 수 있습니다. 데이터베이스 이름은 대/소문자를 구분합니다.

  **해결 방법**: 정확한 데이터베이스 이름을 입력한 후 다시 시도하세요.

- **오류:** '{데이터베이스}' 데이터베이스에 이름이 같은 테이블이 있습니다. Azure Database for MySQL은 대/소문자 구분 테이블을 지원하지 않습니다.

  **제한 사항**: 이 오류는 원본 데이터베이스에 이름이 같은 두 개의 테이블이 있는 경우에 발생합니다. Azure Database for MySQL은 대/소문자 구분 테이블을 지원하지 않습니다.

  **해결 방법**: 테이블 이름을 고유한 이름으로 업데이트한 후 다시 시도하세요.

- **오류:** {데이터베이스} 대상 데이터베이스가 비어 있습니다. 스키마를 마이그레이션하세요.

  **제한 사항**: 이 오류는 대상 Azure Database for MySQL 데이터베이스에 필요한 스키마가 없는 경우에 발생합니다. 대상으로 데이터를 마이그레이션할 수 있도록 하려면 스키마 마이그레이션이 필요합니다.

  **해결 방법**: 원본 데이터베이스에서 대상 데이터베이스로 [스키마를 마이그레이션](./tutorial-mysql-azure-mysql-online.md#migrate-the-sample-schema)하세요.

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

- Azure Database Migration Service에서 하나의 단일 마이그레이션 작업에서 마이그레이션할 데이터베이스의 한계는 4개입니다.

- 부모 테이블에서 행이 삭제되거나 업데이트되는 경우 Azure DMS는 자식 테이블에서 일치하는 행을 자동으로 삭제하거나 업데이트하는 CASCADE 참조 작업을 지원하지 않습니다. 자세한 내용은 MySQL 설명서의 [외래 키 제약 조건](https://dev.mysql.com/doc/refman/8.0/en/create-table-foreign-keys.html) 문서에서 '참조 작업' 섹션을 참조하세요. Azure DMS를 사용하려면 초기 데이터 로드 중에 대상 데이터베이스 서버에서 외래 키 제약 조건을 삭제해야 하며, 참조 작업은 사용할 수 없습니다. 워크로드에서 이 참조 작업을 통해 관련 자식 테이블을 업데이트해야 하는 경우 [덤프 및 복원](../mysql/concepts-migrate-dump-restore.md)을 대신 수행하는 것이 좋습니다. 

- **오류:** 행 크기가 너무 큽니다(> 8126). 일부 열을 TEXT 또는 BLOB으로 변경하면 도움이 될 수 있습니다. 현재 행 형식에서 0바이트의 BLOB 접두사는 인라인으로 저장됩니다.

  **제한 사항**: 이 오류는 InnoDB 스토리지 엔진을 사용하여 Azure Database for MySQL로 마이그레이션할 때 테이블 행 크기가 너무 크면(8126바이트 초과) 발생합니다.

  **해결 방법**: 행 크기가 8126바이트보다 큰 테이블의 스키마를 업데이트합니다. 데이터가 잘리게 되므로 strict 모드를 변경하지 않는 것이 좋습니다. page_size 변경은 지원되지 않습니다.