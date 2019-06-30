---
title: 관리 솔루션에 저장 된 검색 | Microsoft Docs
description: 관리 솔루션은 일반적으로 솔루션에서 수집한 데이터를 분석하기 위해 Log Analytics에 저장된 검색을 포함하게 됩니다. 또한 중요한 문제에 대한 응답으로 사용자에게 알리거나 자동으로 조치를 취하기 위한 경고를 정의합니다. 이 문서에서는 Log Analytics 관리 솔루션에 포함 될 수 있도록 Resource Manager 템플릿에서 저장 검색을 정의 하는 방법을 설명 합니다.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0975b23a8f96da6fc2dfcc8bd9ad046847a68aa9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104833"
---
# <a name="adding-log-analytics-saved-searches-and-alerts-to-management-solution-preview"></a>관리 솔루션(미리 보기)에 Log Analytics에서 저장한 검색 및 경고 추가

> [!IMPORTANT]
> [Log Analytics 경고가 Azure Monitor로 확장되었으므로](../platform/alerts-extend.md) Resource Manager 템플릿을 사용하여 경고를 만드는 데 필요한 세부 정보는 이제 유효 기간이 지났습니다. Resource Manager 템플릿을 사용하여 로그 경고를 만드는 방법에 대한 자세한 내용은 [Azure 리소스 템플릿을 사용하여 로그 경고 관리](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template)를 참조합니다.

> [!NOTE]
> 현재 Preview로 제공되는 관리 솔루션 만들기에 대한 예비 설명서입니다. 아래 설명된 스키마는 변경될 수 있습니다.

[관리 솔루션](solutions.md)은 일반적으로 솔루션에서 수집한 데이터를 분석하기 위해 Log Analytics에 [저장된 검색](../../azure-monitor/log-query/log-query-overview.md)을 포함하게 됩니다. 또한 중요한 문제에 대한 응답으로 사용자에게 알리거나 자동으로 조치를 취하기 위한 [경고](../../azure-monitor/platform/alerts-overview.md)를 정의합니다. 이 문서에서는 [관리 솔루션](solutions-creating.md)에 포함되도록 [리소스 관리 템플릿](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)에서 Log Analytics 저장된 검색 및 경고를 정의하는 방법을 설명합니다.

> [!NOTE]
> 이 문서의 샘플에는 관리 솔루션에 필요하거나 공통적이며 [Azure의 관리 솔루션 디자인 및 빌드](solutions-creating.md)에서 설명한 매개 변수와 변수가 사용됩니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 여러분이 [관리 솔루션을 만드는 방법](solutions-creating.md)과 [Resource Manager 템플릿](../../azure-resource-manager/resource-group-authoring-templates.md) 및 솔루션 파일의 구조를 잘 알고 있다고 가정합니다.


