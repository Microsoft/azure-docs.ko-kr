---
title: DMV를 사용하여 작업 모니터링 | Microsoft Docs
description: DMV를 사용하여 작업을 모니터링하는 방법을 알아봅니다.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
origin.date: 04/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: ff1f613dfdfb5c43b727bcc9c7f7a1f0afca0975
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748777"
---
# <a name="monitor-your-workload-using-dmvs"></a>DMV를 사용하여 작업 모니터링
이 문서에서는 동적 관리 뷰(DMV)를 사용하여 워크로드를 모니터링하는 방법을 설명합니다. 여기에는 Azure SQL Data Warehouse에서 쿼리 실행을 조사하는 것이 포함됩니다.

## <a name="permissions"></a>권한
이 문서의 DMV를 쿼리하려면 VIEW DATABASE STATE 또는 CONTROL 권한이 필요합니다. 일반적으로 VIEW DATABASE STATE 권한 부여를 선호합니다. 훨씬 제한적이기 때문입니다.

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>연결 모니터링
SQL Data Warehouse에 대한 모든 로그인은 [sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions]에 기록됩니다.  이 DMV에는 마지막 10,000회의 로그인 정보가 포함됩니다.  session_id(기본 키)는 각각의 새 로그인에 대해 순차적으로 할당됩니다.

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>쿼리 실행 모니터링
SQL Data Warehouse에 대해 실행되는 모든 쿼리는 [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests]에 기록됩니다.  이 DMV에는 마지막으로 실행한 쿼리 10,000개가 포함됩니다.  이 DMV의 기본 키인 request_id는 각 쿼리를 고유하게 식별합니다.  request_id는 각각의 새 쿼리에 대해 순차적으로 할당되며 쿼리 ID를 나타내는 QID가 접두사로 추가됩니다.  이 DMV에서 지정된 session_id를 쿼리하면 지정된 로그온에 대한 모든 쿼리가 표시됩니다.

> [!NOTE]
> 저장 프로시저는 여러 요청 ID를 사용합니다.  요청 ID는 순차적으로 할당됩니다. 
> 
> 

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

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

위의 쿼리 결과에서 조사할 쿼리의 **요청 ID를 적어 둡니다** .

쿼리를 **일시 중단 된** 활성 실행 중인 쿼리 수가 많아져서 상태 큐에 대기할 수 있습니다. 이러한 쿼리는 [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) 대기 쿼리에도 UserConcurrencyResourceType 형식으로 합니다. 동시성 제한에 대한 자세한 내용은 [성능 계층](performance-tiers.md) 또는 [워크로드 관리를 위한 리소스 클래스](resource-classes-for-workload-management.md)를 참조하세요. 쿼리는 개체 잠금 등의 기타 이유로 인해 대기 상태일 수도 있습니다.  쿼리가 리소스를 대기 중인 경우 이 문서 뒷부분의 [리소스를 대기 중인 쿼리 조사][Investigating queries waiting for resources]를 참조하세요.

