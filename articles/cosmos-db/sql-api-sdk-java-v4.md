---
title: Azure Cosmos DB Java SDK v4 for SQL API 릴리스 정보 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간 변경 내용을 포함하여 SQL API용 Azure Cosmos DB Java SDK v4 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: af0964dceca8b862d0008d878045203983a96bda
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586218"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for Core(SQL) API: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [스프링 데이터 v2](sql-api-sdk-java-spring-v2.md)
> * [스프링 데이터 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](/rest/api/cosmos-db/)
> * [REST 리소스 공급자](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Java SDK v4 for Core(SQL)는 비동기 API와 동기 API를 하나의 Maven 아티팩트로 결합합니다. V4 SDK는 Project Reactor 및 [Netty 라이브러리](https://netty.io/)를 기준으로 향상된 성능, 새 API 기능 및 비동기 지원을 제공합니다. Azure Cosmos DB Java SDK v4와 [Azure Cosmos DB Async Java SDK v2](sql-api-sdk-async-java.md) 및 [Azure Cosmos DB Sync Java SDK v2](sql-api-sdk-java.md)를 사용하여 성능을 향상시킬 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 Azure Cosmos DB Java SDK v4에만 해당됩니다. 현재 v4보다 이전 버전을 사용하는 경우 v4로 업그레이드하는 데 도움이 필요하면 [Azure Cosmos DB Java SDK v4로 마이그레이션](migrate-java-v4-sdk.md) 가이드를 참조하세요.
>
> 빠른 시작을 위한 세 가지 단계는 다음과 같습니다.
> 1. SDK를 사용할 수 있도록 [지원되는 최소 Java 런타임 JDK 8](/java/azure/jdk/?view=azure-java-stable)을 설치합니다.
> 2. Maven 아티팩트에 대한 액세스 권한을 가져오고 기본 Azure Cosmos DB 요청을 안내하는 [Azure Cosmos DB Java SDK v4에 대한 빠른 시작 가이드](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java)를 사용합니다.
> 3. 애플리케이션에 대한 SDK를 최적화하는 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md) 및 [문제 해결](troubleshoot-java-sdk-v4-sql.md) 가이드를 읽어 보세요.
>
> [Azure Cosmos DB 워크샵 및 랩](https://aka.ms/cosmosworkshop)은 Azure Cosmos DB Java SDK v4 사용 방법을 배우는 데 유용한 또 다른 리소스입니다.
>

## <a name="helpful-content"></a>유용한 내용

| 콘텐츠 | 링크 |
|---|---|
|**SDK 다운로드**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API 설명서** | [Java API 참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**SDK에 참여** | [GitHub의 Java용 Azure SDK 중앙 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**시작** | [빠른 시작: Azure Cosmos DB SQL API 데이터를 관리하는 Java 앱 빌드](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [빠른 시작 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**기본 코드 샘플** | [Azure Cosmos DB: SQL API에 대한 Java 예제](sql-api-java-sdk-samples.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**변경 피드를 사용하는 콘솔 앱**| [변경 피드-Java SDK v4 샘플](create-sql-api-java-changefeed.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**웹앱 샘플**| [Java SDK v4를 사용 하 여 웹 앱 빌드](sql-api-java-application.md) <br> [샘플 코드가 포함 된 GitHub 리포지토리](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **성능 팁**| [Java SDK v4용 성능 팁](performance-tips-java-sdk-v4-sql.md)| 
| **문제 해결** | [Java SDK v4 문제 해결](troubleshoot-java-sdk-v4-sql.md) |
| **이전 SDK에서 v4로 마이그레이션** | [Java V4 SDK로 마이그레이션](migrate-java-v4-sdk.md) |
| **지원되는 최소 런타임**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB 워크샵 및 랩** |[Cosmos DB 워크샵 홈페이지](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>릴리스 기록

### <a name="440-beta1-unreleased"></a>4.4.0-베타. 1 (릴리스되지 않음)

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29)
#### <a name="new-features"></a>새로운 기능
* 재 행위자-핵심 라이브러리 버전을 (으)로 업데이트 했습니다 `3.3.8.RELEASE` . 
* Reactor-netty 라이브러리 버전이로 업데이트 되었습니다 `0.9.10.RELEASE` . 
* Netty 라이브러리 버전을로 업데이트 했습니다 `4.1.51.Final` . 
* 를 사용 하 여에 대 한 새 오버 로드 Api를 추가 했습니다 `upsertItem` `partitionKey` . 
* 개방형 원격 분석 추적 지원을 추가 했습니다. 
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 게이트웨이 모드에서 요청을 취소 하는 경우 SSLException이 throw 되는 문제를 해결 했습니다.
* 저장 프로시저 실행에 대 한 리소스 제한 재시도 정책을 수정 했습니다.
* SDK가 로그 수준 디버그 모드에서 중단 되는 문제를 해결 했습니다. 
* 직접 모드의 대기 시간에 대 한 주기적인 급증을 수정 했습니다. 
* 높은 클라이언트 초기화 시간 문제를 수정 했습니다. 
* 직접 모드와 게이트웨이 모드를 사용 하 여 클라이언트를 사용자 지정할 때 http 프록시 버그가 수정 되었습니다. 
* 사용자의 잠재적 NPE이 null 옵션을 전달 하는 문제를 수정 했습니다. 
* 진단 문자열에 timeUnit를 추가 했습니다 `requestLatency` .
* 진단 문자열에서 중복 된 uri 문자열을 제거 했습니다. 
* 시점 작업을 위한 적절 한 JSON 형식의 진단 문자열을 수정 했습니다.
* `.single()`찾을 수 없는 예외가 발생 하는 경우 원자로 체인이 중단 되도록 하는 연산자 문제를 해결 했습니다. 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>새로운 기능
* 스크립트 로깅 사용 API를에 추가 했습니다 `CosmosStoredProcedureRequestOptions` .
* 기본값을 1 시간로 업데이트 하 `DirectConnectionConfig` `idleEndpointTimeout` 고 기본값 `connectTimeout` 을 5 초로 업데이트 합니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 가를 재정의 하는 문제를 해결 `GatewayConnectionConfig` `idleConnectionTimeout` 했습니다 `DirectConnectionConfig` `idleConnectionTimeout` .
* `responseContinuationTokenLimitInKb`의 get 및 Set api를 수정 `CosmosQueryRequestOptions` 했습니다.
* 동일한 이름을 가진 컬렉션을 다시 만들 때 쿼리 및 변경 피드의 문제를 해결 했습니다.
* ClassCastException을 throw 하는 상위 쿼리와 관련 된 문제가 해결 되었습니다.
* NullPointerException을 throw 하는 order by 쿼리와 관련 된 문제를 해결 했습니다.
* 직접 모드에서 취소 된 요청을 처리 하는 동안 발생 하는 문제를 해결 `onErrorDropped` 했습니다. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>새로운 기능
* 쿼리에 대 한 지원이 추가 되었습니다 `GROUP BY` .
* DirectConnectionConfig에서 maxConnectionsPerEndpoint의 기본값을 130로 늘립니다.
* DirectConnectionConfig에서 maxRequestsPerConnection의 기본값을 30으로 늘렸습니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 연속 토큰을 사용 하 여 다시 시작할 때 중복 결과를 반환 하는 order by 쿼리와 관련 된 문제가 해결 되었습니다. 
* 중첩 된 개체에 대해 null 값을 반환 하는 값 쿼리와 관련 된 문제가 해결 되었습니다.
* RntbdClientChannelPool의 요청 관리자에서 null 포인터 예외가 수정 되었습니다.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>새로운 기능
* `QueryRequestOptions`의 이름이 `CosmosQueryRequestOptions`로 바뀌었습니다.
* `ChangeFeedProcessorBuilder`작성기 패턴으로 업데이트 되었습니다.
* `CosmosPermissionProperties`새 컨테이너 이름 및 자식 리소스 api로 업데이트 되었습니다.
* 보강 docs & 추가 샘플이 추가 되었습니다 `CosmosClientBuilder` . 
* `CosmosDatabase`  &  `CosmosContainer` 자동 크기 조정/autopilot 지원에 대해 throughputProperties를 사용 하 여 api를 업데이트 했습니다. 
* `CosmosClientException`의 이름이 `CosmosException`로 바뀌었습니다. 
* `AccessCondition`  &  `AccessConditionType` Api로 대체 `ifMatchETag()`  &  `ifNoneMatchETag()` 되었습니다. 
* 모든 `Cosmos*AsyncResponse`  &  `CosmosResponse` 형식을 단일 형식으로 병합 `CosmosResponse` 했습니다.
* `CosmosResponseDiagnostics`의 이름이 `CosmosDiagnostics`로 바뀌었습니다.  
* `FeedResponseDiagnostics`에 래핑됩니다 `CosmosDiagnostics` . 
* Azure-core를 기반 `jackson` 으로 cosmos &에서 종속성을 제거 했습니다. 
* `CosmosKeyCredential`형식으로 대체 `AzureKeyCredential` 되었습니다. 
* `ProxyOptions`에 api를 추가 했습니다 `GatewayConnectionConfig` . 
* 대신 형식을 사용 하도록 SDK를 업데이트 했습니다 `Instant` `OffsetDateTime` . 
* 새 열거형 형식이 추가 되었습니다 `OperationKind` . 
* `FeedOptions`의 이름이 `QueryRequestOptions`로 바뀌었습니다. 
* `getETag()`  &  `getTimestamp()` 형식에 api를 추가 했습니다 `Cosmos*Properties` . 
* `userAgent`에 정보가 추가 되었습니다 `CosmosException`  &  `CosmosDiagnostics` . 
* 의 줄 바꿈 문자 `Diagnostics` 를 시스템 줄 바꿈 문자로 업데이트 했습니다. 
* `readAll*`Api를 제거 하 고 쿼리를 사용 하 여 대신 모든 api를 선택 합니다.
* `ChangeFeedProcessor`예상 지연 API를 추가 했습니다.   
* SDK에 자동 크기 조정/autopilot 처리량 프로 비전 지원이 추가 되었습니다.  
* `ConnectionPolicy`새 connection configs로 대체 되었습니다. `DirectConnectionConfig`  &  `GatewayConnectionConfig` `CosmosClientBuilder` 직접 & 게이트웨이 모드 연결 구성에 대 한 api를 통해 제공 됩니다.
* `JsonSerializable`  &  `Resource` 구현 패키지로 이동 했습니다. 
* `contentResponseOnWriteEnabled`쓰기 작업에서 전체 응답 콘텐츠를 사용 하지 않도록 설정 하는 API를 CosmosClientBuilder에 추가 했습니다.
* `getETag()`응답 형식에 대 한 api를 노출 합니다.
* `CosmosAuthorizationTokenResolver`구현으로 이동 되었습니다. 
* `preferredLocations`  &  `multipleWriteLocations` API 이름이로 바뀌었습니다 `preferredRegions`  &  `multipleWriteRegions` . 
* 3.3.5로 업데이트 되었습니다. `reactor-core` `reactor-netty` 0.9.7 & `netty` 4.1.49 버전으로 업데이트 되었습니다. 
* SDK에 대 한 지원이 추가 되었습니다 `analyticalStoreTimeToLive` .     
* `CosmosClientException``AzureException`를 확장 합니다. 
* 에서 api를 `maxItemCount`  &  `requestContinuationToken` `FeedOptions` 사용 하는 대신에서 api `byPage()` `CosmosPagedFlux`  &  `CosmosPagedIterable` 를 제거 했습니다.
* `CosmosPermissionProperties`Api에 대 한 공개 화면에서 도입 `Permission` 되었습니다.
* 제거 `SqlParameterList` 된 형식 &을 (를)로 바꿨습니다. `List`
* 직접 TCP 클라이언트에서 여러 메모리 누수 문제를 수정 했습니다. 
* 쿼리에 대 한 지원이 추가 되었습니다 `DISTINCT` . 
* 에서 외부 종속성을 제거 했습니다 `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* `CosmosPagedFlux`  &  `CosmosPagedIterable` 패키지로 이동 `utils` 했습니다. 
* Netty를 4.1.45로 업데이트 했습니다 & project 원자로를 3.3.3 버전으로 업데이트 했습니다.
* 공용 rest 계약을 클래스에 업데이트 했습니다 `Final` .
* 시점 운영에 대 한 고급 진단에 대 한 지원이 추가 되었습니다.
* 업데이트 된 패키지 `com.azure.cosmos`
* `models`모델/rest 계약에 대 한 패키지 추가
* `utils`형식에 대 한 패키지를 추가 했습니다 `CosmosPagedFlux`  &  `CosmosPagedIterable` . 
* SDK 전체에서 사용할 공용 Api를 업데이트 했습니다 `Duration` .
* 패키지에 모든 rest 계약을 추가 했습니다 `models` .
* `RetryOptions`의 이름이 `ThrottlingRetryOptions`으로 바뀌었습니다.
* `CosmosPagedFlux`  &  `CosmosPagedIterable` 쿼리 api에 대 한 페이지 매김 형식이 추가 되었습니다. 
* 에서 새 API를 사용 하 여 CosmosClients의 여러 인스턴스에서 지 수 클라이언트를 공유 하기 위한 지원이 추가 되었습니다. `CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* 이중 직렬화/deserialization을 제거 하 여 쿼리를 최적화 합니다. 
* 불필요 한 복사를 앞뒤로 제거 하 여 응답 헤더를 최적화 합니다. 
* `ByteBuffer`중간 문자열 인스턴스화를 제거 하 여 serialization/deserialization을 최적화 했습니다.

#### <a name="key-bug-fixes"></a>핵심 버그 수정
* ConnectionPolicy `toString()` Null 포인터 예외가 수정 되었습니다.
* 쿼리 결과 값이 쿼리의 경우 쿼리 결과의 구문 분석과 관련 된 문제를 해결 했습니다. 
* 직접 TCP 클라이언트의 소켓 유출 문제를 수정 했습니다.
* `orderByQuery`연속 토큰 버그를 사용 하 여 수정 되었습니다.
* `ChangeFeedProcessor` 파티션을 찾을 수 없을 때 & 파티션 분할 처리를 위한 버그 수정
* `ChangeFeedProcessor` 여러 스레드 간에 임대 업데이트를 동기화 할 때의 버그 수정
* StoreReader에서 예외가 발생 하는 경합 상태를 수정 했습니다. `ArrayIndexOutOfBound`

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.