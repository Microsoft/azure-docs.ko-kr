---
title: 지리적으로 분산 된 솔루션 구현
description: 복제 된 데이터베이스에 대 한 장애 조치 (failover) 및 테스트 장애 조치를 위해 Azure SQL Database 및 클라이언트 응용 프로그램에서 데이터베이스를 구성 하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: bdc03d93654fdb0ae90600f8dad279f0fc8efbe9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070839"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>자습서: 지리적으로 분산 된 데이터베이스 (Azure SQL Database) 구현
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

원격 지역으로 장애 조치 (failover) 하도록 SQL Database 및 클라이언트 응용 프로그램에서 데이터베이스를 구성 하 고 장애 조치 (failover) 계획을 테스트 합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> - [장애 조치 (failover) 그룹](auto-failover-group-overview.md) 만들기
> - Java 응용 프로그램을 실행 하 여 SQL Database에서 데이터베이스 쿼리
> - 테스트 장애 조치

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager 모듈은 여전히 Azure SQL Database에서 지원되지만 향후의 모든 개발은 Az.Sql 모듈을 위한 것입니다. 이러한 cmdlet은 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)을 참조하세요. Az 모듈 및 AzureRm 모듈의 명령에 대한 인수는 실질적으로 동일합니다.

이 자습서를 완료하려면 다음 항목을 설치했는지 확인하세요.

- [Azure PowerShell](/powershell/azure/)
- Azure SQL Database의 단일 데이터베이스. 데이터베이스를 만들려면 다음 중 하나를 사용합니다.
  - [Azure 포털](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > 이 자습서에서는 *AdventureWorksLT* 샘플 데이터베이스를 사용합니다.

- Java 및 Maven. [SQL Server를 사용하여 앱 빌드](https://www.microsoft.com/sql-server/developer-get-started/)로 이동하여 **Java**를 강조 표시한 다음 사용 중인 환경을 선택하고 해당하는 단계를 진행합니다.

> [!IMPORTANT]
> 이 자습서의 단계를 수행하는 컴퓨터의 공용 IP 주소를 사용하도록 방화벽 규칙을 설정하세요. 데이터베이스 수준 방화벽 규칙은 보조 서버에 자동으로 복제됩니다.
>
> 자세한 내용은 [데이터베이스 수준 방화벽 규칙 만들기](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) 를 참조 하거나, 컴퓨터에 대 한 서버 수준 방화벽 규칙에 사용 되는 IP 주소를 확인 하려면 [서버 수준 방화벽 만들기](firewall-create-server-level-portal-quickstart.md)를 참조 하세요.  

## <a name="create-a-failover-group"></a>장애 조치 그룹 만들기

Azure PowerShell를 사용 하 여 기존 서버와 다른 지역의 새 서버 간에 [장애 조치 (failover) 그룹](auto-failover-group-overview.md) 을 만듭니다. 그런 다음 장애 조치(failover) 그룹에 샘플 데이터베이스를 추가합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

장애 조치(failover) 그룹을 만들려면 다음 스크립트를 실행합니다.

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> `az login`을 실행 하 여 Azure에 로그인 합니다.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

데이터베이스를 선택한 다음 **설정**  >  **지역에서 복제**를 선택 하 여 Azure Portal에서 지역에서 복제 설정을 변경할 수도 있습니다.

![지역에서 복제 설정](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>샘플 프로젝트 실행

1. 콘솔에서 다음 명령을 사용하여 Maven 프로젝트를 만듭니다.

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. **Y**를 입력하고 **Enter** 키를 누릅니다.

1. 디렉터리를 새 프로젝트로 변경합니다.

   ```bash
   cd SqlDbSample
   ```

1. 자주 사용 하는 편집기를 사용 하 여 프로젝트 폴더에서 *pom.xml* 파일을 엽니다.

1. 다음 `dependency` 섹션을 추가하여 SQL Server 종속성용 Microsoft JDBC Driver를 추가합니다. 더 큰 `dependencies` 섹션에 종속성을 붙여 넣어야 합니다.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. `dependencies` 섹션 뒤에 `properties` 섹션을 추가하여 Java 버전을 지정합니다.

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. `properties` 섹션 뒤에 `build` 섹션을 추가하여 매니페스트 파일을 지원합니다.

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. *pom.xml* 파일을 저장하고 닫습니다.

1. ..\SqlDbSample\src\main\java\com\sqldbsamples에 있는 *App.java* 파일을 열고 해당 내용을 다음 코드로 바꿉니다.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. *응용 프로그램. java* 파일을 저장 하 고 닫습니다.

1. 명령 콘솔에서 다음 명령을 실행합니다.

   ```bash
   mvn package
   ```

1. 애플리케이션을 시작합니다. 애플리케이션은 수동으로 중지할 때까지 1시간 정도 실행되므로 장애 조치(failover) 테스트를 실행할 수 있습니다.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>테스트 장애 조치

다음 스크립트를 실행하여 장애 조치(failover) 시뮬레이션을 진행한 다음 애플리케이션 결과를 관찰합니다. 데이터베이스 마이그레이션 중에 일부 삽입과 선택이 실패하는 방식을 잘 살펴봅니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

다음 명령을 사용 하 여 테스트 중에 재해 복구 서버의 역할을 확인할 수 있습니다.

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

장애 조치(failover)를 테스트하려면:

1. 장애 조치(failover) 그룹의 수동 장애 조치(failover)를 시작합니다.

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. 장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 명령을 사용 하 여 테스트 중에 재해 복구 서버의 역할을 확인할 수 있습니다.

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

장애 조치(failover)를 테스트하려면:

1. 장애 조치(failover) 그룹의 수동 장애 조치(failover)를 시작합니다.

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. 장애 조치(failover) 그룹을 주 서버로 되돌립니다.

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>다음 단계

이 자습서에서는 원격 지역으로 장애 조치 (failover) 하 고 장애 조치 (failover) 계획을 테스트 하는 응용 프로그램 및 Azure SQL Database의 데이터베이스를 구성 했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> - 지역에서 복제 장애 조치(failover) 그룹 만들기
> - Java 응용 프로그램을 실행 하 여 SQL Database에서 데이터베이스 쿼리
> - 테스트 장애 조치

Azure SQL Managed Instance의 인스턴스를 장애 조치 (failover) 그룹에 추가 하는 방법에 대 한 다음 자습서로 이동 합니다.

> [!div class="nextstepaction"]
> [장애 조치 (failover) 그룹에 Azure SQL Managed Instance 인스턴스 추가](../managed-instance/failover-group-add-instance-tutorial.md)
