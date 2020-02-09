---
title: Azure Kubernetes 서비스에서 가용성 영역 사용 (AKS)
description: Azure Kubernetes 서비스 (AKS)의 가용성 영역에 노드를 배포 하는 클러스터를 만드는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.custom: fasttrack-edit
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: b73cb09f95fa2b23fb23fb719fe57143e1731ceb
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086515"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>가용성 영역을 사용 하는 AKS (Azure Kubernetes Service) 클러스터 만들기

AKS (Azure Kubernetes Service) 클러스터는 기본 Azure 계산 인프라의 논리적 섹션에서 노드 및 저장소와 같은 리소스를 배포 합니다. 이 배포 모델은 노드가 단일 Azure 데이터 센터의 개별 업데이트 및 장애 도메인에서 실행 되는지 확인할 수 있습니다. 이 기본 동작을 사용 하 여 배포 된 AKS 클러스터는 하드웨어 오류 또는 계획 된 유지 관리 이벤트를 방지할 수 있는 높은 수준의 가용성을 제공 합니다.

응용 프로그램에 더 높은 수준의 가용성을 제공 하기 위해 AKS 클러스터는 가용성 영역 간에 분산 될 수 있습니다. 이러한 영역은 지정 된 지역 내에서 물리적으로 분리 된 데이터 센터입니다. 클러스터 구성 요소가 여러 영역에 걸쳐 배포 되는 경우 AKS 클러스터는 이러한 영역 중 하나에서 오류를 허용할 수 있습니다. 응용 프로그램 및 관리 작업은 전체 데이터 센터 하나에 문제가 있는 경우에도 계속 사용할 수 있습니다.

이 문서에서는 AKS 클러스터를 만들고 노드 구성 요소를 가용성 영역에 분산 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.76 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="limitations-and-region-availability"></a>제한 사항 및 지역 가용성

AKS 클러스터는 현재 다음 지역에서 가용성 영역을 사용 하 여 만들 수 있습니다.

* 미국 중부
* 미국 동부 2
* 미국 동부
* 프랑스 중부
* 일본 동부
* 북유럽
* 동남아시아
* 영국 남부
* 서유럽
* 미국 서부 2

가용성 영역을 사용 하 여 AKS 클러스터를 만들 때는 다음과 같은 제한 사항이 적용 됩니다.

* 클러스터를 만들 때에만 가용성 영역을 사용할 수 있습니다.
* 클러스터를 만든 후에는 가용성 영역 설정을 업데이트할 수 없습니다. 또한 가용성 영역을 사용 하도록 기존의 비 가용성 영역 클러스터를 업데이트할 수 없습니다.
* AKS 클러스터를 만든 후에는 가용성 영역을 사용 하지 않도록 설정할 수 없습니다.
* 선택한 노드 크기 (VM SKU)를 모든 가용성 영역에서 사용할 수 있어야 합니다.
* 가용성 영역을 사용 하도록 설정 된 클러스터는 영역 간에 배포 하기 위해 Azure 표준 부하 분산 장치를 사용 해야 합니다.
* 표준 부하 분산 장치를 배포 하려면 Kubernetes 버전 1.13.5 이상을 사용 해야 합니다.

가용성 영역을 사용 하는 AKS 클러스터는 부하 분산 장치 유형의 기본값인 Azure 부하 분산 장치 *표준* SKU를 사용 해야 합니다. 이 부하 분산 장치 유형은 클러스터를 만들 때에만 정의할 수 있습니다. 표준 부하 분산 장치의 제한 사항 및 제한 사항에 대한 자세한 내용은 [Azure 부하 분산 장치 표준 SKU 제한][standard-lb-limitations]을 참조 하세요.

### <a name="azure-disks-limitations"></a>Azure 디스크 제한 사항

Azure managed disks를 사용 하는 볼륨은 현재 영역 리소스가 아닙니다. 원래 영역에서 다른 영역으로 다시 예약 된 pod는 이전 디스크를 다시 연결할 수 없습니다. 영역 문제에 걸쳐 있을 수 있는 영구 저장소가 필요 하지 않은 상태 비저장 워크 로드를 실행 하는 것이 좋습니다.

상태 저장 워크 로드를 실행 해야 하는 경우 pod 사양에서 taints 및 tolerations를 사용 하 여 Kubernetes scheduler에 디스크와 동일한 영역에 pod를 만들도록 지시 합니다. 또는 pod에 연결할 수 있는 Azure Files와 같은 네트워크 기반 저장소를 영역 간에 예약 된 대로 사용 합니다.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>AKS 클러스터에 대한 가용성 영역 개요

가용성 영역은 데이터 센터 오류 로부터 응용 프로그램 및 데이터를 보호 하는 고가용성 제품입니다. 영역은 Azure 지역 내에서 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 복원력을 보장하려면 활성화된 모든 지역에서 최소한 세 개의 별도 영역이 필요합니다. 한 지역 내에서 가용성 영역을 물리적으로 구분하면 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호할 수 있습니다. 영역 중복 서비스는 가용성 영역에서 응용 프로그램 및 데이터를 복제 하 여 단일 오류 지점이 없도록 보호 합니다.

자세한 내용은 [Azure의 가용성 영역 이란?][az-overview]을 참조 하세요.

가용성 영역을 사용 하 여 배포 된 AKS 클러스터는 단일 지역 내의 여러 영역에 노드를 배포할 수 있습니다. 예를 들어 *미국 동부 2* 지역의 클러스터는 *미국 동부 2*의 모든 3 개 가용성 영역에 노드를 만들 수 있습니다. 이 AKS 클러스터 리소스 배포는 특정 영역의 오류에 대한 복원 력을 제공 하므로 클러스터 가용성이 향상 됩니다.

