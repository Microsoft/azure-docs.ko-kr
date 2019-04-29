---
title: AKS(Azure Kubernetes Service) 컨트롤러 로그 보기
description: AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드에 대한 로그 활성화 및 확인 방법 알아보기
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 01/03/2019
ms.author: iainfou
ms.openlocfilehash: 77908e24a19a48bf9b84d5d5b664bf0443159118
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128705"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드 로그 활성화 및 검토

AKS(Azure Kubernetes Service)에서 *kube-apiserver* 및 *kube-controller-manager* 같은 마스터 구성 요소는 관리되는 서비스로 제공됩니다. *kubelet* 및 컨테이너 런타임을 실행하는 노드를 생성 및 관리하고, 관리되는 Kubernetes API 서버를 통해 애플리케이션을 배포합니다. 애플리케이션 및 서비스의 문제를 해결하려면 이러한 마스터 구성 요소에서 생성한 로그를 확인해야 합니다. 이 문서에서는 Azure Monitor 로그를 사용하여 Kubernetes 마스터 구성 요소의 로그를 활성화하고 쿼리하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 경우 Azure 계정에서 실행 중인 기존 AKS 클러스터가 필요합니다. AKS 클러스터가 아직 없는 경우 [Azure CLI] [ cli-quickstart] 또는 [Azure Portal][portal-quickstart]을 사용하여 만듭니다. Azure Monitor 로그는 RBAC 지원 및 RBAC 미지원 AKS 클러스터 둘 다에서 작동합니다.

## <a name="enable-diagnostics-logs"></a>진단 로그 사용

Azure Monitor 로그는 여러 원본의 데이터를 수집하고 검토하는 데 도움이 되도록 환경에 대한 인사이트를 제공하는 분석 엔진과 쿼리 언어를 제공합니다. 작업 영역은 데이터를 정렬하고 분석하는 데 사용되며, Application Insights 및 Security Center와 같은 다른 Azure 서비스와 통합할 수 있습니다. 다른 플랫폼을 사용하여 로그를 분석하려면 Azure 스토리지 계정 또는 이벤트 허브로 진단 로그를 보내는 옵션을 대신 선택할 수 있습니다. 자세한 내용은 [Azure Monitor 로그란?][log-analytics-overview]을 참조하세요.

Azure Monitor 로그는 Azure Portal에서 사용하도록 설정되고 관리됩니다. AKS 클러스터의 Kubernetes 마스터 구성 요소에 대한 로그 수집을 사용하도록 설정하려면 웹 브라우저에서 Azure Portal을 열고 다음 단계를 완료하세요.

1. AKS 클러스터의 리소스 그룹(예: *myResourceGroup*)을 선택합니다. 개별 AKS 클러스터 리소스가 포함된 리소스 그룹(예: *MC_myResourceGroup_myAKSCluster_eastus*)은 선택하지 마세요.
1. 왼쪽에서 **진단 설정**을 선택합니다.
1. AKS 클러스터(예: *myAKSCluster*)를 선택한 후 **진단 켜기**.
1. *myAKSClusterLogs* 등의 이름을 입력하고 **Log Analytics 작업 영역에 보내기** 옵션을 선택합니다.
    * Log Analytics 작업 영역 ‘구성’을 선택한 다음, 기존 작업 영역을 선택하거나 **새 작업 영역 만들기**를 선택합니다.
    * 작업 영역을 만들어야 하는 경우, 이름, 리소스 그룹 및 위치를 지정합니다.
1. 사용 가능한 로그 목록에서 사용하도록 설정하려는 로그를 선택합니다. 기본적으로 *kube-apiserver*, *kube-controller-manager* 및 *kube-scheduler* 로그가 사용하도록 설정됩니다. *kube-audit* 및 *cluster-autoscaler*와 같은 추가 로그를 사용하도록 설정할 수 있습니다. Log Analytics 작업 영역이 사용하도록 설정되면 수집된 로그를 반환하고 변경할 수 있습니다.
1. 준비되면 **저장**을 선택하여 선택된 로그의 수집을 사용하도록 설정합니다.

> [!NOTE]
> AKS는 구독에서 기능 플래그를 사용하도록 설정한 후 만들거나 업그레이드한 클러스터의 감사 로그만 캡처합니다. *AKSAuditLog* 기능 플래그를 등록하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용합니다.
>
> `az feature register --name AKSAuditLog --namespace Microsoft.ContainerService`
>
> 상태에 *등록됨*이 표시될 때까지 기다립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.
>
> `az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAuditLog')].{Name:name,State:properties.state}"`
>
> 준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 AKS 리소스 공급자 등록을 새로 고칩니다.
>
> `az provider register --namespace Microsoft.ContainerService`

