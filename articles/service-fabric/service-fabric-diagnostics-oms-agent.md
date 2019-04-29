---
title: Azure Service Fabric-Azure Monitor를 사용 하 여 성능 모니터링 기록 | Microsoft Docs
description: Azure Service Fabric 클러스터의 컨테이너 및 성능 카운터를 모니터링하기 위해 Log Analytics 에이전트를 설정하는 방법에 대해 알아보세요.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 819f6ee4ab079361279a567bceeb74c33fe14186
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952388"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용 하 여 성능 모니터링

이 문서에서는 Log Analytics 에이전트를 가상 머신 확장 집합의 확장 기능으로 클러스터에 추가하고 이를 기존의 Azure Log Analytics 작업 영역에 연결하는 단계를 설명합니다. 이를 통해 컨테이너, 애플리케이션 및 성능 모니터링에 대한 진단 데이터를 수집할 수 있습니다. 이것을 가상 머신 확장 집합 리소스에 확장으로 추가하면 Azure Resource Manager는 클러스터 크기 조정 시에도 해당 항목이 모든 노드에 설치되도록 보장합니다.

> [!NOTE]
> 이 문서는 Azure Log Analytics 작업 영역이 이미 설정되었다고 가정합니다. 이렇게 하지 않으면 경우 [Azure Monitor 로그 설정](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Azure CLI를 통해 에이전트 확장 추가

Log Analytics 에이전트를 클러스터에 추가하는 가장 좋은 방법은 Azure CLI로 제공되는 가상 머신 확장 집합 API를 통하는 것입니다. 아직 Azure CLI가 설정되지 않은 경우 Azure Portal로 이동하여 [Cloud Shell](../cloud-shell/overview.md) 인스턴스를 열거나 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)합니다.

1. Cloud Shell이 요청되면 리소스와 동일한 구독에서 작업 중인지 확인합니다. `az account show`로 이를 확인하여 "name" 값이 클러스터 구독의 값과 일치하는지 확인합니다.

2. 포털에서 Log Analytics 작업 영역이 있는 리소스 그룹으로 이동합니다. Log analytics 리소스 (Log Analytics 작업 영역 리소스 유형의 수는)를 클릭 합니다. 리소스 개요 페이지에서 왼쪽 메뉴의 설정 섹션 아래에 있는 **고급 설정**을 클릭합니다.

    ![Log analytics 속성 페이지](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Windows 클러스터를 준비하는 경우 **Windows 서버**를 클릭하고 Linux 클러스터를 만드는 경우 **Linux 서버**를 클릭합니다. 이 페이지에는 `workspace ID` 및 `workspace key`(포털에 기본 키로 나열됨)가 표시됩니다. 두 가지 모두 다음 단계에서 필요합니다.

4. 명령을 실행하여 Cloud Shell에서 `vmss extension set` API를 사용하여 클러스터에 Log Analytics 에이전트를 설치합니다.

    Windows 클러스터의 경우:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux 클러스터의 경우:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    다음은 Windows 클러스터에 Log Analytics 에이전트를 추가하는 예입니다.

    ![Log Analytics 에이전트 cli 명령](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. 에이전트를 노드에 성공적으로 추가하기 위해서는 15분 미만이 소요됩니다. `az vmss extension list` API를 사용하여 에이전트가 추가되었는지 확인할 수 있습니다.

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Resource Manager 템플릿을 통해 에이전트 추가

Azure Log Analytics 작업 영역을 배포하고 각 노드에 에이전트를 추가하는 샘플 Resource Manager 템플릿은 [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) 또는 [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux)에서 사용할 수 있습니다.

이 템플릿을 다운로드하고 수정하여 필요에 가장 적합한 클러스터를 배포할 수 있습니다.

## <a name="view-performance-counters"></a>성능 카운터 보기

Log Analytics 에이전트를 추가했으니, Log Analytics 포털로 이동하여 수집하려는 성능 카운터를 선택합니다.

1. Azure Portal에서 Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. **ServiceFabric\<nameOfLog AnalyticsWorkspace\>** 를 선택합니다.

2. **Log Analytics**를 클릭합니다.

3. **고급 설정**을 클릭합니다.

4. **데이터**를 클릭한 다음, **Windows 또는 Linux 성능 카운터**를 클릭합니다. 사용하도록 선택할 수 있는 기본 카운터의 목록이 있고 컬렉션 간격도 설정할 수 있습니다. 수집할 [성능 카운터를 더 추가](service-fabric-diagnostics-event-generation-perf.md)할 수도 있습니다. 올바른 형식은 이 [문서에](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx) 설명되어 있습니다.

5. **저장**을 클릭한 다음, **확인**을 클릭합니다.

6. 고급 설정 블레이드를 닫습니다.

7. 일반 제목에서 **작업 영역 요약**을 클릭합니다.

8. Service Fabric용 타일을 포함하여 활성화된 각 솔루션에 대해 그래프 형태의 타일이 표시됩니다. **Service Fabric** 그래프를 클릭하여 Service Fabric 분석 솔루션으로 이동합니다.

9. 작동 채널 및 Reliable Services 이벤트에 대한 그래프가 있는 타일이 몇 개 표시됩니다. 선택한 카운터에 대한 데이터 흐름의 그래픽 표현이 노드 메트릭 아래에 표시됩니다.

10. 컨테이너 메트릭 그래프를 클릭하여 추가 정보를 확인합니다. 성능 카운터 데이터를 클러스터 이벤트와 유사하게 쿼리하고 Kusto 쿼리 언어를 사용하여 노드, 성능 카운터 이름 및 값을 필터링할 수도 있습니다.

![Log Analytics 성능 카운터 쿼리](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>다음 단계

* 관련 [성능 카운터](service-fabric-diagnostics-event-generation-perf.md)를 수집합니다. Log Analytics 에이전트를 구성하여 특정 성능 카운터를 수집하려면 [데이터 원본 구성](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)을 검토합니다.
* Azure Monitor 로그 설정 구성 [자동 경고](../log-analytics/log-analytics-alerts.md) 감지 및 진단에 도움이 되
* 또는 [Azure Diagnostics 확장을 통해 성능 카운터를 수집하여 Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)에 보낼 수 있습니다.
