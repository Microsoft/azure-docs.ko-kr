---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: AKS (Azure Active Directory 사용 가능한 Azure Kubernetes Service) 클러스터를 만드는 방법
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/02/2019
ms.author: mlearned
ms.openlocfilehash: 956cfbafeebadaa23ba3a48f7d38c6f95b412bd9
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77158216"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure Active Directory 통합

Azure Kubernetes 서비스 (AKS)는 사용자 인증을 위해 Azure AD (Azure Active Directory)를 사용 하도록 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 할 수 있습니다.

클러스터 관리자는 사용자의 id 또는 디렉터리 그룹 멤버 자격을 기반으로 Kubernetes RBAC (역할 기반 액세스 제어)를 구성할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

- AKS 및 Azure AD에 대 한 필수 구성 요소를 배포 합니다.
- Azure AD 사용 클러스터를 배포 합니다.
- Azure Portal를 사용 하 여 AKS 클러스터에서 기본 RBAC 역할을 만듭니다.

[Azure CLI][azure-ad-cli]를 사용 하 여 이러한 단계를 완료할 수도 있습니다.

> [!NOTE]
> Azure AD는 새 RBAC 사용 클러스터를 만들 때만 사용할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.

## <a name="authentication-details"></a>인증 세부 정보

Openid connect Connect가 있는 AKS 클러스터에 대 한 Azure AD 인증이 제공 됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다.

Openid connect Connect에 대 한 자세한 내용은 [Openid connect connect 및 AZURE AD를 사용 하 여 웹 응용 프로그램에 대 한 액세스 권한 부여][open-id-connect]를 참조 하세요.

Kubernetes 클러스터 내에서 webhook 토큰 인증을 사용 하 여 토큰을 인증 합니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다.

Webhook 토큰 인증에 대 한 자세한 내용은 Kubernetes 설명서의 [Webhook 토큰 인증][kubernetes-webhook] 섹션을 참조 하세요.

AKS 클러스터에 대 한 Azure AD 인증을 제공 하기 위해 두 개의 Azure AD 응용 프로그램을 만듭니다. 첫 번째 응용 프로그램은 사용자 인증을 제공 하는 서버 구성 요소입니다. 두 번째 응용 프로그램은 인증을 위해 CLI에서 메시지를 표시 하는 경우 사용 되는 클라이언트 구성 요소입니다. 이 클라이언트 응용 프로그램은 클라이언트에서 제공 하는 자격 증명의 실제 인증을 위해 서버 응용 프로그램을 사용 합니다.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성 하는 경우 두 개의 Azure AD 응용 프로그램이 구성 됩니다. 각 응용 프로그램에 대 한 사용 권한을 위임 하는 단계는 Azure 테 넌 트 관리자가 완료 해야 합니다.

## <a name="create-the-server-application"></a>서버 응용 프로그램 만들기

첫 번째 Azure AD 응용 프로그램이 적용 되어 사용자의 Azure AD 그룹 멤버 자격을 가져옵니다. Azure Portal에서이 응용 프로그램을 만들려면 다음을 수행 합니다.

1. **새 등록** > **앱 등록** **Azure Active Directory** > 를 선택 합니다.

    a. 응용 프로그램에 이름을 지정 합니다 (예: *AKSAzureADServer*).

    b. **지원 되는 계정 유형**으로 **이 조직 디렉터리의 계정만**을 선택 합니다.
    
    다. 리디렉션 URI 형식에 대해 **웹** 을 선택한 다음 *https://aksazureadserver* 와 같은 URI 형식 값을 입력 합니다.

    d. 완료 되 면 **등록** 을 선택 합니다.

2. **매니페스트**를 선택한 다음 **groupMembershipClaims:** value를 **All**로 편집 합니다. 업데이트를 완료 한 후 **저장**을 선택 합니다.

    ![그룹 멤버 자격을 모두로 업데이트](media/aad-integration/edit-manifest.png)

3. Azure AD 응용 프로그램의 왼쪽 창에서 **인증서 & 암호**를 선택 합니다.

    a. **+ 새 클라이언트 암호**를 선택 합니다.

    b. *AKS AZURE AD server*와 같은 키 설명을 추가 합니다. 만료 시간을 선택한 후 **추가**를 선택 합니다.

    다. 지금은 표시 되는 키 값을 적어둡니다. Azure AD 사용 AKS 클러스터를 배포 하는 경우이 값을 서버 응용 프로그램 비밀 이라고 합니다.

