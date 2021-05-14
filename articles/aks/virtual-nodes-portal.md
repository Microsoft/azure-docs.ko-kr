---
title: AKS(Azure Kubernetes Service)에서 포털을 사용하여 가상 노드 만들기
description: Azure Portal을 통해 가상 노드를 사용하여 Pod를 실행하는 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: references_regions
ms.openlocfilehash: fad021dc92753013234a3b0831e76e87fa25db10
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769304"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-in-the-azure-portal"></a>Azure Portal에서 가상 노드를 사용하는 AKS(Azure Kubernetes Service) 클러스터 만들기 및 구성

이 문서에서는 Azure Portal을 활용하여 가상 노드를 사용하도록 설정된 가상 네트워크 리소스 및 AKS 클러스터를 만들고 구성하는 방법을 보여 줍니다.

> [!NOTE]
> [이 문서](virtual-nodes.md)에서는 가상 노드를 사용하여 지역 가용성 및 제한 사항에 대한 개요를 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

가상 노드는 ACI(Azure Container Instances)에서 실행되는 Pod와 AKS 클러스터 간의 네트워크 통신을 활성화합니다. 이 통신을 제공하기 위해 가상 네트워크 서브넷이 만들어지고 위임된 사용 권한이 할당됩니다. 가상 노드는 ‘고급’ 네트워킹(Azure CNI)을 사용하여 만든 AKS 클러스터에서만 작동합니다. 기본적으로 AKS 클러스터는 ‘기본’ 네트워킹(kubenet)을 사용하여 생성됩니다. 이 문서에서는 가상 네트워크 및 서브넷을 만든 다음, 고급 네트워킹을 사용하는 AKS 클러스터에 배포하는 방법을 보여 줍니다.

이전에 ACI를 사용하지 않은 경우 구독에서 서비스 공급자를 등록합니다. 다음 예제와 같이 [az provider list][az-provider-list] 명령을 사용하여 ACI 공급자 등록의 상태를 확인할 수 있습니다.

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 공급자는 다음 예제 출력에 나온 대로 *Registered* 로 보고됩니다.

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

공급자가 *NotRegistered* 로 표시되는 경우 다음 예제에 나온 대로 [az provider register][az-provider-register]를 사용하여 공급자를 등록합니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **Kubernetes Service** 를 선택합니다.

**기본** 페이지에서 다음 옵션을 구성합니다.

- *프로젝트 세부 정보*: Azure 구독을 선택하고 *myResourceGroup* 같은 Azure 리소스 그룹을 선택하거나 만듭니다. *myAKSCluster* 같은 **Kubernetes 클러스터 이름** 을 입력합니다.
- 클러스터 세부 정보: AKS 클러스터의 영역과 Kubernetes 버전을 선택합니다.
- *주 노드 풀*: AKS 노드의 VM 크기를 선택합니다. AKS 클러스터를 배포한 후에는 VM 크기를 변경할 수 **없습니다**.
     - 클러스터에 배포할 노드 수를 선택합니다. 이 문서에서는 **노드 수** 를 *1* 로 설정합니다. 클러스터를 배포한 후에 노드 수를 조정할 수 **있습니다**.

**다음: 노드 풀** 을 클릭합니다.

**노드 풀** 페이지에서 ‘가상 노드 사용 설정’을 선택합니다.

:::image type="content" source="media/virtual-nodes-portal/enable-virtual-nodes.png" alt-text="브라우저에서 Azure Portal에서 사용하도록 설정된 가상 노드를 사용하여 클러스터를 만드는 방법을 보여 줍니다. ‘가상 노드 사용 설정’ 옵션이 강조 표시됩니다.":::

기본적으로 클러스터 ID가 만들어집니다. 이 클러스터 ID는 클러스터 통신 및 다른 Azure 서비스와의 통합에 사용됩니다. 기본적으로 이 클러스터 ID는 관리 ID입니다. 자세한 내용은 [관리 ID 사용](use-managed-identity.md)을 참조하세요. 또한 서비스 주체를 클러스터 ID로 사용할 수 있습니다.

