---
title: Phoenix Query Server REST SDK - Azure HDInsight
description: Azure HDInsight에서 Phoenix Query Server를 위한 REST SDK를 설치 및 사용합니다.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: 1f468cac29579d8748f61a47b548a67d36ff8279
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123062"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/)는 [Apache HBase](apache-hbase-overview.md) 기반의 오픈 소스 대규모 병렬 관계형 데이터베이스 계층입니다. Phoenix를 사용하면 [SQLLine](apache-hbase-phoenix-squirrel-linux.md)과 같은 SSH 도구를 통해 HBase에서 SQL 유사 쿼리를 사용할 수 있습니다. 또한 Phoenix는 클라이언트 통신을 위한 2개의 전송 메커니즘인 JSON과 프로토콜 버퍼를 지원하는 씬 클라이언트인 PQS(Phoenix Query Server)라는 HTTP 서버를 제공합니다. 프로토콜 버퍼는 기본 메커니즘으로, JSON보다 좀 더 효율적인 통신을 제공합니다.

이 문서에서는 PQS REST SDK를 사용하여 테이블을 만들고, 행을 개별적으로 및 대량으로 Upsert하고, SQL 문을 사용하여 데이터를 선택하는 방법을 설명합니다. 예제에서는 [Apache Phoenix Query Server용 Microsoft .NET 드라이버](https://www.nuget.org/packages/Microsoft.Phoenix.Client)를 사용합니다. 이 SDK는 직렬화 형식에 대해 프로토콜 버퍼를 독점적으로 사용하는 [Apache Calcite Avatica](https://calcite.apache.org/avatica/) API에서 빌드되었습니다.

자세한 내용은 [Apache Calcite Avatica 프로토콜 버퍼 참조](https://calcite.apache.org/avatica/docs/protobuf_reference.html)를 참조하세요.

## <a name="install-the-sdk"></a>SDK 설치

Apache Phoenix Query Server용 Microsoft .NET 드라이버는 다음 명령으로 Visual Studio **NuGet 패키지 관리자 콘솔**에서 설치할 수 있는 NuGet 패키지로 제공됩니다.

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>새 PhoenixClient 개체 인스턴스화

라이브러리 사용을 시작하려면 `Uri`을 포함하는 `ClusterCredentials`를 클러스터와 클러스터의 Apache Hadoop 사용자 이름 및 암호로 전달해 새 `PhoenixClient` 개체를 인스턴스화합니다.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

CLUSTERNAME은 HDInsight HBase 클러스터 이름으로, 그리고 사용자 이름 및 암호는 클러스터 만들기에 지정된 Hadoop 자격 증명으로 바꿉니다. 기본 Hadoop 사용자 이름은 **admin**입니다.

## <a name="generate-unique-connection-identifier"></a>고유한 연결 식별자 생성

하나 이상의 요청을 PQS로 전송하려면 고유한 연결 식별자를 포함하여 요청을 해당 연결에 연결해야 합니다.

```csharp
string connId = Guid.NewGuid().ToString();
```

각 예제에서는 먼저 `OpenConnectionRequestAsync` 메서드를 호출하고 고유한 연결 식별자를 전달합니다. 다음으로 `ConnectionProperties` 및 `RequestOptions`를 정의하고 해당 개체와 생성된 연결 식별자를 `ConnectionSyncRequestAsync` 메서드에 전달합니다. PQS의 `ConnectionSyncRequest` 개체는 클라이언트와 서버 둘 다에서 데이터베이스 속성이 일관되게 표시되도록 도와줍니다.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest 및 해당 ConnectionProperties

`ConnectionSyncRequestAsync`를 호출하려면 `ConnectionProperties` 개체를 전달합니다.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

다음은 중요한 일부 속성입니다.

| 자산 | 설명 |
| -- | -- |
| AutoCommit | `autoCommit`이 Phoenix 트랜잭션에 사용되도록 설정되어 있는지 여부를 나타내는 부울입니다. |
| ReadOnly | 연결이 읽기 전용인지 여부를 나타내는 부울입니다. |
| TransactionIsolation | JDBC 사양에 따른 트랜잭션 격리 수준을 나타내는 정수입니다. 다음 표를 참조하세요.|
| 카탈로그 | 연결 속성을 가져올 때 사용할 카탈로그의 이름입니다. |
| 스키마 | 연결 속성을 가져올 때 사용할 스키마의 이름입니다. |
| IsDirty | 속성이 변경되었는지 여부를 나타내는 부울입니다. |

`TransactionIsolation` 값은 다음과 같습니다.

| 격리 값 | 설명 |
| -- | -- |
| 0 | 트랜잭션이 지원되지 않습니다. |
| 1 | 더티 읽기, 반복 불가능 읽기 및 가상 읽기가 발생할 수 있습니다. |
| 2 | 더티 읽기는 금지되지만 반복 불가능 읽기 및 가상 읽기는 발생할 수 있습니다. |
| 4 | 더티 읽기 및 반복 불가능 읽기는 금지되지만 가상 읽기는 발생할 수 있습니다. |
| 8 | 더티 읽기, 반복 불가능 읽기 및 가상 읽기가 모두 금지됩니다. |

## <a name="create-a-new-table"></a>새 테이블 만들기

다른 RDBMS와 마찬가지로 HBase는 데이터를 테이블에 저장합니다. Phoenix는 기본 키 및 열 형식을 정의하면서, 표준 SQL 쿼리를 사용하여 새 테이블을 만듭니다.

이 예제 및 모든 후속 예제는 [새 PhoenixClient 개체 인스턴스화](#instantiate-new-phoenixclient-object)에 정의된 대로 인스턴스화된 `PhoenixClient` 개체를 사용합니다.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

앞의 예제에서는 `IF NOT EXISTS` 옵션을 사용하여 `Customers`라는 새 테이블을 만듭니다. `CreateStatementRequestAsync` 호출은 Avitica(PQS) 서버에 새 문을 만듭니다. `finally` 블록은 반환된 `CreateStatementResponse` 및 `OpenConnectionResponse` 개체를 닫습니다.

## <a name="insert-data-individually"></a>데이터를 개별적으로 삽입

이 예제에서는 미국 주 및 지역 약어의 `List<string>` 컬렉션을 참조하여 개별 데이터 삽입을 보여 줍니다.

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

테이블의 `StateProvince` 열 값은 후속 선택 작업에서 사용됩니다.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

insert 문을 실행하기 위한 구조체는 새 테이블을 만드는 것과 비슷합니다. `try` 블록 끝에서 트랜잭션이 명시적으로 커밋됩니다. 이 예제에서는 insert 트랜잭션을 300번 반복합니다. 다음 예제에서는 좀 더 효율적인 일괄 처리 삽입 프로세스를 보여 줍니다.

## <a name="batch-insert-data"></a>데이터 삽입 일괄 처리

다음 코드는 데이터를 개별적으로 삽입하기 위한 코드와 거의 동일합니다. 이 예제에서는 준비된 문을 사용해서 `ExecuteRequestAsync`를 반복적으로 호출하지 않고, `ExecuteBatchRequestAsync` 호출에서 `UpdateBatch` 개체를 사용합니다.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

하나의 테스트 환경에서 300개의 새 레코드를 개별적으로 삽입하는 데 약 2분이 소요되었습니다. 반면, 300개 레코드를 일괄로 삽입하는 데는 6초만 필요했습니다.

## <a name="select-data"></a>데이터를 선택합니다.

이 예제에서는 하나의 연결을 다시 사용해서 여러 쿼리를 실행하는 방법을 보여 줍니다.

1. 모든 레코드를 선택한 다음, 기본 최대 개수인 100개가 반환된 후에 나머지 레코드를 가져옵니다.
2. 총 행 수 select 문을 사용하여 단일 스칼라 결과를 검색합니다.
3. 주 또는 지역별로 고객의 총 수를 반환하는 select 문을 실행합니다.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

`select` 문의 출력은 다음과 같은 결과를 나타냅니다.

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>다음 단계 

* [HDInsight의 Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
* [Apache HBase REST SDK 사용](apache-hbase-rest-sdk.md)