4. Azure AD 응용 프로그램의 왼쪽 창에서 **API 권한**을 선택 하 고 **+ 권한 추가**를 선택 합니다.

    a. **Microsoft api**에서 **Microsoft Graph**를 선택 합니다.

    b. **위임 된 권한**을 선택 하 고 디렉터리 > 디렉터리 옆의 확인란을 선택 합니다. **모두 읽기 (디렉터리 데이터 읽기)** .

    다. 사용자 >에 대 한 기본 위임 된 권한 **(로그인 및 사용자 프로필 읽기)** 이 존재 하지 않는 경우 옆의 확인란을 선택 합니다.

    d. **응용 프로그램 사용 권한**을 선택한 다음 디렉터리 > 디렉터리 옆의 확인란을 선택 합니다. **모두 읽기 (디렉터리 데이터 읽기)** .

    ![그래프 권한 설정](media/aad-integration/graph-permissions.png)

    e. **권한 추가** 를 선택 하 여 업데이트를 저장 합니다.

    f. **동의 허용**에서 **관리자 동의 부여**를 선택 합니다. 이 단추를 사용할 수 없습니다. 사용 중인 현재 계정이 테 넌 트 관리자로 나열 되지 않습니다.

    권한이 성공적으로 부여 되 면 포털에 다음과 같은 알림이 표시 됩니다.

   ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

5. Azure AD 응용 프로그램의 왼쪽 창에서 **API**표시를 선택 하 고 **+ 범위 추가**를 선택 합니다.
    
    a. **범위 이름**, **관리자 승인 표시 이름**및 *AKSAzureADServer*와 같은 **관리자 동의 설명을** 입력 합니다.

    b. **상태** 가 **사용**으로 설정 되어 있는지 확인 합니다.

    ![다른 서비스와 함께 사용 하기 위해 서버 앱을 API로 노출](media/aad-integration/expose-api.png)

    다. **범위 추가**를 선택 합니다.

