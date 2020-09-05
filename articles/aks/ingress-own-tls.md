---
title: 수신에 TLS 인증서 사용
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service) 클러스터에서 고유한 인증서를 사용하는 NGINX 수신 컨트롤러를 설치하고 구성하는 방법을 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 42e9f2128063caa13cf3fca1a28ec7e6465ba74e
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855686"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에 HTTPS 수신 컨트롤러를 만들고 고유한 TLS 인증서 사용

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 사용자 고유의 인증서를 생성하고 수신 경로에 사용하기 위해 Kubernetes 비밀을 만듭니다. 마지막으로, 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 [투구 3][helm] 을 사용 하 여 NGINX 수신 컨트롤러를 설치 합니다. 최신 버전의 투구를 사용 하 고 있으며 *수신-nginx* 투구 리포지토리에 액세스할 수 있는지 확인 합니다. 업그레이드 지침은 [투구 설치 문서][helm-install]를 참조 하세요. 투구 구성 및 사용에 대 한 자세한 내용은 [Azure Kubernetes 서비스에서 투구를 사용 하 여 응용 프로그램 설치 (AKS)][use-helm]를 참조 하세요.

또한이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

수신 컨트롤러를 만들려면 `Helm`을 사용하여 *nginx-ingress*를 설치합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예에서는 수신 *-기본*이라는 수신 리소스에 대 한 Kubernetes 네임 스페이스를 만듭니다. 필요에 따라 사용자 환경에 대 한 네임 스페이스를 지정 합니다. AKS 클러스터가 RBAC를 사용 하도록 설정 되지 않은 경우 `--set rbac.create=false` 투구 명령에를 추가 합니다.

> [!TIP]
> 클러스터의 컨테이너에 대 한 요청에 대 한 [클라이언트 원본 IP 유지][client-source-ip] 를 사용 하도록 설정 하려면 `--set controller.service.externalTrafficPolicy=Local` 투구 install 명령에를 추가 합니다. 클라이언트 원본 IP가 *X 전달-에 대 한*요청 헤더에 저장 됩니다. 클라이언트 원본 IP 유지를 사용 하는 수신 컨트롤러를 사용 하는 경우 TLS 통과는 작동 하지 않습니다.

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

설치하는 동안 Azure 공용 IP 주소가 수신 컨트롤러에 대해 생성됩니다. 이 공용 IP 주소는 수신 컨트롤러의 수명 동안만 고정됩니다. 수신 컨트롤러를 삭제하면 공용 IP 주소 할당이 손실됩니다. 추가 수신 컨트롤러를 만들면 새 공용 IP 주소가 할당됩니다. 공용 IP 주소를 계속 사용하려는 경우에는 대신 [고정 공용 IP 주소로 수신 컨트롤러를 만들][aks-ingress-static-tls] 수 있습니다.

공용 IP 주소를 얻으려면 `kubectl get service` 명령을 사용합니다.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

서비스에 IP 주소가 할당될 때까지 몇 분 정도 걸립니다.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

배포를 테스트하는 마지막 단계에서 사용되므로 이 공용 IP 주소를 기록해 둡니다.

수신 규칙은 아직 생성되지 않았습니다. 공용 IP 주소로 이동하면, NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다.

## <a name="generate-tls-certificates"></a>TLS 인증서 생성

이 문서에서는`openssl`을 사용하여 자체 서명된 인증서를 생성해 보겠습니다. 프로덕션 사용의 경우 공급자 또는 고유의 CA(인증 기관)를 통해 신뢰할 수 있는 서명된 인증서를 요청해야 합니다. 다음 단계에서는 TLS 인증서와 OpenSSL에서 생성한 프라이빗 키를 사용하여 Kubernetes *비밀*을 생성합니다.

다음 예제에서는 *aks-ingress-tls.crt*라는 365일 동안 유효한 2048비트 RSA X509 인증서를 생성합니다. 프라이빗 키 파일 이름은 *aks-ingress-tls.key*입니다. Kubernetes TLS 비밀에는 이러한 두 파일이 모두 필요합니다.

이 문서는 *demo.azure.com* 주체 일반 이름에 작동하며 변경할 필요가 없습니다. 프로덕션 사용을 위해서는 `-subj` 매개 변수에 대해 고유한 조직 값을 지정합니다.

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>TLS 인증서에 대한 Kubernetes 비밀 만들기

Kubernetes에서 수신 컨트롤러에 대해 TLS 인증서와 프라이빗 키를 사용할 수 있도록 하려면 비밀을 만들어 사용합니다. 이 비밀은 한 번 정의되며, 이전 단계에서 만든 인증서와 키 파일을 사용합니다. 이제 수신 경로를 정의할 때 이 비밀을 참조합니다.

다음 예제에서는 비밀 이름 *aks-ingress-tls*를 만듭니다.

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

인증서를 사용하여 수신 컨트롤러 및 비밀을 구성했습니다. 이제 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 애플리케이션의 두 인스턴스를 배포합니다.

작동 중인 수신 컨트롤러를 확인 하려면 AKS 클러스터에서 두 개의 데모 응용 프로그램을 실행 합니다. 이 예제에서는 `kubectl apply` 를 사용 하 여 간단한 *Hello 세계* 응용 프로그램의 두 인스턴스를 배포 합니다.

