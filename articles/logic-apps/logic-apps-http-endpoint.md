---
title: 논리 앱 호출, 트리거 또는 중첩
description: Azure Logic Apps에서 논리 앱 워크플로를 호출, 트리거 또는 중첩 하기 위해 HTTP 끝점을 설정 합니다.
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191341"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 끝점을 사용 하 여 논리 앱 호출, 트리거 또는 중첩

논리 앱이 다른 서비스에서 들어오는 요청을 받을 수 있도록 URL을 통해 논리 앱을 호출할 수 있도록 하려면 기본적으로 동기 HTTP 끝점을 해당 논리 앱의 트리거로 제공 하면 됩니다. 이 기능을 설정 하면 논리 앱을 다른 논리 앱 내에 중첩 하 여 호출 가능 끝점의 패턴을 만들 수도 있습니다.

HTTP 끝점을 설정 하기 위해 논리 앱에서 들어오는 요청을 받을 수 있도록 하는 이러한 트리거 형식 중 하나를 사용할 수 있습니다.

* [요청](../connectors/connectors-native-reqres.md)
* [HTTP 웹후크](../connectors/connectors-native-webhook.md)
* [ApiConnectionWebhook 형식이](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) 있고 들어오는 HTTP 요청을 받을 수 있는 관리 되는 커넥터 트리거

> [!NOTE]
> 이러한 예제에서는 요청 트리거를 사용 하지만 이전 목록에 있는 모든 HTTP 요청 기반 트리거를 사용할 수 있습니다. 모든 원칙은 이러한 다른 트리거 유형에 동일 하 게 적용 됩니다.

논리 앱을 처음 접하는 경우 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* HTTP 끝점을 트리거로 설정 하려는 논리 앱입니다. 빈 논리 앱 또는 현재 트리거를 바꾸려는 기존 논리 앱으로 시작할 수 있습니다. 이 예에서는 빈 논리 앱으로 시작 합니다.

## <a name="create-a-callable-endpoint"></a>호출 가능 끝점 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 만들고 엽니다.

   이 예제에서는 요청 트리거를 사용 하지만 들어오는 HTTP 요청을 받을 수 있는 트리거를 사용할 수 있습니다. 모든 원칙은 이러한 트리거에 동일 하 게 적용 됩니다. 요청 트리거에 대 한 자세한 내용은 [Azure Logic Apps를 사용 하 여 들어오는 HTTPS 호출 받기 및 응답](../connectors/connectors-native-reqres.md)을 참조 하세요.

