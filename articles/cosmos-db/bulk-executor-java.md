---
title: Bulk Executor Java 라이브러리를 사용하여 Azure Cosmos DB에서 대량 가져오기 및 업데이트 작업 수행
description: Bulk Executor Java 라이브러리를 사용하여 Azure Cosmos DB 문서를 대량으로 가져오고 업데이트합니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: b6a5712c617ab1e16b5341d9727b840fe8ea2213
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60894410"
---
# <a name="use-bulk-executor-java-library-to-perform-bulk-operations-on-azure-cosmos-db-data"></a>Bulk Executor Java 라이브러리를 사용하여 Azure Cosmos DB 데이터에서 대량 작업 수행

이 자습서는 Azure Cosmos DB Bulk Executor Java 라이브러리를 사용하여 Azure Cosmos DB 문서를 가져오고 업데이트하는 데 관한 지침을 제공합니다. Bulk Executor 라이브러리와 방대한 처리량 및 저장소를 활용하는 방법에 대한 자세한 내용은 [Bulk Executor 라이브러리 개요](bulk-executor-overview.md) 문서를 참조하세요. 이 자습서에서는 임의의 문서를 생성하는 Java 애플리케이션을 빌드하고 이러한 문서를 Azure Cosmos DB 컨테이너에 대량으로 가져옵니다. 가져온 후 문서의 일부 속성을 대량 업데이트합니다. 

