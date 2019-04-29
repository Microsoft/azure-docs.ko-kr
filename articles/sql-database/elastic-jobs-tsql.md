---
title: T-SQL(Transact-SQL)을 사용하여 Azure SQL Elastic Database 작업 만들기 및 관리 | Microsoft Docs
description: T-SQL(Transact-SQL)을 사용하여 Elastic Database 작업 에이전트가 있는 여러 데이터베이스에서 스크립트를 실행합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
ms.author: jaredmoo
author: jaredmoo
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 59e0e4cf82af9851dacf3ec030575ed392571331
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475816"
---
# <a name="use-transact-sql-t-sql-to-create-and-manage-elastic-database-jobs"></a>T-SQL(Transact-SQL)을 사용하여 Elastic Database 작업 만들기 및 관리

이 문서에서는 T-SQL을 사용하여 탄력적 작업을 시작할 수 있는 다양한 예제 시나리오를 제공합니다.

예제에서는 [*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에서 사용할 수 있는 [저장 프로시저](#job-stored-procedures)와 [보기](#job-views)를 사용합니다.

T-SQL(Transact-SQL)은 작업을 생성, 구성, 실행 및 관리하는 데 사용됩니다. 탄력적 작업 에이전트 만들기는 T-SQL에서 지원되지 않으므로, 먼저 포털 또는 [PowerShell](elastic-jobs-powershell.md#create-the-elastic-job-agent)을 사용하여 *탄력적 작업 에이전트*를 만들어야 합니다.


## <a name="create-a-credential-for-job-execution"></a>작업 실행에 대한 자격 증명 만들기

자격 증명은 스크립트 실행을 위해 대상 데이터베이스에 연결하는 데 사용됩니다. 자격 증명에는 스크립트를 성공적으로 실행하기 위해 대상 그룹에서 지정한 데이터베이스에 대한 적절한 권한이 필요합니다. 서버 및/또는 풀 대상 그룹 멤버를 사용하는 경우, 작업 실행 시 서버 및/또는 풀을 확장하기 전에 자격 증명을 새로 고치는 데 사용할 마스터 자격 증명을 만드는 것이 매우 좋습니다. 데이터베이스 범위 자격 증명은 작업 에이전트 데이터베이스에 만들어집니다. 대상 데이터베이스에 대한 *로그인 만들기* 및 *로그인에서 사용자를 만들어 데이터베이스 로그인 권한 부여*에서 동일한 자격 증명을 사용해야 합니다.


```sql
--Connect to the job database specified when creating the job agent

-- Create a db master key if one does not already exist, using your own password.  
CREATE MASTER KEY ENCRYPTION BY PASSWORD='<EnterStrongPasswordHere>';  
  
-- Create a database scoped credential.  
CREATE DATABASE SCOPED CREDENTIAL myjobcred WITH IDENTITY = 'jobcred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO

-- Create a database scoped credential for the master database of server1.
CREATE DATABASE SCOPED CREDENTIAL mymastercred WITH IDENTITY = 'mastercred',
    SECRET = '<EnterStrongPasswordHere>'; 
GO
```

## <a name="create-a-target-group-servers"></a>대상 그룹(서버) 만들기

다음 예제에서는 서버의 모든 데이터베이스에 대해 작업을 실행하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.


```sql
-- Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group 'ServerGroup1'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
'ServerGroup1',
@target_type = 'SqlServer',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net'

--View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name='ServerGroup1';
SELECT * FROM jobs.target_group_members WHERE target_group_name='ServerGroup1';
```


## <a name="exclude-an-individual-database"></a>개별 데이터베이스 제외

다음 예제에서는 *MappingDB* 데이터베이스를 제외한 SQL Database 서버의 모든 데이터베이스에 대해 작업을 실행하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing server(s)
EXEC [jobs].sp_add_target_group N'ServerGroup'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net'
GO

-- Add a server target member
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name='server2.database.windows.net'
GO

--Exclude a database target member from the server target group
EXEC [jobs].sp_add_target_group_member
@target_group_name = N'ServerGroup',
@membership_type = N'Exclude',
@target_type = N'SqlDatabase',
@server_name = N'server1.database.windows.net',
@database_name =N'MappingDB'
GO

--View the recently created target group and target group members
SELECT * FROM [jobs].target_groups WHERE target_group_name = N'ServerGroup';
SELECT * FROM [jobs].target_group_members WHERE target_group_name = N'ServerGroup';
```


## <a name="create-a-target-group-pools"></a>대상 그룹(풀) 만들기

다음 예제에서는 하나 이상의 탄력적 풀에 있는 모든 데이터베이스를 대상으로 지정하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- Add a target group containing pool(s)
EXEC jobs.sp_add_target_group 'PoolGroup'

-- Add an elastic pool(s) target member
EXEC jobs.sp_add_target_group_member
'PoolGroup',
@target_type = 'SqlElasticPool',
@refresh_credential_name='mymastercred', --credential required to refresh the databases in server
@server_name='server1.database.windows.net',
@elastic_pool_name='ElasticPool-1'

-- View the recently created target group and target group members
SELECT * FROM jobs.target_groups WHERE target_group_name = N'PoolGroup';
SELECT * FROM jobs.target_group_members WHERE target_group_name = N'PoolGroup';
```


## <a name="deploy-new-schema-to-many-databases"></a>여러 데이터베이스에 새 스키마 배포

다음 예제에서는 새 스키마를 모든 데이터베이스에 배포하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.


```sql
--Connect to the job database specified when creating the job agent

--Add job for create table
EXEC jobs.sp_add_job @job_name='CreateTableTest', @description='Create Table Test'

-- Add job step for create table
EXEC jobs.sp_add_jobstep @job_name='CreateTableTest',
@command=N'IF NOT EXISTS (SELECT * FROM sys.tables 
            WHERE object_id = object_id(''Test''))
CREATE TABLE [dbo].[Test]([TestId] [int] NOT NULL);',
@credential_name='myjobcred',
@target_group_name='PoolGroup'
```


## <a name="data-collection-using-built-in-parameters"></a>기본 제공 매개 변수를 사용하여 데이터 수집

대부분의 데이터 수집 시나리오에서 다음 스크립팅 변수 중 일부를 포함하여 작업 결과를 사후 처리하는 것이 유용할 수 있습니다.

- $(job_name)
- $(job_id)
- $(job_version)
- $(step_id)
- $(step_name)
- $(job_execution_id)
- $(job_execution_create_time)
- $(target_group_name)

예를 들어 동일한 작업 실행의 모든 결과를 함께 그룹화하려면 다음 명령과 같이 *$(job_execution_id)* 를 사용합니다.


```sql
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());'
```


## <a name="monitor-database-performance"></a>데이터베이스 성능 모니터링

다음 예제에서는 새 데이터베이스를 만들어 여러 데이터베이스에서 성능 데이터를 수집합니다.

기본적으로 작업 에이전트는 반환된 결과를 저장할 테이블을 만듭니다. 결과적으로 출력 자격 증명에 사용되는 자격 증명과 관련된 로그인을 위해서는 이를 수행할 충분한 권한이 있어야 합니다. 테이블을 미리 수동으로 만들려는 경우 다음 속성이 있어야 합니다.
1. 결과 집합에 대한 데이터 형식 및 올바른 이름을 사용하는 열입니다.
2. 고유 식별자의 데이터 형식을 사용하는 internal_execution_id에 대한 추가 열입니다.
3. 명명 된 비클러스터형 인덱스를 `IX_<TableName>_Internal_Execution_ID` internal_execution_id 열입니다.

[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- Add a job to collect perf results
EXEC jobs.sp_add_job @job_name ='ResultsJob', @description='Collection Performance data from all customers'

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsJob',
@command= N' SELECT DB_NAME() DatabaseName, $(job_execution_id) AS job_execution_id, * FROM sys.dm_db_resource_stats WHERE end_time > DATEADD(mi, -20, GETDATE());',
@credential_name='myjobcred',
@target_group_name='PoolGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='<resultsdb>',
@output_table_name='<resutlstable>'
Create a job to monitor pool performance
--Connect to the job database specified when creating the job agent

-- Add a target group containing master database
EXEC jobs.sp_add_target_group 'MasterGroup'

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name='MasterGroup',
@target_type='SqlDatabase',
@server_name='server1.database.windows.net',
@database_name='master'

-- Add a job to collect perf results
EXEC jobs.sp_add_job
@job_name='ResultsPoolsJob',
@description='Demo: Collection Performance data from all pools',
@schedule_interval_type='Minutes',
@schedule_interval_count=15

-- Add a job step w/ schedule to collect results
EXEC jobs.sp_add_jobstep
@job_name='ResultsPoolsJob',
@command=N'declare @now datetime
DECLARE @startTime datetime
DECLARE @endTime datetime
DECLARE @poolLagMinutes datetime
DECLARE @poolStartTime datetime
DECLARE @poolEndTime datetime
SELECT @now = getutcdate ()
SELECT @startTime = dateadd(minute, -15, @now)
SELECT @endTime = @now
SELECT @poolStartTime = dateadd(minute, -30, @startTime)
SELECT @poolEndTime = dateadd(minute, -30, @endTime)

SELECT elastic_pool_name , end_time, elastic_pool_dtu_limit, avg_cpu_percent, avg_data_io_percent, avg_log_write_percent, max_worker_percent, max_session_percent,
        avg_storage_percent, elastic_pool_storage_limit_mb FROM sys.elastic_pool_resource_stats
        WHERE end_time > @poolStartTime and end_time <= @poolEndTime;
'),
@credential_name='myjobcred',
@target_group_name='MasterGroup',
@output_type='SqlDatabase',
@output_credential_name='myjobcred',
@output_server_name='server1.database.windows.net',
@output_database_name='resultsdb',
@output_table_name='resutlstable'
```


## <a name="view-job-definitions"></a>작업 정의 보기

다음 예제에서는 현재 작업 정의를 보는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- View all jobs
SELECT * FROM jobs.jobs

-- View the steps of the current version of all jobs
SELECT js.* FROM jobs.jobsteps js
JOIN jobs.jobs j 
  ON j.job_id = js.job_id AND j.job_version = js.job_version

-- View the steps of all versions of all jobs
select * from jobs.jobsteps
```


## <a name="begin-ad-hoc-execution-of-a-job"></a>임시 작업 실행 시작

다음 예제에서는 작업을 즉시 시작하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- Execute the latest version of a job
EXEC jobs.sp_start_job 'CreateTableTest'

-- Execute the latest version of a job and receive the execution id
declare @je uniqueidentifier
exec jobs.sp_start_job 'CreateTableTest', @job_execution_id = @je output
select @je

select * from jobs.job_executions where job_execution_id = @je

-- Execute a specific version of a job (e.g. version 1)
exec jobs.sp_start_job 'CreateTableTest', 1
```


## <a name="schedule-execution-of-a-job"></a>작업 실행 예약

다음 예제에서는 나중에 실행하도록 작업을 예약하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_update_job
@job_name='ResultsJob',
@enabled=1,
@schedule_interval_type='Minutes',
@schedule_interval_count=15
```

## <a name="monitor-job-execution-status"></a>작업 실행 상태 모니터링

다음 예제에서는 모든 작업에 대한 실행 상태 세부 정보를 보는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

--View top-level execution status for the job named 'ResultsPoolJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' and step_id IS NULL
ORDER BY start_time DESC

--View all top-level execution status for all jobs
SELECT * FROM jobs.job_executions WHERE step_id IS NULL
ORDER BY start_time DESC

--View all execution statuses for job named 'ResultsPoolsJob'
SELECT * FROM jobs.job_executions 
WHERE job_name = 'ResultsPoolsJob' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions 
WHERE is_active = 1
ORDER BY start_time DESC
```


## <a name="cancel-a-job"></a>작업 취소

다음 예제에서는 작업을 취소하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- View all active executions to determine job execution id
SELECT * FROM jobs.job_executions 
WHERE is_active = 1 AND job_name = 'ResultPoolsJob'
ORDER BY start_time DESC
GO

-- Cancel job execution with the specified job execution id
EXEC jobs.sp_stop_job '01234567-89ab-cdef-0123-456789abcdef'
```


## <a name="delete-old-job-history"></a>이전 작업 기록 삭제

다음 예제에서는 특정 날짜 이전의 작업 기록을 삭제하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

-- Delete history of a specific job’s executions older than the specified date
EXEC jobs.sp_purge_jobhistory @job_name='ResultPoolsJob', @oldest_date='2016-07-01 00:00:00'

--Note: job history is automatically deleted if it is >45 days old
```

## <a name="delete-a-job-and-all-its-job-history"></a>작업 및 관련된 모든 작업 기록 삭제

다음 예제에서는 작업 및 관련된 모든 작업 기록을 삭제하는 방법을 보여 줍니다.  
[*작업 데이터베이스*](sql-database-job-automation-overview.md#job-database)에 연결하고 다음 명령을 실행합니다.

```sql
--Connect to the job database specified when creating the job agent

EXEC jobs.sp_delete_job @job_name='ResultsPoolsJob'

--Note: job history is automatically deleted if it is >45 days old
```




## <a name="job-stored-procedures"></a>작업 저장 프로시저

[작업 데이터베이스](sql-database-job-automation-overview.md#job-database)에 있는 저장 프로시저는 다음과 같습니다.



|저장 프로시저  |설명  |
|---------|---------|
|[sp_add_job](#sp_add_job)     |     새 작업을 추가합니다.    |
|[sp_update_job](#sp_update_job)    |      기존 작업을 업데이트합니다.   |
|[sp_delete_job](#sp_delete_job)     |      기존 작업을 삭제합니다.   |
|[sp_add_jobstep](#sp_add_jobstep)    |    작업에 단계를 추가합니다.     |
|[sp_update_jobstep](#sp_update_jobstep)     |     작업 단계를 업데이트합니다.    |
|[sp_delete_jobstep](#sp_delete_jobstep)     |     작업 단계를 삭제합니다.    |
|[sp_start_job](#sp_start_job)    |  작업 실행을 시작합니다.       |
|[sp_stop_job](#sp_stop_job)     |     작업 실행을 중지합니다.   |
|[sp_add_target_group](#sp_add_target_group)    |     대상 그룹을 추가합니다.    |
|[sp_delete_target_group](#sp_delete_target_group)     |    대상 그룹을 삭제합니다.     |
|[sp_add_target_group_member](#sp_add_target_group_member)     |    대상 그룹에 데이터베이스 또는 데이터베이스 그룹을 추가합니다.     |
|[sp_delete_target_group_member](#sp_delete_target_group_member)     |     대상 그룹에서 대상 그룹 멤버를 제거합니다.    |
|[sp_purge_jobhistory](#sp_purge_jobhistory)    |    작업에 대한 기록 레코드를 제거합니다.     |





### <a name="spaddjob"></a>sp_add_job

새 작업을 추가합니다. 
  
#### <a name="syntax"></a>구문  
  

```sql
[jobs].sp_add_job [ @job_name = ] 'job_name'  
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
    [ , [ @job_id = ] job_id OUTPUT ]
```

  
#### <a name="arguments"></a>인수  

[ **\@job_name =** ] 'job_name'  
작업의 이름입니다. 이름은 고유해야 하며, 퍼센트(%) 문자는 포함할 수 없습니다. job_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@description =** ] 'description'  
작업에 대한 설명입니다. description은 nvarchar(512) 형식이며, 기본값은 NULL입니다. description이 생략되면 빈 문자열이 사용됩니다.

[ **\@enabled =** ] enabled  
작업 일정이 사용되는지 여부입니다. enabled는 bit 형식이며, 기본값은 0(사용 안 함)입니다. 0인 경우 작업이 사용되지 않으며 일정에 따라 실행되지 않습니다. 그러나 수동으로는 실행할 수 있습니다. 1인 경우 작업이 일정에 따라 실행되며, 수동으로 실행할 수도 있습니다.

[ **\@schedule_interval_type =**] schedule_interval_type  
값은 작업을 실행할 시기를 나타냅니다. schedule_interval_type은 nvarchar(50) 형식이며, 기본값은 Once이고, 다음 값 중 하나일 수 있습니다.
- 'Once',
- 'Minutes',
- 'Hours',
- 'Days',
- 'Weeks',
- 'Months'

[ **\@schedule_interval_count =** ] schedule_interval_count  
각 작업 실행 간에 발생할 schedule_interval_count 기간의 수입니다. schedule_interval_count는 int 형식이며, 기본값은 1입니다. 값은 1보다 크거나 같아야 합니다.

[ **\@schedule_start_time =** ] schedule_start_time  
작업 실행을 시작할 수 있는 날짜입니다. schedule_start_time은 DATETIME2 형식이며, 기본값은 0001-01-01 00:00:00.0000000입니다.

[ **\@schedule_end_time =** ] schedule_end_time  
작업 실행을 중지할 수 있는 날짜입니다. schedule_end_time은 DATETIME2 형식이며, 기본값은 9999-12-31 11:59:59.0000000입니다. 

[ **\@job_id =** ] job_id OUTPUT  
성공적으로 만들어지면 작업에 할당되는 작업 ID 번호입니다. job_id는 uniqueidentifier 형식의 출력 변수입니다.

#### <a name="return-code-values"></a>반환 코드 값

0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
sp_add_job은 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 실행해야 합니다.
sp_add_job을 실행하여 작업이 추가되면 sp_add_jobstep을 사용하여 작업에 대한 활동을 수행하는 단계를 추가할 수 있습니다. 작업의 초기 버전 번호는 0이며, 첫 번째 단계가 추가되면 1로 증가합니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.

- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

### <a name="spupdatejob"></a>sp_update_job

기존 작업을 업데이트합니다.

#### <a name="syntax"></a>구문

```sql
[jobs].sp_update_job [ @job_name = ] 'job_name'  
    [ , [ @new_name = ] 'new_name' ]
    [ , [ @description = ] 'description' ]   
    [ , [ @enabled = ] enabled ]
    [ , [ @schedule_interval_type = ] schedule_interval_type ]  
    [ , [ @schedule_interval_count = ] schedule_interval_count ]   
    [ , [ @schedule_start_time = ] schedule_start_time ]   
    [ , [ @schedule_end_time = ] schedule_end_time ]   
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
업데이트할 작업의 이름입니다. job_name은 nvarchar(128) 형식입니다.

[ **\@new_name =** ] 'new_name'  
작업의 새 이름입니다. new_name은 nvarchar(128) 형식입니다.

[ **\@description =** ] 'description'  
작업에 대한 설명입니다. description은 nvarchar(512) 형식입니다.

[ **\@enabled =** ] enabled  
작업 일정이 사용되는지(1), 아니면 사용되지 않는지(0) 여부를 지정합니다. enabled는 bit 형식입니다.

[ **\@schedule_interval_type=** ] schedule_interval_type  
값은 작업을 실행할 시기를 나타냅니다. schedule_interval_type은 nvarchar(50) 형식이며, 다음 값 중 하나일 수 있습니다.

- 'Once',
- 'Minutes',
- 'Hours',
- 'Days',
- 'Weeks',
- 'Months'

[ **\@schedule_interval_count=** ] schedule_interval_count  
각 작업 실행 간에 발생할 schedule_interval_count 기간의 수입니다. schedule_interval_count는 int 형식이며, 기본값은 1입니다. 값은 1보다 크거나 같아야 합니다.

[ **\@schedule_start_time=** ] schedule_start_time  
작업 실행을 시작할 수 있는 날짜입니다. schedule_start_time은 DATETIME2 형식이며, 기본값은 0001-01-01 00:00:00.0000000입니다.

[ **\@schedule_end_time=** ] schedule_end_time  
작업 실행을 중지할 수 있는 날짜입니다. schedule_end_time은 DATETIME2 형식이며, 기본값은 9999-12-31 11:59:59.0000000입니다. 

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
sp_add_job을 실행하여 작업이 추가되면 sp_add_jobstep을 사용하여 작업에 대한 활동을 수행하는 단계를 추가할 수 있습니다. 작업의 초기 버전 번호는 0이며, 첫 번째 단계가 추가되면 1로 증가합니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.



### <a name="spdeletejob"></a>sp_delete_job

기존 작업을 삭제합니다.

#### <a name="syntax"></a>구문

```sql
[jobs].sp_delete_job [ @job_name = ] 'job_name'
    [ , [ @force = ] force ]
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
삭제할 작업의 이름입니다. job_name은 nvarchar(128) 형식입니다.

[ **\@force =** ] force  
작업 실행이 진행 중인 경우 해당 작업을 삭제할지 여부를 지정하고, 진행 중인 모든 실행을 취소할지(1), 아니면 작업 실행이 진행 중인 경우 실패할지(0) 여부를 지정합니다. force는 bit 형식입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
작업을 삭제하면 작업 기록이 자동으로 삭제됩니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.



### <a name="spaddjobstep"></a>sp_add_jobstep

작업에 단계를 추가합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_add_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] step_name ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_subscription_id = ] 'output_subscription_id' ]   
     [ , [ @output_resource_group_name = ] 'output_resource_group_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>인수

[ **\@job_name =** ] 'job_name'  
단계를 추가할 작업의 이름입니다. job_name은 nvarchar(128) 형식입니다.

[ **\@step_id =** ] step_id  
작업 단계에 대한 시퀀스 ID 번호입니다. 단계 ID 번호는 1에서 시작하여 간격 없이 증가합니다. 이 ID가 이미 기존 단계에 있으면 해당 단계와 모든 다음 단계의 ID가 증가하여 이 새 단계가 시퀀스에 삽입될 수 있습니다. 지정되지 않으면 step_id가 자동으로 단계 시퀀스의 마지막에 할당됩니다. step_id는 int 형식입니다.

[ **\@step_name =** ] step_name  
단계의 이름입니다. 편의상 기본 이름이 'JobStep'인 작업의 첫 번째 단계를 제외하고는 반드시 지정해야 합니다. step_name은 nvarchar(128) 형식입니다.

[ **\@command_type =** ] 'command_type'  
이 jobstep에서 실행되는 명령의 유형입니다. command_type은 nvarchar(50) 형식이며, 기본값은 TSql(@command_type 매개 변수의 값이 T-SQL 스크립트임을 의미함)입니다.

지정되는 경우 이 값은 TSql이어야 합니다.

[ **\@command_source =** ] 'command_source'  
명령이 저장된 위치의 유형입니다. command_source는 nvarchar(50) 형식이며, 기본값은 Inline(@command_source 매개 변수의 값이 명령의 리터럴 텍스트임을 의미함)입니다.

지정되는 경우 이 값은 Inline이어야 합니다.

[ **\@command =** ] 'command'  
명령은 유효한 T-SQL 스크립트여야 하며, 이 작업 단계에서 실행됩니다. 명령은 nvarchar(max) 형식이며, 기본값은 NULL입니다.

[ **\@credential_name =** ] 'credential_name'  
이 단계를 실행할 때 대상 그룹 내의 각 대상 데이터베이스에 연결하는 데 사용되는 이 작업 제어 데이터베이스에 저장된 데이터베이스 범위 자격 증명의 이름입니다. credential_name은 nvarchar(128) 형식입니다.

[ **\@target_group_name =** ] 'target-group_name'  
작업 단계를 실행할 대상 데이터베이스가 포함된 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식입니다.

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
초기 실행 시도에서 작업 단계가 실패하는 경우 첫 번째 재시도가 시작될 때까지의 지연 시간입니다. initial_retry_interval_seconds는 int 형식이며, 기본값은 1입니다.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
재시도 간의 최대 지연 시간입니다. 재시도 간의 지연 시간이 이 값보다 커지면 대신 이 값으로 제한됩니다. maximum_retry_interval_seconds는 int 형식이며, 기본값은 120입니다.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
여러 작업 단계 실행 시도가 실패하는 경우 재시도 지연 시간에 적용할 승수입니다. 예를 들어 첫 번째 재시도의 지연 시간이 5초이고 백오프 승수가 2.0인 경우 두 번째 재시도의 지연 시간은 10초, 세 번째 재시도의 지연 시간은 20초가 됩니다. retry_interval_backoff_multiplier는 real 형식이며, 기본값은 2.0입니다.

[ **\@retry_attempts =** ] retry_attempts  
초기 시도가 실패하는 경우 실행을 다시 시도하는 횟수입니다. 예를 들어 retry_attempts 값이 10이면 초기 시도 횟수는 1회이고, 재시도 횟수는 10회이며, 총 시도 횟수는 11회입니다. 마지막 재시도 시도가 실패하면 lifecycle이 Failed(실패)인 채로 작업 실행이 종료됩니다. retry_attempts는 int 형식이며, 기본값은 10입니다.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
단계를 실행하는 데 허용되는 최대 시간입니다. 이 시간을 초과하면 lifecycle이 TimedOut(시간이 초과됨)인 채로 작업 실행이 종료됩니다. step_timeout_seconds는 int 형식이며, 기본값은 43,200초(12시간)입니다.

[ **\@output_type =** ] 'output_type'  
null이 아닌 경우 명령의 첫 번째 결과 집합이 기록되는 대상의 유형입니다. output_type은 nvarchar(50) 형식이며, 기본값은 NULL입니다.

지정되는 경우 값은 SqlDatabase여야 합니다.

[ **\@output_credential_name =** ] 'output_credential_name'  
null이 아닌 경우 출력 대상 데이터베이스에 연결하는 데 사용되는 데이터베이스 범위 자격 증명의 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_credential_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@output_subscription_id =** ] 'output_subscription_id'  
설명이 필요합니다.

[ **\@output_resource_group_name =** ] 'output_resource_group_name'  
설명이 필요합니다.

[ **\@output_server_name =** ] 'output_server_name'  
null이 아닌 경우 출력 대상 데이터베이스가 포함된 서버의 정규화된 DNS 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_server_name은 nvarchar(256) 형식이며, 기본값은 NULL입니다.

[ **\@output_database_name =** ] 'output_database_name'  
null이 아닌 경우 출력 대상 테이블이 포함된 데이터베이스의 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_database_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@output_schema_name =** ] 'output_schema_name'  
null이 아닌 경우 출력 대상 테이블이 포함된 SQL 스키마의 이름입니다. output_type이 SqlDatabase와 같으면 기본값은 dbo입니다. output_schema_name은 nvarchar(128) 형식입니다.

[ **\@output_table_name =** ] 'output_table_name'  
null이 아닌 경우 명령의 첫 번째 결과 집합이 기록되는 테이블의 이름입니다. 테이블이 아직 없으면 반환되는 결과 집합의 스키마를 기반으로 하여 만들어집니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_table_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@job_version =** ] job_version OUTPUT  
새 작업 버전 번호가 할당될 출력 매개 변수입니다. job_version은 int 형식입니다.

[  **\@max_parallelism =** ] max_parallelism 출력  
탄력적 풀당 최대 병렬 처리 수준입니다. 설정되는 경우 작업 단계가 탄력적 풀당 최댓값 이하의 여러 데이터베이스에서만 실행되도록 제한됩니다. 이는 대상 그룹에 직접 포함되어 있거나 대상 그룹에 포함된 서버 내에 있는 각 탄력적 풀에 적용됩니다. max_parallelism은 int 형식입니다.


#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
sp_add_jobstep이 성공하면 작업의 현재 버전 번호가 증가합니다. 다음에 작업이 실행될 때 새 버전이 사용됩니다. 작업이 현재 실행 중이면 해당 실행에는 새 단계가 포함되지 않습니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.  

- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.



### <a name="spupdatejobstep"></a>sp_update_jobstep

작업 단계를 업데이트합니다.

#### <a name="syntax"></a>구문

```sql
[jobs].sp_update_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]   
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @new_id = ] new_id ]   
     [ , [ @new_name = ] 'new_name' ]   
     [ , [ @command_type = ] 'command_type' ]   
     [ , [ @command_source = ] 'command_source' ]  
     , [ @command = ] 'command'
     , [ @credential_name = ] 'credential_name'
     , [ @target_group_name = ] 'target_group_name'
     [ , [ @initial_retry_interval_seconds = ] initial_retry_interval_seconds ]   
     [ , [ @maximum_retry_interval_seconds = ] maximum_retry_interval_seconds ]   
     [ , [ @retry_interval_backoff_multiplier = ] retry_interval_backoff_multiplier ]   
     [ , [ @retry_attempts = ] retry_attempts ]   
     [ , [ @step_timeout_seconds = ] step_timeout_seconds ]   
     [ , [ @output_type = ] 'output_type' ]   
     [ , [ @output_credential_name = ] 'output_credential_name' ]   
     [ , [ @output_server_name = ] 'output_server_name' ]   
     [ , [ @output_database_name = ] 'output_database_name' ]   
     [ , [ @output_schema_name = ] 'output_schema_name' ]   
     [ , [ @output_table_name = ] 'output_table_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
     [ , [ @max_parallelism = ] max_parallelism ]
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
단계가 속한 작업의 이름입니다. job_name은 nvarchar(128) 형식입니다.

[ **\@step_id =** ] step_id  
수정할 작업 단계에 대한 ID 번호입니다. step_id 또는 step_name을 지정해야 합니다. step_id는 int 형식입니다.

[ **\@step_name =** ] 'step_name'  
수정할 단계의 이름입니다. step_id 또는 step_name을 지정해야 합니다. step_name은 nvarchar(128) 형식입니다.

[ **\@new_id =** ] new_id  
작업 단계에 대한 새 시퀀스 ID 번호입니다. 단계 ID 번호는 1에서 시작하여 간격 없이 증가합니다. 단계의 번호가 변경되면 다른 단계의 번호가 자동으로 다시 매겨집니다.

[ **\@new_name =** ] 'new_name'  
단계의 새 이름입니다. new_name은 nvarchar(128) 형식입니다.

[ **\@command_type =** ] 'command_type'  
이 jobstep에서 실행되는 명령의 유형입니다. command_type은 nvarchar(50) 형식이며, 기본값은 TSql(@command_type 매개 변수의 값이 T-SQL 스크립트임을 의미함)입니다.

지정되는 경우 이 값은 TSql이어야 합니다.

[ **\@command_source =** ] 'command_source'  
명령이 저장된 위치의 유형입니다. command_source는 nvarchar(50) 형식이며, 기본값은 Inline(@command_source 매개 변수의 값이 명령의 리터럴 텍스트임을 의미함)입니다.

지정되는 경우 이 값은 Inline이어야 합니다.

[ **\@command =** ] 'command'  
명령은 유효한 T-SQL 스크립트여야 하며, 이 작업 단계에서 실행됩니다. 명령은 nvarchar(max) 형식이며, 기본값은 NULL입니다.

[ **\@credential_name =** ] 'credential_name'  
이 단계를 실행할 때 대상 그룹 내의 각 대상 데이터베이스에 연결하는 데 사용되는 이 작업 제어 데이터베이스에 저장된 데이터베이스 범위 자격 증명의 이름입니다. credential_name은 nvarchar(128) 형식입니다.

[ **\@target_group_name =** ] 'target-group_name'  
작업 단계를 실행할 대상 데이터베이스가 포함된 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식입니다.

[ **\@initial_retry_interval_seconds =** ] initial_retry_interval_seconds  
초기 실행 시도에서 작업 단계가 실패하는 경우 첫 번째 재시도가 시작될 때까지의 지연 시간입니다. initial_retry_interval_seconds는 int 형식이며, 기본값은 1입니다.

[ **\@maximum_retry_interval_seconds =** ] maximum_retry_interval_seconds  
재시도 간의 최대 지연 시간입니다. 재시도 간의 지연 시간이 이 값보다 커지면 대신 이 값으로 제한됩니다. maximum_retry_interval_seconds는 int 형식이며, 기본값은 120입니다.

[ **\@retry_interval_backoff_multiplier =** ] retry_interval_backoff_multiplier  
여러 작업 단계 실행 시도가 실패하는 경우 재시도 지연 시간에 적용할 승수입니다. 예를 들어 첫 번째 재시도의 지연 시간이 5초이고 백오프 승수가 2.0인 경우 두 번째 재시도의 지연 시간은 10초, 세 번째 재시도의 지연 시간은 20초가 됩니다. retry_interval_backoff_multiplier는 real 형식이며, 기본값은 2.0입니다.

[ **\@retry_attempts =** ] retry_attempts  
초기 시도가 실패하는 경우 실행을 다시 시도하는 횟수입니다. 예를 들어 retry_attempts 값이 10이면 초기 시도 횟수는 1회이고, 재시도 횟수는 10회이며, 총 시도 횟수는 11회입니다. 마지막 재시도 시도가 실패하면 lifecycle이 Failed(실패)인 채로 작업 실행이 종료됩니다. retry_attempts는 int 형식이며, 기본값은 10입니다.

[ **\@step_timeout_seconds =** ] step_timeout_seconds  
단계를 실행하는 데 허용되는 최대 시간입니다. 이 시간을 초과하면 lifecycle이 TimedOut(시간이 초과됨)인 채로 작업 실행이 종료됩니다. step_timeout_seconds는 int 형식이며, 기본값은 43,200초(12시간)입니다.

[ **\@output_type =** ] 'output_type'  
null이 아닌 경우 명령의 첫 번째 결과 집합이 기록되는 대상의 유형입니다. output_type의 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_type은 nvarchar(50) 형식이며, 기본값은 NULL입니다.

지정되는 경우 값은 SqlDatabase여야 합니다.

[ **\@output_credential_name =** ] 'output_credential_name'  
null이 아닌 경우 출력 대상 데이터베이스에 연결하는 데 사용되는 데이터베이스 범위 자격 증명의 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_credential_name 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_credential_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@output_server_name =** ] 'output_server_name'  
null이 아닌 경우 출력 대상 데이터베이스가 포함된 서버의 정규화된 DNS 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_server_name 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_server_name은 nvarchar(256) 형식이며, 기본값은 NULL입니다.

[ **\@output_database_name =** ] 'output_database_name'  
null이 아닌 경우 출력 대상 테이블이 포함된 데이터베이스의 이름입니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_database_name의 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_database_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@output_schema_name =** ] 'output_schema_name'  
null이 아닌 경우 출력 대상 테이블이 포함된 SQL 스키마의 이름입니다. output_type이 SqlDatabase와 같으면 기본값은 dbo입니다. output_schema_name의 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_schema_name은 nvarchar(128) 형식입니다.