쿼리 조회를 간소화 하는 [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) 테이블에서 사용 하 여 [레이블] [ LABEL] 주석을 sys.dm_pdw_exec_에서 조회할 수 있는 쿼리를 할당 하려면 요청 보기입니다.

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;
```

### <a name="step-2-investigate-the-query-plan"></a>2단계: 쿼리 계획 조사
요청 ID를 사용하여 [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps]에서 쿼리의 DSQL(분산된 SQL) 계획을 검색합니다.

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL 계획의 시간이 생각보다 오래 걸리는 경우 계획이 여러 DSQL 단계를 포함하여 복잡하거나 한 단계에 시간이 오래 걸리는 것일 수 있습니다.  계획에 많은 단계가 포함되어 있으며 여러 이동 작업이 수행되는 경우에는 테이블 분산을 최적화하여 데이터 이동을 줄일 수 있습니다. [테이블 분산][Table distribution] 문서에서는 쿼리를 확인하기 위해 데이터를 이동해야 하는 이유와, 데이터 이동을 최소화하기 위한 몇 가지 분산 전략에 대해 설명합니다.

한 단계에서 추가 세부 정보를 조사하려면 오래 실행되는 쿼리 단계의 *operation_type* 열을 확인하고 **단계 인덱스**를 적어 둡니다.

* **SQL 작업**의 경우 3a단계 진행: OnOperation, RemoteOperation, ReturnOperation
* **데이터 이동 작업**의 경우 3b단계 진행: ShuffleMoveOperation, BroadcastMoveOperation, TrimMoveOperation, PartitionMoveOperation, MoveOperation, CopyOperation

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>3a단계: 분산 데이터베이스에서 SQL 조사
요청 ID와 단계 인덱스를 사용하여 [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests]에서 세부 정보를 검색합니다. 이 보기에는 모든 분산 데이터베이스에 대한 쿼리 단계의 실행 정보가 포함되어 있습니다.

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

쿼리 단계가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]을 사용하여 특정 분산에서 현재 실행 중인 단계에 대해 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>3b단계: 분산 데이터베이스에서 데이터 이동 조사
요청 ID 및 단계 인덱스를 사용하여 [sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers]에서 각 분산에 대해 실행 중인 데이터 이동 단계에 대한 정보를 검색합니다.

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 열을 검사하여 특정 배포에서 데이터 이동 시간이 다른 배포보다 오래 걸리는지 확인합니다.
* 장기 실행 배포의 경우 *rows_processed* 열을 검사하여 해당 배포에서 이동되는 행 수가 다른 배포보다 훨씬 큰지 확인합니다. 그렇다면 이 결과는 기본 데이터의 왜곡을 나타낼 수 있습니다.

쿼리가 실행되고 있으면 [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN]을 사용하여 특정 배포 내에서 현재 실행 중인 SQL 단계에 대한 SQL Server 계획 캐시에서 SQL Server 예상 계획을 검색할 수 있습니다.

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
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
Tempdb는 쿼리를 실행 하는 동안 중간 결과 저장 하는 데 사용 됩니다. Tempdb 데이터베이스 사용률이 높은 쿼리 성능을 저하 시킬 수 있습니다. Azure SQL Data Warehouse의 각 노드는 약 1TB의 tempdb에 대 한 원시 공간에 있습니다. 다음은 tempdb 사용량을 모니터링 하 고 쿼리에서 tempdb 사용량 감소에 대 한 팁입니다. 

### <a name="monitoring-tempdb-with-views"></a>뷰를 사용 하 여 tempdb를 모니터링합니다.
Tempdb 사용량을 모니터링 하려면 먼저 설치 합니다 [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) 에서 볼 합니다 [SQL Data Warehouse에 대 한 Microsoft 도구 키트](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)합니다. 그런 다음 모든 실행된 쿼리에 대 한 노드당 tempdb 사용량을 보려면 다음 쿼리를 실행할 수 있습니다.

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

쿼리에서 많은 양의 메모리를 소비 하거나 tempdb의 할당에 관련 된 오류 메시지를 받은 경우 종종 때문에 나타나는 것은 매우 많은 [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) 하거나 [INSERT SELECT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) 문을 실행 하는 최종 데이터 이동 작업에 실패 했습니다. 이 일반적으로 식별할 수 있습니다 ShuffleMove 작업으로 최종 INSERT SELECT 직전 분산된 쿼리 계획.

가장 일반적인 완화 데이터 볼륨은 노드 tempdb 제한 당 1TB를 초과 하지 않도록 여러 load 문을 CTAS 또는 INSERT SELECT 문의 분해 하는 것입니다. 또한 tempdb 크기를 각 개별 노드에서 tempdb를 줄이면 더 많은 노드에 분산 하는 더 큰 크기로 클러스터를 확장할 수 있습니다. 

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

## <a name="next-steps"></a>다음 단계
DMV에 대한 자세한 내용은 [시스템 뷰][System views]를 참조하세요.


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx

<!-- Update_Description: update meta properties, wording update -->