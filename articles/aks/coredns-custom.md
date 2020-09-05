---
title: AKS (Azure Kubernetes Service)에 대 한 CoreDNS 사용자 지정
description: Azure Kubernetes 서비스 (AKS)를 사용 하 여 하위 도메인을 추가 하거나 사용자 지정 DNS 끝점을 확장 하도록 CoreDNS를 사용자 지정 하는 방법 알아보기
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: e99d841dcfb18b41df128283c37f46682e3fa129
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257118"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service를 사용하여 CoreDNS 사용자 지정

AKS (Azure Kubernetes Service)는 모든 *1.12. x* 이상 클러스터에서 클러스터 DNS 관리 및 확인을 위해 [coredns][coredns] 프로젝트를 사용 합니다. 이전에는 kube 프로젝트를 사용 했습니다. 이 kube 프로젝트는 이제 사용 되지 않습니다. CoreDNS 사용자 지정 및 Kubernetes에 대 한 자세한 내용은 [공식 업스트림 설명서][corednsk8s]를 참조 하세요.

AKS는 관리 되는 서비스 이므로 CoreDNS (a *CoreFile*)에 대 한 기본 구성을 수정할 수 없습니다. 대신 Kubernetes *Configmap* 을 사용 하 여 기본 설정을 재정의 합니다. 기본 AKS CoreDNS ConfigMaps를 보려면 `kubectl get configmaps --namespace=kube-system coredns -o yaml` 명령을 사용 합니다.

이 문서에서는 AKS에서 CoreDNS의 기본 사용자 지정 옵션에 대해 ConfigMaps를 사용 하는 방법을 보여 줍니다. 이 방법은 CoreFile를 사용 하는 것과 같은 다른 컨텍스트에서 CoreDNS를 구성 하는 것과 다릅니다. 구성 값이 버전 간에 변경 될 수 있으므로 실행 중인 CoreDNS의 버전을 확인 합니다.

> [!NOTE]
> `kube-dns` Kubernetes 구성 맵을 통해 다른 [사용자 지정 옵션][kubednsblog] 을 제공 했습니다. CoreDNS는 이전 버전인 kube와 호환 **되지 않습니다** . 이전에 사용 했던 사용자 지정 항목은 CoreDNS에서 사용 하기 위해 업데이트 해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

아래 예제와 같은 구성을 만들 때 *데이터* 섹션의 이름은 *server* 또는 *. override*로 끝나야 합니다. 이 명명 규칙은 명령을 사용 하 여 볼 수 있는 기본 AKS CoreDNS Configmap에 정의 되어 있습니다 `kubectl get configmaps --namespace=kube-system coredns -o yaml` .

## <a name="what-is-supportedunsupported"></a>지원 되는/지원 되지 않는 기능

모든 기본 제공 CoreDNS 플러그 인이 지원 됩니다. 추가 기능/타사 플러그 인은 지원 되지 않습니다.

## <a name="rewrite-dns"></a>DNS 다시 작성

사용자가 수행 하는 한 가지 시나리오는 즉석에서 DNS 이름을 다시 작성 하는 것입니다. 다음 예제에서를 `<domain to be written>` 사용자 고유의 정규화 된 도메인 이름으로 바꿉니다. 이라는 파일을 만들고 `corednsms.yaml` 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> CoreDNS 서비스 IP와 같은 DNS 서버로 리디렉션하는 경우 해당 DNS 서버는 다시 작성 된 도메인 이름을 확인할 수 있어야 합니다.

[Kubectl apply ConfigMap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f corednsms.yaml
```

사용자 지정 항목이 적용 되었는지 확인 하려면 [kubectl 가져오기 configmaps][kubectl-get] 를 사용 하 고 *coredns-custom* configmaps을 지정 합니다.

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

이제 CoreDNS를 강제로 실행 하 여 ConfigMap을 다시 로드 합니다. [Kubectl delete pod][kubectl delete] 명령은 소거식이 아니며 시간을 발생 시 키 지 않습니다. `kube-dns`Pod가 삭제 되 고 Kubernetes Scheduler에서 다시 만듭니다. 이러한 새 pod TTL 값의 변경 내용을 포함 합니다.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> 위의 명령이 올바릅니다. 변경 하는 동안 `coredns` 배포는 **kube** 이름 아래에 있습니다.

## <a name="custom-forward-server"></a>사용자 지정 전달 서버

네트워크 트래픽에 대 한 전달 서버를 지정 해야 하는 경우 ConfigMap을 만들어 DNS를 사용자 지정할 수 있습니다. 다음 예제에서는 `forward` 이름 및 주소를 사용자 환경의 값으로 업데이트 합니다. 이라는 파일을 만들고 `corednsms.yaml` 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

이전 예제와 같이 [kubectl apply ConfigMap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 yaml 매니페스트의 이름을 지정 합니다. 그런 다음 Kubernetes Scheduler에 대 한 [kubectl delete pod][kubectl delete] 를 사용 하 여 Coredns에서 configmap을 다시 로드 하 여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>사용자 지정 도메인 사용

내부적 으로만 확인할 수 있는 사용자 지정 도메인을 구성할 수 있습니다. 예를 들어 유효한 최상위 도메인이 아닌 사용자 지정 도메인 *puglife*를 확인 하는 것이 좋습니다. 사용자 지정 도메인 ConfigMap이 없으면 AKS 클러스터가 주소를 확인할 수 없습니다.

다음 예제에서는 사용자 지정 도메인 및 IP 주소를 업데이트 하 여 사용자 환경에 대 한 값으로 트래픽을 보냅니다. 이라는 파일을 만들고 `corednsms.yaml` 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

이전 예제와 같이 [kubectl apply ConfigMap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 yaml 매니페스트의 이름을 지정 합니다. 그런 다음 Kubernetes Scheduler에 대 한 [kubectl delete pod][kubectl delete] 를 사용 하 여 Coredns에서 configmap을 다시 로드 하 여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>스텁 도메인

CoreDNS는 스텁 도메인을 구성 하는 데도 사용할 수 있습니다. 다음 예제에서는 사용자 지정 도메인 및 IP 주소를 사용자 환경의 값으로 업데이트 합니다. 이라는 파일을 만들고 `corednsms.yaml` 다음 예제 구성을 붙여넣습니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

이전 예제와 같이 [kubectl apply ConfigMap][kubectl-apply] 명령을 사용 하 여 ConfigMap을 만들고 yaml 매니페스트의 이름을 지정 합니다. 그런 다음 Kubernetes Scheduler에 대 한 [kubectl delete pod][kubectl delete] 를 사용 하 여 Coredns에서 configmap을 다시 로드 하 여 다시 만듭니다.

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>호스트 플러그 인

모든 기본 제공 플러그 인이 지원 되므로 CoreDNS [호스트][coredns hosts] 플러그 인을 사용자 지정 하는 것도 가능 합니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS 쿼리 디버깅을 위한 로깅 사용 

DNS 쿼리 로깅을 사용 하도록 설정 하려면 coredns-custom ConfigMap에 다음 구성을 적용 합니다.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 CoreDNS 사용자 지정에 대 한 몇 가지 예제 시나리오를 살펴보았습니다. CoreDNS 프로젝트에 대 한 자세한 내용은 [coredns 업스트림 프로젝트 페이지][coredns]를 참조 하세요.

핵심 네트워크 개념에 대해 자세히 알아보려면 [AKS의 응용 프로그램에 대 한 네트워크 개념][concepts-network]을 참조 하세요.

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
