---
title: 새 Elastic Database 작업으로 마이그레이션 | Microsoft Docs
description: 새 Elastic Database 작업으로 마이그레이션합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: johnpaulkee
ms.author: joke
ms.reviewer: sstein
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: f71fe4ff14e5a6f5fd6b91713970a097e4e56fb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61475799"
---
# <a name="migrate-to-the-new-elastic-database-jobs"></a>새 Elastic Database 작업으로 마이그레이션

업그레이드된 버전의 [Elastic Database 작업](elastic-jobs-overview.md)이 지원됩니다.

기존 고객 호스트 버전의 [Elastic Database 작업](sql-database-elastic-jobs-overview.md)이 있는 경우 최신 버전으로 쉽게 마이그레이션할 수 있도록 마이그레이션 cmdlet 및 스크립트가 제공됩니다.


## <a name="prerequisites"></a>필수 조건

업그레이드된 버전의 Elastic Database 작업에는 마이그레이션 중에 사용할 새로운 PowerShell cmdlet 집합이 포함됩니다. 이 새로운 cmdlet은 모든 기존 작업 자격 증명, 대상(데이터베이스, 서버, 사용자 지정 컬렉션), 작업 트리거, 작업 예약, 작업 콘텐츠 및 작업을 새로운 탄력적 작업 에이전트로 전환합니다.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>최신 탄력적 작업 cmdlet 설치

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

**Az.Sql** 1.1.1-preview 모듈을 설치하여 최신 탄력적 작업 cmdlet을 가져옵니다. 관리자 권한으로 PowerShell에서 다음 명령을 실행합니다.

```powershell
# Installs the latest PackageManagement powershell package which PowerShellGet v1.6.5 is dependent on
Find-Package PackageManagement -RequiredVersion 1.1.7.2 | Install-Package -Force

# Installs the latest PowerShellGet module which adds the -AllowPrerelease flag to Install-Module
Find-Package PowerShellGet -RequiredVersion 1.6.5 | Install-Package -Force

# Restart your powershell session with administrative access

# Places Az.Sql preview cmdlets side by side with existing Az.Sql version
Install-Module -Name Az.Sql -RequiredVersion 1.1.1-preview -AllowPrerelease

# Import the Az.Sql module
Import-Module Az.Sql -RequiredVersion 1.1.1

# Confirm if module successfully imported - if the imported version is 1.1.1, then continue
Get-Module Az.Sql
```

### <a name="create-a-new-elastic-job-agent"></a>새로운 탄력적 작업 에이전트 만들기

새 cmdlet을 설치한 후에 새로운 탄력적 작업 에이전트를 만듭니다.

```powershell
# Register your subscription for the for the Elastic Jobs public preview feature
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql

# Get an existing database to use as the job database - or create a new one if necessary
$db = Get-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName>
# Create a new elastic job agent
$agent = $db | New-AzSqlElasticJobAgent -Name <agentName>
```

### <a name="install-the-old-elastic-database-jobs-cmdlets"></a>이전 탄력적 작업 cmdlet 설치

마이그레이션에서는 *이전* 탄력적 작업 cmdlet 중 일부를 사용해야 합니다. 따라서 해당 cmdlet이 설치되지 않은 경우 다음 명령을 실행합니다.

```powershell
# Install the old elastic job cmdlets if necessary and initialize the old jobs cmdlets
.\nuget install Microsoft.Azure.SqlDatabase.Jobs -prerelease

# Install the old jobs cmdlets
cd Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools
Unblock-File .\InstallElasticDatabaseJobsCmdlets.ps1
.\InstallElasticDatabaseJobsCmdlets.ps1

# Choose the subscription where your existing jobs are
Select-AzSubscription -SubscriptionId <subscriptionId>
Use-AzureSqlJobConnection -CurrentAzureSubscription -Credential (Get-Credential)
```



## <a name="migration"></a>마이그레이션

이제 이전 및 새로운 탄력적 작업 cmdlet이 모두 설치되었으므로 작업 자격 증명, 대상 및 작업을 새로운 *작업 데이터베이스*로 마이그레이션합니다.

### <a name="setup"></a>설정

```powershell
$ErrorActionPreference = "Stop";

# Helper function to show starting write output
function Log-StartOutput ($output) {
  Write-Output ("`r--------------------- " + $output + " ---------------------")
}

