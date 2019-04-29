---
title: 메트릭, 경고 및 진단 로그 - Azure Batch | Microsoft Docs
description: 풀, 작업 등과 같은 Azure Batch 계정 리소스에 대해 진단 로그 이벤트를 기록 및 분석합니다.
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 349896a7114fba83d852b8bc066f4f7eb0a070ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550293"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>진단 평가 및 모니터링을 위한 일괄 처리 메트릭, 경고 및 로그

 
이 문서에서는 [Azure Monitor](../azure-monitor/overview.md)의 기능을 사용하여 배치 계정을 모니터링하는 방법을 설명합니다. Azure Monitor는 배치 계정의 리소스에 대해 [메트릭](../azure-monitor/platform/data-platform-metrics.md) 및 [진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md)를 수집합니다. 이 데이터를 다양한 방법으로 수집하고 사용하여 배치 계정을 모니터링하고 문제를 진단합니다. 또한 메트릭이 지정된 값에 도달할 때 알림을 받을 수 있도록 [메트릭 경고](../azure-monitor/platform/alerts-overview.md)를 구성할 수 있습니다. 

## <a name="batch-metrics"></a>일괄 처리 메트릭

메트릭은 Azure Monitor 서비스에서 사용하는 Azure 리소스에서 내보낸 Azure 원격 분석 데이터(성능 카운터라고도 함)입니다. 일괄 처리 계정에 대한 예제 메트릭에는 풀 만들기 이벤트, 낮은 우선 순위 노드 수 및 작업 완료 이벤트가 있습니다. 

[지원되는 일괄 처리 메트릭 목록](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts)을 참조하세요.

이러한 메트릭은 다음과 같습니다.

* 추가 구성 없이 각 배치 계정에서 기본값으로 사용 가능
* 1분마다 생성
* 자동으로 유지되지 않으나 30일 롤링 기록이 있습니다. 활동 메트릭을 진단 로깅의 일환으로 유지할 수 있습니다.

### <a name="view-metrics"></a>메트릭 보기

Azure Portal에서 배치 계정에 대한 메트릭을 봅니다. 기본적으로 계정에 대한 **개요** 페이지에 키 노드, 코어 및 작업 메트릭이 표시됩니다. 

모든 배치 계정 메트릭을 보려면 다음을 수행합니다. 

1. 포털에서 **모든 서비스** > **배치 계정**을 차례로 클릭한 다음, 배치 계정의 이름을 클릭합니다.
2. **모니터링**에서 **메트릭**을 클릭합니다.
3. 하나 이상의 메트릭을 선택합니다. 원하는 경우 **구독**, **리소스 그룹**, **리소스 종류** 및 **리소스** 드롭다운 목록을 사용하여 추가 리소스 메트릭을 선택합니다.

    ![일괄 처리 메트릭](media/batch-diagnostics/metrics-portal.png)

메트릭을 프로그래밍 방식으로 검색하려면 Azure Monitor API를 사용합니다. 예제는 [.NET을 사용하여 Azure Monitor 메트릭 검색](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)을 참조하세요.

## <a name="batch-metric-reliability"></a>일괄 처리 메트릭 안정성

