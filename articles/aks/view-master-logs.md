---
title: AKS(Azure Kubernetes Service) 컨트롤러 로그 보기
description: AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드에 대한 로그 활성화 및 확인 방법 알아보기
services: container-service
ms.topic: article
ms.date: 01/03/2019
ms.openlocfilehash: 721ef4f60d263602b01b5957bfb9bc3b5682a2df
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048281"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Kubernetes 마스터 노드 로그 활성화 및 검토

AKS(Azure Kubernetes Service)에서 *kube-apiserver* 및 *kube-controller-manager* 같은 마스터 구성 요소는 관리되는 서비스로 제공됩니다. *kubelet* 및 컨테이너 런타임을 실행하는 노드를 생성 및 관리하고, 관리되는 Kubernetes API 서버를 통해 애플리케이션을 배포합니다. 애플리케이션 및 서비스의 문제를 해결하려면 이러한 마스터 구성 요소에서 생성한 로그를 확인해야 합니다. 이 문서에서는 Azure Monitor 로그를 사용하여 Kubernetes 마스터 구성 요소의 로그를 활성화하고 쿼리하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 경우 Azure 계정에서 실행 중인 기존 AKS 클러스터가 필요합니다. AKS 클러스터가 아직 없는 경우 [Azure CLI][cli-quickstart] 또는 [Azure Portal][portal-quickstart]을 사용하여 만듭니다. Azure Monitor 로그는 RBAC 지원 및 RBAC 미지원 AKS 클러스터 둘 다에서 작동합니다.

## <a name="enable-resource-logs"></a>리소스 로그 사용

Azure Monitor 로그는 여러 원본의 데이터를 수집하고 검토하는 데 도움이 되도록 환경에 대한 인사이트를 제공하는 분석 엔진과 쿼리 언어를 제공합니다. 작업 영역은 데이터를 정렬하고 분석하는 데 사용되며, Application Insights 및 Security Center와 같은 다른 Azure 서비스와 통합할 수 있습니다. 다른 플랫폼을 사용 하 여 로그를 분석 하려면 대신 Azure storage 계정 또는 이벤트 허브로 리소스 로그를 보내도록 선택할 수 있습니다. 자세한 내용은 [Azure Monitor 로그란?][log-analytics-overview]을 참조하세요.

Azure Monitor 로그는 Azure Portal에서 사용 하도록 설정 되 고 관리 됩니다. AKS 클러스터의 Kubernetes 마스터 구성 요소에 대한 로그 수집을 사용하도록 설정하려면 웹 브라우저에서 Azure Portal을 열고 다음 단계를 완료하세요.

1. AKS 클러스터의 리소스 그룹(예: *myResourceGroup*)을 선택합니다. 개별 AKS 클러스터 리소스가 포함된 리소스 그룹(예: *MC_myResourceGroup_myAKSCluster_eastus*)은 선택하지 마세요.
1. 왼쪽에서 **진단 설정**을 선택합니다.
1. AKS 클러스터를 선택 하 고 (예: *myAKSCluster*) **진단 설정을 추가**하도록 선택 합니다.
1. *myAKSClusterLogs*와 같은 이름을 입력한 후 **Log Analytics에 보내기** 옵션을 선택합니다.
1. 기존 작업 영역을 선택 하거나 새 작업 영역을 만듭니다. 작업 영역을 만드는 경우 작업 영역 이름, 리소스 그룹 및 위치를 제공 합니다.
1. 사용 가능한 로그 목록에서 사용하도록 설정하려는 로그를 선택합니다. 이 예에서는 *kube* 로그를 사용 하도록 설정 합니다. 일반적인 로그에는 *kube 서버*, *kube*및 *kube*가 포함 됩니다. Log Analytics 작업 영역이 사용하도록 설정되면 수집된 로그를 반환하고 변경할 수 있습니다.
1. 준비되면 **저장**을 선택하여 선택된 로그의 수집을 사용하도록 설정합니다.

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

진단 로그를 사용 하도록 설정 하 고 표시 하는 데 몇 분 정도 걸릴 수 있습니다. Azure Portal에서 AKS 클러스터로 이동 하 고 왼쪽에 있는 **로그** 를 선택 합니다. *예제 쿼리* 창이 나타나면 닫습니다.


왼쪽에서 **로그**를 선택합니다. *Kube-감사* 로그를 보려면 텍스트 상자에 다음 쿼리를 입력 합니다.

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

많은 로그가 반환 될 가능성이 높습니다. 이전 단계에서 만든 NGINX pod에 대 한 로그를 볼 수 있도록 쿼리 범위를 좁히려면 다음 예제 쿼리와 같이 *NGINX* 를 검색 하는 또 다른 *where* 문을 추가 합니다.

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

추가 로그를 보려면 사용하도록 설정할 추가 로그에 따라 쿼리에서 *Category* 이름을 *kube-controller-manager* 또는 *kube-scheduler*로 업데이트하면 됩니다. 이때 검색하려는 이벤트를 구체화하기 위해 추가적인 *where* 문을 사용할 수 있습니다.

로그 데이터를 쿼리하고 필터링 하는 방법에 대 한 자세한 내용은 [log analytics 로그 검색을 사용 하 여 수집한 데이터 보기 또는 분석][analyze-log-analytics]을 참조 하세요.

## <a name="log-event-schema"></a>로그 이벤트 스키마

다음 표에는 로그 데이터 분석에 도움이 되도록 각 이벤트에 사용되는 스키마가 자세히 설명되어 있습니다.

| 필드 이름               | Description |
|--------------------------|-------------|
| *resourceId*             | 로그를 생성한 Azure 리소스 |
| *time*                   | 로그가 업로드된 시간의 타임스탬프 |
| *category*               | 로그를 생성하는 컨테이너/로그의 이름 |
| *operationName*          | Always *Microsoft.ContainerService/managedClusters/diagnosticLogs/Read* |
| *properties.log*         | 구성 요소 로그의 전체 텍스트 |
| *properties.stream*      | *stderr* 또는 *stdout* |
| *properties.pod*         | 로그를 가져온 Pod 이름 |
| *properties.containerID* | 이 로그를 가져온 docker 컨테이너의 ID입니다. |

## <a name="log-roles"></a>로그 역할

| 역할                     | Description |
|--------------------------|-------------|
| *aksService*             | HcpService의 제어 평면 작업에 대 한 감사 로그의 표시 이름입니다. |
| *masterclient*           | MasterClientCertificate에 대 한 감사 로그의 표시 이름, az aks get 자격 증명에서 가져온 인증서 |
| *nodeclient*             | ClientCertificate에 대 한 표시 이름으로, 에이전트 노드에서 사용 됩니다. |

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 Kubernetes 마스터 구성 요소에 대한 로그를 사용하도록 설정하고 검토하는 방법을 알아보았습니다. 추가로 모니터링하고 문제를 해결하려면 [Kubelet 로그를 확인][kubelet-logs]하고 [SSH 노드 액세스를 사용하도록 설정][aks-ssh]하면 됩니다.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
