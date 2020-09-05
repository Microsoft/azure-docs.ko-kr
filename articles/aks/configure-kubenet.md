---
title: AKS(Azure Kubernetes Service)에서 kubenet 네트워킹 구성
description: AKS 클러스터를 기존 가상 네트워크와 서브넷에 배포하도록 AKS(Azure Kubernetes Service)에서 kubenet(기본) 네트워크를 구성하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 06/02/2020
ms.reviewer: nieberts, jomore
ms.openlocfilehash: f9bc0cd229888d952821509ced6cc5410000ee52
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078727"
---
# <a name="use-kubenet-networking-with-your-own-ip-address-ranges-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 사용자 고유의 IP 주소 범위에 kubenet 네트워킹 사용

기본적으로 AKS 클러스터는 [kubenet][kubenet]을 사용합니다. 그러면 Azure Virtual Network 및 서브넷이 만들어집니다. *kubenet*을 사용하면 노드는 Azure Virtual Network 서브넷의 IP 주소를 얻습니다. Pod는 논리적으로 다른 주소 공간에서 노드의 Azure 가상 네트워크 서브넷으로 IP 주소를 수신합니다. 그러면 Pod가 Azure 가상 네트워크의 리소스에 연결할 수 있도록 NAT(Network Address Translation)가 구성됩니다. 트래픽의 원본 IP 주소는 노드의 기본 IP 주소로 NAT됩니다. 이 방법을 사용하면 네트워크 공간에서 Pod가 사용하도록 예약해야 하는 IP 주소의 수가 크게 줄어듭니다.

[Azure CNI(컨테이너 네트워킹 인터페이스)][cni-networking]를 사용하면 모든 pod가 서브넷에서 IP 주소를 가져오고 직접 액세스할 수 있습니다. 이러한 IP 주소는 네트워크 공간에서 고유해야 하며 미리 계획되어야 합니다. 각 노드에는 지원하는 최대 Pod 수에 대한 구성 매개 변수가 있습니다. 그러면 노드당 동일한 IP 주소 수가 해당 노드에 대해 미리 예약됩니다. 이 방식을 사용할 경우 더 많은 계획이 필요하며, 애플리케이션 요구가 증가하면서 IP 주소가 고갈되거나 더 큰 서브넷에서 클러스터를 구축해야 할 수 있습니다. 클러스터를 만들 때 또는 새 노드 풀을 만들 때 노드에 배포할 수 있는 최대 pod 수를 구성할 수 있습니다. 새 노드 풀을 만들 때 maxPods를 지정 하지 않으면 kubenet의 기본값 110이 표시 됩니다.