[ **\@output_table_name =** ] 'output_table_name'  
null이 아닌 경우 명령의 첫 번째 결과 집합이 기록되는 테이블의 이름입니다. 테이블이 아직 없으면 반환되는 결과 집합의 스키마를 기반으로 하여 만들어집니다. output_type이 SqlDatabase와 같으면 반드시 지정해야 합니다. output_server_name 값을 NULL로 다시 설정하려면 이 매개 변수의 값을 ''(빈 문자열)로 설정합니다. output_table_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다.

[ **\@job_version =** ] job_version OUTPUT  
새 작업 버전 번호가 할당될 출력 매개 변수입니다. job_version은 int 형식입니다.

[  **\@max_parallelism =** ] max_parallelism 출력  
탄력적 풀당 최대 병렬 처리 수준입니다. 설정되는 경우 작업 단계가 탄력적 풀당 최댓값 이하의 여러 데이터베이스에서만 실행되도록 제한됩니다. 이는 대상 그룹에 직접 포함되어 있거나 대상 그룹에 포함된 서버 내에 있는 각 탄력적 풀에 적용됩니다. max_parallelism의 값을 null로 다시 설정하려면 이 매개 변수 값을 -1로 설정합니다. max_parallelism은 int 형식입니다.


#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
진행 중인 모든 작업 실행은 영향을 받지 않습니다. sp_update_jobstep이 성공하면 작업의 버전 번호가 증가합니다. 다음에 작업이 실행될 때 새 버전이 사용됩니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.

- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.




### <a name="spdeletejobstep"></a>sp_delete_jobstep

작업에서 작업 단계를 제거합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_delete_jobstep [ @job_name = ] 'job_name'   
     [ , [ @step_id = ] step_id ]
     [ , [ @step_name = ] 'step_name' ]   
     [ , [ @job_version = ] job_version OUTPUT ]
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
단계를 제거할 작업의 이름입니다. job_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@step_id =** ] step_id  
삭제할 작업 단계에 대한 ID 번호입니다. step_id 또는 step_name을 지정해야 합니다. step_id는 int 형식입니다.

[ **\@step_name =** ] 'step_name'  
삭제할 단계의 이름입니다. step_id 또는 step_name을 지정해야 합니다. step_name은 nvarchar(128) 형식입니다.

[ **\@job_version =** ] job_version OUTPUT  
새 작업 버전 번호가 할당될 출력 매개 변수입니다. job_version은 int 형식입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
진행 중인 모든 작업 실행은 영향을 받지 않습니다. sp_update_jobstep이 성공하면 작업의 버전 번호가 증가합니다. 다음에 작업이 실행될 때 새 버전이 사용됩니다.

다른 작업 단계는 삭제된 작업 단계에서 남겨진 간격을 채우기 위해 번호가 자동으로 다시 매겨집니다.
 
