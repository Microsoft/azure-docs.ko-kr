---
title: AKS(Azure Kubernetes Service)에서 kubenet 네트워킹 구성
description: AKS 클러스터를 기존 가상 네트워크와 서브넷에 배포하도록 AKS(Azure Kubernetes Service)에서 kubenet(기본) 네트워크를 구성하는 방법을 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: iainfou
ms.reviewer: nieberts, jomore
ms.openlocfilehash: f57c1af4c497b51f5289559737fad5ce4cf2e85b
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67358041"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 사용자 고유의 IP 주소 범위에 kubenet 네트워킹 사용

기본적으로 AKS 클러스터 사용 [kubenet][kubenet], 및 Azure 가상 네트워크 및 서브넷은 만들 수 있습니다. *kubenet*을 사용하면 노드는 Azure Virtual Network 서브넷의 IP 주소를 얻습니다. Pod는 논리적으로 다른 주소 공간에서 Azure Virtual Network 노드 서브넷에 대한 IP 주소를 받습니다. 그런 후에 NAT(Network Address Translation)는 Pod가 Azure Virtual Network의 리소스에 연결할 수 있도록 구성됩니다. 트래픽의 원본 IP 주소는 노드의 기본 IP 주소로 NAT됩니다. 이 방법을 사용하면 네트워크 공간에서 Pod가 사용하도록 예약해야 하는 IP 주소의 수가 크게 줄어듭니다.

사용 하 여 [Azure 네트워킹 인터페이스 CNI (컨테이너)][cni-networking], 모든 pod가 서브넷에서 IP 주소를 가져오고 직접 액세스할 수 있습니다. 이러한 IP 주소는 네트워크 공간에서 고유해야 하며 미리 계획되어야 합니다. 각 노드에는 지원하는 최대 Pod 수에 대한 구성 매개 변수가 있습니다. 그러면 노드당 동일한 IP 주소 수가 해당 노드에 대해 미리 예약됩니다. 이 방식을 사용할 경우 더 많은 계획이 필요하며, 애플리케이션 요구가 증가하면서 IP 주소가 고갈되거나 더 큰 서브넷에서 클러스터를 구축해야 할 수 있습니다.

이 문서에서는 *kubenet* 네트워킹을 사용하여 AKS 클러스터용 가상 네트워크를 만들고 사용하는 방법에 대해 설명합니다. 네트워크 옵션 및 고려 사항에 대 한 자세한 내용은 참조 하세요. [Kubernetes 및 AKS에 대 한 개념을 네트워크][aks-network-concepts]합니다.

> [!WARNING]
> (현재 AKS에서 미리 보기)는에서 Windows Server 노드 풀을 사용 하려면 Azure CNI 사용 해야 합니다. 네트워크 모델로 kubenet 사용 Windows Server 컨테이너에 대해 사용할 수 없는 경우

## <a name="before-you-begin"></a>시작하기 전에

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.65 필요 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>고유한 서브넷을 사용한 Kubenet 네트워킹 개요

많은 환경에서 할당된 IP 주소 범위를 사용하여 가상 네트워크 및 서브넷을 정의했을 것입니다. 이러한 가상 네트워크 리소스는 여러 서비스 및 애플리케이션을 지원하는 데 사용됩니다. AKS 클러스터 네트워크 연결을 제공하기 위해 AKS 클러스터는 *kubenet*(기본 네트워킹) 또는 Azure CNI(*고급 네트워킹*)을 사용할 수 있습니다.

*kubenet*을 사용하는 경우 해당 노드만 가상 네트워크 서브넷의 IP 주소를 수신합니다. Pod는 서로 직접 통신할 수 없습니다. 대신, 노드에서 Pod 간의 연결에 UDR(사용자 정의 라우팅) 및 IP 전달이 사용됩니다. 애플리케이션에 대해 할당된 IP 및 부하 분산 트래픽을 수신하는 서비스 뒤에 pod를 배포할 수도 있습니다. 다음 다이어그램은 AKS 노드가 pod가 아닌 가상 네트워크 서브넷의 IP 주소를 수신하는 방법을 보여 줍니다.

