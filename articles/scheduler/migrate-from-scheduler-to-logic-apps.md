---
title: Azure Scheduler에서 Azure Logic Apps로 마이그레이션
description: 조만간 사용이 중지되는 Azure Scheduler의 작업을 Azure Logic Apps로 바꾸는 방법을 알아봅니다.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531685"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Azure Scheduler 작업을 Azure Logic Apps로 마이그레이션

> [!IMPORTANT]
> Azure Scheduler는 조만간 사용 중지되고 Azure Logic Apps로 대체됩니다. 작업을 예약하려면 이 문서의 설명에 따라 Azure Logic Apps로 전환하세요.

이 문서에서는 Azure Scheduler 대신 Azure Logic Apps를 사용하여 자동화된 워크플로를 만들어서 일회성 및 되풀이 작업을 예약하는 방법을 보여줍니다. Logic Apps를 사용하여 예약된 작업을 만들면 다음과 같은 이점이 있습니다.

* 각 논리 앱이 별도의 Azure 리소스이므로 *작업 컬렉션* 개념에 대해 걱정할 필요가 없습니다.

* 단일 논리 앱을 사용하여 일회성 작업을 여러 개 실행할 수 있습니다.

* Azure Logic Apps 서비스는 표준 시간대 및 DST(일광 절약 시간)를 지원합니다.