![가용성 영역 간 AKS 노드 배포](media/availability-zones/aks-availability-zones.png)

영역 중단에서 노드는 수동으로 또는 클러스터 autoscaler를 사용 하 여 균형 수 있습니다. 단일 영역을 사용할 수 없게 되 면 응용 프로그램은 계속 실행 됩니다.

## <a name="create-an-aks-cluster-across-availability-zones"></a>가용성 영역에서 AKS 클러스터 만들기

[Az aks create][az-aks-create] 명령을 사용 하 여 클러스터를 만들 때 `--zones` 매개 변수는 배포할 영역 에이전트 노드를 정의 합니다. 클러스터에 대한 AKS 제어 평면 구성 요소는 클러스터를 만들 때 `--zones` 매개 변수를 정의할 때 사용 가능한 가장 높은 구성의 영역에도 분산 됩니다.

AKS 클러스터를 만들 때 기본 에이전트 풀에 대한 영역을 정의 하지 않으면 클러스터의 AKS 제어 평면 구성 요소가 가용성 영역을 사용 하지 않습니다. [Az aks nodepool add][az-aks-nodepool-add] 명령을 사용 하 여 노드 풀을 더 추가 하 고 이러한 새 노드에 대해 `--zones` 지정할 수 있지만, 제어 평면 구성 요소는 가용성 영역을 인식 하지 않고 유지 됩니다. 노드 풀 또는 AKS 제어 평면 구성 요소를 배포한 후에는 영역 인식 기능을 변경할 수 없습니다.

다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에 *myAKSCluster* 라는 AKS 클러스터를 만듭니다. 총 *3* 개의 노드가 생성 됩니다. 1 개의 에이전트는 영역 *1*에 있고, 하나는 *2*로, *3은 3*입니다. AKS 제어 평면 구성 요소는 클러스터 만들기 프로세스의 일부로 정의 되므로 사용 가능한 가장 높은 구성의 영역에도 분산 됩니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

AKS 클러스터를 만드는 데 몇 분이 걸립니다.

## <a name="verify-node-distribution-across-zones"></a>영역 간 노드 배포 확인

클러스터가 준비 되 면 확장 집합의 에이전트 노드를 나열 하 여 해당 노드를 배포 하는 가용성 영역을 확인 합니다.

먼저 [az AKS get 자격][az-aks-get-credentials] 증명 명령을 사용 하 여 AKS 클러스터 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

그런 다음 [kubectl 설명][kubectl-describe] 명령을 사용 하 여 클러스터의 노드를 나열 합니다. 다음 예제와 같이 *failure-domain.beta.kubernetes.io/zone* 값을 필터링 합니다.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

다음 예제 출력은 지정 된 지역 및 가용성 영역에 분산 된 세 개의 노드를 보여 줍니다 (예: 첫 번째 가용성 영역에 대한 *eastus2-1* , 두 번째 가용성 영역에 대해 *eastus2-2* ).

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

에이전트 풀에 노드를 더 추가 하면 Azure 플랫폼에서 기본 Vm을 지정 된 가용성 영역에 자동으로 배포 합니다.

최신 Kubernetes 버전 (1.17.0 이상)에서 AKS는 더 이상 사용 되지 않는 `failure-domain.beta.kubernetes.io/zone`외에도 `topology.kubernetes.io/zone` 최신 레이블을 사용 합니다.

## <a name="verify-pod-distribution-across-zones"></a>영역 간 pod 배포 확인

[잘 알려진 레이블, 주석 및 Taints][kubectl-well_known_labels]에 설명 된 대로 Kubernetes는 `failure-domain.beta.kubernetes.io/zone` 레이블을 사용 하 여 사용 가능한 여러 영역에서 자동으로 복제 컨트롤러나 서비스에 pod를 배포 합니다. 이를 테스트 하기 위해 클러스터를 3 개에서 5 개 노드로 확장 하 여 올바른 pod 분산을 확인할 수 있습니다.

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

몇 분 후에 크기 조정 작업이 완료 되 면 명령 `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"`이 샘플과 유사한 출력을 제공 해야 합니다.

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

여기서 볼 수 있듯이 영역 1과 2에는 두 개의 추가 노드가 있습니다. 3 개의 복제본으로 구성 된 응용 프로그램을 배포할 수 있습니다. NGINX를 예제로 사용 합니다.

```console
kubectl run nginx --image=nginx --replicas=3
```

Pod가 실행 되는 노드를 확인 하는 경우 세 개의 다른 가용성 영역에 해당 하는 pod에서 pod가 실행 되 고 있는 것을 볼 수 있습니다. 예를 들어 명령 `kubectl describe pod | grep -e "^Name:" -e "^Node:"`를 사용 하면 다음과 비슷한 출력을 얻을 수 있습니다.

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

이전 출력에서 볼 수 있듯이 첫 번째 pod는 가용성 영역 `eastus2-1`에 있는 노드 0에서 실행 됩니다. 두 번째 pod는 `eastus2-3`에 해당 하는 노드 2에서 실행 되며, `eastus2-2`에서 노드 4의 세 번째 pod입니다. 추가 구성 없이 Kubernetes는 세 가지 가용성 영역에 걸쳐 pod를 올바르게 분산 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가용성 영역을 사용 하는 AKS 클러스터를 만드는 방법에 대해 자세히 설명 합니다. 항상 사용 가능한 클러스터에 대한 자세한 고려 사항은 [AKS에서 비즈니스 연속성 및 재해 복구에 대한 모범 사례][best-practices-bc-dr]를 참조 하세요.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
