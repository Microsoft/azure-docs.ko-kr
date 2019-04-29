---
title: 오류 문제 해결 및 진단 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps에서 워크플로 오류를 진단하고 해결하는 방법 알아보기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.date: 10/15/2017
ms.openlocfilehash: 62a74364939fffb6e06f51f1c0cabb6cce8c10e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60999811"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Azure Logic Apps에서 워크플로 오류를 진단하고 해결

논리 앱은 앱에서 문제를 진단하고 디버깅하도록 도울 수 있는 정보를 생성합니다. Azure Portal을 통해 워크플로의 각 단계를 검토하여 논리 앱을 진단할 수 있습니다. 또는 런타임 디버깅을 위해 워크플로에 몇 가지 단계를 추가할 수 있습니다.

## <a name="review-trigger-history"></a>트리거 기록 검토

각 논리 앱은 트리거로 시작합니다. 트리거가 발생하지 않는 경우 먼저 트리거 기록을 확인하세요. 이 기록은 논리 앱이 수행한 모든 트리거 시도 및 각 트리거 시도에서 입력과 출력에 대한 세부 정보를 나열합니다.

1. 트리거가 발생했는지를 확인하려면 논리 앱 메뉴에서 **개요**를 선택합니다. **트리거 기록** 아래에서 트리거의 상태를 검토합니다.

   > [!TIP]
   > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

   ![트리거 기록 검토](./media/logic-apps-diagnosing-failures/logic-app-trigger-history-overview.png)

   > [!TIP]
   > * 예상한 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택해보세요.
   > * 목록에 표시된 트리거 시도가 많아서 원하는 항목을 찾을 수 없는 경우 목록을 필터링해보세요.

   트리거 시도에서 가능한 상태는 다음과 같습니다.

   | 상태 | 설명 | 
   | ------ | ----------- | 
   | **성공함** | 트리거는 엔드포인트를 확인하고 사용할 수 있는 데이터를 찾았습니다. 일반적으로 "실행됨" 상태도 이 상태와 함께 나타납니다. 그렇지 않은 트리거 정의는 `SplitOn` 또는 조건을 충족하지 않는 명령을 포함합니다. <p>이 상태는 수동 트리거, 되풀이 트리거 또는 폴링 트리거에 적용될 수 있습니다. 작업이 처리되지 않은 오류를 생성할 때 트리거가 성공적으로 실행될 수는 있지만 실행 자체는 실패할 수 있습니다. | 
   | **생략** | 트리거는 엔드포인트를 확인했지만 데이터가 없습니다. | 
   | **실패** | 오류가 발생했습니다. 실패한 트리거에 생성된 오류 메시지를 검토하려면 해당 트리거 시도를 선택하고 **출력**을 선택합니다. 예를 들어 유효하지 않은 입력을 찾을 수 있습니다. | 
   ||| 

   동일한 날짜와 시간을 사용하는 여러 트리거 항목이 있을 수 있습니다. 논리 앱이 여러 항목을 검색하는 경우에 발생됩니다. 
   트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 실행하는 논리 앱 인스턴스를 만듭니다. 기본적으로 실행을 시작하기 전에 대기하는 워크플로가 없도록 각 인스턴스는 병렬로 실행합니다.

   > [!TIP]
   > 다음 되풀이를 기다리지 않고 트리거를 다시 확인할 수 있습니다. 개요 도구 모음에서 **트리거 실행**을 선택하고 트리거를 선택합니다. 그러면 확인을 강제로 수행합니다. 또는 Logic Apps 디자이너 도구 모음에서 **실행**을 선택합니다.