자세한 내용을 알아보려면 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 참조하거나 또는이 빠른 시작에서 첫 번째 논리 앱을 만들어 보세요. [첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다.

* HTTP 요청을 전송하여 논리 앱을 트리거하려면 [Postman 데스크톱 앱](https://www.getpostman.com/apps) 같은 도구를 사용합니다.

## <a name="schedule-one-time-jobs"></a>일회성 작업 예약

단일 논리 앱을 만들어서 일회성 작업을 여러 개 실행할 수 있습니다. 

### <a name="create-your-logic-app"></a>논리 앱 만들기

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 빈 논리 앱을 만듭니다. 

   기본 단계에 따라 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에 “http 요청”을 필터로 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **HTTP 요청을 수신 되는 경우** 

   !["요청" 트리거 추가](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. 요청 트리거의 경우 필요에 따라 논리 앱 디자이너가 들어오는 요청의 입력 구조를 이해하도록 도와주고 워크플로 뒷부분에서 출력을 좀 더 쉽게 선택하도록 만들어주는 JSON 스키마를 제공할 수 있습니다.

   스키마를 지정하려면 다음과 같이 **요청 본문 JSON 스키마** 상자에 스키마를 입력합니다. 

   ![요청 스키마](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   스키마는 없지만 JSON 형식의 샘플 페이로드가 있는 경우 해당 페이로드로 스키마를 생성할 수 있습니다.

   1. 요청 트리거에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택합니다.

   1. 다음과 같이 **샘플 JSON 페이로드를 입력하거나 붙여넣기**에서 샘플 페이로드를 입력하고 **완료**를 선택합니다.

      ![샘플 페이로드](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. 트리거 아래에서 **다음 단계**를 선택합니다. 

1. 검색 상자에 필터로 "다음 기간까지 지연"을 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **다음 기간까지 지연**

   이 작업은 지정된 날짜 및 시간까지 논리 앱 워크플로를 일시 중지합니다.

   !["다음 기간까지 지연" 작업 추가](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. 논리 앱 워크플로를 시작할 타임스탬프를 입력합니다. 

   **타임스탬프** 상자 내부를 클릭하면 필요에 따라 트리거에서 출력을 선택할 수 있는 동적 콘텐츠 목록이 나타납니다.

   !["다음 기간까지 지연" 세부 정보 입력](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. [~200+ 커넥터](../connectors/apis-list.md)에서 실행하려는 다른 작업을 선택하여 추가합니다. 

   예를 들어 URL로 요청을 전송하는 HTTP 작업 또는 Storage 큐, Service Bus 큐, Service Bus 토픽을 사용하는 작업을 포함할 수 있습니다. 

   ![HTTP 동작](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. 완료되면 논리 앱을 저장합니다.

   ![논리 앱 저장](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   논리 앱을 처음으로 저장하면 논리 앱의 요청 트리거에 대한 엔드포인트 URL이 **HTTP POST URL** 상자에 나타납니다. 
   논리 앱을 호출하고 논리 앱으로 입력을 보내 처리하려면 이 URL을 호출 대상으로 사용합니다.

   ![요청 트리거 엔드포인트 URL 저장](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. 나중에 논리 앱을 트리거하는 수동 요청을 보낼 수 있도록 이 엔드포인트 URL을 복사하여 저장합니다. 

## <a name="start-a-one-time-job"></a>일회성 작업 시작

일회성 작업을 수동으로 실행하거나 트리거하려면 논리 앱의 요청 트리거에 대한 엔드포인트 URL에 호출을 보냅니다. 이 호출에서 전송할 입력 또는 페이로드를 지정합니다. 앞에서 스키마를 지정하여 입력 또는 페이로드에 대해 설명했을 수도 있습니다. 

예를 들어 Postman 앱을 사용하는 경우 이 샘플과 비슷한 설정으로 POST 요청을 만든 후 **보내기**를 선택하여 요청을 만들 수 있습니다.

| 요청 메서드 | URL | 본문 | 헤더 |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **원시** <p>**JSON(응용 프로그램/json)** <p>**원시** 상자에 요청에서 보낼 페이로드를 입력합니다. <p>**참고**: 이 설정을 자동으로 구성 합니다 **헤더** 값입니다. | **키**: 콘텐츠 형식 <br>**값**: 응용 프로그램/json
 |||| 

![수동으로 논리 앱을 트리거하는 요청 보내기](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

호출을 보낸 후 **본문** 탭의 **원시** 상자 아래에 논리 앱의 응답이 표시됩니다. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> 나중에 작업을 취소하려면 **헤더** 탭을 선택합니다. 응답에서 **x-ms-workflow-run-id** 헤더 값을 찾아 복사합니다. 
>
> ![response](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>일회성 작업 취소

Logic Apps에서 각 일회성 작업은 단일 논리 앱 실행 인스턴스로 실행됩니다. 일회성 작업을 취소하려면 Logic Apps REST API에서 [Workflow Runs - Cancel](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel)을 사용하면 됩니다. 트리거 호출을 보낼 때 [워크플로 실행 ID](#workflow-run-id)를 제공해야 합니다.

## <a name="schedule-recurring-jobs"></a>되풀이 작업 예약

### <a name="create-your-logic-app"></a>논리 앱 만들기

1. [Azure Portal](https://portal.azure.com)의 논리 앱 디자이너에서 빈 논리 앱을 만듭니다. 

   기본 단계에 따라 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

1. 검색 상자에서 필터로 “되풀이”를 입력합니다. 트리거 목록에서 다음 트리거를 선택합니다. **되풀이** 

   !["되풀이" 트리거 추가](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. 원하는 경우 고급 일정을 설정합니다.

   ![고급 일정](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   고급 일정 옵션에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 되풀이 작업 및 워크플로를 만들고 실행](../connectors/connectors-native-recurrence.md)을 참조하세요.

1. [~200+ 커넥터](../connectors/apis-list.md)에서 원하는 다른 작업을 선택하여 추가합니다. 트리거 아래에서 **다음 단계**를 선택합니다. 원하는 작업을 찾아 선택합니다.

   예를 들어 URL로 요청을 전송하는 HTTP 작업 또는 Storage 큐, Service Bus 큐, Service Bus 토픽을 사용하는 작업을 포함할 수 있습니다. 

   ![HTTP 동작](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. 완료되면 논리 앱을 저장합니다.

   ![논리 앱 저장](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>고급 설정

작업을 사용자 지정하는 다른 방법이 있습니다.

### <a name="retry-policy"></a>다시 시도 정책

일시적인 오류가 발생할 때 논리 앱에서 작업 재실행을 시도하는 방법을 제어하려면 다음과 같이 각 작업의 설정에서 [재시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 설정하면 됩니다.

1. 작업의 (**...** ) 메뉴를 열고 **설정**을 선택합니다.

   ![작업 설정 열기](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. 원하는 재시도 정책을 선택합니다. 각 정책에 대한 자세한 내용은 [재시도 정책](../logic-apps/logic-apps-exception-handling.md#retry-policies)을 참조하세요.

   ![재시도 정책 선택](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>예외 및 오류 처리

Azure Scheduler에서 기본 작업이 실행에 실패하는 경우 오류 조건을 해결하는 대체 작업을 실행할 수 있습니다. Azure Logic Apps에서도 동일한 작업을 수행할 수 있습니다.

1. 논리 앱 디자이너에서, 처리하려는 작업의 위쪽에서 포인터를 단계 사이의 화살표 위로 이동하고 **병렬 분기 추가**를 선택합니다. 

   ![병렬 분기 추가](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. 대체 작업으로 실행할 작업을 찾아서 선택합니다.

   ![병렬 작업 추가](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. 대체 작업에서 (**...** ) 메뉴를 열고 **실행 조건 구성**을 선택합니다.

   ![실행 조건 구성](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. **성공** 속성의 확인란을 선택 취소합니다. **실패함**, **건너뜀** 및 **시간이 초과됨** 속성을 선택합니다.

   !["실행 조건" 속성 설정](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. 모두 마쳤으면 **완료**를 선택합니다.

예외 처리에 대한 자세한 내용은 [오류 및 예외 처리 - RunAfter 속성](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property)을 참조하세요.

## <a name="faq"></a>FAQ

<a name="retire-date"></a> 

**Q**: Azure Scheduler의 사용을 중지 하는 경우는? <br>
**A**: Azure Scheduler는 2019 년 9 월 30 일에 사용 중지 되도록 예약 됩니다.

**Q**: 서비스 사용 중지 한 후 내 Scheduler 작업 컬렉션 및 작업에 미치는 영향 <br>
**A**: 모든 Scheduler 작업 컬렉션 및 작업 시스템에서 삭제 됩니다.

**Q**: 백업 또는 Logic Apps로 내 스케줄러 작업을 마이그레이션하기 전에 다른 작업을 수행 합니까? <br>
**A**: 모범 사례로 항상 작업 백업 합니다. 이전에 만든 논리 앱이 예상대로 실행되는지 확인한 후 Scheduler 작업을 삭제하거나 사용하지 않도록 설정하세요. 

**Q**: Logic Apps 내 작업 Scheduler에서 마이그레이션할 수 있는 도구는 있나요? <br>
**A**: 각 스케줄러 작업 고유 이므로 모든 상황에 맞는 도구 존재 하지 않습니다. 그러나 요구 사항에 맞게 수정할 수 있는 다양한 스크립트가 제공됩니다. 스크립트 가용성은 나중에 다시 확인하세요.

**Q**: Scheduler 작업 내 마이그레이션에 대 한 지원은 어디서 구할 수 있습니까? <br>
**A**: 지원을 받으려면 가지는 다음과 같습니다. 

**Azure Portal**

Azure 구독에 유료 지원 플랜이 있는 경우 Azure Portal에서 기술 지원 요청을 만들 수 있습니다. 없는 경우 다른 지원 옵션을 선택할 수 있습니다.

1. [Azure Portal](https://portal.azure.com) 주 메뉴에서 **도움말 + 지원**을 선택합니다.

1. **지원** 아래에서 **새 지원 요청**을 선택합니다. 요청에 대한 다음 세부 정보를 제공합니다.

   | 설정 | 값 |
   |---------|-------|
   | **문제 유형** | **기술** | 
   | **구독** | <*your-Azure-subscription*> | 
   | **서비스** | **모니터링 및 관리** 아래에서 **Scheduler**를 선택합니다. | 
   ||| 

1. 원하는 지원 옵션을 선택합니다. 유료 지원 플랜이 있는 경우 **다음**을 선택합니다.

**커뮤니티**

* [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps를 사용하여 정기적으로 실행되는 작업 및 워크플로 만들기](../connectors/connectors-native-recurrence.md)
* [자습서: 일정 기반 논리 앱으로 트래픽 확인](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)