#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.






### <a name="spstartjob"></a>sp_start_job

작업 실행을 시작합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_start_job [ @job_name = ] 'job_name'   
     [ , [ @job_execution_id = ] job_execution_id OUTPUT ]   
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
단계를 제거할 작업의 이름입니다. job_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@job_execution_id =** ] job_execution_id OUTPUT  
작업 실행의 ID가 할당될 출력 매개 변수입니다. job_version은 uniqueidentifier입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
없음.
 
#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

### <a name="spstopjob"></a>sp_stop_job

작업 실행을 중지합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_stop_job [ @job_execution_id = ] ' job_execution_id '
```


#### <a name="arguments"></a>인수
[ **\@job_execution_id =** ] job_execution_id  
중지할 작업 실행의 ID 번호입니다. job_execution_id는 uniqueidentifier이며, 기본값은 NULL입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
없음.
 
#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.


### <a name="spaddtargetgroup"></a>sp_add_target_group

대상 그룹을 추가합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_add_target_group [ @target_group_name = ] 'target_group_name'   
     [ , [ @target_group_id = ] target_group_id OUTPUT ]
```


#### <a name="arguments"></a>인수
[ **\@target_group_name =** ] 'target_group_name'  
만들 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[  **\@target_group_id =** ] target_group_id 출력 대상 그룹 id 번호를 성공적으로 생성 된 작업에 할당 합니다. target_group_id는 uniqueidentifier 형식의 출력 변수이며, 기본값은 NULL입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
대상 그룹은 데이터베이스 컬렉션에서 작업을 대상으로 쉽게 지정할 수 있는 방법을 제공합니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

