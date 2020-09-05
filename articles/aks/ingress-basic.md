---
title: 수신 컨트롤러 만들기
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service) 클러스터에 기본 NGINX 수신 컨트롤러를 설치하고 구성하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 9ab177e2756227f3893d13c97d12ad67cfb1ff62
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855827"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 [투구 3][helm] 을 사용 하 여 NGINX 수신 컨트롤러를 설치 합니다. 최신 버전의 투구를 사용 하 고 있으며 *수신-nginx* 투구 리포지토리에 액세스할 수 있는지 확인 합니다.

또한이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

수신 컨트롤러를 만들려면 투구를 사용 하 여 *nginx*를 설치 합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예에서는 수신 *-기본*이라는 수신 리소스에 대 한 Kubernetes 네임 스페이스를 만듭니다. 필요에 따라 사용자 환경에 대 한 네임 스페이스를 지정 합니다.

> [!TIP]
> 클러스터의 컨테이너에 대 한 요청에 대 한 [클라이언트 원본 IP 유지][client-source-ip] 를 사용 하도록 설정 하려면 `--set controller.service.externalTrafficPolicy=Local` 투구 install 명령에를 추가 합니다. 클라이언트 원본 IP가 *X 전달-에 대 한*요청 헤더에 저장 됩니다. 클라이언트 원본 IP 유지를 사용 하는 수신 컨트롤러를 사용 하는 경우 SSL 통과는 작동 하지 않습니다.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 동적 공용 IP 주소를 할당합니다.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

아직 수신 규칙이 만들어지지 않았으므로 내부 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

작동 중인 수신 컨트롤러를 확인 하려면 AKS 클러스터에서 두 개의 데모 응용 프로그램을 실행 합니다. 이 예제에서는 `kubectl apply` 를 사용 하 여 간단한 *Hello 세계* 응용 프로그램의 두 인스턴스를 배포 합니다.

Aks 파일을 만들고 다음 예제 YAML에 복사 *합니다* .

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-one  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Aks 파일을 만들고 다음 예제 YAML에 복사 *합니다* .

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two  
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
        image: neilpeterson/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld-two  
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

다음을 사용 하 여 두 개의 데모 응용 프로그램을 실행 합니다 `kubectl apply` .

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행됩니다. 트래픽을 각 애플리케이션으로 라우팅하려면 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 *EXTERNAL_IP* 에 대 한 트래픽은 이라는 서비스로 라우팅됩니다 `aks-helloworld-one` . *EXTERNAL_IP/hello-world-two* 에 대 한 트래픽은 서비스로 라우팅됩니다 `aks-helloworld-two` . *EXTERNAL_IP/정적* 으로의 트래픽은 정적 자산의 이라는 서비스로 라우팅됩니다 `aks-helloworld-one` .

*Hello-wwpn. yaml* 이라는 파일을 만들고 다음 예제 yaml에 복사 합니다.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>수신 컨트롤러 테스트

수신 컨트롤러의 경로를 테스트하려면 두 개의 애플리케이션으로 이동합니다. NGINX 수신 컨트롤러의 IP 주소 (예: *EXTERNAL_IP*)에 대 한 웹 브라우저를 엽니다. 첫 번째 데모 애플리케이션은 다음 예제와 같이 웹 브라우저에 표시됩니다.

![수신 컨트롤러 뒤에서 실행 중인 첫 번째 앱](media/ingress-basic/app-one.png)

이제 *EXTERNAL_IP/hello-world-two*와 같은 IP 주소에 */hello-world-two* 경로를 추가 합니다. 사용자 지정 제목이 있는 두 번째 데모 애플리케이션이 표시됩니다.

![수신 컨트롤러 뒤에서 실행 중인 두 번째 앱](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리 하려면 전체 샘플 네임 스페이스 또는 개별 리소스를 삭제할 수 있습니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임 스페이스 및 모든 리소스 삭제

전체 샘플 네임 스페이스를 삭제 하려면 명령을 사용 하 `kubectl delete` 고 네임 스페이스 이름을 지정 합니다. 네임 스페이스의 모든 리소스가 삭제 됩니다.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제

또는 만든 개별 리소스를 삭제 하는 것이 더 세부적인 방법입니다. 명령을 사용 하 여 투구 릴리스를 나열 `helm list` 합니다. 다음 예제 출력과 같이 이름이 *nginx-ingress* 및 *aks-helloworld*인 차트를 찾습니다.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

명령을 사용 하 여 릴리스를 제거 합니다 `helm uninstall` . 다음 예제에서는 NGINX 수신 배포를 제거 합니다.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

다음으로 두 개의 샘플 응용 프로그램을 제거 합니다.

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml
```

마지막으로 자체 네임 스페이스를 삭제할 수 있습니다. 명령을 사용 `kubectl delete` 하 여 네임 스페이스 이름을 지정 합니다.

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]

다음도 가능합니다.

- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
