---
title: Azure Kubernetes Service에서 pod 보안 정책 사용 (AKS)
description: AKS (Azure Kubernetes Service)에서 PodSecurityPolicy를 사용 하 여 pod 입학을 제어 하는 방법을 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: 5aec645c19ee5f813fdefb57d728c14688da8712
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926404"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service에서 pod 보안 정책을 사용 하 여 클러스터 보호 (AKS)

AKS 클러스터의 보안을 향상 시키기 위해 예약할 수 있는 pod을 제한할 수 있습니다. 허용 하지 않는 리소스를 요청 하는 pod는 AKS 클러스터에서 실행할 수 없습니다. Pod 보안 정책을 사용 하 여이 액세스를 정의 합니다. 이 문서에서는 pod 보안 정책을 사용 하 여 AKS에서 pod의 배포를 제한 하는 방법을 보여 줍니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 추가 정보 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 기존 AKS 클러스터가 있다고 가정합니다. AKS 클러스터가 필요한 경우 [Azure CLI를 사용][aks-quickstart-cli] 하거나 [Azure Portal를 사용][aks-quickstart-portal]하 여 AKS 빠른 시작을 참조 하세요.

Azure CLI 버전 2.0.61 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

### <a name="install-aks-preview-cli-extension"></a>aks-preview CLI 확장 설치

Pod 보안 정책을 사용 하려면 *aks-preview* CLI extension version 0.4.1 이상이 필요 합니다. [Az extension add][az-extension-add] 명령을 사용 하 여 *aks-preview* Azure CLI 확장을 설치한 다음 [az extension update][az-extension-update] 명령을 사용 하 여 사용 가능한 업데이트를 확인 합니다.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod 보안 정책 기능 공급자 등록

Pod 보안 정책을 사용 하도록 AKS 클러스터를 만들거나 업데이트 하려면 먼저 구독에서 기능 플래그를 사용 하도록 설정 합니다. *PodSecurityPolicyPreview* 기능 플래그를 등록 하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용 합니다.

> [!CAUTION]
> 구독에 기능을 등록 하면 현재 해당 기능을 등록 취소할 수 없습니다. 일부 미리 보기 기능을 사용 하도록 설정한 후에는 구독에서 만든 모든 AKS 클러스터에 대한 기본값을 사용할 수 있습니다. 프로덕션 구독에서 미리 보기 기능을 사용 하도록 설정 하지 마세요. 별도의 구독을 사용 하 여 미리 보기 기능을 테스트 하 고 피드백을 수집 합니다.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. [Az feature list][az-feature-list] 명령을 사용 하 여 등록 상태를 확인할 수 있습니다.

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

준비가 되 면 [az provider register][az-provider-register] 명령을 사용 하 여 *ContainerService* 리소스 공급자 등록을 새로 고칩니다.

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Pod 보안 정책 개요

Kubernetes 클러스터에서 허용 컨트롤러는 리소스를 만들 때 API 서버에 대한 요청을 가로채는 데 사용 됩니다. 그러면 허용 컨트롤러는 규칙 집합에 대해 리소스 요청의 *유효성을 검사* 하거나 배포 매개 변수를 변경 하도록 *리소스를 변경할* 수 있습니다.

*PodSecurityPolicy* 는 pod 사양이 정의 된 요구 사항을 충족 하는지 확인 하는 허용 컨트롤러입니다. 이러한 요구 사항에 따라 권한 있는 컨테이너의 사용, 특정 유형의 저장소에 대한 액세스 또는 컨테이너를 실행할 수 있는 사용자 또는 그룹을 제한할 수 있습니다. Pod 사양이 pod 보안 정책에 설명 된 요구 사항을 충족 하지 않는 리소스를 배포 하려고 하면 요청이 거부 됩니다. AKS 클러스터에서 예약할 수 있는 pod을 제어 하는이 기능은 몇 가지 가능한 보안 취약점 또는 권한 에스컬레이션을 방지 합니다.

AKS 클러스터에서 pod 보안 정책을 사용 하도록 설정 하면 일부 기본 정책이 적용 됩니다. 이러한 기본 정책은 예약할 수 있는 pod를 정의 하는 기본 경험을 제공 합니다. 그러나 사용자 고유의 정책을 정의할 때까지 클러스터 사용자가 pod를 배포 하는 데 문제가 발생할 수 있습니다. 권장 되는 방법은 다음과 같습니다.

* AKS 클러스터 만들기
* 사용자 고유의 pod 보안 정책 정의
* Pod 보안 정책 기능 사용

기본 정책의 제한 pod 배포를 표시 하기 위해이 문서에서는 먼저 pod 보안 정책 기능을 사용 하도록 설정한 다음 사용자 지정 정책을 만듭니다.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>AKS 클러스터에서 pod 보안 정책 사용

