---
title: 워크플로 정의 언어-Azure Logic Apps의에서 트리거 및 작업 형식에 대 한 참조
description: Azure Logic apps 워크플로 정의 언어에서 트리거 및 작업 형식에 대 한 참조 가이드
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: reference
ms.date: 06/22/2018
ms.openlocfilehash: bd588eeec8b560411e3fb4b6f84ec8a4a45f08d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844180"
---
# <a name="reference-for-trigger-and-action-types-in-workflow-definition-language-for-azure-logic-apps"></a>Azure Logic apps 워크플로 정의 언어에서 트리거 및 작업 형식에 대 한 참조

이 참조에는 트리거 및 동작 설명 이며 유효성을 검사 하는 논리 앱의 기본 워크플로 정의 식별 하는 데 사용 되는 일반 유형에 대해 설명 합니다 [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)합니다.
논리 앱에서 사용할 수 있는 특정 커넥터 트리거 및 작업을 찾으려면 아래 목록을 참조 합니다 [커넥터 개요](https://docs.microsoft.com/connectors/)합니다.

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>트리거 개요

모든 워크플로 인스턴스화하고 워크플로 시작 하는 호출을 정의 하는 트리거를 포함 합니다. 일반 트리거 범주는 다음과 같습니다.

* *폴링* 트리거 - 정기적인 간격으로 서비스의 끝점을 검사합니다.

* *푸시* 트리거 - 끝점에 대한 구독을 만들고 *콜백 URL*을 제공하여 지정된 이벤트가 발생하거나 데이터를 사용할 수 있을 때 끝점이 트리거에 알릴 수 있도록 합니다. 그러면 트리거는 발생하기 전에 끝점의 응답을 기다립니다. 

트리거에는 이러한 최상위 요소가 있습니다. 단, 일부는 선택 사항입니다.  
  
```json
"<trigger-name>": {
   "type": "<trigger-type>",
   "inputs": { "<trigger-inputs>" },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "conditions": [ "<array-with-conditions>" ],
   "runtimeConfiguration": { "<runtime-config-options>" },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
},
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*trigger-name*> | String | 트리거의 이름입니다. | 
| <*trigger-type*> | String | "Http" 또는 "ApiConnection"과 같은 트리거 형식입니다. | 
| <*trigger-inputs*> | JSON 개체 | 트리거의 동작을 정의하는 입력입니다. | 
| <*time-unit*> | String | 트리거가 실행되는 빈도를 설명하는 시간 단위로, “초”, “분”, “시간”, “일”, “주” 또는 “월”이 있습니다. | 
| <*number-of-time-units*> | 정수  | 트리거가 다시 실행될 때까지 기다리는 시간 단위 수를 나타내는 빈도에 따라 트리거가 얼마나 자주 발생하는지를 지정하는 값입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어, 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*array-with-conditions*> | 배열 | 워크플로를 실행할 것인지 여부를 결정하는 하나 이상의 [조건](#trigger-conditions)을 포함하는 배열입니다. 트리거에만 사용 가능합니다. | 
| <*runtime-config-options*> | JSON 개체 | `runtimeConfiguration` 속성을 설정하여 트리거 런타임 동작을 변경할 수 있습니다. 자세한 내용은 [런타임 구성 설정](#runtime-config-options)을 참조하세요. | 
| <*splitOn-expression*> | String | 배열을 반환하는 트리거의 경우, 처리를 위해 여러 워크플로 인스턴스로 배열 항목을 [분할 또는 *분리*](#split-on-debatch)하는 식을 지정할 수 있습니다. | 
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

## <a name="trigger-types-list"></a>트리거 유형 목록

각 트리거 유형은 트리거의 동작을 정의하는 다른 인터페이스 및 입력을 갖습니다. 

### <a name="built-in-triggers"></a>기본 제공 트리거

| 트리거 유형 | 설명 | 
|--------------|-------------| 
| [**HTTP**](#http-trigger) | 끝점을 검사하거나 *폴링*합니다. 이 끝점은 “202” 비동기 패턴을 사용하거나 배열을 반환하여 특정 트리거 계약을 준수해야 합니다. | 
| [**HTTPWebhook**](#http-webhook-trigger) | 논리 앱에 대한 호출 가능 끝점을 만들지만 지정된 URL을 호출하여 등록하거나 등록을 취소합니다. |
| [**Recurrence**](#recurrence-trigger) | 정의된 일정에 따라 실행합니다. 이 트리거를 발생시키기 위한 미래의 날짜 및 시간을 설정할 수 있습니다. 빈도에 따라 워크플로를 실행할 시간 및 요일을 지정할 수도 있습니다. | 
| [**Request**](#request-trigger)  | 논리 앱에 대한 호출 가능 끝점을 만들며 “수동” 트리거로도 알려져 있습니다. 예를 들어 [HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. | 
||| 

### <a name="managed-api-triggers"></a>관리되는 API 트리거

| 트리거 유형 | 설명 | 
|--------------|-------------| 
| [**ApiConnection**](#apiconnection-trigger) | [Microsoft 관리 API](../connectors/apis-list.md)를 사용하여 끝점을 검사하거나 *폴링*합니다. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | 구독 및 구독을 취소하는 [Microsoft 관리 API](../connectors/apis-list.md)를 호출하여 논리 앱에 대한 호출 가능 끝점을 만듭니다. | 
||| 

## <a name="triggers---detailed-reference"></a>트리거 - 자세한 참조

<a name="apiconnection-trigger"></a>

### <a name="apiconnection-trigger"></a>APIConnection 트리거  

이 트리거는 [Microsoft 관리 API](../connectors/apis-list.md)를 사용하여 끝점을 검사하거나 *폴링*하므로 이 트리거의 매개 변수는 끝점에 따라 다를 수 있습니다. 이 트리거 정의의 여러 섹션은 선택 사항입니다. 트리거의 동작은 섹션은 포함 여부에 따라 달라집니다.

```json
"<APIConnection_trigger_name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": { "<retry-behavior>" },
      "queries": { "<query-parameters>" }
   },
   "recurrence": { 
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*APIConnection_trigger_name*> | String | 트리거의 이름입니다. | 
| <*connection-name*> | String | 워크플로에서 사용하는 관리되는 API 연결의 이름입니다. | 
| <*method-type*> | String | 관리되는 API와 통신하기 위한 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*api-operation*> | String | 호출할 API 작업입니다. | 
| <*time-unit*> | String | 트리거가 실행되는 빈도를 설명하는 시간 단위로, “초”, “분”, “시간”, “일”, “주” 또는 “월”이 있습니다. | 
| <*number-of-time-units*> | 정수  | 트리거가 다시 실행될 때까지 기다리는 시간 단위 수를 나타내는 빈도에 따라 트리거가 얼마나 자주 발생하는지를 지정하는 값입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어, 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*query-parameters*> | JSON 개체 | API 호출에 포함하려는 쿼리 매개 변수입니다. 예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*max-runs*> | 정수  | 기본적으로 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*splitOn-expression*> | String | 배열을 반환하는 트리거의 경우 이 식은 사용할 배열을 참조하므로, "for each" 루프를 사용하는 대신, 각 배열 항목에 대해 워크플로 인스턴스를 만들고 실행할 수 있습니다. <p>예를 들어, 이 식은 트리거 본문 콘텐츠 내에서 반환되는 배열의 항목을 나타냅니다. `@triggerbody()?['value']` |
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. |
||||

*Outputs*
 
| 요소 | Type | 설명 |
|---------|------|-------------|
| headers | JSON 개체 | 응답의 헤더입니다. |
| 본문 | JSON 개체 | 응답의 본문입니다. |
| 상태 코드 | 정수  | 응답의 상태 코드 |
|||| 

*예제*

이 트리거 정의는 Office 365 Outlook 계정의 받은 편지함 내에서 매일 이메일을 확인합니다. 

```json
"When_a_new_email_arrives": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "get",
      "path": "/Mail/OnNewEmail",
      "queries": {
          "fetchOnlyWithAttachment": false,
          "folderPath": "Inbox",
          "importance": "Any",
          "includeAttachments": false
      }
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

<a name="apiconnectionwebhook-trigger"></a>

### <a name="apiconnectionwebhook-trigger"></a>ApiConnectionWebhook 트리거

이 트리거는 [Microsoft 관리 API](../connectors/apis-list.md)를 사용하여 끝점으로 구독 요청을 전송하고, 끝점이 응답을 전송할 수 있는 *콜백 URL*을 제공하고, 끝점이 응답할 때까지 기다립니다. 자세한 내용은 [끝점 구독](#subscribe-unsubscribe)을 참조하세요.

```json
"<ApiConnectionWebhook_trigger_name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      }
   },
   "splitOn": "<splitOn-expression>",
   "operationOptions": "<operation-option>"
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*connection-name*> | String | 워크플로에서 사용하는 관리되는 API 연결의 이름입니다. | 
| <*body-content*> | JSON 개체 | 관리되는 API에 페이로드로 전송될 메시지 콘텐츠입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*query-parameters*> | JSON 개체 | API 호출에 포함하려는 쿼리 매개 변수입니다. <p>예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*max-runs*> | 정수  | 기본적으로 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*splitOn-expression*> | String | 배열을 반환하는 트리거의 경우 이 식은 사용할 배열을 참조하므로, "for each" 루프를 사용하는 대신, 각 배열 항목에 대해 워크플로 인스턴스를 만들고 실행할 수 있습니다. <p>예를 들어, 이 식은 트리거 본문 콘텐츠 내에서 반환되는 배열의 항목을 나타냅니다. `@triggerbody()?['value']` |
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

*예제*

이 트리거 정의는 Office 365 Outlook API를 구독하고, API 끝점에 대한 콜백 URL을 제공하고, 새 이메일이 도착할 때 끝점이 응답할 때까지 기다립니다.

```json
"When_a_new_email_arrives_(webhook)": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "body": {
         "NotificationUrl": "@{listCallbackUrl()}" 
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "path": "/MailSubscription/$subscriptions",
      "queries": {
          "folderPath": "Inbox",
          "hasAttachment": "Any",
          "importance": "Any"
      }
   },
   "splitOn": "@triggerBody()?['value']"
}
```

<a name="http-trigger"></a>

### <a name="http-trigger"></a>HTTP 트리거

이 트리거는 지정된 되풀이 일정에 따라 지정된 끝점을 검사하거나 폴링합니다. 끝점의 응답은 워크플로가 실행될지 여부를 결정합니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<endpoint-URL>",
      "headers": { "<header-content>" },
      "body": "<body-content>",
      "authentication": { "<authentication-method>" },
      "retryPolicy": { "<retry-behavior>" },
      "queries": "<query-parameters>"
   },
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*method-type*> | String | 지정된 끝점을 폴링하는 데 사용할 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*endpoint-URL*> | String | 끝점이 폴링할 HTTP 또는 HTTPS URL입니다. <p>최대 문자열 크기: 2KB | 
| <*time-unit*> | String | 트리거가 실행되는 빈도를 설명하는 시간 단위로, “초”, “분”, “시간”, “일”, “주” 또는 “월”이 있습니다. | 
| <*number-of-time-units*> | 정수  | 트리거가 다시 실행될 때까지 기다리는 시간 단위 수를 나타내는 빈도에 따라 트리거가 얼마나 자주 발생하는지를 지정하는 값입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어, 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*header-content*> | JSON 개체 | 요청과 함께 보낼 헤더입니다. <p>예를 들어 요청에 대한 언어 및 유형을 설정하려면 다음을 수행합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | String | 요청과 함께 페이로드로 전송할 메시지 콘텐츠입니다. | 
| <*authentication-method*> | JSON 개체 | 요청이 인증에 사용하는 메서드입니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. Scheduler 외에 `authority` 속성이 지원됩니다. 지정되지 않은 경우 기본값은 `https://login.windows.net`이지만, `https://login.windows\-ppe.net`과 같이 다른 값을 사용할 수 있습니다. |
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. |  
 <*query-parameters*> | JSON 개체 | 요청에 포함할 쿼리 매개 변수입니다. <p>예를 들어, `"queries": { "api-version": "2018-01-01" }` 개체는 요청에 `?api-version=2018-01-01`을 추가합니다. | 
| <*max-runs*> | 정수  | 기본적으로 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

*Outputs*

| 요소 | Type | 설명 |
|---------|------|-------------| 
| headers | JSON 개체 | 응답의 헤더입니다. | 
| 본문 | JSON 개체 | 응답의 본문입니다. | 
| 상태 코드 | 정수  | 응답의 상태 코드 | 
|||| 

*들어오는 요청에 대한 요구 사항*

논리 앱에서 잘 작동하려면 끝점은 특정 트리거 패턴 또는 계약을 준수하고, 다음 속성을 인식해야 합니다.  
  
| response | 필수 | 설명 | 
|----------|----------|-------------| 
| 상태 코드 | 예 | “200 확인” 상태 코드가 실행을 시작합니다. 다른 상태 코드는 실행을 시작하지 않습니다. | 
| Retry-after 헤더 | 아닙니다. | 논리 앱 끝점을 다시 폴링할 때까지 시간 (초) 수 | 
| 위치 헤더 | 아닙니다. | 다음 폴링 간격에서 호출할 URL입니다. 지정하지 않으면 원래 URL이 사용됩니다. | 
|||| 

