---
title: Azure Monitor 경고를 사용하여 복잡한 작업 트리거
description: Azure Monitor 경고를 처리하기 위해 논리 앱 작업을 만드는 방법에 대해 알아봅니다.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: f1e81dca6926ae9f57e428eb1cef761c588a78b6
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029848"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Azure Monitor 경고를 사용하여 복잡한 작업을 트리거하는 방법

이 문서에서는 경고가 발생한 경우 Microsoft Teams에 대화를 만들도록 논리 앱을 설정하고 트리거하는 방법을 보여 줍니다.

## <a name="overview"></a>개요

Azure Monitor 경고가 트리거되면 [동작 그룹](./action-groups.md)을 호출합니다. 작업 그룹을 사용하면 다른 사람들에게 경고를 알리도록 하나 이상의 작업을 트리거할 수 있으며, 수정도 가능합니다.

일반적인 절차는 다음과 같습니다.

-   각 경고 유형에 대한 논리 앱을 만듭니다.

-   해당 경고 유형에 대한 샘플 페이로드를 논리 앱으로 가져옵니다.

-   논리 앱 동작을 정의합니다.

-   Azure 작업 그룹에 HTTP 엔드포인트 논리 앱을 복사합니다.

논리 앱에서 다른 작업을 수행하려는 경우 프로세스는 비슷합니다.

## <a name="create-an-activity-log-alert-administrative"></a>활동 로그 경고 만들기 - 관리

