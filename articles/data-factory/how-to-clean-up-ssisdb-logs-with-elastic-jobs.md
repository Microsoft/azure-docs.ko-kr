---
title: Azure Elastic Database 작업을 사용하여 SSISDB 로그 정리 | Microsoft Docs
description: 이 문서에서는 이를 위해 존재하는 저장 프로시저를 트리거하기 위해 Azure Elastic Database 작업을 사용하여 SSISDB 로그를 정리하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/13/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1afc40bd601c06def57ae59797d31a5edf4095bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61345563"
---
# <a name="clean-up-ssisdb-logs-with-azure-elastic-database-jobs"></a>Azure Elastic Database 작업을 사용하여 SSISDB 로그 정리

이 문서에서는 Azure Elastic Database 작업을 사용하여 SQL Server Integration Services 카탈로그 데이터베이스 `SSISDB`에 대한 로그를 정리하는 저장 프로시저를 트리거하는 방법을 설명합니다.

Elastic Database 작업은 데이터베이스 또는 데이터베이스 그룹에 대한 작업을 쉽게 자동화하고 실행시키는 Azure 서비스입니다. Azure Portal, Transact-SQL, PowerShell 또는 REST API를 사용하여 이러한 작업을 예약하고, 실행하고, 모니터링할 수 있습니다. 탄력적 데이터베이스 작업을 사용하여 일회성으로 또는 일정에 따라 로그 정리를 위한 저장 프로시저를 트리거합니다. 지나친 데이터베이스 부하를 방지하기 위해 SSISDB 리소스 사용량을 기준으로 일정 간격을 선택할 수 있습니다.

자세한 내용은 [Elastic Database 작업을 사용하여 데이터베이스 그룹 관리](../sql-database/elastic-jobs-overview.md)를 참조하세요.

다음 섹션에서는 저장 프로시저 `[internal].[cleanup_server_retention_window_exclusive]`를 트리거하는 방법을 설명합니다. 해당 프로시저는 관리자가 설정한 보존 기간을 벗어나는 SSISDB 로그를 제거합니다.

## <a name="clean-up-logs-with-power-shell"></a>PowerShell을 사용하여 로그 정리

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

다음 샘플 PowerShell 스크립트는 SSISDB 로그 정리를 위한 저장 프로시저를 트리거하는 새 탄력적 작업을 만듭니다. 자세한 내용은 [PowerShell을 사용하여 탄력적 작업 에이전트 만들기](../sql-database/elastic-jobs-powershell.md)를 참조하세요.

### <a name="create-parameters"></a>매개 변수 만들기

``` powershell
# Parameters needed to create the Job Database
param(
$ResourceGroupName = $(Read-Host "Please enter an existing resource group name"),
$AgentServerName = $(Read-Host "Please enter the name of an existing Azure SQL server(for example, yhxserver) to hold the SSISDBLogCleanup job database"),
$SSISDBLogCleanupJobDB = $(Read-Host "Please enter a name for the Job Database to be created in the given SQL Server"),
# The Job Database should be a clean,empty,S0 or higher service tier. We set S0 as default.
$PricingTier = "S0",

# Parameters needed to create the Elastic Job agent
$SSISDBLogCleanupAgentName = $(Read-Host "Please enter a name for your new Elastic Job agent"),

# Parameters needed to create the job credential in the Job Database to connect to SSISDB
$PasswordForSSISDBCleanupUser = $(Read-Host "Please provide a new password for SSISDBLogCleanup job user to connect to SSISDB database for log cleanup"),
# Parameters needed to create a login and a user in the SSISDB of the target server
$SSISDBServerEndpoint = $(Read-Host "Please enter the name of the target Azure SQL server which contains SSISDB you need to cleanup, for example, myserver") + '.database.windows.net',
$SSISDBServerAdminUserName = $(Read-Host "Please enter the target server admin username for SQL authentication"),
$SSISDBServerAdminPassword = $(Read-Host "Please enter the target server admin password for SQL authentication"),
$SSISDBName = "SSISDB",

# Parameters needed to set job scheduling to trigger execution of cleanup stored procedure
$RunJobOrNot = $(Read-Host "Please indicate whether you want to run the job to cleanup SSISDB logs outside the log retention window immediately(Y/N). Make sure the retention window is set appropriately before running the following powershell scripts. Those removed SSISDB logs cannot be recoverd"),
$IntervalType = $(Read-Host "Please enter the interval type for the execution schedule of SSISDB log cleanup stored procedure. For the interval type, Year, Month, Day, Hour, Minute, Second can be supported."),
$IntervalCount = $(Read-Host "Please enter the detailed interval value in the given interval type for the execution schedule of SSISDB log cleanup stored procedure"),
# StartTime of the execution schedule is set as the current time as default. 
$StartTime = (Get-Date)
```

