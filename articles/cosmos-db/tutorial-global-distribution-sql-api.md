---
title: '자습서: SQL API의 Azure Cosmos DB 글로벌 배포 자습서'
description: '자습서: .NET, Java, Python 및 기타 다양한 SDK와 함께 SQL API를 사용하여 Azure Cosmos DB 글로벌 배포를 설정하는 방법 알아보기'
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 11/05/2019
ms.reviewer: sngun
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: dbfb90abcf301cb22a84ba28359c6cb0bfaacfd6
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021088"
---
# <a name="tutorial-set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>자습서: SQL API를 사용하여 Azure Cosmos DB 전역 배포 설정

이 문서에서는 Azure Portal을 사용하여 Azure Cosmos DB 글로벌 배포를 설정한 다음, SQL API를 사용하여 연결하는 방법을 보여 줍니다.

이 문서에서 다루는 작업은 다음과 같습니다. 

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * [SQL API](sql-api-introduction.md)를 사용하여 전역 배포 구성

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a><a id="preferred-locations"></a> SQL API를 사용하여 기본 설정 지역에 연결

[전역 배포](distribute-data-globally.md)를 활용하기 위해 클라이언트 애플리케이션은 문서 작업을 수행하는 데 사용할 정렬된 기본 지역 목록을 지정할 수 있습니다. SQL SDK에서 Azure Cosmos DB 계정 구성, 현재 지역 가용성 및 지정된 기본 설정 목록을 기반으로 하여 쓰기 및 읽기 작업을 수행하는 데 가장 적합한 엔드포인트를 선택합니다.

이 기본 설정 목록은 SQL SDK를 사용하여 연결을 초기화할 때 지정됩니다. SDK는 Azure 지역의 정렬된 목록인 선택적 매개 변수 `PreferredLocations`를 수락합니다.

SDK는 현재 쓰기 지역에 모든 쓰기를 자동 전송합니다. 모든 읽기는 기본 설정 위치 목록에서 사용 가능한 첫 번째 지역으로 전송됩니다. 요청이 실패하면 클라이언트는 목록을 다음 지역으로 옮겨갑니다.

SDK는 기본 설정 위치에 지정된 지역에서만 읽기를 시도합니다. 따라서 예를 들어 Azure Cosmos 계정이 4개 지역에서 사용될 수 있지만 클라이언트가 `PreferredLocations` 내에서 2개의 읽기(쓰기 아님) 지역만 지정한 경우, `PreferredLocations`에 지정되지 않은 읽기 지역에서 읽기를 제공하지 않습니다. `PreferredLocations` 목록에 지정된 읽기 영역을 사용할 수 없는 경우 읽기는 쓰기 영역 외부로 처리됩니다.

애플리케이션은 SDK 버전 1.8 이상에서 사용 가능한 두 가지 속성(`WriteEndpoint` 및 `ReadEndpoint`)을 확인하여 SDK가 선택한 현재의 쓰기 엔드포인트와 읽기 엔드포인트를 확인할 수 있습니다. `PreferredLocations` 속성이 설정되지 않으면 모든 요청이 현재 쓰기 지역에서 제공됩니다.

기본 설정 위치를 지정하지 않고 `setCurrentLocation` 메서드를 사용하는 경우 SDK는 클라이언트가 실행 중인 현재 지역에 따라 기본 설정 위치를 자동으로 채웁니다. SDK는 현재 지역에 대한 지역의 근접성에 따라 지역을 정렬합니다.

## <a name="net-sdk"></a>.NET SDK

SDK는 코드 변경 없이 사용할 수 있습니다. 이 경우 SDK는 읽기와 쓰기를 현재 쓰기 하위 지역에 자동으로 가져옵니다.

.NET SDK의 1.8 버전 이상에서 DocumentClient 생성자의 ConnectionPolicy 매개 변수는 Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations라는 속성이 있습니다. 이 속성은 컬렉션 `<string>` 형식이며 지역 이름 목록을 포함합니다. 문자열 값은 [Azure 지역][regions] 페이지의 지역 이름 열에 따라 서식이 지정되고 첫 글자와 마지막 글자 앞/뒤에 공백이 없습니다.

현재 읽기 및 쓰기 엔드포인트는 각각 DocumentClient.WriteEndpoint와 DocumentClient.ReadEndpoint에서 이용할 수 있습니다.

> [!NOTE]
> 엔드포인트의 URL은 수명이 긴 상수로 간주하지 말아야 합니다. 서비스는 언제든지 이 URL을 업데이트할 수 있습니다. SDK가 이런 변경 내용을 자동으로 처리합니다.
>

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

.NET V2 SDK를 사용하는 경우 `PreferredLocations` 속성을 사용하여 기본 설정 지역을 설정합니다.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

또는 `SetCurrentLocation` 속성을 사용하고 SDK에서 근접성에 따라 기본 설정 위치를 선택하도록 할 수 있습니다.

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

connectionPolicy.SetCurrentLocation("West US 2"); /

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

.NET V3 SDK를 사용하는 경우 `ApplicationPreferredRegions` 속성을 사용하여 기본 설정 지역을 설정합니다.

