---
title: Azure Kubernetes 서비스에서 시스템 노드 풀 사용 (AKS)
description: AKS (Azure Kubernetes Service)에서 시스템 노드 풀을 만들고 관리 하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit
ms.openlocfilehash: e068984e02a468169f286ab5b783e531a54bd6ed
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949782"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스에서 시스템 노드 풀 관리 (AKS)

Azure Kubernetes 서비스 (AKS)에서 동일한 구성의 노드는 *노드 풀*로 그룹화 됩니다. 노드 풀에는 응용 프로그램을 실행 하는 기본 Vm이 포함 됩니다. 시스템 노드 풀 및 사용자 노드 풀은 AKS 클러스터에 대 한 두 개의 서로 다른 노드 풀 모드입니다. 시스템 노드 풀은 CoreDNS 및 tunnelfront와 같은 중요 한 시스템 pod를 호스트 하는 기본 목적을 제공 합니다. 사용자 노드 풀은 응용 프로그램 pod를 호스트 하는 기본 목적을 제공 합니다. 그러나 AKS 클러스터에 풀을 하나만 포함 하려는 경우에는 시스템 노드 풀에서 응용 프로그램 pod를 예약할 수 있습니다. 모든 AKS 클러스터에 하나 이상의 노드를 포함 하는 시스템 노드 풀이 하나 이상 있어야 합니다.

> [!Important]
> 프로덕션 환경에서 AKS 클러스터에 대 한 단일 시스템 노드 풀을 실행 하는 경우 노드 풀에 대해 세 개 이상의 노드를 사용 하는 것이 좋습니다.

## <a name="before-you-begin"></a>시작하기 전에

* Azure CLI 버전 2.3.1 이상이 설치 및 구성 되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations"></a>제한 사항

시스템 노드 풀을 지 원하는 AKS 클러스터를 만들고 관리 하는 경우 다음 제한 사항이 적용 됩니다.

