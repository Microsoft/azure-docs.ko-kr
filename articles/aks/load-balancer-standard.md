---
title: AKS (Azure Kubernetes Service)에서 표준 SKU 부하 분산 장치 사용
description: 표준 SKU에서 부하 분산 장치를 사용 하 여 Azure Kubernetes 서비스 (AKS)를 통해 서비스를 노출 하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/27/2019
ms.author: zarhoads
ms.openlocfilehash: b15c60d5436feada8558c83cb14efd7e21a22493
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212424"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 표준 SKU 부하 분산 장치 사용

AKS (Azure Kubernetes Service)에서 `LoadBalancer` 형식의 Kubernetes services를 통해 응용 프로그램에 대 한 액세스를 제공 하려면 Azure Load Balancer를 사용할 수 있습니다. AKS에서 실행 중인 부하 분산 장치는 내부 또는 외부 부하 분산 장치로 사용할 수 있습니다. 내부 부하 분산 장치는 AKS 클러스터와 동일한 가상 네트워크에서 실행 되는 응용 프로그램에만 Kubernetes 서비스에 액세스할 수 있도록 합니다. 외부 부하 분산 장치는 수신에 대 한 공용 Ip를 하나 이상 수신 하 고 공용 Ip를 사용 하 여 외부에서 Kubernetes 서비스에 액세스할 수 있도록 합니다.

Azure Load Balancer는 ‘기본’ 및 ‘표준’이라는 두 SKU에서 사용할 수 있습니다. 기본적으로 *표준* SKU는 AKS 클러스터를 만들 때 사용 됩니다. *표준* SKU 부하 분산 장치를 사용 하면 더 큰 백 엔드 풀 크기 및 가용성 영역 같은 추가 기능 및 기능이 제공 됩니다. 사용할 *것을 선택* 하기 전에 *표준* 부하 분산 장치 간의 차이점을 이해 하는 것이 중요 합니다. AKS 클러스터를 만든 후에는 해당 클러스터에 대 한 부하 분산 장치 SKU를 변경할 수 없습니다. *기본* 및 *표준* sku에 대 한 자세한 내용은 [Azure 부하 분산 장치 SKU 비교][azure-lb-comparison]를 참조 하세요.

이 문서에서는 Kubernetes 및 Azure Load Balancer 개념을 기본적으로 이해 하 고 있다고 가정 합니다. 자세한 내용은 [Kubernetes core 개념에 대 한 AKS (Azure Kubernetes Service)][kubernetes-concepts] 및 [Azure Load Balancer 정의][azure-lb]를 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치 하 고 사용 하도록 선택 하는 경우이 문서에서는 Azure CLI 버전 2.0.81 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 *표준* SKU AZURE LOAD BALANCER 있는 AKS 클러스터가 있다고 가정 합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

기존 서브넷 또는 리소스 그룹을 사용 하는 경우 AKS 클러스터 서비스 주체에도 네트워크 리소스를 관리할 수 있는 권한이 있어야 합니다. 일반적으로 위임 된 리소스의 서비스 사용자에 게 *네트워크 참가자* 역할을 할당 합니다. 사용 권한에 대 한 자세한 내용은 [DELEGATE AKS access to Other Azure resources를][aks-sp]참조 하세요.

### <a name="moving-from-a-basic-sku-load-balancer-to-standard-sku"></a>기본 SKU Load Balancer에서 표준 SKU로 이동

기본 SKU Load Balancer를 사용 하는 기존 클러스터가 있는 경우, 표준 SKU Load Balancer를 사용 하 여 클러스터를 사용 하도록 마이그레이션할 때 유의 해야 할 중요 한 동작 차이점이 있습니다.

예를 들어 클러스터를 마이그레이션하기 위한 파란색/녹색 배포를 수행 하는 것이 일반적인 방법입니다. 클러스터의 `load-balancer-sku` 유형은 클러스터 만들기 시간에만 정의할 수 있습니다. 그러나 *기본 sku* 부하 분산 장치 *는 표준 sku ip 주소가 필요 하므로* *표준 sku* 부하 분산 장치와 호환 되지 않는 *기본 sku* ip 주소를 사용 합니다. Load Balancer Sku 업그레이드로 클러스터를 마이그레이션하는 경우 호환 되는 IP 주소 SKU를 사용 하는 새 IP 주소가 필요 합니다.