### <a name="spdeletetargetgroup"></a>sp_delete_target_group

대상 그룹을 삭제합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_delete_target_group [ @target_group_name = ] 'target_group_name'
```


#### <a name="arguments"></a>인수
[ **\@target_group_name =** ] 'target_group_name'  
삭제할 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
없음.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

### <a name="spaddtargetgroupmember"></a>sp_add_target_group_member

대상 그룹에 데이터베이스 또는 데이터베이스 그룹을 추가합니다.

#### <a name="syntax"></a>구문

```sql
[jobs].sp_add_target_group_member [ @target_group_name = ] 'target_group_name'
         [ @membership_type = ] 'membership_type' ]   
        [ , [ @target_type = ] 'target_type' ]   
        [ , [ @refresh_credential_name = ] 'refresh_credential_name' ]   
        [ , [ @server_name = ] 'server_name' ]   
        [ , [ @database_name = ] 'database_name' ]   
        [ , [ @elastic_pool_name = ] 'elastic_pool_name' ]   
        [ , [ @shard_map_name = ] 'shard_map_name' ]   
        [ , [ @target_id = ] 'target_id' OUTPUT ]
```

#### <a name="arguments"></a>인수
[ **\@target_group_name =** ] 'target_group_name'  
멤버를 추가할 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@membership_type =** ] 'membership_type'  
대상 그룹 멤버가 포함되거나 제외되는지 여부를 지정합니다. target_group_name은 nvarchar(128) 형식이며, 기본값은 'Include'입니다. target_group_name에 대해 유효한 값은 'Include' 또는 'Exclude'입니다.

[ **\@target_type =** ] 'target_type'  
서버의 모든 데이터베이스, 탄력적 풀의 모든 데이터베이스, 분할된 맵의 모든 데이터베이스 또는 개별 데이터베이스가 포함된 대상 데이터베이스 또는 데이터베이스 컬렉션의 유형입니다. target_type은 nvarchar(128) 형식이며, 기본값은 없습니다. target_type에 대해 유효한 값은 'SqlServer', 'SqlElasticPool', 'SqlDatabase' 또는 'SqlShardMap'입니다. 

[ **\@refresh_credential_name =** ] 'refresh_credential_name'  
SQL Database 서버의 이름입니다. refresh_credential_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@server_name =** ] 'server_name'  
지정된 대상 그룹에 추가해야 하는 SQL Database 서버의 이름입니다. target_type이 'SqlServer'이면 server_name을 지정해야 합니다. server_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@database_name =** ] 'database_name'  
지정된 대상 그룹에 추가해야 하는 데이터베이스의 이름입니다. target_type이 'SqlDatabase'이면 database_name을 지정해야 합니다. database_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@elastic_pool_name =** ] 'elastic_pool_name'  
지정된 대상 그룹에 추가해야 하는 탄력적 풀의 이름입니다. target_type이 'SqlElasticPool'이면 elastic_pool_name을 지정해야 합니다. elastic_pool_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@shard_map_name =** ] 'shard_map_name'  
지정된 대상 그룹에 추가해야 하는 분할된 맵 풀의 이름입니다. target_type이 'SqlSqlShardMap'이면 elastic_pool_name을 지정해야 합니다. shard_map_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ **\@target_id =** ] target_group_id OUTPUT  
만들어지면 대상 그룹에 추가되는 대상 그룹 멤버에 할당된 대상 ID 번호입니다. target_id는 uniqueidentifier 형식의 출력 변수이며, 기본값은 NULL입니다.
반환 코드 값 0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
SQL Database 서버 또는 탄력적 풀이 대상 그룹에 포함되는 경우 작업은 실행 시 SQL Database 서버 또는 탄력적 풀 내의 모든 단일 데이터베이스에서 실행됩니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

#### <a name="examples"></a>예
다음 예제에서는 London 및 NewYork 서버에 있는 모든 데이터베이스를 Servers Maintaining Customer Information(고객 정보 유지 관리 서버) 그룹에 추가합니다. 작업 에이전트를 만들 때 지정한 작업 데이터베이스(여기서는 ElasticJobs)에 연결해야 합니다.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Add a target group containing server(s)
EXEC jobs.sp_add_target_group @target_group_name =  N'Servers Maintaining Customer Information'
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'London.database.windows.net' ;
GO

-- Add a server target member
EXEC jobs.sp_add_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_type = N'SqlServer',
@refresh_credential_name=N'mymastercred', --credential required to refresh the databases in server
@server_name=N'NewYork.database.windows.net' ;
GO

--View the recently added members to the target group
SELECT * FROM [jobs].target_group_members WHERE target_group_name= N'Servers Maintaining Customer Information';
GO
```

### <a name="spdeletetargetgroupmember"></a>sp_delete_target_group_member

대상 그룹에서 대상 그룹 멤버를 제거합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_delete_target_group_member [ @target_group_name = ] 'target_group_name'
        [ , [ @target_id = ] 'target_id']
```



Arguments [ @target_group_name = ] 'target_group_name'  
대상 그룹 멤버를 제거할 대상 그룹의 이름입니다. target_group_name은 nvarchar(128) 형식이며, 기본값은 없습니다.

[ @target_id = ] target_id  
 제거할 대상 그룹 멤버에 할당된 대상 ID 번호입니다. target_id는 uniqueidentifier이고, 기본값은 NULL입니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패)

#### <a name="remarks"></a>설명
대상 그룹은 데이터베이스 컬렉션에서 작업을 대상으로 쉽게 지정할 수 있는 방법을 제공합니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

#### <a name="examples"></a>예
다음 예제에서는 Servers Maintaining Customer Information 그룹에서 London 서버를 제거합니다. 작업 에이전트를 만들 때 지정한 작업 데이터베이스(여기서는 ElasticJobs)에 연결해야 합니다.

```sql
--Connect to the jobs database specified when creating the job agent
USE ElasticJobs ; 
GO

-- Retrieve the target_id for a target_group_members
declare @tid uniqueidentifier
SELECT @tid = target_id FROM [jobs].target_group_members WHERE target_group_name = 'Servers Maintaining Customer Information' and server_name = 'London.database.windows.net'