6. 응용 프로그램 **개요** 페이지로 돌아가 **응용 프로그램 (클라이언트) ID**를 확인 합니다. Azure AD 사용 AKS 클러스터를 배포할 때이 값을 서버 응용 프로그램 ID 라고 합니다.

    ![애플리케이션 ID 가져오기](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>클라이언트 응용 프로그램 만들기

두 번째 Azure AD 응용 프로그램은 Kubernetes CLI (kubectl)를 사용 하 여 로그인 할 때 사용 됩니다.

1. **새 등록** > **앱 등록** **Azure Active Directory** > 를 선택 합니다.

    a. 응용 프로그램에 이름을 지정 합니다 (예: *AKSAzureADClient*).

    b. **지원 되는 계정 유형**으로 **이 조직 디렉터리의 계정만**을 선택 합니다.

    다. 리디렉션 URI 형식으로 **웹** 을 선택 하 고 *https://aksazureadclient* 와 같은 URI 형식 값을 입력 합니다.

    >[!NOTE]
    >컨테이너에 대 한 Azure Monitor를 지원 하기 위해 새 RBAC 지원 클러스터를 만드는 경우 다음 두 개의 추가 리디렉션 Url을 **웹** 응용 프로그램 유형으로이 목록에 추가 합니다. 첫 번째 기준 URL 값을 `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 해야 하 고 두 번째 기준 URL 값을 `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`해야 합니다.
    >
    >Azure 중국에서이 기능을 사용 하는 경우 첫 번째 기준 URL 값을 `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` 해야 하 고 두 번째 기준 URL 값을 `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`해야 합니다.
    >
    >자세한 내용은 컨테이너의 Azure Monitor에 대 한 [라이브 데이터 (미리 보기) 기능을 설정 하는 방법](../azure-monitor/insights/container-insights-livedata-setup.md) 및 [AD 통합 인증 구성](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 섹션에서 인증을 구성 하는 단계를 참조 하세요.

    d. 완료 되 면 **등록** 을 선택 합니다.

2. Azure AD 응용 프로그램의 왼쪽 창에서 **API 권한**을 선택 하 고 **+ 권한 추가**를 선택 합니다.

    a. **내 api**를 선택 하 고 이전 단계에서 만든 Azure AD 서버 응용 프로그램 (예: *AKSAzureADServer*)을 선택 합니다.

    b. **위임 된 권한**을 선택 하 고 Azure AD 서버 앱 옆의 확인란을 선택 합니다.

    ![애플리케이션 사용 권한 구성](media/aad-integration/select-api.png)

    다. **권한 추가**를 선택합니다.

    d. **동의 허용**에서 **관리자 동의 부여**를 선택 합니다. 현재 계정이 테 넌 트 관리자가 아닌 경우에는이 단추를 사용할 수 없습니다. 사용 권한이 부여 되 면 포털에 다음과 같은 알림이 표시 됩니다.

    ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

3. Azure AD 응용 프로그램의 왼쪽 창에서 **인증**을 선택 합니다.

    - **기본 클라이언트 유형**아래에서 **예** 를 선택 하 여 **클라이언트를 공용 클라이언트로 처리**합니다.

5. Azure AD 응용 프로그램의 왼쪽 창에서 응용 프로그램 ID를 확인 합니다. Azure AD 사용 AKS 클러스터를 배포 하는 경우이 값을 클라이언트 응용 프로그램 ID 라고 합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>테 넌 트 ID 가져오기

다음으로, Azure 테 넌 트의 ID를 가져옵니다. 이 값은 AKS 클러스터를 만들 때 사용 됩니다.

Azure Portal에서 **Azure Active Directory** > **속성** 을 선택 하 고 **디렉터리 ID**를 확인 합니다. Azure AD 사용 AKS 클러스터를 만들 때이 값을 테 넌 트 ID 라고 합니다.

![Azure 테넌트 ID 가져오기](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>AKS 클러스터 배포

[Az group create][az-group-create] 명령을 사용 하 여 AKS 클러스터에 대 한 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[Az aks create][az-aks-create] 명령을 사용 하 여 aks 클러스터를 배포 합니다. 다음으로 다음 샘플 명령에 있는 값을 바꿉니다. 서버 앱 ID, 앱 비밀, 클라이언트 앱 ID 및 테 넌 트 ID에 대해 Azure AD 응용 프로그램을 만들 때 수집 된 값을 사용 합니다.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

AKS 클러스터를 만드는 데 몇 분 정도 걸립니다.

## <a name="create-an-rbac-binding"></a>RBAC 바인딩 만들기

> [!NOTE]
> 클러스터 역할 바인딩 이름은 대/소문자를 구분 합니다.

AKS 클러스터에 Azure Active Directory 계정을 사용 하기 전에 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. 역할은 부여할 사용 권한을 정의 하 고 바인딩은 원하는 사용자에 게 적용 합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조 하세요.

먼저, `--admin` 인수와 함께 [az aks][az-aks-get-credentials] 명령을 사용 하 여 관리자 액세스 권한으로 클러스터에 로그인 합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

그런 다음 AKS 클러스터에 대 한 액세스 권한을 부여 하려는 Azure AD 계정에 대 한 ClusterRoleBinding을 만듭니다. 다음 예에서는 계정에 클러스터의 모든 네임 스페이스에 대 한 모든 권한을 부여 합니다.

- RBAC 바인딩을 부여한 사용자가 동일한 Azure AD 테 넌 트에 있는 경우 UPN (사용자 계정 이름)을 기반으로 사용 권한을 할당 합니다. ClusterRoleBinding에 대 한 YAML 매니페스트를 만드는 단계로 이동 합니다.

- 사용자가 다른 Azure AD 테 넌 트에 있는 경우에는를 쿼리하고 **objectId** 속성을 대신 사용 합니다. 필요한 경우 [az ad user show][az-ad-user-show] 명령을 사용 하 여 필요한 사용자 계정의 objectId를 가져옵니다. 필요한 계정의 UPN (사용자 계정 이름)을 제공 합니다.

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

파일 (예: *rbac-aad)* 을 만들고 다음 내용을 붙여넣습니다. 마지막 줄에서 **userPrincipalName_or_objectId** 를 UPN 또는 개체 ID로 바꿉니다. 선택은 사용자가 동일한 Azure AD 테 넌 트 인지 여부에 따라 달라 집니다.

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

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용 하 여 바인딩을 적용 합니다.

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD 그룹의 모든 구성원에 대해 역할 바인딩을 만들 수도 있습니다. 다음 예제와 같이 그룹 개체 ID를 사용 하 여 Azure AD 그룹을 지정 합니다.

파일 (예: *rbac-aad)* 을 만든 후 다음 내용을 붙여넣습니다. Azure AD 테넌트의 그룹 개체 ID로 업데이트합니다.

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용 하 여 바인딩을 적용 합니다.

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC를 사용 하 여 Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 [Rbac 권한 부여 사용][rbac-authorization]을 참조 하세요.

## <a name="access-the-cluster-with-azure-ad"></a>Azure AD를 사용 하 여 클러스터에 액세스

[Az aks][az-aks-get-credentials] 명령을 사용 하 여 관리자가 아닌 사용자에 대 한 컨텍스트를 가져옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

`kubectl` 명령을 실행 한 후에는 Azure를 사용 하 여 인증 하 라는 메시지가 표시 됩니다. 다음 예제와 같이 화면의 지시에 따라 프로세스를 완료 합니다.

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

프로세스가 완료 되 면 인증 토큰이 캐시 됩니다. 토큰이 만료 되거나 Kubernetes 구성 파일이 다시 생성 되는 경우에만 다시 로그인 하 라는 메시지가 표시 됩니다.

성공적으로 로그인 한 후에 권한 부여 오류 메시지가 표시 되는 경우 다음 조건을 확인 합니다.

```console
error: You must be logged in to the server (Unauthorized)
```


- 사용자 계정이 동일한 Azure AD 테 넌 트에 있는지 여부에 따라 적절 한 개체 ID 또는 UPN을 정의 했습니다.
- 사용자가 200 보다 많은 그룹의 구성원이 아닙니다.
- 서버에 대 한 응용 프로그램 등록에 정의 된 비밀은 `--aad-server-app-secret`를 사용 하 여 구성 된 값과 일치 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면 [AKS에서 역할 기반 액세스 제어 및 AZURE ad id를 사용 하 여 클러스터 리소스에 대 한 액세스 제어][azure-ad-rbac]를 참조 하세요.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 [AKS에 대 한 액세스 및 id 옵션][rbac-authorization]을 참조 하세요.

Id 및 리소스 제어에 대해 자세히 알아보려면 [AKS에서 인증 및 권한 부여에 대 한 모범 사례][operator-best-practices-identity]를 참조 하세요.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
