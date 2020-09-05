---
title: 'PowerShell: SQL Managed Instance SQL Server 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell 및 Azure Database Migration Service를 사용 하 여 SQL Server에서 Azure SQL Managed Instance로 마이그레이션하는 방법에 대해 알아봅니다.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 37d61a5c199b59a13b54344f5ffaf69d7b369a1b
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078064"
---
# <a name="migrate-sql-server-to-sql-managed-instance-with-powershell--azure-database-migration-service"></a>PowerShell &를 사용 하 여 SQL Managed Instance로 SQL Server 마이그레이션 Azure Database Migration Service

이 문서에서는 Microsoft Azure PowerShell를 사용 하 여 SQL Server 2005 이상의 온-프레미스 인스턴스로 복원 된 **Adventureworks2016** 데이터베이스를 AZURE sql sql Managed Instance로 마이그레이션합니다. Microsoft Azure PowerShell에서 모듈을 사용 하 여 SQL Server 인스턴스에서 SQL Managed Instance로 데이터베이스를 마이그레이션할 수 있습니다 `Az.DataMigration` .

이 문서에서는 다음 방법을 설명합니다.
> [!div class="checklist"]
>
> * 리소스 그룹을 만듭니다.
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service의 인스턴스에서 마이그레이션 프로젝트를 만듭니다.
> * 마이그레이션을 실행합니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에는 온라인 및 오프 라인 마이그레이션을 모두 수행 하는 방법에 대 한 세부 정보가 포함 되어 있습니다.

## <a name="prerequisites"></a>필수 조건

이러한 단계를 완료하려면 다음이 필요합니다.

