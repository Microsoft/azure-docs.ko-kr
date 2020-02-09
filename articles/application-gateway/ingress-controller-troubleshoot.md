---
title: Application Gateway 수신 컨트롤러 문제 해결
description: 이 문서에서는 Application Gateway 수신 컨트롤러와 관련 된 일반적인 질문 및/또는 문제를 해결 하는 방법에 대한 설명서를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a64a9ce5e080308674893273e90a0e83686e339e
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795506"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>수신 컨트롤러에 대한 일반적인 질문 및 문제 해결

[Azure Cloud Shell](https://shell.azure.com/) 은 AKS 및 AGIC 설치와 관련 된 문제를 해결 하는 가장 편리한 방법입니다. [Shell.azure.com](https://shell.azure.com/) 에서 또는 링크를 클릭 하 여 셸을 시작 합니다.

[![시작 포함](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>간단한 Kubernetes 앱을 사용 하 여 테스트

아래 단계에서는 다음을 가정 합니다.
  - AKS 클러스터가 있고 고급 네트워킹을 사용 하도록 설정한 경우
  - AGIC가 AKS 클러스터에 설치 되었습니다.
  - AKS 클러스터와 공유 하는 VNET에 이미 Application Gateway hav가 있습니다.

Application Gateway + AKS + AGIC 설치가 올바르게 설정 되었는지 확인 하려면 가능한 가장 간단한 앱을 배포 합니다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

위의 스크립트에서 [Azure Cloud Shell](https://shell.azure.com/)한 번에 모든 줄을 복사 하 여 붙여 넣습니다. 전체 명령이 복사 되었는지 확인 합니다.-`cat` 시작 하 고 마지막 `EOF`를 포함 합니다.

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

AKS 클러스터 위에 응용 프로그램을 성공적으로 배포한 후에는 새 Pod, 서비스 및 수신이 포함 됩니다.

[Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide`를 사용 하 여 pod 목록을 가져옵니다.
' Agic-pod ' 라는 pod가 생성 된 것으로 간주 됩니다. IP 주소를 갖게 됩니다. 이 주소는 AKS에서 사용 되는 Application Gateway의 VNET 내에 있어야 합니다.

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

서비스 목록 가져오기: `kubectl get services -o wide`. ' Agic ' 라는 서비스가 표시 될 것입니다.

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

조절기: `kubectl get ingress`목록을 가져옵니다. ' Agic-수신 ' 이라는 수신 리소스가 생성 된 것으로 간주 됩니다. 리소스의 호스트 이름은 ' test.agic.contoso.com '입니다.

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Pod 중 하나는 AGIC가 됩니다. `kubectl get pods`에는 ' 수신-azure '로 시작 하는 pod 목록이 표시 됩니다. `kubectl logs <name-of-ingress-controller-pod>`를 사용 하 여 해당 pod의 모든 로그를 가져와 성공적으로 배포 되었는지 확인 합니다. 성공적으로 배포 되 면 로그에 다음 줄이 추가 됩니다.
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

또는 [Cloud Shell](https://shell.azure.com/) 에서 `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'`를 사용 하 여 성공적인 Application Gateway 구성을 나타내는 줄만 검색할 수 있습니다. 여기서 `<ingress-azure....>`은 정확한 AGIC pod의 이름 이어야 합니다.

Application Gateway는 다음 구성을 적용 합니다.

- 수신기: ![수신기](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- 라우팅 규칙: ![routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- 백 엔드 풀:
  - 백 엔드 주소 풀에는 하나의 IP 주소가 있으며 앞에서 관찰 한 Pod의 IP 주소와 일치 하는 `kubectl get pods -o wide`
![backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


마지막으로 [Cloud Shell](https://shell.azure.com/) 내에서 `cURL` 명령을 사용 하 여 새로 배포 된 앱에 대한 HTTP 연결을 설정할 수 있습니다.

1. `kubectl get ingress`를 사용 하 여의 공용 IP 주소를 가져옵니다 Application Gateway
2. `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>` 사용

![pod](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

`HTTP/1.1 200 OK` 결과 Application Gateway + AKS + AGIC 시스템이 예상 대로 작동 하 고 있음을 나타냅니다.


## <a name="inspect-kubernetes-installation"></a>Kubernetes 설치 검사

### <a name="pods-services-ingress"></a>Pod, 서비스, 수신
AGIC (Application Gateway 수신 컨트롤러)는 [배포](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) 또는 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [서비스](https://kubernetes.io/docs/concepts/services-networking/service/), [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 등의 Kubernetes 리소스를 지속적으로 모니터링 합니다.


AGIC가 예상 대로 작동 하려면 다음 사항을 준비 해야 합니다.
  1. AKS에는 정상 **pod**가 하나 이상 있어야 합니다.
     `kubectl get pods -o wide --show-labels`를 사용 하 여 [Cloud Shell](https://shell.azure.com/) 에서 확인 합니다. `apsnetapp`Pod가 있는 경우 출력은 다음과 같습니다.
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. 일치 하는 `selector` 레이블을 통해 위의 pod를 참조 하는 하나 이상의 **서비스**입니다.
     [Cloud Shell](https://shell.azure.com/) 에서 `kubectl get services -o wide`를 확인 합니다.
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. `kubernetes.io/ingress.class: azure/application-gateway`로 주석이 추가 된 **수신**, 위의 서비스를 참조 하면 [Cloud Shell](https://shell.azure.com/) 에서이를 확인 `kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. 위의 수신에 대한 주석 보기: `kubectl get ingress aspnetapp -o yaml` (`aspnetapp`를 수신 이름으로 대체)
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     수신 리소스에 `kubernetes.io/ingress.class: azure/application-gateway`주석을 추가 해야 합니다.
 

### <a name="verify-observed-namespace"></a>관찰 된 네임 스페이스 확인

* Kubernetes 클러스터에서 기존 네임 스페이스를 가져옵니다. 앱이 실행 되는 네임 스페이스는 무엇 인가요? 해당 네임 스페이스를 감시 하 고 AGIC? 관찰 된 네임 스페이스를 적절 하 게 구성 하는 방법에 대한 [여러 네임 스페이스 지원](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) 설명서를 참조 하세요.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC pod는 `default` 네임 스페이스에 있어야 합니다 (`NAMESPACE`열 참조). 정상 pod는 `STATUS` 열에 `Running` 있습니다. AGIC pod가 하나 이상 있어야 합니다.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* AGIC pod가 정상이 아닌 경우 (위 명령의`STATUS` 열은 `Running`되지 않음):
  - 다음 이유를 이해 하기 위한 로그 가져오기: `kubectl logs <pod-name>`
  - pod의 이전 인스턴스: `kubectl logs <pod-name> --previous`
  - 추가 컨텍스트를 얻기 위해 pod에 대해 설명 합니다. `kubectl describe pod <pod-name>`


* Kubernetes [서비스](https://kubernetes.io/docs/concepts/services-networking/service/) 와 [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스가 있나요?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* 다음을 사용 하 여 [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 주석을 답니다. `kubernetes.io/ingress.class: azure/application-gateway`? AGIC는이 주석이 있는 Kubernetes 수신 리소스만 감시 합니다.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC는 특정 심각한 오류에 대한 Kubernetes 이벤트를 내보냅니다. 다음을 볼 수 있습니다.
  - `kubectl get events --sort-by=.metadata.creationTimestamp`를 통해 터미널에서
  - [Kubernetes 웹 UI (대시보드)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 를 사용 하 여 브라우저에서


## <a name="logging-levels"></a>로깅 수준

AGIC에는 3 개의 로깅 수준이 있습니다. 수준 1은 기본 항목 이며 최소 수의 로그 줄을 표시 합니다.
반면 수준 5는 ARM에 적용 된 구성의 삭제 된 콘텐츠를 포함 하 여 모든 로그를 표시 합니다.

Kubernetes 커뮤니티는 [kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) 도구에 대한 9 가지 로깅 수준을 설정 했습니다. 이 리포지토리에는 다음과 같은 의미 체계를 사용 하 여 세 가지를 활용 합니다.


| 자세한 정도 | 설명 |
|-----------|-------------|
|  1        | 기본 로그 수준 시작 정보, 경고 및 오류를 표시 합니다. |
|  3        | 이벤트 및 변경 내용에 대한 확장 된 정보 만든 개체 목록 |
|  5        | 마샬링된 개체를 로깅합니다. ARM에 적용 된 삭제 된 JSON 구성 표시 |


자세한 정도는 [투구-.config](#sample-helm-config-file) 파일의 `verbosityLevel` 변수를 통해 조정할 수 있습니다. `5`에 대한 자세한 정도를 높여 [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에 디스패치 된 JSON 구성을 가져옵니다.
  - [투구-config.xml](#sample-helm-config-file) 에서 한 줄에 `verbosityLevel: 5` 추가 하 고 다시 설치
  - `kubectl logs <pod-name>`로 로그 가져오기

### <a name="sample-helm-config-file"></a>샘플 투구 구성 파일
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