### <a name="trigger-the-cleanup-stored-procedure"></a>정리 저장 프로시저 트리거

```powershell
# Install the latest PackageManagement powershell package which PowershellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force
# You may need to restart the powershell session
# Install the latest PowershellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Place AzureRM.Sql preview cmdlets side by side with existing AzureRM.Sql version
Install-Module -Name AzureRM.Sql -AllowPrerelease -Force

# Sign in to your Azure account
Connect-AzureRmAccount

# Create a Job Database which is used for defining jobs of triggering SSISDB log cleanup stored procedure and tracking cleanup history of jobs
Write-Output "Creating a blank SQL database to be used as the SSISDBLogCleanup  Job Database ..."
$JobDatabase = New-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $AgentServerName -DatabaseName $SSISDBLogCleanupJobDB -RequestedServiceObjectiveName $PricingTier
$JobDatabase

# Enable the Elastic Jobs preview in your Azure subscription
Register-AzureRmProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Create the Elastic Job agent
Write-Output "Creating the Elastic Job agent..."
$JobAgent = $JobDatabase | New-AzureRmSqlElasticJobAgent -Name $SSISDBLogCleanupAgentName
$JobAgent

# Create the job credential in the Job Database to connect to SSISDB database in the target server for log cleanup
Write-Output "Creating job credential to connect to SSISDB database..."
$JobCredSecure = ConvertTo-SecureString -String $PasswordForSSISDBCleanupUser -AsPlainText -Force
$JobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "SSISDBLogCleanupUser", $JobCredSecure
$JobCred = $JobAgent | New-AzureRmSqlElasticJobCredential -Name "SSISDBLogCleanupUser" -Credential $JobCred

# In the master database of the target SQL server which contains SSISDB to cleanup 
# - Create the job user login
Write-Output "Grant permissions on the master database of the target server..."
$Params = @{
  'Database' = 'master'
  'ServerInstance' = $SSISDBServerEndpoint
  'Username' = $SSISDBServerAdminUserName
  'Password' = $SSISDBServerAdminPassword
  'OutputSqlErrors' = $true
  'Query' = "CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '" + $PasswordForSSISDBCleanupUser + "'"
}
Invoke-SqlCmd @Params

# For SSISDB database of the target SQL server
# - Create the SSISDBLogCleanup user from the SSISDBlogCleanup user login
# - Grant permissions for the execution of SSISDB log cleanup stored procedure 
Write-Output "Grant appropriate permissions on SSISDB database..."
$TargetDatabase = $SSISDBName
$CreateJobUser = "CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser"
$GrantStoredProcedureExecution = "GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser"

$TargetDatabase | % {
  $Params.Database = $_
  $Params.Query = $CreateJobUser
  Invoke-SqlCmd @Params
  $Params.Query = $GrantStoredProcedureExecution
  Invoke-SqlCmd @Params
}

# Create a target group which includes SSISDB database needed to cleanup
Write-Output "Creating the target group including only SSISDB database needed to cleanup ..."
$SSISDBTargetGroup = $JobAgent | New-AzureRmSqlElasticJobTargetGroup -Name "SSISDBTargetGroup"
$SSISDBTargetGroup | Add-AzureRmSqlElasticJobTarget -ServerName $SSISDBServerEndpoint -Database $SSISDBName 

# Create the job to trigger execution of SSISDB log cleanup stored procedure
Write-Output "Creating a new job to trigger execution of the stored procedure for SSISDB log cleanup"
$JobName = "CleanupSSISDBLog"
$Job = $JobAgent | New-AzureRmSqlElasticJob -Name $JobName -RunOnce
$Job

# Add the job step to execute internal.cleanup_server_retention_window_exclusive
Write-Output "Adding the job step for the cleanup stored procedure execution"
$SqlText = "EXEC internal.cleanup_server_retention_window_exclusive"
$Job | Add-AzureRmSqlElasticJobStep -Name "step to execute cleanup stored procedure" -TargetGroupName $SSISDBTargetGroup.TargetGroupName -CredentialName $JobCred.CredentialName -CommandText $SqlText

# Run the job to immediately start cleanup stored procedure execution for once
IF(($RunJobOrNot = "Y") -Or ($RunJobOrNot = "y"))
{
Write-Output "Start a new execution of the stored procedure for SSISDB log cleanup immediately..."
$JobExecution = $Job | Start-AzureRmSqlElasticJob
$JobExecution
}

# Schedule the job running to trigger stored procedure execution on schedule for removing SSISDB logs outside the retention window
Write-Output "Start the execution schedule of the stored procedure for SSISDB log cleanup..."
$Job | Set-AzureRmSqlElasticJob -IntervalType $IntervalType -IntervalCount $IntervalCount -StartTime $StartTime -Enable
```

## <a name="clean-up-logs-with-transact-sql"></a>Transact-SQL을 사용하여 로그 정리

다음 샘플 Transact-SQL 스크립트는 SSISDB 로그 정리를 위한 저장 프로시저를 트리거하는 새 탄력적 작업을 만듭니다. 자세한 내용은 [T-SQL(Transact-SQL)을 사용하여 Elastic Database 작업 만들기 및 관리](../sql-database/elastic-jobs-tsql.md)를 참조하세요.

1. SSISDB 정리 작업 데이터베이스인 빈 S0 이상 Azure SQL Database를 만들고 식별합니다. 그런 다음, [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.SQLElasticJobAgent)에서 탄력적 작업 에이전트를 만듭니다.

2. 작업 데이터베이스에서 SSISDB 로그 정리 작업에 대한 자격 증명을 만듭니다. 이 자격 증명은 로그를 정리하는 SSISDB 데이터베이스에 연결하는 데 사용됩니다.

    ```sql
    -- Connect to the job database specified when creating the job agent
    -- Create a database master key if one does not already exist, using your own password.  
    CREATE MASTER KEY ENCRYPTION BY PASSWORD= '<EnterStrongPasswordHere>';  

    -- Create a credential for SSISDB log cleanup.  
    CREATE DATABASE SCOPED CREDENTIAL SSISDBLogCleanupCred WITH IDENTITY = 'SSISDBLogCleanupUser', SECRET = '<EnterStrongPasswordHere>'; 
    ```

3. 정리 저장 프로시저를 실행할 SSISDB 데이터베이스가 포함된 대상 그룹을 정의합니다.

    ```sql
    -- Connect to the job database 
    -- Add a target group 
    EXEC jobs.sp_add_target_group 'SSISDBTargetGroup'

    -- Add SSISDB database into the target group
    EXEC jobs.sp_add_target_group_member 'SSISDBTargetGroup',
    @target_type = 'SqlDatabase',
    @server_name = '<EnterSSISDBTargetServerName>',
    @database_name = '<EnterSSISDBName>'

    --View the recently created target group and target group members
    SELECT * FROM jobs.target_groups WHERE target_group_name = 'SSISDBTargetGroup';
    SELECT * FROM jobs.target_group_members WHERE target_group_name = 'SSISDBTargetGroup';
    ```
4. SSISDB 데이터베이스에 대한 적절한 사용 권한을 부여합니다. SSISDB 로그 정리를 성공적으로 실행하기 위해 SSISDB 카탈로그에는 저장 프로시저에 대한 적절한 사용 권한이 있어야 합니다. 자세한 지침은 [로그인 관리](../sql-database/sql-database-manage-logins.md)를 참조하세요.

    ```sql
    -- Connect to the master database in the target server including SSISDB 
    CREATE LOGIN SSISDBLogCleanupUser WITH PASSWORD = '<strong_password>';

    -- Connect to SSISDB database in the target server to cleanup logs
    CREATE USER SSISDBLogCleanupUser FROM LOGIN SSISDBLogCleanupUser;
    GRANT EXECUTE ON internal.cleanup_server_retention_window_exclusive TO SSISDBLogCleanupUser
    ```