# Helper function to show starting write output
function Log-ChildOutput ($output) {
  Write-Output ("  - " + $output)
}
```



### <a name="migrate-credentials"></a>자격 증명 마이그레이션

```powershell
function Migrate-Credentials ($agent) {
    Log-StartOutput "Migrating credentials"

    $oldCreds = Get-AzureSqlJobCredential
    $oldCreds | % {
        $oldCredName = $_.CredentialName
        $oldUserName = $_.UserName
        Write-Output ("Credential " + $oldCredName)
        $oldCredential = Get-Credential -UserName $oldUserName `
                         -Message ("Please enter in the password that was used for your credential " + $oldCredName)
        try
        {
            $cred = New-AzSqlElasticJobCredential -ParentObject $agent -Name $oldCredName -Credential $oldCredential
        }
        catch [System.Management.Automation.PSArgumentException]
        {
            $cred = Get-AzSqlElasticJobCredential -ParentObject $agent -Name $oldCredName
            $cred = Set-AzSqlElasticJobCredential -InputObject $cred -Credential $oldCredential
        }

        Log-ChildOutput ("Added user " + $oldUserName)
    }
}
```

자격 증명을 마이그레이션하려면 이전의 `$agent` PowerShell 개체에 전달하여 다음 명령을 실행합니다.

```powershell
Migrate-Credentials $agent
```

샘플 출력

```powershell
# You should see similar output after executing the above
# --------------------- Migrating credentials ---------------------
# Credential cred1
#  - Added user user1
# Credential cred2
#  - Added user user2
# Credential cred3
#  - Added user user3
```

### <a name="migrate-targets"></a>대상 마이그레이션

```powershell
function Migrate-TargetGroups ($agent) {
    Log-StartOutput "Migrating target groups"

    # Setup hash of target groups
    $targetGroups = [ordered]@{}

    # Fetch root job targets from old service
    $rootTargets = Get-AzureSqlJobTarget

    # Return if no root targets are found
    if ($rootTargets.Count -eq 0)
    {
        Write-Output "No targets found - no need for migration"
        return
    }

    # Create list of target groups to create
    # We format the target group name as such:
    # - If root target is server type, then target group name is "(serverName)"
    # - If root target is database type, then target group name is "(serverName,databaseName)"
    # - If root target is shard map type, then target group name is "(serverName,databaseName,shardMapName)"
    # - If root target is custom collection, then target group name is "customCollectionName"
    $rootTargets | % {
        $tgName = Format-OldTargetName -target $_
        $childTargets = Get-ChildTargets -target $_
        $targetGroups.Add($tgName, $childTargets)
    }

    # Flatten list
    for ($i=$targetGroups.Count - 1; $i -ge 0; $i--)
    {
        # Fetch target group's initial list of targets unexpanded
        $targets = $targetGroups[$i]

        # Expand custom collection targets
        $j = 0;
        while ($j -lt $targets.Count)
        {
            $target = $targets[$j]
            if ($target.TargetType -eq "CustomCollection")
            {
                $targets = [System.Collections.ArrayList] $targets
                $targets.Remove($target) # Remove this target from the list

                $expandedTargets = $targetGroups[$target.TargetDescription.CustomCollectionName]

                foreach ($expandedTarget in $expandedTargets)
                {
                    $targets.Add($expandedTarget) | Out-Null
                }

                # Set updated list of targets for tg
                $targetGroups[$i] = $targets
                # Note we don't increment here in case we need to expand further
            }
            else
            {
                # Skip if no custom collection target needs to be expanded
                $j++
            }
        }
    }

    # Add targets to target group
    foreach ($targetGroup in $targetGroups.Keys)
    {
        $tg = Setup-TargetGroup -tgName $targetGroup -agent $agent
        $targets = $targetGroups[$targetGroup]
        Migrate-Targets -targets $targets -tg $tg
        $targetsAdded = (Get-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tg.TargetGroupName).Targets
        foreach ($targetAdded in $targetsAdded)
        {
            Log-ChildOutput ("Added target " + (Format-NewTargetName $targetAdded))
        }
    }
}

## Target group helpers
# Migrate shard map target from old jobs to new job's target group
function Migrate-Targets ($targets, $tg) {
  Write-Output ("Target group " + $tg.TargetGroupName)
  foreach ($target in $targets) {
    if ($target.TargetType -eq "Server") {
      Add-ServerTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "Database") {
      Add-DatabaseTarget -target $target -tg $tg
    }
    elseif ($target.TargetType -eq "ShardMap") {
      Add-ShardMapTarget -target $target -tg $tg
    }
  }
}

# Migrate server target from old jobs to new job's target group
function Add-ServerTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $credName = $jobTarget.MasterDatabaseCredentialName
  $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -RefreshCredentialName $credName
}

# Migrate database target from old jobs to new job's target group
function Add-DatabaseTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $serverName = $jobTarget.ServerName
  $databaseName = $jobTarget.DatabaseName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName -Exclude
  }
  else {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -DatabaseName $databaseName
  }
}

# Migrate shard map target from old jobs to new job's target group
function Add-ShardMapTarget ($target, $tg) {
  $jobTarget = Get-AzureSqlJobTarget -TargetId $target.TargetId
  $smName = $jobTarget.ShardMapName
  $serverName = $jobTarget.ShardMapManagerServerName
  $databaseName = $jobTarget.ShardMapManagerDatabaseName
  $credName = $jobTarget.ShardMapManagerCredentialName
  $exclude = $target.Membership

  if ($exclude -eq "Exclude") {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName -Exclude
  }
  else {
    $t = Add-AzSqlElasticJobTarget -ParentObject $tg -ServerName $serverName -ShardMapName $smName -DatabaseName $databasename -RefreshCredentialName $credName
  }
}

# Helper to format target old target names
function Format-OldTargetName ($target) {
  if ($target.TargetType -eq "Server") {
    $tgName = "(" + $target.ServerName + ")"
  }
  elseif ($target.TargetType -eq "Database") {
    $tgName = "(" + $target.ServerName + "," + $target.DatabaseName + ")"
  }
  elseif ($target.TargetType -eq "ShardMap") {
    $tgName = "(" + $target.ShardMapManagerServerName + "," +
    $target.ShardMapManagerDatabaseName + "," + `
      $target.ShardMapName + ")"
  }
  elseif ($target.TargetType -eq "CustomCollection") {
    $tgName = $target.CustomCollectionName
  }

  return $tgName
}

# Helper to format new target names
function Format-NewTargetName ($target) {
  if ($target.TargetType -eq "SqlServer") {
    $tgName = "(" + $target.TargetServerName + ")"
  }
  elseif ($target.TargetType -eq "SqlDatabase") {
    $tgName = "(" + $target.TargetServerName + "," + $target.TargetDatabaseName + ")"
  }
  elseif ($target.TargetType -eq "SqlShardMap") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetShardMapName + ")"
  }
  elseif ($target.TargetType -eq "SqlElasticPool") {
    $tgName = "(" + $target.TargetServerName + "," +
    $target.TargetDatabaseName + "," + `
      $target.TargetElasticPoolName + ")"
  }

  return $tgName
}

# Get child targets
function Get-ChildTargets($target) {
  if ($target.TargetType -eq "CustomCollection") {
    $children = Get-AzureSqlJobChildTarget -TargetId $target.TargetId
    if ($children.Count -eq 1)
    {
        $arr = New-Object System.Collections.ArrayList($null)
        $arr.Add($children)
        $children = $arr
    }
    return $children
  }
  else {
    return $target
  }
}

# Migrates target groups
function Setup-TargetGroup ($tgName, $agent) {
  try {
    $tg = New-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
  catch [System.Management.Automation.PSArgumentException] {
    $tg = Get-AzSqlElasticJobTargetGroup -ParentObject $agent -Name $tgName
    return $tg
  }
}
```

대상(서버, 데이터베이스 및 사용자 지정 컬렉션)을 새로운 작업 데이터베이스로 마이그레이션하려면 **Migrate-TargetGroups** cmdlet을 실행하여 다음을 수행합니다.

- 루트 수준 대상(서버 및 데이터베이스)은 루트 수준 대상만이 포함된 "(\<serverName\>, \<databaseName\>)"이라는 새로운 대상 그룹으로 마이그레이션됩니다.
- 사용자 지정 컬렉션은 모든 자식 대상을 포함한 새로운 대상 그룹으로 마이그레이션됩니다.

```powershell
Migrate-TargetGroups $agent
```

샘플 출력:

```powershell
# --------------------- Migrating target groups ---------------------
# Target group cc1
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group cc2
#   - Added target (s1,db1)
# Target group cc3
#   - Added target (s1)
#   - Added target (s1,db1)
# Target group (s1,db1)
#   - Added target (s1,db1)
# Target group (s1,db2)
#   - Added target (s1,db2)
# Target group (s1)
#   - Added target (s1)
# Target group (s1,db1,sm1)
#   - Added target (s1,db1,sm1)
```



### <a name="migrate-jobs"></a>작업 마이그레이션

```powershell
function Migrate-Jobs ($agent)
{
    Log-StartOutput "Migrating jobs and job steps"

    $oldJobs = Get-AzureSqlJob
    $newJobs = [System.Collections.ArrayList] @()

    foreach ($oldJob in $oldJobs)
    {
        # Ignore system jobs
        if ($oldJob.ContentName -eq $null)
        {
            continue
        }

        # Schedule
        $oldJobTriggers = Get-AzureSqlJobTrigger -JobName $oldJob.JobName

        if ($oldJobTriggers.Count -ge 1)
        {
            foreach ($trigger in $oldJobTriggers)
            {

                $schedule = Get-AzureSqlJobSchedule -ScheduleName $trigger.ScheduleName
                $newJob = [PSCustomObject] @{
                    JobName  = ($trigger.JobName + " (" + $trigger.ScheduleName + ")");
                    Description = $oldJob.ContentName
                    Schedule = $schedule
                    TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                    CredentialName = $oldJob.CredentialName
                    Output = $oldJob.ResultSetDestination
                }
                $newJobs.Add($newJob) | Out-Null
            }
        }
        else
        {
            $newJob = [PSCustomObject] @{
                JobName  = $oldJob.JobName
                Description = $oldJob.ContentName
                Schedule = $null
                TargetGroupName = (Format-OldTargetName(Get-AzureSqlJobTarget -TargetId $oldJob.TargetId))
                CredentialName = $oldJob.CredentialName
                Output = $oldJob.ResultSetDestination
            }
            $newJobs.Add($newJob) | Out-Null
        }
    }

    # At this point, we should have an organized list of jobs to create
    foreach ($newJob in $newJobs)
    {
        Write-Output ("Job " + $newJob.JobName)
        $job = Setup-Job $newJob $agent
        If ($job.Interval -ne $null)
        {
            Log-ChildOutput ("Schedule with start time " + $job.StartTime + " and end time at " + $job.EndTime)
            Log-ChildOutput ("Repeats every " + $job.Interval)
        }
        else {
            Log-ChildOutput ("Repeats once")
        }

        Setup-JobStep $newJob $job
    }
}

# Migrates jobs
function Setup-Job ($job, $agent) {
  $jobName = $newJob.JobName
  $jobDescription = $newJob.Description

  # Create or update a job has a recurring schedule
  if ($newJob.Schedule -ne $null) {
    $schedule = $newJob.Schedule
    $startTime = $schedule.StartTime.UtcTime
    $endTime = $schedule.EndTime.UtcTime
    $intervalType = $schedule.Interval.IntervalType.ToString()
    $intervalType = $intervalType.Substring(0, $intervalType.Length - 1) # Remove the last letter (s)
    $intervalCount = $schedule.Interval.Count

    try {
      $job = New-AzSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzSqlElasticJob -Description $jobDescription -IntervalType $intervalType -IntervalCount $intervalCount `
        -StartTime $startTime -EndTime $endTime
      return $job
    }
  }
  # Create or update a job that runs once
  else {
    try {
      $job = New-AzSqlElasticJob -ParentObject $agent -Name $jobName `
        -Description $jobDescription -RunOnce
      return $job
    }
    catch [System.Management.Automation.PSArgumentException] {
      $job = Get-AzSqlElasticJob -ParentObject $agent -Name $jobName
      $job = $job | Set-AzSqlElasticJob -Description $jobDescription -RunOnce
      return $job
    }
  }
}
# Migrates job steps
function Setup-JobStep ($newJob, $job) {
  $defaultJobStepName = 'JobStep'
  $contentName = $newJob.Description
  $commandText = (Get-AzureSqlJobContentDefinition -ContentName $contentName).CommandText
  $targetGroupName = $newJob.TargetGroupName
  $credentialName = $newJob.CredentialName

  $output = $newJob.Output

  if ($output -ne $null) {
    $outputServerName = $output.TargetDescription.ServerName
    $outputDatabaseName = $output.TargetDescription.DatabaseName
    $outputCredentialName = $output.CredentialName
    $outputSchemaName = $output.SchemaName
    $outputTableName = $output.TableName
    $outputDatabase = Get-AzSqlDatabase -ResourceGroupName $job.ResourceGroupName -ServerName $outputServerName -Databasename $outputDatabaseName

    try {
      $jobStep = $job | Add-AzSqlElasticJobStep -Name $defaultJobStepName `
        -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzSqlElasticJobStep -TargetGroupName $targetGroupName `
        -CredentialName $credentialName -CommandText $commandText `
        -OutputDatabaseObject $outputDatabase `
        -OutputSchemaName $outputSchemaName -OutputTableName $outputTableName `
        -OutputCredentialName $outputCredentialName
    }
  }
  else {
    try {
      $jobStep = $job | Add-AzSqlElasticJobStep -Name $defaultJobStepName -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
    catch [System.Management.Automation.PSArgumentException] {
      $jobStep = $job | Get-AzSqlElasticJobStep -Name $defaultJobStepName
      $jobStep = $jobStep | Set-AzSqlElasticJobStep -TargetGroupName $targetGroupName -CredentialName $credentialName -CommandText $commandText
    }
  }
  Log-ChildOutput ("Added step " + $jobStep.StepName + " using target group " + $jobStep.TargetGroupName + " using credential " + $jobStep.CredentialName)
  Log-ChildOutput("Command text script taken from content name " + $contentName)

  if ($jobStep.Output -ne $null) {
    Log-ChildOutput ("With output target as (" + $jobStep.Output.ServerName + "," + $jobStep.Output.DatabaseName + "," + $jobStep.Output.SchemaName + "," + $jobStep.Output.TableName + ")")
  }
}
```

작업, 작업 콘텐츠, 작업 트리거 및 작업 예약을 새로운 탄력적 작업 에이전트의 데이터베이스로 마이그레이션하려면 에이전트에서 전달되는 **Migrate-Jobs** cmdlet을 실행합니다.

- 예약이 다른 다중 트리거를 포함한 작업은 "\<jobName\> (\<scheduleName\>)"이라는 구성표를 포함한 다중 작업으로 분리됩니다.
- 연결된 명령 텍스트를 포함한 JobStep이라는 기본 작업 단계를 추가하여 작업 콘텐츠를 작업에 마이그레이션합니다.
- 작업은 기본적으로 사용하지 않으므로 활성화하기 전에 유효성을 검사할 수 있습니다.

```powershell
Migrate-Jobs $agent
```

샘플 출력:
```powershell
--------------------- Migrating jobs and job steps ---------------------
Job job1
  - Repeats once
  - Added step JobStep using target group cc2 using credential cred1
  - Command text script taken from content name SampleContext