-- Remove a target group member of type server
EXEC jobs.sp_delete_target_group_member
@target_group_name = N'Servers Maintaining Customer Information',
@target_id = @tid
GO
```

### <a name="sppurgejobhistory"></a>sp_purge_jobhistory

작업에 대한 기록 레코드를 제거합니다.

#### <a name="syntax"></a>구문


```sql
[jobs].sp_purge_jobhistory [ @job_name = ] 'job_name'   
      [ , [ @job_id = ] job_id ]
      [ , [ @oldest_date = ] oldest_date []
```

#### <a name="arguments"></a>인수
[ **\@job_name =** ] 'job_name'  
기록 레코드를 삭제할 작업의 이름입니다. job_name은 nvarchar(128) 형식이며, 기본값은 NULL입니다. job_id 또는 job_name을 지정해야 하지만, 둘 다는 지정할 수 없습니다.

[ **\@job_id =** ] job_id  
 삭제할 레코드에 대한 작업 ID 번호입니다. job_id는 uniqueidentifier이며, 기본값은 NULL입니다. job_id 또는 job_name을 지정해야 하지만, 둘 다는 지정할 수 없습니다.

[ **\@oldest_date =** ] oldest_date  
 기록에서 유지할 가장 오래된 레코드입니다. oldest_date는 DATETIME2 형식이며, 기본값은 NULL입니다. oldest_date가 지정되면 sp_purge_jobhistory는 지정한 값보다 오래된 레코드만 제거합니다.

#### <a name="return-code-values"></a>반환 코드 값
0(성공) 또는 1(실패) 설명 대상 그룹은 데이터베이스 컬렉션에서 작업을 대상으로 쉽게 지정할 수 있는 방법을 제공합니다.

#### <a name="permissions"></a>권한
sysadmin 고정 서버 역할의 멤버는 기본적으로 이 저장 프로시저를 실행할 수 있습니다. 사용자가 작업만 모니터링할 수 있도록 제한하면 작업 에이전트를 만들 때 지정한 작업 에이전트 데이터베이스에서 해당 사용자가 다음 데이터베이스 역할에 속하도록 승인할 수 있습니다.
- jobs_reader

이러한 역할의 권한에 대한 자세한 내용은 이 문서의 [권한] 섹션을 참조하세요. sysadmin의 멤버만 이 저장 프로시저를 사용하여 다른 사용자가 소유한 작업의 특성을 편집할 수 있습니다.

#### <a name="examples"></a>예
다음 예제에서는 London 및 NewYork 서버에 있는 모든 데이터베이스를 Servers Maintaining Customer Information(고객 정보 유지 관리 서버) 그룹에 추가합니다. 작업 에이전트를 만들 때 지정한 작업 데이터베이스(여기서는 ElasticJobs)에 연결해야 합니다.

```sql
--Connect to the jobs database specified when creating the job agent

EXEC sp_delete_target_group_member   
    @target_group_name = N'Servers Maintaining Customer Information',  
    @server_name = N'London.database.windows.net';  
GO
```


## <a name="job-views"></a>작업 보기

[작업 데이터베이스](sql-database-job-automation-overview.md#job-database)에서 사용할 수 있는 보기는 다음과 같습니다.


|보기  |설명  |
|---------|---------|
|[jobs_executions](#jobs_executions-view)     |  작업 실행 기록을 표시합니다.      |
|[jobs](#jobs-view)     |   모든 작업을 표시합니다.      |
|[job_versions](#job_versions-view)     |   모든 작업 버전을 표시합니다.      |
|[jobsteps](#jobsteps-view)     |     각 작업의 현재 버전에 있는 모든 단계를 표시합니다.    |
|[jobstep_versions](#jobstep_versions-view)     |     각 작업의 모든 버전에 있는 모든 단계를 표시합니다.    |
|[target_groups](#target_groups-view)     |      모든 대상 그룹을 표시합니다.   |
|[target_group_members](#target_groups_members-view)     |   모든 대상 그룹의 모든 멤버를 표시합니다.      |


### <a name="jobsexecutions-view"></a>jobs_executions 보기

[jobs].[jobs_executions]

작업 실행 기록을 표시합니다.


|열 이름|   데이터 형식   |설명|
|---------|---------|---------|
|**job_execution_id**   |uniqueidentifier|  작업 실행 인스턴스의 고유 ID입니다.
|**job_name**   |nvarchar(128)  |작업의 이름입니다.
|**job_id** |uniqueidentifier|  작업의 고유 ID입니다.
|**job_version**    |int    |작업 버전입니다(작업이 수정될 때마다 자동으로 업데이트됨).
|**step_id**    |int|   이 작업의 단계에 대한 고유 ID입니다. NULL은 부모 작업 실행임을 나타냅니다.
|**is_active**| bit |정보가 활성 또는 비활성 상태인지 여부를 나타냅니다. 1은 활성 작업을 나타내고, 0은 비활성 작업을 나타냅니다.
|**lifecycle**| nvarchar(50)|작업 상태를 나타내는 값: ‘Created’, ‘In Progress’, ‘Failed’, ‘Succeeded’, ‘Skipped’, ‘SucceededWithSkipped’|
|**create_time**|   datetime2(7)|   작업을 만든 날짜 및 시간입니다.
|**start_time** |datetime2(7)|  작업 실행을 시작한 날짜 및 시간입니다. 작업이 아직 실행되지 않은 경우 NULL입니다.
|**end_time**|  datetime2(7)    |작업 실행을 완료한 날짜 및 시간입니다. 작업이 아직 실행되지 않았거나 실행이 아직 완료되지 않은 경우 NULL입니다.
|**current_attempts**   |int    |단계를 다시 시도한 횟수입니다. 부모 작업은 0이고, 자식 작업 실행은 실행 정책에 따라 1 이상입니다.
|**current_attempt_start_time** |datetime2(7)|  작업 실행을 시작한 날짜 및 시간입니다. NULL은 부모 작업 실행임을 나타냅니다.
|**last_message**   |nvarchar(max)| 작업 또는 단계 기록 메시지입니다. 
|**target_type**|   nvarchar(128)   |서버의 모든 데이터베이스, 탄력적 풀의 모든 데이터베이스 또는 데이터베이스가 포함된 대상 데이터베이스 또는 데이터베이스 컬렉션의 유형입니다. target_type에 대해 유효한 값은 'SqlServer', 'SqlElasticPool' 또는 'SqlDatabase'입니다. NULL은 부모 작업 실행임을 나타냅니다.
|**target_id**  |uniqueidentifier|  대상 그룹 멤버의 고유 ID입니다.  NULL은 부모 작업 실행임을 나타냅니다.
|**target_group_name**  |nvarchar(128)  |대상 그룹의 이름입니다. NULL은 부모 작업 실행임을 나타냅니다.
|**target_server_name**|    nvarchar(256)|  대상 그룹에 포함된 SQL Database 서버의 이름입니다. target_type이 'SqlServer'인 경우에만 지정됩니다. NULL은 부모 작업 실행임을 나타냅니다.
|**target_database_name**   |nvarchar(128)| 대상 그룹에 포함된 데이터베이스의 이름입니다. target_type이 'SqlDatabase'인 경우에만 지정됩니다. NULL은 부모 작업 실행임을 나타냅니다.


### <a name="jobs-view"></a>작업 보기

[jobs].[jobs]

모든 작업을 표시합니다.

|열 이름|   데이터 형식|  설명|
|------|------|-------|
|**job_name**|  nvarchar(128)   |작업의 이름입니다.|
|**job_id**|    uniqueidentifier    |작업의 고유 ID입니다.|
|**job_version**    |int    |작업 버전입니다(작업이 수정될 때마다 자동으로 업데이트됨).|
|**description**    |nvarchar(512)| 작업에 대한 설명입니다. bit 형식으로 사용됩니다. 작업의 사용 여부를 나타냅니다. 1은 사용되는 작업을 나타내고, 0은 사용되지 않는 작업을 나타냅니다.|
|**schedule_interval_type** |nvarchar(50)   |작업을 실행할 시기를 나타내는 값: 'Once', 'Minutes', 'Hours', 'Days', 'Weeks', 'Months'
|**schedule_interval_count**|   int|    각 작업 실행 간에 발생할 schedule_interval_type 기간의 수입니다.|
|**schedule_start_time**    |datetime2(7)|  작업 실행을 마지막으로 시작한 날짜 및 시간입니다.|
|**schedule_end_time**| datetime2(7)|   작업 실행을 마지막으로 완료한 날짜 및 시간입니다.|


### <a name="jobversions-view"></a>job_versions 보기

[jobs].[job_versions]

모든 작업 버전을 표시합니다.

|열 이름|   데이터 형식|  설명|
|------|------|-------|
|**job_name**|  nvarchar(128)   |작업의 이름입니다.|
|**job_id**|    uniqueidentifier    |작업의 고유 ID입니다.|
|**job_version**    |int    |작업 버전입니다(작업이 수정될 때마다 자동으로 업데이트됨).|


### <a name="jobsteps-view"></a>jobsteps 보기

[jobs].[jobsteps]

각 작업의 현재 버전에 있는 모든 단계를 표시합니다.

|열 이름    |데이터 형식| 설명|
|------|------|-------|
|**job_name**   |nvarchar(128)| 작업의 이름입니다.|
|**job_id** |uniqueidentifier   |작업의 고유 ID입니다.|
|**job_version**|   int|    작업 버전입니다(작업이 수정될 때마다 자동으로 업데이트됨).|
|**step_id**    |int    |이 작업의 단계에 대한 고유 ID입니다.|
|**step_name**  |nvarchar(128)  |이 작업의 단계에 대한 고유 이름입니다.|
|**command_type**   |nvarchar(50)   |작업 단계에서 실행할 명령의 유형입니다. v1의 경우 값은 기본값인 'TSql'과 같아야 합니다.|
|**command_source** |nvarchar(50)|  명령의 위치입니다. v1의 경우 'Inline'이 기본값이며, 허용되는 유일한 값입니다.|
|**command**|   nvarchar(max)|  command_type을 통해 탄력적 작업에서 실행할 명령입니다.|
|**credential_name**|   nvarchar(128)   |작업 실행에 사용된 데이터베이스 범위 자격 증명의 이름입니다.|
|**target_group_name**| nvarchar(128)   |대상 그룹의 이름입니다.|
|**target_group_id**|   uniqueidentifier|   대상 그룹의 고유 ID입니다.|
|**initial_retry_interval_seconds**|    int |첫 번째 재시도가 시도될 때까지의 지연 시간입니다. 기본값은 1입니다.|
|**maximum_retry_interval_seconds** |int|   재시도 간의 최대 지연 시간입니다. 재시도 간의 지연 시간이 이 값보다 커지면 대신 이 값으로 제한됩니다. 기본값은 120입니다.|
|**retry_interval_backoff_multiplier**  |real|  여러 작업 단계 실행 시도가 실패하는 경우 재시도 지연 시간에 적용할 승수입니다. 기본값은 2.0입니다.|
|**retry_attempts** |int|   이 단계가 실패하는 경우 사용할 재시도 횟수입니다. 기본값은 10이며, 재시도 횟수가 없음을 나타냅니다.|
|**step_timeout_seconds**   |int|   재시도 간의 시간 간격(분)입니다. 기본값은 0이며, 0분 간격을 나타냅니다.|
|**output_type**    |nvarchar(11)|  명령의 위치입니다. 현재 미리 보기에서는 'Inline'이 기본값이며, 허용되는 유일한 값입니다.|
|**output_credential_name**|    nvarchar(128)   |결과 집합을 저장할 대상 서버에 연결하는 데 사용할 자격 증명의 이름입니다.|
|**output_subscription_id**|    uniqueidentifier|   쿼리 실행의 결과 집합에 대한 대상 서버\데이터베이스의 구독에 대한 고유 ID입니다.|
|**output_resource_group_name** |nvarchar(128)| 대상 서버가 있는 리소스 그룹 이름입니다.|
|**output_server_name**|    nvarchar(256)   |결과 집합에 대한 대상 서버의 이름입니다.|
|**output_database_name**   |nvarchar(128)| 결과 집합에 대한 대상 데이터베이스의 이름입니다.|
|**output_schema_name** |nvarchar(max)| 대상 스키마의 이름입니다. 지정되지 않은 경우 기본값은 dbo입니다.|
|**output_table_name**| nvarchar(max)|  쿼리 결과의 결과 집합을 저장할 테이블의 이름입니다. 테이블이 아직 없는 경우 결과 집합의 스키마를 기반으로 하여 자동으로 만들어집니다. 스키마는 결과 집합의 스키마와 일치해야 합니다.|
|**max_parallelism**|   int|    작업 단계가 한 번에 실행될 탄력적 풀당 최대 데이터베이스 수입니다. 기본값은 NULL이며, 제한이 없음을 의미합니다. |


### <a name="jobstepversions-view"></a>jobstep_versions 보기

[jobs].[jobstep_versions]

각 작업의 모든 버전에 있는 모든 단계를 표시합니다. 스키마는 [jobsteps](#jobsteps-view)와 동일합니다.

### <a name="targetgroups-view"></a>target_groups 보기

[jobs].[target_groups]

모든 대상 그룹을 나열합니다.

|열 이름|데이터 형식| 설명|
|-----|-----|-----|
|**target_group_name**| nvarchar(128)   |데이터베이스 컬렉션인 대상 그룹의 이름입니다. 
|**target_group_id**    |uniqueidentifier   |대상 그룹의 고유 ID입니다.

### <a name="targetgroupsmembers-view"></a>target_groups_members 보기

[jobs].[target_groups_members]

모든 대상 그룹의 모든 멤버를 표시합니다.

|열 이름|데이터 형식| 설명|
|-----|-----|-----|
|**target_group_name**  |nvarchar(128)|데이터베이스 컬렉션인 대상 그룹의 이름입니다. |
|**target_group_id**    |uniqueidentifier   |대상 그룹의 고유 ID입니다.|
|**membership_type**    |int|   대상 그룹 멤버가 대상 그룹에 포함되거나 제외되는지 여부를 지정합니다. target_group_name에 대해 유효한 값은 'Include' 또는 'Exclude'입니다.|
|**target_type**    |nvarchar(128)| 서버의 모든 데이터베이스, 탄력적 풀의 모든 데이터베이스 또는 데이터베이스가 포함된 대상 데이터베이스 또는 데이터베이스 컬렉션의 유형입니다. target_type에 대해 유효한 값은 'SqlServer', 'SqlElasticPool', 'SqlDatabase' 또는 'SqlShardMap'입니다.|
|**target_id**  |uniqueidentifier|  대상 그룹 멤버의 고유 ID입니다.|
|**refresh_credential_name**    |nvarchar(128)  |대상 그룹 멤버에 연결하는 데 사용되는 데이터베이스 범위 자격 증명의 이름입니다.|
|**subscription_id**    |uniqueidentifier|  구독의 고유 ID입니다.|
|**resource_group_name**    |nvarchar(128)| 대상 그룹 멤버가 있는 리소스 그룹의 이름입니다.|
|**server_name**    |nvarchar(128)  |대상 그룹에 포함된 SQL Database 서버의 이름입니다. target_type이 'SqlServer'인 경우에만 지정됩니다. |
|**database_name**  |nvarchar(128)  |대상 그룹에 포함된 데이터베이스의 이름입니다. target_type이 'SqlDatabase'인 경우에만 지정됩니다.|
|**elastic_pool_name**  |nvarchar(128)| 대상 그룹에 포함된 탄력적 풀의 이름입니다. target_type이 'SqlElasticPool'인 경우에만 지정됩니다.|
|**shard_map_name** |nvarchar(128)| 대상 그룹에 포함된 분할된 맵의 이름입니다. target_type이 'SqlShardMap'인 경우에만 지정됩니다.|


## <a name="resources"></a>리소스

 - ![항목 링크 아이콘](https://docs.microsoft.com/sql/database-engine/configure-windows/media/topic-link.gif "항목 링크 아이콘") [Transact-SQL 구문 표기 규칙](https://docs.microsoft.com/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql)  


## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 탄력적 작업 만들기 및 관리](elastic-jobs-powershell.md)
- [SQL Server 권한 부여 및 권한](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authorization-and-permissions-in-sql-server)
