---
title: 개인 Azure Kubernetes Service 클러스터 만들기
description: AKS (개인 Azure Kubernetes Service) 클러스터를 만드는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 1/24/2020
ms.author: mlearned
ms.openlocfilehash: 934dfdb43d6d2e4ccc346b728f0ac4f5febea327
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76932585"
---
# <a name="create-a-private-azure-kubernetes-service-cluster-preview"></a>개인 Azure Kubernetes Service 클러스터 만들기 (미리 보기)

개인 클러스터에서 제어 평면이 나 API 서버에는 [개인 인터넷 문서에 대한 RFC1918 할당](https://tools.ietf.org/html/rfc1918) 에 정의 된 내부 IP 주소가 있습니다. 개인 클러스터를 사용 하 여 API 서버와 노드 풀 간의 네트워크 트래픽이 개인 네트워크에만 남아 있는지 확인할 수 있습니다.

제어 평면 또는 API 서버는 Azure Kubernetes 서비스 (AKS)에서 관리 되는 Azure 구독에 있습니다. 고객의 클러스터 또는 노드 풀은 고객의 구독에 있습니다. 서버와 클러스터 또는 노드 풀은 API 서버 가상 네트워크의 [Azure 개인 링크 서비스][private-link-service] 및 고객의 AKS 클러스터의 서브넷에 노출 되는 개인 엔드포인트을 통해 서로 통신할 수 있습니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 이며 옵트인 기반으로 제공 됩니다. 미리 *보기는 그대로 제공* 되며 *사용 가능* 하 고 SLA (서비스 수준 계약) 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 *최상의 노력* 을 기반으로 고객 지원을 통해 부분적으로 검사 됩니다. 따라서이 기능은 프로덕션 용도로 사용 되지 않습니다. 자세한 내용은 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책](support-policies.md)
> * [Azure 지원 FAQ](faq.md)

## <a name="prerequisites"></a>필수 조건

* Azure CLI 버전 2.0.77 이상 및 Azure CLI AKS Preview 확장 버전 0.4.18

## <a name="currently-supported-regions"></a>현재 지원 되는 지역
* 미국 서부
* 미국 서부 2
* 미국 동부 2
* 캐나다 중부
* 북유럽
* 서유럽
* 오스트레일리아 동부

## <a name="install-the-latest-azure-cli-aks-preview-extension"></a>최신 Azure CLI AKS Preview 확장을 설치 합니다.

개인 클러스터를 사용 하려면 Azure CLI AKS Preview 확장 버전 0.4.18 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 Azure CLI AKS Preview 확장을 설치 하 고 다음 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```
> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대해 기본 설정을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name AKSPrivateLinkPreview --namespace Microsoft.ContainerService
```

등록 상태가 *등록*된 것으로 표시 되는 데 몇 분 정도 걸릴 수 있습니다. 다음 [az feature list][az-feature-list] 명령을 사용 하 여 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSPrivateLinkPreview')].{Name:name,State:properties.state}"
```

상태가 등록 됨 인 경우 다음 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자의 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
az provider register --namespace Microsoft.Network
```
## <a name="create-a-private-aks-cluster"></a>개인 AKS 클러스터 만들기

### <a name="default-basic-networking"></a>기본 네트워킹 

```azurecli-interactive
az aks create -n <private-cluster-name> -g <private-cluster-resource-group> --load-balancer-sku standard --enable-private-cluster  
```
여기서 *--enable-private-cluster* 는 개인 클러스터에 대한 필수 플래그입니다. 

### <a name="advanced-networking"></a>고급 네트워킹  

```azurecli-interactive
az aks create \
    --resource-group <private-cluster-resource-group> \
    --name <private-cluster-name> \
    --load-balancer-sku standard \
    --enable-private-cluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 
```
여기서 *--enable-private-cluster* 는 개인 클러스터에 대한 필수 플래그입니다. 

> [!NOTE]
> Docker 브리지 주소 CIDR (172.17.0.1/16)이 서브넷 CIDR과 충돌 하는 경우 Docker 브리지 주소를 적절 하 게 변경 합니다.

## <a name="connect-to-the-private-cluster"></a>개인 클러스터에 연결
API 서버 엔드포인트에 공용 IP 주소가 없습니다. 따라서 가상 네트워크에서 Azure VM (가상 머신)을 만들고 API 서버에 연결 해야 합니다. 이렇게 하려면 다음을 수행합니다.

