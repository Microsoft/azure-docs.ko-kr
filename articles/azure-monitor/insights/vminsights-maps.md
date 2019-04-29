---
title: VM용 Azure Monitor(미리 보기)를 사용하여 앱 종속성을 보는 방법 | Microsoft Docs
description: 맵은 Windows 및 Linux 시스템의 애플리케이션 구성 요소를 자동으로 검색하고 서비스 간 통신을 매핑하는 VM용 Azure Monitor 기능입니다. 이 문서에서는 다양한 시나리오에서 이 기능을 사용하는 방법에 대해 자세하게 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 34e6ce7f3b38dfd583aa557d2f1d7340ea444da9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115777"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>VM용 Azure Monitor(미리 보기) 맵을 사용하여 애플리케이션 구성 요소 이해
Azure에서 실행 중인 Windows 및 Linux 가상 머신에서 검색된 애플리케이션 구성 요소를 보고 VM용 Azure Monitor를 사용하여 가상 머신에서 직접 또는 Azure Monitor에서 VM 그룹을 통해서와 같이 두 가지 방법으로 환경을 관찰할 수 있습니다. 

이 문서는 이러한 두 관점에서 환경 및 맵 기능을 사용하는 방법을 이해하는 데 도움이 됩니다. VM용 Azure Monitor를 구성하는 방법에 대한 자세한 내용은 [VM용 Azure Monitor 사용하도록 설정](vminsights-onboard.md)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="introduction-to-map-experience"></a>맵 환경 소개
단일 가상 머신 또는 VM 그룹에 대한 맵 보기를 살펴보기 전에 정보를 제공하는 방법 및 시각화에서 나타내는 의미를 파악할 수 있도록 해당 기능에 대해 간략하게 소개해야 합니다.  

VM에서 직접 또는 Azure Monitor에서 맵 기능을 선택하는 경우 일관된 환경을 나타냅니다.  Azure Monitor에서는 하나의 맵에 있는 다중 계층 애플리케이션 또는 클러스터의 모든 멤버를 확인할 수 있다는 것이 유일한 차이점입니다.

맵에서는 지정된 시간 범위 동안 TCP 연결 아키텍처를 통해 서버, 인바운드 및 아웃바운드 연결 대기 시간과 포트 간에 활성 네트워크 연결에서 실행 중인 프로세스를 검색하여 VM 종속성을 시각화합니다.  VM을 확장하면 프로세스 세부 정보를 표시하고 VM과 통신하는 프로세스만 표시됩니다. 가상 머신에 연결된 프런트 엔드 클라이언트 수는 클라이언트 그룹에서 표시됩니다. VM을 연결할 백 엔드 서버 수는 서버 포트 그룹에 표시됩니다. 서버 포트 그룹을 확장하여 해당 포트를 통해 연결된 서버의 자세한 목록을 확인합니다.  

가상 머신을 클릭하면 오른쪽에서 **속성** 창이 확장되여 운영 체제에서 보고된 시스템 정보, Azure VM의 속성 및 검색된 요약을 요약하는 도넛형 차트 등 선택된 항목의 속성을 표시합니다. 

![컴퓨터의 시스템 속성](./media/vminsights-maps/properties-pane-01.png)

오른쪽 창에서 클릭 하 여 **로그 이벤트** VM에서 수집 된 데이터는 테이블의 목록을 표시 하려면 창의 포커스를 전환 하려면 아이콘 Azure Monitor에 전송한 되며 쿼리에 사용할 수 있습니다.  나열된 레코드 형식 중 하나를 클릭하면 **로그** 페이지를 열어서 특정 가상 머신에 대해 필터링된 미리 구성된 쿼리를 사용하여 해당 형식에 대한 결과를 봅니다.  

![속성 창의 로그 검색 목록](./media/vminsights-maps/properties-pane-logs-01.png)

닫기 **로그** 돌아가서 합니다 **속성** 창과 선택 **경고** 경고를 확인할 경고 상태 조건에서 VM에 대해 발생 합니다. 맵은 Azure 경고와 통합되어 선택한 시간 범위에서 선택한 서버에 대해 발생된 경고를 표시합니다. 현재 경고가 발생하면 서버에 아이콘이 표시되고 머신 경고 창에 경고가 나열됩니다. 

![속성 창의 머신 경고](./media/vminsights-maps/properties-pane-alerts-01.png)

맵 기능에서 관련 경고를 표시하려면 특정 컴퓨터에 대해 실행되는 경고 규칙을 만듭니다. 적절한 경고를 만들려면
- 컴퓨터별로 그룹화할 절(예, **by Computer interval 1 minute**)을 포함합니다.
- 미터법을 기준으로 경고하도록 선택

Azure 경고 및 경고 규칙 만들기에 대한 자세한 내용은 [Azure Monitor의 통합 경고](../../azure-monitor/platform/alerts-overview.md)를 참조하세요.

오른쪽 위 모서리에서 **범례** 옵션은 맵의 기호 및 역할을 설명합니다.  맵을 좀 더 자세히 살펴보고 사용해 보기 위해 페이지의 오른쪽 아래에 있는 확대/축소 컨트롤에서는 확대/축소 수준을 설정하고 현재 페이지 크기로 페이지를 맞춥니다.  

## <a name="connection-metrics"></a>연결 메트릭
**연결** 창에는 TCP 포트를 통해 VM에서 선택한 연결에 대한 표준 연결 메트릭이 표시됩니다. 메트릭에는 응답 시간, 분당 요청, 트래픽 처리량 및 링크가 포함됩니다.  

