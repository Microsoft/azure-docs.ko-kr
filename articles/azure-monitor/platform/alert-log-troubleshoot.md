---
title: Azure Monitor의 로그 경고 문제 해결 | Microsoft Docs
description: 일반적인 문제, 오류 및 Azure에서 로그 경고 규칙에 대 한 확인 합니다.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 0c7189f1d43a114532b30b0c1aabe6f7cd4402d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775993"
---
# <a name="troubleshooting-log-alerts-in-azure-monitor"></a>Azure Monitor에서 로그 경고 문제 해결  

## <a name="overview"></a>개요

이 문서에서는 Azure Monitor의 로그 경고를 설정할 때 나타나는 일반적인 문제를 해결 하는 방법을 보여 줍니다. 또한 로그 경고의 기능 또는 구성과 관련하여 자주 묻는 질문에 대한 솔루션을 제공합니다. 

용어 **로그 경고** fire의 로그 쿼리 기반 경고에 설명 합니다.는 [Log Analytics 작업 영역](../learn/tutorial-viewdata.md) 또는 [Application Insights](../../azure-monitor/app/analytics.md)합니다. [로그 경고 - 개요](../platform/alerts-unified-log.md)에서 기능, 용어 및 형식에 대해 자세히 알아봅니다.

> [!NOTE]
> 이 문서에서는 Azure Portal이 표시되는 경우 및 트리거된 경고 규칙 및 연결된 작업 그룹에서 수행된 알림을 고려하지 않습니다. 이러한 경우 [작업 그룹](../platform/action-groups.md)의 문서에 있는 자세한 내용을 참조하세요.

## <a name="log-alert-didnt-fire"></a>로그 경고가 발생하지 않음

구성된 [Azure Monitor의 로그 경고 규칙](../platform/alerts-log.md) 상태가 [예상한 경우 *발생*으로 표시되지 않는](../platform/alerts-managing-alert-states.md) 몇 가지 일반적인 이유는 다음과 같습니다. 

### <a name="data-ingestion-time-for-logs"></a>로그에 대한 데이터 수집 시간

로그 경고는 [Log Analytics](../learn/tutorial-viewdata.md) 또는 [Application Insights](../../azure-monitor/app/analytics.md)에 기반한 쿼리를 주기적으로 실행합니다. 전 세계에서 많은 테라바이트의에서 수천 명의 고객이 다양 한 원본의 데이터를 처리 하는 Azure Monitor 서비스 이므로 다양 한 지연 시간에 취약 합니다. 자세한 내용은 [Azure Monitor 로그에서 데이터 수집 시간](../platform/data-ingestion-time.md)합니다.

데이터 수집 지연을 완화하려면 시스템은 필요한 데이터가 아직 수집되지 않은 것을 발견하는 경우 기다린 다음, 경고 쿼리를 여러 번 다시 시도합니다. 시스템에는 기하급수적으로 증가하는 대기 시간이 설정되어 있습니다. 로그 경고는 데이터를 사용할 수 있는 경우에만 트리거되므로 지연은 느린 로그 데이터 수집 때문일 수 있습니다. 

### <a name="incorrect-time-period-configured"></a>잘못된 기간이 구성됨