![AKS 클러스터를 사용하는 Kubenet 네트워크 모델](media/use-kubenet/kubenet-overview.png)

Azure는 UDR에서 최대 300개의 경로를 지원하므로 400개 노드보다 큰 AKS 클러스터를 사용할 수 없습니다. AKS와 같은 기능 [가상 노드][virtual-nodes] 또는 네트워크 정책을 사용 하 여 지원 되지 않습니다 *kubenet*합니다.

*Azure CNI*를 사용할 경우 각 pod는 IP 서브넷의 IP 주소를 받고, 다른 pod 및 서비스와 직접 통신할 수 있습니다. 클러스터는 사용자가 지정하는 IP 주소 범위만큼 클 수 있습니다. 그러나 IP 주소 범위를 미리 계획해야 하며, 모든 IP 주소는 AKS 노드가 지원할 수 있는 최대 pod 수에 따라 AKS 노드에서 사용됩니다. 와 같은 고급 네트워크 기능 및 시나리오 [가상 노드][virtual-nodes] 또는 네트워크 정책을 사용 하 여 지원 됩니다 *Azure CNI*합니다.

### <a name="ip-address-availability-and-exhaustion"></a>IP 주소 가용성 및 고갈

*Azure CNI*를 사용할 경우, 일반적인 문제는 할당된 IP 주소 범위가 너무 작아서 클러스터를 확장하거나 업그레이드할 때 노드를 더 추가할 수 없다는 것입니다. 또한 네트워크 팀이 예상된 애플리케이션 요구 사항을 지원하기 위해 충분히 큰 IP 주소 범위를 발급하지 못할 수도 있습니다.

절충안으로, *kubenet*을 사용하는 AKS 클러스터를 만들고 기존 가상 네트워크 서브넷에 연결할 수 있습니다. 이 방법을 사용하면 클러스터에서 실행될 수 있는 모든 잠재적 pod를 위해 많은 수의 IP 주소를 미리 예약할 필요가 없이, 노드가 정의된 IP 주소를 수신할 수 있습니다.

*kubenet*을 사용하면 훨씬 더 작은 IP 주소 범위를 사용할 수 있으며 대규모 클러스터 및 애플리케이션 요구를 지원할 수 있습니다. 예를 들어 */27* IP 주소 범위를 사용하더라도 20-25 노드 클러스터를 실행하여 확장 또는 업그레이드를 위한 충분한 공간을 확보할 수 있습니다. 이 클러스터 크기는 최대 *2,200-2,750*개 pod를 지원합니다(노드당 최대 pod 기본값이 110개임). 사용 하 여 구성할 수 있는 노드당 pod의 최대 수 *kubenet* AKS에서은 250입니다.

다음 기본 계산은 네트워크 모델의 차이를 비교합니다.

- **kubenet** - 간단한 */24* IP 주소 범위 하나로 클러스터의 노드를 최대 *251*개 지원할 수 있습니다(각 Azure Virtual Network 서브넷은 관리 작업을 위해 처음 3개의 IP 주소를 예약함).
  - 이 노드 수로 최대 *27,610*개의 pod를 지원할 수 있습니다(*kubenet*을 사용할 경우 노드당 최대 pod 기본값이 110개임).
- **Azure CNI** - 동일한 기본 */24* 서브넷 범위는 클러스터에서 최대 *8*개의 노드만 지원할 수 있습니다.
  - 이 노드 수로 최대 *240*개의 pod만 지원할 수 있습니다(*Azure CNI*를 사용할 경우 노드당 최대 pod 기본값이 30개임).

> [!NOTE]
> 이러한 최대값에는 업그레이드 또는 크기 조정 작업이 고려되지 않습니다. 실제로 서브넷 IP 주소 범위가 지원하는 최대 노드 수를 실행할 수는 없습니다. 업그레이드 작업의 크기 조정 동안 사용할 수 있는 일부 IP 주소를 남겨 두어야 합니다.

### <a name="virtual-network-peering-and-expressroute-connections"></a>가상 네트워크 피어링 및 ExpressRoute 연결

