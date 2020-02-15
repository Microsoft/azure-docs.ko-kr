---
title: 일반적인 Azure Kubernetes Service 문제 해결
description: AKS(Azure Kubernetes Service)를 사용 할 때 발생하는 일반적인 문제를 해결하는 방법을 알아봅니다.
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 663a1dc597493c7b534b54eab7ccc4bed0ff0e11
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209225"
---
# <a name="aks-troubleshooting"></a>AKS 문제 해결

AKS(Azure Kubernetes Service) 클러스터를 만들거나 관리할 때 경우에 따라 문제가 발생할 수도 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>일반적으로 Kubernetes 문제 디버깅에 대한 정보는 어디에서 찾나요?

[Kubernetes 클러스터 문제 해결에 대한 공식 가이드](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)를 검색합니다.
pod, 노드, 클러스터 등의 문제 해결과 관련해서 Microsoft 엔지니어가 게시한 [문제 해결 가이드](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)도 있습니다.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>만들기 또는 업그레이드 동안 "할당량 초과" 오류가 발생합니다. 어떻게 해야 하나요? 

[코어를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)해야 합니다.

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>AKS의 노드당 최대 Pod 설정이란?

노드당 최대 Pod 설정은 Azure Portal에서 AKS 클러스터를 배포하는 경우 기본적으로 30입니다.
노드당 최대 Pod 설정은 Azure CLI에서 AKS 클러스터를 배포하는 경우 기본적으로 110입니다. Azure CLI의 최신 버전을 사용하도록 합니다. 이 기본 설정은 `–-max-pods` 명령에서 `az aks create` 플래그를 사용하여 변경할 수 있습니다.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>고급 네트워킹을 사용하여 AKS 클러스터를 배포하는 동안 insufficientSubnetSize 오류가 발생합니다. 어떻게 해야 하나요?

Azure CNI (고급 네트워킹)를 사용 하는 경우 AKS는 구성 된 노드당 "최대 pod"를 기반으로 IP 주소를 할당 합니다. 노드당 구성 된 max pod을 기반으로 하는 서브넷 크기는 노드 수와 노드당 최대 pod 설정의 곱 보다 커야 합니다. 다음 수식은이에 대해 간략하게 설명 합니다.

서브넷 크기 > 클러스터의 노드 수 (향후 크기 조정 요구 사항을 고려 하 여) * 노드 집합 당 최대 pod.

자세한 내용은 [클러스터에 대한 IP 주소 지정 계획](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)을 참조하세요.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>내 Pod가 CrashLoopBackOff 모드에서 중단됩니다. 어떻게 해야 하나요?

Pod가 해당 모드에서 중단되는 이유는 다양할 수 있습니다. 다음을 확인할 수 있습니다.

* `kubectl describe pod <pod-name>`를 사용하여 pod 자체
* `kubectl logs <pod-name>`을 사용하여 로그

Pod 문제를 해결하는 방법에 대한 자세한 내용은 [애플리케이션 디버그](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)를 참조하세요.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>기존 클러스터에서 RBAC를 사용하도록 설정하려고 합니다. 어떻게 하면 되나요?

현재는 기존 클러스터에서 RBAC(역할 기반 액세스 제어)를 사용하도록 설정할 수 없습니다. 새 클러스터를 명시적으로 만들어야 합니다. CLI를 사용하는 경우 RBAC가 기본적으로 사용됩니다. AKS 포털을 사용하는 경우 만들기 워크플로에서 RBAC를 사용하도록 설정하는 토글 단추를 사용할 수 있습니다.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>기본 설정의 Azure CLI 또는 Azure Portal에서 RBAC가 설정된 클러스터를 만들었으나 kubernetes 대시보드에서 경고가 많이 표시됩니다. 이전에는 대시보드가 경고없이 잘 작동되었습니다. 어떻게 해야 하나요?

대시보드에 경고가 표시되는 이유는 클러스터에 RBAC가 설정되어 있으나 해당 클러스터에 대한 액세스는 기본적으로 사용하지 않도록 설정되어 있기 때문입니다. 일반적으로 클러스터의 모든 사용자에게 기본적으로 대시보드를 노출하면 보안 위협이 초래될 수 있으므로 이렇게 하는 것이 적절합니다. 그래도 대시보드를 사용하도록 설정하려면 [이 블로그 게시물](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)의 단계를 따르세요.

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>대시보드에 연결할 수 없습니다. 어떻게 해야 하나요?

