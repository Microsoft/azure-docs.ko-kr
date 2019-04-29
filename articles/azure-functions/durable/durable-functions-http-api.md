---
title: 지속성 함수의 HTTP API - Azure
description: Azure Functions의 지속성 함수 확장에서 HTTP API를 구현하는 방법을 알아봅니다.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: azfuncdf
ms.openlocfilehash: 5bd977826f489ca8452432babe6126b8553450fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730710"
---
# <a name="http-apis-in-durable-functions-azure-functions"></a>지속성 함수의 HTTP API(Azure Functions)

지속성 작업 확장은 다음 작업을 수행하는 데 사용할 수 있는 HTTP API 집합을 공개합니다.

* 오케스트레이션 인스턴스의 상태를 가져옵니다.
* 대기 중인 오케스트레이션 인스턴스에 이벤트를 보냅니다.
* 실행 중인 오케스트레이션 인스턴스를 종료합니다.

이러한 HTTP API 각각은 지속성 작업 확장에서 직접 처리하는 웹후크 작업입니다. 웹후크 작업은 함수 앱의 모든 함수에 특정되지 않습니다.

> [!NOTE]
> 이러한 작업은 [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스에서 인스턴스 관리 API를 사용하여 직접 호출할 수도 있습니다. 자세한 내용은 [인스턴스 관리](durable-functions-instance-management.md)를 참조하세요.

## <a name="http-api-url-discovery"></a>HTTP API URL 검색

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) 클래스는 지원되는 모든 작업에 대한 링크가 포함된 HTTP 응답 페이로드를 생성하는 데 사용할 수 있는 [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_) API를 공개합니다. 다음은 이 API를 사용하는 방법을 보여 주는 예제 HTTP 트리거 함수입니다.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/HttpStart/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript(Functions 2.x만 해당)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

이러한 예제 함수는 다음 JSON 응답 데이터를 생성합니다. 모든 필드의 데이터 형식은 `string`입니다.

| 필드                   |설명                           |
|-------------------------|--------------------------------------|
| **`id`**                |오케스트레이션 인스턴스의 ID입니다. |
| **`statusQueryGetUri`** |오케스트레이션 인스턴스의 상태 URL입니다. |
| **`sendEventPostUri`**  |오케스트레이션 인스턴스의 "이벤트 발생" URL입니다. |
| **`terminatePostUri`**  |오케스트레이션 인스턴스의 "종료" URL입니다. |
| **`rewindPostUri`**     |오케스트레이션 인스턴스의 "rewind" URL입니다. |

다음은 응답 예제입니다.

```http
HTTP/1.1 202 Accepted
Content-Length: 923
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX

{
    "id":"34ce9a28a6834d8492ce6a295f1a80e2",
    "statusQueryGetUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "sendEventPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "terminatePostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX",
    "rewindPostUri":"https://{host}/runtime/webhooks/durabletask/instances/34ce9a28a6834d8492ce6a295f1a80e2/rewind?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code=XXX"
}
```

> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트의 버전에 따라 달라질 수 있습니다. 위의 예제는 Azure Functions 2.x 호스트에 대한 것입니다.

## <a name="async-operation-tracking"></a>비동기 작업 추적

앞에서 언급한 HTTP 응답은 Durable Functions를 사용하여 장기 실행 HTTP 비동기 API를 구현하는 데 도움이 되도록 설계되었습니다. 이는 때때로 *폴링 소비자 패턴*이라고도 합니다. 클라이언트/서버 흐름은 다음과 같이 작동합니다.

1. 클라이언트에서 장기 실행 프로세스(예: 오케스트레이터 함수)를 시작하기 위해 HTTP 요청을 발급합니다.
2. 대상 HTTP 트리거에서 `statusQueryGetUri` 값을 사용하여 `Location` 헤더가 있는 HTTP 202 응답을 반환합니다.
3. 클라이언트에서 `Location` 헤더의 URL을 폴링합니다. `Location` 헤더가 있는 HTTP 202 응답이 계속 표시됩니다.
4. 인스턴스가 완료되거나 실패하면 `Location` 헤더의 엔드포인트에서 HTTP 200을 반환합니다.

이 프로토콜을 사용하면 HTTP 엔드포인트 폴링 및 `Location` 헤더 추적을 지원하는 외부 클라이언트 또는 서비스에서 장기 실행 프로세스를 조정할 수 있습니다. 기본 조각은 지속성 함수 HTTP API에 이미 작성되어 있습니다.

