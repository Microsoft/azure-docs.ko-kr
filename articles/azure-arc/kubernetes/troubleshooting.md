---
title: 일반적인 Azure Arc 사용 Kubernetes 문제 해결(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arc 사용 Kubernetes 클러스터를 통한 일반적인 문제를 해결합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 404516778255409d56dd5c3a7d1fd96711cc981f
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723676"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Azure Arc 사용 Kubernetes 문제 해결(미리 보기)

이 문서에서는 연결, 권한 및 에이전트를 통한 몇 가지 일반적인 문제 해결 시나리오를 제공합니다.

## <a name="general-troubleshooting"></a>일반적인 문제 해결

### <a name="azure-cli-set-up"></a>Azure CLI 설치
az connectedk8s 또는 az k8sconfiguration CLI 명령을 사용하기 전에 az가 올바른 Azure 구독에 대해 작동하도록 설정되어 있는지 확인합니다.

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>azure arc 에이전트
Azure Arc가 지원되는 Kubernetes의 모든 에이전트는 `azure-arc` 네임스페이스에 pod로 배포됩니다. 정상 작업에서는 모든 Pod가 실행되고 상태 검사를 통과해야 합니다.

먼저, Azure Arc Helm 릴리스를 확인합니다.

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helm 릴리스가 없거나 누락된 경우 클러스터를 다시 온보딩해 보세요.

Helm 릴리스가 있고 `STATUS: deployed`인 경우 `kubectl`를 사용하여 에이전트의 상태를 확인합니다.

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

모든 Pod는 `STATUS`를 `Running`으로 표시해야 하며 `READY`은 `3/3` 또는 `2/2`여야 합니다. 로그를 가져오고 `Error` 또는 `CrashLoopBackOff`를 반환하는 Pod를 설명합니다. 이러한 pod가 상태를 유지 하는 경우 `Pending` 클러스터 노드에 리소스가 부족 하기 때문일 수 있습니다. [클러스터를 확장](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster) 하면 이러한 pod이 상태로 전환 됩니다 `Running` .

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Azure Arc에 Kubernetes 클러스터 연결

클러스터를 Azure에 연결하려면 Azure 구독에 대한 액세스와 대상 클러스터에 대한 `cluster-admin` 액세스가 필요합니다. 클러스터에 연결할 수 없거나 충분한 권한이 없는 경우 온보딩에 실패합니다.

### <a name="insufficient-cluster-permissions"></a>클러스터 권한 부족

제공된 kubeconfig 파일에 Azure Arc 에이전트를 설치할 수 있는 충분한 권한이 없는 경우 Azure CLI 명령은 Kubernetes API를 호출하는 동안 오류를 반환합니다.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

클러스터 소유자는 클러스터 관리자 권한이 있는 Kubernetes 사용자를 사용해야 합니다.

### <a name="installation-timeouts"></a>설치 시간 초과

Azure Arc 에이전트를 설치하려면 대상 클러스터에서 컨테이너 집합을 실행해야 합니다. 클러스터가 저속 인터넷 연결을 통해 실행되는 경우 컨테이너 이미지 풀은 Azure CLI 시간 제한보다 오래 걸릴 수 있습니다.

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

## <a name="configuration-management"></a>구성 관리

### <a name="general"></a>일반
원본 제어 구성과 관련된 문제를 해결하려면--debug 스위치를 사용하여 az 명령을 실행합니다.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>원본 제어 구성 만들기
Kubernetes/connectedCluster 리소스에 대한 참가자 역할은 필수이며 KubernetesConfiguration/sourceControlConfiguration 리소스를 만드는 데에는 충분합니다.

### <a name="configuration-remains-pending"></a>구성을 `Pending`으로 유지

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>모니터링

컨테이너에 대 한 Azure Monitor를 사용 하려면 DaemonSet가 특권 모드에서 실행 되어야 합니다. 모니터링할 정식 Charmed Kubernetes 클러스터를 성공적으로 설정 하려면 다음 명령을 실행 합니다.

```console
juju config kubernetes-worker allow-privileged=true
```