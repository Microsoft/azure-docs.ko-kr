---
title: Pod 보안 정책을 Azure Kubernetes Service (AKS)에서 사용
description: Pod 입학 PodSecurityPolicy Azure Kubernetes Service (AKS)에서 사용 하 여 제어 방법 알아보기
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027378"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service (AKS)에서 pod 보안 정책을 사용 하 여 클러스터 보안

AKS 클러스터의 보안을 강화 하려면 제한할 수 있습니다 수 있는 pod 예약 합니다. Pod를 허용 하지 않는 리소스를 요청 하는 AKS 클러스터에서 실행할 수 없습니다. Pod 보안 정책을 사용 하 여이 액세스 권한을 정의 합니다. 이 문서에서는 pod 보안 정책을 사용 하 여 AKS에서 pod의 배포를 제한 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 및 옵트인 합니다. 미리 보기는 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 그러나 Azure 기술 지원 서비스에서 지원 되지 않습니다 됩니다. 클러스터를 만들거나 기존 클러스터에 이러한 기능을 추가 하는 경우에 기능이 더 이상 미리 보기 상태 이며 일반 공급 (GA) 라는 될 때까지 해당 클러스터 지원 되지 않습니다.
>
> 미리 보기 기능을 사용 하 여 문제가 발생 하면 [AKS GitHub 리포지토리에서 문제를 제기] [ aks-github] 버그 제목에 미리 보기 기능의 이름입니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 AKS 빠른 시작[Azure CLI 사용][aks-quickstart-cli] 또는 [Azure Portal 사용][aks-quickstart-portal]을 참조하세요.

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

AKS 클러스터를 사용 하 여 pod 보안 정책을 사용 하도록 업데이트 되는 *aks 미리 보기* CLI 확장 합니다. 설치를 *aks 미리 보기* 사용 하 여 Azure CLI 확장을 [az 확장 추가] [ az-extension-add] 다음 예와에서 같이 명령:

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> 이전에 설치한 경우 합니다 *aks 미리 보기* 사용 하 여 확장을 설치 가능한 업데이트를 `az extension update --name aks-preview` 명령입니다.

### <a name="register-pod-security-policy-feature-provider"></a>Pod 보안 정책 기능 공급자 등록

을 만들거나 pod 보안 정책을 사용 하 여 AKS 클러스터를 업데이트 하려면 먼저 구독 기능 플래그를 사용 하도록 설정 합니다. 등록 하는 *PodSecurityPolicyPreview* 기능 플래그를 사용 합니다 [az 기능 등록] [ az-feature-register] 다음 예와에서 같이 명령:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [az feature list][az-feature-list] 명령을 사용하여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

준비가 되면 [az provider register][az-provider-register] 명령을 사용하여 *Microsoft.ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 보안 정책 개요

Kubernetes 클러스터에서 리소스를 만들 때 API 서버에 요청을 가로채 컨트롤러를 허용이 됩니다. 그런 다음 허용 컨트롤러 *의 유효성을 검사* 규칙 집합에 대해 리소스 요청 또는 *변경할* 배포 매개 변수를 변경 하려면 리소스입니다.

*PodSecurityPolicy* pod 사양의 유효성을 검사 하는 입학 허가 컨트롤러를 정의 된 요구 사항을 충족 됩니다. 이러한 요구 사항을 권한 있는 컨테이너의 사용을 제한, 저장소 또는 사용자 또는 그룹으로 컨테이너를 실행할 수의 특정 형식에 대 한 액세스 될 수 있습니다. Pod 사양을 pod 보안 정책에서 설명한 요구 사항을 충족 하지는 리소스를 배포 하려는 경우에 요청이 거부 됩니다. Pod 수 있는 제어 하는이 기능은 AKS에서 예약 된 클러스터 또는 방지 하 고 몇 가지 가능한 보안 취약점으로 인 한 권한 에스컬레이션 합니다.

