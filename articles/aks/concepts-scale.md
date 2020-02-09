---
title: 개념 - AKS(Azure Kubernetes Service)에서 애플리케이션 크기 조정
description: Horizontal Pod Autoscaler, 클러스터 자동 크기 조정기 및 Azure Container Instances 커넥터를 포함하는 AKS(Azure Kubernetes Service)의 크기 조정에 대해 알아봅니다.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: aaa279596532d3a1d47a974b48a45bd67101fa95
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768625"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>애플리케이션에 대한 AKS(Azure Kubernetes Service)의 크기 조정 옵션

AKS(Azure Kubernetes Service)에서 애플리케이션을 실행할 때 컴퓨팅 리소스의 양을 늘리거나 줄여야 할 수 있습니다. 필요한 애플리케이션 인스턴스 수가 달라지므로 기본 Kubernetes 노드 수도 변경해야 할 수 있습니다. 또한 많은 수의 추가 응용 프로그램 인스턴스를 신속 하 게 프로 비전 해야 할 수도 있습니다.

이 문서에서는 AKS에서 애플리케이션 크기를 조정하는 데 도움이 되는 핵심 개념을 소개합니다.

- [수동 크기 조정](#manually-scale-pods-or-nodes)
- [HPA(Horizontal Pod Autoscaler)](#horizontal-pod-autoscaler)
- [클러스터 자동 크기 조정기](#cluster-autoscaler)
- [AKS와 ACI(Azure Container Instance) 통합](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>수동으로 Pod 또는 노드 크기 조정

복제본(Pod) 및 노드의 크기를 수동으로 조정하면서 애플리케이션이 사용 가능한 리소스 및 상태 변화에 대응하는 방식을 테스트할 수 있습니다. 또한 리소스 크기를 수동으로 조정하여 고정 비용을 유지하기 위해 사용할 고정된 리소스 양(예: 노드 수)을 정의할 수도 있습니다. 수동으로 크기를 조정 하려면 복제본 또는 노드 수를 정의 합니다. 그런 다음 Kubernetes API는 해당 복제본 또는 노드 수에 따라 추가 pod 또는 드레이닝 노드 만들기를 예약 합니다.

노드를 축소 하는 경우 Kubernetes API는 클러스터에서 사용 하는 계산 형식에 연결 된 관련 Azure 계산 API를 호출 합니다. 예를 들어 VM Scale Sets 기반으로 하는 클러스터의 경우 제거할 노드를 선택 하는 논리는 VM Scale Sets API에 의해 결정 됩니다. 규모 축소에서 노드를 제거 하기 위해 노드를 선택 하는 방법에 대한 자세한 내용은 [Vmss FAQ](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)를 참조 하세요.

Pod 및 노드를 수동으로 확장 하는 작업을 시작 하려면 [AKS에서 응용 프로그램 크기 조정][aks-scale]을 참조 하세요.

## <a name="horizontal-pod-autoscaler"></a>Horizontal Pod Autoscaler

Kubernetes는 HPA(Horizontal Pod Autoscaler)를 사용하여 리소스 수요를 모니터링하고 복제본 수를 자동으로 조정합니다. 기본적으로 Horizontal Pod Autoscaler는 Metrics API에서 30초 간격으로 필요한 복제본 개수 변경을 확인합니다. 변경이 필요한 경우 그에 따라 복제본 수가 늘어나거나 줄어듭니다. Horizontal Pod Autoscaler는 Metrics Server for Kubernetes 1.8+를 배포한 AKS 클러스터에서 작동합니다.

![Kubernetes Horizontal Pod Autoscaling](media/concepts-scale/horizontal-pod-autoscaling.png)

지정된 배포에 대한 Horizontal Pod Autoscaler를 구성하는 경우 실행할 수 있는 최소 및 최대 복제본 수를 정의합니다. 또한 모니터링하고 크기 조정 결정의 기준으로 사용할 메트릭(예: CPU 사용량)도 정의합니다.

AKS에서 수평 pod autoscaler을 시작 하려면 [AKS에서 자동 크기 조정 pod][aks-hpa]를 참조 하세요.

### <a name="cooldown-of-scaling-events"></a>크기 조정 이벤트 휴지

Horizontal Pod Autoscaler는 30분 간격으로 Metrics API를 확인하므로 다른 확인이 되기 전에 이전 크기 조정 이벤트가 제대로 완료되지 않았을 수 있습니다. 이 동작으로 인해 이전 크기 조정 이벤트에서 응용 프로그램 워크 로드를 수신 하 고 리소스를 적절 하 게 조정 하기 전까지 수평 pod autoscaler에서 복제본의 수를 변경할 수 있습니다.

이러한 경합 이벤트를 최소화 하기 위해 쿨 또는 delay 값이 설정 됩니다. 이러한 값은 다른 크기 조정 이벤트가 트리거되기 위해, Horizontal Pod Autoscaler가 크기 조정 이벤트 이후에 대기해야 하는 기간을 정의합니다. 이 동작을 통해 새 복제본 수를 적용 하 고 메트릭 API를 사용 하 여 분산 작업을 반영할 수 있습니다. 기본적으로 규모 확장 이벤트에 따른 지연은 3분이며 규모 축소 이벤트에 따른 지연은 5분입니다.

현재는 이러한 쿨 다운 (기본값) 값을 튜닝할 수 없습니다.

## <a name="cluster-autoscaler"></a>클러스터 자동 크기 조정기

Kubernetes에는 변경 pod 요청에 응답 하기 위해 노드 풀에서 요청 된 계산 리소스에 따라 노드 수를 조정 하는 cluster autoscaler가 있습니다. 기본적으로 클러스터 autoscaler는 노드 수의 필수 변경에 대해 10 초 마다 메트릭 API 서버를 확인 합니다. 클러스터 자동 크기 조정기가 변경이 필요하다고 판단하면 AKS 클러스터의 노드 수가 그에 따라 증가하거나 감소합니다. 클러스터 자동 크기 조정기는 Kubernetes1.10.x 이상을 실행하는 RBAC 사용 AKS 클러스터에서 작동합니다.

![Kubernetes 클러스터 자동 크기 조정기](media/concepts-scale/cluster-autoscaler.png)

클러스터 자동 크기 조정기는 일반적으로 Horizontal Pod Autoscaler와 함께 사용됩니다. 이러한 기능을 함께 사용할 경우 Horizontal Pod Autoscaler는 애플리케이션 요구를 기준으로 Pod의 수를 늘리거나 줄이고, 클러스터 자동 크기 조정기는 해당 추가 Pod를 실행하는 데 필요한 만큼 노드 수를 조정합니다.

AKS에서 클러스터 autoscaler을 시작 하려면 [AKS의 Cluster autoscaler][aks-cluster-autoscaler]를 참조 하십시오.

### <a name="scale-up-events"></a>규모 확장 이벤트

노드에 요청 된 pod를 실행 하는 데 충분 한 계산 리소스가 없는 경우 해당 pod는 예약 프로세스를 통해 진행 되지 않습니다. 노드 풀 내에서 추가 계산 리소스를 사용할 수 없는 경우에는 pod를 시작할 수 없습니다.

노드 풀 리소스 제약 조건으로 인해 예약할 수 없는 클러스터 autoscaler 알림 pod 경우 추가 계산 리소스를 제공 하기 위해 노드 풀 내의 노드 수가 늘어납니다. 이러한 추가 노드가 성공적으로 배포되고 노드 풀 내에서 사용할 수 있게 되면 Pod가 해당 노드에서 실행되도록 예약됩니다.

애플리케이션 크기를 빠르게 조정해야 하는 경우 클러스터 자동 크기 조정기를 통해 배포되는 추가 노드가 예약된 Pod를 수락할 수 있게 될 때까지 일부 Pod는 예약 대기 상태를 유지할 수 있습니다. 버스트 요구가 높은 애플리케이션의 경우 가상 노드 및 Azure Container Instances를 사용하여 크기를 조정할 수 있습니다.

### <a name="scale-down-events"></a>규모 축소 이벤트

또한 클러스터 autoscaler는 최근 새 예약 요청을 받지 않은 노드의 pod 예약 상태를 모니터링 합니다. 이 시나리오는 노드 풀에 필요한 것 보다 많은 계산 리소스가 있으며 노드 수를 줄일 수 있음을 나타냅니다.

기본적으로 10분 동안 더 이상 필요하지 않은 상태로 임계값을 지난 노드는 삭제용으로 예약됩니다. 이 상황이 발생하면 Pod는 노드 풀 내의 다른 노드에서 실행되도록 예약되고 클러스터 자동 크기 조정기는 노드 수를 줄입니다.

Pod는 클러스터 자동 크기 조정기는 노드 수를 줄일 때 다른 노드에서 예약되므로 애플리케이션에는 중단이 발생할 수 있습니다. 중단을 최소화하려면 단일 Pod 인스턴스를 사용하는 애플리케이션을 피합니다.

## <a name="burst-to-azure-container-instances"></a>Azure Container Instances로 버스트

AKS 클러스터 크기를 빠르게 조정하기 위해 ACI(Azure Container Instances)와 통합할 수 있습니다. Kubernetes에는 복제본 및 노드 개수 크기를 조정하기 위한 기본 제공 구성 요소가 있습니다. 그러나 애플리케이션 크기를 빠르게 조정해야 할 경우 Horizontal Pod Autoscaler는 노드 풀의 기존 컴퓨팅 리소스가 제공할 수 있는 것보다 더 많은 Pod를 예약할 수 있습니다. 이 시나리오는 구성된 경우 클러스터 자동 크기 조정기가 노드 풀에 추가 노드를 배포하도록 트리거하지만, 이러한 노드가 성공적으로 프로비전되고 Kubernetes 스케줄러가 Pod를 실행할 수 있도록 하는 데 몇 분 정도 걸릴 수 있습니다.

![Kubernetes를 ACI로 버스트 크기 조정](media/concepts-scale/burst-scaling.png)

ACI를 사용하면 추가 인프라 오버헤드 없이 컨테이너 인스턴스를 빠르게 배포할 수 있습니다. AKS와 연결할 경우 ACI는 AKS 클러스터의 안전한 논리 확장이 됩니다. 가상 [Kubelet][virtual-kubelet]을 기반으로 하는 [가상 노드][virtual-nodes-cli] 구성 요소는 ACI를 가상 Kubernetes 노드로 표시 하는 AKS 클러스터에 설치 됩니다. 그러면 Kubernetes는 AKS 클러스터에서 직접, VM 노드의 Pod로 실행되는 Pod가 아니라 가상 노드를 통해 ACI 인스턴스로 실행되는 Pod를 예약할 수 있습니다. AKS의 가상 노드는 현재 프리뷰입니다.

가상 노드를 사용하기 위해 애플리케이션을 수정할 필요는 없습니다. AKS 및 ACI 간에 배포 크기를 조정할 수 있으며 클러스터 자동 크기 조정기가 AKS 클러스터에 새 노드를 배포할 때 지연이 발생하지 않습니다.

가상 노드는 AKS 클러스터가 있는 동일한 가상 네트워크의 추가 서브넷에 배포됩니다. 이 가상 네트워크 구성을 사용하여 ACI와 AKS 간 트래픽의 보안을 유지할 수 있습니다. AKS 클러스터와 마찬가지로, ACI 인스턴스는 다른 사용자로부터 격리된 보안, 논리 컴퓨팅 리소스입니다.

## <a name="next-steps"></a>다음 단계

응용 프로그램 크기 조정을 시작 하려면 먼저 빠른 시작을 따라 [Azure CLI를 사용 하 여 AKS 클러스터를 만듭니다][aks-quickstart]. 그런 다음, AKS 클러스터에서 애플리케이션 크기를 수동 또는 자동으로 조정할 수 있습니다.

- 수동으로 [pod][aks-manually-scale-pods] 또는 [노드][aks-manually-scale-nodes] 크기 조정
- [수평 pod autoscaler][aks-hpa] 사용
- [클러스터 autoscaler][aks-cluster-autoscaler] 사용

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크 로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS 액세스 및 id][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 저장소][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md