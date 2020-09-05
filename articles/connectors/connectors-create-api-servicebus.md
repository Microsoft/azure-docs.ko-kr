---
title: Azure Service Bus와 메시지 교환
description: Azure Logic Apps에서 Azure Service Bus를 사용 하 여 메시지를 보내고 받는 자동화 된 작업 및 워크플로 만들기
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 07/31/2020
tags: connectors
ms.openlocfilehash: 13732c6d31f19dfb2548154feb8336a1dff3a529
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88853299"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>Azure Logic Apps 및 Azure Service Bus를 사용 하 여 클라우드의 메시지 교환

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 커넥터를 사용 하 여 조직의 응용 프로그램에서 판매 및 구매 주문서, 저널, 인벤토리 이동과 같은 데이터를 전송 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 커넥터는 메시지의 모니터링, 전송 및 관리뿐만 아니라 다음과 같이 큐, 세션, 토픽, 구독 등을 사용한 작업을 수행합니다.

* 큐, 토픽 및 토픽 구독에 메시지가 도착하거나(자동 완성) 수신되면(보기 잠금) 모니터링합니다.
* 메시지를 보냅니다.
* 토픽 구독을 만들고 삭제합니다.
* 큐 및 토픽 구독에서 메시지를 관리합니다. 예를 들어 가져오기, 지연된 가져오기, 완료, 지연, 중단 및 배달하지 못한 편지가 있습니다.
* 큐 및 토픽 구독에서 메시지 및 세션에 대한 잠금을 갱신합니다.
* 큐 및 토픽에서 세션을 닫습니다.

트리거를 사용하여 Service Bus에서 응답을 가져오고 논리 앱의 다른 작업에서 출력을 사용하도록 할 수 있습니다. 또한 다른 작업에서 Service Bus 작업의 출력을 사용하도록 할 수 있습니다. Service Bus 및 Logic Apps를 처음 접하는 경우 [Azure Service Bus 무엇 인가요?](../service-bus-messaging/service-bus-messaging-overview.md) 및 [Azure Logic Apps 란](../logic-apps/logic-apps-overview.md)무엇 인가요?

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Service Bus 네임스페이스 및 메시징 엔터티(예: 큐). 이러한 항목과 논리 앱은 동일한 Azure 구독을 사용 해야 합니다. 이러한 항목에 없는 경우 [Service Bus 네임스페이스 및 큐를 만드는](../service-bus-messaging/service-bus-create-namespace-portal.md) 방법을 알아봅니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Service Bus 네임 스페이스 및 메시징 엔터티를 사용 하는 논리 앱입니다. 논리 앱과 service bus는 동일한 Azure 구독을 사용 해야 합니다. Service Bus 트리거를 사용 하 여 워크플로를 시작 하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). 워크플로에서 Service Bus 작업을 사용 하려면 [되풀이 트리거와](../connectors/connectors-native-recurrence.md)같은 다른 트리거를 사용 하 여 논리 앱을 시작 합니다.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>사용 권한 확인

논리 앱에 Service Bus 네임스페이스에 액세스하기 위한 권한이 있는지 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 계정으로 로그인 합니다.