* [Azure Kubernetes 서비스 (AKS)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조 하세요.
* AKS 클러스터는 가상 머신 확장 집합을 VM 유형 및 *표준* SKU 부하 분산 장치로 구축 해야 합니다.
* 노드 풀의 이름에는 소문자 영숫자만 사용할 수 있으며 소문자 문자로 시작 해야 합니다. Linux 노드 풀의 경우 길이는 1 자에서 12 자 사이 여야 합니다. Windows 노드 풀의 길이는 1에서 6 자 사이 여야 합니다.
* 2020-03-01 이상의 API 버전을 사용 하 여 노드 풀 모드를 설정 해야 합니다. 2020-03-01 보다 오래 된 API 버전에서 만든 클러스터는 사용자 노드 풀만 포함 하지만 [풀 모드 업데이트 단계](#update-existing-cluster-system-and-user-node-pools)를 수행 하 여 시스템 노드 풀을 포함 하도록 마이그레이션할 수 있습니다.
* 노드 풀의 모드는 필수 속성 이며 ARM 템플릿 또는 직접 API 호출을 사용 하는 경우 명시적으로 설정 해야 합니다.

## <a name="system-and-user-node-pools"></a>시스템 및 사용자 노드 풀

시스템 노드 풀의 경우 AKS는 **kubernetes.azure.com/mode: system** 레이블을 해당 노드에 자동으로 할당 합니다. 이로 인해 AKS는이 레이블을 포함 하는 노드 풀에서 시스템 pod 예약을 선호 합니다. 이 레이블은 시스템 노드 풀에서 응용 프로그램 pod 일정을 예약 하는 것을 방지 하지 않습니다. 그러나 잘못 구성 되거나 rogue 응용 프로그램 pod 실수로 시스템 pod를 방해 하지 않도록 응용 프로그램 pod에서 중요 한 시스템 pod를 격리 하는 것이 좋습니다. 전용 시스템 노드 풀을 만들어이 동작을 적용할 수 있습니다. Taint를 사용 `CriticalAddonsOnly=true:NoSchedule` 하 여 시스템 노드 풀에서 응용 프로그램 pod 예약 되지 않도록 합니다.

시스템 노드 풀에는 다음과 같은 제한 사항이 있습니다.

* 시스템 풀 osType는 Linux 여야 합니다.
* 사용자 노드 풀 osType는 Linux 또는 Windows 일 수 있습니다.
* 시스템 풀은 하나 이상의 노드를 포함 해야 하며, 사용자 노드 풀에는 0 개 이상의 노드가 포함 될 수 있습니다.
* 시스템 노드 풀에는 2 개 이상의 vCPUs 및 4GB 메모리의 VM SKU가 필요 합니다.
* 시스템 노드 풀은 [pod에 대 한 최소값 및 최대값 수식][maximum-pods]에 설명 된 대로 최소 30 개의 pod 지원 해야 합니다.
* 별색 노드 풀에는 사용자 노드 풀이 필요 합니다.

노드 풀을 사용 하 여 다음 작업을 수행할 수 있습니다.

* 전용 시스템 노드 풀 만들기 (의 노드 풀에 시스템 pod 예약 선호 `mode:system` )
* AKS 클러스터에서 다른 시스템 노드 풀을 사용할 수 있는 경우 시스템 노드 풀을 사용자 노드 풀로 변경 합니다.
* 사용자 노드 풀을 시스템 노드 풀로 변경 합니다.
* 사용자 노드 풀을 삭제 합니다.
* AKS 클러스터에서 다른 시스템 노드 풀을 사용할 수 있게 되 면 시스템 노드 풀을 삭제할 수 있습니다.
* AKS 클러스터에는 시스템 노드 풀이 여러 개 있을 수 있으며 시스템 노드 풀은 하나 이상 필요 합니다.
* 기존 노드 풀에서 변경할 수 없는 다양 한 설정을 변경 하려면 새 노드 풀을 만들어 해당 설정을 바꿀 수 있습니다. 한 가지 예는 새 maxPods 설정을 사용 하 여 새 노드 풀을 추가 하 고 이전 노드 풀을 삭제 하는 것입니다.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>시스템 노드 풀을 사용 하 여 새 AKS 클러스터 만들기

새 AKS 클러스터를 만들 때 단일 노드를 사용 하 여 시스템 노드 풀을 자동으로 만듭니다. 초기 노드 풀의 기본값은 시스템 형식 모드입니다. 를 사용 하 여 새 노드 풀 `az aks nodepool add` 을 만드는 경우 해당 노드 풀은 모드 매개 변수를 명시적으로 지정 하지 않는 한 사용자 노드 풀입니다.

다음 예제에서는 *미국 동부* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드를 포함 하는 하나의 전용 시스템 풀을 사용 하 여 *myAKSCluster* 라는 클러스터를 만듭니다. 프로덕션 워크 로드의 경우 3 개 이상의 노드가 있는 시스템 노드 풀을 사용 하 고 있는지 확인 합니다. 이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>기존 AKS 클러스터에 전용 시스템 노드 풀 추가

> [!Important]
> 노드 풀을 만든 후 CLI를 통해 노드 taints를 변경할 수 없습니다.

기존 AKS 클러스터에 하나 이상의 시스템 노드 풀을 추가할 수 있습니다. 사용자 노드 풀에서 응용 프로그램 pod를 예약 하 고 시스템 노드 풀을 중요 한 시스템 pod으로만 사용 하는 것이 좋습니다. 그러면 rogue 응용 프로그램 pod 실수로 시스템 pod를 종료 하는 것을 방지 합니다. `CriticalAddonsOnly=true:NoSchedule`시스템 노드 풀에 대해 [taint][aks-taints] 를 사용 하 여이 동작을 적용 합니다. 

다음 명령을 사용 하 여 모드 유형 시스템의 전용 노드 풀을 3 개 노드의 기본 개수로 추가 합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>노드 풀의 세부 정보 표시

다음 명령을 사용 하 여 노드 풀의 세부 정보를 확인할 수 있습니다.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

시스템 노드 풀에 대해 **시스템** 유형 모드가 정의 되 고 사용자 노드 풀에 대해 **사용자** 유형 모드가 정의 됩니다. 시스템 풀의 경우 taint가로 설정 되어 있는지 확인 합니다 `CriticalAddonsOnly=true:NoSchedule` . 그러면이 노드 풀에서 응용 프로그램 pod 예약 된 생물가 발생 하지 않습니다.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>기존 클러스터 시스템 및 사용자 노드 풀 업데이트

> [!NOTE]
> 2020-03-01 이상의 API 버전을 사용 하 여 시스템 노드 풀 모드를 설정 해야 합니다. 2020-03-01 보다 오래 된 API 버전에서 만든 클러스터는 그 결과로 사용자 노드 풀만 포함 합니다. 시스템 노드 풀 기능 및 이전 클러스터의 혜택을 받으려면 최신 Azure CLI 버전에서 다음 명령을 사용 하 여 기존 노드 풀의 모드를 업데이트 합니다.

시스템 및 사용자 노드 풀의 모드를 변경할 수 있습니다. AKS 클러스터에 다른 시스템 노드 풀이 이미 있는 경우에만 사용자 풀로 시스템 노드 풀을 변경할 수 있습니다.

이 명령은 시스템 노드 풀을 사용자 노드 풀로 변경 합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

이 명령은 사용자 노드 풀을 시스템 노드 풀로 변경 합니다.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>시스템 노드 풀 삭제

> [!Note]
> API 버전 2020-03-02 이전에 AKS 클러스터에서 시스템 노드 풀을 사용 하려면 새 시스템 노드 풀을 추가한 다음 원래 기본 노드 풀을 삭제 합니다.

이전에는 AKS 클러스터의 초기 기본 노드 풀 인 시스템 노드 풀을 삭제할 수 없습니다. 이제 클러스터에서 노드 풀을 유연 하 게 삭제할 수 있습니다. AKS 클러스터에는 시스템 노드 풀이 하나 이상 필요 하므로 AKS 클러스터에 두 개 이상의 시스템 노드 풀이 있어야 하나를 삭제할 수 있습니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>리소스 정리

클러스터를 삭제 하려면 [az group delete][az-group-delete] 명령을 사용 하 여 AKS 리소스 그룹을 삭제 합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 시스템 노드 풀을 만들고 관리 하는 방법을 배웠습니다. 여러 노드 풀을 사용 하는 방법에 대 한 자세한 내용은 [여러 노드 풀 사용][use-multiple-node-pools]을 참조 하세요.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
