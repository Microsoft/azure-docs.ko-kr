---
title: Azure Functions에서 연결 관리
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 02/25/2018
ms.openlocfilehash: 7ce933511532fdb1bfb5189e5a900e87f3d83fa2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213967"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Functions에서 연결 관리

함수 앱의 함수는 리소스를 공유 합니다. 이러한 공유 리소스 중에는 연결: HTTP 연결, 데이터베이스 연결 및 서비스에 대 한 연결 (예: Azure Storage)이 있습니다. 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. 이 문서에서는 필요한 것 보다 더 많은 연결을 사용 하지 않도록 함수를 코딩 하는 방법을 설명 합니다.

## <a name="connection-limit"></a>연결 제한

함수 앱은 [샌드박스 환경](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)에서 실행 되므로 사용 가능한 연결 수는 부분적으로 제한 됩니다. 샌드박스에서 코드에 적용 하는 제한 사항 중 하나는 현재 인스턴스당 600 활성 (총 1200) 연결 인 아웃 바운드 연결 수에 대 한 제한입니다. 이 한도에 도달 하면 함수 런타임에서는 로그에 다음 메시지를 기록 합니다 `Host thresholds exceeded: Connections` . 자세한 내용은 [함수 서비스 제한](functions-scale.md#service-limits)을 참조 하세요.

이 제한은 인스턴스당입니다. [크기 조정 컨트롤러에서 함수 앱 인스턴스를 추가](functions-scale.md#how-the-consumption-and-premium-plans-work) 하 여 더 많은 요청을 처리 하면 각 인스턴스에 독립적인 연결 제한이 있습니다. 즉, 전역 연결 제한이 없으며 모든 활성 인스턴스에서 600 개가 넘는 활성 연결을 가질 수 있습니다.

문제를 해결 하려면 함수 앱에 대 한 Application Insights를 사용 하도록 설정 했는지 확인 합니다. Application Insights를 사용 하 여 실행 같은 함수 앱에 대 한 메트릭을 볼 수 있습니다. 자세한 내용은 [Application Insights에서 원격 분석 보기](functions-monitoring.md#view-telemetry-in-application-insights)를 참조 하세요.  

## <a name="static-clients"></a>정적 클라이언트

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. 함수를 작성할 수 있는 모든 언어에 대해 클라이언트 연결을 다시 사용 하는 것이 좋습니다. 예를 들어, 단일 정적 클라이언트를 사용 하는 경우 [Httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1), [documentclient](/dotnet/api/microsoft.azure.documents.client.documentclient)및 Azure Storage 클라이언트와 같은 .net 클라이언트에서 연결을 관리할 수 있습니다.

Azure Functions 응용 프로그램에서 서비스별 클라이언트를 사용 하는 경우 따라야 할 몇 가지 지침은 다음과 같습니다.

- 모든 함수 호출을 사용 하 여 새 클라이언트를 만들지 *마십시오* .
- 모든 함수 호출에서 사용할 수 있는 단일 정적 클라이언트 *를 만듭니다.*
- 다른 함수에서 동일한 서비스를 사용 하는 경우 공유 도우미 클래스에서 단일 정적 클라이언트를 만드는 *것이 좋습니다* .

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="httpclient-example-c"></a>HttpClient 예제(C#)

다음은 정적 [Httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) 인스턴스를 만드는 c # 함수 코드의 예제입니다.

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

.NET의 [Httpclient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) 에 대 한 일반적인 질문은 "클라이언트를 삭제 해야 하나요?"입니다. 일반적으로를 사용 하는 경우를 구현 하는 개체를 삭제 `IDisposable` 합니다. 그러나 정적 클라이언트는 함수가 종료 될 때 사용 하지 않으므로 삭제 하지 않습니다. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

### <a name="http-agent-examples-javascript"></a>HTTP 에이전트 예제 (JavaScript)

더 나은 연결 관리 옵션을 제공 하기 때문에 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 모듈 등의 네이티브가 아닌 메서드 대신 네이티브 클래스를 사용 해야 합니다 `node-fetch` . 연결 매개 변수는 클래스의 옵션을 통해 구성 됩니다 `http.agent` . HTTP 에이전트에서 사용할 수 있는 자세한 옵션은 [새 에이전트 ( \[ 옵션 \] )](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)를 참조 하세요.

에서 사용 하는 전역 `http.globalAgent` 클래스는 `http.request()` 이러한 값을 모두 해당 기본값으로 설정 합니다. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

```js
http.globalAgent.maxSockets = 200;
```

 다음 예제에서는 해당 요청에 대해서만 사용자 지정 HTTP 에이전트를 사용 하 여 새 HTTP 요청을 만듭니다.

```js
var http = require('http');
var httpAgent = new http.Agent();
httpAgent.maxSockets = 200;
options.agent = httpAgent;
http.request(options, onResponseCallback);
```

### <a name="documentclient-code-example-c"></a>DocumentClient 코드 예제(C#)

[DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)는 Azure Cosmos DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```cs
#r "Microsoft.Azure.Documents.Client"
using Microsoft.Azure.Documents.Client;

private static Lazy<DocumentClient> lazyClient = new Lazy<DocumentClient>(InitializeDocumentClient);
private static DocumentClient documentClient => lazyClient.Value;

private static DocumentClient InitializeDocumentClient()
{
    // Perform any initialization here
    var uri = new Uri("example");
    var authKey = "authKey";
    
    return new DocumentClient(uri, authKey);
}

public static async Task Run(string input)
{
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri("database", "collection");
    object document = new { Data = "example" };
    await documentClient.UpsertDocumentAsync(collectionUri, document);
    
    // Rest of function
}
```

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient 코드 예제 (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) 는 Azure Cosmos DB 인스턴스에 연결 됩니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const key = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, key });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { resources: itemArray } = await container.items.readAll().fetchAll();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 연결

함수 코드는 SQL Server에 대 한 .NET Framework Data Provider ([SqlClient](/dotnet/api/system.data.sqlclient?view=dotnet-plat-ext-3.1))를 사용 하 여 SQL 관계형 데이터베이스에 연결할 수 있습니다. [Entity Framework](/ef/ef6/)와 같이 ADO.NET를 사용 하는 데이터 프레임 워크의 기본 공급자 이기도 합니다. [HttpClient](/dotnet/api/system.net.http.httpclient?view=netcore-3.1) 및 [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. 그러나 계속 해 서 연결을 실행할 수 있기 때문에 데이터베이스에 대 한 연결을 최적화 해야 합니다. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> Entity Framework와 같은 일부 데이터 프레임 워크는 일반적으로 구성 파일의 **ConnectionStrings** 섹션에서 연결 문자열을 가져옵니다. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. 함수 코드에 [SqlConnection](/dotnet/api/system.data.sqlclient.sqlconnection?view=dotnet-plat-ext-3.1) 의 인스턴스를 만드는 경우 **응용 프로그램 설정** 에 연결 문자열 값을 다른 연결과 함께 저장 해야 합니다.

## <a name="next-steps"></a>다음 단계

정적 클라이언트를 권장 하는 이유에 대 한 자세한 내용은 [부적절 한 인스턴스화 방지 패턴](/azure/architecture/antipatterns/improper-instantiation/)을 참조 하세요.

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