이 문서에서는 *kubenet* 네트워킹을 사용하여 AKS 클러스터용 가상 네트워크를 만들고 사용하는 방법에 대해 설명합니다. 네트워킹 옵션 및 고려 사항에 대한 자세한 내용은 [Kubernetes 및 AKS에 대한 네트워크 개념][aks-network-concepts]을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* AKS 클러스터에 대한 가상 네트워크는 아웃바운드 인터넷 연결을 허용해야 합니다.
* 동일한 서브넷에 둘 이상의 AKS 클러스터를 만들지 마세요.
* AKS 클러스터 `169.254.0.0/16` `172.30.0.0/16` `172.31.0.0/16` `192.0.2.0/24` 는 Kubernetes 서비스 주소 범위에 대해,, 또는를 사용할 수 없습니다.
* AKS 클러스터에서 사용 하는 서비스 주체는 가상 네트워크 내의 서브넷에 최소한 [네트워크 참가자](../role-based-access-control/built-in-roles.md#network-contributor) 역할이 있어야 합니다. 또한 서비스 주체를 만들고 사용 권한을 할당 하려면 구독 소유자와 같은 적절 한 사용 권한이 있어야 합니다. 기본 제공 네트워크 참가자 역할을 사용하는 대신 [사용자 지정 역할](../role-based-access-control/custom-roles.md)을 정의하려는 경우 다음 권한이 필요합니다.
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

> [!WARNING]
> Windows Server 노드 풀을 사용 하려면 Azure CNI를 사용 해야 합니다. 네트워크 모델로 kubenet를 사용 하는 것은 Windows Server 컨테이너에 사용할 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.65 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="overview-of-kubenet-networking-with-your-own-subnet"></a>고유한 서브넷을 사용한 Kubenet 네트워킹 개요

많은 환경에서 할당된 IP 주소 범위를 사용하여 가상 네트워크 및 서브넷을 정의했을 것입니다. 이러한 가상 네트워크 리소스는 여러 서비스 및 애플리케이션을 지원하는 데 사용됩니다. AKS 클러스터 네트워크 연결을 제공하기 위해 AKS 클러스터는 *kubenet*(기본 네트워킹) 또는 Azure CNI(*고급 네트워킹*)을 사용할 수 있습니다.

*kubenet*을 사용하는 경우 해당 노드만 가상 네트워크 서브넷의 IP 주소를 수신합니다. Pod는 서로 직접 통신할 수 없습니다. 대신, 노드에서 Pod 간의 연결에 UDR(사용자 정의 라우팅) 및 IP 전달이 사용됩니다. 기본적으로 UDRs 및 IP 전달 구성은 AKS 서비스에 의해 생성 되 고 유지 관리 되지만 [사용자 지정 경로 관리를 위해 자체 경로 테이블을 가져오는][byo-subnet-route-table]옵션을 사용할 수 있습니다. 애플리케이션에 대해 할당된 IP 및 부하 분산 트래픽을 수신하는 서비스 뒤에 pod를 배포할 수도 있습니다. 다음 다이어그램은 AKS 노드가 pod가 아닌 가상 네트워크 서브넷의 IP 주소를 수신하는 방법을 보여 줍니다.

![AKS 클러스터를 사용하는 Kubenet 네트워크 모델](media/use-kubenet/kubenet-overview.png)

Azure는 UDR에서 최대 300개의 경로를 지원하므로 400개 노드보다 큰 AKS 클러스터를 사용할 수 없습니다. AKS [가상 노드][virtual-nodes] 및 Azure 네트워크 정책은 *kubenet*에서 지원 되지 않습니다.  [Calico Network 정책은][calico-network-policies]kubenet에서 지원 되므로 사용할 수 있습니다.

*Azure CNI*를 사용할 경우 각 pod는 IP 서브넷의 IP 주소를 받고, 다른 pod 및 서비스와 직접 통신할 수 있습니다. 클러스터는 사용자가 지정하는 IP 주소 범위만큼 클 수 있습니다. 그러나 IP 주소 범위를 미리 계획해야 하며, 모든 IP 주소는 AKS 노드가 지원할 수 있는 최대 pod 수에 따라 AKS 노드에서 사용됩니다. [가상 노드][virtual-nodes] 또는 네트워크 정책 (Azure 또는 calico)과 같은 고급 네트워크 기능 및 시나리오는 *azure cni*에서 지원 됩니다.

### <a name="limitations--considerations-for-kubenet"></a>Kubenet에 대 한 제한 사항 & 고려 사항

* Kubenet 디자인에서 추가 홉이 필요 하며,이는 pod 통신에 약간의 대기 시간을 추가 합니다.
* 경로 테이블 및 사용자 정의 경로는 작업에 복잡성을 추가 하는 kubenet을 사용 하는 데 필요 합니다.
* Kubenet 디자인으로 인해 kubenet에 대 한 직접 pod 주소 지정은 지원 되지 않습니다.
* Azure CNI 클러스터와 달리 여러 kubenet 클러스터는 서브넷을 공유할 수 없습니다.
* **Kubenet에서 지원 되지 않는** 기능은 다음과 같습니다.
   * [Azure 네트워크 정책은](use-network-policies.md#create-an-aks-cluster-and-enable-network-policy)kubenet에서 지원 됩니다.
   * [Windows 노드 풀](windows-node-limitations.md)
   * [가상 노드 추가 기능](virtual-nodes-portal.md#known-limitations)

### <a name="ip-address-availability-and-exhaustion"></a>IP 주소 가용성 및 고갈

*Azure CNI*를 사용할 경우, 일반적인 문제는 할당된 IP 주소 범위가 너무 작아서 클러스터를 확장하거나 업그레이드할 때 노드를 더 추가할 수 없다는 것입니다. 또한 네트워크 팀이 예상된 애플리케이션 요구 사항을 지원하기 위해 충분히 큰 IP 주소 범위를 발급하지 못할 수도 있습니다.

절충안으로, *kubenet*을 사용하는 AKS 클러스터를 만들고 기존 가상 네트워크 서브넷에 연결할 수 있습니다. 이 방법을 사용하면 클러스터에서 실행될 수 있는 모든 잠재적 pod를 위해 많은 수의 IP 주소를 미리 예약할 필요가 없이, 노드가 정의된 IP 주소를 수신할 수 있습니다.

*kubenet*을 사용하면 훨씬 더 작은 IP 주소 범위를 사용할 수 있으며 대규모 클러스터 및 애플리케이션 요구를 지원할 수 있습니다. 예를 들어 서브넷에서 */27* IP 주소 범위를 사용 하는 경우 크기를 조정 하거나 업그레이드할 공간이 충분 한 20-25 노드 클러스터를 실행할 수 있습니다. 이 클러스터 크기는 최대 *2,200-2,750*개 pod를 지원합니다(노드당 최대 pod 기본값이 110개임). AKS에서 *kubenet* 를 사용 하 여 구성할 수 있는 노드당 최대 pod 수는 110입니다.

다음 기본 계산은 네트워크 모델의 차이를 비교합니다.

- **kubenet** - 간단한 */24* IP 주소 범위 하나로 클러스터의 노드를 최대 *251*개 지원할 수 있습니다(각 Azure Virtual Network 서브넷은 관리 작업을 위해 처음 3개의 IP 주소를 예약함).
  - 이 노드 수로 최대 *27,610*개의 pod를 지원할 수 있습니다(*kubenet*을 사용할 경우 노드당 최대 pod 기본값이 110개임).
- **Azure CNI** - 동일한 기본 */24* 서브넷 범위는 클러스터에서 최대 *8*개의 노드만 지원할 수 있습니다.
  - 이 노드 수로 최대 *240*개의 pod만 지원할 수 있습니다(*Azure CNI*를 사용할 경우 노드당 최대 pod 기본값이 30개임).

> [!NOTE]
> 이러한 최대값에는 업그레이드 또는 크기 조정 작업이 고려되지 않습니다. 실제로 서브넷 IP 주소 범위가 지원하는 최대 노드 수를 실행할 수는 없습니다. 업그레이드 작업의 크기 조정 동안 사용할 수 있는 일부 IP 주소를 남겨 두어야 합니다.

### <a name="virtual-network-peering-and-expressroute-connections"></a>가상 네트워크 피어링 및 ExpressRoute 연결

온-프레미스 연결을 제공하려면 *kubenet* 및 *Azure-CNI* 네트워크 접근 방식 둘 다 [Azure Virtual Network 피어링][vnet-peering] 또는 [ExpressRoute 연결][express-route]을 사용할 수 있습니다. 중복되거나 잘못된 트래픽 라우팅를 방지하도록 IP 주소 범위를 신중하게 계획합니다. 예를 들어, 여러 온-프레미스 네트워크는 ExpressRoute 연결을 통해 보급되는 *10.0.0.0/8* 주소 범위를 사용합니다. 이 주소 범위 (예: *172.16.0.0/16*) 외부에 있는 Azure 가상 네트워크 서브넷에 AKS 클러스터를 만드는 것이 좋습니다.

### <a name="choose-a-network-model-to-use"></a>사용할 네트워크 모델 선택

AKS 클러스터에 사용할 네트워크 플러그 인을 선택할 때는 일반적으로 유연성과 고급 구성 요구 사항 간의 균형을 고려해야 합니다. 다음 고려 사항은 각 네트워크 모델이 가장 적합한 경우를 대략적으로 알려줍니다.

다음 경우에 *kubenet*을 사용합니다.

- IP 주소 공간이 제한되어 있습니다.
- Pod 통신 대부분이 클러스터 내에 있습니다.
- 가상 노드 또는 Azure 네트워크 정책과 같은 고급 AKS 기능이 필요 하지 않습니다.  [Calico 네트워크 정책을][calico-network-policies]사용 합니다.

다음 경우에 *Azure CNI*를 사용합니다.

- 사용 가능한 IP 주소 공간이 있습니다.
- Pod 통신 대부분이 클러스터 외부의 리소스를 대상으로 진행됩니다.
- Pod 연결에 대 한 사용자 정의 경로를 관리 하지 않으려고 합니다.
- 가상 노드 또는 Azure 네트워크 정책과 같은 AKS 고급 기능이 필요 합니다.  [Calico 네트워크 정책을][calico-network-policies]사용 합니다.

사용할 네트워크 모델을 결정 하는 데 도움이 되는 자세한 내용은 [네트워크 모델 및 해당 지원 범위 비교][network-comparisons]를 참조 하세요.

## <a name="create-a-virtual-network-and-subnet"></a>가상 네트워크 및 서브넷 만들기

*kubenet*과 사용자 고유의 가상 네트워크 서브넷을 사용하려면 먼저 [az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

사용할 기존 가상 네트워크 및 서브넷이 없으면 [az network vnet create][az-network-vnet-create] 명령을 사용하여 이러한 네트워크 리소스를 만듭니다. 다음 예제에서 가상 네트워크는 주소 접두사가 *192.168.0.0/16*인 *myvnet* 으로 이름이 지정 됩니다. 서브넷은 주소 접두사 *192.168.1.0/24*를 사용 하 여 *myAKSSubnet* 이라는 이름으로 생성 됩니다.

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myAKSVnet \
    --address-prefixes 192.168.0.0/16 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-a-service-principal-and-assign-permissions"></a>서비스 주체 만들기 및 사용 권한 할당

AKS 클러스터가 다른 Azure 리소스와 상호 작용할 수 있도록 Azure Active Directory 서비스 사용자를 사용합니다. 서비스 주체는 AKS 노드가 사용하는 가상 네트워크와 서브넷을 관리하기 위한 권한이 필요합니다. 서비스 주체를 만들려면 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 명령을 사용합니다.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

다음 예제 출력에서는 서비스 주체의 애플리케이션 ID 및 암호를 보여 줍니다. 이러한 값은 서비스 주체에 역할을 할당한 다음, AKS 클러스터를 만드는 추가 단계에서 사용됩니다.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

```output
{
  "appId": "476b3636-5eda-4c0e-9751-849e70b5cfad",
  "displayName": "azure-cli-2019-01-09-22-29-24",
  "name": "http://azure-cli-2019-01-09-22-29-24",
  "password": "a1024cd7-af7b-469f-8fd7-b293ecbb174e",
  "tenant": "72f998bf-85f1-41cf-92ab-2e7cd014db46"
}
```

나머지 단계에서 올바른 위임이 할당하려면 [az network vnet show][az-network-vnet-show] 및 [az network vnet subnet show][az-network-vnet-subnet-show] 명령을 사용하여 필요한 리소스 ID를 가져옵니다. 이러한 리소스 ID는 변수로 저장되고 나머지 단계에서 참조됩니다.

```azurecli-interactive
VNET_ID=$(az network vnet show --resource-group myResourceGroup --name myAKSVnet --query id -o tsv)
SUBNET_ID=$(az network vnet subnet show --resource-group myResourceGroup --vnet-name myAKSVnet --name myAKSSubnet --query id -o tsv)
```

이제 [az role assign create][az-role-assignment-create] 명령을 사용 하 여 가상 네트워크에 대 한 AKS Cluster *network 참가자* 권한에 대 한 서비스 주체를 할당 합니다. *\<appId>* 이전 명령의 출력에 표시 된 것 처럼 사용자 고유의를 제공 하 여 서비스 주체를 만듭니다.

```azurecli-interactive
az role assignment create --assignee <appId> --scope $VNET_ID --role "Network Contributor"
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>가상 네트워크에서 AKS 클러스터 만들기

지금까지 가상 네트워크 및 서브넷을 만들었으며, 서비스 주체가 해당 네트워크 리소스를 사용할 수 있는 권한을 만들고 할당했습니다. 이제 [az aks create][az-aks-create] 명령을 사용하여 가상 네트워크 및 서브넷에 AKS 클러스터를 만듭니다. *\<appId>* *\<password>* 이전 명령의 출력에 표시 된 것 처럼 사용자 고유의 서비스 주체를 정의 하 여 서비스 주체를 만듭니다.

또한 다음 IP 주소 범위도 클러스터를 만드는 과정 중에 정의됩니다.

* *--service-cidr*을 사용하여 AKS 클러스터의 내부 서비스에 IP 주소를 할당합니다. 이 IP 주소 범위는 네트워크 환경의 다른 위치에서 사용되지 않는 주소 공간이어야 합니다. 이 범위는 Express 경로 또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 네트워크에 연결 하거나 연결 하려는 경우 온-프레미스 네트워크 범위를 포함 합니다.

* *--dns-service-ip* 주소는 서비스 IP 주소 범위의 *.10* 주소여야 합니다.

* *--pod-cidr*은 네트워크 환경의 다른 위치에서 사용되지 않는 큰 주소 공간이어야 합니다. 이 범위는 Express 경로 또는 사이트 간 VPN 연결을 사용 하 여 Azure 가상 네트워크에 연결 하거나 연결 하려는 경우 온-프레미스 네트워크 범위를 포함 합니다.
    * 이 주소 범위는 확장할 예정인 노드 수를 수용할만큼 충분히 커야 합니다. 추가 노드를 위해 더 많은 주소가 필요하더라도 클러스터를 배포한 후에는 이 주소 범위를 변경할 수 없습니다.
    * Pod IP 주소 범위를 사용하여 */24* 주소 공간을 클러스터의 각 노드에 할당합니다. 다음 예제에서 *10.244.0.0/16* 의 *--pod-cidr* 은 첫 번째 노드 *10.244.0.0/24*, 두 번째 노드 *10.244.1.0/24*및 세 번째 노드 *10.244.2.0/24*를 할당 합니다.
    * 클러스터가 확장 또는 업그레이드되면 Azure 플랫폼은 새로운 각 노드에 pod IP 주소 범위를 계속 할당합니다.
    
* *--Docker-브리지 주소* 를 사용 하면 AKS 노드가 기본 관리 플랫폼과 통신할 수 있습니다. 이 IP 주소는 클러스터의 가상 네트워크 IP 주소 범위 내에 속하지 않아야 하고 네트워크에서 사용 중인 다른 주소 범위와 겹쳐서는 안 됩니다.

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

> [!Note]
> [Calico 네트워크 정책을][calico-network-policies] 포함 하도록 AKS 클러스터를 설정 하려는 경우 다음 명령을 사용할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 3 \
    --network-plugin kubenet --network-policy calico \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --pod-cidr 10.244.0.0/16 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal <appId> \
    --client-secret <password>
```

AKS 클러스터를 만들 때 네트워크 보안 그룹 및 경로 테이블이 자동으로 만들어집니다. 이러한 네트워크 리소스는 AKS 제어 평면에 의해 관리 됩니다. 네트워크 보안 그룹은 노드의 가상 Nic와 자동으로 연결 됩니다. 경로 테이블은 가상 네트워크 서브넷과 자동으로 연결 됩니다. 네트워크 보안 그룹 규칙 및 경로 테이블은 서비스를 만들고 노출할 때 자동으로 업데이트 됩니다.

## <a name="bring-your-own-subnet-and-route-table-with-kubenet"></a>Kubenet를 사용 하 여 사용자 고유의 서브넷 및 경로 테이블 가져오기

Kubenet를 사용 하는 경우에는 클러스터 서브넷에 경로 테이블이 있어야 합니다. AKS는 사용자 고유의 기존 서브넷 및 경로 테이블을 가져오는 것을 지원 합니다.

사용자 지정 서브넷에 경로 테이블이 포함 되어 있지 않은 경우 AKS는 사용자를 위해 하나를 만들고 클러스터 수명 주기 동안 규칙을 추가 합니다. 클러스터를 만들 때 사용자 지정 서브넷에 경로 테이블이 포함 되어 있는 경우 AKS는 클러스터 작업 중에 기존 경로 테이블을 승인 하 고 클라우드 공급자 작업에 적절 하 게 규칙을 추가/업데이트 합니다.

> [!WARNING]
> 사용자 지정 규칙을 사용자 지정 경로 테이블에 추가 하 고 업데이트할 수 있습니다. 그러나 Kubernetes 클라우드 공급자는 업데이트 하거나 제거 하지 않아야 하는 규칙을 추가 합니다. 0.0.0.0/0과 같은 규칙은 항상 지정 된 경로 테이블에 존재 해야 하며 NVA 또는 다른 송신 게이트웨이와 같은 인터넷 게이트웨이의 대상에 매핑됩니다. 사용자 지정 규칙만 수정 되는 규칙을 업데이트할 때는 주의 해야 합니다.

[사용자 지정 경로 테이블][custom-route-table]을 설정 하는 방법에 대해 자세히 알아보세요.

Kubenet 네트워킹에서는 요청을 성공적으로 라우팅하기 위해 구성 된 경로 테이블 규칙이 필요 합니다. 이 설계로 인해 경로 테이블은이를 사용 하는 각 클러스터에 대해 신중 하 게 유지 관리 해야 합니다. 여러 클러스터의 pod CIDRs가 중복 되어 예기치 않은 라우팅이 발생 하는 것으로 인해 여러 클러스터에서 경로 테이블을 공유할 수 없습니다. 동일한 가상 네트워크에 있는 여러 클러스터를 구성 하거나 각 클러스터에 가상 네트워크를 전용으로 구성 하는 경우 다음 제한 사항을 고려 하십시오.

제한 사항:

* 클러스터를 만들기 전에 사용 권한을 할당 해야 합니다. 사용자 지정 서브넷 및 사용자 지정 경로 테이블에 대 한 쓰기 권한이 있는 서비스 주체를 사용 하 고 있는지 확인 하세요.
* 관리 id는 현재 kubenet의 사용자 지정 경로 테이블에서 지원 되지 않습니다.
* AKS 클러스터를 만들기 전에 사용자 지정 경로 테이블을 서브넷에 연결 해야 합니다.
* 클러스터를 만든 후에는 연결 된 경로 테이블 리소스를 업데이트할 수 없습니다. 경로 테이블 리소스는 업데이트할 수 없지만 경로 테이블에서는 사용자 지정 규칙을 수정할 수 있습니다.
* 각 AKS 클러스터는 클러스터와 연결 된 모든 서브넷에 대해 고유한 하나의 경로 테이블을 사용 해야 합니다. 겹치는 pod CIDRs 및 충돌 하는 라우팅 규칙이 있을 수 있으므로 여러 클러스터에서 경로 테이블을 다시 사용할 수 없습니다.

사용자 지정 경로 테이블을 만들고 가상 네트워크의 서브넷에 연결한 후에는 경로 테이블을 사용 하는 새 AKS 클러스터를 만들 수 있습니다.
AKS 클러스터를 배포 하려는 경우에는 서브넷 ID를 사용 해야 합니다. 이 서브넷은 사용자 지정 경로 테이블에도 연결 되어야 합니다.

```azurecli-interactive
# Find your subnet ID
az network vnet subnet list --resource-group
                            --vnet-name
                            [--subscription]
```

```azurecli-interactive
# Create a kubernetes cluster with with a custom subnet preconfigured with a route table
az aks create -g MyResourceGroup -n MyManagedCluster --vnet-subnet-id MySubnetID
```

## <a name="next-steps"></a>다음 단계

기존 가상 네트워크 서브넷에 배포된 AKS 클러스터를 정상적으로 사용할 수 있습니다. [Azure Dev Spaces를 사용 하 여 앱 빌드][dev-spaces], [투구를 사용 하 여 기존 앱 배포][use-helm]또는 [투구를 사용 하 여 새 앱 만들기][develop-helm]를 시작 하세요.

<!-- LINKS - External -->
[dev-spaces]: ../dev-spaces/index.yml
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[Calico-network-policies]: https://docs.projectcalico.org/v3.9/security/calico-network-policy

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
[byo-subnet-route-table]: #bring-your-own-subnet-and-route-table-with-kubenet
[develop-helm]: quickstart-helm.md
[use-helm]: kubernetes-helm.md
[virtual-nodes]: virtual-nodes-cli.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[express-route]: ../expressroute/expressroute-introduction.md
[network-comparisons]: concepts-network.md#compare-network-models
[custom-route-table]: ../virtual-network/manage-route-table.md
