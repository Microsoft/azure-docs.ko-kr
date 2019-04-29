---
title: 워크로드 분석 - Azure SQL Data Warehouse | Microsoft Docs
description: Azure SQL Data Warehouse의 워크로드에 대한 쿼리 우선 순위 지정 분석 기술.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
origin.date: 03/13/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: jrasnick
ms.openlocfilehash: 434cbb18a109308844dbc7ff219d40948678e86e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679105"
---
# <a name="analyze-your-workload-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에서 워크로드 분석

Azure SQL Data Warehouse에서 워크 로드를 분석 하기 위한 기술입니다.

## <a name="resource-classes"></a>리소스 클래스

SQL Data Warehouse는 쿼리를 시스템 리소스를 할당 하려면 리소스 클래스를 제공 합니다.  리소스 클래스에 대 한 자세한 내용은 참조 하세요. [리소스 클래스 및 워크 로드 관리](resource-classes-for-workload-management.md)합니다.  쿼리는 쿼리에 할당 된 리소스 클래스를 현재 사용할 수 있는 보다 더 많은 리소스가 필요한 경우 대기 합니다.

## <a name="queued-query-detection-and-other-dmvs"></a>큐에 대기 중인 쿼리 검색 및 다른 DMV

`sys.dm_pdw_exec_requests` DMV는 동시성 큐에 대기 중인 쿼리를 식별하는 데 사용할 수 있습니다. 동시성 슬롯을 대기하는 쿼리는 **일시 중단**상태가 됩니다.

```sql
SELECT  r.[request_id]                           AS Request_ID
,       r.[status]                               AS Request_Status
,       r.[submit_time]                          AS Request_SubmitTime
,       r.[start_time]                           AS Request_StartTime
,       DATEDIFF(ms,[submit_time],[start_time])  AS Request_InitiateDuration_ms
,       r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r
;
```

워크로드 관리 역할은 `sys.database_principals`를 사용하여 확인할 수 있습니다.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0
;
```

다음 쿼리는 각 사용자에게 어떤 역할이 할당되었는지 보여줍니다.

```sql
SELECT  r.name AS role_principal_name
,       m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id      = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc')
;
```

SQL Data Warehouse에 다음과 같은 대기 형식이 있습니다.

* **LocalQueriesConcurrencyResourceType**: 동시성 슬롯 프레임워크 외부에 있는 쿼리. `SELECT @@VERSION` 과 같은 DMV 쿼리 및 시스템 함수는 로컬 쿼리의 예입니다.
* **UserConcurrencyResourceType**: 동시성 슬롯 프레임워크 내부에 있는 쿼리. 최종 사용자 테이블에 대한 쿼리는 이 리소스 형식을 사용하는 예를 나타냅니다.
* **DmsConcurrencyResourceType**: 데이터 이동 작업으로 인해 발생하는 대기
* **BackupConcurrencyResourceType**: 데이터베이스를 백업 중임을 나타내는 대기 이 리소스 유형에 대한 최대값은 1입니다. 여러 백업을 동시에 요청한 경우 다른 백업 요청은 큐에 저장됩니다. 일반적으로 10 분의 연속 스냅숏 사이의 최소 시간을 사용 하는 것이 좋습니다. 

`sys.dm_pdw_waits` DMV는 요청이 대기 중인 리소스를 알아내는 데 사용할 수 있습니다.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]                                           AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,       SESSION_ID()                                       AS Current_session
,       s.[status]                                         AS Session_status
,       s.[login_name]
,       s.[query_count]
,       s.[client_id]
,       s.[sql_spid]
,       r.[command]                                        AS Request_command
,       r.[label]
,       r.[status]                                         AS Request_status
,       r.[submit_time]
,       r.[start_time]
,       r.[end_compile_time]
,       r.[end_time]
,       DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,       DATEDIFF(ms,r.[start_time],r.[end_compile_time])   AS Request_compile_time_ms
,       DATEDIFF(ms,r.[end_compile_time],r.[end_time])     AS Request_execution_time_ms
,       r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID()
;
```

`sys.dm_pdw_resource_waits` DMV에 지정 된 쿼리에 대 한 대기 정보를 보여 줍니다. 리소스를 대기 시간 측정 리소스가 제공 될 때까지 기다리는 시간입니다. 신호 대기 시간은 기본 SQL server에서 CPU로 쿼리를 예약 하는 데 걸리는 시간입니다.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID()
;
```

또한 `sys.dm_pdw_resource_waits` DMV를 사용하여 얼마나 많은 동시성 슬롯이 부여되었는지 계산할 수 있습니다.

```sql
SELECT  SUM([concurrency_slots_used]) as total_granted_slots
FROM    sys.[dm_pdw_resource_waits]
WHERE   [state]           = 'Granted'
AND     [resource_class] is not null
AND     [session_id]     <> session_id()
;
```

`sys.dm_pdw_wait_stats` DMV는 기록 추세 분석에 사용할 수 있습니다.

```sql
SELECT   w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w
;
```

## <a name="next-steps"></a>다음 단계

데이터베이스 사용자 및 보안을 관리하는 방법에 대한 자세한 내용은 [SQL Data Warehouse에서 데이터베이스 보호](sql-data-warehouse-overview-manage-security.md)를 참조하세요. 더 큰 리소스 클래스가 클러스터된 columnstore 인덱스 품질을 향상할 방법에 대한 자세한 내용은 [인덱스를 다시 빌드하여 세그먼트 품질 개선](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)을 참조하세요.
