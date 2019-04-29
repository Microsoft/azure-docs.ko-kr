---
title: Azure SQL Database를 사용한 데이터 종속 라우팅 | Microsoft Docs
description: Azure SQL Database의 분할된 데이터베이스 기능인 데이터 종속 라우팅을 위해 .NET 앱에서 ShardMapManager 클래스를 사용하는 방법
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: fe9098592fcfde2d5e23b78a3e33f2b4ebb9e2dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584967"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>데이터 의존 라우팅을 사용하여 적절한 데이터베이스로 쿼리 라우팅

**데이터 종속 라우팅** 은 쿼리에서 데이터를 사용하여 적절한 데이터베이스로 요청을 라우트하는 기능입니다. 데이터 종속 라우팅은 분할된 데이터베이스를 사용할 때의 기본 패턴입니다. 요청 컨텍스트는 특히 분할 키가 쿼리의 일부가 아닌 경우 요청을 라우트하는 데 사용될 수도 있습니다. 데이터 종속 라우팅을 사용하는 애플리케이션의 각 특정 쿼리 또는 트랜잭션은 요청당 하나의 데이터베이스에 액세스하는 것으로 제한됩니다. Azure SQL Database 탄력적 도구의 경우 이 라우팅은 **ShardMapManager**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) 클래스를 통해 수행됩니다.

애플리케이션에서는 다양한 연결 문자열 또는 분할된 환경의 여러 데이터 조각과 연결된 DB 위치를 추적하지 않아도 됩니다. 대신 [분할된 데이터베이스 맵 관리자](sql-database-elastic-scale-shard-map-management.md)에서 필요한 경우 분할된 데이터베이스 맵의 데이터 및 애플리케이션의 요청 대상인 분할 키의 값에 따라 올바른 데이터베이스에 연결을 엽니다. 이 키는 일반적으로 *customer_id*, *tenant_id*, *date_key* 또는 데이터베이스 요청의 기본 매개 변수인 별도의 특정 식별자입니다.

자세한 내용은 [Scaling Out SQL Server with Data Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx)(데이터 종속 라우팅을 사용하여 SQL Server 크기 조정)을 참조하세요.

## <a name="download-the-client-library"></a>클라이언트 라이브러리 다운로드

다운로드하려면:

* 라이브러리의 Java 버전: [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools) 참조
* 라이브러리의 .NET 버전: [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) 참조

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>데이터 종속 라우팅 애플리케이션에서 ShardMapManager 사용

애플리케이션에서 초기화 중에 **GetSQLShardMapManager**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)) 팩터리 호출을 사용하여 **ShardMapManager**를 인스턴스화해야 합니다. 이 예제에서는 **ShardMapManager** 및 포함하는 특정 **ShardMap**이 모두 초기화됩니다. 이 예제에서는 GetSqlShardMapManager 및 GetRangeShardMap([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))) 메서드를 보여 줍니다.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>가능한 가장 낮은 권한 자격 증명을 사용하여 분할된 데이터베이스 맵 가져오기

애플리케이션에서 분할된 데이터베이스 맵 자체를 조작하지 않는 경우 팩터리 메서드에서 사용되는 자격 증명은 **글로벌 분할된 데이터베이스 맵** 데이터베이스에서 읽기 전용 권한을 갖습니다. 이러한 자격 증명을 분할된 데이터베이스 맵 관리자에 대한 연결을 여는데 사용되는 자격 증명과는 일반적으로 다릅니다. 또는 [Elastic Database 클라이언트 라이브러리 액세스에 사용되는 자격 증명](sql-database-elastic-scale-manage-credentials.md)을 참조하세요.

## <a name="call-the-openconnectionforkey-method"></a>OpenConnectionForKey 메서드 호출

