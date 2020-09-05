---
title: HTTPS를 사용하여 호출 수신 및 응답
description: Azure Logic Apps를 사용하여 외부 서비스에서 인바운드 HTTPS 요청 처리
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
tags: connectors
ms.openlocfilehash: 05ce944d195cf43f860fc2b39975a736a4454c05
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226517"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Azure Logic Apps에서 인바운드 HTTPS 요청 수신 및 응답

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 요청 트리거 및 응답 작업을 사용 하 여 HTTPS를 통해 인바운드 요청을 받을 수 있는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 아웃 바운드 요청을 대신 보내려면 기본 제공 [http 트리거 또는 http 동작](../connectors/connectors-native-http.md)을 사용 합니다.

예를 들어 논리 앱을 통해 다음을 수행할 수 있습니다.

* 온-프레미스 데이터베이스에서 데이터에 대한 HTTPS 요청을 수신하고 응답합니다.

* 외부 웹후크 이벤트가 발생하는 경우 워크플로를 트리거합니다.

* 다른 논리 앱의 HTTPS 호출을 수신하고 응답합니다.

이 문서에서는 논리 앱이 인바운드 호출을 수신 하 고 응답할 수 있도록 요청 트리거 및 응답 작업을 사용 하는 방법을 보여 줍니다.

