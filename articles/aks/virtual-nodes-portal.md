---
title: AKS(Azure Kubernetes Service)에서 포털을 사용하여 가상 노드 만들기
description: Azure Portal을 통해 가상 노드를 사용하여 Pod를 실행하는 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 4b9e9aeab6ed24dd2179f853def02ad194fe1b67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61025187"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>미리 보기-만들기 및 Azure portal에서 가상 노드를 사용 하 여 Azure Kubernetes 서비스 (AKS) 클러스터를 구성 합니다.

가상 노드를 사용하면 AKS(Azure Kubernetes Service) 클러스터에서 워크로드를 신속하게 배포할 수 있습니다. 가상 노드를 사용하면 Pod를 신속하게 프로비전할 수 있으며, 실행 시간(초) 단위로 요금이 청구됩니다. 크기 조정 시나리오에서 Kubernetes 클러스터 자동 크기 조정기가 추가 Pod를 실행하는 VM 컴퓨팅 노드를 배포할 때까지 기다릴 필요가 없습니다. 이 문서에서는 가상 노드를 사용하도록 설정된 가상 네트워크 리소스 및 AKS 클러스터를 만들고 구성하는 방법을 보여줍니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 및 옵트인 합니다. 미리 보기는 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 그러나 Azure 기술 지원 서비스에서 지원 되지 않습니다 됩니다. 클러스터를 만들거나 기존 클러스터에 이러한 기능을 추가 하는 경우에 기능이 더 이상 미리 보기 상태 이며 일반 공급 (GA) 라는 될 때까지 해당 클러스터 지원 되지 않습니다.
>
> 미리 보기 기능을 사용 하 여 문제가 발생 하면 [AKS GitHub 리포지토리에서 문제를 제기] [ aks-github] 버그 제목에 미리 보기 기능의 이름입니다.

## <a name="regional-availability"></a>국가별 가용성

지역 가상 노드 배포에 대 한 지원 됩니다.

* 오스트레일리아 동부 (australiaeast)
* 미국 동부(eastus)
* 미국 중서부 (westcentralus)
* 유럽 서부(westeurope)
* 미국 서부(westus)

## <a name="known-limitations"></a>알려진 제한 사항
가상 노드 기능은 ACI의 기능 집합에 따라 크게 달라 집니다. 가상 노드에 다음 시나리오는 아직 지원 되지 않습니다.

