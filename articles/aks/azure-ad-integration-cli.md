---
title: Azure Kubernetes Service (레거시)와 Azure Active Directory 통합
description: Azure CLI를 사용 하 여 AKS (Azure Kubernetes Service) 클러스터 (레거시)를 Azure Active Directory 만들고 사용 하는 방법을 알아봅니다.
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: ab25ec5406c75316aaa1ee8efd0192dc0207ad79
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612421"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Azure CLI (레거시)를 사용 하 여 Azure Kubernetes Service와 Azure Active Directory 통합

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 할 수 있습니다. 클러스터 운영자는 사용자의 id 또는 디렉터리 그룹 멤버 자격을 기반으로 Kubernetes RBAC (역할 기반 액세스 제어)를 구성할 수도 있습니다.

이 문서에서는 필요한 Azure AD 구성 요소를 만든 다음 Azure AD 사용 클러스터를 배포 하 고 AKS 클러스터에서 기본 RBAC 역할을 만드는 방법을 보여 줍니다.

이 문서에서 사용 되는 전체 샘플 스크립트는 [Azure CLI 샘플-AZURE AD와 AKS 통합][complete-script]을 참조 하세요.

> [!Important]
> AKS에는 서버 또는 클라이언트 응용 프로그램을 관리 하지 않아도 되는 향상 된 [AKS 관리 AZURE AD][managed-aad] 환경이 새롭게 제공 됩니다. 마이그레이션하려는 경우 [여기][managed-aad-migrate]에 있는 지침을 따르세요.

## <a name="the-following-limitations-apply"></a>다음과 같은 제한 사항이 적용됩니다.

- Azure AD는 RBAC 지원 클러스터 에서만 사용 하도록 설정할 수 있습니다.
- Azure AD 레거시 통합은 클러스터를 만드는 동안에만 사용할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상이 설치되고 구성되어 있어야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

