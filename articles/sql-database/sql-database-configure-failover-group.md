---
title: 장애 조치 (failover) 그룹 구성
description: Azure Portal, Az CLI 및 PowerShell을 사용 하 여 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스에 대해 자동 장애 조치 (failover) 그룹을 구성 하는 방법에 대해 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: 05b099eebcbb7b8f77357c9dcf3a4d567d3886d6
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553072"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL Database에 대 한 장애 조치 (failover) 그룹 구성

이 항목에서는 Azure Portal 또는 PowerShell을 사용 하 여 Azure SQL Database 단일 데이터베이스, 탄력적 풀 및 관리 되는 인스턴스에 대해 [자동 장애 조치 (failover) 그룹](sql-database-auto-failover-group.md) 을 구성 하는 방법을 설명 합니다. 

## <a name="single-database"></a>단일 데이터베이스
장애 조치 (failover) 그룹을 만들고 Azure Portal 또는 PowerShell을 사용 하 여 단일 데이터베이스를 추가 합니다.

### <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 고려 합니다.

- 보조 서버에 대 한 서버 로그인 및 방화벽 설정은 주 서버의 서버 로그인 및 방화벽 설정과 일치 해야 합니다. 

### <a name="create-failover-group"></a>장애 조치 (failover) 그룹 만들기

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
장애 조치 (failover) 그룹을 만들고 Azure Portal를 사용 하 여 단일 데이터베이스를 추가 합니다.


