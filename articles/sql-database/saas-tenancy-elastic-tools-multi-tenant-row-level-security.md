---
title: RLS 및 탄력적 데이터베이스 도구를 사용한 다중 테넌트 앱 | Microsoft Docs
description: 행 수준 보안으로 탄력적 데이터베이스 도구를 사용하여 확장성이 높은 데이터 계층으로 애플리케이션을 빌드합니다.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 71d2d542d71977f9d8dfe07370dffd7fe508bc92
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485489"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 애플리케이션

[탄력적 데이터베이스 도구](sql-database-elastic-scale-get-started.md) 및 [RLS(행 수준 보안)][rls]는 Azure SQL Database를 사용하여 다중 테넌트 애플리케이션의 데이터 계층을 확장할 수 있도록 지원합니다. 이러한 기술을 함께 사용하면 확장성이 뛰어난 데이터 계층이 있는 애플리케이션을 작성할 수 있습니다. 데이터 계층은 다중 테넌트 분할된 데이터베이스를 지원하며 **ADO.NET SqlClient** 또는 **Entity Framework**를 사용합니다. 자세한 내용은 [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션의 설계 패턴](saas-tenancy-app-design-patterns.md)을 참조하세요.

- **탄력적 데이터베이스 도구**를 사용하면 개발자는 .NET 라이브러리 및 Azure 서비스 템플릿을 사용하여 표준 분할 방법을 통해 데이터 계층을 확장할 수 있습니다. [Elastic Database 클라이언트 라이브러리][s-d-elastic-database-client-library]를 사용하여 분할된 데이터베이스를 관리하면 일반적으로 분할과 관련된 여러 인프라 작업을 자동화 및 간소화하는 데 도움이 됩니다.
- **행 수준 보안**을 사용하면 개발자가 동일한 데이터베이스에 여러 테넌트에 대한 데이터를 안전하게 저장할 수 있습니다. RLS 보안 정책은 쿼리를 실행하는 테넌트에 속하지 않는 행을 필터링합니다. 데이터베이스 내부에 필터 논리를 중앙화하면 유지 관리가 단순해지고 보안 오류 위험이 줄어듭니다. 모든 클라이언트 코드에 의존하여 보안을 강제 적용하는 다른 대안은 위험합니다.

이러한 기능을 함께 사용하면 애플리케이션이 같은 분할된 데이터베이스에 여러 테넌트에 대한 데이터를 저장할 수 있습니다. 그러면 테넌트가 데이터베이스를 공유하는 경우 테넌트당 비용이 적게 듭니다. 또한 동일한 애플리케이션이 계속 해당 프리미엄 테넌트에 고유한 전용 단일 테넌트 분할된 데이터베이스에 대한 지불 옵션을 제공할 수 있습니다. 단일 테넌트 격리의 이점 중 하나는 더욱 견고한 성능을 보장한다는 것입니다. 단일 테넌트 데이터베이스에는 리소스에 대해 경쟁할 다른 테넌트가 없습니다.

목표는 탄력적 데이터베이스 클라이언트 라이브러리 [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) API를 사용하여 지정된 각 테넌트를 올바른 분할된 데이터베이스에 자동으로 연결하는 것입니다. 하나의 분할된 데이터베이스에만 지정된 테넌트에 대한 특정 TenantId 값이 포함됩니다. TenantId는 *분할 키*입니다. 연결이 설정되면 데이터베이스 내의 RLS 보안 정책이 지정된 테넌트가 해당 TenantId를 포함하는 데이터 행만 액세스할 수 있는지 확인합니다.

> [!NOTE]
> 테넌트 식별자에는 열이 두 개 이상 포함될 수 있습니다. 편의를 위해 이 설명에서는 비공식적으로 단일 열 TenantId를 가정합니다.

![앱 아키텍처 블로그][1]

## <a name="download-the-sample-project"></a>샘플 프로젝트 다운로드

### <a name="prerequisites"></a>필수 조건

