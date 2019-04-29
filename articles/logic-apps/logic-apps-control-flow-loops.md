---
title: 작업을 반복하거나 배열을 처리하는 루프 추가 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 워크플로 작업을 반복하거나 배열을 처리하는 루프를 만드는 방법입니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: 339d4270dc1803879607663e9e2db4a86591ec76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684093"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 작업을 반복하거나 배열을 처리하는 루프를 만듭니다.

논리 앱에서 배열을 처리하려면 ["Foreach" 루프](#foreach-loop)를 만들 수 있습니다. 이 루프는 배열의 각 항목에 대해 하나 이상의 작업을 반복합니다. "Foreach" 루프가 처리할 수 있는 배열 항목 수의 제한에 대해서는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

조건이 충족되거나 상태가 변경될 때마다 작업을 반복하려면 ["Until" 루프](#until-loop)를 만들 수 있습니다. 먼저 논리 앱 루프 내에서 모든 작업을 실행 하 고 조건 또는 상태를 확인 합니다. 조건이 충족되면 루프가 중지됩니다. 그렇지 않으면 루프가 반복됩니다. 논리 앱 실행의 "Until" 루프 수 제한에 대해서는 [제한 및 구성](../logic-apps/logic-apps-limits-and-config.md)을 참조하세요. 

> [!TIP]
> 배열을 받는 트리거가 있고 각 배열 항목에 대한 워크플로를 실행하려는 경우, [**SplitOn** 트리거 속성](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)을 사용하여 해당 배열을 *분리 처리(debatch)* 할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" 루프

"Foreach" 루프는 각 배열 항목에 대해 하나 이상의 작업을 반복하고 배열에만 작동합니다. "Foreach" 루프의 반복이 병렬로 실행됩니다. 그렇지만 [순차적 "Foreach" 루프](#sequential-foreach-loop)를 설정하여 한 번에 하나씩 반복을 실행할 수 있습니다. 

"Foreach" 루프를 사용하는 경우 다음과 같은 몇 가지 사항을 고려해야 합니다.

* 중첩된 루프에서는 반복이 항상 병렬이 아닌 순차적으로 실행됩니다. 중첩된 루프의 항목에 대해 작업을 병렬로 실행하려면 [자식 논리 앱을 만들고 호출](../logic-apps/logic-apps-http-endpoint.md)합니다.

* 각 루프 반복 동안 변수 작업에서 예측 가능한 결과를 얻으려면 해당 루프를 순차적으로 실행합니다. 예를 들어, 동시에 실행 중인 루프가 종료될 때 변수에 대해 증가, 감소 및 추가 작업을 수행하면 예측 가능한 결과가 반환됩니다. 그러나 동시 실행 루프에서 각 반복이 수행되는 동안 이러한 작업을 수행하면 예기치 않은 결과가 반환될 수 있습니다. 

* "Foreach" 루프의 작업은 [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
식을 사용하여 배열의 각 항목을 참조 및 처리합니다. 배열에 없는 데이터를 지정하면 논리 앱 워크플로가 실패합니다. 

이 예제의 논리 앱은 웹 사이트 RSS 피드에 대한 일별 요약을 보냅니다. 앱은 각각의 새 항목에 대한 이메일을 보내는 "Foreach" 루프를 사용합니다.

1. Outlook.com 또는 Office 365 Outlook 계정으로 [이 샘플 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. RSS 트리거와 이메일 보내기 작업 사이에 "Foreach" 루프를 추가합니다. 

   1. 단계 사이에서 루프를 추가하려면 해당 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 **더하기 기호**(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

      ![“작업 추가” 선택](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. 검색 상자에서 **모두**를 선택합니다. 검색 상자에서 필터로 “for each”를 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **For each - 컨트롤**

      !["For each" 루프 추가](media/logic-apps-control-flow-loops/select-for-each.png)

3. 이제 루프를 작성합니다. **동적 콘텐츠 추가** 목록이 표시되면 **이전 단계의 출력 선택** 아래에서 RSS 트리거에서 출력된 **피드 링크** 배열을 선택합니다. 

   ![동적 콘텐츠 목록에서 선택](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > 이전 단계의 *배열 출력만* 선택할 수 있습니다.

   선택한 배열이 다음과 같이 표시됩니다.

   ![배열 선택](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. 각 배열 항목에 대한 작업을 실행하려면 **이메일 보내기** 작업을 이 루프로 끕니다. 

   논리 앱은 다음 예와 같을 수 있습니다.

   !["Foreach" 루프에 단계 추가](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. 논리 앱을 저장합니다. 논리 앱을 수동으로 테스트하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" 루프 정의(JSON)

논리 앱에 대한 코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 `Foreach` 루프를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
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
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" 루프: 순차

기본적으로 "Foreach" 루프의 주기는 병렬로 실행됩니다. 각 주기를 순차적으로 실행하려면 루프의 **순차** 옵션을 설정합니다. "Foreach" 루프는 예측 가능한 결과를 예상할 경우 루프 내에 중첩된 루프 또는 변수가 있으면 순차적으로 실행되어야 합니다. 

1. 루프의 오른쪽 위 모서리에서 **줄임표**(**...**) > **설정**을 선택합니다.

   !["Foreach" 루프에서 "..." > "설정"을 차례로 선택합니다.](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. **동시성 제어** 아래에서 **동시성 제어** 설정을 **켜기**로 지정합니다. **병렬도** 슬라이더를 **1**로 이동한 후 **완료**를 선택합니다.

   ![동시성 제어 켜기](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

논리 앱의 JSON 정의를 사용하는 경우 `operationOptions` 매개 변수를 추가하여 `Sequential` 옵션을 사용할 수 있습니다. 예:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" 루프
  
를 실행 하 고 조건을 충족 가져옵니다 또는 상태 변경 될 때까지 작업 반복 "Until" 루프에 이러한 작업을 배치 합니다. 먼저 논리 앱 루프 내에서 모든 작업을 실행 하 고 조건 또는 상태를 확인 합니다. 조건이 충족되면 루프가 중지됩니다. 그렇지 않으면 루프가 반복됩니다.

"Until" 루프를 사용할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

* 원하는 응답을 받을 때까지 엔드포인트를 호출합니다.

* 데이터베이스에 레코드를 만듭니다. 해당 레코드의 특정 필드가 승인될 때까지 기다립니다. 처리를 계속합니다. 

이 예제 논리 앱은 매일 오전 8시에, 변수의 값이 10이 될 때까지 변수를 증가시킵니다. 그런 다음, 논리 앱에서 현재 값을 확인하는 이메일을 보냅니다. 

> [!NOTE]
> 이러한 단계는 Office 365 Outlook을 사용하지만 Logic Apps에서 지원하는 이메일 공급자를 사용할 수 있습니다. 
> [여기에 있는 커넥터 목록을 확인](https://docs.microsoft.com/connectors/)합니다. 다른 이메일 계정을 사용하는 경우 일반적인 단계는 동일하지만 UI가 약간 다르게 표시될 수 있습니다. 

1. 빈 논리 앱을 만듭니다. Logic Apps 디자이너의 검색 상자에서 **모두**를 선택합니다. "되풀이"를 검색합니다. 
   트리거 목록에서 다음 트리거를 선택합니다. **되풀이 - 일정**

   !["되풀이 - 일정" 트리거 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. 간격, 빈도 및 하루 중의 시간을 설정하여 트리거가 실행되는 시기를 지정합니다. 시간을 설정하려면 **고급 옵션 표시**를 선택합니다.

   ![되풀이 일정 설정](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | 자산 | 값 |
   | -------- | ----- |
   | **간격** | 1 | 
   | **Frequency(빈도)** | 일 |
   | **시간 선택** | 8 |
   ||| 

1. 트리거 아래에서 **새 단계**를 선택합니다. 
   "변수"를 검색하고 다음 작업을 선택합니다. **변수 초기화 - 변수**

   !["변수 초기화 - 변수" 작업 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. 다음 값을 사용하여 변수를 설정합니다.

   ![변수 속성 설정](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | 자산 | 값 | 설명 |
   | -------- | ----- | ----------- |
   | **Name** | 제한 | 변수의 이름 | 
   | **형식** | 정수  | 변수의 데이터 형식 | 
   | **값** | 0 | 변수의 시작 값 | 
   |||| 

1. **변수 초기화** 작업 아래에서 **새 단계**를 차례로 선택합니다. 

1. 검색 상자에서 **모두**를 선택합니다. "Until"을 검색하고 다음 작업을 선택합니다. **Until - 컨트롤**

   !["Until" 루프 추가](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. **Limit** 변수 및 **같음** 연산자를 선택하여 루프의 종료 조건을 작성합니다. 
   비교 값으로 **10**을 입력합니다.

   ![루프를 중지하기 위한 종료 조건 작성](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. 루프 내부에서 **작업 추가**를 선택합니다. 

1. 검색 상자에서 **모두**를 선택합니다. "변수"를 검색하고 다음 작업을 선택합니다. **변수 증가 - 변수**

   ![변수를 증가시키기 위한 작업 추가](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. **이름**에 대해 **Limit** 변수를 선택합니다. **값**에 대해 "1"을 입력합니다. 

     !["Limit"를 1씩 증가](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. 루프 외부 및 아래에서 **새 단계**를 선택합니다. 

1. 검색 상자에서 **모두**를 선택합니다. 
     이메일을 보내는 작업을 찾아서 추가합니다. 예를 들면 다음과 같습니다. 

     ![이메일을 보내는 작업 추가](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. 메시지가 표시되면 이메일 계정에 로그인합니다.

1. 이메일 작업의 속성을 설정합니다. **Limit** 변수를 제목에 추가합니다. 이렇게 하면 변수의 현재 값이 지정된 조건을 충족하는지 확인할 수 있습니다. 예를 들어 다음과 같습니다.

      ![이메일 속성 설정](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | 자산 | 값 | 설명 |
      | -------- | ----- | ----------- | 
      | **To** | *\<email-address\@domain>* | 받는 사람의 이메일 주소입니다. 테스트를 위해 자신의 이메일 주소를 사용합니다. | 
      | **제목** | "제한"에 대한 현재 값은 **Limit**입니다. | 이메일 제목을 지정합니다. 이 예에서는 **Limit** 변수를 포함해야 합니다. | 
      | **본문** | <*email-content*> | 보내려는 이메일 메시지의 내용을 지정합니다. 이 예에서는 텍스트를 원하는 대로 입력합니다. | 
      |||| 

1. 논리 앱을 저장합니다. 논리 앱을 수동으로 테스트하려면 디자이너 도구 모음에서 **실행**을 선택합니다.

      논리 앱이 실행되기 시작하면 지정한 내용이 포함된 이메일을 받습니다.

      ![받은 이메일](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>무한 루프 방지

"Until" 루프에는 다음 조건 중 하나가 발생하는 경우 실행을 중지하는 기본 제한이 있습니다.

| 자산 | 기본값 | 설명 | 
| -------- | ------------- | ----------- | 
| **개수** | 60 | 루프가 종료되기 전에 실행되는 최대 루프 수입니다. 기본값은 60회 주기입니다. | 
| **시간 제한** | PT1H | 루프가 종료되기 전에 루프를 실행하는 가장 많은 시간입니다. 기본값은 1시간이며 ISO 8601 형식으로 지정됩니다. <p>시간 제한 값은 각 루프 주기에 대해 평가됩니다. 루프의 작업이 시간 제한보다 오래 걸리면 현재 주기가 중지되지 않습니다. 그러나 제한 조건이 충족되지 않으면 다음 주기가 시작되지 않습니다. | 
|||| 

이러한 기본 제한을 변경하려면 루프 작업 셰이프에서 **고급 옵션 표시**를 선택합니다.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" 정의(JSON)

논리 앱에 대한 코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 `Until` 루프를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

이 제 "Until" 루프는 리소스를 만드는 HTTP 엔드포인트를 호출합니다. HTTP 응답 본문이 `Completed` 상태로 반환되면 루프가 중지됩니다. 또한 무한 루프를 방지하기 위해 다음 조건 중 하나라도 발생하면 루프가 중지됩니다.

* 루프가 `count` 특성에 지정한 대로 10회 실행되었습니다. 기본값은 60회입니다. 

* 루프가 ISO 8601 형식의 `timeout` 특성에 지정한 대로 2시간 동안 실행되었습니다. 기본값은 1시간입니다.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