메트릭은 추세 및 데이터 분석에 사용됩니다. 메트릭 전달은 보장되지 않으며 전달 오류, 데이터 손실 및/또는 중복이 발생할 수 있습니다. 경고 또는 트리거 기능에 단일 이벤트를 사용하는 것은 권장되지 않습니다. 경고에 대해 임계값을 설정하는 방법에 대한 자세한 내용은 [일괄 처리 메트릭 경고](#batch-metric-alerts) 섹션을 참조하세요.

지난 3분 동안 발생한 메트릭은 아직도 집계 중일 수 있습니다. 이 시간 프레임 동안 메트릭 값은 축소 보고될 수 있습니다.

## <a name="batch-metric-alerts"></a>일괄 처리 메트릭 경고

선택적으로 특정 메트릭의 값이 사용자가 할당한 임계값을 초과했을 때 트리거하는 *메트릭 경고*를 거의 실시간으로 구성합니다. 경고가 “활성화”되었을 때(임계값을 초과했고 경고 조건에 부합함)와 “해결”되었을 때(임계값을 다시 초과하고 조건에 더 이상 부합하지 않음) 경고가 사용자가 선택한 [알림](../monitoring-and-diagnostics/insights-alerts-portal.md)을 생성합니다. 단일 데이터 요소를 기준으로 할 경우 메트릭 전달 오류, 데이터 손실 및/또는 중복이 발생할 수 있으므로 권장되지 않습니다. 이러한 불일치를 고려하려면 경고에 임계값을 사용해야 합니다.

예를 들어 풀의 구성을 조정할 수 있도록 우선 순위가 낮은 코어 수가 일정 수준으로 낮아질 경우 메트릭 경고를 구성할 수 있습니다. 낮은 우선 순위 평균 점수가 전체 기간 동안 임계값 아래로 떨어지면 경고가 트리거되는 10분 이상의 기간을 설정하는 것이 좋습니다. 1-5분 기간에는 메트릭이 계속 집계 중일 수 있으므로 경고를 표시하지 않는 것이 좋습니다.

메트릭 경고를 포털에서 구성하려면 다음을 수행합니다.

1. **모든 서비스** > **배치 계정**을 차례로 클릭한 다음, 배치 계정의 이름을 클릭합니다.
2. **모니터링**에서 **경고 규칙** > **메트릭 경고 추가**를 클릭합니다.
3. 메트릭, 경고 조건(예: 메트릭이 일정 기간 동안 특정 값을 초과하는 경우) 및 하나 이상의 알림을 선택합니다.

또한 [REST API](https://docs.microsoft.com/rest/api/monitor/)를 사용하여 거의 실시간으로 경고를 구성할 수 있습니다. 자세한 내용은 [경고 개요](../azure-monitor/platform/alerts-overview.md)를 참조하세요.

## <a name="batch-diagnostics"></a>일괄 처리 진단

진단 로그에는 각 리소스의 작업을 설명하는 Azure 리소스에서 내보낸 정보가 포함됩니다. 일괄 처리의 경우 다음 로그를 수집할 수 있습니다.

* 풀이나 작업 같은 개별 일괄 처리 리소스의 수명 주기 동안 Azure Batch 서비스가 내보내는 **서비스 로그**. 

* 계정 수준의 **메트릭** 로그. 

진단 로그의 컬렉션을 사용하도록 설정하는 것은 기본적으로 사용되지 않습니다. 모니터링할 각 배치 계정에 대해 진단 로그를 명시적으로 사용하도록 설정해야 합니다.

### <a name="log-destinations"></a>로그 대상

일반적인 시나리오는 로그 대상으로 Azure Storage 계정을 선택하는 것입니다. Azure Storage에 로그를 저장하려면 로그 컬렉션을 사용하도록 설정하기 전에 계정을 만듭니다. 저장소 계정을 배치 계정과 연결한 경우 해당 계정을 로그 대상으로 선택할 수 있습니다. 

진단 로그에 대한 다른 선택적 대상:

* 일괄 처리 진단 로그 이벤트를 [Azure Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md)로 스트리밍합니다. Event Hubs는 초당 수백 건의 이벤트를 수집하여 모든 실시간 분석 공급자를 통해 변환 및 저장할 수 있습니다. 

* 진단 로그를 보낼 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md), 하거나 수 있는 분석 하 여 Power BI 또는 Excel에서 분석을 위해 내보냅니다.

> [!NOTE]
> Azure 서비스를 사용하여 진단 로그 데이터를 저장하거나 처리하려면 추가 비용이 발생할 수 있습니다. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>일괄 처리 진단 로그의 컬렉션을 사용하도록 설정

1. 포털에서 **모든 서비스** > **배치 계정**을 차례로 클릭한 다음, 배치 계정의 이름을 클릭합니다.
2. **모니터링**에서 **진단 로그** > **진단 켜기**를 클릭합니다.
3. **진단 설정**설정에 대 한 이름을 입력 하 고 로그 대상 (기존 저장소 계정, Event Hub 또는 Azure Monitor 로그)를 선택 합니다. **ServiceLog**와 **AllMetrics** 중 하나를 선택하거나 두 개를 모두 선택합니다.

    저장소 계정을 선택하는 경우 필요에 따라 보존 정책을 설정합니다. 보존 일 수를 지정하지 않으면 데이터는 저장소 계정의 수명 동안 보존됩니다.

4. **저장**을 클릭합니다.

    ![일괄 처리 진단](media/batch-diagnostics/diagnostics-portal.png)

로그 컬렉션을 사용하도록 설정하는 다른 옵션으로는 포털에서 Azure Monitor를 사용하여 진단 설정을 구성하거나, [Resource Manager 템플릿](../azure-monitor/platform/diagnostic-logs-stream-template.md)을 사용하거나, Azure PowerShell 또는 Azure CLI를 사용하는 방법이 있습니다. [Azure 리소스에서 로그 데이터 수집 및 사용](../azure-monitor/platform/diagnostic-logs-overview.md#how-to-enable-collection-of-diagnostic-logs)을 참조하세요.


### <a name="access-diagnostics-logs-in-storage"></a>스토리지에서 진단 로그에 액세스

일괄 처리 진단 로그를 스토리지 계정에 보관하는 경우 관련된 이벤트가 발생하자마자 스토리지 계정에 스토리지 컨테이너가 만들어집니다. Blob은 다음과 같은 명명 패턴에 따라 생성됩니다.

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
예제:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
각 PT1H.json Blob 파일에는 Blob URL에 지정된 시간 내에서 발생한 JSON 형식의 이벤트가 포함됩니다(예: h=12). 현재 시간 동안 이벤트는 발생하는 순서대로 PT1H.json 파일에 추가됩니다. 진단 로그 이벤트는 시간당 개별 Blob으로 나뉘므로 분 값(m=00)은 항상 00입니다. (모든 시간은 UTC입니다.)


저장소 계정에서 진단 로그의 스키마에 대한 자세한 내용은 [Azure 진단 로그 보관](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account)을 참조하세요.

스토리지 계정에서 로그를 프로그래밍 방식으로 액세스하려면 Storage API를 사용합니다. 

### <a name="service-log-events"></a>서비스 로그 이벤트
Azure Batch 서비스 로그는 수집되는 경우 풀이나 작업 같은 개별 일괄 처리 리소스의 수명 주기 동안 Azure Batch 서비스가 내보낸 이벤트를 포함합니다. 일괄 처리에서 내보내는 각 이벤트는 JSON 형식으로 기록됩니다. 예를 들어 샘플 **풀 만들기 이벤트**의 본문은 다음과 같습니다.

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Batch 서비스는 현재 다음과 같은 서비스 로그 이벤트를 내보냅니다. 이 목록은 전체가 아니며, 이 문서가 마지막으로 업데이트된 뒤 다른 이벤트가 추가되었을 수 있습니다.

| **서비스 로그 이벤트** |
| --- |
| [풀 만들기](batch-pool-create-event.md) |
| [풀 삭제 시작](batch-pool-delete-start-event.md) |
| [풀 삭제 완료](batch-pool-delete-complete-event.md) |
| [풀 크기 조정 시작](batch-pool-resize-start-event.md) |
| [풀 크기 조정 완료](batch-pool-resize-complete-event.md) |
| [작업 시작](batch-task-start-event.md) |
| [작업 완료](batch-task-complete-event.md) |
| [작업 실패](batch-task-fail-event.md) |



## <a name="next-steps"></a>다음 단계

* Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
* [일괄 처리 솔루션 모니터링](monitoring-overview.md)에 대해 자세히 알아봅니다.
