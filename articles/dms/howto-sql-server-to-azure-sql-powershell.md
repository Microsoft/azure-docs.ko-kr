---
title: 'PowerShell: SQL Database로 SQL Server 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service에서 Azure PowerShell를 사용 하 여 SQL Server에서 Azure SQL Database로 데이터베이스를 마이그레이션하는 방법에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e83f69b2a46b54f92bbfe35ac96f870edf5cb807
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078030"
---
# <a name="migrate-a-sql-server-database-to-azure-sql-database-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure SQL Database SQL Server 데이터베이스 마이그레이션

이 문서에서는 Microsoft Azure PowerShell를 사용 하 여 Azure SQL Database SQL Server 2016 이상의 온-프레미스 인스턴스로 복원 된 **Adventureworks2012** 데이터베이스를 마이그레이션합니다. Microsoft Azure PowerShell에서 모듈을 사용 하 여 SQL Server 인스턴스에서 Azure SQL Database로 데이터베이스를 마이그레이션할 수 있습니다 `Az.DataMigration` .

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service의 인스턴스를 만듭니다.
> * Azure Database Migration Service 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.

## <a name="prerequisites"></a>필수 조건

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads)(모든 버전)
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure SQL Database 인스턴스. [Azure Portal에서 Azure SQL Database의 데이터베이스 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)문서에 있는 세부 정보에 따라 Azure SQL Database 인스턴스를 만들 수 있습니다.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 이상.
* 는 [express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Database Migration Service를 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Microsoft Azure Virtual Network를 만들었습니다.
* [SQL Server 마이그레이션 평가를 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) 하는 방법 문서에 설명 된 대로 Data Migration Assistant를 사용 하 여 온-프레미스 데이터베이스 및 스키마 마이그레이션의 평가를 완료 했습니다.
* [Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)을 사용 하 여 PowerShell 갤러리에서 microsoft.datamigration 모듈을 다운로드 하 고 설치 하려면 관리자 권한으로 실행을 사용 하 여 PowerShell 명령 창을 열어야 합니다.
* 원본 SQL Server 인스턴스에 연결하는 데 사용되는 자격 증명에 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)(서버 제어) 권한이 있어야 합니다.
* 대상 Azure SQL DB 인스턴스에 연결하는 데 사용되는 자격 증명에 대상 Azure SQL Database 데이터베이스에 대한 CONTROL DATABASE(데이터베이스 제어) 권한이 있어야 합니다.
* Azure 구독 계정이 없는 경우 시작 하기 전에 [무료](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

[Azure PowerShell을 사용하여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps) 문서의 지침에 따라 PowerShell을 사용하여 Azure 구독에 로그인합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 가상 머신을 만들려면 먼저 리소스 그룹을 만듭니다.

[AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령을 사용 하 여 리소스 그룹을 만듭니다.

다음 예제에서는 *E미국* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름*입니다. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 명령을 사용 하 여 이전에 표시 된 대로 Azure 리소스 그룹을 만들고 해당 이름을 매개 변수로 제공할 수 있습니다.
* *서비스 이름*입니다. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당하는 문자열입니다. 
* *위치*. 서비스의 위치를 지정합니다. 미국 서부 또는 동남 아시아 등 Azure 데이터 센터 위치를 지정합니다.
* *Sku*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원되는 Sku 이름은 *GeneralPurpose_4vCores*입니다.
* *가상 서브넷 식별자*. [AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet을 사용 하 여 서브넷을 만들 수 있습니다. 

다음 예제에서는 *MyVNET*이라는 가상 네트워크 및 *MySubnet*이라는 서브넷을 사용하여 *미국 동부* 지역에 있는 *MyDMSResourceGroup* 리소스 그룹에 *MyDMS*라는 서비스를 만듭니다.

```powershell
 $vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

Azure Database Migration Service 인스턴스를 만든 후 마이그레이션 프로젝트를 만듭니다. Azure Database Migration Service 프로젝트는 프로젝트의 일부로 마이그레이션할 데이터베이스의 목록뿐만 아니라 원본 및 대상 인스턴스 모두에 대한 연결 정보가 필요합니다.

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>원본 및 대상 연결에 대한 데이터베이스 연결 정보 개체 만들기

`New-AzDmsConnInfo` cmdlet을 사용하여 데이터베이스 연결 정보 개체를 만들 수 있습니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.

* *ServerType*. SQL, Oracle 또는 MySQL 등 요청된 데이터베이스 연결 유형입니다. SQL Server 및 Azure SQL에 대해 SQL을 사용합니다.
* *DataSource*. SQL Server 인스턴스 또는 Azure SQL Database의 이름 또는 IP입니다.
* *Authtype*. 연결에 대한 인증 유형이며 SqlAuthentication 또는 WindowsAuthentication일 수 있습니다.
* *TrustServerCertificate* 매개 변수는 인증서 체인 검사를 무시할 때 채널의 암호화 여부를 나타내는 값을 설정하여 신뢰의 유효성을 검사합니다. 값은 True 또는 False일 수 있습니다.

다음 예제에서는 SQL 인증을 사용하여 MySourceSQLServer라는 원본 SQL Server에 대한 연결 정보 개체를 만듭니다.

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

다음 예제에서는 sql 인증을 사용 하 여 SQLAzureTarget 이라는 서버에 대 한 연결 정보를 만드는 방법을 보여 줍니다.

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "sqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>마이그레이션 프로젝트에 대한 데이터베이스 제공

프로젝트 만들기를 위한 매개 변수로 제공될 수 있는 Azure Database Migration 프로젝트의 일부로 데이터베이스를 지정하는 `AzDataMigrationDatabaseInfo` 개체의 목록을 만듭니다. Cmdlet을 `New-AzDataMigrationDatabaseInfo` 사용 하 여 AzDataMigrationDatabaseInfo를 만들 수 있습니다. 

다음 예제에서는 **AdventureWorks2016** 데이터베이스에 대한 `AzDataMigrationDatabaseInfo` 프로젝트를 만들고, 프로젝트 생성을 위한 매개 변수로 제공할 목록에 이를 추가합니다.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>프로젝트 개체 만들기

마지막으로 `New-AzDataMigrationProject`를 사용하여 이전에 만든 원본 및 대상 연결과 마이그레이션할 데이터베이스 목록을 추가하면 *미국 동부*에 있는 *MyDMSProject*라는 Azure Database Migration 프로젝트를 만들 수 있습니다.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작

마지막으로 Azure Database Migration 작업을 만들고 시작합니다. Azure Database Migration 작업은 필수 조건으로 만들어진 프로젝트와 이미 제공된 정보 외에도 원본 및 대상 모두에 대한 연결 자격 증명 정보와 마이그레이션할 데이터베이스 테이블 목록이 필요합니다.

### <a name="create-credential-parameters-for-source-and-target"></a>원본 및 대상에 대한 자격 증명 매개 변수 만들기

[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체로 연결 보안 자격 증명을 만들 수 있습니다.

다음 예제에서는 *$sourcePassword* 및 *$targetPassword* 문자열 변수로 암호를 제공하여 원본 및 대상 연결 모두에 대한 *PSCredential* 개체 만들기를 보여 줍니다.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>테이블 맵 만들기 및 마이그레이션할 원본 및 대상 매개 변수 선택

마이그레이션에 필요한 다른 매개 변수는 원본에서 대상으로 마이그레이션할 테이블의 매핑입니다. 마이그레이션할 원본 및 대상 테이블 간에 매핑을 제공하는 테이블 사전을 만듭니다. 다음 예제에서는 AdventureWorks 2016 데이터베이스에 대한 원본 및 대상 테이블 인적 자원 스키마 간의 매핑을 보여 줍니다.

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

다음 단계는 원본 및 대상 데이터베이스를 선택하고 `New-AzDmsSelectedDB` cmdlet을 사용하여 매개 변수로 마이그레이션할 테이블 매핑을 제공하는 것입니다(다음 예제 참조).

```powershell
$selectedDbs = New-AzDmsSelectedDB -MigrateSqlServerSqlDb -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-the-migration-task-and-start-it"></a>마이그레이션 작업을 만들고 시작 합니다.

`New-AzDataMigrationTask` cmdlet을 사용하여 마이그레이션 작업을 만들고 시작합니다. 이 cmdlet에는 다음 매개 변수가 필요합니다.

* *TaskType* SQL Server에서 Azure SQL Database로 마이그레이션 유형을 만드는 마이그레이션 작업 유형 *MigrateSqlServerSqlDb*가 필요합니다. 
* *리소스 그룹 이름*입니다. 작업을 만들 Azure 리소스 그룹의 이름입니다.
* *ServiceName*. 작업을 만들 Azure Database Migration Service 인스턴스입니다.
* *ProjectName*. 작업을 만들 Azure Database Migration Service 프로젝트의 이름입니다. 
* *TaskName*. 만들 작업의 이름입니다. 
* *SourceConnection*. 원본 SQL Server 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *TargetConnection*. 대상 Azure SQL Database 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *SourceCred*. 원본 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *TargetCred*. 대상 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *SelectedDatabase*. 원본 및 대상 데이터베이스 매핑을 나타내는 AzDataMigrationSelectedDB 개체입니다.
* *Schemavalidation*. (선택 사항, 스위치 매개 변수) 마이그레이션 후는 원본과 대상 간의 스키마 정보를 비교 합니다.
* *Data/Ityvalidation*. (선택 사항, 스위치 매개 변수) 마이그레이션 후는 원본 및 대상 간에 체크섬 기반 데이터 무결성 유효성 검사를 수행 합니다.
* *QueryAnalysisValidation*. (선택 사항, 스위치 매개 변수) 마이그레이션 후는 원본 데이터베이스에서 쿼리를 검색 하 고 대상에서 쿼리를 실행 하 여 빠르고 지능적인 쿼리 분석을 수행 합니다.

다음 예제에서는 myDMSTask라는 마이그레이션 작업을 만들고 시작합니다.

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
```

다음 예에서는 위와 동일한 마이그레이션 작업을 만들고 시작 하지만 세 가지 유효성 검사를 모두 수행 합니다.

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -SchemaValidation `
  -DataIntegrityValidation `
  -QueryAnalysisValidation `
```

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

다음 예제와 같이 작업의 상태 속성을 쿼리하여 실행 중인 마이그레이션 작업을 모니터링할 수 있습니다.

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="deleting-the-dms-instance"></a>DMS 인스턴스 삭제

마이그레이션이 완료된 후에는 Azure DMS 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="next-step"></a>다음 단계

* Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)의 마이그레이션 지침을 검토합니다.