1. Service Bus *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임**에서 해당 네임 스페이스에 대 한 **관리** 권한이 있는지 확인 합니다.

   ![Service Bus 네임 스페이스에 대 한 사용 권한 관리](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. 논리 앱에서 연결 정보를 제공 하는 경우이 문자열이 필요 합니다.

   1. **공유 액세스 정책** 창에서 **RootManageSharedAccessKey**를 선택 합니다.

   1. 기본 연결 문자열 옆에 있는 복사 단추를 선택 합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 연결 문자열을 검색하여 `EntityPath`  매개 변수를 찾습니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="add-service-bus-trigger"></a>Service Bus 트리거 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 "azure service bus"를 필터로 입력 합니다. 트리거 목록에서 원하는 트리거를 선택 합니다.

   예를 들어 Service Bus 큐에 새 항목이 전송 될 때 논리 앱을 트리거하려면 **큐에 메시지가 수신 되는 경우 (자동 완성)** 트리거에서를 선택 합니다.

   ![Service Bus 트리거 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   모든 Service Bus 트리거는 *긴 폴링* 트리거입니다. 이 설명에서는 트리거가 발생 하면 트리거가 모든 메시지를 처리 한 다음, 큐 또는 토픽 구독에 더 많은 메시지가 표시 될 때까지 30 초 동안 기다립니다. 30초 동안 표시되는 메시지가 없으면 트리거 실행을 건너뜁니다. 그렇지 않으면 큐 또는 토픽 구독이 빈 상태가 될 때까지 트리거는 메시지를 계속 읽습니다. 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.

   **큐에 하나 이상의 메시지가 도착 하는 경우 (자동 완성)** 트리거와 같은 일부 트리거는 하나 이상의 메시지를 반환할 수 있습니다. 이러한 트리거는 발생 하면 1과 트리거의 **최대 메시지 수** 속성에 지정 된 메시지 수를 반환 합니다.

    > [!NOTE]
    > 자동 완성 트리거는 메시지를 자동으로 완성 하지만 완료는 다음 트리거 실행 시에만 수행 됩니다. 이 동작은 논리 앱의 디자인에 영향을 줄 수 있습니다. 예를 들어 1 분 마다 메시지를 확인 하도록 자동 완성 트리거를 설정 하지만 잠금 기간이 Service Bus 쪽에서 30 초로 설정 된 경우 메시지를 완료할 때 발생 하는 "잠금 만료" 오류가 발생 합니다. 잠금 기간을 폴링 간격 보다 긴 값으로 설정 해야 합니다.

1. 트리거가 Service Bus 네임 스페이스에 처음으로 연결 하는 경우 논리 앱 디자이너에서 연결 정보를 묻는 메시지가 표시 되 면 다음 단계를 수행 합니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![연결 이름을 제공 하 고 Service Bus 네임 스페이스를 선택 하는 것을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      연결 문자열을 수동으로 입력 하려면 **연결 정보를 수동으로 입력**을 선택 합니다. 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. Service Bus 정책을 선택 하 고 **만들기**를 선택 합니다.

      ![Service Bus 정책 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 큐 또는 항목과 같이 원하는 메시징 엔터티를 선택 합니다. 이 예에서는 Service Bus 큐를 선택 합니다.
   
      ![Service Bus 큐 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 선택한 트리거에 필요한 정보를 제공 합니다. 작업에 사용할 수 있는 다른 속성을 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 속성을 선택 합니다.

   이 예제의 트리거에서는 큐를 확인 하는 폴링 간격 및 빈도를 선택 합니다.

   ![Service Bus 트리거의 폴링 간격 설정을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   사용 가능한 트리거 및 속성에 대 한 자세한 내용은 커넥터의 [참조 페이지](/connectors/servicebus/)를 참조 하세요.

1. 원하는 작업을 추가 하 여 논리 앱을 계속 빌드합니다.

   예를 들어 새 메시지가 도착하면 이메일을 보내는 작업을 추가할 수 있습니다. 트리거가 큐를 확인하고 새 메시지를 발견하는 경우 논리 앱은 찾은 메시지에 대해 사용자가 선택한 작업을 실행합니다.

## <a name="add-service-bus-action"></a>Service Bus 작업 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 작업을 추가 하려는 단계 아래에서 **새 단계**를 선택 합니다.

   또는 단계 간에 작업을 추가 하려면 해당 단계 사이의 화살표 위로 포인터를 이동 합니다. 표시 되는 더하기 기호 ()를 선택 **+** 하 고 **작업 추가**를 선택 합니다.

1. **작업 선택**아래의 검색 상자에 "azure service bus"를 필터로 입력 합니다. 작업 목록에서 원하는 작업을 선택 합니다. 

   이 예에서는 **메시지 보내기** 작업을 선택 합니다.

   ![Service Bus 동작 선택을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 작업에서 Service Bus 네임 스페이스에 처음으로 연결 하는 경우 논리 앱 디자이너에서 연결 정보를 묻는 메시지가 표시 되 면 다음 단계를 수행 합니다.

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![연결 이름을 제공 하 고 Service Bus 네임 스페이스를 선택 하는 것을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      연결 문자열을 수동으로 입력 하려면 **연결 정보를 수동으로 입력**을 선택 합니다. 연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. Service Bus 정책을 선택 하 고 **만들기**를 선택 합니다.

      ![Service Bus 정책을 선택 하 고 만들기 단추를 선택 하는 스크린샷](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 큐 또는 항목과 같이 원하는 메시징 엔터티를 선택 합니다. 이 예에서는 Service Bus 큐를 선택 합니다.

      ![Service Bus 큐를 선택 하는 것을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 선택한 작업에 필요한 정보를 입력합니다. 작업에 사용할 수 있는 다른 속성을 추가 하려면 **새 매개 변수 추가** 목록을 열고 원하는 속성을 선택 합니다.

   예를 들어 **콘텐츠** 및 **콘텐츠 형식** 속성을 선택 하 여 작업에 추가할 수 있습니다. 그런 다음 보내려는 메시지의 콘텐츠를 지정 합니다.

   ![메시지 내용 유형 및 세부 정보 제공을 보여 주는 스크린샷](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   사용 가능한 작업 및 해당 속성에 대 한 자세한 내용은 커넥터의 [참조 페이지](/connectors/servicebus/)를 참조 하세요.

1. 원하는 다른 작업을 추가 하 여 논리 앱을 계속 빌드합니다.

   예를 들어 전자 메일을 보내는 작업을 추가 하 여 메시지가 전송 되었는지 확인할 수 있습니다.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

<a name="sequential-convoy"></a>

## <a name="send-correlated-messages-in-order"></a>상관 관계가 지정 된 메시지를 순서 대로 보냅니다.

관련 메시지를 특정 순서로 보내야 하는 경우에는 [Azure Service Bus 커넥터](../connectors/connectors-create-api-servicebus.md)를 사용 하 여 [ *순차 호위 (convoy* ) 패턴](/azure/architecture/patterns/sequential-convoy) 을 사용할 수 있습니다. 상관 관계가 지정 된 메시지에는 Service Bus [세션](../service-bus-messaging/message-sessions.md) 의 ID와 같은 해당 메시지 간의 관계를 정의 하는 속성이 있습니다.

논리 앱을 만들 때 순차 호위 (convoy) 패턴을 구현 하는 **service bus 세션 템플릿을 사용 하 여 상관 관계가 지정 된 순서 대로 배달을** 선택할 수 있습니다. 자세한 내용은 [순서 대로 관련 메시지 보내기](../logic-apps/send-related-messages-sequential-convoy.md)를 참조 하세요.

<a name="connector-reference"></a>

## <a name="connector-reference"></a>커넥터 참조

Service Bus 커넥터는 Service Bus에서 커넥터 캐시로 최대 1500 개의 고유 세션을 저장할 수 있습니다. 세션 수가이 한도를 초과 하면 이전 세션이 캐시에서 제거 됩니다. 자세한 내용은 [메시지 세션](../service-bus-messaging/message-sessions.md)을 참조 하세요.

커넥터의 Swagger 설명에서 설명 하는 트리거, 작업 및 제한에 대 한 기타 기술 세부 정보는 [커넥터 참조 페이지를 참조](/connectors/servicebus/)하세요. Azure Service Bus 메시징에 대 한 자세한 내용은 [Azure Service Bus 정의](../service-bus-messaging/service-bus-messaging-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
