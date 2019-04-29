---
title: Azure Kubernetes Service와 Azure Active Directory 통합
description: Azure Active Directory 사용 AKS(Azure Kubernetes Service) 클러스터를 만드는 방법
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/09/2018
ms.author: iainfou
ms.openlocfilehash: db92526bd02ba55be5df7ce6999e3099e72b8fa5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62116780"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Kubernetes Service와 Azure Active Directory 통합

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure Active Directory 인증 토큰을 사용 하 여 AKS 클러스터에 서명할 수 있습니다. 또한 클러스터 관리자는 사용자의 id 또는 directory 그룹 멤버 자격에 따라 Kubernetes 역할 기반 액세스 제어 (RBAC)를 구성할 수 있습니다.

이 문서에서는 AKS 및 Azure AD에 대 한 필수 구성 요소를 배포 하는 방법을 다음 Azure AD 사용이 가능한 클러스터를 배포 하 고 AKS 클러스터에서 기본 RBAC 역할을 만드는 방법을 보여 줍니다.

다음 제한 사항이 적용됩니다.

- Azure AD는 RBAC 사용 클러스터를 새로 만들 때만 사용하도록 설정할 수 있습니다. 기존 AKS 클러스터에서는 Azure AD를 사용하도록 설정할 수 없습니다.
- Azure AD에서 *게스트* 사용자(예: 다른 디렉터리에서 페더레이션된 로그인을 사용하는 경우)는 지원되지 않습니다.

## <a name="authentication-details"></a>인증 세부 정보

OpenID Connect와 함께 AKS 클러스터에 Azure AD 인증이 제공됩니다. OpenID Connect는 OAuth 2.0 프로토콜을 기반으로 하는 ID 계층입니다. OpenID Connect에 대한 자세한 내용은 [Open ID 연결 설명서][open-id-connect]를 참조하세요.

Kubernetes 클러스터 내부에서 인증 토큰을 확인하는 데 Webhook 토큰 인증이 사용됩니다. Webhook 토큰 인증은 AKS 클러스터의 일부로 구성 및 관리됩니다. Webhook 토큰 인증에 대한 자세한 내용은 [webhook 인증 설명서][kubernetes-webhook]를 참조하세요.

> [!NOTE]
> AKS 인증을 위해 Azure AD를 구성할 때 두 개의 Azure AD 애플리케이션이 구성됩니다. Azure 테넌트 관리자가 이 작업을 완료해야 합니다.

## <a name="create-server-application"></a>서버 애플리케이션 만들기

첫 번째 Azure AD 애플리케이션은 사용자 Azure AD 그룹 구성원 자격을 가져오는 데 사용됩니다.

1. **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

   애플리케이션에 이름을 지정하고, 애플리케이션 유형에 대해 **웹앱/API**를 선택하고, **로그온 URL**에 URI 서식 지정된 값을 입력합니다. 완료되면 **만들기**를 선택합니다.

   ![Azure AD 등록 만들기](media/aad-integration/app-registration.png)

2. **매니페스트**를 선택하고 `groupMembershipClaims` 값을 `"All"`로 편집합니다.

   완료되면 업데이트를 저장합니다.

   ![그룹 멤버 자격을 모두로 업데이트](media/aad-integration/edit-manifest.png)

3. Azure AD 애플리케이션에서 **설정** > **키**를 선택합니다.

   키 설명을 추가하고, 만료 마감일을 선택하고, **저장**을 선택합니다. 키 값을 적어둡니다. Azure AD 사용 AKS 클러스터를 배포할 때 이 값은 `Server application secret`이라고 합니다.

   ![애플리케이션 개인 키 가져오기](media/aad-integration/application-key.png)

4. Azure AD 애플리케이션으로 돌아와서 **설정** > **필요한 권한** > **추가** > **API 선택** > **Microsoft Graph** > **선택**을 선택합니다.

   ![Graph API 선택](media/aad-integration/graph-api.png)

5. **애플리케이션 사용 권한** 아래에서 **디렉터리 데이터 읽기** 옆을 체크합니다.

   ![애플리케이션 그래프 사용 권한 설정](media/aad-integration/read-directory.png)

