---
title: AKS(Azure Kubernetes Service)에서 클러스터 자동 크기 조정기 사용
description: 클러스터 자동 크기 조정기를 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기를 조정하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 033cf88e29ba4a9f7ce9397fe216f7380e70be07
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76713389"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞게 자동으로 클러스터 크기 조정

AKS(Azure Kubernetes Service)에서 애플리케이션 수요에 맞추려면 워크로드를 실행하는 노드 수를 조정해야 할 수 있습니다. 클러스터 자동 크기 조정기 구성 요소는 리소스 제약 조건으로 인해 예약할 수 없는 클러스터의 Pod를 확인할 수 있습니다. 문제가 감지 되 면 응용 프로그램 수요를 충족 하기 위해 노드 풀의 노드 수가 늘어납니다. 또한 실행 중인 Pod가 부족한지 노드를 주기적으로 확인하고 필요에 따라 노드 수가 감소합니다. AKS 클러스터에서 노드 수를 자동으로 강화하거나 규모 축소하는 이 기능을 통해 효율적이고 비용 효과적인 클러스터를 실행할 수 있습니다.

이 문서에서는 AKS 클러스터에서 클러스터 자동 크기 조정기를 사용하도록 설정하고 관리하는 방법을 보여 줍니다. 

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 Azure CLI 버전 2.0.76 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="limitations"></a>제한 사항

클러스터 autoscaler를 사용 하는 AKS 클러스터를 만들고 관리 하는 경우 다음과 같은 제한 사항이 적용 됩니다.

* HTTP 응용 프로그램 라우팅 추가 기능을 사용할 수 없습니다.

## <a name="about-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 정보

업무 시간에서 야간 사이에 또는 주말에 변화하는 애플리케이션 수요에 맞게 조정하기 위해 일반적으로 클러스터에는 자동으로 크기를 조정하는 방법이 필요합니다. AKS 클러스터는 다음 두 가지 방법 중 하나로 크기를 조정할 수 있습니다.

* **클러스터 자동 크기 조정기**는 리소스 제약 조건으로 인해 노드에서 예약할 수 없는 Pod를 확인합니다. 그러면 클러스터에서 노드 수가 자동으로 늘어납니다.
* **Horizontal Pod Autoscaler**는 Kubernetes 클러스터에서 메트릭 서버를 사용하여 Pod의 리소스 수요를 모니터링합니다. 응용 프로그램에 더 많은 리소스가 필요한 경우에는 수요에 맞게 pod 수가 자동으로 증가 합니다.

![종종 클러스터 자동 크기 조정기 및 Horizontal Pod Autoscaler가 연동되어 필요한 애플리케이션 수요를 지원함](media/autoscaler/cluster-autoscaler.png)

수평 pod autoscaler 및 cluster autoscaler 모두 필요에 따라 pod 및 노드 수를 줄일 수도 있습니다. 클러스터 자동 크기 조정기는 일정 기간 사용되지 않은 용량이 있는 경우 노드 수를 줄입니다. 클러스터 자동 크기 조정기가 제거할 노드의 Pod는 클러스터의 다른 위치에서 안전하게 예약됩니다. 다음 상황에서처럼 Pod를 이동할 수 없는 경우 클러스터 자동 크기 조정기가 규모를 축소하지 못할 수 있습니다.

* Pod는 직접 생성 되며 배포 또는 복제본 집합과 같은 컨트롤러 개체에 의해 지원 되지 않습니다.
* PDB(Pod Disruption Budget)가 너무 제한적이고 이를 사용하면 Pod 수가 특정 임계값보다 낮아질 수 없습니다.
* Pod는 다른 노드에서 예약된 경우 적용할 수 없는 노드 선택기 또는 선호도 방지를 사용합니다.

클러스터 autoscaler를 축소 하지 못할 수 있는 방법에 대한 자세한 내용은 [어떤 유형의 pod 클러스터 autoscaler 노드 제거를 방해할 수 있나요?][autoscaler-scaledown] 를 참조 하세요.

클러스터 자동 크기 조정기는 크기 조정 이벤트와 리소스 임계값 사이의 시간 간격 같은 항목에 시작 매개 변수를 사용합니다. 이러한 매개 변수는 Azure 플랫폼에 의해 정의되며 현재는 사용자가 조정하도록 표시되지 않습니다. 클러스터 autoscaler 사용 하는 매개 변수에 대한 자세한 내용은 [cluster autoscaler parameters?][autoscaler-parameters]를 참조 하세요.