클러스터를 마이그레이션하는 방법에 대 한 자세한 내용은 [마이그레이션 고려 사항에](acs-aks-migration.md) 대 한 설명서를 참조 하 여 마이그레이션할 때 고려해 야 할 중요 항목의 목록을 확인 하세요. AKS에서 표준 SKU 부하 분산 장치를 사용 하는 경우 아래 제한 사항에 유의 해야 하는 중요 한 동작이 있습니다.

### <a name="limitations"></a>제한 사항

*표준* SKU를 사용 하 여 부하 분산 장치를 지 원하는 AKS 클러스터를 만들고 관리 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* AKS 클러스터에서 송신 트래픽을 허용 하려면 하나 이상의 공용 IP 또는 IP 접두사가 필요 합니다. 공용 IP 또는 IP 접두사는 AKS의 이전 버전과의 호환성을 유지 하기 위해 제어 평면과 에이전트 노드 간의 연결을 유지 하는 데에도 필요 합니다. *표준* SKU 부하 분산 장치를 사용 하 여 공용 IP 또는 IP 접두사를 지정 하는 다음과 같은 옵션이 있습니다.
    * 사용자 고유의 공용 Ip를 제공 합니다.
    * 사용자 고유의 공용 IP 접두사를 제공 합니다.
    * AKS 클러스터가 AKS 클러스터로 만든 것과 동일한 리소스 그룹에 여러 *표준* SKU 공용 ip를 만들 수 있도록 허용 하는 숫자를 100까지 지정 합니다 .이는 일반적으로 처음에 *MC_* 으로 명명 됩니다. AKS는 *표준* SKU 부하 분산 장치에 공용 IP를 할당 합니다. 공용 ip, 공용 IP 접두사 또는 Ip 수가 지정 되지 않은 경우 기본적으로 하나의 공용 IP가 AKS 클러스터와 동일한 리소스 그룹에 자동으로 만들어집니다. 또한 공용 주소를 허용 하 고 IP 생성을 차단을 하는 Azure Policy를 만들지 않도록 해야 합니다.
* 부하 분산 장치에 대 한 *표준* SKU를 사용 하는 경우 Kubernetes 버전 *1.13 이상을*사용 해야 합니다.
* 부하 분산 장치 SKU를 정의 하는 것은 AKS 클러스터를 만들 때만 수행할 수 있습니다. AKS 클러스터를 만든 후에는 부하 분산 장치 SKU를 변경할 수 없습니다.
* 단일 클러스터에서 한 가지 유형의 부하 분산 장치 SKU (기본 또는 표준)만 사용할 수 있습니다.
* *표준* SKU 부하 분산 장치는 *표준* Sku IP 주소만 지원 합니다.

## <a name="use-the-standard-sku-load-balancer"></a>*표준* SKU 부하 분산 장치 사용

AKS 클러스터를 만들 때 기본적으로 *표준* SKU 부하 분산 장치는 해당 클러스터에서 서비스를 실행할 때 사용 됩니다. 예를 들어 [Azure CLI를 사용 하는 빠른 시작은][aks-quickstart-cli] *표준* SKU 부하 분산 장치를 사용 하는 샘플 응용 프로그램을 배포 합니다.

> [!IMPORTANT]
> UDR (사용자 정의 경로)을 사용자 지정 하 여 공용 IP 주소를 피할 수 있습니다. AKS 클러스터의 아웃 바운드 유형을 UDR로 지정 하면 AKS 만든 Azure 부하 분산 장치에 대 한 IP 프로 비전 및 백 엔드 풀 설정을 건너뛸 수 있습니다. [클러스터의 `outboundType`을 ' userDefinedRouting '로 설정을](egress-outboundtype.md)참조 하세요.

## <a name="configure-the-load-balancer-to-be-internal"></a>부하 분산 장치를 내부로 구성

부하 분산 장치를 내부로 구성 하 고 공용 IP를 노출 하지 않을 수도 있습니다. 부하 분산 장치를 내부로 구성 하려면 `service.beta.kubernetes.io/azure-load-balancer-internal: "true"`를 *LoadBalancer* 서비스에 주석으로 추가 합니다. [여기][internal-lb-yaml]에서 내부 부하 분산 장치에 대 한 자세한 정보 뿐만 아니라 yaml 매니페스트 예제를 볼 수 있습니다.

## <a name="scale-the-number-of-managed-public-ips"></a>관리 되는 공용 Ip 수 크기 조정

