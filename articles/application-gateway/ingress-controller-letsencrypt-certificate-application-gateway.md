---
title: Application Gateway에서 LetsEncrypt.org certificate 사용
description: 이 문서에서는 LetsEncrypt.org에서 인증서를 획득 하 여 AKS 클러스터에 대 한 Application Gateway에서 사용 하는 방법에 대 한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807026"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS 클러스터에 대 한 Application Gateway에 LetsEncrypt.org에서 인증서 사용

이 섹션에서는 AKS를 구성 하 여 [LetsEncrypt.org](https://letsencrypt.org/) 를 활용 하 고 도메인에 대 한 TLS/SSL 인증서를 자동으로 가져옵니다. AKS 클러스터에 대 한 SSL/TLS 종료를 수행 하는 Application Gateway에 인증서가 설치 됩니다. 여기서 설명 하는 설정은 인증서의 생성 및 관리를 자동화 하는 [cert manager](https://github.com/jetstack/cert-manager) Kubernetes 추가 기능을 사용 합니다.

아래 단계에 따라 기존 AKS 클러스터에 [인증서 관리자](https://docs.cert-manager.io) 를 설치 합니다.

1. 투구 차트

    다음 스크립트를 실행 하 여 투구 차트를 설치 합니다 `cert-manager` . 그러면

    - `cert-manager`AKS에 새 네임 스페이스 만들기
    - 인증서, 챌린지, ClusterIssuer, 발급자, 주문을 만듭니다.
    - [docs.cert-manager.io](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps) 에서 인증서-관리자 차트 설치

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer 리소스

    리소스를 만듭니다 `ClusterIssuer` . 에서 `cert-manager` `Lets Encrypt` 서명 된 인증서를 가져올 인증 기관을 나타내는 데 필요 합니다.

    Namespaced이 아닌 리소스를 사용 하 여 인증서 `ClusterIssuer` 관리자는 여러 네임 스페이스에서 사용할 수 있는 인증서를 발급 합니다. `Let’s Encrypt` ACME 프로토콜을 사용 하 여 지정 된 도메인 이름을 제어 하 고 인증서를 발급 하는지 확인 합니다. 여기에서 속성을 구성 하는 방법에 대해 자세히 설명 `ClusterIssuer` 합니다. [](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html) `ClusterIssuer` 는 `cert-manager` 테스트에 사용 되는 스테이징 환경을 사용 하 여 인증서를 발급 하도록 지시 `Lets Encrypt` 합니다 (루트 인증서가 브라우저/클라이언트 신뢰 저장소에 없음).

    아래 YAML의 기본 챌린지 형식은 `http01` 입니다. 다른 문제는 [letsencrypt.org](https://letsencrypt.org/docs/challenge-types/) 에 설명 되어 있습니다.

    > [!IMPORTANT] 
    > `<YOUR.EMAIL@ADDRESS>`아래 YAML의 업데이트

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. 앱 배포

    에서 `guestbook` Application Gateway를 사용 하 여 응용 프로그램을 노출 하는 수신 리소스를 만들어 인증서 암호화를 사용 합니다.

    DNS 이름을 사용 하는 공용 프런트 엔드 IP 구성이 있는지 확인 합니다 (기본 `azure.com` 도메인을 사용 하거나 서비스를 프로 비전 하 `Azure DNS Zone` 고 사용자 지정 도메인을 할당 하는 Application Gateway).
    주석에 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` 태그가 지정 된 수신 리소스를 처리 하도록 인증서를 알려 줍니다.

    > [!IMPORTANT] 
    > `<PLACEHOLDERS.COM>`사용자 고유의 도메인 (또는 ' kh-aks-ingress.westeurope.cloudapp.azure.com '과 같은 Application Gateway)으로 아래 YAML의 업데이트

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    몇 초 후에 `guestbook` 자동으로 발급 된 **준비** 인증서를 사용 하 여 Application Gateway HTTPS url을 통해 서비스에 액세스할 수 있습니다 `Lets Encrypt` .
    브라우저에서 잘못 된 인증 기관에 대해 경고할 수 있습니다. 에서 준비 인증서를 발급 합니다 `CN=Fake LE Intermediate X1` . 시스템이 예상 대로 작동 하 고 프로덕션 인증서를 사용할 준비가 되었음을 나타냅니다.

4. 프로덕션 인증서

    스테이징 인증서가 성공적으로 설정 되 면 프로덕션 ACME 서버로 전환할 수 있습니다.
    1. 수신 리소스에서 준비 주석을 다음으로 바꿉니다. `certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. `ClusterIssuer`이전 단계에서 만든 기존 준비를 삭제 하 고 위의 ClusterIssuer YAML에서 ACME 서버를 대체 하 여 새로 만듭니다.`https://acme-v02.api.letsencrypt.org/directory`

5. 인증서 만료 및 갱신

    인증서가 `Lets Encrypt` 만료 되기 전에 `cert-manager` 은 Kubernetes 암호 저장소에서 인증서를 자동으로 업데이트 합니다. 이 시점에서 Application Gateway 수신 컨트롤러는 Application Gateway를 구성 하는 데 사용 하는 수신 리소스에서 참조 되는 업데이트 된 암호를 적용 합니다.