[TLS (전송 계층 보안](https://en.wikipedia.org/wiki/Transport_Layer_Security)), 이전에 SSL(SECURE SOCKETS LAYER) (SSL) 또는 [Azure Active Directory 오픈 인증 (Azure AD OAuth)](../active-directory/develop/index.yml)과 같은 논리 앱에 대 한 인바운드 호출에 대 한 암호화, 보안 및 권한 부여에 대 한 자세한 내용은 [요청 기반 트리거에 대 한 인바운드 호출에 대 한 보안 액세스 및 데이터 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 가입](https://azure.microsoft.com/free/)할 수 있습니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps 된 항목](../logic-apps/logic-apps-overview.md)을 검토 하세요.

<a name="add-request"></a>

## <a name="add-request-trigger"></a>요청 트리거 추가

이 기본 제공 트리거는 HTTPS를 *통한 인바운드 요청만* 처리할 수 있는 수동으로 호출할 수 있는 끝점을 만듭니다. 호출자가이 끝점에 요청을 보내면 [요청 트리거가](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) 발생 하 고 논리 앱이 실행 됩니다. 이 트리거를 호출 하는 방법에 대 한 자세한 내용은 [Azure Logic Apps에서 HTTPS 끝점을 사용 하 여 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조 하세요.

논리 앱은 [제한 된 시간](../logic-apps/logic-apps-limits-and-config.md#request-limits)동안만 인바운드 요청을 열어 둡니다. 논리 앱이 [응답 작업](#add-response)을 포함 하 고 있다고 가정 하면 논리 앱이이 시간이 지난 후에 호출자에 게 응답을 보내지 않는 경우 논리 앱 `504 GATEWAY TIMEOUT` 은 호출자에 게 상태를 반환 합니다. 논리 앱에 응답 동작이 포함 되지 않은 경우 
> 논리 앱 `202 ACCEPTED` 은 호출자에 게 즉시 상태를 반환 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 열린 후, 검색 상자에 `http request`를 필터로 입력합니다. 트리거 목록에서 **HTTP 요청을 받을 때** 트리거를 선택 합니다.

   ![요청 트리거 선택](./media/connectors-native-reqres/select-request-trigger.png)

   요청 트리거는 다음과 같은 속성을 표시합니다.

   ![요청 트리거](./media/connectors-native-reqres/request-trigger.png)

   | 속성 이름 | JSON 속성 이름 | 필수 | Description |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL** | {없음} | 예 | 논리 앱을 저장한 후에 생성되고 논리 앱을 호출하는 데 사용되는 엔드포인트 URL입니다. |
   | **요청 본문 JSON 스키마** | `schema` | 예 | 들어오는 요청 본문의 속성 및 값을 설명하는 JSON 스키마입니다. |
   |||||

1. **요청 본문 JSON 스키마** 상자에서 들어오는 요청의 본문을 설명하는 JSON 스키마를 선택적으로 입력합니다. 예를 들면 다음과 같습니다.

   ![JSON 스키마 예제](./media/connectors-native-reqres/provide-json-schema.png)

   디자이너는 이 스키마를 사용하여 요청에서 속성에 대한 토큰을 생성합니다. 이렇게 하면 논리 앱에서 트리거를 통해 요청에서 데이터를 구문 분석하고, 사용하여 워크플로로 전달할 수 있습니다.

   다음은 샘플 스키마입니다.

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   JSON 스키마를 입력하면 요청에 `Content-Type` 헤더를 포함하고 해당 헤더 값을 `application/json`으로 설정하는 미리 알림이 디자이너에 표시됩니다. 자세한 내용은 [콘텐츠 유형 처리](../logic-apps/logic-apps-content-type.md)를 참조하세요.

   ![“Content-Type” 헤더를 포함하는 미리 알림](./media/connectors-native-reqres/include-content-type.png)

   JSON 형식의 이 헤더는 다음과 같습니다.

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   예상 페이로드(데이터)를 기반으로 하는 JSON 스키마를 생성하려면 [JSONSchema.net](https://jsonschema.net)과 같은 도구를 사용하거나 다음 단계를 수행합니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

      !["샘플 페이로드를 사용 하 여 스키마 생성"을 선택 하는 스크린샷](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. 샘플 페이로드를 입력하고 **완료**를 선택합니다.

      ![샘플 페이로드를 입력 하 여 스키마 생성](./media/connectors-native-reqres/enter-payload.png)

      다음은 샘플 페이로드입니다.

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": {
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. 인바운드 호출에 지정 된 스키마와 일치 하는 요청 본문이 있는지 확인 하려면 다음 단계를 수행 합니다.

   1. 요청 트리거의 제목 표시줄에서 줄임표 단추 (**...**)를 선택 합니다.

   1. 트리거의 설정에서 **스키마 유효성 검사**를 켜고 **완료**를 선택 합니다.

      인바운드 호출의 요청 본문이 스키마와 일치 하지 않는 경우 트리거는 오류를 반환 합니다 `HTTP 400 Bad Request` .

1. 추가 속성을 지정하려면 **새 매개 변수 추가** 목록을 열고 추가하려는 매개 변수를 선택합니다.

   | 속성 이름 | JSON 속성 이름 | 필수 | Description |
   |---------------|--------------------|----------|-------------|
   | **메서드** | `method` | 예 | 들어오는 요청에서 논리 앱을 호출하는 데 사용해야 하는 메서드 |
   | **상대 경로** | `relativePath` | 예 | 논리 앱의 엔드포인트의 URL이 수락할 수 있는 매개 변수에 대한 상대 경로입니다. |
   |||||

   이 예제에서는 **메서드** 속성을 추가합니다.

   ![메서드 매개 변수 추가](./media/connectors-native-reqres/add-parameters.png)

   목록에서 메서드를 선택할 수 있도록 **메서드** 속성이 트리거에 표시됩니다.

   ![Select 메서드](./media/connectors-native-reqres/select-method.png)

1. 이제 워크플로의 다음 단계로 다른 작업을 추가합니다. 트리거 아래에서 **다음 단계**를 선택하면 추가하려는 작업을 찾을 수 있습니다.

   예를 들어 사용자 지정된 응답을 반환하는 데 사용할 수 있으며 이 토픽의 뒷부분에서 설명하는 [응답 작업을 추가](#add-response)하여 요청에 응답할 수 있습니다.

   논리 앱은 [제한된 시간](../logic-apps/logic-apps-limits-and-config.md#request-limits) 동안만 들어오는 요청을 열어 둡니다. 논리 앱 워크플로에 응답 작업이 포함되어 있다고 가정하면 논리 앱이 이 시간 경과 후 응답을 반환하지 않는 경우 논리 앱은 호출자에게 `504 GATEWAY TIMEOUT`을 반환합니다. 그렇지 않은 경우 논리 앱에 응답 작업이 포함되어 있지 않으면 논리 앱은 즉시 호출자에게 `202 ACCEPTED` 응답을 반환합니다.

1. 완료되면 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   이 단계에서는 논리 앱을 트리거하는 요청을 보내는 데 사용할 URL을 생성합니다. 이 URL을 복사하려면 URL 옆에 있는 복사 아이콘을 선택합니다.

   ![논리 앱을 트리거하는 데 사용할 URL](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > **#** 요청 트리거를 호출할 때 해시 또는 파운드 기호 ()를 URI에 포함 하려면이 인코딩된 버전을 대신 사용 합니다.`%25%23`

1. 논리 앱을 트리거하려면 생성된 URL에 HTTP POST를 보냅니다.

   예를 들어 [Postman](https://www.getpostman.com/)과 같은 도구를 사용하여 HTTP POST를 보낼 수 있습니다. 트리거의 기본 JSON 정의 및 이 트리거를 호출하는 방법에 대한 자세한 내용은 [요청 트리거 형식](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) 및 [Azure Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

## <a name="trigger-outputs"></a>트리거 출력

요청 트리거의 출력에 대한 자세한 내용은 다음과 같습니다.

| JSON 속성 이름 | 데이터 형식 | Description |
|--------------------|-----------|-------------|
| `headers` | Object | 요청의 헤더를 설명하는 JSON 개체입니다. |
| `body` | Object | 요청의 본문 콘텐츠를 설명하는 JSON 개체입니다. |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>응답 작업 추가

요청 트리거를 사용 하 여 인바운드 요청을 처리 하는 경우 기본 제공 [응답 작업](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)을 사용 하 여 응답을 모델링 하 고 페이로드 결과를 다시 호출자에 게 보낼 수 있습니다. 응답 *작업은* 요청 트리거와 함께 사용할 수 있습니다. 요청 트리거 및 응답 작업과 함께이 조합은 [요청-응답 패턴](https://en.wikipedia.org/wiki/Request%E2%80%93response)을 만듭니다. Foreach 루프와 Until 루프, 병렬 분기를 제외 하 고, 워크플로의 아무 곳에 나 응답 작업을 추가할 수 있습니다.

> [!IMPORTANT]
> 응답 작업에 이러한 헤더가 포함된 경우 Logic Apps는 경고 또는 오류를 표시하지 않고 생성된 응답 메시지에서 이러한 헤더를 제거합니다.
>
> * `Allow`
> * `Content-Disposition`, `Content-Encoding` 및 `Content-Type`과 같은 예외가 있는 `Content-*`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> 논리 앱에서 사용자가 이러한 헤더를 사용하여 응답 작업이 있는 논리 앱을 저장할 수는 있지만, 논리 앱은 이러한 헤더를 무시합니다.

1. 논리 앱 디자이너에서 응답 작업을 추가하려는 단계 아래에서 **새 단계**를 선택합니다.

   예를 들어 앞에 나온 요청 트리거를 사용합니다.

   ![새 단계 추가](./media/connectors-native-reqres/add-response.png)

   단계 사이에 작업을 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시되는 더하기 기호( **+** )를 선택한 다음, **작업 추가**를 선택합니다.

1. **작업 선택**아래의 검색 상자에서를 `response` 필터로 입력 하 고 **응답** 작업을 선택 합니다.

   ![응답 작업 선택](./media/connectors-native-reqres/select-response-action.png)

   이 예제에서는 편의상 요청 트리거가 축소되어 있습니다.

1. 응답 메시지에 필요한 값을 추가합니다.

   일부 필드에서 상자 내부를 클릭하면 동적 콘텐츠 목록이 열립니다. 그런 다음, 워크플로의 이전 단계에서 사용 가능한 출력을 나타내는 토큰을 선택할 수 있습니다. 이전 예제에 지정된 스키마의 속성이 이제 동적 콘텐츠 목록에 표시됩니다.

   예를 들어 **헤더** 상자의 경우 이 토픽의 앞부분에서 설명한 것처럼 `Content-Type`을 키 이름으로 포함하고 키 값을 `application/json`으로 설정합니다. **본문** 상자의 경우 동적 콘텐츠 목록에서 트리거 본문 출력을 선택할 수 있습니다.

   ![응답 작업 세부 정보](./media/connectors-native-reqres/response-details.png)

   헤더를 JSON 형식으로 보려면 **텍스트 뷰로 전환**를 선택합니다.

   ![머리글 - 텍스트 뷰로 전환](./media/connectors-native-reqres/switch-to-text-view.png)

   응답 작업에서 설정할 수 있는 속성에 대한 자세한 내용은 다음을 참조하세요.

   | 속성 이름 | JSON 속성 이름 | 필수 | Description |
   |---------------|--------------------|----------|-------------|
   | **상태 코드** | `statusCode` | 예 | 응답에 반환할 상태 코드 |
   | **헤더** | `headers` | 예 | 응답에 포함할 하나 이상의 헤더를 설명하는 JSON 개체입니다. |
   | **본문** | `body` | 예 | 응답 본문 |
   |||||

1. 응답 본문에 대한 JSON 스키마와 같은 추가 속성을 지정하려면 **새 매개 변수 추가** 목록을 열고 추가하려는 매개 변수를 선택합니다.

1. 완료되면 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

* [요청 기반 트리거에 대 한 인바운드 호출에 대 한 보안 액세스 및 데이터 액세스](../logic-apps/logic-apps-securing-a-logic-app.md#secure-inbound-requests)
* [Logic Apps용 커넥터](../connectors/apis-list.md)