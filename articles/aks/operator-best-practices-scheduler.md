---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Service)의 기본 스케줄러 기능
description: AKS(Azure Kubernetes Service)의 리소스 할당량 및 Pod 중단 예산과 같은 기본 스케줄러 기능 사용에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 3661f435b5c2dd88aa8e17ca396f9af43aea5224
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293609"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 기본 스케줄러 기능 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 경우 종종 팀 및 워크로드를 격리해야 합니다. Kubernetes 스케줄러는 컴퓨팅 리소스의 배포를 제어하거나 유지 관리 이벤트의 영향을 제한할 수 있는 기능을 제공합니다.

이 모범 사례 문서에서는 클러스터 운영자의 기본 Kubernetes 예약 기능을 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 리소스 할당량을 사용하여 팀 또는 워크로드에 고정된 리소스 양 제공
> * Pod 중단 예산을 사용하여 예약된 유지 관리의 영향 제한
> * `kube-advisor` 도구를 사용하여 누락된 Pod 리소스 요청 및 한도 확인

## <a name="enforce-resource-quotas"></a>리소스 할당량 적용

**모범 사례 가이드** - 네임스페이스 수준에서 리소스 할당량을 계획하고 적용합니다. Pod가 리소스 요청 및 한도를 정의하지 않는 경우 배포를 거부합니다. 리소스 사용량을 모니터링하고 필요에 따라 할당량을 조정합니다.

리소스 요청 및 한도는 Pod 사양에 배치됩니다. 이러한 한도는 배포 시 Kubernetes 스케줄러가 클러스터에서 사용 가능한 노드를 찾는 데 사용됩니다. 이러한 한도 및 요청은 개별 Pod 수준에서 작동합니다. 이러한 값을 정의 하는 방법에 대한 자세한 내용은 [pod 리소스 요청 및 제한 정의][resource-limits] 를 참조 하세요.

개발 팀 또는 프로젝트에서 리소스를 예약하고 제한하는 방법을 제공하려면 ‘리소스 할당량’을 사용해야 합니다. 이러한 할당량은 네임스페이스에 정의되며 다음 기준으로 할당량을 설정하는 데 사용할 수 있습니다.

* CPU 및 메모리 또는 GPU와 같은 **컴퓨팅 리소스**.
* **스토리지 리소스**에는 지정된 스토리지 클래스에 대한 총 볼륨 또는 디스크 공간이 포함됩니다.
* 최대 비밀, 서비스 또는 작업 수와 같은 **개체 개수**를 만들 수 있습니다.

Kubernetes는 리소스를 초과 커밋하지 않습니다. 리소스 요청 또는 한도의 누적 총계가 지정된 할당량을 초과하면 추가 배포가 수행되지 않습니다.

리소스 할당량을 정의하는 경우 네임스페이스에 만들어진 모든 Pod는 해당 Pod 사양의 한도 또는 요청을 제공해야 합니다. 이러한 값이 제공되지 않으면 배포를 거부할 수 있습니다. 대신 [네임 스페이스에 대한 기본 요청 및 제한을 구성할][configure-default-quotas]수 있습니다.

다음 예제에서는 *dev-app-team-quotas.yaml*이라는 YAML 매니페스트가 엄격한 한도로 총 CPU *10*개, 메모리 *20Gi* 및 Pod *10*개를 설정합니다.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

*dev-apps*와 같은 네임스페이스를 지정하여 이 리소스 할당량을 적용할 수 있습니다.

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

애플리케이션 개발자 및 소유자와 협력하여 요구 사항을 이해하고 적절한 리소스 할당량을 적용하세요.

사용 가능한 리소스 개체, 범위 및 우선 순위에 대한 자세한 내용은 [Kubernetes의 리소스 할당량][k8s-resource-quotas]을 참조 하세요.

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Pod 중단 예산을 사용하여 가용성 계획

**모범 사례 가이드** - 애플리케이션 가용성을 유지 관리하려면 PDB(Pod 중단 예산)를 정의하여 클러스터에서 최소한의 Pod를 사용할 수 있도록 합니다.

Pod를 제거하는 두 개의 중단 이벤트가 있습니다.

* ‘비자발적 중단’은 클러스터 운영자 또는 애플리케이션 소유자의 일반적인 제어를 벗어나는 이벤트입니다.
  * 이러한 비자발적 중단에는 물리적 머신의 하드웨어 오류, 커널 패닉 또는 노드 VM 삭제가 포함됩니다.