고급 네트워킹에 대한 클러스터도 구성됩니다. 가상 노드는 자체 Azure 가상 네트워크 서브넷을 사용하도록 구성됩니다. 이 서브넷은 AKS 클러스터 간에 Azure 리소스를 연결할 수 있는 위임된 권한을 갖습니다. 위임된 서브넷이 아직 없는 경우 Azure Portal에서는 가상 노드에 사용할 Azure 가상 네트워크 및 서브넷을 만들고 구성합니다.

**검토 + 만들기** 를 선택합니다. 유효성 검사가 완료되면 **만들기** 를 선택합니다.

AKS 클러스터를 만들고 사용 준비를 마칠 때까지 몇 분 정도 걸립니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. Kubernetes 클러스터를 관리하려면 Kubernetes 명령줄 클라이언트인 [kubectl][kubectl]을 사용하세요. `kubectl` 클라이언트가 Azure Cloud Shell에 사전 설치됩니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요** 를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사** 를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

[az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 Kubernetes 클러스터에 연결하도록 `kubectl`을 구성합니다. 다음 예제는 *myResourceGroup* 이라는 리소스 그룹에서 *myAKSCluster* 라는 클러스터의 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```console
kubectl get nodes
```

다음 예제 출력은 생성된 단일 VM 노드를 보여준 후 Linux용 가상 노드 *virtual-node-aci-linux* 를 보여 줍니다.

```output
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
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
```

[kubectl apply][kubectl-apply] 명령을 사용하여 애플리케이션을 실행합니다.

```azurecli-interactive
kubectl apply -f virtual-node.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 Pod 및 예약된 노드 목록을 출력합니다. `virtual-node-helloworld` Pod는 `virtual-node-linux` 노드에서 예약되었습니다.

```console
kubectl get pods -o wide
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE       IP           NODE
virtual-node-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod에는 가상 노드에 사용하도록 위임된 Azure 가상 네트워크 서브넷의 내부 IP 주소가 할당됩니다.

> [!NOTE]
> Azure Container Registry에 저장된 이미지를 사용하는 경우 [Kubernetes 비밀을 구성하고 사용][acr-aks-secrets]합니다. 가상 노드의 현재 제한 사항은 통합 Azure AD 서비스 주체 인증을 사용할 수 없다는 것입니다. 비밀을 사용하지 않으면 가상 노드에서 예약된 Pod가 시작되지 않고 오류 `HTTP response status code 400 error code "InaccessibleImage"`가 보고됩니다.

## <a name="test-the-virtual-node-pod"></a>가상 노드 Pod 테스트

가상 노드에서 실행되는 Pod를 테스트하려면 웹 클라이언트를 사용하여 데모 애플리케이션으로 이동합니다. Pod에는 내부 IP 주소가 할당되므로 AKS 클러스터의 다른 Pod에서 이 연결을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```console
kubectl run -it --rm virtual-node-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```console
apt-get update && apt-get install -y curl
```

이제 *http://10.241.0.4* 같은 `curl` 을 사용하여 Pod 주소에 액세스합니다. 앞의 `kubectl get pods` 명령에서 본 내부 IP 주소를 입력합니다.

```console
curl -L http://10.241.0.4
```

다음과 같이 축소된 예제 출력처럼 데모 애플리케이션이 표시됩니다.

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

`exit` 명령을 사용하여 테스트 Pod에 대한 터미널 세션을 종료합니다. 세션이 종료되면 Pod가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 노드에서 Pod를 예약하고 프라이빗 내부 IP 주소를 할당했습니다. 그 대신 서비스 배포를 만들고 부하 분산 장치 또는 수신 컨트롤러를 통해 Pod로 트래픽을 라우팅할 수도 있습니다. 자세한 내용은 [AKS에서 기본 수신 컨트롤러 만들기][aks-basic-ingress]를 참조하세요.

가상 노드는 AKS에서 크기 조정 솔루션의 구성 요소 중 하나입니다. 크기 조정 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes 수평 방향 Pod 자동 크기 조정기 사용][aks-hpa]
- [Kubernetes 클러스터 자동 크기 조정기 사용][aks-cluster-autoscaler]
- [가상 노드에 대한 자동 크기 조정 샘플 체크 아웃][virtual-node-autoscale]
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
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[aks-network]: ./configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az_provider_list
[az-provider-register]: /cli/azure/provider#az_provider_register
