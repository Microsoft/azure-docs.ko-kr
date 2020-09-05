---
title: Dmv를 사용 하 여 SQL 풀 워크 로드 모니터링
description: Dmv를 사용 하 여 Azure Synapse Analytics SQL 풀 작업 및 쿼리 실행을 모니터링 하는 방법에 대해 알아봅니다.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/24/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: synapse-analytics
ms.openlocfilehash: 9eb1006bdba6c69136c972359bb13420a04f4180
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048027"
---
# <a name="monitor-your-azure-synapse-analytics-sql-pool-workload-using-dmvs"></a>Dmv를 사용 하 여 Azure Synapse Analytics SQL 풀 워크 로드 모니터링

이 문서에서는 Dmv (동적 관리 뷰)를 사용 하 여 SQL 풀에서 쿼리 실행을 조사 하는 작업을 비롯 한 작업을 모니터링 하는 방법을 설명 합니다.

## <a name="permissions"></a>사용 권한

이 문서에서 Dmv를 쿼리하려면 **VIEW DATABASE STATE** 또는 **CONTROL** 권한이 필요 합니다. 일반적으로 **뷰 데이터베이스 상태** 를 부여 하는 것은 매우 제한적 이기 때문에 기본 설정 된 사용 권한입니다.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>연결 모니터링

