---
title: API 서버에 IP 범위 Azure Kubernetes Service (AKS)에서 권한이 부여
description: Azure Kubernetes Service (AKS)에서 API 서버에 대 한 액세스에 대 한 IP 주소 범위를 사용 하 여 클러스터를 보호 하는 방법에 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 9ec48c8ed924293a5ffea903fe03a9830dcd1184
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329426"
---
# <a name="preview---secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>미리 보기-통해 API 서버에 대 한 보안 액세스 권한이 Azure Kubernetes Service (AKS)에서 IP 주소 범위

Kubernetes API 서버는 리소스 만들기 또는 노드 수를 조정 하려면 같은 클러스터에서 작업을 수행 하는 요청을 받습니다. API 서버는 중앙 방법 상호 작용 하 고 클러스터를 관리 합니다. 클러스터 보안을 개선 하 고 공격을 최소화 하려면 API 서버에 IP 주소 범위 제한 된 집합에서 액세스할 수만.

이 문서에서는 API 권한이 부여 된 서버의 IP 주소 범위를 사용 하 여 제어 평면에 대 한 요청을 제한 하는 방법을 보여 줍니다. 이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스, 옵트인 합니다. 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 미리 보기에서이 기능이 없는 프로덕션 사용 해야 합니다. 공개 미리 보기에서 기능 '최상의' 지원에 속합니다. AKS 기술 지원 팀의 지원 업무 시간은 태평양 표준 시간대 (PST)만 제공 됩니다. 추가 정보는 다음과 같은 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

API 서버 권한이 부여 된 IP 범위를 만들면 새 AKS 클러스터에 대해서만 작동 합니다. 이 문서에서는 Azure CLI를 사용 하 여 AKS 클러스터를 만드는 방법을 보여 줍니다.

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드를 참조 해야 하는 경우 [Azure CLI 설치][install-azure-cli]합니다.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

필요한 API 권한이 부여 된 서버 IP 범위를 구성 하는 *aks 미리 보기* CLI 확장 버전 0.4.1 이상. 설치 합니다 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] 명령:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flag-for-your-subscription"></a>구독에 대해 기능 플래그를 등록 합니다.

API 서버 권한이 부여 된 IP 범위를 사용 하려면 먼저 구독 기능 플래그를 사용 하도록 설정 합니다. 등록 하는 *APIServerSecurityPreview* 기능 플래그를 사용 합니다 [az 기능 등록][az-feature-register] 다음 예와에서 같이 명령:

> [!CAUTION]
> 구독에서 기능을 등록 하면 수 없는 현재 등록을 취소 기능. 일부 미리 보기 기능을 사용 하도록 설정 하면 구독에서 만든 모든 AKS 클러스터에 대 한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하지 마십시오. 별도 구독을 사용 하 여 미리 보기 기능을 테스트 및 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name APIServerSecurityPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. 사용 하 여 등록 상태를 확인할 수 있습니다 합니다 [az 기능 목록][az-feature-list] 명령:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/APIServerSecurityPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 새로 고침의 등록 합니다 *Microsoft.ContainerService* 사용 하 여 리소스 공급자를 [az provider register][az-provider-register] 명령:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>제한 사항

API 서버 권한이 부여 된 IP 범위를 구성할 때 다음과 같은 제한이 있습니다.

* API 서버와의 통신도 차단 됩니다 Azure 개발 공간 현재 사용할 수 없습니다.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API 서버 개요 공인 IP 범위

Kubernetes API 서버가 기본 Kubernetes Api 노출 되는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다. AKS는 단일 테 넌 트 클러스터 마스터 전용된 API 서버를 사용 하 여 제공합니다. 기본적으로 API 서버에는 공용 IP 주소가 할당 됩니다 하 고 역할 기반 액세스 제어 (RBAC)를 사용 하 여 액세스를 제어 해야 합니다.

그렇지 않으면 공개적으로 액세스 가능한 AKS 제어 평면에 대 한 보안 액세스를 API 서버를 사용 하 여 / IP 범위를 승인 합니다. 이러한 권한 있는 IP 범위 정의 된 IP 주소 범위 API 서버와 통신 하도록 허용 합니다. 이러한 권한 있는 IP 범위에 속하지 않은 IP 주소에서 API 서버에 대 한 요청을 차단 됩니다. RBAC를 사용 하 여 사용자 및 요청 작업을 만든 다음 권한을 부여할 계속 해야 합니다.

API 서버 및 기타 클러스터 구성 요소에 대 한 자세한 내용은 참조 하세요. [AKS에 대 한 핵심 개념인 Kubernetes][concepts-clusters-workloads]합니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

API 서버 권한이 부여 된 IP 범위 새 AKS 클러스터에 대해서만 작동 합니다. 클러스터의 일부 작업을 만들 때 권한 있는 IP 범위를 사용할 수 없습니다. 사용 하도록 설정할 경우 송신 IP 주소를 해당 시점에 정의 되어 있지 않은으로 배포 하는 동안 API 서버에 액세스할 수 없는 클러스터 노드, 클러스터의 일부분으로 권한이 부여 된 IP 범위 프로세스를 만듭니다.