```csharp

CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
options.ApplicationPreferredRegions = new List<string> {Regions.WestUS, Regions.WestUS2};

CosmosClient client = new CosmosClient(connectionString, options);

```

또는 `ApplicationRegion` 속성을 사용하고 SDK에서 근접성에 따라 기본 설정 위치를 선택하도록 할 수 있습니다.

```csharp
CosmosClientOptions options = new CosmosClientOptions();
options.ApplicationName = "MyApp";
// If the application is running in West US
options.ApplicationRegion = Regions.WestUS;

CosmosClient client = new CosmosClient(connectionString, options);
```

---

## <a name="nodejsjavascript"></a>Node.js/JavaScript

> [!NOTE]
> 엔드포인트의 URL은 수명이 긴 상수로 간주하지 말아야 합니다. 서비스는 언제든지 이 URL을 업데이트할 수 있습니다. SDK가 이런 변경 내용을 자동으로 처리합니다.
>
>

다음은 Node.js/Javascript의 코드 예제입니다.

```JavaScript
// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
const preferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
const client = new CosmosClient{ endpoint, key, connectionPolicy: { preferredLocations } });
```

## <a name="python-sdk"></a>Python SDK

다음 코드는 Python SDK를 사용하여 기본 위치를 설정하는 방법을 보여줍니다.

```python
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v4-sdk"></a><a id="java4-preferred-locations"></a> Java V4 SDK

다음 코드는 Java SDK를 사용하여 기본 위치를 설정하는 방법을 보여줍니다.

# <a name="async"></a>[Async](#tab/api-async)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Async API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TutorialGlobalDistributionPreferredLocationAsync)]

# <a name="sync"></a>[동기화](#tab/api-sync)

   [Java SDK V4](sql-api-sdk-java-v4.md)(Maven [com.azure::azure-cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=TutorialGlobalDistributionPreferredLocationSync)]

--- 

## <a name="rest"></a>REST (영문)

데이터베이스 계정을 여러 지역에서 사용할 수 있게 되면 클라이언트는 이 URI `https://{databaseaccount}.documents.azure.com/`에서 GET 요청을 수행하여 가용성을 쿼리할 수 있습니다.

서비스에서 복제본에 대한 지역 목록과 해당 Azure Cosmos DB 엔드포인트 URI를 반환합니다. 현재 쓰기 지역이 응답에 표시됩니다. 클라이언트는 다음과 같이 모든 추가 REST API 요청에 알맞은 엔드포인트를 선택할 수 있습니다.

예제 응답

```json
{
    "_dbs": "//dbs/",
    "media": "//media/",
    "writableLocations": [
        {
            "Name": "West US",
            "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
        }
    ],
    "readableLocations": [
        {
            "Name": "East US",
            "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
        }
    ],
    "MaxMediaStorageUsageInMB": 2048,
    "MediaStorageUsageInMB": 0,
    "ConsistencyPolicy": {
        "defaultConsistencyLevel": "Session",
        "maxStalenessPrefix": 100,
        "maxIntervalInSeconds": 5
    },
    "addresses": "//addresses/",
    "id": "globaldbexample",
    "_rid": "globaldbexample.documents.azure.com",
    "_self": "",
    "_ts": 0,
    "_etag": null
}
```

* 모든 PUT, POST 및 DELETE 요청은 표시된 쓰기 URI로 이동해야 합니다.
* 모든 GET 및 다른 읽기 전용 요청(예: 쿼리)은 클라이언트가 선택한 엔드포인트로 이동할 수 있습니다.

읽기 전용 지역에 대한 쓰기 요청은 HTTP 오류 코드 403(“사용 권한 없음”)과 함께 실패합니다.

클라이언트의 최초 검색 단계 이후에 쓰기 지역이 변경되면 나중에 이전 쓰기 지역에 쓰려고 하면 HTTP 오류 코드 403("사용 권한 없음")과 함께 실패합니다. 클라이언트는 업데이트된 쓰기 지역을 가져오려면 지역 목록을 다시 가져와야 합니다.

이것으로 끝이며, 이 자습서를 완료했습니다! [Azure Cosmos DB의 일관성 수준](consistency-levels.md)을 참조하여 전역적으로 복제한 계정의 일관성을 관리하는 방법에 대해 알아볼 수 있습니다. 그리고 Azure Cosmos DB에서 전역 데이터베이스 복제가 작동하는 방법에 대한 자세한 내용은 [Azure Cosmos DB를 사용하여 전역적으로 데이터 배포](distribute-data-globally.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음을 수행했습니다.

> [!div class="checklist"]
> * Azure Portal을 사용하여 전역 배포 구성
> * SQL API를 사용하여 전역 배포 구성

이제 다음 자습서로 진행하여 Azure Cosmos DB 로컬 에뮬레이터를 사용하여 로컬로 개발하는 방법에 대해 자세히 알아볼 수 있습니다.

> [!div class="nextstepaction"]
> [에뮬레이터를 사용하여 로컬로 개발](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