6. **위임된 사용 권한**  아래에서 **로그인 및 사용자 프로필 읽기** 및 **디렉터리 데이터 읽기** 옆을 체크합니다. 완료되면 업데이트를 저장합니다.

   ![애플리케이션 그래프 사용 권한 설정](media/aad-integration/delegated-permissions.png)

   **완료**를 선택합니다.

7. API 목록에서 *Microsoft Graph*를 선택한 다음, **권한 부여**를 선택합니다. 현재 계정이 테넌트 관리자가 아닌 경우 이 단계가 실패합니다.

   ![애플리케이션 그래프 사용 권한 설정](media/aad-integration/grant-permissions.png)

   권한을 성공적으로 부여 받으면 포털에서 다음 알림이 표시됩니다.

   ![성공적인 권한 부여의 알림](media/aad-integration/permissions-granted.png)

8. 애플리케이션으로 돌아오고 **애플리케이션 ID**를 기록해 둡니다. Azure AD 사용 AKS 클러스터를 배포할 때 이 값은 `Server application ID`이라고 합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id.png)

## <a name="create-client-application"></a>클라이언트 애플리케이션 만들기

두 번째 Azure AD 애플리케이션은 Kubernetes CLI(kubectl)를 사용하여 로그인할 때 사용됩니다.

1. **Azure Active Directory** > **앱 등록** > **새 애플리케이션 등록**을 선택합니다.

   애플리케이션에 이름을 지정하고, 애플리케이션 유형에 대해 **네이티브**를 선택하고, **리디렉션 URI**에 URI 서식 지정된 값을 입력합니다. 완료되면 **만들기**를 선택합니다.

   ![AAD 등록 만들기](media/aad-integration/app-registration-client.png)

2. Azure AD 애플리케이션에서 **설정** > **필요한 권한** > **추가** > **API 선택**을 선택하고 이 문서의 마지막 단계에서 만든 서버 애플리케이션의 이름을 검색합니다.

   ![애플리케이션 사용 권한 구성](media/aad-integration/select-api.png)

3. 애플리케이션 옆에 확인 표시를 두고 **선택**을 클릭합니다.

   ![AKS AAD 서버 애플리케이션 엔드포인트 선택](media/aad-integration/select-server-app.png)

   **완료**를 선택합니다.

4. 목록에서 서버 API를 선택한 다음, **권한 부여**를 선택합니다.

   ![권한 부여](media/aad-integration/grant-permissions-client.png)

5. AD 애플리케이션으로 돌아오고, **애플리케이션 ID**를 기록해 둡니다. Azure AD 사용 AKS 클러스터를 배포할 때 이 값은 `Client application ID`이라고 합니다.

   ![애플리케이션 ID 가져오기](media/aad-integration/application-id-client.png)

## <a name="get-tenant-id"></a>테넌트 ID 가져오기

마지막으로 Azure 테넌트의 ID를 가져옵니다. 이 값은 AKS 클러스터를 배포하는 경우에도 사용됩니다.

Azure Portal에서 **Azure Active Directory** > **속성**을 선택하고 **디렉터리 ID**를 기록해 둡니다. Azure AD 사용 AKS 클러스터를 배포할 때 이 값은 `Tenant ID`이라고 합니다.

![Azure 테넌트 ID 가져오기](media/aad-integration/tenant-id.png)

## <a name="deploy-cluster"></a>클러스터 배포

[az group create][az-group-create] 명령을 사용하여 AKS 클러스터에 대한 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az aks create][az-aks-create] 명령을 사용하여 클러스터를 배포합니다. 아래 샘플 명령의 값을 Azure AD 애플리케이션을 만들 때 수집한 값으로 바꿉니다.

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

## <a name="create-rbac-binding"></a>RBAC 바인딩 만들기

Azure Active Directory 계정을 AKS 클러스터와 함께 사용하려면 역할 바인딩 또는 클러스터 역할 바인딩을 만들어야 합니다. *역할*은 부여할 사용 권한을 정의하고, *바인딩*은 원하는 사용자에게 역할을 적용합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

먼저 사용 하 여는 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 명령과 `--admin` 인수에 대 한 관리자 액세스를 사용 하 여 클러스터에 로그인 합니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

