---
title: AKS(Azure Kubernetes Service)에서 Azure CLI를 사용하여 가상 노드 만들기
description: Azure CLI를 통해 가상 노드를 사용하여 Pod를 실행하는 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.topic: conceptual
ms.service: container-service
ms.date: 12/03/2018
ms.author: iainfou
ms.openlocfilehash: 38b2654c8f3e8d302a66cac335913583bd4426ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024557"
---
# <a name="preview---create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>미리 보기-만들기 및 Azure CLI를 사용 하 여 가상 노드를 사용 하 여 Azure Kubernetes 서비스 (AKS) 클러스터를 구성 합니다.

AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 워크로드 크기를 신속하게 조정하려면 가상 노드를 사용할 수 있습니다. 가상 노드를 사용하면 Pod를 신속하게 프로비전할 수 있으며, 실행 시간(초) 단위로 요금이 청구됩니다. Kubernetes 클러스터 자동 크기 조정기가 추가 Pod를 실행하는 VM 컴퓨팅 노드를 배포할 때까지 기다릴 필요가 없습니다. 이 문서에서는 가상 네트워크 리소스 및 AKS 클러스터를 만들고 구성한 후 가상 노드를 사용하도록 설정하는 방법을 보여 줍니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 및 옵트인 합니다. 미리 보기는 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 그러나 Azure 기술 지원 서비스에서 지원 되지 않습니다 됩니다. 클러스터를 만들거나 기존 클러스터에 이러한 기능을 추가 하는 경우에 기능이 더 이상 미리 보기 상태 이며 일반 공급 (GA) 라는 될 때까지 해당 클러스터 지원 되지 않습니다.
>
> 미리 보기 기능을 사용 하 여 문제가 발생 하면 [AKS GitHub 리포지토리에서 문제를 제기] [ aks-github] 버그 제목에 미리 보기 기능의 이름입니다.

## <a name="before-you-begin"></a>시작하기 전에

가상 노드는 ACI에서 실행되는 Pod와 AKS 클러스터 간의 네트워크 통신을 활성화합니다. 이 통신을 제공하기 위해 가상 네트워크 서브넷이 만들어지고 위임된 사용 권한이 할당됩니다. 가상 노드는 *고급* 네트워킹을 사용하여 만든 AKS 클러스터에만 작동합니다. 기본적으로 AKS 클러스터는 *기본* 네트워킹을 사용하여 만듭니다. 이 문서에서는 가상 네트워크 및 서브넷을 만든 다음, 고급 네트워킹을 사용하는 AKS 클러스터에 배포하는 방법을 보여 줍니다.

이전에 ACI를 사용하지 않은 경우 구독에서 서비스 공급자를 등록합니다. 다음 예제와 같이 [az provider list][az-provider-list] 명령을 사용하여 ACI 공급자 등록의 상태를 확인할 수 있습니다.

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 공급자는 다음 예제 출력에 나온 대로 *Registered*로 보고됩니다.

```
Namespace                    RegistrationState
---------------------------  -------------------
Microsoft.ContainerInstance  Registered
```