- Visual Studio 2012 이상 사용
- Azure SQL 데이터베이스 3개 만들기
- 샘플 프로젝트 다운로드: [Azure SQL을 위한 탄력적 DB 도구 - 다중 테넌트 분할된 데이터베이스](https://go.microsoft.com/?linkid=9888163)
  - **Program.cs**

이 프로젝트는 [Azure SQL을 위한 탄력적 DB 도구 - Entity Framework 통합](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) 에서 설명한 프로젝트에 다중 테넌트 분할된 데이터베이스에 대한 지원을 추가하는 확장 프로젝트입니다. 이 프로젝트는 블로그 및 게시물을 만들기 위한 간단한 콘솔 애플리케이션을 작성합니다. 프로젝트에는 4명의 테넌트와 두 개의 다중 테넌트 분할 데이터베이스가 포함됩니다. 이 구성은 이전 다이어그램에 설명되어 있습니다.

애플리케이션을 빌드 및 실행합니다. 그러면 탄력적 데이터베이스 도구의 분할된 데이터베이스 맵 관리자가 부트스트랩을 실행하고 다음 테스트를 수행합니다.

1. Entity Framework 및 LINQ를 사용하여 새 블로그를 만든 후 각 테넌트에 대한 모든 블로그 표시
2. ADO.NET SqlClient를 사용하여 특정 테넌트에 대한 모든 블로그 표시
3. 잘못된 테넌트에 대한 블로그 삽입을 시도하여 오류가 throw되는지 확인

분할된 데이터베이스에서 아직 RLS를 설정하지 않았기 때문에 각 테스트에서 다음 문제가 발생합니다. 테넌트가 자신에게 속하지 않은 블로그를 볼 수 있으며 애플리케이션에서 잘못된 테넌트에 대한 블로그를 삽입할 수 있습니다. 이 문서의 나머지 부분에서는 RLS로 테넌트를 강제 격리하여 이러한 문제를 해결하는 방법을 설명합니다. 두 단계가 있습니다.

1. **애플리케이션 계층**: 연결을 연 후 애플리케이션 코드를 수정하여 SESSION\_CONTEXT에서 현재 TenantId를 항상 설정합니다. 샘플 프로젝트에서는 이미 TenantId를 이 방법으로 설정합니다.
2. **데이터 계층**: 각 분할된 데이터베이스에서 SESSION\_CONTEXT에 저장된 TenantId에 따라 행을 필터링하는 RLS 보안 정책을 만듭니다. 분할된 데이터베이스 각각에 대해 정책을 만듭니다. 그렇지 않으면 다중 테넌트 분할된 데이터베이스의 행이 필터링되지 않습니다.

## <a name="1-application-tier-set-tenantid-in-the-sessioncontext"></a>1. 애플리케이션 계층: SESSION\_CONTEXT에서 TenantId 설정

먼저 탄력적 데이터베이스 클라이언트 라이브러리의 데이터 종속 라우팅 API를 사용하여 분할된 데이터베이스에 연결합니다. 애플리케이션은 연결을 사용 중인 TenantId를 계속 데이터베이스에 알려야 합니다. TenantId는 어떤 행을 다른 테넌트에 속하는 것으로 필터링해야 하는지 RLS 보안 정책에 알려줍니다. 연결의 [SESSION\_CONTEXT](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql)에 현재 TenantId를 저장합니다.

SESSION\_CONTEXT에 대한 대안은 [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql)를 사용하는 것입니다. 하지만 SESSION\_CONTEXT가 더 나은 옵션입니다. SESSION\_CONTEXT는 사용하기 쉬우며 기본적으로 NULL을 반환하고 키-값 쌍을 지원합니다.

### <a name="entity-framework"></a>Entity Framework

Entity Framework를 사용하는 애플리케이션의 경우 가장 간단한 방법은 [EF DbContext를 사용하는 데이터 종속 라우팅](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext)에 설명된 ElasticScaleContext 재정의 내에서 SESSION\_CONTEXT를 설정하는 것입니다. SESSION\_CONTEX에서 TenantId를 연결에 대해 지정된 shardingKey로 설정하는 SqlCommand를 만들어서 실행합니다. 그러면 데이터 종속 라우팅을 통해 조정된 연결이 반환됩니다. 이 방법을 사용하면 SESSION\_CONTEXT를 설정하는 코드를 한 번만 작성하면 됩니다.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

이제 ElasticScaleContext가 호출될 때마다 SESSION\_CONTEXT는 자동으로 지정된 TenantId로 설정됩니다.

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

ADO.NET SqlClient를 사용하는 애플리케이션에 대해 ShardMap.OpenConnectionForKey 메서드를 기준으로 래퍼 함수를 만듭니다. 연결을 반환하기 전에 래퍼는 SESSION\_CONTEXT의 TenantId를 현재 TenantId로 자동으로 설정합니다. SESSION\_CONTEXT가 항상 설정되도록 하기 위해 이 래퍼 함수를 사용해서만 연결을 열어야 합니다.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. 데이터 계층: 행 수준 보안 정책 만들기

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>각 테넌트가 액세스할 수 있는 행을 필터링하는 보안 정책 만들기

애플리케이션에서 SESSION\_CONTEXT를 현재 TenantId로 설정한 후 쿼리하므로 RLS 보안 정책에서 쿼리를 필터링하고 TenantId가 다른 행을 제외할 수 있습니다.

RLS는 Transact-SQL에서 구현됩니다. 사용자 정의 함수에서 액세스 논리를 정의하고, 보안 정책에서 이 함수를 모든 테이블에 바인딩합니다. 이 프로젝트의 경우:

1. 이 함수는 애플리케이션이 데이터베이스에 연결되었는지, SESSION\_CONTEXT에 저장된 'TenantId'가 지정된 행의 TenantId와 일치하는지 확인합니다.
    - 애플리케이션은 다른 SQL 사용자 대신 연결됩니다.

2. FILTER 조건자를 사용하면 SELECT, UPDATE 및 DELETE 쿼리에 대해 TenantId 필터와 일치하는 행을 제외시킬 수 있습니다.
    - BLOCK 조건자는 행에서 필터가 INSERT 또는 UPDATE에 실패하는 것을 방지합니다.
    - SESSION\_CONTEXT가 설정되지 않은 경우 함수는 NULL을 반환하며 행이 표시되지 않거나 삽입할 수 없습니다.

모든 분할 영역에서 RLS를 사용하려면 Visual Studio(SSDT), SSMS 또는 프로젝트에 포함된 PowerShell 스크립트를 사용하여 다음 T-SQL을 실행합니다. 또는 [Elastic Database 작업](sql-database-elastic-jobs-overview.md)을 사용 중인 경우 모든 분할 영역에서 이 T-SQL의 실행을 자동화할 수 있습니다.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> 복잡한 프로젝트에서 수백 개의 테이블에 조건자를 추가하는 것은 지루한 작업이 될 수 있습니다. 자동으로 보안 정책을 생성하고 스키마의 모든 테이블에는 조건자를 추가하는 도우미 저장 프로시저가 있습니다. 자세한 내용은 [모든 테이블에 행 수준 보안 적용 – 도우미 스크립트(블로그)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)에서 블로그 게시물을 참조하세요.

이제 샘플 애플리케이션을 다시 실행하면 각 테넌트는 자신에게 속한 행만 보게 됩니다. 또한 애플리케이션에서는 현재 분할된 데이터베이스에 연결된 테넌트 이외의 다른 테넌트에 속한 행을 삽입할 수 없습니다. 또한 앱은 표시되는 행의 TenantId를 업데이트할 수 없습니다. 앱에서 두 작업을 시도하면 DbUpdateException이 발생합니다.

나중에 새 테이블을 추가하는 경우 보안 정책을 변경하고 새 테이블에 FILTER 및 BLOCK 조건자를 추가합니다.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>삽입 시 TenantId를 자동으로 채우도록 기본 제약 조건 추가

행을 삽입할 때 자동으로 SESSION\_CONTEXT에 현재 저장된 값으로 TenantId를 채우도록 각 테이블에 기본 제약 조건을 추가할 수 있습니다. 예제는 다음과 같습니다.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

이제 행을 삽입할 때 애플리케이션에서 TenantId를 지정할 필요가 없습니다.

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Entity Framework 프로젝트에 기본 제약 조건을 사용하는 경우 EF 데이터 모델에 TenantId 열을 포함하지 *않는* 것이 좋습니다. 이 권장 사항은 Entity Framework 쿼리에서 자동으로 기본값을 제공하는데, 이 기본값은 T-SQL에서 만든 SESSION\_CONTEXT를 사용하는 기본 제약 조건을 재정의하기 때문입니다.
> 예를 들어 샘플 프로젝트에서 기본 제약 조건을 사용하려면 DataClasses.cs에서 TenantId를 제거하고(그리고 패키지 관리자 콘솔에서 Add-Migration을 실행하고) 데이터베이스 테이블에만 필드가 있도록 T-SQL을 사용해야 합니다. 그러면 데이터 삽입 시 EF에서 잘못된 기본값을 자동으로 제공합니다.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(선택 사항) 모든 행에 액세스 하려면 *superuser*를 사용 하도록 설정

일부 애플리케이션은 모든 행에 액세스할 수 있는 *superuser*를 만들어야 할 수 있습니다. superuser는 모든 분할된 데이터베이스의 모든 테넌트에서 보고를 사용할 수 있습니다. 또는 superuser가 데이터베이스 간에 테넌트 행 이동과 관련된 분할된 데이터베이스에서 분할-병합 작업을 수행할 수 있습니다.

superuser를 사용하려면 각 분할 데이터베이스에서 새 SQL 사용자(이 예에서 `superuser`)를 만듭니다. 그런 다음 사용자를 모든 행에 액세스할 수 있도록 하는 새 조건자 함수를 사용하여 보안 정책을 변경합니다. 해당 함수는 다음과 같이 제공됩니다.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>유지 관리

- **새로운 분할된 데이터베이스 추가**: T-SQL 스크립트를 실행하여 모든 새로운 분할된 데이터베이스에서 RLS를 설정합니다. 그렇지 않으면 분할된 데이터베이스에 대한 쿼리가 필터링되지 않습니다.
- **새 테이블 추가**: 새 테이블을 만들 때마다 모든 분할된 데이터베이스의 보안 정책에 FILTER 및 BLOCK 조건자를 추가합니다. 그렇지 않으면 새 테이블에 대한 쿼리가 필터링되지 않습니다. [새로 만든 테이블에 자동으로 행 수준 보안 적용(블로그)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx)에 설명된 것처럼 DDL 트리거를 사용하여 이 추가 작업을 자동화할 수 있습니다.

## <a name="summary"></a>요약

탄력적 데이터베이스 도구와 행 수준 보안을 함께 사용하면 다중 테넌트 및 단일 테넌트 분할된 데이터베이스를 모두 지원하여 애플리케이션의 데이터 계층을 확장할 수 있습니다. 다중 테넌트 분할된 데이터베이스는 데이터를 보다 효율적으로 저장하는 데 사용할 수 있습니다. 이러한 효율성은 많은 수의 테넌트가 몇 개의 데이터 행만 가진 경우 분명합니다. 단일 테넌트의 분할된 데이터베이스는 보다 엄격한 성능 및 격리 요구 사항을 갖는 프리미엄 테넌트를 지원할 수 있습니다. 자세한 내용은 [행 수준 보안 참조][rls]를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure 탄력적 풀이란?](sql-database-elastic-pool.md)
- [Azure SQL Database를 사용하여 확장](sql-database-elastic-scale-introduction.md)
- [Azure SQL Database를 사용한 다중 테넌트 SaaS 애플리케이션 디자인 패턴](saas-tenancy-app-design-patterns.md)
- [Azure AD 및 OpenID Connect를 사용하여 다중 테넌트 앱에서 인증](../guidance/guidance-multitenant-identity-authenticate.md)
- [Tailspin 설문 조사 애플리케이션](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>질문 및 기능 요청

궁금한 사항이 있는 경우 [SQL Database 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)에 문의하세요. 또한 [SQL Database 피드백 포럼](https://feedback.azure.com/forums/217321-sql-database/)에서 기능 요청을 추가하세요.

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md
