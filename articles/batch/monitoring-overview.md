---
title: Azure Batch 모니터링 | Microsoft Docs
description: Azure Batch를 위한 Azure 모니터링 서비스, 메트릭, 진단 로그 및 기타 모니터링 기능에 대해 알아봅니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: b0243b37f725fc977337b72998d610e9bda71a86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128858"
---
# <a name="monitor-batch-solutions"></a>Batch 솔루션 모니터링

Azure 및 Batch 서비스는 Batch 솔루션을 모니터링하는 다양한 서비스, 도구 및 API를 제공합니다. 이 개요 문서에서는 요구 사항에 적합한 모니터링 방법을 선택하도록 도와줍니다.

Azure 리소스 모니터링에 사용할 수 있는 Azure 구성 요소 및 서비스에 대한 개요는 [Azure 애플리케이션 및 리소스 모니터링](../monitoring-and-diagnostics/monitoring-overview.md)을 참조하세요.

## <a name="subscription-level-monitoring"></a>구독 수준 모니터링

Batch 계정을 포함하는 구독 수준에서, [Azure 활동 로그](../azure-monitor/platform/activity-logs-overview.md)는 [여러 범주](../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log)에서 작업 이벤트 데이터를 수집합니다.

특히 Batch 계정의 경우 활동 로그는 계정 생성/삭제 및 키 관리와 관련된 이벤트를 수집합니다.

활동 로그에서 이벤트를 검색하는 한 가지 방법은Azure Portal을 사용하는 것입니다. **모든 서비스** > **활동 로그**를 클릭합니다. 또는 Azure CLI, PowerShell cmdlet 또는 Azure Monitor REST API를 사용하여 이벤트를 쿼리합니다. 활동 로그를 내보내거나 [활동 로그 경고](../monitoring-and-diagnostics/monitoring-activity-log-alerts-new-experience.md)를 구성할 수도 있습니다.

## <a name="batch-account-level-monitoring"></a>Batch 계정 수준 모니터링

[Azure Monitor](../azure-monitor/overview.md)의 기능을 사용하여 각 Batch 계정을 모니터링합니다. Azure Monitor는 Batch 계정 수준에서 범위가 지정된 풀, 작업, 태스크 등의 리소스에 대한 [메트릭](../azure-monitor/platform/data-platform-metrics.md), 그리고 필요에 따라 [진단 로그](../azure-monitor/platform/diagnostic-logs-overview.md)를 수집합니다. 이 데이터를 수동으로 또는 프로그래밍 방식으로 수집하고 사용하여 Batch 계정의 활동을 모니터링하고 문제를 진단할 수 있습니다. 자세한 내용은 [진단 평가 및 모니터링을 위한 Batch 메트릭, 경고 및 로그](batch-diagnostics.md)를 참조하세요.
 
> [!NOTE]
> 메트릭은 추가 구성 없이 Batch 계정에서 기본적으로 사용할 수 있으며 30일의 롤링 기록이 있습니다. Batch 계정에 진단 로깅을 사용하도록 설정해야 하며, 진단 로그 데이터를 저장 또는 처리하는 추가 비용이 발생할 수 있습니다. 

## <a name="batch-resource-monitoring"></a>Batch 리소스 모니터링

Batch 애플리케이션에서 Batch API를 사용하여 작업, 태스크, 노드, 풀을 포함한 리소스 상태를 모니터링하거나 쿼리할 수 있습니다. 예를 들면 다음과 같습니다.

* [상태별 태스크 및 계산 노드 카운팅](batch-get-resource-counts.md)
* [Batch 리소스를 효율적으로 나열하는 쿼리 만들기](batch-efficient-list-queries.md)
* [태스크 종속성 만들기](batch-task-dependencies.md)
* [작업 관리자 태스크](/rest/api/batchservice/job/add#jobmanagertask) 사용
* [태스크 상태](/rest/api/batchservice/task/list#taskstate) 모니터링
* [노드 상태](/rest/api/batchservice/computenode/list#computenodestate) 모니터링
* [풀 상태](/rest/api/batchservice/pool/get#poolstate) 모니터링
* [계정의 풀 사용량](/rest/api/batchservice/pool/listusagemetrics) 모니터링
* [상태별로 풀 노드 집계](/rest/api/batchservice/account/listpoolnodecounts)

## <a name="vm-performance-counters-and-application-monitoring"></a>VM 성능 카운터 및 애플리케이션 모니터링

* [Application Insights](../azure-monitor/app/app-insights-overview.md)는 Batch 작업 및 태스크의 가용성, 성능, 사용량을 프로그래밍 방식으로 모니터링하는 데 사용할 수 있는 Azure 서비스입니다. 간편하게 계산 노드(VM)에서 성능 카운터를 가져오고 VM에서 태스크에 대한 사용자 지정 정보를 얻을 수 있습니다. 

  예를 들어 [Application Insights를 사용하여 Batch .NET 애플리케이션 모니터링 및 디버그](monitor-application-insights.md) 및 함께 제공되는 [코드 샘플](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)을 참조하세요.

  > [!NOTE]
  > Application Insights를 사용하는 추가 비용이 발생할 수 있습니다. [가격 책정 옵션](https://azure.microsoft.com/pricing/details/application-insights/)을 참조하세요. 
  >

* [Batch Explorer](https://github.com/Azure/BatchExplorer)는 Azure Batch 애플리케이션을 만들고, 디버그하고, 모니터링할 수 있도록 하는 무료의 풍부한 기능을 가진 독립 실행형 클라이언트 도구입니다. Mac, Linux 또는 Windows의 경우 [설치 패키지](https://azure.github.io/BatchExplorer/)를 다운로드합니다. 필요에 따라 Batch Explorer의 VM 성능 카운터 같은 [Application Insights 데이터를 표시](https://github.com/Azure/batch-insights)하도록 Batch 솔루션을 구성합니다.


## <a name="next-steps"></a>다음 단계

* Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
* Batch를 사용하는 [진단 로깅](batch-diagnostics.md)에 대해 자세히 알아봅니다.