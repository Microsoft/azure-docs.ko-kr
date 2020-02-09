---
title: 기존 Application Gateway를 사용 하 여 수신 컨트롤러 만들기
description: 이 문서에서는 기존 Application Gateway를 사용 하 여 Application Gateway 수신 컨트롤러를 배포 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: dec43a4d7eb5a9546fcd77cce972b93542ea3b10
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795949"
---
# <a name="install-an-application-gateway-ingress-controller-agic-using-an-existing-application-gateway"></a>기존 Application Gateway를 사용 하 여 AGIC (Application Gateway 수신 컨트롤러) 설치

AGIC (Application Gateway 수신 컨트롤러)는 Kubernetes 클러스터 내의 pod입니다.
AGIC는 Kubernetes [수신](https://kubernetes.io/docs/concepts/services-networking/ingress/) 리소스를 모니터링 하 고 Kubernetes 클러스터의 상태에 따라 Application Gateway 구성을 만들고 적용 합니다.

## <a name="outline"></a>아웃
- [필수 조건](#prerequisites)
- [ARM (Azure Resource Manager 인증)](#azure-resource-manager-authentication)
    - 옵션 1: [aad-pod-Id 설정](#set-up-aad-pod-identity) 및 Arm에서 Azure id 만들기
    - 옵션 2: [서비스 주체 사용](#using-a-service-principal)
- [투구를 사용 하 여 수신 컨트롤러 설치](#install-ingress-controller-as-a-helm-chart)
- [다중 클러스터/공유 Application Gateway](#multi-cluster--shared-application-gateway): 하나 이상의 AKS 클러스터 및/또는 기타 Azure 구성 요소 간에 Application Gateway 공유 되는 환경에 AGIC를 설치 합니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 다음과 같은 도구 및 인프라를 이미 설치 했다고 가정 합니다.
- [고급 네트워킹](https://docs.microsoft.com/azure/aks/configure-azure-cni) 을 사용 하는 [AKS](https://azure.microsoft.com/services/kubernetes-service/)
- AKS와 동일한 가상 네트워크의 [Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/create-zone-redundant)
- AKS 클러스터에 설치 된 [AAD Pod id](https://github.com/Azure/aad-pod-identity)
- [Cloud Shell](https://shell.azure.com/) 은 `az` CLI, `kubectl`및 `helm` 설치 된 Azure Shell 환경입니다. 이러한 도구는 아래 명령에 필요 합니다.

AGIC를 설치 하기 전에 __Application Gateway의 구성을 백업__ 하세요.
  1. [Azure Portal](https://portal.azure.com/) 사용 하 여 `Application Gateway` 인스턴스로 이동
  2. `Export template` 클릭 `Download`

다운로드 한 zip 파일에는 JSON 템플릿, bash 및 앱 게이트웨이를 복원 하는 데 사용할 수 있는 PowerShell 스크립트가 포함 됩니다.

## <a name="install-helm"></a>Helm 설치
[투구](https://docs.microsoft.com/azure/aks/kubernetes-helm) 는 Kubernetes 패키지 관리자입니다. Microsoft는이를 활용 하 여 `application-gateway-kubernetes-ingress` 패키지를 설치 합니다.
[Cloud Shell](https://shell.azure.com/) 를 사용 하 여 투구 설치:

1. [투구](https://docs.microsoft.com/azure/aks/kubernetes-helm) 를 설치 하 고 다음을 실행 하 `application-gateway-kubernetes-ingress` 투구 패키지를 추가 합니다.

    - *RBAC 사용* AKS 클러스터

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

    - *RBAC 사용 안 함* AKS 클러스터

    ```bash
    helm init
    ```

1. AGIC 투구 리포지토리를 추가 합니다.
    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="azure-resource-manager-authentication"></a>Azure Resource Manager 인증

AGIC는 Kubernetes API 서버 및 Azure Resource Manager와 통신 합니다. 이러한 Api에 액세스 하려면 id가 필요 합니다.

## <a name="set-up-aad-pod-identity"></a>AAD Pod Id 설정

[AAD Pod id](https://github.com/Azure/aad-pod-identity) 는 AGIC와 비슷하며 AKS 에서도 실행 되는 컨트롤러입니다. Azure Active Directory id를 Kubernetes pod에 바인딩합니다. Kubernetes pod의 응용 프로그램은 다른 Azure 구성 요소와 통신할 수 있도록 id가 필요 합니다. 여기의 특정 사례에서 [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에 대한 HTTP 요청을 만들기 위해 AGIC pod에 대한 권한 부여가 필요 합니다.

[AAD Pod id 설치 지침](https://github.com/Azure/aad-pod-identity#deploy-the-azure-aad-identity-infra) 에 따라이 구성 요소를 AKS에 추가 합니다.

다음으로 Azure id를 만들고 사용 권한을 ARM에 부여 해야 합니다.
[Cloud Shell](https://shell.azure.com/) 를 사용 하 여 다음 모든 명령을 실행 하 고 id를 만듭니다.

1. **AKS 노드와 동일한 리소스 그룹에**Azure id를 만듭니다. 올바른 리소스 그룹을 선택 하는 것이 중요 합니다. 아래 명령에 필요한 리소스 그룹은 AKS 포털 창에서 참조 되는 것이 *아닙니다* . `aks-agentpool` 가상 컴퓨터의 리소스 그룹입니다. 일반적으로 리소스 그룹은 `MC_` 시작 하 고 AKS의 이름을 포함 합니다. 예: `MC_resourceGroup_aksABCD_westus`

    ```bash
    az identity create -g <agent-pool-resource-group> -n <identity-name>
    ```

1. 아래의 역할 할당 명령에 대해 새로 만든 id에 대한 `principalId`를 가져와야 합니다.

    ```bash
    az identity show -g <resourcegroup> -n <identity-name>
    ```

1. Id `Contributor` Application Gateway에 액세스할 수 있도록 합니다. 이 경우 다음과 같이 표시 되는 Application Gateway ID가 필요 합니다. `/subscriptions/A/resourceGroups/B/providers/Microsoft.Network/applicationGateways/C`

    다음을 사용 하 여 구독에서 Application Gateway Id 목록을 가져옵니다. `az network application-gateway list --query '[].id'`

    ```bash
    az role assignment create \
        --role Contributor \
        --assignee <principalId> \
        --scope <App-Gateway-ID>
    ```

1. Id `Reader` Application Gateway 리소스 그룹에 대한 액세스 권한을 부여 합니다. 리소스 그룹 ID는 `/subscriptions/A/resourceGroups/B`와 같습니다. 다음을 사용 하 여 모든 리소스 그룹을 가져올 수 있습니다 `az group list --query '[].id'`

    ```bash
    az role assignment create \
        --role Reader \
        --assignee <principalId> \
        --scope <App-Gateway-Resource-Group-ID>
    ```

## <a name="using-a-service-principal"></a>서비스 주체 사용
Kubernetes 암호를 통해 ARM에 AGIC 액세스를 제공할 수도 있습니다.

1. Active Directory 서비스 주체를 만들고 b a s e 64로 인코딩합니다. Base64 인코딩은 JSON blob을 Kubernetes에 저장 하는 데 필요 합니다.

```bash
az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0
```

2. `helm-config.yaml` 파일에 base64 인코딩 JSON blob을 추가 합니다. `helm-config.yaml`에 대한 자세한 내용은 다음 섹션에 있습니다.
```yaml
armAuth:
    type: servicePrincipal
    secretJSON: <Base64-Encoded-Credentials>
```

## <a name="install-ingress-controller-as-a-helm-chart"></a>수신 컨트롤러를 투구 차트로 설치
처음 몇 단계에서는 Kubernetes 클러스터에 투구의 Tiller을 설치 합니다. [Cloud Shell](https://shell.azure.com/) 를 사용 하 여 AGIC 투구 패키지를 설치 합니다.

1. `application-gateway-kubernetes-ingress` 투구 리포지토리를 추가 하 고 투구 업데이트를 수행 합니다.

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

1. AGIC를 구성 하는 투구-config.xml을 다운로드 합니다.
    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```
    또는 아래에서 YAML 파일을 복사 합니다. 
    
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

1. 투구를 편집 하 고 `appgw` 및 `armAuth`에 대한 값을 입력 합니다.
    ```bash
    nano helm-config.yaml
    ```

    > [!NOTE] 
    > `<identity-resource-id>` 및 `<identity-client-id>`는 이전 섹션에서 설정 하는 Azure AD Id의 속성입니다. 다음 명령을 실행 하 여이 정보를 검색할 수 있습니다 `az identity show -g <resourcegroup> -n <identity-name>`. 여기서 `<resourcegroup>`는 최상위 AKS 클러스터 개체, Application Gateway 및 관리 되는 Id가 배포 되는 리소스 그룹입니다.

1. 이전 단계에서 `helm-config.yaml` 구성을 사용 하 여 `application-gateway-kubernetes-ingress` 투구 차트 설치

    ```bash
    helm install -f <helm-config.yaml> application-gateway-kubernetes-ingress/ingress-azure
    ```

    또는 `helm-config.yaml` 및 투구 명령을 한 번에 결합할 수 있습니다.
    ```bash
    helm install ./helm/ingress-azure \
         --name ingress-azure \
         --namespace default \
         --debug \
         --set appgw.name=applicationgatewayABCD \
         --set appgw.resourceGroup=your-resource-group \
         --set appgw.subscriptionId=subscription-uuid \
         --set appgw.shared=false \
         --set armAuth.type=servicePrincipal \
         --set armAuth.secretJSON=$(az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0) \
         --set rbac.enabled=true \
         --set verbosityLevel=3 \
         --set kubernetes.watchNamespace=default \
         --set aksClusterConfiguration.apiServerAddress=aks-abcdefg.hcp.westus2.azmk8s.io
    ```

1. 새로 만든 pod의 로그를 확인 하 여 제대로 시작 되었는지 확인 합니다.

Azure 애플리케이션 Gateway를 사용 하 여 HTTP 또는 HTTPS를 통해 AKS 서비스를 인터넷에 노출 하는 방법을 이해 하려면 [이 방법 가이드](ingress-controller-expose-service-over-http-https.md) 를 참조 하세요.



## <a name="multi-cluster--shared-application-gateway"></a>다중 클러스터/공유 Application Gateway
기본적으로 AGIC는 연결 된 Application Gateway에 대한 전체 소유권을 가정 합니다. AGIC 버전 0.8.0부터 이상에서는 다른 Azure 구성 요소와 단일 Application Gateway를 공유할 수 있습니다. 예를 들어 가상 머신 확장 집합에서 호스트 되는 앱 및 AKS 클러스터에 동일한 Application Gateway를 사용할 수 있습니다.

이 설정을 사용 하도록 설정 하기 전에 __Application Gateway의 구성을 백업__ 하십시오.
  1. [Azure Portal](https://portal.azure.com/) 사용 하 여 `Application Gateway` 인스턴스로 이동
  2. `Export template` 클릭 `Download`

다운로드 한 zip 파일에는 Application Gateway을 복원 하는 데 사용할 수 있는 JSON 템플릿, bash 및 PowerShell 스크립트가 포함 됩니다.

### <a name="example-scenario"></a>예제 시나리오
두 웹 사이트에 대한 트래픽을 관리 하는 가상의 Application Gateway 살펴보겠습니다.
  - `dev.contoso.com`-Application Gateway 및 AGIC를 사용 하 여 새 AKS에서 호스트 됩니다.
  - [Azure 가상 머신 확장 집합](https://azure.microsoft.com/services/virtual-machine-scale-sets/) 에서 호스트 되는 `prod.contoso.com`

AGIC는 기본 설정을 사용 하 여 가리키는 Application Gateway의 100% 소유권을 가정 합니다. AGIC는 모든 App Gateway의 구성을 덮어씁니다. Kubernetes 수신에서 정의 하지 않고 Application Gateway에서 `prod.contoso.com`에 대한 수신기를 수동으로 만드는 경우 AGIC은 몇 초 내에 `prod.contoso.com` 구성을 삭제 합니다.

AGIC를 설치 하 고 가상 머신 확장 집합 컴퓨터의 `prod.contoso.com`도 제공 하려면 `dev.contoso.com`만 구성 하도록 AGIC를 제한 해야 합니다. 다음 [CRD](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/)를 인스턴스화하여이 작업을 쉽게 수행 합니다.

```bash
cat <<EOF | kubectl apply -f -
apiVersion: "appgw.ingress.k8s.io/v1"
kind: AzureIngressProhibitedTarget
metadata:
  name: prod-contoso-com
spec:
  hostname: prod.contoso.com
EOF
```

위의 명령은 `AzureIngressProhibitedTarget` 개체를 만듭니다. 이렇게 하면 AGIC (버전 0.8.0부터 이상)에서 `prod.contoso.com`에 대한 Application Gateway 구성이 있는지 인식 하 고 해당 호스트 이름과 관련 된 구성을 변경 하지 않도록 명시적으로 지시 합니다.


### <a name="enable-with-new-agic-installation"></a>새 AGIC 설치를 사용 하도록 설정
AGIC (버전 0.8.0부터 이상)을 Application Gateway 구성의 하위 집합으로 제한 하려면 `helm-config.yaml` 템플릿을 수정 합니다.
`appgw:` 섹션에서 `shared` 키를 추가 하 고 `true`로 설정 합니다.

```yaml
appgw:
    subscriptionId: <subscriptionId>    # existing field
    resourceGroup: <resourceGroupName>  # existing field
    name: <applicationGatewayName>      # existing field
    shared: true                        # <<<<< Add this field to enable shared Application Gateway >>>>>
```

투구 변경 내용을 적용 합니다.
  1. `AzureIngressProhibitedTarget` CRD가 설치 되어 있는지 확인 합니다.
      ```bash
      kubectl apply -f https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/ae695ef9bd05c8b708cedf6ff545595d0b7022dc/crds/AzureIngressProhibitedTarget.yaml
      ```
  2. 업데이트 투구:
      ```bash
      helm upgrade \
          --recreate-pods \
          -f helm-config.yaml \
          ingress-azure application-gateway-kubernetes-ingress/ingress-azure
      ```

결과적으로 AKS는 `prohibit-all-targets`라는 `AzureIngressProhibitedTarget`의 새 인스턴스를 갖게 됩니다.
```bash
kubectl get AzureIngressProhibitedTargets prohibit-all-targets -o yaml
```

이름이 암시 하는 개체 *`prohibit-all-targets`는 AGIC에서 호스트 및* 경로에 대한 구성을 변경 하는 것을 금지 합니다.
`appgw.shared=true`를 사용 하는 투구 설치는 AGIC를 배포 하지만 Application Gateway를 변경 하지는 않습니다.


### <a name="broaden-permissions"></a>권한 넓히기
`appgw.shared=true`를 사용 하는 투구와 기본 `prohibit-all-targets`는 AGIC에서 구성 적용을 차단 합니다.

다음을 사용 하 여 AGIC 권한 넓히기:
1. 특정 설치를 사용 하 여 새 `AzureIngressProhibitedTarget`을 만듭니다.
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: your-custom-prohibitions
    spec:
      hostname: your.own-hostname.com
    EOF
    ```

2. 사용자 지정 금지를 만든 후에만 기본을 삭제할 수 있습니다.

    ```bash
    kubectl delete AzureIngressProhibitedTarget prohibit-all-targets
    ```

### <a name="enable-for-an-existing-agic-installation"></a>기존 AGIC 설치를 사용 하도록 설정
클러스터에 이미 작동 중인 AKS, Application Gateway 및 구성 된 AGIC 있다고 가정해 보겠습니다. `prod.contosor.com`에 대한 수신이 있으며 AKS에서이에 대한 트래픽을 성공적으로 처리 하 고 있습니다. 기존 Application Gateway에 `staging.contoso.com`를 추가 하려고 하지만 [VM](https://azure.microsoft.com/services/virtual-machines/)에서 호스트 해야 합니다. 기존 Application Gateway를 다시 사용 하 고 `staging.contoso.com`에 대한 수신기 및 백 엔드 풀을 수동으로 구성 하겠습니다. 그러나 Application Gateway 구성 ( [포털](https://portal.azure.com), [ARM Api](https://docs.microsoft.com/rest/api/resources/) 또는 [terraform](https://www.terraform.io/)을 통해)을 수동으로 조정 하는 것은 AGIC의 전체 소유권 가정과 충돌 합니다. 변경 내용을 적용 하 고 나면 잠시 후 AGIC에서 덮어쓰거나 삭제 합니다.

AGIC 구성의 하위 집합을 변경 하지 못하도록 방지할 수 있습니다.

1. `AzureIngressProhibitedTarget` 개체를 만듭니다.
    ```bash
    cat <<EOF | kubectl apply -f -
    apiVersion: "appgw.ingress.k8s.io/v1"
    kind: AzureIngressProhibitedTarget
    metadata:
      name: manually-configured-staging-environment
    spec:
      hostname: staging.contoso.com
    EOF
    ```

2. 새로 만든 개체를 표시 합니다.
    ```bash
    kubectl get AzureIngressProhibitedTargets
    ```

3. 포털을 통해 Application Gateway 구성 수정-수신기, 라우팅 규칙, 백 엔드 등을 추가 합니다. 만든 새 개체 (`manually-configured-staging-environment`)는 AGIC가 `staging.contoso.com`와 관련 된 Application Gateway 구성을 덮어쓸 수 없도록 합니다.
