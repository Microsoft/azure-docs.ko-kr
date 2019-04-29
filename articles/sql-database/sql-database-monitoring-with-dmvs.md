---
title: DMV를 사용하여 Azure SQL Database 성능 모니터링 | Microsoft Docs
description: 동적 관리 뷰를 사용하여 Microsoft Azure SQL Database를 모니터링하여 일반적인 성능 문제를 감지 및 진단하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 371632a28d22583f8b206e4d8b9d2b6b4e510ab0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103766"
---
# <a name="monitoring-performance-azure-sql-database-using-dynamic-management-views"></a>동적 관리 뷰를 사용하여 Azure SQL Database 성능모니터링

Microsoft Azure SQL Database를 사용하여 차단되거나 오래 실행된 쿼리, 리소스 병목 현상, 잘못된 쿼리 계획 등에 의해 야기될 수 있는 동적 관리 뷰의 하위 집합에서 성능 문제를 진단할 수 있습니다. 이 항목에서는 동적 관리 뷰를 사용하여 일반적인 성능 문제를 감지하는 방법을 설명합니다.

SQL Database는 세 가지 범주의 동적 관리 뷰를 부분적으로 지원합니다.

- 데이터베이스 관련 동적 관리 뷰.
- 실행 관련 동적 관리 뷰.
- 트랜잭션 관련 동적 관리 뷰

동적 관리 뷰에 대한 자세한 내용은 SQL Server 온라인 설명서의 [동적 관리 뷰 및 함수(Transact-SQL)](https://msdn.microsoft.com/library/ms188754.aspx) 를 참조하세요.

## <a name="permissions"></a>권한

SQL Database에서 동적 관리 뷰를 쿼리하려면 **VIEW DATABASE STATE** 권한이 있어야 합니다. **VIEW DATABASE STATE** 권한은 현재 데이터베이스 내의 모든 개체에 대한 정보를 반환합니다.
특정 데이터베이스 사용자에게 **VIEW DATABASE STATE** 권한을 부여하려면 다음 쿼리를 실행합니다.

```sql
GRANT VIEW DATABASE STATE TO database_user;
```

온-프레미스 SQL 서버의 인스턴스에서 동적 관리 뷰는 서버 상태 정보를 반환합니다. SQL Database에서는 현재의 논리 데이터베이스에 관한 정보만 반환합니다.

## <a name="identify-cpu-performance-issues"></a>CPU 성능 문제 식별

CPU 사용량이 오랫동안 80%를 초과하면 다음 문제 해결 단계를 고려해야 합니다.

### <a name="the-cpu-issue-is-occurring-now"></a>현재 CPU 문제가 발생 중

현재 문제가 발생 중인 경우 두 가지 가능한 시나리오가 있습니다.

#### <a name="many-individual-queries-that-cumulatively-consume-high-cpu"></a>점증적으로 CPU 사용량이 높은 여러 개별 쿼리

다음 쿼리를 사용하여 상위 쿼리 해시를 식별합니다.

```sql
PRINT '-- top 10 Active CPU Consuming Queries (aggregated)--';
SELECT TOP 10 GETDATE() runtime, *
FROM(SELECT query_stats.query_hash, SUM(query_stats.cpu_time) 'Total_Request_Cpu_Time_Ms', SUM(logical_reads) 'Total_Request_Logical_Reads', MIN(start_time) 'Earliest_Request_start_Time', COUNT(*) 'Number_Of_Requests', SUBSTRING(REPLACE(REPLACE(MIN(query_stats.statement_text), CHAR(10), ' '), CHAR(13), ' '), 1, 256) AS "Statement_Text"
     FROM(SELECT req.*, SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1) AS statement_text
          FROM sys.dm_exec_requests AS req
               CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST ) AS query_stats
     GROUP BY query_hash) AS t
ORDER BY Total_Request_Cpu_Time_Ms DESC;
```

#### <a name="long-running-queries-that-consume-cpu-are-still-running"></a>CPU를 사용하는 장기 실행 쿼리가 여전히 실행 중

다음 쿼리를 사용하여 이러한 쿼리를 식별합니다.

```sql
PRINT '--top 10 Active CPU Consuming Queries by sessions--';
SELECT TOP 10 req.session_id, req.start_time, cpu_time 'cpu_time_ms', OBJECT_NAME(ST.objectid, ST.dbid) 'ObjectName', SUBSTRING(REPLACE(REPLACE(SUBSTRING(ST.text, (req.statement_start_offset / 2)+1, ((CASE statement_end_offset WHEN -1 THEN DATALENGTH(ST.text)ELSE req.statement_end_offset END-req.statement_start_offset)/ 2)+1), CHAR(10), ' '), CHAR(13), ' '), 1, 512) AS statement_text
FROM sys.dm_exec_requests AS req
     CROSS APPLY sys.dm_exec_sql_text(req.sql_handle) AS ST
ORDER BY cpu_time DESC;
GO
```

### <a name="the-cpu-issue-occurred-in-the-past"></a>과거에 발생한 CPU 문제

과거에 문제가 발생했고 근본 원인을 분석하려면 [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)를 사용합니다. 데이터베이스 액세스 권한이 있는 사용자는 T-SQL을 사용하여 쿼리 저장소 데이터를 쿼리할 수 있습니다.  쿼리 저장소 기본 구성은 세분성 1시간을 사용합니다.  CPU 사용량이 많은 쿼리에 대한 작업을 살펴보려면 다음 쿼리를 사용합니다. 이 쿼리는 CPU 사용량이 많은 상위 15개 쿼리를 반환합니다.  잊지 말고 `rsi.start_time >= DATEADD(hour, -2, GETUTCDATE()`를 변경하세요.

```sql
-- Top 15 CPU consuming queries by query hash
-- note that a query  hash can have many query id if not parameterized or not parameterized properly
-- it grabs a sample query text by min
WITH AggregatedCPU AS (SELECT q.query_hash, SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec, SUM(count_executions * avg_cpu_time / 1000.0)/ SUM(count_executions) AS avg_cpu_millisec, MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec, MAX(max_logical_io_reads) max_logical_reads, COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans, COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids, SUM(CASE WHEN rs.execution_type_desc='Aborted' THEN count_executions ELSE 0 END) AS Aborted_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Regular' THEN count_executions ELSE 0 END) AS Regular_Execution_Count, SUM(CASE WHEN rs.execution_type_desc='Exception' THEN count_executions ELSE 0 END) AS Exception_Execution_Count, SUM(count_executions) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text
                       FROM sys.query_store_query_text AS qt
                            JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                            JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                            JOIN sys.query_store_runtime_stats AS rs ON rs.plan_id=p.plan_id
                            JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=rs.runtime_stats_interval_id
                       WHERE rs.execution_type_desc IN ('Regular', 'Aborted', 'Exception')AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                       GROUP BY q.query_hash), OrderedCPU AS (SELECT query_hash, total_cpu_millisec, avg_cpu_millisec, max_cpu_millisec, max_logical_reads, number_of_distinct_plans, number_of_distinct_query_ids, total_executions, Aborted_Execution_Count, Regular_Execution_Count, Exception_Execution_Count, sampled_query_text, ROW_NUMBER() OVER (ORDER BY total_cpu_millisec DESC, query_hash ASC) AS RN
                                                              FROM AggregatedCPU)
SELECT OD.query_hash, OD.total_cpu_millisec, OD.avg_cpu_millisec, OD.max_cpu_millisec, OD.max_logical_reads, OD.number_of_distinct_plans, OD.number_of_distinct_query_ids, OD.total_executions, OD.Aborted_Execution_Count, OD.Regular_Execution_Count, OD.Exception_Execution_Count, OD.sampled_query_text, OD.RN
FROM OrderedCPU AS OD
WHERE OD.RN<=15
ORDER BY total_cpu_millisec DESC;
```

문제가 있는 쿼리를 식별한 후에는 CPU 사용률을 줄이도록 쿼리를 튜닝해야 합니다.  쿼리를 튜닝할 시간이 없는 경우 데이터베이스의 SLO를 업그레이드하여 문제를 해결할 수도 있습니다.

## <a name="identify-io-performance-issues"></a>IO 성능 문제 식별

IO 성능 문제를 식별할 때 IO 문제와 관련된 상위 대기 유형은 다음과 같습니다.

- `PAGEIOLATCH_*`

  데이터 파일 IO 문제인 경우(`PAGEIOLATCH_SH`, `PAGEIOLATCH_EX`, `PAGEIOLATCH_UP` 포함).  대기 유형 이름에 **IO**가 있으면 IO 문제를 가리키는 것입니다. 페이지 래치 대기 이름에 **IO**가 없으면 다른 유형의 문제(예: tempdb 경합)를 가리키는 것입니다.

- `WRITE_LOG`

  트랜잭션 로그 IO 문제인 경우.

### <a name="if-the-io-issue-is-occurring-right-now"></a>현재 IO 문제가 발생 중인 경우

[sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) 또는 [sys.dm_os_waiting_tasks](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql)를 사용하여 `wait_type` 및 `wait_time`을 확인합니다.

#### <a name="identify-data-and-log-io-usage"></a>데이터 및 로그 IO 사용량 식별

다음 쿼리를 사용하여 데이터 및 로그 IO 사용량을 식별할 수 있습니다. 데이터 또는 로그 IO가 80%를 초과하면 사용자가 SQL DB 서비스 계층에 제공되는 IO를 사용하는 것입니다.

```sql
SELECT end_time, avg_data_io_percent, avg_log_write_percent
FROM sys.dm_db_resource_stats
ORDER BY end_time DESC;
```

IO 제한에 도달하는 경우 두 가지 옵션이 있습니다.

- 옵션 1: 컴퓨팅 크기 또는 서비스 계층 업그레이드
- 옵션 2: 대부분의 IO를 사용하는 쿼리를 식별하여 튜닝

#### <a name="view-buffer-related-io-using-the-query-store"></a>쿼리 저장소를 사용하여 버퍼 관련 IO 확인

옵션 2의 경우 버퍼 관련 IO에 대한 쿼리 저장소에 다음 쿼리를 사용하여 마지막 2시간 동안 추적된 작업을 볼 수 있습니다.

```sql
-- top queries that waited on buffer
-- note these are finished queries
WITH Aggregated AS (SELECT q.query_hash, SUM(total_query_wait_time_ms) total_wait_time_ms, SUM(total_query_wait_time_ms / avg_query_wait_time_ms) AS total_executions, MIN(qt.query_sql_text) AS sampled_query_text, MIN(wait_category_desc) AS wait_category_desc
                    FROM sys.query_store_query_text AS qt
                         JOIN sys.query_store_query AS q ON qt.query_text_id=q.query_text_id
                         JOIN sys.query_store_plan AS p ON q.query_id=p.query_id
                         JOIN sys.query_store_wait_stats AS waits ON waits.plan_id=p.plan_id
                         JOIN sys.query_store_runtime_stats_interval AS rsi ON rsi.runtime_stats_interval_id=waits.runtime_stats_interval_id
                    WHERE wait_category_desc='Buffer IO' AND rsi.start_time>=DATEADD(HOUR, -2, GETUTCDATE())
                    GROUP BY q.query_hash), Ordered AS (SELECT query_hash, total_executions, total_wait_time_ms, sampled_query_text, wait_category_desc, ROW_NUMBER() OVER (ORDER BY total_wait_time_ms DESC, query_hash ASC) AS RN
                                                        FROM Aggregated)
SELECT OD.query_hash, OD.total_executions, OD.total_wait_time_ms, OD.sampled_query_text, OD.wait_category_desc, OD.RN
FROM Ordered AS OD
WHERE OD.RN<=15
ORDER BY total_wait_time_ms DESC;
GO
```

#### <a name="view-total-log-io-for-writelog-waits"></a>WRITELOG 대기에 대한 총 로그 IO 보기

대기 유형이 `WRITELOG`이면 다음 쿼리를 사용하여 명령문별로 총 로그 IO를 봅니다.

```sql
-- Top transaction log consumers
-- Adjust the time window by changing
-- rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
WITH AggregatedLogUsed
AS (SELECT q.query_hash,
           SUM(count_executions * avg_cpu_time / 1000.0) AS total_cpu_millisec,
           SUM(count_executions * avg_cpu_time / 1000.0) / SUM(count_executions) AS avg_cpu_millisec,
           SUM(count_executions * avg_log_bytes_used) AS total_log_bytes_used,
           MAX(rs.max_cpu_time / 1000.00) AS max_cpu_millisec,
           MAX(max_logical_io_reads) max_logical_reads,
           COUNT(DISTINCT p.plan_id) AS number_of_distinct_plans,
           COUNT(DISTINCT p.query_id) AS number_of_distinct_query_ids,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Aborted' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Aborted_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Regular' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Regular_Execution_Count,
           SUM(   CASE
                      WHEN rs.execution_type_desc = 'Exception' THEN
                          count_executions
                      ELSE
                          0
                  END
              ) AS Exception_Execution_Count,
           SUM(count_executions) AS total_executions,
           MIN(qt.query_sql_text) AS sampled_query_text
    FROM sys.query_store_query_text AS qt
        JOIN sys.query_store_query AS q
            ON qt.query_text_id = q.query_text_id
        JOIN sys.query_store_plan AS p
            ON q.query_id = p.query_id
        JOIN sys.query_store_runtime_stats AS rs
            ON rs.plan_id = p.plan_id
        JOIN sys.query_store_runtime_stats_interval AS rsi
            ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
    WHERE rs.execution_type_desc IN ( 'Regular', 'Aborted', 'Exception' )
          AND rsi.start_time >= DATEADD(HOUR, -2, GETUTCDATE())
    GROUP BY q.query_hash),
     OrderedLogUsed
AS (SELECT query_hash,
           total_log_bytes_used,
           number_of_distinct_plans,
           number_of_distinct_query_ids,
           total_executions,
           Aborted_Execution_Count,
           Regular_Execution_Count,
           Exception_Execution_Count,
           sampled_query_text,
           ROW_NUMBER() OVER (ORDER BY total_log_bytes_used DESC, query_hash ASC) AS RN
    FROM AggregatedLogUsed)
SELECT OD.total_log_bytes_used,
       OD.number_of_distinct_plans,
       OD.number_of_distinct_query_ids,
       OD.total_executions,
       OD.Aborted_Execution_Count,
       OD.Regular_Execution_Count,
       OD.Exception_Execution_Count,
       OD.sampled_query_text,
       OD.RN
FROM OrderedLogUsed AS OD
WHERE OD.RN <= 15
ORDER BY total_log_bytes_used DESC;
GO
```

## <a name="identify-tempdb-performance-issues"></a>`tempdb` 성능 문제 식별

IO 성능 문제를 식별할 때 `tempdb` 문제와 관련된 상위 대기 유형은 `PAGELATCH_*`입니다(`PAGEIOLATCH_*` 아님). 그러나 `PAGELATCH_*` 대기는 항상 `tempdb` 경합이 있다는 의미가 아닙니다.  이 대기는 동일한 데이터 페이지를 대상으로 하는 동시 요청으로 인해 사용자 개체 데이터 페이지 경합이 있다는 의미일 수도 있습니다. `tempdb` 경합을 추가로 확인하려면 [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)를 사용하여 `2:x:y`로 시작하는 wait_resource 값을 확인합니다. 여기서 2는 `tempdb` 데이터베이스 ID, `x`는 파일 ID, `y`는 페이지 ID입니다.  

tempdb 경합의 경우 `tempdb`를 사용하는 애플리케이션 코드를 줄이거나 다시 작성하는 것이 일반적인 방법입니다.  일반적인 `tempdb` 사용량 영역에는 다음이 포함됩니다.

- 임시 테이블
- 테이블 변수
- 테이블 값 매개 변수
- 버전 저장소 사용량(특히 장기 실행 트랜잭션과 연결된)
- 정렬, 해시 조인 및 스풀을 사용하는 쿼리 계획이 있는 쿼리

### <a name="top-queries-that-use-table-variables-and-temporary-tables"></a>테이블 변수 및 임시 테이블을 사용하는 상위 쿼리

테이블 변수 및 임시 테이블을 사용하는 상위 쿼리를 식별하려면 다음 쿼리를 사용합니다.

```sql
SELECT plan_handle, execution_count, query_plan
INTO #tmpPlan
FROM sys.dm_exec_query_stats
     CROSS APPLY sys.dm_exec_query_plan(plan_handle);
GO

WITH XMLNAMESPACES('http://schemas.microsoft.com/sqlserver/2004/07/showplan' AS sp)
SELECT plan_handle, stmt.stmt_details.value('@Database', 'varchar(max)') 'Database', stmt.stmt_details.value('@Schema', 'varchar(max)') 'Schema', stmt.stmt_details.value('@Table', 'varchar(max)') 'table'
INTO #tmp2
FROM(SELECT CAST(query_plan AS XML) sqlplan, plan_handle FROM #tmpPlan) AS p
    CROSS APPLY sqlplan.nodes('//sp:Object') AS stmt(stmt_details);
GO

SELECT t.plan_handle, [Database], [Schema], [table], execution_count
FROM(SELECT DISTINCT plan_handle, [Database], [Schema], [table]
     FROM #tmp2
     WHERE [table] LIKE '%@%' OR [table] LIKE '%#%') AS t
    JOIN #tmpPlan AS t2 ON t.plan_handle=t2.plan_handle;
```

### <a name="identify-long-running-transactions"></a>장기 실행 트랜잭션 식별

장기 실행 트랜잭션을 식별하려면 다음 쿼리를 사용합니다. 장기 실행 트랜잭션은 버전 저장소를 정리하지 못하게 차단합니다.

```sql
SELECT DB_NAME(dtr.database_id) 'database_name',
       sess.session_id,
       atr.name AS 'tran_name',
       atr.transaction_id,
       transaction_type,
       transaction_begin_time,
       database_transaction_begin_time transaction_state,
       is_user_transaction,
       sess.open_transaction_count,
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   txt.text,
                                                                   (req.statement_start_offset / 2) + 1,
                                                                   ((CASE req.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(txt.text)
                                                                         ELSE
                                                                             req.statement_end_offset
                                                                     END - req.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) Running_stmt_text,
       recenttxt.text 'MostRecentSQLText'
FROM sys.dm_tran_active_transactions AS atr
    INNER JOIN sys.dm_tran_database_transactions AS dtr
        ON dtr.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_tran_session_transactions AS sess
        ON sess.transaction_id = atr.transaction_id
    LEFT JOIN sys.dm_exec_requests AS req
        ON req.session_id = sess.session_id
           AND req.transaction_id = sess.transaction_id
    LEFT JOIN sys.dm_exec_connections AS conn
        ON sess.session_id = conn.session_id
    OUTER APPLY sys.dm_exec_sql_text(req.sql_handle) AS txt
    OUTER APPLY sys.dm_exec_sql_text(conn.most_recent_sql_handle) AS recenttxt
WHERE atr.transaction_type != 2
      AND sess.session_id != @@spid
ORDER BY start_time ASC;
```

## <a name="identify-memory-grant-wait-performance-issues"></a>메모리 부여 대기 성능 문제 식별

상위 대기 유형이 `RESOURCE_SEMAHPORE`이고 높은 CPU 사용량 문제가 없는 경우 메모리 부여 대기 문제일 가능성이 있습니다.

### <a name="determine-if-a-resourcesemahpore-wait-is-a-top-wait"></a>`RESOURCE_SEMAHPORE` 대기가 상위 대기인지 확인

`RESOURCE_SEMAHPORE` 대기가 상위 대기인지 확인하려면 다음 쿼리를 사용합니다.

```sql
SELECT wait_type,
       SUM(wait_time) AS total_wait_time_ms
FROM sys.dm_exec_requests AS req
    JOIN sys.dm_exec_sessions AS sess
        ON req.session_id = sess.session_id
WHERE is_user_process = 1
GROUP BY wait_type
ORDER BY SUM(wait_time) DESC;
```

### <a name="identity-high-memory-consuming-statements"></a>메모리 사용량이 높은 명령문 식별

메모리 사용량이 높은 명령문을 식별하려면 다음 쿼리를 사용합니다.

```sql
SELECT IDENTITY(INT, 1, 1) rowId,
    CAST(query_plan AS XML) query_plan,
    p.query_id
INTO #tmp
FROM sys.query_store_plan AS p
    JOIN sys.query_store_runtime_stats AS r
        ON p.plan_id = r.plan_id
    JOIN sys.query_store_runtime_stats_interval AS i
        ON r.runtime_stats_interval_id = i.runtime_stats_interval_id
WHERE start_time > '2018-10-11 14:00:00.0000000'
      AND end_time < '2018-10-17 20:00:00.0000000';
GO
;WITH cte
AS (SELECT query_id,
        query_plan,
        m.c.value('@SerialDesiredMemory', 'INT') AS SerialDesiredMemory
    FROM #tmp AS t
        CROSS APPLY t.query_plan.nodes('//*:MemoryGrantInfo[@SerialDesiredMemory[. > 0]]') AS m(c) )
SELECT TOP 50
    cte.query_id,
    t.query_sql_text,
    cte.query_plan,
    CAST(SerialDesiredMemory / 1024. AS DECIMAL(10, 2)) SerialDesiredMemory_MB
FROM cte
    JOIN sys.query_store_query AS q
        ON cte.query_id = q.query_id
    JOIN sys.query_store_query_text AS t
        ON q.query_text_id = t.query_text_id
ORDER BY SerialDesiredMemory DESC;
```

### <a name="identify-the-top-10-active-memory-grants"></a>상위 10개 활성 메모리 부여 식별

상위 10개 활성 메모리 부여를 식별하려면 다음 쿼리를 사용합니다.

```sql
SELECT TOP 10
    CONVERT(VARCHAR(30), GETDATE(), 121) AS runtime,
       r.session_id,
       r.blocking_session_id,
       r.cpu_time,
       r.total_elapsed_time,
       r.reads,
       r.writes,
       r.logical_reads,
       r.row_count,
       wait_time,
       wait_type,
       r.command,
       OBJECT_NAME(txt.objectid, txt.dbid) 'Object_Name',
       LTRIM(RTRIM(REPLACE(
                              REPLACE(
                                         SUBSTRING(
                                                      SUBSTRING(
                                                                   text,
                                                                   (r.statement_start_offset / 2) + 1,
                                                                   ((CASE r.statement_end_offset
                                                                         WHEN -1 THEN
                                                                             DATALENGTH(text)
                                                                         ELSE
                                                                             r.statement_end_offset
                                                                     END - r.statement_start_offset
                                                                    ) / 2
                                                                   ) + 1
                                                               ),
                                                      1,
                                                      1000
                                                  ),
                                         CHAR(10),
                                         ' '
                                     ),
                              CHAR(13),
                              ' '
                          )
                  )
            ) stmt_text,
       mg.dop,                                               --Degree of parallelism
       mg.request_time,                                      --Date and time when this query requested the memory grant.
       mg.grant_time,                                        --NULL means memory has not been granted
       mg.requested_memory_kb / 1024.0 requested_memory_mb,  --Total requested amount of memory in megabytes
       mg.granted_memory_kb / 1024.0 AS granted_memory_mb,   --Total amount of memory actually granted in megabytes. NULL if not granted
       mg.required_memory_kb / 1024.0 AS required_memory_mb, --Minimum memory required to run this query in megabytes.
       max_used_memory_kb / 1024.0 AS max_used_memory_mb,
       mg.query_cost,                                        --Estimated query cost.
       mg.timeout_sec,                                       --Time-out in seconds before this query gives up the memory grant request.
       mg.resource_semaphore_id,                             --Non-unique ID of the resource semaphore on which this query is waiting.
       mg.wait_time_ms,                                      --Wait time in milliseconds. NULL if the memory is already granted.
       CASE mg.is_next_candidate --Is this process the next candidate for a memory grant
           WHEN 1 THEN
               'Yes'
           WHEN 0 THEN
               'No'
           ELSE
               'Memory has been granted'
       END AS 'Next Candidate for Memory Grant',
       qp.query_plan
FROM sys.dm_exec_requests AS r
    JOIN sys.dm_exec_query_memory_grants AS mg
        ON r.session_id = mg.session_id
           AND r.request_id = mg.request_id
    CROSS APPLY sys.dm_exec_sql_text(mg.sql_handle) AS txt
    CROSS APPLY sys.dm_exec_query_plan(r.plan_handle) AS qp
ORDER BY mg.granted_memory_kb DESC;
```

## <a name="calculating-database-and-objects-sizes"></a>데이터베이스 및 개체 크기 계산

다음 쿼리는 데이터베이스 크기(MB)를 반환합니다.

```sql
-- Calculates the size of the database.
SELECT SUM(CAST(FILEPROPERTY(name, 'SpaceUsed') AS bigint) * 8192.) / 1024 / 1024 AS DatabaseSizeInMB
FROM sys.database_files
WHERE type_desc = 'ROWS';
GO
```

다음 쿼리는 데이터베이스의 개별 개체 크기(MB)를 반환합니다.

```sql
-- Calculates the size of individual database objects.
SELECT sys.objects.name, SUM(reserved_page_count) * 8.0 / 1024
FROM sys.dm_db_partition_stats, sys.objects
WHERE sys.dm_db_partition_stats.object_id = sys.objects.object_id
GROUP BY sys.objects.name;
GO
```

## <a name="monitoring-connections"></a>연결 모니터링

[sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) 뷰를 사용하여 특정 Azure SQL Database 서버에 설정된 연결에 관한 정보 및 각 연결의 세부 정보를 검색할 수 있습니다. 또한 [sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) 뷰는 모든 활성 사용자 연결 및 내부 작업에 대한 정보를 검색할 때 유용합니다.
다음 쿼리는 현재 연결에 대한 정보를 검색합니다.

```sql
SELECT
    c.session_id, c.net_transport, c.encrypt_option,
    c.auth_scheme, s.host_name, s.program_name,
    s.client_interface_name, s.login_name, s.nt_domain,
    s.nt_user_name, s.original_login_name, c.connect_time,
    s.login_time
FROM sys.dm_exec_connections AS c
JOIN sys.dm_exec_sessions AS s
    ON c.session_id = s.session_id
WHERE c.session_id = @@SPID;
```

> [!NOTE]
> **sys.dm_exec_requests** 및 **sys.dm_exec_sessions views**를 실행하는 경우, 데이터베이스에 대한 **VIEW DATABASE STATE** 권한을 가지고 있으면 데이터베이스에 대한 모든 실행 세션이 표시되며, 그렇지 않으면 현재 세션만 표시됩니다.

## <a name="monitor-resource-use"></a>리소스 사용 모니터링

[SQL Database Query Performance Insight](sql-database-query-performance.md) 및 [쿼리 저장소](https://msdn.microsoft.com/library/dn817826.aspx)를 사용하여 리소스 사용량을 모니터링할 수 있습니다.

또한 다음 두 가지 뷰를 사용하여 사용을 모니터링할 수도 있습니다.

- [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)
- [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx)

### <a name="sysdmdbresourcestats"></a>sys.dm_db_resource_stats

모든 SQL Database에 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) 뷰를 사용할 수 있습니다. **sys.dm_db_resource_stats** 뷰는 서비스 계층과 관련된 최근 리소스 사용 데이터를 보여 줍니다. CPU, 데이터 IO, 로그 쓰기 및 메모리의 평균 백분율을 15초마다 기록되고 1시간 동안 유지됩니다.

이 뷰는 리소스 사용률에 대한 훨씬 구체적인 정보를 제공하므로 현재 상태 분석 또는 문제 해결에 **sys.dm_db_resource_stats**를 먼저 사용해야 합니다. 예를 들어 이 쿼리는 지난 시간 동안 현재 데이터베이스의 평균 및 최대 리소스 사용률을 보여 줍니다.

```sql
SELECT  
    AVG(avg_cpu_percent) AS 'Average CPU use in percent',
    MAX(avg_cpu_percent) AS 'Maximum CPU use in percent',
    AVG(avg_data_io_percent) AS 'Average data IO in percent',
    MAX(avg_data_io_percent) AS 'Maximum data IO in percent',
    AVG(avg_log_write_percent) AS 'Average log write use in percent',
    MAX(avg_log_write_percent) AS 'Maximum log write use in percent',
    AVG(avg_memory_usage_percent) AS 'Average memory use in percent',
    MAX(avg_memory_usage_percent) AS 'Maximum memory use in percent'
FROM sys.dm_db_resource_stats;  
```

다른 쿼리는 [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx)의 예를 참조하세요.

### <a name="sysresourcestats"></a>sys.resource_stats

**마스터** 데이터베이스의 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 뷰에는 특정 서비스 계층 및 컴퓨팅 크기에서 SQL 데이터베이스의 성능 수준을 모니터링할 수 있는 추가 정보가 포함되어 있습니다. 데이터는 5분마다 수집되어 약 14일 동안 보관됩니다. 이 뷰는 SQL Database가 리소스를 사용하는 방법에 대한 장기적인 기록 분석에 유용합니다.

다음 그래프는 한 주 동안 시간당 P2 컴퓨팅 크기의 프리미엄 데이터베이스에 사용된 CPU 리소스 사용량을 보여줍니다. 이 그래프는 월요일부터 5근무일과 애플리케이션 사용량이 훨씬 적은 주말까지 표시되어 있습니다.

![SQL Database 리소스 사용](./media/sql-database-performance-guidance/sql_db_resource_utilization.png)

이 데이터베이스의 현재 최고 CPU 부하는 P2 컴퓨팅 크기에 비해 약 50% 더 많습니다(화요일 낮). CPU가 애플리케이션의 리소스 프로필에서 가장 지배적인 요인인 경우 P2가 항상 워크로드를 충족하는 데 적합한 컴퓨팅 크기임을 확인할 수 있습니다. 애플리케이션이 시간이 지남에 따라 성장할 것으로 예상되는 경우 애플리케이션이 성능 수준 한도에 도달하지 않도록 추가 리소스 버퍼를 두는 것이 좋습니다. 컴퓨팅 크기를 늘리면 특히 대기 시간이 중요한 환경에서 데이터베이스가 요청을 효과적으로 처리할 수 있는 충분한 능력이 없을 때 발생할 수 있는 고객이 볼 수 있는 오류를 방지할 수 있습니다. 예를 들어 데이터베이스 호출의 결과를 기반으로 웹 페이지를 표시하는 애플리케이션을 지원하는 데이터베이스가 있습니다.

다른 애플리케이션 유형은 동일한 그래프를 다르게 해석할 수 있습니다. 예를 들어 애플리케이션에서 매일 급여 데이터를 처리하고 동일한 차트를 사용하는 경우와 같은 "일괄 처리 작업" 모델은 P1 컴퓨팅 크기로 충분할 수 있습니다. P1 컴퓨팅 크기는 DTU 100개를 제공하고 P2 컴퓨팅 크기는 DTU 200개를 제공합니다. P1 컴퓨팅 크기는 P2 계산 컴퓨팅의 절반 성능을 제공합니다. 따라서 P2에서 CPU 사용의 50%는 P1에서 100% CPU 사용과 같습니다. 애플리케이션에 시간 제한이 없는 경우 작업이 오늘 완료되기만 한다면 2시간이 소요되든 또는 2.5시간이 소요되든 중요하지 않을 수 있습니다. 이 범주의 애플리케이션은 P1 컴퓨팅 크기를 사용할 수도 있습니다. 하루 중 리소스 사용량이 낮은 시간대가 있다는 사실을 활용할 수 있습니다. 즉, "최고" 시간대의 작업을 하루 중 사용량이 낮은 시간대 중 하나로 나눌 수 있습니다. 작업을 매일 정시에 완료할 수 있는 경우 이러한 종류의 애플리케이션에는 P1 컴퓨팅 크기가 적합하며 비용도 절감할 수 있습니다.

Azure SQL Database는 각 서버에 있는 **마스터** 데이터베이스의 **sys.resource_stats** 뷰로 각 활성 데이터베이스에 사용된 리소스 정보를 표시합니다. 표의 데이터는 5분 간격으로 집계되어 있습니다. Basic, Standard, Premium 서비스 계층에서 데이터가 테이블에 표시될 때까지 5분 이상이 소요될 수 있어 이 데이터는 거의 실시간에 가까운 분석보다 기록 분석에 더 적합합니다. **sys.resource_stats** 뷰에 대한 쿼리는 데이터베이스의 최근 기록을 보여주며 선택한 예약이 필요 시 원하는 성능을 제공했는지 여부를 검증합니다.

> [!NOTE]
> 다음 예제에서 **sys.resource_stats**를 쿼리하려면 SQL Database 서버의 **master** 데이터베이스에 연결해야 합니다.

이 예제에서는 이 뷰의 데이터가 표시되는 방법을 보여줍니다.

```sql
SELECT TOP 10 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![sys.resource_stats 카탈로그 뷰](./media/sql-database-performance-guidance/sys_resource_stats.png)

다음 예제에서는 **sys.resource_stats** 카탈로그 뷰를 사용하여 SQL Database에서 리소스를 사용하는 방법에 대한 정보를 얻을 수 있는 다른 방법을 보여줍니다.

1. 데이터베이스 userdb1에서 지난 주의 리소스 사용을 확인하고자 할 때 이 쿼리를 실행할 수 있습니다.

    ```sql
    SELECT *
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND
        start_time > DATEADD(day, -7, GETDATE())
    ORDER BY start_time DESC;
    ```

2. 워크로드가 컴퓨팅 크기에 얼마나 적합한지 평가하려면 리소스 메트릭의 각 측면을 집중 분석해야 합니다. (예: CPU, 읽기, 쓰기, 작업자 수, 세션 수) 다음은 이러한 리소스 메트릭의 평균값 및 최대값에 대해 보고하기 위해 **sys.resource_stats**를 사용하여 수정한 쿼리입니다.

    ```sql
    SELECT
        avg(avg_cpu_percent) AS 'Average CPU use in percent',
        max(avg_cpu_percent) AS 'Maximum CPU use in percent',
        avg(avg_data_io_percent) AS 'Average physical data IO use in percent',
        max(avg_data_io_percent) AS 'Maximum physical data IO use in percent',
        avg(avg_log_write_percent) AS 'Average log write use in percent',
        max(avg_log_write_percent) AS 'Maximum log write use in percent',
        avg(max_session_percent) AS 'Average % of sessions',
        max(max_session_percent) AS 'Maximum % of sessions',
        avg(max_worker_percent) AS 'Average % of workers',
        max(max_worker_percent) AS 'Maximum % of workers'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

3. 각 리소스 메트릭의 평균값 및 최댓값에 대한 이 정보를 사용하여 워크로드가 선택한 컴퓨팅 크기에 얼마나 적합한지 평가할 수 있습니다. 일반적으로 **sys.resource_stats**의 평균값은 대상 크기에 맞게 사용하기에 적합한 기준선을 제공합니다. 기본 측정 기준이 되어야 합니다. 예를 들어 S2 컴퓨팅 크기와 함께 표준 서비스 계층을 사용할 수 있습니다. CPU 및 IO 읽기와 쓰기에 대한 평균 사용 비율은 40% 미만, 평균 작업자 수는 50 미만, 평균 세션 수는 200 미만입니다. 워크로드가 S1 컴퓨팅 크기에 적합할 수 있습니다. 데이터베이스가 작업자 및 세션 한도 이내에서 적합한지 여부를 쉽게 확인할 수 있습니다. 데이터베이스가 CPU, 읽기, 쓰기 기준의 낮은 컴퓨팅 크기에 적합한지 확인하려면 낮은 컴퓨팅 크기의 DTU 수를 현재 컴퓨팅 크기의 DTU 수로 나눈 다음, 그 결과에 100을 곱합니다.

    ```S1 DTU / S2 DTU * 100 = 20 / 50 * 100 = 40```

    결과는 백분율로 표시한 두 컴퓨팅 크기의 상대적 성능 차이입니다. 리소스 사용량이 이 양보다 많지 않은 경우 워크로드가 낮은 컴퓨팅 크기에 적합할 수 있습니다. 하지만 모든 범위의 리소스 사용 값을 살펴보고, 데이터베이스 워크로드가 낮은 컴퓨팅 크기에 적합한 빈도를 백분율로 확인해야 합니다. 다음 쿼리는 이 예에서 계산된 40%의 임계값을 기준으로 리소스 규격당 적합률을 출력합니다.

   ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log Write Fit Percent',
        (COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 40 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical Data IO Fit Percent'
    FROM sys.resource_stats
    WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    데이터베이스 서비스 계층을 기준으로 워크로드가 낮은 컴퓨팅 크기에 적합한지 확인할 수 있습니다. 데이터베이스 워크로드 목표가 99.9%이고 이전 쿼리가 세 가지 리소스 크기에 대해 전부 99.9%보다 큰 값을 반환하는 경우 워크로드가 낮은 컴퓨팅 크기에 적합할 가능성이 높습니다.

    적합도를 살펴보면 목표를 충족하기 위해 상위 컴퓨팅 크기로 이동해야 하는지 알 수 있습니다. 예를 들어 userdb1에서 지난 주에 대한 CPU 사용률은 다음과 같습니다.

   | 평균 CPU 사용률 | 최대 CPU 사용률 |
   | --- | --- |
   | 24.5 |100.00 |

    평균 CPU는 컴퓨팅 크기 한도의 약 1/4이며 데이터베이스 컴퓨팅 크기에 적합합니다. 하지만 최댓값은 데이터베이스가 컴퓨팅 크기 한도에 도달했다는 의미입니다. 다음 상위 컴퓨팅 크기로 이동해야 하나요? 워크로드가 100%에 도달하는 횟수를 살펴보고 데이터베이스 워크로드 목표와 비교해 보세요.

    ```sql
    SELECT
        (COUNT(database_name) - SUM(CASE WHEN avg_cpu_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'CPU fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_log_write_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Log write fit percent'
        ,(COUNT(database_name) - SUM(CASE WHEN avg_data_io_percent >= 100 THEN 1 ELSE 0 END) * 1.0) / COUNT(database_name) AS 'Physical data IO fit percent'
        FROM sys.resource_stats
        WHERE database_name = 'userdb1' AND start_time > DATEADD(day, -7, GETDATE());
    ```

    이 쿼리가 세 가지 리소스 크기에 대해 전부 99.9% 미만의 값을 반환하는 경우 다음 상위 컴퓨팅 크기로 이동하거나 애플리케이션 튜닝 기술을 사용하여 SQL 데이터베이스의 부하를 줄이는 방안을 고려해야 합니다.

4. 이 연습에서는 향후 예상되는 워크로드 증가도 고려합니다.

탄력적 풀의 경우 이 섹션에서 설명하는 기법을 사용하여 풀의 개별 데이터베이스를 모니터링할 수 있습니다. 하지만 전체적으로 풀을 모니터링할 수도 있습니다. 자세한 내용은 [탄력적 풀 모니터링 및 관리](sql-database-elastic-pool-manage-portal.md)를 참조하세요.

### <a name="maximum-concurrent-requests"></a>동시 요청이 최대인 경우

동시 요청 수를 보려면 SQL Database에서 이 Transact-SQL 쿼리를 실행하세요.

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R;
    ```

온-프레미스 SQL Server 데이터베이스의 워크로드를 분석하려면 이 쿼리를 수정하여 분석할 특정 데이터베이스에서 필터링해야 합니다. 예를 들어 MyDatabase라는 온-프레미스 데이터베이스가 있다면 이 Transact-SQL 쿼리는 해당 데이터베이스의 동시 요청 수를 반환합니다.

    ```sql
    SELECT COUNT(*) AS [Concurrent_Requests]
    FROM sys.dm_exec_requests R
    INNER JOIN sys.databases D ON D.database_id = R.database_id
    AND D.name = 'MyDatabase';
    ```

이는 단일 시점의 스냅숏일 뿐입니다. 워크로드 및 동시 요청 요구 사항을 더 깊이 이해하려면 시간 변화에 따라 여러 샘플을 수집해야 합니다.

### <a name="maximum-concurrent-logins"></a>최대 동시 로그인 수

사용자 및 애플리케이션 패턴을 분석하여 로그인 빈도를 파악할 수 있습니다. 또한 테스트 환경에서 실제 부하를 실행하여 이 문서에서 설명한 이 한도 또는 기타 한도에 도달하지 않도록 보장할 수 있습니다. 동시 로그인 수 또는 기록을 표시할 수 있는 단일 쿼리 또는 동적 관리 뷰(DMV)는 없습니다.

여러 클라이언트에서 동일한 연결 문자열을 사용하는 경우 서비스에서는 각 로그인을 인증합니다. 10명의 사용자가 동일한 사용자 이름 및 암호를 사용하여 데이터베이스에 동시에 연결하는 경우 동시 로그인 수는 10개가 됩니다. 이 한도는 로그인 및 인증 기간에만 적용됩니다. 따라서 동일한 10명의 사용자가 특정 데이터베이스에 순차적으로 연결하는 경우 동시 로그인 수는 절대로 1보다 높을 수 없습니다.

> [!NOTE]
> 현재 이 한도는 탄력적 풀의 데이터베이스에는 적용되지 않습니다.

### <a name="maximum-sessions"></a>최대 세션 수

현재 활성 세션 수를 보려면 SQL Database에서 이 Transact-SQL 쿼리를 실행하세요.

    SELECT COUNT(*) AS [Sessions]
    FROM sys.dm_exec_connections

온-프레미스 SQL Server 워크로드를 분석하려는 경우 특정 데이터베이스에 집중하도록 쿼리를 수정하세요. 이 쿼리는 데이터베이스를 Azure SQL Database로 이동을 고려할 때 가능한 세션 요구 사항을 확인하는 데 도움이 됩니다.

    SELECT COUNT(*)  AS [Sessions]
    FROM sys.dm_exec_connections C
    INNER JOIN sys.dm_exec_sessions S ON (S.session_id = C.session_id)
    INNER JOIN sys.databases D ON (D.database_id = S.database_id)
    WHERE D.name = 'MyDatabase'

역시 이러한 쿼리도 지정 시간 수를 반환합니다. 시간이 지남에 따라 여러 샘플을 수집하는 경우 자신의 세션 사용을 잘 이해해야 합니다.

SQL Database 분석의 경우 [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) 뷰를 쿼리하고 **active_session_count** 열을 검토하여 세션에 대한 통계 자료를 얻을 수 있습니다.

## <a name="monitoring-query-performance"></a>쿼리 성능 모니터링

느린 속도로 또는 장시간 실행하는 쿼리는 많은 양의 시스템 리소스를 사용할 수 있습니다. 이 섹션에서는 동적 관리 뷰를 사용하여 몇 가지 일반적인 쿼리 성능 문제를 감지하는 방법을 보여 줍니다. 오래 되었지만 여전히 유익한 문제 해결 참고 자료는 Microsoft Technet의 [SQL Server 2008의 성능 문제해결](https://download.microsoft.com/download/D/B/D/DBDE7972-1EB9-470A-BA18-58849DB3EB3B/TShootPerfProbs2008.docx) 문서입니다.

### <a name="finding-top-n-queries"></a>최상위 N개 쿼리 찾기

다음 예제는 평균 CPU 시간별로 상위 5개의 쿼리에 대한 정보를 반환합니다. 이 예제는 쿼리 해시에 따라 쿼리를 집계하므로 논리적으로 동일한 쿼리는 해당 누적 리소스 소비량에 따라 그룹화됩니다.

    ```sql
    SELECT TOP 5 query_stats.query_hash AS "Query Hash",
       SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
       MIN(query_stats.statement_text) AS "Statement Text"
    FROM
       (SELECT QS.*,
        SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
        ((CASE statement_end_offset
           WHEN -1 THEN DATALENGTH(ST.text)
           ELSE QS.statement_end_offset END
           - QS.statement_start_offset)/2) + 1) AS statement_text
    FROM sys.dm_exec_query_stats AS QS
    CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
    GROUP BY query_stats.query_hash
    ORDER BY 2 DESC;
    ```

### <a name="monitoring-blocked-queries"></a>차단된 쿼리 모니터링

느린 속도로 또는 장시간 실행하는 쿼리는 과도한 리소스 소비에 기여하고 차단된 쿼리에 따른 결과일 수 있습니다. 차단의 원인으로 부실한 애플리케이션 디자인, 잘못된 쿼리 계획, 유용한 인덱스 부족 등이 있습니다. sys.dm_tran_locks 뷰를 사용하여 Azure SQL Database의 현재 잠금 작업에 관한 정보를 가져올 수 있습니다. 예제 코드는 SQL Server 온라인 설명서의 [sys.dm_tran_locks (Transact-SQL)](https://msdn.microsoft.com/library/ms190345.aspx)를 참조하세요.

### <a name="monitoring-query-plans"></a>쿼리 계획 모니터링

또한 비효율적인 쿼리 계획 때문에 CPU 사용량이 증가할 수 있습니다. 다음 예제에서는 [sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) 뷰를 사용하여 가장 많은 누적 CPU를 사용하는 쿼리를 확인합니다.

    ```sql
    SELECT
       highest_cpu_queries.plan_handle,
       highest_cpu_queries.total_worker_time,
       q.dbid,
       q.objectid,
       q.number,
       q.encrypted,
       q.[text]
    FROM
       (SELECT TOP 50
        qs.plan_handle,
        qs.total_worker_time
    FROM
        sys.dm_exec_query_stats qs
    ORDER BY qs.total_worker_time desc) AS highest_cpu_queries
    CROSS APPLY sys.dm_exec_sql_text(plan_handle) AS q
    ORDER BY highest_cpu_queries.total_worker_time DESC;
    ```

## <a name="see-also"></a>참고 항목

[SQL Database 소개](sql-database-technical-overview.md)
