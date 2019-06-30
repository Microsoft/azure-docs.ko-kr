---
title: AKS(Azure Kubernetes Service)에서 내부 부하 분산 장치 만들기
description: 내부 부하 분산 장치를 만들고 사용하여 AKS(Azure Kubernetes Service)를 통해 서비스를 노출하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: iainfou
ms.openlocfilehash: 50a2161be4ee70f7ae5c8baa3816eb9f9943a5d2
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67358010"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치 사용

AKS(Azure Kubernetes Service)에서 애플리케이션에 대한 액세스를 제한하기 위해 내부 부하 분산 장치를 만들고 사용할 수 있습니다. 내부 부하 분산 장치는 Kubernetes 서비스가 Kubernetes 클러스터와 동일한 가상 네트워크에서 실행되는 애플리케이션에만 액세스할 수 있게 합니다. 이 문서에서는 AKS(Azure Kubernetes Service)를 통해 내부 부하 분산 장치를 만들고 사용하는 방법을 보여줍니다.

> [!NOTE]
> Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다.   기본적으로 *기본* SKU는 AKS에서 부하 분산 장치를 만들려면 서비스 매니페스트를 사용 하는 경우에 사용 됩니다. 자세한 내용은 [Azure load balancer SKU 비교][azure-lb-comparison]합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터에 필요한 경우 AKS 빠른 시작을 참조 하세요 [Azure CLI를 사용 하 여][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal]합니다.

또한 Azure cli 버전 2.0.59 또는 나중에 설치 하 고 구성한 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

AKS 클러스터 서비스 주체에 기존 서브넷 또는 리소스 그룹을 사용 하는 경우 네트워크 리소스를 관리할 수 있는 권한이 필요 합니다. 일반적으로 할당 합니다 *네트워크 참가자* 위임 된 리소스에 서비스 주체에 역할입니다. 사용 권한에 대 한 자세한 내용은 참조 하세요. [다른 Azure 리소스에 대 한 액세스를 대리자 AKS][aks-sp]합니다.

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

사용 하 여 내부 부하 분산 장치 배포 합니다 [kubectl 적용][kubectl-apply] YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f internal-lb.yaml
```

Azure load balancer는 노드 리소스 그룹에 만들어지고 AKS 클러스터와 동일한 가상 네트워크에 연결 합니다.

서비스 세부 정보를 보면 내부 부하 분산 장치의 IP 주소는 *EXTERNAL-IP* 열에 표시됩니다. 이 컨텍스트에서 *외부* 아닙니다 부하 분산 장치의 외부 인터페이스와 관련 하 여 외부 IP 주소를 공용에서 받는 것입니다. 다음 예제와 같이 IP 주소가 \<보류 중\>에서 실제 내부 IP 주소로 변경되는 데 1~2분이 소요될 수 있습니다. 

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

배포 하는 경우 서비스 세부 정보를의 IP 주소를 확인 합니다 *EXTERNAL-IP* 열은 지정 된 IP 주소를 반영:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

## <a name="use-private-networks"></a>프라이빗 네트워크 사용

AKS 클러스터를 만들 때 고급 네트워킹 설정을 지정할 수 있습니다. 이 방법을 통해 클러스터를 기존 Azure 가상 네트워크 및 서브넷에 배포할 수 있습니다. 한 가지 시나리오는 온-프레미스 환경에 연결된 프라이빗 네트워크에 AKS 클러스터를 배포하여 내부에서 액세스할 수 있는 서비스만 실행하는 것입니다. 자세한 내용은 참조 하세요. 사용 하 여 사용자 고유의 가상 네트워크 서브넷 구성 [Kubenet][use-kubenet] or [Azure CNI][advanced-networking]합니다.

프라이빗 네트워크를 사용하는 AKS 클러스터에 내부 부하 분산 장치를 배포하려면 이전 단계를 변경하지 않아도 됩니다. 부하 분산 장치는 AKS 클러스터와 동일한 리소스 그룹에 만들어지지만 다음 예와 같이 프라이빗 가상 네트워크 및 서브넷에 연결됩니다.

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> AKS 클러스터의 서비스 주체에 Azure 가상 네트워크 리소스가 배포되어 있는 리소스 그룹에 대한 *네트워크 기여자* 역할을 부여해야 할 수도 있습니다. 사용 하 여 서비스 주체를 보려면 [az aks show][az-aks-show]와 같은 `az aks show --resource-group myResourceGroup --name myAKSCluster --query "servicePrincipalProfile.clientId"`합니다. 역할 할당을 만들려면 사용 합니다 [az 역할 할당 만들기][az-role-assignment-create] 명령입니다.

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

Kubernetes 서비스에 자세히 알아보려면 합니다 [Kubernetes 서비스 설명서][kubernetes-services]합니다.

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
