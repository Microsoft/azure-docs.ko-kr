---
title: Azure Kubernetes 서비스 (AKS)에서 Consul 설치
description: Consul을 설치 하 고 사용 하 여 AKS (Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법에 대해 알아봅니다.
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5d0a575c3fb662df034b66a48135ac33393f95c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885399"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 Consul 설치 및 사용

[Consul][consul-github] 은 Kubernetes 클러스터의 마이크로 서비스에서 주요 기능 집합을 제공 하는 오픈 소스 서비스 메시입니다. 이러한 기능에는 서비스 검색, 상태 검사, 서비스 구분 및 관찰성 포함 됩니다. Consul에 대한 자세한 내용은 공식 이란 [?][consul-docs-concepts] 설명서를 참조 하세요.

이 문서에서는 Consul을 설치 하는 방법을 보여 줍니다. Consul 구성 요소는 AKS의 Kubernetes 클러스터에 설치 됩니다.

> [!NOTE]
> 이 지침은 Consul 버전 `1.6.0`을 참조 하 고 최소한의 투구 버전 `2.14.2`를 사용 합니다.
>
> Kubernetes 버전 `1.13+`에 대해 Consul `1.6.x` 릴리스를 실행할 수 있습니다. [GitHub][consul-github-releases] 에서 추가 기능을 사용할 수 [있습니다. 릴리스][consul-release-notes]정보에서 각 릴리스에 대한 정보를 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * AKS에 Consul 구성 요소 설치
> * Consul 설치 유효성 검사
> * AKS에서 Consul 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 설명 된 단계에서는 AKS 클러스터 (RBAC를 사용 하 여 Kubernetes `1.13` 이상)를 만들고 클러스터와 `kubectl` 연결을 설정 했다고 가정 합니다. 이러한 항목에 대한 도움이 필요한 경우 [AKS 빠른][aks-quickstart]시작을 참조 하세요. 클러스터의 Linux 노드 풀에 노드가 3 개 이상 있는지 확인 합니다.

이러한 지침을 따르고 Consul을 설치 하는 [투구][helm] 가 필요 합니다. 클러스터에 제대로 설치 되 고 구성 된 안정적인 최신 버전을 설치 하는 것이 좋습니다. 투구 설치와 관련 하 여 도움이 필요한 경우 [AKS 투구 설치 지침][helm-install]을 참조 하세요. 모든 Consul pod는 Linux 노드에서 실행 되도록 예약 되어야 합니다.

이 문서에서는 Consul 설치 지침을 여러 불연속 단계로 구분 합니다. 최종 결과는 공식 Consul 설치 [지침][consul-install-k8]과 동일한 구조에서 동일 합니다.

### <a name="install-the-consul-components-on-aks"></a>AKS에 Consul 구성 요소 설치

먼저 Consul 투구 차트의 버전 `v0.10.0`을 다운로드 합니다. 이 버전의 차트에는 `1.6.0`포함 되어 있습니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

투구 및 다운로드 한 `consul-helm` 차트를 사용 하 여 AKS 클러스터의 `consul` 네임 스페이스에 Consul 구성 요소를 설치 합니다. 

> [!NOTE]
> **설치 옵션**
> 
> 설치의 일부로 다음 옵션을 사용 하 고 있습니다.
> - `connectInject.enabled=true`-프록시를 pod에 삽입할 수 있습니다.
> - `client.enabled=true`-모든 노드에서 실행 될 수 있는 Consul 클라이언트
> - `client.grpc=true`-connectInject 용 gRPC 수신기 사용
> - `syncCatalog.enabled=true` 동기화 Kubernetes 및 Consul 서비스
>
> **노드 선택기**
>
> 현재는 Linux 노드에서 실행 되도록 예약 해야 합니다. 클러스터에 Windows Server 노드가 있는 경우 Consul pod Linux 노드에서 실행 되도록 예약 되어 있는지 확인 해야 합니다. [노드 선택기][kubernetes-node-selectors] 를 사용 하 여 pod이 올바른 노드에 예약 되었는지 확인 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` 투구 차트는 많은 개체를 배포 합니다. 위의 `helm install` 명령 출력에서 목록을 볼 수 있습니다. 클러스터 환경에 따라 Consul 구성 요소의 배포를 완료 하는 데 3 분 정도 걸릴 수 있습니다.

이 시점에서 AKS 클러스터에 Consul을 배포 했습니다. Consul이 성공적으로 배포 되었는지 확인 하려면 다음 섹션으로 이동 하 여 Consul 설치의 유효성을 검사 해 보겠습니다.

## <a name="validate-the-consul-installation"></a>Consul 설치 유효성 검사

리소스가 성공적으로 만들어졌는지 확인 합니다. [Kubectl get svc][kubectl-get] 및 [kubectl get pod][kubectl-get] 명령을 사용 하 여 `helm install` 명령을 통해 consul 구성 요소가 설치 된 `consul` 네임 스페이스를 쿼리 합니다.

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

다음 예제 출력에서는 현재 실행 되어야 하는 서비스 및 pod (Linux 노드에 예약 됨)를 보여 줍니다.

```console
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

모든 pod는 `Running`상태를 표시 해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고 하는 경우 [kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 출력 및 상태를 검토 합니다.

## <a name="accessing-the-consul-ui"></a>Consul UI 액세스

Consul UI는 위의 설정에서 설치 되었으며 Consul에 대한 UI 기반 구성을 제공 합니다. Consul의 UI는 외부 ip 주소를 통해 공개적으로 노출 되지 않습니다. Consul 사용자 인터페이스에 액세스 하려면 [kubectl][kubectl-port-forward] 명령을 사용 합니다. 이 명령은 클라이언트 컴퓨터와 AKS 클러스터의 관련 pod 간에 보안 연결을 만듭니다.

```azurecli
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

이제 브라우저를 열고 `http://localhost:8080/ui` 하 여 Consul UI를 열 수 있습니다. UI를 열 때 다음이 표시 됩니다.

![Consul UI](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS에서 Consul 제거

> [!WARNING]
> 실행 중인 시스템에서 Consul을 삭제 하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 하기 전에 시스템에 대한 프로 비전을 제대로 작동 하는지 확인 합니다.

### <a name="remove-consul-components-and-namespace"></a>Consul 구성 요소 및 네임 스페이스 제거

AKS 클러스터에서 Consul을 제거 하려면 다음 명령을 사용 합니다. `helm delete` 명령은 `consul` 차트를 제거 하 고 `kubectl delete namespace` 명령은 `consul` 네임 스페이스를 제거 합니다.

```azurecli
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>다음 단계

Consul에 대한 추가 설치 및 구성 옵션을 살펴보려면 다음 공식 기술 문서를 참조 하세요.

- [Consul-투구 설치 가이드][consul-install-k8]
- [Consul-투구 설치 옵션][consul-install-helm-options]

다음을 사용 하 여 추가 시나리오를 따를 수도 있습니다.

- [Consul 예제 응용 프로그램][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