기본적으로 생성 되는 관리 되는 아웃 바운드 공용 Ip를 사용 하 여 *표준* SKU 부하 분산 장치를 사용 하는 경우, *부하 분산 장치 관리-ip 카운트* 매개 변수를 사용 하 여 관리 되는 아웃 바운드 공용 ip 수를 조정할 수 있습니다.

기존 클러스터를 업데이트 하려면 다음 명령을 실행 합니다. 이 매개 변수는 클러스터 만들기 시간에 여러 관리 되는 아웃 바운드 공용 Ip를 갖도록 설정할 수도 있습니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

위의 예에서는 *Myresourcegroup*의 *myAKSCluster* 클러스터에 대 한 관리 되는 아웃 바운드 공용 ip 수를 *2* 로 설정 합니다. 

또한 `--load-balancer-managed-outbound-ip-count` 매개 변수를 추가 하 고 원하는 값으로 설정 하 여 클러스터를 만들 때 *부하 분산 장치-관리-ip 카운트* 매개 변수를 사용 하 여 초기에 관리 되는 아웃 바운드 공용 ip 수를 설정할 수 있습니다. 관리 되는 아웃 바운드 공용 Ip의 기본 수는 1입니다.

## <a name="provide-your-own-public-ips-or-prefixes-for-egress"></a>송신을 위해 사용자 고유의 공용 Ip 또는 접두사 제공

*표준* sku 부하 분산 장치를 사용 하는 경우 AKS 클러스터는 AKS 클러스터에 대해 생성 된 동일한 리소스 그룹에서 공용 ip를 자동으로 만들고 *표준* SKU 부하 분산 장치에 공용 ip를 할당 합니다. 또는 클러스터를 만들 때 사용자 고유의 공용 IP를 할당 하거나 기존 클러스터의 부하 분산 장치 속성을 업데이트할 수 있습니다.

여러 IP 주소 또는 접두사를 가져오면 단일 부하 분산 장치 개체 뒤에 IP 주소를 정의할 때 여러 지원 서비스를 정의할 수 있습니다. 특정 노드의 송신 끝점은 연결 된 서비스에 따라 달라 집니다.

> [!IMPORTANT]
> 부하 분산 장치를 *표준* sku와 함께 수신 하려면 *표준* sku 공용 ip를 사용 해야 합니다. [Az network public ip show][az-network-public-ip-show] 명령을 사용 하 여 공용 IP의 SKU를 확인할 수 있습니다.
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

[Az network 공용-ip show][az-network-public-ip-show] 명령을 사용 하 여 공용 Ip의 id를 나열 합니다.

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

위의 명령은 *Myresourcegroup* 리소스 그룹의 *myPublicIP* 공용 IP에 대 한 ID를 표시 합니다.

Aks *매개 변수와* 함께 *az update* 명령을 사용 하 여 공용 ip로 클러스터를 업데이트 합니다.

다음 예제에서는 이전 명령의 Id를 사용 하 여 *부하 분산 장치-아웃 바운드 ip* 매개 변수를 사용 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*표준* SKU 부하 분산 장치에 대 한 공용 IP 접두사를 사용할 수도 있습니다. 다음 예제에서는 [az network 공용 ip prefix show][az-network-public-ip-prefix-show] 명령을 사용 하 여 공용 ip 접두사의 id를 나열 합니다.

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

위의 명령은 *Myresourcegroup* 리소스 그룹의 *myPublicIPPrefix* 공용 IP 접두사에 대 한 ID를 표시 합니다.

다음 예제에서는 이전 명령의 Id를 사용 하 여 *부하 분산 장치-아웃 바운드 ip 접두사* 매개 변수를 사용 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> 공용 Ip 및 IP 접두사는 AKS 클러스터와 동일한 구독의 일부 및 동일한 지역에 있어야 합니다. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>클러스터를 만들 때 사용자 고유의 공용 IP 또는 접두사를 정의 합니다.

허용 목록 송신 끝점과 같은 시나리오를 지원 하기 위해 클러스터를 만들 때 송신 하기 위해 고유한 IP 주소 또는 IP 접두사를 가져올 수 있습니다. 클러스터 생성 단계에 위에 표시 된 것과 동일한 매개 변수를 추가 하 여 클러스터의 수명 주기 시작 부분에 고유한 공용 ip 및 IP 접두사를 정의 합니다.

Aks *매개 변수와* 함께 *az create* 명령을 사용 하 여 시작 시 공용 ip를 사용 하 여 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

*로드 균형 조정기-* aks 매개 변수와 함께 *az create* 명령을 사용 하 여 시작 시 공용 ip 접두사를 사용 하 여 새 클러스터를 만듭니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

