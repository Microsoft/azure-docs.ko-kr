---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 클러스터 보안
description: AKS(Azure Kubernetes Services)에서 클러스터 보안 및 업그레이드를 관리하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 2377cc2e0824911217fbc2783fdf8e86fd7b9405
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549209"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 클러스터 보안 및 업그레이드 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 때 워크로드 및 데이터의 보안은 주요 고려 사항입니다. 논리적 격리를 사용하여 다중 테넌트 클러스터를 실행하는 경우에 특히, 리소스 및 워크로드에 대한 액세스를 보호해야 합니다. 공격 위험을 최소화하려면 최신 Kubernetes 및 노드 OS 보안 업데이트를 적용하는지도 확인해야 합니다.

이 문서에서는 AKS 클러스터의 보안을 유지하는 방법을 중점적으로 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Active Directory 및 역할 기반 액세스 제어를 사용하여 API 서버 액세스 보안 유지
> * 노드 리소스에 대한 컨테이너 액세스 보안 유지
> * AKS 클러스터를 최신 Kubernetes 버전으로 업그레이드
> * 노드를 최신 상태로 유지 하 고 보안 패치를 자동으로 적용

[컨테이너 이미지 관리][best-practices-container-image-management] 및 [pod 보안][best-practices-pod-security]에 대한 모범 사례를 읽을 수도 있습니다.

또한 [Security Center와의 Azure Kubernetes Services 통합][security-center-aks] 을 사용 하 여 위협을 감지 하 고 AKS 클러스터를 보호 하기 위한 권장 사항을 확인할 수 있습니다.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API 서버 및 클러스터 노드에 대한 액세스 보안 유지

**모범 사례 지침** - Kubernetes API 서버 액세스에 대한 보안을 유지하는 것은 클러스터를 보호하기 위해 수행할 수 있는 가장 중요한 작업 중 하나입니다. Kubernetes RBAC(역할 기반 액세스 제어)를 Azure Active Directory와 통합하여 API 서버에 대한 액세스를 제어합니다. 이러한 제어를 통해 AKS Azure 구독에 대한 액세스를 보호할 때와 동일한 방식으로 AKS를 보호할 수 있습니다.

Kubernetes API 서버는 클러스터 내의 작업 수행 요청에 대한 단일 연결점을 제공합니다. API 서버에 대한 액세스를 보호하고 감사하려면 액세스를 제한하고, 필요한 최소의 액세스 권한을 제공합니다. 이 방법은 Kubernetes에만 국한되지 않지만 AKS 클러스터가 다중 테넌트 사용을 위해 논리적으로 분리된 경우에 특히 유용합니다.

Azure AD(Active Directory)는 AKS 클러스터와 통합되는 기업환경에 맞게 준비된 ID 관리 솔루션을 제공합니다. Kubernetes는 ID 관리 솔루션을 제공하지 않으므로 API 서버에 대한 액세스를 제한하는 좀 더 세부적인 방법을 제공하는 것이 어려울 수 있습니다. AKS에서 Azure AD 통합 클러스터를 사용하는 경우 기존 사용자 및 그룹 계정을 사용하여 API 서버에서 사용자를 인증합니다.

