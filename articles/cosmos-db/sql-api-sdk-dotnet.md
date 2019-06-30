---
title: Azure Cosmos DB는 SQL .NET API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB .NET SDK의 각 버전 간 변경 내용을 포함하여 SQL .NET API 및 SDK에 대한 모든 것을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: d34da8928e06f04756e24f5cc3e4c430c2892f79
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341579"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>SQL API용 Azure Cosmos DB .NET SDK: 다운로드 및 릴리스 정보
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

| |  |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API 설명서**|[.NET API 참조 설명서](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**샘플**|[.NET 코드 샘플](sql-api-dotnet-samples.md)|
|**시작**|[Azure Cosmos DB .NET SDK 시작](sql-api-get-started.md)|
|**웹앱 자습서**|[Azure Cosmos DB를 사용한 웹 애플리케이션 개발](sql-api-dotnet-application.md)|
|**현재 지원되는 프레임워크**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>릴리스 정보

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-preview
* 공개 미리 보기용 .NET SDK [버전 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)의 미리 보기 1.
* .NET Framework 4.6.1 이상 및 .NET Core 2.0 이상을 지원하는 대상 .NET Standard
* 최상위 CosmosClient 및 관련 CosmosDatabases, CosmosContainers 및 CosmosItems 클래스 간에 분할되는 메서드가 포함된 새 개체 모델 
* 스트림 지원 
* 상태 코드를 반환하고 응답이 반환되지 않을 경우에만 예외를 throw하는 서버에서 업데이트된 CosmosResponseMessage 

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* 빈 페이지를 일으키는 쿼리에 대 한 경합 상태를 추적 하는 수정

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* NuGet 패키지에 정의 된 SDK의 System.Net.Http 버전 일치
* LINQ 쿼리에 대 한 전체 자릿수 크기를 늘렸습니다.
* CompositePath "," CompositePathSortOrder "," SpatialSpec "," SpatialType "및" PartitionKeyDefinitionVersion 새 클래스 추가
* DocumentCollection에 추가 된 TimeToLivePropertyPath
* 추가 CompositeIndexes 및 SpatialIndexes IndexPolicy
* PartitionKeyDefinition에 버전 추가
* PartitionKey를 None 추가

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * 추가 IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection 및 MaxTcpConnectionsPerEndpoint ConnectionPolicy에 있습니다.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* 진단 개선 사항

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* 환경 변수 설정 “POCOSerializationOnly”가 추가되었습니다.

* DocumentDB.Spatial.Sql.dll이 제거되고 Microsoft.Azure.Documents.ServiceInterop.dll에 포함되었습니다.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* StoredProcedure 실행 호출 장애 조치(failover) 중 다시 시도 논리가 개선되었습니다.

* DocumentClientEventSource 싱글톤을 만들었습니다. 

* ConnectionPolicy RequestTimeout을 고려하지 않는 GatewayAddressCache 시간 제한이 수정되었습니다.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* 직접/TCP 전송 진단의 경우 SDK의 내부 예외 유형인 TransportException이 추가되었습니다. 예외 메시지에 포함될 경우 이 유형은 클라이언트 연결 문제를 해결하기 위해 추가 정보를 인쇄합니다.

* HttpClient 요청(예: HttpClientHandler)을 전송하는 데 사용할 HTTP 처리기 스택인 HttpMessageHandler를 가져오는 새 생성자 오버로드가 추가되었습니다.

* Null 값이 있는 헤더가 제대로 처리되지 않은 버그를 수정합니다.

* 컬렉션 캐시 유효성 검사를 개선했습니다.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security가 4.3.2로 업데이트되었습니다.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* 진단 추적 기능 개선

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* 다중 지역 요청 일시적인 오류에 대한 복원력이 강화되었습니다.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* 다중 지역 쓰기 지원이 추가되었습니다.
* TOP 및 MaxBufferedItemCount를 사용하여 파티션 간 쿼리 성능 향상 기능입니다.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* 요청 취소 지원이 추가되었습니다.
* 지역에 따라 기본 설정 위치를 자동으로 채우는 SetCurrentLocation이 ConnectionPolicy에 추가되었습니다.
* 개별 파티션의 문서를 일치시키지 않는 필터 및 Min/Max를 사용한 파티션 간 쿼리의 버그가 수정되었습니다.
* 이제 DocumentClient 메서드에는 IDocumentClient를 포함하는 패리티가 있습니다.
* 설정된 연결 수를 줄이도록 직접 TCP 전송 스택이 업데이트되었습니다.
* 비 Windows 클라이언트에 대한 직접 모드 TCP에 대한 지원이 추가되었습니다.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* 요청 취소 지원이 추가되었습니다.
* 지역에 따라 기본 설정 위치를 자동으로 채우는 SetCurrentLocation이 ConnectionPolicy에 추가되었습니다.
* 개별 파티션의 문서를 일치시키지 않는 필터 및 Min/Max를 사용한 파티션 간 쿼리의 버그가 수정되었습니다.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* 이제 DocumentClient 메서드에는 IDocumentClient를 포함하는 패리티가 있습니다.
* 설정된 연결 수를 줄이도록 직접 TCP 전송 스택이 업데이트되었습니다.
* 비 Windows 클라이언트에 대한 직접 모드 TCP에 대한 지원이 추가되었습니다.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* FeedOptions에 ConsistencyLevel 속성이 추가되었습니다.
* RequestOptions 및 FeedOptions에 JsonSerializerSettings가 추가되었습니다.
* ConnectionPolicy에 EnableReadRequestsFallback이 추가되었습니다.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* 코너 사례에서 쿼리로 파티션 간 순서에 대한 KeyNotFoundException을 수정했습니다.
* LINQ 쿼리에 대한 select 절에 JsonPropery 특성이 적용되지 않았던 버그가 수정되었습니다.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* 세션 일관성 수준을 사용하는 경우 일시적인 “Microsoft.Azure.Documents.NotFoundException: 읽기 세션을 입력 세션 토큰에 사용할 수 없습니다.” 오류가 발생하는 특정 경합 조건에 도달하는 버그가 수정되었습니다.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* FeedOptions.MaxItemCount = -1에서 System.ArithmeticException: 페이지 크기가 음수입니다를 throw하는 회기가 수정되었습니다.
* QueryMetrics에 새 tostring() 함수가 추가되었습니다.
* 컬렉션을 읽을 때 파티션 통계가 노출되었습니다.
* PartitionKey 속성이 ChangeFeedOptions에 추가되었습니다.
* 사소한 버그가 수정되었습니다.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* DocumentCollection에 UniqueKeyPolicy 속성을 사용하여 문서의 고유 인덱스를 지정하는 기능을 추가합니다.
* 일부 쿼리 및 저장 프로시저 실행에 대해 사용자 지정 JsonSerializer 설정이 적용되지 않는 버그가 수정되었습니다.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* API 참조 설명서, 어셈블리의 메타데이터 정보 및 NuGet 패키지에서 Azure DocumentDB를 Azure Cosmos DB로 브랜딩 변경. 
* 직접 연결 모드로 전송된 요청의 응답에서 진단 정보 및 대기 시간을 표시합니다. 속성 이름은 ResourceResponse 클래스의 RequestDiagnosticsString 및 RequestLatency입니다.
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator 에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Microsoft 친구 어셈블리에 대한 내부 변경입니다.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* 여러 가지 안정성 수정 및 향상된 기능이 추가되었습니다.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* 특정 파티션 키 범위 값으로 쿼리 결과의 범위를 지정하기 위해 PartitionKeyRangeId에 대한 지원이 FeedOption으로 추가되었습니다. 
* 해당 시간 이후에 변경 검색을 시작할 수 있도록 StartTime에 대한 지원이 ChangeFeedOption으로 추가되었습니다.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* 스택 오버플로 예외를 일으킬 수 있는 JsonSerializable 클래스의 문제가 수정되었습니다.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   JsonSerializerSettings가 DocumentClient 생성자의 선택적 매개 변수로 도입된 것으로 인해 애플리케이션을 다시 컴파일해야 하는 문제가 수정되었습니다.
* JsonSerializerSettings 매개 변수 전달 시 ConnectionPolicy 및 ConsistencyLevel 매개 변수의 기본값을 허용하도록 마지막 매개 변수로 JsonSerializerSettings가 필요한 DocumentClient 생성자가 더 이상 사용되지 않는 것으로 표시되었습니다.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet)를 인스턴스화하는 동안 사용자 지정 JsonSerializerSettings를 지정하기 위한 지원을 추가하였습니다.

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   SSE4 명령을 지원하지 않고 Azure Cosmos DB SQL 쿼리를 실행할 때 SEHException을 throw하는 X64 컴퓨터에 영향을 준 문제가 해결되었습니다.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.
*   개별 파티션의 쿼리 메트릭에 대한 지원이 추가되었습니다.
*   쿼리의 연속 토큰 크기 제한에 대한 지원이 추가되었습니다.
*   실패한 요청의 자세한 추적에 대한 지원이 추가되었습니다.
*   SDK의 성능이 약간 향상되었습니다.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* 기능적으로 1.13.3과 동일합니다. 내부적으로 약간 변경되었습니다.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* 기능적으로 1.13.2와 동일합니다. 내부적으로 약간 변경되었습니다.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* 집계 쿼리에 대한 FeedOptions에 제공된 PartitionKey 값을 무시하던 문제를 해결했습니다.
* 플라이트 중간의 파티션 간 Order By 쿼리를 실행하는 동안 파티션 관리의 투명한 처리 문제를 해결했습니다.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* ASP.NET 컨텍스트 내에서 사용할 경우 일부 비동기 API에서 교착 상태를 일으키는 문제를 해결했습니다.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* 특정 조건에서 자동 장애 조치(Failover)를 수행하도록 SDK를 좀 더 탄력적으로 만들었습니다.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* 문제를 수정하는 경우 때때로 WebException이 발생합니다. 원격 이름을 확인할 수 없습니다.
* ReadDocumentAsync API에 새 오버로드를 추가하여 형식화된 문서를 직접 읽기 위한 지원을 추가했습니다.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 LINQ 지원이 추가되었습니다.
* 이벤트 처리기를 사용하여 발생한 ConnectionPolicy 개체의 메모리 누수 문제를 해결합니다.
* ETag를 사용할 때 UpsertAttachmentAsync가 작동하지 않는 문제를 해결합니다.
* 문자열 필드를 정렬할 때 파티션 간 order-by 쿼리 연속 작업이 작동하지 않는 문제를 해결합니다.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다. [집계 지원](sql-query-aggregates.md)을 참조하세요.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* 32비트 호스트 프로세스에서 일부 파티션 간 쿼리가 실패하는 문제를 수정했습니다.
* 게이트웨이 모드에서 실패한 요청에 대한 토큰으로 세션 컨테이너를 업데이트하지 않는 문제를 수정했습니다.
* 경우에 따라 프로젝션에서 UDF 호출을 사용하는 쿼리가 실패하는 문제를 수정했습니다.
* 요청의 읽기 및 쓰기 처리량을 높이기 위해 클라이언트 쪽 성능을 수정했습니다.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* 실패한 요청에 대한 토큰으로 세션 컨테이너를 업데이트하지 않는 문제를 수정했습니다.
* 32비트 호스트 프로세스에서 작동하도록 SDK에 대한 지원을 추가했습니다. 파티션 간 쿼리를 사용하는 경우 향상된 성능을 위해 64비트 호스트를 처리하는 것이 좋습니다.
* IN 식에 많은 수의 파티션 키 값을 사용하는 쿼리와 관련된 시나리오의 성능이 향상되었습니다.
* PopulateQuotaInfo 요청 옵션을 설정하는 경우 문서 컬렉션 읽기 요청에 대한 ResourceResponse에서 다양한 리소스 할당량 통계가 채워집니다.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* 1\.11.0에 도입된 CreateDocumentCollectionIfNotExistsAsync API 성능이 약간 향상되었습니다.
* 높은 수준의 동시 요청이 개입되는 시나리오를 위한 SDK의 성능 픽스입니다.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* 컬렉션 내 문서의 [피드 변경](change-feed.md) 프로세스에 대한 새 클래스 및 메서드 지원
* 파티션 간 쿼리 연속 및 파티션 간 쿼리에 대한 일부 성능 향상 지원
* CreateDatabaseIfNotExistsAsync 및 CreateDocumentCollectionIfNotExistsAsync 메서드 추가
* 시스템 함수에 대한 LINQ 지원: IsDefined, IsNull 및 IsPrimitive.
* project.json 도구를 포함한 프로젝트의 NuGet 패키지를 사용할 때 Microsoft.Azure.Documents.ServiceInterop.dll 및 DocumentDB.Spatial.Sql.dll 어셈블리가 자동으로 애플리케이션의 휴지통에 들어가는 현상 수정
* 시나리오를 디버깅하는 데 도움이 될 수 있는 클라이언트 쪽 ETW 추적 내보내기 지원

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* 분할된 컬렉션에 대한 직접 연결 지원이 추가되었습니다.
* 제한된 부실 일관성 수준에 대한 성능이 향상되었습니다.
* 지역 펜싱 공간 쿼리에 대한 컬렉션 인덱싱 정책을 지정하는 동안 Polygon 및 LineString 데이터 형식이 추가되었습니다.
* 조건자를 변환하는 동안 StringEnumConverter, IsoDateTimeConverter 및 UnixDateTimeConverter에 대한 LINQ 지원이 추가되었습니다.
* 다양한 SDK 버그가 수정되었습니다.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* 다음 NotFoundException의 원인이 되는 문제를 수정했습니다. 읽기 세션을 입력 세션 토큰에 사용할 수 없습니다. 이 예외는 지리적으로 분산된 계정의 읽기 영역을 쿼리할 때 가끔 발생합니다.
* 응답에서 내부 스트림에 직접 액세스할 수 있도록 하는 ResourceResponse 클래스의 ResponseStream 속성이 노출되었습니다.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* ResourceResponse, FeedResponse, StoredProcedureResponse 및 MediaResponse 클래스가 해당하는 공용 인터페이스를 구현하도록 수정되어 테스트 지향 배포(TDD)를 위해 모의 수행할 수 있도록 합니다.
* 데이터 직렬화를 위해 사용자 지정 JsonSerializerSettings 개체를 사용할 때 잘못된 파티션 키 헤더가 발생하는 문제가 수정되었습니다.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* 오래 실행하는 쿼리가 오류와 함께 실패하게 한 문제를 해결했습니다. 인증 토큰은 현재 시간에 유효하지 않습니다.
* 크로스 파티션 위쪽/order-by 쿼리에서 원래 SqlParameterCollection을 제거하는 문제를 해결했습니다.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* 분할된 컬렉션에 대한 병렬 쿼리에 지원을 추가했습니다.
* 분할된 컬렉션에 대한 파티션 간 Order By 및 TOP 쿼리에 대한 지원을 추가했습니다.
* Azure Cosmos DB Nuget 패키지에 대한 참조와 함께 Azure Cosmos DB 프로젝트를 참조할 때 필요한 DocumentDB.Spatial.Sql.dll 및 Microsoft.Azure.Documents.ServiceInterop.dll에 대한 누락된 참조를 수정했습니다.
* LINQ에서 사용자 정의 함수를 사용할 때 여러 유형의 매개 변수를 사용하도록 기능을 수정했습니다. 
* 쓰기 위치 대신 읽기 위치에 Upsert 호출을 전달하는 전역적으로 복제된 계정에 대한 버그를 수정했습니다.
* IDocumentClient 인터페이스에 누락된 다음 메서드를 추가했습니다. 
  * mediaStream 및 options를 매개 변수로 사용하는 UpsertAttachmentAsync 메서드
  * options를 매개 변수로 사용하는 CreateAttachmentAsync 메서드
  * querySpec을 매개 변수로 사용하는 CreateOfferQuery 메서드
