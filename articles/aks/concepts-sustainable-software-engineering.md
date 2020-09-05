---
title: 개념-Azure Kubernetes Services에서 유지 가능한 소프트웨어 엔지니어링 (AKS)
description: Azure Kubernetes 서비스 (AKS)에서 지속 가능한 소프트웨어 엔지니어링에 대해 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 08/26/2020
ms.openlocfilehash: bc2240b3cb8508109f6dd25c4c80ff31ba197cfd
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086093"
---
# <a name="sustainable-software-engineering-principals-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 지속 가능한 소프트웨어 엔지니어링 보안 주체

유지 가능한 소프트웨어 엔지니어링 보안 주체는 유지 가능한 응용 프로그램을 정의, 빌드 및 실행 하는 데 도움이 되는 역량 집합입니다. 전반적인 목표는 응용 프로그램의 모든 측면에 대 한 참조 공간을 줄이는 것입니다. [원칙과 프로젝트][principals-green] 에는 유지 가능한 소프트웨어 엔지니어링의 보안 주체에 대 한 개요가 포함 되어 있습니다.

유지 가능한 소프트웨어 엔지니어링에 대해 이해 하는 데 중요 한 이유는 우선 순위와 초점의 변화입니다. 대부분의 경우에는 성능이 저하 되 고 대기 시간이 짧은 방식으로 소프트웨어가 설계 되 고 실행 됩니다. 유지 가능한 소프트웨어 엔지니어링은 최대한 많은 참조를 줄이는 데 중점을 둔 것입니다. 경우에 따라 유지 가능한 소프트웨어 엔지니어링 주체를 적용 하면 총 네트워크 여행 절감 등의 방법으로 더 빠른 성능을 제공 하거나 대기 시간을 줄일 수 있습니다. 다른 경우에는 다른 경우에는 우선 순위가 낮은 워크 로드를 지연 하는 등의 방법으로 성능이 저하 되거나 대기 시간이 길어질 수 있습니다. 응용 프로그램에 적용 가능한 소프트웨어 엔지니어링 주체를 적용 하기 전에 응용 프로그램의 우선 순위, 요구 사항 및 장단점을 검토 합니다.

## <a name="measure-and-optimize"></a>측정 및 최적화

AKS 클러스터의 참조 공간을 줄이려면 클러스터의 리소스를 사용 하는 방법을 이해 해야 합니다. [Azure Monitor][azure-monitor] 는 메모리 및 CPU 사용량과 같은 클러스터의 리소스 사용에 대 한 세부 정보를 제공 합니다. 이 데이터는 클러스터의 참조 공간을 줄이고 변경의 영향을 관찰할 수 있도록 결정을 내리는 데 도움이 될 수 있습니다. [Microsoft 지속 가능 계산기][sustainability-calculator] 를 설치 하 여 모든 Azure 리소스의 참조 공간을 확인할 수도 있습니다.

## <a name="increase-resource-utilization"></a>리소스 사용률 증가

참조 공간을 줄이는 한 가지 방법은 계산 리소스에 대 한 유휴 시간의 양을 줄이는 것입니다. 유휴 시간을 줄이면 계산 리소스의 사용률이 늘어납니다. 예를 들어 클러스터에 4 개의 노드가 있고 각 노드가 50% 용량으로 실행 되는 경우 4 개의 노드는 모두 50%의 사용 되지 않는 용량을 유휴 상태로 유지 합니다. 클러스터를 3 개 노드로 줄인 경우 동일한 작업을 수행 하면 3 개의 노드가 67% 용량으로 실행 되어 사용 되지 않는 용량이 각 노드에서 33%로 줄어들고 사용이 늘어납니다.

> [!IMPORTANT]
> 클러스터의 리소스를 변경 하는 것을 고려할 때 클러스터의 핵심 시스템 구성 요소에 대 한 안정성을 유지 하기 위한 충분 한 리소스가 [시스템 풀][system-pools] 에 있는지 확인 합니다. 클러스터가 불안정 해질 수 있는 지점으로 클러스터의 리소스를 줄일 수 없습니다.

클러스터의 사용률을 검토 한 후에는 [여러 노드 풀][multiple-node-pools]에서 제공 하는 기능을 사용 하는 것이 좋습니다. [노드 크기 조정을][node-sizing] 사용 하 여 특정 CPU 및 메모리 프로필을 사용 하 여 노드 풀을 정의할 수 있으므로 워크 로드 요구에 맞게 노드를 조정할 수 있습니다. 워크 로드 요구 사항에 맞게 노드 크기를 조정 하면 더 높은 사용률에서 몇 개의 노드를 실행 하는 데 도움이 됩니다. 또한 클러스터의 크기를 [조정][scale] 하 고 [수평 pod autoscaler][scale-horizontal] 및 [cluster autoscaler][scale-auto] 를 사용 하 여 구성에 따라 클러스터를 자동으로 확장 하는 방법을 구성할 수 있습니다. 클러스터 크기를 제어 하면 클러스터의 워크 로드에 대 한 변경 내용을 유지 하면서 모든 노드를 높은 사용률으로 실행 하는 데 도움이 될 수 있습니다. 워크 로드가 갑작스러운 중단 또는 종료를 허용 하는 경우 Azure 내에서 유휴 용량을 활용 하기 위해 [스폿 풀][spot-pools] 을 사용할 수 있습니다. 예를 들어, 배치 작업 또는 개발 환경에서 스폿 풀이 잘 작동할 수 있습니다.