Job job2
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
  - With output target as (s1,db1,dbo,sampleTable)
Job job3 (repeat every 10 min)
  - Schedule with start time 05/16/2018 22:05:28 and end time at 12/31/9999 11:59:59
  - Repeats every PT10M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job3 (repeat every 5 min)
  - Schedule with start time 05/16/2018 22:05:31 and end time at 12/31/9999 11:59:59
  - Repeats every PT5M
  - Added step JobStep using target group cc1 using credential cred1
  - Command text script taken from content name SampleContent
Job job4
  - Repeats once
  - Added step JobStep using target group (s1,db1) using credential cred1
  - Command text script taken from content name SampleContent
```



## <a name="migration-complete"></a>마이그레이션 완료

이제 *job database*에는 작업 자격 증명, 대상, 작업 트리거, 작업 예약, 작업 콘텐츠 및 마이그레이션 작업이 모두 포함됩니다.

모두 정확히 마이그레이션되었는지 확인하려면 다음 스크립트를 사용합니다.

```powershell
$creds = $agent | Get-AzSqlElasticJobCredential
$targetGroups = $agent | Get-AzSqlElasticJobTargetGroup
$jobs = $agent | Get-AzSqlElasticJob
$steps = $jobs | Get-AzSqlElasticJobStep
```

작업이 정확히 실행되는지 테스트하려면 해당 작업을 시작합니다.

```powershell
$jobs | Start-AzSqlElasticJob
```

예약 대로 실행되던 작업의 경우 백그라운드에서 실행할 수 있도록 활성화해야 합니다.

```powershell
$jobs | Set-AzSqlElasticJob -Enable
```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용하여 탄력적 작업 만들기 및 관리](elastic-jobs-powershell.md)
- [T-SQL(Transact-SQL)을 사용하여 탄력적 작업 만들기 및 관리](elastic-jobs-tsql.md)