> [!NOTE]
> 기본적으로 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)에서 제공하는 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 이 기능을 사용하면 Logic Apps 워크플로의 일부로 장기 실행 지속성 함수를 포함할 수 있습니다. Logic Apps의 비동기 HTTP 패턴 지원에 대한 자세한 내용은 [Azure Logic Apps 워크플로 작업 및 트리거 설명서](../../logic-apps/logic-apps-workflow-actions-triggers.md#asynchronous-patterns)에서 찾을 수 있습니다.

## <a name="http-api-reference"></a>HTTP API 참조

확장에서 구현된 모든 HTTP API에서 사용하는 매개 변수는 다음과 같습니다. 모든 매개 변수의 데이터 형식은 `string`입니다.

| 매개 변수        | 매개 변수 형식  | 설명 |
|------------------|-----------------|-------------|
| **`taskHub`**    | 쿼리 문자열    | [작업 허브](durable-functions-task-hubs.md)의 이름입니다. 지정하지 않으면 현재 함수 앱의 작업 허브 이름이 사용됩니다. |
| **`connection`** | 쿼리 문자열    | 저장소 계정에 대한 연결 문자열의 **이름**입니다. 지정하지 않으면 함수 앱에 대한 기본 연결 문자열이 사용됩니다. |
| **`systemKey`**  | 쿼리 문자열    | API를 호출하는 데 필요한 권한 부여 키입니다. |

`systemKey`는 Azure Functions 호스트에서 자동 생성된 권한 부여 키입니다. 특히 지속성 작업 확장 API에 대한 액세스 권한을 부여하고 [다른 권한 부여 키](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API)와 동일한 방식으로 관리할 수 있습니다. `systemKey` 값을 검색하는 가장 간단한 방법은 앞에서 언급한 `CreateCheckStatusResponse` API를 사용하는 것입니다.

다음 몇 가지 섹션에서는 확장에서 지원되는 특정 HTTP API에 대해 설명하고 이 API를 사용하는 방법에 대한 예제를 제공합니다.

### <a name="get-instance-status"></a>인스턴스 상태 가져오기

지정된 오케스트레이션 인스턴스의 상태를 가져옵니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드                   | 매개 변수 형식  | 설명 |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`showInput`**         | 쿼리 문자열    | 선택적 매개 변수. 경우 설정 `false`, 함수 입력 응답 페이로드에 포함 되지 것입니다.|
| **`showHistory`**       | 쿼리 문자열    | 선택적 매개 변수. `true`로 설정하면 오케스트레이션 실행 기록이 응답 페이로드에 포함됩니다.|
| **`showHistoryOutput`** | 쿼리 문자열    | 선택적 매개 변수. 경우 설정 `true`, 함수 출력의 오케스트레이션 실행 기록이 포함 됩니다.|
| **`createdTimeFrom`**   | 쿼리 문자열    | 선택적 매개 변수. 를 지정 하는 경우 또는 지정된 된 ISO8601 타임 스탬프 후에 생성 된 반환 된 인스턴스 목록을 필터링 합니다.|
| **`createdTimeTo`**     | 쿼리 문자열    | 선택적 매개 변수. 지정 하면 지정된 된 ISO8601 타임 스탬프 전후에 생성 된 반환 된 인스턴스 목록을 필터링 합니다.|
| **`runtimeStatus`**     | 쿼리 문자열    | 선택적 매개 변수. 지정하면 런타임 상태를 기반으로 반환된 인스턴스 목록을 필터링합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 항목을 참조하세요. |

#### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 200(OK)**: 지정된 인스턴스가 완료된 상태입니다.
* **HTTP 202(수락됨)**: 지정된 인스턴스가 진행 중입니다.
* **HTTP 400(잘못된 요청)**: 지정된 인스턴스가 실패했거나 종료되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스가 없거나 실행이 시작되지 않았습니다.
* **HTTP 500(내부 서버 오류)**: 처리되지 않은 예외로 인해 지정된 인스턴스가 실패했습니다.

**HTTP 200** 및 **HTTP 202** 사례에 대한 응답 페이로드는 다음 필드가 있는 JSON 개체입니다.

| 필드                 | 데이터 형식 | 설명 |
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

### <a name="get-all-instances-status"></a>모든 인스턴스 상태 가져오기

제거 하 여 모든 인스턴스의 상태를 쿼리할 수도 있습니다는 `instanceId` 인스턴스 상태 가져오기' 요청에서. 이 경우 기본 매개 변수 'Get 인스턴스 상태'와 동일합니다. 필터링에 대 한 쿼리 문자열 매개 변수도 지원 됩니다.

기억해야 할 한 가지는 `connection` 및 `code`가 선택 사항이라는 점입니다. 함수에 익명 인증이 있는 경우 코드는 필요하지 않습니다.
이외의 AzureWebJobsStorage 앱 설정에 정의 된 다른 저장소 연결 문자열을 사용 하지 않으려는 경우 다음 무시 해도 연결 쿼리 문자열 매개 변수입니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

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

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

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
| **`showInput`**         | 쿼리 문자열    | 선택적 매개 변수. 경우 설정 `false`, 함수 입력 응답 페이로드에 포함 되지 것입니다.|
| **`showHistory`**       | 쿼리 문자열    | 선택적 매개 변수. `true`로 설정하면 오케스트레이션 실행 기록이 응답 페이로드에 포함됩니다.|
| **`showHistoryOutput`** | 쿼리 문자열    | 선택적 매개 변수. 경우 설정 `true`, 함수 출력의 오케스트레이션 실행 기록이 포함 됩니다.|
| **`createdTimeFrom`**   | 쿼리 문자열    | 선택적 매개 변수. 를 지정 하는 경우 또는 지정된 된 ISO8601 타임 스탬프 후에 생성 된 반환 된 인스턴스 목록을 필터링 합니다.|
| **`createdTimeTo`**     | 쿼리 문자열    | 선택적 매개 변수. 지정 하면 지정된 된 ISO8601 타임 스탬프 전후에 생성 된 반환 된 인스턴스 목록을 필터링 합니다.|
| **`runtimeStatus`**     | 쿼리 문자열    | 선택적 매개 변수. 지정하면 런타임 상태를 기반으로 반환된 인스턴스 목록을 필터링합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 항목을 참조하세요. |
| **`top`**               | 쿼리 문자열    | 선택적 매개 변수. 지정 하면 쿼리에서 반환 되는 인스턴스 수를 제한 합니다. |

#### <a name="response"></a>response

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
> 이 작업은 인스턴스 테이블에 많은 행이 있는 경우 Azure Storage I/O의 측면에서 매우 비쌀 수 있습니다. 인스턴스 테이블에 대한 자세한 내용은 [지속성 함수의 성능 및 크기 조정(Azure Functions)](durable-functions-perf-and-scale.md#instances-table) 설명서에서 확인할 수 있습니다.
>

더 많은 결과가 없으면 연속 토큰이 응답 헤더에 반환 됩니다.  헤더의 이름은 `x-ms-continuation-token`입니다.

다음 요청 헤더에 연속 토큰 값을 설정 하는 경우 결과의 다음 페이지를 가져올 수 있습니다. 이 요청 헤더의 이름은 또한 `x-ms-continuation-token`합니다.

### <a name="purge-single-instance-history"></a>단일 인스턴스 기록 제거

기록 및 지정 된 오케스트레이션 인스턴스에 대 한 관련된 아티팩트를 삭제합니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

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

#### <a name="response"></a>response

다음 HTTP 상태 코드 값을 반환할 수 있습니다.

* **HTTP 200(OK)**: 인스턴스 기록이 삭제 되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정한 인스턴스가 존재 하지 않습니다.

에 대 한 응답 페이로드는 **HTTP 200** 사례 필드를 사용 하 여 JSON 개체입니다.

| 필드                  | 데이터 형식 | 설명 |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | 정수   | 삭제 되는 인스턴스의 수입니다. 단일 인스턴스의 경우이 값은 항상 `1`합니다. |

다음은 응답 페이로드 예제입니다(읽기 쉽도록 서식 지정).

```json
{
    "instancesDeleted": 1
}
```

### <a name="purge-multiple-instance-history"></a>여러 인스턴스에 기록 제거

제거 하 여 기록 및 작업 허브 내의 여러 인스턴스에 대 한 관련된 아티팩트를 삭제할 수도 있습니다는 `{instanceId}` '단일 인스턴스 기록 제거' 요청에서. 인스턴스 기록을 선택적으로 제거 하려면 '모든 인스턴스 상태 가져오기' 요청에 설명 된 동일한 필터를 사용 합니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

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
| **`createdTimeFrom`** | 쿼리 문자열    | 선택적 매개 변수. 를 지정 하는 경우 또는 지정된 된 ISO8601 타임 스탬프 후에 생성 된 제거 된 인스턴스 목록을 필터링 합니다.|
| **`createdTimeTo`**   | 쿼리 문자열    | 선택적 매개 변수. 지정 하면 지정된 된 ISO8601 타임 스탬프 전후에 생성 된 제거 된 인스턴스 목록을 필터링 합니다.|
| **`runtimeStatus`**   | 쿼리 문자열    | 선택적 매개 변수. 를 지정 하는 경우 제거 된 인스턴스 목록을 필터 런타임 상태에 기반 합니다. 가능한 런타임 상태 값의 목록을 보려면 [인스턴스 쿼리](durable-functions-instance-management.md) 항목을 참조하세요. |

매개 변수 없이 지정 된 경우 작업 허브의 모든 인스턴스가 제거 됩니다.

> [!NOTE]
> 많이 있는 경우이 작업을 Azure 저장소 I/O 사용량이 상당히 늘어날 수 인스턴스 및/또는 기록에 있는 행의 테이블입니다. 이러한 테이블에 대 한 자세한 내용은에서 찾을 수 있습니다 합니다 [성능 및 규모 감축 Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) 설명서.

#### <a name="response"></a>response

다음 HTTP 상태 코드 값을 반환할 수 있습니다.

* **HTTP 200(OK)**: 인스턴스 기록이 삭제 되었습니다.
* **HTTP 404(찾을 수 없음)**: 필터 식과 일치 하는 인스턴스를 찾지 못했습니다.

에 대 한 응답 페이로드는 **HTTP 200** 사례 필드를 사용 하 여 JSON 개체입니다.

| 필드                   | 데이터 형식 | 설명 |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | 정수   | 삭제 되는 인스턴스의 수입니다. |

다음은 응답 페이로드 예제입니다(읽기 쉽도록 서식 지정).

```json
{
    "instancesDeleted": 250
}
```

### <a name="raise-event"></a>이벤트 발생

실행 중인 오케스트레이션 인스턴스에 이벤트 알림 메시지를 보냅니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

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

#### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 발생된 이벤트를 처리하도록 수락되었습니다.
* **HTTP 400(잘못된 요청)**: 요청 콘텐츠가 `application/json` 형식이 아니거나 유효한 JSON이 아니었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 실패했으며 발생한 이벤트를 처리할 수 없습니다.

다음은 **operation**이라는 이벤트를 기다리는 인스턴스에 `"incr"` JSON 문자열을 보내는 요청 예제입니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

### <a name="terminate-instance"></a>인스턴스 종료

실행 중인 오케스트레이션 인스턴스를 종료합니다.

#### <a name="request"></a>요청

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 형식  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`reason`**      | 쿼리 문자열    | 선택 사항입니다. 오케스트레이션 인스턴스를 종료하는 이유입니다. |

#### <a name="response"></a>response

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

버전에 대 한 요청 Functions 런타임의 1.x (여러 줄은 명확성을 위해 표시 됨) 다음과 같은 형식이 있습니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

URL 형식은 Functions 런타임의 2.x에는 모두 동일한 매개 변수에서 버전 하지만 약간 다른 접두사를 사용 하 여:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

이 API에 대한 요청 매개 변수에는 앞에서 언급한 기본 집합과 다음과 같은 고유한 매개 변수가 포함됩니다.

| 필드             | 매개 변수 형식  | 설명 |
|-------------------|-----------------|-------------|
| **`instanceId`**  | URL             | 오케스트레이션 인스턴스의 ID입니다. |
| **`reason`**      | 쿼리 문자열    | 선택 사항입니다. 오케스트레이션 인스턴스를 되감는 이유입니다. |

### <a name="response"></a>response

몇 가지 가능한 상태 코드 값을 반환할 수 있습니다.

* **HTTP 202(수락됨)**: 되감기 요청을 처리하도록 수락되었습니다.
* **HTTP 404(찾을 수 없음)**: 지정된 인스턴스를 찾을 수 없습니다.
* **HTTP 410(없음)**: 지정된 인스턴스가 완료되었거나 종료되었습니다.

다음은 실패한 인스턴스를 되감고 **fixed**(수정됨)의 이유를 지정하는 요청 예제입니다.

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

이 API에 대한 응답에는 내용이 포함되어 있지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [오류 처리](durable-functions-error-handling.md)