1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 (failover) 그룹에 추가 하려는 단일 데이터베이스를 선택 합니다. 
1. 서버 **이름** 아래에서 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.

   ![단일 db에 대해 서버 열기](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 **그룹 추가** 를 선택 하 여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 (failover) 그룹 추가](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **장애 조치 (Failover) 그룹** 페이지에서 필요한 값을 입력 하거나 선택한 다음 **만들기**를 선택 합니다.

   - **그룹 내의 데이터베이스**: 장애 조치 (failover) 그룹에 추가 하려는 데이터베이스를 선택 합니다. 장애 조치 (failover) 그룹에 데이터베이스를 추가 하면 지역에서 복제 프로세스가 자동으로 시작 됩니다. 
        
    ![장애 조치 (failover) 그룹에 SQL DB 추가](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용 하 여 장애 조치 그룹을 만들고 단일 데이터베이스를 추가 합니다. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

---

### <a name="test-failover"></a>테스트 장애 조치 

Azure Portal 또는 PowerShell을 사용 하 여 장애 조치 (failover) 그룹의 장애 조치를 테스트 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 장애 조치 (failover) 그룹의 장애 조치를 테스트 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 (failover) 그룹에 추가 하려는 단일 데이터베이스를 선택 합니다. 

   ![단일 db에 대해 서버 열기](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 방금 만든 장애 조치 (failover) 그룹을 선택 합니다. 
  
   ![포털에서 장애 조치 (failover) 그룹을 선택 합니다.](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 주 서버 및 보조 서버를 검토 합니다. 
1. 단일 데이터베이스가 포함 된 장애 조치 (failover) 그룹을 장애 조치 (Failover) 하려면 작업 창에서 **장애 조치** 를 선택 합니다. 
1. TDS 세션의 연결을 끊을 것을 알리는 경고에서 **예** 를 선택 합니다. 

   ![SQL database를 포함 하는 장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 현재 주 서버와 보조 서버가 있는 서버를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 서버에 교환 된 역할이 있어야 합니다. 
1. **장애 조치 (Failover)** 를 다시 선택 하 여 서버가 원래 역할로 다시 장애 조치 (Failover) 합니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 장애 조치 그룹의 장애 조치를 테스트 합니다.  

보조 복제본의 역할을 확인 합니다. 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
보조 서버로 장애 조치 (failover): 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제 해야 하는 경우 해당 데이터베이스를 삭제 하기 전에 장애 조치 (failover) 그룹에서 제거 합니다. 장애 조치 (failover) 그룹에서 보조 데이터베이스를 제거 하기 전에 삭제 하면 예기치 않은 동작이 발생할 수 있습니다. 

## <a name="elastic-pool"></a>Elastic Pool
장애 조치 (failover) 그룹을 만들고 Azure Portal 또는 PowerShell을 사용 하 여 탄력적 풀을 추가 합니다.  

### <a name="prerequisites"></a>필수 조건

다음 필수 구성 요소를 고려 합니다.

- 보조 서버에 대 한 서버 로그인 및 방화벽 설정은 주 서버의 서버 로그인 및 방화벽 설정과 일치 해야 합니다. 

### <a name="create-the-failover-group"></a>장애 조치 (failover) 그룹 만들기 

Azure Portal 또는 PowerShell을 사용 하 여 탄력적 풀에 대 한 장애 조치 (failover) 그룹을 만듭니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
장애 조치 (failover) 그룹을 만들고 Azure Portal를 사용 하 여 탄력적 풀을 추가 합니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 (failover) 그룹에 추가 하려는 탄력적 풀을 선택 합니다. 
1. **개요** 창에서 서버 **이름** 아래의 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.
  
    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 **그룹 추가** 를 선택 하 여 새 장애 조치 그룹을 만듭니다. 

    ![새 장애 조치 (failover) 그룹 추가](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **장애 조치 (Failover) 그룹** 페이지에서 필요한 값을 입력 하거나 선택한 다음 **만들기**를 선택 합니다. 새 보조 서버를 만들거나 기존 보조 서버를 선택 합니다. 

1. **그룹 내의 데이터베이스** 를 선택한 다음 장애 조치 (failover) 그룹에 추가할 탄력적 풀을 선택 합니다. 탄력적 풀이 보조 서버에 아직 없는 경우 보조 서버에서 탄력적 풀을 만드는 메시지가 표시 됩니다. 경고를 선택한 다음 **확인** 을 선택 하 여 보조 서버에서 탄력적 풀을 만듭니다. 
        
    ![장애 조치 (failover) 그룹에 탄력적 풀 추가](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. **선택** 을 선택 하 여 탄력적 풀 설정을 장애 조치 (failover) 그룹에 적용 한 다음 **만들기** 를 선택 하 여 장애 조치 그룹을 만듭니다. 장애 조치 (failover) 그룹에 탄력적 풀을 추가 하면 지역에서 복제 프로세스가 자동으로 시작 됩니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

장애 조치 (failover) 그룹을 만들고 PowerShell을 사용 하 여 탄력적 풀을 추가 합니다. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   Write-host "Databases added to failover group successfully." 
  ```

---

### <a name="test-failover"></a>테스트 장애 조치

Azure Portal 또는 PowerShell을 사용 하 여 탄력적 풀의 장애 조치 (failover)를 테스트 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

장애 조치 (failover) 그룹을 보조 서버로 장애 조치 (failover) 한 다음 Azure Portal를 사용 하 여 장애 복구 (failback) 합니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 (failover) 그룹에 추가 하려는 탄력적 풀을 선택 합니다. 
1. **개요** 창에서 서버 **이름** 아래의 서버 이름을 선택 하 여 서버에 대 한 설정을 엽니다.
  
    ![탄력적 풀에 대 한 서버 열기](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. **설정** 창에서 **장애 조치 (failover) 그룹** 을 선택 하 고 섹션 2에서 만든 장애 조치 (failover) 그룹을 선택 합니다. 
  
   ![포털에서 장애 조치 (failover) 그룹을 선택 합니다.](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. 주 서버와 보조 서버를 검토 합니다. 
1. 작업 창에서 **장애 조치 (Failover)** 를 선택 하 여 탄력적 풀이 포함 된 장애 조치 그룹을 장애 조치 (failover) 합니다. 
1. TDS 세션의 연결을 끊을 것을 알리는 경고에서 **예** 를 선택 합니다. 

   ![SQL database를 포함 하는 장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. 주 서버가 보조 서버인 서버를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 서버에 교환 된 역할이 있어야 합니다. 
1. 장애 **조치 (** failover)를 다시 선택 하 여 장애 조치 그룹을 다시 원래 설정으로 장애 조치 합니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 장애 조치 그룹의 장애 조치를 테스트 합니다.

보조 복제본의 역할을 확인 합니다. 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

보조 서버로 장애 조치 (failover): 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

---

> [!IMPORTANT]
> 보조 데이터베이스를 삭제 해야 하는 경우 해당 데이터베이스를 삭제 하기 전에 장애 조치 (failover) 그룹에서 제거 합니다. 장애 조치 (failover) 그룹에서 보조 데이터베이스를 제거 하기 전에 삭제 하면 예기치 않은 동작이 발생할 수 있습니다. 

## <a name="managed-instance"></a>Managed Instance

Azure Portal 또는 PowerShell을 사용 하 여 두 개의 관리 되는 인스턴스 간에 장애 조치 (failover) 그룹을 만듭니다. 

[Express](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 경로를 구성 하거나 각 관리 되는 인스턴스의 가상 네트워크에 대 한 게이트웨이를 만들고 두 게이트웨이를 연결한 다음 장애 조치 (failover) 그룹을 만들어야 합니다. 

### <a name="prerequisites"></a>필수 조건
다음 필수 구성 요소를 고려 합니다.

- 보조 관리 되는 인스턴스는 비어 있어야 합니다.
- 보조 가상 네트워크의 서브넷 범위는 기본 가상 네트워크의 서브넷 범위와 겹치지 않아야 합니다. 
- 보조 인스턴스의 데이터 정렬 및 표준 시간대는 주 인스턴스의 데이터 정렬 및 표준 시간대와 일치 해야 합니다. 
- 두 게이트웨이를 연결 하는 경우 두 연결 모두에 대해 **공유 키** 가 동일 해야 합니다. 

### <a name="create-primary-virtual-network-gateway"></a>기본 가상 네트워크 게이트웨이 만들기 

[Express](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)경로를 구성 하지 않은 경우 Azure Portal 또는 PowerShell을 사용 하 여 기본 가상 네트워크 게이트웨이를 만들 수 있습니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 기본 가상 네트워크 게이트웨이를 만듭니다. 

1. [Azure Portal](https://portal.azure.com)에서 리소스 그룹으로 이동 하 여 기본 관리 되는 인스턴스에 대 한 **가상 네트워크** 리소스를 선택 합니다. 
1. **설정** 아래에서 **서브넷** 을 선택 하 고 새 **게이트웨이 서브넷**을 추가 하려면 선택 합니다. 기본값을 그대로 둡니다. 

   ![기본 관리 되는 인스턴스에 대 한 게이트웨이 추가](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. 서브넷 게이트웨이를 만든 후 왼쪽 탐색 창에서 **리소스 만들기** 를 선택 하 고 검색 상자에 `Virtual network gateway`을 입력 합니다. **Microsoft**에서 게시 한 **가상 네트워크 게이트웨이** 리소스를 선택 합니다. 

   ![새 가상 네트워크 게이트웨이 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. 필수 필드를 입력 하 여 게이트웨이를 기본 관리 되는 인스턴스로 구성 합니다. 

   다음 표에서는 기본 관리 되는 인스턴스의 게이트웨이에 필요한 값을 보여 줍니다.
 
    | **필드** | 값 |
    | --- | --- |
    | **구독** |  기본 관리 되는 인스턴스가 있는 구독입니다. |
    | **이름** | 가상 네트워크 게이트웨이의 이름입니다. | 
    | **지역** | 보조 관리 되는 인스턴스가 있는 지역입니다. |
    | **게이트웨이 유형** | **VPN**을 선택합니다. |
    | **VPN 유형** | **경로 기반** 선택 |
    | **SKU**| `VpnGw1`기본값을 그대로 둡니다. |
    | **위치**| 보조 관리 되는 인스턴스와 보조 가상 네트워크가 있는 위치입니다.   |
    | **가상 네트워크**| 보조 관리 되는 인스턴스의 가상 네트워크를 선택 합니다. |
    | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
    | **공용 IP 주소 이름**| IP 주소에 대 한 이름을 입력 합니다. |
    | &nbsp; | &nbsp; |

1. 다른 값은 기본값으로 두고 **검토 + 만들기** 를 선택 하 여 가상 네트워크 게이트웨이에 대 한 설정을 검토 합니다.

   ![기본 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. **만들기** 를 선택 하 여 새 가상 네트워크 게이트웨이를 만듭니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 기본 가상 네트워크 게이트웨이를 만듭니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   
   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location
   
   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
    
   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>보조 가상 네트워크 게이트웨이 만들기

Azure Portal 또는 PowerShell을 사용 하 여 보조 가상 네트워크 게이트웨이를 만듭니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
이전 섹션의 단계를 반복 하 여 보조 관리 되는 인스턴스에 대 한 가상 네트워크 서브넷 및 게이트웨이를 만듭니다. 보조 관리 되는 인스턴스에 대 한 게이트웨이를 구성 하는 데 필요한 필드를 입력 합니다. 

   다음 표에서는 보조 관리 되는 인스턴스의 게이트웨이에 필요한 값을 보여 줍니다.

   | **필드** | 값 |
   | --- | --- |
   | **구독** |  보조 관리 되는 인스턴스가 있는 구독입니다. |
   | **이름** | `secondary-mi-gateway`와 같은 가상 네트워크 게이트웨이의 이름입니다. | 
   | **지역** | 보조 관리 되는 인스턴스가 있는 지역입니다. |
   | **게이트웨이 유형** | **VPN**을 선택합니다. |
   | **VPN 유형** | **경로 기반** 선택 |
   | **SKU**| `VpnGw1`기본값을 그대로 둡니다. |
   | **위치**| 보조 관리 되는 인스턴스와 보조 가상 네트워크가 있는 위치입니다.   |
   | **가상 네트워크**| `vnet-sql-mi-secondary`와 같이 섹션 2에서 만든 가상 네트워크를 선택 합니다. |
   | **공용 IP 주소**| **새로 만들기**를 선택합니다. |
   | **공용 IP 주소 이름**| IP 주소 이름을 입력 합니다 (예: `secondary-gateway-IP`). |
   | &nbsp; | &nbsp; |

   ![보조 게이트웨이 설정](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 보조 가상 네트워크 게이트웨이를 만듭니다. 

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   
   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location
    
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
    
   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   
   $gw2
   ```

---


### <a name="connect-the-gateways"></a>게이트웨이 연결 
Azure Portal 또는 PowerShell을 사용 하 여 두 게이트웨이 간의 연결을 만듭니다. 

기본 게이트웨이에서 보조 게이트웨이로 연결 하 고 보조 게이트웨이에서 기본 게이트웨이로 연결 하 여 두 개의 연결을 만들어야 합니다. 

두 연결 모두에 사용 되는 공유 키는 각 연결에 대해 동일 해야 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)
Azure Portal를 사용 하 여 두 게이트웨이 간의 연결을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택 합니다.
1. 검색 상자에 `connection`를 입력 한 다음 enter 키를 눌러 Microsoft에서 게시 한 **연결** 리소스로 이동 합니다.
1. **만들기** 를 선택 하 여 연결을 만듭니다. 
1. **기본 사항** 탭에서 다음 값을 선택 하 고 **확인**을 선택 합니다. 
    1. **연결 형식**에 대 한 `VNet-to-VNet`를 선택 합니다. 
    1. 드롭다운에서 구독을 선택합니다. 
    1. 드롭다운에서 관리 되는 인스턴스의 리소스 그룹을 선택 합니다. 
    1. 드롭다운에서 기본 관리 되는 인스턴스의 위치를 선택 합니다. 
1. **설정** 탭에서 다음 값을 선택 하거나 입력 한 다음, **확인**을 선택 합니다.
    1. **첫 번째 가상 네트워크 게이트웨이**(예: `Primary-Gateway`)에 대 한 기본 네트워크 게이트웨이를 선택 합니다.  
    1. **두 번째 가상 네트워크 게이트웨이**(예: `Secondary-Gateway`)에 대 한 보조 네트워크 게이트웨이를 선택 합니다. 
    1. **양방향 연결 설정**옆의 확인란을 선택 합니다. 
    1. 기본 기본 연결 이름을 그대로 두거나 원하는 값으로 이름을 바꿉니다. 
    1. 연결에 대 한 **공유 키 (PSK)** 를 제공 합니다 (예: `mi1m2psk`). 

   ![게이트웨이 연결 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. **요약** 탭에서 양방향 연결에 대 한 설정을 검토 한 다음 **확인** 을 선택 하 여 연결을 만듭니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 두 게이트웨이 간의 연결을 만듭니다. 

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
    
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
    
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>장애 조치 (failover) 그룹 만들기 
Azure Portal 또는 PowerShell을 사용 하 여 관리 되는 인스턴스에 대해 장애 조치 (failover) 그룹을 만듭니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 관리 되는 인스턴스에 대해 장애 조치 (failover) 그룹을 만듭니다. 

1. [Azure Portal](https://portal.azure.com)의 왼쪽 메뉴에서 **Azure SQL**을 선택합니다. **AZURE sql** 이 목록에 없는 경우 **모든 서비스**를 선택한 다음 검색 상자에 azure sql을 입력 합니다. (선택 사항) **Azure SQL** 옆의 별표를 선택하여 즐겨찾기로 선택하고 왼쪽 탐색에 항목으로 추가합니다. 
1. 장애 조치 (failover) 그룹에 추가 하려는 기본 관리 되는 인스턴스를 선택 합니다.  
1. **설정**에서 **인스턴스 장애 조치 (failover) 그룹** 으로 이동한 다음 **그룹 추가** 를 선택 하 여 **인스턴스 장애 조치 (failover) 그룹** 페이지를 엽니다. 

   ![장애 조치 (failover) 그룹 추가](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **인스턴스 장애 조치 (Failover) 그룹** 페이지에서 장애 조치 (failover) 그룹의 이름을 입력 한 다음 드롭다운에서 보조 관리 되는 인스턴스를 선택 합니다. **만들기** 를 선택 하 여 장애 조치 (failover) 그룹을 만듭니다. 

   ![장애 조치 (failover) 그룹 만들기](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. 장애 조치 (failover) 그룹 배포가 완료 되 면 **장애 조치 (Failover) 그룹** 페이지로 돌아갑니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 관리 되는 인스턴스에 대해 장애 조치 (failover) 그룹을 만듭니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```
---

### <a name="test-failover"></a>테스트 장애 조치

Azure Portal 또는 PowerShell을 사용 하 여 장애 조치 (failover) 그룹의 장애 조치를 테스트 합니다. 

# <a name="portaltabazure-portal"></a>[포털](#tab/azure-portal)

Azure Portal를 사용 하 여 장애 조치 (failover) 그룹의 장애 조치를 테스트 합니다. 

1. [Azure Portal](https://portal.azure.com) 내에서 관리 되는 인스턴스로 이동 하 고 설정 아래에서 **인스턴스 장애 조치 (Failover) 그룹** 을 선택 합니다. 
1. 첫 번째 관리 되는 인스턴스를 검토 하 고 보조 복제본을 관리 하는 인스턴스를 검토 합니다. 
1. **장애 조치 (Failover)** 를 선택한 다음 연결을 끊을 TDS 세션 경고에 대해 **예** 를 선택 합니다. 

   ![장애 조치 (failover) 그룹 장애 조치 (failover)](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. 주 복제본이 관리 되 인스턴스를 검토 합니다. 장애 조치 (failover)에 성공 하면 두 인스턴스는 역할을 전환 해야 합니다. 

   ![장애 조치 (failover) 후 관리 되는 인스턴스의 역할이 전환 됨](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. **장애 조치 (Failover)** 를 다시 한 번 선택 하 여 주 인스턴스가 주 역할로 다시 장애 조치 (Failover) 합니다. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 장애 조치 그룹의 장애 조치를 테스트 합니다. 

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="locate-listener-endpoint"></a>수신기 엔드포인트 찾기

장애 조치 (failover) 그룹이 구성 되 면 응용 프로그램에 대 한 연결 문자열을 수신기 엔드포인트으로 업데이트 합니다. 이렇게 하면 기본 데이터베이스, 탄력적 풀 또는 관리 되는 인스턴스가 아닌 장애 조치 (failover) 그룹 수신기에 응용 프로그램이 연결 된 상태로 유지 됩니다. 이러한 방식으로 Azure SQL database 엔터티가 장애 조치 (failover) 될 때마다 연결 문자열을 수동으로 업데이트 하지 않아도 되 고, 현재 기본으로 있는 엔터티에 트래픽이 라우팅됩니다. 

수신기 엔드포인트은 `fog-name.database.windows.net`형식이 며 장애 조치 (failover) 그룹을 볼 때 Azure Portal에 표시 됩니다.

![장애 조치 (Failover) 그룹 연결 문자열](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>설명

- 단일 데이터베이스 또는 풀링된 데이터베이스에 대 한 장애 조치 (failover) 그룹을 제거 해도 복제는 중지 되지 않으며 복제 된 데이터베이스는 삭제 되지 않습니다. 단일 또는 풀링된 데이터베이스를 제거한 후 장애 조치 (failover) 그룹에 다시 추가 하려면 지역에서 복제를 수동으로 중지 하 고 보조 서버에서 데이터베이스를 삭제 해야 합니다. 이러한 작업을 수행 하지 못하면 장애 조치 (failover) 그룹에 데이터베이스를 추가 하려고 할 때 `The operation cannot be performed due to multiple errors`와 유사한 오류가 발생할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

장애 조치 (failover) 그룹을 구성 하는 자세한 단계는 다음 자습서를 참조 하세요.
- [단일 데이터베이스를 장애 조치 (failover) 그룹에 추가](sql-database-single-database-failover-group-tutorial.md)
- [장애 조치 (failover) 그룹에 탄력적 풀 추가](sql-database-elastic-pool-failover-group-tutorial.md)
- [장애 조치 (failover) 그룹에 관리 되는 인스턴스 추가](sql-database-managed-instance-failover-group-tutorial.md)
 
Azure SQL Database 고가용성 옵션에 대 한 개요는 지역에서 [복제](sql-database-active-geo-replication.md) 및 [자동 장애 조치 (failover) 그룹](sql-database-auto-failover-group.md)을 참조 하세요. 
