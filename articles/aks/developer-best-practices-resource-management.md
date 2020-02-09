---
title: 개발자 모범 사례 - AKS(Azure Kubernetes Services)의 리소스 관리
description: AKS(Azure Kubernetes Services)의 리소스 관리를 위한 애플리케이션 개발자 모범 사례 알아보기
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zarhoads
ms.openlocfilehash: e98a1f49ebf0fd8811be8efe8d001d450959369a
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549243"
---
# <a name="best-practices-for-application-developers-to-manage-resources-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 리소스를 관리하기 위한 애플리케이션 개발자 모범 사례

AKS(Azure Kubernetes Service)에서 애플리케이션을 개발 빛 실행할 경우 몇 가지 주요 영역을 고려해야 합니다. 애플리케이션 배포를 관리하는 방법은 사용자가 제공하는 서비스의 최종 사용자 환경에 부정적인 영향을 줄 수 있습니다. 성공하려면 AKS에서 애플리케이션을 개발하고 실행할 때 따를 수 있는 몇 가지 모범 사례를 고려하세요.

이 모범 사례 문서는 애플리케이션 개발자 관점에서 클러스터 및 워크로드를 실행하는 방법을 중점적으로 설명합니다. 관리 모범 사례에 대한 자세한 내용은 [Azure Kubernetes 서비스의 격리 및 리소스 관리에 대한 클러스터 운영자 모범 사례 (AKS)][operator-best-practices-isolation]를 참조 하세요. 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Pod 리소스 요청 및 한도의 개념
> * Dev Spaces 및 Visual Studio Code를 사용하여 애플리케이션을 개발 및 배포하는 방법
> * `kube-advisor` 도구를 사용하여 배포 관련 문제를 확인하는 방법

## <a name="define-pod-resource-requests-and-limits"></a>Pod 리소스 요청 및 한도 정의

**모범 사례 가이드** - YAML 매니페스트의 모든 Pod에서 Pod 요청 및 한도를 설정합니다. AKS 클러스터에서 ‘리소스 할당량’을 사용하는 경우 이러한 값을 정의하지 않으면 배포가 거부될 수 있습니다.

AKS 클러스터 내에서 컴퓨팅 리소스를 관리하는 기본 방법은 Pod 요청 및 한도를 사용하는 것입니다. 이러한 요청 및 한도를 사용하면 Kubernetes 스케줄러가 Pod에 할당해야 하는 컴퓨팅 리소스를 알 수 있습니다.

* **POD cpu/메모리 요청** 은 pod가 정기적으로 필요로 하는 설정 된 cpu 및 메모리 양을 정의 합니다.
    * Kubernetes scheduler가 노드에 pod를 추가 하려고 할 때 pod 요청은 예약에 사용할 수 있는 충분 한 리소스가 있는 노드를 확인 하는 데 사용 됩니다.
    * Pod 요청을 설정 하지 않으면 기본적으로 정의 된 제한으로 설정 됩니다.
    * 응용 프로그램의 성능을 모니터링 하 여 이러한 요청을 조정 하는 것이 매우 중요 합니다. 요청 수가 충분 하지 않으면 노드 예약을 통해 응용 프로그램의 성능이 저하 될 수 있습니다. 요청이 과도 하 게 예상 되는 경우 응용 프로그램의 문제가 증가 하 여 예약 될 수 있습니다.
* **POD cpu/메모리 제한은** pod에서 사용할 수 있는 최대 cpu 및 메모리의 양입니다. 이러한 제한을 통해 리소스가 부족 하 여 노드가 불안정 한 경우 중단 해야 하는 pod를 정의할 수 있습니다. 적절 한 제한이 없으면 리소스 압력이 리프트 될 때까지 pod가 종료 됩니다.
    * Pod 제한은 pod가 리소스 소비량에 대한 제어권을 상실 한 경우를 정의 합니다. 한도를 초과 하면 pod의 우선 순위를 지정 하 여 노드 상태를 유지 하 고 노드를 공유 하는 pod에 대한 영향을 최소화 합니다.
    * Pod 제한을 설정 하지 않으면 기본적으로 지정 된 노드에서 사용 가능한 가장 높은 값으로 설정 됩니다.
    * 노드에서 지원할 수 있는 것보다 높은 Pod 한도를 설정하지 마세요. 각 AKS 노드는 핵심 Kubernetes 구성 요소의 설정된 CPU 및 메모리 크기를 예약합니다. 애플리케이션이 노드에서 너무 많은 리소스를 사용하려고 하여 다른 Pod가 성공적으로 실행되지 않을 수 있습니다.
    * 이 경우에도 하루 또는 일주일 동안 응용 프로그램의 성능을 서로 다른 시간에 모니터링 하는 것이 매우 중요 합니다. 최대 수요를 확인 하 고 응용 프로그램의 최대 요구를 충족 하는 데 필요한 리소스에 pod 제한을 맞춰야 합니다.

