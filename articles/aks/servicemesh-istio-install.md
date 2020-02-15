---
title: Azure Kubernetes Service(AKS)에서 Istio 설치
description: Istio를 설치 및 사용하여 AKS(Azure Kubernetes Service) 클러스터에서 서비스 메시를 만드는 방법을 알아봅니다.
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d886205e88db780a7a09554391bd975f57eebfe7
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251739"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service(AKS)에서 Istio 설치 및 사용

[Istio][istio-github] 는 Kubernetes 클러스터의 마이크로 서비스에서 주요 기능 집합을 제공 하는 오픈 소스 서비스 메시입니다. 이 기능에는 트래픽 관리, 서비스 ID 및 보안, 정책 적용 및 관찰성을 포함됩니다. Istio에 대 한 자세한 내용은 [Istio 란 공식 이란?][istio-docs-concepts] 설명서를 참조 하세요.

이 문서에서는 Istio를 설치하는 방법을 보여줍니다. Istio `istioctl` 클라이언트 이진 파일은 클라이언트 컴퓨터에 설치 되 고 Istio 구성 요소는 AKS의 Kubernetes 클러스터에 설치 됩니다.

> [!NOTE]
> 다음 지침은 Istio 버전 `1.4.0`를 참조 합니다.
>
> Istio `1.4.x` 릴리스는 `1.13`, `1.14`, `1.15`Kubernetes 버전에 대해 Istio 팀에서 테스트 했습니다. [GitHub][istio-github-releases]에서 추가 istio 버전, [istio News][istio-release-notes] 의 각 릴리스에 대 한 정보 및 [istio 일반 FAQ][istio-faq]에서 지원 되는 Kubernetes 버전을 찾을 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Istio istio 클라이언트 이진 파일을 다운로드 하 여 설치 합니다.
> * AKS에 Istio 설치
> * Istio 설치 유효성 검사
> * 추가 기능에 액세스
> * AKS에서 Istio 제거

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 설명 된 단계에서는 AKS 클러스터 (RBAC를 사용 하 여 Kubernetes `1.13` 이상)를 만들고 클러스터와 `kubectl` 연결을 설정 했다고 가정 합니다. 이러한 항목에 대 한 도움이 필요한 경우 [AKS 빠른][aks-quickstart]시작을 참조 하세요.

