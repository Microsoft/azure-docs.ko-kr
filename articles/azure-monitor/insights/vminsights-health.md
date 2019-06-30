---
title: Azure virtual machines의 상태를 이해 | Microsoft Docs
description: 이 문서에서는 Vm에 대 한 virtual machines 및 Azure Monitor를 사용 하 여 기본 운영 체제의 상태를 이해 하는 방법을 설명 합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340134"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>Azure virtual machines의 상태를 이해 합니다.

Azure는 모니터링 공간에서 특정 역할이 나 작업에 대 한 서비스를 포함 하지만 Azure virtual machines (Vm)에서 호스트 운영 체제 (Os)의 자세한 상태 큐브 뷰를 제공 하지 않습니다. 다른 조건에 대 한 Azure Monitor를 사용 하 여 있지만 기능이 없는 모델 및 핵심 구성 요소 상태 또는 Vm의 전반적인 상태를 나타냅니다.

Vm 상태에 대 한 Azure Monitor를 통해 Windows 또는 Linux 게스트 OS의 성능과 가용성을 적극적으로 모니터링할 수 있습니다. 상태 모니터링 기능 구성 요소 상태를 측정 하는 방법을 지정 하 고 비정상 상태를 감지한 경우 경고를 전송 하는 주요 구성 요소 및 해당 관계를 나타내며, 조건을 제공 하는 모델을 사용 합니다.

Azure VM과 기본 OS의 전반적인 상태를 보는 두 가지 관점에서 관찰할 수 있습니다: Azure Monitor에서 리소스 그룹의 모든 vm 또는 VM에서 직접.

이 문서에 신속 하 게 평가, 조사 및 Vm 상태 기능에 대 한 Azure Monitor에서 감지 된 경우 상태 문제를 해결 하는 방법을 보여 줍니다.

VM용 Azure Monitor를 구성하는 방법에 대한 자세한 내용은 [VM용 Azure Monitor 사용하도록 설정](vminsights-enable-overview.md)을 참조하세요.

## <a name="monitoring-configuration-details"></a>모니터링 구성 세부 정보

이 섹션에서는 Azure Windows 및 Linux Vm을 모니터링 하는 기본 상태 조건을 설명 합니다. 모든 상태 조건을 비정상 상태를 감지할 때 경고를 전송 하도록 미리 구성 됩니다.

### <a name="windows-vms"></a>Windows VM

- 메모리의 사용 가능 메가바이트
- 쓰기(논리 디스크)당 평균 디스크 시간(초)
- 쓰기(디스크)당 평균 디스크 시간(초)
- 읽기당 평균 논리적 디스크 시간(초)
- 전송당 평균 논리적 디스크 시간(초)
- 읽기당 평균 디스크 시간(초)
- 전송당 평균 디스크 시간(초)
- 현재 디스크 큐 길이(논리 디스크)
- 현재 디스크 큐 길이(디스크)
- 디스크의 유휴 시간 비율
- 파일 시스템 오류 또는 손상
- 논리적 디스크에서 사용 가능한 공간 (%) 낮음
- 논리적 디스크에서 사용 가능한 공간 (MB) 낮음
- 논리적 디스크의 유휴 시간 비율
- 초당 메모리 페이지 수
- 읽기에 사용된 대역폭 비율
- 총 사용된 대역폭 비율
- 쓰기에 사용된 대역폭 비율
- 사용 중인 커밋된 메모리 비율
- 디스크의 유휴 시간 비율
- DHCP 클라이언트 서비스 상태
- DNS 클라이언트 서비스 상태
- RPC 서비스 상태
- 서버 서비스 상태
- 총 CPU 사용 백분율
- Windows 이벤트 로그 서비스 상태
- Windows 방화벽 서비스 상태
- Windows 원격 관리 서비스 상태

### <a name="linux-vms"></a>Linux VM

- 디스크 평균 디스크 초/전송
- 디스크 평균 디스크 초/읽기
- 디스크 평균 디스크 초/쓰기
- 디스크 상태
- 논리적 디스크에서 사용 가능한 공간
- 논리적 디스크에서 사용 가능한 공간 비율
- 논리적 디스크에서 사용 가능한 inode 비율
- 네트워크 어댑터 상태
- 총 프로세서 시간 비율
- 운영 체제에서 사용 가능한 메모리의 메가바이트

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