* [SQL Server 2016 이상](https://www.microsoft.com/sql-server/sql-server-downloads) (모든 버전)
* [여기](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)에서 다운로드할 수 있는 **AdventureWorks2016** 데이터베이스의 로컬 복사본입니다.
* SQL Server Express 설치에서 기본적으로 사용하지 않도록 설정된 TCP/IP 프로토콜을 사용하도록 설정합니다. [서버 네트워크 프로토콜 설정 또는 해제](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure) 문서에 따라 TCP/IP 프로토콜을 사용하도록 설정합니다.
* [데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성하려면
* Azure 구독 구독이 없으면 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/).
* SQL Managed Instance입니다. [ASQL Managed Instance 만들기](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)문서의 세부 정보에 따라 SQL Managed Instance를 만들 수 있습니다.
* [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 이상을 다운로드 하 여 설치 합니다.
* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대 한 사이트 간 연결을 제공 하는 Azure Database Migration Service를 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 만든 Microsoft Azure Virtual Network입니다.
* [SQL Server 마이그레이션 평가 수행](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)문서에 설명 된 대로 Data Migration Assistant를 사용 하 여 온-프레미스 데이터베이스 및 스키마 마이그레이션의 평가를 완료 했습니다.
* `Az.DataMigration` [Install-module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)을 사용 하 여 PowerShell 갤러리에서 모듈 (버전 0.7.2 이상)을 다운로드 하 고 설치 합니다.
* 원본 SQL Server 인스턴스에 연결 하는 데 사용 되는 자격 증명에 [CONTROL Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 권한이 있는지 확인 합니다.
* 대상 SQL Managed Instance에 연결 하는 데 사용 되는 자격 증명에 대상 SQL Managed Instance 데이터베이스에 대 한 CONTROL DATABASE 권한이 있는지 확인 합니다.

    > [!IMPORTANT]
    > 온라인 마이그레이션의 경우 이미 Azure Active Directory 자격 증명을 설정 해야 합니다. 자세한 내용은 [포털을 사용 하 여 리소스에 액세스할 수 있는 AZURE AD 응용 프로그램 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)문서를 참조 하세요.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure 구독에 로그인

PowerShell을 사용 하 여 Azure 구독에 로그인 합니다. 자세한 내용은 [Azure PowerShell를 사용 하 여 로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)문서를 참조 하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

명령을 사용 하 여 리소스 그룹을 만듭니다 [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) .

다음 예제에서는 *미국 동부* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스 만들기

`New-AzDataMigrationService` cmdlet을 사용하여 새 Azure Database Migration Service 인스턴스를 만들 수 있습니다.
이 cmdlet에는 다음 매개 변수가 필요합니다.

* *Azure 리소스 그룹 이름*입니다. [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)명령을 사용 하 여 이전에 표시 된 대로 Azure 리소스 그룹을 만들고 해당 이름을 매개 변수로 제공할 수 있습니다.
* *서비스 이름*입니다. Azure Database Migration Service의 원하는 고유 서비스 이름에 해당 하는 문자열입니다.
* *위치*. 서비스의 위치를 지정합니다. 미국 서 부 또는 동남 아시아와 같은 Azure 데이터 센터 위치를 지정 합니다.
* *Sku*. 이 매개 변수는 DMS Sku 이름에 해당합니다. 현재 지원 되는 Sku 이름은 *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*입니다.
* *가상 서브넷 식별자*. Cmdlet을 사용 하 여 [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) 서브넷을 만들 수 있습니다.

다음 예제에서는 *Mydms* 이라는 가상 네트워크와 *mydms*이라는 서브넷을 사용 하 여 *미국 동부* 지역에 있는 *Mydmsresourcegroup* 리소스 그룹에 *mydms* 라는 서비스를 만듭니다.

> [!IMPORTANT]
> 아래 코드 조각은 프리미엄 SKU를 기반으로 Azure Database Migration Service 인스턴스를 필요로 하지 않는 오프 라인 마이그레이션을 위한 것입니다. 온라인 마이그레이션의 경우-Sku 매개 변수 값에 Premium SKU가 포함 되어야 합니다.

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

Cmdlet을 사용 하 여 데이터베이스 연결 정보 개체를 만들 수 있습니다 `New-AzDmsConnInfo` . 여기에는 다음 매개 변수가 필요 합니다.

* *ServerType*. SQL, Oracle 또는 MySQL 등 요청된 데이터베이스 연결 유형입니다. SQL Server 및 Azure SQL에 대해 SQL을 사용합니다.
* *DataSource*. SQL Server 인스턴스 또는 Azure SQL Database 인스턴스의 이름 또는 IP입니다.
* *Authtype*. 연결에 대한 인증 유형이며 SqlAuthentication 또는 WindowsAuthentication일 수 있습니다.
* *Trustservercertificate*. 이 매개 변수는 신뢰의 유효성을 검사 하기 위해 인증서 체인을 우회 하는 동안 채널이 암호화 되는지 여부를 나타내는 값을 설정 합니다. 값은 `$true`, `$false`입니다.

다음 예에서는 SQL 인증을 사용 하 여 *MySourceSQLServer* 라는 원본 SQL Server에 대 한 연결 정보 개체를 만듭니다.

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

다음 예제에서는 ' targetmanagedinstance ' 라는 Azure SQL Managed Instance에 대 한 연결 정보를 만드는 방법을 보여 줍니다.

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>마이그레이션 프로젝트에 대한 데이터베이스 제공

`AzDataMigrationDatabaseInfo`프로젝트 만들기를 위한 매개 변수로 제공할 수 있는 Azure Database Migration Service 프로젝트의 일부로 데이터베이스를 지정 하는 개체 목록을 만듭니다. Cmdlet을 사용 하 여 `New-AzDataMigrationDatabaseInfo` 를 만들 수 있습니다 `AzDataMigrationDatabaseInfo` .

다음 예에서는 `AzDataMigrationDatabaseInfo` **AdventureWorks2016** 데이터베이스에 대 한 프로젝트를 만들고 프로젝트를 만들기 위한 매개 변수로 제공할 목록에 추가 합니다.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>프로젝트 개체 만들기

마지막으로,를 사용 하 여 *미국 동부* 에 있는 *MyDMSProject* 라는 Azure Database Migration Service 프로젝트를 만들고 `New-AzDataMigrationProject` 이전에 만든 원본 및 대상 연결과 마이그레이션할 데이터베이스 목록을 추가할 수 있습니다.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>마이그레이션 작업 만들기 및 시작

다음으로 Azure Database Migration Service 작업을 만들고 시작 합니다. 이 태스크에는 원본 및 대상 모두에 대 한 연결 자격 증명 정보 뿐만 아니라 마이그레이션할 데이터베이스 테이블 목록 및 필수 구성 요소로 만든 프로젝트에 이미 제공 된 정보가 필요 합니다.

### <a name="create-credential-parameters-for-source-and-target"></a>원본 및 대상에 대한 자격 증명 매개 변수 만들기

연결 보안 자격 증명을 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체로 만듭니다.

다음 예제에서는 원본 및 대상 연결에 대 한 *PSCredential* 개체를 만드는 방법을 보여 줍니다. *$sourcePassword* 는 암호를 문자열 변수로 제공 하 고 *$targetPassword*합니다.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>백업 파일 공유 개체 만들기

이제 cmdlet을 사용 하 여 원본 데이터베이스 백업을 수행할 수 Azure Database Migration Service 로컬 SMB 네트워크 공유를 나타내는 파일 공유 개체를 만듭니다 `New-AzDmsFileShare` .

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>선택한 데이터베이스 개체 만들기

다음 단계는 cmdlet을 사용 하 여 원본 및 대상 데이터베이스를 선택 하는 것입니다 `New-AzDmsSelectedDB` .

다음 예는 단일 데이터베이스를 SQL Server에서 Azure SQL Managed Instance로 마이그레이션하는 것입니다.

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

전체 SQL Server 인스턴스에 Azure SQL Managed Instance에 대 한 리프트 앤 시프트가 필요한 경우 원본에서 모든 데이터베이스를 가져오는 루프가 아래에 제공 됩니다. 다음 예에서는 $Server, $SourceUserName 및 $SourcePassword에 대해 소스 SQL Server 세부 정보를 제공 합니다.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure Storage 컨테이너에 대한 SAS URI

서비스에서 백업 파일을 업로드하는 스토리지 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI가 포함된 변수를 만듭니다.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure Database Migration Service는 계정 수준 SAS 토큰 사용을 지원 하지 않습니다. 저장소 계정 컨테이너에 대 한 SAS URI를 사용 해야 합니다. [Blob 컨테이너에 대 한 SAS URI를 가져오는 방법에 대해 알아봅니다](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>추가 구성 요구 사항

해결 해야 할 몇 가지 추가 요구 사항이 있지만 오프 라인 또는 온라인 마이그레이션을 수행 하 고 있는지 여부에 따라 달라 집니다.

#### <a name="offline-migrations"></a>오프 라인 마이그레이션

오프 라인 마이그레이션의 경우에만 다음 추가 구성 작업을 수행 합니다.

* **로그인을 선택**합니다. 다음 예제와 같이 마이그레이션할 로그인 목록을 만듭니다.

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 현재 Azure Database Migration Service는 SQL 로그인의 마이그레이션만 지원 합니다.

* **에이전트 작업을 선택**합니다. 다음 예제와 같이 마이그레이션할 에이전트 작업 목록을 만듭니다.

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 현재 Azure Database Migration Service는 T-sql 하위 시스템 작업 단계를 사용 하는 작업만 지원 합니다.

#### <a name="online-migrations"></a>온라인 마이그레이션

온라인 마이그레이션의 경우에만 다음 추가 구성 작업을 수행 합니다.

* **Azure Active Directory 앱 구성**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **저장소 리소스 구성**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>마이그레이션 작업 만들기 및 시작

`New-AzDataMigrationTask` cmdlet을 사용하여 마이그레이션 작업을 만들고 시작합니다.

#### <a name="specify-parameters"></a>매개 변수 지정

##### <a name="common-parameters"></a>일반 매개 변수

오프 라인 또는 온라인 마이그레이션을 수행 하 고 있는지 여부에 관계 없이 `New-AzDataMigrationTask` cmdlet에는 다음 매개 변수가 필요 합니다.

* *TaskType* Azure SQL Managed Instance SQL Server에 대해 만들 마이그레이션 작업의 유형 *MigrateSqlServerSqlDbMi* 이 필요 합니다. 
* *리소스 그룹 이름*입니다. 작업을 만들 Azure 리소스 그룹의 이름입니다.
* *ServiceName*. 작업을 만들 Azure Database Migration Service 인스턴스입니다.
* *ProjectName*. 작업을 만들 Azure Database Migration Service 프로젝트의 이름입니다. 
* *TaskName*. 만들 작업의 이름입니다. 
* *SourceConnection*. 원본 SQL Server 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *TargetConnection*. 대상 Azure SQL Managed Instance 연결을 나타내는 AzDmsConnInfo 개체입니다.
* *SourceCred*. 원본 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *TargetCred*. 대상 서버에 연결할 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 개체입니다.
* *SelectedDatabase*. 원본 및 대상 데이터베이스 매핑을 나타내는 AzDataMigrationSelectedDB 개체입니다.
* *BackupFileShare*. Azure Database Migration Service에서 원본 데이터베이스 백업을 수행할 수 있는 로컬 네트워크 공유를 나타내는 FileShare 개체입니다.
* *BackupBlobSasUri*. 서비스에서 백업 파일을 업로드하는 스토리지 계정 컨테이너에 대한 액세스를 Azure Database Migration Service에 제공하는 SAS URI입니다. Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아보세요.
* *Selectedlogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
* *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

##### <a name="additional-parameters"></a>추가 매개 변수

`New-AzDataMigrationTask`또한 cmdlet에는 수행 중인 마이그레이션 유형 (오프 라인 또는 온라인)에 고유한 매개 변수가 필요 합니다.

* **오프 라인 마이그레이션**. 오프 라인 마이그레이션의 경우에 `New-AzDataMigrationTask` 도 cmdlet에는 다음 매개 변수가 필요 합니다.

  * *Selectedlogins*. 마이그레이션하도록 선택한 로그인 목록입니다.
  * *SelectedAgentJobs*. 마이그레이션하도록 선택한 에이전트 작업 목록입니다.

* **온라인 마이그레이션**. 온라인 마이그레이션의 경우에 `New-AzDataMigrationTask` 도 cmdlet에는 다음 매개 변수가 필요 합니다.

* *AzureActiveDirectoryApp*. 응용 프로그램을 Active Directory 합니다.
* *StorageResourceID*. 저장소 계정의 리소스 ID입니다.

#### <a name="create-and-start-an-offline-migration-task"></a>오프 라인 마이그레이션 작업 만들기 및 시작

다음 예에서는 **myDMSTask**이라는 오프 라인 마이그레이션 작업을 만들고 시작 합니다.

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>온라인 마이그레이션 작업 만들기 및 시작

다음 예에서는 **myDMSTask**라는 온라인 마이그레이션 작업을 만들고 시작 합니다.

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

마이그레이션을 모니터링 하려면 다음 작업을 수행 합니다.

1. 모든 마이그레이션 세부 정보를 $CheckTask 라는 변수에 통합 합니다.

    마이그레이션과 관련 된 속성, 상태 및 데이터베이스 정보 등의 마이그레이션 정보를 결합 하려면 다음 코드 조각을 사용 합니다.

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 변수를 사용 `$CheckTask` 하 여 마이그레이션 작업의 현재 상태를 가져옵니다.

    변수를 사용 하 여 `$CheckTask` 마이그레이션 작업의 현재 상태를 가져오려면 다음 예제와 같이 작업의 상태 속성을 쿼리하여 실행 중인 마이그레이션 작업을 모니터링할 수 있습니다.

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>가공선 수행 (온라인 마이그레이션에만 해당)

온라인 마이그레이션을 사용 하 여 데이터베이스의 전체 백업 및 복원이 수행 된 다음 BackupFileShare 공유에 저장 된 트랜잭션 로그를 복원 하는 작업을 진행 합니다.

Azure SQL Managed Instance 데이터베이스를 최신 데이터로 업데이트 하 고 원본 데이터베이스와 동기화 하는 경우에는 조치를 수행할 수 있습니다.

다음 예에서는 cutover\migration.을 완료 합니다. 사용자는 신중 하 게이 명령을 호출 합니다.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure Database Migration Service 인스턴스를 삭제 하는 중

마이그레이션이 완료 되 면 Azure Database Migration Service 인스턴스를 삭제할 수 있습니다.

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>추가 리소스

추가 마이그레이션 시나리오 (원본/대상 쌍)에 대 한 자세한 내용은 Microsoft [데이터베이스 마이그레이션 가이드](https://datamigration.microsoft.com/)를 참조 하십시오.

## <a name="next-steps"></a>다음 단계

[Azure Database Migration Service 이란?](https://docs.microsoft.com/azure/dms/dms-overview)문서에서 Azure Database Migration Service에 대해 자세히 알아보세요.