* IDocumentClient 인터페이스에 노출된 공용 클래스를 공개했습니다.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 정제된 요청에 대한 재시도 지원이 추가되었습니다.  사용자가 ConnectionPolicy.RetryOptions 속성을 구성하여 재시도 횟수와 최대 대기 시간을 지정할 수 있습니다.
* 모든 DocumentClient 속성 및 메서드의 시그니처를 정의하는 새로운 IDocumentClient 인터페이스가 추가되었습니다.  이러한 변경의 일환으로 DocumentClient 클래스 자체에서 IQueryable과 IOrderedQueryable을 메서드로 만드는 확장 메서드도 변경되었습니다.
* 특정 Azure Cosmos DB 엔드포인트 Uri에 대해 ServicePoint.ConnectionLimit를 설정하는 구성 옵션이 추가되었습니다.  ConnectionPolicy.MaxConnectionLimit를 사용하여 기본값(50)을 변경합니다.
* IPartitionResolver와 해당 구현의 사용이 중단되었습니다.  IPartitionResolver 지원이 중단되었습니다. 보다 큰 저장소 및 처리량에는 파티션된 컬렉션을 사용하는 것이 좋습니다.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* RequestOptions를 매개 변수로 사용하는 Uri 기반 ExecuteStoredProcedureAsync 메서드에 오버로드가 추가되었습니다.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* 문서에 대한 TTL(Time to Live) 지원이 추가되었습니다.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Azure 클라우드 서비스 솔루션의 일부로 패키징에 대한 .NET SDK의 Nuget 패키징에서 버그가 수정되었습니다.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다. 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[수정됨]** Azure Cosmos DB 엔드포인트를 쿼리하면 다음이 throw됩니다. ‘System.Net.Http.HttpRequestException: 콘텐츠를 스트림에 복사하는 중 오류가 발생했습니다.’

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* 페이징, 조건식 및 범위 비교에 대한 새 연산자를 포함하는 LINQ 지원이 확장되었습니다.
  * LINQ에서 SELECT TOP 동작을 사용하도록 설정하는 Take 연산자
  * 문자열 범위 비교를 사용하도록 설정하는 CompareTo 연산자
  * 조건부 (?) 및 병합 연산자 (??)
