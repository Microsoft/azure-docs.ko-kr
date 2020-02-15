---
title: Azure Cosmos DB SQL API에서로 마이그레이션
description: Azure Cosmos DB SQL API에서로 마이그레이션하기 위한 단계별 지침
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.openlocfilehash: 9713d963978e34ad874dc032676a6e1f14e4657c
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210945"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>Azure Cosmos DB SQL API에서로 마이그레이션

Azure Cosmos DB는 확장 가능 하 고 전역적으로 분산 되 고 완전히 관리 되는 데이터베이스입니다. 데이터에 대 한 대기 시간이 짧은 액세스를 보장 합니다. Azure Cosmos DB에 대 한 자세한 내용은 [개요](introduction.md) 문서를 참조 하세요. 이 문서에서는 Azure Cosmos DB의 SQL API 계정에 연결 된 Java 응용 프로그램을 마이그레이션하는 방법에 대 한 지침을 제공 합니다.

## <a name="differences-in-nomenclature"></a>명명법의 차이점

다음은 Azure Cosmos DB에서 서로 다르게 작동 하는 주요 기능입니다.

|   Couchbase     |   Azure Cosmos DB   |
| ---------------|-------------------|
|가는 기본 서버| 계정       |
|버킷           | 데이터베이스      |
|버킷           | 컨테이너/컬렉션 |
|JSON 문서    | 항목/문서 |

## <a name="key-differences"></a>주요 차이점

* Azure Cosmos DB에는 문서 내에 "ID" 필드가 있고,이 경우에는이 ID가 버킷 일부로 포함 됩니다. "ID" 필드는 파티션 전체에서 고유 합니다.

* 분할 또는 분할 기술을 사용 하 여 크기를 조정 Azure Cosmos DB 합니다. 즉, 데이터를 여러 분할/파티션으로 분할 합니다. 이러한 파티션/분할는 사용자가 제공 하는 파티션 키 속성을 기반으로 생성 됩니다. 파티션 키를 선택 하 여 읽기 및 쓰기 작업 또는 읽기/쓰기를 최적화 하 여 최적화할 수 있습니다. 자세히 알아보려면 [분할](./partition-data.md) 문서를 참조 하세요.

* 컬렉션 이름이 이미 존재 하기 때문에 Azure Cosmos DB 최상위 계층 구조에서 컬렉션을 나타내는 데 필요 하지 않습니다. 이 기능을 사용 하면 JSON 구조가 훨씬 간단해 집니다. 다음은 데이터 모델에서가는와 Azure Cosmos DB 간의 차이점을 보여 주는 예제입니다.

   어 **Chbase**: 문서 ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: 아래와 같이 문서 내에서 "ID"를 참조 합니다.

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK 지원

Azure Cosmos DB에는 다른 Java 프레임 워크를 지 원하는 다음과 같은 Sdk가 있습니다.

* 비동기 SDK
* 스프링 부팅 SDK

다음 섹션에서는 이러한 각 Sdk를 사용 하는 경우를 설명 합니다. 3 가지 유형의 워크 로드가 있는 예를 살펴보겠습니다.

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>& 스프링 데이터 기반 사용자 지정 쿼리를 문서 리포지토리로 사용 합니다.

마이그레이션하려는 워크 로드가 스프링 부팅 기반 SDK를 기반으로 하는 경우 다음 단계를 사용할 수 있습니다.

1. POM .xml 파일에 부모를 추가 합니다.

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. POM .xml 파일에 속성을 추가 합니다.

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. POM .xml 파일에 종속성을 추가 합니다.

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 리소스에서 응용 프로그램 속성을 추가 하 고 다음을 지정 합니다. URL, 키 및 데이터베이스 이름 매개 변수를 바꾸어야 합니다.

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 모델에서 컬렉션의 이름을 정의 합니다. 추가 주석을 지정할 수도 있습니다. 명시적으로 표시 하는 ID, 파티션 키 등을 예로 들 수 있습니다.

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

다음은 CRUD 작업에 대 한 코드 조각입니다.

### <a name="insert-and-update-operations"></a>삽입 및 업데이트 작업

여기서 *_repo* 는 리포지토리의 개체이 고 *DOC* 는 pojo 클래스의 개체입니다. `.save`를 사용 하 여 지정 된 ID의 문서가 있는 경우 삽입 하거나 upsert 수 있습니다. 다음 코드 조각에서는 doc 개체를 삽입 하거나 업데이트 하는 방법을 보여 줍니다.

```_repo.save(doc);```

### <a name="delete-operation"></a>삭제 작업

개체를 찾아서 삭제 하려면 doc 개체가 ID 및 파티션 키를 가져야 하는 다음 코드 조각을 고려 하십시오.

```_repo.delete(doc);```

### <a name="read-operation"></a>읽기 작업

