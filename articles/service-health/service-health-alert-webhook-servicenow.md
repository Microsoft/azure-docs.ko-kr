---
title: ServiceNow를 사용하여 Azure 서비스 상태 경고 구성 | Microsoft Docs
description: 서비스 상태 이벤트에 대한 개인 설정 알림을 ServiceNow 인스턴스로 가져옵니다.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: f17215a5695128bf2ea507efa0c12fdbba9467d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620943"
---
# <a name="configure-service-health-alerts-with-servicenow"></a>ServiceNow를 사용하여 서비스 상태 경고 구성

이 문서에서는 웹후크를 사용하여 ServiceNow와 Azure 서비스 상태 경고를 통합하는 방법을 보여 줍니다. ServiceNow 인스턴스로 웹후크 통합을 설정한 후 Azure 서비스 문제에 영향을 받는 경우 기존 알림 인프라를 통해 경고를 받습니다. Azure 서비스 상태 경고가 발생할 때마다 ServiceNow의 스크립팅된 REST API를 통해 웹후크를 호출합니다.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>ServiceNow에서 스크립팅된 REST API 만들기
1.  [ServiceNow](https://www.servicenow.com/) 계정에 등록하고 로그인했는지 확인합니다.

1.  ServiceNow에서 **시스템 웹 서비스** 섹션으로 이동하여 **스크립팅된 REST API**를 선택합니다.

    ![ServiceNow에서 "스크립팅된 웹 서비스" 섹션](./media/webhook-alerts/servicenow-sws-section.png)

1.  **새로 만들기**를 선택하여 스크립팅된 새 REST 서비스를 만듭니다.
 
    ![ServiceNow에서 "스크립팅된 새 REST API" 단추](./media/webhook-alerts/servicenow-new-button.png)

1.  REST API에 **이름**을 추가하고 **API ID**를 `azureservicehealth`로 설정합니다.

1.  **제출**을 선택합니다.

    ![ServiceNow에서 "REST API 설정"](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  만든 REST API를 선택하고 **리소스** 탭에서 **새로 만들기**를 선택합니다.

    ![ServiceNow에서 "리소스 탭"](./media/webhook-alerts/servicenow-resources-tab.png)

1.  새 리소스 `event`의 **이름**을 지정하고 **HTTP 메서드**를 `POST`로 변경합니다.

1.  **스크립트** 섹션에서 다음 JavaScript 코드를 추가합니다.

    >[!NOTE]
    >아래 스크립트에서 `<secret>`,`<group>` 및 `<email>` 값을 업데이트해야 합니다.
    >* `<secret>`는 GUID와 같은 임의 문자열이어야 합니다.
    >* `<group>`은 인시던트를 할당할 ServiceNow 그룹이어야 합니다.
    >* `<email>`은 인시던트를 할당할 특정 사용자여야 합니다(선택 사항).
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  보안 탭에서 **인증 필요**를 선택 취소하고 **제출**을 선택합니다. 대신 설정한 `<secret>`로 이 API를 보호합니다.

    ![ServiceNow에서 "인증 필요" 확인란](./media/webhook-alerts/servicenow-resource-settings.png)

1.  다시 스크립팅된 REST API 섹션에서 새 REST API의 **기본 API 경로**를 찾아야 합니다.

     ![ServiceNow에서 "기본 API 경로"](./media/webhook-alerts/servicenow-base-api-path.png)

1.  전체 통합 URL은 다음과 같습니다.
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Azure Portal에서 ServiceNow를 사용하여 경고 만들기
### <a name="for-a-new-action-group"></a>새 작업 그룹의 경우:
1. [이 문서](../azure-monitor/platform/alerts-activity-log-service-notifications.md)의 1~8단계를 수행하여 새 작업 그룹으로 경고를 만듭니다.

1. **작업** 목록에서 다음을 정의합니다.

    a. **작업 유형:** *웹후크*

    b. **세부 정보:** 이전에 저장한 ServiceNow **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

1. 경고 만들기가 완료되면 **저장**을 선택합니다.

### <a name="for-an-existing-action-group"></a>기존 작업 그룹의 경우:
1. [Azure Portal](https://portal.azure.com/)에서 **모니터**를 선택합니다.

1. **설정** 섹션에서 **작업 그룹**을 선택합니다.

1. 편집하려는 작업 그룹을 찾아 선택합니다.

1. **작업** 목록에 다음을 추가합니다.

    a. **작업 유형:** *웹후크*

    b. **세부 정보:** 이전에 저장한 ServiceNow **통합 URL**입니다.

    다. **이름:** 웹후크의 이름, 별칭 또는 식별자입니다.

1. 작업 그룹 업데이트가 완료되면 **저장**을 선택합니다.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>HTTP POST 요청을 통해 웹후크 통합 테스트
1. 보낼 서비스 상태 페이로드를 만듭니다. [Azure 활동 로그 경고에 대한 웹후크](../azure-monitor/platform/activity-log-alerts-webhook.md)에서 예제 서비스 상태 웹후크 페이로드를 찾을 수 있습니다.

1. 다음과 같이 HTTP POST 요청을 만듭니다.

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. "인시던트가 생성되었습니다" 메시지와 함께 `200 OK` 응답이 표시됩니다.

1. [ServiceNow](https://www.servicenow.com/)로 이동하여 통합이 성공적으로 설정되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계
- [기존 문제 관리 시스템에 대한 웹후크 알림 구성](service-health-alert-webhook-guide.md) 방법에 대해 알아봅니다.
- [활동 로그 경고 웹후크 스키마](../azure-monitor/platform/activity-log-alerts-webhook.md)를 검토하세요. 
- [서비스 상태 알림](../azure-monitor/platform/service-notifications.md)에 대해 자세히 알아보세요.
- [작업 그룹](../azure-monitor/platform/action-groups.md)에 대해 자세히 알아보세요.