먼저 사용 하 여 클러스터를 만듭니다는 [az aks 만들기][az-aks-create] 명령입니다. 다음 예제에서는 라는 단일 노드 클러스터를 만듭니다 *myAKSCluster* 이라는 리소스 그룹에서 *myResourceGroup*합니다.

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location eastus

# Create an AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --generate-ssh-keys

# Get credentials to access the cluster
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-outbound-gateway-for-firewall-rules"></a>방화벽 규칙에 대 한 아웃 바운드 게이트웨이 만들기

다음 섹션에서 권한 있는 IP 범위를 사용 하도록 설정 하면 클러스터의 노드 API 서버와 통신할 안정적으로 수 있도록 아웃 바운드 게이트웨이로 사용에 대 한 Azure 방화벽을 만듭니다. 다음 Azure 방화벽의 IP 주소는 다음 섹션에서 권한이 부여 된 API 서버 IP 주소 목록에 추가 됩니다.

> [!WARNING]
> Azure 방화벽의 사용은 월간 청구 주기 동안 상당한 비용이 수 있습니다. Azure 방화벽을 사용 하는 요구 사항을이 초기 미리 보기 기간에만 해야 해야 합니다. 자세한 내용 및 비용 계획을 참조 하세요 [Azure 방화벽 가격][azure-firewall-costs]합니다.

먼저 가져옵니다 합니다 *MC_* AKS 클러스터와 가상 네트워크에 대 한 리소스 그룹 이름입니다. 그런 다음 사용 하 여 서브넷을 만듭니다는 [az 네트워크 vnet 서브넷 만들기][az-network-vnet-subnet-create] 명령입니다. 다음 예에서는 이라는 서브넷을 만듭니다 *AzureFirewallSubnet* CIDR 범위를 사용 하 여 *10.200.0.0/16*:

```azurecli-interactive
# Get the name of the MC_ cluster resource group
MC_RESOURCE_GROUP=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query nodeResourceGroup -o tsv)

# Get the name of the virtual network used by the cluster
VNET_NAME=$(az network vnet list \
    --resource-group $MC_RESOURCE_GROUP \
    --query [0].name -o tsv)

# Create a subnet in the virtual network for Azure Firewall
az network vnet subnet create \
    --resource-group $MC_RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name AzureFirewallSubnet \
    --address-prefixes 10.200.0.0/16
```

만들려면 Azure 방화벽을 설치 합니다는 *azure 방화벽* 사용 하 여 CLI 확장을 [az 확장 추가][az-extension-add] command. Then, create a firewall using the [az network firewall create][az-network-firewall-create] 명령입니다. 다음 예제에서는 라는 Azure 방화벽을 만듭니다 *myAzureFirewall*:

```azurecli-interactive
# Install the CLI extension for Azure Firewall
az extension add --name azure-firewall

# Create an Azure firewall
az network firewall create \
    --resource-group $MC_RESOURCE_GROUP\
    --name myAzureFirewall
```

Azure는 방화벽에는 송신 트래픽이 통과 하는 공용 IP 주소를 할당 됩니다. 사용 하 여 공용 주소를 [az network public ip 만들기][az-network-public-ip-create] command, then create an IP configuration on the firewall using the [az network firewall ip-config create][az-network-firewall-ip-config-create] 적용 되는 공용 IP:

```azurecli-interactive
# Create a public IP address for the firewall
FIREWALL_PUBLIC_IP=$(az network public-ip create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallPublicIP \
    --sku Standard \
    --query publicIp.ipAddress -o tsv)

# Associated the firewall with virtual network
az network firewall ip-config create \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewallIPConfig \
    --vnet-name $VNET_NAME \
    --firewall-name myAzureFirewall \
    --public-ip-address myAzureFirewallPublicIP
```

이제 Azure 방화벽 네트워크 규칙을 만들 *허용* 모든 *TCP* 사용 하 여 트래픽 합니다 [az 네트워크 방화벽 네트워크 규칙 만들기][az-network-firewall-network-rule-create] 명령입니다. 다음 예제에서는 명명 된 네트워크 규칙을 만듭니다 *AllowTCPOutbound* 모든 원본 또는 대상 주소를 사용 하 여 트래픽에 대 한 합니다.

```azurecli-interactive
az network firewall network-rule create \
    --resource-group $MC_RESOURCE_GROUP \
    --firewall-name myAzureFirewall \
    --name AllowTCPOutbound \
    --collection-name myAzureFirewallCollection \
    --priority 200 \
    --action Allow \
    --protocols TCP \
    --source-addresses '*' \
    --destination-addresses '*' \
    --destination-ports '*'
```