## <a name="log-analytics-workspace"></a>Log Analytics 작업 영역
Log Analytics의 모든 리소스는 [작업 영역](../../azure-monitor/platform/manage-access.md)에 포함됩니다. [Log Analytics 작업 영역 및 Automation 계정](solutions.md#log-analytics-workspace-and-automation-account)에서 설명한 대로 작업 영역은 관리 솔루션에 포함되지 않지만, 솔루션이 설치되기 전에 존재해야 합니다. 계정을 사용할 수 없으면 솔루션 설치에 실패합니다.

작업 영역 이름은 각 Log Analytics 리소스의 이름을 사용합니다. 이 작업은 SavedSearch 리소스 예제와 같이 **workspace** 매개 변수가 포함된 솔루션에서 이루어집니다.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Log Analytics API 버전
Resource Manager 템플릿에 정의된 모든 Log Analytics 리소스에는 리소스가 사용해야 하는 API의 버전을 정의하는 **apiVersion** 속성이 있습니다.

다음 표에는 이 예제에서 사용된 리소스의 API 버전이 제공됩니다.

| 리소스 종류 | API 버전 | query |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Event &#124; where EventLevelName == "Error"  |


## <a name="saved-searches"></a>저장된 검색
솔루션에서 수집한 데이터를 사용자가 쿼리할 수 있도록 솔루션에 [저장된 검색](../../azure-monitor/log-query/log-query-overview.md)을 포함합니다. 저장된 검색은 Azure Portal의 **저장된 검색**에 표시됩니다. 각 경고에도 저장된 검색이 필요합니다.

[Log Analytics 및 저장된 검색](../../azure-monitor/log-query/log-query-overview.md) 리소스는 `Microsoft.OperationalInsights/workspaces/savedSearches` 형식을 가지며 구조는 다음과 같습니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

저장된 검색의 각 속성은 다음 표에 설명되어 있습니다.

| 자산 | description |
|:--- |:--- |
| category | 저장된 검색의 범주입니다.  같은 솔루션에 있는 저장된 검색은 종종 단일 범주를 공유하므로 콘솔에서 함께 그룹화됩니다. |
| displayname | 포털에서 저장된 검색에 표시할 이름입니다. |
| query | 실행할 쿼리입니다. |

> [!NOTE]
> JSON으로 해석될 수 있는 문자를 포함하고 있는 경우 쿼리에 이스케이프 문자를 사용해야 합니다. 예를 들어, 쿼리가 **AzureActivity | OperationName:“Microsoft.Compute/virtualMachines/write”** 이면 솔루션 파일에 **AzureActivity | OperationName:/\"Microsoft.Compute/virtualMachines/write\"** 라고 써야 합니다.

## <a name="alerts"></a>경고
[Azure 로그 경고](../../azure-monitor/platform/alerts-unified-log.md)는 일정한 간격으로 지정된 로그 쿼리를 실행하는 Azure Alerts에 의해 생성됩니다. 쿼리 결과가 지정된 기준과 일치하면 경고 레코드가 생성되고 하나 이상의 작업이 [작업 그룹](../../azure-monitor/platform/action-groups.md)을 사용하여 실행됩니다.

> [!NOTE]
> 2018년 5월 14일부터 Log Analytics 작업 영역의 Azure 공용 클라우드 인스턴스에서 발생하는 모든 경고가 Azure로 확장됩니다. 자세한 내용은 [Azure로 경고 확장](../../azure-monitor/platform/alerts-extend.md)을 참조하세요. Azure로 경고를 확장하는 사용자의 경우 작업은 이제 Azure 작업 그룹에서 제어됩니다. 작업 영역 및 해당 경고가 Azure로 확장되는 경우 [작업 그룹 - Azure Resource Manager 템플릿](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)을 사용하여 작업을 검색하거나 추가할 수 있습니다.
관리 솔루션의 경고 규칙은 다음 세 가지 리소스로 구성됩니다.

- **저장된 검색.** 실행될 로그 검색을 정의합니다. 여러 경고 규칙이 하나의 저장된 검색을 공유할 수 있습니다.
- **일정.** 로그 검색이 실행될 빈도를 정의합니다. 각 경고 규칙은 일정을 하나만 갖습니다.
- **경고 작업.** 각 경고 규칙은 경고 레코드가 생성되는 시기, 경고 심각도 등의 경고 세부 정보를 정의하는 **경고** 형식의 작업 그룹 리소스 또는 작업 리소스(레거시) 하나를 갖게 됩니다. [작업 그룹](../../azure-monitor/platform/action-groups.md) 리소스에는 음성 통화, SMS, 이메일, 웹후크, ITSM 도구, 자동화 Runbook, 논리 앱 등 경고가 발생할 때 수행되도록 구성된 작업 목록이 있을 수 있습니다.

작업 리소스(레거시)는 메일 및 Runbook 응답을 선택적으로 정의합니다.
- **웹후크 작업(레거시).** 경고 규칙이 웹후크를 호출하면 **웹후크** 형식의 추가 작업 리소스가 필요합니다.

저장된 검색 리소스는 위에 설명되어 있습니다. 다른 리소스는 아래에 설명되어 있습니다.

### <a name="schedule-resource"></a>일정 리소스

저장된 검색은 하나 이상의 일정을 가질 수 있으며 각 일정은 별도의 경고 규칙을 나타냅니다. 일정은 검색이 실행되는 빈도 및 데이터가 검색되는 시간 간격을 정의합니다. 일정 리소스는 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/` 형식을 가지며 구조는 다음과 같습니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name)]"
        ],
        "properties": {
            "etag": "*",
            "interval": "[variables('Schedule').Interval]",
            "queryTimeSpan": "[variables('Schedule').TimeSpan]",
            "enabled": "[variables('Schedule').Enabled]"
        }
    }
일정 리소스의 속성은 다음 테이블에 설명되어 있습니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| enabled       | 예 | 경고를 만들 때 사용 여부를 지정합니다. |
| interval      | 예 | 쿼리가 실행되는 빈도(분)입니다. |
| queryTimeSpan | 예 | 결과를 평가하는 시간의 길이(분)입니다. |

일정 전에 저장된 검색이 생성되도록 일정 리소스는 저장된 검색에 따라 결정됩니다.
> [!NOTE]
> 일정 이름은 지정된 작업 영역에서 고유해야 합니다. 두 일정이 서로 다른 저장된 검색과 연결되었다 하더라도 동일한 ID를 가질 수 없습니다. 또한 Log Analytics API를 사용하여 만든 저장된 모든 검색, 일정 및 작업의 이름은 소문자여야 합니다.

### <a name="actions"></a>작업
일정이 여러 작업을 가질 수 있습니다. 작업은 메일 보내기 또는 Runbook 시작과 같은 하나 이상의 수행할 프로세스를 정의하거나 검색 결과가 일부 조건과 일치하는 경우를 결정하는 임계값을 정의할 수 있습니다. 일부 작업은 임계값을 만족할 때 프로세스가 수행되도록 정의합니다.
[작업 그룹] 리소스 또는 작업 리소스를 사용하여 작업을 정의할 수 있습니다.
> [!NOTE]
> 2018년 5월 14일부터 Log Analytics 작업 영역의 Azure 공용 클라우드 인스턴스에서 발생하는 모든 경고가 Azure로 자동 확장됩니다. 자세한 내용은 [Azure로 경고 확장](../../azure-monitor/platform/alerts-extend.md)을 참조하세요. Azure로 경고를 확장하는 사용자의 경우 작업은 이제 Azure 작업 그룹에서 제어됩니다. 작업 영역 및 해당 경고가 Azure로 확장되는 경우 [작업 그룹 - Azure Resource Manager 템플릿](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)을 사용하여 작업을 검색하거나 추가할 수 있습니다.
**Type** 속성에서 지정하는 두 가지 형식의 작업 리소스가 있습니다. 일정에는 경고 규칙 세부 정보 그리고 경고가 생성될 때 수행할 작업을 정의하는 **경고** 작업 하나가 필요합니다. 작업 리소스의 형식은 `Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions`입니다.

경고 작업의 구조는 다음과 같습니다. 여기에는 일반 변수 및 매개 변수가 포함되어 있으므로 이 코드 조각을 복사하여 솔루션 파일에 붙여넣고 매개 변수 이름을 변경할 수 있습니다.

```json
{
    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Alert').Name)]",
    "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
    "apiVersion": "[variables('LogAnalyticsApiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
    ],
    "properties": {
        "etag": "*",
        "type": "Alert",
        "name": "[variables('Alert').Name]",
        "description": "[variables('Alert').Description]",
        "severity": "[variables('Alert').Severity]",
        "threshold": {
            "operator": "[variables('Alert').Threshold.Operator]",
            "value": "[variables('Alert').Threshold.Value]",
            "metricsTrigger": {
                "triggerCondition": "[variables('Alert').Threshold.Trigger.Condition]",
                "operator": "[variables('Alert').Trigger.Operator]",
                "value": "[variables('Alert').Trigger.Value]"
            },
        },
        "AzNsNotification": {
            "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
            "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]",
            "CustomWebhookPayload": "[variables('MyAlert').AzNsNotification.CustomWebhookPayload]"
        }
    }
}
```

경고 작업 리소스의 속성은 다음 테이블에 설명되어 있습니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| type | 예 | 작업의 유형입니다.  경고 작업의 **경고**가 됩니다. |
| name | 예 | 경고에 대한 표시 이름입니다.  경고 규칙에 대한 콘솔에 표시되는 이름입니다. |
| description | 아닙니다. | 경고에 대한 선택적 설명입니다. |
| 심각도 | 예 | 다음 값의 경고 레코드의 심각도입니다.<br><br> **중요**<br>**경고**<br>**정보 제공**


#### <a name="threshold"></a>임계값
이 섹션은 필수입니다. 경고 임계값의 속성을 정의합니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| 연산자 | 예 | 다음 값의 비교 연산자입니다.<br><br>**gt = 보다 큼<br>lt = 보다 작음** |
| 값 | 예 | 결과를 비교하는 값입니다. |

##### <a name="metricstrigger"></a>MetricsTrigger
이 섹션은 선택 사항입니다. 미터법 경고에는 이 섹션을 포함해야 합니다.

> [!NOTE]
> 미터법 알림은 현재 공개 미리 보기 상태입니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| TriggerCondition | 예 | 임계값이 총 위반 수인지 아니면 연속 위반인지 다음 값을 사용하여 지정합니다.<br><br>**총<br>연속** |
| 연산자 | 예 | 다음 값의 비교 연산자입니다.<br><br>**gt = 보다 큼<br>lt = 보다 작음** |
| 값 | 예 | 경고를 트리거하기 위해 조건을 충족해야 하는 시간입니다. |


#### <a name="throttling"></a>제한
이 섹션은 선택 사항입니다. 경고가 생성된 후 일정 시간 동안 같은 규칙의 경고를 표시하지 않으려면 이 섹션을 포함해야 합니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| DurationInMinutes | 제한 요소가 포함된 경우 필수입니다. | 같은 경고 규칙에서 경고가 생성되면 이 시간 동안 경고를 표시하지 않습니다. |

#### <a name="azure-action-group"></a>Azure 작업 그룹
Azure에서 모든 경고는 작업을 처리하기 위한 기본 메커니즘으로 작업 그룹을 사용합니다. 작업 그룹을 사용하여 작업을 한 번 지정한 다음, 작업 그룹을 Azure 전체에서 여러 경고에 연결할 수 있습니다. 필요가 없으면, 반복적으로 동일한 작업을 반복하고 다시 선언합니다. 작업 그룹은 이메일, SMS, 음성 통화, ITSM 연결, Automation Runbook, 웹후크 URI 등을 포함하는 여러 작업을 지원합니다.

자신의 경고를 Azure로 확장한 사용자의 경우 일정은 이제 경고를 만들 수 있도록 임계값과 함께 전달된 작업 그룹 세부 정보가 있어야 합니다. 경고를 만들려면 먼저 작업 그룹 내에서 이메일 세부 정보, 웹후크 URL, Runbook Automation 세부 정보 및 기타 작업을 정의해야 합니다. 사용자는 포털에서 [Azure Monitor로 작업 그룹](../../azure-monitor/platform/action-groups.md)을 만들거나 [작업 그룹 - 리소스 템플릿](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)을 사용할 수 있습니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| AzNsNotification | 예 | 경고 조건이 충족되면 필요한 작업을 수행하도록 경고와 연결되는 Azure 작업 그룹의 리소스 ID. |
| CustomEmailSubject | 아닙니다. | 연결된 작업 그룹에서 지정된 모든 주소로 전송되는 메일의 사용자 지정 제목 줄. |
| CustomWebhookPayload | 아닙니다. | 연결된 작업 그룹에 정의된 모든 웹후크 엔드포인트로 보낼 사용자 지정된 페이로드. 형식은 웹후크에서 기대하는 내용에 따라 달라지며 유효한 직렬화 JSON이어야 합니다. |

#### <a name="actions-for-oms-legacy"></a>OMS에 대한 작업(레거시)

모든 일정은 하나의 **경고** 작업을 갖게 됩니다. 이 경고 작업은 경고의 세부 정보를 정의하고 필요에 따라 알림 및 재구성 작업을 정의합니다. 알림은 하나 이상의 주소에 전자 메일을 보냅니다. 재구성은 Azure Automation에서 runbook을 시작하여 검색된 문제 해결을 시도합니다.

> [!NOTE]
> 2018년 5월 14일부터 Log Analytics 작업 영역의 Azure 공용 클라우드 인스턴스에서 발생하는 모든 경고가 Azure로 자동 확장됩니다. 자세한 내용은 [Azure로 경고 확장](../../azure-monitor/platform/alerts-extend.md)을 참조하세요. Azure로 경고를 확장하는 사용자의 경우 작업은 이제 Azure 작업 그룹에서 제어됩니다. 작업 영역 및 해당 경고가 Azure로 확장되는 경우 [작업 그룹 - Azure Resource Manager 템플릿](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)을 사용하여 작업을 검색하거나 추가할 수 있습니다.

##### <a name="emailnotification"></a>EmailNotification
 이 섹션은 선택 사항입니다. 한 명 이상의 수신자에게 메일을 보내 경고하려면 이 섹션을 포함해야 합니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| Recipients | 예 | 다음 예제와 같이 경고가 생성되면 알림을 보낼 쉼표로 구분된 전자 메일 주소 목록입니다.<br><br>**[ "recipient1\@contoso.com", "recipient2\@contoso.com" ]** |
| Subject | 예 | 메일의 제목 줄입니다. |
| Attachment | 아닙니다. | 첨부 파일은 현재 지원되지 않습니다. 이 요소를 포함하는 경우 **없음**이어야 합니다. |

##### <a name="remediation"></a>재구성
이 섹션은 선택 사항입니다. 경고에 대한 응답으로 runbook을 시작하려면 이 섹션을 포함해야 합니다. 

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| RunbookName | 예 | 시작할 runbook의 이름입니다. |
| WebhookUri | 예 | runbook의 웹후크 Uri입니다. |
| Expiry | 아닙니다. | 재구성이 만료되는 날짜 및 시간입니다. |

##### <a name="webhook-actions"></a>웹후크 작업

웹후크 작업은 URL을 호출하고 선택적으로 보낼 페이로드를 제공하는 것으로 프로세스를 시작합니다. 이들은 웹후크에 대해 Azure Automation Runbook 이외의 프로세스를 호출할 수 있다는 것을 제외하고 수정 작업과 유사합니다. 또한 원격 프로세스에 전달할 페이로드를 제공하는 추가 옵션을 제공합니다.

경고에서 웹후크를 호출하는 경우 **경고** 작업 리소스 외에도 **웹후크** 형식의 작업 리소스가 필요합니다.

    {
      "name": "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name, '/', variables('Schedule').Name, '/', variables('Webhook').Name)]",
      "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions/",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('SavedSearch').Name, '/schedules/', variables('Schedule').Name)]"
      ],
      "properties": {
        "etag": "*",
        "type": "[variables('Alert').Webhook.Type]",
        "name": "[variables('Alert').Webhook.Name]",
        "webhookUri": "[variables('Alert').Webhook.webhookUri]",
        "customPayload": "[variables('Alert').Webhook.CustomPayLoad]"
      }
    }
웹후크 작업 리소스의 속성은 다음 표에 설명되어 있습니다.

| 요소 이름 | 필수 | description |
|:--|:--|:--|
| 형식 | 예 | 작업의 유형입니다. 웹후크 작업의 **웹후크**가 됩니다. |
| name | 예 | 작업의 표시 이름입니다. 콘솔에 표시되지 않습니다. |
| webhookUri | 예 | 웹후크의 Uri입니다. |
| customPayload | 아닙니다. | 웹후크에 보낼 사용자 지정 페이로드입니다. 형식은 예상하는 웹후크에 따라 달라집니다. |

## <a name="sample"></a>샘플

아래는 다음 리소스를 포함하는 솔루션의 샘플입니다.

- 저장된 검색
- 일정
- 작업 그룹

이 샘플에서는 리소스 정의의 값을 하드 코딩하는 대신 솔루션에 일반적으로 사용되는 [표준 솔루션 매개 변수]( solutions-solution-file.md#parameters) 변수를 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "Description": "Name of Log Analytics workspace"
            }
        },
        "workspaceregionId": {
            "type": "string",
            "metadata": {
                "Description": "Region of Log Analytics workspace"
            }
        },
        "actiongroup": {
            "type": "string",
            "metadata": {
                "Description": "List of action groups for alert actions separated by semicolon"
            }
        }
    },
    "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
        "LogAnalyticsApiVersion-Search": "2017-03-15-preview",
        "LogAnalyticsApiVersion-Solution": "2015-11-01-preview",
        "MySearch": {
            "displayName": "Error records by hour",
            "query": "MyRecord_CL | summarize AggregatedValue = avg(Rating_d) by Instance_s, bin(TimeGenerated, 60m)",
            "category": "Samples",
            "name": "Samples-Count of data"
        },
        "MyAlert": {
            "Name": "[toLower(concat('myalert-',uniqueString(resourceGroup().id, deployment().name)))]",
            "DisplayName": "My alert rule",
            "Description": "Sample alert. Fires when 3 error records found over hour interval.",
            "Severity": "critical",
            "ThresholdOperator": "gt",
            "ThresholdValue": 3,
            "Schedule": {
                "Name": "[toLower(concat('myschedule-',uniqueString(resourceGroup().id, deployment().name)))]",
                "Interval": 15,
                "TimeSpan": 60
            },
            "MetricsTrigger": {
                "TriggerCondition": "Consecutive",
                "Operator": "gt",
                "Value": 3
            },
            "ThrottleMinutes": 60,
            "AzNsNotification": {
                "GroupIds": [
                    "[parameters('actiongroup')]"
                ],
                "CustomEmailSubject": "Sample alert"
            }
        }
    },
    "resources": [
        {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
            "location": "[parameters('workspaceRegionId')]",
            "tags": { },
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Solution')]",
            "dependsOn": [
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
            ],
            "properties": {
                "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
                "referencedResources": [
                ],
                "containedResources": [
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches', parameters('workspacename'), variables('MySearch').Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name)]",
                    "[resourceId('Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions', parameters('workspacename'), variables('MySearch').Name, variables('MyAlert').Schedule.Name, variables('MyAlert').Name)]"
                ]
            },
            "plan": {
                "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
                "Version": "[variables('SolutionVersion')]",
                "product": "[variables('ProductName')]",
                "publisher": "[variables('SolutionPublisher')]",
                "promotionCode": ""
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [ ],
            "tags": { },
            "properties": {
                "etag": "*",
                "query": "[variables('MySearch').query]",
                "displayName": "[variables('MySearch').displayName]",
                "category": "[variables('MySearch').category]"
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name)]"
            ],
            "properties": {
                "etag": "*",
                "interval": "[variables('MyAlert').Schedule.Interval]",
                "queryTimeSpan": "[variables('MyAlert').Schedule.TimeSpan]",
                "enabled": true
            }
        },
        {
            "name": "[concat(parameters('workspaceName'), '/', variables('MySearch').Name, '/', variables('MyAlert').Schedule.Name, '/', variables('MyAlert').Name)]",
            "type": "Microsoft.OperationalInsights/workspaces/savedSearches/schedules/actions",
            "apiVersion": "[variables('LogAnalyticsApiVersion-Search')]",
            "dependsOn": [
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/savedSearches/', variables('MySearch').Name, '/schedules/', variables('MyAlert').Schedule.Name)]"
            ],
            "properties": {
                "etag": "*",
                "Type": "Alert",
                "Name": "[variables('MyAlert').DisplayName]",
                "Description": "[variables('MyAlert').Description]",
                "Severity": "[variables('MyAlert').Severity]",
                "Threshold": {
                    "Operator": "[variables('MyAlert').ThresholdOperator]",
                    "Value": "[variables('MyAlert').ThresholdValue]",
                    "MetricsTrigger": {
                        "TriggerCondition": "[variables('MyAlert').MetricsTrigger.TriggerCondition]",
                        "Operator": "[variables('MyAlert').MetricsTrigger.Operator]",
                        "Value": "[variables('MyAlert').MetricsTrigger.Value]"
                    }
                },
                "Throttling": {
                    "DurationInMinutes": "[variables('MyAlert').ThrottleMinutes]"
                },
                "AzNsNotification": {
                    "GroupIds": "[variables('MyAlert').AzNsNotification.GroupIds]",
                    "CustomEmailSubject": "[variables('MyAlert').AzNsNotification.CustomEmailSubject]"
                }
            }
        }
    ]
}
```

다음 매개 변수 파일은 이 솔루션에 대한 샘플 값을 제공합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspacename": {
            "value": "myWorkspace"
        },
        "accountName": {
            "value": "myAccount"
        },
        "workspaceregionId": {
            "value": "East US"
        },
        "regionId": {
            "value": "East US 2"
        },
        "pricingTier": {
            "value": "Free"
        },
        "actiongroup": {
            "value": "/subscriptions/3b540246-808d-4331-99aa-917b808a9166/resourcegroups/myTestGroup/providers/microsoft.insights/actiongroups/sample"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계
* 관리 솔루션에 대한 [보기를 추가](solutions-resources-views.md)합니다.
* 관리 솔루션에 [Automation runbook 및 기타 리소스를 추가](solutions-resources-automation.md)합니다.
