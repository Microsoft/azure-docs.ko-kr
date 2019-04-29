---
title: Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대한 문서 | Microsoft Docs
description: Azure Database for MySQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/23/2019
ms.openlocfilehash: 2c8a3f36e04fbedfdd127939d55fab376e3e6b30
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097762"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-postgresql"></a>Azure DB for PostgreSQL로의 온라인 마이그레이션과 관련된 알려진 문제/마이그레이션 제한 사항

다음 섹션에서는 PostgreSQL에서 Azure Database for PostgreSQL로의 온라인 마이그레이션과 관련된 알려진 문제 및 제한 사항에 대해 설명합니다. 

## <a name="online-migration-configuration"></a>온라인 마이그레이션 구성
- 원본 PostgreSQL Server는 버전 9.5.11, 9.6.7 또는 10.3 이상을 실행해야 합니다. 자세한 내용은 [지원되는 PostgreSQL 데이터베이스 버전](../postgresql/concepts-supported-versions.md) 문서를 참조하세요.
- 같은 버전의 마이그레이션만 지원됩니다. 예를 들어 PostgreSQL 9.5.11을 Azure Database for PostgreSQL 9.6.7로 마이그레이션할 수는 없습니다.

    > [!NOTE]
    > PostgreSQL 버전 10에 대 한 현재 DMS 지원 PostgreSQL 용 Azure 데이터베이스로 10.3 버전의 마이그레이션. 가능한 한 빨리 최신 버전의 PostgreSQL 지원 하도록 계획 합니다.

- **원본 PostgreSQL postgresql.conf** 파일에서 논리 복제를 사용하도록 설정하려면 다음 매개 변수를 설정합니다.
    - **wal_level** = logical
    - **max_replication_slots** = [마이그레이션할 데이터베이스의 최대 수]. 데이터베이스 4개를 마이그레이션하려면 값을 4로 설정합니다.
    - **max_wal_senders** = [동시에 실행되는 데이터베이스 수]. 권장 값은 10입니다.
- 원본 PostgresSQL pg_hba.conf에 DMS 에이전트 IP를 추가합니다.
    1. DMS 인스턴스 프로비전을 완료한 후 DMS IP 주소를 기록해 둡니다.
    2. 아래에 나와 있는 것처럼 pg_hba.conf 파일에 IP 주소를 추가합니다.

        host    all     172.16.136.18/10    md5  host    replication postgres    172.16.136.18/10    md5

- 사용자에게 원본 데이터베이스를 호스팅하는 서버에 대한 슈퍼 사용자 권한이 있어야 합니다.
- 원본 데이터베이스 스키마에 ENUM이 포함되어 있어야 할 뿐 아니라 원본 및 대상 데이터베이스 스키마가 일치해야 합니다.
- 대상 Azure Database for PostgreSQL의 스키마에는 외래 키가 없어야 합니다. 외래 키를 삭제하려면 다음 쿼리를 사용합니다.

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    쿼리 결과에서 외래 키 삭제(두 번째 열)를 실행합니다.

- 대상 Azure Database for PostgreSQL의 스키마에는 트리거가 없어야 합니다. 대상 데이터베이스에서 트리거를 사용하지 않도록 설정하려면 다음 명령을 사용합니다.

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>데이터 형식 제한 사항

- **제한 사항**: 원본 PostgreSQL 데이터베이스에서 ENUM 데이터 형식이 있는 경우 지속적인 동기화를 수행하는 동안 마이그레이션이 실패합니다.

    **해결 방법**: ENUM 데이터 형식을 Azure Database for PostgreSQL의 다른 문자로 수정합니다.

- **제한 사항**: 테이블에 기본 키가 없는 경우 지속적인 동기화가 실패합니다.

    **해결 방법**: 마이그레이션을 진행할 수 있도록 일시적으로 테이블에 대한 기본 키를 설정합니다. 데이터 마이그레이션이 완료된 후 기본 키를 제거할 수 있습니다.

