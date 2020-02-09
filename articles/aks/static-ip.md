---
title: AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 IP 주소를 사용합니다.
description: 고정 IP 주소를 만들어 AKS(Azure Kubernetes Service) 부하 분산 장치에서 사용하는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/06/2019
ms.author: mlearned
ms.openlocfilehash: 8457f1c0c5b6107c4b44f6f00236a33f7c67452a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325434"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>AKS(Azure Kubernetes Service) 부하 분산 장치에 고정 공용 IP 주소 사용

기본적으로 AKS 클러스터에서 만드는 부하 분산 장치 리소스에 할당되는 공용 IP 주소는 해당 리소스의 수명 동안만 유효합니다. Kubernetes 서비스를 삭제하면 연결된 부하 분산 장치 및 IP 주소도 삭제됩니다. 특정 IP 주소를 할당하거나 재배포된 Kubernetes 서비스의 IP 주소를 유지하려는 경우에는 고정 공용 IP 주소를 만들어 사용할 수 있습니다.

이 문서에서는 고정 공용 IP 주소를 만들어 Kubernetes 서비스에 할당하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

이 문서에서는 표준 sku 부하 분산 장치를 사용 *하는* *표준* sku IP를 사용 하는 방법을 설명 합니다. 자세한 내용은 [Azure의 IP 주소 유형 및 할당 방법][ip-sku]을 참조 하세요.

## <a name="create-a-static-ip-address"></a>고정 IP 주소 만들기

[Az network public ip create][az-network-public-ip-create] 명령을 사용 하 여 고정 공용 ip 주소를 만듭니다. 다음은 *Myresourcegroup* 리소스 그룹에 *MYAKSPUBLICIP* 이라는 고정 IP 리소스를 만듭니다.

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> AKS 클러스터에서 *기본* sku 부하 분산 장치를 사용 하는 경우 공용 IP를 정의할 때 *sku* 매개 변수에 대한 *기본* 를 사용 합니다. 기본 *Sku ip만* *기본* sku 부하 분산 장치에서 작동 하며 표준 *sku ip만* *표준* sku 부하 분산 장치에서 작동 합니다. 

IP 주소는 다음 압축 예제 출력과 같이 표시 됩니다.

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

나중에 [az network 공용-ip list][az-network-public-ip-list] 명령을 사용 하 여 공용 ip 주소를 가져올 수 있습니다. 만든 노드 리소스 그룹의 이름과 공용 IP 주소를 지정한 후 다음 예제와 같이 *ipAddress*를 쿼리합니다.

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>고정 IP 주소를 사용하여 서비스 만들기

서비스를 만들기 전에 AKS 클러스터에서 사용 하는 서비스 사용자에 게 다른 리소스 그룹에 대한 위임 된 권한이 있는지 확인 합니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

고정 공용 IP 주소를 사용 하 여 *LoadBalancer* 서비스를 만들려면 `loadBalancerIP` 속성 및 고정 공용 ip 주소의 값을 yaml 매니페스트에 추가 합니다. 파일 `load-balancer-service.yaml`을 만들고 다음 YAML에 복사합니다. 이전 단계에서 만든 공용 IP 주소를 입력합니다. 또한 다음 예제에서는 주석을 *Myresourcegroup*이라는 리소스 그룹에 설정 합니다. 사용자 고유의 리소스 그룹 이름을 제공 합니다.

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

`kubectl apply` 명령을 사용하여 서비스와 배포를 만듭니다.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="troubleshoot"></a>문제 해결

Kubernetes service 매니페스트의 *loadBalancerIP* 속성에 정의 된 고정 IP 주소가 존재 하지 않거나 노드 리소스 그룹에 만들어지지 않고 추가 위임이 구성 되지 않은 경우 부하 분산 장치 서비스를 만들지 못합니다. 문제를 해결 하려면 [kubectl 설명][kubectl-describe] 명령을 사용 하 여 서비스 만들기 이벤트를 검토 합니다. 다음 예제와 같이 YAML 매니페스트에 지정된 대로 서비스의 이름을 입력합니다.

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

응용 프로그램에 대한 네트워크 트래픽을 추가로 제어 하려면 [수신 컨트롤러][aks-ingress-basic]를 대신 만드는 것이 좋습니다. [고정 공용 IP 주소를 사용 하 여 수신 컨트롤러를 만들][aks-static-ingress]수도 있습니다.

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