AKS 클러스터의 pod 보안 정책을 사용 하도록 설정 하면 일부 기본 정책이 적용 됩니다. 이러한 기본 정책은 어떤 포드를 정의 하는 기본 제공 환경을 예약할 수를 제공 합니다. 그러나 클러스터 사용자 사용자 고유의 정책을 정의 될 때까지 pod를 배포 하는 문제를 실행할 수 있습니다. 권장 되는 방법은 다음과 같습니다.

* AKS 클러스터 만들기
* 사용자 고유의 pod 보안 정책 정의
* Pod 보안 정책 기능을 사용 하도록 설정

표시할 기본 정책의 pod 배포를 제한 하는 방법이 문서의 처음 pod 보안 정책 기능을 설정 하 고 사용자 지정 정책을 만듭니다.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS 클러스터에서 pod 보안 정책을 사용 하도록 설정

Pod 보안 정책을 사용 하지 않도록 설정 하거나 설정할 수 있습니다 합니다 [az aks 업데이트할] [ az-aks-update] 명령입니다. 다음 예제에서는 수 있도록 pod의 보안 정책에 클러스터 이름 *myAKSCluster* 이라는 리소스 그룹에서 *myResourceGroup*합니다.

> [!NOTE]
> 실제 사용에 대 한 사용 안 함 pod 보안 정책을 사용자 지정 정책 정의 하기 전에 합니다. 이 문서에서는 기본 정책에서 pod를 제한 하는 방법을 확인 하려면 첫 번째 단계로 pod 보안 정책 설정 배포 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>기본 AKS 정책

Pod 보안 정책을 사용 하면 AKS 라는 두 개의 기본 정책을 만듭니다 *privileged* 하 고 *제한*합니다. 이러한 기본 정책을 제거 하거나 편집 하지 마십시오. 대신, 컨트롤 설정을 정의 하는 고유한 정책을 만듭니다. 보겠습니다 첫 번째 이러한 기본 정책 살펴보고 어떻게 영향을 주는지 pod 배포 됩니다.

사용 가능한 정책 보기를 사용 합니다 [kubectl get psp] [ kubectl-get] 명령을 다음 예와에서 같이 합니다. 기본값의 일부로 *제한* 정책에 사용자가 거부 *PRIV* 권한 있는 pod 에스컬레이션 및 사용자에 대 한 사용 *MustRunAsNonRoot*합니다.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

합니다 *제한 된* pod 보안 정책은 AKS 클러스터의 모든 인증 된 사용자에 적용 됩니다. 이 할당 ClusterRoles ClusterRoleBindings로 제어 됩니다. 사용 합니다 [kubectl get clusterrolebindings] [ kubectl-get] 명령 및 검색 합니다 *기본: 제한:* 바인딩:

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

압축 된 다음 출력에 표시 된 대로 합니다 *psp: 제한* ClusterRole에 할당 됩니다 *시스템: 인증* 사용자입니다. 이 기능 없이 정의 되는 사용자 고유의 정책 제한의 기본 수준을 제공 합니다.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

사용자 고유의 pod 보안 정책 만들기를 시작 하기 전에 포드를 예약 하는 사용자 요청을 사용 하 여 이러한 기본 정책 상호 작용 하는 방법을 이해 하는 것이 반드시 합니다. 다음 몇 섹션에서 보겠습니다 작업에서 이러한 기본 정책을 확인 하려면 몇 가지 포드를 예약 합니다.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS 클러스터에서 테스트 사용자 만들기

기본적으로 사용 하는 경우는 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 명령 합니다 *관리자* 에 추가한 AKS 클러스터에 대 한 자격 증명에 `kubectl` 구성 합니다. Admin 사용자는 pod 보안 정책의 적용을 무시합니다. AKS 클러스터에 대해 Azure Active Directory 통합을 사용 하는 경우 실행 중인 정책의 적용을 확인 하려면 관리자가 아닌 사용자의 자격 증명을 로그인 수 있습니다. 이 문서에서는 사용할 수 있는 AKS 클러스터에서 테스트 사용자 계정을 만들어 보겠습니다.