![AKS 클러스터와의 Azure Active Directory 통합](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC 및 Azure AD 통합을 사용하여 API 서버를 보호하고, 범위가 지정된 리소스 세트에 필요한 최소 개수의 권한을 제공합니다(예: 단일 네임스페이스). Azure AD의 사용자 또는 그룹마다 다른 RBAC 역할에 부여할 수 있습니다. 이러한 세분화된 권한을 통해 API 서버에 대한 액세스를 제한하고, 수행되는 작업에 대한 명확한 감사 내역을 제공할 수 있습니다.

권장되는 모범 사례는 개별 ID가 아니라 그룹을 사용하여 파일 및 폴더에 대한 액세스 권한을 제공하고, Azure AD *그룹* 멤버 자격을 사용하여 개발 *사용자*가 아닌 여러 사용자를 RBAC 역할에 바인딩하는 것입니다. 사용자의 그룹 멤버 자격이 변경되면 AKS 클러스터에 대한 해당 액세스 권한도 그에 따라 변경됩니다. 역할에 직접 사용자를 바인딩하는 경우 직무가 변경될 수 있습니다. Azure AD 그룹 멤버 자격이 업데이트되지만 AKS 클러스터에 대한 권한에는 이러한 업데이트 결과가 반영되지 않습니다. 이 시나리오에서는 사용자에게 필요한 것보다 더 많은 권한이 부여됩니다.

Azure AD 통합 및 RBAC에 대한 자세한 내용은 [AKS의 인증 및 권한 부여에 대한 모범 사례][aks-best-practices-identity]를 참조 하세요.

## <a name="secure-container-access-to-resources"></a>리소스에 대한 컨테이너 액세스 보호

**모범 사례 지침** - 컨테이너에서 수행할 수 있는 작업에 대한 액세스를 제한합니다. 최소 개수의 권한을 제공하고, 루트/권한 에스컬레이션을 사용하지 않도록 합니다.

사용자 또는 그룹에 필요한 최소 개수의 권한을 부여하는 것과 같은 방식으로, 컨테이너도 필요한 작업 및 프로세스로 제한해야 합니다. 공격 위험을 최소화하기 위해서는 에스컬레이션된 권한 또는 루트 액세스를 요구하는 애플리케이션 및 컨테이너를 구성하지 마세요. 예를 들어 pod 매니페스트에서 `allowPrivilegeEscalation: false`를 설정합니다. 이러한 *pod 보안 컨텍스트*가 Kubernetes에 빌드되며, 추가 권한(예: 사용자 또는 그룹으로 실행)을 정의하거나 노출할 Linux 기능을 정의할 수 있습니다. 모범 사례는 [리소스에 대한 보안 pod 액세스][pod-security-contexts]를 참조 하세요.

컨테이너 작업을 보다 미세하게 제어하기 위해 *AppArmor* 및 *seccomp*와 같은 기본 제공 Linux 보안 기능을 사용할 수도 있습니다. 이러한 기능은 노드 수준에서 정의되며 pod 매니페스트를 통해 구현됩니다. Linux 노드 및 pod 기본 제공 Linux 보안 기능을 사용할 수 있습니다.

> [!NOTE]
> AKS 또는 다른 곳의 Kubernetes 환경은 악의적인 다중 테넌트 사용에 대해 완전히 안전하지 않습니다. *AppArmor*, *seccomp*, *Pod 보안 정책*과 같은 추가 보안 기능 또는 노드에 대해 보다 세분화된 RBAC(역할 기반 액세스 제어)를 사용하면 악용이 더 어려워집니다. 그러나 악의적인 다중 테넌트 워크로드를 실행할 때 진정한 보안을 위해서는 하이퍼바이저가 신뢰할 수 있는 유일한 보안 수준입니다. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다.

### <a name="app-armor"></a>App Armor

컨테이너에서 수행할 수 있는 작업을 제한 하기 위해 [AppArmor][k8s-apparmor] Linux 커널 보안 모듈을 사용할 수 있습니다. AppArmor는 기본 AKS 노드 OS의 일부로 사용할 수 있으며 기본적으로 사용하도록 설정됩니다. 읽기, 쓰기 또는 실행과 같은 작업이나 파일 시스템 탑재와 같은 시스템 기능을 제한하는 AppArmor 프로필을 만듭니다. 기본 AppArmor 프로필은 다양한 `/proc` 및 `/sys` 위치에 대한 액세스를 제한하며, 기본 노드에서 컨테이너를 논리적으로 격리하는 방법을 제공합니다. AppArmor는 Kubernetes pod 뿐 아니라 Linux에서 실행되는 모든 애플리케이션에 작동합니다.

![AKS 클러스터에서 컨테이너 작업을 제한하기 위해 사용되는 AppArmor 프로필](media/operator-best-practices-container-security/apparmor.png)

작동하는 AppArmor를 볼 수 있도록 하기 위해 다음 예제에서는 파일에 대한 쓰기를 방지하는 프로필을 만듭니다. AKS 노드에 대해 [SSH][aks-ssh] 를 수행 하 고, 파일을 만들고, 다음 콘텐츠를 붙여넣습니다 *.*

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor 프로필은 `apparmor_parser` 명령을 사용하여 추가됩니다. AppArmor에 프로필을 추가하고 이전 단계에서 만든 프로필의 이름을 지정합니다.

```console
sudo apparmor_parser deny-write.profile
```

프로필을 올바르게 구문 분석하고 AppArmor에 적용하면 반환되는 출력이 없습니다. 명령 프롬프트로 반환됩니다.

로컬 컴퓨터에서 *aks-apparmor.yaml*이라는 pod 매니페스트를 만들고 다음 콘텐츠를 붙여넣습니다. 이 매니페스트는 `container.apparmor.security.beta.kubernetes`에 대한 주석을 정의하고 이전 단계에서 만든 *deny-write* 프로필을 참조합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 샘플 pod를 배포 합니다.

```console
kubectl apply -f aks-apparmor.yaml
```

Pod를 배포한 상태에서 [kubectl exec][kubectl-exec] 명령을 사용 하 여 파일에 씁니다. 다음 예제 출력에서처럼 명령을 실행할 수 없습니다.

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

AppArmor에 대한 자세한 내용은 [AppArmor profiles In Kubernetes][k8s-apparmor]를 참조 하세요.

### <a name="secure-computing"></a>컴퓨팅 보안

AppArmor는 모든 Linux 응용 프로그램에서 작동 하지만 [seccomp (*sec*의 uting)][seccomp] 는 프로세스 수준에서 작동 합니다. Seccomp는 또한 Linux 커널 보안 모듈이며, 기본적으로 AKS 노드에 사용되는 Docker 런타임에서 지원됩니다. Seccomp를 사용하는 경우 컨테이너가 수행할 수 있는 프로세스 호출이 제한됩니다. 허용 또는 거부할 작업을 정의하는 필터를 만들고, pod YAML 매니페스트 내에서 주석을 사용하여 seccomp 필터에 연결합니다. 이러한 방식은 실행하는 데 필요한 최소 권한만 컨테이너에 부여하는 모범 사례에 적합합니다.

작동 중인 seccomp를 보려면 파일에 대한 권한을 변경하지 못하게 하는 필터를 만듭니다. AKS 노드에 대해 [SSH][aks-ssh] 를 수행 하 고 */var/lib/kubelet/seccomp/prevent-chmod* 라는 seccomp 필터를 만든 다음 다음 콘텐츠를 붙여 넣습니다.

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

로컬 컴퓨터에서 이제 *aks-seccomp.yaml*이라는 pod 매니페스트를 만든 후 다음 콘텐츠를 붙여넣습니다. 이 매니페스트는 `seccomp.security.alpha.kubernetes.io`에 대한 주석을 정의하고 이전 단계에서 만든 *prevent-chmod* 프로필을 참조합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 샘플 pod를 배포 합니다.

```console
kubectl apply -f ./aks-seccomp.yaml
```

[Kubectl get pod][kubectl-get] 명령을 사용 하 여 pod의 상태를 확인 합니다. Pod는 오류를 보고합니다. 다음 예제 출력에서처럼 `chmod` 명령이 seccomp 필터에 의해 실행되지 않도록 합니다.

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

사용 가능한 필터에 대한 자세한 내용은 [Docker의 Seccomp 보안 프로필][seccomp]을 참조 하세요.

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>최신 버전의 Kubernetes로 정기적으로 업데이트

**모범 사례 지침** - 새로운 기능 및 버그 수정 사항으로 최신 상태를 유지하려면 AKS 클러스터의 Kubernetes 버전으로 정기적으로 업데이트합니다.

Kubernetes는 보다 일반적인 인프라 플랫폼보다 더 빠른 속도로 새 기능을 릴리스합니다. Kubernetes 업데이트에는 새 기능, 버그 또는 보안 수정 사항이 포함됩니다. 일반적으로 새로운 기능은 *알파* 버전으로 이동된 후 *베타* 상태가 되며, 그 이후에 *안정화*되면서 일반 공급되고 프로덕션 용도로 권장됩니다. 이러한 릴리스 주기에 따라 정기적으로 호환성이 손상되는 변경을 겪거나 배포 및 템플릿을 조정하지 않고도 Kubernetes를 업데이트할 수 있습니다.

AKS에서 지원하는 Kubernetes의 4개 부 버전은 다음과 같습니다. 즉, 새로운 부 패치 버전이 릴리스되면 지원되는 가장 오래된 부 버전 및 패치 릴리스는 사용 중지됩니다. Kubernetes의 부 업데이트는 정기적으로 발생합니다. 거버넌스 프로세스를 확인하고 지원을 받지 못하는 일이 없도록 필요할 때 업그레이드해야 합니다. 자세한 내용은 [지원 되는 Kubernetes 버전 AKS][aks-supported-versions] 을 참조 하세요.

클러스터에 대해 사용할 수 있는 버전을 확인 하려면 다음 예제와 같이 [az aks][az-aks-get-upgrades] 명령을 사용 합니다.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

그런 다음 [az AKS upgrade][az-aks-upgrade] 명령을 사용 하 여 AKS 클러스터를 업그레이드할 수 있습니다. 업그레이드 프로세스는 한 번에 하나의 노드를 안전하게 차단했다가 드레이닝하고, 나머지 노드에서 pod를 예약한 후 최신 OS 및 Kubernetes 버전을 실행하는 새 노드를 배포합니다.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

AKS의 업그레이드에 대한 자세한 내용은 [AKS에서 지원 되는 Kubernetes 버전][aks-supported-versions] 및 [AKS 클러스터 업그레이드][aks-upgrade]를 참조 하세요.

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Kured를 사용 하 여 Linux 노드 업데이트 및 재부팅 프로세스

**모범 사례 지침** -AKS에서 각 Linux 노드에 보안 픽스를 자동으로 다운로드 하 여 설치 하지만, 필요한 경우 자동으로 다시 부팅 되지 않습니다. `kured`를 사용하여 보류 중인 다시 부팅을 감시하고, 노드를 안전하게 차단했다가 드레이닝하여 다시 부팅되도록 하고, 업데이트를 적용한 후 OS와 관련해서 최대한 안전하게 유지합니다. Windows Server 노드 (현재 AKS의 미리 보기 상태)의 경우 AKS 업그레이드 작업을 정기적으로 수행 하 여 pod를 안전 하 게 무시 하 고 업데이트 된 노드를 배포 합니다.

AKS의 각 야간, Linux 노드는 배포판 업데이트 채널을 통해 사용할 수 있는 보안 패치를 가져옵니다. 이 동작은 노드가 AKS 클러스터에 배포되면 자동으로 구성됩니다. 실행 중인 워크로드 중단 및 잠재적인 영향을 최소화하기 위해 보안 패치 또는 커널 업데이트에 필요한 경우에도 노드가 자동으로 다시 부팅되지 않습니다.

Weaveworks가 보류 중인 노드 재부팅을 감시 하는 오픈 소스 [kured (KUbernetes REboot Daemon)][kured] 프로젝트입니다. Linux 노드가 다시 부팅 해야 하는 업데이트를 적용 하는 경우 노드를 안전 하 게 통제 하 여 클러스터의 다른 노드에서 pod을 이동 하 고 예약할 수 있습니다. 노드는 일단 다시 부팅되면 클러스터에 다시 추가되고, Kubernetes는 해당 포드 예약을 다시 시작합니다. 중단을 최소화하기 위해 한 번에 하나의 노드만 `kured`에서 다시 부팅되도록 허용됩니다.

![kured를 사용하는 AKS 노드 다시 부팅 프로세스](media/operator-best-practices-cluster-security/node-reboot-process.png)

다시 부팅이 발생하는 경우를 보다 세부적으로 제어하려는 경우, `kured`를 Prometheus와 통합하여 다른 유지 관리 이벤트 또는 클러스터 문제가 진행 중인 경우에는 다시 부팅을 방지할 수 있습니다. 이러한 통합은 다른 문제를 해결하는 동안, 노드를 다시 부팅하여 추가적인 복잡성을 최소화합니다.

노드 재부팅을 처리 하는 방법에 대한 자세한 내용은 [AKS의 노드에 보안 및 커널 업데이트 적용][aks-kured]을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터의 보안을 유지하는 방법을 중점적으로 설명했습니다. 이러한 일부 영역을 구현하려면 다음 문서를 참조하세요.

* [AKS와 Azure Active Directory 통합][aks-aad]
* [AKS 클러스터를 최신 버전의 Kubernetes로 업그레이드][aks-upgrade]
* [Kured를 사용 하 여 보안 업데이트 및 노드 다시 부팅 처리][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
