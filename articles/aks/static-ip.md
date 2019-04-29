---
title: AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소를 사용합니다.
description: 고정 IP 주소를 만들어 AKS(Azure Kubernetes Service) 부하 분산 장치에서 사용하는 방법에 대해 알아봅니다.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 03/04/2019
ms.date: 04/08/2019
ms.author: v-yeche
ms.openlocfilehash: d2e4314948eeda0c82c004414f894dafc4d4cff6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031650"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 공용 IP 주소 사용

기본적으로 AKS 클러스터에서 만드는 부하 분산 장치 리소스에 할당되는 공용 IP 주소는 해당 리소스의 수명 동안만 유효합니다. Kubernetes 서비스를 삭제하면 연결된 부하 분산 장치 및 IP 주소도 삭제됩니다. 특정 IP 주소를 할당하거나 재배포된 Kubernetes 서비스의 IP 주소를 유지하려는 경우에는 고정 공용 IP 주소를 만들어 사용할 수 있습니다.

이 문서에서는 고정 공용 IP 주소를 만들어 Kubernetes 서비스에 할당하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

현재 유일한 *기본 IP SKU*지원 됩니다. 작업 지원에 대해 진행 되는 *표준 IP* 리소스 SKU입니다. 자세한 내용은 [IP 주소 유형 및 Azure에서 할당 메서드][ip-sku]합니다.

## <a name="create-a-static-ip-address"></a>고정 IP 주소 만들기

AKS에서 사용할 고정 공용 IP 주소를 만드는 경우 **노드** 리소스 그룹에서 IP 주소 리소스를 만들어야 합니다. 리소스를 구분 하려는 경우 다음 섹션을 참조 하세요 [노드 리소스 그룹 외부의 고정 IP 주소를 사용 하 여](#use-a-static-ip-address-outside-of-the-node-resource-group)입니다.

먼저 노드가 리소스 그룹 이름을 가져옵니다 합니다 [az aks show] [ az-aks-show] 명령 및 추가 `--query nodeResourceGroup` 쿼리 매개 변수입니다. 다음 예제는 *myResourceGroup* 리소스 그룹에서 AKS 클러스터 *myAKSCluster*의 노드 리소스 그룹을 가져옵니다.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_chinaeast
```

이제 [az network public ip create][az-network-public-ip-create] 명령을 사용하여 고정 공용 IP 주소를 만듭니다. 이전 명령에서 가져온 노드 리소스 그룹 이름을 지정한 다음 IP 주소 리소스의 이름을 *myAKSPublicIP*와 같이 지정합니다.

```azurecli
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_chinaeast \
    --name myAKSPublicIP \
    --allocation-method static
```

IP 주소는 다음 압축 된 예제 출력 에서처럼 표시 됩니다.

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_chinaeast/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

나중에 [az network public-ip list][az-network-public-ip-list] 명령을 사용하여 공용 IP 주소를 가져올 수 있습니다. 만든 노드 리소스 그룹의 이름과 공용 IP 주소를 지정한 후 다음 예제와 같이 *ipAddress*를 쿼리합니다.

```azurecli
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_chinaeast --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>고정 IP 주소를 사용하여 서비스 만들기

고정 공용 IP 주소를 사용하여 서비스를 만들려면 고정 공용 IP 주소의 값과 `loadBalancerIP` 속성을 YAML 매니페스트에 추가합니다. 파일 `load-balancer-service.yaml`을 만들고 다음 YAML에 복사합니다. 이전 단계에서 만든 공용 IP 주소를 입력합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

`kubectl apply` 명령을 사용하여 서비스와 배포를 만듭니다.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>노드 리소스 그룹 외부의 고정 IP 주소 사용

Kubernetes 1.10 이상에서는 노드 리소스 그룹 외부에 생성된 고정 IP 주소를 사용할 수 있습니다. 다음 예제와 같이 AKS 클러스터에서 사용하는 서비스 주체에 다른 리소스 그룹에 대한 위임된 권한이 있어야 합니다.

```azurecli
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

노드 리소스 그룹 외부의 IP 주소를 사용하려면 서비스 정의에 주석을 추가합니다. 다음 예제에서는 *myResourceGroup*이라는 리소스 그룹에 주석을 설정합니다. 사용자 고유의 리소스 그룹 이름을 입력합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

## <a name="troubleshoot"></a>문제 해결

고정 IP 주소에 정의 된 경우는 *loadBalancerIP* Kubernetes 서비스 매니페스트의 속성, 존재 하지 않거나 노드 리소스 그룹 및 구성 추가 위임이 없습니다, 부하 분산 장치 서비스를 만들지 않은 만들 수 없습니다. 문제를 해결하려면 [kubectl describe][kubectl-describe] 명령을 사용하여 서비스 만들기 이벤트를 검토합니다. 다음 예제와 같이 YAML 매니페스트에 지정된 대로 서비스의 이름을 입력합니다.

```console
kubectl describe service azure-load-balancer
```

Kubernetes 서비스 리소스에 대한 정보가 표시 됩니다. 다음 예제 출력 끝부분의 *Events*는 *사용자가 입력한 IP 주소를 찾을 수 없음*을 나타냅니다. 이러한 시나리오에서는 노드 리소스 그룹에 고정 공용 IP 주소를 만들었으며 Kubernetes 서비스 매니페스트에 지정된 IP 주소가 올바른지 확인합니다.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>다음 단계

애플리케이션에 대한 네트워크 트래픽을 추가로 제어하려는 경우에는 [수신 컨트롤러 만들기][aks-ingress-basic]를 대신 수행할 수 있습니다. [고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기][aks-static-ingress]를 수행할 수도 있습니다.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-create
[az-network-public-ip-list]: https://docs.azure.cn/zh-cn/cli/network/public-ip?view=azure-cli-latest#az-network-public-ip-list
[az-aks-show]: https://docs.azure.cn/zh-cn/cli/aks?view=azure-cli-latest#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku