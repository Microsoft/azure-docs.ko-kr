---
title: Async Java용 Azure Cosmos DB 성능 팁
description: Azure Cosmos DB 데이터베이스 성능 향상을 위한 클라이언트 구성 옵션에 대한 자세한 정보
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 07da7f8905d7b8952db852d3da1dab12884de509
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60932921"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Azure Cosmos DB 및 Async Java에 대한 성능 팁

> [!div class="op_single_selector"]
> * [비동기 Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. Azure Cosmos DB를 사용하여 데이터베이스의 크기를 조정하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요는 없습니다. 규모를 확장 및 축소하는 것은 단일 API 호출 또는 SDK 메서드 호출을 수행하는 것만큼 쉽습니다. 그러나 네트워크 호출을 통해 Azure Cosmos DB에 액세스하기 때문에 [SQL Async Java SDK](sql-api-sdk-async-java.md)를 사용할 때 최대 성능을 얻기 위해 클라이언트 쪽에서 최적화를 지정할 수 있습니다.

"내 데이터베이스 성능을 향상시키는 방법"을 물으면 다음 옵션을 고려합니다.

## <a name="networking"></a>네트워킹
   <a id="same-region"></a>
1. **성능을 위해 동일한 Azure 지역에 클라이언트 배치**

    가능한 경우 Azure Cosmos DB를 호출하는 모든 애플리케이션을 Azure Cosmos DB 데이터베이스와 동일한 지역에 배치합니다. 대략적으로 비교한다면, 동일한 지역 내의 Azure Cosmos DB 호출은 1-2밀리초 내에 완료되지만 미국 서부와 동부 해안 간의 대기 시간은 50밀리초보다 큽니다. 클라이언트에서 Azure 데이터 센터 경계로 요청이 전달되는 경로에 따라 이러한 요청 간 대기 시간은 달라질 수 있습니다. 호출하는 애플리케이션이 프로비전된 Azure Cosmos DB 엔드포인트와 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

    ![Azure Cosmos DB 연결 정책 그림](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK 사용
1. **최신 SDK 설치**

    Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.
2. **애플리케이션 수명 동안 singleton Azure Cosmos DB 클라이언트 사용**

    각 AsyncDocumentClient 인스턴스는 스레드로부터 안전하고 효율적인 연결 관리와 주소 캐싱을 수행합니다. AsyncDocumentClient에 의해 연결을 효율적으로 관리하고 성능을 개선하려면 애플리케이션 수명 동안 AppDomain당 AsyncDocumentClient의 단일 인스턴스를 사용하는 것이 좋습니다.

   <a id="max-connection"></a>

3. **ConnectionPolicy 튜닝**

    Async Java SDK를 사용하는 경우 Azure Cosmos DB 요청은 HTTPS/REST를 통해 수행되며 기본 최대 연결 풀 크기(1000)가 적용됩니다. 이 기본값은 대부분의 사용 사례에 적합해야 합니다. 그러나 많은 파티션이 있는 큰 컬렉션의 경우 setMaxPoolSize를 사용하여 최대 연결 풀 크기를 더 큰 숫자(예: 1500)로 설정할 수 있습니다.

4. **분할된 컬렉션에 대한 병렬 쿼리 튜닝**

    Azure Cosmos DB SQL Async Java SDK는 분할된 컬렉션을 병렬로 쿼리할 수 있는 병렬 쿼리를 지원합니다. 자세한 내용은 SDK 사용과 관련된 [코드 샘플](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples)을 참조하세요. 병렬 쿼리는 해당 직렬 대응을 통해 쿼리 대기 시간 및 처리량을 개선하기 위해 설계되었습니다.

    (a) ***setMaxDegreeOfParallelism 튜닝\:*** 여러 파티션을 병렬로 쿼리하여 병렬 쿼리가 작동합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 순차적으로 가져오기 됩니다. 따라서 setMaxDegreeOfParallelism을 사용하여 파티션 수를 설정하면 다른 모든 시스템 조건을 동일하게 유지하는 동시에 가장 성능이 뛰어난 쿼리를 달성할 수 있는 가능성을 극대화합니다. 파티션 수를 모르는 경우 setMaxDegreeOfParallelism을 사용하여 더 높은 값을 설정할 수 있습니다. 그러면 시스템에서 최소값(사용자가 제공한 입력인 파티션 수)을 최대 병렬 처리 수준으로 선택합니다.

    데이터가 쿼리와 관련하여 모든 파티션에 균등하게 분산되어 있는 경우 병렬 쿼리가 최고의 성능을 발휘한다는 것이 중요합니다. 쿼리에서 반환된 전체 또는 대부분의 데이터가 몇 개의 파티션(최악의 경우 하나의 파티션)에 집중되는 것처럼 분할된 컬렉션이 분할되는 경우 해당 파티션으로 인해 쿼리의 성능에는 장애가 발생합니다.

    (b) ***setMaxBufferedItemCount 튜닝\:*** 결과의 현재 일괄 처리가 클라이언트에서 처리되는 반면 병렬 쿼리는 결과를 프리페치하도록 설계되었습니다. 프리페치는 쿼리의 전체 대기 시간 개선 사항에 도움이 됩니다. setMaxBufferedItemCount는 프리페치된 결과의 수를 제한합니다. setMaxBufferedItemCount를 설정하면 반환된 결과의 예상 수(또는 더 높은 수)로 설정하면 쿼리가 프리페치의 최대 이점을 얻을 수 있습니다.

    프리페치는 MaxDegreeOfParallelism에 관계없이 동일한 방식으로 작동하고 여기에는 모든 파티션의 데이터에 대한 단일 버퍼가 있습니다.

5. **getRetryAfterInMilliseconds 간격으로 백오프 구현**

    성능 테스트 중에는 작은 비율의 요청이 제한될 때까지 로드를 늘려야 합니다. 제한될 경우 클라이언트 애플리케이션은 서버에서 지정한 재시도 간격 제한을 백오프해야 합니다. 백오프를 통해 재시도 간 기간을 최소화할 수 있습니다.
6. **클라이언트 워크로드 규모 확장**

    높은 처리량 수준에서 테스트하는 경우(&gt;50,000 RU/s) 컴퓨터의 CPU 또는 네트워크 사용률이 최대화되므로 클라이언트 애플리케이션은 병목 상태가 될 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

7. **이름 기반 주소 지정 사용**

    링크를 생성하는 데 사용된 모든 리소스의 ResourceId를 검색하지 않도록 링크 형식이 `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`인 SelfLinks(\_self) 대신 링크 형식이 `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`인 이름 기준 주소를 사용합니다. 또한 이러한 리소스는 (동일한 이름을 사용하여) 다시 생성되므로 이를 캐싱하는 것이 별 도움이 되지 않을 수도 있습니다.

   <a id="tune-page-size"></a>
8. **성능 향상을 위해 쿼리/읽기 피드에 맞게 페이지 크기 조정**

    읽기 피드 기능을 사용하여 대량의 문서 읽기를 수행하거나(예: readDocuments) SQL 쿼리를 실행하면 결과 집합이 너무 큰 경우 결과가 분할되어 반환됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

    모든 적용 가능한 결과를 검색하는 데 필요한 네트워크 왕복 횟수를 줄이려면 [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 요청 헤더를 사용하는 페이지 크기를 최대 1000으로 늘릴 수 있습니다. 사용자 인터페이스 또는 애플리케이션 API가 한 번에 10개의 결과만 반환하는 것처럼 몇 가지 결과만 표시해야 하는 경우, 읽기 및 쿼리에 사용되는 처리량을 줄이기 위해 페이지 크기를 10으로 줄일 수도 있습니다.

    setMaxItemCount 메서드를 사용하여 페이지 크기를 설정할 수도 있습니다.

9. **적절한 스케줄러 사용(이벤트 루프 IO Netty 스레드 도용 방지)**

    Async Java SDK는 비블록킹 IO에 [netty](https://netty.io/)를 사용합니다. SDK는 IO 작업을 실행하기 위해 고정된 수의 IO netty 이벤트 루프 스레드(시스템에 있는 CPU 코어 수만큼)를 사용합니다. API에서 반환된 Observable은 공유 IO 이벤트 루프 netty 스레드 중 하나에서 결과를 도출합니다. 따라서 이 공유 IO 이벤트 루프 netty 스레드를 차단하지 않는 것이 중요합니다. IO 이벤트 루프 netty 스레드에서 CPU 집약적인 작업 또는 차단 작업을 수행하면 교착 상태가 발생하거나 SDK 처리량이 크게 저하될 수 있습니다.

    예를 들어 다음 코드는 이벤트 루프 IO netty 스레드에서 CPU 집약적인 작업을 실행합니다.

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    결과에 대해 CPU 집약적인 작업을 수행하려는 경우 결과를 수신한 후에는 이벤트 루프 IO netty 스레드에서 이를 수행하지 않아야 합니다. 대신 사용자의 스케줄러를 제공하여 작업 실행을 위해 사용자의 스레드를 제공할 수 있습니다.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    작업 유형에 따라 작업에 적절한 RxJava Scheduler를 사용해야 합니다. 여기 [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)를 읽으세요.

    자세한 내용은 [GitHub 페이지](https://github.com/Azure/azure-cosmosdb-java)에서 Async Java SDK를 확인하세요.

10. **Netty의 로깅 사용 안 함** Netty 라이브러리 로깅은 대화량이 많으므로 추가 CPU 비용을 피하려면 꺼두어야 합니다(구성에서 로그인을 억제하는 것으로 충분하지 않음). 디버깅 모드가 아니라면 netty의 로깅을 모두 해제합니다. 따라서 log4j를 사용하여 netty에서 ``org.apache.log4j.Category.callAppenders()``에 의한 추가 CPU 비용이 발생하지 않도록 하려면 코드베이스에 다음 행을 추가합니다.

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **OS 오픈 파일 리소스 제한** 일부 Linux 시스템(Red Hat 등)에는 열린 파일 수와 총 연결 수에 대한 상한이 있습니다. 현재 한도를 보려면 다음을 실행합니다.

    ```bash
    ulimit -a
    ```

    열린 파일(nofile)의 수는 OS에 의해 구성된 연결 풀 크기와 다른 열린 파일을 위한 공간이 충분할 만큼 커야 합니다. 연결 풀 크기를 더 크게 수정할 수 있습니다.

    limits.conf 파일을 엽니다.

    ```bash
    vim /etc/security/limits.conf
    ```
    
    다음 줄을 추가/수정합니다.

    ```
    * - nofile 100000
    ```

12. **Netty에 원시 SSL 구현 사용** Netty는 SSL 구현 스택에 OpenSSL을 직접 사용하여 성능을 향상시킬 수 있습니다. 이 구성이 없으면 Netty는 Java의 기본 SSL 구현으로 돌아갑니다.

    Ubuntu의 경우:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    프로젝트 maven 종속성에 다음 종속성을 추가합니다.
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

기타 플랫폼(Red Hat, Windows, Mac 등)의 경우 이러한 지침(https://netty.io/wiki/forked-tomcat-native.html)을 참조하세요.

## <a name="indexing-policy"></a>인덱싱 정책
 
1. **더 빠른 쓰기에 대한 인덱싱에서 사용하지 않는 경로 제외**

    Azure Cosmos DB의 인덱싱 정책을 통해 인덱싱 경로(setIncludedPaths 및 setExcludedPaths)를 활용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수 있습니다. 인덱싱 비용이 인덱싱된 고유 경로 수와 직접 관련이 있기 때문에, 인덱싱 경로를 사용하면 사전에 알려진 쿼리 패턴의 시나리오에 대해 쓰기 성능을 향상시키고 인덱스 저장소를 낮출 수 있습니다. 예를 들어 다음 코드는 "*" 와일드카드를 사용하여 인덱싱에서 문서의 전체 섹션(하위 트리라고도 함)을 제외하는 방법을 보여 줍니다.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    자세한 내용은 [Azure Cosmos DB 인덱싱 정책](indexing-policies.md)을 참조하세요.

## <a name="throughput"></a>처리량
<a id="measure-rus"></a>

1. **낮은 요청 단위/초 사용량 측정 및 튜닝**

    Azure Cosmos DB는 관계형 쿼리와 계층형 쿼리 등 다양한 데이터베이스 작업에 데이터베이스 컬렉션 내부의 문서에서 적용되는 UDF, 저장 프로시저 및 트리거를 제공합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 애플리케이션 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

    처리량은 각 컨테이너에 설정된 [요청 단위](request-units.md) 수에 따라 프로비전됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 해당 컨테이너에 프로비전된 요청 단위 비율을 초과하는 애플리케이션은 비율이 컨테이너에 프로비전된 수준 아래로 떨어질 때까지 제한됩니다. 애플리케이션에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로비전하여 처리량을 늘릴 수 있습니다.

    쿼리의 복잡성은 작업에 사용되는 요청 단위의 양에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.

    모든 작업(만들기, 업데이트 또는 삭제)에 대한 오버헤드를 측정하려면 [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 헤더를 검사하여 이 작업에 사용된 요청 단위 수를 측정합니다. ResourceResponse<T> 또는 FeedResponse<T>에서 동일한 RequestCharge 속성을 볼 수도 있습니다.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    이 헤더에서 반환된 요청 비용은 프로비전된 처리량의 일부입니다. 예를 들어 RU 2000개를 프로비전했고 앞의 쿼리에서 1000개의 1KB 문서를 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md)와 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.
<a id="429"></a>
2. **너무 큰 속도 제한/요청 속도 처리**

    클라이언트가 계정에 대해 예약된 처리량을 초과하려 할 때도 서버의 성능이 저하되거나 예약된 수준 이상의 처리량이 사용되지 않습니다. 서버에서 RequestRateTooLarge(HTTP 상태 코드 429)를 사용하여 선제적으로 요청을 종료하고, 사용자가 요청을 다시 시도할 수 있을 때까지 기다려야 하는 시간을 밀리초 단위로 표시하는 [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 헤더를 반환합니다.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

    하나 이상의 클라이언트가 누적적으로 요청 속도를 계속 초과하여 작동하는 경우,클라이언트에 의해 현재 내부적으로 9로 설정된 기본 재시도 횟수는 충분하지 않을 수 있습니다. 이 경우 클라이언트는 애플리케이션에 상태 코드가 429인 DocumentClientException을 throw합니다. 기본 재시도 횟수는 ConnectionPolicy 인스턴스에서 setRetryOptions를 설정하여 변경할 수 있습니다. 기본적으로, 요청이 계속 요청 속도 이상으로 작동하는 경우 상태 코드가 429인 DocumentClientException은 누적 대기 시간 30초 후에 반환됩니다. 현재 재시도 횟수가 최대 재시도 횟수보다 작은 경우에도 이러한 현상이 발생하기 때문에 기본값인 9 또는 사용자 정의 값으로 두세요.

    자동화된 재시도 동작은 대부분의 애플리케이션에 대한 복원력 및 유용성을 개선하는 데 도움이 되는 반면, 성능 벤치마크 수행 시 특히 대기 시간을 측정할 때 방해가 될 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [요청 단위](request-units.md)를 참조하세요.
3. **처리량을 높이기 위해 문서 크기를 줄이도록 설계**

    주어진 작업의 요청 비용(요청 처리 비용)은 문서 크기와 직접 관련이 있습니다. 큰 문서에서 작업하는 경우 작은 문서 작업에 비해 비용이 많이 듭니다.

## <a name="next-steps"></a>다음 단계
확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.
