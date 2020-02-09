---
title: 이벤트 대기 및 응답
description: Azure Logic Apps를 사용 하 여 서비스 엔드포인트의 이벤트를 기반으로 트리거, 일시 중지 및 다시 시작 하는 워크플로를 자동화 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: 24746b7bbbbf3985a9801139b301a829c51a14da
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030072"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Azure Logic Apps에서 HTTP 웹 후크를 사용 하 여 자동화 된 이벤트 기반 워크플로 만들기 및 실행

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 기본 제공 http Webhook 커넥터를 사용 하면 논리 앱을 빌드하여 HTTP 또는 HTTPS 엔드포인트에서 발생 하는 특정 이벤트를 기반으로 대기 및 실행 되는 워크플로를 자동화할 수 있습니다. 예를 들어 워크플로를 트리거하기 전에 특정 이벤트를 대기 하 고 해당 엔드포인트을 정기적으로 확인 하거나 *폴링하* 는 대신 지정 된 작업을 실행 하 여 서비스 엔드포인트을 모니터링 하는 논리 앱을 만들 수 있습니다.

몇 가지 예제 이벤트 기반 워크플로는 다음과 같습니다.

* 논리 앱 실행을 트리거하기 전에 [Azure 이벤트 허브](https://github.com/logicappsio/EventHubAPI) 에서 항목이 도착할 때까지 기다립니다.
* 워크플로를 계속 하기 전에 승인을 기다립니다.

## <a name="how-do-webhooks-work"></a>웹 후크는 어떻게 작동 하나요?

HTTP 웹 후크 트리거는 이벤트 기반 이므로 정기적으로 새 항목을 확인 하거나 폴링하는 데 의존 하지 않습니다. 웹 후크 트리거로 시작 하는 논리 앱을 저장 하거나 논리 앱을 사용 안 함에서 사용으로 변경 하는 경우 웹 후크 트리거는 해당 서비스 또는 엔드포인트에 *콜백 URL* 을 등록 하 여 특정 서비스나 엔드포인트을 *구독* 합니다. 그런 다음 트리거는 해당 서비스 또는 엔드포인트이 논리 앱 실행을 시작 하는 URL을 호출 하기를 기다립니다. [요청 트리거와](connectors-native-reqres.md)마찬가지로 논리 앱은 지정 된 이벤트가 발생할 때 즉시 발생 합니다. 트리거를 제거 하 고 논리 앱을 저장 하거나 논리 앱을 사용에서 사용 안 함으로 변경 하면 서비스 또는 엔드포인트에서 *구독을 구독* 취소 합니다.

HTTP 웹 후크 작업은 또한 이벤트 기반 이며, 해당 서비스 또는 엔드포인트에 *콜백 URL* 을 등록 하 여 특정 서비스 또는 엔드포인트을 *구독* 합니다. 웹 후크 작업은 논리 앱의 워크플로를 일시 중지 하 고 논리 앱이 실행을 다시 시작 하기 전에 서비스 또는 엔드포인트이 URL을 호출할 때까지 대기 합니다. 이 경우 작업 논리 앱은 서비스 또는 엔드포인트에서 *구독* 을 취소 합니다.

* Webhook 작업이 성공적으로 완료 되 면
* 응답을 기다리는 동안 논리 앱 실행을 취소 하는 경우
* 논리 앱 시간이 초과 되기 전에

예를 들어 Office 365 Outlook 커넥터의 [**승인 전자 메일 보내기**](connectors-create-api-office365-outlook.md) 작업은이 패턴을 따르는 웹 후크 작업의 예입니다. 웹 후크 작업을 사용 하 여이 패턴을 모든 서비스로 확장할 수 있습니다.

> [!NOTE]
> Logic Apps는 HTTP 웹 후크 트리거 또는 작업으로 콜백할 때 TLS (전송 계층 보안) 1.2을 적용 합니다. SSL 핸드셰이크 오류가 표시 되 면 TLS 1.2을 사용 하는지 확인 합니다. 들어오는 호출의 경우 지원 되는 암호 그룹은 다음과 같습니다.
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

자세한 내용은 다음 항목을 참조하세요.

* [HTTP Webhook 트리거 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [웹 후크 및 구독](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhook을 지 원하는 사용자 지정 Api 만들기](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* 논리 앱에서 웹 후크 [트리거에](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) 대한 webhook 구독 및 구독 취소 패턴을 지 원하는 이미 배포 된 엔드포인트 또는 API에 대한 URL 및 [논리 앱의](../logic-apps/logic-apps-create-api-app.md#webhook-actions) 웹 후크 작업 적절 한 경우

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

* 대상 엔드포인트에서 특정 이벤트를 대기 하려는 논리 앱입니다. HTTP Webhook 트리거로 시작 하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). HTTP Webhook 작업을 사용 하려면 원하는 트리거를 사용 하 여 논리 앱을 시작 합니다. 이 예제에서는 첫 번째 단계로 HTTP 트리거를 사용 합니다.

## <a name="add-an-http-webhook-trigger"></a>HTTP Webhook 트리거 추가

이 기본 제공 트리거는 지정 된 서비스를 사용 하 여 콜백 URL을 등록 하 고 해당 서비스에서 해당 URL로 HTTP POST 요청을 보낼 때까지 대기 합니다. 이 이벤트가 발생 하면 트리거가 발생 하 고 논리 앱을 즉시 실행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 디자이너의 검색 상자에 "http webhook"를 필터로 입력 합니다. **트리거** 목록에서 **HTTP Webhook** 트리거를 선택 합니다.

   ![HTTP Webhook 트리거를 선택 합니다.](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   이 예에서는 단계가 보다 설명적인 이름을 갖도록 "HTTP Webhook 트리거"로 트리거의 이름을 바꿉니다. 또한 나중에이 예제에서는 HTTP Webhook 작업을 추가 하 고 두 이름이 모두 고유 해야 합니다.

1. 구독 및 구독 취소 호출에 사용할 [HTTP Webhook 트리거 매개 변수에](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) 대한 값을 제공 합니다. 예를 들면 다음과 같습니다.

   ![HTTP Webhook 트리거 매개 변수 입력](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

   HTTP Webhook에 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

1. 트리거가 발생할 때 실행되는 작업을 사용하여 논리 앱의 워크플로를 계속해서 작성합니다.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

   논리 앱을 저장 하면 구독 엔드포인트을 호출 하 고이 논리 앱을 트리거하기 위한 콜백 URL을 등록 합니다.

1. 이제 대상 서비스에서 콜백 URL에 `HTTP POST` 요청을 보낼 때마다 논리 앱이 실행 되 고 요청을 통해 전달 되는 모든 데이터가 포함 됩니다.

## <a name="add-an-http-webhook-action"></a>HTTP Webhook 작업 추가

이 기본 제공 작업은 지정 된 서비스를 사용 하 여 콜백 URL을 등록 하 고, 논리 앱의 워크플로를 일시 중지 하 고, 해당 서비스에서 해당 URL로 HTTP POST 요청을 보낼 때까지 대기 합니다. 이 이벤트가 발생 하면 작업에서 논리 앱 실행을 다시 시작 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. Logic Apps 디자이너에서 논리 앱을 엽니다.

   이 예제에서는 첫 번째 단계로 HTTP Webhook 트리거를 사용 합니다.

1. HTTP Webhook 작업을 추가 하려는 단계에서 **새 단계**를 선택 합니다.

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. 디자이너의 검색 상자에 "http webhook"를 필터로 입력 합니다. **작업** 목록에서 **HTTP Webhook** 작업을 선택 합니다.

   ![HTTP Webhook 작업 선택](./media/connectors-native-webhook/select-http-webhook-action.png)

   이 예에서는 단계에 보다 설명적인 이름을 포함 하도록 동작의 이름을 "HTTP Webhook 작업"으로 바꿉니다.

1. HTTP Webhook 작업 매개 변수에 대한 값을 제공 합니다 .이 매개 변수는 구독 및 구독 취소 호출에 사용 하려는 [Http webhook 트리거와](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) 유사 합니다. 예를 들면 다음과 같습니다.

   ![HTTP Webhook 작업 매개 변수 입력](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   런타임 중에 논리 앱은이 작업을 실행할 때 구독 엔드포인트을 호출 합니다. 그러면 논리 앱이 워크플로를 일시 중지 하 고 대상 서비스에서 콜백 URL로 `HTTP POST` 요청을 보낼 때까지 대기 합니다. 작업이 성공적으로 완료 되 면 엔드포인트에서 구독을 취소 하 고 논리 앱이 워크플로 실행을 다시 시작 합니다.

1. 사용할 수 있는 다른 매개 변수를 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 매개 변수를 선택 합니다.

   HTTP Webhook에 사용할 수 있는 인증 유형에 대한 자세한 내용은 [아웃 바운드 호출에 인증 추가](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)를 참조 하세요.

1. 완료 되 면 논리 앱을 저장 해야 합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

트리거와 유사 하 게 트리거와 동작 매개 변수에 대한 자세한 내용은 [HTTP Webhook 매개 변수](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)를 참조 하세요.

### <a name="output-details"></a>출력 세부 정보

다음은이 정보를 반환 하는 HTTP Webhook 트리거 또는 작업의 출력에 대한 자세한 정보입니다.

| 속성 이름 | 유형 | Description |
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
