---
title: 상태 확인, 로깅 설정 및 경고 받기 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 상태 모니터링, 진단 데이터 로깅 및 경고 설정
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 5c1b1e15-3b6c-49dc-98a6-bdbe7cb75339
ms.date: 07/21/2017
ms.openlocfilehash: 80776f9284752e8554486cb458096ccc9319949e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61325287"
---
# <a name="monitor-status-set-up-diagnostics-logging-and-turn-on-alerts-for-azure-logic-apps"></a>상태 모니터링, 진단 로깅 설정, Azure Logic Apps에 대한 경고 설정

[논리 앱을 만들고 실행](../logic-apps/quickstart-create-first-logic-app-workflow.md)한 후 해당 실행 기록, 트리거 기록, 상태 및 성능을 확인할 수 있습니다. 실시간 이벤트 모니터링 및 보다 풍부한 디버깅은 논리 앱에 대한 [진단 로깅](#azure-diagnostics)을 설정합니다. 이런 방식으로 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 [이벤트를 찾고 볼](#find-events) 수 있습니다. 또한 Azure Storage 및 Azure Event Hub와 같은 [다른 서비스와 함께 진단 데이터를 사용](#extend-diagnostic-data)할 수도 있습니다. 

오류 또는 가능한 다른 문제에 대한 알림을 받으려면 [경고](#add-azure-alerts)를 설정합니다. 예를 들어 "한 시간에 5개 이상의 실행이 실패하는 경우"를 검색하는 경고를 만들 수 있습니다. [Azure 진단 이벤트 설정 및 속성](#diagnostic-event-properties)을 사용하여 프로그래밍 방식으로 모니터링, 추적 및 로깅을 설정할 수도 있습니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-runs-and-trigger-history-for-your-logic-app"></a>논리 앱에 대한 실행 및 트리거 기록 보기

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 찾으려면 주요 Azure 메뉴에서 **모든 서비스**를 선택합니다. 검색 상자에 "논리 앱"을 입력하고 **논리 앱**을 선택합니다.

   ![논리 앱 찾기](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   Azure Portal에서는 Azure 구독과 연결된 모든 논리 앱을 보여 줍니다. 

2. 논리 앱을 선택한 후 **개요**를 선택합니다.

   Azure Portal에서는 논리 앱에 대한 실행 기록 및 트리거 기록을 보여 줍니다. 예를 들면 다음과 같습니다.

   ![논리 앱 실행 기록 및 트리거 기록](media/logic-apps-monitor-your-logic-apps/overview.png)

   * **실행 기록**은 논리 앱에 대한 모든 실행을 보여 줍니다. 
   * **트리거 기록**은 논리 앱에 대한 모든 트리거 작업을 나열합니다.

   상태 설명은 [논리 앱의 문제 해결](../logic-apps/logic-apps-diagnosing-failures.md)을 참조하세요.

   > [!TIP]
   > 예상하는 데이터를 찾을 수 없는 경우 도구 모음에서 **새로 고침**을 선택합니다.

3. 특정 실행의 단계를 보려면 **실행 기록** 아래에서 해당 실행을 선택합니다. 

   모니터링 보기는 해당 실행의 각 단계를 표시합니다. 예를 들면 다음과 같습니다.

   ![특정 실행에 대한 작업](media/logic-apps-monitor-your-logic-apps/monitor-view-updated.png)

4. 실행에 대한 자세한 내용을 보려면 **실행 세부 정보**를 선택합니다. 이 정보는 실행에 대한 단계, 상태, 입력 및 출력을 요약합니다. 

   !["실행 세부 정보" 선택](media/logic-apps-monitor-your-logic-apps/run-details.png)

   예를 들어 [Logic Apps에 대한 REST API](https://docs.microsoft.com/rest/api/logic)를 사용할 때 필요할 수 있는 실행의 **상관 관계 ID**를 가져올 수 있습니다.

5. 특정 단계에 대한 세부 정보를 얻으려면 해당 단계를 선택합니다. 이제 해당 단계에 대해 발생한 입력, 출력 및 모든 오류와 같은 세부 정보를 검토할 수 있습니다. 예를 들면 다음과 같습니다.

   ![단계 세부 정보](media/logic-apps-monitor-your-logic-apps/monitor-view-details.png)
   
   > [!NOTE]
   > 모든 런타임 세부 정보 및 이벤트는 Logic Apps 서비스 내에서 암호화됩니다. 사용자가 해당 데이터를 보기 위해 요청하는 경우에만 해독됩니다. [Azure 역할 기반 Access Control(RBAC)](../role-based-access-control/overview.md)을 통해서 이러한 이벤트에 대한 액세스를 제어할 수도 있습니다.

6. 특정 트리거 이벤트에 대한 세부 정보를 얻으려면 **개요** 창으로 돌아갑니다. **트리거 기록** 아래에서 트리거 이벤트를 선택합니다. 이제 입력 및 출력과 같은 세부 정보를 검토할 수 있습니다. 예를 들어 다음과 같습니다.

   ![트리거 이벤트 출력 세부 정보](media/logic-apps-monitor-your-logic-apps/trigger-details.png)

<a name="azure-diagnostics"></a>

## <a name="turn-on-diagnostics-logging-for-your-logic-app"></a>논리 앱에 대한 진단 로깅 켜기

런타임 세부 정보 및 이벤트를 사용 하 여 보다 풍부한 디버깅에 대 한 진단 로깅을 사용 하 여 설정할 수 있습니다 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md)합니다. Azure Monitor는 Azure 클라우드를 모니터링 하 고 온-프레미스 환경의 해당 가용성 및 성능을 유지 하는 데에 서비스입니다. 

시작하기 전에 Log Analytics 작업 영역이 필요합니다. [Log Analytics 작업 영역을 만드는 방법](../azure-monitor/learn/quick-create-workspace.md)을 알아봅니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱을 찾고 선택합니다. 

2. 논리 앱 블레이드 메뉴에서 **모니터링**, 선택 **진단** > **진단 설정을**합니다.

   ![모니터링, 진단, 진단 설정으로 이동](media/logic-apps-monitor-your-logic-apps/logic-app-diagnostics.png)

3. **진단 설정** 아래에서 **켜기**를 선택합니다.

   ![진단 로그 설정](media/logic-apps-monitor-your-logic-apps/turn-on-diagnostics-logic-app.png)

4. 이제 표시된 것처럼 로깅에 대한 Log Analytics 작업 영역 및 이벤트 범주를 선택합니다.

   1. **Log Analytics에 보내기**를 선택합니다. 
   2. **Log Analytics** 아래에서 **구성**을 선택합니다. 
   3. **OMS 작업 영역**에서 로깅에 사용할 작업 영역을 선택합니다.
      > [!NOTE]
      > OMS 작업 영역을 이제 Log Analytics 작업 영역이라고 합니다.
   4. **로그** 아래에서 **WorkflowRuntime** 범주를 선택합니다.
   5. 메트릭 간격을 선택합니다.
   6. 완료하면 **저장**을 선택합니다.

   ![로깅에 대한 Log Analytics 작업 영역 및 데이터 선택](media/logic-apps-monitor-your-logic-apps/send-diagnostics-data-log-analytics-workspace.png)

이제 트리거 이벤트, 실행 이벤트 및 작업 이벤트에 대한 이벤트 및 기타 데이터를 찾을 수 있습니다.

<a name="find-events"></a>

## <a name="find-events-and-data-for-your-logic-app"></a>논리 앱에 대한 이벤트 및 데이터 찾기

논리 앱에서 트리거 이벤트, 실행 이벤트 및 작업 이벤트와 같은 이벤트를 찾고 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **모든 서비스**를 선택합니다. "로그 분석"에 대해 검색한 후 다음과 같이 **Log Analytics**를 선택합니다.

   !["Log Analytics" 선택](media/logic-apps-monitor-your-logic-apps/browseloganalytics.png)

2. **Log Analytics** 아래에서 Log Analytics 작업 영역을 찾고 선택합니다. 

   ![Log Analytics 작업 영역 선택](media/logic-apps-monitor-your-logic-apps/selectla.png)

3. **관리**에서 **로그 검색**을 선택합니다.

   ![“로그 검색” 선택](media/logic-apps-monitor-your-logic-apps/log-search.png)

4. 검색 상자에 찾으려는 필드를 지정하고 **Enter** 키를 누릅니다. 입력을 시작할 때 사용할 수 있는 가능한 일치 및 작업이 표시됩니다. 

   예를 들어 발생한 상위 10개의 이벤트를 찾으려면 이 검색 쿼리: **search Category == "WorkflowRuntime" | limit 10**을 입력하고 선택합니다.

   ![검색 문자열 입력](media/logic-apps-monitor-your-logic-apps/oms-start-query.png)

   에 대해 자세히 알아보세요 [Azure Monitor 로그에서 데이터를 찾는 방법](../log-analytics/log-analytics-log-searches.md)합니다.

5. 결과 페이지의 왼쪽 모음에서 보려는 시간 프레임을 선택합니다.
필터를 추가하여 쿼리를 구체화하려면 **+추가**를 선택합니다.

   ![쿼리 결과에 대한 시간 프레임 선택](media/logic-apps-monitor-your-logic-apps/query-results.png)

6. **필터 추가** 아래에서 원하는 필터를 찾을 수 있도록 필터 이름을 입력합니다. 필터를 선택하고 **+추가**를 선택합니다.

   이 예제에서는 "상태"라는 단어를 사용하여 **AzureDiagnostics** 아래에서 실패한 이벤트를 찾습니다.
   여기에서 **status_s**에 대한 필터가 이미 선택되어 있습니다.

   ![필터 선택](media/logic-apps-monitor-your-logic-apps/log-search-add-filter.png)

7. 왼쪽 모음에서 사용하려는 필터 값을 선택하고 **적용**을 선택합니다.

   ![필터 값 선택, "적용" 선택](media/logic-apps-monitor-your-logic-apps/log-search-apply-filter.png)

8. 이제 작성 중인 쿼리로 돌아갑니다. 선택한 필터 및 값으로 쿼리가 업데이트됩니다. 이제 이전 결과 또한 필터링되어 있습니다.

   ![필터링된 결과와 함께 쿼리로 돌아가기](media/logic-apps-monitor-your-logic-apps/log-search-query-filtered-results.png)

9. 나중에 사용할 쿼리를 저장하려면 **저장**을 선택합니다. [쿼리를 저장하는 방법](../logic-apps/logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md#save-oms-query)에 대해 알아봅니다.

<a name="extend-diagnostic-data"></a>

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>다른 서비스와 함께 진단 데이터를 사용하는 방법 및 위치 확장

Azure Monitor 로그와 함께 사용법 논리 앱의 진단 데이터의 다른 Azure 서비스를 사용 하 여 예를 들어 확장할 수 있습니다. 

* [Azure Storage에 Azure 진단 로그 보관](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Azure Event Hubs로 Azure 진단 로그 스트림](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

그런 다음 [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) 및 [Power BI](../azure-monitor/platform/powerbi.md)와 같은 다른 서비스의 원격 분석 및 분석을 사용하여 실시간으로 모니터링할 수 있습니다. 예를 들면 다음과 같습니다.

* [Event Hub에서 Stream Analytics로 데이터 스트림](../stream-analytics/stream-analytics-define-inputs.md)
* [Stream Analytics를 사용하여 스트리밍 데이터 분석 및 Power BI에서 실시간 분석 대시보드 만들기](../stream-analytics/stream-analytics-power-bi-dashboard.md)

설정하려는 옵션에 따라 먼저 [Azure 저장소 계정을 만들](../storage/common/storage-create-storage-account.md)거나 [Azure 이벤트 허브를 만들](../event-hubs/event-hubs-create.md)어야 합니다. 그런 다음 진단 데이터를 전송하려는 위치에 대한 옵션을 선택합니다.

![Azure 저장소 계정 또는 이벤트 허브로 데이터 보내기](./media/logic-apps-monitor-your-logic-apps/storage-account-event-hubs.png)

> [!NOTE]
> 보존 기간은 저장소 계정을 사용하도록 선택한 경우에만 적용됩니다.

<a name="add-azure-alerts"></a>

## <a name="set-up-alerts-for-your-logic-app"></a>논리 앱에 대한 경고 설정

논리 앱에 대한 특정 메트릭 또는 초과된 임계값을 모니터링하려면 [Azure에서 경고](../azure-monitor/platform/alerts-overview.md)를 설정합니다. [Azure의 매트릭](../monitoring-and-diagnostics/monitoring-overview-metrics.md)에 대해 알아봅니다. 

없이 경고를 설정할 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md), 다음이 단계를 수행 합니다. 고급 경고 조건 및 작업에 대 한 [Azure Monitor 로그 설정](#azure-diagnostics) 너무 합니다.

1. 논리 앱 블레이드 메뉴의 **모니터링** 아래에서 다음과 같이 **진단** > **경고 규칙** > **경고 추가**를 선택합니다.

   ![논리 앱에 대한 경고 추가](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

2. **경고 규칙 추가** 블레이드에서 표시된 것처럼 경고를 만듭니다.

   1. **리소스** 아래에서 논리 앱을 선택하지 않은 경우 선택합니다. 
   2. 경고에 대한 이름 및 설명을 제공합니다.
   3. 추적하려는 **메트릭** 또는 이벤트를 선택합니다.
   4. **조건**을 선택하고 메트릭에 대한 **임계값**을 지정하고 이 메트릭 모니터링에 대한 **기간**을 선택합니다.
   5. 경고에 대한 메일을 보낼 것인지 여부를 선택합니다. 
   6. 경고를 보내기 위한 다른 전자 메일 주소를 지정합니다. 
   또한 경고를 보내려는 웹후크 URL을 지정할 수도 있습니다.

   예를 들어 이 규칙은 한 시간에 5개 이상의 실행이 실패하는 경우 경고를 보냅니다.

   ![메트릭 경고 규칙 만들기](media/logic-apps-monitor-your-logic-apps/create-alert-rule.png)

> [!TIP]
> 경고에서 논리 앱을 실행하기 위해 워크플로에서 다음 예제와 같은 작업을 수행할 수 있도록 하는 [요청 트리거](../connectors/connectors-native-reqres.md)를 포함할 수 있습니다.
> 
> * [Slack에 게시](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [텍스트 보내기](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [큐에 메시지 추가](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

<a name="diagnostic-event-properties"></a>

## <a name="azure-diagnostics-event-settings-and-details"></a>Azure Diagnostics 이벤트 설정 및 세부 정보

각 진단 이벤트는 논리 앱 및 해당 이벤트(예: 상태, 시작 시간, 종료 시간 등)에 대한 세부 정보를 포함합니다. 프로그래밍 방식으로 모니터링, 추적 및 로깅을 설정하기 위해 [Azure Logic Apps용 REST API](https://docs.microsoft.com/rest/api/logic) 및 [Azure Diagnostics용 REST API](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows)와 함께 이러한 세부 정보를 사용할 수 있습니다.

예를 들어 `ActionCompleted` 이벤트에는 추적 및 모니터링에 사용할 수 있는 `clientTrackingId` 및 `trackedProperties` 속성이 있습니다.

``` json
{
    "time": "2016-07-09T17:09:54.4773148Z",
    "workflowId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP",
    "resourceId": "/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/MYLOGICAPP/RUNS/08587361146922712057/ACTIONS/HTTP",
    "category": "WorkflowRuntime",
    "level": "Information",
    "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
    "properties": {
        "$schema": "2016-06-01",
        "startTime": "2016-07-09T17:09:53.4336305Z",
        "endTime": "2016-07-09T17:09:53.5430281Z",
        "status": "Succeeded",
        "code": "OK",
        "resource": {
            "subscriptionId": "<subscription-ID>",
            "resourceGroupName": "MyResourceGroup",
            "workflowId": "cff00d5458f944d5a766f2f9ad142553",
            "workflowName": "MyLogicApp",
            "runId": "08587361146922712057",
            "location": "westus",
            "actionName": "Http"
        },
        "correlation": {
            "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
            "clientTrackingId": "<my-custom-tracking-ID>"
        },
        "trackedProperties": {
            "myTrackedProperty": "<value>"
        }
    }
}
```

* `clientTrackingId`: 제공되지 않은 경우 Azure는 자동으로 이 ID를 생성하고 논리 앱에서 호출되는 중첩된 모든 워크플로를 포함하여 논리 앱 실행 간의 이벤트를 상호 연결합니다. 트리거 요청에서 사용자 지정 ID 값과 함께 `x-ms-client-tracking-id` 헤더를 전달하여 트리거에서 직접 이 ID를 지정할 수 있습니다. 요청 트리거, HTTP 트리거 또는 웹후크 트리거를 사용할 수 있습니다.

* `trackedProperties`: 진단 데이터에서 입력 또는 출력을 추적하기 위해 추적된 속성을 논리 앱의 JSON 정의에서 작업에 추가할 수 있습니다. 추적된 속성으로 단일 작업의 입력 및 출력만 추적할 수 있지만 이벤트의 `correlation` 속성을 사용하여 실행에 있는 작업 간에 상호 연결할 수 있습니다.

  하나 이상의 속성을 추적하려면 `trackedProperties` 섹션 및 원하는 속성을 작업 정의에 추가합니다. 예를 들어 원격 분석에서 "주문 ID"와 같은 데이터를 추적하려고 한다고 가정합니다.

  ``` json
  "myAction": {
    "type": "http",
    "inputs": {
        "uri": "http://uri",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": "@triggerBody()"
    },
    "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
    }
  }
  ```

## <a name="next-steps"></a>다음 단계

* [논리 앱 배포 및 릴리스 관리용 템플릿 만들기](../logic-apps/logic-apps-create-deploy-template.md)
* [B2B 시나리오 및 엔터프라이즈 통합 팩](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [B2B 메시지 모니터링](../logic-apps/logic-apps-monitor-b2b-message.md)
