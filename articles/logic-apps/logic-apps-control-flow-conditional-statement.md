---
title: 워크플로에 조건문 추가 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 흐름의 작업을 제어하는 조건을 만드는 방법입니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 9ee484971e217b0ca4dd7ad855e9e6dc3313e5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684807"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 작업을 제어하는 조건문 만들기

지정된 조건을 통과한 후에만 논리 앱에서 특정 동작을 실행하려면 *조건문*을 추가합니다. 이 제어 구조는 워크플로의 데이터를 특정 값 또는 필드와 비교합니다. 그런 다음, 데이터가 조건을 충족하는지 여부에 따라 실행되는 다양한 작업을 지정할 수 있습니다. 서로의 내부에 조건을 중첩할 수 있습니다.

예를 들어 웹 사이트의 RSS 피드에 새 항목이 표시될 때 너무 많은 이메일을 보내는 논리 앱이 있다고 가정합니다. 새 항목에 특정 문자열이 포함된 경우에만 이메일을 보내는 조건문을 추가할 수 있습니다. 

> [!TIP]
> 다른 특정 값에 따라 다른 단계를 실행하려면 [*switch 문*](../logic-apps/logic-apps-control-flow-switch-statement.md)을 대신 사용합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* 이 문서의 예를 수행하려면 Outlook.com 또는 Office 365 Outlook 계정으로 [이 샘플 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-condition"></a>조건 추가

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 원하는 위치에 조건을 추가합니다. 

   단계 사이에 조건을 추가하려면 조건을 추가하려는 화살표 위로 포인터를 이동합니다. 표시되는 **더하기 기호**(**+**)를 선택한 다음 **작업 추가**를 선택합니다. 예를 들면 다음과 같습니다.

   ![단계 사이에 작업 추가](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   워크플로 끝 부분에 조건을 추가하려면 논리 앱의 맨 아래에서 **새 단계** > **작업 추가**를 선택합니다.

1. 검색 상자에서 필터로 “조건”을 입력합니다. 현재 선택한 작업: **조건-컨트롤**

   ![조건 추가](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. **조건** 상자에서 조건을 작성합니다. 

   1. 왼쪽 상자에서 비교하려는 데이터 또는 필드를 지정합니다.

      왼쪽 상자 내부를 클릭하면 논리 앱에서 이전 단계의 출력을 선택할 수 있도록 동적 콘텐츠 목록이 나타납니다. 
      예를 들어 RSS 피드 요약을 선택합니다.

      ![조건 빌드](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. 가운데 상자에서 수행할 작업을 선택합니다. 
   이 예제에서는 "**포함**"을 선택합니다. 

   1. 오른쪽 상자에서 또는 필드를 기준으로 값 지정합니다. 
   예를 들어이 문자열을 지정 합니다. **Microsoft**

   완성된 조건은 다음과 같습니다.

   ![완성된 조건](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   조건에 다른 행을 추가하려면 **추가** > **행 추가**를 선택합니다. 
   하위 조건이 있는 그룹을 추가하려면 **추가** > **그룹 추가**를 선택합니다. 
   기존 행을 그룹화하려면 해당 행의 확인란을 선택하고, 임의 행의 줄임표(...) 단추를 선택한 다음, **그룹 만들기**를 선택합니다.

1. **If true** 및 **If false** 아래에서 조건이 충족되는지 여부에 따라 수행할 단계를 추가합니다. 예를 들면 다음과 같습니다.

   !["If true" 및 "If false" 경로를 포함한 조건](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > 기존 동작을 **If true** 및 **If false** 경로로 끌어올 수 있습니다.

1. 논리 앱을 저장합니다.

이제 RSS 피드의 새 항목이 조건을 충족하는 경우에만 이 논리 앱에서 메일을 보냅니다.

## <a name="json-definition"></a>JSON 정의

다음은 조건문 뒤의 상위 수준 코드 정의입니다.

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [병렬 단계 실행 및 병합(분기)](../logic-apps/logic-apps-control-flow-branches.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
