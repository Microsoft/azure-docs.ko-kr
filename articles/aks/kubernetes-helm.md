---
title: AKS에서 투구를 사용 하 여 기존 응용 프로그램 설치
description: 투구 패키징 도구를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에 컨테이너를 배포 하는 방법을 알아봅니다.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 06/24/2020
ms.author: zarhoads
ms.openlocfilehash: d05d0166724e586fa79e58e2e74fb583b45d0cc6
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852866"
---
# <a name="install-existing-applications-with-helm-in-azure-kubernetes-service-aks"></a>AKS (Azure Kubernetes Service)에서 투구를 사용 하 여 기존 응용 프로그램 설치

[투구][helm] 는 Kubernetes 응용 프로그램의 수명 주기를 설치 하 고 관리 하는 데 도움이 되는 오픈 소스 패키징 도구입니다. *APT* , *Yum*등의 Linux 패키지 관리자와 마찬가지로, 투구는 미리 구성 된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리 하는 데 사용 됩니다.

이 아티클에서는 AKS의 Kubernetes 클러스터에서 Helm을 구성하고 사용하는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

또한 개발 시스템에서 실행 되는 클라이언트 인 투구 CLI가 설치 되어 있어야 합니다. 이를 통해 투구로 응용 프로그램을 시작, 중지 및 관리할 수 있습니다. Azure Cloud Shell을 사용하는 경우 Helm CLI가 이미 설치되어 있습니다. 로컬 플랫폼에 대 한 설치 지침은 [투구 설치][helm-install]를 참조 하세요.

> [!IMPORTANT]
> 투구는 Linux 노드에서 실행 하기 위한 것입니다. 클러스터에 Windows Server 노드가 있는 경우에는 투구 pod가 Linux 노드에서 실행 되도록 예약 되어 있는지 확인 해야 합니다. 또한 설치 하는 투구 차트가 올바른 노드에서 실행 되도록 예약 되어 있는지도 확인 해야 합니다. 이 문서의 명령은 [노드 선택기] [k8s]를 사용 하 여 pod이 올바른 노드에 예약 되어 있는지 확인 하지만 일부 투구 차트는 노드 선택기를 표시할 수 없습니다. [Taints][taints]와 같은 클러스터의 다른 옵션을 사용 하는 것을 고려할 수도 있습니다.

## <a name="verify-your-version-of-helm"></a>투구 버전 확인

명령을 사용 하 여 `helm version` 투구 3이 설치 되어 있는지 확인 합니다.

```console
helm version
```

다음 예제에서는 3.0.0 설치 된 투구 버전을 보여 줍니다.

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

## <a name="install-an-application-with-helm-v3"></a>투구 v3을 사용 하 여 응용 프로그램 설치

### <a name="add-helm-repositories"></a>투구 리포지토리 추가

[투구 리포지토리][helm-repo-add] 명령을 사용 하 여 공식 투구 안정적인 차트와 *수신-nginx* 리포지토리를 추가 합니다.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

### <a name="find-helm-charts"></a>Helm 차트 찾기

Helm 차트는 Kubernetes 클러스터에 애플리케이션을 배포하는 데 사용됩니다. 미리 만든 투구 차트를 검색 하려면 [투구 검색][helm-search] 명령을 사용 합니다.

```console
helm search repo stable
```

다음 압축된 예제 출력에서는 사용할 Helm 차트의 일부를 보여줍니다.

```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

차트 목록을 업데이트하려면 [helm repo update][helm-repo-update] 명령을 사용합니다.

```console
helm repo update
```

다음 예제에서는 성공적인 리포지토리 업데이트를 보여줍니다.

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm 차트 실행

투구를 사용 하 여 차트를 설치 하려면 [투구 install][helm-install-command] 명령을 사용 하 고 릴리스 이름 및 설치할 차트 이름을 지정 합니다. 동작에서 투구 차트 설치를 보려면 투구 차트를 사용 하 여 기본 nginx 배포를 설치 해 보겠습니다.

```console
helm install my-nginx-ingress ingress-nginx/ingress-nginx \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

다음 압축된 예제 출력에서는 Helm 차트에서 만든 Kubernetes 리소스의 배포 상태를 보여줍니다.

```console
$ helm install my-nginx-ingress ingress-nginx/ingress-nginx \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller'
...
```

명령을 사용 `kubectl get services` 하 여 서비스의 *외부 IP* 를 가져옵니다.

```console
kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller
```

예를 들어 아래 명령은 *nginx-nginx* 서비스의 *외부 IP* 를 보여 줍니다.

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-ingress-nginx-controller

NAME                                        TYPE           CLUSTER-IP   EXTERNAL-IP      PORT(S)                      AGE   SELECTOR
my-nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.2.237   <EXTERNAL-IP>    80:31380/TCP,443:32239/TCP   72s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=my-nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

### <a name="list-releases"></a>릴리스 목록

클러스터에 설치 된 릴리스 목록을 보려면 `helm list` 명령을 사용 합니다.

```console
helm list
```

다음 예제에서는 이전 단계에서 배포한 *nginx 수신* 릴리스를 보여 줍니다.

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>리소스 정리

Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이러한 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리 하려면 [투구 uninstall][helm-cleanup] 명령을 사용 하 여 이전 명령에 나와 있는 대로 릴리스 이름을 지정 `helm list` 합니다.

```console
helm uninstall my-nginx-ingress
```

다음 예제에서는 *nginx* 라는 릴리스가 제거 된 것을 보여 줍니다.

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="next-steps"></a>다음 단계

Helm을 사용하여 Kubernetes 애플리케이션 배포를 관리하는 방법에 대한 자세한 내용은 Helm 설명서를 참조하세요.

> [!div class="nextstepaction"]
> [Helm 설명서][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[taints]: operator-best-practices-advanced-scheduler.md
