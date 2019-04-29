---
title: AKS(Azure Kubernetes Service) 클러스터 크기 조정
description: AKS(Azure Kubernetes Service) 클러스터의 노드 수를 조정하는 방법을 알아봅니다.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/10/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 558a3b6dc15293ab9a0895aa4f9f709ba2d0a51f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61032166"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>AKS(Azure Kubernetes Service) 클러스터의 노드 수 조정

애플리케이션의 리소스 요구량이 변경되면 AKS 클러스터의 크기를 수동으로 조정하여 실행되는 노드 수를 변경할 수 있습니다. 클러스터를 축소하면 실행 중인 애플리케이션의 중단을 최소화하기 위해 노드 수가 적절하게 [제한 및 감소][kubernetes-drain]됩니다. 반면 클러스터를 확장하면 Kubernetes 클러스터에서 노드를 `Ready`로 표시할 때까지 `az` 명령이 대기합니다.

## <a name="scale-the-cluster-nodes"></a>클러스터 노드 크기 조정

먼저 [az aks show][az-aks-show] 명령을 사용하여 노드 풀의 *name*을 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 클러스터의 노드 풀 이름을 가져옵니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

다음 예제 출력에는 *name*이 *nodepool1*로 표시되어 있습니다.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

`az aks scale` 명령을 사용하여 클러스터 노드의 크기를 조정합니다. 다음 예제에서는 *myAKSCluster*라는 클러스터를 단일 노드로 크기 조정합니다. 위 명령에서 *nodepool1*과 같은 실제 *--nodepool-name*을 입력합니다.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

다음 예제 출력에는 *agentPoolProfiles* 섹션에 나와 있는 것처럼 클러스터의 크기가 노드 하나로 올바르게 조정되었음이 표시됩니다.

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  "dnsPrefix": "myAKSClust-myResourceGroup-19da35",
  "enableRbac": true,
  "fqdn": "myaksclust-myresourcegroup-19da35-0d60b16a.hcp.chinaeast2.azmk8s.io",
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "kubernetesVersion": "1.9.11",
  "linuxProfile": {
    "adminUsername": "azureuser",
    "ssh": {
      "publicKeys": [
        {
          "keyData": "[...]"
        }
      ]
    }
  },
  "location": "chinaeast2",
  "name": "myAKSCluster",
  "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "networkPlugin": "kubenet",
    "networkPolicy": null,
    "podCidr": "10.244.0.0/16",
    "serviceCidr": "10.0.0.0/16"
  },
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_chinaeast2",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "servicePrincipalProfile": {
    "clientId": "[...]",
    "secret": null
  },
  "tags": null,
  "type": "Microsoft.ContainerService/ManagedClusters"
}
```

## <a name="next-steps"></a>다음 단계

AKS 자습서를 통한 AKS 배포 및 관리에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show