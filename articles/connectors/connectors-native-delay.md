---
title: 워크플로의 다음 작업 지연
description: 에서 지연 또는 지연 작업을 사용 하 여 논리 앱 워크플로에서 다음 작업을 실행할 때까지 기다립니다 Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, klam, logicappspm
ms.topic: conceptual
ms.date: 05/25/2019
tags: connectors
ms.openlocfilehash: 5348ade1ba6eec6cbd360849411b4520cb3c2b19
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74787339"
---
# <a name="delay-running-the-next-action-in-azure-logic-apps"></a>Azure Logic Apps에서 다음 작업 실행 지연

논리 앱이 다음 작업을 실행 하기 전에 일정 시간 동안 대기 하도록 하려면 논리 앱의 워크플로에서 동작 전에 기본 제공 **지연 일정** 작업을 추가 하면 됩니다. 또는 다음 작업을 실행 하기 전에 기본 제공 **지연** 시간을 추가 하 여 특정 날짜 및 시간까지 기다릴 수 있습니다. 기본 제공 일정 작업 및 트리거에 대한 자세한 내용은 [Azure Logic Apps를 사용 하 여 반복 되는 자동, 작업 및 워크플로 예약 및 실행](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)을 참조 하세요.

* **Delay**: 다음 작업을 실행 하기 전에 지정 된 시간 단위 (예: 초, 분, 시간, 일, 주 또는 월)를 대기 합니다.

* **지연**시간: 다음 작업을 실행 하기 전에 지정 된 날짜 및 시간까지 대기 합니다.

이러한 작업을 사용 하는 몇 가지 예제는 다음과 같습니다.

* 평일까지 기다렸다가 전자 메일로 상태 업데이트 전송

* 데이터를 다시 시작 하 고 검색 하기 전에 HTTP 호출이 완료 될 때까지 워크플로를 지연 합니다.

## <a name="prerequisites"></a>전제 조건

* Azure 구독. 구독이 없는 경우 [무료 Azure 계정에 등록할](https://azure.microsoft.com/free/)수 있습니다.

* [논리 앱](../logic-apps/logic-apps-overview.md)에 대한 기본 지식. 작업을 사용 하려면 먼저 논리 앱이 트리거로 시작 해야 합니다. 지연 동작을 추가 하기 전에 원하는 트리거를 사용 하 고 다른 작업을 추가할 수 있습니다. 이 항목에서는 Office 365 Outlook 트리거를 사용 합니다. 논리 앱을 처음 접하는 경우 [첫 번째 논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 대해 알아보세요.

<a name="add-delay"></a>

## <a name="add-the-delay-action"></a>지연 동작 추가

1. 논리 앱 디자이너에서 지연 작업을 추가 하려는 단계 아래에 있는 **새 단계**를 선택 합니다.

   단계 간의 지연 동작을 추가 하려면 단계를 연결 하는 화살표 위로 포인터를 이동 합니다. 표시 되는 더하기 기호 (+)를 선택 하 고 **작업 추가**를 선택 합니다.

1. 검색 상자에 필터로 "지연"을 입력 합니다. 작업 목록에서 다음 작업: **지연** 을 선택 합니다.

   !["지연" 작업 추가](./media/connectors-native-delay/add-delay-action.png)

1. 다음 작업을 실행 하기 전에 대기할 시간을 지정 합니다.

   ![지연 시간 설정](./media/connectors-native-delay/delay-time-intervals.png)

   | 자산 | JSON 이름 | 필수 | Type | 설명 |
   |----------|-----------|----------|------|-------------|
   | 카운트 | count | yes | 정수 | 지연할 시간 단위 수 |
   | 단위 | 단위 | yes | string | 시간 단위 (예: `Second`, `Minute`, `Hour`, `Day`, `Week`또는 `Month` |
   ||||||

1. 워크플로에서 실행 하려는 다른 작업을 추가 합니다.

1. 완료되면 논리 앱을 저장합니다.

<a name="add-delay-until"></a>

## <a name="add-the-delay-until-action"></a>지연 작업을 추가 합니다.

1. 논리 앱 디자이너에서 지연 작업을 추가 하려는 단계 아래에 있는 **새 단계**를 선택 합니다.

   단계 간의 지연 동작을 추가 하려면 단계를 연결 하는 화살표 위로 포인터를 이동 합니다. 표시 되는 더하기 기호 (+)를 선택 하 고 **작업 추가**를 선택 합니다.

1. 검색 상자에 필터로 "지연"을 입력 합니다. 작업 목록에서 다음 **시간까지 지연** 작업을 선택 합니다.

   !["다음 기간까지 지연" 작업 추가](./media/connectors-native-delay/add-delay-until-action.png)

1. 워크플로를 다시 시작 하려는 경우의 종료 날짜 및 시간을 제공 합니다.

   ![지연 시간을 종료 하는 시간에 대한 타임 스탬프를 지정 합니다.](./media/connectors-native-delay/delay-until-timestamp.png)

   | 자산 | JSON 이름 | 필수 | Type | 설명 |
   |----------|-----------|----------|------|-------------|
   | Timestamp | timestamp | yes | string | 다음 형식을 사용 하 여 워크플로를 다시 시작 하는 종료 날짜 및 시간입니다. <p>YYYY-MM-DD Yyyy-mm-ddthh: MM: ssZ <p>예를 들어, 2017 년 9 월 18 일 오후 2:00 시에는 "2017-09-18T14:00:00Z"를 지정 합니다. <p>**참고:** 이 시간 형식은 utc [오프셋](https://en.wikipedia.org/wiki/UTC_offset)없이 [utc 날짜/시간 형식의](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) 따라야 합니다. 표준 시간대가 없으면 공백 없이 끝에 문자 "Z"를 추가 해야 합니다. 이 "Z"는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. |
   ||||||

1. 워크플로에서 실행 하려는 다른 작업을 추가 합니다.

1. 완료되면 논리 앱을 저장합니다.

## <a name="next-steps"></a>다음 단계

* [되풀이 트리거를 사용 하 여 되풀이 작업 및 워크플로 만들기, 예약 및 실행](../connectors/connectors-native-recurrence.md)
* [Logic Apps 커넥터](../connectors/apis-list.md)