Cluster 및 수평 pod autoscalers는 함께 작동할 수 있으며, 클러스터에 배포 되는 경우가 많습니다. 결합된 경우 Horizontal Pod Autoscaler는 애플리케이션 수요를 충족하는 데 필요한 개수의 Pod를 실행하는 데 중점을 둡니다. 클러스터 자동 크기 조정기는 예약된 Pod를 지원하는 데 필요한 개수의 노드를 실행하는 데 중점을 둡니다.

> [!NOTE]
> 클러스터 자동 크기 조정기를 사용하면 수동 크기 조정이 사용하지 않도록 설정됩니다. 클러스터 자동 크기 조정기가 필요한 노드 수를 결정하도록 해보겠습니다. 클러스터 크기를 수동으로 조정하려면 [클러스터 자동 크기 조정기를 사용하지 않도록 설정](#disable-the-cluster-autoscaler)합니다.

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>AKS 클러스터를 만들고 클러스터 자동 크기 조정기를 사용하도록 설정

AKS 클러스터를 만들어야 하는 경우 [az AKS create][az-aks-create] 명령을 사용 합니다. 클러스터에 대한 노드 풀에서 클러스터 autoscaler를 사용 하도록 설정 하 고 구성 하려면 *--autoscaler* 매개 변수를 사용 하 고 노드- *-min-count* 및 *--max-count*를 지정 합니다.

> [!IMPORTANT]
> 클러스터 자동 크기 조정기는 Kubernetes 구성 요소입니다. AKS 클러스터는 가상 머신 확장 집합을 노드에 사용하지만, Azure Portal에서 또는 Azure CLI를 사용하여 확장 집합 자동 크기 조정에 대한 설정을 직접 설정하거나 편집하지 마세요. Kubernetes 클러스터 자동 크기 조정기가 필수 크기 조정 설정을 자동으로 관리하게 두세요. 자세한 내용은 [노드 리소스 그룹에서 AKS 리소스를 수정할 수 있나요?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group) 를 참조 하세요.

다음 예제에서는 가상 머신 확장 집합에 의해 지원 되는 단일 노드 풀로 AKS 클러스터를 만듭니다. 또한 클러스터의 노드 풀에서 클러스터 autoscaler를 사용 하도록 설정 하 고 최소 *1 개* 에서 최대 *3* 개의 노드를 설정 합니다.

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

클러스터를 만들고 클러스터 자동 크기 조정기 설정을 구성하는 데 몇 분 정도 걸립니다.

## <a name="change-the-cluster-autoscaler-settings"></a>클러스터 자동 크기 조정기 설정 변경

> [!IMPORTANT]
> AKS 클러스터에 여러 노드 풀이 있는 경우 [여러 에이전트 풀을 사용 하 여 자동 크기 조정 섹션](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)으로 건너뜁니다. 여러 에이전트 풀을 포함 하는 클러스터는 `az aks nodepool` 명령 집합을 사용 하 여 `az aks`대신 노드 풀 특정 속성을 변경 해야 합니다.

이전 단계에서 AKS 클러스터를 만들거나 기존 노드 풀을 업데이트 하려면 클러스터 autoscaler 최소 노드 수를 *1*로 설정 하 고 최대 노드 수를 *3*으로 설정 했습니다. 애플리케이션 수요가 변경되면 클러스터 자동 크기 조정기의 노드 수를 조정해야 할 수 있습니다.

노드 수를 변경 하려면 [az aks update][az-aks-update] 명령을 사용 합니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

위의 예에서는 *myAKSCluster* 의 단일 노드 풀에 있는 클러스터 autoscaler를 최소 *1* 개에서 최대 *5* 개의 노드로 업데이트 합니다.

> [!NOTE]
> 현재 노드 풀에 대해 설정 된 것 보다 높은 최소 노드 수를 설정할 수 없습니다. 예를 들어 현재 최소 개수가 *1*로 설정되어 있으면 최소 개수를 *3*으로 업데이트할 수 없습니다.

애플리케이션 및 서비스의 성능을 모니터링하고 클러스터 자동 크기 조정기 노드 수를 필요한 성능과 일치하도록 조정합니다.

## <a name="disable-the-cluster-autoscaler"></a>클러스터 자동 크기 조정기 사용 안 함

클러스터 autoscaler을 더 이상 사용 하지 않으려면 [az aks update][az-aks-update] 명령을 사용 하 여 *--autoscaler* 매개 변수를 지정 하 여 사용 하지 않도록 설정할 수 있습니다. 클러스터 자동 크기 조정기가 사용하지 않도록 설정되면 노드가 제거되지 않습니다.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

[Az aks scale][az-aks-scale] 명령을 사용 하 여 클러스터 autoscaler를 사용 하지 않도록 설정한 후 수동으로 클러스터를 확장할 수 있습니다. 수평 pod autoscaler를 사용 하는 경우이 기능은 클러스터 autoscaler 사용 하지 않도록 설정 된 상태에서 계속 실행 되지만, 모든 노드 리소스가 사용 중이면 pod를 예약할 수 없습니다.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>사용 하지 않도록 설정 된 클러스터 autoscaler 다시 사용

기존 클러스터에서 클러스터 autoscaler를 다시 사용 하도록 설정 하려는 경우 [az aks update][az-aks-update] 명령을 사용 하 여 *--enable-autoscaler*, *--min-count*및 *--max-count* 매개 변수를 지정 하 여 다시 사용 하도록 설정할 수 있습니다.

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>클러스터 autoscaler 로그 및 상태 검색

Autoscaler 이벤트를 진단 하 고 디버그 하려면 autoscaler 추가 기능에서 로그 및 상태를 검색할 수 있습니다.

AKS는 사용자를 대신 하 여 클러스터 autoscaler를 관리 하 고 관리 되는 제어 평면에서 실행 합니다. 마스터 노드 로그를 결과로 표시 되도록 구성 해야 합니다.

Autoscaler 클러스터에서 푸시 되도록 로그를 구성 하려면 다음 단계를 수행 Log Analytics 합니다.

1. Log Analytics에 클러스터 autoscaler 로그를 푸시하는 진단 로그에 대한 규칙을 설정 합니다. [지침은 여기에 자세히 설명 되어](https://docs.microsoft.com/azure/aks/view-master-logs#enable-diagnostics-logs)있습니다. "로그" 옵션을 선택 하는 경우 `cluster-autoscaler`의 확인란을 선택 합니다.
1. Azure Portal를 통해 클러스터에서 "로그" 섹션을 클릭 합니다.
1. 다음 예제를 입력 하 Log Analytics에 쿼리 합니다.

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

검색할 로그가 있는 경우 반환 되는 것과 유사한 로그가 표시 됩니다.

![Log Analytics 로그](media/autoscaler/autoscaler-logs.png)

또한 클러스터 autoscaler는 `cluster-autoscaler-status`이라는 configmap에 상태를 기록 합니다. 이러한 로그를 검색 하려면 다음 `kubectl` 명령을 실행 합니다. 클러스터 autoscaler 구성 된 각 노드 풀에 대해 상태가 보고 됩니다.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Autoscaler에서 로깅되는 내용에 대한 자세한 내용은 [Kubernetes/Autoscaler GitHub 프로젝트](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why)에 대한 FAQ를 참조 하세요.

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>여러 노드 풀을 사용할 수 있는 클러스터 autoscaler 사용

클러스터 autoscaler는 [여러 노드 풀](use-multiple-node-pools.md) 을 사용할 수 있는 경우 함께 사용할 수 있습니다. 이 문서에 따라 여러 노드 풀을 사용 하도록 설정 하 고 기존 클러스터에 노드 풀을 더 추가 하는 방법을 알아봅니다. 두 기능을 함께 사용 하는 경우 클러스터의 각 개별 노드 풀에서 클러스터 autoscaler를 사용 하도록 설정 하 고 각각에 고유한 자동 크기 조정 규칙을 전달할 수 있습니다.

아래 명령에서는이 문서의 앞부분에 있는 [초기 지침](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) 을 따르고 기존 노드 풀의 최대 개수를 *3* 에서 *5*로 업데이트 하려고 한다고 가정 합니다. [Az aks nodepool update][az-aks-nodepool-update] 명령을 사용 하 여 기존 노드 풀의 설정을 업데이트 합니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

[Az aks nodepool update][az-aks-nodepool-update] 를 사용 하 여 클러스터 autoscaler를 사용 하지 않도록 설정 하 고 `--disable-cluster-autoscaler` 매개 변수를 전달할 수 있습니다.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

기존 클러스터에서 클러스터 autoscaler를 다시 사용 하도록 설정 하려는 경우 [az aks nodepool update][az-aks-nodepool-update] 명령을 사용 하 여 *--enable-autoscaler*, *--min-count*및 *--max-count* 매개 변수를 지정 하 여 다시 사용 하도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 노드 수를 자동으로 조정하는 방법을 설명했습니다. Horizontal Pod Autoscaler를 사용하여 애플리케이션을 실행하는 Pod 수를 자동으로 조정할 수도 있습니다. 수평 pod autoscaler 사용에 대한 단계는 [AKS에서 응용 프로그램 크기 조정][aks-scale-apps]을 참조 하세요.

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
