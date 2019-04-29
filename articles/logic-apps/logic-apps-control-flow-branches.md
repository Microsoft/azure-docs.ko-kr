---
title: 병렬 분기 만들기 또는 조인 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 워크플로에 대한 병렬 분기를 만들거나 조인하는 방법입니다.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 2e1c155a371fa96e4f772f632a9585948b012e54
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60685161"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 작업에 대한 병렬 분기 만들기 또는 조인

기본적으로 논리 앱 워크플로의 작업은 순차적으로 실행됩니다. 독립적인 작업을 동시에 수행하려면 [병렬 분기](#parallel-branches)를 만든 다음, 나중에 흐름에서 [이러한 분기를 조인](#join-branches)할 수 있습니다. 

> [!TIP] 
> 배열을 받는 트리거가 있고 각 배열 항목에 대한 워크플로를 실행하려는 경우, [**SplitOn** 트리거 속성](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)을 사용하여 해당 배열을 *분리 처리(debatch)* 할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>병렬 분기 추가

독립적인 단계를 동시에 실행하려면 기존 단계 옆에 병렬 분기를 추가할 수 있습니다. 

![병렬로 단계 실행](media/logic-apps-control-flow-branches/parallel.png)

논리 앱은 워크플로를 계속하기 전에 모든 분기가 완료될 때까지 기다립니다. `runAfter` 속성 값이 완료된 부모 단계의 상태와 일치할 때만 병렬 분기가 실행됩니다. 예를 들어 `branchAction1`과 `branchAction2`는 모두 `parentAction`이 `Succeeded` 상태로 완료될 때만 실행되도록 설정됩니다.

> [!NOTE]
> 시작하기 전에 논리 앱에 병렬 분기를 추가할 수 있는 단계가 이미 있어야 합니다.

1. <a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 논리 앱 디자이너에서 논리 앱을 엽니다.

1. 병렬 분기를 추가하려는 단계 위의 화살표 위로 포인터를 이동합니다. 표시되는 **더하기** 기호(**+**)를 선택한 다음, **병렬 분기 추가**를 선택합니다. 

   ![병렬 분기 추가](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. 검색 상자에서 원하는 작업을 찾아서 선택합니다.

   ![원하는 작업을 찾아서 선택](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   이제 선택한 작업이 다음과 같이 병렬 분기에 표시됩니다.

   ![원하는 작업을 찾아서 선택](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. 각 병렬 분기에 원하는 단계를 추가합니다. 분기에 또 다른 작업을 추가하려면 순차적 작업을 추가하려는 작업 아래로 포인터를 이동합니다. 표시되는 **더하기** 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

   ![병렬 분기에 순차적 작업 추가](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. 검색 상자에서 원하는 작업을 찾아서 선택합니다.

   ![순차적 작업을 찾아서 선택](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   이제 선택한 작업이 다음과 같이 현재 분기 내에 표시됩니다.

   ![순차적 작업을 찾아서 선택](media/logic-apps-control-flow-branches/added-sequential-action.png)

분기를 다시 병합하려면 [병렬 분기를 조인합니다](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>병렬 분기 정의(JSON)

코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 병렬 구조를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>병렬 분기 조인

병렬 분기를 모두 병합하려면 모든 분기의 아래쪽에 단계를 추가하기만 하면 됩니다. 이 단계는 모든 병렬 분기의 실행이 완료된 후에 실행됩니다.

![병렬 분기 조인](media/logic-apps-control-flow-branches/join.png)

1. [Azure Portal](https://portal.azure.com)의 Logic App Designer에서 논리 앱을 찾아서 엽니다. 

1. 조인하려는 병렬 분기에서 **새 단계**를 선택합니다. 

   ![조인할 단계 추가](media/logic-apps-control-flow-branches/add-join-step.png)

1. 검색 상자에서 분기를 조인하는 단계로 원하는 작업을 찾아서 선택합니다.

   ![병렬 분기를 조인하는 작업을 찾아서 선택](media/logic-apps-control-flow-branches/join-steps.png)

   이제 병렬 분기가 병합되었습니다.

   ![조인된 분기](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>조인 정의(JSON)

코드 뷰에서 작업하는 경우 논리 앱의 JSON 정의에서 조인 구조를 대신 정의할 수 있습니다. 예를 들어 다음과 같습니다.

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 및 제안을 제출하거나 투표하려면 [Azure Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* [조건에 따라 단계 실행(조건문)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [다른 값에 따라 단계 실행(switch 문)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [단계 실행 및 반복(루프)](../logic-apps/logic-apps-control-flow-loops.md)
* [그룹화된 작업 상태에 따라 단계 실행(범위)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
