---
title: Azure Kubernetes Service에서 여러 노드 풀 사용 (AKS)
description: Azure Kubernetes 서비스 (AKS)에서 클러스터에 대 한 여러 노드 풀을 만들고 관리 하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/22/2020
ms.author: mlearned
ms.openlocfilehash: bbfb65c31bf6fd46cc18c9eee66086afbbff1d5f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157977"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 클러스터에 대 한 여러 노드 풀 만들기 및 관리

Azure Kubernetes 서비스 (AKS)에서 동일한 구성의 노드는 *노드 풀*로 그룹화 됩니다. 이러한 노드 풀에는 응용 프로그램을 실행 하는 기본 Vm이 포함 됩니다. 초기 노드 수와 해당 크기 (SKU)는 *기본 노드 풀*을 만드는 AKS 클러스터를 만들 때 정의 됩니다. 계산 또는 저장소 요구가 다른 응용 프로그램을 지원 하기 위해 추가 노드 풀을 만들 수 있습니다. 예를 들어 이러한 추가 노드 풀을 사용 하 여 계산 집약적인 응용 프로그램을 위한 Gpu를 제공 하거나 고성능 SSD 저장소에 액세스할 수 있습니다.

> [!NOTE]
> 이 기능을 사용 하면 여러 노드 풀을 만들고 관리 하는 방법을 보다 효과적으로 제어할 수 있습니다. 따라서 create/update/delete에 별도의 명령이 필요 합니다. 이전에 `az aks create` 또는 `az aks update`를 통한 클러스터 작업에서 managedCluster API를 사용 했으며,이 옵션은 제어 평면과 단일 노드 풀을 변경 하는 유일한 옵션 이었습니다. 이 기능은 agentPool API를 통해 에이전트 풀에 대 한 별도의 작업 집합을 노출 하 고 개별 노드 풀에서 작업을 실행 하려면 `az aks nodepool` 명령 집합을 사용 해야 합니다.

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.76 이상이 설치 및 구성 되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="limitations"></a>제한 사항

여러 노드 풀을 지 원하는 AKS 클러스터를 만들고 관리 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* [Azure Kubernetes 서비스 (AKS)의 할당량, 가상 머신 크기 제한 및 지역 가용성][quotas-skus-regions]을 참조 하세요.
* 기본 (첫 번째) 노드 풀은 삭제할 수 없습니다.
* HTTP 응용 프로그램 라우팅 추가 기능을 사용할 수 없습니다.
* AKS 클러스터는 표준 SKU 부하 분산 장치를 사용 하 여 여러 노드 풀을 사용 해야 합니다 .이 기능은 기본 SKU 부하 분산 장치에서 지원 되지 않습니다.
* AKS 클러스터는 노드에 대 한 가상 머신 확장 집합을 사용 해야 합니다.
* 노드 풀의 이름에는 소문자 영숫자만 사용할 수 있으며 소문자 문자로 시작 해야 합니다. Linux 노드 풀의 경우 길이는 1 자에서 12 자 사이 여야 하 고 Windows 노드 풀의 길이는 1에서 6 자 사이 여야 합니다.
* 모든 노드 풀은 동일한 vnet 및 서브넷에 있어야 합니다.
* 클러스터를 만들 때 여러 노드 풀을 만들 때 노드 풀에서 사용 하는 모든 Kubernetes 버전은 제어 평면에 대해 설정 된 버전과 일치 해야 합니다. 이는 클러스터를 프로 비전 한 후 노드당 풀 작업을 사용 하 여 업데이트할 수 있습니다.

## <a name="create-an-aks-cluster"></a>AKS 클러스터 만들기

시작 하려면 단일 노드 풀로 AKS 클러스터를 만듭니다. 다음 예제에서는 [az group create][az-group-create] 명령을 사용 하 여 *에서는 eastus* 지역에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다. 그런 다음 *myAKSCluster* 라는 AKS 클러스터가 [az AKS create][az-aks-create] 명령을 사용 하 여 만들어집니다. *1.15.7* 의 *kubernetes 버전* 은 다음 단계에서 노드 풀을 업데이트 하는 방법을 보여 주는 데 사용 됩니다. [지원 되는 Kubernetes 버전][supported-versions]을 지정할 수 있습니다.

