---
title: 레거시 Log Analytics 경고 API에서 새 Azure Alerts API로 전환
description: 레거시 savedSearch 기반 Log Analytics 경고 API의 사용 중지 및 경고 규칙을 새 ScheduledQueryRules API로 전환하는 프로세스를 간략하게 설명하고 일반적인 고객 문제를 해결하는 세부 정보를 제공합니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1706fc050fecd2e4be3a40725ec3e63a9036b3a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995989"
---
# <a name="switch-api-preference-for-log-alerts"></a>로그 경고의 API 기본 설정 전환

> [!NOTE]
> 콘텐츠를 사용자에 게 적용 명시 된 Azure 공용 클라우드만 하 고 **되지** Azure Government 또는 Azure 중국 클라우드에 대 한 합니다.  

최근까지 Microsoft Operations Management Suite 포털에서 경고 규칙을 관리했습니다. 새 경고 환경이 Log Analytics를 포함한 Microsoft Azure의 다양한 서비스와 통합되었으며 Microsoft에서는 [OMS 포털에서 Azure로 경고 규칙을 확장](alerts-extend.md)하라고 요청했습니다. 하지만, 고객 서비스 중단을 최소화하기 위해 사용할 프로그래밍 인터페이스인 SavedSearch 기반의 [Log Analytics 경고 API](api-alerts.md)를 변경하지 않았습니다.