* **[수정됨]** 모델 프로젝션을 LINQ 쿼리의 Where-In과 결합할 때의 ArgumentOutOfRangeException이 수정되었습니다. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[수정됨]** Select가 마지막 식이 아니면 LINQ 공급자는 프로젝션이 없다고 가정하고 SELECT *를 잘못 생성했습니다.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert가 구현되고 UpsertXXXAsync 메서드가 추가됨
* 모든 요청에 대한 성능 향상
* 문자열의 조건부, 병합 및 CompareTo 메서드에 대한 LINQ 공급자 지원
* **[수정됨]** LINQ 공급자 --> 목록에 Contains 메서드를 구현하여 IEnumerable 및 배열과 동일한 SQL을 생성합니다.
* **[수정됨]** BackoffRetryUtility가 다시 시도할 때 새로 만드는 대신 동일한 HttpRequestMessage를 다시 사용합니다.
* **[사용되지 않음]** UriFactory.CreateCollection --> 이제 UriFactory.CreateDocumentCollection을 사용해야 합니다.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[수정됨]** nl-NL 등과 같은 en 이외의 문화권 정보를 사용할 때의 지역화 문제 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* ID 기반 라우팅 추가
  * ID 기반 리소스 링크 생성을 지원하기 위한 새 UriFactory 도우미
  * URI에서 수행할 DocumentClient의 새 오버로드