파티션 키를 지정 하거나 지정 하지 않고 문서를 읽을 수 있습니다. 파티션 키를 지정 하지 않으면 파티션 키가 파티션 간 쿼리로 처리 됩니다. 다음 코드 샘플을 고려 합니다. 먼저 ID 및 파티션 키 필드를 사용 하 여 작업을 수행 합니다. 두 번째 예에서는 파티션 키 필드를 지정 하지 않고 일반 필드 &를 사용 합니다.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

이제 Azure Cosmos DB에서 응용 프로그램을 사용할 수 있습니다. 이 문서에 설명 된 예제의 전체 코드 샘플은 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/SpringCosmos) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>N1QL 쿼리를 사용 하 & 문서 리포지토리로 사용 되는 문서 리포지토리입니다.

N1QL 쿼리는 여는 Chbase에서 쿼리를 정의 하는 방법입니다.

|N1QL 쿼리 | Azure CosmosDB 쿼리|
|-------------------|-------------------|
|META (`TravelDocument`) id로 id, `TravelDocument`. * FROM `TravelDocument`를 선택 합니다. 여기서 `_type` = "부탄" 및 country = ' 인도 '이 고 Visas의 모든 m이 m을 충족 합니다. type = = ' 다중 항목 ' 및 m. Country IN [' 인도 ', '] ORDER BY ` Validity` DESC 제한 25 오프셋 0   | C를 선택 합니다. c의 c 조인 m에서 c. country = ' 인도 ' 인 c. country = ' 인도 ' 및 부탄 = ' _type 인도 ' 및 m. type = ' Multi ' 및 m. Country IN (' 인도 ', ' ') ORDER BY c. 유효 DESC 오프셋 0 제한 25 |

N1QL 쿼리에서 다음과 같이 변경 된 내용을 확인할 수 있습니다.

* META 키워드를 사용 하거나 첫 번째 수준 문서를 참조할 필요는 없습니다. 대신, 컨테이너에 대 한 고유한 참조를 만들 수 있습니다. 이 예제에서는 "c"로 간주 합니다 (모든 것이 될 수 있음). 이 참조는 모든 첫 번째 수준 필드의 접두사로 사용 됩니다. Fr-fr 예, c.id, c., country 등

* 이제 "ANY" 대신 하위 문서에 대 한 조인을 수행 하 고 "m"과 같은 전용 별칭으로 참조할 수 있습니다. 하위 문서에 대 한 별칭을 만든 후에는 별칭을 사용 해야 합니다. 예: m. Country.

* 오프셋의 시퀀스는 Azure Cosmos DB 쿼리에서 다르며, 먼저 OFFSET then LIMIT를 지정 해야 합니다. 쿼리를 Azure Cosmos DB에 전달 하는 동안 클라이언트 쪽에서 불필요 한 오버 헤드가 발생할 수 있으므로 사용자 지정 정의 된 최대 쿼리를 사용 하는 경우에는 스프링 데이터 SDK를 사용 하지 않는 것이 좋습니다. 대신이 경우에 훨씬 효율적으로 활용할 수 있는 직접 비동기 Java SDK가 있습니다.

### <a name="read-operation"></a>읽기 작업

다음 단계를 수행 하 여 Async Java SDK를 사용 합니다.

1. POM .xml 파일에 대해 다음 종속성을 구성 합니다.

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 다음 예제와 같이 `ConnectionBuilder` 메서드를 사용 하 여 Azure Cosmos DB에 대 한 연결 개체를 만듭니다. 다음 코드를 한 번만 실행 하려면이 선언을 bean에 배치 해야 합니다.

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();   
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 쿼리를 실행 하려면 다음 코드 조각을 실행 해야 합니다.

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

이제 위 방법의 도움을 사용 하 여 여러 쿼리를 전달 하 고 필요 없이 실행할 수 있습니다. 여러 쿼리로 분할 될 수 있는 하나의 대량 쿼리를 실행 해야 하는 경우에는 이전 코드 조각 대신 다음 코드 조각을 시도 합니다.

```java
for(SqlQuerySpec query:queries)
{
    objFlux= container.queryItems(query, fo);
    objFlux .publishOn(Schedulers.elastic())
            .subscribe(feedResponse->
                {
                    if(feedResponse.results().size()>0)
                    {
                        _docs.addAll(feedResponse.results());
                    }
                
                },
                Throwable::printStackTrace,latch::countDown);
    lstFlux.add(objFlux);
}
                        
        Flux.merge(lstFlux);
        latch.await();
}
```

이전 코드를 사용 하 여 쿼리를 병렬로 실행 하 고 최적화를 위해 분산 실행을 늘릴 수 있습니다. 또한 삽입 및 업데이트 작업을 실행할 수 있습니다.

### <a name="insert-operation"></a>삽입 작업

문서를 삽입 하려면 다음 코드를 실행 합니다.

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 Mono를 구독 합니다.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();              
```

### <a name="upsert-operation"></a>Upsert 작업

Upsert 작업을 수행 하려면 업데이트 해야 하는 문서를 지정 해야 합니다. 전체 문서를 페치 하려면 머리글 읽기 작업 아래에 언급 된 코드 조각을 사용 하 고 필요한 필드를 수정할 수 있습니다. 다음 코드 조각은 문서를 upsert 합니다.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 mono를 구독 합니다. 삽입 작업에서 mono 구독 코드 조각을 참조 하세요.

### <a name="delete-operation"></a>삭제 작업

다음 코드 조각은 삭제 작업을 수행 합니다.

```java     
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 mono를 구독 합니다. 삽입 작업에서 mono 구독 코드 조각을 참조 하세요. 전체 코드 샘플은 [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncInSpring) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-keyvalue-pair"></a>키/값 쌍으로 된

이는 쿼리 대신 조회를 수행할 수 있는 간단한 작업 유형입니다. 키/값 쌍에는 다음 단계를 사용 합니다.

1. "/ID"를 기본 키로 사용 하는 것이 좋습니다. 이렇게 하면 특정 파티션에서 직접 조회 작업을 수행할 수 있습니다. 컬렉션을 만들고 "/ID"를 파티션 키로 지정 합니다.

1. 인덱싱을 완전히 해제 합니다. 조회 작업을 실행할 것 이므로 인덱싱 오버 헤드를 전달 하는 점이 없습니다. 인덱싱을 끄려면 Azure Portal에 로그인 하 여 Azure Cosmos DB 계정으로 이동 합니다. **데이터 탐색기**열고 **데이터베이스** 및 **컨테이너**를 선택 합니다. **크기 조정 & 설정** 탭을 열고 **인덱싱 정책을**선택 합니다. 현재 인덱싱 정책은 다음과 같습니다.
    
   ```json
   {
       "indexingMode": "consistent",
       "includedPaths": 
       [
           {
            "path": "/*",
            "indexes": 
             [
                {
                  "kind": "Range",
                  "dataType": "Number"
                },
                {
                  "kind": "Range",
                  "dataType": "String"
                },
                {
                   "kind": "Spatial",
                   "dataType": "Point"
                }
             ]
          }
       ],
       "excludedPaths": 
       [
         {
             "path": "/path/to/single/excluded/property/?"
         },
         {
             "path": "/path/to/root/of/multiple/excluded/properties/*"
         }
      ]
   }
   ````

   위의 인덱싱 정책을 다음 정책으로 바꿉니다.

   ```json
   {
       "indexingMode": "none"
   }
   ```

1. 다음 코드 조각을 사용 하 여 연결 개체를 만듭니다. 연결 개체 (@Bean에 배치 하거나 정적으로 설정):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
    client= CosmosClient.builder()
        .endpoint(Host)//(Host, MasterKey, dbName, collName).Builder()
        .connectionPolicy(cp)
        .key(MasterKey)
        .consistencyLevel(ConsistencyLevel.EVENTUAL)
        .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

이제 다음과 같이 CRUD 작업을 실행할 수 있습니다.

### <a name="read-operation"></a>읽기 작업

항목을 읽으려면 다음 코드 조각을 사용 합니다.

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
        
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.item()!=null)
            {
                doc= resourceResponse.properties().toObject(UserModel.class);
            }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>삽입 작업

항목을 삽입 하려면 다음 코드를 수행할 수 있습니다.

```java 
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 mono를 구독 합니다.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
            if(resourceResponse.statusCode()!=successStatus)
                {
                    throw new RuntimeException(resourceResponse.toString());
                }
            },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 작업

