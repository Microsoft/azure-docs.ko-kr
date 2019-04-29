---
title: Azure Service Bus를 사용하여 메시지 전송 및 수신 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 Azure Service Bus를 사용하여 엔터프라이즈 클라우드 메시징 설정
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105583"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>Azure Service Bus 및 Azure Logic Apps를 사용하여 클라우드에서 메시지 교환

Azure Logic Apps 및 Azure Service Bus 커넥터를 사용하면 영업 및 구매 주문, 저널 및 조직용 애플리케이션 간 재고 이동과 같은 데이터를 전송하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 커넥터는 메시지의 모니터링, 전송 및 관리뿐만 아니라 다음과 같이 큐, 세션, 토픽, 구독 등을 사용한 작업을 수행합니다.

* 큐, 토픽 및 토픽 구독에 메시지가 도착하거나(자동 완성) 수신되면(보기 잠금) 모니터링합니다. 
* 메시지를 보냅니다.
* 토픽 구독을 만들고 삭제합니다.
* 큐 및 토픽 구독에서 메시지를 관리합니다. 예를 들어 가져오기, 지연된 가져오기, 완료, 지연, 중단 및 배달하지 못한 편지가 있습니다.
* 큐 및 토픽 구독에서 메시지 및 세션에 대한 잠금을 갱신합니다.
* 큐 및 토픽에서 세션을 닫습니다.

트리거를 사용하여 Service Bus에서 응답을 가져오고 논리 앱의 다른 작업에서 출력을 사용하도록 할 수 있습니다. 또한 다른 작업에서 Service Bus 작업의 출력을 사용하도록 할 수 있습니다. Service Bus 및 Logic Apps가 처음인 경우 [Azure Service Bus란?](../service-bus-messaging/service-bus-messaging-overview.md) 및 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* Service Bus 네임스페이스 및 메시징 엔터티(예: 큐). 이러한 항목에 없는 경우 [Service Bus 네임스페이스 및 큐를 만드는](../service-bus-messaging/service-bus-create-namespace-portal.md) 방법을 알아봅니다. 

  이러한 항목은 이러한 항목을 사용하는 논리 앱과 동일한 Azure 구독에 있어야 합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Service Bus를 사용하려는 논리 앱입니다. 논리 앱은 Service Bus와 동일한 Azure 구독에 있어야 합니다. Service Bus 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). Service Bus 동작을 사용하려면 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>권한 확인

논리 앱에 Service Bus 네임스페이스에 액세스하기 위한 권한이 있는지 확인합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 

2. Service Bus *네임스페이스*로 이동합니다. 네임스페이스 페이지의 **설정**에서 **공유 액세스 정책**을 선택합니다. **클레임**에서 해당 네임스페이스에 대한 **관리** 권한이 있는지 확인합니다.

   ![Service Bus 네임스페이스에 대한 관리 권한](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. Service Bus 네임스페이스에 대한 연결 문자열을 가져옵니다. 논리 앱에서 연결 정보를 입력하는 경우 이 문자열이 필요합니다.

   1. **RootManageSharedAccessKey**를 선택합니다. 
   
   1. 기본 연결 문자열 옆에 있는 복사 단추를 선택합니다. 나중에 사용할 연결 문자열을 저장합니다.

      ![Service Bus 네임스페이스 연결 문자열 복사](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 연결 문자열이 Service Bus 네임스페이스 또는 메시징 엔터티와 연결되어 있는지 확인하려면 연결 문자열을 검색하여 `EntityPath`  매개 변수를 찾습니다. 이 매개 변수를 찾은 경우 연결 문자열은 특정 엔터티에 대한 것이고 논리 앱에 사용할 올바른 문자열이 아닙니다.

## <a name="add-trigger-or-action"></a>트리거 또는 작업 추가

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. *트리거*를 빈 논리 앱에 추가하려면 검색 상자에 “Azure Service Bus”를 필터로 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다. 

   예를 들어 새 항목이 Service Bus 큐로 보내질 때 논리 앱 트리거이 트리거를 선택 합니다. **(자동 완성) 큐에 메시지를 받을 때**

   ![Service Bus 트리거 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > **큐에 하나 이상의 메시지가 도착할 때(자동 완성)** 트리거와 같은 일부 트리거는 1개 이상의 메시지를 반환합니다. 이러한 트리거가 발생되면 1과 트리거의 **최대 메시지 수** 속성으로 지정된 메시지 수 사이의 값을 반환합니다.

   *모든 Service Bus 트리거는 긴 폴링 트리거입니다.* 즉, 트리거가 발생될 때 이 트리거는 모든 메시지를 처리한 다음, 더 많은 메시지가 큐 또는 토픽 구독에 나타날 때까지 30초 동안 기다립니다. 
   30초 동안 표시되는 메시지가 없으면 트리거 실행을 건너뜁니다. 
   그렇지 않으면 큐 또는 토픽 구독이 빈 상태가 될 때까지 트리거는 메시지를 계속 읽습니다. 다음 트리거 폴링은 트리거의 속성에 지정된 되풀이 간격을 기준으로 합니다.

1. *작업*을 기존 논리 앱에 추가하려면 다음 단계를 수행합니다. 

   1. 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

      단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
      표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

   1. 검색 상자에서 “Azure Service Bus”를 필터로 입력합니다. 
   작업 목록에서 원하는 작업을 선택합니다. 
 
      예를 들어,이 작업을 선택 합니다. **메시지 보내기**

      ![Service Bus 동작 선택](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 논리 앱을 Service Bus 네임스페이스에 처음으로 연결하는 경우 Logic App 디자이너에서 이제 사용자의 연결 정보를 묻는 메시지를 표시합니다. 

   1. 연결에 사용할 이름을 입력하고 Service Bus 네임스페이스를 선택합니다.

      ![Service Bus 연결 만들기, 1부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      대신 연결 문자열을 수동으로 입력하려면 **연결 정보를 수동으로 입력**을 선택합니다. 
      연결 문자열이 없는 경우 [연결 문자열을 찾는 방법](#permissions-connection-string)에 대해 알아봅니다.

   1. 이제 Service Bus 정책을 선택한 다음, **만들기**를 선택합니다.

      ![Service Bus 연결 만들기, 2부](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. 이 예제의 경우 큐 또는 토픽과 같이 원하는 메시징 엔터티를 선택합니다. 이 예제에서는 Service Bus 큐를 선택합니다. 
   
   ![Service Bus 큐 선택](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. 트리거 또는 작업에 필요한 세부 정보를 입력합니다. 이 예제의 경우 트리거 또는 작업에 대해 관련 단계를 수행합니다. 

   * **샘플 트리거에 대 한**: 폴링 간격 및 큐 점검을 위한 빈도 설정 합니다.

     ![폴링 간격 설정](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     완료되면 원하는 작업을 추가하여 논리 앱의 워크플로를 계속 빌드합니다. 예를 들어 새 메시지가 도착하면 이메일을 보내는 작업을 추가할 수 있습니다.
     트리거가 큐를 확인하고 새 메시지를 발견하는 경우 논리 앱은 찾은 메시지에 대해 사용자가 선택한 작업을 실행합니다.

   * **샘플 작업에 대 한**: 메시지 콘텐츠 및 기타 세부 정보를 입력 합니다. 

     ![메시지 콘텐츠 및 세부 정보를 입력합니다.](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     완료되면 원하는 다른 작업을 추가하여 논리 앱의 워크플로를 계속 빌드합니다. 예를 들어 메시지가 전송되었음을 확인하는 이메일을 보내는 작업을 추가할 수 있습니다.

1. 논리 앱을 저장합니다. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/servicebus/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.