다음으로, AKS 클러스터에 대 한 액세스 권한을 부여 하려는 Azure AD 계정의 ClusterRoleBinding를 만듭니다. 다음 예제에서는 클러스터의 모든 네임 스페이스에는 계정 전체 액세스를 제공합니다.

- 에 대 한 RBAC 바인딩을 동일한 Azure AD 테 넌 트에 부여 하면 사용자, 사용자 계정 이름 (UPN)에 따라 사용 권한을 할당 합니다. 이동할 단계로 ClusterRuleBinding에 대 한 YAML 매니페스트를 만듭니다.

- 사용자가 테 넌 트의 다른 Azure AD에 대 한 쿼리 및 사용 합니다 *objectId* 속성 대신 합니다. 필요한 경우는 *objectId* 필요한 사용자의 계정을 사용 하 여 합니다 [az ad 사용자 표시] [ az-ad-user-show] 명령입니다. 필요한 계정의 사용자 계정 이름 (UPN)을 제공 합니다.

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

*rbac-aad-user.yaml*과 같은 파일을 만들고 다음 내용을 붙여 넣습니다. 마지막 줄으로 바꿉니다 *userPrincipalName_or_objectId* 경우에 따라 UPN 또는 개체 ID를 사용 하 여 사용자가 동일한 Azure AD 테 넌 트 여부.

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

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 바인딩을 적용합니다.

```console
kubectl apply -f rbac-aad-user.yaml
```

Azure AD 그룹의 모든 구성원에 대해 역할 바인딩을 만들 수도 있습니다. 다음 예제에서와 같이 Azure AD 그룹은 그룹 개체 ID를 사용하여 지정됩니다. *rbac-aad-group.yaml*과 같은 파일을 만들고 다음 내용을 붙여 넣습니다. Azure AD 테넌트의 그룹 개체 ID로 업데이트합니다.

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

다음 예제와 같이 [kubectl apply][kubectl-apply] 명령을 사용하여 바인딩을 적용합니다.

```console
kubectl apply -f rbac-aad-group.yaml
```

RBAC를 사용하여 Kubernetes 클러스터 보호에 대한 자세한 내용은 [RBAC 권한 부여 사용][rbac-authorization]을 참조하세요.

## <a name="access-cluster-with-azure-ad"></a>Azure AD를 사용하여 클러스터에 액세스

다음으로 [az aks get-credentials][az-aks-get-credentials] 명령을 사용하여 비 관리자 사용자에 대한 컨텍스트를 끌어 옵니다.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

모든 kubectl 명령을 실행한 후에 Azure에 인증할지를 묻는 메시지가 표시됩니다. 화면에 나타나는 지침을 따릅니다.

```console
$ kubectl get nodes

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-1   Ready     agent     1h        v1.9.9
aks-nodepool1-79590246-2   Ready     agent     1h        v1.9.9
```

완료되면 인증 토큰이 캐시됩니다. 만 경우 토큰이 만료 되었는지 또는 다시 생성 하는 Kubernetes 구성 파일에 로그인 reprompted는 있습니다.

성공적으로 로그인한 후에 권한 부여 오류 메시지가 표시되는 경우 다음 사항에 해당하는지 여부를 확인합니다.
1. 로그인한 사용자는 Azure AD 인스턴스의 게스트가 아닙니다(다른 디렉터리에서 페더레이션 로그인을 사용하는 경우가 많음).
2. 사용자는 200개가 넘는 그룹의 멤버가 아닙니다.

```console
error: You must be logged in to the server (Unauthorized)
```

## <a name="next-steps"></a>다음 단계

Azure AD 사용자 및 그룹을 사용 하 여 클러스터 리소스에 대 한 액세스를 제어 하려면를 참조 하세요 [역할 기반 access control 및 Azure AD id를 사용 하 여 AKS에서 클러스터 리소스에 대 한 액세스 제어][azure-ad-rbac]합니다.

Kubernetes 클러스터를 보호 하는 방법에 대 한 자세한 내용은 참조 하십시오 [AKS에 대 한 액세스 및 id 옵션)][rbac-authorization]합니다.

Id 및 리소스 제어에 대 한 모범 사례를 참조 하세요 [인증 및 권한 부여 AKS에 대 한 유용한][operator-best-practices-identity]합니다.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
