---
title: Azure Cosmos DB SQL API 계정에서 지리 공간적 데이터 작업
description: Azure Cosmos DB 및 SQL API를 사용하여 공간 개체를 만들고 인덱싱 및 쿼리하는 방법을 이해합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: sngun
ms.openlocfilehash: 9c6ea982d9a605696dad0c943aa6dd2ae155d6bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60888672"
---
# <a name="use-geospatial-and-geojson-location-data-with-azure-cosmos-db-sql-api-account"></a>Azure Cosmos DB SQL API 계정에서 지리 공간 및 GeoJSON 위치 데이터 사용

이 문서에서는 Azure Cosmos DB의 지리 공간 기능을 소개합니다. 현재 지리 공간 데이터의 저장 및 액세스는 Cosmos DB SQL API 계정에서만 지원됩니다. 이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

* Azure Cosmos DB에 공간 데이터를 저장하려면 어떻게 해야 하나요?
* SQL 및 LINQ에서 Azure Cosmos DB의 지리 공간 데이터를 쿼리하려면 어떻게 해야 하나요?
* Azure Cosmos DB에서 공간 인덱싱을 사용하거나 사용하지 않도록 설정하려면 어떻게 해야 하나요?

이 문서에는 SQL API를 통해 공간 데이터를 사용하는 방법을 보여 줍니다. 코드 샘플은 이 [GitHub 프로젝트](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Geospatial/Program.cs)를 참조하세요.

## <a name="introduction-to-spatial-data"></a>공간 데이터 소개
공간 데이터는 공간에서 개체의 위치와 모양을 설명합니다. 대부분의 애플리케이션에서 이러한 데이터는 지구의 개체 및 지리 공간 데이터에 해당합니다. 공간 데이터를 사용하여 사람, 관심 있는 장소 또는 도시나 호수 경계의 위치를 나타낼 수 있습니다. 일반적인 사용 사례에는 종종 근접 쿼리(예: “내 현재 위치 근처의 모든 커피숍 찾기”)가 포함됩니다. 

