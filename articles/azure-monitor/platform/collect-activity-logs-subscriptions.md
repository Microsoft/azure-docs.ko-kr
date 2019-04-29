---
title: Log Analytics 작업 영역으로 구독 전체의 Azure 활동 로그를 수집 | Microsoft Docs
description: Event Hubs 및 Logic Apps를 사용 하 여 Azure 활동 로그에서 데이터를 수집 하 여 다른 테 넌 트에서 Azure Monitor에서 Log Analytics 작업 영역으로 보낼 수 있습니다.
services: log-analytics, logic-apps, event-hubs
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 76a789bd4f9167344fcc240e3f3fdbf3a00aa5a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742683"
---
# <a name="collect-azure-activity-logs-into-a-log-analytics-workspace-across-subscriptions-in-different-azure-active-directory-tenants"></a>Log Analytics 작업 영역으로 다른 Azure Active Directory 테 넌 트에서 구독 전체의 Azure 활동 로그를 수집

이 문서는 Azure Log Analytics Data Collector 커넥터를 사용 하 여 Logic Apps에 대 한 Azure Monitor에서 Log Analytics 작업 영역으로 Azure 활동 로그를 수집 하는 방법 단계별로 안내 합니다. 다른 Azure Active Directory 테넌트의 작업 영역에 로그를 보내야 하는 경우 이 문서의 과정을 사용합니다. 예를 들어 관리형 서비스 공급자인 경우 고객의 구독에서 활동 로그를 수집하여 자체 구독의 Log Analytics 작업 영역에 저장하는 것이 좋습니다.

Log Analytics 작업 영역이 동일한 Azure 구독에 있거나 동일한 Azure Active Directory의 다른 구독에 있는 경우 [Azure 활동 로그 솔루션](collect-activity-logs.md)의 단계에 따라 Azure 활동 로그를 수집합니다.

## <a name="overview"></a>개요

이 시나리오에서 사용된 전략은 Azure 활동 로그가 [이벤트 허브](../../event-hubs/event-hubs-about.md)에 이벤트를 보내게 하고, 여기서 [Logic App](../../logic-apps/logic-apps-overview.md)이 이벤트를 Log Analytics 작업 영역으로 보내는 것입니다. 

![Log Analytics 작업 영역에 활동 로그에서 데이터 흐름 이미지](media/collect-activity-logs-subscriptions/data-flow-overview.png)

이 접근 방법의 이점은 다음과 같습니다.
- Azure 활동 로그가 이벤트 허브로 스트리밍된 이후 낮은 대기 시간.  논리 앱은 다음 트리거되고 작업 영역에 데이터를 게시 합니다. 
- 최소 코드가 필요하며, 배포할 서버 인프라가 없습니다.

이 문서에서는 다음 방법을 안내합니다.
1. 이벤트 허브를 만듭니다. 
2. Azure 활동 로그 내보내기 프로필을 사용하여 활동 로그를 이벤트 허브로 내보냅니다.
3. Log Analytics 작업 영역에 이벤트를 보내고 이벤트 허브에서 읽을 수 있는 논리 앱을 만듭니다.

## <a name="requirements"></a>요구 사항
다음은 이 시나리오에서 사용되는 Azure 리소스에 대한 요구 사항입니다.

- Event Hub 네임스페이스는 로그를 내보내는 구독과 동일한 구독에 있지 않아도 됩니다. 설정을 구성하는 사용자에게 두 구독에 대한 적절한 액세스 권한이 있어야 합니다. 동일한 Azure Active Directory에 여러 구독이 있는 경우 모든 구독에 대한 활동 로그를 단일 이벤트 허브로 보낼 수 있습니다.
- Logic App은 이벤트 허브와 다른 구독에 있을 수 있으며, 동일한 Azure Active Directory에 있지 않아도 됩니다. Logic App은 이벤트 허브의 공유 액세스 키를 사용하여 이벤트 허브에서 읽습니다.
- Log Analytics 작업 영역은 Logic App과 다른 구독 및 Azure Active Directory에 있을 수 있지만 설명하기 쉽게 동일한 구독에 있는 것이 좋습니다. 논리 앱을 Log Analytics 작업 영역 ID 및 키를 사용 하 여 작업 영역에 보냅니다.