AKS 클러스터에서 Istio를 실행 하기 위한 추가 리소스 요구 사항을 이해 하려면 [Istio 성능 및 확장성](https://istio.io/docs/concepts/performance-and-scalability/) 설명서를 확인 해야 합니다. 코어 및 메모리 요구 사항은 특정 워크 로드에 따라 달라 집니다. 설치에 사용할 적절 한 수의 노드 및 VM 크기를 선택 합니다.

이 문서에서는 몇 단계로 Istio 설치 지침을 구분합니다. 최종 결과는 공식 Istio 설치 [지침][istio-install-istioctl]과 동일한 구조에서 동일 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>AKS에 Istio 구성 요소를 설치 합니다.

[Grafana][grafana] 및 [Kiali][kiali] 를 설치 하는 과정을 istio 설치의 일부로 설치 합니다. Grafana는 분석 및 모니터링 대시보드를 제공 하 고 Kiali는 서비스 메시 관찰성 대시보드를 제공 합니다. 설치 프로그램에서 이러한 각 구성 요소에는 [암호로][kubernetes-secrets]제공 되어야 하는 자격 증명이 필요 합니다.

Istio 구성 요소를 설치 하기 전에 Grafana 및 Kiali 모두에 대 한 암호를 만들어야 합니다. 이러한 암호는 Istio에서 사용 되는 `istio-system` 네임 스페이스에 설치 해야 하므로 네임 스페이스를 만들어야 합니다. `kubectl create`를 통해 네임 스페이스를 만들 때 `--save-config` 옵션을 사용 하 여 Istio 설치 관리자가 나중에이 개체에 대해 `kubectl apply`를 실행할 수 있도록 해야 합니다.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio 구성 요소 설치

이제 AKS 클러스터에 Grafana 및 Kiali 비밀을 성공적으로 만들었으므로 Istio 구성 요소를 설치 해야 합니다. 

Istio에 대 한 [투구][helm] 설치 접근 방식은 나중에 사용 되지 않습니다. Istio에 대 한 새로운 설치 방법은 `istioctl` 클라이언트 이진, [istio 구성 프로필][istio-configuration-profiles]및 새 [istio 제어 평면 사양 및 api][istio-control-plane]를 활용 합니다. 이 새로운 방법은 Istio를 설치 하기 위해 사용 하는 것입니다.

> [!NOTE]
> Istio는 현재 Linux 노드에서 실행 되도록 예약 되어야 합니다. 클러스터에 Windows Server 노드가 있는 경우 Istio pod Linux 노드에서 실행 되도록 예약 되어 있는지 확인 해야 합니다. [노드 선택기][kubernetes-node-selectors] 를 사용 하 여 pod이 올바른 노드에 예약 되었는지 확인 합니다.

> [!CAUTION]
> [SDS (비밀 검색 서비스)][istio-feature-sds] 및 [istio cni][istio-feature-cni] Istio 기능은 현재 [알파][istio-feature-stages]에 있으므로 이러한 기능을 사용 하도록 설정 하기 전에 고려해 야 합니다. 
>
> 이제 AKS의 모든 Kubernetes 1.13 이상 버전에 대 한 [서비스 계정 토큰 Volume 프로젝션][kubernetes-feature-sa-projected-volume] Kubernetes 기능 (SDS에 대 한 요구 사항)이 **활성화** 됩니다.

다음 콘텐츠를 사용 하 여 `istio.aks.yaml` 라는 파일을 만듭니다. 이 파일은 Istio를 구성 하기 위한 [istio 제어 평면 사양][istio-control-plane] 세부 정보를 포함 합니다.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

다음과 같이 `istioctl apply` 명령과 위의 `istio.aks.yaml` Istio 제어 평면 사양 파일을 사용 하 여 istio를 설치 합니다.

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

설치 관리자는 다양 한 [Crds][kubernetes-crd] 를 배포한 다음 종속성을 관리 하 여이 istio 구성에 대해 정의 된 관련 개체를 모두 설치 합니다. 다음 출력 코드 조각과 유사한 내용이 표시 되어야 합니다.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

이제 AKS 클러스터에 Istio를 배포 했습니다. Istio를 성공적으로 배포 하기 위해 다음 섹션으로 이동 하 여 [istio 설치의 유효성을 검사](#validate-the-istio-installation)해 보겠습니다.

## <a name="validate-the-istio-installation"></a>Istio 설치 유효성 검사

먼저 예상 서비스가 만들어졌는지 확인합니다. [Kubectl get svc][kubectl-get] 명령을 사용 하 여 실행 중인 서비스를 볼 수 있습니다. `istio` 투구 차트에서 Istio 및 추가 기능 구성 요소가 설치 된 `istio-system` 네임 스페이스를 쿼리 합니다.

```console
kubectl get svc --namespace istio-system --output wide
```

다음 예제 출력에서는 실행되어야 하는 서비스를 보여줍니다.

- `istio-*` 서비스
- `jaeger-*`, `tracing`및 `zipkin` 추가 기능 추적 서비스
- 추가 기능 메트릭 서비스 `prometheus`
- `grafana` 추가 기능 분석 및 모니터링 대시보드 서비스
- `kiali` 추가 기능 서비스 메시 대시보드 서비스

`istio-ingressgateway`에서 `<pending>`의 외부 IP를 표시하는 경우 Azure 네트워킹에서 IP 주소가 할당될 때까지 몇 분이 걸립니다.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

다음으로 필요한 Pod가 만들어졌는지 확인합니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 `istio-system` 네임 스페이스를 다시 쿼리 합니다.

```console
kubectl get pods --namespace istio-system
```

다음 예제 출력에서는 실행 중인 Pod을 보여줍니다.

- `istio-*` pod
- `prometheus-*` 추가 기능 메트릭 pod
- `grafana-*` 추가 기능 분석 및 모니터링 대시보드 pod
- `kiali` 추가 기능 서비스 메시 대시보드 pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

모든 pod는 `Running`상태를 표시 해야 합니다. Pod에서 상태가 표시되지 않는 경우 상태가 표시될 때까지 1~2분 정도 걸릴 수 있습니다. Pod에서 문제를 보고 하는 경우 [kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 출력 및 상태를 검토 합니다.

## <a name="accessing-the-add-ons"></a>추가 항목 액세스

추가 기능을 제공 하는 위의 설정에서 Istio에 의해 많은 추가 기능이 설치 되었습니다. 추가 기능에 대 한 웹 응용 프로그램은 외부 ip 주소를 통해 공개적으로 노출 **되지 않습니다** . 

추가 기능 사용자 인터페이스에 액세스 하려면 `istioctl dashboard` 명령을 사용 합니다. 이 명령은 [kubectl 포트 전달][kubectl-port-forward] 및 임의 포트를 활용 하 여 클라이언트 컴퓨터와 AKS 클러스터의 관련 pod 간에 보안 연결을 만듭니다. 그런 다음 기본 브라우저에서 추가 기능 웹 응용 프로그램을 자동으로 엽니다.

이 문서 앞부분에서 Grafana 및 Kiali에 대 한 자격 증명을 지정 하 여 추가 보안 계층을 추가 했습니다.

### <a name="grafana"></a>Grafana

Istio에 대 한 분석 및 모니터링 대시보드는 [Grafana][grafana]에서 제공 합니다. 메시지가 표시 되 면 이전에 Grafana 암호를 통해 만든 자격 증명을 사용 해야 합니다. 다음과 같이 Grafana 대시보드를 안전 하 게 엽니다.

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Istio에 대 한 메트릭은 [프로메테우스][prometheus]에서 제공 합니다. 다음과 같이 안전 하 게 프로메테우스 대시보드를 엽니다.

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 내의 추적은 [Jaeger][jaeger]에서 제공 합니다. 다음과 같이 Jaeger 대시보드를 안전 하 게 엽니다.

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

서비스 메시 관찰성 대시보드는 [Kiali][kiali]에서 제공 합니다. 메시지가 표시 되 면 이전에 Kiali 암호를 통해 만든 자격 증명을 사용 해야 합니다. 다음과 같이 Kiali 대시보드를 안전 하 게 엽니다.

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[엔보이][envoy] 프록시에 대 한 간단한 인터페이스를 사용할 수 있습니다. 지정 된 pod에서 실행 되는 엔보이 프록시에 대 한 구성 정보 및 메트릭을 제공 합니다. 다음과 같이 엔보이 인터페이스를 안전 하 게 엽니다.

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>AKS에서 Istio 제거

> [!WARNING]
> 실행 중인 시스템에서 Istio를 삭제 하면 서비스 간에 트래픽 관련 문제가 발생할 수 있습니다. 계속 하기 전에 Istio 없이 제대로 작동 하도록 시스템에 대 한 프로 비전을 수행 했는지 확인 합니다.

### <a name="remove-istio-components-and-namespace"></a>Istio 구성 요소 및 네임 스페이스 제거

AKS 클러스터에서 Istio를 제거 하려면 `istio.aks.yaml` Istio 제어 평면 사양 파일과 함께 `istioctl manifest generate` 명령을 사용 합니다. 그러면 설치 된 모든 구성 요소와 `istio-system` 네임 스페이스를 제거 하기 위해 `kubectl delete`에 파이프 하는 배포 된 매니페스트가 생성 됩니다.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRDs 및 비밀 제거

위의 명령은 Istio 구성 요소와 네임 스페이스를 모두 삭제 하지만 Istio 비밀이 생성 된 상태로 남아 있습니다. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>다음 단계

다음 설명서에서는 Istio를 사용 하 여 카나리아 릴리스를 롤아웃하기 위한 지능형 라우팅을 제공 하는 방법을 설명 합니다.

> [!div class="nextstepaction"]
> [AKS Istio 인텔리전트 라우팅 시나리오][istio-scenario-routing]

Istio에 대 한 추가 설치 및 구성 옵션을 살펴보려면 다음 공식 Istio 지침을 참조 하세요.

- [Istio-설치 가이드][istio-installation-guides]

다음을 사용 하 여 추가 시나리오를 따를 수도 있습니다.

- [Istio Bookinfo 응용 프로그램 예제][istio-bookinfo-example]

Application Insights 및 Istio를 사용 하 여 AKS 응용 프로그램을 모니터링 하는 방법에 대 한 자세한 내용은 다음 Azure Monitor 설명서를 참조 하세요.

- [Kubernetes 호스팅된 응용 프로그램에 대 한 제로 계측 응용 프로그램 모니터링][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