*Aks* 파일을 만들고 다음 예제 yaml에 복사 합니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
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
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

*수신-데모 .yaml* 파일을 만들고 다음 예제 yaml에 복사 합니다.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
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
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

다음을 사용 하 여 두 개의 데모 응용 프로그램을 실행 합니다 `kubectl apply` .

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>수신 경로 만들기

이제 두 애플리케이션이 모두 Kubernetes 클러스터에서 실행되고 있지만, `ClusterIP` 유형의 서비스로 구성되었습니다. 따라서 인터넷에서 애플리케이션에 액세스할 수 없습니다. 응용 프로그램을 공개적으로 사용할 수 있도록 Kubernetes 수신 리소스를 만듭니다. 수신 리소스는 두 애플리케이션 중 하나로 트래픽을 라우팅하는 규칙을 구성합니다.

다음 예제에서 주소 `https://demo.azure.com/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `https://demo.azure.com/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다. 이 문서에서는 해당 데모 호스트 이름을 바꿀 필요가 없습니다. 프로덕션 사용의 경우 지정된 이름을 인증서 요청 및 생성 프로세스의 일부로 제공합니다.

> [!TIP]
> 인증서 요청 프로세스 중에 지정 된 호스트 이름이 수신 경로에 정의 된 호스트와 일치 하지 않는 경우 수신 컨트롤러는 *Kubernetes 수신 컨트롤러 가짜 인증서* 경고를 표시 합니다. 인증서 및 수신 경로 호스트 이름이 일치하는지 확인합니다.

*tls* 섹션은 호스트 *demo.azure.com*에 대해 *aks-ingress-tls*라는 비밀을 사용하도록 수신 경로에 지시합니다. 마찬가지로 프로덕션 사용을 위해서는 사용자 고유의 호스트 주소를 지정합니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

`kubectl apply -f hello-world-ingress.yaml` 명령을 사용하여 수신 리소스를 만듭니다.

```console
kubectl apply -f hello-world-ingress.yaml
```

예제 출력에서는 수신 리소스가 생성 된 것을 보여 줍니다.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

가짜 *demo.azure.com* 호스트로 인증서를 테스트하고 `curl`을 사용하여 *--resolve* 매개 변수를 지정합니다. 이 매개 변수를 사용하여 *demo.azure.com* 이름을 수신 컨트롤러의 공용 IP 주소 이름에 매핑할 수 있습니다. 다음 예제에서와 같이 고유한 수신 컨트롤러의 공용 IP 주소를 지정합니다.

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

주소와 함께 추가 경로를 제공 하지 않았으므로 수신 컨트롤러의 기본값은 */* 경로입니다. 첫 번째 데모 애플리케이션은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

`curl` 명령의 *-v* 매개 변수는 수신된 TLS 인증서를 비롯한 자세한 정보를 출력합니다. curl 출력을 따라 절반 부분까지 이동하면 고유한 TLS 인증서를 사용했는지 확인할 수 있습니다. *-k* 매개 변수는 자체 서명된 인증서를 사용하더라도 페이지를 계속 로드합니다. 다음 예제에서는 *발급자: CN=demo.azure.com; O = CN=demo.azure.com; O=aks-ingress-tls* 인증서가 사용되었음을 보여 줍니다.

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

이제 `https://demo.azure.com/hello-world-two`와 같은 주소에 */hello-world-two* 경로를 추가합니다. 사용자 지정 제목이 있는 두 번째 데모 애플리케이션은 다음 축소된 예제 출력에 표시된 대로 반환됩니다.

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이러한 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리 하려면 전체 샘플 네임 스페이스 또는 개별 리소스를 삭제할 수 있습니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임 스페이스 및 모든 리소스 삭제

전체 샘플 네임 스페이스를 삭제 하려면 명령을 사용 하 `kubectl delete` 고 네임 스페이스 이름을 지정 합니다. 네임 스페이스의 모든 리소스가 삭제 됩니다.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제

또는 만든 개별 리소스를 삭제 하는 것이 더 세부적인 방법입니다. 명령을 사용 하 여 투구 릴리스를 나열 `helm list` 합니다. 

```console
helm list --namespace ingress-basic
```

다음 예제 출력과 같이 *nginx* 이라는 차트를 찾습니다.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1 
```

명령을 사용 하 여 릴리스를 제거 합니다 `helm uninstall` . 

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

다음 예제에서는 NGINX 수신 배포를 제거 합니다.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

다음으로 두 개의 샘플 응용 프로그램을 제거 합니다.

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

트래픽을 샘플 앱으로 유도한 수신 경로를 제거합니다.

```console
kubectl delete -f hello-world-ingress.yaml
```

인증서 암호를 삭제 합니다.

```console
kubectl delete secret aks-ingress-tls
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

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- Let’s Encrypt를 사용하여 [동적 공용 IP 주소][aks-ingress-tls] 또는 [고정 공용 IP 주소][aks-ingress-static-tls]로 TLS 인증서를 자동으로 생성하는 수신 컨트롤러 만들기

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
