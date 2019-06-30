---
title: 개념 - AKS(Azure Kubernetes Service)의 Kubernetes 기본 사항
description: Kubernetes의 기본 클러스터 및 워크로드 구성 요소와 AKS(Azure Kubernetes Service)의 기능과의 관계에 대해 알아봅니다.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: iainfou
ms.openlocfilehash: ab818c0bded71b4566173f4a6a720fce9bc539c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514521"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념

컨테이너 기반 방식으로 응용 프로그램을 개발함에 따라 리소스 관리와 오케스트레이션이 중요합니다. Kubernetes는 내결함성 애플리케이션 워크로드를 안정적으로 예약할 수 있는 기능을 제공하는 선도적인 플랫폼입니다. AKS(Azure Kubernetes Service)는 관리되는 Kubernetes 제품으로, 컨테이너 기반 애플리케이션을 더 간편하게 배포하고 관리할 수 있습니다.

이 문서에서는 *클러스터 마스터*, *노드* 및 *노드 풀*과 같은 Kubernetes 핵심 인프라 구성 요소를 소개합니다. 리소스를 *네임스페이스*로 그룹화하는 방법과 함께 *Pod*, *배포* 및 *집합*과 같은 워크로드 리소스도 소개합니다.

## <a name="what-is-kubernetes"></a>Kubernetes란?

Kubernetes는 컨테이너 기반 애플리케이션과 관련 네트워킹 및 저장소 구성 요소를 관리하는 플랫폼으로서 빠르게 진화하고 있습니다. 기본 인프라 구성 요소가 아니라 애플리케이션 워크로드에 중점을 두고 있습니다. Kubernetes는 관리 작업을 위한 강력한 API 집합을 통해 지원되는 선언적 배포 방식을 제공합니다.

Kubernetes에서 이러한 애플리케이션 구성 요소의 가용성을 오케스트레이션하고 관리하는 데 도움이 되는 최신의 이식 가능한 마이크로 서비스 기반 애플리케이션을 구축하고 실행할 수 있습니다. Kubernetes는 팀에서 마이크로 서비스 기반 애플리케이션의 채택을 통해 진행함에 따라 상태 비저장 및 상태 저장 애플리케이션을 모두 지원합니다.

오픈 플랫폼인 Kubernetes를 사용하면 기본 설정 프로그래밍 언어, OS, 라이브러리 또는 메시지 버스를 사용하여 애플리케이션을 빌드할 수 있습니다. 기존의 CI/CD(지속적인 통합 및 지속적인 업데이트) 도구는 Kubernetes와 통합되어 릴리스를 예약하고 배포할 수 있습니다.