1. 클러스터에 연결 하기 위한 자격 증명을 가져옵니다.

   ```azurecli-interactive
   az aks get-credentials --name MyManagedCluster --resource-group MyResourceGroup
   ```

1. 다음 중 하나를 수행합니다.
   * AKS 클러스터와 동일한 가상 네트워크에 VM을 만듭니다.  
   * 다른 가상 네트워크에 VM을 만들고 AKS 클러스터 가상 네트워크를 사용 하 여이 가상 네트워크를 피어 링 합니다.

     다른 가상 네트워크에 VM을 만드는 경우이 가상 네트워크와 개인 DNS 영역 간의 링크를 설정 합니다. 이렇게 하려면 다음을 수행합니다.
    
     a. Azure Portal에서 MC_ * 리소스 그룹으로 이동 합니다.  
     b. 개인 DNS 영역을 선택 합니다.   
     다. 왼쪽 창에서 **Virtual network** 링크를 선택 합니다.  
     d. VM의 가상 네트워크를 개인 DNS 영역에 추가 하는 새 링크를 만듭니다. DNS 영역 링크를 사용할 수 있게 되는 데 몇 분 정도 걸립니다.  
     e. Azure Portal에서 MC_ * 리소스 그룹으로 돌아갑니다.  
     f. 오른쪽 창에서 가상 네트워크를 선택 합니다. 가상 네트워크 이름은 *aks-\** 형식으로 되어 있습니다.  
     g. 왼쪽 창에서 **피어 링**을 선택 합니다.  
     h. **추가**를 선택 하 고 VM의 가상 네트워크를 추가한 다음 피어 링을 만듭니다.  
     i. VM이 있는 가상 네트워크로 이동 하 **여 피어 링을 선택 하**고 AKS 가상 네트워크를 선택한 다음 피어 링을 만듭니다. AKS 가상 네트워크의 주소 범위와 VM의 가상 네트워크가 충돌 하는 경우 피어 링이 실패 합니다. 자세한 내용은 [가상 네트워크 피어 링][virtual-network-peering]을 참조 하세요.

1. Secure Shell (SSH)를 통해 VM에 액세스 합니다.
1. Kubectl 도구를 설치 하 고 Kubectl 명령을 실행 합니다.


## <a name="dependencies"></a>종속성  
* 개인 링크 서비스는 표준 Azure Load Balancer 에서만 지원 됩니다. 기본 Azure Load Balancer 지원 되지 않습니다.  
* 사용자 지정 DNS 서버를 사용 하려면 DNS를 사용 하 여이 IP 168.63.129.16 전달 되는 AD 서버를 배포 합니다.

## <a name="limitations"></a>제한 사항 
* 가용성 영역는 현재 미국 동부 2 및 미국 서 부 2 지역 에서만 지원 됩니다.
* [Azure 개인 링크 서비스 제한은][private-link-service] 현재 동일한 가상 네트워크에서 지원 되지 않는 개인 클러스터, Azure 개인 엔드포인트 및 가상 네트워크 서비스 엔드포인트에 적용 됩니다.
* 개인 Azure 가상 네트워크에서 개인 Azure Container Instances (ACI)를 spin 개인 클러스터의 가상 노드가 지원 되지 않음
* 개인 클러스터와 함께 기본 제공 되는 Azure DevOps 통합을 지원 하지 않습니다.
* Azure Container Registry를 개인 AKS 사용 하도록 설정 해야 하는 고객의 경우 Container Registry 가상 네트워크는 에이전트 클러스터 가상 네트워크와 피어 링 되어야 합니다.
* 현재 Azure Dev Spaces 지원 안 함
* 기존 AKS 클러스터를 개인 클러스터로 변환 하는 기능이 지원 되지 않음
* 고객 서브넷에서 개인 엔드포인트을 삭제 하거나 수정 하면 클러스터의 작동이 중지 됩니다. 
* 컨테이너 라이브 데이터에 대한 Azure Monitor 현재 지원 되지 않습니다.
* *사용자 고유의 DNS 가져오기* 는 현재 지원 되지 않습니다.


<!-- LINKS - internal -->
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[private-link-service]: https://docs.microsoft.com/azure/private-link/private-link-service-overview
[virtual-network-peering]: ../virtual-network/virtual-network-peering-overview.md