현재 Azure Cosmos DB SQL API 및 Gremlin API 계정에서만 Bulk Executor 라이브러리가 지원됩니다. 이 문서에서는 SQL API 계정을 통해 Bulk Executor .NET 라이브러리를 사용하는 방법을 설명합니다. Bulk Executor .NET 라이브러리 사용에 대해 알아보려면 [Azure Cosmos DB Gremlin API에서 대량 작업 수행](bulk-executor-graph-dotnet.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.  

* Azure 구독, 요금 및 약정 없이 [무료로 Azure Cosmos DB를 사용해 볼 수 있습니다](https://azure.microsoft.com/try/cosmosdb/). 또는 엔드포인트가 `https://localhost:8081`인 [Azure Cosmos DB 에뮬레이터](https://docs.microsoft.com/azure/cosmos-db/local-emulator)를 사용할 수 있습니다. 기본 키는 [인증 요청](local-emulator.md#authenticating-requests)에 제공됩니다.  

* [JDK(Java Development Kit) 1.7+](https://aka.ms/azure-jdks)  
  - Ubuntu에서 `apt-get install default-jdk`를 실행하여 JDK를 설치합니다.  

  - JAVA_HOME 환경 변수가 반드시 JDK가 설치된 폴더를 지정하도록 설정합니다.

* [Maven](https://maven.apache.org/) 이진 아카이브 [다운로드](https://maven.apache.org/download.cgi) 및 [설치](https://maven.apache.org/install.html)  
  
  - Ubuntu에서 `apt-get install maven`을 실행하여 Maven을 실행할 수 있습니다.

* Java 빠른 시작 문서의 [데이터베이스 계정 만들기](create-sql-api-java.md#create-a-database-account) 섹션에 설명된 단계를 사용하여 Azure Cosmos DB SQL API 계정을 만듭니다.

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 샘플 Java 애플리케이션을 다운로드하여 코드 작업으로 전환해 보겠습니다. 이 애플리케이션은 Azure Cosmos DB 데이터에서 대량 작업을 수행합니다. 애플리케이션을 복제하려면 명령 프롬프트를 열고, 애플리케이션을 복사할 디렉터리로 이동한 후, 다음 명령을 실행합니다.

```
 git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started.git 
```

복제한 리포지토리에는 “\azure-cosmosdb-bulkexecutor-java-getting-started\samples\bulkexecutor-sample\src\main\java\com\microsoft\azure\cosmosdb\bulkexecutor” 폴더에 대한 두 개의 샘플 “bulkimport” 및 “bulkupdate”가 포함됩니다. “bulkimport” 애플리케이션은 임의의 문서를 생성하고 Azure Cosmos DB에 가져옵니다. “bulkupdate” 애플리케이션은 Azure Cosmos DB에서 일부 문서를 업데이트합니다. 다음 섹션에서는 이러한 각 샘플 앱에서 코드를 검토하겠습니다. 

## <a name="bulk-import-data-to-azure-cosmos-db"></a>Azure Cosmos DB로 데이터 대량 가져오기

1. Azure Cosmos DB의 연결 문자열은 인수로 읽고 CmdLineConfiguration.java 파일에 정의된 변수로 할당됩니다.  

2. 다음으로 DocumentClient 개체는 다음 명령문을 사용하여 초기화됩니다.  

   ```java
   ConnectionPolicy connectionPolicy = new ConnectionPolicy();
   connectionPolicy.setMaxPoolSize(1000);
   DocumentClient client = new DocumentClient(
      HOST,
      MASTER_KEY, 
      connectionPolicy,
      ConsistencyLevel.Session)
   ```

3. DocumentBulkExecutor 개체는 대기 시간 및 스로틀된 요청을 위해 높은 다시 시도 값으로 초기화됩니다. 그런 다음, 정체 제어를 해당 수명에 대한 DocumentBulkExecutor로 전달하도록 0으로 설정됩니다.  

   ```java
   // Set client's retry options high for initialization
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(30);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(9);

   // Builder pattern
   Builder bulkExecutorBuilder = DocumentBulkExecutor.builder().from(
     client,
     DATABASE_NAME,
     COLLECTION_NAME,
     collection.getPartitionKey(),
     offerThroughput) // throughput you want to allocate for bulk import out of the container's total throughput

   // Instantiate DocumentBulkExecutor
   DocumentBulkExecutor bulkExecutor = bulkExecutorBuilder.build()

   // Set retries to 0 to pass complete control to bulk executor
   client.getConnectionPolicy().getRetryOptions().setMaxRetryWaitTimeInSeconds(0);
   client.getConnectionPolicy().getRetryOptions().setMaxRetryAttemptsOnThrottledRequests(0);
   ```

4. 임의 문서를 생성하여 Azure Cosmos DB 컨테이너에 대량으로 가져오는 importAll API를 호출합니다. CmdLineConfiguration.java 파일 내에서 명령줄 구성을 구성할 수 있습니다.

   ```java
   BulkImportResponse bulkImportResponse = bulkExecutor.importAll(documents, false, true, null);
   ```
   대량 가져오기 API가 JSON 직렬화된 문서의 컬렉션을 수락하고 다음과 같은 구문을 가집니다. 자세한 내용은 [API 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)를 참조하세요.

   ```java
   public BulkImportResponse importAll(
        Collection<String> documents,
        boolean isUpsert,
        boolean disableAutomaticIdGeneration,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;   
   ```

   importAll 메서드는 다음 매개 변수를 수락합니다.
 
   |**매개 변수**  |**설명**  |
   |---------|---------|
   |isUpsert    |   문서의 upsert를 사용할 수 있도록 설정하는 플래그입니다. 주어진 ID의 문서가 이미 존재하는 경우 업데이트됩니다.  |
   |disableAutomaticIdGeneration     |   ID의 자동 생성을 사용하지 않도록 설정하는 플래그입니다. 기본적으로 true로 설정됩니다.   |
   |maxConcurrencyPerPartitionRange    |  파티션 키 범위당 최대 동시성 수준입니다. 기본값은 20입니다.  |

   **대량 가져오기 응답 개체 정의** 대량 가져오기 API 호출의 결과에는 다음 get 메서드가 포함되어 있습니다.

   |**매개 변수**  |**설명**  |
   |---------|---------|
   |int getNumberOfDocumentsImported()  |   대량 가져오기 API 호출에 적용된 문서 중에서 성공적으로 가져온 총 문서 수입니다.      |
   |double getTotalRequestUnitsConsumed()   |  대량 가져오기 API 호출에서 사용된 총 요청 단위(RU)입니다.       |
   |Duration getTotalTimeTaken()   |    실행을 완료하기까지 대량 가져오기 API 호출에서 사용하는 총 시간입니다.     |
   |목록\<예외 > getErrors() |  대량 가져오기 API 호출에 적용된 일괄 처리 중에서 일부 문서가 삽입에 실패한 경우 오류의 목록을 가져옵니다.       |
   |List\<Object> getBadInputDocuments()  |    대량 가져오기 API 호출에 성공적으로 가져오지 못한 잘못된 형식의 문서 목록입니다. 사용자는 반환된 문서를 수정하고 가져오기를 다시 시도해야 합니다. 잘못된 형식의 문서에는 ID 값이 문자열이 아닌 문서가 포함됩니다(null 또는 다른 데이터 형식이 잘못된 것으로 간주됨).     |

5. 대량 가져오기 애플리케이션을 준비한 후, ‘mvn clean package’ 명령을 사용하여 원본의 명령줄 도구를 빌드합니다. 이 명령은 대상 폴더에서 jar 파일을 생성합니다.  

   ```java
   mvn clean package
   ```

6. 대상 종속성이 생성된 후 다음 명령을 사용하여 대량 가져오기 애플리케이션을 호출할 수 있습니다.  

   ```java
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint *<Fill in your Azure Cosmos DB’s endpoint>*  -masterKey *<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkImportDb -collectionId bulkImportColl -operation import -shouldCreateCollection -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

   대량 가져오기는 App.config 파일에 지정된 데이터베이스 이름, 컬렉션 이름 및 처리량 값이 포함된 새 데이터베이스 및 컬렉션을 만듭니다. 

## <a name="bulk-update-data-in-azure-cosmos-db"></a>Azure Cosmos DB에서 데이터 대량 업데이트

BulkUpdateAsync API를 사용하여 기존 문서를 업데이트할 수 있습니다. 이 예제에서는 이름 필드를 새 값으로 설정하고 기존 문서에서 설명 필드를 제거합니다. 지원되는 필드 업데이트 작업의 전체 집합은 [API 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)를 참조하세요. 

1. 해당 필드 업데이트 작업과 함께 업데이트 항목을 정의합니다. 이 예제에서는 SetUpdateOperation을 사용하여 이름 필드를 업데이트하고, UnsetUpdateOperation을 사용하여 모든 문서에서 설명 필드를 제거합니다. 특정 값으로 문서 필드 증가와 같은 다른 작업을 수행하거나, 배열 필드에 특정 값을 푸시하거나, 배열 필드에서 특정 값을 제거할 수 있습니다. 대량 업데이트 API에서 제공하는 다른 방법을 알아보려면 [API 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)를 참조하세요.  

   ```java
   SetUpdateOperation<String> nameUpdate = new SetUpdateOperation<>("Name","UpdatedDocValue");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   ArrayList<UpdateOperationBase> updateOperations = new ArrayList<>();
   updateOperations.add(nameUpdate);
   updateOperations.add(descriptionUpdate);

   List<UpdateItem> updateItems = new ArrayList<>(cfg.getNumberOfDocumentsForEachCheckpoint());
   IntStream.range(0, cfg.getNumberOfDocumentsForEachCheckpoint()).mapToObj(j -> {                      
    return new UpdateItem(Long.toString(prefix + j), Long.toString(prefix + j), updateOperations);
    }).collect(Collectors.toCollection(() -> updateItems));
   ```

2. 임의 문서를 생성한 다음, Azure Cosmos DB 컨테이너에 대량으로 가져오는 updateAll API를 호출합니다. CmdLineConfiguration.java 파일에 전달할 명령줄 구성을 구성할 수 있습니다.

   ```java
   BulkUpdateResponse bulkUpdateResponse = bulkExecutor.updateAll(updateItems, null)
   ```

   대량 업데이트 API는 업데이트할 항목의 컬렉션을 수락합니다. 각 업데이트 항목은 ID와 파티션 키 값에 의해 식별되는 문서에서 수행할 필드 업데이트 작업의 목록을 지정합니다. 자세한 내용은 [API 설명서](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)를 참조하세요.

   ```java
   public BulkUpdateResponse updateAll(
        Collection<UpdateItem> updateItems,
        Integer maxConcurrencyPerPartitionRange) throws DocumentClientException;
   ```

   updateAll 메서드는 다음 매개 변수를 수락합니다.

   |**매개 변수** |**설명** |
   |---------|---------|
   |maxConcurrencyPerPartitionRange   |  파티션 키 범위당 최대 동시성 수준입니다. 기본값은 20입니다.  |
 
   **대량 가져오기 응답 개체 정의** 대량 가져오기 API 호출의 결과에는 다음 get 메서드가 포함되어 있습니다.

   |**매개 변수** |**설명**  |
   |---------|---------|
   |int getNumberOfDocumentsUpdated()  |   대량 업데이트 API 호출에 적용된 문서 중에서 성공적으로 업데이트된 총 문서 수입니다.      |
   |double getTotalRequestUnitsConsumed() |  대량 업데이트 API 호출에서 사용한 총 요청 단위(RU)입니다.       |
   |Duration getTotalTimeTaken()  |   실행을 완료하기까지 대량 업데이트 API 호출에서 사용하는 총 시간입니다.      |
   |목록\<예외 > getErrors()   |    대량 업데이트 API 호출에 적용된 일괄 처리 중에서 일부 문서가 삽입에 실패한 경우 오류의 목록을 가져옵니다.      |

3. 대량 업데이트 애플리케이션을 준비한 후, ‘mvn clean package’ 명령을 사용하여 원본의 명령줄 도구를 빌드합니다. 이 명령은 대상 폴더에서 jar 파일을 생성합니다.  

   ```
   mvn clean package
   ```

4. 대상 종속성이 생성된 후 다음 명령을 사용하여 대량 업데이트 애플리케이션을 호출할 수 있습니다.

   ```
   java -Xmx12G -jar bulkexecutor-sample-1.0-SNAPSHOT-jar-with-dependencies.jar -serviceEndpoint **<Fill in your Azure Cosmos DB’s endpoint>* -masterKey **<Fill in your Azure Cosmos DB’s master key>* -databaseId bulkUpdateDb -collectionId bulkUpdateColl -operation update -collectionThroughput 1000000 -partitionKey /profileid -maxConnectionPoolSize 6000 -numberOfDocumentsForEachCheckpoint 1000000 -numberOfCheckpoints 10
   ```

## <a name="performance-tips"></a>성능 팁 

Bulk Executor 라이브러리를 사용하는 경우 성능 향상을 위해 다음 사항을 고려합니다.

* 최상의 성능을 위해 Cosmos DB 계정 작성 지역과 동일한 지역의 Azure VM에서 애플리케이션을 실행합니다.  
* 더 높은 처리량을 달성하려면:  

   * 많은 수의 문서를 처리할 때 메모리 문제를 방지하기 위해 JVM의 힙 크기를 충분한 수로 설정합니다. 제안되는 힙 크기: 최대(3GB, 3 * sizeof(각 일괄 처리에서 대량 가져오기 API로 전달된 모든 문서)).  
   * 문서 수가 많은 대량 작업을 수행하면 처리량이 더 높아지므로 전처리 시간이 있습니다. 따라서 10,000,000개 문서를 가져오려는 경우 크기가 100,000개인 대량 문서 100개에서 대량 가져오기를 100번 수행하는 것보다 각 크기가 1,000,000개인 대량 문서 10개에서 대량 가져오기를 10번 실행하는 것이 좋습니다.  

* 특정 Azure Cosmos DB 컨테이너에 해당하는 단일 가상 머신 내에서 전체 애플리케이션에 대한 단일 DocumentBulkExecutor 개체를 인스턴스화하는 것이 좋습니다.  

* 단일 대량 작업 API 실행은 클라이언트 컴퓨터의 CPU 및 네트워크 IO의 대규모 청크를 사용하기 때문입니다. 이는 내부적으로 여러 작업을 생성되며 발생합니다. 대량 작업 API 호출을 각각 실행하는 애플리케이션 내에서 여러 동시 작업이 발생하지 않도록 하십시오. 단일 가상 머신에서 실행되는 단일 대량 작업 API 호출에서 전체 컨테이너의 처리량을 사용할 수 없는 경우(컨테이너의 처리량이 1백만 RU/s 미만인 경우) 대량 작업 API 호출을 동시에 실행하도록 개별 가상 머신을 만드는 것이 좋습니다.

    
## <a name="next-steps"></a>다음 단계
* Maven 패키지 세부 정보 및 Bulk Executor Java 라이브러리의 릴리스 정보를 알아보려면 [Bulk Executor SDK 세부 정보](sql-api-sdk-bulk-executor-java.md)를 참조하세요.