AKS(Azure Kubernetes Service)는 업그레이드 조정을 포함하여 배포 및 핵심 관리 작업의 복잡성을 줄여 주는 관리되는 Kubernetes 서비스를 제공합니다. AKS 클러스터 마스터는 Azure 플랫폼에서 관리하며, 애플리케이션을 실행하는 AKS 노드에 대해서만 비용을 지불합니다. AKS는 오픈 소스 Azure Kubernetes 서비스 엔진 위에 빌드됩니다 ([aks 엔진][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Kubernetes 클러스터 아키텍처

Kubernetes 클러스터는 다음 두 가지 구성 요소로 구분됩니다.

- *클러스터 마스터* 노드에서 Kubernetes 핵심 서비스와 애플리케이션 워크로드 오케스트레이션을 제공합니다.
- *노드*에서 애플리케이션 워크로드를 실행합니다.

![Kubernetes 클러스터 마스터 및 노드 구성 요소](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>클러스터 마스터

AKS 클러스터를 만들면 클러스터 마스터가 자동으로 만들어지고 구성됩니다. 이 클러스터 마스터는 사용자로부터 추상화된 관리되는 Azure 리소스로 제공됩니다. AKS 클러스터의 일부인 노드만 클러스터 마스터에 대 한 비용은 없습니다.

클러스터 마스터에 포함된 핵심 Kubernetes 구성 요소는 다음과 같습니다.

- *kube-apiserver* - API 서버는 기본 Kubernetes API를 공개하는 방법입니다. 이 구성 요소는 `kubectl` 또는 Kubernetes 대시보드와 같은 관리 도구에 대한 상호 작용을 제공합니다.
- *etcd* - 고가용성 *etcd*는 Kubernetes 클러스터 및 구성의 상태를 유지 관리하기 위해 Kubernetes 내에 있는 키 값 저장소입니다.
- *kube-scheduler* - 애플리케이션을 만들거나 확장할 때 스케줄러는 워크로드를 실행할 수 있는 노드를 결정하고 시작합니다.
- *kube-controller-manager* - 컨트롤러 관리자는 Pod 복제 및 노드 작업 처리와 같은 작업을 수행하는 다수의 작은 컨트롤러를 감독합니다.

AKS는 전용 API 서버, 스케줄러 등을 사용하여 단일 테넌트 클러스터 마스터를 제공합니다. 노드의 수와 크기를 정의하고, Azure 플랫폼에서 클러스터 마스터와 노드 간의 보안 통신을 구성합니다. 클러스터 마스터와의 상호 작용은 `kubectl` 또는 Kubernetes 대시보드와 같은 Kubernetes API를 통해 수행됩니다.

이 관리 되는 클러스터 마스터는 항상 사용 가능한 같은 구성 요소를 구성할 필요가 없습니다 의미 *etcd* 저장소 하지만 또한 클러스터 마스터 직접 액세스할 수 없습니다. Kubernetes로의 업그레이드는 Azure CLI 또는 Azure Portal을 통해 오케스트레이션되며, 클러스터 마스터, 다음으로 노드를 업그레이드합니다. 가능한 문제를 해결하기 위해 Azure Monitor 로그를 통해 클러스터 마스터 로그를 검토할 수 있습니다.

클러스터 마스터를 특정 방식으로 구성해야 하거나 직접 액세스해야 하는 경우 [aks-engine][aks-engine]을 사용하여 사용자 고유의 Kubernetes 클러스터를 배포할 수 있습니다.

관련된 모범 사례를 참조 하세요 [클러스터 보안 및 AKS에서 업그레이드에 대 한 유용한][operator-best-practices-cluster-security]합니다.

## <a name="nodes-and-node-pools"></a>노드 및 노드 풀

애플리케이션과 지원 서비스를 실행하려면 Kubernetes *노드*가 필요합니다. AKS 클러스터에는 하나 이상의 노드가 있습니다. 이 노드는 Kubernetes 노드 구성 요소 및 컨테이너 런타임을 실행하는 Azure VM(가상 머신)입니다.

- `kubelet`은 클러스터 마스터의 오케스트레이션 요청을 처리하고 요청된 컨테이너를 실행하도록 예약하는 Kubernetes 에이전트입니다.
- 가상 네트워킹은 각 노드의 *kube-proxy*에서 처리됩니다. 프록시는 네트워크 트래픽을 라우팅하고 서비스와 Pod에 대한 IP 주소 지정을 관리합니다.
- *컨테이너 런타임*은 컨테이너화된 애플리케이션을 실행하고 가상 네트워크 및 저장소와 같은 추가 리소스와 상호 작용할 수 있게 하는 구성 요소입니다. AKS를 모 비 컨테이너 런타임을으로 사용 됩니다.

![Kubernetes 노드용 Azure 가상 머신 및 지원 리소스](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

노드의 Azure VM 크기는 CPU 수, 메모리 크기, 사용 가능한 저장소(예: 고성능 SSD 또는 일반 HDD)의 크기 및 유형을 정의합니다. 대용량의 CPU와 메모리 또는 고성능 저장소가 필요한 애플리케이션이 요구되는 경우 노드 크기를 적절히 계획합니다. 요구에 맞게 AKS 클러스터의 노드 수를 확장할 수도 있습니다.


AKS에서 클러스터의 노드에 대한 VM 이미지는 현재 Ubuntu Linux 또는 Windows Server 2019 기반입니다. AKS 클러스터를 만들거나 노드 수를 확장하면 Azure 플랫폼에서 요청된 수의 VM을 만들고 구성합니다. 수동으로 진행해야 하는 구성은 없습니다. 에이전트 노드는 표준 가상 머신으로 청구되며, 사용하는 VM 크기에 대한 할인 ([Azure 예약][reservation-discounts] 포함)이 자동으로 적용됩니다.


다른 호스트 OS, 컨테이너 런타임을 사용하거나 사용자 지정 패키지를 포함해야 하는 경우 [aks-engine][aks-engine]을 사용하여 사용자 고유의 Kubernetes 클러스터를 배포할 수 있습니다. 업스트림 `aks-engine`은 AKS 클러스터에서 공식적으로 지원되기 전에 기능을 릴리스하여 구성 옵션을 제공합니다. 예를 들어 모비(Moby) 이외의 컨테이너 런타임을 사용하려면 `aks-engine`을 사용하여 Kubernetes 클러스터를 구성하고 배포할 수 있습니다.

### <a name="resource-reservations"></a>리소스 예약

각 노드의 핵심 Kubernetes 구성 요소(예: *kubelet*, *kube-proxy* 및 *kube-dns*)를 관리할 필요는 없지만 이러한 구성 요소는 사용 가능한 컴퓨팅 리소스 중 일부를 사용합니다. 노드 성능 및 기능을 유지 관리하기 위해 각 노드에서 다음 컴퓨팅 리소스가 예약됩니다.

- **CPU** - 60ms
- **메모리** - 최대 4GiB까지 20%

이러한 예약은 애플리케이션에 사용 가능한 CPU 및 메모리 양이 노드 자체에 포함된 양보다 적게 표시될 수 있음을 의미합니다. 실행하는 애플리케이션 수로 인해 리소스 제약 조건이 있는 경우 이러한 예약을 통해 핵심 Kubernetes 구성 요소에 사용 가능한 CPU 및 메모리를 유지할 수 있습니다. 리소스 예약을 변경할 수 없습니다.

예를 들면 다음과 같습니다.

- **표준 DS2 v2** 노드 크기에는 2개의 vCPU 및 7GiB 메모리가 포함됩니다.
    - 7GiB 메모리의 20%는 1.4GiB입니다.
    - 노드에 사용 가능한 총 메모리는 *(7 - 1.4) = 5.6GiB*입니다.
    
- **표준 E4s v3** 노드 크기에는 4개의 vCPU 및 32GiB 메모리가 포함됩니다.
    - 32GiB 메모리의 20%는 6.4GiB이지만 AKS에서는 최대 4GiB만 예약합니다.
    - 노드에 사용 가능한 총 메모리는 *(32 - 4) = 28GiB*입니다.
    
기본 노드 OS에는 자체 핵심 기능을 완료하는 데 필요한 소량의 CPU 및 메모리 리소스도 필요합니다.

관련 모범 사례는 [AKS의 클러스터 보안 및 업그레이드 모범 사례][operator-best-practices-scheduler]를 참조하십시오.

### <a name="node-pools"></a>노드 풀

동일한 구성의 노드는 모두 *노드 풀*로 그룹화됩니다. Kubernetes 클러스터에는 하나 이상의 노드 풀이 포함됩니다. 노드의 초기 수와 크기는 *기본 노드 풀*을 만드는 AKS 클러스터를 만들 때 정의됩니다. AKS의 기본 노드 풀에는 에이전트 노드를 실행하는 기본 VM이 포함됩니다. 여러 노드 풀 지원은 현재 AKS에서 미리 보기로 제공 됩니다.

AKS 클러스터를 확장 또는 업그레이드할 때 기본 노드 풀에 대한 작업이 수행됩니다. 크기를 조정 하거나 특정 노드 풀을 업그레이드할 수도 있습니다. 업그레이드 작업의 경우, 모든 노드가 성공적으로 업그레이드될 때까지 실행 중인 컨테이너는 노드 풀의 다른 노드에 예약됩니다.

AKS에서 여러 노드 풀을 사용하는 자세한 방법은 [AKS 클러스터에 대한 여러 노드 풀 만들기 및 관리하기][use-multiple-node-pools]를 참고하세요.

### <a name="node-selectors"></a>노드 선택기

AKS 클러스터에서 여러 노드 풀을 포함하는 경우 지정된 리소스에 대해 사용할 노드 풀을 Kubernetes 스케줄러에게 알려주어야 할 수 있습니다. 예를 들어, 수신 컨트롤러는 Windows Server 노드에서 실행하지 않아야 합니다(현재 AKS에서 미리 보기). 노드 선택기를 사용하면 노드 OS와 같은 다양한 매개 변수를 정의하여 pod 예약 제어를 할 수 있습니다.

다음 기본 예제에서는 노드 선택기 *"beta.kubernetes.io/os": linux*를 사용하여 Linux 노드 상의 NGINX 인스턴스를 예약합니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Pod 예약을 제어하는 방법에 대한 자세한 내용은 [AKS의 고급 스케줄러 기능에 대한 모범 사례][operator-best-practices-advanced-scheduler]를 참조하세요.

## <a name="pods"></a>Pod

Kubernetes는 *Pod*를 사용하여 애플리케이션의 인스턴스를 실행합니다. Pod는 애플리케이션의 단일 인스턴스를 나타냅니다. Pods에는 일반적으로 컨테이너와의 1:1 매핑이 있지만, 한 Pod에 여러 컨테이너가 포함될 수 있는 고급 시나리오도 있습니다. 이러한 다중 컨테이너 Pod는 모두 동일한 노드에 예약되며, 컨테이너에서 관련 리소스를 공유할 수 있습니다.

Pod를 만드는 경우 특정 양의 CPU 또는 메모리 리소스를 요청하는 *리소스 제한*을 정의할 수 있습니다. Kubernetes 스케줄러는 요구에 맞게 사용 가능한 리소스가 있는 노드에서 Pod가 실행되도록 예약하려고 시도합니다. 지정된 노드에서 기본 노드의 계산 리소스를 너무 많이 소비하지 못하도록 최대 리소스 제한을 지정할 수도 있습니다. Kubernetes 스케줄러에서 필요한 리소스와 허용되는 리소스를 파악하는 데 도움이 되도록 모든 Pod에 대한 리소스 제한을 포함하는 것이 좋습니다.

자세한 내용은 [Kubernetes Pod][kubernetes-pods] 및 [Kubernetes Pod 수명 주기][kubernetes-pod-lifecycle]를 참조하세요.

Pod는 논리적인 리소스이지만, 컨테이너는 애플리케이션 워크로드가 실행되는 위치입니다. Pod는 일반적으로 사용 후 삭제 가능한 리소스이며, 개별적으로 예약된 Pod에는 Kubernetes에서 제공하는 고가용성 및 중복성 기능 중 일부가 누락됩니다. 대신, Pod는 일반적으로 배포 컨트롤러와 같은 Kubernetes *컨트롤러*에서 배포되고 관리됩니다.

## <a name="deployments-and-yaml-manifests"></a>배포 및 YAML 매니페스트

*배포*는 Kubernetes 배포 컨트롤에서 관리되는 하나 이상의 동일한 Pod를 나타냅니다. 배포는 만들 *복제본*(Pod)의 수를 정의하고, Kubernetes 스케줄러는 Pod 또는 노드에 문제가 발생하면 추가 Pod가 정상 노드에 예약되도록 합니다.

Pod나 사용된 컨테이너 이미지, 연결된 저장소의 구성을 변경하도록 배포를 업데이트할 수 있습니다. 배포 컨트롤러는 지정된 수의 복제본을 내보내거나 종료하고, 새 배포 정의에서 복제본을 만든 후에 배포의 모든 복제본이 업데이트될 때까지 프로세스를 계속 수행합니다.

AKS의 상태 비저장 애플리케이션 대부분은 개별 Pod를 예약하는 것이 아니라 배포 모델을 사용해야 합니다. Kubernetes는 배포의 상태를 모니터링하여 필요한 수의 복제본이 클러스터 내에서 실행되도록 할 수 있습니다. 개별 pod를 예약만 하는 경우, 문제가 발생하면 pod가 다시 시작되지 않고, 현재 노드에 문제가 발생할 경우 정상 노드에서 재예약이 되지 않습니다.

애플리케이션에서 관리 결정을 위해 인스턴스의 쿼럼을 항상 사용할 수 있어야 하는 경우 업데이트 프로세스에서 해당 기능을 중단하지 않도록 합니다. *Pod 중단 예산*은 업데이트 또는 노드 업그레이드 중에 중단할 수 있는 배포의 복제본 수를 정의하는 데 사용할 수 있습니다. 예를 들어 배포에 *5*개의 복제본이 있는 경우 한 번에 하나의 복제본만 삭제/다시 예약되도록 Pod 중단을 *4*로 정의할 수 있습니다. Pod 리소스 제한과 마찬가지로 항상 최소 개수의 복제본이 존재하도록 요구하는 애플리케이션에 Pod 중단 예산을 정의하는 것이 가장 좋습니다.

배포는 일반적으로 `kubectl create` 또는 `kubectl apply`를 사용하여 만들고 관리합니다. 배포를 만들려면 매니페스트 파일을 YAML(YAML Ain't Markup Language) 형식으로 정의합니다. 다음 예제에서는 NGINX 웹 서버의 기본 배포를 만듭니다. 배포에서 *3*개의 복제본을 만들고 해당 *80*번 포트가 컨테이너에서 열리도록 지정합니다. CPU 및 메모리에 대한 리소스 요청 및 제한도 정의합니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

YAML 매니페스트 내에 부하 분산 장치와 같은 서비스를 포함시킴으로써 더 복잡한 애플리케이션을 만들 수 있습니다.

자세한 내용은 [Kubernetes 배포][kubernetes-deployments]를 참조하세요.

### <a name="package-management-with-helm"></a>Helm을 사용한 패키지 관리

Kubernetes에서 애플리케이션을 관리하는 일반적인 방법은 [Helm][helm]을 사용하는 것입니다. 패키지 버전의 애플리케이션 코드와 Kubernetes YAML 매니페스트가 포함된 기존 공용 Helm *차트*를 빌드하고 사용하여 리소스를 배포할 수 있습니다. 이러한 Helm 차트는 로컬로 저장하거나 종종 [Azure Container Registry Helm 차트 리포지토리][acr-helm]와 같은 원격 저장소에 저장할 수 있습니다.

Helm을 사용하려면 *Tiller*라는 서버 구성 요소가 Kubernetes 클러스터에 설치되어 있어야 합니다. Tiller는 클러스터 내의 차트 설치를 관리합니다. Helm 클라이언트 자체는 컴퓨터에 로컬로 설치하거나 [Azure Cloud Shell][azure-cloud-shell] 내에서 사용할 수 있습니다. 클라이언트에서 Helm 차트를 검색하거나 만든 다음, Kubernetes 클러스터에 설치할 수 있습니다.

![Helm에는 클라이언트 구성 요소와 Kubernetes 클러스터 내에 리소스를 만드는 서버 쪽 Tiller 구성 요소가 포함됩니다.](media/concepts-clusters-workloads/use-helm.png)

자세한 내용은 [AKS(Azure Kubernetes Service)에서 Helm을 사용하여 애플리케이션 설치][aks-helm]를 참조하세요.

## <a name="statefulsets-and-daemonsets"></a>StatefulSets 및 DaemonSets

배포 컨트롤러는 Kubernetes 스케줄러를 사용하여 사용 가능한 리소스가 있는 사용 가능한 노드에서 지정된 수의 복제본을 실행합니다. 이러한 배포 방법은 상태 비저장 애플리케이션에는 충분하지만, 영구적인 명명 규칙이나 저장소가 필요한 애플리케이션에는 충분하지 않을 수 있습니다. 클러스터 내의 각 노드 또는 선택한 노드에 복제본이 있어야 하는 애플리케이션의 경우 배포 컨트롤러는 복제본이 노드 전체에 분산되는 방식을 확인하지 않습니다.

이러한 유형의 애플리케이션을 관리할 수 있는 두 가지 Kubernetes 리소스는 다음과 같습니다.

- *StatefulSets* - 애플리케이션 상태를 저장소와 같은 개별 Pod 수명 주기 이상으로 유지합니다.
- *DaemonSets* - Kubernetes 부트스트랩 프로세스의 초기에 각 노드에서 실행 중인 인스턴스를 확인합니다.

### <a name="statefulsets"></a>StatefulSets

최신 애플리케이션 개발은 종종 상태 비저장 애플리케이션을 대상으로 하지만, *StatefulSets*는 데이터베이스 구성 요소가 포함된 애플리케이션과 같은 상태 저장 애플리케이션에 사용할 수 있습니다. StatefulSet은 하나 이상의 동일한 Pod가 만들어지고 관리된다는 점에서 배포와 비슷합니다. StatefulSet의 복제본은 배포, 크기 조정, 업그레이드 및 종료에 대한 정상적이고 순차적인 접근 방식을 따릅니다. StatefulSet을 사용하면 복제본이 다시 예약될 때 명명 규칙, 네트워크 이름 및 저장소가 그대로 유지됩니다.

`kind: StatefulSet`을 사용하여 애플리케이션을 YAML 형식으로 정의한 다음, StatefulSet 컨트롤러에서 필요한 복제본의 배포와 관리를 처리합니다. 데이터는 Azure Managed Disks 또는 Azure Files에서 제공되는 영구적 저장소에 기록됩니다. StatefulSet을 사용하면 StatefulSet이 삭제되는 경우에도 기본 영구적 저장소가 유지됩니다.

자세한 내용은 [Kubernetes StatefulSets][kubernetes-statefulsets]를 참조하세요.

StatefulSet의 복제본은 AKS 클러스터의 사용 가능한 모든 노드에서 예약되고 실행됩니다. Set에 있는 하나 이상의 Pod가 노드에서 실행되도록 하려면 DaemonSet을 대신 사용할 수 있습니다.

### <a name="daemonsets"></a>DaemonSets

특정 로그 수집 또는 모니터링이 필요한 경우 모든 노드 또는 선택한 노드에서 지정된 Pod를 실행해야 할 수 있습니다. *DaemonSet*은 하나 이상의 동일한 Pod를 배포하는 데 다시 사용되지만, DaemonSet 컨트롤러는 지정된 각 노드가 Pod의 인스턴스를 실행하도록 합니다.

기본 Kubernetes 스케줄러가 시작되기 전에 DaemonSet 컨트롤러에서 클러스터 부팅 프로세스의 초기에 Pod를 노드에 예약할 수 있습니다. 이 기능은 Deployment 또는 StatefulSet의 기존 Pod가 예약되기 전에 DaemonSet의 Pod가 시작되도록 합니다.

StatefulSet과 마찬가지로 DaemonSet은 `kind: DaemonSet`을 사용하여 YAML 정의의 일부로 정의됩니다.

자세한 내용은 [Kubernetes DaemonSets][kubernetes-daemonset]를 참조하세요.

> [!NOTE]
> [가상 노드 추가 기능](virtual-nodes-cli.md#enable-virtual-nodes-addon)을 사용하는 경우, Daemonset는 해당 가상 노드에 pod를 생성하지 않습니다.

## <a name="namespaces"></a>네임스페이스

Pod 및 Deployment와 같은 Kubernetes 리소스는 논리적으로 *네임스페이스*로 그룹화됩니다. 이러한 그룹은 AKS 클러스터를 논리적으로 분할하고 액세스를 제한하여 리소스를 만들거나 보거나 관리하는 방법을 제공합니다. 예를 들어 비즈니스 그룹을 구분하는 네임스페이스를 만들 수 있습니다. 사용자는 할당된 네임스페이스 내의 리소스와만 상호 작용할 수 있습니다.

![리소스와 애플리케이션이 논리적으로 분할된 Kubernetes 네임스페이스](media/concepts-clusters-workloads/namespaces.png)

AKS 클러스터를 만들 때 사용할 수 있는 네임스페이스는 다음과 같습니다.

- *default* - 이 네임스페이스는 아무 것도 제공되지 않을 때 Pod 및 배포가 기본적으로 만들어지는 위치입니다. 소규모 환경에서는 논리적 구분을 추가로 만들지 않고 애플리케이션을 기본 네임스페이스로 직접 배포할 수 있습니다. `kubectl get pods`와 같이 Kubernetes API와 상호 작용할 때 아무 것도 지정되지 않으면 기본 네임스페이스가 사용됩니다.
- *kube-system* - 이 네임스페이스는 네트워크 기능(예: DNS 및 프록시) 또는 Kubernetes 대시보드와 같은 핵심 리소스가 있는 위치입니다. 일반적으로 사용자 고유의 애플리케이션은 이 네임스페이스에 배포하지 않습니다.
- *kube-public* - 이 네임스페이스는 일반적으로 사용되지 않지만, 클러스터 전체에 표시되는 리소스에 사용할 수 있으며 모든 사용자가 볼 수 있습니다.

자세한 내용은 [Kubernetes 네임스페이스][kubernetes-namespaces]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Kubernetes 핵심 구성 요소 중 일부와 AKS 클러스터에 이를 적용하는 방법에 대해 설명하고 있습니다. Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 액세스 및 ID][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 저장소][aks-concepts-storage]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md