---
title: 미리 보기 - Kubernetes용 Azure Policy 알아보기
description: Azure Policy에서 Rego 및 Open Policy Agent를 사용하여 Azure 또는 온-프레미스에서 Kubernetes를 실행하는 클러스터를 관리하는 방법을 알아봅니다. 이 기능은 미리 보기 기능입니다.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: e9da5caf13994e1c198345958feec43867c0b5f5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509878"
---
# <a name="understand-azure-policy-for-kubernetes-clusters-preview"></a>Kubernetes용 Azure Policy 클러스터 이해(미리 보기)

Azure Policy는 OPA([Open Policy Agent](https://www.openpolicyagent.org/))에 대한 ‘허용 컨트롤러 웹후크’인 [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3을 확장하여 중앙 집중식의 일관된 방식으로 클러스터에 대규모 적용 및 보호 기능을 적용합니다. Azure Policy를 사용하면 한 곳에서 Kubernetes 클러스터의 준수 상태를 관리하고 보고할 수 있습니다. 추가 기능은 다음 함수를 적용합니다.

- Azure Policy 서비스를 사용하여 클러스터에 대한 정책 할당을 확인합니다.
- [제약 조건 템플릿](https://github.com/open-policy-agent/gatekeeper#constraint-templates) 및 [제약 조건](https://github.com/open-policy-agent/gatekeeper#constraints) 사용자 지정 리소스로 클러스터에 정책 정의를 배포합니다.
- 감사 및 규정 준수 세부 정보를 다시 Azure Policy 서비스에 보고

Kubernetes용 Azure Policy는 다음 클러스터 환경을 지원합니다.

- [AKS(Azure Kubernetes Service)](../../../aks/intro-kubernetes.md)
- [Azure Arc 지원 Kubernetes](../../../azure-arc/kubernetes/overview.md)
- [AKS 엔진](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Kubernetes용 Azure Policy는 미리 보기 상태이며 Linux 노드 풀 및 기본 제공 정책 정의만 지원합니다. 기본 제공 정책 정의는 **Kubernetes** 범주에 있습니다. **EnforceOPAConstraint** 및 **EnforceRegoPolicy** 효과를 사용 하는 제한 된 미리 보기 정책 정의와 관련 **Kubernetes 서비스** 범주는 _사용 되지_않습니다. 대신 리소스 공급자 모드를 사용 하 여 _감사_ 및 _거부_ 효과를 사용 `Microsoft.Kubernetes.Data` 합니다.

## <a name="overview"></a>개요

Kubernetes 클러스터에서 Azure Policy를 사용하도록 설정하고 사용하려면 다음 작업을 수행합니다.

1. Kubernetes 클러스터를 구성하고 추가 기능을 설치합니다.
   - [AKS(Azure Kubernetes Service)](#install-azure-policy-add-on-for-aks)
   - [Azure Arc 지원 Kubernetes](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [AKS 엔진](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > 설치와 관련 된 일반적인 문제는 [문제 해결-Azure Policy 추가 기능](../troubleshoot/general.md#add-on-installation-errors)을 참조 하세요.

1. [Kubernetes용 Azure Policy 언어 이해](#policy-language)

1. [Kubernetes 클러스터에 기본 제공 정의 할당](#assign-a-built-in-policy-definition)

1. [유효성 검사 대기](#policy-evaluation)

## <a name="install-azure-policy-add-on-for-aks"></a>AKS에 대한 Azure Policy 추가 기능 설치

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독은 **Microsoft.ContainerService** 및 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정해야 합니다.

1. Azure CLI 버전 2.0.62 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자 및 미리 보기 기능을 등록합니다.

   - Azure Portal:

     1. **Microsoft.ContainerService** 및 **Microsoft.PolicyInsights** 리소스 공급자를 등록합니다. 단계는 [리소스 공급자 및 형식](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)을 참조하세요.

     1. **모든 서비스**를 클릭한 후 **정책**을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.

        :::image type="content" source="../media/policy-for-kubernetes/search-policy.png" alt-text="모든 서비스에서 정책 검색" border="false":::

     1. Azure Policy 페이지의 왼쪽에서 **미리 보기에 조인**을 선택합니다.

        :::image type="content" source="../media/policy-for-kubernetes/join-aks-preview.png" alt-text="AKS용 Policy 미리 보기에 조인" border="false":::

     1. 미리 보기에 추가하려는 구독의 행을 선택합니다.

     1. 구독 목록 맨 위에 있는 **옵트인** 단추를 선택합니다.

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights

     # Feature register: enables installing the add-on
     az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

     # Use the following to confirm the feature has registered
     az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].   {Name:name,State:properties.state}"

     # Once the above shows 'Registered' run the following to propagate the update
     az provider register -n Microsoft.ContainerService
     ```

1. 제한된 미리 보기 정책 정의가 설치된 경우에는 **정책(미리 보기)** 페이지에서 AKS 클러스터의 **사용 안 함** 단추로 추가 기능을 제거합니다.

1. AKS 클러스터 버전은 _1.14_ 이상이어야 합니다. 다음 스크립트를 사용하여 AKS 클러스터 버전의 유효성을 검사합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. AKS용 Azure CLI 미리 보기 확장인 `aks-preview`의 버전 _0.4.0_을 설치합니다.

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 이전에 _aks-preview_ 확장을 설치한 경우 `az extension update --name aks-preview` 명령을 사용하여 업데이트를 설치합니다.

위의 필수 구성 요소 단계가 완료되면 관리하려는 AKS 클러스터에 Azure Policy 추가 기능을 설치합니다.

- Azure portal

  1. **모든 서비스**를 클릭한 후 **Kubernetes 서비스를** 을 검색하고 선택하여 Azure Portal에서 AKS 서비스를 시작합니다.

  1. AKS 클러스터 중 하나를 선택합니다.

  1. Kubernetes 서비스 페이지의 왼쪽에서 **정책(미리 보기)** 을 선택합니다.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS 클러스터의 정책 정의" border="false":::

  1. 기본 페이지에서 **추가 기능 사용** 단추를 선택합니다.

     :::image type="content" source="../media/policy-for-kubernetes/enable-policy-add-on.png" alt-text="AKS용 Azure Policy 추가 기능 사용":::

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > **추가 기능 사용** 단추가 회색으로 표시되면 구독이 미리 보기에 아직 추가되지 않은 것입니다. **추가 기능 사용 안 함** 단추를 사용 하 고 마이그레이션 경고 v2 메시지가 표시 되 면 v1 추가 기능이 설치 되어 v2 정책 정의를 할당 하기 전에 제거 해야 합니다. _사용 되지 않는_ v1 추가 기능은 2020 년 8 월 24 일부 터 v2 추가 기능으로 자동으로 대체 됩니다. 그런 다음 새 v2 버전의 정책 정의를 할당 해야 합니다. 지금 업그레이드 하려면 다음 단계를 수행 합니다.
     >
     > 1. AKS 클러스터의 **정책 (미리 보기)** 페이지를 방문 하 여 AKS 클러스터의 v1 추가 기능이 설치 되어 있는지 확인 하 고 "현재 클러스터에서 Azure Policy 추가 기능을 사용 합니다. 메시지.
     > 1. [추가 기능을 제거](#remove-the-add-on-from-aks)합니다.
     > 1. 추가 기능 **사용** 단추를 선택 하 여 v2 버전의 추가 기능을 설치 합니다.
     > 1. [V2 버전의 v1 기본 제공 정책 정의를 할당 합니다.](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

마지막으로 Azure CLI 명령을 실행하여 최신 추가 기능이 설치되어 있는지 확인하고 `<rg>`를 리소스 그룹 이름으로, `<cluster-name>`을 AKS 클러스터의 이름 `az aks show -g <rg> -n <cluster-name>`으로 바꿉니다. 결과는 다음 출력과 유사하게 표시되고 **config.version**은 `v2`여야 합니다.

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes용 Azure Policy 추가 기능 설치

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독은 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정하고 클러스터 서비스 주체에 대한 역할 할당을 만들어야 합니다.

1. Azure CLI 버전 2.0.62 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자를 사용하도록 설정하려면 [리소스 공급자 및 유형](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계를 따르거나 Azure CLI 또는 Azure PowerShell 명령을 실행합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Kubernetes 클러스터는 버전 _1.14_ 이상이어야 합니다.

1. [Helm 3](https://v3.helm.sh/docs/intro/install/)을 설치합니다.

1. Azure Arc에 대해 Kubernetes 클러스터를 사용하도록 설정했습니다. 자세한 내용은 [Azure Arc에 Kubernetes 클러스터 온보딩](../../../azure-arc/kubernetes/connect-cluster.md)을 참조하세요.

1. Azure Arc 지원 Kubernetes 클러스터의 정규화된 Azure 리소스 ID가 있어야 합니다.

1. 추가 기능에 대한 포트를 엽니다. Azure Policy 추가 기능은 이러한 도메인과 포트를 사용하여 정책 정의 및 할당을 가져오고 클러스터의 규정 준수를 Azure Policy에 다시 보고합니다.

   |도메인 |포트 |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. ‘Policy Insights Data Writer(미리 보기)’ 역할 할당을 Azure Arc 지원 Kubernetes 클러스터에 할당합니다. `<subscriptionId>`를 구독 ID로, `<rg>`를 Azure Arc 지원 Kubernetes 클러스터의 리소스 그룹으로, `<clusterName>`을 Azure Arc 지원 Kubernetes 클러스터 이름으로 바꿉니다. 설치 단계에서 _appId_, _password_ 및 _tenant_에 대해 반환된 값을 추적합니다.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   위 명령 출력의 샘플은 다음과 같습니다.

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

위의 필수 구성 요소 단계가 완료되면 Azure Arc 지원 Kubernetes 클러스터에 Azure Policy 추가 기능을 설치합니다.

1. Helm에 Azure Policy 추가 기능 리포지토리를 추가합니다.

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Helm 차트를 사용하여 Azure Policy 추가 기능을 설치합니다.

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   추가 기능 Helm 차트 설치에 대한 자세한 내용은 GitHub에서 [Azure Policy 추가 기능 Helm 차트 정의](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters)를 참조하세요.

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine"></a>AKS 엔진에 대한 Azure Policy 추가 기능 설치

Azure Policy 추가 기능을 설치하거나 서비스 기능을 사용하도록 설정하기 전에 해당 구독은 **Microsoft.PolicyInsights** 리소스 공급자를 사용하도록 설정하고 클러스터 서비스 주체에 대한 역할 할당을 만들어야 합니다.

1. Azure CLI 버전 2.0.62 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

1. 리소스 공급자를 사용하도록 설정하려면 [리소스 공급자 및 유형](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)의 단계를 따르거나 Azure CLI 또는 Azure PowerShell 명령을 실행합니다.

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. 클러스터 서비스 주체에 대한 역할 할당을 만듭니다.

   - 클러스터 서비스 주체 앱 ID를 모르는 경우 다음 명령을 사용하여 조회합니다.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI를 사용하여 ‘Policy Insights Data Writer(미리 보기)’ 역할 할당을 클러스터 서비스 주체 앱 ID(이전 단계의 _aadClientID_ 값)에 할당합니다. `<subscriptionId>`를 구독 ID로 바꾸고 `<aks engine cluster resource group>`은 AKS 엔진 자체 관리형 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

위의 필수 구성 요소 단계가 완료되면 Azure Policy 추가 기능을 설치합니다. 설치는 AKS 엔진의 만들기 또는 업데이트 주기 동안 또는 기존 클러스터에 대한 독립된 작업으로 수행할 수 있습니다.

- 만들기 또는 업데이트 주기 중에 설치

  자체 관리형 클러스터를 새로 만드는 동안 또는 기존 클러스터에 대한 업데이트로 Azure Policy 추가 기능을 사용하도록 설정하려면 AKS 엔진에 대한 [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) 속성 클러스터 정의를 포함합니다.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  자세한 내용은 외부 가이드인 [AKS 엔진 클러스터 정의](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)를 참조하세요.

- Helm 차트를 사용하여 기존 클러스터에 설치

  다음 단계를 수행하여 클러스터를 준비하고 추가 기능을 설치합니다.

  1. [Helm 3](https://v3.helm.sh/docs/intro/install/)을 설치합니다.

  1. Helm에 Azure Policy 리포지토리를 추가합니다.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     자세한 내용은 [Helm 차트 - 빠른 시작 가이드](https://helm.sh/docs/using_helm/#quickstart-guide)를 참조하세요.

  1. Helm 차트를 사용하여 추가 기능을 설치합니다. `<subscriptionId>`를 구독 ID로 바꾸고 `<aks engine cluster resource group>`은 AKS 엔진 자체 관리형 Kubernetes 클러스터가 있는 리소스 그룹으로 바꿉니다.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     추가 기능 Helm 차트 설치에 대한 자세한 내용은 GitHub에서 [Azure Policy 추가 기능 Helm 차트 정의](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine)를 참조하세요.

     > [!NOTE]
     > Azure Policy 추가 기능과 리소스 그룹 ID 간의 관계로 인해 Azure Policy는 각 리소스 그룹에 대해 AKS 엔진 클러스터를 하나만 지원합니다.

추가 기능 설치가 성공적이고 해당 _azure-policy_ 및 _gatekeeper_ Pod가 실행 중인지 유효성을 검사하려면 다음 명령을 실행합니다.

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>정책 언어

Kubernetes를 관리하기 위한 Azure Policy 언어 구조는 기존 정책 정의의 언어를 따릅니다. 의 [리소스 공급자 모드](./definition-structure.md#resource-provider-modes) 에서는 `Microsoft.Kubernetes.Data` Kubernetes 클러스터를 관리 하는 데 효과 [감사](./effects.md#audit) 및 [거부가](./effects.md#deny) 사용 됩니다. _Audit_ 및 _Deny_ [는 Opa 제약 조건 프레임 워크](https://github.com/open-policy-agent/frameworks/tree/master/constraint) 및 게이트 키퍼 v3 작업과 관련 된 **세부** 정보 속성을 제공 해야 합니다.

정책 정의의 _details.constraintTemplate_ 및 _details.constraint_ 속성의 일부로, Azure Policy는 CRD([CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates))의 URI를 추가 기능에 전달합니다. Rego는 Kubernetes 클러스터에 대한 요청을 유효성 검사하도록 OPA 및 Gatekeeper가 지원하는 언어입니다. Kubernetes 관리의 기존 표준을 지원함으로써 Azure Policy에서는 기존 규칙을 다시 사용하고 Azure Policy와 쌍으로 연결하여 통합 클라우드 규정 준수 보고 환경을 구성할 수 있습니다. 자세한 내용은 [Rego란?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)을 참조하세요.

## <a name="assign-a-built-in-policy-definition"></a>기본 제공 정책 정의 할당

Kubernetes 클러스터에 정책 정의를 할당하려면 적절한 RBAC(역할 기반 액세스 제어) 정책 할당 작업이 할당되어야 합니다. Azure 기본 제공 역할 **리소스 정책 참가자** 및 **소유자** 는 이러한 작업을 수행 합니다. 자세한 내용은 [Azure Policy의 RBAC 권한](../overview.md#rbac-permissions-in-azure-policy)을 참조하세요.

다음 단계를 통해 Azure Portal을 사용하여 클러스터를 관리하기 위한 기본 제공 정책 정의를 찾습니다.

1. Azure Portal에서 Azure Policy 서비스를 시작합니다. 왼쪽 창에서 **모든 서비스**를 선택한 다음, **정책**을 검색하여 선택합니다.

1. Azure Policy 페이지의 왼쪽 창에서 **정의**를 선택합니다.

1. 범주 드롭다운 목록 상자에서 **모두 선택**을 사용하여 필터를 지운 다음, **Kubernetes**를 선택합니다.

1. 정책 정의를 선택한 다음, **할당** 단추를 선택합니다.

1. **범위**를 정책 할당이 적용될 관리 그룹, 구독 또는 Kubernetes 클러스터의 리소스 그룹으로 설정합니다.

   > [!NOTE]
   > Kubernetes 정의를 위해 Azure Policy를 할당할 경우 **범위**에 클러스터 리소스가 포함되어야 합니다. AKS 엔진 클러스터의 경우 **범위**는 클러스터의 리소스 그룹이어야 합니다.

1. 쉽게 식별할 수 있도록 정책 할당에 **이름**과 **설명**을 지정합니다.

1. [정책 적용](./assignment-structure.md#enforcement-mode) 을 아래 값 중 하나로 설정 합니다.

   - **사용** - 클러스터에서 정책을 적용합니다. 위반이 있는 Kubernetes 허용 요청이 거부됩니다.

   - **사용 안 함** - 클러스터에서 정책을 적용하지 않습니다. 위반이 있는 Kubernetes 허용 요청이 거부되지 않습니다. 규정 준수 평가 결과는 계속 제공됩니다. 실행 중인 클러스터에 새 정책 정의를 롤아웃하는 경우 위반이 있는 허용 요청은 거부되지 않으므로 ‘사용 안 함’ 옵션이 정책 정의를 테스트하는 데 도움이 됩니다.

1. **다음**을 선택합니다.

1. **매개 변수 값** 설정

   - 정책 평가에서 Kubernetes 네임스페이스를 제외하려면 **네임 스페이스 제외** 매개 변수에 네임스페이스 목록을 지정합니다. _kube-system_, _gatekeeper-system_ 및 _azure-arc_를 제외하는 것이 좋습니다.

1. **검토 + 만들기**를 선택합니다.

또는 [정책 할당 - 포털](../assign-policy-portal.md) 빠른 시작을 사용하여 Kubernetes 정책을 찾고 할당합니다. 샘플 ‘audit vms’ 대신 Kubernetes 정책 정의를 검색합니다.

> [!IMPORTANT]
> 기본 제공 정책 정의는 **Kubernetes** 범주의 Kubernetes 클러스터에 제공됩니다. 기본 제공 정책 정의 목록은 [Kubernetes 샘플](../samples/built-in-policies.md#kubernetes)을 참조하세요.

## <a name="policy-evaluation"></a>정책 평가

추가 기능은 Azure Policy 서비스를 통해 체크 인하여 15분마다 정책 할당에 변경 사항이 있는지 확인합니다.
새로 고침 주기 동안 추가 기능은 변경 사항이 있는지 확인합니다. 이러한 변경 사항은 제약 조건 템플릿 및 제약 조건에 대한 생성, 업데이트 또는 삭제를 트리거합니다.

Kubernetes 클러스터에서 네임스페이스에 다음 레이블 중 하나가 있으면 위반이 있는 허용 요청이 거부되지 않습니다. 규정 준수 평가 결과는 계속 제공됩니다.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> 클러스터 관리자는 Azure Policy 추가 기능에서 설치한 제약 조건 템플릿 및 제약 조건 리소스를 만들고 업데이트할 수 있는 권한이 있지만 수동 업데이트가 덮어써 있으므로 지원되지 않는 시나리오입니다. Gatekeeper는 추가 기능을 설치하고 Azure Policy 정책 정의를 할당하기 전에 있었던 정책을 계속해서 평가합니다.

추가 기능은 15분마다 클러스터에 대한 전체 검사를 호출합니다. Gatekeeper에서 클러스터에 시도된 변경을 전체 검사하고 실시간 평가한 세부 정보를 수집한 후 추가 기능은 Azure Policy 할당 같은 [규정 준수 세부 정보](../how-to/get-compliance-data.md)를 포함하기 위해 결과를 다시 Azure Policy에 보고합니다. 감사 주기 동안에는 활성 정책 할당의 결과만 반환됩니다. 감사 결과는 실패한 제약 조건의 상태 필드에 나열된 [위반](https://github.com/open-policy-agent/gatekeeper#audit)으로 확인할 수도 있습니다.

> [!NOTE]
> Kubernetes 클러스터에 대한 Azure Policy의 각 규정 준수 보고서에는 지난 45분 이내의 모든 위반이 포함됩니다. 타임스탬프는 위반이 발생한 시기를 나타냅니다.

## <a name="logging"></a>로깅

Kubernetes 컨트롤러/컨테이너로 _azure-policy_ 및 _gatekeeper_ Pod는 모두 Kubernetes 클러스터에 로그를 유지합니다. 로그는 Kubernetes 클러스터의 **인사이트** 페이지에 공개될 수 있습니다.
자세한 내용은 [컨테이너용 Azure Monitor를 사용하여 Kubernetes 클러스터 성능 모니터링](../../../azure-monitor/insights/container-insights-analyze.md)을 참조하세요.

추가 기능 로그를 보려면 `kubectl`을 사용합니다.

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

자세한 내용은 Gatekeeper 설명서의 [Gatekeeper 디버깅](https://github.com/open-policy-agent/gatekeeper#debugging)을 참조하세요.

## <a name="remove-the-add-on"></a>추가 기능 제거

### <a name="remove-the-add-on-from-aks"></a>AKS에서 추가 기능 제거

AKS 클러스터에서 Azure Policy 추가 기능을 제거하려면 Azure Portal 또는 Azure CLI를 사용합니다.

- Azure portal

  1. **모든 서비스**를 클릭한 후 **Kubernetes 서비스를** 을 검색하고 선택하여 Azure Portal에서 AKS 서비스를 시작합니다.

  1. Azure Policy 추가 기능을 사용하지 않으려는 AKS 클러스터를 선택합니다.

  1. Kubernetes 서비스 페이지의 왼쪽에서 **정책(미리 보기)** 을 선택합니다.

     :::image type="content" source="../media/policy-for-kubernetes/policies-preview-from-aks-cluster.png" alt-text="AKS 클러스터의 정책 정의" border="false":::

  1. 기본 페이지에서 **추가 기능 사용 안 함** 단추를 선택합니다.

     :::image type="content" source="../media/policy-for-kubernetes/disable-policy-add-on.png" alt-text="AKS용 Azure Policy 추가 기능 사용 안 함" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Azure Arc 지원 Kubernetes에서 추가 기능 제거

Azure Arc 지원 Kubernetes 클러스터에서 Azure Policy 추가 기능 및 Gatekeeper를 제거하려면 다음 Helm 명령을 실행합니다.

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>AKS 엔진에서 추가 기능 제거

AKS 엔진 클러스터에서 Azure Policy 추가 기능 및 Gatekeeper를 제거하려면 추가 기능 설치에 적절한 방법을 사용합니다.

- AKS 엔진에 대한 클러스터 정의에서 **addons** 속성을 설정하여 설치된 경우:

  _azure-policy_에 대한 **addons** 속성을 false로 변경한 후 클러스터 정의를 AKS 엔진에 다시 배포합니다.


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  자세한 내용은 [AKS 엔진 - Azure Policy 추가 기능 사용 안 함](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on)을 참조하세요.

- Helm 차트와 함께 설치한 경우에는 다음 Helm 명령을 실행합니다.

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy 추가 기능이 수집한 진단 데이터

Kubernetes용 Azure Policy 추가 기능은 제한된 클러스터 진단 데이터를 수집합니다. 이 진단 데이터는 소프트웨어 및 성능과 관련된 중요한 기술 데이터입니다. 다음과 같은 방법으로 사용됩니다.

- Azure Policy 추가 기능을 최신 상태로 유지
- Azure Policy 추가 기능을 안전하고 신뢰할 수 있으며 성능이 뛰어나게 유지
- Azure Policy 추가 기능 향상 - 추가 기능 사용의 집계 분석을 통해

추가 기능이 수집한 정보는 개인 데이터가 아닙니다. 현재 수집되는 세부 정보는 다음과 같습니다.

- Azure Policy 추가 기능 에이전트 버전
- 클러스터 유형
- 클러스터 영역
- 클러스터 리소스 그룹
- 클러스터 리소스 ID
- 클러스터 구독 ID
- 클러스터 OS(예: Linux)
- 클러스터 도시(예: 시애틀)
- 클러스터 주 또는 지방(예: 워싱턴)
- 클러스터 국가 또는 지역(예: 미국)
- 정책 평가에 에이전트를 설치하는 동안 Azure Policy 추가 기능에서 발생한 예외/오류
- Azure Policy 추가 기능으로 설치하지 않은 Gatekeeper 정책 정의 수

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Policy 정의 구조](definition-structure.md)를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.