사용률을 높이면 과도 한 노드를 줄일 수 있으므로 [각 노드의 리소스 예약][resource-reservations]에 사용 되는 에너지를 줄일 수 있습니다.

또한 응용 프로그램의 Kubernetes 매니페스트에서 CPU 및 메모리 *요청* *및 한도를* 검토 합니다. 메모리 및 CPU에 대 한 값을 낮추면 클러스터에서 다른 작업을 실행 하는 데 더 많은 메모리와 CPU를 사용할 수 있습니다. 낮은 CPU와 메모리를 사용 하 여 더 많은 워크 로드를 실행 하는 경우 클러스터는 더 많은 조밀 할당 되므로 사용률을 높일 수 있습니다. 응용 프로그램의 CPU 및 메모리를 줄일 때 이러한 값을 너무 낮게 설정 하면 응용 프로그램의 동작이 저하 되거나 불안정 해질 수 있습니다. CPU 및 메모리 *요청* 및 한 *도*를 변경 하기 전에 일부 벤치마킹 테스트를 실행 하 여 이러한 값이 적절 하 게 설정 되어 있는지 이해 하는 것이 좋습니다. 또한 응용 프로그램이 불안정 한 시점으로 이러한 값을 줄이지 마십시오.

## <a name="reduce-network-travel"></a>네트워크 여행 줄이기

클러스터에서 이동 해야 하는 거리 요청 및 응답을 줄이는 것은 일반적으로 네트워킹 장치에의 한 전력 소비를 줄이고, 네트워크 트래픽을 검토 한 후 네트워크 트래픽 원본에 가까운 [지역에서][regions] 클러스터를 만드는 것이 좋습니다. [Azure Traffic Manager][azure-traffic-manager] 를 사용 하 여 가장 가까운 클러스터 및 [근접 배치 그룹][proiximity-placement-groups] 으로 트래픽을 라우팅하는 데 도움을 줄 수 있습니다.

> [!IMPORTANT]
> 클러스터의 네트워킹을 변경 하는 것을 고려할 때 작업 요구 사항에 대 한 비용으로 네트워크 이동은 줄이지 마세요. 예를 들어 [가용성 영역][availability-zones] 을 사용 하면 클러스터에서 더 많은 네트워크 이동이 발생 하지만 워크 로드 요구 사항을 처리 하기 위해이 기능을 사용 해야 할 수 있습니다.

## <a name="demand-shaping"></a>수요 셰이핑

가능 하면 클러스터의 리소스에 대 한 수요를 초과 용량을 사용할 수 있는 시간 또는 지역으로 이동 하는 것이 좋습니다. 예를 들어 배치 작업을 실행 하거나 [스폿 풀][spot-pools]을 사용 하기 위한 시간 또는 지역을 변경 하는 것이 좋습니다. 또한 큐를 사용 하 여 즉각적인 처리가 필요 하지 않은 실행 중인 작업을 연기 하도록 응용 프로그램을 리팩터링 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에 언급 된 AKS의 기능에 대해 자세히 알아보세요.

* [여러 노드 풀][multiple-node-pools]
* [노드 크기 조정][node-sizing]
* [클러스터 크기 조정][scale]
* [Horizontal Pod Autoscaler][scale-horizontal]
* [클러스터 자동 크기 조정기][scale-auto]
* [스폿 풀][spot-pools]
* [시스템 풀][system-pools]
* [리소스 예약][resource-reservations]
* [근접 배치 그룹][proiximity-placement-groups]
* [가용성 영역][availability-zones]

[availability-zones]: availability-zones.md
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md
[azure-traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[proiximity-placement-groups]: reduce-latency-ppg.md
[regions]: faq.md#which-azure-regions-currently-provide-aks
[resource-reservations]: concepts-clusters-workloads.md#resource-reservations
[scale]: concepts-scale.md
[scale-auto]: concepts-scale.md#cluster-autoscaler
[scale-horizontal]: concepts-scale.md#horizontal-pod-autoscaler
[spot-pools]: spot-node-pool.md
[multiple-node-pools]: use-multiple-node-pools.md
[node-sizing]: use-multiple-node-pools.md#specify-a-vm-size-for-a-node-pool
[sustainability-calculator]: https://azure.microsoft.com/blog/microsoft-sustainability-calculator-helps-enterprises-analyze-the-carbon-emissions-of-their-it-infrastructure/
[system-pools]: use-system-pools.md
[principals-green]: https://principles.green/