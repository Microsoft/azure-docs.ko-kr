---
title: Azure Cosmos DB는 SQL Node.js API, SDK 및 리소스
description: 릴리스 날짜, 사용 중지 날짜 및 Azure Cosmos DB Node.js SDK의 각 버전 간 변경 내용을 포함하여 SQL Node.js API 및 SDK에 대한 모든 것을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: de1c1b93b813f71b321da0625bc60e0762a859c7
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207952"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>SQL API용 Azure Cosmos DB Node.js SDK: 릴리스 정보 및 리소스
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 변경 피드](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [비동기 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 리소스 공급자](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행자-Java](sql-api-sdk-bulk-executor-java.md)

|리소스  |링크  |
|---------|---------|
|SDK 다운로드  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 설명서  |  [JavaScript SDK 참조 설명서](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK 설치 지침  |  [설치 지침](https://github.com/Azure/azure-cosmos-js#installation)
|SDK에 참여 | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 샘플 | [Node.js 코드 샘플](sql-api-nodejs-samples.md)
| 시작 자습서 | [JavaScript SDK 시작](sql-api-nodejs-get-started.md)
| 웹앱 자습서 | [Azure Cosmos DB를 사용하여 Node.js 웹 애플리케이션 빌드](sql-api-nodejs-application.md)
| 현재 지원되는 플랫폼 | [Node.js v12](https://nodejs.org/en/blog/release/v12.7.0/) -SDK 버전 3.x. x. x<br/>[Node.js v10](https://nodejs.org/en/blog/release/v10.6.0/) -SDK 버전 3.x. x. x<br/>[Node.js v8](https://nodejs.org/en/blog/release/v8.16.0/) -SDK 버전 3.x. x. x<br/>[Node.js v6. x](https://nodejs.org/en/blog/release/v6.10.3/) SDK 버전 2.x. x<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK 버전 1.x<br/> [Node.js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK 버전 1.x<br/> [Node.js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK 버전 1.x

## <a name="release-notes"></a>릴리스 정보

### <a name="3.1.0"/>3.1.0</a>
* 기본 ResponseContinuationTokenLimitInKB를 1kb로 설정 합니다. 기본적으로 긴 헤더를 사용 하지 않도록 1kb (node.js는 전역 헤더 크기 제한을 50,). 사용자는이 필드를 긴 헤더를 허용 하도록 설정 하 여 백 엔드에서 쿼리 실행을 최적화 하는 데 도움이 될 수 있습니다.
* DisableSSLVerification을 제거 합니다. 이 옵션에는에 설명 된 새로운 대안이 [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name="3.0.4"/>3.0.4</a>
* InitialHeaders 파티션 키 헤더를 명시적으로 설정 하도록 허용
* Package # 파일을 사용 하 여 불필요 한 파일이 게시 되지 않도록 합니다.
* 이전 버전의 node + v8에서 라우팅 맵 정렬 오류 수정
* 사용자가 부분 다시 시도 옵션을 제공 하면 버그를 수정 합니다.

### <a name="3.0.3"/>3.0.3</a>
* Webpack에서 필수로 호출 된 모듈을 확인 하지 못하도록 방지

### <a name="3.0.2"/>3.0.2</a>
* RUs가 집계 쿼리에 대해 항상 0으로 보고 되는 긴 미해결 버그를 수정 합니다.

### <a name="3.0.0"/>3.0.0</a>

🎉 v3 릴리스 새 기능, 버그 수정 및 몇 가지 주요 변경 내용을 🎉 합니다. 이 릴리스의 주요 목표:

* 주요 새로운 기능 구현
  * 고유 쿼리
  * 제한/오프셋 쿼리
  * 사용자가 취소할 때 요청
* 모든 컨테이너가 무제한으로 확장 된 최신 Cosmos REST API 버전으로 업데이트
* 브라우저에서 Cosmos을 보다 쉽게 사용할 수 있도록 합니다.
* 새 Azure JS SDK 지침에 맞게 조정

#### <a name="migration-guide-for-breaking-changes"></a>주요 변경 내용에 대한 마이그레이션 가이드
##### <a name="improved-client-constructor-options"></a>향상 된 클라이언트 생성자 옵션

생성자 옵션이 간소화 되었습니다.

* masterKey가 key로 이름이 바뀌고 최상위 수준으로 이동 되었습니다.
* 이전에 옵션 아래에 있는 속성. auth가 최상위 수준으로 이동 했습니다.

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>간소화 된 QueryIterator API
V2에서는 쿼리 결과를 반복 하거나 검색 하는 다양 한 방법이 있었습니다. V3 API를 간소화 하 고 유사 하거나 중복 된 Api를 제거 하려고 했습니다.

* Iterator를 제거 합니다. next () 및 iterator. current (). FetchNext ()를 사용 하 여 결과 페이지를 가져옵니다.
* Iterator. forEach ()를 제거 합니다. 대신 비동기 반복기를 사용 합니다.
* iterator. executeNext () 이름이 iterator로 바뀌었습니다. fetchNext ()
* iterator. toArray () 이름이 iterator로 바뀌었습니다. fetchAll ()
* 이제 페이지는 일반 JS 개체 대신 적절 한 응답 개체입니다.
* const container = client.msi (dbId). 컨테이너 (containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>이제 고정 컨테이너가 분할 됩니다.
이제 Cosmos 서비스는 이전에 고정 컨테이너로 만든 컨테이너를 포함 하 여 모든 컨테이너의 파티션 키를 지원 합니다. V3 SDK는이 변경 내용을 구현 하는 최신 API 버전으로 업데이트 되지만 중단 되지 않습니다. 작업에 대한 파티션 키를 제공 하지 않으면 기존 컨테이너와 문서 모두에서 작동 하는 시스템 키가 기본값으로 사용 됩니다.

##### <a name="upsert-removed-for-stored-procedures"></a>저장 프로시저에 대한 Upsert 제거 됨
이전에는 분할 되지 않은 컬렉션에 대해 upsert를 허용 했지만 API 버전 업데이트를 사용 하는 경우 모든 컬렉션이 분할 되므로 완전히 제거 되었습니다.

##### <a name="item-reads-will-not-throw-on-404"></a>항목 읽기는 404에서 throw 되지 않습니다.
const container = client.msi (dbId). 컨테이너 (containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>기본 다중 지역 쓰기
이제 Cosmos 구성에서 지 원하는 경우 SDK는 기본적으로 여러 지역에 작성 됩니다. 이는 이전에 옵트인 동작 이었습니다.

##### <a name="proper-error-objects"></a>적절 한 오류 개체
이제 실패 한 요청은 적절 한 오류 또는 오류 하위 클래스를 throw 합니다. 이전에는 일반 JS 개체를 throw 했습니다.

#### <a name="new-features"></a>새 기능
##### <a name="user-cancelable-requests"></a>사용자가 취소할 때 요청
내부적으로 fetch로 이동 하면 browser AbortController API를 사용 하 여 사용자가 취소할 수 있는 작업을 지원할 수 있습니다. 여러 요청이 잠재적으로 진행 중인 작업 (예: 파티션 간 쿼리)의 경우 작업에 대한 모든 요청이 취소 됩니다. 최신 브라우저 사용자는 이미 AbortController을가지고 있습니다. Node.js 사용자가 safehtml 라이브러리를 사용 해야 합니다.

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Db/컨테이너 만들기 작업의 일부로 처리량 설정
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
헤더 토큰 생성이 새 라이브러리인 @azure/cosmos-sign로 분할 되었습니다. Cosmos REST API를 직접 호출 하는 모든 사용자는 내부 @azure/cosmos에서 호출 하는 동일한 코드를 사용 하 여 헤더에 서명 하는 데 사용할 수 있습니다

##### <a name="uuid-for-generated-ids"></a>생성 된 Id의 UUID
v2에는 항목 Id를 생성 하는 사용자 지정 코드가 있습니다. 잘 알려진 커뮤니티 라이브러리 uuid로 전환 했습니다.

##### <a name="connection-strings"></a>연결 문자열
이제 Azure Portal에서 복사 된 연결 문자열을 전달할 수 있습니다.

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>향상 된 브라우저 환경
브라우저에서 v2 SDK를 사용할 수 있지만 이상적인 환경은 아닙니다. 여러 node.js 기본 제공 라이브러리를 safehtml 하 고 Webpack 또는 Parcel와 같은 번들러을 사용 해야 합니다. V3 SDK를 사용 하면 브라우저 사용자에 게 가장 적합 한 환경을 사용할 수 있습니다.

* 요청 내부를 fetch로 바꾸기 (#245)
* 버퍼 사용 제거 (#330)
* 유니버설 패키지/a p i를 사용 하 여 노드 builtin 사용을 제거 합니다 (#328).
* 노드 중단으로 전환 (#294)

#### <a name="bug-fixes"></a>버그 수정
* 제품 읽기 및 다시 가져오기 제품 수정 (#224)
* EnableEndpointDiscovery 수정 (#207)
* 페이지가 매겨진 결과에서 누락 된 RUs 수정 (#360)
* SQL 쿼리 매개 변수 형식 확장 (#346)
* ItemDefinition에 ttl 추가 (#341)
* CP 쿼리 메트릭 수정 (#311)
* FeedResponse에 활동 id 추가 (#293)
* Ts 형식에서 숫자 (#252)로 전환 (#295)
* 요청 요금 집계 수정 (#289)
* 빈 문자열 파티션 키 허용 (#277)
* 충돌 쿼리 유형에 문자열 추가 (#237)
* 컨테이너에 uniqueKeyPolicy 추가 (#234)

#### <a name="engineering-systems"></a>엔지니어링 시스템
항상 가장 눈에 띄는 변경 사항은 아니지만 팀이 더 나은 코드를 더 빠르게 제공 하는 데 도움이 됩니다.

* 프로덕션 빌드에 대해 rollup 사용 (#104)
* Typescript 3.5로 업데이트 (#327)
* TS 프로젝트 참조로 변환 합니다. 테스트 폴더 추출 (#270)
* NoUnusedLocals 및 noUnusedParameters (#275) 사용
* CI 빌드에 대한 Azure Pipelines YAML (#298)

### <a name="2.1.5"/>2.1.5</a>
* 코드는 변경 되지 않습니다. 2\.1.4 패키지에 추가 파일이 포함 된 문제를 해결 합니다.

### <a name="2.1.4"/>2.1.4</a>
* 재시도 정책 내에서 지역별 장애 조치 (failover) 수정
* ChangeFeed hasMoreResults 속성 수정
* Dev 종속성 업데이트
* PolicheckExclusions 추가

### <a name="2.1.3"/>2.1.3</a>
* Ts 형식을 문자열에서 숫자로 전환 (_s)
* 기본 인덱싱 테스트 수정
* 백 포트 uniqueKeyPolicy v2
* 데모 및 데모 디버깅 수정

### <a name="2.1.2"/>2.1.2</a>
* V3 분기의 백 포트 제공 수정
* ExecuteNext () 형식 시그니처의 버그 수정
* 오타가 수정

### <a name="2.1.1"/>2.1.1</a>
* 빌드 재구성. 빌드 시 SDK 버전을 끌어올 수 있습니다.

### <a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>새 기능
* 추가 된 ChangeFeed 지원 (#196)
* 인덱싱에 대한 MultiPolygon 데이터 형식을 추가 했습니다 (#191).
* MasterKey (#202)에 대한 별칭으로 생성자에 "key" 속성을 추가 합니다.

#### <a name="fixes"></a>수정 프로그램
* Next ()가 반복기에서 잘못 된 값을 반환 하는 버그를 수정 합니다.

#### <a name="engineering-improvements"></a>엔지니어링 기능 향상
* Typescript 소비에 대한 통합 테스트 추가 (#199)
* GitHub에서 직접 설치 (#194) 사용

### <a name="2.0.5"/>2.0.5</a>
* 노드 에이전트 형식의 인터페이스를 추가합니다. Typescript 사용자는 더 이상 @types/node를 종속성으로 설치할 필요가 없습니다.
* 이제 기본 위치가 제대로 적용됩니다.
* 개발자 설명서에 포함된 향상된 기능
* 다양한 오타 수정

### <a name="2.0.4"/>2.0.4</a>
* 2\.0.3에 도입된 형식 정의 문제 수정

### <a name="2.0.3"/>2.0.3</a>
* `big-integer` 종속성 제거
* AsyncIterable 형식에 대한 참조 지시문으로 전환 Typescript 사용자는 더 이상 해당 "lib" 설정을 사용자 지정할 필요가 없습니다.
* 오타 수정

### <a name="2.0.2"/>2.0.2</a>
* 추가 정보 링크 수정

### <a name="2.0.1"/>2.0.1</a>
* 재시도 인터페이스 구현 수정

### <a name="2.0.0"/>2.0.0</a>
* JavaScript SDK 버전 2.0.0 GA
* 다중 지역 쓰기 지원이 추가되었습니다.

### <a name="2.0.0-3"/>2.0.0-3</a>
* 공개 미리 보기용 JavaScript SDK 버전 2.0.0의 RC1
* 관련 데이터베이스, 컨테이너 및 항목 클래스에 분할된 최상위 CosmosClient 및 메서드가 있는 새로운 개체 모델 
* [프라미스](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)에 대한 지원 
* TypeScript로 변환된 SDK

### <a name="1.14.4"/>1.14.4</a>
* npm 설명서가 수정되었습니다.

### <a name="1.14.3"/>1.14.3</a>
* 연결 문제에 대한 기본 재시도 지원이 추가되었습니다.
* 컬렉션 변경 피드 읽기에 대한 지원이 추가되었습니다.
* 일시적인 "읽기 세션을 사용할 수 없음" 세션 일관성 버그가 수정되었습니다.
* 쿼리 메트릭에 대한 지원이 추가되었습니다.
* http 에이전트의 최대 연결 수가 수정되었습니다.

### <a name="1.14.2"/>1.14.2</a>
* Azure DocumentDB 대신 Azure Cosmos DB를 참조하도록 업데이트된 설명서입니다.
* ConnectionPolicy에서 proxyUrl 설정에 대한 지원이 추가되었습니다.

### <a name="1.14.1"/>1.14.1</a>
* 대/소문자 구분 파일 시스템에 대해 사소한 수정이 있었습니다.

### <a name="1.14.0"/>1.14.0</a>
* 세션 일관성에 대한 지원이 추가되었습니다.
* 이 SDK 버전은 https://aka.ms/cosmosdb-emulator 에서 다운로드할 수 있는 최신 버전의 Azure Cosmos DB 에뮬레이터가 필요합니다.

### <a name="1.13.0"/>1.13.0</a>
* 파티션 간 쿼리를 분할 검증했습니다
* 선행 및 후행 슬래시(및 해당 테스트)와의 리소스 링크 지원이 추가되었습니다.

### <a name="1.12.2"/>1.12.2</a>
*   npm 설명서가 수정되었습니다.

### <a name="1.12.1"/>1.12.1</a>
* 관련된 문서에 특수 유니코드 문자(LS, PS)가 있는 executeStoredProcedure의 버그를 수정했습니다.
* 파티션 키의 유니코드 문자를 사용하여 문서를 처리할 때 버그를 수정했습니다.
* 이름 미디어를 사용하여 컬렉션을 만들기 위한 지원을 수정했습니다. GitHub 문제 #114.
* 권한 부여 토큰에 대한 지원을 수정했습니다. GitHub 문제 #178.

### <a name="1.12.0"/>1.12.0</a>
* ConsistentPrefix라고 하는 새로운 [일관성 수준](consistency-levels.md)에 대한 지원이 추가되었습니다.
* UriFactory에 대한 지원이 추가되었습니다.
* 유니코드 지원 버그를 수정했습니다. GitHub 문제 #171.

### <a name="1.11.0"/>1.11.0</a>
* 집계 쿼리(COUNT, MIN, MAX, SUM 및 AVG)에 대한 지원이 추가되었습니다.
* 파티션 간 쿼리에 대한 병렬 처리 수준을 제어하기 위한 옵션을 추가했습니다.
* Azure Cosmos DB 에뮬레이터에 대해 실행하는 경우 SSL 유효성 검사를 비활성화하기 위한 옵션을 추가했습니다.
* 분할된 컬렉션에 대한 최소 처리량이 10,100RU/s에서 2500RU/s로 감소됩니다.
* 단일 파티션 컬렉션에 대한 연속 토큰 버그를 수정했습니다. GitHub 문제 #107.
* 단일 매개 변수인 0을 처리하는 도중 executeStoredProcedure 버그를 수정했습니다. GitHub 문제 #155.

### <a name="1.10.2"/>1.10.2</a>
* SDK 버전을 포함하도록 수정된 사용자 에이전트 헤더입니다.
* 사소한 코드 정리입니다.

### <a name="1.10.1"/>1.10.1</a>
* SDK를 사용하여 에뮬레이터를 대상으로 지정할 때(hostname=localhost) SSL 유효성 검사를 사용하지 않도록 설정됩니다.
* 저장된 프로시저가 실행되는 동안 스크립트 로깅을 사용할 수 있도록 지원이 추가되었습니다.

### <a name="1.10.0"/>1.10.0</a>
* 파티션 간 병렬 쿼리에 대한 지원이 추가되었습니다.
* 분할된 컬렉션의 TOP/ORDER BY 쿼리에 대한 지원이 추가되었습니다.

### <a name="1.9.0"/>1.9.0</a>
* 제한된 요청에 대한 재시도 정책 지원이 추가되었습니다. (제한된 요청은 오류 코드 429, 요청 속도가 너무 크다는 예외를 수신합니다.) 기본적으로 오류 코드 429가 발생하면 Azure Cosmos DB는 각 요청을 9번 다시 시도하며 응답 헤더에서 retryAfter 시간을 적용합니다. 이제 다시 시도 간의 서버에서 반환한 retryAfter 시간을 무시하려는 경우 고정된 다시 시도 간격 시간은 ConnectionPolicy 개체에서 RetryOptions 속성의 일부로 설정될 수 있습니다. Azure Cosmos DB는 제한된 요청 각각에 대해 30초 동안 대기하고(다시 시도 횟수와 관계 없이) 오류 코드 429를 포함하는 응답을 반환합니다. 이 시간도 ConnectionPolicy 개체에 있는 RetryOptions 속성에서 재정의할 수 있습니다.
* 이제 Cosmos DB는 x-ms-throttle-retry-count 및 x-ms-throttle-retry-wait-time-ms를 다시 시도 사이에 요청이 대기한 제한 다시 시도 수 및 누적 시간을 표시하는 모든 요청의 응답 헤더로 반환합니다.
* 기본 다시 시도 옵션의 일부를 재정의하는 데 사용할 수 있는 ConnectionPolicy 클래스에 RetryOptions 속성을 노출하여 RetryOptions 클래스를 추가했습니다.

### <a name="1.8.0"/>1.8.0</a>
* 다중 지역 데이터베이스 계정에 대한 지원이 추가되었습니다.

### <a name="1.7.0"/>1.7.0</a>
* 문서의 TTL(Time to Live) 기능에 대한 지원이 추가되었습니다.

### <a name="1.6.0"/>1.6.0</a>
* [분할된 컬렉션](partition-data.md) 및 [사용자 정의 성능 수준](performance-levels.md)이 구현되었습니다.

### <a name="1.5.6"/>1.5.6</a>
* 잘못된 concat 결과로 인해 링크를 반환하지 못했던 RangePartitionResolver.resolveForRead 버그가 수정되었습니다.

### <a name="1.5.5"/>1.5.5</a>
* hashPartitionResolver resolveForRead() 해결: 예외를 throw하는 제공된 파티션 키가 없는 경우 모든 등록된 링크의 목록을 대신 반환합니다.

### <a name="1.5.4"/>1.5.4</a>
* 문제 해결 [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - 전용 HTTPS 에이전트: Azure Cosmos DB 목적으로 전역 에이전트를 수정하지 않도록 합니다. 모든 lib의 요청에 대해 전용 에이전트를 사용 합니다.

### <a name="1.5.3"/>1.5.3</a>
* 문제 해결 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 미디어 ID에서 대시를 올바르게 처리합니다.

### <a name="1.5.2"/>1.5.2</a>
* 문제 해결 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 수신기 누수 경고

### <a name="1.5.1"/>1.5.1</a>
* 문제 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) 해결 - 대/소문자 구분 시스템으로 인해 Hash 폴더의 이름을 hash로 바꿉니다.

### <a name="1.5.0"/>1.5.0</a>
* 해시 및 범위 파티션 해결 프로그램을 추가하여 분할 지원 구현

### <a name="1.4.0"/>1.4.0</a>
* Upsert를 구현합니다. DocumentClient의 새로운 upsertXXX 메서드입니다.

### <a name="1.3.0"/>1.3.0</a>
* 다른 SDK와 정렬된 버전 번호를 가져오기 위해 건너뛰었습니다.

### <a name="1.2.2"/>1.2.2</a>
* 프라미스 래퍼를 새 리포지토리로 분할합니다.
* npm 레지스트리에 대한 패키지 파일로 업데이트합니다.

### <a name="1.2.1"/>1.2.1</a>
* ID 기반 라우팅을 구현합니다.
* 문제 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) 수정 - 현재 속성이 메서드 current()와 충돌합니다.

### <a name="1.2.0"/>1.2.0</a>
* 지리 공간 인덱스에 대한 지원이 추가되었습니다.
* 모든 리소스에 대한 ID 속성의 유효성을 검사합니다. 리소스 id에는?,/, #, &#47; &#47;, 문자를 사용할 수 없으며 공백으로 끝날 수 없습니다.
* 새 헤더 "인덱스 변환 진행률"을 ResourceResponse에 추가합니다.

### <a name="1.1.0"/>1.1.0</a>
* V2 인덱싱 정책을 구현합니다.

### <a name="1.0.3"/>1.0.3</a>
* 문제 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 핵심 및 프라미스 SDK에서 eslint 및 grunt 구성을 구현했습니다.

### <a name="1.0.2"/>1.0.2</a>
* 문제 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - 프라미스 래퍼에 오류가 있는 헤더가 포함되지 않습니다.

### <a name="1.0.1"/>1.0.1</a>
* readConflicts, readConflictAsync 및 queryConflicts를 추가하여 충돌을 쿼리하는 기능을 구현했습니다.
* API 설명서가 업데이트되었습니다.
* 문제 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 오류

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜
Microsoft는 최신/지원 버전으로 원활히 전환할 수 있도록 SDK 사용 중지 최소 **12개월** 전에 알림을 제공합니다.

새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

사용 중지된 SDK를 사용하는 Cosmos DB에 대한 요청은 서비스에서 거부됩니다.

> [!WARNING]
> SQL API 용 노드 클라이언트 SDK의 모든 버전 1.x는 **2020 년 8 월 30**일에 사용 중지 됩니다. 이는 클라이언트 쪽 노드 SDK에만 영향을 주며 서버 쪽 스크립트 (저장 프로시저, 트리거 및 Udf)에는 영향을 주지 않습니다.
> 
>
<br/>

| 버전 | 릴리스 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 년 7 월 26 일 |--- |
| [3.0.4](#3.0.4) |2019년 7월 22일 |--- |
| [3.0.3](#3.0.3) |2019 년 7 월 17 일 |--- |
| [3.0.2](#3.0.2) |2019 년 7 월 9 일 |--- |
| [3.0.0](#3.0.0) |6 월 28 일, 2019 |--- |
| [2.1.5](#2.1.5) |2019년 3월 20일 |--- |
| [2.1.4](#2.1.4) |2019 년 3 월 15 일 |--- |
| [2.1.3](#2.1.3) |2019 년 3 월 8 일 |--- |
| [2.1.2](#2.1.2) |2019년 1월 28일 |--- |
| [2.1.1](#2.1.1) |2018년 12월 5일 |--- |
| [2.1.0](#2.1.0) |2018년 12월 4일 |--- |
| [2.0.5](#2.0.5) |2018 년 11 월 7 일 |--- |
| [2.0.4](#2.0.4) |2018 년 10 월 30 일 |--- |
| [2.0.3](#2.0.3) |2018 년 10 월 30 일 |--- |
| [2.0.2](#2.0.2) |2018 년 10 월 10 일 |--- |
| [2.0.1](#2.0.1) |2018 년 9 월 25 일 |--- |
| [2.0.0](#2.0.0) |2018년 9월 24일 |--- |
| [2.0.0-3(RC)](#2.0.0-3) |2018년 8월 2일 |--- |
| [1.14.4](#1.14.4) |2018년 5월 3일 |2020 년 8 월 30 일 |
| [1.14.3](#1.14.3) |2018년 5월 3일 |2020 년 8 월 30 일 |
| [1.14.2](#1.14.2) |2017년 12월 21일 |2020 년 8 월 30 일 |
| [1.14.1](#1.14.1) |2017년 11월 10일 |2020 년 8 월 30 일 |
| [1.14.0](#1.14.0) |2017년 11월 9일 |2020 년 8 월 30 일 |
| [1.13.0](#1.13.0) |2017년 10월 11일 |2020 년 8 월 30 일 |
| [1.12.2](#1.12.2) |2017년 8월 10일 |2020 년 8 월 30 일 |
| [1.12.1](#1.12.1) |2017년 8월 10일 |2020 년 8 월 30 일 |
| [1.12.0](#1.12.0) |2017년 5월 10일 |2020 년 8 월 30 일 |
| [1.11.0](#1.11.0) |2017년 3월 16일 |2020 년 8 월 30 일 |
| [1.10.2](#1.10.2) |2017년 1월 27일 |2020 년 8 월 30 일 |
| [1.10.1](#1.10.1) |2016년 12월 22일 |2020 년 8 월 30 일 |
| [1.10.0](#1.10.0) |2016년 10월 3일 |2020 년 8 월 30 일 |
| [1.9.0](#1.9.0) |2016년 7월 7일 |2020 년 8 월 30 일 |
| [1.8.0](#1.8.0) |2016년 6월 14일 |2020 년 8 월 30 일 |
| [1.7.0](#1.7.0) |2016년 4월 26일 |2020 년 8 월 30 일 |
| [1.6.0](#1.6.0) |2016년 3월 29일 |2020 년 8 월 30 일 |
| [1.5.6](#1.5.6) |2016년 3월 8일 |2020 년 8 월 30 일 |
| [1.5.5](#1.5.5) |2016년 2월 2일 |2020 년 8 월 30 일 |
| [1.5.4](#1.5.4) |2016년 2월 1일 |2020 년 8 월 30 일 |
| [1.5.2](#1.5.2) |2016년 1월 26일 |2020 년 8 월 30 일 |
| [1.5.2](#1.5.2) |2016년 1월 22일 |2020 년 8 월 30 일 |
| [1.5.1](#1.5.1) |2016년 1월 4일 |2020 년 8 월 30 일 |
| [1.5.0](#1.5.0) |2015년 12월 31일 |2020 년 8 월 30 일 |
| [1.4.0](#1.4.0) |2015년 10월 6일 |2020 년 8 월 30 일 |
| [1.3.0](#1.3.0) |2015년 10월 6일 |2020 년 8 월 30 일 |
| [1.2.2](#1.2.2) |2015년 9월 10일 |2020 년 8 월 30 일 |
| [1.2.1](#1.2.1) |2015년 8월 15일 |2020 년 8 월 30 일 |
| [1.2.0](#1.2.0) |2015년 8월 5일 |2020 년 8 월 30 일 |
| [1.1.0](#1.1.0) |2015년 7월 9일 |2020 년 8 월 30 일 |
| [1.0.3](#1.0.3) |2015년 6월 4일 |2020 년 8 월 30 일 |
| [1.0.2](#1.0.2) |2015년 5월 23일 |2020 년 8 월 30 일 |
| [1.0.1](#1.0.1) |2015년 5월 15일 |2020 년 8 월 30 일 |
| [1.0.0](#1.0.0) |2015년 4월 8일 |2020 년 8 월 30 일 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고자료
Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.