1. [논리 앱 만들기](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

1.  트리거 선택: **HTTP 요청을 받은 경우**.

1. **HTTP 요청을 수신한 경우** 대화 상자에서 **샘플 페이로드를 사용하여 스키마 생성** 을 선택합니다.

    ![HTTP 요청 대화 상자와 샘플 페이로드를 사용하여 선택한 스키마 옵션을 생성하는 경우를 보여주는 스크린샷. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

1.  다음과 같은 샘플 페이로드를 복사하여 대화 상자에 붙여넣습니다.

    ```json
        {
            "schemaId&quot;: &quot;Microsoft.Insights/activityLogs&quot;,
            &quot;data&quot;: {
                &quot;status&quot;: &quot;Activated&quot;,
                &quot;context&quot;: {
                &quot;activityLog&quot;: {
                    &quot;authorization&quot;: {
                    &quot;action&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;scope&quot;: &quot;/subscriptions/…&quot;
                    },
                    &quot;channels&quot;: &quot;Operation&quot;,
                    &quot;claims&quot;: &quot;…&quot;,
                    &quot;caller&quot;: &quot;logicappdemo@contoso.com&quot;,
                    &quot;correlationId&quot;: &quot;91ad2bac-1afa-4932-a2ce-2f8efd6765a3&quot;,
                    &quot;description&quot;: &quot;&quot;,
                    &quot;eventSource&quot;: &quot;Administrative&quot;,
                    &quot;eventTimestamp&quot;: &quot;2018-04-03T22:33:11.762469+00:00&quot;,
                    &quot;eventDataId&quot;: &quot;ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0&quot;,
                    &quot;level&quot;: &quot;Informational&quot;,
                    &quot;operationName&quot;: &quot;microsoft.insights/activityLogAlerts/write&quot;,
                    &quot;operationId&quot;: &quot;61f59fc8-1442-4c74-9f5f-937392a9723c&quot;,
                    &quot;resourceId&quot;: &quot;/subscriptions/…&quot;,
                    &quot;resourceGroupName&quot;: &quot;LOGICAPP-DEMO&quot;,
                    &quot;resourceProviderName&quot;: &quot;microsoft.insights&quot;,
                    &quot;status&quot;: &quot;Succeeded&quot;,
                    &quot;subStatus&quot;: &quot;&quot;,
                    &quot;subscriptionId&quot;: &quot;…&quot;,
                    &quot;submissionTimestamp&quot;: &quot;2018-04-03T22:33:36.1068742+00:00&quot;,
                    &quot;resourceType&quot;: &quot;microsoft.insights/activityLogAlerts&quot;
                }
                },
                &quot;properties&quot;: {}
            }
        }
    ```

1. **Logic Apps 디자이너** 는 논리 앱에 전송된 요청이 **Content-Type** 헤더를 **application/json** 으로 설정해야 함을 사용자에게 알리는 팝업 창을 표시합니다. 팝업 창을 닫습니다. Azure Monitor 경고는 헤더를 설정합니다.

    ![Content-Type 헤더 설정](media/action-groups-logic-app/content-type-header.png &quot;Content-Type 헤더 설정")

1. **+** **새 단계** 를 선택한 다음, **작업 추가** 를 선택합니다.

    ![작업 추가](media/action-groups-logic-app/add-action.png "작업 추가")

1. Microsoft Teams 커넥터를 검색하고 선택합니다. **Microsoft Teams - 메시지 게시** 작업을 선택합니다.

    ![Microsoft Teams 작업](media/action-groups-logic-app/microsoft-teams-actions.png "Microsoft Teams 작업")

1. Microsoft Teams 작업을 구성합니다. **Logic Apps 디자이너** 에서 회사 또는 학교 계정에 인증할 것을 요청합니다. 메시지를 전송할 **팀 ID** 및 **채널 ID** 를 선택합니다.

13. 동적 콘텐츠의 \<fields\>에 정적 텍스트 및 참조의 조합을 사용하여 메시지를 구성합니다. 다음 텍스트를 잘라내어 **메시지** 필드에 붙여넣습니다.

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    그런 다음, \<fields\>를 검색하여 동일한 이름의 동적 콘텐츠 태그로 바꿉니다.

    > [!NOTE]
    > **상태** 라는 두 개의 동적 필드가 있습니다. 메시지에 이러한 필드를 모두 추가합니다. **activityLog** 속성 모음에서 하나를 사용하고 다른 필드는 삭제합니다. **상태** 필드 위로 커서를 가져가면 다음 스크린샷에 표시된 것과 같이 정규화된 필드 참조가 나타납니다.

    ![Microsoft Teams 작업: 메시지 게시](media/action-groups-logic-app/teams-action-post-message.png "Microsoft Teams 작업: 메시지 게시")

1. **Logic Apps 디자이너** 맨 위에서 **저장** 을 선택하여 논리 앱을 저장합니다.

1. 기존 작업 그룹을 열고 논리 앱을 참조하는 작업을 추가합니다. 기존 작업 그룹이 없으면 [Azure Portal에서 작업 그룹 만들기 및 관리](./action-groups.md)를 참조하세요. 변경 내용은 반드시 저장해야 합니다.

    ![작업 그룹 업데이트](media/action-groups-logic-app/update-action-group.png "작업 그룹 업데이트")

다음에 경고가 사용자의 작업 그룹을 호출하면 논리 앱이 호출됩니다.

## <a name="create-a-service-health-alert"></a>서비스 상태 경고 만들기

Azure Service Health 항목은 작업 로그에 속해 있습니다. 경고 만들기 프로세스는 몇 가지 변경을 제외하고는 [활동 로그 경고 만들기](#create-an-activity-log-alert-administrative)와 비슷합니다.

- 1-3단계는 동일합니다.
- 4단계의 경우 HTTP 요청 트리거에 대한 다음 샘플 페이로드를 사용합니다.

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  5-6단계는 동일합니다.
-  7-11단계의 경우 다음 프로세스를 사용합니다.

   1. **+** **새 단계** 를 선택한 다음, **조건 추가** 를 선택합니다. 입력 데이터가 아래 값과 일치하는 경우에만 논리 앱을 실행하도록 다음 조건을 설정합니다.  버전 값을 텍스트 상자에 입력할 때 숫자 형식이 아닌 문자열로 계산되도록 주위에 따옴표를 배치합니다("0.1.1").  페이지에 반환하는 경우 시스템은 따옴표를 표시하지 않지만 기본 코드는 여전히 문자열 형식을 유지 관리합니다.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Service Health 페이로드 조건"](media/action-groups-logic-app/service-health-payload-condition.png "Service Health 페이로드 조건")

   1. **if true** 조건에서 [활동 로그 경고 만들기](#create-an-activity-log-alert-administrative)의 11-13 단계 지침에 따라 Microsoft Teams 작업을 추가합니다.

   1. HTML과 동적 콘텐츠의 조합을 사용하여 메시지를 정의합니다. 다음 콘텐츠를 복사하여 **메시지** 필드에 붙여넣습니다. `[incidentType]`, `[trackingID]`, `[title]` 및 `[communication]` 필드를 동일한 이름의 동적 콘텐츠 태그로 바꿉니다.

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Service Health true 조건 게시 작업"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Service Health true 조건 게시 작업")

   1. **If false** 조건의 경우 유용한 메시지를 제공합니다.

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Service Health false 조건 게시 작업"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Service Health false 조건 게시 작업")

- 15단계는 동일합니다. 지침을 따라 논리 앱을 저장하고 작업 그룹을 업데이트합니다.

## <a name="create-a-metric-alert"></a>메트릭 경고 만들기

메트릭 경고 만들기 프로세스는 몇 가지 변경을 제외하고는 [활동 로그 경고 만들기](#create-an-activity-log-alert-administrative)와 비슷합니다.

- 1-3단계는 동일합니다.
- 4단계의 경우 HTTP 요청 트리거에 대한 다음 샘플 페이로드를 사용합니다.

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- 5-6단계는 동일합니다.
- 7-11단계의 경우 다음 프로세스를 사용합니다.

  1. **+** **새 단계** 를 선택한 다음, **조건 추가** 를 선택합니다. 입력 데이터가 아래 값과 일치하는 경우에만 논리 앱을 실행하도록 다음 조건을 설정합니다. 버전 값을 텍스트 상자에 입력할 때 숫자 형식이 아닌 문자열로 계산되도록 주위에 따옴표를 배치합니다("2.0").  페이지에 반환하는 경우 시스템은 따옴표를 표시하지 않지만 기본 코드는 여전히 문자열 형식을 유지 관리합니다. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["메트릭 경고 페이로드 조건"](media/action-groups-logic-app/metric-alert-payload-condition.png "메트릭 경고 페이로드 조건")

  1. **if true** 조건에서 **For each** 루프 및 Microsoft Teams 작업을 추가합니다. HTML과 동적 콘텐츠의 조합을 사용하여 메시지를 정의합니다.

      !["메트릭 경고 true 조건 게시 작업"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "메트릭 경고 true 조건 게시 작업")

  1. **If false** 조건에서는 메트릭 경고가 논리 앱의 예상과 일치하지 않는다고 통신하는 Microsoft Teams 작업을 정의합니다. JSON 페이로드를 포함합니다. `json()` 식에서 `triggerBody` 동적 콘텐츠를 참조하는 방법을 확인합니다.

      !["메트릭 경고 false 조건 게시 작업"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "메트릭 경고 false 조건 게시 작업")

- 15단계는 동일합니다. 지침을 따라 논리 앱을 저장하고 작업 그룹을 업데이트합니다.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Microsoft Teams 외에 다른 애플리케이션 호출
Logic Apps는 방대한 애플리케이션 및 데이터베이스에서 작업을 트리거할 수 있는 다양한 커넥터를 제공합니다. Slack, SQL Server, Oracle, Salesforce 등을 예로 들 수 있습니다. 커넥터에 대한 자세한 내용은 [논리 앱 커넥터](../../connectors/apis-list.md)를 참조하세요.  

## <a name="next-steps"></a>다음 단계
* [Azure 활동 로그 경고의 개요](./alerts-overview.md)를 확인하고 경고를 받는 방법에 대해 알아보세요.  
* [Azure Service Health 알림이 게시될 때 경고를 구성](../../service-health/alerts-activity-log-service-notifications-portal.md)하는 방법을 알아보세요.
* [작업 그룹](./action-groups.md)에 대해 자세히 알아보세요.
