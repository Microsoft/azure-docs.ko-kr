---
title: 논리 앱 및 runbook을 업데이트 하 여 클래식 경고 마이그레이션 Azure Monitor 준비
author: yanivlavi
description: 자발적 마이그레이션을 준비 하기 위해 웹 후크, 논리 앱 및 runbook을 수정 하는 방법에 대해 알아봅니다.
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 58ba95ff60ddccf909578a673110c870caf57376
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705567"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>기존 경고 규칙의 마이그레이션을 위해 논리 앱 및 runbook 준비

[이전에 발표](monitoring-classic-retirement.md)한 대로 Azure Monitor의 클래식 경고는 9 월 2019 (원래 7 월 2019 일)에 사용 중지 됩니다. 마이그레이션 도구는 클래식 경고 규칙을 사용 하는 고객과 마이그레이션 자체를 트리거하는 고객에 게 Azure Portal에서 사용할 수 있습니다.

> [!NOTE]
> 마이그레이션 도구의 롤아웃 지연으로 인해 클래식 경고 마이그레이션의 사용 중지 날짜가 원래 발표 된 날짜 6 월 30 일까 지 2019 년 8 월 31 2019 일로 연장 되었습니다.

기존 경고 규칙을 자발적으로 새 경고 규칙으로 마이그레이션하기를 선택 하는 경우 두 시스템 간에 몇 가지 차이점이 있습니다. 이 문서에서는 이러한 차이점과 변경을 준비 하는 방법을 설명 합니다.

## <a name="api-changes"></a>API 변경

`microsoft.insights/alertrules`(클래식 경고 규칙)를 만들고 관리 하는 Api는 새 메트릭 경고 (`microsoft.insights/metricalerts`)를 만들고 관리 하는 Api와 다릅니다. 지금 기존 경고 규칙을 프로그래밍 방식으로 만들고 관리 하는 경우 새 Api를 사용 하도록 배포 스크립트를 업데이트 합니다.

다음 표는 클래식 및 새 경고의 프로그래밍 인터페이스에 대 한 참조입니다.