라는 샘플 네임 스페이스 만들기 *psp aks* 사용 하 여 테스트 리소스에 대 한 합니다 [kubectl 네임 스페이스 만들기] [ kubectl-create] 명령입니다. 그런 다음 명명 된 서비스 계정을 만듭니다 *nonadmin 사용자* 사용 하 여는 [kubectl serviceaccount를 만듭니다] [ kubectl-create] 명령:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

그런 다음에 대 한 RoleBinding를 만듭니다는 *nonadmin 사용자* 사용 하 여 네임 스페이스에서 기본 작업을 수행 하는 [kubectl rolebinding 만들기] [ kubectl-create] 명령:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>관리자 및 관리자가 아닌 사용자에 대 한 별칭 명령 만들기

사용 하는 경우 일반 관리자 간의 차이 강조 표시할 `kubectl` 이전 단계에서 만든 관리자가 아닌 사용자가 두 개의 명령줄 별칭을 만들어:

* **kubectl 관리자** 별칭 일반 관리자 이며 범위는 합니다 *psp aks* 네임 스페이스입니다.
* **kubectl nonadminuser** 에 대 한 별칭은를 *nonadmin 사용자* 범위를 지정 하 고 이전 단계에서 만든를 *psp aks* 네임 스페이스입니다.

다음 명령에서 표시 된 것과 같이 이러한 두 개의 별칭을 만듭니다.

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>권한 있는 pod의 생성 테스트

보겠습니다의 보안 컨텍스트를 사용 하 여 pod를 예약 하는 경우 먼저 테스트 `privileged: true`합니다. 이 보안 컨텍스트에 pod의 권한을 에스컬레이션합니다. 기본 AKS pod 보안 정책에는 이전 섹션에는 *제한 된* 정책이이 요청을 거부 해야 합니다.

라는 파일을 만들고 `nginx-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

사용 하 여 pod를 만듭니다는 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

다음 예제 출력 에서처럼 pod 예약, 실패 합니다.

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod는 리소스가 없습니다. 계속 하기 전에 삭제 되므로 예약 단계를 도달 하지 않습니다.

## <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 포드를 테스트 만들기

이전 예제에서는 pod 사양에는 권한 상승을 요청 했습니다. 이 요청은 기본적으로 거부 됩니다 *제한 된* pod 실패를 예약할 수 있도록 보안 정책에 pod입니다. 이제 권한 상승 요청 하지 않고 해당 동일한 NGINX pod를 실행 해 보겠습니다.

라는 파일을 만들고 `nginx-unprivileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

사용 하 여 pod를 만듭니다는 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes 스케줄러 pod 요청을 수락 합니다. 그러나 사용 하 여 pod의 상태를 보면 `kubectl get pods`, 오류가 발생 했습니다:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

사용 된 [kubectl pod에 설명] [ kubectl-describe] pod에 대 한 이벤트를 보고 하는 명령입니다. 다음 압축 된 예제에서는 해당 요청 하지 않은 경우에 컨테이너 및 이미지를 루트 권한이 필요:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

NGINX 컨테이너 이미지를 포트에 대 한 바인딩을 만들 해야 모든 권한 있는 액세스를 요청 하지 않은 것도 *80*합니다. 포트에 바인딩할 *1024* 하는 *루트* 사용자가 필요 합니다. Pod를 시작 하려고 할 때 합니다 *제한 된* pod 보안 정책은이 요청을 거부 합니다.

이 예와 AKS에서 만든 기본 pod 보안 정책을 적용 하 고 사용자가 수행할 수 작업을 제한 합니다. 기본 NGINX pod 거부 될 수 있습니다 예상한 대로 것 이러한 기본 정책의 동작을 이해 해야 합니다.

다음 단계를 이동 하기 전에 사용 하 여이 테스트 pod를 삭제 합니다 [kubectl pod를 삭제] [ kubectl-delete] 명령:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>특정 사용자 컨텍스트를 사용 하 여 pod의 테스트 만들기

