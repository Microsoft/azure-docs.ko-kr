---
title: Azure Cosmos DB는 SQL Java API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB SQL Java SDK의 각 버전 간 변경 내용을 포함하여 SQL Java API 및 SDK에 대한 모든 것을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 7af43d24b03a4c548925bdca4189fd0524c701a4
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341629"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Java SDK: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

SQL API Java SDK는 동기 작업을 지원합니다. 비동기 지원의 경우 [SQL API 비동기 Java SDK](sql-api-sdk-async-java.md)를 사용하세요. 

| |  |
|---|---|
|**SDK 다운로드**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API 설명서**|[Java API 참조 설명서](/java/api/com.microsoft.azure.documentdb)|
|**SDK에 참여**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**시작**|[Java SDK 시작](sql-api-java-get-started.md)|
|**웹앱 자습서**|[Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-java-application.md)|
|**지원되는 최소 런타임**|[JDK(Java Development Kit) 7 이상](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* 해시 V2에 대 한 PartitionKey의 버그가 수정 되었습니다.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* 복합 인덱스에 대 한 지원이 추가 되었습니다.
* 새로 고침을 강제로 전역 종단점 관리자에 대 한 버그가 수정 되었습니다.
* 직접 모드에서 사전 조건 사용 하 여 업 서 트 버그가 수정 되었습니다.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* 게이트웨이 주소 캐시에 대 한 버그가 수정 되었습니다.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* 직접 모드에 대한 다중 지역 쓰기 지원이 추가되었습니다.
* 프록시에서 ServiceUnavailable 예외로 throw된 IOExceptions 처리에 대한 지원이 추가되었습니다.
* 엔드포인트 검색 다시 시도 정책에서 버그가 수정되었습니다.
* Null 포인터 예외가 BaseDatabaseAccountConfigurationProvider에서 throw되지 않도록 하는 버그가 수정되었습니다.
* 확인 QueryIterator null을 반환 하지 않는 버그가 수정 되었습니다.
* 큰 PartitionKey가 허용되도록 하는 버그가 수정되었습니다.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* 게이트웨이 모드에 대한 다중 지역 쓰기 지원이 추가되었습니다.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* 쿼리에 대한 파티션 읽기 키 범위의 버그가 수정되었습니다.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* DirectHttps 모드에서 연속 토큰 헤더 크기를 설정하는 중에 발생하는 버그가 수정되었습니다.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* 스트리밍 장애 조치(failover) 지원이 추가되었습니다.
* 사용자 지정 메타데이터에 대해 지원이 추가되었습니다.
* 논리를 처리하는 세션이 향상되었습니다.
* 파티션 키 범위 캐시에서 버그를 수정했습니다.
* 직접 모드에서 NPE 버그를 수정했습니다.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* 고유 인덱스에 대한 지원이 추가되었습니다.
* 피드 옵션에서 연속 토큰 크기를 제한할 수 있는 지원이 추가되었습니다.
* Json Serialization의 버그를 수정했습니다(타임스탬프).
* Json Serialization의 버그를 수정했습니다(열거형).
* com.fasterxml.jackson.core:jackson-databind 종속성이 2.9.5로 업그레이드되었습니다.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* 직접 모드에 대한 풀링이 향상되었습니다.
* 비 orderby 교차 파티션 쿼리의 프리페치 성능이 향상되었습니다.
* UUID 생성 기능이 향상되었습니다.
* 세션 일관성 논리가 향상되었습니다.
* multipolygon 지원이 추가되었습니다.
* 컬렉션의 파티션 키 범위 통계에 대한 지원이 추가되었습니다.
* 다중 지역 지원의 버그를 수정했습니다.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* JSON Serialization 성능이 향상되었습니다.
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator 에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Microsoft 친구 라이브러리에 대한 내부 변경입니다.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* 단일 파티션 키 범위 읽기 문제가 해결되었습니다.
* 이름이 짧은 데이터베이스에 영향을 주는 ResourceID 구문 분석 문제가 해결되었습니다.
* 파티션 키 인코딩으로 인한 문제가 해결되었습니다.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* 파티션 분할 동안 요청 처리에 대한 중요한 버그 수정.
* Strong 및 BoundedStaleness 일관성 수준 관련 문제를 수정했습니다.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.
* 세션 모드에서 컬렉션을 읽을 때 발생하는 버그를 수정했습니다.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* 초당 2,500 RU 및 초당 100 RU의 규모 조정 능력을 통해 분할된 컬렉션을 지원합니다.
* 일부 쿼리에서 NullRef 예외를 일으킬 수 있는 네이티브 어셈블리 버그를 수정했습니다.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* 게이트웨이 모드에서 쿼리에 대한 예외를 일으킬 수 있는 쿼리 엔진 구성에서 버그를 수정했습니다.
* 컬렉션을 만든 후 즉시 요청에 대한 "소유자 리소스를 찾을 수 없습니다." 예외를 일으킬 수 있는 세션 컨테이너에 몇 가지 버그가 수정되었습니다.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](sql-query-aggregates.md)을 참조하세요.
* 변경 피드에 대한 지원이 추가되었습니다.
* RequestOptions.setPopulateQuotaInfo를 통한 컬렉션 할당량 정보에 대한 지원이 추가되었습니다.
* RequestOptions.setScriptLoggingEnabled를 통한 저장 프로시저 스크립트에 대한 지원이 추가되었습니다.
* 여기서 DirectHttps 모드의 쿼리 응답 하지 않고 제한 오류가 발생할 때 버그를 수정 했습니다.
* 세션 일관성 모드의 버그를 수정했습니다.
* 요청 속도가 높은 경우 HttpContext에서 NullReferenceException을 유발할 수 있는 버그를 수정했습니다.
* DirectHttps 모드의 성능이 향상되었습니다.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* ConnectionPolicy.setProxy() API와 함께 샘플 클라이언트 인스턴스 기반 프록시 지원이 추가되었습니다.
* DocumentClient 인스턴스를 올바르게 종료하기 위한 DocumentClient.close() API가 추가되었습니다.
* 게이트웨이 대신 네이티브 어셈블리에서 쿼리 계획을 파생하여 직접 연결 모드의 쿼리 성능을 개선했습니다.
* 사용자가 POJO에서 JsonIgnoreProperties를 정의할 필요가 없도록 FAIL_ON_UNKNOWN_PROPERTIES = false로 설정했습니다.
* SLF4J를 사용하도록 로깅을 리팩터링했습니다.
* 일관성 판독기의 몇 가지 버그를 수정했습니다.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* 직접 연결 모드에서 연결 누수를 방지하기 위해 연결 관리의 버그를 수정했습니다.
* NullReference 예외가 throw 될 수 있는 상위 쿼리의 버그를 수정했습니다.
* 내부 캐시에 대한 네트워크 호출의 수를 줄여 성능을 개선했습니다.
* DocumentClientException에 상태 코드 ActivityID 및 요청 URI를 추가하여 문제 해결을 개선했습니다.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* 연결 관리의 문제를 해결하여 안정성을 높였습니다.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* BoundedStaleness 일관성 수준에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 CRUD 작업에 대한 직접 연결 지원이 추가되었습니다.
* SQL을 사용하여 데이터베이스를 쿼리할 때의 버그가 수정되었습니다.
* 세션 토큰이 잘못 설정된 경우에 발생할 수 있는 세션 캐시의 버그가 수정되었습니다.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.
* 강력한 일관성에 대한 지원이 추가되었습니다.
* 직접 연결을 사용할 때 이름 기반 요청에 대한 지원이 추가되었습니다.
* 모든 요청 다시 시도 간에 ActivityId가 일관성을 유지하도록 수정되었습니다.
* 동일한 이름의 컬렉션을 다시 만들 때 세션 캐시와 관련된 버그가 수정되었습니다.
* 지역 펜싱 공간 쿼리에 대한 컬렉션 인덱싱 정책을 지정하는 동안 Polygon 및 LineString 데이터 형식이 추가되었습니다.
* Java 1.8용 Java 문서 관련 문제가 해결되었습니다.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* PartitionKeyDefinitionMap에서 버그를 수정하여 단일 파티션 컬렉션을 캐시하고 추가 인출 파티션 키를 요청하지 않았습니다.
* 잘못된 파티션 키 값이 제공되는 경우 버그가 다시 시도되지 않도록 수정했습니다.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 최대 재시도 횟수와 최대 재시도 대기 시간을 지정하는 옵션과 함께 정제된 요청의 자동 재시도 지원이 추가되었습니다.  RetryOptions와 ConnectionPolicy.getRetryOptions()을 참조하세요.
* IPartitionResolver 기반 사용자 지정 파티셔닝 코드의 사용이 중단되었습니다. 보다 큰 저장소 및 처리량에는 파티션된 컬렉션을 사용하세요.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* 속도 제한에 대한 재시도 정책 지원이 추가되었습니다.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* 다른 SDK와 일치하도록 little-endian의 해시 값을 생성하는 HashPartitionResolver의 버그를 수정합니다.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* 여러 파티션 간의 애플리케이션 분할을 지원하기 위해 해시 및 범위 파티션 해결 프로그램을 추가합니다.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert를 구현합니다. 새로운 upsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
* ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 지리 공간 인덱스 지원
* 모든 리소스에 대 한 ID 속성의 유효성을 검사 합니다. 리소스에 대한 ID는 ?, /, #, \, 문자를 포함하거나 공백으로 끝날 수 없습니다.
* 새 헤더 "인덱스 변환 진행률"을 ResourceResponse에 추가합니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* V2 인덱싱 정책 구현

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