온-프레미스 연결을 제공 하려면 둘 다 *kubenet* 하 고 *Azure CNI* 네트워크 접근 방식을 사용할 수 있습니다 [Azure 가상 네트워크 피어 링][vnet-peering] or [ExpressRoute connections][express-route]. 중복되거나 잘못된 트래픽 라우팅를 방지하도록 IP 주소 범위를 신중하게 계획합니다. 예를 들어, 여러 온-프레미스 네트워크는 ExpressRoute 연결을 통해 보급되는 *10.0.0.0/8* 주소 범위를 사용합니다. 와 같은 해당 주소 범위를 벗어나는 Azure 가상 네트워크 서브넷에 AKS 클러스터를 만드는 것이 좋습니다 *172.16.0.0/16*합니다.

### <a name="choose-a-network-model-to-use"></a>사용할 네트워크 모델 선택

AKS 클러스터에 사용할 네트워크 플러그 인을 선택할 때는 일반적으로 유연성과 고급 구성 요구 사항 간의 균형을 고려해야 합니다. 다음 고려 사항은 각 네트워크 모델이 가장 적합한 경우를 대략적으로 알려줍니다.

다음 경우에 *kubenet*을 사용합니다.

- IP 주소 공간이 제한되어 있습니다.
- Pod 통신 대부분이 클러스터 내에 있습니다.
- 가상 노드 또는 네트워크 정책과 같은 고급 기능이 필요하지 않습니다.

다음 경우에 *Azure CNI*를 사용합니다.

- 사용 가능한 IP 주소 공간이 있습니다.
- Pod 통신 대부분이 클러스터 외부의 리소스를 대상으로 진행됩니다.
- UDR을 관리하지 않으려고 합니다.
- 가상 노드 또는 네트워크 정책과 같은 고급 기능이 필요합니다.

네트워크 모델을 사용 하 여 결정 하는 데 대 한 자세한 내용은 참조 하세요. [모델 및 해당 지원 범위 비교][network-comparisons]합니다.

> [!NOTE]
> Kuberouter 수 있도록 kubenet를 사용 하는 경우 네트워크 정책을 사용 하도록 설정 하 고 AKS 클러스터에서 daemonset으로 설치할 수 있습니다. 주의 하십시오 kube-라우터는 아직 베타 버전 및 지원 되지 않습니다 프로젝트에 대해 Microsoft에서 제공 됩니다.

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

사용 하 여 시작 하려면 *kubenet* 및 사용자 고유의 가상 네트워크 서브넷을 먼저 사용 하 여 리소스 그룹을 만들 합니다 [az 그룹 만들기][az-group-create] 명령입니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

기존 가상 네트워크 및 서브넷을 사용 하 여 없다면 만들기를 사용 하 여 이러한 네트워크 리소스를 [az 네트워크 vnet 만들기][az-network-vnet-create] 명령입니다. 다음 예제에서는 가상 네트워크의 이름은 *myVnet* 의 주소 접두사를 사용 하 여 *192.168.0.0/16*합니다. 서브넷이 명명 된 만들어집니다 *myAKSSubnet* 주소 접두사를 사용 하 여 *192.168.1.0/24*합니다.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>서비스 주체 만들기 및 사용 권한 할당

AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 Azure Active Directory 서비스 사용자를 사용합니다. 서비스 주체는 AKS 노드가 사용하는 가상 네트워크와 서브넷을 관리하기 위한 권한이 필요합니다. 서비스 주체를 만들려면 사용 합니다 [az ad sp 만들기 rbac 용][az-ad-sp-create-for-rbac] 명령:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

다음 예제 출력에서는 서비스 주체의 애플리케이션 ID 및 암호를 보여 줍니다. 이러한 값은 서비스 주체에 역할을 할당한 다음, AKS 클러스터를 만드는 추가 단계에서 사용됩니다.

