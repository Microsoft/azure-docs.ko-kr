---
title: PowerShell을 사용하여 탄력적 작업 에이전트 만들기
description: PowerShell을 사용하여 탄력적 작업 에이전트를 만드는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 0fe6a3b1d0e5216d923cfe3997826e7a727f1a2b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077350"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>PowerShell을 사용하여 탄력적 작업 에이전트 만들기
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[탄력적 작업(미리 보기)](job-automation-overview.md#elastic-database-jobs-preview)을 사용하면 여러 데이터페이스에 병렬적으로 하나 이상의 T-SQL(Transact-SQL) 스크립트를 실행할 수 있습니다.

이 자습서에서는 여러 데이터베이스에서 쿼리를 실행하는 데 필요한 단계를 알아봅니다.

> [!div class="checklist"]
> * 탄력적 작업 에이전트 만들기
> * 작업이 해당 대상에서 스크립트를 실행할 수 있도록 작업 자격 증명 만들기
> * 작업을 실행하려는 대상(서버, 탄력적 풀, 데이터베이스, 분할 데이터베이스 맵) 정의
> * 에이전트가 작업을 연결하고 실행하도록 대상 데이터베이스에서 데이터베이스 범위 자격 증명 만들기
> * 작업 만들기
> * 작업에 작업 단계 추가
> * 작업 실행 시작
> * 작업 모니터링

## <a name="prerequisites"></a>필수 구성 요소

업그레이드된 버전의 Elastic Database 작업에는 마이그레이션 중에 사용할 새로운 PowerShell cmdlet 집합이 포함됩니다. 이 새로운 cmdlet은 모든 기존 작업 자격 증명, 대상(데이터베이스, 서버, 사용자 지정 컬렉션), 작업 트리거, 작업 예약, 작업 콘텐츠 및 작업을 새로운 탄력적 작업 에이전트로 전환합니다.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>최신 탄력적 작업 cmdlet 설치

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정을 만듭니다](https://azure.microsoft.com/free/).

**Az.Sql** 모듈을 설치하여 최신 탄력적 작업 cmdlet을 가져옵니다. 관리자 권한으로 PowerShell에서 다음 명령을 실행합니다.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

이 자습서에는 **Az.Sql** 모듈 외에도 *SqlServer* PowerShell 모듈이 필요합니다. 자세한 내용은 [SQL Server PowerShell 모듈 설치](/sql/powershell/download-sql-server-ps-module)를 참조하세요.

## <a name="create-required-resources"></a>필수 리소스 만들기

탄력적 작업 에이전트를 만들려면 [작업 데이터베이스](job-automation-overview.md#job-database)로 사용할 데이터베이스(S0 이상)가 필요합니다.

아래 스크립트에서는 작업 데이터베이스로 사용할 새 리소스 그룹, 서버 및 데이터베이스를 만듭니다. 두 번째 스크립트에서는 작업을 실행할 수 있도록 빈 데이터베이스 2개가 포함된 두 번째 서버를 만듭니다.

[Azure 요구 사항](/azure/architecture/best-practices/resource-naming)을 준수하면 원하는 명명 규칙을 사용할 수 있도록 탄력적 작업에는 특정 명명 요구 사항이 포함되지 않습니다.

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>탄력적 작업 사용

탄력적 작업을 사용하려면 다음 명령을 실행하여 Azure 구독에서 해당 기능을 등록합니다. 탄력적 작업 에이전트를 프로비저닝하려는 구독에 대해 이 명령을 한 번씩 실행합니다. 작업 대상인 데이터베이스만 포함하는 구독은 등록할 필요가 없습니다.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>탄력적 작업 에이전트 만들기

탄력적 작업 에이전트는 작업을 생성하고 실행하고 관리하기 위한 Azure 리소스입니다. 에이전트는 일정에 따라 또는 일회성 작업으로 작업을 실행합니다.

*resourceGroupName*, *serverName* 및  *databaseName* 매개 변수가 모두 기존 리소스를 가리켜야 하므로 **New-AzSqlElasticJobAgent** cmdlet에는 Azure SQL Database의 데이터베이스가 이미 있어야 합니다.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>작업 자격 증명 만들기

작업은 데이터베이스 범위 자격 증명을 사용하여 실행 시 대상 그룹에서 지정한 대상 데이터베이스에 연결하고 스크립트를 실행합니다. 이러한 데이터베이스 범위 자격 증명이 대상 그룹 멤버 형식으로 사용되는 경우 해당 자격 증명을 사용하여 서버 또는 탄력적 풀의 모든 데이터베이스를 열거하기 위해 master 데이터베이스에 연결합니다.

데이터베이스 범위 자격 증명은 작업 데이터베이스에서 만들어져야 합니다. 모든 대상 데이터베이스는 작업이 성공적으로 완료되게 하려면 충분한 사용 권한으로 로그인해야 합니다.

![탄력적 작업 자격 증명](./media/elastic-jobs-powershell-create/job-credentials.png)

이미지의 자격 증명 외에도 다음 스크립트에서 **GRANT** 명령을 추가합니다. 이러한 사용 권한은 이 예제 작업에서 선택한 스크립트에 필요합니다. 이 예제에서는 대상 데이터베이스에 새 테이블을 만들기 때문에 각 대상 데이터베이스가 성공적으로 실행되려면 적절한 권한이 필요합니다.

작업 데이터베이스에서 필수 작업 자격 증명을 만들려면 다음 스크립트를 실행합니다.

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>작업을 실행할 대상 데이터베이스 정의

[대상 그룹](job-automation-overview.md#target-group)은 작업 단계에서 실행될 데이터베이스 중 하나 이상의 집합을 정의합니다.

다음 코드 조각은 *serverGroup* 및 *serverGroupExcludingDb2*라는 두 개의 대상 그룹을 만듭니다. *serverGroup*은 실행 시 서버에 존재하는 모든 데이터베이스를 대상으로 지정하고, *serverGroupExcludingDb2*는 *targetDb2*를 제외한 서버의 모든 데이터베이스를 대상으로 지정합니다.

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>작업 및 단계 만들기

이 예제에서는 작업 하나와 작업을 실행할 두 개의 작업 단계를 정의합니다. 첫 번째 작업 단계(*step1*)는 대상 그룹 *ServerGroup*의 모든 데이터베이스에서 새로운 테이블(*Step1Table*)을 만듭니다. 이전에 정의된 대상 그룹이 해당 항목을 제외하도록 지정되었기 때문에 두 번째 작업 단계(*step2*)는 *TargetDb2*의 모든 데이터베이스에서 새로운 테이블(*Step2Table*)을 만듭니다.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>작업 실행

작업을 즉시 시작하려면 다음 명령을 실행합니다.

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

성공적으로 완료한 후에 TargetDb1에서 새로운 두 개의 테이블이 표시되고 TargetDb2에서 새로운 하나의 테이블이 표시됩니다.

   ![SSMS에서 새 테이블 유효성 검사](./media/elastic-jobs-powershell-create/job-execution-verification.png)

작업이 나중에 실행되도록 예약할 수도 있습니다. 특정 시점에 작업이 실행되도록 예약하려면 다음 명령을 실행합니다.

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>작업 실행 상태 모니터링

다음 코드 조각은 작업 실행 세부 정보를 가져옵니다.

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

다음 표에서 가능한 작업 실행 상태를 나열합니다.

|시스템 상태|Description|
|:---|:---|
|**만든 날짜** | 작업 실행이 방금 만들어졌으며 아직 진행 중이 아닙니다.|
|**InProgress** | 작업 실행이 현재 진행 중입니다.|
|**WaitingForRetry** | 작업 실행이 해당 작업을 완료할 수 없어 다시 시도를 기다리고 있습니다.|
|**성공함** | 작업 실행이 성공적으로 완료되었습니다.|
|**SucceededWithSkipped** | 작업 실행이 성공적으로 완료되었지만 자식 중 일부를 건너뛰었습니다.|
|**실패** | 작업 실행이 실패했으며 해당 재시도 횟수를 소진했습니다.|
|**TimedOut** | 작업 실행 시간이 초과되었습니다.|
|**Canceled** | 작업 실행이 취소되었습니다.|
|**생략** | 동일한 작업 단계의 또 다른 실행이 동일한 대상에서 이미 실행 중이므로 작업 실행을 건너뛰었습니다.|
|**WaitingForChildJobExecutions** | 작업 실행에서 해당 자식 실행이 완료되기를 기다리고 있습니다.|

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹을 삭제하여 이 빠른 시작에서 만든 Azure 리소스를 삭제합니다.

> [!TIP]
> 이러한 작업을 계속 사용하려면 이 문서에서 만든 리소스를 정리하지 마세요.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

이 자습서에서 데이터베이스 집합에 대해 Transact-SQL 스크립트를 실행했습니다. 다음 작업을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 탄력적 작업 에이전트 만들기
> * 작업이 해당 대상에서 스크립트를 실행할 수 있도록 작업 자격 증명 만들기
> * 작업을 실행하려는 대상(서버, 탄력적 풀, 데이터베이스, 분할 데이터베이스 맵) 정의
> * 에이전트가 작업을 연결하고 실행하도록 대상 데이터베이스에서 데이터베이스 범위 자격 증명 만들기
> * 작업 만들기
> * 작업에 작업 단계 추가
> * 작업 실행 시작
> * 작업 모니터링

> [!div class="nextstepaction"]
> [Transact-SQL을 사용하여 탄력적 작업 관리](elastic-jobs-tsql-create-manage.md)