> [!WARNING]
> 모든 버전 **1.x** SQL SDK for Java의 일에 종료 됩니다 **2020 년 5 월 30**합니다.
> 
>

> [!WARNING]
> **1.0.0** 이전 버전의 Java에 대한 모든 버전의 SQL SDK는 **2016년 2월 29일**에 사용 중지됩니다.
> 
> 

<br/>

| Version | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.1.3](#2.1.3) |2018 년 3 월 13 일 |--- |
| [2.1.2](#2.1.2) |2018 년 3 월 9 일 |--- |
| [2.1.1](#2.1.1) |2018 년 12 월 13 일 |--- |
| [2.1.0](#2.1.0) |2018 년 11 월 20 일 |--- |
| [2.0.0](#2.0.0) |2018년 9월 21일 |--- |
| [1.16.4](#1.16.4) |2018년 9월 10일 |2020 년 5 월 30 |
| [1.16.3](#1.16.3) |2018년 9월 9일 |2020 년 5 월 30 |
| [1.16.2](#1.16.2) |2018년 6월 29일 |2020 년 5 월 30 |
| [1.16.1](#1.16.1) |2018년 5월 16일 |2020 년 5 월 30 |
| [1.16.0](#1.16.0) |2018년 3월 15일 |2020 년 5 월 30 |
| [1.15.0](#1.15.0) |2017년 11월 14일 |2020 년 5 월 30 |
| [1.14.0](#1.14.0) |2017년 10월 28일 |2020 년 5 월 30 |
| [1.13.0](#1.13.0) |2017년 8월 25일 |2020 년 5 월 30 |
| [1.12.0](#1.12.0) |2017년 7월 11일 |2020 년 5 월 30 |
| [1.11.0](#1.11.0) |2017년 5월 10일 |2020 년 5 월 30 |
| [1.10.0](#1.10.0) |2017년 3월 11일 |2020 년 5 월 30 |
| [1.9.6](#1.9.6) |2017년 2월 21일 |2020 년 5 월 30 |
| [1.9.5](#1.9.5) |2017년 1월 31일 |2020 년 5 월 30 |
| [1.9.4](#1.9.4) |2016년 11월 24일 |2020 년 5 월 30 |
| [1.9.3](#1.9.3) |2016년 10월 30일 |2020 년 5 월 30 |
| [1.9.2](#1.9.2) |2016년 10월 28일 |2020 년 5 월 30 |
| [1.9.1](#1.9.1) |2016년 10월 26일 |2020 년 5 월 30 |
| [1.9.0](#1.9.0) |2016년 10월 3일 |2020 년 5 월 30 |
| [1.8.1](#1.8.1) |2016년 6월 30일 |2020 년 5 월 30 |
| [1.8.0](#1.8.0) |2016년 6월 14일 |2020 년 5 월 30 |
| [1.7.1](#1.7.1) |2016년 4월 30일 |2020 년 5 월 30 |
| [1.7.0](#1.7.0) |2016년 4월 27일 |2020 년 5 월 30 |
| [1.6.0](#1.6.0) |2016년 3월 29일 |2020 년 5 월 30 |
| [1.5.1](#1.5.1) |2015년 12월 31일 |2020 년 5 월 30 |
| [1.5.0](#1.5.0) |2015년 12월 4일 |2020 년 5 월 30 |
| [1.4.0](#1.4.0) |2015년 10월 5일 |2020 년 5 월 30 |
| [1.3.0](#1.3.0) |2015년 10월 5일 |2020 년 5 월 30 |
| [1.2.0](#1.2.0) |2015년 8월 5일 |2020 년 5 월 30 |
| [1.1.0](#1.1.0) |2015년 7월 9일 |2020 년 5 월 30 |
| 1.0.1 |2015년 5월 12일 |2020 년 5 월 30 |
| [1.0.0](#1.0.0) |2015년 4월 7일 |2020 년 5 월 30 |
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

