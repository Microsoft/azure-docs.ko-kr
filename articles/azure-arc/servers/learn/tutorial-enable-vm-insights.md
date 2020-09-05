---
title: 자습서 - VM용 Azure Monitor를 사용하여 하이브리드 머신 모니터링
description: Azure Monitor에서 하이브리드 머신의 데이터를 수집하고 분석하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 08/12/2020
ms.openlocfilehash: 76df7d403fdce6fc3ac77c0b24849aedffb57ce0
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213169"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-azure-monitor-for-vms"></a>자습서: VM용 Azure Monitor를 사용하여 하이브리드 머신 모니터링

[Azure Monitor](../overview.md)는 상세한 분석 및 상관 관계 파악을 위해 하이브리드 가상 머신 데이터를 Log Analytics 작업 영역으로 직접 수집할 수 있습니다. 일반적으로 이는 구성 관리 표준에 따라 스크립트, 수동으로 또는 자동화된 방법을 사용하여 머신에 [Log Analytics 에이전트](../../../azure-monitor/platform/agents-overview.md#log-analytics-agent)를 설치해야 합니다. Arc 사용 서버(미리 보기)는 최근에 Windows 및 Linux용 Log Analytics 및 종속성 에이전트 [VM 확장](../manage-vm-extensions.md)을 설치하는 기능을 지원하여 Azure Monitor가 Azure가 아닌 VM에서 데이터를 수집할 수 있도록 합니다.

이 자습서에서는 간단한 단계 집합에 따라 VM용 Azure Monitor를 사용하도록 설정하여 Linux 또는 Windows VM에서 데이터를 구성하고 수집하는 방법을 보여 줍니다. 이는 환경을 간소화하고 시간을 단축하는 데 사용됩니다.  

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* VM 확장 기능은 [지원되는 지역](../overview.md#supported-regions) 목록에서만 사용할 수 있습니다.

* 사용하도록 설정하는 VM 운영 체제가 VM용 Azure Monitor에서 지원되는지 확인하려면 [지원되는 운영 체제](../../../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)를 참조하세요.

* [Log Analytics 에이전트 개요](../../../azure-monitor/platform/log-analytics-agent.md#network-requirements)에 제공된 Log Analytics 에이전트에 대한 방화벽 요구 사항을 검토합니다. VM용 Azure Monitor 맵 Dependency Agent는 데이터 자체를 전송하지 않으며 방화벽 또는 포트를 변경하지 않아도 됩니다.

## <a name="sign-in-to-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="enable-azure-monitor-for-vms"></a>VM용 Azure Monitor 사용

1. **모든 서비스**를 클릭한 다음, **머신 - Azure Arc**를 검색하고 선택하여 Azure Portal에서 Azure Arc 서비스를 시작합니다.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="모든 서비스에서 Arc 사용 서버 검색" border="false":::

1. **머신 - Azure Arc** 페이지에서 [빠른 시작](quick-enable-hybrid-vm.md) 문서에서 만든 연결된 머신을 선택합니다.

1. **모니터링** 섹션 아래의 왼쪽 창에서 **인사이트**를 선택한 다음, **사용**을 선택합니다.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="왼쪽 메뉴에서 인사이트 옵션 선택" border="false":::

1. Azure Monitor **인사이트 온보딩** 페이지에 작업 영역을 만들라는 메시지가 표시됩니다. 이 자습서에서는 기존 Log Analytics 작업 영역(이미 있는 경우)을 선택하지 않는 것이 좋습니다. 등록된 연결된 머신과 동일한 지역에서 고유한 이름을 사용하는 작업 영역인 기본값을 선택합니다. 이 작업 영역은 생성되고 구성됩니다.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="VM용 Azure Monitor 사용 페이지" border="false":::

1. 구성을 수행하는 동안 상태 메시지를 수신합니다. 이 프로세스는 연결된 머신에 확장을 설치하는 데 몇 분 정도 걸립니다.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="VM용 Azure Monitor 사용 진행률 상태 메시지" border="false":::

    완료되면 머신이 성공적으로 등록되고 인사이트가 성공적으로 배포되었음을 나타내는 메시지가 표시됩니다.

## <a name="view-data-collected"></a>수집되는 데이터 보기

배포 및 구성이 완료된 후 **인사이트**를 선택한 다음, **성능** 탭을 선택합니다. 성능 탭에 VM의 게스트 운영 체제에서 수집된 일부 성능 카운터 그룹이 표시됩니다. 아래로 스크롤하여 더 많은 카운터를 표시하고, 그래프 위로 마우스를 이동하여 Log Analytics VM 확장이 머신에 설치된 시간부터 시작되는 평균 및 백분위수를 확인합니다.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="선택한 머신에 대한 VM용 Azure Monitor 성능 차트" border="false":::

**맵**을 선택하여 가상 머신에서 실행 중인 프로세스와 해당 종속성을 보여 주는 맵 기능을 엽니다. 아직 속성 창을 열지 않았으면 **속성**을 선택하여 속성 창을 엽니다.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="선택한 머신에 대한 VM용 Azure Monitor 맵" border="false":::

가상 머신의 프로세스를 확장합니다. 프로세스 중 하나를 선택하여 세부 정보를 확인하고 해당 종속성을 강조 표시합니다.

가상 머신을 다시 선택한 다음, **로그 이벤트**를 선택합니다. 가상 머신의 Log Analytics 작업 영역에 저장된 테이블 목록이 표시됩니다. 이 목록은 Windows 가상 머신을 사용하는지 아니면 Linux 가상 머신을 사용하는지에 따라 달라집니다. **이벤트** 테이블을 선택합니다. **이벤트** 테이블에는 Windows 이벤트 로그의 모든 이벤트가 포함됩니다. 수집된 이벤트 로그 항목을 검색하는 간단한 쿼리와 함께 Log Analytics가 열립니다.

## <a name="next-steps"></a>다음 단계

Azure Monitor에 대해 자세히 알아보려면 다음 문서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Monitor 개요](../../../azure-monitor/overview.md)