데이터 웨어하우스에 대 한 모든 로그인은 [sys. dm_pdw_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 기록 됩니다.  이 DMV에는 마지막 10,000회의 로그인 정보가 포함됩니다.  session_id(기본 키)는 각각의 새 로그인에 대해 순차적으로 할당됩니다.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>쿼리 실행 모니터링

SQL 풀에서 실행 되는 모든 쿼리는 [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에 기록 됩니다.  이 DMV에는 마지막으로 실행한 쿼리 10,000개가 포함됩니다.  이 DMV의 기본 키인 request_id는 각 쿼리를 고유하게 식별합니다.  request_id는 각각의 새 쿼리에 대해 순차적으로 할당되며 쿼리 ID를 나타내는 QID가 접두사로 추가됩니다.  이 DMV에서 지정된 session_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

> [!NOTE]
> 저장 프로시저는 여러 요청 ID를 사용합니다.  요청 ID는 순차적으로 할당됩니다.

특정 쿼리에 대한 쿼리 실행 계획 및 시간을 조사하기 위해 수행하는 단계는 다음과 같습니다.

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>1단계: 조사하려는 쿼리 식별

```sql
-- Monitor active queries
SELECT *
FROM sys.dm_pdw_exec_requests
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 *
FROM sys.dm_pdw_exec_requests
ORDER BY total_elapsed_time DESC;

```

위의 쿼리 결과에서 조사할 쿼리의 **요청 ID를 적어 둡니다** .

**일시 중단** 상태의 쿼리는 실행 중인 활성 쿼리 수가 많기 때문에 큐에 대기할 수 있습니다. 이러한 쿼리는 UserConcurrencyResourceType 형식으로 [dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 대기 하는 쿼리를 사용 하 여 표시 됩니다. 동시성 제한에 대 한 자세한 내용은 [메모리 및 동시성 제한](memory-concurrency-limits.md) 또는 [워크 로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조 하세요. 쿼리는 개체 잠금 등의 기타 이유로 인해 대기 상태일 수도 있습니다.  쿼리가 리소스를 대기 중인 경우 이 문서 뒷부분의 [리소스를 대기 중인 쿼리 조사](#monitor-waiting-queries)를 참조하세요.

[Dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 테이블에서 쿼리 조회를 간소화 하려면 [레이블을](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 사용 하 여 쿼리에 주석을 할당 합니다 .이는 dm_pdw_exec_requests 뷰에서 조회할 수 있습니다.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>2단계: 쿼리 계획 조사

요청 ID를 사용 하 여 [sys. dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 에서 쿼리의 분산 SQL (sql) 계획을 검색 합니다.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL 계획의 시간이 생각보다 오래 걸리는 경우 계획이 여러 DSQL 단계를 포함하여 복잡하거나 한 단계에 시간이 오래 걸리는 것일 수 있습니다.  계획에 많은 단계가 포함되어 있으며 여러 이동 작업이 수행되는 경우에는 테이블 분산을 최적화하여 데이터 이동을 줄일 수 있습니다. [테이블 배포](sql-data-warehouse-tables-distribute.md) 문서에는 쿼리를 해결 하기 위해 데이터를 이동 해야 하는 이유가 설명 되어 있습니다. 이 문서에서는 데이터 이동을 최소화 하기 위한 몇 가지 배포 전략에 대해서도 설명 합니다.

한 단계에서 추가 세부 정보를 조사하려면 오래 실행되는 쿼리 단계의 *operation_type* 열을 확인하고 **단계 인덱스**를 적어 둡니다.

* **SQL 작업**: Onoperation, Remoteoperation, ReturnOperation에 대해 3 단계를 진행 합니다.
* **데이터 이동 작업**에 대해 4 단계를 진행 합니다. ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, 파티션 된 MoveOperation, Moveoperation, copyoperation.

### <a name="step-3-investigate-sql-on-the-distributed-databases"></a>3 단계: 분산 데이터베이스에서 SQL 조사

요청 ID와 단계 인덱스를 사용하여 [sys.dm_pdw_sql_requests](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에서 세부 정보를 검색합니다. 이 보기에는 모든 분산 데이터베이스에 대한 쿼리 단계의 실행 정보가 포함되어 있습니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

쿼리 단계가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)을 사용하여 특정 분산에서 현재 실행 중인 단계에 대해 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL pool or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-4-investigate-data-movement-on-the-distributed-databases"></a>4 단계: 분산 데이터베이스에서 데이터 이동 조사

요청 ID 및 단계 인덱스를 사용하여 [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)에서 각 분산에 대해 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
* 장기 실행 배포의 경우 *rows_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 그렇다면 이 결과는 기본 데이터의 왜곡을 나타낼 수 있습니다.

쿼리가 실행 중이면 [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 를 사용 하 여 특정 배포 내에서 현재 실행 중인 SQL 단계에 대 한 SQL Server 계획 캐시에서 예상 SQL Server 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL pool Compute or control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>대기 중인 쿼리 모니터링

쿼리가 리소스를 대기하는 중이어서 진행되고 있지 않은 경우, 쿼리가 대기 중인 모든 리소스를 표시하는 쿼리는 다음과 같습니다.

```sql
-- Find queries
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

쿼리가 적극적으로 다른 쿼리의 리소스를 대기 중인 경우 상태는 **AcquireResources**입니다.  쿼리가 필요한 리소스를 모두 가지고 있으면 상태는 **Granted**입니다.

## <a name="monitor-tempdb"></a>tempdb 모니터링

Tempdb는 쿼리를 실행 하는 동안 중간 결과를 저장 하는 데 사용 됩니다. Tempdb 데이터베이스의 사용률이 높으면 쿼리 성능이 저하 될 수 있습니다. 구성 된 모든 DW100c에 대해 399 g b의 tempdb 공간이 할당 됩니다 (DW1000c에 3.99 TB의 tempdb 공간이 있는 경우).  다음은 tempdb 사용을 모니터링 하 고 쿼리에서 tempdb 사용을 줄이는 데 유용한 팁입니다.

### <a name="monitoring-tempdb-with-views"></a>뷰를 사용 하 여 tempdb 모니터링

Tempdb 사용 현황을 모니터링 하려면 먼저 [sql 풀 용 Microsoft Toolkit](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)의 [microsoft. vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) 뷰를 설치 합니다. 그러면 다음 쿼리를 실행 하 여 실행 된 모든 쿼리에 대 한 노드당 tempdb 사용량을 확인할 수 있습니다.

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa'
ORDER BY sr.request_id;
```

많은 양의 메모리를 사용 하는 쿼리가 있거나 tempdb 할당과 관련 된 오류 메시지를 받은 경우 최종 데이터 이동 작업에서 실패 하는 [select (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 또는 [INSERT select](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 문으로 매우 큰 CREATE TABLE 때문일 수 있습니다. 이는 일반적으로 분산 쿼리 계획에서 최종 INSERT SELECT 바로 전에 ShuffleMove 작업으로 식별 될 수 있습니다.  ShuffleMove를 [dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 사용 하 여 작업을 모니터링할 수 있습니다.

가장 일반적인 완화 방법은 CTAS 또는 INSERT SELECT 문을 여러 개의 load 문으로 분할 하는 것입니다. 따라서 데이터 볼륨은 노드 tempdb 제한 당 1TB를 초과 하지 않습니다. 또한 클러스터의 크기를 더 크게 조정 하 여 더 많은 노드 간에 tempdb 크기를 분산할 수 있습니다. 그러면 각 개별 노드에서 tempdb를 줄일 수 있습니다.

CTAS 및 INSERT SELECT 문 외에도 메모리 부족으로 실행 되는 복잡 한 쿼리는 tempdb로 분할 되어 쿼리가 실패할 수 있습니다.  Tempdb로 분산 하지 않도록 더 큰 [리소스 클래스](resource-classes-for-workload-management.md) 를 사용 하 여 실행 하는 것이 좋습니다.

## <a name="monitor-memory"></a>메모리 모니터링

메모리는 성능 저하 및 메모리 부족 문제의 근본 원인일 수 있습니다. SQL Server 메모리 사용량이 쿼리 실행 중 한계에 도달한 것을 발견한 경우 데이터 웨어하우스를 확장하는 것이 좋습니다.

다음 쿼리는 노드당 SQL Server 메모리 사용량 및 메모리 부족을 반환합니다.

```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB,
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```

## <a name="monitor-transaction-log-size"></a>트랜잭션 로그 크기 모니터링

다음 쿼리는 각 배포에서 트랜잭션 로그 크기를 반환합니다. 로그 파일 중 하나가 160GB에 도달하는 경우 인스턴스를 확장하거나 트랜잭션 크기를 제한해야 합니다.

```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id
FROM sys.dm_pdw_nodes_os_performance_counters
WHERE
instance_name like 'Distribution_%'
AND counter_name = 'Log File(s) Used Size (KB)'
```

## <a name="monitor-transaction-log-rollback"></a>트랜잭션 로그 롤백 모니터링

쿼리가 실패하거나 진행하는 데 시간이 오래 걸리는 경우 트랜잭션 롤백이 있는지 확인하고 모니터링할 수 있습니다.

```sql
-- Monitor rollback
SELECT
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>PolyBase 부하 모니터링

다음 쿼리는 예상 되는 로드 진행률을 제공 합니다. 이 쿼리는 현재 처리 중인 파일만 표시 합니다.

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files,
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>다음 단계

DMV에 대한 자세한 내용은 [시스템 뷰](../sql/reference-tsql-system-views.md)를 참조하세요.
