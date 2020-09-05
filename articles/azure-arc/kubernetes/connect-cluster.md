---
title: Azure Arc가 지원되는 Kubernetes 클러스터 연결(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc를 사용하여 Azure Arc가 지원되는 Kubernetes 클러스터 연결
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.custom: references_regions
ms.openlocfilehash: eb3921d3ab2090b6bac54c9b68e9def3949ed4b5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723744"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure Arc가 지원되는 Kubernetes 클러스터 연결(미리 보기)

이 문서에서는 Azure의 AKS-engine, Azure Stack Hub의 AKS, GKE, EKS 및 VMware vSphere cluster와 같은 모든 CNCF (Cloud Native Kubernetes Foundation) 인증 된 클러스터를 Azure Arc에 연결 하는 과정을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

다음 요구 사항이 준비되었는지 확인합니다.

* Kubernetes 클러스터가 실행 되 고 있습니다. 기존 Kubernetes 클러스터가 없는 경우 다음 가이드 중 하나를 사용 하 여 테스트 클러스터를 만들 수 있습니다.
  * [Docker (kind)에서 Kubernetes](https://kind.sigs.k8s.io/) 를 사용 하 여 Kubernetes 클러스터 만들기
  * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) 또는 [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 용 Docker를 사용 하 여 Kubernetes 클러스터 만들기
* Arc enabled Kubernetes 에이전트를 배포 하기 위해 클러스터의 클러스터 및 클러스터 관리자 역할에 액세스 하려면 kubeconfig 파일이 필요 합니다.
* `az login` 및 `az connectedk8s connect` 명령에 사용되는 사용자 또는 서비스 주체에는 'Microsoft.Kubernetes/connectedclusters' 리소스 종류에 대한 '읽기' 및 '쓰기' 권한이 있어야 합니다. "Kubernetes Cluster-Azure Arc 온 보 딩" 역할에는 이러한 권한이 있으며 사용자 또는 서비스 주체의 역할 할당에 사용할 수 있습니다.
* Connectedk8s 확장을 사용 하 여 클러스터를 등록 하려면 투구 3이 필요 합니다. 이 요구 사항을 충족 하기 위해 [최신 버전의 투구 3을 설치](https://helm.sh/docs/intro/install) 합니다.
* Azure CLI 버전 2.3 +는 Azure Arc 사용 Kubernetes CLI 확장을 설치 하는 데 필요 합니다. [Azure CLI를 설치](/cli/azure/install-azure-cli?view=azure-cli-latest) 하거나 최신 버전으로 업데이트 하 여 Azure CLI 버전 2.3 +가 설치 되어 있는지 확인 합니다.
* Arc enabled Kubernetes CLI 확장을 설치 합니다.
  
  Kubernetes 클러스터를 Azure에 연결하는 데 도움이 되는 `connectedk8s` 확장을 설치합니다.
  
  ```console
  az extension add --name connectedk8s
  ```
  
  `k8sconfiguration` 확장 설치
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  이러한 확장을 나중에 업데이트 하려면 다음 명령을 실행 합니다.
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>지원되는 지역

* 미국 동부
* 서유럽

## <a name="network-requirements"></a>네트워크 요구 사항

Azure Arc 에이전트는 다음 프로토콜/포트/아웃바운드 URL이 작동하도록 요구합니다.

* 포트 443의 TCP --> `https://:443`
* 포트 9418의 TCP --> `git://:9418`

| 엔드포인트(DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | 에이전트가 Azure에 연결하고 클러스터를 등록하는 데 필요합니다.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | 상태를 푸시하고 구성 정보를 가져오는 에이전트의 데이터 평면 엔드포인트                                      |
| `https://docker.io`                                                                                            | 컨테이너 이미지를 끌어오는 데 필요합니다.                                                                                         |
| `https://github.com`, git://github.com                                                                         | 예제 GitOps 리포지토리는 GitHub에서 호스트됩니다. 구성 에이전트를 사용하려면 지정한 git 엔드포인트에 연결해야 합니다. |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager 토큰을 가져오고 업데이트하는 데 필요합니다.                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Azure Arc 에이전트의 컨테이너 이미지를 끌어오는 데 필요합니다.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  시스템 할당 관리 id 인증서를 가져오는 데 필요 합니다.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Azure Arc가 지원되는 Kubernetes의 두 공급자 등록

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

등록은 비동기 프로세스입니다. 등록에 약 10분이 소요될 수 있습니다. 다음 명령을 사용하여 등록 프로세스를 모니터링할 수 있습니다.

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 사용하여 클러스터에 대한 메타데이터를 저장합니다.

먼저, 연결된 클러스터 리소스를 유지할 리소스 그룹을 만듭니다.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**출력:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>클러스터 연결

다음으로 Kubernetes 클러스터를 Azure에 연결합니다. `az connectedk8s connect`의 워크플로는 다음과 같습니다.

1. `KUBECONFIG`, `~/.kube/config` 또는 `--kube-config`를 통해 Kubernetes 클러스터에 대한 연결 확인
1. Helm 3을 사용하여 Kubernetes용 Azure Arc 에이전트를 `azure-arc` 네임스페이스에 배포

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**출력:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>연결된 클러스터 확인

연결된 클러스터 나열

```console
az connectedk8s list -g AzureArcTest -o table
```

**출력:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

[Azure Portal](https://portal.azure.com/)에서이 리소스를 볼 수도 있습니다. 브라우저에서 포털을 연 후에는 명령 앞부분에서 사용 된 리소스 이름 및 리소스 그룹 이름 입력을 기반으로 리소스 그룹 및 Azure Arc enabled Kubernetes 리소스로 이동 `az connectedk8s connect` 합니다.

> [!NOTE]
> 클러스터를 등록 한 후 Azure Portal에서 Azure Arc enabled Kubernetes 리소스의 개요 페이지에 표시 되는 클러스터 메타 데이터 (클러스터 버전, 에이전트 버전, 노드 수)가 5 ~ 10 분 정도 소요 됩니다.

## <a name="connect-using-an-outbound-proxy-server"></a>아웃 바운드 프록시 서버를 사용 하 여 연결

클러스터가 아웃 바운드 프록시 서버 뒤에 있는 경우 Azure CLI 하 고 Arc enabled Kubernetes 에이전트는 아웃 바운드 프록시 서버를 통해 해당 요청을 라우팅해야 합니다. 다음 구성은이를 사용 하도록 설정 합니다.

1. `connectedk8s`다음 명령을 실행 하 여 컴퓨터에 설치 된 확장의 버전을 확인 합니다.

    ```bash
    az -v
    ```

    `connectedk8s`아웃 바운드 프록시를 사용 하 여 에이전트를 설정 하려면 >= 0.2.3 확장 버전이 필요 합니다. 컴퓨터에 < 0.2.3 버전이 있으면 [업데이트 단계](#before-you-begin) 를 따라 컴퓨터에서 최신 버전의 확장을 가져옵니다.

2. Azure CLI에 필요한 환경 변수를 설정 합니다.

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

3. 프록시 매개 변수를 지정 하 여 connect 명령을 실행 합니다.

    ```bash
    az connectedk8s connect -n <cluster-name> -g <resource-group> \
    --proxy-https https://<proxy-server-ip-address>:<port> \
    --proxy-http http://<proxy-server-ip-address>:<port> \
    --proxy-skip-range <excludedIP>,<excludedCIDR>
    ```

> [!NOTE]
> 1. --Proxy-skip 범위에서 excludedCIDR을 지정 하는 것이 에이전트에 대해 클러스터 간 통신이 끊어지지 않았는지 확인 하는 데 중요 합니다.
> 2. 위의 프록시 사양은 현재 Arc 에이전트에만 적용 되 고 sourceControlConfiguration에서 사용 되는 flux pod는 적용 되지 않습니다. Arc enabled Kubernetes 팀은이 기능에서 적극적으로 작업 중 이며 곧 사용할 수 있게 될 예정입니다.

## <a name="azure-arc-agents-for-kubernetes"></a>Kubernetes용 Azure Arc 에이전트

Azure Arc가 지원되는 Kubernetes는 `azure-arc` 네임스페이스에 소수의 연산자를 배포합니다. 이러한 배포 및 Pod는 다음에서 확인할 수 있습니다.

```console
kubectl -n azure-arc get deployments,pods
```

**출력:**

```console
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

Azure Arc가 지원되는 Kubernetes는 `azure-arc` 네임스페이스에 배포된 클러스터에서 실행되는 소수의 에이전트(운영자)로 구성됩니다.

* `deployment.apps/config-agent`: 클러스터에 적용된 원본 제어 구성 리소스에 대해 연결된 클러스터를 감시하고 호환성 상태를 업데이트합니다.
* `deployment.apps/controller-manager`: 연산자의 연산자이며 Azure Arc 구성 요소 간의 상호 작용을 조정합니다.
* `deployment.apps/metrics-agent`: 다른 Arc 에이전트의 메트릭을 수집하여 이러한 에이전트가 최적의 성능을 발휘하는지 확인합니다.
* `deployment.apps/cluster-metadata-operator`: 클러스터 메타 데이터를 수집 합니다. 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전
* `deployment.apps/resource-sync-agent`: 위에서 언급한 클러스터 메타데이터를 Azure에 동기화합니다.
* `deployment.apps/clusteridentityoperator`: Azure Arc enabled Kubernetes는 현재 시스템 할당 id를 지원 합니다. clusteridentityoperator는 다른 에이전트가 Azure와 통신 하기 위해 사용 하는 MSI (관리 서비스 id) 인증서를 유지 관리 합니다.
* `deployment.apps/flux-logs-agent`: 원본 제어 구성의 일부로 배포된 Flux 연산자에서 로그를 수집합니다.

## <a name="delete-a-connected-cluster"></a>연결된 클러스터 삭제

Azure CLI 또는 Azure Portal을 사용하여 `Microsoft.Kubernetes/connectedcluster` 리소스를 삭제할 수 있습니다.


* **Azure CLI를 사용 하 여 삭제**: 다음 Azure CLI 명령을 사용 하 여 Azure Arc enabled Kubernetes 리소스의 삭제를 시작할 수 있습니다.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  이 명령은 `Microsoft.Kubernetes/connectedCluster` Azure에서 리소스 및 연결 된 모든 리소스를 제거 합니다 `sourcecontrolconfiguration` . Azure CLI는 투구 제거를 사용 하 여 클러스터에서 실행 중인 에이전트도 제거 합니다.

* **Azure Portal 삭제**: Azure Portal azure Arc enabled Kubernetes 리소스를 삭제 하면 `Microsoft.Kubernetes/connectedcluster` azure에서 리소스 및 연결 된 모든 `sourcecontrolconfiguration` 리소스가 삭제 되지만 클러스터에서 실행 중인 에이전트는 삭제 되지 않습니다. 클러스터에서 실행 중인 에이전트를 삭제 하려면 다음 명령을 실행 합니다.

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>다음 단계

* [연결된 클러스터에서 GitOps 사용](./use-gitops-connected-cluster.md)
* [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)
