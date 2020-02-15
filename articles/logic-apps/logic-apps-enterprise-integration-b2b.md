---
title: B2B 엔터프라이즈 통합 시나리오용 메시지 교환
description: 엔터프라이즈 통합 팩를 사용 하 여 Azure Logic Apps 거래 파트너 간에 B2B 메시지 수신 및 전송
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151203"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩를 사용 하 여 B2B 메시지 수신 및 전송

거래 업체 및 규약을 정의 하는 통합 계정이 있는 경우 [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)와 [Azure Logic Apps](../logic-apps/logic-apps-overview.md) 를 사용 하 여 거래 업체 간에 메시지를 교환 하는 자동화 된 b2b (비즈니스 간) 워크플로를 만들 수 있습니다. Azure Logic Apps는 AS2, X12, EDIFACT 및 RosettaNet 산업 표준 프로토콜을 지 원하는 커넥터를 사용 하 여 작동 합니다. 이러한 커넥터를 [Logic Apps에서 사용할 수](../connectors/apis-list.md)있는 다른 커넥터 (예: Salesforce 및 Office 365 Outlook)와 결합할 수도 있습니다.

이 문서에서는 요청 트리거를 사용 하 여 HTTP 요청을 수신 하 고, AS2 및 X12 작업을 사용 하 여 메시지 콘텐츠를 디코딩하고, 응답 작업을 사용 하 여 응답을 반환 하는 논리 앱을 만드는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 아직 구독이 없는 경우 [무료 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 새 논리 앱: 다음 작업을 수행 하는 [요청](../connectors/connectors-native-reqres.md) 트리거를 사용 하 여 B2B 워크플로를 만들 수 있습니다.

  * [AS2 디코드](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [조건](../logic-apps/logic-apps-control-flow-conditional-statement.md)-AS2 디코드 작업이 성공 또는 실패 여부에 따라 [응답](../connectors/connectors-native-reqres.md) 을 보냅니다.

  * [X12 메시지 디코딩](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  논리 앱을 처음 접하는 경우 [Azure Logic Apps?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토 하세요.

* Azure 구독과 연결 되 고 논리 앱에 연결 된 [통합 계정](../logic-apps/logic-apps-enterprise-integration-accounts.md) 입니다. 논리 앱과 통합 계정은 모두 동일한 위치 또는 Azure 지역에 있어야 합니다.

* 해당 파트너에 대 한 [AS2 및 X12 계약과](logic-apps-enterprise-integration-agreements.md) 함께 통합 계정에 이미 정의 된 두 개 이상의 [거래 업체](../logic-apps/logic-apps-enterprise-integration-partners.md) .

## <a name="add-request-trigger"></a>요청 트리거 추가

이 예제에서는 Azure Portal에서 논리 앱 디자이너를 사용 하지만 Visual Studio의 논리 앱 디자이너에 대해 비슷한 단계를 따를 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 `when a http request`를 입력 하 고 트리거로 사용할 **HTTP 요청을 받을 때** 를 선택 합니다.

   ![논리 앱 워크플로를 시작 하려면 요청 트리거를 선택 합니다.](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. X12 메시지가 플랫 파일 이므로 **요청 본문 JSON 스키마** 상자를 비워 둡니다.

   !["요청 본문 JSON 스키마"를 비워 둡니다.](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. 작업이 완료 되 면 디자이너 도구 모음에서 **저장**을 선택 합니다.

   이 단계에서는 논리 앱을 트리거하는 요청을 보내는 데 사용할 **HTTP POST URL** 을 생성 합니다. 이 URL을 복사 하려면 URL 옆에 있는 복사 아이콘을 선택 합니다.

   ![호출을 받기 위한 요청 트리거에 대해 생성 된 URL입니다.](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>AS2 디코드 작업 추가

이제 사용 하려는 B2B 작업을 추가 합니다. 이 예제에서는 AS2 및 X12 작업을 사용 합니다.

1. 트리거 아래에서 **새 단계**를 선택합니다. 트리거 세부 정보를 숨기려면 트리거의 제목 표시줄을 클릭 합니다.

   ![논리 앱 워크플로에 다른 단계 추가](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. **작업 선택**아래의 검색 상자에 `as2 decode`를 입력 하 고 **AS2 디코드 (v2)** 를 선택 합니다.

   !["AS2 디코드 (v2)"를 찾아 선택 합니다.](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. **디코딩할 메시지** 속성에 대해 AS2 작업이 디코딩할 입력 (HTTP 요청 트리거에서 받은 `body` 콘텐츠)을 입력 합니다. 동적 콘텐츠 목록이 나 식으로이 콘텐츠를 입력으로 지정 하는 여러 가지 방법이 있습니다.

   * 사용 가능한 트리거 출력을 표시 하는 목록에서 선택 하려면 **디코딩할 메시지** 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시 되 면 **HTTP 요청을 받을 때**아래에서 **Body** 속성 값을 선택 합니다. 예를 들면 다음과 같습니다.

     ![트리거에서 "Body" 값을 선택 합니다.](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * 트리거의 `body` 출력을 참조 하는 식을 입력 하려면 **디코딩할 메시지** 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시 되 면 **식**을 선택 합니다. 식 편집기에서 여기에 식을 입력 하 고 **확인**을 선택 합니다.

     `triggerOutputs()['body']`

     또는 **디코딩할 메시지** 상자에서이 식을 직접 입력 합니다.

     `@triggerBody()`

     이 식은 **본문** 토큰으로 확인 됩니다.

     ![트리거에서 확인 된 본문 출력](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. **메시지 헤더** 속성에 대해 HTTP 요청 트리거에서 받은 `headers` 콘텐츠에서 설명 된 AS2 작업에 필요한 헤더를 입력 합니다.

   트리거의 `headers` 출력을 참조 하는 식을 입력 하려면 **메시지 헤더** 상자 내부를 클릭 합니다. 동적 콘텐츠 목록이 표시 되 면 **식**을 선택 합니다. 식 편집기에서 여기에 식을 입력 하 고 **확인**을 선택 합니다.

   `triggerOutputs()['Headers']`

   이 토큰을이 토큰으로 확인 하려면 디자이너와 코드 뷰 간을 전환 합니다. 예를 들면 다음과 같습니다.

   ![트리거에서 확인 된 헤더 출력](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>메시지 수신 알림에 대 한 응답 작업 추가

거래 파트너에 게 메시지를 수신 했음을 알리려면 **응답** 작업을 사용 하 여 AS2 MDN (메시지 처리 알림)이 포함 된 응답을 반환 하면 됩니다. **AS2 디코드** 작업 바로 뒤에이 작업을 추가 하면 작업이 실패 하 고 논리 앱이 계속 처리 되지 않습니다.

1. **AS2 디코드** 작업에서 **새 단계**를 선택 합니다.

1. **작업 선택**아래의 검색 상자 아래에서 **기본 제공**을 선택 합니다. 검색 상자에 `condition`를 입력합니다. **작업** 목록에서 **조건**을 선택 합니다.

   !["조건" 동작 추가](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   이제 조건이 충족 되었는지 여부에 대 한 경로를 포함 하 여 condition 셰이프가 나타납니다.

   ![의사 결정 경로가 있는 Condition 셰이프](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. 이제 평가할 조건을 지정 합니다. **값 선택** 상자에 다음 식을 입력 합니다.

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   가운데 상자에서 비교 작업이 `is equal to`로 설정 되었는지 확인 합니다. 오른쪽 상자에 `Expected`값을 입력 합니다. 이 토큰으로 확인할 식을 얻으려면 디자이너와 코드 뷰 간을 전환 합니다.

   ![의사 결정 경로가 있는 Condition 셰이프](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. 이제 **AS2 디코드** 작업 성공 여부를 반환 하는 응답을 지정 합니다.

   1. **AS2 디코드** 작업이 성공 하는 경우 **true** 셰이프에서 **동작 추가**를 선택 합니다. **작업 선택**아래의 검색 상자에 `response`를 입력 하 고 **응답**을 선택 합니다.

      !["응답" 작업을 찾아 선택 합니다.](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. As2 **디코드** 작업의 출력에서 as2 MDN에 액세스 하려면 다음과 같은 식을 지정 합니다.

      * **응답** 동작의 **헤더** 속성에 다음 식을 입력 합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * **응답** 동작의 **Body** 속성에 다음 식을 입력 합니다.

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. 토큰으로 확인할 식을 얻으려면 디자이너와 코드 뷰 사이를 전환 합니다.

      ![AS2 MDN에 액세스 하기 위한 확인 된 식](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. **AS2 디코드** 작업이 실패 하는 경우 **If False** 셰이프에서 **동작 추가**를 선택 합니다. **작업 선택**아래의 검색 상자에 `response`를 입력 하 고 **응답**을 선택 합니다. **응답** 작업을 설정 하 여 원하는 상태와 오류를 반환 합니다.

1. 논리 앱을 저장합니다.

## <a name="add-decode-x12-message-action"></a>X12 메시지 디코딩 작업 추가

1. 이제 **X12 메시지 디코딩** 작업을 추가 합니다. **응답** 작업에서 **작업 추가**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 `x12 decode`를 입력 하 고 **X12 메시지 디코딩**을 선택 합니다.

   !["X12 메시지 디코딩" 작업을 찾아 선택 합니다.](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. X12 작업에서 연결 정보를 묻는 메시지가 표시 되 면 연결의 이름을 입력 하 고 사용 하려는 통합 계정을 선택한 다음 **만들기**를 선택 합니다.

   ![통합 계정에 대 한 X12 연결 만들기](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. 이제 X12 동작에 대 한 입력을 지정 합니다. 이 예제에서는 메시지 콘텐츠 인 AS2 작업의 출력을 사용 하지만,이 콘텐츠는 JSON 개체 형식이 며 base64 인코딩 됨을 확인 합니다. 따라서이 콘텐츠를 문자열로 변환 해야 합니다.

   **디코딩할 X12 플랫 파일 메시지** 상자에서 다음 식을 입력 하 여 AS2 출력을 변환 합니다.

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    이 토큰으로 확인할 식을 얻으려면 디자이너와 코드 뷰 간을 전환 합니다.

    ![B a s e 64로 인코딩된 콘텐츠를 문자열로 변환](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. 논리 앱을 저장합니다.

   메시지 내용을 디코딩하고 JSON 개체 형식으로 해당 콘텐츠를 출력 하는 등이 논리 앱에 대 한 추가 단계가 필요한 경우 논리 앱 빌드를 계속 합니다.

이제 B2B 논리 앱을 설정 하는 작업이 완료 되었습니다. 실제 앱에서는 디코딩된 X12 데이터를 LOB (기간 업무) 앱 또는 데이터 저장소에 저장할 수 있습니다. 예를 들어 다음 문서를 참조 하세요.

* [Azure Logic Apps에서 SAP 시스템에 연결](../logic-apps/logic-apps-using-sap-connector.md)
* [SSH 및 Azure Logic Apps를 사용 하 여 SFTP 파일 모니터링, 만들기 및 관리](../connectors/connectors-sftp-ssh.md)

사용자 고유의 LOB 앱을 연결 하 고 논리 앱에서 이러한 Api를 사용 하려면 더 많은 작업을 추가 하거나 [사용자 지정 api를 작성할](../logic-apps/logic-apps-create-api-app.md)수 있습니다.

## <a name="next-steps"></a>다음 단계

* [들어오는 HTTPS 호출 수신 및 응답](../connectors/connectors-native-reqres.md)
* [B2B 엔터프라이즈 통합용 AS2 메시지 교환](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [B2B 엔터프라이즈 통합용 X12 메시지 교환](../logic-apps/logic-apps-enterprise-integration-x12.md)
* [엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md) 에 대 한 자세한 정보