```console
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

나머지 단계에서 올바른 위임이 할당 하려면 사용 합니다 [az network vnet show][az-network-vnet-show] and [az network vnet subnet show][az-network-vnet-subnet-show] 필수 리소스 Id를 가져오려면 명령입니다. 이러한 리소스 ID는 변수로 저장되고 나머지 단계에서 참조됩니다.

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

이제 AKS 클러스터의 서비스 주체를 할당할 *참가자* 사용 하 여 가상 네트워크에 대 한 권한을 합니다 [az 역할 할당 만들기][az-role-assignment-create] 명령입니다. 직접 제공할  *\<appId >* 서비스 주체를 만들려면 이전 명령의 출력에 표시 된 대로:

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role Contributor
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>가상 네트워크에서 AKS 클러스터 만들기

지금까지 가상 네트워크 및 서브넷을 만들었으며, 서비스 주체가 해당 네트워크 리소스를 사용할 수 있는 권한을 만들고 할당했습니다. 이제 가상 네트워크 및 서브넷에 사용 하 여 AKS 클러스터를 만들 합니다 [az aks 만들기][az-aks-create] 명령입니다. 고유한 서비스 주체를 정의  *\<appId >* 하 고  *\<암호 >* 서비스 주체를 만들려면 이전 명령의 출력에 표시 된 것 처럼 합니다.

또한 다음 IP 주소 범위도 클러스터를 만드는 과정 중에 정의됩니다.

* *--service-cidr*을 사용하여 AKS 클러스터의 내부 서비스에 IP 주소를 할당합니다. 이 IP 주소 범위는 네트워크 환경의 다른 위치에서 사용되지 않는 주소 공간이어야 합니다. 이 범위에 연결 또는 Express Route 또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 네트워크에 연결 하려고 하는 경우 모든 온-프레미스 네트워크 범위를 포함 합니다.

* *--dns-service-ip* 주소는 서비스 IP 주소 범위의 *.10* 주소여야 합니다.

* *--pod-cidr*은 네트워크 환경의 다른 위치에서 사용되지 않는 큰 주소 공간이어야 합니다. 이 범위에 연결 또는 Express Route 또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 네트워크에 연결 하려고 하는 경우 모든 온-프레미스 네트워크 범위를 포함 합니다.
    * 이 주소 범위는 확장할 예정인 노드 수를 수용할만큼 충분히 커야 합니다. 추가 노드를 위해 더 많은 주소가 필요하더라도 클러스터를 배포한 후에는 이 주소 범위를 변경할 수 없습니다.
    * Pod IP 주소 범위를 사용하여 */24* 주소 공간을 클러스터의 각 노드에 할당합니다. 다음 예제에서는 *-pod cidr* 의 *10.244.0.0/16* 첫 번째 노드를 할당 *10.244.0.0/24*를 두 번째 노드 *10.244.1.0/24*, 및 세 번째 노드 *10.244.2.0/24*합니다.
    * 클러스터가 확장 또는 업그레이드되면 Azure 플랫폼은 새로운 각 노드에 pod IP 주소 범위를 계속 할당합니다.
    
* 합니다 *-docker 브리지 주소* AKS 노드의 기본 관리 플랫폼을 사용 하 여 통신할 수 있습니다. 이 IP 주소는 클러스터의 가상 네트워크 IP 주소 범위 내에 속하지 않아야 하고 네트워크에서 사용 중인 다른 주소 범위와 겹쳐서는 안 됩니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

AKS 클러스터를 만들면 네트워크 보안 그룹 및 경로 테이블이 생성됩니다. 이러한 네트워크 리소스는 AKS 제어 평면으로 관리 됩니다. 네트워크 보안 그룹 노드에서 가상 Nic와 사용 하 여 자동 연결 됩니다. 경로 테이블은 자동으로 가상 네트워크 서브넷에 연결 합니다. 네트워크 보안 그룹 규칙 및 경로 테이블은 자동으로 업데이트를 만들고 서비스를 공개 합니다.

## <a name="next-steps"></a>다음 단계

기존 가상 네트워크 서브넷에 배포된 AKS 클러스터를 정상적으로 사용할 수 있습니다. 시작 [Azure 개발 공간을 사용 하 여 앱을 빌드하][dev-spaces] or [using Draft][use-draft], 또는 [Helm을 사용 하 여 앱을 배포할][사용 하 여 helm]합니다.

<!-- LINKS - External -->
[dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-network-concepts]: concepts-network.md
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[use-helm]: kubernetes-helm.md
[use-draft]: kubernetes-draft.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