## <a name="configure-outbound-ports-and-idle-timeout"></a>아웃 바운드 포트 및 유휴 시간 제한 구성

> [!WARNING]
> 다음 섹션에서는 더 큰 규모의 네트워킹 또는 기본 구성의 SNAT 소모 문제를 해결 하기 위한 고급 시나리오를 위한 것입니다. 정상 클러스터를 유지 하려면 기본값에서 *AllocatedOutboundPorts* 또는 *IdleTimeoutInMinutes* 를 변경 하기 전에 vm 및 IP 주소에 사용할 수 있는 할당량의 정확한 인벤토리가 있어야 합니다.
> 
> *AllocatedOutboundPorts* 및 *IdleTimeoutInMinutes* 에 대 한 값을 변경 하면 부하 분산 장치에 대 한 아웃 바운드 규칙의 동작이 크게 달라질 수 있습니다. 변경 내용의 영향을 완벽 하 게 이해 하기 위해 이러한 값을 업데이트 하기 전에 [Azure의][azure-lb-outbound-connections] [Load Balancer 아웃 바운드 규칙][azure-lb-outbound-rules-overview], [부하 분산 장치 아웃 바운드 규칙][azure-lb-outbound-rules]및 아웃 바운드 연결을 검토 합니다.

아웃 바운드 할당 된 포트 및 해당 유휴 시간 제한이 [SNAT][azure-lb-outbound-connections]에 사용 됩니다. 기본적으로 *표준* SKU 부하 분산 장치는 백 엔드 풀 크기 및 각 포트의 30 분 유휴 시간 제한 [에 따라 아웃 바운드 포트 수에 자동 할당][azure-lb-outbound-preallocatedports] 을 사용 합니다. 이러한 값을 보려면 [az network lb 아웃 바운드 규칙 목록을][az-network-lb-outbound-rule-list] 사용 하 여 부하 분산 장치에 대 한 아웃 바운드 규칙을 표시 합니다.

```azurecli-interactive
NODE_RG=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az network lb outbound-rule list --resource-group $NODE_RG --lb-name kubernetes -o table
```

이전 명령에는 부하 분산 장치에 대 한 아웃 바운드 규칙이 나열 됩니다. 예를 들면 다음과 같습니다.

```console
AllocatedOutboundPorts    EnableTcpReset    IdleTimeoutInMinutes    Name             Protocol    ProvisioningState    ResourceGroup
------------------------  ----------------  ----------------------  ---------------  ----------  -------------------  -------------
0                         True              30                      aksOutboundRule  All         Succeeded            MC_myResourceGroup_myAKSCluster_eastus  
```

예제 출력에서는 *AllocatedOutboundPorts* 및 *IdleTimeoutInMinutes*의 기본값을 보여 줍니다. *AllocatedOutboundPorts* 에 대 한 값 0은 백 엔드 풀 크기에 따라 아웃 바운드 포트 수에 자동 할당을 사용 하는 아웃 바운드 포트의 수를 설정 합니다. 예를 들어 클러스터에 50 개 이하의 노드가 있는 경우 각 노드에 대 한 1024 포트가 할당 됩니다.

위의 기본 구성에 따라 SNAT 고갈를 발생 시킬 것으로 생각 되는 경우 *allocatedOutboundPorts* 또는 *IdleTimeoutInMinutes* 의 설정을 변경 하는 것이 좋습니다. 각각의 추가 IP 주소는 할당을 위해 64000 추가 포트를 사용할 수 있지만, Azure 표준 Load Balancer는 추가 IP 주소가 추가 될 때 노드당 포트를 자동으로 증가 시 지 않습니다. *부하 분산 장치-아웃 바운드 포트* 및 *부하 분산 장치-유휴 시간 제한* 매개 변수를 설정 하 여 이러한 값을 변경할 수 있습니다. 다음은 그 예입니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

> [!IMPORTANT]
> 연결 또는 크기 조정 문제를 방지 하려면 *allocatedOutboundPorts* 을 사용자 지정 하기 전에 [필요한 할당량을 계산][calculate-required-quota] 해야 합니다. *AllocatedOutboundPorts* 에 대해 지정 하는 값도 8의 배수 여야 합니다.

클러스터를 만들 때 *부하 분산 장치-아웃 바운드 포트* 및 *부하 분산 장치-유휴 시간 제한* 매개 변수를 사용할 수도 있지만, *부하 분산 장치-아웃 바운드-ip 수*, *부하 분산*장치-아웃 바운드- *ip 접두사* 도 지정 해야 합니다.  다음은 그 예입니다.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-managed-outbound-ip-count 2 \
    --load-balancer-outbound-ports 0 \
    --load-balancer-idle-timeout 30
```

*부하 분산 장치-아웃 바운드 포트* 및 *부하 분산 장치-유휴 시간 제한* 매개 변수를 기본값에서 변경 하는 경우 전체 클러스터에 영향을 주는 부하 분산 장치 프로필의 동작에 영향을 줍니다.

### <a name="required-quota-for-customizing-allocatedoutboundports"></a>AllocatedOutboundPorts 사용자 지정에 필요한 할당량
노드 Vm 수 및 원하는 할당 된 아웃 바운드 포트를 기반으로 하는 아웃 바운드 IP 용량이 충분 해야 합니다. 아웃 바운드 IP 용량이 충분 한지 확인 하려면 다음 수식을 사용 합니다. 
 
*outboundIPs* \* 64000 \> *nodevms* \* *desiredAllocatedOutboundPorts*.
 
예를 들어, 3 개의 *Nodevms*및 5만 *desiredAllocatedOutboundPorts*가 있는 경우 *outboundIPs*3 개 이상이 필요 합니다. 필요한 것 보다 더 많은 아웃 바운드 IP 용량을 통합 하는 것이 좋습니다. 또한 아웃 바운드 IP 용량을 계산할 때 클러스터 autoscaler 및 노드 풀 업그레이드 가능성을 고려해 야 합니다. 클러스터 autoscaler의 경우 현재 노드 수와 최대 노드 수를 검토 하 고 더 높은 값을 사용 합니다. 업그레이드 하는 경우 업그레이드를 허용 하는 모든 노드 풀의 추가 노드 VM을 고려 합니다.
 
*IdleTimeoutInMinutes* 를 기본값 30 분이 아닌 다른 값으로 설정 하는 경우 작업에 아웃 바운드 연결이 필요한 기간을 고려 합니다. 또한 AKS 외부에서 사용 되는 *표준* SKU 부하 분산 장치에 대 한 기본 시간 제한 값은 4 분입니다. 특정 AKS 워크 로드를 보다 정확 하 게 반영 하는 *IdleTimeoutInMinutes* 값은 더 이상 사용 되지 않는 연결을 연결 하 여 SNAT 소모를 줄이는 데 도움이 될 수 있습니다.

## <a name="restrict-access-to-specific-ip-ranges"></a>특정 IP 범위에 대 한 액세스 제한

부하 분산 장치에 대 한 가상 네트워크와 연결 된 NSG (네트워크 보안 그룹)에는 기본적으로 모든 인바운드 외부 트래픽을 허용 하는 규칙이 있습니다. 인바운드 트래픽에 대해 특정 IP 범위만 허용 하도록이 규칙을 업데이트할 수 있습니다. 다음 매니페스트에서는 *loadBalancerSourceRanges* 을 사용 하 여 인바운드 외부 트래픽에 대 한 새 IP 범위를 지정 합니다.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
  loadBalancerSourceRanges:
  - MY_EXTERNAL_IP_RANGE
```

위의 예에서는 *MY_EXTERNAL_IP_RANGE* 범위에서 인바운드 외부 트래픽만 허용 하도록 규칙을 업데이트 합니다. 이 방법을 사용 하 여 부하 분산 장치 서비스에 대 한 액세스를 제한 하는 방법에 대 한 자세한 내용은 [Kubernetes 설명서][kubernetes-cloud-provider-firewall]에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Kubernetes services에 대 한 자세한 내용은 [Kubernetes services 설명서][kubernetes-services]를 참조 하세요.

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubernetes-cloud-provider-firewall]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-cloud-provider-firewall/#restrict-access-for-loadbalancer-service
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-network-lb-outbound-rule-list]: /cli/azure/network/lb/outbound-rule?view=azure-cli-latest#az-network-lb-outbound-rule-list
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/concepts-limitations.md#skus
[azure-lb-outbound-rules]: ../load-balancer/load-balancer-outbound-rules-overview.md#snatports
[azure-lb-outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#snat
[azure-lb-outbound-preallocatedports]: ../load-balancer/load-balancer-outbound-connections.md#preallocatedports
[azure-lb-outbound-rules-overview]: ../load-balancer/load-balancer-outbound-rules-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[calculate-required-quota]: #required-quota-for-customizing-allocatedoutboundports