다음의 예제 포털 스크린샷에서는 *진단 설정* 창과 Log Analytics 작업 영역 생성 옵션을 보여줍니다.

![AKS 클러스터의 Azure Monitor 로그에 대해 Log Analytics 작업 영역을 사용하도록 설정](media/view-master-logs/enable-oms-log-analytics.png)

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>AKS 클러스터의 테스트 Pod 예약

일부 로그를 생성하려면 AKS 클러스터에 새 Pod를 만듭니다. 다음 예제의 YAML 매니페스트는 기본 NGINX 인스턴스를 만드는 데 사용할 수 있습니다. 원하는 편집기에서 `nginx.yaml`이라는 파일을 만들고 다음 내용을 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
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
    ports:
    - containerPort: 80
```

다음 예에 표시된 대로 [kubectl create][kubectl-create] 명령을 사용하여 Pod를 만들고 YAML 파일을 지정합니다.

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>수집된 로그 보기

진단 로그를 사용하도록 설정하고 Log Analytics 작업 영역에 표시되기까지 몇 분 정도 걸릴 수 있습니다. Azure portal에서 Log Analytics 작업 영역에 대 한 리소스 그룹을 같은 선택 *myResourceGroup*를 선택한 다음 log analytics 리소스를 사용 하 여 같은 *myAKSLogs*합니다.

![AKS 클러스터의 Log Analytics 작업 영역 선택](media/view-master-logs/select-log-analytics-workspace.png)

왼쪽에서 **로그**를 선택합니다. *kube-apiserver*를 보려면 텍스트 상자에 다음 쿼리를 입력합니다.

```
AzureDiagnostics
| where Category == "kube-apiserver"
| project log_s
```

API 서버에 대한 많은 로그가 반환됩니다. 쿼리 범위를 좁혀서 이전 단계에서 만든 NGINX Pod에 대한 로그만 보려면 다음 쿼리 예제에 표시된 것처럼 *pods/nginx*를 검색하는 *where* 문을 추가합니다.

```
AzureDiagnostics
| where Category == "kube-apiserver"
| where log_s contains "pods/nginx"
| project log_s
```

다음 예제 스크린샷에 표시된 것처럼 NGINX Pod에 대한 자세한 로그가 표시됩니다.

![샘플 NGINX Pod에 대한 Log Analytics 쿼리 결과](media/view-master-logs/log-analytics-query-results.png)

추가 로그를 보려면 사용하도록 설정할 추가 로그에 따라 쿼리에서 *Category* 이름을 *kube-controller-manager* 또는 *kube-scheduler*로 업데이트하면 됩니다. 이때 검색하려는 이벤트를 구체화하기 위해 추가적인 *where* 문을 사용할 수 있습니다.

로그 데이터를 쿼리하고 필터링하는 방법에 대한 자세한 내용은 [로그 분석 로그 검색을 사용하여 수집한 데이터 보기 또는 분석][analyze-log-analytics]을 참조하세요.

## <a name="log-event-schema"></a>로그 이벤트 스키마

다음 표에는 로그 데이터 분석에 도움이 되도록 각 이벤트에 사용되는 스키마가 자세히 설명되어 있습니다.

| 필드 이름               | 설명 |
|--------------------------|-------------|
| *resourceId*             | 로그를 생성한 Azure 리소스 |
| *time*                   | 로그가 업로드된 시간의 타임스탬프 |
| *category*               | 로그를 생성하는 컨테이너/로그의 이름 |
| *operationName*          | Always *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | 구성 요소 로그의 전체 텍스트 |
| *properties.stream*      | *stderr* 또는 *stdout* |
| *properties.pod*         | 로그를 가져온 Pod 이름 |
| *properties.containerID* | 이 로그를 가져온 Docker 컨테이너의 ID |

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 Kubernetes 마스터 구성 요소에 대한 로그를 사용하도록 설정하고 검토하는 방법을 알아보았습니다. 추가로 모니터링하고 문제를 해결하려면 [Kubelet 로그를 확인][kubelet-logs]하고 [SSH 노드 액세스를 사용하도록 설정][aks-ssh]하면 됩니다.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../log-analytics/log-analytics-overview.md
[analyze-log-analytics]: ../azure-monitor/learn/tutorial-viewdata.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