* ‘자발적 중단’은 클러스터 운영자 또는 애플리케이션 소유자가 요청한 이벤트입니다.
  * 이러한 자발적 중단에는 클러스터 업그레이드, 업데이트된 배포 템플릿 또는 우발적인 Pod 삭제가 포함됩니다.

비자발적 중단은 배포에서 여러 Pod 복제본을 사용하여 완화할 수 있습니다. 또한 AKS 클러스터에서 여러 노드를 실행하면 이러한 비자발적 중단에 도움이 됩니다. 자발적 중단의 경우 Kubernetes는 클러스터 운영자가 최소 사용 가능 리소스 수 또는 최대 사용 불가능 리소스 수를 정의할 수 있는 ‘Pod 중단 예산’을 제공합니다. 이러한 Pod 중단 예산을 사용하면 자발적 중단 이벤트가 발생할 때 배포 또는 복제본 세트가 응답하는 방식을 계획할 수 있습니다.

클러스터가 업그레이드되거나 배포 템플릿이 업데이트되는 경우 Kubernetes 스케줄러는 자발적 중단 이벤트가 계속되기 전에 다른 노드에 추가 Pod가 예약되어 있는지 확인합니다. 스케줄러는 노드가 다시 부팅되기 전에 정의된 수의 Pod가 클러스터의 다른 노드에 성공적으로 예약될 때까지 대기합니다.

NGINX를 실행하는 5개의 Pod가 있는 복제본 세트의 예를 살펴보겠습니다. 복제본 집합의 pod에 `app: nginx-frontend`레이블이 할당 됩니다. 자발적 중단 이벤트(예: 클러스터 업그레이드) 중에 최소 3개의 Pod가 계속 실행되게 하려고 합니다. *PodDisruptionBudget* 개체의 다음 YAML 매니페스트는 다음 요구 사항을 정의합니다.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

또한 복제본 세트의 Pod 수 강화를 자동으로 보완할 수 있는 백분율(예: *60%* )을 정의할 수도 있습니다.

복제본 세트에서 사용 불가능한 인스턴스의 최대 수를 정의할 수 있습니다. 또한 최대 사용 불가능한 Pod의 백분율을 정의할 수 있습니다. 다음 Pod 중단 예산 YAML 매니페스트는 복제본 세트에서 Pod를 2개 이하로 사용하도록 정의합니다.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

Pod 중단 예산이 정의되면 다른 Kubernetes 개체와 같이 AKS 클러스터에서 이를 만듭니다.

```console
kubectl apply -f nginx-pdb.yaml
```

애플리케이션 개발자 및 소유자와 협력하여 요구 사항을 이해하고 적절한 Pod 중단 예산을 적용하세요.

Pod 중단 예산을 사용 하는 방법에 대한 자세한 내용은 [응용 프로그램에 대한 중단 예산 지정][k8s-pdbs]을 참조 하세요.

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>kube-advisor를 사용하여 클러스터 문제를 정기적으로 확인

**모범 사례 지침** -최신 버전의 `kube-advisor` 오픈 소스 도구를 정기적으로 실행 하 여 클러스터의 문제를 검색 합니다. 기존 AKS 클러스터에서 리소스 할당량을 적용하는 경우 먼저 `kube-advisor`를 실행하여 리소스 요청 및 한도가 정의되지 않은 Pod를 찾습니다.

[Kube-advisor][kube-advisor] 도구는 Kubernetes 클러스터를 검색 하 고 발견 된 문제를 보고 하는 연결 된 AKS 오픈 소스 프로젝트입니다. 한 가지 유용한 검사는 리소스 요청 및 한도가 없는 Pod를 식별하는 것입니다.

Kube-advisor 도구는 Linux 응용 프로그램 뿐만 아니라 Windows 응용 프로그램에 대해 PodSpecs에 누락 된 리소스 요청 및 제한에 대해 보고할 수 있지만 kube 도구 자체는 Linux pod에서 예약 해야 합니다. Pod 구성에서 [노드 선택기][k8s-node-selector] 를 사용 하 여 특정 OS를 사용 하 여 노드 풀에서 실행 되도록 pod를 예약할 수 있습니다.

여러 개발 팀과 애플리케이션을 호스트하는 AKS 클러스터에서는 이러한 리소스 요청 및 한도가 설정되지 않은 Pod를 추적하기 어려울 수 있습니다. 특히 리소스 할당량을 네임스페이스에 할당하지 않는 경우 AKS 클러스터에서 `kube-advisor`를 정기적으로 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 기본 Kubernetes 스케줄러 기능을 중점적으로 설명했습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [Advanced Kubernetes scheduler 기능][aks-best-practices-advanced-scheduler]
* [인증 및 권한 부여][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