[로그 경고 용어](../platform/alerts-unified-log.md#log-search-alert-rule---definition-and-types)의 문서에서 설명한 대로 구성에 지정된 기간은 쿼리의 시간 범위를 지정합니다. 쿼리는 이 시간 범위 내에서 만들어진 레코드만 반환합니다. 기간은 남용을 방지하고 쿼리 로그에 사용되는 시간 명령(*ago* 등)을 회피하도록 로그 쿼리에 대해 인출된 데이터를 제한합니다. 예를 들어 기간이 60분으로 설정되고 오후 1시 15분에 쿼리를 실행하는 경우 오후 12시 15분부터 오후 1시 15분 사이에 생성된 레코드만 로그 쿼리에 사용됩니다. 로그 쿼리가 *ago (1d)* 와 같은 시간 명령을 사용하는 경우 기간은 해당 간격으로 설정되어 있으므로 쿼리는 여전히 오후 12시 15분부터 오후 1시 15분 사이의 데이터를 사용합니다.*

따라서 구성의 기간이 쿼리와 일치하는지 확인합니다. 앞에서 설명한 예제의 경우 로그 쿼리에서 녹색 표식과 같이 *ago (1d)* 를 사용하는 경우, 쿼리가 의도대로 실행되도록 하려면 기간을 24시간 또는 1440분(빨간색으로 표시)으로 설정해야 합니다.

![기간](media/alert-log-troubleshoot/LogAlertTimePeriod.png)

### <a name="suppress-alerts-option-is-set"></a>경고 표시 안 함 옵션이 설정됨

[Azure Portal에서 로그 경고 규칙 만들기](../platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)에 대한 문서의 8단계에서 설명한 것처럼 로그 경고는 구성된 시간에 대한 트리거 및 알림 작업을 표시하지 않는 **경고 표시 안 함** 옵션을 제공합니다. 결과적으로 경고가 실제로 수행했던 동안 발생하지 않았지만 표시되지 않은 것으로 생각할 수 있습니다.  

![경고 표시 안 함](media/alert-log-troubleshoot/LogAlertSuppress.png)

### <a name="metric-measurement-alert-rule-is-incorrect"></a>메트릭 측정 경고 규칙이 잘못됨

**메트릭 측정 로그 경고**는 특수 기능 및 제한된 경고 쿼리 구문을 포함하는 로그 경고의 하위 유형입니다. 메트릭 측정 로그 경고 규칙을 사용하려면 쿼리 출력이 메트릭 시계열이어야 합니다. 즉, 이 테이블은 서로 동일한 크기의 기간과 계산된 해당 값을 포함합니다. 또한 사용자는 AggregatedValue와 함께 테이블에 추가 변수를 갖도록 선택할 수 있습니다. 이러한 변수는 테이블을 정렬하는 데 사용될 수 있습니다. 

예를 들어 메트릭 측정 로그 경고 규칙이 다음과 같이 구성되었다고 가정합니다.

- 쿼리: `search *| summarize AggregatedValue = count() by $table, bin(timestamp, 1h)`  
- 기간: 6시간
- 임계값: 50
- 경고 논리: 3회 연속 위반
- 다음에 대한 집계: $table로 선택됨

명령에 *summarize ... by* 및 제공된 두 개의 변수(timestamp 및 $table)가 포함되므로 시스템은 $table을 ‘다음에 대한 집계’로 선택합니다. 아래와 같이 *$table* 필드를 기준으로 결과 테이블을 정렬한 다음, 각 테이블 유형(예: availabilityResults)에 대한 여러 개의 AggregatedValue를 조사하여 3회 이상의 연속 위반이 있었는지 확인합니다.

![여러 값을 사용한 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMQuery.png)

‘다음에 대한 집계’는 ‘$table’에 정의되었으므로 ‘$table’ 열(빨간색으로 표시됨)의 데이터가 정렬됩니다. 그러면 ‘다음에 대한 집계’ 필드, 즉 $table의 형식을 그룹화하고 살펴봅니다. 예를 들어 availabilityResults 값은 하나의 그림/엔터티(주황색으로 강조 표시됨)로 간주됩니다. 이 값 그림/엔터티에서 경고 서비스는 ‘availabilityResults’ 테이블 값에 대해 경고가 트리거되는 3회 연속 위반(녹색으로 표시됨)이 발생했는지 확인합니다. 마찬가지로, $table의 다른 값에 대해 3회 연속 위반이 나타나면 동일한 항목에 대해 또 다른 경고 알림이 트리거됩니다. 경보 서비스는 시간별로 하나의 그림/엔터티(주황색으로 표시됨)의 값을 자동으로 정렬합니다.

이제 메트릭 측정 로그 경고 규칙이 수정되었고, 쿼리는 3회 연속 위반에 대한 경고 논리를 포함하여 이전 구성과 동일하게 유지되는 구성의 나머지 부분이 있는 `search *| summarize AggregatedValue = count() by bin(timestamp, 1h)`였다고 가정합니다. 이 경우 "다음에 대한 집계" 옵션은 기본적으로 timestamp가 됩니다. *summarize ... by*, 즉 *timestamp*에 대한 쿼리에는 하나의 값만 제공되므로 앞의 예제와 마찬가지로 실행 종료 시의 출력은 아래 그림과 같습니다.

   ![단일 값을 사용한 메트릭 측정 쿼리 실행](media/alert-log-troubleshoot/LogMMtimestamp.png)

‘다음에 대한 집계’는 timestamp에 정의되었으므로 데이터가 *timestamp* 열(빨간색)의 데이터가 정렬됩니다. 그러면 timestamp를 기준으로 그룹화합니다. 예를 들어 `2018-10-17T06:00:00Z`에 대한 값은 하나의 그림/엔터티(주황색으로 강조 표시됨)로 간주됩니다. 이 값 그림/엔터티에서 경고 서비스는 연속 위반이 발생하지 않으며(각 timestamp 값에 하나의 항목만 있음), 경고가 트리거되지 않습니다. 따라서 이러한 경우 사용자는 다음 중 하나를 수행해야 합니다.

- 구성된 "다음에 대한 집계" 필드를 사용하여 올바르게 정렬하도록 더미 변수 또는 기존 변수(예: $table)를 추가합니다.
- (또는) *총 위반 횟수*를 기반으로 하는 경고 논리를 적절하게 대신 사용하도록 경고 규칙을 재구성합니다.

## <a name="log-alert-fired-unnecessarily"></a>로그 경고가 불필요하게 발생함

다음은 발생할 것으로 예상되지 않을 때 [Azure 경고](../platform/alerts-managing-alert-states.md)에서 보면 [Azure Monitor에서 구성된 로그 경고 규칙](../platform/alerts-log.md)이 트리거될 수 있는 몇 가지 일반적인 이유입니다.

### <a name="alert-triggered-by-partial-data"></a>부분 데이터로 인해 경고가 트리거됨

Log Analytics 및 Application Insights를 구동하는 분석은 수집 지연 및 처리의 영향을 받을 수 있습니다. 이로 인해 제공된 로그 경고 쿼리가 실행될 때 데이터를 사용할 수 없거나 일부 데이터만 사용할 수 있는 경우가 있을 수 있습니다. 자세한 내용은 [Azure Monitor의 로그 데이터 수집 시간](../platform/data-ingestion-time.md)합니다.

경고 규칙이 구성된 방식에 따라 경고 실행 시 로그에 데이터가 없거나 일부가 없는 경우 잘못 발생하는 것일 수 있습니다. 이러한 경우 경고 쿼리 또는 구성을 변경하는 것이 좋습니다. 

예를 들어 분석 쿼리의 결과 수가 5보다 적을 때 트리거하도록 로그 경고 규칙이 구성된 경우 데이터가 없거나(0개 레코드) 부분적인 결과가 제공될 경우(1개 레코드) 경고가 트리거됩니다. 그러나 데이터 수집이 지연된 후에는 전체 데이터를 사용하여 동일한 쿼리가 10개의 레코드 결과를 제공할 수 있습니다.

### <a name="alert-query-output-misunderstood"></a>경고 쿼리 출력이 잘못 이해됨

분석 쿼리에서 로그 경고에 대한 논리를 제공합니다. 분석 쿼리는 다양한 빅 데이터 및 수학 함수를 사용할 수 있습니다.  경고 서비스는 지정된 기간에 대한 데이터를 사용하여 지정된 간격으로 쿼리를 실행합니다. 경고 서비스는 선택한 경고 유형에 따라 제공된 쿼리를 약간 변경합니다. 이 변경의 "쿼리 실행" 섹션에서 볼 수 있습니다 *신호 논리 구성* 아래와 같이 화면: ![실행될 쿼리](media/alert-log-troubleshoot/LogAlertPreview.png)

**실행될 쿼리** 상자에는 로그 경고 서비스가 실행하는 쿼리가 표시됩니다. 실제로 경고를 만들기 이전의 경고 쿼리 출력을 이해하려면 [Analytics 포털](../log-query/portals.md) 또는 [Analytics API](https://docs.microsoft.com/rest/api/loganalytics/)를 통해 시간 범위 뿐만 아니라 명시된 쿼리를 실행할 수 있습니다.

## <a name="log-alert-was-disabled"></a>로그 경고를 사용 하지 않도록 설정

다음은 몇 가지 이유 때문 [Azure Monitor의 로그 경고 규칙](../platform/alerts-log.md) Azure Monitor에서 사용할 수 없습니다.

### <a name="resource-on-which-alert-was-created-no-longer-exists"></a>경고를 더 이상 만든 리소스가 존재

Azure Monitor에서 만든 로그 경고 규칙에는 Azure Log Analytics 작업 영역, Azure Application Insights 앱 및 Azure 리소스와 같은 특정 리소스를 대상입니다. 고의 로그 경고 서비스는 지정된 된 대상에 대 한 규칙에서 제공 하는 분석 쿼리를 실행 합니다. 하지만 규칙 생성 후 대개 사용자가 이동할 Azure에서 삭제 하거나 경고 규칙의 대상 Azure 내에서 이동 합니다. 로그 경고 규칙의 대상 이므로 더 이상 유효한 규칙의 실행이 실패 합니다.

이러한 경우에 Azure Monitor의 로그 경고를 사용 하지 않도록 설정 되며 고객에 게는 규칙 자체 일주일 같은 상당한 기간 동안 지속적으로 실행할 수 없을 때 불필요 하 게 비용이 청구 되지 않도록 합니다. 사용자는 로그 경고 규칙을 통해 Azure Monitor에서 비활성화 되었습니다 정확한 시간에서 찾을 수 있습니다 [Azure 활동 로그](../../azure-resource-manager/resource-group-audit.md)합니다. Azure 활동 로그의 로그 경고 규칙은 Azure에서 사용 하지 않도록 설정 하는 경우 Azure 활동 로그에 이벤트 추가 됩니다.

지속적인 못하여; 사용 하지 않도록 설정 경고 규칙에 대 한 Azure 활동 로그의 샘플 이벤트 다음과 같습니다.

```json
{
    "caller": "Microsoft.Insights/ScheduledQueryRules",
    "channels": "Operation",
    "claims": {
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/ScheduledQueryRules"
    },
    "correlationId": "abcdefg-4d12-1234-4256-21233554aff",
    "description": "Alert: test-bad-alerts is disabled by the System due to : Alert has been failing consistently with the same exception for the past week",
    "eventDataId": "f123e07-bf45-1234-4565-123a123455b",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2019-03-22T04:18:22.8569543Z",
    "id": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "localizedValue": "Microsoft.Insights/ScheduledQueryRules/disable/action"
    },
    "resourceGroupName": "<Resource Group>",
    "resourceProviderName": {
        "value": "MICROSOFT.INSIGHTS",
        "localizedValue": "Microsoft Insights"
    },
    "resourceType": {
        "value": "MICROSOFT.INSIGHTS/scheduledqueryrules",
        "localizedValue": "MICROSOFT.INSIGHTS/scheduledqueryrules"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-03-22T04:18:22.8569543Z",
    "subscriptionId": "<SubscriptionId>",
    "properties": {
        "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<ResourceGroup>/PROVIDERS/MICROSOFT.INSIGHTS/SCHEDULEDQUERYRULES/TEST-BAD-ALERTS",
        "subscriptionId": "<SubscriptionId>",
        "resourceGroup": "<ResourceGroup>",
        "eventDataId": "12e12345-12dd-1234-8e3e-12345b7a1234",
        "eventTimeStamp": "03/22/2019 04:18:22",
        "issueStartTime": "03/22/2019 04:18:22",
        "operationName": "Microsoft.Insights/ScheduledQueryRules/disable/action",
        "status": "Succeeded",
        "reason": "Alert has been failing consistently with the same exception for the past week"
    },
    "relatedEvents": []
}
```

### <a name="query-used-in-log-alert-is-not-valid"></a>로그 경고에 사용 된 쿼리가 올바르지 않습니다.

Azure Monitor의 해당 구성의 일부로 만든 각 로그 경고 규칙 경고 서비스에서 주기적으로 실행 하는 분석 쿼리를 지정 해야 합니다. 분석 쿼리 규칙 만들기 또는 업데이트 시 올바른 구문을 있을 수 있습니다. 로그에 일부 시간 기간을 통해 쿼리 제공 경고 규칙 구문 문제를 개발 하 고 규칙 실행이 실패 하도록 합니다. 로그 경고 규칙에서 제공 하는 분석 쿼리 오류를 개발할 수 이유 몇 가지 일반적인 원인은 다음과 같습니다.

- 쿼리를 쓸 [여러 리소스에서 실행](../log-query/cross-workspace-query.md) 하며, 지정 된 리소스 하나 이상의 이제 존재 하지 않습니다.
- Analytics 플랫폼 때문에 데이터 흐름 없음 되었습니다 합니다 [쿼리를 실행 하면 오류가 발생](https://dev.loganalytics.io/documentation/Using-the-API/Errors) 으로 제공 된 쿼리에 대 한 데이터가 없습니다.
- 변경 내용 [쿼리 언어](https://docs.microsoft.com/azure/kusto/query/) 는 명령에서 발생 한 있으며 함수는 수정 된 형식입니다. 따라서 경고 규칙에서 이전에 제공된 된 쿼리와 더 이상 유효 합니다.

사용자는에 대 한 경고가이 동작이 먼저 통해 [Azure Advisor](../../advisor/advisor-overview.md)합니다. 중간 영향 및 설명 대로 "모니터링 되도록 로그 경고 규칙 복구"를 사용 하 여 고가용성 범주 아래의 Azure Advisor에서 특정 로그 경고 규칙에 대 한 권장 사항을 추가 됩니다. 쿼리 경고가 7 일 동안 Azure Advisor의 권장 사항 제공 하는 경우 지정 된 로그 경고 규칙 수정 되지 않습니다. 그런 다음 Azure Monitor 로그 경고를 사용 하지 않도록 설정 하 고 고객에 게는 규칙 자체 일주일 같은 상당한 기간 동안 지속적으로 실행할 수 없을 때 불필요 하 게 비용이 청구 되지 않도록 합니다.

사용자는 로그 경고 규칙을 통해 Azure Monitor에서 비활성화 되었습니다 정확한 시간에서 찾을 수 있습니다 [Azure 활동 로그](../../azure-resource-manager/resource-group-audit.md)합니다. Azure 활동 로그에서 로그 경고 규칙 사용-Azure에서 되지 이벤트는 Azure 활동 로그에 추가 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Alerts의 로그 경고](../platform/alerts-unified-log.md)에 대해 알아보기
- [Application Insights](../../azure-monitor/app/analytics.md)에 대해 자세히 알아보기
- 자세한 내용은 [로그 쿼리](../log-query/log-query-overview.md)
