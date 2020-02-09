---
title: AKS(Azure Kubernetes Service)의 HTTP 애플리케이션 라우팅 추가 기능
description: AKS(Azure Kubernetes Service)의 HTTP 애플리케이션 라우팅 추가 기능을 사용합니다.
services: container-service
author: lachie83
ms.service: container-service
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: fc04e38c3d6933cde81d81d5569ed73e7506a745
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756439"
---
# <a name="http-application-routing"></a>HTTP 애플리케이션 라우팅

HTTP 애플리케이션 라우팅 솔루션을 사용하면 AKS(Azure Kubernetes Service) 클러스터에 배포된 애플리케이션에 쉽게 액세스할 수 있습니다. 솔루션을 사용 하도록 설정 하면 AKS 클러스터에서 [수신 컨트롤러](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) 를 구성 합니다. 애플리케이션이 배포되면 솔루션에서 애플리케이션 엔드포인트에 대해 공개적으로 액세스할 수 있는 DNS 이름도 만듭니다.

추가 기능이 사용하도록 설정되면 구독에 DNS 영역을 만듭니다. DNS 비용에 대한 자세한 내용은 [dns 가격 책정][dns-pricing]을 참조 하세요.

> [!CAUTION]
> HTTP 애플리케이션 라우팅 추가 기능은 수신 컨트롤러를 빠르게 만들고 애플리케이션에 액세스할 수 있도록 설계되었습니다. 이 추가 기능은 프로덕션 용도로 사용하지 않는 것이 좋습니다. 여러 복제본 및 TLS 지원을 포함하는 프로덕션 준비 수신 배포에 대해서는 [HTTPS 수신 컨트롤러 만들기](https://docs.microsoft.com/azure/aks/ingress-tls)를 참조하세요.

## <a name="http-routing-solution-overview"></a>HTTP 라우팅 솔루션 개요

추가 기능에서는 [Kubernetes 수신 컨트롤러][ingress] 와 [외부 DNS][external-dns] 컨트롤러의 두 구성 요소를 배포 합니다.

- **수신 컨트롤러**: 수신 컨트롤러가 LoadBalancer 유형의 Kubernetes 서비스를 사용하여 인터넷에 노출됩니다. 수신 컨트롤러는 응용 프로그램 엔드포인트에 대한 경로를 만드는 [Kubernetes 수신 리소스][ingress-resource]를 감시 하 고 구현 합니다.
- **외부 DNS 컨트롤러**: Kubernetes 수신 리소스를 감시하고 클러스터 특정 DNS 영역에 DNS A 레코드를 만듭니다.

## <a name="deploy-http-routing-cli"></a>HTTP 라우팅 배포: CLI

AKS 클러스터를 배포할 때 Azure CLI를 통해 HTTP 애플리케이션 라우팅 추가 기능을 사용하도록 설정할 수 있습니다. 이렇게 하려면 `--enable-addons` 인수와 함께 [az aks create][az-aks-create] 명령을 사용 합니다.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> 여러 개의 추가 기능을 사용하려는 경우 쉼표로 구분된 목록으로 제공합니다. 예를 들어 HTTP 애플리케이션 라우팅 및 모니터링을 사용하려면 `--enable-addons http_application_routing,monitoring` 형식을 사용합니다.

[Az AKS addons][az-aks-enable-addons] 명령을 사용 하 여 기존 AKS 클러스터에서 HTTP 라우팅을 사용 하도록 설정할 수도 있습니다. 기존 클러스터에서 HTTP 라우팅을 활성화하려면 `--addons` 매개 변수를 추가하고 다음 예제에서와 같이 *http_application_routing*을 지정합니다.

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

클러스터를 배포 하거나 업데이트 한 후에는 [az aks show][az-aks-show] 명령을 사용 하 여 DNS 영역 이름을 검색 합니다. 이 이름은 애플리케이션을 AKS 클러스터에 배포하는 데 필요합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table
```

결과

9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io


## <a name="deploy-http-routing-portal"></a>HTTP 라우팅 배포: Portal

AKS 클러스터를 배포할 때 Azure Portal을 통해 HTTP 애플리케이션 라우팅 추가 기능을 사용하도록 설정할 수 있습니다.

![HTTP 라우팅 기능을 사용하도록 설정](media/http-routing/create.png)

클러스터가 배포되면 자동 생성 AKS 리소스 그룹을 찾아 DNS 영역을 선택합니다. DNS 영역 이름을 기록해 둡니다. 이 이름은 애플리케이션을 AKS 클러스터에 배포하는 데 필요합니다.

![DNS 영역 이름 가져오기](media/http-routing/dns.png)

## <a name="use-http-routing"></a>HTTP 라우팅 사용

HTTP 애플리케이션 라우팅 솔루션은 다음과 같이 주석 처리된 수신 리소스에서만 트리거될 수 있습니다.

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

**samples-http-application-routing.yaml**이라는 파일을 만들고 다음 YAML을 복사합니다. 줄 43에서, 이 문서의 이전 단계에서 수집한 DNS 영역 이름으로 `<CLUSTER_SPECIFIC_DNS_ZONE>`을 업데이트합니다.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 리소스를 만듭니다.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

cURL 또는 브라우저를 사용하여 samples-http-application-routing.yaml 파일의 호스트 섹션에 지정된 호스트 이름으로 이동합니다. 인터넷을 통해 애플리케이션을 사용할 수 있기까지 최대 1분 정도 걸릴 수 있습니다.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>HTTP 라우팅 제거

Azure CLI를 사용하여 HTTP 라우팅 솔루션을 제거할 수 있습니다. 이렇게 하려면 AKS 클러스터 및 리소스 그룹 이름을 대체하는 다음 명령을 실행합니다.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

HTTP 애플리케이션 라우팅 추가 기능을 비활성화하면 일부 Kubernetes 리소스는 클러스터에서 남아 있을 수 있습니다. 이러한 리소스는 *configMaps* 및 *secrets*를 포함하며 *kube 시스템* 네임스페이스에서 만들어집니다. 정리 클러스터를 유지하려면 이러한 리소스를 제거하는 것이 좋습니다.

다음 [kubectl get][kubectl-get] 명령을 사용 하 여 *추가 기능-http-응용 프로그램 라우팅* 리소스를 찾습니다.

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

다음 예제 출력은 삭제되어야 하는 configMaps를 보여줍니다.

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

리소스를 삭제 하려면 [kubectl delete][kubectl-delete] 명령을 사용 합니다. 리소스 종류, 리소스 이름 및 네임스페이스를 지정합니다. 다음 예제에서는 이전 configmaps 중 하나를 삭제합니다.

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

클러스터에 남아 있던 모든 *addon-http-application-routing* 리소스에 대해 이전 `kubectl delete` 단계를 반복합니다.

## <a name="troubleshoot"></a>문제 해결

[Kubectl logs][kubectl-logs] 명령을 사용 하 여 외부 DNS 응용 프로그램의 응용 프로그램 로그를 볼 수 있습니다. 로그에서 A 및 TXT DNS 레코드가 성공적으로 만들어졌음을 확인합니다.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Azure Portal의 DNS 영역 리소스에서 이러한 레코드를 볼 수도 있습니다.

![DNS 레코드 가져오기](media/http-routing/clippy.png)

[Kubectl logs][kubectl-logs] 명령을 사용 하 여 Nginx 수신 컨트롤러에 대한 응용 프로그램 로그를 확인 합니다. 로그에서 수신 리소스 `CREATE` 및 컨트롤러 다시 로드를 확인합니다. 모든 HTTP 작업이 기록됩니다.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>정리

이 문서에서 만든 연결된 Kubernetes 개체를 제거합니다.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>다음 단계

AKS에서 HTTPS 보안 수신 컨트롤러를 설치 하는 방법에 대한 자세한 내용은 [AKS (Azure Kubernetes Service)에서 Https 수신][ingress-https]을 참조 하세요.

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