클러스터 외부의 서비스에 액세스하는 가장 쉬운 방법은 `kubectl proxy`를 실행하는 것입니다. 그러면 localhost 포트 8001로 전송된 요청이 Kubernetes API 서버로 프록시됩니다. 여기에서 API 서버는 서비스로 프록시할 수 있습니다. `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`

Kubernetes 대시보드가 표시되지 않으면 `kube-proxy` pod가 `kube-system` 네임스페이스에서 실행되고 있는지를 확인합니다. 실행 중 상태가 아니면 pod를 삭제합니다. 그러면 다시 시작됩니다.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>kubectl logs를 사용하여 로그를 가져올 수 없고 API 서버에 연결할 수 없습니다. "서버에서 오류가 발생 했습니다. 오류를 전화 접속 하는 중입니다." 라는 오류가 발생 합니다. 어떻게 해야 하나요?

기본 네트워크 보안 그룹이 수정 되지 않고 API 서버에 연결 하기 위해 포트 22 및 9000이 모두 열려 있는지 확인 합니다. `tunnelfront` pod가 `kubectl get pods --namespace kube-system` 명령을 사용 하 여 *kube* 네임 스페이스에서 실행 중인지 확인 합니다. 실행되지 않으면 pod를 강제로 삭제합니다. 그러면 다시 시작됩니다.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>업그레이드 하거나 크기를 조정 하는 중입니다. "메시지: ' imageReference ' 속성이 변경 될 수 없습니다." 오류가 발생 합니다. 이 문제를 어떻게 해결하나요?

AKS 클러스터 내의 에이전트 노드에서 태그를 수정했기 때문에 이 오류가 발생하는 것일 수 있습니다. MC_* 리소스 그룹에서 태그 및 리소스의 기타 속성을 수정 및 삭제하면 예기치 않은 결과가 발생할 수 있습니다. AKS 클러스터의 MC_* 그룹 아래에서 리소스를 수정하면 SLO(서비스 수준 목표)가 중단됩니다.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>내 클러스터가 실패 상태에 있는 오류를 수신 하 고 있으며, 문제가 해결 될 때까지 업그레이드 또는 확장이 작동 하지 않습니다.

*이 문제 해결 지원은 https://aka.ms/aks-cluster-failed에서 전송 됩니다.*

이 오류는 여러 가지 이유로 클러스터가 실패 상태를 입력 하는 경우에 발생 합니다. 이전에 실패 한 작업을 다시 시도 하기 전에 다음 단계를 수행 하 여 클러스터 실패 상태를 확인 합니다.