![네트워크 연결 차트 창 예제](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>실패한 연결
실패한 연결은 프로세스 및 컴퓨터에 대한 맵에 표시됩니다. 여기에서 빨간색 점선은 클라이언트 시스템이 프로세스 또는 포트에 도달하는 데 실패했음을 나타냅니다. 시스템에서 실패한 연결을 시도하는 경우 종속성 에이전트를 사용하여 해당 시스템의 실패한 연결이 보고됩니다. 맵은 연결 설정에 실패한 TCP 소켓을 관찰하여 이 프로세스를 측정합니다. 이러한 실패는 방화벽, 클라이언트 또는 서버의 잘못된 구성 또는 원격 서비스를 사용할 수 없기 때문에 발생할 수 있습니다.

![맵에서 실패한 연결 예제](./media/vminsights-maps/map-group-failed-connection-01.png)

실패한 연결을 이해하면 서비스의 전체 아키텍처에서 문제 해결, 마이그레이션 유효성 검사, 보안 분석 및 이해에 도움이 됩니다. 실패한 연결이 문제가 되지 않을 때도 있지만 갑자기 장애 조치(failover) 환경에 도달할 수 없거나 두 개의 애플리케이션 계층이 클라우드 마이그레이션 후에 서로 의사 소통할 수 없는 등과 같은 문제를 직접적으로 나타내는 경우가 있습니다.

### <a name="client-groups"></a>클라이언트 그룹
맵의 클라이언트 그룹은 매핑된 머신에 연결된 클라이언트 머신을 나타냅니다. 단일 클라이언트 그룹은 개별 프로세스 및 컴퓨터에 대한 클라이언트를 나타냅니다.

![맵의 클라이언트 그룹 예제](./media/vminsights-maps/map-group-client-groups-01.png)

클라이언트 그룹에서 시스템의 모니터링된 클라이언트 및 서버 IP 주소를 확인하려면 그룹을 선택합니다. 그룹의 내용은 그룹 아래에 나열됩니다.  

![맵의 클라이언트 그룹 IP 주소 목록 예제](./media/vminsights-maps/map-group-client-group-iplist-01.png)

그룹에 모니터링된 클라이언트 및 모니터링되지 않은 클라이언트가 포함된 경우 클라이언트를 필터링하기 위해 그룹에서 도넛형 차트의 해당 섹션을 선택할 수 있습니다.

### <a name="server-port-groups"></a>서버 포트 그룹
서버 포트 그룹은 매핑된 머신의 인바운드 연결된 서버에서 서버 포트를 나타냅니다. 이 그룹에는 서버 포트와 이 포트에 연결된 서버 수가 표시됩니다. 나열된 개별 서버 및 연결을 보려면 그룹을 선택합니다. 

![맵의 서버 포트 그룹 예제](./media/vminsights-maps/map-group-server-port-groups-01.png)  

그룹에 모니터링된 서버 및 모니터링되지 않은 서버가 포함된 경우 서버를 필터링하기 위해 그룹에서 도넛형 차트의 해당 섹션을 선택할 수 있습니다.

## <a name="view-map-directly-from-a-virtual-machine"></a>가상 머신에서 직접 맵 보기 

가상 머신에서 직접 VM용 Azure Monitor에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 **Virtual Machines**를 선택합니다. 
2. 목록에서 VM을 선택하고 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.  
3. **맵** 탭을 선택합니다.

맵은 지정된 시간 범위 동안 VM 종속성, 즉, 실행 중인 프로세스 그룹 및 활성 네트워크 연결을 사용하는 프로세스 등을 시각화합니다.  기본적으로 맵은 최근 30분을 보여줍니다.  왼쪽 위 모서리에서 **TimeRange** 선택기를 사용하여 최대 1시간이라는 기록 시간 범위에 대해 쿼리하여 과거의 종속성(예: 인시던트 중 또는 변경되기 전)을 보여줄 수 있습니다.  

![직접 VM 맵 개요](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Azure Monitor에서 맵 보기
Azure Monitor에서 맵 기능은 가상 머신 및 해당 종속성의 글로벌 보기를 제공합니다.  Azure Monitor에서 맵 기능에 액세스하려면 다음을 수행합니다. 

1. Azure Portal에서 **모니터**를 선택합니다. 
2. **인사이트** 섹션에서 **Virtual Machines(미리 보기)** 를 선택합니다.
3. **맵** 탭을 선택합니다.

![Azure Monitor 다중 VM 맵 개요](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

페이지 위쪽의 **작업 영역** 선택기에서 Log Analytics 작업 영역이 둘 이상 있는 경우 솔루션에서 사용하도록 설정되고 가상 머신에서 보고하는 작업 영역을 선택합니다. **그룹** 선택기에서 선택한 작업 영역과 관련된 컴퓨터의 구독, 리소스 그룹, [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md) 및 VM 확장 집합을 반환합니다. 선택 항목은 맵 기능에만 적용되며 성능 또는 맵으로 전달되지 않습니다.

기본적으로 맵은 최근 30분을 보여줍니다. **TimeRange** 선택기를 사용하여 최대 1시간이라는 기록 시간 범위에 대해 쿼리하여 과거의 종속성(예: 인시던트 중 또는 변경되기 전)을 보여줄 수 있습니다.   

## <a name="next-steps"></a>다음 단계
상태 기능을 사용하는 방법을 알아보려면 [Azure VM 상태 보기](vminsights-health.md)를 참조하세요. 병목 상태 및 전체 VM 성능 사용률을 식별하려면 [VM용 Azure Monitor 성능 보기](vminsights-performance.md)를 참조하세요. 
