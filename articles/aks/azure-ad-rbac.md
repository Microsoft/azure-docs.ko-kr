---
title: RBAC와 Azure Kubernetes Service에서 Azure AD를 사용 하 여 클러스터 리소스 제어
description: Azure Active Directory 그룹 멤버 자격을 사용 하 여 Azure Kubernetes Service (AKS)에서 역할 기반 액세스 제어 (RBAC)를 사용 하 여 클러스터 리소스에 대한 액세스를 제한 하는 방법
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: fba54fd23fefbe0029b9a809b23568490f05b23e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616160"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>역할 기반 access control 및 Azure Active Directory id를 사용 하 여 Azure Kubernetes Service에서 클러스터 리소스에 대한 액세스 제어

사용자 인증을 위해 Azure AD(Active Directory)를 사용하도록 AKS(Azure Kubernetes Service)를 구성할 수 있습니다. 이 구성에서는 Azure AD 인증 토큰을 사용 하 여 AKS 클러스터에 로그인 있습니다. Kubernetes를 구성할 수도 있습니다 클러스터 리소스에 대한 액세스를 제한 하려면 역할 기반 액세스 제어 (RBAC) 사용자의 id 또는 그룹 구성원 자격을 기반으로 합니다.

이 문서에서는 네임 스페이스에 대한 액세스를 제어 하 고 Kubernetes RBAC를 사용 하 여 AKS 클러스터에서 리소스를 클러스터에 Azure AD 그룹 구성원 자격을 사용 하는 방법을 보여 줍니다. 예제에서는 그룹 및 사용자가 Azure AD에서 생성 됩니다 다음 역할 및 RoleBindings을 만들고 리소스를 볼 적절 한 권한을 부여 하려면 AKS 클러스터에 생성 됩니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서에서는 Azure AD 통합을 사용 하도록 설정 하는 기존 AKS 클러스터 있다고 가정 합니다. AKS 클러스터에 필요한 경우 참조 [통합 Azure Active Directory AKS 사용 하 여][azure-ad-aks-cli]입니다.

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="create-demo-groups-in-azure-ad"></a>Azure AD에서 데모 그룹 만들기

이 문서에서는 Kubernetes RBAC와 Azure AD 클러스터 리소스에 대한 액세스를 제어 하는 방법을 보여 주는 데 사용할 수 있는 두 개의 사용자 역할을 만들어 보겠습니다. 다음 두 가지 예제 역할 사용 됩니다.

* **응용 프로그램 개발자**
    * 명명 된 사용자 *aksdev* 일부분인 합니다 *appdev* 그룹입니다.
* **사이트 안정성 엔지니어로 근무**
    * 명명 된 사용자 *akssre* 일부분인 합니다 *opssre* 그룹입니다.

프로덕션 환경에서는 기존 사용자 및 Azure AD 테넌트 내에서 그룹을 사용할 수 있습니다.

사용 하 여 AKS 클러스터의 리소스 ID를 가져오려면 먼저 합니다 [az aks 표시][az-aks-show] 명령입니다. 리소스 ID를 변수에 할당할 *AKS_ID* 추가 명령에서 참조 될 수 있도록 합니다.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

사용 하 여 응용 프로그램 개발자를 위한 Azure AD에서 첫 번째 예제에서는 그룹을 만들 합니다 [az ad 그룹 만들기][az-ad-group-create] 명령입니다. 다음 예제에서는 명명 된 그룹을 만듭니다 *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