Azure 방화벽이 네트워크 경로 사용 하 여에 연결 하려면 기존 경로 테이블 정보, Azure 방화벽의 내부 IP 주소 및 API 서버의 IP 주소를 가져옵니다. 이러한 IP 주소는 클러스터 통신에 대 한 트래픽을 라우팅해야 하는 방법을 제어 하려면 다음 섹션에 지정 됩니다.

```azurecli-interactive
# Get the AKS cluster route table
ROUTE_TABLE=$(az network route-table list \
    --resource-group $MC_RESOURCE_GROUP \
    --query "[?contains(name,'agentpool')].name" -o tsv)

# Get internal IP address of the firewall
FIREWALL_INTERNAL_IP=$(az network firewall show \
    --resource-group $MC_RESOURCE_GROUP \
    --name myAzureFirewall \
    --query ipConfigurations[0].privateIpAddress -o tsv)

# Get the IP address of API server endpoint
K8S_ENDPOINT_IP=$(kubectl get endpoints -o=jsonpath='{.items[?(@.metadata.name == "kubernetes")].subsets[].addresses[].ip}')
```

마지막으로, 기존 AKS 네트워크 경로 테이블 사용 하 여 경로 만듭니다는 [az network 경로 테이블 경로 만들기][az-network-route-table-route-create] API 서버 통신을 위한 Azure 방화벽 어플라이언스를 사용 하는 트래픽을 허용 하는 명령입니다.

```azurecli-interactive
az network route-table route create \
    --resource-group $MC_RESOURCE_GROUP \
    --route-table-name $ROUTE_TABLE \
    --name AzureFirewallAPIServer \
    --address-prefix $K8S_ENDPOINT_IP/32 \
    --next-hop-ip-address $FIREWALL_INTERNAL_IP \
    --next-hop-type VirtualAppliance

echo "Public IP address for the Azure Firewall instance that should be added to the list of API server authorized addresses is:" $FIREWALL_PUBLIC_IP
```

Azure 방화벽 어플라이언스의 공용 IP 주소를 기록해 둡니다. 이 주소는 다음 섹션에서 API 서버 권한이 부여 된 IP 범위 목록에 추가 됩니다.

## <a name="enable-authorized-ip-ranges"></a>권한 있는 IP 범위를 사용 하도록 설정

API 서버 권한이 부여 된 IP 범위를 사용 하려면 권한 있는 IP 주소 범위 목록을 제공할 수 있습니다. CIDR 범위를 지정 하는 경우 범위에서 첫 번째 IP 주소를 사용 하 여 시작 합니다. 예를 들어 *137.117.106.90/29* 올바른 범위는 같은 범위에서 첫 번째 IP 주소를 지정 해야 하지만 *137.117.106.88/29*합니다.

사용 하 여 [az aks 업데이트][az-aks-update] 명령 및 지정 된 *-api-서버-권한이 부여 된-ip 범위* 수 있도록 합니다. 이러한 IP 주소 범위는 일반적으로 온-프레미스 네트워크에서 사용 하는 주소 범위입니다. 와 같은 이전 단계에서 얻은 사용자 고유의 Azure 방화벽의 공용 IP 주소 추가 *20.42.25.196/32*합니다.

다음 예제에서는 명명 된 클러스터에서 API 권한이 부여 된 서버 IP 범위 *myAKSCluster* 이라는 리소스 그룹에서 *myResourceGroup*합니다. IP 주소 범위 권한을 부여 하는 *20.42.25.196/32* (Azure 방화벽 공용 IP 주소), 한 다음 *172.0.0.10/16* 하 고 *168.10.0.10/18*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges 20.42.25.196/32,172.0.0.10/16,168.10.0.10/18
```

## <a name="update-or-disable-authorized-ip-ranges"></a>업데이트 또는 권한 있는 IP 범위를 사용 하지 않도록 설정

다시 사용을 업데이트 하거나 권한 있는 IP 범위를 사용 하지 않도록 설정 하려면 [az aks 업데이트][az-aks-update] 명령입니다. 허용 하거나 다음 예와에서 같이 빈 범위 API 서버를 사용 하지 않도록 설정 권한이 IP 범위를 지정 하려면 업데이트 된 CIDR 범위를 지정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 API 권한이 부여 된 서버 IP 범위를 사용 합니다. 이 방법은 보안 AKS 클러스터를 실행 하는 방법의 한 부분에 설명 합니다.

자세한 내용은 [응용 프로그램 및 AKS 클러스터에 대 한 보안 개념][concepts-security] and [Best practices for cluster security and upgrades in AKS][operator-best-practices-cluster-security]합니다.

<!-- LINKS - external -->
[azure-firewall-costs]: https://azure.microsoft.com/pricing/details/azure-firewall/

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[create-aks-sp]: kubernetes-service-principal.md#manually-create-a-service-principal
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-create]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-create
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-firewall-ip-config-create]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-create
[az-network-firewall-network-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/network-rule#ext-azure-firewall-az-network-firewall-network-rule-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-list]: /cli/azure/extension#az-extension-list
[az-extension-update]: /cli/azure/extension#az-extension-update
