---
title: 근접 배치 그룹을 사용하여 AKS(Azure Kubernetes Service) 클러스터의 대기 시간 단축
description: 근접 배치 그룹을 사용하여 AKS 클러스터 워크로드의 대기 시간을 단축하는 방법을 알아봅니다.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779600"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>근접 배치 그룹을 사용하여 대기 시간 단축

> [!Note]
> AKS에서 근접 배치 그룹을 사용하는 경우 공동 배치는 에이전트 노드에만 적용됩니다. 노드 간 대기 시간과 해당하는 호스트된 Pod 간 대기 시간이 단축됩니다. 공동 배치는 클러스터의 컨트롤 플레인 배치에 영향을 주지 않습니다.

Azure에서 애플리케이션을 배포할 때 지역 또는 가용성 영역에 걸쳐 VM(가상 머신) 인스턴스를 분산하면 네트워크 대기 시간이 발생하여 애플리케이션의 전체 성능에 영향을 줄 수 있습니다. 근접 배치 그룹은 Azure 컴퓨팅 리소스가 물리적으로 서로 가깝게 배치되도록 하는 데 사용되는 논리적 그룹화입니다. 게임, 엔지니어링 시뮬레이션, HFT(자주 발생하는 거래) 같은 일부 애플리케이션에서는 대기 시간이 짧아 작업이 신속하게 완료되어야 합니다. 이러한 HPC(고성능 컴퓨팅) 시나리오에서는 클러스터의 노드 풀에 PPG([근접 배치 그룹](../virtual-machines/co-location.md#proximity-placement-groups))를 사용하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하려면 Azure CLI 버전 2.14 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

### <a name="limitations"></a>제한 사항

* 근접 배치 그룹은 최대 하나의 가용성 영역에 매핑할 수 있습니다.
* 노드 풀은 Virtual Machine Scale Sets를 사용하여 근접 배치 그룹을 연결해야 합니다.
* 노드 풀은 노드 풀을 만들 때만 근접 배치 그룹을 연결할 수 있습니다.

## <a name="node-pools-and-proximity-placement-groups"></a>노드 풀 및 근접 배치 그룹

근접 배치 그룹을 사용하여 배포하는 첫 번째 리소스는 특정 데이터 센터에 연결됩니다. 동일한 근접 배치 그룹을 사용하여 배포된 추가 리소스는 동일한 데이터 센터에 공동 배치됩니다. 근접 배치 그룹을 사용하는 모든 리소스를 중지(할당 취소)하거나 삭제한 후에는 더 이상 연결되지 않습니다.

* 많은 노드 풀을 단일 근접 배치 그룹에 연결할 수 있습니다.
* 노드 풀은 단일 근접 배치 그룹에만 연결할 수 있습니다.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>가용성 영역을 사용하여 근접 배치 그룹 구성

> [!NOTE]
> 근접 배치 그룹에는 최대 하나의 가용성 영역을 사용하는 노드 풀이 필요하지만 [99.9%의 기준 Azure VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)는 여전히 단일 영역의 VM에 적용됩니다.

근접 배치 그룹은 노드 풀 개념이며 각 개별 노드 풀과 연결됩니다. PPG 리소스를 사용해도 AKS 컨트롤 플레인 가용성에는 영향을 주지 않습니다. 영역을 사용하여 클러스터를 디자인하는 방법에는 영향을 줄 수 있습니다. 클러스터가 여러 영역에 걸쳐 분산되도록 하려면 다음과 같이 디자인하는 것이 좋습니다.

* 3개 영역을 사용하고 근접 배치 그룹이 연결되지 않은 첫 번째 시스템 풀을 사용하여 클러스터를 프로비저닝합니다. 이렇게 하면 시스템 Pod는 여러 영역에 걸쳐 분산되는 전용 노드 풀에 배치됩니다.
* 고유한 영역과 각 풀에 연결된 근접 배치 그룹을 사용하여 추가 사용자 노드 풀을 추가합니다. 예를 들어 영역 1과 PPG1의 nodepool1, 영역 2와 PPG2의 nodepool2, 영역 3과 PPG3의 nodepool3입니다. 이렇게 하면 클러스터 수준에서 노드가 여러 영역에 걸쳐 분산되고 각 개별 노드 풀은 전용 PPG 리소스와 함께 지정된 영역에 공동 배치됩니다.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>근접 배치 그룹을 사용하여 새 AKS 클러스터 만들기

다음 예제에서는 [az group create][az-group-create] 명령을 사용하여 *centralus* 지역에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다. 그런 다음, [az aks create][az-aks-create] 명령을 사용하여 *myAKSCluster* 라는 AKS 클러스터를 만듭니다.

가속화된 네트워킹을 통해 가상 머신의 네트워킹 성능이 크게 향상됩니다. 근접 배치 그룹을 가속화된 네트워킹과 함께 사용하는 것이 가장 좋습니다. 기본적으로 AKS는 둘 이상의 vCPU와 함께 대부분의 Azure 가상 머신이 포함되는 [지원되는 가상 머신 인스턴스](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)에서 가속화된 네트워킹을 사용합니다.

첫 번째 시스템 노드 풀에 연결된 근접 배치 그룹을 사용하여 새 AKS 클러스터를 만듭니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
다음 명령을 실행하고 반환되는 ID를 저장합니다.

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

이 명령은 다음 CLI 명령에 필요한 *id* 값을 포함하는 출력을 생성합니다.

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

다음 명령의 *myPPGResourceID* 값에 근접 배치 그룹 리소스 ID를 사용합니다.

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>기존 클러스터에 근접 배치 그룹 추가

새 노드 풀을 만들어 기존 클러스터에 근접 배치 그룹을 추가할 수 있습니다. 그런 다음, 필요에 따라 기존 워크로드를 새 노드 풀로 마이그레이션하고 원래 노드 풀을 삭제할 수 있습니다.

앞에서 만든 것과 동일한 근접 배치 그룹을 사용합니다. 그러면 AKS 클러스터에 있는 두 노드 풀의 에이전트 노드가 동일한 데이터 센터에 물리적으로 배치됩니다.

앞에서 만든 근접 배치 그룹의 리소스 ID를 사용하고 [`az aks nodepool add`][az-aks-nodepool-add] 명령을 사용하여 새 노드 풀을 추가합니다.

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>정리

클러스터를 삭제하려면 [`az group delete`][az-group-delete] 명령을 사용하여 AKS 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

* [근접 배치 그룹][proximity-placement-groups]에 대해 자세히 알아봅니다.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete