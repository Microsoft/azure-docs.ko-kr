---
title: 연속 데이터를 처리 하는 작업 예약
description: Azure Logic Apps에서 슬라이딩 윈도우를 사용 하 여 연속 데이터를 처리 하는 되풀이 작업 만들기 및 실행
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: ab4bf802772c95d8c48a8cdba48def05e8a2761b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786914"
---
# <a name="schedule-and-run-tasks-for-contiguous-data-by-using-the-sliding-window-trigger-in-azure-logic-apps"></a>에서 슬라이딩 윈도우 트리거를 사용 하 여 연속 된 데이터에 대한 작업 예약 및 실행 Azure Logic Apps

연속 청크의 데이터를 처리 해야 하는 작업, 프로세스 또는 작업을 정기적으로 실행 하려면 **슬라이딩 윈도우** 트리거를 사용 하 여 논리 앱 워크플로를 시작할 수 있습니다. 워크플로를 시작 하기 위한 표준 시간대와 날짜 및 시간을 설정할 수 있으며 해당 워크플로를 반복 하기 위한 되풀이를 설정할 수 있습니다. 어떤 이유로 든 되풀이가 누락 되는 경우이 트리거는 누락 된 되풀이를 처리 합니다. 예를 들어 데이터베이스와 백업 저장소 간에 데이터를 동기화 할 때 데이터의 간격이 발생 하지 않고 동기화 되도록 슬라이딩 윈도우 트리거를 사용 합니다. 기본 제공 일정 트리거 및 작업에 대한 자세한 내용은 [Azure Logic Apps를 사용 하 여 되풀이 자동화, 작업 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조 하세요.

이 트리거가 지 원하는 몇 가지 패턴은 다음과 같습니다.

* 즉시를 실행 하 고 *n* 초, 분 또는 시간 마다 반복 합니다.

* 특정 날짜 및 시간에 시작한 다음, *n* 초, 분 또는 시간 마다 실행 하 고 반복 합니다. 이 트리거를 사용 하면 과거 되풀이를 모두 실행 하는 과거의 시작 시간을 지정할 수 있습니다.

* 실행 하기 전에 특정 기간에 대한 각 되풀이를 지연 합니다.

이 트리거와 되풀이 트리거 간의 차이점 또는 되풀이 워크플로를 예약 하는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용 하 여 되풀이 자동화 된 작업, 프로세스 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조 하세요.

> [!TIP]
> 논리 앱을 트리거하고 나중에 한 번만 실행 하려는 경우 [한 번만 실행 작업](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정에 등록할](https://azure.microsoft.com/free/)수 있습니다.

* [논리 앱](../logic-apps/logic-apps-overview.md)에 대한 기본 지식. 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 대해 알아보세요.

## <a name="add-sliding-window-trigger"></a>슬라이딩 윈도우 트리거 추가

1. [Azure portal](https://portal.azure.com)에 로그인합니다. 빈 논리 앱을 만듭니다.

1. 논리 앱 디자이너가 표시 되 면 검색 상자에 필터로 "슬라이딩 윈도우"를 입력 합니다. 트리거 목록에서 논리 앱 워크플로에서 첫 번째 단계로이 트리거를 선택 합니다. **슬라이딩 윈도우**

   !["슬라이딩 윈도우" 트리거 선택](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. 되풀이에 대한 간격 및 빈도를 설정합니다. 이 예제에서는 매주 워크플로를 실행하도록 이러한 속성을 설정합니다.

   ![간격 및 빈도 설정](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **간격** | yes | interval | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p>- 시간: 1-12,000시간 </br>- 분: 1-72,000분 </br>- 초: 1-9,999,999초<p>예를 들어 간격이 6이 고 빈도가 "Hour" 이면 되풀이 간격은 6 시간 마다입니다. |
   | **Frequency(빈도)** | yes | frequency | string | 되풀이 시간 단위: **초**, **분**또는 **시간** |
   ||||||

   ![고급 되풀이 옵션](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   추가 되풀이 옵션을 보려면 **새 매개 변수 추가** 목록을 엽니다. 
   선택 하는 옵션은 선택 후 트리거에 표시 됩니다.

   | 자산 | 필수 | JSON 이름 | Type | 설명 |
   |----------|----------|-----------|------|-------------|
   | **연기할** | 아닙니다. | delay | string | [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Durations) 사용 하 여 각 되풀이를 지연 하는 기간 |
   | **표준 시간대** | 아닙니다. | timeZone | string | 이 트리거가 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | 아닙니다. | startTime | string | 시작 날짜와 시간을 다음 형식으로 지정 합니다. <p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 오후 2:00에 2017 년 9 월 18 일을 원하는 경우 "2017-09-18T14:00:00"을 지정 하 고 태평양 표준시와 같은 표준 시간대를 선택 합니다. 또는 표준 시간대 없이 "2017-09-18T14:00:00Z"를 지정합니다. <p>**참고:** 이 시작 시간은 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset) 없이 [UTC 날짜/시간 형식](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)의 [ISO 8601 날짜/시간 사양](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)을 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 "Z"는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간은 첫 번째 발생이 고, 고급 되풀이의 경우 트리거는 시작 시간 보다 더 일찍 발생 하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. 이제 다른 작업을 사용 하 여 나머지 워크플로를 빌드합니다. 추가할 수 있는 작업에 대한 자세한 내용은 [Azure Logic Apps 커넥터](../connectors/apis-list.md)를 참조 하십시오.

## <a name="workflow-definition---sliding-window"></a>워크플로 정의-슬라이딩 윈도우

JSON을 사용 하는 논리 앱의 기본 워크플로 정의에서 선택한 옵션을 사용 하 여 슬라이딩 윈도우 트리거 정의를 볼 수 있습니다. 이 정의를 보려면 디자이너 도구 모음에서 **코드 보기**를 선택 합니다. 디자이너로 돌아가려면 디자이너 도구 모음에서 **디자이너**를 선택 합니다.

이 예에서는 각 되풀이의 지연이 매시간 되풀이에 대해 5 초 이면 슬라이딩 윈도우 트리거 정의가 기본 워크플로 정의에서 어떻게 표시 될 수 있는지를 보여 줍니다.

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>다음 단계

* [워크플로의 다음 작업 지연](../connectors/connectors-native-delay.md)
* [Logic Apps 커넥터](../connectors/apis-list.md)