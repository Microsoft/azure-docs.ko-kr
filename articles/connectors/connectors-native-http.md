---
title: HTTP 및 HTTPS 끝점 호출
description: Azure Logic Apps를 사용 하 여 HTTP 및 HTTPS 끝점에 보내는 요청 보내기
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 9c1b2af8d06c9466ed6c82308de941b43510238a
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77117985"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 HTTP 또는 HTTPS 끝점에 보내는 호출 보내기

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 http 트리거 또는 작업을 사용 하 여 HTTP 또는 HTTPS 끝점으로 요청을 정기적으로 전송 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 들어오는 HTTP 또는 HTTPS 호출을 수신 하 고 대신 응답 하려면 기본 제공 [요청 트리거 또는 응답 작업](../connectors/connectors-native-reqres.md)을 사용 합니다.

예를 들어 지정 된 일정에 따라 해당 끝점을 확인 하 여 웹 사이트에 대 한 서비스 끝점을 모니터링할 수 있습니다. 웹 사이트의 작동이 중단 되는 경우와 같이 해당 끝점에서 특정 이벤트가 발생 하면 이벤트는 논리 앱의 워크플로를 트리거하고 지정 된 작업을 실행 합니다.

정기적으로 끝점을 검사 하거나 *폴링하기* 위해 워크플로의 첫 단계로 HTTP 트리거를 사용할 수 있습니다. 각 검사에서 트리거는 엔드포인트에 호출 또는 *요청*을 전송합니다. 엔드포인트의 응답은 논리 앱의 워크플로가 실행될지 여부를 결정합니다. 트리거는 응답에서 논리 앱의 작업으로 모든 콘텐츠를 전달합니다.

원하는 경우 엔드포인트 호출을 위해 워크플로의 다른 단계로 HTTP 작업을 사용할 수 있습니다. 엔드포인트의 응답은 워크플로의 나머지 작업을 실행하는 방법을 결정합니다.

대상 끝점의 기능을 기반으로 하는 HTTP 커넥터는 TLS (전송 계층 보안) 버전 1.0, 1.1 및 1.2을 지원 합니다. 가능 하면 지원 되는 가장 높은 버전을 사용 하 여 끝점에 대 한 Logic Apps 협상 합니다. 따라서 예를 들어 끝점이 1.2을 지 원하는 경우 커넥터는 1.2를 먼저 사용 합니다. 그렇지 않으면 커넥터에서 지원 되는 가장 높은 다음 버전을 사용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 호출 하려는 대상 끝점의 URL입니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps 된 항목](../logic-apps/logic-apps-overview.md)을 검토 하세요.

* 대상 끝점을 호출 하려는 논리 앱입니다. HTTP 트리거를 시작 하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP 작업을 사용 하려면 원하는 트리거를 사용 하 여 논리 앱을 시작 합니다. 이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용 합니다.

## <a name="add-an-http-trigger"></a>HTTP 트리거 추가

이 기본 제공 트리거는 끝점에 대해 지정 된 URL에 대 한 HTTP 호출을 수행 하 고 응답을 반환 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. **작업 선택**아래의 검색 상자에 "http"를 필터로 입력 합니다. **트리거** 목록에서 **HTTP** 트리거를 선택 합니다.

   ![HTTP 트리거 선택](./media/connectors-native-http/select-http-trigger.png)

   이 예에서는 단계가 보다 설명적인 이름을 갖도록 "HTTP 트리거"로 트리거의 이름을 바꿉니다. 또한 나중에이 예제에서는 HTTP 작업을 추가 하 고 두 이름이 모두 고유 해야 합니다.

1. 대상 끝점에 대 한 호출에 포함 하려는 [HTTP 트리거 매개 변수에](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) 대 한 값을 제공 합니다. 트리거가 대상 끝점을 확인 하는 빈도에 대 한 되풀이를 설정 합니다.

   ![HTTP 트리거 매개 변수 입력](./media/connectors-native-http/http-trigger-parameters.png)

   **없음**이외의 인증 유형을 선택 하는 경우 인증 설정은 선택 사항에 따라 달라 집니다. HTTP에 사용할 수 있는 인증 유형에 대 한 자세한 내용은 다음 항목을 참조 하세요.

   * [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리 id를 사용 하 여 리소스에 대 한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="add-an-http-action"></a>HTTP 동작 추가

이 기본 제공 작업은 끝점에 대해 지정 된 URL에 대 한 HTTP 호출을 수행 하 고 응답을 반환 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용 합니다.

1. HTTP 작업을 추가 하려는 단계 아래에서 **새 단계**를 선택 합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 "http"를 필터로 입력 합니다. **작업** 목록에서 **HTTP** 작업을 선택 합니다.

   ![HTTP 작업 선택](./media/connectors-native-http/select-http-action.png)

   이 예에서는 단계에 보다 설명적인 이름을 포함 하도록 동작의 이름을 "HTTP action"으로 바꿉니다.

1. 대상 끝점에 대 한 호출에 포함 하려는 [HTTP 작업 매개 변수의](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) 값을 제공 합니다.

   ![HTTP 작업 매개 변수 입력](./media/connectors-native-http/http-action-parameters.png)

   **없음**이외의 인증 유형을 선택 하는 경우 인증 설정은 선택 사항에 따라 달라 집니다. HTTP에 사용할 수 있는 인증 유형에 대 한 자세한 내용은 다음 항목을 참조 하세요.

   * [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [관리 id를 사용 하 여 리소스에 대 한 액세스 인증](../logic-apps/create-managed-service-identity.md)

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="content-with-multipartform-data-type"></a>Multipart/form 데이터 형식의 콘텐츠

HTTP 요청에 `multipart/form-data` 형식이 있는 콘텐츠를 처리 하려면이 형식을 사용 하 여 `$content-type` 및 `$multipart` 특성을 포함 하는 JSON 개체를 HTTP 요청의 본문에 추가할 수 있습니다.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

예를 들어 `multipart/form-data` 유형을 지 원하는 사이트의 API를 사용 하 여 Excel 파일에 대 한 HTTP POST 요청을 웹 사이트로 보내는 논리 앱이 있다고 가정 합니다. 이 작업의 모양은 다음과 같습니다.

![다중 파트 폼 데이터](./media/connectors-native-http/http-action-multipart.png)

다음은 기본 워크플로 정의에서 HTTP 작업의 JSON 정의를 보여 주는 동일한 예제입니다.

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>커넥터 참조

트리거 및 동작 매개 변수에 대 한 자세한 내용은 다음 섹션을 참조 하십시오.

* [HTTP 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP 동작 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>출력 세부 정보

이 정보를 반환 하는 HTTP 트리거 또는 작업의 출력에 대 한 자세한 내용은 다음과 같습니다.

| 속성 이름 | Type | Description |
|---------------|------|-------------|
| headers | object | 요청의 헤더입니다. |
| 본문 | object | JSON 개체 | 요청의 본문 내용이 포함 된 개체입니다. |
| 상태 코드 | int | 요청의 상태 코드 |
|||

| 상태 코드 | Description |
|-------------|-------------|
| 200 | 확인 |
| 202 | 수락됨 |
| 400 | 잘못된 요청 |
| 401 | 권한 없음 |
| 403 | 사용할 수 없음 |
| 404 | 찾을 수 없음 |
| 500 | 내부 서버 오류. 알 수 없는 오류 발생. |
|||

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