## <a name="step-1---create-an-event-hub"></a>1단계 - 이벤트 허브 만들기

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Azure Portal에서 **리소스 만들기** > **사물 인터넷** > **Event Hubs**를 선택합니다.

   ![마켓플레이스에 새 이벤트 허브 출시](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. **네임스페이스 만들기** 아래에서 새 네임스페이스를 입력하거나 기존 네임스페이스를 선택합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.

   ![이벤트 허브 만들기 대화 상자 이미지](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. 새 리소스의 가격 책정 계층(기본 또는 표준), Azure 구독, 리소스 그룹 및 위치를 선택합니다.  **만들기** 를 클릭하여 네임스페이스를 만듭니다. 시스템에서 리소스를 완전히 프로비전하기까지 몇 분 동안 기다려야 할 수 있습니다.
6. 목록에서 방금 만든 네임스페이스를 클릭합니다.
7. **공유 액세스 정책**을 선택하고 **RootManageSharedAccessKey**를 클릭합니다.

   ![이벤트 허브 공유 액세스 정책 이미지](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. 복사 단추를 클릭하여 클립보드에 대한 **RootManageSharedAccessKey** 연결 문자열을 복사합니다. 

   ![이벤트 허브 공유 액세스 키 이미지](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. 메모장과 같은 임시 위치에 이벤트 허브 이름과 기본 또는 보조 이벤트 허브 연결 문자열의 사본을 유지합니다. Logic App에는 이러한 값이 필요합니다.  이벤트 허브 연결 문자열의 경우 **RootManageSharedAccessKey** 연결 문자열을 사용하거나 별도의 연결 문자열을 만들 수 있습니다.  사용하는 연결 문자열은 `Endpoint=sb://`로 시작해야 하며 **관리** 액세스 정책이 있는 정책에 대한 것이어야 합니다.


## <a name="step-2---export-activity-logs-to-event-hub"></a>2단계 - 활동 로그를 이벤트 허브로 내보내기

활동 로그의 스트리밍을 사용하려면 해당 네임스페이스에 대한 이벤트 허브 네임스페이스 및 공유 액세스 정책을 선택합니다. 첫 번째 새 활동 로그 이벤트가 발생하면 해당 네임스페이스에 이벤트 허브가 생성됩니다. 

로그를 내보내는 구독과 동일한 구독에 없는 이벤트 허브 네임스페이스를 사용할 수 있지만 구독은 동일한 Azure Active Directory에 있어야 합니다. 설정을 구성하는 사용자에게 두 구독에 대한 적절한 RBAC 액세스 권한이 있어야 합니다. 

1. Azure Portal에서 **모니터** > **활동 로그**를 선택합니다.
3. 페이지 맨 위에서 **내보내기** 단추를 클릭합니다.

   ![탐색의 Azure Monitor 이미지](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. 내보낼 **구독**을 선택한 다음 **지역** 드롭다운에서 **모두 선택**을 클릭하여 모든 지역의 리소스에 대한 이벤트를 선택합니다. **이벤트 허브로 내보내기**를 클릭합니다.
7. **서비스 버스 네임스페이스**를 클릭하고 이벤트 허브, **이벤트 허브 네임스페이스** 및 **이벤트 허브 정책 이름**이 있는 **구독**을 선택합니다.

    ![이벤트 허브 페이지로 내보내기 이미지](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. **확인**, **저장**을 차례로 클릭하여 이러한 설정을 저장합니다. 해당 설정이 구독에 즉시 적용됩니다.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>3단계 - Logic App 만들기

활동 로그를 이벤트 허브에 작성 되 면 이벤트 허브에서 로그를 수집 하 고 Log Analytics 작업 영역에 작성 하는 논리 앱 만들기

Logic App에는 다음이 포함됩니다.
- 이벤트 허브에서 읽기 위한 [이벤트 허브 커넥터](https://docs.microsoft.com/connectors/eventhubs/) 트리거
- JSON 이벤트를 추출하기 위한 [JSON 구문 분석 작업](../../logic-apps/logic-apps-content-type.md)
- JSON을 개체로 변환하기 위한 [작성 작업](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)
- A [Log Analytics 데이터 보내기 커넥터](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) Log Analytics 작업 영역에 데이터를 게시 합니다.

   ![Logic Apps에서 이벤트 허브 트리거 추가 이미지](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Logic App 요구 사항
Logic App을 만들기 전에 이전 단계에서 다음 정보가 있는지 확인합니다.
- 이벤트 허브 이름
- 이벤트 허브 네임스페이스에 대한 이벤트 허브 연결 문자열(기본 또는 보조)
- Log Analytics 작업 영역 ID
- Log Analytics 공유 키

이벤트 허브 이름과 연결 문자열을 가져오려면 [Event Hubs 네임스페이스 권한 확인 및 연결 문자열 찾기](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string)의 단계를 따르세요.


### <a name="create-a-new-blank-logic-app"></a>새 빈 Logic App 만들기

1. Azure Portal에서 **리소스 만들기** > **엔터프라이즈 통합** > **Logic App**을 선택합니다.

    ![마켓플레이스에 새 Logic App 출시](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. 아래 표에 있는 설정을 입력합니다.

    ![논리 앱 만들기](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |설정 | 설명  |
   |:---|:---|
   | 이름           | Logic App의 고유 이름입니다. |
   | 구독   | Logic App이 포함될 Azure 구독을 선택합니다. |
   | 리소스 그룹 | 기존 Azure 리소스 그룹을 선택하거나 Logic App에 사용할 리소스 그룹을 새로 만듭니다. |
   | 위치       | 논리 앱을 배포하기 위한 데이터 센터 지역을 선택합니다. |
   | Log Analytics  | Log Analytics 작업 영역에서 논리 앱의 각 실행의 상태를 기록 하려는 경우 선택 합니다.  |

    
3. **만들기**를 선택합니다. **배포 성공** 알림이 표시되면 **리소스로 이동**을 클릭하여 Logic App을 엽니다.

4. **템플릿** 아래에서 **빈 Logic App**을 선택합니다. 

이제 Logic Apps 디자이너에 사용 가능한 커넥터 및 해당 트리거가 표시됩니다. 이는 Logic App 워크플로를 시작하는 데 사용합니다.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>이벤트 허브 트리거 추가

1. Logic App 디자이너의 검색 상자에서 필터에 *이벤트 허브*를 입력합니다. **Event Hubs - 이벤트 허브에서 이벤트를 사용할 수 있는 경우** 트리거를 선택합니다.

   ![Logic Apps에서 이벤트 허브 트리거 추가 이미지](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. 자격 증명을 묻는 메시지가 표시되면 Event Hubs 네임스페이스에 연결합니다. 연결 이름과 복사한 연결 문자열을 입력합니다.  **만들기**를 선택합니다.

   ![Logic Apps에서 이벤트 허브 연결 추가 이미지](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. 연결을 만든 후 트리거 설정을 편집합니다. 먼저 **이벤트 허브 이름** 드롭다운에서 **insights-operational-logs**를 선택합니다.

   ![이벤트를 사용할 수 있는 경우 대화 상자](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. **고급 옵션 표시**를 확장하고 **콘텐츠 형식**을 *application/json*으로 변경합니다.

   ![이벤트 허브 구성 대화 상자](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>JSON 구문 분석 작업 추가

이벤트 허브의 출력에는 JSON 페이로드가 레코드 배열과 함께 포함됩니다. 합니다 [JSON 구문 분석](../../logic-apps/logic-apps-content-type.md) 작업은 Log Analytics 작업 영역으로 보낼 레코드 배열만 추출 하는 데 사용 됩니다.

1. **새 단계** > **작업 추가**를 클릭합니다.
2. 검색 상자에서 필터에 *JSON 구문 분석*을 입력합니다. **데이터 작업 - JSON 구문 분석** 작업을 선택합니다.

   ![Logic Apps에서 JSON 구문 분석 작업 추가](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. **콘텐츠** 필드 안을 클릭하고 *본문*을 선택합니다.

4. 다음 스키마를 복사하여 **스키마** 필드에 붙여넣습니다.  이 스키마는 이벤트 허브 작업의 출력과 일치합니다.  

   ![JSON 구문 분석 대화 상자](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> **실행**을 클릭하고 이벤트 허브의 **원시 출력**을 확인하여 샘플 페이로드를 가져올 수 있습니다.  그런 다음 **JSON 구문 분석** 활동의 **샘플 페이로드를 사용하여 스키마 생성**과 함께 이 출력을 사용하여 스키마를 생성할 수 있습니다.

### <a name="add-compose-action"></a>작성 작업 추가
[작성](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) 작업은 JSON 출력을 사용하고 Log Analytics 작업에 사용될 수 있는 개체를 만듭니다.

1. **새 단계** > **작업 추가**를 클릭합니다.
2. 필터에 ‘작성’을 입력하고 **데이터 작업 - 작성** 작업을 선택합니다.

    ![작성 작업 추가](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. **입력** 필드를 클릭하고 **JSON 구문 분석** 아래의 **본문**을 선택합니다.


### <a name="add-log-analytics-send-data-action"></a>Log Analytics 데이터 보내기 작업 추가
합니다 [Azure Log Analytics Data Collector](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) 작업 작성 작업에서 개체를 사용 하 고 Log Analytics 작업 영역에 보냅니다.

1. **새 단계** > **작업 추가**를 클릭합니다.
2. 필터에 *로그 분석*을 입력한 다음, **Azure Log Analytics Data Collector - 데이터 보내기** 작업을 선택합니다.

   ![논리 앱에서 로그 분석 데이터 보내기 작업 추가](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. 연결 이름을 입력하고 Log Analytics 작업 영역의 **작업 영역 ID** 및 **작업 영역 키**를 붙여넣습니다.  **만들기**를 클릭합니다.

   ![논리 앱에서 로그 분석 연결 추가](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. 연결을 만든 후 아래 표에 있는 설정을 편집합니다. 

    ![데이터 보내기 작업 구성](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |설정        | 값           | 설명  |
   |---------------|---------------------------|--------------|
   |JSON 요청 본문  | **작성** 작업의 **출력** | 작성 작업의 본문에서 레코드를 검색합니다. |
   | 사용자 지정 로그 이름 | AzureActivity | 가져온된 데이터를 저장할 Log Analytics 작업 영역에서 만드는 사용자 지정 로그 테이블의 이름입니다. |
   | Time-generated-field | 실시간 | **time**에 대해 JSON 필드를 선택하지 않고 time 단어만 입력합니다. JSON 필드를 선택하면 디자이너가 **데이터 보내기** 작업을 *For Each* 루프에 넣으며, 이는 원하는 작업이 아닙니다. |




10. **저장**을 클릭하여 Logic App에 대한 변경 내용을 저장합니다.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>4단계 - 문제 해결 테스트 및 문제 해결
워크플로가 완료되면 디자이너에서 테스트하여 오류 없이 작동하는지 확인할 수 있습니다.

Logic App 디자이너에서 **실행**을 클릭하여 Logic App을 테스트합니다. Logic App의 각 단계에 상태 아이콘이 표시되며, 녹색 원에 흰색 확인 표시가 있으면 성공을 나타냅니다.

   ![Logic App 테스트](media/collect-activity-logs-subscriptions/test-logic-app.png)

각 단계에 대한 자세한 정보를 보려면 단계 이름을 클릭하여 확장합니다. **원시 입력 표시** 및 **원시 출력 표시**를 클릭하여 각 단계에서 보내고 받은 데이터에 대한 자세한 정보를 확인합니다.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>5단계 - Log Analytics에서 Azure 활동 로그 보기
최종 단계는 Log Analytics 작업 영역을 검사하여 데이터가 예상대로 수집되는지 확인하는 것입니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 작업 영역 목록에서 작업 영역을 선택합니다.
3.  **로그 검색** 타일을 클릭하고 로그 검색 창의 쿼리 필드에 `AzureActivity_CL`를 입력한 후 Enter 키를 누르거나 쿼리 필드의 오른쪽의 검색 단추를 클릭합니다. 사용자 지정 로그의 이름을 *AzureActivity*로 지정하지 않은 경우 선택한 이름을 입력하고 `_CL`을 추가합니다.

>[!NOTE]
> 처음으로 새 사용자 지정 로그를 Log Analytics 작업 영역으로 전송 됩니다 걸릴 수 있습니다 검색할 수 있는 반면 사용자 지정 로그에 대 한 1 시간.

>[!NOTE]
> 활동 로그는 사용자 지정 테이블에 기록되며 [활동 로그 솔루션](./collect-activity-logs.md)에 표시되지 않습니다.


![Logic App 테스트](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 이벤트 허브에서 Azure 활동 로그를 읽고 분석을 위해 Log Analytics 작업 영역으로 보내도록 logic app을 만들었습니다. 대시보드 만들기를 포함 하는 작업 영역에서 데이터를 시각화에 대해 자세히 알아보려면 데이터 시각화에 대 한 자습서를 검토 합니다.

> [!div class="nextstepaction"]
> [로그 검색 데이터 시각화 자습서](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