항목의 값을 업데이트 하려면 아래 코드 조각을 참조 하세요.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 mono를 구독 합니다. 삽입 작업에서 mono 구독 코드 조각을 참조 하세요.

### <a name="delete-operation"></a>삭제 작업

다음 코드 조각을 사용 하 여 삭제 작업을 실행할 수 있습니다.

```java     
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 mono를 구독 합니다. 삽입 작업에서 mono 구독 코드 조각을 참조 하세요. 전체 코드 샘플은 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/master/AsyncKeyValue) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="data-migration"></a>데이터 마이그레이션

데이터를 마이그레이션하는 방법에는 두 가지가 있습니다.

* **Azure Data Factory 사용:** 데이터를 마이그레이션하는 가장 권장 되는 방법입니다. 원본 Azure Cosmos DB SQL API로 원본 및 싱크를 구성 하 고 자세한 단계는 Azure [Cosmos DB Data Factory 커넥터](../data-factory/connector-azure-cosmos-db.md) 문서를 참조 하세요.

* **Azure Cosmos DB 데이터 가져오기 도구를 사용 합니다.** 이 옵션은 데이터 양이 더 작은 Vm을 사용 하 여 마이그레이션하는 데 권장 됩니다. 자세한 단계는 [데이터 가져오기](./import-data.md) 문서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 성능 테스트를 수행 하려면 [Azure Cosmos DB를 사용한 성능 및 규모 테스트](./performance-testing.md) 를 참조 하세요.
* 코드를 최적화 하려면 Azure Cosmos DB 문서 [에 대 한 성능 팁](./performance-tips-async-java.md) 을 참조 하세요.
* Java Async V3 SDK, [SDK 참조](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 리포지토리를 살펴보세요.