5. SSISDB 로그 정리를 위한 저장 프로시저 실행을 트리거하려면 작업을 만들고 작업 단계를 추가합니다.

    ```sql
    --Connect to the job database 
    --Add the job for the execution of SSISDB log cleanup stored procedure.
    EXEC jobs.sp_add_job @job_name='CleanupSSISDBLog', @description='Remove SSISDB logs which are outside the retention window'

    --Add a job step to execute internal.cleanup_server_retention_window_exclusive
    EXEC jobs.sp_add_jobstep @job_name='CleanupSSISDBLog',
    @command=N'EXEC internal.cleanup_server_retention_window_exclusive',
    @credential_name='SSISDBLogCleanupCred',
    @target_group_name='SSISDBTargetGroup'
    ```
6. 계속하기 전에 보존 기간을 적절히 설정했는지 확인합니다. 기간을 벗어나는 SSISDB 로그는 삭제되고 복구될 수 없습니다.

   그런 다음, SSISDB 로그 정리를 시작하는 작업을 즉시 실행할 수 있습니다.

    ```sql
    --Connect to the job database 
    --Run the job immediately to execute the stored procedure for SSISDB log cleanup
    declare @je uniqueidentifier
    exec jobs.sp_start_job 'CleanupSSISDBLog', @job_execution_id = @je output

    --Watch the execution results for SSISDB log cleanup 
    select @je
    select * from jobs.job_executions where job_execution_id = @je
    ```
7. 필요한 경우 일정에 따라 보존 기간을 벗어나는 SSISDB 로그를 제거하는 작업을 실행하도록 예약합니다. 비슷한 명령문을 사용하여 작업 매개 변수를 업데이트합니다.

    ```sql
    --Connect to the job database 
    EXEC jobs.sp_update_job
    @job_name='CleanupSSISDBLog',
    @enabled=1,
    @schedule_interval_type='<EnterIntervalType(Month,Day,Hour,Minute,Second)>',
    @schedule_interval_count='<EnterDetailedIntervalValue>',
    @schedule_start_time='<EnterProperStartTimeForSchedule>',
    @schedule_end_time='<EnterProperEndTimeForSchedule>'
    ```

## <a name="monitor-the-cleanup-job-in-the-azure-portal"></a>Azure Portal에서 정리 작업 모니터링

Azure Portal에서 정리 작업의 실행을 모니터링할 수 있습니다. 각 실행에 대한 작업의 상태, 시작 시간 및 종료 시간이 표시됩니다.

![Azure Portal에서 정리 작업 모니터링](media/how-to-clean-up-ssisdb-logs-with-elastic-jobs/monitor-cleanup-job-portal.png)

## <a name="monitor-the-cleanup-job-with-transact-sql"></a>Transact-SQL을 사용하여 정리 작업 모니터링

정리 작업의 실행 기록을 보려면 Transact-SQL을 사용할 수도 있습니다.

```sql
--Connect to the job database 
--View all execution statuses for the job to cleanup SSISDB logs
SELECT * FROM jobs.job_executions WHERE job_name = 'CleanupSSISDBLog' 
ORDER BY start_time DESC

-- View all active executions
SELECT * FROM jobs.job_executions WHERE is_active = 1
ORDER BY start_time DESC
```

## <a name="next-steps"></a>다음 단계

Azure-SSIS Integration Runtime과 관련된 관리 및 모니터링 태스크의 경우 다음 아티클을 참조하세요. Azure SSIS IR은 Azure SQL Database에서 SSISDB에 저장된 SSIS 패키지의 런타임 엔진입니다.

-   [Azure-SSIS 통합 런타임 다시 구성](manage-azure-ssis-integration-runtime.md)

-   [Azure-SSIS Integration Services 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime)