[https://shell.azure.com](https://shell.azure.com)으로 이동하여 브라우저에서 Cloud Shell을 엽니다.

일관성을 위해이 문서의 명령을 실행 하는 데 도움이 되도록 원하는 AKS 클러스터 이름에 대 한 변수를 만듭니다. 다음 예에서는 *myakscluster*이름을 사용 합니다.

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션을 구성합니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다.

## <a name="create-azure-ad-server-component"></a>Azure AD 서버 구성 요소 만들기

AKS와 통합 하려면 id 요청에 대 한 끝점 역할을 하는 Azure AD 응용 프로그램을 만들고 사용 합니다. 필요한 첫 번째 Azure AD 응용 프로그램은 사용자에 대 한 Azure AD 그룹 멤버 자격을 가져옵니다.

[Az ad app create][az-ad-app-create] 명령을 사용 하 여 서버 응용 프로그램 구성 요소를 만든 다음 [az ad app update][az-ad-app-update] 명령을 사용 하 여 그룹 멤버 자격 클레임을 업데이트 합니다. 다음 예에서는 [시작 하기 전에](#before-you-begin) 섹션에 정의 된 *aksname* 변수를 사용 하 여 변수를 만듭니다.

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

이제 [az ad sp create][az-ad-sp-create] 명령을 사용 하 여 서버 앱에 대 한 서비스 주체를 만듭니다. 이 서비스 주체는 Azure 플랫폼 내에서 자신을 인증 하는 데 사용 됩니다. 그런 다음 [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용 하 여 서비스 주체 암호를 가져오고 다음 단계 중 하나에서 사용할 *serverapplicationsecret* 이라는 변수에 할당 합니다.

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD 서비스 주체에는 다음 작업을 수행할 수 있는 권한이 필요 합니다.

* 디렉터리 데이터 읽기
* 로그인 및 사용자 프로필 읽기

[Az ad app permission add][az-ad-app-permission-add] 명령을 사용 하 여 이러한 권한을 할당 합니다.

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

마지막으로 [az ad app permission grant][az-ad-app-permission-grant] 명령을 사용 하 여 서버 응용 프로그램에 대해 이전 단계에서 할당 된 사용 권한을 부여 합니다. 현재 계정이 테 넌 트 관리자가 아닌 경우이 단계가 실패 합니다. 또한 [az ad app permission 관리자 동의][az-ad-app-permission-admin-consent]를 사용 하 여 관리자 동의가 필요할 수 있는 정보를 요청 하기 위해 Azure ad 응용 프로그램에 대 한 권한을 추가 해야 합니다.

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD 클라이언트 구성 요소 만들기

두 번째 Azure AD 응용 프로그램은 사용자가 Kubernetes CLI ()를 사용 하 여 AKS 클러스터에 로그인 할 때 사용 됩니다 `kubectl` . 이 클라이언트 응용 프로그램은 사용자의 인증 요청을 받아서 자격 증명 및 사용 권한을 확인 합니다. [Az AD app create][az-ad-app-create] 명령을 사용 하 여 클라이언트 구성 요소에 대 한 Azure AD 앱을 만듭니다.

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

[Az ad sp create][az-ad-sp-create] 명령을 사용 하 여 클라이언트 응용 프로그램에 대 한 서비스 주체를 만듭니다.

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

[Az ad app show][az-ad-app-show] 명령을 사용 하 여 두 앱 구성 요소 간의 인증 흐름을 허용 하도록 서버 앱에 대 한 oAuth2 ID를 가져옵니다. 이 oAuth2 ID는 다음 단계에서 사용 됩니다.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

[Az ad app permission add][az-ad-app-permission-add] 명령을 사용 하 여 oAuth2 통신 흐름을 사용 하도록 클라이언트 응용 프로그램 및 서버 응용 프로그램 구성 요소에 대 한 사용 권한을 추가 합니다. 그런 다음 [az ad app permission grant][az-ad-app-permission-grant] 명령을 사용 하 여 서버 응용 프로그램과 통신 하는 클라이언트 응용 프로그램에 대 한 권한을 부여 합니다.

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>클러스터 배포

두 개의 Azure AD 응용 프로그램을 만든 후 이제 AKS 클러스터 자체를 만듭니다. 먼저 [az group create][az-group-create] 명령을 사용 하 여 리소스 그룹을 만듭니다. 다음 예에서는 *E미국* 지역에 리소스 그룹을 만듭니다.

클러스터에 대 한 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

[Az account show][az-account-show] 명령을 사용 하 여 Azure 구독의 테 넌 트 ID를 가져옵니다. 그런 다음 [az AKS create][az-aks-create] 명령을 사용 하 여 AKS 클러스터를 만듭니다. AKS 클러스터를 만드는 명령은 서버 및 클라이언트 응용 프로그램 Id, 서버 응용 프로그램 서비스 주체 암호 및 테 넌 트 ID를 제공 합니다.

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

마지막으로 [az aks][az-aks-get-credentials] 명령을 사용 하 여 클러스터 관리자 자격 증명을 가져옵니다. 다음 단계 중 하나에서 일반 *사용자* 클러스터 자격 증명을 가져와 Azure AD 인증 흐름이 작동 하는지 확인 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할*은 부여할 사용 권한을 정의하고, *바인딩*은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

[Az ad signed-user show][az-ad-signed-in-user-show] 명령을 사용 하 여 현재 로그인 한 사용자의 upn (사용자 계정 이름)을 가져옵니다. 이 사용자 계정은 다음 단계에서 Azure AD 통합에 대해 사용 하도록 설정 됩니다.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> RBAC 바인딩을 부여한 사용자가 동일한 Azure AD 테 넌 트에 있는 경우 *userPrincipalName*에 따라 사용 권한을 할당 합니다. 사용자가 다른 Azure AD 테 넌 트에 있는 경우에는를 쿼리하고 *objectId* 속성을 대신 사용 합니다.

이라는 YAML 매니페스트를 만들고 `basic-azure-ad-binding.yaml` 다음 콘텐츠를 붙여 넣습니다. 마지막 줄에서 *userPrincipalName_or_objectId*  을 이전 명령의 UPN 또는 개체 ID 출력으로 바꿉니다.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 ClusterRoleBinding을 만들고 yaml 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

이제 AKS 클러스터에 대 한 Azure AD 인증의 통합을 테스트 하겠습니다. `kubectl`일반 사용자 자격 증명을 사용 하도록 구성 컨텍스트를 설정 합니다. 이 컨텍스트는 모든 인증 요청을 Azure AD를 통해 다시 전달 합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

이제 [kubectl get pod][kubectl-get] 명령을 사용 하 여 모든 네임 스페이스에서 pod를 확인 합니다.

```console
kubectl get pods --all-namespaces
```

웹 브라우저를 사용 하 여 Azure AD 자격 증명을 사용 하 여 인증 하는 로그인 프롬프트가 표시 됩니다. 성공적으로 인증 되 면 `kubectl` 다음 예제 출력과 같이 명령이 AKS 클러스터에 pod를 표시 합니다.

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

에 대해 수신 된 인증 토큰이 `kubectl` 캐시 됩니다. 토큰이 만료 되었거나 Kubernetes 구성 파일이 다시 생성 된 경우에만 로그인 하 라는 메시지가 표시 됩니다.

다음 예제 출력과 같이 웹 브라우저를 사용 하 여 성공적으로 로그인 한 후에 권한 부여 오류 메시지가 표시 되는 경우 다음과 같은 가능한 문제를 확인 합니다.

```output
error: You must be logged in to the server (Unauthorized)
```

* 사용자 계정이 동일한 Azure AD 테 넌 트에 있는지 여부에 따라 적절 한 개체 ID 또는 UPN을 정의 했습니다.
* 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.
* 서버에 대 한 응용 프로그램 등록에 정의 된 비밀이를 사용 하 여 구성 된 값과 일치 합니다. `--aad-server-app-secret`

## <a name="next-steps"></a>다음 단계

이 문서에 표시 된 명령을 포함 하는 전체 스크립트는 [AKS samples 리포지토리의 AZURE AD 통합 스크립트][complete-script]를 참조 하세요.

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면 [AKS에서 역할 기반 액세스 제어 및 AZURE ad id를 사용 하 여 클러스터 리소스에 대 한 액세스 제어][azure-ad-rbac]를 참조 하세요.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 [AKS에 대 한 액세스 및 id 옵션][rbac-authorization]을 참조 하세요.

Id 및 리소스 제어에 대 한 모범 사례는 [AKS의 인증 및 권한 부여에 대 한 모범 사례][operator-best-practices-identity]를 참조 하세요.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
