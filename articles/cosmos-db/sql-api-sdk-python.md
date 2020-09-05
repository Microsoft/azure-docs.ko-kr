---
title: Azure Cosmos DB SQL Python API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Python SDK의 각 버전 간 변경 내용을 포함하여 SQL Python API 및 SDK에 대한 모든 것을 알아봅니다.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-python
ms.openlocfilehash: e9f9daea2c0d570efb81603784ee730b11668426
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585987"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Python SDK: 릴리스 정보 및 리소스

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

| |  |
|---|---|
|**SDK 다운로드**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API 설명서**|[Python API 참조 설명서](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**SDK 설치 지침**|[Python SDK 설치 지침](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)|
|**시작**|[Python SDK 시작](create-sql-api-python.md)|
|**현재 지원되는 플랫폼**|[Python 2.7](https://www.python.org/downloads/) 및 [Python 3.5.3+](https://www.python.org/downloads/)|

## <a name="release-history"></a>릴리스 기록

### <a name="410-2020-08-10"></a>4.1.0 (2020-08-10)

- "지연" 인덱싱 모드에 대 한 사용 중단 경고가 추가 되었습니다. 백 엔드는이 모드를 사용 하 여 컨테이너를 만드는 것을 허용 하지 않으며 대신 일관 되도록 설정 합니다.

**새로운 기능**
- 새 컨테이너를 만들 때 분석 저장소 TTL을 설정 하는 기능이 추가 되었습니다.

**버그 수정**
- Get_client Api에 대 한 입력으로 dicts에 대 한 지원이 수정 되었습니다.
- 쿼리 반복기에서 Python 2/3 호환성이 수정 되었습니다.
- 형식 힌트 오류 (문제 #12570)를 수정 했습니다.
- 옵션 헤더가 upsert_item 함수에 추가 되지 않은 버그를 수정 했습니다. 문제 #11791-감사 @aalapatirvbd 합니다.
- 항목에서 문자열이 아닌 ID를 사용할 때 발생 하는 오류를 수정 했습니다. 이제 AttributeError (Issue #11793)가 아닌 TypeError이 발생 합니다.

### <a name="400"></a>4.0.0

* 안정적인 릴리스.
* 요청 및 응답 헤더에 대해 사용자 지정 로커를 전달하도록 파이프라인에 HttpLoggingPolicy가 추가되었습니다.

### <a name="400b6"></a>4.0.0b6

* 미디어 API에 대한 synchronized_request의 버그가 수정되었습니다.
* 미디어 요청이 지원되지 않으므로 MediaReadMode 및 MediaRequestTimeout이 ConnectionPolicy에서 제거되었습니다.

### <a name="400b5"></a>4.0.0b5

* azure.cosmos.errors 모듈이 더 이상 사용되지 않으며 azure.cosmos.exceptions로 대체되었습니다.
* 이 액세스 조건 매개 변수(`access_condition`, `if_match`, `if_none_match`)가 더 이상 사용되지 않으며, 개별 `match_condition` 및 `etag` 매개 변수가 사용됩니다.
* 라우팅 맵 공급자의 버그가 수정되었습니다.
* 쿼리에 대한 Distinct, Offset 및 Limit 지원이 추가되었습니다.
* 이제 기본 문서 쿼리 실행 컨텍스트가 다음 쿼리에 대해 사용됩니다.

  * ChangeFeed 쿼리
  * 단일 파티션 쿼리(partitionkey, partitionKeyRangeId가 옵션에 제공됨)
  * 비문서 쿼리

* enable cross partition query가 true로 설정되었지만 "value" 키워드가 제공되지 않은 다중 파티션에서의 집계에 대한 오류
* 쿼리 계획을 페치하는 다른 시나리오를 위한 쿼리 계획 엔드포인트 적중
* Cosmos 엔터티 개체에 대해 `__repr__` 지원이 추가되었습니다.
* 설명서가 업데이트되었습니다.

### <a name="400b4"></a>4.0.0b4

* 작업을 완료해야 하는 절대 시간 제한(초)을 지정할 수 있도록 모든 작업에 `timeout` 키워드 인수 지원이 추가되었습니다. 시간 제한 값이 초과되면 `azure.cosmos.errors.CosmosClientTimeoutError`가 발생합니다.

* HTTP 연결 오류 중 재시도 동작을 관리하기 위한 새로운 `ConnectionRetryPolicy`가 추가되었습니다.

* 새로운 생성자 및 작업별 구성 키워드 인수가 추가되었습니다.

  * `retry_total` - 최대 재시도 횟수입니다.
  * `retry_backoff_max` - 최대 재시도 대기 시간(초)입니다.
  * `retry_fixed_interval` - 고정된 재시도 간격(밀리초)입니다.
  * `retry_read` - 최대 소켓 읽기 재시도 횟수입니다.
  * `retry_connect` - 최대 연결 오류 재시도 횟수입니다.
  * `retry_status` - 오류 상태 코드에 대한 최대 재시도 횟수입니다.
  * `retry_on_status_codes` - 재시도할 특정 상태 코드 목록입니다.
  * `retry_backoff_factor` - 재시도 사이의 대기 시간을 계산하기 위한 요소입니다.

### <a name="400b3"></a>4.0.0b3

* CosmosClient 및 Database에 각각 `create_database_if_not_exists()` 및 `create_container_if_not_exists` 기능이 추가되었습니다.

### <a name="400b2"></a>4.0.0b2

* 4\.0.0b2 버전은 Python 언어 모범 사례에 맞는 클라이언트 라이브러리를 빌드하기 위한 노력의 두 번째 결과입니다.

**주요 변경 내용**

* `azure.core.pipeline`에 정의된 HTTP 파이프라인을 사용하도록 클라이언트 연결이 수정되었습니다.

* 이제 대화형 개체가 프록시로 변경되었습니다. 다음 내용이 포함됩니다.

  * `Database` -> `DatabaseProxy`
  * `User` -> `UserProxy`
  * `Container` -> `ContainerProxy`
  * `Scripts` -> `ScriptsProxy`

* `CosmosClient` 생성자가 업데이트되었습니다.

  * `auth` 매개 변수가 `credential`로 변경되었고 이제 인증 형식을 직접 사용합니다. 즉, 마스터 키 값, 리소스 토큰의 사전 또는 권한 목록을 전달할 수 있습니다. 하지만 이전 사전 형식도 계속 지원됩니다.

  * `connection_policy` 매개 변수가 키워드 전용 매개 변수로 설정되었고, 이 매개 변수도 계속 지원되지만, 이제는 정책의 각 개별 특성을 명시적 키워드 인수로 전달할 수 있습니다.

    * `request_timeout`
    * `media_request_timeout`
    * `connection_mode`
    * `media_read_mode`
    * `proxy_config`
    * `enable_endpoint_discovery`
    * `preferred_locations`
    * `multiple_write_locations`

* Azure Portal에서 검색된 연결 문자열을 통해 만들기를 지원하도록 새로운 생성자가 `CosmosClient`에 추가되었습니다.

* 일부 `read_all` 작업이 `list` 작업으로 변경되었습니다.

  * `CosmosClient.read_all_databases` -> `CosmosClient.list_databases`
  * `Container.read_all_conflicts` -> `ContainerProxy.list_conflicts`
  * `Database.read_all_containers` -> `DatabaseProxy.list_containers`
  * `Database.read_all_users` -> `DatabaseProxy.list_users`
  * `User.read_all_permissions` -> `UserProxy.list_permissions`

* `request_options` 또는 `feed_options` 매개 변수를 사용하는 모든 작업이 키워드 전용 매개 변수로 이동되었습니다. 또한 이러한 옵션 사전도 계속 지원되지만, 사전 내의 개별 옵션이 이제는 명시적 키워드 인수로 지원됩니다.

* 오류 계층 구조는 이제 다음에서 상속 됩니다 `azure.core.AzureError` .

  * `HTTPFailure`는 `CosmosHttpResponseError`로 이름이 변경되었습니다.
  * `JSONParseFailure`가 제거되었고 대신 `azure.core.DecodeError`가 사용됩니다.
  * 특정 응답 코드에 대해 오류가 더 추가되었습니다.
    * `CosmosResourceNotFoundError` - 상태 404
    * `CosmosResourceExistsError` - 상태 409
    * `CosmosAccessConditionFailedError` - 상태 412

* 이제 `CosmosClient`를 컨텍스트 관리자에서 실행하여 클라이언트 연결 닫기를 처리할 수 있습니다.

* 반복 가능한 응답(예: 쿼리 응답 및 목록 응답)이 이제 `azure.core.paging.ItemPaged` 형식입니다. `fetch_next_block` 메서드가 `by_page` 메서드로 액세스되는 보조 반복기로 대체되었습니다.

### <a name="400b1"></a>4.0.0b1

4\.0.0b1 버전은 Python 언어 모범 사례에 맞는 사용자에게 친숙한 클라이언트 라이브러리를 만들기 위한 노력의 첫 번째 미리보기입니다. 이에 대한 자세한 내용을 보고 다른 Azure SDK 라이브러리의 미리보기 릴리스에 대한 자세한 내용을 보려면 https://aka.ms/azure-sdk-preview1-python 을 참조하세요.

**주요 변경 내용: 새로운 API 디자인**

* 작업 범위가 이제 특정 클라이언트를 대상으로 지정됩니다.

  * `CosmosClient`: 이 클라이언트는 계정 수준의 작업을 처리합니다. 여기에는 계정의 데이터베이스 나열 및 서비스 속성 관리가 포함됩니다.
  * `Database`: 이 클라이언트는 데이터베이스 수준의 작업을 처리합니다. 여기에는 컨테이너, 사용자 및 저장 프로시저 만들기 및 삭제가 포함됩니다. `cosmosClient` 인스턴스에서 이름으로 액세스될 수 있습니다.
  * `Container`: 이 클라이언트는 특정 컨테이너에 대한 작업을 처리합니다. 여기에는 항목 쿼리 및 삽입과 속성 관리가 포함됩니다.
  * `User`: 이 클라이언트는 특정 사용자에 대한 작업을 처리합니다. 여기에는 권한 추가 및 삭제와 사용자 속성 관리가 포함됩니다.

    이러한 클라이언트는 `get_<child>_client` 메서드를 사용하여 클라이언트 계층 구조를 따라 탐색하여 액세스될 수 있습니다. 새 API에 대한 자세한 내용은 [참조 설명서](https://aka.ms/azsdk-python-cosmos-ref)를 참조하세요.

* 클라이언트는 ID가 아닌 이름으로 액세스됩니다. 링크를 만들기 위해 문자열을 연결할 필요가 없습니다.

* 더 이상 개별 모듈에서 형식 및 메서드를 가져올 필요가 없습니다. 공용 API 노출 영역이 `azure.cosmos` 패키지에서 직접 제공됩니다.

* 개별 `RequestOptions` 인스턴스를 생성하는 대신 개별 요청 속성을 키워드 인수로 제공할 수 있습니다.

### <a name="302"></a>3.0.2

* MultiPolygon 데이터 형식 지원 추가
* 세션 읽기 다시 시도 정책의 버그 수정
* Base64 문자열을 디코딩하는 동안 잘못된 패딩 문제에 대한 버그 수정

### <a name="301"></a>3.0.1

* LocationCache의 버그 수정
* 버그 수정 엔드포인트 다시 시도 논리
* 설명서 수정됨

### <a name="300"></a>3.0.0

* 다중 지역 쓰기 지원 추가
* 이름 변경
  * DocumentClient에서 CosmosClient로
  * 컬렉션에서 컨테이너로
  * 항목에서 문서로
  * 패키지 이름이 "azure-cosmos"로 업데이트됨
  * 네임스페이스가 "azure.cosmos"로 업데이트됨

### <a name="233"></a>2.3.3

* 프록시 지원이 추가됨
* 변경 피드 읽기 지원이 추가됨
* 컬렉션 할당량 헤더 지원이 추가됨
* 큰 세션 토큰 문제의 버그 수정
* ReadMedia API 버그 수정
* 파티션 키 범위 캐시의 버그 수정

### <a name="232"></a>2.3.2

* 연결 문제에 대한 기본 재시도 지원이 추가되었습니다.

### <a name="231"></a>2.3.1

* Azure DocumentDB 대신 Azure Cosmos DB를 참조하도록 업데이트된 설명서입니다.

### <a name="230"></a>2.3.0

* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator 에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name="221"></a>2.2.1

* 집계 사전에 대한 버그 수정
* 리소스 링크의 슬래시 잘라내기에 대한 버그 수정
* 유니코드 인코딩 테스트

### <a name="220"></a>2.2.0

* 분당 요청 단위(RU/m) 기능에 대한 지원이 추가되었습니다.
* ConsistentPrefix라는 새로운 일관성 수준에 대한 지원이 추가되었습니다.

### <a name="210"></a>2.1.0

* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다.
* DocumentDB 에뮬레이터에 대해 실행하는 경우 SSL 유효성 검사를 비활성화하기 위한 옵션을 추가했습니다.
* 종속 요청 모듈이 정확히 2.10.0이어야 하는 제한을 제거했습니다.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.
* 저장된 프로시저가 실행되는 동안 스크립트 로깅을 사용할 수 있도록 지원이 추가되었습니다.
* 이 릴리스에서 REST API 버전이 '2017-01-19'로 증가되었습니다.

### <a name="201"></a>2.0.1

* 문서 주석에 대한 편집이 변경되었습니다.

### <a name="200"></a>2.0.0

* Python 3.5에 대한 지원이 추가되었습니다.
* 요청 모듈을 사용하는 연결 풀링에 대한 지원이 추가되었습니다.
* 세션 일관성에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDERBY 쿼리에 대한 지원이 추가되었습니다.

### <a name="190"></a>1.9.0

* 제한된 요청에 대한 재시도 정책 지원이 추가되었습니다. (제한된 요청은 오류 코드 429, 요청 속도가 너무 크다는 예외를 수신합니다.) 기본적으로 오류 코드 429가 발생하면 DocumentDB는 각 요청을 9번 다시 시도하며 응답 헤더에서 retryAfter 시간을 적용합니다.
  이제 다시 시도 간의 서버에서 반환한 retryAfter 시간을 무시하려는 경우 고정된 다시 시도 간격 시간은 ConnectionPolicy 개체에서 RetryOptions 속성의 일부로 설정될 수 있습니다.
  DocumentDB는 제한된 요청 각각에 대해 30초 동안 대기하고(다시 시도 횟수와 관계 없이) 오류 코드 429를 포함하는 응답을 반환합니다.
  이 시간도 ConnectionPolicy 개체에 있는 RetryOptions 속성에서 재정의할 수 있습니다.

* 이제 DocumentDB는 x-ms-throttle-retry-count 및 x-ms-throttle-retry-wait-time-ms를 다시 시도 사이에 요청이 대기한 스로틀 다시 시도 수 및 누적 시간을 표시하는 모든 요청의 응답 헤더로 반환합니다.

* document_client 클래스에 노출된 RetryPolicy 클래스 및 해당 속성(retry_policy)을 제거하고 대신 일부 기본 다시 시도 옵션을 재정의하는 데 사용할 수 있는 ConnectionPolicy 클래스의 RetryOptions 속성을 노출하는 RetryOptions 클래스를 지정했습니다.

### <a name="180"></a>1.8.0

* 지리적으로 복제된 데이터베이스 계정에 대한 지원이 추가되었습니다.
* 글로벌 호스트와 masterKey를 개별 테스트 클래스로 이동하기 위한 테스트 수정

### <a name="170"></a>1.7.0

* 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

### <a name="161"></a>1.6.1

* 파티션 키 경로에 특수 문자를 허용하는 서버 쪽 분할과 관련된 버그 수정입니다.

### <a name="160"></a>1.6.0

* 서버 쪽 분할 컬렉션 기능에 대한 지원이 추가되었습니다.

### <a name="150"></a>1.5.0

* 클라이언트 쪽 분할 프레임워크가 SDK에 추가되었습니다. HashPartionResolver 및 RangePartitionResolver 클래스가 구현되었습니다.

### <a name="142"></a>1.4.2

* Upsert를 구현합니다. 새로운 UpsertXXX 메서드가 Upsert 기능을 지원하기 위해 추가되었습니다.
* ID 기반 라우팅을 구현합니다. 공용 API 변경 없이 모두 내부에서 변경됩니다.

### <a name="130"></a>1.3.0

* 다른 SDK와 일치하는 버전 번호 가져오기를 생략하는 릴리스

### <a name="120"></a>1.2.0

* 지리 공간 인덱스를 지원합니다.
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스에 대한 ID는 `?, /, #, \\` 문자를 포함하거나 공백으로 끝날 수 없습니다.
* ResourceResponse에 새 헤더 "인덱스 변환 진행"을 추가합니다.

### <a name="110"></a>1.1.0

* V2 인덱싱 정책 구현

### <a name="101"></a>1.0.1

* 프록시 연결 지원

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜

Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다. 새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

> [!WARNING]
> Azure Cosmos DB 2022 년 8 월 31 일 이후에는 더 이상 버그를 수정 하 고, 새 기능을 추가 하 고, Azure Cosmos DB Python SDK for SQL API에 대 한 지원을 제공 합니다. 업그레이드 하지 않으려는 경우 SDK의 버전 1.x 및 2.x에서 전송 된 요청은 Azure Cosmos DB 서비스에서 계속 제공 됩니다.

| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [4.0.0](#400) |2020년 5월 20일 |--- |
| [3.0.2](#302) |2018년 11월 15일 |--- |
| [3.0.1](#301) |2018년 10월 4일 |--- |
| [2.3.3](#233) |2018년 9월 8일 |2020년 8월 30일 |
| [2.3.2](#232) |2018년 5월 8일 |2020년 8월 30일 |
| [2.3.1](#231) |2017년 12월 21일 |2020년 8월 30일 |
| [2.3.0](#230) |2017년 11월 10일 |2020년 8월 30일 |
| [2.2.1](#221) |2017년 9월 29일 |2020년 8월 30일 |
| [2.2.0](#220) |2017년 5월 10일 |2020년 8월 30일 |
| [2.1.0](#210) |2017년 5월 1일 |2020년 8월 30일 |
| [2.0.1](#201) |2016년 10월 30일 |2020년 8월 30일 |
| [2.0.0](#200) |2016년 9월 29일 |2020년 8월 30일 |
| [1.9.0](#190) |2016년 7월 7일 |2020년 8월 30일 |
| [1.8.0](#180) |2016년 6월 14일 |2020년 8월 30일 |
| [1.7.0](#170) |2016년 4월 26일 |2020년 8월 30일 |
| [1.6.1](#161) |2016년 4월 8일 |2020년 8월 30일 |
| [1.6.0](#160) |2016년 3월 29일 |2020년 8월 30일 |
| [1.5.0](#150) |2016년 1월 3일 |2020년 8월 30일 |
| [1.4.2](#142) |2015년 10월 6일 |2020년 8월 30일 |
| 1.4.1 |2015년 10월 6일 |2020년 8월 30일 |
| [1.2.0](#120) |2015년 8월 6일 |2020년 8월 30일 |
| [1.1.0](#110) |2015년 7월 9일 |2020년 8월 30일 |
| [1.0.1](#101) |2015년 5월 25일 |2020년 8월 30일 |
| 1.0.0 |2015년 4월 7일 |2020년 8월 30일 |
| 0.9.4-prelease |2015년 1월 14일 |2016년 2월 29일 |
| 0.9.3-prelease |2014년 12월 9일 |2016년 2월 29일 |
| 0.9.2-prelease |2014년 11월 25일 |2016년 2월 29일 |
| 0.9.1-prelease |2014년 9월 23일 |2016년 2월 29일 |
| 0.9.0-prelease |2014년 8월 21일 |2016년 2월 29일 |

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>다음 단계

Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요. 