[Az aks update][az-aks-update] 명령을 사용 하 여 pod 보안 정책을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹의 클러스터 이름 *myAKSCluster* 에서 pod 보안 정책을 사용 하도록 설정 합니다.

> [!NOTE]
> 실제 사용의 경우 사용자 고유의 사용자 지정 정책을 정의할 때까지 pod 보안 정책을 사용 하도록 설정 하지 마세요. 이 문서에서는 기본 정책이 pod 배포를 제한 하는 방법을 확인 하는 첫 번째 단계로 pod 보안 정책을 사용 하도록 설정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>기본 AKS 정책

Pod 보안 정책을 사용 하도록 설정 하는 경우 AKS은 *권한*있는 이라는 기본 정책 하나를 만듭니다. 기본 정책을 편집 하거나 제거 하지 마세요. 대신 제어 하려는 설정을 정의 하는 고유한 정책을 만듭니다. 먼저 이러한 기본 정책이 pod 배포에 영향을 주는 방식을 살펴보겠습니다.

사용 가능한 정책을 보려면 다음 예제에 표시 된 것 처럼 [kubectl get psp][kubectl-get] 명령을 사용 합니다.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

*권한* 있는 pod 보안 정책은 AKS 클러스터의 모든 인증 된 사용자에 게 적용 됩니다. 이 할당은 ClusterRoles 및 ClusterRoleBindings에 의해 제어 됩니다. [Kubectl get clusterrolebindings][kubectl-get] 명령을 사용 하 여 *기본: 특권:* binding을 검색 합니다.

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

다음 압축 된 출력과 같이 *psp: 제한* 된 ClusterRole는 모든 *시스템: 인증* 된 사용자에 게 할당 됩니다. 이 기능은 사용자 고유의 정책을 정의 하지 않아도 기본적인 제한 수준을 제공 합니다.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

사용자 고유의 pod 보안 정책을 만들기 전에 이러한 기본 정책에서 pod 일정을 예약 하는 방법을 이해 하는 것이 중요 합니다. 다음 몇 개 섹션에서는 몇 가지 pod를 예약 하 여 이러한 기본 정책이 작동 하는 것을 확인 하겠습니다.

## <a name="create-a-test-user-in-an-aks-cluster"></a>AKS 클러스터에서 테스트 사용자 만들기

기본적으로 [az aks][az-aks-get-credentials] 명령을 사용 하는 경우 aks 클러스터에 대한 *관리자* 자격 증명이 `kubectl` 구성에 추가 됩니다. 관리 사용자는 pod 보안 정책의 적용을 무시 합니다. AKS 클러스터에 Azure Active Directory 통합을 사용 하는 경우 관리자가 아닌 사용자의 자격 증명을 사용 하 여 로그인 하 여 정책 적용을 확인할 수 있습니다. 이 문서에서는 AKS 클러스터에서 사용할 수 있는 테스트 사용자 계정을 만들어 보겠습니다.

[Kubectl create namespace][kubectl-create] 명령을 사용 하 여 테스트 리소스에 대해 *psp-aks* 라는 샘플 네임 스페이스를 만듭니다. 그런 다음 [kubectl create serviceaccount][kubectl-create] 명령을 사용 하 여 *nonadmin-user* 라는 서비스 계정을 만듭니다.

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

그런 다음 [kubectl create rolebinding][kubectl-create] 명령을 사용 하 여 네임 스페이스에서 기본 작업을 수행 하기 위해 *nonadmin-사용자* 에 대한 rolebinding을 만듭니다.

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>관리자 및 관리자가 아닌 사용자에 대한 별칭 만들기 명령

이전 단계에서 만든 `kubectl` 및 비관리자 사용자를 사용할 때 일반 관리자 사용자 간의 차이점을 강조 표시 하려면 다음 두 명령줄 별칭을 만듭니다.

* **Kubectl** 별칭은 일반 관리자 사용자를 위한 것 이며,은 *psp-aks* 네임 스페이스로 범위가 지정 됩니다.
* **Kubectl 사용자** 별칭은 이전 단계에서 만든 *nonadmin* 이 아닌 사용자를 위한 것 이며,이 별칭은 *psp-aks* 네임 스페이스로 범위가 지정 됩니다.

다음 명령에 표시 된 것 처럼 이러한 두 별칭을 만듭니다.

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>권한 있는 pod 생성 테스트

먼저 `privileged: true`의 보안 컨텍스트를 사용 하 여 pod를 예약할 때 발생 하는 결과를 테스트 하겠습니다. 이 보안 컨텍스트는 pod의 권한을 에스컬레이션 합니다. 기본 AKS pod 보안 정책을 보여 준 이전 섹션에서 *제한* 된 정책은이 요청을 거부 해야 합니다.