이전 예제에서는 컨테이너 이미지를 자동으로 루트를 사용 하 여 포트 80에 NGINX를 바인딩할 하 려 합니다. 기본적으로이 요청이 거부 되었습니다 *제한 된* pod가 시작 되므로 보안 정책에 pod입니다. 와 같은 특정 사용자 컨텍스트를 사용 하 여 해당 동일한 NGINX pod를 이제 실행 해 보겠습니다 `runAsUser: 2000`합니다.

라는 파일을 만들고 `nginx-unprivileged-nonroot.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

사용 하 여 pod를 만듭니다는 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes 스케줄러 pod 요청을 수락 합니다. 그러나 살펴보면 사용 하 여 pod의 상태 `kubectl get pods`, 이전 예제 보다 다양 한 오류가 발생 합니다.

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

사용 된 [kubectl pod에 설명] [ kubectl-describe] pod에 대 한 이벤트를 보고 하는 명령입니다. 압축 된 다음 예제에는 pod 이벤트를 보여 줍니다.

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

이벤트는 컨테이너 생성 되어 시작을 나타냅니다. pod 실패 한 상태인 이유는 무엇에 대 한 즉시 알아낼 없습니다. 사용 하 여 pod 로그에 살펴보겠습니다 합니다 [kubectl 로그] [ kubectl-logs] 명령:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

다음 예제에서는 로그 출력을 표시 하는 자체 NGINX 구성 내에서 제공 서비스를 시작 하려고 할 때 사용 권한 오류가 발생 합니다. 이 오류는 포트 80 바인딩할 필요가 원인은 다시입니다. 일반 사용자 계정을 정의 하는 pod 사양, 있지만이 사용자 계정은 OS 수준에 NGINX 서비스에 대 한 제한 된 포트에 바인딩하고 시작에 충분 하지 않습니다.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

다시 기본 pod 보안 정책의 동작을 이해 하는 것이 반드시 합니다. 이 오류를 추적 하는 약간 더 어렵습니다 되었으며 다시 있습니다 예상할 수 없는 거부 될 기본 NGINX pod입니다.

다음 단계를 이동 하기 전에 사용 하 여이 테스트 pod를 삭제 합니다 [kubectl pod를 삭제] [ kubectl-delete] 명령:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>사용자 지정 pod 보안 정책 만들기

기본 pod 보안 정책의 동작을 살펴보았으므로 이제 보겠습니다 방법을 제공 합니다 *nonadmin 사용자* 성공적으로 pod를 예약 하려면.

권한 있는 액세스를 요청 하는 pod를 거부 하는 정책을 만들어 보겠습니다. 와 같은 다른 옵션 *runAsUser* 허용 하거나 *볼륨*를 명시적으로 제한 되지 않습니다. 이 유형의 정책은 권한 있는 액세스에 대 한 요청을 거부 하지만 그렇지 않은 경우 요청 된 pod를 실행 하는 클러스터 수 있습니다.

라는 파일을 만들고 `psp-deny-privileged.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

사용 하 여 정책 만들기를 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged.yaml
```

사용 가능한 정책 보기를 사용 합니다 [kubectl get psp] [ kubectl-get] 명령을 다음 예와에서 같이 합니다. 비교는 *psp 거부 권한의* 기본값을 사용 하 여 정책을 *제한* pod를 만들려면 이전 예제에서 적용 된 정책입니다. 사용할 *PRIV* 에스컬레이션 정책에 의해 거부 되었습니다. 사용자 또는 그룹에 대 한 제한은 없습니다 합니다 *psp 거부 권한* 정책입니다.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>사용자 지정 pod 보안 정책을 사용 하 여 사용자 계정 허용

이전 단계에서 만든 pod를 거부 하는 pod 보안 정책을 요청 권한 있는 액세스를 사용 하는 합니다. 만든 정책을 사용할 수 있도록를 *역할* 또는 *ClusterRole*합니다. 그런 다음 연결 사용 하 여 이러한 역할 중 하나를 *RoleBinding* 또는 *ClusterRoleBinding*합니다.

예를 들어 만들 수 있도록 ClusterRole *사용 하 여* 는 *psp 거부 권한* 이전 단계에서 만든 정책입니다. 라는 파일을 만들고 `psp-deny-privileged-clusterrole.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