공급자가 *NotRegistered*로 표시되는 경우 다음 예제에 나온 대로 [az provider register][az-provider-register]를 사용하여 공급자를 등록합니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

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

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다.

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택합니다. 또한 [https://shell.azure.com/bash](https://shell.azure.com/bash)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

이 문서에 따라 CLI를 로컬에서 설치하여 사용하려면 Azure CLI 버전 2.0.49 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure 리소스 그룹은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *westus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

[az network vnet create][az-network-vnet-create] 명령을 사용하여 가상 네트워크를 만듭니다. 다음 예제는 주소 접두사 *10.0.0.0/8*과 *myAKSSubnet*라는 서브넷을 사용하여 *myVnet*이라는 가상 네트워크를 만듭니다. 기본적으로 이 서브넷의 주소 접두사는 *10.240.0.0/16*입니다.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

이제 [az network vnet subnet create][az-network-vnet-subnet-create] 명령을 사용하여 가상 노드에 대해 추가 서브넷을 만듭니다. 다음 예제에서는 주소 접두사 *10.241.0.0/16*을 사용하여 *myVirtualNodeSubnet*이라는 서브넷을 만듭니다.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>서비스 주체 만들기

AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 Azure Active Directory 서비스 사용자를 사용합니다. Azure CLI 또는 포털에서 이 서비스 주체를 자동으로 생성하거나 추가 사용 권한을 미리 만고 할당할 수 있습니다.

[az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령을 사용하여 서비스 주체를 만듭니다. `--skip-assignment` 매개 변수는 다른 추가 사용 권한이 할당되지 않도록 제한합니다.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

다음 예제와 유사하게 출력됩니다.

```
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

*appId* 및 *암호*를 기록해 둡니다. 다음 단계에서 이러한 값을 사용합니다.

## <a name="assign-permissions-to-the-virtual-network"></a>가상 네트워크에 사용 권한 할당

클러스터가 가상 네트워크를 사용하고 관리하도록 하려면 AKS 서비스 주체에 네트워크 리소스를 사용할 수 있는 올바른 권한을 부여해야 합니다.

먼저, [az network vnet show][az-network-vnet-show]를 사용하여 가상 네트워크 리소스 ID를 가져옵니다.

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

가상 네트워크를 사용하도록 AKS 클러스터에 대한 올바른 액세스 권한을 부여하려면 [az role assignment create][az-role-assignment-create] 명령을 사용하여 역할 할당을 만듭니다. `<appId`> 및 `<vnetId>`를 이전 두 단계에서 수집한 값으로 바꿉니다.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

이전 단계에서 만든 AKS 서브넷에 AKS 클러스터를 배포합니다. [az network vnet subnet show][az-network-vnet-subnet-show]를 사용하여 이 서브넷의 ID를 가져옵니다.

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

[az aks create][az-aks-create] 명령을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 *myAKSCluster*라는 클러스터를 만듭니다. `<subnetId>`를 이전 단계에서 가져온 ID로 바꾼 후 `<appId>` 및 `<password>`를 다음으로 바꿉니다. 

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

몇 분 후 명령이 완료되고 클러스터에 대해 JSON 형식 정보가 반환됩니다.

## <a name="enable-virtual-nodes-addon"></a>가상 노드 추가 기능을 사용 하도록 설정

가상 노드를 사용하도록 설정하려면 이제 [az aks enable-addons][az-aks-enable-addons] 명령을 사용합니다. 다음 예제에서는 이전 단계에서 만든 *myVirtualNodeSubnet*이라는 서브넷을 사용합니다.

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```
> [!NOTE]
> 해당 CLI 확장을 설치 해야 가상 노드를 찾을 수에 대 한 오류가 표시 하는 경우 
> ```azurecli-interactive
> az extension add --source https://aksvnodeextension.blob.core.windows.net/aks-virtual-node/aks_virtual_node-0.2.0-py2.py3-none-any.whl
> ```

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 단계에서는 자격 증명을 다운로드하고 Kubernetes CLI가 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```console
kubectl get nodes
```

다음 예제 출력은 생성된 단일 VM 노드를 보여준 후 Linux용 가상 노드 *virtual-node-aci-linux*를 보여 줍니다.

```
$ kubectl get nodes

NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>샘플 앱 배포

파일 `virtual-node.yaml`을 만들고 다음 YAML에 복사합니다. 노드에서 컨테이너를 예약하기 위해 [nodeSelector][node-selector] 및 [toleration][toleration]이 정의됩니다.

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

```console
kubectl apply -f virtual-node.yaml
```

`-o wide` 인수와 [kubectl get pods][kubectl-get] 명령을 사용하여 Pod 및 예약된 노드 목록을 출력합니다. `aci-helloworld` Pod는 `virtual-node-aci-linux` 노드에서 예약되었습니다.

```
$ kubectl get pods -o wide

NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod에는 가상 노드에 사용하도록 위임된 Azure 가상 네트워크 서브넷의 내부 IP 주소가 할당됩니다.

> [!NOTE]
> Azure Container Registry에 저장된 이미지를 사용하는 경우 [Kubernetes 비밀을 구성하고 사용합니다][acr-aks-secrets]. 가상 노드 미리 보기의 현재 제한 사항은 통합된 Azure AD 서비스 주체 인증을 사용할 수 없다는 것입니다. 비밀을 사용하지 않으면 가상 노드에서 예약된 Pod가 시작되지 않고 오류 `HTTP response status code 400 error code "InaccessibleImage"`가 보고됩니다.

## <a name="test-the-virtual-node-pod"></a>가상 노드 Pod 테스트

가상 노드에서 실행되는 Pod를 테스트하려면 웹 클라이언트를 사용하여 데모 애플리케이션으로 이동합니다. Pod에는 내부 IP 주소가 할당되므로 AKS 클러스터의 다른 Pod에서 이 연결을 신속하게 테스트할 수 있습니다. 테스트 Pod를 만들고 여기에 터미널 세션을 연결합니다.

```console
kubectl run -it --rm virtual-node-test --image=debian
```

`apt-get`을 사용하여 Pod에 `curl`을 설치합니다.

```console
apt-get update && apt-get install -y curl
```

이제 *http://10.241.0.4* 같은 `curl`을 사용하여 Pod 주소에 액세스합니다. 앞의 `kubectl get pods` 명령에서 본 내부 IP 주소를 입력합니다.

```console
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

## <a name="remove-virtual-nodes"></a>가상 노드 제거

가상 노드를 더 이상 사용하지 않으려면 [az aks disable-addons][az aks disable-addons] 명령을 사용하여 사용하지 않도록 설정할 수 있습니다. 다음 예제에서는 Linux 가상 노드를 사용하지 않도록 설정합니다.

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

이제 가상 네트워크 리소스 및 리소스 그룹을 제거합니다.

```azurecli-interactive
# Change the name of your resource group and network resources as needed
RES_GROUP=myResourceGroup

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Get the service association link (SAL) ID
SAL_ID=$(az network vnet subnet show --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --query id --output tsv)/providers/Microsoft.ContainerInstance/serviceAssociationLinks/default

# Delete the default SAL ID for the subnet
az resource delete --ids $SAL_ID --api-version 2018-07-01

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name myVnet --name myVirtualNodeSubnet --remove delegations 0
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 노드에서 Pod를 예약하고 비공개 내부 IP 주소를 할당했습니다. 그 대신 서비스 배포를 만들고 부하 분산 장치 또는 수신 컨트롤러를 통해 Pod로 트래픽을 라우팅할 수도 있습니다. 자세한 내용은 [AKS에서 기본 수신 컨트롤러 만들기][aks-basic-ingress]를 참조하세요.

가상 노드는 종종 AKS에서 크기 조정 솔루션의 구성 요소 중 하나입니다. 크기 조정 솔루션에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes 수평 방향 Pod 자동 크기 조정기 사용][aks-hpa]
- [Kubernetes 클러스터 자동 크기 조정기 사용][aks-cluster-autoscaler]
- [가상 노드에 대해 자동 크기 조정 샘플 확인][virtual-node-autoscale]
- [Virtual Kubelet 오픈 소스 라이브러리에 대해 자세히 알아보기][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[acr-aks-secrets]: ../container-registry/container-registry-auth-aks.md#access-with-kubernetes-secret
