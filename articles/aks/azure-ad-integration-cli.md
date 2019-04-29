---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure CLI를 만들기 및 Azure Active Directory 지원 Azure Kubernetes Service (AKS) 클러스터를 사용 하는 방법을 알아봅니다
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: c403d809aa1fc6cdbb0555eef414f79b02ab5a8a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764130"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Azure CLI를 사용 하 여 Azure Kubernetes 서비스를 사용 하 여 Azure Active Directory 통합

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 할 수 있습니다. 클러스터 운영자는 사용자의 id 또는 directory 그룹 멤버 자격에 따라 Kubernetes 역할 기반 액세스 제어 (RBAC)를 구성할 수도 있습니다.

이 문서에서는 필요한 만드는 방법을 보여 줍니다. 그런 다음 Azure AD 지원 클러스터 배포 및 AKS 클러스터에서 기본 RBAC 역할을 만드는 Azure AD 구성 요소입니다. 할 수도 있습니다 [Azure portal을 사용 하 여 이러한 단계를 완료할][azure-ad-portal]합니다.

이 문서에 사용 되는 전체 샘플 스크립트를 참조 하세요 [Azure CLI 샘플-Azure AD 사용 하 여 AKS 통합][complete-script]합니다.

다음 제한 사항이 적용됩니다.

- Azure AD는 RBAC 사용 클러스터를 새로 만들 때만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.
- *게스트* 사용자가 Azure AD에서 이러한 다른 디렉터리에서 페더레이션된 로그인을 사용 하는 경우에 따라 지원 되지 않습니다.

## <a name="before-you-begin"></a>시작하기 전에

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

일관성 및이 문서의 명령을 실행 하는 데에 원하는 AKS 클러스터 이름에 대 한 변수를 만듭니다. 다음 예제에서는 이름이 *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Azure AD 인증 개요

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션이 구성됩니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다.

## <a name="create-azure-ad-server-component"></a>Azure AD 서버 구성 요소 만들기

AKS와 통합 하려면 만들기 및 id 요청에 대 한 끝점으로 사용 되는 Azure AD 응용 프로그램을 사용 합니다. 필요한 첫 번째 Azure AD 응용 프로그램 사용자에 대 한 Azure AD 그룹 구성원 자격을 가져옵니다.

