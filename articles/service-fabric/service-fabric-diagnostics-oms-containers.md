---
title: Azure Monitor 로그로 컨테이너 모니터링
description: Azure Service Fabric 클러스터에서 실행 중인 컨테이너를 모니터링하려면 Azure Monitor 로그를 사용합니다.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626659"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Azure Monitor 로그로 컨테이너 모니터링
 
이 문서에서는 컨테이너 이벤트를 보기 위한 Azure Monitor 로그 컨테이너 모니터링 솔루션을 설정하는 데 필요한 단계를 다룹니다. 클러스터가 컨테이너 이벤트를 수집하도록 설정하려면 이 [단계별 자습서](service-fabric-tutorial-monitoring-wincontainers.md)를 참조하세요. 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>컨테이너 모니터링 솔루션 설정

> [!NOTE]
> 클러스터에 Azure Monitor 로그를 설정하고 노드에 Log Analytics 에이전트를 배포해야 합니다. 그렇지 않으면 [Azure Monitor 로그 설정](service-fabric-diagnostics-oms-setup.md) 및 [클러스터에 Log Analytics 에이전트 추가](service-fabric-diagnostics-oms-agent.md) 단계를 먼저 수행합니다.

1. 클러스터가 Azure Monitor 로그 및 Log Analytics 에이전트로 설정되었으면 컨테이너를 배포합니다. 다음 단계로 이동하기 전에 컨테이너가 배포될 때까지 기다립니다.

2. Azure Marketplace에서 *컨테이너 모니터링 솔루션* 을 검색하고 모니터링 + 관리 범주 아래에 나타나는 **컨테이너 모니터링 솔루션** 리소스를 클릭합니다.

    ![컨테이너 솔루션 추가](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 클러스터에 대해 이미 작성된 동일한 작업 영역 내부에 솔루션을 만듭니다. 이렇게 변경하면 컨테이너에서 docker 데이터 수집을 시작하는 에이전트가 자동으로 트리거됩니다. 아래 이미지에서처럼 약 15분 정도 로그와 통계가 들어오는 것으로 솔루션에 불이 들어옵니다.

    ![기본 Log Analytics 대시보드](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

에이전트를 사용하면 Azure Monitor 로그에서 쿼리하거나 성능 지표를 시각화하는 데 사용할 수 있는 몇 가지 컨테이너별 로그를 수집할 수 있습니다. 수집되는 로그 형식은 다음과 같습니다.

* ContainerInventory: 컨테이너 위치, 이름 및 이미지에 대한 정보를 표시합니다.
* ContainerImageInventory: ID 또는 크기를 포함하여 배포된 이미지에 대한 정보를 표시합니다.
* ContainerLog: 특정 오류 로그, docker 로그(stdout 등) 및 기타 항목을 표시합니다.
* ContainerServiceLog: 실행된 docker 디먼 명령을 표시합니다.
* Perf: 호스트 컴퓨터의 컨테이너 CPU, 메모리, 네트워크 트래픽, 디스크 I/O, 사용자 지정 메트릭을 포함하는 성능 카운터를 표시합니다.



## <a name="next-steps"></a>다음 단계
* [Azure Monitor 로그 컨테이너 솔루션](../azure-monitor/containers/containers.md)에 대해 자세히 알아보세요.
* Service Fabric - [Service Fabric 및 컨테이너](service-fabric-containers-overview.md)에서 컨테이너 오케스트레이션에 대해 자세히 알아보세요.
* Azure Monitor 로그의 일부로 제공되는 [로그 검색 및 쿼리](../azure-monitor/logs/log-query-overview.md) 기능을 알아봅니다.
* 검색 및 진단에 도움이 되는 [자동 경고](../azure-monitor/alerts/alerts-overview.md) 규칙을 설정하도록 Azure Monitor 로그를 구성합니다.
