---
title: Azure Monitor의 용량 및 성능 솔루션 | Microsoft Docs
description: 모니터 용량 및 성능 솔루션을 사용 하 여 Hyper-v 서버의 용량을 이해할 수 있도록 합니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 51617a6f-ffdd-4ed2-8b74-1257149ce3d4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.author: magoedte
ms.openlocfilehash: fcf71bf144b559c4867303988d4c1f08b7aa5605
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101917"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>(사용 되지 않음) 용량 및 성능 솔루션을 사용 하 여 Hyper-v 가상 머신 용량 계획

![용량 및 성능 기호](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> 용량 및 성능 솔루션은 더 이상 사용되지 않습니다.  솔루션을 이미 설치한 고객은 계속해서 솔루션을 사용할 수 있지만, 새 작업 영역에는 용량 및 성능 솔루션을 추가할 수 없습니다.

모니터에서 Hyper-v 서버의 용량을 이해할 수 있도록 용량 및 성능 솔루션을 사용할 수 있습니다. 이 솔루션은 Hyper-V 호스트에서 실행 중인 호스트 및 VM의 전체 사용률(CPU, 메모리 및 디스크)을 보여 줌으로써 Hyper-V 환경에 대한 정보를 제공합니다. 모든 호스트와 해당 호스트에서 실행되는 VM의 CPU, 메모리 및 디스크에 대한 메트릭이 수집됩니다.

솔루션:

-   최고 및 최저 CPU 및 메모리 사용률을 가진 호스트를 보여 줍니다.
-   최고 및 최저 CPU 및 메모리 사용률을 가진 VM을 보여 줍니다.
-   최고 및 최저 IOPS 및 처리량 사용률을 가진 VM을 보여 줍니다.
-   어떤 호스트에서 어떤 VM이 실행되고 있는지 보여 줍니다.
-   클러스터된 공유 볼륨에서 높은 처리량, IOPS 및 대기 시간을 가진 상위 디스크를 보여 줍니다.
- 그룹별로 사용자 지정 및 필터링할 수 있습니다.

> [!NOTE]
> 용량 관리라는 이전 버전의 용량 및 성능 솔루션에는 System Center Operations Manager와 System Center Virtual Machine Manager가 모두 필요했습니다. 업데이트된 이 솔루션에는 이러한 종속성이 없습니다.


## <a name="connected-sources"></a>연결된 소스

다음 표는 이 솔루션이 지원하는 연결된 소스를 설명합니다.

| 연결된 소스 | 지원 | 설명 |
|---|---|---|
| [Windows 에이전트](../../azure-monitor/platform/agent-windows.md) | 예 | 솔루션에서 Windows 에이전트의 용량 및 성능 데이터 정보를 수집합니다. |
| [Linux 에이전트](../../azure-monitor/learn/quick-collect-linux-computer.md) | 아닙니다.    | 솔루션에서 Linux 에이전트의 용량 및 성능 데이터 정보를 직접 수집하지 않습니다.|
| [SCOM 관리 그룹](../../azure-monitor/platform/om-agents.md) | 예 |솔루션에서 연결된 SCOM 관리 그룹에 있는 에이전트의 용량 및 성능 데이터를 수집합니다. SCOM에서 Log Analytics로 직접 연결은 필요하지 않습니다.|
| [Azure 저장소 계정](../../azure-monitor/platform/collect-azure-metrics-logs.md) | 아닙니다. | Azure 저장소는 용량 및 성능 데이터를 포함하지 않습니다.|

## <a name="prerequisites"></a>필수 조건

- Windows 또는 Operations Manager 에이전트는 가상 머신이 아닌 Windows Server 2012 이상의 Hyper-V 호스트에 설치해야 합니다.


## <a name="configuration"></a>구성

다음 단계를 수행하여 용량 및 성능 솔루션을 작업 영역에 추가합니다.

- [솔루션 갤러리에서 Log Analytics 솔루션 추가](../../azure-monitor/insights/solutions.md)에서 설명하는 프로세스를 사용하여 Log Analytics 작업 영역에 용량 및 성능 솔루션을 추가합니다.

## <a name="management-packs"></a>관리 팩

SCOM 관리 그룹이 Log Analytics 작업 영역에 연결된 경우 이 솔루션을 추가할 때 다음 관리 팩이 SCOM에 설치됩니다. 이 관리 팩에 대한 구성 또는 유지 관리는 필요 없습니다.

- Microsoft.IntelligencePacks.CapacityPerformance

1201 이벤트는 다음과 유사합니다.


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

용량 및 성능 솔루션을 업데이트하면 버전 번호가 변경됩니다.

솔루션 관리 팩이 업데이트되는 방법에 대한 자세한 내용은 [Log Analytics에 Operations Manager 연결](../../azure-monitor/platform/om-agents.md)을 참조하세요.

## <a name="using-the-solution"></a>솔루션 사용

용량 및 성능 솔루션을 작업 영역에 추가하면 [용량 및 성능]이 [개요] 대시보드에 추가됩니다. 이 타일에서는 현재 활성 상태인 Hyper-V 호스트의 수와 선택한 기간 동안 모니터링된 활성 가상 머신의 수를 표시합니다.

![용량 및 성능 타일](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>사용률 검토

[용량 및 성능] 타일을 클릭하여 [용량 및 성능] 대시보드를 엽니다. 대시보드는 다음 표의 열을 포함하고 있습니다. 각 열은 지정된 범위 및 시간 범위에 대한 열의 기준과 일치하는 최대 열 개의 항목을 나열합니다. 열 아래쪽의 **모두 보기**를 클릭하거나 열 헤더를 클릭하여 모든 레코드를 반환하는 로그 검색을 실행할 수 있습니다.

- **호스트**
    - **호스트 CPU 사용률** 선택한 기간을 기준으로 호스트 컴퓨터 CPU 사용률과 호스트 목록의 그래픽 추세를 표시합니다. 특정 시점의 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 호스팅된 VM의 CPU 카운터 세부 정보를 보려면 호스트 이름을 클릭합니다.
    - **호스트 메모리 사용률** 선택한 기간을 기준으로 호스트 컴퓨터 메모리 사용률과 호스트 목록의 그래픽 추세를 표시합니다. 특정 시점의 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 호스팅된 VM의 메모리 카운터 세부 정보를 보려면 호스트 이름을 클릭합니다.
- **Virtual Machines**
    - **VM CPU 사용률** 선택한 기간을 기준으로 가상 머신 CPU 사용률 및 가상 머신 목록의 그래픽 추세를 표시합니다. 특정 시점의 상위 3개 VM에 대한 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 VM에 대해 집계된 CPU 카운터 세부 정보를 보려면 VM 이름을 클릭합니다.
    - **VM 메모리 사용률** 선택한 기간을 기준으로 가상 머신 메모리 사용률과 가상 머신 목록의 그래픽 추세를 표시합니다. 특정 시점의 상위 3개 VM에 대한 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 VM에 대해 집계된 메모리 카운터 세부 정보를 보려면 VM 이름을 클릭합니다.
    - **VM 총 디스크 IOPS** 선택한 기간을 기준으로 가상 머신의 총 디스크 IOPS 및 각각의 IOPS를 포함한 가상 머신의 목록에 대한 그래픽 추세를 표시합니다. 특정 시점의 상위 3개 VM에 대한 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 VM에 대해 집계된 디스크 IOPS 카운터 세부 정보를 보려면 VM 이름을 클릭합니다.
    - **VM 총 디스크 처리량** 선택한 기간을 기준으로 가상 머신의 총 디스크 처리량 및 각각의 총 디스크 처리량을 포함한 가상 머신의 목록에 대한 그래픽 추세를 표시합니다. 특정 시점의 상위 3개 VM에 대한 세부 정보를 보려면 마우스로 꺾은선형 차트 위를 가리킵니다. 자세한 로그 검색 정보를 보려면 차트를 클릭합니다. 로그 검색을 열어 VM에 대해 집계된 총 디스크 처리량 카운터 세부 정보를 보려면 VM 이름을 클릭합니다.
- **클러스터된 공유 볼륨**
    - **총 처리량** 클러스터된 공유 볼륨에 대한 읽기와 쓰기의 합계를 표시합니다.
    - **총 IOPS** 클러스터된 공유 볼륨에 대한 초당 입출력 작업의 합계를 표시합니다.
    - **총 대기 시간** 클러스터된 공유 볼륨에 대한 총 대기 시간을 표시합니다.
- **호스트 밀도** 상위 타일에서 솔루션에 사용할 수 있는 호스트와 가상 머신의 총 수를 표시합니다. 로그 검색의 추가 세부 정보를 보려면 상위 타일을 클릭합니다. 모든 호스트와 호스팅되는 가상 머신의 수도 나열합니다. 로그 검색의 VM 결과를 자세히 분석하려면 호스트를 클릭합니다.


![호스트 블레이드 대시보드](./media/capacity-performance/dashboard-hosts.png)

![가상 머신 블레이드 대시보드](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>성능 평가

프로덕션 컴퓨팅 환경은 조직마다 크게 다릅니다. 또한 용량 및 성능 워크로드는 VM을 실행하는 방법 및 일반적인 고려 사항에 따라 달라질 수 있습니다. 성능을 측정하는 데 도움이 되는 특정 절차는 사용자 환경에 적용되지 않을 것입니다. 따라서 더 일반화된 규범적 지침이 도움을 주는 데 더 적합합니다. Microsoft는 성능 측정에 도움이 되는 다양한 규범적 지침 문서를 게시하고 있습니다.

요약하자면, 이 솔루션은 성능 카운터를 포함하여 다양한 원본에서 용량 및 성능 데이터를 수집합니다. 솔루션의 다양한 표면에 제시된 용량 및 성능 데이터를 사용하고 해당 결과를 [Hyper-V에 대한 성능 측정](https://msdn.microsoft.com/library/cc768535.aspx)(영문) 문서의 결과와 비교합니다. 얼마 전에 이 문서를 게시했지만 메트릭, 고려 사항 및 지침은 여전히 유효합니다. 이 문서에는 다른 유용한 리소스에 대한 링크가 포함되어 있습니다.


## <a name="sample-log-searches"></a>샘플 로그 검색

다음 표는 이 솔루션에서 수집하고 계산한 용량 및 성능 데이터에 대한 샘플 로그 검색을 제공합니다.


| 쿼리 | 설명 |
|:--- |:--- |
| 모든 호스트 메모리 구성 | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "Host Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| 모든 VM 메모리 구성 | Perf &#124; where ObjectName == "Capacity and Performance" and CounterName == "VM Assigned Memory MB" &#124; summarize MB = avg(CounterValue) by InstanceName |
| 모든 VM의 총 디스크 IOPS 분석 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Reads/s" or CounterName == "VHD Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 모든 VM의 총 디스크 처리량 분석 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "VHD Read MB/s" or CounterName == "VHD Write MB/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 모든 CSV의 총 IOPS 분석 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 모든 CSV의 총 처리량 분석 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Reads/s" or CounterName == "CSV Writes/s") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |
| 모든 CSV의 총 대기 시간 분석 | Perf &#124; where ObjectName == "Capacity and Performance" and (CounterName == "CSV Read Latency" or CounterName == "CSV Write Latency") &#124; summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1h), CounterName, InstanceName |


## <a name="next-steps"></a>다음 단계
* [Log Analytics의 로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용하여 자세한 용량 및 성능 데이터를 확인합니다.
