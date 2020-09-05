---
title: Azure Cosmos DB는 SQL Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Java SDK의 각 버전 간 변경 내용을 포함하여 SQL Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 406690eb1303a25002d3d787e746bd091dd83fc6
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586132"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Java SDK: 릴리스 정보 및 리소스
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

이는 동기화 작업을 지원하는 SQL API용 원본 Azure Cosmos DB Sync Java SDK v2입니다.

> [!IMPORTANT]  
> Azure Cosmos DB의 최신 Java SDK가 *아닙니다*. 프로젝트에 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)를 사용하는 것이 좋습니다. 업그레이드하려면 [Azure Cosmos DB Java SDK v4로 마이그레이션하기](migrate-java-v4-sdk.md) 안내서 및 [Reactor vs RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 안내서의 지침을 따르세요. 
>

| |  |
|---|---|
|**SDK 다운로드**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API 설명서**|[Java API 참조 설명서](/java/api/com.microsoft.azure.documentdb)|
|**SDK에 참여**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**시작**|[Java SDK 시작](sql-api-java-get-started.md)|
|**웹앱 자습서**|[Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-java-application.md)|
|**지원되는 최소 런타임**|[JDK(Java Development Kit) 7 이상](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>릴리스 정보

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* DocumentCollection 쿼리에서 마스터 파티션 캐시 문제를 수정 합니다.

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* 449 다시 시도 사용자 지정 구성에 대 한 지원이 추가 되었습니다.

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* 연결 풀 제한 시간 문제를 수정합니다.
* 내부에서 다시 시도할 때 인증 토큰 새로 고침을 수정합니다.

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* databaseAccount의 올바른 클라이언트 쪽 복제본 정책 태그를 업데이트하고 캐시에서 databaseAccount 구성을 읽었습니다.

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* 사용자가 pkRangeId를 제공하는 경우 잘못된 파티션 키 범위 오류에 대한 재시도를 방지합니다.

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* 파티션 키 범위 캐시 새로 고침이 최적화되었습니다.
* SDK가 서버의 파티션 분할 힌트를 입력하지 않아 클라이언트 쪽 라우팅 캐시 새로 고침이 잘못 수행되는 시나리오를 수정합니다.

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* 컬렉션 캐시 새로 고침이 최적화되었습니다.

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* 요청 진단 문자열에서 내부 예외 메시지를 검색하는 지원이 추가되었습니다.

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* PartitionKeyDefinition에 버전 API가 도입되었습니다.

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* 직접 모드에 대해 별도의 시간 제한 지원이 추가되었습니다.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* 서비스에서 null 오류 메시지를 사용하고 문서 클라이언트 예외를 생성합니다.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* 소켓 연결이 향상되었으며 SoKeepAlive 기본값 true를 추가했습니다.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* 요청 진단 문자열 지원이 추가되었습니다.

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* Hash V2에 대한 PartitionKey의 버그가 수정되었습니다.

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* 복합 인덱스에 대한 지원이 추가되었습니다.
* 전역 엔드포인트 관리자에서 새로 고침이 강제로 수행되는 버그가 수정되었습니다.
* 직접 모드에서 사전 조건이 있는 upsert에 대한 버그가 수정되었습니다.

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* 게이트웨이 주소 캐시의 버그가 수정되었습니다.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* 직접 모드에 대한 다중 지역 쓰기 지원이 추가되었습니다.
* 프록시에서 ServiceUnavailable 예외로 throw된 IOExceptions 처리에 대한 지원이 추가되었습니다.
* 엔드포인트 검색 다시 시도 정책에서 버그가 수정되었습니다.
* Null 포인터 예외가 BaseDatabaseAccountConfigurationProvider에서 throw되지 않도록 하는 버그가 수정되었습니다.
* QueryIterator가 null을 반환하지 않도록 하는 버그가 수정되었습니다.
* 큰 PartitionKey가 허용되도록 하는 버그가 수정되었습니다.

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* 게이트웨이 모드에 대한 다중 지역 쓰기 지원이 추가되었습니다.

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* 쿼리에 대한 파티션 읽기 키 범위의 버그가 수정되었습니다.

### <a name="1163"></a><a name="1.16.3"></a>1.16.3
* DirectHttps 모드에서 연속 토큰 헤더 크기를 설정하는 중에 발생하는 버그가 수정되었습니다.

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* 스트리밍 장애 조치(failover) 지원이 추가되었습니다.
* 사용자 지정 메타데이터에 대해 지원이 추가되었습니다.
* 논리를 처리하는 세션이 향상되었습니다.
* 파티션 키 범위 캐시에서 버그를 수정했습니다.
* 직접 모드에서 NPE 버그를 수정했습니다.

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* 고유 인덱스에 대한 지원이 추가되었습니다.
* 피드 옵션에서 연속 토큰 크기를 제한할 수 있는 지원이 추가되었습니다.
* Json Serialization의 버그를 수정했습니다(타임스탬프).
* Json Serialization의 버그를 수정했습니다(열거형).
* com.fasterxml.jackson.core:jackson-databind 종속성이 2.9.5로 업그레이드되었습니다.

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* 직접 모드에 대한 풀링이 향상되었습니다.
* 비 orderby 교차 파티션 쿼리의 프리페치 성능이 향상되었습니다.
* UUID 생성 기능이 향상되었습니다.
* 세션 일관성 논리가 향상되었습니다.
* multipolygon 지원이 추가되었습니다.
* 컬렉션의 파티션 키 범위 통계에 대한 지원이 추가되었습니다.
* 다중 지역 지원의 버그를 수정했습니다.

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* JSON Serialization 성능이 향상되었습니다.
* 이 SDK 버전을 사용하려면 최신 버전의 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)가 필요합니다.

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Microsoft 친구 라이브러리에 대한 내부 변경입니다.

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* 단일 파티션 키 범위 읽기 문제가 해결되었습니다.
* 이름이 짧은 데이터베이스에 영향을 주는 ResourceID 구문 분석 문제가 해결되었습니다.
* 파티션 키 인코딩으로 인한 문제가 해결되었습니다.

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* 파티션 분할 동안 요청 처리에 대한 중요한 버그 수정.
* Strong 및 BoundedStaleness 일관성 수준 관련 문제를 수정했습니다.

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.
* 세션 모드에서 컬렉션을 읽을 때 발생하는 버그를 수정했습니다.

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* 초당 2,500 RU 및 초당 100 RU의 규모 조정 능력을 통해 분할된 컬렉션을 지원합니다.
* 일부 쿼리에서 NullRef 예외를 일으킬 수 있는 네이티브 어셈블리 버그를 수정했습니다.

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* 게이트웨이 모드에서 쿼리에 대한 예외를 일으킬 수 있는 쿼리 엔진 구성에서 버그를 수정했습니다.
* 컬렉션을 만든 후 즉시 요청에 대한 "소유자 리소스를 찾을 수 없습니다." 예외를 일으킬 수 있는 세션 컨테이너에 몇 가지 버그가 수정되었습니다.

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](sql-query-aggregates.md)을 참조하세요.
* 변경 피드에 대한 지원이 추가되었습니다.
* RequestOptions.setPopulateQuotaInfo를 통한 컬렉션 할당량 정보에 대한 지원이 추가되었습니다.
* RequestOptions.setScriptLoggingEnabled를 통한 저장 프로시저 스크립트에 대한 지원이 추가되었습니다.
* 제한 오류가 발생할 때 DirectHttps 모드의 쿼리의 응답이 중지될 수 있는 버그가 수정되었습니다.
* 세션 일관성 모드의 버그를 수정했습니다.
* 요청 속도가 높은 경우 HttpContext에서 NullReferenceException을 유발할 수 있는 버그를 수정했습니다.
* DirectHttps 모드의 성능이 향상되었습니다.

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* ConnectionPolicy.setProxy() API와 함께 샘플 클라이언트 인스턴스 기반 프록시 지원이 추가되었습니다.
* DocumentClient 인스턴스를 올바르게 종료하기 위한 DocumentClient.close() API가 추가되었습니다.
* 게이트웨이 대신 네이티브 어셈블리에서 쿼리 계획을 파생하여 직접 연결 모드의 쿼리 성능을 개선했습니다.
* 사용자가 POJO에서 JsonIgnoreProperties를 정의할 필요가 없도록 FAIL_ON_UNKNOWN_PROPERTIES = false로 설정했습니다.
* SLF4J를 사용하도록 로깅을 리팩터링했습니다.
* 일관성 판독기의 몇 가지 버그를 수정했습니다.

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* 직접 연결 모드에서 연결 누수를 방지하기 위해 연결 관리의 버그를 수정했습니다.
* NullReference 예외가 throw 될 수 있는 상위 쿼리의 버그를 수정했습니다.
* 내부 캐시에 대한 네트워크 호출의 수를 줄여 성능을 개선했습니다.
* DocumentClientException에 상태 코드 ActivityID 및 요청 URI를 추가하여 문제 해결을 개선했습니다.

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* 연결 관리의 문제를 해결하여 안정성을 높였습니다.

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* BoundedStaleness 일관성 수준에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 CRUD 작업에 대한 직접 연결 지원이 추가되었습니다.
* SQL을 사용하여 데이터베이스를 쿼리할 때의 버그가 수정되었습니다.
* 세션 토큰이 잘못 설정된 경우에 발생할 수 있는 세션 캐시의 버그가 수정되었습니다.

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.
* 강력한 일관성에 대한 지원이 추가되었습니다.
* 직접 연결을 사용할 때 이름 기반 요청에 대한 지원이 추가되었습니다.
* 모든 요청 다시 시도 간에 ActivityId가 일관성을 유지하도록 수정되었습니다.
* 동일한 이름의 컬렉션을 다시 만들 때 세션 캐시와 관련된 버그가 수정되었습니다.
* 지역 펜싱 공간 쿼리에 대한 컬렉션 인덱싱 정책을 지정하는 동안 Polygon 및 LineString 데이터 형식이 추가되었습니다.
* Java 1.8용 Java 문서 관련 문제가 해결되었습니다.

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* PartitionKeyDefinitionMap에서 버그를 수정하여 단일 파티션 컬렉션을 캐시하고 추가 인출 파티션 키를 요청하지 않았습니다.
* 잘못된 파티션 키 값이 제공되는 경우 버그가 다시 시도되지 않도록 수정했습니다.

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 최대 재시도 횟수와 최대 재시도 대기 시간을 지정하는 옵션과 함께 정제된 요청의 자동 재시도 지원이 추가되었습니다.  RetryOptions와 ConnectionPolicy.getRetryOptions()을 참조하세요.
* IPartitionResolver 기반 사용자 지정 파티셔닝 코드의 사용이 중단되었습니다. 보다 큰 스토리지 및 처리량에는 파티션된 컬렉션을 사용하세요.

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* 속도 제한에 대한 재시도 정책 지원이 추가되었습니다.  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다.

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* 다른 SDK와 일치하도록 little-endian의 해시 값을 생성하는 HashPartitionResolver의 버그를 수정합니다.

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* 여러 파티션 간의 애플리케이션 분할을 지원하기 위해 해시 및 범위 파티션 해결 프로그램을 추가합니다.

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* Upsert를 구현합니다. 새로운 upsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
* ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 지리 공간 인덱스 지원
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 ?, /, #, \, 문자를 포함하거나 공백으로 끝날 수 없습니다.
* ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* V2 인덱싱 정책 구현

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜

Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다. 새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