1. 클러스터가 `failed` 상태가 될 때까지 `upgrade` 및 `scale` 작업이 성공 하지 않습니다. 일반적인 근본 문제 및 해결 방법은 다음과 같습니다.
    * **계산 (CRP) 할당량이 부족**한 크기 조정 문제를 해결 하려면 먼저 할당량 내에서 안정적인 목표 상태로 클러스터를 다시 확장 합니다. 그런 다음 초기 할당량 한도를 초과 하 여 다시 확장 하기 전에 [계산 할당량 증가를 요청 하려면 다음 단계를](../azure-portal/supportability/resource-manager-core-quotas-request.md) 수행 합니다.
    * 고급 네트워킹으로 클러스터 크기를 조정 하 고 **서브넷 (네트워킹) 리소스가 부족**합니다. 문제를 해결 하려면 먼저 할당량 내에서 안정적인 목표 상태로 클러스터를 다시 확장 합니다. 그런 후에 다음 단계에 따라 초기 할당량 한도를 초과 하 여 다시 확장 하기 전에 [리소스 할당량 증가를 요청](../azure-resource-manager/templates/error-resource-quota.md#solution) 합니다.
2. 업그레이드 실패의 근본 원인이 해결 되 면 클러스터가 성공 상태 여야 합니다. 성공 상태가 확인 되 면 원래 작업을 다시 시도 합니다.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>클러스터가 현재 업그레이드 중이거나 업그레이드에 실패 한 상태를 업그레이드 하거나 크기를 조정 하려고 할 때 오류가 발생 합니다.

*이 문제 해결 지원은 https://aka.ms/aks-pending-upgrade에서 전송 됩니다.*

단일 노드 풀 또는 [여러 노드 풀](use-multiple-node-pools.md) 을 포함 하는 클러스터에서 클러스터에 대 한 업그레이드 및 크기 조정 작업은 함께 사용할 수 없습니다. 클러스터 또는 노드 풀을 동시에 업그레이드 하 고 확장할 수 없습니다. 대신, 동일한 리소스에 대 한 다음 요청 전에 대상 리소스에서 각 작업 유형이 완료 되어야 합니다. 따라서 활성 업그레이드 또는 크기 조정 작업이 발생 하거나 시도한 후에 실패 하는 경우 작업이 제한 됩니다. 

문제를 진단 하는 데 도움이 되도록 `az aks show -g myResourceGroup -n myAKSCluster -o table`를 실행 하 여 클러스터에서 자세한 상태를 검색 합니다. 결과에 따라:

* 클러스터가 적극적으로 업그레이드 되는 경우 작업이 종료 될 때까지 기다립니다. 성공 하면 이전에 실패 한 작업을 다시 시도 합니다.
* 클러스터가 업그레이드에 실패 한 경우 이전 섹션에 설명 된 단계를 수행 합니다.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>클러스터를 새 테 넌 트에 대 한 다른 구독 또는 구독으로 이동할 수 있나요?

AKS 클러스터를 다른 구독으로 이동 했거나 클러스터를 새 테 넌 트로 소유 하 고 있는 경우 역할 할당 및 서비스 사용자 권한이 손실 되어 클러스터에서 기능을 잃게 됩니다. AKS는이 제약 조건으로 인해 **구독 또는 테 넌 트 간 클러스터 이동을 지원 하지** 않습니다.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>가상 컴퓨터 크기 집합을 필요로 하는 기능을 사용 하려는 동안 오류가 발생 했습니다.

*이 문제 해결 지원은 aka.ms/aks-vmss-enablement에서 전송 됩니다.*

AKS 클러스터가 다음 예제와 같이 가상 머신 확장 집합에 없음을 나타내는 오류가 발생할 수 있습니다.

**AgentPool ' agentpool '에서 자동 크기 조정을 사용 하도록 설정 했지만 Virtual Machine Scale Sets 되지 않았습니다.**

클러스터 autoscaler 또는 여러 노드 풀과 같은 기능을 사용 하려면 가상 머신 확장 집합을 사용 하는 AKS 클러스터를 만들어야 합니다. 가상 머신 확장 집합에 종속 된 기능을 사용 하려고 할 때 가상 머신 확장 집합 AKS 클러스터가 아닌 일반 사용자를 대상으로 하는 경우 오류가 반환 됩니다.

적절 한 doc의 단계를 *시작 하기 전에* 다음 단계에 따라 AKS 클러스터를 올바르게 만듭니다.

* [클러스터 autoscaler 사용](cluster-autoscaler.md)
* [여러 노드 풀 만들기 및 사용](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>AKS 리소스 및 매개 변수에 적용 되는 명명 제한은 무엇 인가요?

*이 문제 해결 지원은 aka.ms/aks-naming-rules에서 전송 됩니다.*

명명 제한은 Azure 플랫폼과 AKS 모두에 의해 구현 됩니다. 리소스 이름 또는 매개 변수가 이러한 제한 중 하나를 중단 하는 경우 다른 입력을 제공 하 라는 오류가 반환 됩니다. 다음과 같은 일반적인 명명 지침이 적용 됩니다.

* 클러스터 이름은 1-63 자 여야 합니다. 허용 되는 문자는 문자, 숫자, 대시 및 밑줄만 사용할 수 있습니다. 첫 번째 및 마지막 문자는 문자 또는 숫자 여야 합니다.
* AKS *MC_* 리소스 그룹 이름은 리소스 그룹 이름과 리소스 이름을 결합 합니다. `MC_resourceGroupName_resourceName_AzureRegion`의 자동 생성 구문은 80 자이 하 여야 합니다. 필요한 경우 리소스 그룹 이름 또는 AKS 클러스터 이름 길이를 줄이십시오.
* *DnsPrefix* 는 영숫자 값으로 시작 하 고 끝나야 하며 1-54 자 사이 여야 합니다. 유효한 문자에는 영숫자 값과 하이픈 (-)이 포함 됩니다. *DnsPrefix* 에는 마침표 (.)와 같은 특수 문자를 포함할 수 없습니다.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>클러스터를 만들거나, 업데이트 하거나, 크기를 삭제 하거나, 업그레이드 하는 동안 오류가 발생 하는 경우 다른 작업이 진행 중 이므로 해당 작업이 허용 되지 않습니다.

*이 문제 해결 지원은 aka.ms/aks-pending-operation에서 전송 됩니다.*

이전 작업이 아직 진행 중인 경우 클러스터 작업이 제한 됩니다. 클러스터의 자세한 상태를 검색 하려면 `az aks show -g myResourceGroup -n myAKSCluster -o table` 명령을 사용 합니다. 필요에 따라 고유한 리소스 그룹 및 AKS 클러스터 이름을 사용 합니다.

클러스터 상태의 출력을 기반으로 합니다.

* 클러스터가 *성공* 또는 *실패*이외의 프로 비전 상태에 있으면 작업 (*업그레이드/업데이트/만들기/크기 조정/삭제/마이그레이션*)이 종료 될 때까지 기다립니다. 이전 작업이 완료 되 면 최신 클러스터 작업을 다시 시도 합니다.

* 클러스터의 업그레이드가 실패 한 경우에는 [내 클러스터가 실패 한 상태에 있음을 나타내는 단계를 수행 하 고, 업그레이드가 완료 될 때까지 업그레이드 또는 크기 조정이 작동 하지](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)않습니다.

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>기존 항목을 전달 하지 않고 새 클러스터를 만들려고 할 때 서비스 주체를 찾을 수 없다는 오류를 수신 합니다.

AKS 클러스터를 만들 때 사용자를 대신 하 여 리소스를 만들어야 하는 서비스 주체가 필요 합니다. AKS은 클러스터를 만들 때 새 서비스 주체를 만들 수 있는 기능을 제공 하지만, 클러스터를 만드는 데 성공 하기 위해 적절 한 시간에 새 서비스 주체를 완전히 전파 하는 Azure Active Directory 필요 합니다. 이 전파 시간이 너무 오래 걸리면 클러스터에서 사용할 수 있는 서비스 주체를 찾을 수 없기 때문에를 만들기 위해 유효성 검사가 실패 합니다. 

이에 대 한 다음 해결 방법을 사용 합니다.
1. 지역에 걸쳐 이미 전파 된 기존 서비스 사용자를 사용 하 여 클러스터 만들기 시간에 AKS에 전달 합니다.
2. 자동화 스크립트를 사용 하는 경우 서비스 주체 만들기와 AKS 클러스터 만들기 사이의 시간 지연을 추가 합니다.
3. Azure Portal 사용 하는 경우 만드는 동안 클러스터 설정으로 돌아가서 몇 분 후에 유효성 검사 페이지를 다시 시도 합니다.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>송신 트래픽을 제한 한 후 오류를 수신 합니다.

AKS 클러스터에서 송신 트래픽을 제한 하는 경우 [필수 및 선택적으로 권장](limit-egress-traffic.md) 되는 아웃 바운드 포트/네트워크 규칙 및 AKS에 대 한 FQDN/응용 프로그램 규칙이 필요 합니다. 설정이 이러한 규칙과 충돌 하는 경우 특정 `kubectl` 명령을 실행 하지 못할 수 있습니다. AKS 클러스터를 만들 때 오류가 표시 될 수도 있습니다.

설정이 필수 또는 옵션인 권장 아웃 바운드 포트/네트워크 규칙 및 FQDN/응용 프로그램 규칙과 충돌 하지 않는지 확인 합니다.

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure Storage 및 AKS 문제 해결

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Azure disk에 대해 Kubernetes의 권장 되는 안정적인 버전은 무엇 인가요? 

| Kubernetes 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.9 이상 |
| 1.13 | 1.13.6 이상 |
| 1.14 | 1.14.2 이상 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>소 버린 클라우드에서 Azure 디스크가 지원 되는 Kubernetes 버전은 무엇 인가요?

| Kubernetes 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.0 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 | 1.14.0 이상 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 디스크에 대 한 WaitForAttach 실패: 구문 분석 "/dev/disk/azure/scsi1/lun1": 구문이 잘못 되었습니다.

Kubernetes 버전 1.10에서 MountVolume는 Azure 디스크가 다시 탑재 되어 실패할 수 있습니다.

Linux에서 잘못 된 DevicePath format 오류가 표시 될 수 있습니다. 다음은 그 예입니다.

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Windows에서 잘못 된 DevicePath (LUN) 번호 오류가 표시 될 수 있습니다. 다음은 그 예입니다.

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.2 이상 |
| 1.11 | 1.11.0 이상 |
| 1.12 이상 | 해당 없음 |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Azure 디스크에 대해 mountOptions에서 uid 및 gid를 설정 하는 동안 오류가 발생 했습니다.

Azure 디스크는 기본적으로 ext4, xfs 파일 시스템을 사용 하 고 uid = x, gid = x와 같은 mountOptions는 탑재 시 설정할 수 없습니다. 예를 들어 mountOptions uid = 999, gid = 999를 설정 하려는 경우 다음과 같은 오류가 표시 됩니다.

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

다음 중 하나를 수행 하 여 문제를 완화할 수 있습니다.

* FsGroup의 runAsUser 및 gid에서 uid를 설정 하 여 [pod의 보안 컨텍스트를 구성](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) 합니다. 예를 들어 다음 설정은 pod run을 root로 설정 하 고 모든 파일에 액세스할 수 있도록 합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Gid와 uid는 기본적으로 root 또는 0으로 탑재 됩니다. Gid 또는 uid가 루트가 아닌 것으로 설정 된 경우 (예: 1000) Kubernetes는 `chown`을 사용 하 여 해당 디스크 아래의 모든 디렉터리와 파일을 변경 합니다. 이 작업을 수행 하는 데 시간이 오래 걸릴 수 있으며 디스크를 매우 느리게 탑재할 수 있습니다.

* InitContainers의 `chown`를 사용 하 여 gid 및 uid를 설정 합니다. 다음은 그 예입니다.

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Pod에서 사용 중인 Azure Disk PersistentVolumeClaim를 삭제 하는 동안 오류 발생

Pod에서 사용 중인 Azure Disk PersistentVolumeClaim를 삭제 하려고 하면 오류가 표시 될 수 있습니다. 다음은 그 예입니다.

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Kubernetes 버전 1.10 이상에서는이 오류를 방지 하기 위해 기본적으로 PersistentVolumeClaim 보호 기능이 사용 되도록 설정 되어 있습니다. 이 문제에 대 한 수정 사항이 없는 Kubernetes 버전을 사용 하는 경우 PersistentVolumeClaim를 삭제 하기 전에 PersistentVolumeClaim를 사용 하 여 pod를 삭제 하 여이 문제를 완화할 수 있습니다.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>노드에 디스크를 연결할 때 "디스크의 Lun을 찾을 수 없음" 오류

노드에 디스크를 연결할 때 다음 오류가 표시 될 수 있습니다.

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.10 이상 |
| 1.11 | 1.11.5 이상 |
| 1.12 | 1.12.3 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대 한 해결 방법이 없는 Kubernetes 버전을 사용 하는 경우 몇 분 동안 기다린 후 다시 시도 하 여 문제를 완화할 수 있습니다.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>여러 연결/분리 작업을 수행 하는 동안 Azure 디스크 연결/분리 실패, 탑재 문제 또는 i/o 오류

Kubernetes 버전 1.9.2부터 여러 연결/분리 작업을 병렬로 실행 하는 경우 더티 VM 캐시로 인해 다음과 같은 디스크 문제가 발생할 수 있습니다.

* 디스크 연결/분리 오류
* 디스크 i/o 오류
* VM에서 예기치 않은 디스크 분리
* 존재 하지 않는 디스크를 연결 했기 때문에 VM이 실패 상태로 실행 됩니다.

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.10 | 1.10.12 이상 |
| 1.11 | 1.11.6 이상 |
| 1.12 | 1.12.4 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대 한 해결 방법이 없는 Kubernetes 버전을 사용 하는 경우 다음을 시도 하 여 문제를 완화할 수 있습니다.

* 디스크가 오랜 시간 동안 분리 될 때까지 대기 중인 경우 디스크를 수동으로 분리 해 보세요.

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>무기한 분리 대기 중인 Azure 디스크

첫 번째 시도에서 Azure Disk detach 작업이 실패 하는 경우에는 분리 작업을 다시 시도 하지 않고 원래 노드 VM에 연결 된 상태로 유지 됩니다. 이 오류는 한 노드에서 다른 노드로 디스크를 이동 하는 경우에 발생할 수 있습니다. 다음은 그 예입니다.

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.11 | 1.11.9 이상 |
| 1.12 | 1.12.7 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 이상 | 해당 없음 |

이 문제에 대 한 해결 방법이 없는 Kubernetes 버전을 사용 하는 경우 디스크를 수동으로 분리 하 여 문제를 완화할 수 있습니다.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>잠재적인 경합 상태 문제 및 잘못 된 데이터 디스크 목록에 대 한 Azure 디스크 분리 오류

Azure 디스크를 분리 하지 못하면 지 수 백오프를 사용 하 여 디스크를 분리 하는 데 최대 6 회까지 다시 시도 합니다. 또한 약 3 분 동안 데이터 디스크 목록에 대 한 노드 수준 잠금을 유지 합니다. 수동 연결 또는 분리 작업과 같이 해당 기간 동안 디스크 목록이 수동으로 업데이트 되는 경우 노드 수준 잠금이 보유 하 고 있는 디스크 목록이 더 이상 사용 되지 않고 노드 VM에서 불안정 해질 수 있습니다.

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.9 이상 |
| 1.13 | 1.13.6 이상 |
| 1.14 | 1.14.2 이상 |
| 1.15 이상 | 해당 없음 |

이 문제에 대 한 해결 방법이 없는 Kubernetes의 버전을 사용 하는 경우 노드 VM에 사용 되지 않는 디스크 목록이 있으면 VM의 모든 비 기존 디스크를 단일 대량 작업으로 분리 하 여 문제를 완화할 수 있습니다. **존재 하지 않는 디스크를 개별적으로 분리 하면 실패할 수 있습니다.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>많은 수의 Azure 디스크가 있으면 연결/분리 속도가 느려집니다.

노드 VM에 연결 된 Azure 디스크 수가 10 개를 초과 하면 연결 및 분리 작업이 느릴 수 있습니다. 이 문제는 알려진 문제 이며 지금은 해결 방법이 없습니다.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>실패 상태에서 잠재적 노드 VM으로 이어지는 Azure 디스크 분리 오류

일부 edge의 경우 Azure 디스크 분리가 부분적으로 실패 하 고 노드 VM을 실패 상태로 유지할 수 있습니다.

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.10 이상 |
| 1.13 | 1.13.8 이상 |
| 1.14 | 1.14.4 이상 |
| 1.15 이상 | 해당 없음 |

이 문제에 대 한 해결 방법이 없는 Kubernetes의 버전을 사용 중이 고 노드 VM이 실패 한 상태 이면 아래 중 하나를 사용 하 여 VM 상태를 수동으로 업데이트 하 여 문제를 완화할 수 있습니다.

* 가용성 집합 기반 클러스터의 경우:
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* VMSS 기반 클러스터의 경우:
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure Files 및 AKS 문제 해결

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure files에 대해 Kubernetes의 권장 되는 안정적인 버전은 무엇 인가요?
 
| Kubernetes 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.6 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 | 1.14.0 이상 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>소 버린 클라우드에서 Azure Files 지원 되는 Kubernetes의 버전은 무엇 인가요?

| Kubernetes 버전 | 권장 버전 |
| -- | :--: |
| 1.12 | 1.12.0 이상 |
| 1.13 | 1.13.0 이상 |
| 1.14 | 1.14.0 이상 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Azure Files를 사용 하는 경우 기본 mountOptions?

권장 설정:

| Kubernetes 버전 | fileMode 및 Dimode 값|
| -- | :--: |
| 1.12.0-1.12.1 | 0755 |
| 1.12.2 이상 | 0777 |

Kuberetes 버전이 1.8.5 이상인 클러스터를 사용 하 고 저장소 클래스를 사용 하 여 영구적 볼륨을 동적으로 만드는 경우 저장소 클래스 개체에 탑재 옵션을 지정할 수 있습니다. 다음 예제에서는 *0777*을 설정합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

몇 가지 추가 유용한 *mountOptions* 설정:

* *mfsymlinks* 는 cifs (Azure Files mount)에서 기호화 된 링크를 지원 합니다.
* *nobrl* 에서는 서버에 바이트 범위 잠금 요청을 보낼 수 없습니다. 이 설정은 cifs 스타일 필수 바이트 범위 잠금으로 인해 중단 되는 특정 응용 프로그램에 필요 합니다. 대부분의 cifs 서버는 아직 요청 하는 advise 바이트 범위 잠금을 지원 하지 않습니다. *Nobrl*을 사용 하지 않는 경우 cifs 스타일 필수 바이트 범위 잠금으로 인해 발생 하는 응용 프로그램에서 다음과 유사한 오류 메시지가 나타날 수 있습니다.
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Azure Files를 사용 하는 경우 "사용 권한을 변경할 수 없습니다" 오류

Azure Files 플러그 인에서 PostgreSQL를 실행 하면 다음과 유사한 오류가 표시 될 수 있습니다.

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

이 오류는 Azure Files 플러그 인에서 cifs/SMB 프로토콜을 사용 하 여 발생 합니다. Cifs/SMB 프로토콜을 사용 하는 경우 탑재 후에 파일 및 디렉터리 사용 권한을 변경할 수 없습니다.

이 문제를 해결 하려면 Azure Disk plugin과 함께 하위 *경로* 를 사용 합니다. 

> [!NOTE] 
> Ext3/4 디스크 유형의 경우 디스크를 포맷 한 후 손실 + 찾기 디렉터리가 있습니다.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>많은 작은 파일을 처리할 때 Azure 디스크에 비해 대기 시간이 긴 Azure Files

몇 가지 작은 파일을 처리 하는 등의 경우에는 Azure Disk와 비교할 때 Azure Files를 사용할 때 대기 시간이 길어질 수 있습니다.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>저장소 계정에서 "선택한 네트워크에서 액세스할 수 있도록 허용" 설정을 사용 하도록 설정 하는 동안 오류 발생

AKS에서 동적 프로 비전에 사용 되는 저장소 계정에 *선택한 네트워크에서 액세스 허용* 을 사용 하도록 설정 하면 AKS에서 파일 공유를 만들 때 오류가 발생 합니다.

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

이 오류는 *선택한 네트워크에서 액세스 허용*을 설정할 때 선택한 네트워크에 Kubernetes *persistentvolume* 가 있지 않기 때문에 발생 합니다.

[Azure Files로 정적 프로 비전](azure-files-volume.md)을 사용 하 여 문제를 완화할 수 있습니다.

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Windows pod에서 Azure Files 다시 탑재 실패

Azure Files 탑재 된 Windows pod가 삭제 된 다음 동일한 노드에서 다시 만들어지도록 예약 된 경우 탑재는 실패 합니다. 이 오류는 Azure Files 마운트가 이미 노드에 탑재 된 이후에 `New-SmbGlobalMapping` 명령이 실패 했기 때문입니다.

예를 들어 다음과 유사한 오류가 표시 될 수 있습니다.

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

다음 버전의 Kubernetes에서는이 문제가 해결 되었습니다.

| Kubernetes 버전 | 고정 버전 |
| -- | :--: |
| 1.12 | 1.12.6 이상 |
| 1.13 | 1.13.4 이상 |
| 1.14 이상 | 해당 없음 |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>저장소 계정 키가 변경 되어 Azure Files 탑재 실패

저장소 계정 키가 변경 된 경우 탑재 실패 Azure Files 표시 될 수 있습니다.

Base64 인코딩 저장소 계정 키를 사용 하 여 Azure 파일 암호에서 수동으로 *azurestorageaccountkey* 필드를 수동으로 업데이트 하 여 문제를 완화할 수 있습니다.

Base64에서 저장소 계정 키를 인코딩하려면 `base64`를 사용할 수 있습니다. 다음은 그 예입니다.

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Azure 비밀 파일을 업데이트 하려면 `kubectl edit secret`을 사용 합니다. 다음은 그 예입니다.

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

몇 분 후에 에이전트 노드에서 업데이트 된 저장소 키로 azure 파일 탑재를 다시 시도 합니다.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>노드 그룹 크기를 수정 하지 못했습니다. 오류가 발생 하 여 클러스터 autoscaler 크기를 조정 하지 못함

클러스터 autoscaler 확장/축소 되지 않고 [클러스터 autoscaler 로그][view-master-logs]에 아래와 같은 오류가 표시 됩니다.

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

이 오류는 클러스터 autoscaler가 실제로 클러스터에 있는 값과 다른 값으로 끝나는 업스트림 클러스터 autoscaler 경합 상태 때문에 발생 합니다. 이 상태를 확인 하려면 [클러스터 autoscaler][cluster-autoscaler]를 사용 하지 않도록 설정 하 고 다시 사용 하도록 설정 하면 됩니다.

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