* 끌어오기 ACR 이미지에 서비스 주체를 사용합니다. [해결 방법](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) 사용 하 여 [Kubernetes 비밀](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [가상 네트워크 제한 사항](../container-instances/container-instances-vnet.md) VNet 피어 링, Kubernetes 네트워크 정책 및 네트워크 보안 그룹을 사용 하 여 인터넷에 아웃 바운드 트래픽을 포함 합니다.
* Init 컨테이너
* [호스트 별칭](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [인수](../container-instances/container-instances-exec.md#restrictions) ACI에서 exec에 대 한
* [Daemonsets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) 가상 노드에 pod를 배포 하지 것입니다

## <a name="sign-in-to-azure"></a>Azure에 로그인

 [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **Kubernetes Service**를 선택합니다.

**기본** 페이지에서 다음 옵션을 구성합니다.

- *프로젝트 세부 정보*: Azure 구독을 선택하고 *myResourceGroup* 같은 Azure 리소스 그룹을 선택하거나 만듭니다. *myAKSCluster* 같은 **Kubernetes 클러스터 이름**을 입력합니다.
- *클러스터 세부 정보*: AKS 클러스터의 지역, Kubernetes 버전 및 DNS 이름 접두사를 선택합니다.
- *크기 조정*: AKS 노드의 VM 크기를 선택합니다. AKS 클러스터를 배포한 후에는 VM 크기를 변경할 수 **없습니다**.
    - 클러스터에 배포할 노드 수를 선택합니다. 이 문서에서는 **노드 수**를 *1*로 설정합니다. 클러스터를 배포한 후에 노드 수를 조정할 수 **있습니다**.
    - **가상 노드** 아래에서 *사용*을 선택합니다.

![AKS 클러스터를 만들고 가상 노드를 사용하도록 설정](media/virtual-nodes-portal/enable-virtual-nodes.png)

기본적으로 Azure Active Directory 서비스 사용자가 생성됩니다. 이 서비스 사용자는 클러스터 통신 및 다른 Azure 서비스와의 통합에 사용됩니다.

고급 네트워킹에 대한 클러스터도 구성됩니다. 가상 노드는 자체 Azure 가상 네트워크 서브넷을 사용하도록 구성됩니다. 이 서브넷은 AKS 클러스터 간에 Azure 리소스를 연결할 수 있는 위임된 권한을 갖습니다. 위임된 서브넷이 아직 없는 경우 Azure Portal에서는 가상 노드에 사용할 Azure 가상 네트워크 및 서브넷을 만들고 구성합니다.

**검토 + 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다.

AKS 클러스터를 만들고 사용 준비를 마칠 때까지 몇 분 정도 걸립니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. `kubectl` 클라이언트가 Azure Cloud Shell에 사전 설치됩니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 다음 예제는 *myResourceGroup*이라는 리소스 그룹에서 *myAKSCluster*라는 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력은 생성된 단일 VM 노드를 보여준 후 Linux용 가상 노드 *virtual-node-aci-linux*를 보여 줍니다.

```
$ kubectl get nodes

NAME                           STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux         Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0       Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>샘플 앱 배포

Azure Cloud Shell에서 `virtual-node.yaml`이라는 파일을 만들고 다음 YAML에 복사합니다. 노드에서 컨테이너를 예약하기 위해 [nodeSelector][node-selector] 및 [toleration][toleration]이 정의됩니다. 이러한 설정을 통해 가상 노드에서 Pod를 예약하고 기능이 성공적으로 활성화되었는지 확인할 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

[kubectl create][kubectl-apply] 명령을 사용하여 애플리케이션을 실행합니다.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 Pod 및 예약된 노드 목록을 출력합니다. `virtual-node-helloworld` Pod는 `virtual-node-linux` 노드에서 예약되었습니다.

```
$ kubectl get pods -o wide

NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod에는 가상 노드에 사용하도록 위임된 Azure 가상 네트워크 서브넷의 내부 IP 주소가 할당됩니다.

> [!NOTE]
> Azure Container Registry에 저장된 이미지를 사용하는 경우 [Kubernetes 비밀을 구성하고 사용합니다][acr-aks-secrets]. 가상 노드 미리 보기의 현재 제한 사항은 통합된 Azure AD 서비스 주체 인증을 사용할 수 없다는 것입니다. 비밀을 사용하지 않으면 가상 노드에서 예약된 Pod가 시작되지 않고 오류 `HTTP response status code 400 error code "InaccessibleImage"`가 보고됩니다.

## <a name="test-the-virtual-node-pod"></a>가상 노드 Pod 테스트

가상 노드에서 실행되는 Pod를 테스트하려면 웹 클라이언트를 사용하여 데모 애플리케이션으로 이동합니다. Pod에는 내부 IP 주소가 할당되므로 AKS 클러스터의 다른 Pod에서 이 연결을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```azurecli-interactive
kubectl run -it --rm virtual-node-test --image=debian
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```azurecli-interactive
apt-get update && apt-get install -y curl
```

이제 *http://10.241.0.4* 같은 `curl`을 사용하여 Pod 주소에 액세스합니다. 앞의 `kubectl get pods` 명령에서 본 내부 IP 주소를 입력합니다.

```azurecli-interactive
curl -L http://10.241.0.4
```

다음과 같이 축소된 예제 출력처럼 데모 애플리케이션이 표시됩니다.

```
$ curl -L 10.241.0.4

<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` 명령을 사용하여 테스트 Pod에 대한 터미널 세션을 종료합니다. 세션이 종료되면 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 노드에서 Pod를 예약하고 비공개 내부 IP 주소를 할당했습니다. 그 대신 서비스 배포를 만들고 부하 분산 장치 또는 수신 컨트롤러를 통해 Pod로 트래픽을 라우팅할 수도 있습니다. 자세한 내용은 [AKS에서 기본 수신 컨트롤러 만들기][aks-basic-ingress]를 참조하세요.

가상 노드는 AKS에서 크기 조정 솔루션의 구성 요소 중 하나입니다. 크기 조정 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes 수평 방향 Pod 자동 크기 조정기 사용][aks-hpa]
- [Kubernetes 클러스터 자동 크기 조정기 사용][aks-cluster-autoscaler]
- [가상 노드에 대해 자동 크기 조정 샘플 확인][virtual-node-autoscale]
- [Virtual Kubelet 오픈 소스 라이브러리에 대해 자세히 알아보기][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[aks-github]: https://github.com/azure/aks/issues]
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[aks-network]: ./networking-overview.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
