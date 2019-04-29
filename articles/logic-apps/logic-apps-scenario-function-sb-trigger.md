---
title: 시나리오 - Azure Functions 및 Azure Service Bus를 사용하여 논리 앱 트리거 | Microsoft Docs
description: Azure Functions 및 Azure Service Bus를 사용하여 논리 앱을 트리거하는 함수 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996319"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>시나리오: Azure Functions 및 Azure Service Bus를 사용 하 여 논리 앱 트리거

Azure Functions로 장기 실행 수신기 또는 작업을 배포하는 데 필요한 논리 앱용 트리거를 만들 수 있습니다. 예를 들어, 큐에서 수신 대기하고 있다가 푸시 트리거로 즉시 논리 앱을 실행하는 함수를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식 

* Azure 함수를 만들려면 먼저 [함수 앱을 만들어야 합니다](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>논리 앱 만들기

이 예에는 트리거할 필요가 있는 각 논리 앱에 대해 실행되는 함수가 있습니다. 먼저, HTTP 요청 트리거가 있는 논리 앱을 만듭니다. 이 함수는 큐 메시지를 수신할 때마다 엔드포인트를 호출합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 빈 논리 앱을 만듭니다. 

   논리 앱을 처음 접하는 경우 검토 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에 "http 요청"을 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **HTTP 요청을 수신 되는 경우**

   ![트리거 선택](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. **요청** 트리거의 경우 필요에 따라 큐 메시지에 사용할 JSON 스키마를 입력할 수 있습니다. JSON 스키마는 논리 앱 디자이너가 입력 데이터 구조체를 이해하고 워크플로 전체에서 선택이 용이하도록 출력을 만드는 데 도움이 됩니다. 

   스키마를 지정하려면 다음과 같이 **요청 본문 JSON 스키마** 상자에 스키마를 입력합니다. 

   ![JSON 스키마 지정](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   스키마는 없지만 JSON 형식의 샘플 페이로드가 있는 경우 해당 페이로드로 스키마를 생성할 수 있습니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   1. **샘플 JSON 페이로드를 입력하거나 붙여넣기**에서 샘플 페이로드를 입력하고 **완료**를 선택합니다.
      
      ![샘플 페이로드 입력](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   이 샘플 페이로드는 트리거에 표시되는 이 스키마를 생성합니다.

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. 큐 메시지를 받은 후 수행할 다른 작업을 추가합니다. 

   예를 들어 Office 365 Outlook 커넥터를 사용하여 이메일을 보낼 수 있습니다.

1. 논리 앱을 저장합니다. 그러면 이 논리 앱의 트리거에 대한 콜백 URL이 생성됩니다. 이 URL은 **HTTP POST URL** 속성에 표시됩니다.

   ![트리거에 대해 생성된 콜백 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Azure 함수 만들기

다음으로 트리거로 작동하고 큐에 수신 대기하는 함수를 만들겠습니다. 

1. 아직 함수 앱을 열지 않았으면 Azure Portal에서 함수 앱을 열고 확장합니다. 

1. 함수 앱 이름 아래에서 **Functions**를 확장합니다. **Functions** 창에서 **새 함수**를 선택합니다. 이 서식 파일을 선택 합니다. **Service Bus 큐 트리거-C#**
   
   ![Azure Functions 포털 선택](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. 트리거 이름을 입력한 다음, Azure Service Bus SDK `OnMessageReceive()` 수신기를 사용하도록 Service Bus 큐에 대한 연결을 구성합니다.

1. 다음과 같이 큐 메시지를 트리거로 사용하여 앞에서 만든 논리 앱 엔드포인트를 호출하는 기본 함수를 작성합니다. 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   이 예제에서는 `application/json` 메시지 콘텐츠 형식을 사용하지만, 필요에 따라 형식을 변경할 수 있습니다.

1. 함수를 테스트하기 위해 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer) 같은 도구를 사용하여 큐 메시지를 추가합니다. 

   함수가 메시지를 받는 즉시 논리 앱이 트리거됩니다.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