3. 트리거 시도에 대한 세부 정보를 검사하려면 **트리거 기록** 아래에서 해당 트리거 시도를 선택합니다. 

   ![트리거 시도를 선택합니다.](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

4. 트리거가 생성한 모든 입력 및 출력을 검토합니다. 트리거 출력은 트리거에서 온 데이터를 보여 줍니다. 이러한 출력은 모든 속성이 예상대로 반환되었는지 확인하는 데 도움이 됩니다.

   > [!NOTE]
   > 이해할 수 없는 콘텐츠가 있는 경우 Azure Logic Apps에서 [다양한 콘텐츠 형식을 처리](../logic-apps/logic-apps-content-type.md)하는 방법을 알아봅니다.

   ![트리거 출력](./media/logic-apps-diagnosing-failures/trigger-outputs.png)

## <a name="review-run-history"></a>실행 기록 검토

각 발생된 트리거는 워크플로 실행을 시작합니다. 워크플로에서 각 단계의 상태 및 각 단계의 입력과 출력을 비롯하여 해당 실행 중에 발생한 결과를 검토할 수 있습니다.

1. 논리 앱 메뉴에서 **개요**를 선택합니다. **실행 기록** 아래에서 발생된 트리거의 실행을 검토합니다.

   > [!TIP]
   > 논리 앱 메뉴가 표시되지 않으면 Azure 대시보드로 돌아가서 논리 앱을 다시 엽니다.

   ![실행 기록 검토](./media/logic-apps-diagnosing-failures/logic-app-runs-history-overview.png)

   > [!TIP]
   > * 예상한 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택해보세요.
   > * 목록에 표시된 실행이 많아서 원하는 항목을 찾을 수 없는 경우 목록을 필터링해보세요.

   실행에서 가능한 상태는 다음과 같습니다.

   | 상태 | 설명 | 
   | ------ | ----------- | 
   | **성공함** | 모든 작업에 성공했습니다. <p>특정 작업에서 오류가 발생하는 경우 워크플로에서 다음 작업은 해당 오류를 처리합니다. | 
   | **실패** | 하나 이상의 작업이 실패하면 워크플로에서 오류를 처리하기 위한 후속 동작이 설정되지 않습니다. | 
   | **취소** | 워크플로가 실행 중이지만 취소 요청을 받았습니다. | 
   | **실행 중** | 워크플로가 현재 실행 중입니다. <p>이 상태는 제한된 워크플로 또는 현재 요금제로 인해 발생할 수 있습니다. 자세한 내용은 [가격 책정 페이지에서 작업 제한](https://azure.microsoft.com/pricing/details/logic-apps/)을 참조하세요. [진단 로깅](../logic-apps/logic-apps-monitor-your-logic-apps.md)을 설정한 경우 발생하는 모든 제한된 이벤트에 대한 정보를 얻을 수 있습니다. | 
   ||| 

2. 특정 실행의 각 단계에 대한 세부 정보를 검토합니다. **실행 기록** 아래에서 검사하려는 실행을 선택합니다.

   ![실행 기록 검토](./media/logic-apps-diagnosing-failures/logic-app-run-history.png)

   실행 자체가 성공했는지 아니면 실패했는지와 상관 없이 실행 세부 정보 보기에서는 각 단계 및 성공 또는 실패 여부를 보여줍니다.

   ![논리 앱 실행에 대한 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

3. 특정 단계에 대한 입력, 출력 및 오류 메시지를 검사하려면 셰이프가 확장되어 세부 정보를 표시하도록 해당 단계를 선택합니다. 예를 들면 다음과 같습니다.

   ![단계 세부 정보 보기](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>런타임 디버깅 수행

디버깅에 도움이 되도록 하려면 트리거 및 실행 기록을 검토하는 한편 워크플로에 진단 단계를 추가할 수 있습니다. 예를 들어, HTTP 요청을 검사하고 정확한 해당 크기, 모양 및 형식을 확인할 수 있도록 [Webhook Tester](https://webhook.site/) 서비스를 사용하는 단계를 추가할 수 있습니다.

1. [Webhook Tester](https://webhook.site/)를 방문하고 생성된 고유한 URL을 복사합니다.

2. 논리 앱에서 테스트하려는 본문 콘텐츠(예: 식, 다른 단계 출력)를 포함하는 HTTP POST 작업을 추가합니다.

3. Webhook Tester의 URL을 HTTP POST 작업에 붙여 넣습니다.

4. 요청이 Logic Apps에서 생성될 때 형성되는 방식을 검토하려면 논리 앱을 실행하고 Webhook Tester에서 자세한 내용을 확인합니다.

## <a name="next-steps"></a>다음 단계

[논리 앱 모니터링](../logic-apps/logic-apps-monitor-your-logic-apps.md)