*다른 요청에 대한 예제 동작*

| 상태 코드 | 다음 후 다시 시도 | 동작 | 
|-------------|-------------|----------|
| 200 | {없음} | 워크플로를 실행한 다음, 정의된 되풀이 이후에 데이터가 더 있는지를 다시 확인합니다. | 
| 200 | 10초 | 워크플로를 실행한 다음, 10초 후에 데이터가 더 있는지를 다시 확인합니다. |  
| 202 | 60초 | 워크플로를 트리거하지 않습니다. 정의된 되풀이 간격에 따라 1분 후에 다음 시도를 합니다. 정의된 되풀이 간격이 1분 이내이면 retry-after 헤더가 우선적으로 적용됩니다. 그렇지 않으면 정의된 되풀이 간격이 사용됩니다. | 
| 400 | {없음} | 잘못된 요청이며 워크플로를 실행하지 않습니다. `retryPolicy`가 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거는 정의된 되풀이 이후에 데이터가 더 있는지 다시 확인합니다. | 
| 500 | {없음}| 서버 오류이며 워크플로를 실행하지 않습니다. `retryPolicy`가 정의되지 않으면 기본 정책이 사용됩니다. 다시 시도 횟수에 도달하면 트리거는 정의된 되풀이 이후에 데이터가 더 있는지 다시 확인합니다. | 
|||| 

<a name="http-webhook-trigger"></a>

### <a name="httpwebhook-trigger"></a>HTTPWebhook 트리거  

이 트리거는 지정된 끝점 URL을 호출하여 구독을 등록할 수 있는 끝점을 만들어 논리 앱이 호출 가능해지도록 만듭니다. 워크플로에서 이 트리거를 만들 때 나가는 요청은 호출을 수행하여 구독을 등록합니다. 이러한 방식으로 트리거는 이벤트 수신을 시작할 수 있습니다. 작업에서 이 트리거가 유효해지지 않을 경우 나가는 요청은 자동으로 호출을 수행하여 구독을 취소합니다. 자세한 내용은 [끝점 구독](#subscribe-unsubscribe)을 참조하세요.

**HTTPWebhook** 트리거에 대해 [비동기 제한](#asynchronous-limits)을 지정할 수도 있습니다.
트리거 동작은 사용하거나 생략하는 섹션에 따라 달라집니다. 

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<endpoint-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": { "<retry-behavior>" }
         },
      },
      "unsubscribe": {
         "method": "<method-type>",
         "url": "<endpoint-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" }
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

일부 값(예: <*method-type*>)은 `"subscribe"` 및 `"unsubscribe"` 개체 둘 다에 사용할 수 있습니다.

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*method-type*> | String | 구독 요청에 사용되는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*endpoint-subscribe-URL*> | String | 구독 요청을 전송할 끝점 URL입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*method-type*> | String | 취소 요청에 사용하는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*endpoint-unsubscribe-URL*> | String | 취소 요청을 보낼 끝점 URL입니다. | 
| <*body-content*> | String | 구독 또는 취소 요청에 전송할 메시지 콘텐츠입니다. | 
| <*authentication-method*> | JSON 개체 | 요청이 인증에 사용하는 메서드입니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*max-runs*> | 정수  | 기본적으로 모든 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

*Outputs* 

| 요소 | Type | 설명 |
|---------|------|-------------| 
| headers | JSON 개체 | 응답의 헤더입니다. | 
| 본문 | JSON 개체 | 응답의 본문입니다. | 
| 상태 코드 | 정수  | 응답의 상태 코드 | 
|||| 

*예제*

이 트리거는 지정된 끝점에 대한 구독을 만들고, 고유한 콜백 URL을 제공하고, 새로 게시된 기술 문서를 기다립니다.

```json
"HTTP_Webhook": {
   "type": "HttpWebhook",
   "inputs": {
      "subscribe": {
         "method": "POST",
         "uri": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{listCallbackUrl()}",
            "hub.mode": "subscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         }
      }
   }
}
```

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>되풀이 트리거  

이 트리거는 지정된 되풀이 일정에 따라 실행되고, 주기적으로 실행되는 워크플로를 만드는 편리한 방법을 제공합니다. 

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-runs-queue>
      }
   },
   "operationOptions": "<operation-option>"
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*time-unit*> | String | 트리거가 실행되는 빈도를 설명하는 시간 단위로, “초”, “분”, “시간”, “일”, “주” 또는 “월”이 있습니다. | 
| <*number-of-time-units*> | 정수  | 트리거가 다시 실행될 때까지 기다리는 시간 단위 수를 나타내는 빈도에 따라 트리거가 얼마나 자주 발생하는지를 지정하는 값입니다. <p>다음은 최소 및 최대 간격입니다. <p>- 월: 1-16개월 </br>- 일: 1-500일 </br>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어, 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*start-date-time-with-format-YYYY-MM-DDThh:mm:ss*> | String | 다음 형식의 시작 날짜 및 시간: <p>YYYY-MM-DDThh:mm:ss(표준 시간대를 지정하는 경우) <p>또는 <p>YYYY-MM-DDThh:mm:ssZ(표준 시간대를 지정하지 않는 경우) <p>따라서 예를 들어 2017년 9월 18일, 오후 2시를 원할 경우 “2017-09-18T14:00:00”을 지정하고 “태평양 표준시”와 같은 표준 시간대를 지정하거나 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜 시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜 시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 지정하지 않으면 공백 없이 맨 끝에 문자 "Z"를 추가해야 합니다. 이 "Z"는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간이 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. 시작 날짜 및 시간에 대한 자세한 내용은 [정기적으로 실행되는 작업 만들기 및 예약](../connectors/connectors-native-recurrence.md)을 참조하세요. | 
| <*time-zone*> | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 지정합니다. | 
| <*one-or-more-hour-marks*> | 정수 또는 정수 배열 | `frequency`에 대해 "Day" 또는 "Week"를 지정하는 경우 0~23 사이의 정수 하나 이상을 쉼표로 구분해서 워크플로를 실행하려는 시간으로 지정할 수 있습니다. <p>예를 들어 “10”, “12” 및 “14”를 지정하면 10 AM, 12 PM 및 2 PM이 시간 표시로 제공됩니다. | 
| <*one-or-more-minute-marks*> | 정수 또는 정수 배열 | `frequency`에 대해 "Day" 또는 "Week"를 지정하는 경우 0~59 사이의 정수 하나 이상을 쉼표로 구분해서 워크플로를 실행하려는 분으로 지정할 수 있습니다. <p>예를 들어 분 표시로 “30”을 지정하고, 앞에 나온 하루 시간 예제를 사용하면 10:30 AM, 12:30 PM 및 2:30 PM이 표시됩니다. | 
| weekDays | 문자열 또는 문자열 배열 | `frequency`에 대해 “Week”를 지정하는 경우 워크플로를 실행하려는 경우 하나 이상의 요일을 쉼표로 구분해서 지정할 수 있습니다. 예를 들면 "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"와 같이 지정합니다. | 
| <*max-runs*> | 정수  | 기본적으로 모든 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

*예 1*

이 기본 되풀이 트리거가 매일 실행됩니다.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*예 2*

트리거를 실행할 시작 날짜 및 시간을 설정할 수 있습니다. 이 되풀이 트리거는 지정된 날짜에 시작한 다음, 매일 실행됩니다.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*예 3*

이 되풀이 트리거는 2017년 9월 9일 오후 2시에 시작하고, 매주 월요일 오전 10시 30분, 오후 12시 30분, 오후 2시 30분에 실행됩니다(태평양 표준시).

``` json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

이 트리거에 대한 자세한 정보 및 예제는 [정기적으로 실행되는 작업 만들기 및 예약](../connectors/connectors-native-recurrence.md)을 참조하세요.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>요청 트리거

이 트리거는 들어오는 요청을 수락할 수 있는 끝점을 만들어서 논리 앱을 호출할 수 있게 합니다. 이 트리거의 경우 트리거가 들어오는 요청에서 수신하는 입력 또는 페이로드에 대해 설명하고 유효성을 검사하는 JSON 스키마를 제공합니다. 또한 이 스키마는 워크플로의 이후 작업에서 트리거 속성을 보다 쉽게 참조할 수 있도록 합니다. 

이 트리거를 호출하려면 [워크플로 서비스 REST API](https://docs.microsoft.com/rest/api/logic/workflows)에 설명된 `listCallbackUrl` API를 사용해야 합니다. HTTP 엔드포인트로 이 트리거를 사용하는 방법을 알아보려면 [HTTP 엔드포인트를 사용하여 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "<method-type>",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<property-name>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   },
   "runTimeConfiguration": {
      "concurrency": {
         "runs": <max-runs>,
         "maximumWaitingRuns": <max-run-queue>
      },
   },
   "operationOptions": "<operation-option>"
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*property-name*> | String | 페이로드를 설명하는 JSON 스키마의 속성 이름입니다. | 
| <*property-type*> | String | 속성의 형식입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*method-type*> | String | 들어오는 요청에서 논리 앱을 호출하는 데 사용해야 하는 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” |
| <*relative-path-for-accepted-parameter*> | String | 끝점의 URL이 수락할 수 있는 매개 변수에 대한 상대 경로입니다. | 
| <*required-properties*> | 배열 | 값이 필요한 하나 이상의 속성 | 
| <*max-runs*> | 정수  | 기본적으로 모든 워크플로 인스턴스 실행을 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency)을 참조하세요. | 
| <*max-runs-queue*> | 정수  | 워크플로 인스턴스의 최대 수를 이미 실행 중인 경우에 따라 변경할 수 있습니다는 `runtimeConfiguration.concurrency.runs` 속성을 모든 새 실행은이 큐에 추가이는 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 
| <*operation-option*> | String | `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

*예제*

이 트리거는 들어오는 요청이 HTTP POST 메서드를 사용하여 트리거를 호출해야 하고, 들어오는 요청에서 입력의 유효성을 검사하는 스키마를 포함합니다. 

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "string"
                  },
                  "city": {
                     "type": "string"
                  }
               }
            }
         }
      }
   }
}
```

<a name="trigger-conditions"></a>

## <a name="trigger-conditions"></a>트리거 조건

트리거의 경우(트리거에 한해) 워크플로의 실행 여부를 결정하는 조건에 대한 하나 이상의 식이 들어 있는 배열을 포함할 수 있습니다. 추가 된 `conditions` 워크플로에서 트리거 속성 코드 보기 편집기에서 논리 앱을 엽니다.

예를 들어, `conditions` 속성에서 트리거의 상태 코드를 참조하여 웹 사이트가 내부 서버 오류를 반환할 때만 트리거가 발생하도록 지정할 수 있습니다.

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Hour",
      "interval": 1
   },
   "conditions": [ {
      "expression": "@equals(triggers().code, 'InternalServerError')"
   } ]
}
```

기본적으로 트리거는 "200 정상" 응답을 받은 경우에만 트리거됩니다. 식에서 트리거의 상태 코드를 참조하는 경우 트리거의 기본 동작이 바뀝니다. 따라서 "200" 및 "201"과 같은 둘 이상의 상태 코드에 대해 트리거가 발생하도록 하려면 다음 식을 조건으로 포함해야 합니다. 

