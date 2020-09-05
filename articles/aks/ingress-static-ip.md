---
title: 고정 IP와 함께 수신 컨트롤러 사용
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Service) 클러스터에서 고정 공용 IP 주소를 사용하여 NGINX 수신 컨트롤러를 설치하고 구성하는 방법에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: dbab9df3acf7de801a4e75502863fff698232458
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88852568"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 고정 공용 IP 주소를 사용하여 수신 컨트롤러 만들기

수신 컨트롤러는 역방향 프록시, 구성 가능한 트래픽 라우팅, Kubernetes 서비스에 대한 TLS 종료를 제공하는 소프트웨어입니다. Kubernetes 수신 리소스는 개별 Kubernetes 서비스에 대한 수신 규칙 및 라우팅을 구성하는 데 사용됩니다. 수신 컨트롤러 및 수신 규칙을 사용하면 단일 IP 주소를 사용하여 Kubernetes 클러스터의 여러 서비스에 트래픽을 라우팅할 수 있습니다.

이 문서에서는 AKS(Azure Kubernetes Service) 클러스터에 [NGINX 수신 컨트롤러][nginx-ingress]를 배포하는 방법을 보여 줍니다. 고정 공용 IP 주소를 사용하여 수신 컨트롤러를 구성합니다. [cert-manager][cert-manager] 프로젝트는 [Let's Encrypt][lets-encrypt] 인증서를 자동으로 생성하고 구성하는 데 사용됩니다. 마지막으로, 두 애플리케이션이 AKS 클러스터에서 실행되며 단일 IP 주소를 통해 각 애플리케이션에 액세스할 수 있습니다.

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [동적 공용 IP를 사용하여 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt를 사용하는 수신 컨트롤러 만들기][aks-ingress-tls]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작 [Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

이 문서에서는 [투구 3][helm] 을 사용 하 여 NGINX 수신 컨트롤러 및 인증서 관리자를 설치 합니다. 최신 버전의 투구를 사용 하 고 *수신-nginx* 및 *jetstack* 투구 리포지토리에 액세스할 수 있는지 확인 합니다. 업그레이드 지침은 [투구 설치 문서][helm-install]를 참조 하세요. 투구 구성 및 사용에 대 한 자세한 내용은 [Azure Kubernetes 서비스에서 투구를 사용 하 여 응용 프로그램 설치 (AKS)][use-helm]를 참조 하세요.

또한이 문서에서는 Azure CLI 버전 2.0.64 이상을 실행 해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="create-an-ingress-controller"></a>수신 컨트롤러 만들기

기본적으로는 새 공용 IP 주소 할당을 통해 NGINX 수신 컨트롤러를 만듭니다. 이 공용 IP 주소는 수신 컨트롤러의 수명 동안만 고정되며 컨트롤러를 삭제했다가 다시 만들면 손실됩니다. 일반적인 구성 요구 사항은 기존 고정 공용 IP 주소를 NGINX 수신 컨트롤러에 제공하는 것입니다. 수신 컨트롤러를 삭제해도 고정 공용 IP 주소는 유지됩니다. 이 방법을 사용하면 애플리케이션 수명 주기 전반에 걸쳐 일관된 방식으로 기존 DNS 레코드 및 네트워크 구성을 사용할 수 있습니다.

고정 공용 IP 주소를 만들어야 하는 경우 먼저 [az aks show][az-aks-show] 명령을 사용하여 AKS 클러스터의 리소스 그룹 이름을 가져옵니다.

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

다음으로, [az network public-ip create][az-network-public-ip-create] 명령을 사용하여 *고정* 할당 메서드로 공용 IP 주소를 만듭니다. 다음 예제에서는 이전 단계에서 가져온 AKS 클러스터 리소스 그룹에 *myAKSPublicIP*라는 공용 IP 주소를 만듭니다.

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

> [!NOTE]
> 위의 명령은 AKS 클러스터를 삭제 하는 경우 삭제 되는 IP 주소를 만듭니다. 또는 AKS 클러스터와 별도로 관리할 수 있는 다른 리소스 그룹에 IP 주소를 만들 수 있습니다. 다른 리소스 그룹에 IP 주소를 만드는 경우 AKS 클러스터에서 사용 하는 서비스 주체에 게 *네트워크 참가자*와 같은 다른 리소스 그룹에 대 한 위임 된 권한이 있는지 확인 합니다.

이제 Helm을 사용하여 *nginx-ingress* 차트를 배포합니다. 중복성을 추가하기 위해 NGINX 수신 컨트롤러의 두 복제본이 `--set controller.replicaCount` 매개 변수와 함께 배포됩니다. 수신 컨트롤러의 복제본을 실행하는 이점을 최대한 활용하려면 AKS 클러스터에 둘 이상의 노드가 있어야 합니다.

수신 컨트롤러는 수신 컨트롤러 서비스에 할당 되는 부하 분산 장치의 고정 IP 주소와 공용 IP 주소 리소스에 적용 되는 DNS 이름 레이블을 모두 인식 하도록 투구 릴리스에 두 개의 추가 매개 변수를 전달 해야 합니다. HTTPS 인증서가 제대로 작동 하려면 DNS 이름 레이블이 수신 컨트롤러 IP 주소에 대 한 FQDN을 구성 하는 데 사용 됩니다.

1. `--set controller.service.loadBalancerIP`매개 변수를 추가 합니다. 이전 단계에서 만든 사용자 고유의 공용 IP 주소를 지정 합니다.
1. `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"`매개 변수를 추가 합니다. 이전 단계에서 만든 공용 IP 주소에 적용할 DNS 이름 레이블을 지정 합니다.

수신 컨트롤러도 Linux 노드에서 예약해야 합니다. Windows Server 노드가 수신 컨트롤러를 실행해서는 안 됩니다. `--set nodeSelector` 매개 변수를 사용하여 노드 선택기를 지정하면 Linux 기반 노드에서 NGINX 수신 컨트롤러를 실행하도록 Kubernetes 스케줄러에 지시할 수 있습니다.

> [!TIP]
> 다음 예에서는 수신 *-기본*이라는 수신 리소스에 대 한 Kubernetes 네임 스페이스를 만듭니다. 필요에 따라 사용자 환경에 대 한 네임 스페이스를 지정 합니다. AKS 클러스터가 RBAC를 사용 하도록 설정 되지 않은 경우 `--set rbac.create=false` 투구 명령에를 추가 합니다.

> [!TIP]
> 클러스터의 컨테이너에 대 한 요청에 대 한 [클라이언트 원본 IP 유지][client-source-ip] 를 사용 하도록 설정 하려면 `--set controller.service.externalTrafficPolicy=Local` 투구 install 명령에를 추가 합니다. 클라이언트 원본 IP가 *X 전달-에 대 한*요청 헤더에 저장 됩니다. 클라이언트 원본 IP 유지를 사용 하는 수신 컨트롤러를 사용 하는 경우 TLS 통과는 작동 하지 않습니다.

수신 컨트롤러의 **IP 주소** 와 FQDN 접두사에 사용할 **고유 이름을** 사용 하 여 다음 스크립트를 업데이트 합니다.

> [!IMPORTANT]
> 명령을 실행할 때 *STATIC_IP* 및 *DNS_LABEL* 을 고유한 IP 주소 및 고유한 이름으로 바꾸어야 합니다.

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
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

NGINX 수신 컨트롤러에 대해 Kubernetes 부하 분산 장치 서비스를 만든 경우 다음 예제 출력에 표시된 대로 고정 IP 주소를 할당합니다.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

아직 수신 규칙이 만들어지지 않았으므로 공용 IP 주소를 검색하면 NGINX 수신 컨트롤러의 기본 404 페이지가 표시됩니다. 수신 규칙은 다음 단계에서 구성됩니다.

다음과 같이 공용 IP 주소에서 FQDN을 쿼리하여 DNS 이름 레이블이 적용 되었는지 확인할 수 있습니다.

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?name=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

이제 IP 주소 또는 FQDN을 통해 수신 컨트롤러에 액세스할 수 있습니다.

## <a name="install-cert-manager"></a>cert-manager 설치

NGINX 수신 컨트롤러는 TLS 종료를 지원합니다. HTTPS에 대한 인증서를 검색하고 구성하는 몇 가지 방법이 있습니다. 이 문서에서는 자동 [Lets Encrypt][lets-encrypt] 인증서 생성 및 관리 기능을 제공하는 [cert-manager][cert-manager]를 사용하는 방법을 보여 줍니다.

> [!NOTE]
> 이 문서에서는 Let's Encrypt에 대해 `staging` 환경을 사용합니다. 프로덕션 배포에서는 Helm 차트를 설치할 때 및 리소스 정의에서 `letsencrypt-prod` 및 `https://acme-v02.api.letsencrypt.org/directory`를 사용합니다.

RBAC 사용이 가능한 클러스터에 cert-manager 컨트롤러를 설치하려면 다음과 같은 `helm install` 명령을 사용합니다.

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

cert-manager 구성에 대한 자세한 내용은 [cert-manager 프로젝트][cert-manager]를 참조합니다.

## <a name="create-a-ca-cluster-issuer"></a>CA 클러스터 발급자 만들기

인증서가 발급되기 전에 cert-manager에게는 [발급자][cert-manager-issuer] 또는 [ClusterIssuer][cert-manager-cluster-issuer] 리소스가 필요합니다. 이러한 Kubernetes 리소스는 기능이 동일하지만, `Issuer`는 단일 네임스페이스에서 작동하고 `ClusterIssuer`는 모든 네임스페이스에서 작동합니다. 자세한 내용은 [cert-manager 발급자][cert-manager-issuer] 설명서를 참조하세요.

다음 예제 매니페스트를 사용하여 클러스터 발급자(예: `cluster-issuer.yaml`)를 만듭니다. 조직의 유효한 주소로 메일 주소를 업데이트합니다.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

발급자를 만들려면 `kubectl apply` 명령을 사용합니다.

```
$ kubectl apply -f cluster-issuer.yaml --namespace ingress-basic

clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>데모 애플리케이션 실행

수신 컨트롤러와 인증서 관리 솔루션이 구성되었습니다. 이제 AKS 클러스터에서 두 개의 데모 애플리케이션을 실행하겠습니다. 이 예제에서는 Helm을 사용하여 간단한 ‘Hello world’ 애플리케이션의 두 인스턴스를 배포합니다.

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

다음 예제에서 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/`으로 향하는 트래픽은 `aks-helloworld`라는 서비스로 라우트됩니다. 주소 `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`로 향하는 트래픽은 `ingress-demo` 서비스로 라우팅됩니다. *hosts* 및 *host*를 이전 단계에서 만든 DNS 이름으로 업데이트합니다.

`hello-world-ingress.yaml` 파일을 만들고 다음 예제 YAML을 복사합니다.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

`kubectl apply` 명령을 사용하여 수신 리소스를 만듭니다.

```
$ kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>인증서 개체 만들기

다음으로, 인증서 리소스를 만들어야 합니다. 인증서 리소스는 원하는 X.509 인증서를 정의합니다. 자세한 내용은 [인증서 관리자 인증서][cert-manager-certificates]를 참조 하세요.

인증서 관리자는 v0.2.2 이후 인증서 관리자와 자동으로 배포되는 수신 shim을 사용하여 해당 관리자에 대한 인증서 개체가 자동으로 생성되었을 가능성이 있습니다. 자세한 내용은 [수신 shim 설명서][ingress-shim]를 참조하세요.

인증서가 성공적으로 만들어졌는지 확인하려면 `kubectl describe certificate tls-secret --namespace ingress-basic` 명령을 사용합니다.

인증서가 발급되면 다음과 비슷한 출력이 표시됩니다.
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

추가 인증서 리소스를 만들어야 하는 경우 다음 예제 매니페스트를 사용하여 만들 수 있습니다. *dnsNames* 및 *domains*를 이전 단계에서 만든 DNS 이름으로 업데이트합니다. 내부 전용 수신 컨트롤러를 사용하는 경우에 서비스의 내부 DNS 이름을 지정합니다.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

인증서 리소스를 만들려면 `kubectl apply` 명령을 사용합니다.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>수신 구성 테스트

Kubernetes 수신 컨트롤러의 FQDN (예:)에 대 한 웹 브라우저를 엽니다 *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

이러한 예제에서를 사용 하는 `letsencrypt-staging` 경우 발급 된 TLS/SSL 인증서가 브라우저에서 신뢰 되지 않습니다. 경고 프롬프트를 수락하여 애플리케이션에서 계속 진행합니다. 인증서 정보에 이 *Fake LE Intermediate X1* 인증서가 Let's Encrypt에서 발급되었다고 표시됩니다. 이 가짜 인증서는 `cert-manager`에서 요청을 올바르게 처리했으며 공급자로부터 인증서를 받았음을 나타냅니다.

![Let's Encrypt 스테이징 인증서](media/ingress/staging-certificate.png)

`staging` 대신 `prod`를 사용하도록 Let's Encrypt를 변경하면, 다음 예제와 같이 Let's Encrypt에서 발급된 신뢰할 수 있는 인증서가 사용됩니다.

![Let's Encrypt 인증서](media/ingress/certificate.png)

데모 애플리케이션이 웹 브라우저에 표시됩니다.

![애플리케이션 예제 1](media/ingress/app-one.png)

이제 FQDN에 */hello-world-two* 경로를 추가합니다(예: *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`*). 사용자 지정 제목이 있는 두 번째 데모 애플리케이션이 표시됩니다.

![애플리케이션 예제 2](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 Helm을 사용하여 수신 구성 요소, 인증서 및 샘플 앱을 설치했습니다. Helm 차트를 배포하면 다수의 Kubernetes 리소스가 생성됩니다. 이 리소스에는 Pod, 배포 및 서비스가 포함됩니다. 이러한 리소스를 정리 하려면 전체 샘플 네임 스페이스 또는 개별 리소스를 삭제할 수 있습니다.

### <a name="delete-the-sample-namespace-and-all-resources"></a>샘플 네임 스페이스 및 모든 리소스 삭제

전체 샘플 네임 스페이스를 삭제 하려면 명령을 사용 하 `kubectl delete` 고 네임 스페이스 이름을 지정 합니다. 네임 스페이스의 모든 리소스가 삭제 됩니다.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>리소스를 개별적으로 삭제

또는 만든 개별 리소스를 삭제 하는 것이 더 세부적인 방법입니다. 먼저 인증서 리소스를 제거 합니다.

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

이제 `helm list` 명령을 사용하여 Helm 릴리스를 나열합니다. 다음 예제 출력과 같이 *nginx* 이라는 차트를 *cert-manager* 찾습니다.

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

명령을 사용 하 여 릴리스를 제거 합니다 `helm uninstall` . 다음 예제에서는 NGINX 수신 배포 및 인증서 관리자 배포를 제거 합니다.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

다음으로 두 개의 샘플 응용 프로그램을 제거 합니다.

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

자체 네임 스페이스를 삭제 합니다. 명령을 사용 `kubectl delete` 하 여 네임 스페이스 이름을 지정 합니다.

```console
kubectl delete namespace ingress-basic
```

마지막으로 수신 컨트롤러에 대해 생성된 고정 공용 IP 주소를 제거합니다. 이 문서의 첫 번째 단계에서 얻은 *MC_* 클러스터 리소스 그룹 이름(예: *MC_myResourceGroup_myAKSCluster_eastus*)을 제공합니다.

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>다음 단계

이 문서에는 AKS의 몇 가지 외부 구성 요소가 포함되었습니다. 이러한 구성 요소에 대한 자세한 내용은 다음 프로젝트 페이지를 참조하세요.

- [Helm CLI][helm-cli]
- [NGINX 수신 컨트롤러][nginx-ingress]
- [인증서 관리자][cert-manager]

다음도 가능합니다.

- [외부 네트워크 연결을 사용하여 기본적인 수신 컨트롤러 만들기][aks-ingress-basic]
- [HTTP 애플리케이션 라우팅 추가 기능 사용][aks-http-app-routing]
- [내부 프라이빗 네트워크 및 IP 주소를 사용하는 수신 컨트롤러 만들기][aks-ingress-internal]
- [사용자 고유의 TLS 인증서를 사용하는 수신 컨트롤러 만들기][aks-ingress-own-tls]
- [동적 공용 IP를 사용하여 수신 컨트롤러를 만들고 TLS 인증서를 자동으로 생성하도록 Let’s Encrypt 구성][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
