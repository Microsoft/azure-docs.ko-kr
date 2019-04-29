---
title: Azure Functions에서 연결 관리
description: 정적 연결 클라이언트를 사용하여 Azure Functions에서 성능 문제를 방지하는 방법을 알아봅니다.
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 02/25/2018
ms.author: glenga
ms.openlocfilehash: 4e9bd4e9ea467446c2814cdb8956a40b1503b027
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61020488"
---
# <a name="manage-connections-in-azure-functions"></a>Azure Functions에서 연결 관리

함수 앱에서 함수는 리소스를 공유합니다. 이러한 공유 리소스 연결 같습니다. HTTP 연결, 데이터베이스 연결 및 Azure Storage와 같은 서비스에 연결 합니다. 많은 함수가 동시에 실행되면 사용 가능한 연결이 부족해질 수 있습니다. 이 문서에서는 필요한 것 보다 더 많은 연결을 사용 하지 않도록 하는 함수를 코딩 하는 방법을 설명 합니다.

## <a name="connection-limit"></a>연결 제한

함수 앱에서 실행 되기 때문에 부분적으로 사용 가능한 연결 수는 제한 된 [샌드박스 환경](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)합니다. 샌드박스 코드에 적용 되는 제한 사항 중 하나를 [(현재에서 600 활성 연결 및 총 연결 수 1, 200) 연결 수에 대 한 제한은](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#numerical-sandbox-limits) 인스턴스당 합니다. 이 제한에 도달하면 함수 런타임에서 `Host thresholds exceeded: Connections` 메시지가 있는 로그를 만듭니다.

이 제한은 인스턴스당입니다.  경우는 [함수 앱 인스턴스를 추가 하는 크기 조정 컨트롤러](functions-scale.md#how-the-consumption-and-premium-plans-work) 더 많은 요청을 처리 하려면 각 인스턴스에 독립 연결 제한이 있습니다. 즉, 글로벌 연결 제한은 모든 활성 인스턴스에 걸쳐 600 개 이상 훨씬 활성 연결을 할 수 있습니다.

문제를 해결 하려면 함수 앱에 대 한 Application Insights를 설정 했는지 있는지 확인 합니다. Application Insights를 사용 하면 실행 등의 함수 앱에 대 한 메트릭을 볼 수 있습니다. 자세한 내용은 [Application Insights에서 원격 분석 보기](functions-monitoring.md#view-telemetry-in-application-insights)합니다.  

## <a name="static-clients"></a>정적 클라이언트

필요한 것보다 더 많은 연결을 유지하지 않으려면, 각 함수 호출을 사용하여 새 인스턴스를 만드는 대신 클라이언트 인스턴스를 다시 사용합니다. 함수에서 작성할 수 있는 모든 언어에 대 한 클라이언트 연결을 다시 사용 하는 것이 좋습니다. .NET 클라이언트와 같은 예를 들어를 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx), [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
), 정적 클라이언트를 사용 하는 경우 Azure Storage 클라이언트 연결을 관리할 수 있습니다.

Azure Functions 응용 프로그램에서 서비스별 클라이언트를 사용 하는 경우 따라야 할 몇 가지 지침은 다음과 같습니다.

- *그렇지 않은* 모든 함수 호출을 사용 하 여 새 클라이언트를 만듭니다.
- *수행할* 모든 함수 호출에서 사용할 수 있는 정적 클라이언트를 만듭니다.
- *고려* 다양 한 기능 같은 서비스를 사용 하는 경우 공유 도우미 클래스에서 정적 클라이언트 만들기.

## <a name="client-code-examples"></a>클라이언트 코드 예제

이 섹션에서는 함수 코드에서 클라이언트를 만들고 사용하기 위한 모범 사례를 보여줍니다.

### <a name="httpclient-example-c"></a>HttpClient 예제(C#)

예로 C# 함수를 만드는 정적 코드 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 인스턴스:

```cs
// Create a single, static HttpClient
private static HttpClient httpClient = new HttpClient();

public static async Task Run(string input)
{
    var response = await httpClient.GetAsync("https://example.com");
    // Rest of function
}
```

에 대 한 일반적인 질문 [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) .NET은 "I는 삭제 해야 클라이언트?" 구현 하는 개체의 삭제 하는 일반적으로 `IDisposable` 마친 경우 사용 합니다. 작업을 수행 하지 않으므로 정적 클라이언트의 삭제 되지 않지만 함수가 종료 될 때 사용 합니다. 정적 클라이언트가 애플리케이션 기간 동안 지속되도록 합니다.

### <a name="http-agent-examples-javascript"></a>HTTP 에이전트 예제 (JavaScript)

개선된 연결 관리 옵션을 제공하므로 비네이티브 메서드 대신 `node-fetch` 모듈과 같이 네이티브 [`http.agent`](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_class_http_agent) 클래스를 사용해야 합니다. 연결 매개 변수에서 옵션을 통해 구성 되는 `http.agent` 클래스입니다. HTTP 에이전트를 사용 하 여 사용할 수 있는 자세한 옵션을 참조 하세요 [새 에이전트 (\[옵션\])](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_new_agent_options)합니다.

전역 `http.globalAgent` 사용 되는 클래스 `http.request()` 가 이러한 모든 값을 해당 기본값으로 설정 합니다. 함수에서 연결 제한을 구성하는 방법은 최대 수를 전역적으로 설정하는 것이 좋습니다. 다음 예제에서는 함수 앱에 대한 소켓의 최대 수를 설정합니다.

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

[DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)는 Azure Cosmos DB 인스턴스에 연결합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](https://docs.microsoft.com/azure/cosmos-db/performance-tips#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

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

### <a name="cosmosclient-code-example-javascript"></a>CosmosClient 코드 예제에서는 (JavaScript)
[CosmosClient](/javascript/api/@azure/cosmos/cosmosclient) Azure Cosmos DB 인스턴스에 연결 합니다. Azure Cosmos DB 문서에서는 [애플리케이션 수명 동안 싱글톤 Azure Cosmos DB 클라이언트를 사용](../cosmos-db/performance-tips.md#sdk-usage)하도록 권장하고 있습니다. 다음 예제에서는 함수에서 이 작업을 수행하는 하나의 패턴을 보여 줍니다.

```javascript
const cosmos = require('@azure/cosmos');
const endpoint = process.env.COSMOS_API_URL;
const masterKey = process.env.COSMOS_API_KEY;
const { CosmosClient } = cosmos;

const client = new CosmosClient({ endpoint, auth: { masterKey } });
// All function invocations also reference the same database and container.
const container = client.database("MyDatabaseName").container("MyContainerName");

module.exports = async function (context) {
    const { result: itemArray } = await container.items.readAll().toArray();
    context.log(itemArray);
}
```

## <a name="sqlclient-connections"></a>SqlClient 연결

함수 코드는.NET Framework Data Provider를 사용 하 여 SQL Server에 대 한 수 있습니다 ([SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient(v=vs.110).aspx)) SQL 관계형 데이터베이스에 연결 합니다. 와 같은 ADO.NET을 사용 하는 데이터 프레임 워크에 대 한 기본 공급자 이기도 [Entity Framework](https://msdn.microsoft.com/library/aa937723(v=vs.113).aspx)합니다. [HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient(v=vs.110).aspx) 및 [DocumentClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient
)와 달리, ADO.NET은 기본적으로 연결 풀링을 구현합니다. 하지만 연결에서 계속 실행할 수 있습니다, 되므로 데이터베이스에 대 한 연결을 최적화 해야 합니다. 자세한 내용은 [SQL Server 연결 풀링(ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)을 참조하세요.

> [!TIP]
> Entity Framework와 같은 일부 데이터 프레임 워크는 일반적으로에서 연결 문자열 가져오기 합니다 **ConnectionStrings** 구성 파일의 섹션입니다. 이 경우, 함수 앱 설정의 **연결 문자열** 컬렉션과 로컬 프로젝트의 [local.settings.json 파일](functions-run-local.md#local-settings-file)에 SQL 데이터베이스 연결 문자열을 명시적으로 추가해야 합니다. 인스턴스를 만드는 경우 [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection(v=vs.110).aspx) 함수 코드에서 연결 문자열 값을 저장 해야 **응용 프로그램 설정** 다른 연결을 사용 하 여 합니다.

## <a name="next-steps"></a>다음 단계

정적 클라이언트 권장 하는 이유는 방법에 대 한 자세한 내용은 참조 하세요. [부적절 한 인스턴스화 안티패턴](https://docs.microsoft.com/azure/architecture/antipatterns/improper-instantiation/)합니다.

Azure Functions 성능 팁에 대한 자세한 내용은 [Azure 함수의 성능 및 안정성 최적화](functions-best-practices.md)를 참조하세요.