이제 Log Analytics 경고 사용자에게 진정한 Azure 프로그래밍 대체 인터페이스인 [Azure Monitor - ScheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 발표합니다. 이 API는 [Azure 청구 - 로그 경고용](alerts-unified-log.md#pricing-and-billing-of-log-alerts)에도 반영됩니다. API를 사용하여 로그 경고를 관리하는 방법을 자세히 알아보려면 [Azure 리소스 템플릿을 사용하여 로그 경고 관리](alerts-log.md#managing-log-alerts-using-azure-resource-template) 및 [PowerShell, CLI 또는 API를 사용하여 로그 경고 관리](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)를 참조하세요.

## <a name="benefits-of-switching-to-new-azure-api"></a>새 Azure API로 전환하면 얻을 수 있는 이점

[레거시 Log Analytics 경고 API](api-alerts.md)가 아닌 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하여 경고를 만들고 관리하는 경우 여러 가지 장점이 있습니다. 몇 가지 주요 장점은 다음과 같습니다.

- 경고 규칙에서 [작업 영역 로그 검색을 교차](../log-query/cross-workspace-query.md)하고 Log Analytics 작업 영역이나 Application Insights 앱 같은 외부 리소스를 포괄할 수 있음
- 쿼리에서 그룹화하는 데 여러 필드가 사용된 경우 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하면 사용자가 Azure Portal에서 집계할 필드를 지정할 수 있음
- [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하여 만든 로그 경고는 최대 48시간까지 정의된 기간을 포함할 수 있으며 이전보다 더 오랜 기간 동안 데이터를 페치할 수 있음
- [레거시 Log Analytics 경고 API](api-alerts.md)를 사용하여 세 가지 수준의 리소스를 만들 필요 없이 단일 리소스로 한 번에 경고 규칙을 만들 수 있음
- Azure에 있는 쿼리 기반 로그 경고의 모든 변형을 위한 단일 프로그래밍 인터페이스 - 새로운 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하여 Application Insights뿐 아니라 Log Analytics의 규칙을 관리할 수 있음
- 모든 새 로그 경고 기능 및 향후 개발 사항은 새로운 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 통해서만 제공됨

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>레거시 로그 경고 API에서 전환하는 프로세스

[레거시 Log Analytics 경고 API](api-alerts.md)에서 경고 규칙을 이동하는 프로세스는 어떠한 방법으로도 경고 정의, 쿼리 또는 구성의 변경을 포함하지 않습니다. 경고 규칙 및 영향을 받지 및 경고 모니터링 중지 되지 않습니다 또는 있거나 전환 된 후 중단 됩니다.

사용자는 [레거시 Log Analytics 경고 API](api-alerts.md) 또는 새 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용할 수 있습니다. API에서 생성된 경고 규칙은 ‘동일한 API에서만 관리할 수 있고’ Azure Portal에서도 관리할 수 있습니다. 기본적으로 Azure Monitor는 [레거시 Log Analytics 경고 API](api-alerts.md)를 계속 사용하여 Azure Portal에서 새 경고 규칙을 만듭니다.

scheduledQueryRules API로 기본 설정을 전환할 경우 미치는 영향은 다음과 같이 컴파일됩니다.

- 프로그래밍 인터페이스를 통해 로그 경고를 관리하기 위해 수행된 모든 조작은 이제 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하여 수행해야 합니다. 자세한 내용은 [Azure 리소스 템플릿을 통한 샘플 사용](alerts-log.md#managing-log-alerts-using-azure-resource-template) 및 [Azure CLI 및 PowerShell을 통한 샘플 사용](alerts-log.md#managing-log-alerts-using-powershell-cli-or-api)을 참조하세요.
- Azure Portal에서 만든 모든 새 로그 경고 규칙은 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)만 사용하여 만들어지고, 이를 통해 사용자는 Azure Portal을 통해 [새 API의 추가 기능](#benefits-of-switching-to-new-azure-api)을 사용할 수 있습니다.
- 로그 경고 규칙에 대 한 심각도에서 이동 합니다. *위험, 경고 및 알림*를 *심각도 값 0, 1 및 2*합니다. 만들거나 업데이트 하는 옵션과 함께 심각도 4 인 규칙을도 경고 합니다.

> [!CAUTION]
> 사용자가 새 기본 설정 전환 되 면 [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), 규칙 다시 선택 하거나 이전 사용으로 되돌릴 수 없습니다 [레거시 Log Analytics 경고 API](api-alerts.md)합니다.

자발적으로 새 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)로 전환하고 [레거시 Log Analytics 경고 API](api-alerts.md)의 사용을 차단하려는 고객은 아래 API에서 PUT 호출을 수행하여 특정 Log Analytics 작업 영역과 연결된 모든 경고 규칙을 전환하면 됩니다.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

아래 JSON을 포함하는 요청 본문을 사용합니다.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

또한 Azure Resource Manager API 호출을 단순화하는 오픈 소스 명령줄 도구 [ARMClient](https://github.com/projectkudu/ARMClient)를 사용하여 PowerShell 명령줄에서 API를 액세스할 수 있습니다. 아래와 같이 샘플 PUT 호출에서 ARMclient 도구를 사용하여 특정 Log Analytics 작업 영역과 연결된 모든 경고 규칙을 전환합니다.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

새 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)를 사용하기 위해 Log Analytics 작업 영역에서 모든 경고 규칙을 성공적으로 전환하면 다음 응답이 제공됩니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

또한 사용자는 Log Analytics 작업 영역의 현재 상태를 확인하고 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)만 사용하도록 전환되었는지 여부를 확인할 수 있습니다. 확인하려면 사용자가 아래 API에서 GET 호출을 수행하면 됩니다.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

위의 내용을 [ARMClient](https://github.com/projectkudu/ARMClient) 도구를 사용하여 PowerShell 명령줄에서 실행하려면 아래 샘플을 참조하세요.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

지정된 Log Analytics 작업 영역이 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)만 사용하도록 전환된 경우 응답 JSON이 다음과 같이 나열됩니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
지정된 Log Analytics 작업 영역이 [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)만 사용하도록 전환되지 않은 경우에는 응답 JSON이 다음과 같이 나열됩니다.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Monitor - 로그 경고](alerts-unified-log.md)에 대해 알아봅니다.
- [Azure Alerts에서 로그 경고](alerts-log.md)를 만드는 방법을 알아봅니다.
- [Azure Alerts 환경](../../azure-monitor/platform/alerts-overview.md)에 대해 자세히 알아봅니다.