### <a name="geojson"></a>GeoJSON
Azure Cosmos DB는 인덱싱 및 지리 공간 지점 데이터의 쿼리를 지원하고 [GeoJSON 사양](https://tools.ietf.org/html/rfc7946)을 사용하여 나타납니다. GeoJSON 데이터 구조는 항상 유효한 JSON 개체이므로 특수 도구나 라이브러리 없이 Azure Cosmos DB를 사용하여 저장 및 쿼리할 수 있습니다. Azure Cosmos DB SDK는 쉽게 공간 데이터로 작업할 수 있게 해주는 도우미 클래스와 메서드를 제공합니다. 

### <a name="points-linestrings-and-polygons"></a>Points, LineStrings 및 Polygons
**점** 은 공간 내의 단일 위치를 나타냅니다. 지리 공간 데이터에서 점은 식품점, 키오스크, 자동차 또는 도시의 주소일 수 있는 정확한 위치를 나타냅니다.  점은 좌표 쌍이나 경도 및 위도를 사용하여 GeoJSON(및 Azure Cosmos DB)에서 표시됩니다. 점에 대한 예제 JSON은 다음과 같습니다.

**Azure Cosmos DB의 점**

```json
{
    "type":"Point",
    "coordinates":[ 31.9, -4.8 ]
}
```

> [!NOTE]
> GeoJSON 사양은 경도를 먼저 지정하고 위도를 두 번째로 지정합니다. 다른 매핑 애플리케이션과 마찬가지로 경도와 위도는 각도이며 도 단위로 표시됩니다. 경도 값은 본초 자오선에서 측정되고 -180도와 180.0도 사이이고, 위도 값은 적도에서 측정되고 -90.0도와 90.0도 사이입니다. 
> 
> Azure Cosmos DB는 WGS-84 참조 시스템을 기준으로 좌표를 해석합니다. 좌표 참조 시스템에 대한 자세한 내용은 아래를 참조하세요.
> 
> 

위치 데이터를 포함하는 이 사용자 프로필 예제와 같이 Azure Cosmos DB 문서에 포함할 수 있습니다.

**위치가 Azure Cosmos DB에 저장되어 있는 프로필 사용**

```json
{
    "id":"cosmosdb-profile",
    "screen_name":"@CosmosDB",
    "city":"Redmond",
    "topics":[ "global", "distributed" ],
    "location":{
        "type":"Point",
        "coordinates":[ 31.9, -4.8 ]
    }
}
```

점 외에도 GeoJSON은 LineString 및 다각형을 지원합니다. **LineString** 은 공간의 둘 이상 점과 이러한 점을 연결하는 선 세그먼트를 나타냅니다. 지리 공간 데이터에서 LineStrings은 일반적으로 고속도로나 강을 나타내는 데 사용됩니다. **Polygon**은 닫힌 LineString을 형성하는 연결된 점의 경계입니다. 다각형은 일반적으로 호수와 같은 자연스러운 대형이나 구/군/시 및 시/도와 같은 정치적 관할지를 나타내는 데 사용됩니다. Azure Cosmos DB에서 다각형의 예는 다음과 같습니다. 

**GeoJSON의 다각형**

```json
{
    "type":"Polygon",
    "coordinates":[ [
        [ 31.8, -5 ],
        [ 31.8, -4.7 ],
        [ 32, -4.7 ],
        [ 32, -5 ],
        [ 31.8, -5 ]
    ] ]
}
```

> [!NOTE]
> GeoJSON 사양에서는 유효한 다각형이 되기 위해 마지막 좌표 쌍을 첫 번째 좌표 쌍과 동일하게 제공하여 닫힌 도형을 만들어야 합니다.
> 
> 다각형 내의 점을 시계 반대 방향 순서로 지정해야 합니다. 시계 방향 순서로 지정된 다각형은 내부 영역의 반전을 나타냅니다.
> 
> 

점, LineString 및 다각형 외에도 GeoJSON은 여러 지리 공간 위치를 그룹화하는 방법 및 임의 속성을 지리적 위치에 **기능**으로 연결하는 방법에 대한 표현을 지정합니다. 이러한 개체는 유효한 JSON이므로 모두 Azure Cosmos DB에 저장하고 처리할 수 있습니다. 그러나 Azure Cosmos DB는 지점의 자동 인덱싱만 지원합니다.

### <a name="coordinate-reference-systems"></a>좌표 참조 시스템
지구 모양이 불규칙적이므로 지리 공간 데이터의 좌표는 각각 고유한 참조 프레임과 측정 단위가 있는 많은 CRS(좌표 참조 시스템)에 표시됩니다. 예를 들어 "National Grid of Britain"은 영국에서만 정확하고 그 밖의 지역에서는 정확하지 않은 참조 시스템입니다. 

현재 가장 많이 사용되는 CRS는 World Geodetic System [WGS-84](http://earth-info.nga.mil/GandG/wgs84/)입니다. GPS 디바이스와 Google Map 및 Bing 지도 API를 비롯한 많은 매핑 서비스는 WGS-84를 사용합니다. Azure Cosmos DB는 WGS-84 CRS만 사용하여 지리 공간 데이터의 인덱싱 및 쿼리를 지원합니다. 

## <a name="creating-documents-with-spatial-data"></a>공간 데이터를 포함하는 문서 만들기
GeoJSON 값을 포함하는 문서를 만드는 경우, 컨테이너의 인덱싱 정책에 따라 공간 인덱스를 사용하여 자동으로 인덱싱됩니다. Python 또는 Node.js와 같은 동적으로 형식화된 언어로 Azure Cosmos DB SDK 작업을 수행하는 경우 유효한 GeoJSON을 만들어야 합니다.

**Node.js에서 지리 공간 데이터를 포함하는 문서 만들기**

```json
var userProfileDocument = {
    "name":"cosmosdb",
    "location":{
        "type":"Point",
        "coordinates":[ -122.12, 47.66 ]
    }
};

client.createDocument(`dbs/${databaseName}/colls/${collectionName}`, userProfileDocument, (err, created) => {
    // additional code within the callback
});
```

SQL API로 작업하는 경우 `Microsoft.Azure.Documents.Spatial` 네임스페이스 내에서 `Point` 및 `Polygon` 클래스를 사용하여 위치 정보를 애플리케이션 개체 내에 포함할 수 있습니다. 이러한 클래스는 GeoJSON으로 공간 데이터 직렬화 및 역직렬화를 간소화하는 데 도움이 됩니다.

**.NET에서 지리 공간 데이터를 포함하는 문서 만들기**

```json
using Microsoft.Azure.Documents.Spatial;

public class UserProfile
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("location")]
    public Point Location { get; set; }

    // More properties
}

await client.CreateDocumentAsync(
    UriFactory.CreateDocumentCollectionUri("db", "profiles"), 
    new UserProfile 
    { 
        Name = "cosmosdb", 
        Location = new Point (-122.12, 47.66) 
    });
```

위도 및 경도 정보가 없지만 실제 주소나 도시 또는 국가와 같은 위치 이름이 있는 경우 Bing 지도 REST 서비스와 같은 지오코딩 서비스를 사용하여 실제 좌표를 조회할 수 있습니다. [여기](https://msdn.microsoft.com/library/ff701713.aspx)서 Bing 지도 지오코딩에 대해 자세히 알아보세요.

## <a name="querying-spatial-types"></a>공간 형식 쿼리
지리 공간 데이터를 삽입하는 방법을 살펴보았으며, 이제 SQL 및 LINQ에서 Azure Cosmos DB를 사용하여 이 데이터를 쿼리하는 방법을 살펴보겠습니다.

### <a name="spatial-sql-built-in-functions"></a>공간 SQL 기본 제공 함수
Azure Cosmos DB는 지리 공간 쿼리를 위해 다음과 같은 OGC(Open Geospatial Consortium) 기본 제공 함수를 지원합니다. SQL 언어의 전체 기본 제공 함수 집합에 대한 자세한 내용은 [Azure Cosmos DB 쿼리](how-to-sql-query.md)를 참조하세요.

|**사용 현황**|**설명**|
|---|---|
| ST_DISTANCE(spatial_expr, spatial_expr) | 두 GeoJSON Point, Polygon 또는 LineString 식 사이의 거리를 반환합니다.|
|ST_WITHIN(spatial_expr, spatial_expr) | 첫 번째 GeoJSON 개체(Point, Polygon 또는 LineString)가 두 번째 GeoJSON 개체(Point, Polygon 또는 LineString) 내에 있는지를 나타내는 부울 식을 반환합니다.|
|ST_INTERSECTS(spatial_expr, spatial_expr)| 지정한 두 GeoJSON 개체(Point, Polygon 또는 LineString)가 교차하는지 여부를 나타내는 부울 식을 반환합니다.|
|ST_ISVALID| 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한지 여부를 나타내는 부울 값을 반환합니다.|
| ST_ISVALIDDETAILED| 지정된 GeoJSON Point, Polygon 또는 LineString 식이 유효한 경우 부울 값을 포함하는 JSON 값을 반환하고, 잘못된 경우 추가로 그 이유를 문자열 값으로 반환합니다.|

공간 함수를 사용하여 공간 데이터에 대한 근접 쿼리를 수행할 수 있습니다. 예를 들어 ST_DISTANCE 기본 제공 함수를 사용하여 지정된 위치에서 30km 이내에 있는 모든 제품군 문서를 반환하는 쿼리는 다음과 같습니다. 

**쿼리**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**결과**

    [{
      "id": "WakefieldFamily"
    }]

인덱싱 정책에 공간 인덱싱을 포함하면 "거리 쿼리"가 인덱스를 통해 효율적으로 처리됩니다. 공간 인덱싱에 대한 자세한 내용은 아래 섹션을 참조하세요. 지정된 경로에 대한 공간 인덱스가 없는 경우에도 값이 "true"로 설정된 `x-ms-documentdb-query-enable-scan` 요청 헤더를 지정하여 공간 쿼리를 수행할 수 있습니다. .NET에서는 **EnableScanInQuery** 가 true로 설정된 쿼리에 선택적 [FeedOptions](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) 인수를 전달하여 이 작업을 수행할 수 있습니다. 

ST_WITHIN을 사용하여 점이 다각형 내에 있는지 여부를 확인할 수 있습니다. 일반적으로 다각형은 우편 번호, 시/도 경계 또는 자연스러운 대형과 같은 경계를 나타내는 데 사용됩니다. 인덱싱 정책에 공간 인덱싱을 포함하면 "이내" 쿼리가 인덱스를 통해 효율적으로 처리됩니다. 

ST_WITHIN의 다각형 인수에는 단일 링만 포함될 수 있습니다. 즉, 다각형에 구멍이 포함되지 않아야 합니다. 

**쿼리**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**결과**

    [{
      "id": "WakefieldFamily",
    }]

> [!NOTE]
> Azure Cosmos DB 쿼리에서 일치하지 않는 형식이 작동하는 방식과 비슷하게, 인수에 지정된 위치 값이 잘못되었거나 형식이 잘못된 경우 **정의되지 않음**으로 평가되고 평가된 문서는 쿼리 결과에서 생략됩니다. 쿼리에서 결과가 반환되지 않는 경우 ST_ISVALIDDETAILED를 실행하여 공간 형식이 잘못된 이유를 디버그합니다.     
> 
> 

Azure Cosmos DB는 반전 쿼리 수행도 지원합니다. 즉, Azure Cosmos DB에서 다각형 또는 선을 인덱싱한 다음, 지정된 점이 포함된 영역을 쿼리할 수 있습니다. 이 패턴은 일반적으로 특정 시점(예: 지정된 영역에 트럭이 출입한 시점)을 식별하기 위해 물류에서 사용됩니다. 

**쿼리**

    SELECT * 
    FROM Areas a 
    WHERE ST_WITHIN({'type': 'Point', 'coordinates':[31.9, -4.8]}, a.location)


**결과**

    [{
      "id": "MyDesignatedLocation",
      "location": {
        "type":"Polygon", 
        "coordinates": [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
      }
    }]

ST_ISVALID 및 ST_ISVALIDDETAILED를 사용하여 공간 개체가 유효한지 확인할 수 있습니다. 예를 들어 다음 쿼리는 위도 값(-132.8)이 범위를 벗어난 점의 유효성을 검사합니다. ST_ISVALID는 부울 값만 반환하고 ST_ISVALIDDETAILED는 부울 및 잘못된 것으로 간주된 이유를 포함하는 문자열을 반환합니다.

**쿼리**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**결과**

    [{
      "$1": false
    }]

이러한 함수를 사용하여 다각형의 유효성을 검사할 수도 있습니다. 예를 들어 여기서는 ST_ISVALIDDETAILED를 사용하여 닫혀 있지 않은 다각형의 유효성을 검사합니다. 

**쿼리**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**결과**

    [{
       "$1": { 
            "valid": false, 
            "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a Polygon must have the same start and end points." 
          }
    }]

### <a name="linq-querying-in-the-net-sdk"></a>.NET SDK의 LINQ 쿼리
SQL .NET SDK는 LINQ 식에서 사용하기 위한 스텁 메서드 `Distance()` 및 `Within()`도 제공합니다. SQL LINQ 공급자는 이 메서드 호출을 동등한 SQL 기본 제공 함수 호출(각각 ST_DISTANCE 및 ST_WITHIN)로 변환합니다. 

LINQ를 사용하여 Azure Cosmos DB 컬렉션에서 “위치” 값이 지정된 점에서 30km 반지름 내에 있는 모든 문서를 찾는 LINQ 쿼리의 예는 다음과 같습니다.

**거리에 대한 LINQ 쿼리**

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(u => u.ProfileType == "Public" && a.Location.Distance(new Point(32.33, -4.66)) < 30000))
    {
        Console.WriteLine("\t" + user);
    }

마찬가지로, "위치"가 지정된 상자/다각형 내에 있는 모든 문서를 찾기 위한 쿼리는 다음과 같습니다. 

**이내에 대한 LINQ 쿼리**

    Polygon rectangularArea = new Polygon(
        new[]
        {
            new LinearRing(new [] {
                new Position(31.8, -5),
                new Position(32, -5),
                new Position(32, -4.7),
                new Position(31.8, -4.7),
                new Position(31.8, -5)
            })
        });

    foreach (UserProfile user in client.CreateDocumentQuery<UserProfile>(UriFactory.CreateDocumentCollectionUri("db", "profiles"))
        .Where(a => a.Location.Within(rectangularArea)))
    {
        Console.WriteLine("\t" + user);
    }


LINQ 및 SQL을 사용하여 문서를 쿼리하는 방법을 살펴보았으며, 이제 공간 인덱싱을 위해 Azure Cosmos DB를 구성하는 방법을 살펴보겠습니다.

## <a name="indexing"></a>인덱싱
[Azure Cosmos DB를 사용한 스키마 제약 없는 인덱싱](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) 에서 설명했듯이 Azure Cosmos DB의 데이터베이스 엔진은 스키마 제약 없이 JSON을 최고 수준으로 지원하도록 설계되었습니다. Azure Cosmos DB의 쓰기 최적화된 데이터베이스 엔진은 GeoJSON 표준으로 표시된 공간 데이터(점, 다각형 및 선)를 기본적으로 이해합니다.

간단히 말해, 기하 도형이 측지 좌표에서 2D 평면으로 프로젝션된 다음 **quadtree**를 사용하여 셀에 점진적으로 나뉩니다. 이러한 셀은 점의 위치를 유지하는 **힐버트 공간 채움 곡선** 내의 셀 위치에 따라 1D에 매핑됩니다. 또한 위치 데이터는 인덱싱될 때 **공간 분할**이라는 프로세스를 거칩니다. 즉, 위치와 교차하는 모든 셀이 식별되고 Azure Cosmos DB 인덱스에 키로 저장됩니다. 쿼리 시 점 및 다각형과 같은 인수도 관련 셀 ID 범위를 추출하기 위해 공간 분할된 다음 인덱스에서 데이터를 검색하는 데 사용됩니다.

/*(모든 경로)에 대한 공간 인덱스를 포함하는 인덱싱 정책을 지정하는 경우 효율적인 공간 쿼리(ST_WITHIN 및 ST_DISTANCE)를 위해 컬렉션 내에 있는 모든 점이 인덱싱됩니다. 공간 인덱스에는 전체 자릿수 값이 없으며 항상 기본 전체 자릿수 값을 사용합니다.

> [!NOTE]
> Azure Cosmos DB는 점, 다각형 및 LineStrings의 자동 인덱싱을 지원합니다.
> 
> 

다음 JSON 코드 조각은 공간 인덱싱이 사용되는 인덱싱 정책을 보여 줍니다. 즉, 공간 쿼리를 위해 문서 내에 있는 모든 GeoJSON 점을 인덱싱합니다. Azure Portal을 사용하여 인덱싱 정책을 수정하는 경우 인덱싱 정책에 대해 다음과 같은 JSON을 지정하여 컬렉션에서 공간 인덱싱을 사용하도록 설정할 수 있습니다.

**점 및 다각형에 대한 공간 인덱싱이 사용되는 컬렉션 인덱싱 정책 JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Range",
                   "dataType":"String",
                   "precision":-1
                },
                {
                   "kind":"Range",
                   "dataType":"Number",
                   "precision":-1
                },
                {
                   "kind":"Spatial",
                   "dataType":"Point"
                },
                {
                   "kind":"Spatial",
                   "dataType":"Polygon"
                }                
             ]
          }
       ],
       "excludedPaths":[
       ]
    }

점을 포함하는 모든 경로에 대해 공간 인덱싱이 설정된 컬렉션을 만드는 방법을 보여 주는 .NET 코드 조각은 다음과 같습니다. 

**공간 인덱싱을 사용하여 컬렉션 만들기**

    DocumentCollection spatialData = new DocumentCollection()
    spatialData.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point)); //override to turn spatial on by default
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), spatialData);

문서 내에 저장된 모든 점에 대해 공간 인덱싱을 활용하도록 기존 컬렉션을 수정하는 방법은 다음과 같습니다.

**공간 인덱싱을 사용하여 기존 컬렉션 수정**

    Console.WriteLine("Updating collection with spatial indexing enabled in indexing policy...");
    collection.IndexingPolicy = new IndexingPolicy(new SpatialIndex(DataType.Point));
    await client.ReplaceDocumentCollectionAsync(collection);

    Console.WriteLine("Waiting for indexing to complete...");
    long indexTransformationProgress = 0;
    while (indexTransformationProgress < 100)
    {
        ResourceResponse<DocumentCollection> response = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
        indexTransformationProgress = response.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromSeconds(1));
    }

> [!NOTE]
> 문서 내의 위치 GeoJSON 값이 잘못되었거나 형식이 잘못된 경우 공간 쿼리를 위해 인덱싱되지 않습니다. ST_ISVALID 및 ST_ISVALIDDETAILED를 사용하여 위치 값의 유효성을 검사할 수 있습니다.
> 
> 컬렉션 정의가 파티션 키를 포함하는 경우 인덱싱 변환 진행률은 보고되지 않습니다. 
> 
> 

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB에서 지리 공간 지원을 시작하는 방법을 배웠으므로 이제 다음 작업을 수행할 수 있습니다.

* [GitHub의 지리 공간 .NET 코드 샘플](https://github.com/Azure/azure-documentdb-dotnet/blob/fcf23d134fc5019397dcf7ab97d8d6456cd94820/samples/code-samples/Geospatial/Program.cs)을 사용하여 코딩 시작
* [Azure Cosmos DB 쿼리 실습](https://www.documentdb.com/sql/demo#geospatial)에서 지리 공간 쿼리 실습
* [Azure Cosmos DB 쿼리](how-to-sql-query.md)에 대해 알아보기
* [Azure Cosmos DB 인덱싱 정책에 대해 알아보기](index-policy.md)

