---
title: Application Gateway 수신 컨트롤러에 대해 여러 네임 스페이스 지원 사용
description: 이 문서에서는 Application Gateway 수신 컨트롤러를 사용 하 여 Kubernetes 클러스터에서 여러 네임 스페이스 지원을 사용 하도록 설정 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795553"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러를 사용 하 여 AKS 클러스터에서 여러 네임 스페이스 지원 사용

## <a name="motivation"></a>부여
Kubernetes [네임 스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) 를 통해 Kubernetes 클러스터를 분할 하 고 대규모 팀의 하위 그룹에 할당할 수 있습니다. 그런 다음 이러한 하위 팀은 리소스, 보안, 구성 등의 더 세부적인 제어를 통해 인프라를 배포 하 고 관리할 수 있습니다. Kubernetes를 사용 하면 각 네임 스페이스 내에서 하나 이상의 수신 리소스를 독립적으로 정의할 수 있습니다.

버전 0.7 [Azure 애플리케이션 게이트웨이 Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC)는에서 이벤트를 수집 하 고 여러 네임 스페이스를 관찰할 수 있습니다. AKS 관리자가 [App Gateway](https://azure.microsoft.com/services/application-gateway/) 를 수신으로 사용 하기로 결정 하는 경우 모든 네임 스페이스는 동일한 Application Gateway 인스턴스를 사용 합니다. 단일 수신 컨트롤러 설치는 액세스 가능한 네임 스페이스를 모니터링 하 고 연결 된 Application Gateway를 구성 합니다.

AGIC 버전 0.7는이를 투구 구성에서 하나 이상의 다른 네임 스페이스로 명시적으로 변경 하지 않는 한, `default` 네임 스페이스를 계속 독점적으로 관찰 합니다 (아래 섹션 참조).

## <a name="enable-multiple-namespace-support"></a>여러 네임 스페이스 지원 사용
여러 네임 스페이스 지원을 사용 하려면 다음을 수행 합니다.
1. 다음 방법 중 하나를 통해 [투구-.config](#sample-helm-config-file) 파일을 수정 합니다.
   - AGIC에서 완전히 `watchNamespace` 키를 삭제 하 고 모든 네임 스페이스를 관찰 [합니다.](#sample-helm-config-file)
   - `watchNamespace`를 빈 문자열로 설정-AGIC은 모든 네임 스페이스를 관찰 합니다.
   - 쉼표로 구분 된 여러 네임 스페이스 추가 (`watchNamespace: default,secondNamespace`)-AGIC는 이러한 네임 스페이스를 독점적으로 관찰 합니다.
2. 다음을 사용 하 여 투구 템플릿 변경 내용 적용: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

여러 네임 스페이스를 관찰 하는 기능으로 배포 된 후에는 AGIC에서 다음을 수행 합니다.
  - 액세스 가능한 모든 네임 스페이스의 수신 리소스 나열
  - `kubernetes.io/ingress.class: azure/application-gateway`로 주석이 추가 된 수신 리소스를 필터링 합니다.
  - 구성 결합 [Application Gateway 구성](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744)
  - [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 을 통해 연결 된 Application Gateway에 구성을 적용 합니다.

## <a name="conflicting-configurations"></a>충돌 하는 구성
여러 namespaced [수신 리소스](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) 는 단일 Application Gateway에 대해 충돌 하는 구성을 만들도록 AGIC에 지시할 수 있습니다. (두 조절기는 인스턴스에 대해 동일한 도메인을 주장 합니다.)

계층의 맨 위에 있는 **수신기** (IP 주소, 포트 및 호스트)와 **라우팅 규칙** (바인딩 수신기, 백 엔드 풀 및 HTTP 설정)은 여러 네임 스페이스/조절기에서 만들고 공유할 수 있습니다.

반면에 경로, 백 엔드 풀, HTTP 설정 및 TLS 인증서는 하나의 네임 스페이스에 의해서만 생성 될 수 있으며 중복 항목이 제거 됩니다.

예를 들어 다음과 같은 중복 수신 리소스 정의 네임 스페이스는 `www.contoso.com`에 대한 `staging` 및 `production`을 참조 하세요.
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

`www.contoso.com`에 대한 두 개의 수신 리소스가 각각의 Kubernetes 네임 스페이스로 라우팅되도록 하기에도 불구 하 고 하나의 백 엔드가 트래픽을 처리 하는 것이 가능 합니다. AGIC는 "먼저 리소스 중 하나에 대해" 처음 제공 되는 "기반 구성을 만듭니다. 동시에 두 개의 조절기 리소스를 만든 경우 해당 리소스의 앞에 있는 하나는 우선적으로 적용 됩니다. 위의 예제에서 `production` 수신에 대한 설정만 만들 수 있습니다. Application Gateway는 다음 리소스로 구성 됩니다.

  - 수신기: `fl-www.contoso.com-80`
  - 라우팅 규칙: `rr-www.contoso.com-80`
  - 백 엔드 풀: `pool-production-contoso-web-service-80-bp-80`
  - HTTP 설정: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - 상태 프로브: `pb-production-contoso-web-service-80-websocket-ingress`

*수신기* 및 *라우팅 규칙*을 제외 하 고 만들어진 Application Gateway 리소스에는 해당 리소스가 생성 된 네임 스페이스 (`production`)의 이름이 포함 됩니다.

두 수신 리소스가 서로 다른 시점에 AKS 클러스터에 도입 되는 경우 AGIC를 다시 구성 하 고 `namespace-B`에서 `namespace-A`으로 트래픽을 다시 라우팅하는 Application Gateway 시나리오에서 될 수 있습니다.

예를 들어 `staging`를 먼저 추가한 경우 AGIC은 준비 백 엔드 풀로 트래픽을 라우팅하는 Application Gateway를 구성 합니다. `production` 수신을 소개 하는 이후 단계에서 `production` 백 엔드 풀로 트래픽 라우팅을 시작 하는 AGIC가 mk-reprogram Application Gateway 발생 합니다.

## <a name="restrict-access-to-namespaces"></a>네임 스페이스에 대한 액세스 제한
기본적으로 AGIC는 네임 스페이스 내에서 주석이 추가 된 수신에 따라 Application Gateway를 구성 합니다. 이 동작을 제한 하려는 경우 다음 옵션을 선택할 수 있습니다.
  - AGIC에서 YAML 키 `watchNamespace`를 통해 관찰 해야 하는 네임 스페이스를 명시적으로 정의 하 여 네임 스페이스를 제한 [합니다.](#sample-helm-config-file)
  - [Role/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) 을 사용 하 여 AGIC를 특정 네임 스페이스로 제한

## <a name="sample-helm-config-file"></a>샘플 투구 구성 파일
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