1. 검색 상자 아래에서 **기본 제공**을 선택 합니다. 검색 상자에서 필터로 `request`을 입력합니다. 트리거 목록에서 **HTTP 요청을 받을 때**를 선택 합니다.

   ![요청 트리거 찾기 및 선택](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. 필요에 따라 **본문 Json 스키마 요청** 상자에서 트리거를 받을 것으로 간주 되는 페이로드 또는 데이터를 설명 하는 json 스키마를 입력할 수 있습니다.

   디자이너는이 스키마를 사용 하 여 트리거 출력을 나타내는 토큰을 생성 합니다. 그런 다음 논리 앱의 워크플로 전체에서 이러한 출력을 쉽게 참조할 수 있습니다. [JSON 스키마에서 생성 된 토큰](#generated-tokens)에 대해 자세히 알아보세요.

   이 예에서는 다음 스키마를 입력 합니다.

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![요청 작업에 대 한 JSON 스키마 제공](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   또는 샘플 페이로드를 제공 하 여 JSON 스키마를 생성할 수 있습니다.

   1. **요청** 트리거에서 **샘플 페이로드를 사용 하 여 스키마 생성을**선택 합니다.

   1. **샘플 JSON 페이로드 입력 또는 붙여넣기** 상자에 샘플 페이로드를 입력 합니다. 예를 들면 다음과 같습니다.

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. 준비가 되 면 **완료**를 선택 합니다.

      이제 **요청 본문 JSON 스키마** 상자에 생성 된 스키마가 표시 됩니다.

1. 논리 앱을 저장합니다.

   이제 **이 URL에 대 한 HTTP POST** 상자에는 다른 서비스에서 논리 앱을 호출 하 고 트리거하는 데 사용할 수 있는 생성 된 콜백 URL이 표시 됩니다. 이 URL에는 인증에 사용 되는 SAS (공유 액세스 서명) 키가 쿼리 매개 변수에 포함 되어 있습니다. 예를 들면 다음과 같습니다.

   ![엔드포인트에 대해 생성된 콜백 URL](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   논리 앱의 **개요** 창에서 HTTP 끝점 URL을 가져올 수도 있습니다.

   1. 논리 앱의 메뉴에서 **개요**를 선택 합니다.

   1. **요약** 섹션에서 **트리거 기록 참조**를 선택 합니다.

      ![Azure Portal에서 HTTP 엔드포인트 URL 가져오기](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. **콜백 url [POST]** 에서 url을 복사 합니다.

      ![Azure Portal에서 HTTP 끝점 URL을 복사 합니다.](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      또는 다음을 호출하여 URL을 가져올 수도 있습니다.

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>필요한 HTTP 메서드 설정

기본적으로 요청 트리거는 HTTP POST 요청을 예상합니다. 그러나 다른 메서드를 지정 하 고 메서드를 하나만 지정할 수 있습니다.

1. 요청 트리거에서 **새 매개 변수 추가** 목록을 열고 **메서드**를 선택 하 여이 속성을 트리거에 추가 합니다.

   ![트리거에 "메서드" 속성 추가](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. **메서드** 목록에서 instead of 트리거를 필요로 하는 다른 메서드를 선택 합니다. 또는 사용자 지정 메서드를 지정할 수 있습니다.

   예를 들어 나중에 HTTP 끝점의 URL을 테스트할 수 있도록 **GET** 메서드를 선택 합니다.

   ![트리거에 사용할 HTTP 메서드를 선택 합니다.](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>끝점 URL의 매개 변수 허용

끝점 URL에 매개 변수를 허용 하려면 트리거에서 상대 경로를 지정 합니다. 또한 HTTP 요청에 필요한 [메서드](#set-method) 를 명시적으로 설정 해야 합니다.

1. 요청 트리거에서 **새 매개 변수 추가** 목록을 열고이 속성을 트리거에 추가 하는 **상대 경로**를 선택 합니다.

   ![트리거에 "상대 경로" 속성 추가](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. **상대 경로** 속성에서 URL에 허용 하려는 JSON 스키마의 매개 변수에 대 한 상대 경로를 지정 합니다 (예: `address/{postalCode}`).

   ![매개 변수에 대 한 상대 경로를 지정 합니다.](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. 매개 변수를 사용 하려면 논리 앱에 대 한 **응답** 작업을 찾아 추가 합니다.

   1. 요청 트리거에서 **새 단계** > **작업 추가**를 선택 합니다.

   1. **작업 선택** 아래의 검색 상자에 `response`을 필터로 입력합니다.

   1. 작업 목록에서 **응답** 작업을 선택 합니다.

1. 응답 동작의 **Body** 속성에서 트리거의 상대 경로에 지정한 매개 변수를 나타내는 토큰을 포함 합니다.

   예를 들어 응답 작업에서 `Postal Code: {postalCode}`반환 하려고 한다고 가정 합니다.

   **본문** 속성에 후행 공백이 있는 `Postal Code: `를 입력 합니다. 표시 되는 동적 콘텐츠 목록에서 **postalCode** 토큰을 선택 합니다.

   ![지정 된 매개 변수를 응답 본문에 추가 합니다.](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   **Body** 속성에는 이제 선택 된 매개 변수가 포함 됩니다.

   ![매개 변수가 있는 예제 응답 본문](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. 논리 앱을 저장합니다.

    HTTP 엔드포인트 URL은 이제 다음 예와 같은 상대 경로를 포함합니다.

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. HTTP 엔드포인트를 테스트하려면 업데이트된 URL을 복사하여 다른 브라우저 창에 붙여넣되, `{postalCode}`을 `123456`로 바꾸고 Enter 키를 누릅니다.

   브라우저에서 다음 텍스트를 표시 합니다. `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>HTTP 끝점을 통해 논리 앱 호출

HTTP 끝점을 만든 후 끝점의 전체 URL에 HTTP `POST` 요청을 전송 하 여 논리 앱을 트리거할 수 있습니다. Logic Apps는 직접 액세스 엔드포인트에 대한 기본 제공 지원을 포함합니다.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>스키마에서 생성 된 토큰

요청 트리거에서 JSON 스키마를 제공 하는 경우 논리 앱 디자이너는 해당 스키마의 속성에 대 한 토큰을 생성 합니다. 논리 앱 워크플로를 통해 데이터를 전달하는 데 해당 토큰을 사용할 수 있습니다.

예를 들어 `"suite"`와 같은 속성을 JSON 스키마에 추가 하는 경우 논리 앱의 이후 단계에서 해당 속성에 대 한 토큰을 사용할 수 있습니다. 다음은 완료된 JSON 스키마입니다.

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>중첩 된 논리 앱 만들기

요청을 받을 수 있는 다른 논리 앱을 추가하여 Logic Apps에서 워크플로를 중첩할 수 있습니다. 이러한 논리 앱을 포함 하려면 다음 단계를 수행 합니다.

1. 다른 논리 앱을 호출 하려는 단계 아래에서 **새 단계** > **작업 추가**를 선택 합니다.

1. **작업 선택** 아래에서 **기본 제공**을 선택합니다. 검색 상자에서 필터로 `logic apps`을 입력합니다. 작업 목록에서 **Logic Apps 워크플로 선택**을 선택 합니다.

   ![현재 논리 앱 내에서 논리 앱 중첩](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   사용자가 선택할 수 있는 적합 한 논리 앱이 디자이너에 표시 됩니다.

1. 현재 논리 앱에서 호출할 논리 앱을 선택 합니다.

   ![현재 논리 앱에서 호출할 논리 앱을 선택 합니다.](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>들어오는 요청의 콘텐츠 참조

들어오는 요청의 콘텐츠 형식이 `application/json`경우 들어오는 요청에서 속성을 참조할 수 있습니다. 그렇지 않으면이 콘텐츠는 다른 Api에 전달할 수 있는 단일 이진 단위로 처리 됩니다. 논리 앱의 워크플로 내에서이 콘텐츠를 참조 하려면 먼저 해당 콘텐츠를 변환 해야 합니다.

예를 들어 `application/xml` 형식이 있는 콘텐츠를 전달 하는 경우 [`@xpath()` 식을](../logic-apps/workflow-definition-language-functions-reference.md#xpath) 사용 하 여 XPath 추출을 수행 하거나 [`@json()` 식을](../logic-apps/workflow-definition-language-functions-reference.md#json) 사용 하 여 XML을 JSON으로 변환할 수 있습니다. 지원 되는 [콘텐츠 형식을](../logic-apps/logic-apps-content-type.md)사용 하는 방법에 대해 자세히 알아보세요.

들어오는 요청의 출력을 가져오려면 [`@triggerOutputs` 식을](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)사용할 수 있습니다. 예를 들어 다음 예제와 같은 출력을 가정 합니다.

```json
{
   "headers": {
      "content-type" : "application/json"
   },
   "body": {
      "myProperty" : "property value"
   }
}
```

`body` 속성에 액세스 하기 위해 [`@triggerBody()` 식을](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) 바로 가기로 사용할 수 있습니다.

## <a name="respond-to-requests"></a>요청에 응답

호출자에 게 콘텐츠를 반환 하 여 논리 앱을 트리거하는 특정 요청에 응답 하려는 경우가 있습니다. 응답의 상태 코드, 헤더 및 본문을 구성 하려면 응답 작업을 사용 합니다. 이 작업은 워크플로의 끝뿐만 아니라 논리 앱의 어디서나 나타날 수 있습니다. 논리 앱에 응답 작업이 포함 되어 있지 않은 경우 HTTP 끝점은 **202 수락** 상태를 사용 하 여 *즉시* 응답 합니다.

원래 호출자가 성공적으로 응답을 받으려면 트리거된 논리 앱을 중첩 된 논리 앱으로 호출 하지 않는 한 응답에 필요한 모든 단계를 [요청 시간](./logic-apps-limits-and-config.md) 제한 내에 완료 해야 합니다. 이 한도 내에 응답이 반환 되지 않으면 들어오는 요청 시간이 초과 되어 **408 클라이언트 시간 제한** 응답을 받습니다.

중첩 된 논리 앱의 경우 부모 논리 앱은 필요한 시간에 관계 없이 모든 단계가 완료 될 때까지 응답을 계속 기다립니다.

### <a name="construct-the-response"></a>응답 생성

응답 본문에는 여러 헤더와 모든 형식의 콘텐츠를 포함할 수 있습니다. 예를 들어이 응답의 헤더는 응답의 콘텐츠 형식이 `application/json` 되 고 본문에 요청 트리거에 대 한이 항목의 앞부분에서 설명한 JSON 스키마에 따라 `town` 및 `postalCode` 속성에 대 한 값이 포함 되도록 지정 합니다.

![HTTP 응답 작업에 대 한 응답 콘텐츠 제공](./media/logic-apps-http-endpoint/content-for-response-action.png)

응답 속성:

| 속성 (표시) | 속성 (JSON) | Description |
|--------------------|-----------------|-------------|
| **상태 코드** | `statusCode` | 들어오는 요청에 대 한 응답에서 사용할 HTTP 상태 코드입니다. 이 코드는 2xx, 4xx 또는 5xx로 시작하는 모든 유효한 상태 코드가 될 수 있습니다. 하지만 3xx 상태 코드는 허용되지 않습니다. |
| **헤더** | `headers` | 응답에 포함할 하나 이상의 헤더입니다. |
| **본문** | `body` | 문자열, JSON 개체 또는 이전 단계에서 참조 한 이진 콘텐츠 일 수 있는 body 개체입니다. |
||||

논리 앱 디자이너 도구 모음에서 응답 작업 및 논리 앱의 전체 JSON 정의에 대 한 JSON 정의를 보려면 **코드 보기**를 선택 합니다.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>질문과 대답

#### <a name="q-what-about-url-security"></a>Q: URL 보안이란 무엇입니까?

**A**: AZURE는 [SAS (공유 액세스 서명)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)를 사용 하 여 논리 앱 콜백 url을 안전 하 게 생성 합니다. 이 서명은 쿼리 매개 변수로 전달 되 고 논리 앱을 실행 하기 전에 유효성을 검사 해야 합니다. Azure는 논리 앱, 트리거 이름 및 수행되는 작업 별로 비밀 키의 고유한 조합을 사용하여 서명을 생성합니다. 따라서 사용자가 비밀 논리 앱 키에 액세스하지 않으면 유효한 서명을 생성할 수 없습니다.

> [!IMPORTANT]
> 프로덕션 및 고급 보안 시스템의 경우 다음과 같은 이유로 브라우저에서 직접 논리 앱을 호출 하는 것에 대해 적극 권장 합니다.
>
> * URL에 공유 액세스 키가 나타납니다.
> * Azure Logic Apps 고객 간에 공유 도메인으로 인해 보안 콘텐츠 정책을 관리할 수 없습니다.

#### <a name="q-can-i-configure-http-endpoints-further"></a>Q: HTTP 엔드포인트를 추가로 구성할 수 있습니까?

**A**: 예, HTTP 끝점은 [Azure API Management](../api-management/api-management-key-concepts.md)를 통해 더 많은 고급 구성을 지원 합니다. 또한 이 서비스는 Logic Apps를 포함한 모든 API를 일관성 있게 관리하고 사용자 지정 도메인 이름을 설정하고 다음과 같은 더 많은 인증 방법을 사용하는 기능을 제공합니다.

* [요청 메서드 변경](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [요청의 URL 세그먼트 변경](../api-management/api-management-transformation-policies.md#RewriteURL)
* [Azure Portal](https://portal.azure.com/) 에서 API Management 도메인 설정
* 기본 인증을 확인하는 정책 설정

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps를 사용 하 여 들어오는 HTTPS 호출 받기 및 응답](../connectors/connectors-native-reqres.md)