## <a name="lob-limitations"></a>LOB 제한 사항
LOB(Large Object) 열은 커질 수 있는 열입니다. PostgreSQL의 경우 LOB 데이터 형식의 예로는 XML, JSON, IMAGE, TEXT 등이 있습니다.

- **제한 사항**: LOB 데이터 형식이 기본 키로 사용되는 경우 마이그레이션이 실패합니다.

    **해결 방법**: 기본 키를 LOB가 아닌 열이나 기타 데이터 형식으로 바꿉니다.

- **제한 사항**: LOB(Large Object) 열의 길이가 32KB보다 큰 경우 데이터는 대상에서 잘릴 수 있습니다. 이 쿼리를 사용하여 LOB 열의 길이를 확인할 수 있습니다.

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **해결 방법**: 32KB 보다 큰 LOB 개체에 있는 경우 엔지니어링 팀에 문의 [Azure 데이터베이스 마이그레이션을 요청](mailto:AskAzureDatabaseMigrations@service.microsoft.com)합니다.

- **제한 사항**: 테이블에 LOB 열이 있는데 테이블의 기본 키가 설정되어 있지 않으면 해당 테이블의 데이터가 마이그레이션되지 않을 수 있습니다.

    **해결 방법**: 마이그레이션을 진행할 수 있도록 일시적으로 테이블에 대한 기본 키를 설정합니다. 데이터 마이그레이션이 완료된 후 기본 키를 제거할 수 있습니다.

## <a name="postgresql10-workaround"></a>PostgreSQL10 해결 방법
PostgreSQL 10.x에서는 pg_xlog 폴더 이름이 여러 가지로 변경되므로 마이그레이션이 정상적으로 실행되지 않을 수도 있습니다. PostgreSQL 10.x에서 Azure Database for PostgreSQL 10.3으로 마이그레이션하는 경우 원본 PostgreSQL 데이터베이스에서 다음 스크립트를 실행하여 pg_xlog 함수용 래퍼 함수를 만듭니다.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="other-limitations"></a>기타 제한 사항
- 데이터베이스 이름은 세미콜론(;)을 포함할 수 없습니다.
- 여는 중괄호와 닫는 중괄호 {}를 포함하는 암호 문자열은 지원되지 않습니다. 이 제한 사항은 원본 PostgreSQL 및 대상 Azure Database for PostgreSQL에 연결할 때 모두 적용됩니다.
- 캡처된 테이블에는 기본 키가 있어야 합니다. 테이블에 기본 키가 없는 경우 DELETE 및 UPDATE 레코드 작업의 결과를 예측할 수 없습니다.
- 기본 키 세그먼트 업데이트는 무시됩니다. 이 경우 해당 업데이트를 적용하면 대상이 업데이트 작업을 모든 행을 업데이트하지 않은 업데이트로 식별하므로 예외 테이블에 레코드가 작성됩니다.
- 이름은 같고 대/소문자만 다른 여러 테이블(예: table1, TABLE1, Table1)을 마이그레이션하면 예기치 않은 동작이 발생할 수 있으므로 해당 마이그레이션은 지원되지 않습니다.
- [CREATE | ALTER | DROP] 테이블 DDL의 변경 처리는 내부 함수/프로시저 본문 블록이나 기타 중첩 구문에 포함되지 않은 경우에 한해 지원됩니다. 예를 들어 다음 변경은 캡처되지 않습니다.

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- TRUNCATE 작업의 변경 처리(지속적인 동기화)는 지원되지 않습니다. 분할된 테이블의 마이그레이션은 지원되지 않습니다. 분할된 테이블이 검색되면 다음 작업이 수행됩니다.
    - 데이터베이스가 부모 및 자식 테이블 목록을 보고합니다.
    - 선택한 테이블과 같은 속성을 사용하여 대상에 일반 테이블이 생성됩니다.
    - 원본 데이터베이스의 부모 테이블 기본 키가 자식 테이블과 같으면 “중복 키” 오류가 생성됩니다.
- DMS에서 하나의 단일 마이그레이션 작업에서 마이그레이션할 데이터베이스의 한계는 4개입니다.