> [!NOTE]
> 여러 노드 풀을 사용 하는 경우 *기본* 부하 분산 장치 SKU가 **지원 되지 않습니다** . 기본적으로 AKS 클러스터는 Azure CLI 및 Azure Portal에서 *표준* 부하 분산 장치 SKU를 사용 하 여 만들어집니다.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

클러스터를 만드는 데 몇 분이 걸립니다.

> [!NOTE]
> 클러스터가 안정적으로 작동 하도록 하려면이 노드 풀에서 필수적인 시스템 서비스가 실행 되 고 있으므로 기본 노드 풀에서 2 개 이상의 노드를 실행 해야 합니다.

클러스터가 준비 되 면 [az aks][az-aks-get-credentials] 명령을 사용 하 여 `kubectl`와 함께 사용할 클러스터 자격 증명을 가져옵니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>노드 풀 추가

이전 단계에서 만든 클러스터에는 단일 노드 풀이 있습니다. [Az aks nodepool add][az-aks-nodepool-add] 명령을 사용 하 여 두 번째 노드 풀을 추가 해 보겠습니다. 다음 예에서는 *3 개의* 노드를 실행 하는 *mynodepool* 이라는 노드 풀을 만듭니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> 노드 풀의 이름은 소문자로 시작 해야 하며 영숫자 문자만 포함할 수 있습니다. Linux 노드 풀의 경우 길이는 1 자에서 12 자 사이 여야 하 고 Windows 노드 풀의 길이는 1에서 6 자 사이 여야 합니다.

노드 풀의 상태를 확인 하려면 [az aks node pool list][az-aks-nodepool-list] 명령을 사용 하 여 리소스 그룹 및 클러스터 이름을 지정 합니다.

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

다음 예제 출력은 노드 풀에서 3 개의 노드를 사용 하 여 *mynodepool* 을 성공적으로 만들었음을 보여 줍니다. 이전 단계에서 AKS 클러스터를 만든 경우 기본 *nodepool1* 노드 수를 *2*로 만들었습니다.

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 노드 풀을 추가할 때 *Vmsize* 가 지정 되지 않은 경우 기본 크기는 Windows 노드 풀의 경우 *Standard_DS2_v3* 이 고 Linux 노드 풀의 경우 *Standard_DS2_v2* 입니다. *OrchestratorVersion* 가 지정 되지 않은 경우 기본값은 컨트롤 평면과 동일한 버전입니다.

## <a name="upgrade-a-node-pool"></a>노드 풀 업그레이드

