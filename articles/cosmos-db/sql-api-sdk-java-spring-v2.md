---
title: SQL API 릴리스 정보 및 리소스에 대 한 스프링 데이터 Azure Cosmos DB v2
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Async Java SDK의 각 버전 간의 변경 내용을 포함 하 여 SQL API 용 스프링 데이터 Azure Cosmos DB v2에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 47d9a8ff884e29dc5692c97d5e7867a856d01063
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590748"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>Core (SQL) API에 대 한 스프링 데이터 Azure Cosmos DB v2: 릴리스 정보 및 리소스
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
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

Core 용 스프링 데이터 Azure Cosmos DB v2 (SQL)를 사용 하 여 개발자는 스프링 응용 프로그램에서 Azure Cosmos DB를 활용할 수 있습니다. 스프링 데이터 Azure Cosmos DB는 데이터베이스 및 컬렉션을 조작 하 고, 문서 작업을 수행 하 고, 쿼리를 실행 하기 위한 스프링 데이터 인터페이스를 제공 합니다. 동기화 및 비동기 (대응식) Api는 모두 동일한 Maven 아티팩트에서 지원 됩니다. 

[스프링 프레임 워크](https://spring.io/projects/spring-framework) 는 Java 응용 프로그램 개발을 간소화 하는 프로그래밍 및 구성 모델입니다. 조직의 웹 사이트를 인용 하기 위해 스프링은 종속성 주입을 사용 하 여 응용 프로그램의 "배관"을 간소화 합니다. 응용 프로그램 빌드 및 테스트로 인해 스프링과 같은 많은 개발자 들이 더욱 간편 합니다. [스프링 부팅](https://spring.io/projects/spring-boot) 은 웹 응용 프로그램 및 마이크로 서비스 개발을 중심으로 하는이를 처리 하는 이러한 아이디어를 확장 합니다. [스프링 데이터](https://spring.io/projects/spring-data) 는 스프링 또는 스프링 부팅 응용 프로그램의 컨텍스트에서 Azure Cosmos DB와 같은 데이터 저장소에 액세스 하기 위한 프로그래밍 모델입니다. 

[Azure 스프링 클라우드](https://azure.microsoft.com/services/spring-cloud/) 응용 프로그램에서 스프링 데이터 Azure Cosmos DB를 사용할 수 있습니다.

> [!IMPORTANT]  
> 이러한 릴리스 정보는 v 2의 스프링 데이터 Azure Cosmos DB입니다. [여기](sql-api-sdk-java-spring-v3.md)에서 v3 릴리스 정보를 찾을 수 있습니다. 
>
> 스프링 데이터 Azure Cosmos DB는 SQL API만 지원 합니다.
>
> 다음 가이드에서는 다른 Azure Cosmos DB Api에 대 한 스프링 데이터를 지원 합니다.
> * [Azure Cosmos DB에서 Apache Cassandra에 대 한 스프링 데이터](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 MongoDB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB로 스프링 데이터 Gremlin](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> 신속 하 게 진행 하 고 싶으세요?
> 1. SDK를 사용할 수 있도록 [지원되는 최소 Java 런타임 JDK 8](/java/azure/jdk/?view=azure-java-stable)을 설치합니다.
> 2. 시작을 사용 하 여 스프링 데이터 Azure Cosmos DB [앱을 만듭니다.](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)
> 3. 기본 Azure Cosmos DB 요청을 안내 하는 [스프링 데이터 Azure Cosmos DB 개발자 가이드를 참조](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) 하세요.
>
> 스프링 [Inr](https://start.spring.io/)을 사용 하 여 스프링 부팅 시작 앱을 빠르게 회전할 수 있습니다.
>

## <a name="helpful-content"></a>유용한 내용

| 콘텐츠 | 링크 |
|---|---|
| **SDK 다운로드** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 설명서** | [스프링 데이터 Azure Cosmos DB 참조 설명서]() |
|**SDK에 참여** | [GitHub의 스프링 데이터 Azure Cosmos DB 리포지토리](https://github.com/microsoft/spring-data-cosmosdb) | 
|**스프링 부팅 스타터**| [Java 용 Azure Cosmos DB 스프링 부팅 스타터 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Azure Cosmos DB를 사용 하는 스프링 TODO 앱 샘플**| [App Service Linux에서 종단 간 Java 환경 (2 부)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**개발자 가이드** | [스프링 데이터 Azure Cosmos DB 개발자 가이드](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**시작 사용 가이드** | [Azure Cosmos DB SQL API에서 Spring Boot Starter를 사용하는 방법](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Azure 스프링 부팅 스타터 Cosmos DB 용 GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**App Services 사용 예제** | [Azure App Service on Linux를 통해 Spring 및 Cosmos DB를 사용하는 방법](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 앱 샘플](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>릴리스 기록

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>새로운 기능
* 2.3.0로 스프링 부팅 버전 업데이트 
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>새로운 기능
* Azure Cosmos DB 버전을 v 3.7.3로 업데이트 함
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* Cosmos SDK v 3.7.3의 메모리 누수 수정 및 netty 버전 업그레이드를 포함 합니다. 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* CosmosDbConfig에서 고려 하도록 allowTelemetry 분석 플래그를 수정 했습니다.
* 컨테이너의 TTL 속성을 고정 했습니다.

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>새로운 기능
* 파티션 키 API로 새 findAll을 추가 했습니다.
* Azure-cosmos 버전을 3.7.0로 업데이트 함
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* CollectionName-> containerName 고정
* > domainClass domainType & entityClass 고정
* "입력 엔터티 대신 리포지토리에 저장 된 반환 엔터티 컬렉션"을 수정 했습니다.

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "입력 엔터티 대신 리포지토리에 저장 된 반환 엔터티 컬렉션"에 대 한 수정

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>새로운 기능
* Azure-cosmos 버전을 v 3.6.0로 업데이트 함
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>새로운 기능
* Cosmos DB SDK 버전을 3.5.0로 업데이트 함
* 자동 생성 컬렉션을 사용/사용 하지 않도록 설정 하는 주석 필드가 추가 됨
* 향상 된 예외 처리, CosmosDBAccessException을 통해 CosmosClientException 노출
* ResponseDiagnostics를 통해 노출 된 requestCharge 및 활동 id
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* SDK 3.5.0 업데이트는 "Cosmos DB HTTP 응답 헤더가 8192 바이트 보다 큰 경우" 예외를 수정 합니다. "ConsistencyPolicy defaultConsistencyLevel ()는 제한 된 부실 접두사에서 실패 합니다."
* 예외를 throw 하는 대신 findById Api 동작을 수정 하 고, 찾을 수 없는 경우 빈 반환
* CosmosPageRequest를 사용 하는 경우 다음 페이지에 정렬이 적용 되지 않은 버그가 수정 되었습니다.

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>새로운 기능
* 자동 생성 컬렉션을 사용/사용 하지 않도록 설정 하는 주석 필드가 추가 됨
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 예외를 throw 하는 대신 findById Api 동작을 수정 하 고, 찾을 수 없는 경우 빈 반환

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>새로운 기능
* 사후 Cosmos 리포지토리 지원 완료
* Cosmos DB 요청 진단 문자열 및 쿼리 메트릭 지원
* 3.3.1에 SDK 버전 업데이트 Cosmos DB
* 5.2.0로의 스프링 프레임 워크 버전 업그레이드
* 2.2.0로 스프링 데이터 Commons 버전 업그레이드
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey Api 추가
* Azure에서 종속성을 제거 했습니다. doumentdb
* Cosmos에 대 한 DocumentDb
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "PageSize가 리포지토리의 전체 항목 보다 작은 경우 정렬 시 예외가 throw 됨"이 수정 됨

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>새로운 기능
* 사용 중단 문서 DB Api
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey Api가 추가 되었습니다.
* _Etag에 따라 낙관적 잠금이 추가 됨
* 문서 컬렉션 이름에 대해 SPeL 식 사용
* ObjectMapper 기능이 향상 되었습니다.
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>새로운 기능
* Cosmos SDK v3 종속성이 추가 됨
* 사후 Cosmos 리포지토리가 추가 됨
* Cosmos SDK v3를 사용 하도록 DocumentDbTemplate 구현을 업데이트 했습니다.
* 사후 Cosmos 리포지토리 지원에 대 한 기타 구성 변경 내용
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* ApplicationInsights 종속성 제거
    * 종속성 polluting 잠재적 위험
    * Java 11 비호환
    * CPU 및/또는 메모리에 대 한 잠재적 성능 저하를 방지 합니다.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>새로운 기능
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* Backport는 다음에 대 한 applicationInsights 종속성을 제거 합니다.
    * 종속성 polluting 잠재적 위험
    * Java 11 비호환
    * CPU 및/또는 메모리에 대 한 잠재적 성능 저하를 방지 합니다.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>새로운 기능
* 마스터 버전을 2.1.1로 업데이트
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>새로운 기능
* 원격 분석의 모든 예외 무시
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>새로운 기능
* 버전을 2.1.0로 업데이트 하 여 문제 해결
#### <a name="key-bug-fixes"></a>핵심 버그 수정

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>새로운 기능
* Add 키워드가 exists, startsWith
* 추가 정보 업데이트
#### <a name="key-bug-fixes"></a>핵심 버그 수정
* "엔터티에 대해 직접 자체 href를 호출할 없음" 수정
* "수집이 생성 되지 않으면 findAll이 실패 함" 수정

### <a name="204-pre-release-2018-08-23"></a>2.0.4 이상을 (시험판) (2018-08-23)
#### <a name="new-features"></a>새로운 기능
* Documentdb에서 cosmosdb로 패키지 이름 바꾸기
* 쿼리 메서드 키워드의 새로운 기능인 Sql API에서 지원 되는 16 개 키워드를 추가 합니다.
* 페이징 및 정렬을 사용 하 여 쿼리의 새 기능을 추가 합니다.
* 스프링 데이터 cosmosdb의 구성을 간소화 합니다.
* DeleteCollection 및 deleteAll API를 추가 합니다.

#### <a name="key-bug-fixes"></a>핵심 버그 수정
* 버그 수정 및 결함 향상.

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

스프링 프레임 워크에 대 한 자세한 내용은 [프로젝트 홈 페이지](https://spring.io/projects/spring-framework)를 참조 하세요.

스프링 부팅에 대해 자세히 알아보려면 [프로젝트 홈 페이지](https://spring.io/projects/spring-boot)를 참조 하세요.

스프링 데이터에 대해 자세히 알아보려면 [프로젝트 홈 페이지](https://spring.io/projects/spring-data)를 참조 하세요.