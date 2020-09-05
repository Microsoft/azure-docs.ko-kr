---
title: AKS (Azure Kubernetes Service) 노드 이미지 업그레이드
description: AKS 클러스터 노드 및 노드 풀에서 이미지를 업그레이드 하는 방법에 대해 알아봅니다.
author: laurenhughes
ms.author: lahugh
ms.service: container-service
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 744e62f8a2207cff400a96069fc6ea82866f6e2d
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055688"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>AKS (Azure Kubernetes Service) 노드 이미지 업그레이드

AKS는 노드의 이미지를 업그레이드할 수 있도록 지원 하므로 최신 OS 및 런타임 업데이트를 최신 상태로 유지 합니다. AKS는 최신 업데이트를 사용 하 여 일주일에 하나의 새 이미지를 제공 하므로 Linux 또는 Windows 패치를 비롯 하 여 최신 기능을 위해 노드의 이미지를 정기적으로 업그레이드 하는 것이 유용 합니다. 이 문서에서는 AKS 클러스터 노드 이미지를 업그레이드 하는 방법 뿐만 아니라 Kubernetes 버전을 업그레이드 하지 않고 노드 풀 이미지를 업데이트 하는 방법을 보여 줍니다.

AKS에서 제공 하는 최신 이미지에 대해 알아보려면 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases) 를 참조 하세요.

클러스터에 대 한 Kubernetes 버전을 업그레이드 하는 방법에 대 한 자세한 내용은 [AKS 클러스터 업그레이드][upgrade-cluster]를 참조 하세요.

## <a name="limitations"></a>제한 사항

* AKS 클러스터는 노드에 대 한 가상 머신 확장 집합을 사용 해야 합니다.

## <a name="install-the-aks-cli-extension"></a>AKS CLI 확장을 설치 합니다.

다음 핵심 CLI 버전이 해제 되기 전에 노드 이미지 업그레이드를 사용 하려면 *aks-preview* CLI 확장이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="upgrade-all-nodes-in-all-node-pools"></a>모든 노드 풀의 모든 노드 업그레이드

노드 이미지 업그레이드는를 사용 하 여 수행 됩니다 `az aks upgrade` . 노드 이미지를 업그레이드 하려면 다음 명령을 사용 합니다.

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료 되 면를 사용 `az aks show` 하 여 업데이트 된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>특정 노드 풀 업그레이드

노드 풀에서 이미지를 업그레이드 하는 것은 클러스터에서 이미지를 업그레이드 하는 것과 비슷합니다.

Kubernetes 클러스터 업그레이드를 수행 하지 않고 노드 풀의 OS 이미지를 업데이트 하려면 `--node-image-only` 다음 예제에서 옵션을 사용 합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

업그레이드가 완료 되 면를 사용 `az aks nodepool show` 하 여 업데이트 된 노드 풀 세부 정보를 가져옵니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>노드 서 지를 사용 하 여 노드 이미지 업그레이드

노드 이미지 업그레이드 프로세스의 속도를 높이려면 사용자 지정 가능한 노드 서 수 값을 사용 하 여 노드 이미지를 업그레이드할 수 있습니다. 기본적으로 AKS는 하나의 추가 노드를 사용 하 여 업그레이드를 구성 합니다.

업그레이드 속도를 높이려면 값을 사용 `--max-surge` 하 여 업그레이드에 사용할 노드 수를 구성 합니다. 이렇게 하면 더 빨리 완료할 수 있습니다. 다양 한 설정의 장단점에 대해 자세히 알아보려면 `--max-surge` [노드 서 수 업그레이드 사용자 지정][max-surge]을 참조 하세요.

다음 명령은 노드 이미지 업그레이드를 수행 하기 위한 최대 서 지 값을 설정 합니다.

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

업그레이드 하는 동안 다음 명령을 사용 하 여 노드 이미지의 상태를 확인 `kubectl` 하 고 현재 노드 이미지 정보를 필터링 합니다.

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

`az aks nodepool show`업데이트 된 노드 풀 세부 정보를 가져오려면를 사용 합니다. 현재 노드 이미지는 속성에 표시 됩니다 `nodeImageVersion` .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>다음 단계

- 최신 노드 이미지에 대 한 자세한 내용은 [AKS 릴리스 정보](https://github.com/Azure/AKS/releases) 를 참조 하세요.
- [AKS 클러스터 업그레이드][upgrade-cluster]를 사용 하 여 Kubernetes 버전을 업그레이드 하는 방법을 알아봅니다.
- [Azure Kubernetes 서비스 (AKS)에서 Linux 노드에 보안 및 커널 업데이트 적용][security-update]
- 여러 노드 풀 및 [여러 노드 풀 만들기 및 관리][use-multiple-node-pools]를 사용 하 여 노드 풀을 업그레이드 하는 방법에 대해 자세히 알아보세요.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[security-update]: node-updates-kured.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade-preview
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