**ShardMap.OpenConnectionForKey 메서드**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey))는 **key** 매개 변수 값에 따라 적절한 데이터베이스에 명령을 실행할 수 있도록 준비된 연결을 반환합니다. 분할된 데이터베이스 정보는 **ShardMapManager**를 통해 애플리케이션에 캐시되므로, 이러한 요청 시에는 일반적으로 **전역 분할된 데이터베이스 맵**에 대한 데이터베이스 조회를 수행하지 않습니다.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* **key** 매개 변수는 요청에 대한 적합한 데이터베이스를 결정하는 분할된 데이터베이스 맵의 조회 키로 사용됩니다.
* **connectionString** 은 원하는 연결에 대한 사용자 자격 증명만 전달하는 데 사용됩니다. 메서드에서 **ShardMap**을 사용하여 데이터베이스와 서버를 결정하므로 이 *connectionString*에는 데이터베이스 이름 또는 서버 이름이 포함되지 않습니다.
* 분할된 데이터베이스 맵이 변경되고 분할 또는 병합 작업의 결과에 따라 다른 데이터베이스로 행이 이동할 수 있는 환경인 경우 **connectionOptions**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions))를 **ConnectionOptions.Validate**로 설정해야 합니다. 이 유효성 검사에는 연결이 애플리케이션에 전달되기 전 대상 데이터베이스의 로컬 분할된 데이터베이스 맵(글로벌하게 분할된 데이터베이스 맵이 아님)에 대한 간략한 쿼리가 포함됩니다.

로컬 분할된 데이터베이스 맵에 대한 유효성 검사가 실패하면(캐시가 올바르지 않음을 나타냄) 분할된 데이터베이스 맵 관리자에서 전역 분할된 데이터베이스 맵을 쿼리하여 올바른 새 값을 가져오고, 캐시를 업데이트하고, 적절한 데이터베이스 연결을 가져오고 반환합니다.

**ConnectionOptions.None**은 애플리케이션이 온라인 상태이며 분할된 데이터베이스 매핑 변경이 필요하지 않는 경우에만 사용합니다. 이 경우 캐시된 값은 항상 올바른 것으로 간주되며, 대상 데이터베이스에 대한 추가 왕복 유효성 검사 호출을 건너뛸 수 있습니다. 이렇게 하면 데이터베이스 트래픽이 감소합니다. **connectionOptions** 는 구성 파일의 값을 통해 특정 기간 동안 분할 변경이 필요한지 여부를 나타내도록 설정될 수 있습니다.  

이 예제에서는 **customerShardMap**이라는 **ShardMap** 개체를 사용하는 정수 키 **CustomerID** 값을 사용합니다.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

**OpenConnectionForKey** 메서드는 이미 열려 있는 새 연결을 올바른 데이터베이스에 반환합니다. 이러한 방식으로 활용되는 연결은 연결 풀링을 최대한 활용합니다.

애플리케이션에서 비동기 프로그래밍을 사용하는 경우 **OpenConnectionForKeyAsync 메서드**([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync))도 사용할 수 있습니다.

## <a name="integrating-with-transient-fault-handling"></a>일시적인 오류 처리와 통합

클라우드에서 데이터 액세스 애플리케이션을 개발할 경우 일시적인 오류는 앱을 통해 catch하고 오류를 throw하기 전에 작업이 여러 번 다시 시도되는지 확인하는 것이 가장 좋습니다. 클라우드 애플리케이션에 대한 일시적인 오류 처리는 일시적인 오류 처리([Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60)))에서 설명합니다.

일시적인 오류 처리는 데이터 종속 라우팅 패턴과 함께 자연스럽게 사용할 수 있습니다. 주요 요구 사항은 데이터 종속 라우팅 연결을 가져온 **using** 블록을 포함하여 전체 데이터 액세스 요청을 다시 시도하는 것입니다. 위의 예제는 다음과 같이 다시 작성할 수 있습니다.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>예제 - 일시적인 오류 처리를 사용하는 데이터 종속 라우팅

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() =&gt;
{
    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

일시적인 오류 처리를 구현하는 데 필요한 패키지는 탄력적 데이터베이스 샘플 애플리케이션을 빌드할 때 자동으로 다운로드됩니다.

## <a name="transactional-consistency"></a>트랜잭션 일관성

트랜잭션 속성은 분할된 데이터베이스에 로컬인 모든 작업에 대해 보장됩니다. 예를 들어 데이터 종속 라우팅을 통해 제출된 트랜잭션은 연결의 대상인 분할된 데이터베이스 범위 내에서 실행합니다. 이때 트랜잭션으로 여러 연결을 등록하기 위한 기능은 제공되지 않으므로 분할된 데이터베이스 간에 수행되는 작업에 대한 트랜잭션은 보장되지 않습니다.

## <a name="next-steps"></a>다음 단계

분할된 데이터베이스를 분리하거나 다시 연결하려면 [RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]