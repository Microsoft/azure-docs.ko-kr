---
title: Azure Kubernetes 서비스 (AKS)의 API server 권한 있는 IP 범위
description: Azure Kubernetes 서비스 (AKS)에서 API 서버에 액세스 하기 위한 IP 주소 범위를 사용 하 여 클러스터를 보호 하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/05/2019
ms.author: mlearned
ms.openlocfilehash: 5f3e6cf8c5de8d5f3de17ad0b5d4bb4c004c06df
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75442989"
---
# <a name="secure-access-to-the-api-server-using-authorized-ip-address-ranges-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 권한이 부여 된 IP 주소 범위를 사용 하 여 API 서버에 대한 액세스 보호

Kubernetes에서 API 서버는 리소스를 만들거나 노드 수를 조정 하는 등 클러스터에서 작업을 수행 하기 위한 요청을 수신 합니다. API 서버는 클러스터와 상호 작용 하 고 클러스터를 관리 하는 중심 방법입니다. 클러스터 보안을 개선 하 고 공격을 최소화 하려면 제한 된 IP 주소 범위 에서만 API 서버에 액세스할 수 있어야 합니다.

이 문서에서는 API server 권한이 부여 된 IP 주소 범위를 사용 하 여 제어 평면에 액세스할 수 있는 IP 주소 및 CIDRs를 제한 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> 새 클러스터에서 API 서버 권한이 부여 된 IP 주소 범위는 *표준* SKU 부하 분산 장치 에서만 지원 됩니다. *기본* sku 부하 분산 장치 및 API 서버 권한이 부여 된 IP 주소 범위를 포함 하는 기존 클러스터는 계속 작동 하지만 *표준* SKU 부하 분산 장치로 마이그레이션될 수 없습니다. 이러한 기존 클러스터는 Kubernetes 버전이 나 제어 평면이 업그레이드 된 경우에도 계속 작동 합니다.

## <a name="before-you-begin"></a>시작하기 전에

API server 권한 있는 IP 범위는 새로 만든 AKS 클러스터에만 적용 됩니다. 이 문서에서는 Azure CLI를 사용 하 여 AKS 클러스터를 만드는 방법을 보여 줍니다.

Azure CLI 버전 2.0.76 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

## <a name="overview-of-api-server-authorized-ip-ranges"></a>API server 권한 있는 IP 범위 개요

Kubernetes API 서버는 기본 Kubernetes Api가 노출 되는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다. AKS는 전용 API 서버를 사용 하 여 단일 테넌트 클러스터 마스터를 제공 합니다. 기본적으로 API 서버에는 공용 IP 주소가 할당 되며, RBAC (역할 기반 액세스 제어)를 사용 하 여 액세스를 제어 해야 합니다.

공개적으로 액세스할 수 있는 AKS 제어 평면/a p i 서버에 대한 액세스를 보호 하기 위해 권한 있는 IP 범위를 사용 하도록 설정 하 고 사용할 수 있습니다. 이러한 권한 있는 IP 범위는 정의 된 IP 주소 범위만 API 서버와 통신 하도록 허용 합니다. 이러한 권한 있는 IP 범위에 속하지 않은 IP 주소에서 API 서버에 대한 요청은 차단 됩니다. RBAC를 사용 하 여 사용자 및 사용자가 요청한 작업에 권한을 부여 합니다.

API 서버 및 기타 클러스터 구성 요소에 대한 자세한 내용은 [Kubernetes core 개념 AKS][concepts-clusters-workloads]을 참조 하세요.

## <a name="create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled"></a>API server 권한 부여 된 IP 범위를 사용 하 여 AKS 클러스터 만들기

API server 권한 있는 IP 범위는 새 AKS 클러스터에만 적용 됩니다. [Az aks create][az-aks-create] 를 사용 하 여 클러스터를 만들고 *--api-version-범위* 매개 변수를 지정 하 여 권한이 부여 된 ip 주소 범위 목록을 제공 합니다. 이러한 IP 주소 범위는 일반적으로 온-프레미스 네트워크 또는 공용 Ip에서 사용 되는 주소 범위입니다. CIDR 범위를 지정 하는 경우 범위의 첫 번째 IP 주소부터 시작 합니다. 예를 들어 *137.117.106.90/29* 는 유효한 범위 이지만 *137.117.106.88/29*와 같이 범위의 첫 번째 IP 주소를 지정 해야 합니다.

> [!IMPORTANT]
> 기본적으로 클러스터는 아웃 바운드 게이트웨이를 구성 하는 데 사용할 수 있는 [표준 SKU 부하 분산 장치][standard-sku-lb] 를 사용 합니다. 클러스터를 만드는 동안 API server 권한 있는 IP 범위를 사용 하도록 설정 하면 지정 된 범위 외에도 기본적으로 클러스터에 대한 공용 IP가 허용 됩니다. *--------* ---------------------------------

