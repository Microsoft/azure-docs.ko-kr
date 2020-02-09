---
title: AKS(Azure Kubernetes Service)에서 내부 부하 분산 장치 만들기
description: 내부 부하 분산 장치를 만들고 사용하여 AKS(Azure Kubernetes Service)를 통해 서비스를 노출하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 8b0b0358534c30407c9841d5c23560623545cf64
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044996"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치 사용

AKS(Azure Kubernetes Service)에서 애플리케이션에 대한 액세스를 제한하기 위해 내부 부하 분산 장치를 만들고 사용할 수 있습니다. 내부 부하 분산 장치는 Kubernetes 서비스가 Kubernetes 클러스터와 동일한 가상 네트워크에서 실행되는 애플리케이션에만 액세스할 수 있게 합니다. 이 문서에서는 AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치를 만들고 사용하는 방법을 보여줍니다.

> [!NOTE]
> Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다. 기본적으로 표준 SKU는 AKS 클러스터를 만들 때 사용 됩니다.  유형이 LoadBalancer 인 서비스를 만들 때 클러스터를 프로 비전 할 때와 동일한 LB 유형을 얻게 됩니다. 자세한 내용은 [Azure 부하 분산 장치 SKU 비교][azure-lb-comparison]를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

또한 Azure CLI 버전 2.0.59 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

기존 서브넷 또는 리소스 그룹을 사용 하는 경우 AKS 클러스터 서비스 주체에 네트워크 리소스를 관리할 수 있는 권한이 있어야 합니다. 일반적으로 위임 된 리소스의 서비스 사용자에 게 *네트워크 참가자* 역할을 할당 합니다. 사용 권한에 대한 자세한 내용은 [DELEGATE AKS access to Other Azure resources를][aks-sp]참조 하세요.

## <a name="create-an-internal-load-balancer"></a>내부 부하 분산 장치 만들기

내부 부하 분산 장치를 만들려면 다음 예제와 같이 서비스 형식 *LoadBalancer* 및 *azure-load-balancer-internal* 주석으로 `internal-lb.yaml`이라는 서비스 매니페스트를 만듭니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

[Kubectl apply][kubectl-apply] 를 사용 하 여 내부 부하 분산 장치를 배포 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f internal-lb.yaml
```

Azure 부하 분산 장치는 노드 리소스 그룹에 만들어지고 AKS 클러스터와 동일한 가상 네트워크에 연결 됩니다.

서비스 세부 정보를 보면 내부 부하 분산 장치의 IP 주소는 *EXTERNAL-IP* 열에 표시됩니다. 이 컨텍스트에서 *external* 은 부하 분산 장치의 외부 인터페이스와 관련 되어 있으며 공용 외부 IP 주소를 수신 하지는 않습니다. 다음 예제와 같이 IP 주소가 \<보류 중\>에서 실제 내부 IP 주소로 변경되는 데 1~2분이 소요될 수 있습니다.

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>IP 주소 지정

내부 부하 분산 장치를 통해 특정 IP 주소를 사용하려는 경우 부하 분산 장치 YAML 매니페스트에 *loadBalancerIP* 속성을 추가합니다. 지정된 IP 주소는 AKS 클러스터와 동일한 서브넷에 상주해야 하며 아직 리소스에 할당되면 안 됩니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

배포 된 경우 서비스 세부 정보를 볼 때 *외부 ip* 열의 ip 주소는 지정 된 ip 주소를 반영 합니다.

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>프라이빗 네트워크 사용

AKS 클러스터를 만들 때 고급 네트워킹 설정을 지정할 수 있습니다. 이 방법을 통해 클러스터를 기존 Azure 가상 네트워크 및 서브넷에 배포할 수 있습니다. 한 가지 시나리오는 온-프레미스 환경에 연결된 프라이빗 네트워크에 AKS 클러스터를 배포하여 내부에서 액세스할 수 있는 서비스만 실행하는 것입니다. 자세한 내용은 [Kubenet][use-kubenet] 또는 [Azure cni][advanced-networking]를 사용 하 여 사용자 고유의 가상 네트워크 서브넷 구성을 참조 하세요.

프라이빗 네트워크를 사용하는 AKS 클러스터에 내부 부하 분산 장치를 배포하려면 이전 단계를 변경하지 않아도 됩니다. 부하 분산 장치는 AKS 클러스터와 동일한 리소스 그룹에 만들어지지만 다음 예와 같이 프라이빗 가상 네트워크 및 서브넷에 연결됩니다.

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS 클러스터의 서비스 주체에 Azure 가상 네트워크 리소스가 배포되어 있는 리소스 그룹에 대한 *네트워크 기여자* 역할을 부여해야 할 수도 있습니다. `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`와 같이 [az aks show][az-aks-show]를 사용 하 여 서비스 사용자를 확인 합니다. 역할 할당을 만들려면 [az role 대입문 create][az-role-assignment-create] 명령을 사용 합니다.

## <a name="specify-a-different-subnet"></a>다른 서브넷 지정

부하 분산 장치에 대한 서브넷을 지정하려면 서비스에 *azure-load-balancer-internal-subnet* 주석을 추가합니다. 지정된 서브넷은 AKS 클러스터와 동일한 가상 네트워크에 있어야 합니다. 배포된 경우, 부하 분산 장치 *EXTERNAL-IP* 주소가 지정된 서브넷에 속합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>부하 분산 장치 삭제하기

내부 부하 분산 장치를 사용하는 모든 서비스가 삭제되면 부하 분산 장치 자체도 삭제됩니다.

또한 `kubectl delete service internal-app`과 같은 Kubernetes 리소스의 경우처럼 서비스를 직접 삭제할 수도 있습니다. 그러면 기본 Azure 부하 분산 장치도 삭제됩니다.

## <a name="next-steps"></a>다음 단계

Kubernetes services에 대한 자세한 내용은 [Kubernetes services 설명서][kubernetes-services]를 참조 하세요.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