> [!NOTE]
> 클러스터 또는 노드 풀에서 업그레이드 및 크기 조정 작업을 동시에 수행할 수 없습니다. 시도 하면 오류가 반환 됩니다. 대신, 동일한 리소스에 대 한 다음 요청 전에 대상 리소스에서 각 작업 유형이 완료 되어야 합니다. 이에 대 한 자세한 내용은 [문제 해결 가이드](https://aka.ms/aks-pending-upgrade)를 참조 하세요.

첫 번째 단계에서 AKS 클러스터를 처음 만들 때 *1.15.7* 의 `--kubernetes-version` 지정 되었습니다. 그러면 제어 평면과 기본 노드 풀 모두에 대해 Kubernetes 버전이 설정 됩니다. 이 섹션의 명령은 단일 특정 노드 풀을 업그레이드 하는 방법에 대해 설명 합니다.

Kubernetes 버전의 제어 평면과 노드 풀을 업그레이드 하는 경우의 관계는 [아래 섹션](#upgrade-a-cluster-control-plane-with-multiple-node-pools)에 설명 되어 있습니다.

> [!NOTE]
> 노드 풀 OS 이미지 버전은 클러스터의 Kubernetes 버전에 연결 됩니다. 클러스터 업그레이드 후에는 OS 이미지 업그레이드만 가져옵니다.

이 예제에서는 두 개의 노드 풀이 있으므로 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 를 사용 하 여 노드 풀을 업그레이드 해야 합니다. *Mynodepool* 을 Kubernetes *1.15.7*로 업그레이드 하겠습니다. 다음 예제와 같이 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 명령을 사용 하 여 노드 풀을 업그레이드 합니다.

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

[Az aks node pool list][az-aks-nodepool-list] 명령을 사용 하 여 노드 풀의 상태를 다시 나열 합니다. 다음 예에서는 *mynodepool* 이 *1.15.7*에 대 한 *업그레이드* 상태임을 보여 줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드를 지정 된 버전으로 업그레이드 하는 데 몇 분 정도 걸립니다.

AKS 클러스터의 모든 노드 풀을 동일한 Kubernetes 버전으로 업그레이드 하는 것이 가장 좋습니다. `az aks upgrade`의 기본 동작은이 맞춤을 얻기 위해 모든 노드 풀을 제어 평면과 함께 업그레이드 하는 것입니다. 개별 노드 풀을 업그레이드 하는 기능을 사용 하면 롤링 업그레이드를 수행 하 고 노드 풀 간에 pod 일정을 예약 하 여 위에서 언급 한 제약 조건 내에서 응용 프로그램 작동 시간을 유지할 수 있습니다

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>여러 노드 풀을 사용 하 여 클러스터 제어 평면 업그레이드

> [!NOTE]
> Kubernetes는 표준 [의미 체계 버전](https://semver.org/) 관리 체계를 사용 합니다. 버전 번호는 *x-y*로 표시 됩니다. 여기서 *x* 는 주 버전이 고 *y* 는 부 버전이 며 *z* 는 패치 버전입니다. 예를 들어 버전 *1.12.6*에서 1은 주 버전이 고 12는 부 버전 이며 6은 패치 버전입니다. 제어 평면과 초기 노드 풀의 Kubernetes 버전은 클러스터를 만드는 동안 설정 됩니다. 모든 추가 노드 풀은 클러스터에 추가 될 때 해당 Kubernetes 버전을 설정 합니다. Kubernetes 버전은 노드 풀과 노드 풀 및 제어 평면 사이에서 다를 수 있습니다.

AKS 클러스터에는 Kubernetes 버전이 연결 된 두 개의 클러스터 리소스 개체가 있습니다.

1. 클러스터 제어 평면 Kubernetes 버전입니다.
2. Kubernetes 버전을 포함 하는 노드 풀

컨트롤 평면은 하나 이상의 노드 풀에 매핑됩니다. 업그레이드 작업의 동작은 사용 되는 Azure CLI 명령에 따라 달라 집니다.

AKS 제어 평면을 업그레이드 하려면 `az aks upgrade`를 사용 해야 합니다. 그러면 클러스터에 있는 모든 노드 풀 및 제어 평면 버전이 업그레이드 됩니다. 

`--control-plane-only` 플래그를 사용 하 여 `az aks upgrade` 명령을 실행 하면 클러스터 제어 평면만 업그레이드 됩니다. 클러스터에 연결 된 노드 풀이 모두 변경 되지 않았습니다.

개별 노드 풀을 업그레이드 하려면 `az aks nodepool upgrade`를 사용 해야 합니다. 지정 된 Kubernetes 버전을 사용 하 여 대상 노드 풀만 업그레이드 합니다.

### <a name="validation-rules-for-upgrades"></a>업그레이드에 대 한 유효성 검사 규칙

클러스터의 제어 평면과 노드 풀에 대 한 유효한 Kubernetes 업그레이드는 다음 규칙 집합에 의해 유효성이 검사 됩니다.

* 노드 풀을 업그레이드 하는 데 유효한 버전에 대 한 규칙:
   * 노드 풀 버전은 제어 평면과 동일한 *주* 버전을 포함 해야 합니다.
   * 노드 풀 *부* 버전은 제어 평면 버전의 두 *부* 버전 내에 있어야 합니다.
   * 노드 풀 버전은 컨트롤 `major.minor.patch` 버전 보다 클 수 없습니다.

* 업그레이드 작업을 제출 하기 위한 규칙:
   * 제어 평면이 나 노드 풀 Kubernetes 버전을 다운 그레이드할 수 없습니다.
   * Node pool Kubernetes version을 지정 하지 않은 경우 동작은 사용 되는 클라이언트에 따라 달라 집니다. 리소스 관리자 템플릿의 선언은 사용 되는 경우 노드 풀에 대해 정의 된 기존 버전으로 대체 됩니다. 설정 되지 않은 경우에는 제어 평면 버전이 사용 됩니다.
   * 지정 된 시간에 제어 평면 또는 노드 풀을 업그레이드 하거나 크기를 조정할 수 있습니다. 단일 제어 평면이 나 노드 풀 리소스에 대해 동시에 여러 작업을 제출할 수 없습니다.

## <a name="scale-a-node-pool-manually"></a>수동으로 노드 풀 크기 조정

응용 프로그램 워크 로드 요구가 변경 됨에 따라 노드 풀의 노드 수를 조정 해야 할 수 있습니다. 노드 수를 확장 하거나 축소할 수 있습니다.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

노드 풀의 노드 수를 조정 하려면 [az aks node pool scale][az-aks-nodepool-scale] 명령을 사용 합니다. 다음 예에서는 *mynodepool* 의 노드 수를 *5*로 조정 합니다.

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

[Az aks node pool list][az-aks-nodepool-list] 명령을 사용 하 여 노드 풀의 상태를 다시 나열 합니다. 다음 예에서는 *mynodepool* 이 새 개수의 *5 개* 노드를 사용 하 여 *크기 조정* 상태에 있음을 보여 줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

크기 조정 작업을 완료 하는 데 몇 분이 걸립니다.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>클러스터 autoscaler를 사용 하도록 설정 하 여 특정 노드 풀의 크기를 자동으로 조정 합니다.

AKS는 [cluster autoscaler](cluster-autoscaler.md)라는 기능을 사용 하 여 노드 풀의 크기를 자동으로 조정 하는 별도의 기능을 제공 합니다. 이 기능은 노드 풀 당 고유한 최소 및 최대 소수 자릿수를 사용 하 여 노드 풀 당 사용 하도록 설정할 수 있습니다. [노드 풀 당 클러스터 autoscaler를 사용](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)하는 방법을 알아봅니다.

## <a name="delete-a-node-pool"></a>노드 풀 삭제

풀이 더 이상 필요 하지 않은 경우 삭제 하 고 기본 VM 노드를 제거할 수 있습니다. 노드 풀을 삭제 하려면 [az aks node pool delete][az-aks-nodepool-delete] 명령을 사용 하 여 노드 풀 이름을 지정 합니다. 다음 예에서는 이전 단계에서 만든 *mynoodepool* 를 삭제 합니다.

> [!CAUTION]
> 노드 풀을 삭제할 때 발생할 수 있는 데이터 손실에 대 한 복구 옵션은 없습니다. 다른 노드 풀에서 pod을 예약할 수 없는 경우 해당 응용 프로그램을 사용할 수 없습니다. 사용 중인 응용 프로그램에 데이터 백업이 없거나 클러스터의 다른 노드 풀에서 실행할 수 없는 경우 노드 풀을 삭제 하지 않아야 합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

[Az aks node pool list][az-aks-nodepool-list] 명령에서 출력 하는 다음 예제에서는 *Mynodepool* 이 *삭제* 상태임을 보여 줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

노드 및 노드 풀을 삭제 하는 데 몇 분 정도 걸립니다.

## <a name="specify-a-vm-size-for-a-node-pool"></a>노드 풀의 VM 크기 지정

이전 예제에서 노드 풀을 만드는 경우 클러스터에서 만든 노드에 기본 VM 크기가 사용 되었습니다. 보다 일반적인 시나리오는 VM 크기 및 기능이 서로 다른 노드 풀을 만드는 것입니다. 예를 들어 많은 양의 CPU 또는 메모리를 포함 하는 노드 또는 GPU 지원을 제공 하는 노드 풀을 포함 하는 노드 풀을 만들 수 있습니다. 다음 단계에서는 [taints 및 tolerations를 사용](#schedule-pods-using-taints-and-tolerations) 하 여 이러한 노드에서 실행 될 수 있는 pod에 대 한 액세스를 제한 하는 방법을 Kubernetes scheduler에 지시 합니다.

다음 예제에서는 *Standard_NC6* VM 크기를 사용 하는 GPU 기반 노드 풀을 만듭니다. 이러한 Vm은 NVIDIA Tesla K80 카드에 의해 구동 됩니다. 사용 가능한 VM 크기에 대 한 자세한 내용은 [Azure의 Linux 가상 머신에 대 한 크기][vm-sizes]를 참조 하세요.

[Az aks node pool add][az-aks-nodepool-add] 명령을 사용 하 여 노드 풀을 만듭니다. 이번에는 *gpunodepool*이름을 지정 하 고 `--node-vm-size` 매개 변수를 사용 하 여 *Standard_NC6* 크기를 지정 합니다.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[Az aks node pool list][az-aks-nodepool-list] 명령의 다음 예제 출력에서는 *gpunodepool* 가 지정 된 *vmsize*를 사용 하 여 노드를 *생성* 하 고 있음을 보여 줍니다.

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

*Gpunodepool* 성공적으로 생성 되는 데 몇 분 정도 걸립니다.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Taints 및 tolerations를 사용 하 여 pod 예약

이제 클러스터에 두 개의 노드 풀 (처음에 만든 기본 노드 풀 및 GPU 기반 노드 풀)이 있습니다. [Kubectl 노드 가져오기][kubectl-get] 명령을 사용 하 여 클러스터의 노드를 볼 수 있습니다. 다음 예제 출력에서는 노드를 보여 줍니다.

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes 스케줄러는 taint 및 toleration을 사용하여 노드에서 실행할 수 있는 워크로드를 제한할 수 있습니다.

* **Taint**는 노드에서 특정 Pod만 예약할 수 있음을 나타내는 노드에 적용됩니다.
* **Toleration**은 노드의 오류를 ‘허용’할 수 있도록 하는 Pod에 적용됩니다.

고급 Kubernetes 예약 기능을 사용 하는 방법에 대 한 자세한 내용은 [AKS의 advanced scheduler 기능 모범 사례][taints-tolerations] 를 참조 하세요.

이 예에서는--taints 명령을 사용 하 여 GPU 기반 노드에 taint을 적용 합니다. 이전 `kubectl get nodes` 명령의 출력에서 GPU 기반 노드의 이름을 지정 합니다. Taint는 *키: 값* 으로 적용 된 다음 일정 옵션으로 적용 됩니다. 다음 예제에서는 *sku = gpu* 쌍을 사용 하 고, 그렇지 않은 경우 *noschedule* 기능이 있는 pod를 정의 합니다.

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

다음 기본 예제 YAML 매니페스트는 toleration를 사용 하 여 Kubernetes scheduler가 GPU 기반 노드에서 NGINX pod를 실행 하도록 허용 합니다. MNIST 데이터 집합에 대해 Tensorflow 작업을 실행 하는 보다 적절 하 고 시간이 많이 걸리는 예제는 [AKS에서 계산 집약적인 작업에 Gpu 사용][gpu-cluster]을 참조 하세요.

`gpu-toleration.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

`kubectl apply -f gpu-toleration.yaml` 명령을 사용 하 여 pod를 예약 합니다.

```console
kubectl apply -f gpu-toleration.yaml
```

Pod를 예약 하 고 NGINX 이미지를 풀 하는 데 몇 초 정도 걸립니다. [Kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 pod 상태를 확인 합니다. 다음 압축 예제 출력에서는 *sku = gpu: NoSchedule* toleration이 적용 된 것을 보여 줍니다. 이벤트 섹션에서 스케줄러는 *aks-gpunodepool-28993262-vmss000000* GPU 기반 노드에 pod를 할당 했습니다.

```console
$ kubectl describe pod mypod

[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

이 taint가 적용 된 pod만 *gpunodepool*의 노드에서 예약할 수 있습니다. 다른 pod는 *nodepool1* 노드 풀에서 예약 됩니다. 추가 노드 풀을 만드는 경우 추가 taints 및 tolerations를 사용 하 여 해당 노드 리소스에서 예약할 수 있는 pod를 제한할 수 있습니다.

## <a name="manage-node-pools-using-a-resource-manager-template"></a>리소스 관리자 템플릿을 사용 하 여 노드 풀 관리

Azure Resource Manager 템플릿을 사용 하 여 리소스를 만들고 관리 하는 경우 일반적으로 템플릿에서 설정을 업데이트 하 고 다시 배포 하 여 리소스를 업데이트할 수 있습니다. AKS의 노드 풀을 사용 하면 AKS 클러스터가 생성 된 후 초기 노드 풀 프로필을 업데이트할 수 없습니다. 이 동작은 기존 리소스 관리자 템플릿을 업데이트 하 고, 노드 풀을 변경 하 고, 다시 배포할 수 없음을 의미 합니다. 대신, 기존 AKS 클러스터에 대 한 노드 풀만 업데이트 하는 별도의 리소스 관리자 템플릿을 만들어야 합니다.

`aks-agentpools.json`와 같은 템플릿을 만들고 다음 예제 매니페스트를 붙여 넣습니다. 이 예제 템플릿은 다음 설정을 구성 합니다.

* 3 개의 노드를 실행 하도록 *myagentpool* 이라는 *Linux* 노드 풀을 업데이트 합니다.
* Kubernetes version *1.15.7*를 실행 하도록 노드 풀의 노드를 설정 합니다.
* 노드 크기를 *Standard_DS2_v2*으로 정의 합니다.

필요에 따라 노드 풀을 업데이트, 추가 또는 삭제 해야 하는 경우 이러한 값을 편집 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

다음 예제와 같이 [az group deployment create][az-group-deployment-create] 명령을 사용 하 여이 템플릿을 배포 합니다. 기존 AKS 클러스터 이름 및 위치를 묻는 메시지가 표시 됩니다.

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 다음 예제와 같이 템플릿에 *태그* 속성을 추가 하 여 노드 풀에 태그를 추가할 수 있습니다.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

리소스 관리자 템플릿에서 정의한 노드 풀 설정 및 작업에 따라 AKS 클러스터를 업데이트 하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>노드 풀에서 노드 당 공용 IP 할당

> [!WARNING]
> 노드 당 공용 IP를 할당 하는 미리 보기 중에 VM 프로 비전과 충돌 하는 가능한 부하 분산 장치 규칙으로 인해 *AKS의 표준 LOAD BALANCER SKU* 와 함께 사용할 수 없습니다. 이러한 제한으로 인해 Windows 에이전트 풀은이 미리 보기 기능에서 지원 되지 않습니다. 미리 보기 중에는 노드당 공용 IP를 할당 해야 하는 경우 *기본 LOAD BALANCER SKU* 를 사용 해야 합니다.

AKS 노드에는 통신에 고유한 공용 IP 주소가 필요 하지 않습니다. 그러나 경우에 따라 노드 풀의 노드에 고유한 공용 IP 주소가 있어야 합니다. 예를 들어 콘솔에서 클라우드 가상 컴퓨터에 직접 연결 하 여 홉을 최소화 해야 하는 게임이 있습니다. 이는 별도의 미리 보기 기능인 노드 공용 IP (미리 보기)를 등록 하 여 수행할 수 있습니다.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

등록에 성공 하면 [위와](#manage-node-pools-using-a-resource-manager-template) 동일한 지침에 따라 Azure Resource Manager 템플릿을 배포 하 고 agentpoolprofiles에 `enableNodePublicIP` 부울 값 속성을 추가 합니다. 값을 기본적으로 `true`로 설정 합니다. 지정 하지 않으면 `false`로 설정 됩니다. 이는 생성 시간 전용 속성 이며 최소 API 버전 2019-06-01이 필요 합니다. 이는 Linux 및 Windows 노드 풀 모두에 적용할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 GPU 기반 노드를 포함 하는 AKS 클러스터를 만들었습니다. 불필요 한 비용을 줄이기 위해 *gpunodepool*또는 전체 AKS 클러스터를 삭제할 수 있습니다.

GPU 기반 노드 풀을 삭제 하려면 다음 예제와 같이 [az aks nodepool delete][az-aks-nodepool-delete] 명령을 사용 합니다.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

클러스터 자체를 삭제 하려면 [az group delete][az-group-delete] 명령을 사용 하 여 AKS 리소스 그룹을 삭제 합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터에서 여러 노드 풀을 만들고 관리 하는 방법을 배웠습니다. 노드 풀에서 pod을 제어 하는 방법에 대 한 자세한 내용은 [AKS의 advanced scheduler 기능 모범 사례][operator-best-practices-advanced-scheduler]를 참조 하세요.

Windows Server 컨테이너 노드 풀을 만들고 사용 하려면 [AKS에서 Windows server 컨테이너 만들기][aks-windows]를 참조 하세요.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[quotas-skus-regions]: quotas-skus-regions.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
