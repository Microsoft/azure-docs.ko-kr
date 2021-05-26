---
title: 지속성 함수의 HTTP API - Azure Functions
description: Azure Functions의 지속성 함수 확장에서 HTTP API를 구현하는 방법을 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/11/2021
ms.author: azfuncdf
ms.openlocfilehash: eff6a44734600a6399f76fc7be331835ae395593
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110377453"
---
# <a name="http-api-reference"></a>HTTP API 참조

지속성 함수 확장은 [오케스트레이션](durable-functions-types-features-overview.md#orchestrator-functions), [엔터티](durable-functions-types-features-overview.md#entity-functions) 및 [작업 허브](durable-functions-task-hubs.md)에서 관리 작업을 수행하기 위해 사용될 수 있는 기본 제공 HTTP API 집합을 제공합니다. 이러한 HTTP API는 Azure Functions 호스트에서 권한 부여되었지만 지속성 함수 확장으로 직접 처리되는 확장성 웹후크입니다.

이 확장으로 구현되는 모든 HTTP API에는 다음 매개 변수가 필요합니다. 모든 매개 변수의 데이터 형식은 `string`입니다.

| 매개 변수        | 매개 변수 유형  | 설명 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 쿼리 문자열    | [작업 허브](durable-functions-task-hubs.md)의 이름입니다. 지정하지 않으면 현재 함수 앱의 작업 허브 이름이 사용됩니다. |
| **`connection`** | 쿼리 문자열    | 백 엔드 스토리지 공급자에 대한 연결 앱 설정의 **이름** 입니다. 지정하지 않으면 함수 앱에 대한 기본 연결 구성이 가정됩니다. |
| **`systemKey`**  | 쿼리 문자열    | API를 호출하는 데 필요한 권한 부여 키입니다. |

`systemKey`는 Azure Functions 호스트에서 자동 생성된 권한 부여 키입니다. 특히 지속성 작업 확장 API에 대한 액세스 권한을 부여하고 [다른 Azure Functions 액세스 키](../security-concepts.md#function-access-keys)와 동일한 방식으로 관리할 수 있습니다. .NET의 `CreateCheckStatusResponse` 및 `CreateHttpManagementPayload` API, JavaScript의 `createCheckStatusResponse` 및 `createHttpManagementPayload` API 등과 같은 [오케스트레이션 클라이언트 바인딩](durable-functions-bindings.md#orchestration-client) API를 사용하여 올바른 `taskHub`, `connection` 및 `systemKey` 쿼리 문자열 값을 포함하는 URL을 생성할 수 있습니다.

다음 몇 가지 섹션에서는 확장에서 지원되는 특정 HTTP API에 대해 설명하고 이 API를 사용하는 방법에 대한 예제를 제공합니다.

## <a name="start-orchestration"></a>오케스트레이션 시작

지정된 오케스트레이터 함수의 새 인스턴스 실행을 시작합니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드              | 매개 변수 형식  | 설명 |
|--------------------|-----------------|-------------|
| **`functionName`** | URL             | 시작할 오케스트레이터 작업의 이름입니다. |
| **`instanceId`**   | URL             | 선택적 매개 변수입니다. 오케스트레이션 인스턴스의 ID입니다. 지정하지 않으면 오케스트레이터 함수가 임의 인스턴스 ID로 시작됩니다. |
| **`{content}`**    | 요청 콘텐츠 | 선택 사항입니다. JSON 형식의 오케스트레이터 함수 입력입니다. |

### <a name="response"></a>응답

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)** : 지정된 오케스트레이터 함수가 실행을 시작하도록 예약되었습니다. `Location` 응답 헤더에는 오케스트레이션 상태를 폴링하기 위한 URL이 포함됩니다.
* **HTTP 400(잘못된 요청)** : 지정된 오케스트레이터 함수가 존재하지 않지만, 지정된 인스턴스 ID가 잘못되었거나, 요청 콘텐츠가 올바른 JSON이 아닙니다.

다음은 `RestartVMs` 오케스트레이터 함수를 시작하고 JSON 개체 페이로드를 포함하는 예제 요청입니다.

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

**HTTP 202** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드                       | 설명                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |오케스트레이션 인스턴스의 ID입니다. |
| **`statusQueryGetUri`**     |오케스트레이션 인스턴스의 상태 URL입니다. |
| **`sendEventPostUri`**      |오케스트레이션 인스턴스의 "이벤트 발생" URL입니다. |
| **`terminatePostUri`**      |오케스트레이션 인스턴스의 "종료" URL입니다. |
| **`purgeHistoryDeleteUri`** |오케스트레이션 인스턴스의 “제거 기록” URL입니다. |
| **`rewindPostUri`**         |(미리 보기) 오케스트레이션 인스턴스의 "되감기" URL입니다. |

모든 필드의 데이터 형식은 `string`입니다.

다음은 해당 ID로 `abc123`(가독성을 위해 형식 지정됨)이 사용된 오케스트레이션 인스턴스의 예제 응답 페이로드입니다.

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

HTTP 응답은 *폴링 소비자 패턴* 과 호환됩니다. 또한 다음과 같은 주목할 만한 응답 헤더를 포함합니다.

* **위치**: 상태 엔드포인트의 URL입니다. 이 URL에는 `statusQueryGetUri` 필드와 동일한 값이 포함됩니다.
* **Retry-After**: 폴링 작업 간 대기 시간(초)입니다. 기본값은 `10`입니다.

비동기 HTTP 폴링 패턴에 대한 자세한 내용은 [HTTP 비동기 작업 추적](durable-functions-http-features.md#async-operation-tracking) 설명서를 참조하세요.

## <a name="get-instance-status"></a>인스턴스 상태 가져오기

지정된 오케스트레이션 인스턴스의 상태를 가져옵니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드                   | 매개 변수 형식  | 설명 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`showInput`**         | 쿼리 문자열    | 선택적 매개 변수입니다. `false`로 설정된 경우 함수 입력이 응답 페이로드에 포함되지 않습니다.|
| **`showHistory`**       | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 오케스트레이션 실행 기록이 응답 페이로드에 포함됩니다.|
| **`showHistoryOutput`** | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 함수 출력이 오케스트레이션 실행 기록에 포함됩니다.|
| **`createdTimeFrom`**   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 지정 ISO8601 타임스탬프 시 또는 이후에 생성된 반환된 인스턴스 목록을 필터링합니다.|
| **`createdTimeTo`**     | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 지정 ISO8601 타임스탬프 시 또는 이전에 생성된 반환된 인스턴스 목록을 필터링합니다.|
| **`runtimeStatus`**     | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 런타임 상태를 기반으로 반환된 인스턴스 목록을 필터링합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 문서를 참조하세요. |
| **`returnInternalServerErrorOnFailure`**  | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 인스턴스가 오류 상태인 경우 이 API가 200 대신 HTTP 500 응답을 반환합니다. 이 매개 변수는 자동화된 상태 폴링 시나리오를 위한 것입니다. |

### <a name="response"></a>응답

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 200(OK)** : 지정된 인스턴스가 완료된 상태 또는 실패한 상태입니다.
* **HTTP 202(수락됨)**: 지정된 인스턴스가 진행 중입니다.
* **HTTP 400(잘못된 요청)**: 지정된 인스턴스가 실패했거나 종료되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스가 없거나 실행이 시작되지 않았습니다.
* **HTTP 500(내부 서버 오류)** : `returnInternalServerErrorOnFailure`가 `true`로 설정되어 있고 지정된 인스턴스가 처리되지 않은 예외로 인해 실패한 경우에만 반환됩니다.

**HTTP 200** 및 **HTTP 202** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드                 | 데이터 형식 | Description |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | 문자열    | 인스턴스의 런타임 상태입니다. *Running*, *Pending*, *Failed*, *Canceled*, *Terminated*, *Completed* 값이 포함됩니다. |
| **`input`**           | JSON      | 인스턴스를 초기화하는 데 사용되는 JSON 데이터입니다. `showInput` 쿼리 문자열 매개 변수가 `false`로 설정되면 이 필드는 `null`입니다.|
| **`customStatus`**    | JSON      | 사용자 지정 오케스트레이션 상태에 사용되는 JSON 데이터입니다. 이 필드는 설정하지 않으면 `null`입니다. |
| **`output`**          | JSON      | 인스턴스의 JSON 출력입니다. 인스턴스가 완료된 상태가 아닌 경우 이 필드는 `null`입니다. |
| **`createdTime`**     | 문자열    | 인스턴스를 만든 시간입니다. ISO 8601 확장 표기법을 사용합니다. |
| **`lastUpdatedTime`** | 문자열    | 인스턴스가 마지막으로 유지된 시간입니다. ISO 8601 확장 표기법을 사용합니다. |
| **`historyEvents`**   | JSON      | 오케스트레이션 실행 기록이 포함된 JSON 배열입니다. `showHistory` 쿼리 문자열 매개 변수가 `true`로 설정되지 않으면 이 필드는 `null`입니다. |

다음은 오케스트레이션 실행 기록 및 작업 출력을 포함하는 응답 페이로드 예제입니다(읽기 쉽게 형식이 지정됨).

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

**HTTP 202** 응답에는 앞에서 언급한 `statusQueryGetUri` 필드와 동일한 URL을 참조하는 **위치** 응답 헤더도 포함됩니다.

## <a name="get-all-instances-status"></a>모든 인스턴스 상태 가져오기

또한 ‘인스턴스 상태 가져오기’ 요청에서 `instanceId`를 제거하여 모든 인스턴스의 상태를 쿼리할 수 있습니다. 이 경우 기본 매개 변수가 ‘인스턴스 상태 가져오기’와 동일합니다. 필터링을 위한 쿼리 문자열 매개 변수도 지원됩니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드                   | 매개 변수 형식  | 설명 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`showInput`**         | 쿼리 문자열    | 선택적 매개 변수입니다. `false`로 설정된 경우 함수 입력이 응답 페이로드에 포함되지 않습니다.|
| **`showHistory`**       | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 오케스트레이션 실행 기록이 응답 페이로드에 포함됩니다.|
| **`showHistoryOutput`** | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 함수 출력이 오케스트레이션 실행 기록에 포함됩니다.|
| **`createdTimeFrom`**   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 지정 ISO8601 타임스탬프 시 또는 이후에 생성된 반환된 인스턴스 목록을 필터링합니다.|
| **`createdTimeTo`**     | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 지정 ISO8601 타임스탬프 시 또는 이전에 생성된 반환된 인스턴스 목록을 필터링합니다.|
| **`runtimeStatus`**     | 쿼리 문자열    | 선택적 매개 변수입니다. 지정하면 런타임 상태를 기반으로 반환된 인스턴스 목록을 필터링합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 문서를 참조하세요. |
| **`top`**               | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 쿼리로 반환되는 인스턴스 수를 제한합니다. |

### <a name="response"></a>응답

오케스트레이션 상태(읽기 쉽도록 서식이 지정됨)를 포함하여 응답 페이로드의 예제는 다음과 같습니다.

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> 이 작업은 [기본 Azure Storage 공급자](durable-functions-storage-providers.md#azure-storage)를 사용하고 인스턴스 테이블에 행이 많은 경우 Azure Storage I/O 측면에서 비용이 매우 많이 들 수 있습니다. 인스턴스 테이블에 대한 자세한 내용은 [지속성 함수의 성능 및 크기 조정(Azure Functions)](durable-functions-perf-and-scale.md#instances-table) 설명서에서 확인할 수 있습니다.

결과가 더 있으면 응답 헤더에 연속 토큰이 반환됩니다.  헤더의 이름은 `x-ms-continuation-token`입니다.

다음 요청 헤더에 연속 토큰 값을 설정할 경우, 결과의 다음 페이지를 가져올 수 있습니다. 요청 헤더의 이 이름도 `x-ms-continuation-token`입니다.

## <a name="purge-single-instance-history"></a>단일 인스턴스 기록 제거

지정된 오케스트레이션 인스턴스에 대해 기록 및 관련 아티팩트를 삭제합니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 형식  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |

### <a name="response"></a>응답

다음 HTTP 상태 코드 값이 반환될 수 있습니다.

* **HTTP 200(OK)** : 인스턴스 기록이 성공적으로 제거되었습니다.
* **HTTP 404(찾을 수 없음)** : 지정된 인스턴스가 존재하지 않습니다.

**HTTP 200** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드                  | 데이터 형식 | Description |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | 정수   | 삭제된 인스턴스 수입니다. 단일 인스턴스 사례의 경우 이 값이 항상 `1`입니다. |

다음은 응답 페이로드 예제입니다(읽기 쉽도록 서식 지정).

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>여러 인스턴스 기록 제거

또한 ‘단일 인스턴스 기록 제거’ 요청에서 `{instanceId}`를 제거하여 작업 허브 내에서 여러 인스턴스에 대해 기록 및 관련 아티팩트를 삭제할 수 있습니다. 인스턴스 기록을 선택적으로 제거하려면 ‘모든 인스턴스 상태 가져오기’ 요청에 설명된 동일한 필터를 사용합니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드                 | 매개 변수 형식  | 설명 |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | 쿼리 문자열    | 지정된 ISO8601 타임스탬프에 또는 그 이후에 생성된 제거된 인스턴스 목록을 필터링합니다.|
| **`createdTimeTo`**   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 제공된 ISO8601 타임스탬프 또는 이전에 생성된 제거된 인스턴스 목록을 필터링합니다.|
| **`runtimeStatus`**   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 해당 런타임 상태를 기준으로 제거된 인스턴스 목록을 필터링합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 문서를 참조하세요. |

> [!NOTE]
> 이 작업은 [기본 Azure Storage 공급자](durable-functions-storage-providers.md#azure-storage)를 사용하고 인스턴스 및/또는 기록 테이블에 행이 많은 경우 Azure Storage I/O 측면에서 비용이 매우 많이 들 수 있습니다. 이러한 테이블에 대한 자세한 내용은 [지속성 함수의 성능 및 크기 조정(Azure Functions)](durable-functions-perf-and-scale.md#instances-table) 설명서에서 확인할 수 있습니다.

### <a name="response"></a>응답

다음 HTTP 상태 코드 값이 반환될 수 있습니다.

* **HTTP 200(OK)** : 인스턴스 기록이 성공적으로 제거되었습니다.
* **HTTP 404(찾을 수 없음)** : 필터 식과 일치하는 인스턴스를 찾을 수 없습니다.

**HTTP 200** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드                   | 데이터 형식 | Description |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | 정수   | 삭제된 인스턴스 수입니다. |

다음은 응답 페이로드 예제입니다(읽기 쉽도록 서식 지정).

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>이벤트 발생

실행 중인 오케스트레이션 인스턴스에 이벤트 알림 메시지를 보냅니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 형식  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`eventName`**   | URL             | 대상 오케스트레이션 인스턴스가 대기 중인 이벤트의 이름입니다. |
| **`{content}`**   | 요청 콘텐츠 | JSON 형식 이벤트 페이로드입니다. |

### <a name="response"></a>응답

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 발생된 이벤트를 처리하도록 수락되었습니다.
* **HTTP 400(잘못된 요청)**: 요청 콘텐츠가 `application/json` 형식이 아니거나 유효한 JSON이 아니었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 실패했으며 발생된 이벤트를 처리할 수 없습니다.

다음은 **operation** 이라는 이벤트를 기다리는 인스턴스에 `"incr"` JSON 문자열을 보내는 요청 예제입니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

## <a name="terminate-instance"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스를 종료합니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 유형  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`reason`**      | 쿼리 문자열    | 선택 사항입니다. 오케스트레이션 인스턴스를 종료하는 이유입니다. |

### <a name="response"></a>응답

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 종료된 요청을 처리하도록 수락되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 실패했습니다.

다음은 실행 중인 인스턴스를 종료하고 **buggy(버그 있음)** 에 대한 이유를 지정하는 요청 예제입니다.

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

## <a name="rewind-instance-preview"></a>rewind(되감기) 인스턴스(미리 보기)

가장 최근에 실패한 작업을 재생하여 실패한 오케스트레이션 인스턴스를 실행 중 상태로 복원합니다.

### <a name="request"></a>요청

함수 런타임의 버전 1.x의 경우, 요청 형식이 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

함수 런타임의 버전 2.x의 경우, URL 형식에 모두 동일한 매개 변수가 포함되지만 약간 다른 접두사가 사용됩니다.

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 유형  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`reason`**      | 쿼리 문자열    | 선택 사항입니다. 오케스트레이션 인스턴스를 되감는 이유입니다. |

### <a name="response"></a>응답

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 되감기 요청을 처리하도록 수락되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 종료되었습니다.

다음은 실패한 인스턴스를 되감고 **fixed**(수정됨)의 이유를 지정하는 요청 예제입니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

## <a name="signal-entity"></a>신호 엔터티

[영구 엔터티](durable-functions-types-features-overview.md#entity-functions)로 단방향 작업 메시지를 전송합니다. 엔터티가 없으면 자동으로 생성됩니다.

> [!NOTE]
> 지속성 엔터티는 지속성 함수 2.0부터 제공됩니다.

### <a name="request"></a>요청

HTTP 요청 형식은 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 형식  | 설명 |
|-------------------|-----------------|-------------|
| **`entityName`**  | URL             | 엔터티의 이름(유형)입니다. |
| **`entityKey`**   | URL             | 엔터티의 키(고유 ID)입니다. |
| **`op`**          | 쿼리 문자열    | 선택 사항입니다. 호출할 사용자 정의 작업의 이름입니다. |
| **`{content}`**   | 요청 콘텐츠 | JSON 형식 이벤트 페이로드입니다. |

사용자 정의된 "추가" 메시지를 `steps`라는 `Counter` 엔터티로 전송하는 요청 예제는 다음과 같습니다. 메시지 내용은 `5` 값입니다. 엔터티가 아직 없으면 이 요청으로 생성됩니다.

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> 기본적으로 [.NET의 클래스 기반 엔터티](durable-functions-dotnet-entities.md#defining-entity-classes)에서 `delete`의 `op` 값을 지정하면 엔터티 상태가 삭제됩니다. 하지만 엔터티가 `delete`라는 작업을 정의하는 경우 사용자 정의 작업이 대신 호출됩니다.

### <a name="response"></a>응답

이 작업에는 몇 가지 가능한 응답이 포함됩니다.

* **HTTP 202(수락됨)** : 비동기 처리를 위해 신호 작업이 수락되었습니다.
* **HTTP 400(잘못된 요청)** : 요청 콘텐츠가 `application/json` 유형이 아니거나, 잘못된 JSON이거나, 잘못된 `entityKey` 값을 포함합니다.
* **HTTP 404(찾을 수 없음)** : 지정된 `entityName`을 찾을 수 없습니다.

성공한 HTTP 요청에 응답의 어떤 콘텐츠도 포함되지 않습니다. 실패한 HTTP 요청에 응답 콘텐츠의 JSON 형식 오류 정보가 포함될 수 있습니다.

## <a name="get-entity"></a>엔터티 가져오기

지정된 엔터티의 상태를 가져옵니다.

### <a name="request"></a>요청

HTTP 요청 형식은 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>응답

이 작업에는 두 가지 가능한 응답이 포함됩니다.

* **HTTP 200(OK)** : 지정된 엔터티가 존재합니다.
* **HTTP 404(찾을 수 없음)** : 지정된 엔터티를 찾을 수 없습니다.

성공한 응답에는 해당 콘텐츠로 엔터티의 JSON으로 직렬화된 상태가 포함됩니다.

### <a name="example"></a>예제
다음 예제 HTTP 요청은 `steps`라는 기존 `Counter` 엔터티의 상태를 가져옵니다.

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

`Counter` 엔터티에 단순히 `currentValue` 필드에 저장된 여러 단계가 포함된 경우, 응답 콘텐츠가 다음과 같이 표시될 수 있습니다(가독성을 위해 형식 지정됨).

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>엔터티 나열

엔터티 이름 또는 마지막 작업 날짜를 기준으로 여러 엔터티를 쿼리할 수 있습니다.

### <a name="request"></a>요청

HTTP 요청 형식은 다음과 같이 지정됩니다(가독성을 위해 여러 줄로 표시됨).

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드                       | 매개 변수 형식  | 설명 |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | URL             | 선택 사항입니다. 지정된 경우 해당 엔터티 이름으로 반환된 엔터티 목록을 필터링합니다(대/소문자 구분 안 함). |
| **`fetchState`**            | 쿼리 문자열    | 선택적 매개 변수입니다. `true`로 설정하면 엔터티 상태가 응답 페이로드에 포함됩니다. |
| **`lastOperationTimeFrom`** | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 제공된 ISO8601 타임스탬프 이후에 작업을 처리한 반환된 엔터티 목록을 필터링합니다. |
| **`lastOperationTimeTo`**   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 제공된 ISO8601 타임스탬프 이전에 작업을 처리한 반환된 엔터티 목록을 필터링합니다. |
| **`top`**                   | 쿼리 문자열    | 선택적 매개 변수입니다. 지정된 경우 쿼리로 반환되는 엔터티 수를 제한합니다. |


### <a name="response"></a>응답

성공한 HTTP 200 응답에는 엔터티의 JSON으로 직렬화된 배열과 선택적으로 각 엔터티의 상태가 포함됩니다.

기본적으로 이 작업은 쿼리 조건과 일치하는 처음 100개의 엔터티를 반환합니다. 호출자는 다른 최대 결과 수를 반환하도록 `top`에 대해 쿼리 문자열 매개 변수 값을 지정할 수 있습니다. 반환되는 것보다 결과가 많은 경우 응답 헤더에 연속성 토큰도 반환됩니다. 헤더의 이름은 `x-ms-continuation-token`입니다.

다음 요청 헤더에 연속 토큰 값을 설정할 경우, 결과의 다음 페이지를 가져올 수 있습니다. 요청 헤더의 이 이름도 `x-ms-continuation-token`입니다.

### <a name="example---list-all-entities"></a>예제 - 모든 엔터티 나열

다음 예제 HTTP 요청은 작업 허브에 잇는 모든 엔터티를 나열합니다.

```http
GET /runtime/webhooks/durabletask/entities
```

응답 JSON이 다음과 같을 수 있습니다(가독성을 위해 형식 지정됨).

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>예제 - 엔터티 목록 필터링

다음 예제 HTTP 요청은 `counter` 유형의 처음 2개 엔터티만 나열하고, 해당 상태를 가져옵니다.

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

응답 JSON이 다음과 같을 수 있습니다(가독성을 위해 형식 지정됨).

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속성 함수 모니터링을 위한 Application Insights 사용 방법 알아보기](durable-functions-diagnostics.md)