> [!WARNING]
> 30 년 5 월 2020, Azure Cosmos DB는 더 이상 버그를 수정 하 고, 새 기능을 추가 하 고, Azure Cosmos DB Java SDK for SQL API의 버전 2.x에 대 한 지원을 제공 합니다. 업그레이드 하지 않으려는 경우 SDK의 버전 1.x에서 전송 된 요청은 Azure Cosmos DB 서비스에서 계속 제공 됩니다.
>
> 2016 년 2 월 29 일부 터 Azure Cosmos DB는 더 이상 버그를 수정 하지 않으며, 새 기능을 추가 하 고, Azure Cosmos DB Java SDK for SQL API의 버전 2.x에 대 한 지원을 제공 합니다. 업그레이드 하지 않으려는 경우 SDK의 버전 0. x에서 전송 된 요청은 Azure Cosmos DB 서비스에서 계속 제공 됩니다.


| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.5.1](#2.5.1) |June 03, 2020 |--- |
| [2.5.0](#2.5.0) |2020년 5월 12일 |--- |
| [2.4.7](#2.4.7) |2020년 2월 20일 |--- |
| [2.4.6](#2.4.6) |2020년 1월 24일 |--- |
| [2.4.5](#2.4.5) |2019년 11월 10일 |--- |
| [2.4.4](#2.4.4) |2019년 10월 24일 |--- |
| [2.4.2](#2.4.2) |2019년 9월 26일 |--- |
| [2.4.1](#2.4.1) |2019년 7월 18일 |--- |
| [2.4.0](#2.4.0) |2019년 5월 4일 |--- |
| [2.3.0](#2.3.0) |2019년 4월 24일 |--- |
| [2.2.3](#2.2.3) |2019년 4월 16일 |--- |
| [2.2.2](#2.2.2) |2019년 4월 5일 |--- |
| [2.2.0](#2.2.0) |2019년 3월 27일 |--- |
| [2.1.3](#2.1.3) |2019년 3월 13일 |--- |
| [2.1.2](#2.1.2) |2019년 3월 9일 |--- |
| [2.1.1](#2.1.1) |2018년 12월 13일 |--- |
| [2.1.0](#2.1.0) |2018년 11월 20일 |--- |
| [2.0.0](#2.0.0) |2018년 9월 21일 |--- |
| [1.16.4](#1.16.4) |2018년 9월 10일 |2020년 5월 30일 |
| [1.16.3](#1.16.3) |2018년 9월 9일 |2020년 5월 30일 |
| [1.16.2](#1.16.2) |2018년 6월 29일 |2020년 5월 30일 |
| [1.16.1](#1.16.1) |2018년 5월 16일 |2020년 5월 30일 |
| [1.16.0](#1.16.0) |2018년 3월 15일 |2020년 5월 30일 |
| [1.15.0](#1.15.0) |2017년 11월 14일 |2020년 5월 30일 |
| [1.14.0](#1.14.0) |2017년 10월 28일 |2020년 5월 30일 |
| [1.13.0](#1.13.0) |2017년 8월 25일 |2020년 5월 30일 |
| [1.12.0](#1.12.0) |2017년 7월 11일 |2020년 5월 30일 |
| [1.11.0](#1.11.0) |2017년 5월 10일 |2020년 5월 30일 |
| [1.10.0](#1.10.0) |2017년 3월 11일 |2020년 5월 30일 |
| [1.9.6](#1.9.6) |2017년 2월 21일 |2020년 5월 30일 |
| [1.9.5](#1.9.5) |2017년 1월 31일 |2020년 5월 30일 |
| [1.9.4](#1.9.4) |2016년 11월 24일 |2020년 5월 30일 |
| [1.9.3](#1.9.3) |2016년 10월 30일 |2020년 5월 30일 |
| [1.9.2](#1.9.2) |2016년 10월 28일 |2020년 5월 30일 |
| [1.9.1](#1.9.1) |2016년 10월 26일 |2020년 5월 30일 |
| [1.9.0](#1.9.0) |2016년 10월 3일 |2020년 5월 30일 |
| [1.8.1](#1.8.1) |2016년 6월 30일 |2020년 5월 30일 |
| [1.8.0](#1.8.0) |2016년 6월 14일 |2020년 5월 30일 |
| [1.7.1](#1.7.1) |2016년 4월 30일 |2020년 5월 30일 |
| [1.7.0](#1.7.0) |2016년 4월 27일 |2020년 5월 30일 |
| [1.6.0](#1.6.0) |2016년 3월 29일 |2020년 5월 30일 |
| [1.5.1](#1.5.1) |2015년 12월 31일 |2020년 5월 30일 |
| [1.5.0](#1.5.0) |2015년 12월 4일 |2020년 5월 30일 |
| [1.4.0](#1.4.0) |2015년 10월 5일 |2020년 5월 30일 |
| [1.3.0](#1.3.0) |2015년 10월 5일 |2020년 5월 30일 |
| [1.2.0](#1.2.0) |2015년 8월 5일 |2020년 5월 30일 |
| [1.1.0](#1.1.0) |2015년 7월 9일 |2020년 5월 30일 |
| 1.0.1 |2015년 5월 12일 |2020년 5월 30일 |
| [1.0.0](#1.0.0) |2015년 4월 7일 |2020년 5월 30일 |
| 0.9.5-prelease |2015년 3월 9일 |2016년 2월 29일 |
| 0.9.4-prelease |2015년 2월 17일 |2016년 2월 29일 |
| 0.9.3-prelease |2015년 1월 13일 |2016년 2월 29일 |
| 0.9.2-prelease |2014년 12월 19일 |2016년 2월 29일 |
| 0.9.1-prelease |2014년 12월 19일 |2016년 2월 29일 |
| 0.9.0-prelease |2014년 12월 10일 |2016년 2월 29일 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