* LINQ에서 지리 공간에 대한 IsValid() 및 IsValidDetailed() 추가됨
* LINQ 공급자 지원이 향상되었습니다.
  * **수학** - Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **문자열** - Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **배열** - Concat, Contains, Count
  * **IN** 연산자

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* 인덱싱 정책 수정 지원이 추가되었습니다.
  * DocumentClient의 새로운 ReplaceDocumentCollectionAsync 메서드
  * 인덱스 정책 변경 진행률을 추적하기 위한 ResourceResponse<T>의 새로운 IndexTransformationProgress 속성
  * 이제 DocumentCollection.IndexingPolicy를 변경할 수 있음
* 공간 인덱싱 및 쿼리 지원이 추가되었습니다.
  * 점 및 다각형과 같은 공간 형식을 직렬화/역직렬화하기 위한 새로운 Microsoft.Azure.Documents.Spatial 네임스페이스
  * Cosmos DB에 저장된 GeoJSON 데이터를 인덱싱하기 위한 새로운 SpatialIndex 클래스
* **[수정됨]** LINQ 식 [#38](https://github.com/Azure/azure-documentdb-net/issues/38)에서 생성된 잘못된 SQL 쿼리.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Newtonsoft.Json v5.0.7에 대한 종속성이 추가되었습니다.
* Order By를 지원하기 위한 변경 내용은 다음과 같습니다.
  
  * OrderBy() 또는 OrderByDescending()에 대한 LINQ 공급자 지원
  * Order By를 지원하기 위한 IndexingPolicy 
    
    **가능한 주요 변경 내용** 
    
    사용자 지정 인덱싱 정책을 사용하여 컬렉션을 프로비전하는 기존 코드가 있다면 기존 코드는 새 IndexingPolicy 클래스를 지원하도록 업데이트되어야 합니다. 사용자 지정 인덱싱 정책이 없다면 이 변경 사항은 영향을 주지 않습니다.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 새로운 HashPartitionResolver 및 RangePartitionResolver 클래스와 IPartitionResolver를 사용한 데이터 분할 지원이 추가되었습니다.
* DataContract serialization이 추가되었습니다.
* LINQ 공급자의 GUID 지원이 추가되었습니다.
* LINQ의 UDF 지원이 추가되었습니다.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다. 

사용 중지된 SDK를 사용하는 Azure Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

<br/>

| Version | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.4.1](#2.4.1) |2019 년 6 월 20 일 |--- |
| [2.4.0](#2.4.0) |2019 년 5 월 5 일 |--- |
| [2.3.0](#2.3.0) |2019 년 4 월 4 일 |--- |
| [2.2.3](#2.2.3) |2019 년 2 월 11 일 |--- |
| [2.2.2](#2.2.2) |2019년 2월 6일 |--- |
| [2.2.1](#2.2.1) |2018년 12월 24일 |--- |
| [2.2.0](#2.2.0) |2018년 12월 7일 |--- |
| [2.1.3](#2.1.3) |2018년 10월 15일 |--- |
| [2.1.2](#2.1.2) |2018년 10월 4일 |--- |
| [2.1.1](#2.1.1) |2018년 9월 27일 |--- |
| [2.1.0](#2.1.0) |2018년 9월 21일 |--- |
| [2.0.0](#2.0.0) |2018년 9월 7일 |--- |
| [1.22.0](#1.22.0) |2018년 4월 19일 |--- |
| [1.21.1](#1.20.1) |2018년 3월 9일 |--- |
| [1.20.2](#1.20.1) |2018년 2월 21일 |--- |
| [1.20.1](#1.20.1) |2018년 2월 5일 |--- |
| [1.19.1](#1.19.1) |2017년 11월 16일 |--- |
| [1.19.0](#1.19.0) |2017년 11월 10일 |--- |
| [1.18.1](#1.18.1) |2017년 11월 7일 |--- |
| [1.18.0](#1.18.0) |2017년 10월 17일 |--- |
| [1.17.0](#1.17.0) |2017년 8월 10일 |--- |
| [1.16.1](#1.16.1) |2017년 8월 7일 |--- |
| [1.16.0](#1.16.0) |2017년 8월 2일 |--- |
| [1.15.0](#1.15.0) |2017년 6월 30일 |--- |
| [1.14.1](#1.14.1) |2017년 5월 23일 |--- |
| [1.14.0](#1.14.0) |2017년 5월 10일 |--- |
| [1.13.4](#1.13.4) |2017년 5월 09일 |--- |
| [1.13.3](#1.13.3) |2017년 5월 06일 |--- |
| [1.13.2](#1.13.2) |2017년 4월 19일 |--- |
| [1.13.1](#1.13.1) |2017년 3월 29일 |--- |
| [1.13.0](#1.13.0) |2017년 3월 24일 |--- |
| [1.12.2](#1.12.2) |2017년 3월 20일 |--- |
| [1.12.1](#1.12.1) |2017년 3월 14일 |--- |
| [1.12.0](#1.12.0) |2017년 2월 15일 |--- |
| [1.11.4](#1.11.4) |2017년 2월 6일 |--- |
| [1.11.3](#1.11.3) |2017년 1월 26일 |--- |
| [1.11.1](#1.11.1) |2016년 12월 21일 |--- |
| [1.11.0](#1.11.0) |2016년 12월 8일 |--- |
| [1.10.0](#1.10.0) |2016년 9월 27일 |--- |
| [1.9.5](#1.9.5) |2016년 9월 1일 |--- |
| [1.9.4](#1.9.4) |2016년 8월 24일 |--- |
| [1.9.3](#1.9.3) |2016년 8월 15일 |--- |
| [1.9.2](#1.9.2) |2016년 7월 23일 |--- |
| [1.8.0](#1.8.0) |2016년 6월 14일 |--- |
| [1.7.1](#1.7.1) |2016년 5월 6일 |--- |
| [1.7.0](#1.7.0) |2016년 4월 26일 |--- |
| [1.6.3](#1.6.3) |2016년 4월 8일 |--- |
| [1.6.2](#1.6.2) |2016년 3월 29일 |--- |
| [1.5.3](#1.5.3) |2016년 2월 19일 |--- |
| [1.5.2](#1.5.2) |2015년 12월 14일 |--- |
| [1.5.1](#1.5.1) |2015년 11월 23일 |--- |
| [1.5.0](#1.5.0) |2015년 10월 5일 |--- |
| [1.4.1](#1.4.1) |2015년 8월 25일 |--- |
| [1.4.0](#1.4.0) |2015년 8월 13일 |--- |
| [1.3.0](#1.3.0) |2015년 8월 5일 |--- |
| [1.2.0](#1.2.0) |2015년 7월 6일 |--- |
| [1.1.0](#1.1.0) |2015년 4월 30일 |--- |
| [1.0.0](#1.0.0) |2015년 4월 8일 |--- |


## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 