로그인 하려면로 이동 합니다 [Azure portal](https://portal.azure.com)합니다.

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Vm 상태에 대 한 Azure Monitor 소개

단일 VM 또는 Vm 그룹에 대 한 상태 모니터링 기능을 사용 하기 전에는 정보 제공 되는 방식은 및 시각화 나타내는 의미를 이해 해야 합니다.

### <a name="view-health-directly-from-a-vm"></a>VM에서 직접 상태 보기

Azure VM의 상태를 보려면 선택 **Insights (미리 보기)** VM의 왼쪽된 창에서. VM insights 페이지에는 **상태** 탭이 기본적으로 열려 있고 VM의 상태 보기를 표시 합니다.

![선택한 Azure 가상 머신에 대한 VM용 Azure Monitor 상태 개요](./media/vminsights-health/vminsights-directvm-health.png)

에 **상태** 탭의 **게스트 VM 상태**, VM의 상태를 표시 하는 테이블 및 총 VM 상태 경고는 비정상적인 구성 요소에 의해 발생 합니다.

자세한 내용은 [경고](#alerts)를 참조하세요.

다음 표에는 VM에 대해 정의된 상태가 나와 있습니다.

|아이콘 |성능 상태 |의미 |
|-----|-------------|---------------|
| |Healthy |정의 된 상태 조건 내에서 VM이 있습니다. 이 상태는 문제가 없는지 감지 하 고 VM을 정상적으로 작동 있습니다 나타냅니다. 부모 롤업 모니터를 사용 하 여 상태를 롤업하여 자식 최상의 또는 최악의 상태를 반영 합니다.|
| |중요 |하나를 나타내는 정의한 상태 조건 내에서 상태 없거나 더 중요 한 문제가 발견 되었습니다. 일반 기능을 복원 하려면 이러한 문제를 해결 해야 합니다. 부모 롤업 모니터를 사용 하 여 상태를 롤업 하며 자식 최상의 또는 최악의 상태를 반영 합니다.|
| |Warning |상태가 경고 상태와 다른 나타냅니다 위험 상태 (세 가지 상태 임계값을 구성할 수 있습니다.), 또는 경우는 중요 하지 않은 경우 발생할 수 있습니다 중요 한 문제 나타냅니다 하나 정의한 상태 조건에 대 한 두 임계값 사이 확인 되지 않은 합니다. 부모 롤업 모니터를 사용 하 여 하나 이상의 자식이 경고 상태에 있으면 부모는 상태를 반영을 경고 합니다. 하나의 자식 모니터가 위험 상태 및 경고 상태에 다른 자식에 있으면 부모 롤업 성능 상태가 위험으로 표시 됩니다.|
| |알 수 없음 |여러 가지 이유로 상태를 계산할 수 없습니다. 다음 섹션에는 추가 세부 정보 및 가능한 해결 방법을 제공합니다. |

알 수 없는 성능 상태를 다음 문제로 인해 발생할 수 있습니다.

- 에이전트를 다시 구성 하 고 Vm에 대 한 Azure Monitor를 사용 하는 경우 보고서 작업 영역을 지정 하는 더 이상. 작업 영역 참조를 보고 하도록 에이전트를 구성 하려면 [추가 또는 제거 하려면 작업 영역](../platform/agent-manage.md#adding-or-removing-a-workspace)합니다.
- VM이 삭제 됩니다.
- Vm에 대 한 Azure Monitor와 사용 하 여 연결 된 작업 영역 삭제 되었습니다. 프리미어 지원 혜택을 사용 하는 경우 작업 영역을 복구할 수 있습니다. 로 이동 [프리미어](https://premier.microsoft.com/) 지원 요청을 엽니다.
- 솔루션 종속성이 삭제 되었습니다. Log Analytics 작업 영역에서은 ServiceMap 및 InfrastructureInsights 솔루션을 다시 활성화 하려면 사용 하 여 이러한 솔루션을 다시 설치 합니다 [Azure Resource Manager 템플릿을](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions)합니다. 또는 시작 탭에 있는 작업 영역 구성 옵션을 사용 합니다.
- VM이 종료 되었습니다.
- Azure VM 서비스를 사용할 수 없는 또는 유지 관리 수행 됨.
- 작업 영역 [일일 데이터 또는 보존 한도](../platform/manage-cost-storage.md) 충족 되었습니다.

선택 **상태 진단 보기** 연결 된 상태 조건, 상태 변경 및 기타 문제 VM과 관련 된 구성 요소를 모니터링 하 여 검색 된 VM의 모든 구성 요소를 표시 하는 페이지를 엽니다.

자세한 내용은 [상태 진단](#health-diagnostics)을 참조하세요.

에 **상태** 섹션에서는 테이블에서 상태 조건을 모니터링 하는 성능 구성 요소 상태 롤업을 보여 줍니다. 이러한 구성 요소 **CPU**, **메모리**합니다 **디스크**, 및 **네트워크**합니다. 구성 요소를 선택 하면 모든 모니터링 조건 및 해당 구성 요소의 상태를 나열 하는 페이지를 엽니다.

아래에 Windows를 실행 하는 Azure VM에서 상태에 액세스 하는 경우 상위 5 개 핵심 Windows 서비스의 상태에 표시 됩니다 **Core services 상태**합니다. 서비스 중 하나를 선택 하 여 상태와 함께 해당 구성 요소에 대 한 모니터링 상태 조건을 나열 하는 페이지를 엽니다.

상태 조건의 이름을 선택 하면 속성 창이 열립니다. 이 창에서 상태 조건에 해당 하는 Azure Monitor 경고를 경우를 포함 하 여 구성 세부 정보를 검토할 수 있습니다.

자세한 내용은 [상태 진단 및 사용 상태 조건](#health-diagnostics)합니다.

### <a name="aggregate-vm-perspective"></a>집계 VM 관점

선택 된 리소스 그룹에에서 모든 Vm에 대 한 상태 수집을 보려는 **Azure Monitor** 한 다음 선택한 포털 탐색 목록에서 **가상 머신 (미리 보기)** .

![Azure Monitor의 VM 인사이트 모니터링 보기](./media/vminsights-health/vminsights-aggregate-health.png)

에 **구독** 하 고 **리소스 그룹** 드롭 다운 목록을 보고 된 상태를 보려는 그룹에 관련 된 Vm을 포함 하는 적절 한 리소스 그룹을 선택 합니다. 선택 항목에만 적용 상태 기능 되며를 전달 하지 않습니다 **성능** 하거나 **지도** 탭 합니다.

합니다 **상태** 탭에서는 다음 정보를 제공 합니다.

* 얼마나 많은 Vm 정상 인지와 위험 또는 비정상 상태의 또는 데이터 (알 수 없는 상태 라고 함)을 제출 하지 않습니다.
* 및 얼마나 많은 Vm OS가 비정상 상태를 보고 합니다.
* 얼마나 많은 Vm 프로세서, 디스크, 메모리 또는 상태에 의해 분류 되는 네트워크 어댑터를 사용 하 여 검색 된 문제로 인해 정상 상태가 아닌 합니다.
* 얼마나 많은 Vm 상태에 의해 분류 하는 핵심 OS 서비스를 사용 하 여 검색 된 문제로 인해 정상 상태가 아닌 합니다.

에 **상태** 탭, VM 및 검토 경고 세부 정보 및 관련 된 기술 항목을 모니터링 하는 상태 조건을 감지한 중요 한 문제를 식별할 수 있습니다. 이러한 문서는 진단 및 문제 해결할 지원할 수 있습니다. 아무 심각도나 선택하면 해당 심각도를 기준으로 필터링된 [모든 경고](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 페이지가 열립니다.

**운영 체제별 VM 배포** 목록에는 Windows 버전 또는 Linux 배포판에 나열된 VM이 해당 버전과 함께 표시됩니다. OS 범주별에서 Vm은 더 세분화 된 VM의 상태를 기반으로 합니다.

![VM 인사이트 가상 머신 배포 관점](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

포함 한 모든 열 선택 **VM 수**, **위험**를 **경고**를 **정상**, 또는 **알 수 없는**합니다. 필터링 된 결과의 목록을 보려면 합니다 **가상 머신** 선택한 열과 일치 하는 페이지입니다.

예를 들어, Red Hat Enterprise Linux 버전 7.5를 실행 하는 모든 Vm을 검토 하려면 선택 합니다 **VM 개수** 값에 대 한 해당 OS 해당 필터와 해당 현재 상태를 일치 하는 Vm이 나열 됩니다.

![Red Hat Linux VM의 롤업 예제](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

에 **Virtual Machines** 열 아래에 있는 VM의 이름을 선택 하면 페이지 **VM 이름**로 이동 합니다 **VM 인스턴스** 페이지. 이 페이지는 경고와 선택한 VM 영향을 주는 상태 조건 문제에 대 한 자세한 정보를 제공 합니다. 선택 하 여 성능 상태의 세부 정보가 필터링 **상태** 정상 상태가 아닌 구성 요소를 확인 하려면 페이지의 왼쪽 위 모서리에 있는 아이콘입니다. 또한 경고 심각도 별로 분류 비정상 구성 요소에서 발생 하는 VM 상태 경고를 볼 수 있습니다.

**VM 목록** 보기를 열려면 VM 이름을 선택 합니다 **상태** 선택한 경우와 마찬가지로 해당 VM에 대 한 페이지 **Insights (미리 보기)** 직접 VM에서 합니다.

![선택한 Azure 가상 머신에 대한 VM 인사이트](./media/vminsights-health/vminsights-directvm-health.png)

합니다 **Insights (미리 보기)** 페이지 VM 경고에 대해 롤업 상태를 표시 합니다. 이 상태를 나타내는 VM 상태를 비정상을에서 정상 상태 변경 될 때 발생 조건에 따라 경고 심각도 별로 분류 됩니다. 선택 **중요 한 조건에는 Vm** 위험 성능 상태에 하나 이상의 Vm의 목록이 있는 페이지를 엽니다.

Vm 중 하나에 대 한 상태를 선택 합니다 **상태 진단** VM의 보기입니다. 이 뷰에서 상태 조건을 성능 상태 문제를 반영 하는 것을 확인할 수 있습니다. 경우는 **상태 진단** 페이지가 열리고, VM의 모든 구성 요소 및 현재 상태를 사용 하 여 해당 연결된 상태 조건을 보여 줍니다.

자세한 내용은 [상태 진단](#health-diagnostics)을 참조하세요.

**모든 상태 조건 보기**를 선택하면 이 기능으로 사용할 수 있는 모든 상태 조건의 목록을 보여주는 페이지가 열립니다. 이 정보는 다음과 같은 옵션을 기반으로 추가로 필터링할 수 있습니다.

* **형식**합니다. 상태 조건 상태를 평가 하 고 모니터링 된 VM의 전반적인 상태를 롤업 하는 다음과 같은 세 종류가 있습니다.
    - **단위**합니다. VM의 일부 측면을 측정합니다. 이 상태 조건 형식은 가상 트랜잭션을 수행 하는 스크립트를 실행 하거나 오류를 나타내는 이벤트를 볼 구성 요소의 성능을 결정 하려면 성능 카운터를 확인할 수 있습니다. 필터는 기본적으로 장치에 설정 됩니다.
    - **종속성**합니다. 다른 엔터티 간의 상태 롤업을 제공합니다. 이 상태 조건을 다른 엔터티 형식에 의존 하는 성공적인 작업의 상태에 따라 달라 지도록 엔터티의 상태를 허용 합니다.
    - **집계**합니다. 유사한 상태 조건 결합 된 성능 상태를 제공합니다. 유닛 및 종속성 상태 조건은 집계 상태 조건을 설정 하는 아래에서 일반적으로 구성 됩니다. 엔터티를 대상으로 하는 다양한 상태 조건에 대해 보다 나은 일반적인 구성을 제공하는 것 외에도, 집계 상태 조건은 엔터티의 개별 범주에 대해 고유한 성능 상태를 제공합니다.

* **범주**합니다. 보고를 목적으로 유사한 조건 그룹에 사용 되는 상태 조건의 형식입니다. 이러한 범주는 **가용성** 하 고 **성능**합니다.

정상 상태가 아닌 인스턴스를 보려면 아래 값을 선택 합니다 **비정상적인 구성 요소** 열입니다. 이 페이지에서 테이블 위험 성능 상태에 있는 구성 요소를 나열 합니다.

## <a name="health-diagnostics"></a>상태 진단

합니다 **상태 진단** 페이지를 사용 하면 VM의 상태 모델을 시각화할 수 있습니다. 이 페이지는 VM의 모든 구성 요소, 연결된 상태 조건, 상태 변경 및 VM과 관련 된 모니터링 대상된 구성 요소에서 식별 하는 다른 중요 한 문제를 나열 합니다.

![VM에 대한 상태 진단 페이지의 예](./media/vminsights-health/health-diagnostics-page-01.png)

다음 메서드를 사용 하 여 상태 진단을 시작 합니다.

* Azure Monitor에서 집계 VM 관점에서 모든 Vm에 대 한 성능 상태를 롤업 합니다.

    1. 에 **상태** 페이지에 대 한 아이콘을 선택 합니다 **위험**를 **경고**를 **정상**, 또는 **알 수 없는** 상태 섹션 **게스트 VM 상태**합니다.
    2. 필터링 된 범주와 일치 하는 모든 Vm을 나열 하는 페이지로 이동 합니다.
    3. 값을 선택 합니다 **상태** 열을 해당 VM에 범위가 지정 된 상태 진단은 엽니다.

* Azure Monitor에서 집계 VM 관점에서 OS를 제공 합니다. **VM 배포** 아래에 있는 열 값 중 하나를 선택하면 **Virtual Machines** 페이지가 열리고 필터링된 범주와 일치하는 테이블에 목록이 반환됩니다. 아래에 있는 값을 선택 하면 **상태** 열 선택된 된 VM에 대 한 상태 진단 열립니다.
 
* 게스트 Vm에 대 한 Azure Monitor에는 VM에서에서 **Health** 탭을 선택 하 여 **상태 진단 보기**합니다.

두 가지 범주로 상태 정보를 구성 하는 상태 진단: 가용성 및 성능.
 
두 범주에 필터링 하지 않고 논리 디스크, CPU 및 등과 같은 구성 요소에 대해 정의 된 모든 상태 조건은 볼 수 있습니다. 이러한 뷰는 전체 뷰를 선택 하면 두 범주별으로 결과 필터링 또는 조건에 있을 수 있습니다 **가용성** 하거나 **성능**합니다.

또한 조건 범주를 볼 수 옆에 **상태 조건을** 열입니다. 조건에는 선택한 범주에 일치 하지 않는 경우 메시지가 **선택한 범주에 사용할 수 없는 상태 조건을** 에 표시 되는 **상태 조건을** 열입니다.

상태 조건 상태의 네 가지 형식 중 하나에 의해 정의 됩니다. **중요**, **경고**합니다 **정상**, 및 **알 수 없는**합니다. 처음 3 개는 의미에서 직접 모니터의 임계값을 수정할 수는 구성 가능 합니다 **상태 조건을** 구성 창입니다. 도 가능한 Azure Monitor REST API를 사용 하 여 [업데이트 작업을 모니터](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)합니다. **알 수 없는** 은 구성할 수 없습니다 및 특정 시나리오에 대해 예약 됩니다.

합니다 **상태 진단** 페이지에 세 가지 주요 섹션이 있습니다.

* 구성 요소 모델
* 상태 조건
* 상태 변경

![상태 진단 페이지의 섹션](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>구성 요소 모델

맨 왼쪽 열에는 **상태 진단** 페이지가 **구성 요소 모델**합니다. VM과 연결 된 모든 구성 요소는 현재 상태와 함께이 열에 표시 됩니다.

다음 예제에서는 검색 된 구성 요소는 **디스크**를 **논리 디스크**합니다 **프로세서**를 **메모리**, 및  **운영 체제**합니다. 이러한 구성 요소의 여러 인스턴스가 검색되어 이 열에 표시됩니다.

예를 들어, 다음 그림은 VM에 논리 디스크의 두 인스턴스가 있는 **c:** 하 고 **d:** , 정상 상태의:

![상태 진단에 제공된 예제 구성 요소 모델](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>상태 조건

상태 진단 페이지의 가운데 열 **상태 조건을**합니다. VM에 대해 정의된 상태 모델이 계층 구조 트리에 표시됩니다. VM의 상태 모델은 단위 및 집계 상태 조건으로 구성됩니다.

![상태 진단에 제공된 상태 조건 예제](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

상태 조건을 모니터링 임계값, 엔터티, 및 등의 상태 수 있는 인스턴스의 상태를 측정 합니다. 상태 조건에는 두 가지 또는 세 개의 구성 가능한 상태 임계값을 앞에서 설명한 대로 언제 든 지 상태 조건을 상태만 중 하나만 있을 수 있습니다.

상태 모델 전체 대상의 상태 및 대상 구성 요소를 결정 하는 기준을 정의 합니다. 조건의 계층에 표시 됩니다는 **상태 조건을** 섹션에 **상태 진단** 페이지.

상태 롤업 정책 집계 상태 조건 구성의 일부인 (기본으로 설정 됩니다 **최악의의**). 이 기능의 일부로 실행 하는 상태 조건의 기본 집합을 찾을 수 있습니다 합니다 [구성 세부 정보 모니터링](#monitoring-configuration-details) 이 문서의 섹션입니다.

Azure Monitor REST API를 사용할 수도 있습니다 [리소스별 모니터 인스턴스 목록](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource) 모든 상태 조건 목록을 가져오려고 합니다. 이 조건은 Azure VM 리소스에 대해 실행 되는 구성 세부 정보를 포함 합니다.

합니다 **단위** 상태 조건 형식은 오른쪽에 있는 줄임표 (...) 링크를 선택 하 여 수정 구성을 가질 수 있습니다. 선택 **자세한 정보 표시** 구성 창을 엽니다.

![상태 조건 구성 예제](./media/vminsights-health/health-diagnostics-vm-example-02.png)

예제를 사용 하는 경우 선택한 상태 조건에 대해 구성 창의 **평균 디스크 초 쓰기**를 다른 숫자 값을 사용 하 여 임계값을 구성할 수 있습니다. 에서만 변경할 수 있습니다 즉, 두 가지 상태 모니터가 **정상** 하 **경고**합니다.

다른 상태 조건을 경고 및 위험 성능 상태 임계값에 대 한 값을 구성할 수 있는 세 가지 상태를 종종 사용 합니다. Azure Monitor REST API를 사용 하 여 임계값을 수정할 수도 있습니다 [모니터 구성을](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)합니다.

>[!NOTE]
>모든 모니터링 대상 인스턴스에 적용 인스턴스 하나에 상태 조건을 구성 변경 내용을 적용 합니다. 예를 들어, 선택 하는 경우 **-1: d: 디스크** 한 다음 수정 합니다 **평균 디스크 초 쓰기** 임계값 변경 내용을 검색 하 고 VM에서 모니터링 하는 모든 인스턴스에 적용 합니다.


![단위 모니터의 상태 조건 구성 예제](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

상태 조건에 자세히 알아보려면 원한다 면 문제, 원인 및 해결책을 식별할 수 있도록 기술 항목 포함 했습니다. 선택 **정보를 보려면** 페이지의 관련된 기술 항목을 참조 합니다.

Vm 상태에 대 한 Azure Monitor를 사용 하 여 포함 된 모든 기술 항목, 참조 [Azure Monitor 상태 기술 설명서](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)합니다.

### <a name="state-changes"></a>상태 변경

맨 오른쪽 열에는 **상태 진단** 페이지가 **상태 변경을**합니다. 이 열에서 선택한 상태 조건과 사용 하 여 연결 된 모든 상태 변경 내용을 나열 합니다 **상태 조건을** 섹션인 또는 VM에서 선택 된 경우 VM의 상태 변경 합니다 **구성 요소 모델** 또는  **상태 조건** 테이블의 열입니다.

![상태 진단에 제공된 상태 변경 예제](./media/vminsights-health/health-diagnostics-page-statechanges.png)

다음 섹션에서는 상태 조건을 상태와 연결 된 시간을 보여 줍니다. 이 정보는 열의 맨 위에 있는 최신 상태를 보여 줍니다.

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>구성 요소 모델, 상태 조건 및 상태 변경 내용을 열의 연결

세 개의 열은 서로 간에 상호 연결되어 있습니다. 인스턴스를 선택 하는 경우는 **구성 요소 모델** 열을 **상태 조건을** 열이 해당 구성 요소 뷰를 필터링 합니다. 마찬가지로 합니다 **상태 변경 내용을** 열이 선택한 상태 기준에 따라 업데이트 됩니다.

![모니터링되는 인스턴스 선택 및 결과 예제](./media/vminsights-health/health-diagnostics-vm-example-01.png)

예를 들어, 선택 하는 경우 *디스크-1: d:* 아래의 목록에서 **구성 요소 모델**를 **상태 조건을** 필터링 *디스크-D 1:* , 및  **상태 변경** 의 가용성을 기준으로 하는 상태 변경을 보여 줍니다 *디스크-1: d:* 합니다.

업데이트 된 상태를 보려면 새로 고칠 수 있습니다 상태 진단 페이지를 선택 하 여 합니다 **새로 고침** 링크 합니다. 미리 정의된 폴링 간격을 기반으로 상태 조건의 성능 상태에 대한 업데이트가 있는 경우, 이 작업을 통해 대기하지 않고 최신 성능 상태를 반영할 수 있습니다. 합니다 **상태 조건을** 는 선택 된 상태를 기준으로 결과 범위를 사용 하면 필터: 정상, 경고, 중요, 알 수 없는, 및 All입니다. 합니다 **마지막 업데이트** 오른쪽 위 모퉁이에서 시간 상태 진단 페이지를 새로 고친 마지막 시간을 나타냅니다.

## <a name="alerts"></a>경고

Vm 상태에 대 한 azure Monitor와 통합 [Azure Alerts](../../azure-monitor/platform/alerts-overview.md)합니다. 경고를 발생 시킵니다 미리 정의 된 조건을 검색 하는 경우 정상 상태에서 비정상 상태로 변경 하는 경우. 경고는 심각도에서 가장 높은 수준으로 심각도 0 사용 하 여 심각도 4 까지의 심각도 0으로 분류 됩니다.

경고는 경고가 트리거된 경우 사용자에 게 알려 주기 위해 작업 그룹에 연관 되어 있지 않습니다. 구독 소유자의 단계를 수행 하 여 알림을 구성 해야 합니다 [경고 구성](#configure-alerts) 섹션입니다.

VM 상태 경고 심각도 별로 분류의 총 수를 **상태** 아래에 있는 대시보드는 **경고** 섹션입니다. 총 경고 수 또는 심각도 수준에 해당하는 숫자를 선택하면, **경고** 페이지가 열리고 선택 항목과 일치하는 모든 경고가 나열됩니다.

예를 들어, 해당 하는 행을 선택 하면 **심각도 수준 1**, 다음 보기를 표시 합니다.

![모든 심각도 수준 1 경고 예제](./media/vminsights-health/vminsights-sev1-alerts-01.png)

합니다 **모든 경고** 페이지 선택 항목을 일치 하는 경고만 표시 하도록 범위가 지정 되지 않습니다. 또한에 따라서도 필터링 됩니다 **리소스 종류** 만 VM 리소스에서 발생 하는 상태 경고를 표시 합니다. 이 형식은 열 아래에서 경고 목록에 반영 됩니다 **대상 리소스**위치를 표시 합니다 Azure VM 발생된 한 경고는 비정상 조건이 충족 된 경우.

이 보기에 포함 될 다른 리소스 유형 또는 서비스에서 경고를 사용 하는 것이 없습니다. 이러한 경고는 일반적으로 기본 Azure Monitor에서에서 볼 수 있는 메트릭 경고 또는 로그 쿼리를 기반으로 하는 로그 경고를 포함 [모든 경고](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 페이지입니다.

페이지의 맨 위에 있는 드롭다운 메뉴에서 값을 선택 하 여이 보기를 필터링 할 수 있습니다.

|열 |설명 |
|-------|------------|
|구독 |Azure 구독을 선택합니다. 선택한 구독의 경고만 보기에 포함됩니다. |
|리소스 그룹 |단일 리소스 그룹을 선택합니다. 선택한 리소스 그룹의 대상이 있는 경고만 보기에 포함됩니다. |
|리소스 종류 |리소스 종류를 하나 이상 선택합니다. 기본적으로 대상 **가상 머신**의 경고만 선택되고 이 보기에 포함됩니다. 이 열은 리소스 그룹을 지정한 후에만 사용할 수 있습니다. |
|리소스 |리소스를 선택합니다. 해당 리소스가 대상으로 지정된 경고만 보기에 포함됩니다. 이 열은 리소스 종류를 지정한 후에 사용할 수 있습니다. |
|Severity |경고 심각도를 선택하거나 심각도에 상관없이 모든 경고를 포함하려면 **모두**를 선택합니다. |
|조건 모니터링 |발생 또는 조건이 더 이상 활성 상태가 되는 경우 시스템에서 해결 하는 경우 모니터 조건이 필터 경고를 선택 합니다. 또는 선택 **모든** 모든 조건에 대 한 경고를 포함 합니다. |
|경고 상태 |경고 상태를 선택 **새로 만들기**, **승인**를 **닫힌**, 또는 **모든** 모든 상태에 대 한 경고를 포함 하도록 합니다. |
|서비스 모니터링 |한 서비스를 선택하거나 모든 서비스를 포함하려면 **모두**를 선택합니다. 이 기능에 대 한 VM Insights에서 경고에만 지원 됩니다.|
|시간 범위| 선택한 기간 내에 발생한 경고만 보기에 포함됩니다. 지원되는 값은 지난 1시간, 지난 24시간, 지난 7일 및 지난 30일입니다. |

경고를 선택 하는 경우는 **세부 정보를 경고** 페이지가 표시 됩니다. 이 페이지에는 해당 상태를 변경할 수 있습니다 및 경고의 세부 정보를 제공 합니다.

경고 관리에 대한 자세한 내용은 [Azure Monitor를 사용하여 경고 만들기, 보기 및 관리](../../azure-monitor/platform/alerts-metric.md)를 참조하세요.

>[!NOTE]
>상태 조건 또는 수정 하 고 기존 상태 경고 규칙 Azure Monitor에서 포털에서 기반으로 새 경고를 만들면 현재 지원 되지 않습니다.


![선택한 경고에 대한 경고 세부 정보 창](./media/vminsights-health/alert-details-pane-01.png)

를 선택한 다음를 선택 하 여 하나 이상의 경고에 대 한 경보 상태를 변경할 수 있습니다 **상태를 변경할** 에서 합니다 **모든 경고** 왼쪽 위 모서리에 있는 페이지. 상태 중 하나를 선택는 **경고 상태 변경** 창 변경에 대 한 설명을 추가 합니다 **주석** 필드를 선택한 후 **확인** 변경 내용을 적용 하려면. 정보가 확인 되 고 변경 내용이 적용 될 때 진행 상황을 추적할 **알림을** 메뉴에서.

### <a name="configure-alerts"></a>경고 구성
Azure portal에서 특정 경고 관리 작업을 관리할 수 없습니다. 사용 하 여 이러한 작업을 수행 해야 합니다 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)합니다. 구체적으로 살펴보면 다음과 같습니다.

- 상태 조건에 대 한 경고 사용 여부 설정
- 상태 조건 경고에 대 한 알림 설정

각 예에서는 [ARMClient](https://github.com/projectkudu/armclient) Windows 컴퓨터에 있습니다. 이 메서드를 사용 하 여 잘 모르는 경우 [ARMClient를 사용 하 여](../platform/rest-api-walkthrough.md#use-armclient)입니다.

#### <a name="enable-or-disable-an-alert-rule"></a>경고 규칙을 사용할지 설정 합니다.

특정 상태 조건 속성에 대 한 알림을 사용할지 **alertGeneration** 값을 사용 하 여 수정 해야 합니다 **사용 안 함** 또는 **사용**합니다.

식별 하는 *monitorId* 다음 예제에서는 특정 성능 조건에 대 한 조건에 대 한 해당 값에 대해 쿼리 하는 방법을 보여 줍니다 **LogicalDisk\Avg Disk 초 Per Transfer**:

1. 터미널 창에서 **armclient.exe login**을 입력합니다. 이렇게 Azure에 로그인 하 라는 메시지가 표시 됩니다.

2. 특정 VM에서 활성 상태 조건을 검색 하 고에 대 한 값을 식별 하려면 다음 명령을 입력 *monitorId* 속성:

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    다음 예제에서는 출력을 *armclient GET* 명령입니다. 값을 기록해 *MonitorId*합니다. 이 값이 있는 해야 상태 조건의 ID를 지정 하 고 경고를 만들려면 해당 속성을 수정 하는 다음 단계에 대 한 필요 합니다.

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 수정 하려면 다음 명령을 입력 합니다 *alertGeneration* 속성:

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 속성 값을로 설정 되어 있는지 확인 하려면 2 단계에서 사용한 GET 명령을 **사용 안 함**합니다.

#### <a name="associate-an-action-group-with-health-criteria"></a>상태 조건을 사용 하 여 작업 그룹 연결

Vm 상태에 대 한 azure Monitor는 비정상 상태 조건에서 경고가 발생할 때 SMS 및 전자 메일 알림을 지원 합니다. 에 알림을 구성 하려면 note SMS 또는 전자 메일 알림을 보내도록 구성 된 작업 그룹의 이름입니다.

>[!NOTE]
>이 작업에 대 한 알림을 수신 하려는 각 모니터링 된 VM에 대해 수행 되어야 합니다. 리소스 그룹의 모든 Vm에 적용 되지 않습니다.

1. 터미널 창에서 입력 *armclient.exe login*합니다. 이렇게 Azure에 로그인 하 라는 메시지가 표시 됩니다.

2. 경고 규칙을 사용 하 여 작업 그룹을 연결 하려면 다음 명령을 입력 합니다.
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 확인 하려면 속성의 값 **actionGroupResourceIds** 를 만들었습니다. 업데이트 하 고, 다음 명령을 입력 합니다.

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    출력은 다음 조건을 같이 표시 됩니다.
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>다음 단계

- 제한 사항 및 전체 VM 성능을 식별 하려면 참조 [보기 Azure VM의 성능](vminsights-performance.md)합니다.
- 검색 된 응용 프로그램 종속성을 알아보려면 [Vm 맵에 대 한 Azure Monitor 뷰](vminsights-maps.md)합니다.