사용 하 여 ClusterRole 만듭니다를 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

이제 이전 단계에서 만든 ClusterRole 데 ClusterRoleBinding를 만듭니다. 라는 파일을 만들고 `psp-deny-privileged-clusterrolebinding.yaml` 다음 YAML 매니페스트를 붙여넣습니다.

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

사용 하 여 ClusterRoleBinding 합니다 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 이 문서의 첫 번째 단계에서는 AKS 클러스터의 pod 보안 정책 기능 활성화 되었습니다. 권장 되는 고유한 정책을 정의한 후에 pod 보안 정책 기능을 사용 하도록 설정 했습니다. Pod 보안 정책 기능은 설정한 단계입니다. 하나 이상의 사용자 지정 정책에 정의 된 및 이러한 정책과 사용 하 여 연결 된 사용자 계정입니다. Pod 보안 정책을 안전 하 게 이제 기능 및 기본 정책에 의해 발생 한 문제를 최소화 합니다.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>테스트 권한 없는 포드를 다시 생성

사용자 지정 pod 보안 정책 적용 및 정책을 사용 하 여 사용자 계정에 대 한 바인딩을 사용 하 여 권한 없는 포드를 다시 만들려고 시도 보겠습니다. 사용한 것과 동일한 `nginx-privileged.yaml` 를 사용 하 여 pod를 만들려면 매니페스트 합니다 [kubectl 적용] [ kubectl-apply] 명령:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod는 성공적으로 예약 됩니다. 사용 하 여 pod의 상태를 확인 하는 경우는 [kubectl get pod] [ kubectl-get] pod가 명령을 *실행*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

이 예제에서는 다른 사용자 또는 그룹에 대 한 AKS 클러스터에 대 한 액세스를 정의 하는 사용자 지정 pod 보안 정책을 만드는 방법을 보여 줍니다. 따라서 AKS 정책은 실행할 수 있는 pod에서 엄격 하 게 제어를 제공 하는 기본 다음 해야 하는 제한 사항이 올바르게 정의 하는 사용자 고유의 사용자 지정 정책을 만듭니다.

사용 하 여 권한 없는 NGINX pod를 삭제 합니다 [kubectl 삭제] [ kubectl-delete] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

Pod 보안 정책을 사용 하려면 사용 합니다 [az aks 업데이트할] [ az-aks-update] 명령을 다시 합니다. 클러스터 이름에는 다음 예제에서는 사용 하지 않도록 설정 pod 보안 정책 *myAKSCluster* 이라는 리소스 그룹에서 *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

다음으로 ClusterRole 및 ClusterRoleBinding 삭제:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

사용 하 여 네트워크 정책을 삭제할 [kubectl 삭제] [ kubectl-delete] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```console
kubectl delete -f psp-deny-privileged.yaml
```

마지막으로 삭제 합니다 *psp aks* 네임 스페이스:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스의 사용을 차단 하는 pod 보안 정책을 만드는 방법을 보여 주었습니다. 같은 유형의 볼륨 또는 RunAs 사용자는 정책을 적용할 수 있는 기능이 많습니다. 사용 가능한 옵션에 대 한 자세한 내용은 참조는 [Kubernetes pod 보안 정책 참조 문서][kubernetes-policy-reference]합니다.

Pod 네트워크 트래픽을 제한 하는 방법에 대 한 자세한 내용은 참조 하세요. [AKS에 네트워크 정책을 사용 하 여 pod 간에 트래픽을 보호][network-policies]합니다.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