`@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="trigger-multiple-runs"></a>여러 실행 트리거

트리거에서 논리 앱이 처리할 배열을 반환하는 경우, "for each" 루프에서 각 배열 항목을 처리하는 데 너무 오래 걸리는 경우가 있습니다. 대신, 트리거에 **SplitOn** 속성을 사용하여 배열을 *분리 처리(debatch)* 합니다. 분리는 배열 항목으로 분할 하 고 각 배열 항목에 대해 실행 되는 새 워크플로 인스턴스를 시작 합니다. 예를 들어 이 방법은 폴링 간격 사이에 여러 개의 새 항목을 반환할 수 있는 엔드포인트를 폴링하려는 경우에 유용합니다.
**SplitOn**이 단일 논리 앱 실행에서 처리할 수 있는 배열 항목의 최대 개수는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)을 참조하세요. 

> [!NOTE]
> 동기 응답 패턴에는 **SplitOn**을 사용할 수 없습니다. **SplitOn**을 사용하고 응답 작업을 포함하는 모든 워크플로는 비동기적으로 실행되고 즉시 `202 ACCEPTED` 응답을 보냅니다.

트리거의 Swagger 파일에서 배열인 페이로드를 설명하고 있으면 **SplitOn** 속성이 자동으로 트리거에 추가됩니다. 그렇지 않으면 분리 처리할 배열이 있는 응답 페이로드 내에 이 속성을 추가합니다. 

*예제*

이 응답을 반환하는 API가 있다고 가정합니다. 
  
```json
{
   "Status": "Succeeded",
   "Rows": [ 
      { 
         "id": 938109380,
         "name": "customer-name-one"
      },
      {
         "id": 938109381,
         "name": "customer-name-two"
      }
   ]
}
```
 
논리 앱에는 `Rows`의 배열 콘텐츠만 필요하므로 다음 예제와 같은 트리거를 만들 수 있습니다.

``` json
"HTTP_Debatch": {
   "type": "Http",
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
   "recurrence": {
      "frequency": "Second",
      "interval": 1
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> `SplitOn` 명령을 사용하는 경우 배열 외부에 있는 속성을 가져올 수 없습니다. 따라서 이 예제에서는 API에서 반환된 응답에서 `status` 속성을 가져올 수 없습니다.
> 
> `Rows` 속성이 없는 경우 오류를 방지하기 위해 이 예제에서는 `?` 연산자를 사용하고 있습니다.

이제 워크플로 정의에서 `@triggerBody().name`을 사용하여 첫 번째 실행에서 `"customer-name-one"`, 두 번째 실행에서 `"customer-name-two"`에 해당하는 `name` 값을 가져올 수 있습니다. 따라서 트리거 출력의 예제는 다음과 같습니다.

```json
{
   "body": {
      "id": 938109380,
      "name": "customer-name-one"
   }
}
```

```json
{
   "body": {
      "id": 938109381,
      "name": "customer-name-two"
   }
}
```

<a name="actions-overview"></a>

## <a name="actions-overview"></a>작업 개요

Azure Logic Apps는 각각이 작업의 고유한 동작을 정의하는 다른 입력을 받는 다양한 작업 형식을 제공합니다. 

작업에는 이러한 최상위 요소가 있습니다. 단, 일부는 선택 사항입니다.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { 
      "<input-name>": { "<input-value>" },
      "retryPolicy": "<retry-behavior>" 
   },
   "runAfter": { "<previous-trigger-or-action-status>" },
   "runtimeConfiguration": { "<runtime-config-options>" },
   "operationOptions": "<operation-option>"
},
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------|
| <*action-name*> | String | 작업의 이름입니다. | 
| <*action-type*> | String | 작업 유형, 예: “Http” 또는 “ApiConnection”| 
| <*input-name*> | String | 작업의 동작을 정의하는 입력의 이름입니다. | 
| <*input-value*> | 다양 | 입력 값(예: 문자열, 정수, JSON 개체)입니다. | 
| <*previous-trigger-or-action-status*> | JSON 개체 | 이 현재 작업이 실행되기 위해 먼저 실행되어야 하는 트리거 또는 작업에 대한 이름 및 결과 상태입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------|
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 다시 시도 정책을 참조하세요. | 
| <*runtime-config-options*> | JSON 개체 | 일부 작업의 경우 `runtimeConfiguration` 속성을 설정하여 런타임에 작업 동작을 변경할 수 있습니다. 자세한 내용은 [런타임 구성 설정](#runtime-config-options)을 참조하세요. | 
| <*operation-option*> | String | 일부 작업의 경우 `operationOptions` 속성을 설정하여 기본 동작을 변경할 수 있습니다. 자세한 내용은 [작업 옵션](#operation-options)을 참조하세요. | 
|||| 

## <a name="action-types-list"></a>작업 유형 목록

다음은 일반적으로 사용되는 작업 유형입니다. 

* [기본 제공 작업 유형](#built-in-actions)(다음 예제 참조) 

  * [**HTTP**](#http-action): HTTP 또는 HTTPS에 대해 끝점을 호출하는 데 사용됩니다.

  * [**Response**](#response-action): 요청에 응답하는 데 사용됩니다.

  * [**Function**](#function-action): Azure Functions를 호출하는 데 사용됩니다.

  * [**Join**](#join-action), [**Compose**](#compose-action), [**Table**](#table-action), [**Select**](#select-action)와 같은 데이터 작업 및 다양한 입력에서 데이터를 만들거나 변형하는 기타 작업

  * [**Workflow**](#workflow-action): 다른 논리 앱 워크플로를 호출하는 데 사용됩니다.

* [관리되는 API 작업 유형](#managed-api-actions)(예: [**ApiConnection**](#apiconnection-action) 및 [**ApiConnectionWebHook**](#apiconnectionwebhook-action)): Azure Service Bus, Office 365 Outlook, Power BI, Azure Blob Storage, OneDrive, GitHub 등, Microsoft에서 관리하는 다양한 커넥터 및 API를 호출합니다.

* [워크플로 제어 작업 유형](#control-workflow-actions)(예: [**If**](#if-action), [**Foreach**](#foreach-action), [**Switch**](#switch-action), [**Scope**](#scope-action) 및 [**Until**](#until-action)): 다른 작업을 포함하고, 워크플로 실행을 구성하는 데 도움을 줍니다.

<a name="built-in-actions"></a>

### <a name="built-in-actions"></a>기본 제공 작업

| 작업 유형 | 설명 | 
|-------------|-------------| 
| [**Compose**](#compose-action) | 입력에서 단일 출력을 만듭니다. 다양한 형식을 가질 수 있습니다. | 
| [**Function**](#function-action) | Azure 함수를 호출합니다. | 
| [**HTTP**](#http-action) | HTTP 끝점을 호출합니다. | 
| [**Join**](#join-action) | 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다. | 
| [**Parse JSON**](#parse-json-action) | JSON 콘텐츠의 속성에서 친숙한 토큰을 만듭니다. 논리 앱에 토큰을 포함하여 해당 속성을 참조할 수 있습니다. | 
| [**Query**](#query-action) | 조건 또는 필터를 기준으로 다른 배열에 있는 항목에서 배열을 만듭니다. | 
| [**Response**](#response-action) | 들어오는 호출 또는 요청에 대한 응답을 만듭니다. | 
| [**Select**](#select-action) | 지정된 맵을 기준으로 다른 배열의 항목을 변형하여 JSON 개체를 포함하는 배열을 만듭니다. | 
| [**Table**](#table-action) | 배열에서 CSV 또는 HTML 테이블을 만듭니다. | 
| [**Terminate**](#terminate-action) | 현재 실행 중인 워크플로를 중지합니다. | 
| [**Wait**](#wait-action) | 지정된 기간 동안 또는 지정된 날짜 및 시간까지 워크플로를 일시중지합니다. | 
| [**Workflow**](#workflow-action) | 다른 워크플로 내에 워크플로를 중첩합니다. | 
||| 

<a name="managed-api-actions"></a>

### <a name="managed-api-actions"></a>관리되는 API 작업

| 작업 유형 | 설명 | 
|-------------|-------------|  
| [**ApiConnection**](#apiconnection-action) | [Microsoft 관리 API](../connectors/apis-list.md)를 사용하여 HTTP 끝점을 호출합니다. | 
| [**ApiConnectionWebhook**](#apiconnectionwebhook-action) | HTTP 웹후크처럼 작동하지만 [Microsoft 관리 API](../connectors/apis-list.md)를 사용합니다. | 
||| 

<a name="control-workflow-actions"></a>

### <a name="control-workflow-actions"></a>워크플로 제어 작업

이러한 작업은 워크플로 실행을 제어하고 다른 작업을 포함하도록 도와줍니다. 워크플로 제어 작업 외부에서, 해당 워크플로 제어 작업 내부의 작업을 직접 참조할 수 있습니다. 예를 들어, `Http` 작업이 범위 내에 있는 경우 워크플로의 어디에서든지 `@body('Http')` 식을 참조할 수 있습니다. 그러나 워크플로 제어 작업 내에서 존재하는 작업은 동일한 워크플로 제작어 업 구조 내에 있는 다른 작업 "이후에만 실행"될 수 있습니다.

| 작업 유형 | 설명 | 
|-------------|-------------| 
| [**ForEach**](#foreach-action) | 배열에 있는 모든 항목에 대해 동일한 작업을 반복해서 실행합니다. | 
| [**If**](#if-action) | 지정된 조건이 true인지 또는 false인지에 따라 작업을 실행합니다. | 
| [**Scope**](#scope-action) | 작업 집합의 그룹 상태를 기준으로 작업을 실행합니다. | 
| [**Switch**](#switch-action) | 식, 개체 또는 토큰의 값이 각 사례에 지정된 값과 일치할 때 사례로 구성된 작업을 실행합니다. | 
| [**Until**](#until-action) | 지정된 조건이 충족될 때까지 작업을 반복해서 실행합니다. | 
|||  

## <a name="actions---detailed-reference"></a>작업 - 자세한 참조

<a name="apiconnection-action"></a>

### <a name="apiconnection-action"></a>APIConnection 작업

이 작업은 [Microsoft 관리 API](../connectors/apis-list.md)에 HTTP 요청을 전송하며, API 및 매개 변수에 대한 정보와 유효한 연결에 대한 참조가 필요합니다. 

``` json
"<action-name>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<api-name>']['connectionId']"
         },
         "<other-action-specific-input-properties>"        
      },
      "method": "<method-type>",
      "path": "/<api-operation>",
      "retryPolicy": "<retry-behavior>",
      "queries": { "<query-parameters>" },
      "<other-action-specific-properties>"
    },
    "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*action-name*> | String | 커넥터에서 제공하는 작업의 이름입니다. | 
| <*api-name*> | String | 연결에 사용되는 Microsoft 관리 API의 이름입니다. | 
| <*method-type*> | String | API를 호출하기 위한 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*api-operation*> | String | 호출할 API 작업입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*other-action-specific-input-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 입력 속성입니다. | 
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*query-parameters*> | JSON 개체 | API 호출에 포함하려는 쿼리 매개 변수입니다. <p>예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*other-action-specific-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 속성입니다. | 
|||| 

*예제*

이 정의는 Microsoft 관리 API에 해당하는 Office 365 Outlook 커넥터에 대한 **Send an email** 작업을 설명합니다. 

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "Thank you for your membership!",
         "Subject": "Hello and welcome!",
         "To": "Sophie.Owen@contoso.com"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "POST",
      "path": "/Mail"
    },
    "runAfter": {}
}
```

<a name="apiconnection-webhook-action"></a>

### <a name="apiconnectionwebhook-action"></a>APIConnectionWebhook 작업

이 작업은 [Microsoft 관리 API](../connectors/apis-list.md)를 사용하여 HTTP를 통해 끝점으로 구독 요청을 전송하고, 끝점이 응답을 전송할 수 있는 *콜백 URL*을 제공하고, 끝점이 응답할 때까지 기다립니다. 자세한 내용은 [끝점 구독](#subscribe-unsubscribe)을 참조하세요.

```json
"<action-name>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "subscribe": {
         "method": "<method-type>",
         "uri": "<api-subscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "retryPolicy": "<retry-behavior>",
         "queries": { "<query-parameters>" },
         "<other-action-specific-input-properties>"
      },
      "unsubscribe": {
         "method": "<method-type>",
         "uri": "<api-unsubscribe-URL>",
         "headers": { "<header-content>" },
         "body": "<body-content>",
         "authentication": { "<authentication-method>" },
         "<other-action-specific-properties>"
      },
   },
   "runAfter": {}
}
```

일부 값(예: <*method-type*>)은 `"subscribe"` 및 `"unsubscribe"` 개체 둘 다에 사용할 수 있습니다.

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*action-name*> | String | 커넥터에서 제공하는 작업의 이름입니다. | 
| <*method-type*> | String | 끝점을 구독하거나 끝점에서 구독 취소하는 데 사용하는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*api-subscribe-URL*> | String | API를 구독하는 데 사용할 URI입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*api-unsubscribe-URL*> | String | API에서 구독 취소하는 데 사용할 URI입니다. | 
| <*header-content*> | JSON 개체 | 요청에 전송할 헤더입니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON 개체 | 요청에 전송할 메시지 콘텐츠입니다. | 
| <*authentication-method*> | JSON 개체 | 요청이 인증에 사용하는 메서드입니다. 자세한 내용은 [스케줄러 아웃바운드 인증](../scheduler/scheduler-outbound-authentication.md)을 참조하세요. |
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*query-parameters*> | JSON 개체 | API 호출에 포함하려는 쿼리 매개 변수입니다. <p>예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*other-action-specific-input-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 입력 속성입니다. | 
| <*other-action-specific-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 속성입니다. | 
|||| 

[HTTP 비동기 제한](#asynchronous-limits)과 동일한 방식으로 **ApiConnectionWebhook** 작업에 대한 제한을 지정할 수도 있습니다.

<a name="compose-action"></a>

### <a name="compose-action"></a>작성 작업

이 작업은 식을 포함하는 여러 입력에서 단일 출력을 만듭니다. 출력 및 입력 둘 다 배열, JSON 개체, XML 및 이진 파일 등 Azure Logic Apps에서 기본적으로 지원하는 어떤 형식도 될 수 있습니다.
그런 후, 다른 작업에서 해당 작업의 출력을 사용할 수 있습니다. 

```json
"Compose": {
   "type": "Compose",
   "inputs": "<inputs-to-compose>",
   "runAfter": {}
},
```

*필수* 

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*inputs-to-compose*> | 모두 | 단일 출력을 만들기 위한 입력입니다. | 
|||| 

*예 1*

<!-- markdownlint-disable MD038 -->
이 작업 정의는 `abcdefg `를 후행 공백과 값 `1234`에 병합합니다.
<!-- markdownlint-enable MD038 -->

```json
"Compose": {
   "type": "Compose",
   "inputs": "abcdefg 1234",
   "runAfter": {}
},
```

이 작업이 만드는 출력은 다음과 같습니다.

`abcdefg 1234`

*예 2*

이 작업 정의는 `abcdefg`를 포함하는 문자열 변수와 `1234`를 포함하는 정수 변수를 병합합니다.

```json
"Compose": {
   "type": "Compose",
   "inputs": "@{variables('myString')}@{variables('myInteger')}",
   "runAfter": {}
},
```

이 작업이 만드는 출력은 다음과 같습니다.

`"abcdefg1234"`

<a name="function-action"></a>

### <a name="function-action"></a>Function 작업

이 작업은 이전에 만든 [Azure 함수](../azure-functions/functions-create-first-azure-function.md)를 호출합니다.

```json
"<Azure-function-name>": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "<Azure-function-ID>"
      },
      "method": "<method-type>",
      "headers": { "<header-content>" },
      "body": { "<body-content>" },
      "queries": { "<query-parameters>" } 
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------|  
| <*Azure-function-ID*> | String | 호출하려는 Azure 함수의 리소스 ID입니다. 이 값의 형식은 다음과 같습니다.<p>"/subscriptions/<*Azure-subscription-ID*>/resourceGroups/<*Azure-resource-group*>/providers/Microsoft.Web/sites/<*Azure-function-app-name*>/functions/<*Azure-function-name*>" | 
| <*method-type*> | String | 함수 호출에 사용하는 HTTP 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” <p>지정하지 않으면 기본값은 "POST" 메서드입니다. | 
||||

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------|  
| <*header-content*> | JSON 개체 | 호출과 함께 보낼 헤더입니다. <p>예를 들어 요청에 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON 개체 | 요청에 전송할 메시지 콘텐츠입니다. | 
| <*query-parameters*> | JSON 개체 | API 호출에 포함하려는 쿼리 매개 변수입니다. <p>예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*other-action-specific-input-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 입력 속성입니다. | 
| <*other-action-specific-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 속성입니다. | 
||||

논리 앱을 저장하면 Azure Logic Apps 엔진에서 참조된 함수에 대해 다음과 같은 검사를 수행합니다.

* 워크플로는 함수에 액세스할 수 있어야 합니다.

* 워크플로는 표준 HTTP 트리거 또는 일반 JSON 웹후크 트리거만 사용할 수 있습니다. 

  Logic Apps 엔진은 런타임에 사용되는 트리거 URL을 가져오고 캐시합니다. 그렇지만 작업에서 캐시된 URL이 무효화되면 런타임에 **Function** 작업이 실패합니다. 이 문제를 해결하려면 논리 앱이 트리거 URL을 다시 가져오고 캐시할 수 있도록 논리 앱을 다시 저장합니다.

* 함수에는 경로가 정의될 수 없습니다.

* "함수" 및 "익명" 권한 부여 수준만 허용됩니다. 

*예제*

이 작업 정의는 이전에 만든 "GetProductID" 함수를 호출합니다.

```json
"GetProductID": {
   "type": "Function",
   "inputs": {
     "function": {
        "id": "/subscriptions/<XXXXXXXXXXXXXXXXXXXX>/resourceGroups/myLogicAppResourceGroup/providers/Microsoft.Web/sites/InventoryChecker/functions/GetProductID"
      },
      "method": "POST",
      "headers": { 
          "x-ms-date": "@utcnow()"
       },
      "body": { 
          "Product_ID": "@variables('ProductID')"
      }
   },
   "runAfter": {}
}
```

<a name="http-action"></a>

### <a name="http-action"></a>HTTP 동작

이 작업은 지정된 끝점으로 요청을 전송하고 응답을 확인하여 워크플로를 실행할지 여부를 결정합니다. 

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "<method-type>",
      "uri": "<HTTP-or-HTTPS-endpoint-URL>"
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*method-type*> | String | 요청을 보내는 데 사용하는 메서드: “GET”, “PUT”, “POST”, “PATCH”, “DELETE” | 
| <*HTTP-or-HTTPS-endpoint-URL*> | String | 호출할 HTTP 또는 HTTPS 끝점입니다. 최대 문자열 크기: 2KB | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*header-content*> | JSON 개체 | 요청과 함께 보낼 헤더입니다. <p>예를 들어 언어 및 형식을 설정하려면 다음과 같이 합니다. <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` |
| <*body-content*> | JSON 개체 | 요청에 전송할 메시지 콘텐츠입니다. | 
| <*retry-behavior*> | JSON 개체 | 상태 코드 408, 429 및 5XX와 연결 예외가 있는 일시적 오류에 대한 재시도 동작을 사용자 지정합니다. 자세한 내용은 [다시 시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요. | 
| <*query-parameters*> | JSON 개체 | 요청에 포함할 쿼리 매개 변수입니다. <p>예를 들어 `"queries": { "api-version": "2018-01-01" }` 개체는 호출에 `?api-version=2018-01-01`을 추가합니다. | 
| <*other-action-specific-input-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 입력 속성입니다. | 
| <*other-action-specific-properties*> | JSON 개체 | 이 특정 작업에 적용되는 다른 속성입니다. | 
|||| 

*예제*

이 작업 정의는 지정된 끝점에 요청을 보내 최신 뉴스를 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest"
   }
}
```

<a name="join-action"></a>

### <a name="join-action"></a>Join 작업

이 작업은 배열의 모든 항목에서 문자열을 만들고, 지정한 구분 기호를 사용하여 해당 항목을 구분합니다. 

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": <array>,
      "joinWith": "<delimiter>"
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*array*> | 배열 | 원본 항목을 제공하는 배열 또는 식입니다. 식을 지정할 때는 큰따옴표로 식을 묶습니다. | 
| <*delimiter*> | 단일 문자 문자열 | 문자열의 각 항목을 구분하는 문자입니다. | 
|||| 

*예제*

이 정수 배열이 포함된 이전에 만든 "myIntegerArray" 변수가 있다고 가정합니다. 

`[1,2,3,4]` 

이 작업 정의는 식에서 `variables()` 함수를 사용하여 변수에서 값을 가져오고 해당 값을 쉼표로 구분하여 문자열 `"1,2,3,4"`를 만듭니다.

```json
"Join": {
   "type": "Join",
   "inputs": {
      "from": "@variables('myIntegerArray')",
      "joinWith": ","
   },
   "runAfter": {}
}
```

<a name="parse-json-action"></a>

### <a name="parse-json-action"></a>Parse JSON 작업

이 작업은 JSON 콘텐츠의 속성에서 사용자에게 친숙한 필드 또는 *토큰*을 만듭니다. 그러면 토큰을 대신 사용하여 논리 앱에서 해당 속성에 액세스할 수 있습니다. 예를 들어, Azure Service Bus 및 Azure Cosmos DB와 같은 서비스에서 JSON 출력을 사용하려는 경우 해당 출력에서 데이터를 보다 쉽게 참조할 수 있도록 논리 앱에 이 작업을 포함할 수 있습니다. 

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": "<JSON-source>",
         "schema": { "<JSON-schema>" }
      },
      "runAfter": {}
},
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*JSON-source*> | JSON 개체 | 구문 분석할 JSON 콘텐츠입니다. | 
| <*JSON-schema*> | JSON 개체 | 기본 작업이 원본 JSON 콘텐츠를 구문 분석하는 데 사용하는 기본 JSON 콘텐츠를 설명하는 JSON 스키마입니다. <p>**팁**: Logic Apps 디자이너에서 스키마를 제공하거나 작업에서 스키마를 생성할 수 있도록 샘플 페이로드를 제공할 수 있습니다. | 
|||| 

*예제*

이 작업 정의 사용 하 여 워크플로에서 있지만 작업 에서만에서 실행된 다음 이러한 토큰을 만듭니다는 **JSON 구문 분석** 작업: 

`FirstName`, `LastName`, `Email`

```json
"Parse_JSON": {
   "type": "ParseJson",
   "inputs": {
      "content": {
         "Member": {
            "Email": "Sophie.Owen@contoso.com",
            "FirstName": "Sophie",
            "LastName": "Owen"
         }
      },
      "schema": {
         "type": "object",
         "properties": {
            "Member": {
               "type": "object",
               "properties": {
                  "Email": {
                     "type": "string"
                  },
                  "FirstName": {
                     "type": "string"
                  },
                  "LastName": {
                     "type": "string"
                  }
               }
            }
         }
      }
   },
   "runAfter": { }
},
```

이 예제에서 "content" 속성은 작업이 구문 분석할 JSON 콘텐츠를 지정합니다. 이 JSON 콘텐츠를 스키마를 생성하기 위한 샘플 페이로드로 제공할 수도 있습니다.

```json
"content": {
   "Member": { 
      "FirstName": "Sophie",
      "LastName": "Owen",
      "Email": "Sophie.Owen@contoso.com"
   }
},
```

"schema" 속성은 JSON 콘텐츠를 설명하는 데 사용되는 JSON 스키마를 지정합니다.

```json
"schema": {
   "type": "object",
   "properties": {
      "Member": {
         "type": "object",
         "properties": {
            "FirstName": {
               "type": "string"
            },
            "LastName": {
               "type": "string"
            },
            "Email": {
               "type": "string"
            }
         }
      }
   }
}
```

<a name="query-action"></a>

### <a name="query-action"></a>쿼리 작업

이 작업에서는 지정된 조건 또는 필터를 기준으로 다른 배열에 있는 항목에서 배열을 만듭니다.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": <array>,
      "where": "<condition-or-filter>"
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*array*> | 배열 | 원본 항목을 제공하는 배열 또는 식입니다. 식을 지정할 때는 큰따옴표로 식을 묶습니다. |
| <*condition-or-filter*> | String | 원본 배열에서 항목을 필터링하는 데 사용되는 조건입니다. <p>**참고**: 조건을 충족하는 값이 없는 경우 작업은 빈 배열을 만듭니다. |
|||| 

*예제*

이 작업 정의는 지정된 값(2)보다 더 큰 값을 갖는 배열을 만듭니다.

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": [ 1, 3, 0, 5, 4, 2 ],
      "where": "@greater(item(), 2)"
   }
}
```

<a name="response-action"></a>

### <a name="response-action"></a>응답 작업  

이 작업은 HTTP 요청에 대한 응답의 페이로드를 만듭니다. 

```json
"Response" {
    "type": "Response",
    "kind": "http",
    "inputs": {
        "statusCode": 200,
        "headers": { <response-headers> },
        "body": { <response-body> }
    },
    "runAfter": {}
},
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*response-status-code*> | 정수  | 들어오는 요청으로 전송되는 HTTP 상태 코드입니다. 기본 코드는 "200 정상"이지만 3xxx를 제외하고 2xx, 4xx 또는 5xx로시작 되는 어떤 유효한 상태 코드도 될 수 있습니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*response-headers*> | JSON 개체 | 응답에 포함할 하나 이상의 헤더입니다. | 
| <*response-body*> | 다양 | 이전 작업의 문자열, JSON 개체 또는 이진 콘텐츠일 수 있는 응답 본문입니다. | 
|||| 

*예제*

이 작업 정의는 지정된 상태 코드, 메시지 본문 및 메시지 헤더를 사용하여 HTTP 요청에 대한 응답을 만듭니다.

```json
"Response": {
   "type": "Response",
   "inputs": {
      "statusCode": 200,
      "body": {
         "ProductID": 0,
         "Description": "Organic Apples"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "content-type": "application/json"
      }
   },
   "runAfter": {}
}
```

*제한 사항*

다른 작업과 달리, **Response** 작업에는 다음과 같은 특수한 제한이 있습니다. 

* 워크플로는 HTTP 요청 트리거로 시작될 때만 **Response** 작업을 사용할 수 있습니다. 즉, 워크플로가 HTTP 요청에 의해 트리거되어야 합니다.

* 워크플로는 순차 루프 및 병렬 분기를 포함하여 **Foreach** 루프, **Until** 루프 내부를 *제외하고* 어디에서든지 **Response** 작업을 사용할 수 있습니다. 

* 원래 HTTP 요청은 **Response** 작업에서 요청된 모든 작업이 [HTTP 요청 시간 제한](../logic-apps/logic-apps-limits-and-config.md#request-limits) 내에서 완료될 때만 워크플로의 응답을 가져옵니다.

  그러나 워크플로가 다른 논리 앱을 중첩된 워크플로로 호출할 경우, 중첩된 워크플로가 완료되기 전에 얼마나 오랜 시간이 경과되는지에 관계없이 부모 워크플로는 중첩된 워크플로가 완료될 때까지 기다립니다.

* 워크플로가 **Response** 작업 및 동기 응답 패턴을 사용할 경우 **splitOn** 명령이 여러 실행을 만들기 때문에 워크플로는 트리거 정의에서 이 명령을 사용할 수 없습니다. PUT 메서드가 사용될 경우 이러한 경우인지 확인하고, 해당 경우에는 "잘못된 요청" 응답을 반환합니다.

  그렇지 않고, 워크플로에서 **splitOn** 명령 및 **Response** 작업을 사용할 경우 워크플로는 비동기식으로 실행되며 즉시 "202 수락됨" 응답을 반환합니다.

* 워크플로 실행이 **Response** 작업에 도달하지만 들어오는 요청이 이미 응답을 수신한 경우 **Response** 작업은 충돌로 인해 "실패"로 표시됩니다. 따라서 논리 앱 실행도 “실패” 상태로 표시됩니다.

<a name="select-action"></a>

### <a name="select-action"></a>작업 선택

이 작업은 지정된 맵을 기준으로 다른 배열의 항목을 변형하여 JSON 개체를 포함하는 배열을 만듭니다. 출력 배열 및 원본 배열은 항상 동일한 항목 수를 갖습니다. 출력 배열의 개체 수를 변경할 수 없지만 해당 개체에서 속성 및 값을 추가하거나 제거할 수 있습니다. `select` 속성은 원본 배열에서 항목을 변환하기 위한 맵을 정의하는 하나 이상의 키-값 쌍을 지정합니다. 키-값 쌍은 출력 배열에 있는 모든 개체에서 속성 및 해당 값을 나타냅니다. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": <array>,
      "select": { 
          "<key-name>": "<expression>",
          "<key-name>": "<expression>"        
      }
   },
   "runAfter": {}
},
```

*필수* 

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*array*> | 배열 | 원본 항목을 제공하는 배열 또는 식입니다. 식을 큰따옴표로 묶어야 합니다. <p>**참고**: 원본 배열이 비어 있는 경우 작업은 빈 배열을 만듭니다. | 
| <*key-name*> | String | <*expression*> 의 결과에 할당되는 속성 이름입니다.<p>출력 배열의 모든 개체에 새 속성을 추가하려면 해당 속성의 <*key-name*>과 해당 속성 값의 <*expression*>을 제공합니다. <p>배열의 모든 개체에서 속성을 제거하려면 해당 속성의 <*key-name*>을 생략합니다. | 
| <*expression*> | String | 원본 배열에서 항목을 변환하고 결과를 <*key-name*>에 할당하는 식입니다. | 
|||| 

**Select** 작업은 배열을 출력으로 만들기 때문에 이 출력을 사용하려는 모든 작업은 배열을 수락해야 하고, 그렇지 않은 경우 사용자가 소비자 작업이 수락하는 형식으로 배열을 변환해야 합니다. 예를 들어, 출력 배열을 문자열로 변환하려면 해당 배열을 **Compose** 작업으로 전달한 후 **Compose** 작업의 출력을 다른 작업에서 참조할 수 있습니다.

*예제*

이 작업 정의는 정수 배열에서 JSON 개체 배열을 만듭니다. 이 작업은 원본 배열을 반복하고, `@item()` 식을 사용하여 각 정수 값을 가져오고, 각 값을 각 JSON 개체의 "`number`" 속성에 할당합니다. 

```json
"Select": {
   "type": "Select",
   "inputs": {
      "from": [ 1, 2, 3 ],
      "select": { 
         "number": "@item()" 
      }
   },
   "runAfter": {}
},
```

이 작업이 만드는 배열은 다음과 같습니다.

`[ { "number": 1 }, { "number": 2 }, { "number": 3 } ]`

다른 작업에서 이 배열 출력을 사용하려면 이 출력을 **Compose** 작업으로 전달합니다.

```json
"Compose": {
   "type": "Compose",
   "inputs": "@body('Select')",
   "runAfter": {
      "Select": [ "Succeeded" ]
   }
},
```

그러면 **Compose** 작업의 출력을 다른 작업(예: **Office 365 Outlook - Send an email** 작업)에서 사용할 수 있습니다.

```json
"Send_an_email": {
   "type": "ApiConnection",
   "inputs": {
      "body": {
         "Body": "@{outputs('Compose')}",
         "Subject": "Output array from Select and Compose actions",
         "To": "<your-email@domain>"
      },
      "host": {
         "connection": {
            "name": "@parameters('$connections')['office365']['connectionId']"
         }
      },
      "method": "post",
      "path": "/Mail"
   },
   "runAfter": {
      "Compose": [ "Succeeded" ]
   }
},
```

<a name="table-action"></a>

### <a name="table-action"></a>테이블 작업

이 작업은 배열에서 CSV 또는 HTML 테이블을 만듭니다. JSON 개체가 있는 배열의 경우, 이 작업은 개체의 속성 이름에서 열 머리글을 자동으로 만듭니다. 기타 데이터 형식을 갖는 배열의 경우 열 머리글과 값을 지정해야 합니다. 예를 들어, 이 배열에는 이 작업이 열 머리글 이름에 사용할 수 있는 "ID" 및 "Product_Name" 속성이 포함되어 있습니다.

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]` 

```json
"Create_<CSV | HTML>_table": {
   "type": "Table",
   "inputs": {
      "format": "<CSV | HTML>",
      "from": <array>,
      "columns": [ 
         {
            "header": "<column-name>",
            "value": "<column-value>"
         },
         {
            "header": "<column-name>",
            "value": "<column-value>"
         } 
      ]
   },
   "runAfter": {}
}
```

*필수* 

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <CSV *또는* HTML>| String | 만들려는 테이블의 형식입니다. | 
| <*array*> | 배열 | 테이블의 원본 항목을 제공하는 배열 또는 식입니다. <p>**참고**: 원본 배열이 비어 있는 경우 작업은 빈 테이블을 만듭니다. | 
|||| 

*선택 사항*

열 머리글 및 값을 지정하거나 사용자 지정하려면 `columns` 배열을 사용합니다. `header-value` 쌍이 같은 머리글 이름을 갖는 경우 해당 값은 해당 머리글 이름 아래의 동일한 열에 표시됩니다. 그렇지 않은 경우, 각 고유 머리글은 고유한 열을 정의합니다.

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*column-name*> | String | 열의 머리글 이름입니다. | 
| <*column-value*> | 모두 | 해당 열의 값입니다. | 
|||| 

*예 1*

현재 이 배열이 포함된 이전에 만든 "myItemArray" 변수가 있다고 가정합니다. 

`[ {"ID": 0, "Product_Name": "Apples"}, {"ID": 1, "Product_Name": "Oranges"} ]`

이 작업 정의는 "myItemArray" 변수에서 CSV 테이블을 만듭니다. `from` 속성에 사용되는 식은 `variables()` 함수를 사용하여 "myItemArray"에서 배열을 가져옵니다. 

```json
"Create_CSV_table": {
   "type": "Table",
   "inputs": {
      "format": "CSV",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

이 작업이 만드는 CSV 테이블은 다음과 같습니다. 

```
ID,Product_Name 
0,Apples 
1,Oranges 
```

*예 2*

이 작업 정의는 "myItemArray" 변수에서 HTML 테이블을 만듭니다. `from` 속성에 사용되는 식은 `variables()` 함수를 사용하여 "myItemArray"에서 배열을 가져옵니다. 

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')"
   },
   "runAfter": {}
}
```

이 작업이 만드는 HTML 테이블은 다음과 같습니다. 

<table><thead><tr><th>ID</th><th>Product_Name</th></tr></thead><tbody><tr><td>0</td><td>Apples</td></tr><tr><td>1</td><td>Oranges</td></tr></tbody></table>

*예 3*

이 작업 정의는 "myItemArray" 변수에서 HTML 테이블을 만듭니다. 그러나 이 예제에서는 기본 열 머리글 이름을 "Stock_ID" 및 "Description"으로 재정의하고, "Description" 열의 값에 "Organic"이라는 단어를 추가합니다.

```json
"Create_HTML_table": {
   "type": "Table",
   "inputs": {
      "format": "HTML",
      "from": "@variables('myItemArray')",
      "columns": [ 
         {
            "header": "Stock_ID",
            "value": "@item().ID"
         },
         {
            "header": "Description",
            "value": "@concat('Organic ', item().Product_Name)"
         }
      ]
    },
   "runAfter": {}
},
```

이 작업이 만드는 HTML 테이블은 다음과 같습니다. 

<table><thead><tr><th>Stock_ID</th><th>설명</th></tr></thead><tbody><tr><td>0</td><td>Organic Apples</td></tr><tr><td>1</td><td>Organic Oranges</td></tr></tbody></table>

<a name="terminate-action"></a>

### <a name="terminate-action"></a>종료 작업

이 작업 워크플로 인스턴스 실행을 중지, 진행 중인 작업 취소, 나머지 작업을 건너뛰고 지정 된 상태를 반환 합니다. 예를 들어, 논리 앱이 오류 상태에서 완전히 종료되어야 할 경우 **Terminate** 작업을 사용할 수 있습니다. 이 작업이 이미 완료된 작업에 영향을 주지 않으며 순차 루프를 포함하여 **Foreach** 및 **Until** 루프 내에 포함될 수 없습니다. 

```json
"Terminate": {
   "type": "Terminate",
   "inputs": {
       "runStatus": "<status>",
       "runError": {
            "code": "<error-code-or-name>",
            "message": "<error-message>"
       }
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*status*> | String | 실행에 대해 반환하는 상태: "Failed", "Cancelled" 또는 "Succeeded" |
|||| 

*선택 사항*

"runStatus" 개체의 속성은 "runStatus" 속성이 "Failed" 상태로 설정될 때만 적용됩니다.

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*error-code-or-name*> | String | 오류에 대한 코드 또는 이름입니다. |
| <*error-message*> | String | 오류 및 앱 사용자가 수행할 수 있는 작업을 설명하는 메시지 또는 텍스트입니다. | 
|||| 

*예제*

이 작업 정의는 워크플로 실행을 중지하고, 실행 상태를 "Failed"로 설정하고, 상태, 오류 코드 및 오류 메시지를 반환합니다.

```json
"Terminate": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "Unexpected response",
            "message": "The service received an unexpected response. Please try again."
        }
   },
   "runAfter": {}
}
```

<a name="wait-action"></a>

### <a name="wait-action"></a>대기 작업  

이 작업은 지정된 간격 동안 또는 지정된 시간이 도달할 때까지 워크플로 실행을 일시 중지합니다. 

*지정된 간격*

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": <number-of-units>,
         "unit": "<interval>"
      }
   },
   "runAfter": {}
},
```

*지정된 시간*

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "<date-time-stamp>"
      }
   },
   "runAfter": {}
},
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*number-of-units*> | 정수  | **Delay** 작업에 대해 대기할 단위 수입니다. | 
| <*interval*> | String | **Delay** 작업의 경우 대기하는 간격: “초”, “분”, “시간”, “일”, “주” 또는 “월” | 
| <*date-time-stamp*> | String | **Delay Until** 작업에 대해 실행을 다시 시작할 날짜 및 시간입니다. 이 값은 [UTC 날짜/시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)을 사용해야 합니다. | 
|||| 

*예 1*

이 작업 정의는 15분 동안 워크플로를 일시 중지합니다.

```json
"Delay": {
   "type": "Wait",
   "inputs": {
      "interval": {
         "count": 15,
         "unit": "Minute"
      }
   },
   "runAfter": {}
},
```

*예 2*

이 작업 정의는 지정된 시간까지 워크플로를 일시 중지합니다.

```json
"Delay_until": {
   "type": "Wait",
   "inputs": {
      "until": {
         "timestamp": "2017-10-01T00:00:00Z"
      }
   },
   "runAfter": {}
},
```

<a name="workflow-action"></a>

### <a name="workflow-action"></a>워크플로 작업

이 작업은 이전에 만든 다른 논리 앱을 호출합니다. 즉, 다른 논리 앱 워크플로를 포함하고 다시 시작할 수 있습니다. 또한 자식 논리 앱이 응답을 반환할 경우, 중첩된 논리 앱을 따르는 작업에서도 자식 또는 *중첩된* 논리 앱의 출력을 사용할 수 있습니다.

Logic Apps 엔진은 호출하려는 트리거에 대한 액세스 권한을 확인하므로 해당 트리거에 액세스할 수 있어야 합니다. 또한 중첩된 논리 앱은 다음 조건을 충족해야 합니다.

* [Request](#request-trigger) 또는 [HTTP](#http-trigger) 트리거와 같은 트리거는 중첩된 논리 앱을 호출 가능하게 만듭니다.

* 부모 논리 앱과 동일한 Azure 구독

* 부모 논리 앱에서 중첩된 논리 앱의 출력을 사용하려면 중첩된 논리 앱에 [Response](#response-action) 작업이 있어야 합니다. 

```json
"<nested-logic-app-name>": {
   "type": "Workflow",
   "inputs": {
      "body": { "<body-content" },
      "headers": { "<header-content>" },
      "host": {
         "triggerName": "<trigger-name>",
         "workflow": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group>/providers/Microsoft.Logic/<nested-logic-app-name>"
         }
      }
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*nested-logic-app-name*> | String | 호출하려는 논리 앱의 이름입니다. | 
| <*trigger-name*> | String | 호출하려는 중첩된 논리 앱의 트리거 이름입니다. | 
| <*Azure-subscription-ID*> | String | 중첩된 논리 앱의 Azure 구독 ID입니다. |
| <*Azure-resource-group*> | String | 중첩된 논리 앱의 Azure 리소스 그룹 이름입니다. |
| <*nested-logic-app-name*> | String | 호출하려는 논리 앱의 이름입니다. |
||||

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------|  
| <*header-content*> | JSON 개체 | 호출과 함께 보낼 헤더입니다. | 
| <*body-content*> | JSON 개체 | 호출과 함께 전송할 메시지 콘텐츠입니다. | 
||||

*Outputs*

이 작업의 출력은 중첩된 논리 앱의 Response 작업에 따라 다릅니다. 중첩된 논리 앱에 Response 작업이 없으면 출력은 비어 있습니다.

*예제*

"Start_search" 작업이 성공적으로 완료되면 이 워크플로 작업 정의는 지정된 입력을 전달하는 "Get_product_information"이라는 다른 논리 앱을 호출합니다. 

```json
"actions": {
   "Start_search": { <action-definition> },
   "Get_product_information": {
      "type": "Workflow",
      "inputs": {
         "body": {
            "ProductID": "24601",
         },
         "host": {
            "id": "/subscriptions/XXXXXXXXXXXXXXXXXXXXXXXXXX/resourceGroups/InventoryManager-RG/providers/Microsoft.Logic/Get_product_information",
            "triggerName": "Find_product"
         },
         "headers": {
            "content-type": "application/json"
         }
      },
      "runAfter": { 
         "Start_search": [ "Succeeded" ]
      }
   }
},
```

## <a name="control-workflow-action-details"></a>워크플로 제어 작업 세부 정보

<a name="foreach-action"></a>

### <a name="foreach-action"></a>Foreach 작업

이 루프 작업은 배열을 반복하고 각 배열 항목에 대해 작업을 수행합니다. 기본적으로 "for each" 루프는 최대 수의 루프까지 병렬로 실행됩니다. 이 최대 수에 대해서는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)을 참조하세요. ["for each" 루프를 만드는 방법](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop)을 알아보세요.

```json
"For_each": {
   "type": "Foreach",
   "actions": { 
      "<action-1>": { "<action-definition-1>" },
      "<action-2>": { "<action-definition-2>" }
   },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": <count>
      }
    },
    "operationOptions": "<operation-option>"
}
```

*필수* 

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*action-1...n*> | String | 각 배열 항목에 대해 실행되는 작업의 이름입니다. | 
| <*action-definition-1...n*> | JSON 개체 | 실행되는 작업에 대한 정의입니다. | 
| <*for-each-expression*> | String | 지정된 배열의 각 항목을 참조하는 식입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*count*> | 정수  | 기본적으로 "for each" 루프 반복은 동시에 또는 병렬로 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)까지 실행됩니다. 새로운 <*count*> 값을 설정하여 이 제한을 변경하려면 ["for each" 동시성 변경](#change-for-each-concurrency)을 참조하세요. | 
| <*operation-option*> | String | "for each" 루프를 병렬 방식이 아닌 순차적으로 실행하려면 <*operation-option*>을 `Sequential`로 설정하거나 <*count*>를 `1`로 설정합니다. 둘 중 하나만 설정해야 합니다. 자세한 내용은 ["for each" 루프를 순차적으로 실행](#sequential-for-each)을 참조하세요. | 
|||| 

*예제*

이 "for each" 루프는 들어오는 이메일의 첨부 파일을 포함하여 배열의 각 항목에 대해 이메일을 전송합니다. 루프는 첨부 파일을 포함하는 이메일을 첨부 파일을 검토하는 사람에게 전송합니다.

```json
"For_each": {
   "type": "Foreach",
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "body": {
               "Body": "@base64ToString(items('For_each')?['Content'])",
               "Subject": "Review attachment",
               "To": "Sophie.Owen@contoso.com"
                },
            "host": {
               "connection": {
                  "id": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {}
      }
   },
   "foreach": "@triggerBody()?['Attachments']",
   "runAfter": {}
}
```

트리거에서 출력으로 전달되는 배열만 지정하려면 이 식은 트리거 본문에서 <*array-name*> 배열을 가져옵니다. 배열이 없는 경우 오류를 방지하기 위해 이 식은 `?` 연산자를 사용합니다.

`@triggerBody()?['<array-name>']` 

<a name="if-action"></a>

### <a name="if-action"></a>If 작업

*조건문*에 해당하는 이 작업은 조건을 나타내는 식을 평가하고, 조건이 true인지 또는 false인지에 따라 다른 분기를 실행합니다. 조건이 true이면 해당 조건은 “Succeeded” 상태로 표시됩니다. [조건문을 만드는 방법](../logic-apps/logic-apps-control-flow-conditional-statement.md)을 알아보세요.

``` json
"Condition": {
   "type": "If",
   "expression": { "<condition>" },
   "actions": {
      "<action-1>": { "<action-definition>" }
   },
   "else": {
      "actions": {
        "<action-2>": { "<action-definition" }
      }
   },
   "runAfter": {}
}
```

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*condition*> | JSON 개체 | 평가할 조건으로, 식이 될 수 있습니다. | 
| <*action-1*> | JSON 개체 | <*condition*>이 true로 평가될 때 실행할 작업입니다. | 
| <*action-definition*> | JSON 개체 | 작업에 대한 정의입니다. | 
| <*action-2*> | JSON 개체 | <*condition*>이 false로 평가될 때 실행할 작업입니다. | 
|||| 

`actions` 또는 `else` 개체의 작업은 다음 상태를 가져옵니다.

* "성공" - 성공적으로 실행되는 경우
* "실패" - 실행되었지만 실패하는 경우
* "건너뜀" - 해당 분기가 실행되지 않는 경우

*예제*

이 조건은 정수 변수가 0보다 큰 값을 가질 경우 워크플로가 웹 사이트를 확인하도록 지정합니다. 변수가 0 이하인 경우 워크플로는 다른 웹 사이트를 확인합니다.

```json
"Condition": {
   "type": "If",
   "expression": {
      "and": [ {
         "greater": [ "@variables('myIntegerVariable')", 0 ] 
      } ]
   },
   "actions": { 
      "HTTP - Check this website": {
         "type": "Http",
         "inputs": {
         "method": "GET",
            "uri": "http://this-url"
         },
         "runAfter": {}
      }
   },
   "else": {
      "actions": {
         "HTTP - Check this other website": {
            "type": "Http",
            "inputs": {
               "method": "GET",
               "uri": "http://this-other-url"
            },
            "runAfter": {}
         }
      }
   },
   "runAfter": {}
}
```  

#### <a name="how-conditions-use-expressions"></a>조건이 식을 사용하는 방법

조건에서 식을 사용하는 방법을 보여 주는 몇 가지 예제는 다음과 같습니다.
  
| JSON | 결과 | 
|------|--------| 
| "expression": "@parameters('<*hasSpecialAction*>')" | 부울 식의 경우에만, 이 조건은 true로 평가되는 모든 값을 통과합니다. <p>다른 형식을 부울로 변환하려면 `empty()` 또는 `equals()` 함수를 사용합니다. | 
| "expression": "@greater(actions('<*action*>').output.value, parameters('<*threshold*>'))" | 비교 함수의 경우, 이 작업은 <*action*>의 출력이 <*threshold*> 값보다 클 경우에만 실행됩니다. | 
| "expression": "@or(greater(actions('<*action*>').output.value, parameters('<*threshold*>')), less(actions('<*same-action*>').output.value, 100))" | 논리 함수 및 중첩된 부울 식을 만드는 경우, 이 작업은 <*action*>의 출력이 <*threshold*> 값보다 크거나 100보다 작을 때 실행됩니다. | 
| "expression": "@equals(length(actions('<*action*>').outputs.errors), 0))" | 배열에 항목이 있는지 여부를 확인하기 위해 배열 함수를 사용할 수 있습니다. 이 작업은 `errors` 배열이 비어 있을 때 실행됩니다. | 
||| 

<a name="scope-action"></a>

### <a name="scope-action"></a>범위 작업

이 작업은 범위에 속한 작업의 실행이 완료되면 해당 상태를 가져오는 *범위*로 작업을 논리적으로 그룹화합니다. 그런 후에는 범위의 상태를 사용하여 다른 작업의 실행 여부를 확인할 수 있습니다. [범위 만드는 방법](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)을 알아보세요.

```json
"Scope": {
   "type": "Scope",
   "actions": {
      "<inner-action-1>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<inner-action-2>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   }
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------|  
| <*inner-action-1...n*> | JSON 개체 | 범위 내에서 실행되는 하나 이상의 작업입니다. |
| <*action-inputs*> | JSON 개체 | 각 작업에 대한 입력입니다. |
|||| 

<a name="switch-action"></a>

### <a name="switch-action"></a>Switch 작업

*switch 문*이라고도 하는 이 작업은 다른 작업을 *사례*로 구성하고 각 사례에 값을 할당합니다. 단, 기본 사례(있는 경우)는 예외입니다. 워크플로가 실행될 경우 **Switch** 작업은 식, 개체 또는 토큰의 값을 각 사례에 대해 지정된 값과 비교합니다. **Switch** 작업이 일치하는 사례를 찾으면 워크플로는 해당 사례에 대해서만 작업을 실행합니다. **Switch** 작업이 실행될 때마다 일치하는 사례만 존재하거나 일치하는 사례가 존재하지 않는 것입니다. 일치하는 사례가 존재하는 경우 **Switch** 작업은 기본 작업을 실행합니다. [switch 문을 만드는 방법](../logic-apps/logic-apps-control-flow-switch-statement.md)을 알아보세요.

``` json
"Switch": {
   "type": "Switch",
   "expression": "<expression-object-or-token>",
   "cases": {
      "Case": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      },
      "Case_2": {
         "actions": {
           "<action-name>": { "<action-definition>" }
         },
         "case": "<matching-value>"
      }
   },
   "default": {
      "actions": {
         "<default-action-name>": { "<default-action-definition>" }
      }
   },
   "runAfter": {}
}
```

*필수*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*expression-object-or-token*> | 다름 | 평가할 식, JSON 개체 또는 토큰입니다. | 
| <*action-name*> | String | 일치하는 사례에 대해 실행할 작업의 이름입니다. | 
| <*action-definition*> | JSON 개체 | 일치하는 사례에 대해 실행할 작업에 대한 정의입니다. | 
| <*matching-value*> | 다름 | 평가된 결과와 비교할 값입니다. | 
|||| 

*선택 사항*

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*default-action-name*> | String | 일치하는 사례가 없는 경우에 실행할 기본 작업의 이름입니다. | 
| <*default-action-definition*> | JSON 개체 | 일치하는 사례가 없는 경우에 실행할 작업에 대한 정의입니다. | 
|||| 

*예제*

이 작업 정의는 승인 요청 이메일에 응답하는 사용자가 "승인" 옵션을 선택했는지 또는 "거부" 옵션을 선택했는지를 평가합니다. 이 선택에 따라, **Switch** 작업은 일치하는 사례에 대해 작업을 실행합니다. 즉, 각 사례에 다른 내용을 사용하여 응답자에게 다른 이메일을 전송합니다. 

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
            "Send_an_email": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your approval.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
            "Send_an_email_2": { 
               "type": "ApiConnection",
               "inputs": {
                  "Body": "Thank you for your response.",
                  "Subject": "Response received", 
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}     
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": { 
         "Send_an_email_3": { 
            "type": "ApiConnection",
            "inputs": {
               "Body": "Please respond with either 'Approve' or 'Reject'.",
               "Subject": "Please respond", 
               "To": "Sophie.Owen@contoso.com"
            },
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "post",
            "path": "/Mail"
         },
         "runAfter": {} 
      }
   },
   "runAfter": {
      "Send_approval_email": [ 
         "Succeeded"
      ]
   }
}
```

<a name="until-action"></a>

### <a name="until-action"></a>Until 작업

이 루프 작업은 지정된 조건이 충족될 때까지 실행되는 작업을 포함합니다. 이 루프는 다른 모든 작업이 실행된 후에 마지막 단계로서 해당 조건을 확인합니다. `"actions"` 개체에 둘 이상의 작업을 포함할 수 있으며 작업은 하나 이상의 제한을 정의해야 합니다. ["until" 루프를 만드는 방법](../logic-apps/logic-apps-control-flow-loops.md#until-loop)을 알아보세요. 

```json
 "Until": {
   "type": "Until",
   "actions": {
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      },
      "<action-name>": {
         "type": "<action-type>",
         "inputs": { "<action-inputs>" },
         "runAfter": {}
      }
   },
   "expression": "<condition>",
   "limit": {
      "count": <loop-count>,
      "timeout": "<loop-timeout>"
   },
   "runAfter": {}
}
```

| 값 | Type | 설명 | 
|-------|------|-------------| 
| <*action-name*> | String | 루프 내에서 실행하려는 작업의 이름입니다. | 
| <*action-type*> | String | 실행하려는 작업 유형입니다. | 
| <*action-inputs*> | 다양 | 실행할 작업에 대한 입력입니다. | 
| <*condition*> | String | 루프의 모든 작업이 실행 완료된 후에 평가할 조건 또는 식입니다. | 
| <*loop-count*> | 정수  | 작업이 실행할 수 있는 가장 많은 루프 수에 대한 제한입니다. 기본 `count` 값은 60입니다. | 
| <*loop-timeout*> | String | 루프가 실행될 수 있는 가장 긴 시간의 제한입니다. 기본 `timeout` 값은 필요한 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601)에 해당하는 `PT1H`입니다. |
|||| 

*예제*

이 루프 작업 정의는 다음 조건 중 하나가 충족될 때까지 지정된 URL에 HTTP 요청을 보냅니다. 

* 요청은 "200 정상" 상태 코드의 응답을 받습니다.
* 루프는 60번 실행됩니다.
* 루프는 1시간 동안 실행됩니다.

```json
 "Run_until_loop_succeeds_or_expires": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200])",
    "limit": {
        "count": 60,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

<a name="subscribe-unsubscribe"></a>

## <a name="webhooks-and-subscriptions"></a>웹후크 및 구독

웹후크 기반 트리거 및 작업은 끝점을 주기적으로 검사하지 않지만, 대신 해당 끝점의 특정 이벤트 또는 데이터를 기다립니다. 이러한 트리거 및 작업은 끝점이 응답을 보낼 수 있는 *콜백 URL*을 제공하여 끝점을 *구독*합니다.

`subscribe` 호출은 예를 들어 자격 증명이 갱신되거나 트리거 또는 작업의 입력 매개 변수가 변경될 때와 같이 워크플로가 어떤 식으로든 변경될 때 발생합니다. 이 호출은 표준 HTTP 작업과 동일한 매개 변수를 사용합니다. 

다음 예제와 같이 작업으로 인해 트리거 또는 작업이 유효해지지 않을 때 `unsubscribe` 호출이 자동으로 발생합니다.

* 트리거 삭제 또는 비활성화 
* 워크플로 삭제 또는 비활성화 
* 구독 삭제 또는 비활성화 

이러한 호출을 지원하려면 `@listCallbackUrl()` 식은 트리거 또는 작업에 대한 고유한 “콜백 URL”을 반환합니다. 이 URL은 서비스의 REST API를 사용하는 엔드포인트에 대한 고유 식별자를 나타냅니다. 이 함수에 대한 매개 변수는 웹후크 트리거 또는 작업과 같습니다.

<a name="asynchronous-limits"></a>

## <a name="change-asynchronous-duration"></a>비동기 지속 시간 변경

트리거 및 작업에 대해 `limit.timeout` 속성을 추가하여 비동기 패턴의 지속 시간을 특정 시간 간격으로 제한할 수 있습니다. 이러한 방식으로, 간격이 경과할 때 작업이 완료되지 않은 경우 상태는 `ActionTimedOut` 코드와 함께 `Cancelled`로 표시됩니다. `timeout` 속성은 [ISO 8601 형식](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 사용합니다. 

``` json
"<trigger-or-action-name>": {
   "type": "Workflow | Webhook | Http | ApiConnectionWebhook | ApiConnection",
   "inputs": {},
   "limit": {
      "timeout": "PT10S"
   },
   "runAfter": {}
}
```

<a name="runtime-config-options"></a>

## <a name="runtime-configuration-settings"></a>런타임 구성 설정

트리거 및 작업에 대한 기본 런타임 동작을 트리거 또는 작업 정의의 `runtimeConfiguration` 속성을 사용하여 변경할 수 있습니다.

| 자산 | Type | 설명 | 트리거 또는 작업 | 
|----------|------|-------------|-------------------| 
| `runtimeConfiguration.concurrency.runs` | 정수  | 변경 된 [ *기본 제한* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) 동시에, 또는 병렬로 실행할 수 있는 워크플로 인스턴스의 수에 합니다. 이 값은 백 엔드 시스템에서 수신하는 요청 수를 제한하는 데 도움이 됩니다. <p>`runs` 속성을 `1`로 설정하면 `operationOptions` 속성을 `SingleInstance`로 설정할 때와 같은 결과가 나타납니다. 두 속성 중 하나만 설정할 수 있습니다. <p>기본 제한을 변경하려면 [트리거 동시성 변경](#change-trigger-concurrency) 또는 [순차적으로 인스턴스 트리거](#sequential-trigger)를 참조하세요. | 모든 트리거 | 
| `runtimeConfiguration.concurrency.maximumWaitingRuns` | 정수  | 변경 된 [ *기본 제한* ](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) 워크플로 최대 동시 인스턴스를 이미 실행 중인 경우 실행 하기까지 대기할 수 있는 워크플로 인스턴스의 수에 합니다. `concurrency.runs` 속성에서 동시성 제한을 변경할 수 있습니다. <p>기본 제한을 변경하려면 [대기 실행 제한 변경](#change-waiting-runs)을 참조하세요. | 모든 트리거 | 
| `runtimeConfiguration.concurrency.repetitions` | 정수  | 동시에 또는 병렬로 실행할 수 있는 "for each" 루프 반복 수에 대한 [*기본 제한*](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)을 변경합니다. <p>`repetitions` 속성을 `1`로 설정하면 `operationOptions` 속성을 `SingleInstance`로 설정할 때와 같은 결과가 나타납니다. 두 속성 중 하나만 설정할 수 있습니다. <p>기본 제한을 변경하려면 ["for each" 동시성 변경](#change-for-each-concurrency) 또는 [순차적으로 "for each" 루프 실행](#sequential-for-each)을 참조하세요. | 작업: <p>[Foreach](#foreach-action) | 
||||| 

<a name="operation-options"></a>

## <a name="operation-options"></a>작업 옵션

트리거 및 작업에 대한 기본 동작을 트리거 또는 작업 정의의 `operationOptions` 속성을 사용하여 변경할 수 있습니다.

| 작업 옵션 | Type | 설명 | 트리거 또는 작업 | 
|------------------|------|-------------|-------------------| 
| `DisableAsyncPattern` | String | 비동기가 아닌 동기적으로 HTTP 기반 작업을 실행합니다. <p><p>이 옵션을 설정하려면 [동기적으로 작업 실행](#asynchronous-patterns)을 참조하세요. | 작업: <p>[ApiConnection](#apiconnection-action), <br>[HTTP](#http-action), <br>[응답](#response-action) | 
| `OptimizedForHighThroughput` | String | 5분당 작업 실행 수에 대한 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)을 [최대 제한](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)으로 변경합니다. <p><p>이 옵션을 설정하려면 [높은 처리량 모드에서 실행](#run-high-throughput-mode)을 참조하세요. | 모든 작업 | 
| `Sequential` | String | "for each" 루프 반복을 모두 동시에 병렬로 실행하지 않고 한 번에 하나씩 실행합니다. <p>이 옵션은 `runtimeConfiguration.concurrency.repetitions` 속성을 `1`로 설정할 때와 동일하게 작동합니다. 두 속성 중 하나만 설정할 수 있습니다. <p><p>이 옵션을 설정하려면 ["for each" 루프를 순차적으로 실행](#sequential-for-each)을 참조하세요.| 작업: <p>[Foreach](#foreach-action) | 
| `SingleInstance` | String | 각 논리 앱 인스턴스에 대한 트리거를 순차적으로 실행하고, 다음 논리 앱 인스턴스를 트리거하기 전에 이전에 활성 상태이던 실행이 완료될 때까지 기다립니다. <p><p>이 옵션은 `runtimeConfiguration.concurrency.runs` 속성을 `1`로 설정할 때와 동일하게 작동합니다. 두 속성 중 하나만 설정할 수 있습니다. <p>이 옵션을 설정하려면 [인스턴스를 순차적으로 트리거](#sequential-trigger)를 참조하세요. | 모든 트리거 | 
||||

<a name="change-trigger-concurrency"></a>

### <a name="change-trigger-concurrency"></a>트리거 동시성 변경경

기본적으로 논리 앱 인스턴스는 동시에 또는 병렬로 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)까지 실행됩니다. 따라서 각 트리거 인스턴스가 이전 워크플로 인스턴스 실행이 완료 되기 전에 발생 합니다. 이 제한은 백 엔드 시스템에서 수신하는 요청 수를 제한하는 데 도움이 됩니다. 

디자이너를 통해 동시성 설정을 변경하면 기본 트리거 정의에서 `runtimeConfiguration.concurrency.runs` 속성이 추가 또는 업데이트되고, 그 반대의 경우도 마찬가지이므로 기본 제한을 변경하려면 코드 보기 편집기 또는 Logic Apps 디자이너를 사용할 수 있습니다. 이 속성은 병렬로 실행할 수 있는 워크플로 인스턴스의 최대 수를 제어 합니다. 

> [!NOTE] 
> 디자이너 또는 코드 보기 편집기를 사용하여 순차적으로 실행되도록 트리거를 설정할 경우 코드 보기 편집기에서 트리거의 `operationOptions` 속성을 `SingleInstance`로 설정하지 마세요. 그렇지 않으면 유효성 검사 오류가 표시됩니다. 자세한 내용은 [인스턴스를 순차적으로 트리거](#sequential-trigger)를 참조하세요.

#### <a name="edit-in-code-view"></a>코드 보기에서 편집 

기본 트리거 정의에서 `1` 및 `50` 사이의 값을 갖는 `runtimeConfiguration.concurrency.runs` 속성을 추가하거나 속성을 이 값으로 업데이트합니다.

동시 실행 수를 10 인스턴스로 제한하는 예제는 다음과 같습니다.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps 디자이너에서 편집

1. 트리거의 오른쪽 위 모서리에서 줄임표 단추 (...)를 선택한 다음, **설정**을 선택합니다..

2. **동시성 제어** 아래에서 **제한**을 **켜기**로 설정합니다. 

3. **병렬도** 슬라이더를 원하는 값으로 끕니다. 논리 앱을 순차적으로 실행하려면 슬라이더 값을 **1**로 끕니다.

<a name="change-for-each-concurrency"></a>

### <a name="change-for-each-concurrency"></a>"for each" 동시성 변경

기본적으로 "for each" 루프 반복은 동시에 또는 병렬로 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)까지 실행됩니다. 디자이너를 통해 동시성 설정을 변경하면 기본 "for each" 작업 정의에서 `runtimeConfiguration.concurrency.repetitions` 속성이 추가 또는 업데이트되고, 그 반대의 경우도 마찬가지이므로 기본 제한을 변경하려면 코드 보기 편집기 또는 Logic Apps 디자이너를 사용할 수 있습니다. 이 속성은 병렬로 실행할 수 있는 최대 반복 수를 제어합니다.

> [!NOTE] 
> 디자이너 또는 코드 보기 편집기를 사용하여 "for each" 작업이 순차적으로 실행되도록 설정할 경우 코드 보기 편집기에서 작업의 `operationOptions` 속성을 `Sequential`로 설정하지 마세요. 그렇지 않으면 유효성 검사 오류가 표시됩니다. 자세한 내용은 ["for each" 루프를 순차적으로 실행](#sequential-for-each)을 참조하세요.

#### <a name="edit-in-code-view"></a>코드 보기에서 편집 

기본 "for each" 정의에서 `1` 및 `50` 사이의 값을 갖는 `runtimeConfiguration.concurrency.repetitions` 속성을 추가하거나 속성을 이 값으로 업데이트합니다. 

동시 실행 수를 10번 반복으로 제한하는 예제는 다음과 같습니다.

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 10
      }
   }
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps 디자이너에서 편집

1. **For each** 작업의 오른쪽 위 모서리에서 줄임표(...) 단추를 선택하고 **설정**을 선택합니다.

2. **동시성 제어** 아래에서 **동시성 제어**를 **켜기**로 설정합니다. 

3. **병렬도** 슬라이더를 원하는 값으로 끕니다. 논리 앱을 순차적으로 실행하려면 슬라이더 값을 **1**로 끕니다.

<a name="change-waiting-runs"></a>

### <a name="change-waiting-runs-limit"></a>실행 대기 제한 변경

기본적으로 모든 논리 앱 워크플로 인스턴스 실행을 동시에 동시에 또는 병렬로 최대 합니다 [기본 제한은](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits)합니다. 각 트리거 인스턴스에 이전에 활성 워크플로 인스턴스 실행이 완료 되기 전에 발생 합니다. 수 있지만 [이 기본 제한을 변경](#change-trigger-concurrency)워크플로 인스턴스의 수에는 새 동시성 제한에 도달 하는 경우 다른 새 인스턴스를 실행 하기 위해 대기 해야 합니다. 

대기할 수 있는 실행 수에도 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits) 있습니다. 이 제한은 변경 가능합니다. 그러나 논리 앱이 대기 중인 실행 한도에 도달하면 Logic Apps 엔진은 새 실행을 더 이상 허용하지 않습니다. 요청 및 웹후크 트리거는 429 오류를 반환하며, 되풀이 트리거는 폴링 시도를 건너뛰기 시작합니다.

대기 중인 실행에 대한 기본 제한을 변경하려면 기본 트리거 정의에서 값이 `0`~`100` 사이인 `runtimeConfiguration.concurency.maximumWaitingRuns` 속성을 추가합니다. 

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "maximumWaitingRuns": 50
      }
   }
}
```

<a name="sequential-trigger"></a>

### <a name="trigger-instances-sequentially"></a>인스턴스를 순차적으로 트리거

각 논리를 실행 하려면 앱 워크플로 인스턴스가 이전 인스턴스가 완료 된 후에 실행 순차적 실행 되도록 트리거를 설정 합니다. 또한 디자이너를 통해 동시성 설정을 변경하면 기본 트리거 정의에서 `runtimeConfiguration.concurrency.runs` 속성이 추가 또는 업데이트되고, 그 반대의 경우도 마찬가지이므로 코드 보기 편집기 또는 Logic Apps 디자이너를 사용할 수 있습니다. 

> [!NOTE] 
> 디자이너 또는 코드 보기 편집기를 사용하여 순차적으로 실행되도록 트리거를 설정할 경우 코드 보기 편집기에서 트리거의 `operationOptions` 속성을 `Sequential`로 설정하지 마세요. 그렇지 않으면 유효성 검사 오류가 표시됩니다. 

#### <a name="edit-in-code-view"></a>코드 보기에서 편집

트리거 정의에서 다음 속성 중 하나만 설정합니다. 

`runtimeConfiguration.concurrency.runs` 속성을 `1`로 설정합니다.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": 1
      }
   }
}
```

*-또는-*

`operationOptions` 속성을 `SingleInstance`로 설정합니다.

```json
"<trigger-name>": {
   "type": "<trigger-name>",
   "recurrence": {
      "frequency": "<time-unit>",
      "interval": <number-of-time-units>,
   },
   "operationOptions": "SingleInstance"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps 디자이너에서 편집

1. 트리거의 오른쪽 위 모서리에서 줄임표 단추 (...)를 선택한 다음, **설정**을 선택합니다..

2. **동시성 제어** 아래에서 **제한**을 **켜기**로 설정합니다. 

3. **병렬도** 슬라이더를 숫자 `1`로 끕니다. 

<a name="sequential-for-each"></a>

### <a name="run-for-each-loops-sequentially"></a>"for each" 루프를 순차적으로 실행

"for each" 루프 반복을 이전 반복 실행이 끝난 후에만 실행하려면 "for each" 작업이 순차적으로 실행되도록 설정합니다. 또한 디자이너를 통해 작업의 동시성을 변경하면 기본 작업 정의에서 `runtimeConfiguration.concurrency.repetitions` 속성이 추가 또는 업데이트되고, 그 반대의 경우도 마찬가지이므로 코드 보기 편집기 또는 Logic Apps 디자이너를 사용할 수 있습니다. 

> [!NOTE] 
> 디자이너 또는 코드 보기 편집기를 사용하여 "for each" 작업이 순차적으로 실행되도록 설정할 경우 코드 보기 편집기에서 작업의 `operationOptions` 속성을 `Sequential`로 설정하지 마세요. 그렇지 않으면 유효성 검사 오류가 표시됩니다. 

#### <a name="edit-in-code-view"></a>코드 보기에서 편집

작업 정의에서 다음 속성 중 하나만 설정합니다. 

`runtimeConfiguration.concurrency.repetitions` 속성을 `1`로 설정합니다.

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "runtimeConfiguration": {
      "concurrency": {
         "repetitions": 1
      }
   }
}
```

*-또는-*

`operationOptions` 속성을 `Sequential`로 설정합니다.

```json
"For_each" {
   "type": "Foreach",
   "actions": { "<actions-to-run>" },
   "foreach": "<for-each-expression>",
   "runAfter": {},
   "operationOptions": "Sequential"
}
```

#### <a name="edit-in-logic-apps-designer"></a>Logic Apps 디자이너에서 편집

1. **For each** 작업의 오른쪽 위 모서리에서 줄임표 단추 (...)를 선택한 다음, **설정**을 선택합니다.

2. **동시성 제어** 아래에서 **동시성 제어**를 **켜기**로 설정합니다. 

3. **병렬도** 슬라이더를 숫자 `1`로 끕니다. 

<a name="asynchronous-patterns"></a>

### <a name="run-actions-synchronously"></a>작업을 동기적으로 실행

기본적으로 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 따릅니다. 이 패턴은 HTTP 기반 작업이 지정된 끝점으로 요청을 보낼 때 원격 서버가 "202 수락됨" 응답을 다시 보내도록 지정합니다. 이 응답은 서버가 처리 요청을 수락했음을 의미합니다. Logic Apps 엔진은 처리가 중지될 때까지 응답(202 이외의 응답)의 위치 헤더가 지정하는 URL을 계속 확인합니다.

그러나 요청에는 제한 시간이 있으므로 장기 실행 작업의 경우 작업의 입력 아래에서 `operationOptions` 속성을 추가하고 `DisableAsyncPattern`으로 설정하여 비동기 동작을 사용하지 않도록 설정할 수 있습니다.
  
```json
"<some-long-running-action>": {
   "type": "Http",
   "inputs": { "<action-inputs>" },
   "operationOptions": "DisableAsyncPattern",
   "runAfter": {}
}
```

<a name="run-high-throughput-mode"></a>

### <a name="run-in-high-throughput-mode"></a>높은 처리량 모드에서 실행

단일 논리 앱 실행의 경우 5분마다 실행되는 작업 수에는 [기본 제한](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)이 적용됩니다. 이 제한을 가능한 [최대](../logic-apps/logic-apps-limits-and-config.md#throughput-limits)로 높이려면 `operationOptions` 속성을 `OptimizedForHighThroughput`으로 설정합니다. 이렇게 설정하면 논리 앱이 "높은 처리량" 모드가 됩니다. 

> [!NOTE]
> 높은 처리량 모드는 미리 보기로 제공됩니다. 또는 필요에 따라 둘 이상의 논리 앱에 워크로드를 분산할 수도 있습니다.

```json
"<action-name>": {
   "type": "<action-type>",
   "inputs": { "<action-inputs>" },
   "operationOptions": "OptimizedForHighThroughput",
   "runAfter": {}
}
```

<a name="connector-authentication"></a>

## <a name="authenticate-http-triggers-and-actions"></a>HTTP 트리거 및 작업 인증

HTTP 엔드포인트는 다양한 종류의 인증을 지원합니다. 이러한 HTTP 트리거 및 작업에 대한 인증을 설정할 수 있습니다.

* [HTTP](../connectors/connectors-native-http.md)
* [HTTP + Swagger](../connectors/connectors-native-http-swagger.md)
* [HTTP 웹후크](../connectors/connectors-native-webhook.md)

설정할 수 있는 인증의 종류는 다음과 같습니다.

* [기본 인증](#basic-authentication)
* [클라이언트 인증서 인증](#client-certificate-authentication)
* [Azure AD(Azure Active Directory) OAuth 인증](#azure-active-directory-oauth-authentication)

> [!IMPORTANT]
> 논리 앱 워크플로 정의가 처리하는 중요한 정보를 보호해야 합니다. 보안 매개 변수를 사용하고 필요에 따라 데이터를 인코딩하십시오. 매개 변수 사용 및 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>기본 인증

Azure Active Directory를 사용한 [기본적인 인증](../active-directory-b2c/active-directory-b2c-custom-rest-api-netfw-secure-basic.md)의 경우 트리거나 작업 정의에 `authentication` JSON 개체를 포함할 수 있습니다. 여기에는 다음 표에 지정된 속성을 포함합니다. 런타임 시 매개 변수 값에 액세스하려면 [워크플로 정의 언어](https://aka.ms/logicappsdocs)에 제공되는 `@parameters('parameterName')` 식을 사용하면 됩니다. 

| 자산 | 필수 | Value | 설명 | 
|----------|----------|-------|-------------| 
| **type** | 예 | "Basic" | 사용할 인증 유형입니다. 여기에서는 "Basic"입니다. | 
| **사용자 이름** | 예 | "@parameters('userNameParam')" | 대상 서비스 엔드포인트에 대한 액세스를 인증하는 사용자 이름입니다. |
| **암호** | 예 | "@parameters('passwordParam')" | 대상 서비스 엔드포인트에 대한 액세스를 인증하는 암호입니다. |
||||| 

이 예제 HTTP 작업 정의에서는 `authentication` 섹션이 `Basic` 인증을 지정합니다. 매개 변수 사용 및 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

> [!IMPORTANT]
> 논리 앱 워크플로 정의가 처리하는 중요한 정보를 보호해야 합니다. 보안 매개 변수를 사용하고 필요에 따라 데이터를 인코딩하십시오. 매개 변수 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

Azure Active Directory를 사용한 [인정서 기반 인증](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)의 경우 트리거나 작업 정의에 `authentication` JSON 개체를 포함할 수 있습니다. 여기에는 다음 표에 지정된 속성을 포함합니다. 런타임 시 매개 변수 값에 액세스하려면 [워크플로 정의 언어](https://aka.ms/logicappsdocs)에 제공되는 `@parameters('parameterName')` 식을 사용하면 됩니다. 사용할 수 있는 클라이언트 인증서 수에 대한 제한은 [Azure Logic Apps의 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요.

| 자산 | 필수 | Value | 설명 |
|----------|----------|-------|-------------|
| **type** | 예 | "ClientCertificate" | SSL(Secure Sockets Layer) 클라이언트 인증서에 사용할 인증 유형입니다. 자체 서명된 인증서가 지원되지만 SSL에 대한 자체 서명된 인증서는 지원되지 않습니다. |
| **pfx** | 예 | "@parameters('pfxParam') | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 |
| **암호** | 예 | "@parameters('passwordParam')" | PFX 파일에 액세스하기 위한 암호 |
||||| 

이 예제 HTTP 작업 정의에서는 `authentication` 섹션이 `ClientCertificate` 인증을 지정합니다. 매개 변수 사용 및 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> 논리 앱 워크플로 정의가 처리하는 중요한 정보를 보호해야 합니다. 보안 매개 변수를 사용하고 필요에 따라 데이터를 인코딩하십시오. 매개 변수 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-ad-oauth-authentication"></a>Azure AD(Active Directory) OAuth 인증

[Azure AD OAuth 인증](../active-directory/develop/authentication-scenarios.md)의 경우 트리거나 작업 정의에 `authentication` JSON 개체를 포함할 수 있습니다. 여기에는 다음 표에 지정된 속성을 포함합니다. 런타임 시 매개 변수 값에 액세스하려면 [워크플로 정의 언어](https://aka.ms/logicappsdocs)에 제공되는 `@parameters('parameterName')` 식을 사용하면 됩니다.

| 자산 | 필수 | Value | 설명 |
|----------|----------|-------|-------------|
| **type** | 예 | `ActiveDirectoryOAuth` | 사용할 인증 유형, Azure AD OAuth의 경우 "ActiveDirectoryOAuth" |
| **authority** | 아닙니다. | <*URL-for-authority-token-issuer*> | 인증 토큰을 제공하는 기관의 URL |
| **테넌트** | 예 | <*tenant-ID*> | Azure AD 테넌트의 테넌트 ID |
| **대상** | 예 | <*resource-to-authorize*> | 권한 부여에 사용할 리소스(예: `https://management.core.windows.net/`) |
| **clientId** | 예 | <*client-ID*> | 권한 부여를 요청하는 앱에 대한 클라이언트 ID |
| **credentialType** | 예 | "인증서" 또는 "비밀" | 클라이언트에서 권한 부여 요청에 대해 사용하는 자격 증명 유형 이 속성 및 값은 기본 정의에 표시되지 않지만 자격 증명 형식에 대한 필수 매개 변수를 결정합니다. |
| **pfx** | 예, "Certificate" 자격 증명 유형의 경우 | "@parameters('pfxParam') | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 |
| **암호** | 예, "Certificate" 자격 증명 유형의 경우 | "@parameters('passwordParam')" | PFX 파일에 액세스하기 위한 암호 |
| **암호** | 예, "Secret" 자격 증명 유형의 경우 | "@parameters('secretParam')" | 권한 부여를 요청하는 클라이언트 비밀 |
|||||

이 예제 HTTP 작업 정의에서는 `authentication` 섹션이 `ActiveDirectoryOAuth` 인증 및 "비밀" 자격 증명 형식을 지정합니다. 매개 변수 사용 및 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://www.microsoft.com",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
         "audience": "https://management.core.windows.net/",
         "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

> [!IMPORTANT]
> 논리 앱 워크플로 정의가 처리하는 중요한 정보를 보호해야 합니다. 보안 매개 변수를 사용하고 필요에 따라 데이터를 인코딩하십시오. 매개 변수 보호에 대한 자세한 내용은 [논리 앱 보호](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 대해 자세히 알아봅니다.