다음 예제에서는 API server 권한이 부여 된 IP 범위를 사용 하 여 *Myresourcegroup* 이라는 리소스 그룹에 *myAKSCluster* 라는 단일 노드 클러스터를 만듭니다. 허용 되는 IP 주소 범위는 *73.140.245.0/24*입니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --generate-ssh-keys
```

> [!NOTE]
> 이러한 범위는 허용 목록에 추가 해야 합니다.
> - 방화벽 공용 IP 주소
> - 클러스터를 관리할 네트워크를 나타내는 모든 범위
> - AKS 클러스터에서 Azure Dev Spaces를 사용 하는 경우 [해당 지역에 따라 추가 범위][dev-spaces-ranges]를 허용 해야 합니다.

### <a name="specify-the-outbound-ips-for-the-standard-sku-load-balancer"></a>표준 SKU 부하 분산 장치에 대한 아웃 바운드 Ip를 지정 합니다.

AKS 클러스터를 만들 때 클러스터에 대한 아웃 바운드 IP 주소 또는 접두사를 지정 하면 해당 주소 또는 접두사도 허용 됩니다. 예:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 73.140.245.0/24 \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2> \
    --generate-ssh-keys
```

위의 예에서는 *-* --------------------------------접두사 매개 변수에서 ip *접두사* 와 함께 제공 된 모든 ip가 허용 됩니다.

또는 *--부하 분산 장치----접두사* 매개 변수를 지정 하 여 아웃 바운드 부하 분산 장치 ip 접두사를 허용 합니다.

### <a name="allow-only-the-outbound-public-ip-of-the-standard-sku-load-balancer"></a>표준 SKU 부하 분산 장치의 아웃 바운드 공용 IP만 허용

클러스터를 만드는 동안 API server 권한 있는 IP 범위를 사용 하도록 설정 하면 사용자가 지정한 범위 외에도 기본적으로 클러스터에 대한 표준 SKU 부하 분산 장치에 대한 아웃 바운드 공용 IP가 허용 됩니다. 표준 SKU 부하 분산 장치의 아웃 바운드 공용 IP만 허용 하려면 *--api-version-범위* 매개 변수를 지정할 때 *0.0.0.0/32* 를 사용 합니다.

다음 예제에서는 표준 SKU 부하 분산 장치의 아웃 바운드 공용 IP만 허용 되며, 클러스터 내의 노드에서 API 서버에만 액세스할 수 있습니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --api-server-authorized-ip-ranges 0.0.0.0/32 \
    --generate-ssh-keys
```

## <a name="update-a-clusters-api-server-authorized-ip-ranges"></a>클러스터의 API 서버에 권한 있는 IP 범위 업데이트

기존 클러스터에서 API server 권한 있는 IP 범위를 업데이트 하려면 [az aks update][az-aks-update] 명령을 사용 하 고-- *API*------------- *---* --------------- *--* - *--* .

다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myAKSCluster* 이라는 클러스터의 API server 권한 있는 IP 범위를 업데이트 합니다. 권한 부여를 위한 IP 주소 범위는 *73.140.245.0/24*입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges  73.140.245.0/24
```

표준 SKU 부하 분산 장치의 공용 IP만 허용 하도록 *--api-version----* -----범위 매개 변수를 지정할 때 *0.0.0.0/32* 를 사용할 수도 있습니다.

## <a name="disable-authorized-ip-ranges"></a>권한 있는 IP 범위 사용 안 함

권한 있는 IP 범위를 사용 하지 않도록 설정 하려면 [az aks update][az-aks-update] 를 사용 하 고 API server 권한 있는 ip 범위를 사용 하지 않도록 빈 범위를 지정 합니다. 예:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --api-server-authorized-ip-ranges ""
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 API 서버에 권한 있는 IP 범위를 사용 하도록 설정 했습니다. 이 방법은 secure AKS 클러스터를 실행 하는 방법의 한 부분입니다.

자세한 내용은 [AKS의 응용 프로그램 및 클러스터에 대한 보안 개념][concepts-security] 및 [AKS에서 클러스터 보안 및 업그레이드에 대한 모범 사례][operator-best-practices-cluster-security]를 참조 하세요.

<!-- LINKS - external -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[dev-spaces-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[kubenet]: https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/#kubenet

<!-- LINKS - internal -->
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[concepts-security]: concepts-security.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[route-tables]: ../virtual-network/manage-route-table.md
[standard-sku-lb]: load-balancer-standard.md
