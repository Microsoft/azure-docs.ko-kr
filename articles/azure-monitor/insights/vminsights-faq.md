---
title: VM용 Azure Monitor(미리 보기) 질문과 대답 | Microsoft Docs
description: VM용 Azure Monitor는 Azure VM 운영 체제의 상태 및 성능 모니터링뿐만 아니라 애플리케이션 구성 요소 및 종속성의 자동 검색도 다른 리소스와 결합하고, 이러한 항목 간의 통신을 매핑하는 Azure의 솔루션입니다. 이 문서에서는 일반적인 질문에 답변합니다.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: ade12225a470b64278b9d27676ceab768f64d904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596596"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>VM용 Azure Monitor(미리 보기) 질문과 대답
Microsoft FAQ는 VM용 Azure Monitor에 대한 질문과 대답 목록입니다. 솔루션에 대한 추가 질문이 있으면 [토론 포럼](https://feedback.azure.com/forums/34192--general-feedback)으로 이동하여 질문을 게시하세요. 자주 묻는 질문일 경우 빠르고 쉽게 찾을 수 있도록 이 문서에 추가하겠습니다.

## <a name="can-i-onboard-to-an-existing-workspace"></a>기존 작업 영역에 온보딩할 수 있나요?
가상 머신이 Log Analytics 작업 영역에 이미 연결된 경우 VM용 Azure Monitor에 온보딩되면 해당 작업 영역을 계속 사용하도록 [여기](vminsights-onboard.md#prerequisites)에서 나열된 지원되는 지역 중 하나에서 제공될 수 있습니다.

온보딩하면 VM용 Azure Monitor에서 표시하고 분석할 이 정보의 수집을 시작하기 위해 모든 VM이 작업 영역에 데이터를 보고하게 되는 작업 영역에 대한 성능 카운터를 구성합니다.  결과적으로, 선택된 작업 영역에 연결된 VM의 모든 성능 데이터가 표시됩니다.  상태 및 맵 기능은 온보딩하도록 지정된 VM에 대해서만 활성화됩니다.

성능 카운터를 사용할 수 있는 경우에 대한 자세한 내용은 [온보딩](vminsights-onboard.md) 문서를 참조하세요.

## <a name="can-i-onboard-to-a-new-workspace"></a>새 작업 영역에 온보딩할 수 있나요? 
현재 VM이 기존 Log Analytics 작업 영역에 연결되지 않은 경우 데이터를 저장할 새 작업 영역을 만들어야 합니다.  Azure Portal을 통해 VM용 Azure Monitor에 대해 단일 Azure VM을 구성한 경우 새 기본 작업 영역을 만드는 작업이 자동으로 수행됩니다.

스크립트 기반 메서드를 사용하려는 경우 [온보딩](vminsights-onboard.md) 문서에서 이러한 단계를 다룹니다. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>내 VM에서 이미 기존 작업 영역에 보고하는 경우 어떻게 해야 하나요?
이미 가상 머신의 데이터를 수집하는 경우 기존 Log Analytics 작업 영역에 데이터를 보고하도록 이미 구성되었을 수 있습니다.  해당 작업 영역이 지원되는 지역 중 하나라면 기존 작업 영역에 VM용 Azure Monitor를 사용하도록 설정할 수 있습니다.  이미 사용 중인 작업 영역이 지원되는 지역 중 하나가 아닌 경우 이번에는 VM용 Azure Monitor에 온보딩할 수 없습니다.  본사에서는 추가 지역을 지원하기 위해 적극적으로 노력하고 있습니다.

>[!NOTE]
>VM용 Azure Monitor에 온보딩하도록 선택했는지 여부와 상관 없이 작업 영역에 보고는 모든 VM에 영향을 주는 작업 영역에 대한 성능 카운터를 구성합니다. 작업 영역에 대해 성능 카운터를 구성하는 방법에 대한 자세한 내용은 [설명서](../../azure-monitor/platform/data-sources-performance-counters.md)를 참조하세요. VM용 Azure Monitor에 구성된 카운터에 대한 자세한 내용은 [온보딩 설명서](vminsights-onboard.md#performance-counters-enabled)를 참조하세요.  

## <a name="why-did-my-vm-fail-to-onboard"></a>내 VM이 온보딩하지 못한 이유는?
Azure Portal에서 Azure VM을 온보딩한 경우 다음 단계가 수행됩니다.

* 해당 옵션을 선택한 경우 기본 Log Analytics 작업 영역이 만들어집니다.
* 선택한 작업 영역에 대한 성능 카운터가 구성됩니다. 이 단계가 실패하면 일부 성능 차트 및 표에서는 온보딩한 VM에 대한 데이터를 표시하지 않습니다. [여기](vminsights-onboard.md#enable-with-powershell)에 문서화된 PowerShell 스크립트를 실행하여 이 문제를 해결할 수 있습니다.
* 필요한 경우 Log Analytics 에이전트는 VM 확장을 사용하여 설치됩니다.  
* 필요한 경우 VM용 Azure Monitor 맵 종속성 에이전트는 VM 확장을 사용하여 Azure VM에 설치됩니다.  
* 필요한 경우 Azure Monitor 구성 요소가 상태 기능을 지원하도록 구성하고, VM이 상태 데이터를 보고하도록 구성합니다.

온보딩 프로세스 중에 포털에서 알림 상태를 반환하는 위의 각 상태에 대해 확인합니다. 작업 영역 및 에이전트 설치의 구성에는 일반적으로 5~10분 정도가 걸립니다. 포털에서 모니터링 및 상태 데이터를 보려면 5~10분이 더 걸립니다.  

온보딩을 시작하고 VM이 온보딩되어야 함을 나타내는 메시지가 표시된 경우 VM이 프로세스를 완료하려면 최대 30분이 허용됩니다. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>VM용 Azure Monitor만 사용하도록 설정했는데 상태 기능에서 모니터링되는 모든 VM이 표시되는 이유는 무엇인가요?
상태 기능은 단일 VM에서 작업이 시작된 경우에도 Log Analytics 작업 영역에 연결된 모든 VM에 사용하도록 설정됩니다.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>상태 조건이 상황을 평가하게 되는 일정을 수정할 수 있나요?
아니요. 이 릴리스에서는 상태 조건의 기간 및 빈도를 수정할 수 없습니다. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>모니터링할 필요가 없는 상황에서 상태 조건을 사용하지 않도록 설정할 수 있나요?
이 릴리스에서는 상태 조건을 사용하지 않도록 설정할 수 없습니다.

## <a name="are-the-health-alert-severities-configurable"></a>상태 경고 심각도를 구성할 수 있나요?  
상태 경고 심각도는 수정할 수 없으며, 사용하거나 사용하지 않도록 설정할 수만 있습니다. 또한 일부 경고 심각도는 상태 조건의 상태에 따라 업데이트됩니다. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>특정 상태 조건의 설정을 다시 구성하는 경우 특정 인스턴스로 범위를 지정할 수 있나요?  
상태 조건 인스턴스의 설정을 수정하면 Azure VM에서 동일한 유형의 모든 상태 조건 인스턴스가 수정됩니다. 예를 들어 C: 논리 디스크에 해당하는 사용 가능한 디스크 공간 상태 조건 인스턴스의 임계값을 수정할 경우 이 임계값은 동일한 VM에 대해 검색 및 모니터링되는 다른 모든 논리 디스크에 적용됩니다.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>상태 기능이 논리 프로세서 및 코어를 모니터링하나요?
아니요. 개별 프로세서 및 논리적 프로세서 수준 상태 조건은 Windows에 포함되지 않으며, Azure VM에 사용할 수 있는 논리 CPU의 총 수에 따라 CPU 압력을 효과적으로 평가하기 위해 기본적으로 총 CPU 사용률만 모니터링됩니다. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>모든 상태 조건 임계값을 구성할 수 있나요?  
상태가 *실행 중* 또는 *사용 가능*으로 설정되어 있는 Windows VM을 대상으로 하는 상태 조건의 임계값은 수정할 수 없습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서 상태를 쿼리할 때 다음과 같은 경우 서비스 또는 엔터티에 대해 *comparisonOperator* 값 **LessThan** 또는 **GreaterThan**과 *threshold* 값 **4**가 표시됩니다.
   - DNS 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - DHCP 클라이언트 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - RPC 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 방화벽 서비스 상태 – 서비스가 실행 중이 아닙니다.
   - Windows 이벤트 로그 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 서버 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - Windows 원격 관리 서비스 상태 – 서비스가 실행 중이 아닙니다. 
   - 파일 시스템 오류 또는 손상 - 논리 디스크를 사용할 수 없습니다.

상태가 이미 *true*로 설정되어 있는 다음 Linux 상태 조건의 임계값은 수정할 수 없습니다. 워크로드 모니터링 API에서 엔터티에 대해 쿼리할 때 컨텍스트에 따라 상태에 *comparisonOperator* 값 **LessThan** 및 *threshold* 값 **1**이 표시됩니다.
   - 논리 디스크 상태 - 논리 디스크가 온라인/사용 가능 상태가 아님
   - 디스크 상태 - 디스크가 온라인/사용 가능 상태가 아님
   - 네트워크 어댑터 상태 - 네트워크 어댑터가 사용 안 함임

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>상태 기능에 포함된 경고를 수정하려면 어떻게 해야 하나요?
각 상태 조건에 대해 정의된 경고 규칙은 Azure Portal에 표시되지 않습니다. [워크로드 모니터 API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)에서만 상태 경고 규칙을 사용하거나 사용하지 않도록 설정할 수 있습니다. 또한 Azure Portal에서는 상태 경고에 대해 [Azure Monitor 작업 그룹](../../azure-monitor/platform/action-groups.md)을 할당할 수 없습니다. 오직 알림 설정 API를 사용하여 상태 경고가 발생할 때마다 트리거되도록 작업 그룹을 구성할 수 있습니다. 현재, VM에 대해 발생한 모든 *상태 경고*가 동일한 작업 그룹을 트리거하도록 VM에 대해 작업 그룹을 할당할 수 있습니다. 기존의 Azure 경고와 달리, 각 상태 경고 규칙에 대한 별도의 작업 그룹이라는 개념이 없습니다. 또한 상태 경고가 트리거될 때 메일 또는 SMS 알림을 제공하도록 구성된 작업 그룹만 지원됩니다. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>내 VM의 성능 차트에 일부 또는 모든 데이터가 표시되지 않습니다
디스크 테이블 또는 일부 성능 차트에 성능 데이터가 표시되지 않는 경우 작업 영역에서 성능 카운터를 구성할 수 없습니다. 이 문제를 해결하려면 다음 [PowerShell 스크립트](vminsights-onboard.md#enable-with-powershell)를 실행합니다.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>VM용 Azure Monitor 맵 기능과 서비스 맵이 어떻게 다른가요?
VM용 Azure Monitor 맵 기능은 서비스 맵을 기반으로 하지만 다음과 같은 차이점이 있습니다.

* 맵 보기는 VM 블레이드 및 Azure Monitor의 VM용 Azure Monitor에서 액세스할 수 있습니다.
* 이제 맵에서 연결은 클릭 가능하고 선택한 연결에 대한 사이드 패널에서 연결 메트릭 데이터 보기를 표시합니다.
* 더 복잡한 맵을 지원하기 위해 맵을 만드는 데 사용되는 새 API가 있습니다.
* 이제 모니터링된 VM은 클라이언트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 가상 머신 및 모니터링되지 않는 가상 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 이제 모니터링된 가상 머신은 서버 포트 그룹 노드에 포함됩니다. 도넛형 차트에서는 그룹에서 모니터링된 머신 및 모니터링되지 않는 머신의 비율을 보여줍니다.  그룹이 확장되면 머신의 목록을 필터링하는 데 사용할 수도 있습니다.
* 맵 스타일은 Application insights에서 앱 맵을 사용하여 더욱 일관되게 업데이트되었습니다.
* 쪽 패널 업데이트 되었으며 및 통합의 서비스 맵-업데이트 관리, 변경 내용 추적, 보안 및 서비스 데스크에에서 지원 하 던 중 일부만 필요가 없습니다. 
* 맵에 대한 그룹 및 머신을 선택하는 옵션이 업데이트되어 이제 구독, 리소스 그룹, Azure 가상 머신 확장 집합 및 클라우드 서비스를 지원합니다.
* VM용 Azure Monitor 맵 기능에서 새 서비스 맵 머신 그룹을 만들 수 없습니다.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>내 성능 차트가 점선을 표시하는 이유는?
다음과 같은 이유로 발생할 수 있습니다.  데이터 수집에 간격이 있는 경우에 줄이 점선으로 그려집니다.  사용하도록 설정된 성능 카운터에 대한 데이터 샘플링 빈도를 수정한 경우(기본 설정은 60초마다 데이터를 수집하는 것임) 차트에 대해 좁은 시간 범위를 선택하고 샘플링 빈도가 차트에 사용된 버킷 크기 미만이면(예: 샘플링 빈도는 10분 간격이며 차트의 각 버킷은 5분 간격임) 차트에서 점선을 볼 수 있습니다.  광범위한 시간 범위를 보도록 선택하면 이 경우에 차트 선이 점선이 아닌 실선으로 표시됩니다.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>VM용 Azure Monitor에서 그룹이 지원되나요?
예, 종속성 에이전트가 설치되면 VM에서 정보를 수집하여 구독, 리소스 그룹, 가상 머신 확장 집합 및 클라우드 서비스에 기반한 그룹을 표시합니다.  서비스 맵을 사용하고 있고 시스템 그룹을 만든 경우에도 이러한 그룹이 표시됩니다.  또한 보고 있는 작업 영역에 대한 컴퓨터 그룹을 만든 경우에도 그룹 필터에 표시됩니다. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>집계 성능 차트에서 95번째 백분위수 줄에 영향을 주는 항목에 대한 세부 정보를 표시하려면 어떻게 할까요?
기본적으로 목록은 5번째 백분위수의 최솟값을 포함한 머신을 보여주는 사용 가능한 메모리 차트를 제외하고 선택된 메트릭에 대해 95번째 백분위수에 대한 최댓값이 있는 VM을 표시하도록 정렬됩니다.  차트를 클릭하면 적절한 메트릭이 선택된 **상위 N개 목록** 보기가 열립니다.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>맵 기능이 다른 VNet 및 서브넷에서 중복 IP를 어떻게 처리하나요?
서브넷 및 VNet에서 VM 또는 Azure 가상 머신 확장 집합을 사용하여 IP 범위를 복제하는 경우 VM용 Azure Monitor에서 잘못된 정보를 표시할 수 있습니다. 알려진 문제이며 본사에서는 이 환경을 개선하는 옵션을 개발하는 중입니다.

## <a name="does-map-feature-support-ipv6"></a>맵 기능이 IPv6을 지원하나요?
맵 기능은 현재 IPv4만 지원하며 본사에서는 IPv6에 대한 지원을 개발하는 중입니다. IPv6 내에서 터널링된 IPv4도 지원합니다.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>리소스 그룹 또는 다른 대규모 그룹에 대한 맵을 로드하는 경우 맵을 보기 어렵습니다.
크고 복잡한 구성을 처리하도록 맵을 개선하는 동안 맵에서는 많은 노드, 연결 및 노드가 클러스터로 작동할 수 있습니다.  계속 확장성을 증가시키도록 지원을 강화하기 위해 최선을 다하고 있습니다.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>성능 탭의 네트워크 차트가 Azure VM 개요 페이지의 네트워크 차트와 다르게 보이는 이유는?

Azure VM의 개요 페이지에는 게스트 VM에서 작업의 호스트 측정값에 따라 차트가 표시됩니다.  Azure VM 개요에 대한 네트워크 차트의 경우 청구되는 네트워크 트래픽만 표시합니다.  여기에는 VNet 간 트래픽이 포함되지 않습니다.  VM용 Azure Monitor에 대해 표시된 데이터 및 차트는 게스트 VM의 데이터를 기반으로 하고 네트워크 차트는 VNet 간 트래픽을 비롯하여 해당 VM에 대한 인바운드 및 아웃바운드인 모든 TCP/IP 트래픽을 표시합니다.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>VMConnection에 저장 되 고 통합 문서 연결 패널에 표시 된 데이터에 대 한 응답 시간 측정 방법

응답 시간은 근사값입니다. 응용 프로그램의 코드를 계측 하지 수행 되므로 파악 하지 못해도 실제로 요청을 시작 하는 시점 및 응답이 도착 하는 경우. 대신 데이터 연결에서 전송 되 고 해당 연결에서 다시 가져온 데이터를 관찰 했습니다. 이 에이전트 추적 이러한 송신 및 수신 하 고 연결할 하려고: 시퀀스 뒤에 보내기 시퀀스로 수신 요청/응답 쌍으로 해석 됩니다. 이러한 작업 간의 시간 응답 시간입니다. 네트워크 대기 시간 및 서버 처리에 포함 됩니다.

이 근사치 프로토콜 요청/응답 기반에 적합 합니다: 단일 요청에 연결 하는 사용자는 단일 응답에 도착 합니다. 이 경우 HTTP (S) (파이프라인)를 제외 하지만 다른 프로토콜에 대 한 충족 하지 않습니다.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Log Analytics 무료 가격 플랜을 사용하고 있는 경우 제한 사항이 있나요?
*무료* 가격 책정 계층을 사용하여 Log Analytics 작업 영역에 Azure Monitor를 구성한 경우 VM용 Azure Monitor 맵 기능은 5대의 작업 영역에 연결된 머신만 지원합니다. 무료 작업 영역에 5대의 VM이 연결되어 있는 경우 VM 중 하나의 연결을 끊은 후 나중에 새 VM을 연결하면 맵 페이지에서 새 VM이 모니터링 및 반영되지 않습니다.  

따라서 **Insights(미리 보기)** 가 VM에 이미 설치된 후라도 해당 VM을 열고 왼쪽 창에서 Insights(미리 보기)를 선택하면 **지금 사용해 보기** 옵션이 포함된 메시지가 표시됩니다.  그러나 이 VM이 VM용 Azure Monitor에 등록되지 않은 경우에는 일반적인 경우처럼 옵션이 포함된 메시지가 표시되지 않습니다. 

## <a name="next-steps"></a>다음 단계
[VM용 Azure Monitor 등록](vminsights-onboard.md)을 검토하여 가상 머신의 모니터링을 사용하기 위한 요구 사항과 방법을 알아보세요.