Pod 사양에서는 위의 정보에 따라 이러한 요청 및 제한을 정의 하는 것이 **가장 좋은 방법** 입니다. 이러한 값을 포함 하지 않으면 Kubernetes scheduler는 응용 프로그램에서 일정 결정을 돕는 데 필요한 리소스를 고려할 수 없습니다.

스케줄러가 리소스가 부족 한 노드에 pod를 배치 하면 응용 프로그램 성능이 저하 됩니다. 클러스터 관리자는 리소스 요청 및 제한을 설정 해야 하는 네임 스페이스에 *리소스 할당량* 을 설정 하는 것이 좋습니다. 자세한 내용은 [AKS 클러스터의 리소스 할당량][resource-quotas]을 참조 하세요.

CPU 요청 또는 한도를 정의하는 경우 값은 CPU 단위로 측정됩니다. 
* *1.0* CPU는 노드에 있는 하나의 기본 가상 CPU 코어와 동일합니다. 
* GPU에도 동일한 측정이 사용됩니다.
* Millicores에서 측정 된 분수를 정의할 수 있습니다. 예를 들어 *100m* 는 기본 vcpu 코어의 *0.1* 입니다.

단일 NGINX Pod에 대한 다음 기본 예제에서 Pod는 *100m* CPU 시간 및 *128Mi* 메모리를 요청합니다. Pod의 리소스 한도는 *250m* CPU 및 *256Mi* 메모리로 설정됩니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: mypod
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
```

리소스 측정 및 할당에 대한 자세한 내용은 [컨테이너의 계산 리소스 관리][k8s-resource-limits]를 참조 하세요.

## <a name="develop-and-debug-applications-against-an-aks-cluster"></a>AKS 클러스터에서 애플리케이션 개발 및 디버그

**모범 사례 가이드** - 개발 팀은 Dev Spaces를 사용하여 AKS 클러스터에서 배포 및 디버그해야 합니다. 이 개발 모델에서는 앱이 프로덕션 환경에 배포되기 전에 역할 기반 액세스 제어, 네트워크 또는 스토리지 요구 사항이 구현되도록 합니다.

Azure Dev Spaces를 사용하면 AKS 클러스터에서 직접 애플리케이션을 개발, 디버그 및 테스트합니다. 팀 내의 개발자들은 애플리케이션 수명 주기 내내 함께 빌드하고 테스트합니다. Visual Studio 또는 Visual Studio Code와 같은 기존 도구를 계속 사용할 수 있습니다. 확장은 AKS 클러스터에서 애플리케이션을 실행하고 디버그하는 옵션을 제공하는 Dev Spaces용으로 설치됩니다.

![Dev Spaces를 사용하여 AKS 클러스터에서 애플리케이션 디버그](media/developer-best-practices-resource-management/dev-spaces-debug.png)

이러한 통합 된 개발 및 테스트 프로세스를 통해 [minikube][minikube]와 같은 로컬 테스트 환경에 대한 필요성이 줄어듭니다. 대신 AKS 클러스터에서 개발하고 테스트합니다. 네임스페이스를 사용하여 클러스터를 논리적으로 격리하는 방법에 대한 이전 섹션의 설명대로 이 클러스터를 보호 및 격리할 수 있습니다. 앱을 프로덕션 환경에 배포할 준비가 되면 실제 AKS 클러스터에서 배포가 모두 완료되었으므로 자신 있게 배포할 수 있습니다.

Azure Dev Spaces은 Linux pod 및 노드에서 실행 되는 응용 프로그램에서 사용 하기 위한 것입니다.

## <a name="use-the-visual-studio-code-extension-for-kubernetes"></a>Kubernetes용 Visual Studio Code 확장 사용

**모범 사례 가이드** - YAML 매니페스트를 작성할 때 Kubernetes용 VS Code 확장을 설치하고 사용합니다. 통합 배포 솔루션에 확장을 사용할 수도 있고, 이는 AKS 클러스터를 드물게 조작하는 애플리케이션 소유자에게 도움이 될 수 있습니다.

[Kubernetes에 대한 Visual Studio Code 확장][vscode-kubernetes] 은 AKS에 응용 프로그램을 개발 하 고 배포 하는 데 도움이 됩니다. 이 확장은 Kubernetes 리소스와 Helm 차트 및 템플릿에 대한 intellisense를 제공합니다. VS Code 내에서 Kubernetes 리소스를 검색, 배포 및 편집할 수도 있습니다. 이 확장은 Pod 사양에서 설정되는 리소스 요청 또는 한도에 대한 intellisense 검사도 제공합니다.

![메모리 한도 누락에 대한 Kubernetes용 VS 코드 확장 경고](media/developer-best-practices-resource-management/vs-code-kubernetes-extension.png)

## <a name="regularly-check-for-application-issues-with-kube-advisor"></a>kube-advisor를 사용하여 애플리케이션 문제를 정기적으로 확인

**모범 사례 지침** -최신 버전의 `kube-advisor` 오픈 소스 도구를 정기적으로 실행 하 여 클러스터의 문제를 검색 합니다. 기존 AKS 클러스터에서 리소스 할당량을 적용하는 경우 먼저 `kube-advisor`를 실행하여 리소스 요청 및 한도가 정의되지 않은 Pod를 찾습니다.

[Kube-advisor][kube-advisor] 도구는 Kubernetes 클러스터를 검색 하 고 발견 된 문제를 보고 하는 연결 된 AKS 오픈 소스 프로젝트입니다. 한 가지 유용한 검사는 리소스 요청 및 한도가 없는 Pod를 식별하는 것입니다.

Kube-advisor 도구는 Linux 응용 프로그램 뿐만 아니라 Windows 응용 프로그램에 대해 PodSpecs에 누락 된 리소스 요청 및 제한에 대해 보고할 수 있지만 kube 도구 자체는 Linux pod에서 예약 해야 합니다. Pod 구성에서 [노드 선택기][k8s-node-selector] 를 사용 하 여 특정 OS를 사용 하 여 노드 풀에서 실행 되도록 pod를 예약할 수 있습니다.

많은 개발 팀과 애플리케이션을 호스트하는 AKS 클러스터에서는 이러한 리소스 요청 및 한도가 설정되지 않은 Pod를 추적하기 어려울 수 있습니다. AKS 클러스터에서 `kube-advisor`를 정기적으로 실행하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

이 모범 사례 문서는 클러스터 운영자 관점에서 클러스터 및 워크로드를 실행하는 방법을 중점적으로 설명했습니다. 관리 모범 사례에 대한 자세한 내용은 [Azure Kubernetes 서비스의 격리 및 리소스 관리에 대한 클러스터 운영자 모범 사례 (AKS)][operator-best-practices-isolation]를 참조 하세요.

이러한 일부 모범 사례를 구현하려면 다음 문서를 참조하세요.

* [Dev Spaces를 사용하여 개발][dev-spaces]
* [Kube의 문제 확인-advisor][aks-kubeadvisor]

<!-- EXTERNAL LINKS -->
[k8s-resource-limits]: https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/
[vscode-kubernetes]: https://github.com/Azure/vscode-kubernetes-tools
[kube-advisor]: https://github.com/Azure/kube-advisor
[minikube]: https://kubernetes.io/docs/setup/minikube/

<!-- INTERNAL LINKS -->
[aks-kubeadvisor]: kube-advisor-tool.md
[dev-spaces]: ../dev-spaces/get-started-netcore.md
[operator-best-practices-isolation]: operator-best-practices-cluster-isolation.md
[resource-quotas]: operator-best-practices-scheduler.md#enforce-resource-quotas
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
