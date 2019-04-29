---
title: SQL 데이터베이스 감사 로그 형식 | Microsoft Docs
description: SQL Database 감사 로그를 구성 하는 방법을 이해 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: 0fefe01e413e30e4aa3c1fa90de77cbdece39c38
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61417391"
---
# <a name="sql-database-audit-log-format"></a>SQL 데이터베이스 감사 로그 형식

[Azure SQL Database 감사](sql-database-auditing.md) 데이터베이스 이벤트를 Azure storage 계정의 감사 로그 기록 추적 또는 다운스트림 처리 및 분석을 위해 이벤트 허브 또는 Log Analytics로 보냅니다.

## <a name="naming-conventions"></a>명명 규칙

### <a name="blob-audit"></a>Blob 감사

라는 컨테이너에 저장 된 Blob 저장소에 저장 된 감사 로그 `sqldbauditlogs` Azure Storage 계정에 있습니다. 컨테이너 내의 디렉터리 계층 구조는 `<ServerName>/<DatabaseName>/<AuditName>/<Date>/`합니다. Blob 파일 이름 형식은 `<CreationTime>_<FileNumberInSession>.xel`, 여기서 `CreationTime` 는 UTC `hh_mm_ss_ms` 형식으로 및 `FileNumberInSession` 세션 여러 Blob 파일 범위를 기록 하는 경우 실행 하는 인덱스는 합니다.

데이터베이스에 대 한 예를 들어 `Database1` 에서 `Server1` 다음은 가능한 유효 경로:

    Server1/Database1/SqlDbAuditing_ServerAudit_NoRetention/2019-02-03/12_23_30_794_0.xel

### <a name="event-hub"></a>이벤트 허브

감사 구성 하는 동안 정의 된 본문에 기록 되는 네임 스페이스 및 이벤트 허브에 감사 이벤트를 쓰는 [Apache Avro](https://avro.apache.org/) 이벤트 utf-8 인코딩을 사용 하 여 JSON 형식을 사용 하 여 저장 합니다. 감사 로그를 읽으려면 [Avro 도구](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) 또는 이 형식을 처리하는 유사한 도구를 사용할 수 있습니다.

### <a name="log-analytics"></a>Log Analytics

감사 이벤트는 감사 구성 중에 정의 된 Log Analytics 작업 영역에 기록 합니다 `AzureDiagnostics` 범주를 사용 하 여 테이블 `SQLSecurityAuditEvents`합니다. Log Analytics 검색 언어 및 명령에 대한 유용한 추가 정보는 [Log Analytics 검색 참조](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search)를 참조하세요.

## <a id="subheading-1"></a>감사 로그 필드

| 이름 (Blob) | 이름 (이벤트 허브/Log Analytics) | 설명 | Blob 유형 | 이벤트 허브/Log Analytics 유형 |
|-------------|---------------------------------|-------------|-----------|-------------------------------|
| action_id | action_id_s | 동작의 ID입니다. | varchar(4) | 문자열 |
| action_name | action_name_s | 작업의 이름 | N/A | 문자열 |
| additional_information | additional_information_s | XML로 저장 하는 이벤트에 대 한 추가 정보 | nvarchar(4000) | 문자열 |
| affected_rows | affected_rows_d | 쿼리에서 영향을 받는 행 수 | bigint | int |
| application_name | application_name_s| 클라이언트 응용 프로그램의 이름 | nvarchar(128) | 문자열 |
| audit_schema_version | audit_schema_version_d | 항상 1 | int | int |
| class_type | class_type_s | 감사가 수행 하는 감사 가능한 엔터티의 형식 | varchar(2) | 문자열 |
| class_type_desc | class_type_description_s | 감사가 수행 하는 감사 가능한 엔터티의 설명 | N/A | 문자열 |
| client_ip | client_ip_s | 원본 IP는 클라이언트 응용 프로그램 | nvarchar(128) | 문자열 |
| connection_id | N/A | 서버에서 연결의 ID | GUID | N/A |
| data_sensitivity_information | data_sensitivity_information_s | 정보 유형 및 데이터베이스에 분류 된 열을 기반으로 감사 쿼리에서 반환 되는 민감도 레이블. 자세한 내용은 [Azure SQL Database 데이터 검색 및 분류](sql-database-data-discovery-and-classification.md) | nvarchar(4000) | 문자열 |
| database_name | database_name_s | 작업이 발생 한 데이터베이스 컨텍스트 | sysname | 문자열 |
| database_principal_id | database_principal_id_d | 작업을 수행한 데이터베이스 사용자 컨텍스트의 ID | int | int |
| database_principal_name | database_principal_name_s | 작업이 수행 되는 데이터베이스 사용자 컨텍스트의 이름 | sysname | 문자열 |
| duration_milliseconds | duration_milliseconds_d | 쿼리 실행 시간 (밀리초) | bigint | int |
| event_time | event_time_t | 감사 가능한 동작이 발생 하는 시간과 날짜 | datetime2 | Datetime |
| host_name | N/A | 클라이언트 호스트 이름 | 문자열 | N/A |
| is_column_permission | is_column_permission_s | 열 수준 사용 권한 임을 나타내는 플래그입니다. 1 = true, 0 = false | bit | 문자열 |
| N/A | is_server_level_audit_s | 이 감사 서버 수준에 있는 경우를 나타내는 플래그입니다. | N/A | 문자열 |
| object_ id | object_id_d | 감사가 수행 하는 엔터티의 ID입니다. 여기에: 서버 개체, 데이터베이스, 데이터베이스 개체 및 스키마 개체입니다. 개체 수준에서 수행 엔터티가 서버 자체 이면 또는 감사에는 없는 경우 0 | int | int |
| object_name | object_name_s | 감사가 수행된 대상 엔터티의 이름입니다. 여기에: 서버 개체, 데이터베이스, 데이터베이스 개체 및 스키마 개체입니다. 개체 수준에서 수행 엔터티가 서버 자체 이면 또는 감사에는 없는 경우 0 | sysname | 문자열 |
| permission_bitmask | permission_bitmask_s | 해당되는 경우 부여, 거부 또는 취소된 사용 권한을 표시합니다. | varbinary(16) | 문자열 |
| response_rows | response_rows_d | 결과 집합에 반환 된 행 수 | bigint | int |
| schema_name | schema_name_s | 동작이 수행된 스키마 컨텍스트입니다. 스키마 외부에서 발생 하는 감사에 대 한 NULL | sysname | 문자열 |
| N/A | securable_class_type_s | 감사할 class_type에 매핑되는 보안 개체 | N/A | 문자열 |
| sequence_group_id | sequence_group_id_g | 고유 식별자 | varbinary | GUID |
| sequence_number | sequence_number_d | 이 너무 커서 감사에 대 한 쓰기 버퍼에 맞지 않는 단일 감사 레코드는 레코드의 시퀀스를 추적 합니다. | int | int |
| server_instance_name | server_instance_name_s | 감사가 수행 된 서버 인스턴스의 이름 | sysname | 문자열 |
| server_principal_id | server_principal_id_d | 동작을 수행한 로그인 컨텍스트의 ID | int | int |
| server_principal_name | server_principal_name_s | 현재 로그인 | sysname | 문자열 |
| server_principal_sid | server_principal_sid_s | 현재 로그인 SID | varbinary | 문자열 |
| session_id | session_id_d | 이벤트가 발생 한 세션의 ID | smallint | int |
| session_server_principal_name | session_server_principal_name_s | 세션에 대 한 서버 보안 주체 | sysname | 문자열 |
| 문 | statement_s | T-SQL 문 (있는 경우) 실행 | nvarchar(4000) | 문자열 |
| 성공함 | succeeded_s | 이벤트를 트리거한 작업에 성공 했는지 여부를 나타냅니다. 로그인 및 일괄 처리 이외의 이벤트에 대 한만 사용 권한 검사의 성공 또는 실패의 경우 작업이 아닌 여부를 보고 합니다. 1 = 성공, 0 = 실패 | bit | 문자열 |
| target_database_principal_id | target_database_principal_id_d | 데이터베이스 보안 주체에 GRANT/DENY/REVOKE 작업이 수행 됩니다. 적용할 수 없는 경우 0 | int | int |
| target_database_principal_name | target_database_principal_name_s | 작업의 대상 사용자입니다. 적용할 수 없는 경우 NULL | 문자열 | 문자열 |
| target_server_principal_id | target_server_principal_id_d | GRANT/DENY/REVOKE 작업이 수행 되는 서버 보안 주체입니다. 적용할 수 없는 경우 0을 반환 합니다. | int | int |
| target_server_principal_name | target_server_principal_name_s | 작업의 대상 로그인입니다. 적용할 수 없는 경우 NULL | sysname | 문자열 |
| target_server_principal_sid | target_server_principal_sid_s | 대상 로그인의 SID입니다. 적용할 수 없는 경우 NULL | varbinary | 문자열 |
| transaction_id | transaction_id_d | SQL Server만 (2016부터 시작)-Azure SQL DB에 대 한 0 | bigint | int |
| user_defined_event_id | user_defined_event_id_d | 사용자 정의 이벤트 id sp_audit_write에 인수로 전달 합니다. 시스템 이벤트 (기본값)에 대 한 NULL 및 0이 아닌 사용자 정의 이벤트에 대 한 합니다. 자세한 내용은 참조 하세요. [sp_audit_write (TRANSACT-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | smallint | int |
| user_defined_information | user_defined_information_s | 사용자 정의 sp_audit_write 인수로 전달 된 정보입니다. 시스템 이벤트 (기본값)에 대 한 NULL 및 0이 아닌 사용자 정의 이벤트에 대 한 합니다. 자세한 내용은 참조 하세요. [sp_audit_write (TRANSACT-SQL)](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-audit-write-transact-sql) | nvarchar(4000) | 문자열 |

## <a name="next-steps"></a>다음 단계

에 대해 자세히 알아보세요 [Azure SQL Database 감사](sql-database-auditing.md)합니다.