|         |클래식 경고  |새 메트릭 경고 |
|---------|---------|---------|
|REST API     | [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules)         | [microsoft.insights/metricalerts](https://docs.microsoft.com/rest/api/monitor/metricalerts)       |
|Azure CLI     | [az monitor alert](https://docs.microsoft.com/cli/azure/monitor/alert?view=azure-cli-latest)        | [az monitor 메트릭 경고](https://docs.microsoft.com/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|PowerShell      | [참조](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrule)       |  [참조](https://docs.microsoft.com/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Azure Resource Manager 템플릿 | [클래식 경고의 경우](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-enable-template)|[새 메트릭 경고의 경우](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates)|

## <a name="notification-payload-changes"></a>알림 페이로드 변경

알림 페이로드 형식은 [클래식 경고 규칙과](alerts-webhooks.md) [새 메트릭 경고](alerts-metric-near-real-time.md#payload-schema)간에 약간 다릅니다. 기존 경고 규칙에 의해 트리거되는 webhook, 논리 앱 또는 runbook 작업이 있는 경우 새 메트릭 경고의 페이로드 형식을 허용 하도록 해당 알림 엔드포인트을 업데이트 해야 합니다.

다음 표를 사용 하 여 기본 형식에서 새 형식으로 webhook 페이로드 필드를 매핑합니다.

|  |클래식 경고  |새 메트릭 경고 |
|---------|---------|---------|
|경고가 활성화 또는 해결 되었습니까?    | **status**       | **data.status** |
|경고에 대 한 컨텍스트 정보     | **context**        | **data.context**        |
|경고가 활성화 되거나 해결 된 타임 스탬프입니다.     | **context.timestamp**       | **data.context.timestamp**        |
| 경고 규칙 ID | **context.id** | **data.context.id** |
| 경고 규칙 이름 | **context.name** | **data.context.name** |
| 경고 규칙에 대 한 설명 | **context.description** | **data.context.description** |
| 경고 규칙 조건 | **context.condition** | **data.context.condition** |
| 메트릭 이름 | **context.condition.metricName** | **data.context.condition.allOf[0].metricName** |
| 시간 집계 (평가 기간 동안 메트릭이 집계 되는 방법)| **컨텍스트별. timeAggregation** | **컨텍스트별. timeAggregation** |
| 평가 기간 | **context.condition.windowSize** | **data.context.condition.windowSize** |
| 연산자 (집계 된 메트릭 값을 임계값과 비교 하는 방법) | **context.condition.operator** | **data.context.condition.operator** |
| 임계값 | **context.condition.threshold** | **data.context.condition.allOf[0].threshold** |
| 메트릭 값 | **context.condition.metricValue** | **data.context.condition.allOf[0].metricValue** |
| 구독 ID | **context.subscriptionId** | **data.context.subscriptionId** |
| 영향을 받는 리소스의 리소스 그룹 | **context.resourceGroup** | **data.context.resourceGroup** |
| 영향을 받는 리소스의 이름 | **context.resourceName** | **data.context.resourceName** |
| 영향을 받는 리소스의 유형입니다. | **context.resourceType** | **data.context.resourceType** |
| 영향을 받는 리소스의 리소스 ID | **context.resourceId** | **data.context.resourceId** |
| 포털 리소스 요약 페이지에 대 한 직접 링크 | **context.portalLink** | **data.context.portalLink** |
| 웹 후크 또는 논리 앱에 전달할 사용자 지정 페이로드 필드 | **properties** | **data.properties** |

페이로드는 볼 수 있는 것과 유사 합니다. 다음 섹션에서 다음을 제공 합니다.

- 새 형식으로 작업 하기 위해 논리 앱을 수정 하는 방법에 대해 자세히 설명 합니다.
- 새 경고에 대 한 알림 페이로드를 구문 분석 하는 runbook 예제입니다.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>메트릭 경고 알림을 받도록 논리 앱 수정

클래식 경고를 사용 하는 논리 앱을 사용 하는 경우 새 메트릭 경고 페이로드를 구문 분석 하도록 논리 앱 코드를 수정 해야 합니다. 다음 단계를 수행하세요.

1. 새 논리 앱을 만듭니다.

1. "Azure Monitor-메트릭 경고 처리기" 템플릿을 사용 합니다. 이 템플릿에는 적절 한 스키마가 정의 된 **HTTP 요청** 트리거가 있습니다.

    ![논리-앱-템플릿](media/alerts-migration/logic-app-template.png "메트릭 경고 템플릿")

1. 처리 논리를 호스트 하는 작업을 추가 합니다.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>메트릭 경고 알림을 수신 하는 automation runbook 사용

다음 예제에서는 runbook에서 사용할 수 있는 PowerShell 코드를 제공 합니다. 이 코드는 클래식 메트릭 경고 규칙 및 새 메트릭 경고 규칙에 대 한 페이로드를 구문 분석할 수 있습니다.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

경고가 트리거될 때 가상 머신을 중지 하는 runbook의 전체 예는 [Azure Automation 설명서](https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook)를 참조 하세요.

## <a name="partner-integration-via-webhooks"></a>웹 후크를 통한 파트너 통합

[클래식 경고와 통합 되는](https://docs.microsoft.com/azure/azure-monitor/platform/partners) 대부분의 파트너는 통합을 통해 최신 메트릭 경고를 이미 지원 합니다. 이미 새 메트릭 경고와 함께 작동 하는 알려진 통합은 다음과 같습니다.

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

여기에 나열 되지 않은 파트너 통합을 사용 하는 경우 통합이 새 메트릭 경고와 함께 작동 하는지 통합 공급자에 게 확인 하세요.

## <a name="next-steps"></a>다음 단계

- [마이그레이션 도구 사용 방법](alerts-using-migration-tool.md)
- [마이그레이션 도구의 작동 방식 이해](alerts-understand-migration.md)
