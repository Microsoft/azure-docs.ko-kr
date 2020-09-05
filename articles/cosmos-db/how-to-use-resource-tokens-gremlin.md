---
title: Gremlin SDK를 통해 Azure Cosmos DB 리소스 토큰 사용
description: 리소스 토큰을 만들어서 Graph 데이터베이스에 액세스하는 데 사용하는 방법을 알아봅니다.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/06/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bb2f948a49badf2578957b137d185c26607923b7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997203"
---
# <a name="use-azure-cosmos-db-resource-tokens-with-the-gremlin-sdk"></a>Gremlin SDK를 통해 Azure Cosmos DB 리소스 토큰 사용

이 문서에서는 [Azure Cosmos DB 리소스 토큰](secure-access-to-data.md)을 사용하여 Gremlin SDK를 통해 Graph 데이터베이스에 액세스하는 방법을 설명합니다.

## <a name="create-a-resource-token"></a>리소스 토큰 만들기

Apache TinkerPop Gremlin SDK에는 리소스 토큰을 만들기 위한 API가 없습니다. *리소스 토큰*이라는 용어는 Azure Cosmos DB 개념입니다. 리소스 토큰을 만들려면 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)를 다운로드합니다. 애플리케이션에서 리소스 토큰을 만들고 Graph 데이터베이스에 액세스할 때 리소스 토큰을 사용해야 하는 경우 두 개의 개별 SDK가 필요합니다.

다음 그림은 리소스 토큰의 기반이 되는 개체 모델 계층 구조를 간략하게 보여줍니다.

- **Azure Cosmos DB 계정** - DNS가 연결되어 있는 최상위 엔터티입니다(예: `contoso.gremlin.cosmos.azure.com`).
  - **Azure Cosmos DB 데이터베이스**
    - **사용자**
      - **사용 권한**
        - **토큰** - 허용되는 작업 또는 거부되는 작업을 나타내는 권한 개체 속성입니다.

리소스 토큰은 `"type=resource&ver=1&sig=<base64 string>;<base64 string>;"` 형식을 사용합니다. 이 문자열은 클라이언트에 표시되지 않으며, 수정하거나 해석하지 않고 그대로 사용해야 합니다.

```csharp
// Notice that document client is created against .NET SDK endpoint, rather than Gremlin.
DocumentClient client = new DocumentClient(
  new Uri("https://contoso.documents.azure.com:443/"), 
  "<master key>", 
  new ConnectionPolicy 
  {
    EnableEndpointDiscovery = false, 
    ConnectionMode = ConnectionMode.Direct 
  });

  // Read specific permission to obtain a token.
  // The token isn't returned during the ReadPermissionReedAsync() call.
  // The call succeeds only if database id, user id, and permission id already exist. 
  // Note that <database id> is not a database name. It is a base64 string that represents the database identifier, for example "KalVAA==".
  // Similar comment applies to <user id> and <permission id>.
  Permission permission = await client.ReadPermissionAsync(UriFactory.CreatePermissionUri("<database id>", "<user id>", "<permission id>"));

  Console.WriteLine("Obtained token {0}", permission.Token);
}
```

## <a name="use-a-resource-token"></a>리소스 토큰 사용
GremlinServer 클래스를 생성할 때 리소스 토큰을 "password" 속성으로 직접 사용할 수 있습니다.

```csharp
// The Gremlin application needs to be given a resource token. It can't discover the token on its own.
// You can obtain the token for a given permission by using the Azure Cosmos DB SDK, or you can pass it into the application as a command line argument or configuration value.
string resourceToken = GetResourceToken();

// Configure the Gremlin server to use a resource token rather than a master key.
GremlinServer server = new GremlinServer(
  "contoso.gremlin.cosmosdb.azure.com",
  port: 443,
  enableSsl: true,
  username: "/dbs/<database name>/colls/<collection name>",

  // The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
  password: resourceToken);

  using (GremlinClient gremlinClient = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
  {
      await gremlinClient.SubmitAsync("g.V().limit(1)");
  }
```

동일한 접근 방식이 모든 TinkerPop Gremlin SDK에 적용됩니다.

```java
Cluster.Builder builder = Cluster.build();

AuthProperties authenticationProperties = new AuthProperties();
authenticationProperties.with(AuthProperties.Property.USERNAME,
    String.format("/dbs/%s/colls/%s", "<database name>", "<collection name>"));

// The format of the token is "type=resource&ver=1&sig=<base64 string>;<base64 string>;".
authenticationProperties.with(AuthProperties.Property.PASSWORD, resourceToken);

builder.authProperties(authenticationProperties);
```

## <a name="limit"></a>제한

Gremlin 계정 하나로 토큰을 개수 제한 없이 발급할 수 있습니다. 그러나 1시간 내에 최대 100개 토큰을 동시에 사용할 수 있습니다. 응용 프로그램이 시간당 토큰 한도를 초과 하면 인증 요청이 거부 되 고 다음과 같은 오류 메시지가 표시 됩니다. "동시에 사용할 수 있는 100의 허용 된 리소스 토큰 제한을 초과 했습니다." 특정 토큰을 사용하는 활성 연결을 닫아 새 토큰에 사용할 슬롯을 확보할 수 없습니다. Azure Cosmos DB Gremlin 데이터베이스 엔진은 인증 요청 직전의 시간 동안 고유한 토큰을 추적합니다.

## <a name="permission"></a>사용 권한

애플리케이션에서 리소스 토큰을 사용하는 동안 발생하는 일반적인 오류는 "해당 요청에 대한 권한 부여 헤더에 제공된 권한이 부족합니다. 다른 인증 헤더를 사용하여 다시 시도하세요" 오류입니다. 이 오류는 Gremlin 통과에서 에지 또는 꼭짓점을 쓰려고 하는데 리소스 토큰이 *읽기* 권한만 부여하는 경우에 반환됩니다. 통과를 검사하여 *.addV()*, *.addE()*, *.drop()* 또는 *.property()* 단계 중 하나를 포함하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB의 [azure 역할 기반 access control (AZURE RBAC)](role-based-access-control.md)
* Azure Cosmos DB에서 [데이터 액세스를 보호하는 방법 알아보기](secure-access-to-data.md)
