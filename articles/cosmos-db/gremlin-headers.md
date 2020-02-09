---
title: Azure Cosmos DB Gremlin 응답 헤더
description: 추가 문제 해결을 가능 하 게 하는 서버 응답 메타 데이터에 대한 참조 설명서
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/03/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 95677f4c45c0213de5ffac5521bac1c6bf7294e4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755090"
---
# <a name="azure-cosmos-db-gremlin-server-response-headers"></a>Azure Cosmos DB Gremlin 서버 응답 헤더
이 문서에서는 Gremlin 서버가 요청 실행 시 호출자에 게 반환 Cosmos DB 하는 헤더에 대해 설명 합니다. 이러한 헤더는 Cosmos DB 서비스에 기본적으로 통합 되 고 고객 지원 서비스를 간소화 하는 응용 프로그램을 구축 하는 요청 성능 문제 해결에 유용 합니다.

이러한 헤더에 대한 종속성을 유지 하는 것은 응용 프로그램의 이식성을 다른 Gremlin 구현으로 제한 한다는 점에 유의 해야 합니다. 반환 시 Cosmos DB Gremlin와 긴밀 하 게 통합 됩니다. 이러한 헤더는 TinkerPop 표준이 아닙니다.

## <a name="headers"></a>headers

| 헤더 | Type | 샘플 값 | 포함 된 경우 | 설명 |
| --- | --- | --- | --- | --- |
| **x-ms-request-charge** | Double | 11.3243 | Success and Failure | 부분 응답 메시지에 대한 [요청 단위 (o s/s 또는 RUs)](request-units.md) 에서 사용 되는 수집 또는 데이터베이스 처리량입니다. 이 헤더는 여러 청크를 포함 하는 요청에 대한 모든 연속에 존재 합니다. 특정 응답 청크의 요금을 반영 합니다. 단일 응답 청크로 구성 된 요청에 대해서만이 헤더는 총 순회 비용과 일치 합니다. 그러나 대부분의 복합 순회에서이 값은 부분 비용을 나타냅니다. |
| **x-ms-total-request-charge** | Double | 423.987 | Success and Failure | 전체 요청에 대한 [요청 단위 (r u/초 또는 RUs)](request-units.md) 에서 사용 된 수집 또는 데이터베이스 처리량입니다. 이 헤더는 여러 청크를 포함 하는 요청에 대한 모든 연속에 존재 합니다. 요청 시작 이후 누적 요금을 나타냅니다. 마지막 청크의이 헤더의 값은 전체 요청 요금을 나타냅니다. |
| **x-y-시간-ms** | Double | 13.75 | Success and Failure | 이 헤더는 대기 시간 문제 해결을 위해 포함 되었습니다. Cosmos DB Gremlin 서버가 부분 응답 메시지를 실행 하 고 생성 하는 데 걸린 시간 (밀리초)을 나타냅니다. 이 헤더의 값을 사용 하 여 전체 요청 대기 시간 응용 프로그램과 비교 하면 네트워크 대기 시간 오버 헤드를 계산할 수 있습니다. |
| **x 밀리초-총 서버 시간-ms** | Double | 130.512 | Success and Failure | Cosmos DB Gremlin 서버가 전체 순회를 실행 하는 데 걸린 총 시간 (밀리초)입니다. 이 헤더는 모든 부분 응답에 포함 됩니다. 요청이 시작 된 이후의 누적 실행 시간을 나타냅니다. 마지막 응답은 총 실행 시간을 나타냅니다. 이 헤더는 클라이언트와 서버를 대기 시간 원본으로 구분 하는 데 유용 합니다. 클라이언트의 순회 실행 시간을이 헤더의 값과 비교할 수 있습니다. |
| **x-ms-status-code** | long | 200 | Success and Failure | 헤더는 요청 완료 또는 종료에 대한 내부 이유를 나타냅니다. 응용 프로그램은이 헤더의 값을 확인 하 고 수정 작업을 수행 하는 것이 좋습니다. |
| **x-m-하위 상태 코드** | long | 1003 | 오류만 | Cosmos DB은 통합 저장소 계층을 기반으로 하는 다중 모델 데이터베이스입니다. 이 헤더에는 고가용성 스택의 하위 계층 내에서 오류가 발생 하는 경우의 실패 원인에 대한 추가 정보가 포함 되어 있습니다. 응용 프로그램은이 헤더를 저장 하 고 고객 지원 Cosmos DB 연락할 때 사용 하는 것이 좋습니다. 이 헤더의 값은 빠른 문제 해결을 위해 Cosmos DB 엔지니어에 게 유용 합니다. |
| **x-ms-retry-after-ms** | 문자열 (TimeSpan) | "00:00:03.9500000" | 오류만 | 이 헤더는 .NET [TimeSpan](https://docs.microsoft.com/dotnet/api/system.timespan) 형식의 문자열 표현입니다. 이 값은 프로 비전 된 처리량 고갈로 인해 실패 한 요청에만 포함 됩니다. 응용 프로그램은 지시 시간 후에 다시 전송 해야 합니다. |
| **x-ms-activity-id** | 문자열 (Guid) | "A9218E01-3A3A-4716-9636-5BD86B056613" | Success and Failure | 헤더에는 요청의 고유한 서버 쪽 식별자가 포함 되어 있습니다. 각 요청에는 추적을 위해 서버에서 고유 식별자가 할당 됩니다. 응용 프로그램은 고객이 고객 지원 담당자에 게 문의할 수 있는 요청에 대해 서버에서 반환 하는 작업 식별자를 기록해 야 합니다. Cosmos DB 지원 담당자 Cosmos DB 서비스 원격 분석에서 이러한 식별자로 특정 요청을 찾을 수 있습니다. |

## <a name="status-codes"></a>상태 코드

서버에서 반환 하는 가장 일반적인 상태 코드는 다음과 같습니다.

| 상태 | 설명 |
| --- | --- |
| **401** | 인증 암호가 Cosmos DB 계정 키와 일치 하지 않을 경우 오류 메시지 `"Unauthorized: Invalid credentials provided"` 반환 됩니다. Azure Portal에서 Cosmos DB Gremlin 계정으로 이동 하 여 키가 올바른지 확인 합니다.|
| **404** | 동일한 edge 또는 정점을 동시에 삭제 하 고 업데이트 하려고 하는 동시 작업 `"Owner resource does not exist"` 오류 메시지는 지정된 데이터베이스 또는 컬렉션이 `/dbs/<database name>/colls/<collection or graph name>` 형식의 연결 매개 변수에서 올바르지 않다는 것을 의미합니다.|
| **408** | `"Server timeout"`는 순회가 **30 초** 이상 걸려서 서버에 의해 취소 되었음을 나타냅니다. 트래버스의 모든 홉에 대한 꼭 짓 점 또는 가장자리를 필터링 하 여 검색 범위를 좁혀 빠르게 실행 되도록 트래버스를 최적화 합니다.|
| **409** | 이는 대개 그래프에 식별자가 있는 꼭 짓 점 또는 가장자리가 이미 있는 경우에 발생 합니다. `"Conflicting request to resource has been attempted. Retry to avoid conflicts."`| 
| **412** | 상태 코드는 오류 메시지 `"PreconditionFailedException": One of the specified pre-condition is not met`와 함께 보충 됩니다. 이 오류는에 지 또는 꼭 짓 점 읽기와 수정 후 저장소에 다시 쓰기 간의 낙관적 동시성 제어 위반을 나타냅니다. 이 오류가 발생 하는 가장 일반적인 상황은 속성 수정입니다 (예: `g.V('identifier').property('name','value')`). Gremlin 엔진에서 꼭 짓 점을 읽고, 수정 하 고, 다시 씁니다. 동시에 동일한 꼭 짓 점 또는 가장자리를 쓰려고 하는 다른 트래버스를 실행 하는 경우 그 중 하나에이 오류가 표시 됩니다. 응용 프로그램에서 서버에 다시 트래버스를 제출 해야 합니다.| 
| **429** | 요청이 제한 되었으며 **x-ms** 의 값 이후 다시 시도 해야 합니다.--ms| 
| **500** | @No__t_0를 포함 하는 오류 메시지는 데이터베이스 및/또는 컬렉션이 같은 이름으로 다시 생성 되었음을 나타냅니다. 변경 내용이 전파되면 5분 내에 이 오류가 사라지고 다른 Cosmos DB 구성 요소에서 캐시가 무효화됩니다. 이 문제를 방지하려면 매번 고유한 데이터베이스 및 컬렉션 이름을 사용합니다.| 
| **1000** | 이 상태 코드는 서버에서 메시지를 성공적으로 구문 분석 했지만 실행할 수 없는 경우 반환 됩니다. 일반적으로 쿼리에 문제가 있음을 나타냅니다.| 
| **1001** | 이 코드는 서버에서 트래버스 실행을 완료 했지만 클라이언트에 응답을 다시 serialize 하지 못할 때 반환 됩니다. 순회가 너무 크거나 TinkerPop 프로토콜 사양을 따르지 않는 복잡 한 결과를 생성 하는 경우이 오류가 발생할 수 있습니다. 응용 프로그램은이 오류가 발생할 때 트래버스를 단순화 해야 합니다. | 
| **1003** | 순회가 허용 된 메모리 제한을 초과 하는 경우 `"Query exceeded memory limit. Bytes Consumed: XXX, Max: YYY"` 반환 됩니다. 메모리 제한은 트래버스 **당 2gb** 입니다.| 
| **1004** | 이 상태 코드는 잘못 된 그래프 요청을 나타냅니다. Deserialization에 실패 하는 경우 요청의 형식이 잘못 될 수 있습니다. 값 형식 또는 지원 되지 않는 gremlin 작업이 요청 되었으므로 값이 아닌 형식이 deserialize 됩니다. 응용 프로그램이 성공 하지 않으므로 요청을 다시 시도해 서는 안 됩니다. | 
| **1007** | 일반적으로이 상태 코드는 오류 메시지 `"Could not process request. Underlying connection has been closed."`와 함께 반환 됩니다. 이 상황은 클라이언트 드라이버가 서버에서 닫히는 연결을 사용 하려고 시도 하는 경우에 발생할 수 있습니다. 응용 프로그램은 다른 연결에서 트래버스를 다시 시도해 야 합니다.
| **1008** | Cosmos DB Gremlin 서버는 클러스터의 균형을 다시 조정 하는 연결을 종료할 수 있습니다. 클라이언트 드라이버는 이러한 상황을 처리 하 고 라이브 연결만을 사용 하 여 서버에 요청을 보냅니다. 때때로 클라이언트 드라이버에서 연결이 닫 혔는 것을 감지 하지 못할 수 있습니다. 응용 프로그램에서 오류가 발생 하는 경우 다른 연결에서 트래버스를 다시 시도해 야 `"Connection is too busy. Please retry after sometime or open more connections."`.

## <a name="samples"></a>샘플

하나의 상태 특성을 읽는 Gremlin.Net 기반 샘플 클라이언트 응용 프로그램:

```csharp
// Following example reads a status code and total request charge from server response attributes.
// Variable "server" is assumed to be assigned to an instance of a GremlinServer that is connected to Cosmos DB account.
using (GremlinClient client = new GremlinClient(server, new GraphSON2Reader(), new GraphSON2Writer(), GremlinClient.GraphSON2MimeType))
{
  ResultSet<dynamic> responseResultSet = await GremlinClientExtensions.SubmitAsync<dynamic>(client, requestScript: "g.V().count()");
  long statusCode = (long)responseResultSet.StatusAttributes["x-ms-status-code"];
  double totalRequestCharge = (double)responseResultSet.StatusAttributes["x-ms-total-request-charge"];

  // Status code and request charge are logged into application telemetry.
}
```

Gremlin java 클라이언트에서 상태 특성을 읽는 방법을 보여 주는 예제는 다음과 같습니다.

```java
try {
  ResultSet resultSet = this.client.submit("g.addV().property('id', '13')");
  List<Result> results = resultSet.all().get();

  // Process and consume results

} catch (ResponseException re) {
  // Check for known errors that need to be retried or skipped
  if (re.getStatusAttributes().isPresent()) {
    Map<String, Object> attributes = re.getStatusAttributes().get();
    int statusCode = (int) attributes.getOrDefault("x-ms-status-code", -1);

    // Now we can check for specific conditions
    if (statusCode == 409) {
        // Handle conflicting writes
      }
    }

    // Check if we need to delay retry
    if (attributes.containsKey("x-ms-retry-after-ms")) {
      // Read the value of the attribute as is
      String retryAfterTimeSpan = (String) attributes.get("x-ms-retry-after-ms"));

      // Convert the value into actionable duration
            LocalTime locaTime = LocalTime.parse(retryAfterTimeSpan);
            Duration duration = Duration.between(LocalTime.MIN, locaTime);

      // Perform a retry after "duration" interval of time has elapsed
    }
  }
}

```

## <a name="next-steps"></a>다음 단계
* [Azure Cosmos DB에 대한 HTTP 상태 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) 
* [일반 Azure Cosmos DB REST 응답 헤더](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers)
* [TinkerPop Graph 드라이버 공급자 요구 사항]( http://tinkerpop.apache.org/docs/current/dev/provider/#_graph_driver_provider_requirements)
