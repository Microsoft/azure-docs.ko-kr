---
title: Azure SQL Managed Instance와 SQL Server 간의 트랜잭션 복제 구성
description: 프라이빗 DNS 영역 및 VPN 피어링과 같은 필수 네트워킹 구성 요소와 함께 Azure VM의 게시자 관리형 인스턴스, 배포자 관리형 인스턴스 및 SQL Server 구독자 간의 복제를 구성하는 자습서입니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: d89bc33b0ddd0793a3c55dbd64bef9678bd723e7
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920146"
---
# <a name="tutorial-configure-transactional-replication-between-azure-sql-managed-instance-and-sql-server"></a>자습서: Azure SQL Managed Instance와 SQL Server 간의 트랜잭션 복제 구성
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

트랜잭션 복제를 사용하면 한 데이터베이스의 SQL Server 또는 [Azure SQL Managed Instance](sql-managed-instance-paas-overview.md)에서 호스팅되는 다른 데이터베이스로 데이터를 복제할 수 있습니다. SQL Managed Instance는 복제 토폴로지의 게시자, 배포자 또는 구독자일 수 있습니다. 사용 가능한 구성에 대해서는 [트랜잭션 복제 구성](replication-transactional-overview.md#common-configurations)을 참조하세요. 

트랜잭션 복제는 현재 SQL Managed Instance에 대한 공개 미리 보기로 제공됩니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> - 관리되는 인스턴스를 복제 게시자로 구성합니다.
> - 관리되는 인스턴스를 복제 배포자로 구성합니다.
> - SQL Server를 구독자로 구성합니다.

![관리되는 인스턴스 구독자, 관리되는 인스턴스 배포자 및 SQL Server 구독자 간 복제](./media/replication-two-instances-and-sql-server-configure-tutorial/sqlmi-to-sql-replication.png)

이 자습서는 숙련된 사용자를 대상으로 하며, 사용자가 Azure 내에서 관리형 인스턴스와 SQL Server VM 모두를 배포하고 연결하는 데 익숙하다고 가정합니다. 


> [!NOTE]
> 이 문서에서는 Azure SQL Managed Instance에서 [트랜잭션 복제](https://docs.microsoft.com/sql/relational-databases/replication/transactional/transactional-replication)를 사용하는 방법을 설명합니다. 개별 인스턴스의 전체 읽기 가능 복제본을 만들 수 있는 Azure SQL Managed Instance 기능인 [장애 조치(failover) 그룹](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group)과는 관련이 없습니다. [장애 조치(failover) 그룹을 사용하여 트랜잭션 복제](replication-transactional-overview.md#with-failover-groups)를 구성할 때 추가 고려 사항이 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 조건이 충족되어야 합니다.

- [Azure 구독](https://azure.microsoft.com/free/).
- 동일한 가상 네트워크 내에 두 개의 관리형 인스턴스를 배포하는 환경
- SQL Server 구독자(온-프레미스 VM 또는 Azure VM). 이 자습서에서는 Azure VM을 사용합니다.  
- [SSMS(SQL Server Management Studio) 18.0 이상](/sql/ssms/download-sql-server-management-studio-ssms).
- 최신 버전의 [Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0)
- 445 및 1433 포트는 Azure 방화벽과 Windows 방화벽 모두에서 SQL 트래픽을 허용합니다.

## <a name="1---create-the-resource-group"></a>1 - 리소스 그룹 만들기

다음 PowerShell 코드 조각을 사용하여 새 리소스 그룹을 만듭니다.

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - 두 개의 관리형 인스턴스 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 두 개의 관리형 인스턴스를 이 새 리소스 그룹 내에 만듭니다.

- 게시자 관리형 인스턴스의 이름은 `sql-mi-publisher`(임의 지정의 경우 몇 개 문자 포함)여야 하며, 가상 네트워크 이름은 `vnet-sql-mi-publisher`여야 합니다.
- 배포자 관리형 인스턴스의 이름은 `sql-mi-distributor`(임의 지정의 경우 몇 개 문자 포함)여야 하며, _게시자 관리형 인스턴스와 동일한 가상 네트워크_에 있어야 합니다.

   ![배포자에 게시자 VNet 사용](./media/replication-two-instances-and-sql-server-configure-tutorial/use-same-vnet-for-distributor.png)

관리형 인스턴스를 만드는 방법에 대한 자세한 내용은 [포털에서 관리형 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.

  > [!NOTE]
  > 편의상 그리고 가장 일반적인 구성이므로 이 자습서에서는 배포자 관리형 인스턴스를 게시자와 동일한 가상 네트워크 내에 배치하는 것이 좋습니다. 그러나 배포자를 별도의 가상 네트워크에 만들 수 있습니다. 이렇게 하려면 게시자와 배포자의 가상 네트워크 간에 VPN 피어링을 구성한 다음, 배포자와 구독자의 가상 네트워크 간에 VPN 피어링을 구성해야 합니다.

## <a name="3---create-a-sql-server-vm"></a>3 - SQL Server VM 만들기

[Azure Portal](https://portal.azure.com)을 사용하여 SQL Server 가상 머신을 만듭니다. SQL Server 가상 머신의 특징은 다음과 같습니다.

- 이름: `sql-vm-sub`
- 이미지: SQL Server 2016 이상
- 리소스 그룹: 관리형 인스턴스와 동일
- 가상 네트워크: `sql-vm-sub-vnet`

SQL Server VM을 Azure에 배포하는 방법에 대한 자세한 내용은 [빠른 시작: SQL Server VM 만들기](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)를 참조하세요.

## <a name="4---configure-vpn-peering"></a>4 - VPN 피어링 구성

두 개의 관리형 인스턴스의 가상 네트워크와 SQL Server의 가상 네트워크 간의 통신을 사용하도록 VPN 피어링을 구성합니다. 이렇게 하려면 다음 PowerShell 코드 조각을 사용합니다.

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber VNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

VPN 피어링이 설정되면 SQL Server에서 SSMS(SQL Server Management Studio)를 시작하고 두 개의 관리형 인스턴스에 연결하여 연결을 테스트합니다. SSMS를 사용하여 관리형 인스턴스에 연결하는 방법에 대한 자세한 내용은 [SSMS를 사용하여 SQL Managed Instance에 연결](point-to-site-p2s-configure.md#connect-with-ssms)을 참조하세요.

![관리형 인스턴스에 대한 연결 테스트](./media/replication-two-instances-and-sql-server-configure-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-a-private-dns-zone"></a>5 - 프라이빗 DNS 영역 만들기

프라이빗 DNS 영역을 사용하면 관리형 인스턴스와 SQL Server 간의 DNS 라우팅을 사용할 수 있습니다.

### <a name="create-a-private-dns-zone"></a>프라이빗 DNS 영역 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **리소스 만들기**를 선택하여 새 Azure 리소스를 만듭니다.
1. Azure Marketplace에서 `private dns zone`을 검색합니다.
1. Microsoft에서 게시한 **프라이빗 DNS 영역** 리소스를 선택한 다음, **만들기**를 선택하여 DNS 영역을 만듭니다.
1. 드롭다운에서 구독 및 리소스 그룹을 선택합니다.
1. DNS 영역에 대한 임의의 이름(예: `repldns.com`)을 제공합니다.

   ![프라이빗 DNS 영역 만들기](./media/replication-two-instances-and-sql-server-configure-tutorial/create-private-dns-zone.png)

1. **검토 + 만들기**를 선택합니다. 프라이빗 DNS 영역에 대한 매개 변수를 검토한 다음, **만들기**를 선택하여 리소스를 만듭니다.

### <a name="create-an-a-record"></a>A 레코드 만들기

1. 새 **프라이빗 DNS 영역**으로 이동하여 **개요**를 선택합니다.
1. **+ 레코드 집합**을 선택하여 새 A 레코드를 만듭니다.
1. SQL Server VM의 이름과 개인 내부 IP 주소를 입력합니다.

   ![A 레코드 구성](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-a-record.png)

1. **확인**을 선택하여 A 레코드를 만듭니다.

### <a name="link-the-virtual-network"></a>가상 네트워크 연결

1. 새 **프라이빗 DNS 영역**으로 이동하여 **가상 네트워크 링크**를 선택합니다.
1. **+추가**를 선택합니다.
1. 링크 이름(예: `Pub-link`)을 입력합니다.
1. 드롭다운에서 구독을 선택한 다음, 게시자 관리형 인스턴스의 가상 네트워크를 선택합니다.
1. **자동 등록 사용** 옆의 확인란을 선택합니다.

   ![VNet 링크 만들기](./media/replication-two-instances-and-sql-server-configure-tutorial/configure-vnet-link.png)

1. **확인**을 선택하여 가상 네트워크를 연결합니다.
1. `Sub-link`와 같은 이름을 사용하여 구독자 가상 네트워크에 대한 링크를 추가하려면 이러한 단계를 반복합니다.

## <a name="6---create-an-azure-storage-account"></a>6 - Azure 스토리지 계정 만들기

작업 디렉터리에 대한 [Azure 스토리지 계정](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account)을 만든 다음, 스토리지 계정 내에서 [파일 공유](../../storage/files/storage-how-to-create-file-share.md)를 만듭니다.

파일 공유 경로를 `\\storage-account-name.file.core.windows.net\file-share-name` 형식으로 복사합니다.

예: `\\replstorage.file.core.windows.net\replshare`

스토리지 액세스 키 연결 문자열을 `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net` 형식으로 복사합니다.

예: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

자세한 내용은 [스토리지 계정 액세스 키 관리](../../storage/common/storage-account-keys-manage.md)를 참조하세요.

## <a name="7---create-a-database"></a>7 - 데이터베이스 만들기

게시자 관리되는 인스턴스에서 새 데이터베이스를 만듭니다. 이렇게 하려면 다음 단계를 따르십시오.

1. SQL 서버에서 SQL Server Management Studio를 시작합니다.
1. `sql-mi-publisher` 관리형 인스턴스에 연결합니다.
1. **새 쿼리** 창을 열고, 다음 T-SQL 쿼리를 실행하여 데이터베이스를 만듭니다.

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
   ID INT NOT NULL PRIMARY KEY,
   c1 VARCHAR(100) NOT NULL,
   dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - 배포 구성

연결이 설정되고 샘플 데이터베이스가 있으면 `sql-mi-distributor` 관리형 인스턴스에서 배포를 구성할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. SQL 서버에서 SQL Server Management Studio를 시작합니다.
1. `sql-mi-distributor` 관리형 인스턴스에 연결합니다.
1. **새 쿼리** 창을 열고, 다음 Transact-SQL 코드를 실행하여 배포자 관리형 인스턴스에서 배포를 구성합니다.

   ```sql
   EXEC sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   
   EXEC sp_adddistributiondb @database = N'distribution'
   
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > @working_directory 매개 변수에는 백슬래시(`\`)만 사용해야 합니다. 슬래시(`/`)를 사용하면 파일 공유에 연결할 때 오류가 발생할 수 있습니다.

1. `sql-mi-publisher` 관리형 인스턴스에 연결합니다.
1. **새 쿼리** 창을 열고, 다음 Transact-SQL 코드를 실행하여 배포자를 게시자에 등록합니다.

   ```sql
   Use MASTER
   EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>'
   ```

## <a name="9---create-the-publication"></a>9 - 게시 만들기

배포가 구성되면 이제 게시를 만들 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. SQL 서버에서 SQL Server Management Studio를 시작합니다.
1. `sql-mi-publisher` 관리형 인스턴스에 연결합니다.
1. **개체 탐색기**에서 **복제** 노드를 펼치고, 마우스 오른쪽 단추로 **로컬 게시** 폴더를 클릭합니다. **새 게시...** 를 선택합니다.
1. **다음**을 선택하여 시작 페이지를 지나서 이동합니다.
1. **게시 데이터베이스** 페이지에서 이전에 만든 `ReplTutorial` 데이터베이스를 선택합니다. **다음**을 선택합니다.
1. **게시 유형** 페이지에서 **트랜잭션 게시**를 선택합니다. **다음**을 선택합니다.
1. **문서** 페이지에서 **테이블** 옆의 확인란을 선택합니다. **다음**을 선택합니다.
1. **테이블 행 필터** 페이지에서 필터를 추가하지 않고 **다음**을 선택합니다.
1. **스냅샷 에이전트** 페이지에서 **즉시 스냅샷을 만들고 구독 초기화에 사용할 수 있도록 유지합니다.** 옆의 확인란을 선택합니다. **다음**을 선택합니다.
1. **에이전트 보안** 페이지에서 **보안 설정...** 을 선택합니다. 스냅샷 에이전트에 사용할 SQL Server 로그인 자격 증명을 제공하고, 게시자에 연결합니다. **확인**을 선택하여 **스냅샷 에이전트 보안** 페이지를 닫습니다. **다음**을 선택합니다.

   ![스냅샷 에이전트 보안 구성](./media/replication-two-instances-and-sql-server-configure-tutorial/snapshot-agent-security.png)

1. **마법사 작업** 페이지에서 **게시 만들기**를 선택하고, 필요에 따라 이 스크립트를 나중에 저장하려면 **게시 생성 단계를 포함하는 스크립트 파일 생성**을 선택합니다.
1. **마법사 완료** 페이지에서 게시 이름으로 `ReplTest`를 지정하고, **다음**을 선택하여 게시를 만듭니다.
1. 게시가 만들어지면 **개체 탐색기**에서 **복제** 노드를 새로 고치고, **로컬 게시**를 펼쳐 새 게시를 확인합니다.

## <a name="10---create-the-subscription"></a>10 - 구독 만들기

게시가 만들어지면 구독을 만들 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오.

1. SQL 서버에서 SQL Server Management Studio를 시작합니다.
1. `sql-mi-publisher` 관리형 인스턴스에 연결합니다.
1. **새 쿼리** 창을 열고, 다음 Transact-SQL 코드를 실행하여 구독 및 배포 에이전트를 추가합니다. DNS를 구독자 이름의 일부로 사용합니다.

```sql
use [ReplTutorial]
exec sp_addsubscription
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub',
@subscription_type = N'Push',
@sync_type = N'automatic',
@article = N'all',
@update_mode = N'read only',
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub',
@job_login = N'azureuser',
@job_password = '<Complex Password>',
@subscriber_security_mode = 0,
@subscriber_login = N'azureuser',
@subscriber_password = '<Complex Password>',
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - 복제 테스트

복제가 구성되면 새 항목을 게시자에 삽입하고 구독자에 전파되는 변경 내용을 감시하여 이를 테스트할 수 있습니다.

다음 T-SQL 코드 조각을 실행하여 구독자의 행을 확인합니다.

```sql
Use ReplSub
select * from dbo.ReplTest
```

다음 T-SQL 코드 조각을 실행하여 추가 행을 게시자에 삽입한 다음, 구독자에서 해당 행을 다시 확인합니다.

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>리소스 정리

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동합니다.
1. 관리형 인스턴스를 선택한 다음, **삭제**를 선택합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제**를 선택합니다. 이 프로세스는 백그라운드에서 완료하는 데 다소 시간이 걸릴 수 있으며, 완료될 때까지 *가상 클러스터* 또는 기타 종속 리소스를 삭제할 수 없습니다. **활동** 탭에서 삭제를 모니터링하여 관리되는 인스턴스가 삭제되었는지 확인합니다.
1. 관리되는 인스턴스가 삭제되면 리소스 그룹에서 *가상 클러스터*를 선택한 다음, **삭제**를 선택하여 해당 클러스터를 삭제합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제**를 선택합니다.
1. 나머지 리소스를 삭제합니다. 텍스트 상자에서 `yes`를 입력하여 리소스를 삭제할지 확인한 다음, **삭제**를 선택합니다.
1. **리소스 그룹 삭제**를 선택하고, 리소스 그룹 이름(`myResourceGroup`)을 입력한 다음, **삭제**를 선택하여 해당 리소스 그룹을 삭제합니다.

## <a name="known-errors"></a>알려진 오류

### <a name="windows-logins-are-not-supported"></a>Windows 로그인이 지원되지 않음

`Exception Message: Windows logins are not supported in this version of SQL Server.`

에이전트가 Windows 로그인을 사용하여 구성되었으므로 SQL Server 로그인을 대신 사용해야 합니다. **게시 속성**의 **에이전트 보안** 페이지를 사용하여 로그인 자격 증명을 SQL Server 로그인으로 변경합니다.

### <a name="failed-to-connect-to-azure-storage"></a>Azure Storage에 연결하지 못함

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 replstorage에 대한 Azure Storage 연결 문자열을 가져왔습니다. 2019-11-19 02:21:05.07 Azure Files Storage '\\replstorage.file.core.windows.net\replshare'에 연결하는 중입니다. 2019-11-19 02:21:31.21 53 OS 오류로 인해 Azure Storage ''에 연결하지 못했습니다.

이는 Azure 방화벽, Windows 방화벽 또는 둘 다에서 445 포트가 닫혀 있기 때문일 수 있습니다.

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

파일 공유의 파일 경로에서 백슬래시 대신 슬래시를 사용하면 이 오류가 발생할 수 있습니다.
  
  - 정상: `\\replstorage.file.core.windows.net\replshare`
  - OS 55 오류가 발생할 수 있음: `'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>구독자에 연결할 수 없음

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

가능한 해결 방법:

- 1433 포트가 열려 있는지 확인합니다.
- 구독자에서 TCP/IP를 사용하도록 설정되었는지 확인합니다.
- 구독자를 만들 때 DNS 이름이 사용되었는지 확인합니다.
- 가상 네트워크가 프라이빗 DNS 영역에 올바르게 연결되었는지 확인합니다.
- A 레코드가 올바르게 구성되었는지 확인합니다.
- VPN 피어링이 올바르게 구성되었는지 확인합니다.

### <a name="no-publications-to-which-you-can-subscribe"></a>구독할 수 있는 게시가 없음

**새 구독** 마법사를 사용하여 새 구독을 추가할 때 **게시** 페이지에서 사용 가능한 옵션으로 나열된 데이터베이스 및 게시가 없을 수 있으며 다음 오류 메시지가 표시될 수 있습니다.

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`

이 오류 메시지는 정확하고 연결된 게시자에서 사용할 수 있는 게시가 없거나 권한이 부족한 경우에도 이 오류는 이전 버전의 SQL Server Management Studio에서 발생할 수 있습니다. 이를 근본 원인으로 제외하려면 SQL Server Management Studio 18.0 이상으로 업그레이드해 보세요.

## <a name="next-steps"></a>다음 단계

### <a name="enable-security-features"></a>보안 기능 사용

데이터베이스를 보호하는 방법에 대한 전체 목록은 [Azure SQL Managed Instance란?](sql-managed-instance-paas-overview.md#advanced-security-and-compliance) 문서를 참조하세요. 다음과 같은 보안 기능을 설명합니다.

- [SQL Managed Instance 감사](auditing-configure.md)
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [위협 감지](threat-detection-configure.md)
- [동적 데이터 마스킹](/sql/relational-databases/security/dynamic-data-masking)
- [행 수준 보안](/sql/relational-databases/security/row-level-security)
- [TDE(투명한 데이터 암호화)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="sql-managed-instance-capabilities"></a>SQL Managed Instance 기능

관리되는 인스턴스 기능의 전체 개요는

> [!div class="nextstepaction"]
> [SQL Managed Instance 기능](sql-managed-instance-paas-overview.md)을 참조하세요.