`nginx-privileged.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

다음 예제 출력과 같이 pod가 예약 되지 않습니다.

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Pod는 일정 단계에 도달 하지 않으므로 이동 하기 전에 삭제할 리소스가 없습니다.

## <a name="test-creation-of-an-unprivileged-pod"></a>권한 없는 pod의 테스트 만들기

이전 예제에서 pod 사양은 권한 있는 에스컬레이션을 요청 했습니다. 이 요청은 기본 *제한* 된 pod 보안 정책에 의해 거부 되므로 pod를 예약 하지 못합니다. 이제 권한 상승 요청 없이 동일한 NGINX pod를 실행 해 보겠습니다.

`nginx-unprivileged.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Kubernetes scheduler는 pod 요청을 허용 합니다. 그러나 `kubectl get pods`를 사용 하 여 pod의 상태를 살펴보면 오류가 발생 합니다.

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

[Kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 pod에 대한 이벤트를 확인 합니다. 다음 압축 예제에서는 요청 하지 않았더라도 컨테이너와 이미지에 루트 권한이 필요 합니다.

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

권한 있는 액세스를 요청 하지 않은 경우에도 NGINX의 컨테이너 이미지는 포트 *80*에 대한 바인딩을 만들어야 합니다. 포트 *1024* 를 바인딩하려면 *루트* 사용자가 필요 합니다. Pod가 시작 되려고 할 때 *제한* 된 pod 보안 정책은이 요청을 거부 합니다.

이 예에서는 AKS에서 만든 기본 pod 보안 정책이 적용 되 고 사용자가 수행할 수 있는 작업을 제한 하는 방법을 보여 줍니다. 기본 NGINX pod를 거부 하지 않을 수 있으므로 이러한 기본 정책의 동작을 이해 하는 것이 중요 합니다.

다음 단계로 이동 하기 전에 [kubectl delete pod][kubectl-delete] 명령을 사용 하 여이 테스트 pod를 삭제 합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>특정 사용자 컨텍스트를 사용 하 여 pod 생성 테스트

이전 예제에서 컨테이너 이미지는 자동으로 root를 사용 하 여 NGINX를 포트 80에 바인딩합니다. 이 요청은 기본 *제한* 된 pod 보안 정책에 의해 거부 되었으므로 pod를 시작 하지 못합니다. 이제 `runAsUser: 2000`와 같은 특정 사용자 컨텍스트를 사용 하 여 동일한 NGINX pod를 실행 해 보겠습니다.

`nginx-unprivileged-nonroot.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Kubernetes scheduler는 pod 요청을 허용 합니다. 그러나 `kubectl get pods`를 사용 하 여 pod의 상태를 살펴보면 이전 예제와 다른 오류가 발생 합니다.

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

[Kubectl 설명 pod][kubectl-describe] 명령을 사용 하 여 pod에 대한 이벤트를 확인 합니다. 다음 압축 예제에서는 pod 이벤트를 보여 줍니다.

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

이벤트는 컨테이너를 만들고 시작 했음을 표시 합니다. Pod가 실패 한 상태에 있다는 것을 명확히 알 수 없습니다. [Kubectl logs][kubectl-logs] 명령을 사용 하 여 pod 로그를 살펴보겠습니다.

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

다음 예제 로그 출력은 NGINX 구성 자체 내에서 서비스를 시작 하려고 할 때 사용 권한 오류가 있음을 나타냅니다. 이 오류는 포트 80에 바인딩해야 하는 경우에 다시 발생 합니다. Pod 사양은 일반 사용자 계정을 정의 했지만,이 사용자 계정은 NGINX 서비스를 시작 하 고 제한 된 포트에 바인딩하기 위해 OS 수준에서 충분 하지 않습니다.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

다시, 기본 pod 보안 정책의 동작을 이해 하는 것이 중요 합니다. 이 오류는 추적 하기가 약간 어려우므로 기본 NGINX pod를 거부 하는 것은 아닙니다.

다음 단계로 이동 하기 전에 [kubectl delete pod][kubectl-delete] 명령을 사용 하 여이 테스트 pod를 삭제 합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>사용자 지정 pod 보안 정책 만들기

이제 기본 pod 보안 정책의 동작을 살펴보았으므로 *관리자가 아닌 사용자* 가 pod를 예약 하는 방법을 제공 하겠습니다.

권한 있는 액세스를 요청 하는 pod를 거부 하는 정책을 만들어 보겠습니다. *RunAsUser* 또는 허용 된 *볼륨과*같은 기타 옵션은 명시적으로 제한 되지 않습니다. 이 유형의 정책은 권한 있는 액세스에 대한 요청을 거부 하지만, 그렇지 않으면 클러스터에서 요청 된 pod을 실행할 수 있습니다.

`psp-deny-privileged.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 정책을 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged.yaml
```

사용 가능한 정책을 보려면 다음 예제와 같이 [kubectl get psp][kubectl-get] 명령을 사용 합니다. 이전 예제에서 적용 된 기본 *제한* 정책과 함께 *psp-거부 권한* 정책을 비교 하 여 pod를 만듭니다. 정책에 따라 *PRIV* 에스컬레이션 사용만 거부 됩니다. *Psp-거부 권한* 정책에 대한 사용자 또는 그룹에는 제한이 없습니다.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>사용자 계정이 사용자 지정 pod 보안 정책을 사용할 수 있도록 허용

이전 단계에서는 권한 있는 액세스를 요청 하는 pod를 거부 하는 pod 보안 정책을 만들었습니다. 정책을 사용할 수 있도록 허용 하려면 *역할* 또는 *ClusterRole*를 만듭니다. 그런 다음 *rolebinding* 또는 *clusterrolebinding*을 사용 하 여 이러한 역할 중 하나를 연결 합니다.

이 예에서는 이전 단계에서 만든 *psp-거부 권한* 정책을 *사용할* 수 있는 ClusterRole를 만듭니다. `psp-deny-privileged-clusterrole.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 ClusterRole를 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

이제 이전 단계에서 만든 ClusterRole를 사용 하는 ClusterRoleBinding을 만듭니다. `psp-deny-privileged-clusterrolebinding.yaml` 라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다.

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

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 ClusterRoleBinding을 만들고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> 이 문서의 첫 번째 단계에서는 pod 보안 정책 기능이 AKS 클러스터에서 사용 하도록 설정 되었습니다. 사용자 고유의 정책을 정의한 후에만 pod 보안 정책 기능을 사용 하도록 설정 하는 것이 좋습니다. Pod 보안 정책 기능을 사용 하도록 설정 하는 단계입니다. 하나 이상의 사용자 지정 정책이 정의 되었고 사용자 계정이 해당 정책과 연결 되어 있습니다. 이제 pod 보안 정책 기능을 안전 하 게 사용 하도록 설정 하 고 기본 정책으로 인해 발생 하는 문제를 최소화할 수 있습니다.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>권한 없는 pod 생성을 다시 테스트 합니다.

사용자 지정 pod 보안 정책이 적용 되 고 정책을 사용 하기 위한 사용자 계정에 대한 바인딩이 있으면 권한 없는 pod를 다시 만들려고 시도 합니다. [Kubectl apply][kubectl-apply] 명령을 사용 하 여 pod를 만들려면 동일한 `nginx-privileged.yaml` 매니페스트를 사용 합니다.

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pod가 성공적으로 예약 되었습니다. [Kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 상태를 확인 하는 경우 Pod가 *실행 중*입니다.

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

이 예제에서는 여러 사용자 또는 그룹에 대한 AKS 클러스터에 대한 액세스를 정의 하는 사용자 지정 pod 보안 정책을 만드는 방법을 보여 줍니다. 기본 AKS 정책은 pod가 실행할 수 있는 작업에 대한 긴밀 한 제어를 제공 하므로 사용자 지정 정책을 만들어 필요한 제한 사항을 올바르게 정의 합니다.

[Kubectl delete][kubectl-delete] 명령을 사용 하 여 NGINX 권한 없는 pod를 삭제 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>리소스 정리

Pod 보안 정책을 사용 하지 않도록 설정 하려면 [az aks update][az-aks-update] 명령을 다시 사용 합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹의 클러스터 이름 *myAKSCluster* 에서 pod 보안 정책을 사용 하지 않도록 설정 합니다.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

다음으로 ClusterRole 및 ClusterRoleBinding을 삭제 합니다.

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

[Kubectl delete][kubectl-delete] 명령을 사용 하 여 보안 정책을 삭제 하 고 yaml 매니페스트의 이름을 지정 합니다.

```console
kubectl delete -f psp-deny-privileged.yaml
```

마지막으로 *psp-aks* 네임 스페이스를 삭제 합니다.

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 권한 있는 액세스 사용을 방지 하기 위해 pod 보안 정책을 만드는 방법을 살펴보았습니다. 볼륨이 나 RunAs 사용자와 같은 정책에서 적용할 수 있는 많은 기능이 있습니다. 사용할 수 있는 옵션에 대한 자세한 내용은 [Kubernetes pod 보안 정책 참조 문서][kubernetes-policy-reference]를 참조 하세요.

Pod 네트워크 트래픽을 제한 하는 방법에 대한 자세한 내용은 [AKS에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보호][network-policies]를 참조 하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