사용 하 여 서버 응용 프로그램 구성 요소 만들기를 [az ad app 만들기] [ az-ad-app-create] 명령을 선택한 다음 업데이트를 사용 하 여 그룹 멤버 자격 클레임을 [az ad app update] [ az-ad-app-update] 명령입니다. 다음 예제에서는 합니다 *aksname* 에 정의 된 변수를 [시작 하기 전에](#before-you-begin) 섹션 및 변수를 만듭니다

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

이제 서비스 주체를 사용 하 여 서버 앱을 만들 합니다 [az ad sp 만들기] [ az-ad-sp-create] 명령입니다. 이 서비스 주체는 Azure 플랫폼 내에서 인증을 얻는 데 사용 됩니다. 그런 다음, 사용자 비밀 통해 서비스를 가져옵니다 합니다 [az ad sp 자격 증명 재설정] [ az-ad-sp-credential-reset] 명령 및 라는 변수에 할당 *serverApplicationSecret* 중 하나에서 사용 하기 위해는 다음 단계:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Azure AD에는 다음 작업을 수행할 수 있는 권한이 필요 합니다.

* 디렉터리 데이터 읽기
* 로그인 및 사용자 프로필 읽기
* 디렉터리 데이터 읽기

사용 하 여 이러한 사용 권한을 할당 합니다 [az ad app 사용 권한 추가] [ az-ad-app-permission-add] 명령:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

마지막으로 사용 하 여 서버 응용 프로그램에 대 한 이전 단계에서 할당 된 사용 권한을 부여 합니다 [az ad app 권한 부여] [ az-ad-app-permission-grant] 명령입니다. 현재 계정이 테 넌 트 관리자 아닌 경우이 단계가 실패 그렇지 않은 경우 다음을 사용 하 여 관리자의 동의가 필요할 수 있는 정보를 요청 하려면 Azure AD 응용 프로그램에 대 한 사용 권한을 추가 해야 합니다 [az ad app 권한이 관리자 동의][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD 클라이언트 구성 요소 만들기

Kubernetes CLI를 사용 하 여 AKS 클러스터를 사용자가 로그인 하는 경우 두 번째 Azure AD 응용 프로그램 사용 됩니다 (`kubectl`). 이 클라이언트 응용 프로그램 사용자 로부터 인증 요청을 사용 하 고 해당 자격 증명 및 권한을 확인 합니다. 사용 하 여 클라이언트 구성 요소에 대 한 Azure AD 앱 만들기를 [az ad app 만들기] [ az-ad-app-create] 명령:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

사용 하 여 클라이언트 응용 프로그램에 대 한 서비스 주체를 만들려면 합니다 [az ad sp 만들기] [ az-ad-sp-create] 명령:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

OAuth2를 사용 하 여 두 개의 앱 구성 요소 간에 인증 흐름을 허용 하도록 서버 앱에 대 한 ID를 가져오려면 합니다 [az ad app show] [ az-ad-app-show] 명령입니다. 이 oAuth2 ID는 다음 단계에서 사용 됩니다.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

클라이언트 응용 프로그램에 대 한 사용 권한을 추가 하 고 oAuth2 통신을 사용 하 여 서버 응용 프로그램 구성 요소를 사용 하 여 흐름을 [az ad app 사용 권한 추가] [ az-ad-app-permission-add] 명령입니다. 그런 다음 클라이언트 응용 프로그램을 사용 하 여 서버 응용 프로그램 통신에 대 한 사용 권한을 부여 합니다 [az ad app 권한 부여] [ az-ad-app-permission-grant] 명령:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>클러스터 배포

만든 두 개의 Azure AD 응용 프로그램을 사용 하 여 이제 AKS 클러스터 자체를 만듭니다. 먼저 사용 하 여 리소스 그룹을 만듭니다는 [az 그룹 만들기] [ az-group-create] 명령입니다. 다음 예제에서는 리소스 그룹을 *EastUS* 지역:

클러스터에 대 한 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

사용 하 여 Azure 구독 테 넌 트 ID 가져오기 합니다 [az 계정 표시] [ az-account-show] 명령입니다. 그런 다음 사용 하 여 AKS 클러스터를 만듭니다는 [az aks 만듭니다] [ az-aks-create] 명령입니다. AKS 클러스터를 만드는 명령을 서버와 클라이언트 응용 프로그램 Id, 서버 응용 프로그램 서비스 주체 암호 및 테 넌 트 ID

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

마지막으로, 클러스터를 사용 하 여 관리자 자격 증명 가져오기의 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 명령입니다. 일반적인 다음 단계 중 하나를 받게 *사용자* 클러스터를 Azure AD 인증 자격 증명 흐름입니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할*은 부여할 사용 권한을 정의하고, *바인딩*은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

사용자 현재 사용 하 여 로그인에 대 한 사용자 계정 이름 (UPN)을 가져오기 합니다 [az ad 로그인에 사용자 표시] [ az-ad-signed-in-user-show] 명령입니다. 다음 단계에서 Azure AD 통합을 위해이 사용자 계정이 사용 됩니다.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> 동일한 Azure AD 테 넌 트에 대 한 RBAC 바인딩을 부여한 사용자 인 경우에 따라 사용 권한을 할당 합니다 *userPrincipalName*합니다. 사용자가 테 넌 트의 다른 Azure AD에 대 한 쿼리 및 사용 합니다 *objectId* 속성 대신 합니다.

명명 된 YAML 매니페스트 만들기 `basic-azure-ad-binding.yaml` 다음 콘텐츠를 붙여 넣습니다. 마지막 줄으로 바꿉니다 *userPrincipalName_or_objectId* 이전 명령에서 UPN 또는 개체 ID 출력을 사용 하 여:

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

사용 하 여 ClusterRoleBinding 만듭니다를 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

이제 AKS 클러스터에 대 한 Azure AD 인증의 통합을 테스트해 보겠습니다. 설정 된 `kubectl` 구성 컨텍스트를 일반 사용자 자격 증명을 사용 합니다. 이 컨텍스트에서 Azure AD 통해 모든 인증 요청을 전달합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

이제 사용 합니다 [kubectl get pod] [ kubectl-get] 모든 네임 스페이스에서 pod를 보려는 명령:

```console
kubectl get pods --all-namespaces
```

웹 브라우저를 사용 하 여 Azure AD 자격 증명을 사용 하 여 인증 프롬프트에서 기호를 받게 됩니다. 성공적으로 인증 한 후의 `kubectl` 명령은 다음 예제 출력 에서처럼 AKS 클러스터의 pod를 표시 합니다.

```console
$ kubectl get pods --all-namespaces

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

인증 토큰에 대 한 수신 `kubectl` 캐시 됩니다. 만 토큰이 만료 되거나 Kubernetes 구성 파일을이 다시 만들어질 때 로그인 reprompted는 있습니다.

다음 예제 출력 에서처럼 웹 브라우저를 사용 하 여 성공적으로 등록 한 후에 권한 부여 오류 메시지를 표시 하는 경우 다음과 같은 가능한 문제를 확인 합니다.

```console
error: You must be logged in to the server (Unauthorized)
```

* 없는 계정에 로그인 하면 사용자는 *게스트* (이 경우 종종 다른 디렉터리에서 페더레이션된 로그인을 사용 하는 경우) Azure AD 인스턴스에 있습니다.
* 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.

## <a name="next-steps"></a>다음 단계

이 문서에 표시 된 명령을 포함 하는 전체 스크립트에 대 한 참조를 [는 AKS에서 Azure AD 통합 스크립트 샘플 리포지토리][complete-script]합니다.

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면를 참조 하세요 [역할 기반 access control 및 Azure AD id를 사용 하 여 AKS에서 클러스터 리소스에 대 한 액세스 제어][azure-ad-rbac]합니다.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 참조 하십시오 [AKS에 대 한 액세스 및 id 옵션)][rbac-authorization]합니다.

Id 및 리소스 제어에 대 한 모범 사례를 참조 하세요 [인증 및 권한 부여 AKS에 대 한 유용한][operator-best-practices-identity]합니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
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
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