이제의 Azure 역할 할당을 만듭니다는 *appdev* 사용 하 여 그룹을 [az 역할 할당 만들기][az-role-assignment-create] 명령입니다. 이 할당에 사용 하 여 그룹의 멤버 수 있습니다 `kubectl` 부여 하 여 AKS 클러스터와 상호 작용 하는 *Azure Kubernetes 서비스 클러스터 사용자 역할*입니다.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> 와 같은 오류가 표시 되 면 `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, Azure AD 그룹 개체 id 디렉터리를 통해 전파 시도를 몇 초를 `az role assignment create` 명령을 다시 합니다.

두 번째 예제에서는 그룹 만들기, SREs에이 이름이 *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

그룹의 멤버에 게 부여 하는 Azure 역할 할당을 다시 만드는 합니다 *Azure Kubernetes 서비스 클러스터 사용자 역할*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Azure AD에서 데모 사용자 만들기

응용 프로그램 개발자 및 SREs에 대한 Azure AD에서 만든 두 개의 예제에서는 그룹을 사용 하 여 이제 두 예제에서는 사용자가 만드는 있습니다. 문서 끝에는 RBAC 통합을 테스트 하려면 이러한 계정 사용 하 여 AKS 클러스터에 로그인 할 합니다.

사용 하 여 Azure AD에서 첫 번째 사용자 계정을 만듭니다는 [az ad 사용자가 만들][az-ad-user-create] 명령입니다.

다음 예제에서는 표시 이름을 사용 하 여 사용자를 만듭니다 *AKS Dev* 및의 사용자 계정 이름 (UPN) `aksdev@contoso.com`합니다. Azure AD 테넌트에 대한 확인된 된 도메인을 포함 하도록 UPN을 업데이트 (바꿉니다 *contoso.com* 자체 도메인을 사용 하 여), 사용자 고유의 보안 제공 `--password` 자격 증명:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

이제 사용자를 추가 합니다 *appdev* 사용 하 여 이전 섹션에서 만든 그룹은 [az ad 그룹 구성원 추가][az-ad-group-member-add] 명령:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

두 번째 사용자 계정을 만듭니다. 다음 예제에서는 표시 이름을 사용 하 여 사용자를 만듭니다 *AKS SRE* 및의 사용자 계정 이름 (UPN) `akssre@contoso.com`합니다. 다시 Azure AD 테넌트에 대한 확인된 된 도메인을 포함 하도록 UPN을 업데이트 (바꿉니다 *contoso.com* 자체 도메인을 사용 하 여), 사용자 고유의 보안 제공 `--password` 자격 증명:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>앱 개발자를 위한 AKS 클러스터 리소스 만들기

이제 Azure AD 그룹 및 사용자가 생성 됩니다. 일반 사용자로 AKS 클러스터에 연결 하는 그룹 멤버에 대한 azure 역할 할당을 만들었습니다. 이제 이러한 특정 리소스에 대한 서로 다른 그룹 액세스를 허용 하도록 AKS 클러스터를 구성 해 보겠습니다.

먼저 클러스터를 사용 하 여 관리자 자격 증명 가져오기의 [az aks 자격 증명 가져오기][az-aks-get-credentials] 명령입니다. 일반 다음 섹션 중 하나를 받게 *사용자* 클러스터를 Azure AD 인증 자격 증명 흐름입니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

네임 스페이스를 사용 하 여 AKS 클러스터 만들기는 [kubectl 네임 스페이스 만들기][kubectl-create] 명령입니다. 다음 예제에서는 네임 스페이스 이름을 *개발*:

```console
kubectl create namespace dev
```

Kubernetes에서 *역할* 에 권한을 부여 하려면 권한을 정의 하 고 *RoleBindings* 원하는 사용자 또는 그룹에 적용 합니다. 이러한 할당은 주어진 네임스페이스 또는 전체 클러스터에 적용될 수 있습니다. 자세한 내용은 [RBAC를 사용 하 여 권한 부여][rbac-authorization]합니다.

먼저 역할을 만들 합니다 *개발* 네임 스페이스입니다. 이 역할에는 네임 스페이스에 대한 모든 권한을 부여합니다. 프로덕션 환경에서는 다른 사용자 또는 그룹에 대한 보다 세분화 된 사용 권한을 지정할 수 있습니다.

라는 파일을 만들고 `role-dev-namespace.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

사용 하 여 역할 만들기를 [kubectl 적용][kubectl-apply] 명령 및 YAML 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f role-dev-namespace.yaml
```

다음으로에 대한 리소스 ID를 가져옵니다는 *appdev* 사용 하 여 그룹을 [az ad 그룹 표시][az-ad-group-show] 명령입니다. 이 그룹은 다음 단계에서는 RoleBinding의 제목으로 설정 됩니다.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

이제 한 RoleBinding 만듭니다는 *appdev* 네임 스페이스 액세스에 대한 이전에 만든된 역할을 사용 하는 그룹입니다. `rolebinding-dev-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄으로 바꿉니다 *groupObjectId* 이전 명령에서 그룹 개체 ID 출력을 사용 하 여:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

사용 하 여 RoleBinding 만듭니다를 [kubectl 적용][kubectl-apply] 명령 및 YAML 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>SREs에 AKS 클러스터 리소스 만들기

이제는 SREs에 대한 네임 스페이스, 역할 및 RoleBinding 만드는 이전 단계를 반복 합니다.

먼저 네임 스페이스를 만듭니다 *sre* 사용 하는 [kubectl 네임 스페이스 만들기][kubectl-create] 명령:

```console
kubectl create namespace sre
```

라는 파일을 만들고 `role-sre-namespace.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

사용 하 여 역할 만들기를 [kubectl 적용][kubectl-apply] 명령 및 YAML 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f role-sre-namespace.yaml
```

에 대한 리소스 ID를 가져오려면 합니다 *opssre* 사용 하 여 그룹을 [az ad 그룹 표시][az-ad-group-show] 명령:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

