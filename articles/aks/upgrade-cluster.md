---
title: AKS(Azure Kubernetes Service) 클러스터 업그레이드
description: AKS(Azure Kubernetes Service) 클러스터를 업그레이드하는 방법 알아보기
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 02/12/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 59d52db8c3f5f8968eae1a544abe1e5c6bbaacca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028443"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터 업그레이드

AKS 클러스터의 수명 주기의 일환으로, 최신 Kubernetes 버전으로 업그레이드해야 하는 경우가 많습니다. 최신 Kubernetes 보안 릴리스를 적용하거나 최신 기능으로 업그레이드하는 것은 중요합니다. 이 문서에서는 기존 AKS 클러스터를 업그레이드하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 진행하려면 Azure CLI 버전 2.0.56 이상을 실행하고 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="check-for-available-aks-cluster-upgrades"></a>사용할 수 있는 AKS 클러스터 업그레이드를 위한 확인

클러스터에 사용할 수 있는 Kubernetes 릴리스를 확인하려면 [az aks get-upgrades][az-aks-get-upgrades] 명령을 사용합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터로 가능한 업그레이드를 확인합니다.

```azurecli
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> AKS 클러스터를 업그레이드하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 예를 들어 *1.10.x* -> *1.11.x* 또는 *1.11.x* -> *1.12.x* 업그레이드는 가능하지만, *1.10.x* -> *1.12.x* 업그레이드는 가능하지 않습니다.
>
> *1.10.x* -> *1.12.x* 업그레이드를 수행하려면 먼저 *1.10.x* -> *1.11.x* 업그레이드를 수행한 다음, *1.11.x* -> *1.12.x* 업그레이드를 수행합니다.

다음 예제 출력에서는 *1.11.5* 또는 *1.11.6* 버전으로 클러스터를 업그레이드할 수 있음을 보여줍니다.

```console
Name     ResourceGroup    MasterVersion    NodePoolVersion    Upgrades
-------  ---------------  ---------------  -----------------  --------------
default  myResourceGroup  1.10.12          1.10.12            1.11.5, 1.11.6
```

## <a name="upgrade-an-aks-cluster"></a>AKS 클러스터 업그레이드

AKS 클러스터에 대한 사용 가능한 버전 목록을 통해 [az aks upgrade][az-aks-upgrade] 명령을 사용하여 업그레이드합니다. 업그레이드 프로세스 동안 AKS는 클러스터에 새 노드를 추가한 다음, 노드를 한 번에 하나씩 신중하게 [차단 및 드레이닝][kubernetes-drain]하여 실행 중인 애플리케이션에 대한 중단을 최소화합니다. 다음 예제에서는 클러스터를 버전 *1.11.6*으로 업그레이드합니다.

```azurecli
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.6
```

노드 수에 따라 클러스터를 업그레이드하는 데 몇 분 정도 걸립니다.

업그레이드가 성공적이었는지 확인하려면 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

다음 예제 출력은 클러스터가 이제 *1.11.6*을 실행하고 있음을 보여줍니다.

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  chinaeast2      myResourceGroup  1.11.6               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.chinaeast2.azmk8s.io
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 기존 AKS 클러스터를 업그레이드하는 방법을 설명했습니다. AKS 클러스터의 배포 및 관리에 대해 더 자세히 알아보려면 자습서 세트를 참조하세요.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-get-upgrades]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-get-upgrades
[az-aks-upgrade]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-upgrade
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show