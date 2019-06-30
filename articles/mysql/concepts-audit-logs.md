---
title: MySQL 용 Azure Database에 대 한 감사 로그
description: MySQL 및 로깅 수준을 사용 하도록 설정 하는 것에 대 한 사용 가능한 매개 변수에 대 한 Azure Database에서 사용할 수 있는 감사 로그를 설명 합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: a82afe6f5299609fd6dd57a54f04f49fad5d2268
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357637"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>MySQL 용 Azure Database에서 감사 로그

Azure Database for MySQL에서는 감사 로그의 사용자에 게 제공 합니다. 감사 로그를 사용 하 여 데이터베이스 수준 활동을 추적할 수 있습니다 및 규정 준수에 대 한 일반적으로 사용 됩니다.

> [!IMPORTANT]
> 감사 로그 기능은 현재 미리 보기로 제공에서 됩니다.

## <a name="configure-audit-logging"></a>감사 로깅 구성

감사 로그는 기본적으로 사용 하지 않도록 설정 합니다. 를 사용 하도록 설정 `audit_log_enabled` on입니다.

조정할 수 있는 다른 매개 변수는 다음과 같습니다.

- `audit_log_events`: 로깅할 이벤트를 제어 합니다. 특정 감사 이벤트에 대 한 테이블 아래를 참조 하세요.
- `audit_log_exclude_users`: MySQL 사용자 로깅에서 제외 될 수 있습니다. 최대 4 명의 사용자가 있습니다. 매개 변수의 최대 길이 256 자입니다.

| **Event** | **설명** |
|---|---|
| `CONNECTION` | -(성공 또는 실패) 연결 시작 <br> 다른 사용자/암호를 사용 하 여 세션 중 사용자 재인증 <br> 연결 종료 |
| `DML_SELECT`| SELECT 쿼리 |
| `DML_NONSELECT` | INSERT/DELETE/UPDATE 쿼리 |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | "DROP DATABASE"와 같은 쿼리 |
| `DCL` | "권한 부여"와 같은 쿼리 |
| `ADMIN` | "상태 표시"와 같은 쿼리 |
| `GENERAL` | DML_SELECT, DML_NONSELECT, DML, DDL, DCL, 및 관리자 모두에 |
| `TABLE_ACCESS` | -MySQL 5.7 에서만 사용 가능 <br> 테이블에 SELECT 또는 INSERT INTO 문과 읽기... SELECT <br> 테이블에 DELETE 또는 TRUNCATE TABLE 등의 문에서 삭제 <br> 삽입 또는 교체와 같은 테이블 insert 문 <br> 테이블 업데이트 문을 업데이트와 같은 |

## <a name="access-audit-logs"></a>감사 로그 액세스

감사 로그는 Azure Monitor 진단 로그와 통합 됩니다. MySQL 서버에서 감사 로그를 설정한 후에 Azure Monitor 로그, Event Hubs 또는 Azure 저장소로 내보낼 수 있습니다. Azure portal에서 진단 로그를 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 참조는 [감사 로그에 대 한 포털 문서](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs)합니다.

## <a name="diagnostic-logs-schemas"></a>진단 로그 스키마

다음 섹션에서는 출력 MySQL 감사 로그 이벤트 유형을 기반으로 하는 새로운 기능을 설명 합니다. 포함되는 필드와 이러한 필드가 표시되는 순서는 출력 방법에 따라 달라질 수 있습니다.

### <a name="connection"></a>연결

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `connection_log` |
| `event_subclass` | `CONNECT`하십시오 `DISCONNECT`, `CHANGE USER` (MySQL 5.7에 대 한 사용 가능) |
| `connection_id` | MySQL에서 생성 된 고유 연결 ID |
| `host` | 비어 있음 |
| `ip` | MySQL에 연결 하는 클라이언트의 IP 주소 |
| `user` | 쿼리를 실행 하는 사용자의 이름 |
| `db` | 연결할 데이터베이스의 이름 |
| `\_ResourceId` | 리소스 URI |

### <a name="general"></a>일반

아래의 스키마 일반, DML_SELECT, DML_NONSELECT, DML, DDL, DCL, 및 관리 이벤트 유형에 적용 됩니다.

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `general_log` |
| `event_subclass` | `LOG`하십시오 `ERROR`, `RESULT` (MySQL 5.6에 대해 사용 가능) |
| `event_time` | 쿼리는 UNIX 타임 스탬프에 시간 (초) 시작 |
| `error_code` | 쿼리 실패 한 경우 오류 코드입니다. `0` 의미 없는 오류 |
| `thread_id` | 쿼리를 실행 하는 스레드의 ID |
| `host` | 비어 있음 |
| `ip` | MySQL에 연결 하는 클라이언트의 IP 주소 |
| `user` | 쿼리를 실행 하는 사용자의 이름 |
| `sql_text` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

### <a name="table-access"></a>테이블 액세스

| **속성** | **설명** |
|---|---|
| `TenantId` | 테넌트 ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` [UTC] | UTC에 로그가 기록된 때의 타임스탬프 |
| `Type` | 로그의 형식 항상 `AzureDiagnostics` |
| `SubscriptionId` | 서버가 속한 구독의 GUID |
| `ResourceGroup` | 서버가 속한 리소스 그룹의 이름 |
| `ResourceProvider` | 리소스 공급자의 이름. 항상 `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | 리소스 URI |
| `Resource` | 서버의 이름 |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `event_class` | `table_access_log` |
| `event_subclass` | `READ`하십시오 `INSERT`, `UPDATE`, 또는 `DELETE` |
| `connection_id` | MySQL에서 생성 된 고유 연결 ID |
| `db` | 액세스 데이터베이스의 이름 |
| `table` | 액세스 하는 테이블 이름 |
| `sql_text` | 전체 쿼리 텍스트 |
| `\_ResourceId` | 리소스 URI |

## <a name="next-steps"></a>다음 단계

- [Azure portal에서 감사 로그를 구성 하는 방법](howto-configure-audit-logs-portal.md)