만들기에 대한 RoleBinding 합니다 *opssre* 네임 스페이스 액세스에 대한 이전에 만든된 역할을 사용 하는 그룹입니다. `rolebinding-sre-namespace.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 마지막 줄으로 바꿉니다 *groupObjectId* 이전 명령에서 그룹 개체 ID 출력을 사용 하 여:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

사용 하 여 RoleBinding 만듭니다를 [kubectl 적용][kubectl-apply] 명령 및 YAML 매니페스트의 파일 이름을 지정 합니다.

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Azure AD id를 사용 하 여 클러스터 리소스와 상호 작용

이제 만들고 AKS 클러스터에서 리소스를 관리할 때 예상 되는 권한 작업을 테스트해 보겠습니다. 이 예제에서는 예약 하 고 사용자의 할당 된 네임 스페이스에 있는 pod를 확인 합니다. 그런 다음 할당 된 네임 스페이스 외부에서 일정 및 보기 pod를 시도 합니다.

먼저 다시 설정 합니다 *kubeconfig* 컨텍스트를 사용 하 여 합니다 [az aks 자격 증명 가져오기][az-aks-get-credentials] 명령입니다. 이전 섹션에서 클러스터 관리자 자격 증명을 사용 하 여 컨텍스트를 설정 해야 합니다. 관리 사용자를 Azure AD 로그인 프롬프트를 무시합니다. 없이 `--admin` 모든 요청을 Azure AD를 사용 하 여 인증 해야 하는 사용자 컨텍스트 매개 변수에 적용 됩니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

일정을 사용 하 여 기본 NGINX pod를 [실행 kubectl][kubectl-run] 명령을 합니다 *개발* 네임 스페이스:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

프롬프트 로그인으로 사용자 고유의 자격 증명을 입력 `appdev@contoso.com` 문서의 시작 부분에 만든 계정. 미래에 대한 계정 토큰을 캐시 되는 성공적으로 로그인 하면, `kubectl` 명령입니다. NGINX는 다음 예제 출력 에서처럼 성공적으로 예약 됩니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

이제 사용 하 여는 [kubectl get pod][kubectl-get] 의 pod를 보려면 명령을 합니다 *개발* 네임 스페이스입니다.

```console
kubectl get pods --namespace dev
```

NGINX pod를 성공적으로 다음 예제 출력 에서처럼 *실행*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>만들기 및 할당 된 네임 스페이스 외부에서 클러스터 리소스 보기

이제 외부 pod를 보려고 합니다 *개발* 네임 스페이스입니다. 사용 된 [kubectl get pod][kubectl-get] 이 때 명령을 다시 `--all-namespaces` 같이:

```console
kubectl get pods --all-namespaces
```

사용자의 그룹 멤버 자격을 Kubernetes 역할이 없습니다이 작업을 허용 하는 다음 예제 출력 에서처럼:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

마찬가지로와 같은 다른 네임 스페이스에 있는 pod를 예약 하려고 시도 합니다 *sre* 네임 스페이스입니다. 다음 예제 출력 에서처럼 사용자의 그룹 멤버 자격와 Kubernetes 역할 RoleBinding 이러한 권한을 부여 하려면 정렬 되지 않습니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>AKS 클러스터 리소스에 대한 SRE 액세스 테스트

이 Azure AD 그룹 구성원 자격 및 Kubernetes RBAC 올바르게 작동 하는지 다른 사용자와 그룹 간에 확인 하려면 이전 명령으로 로그인 할 때를 시도 합니다 *opssre* 사용자입니다.

다시 설정 합니다 *kubeconfig* 컨텍스트를 사용 하 여 합니다 [az aks 자격 증명 가져오기][az-aks-get-credentials] 을 이전에 캐시 된 인증 토큰을 지우는 명령 합니다 *aksdev* 사용자:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

일정 및 보기 pod에 할당 된 하려고 *sre* 네임 스페이스입니다. 메시지가 표시 되 면 로그인 사용자 고유의 `opssre@contoso.com` 문서의 시작 부분에 생성 된 자격 증명:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

다음 예제 출력 에서처럼 성공적으로 만드는 고 pod를 볼 수 있습니다.

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

이제 하려고 보거나 할당된 SRE 네임 스페이스 외부에서 pod를 예약 합니다.

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

이러한 `kubectl` 다음 예제 출력 에서처럼 명령이 실패 합니다. 사용자의 그룹 멤버 자격 및 Kubernetes 역할과 RoleBindings 만들 수 있는 권한이 나 다른 네임 스페이스의 리소스 관리자 권한을 부여 하지 않으면:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 AKS 클러스터와 사용자의 리소스 및 Azure AD의 그룹을 만들었습니다. 이러한 모든 리소스를 정리 하려면 다음 명령을 실행 합니다.

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>다음 단계

Kubernetes 클러스터를 보호 하는 방법에 대한 자세한 내용은 참조 하십시오 [AKS에 대한 액세스 및 id 옵션)][rbac-authorization]합니다.

Id 및 리소스 제어에 대한 모범 사례를 참조 하세요 [인증 및 권한 부여 AKS에 대한 유용한][operator-best-practices-identity]합니다.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
