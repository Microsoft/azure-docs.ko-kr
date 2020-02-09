---
title: 컨테이너용 Azure Monitor 문제 해결 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너용 Azure Monitor의 문제를 해결하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 17a2817b320599b2aa2c331c354d316b9d864a32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75403374"
---
# <a name="troubleshooting-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor 문제 해결

컨테이너용 Azure Monitor로 Azure Kubernetes Service(AKS) 클러스터 모니터링을 구성할 때 데이터 컬렉션 또는 상태 보고를 방지하는 문제가 발생할 수 있습니다. 이 문서에서는 몇 가지 일반적인 문제 및 문제 해결 단계를 자세히 설명합니다.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>등록 또는 업데이트 작업 중에 권한 부여 오류가 발생 했습니다.
컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하거나 메트릭을 수집 하도록 클러스터를 업데이트 하는 동안 다음과 유사한 오류가 발생할 수 있습니다. *개체 id가 ' < 사용자의 objectId > ' 인 클라이언트 < 사용자 id > '은 (는) 범위에 대해 ' Microsoft 권한 부여/roleAssignments/write ' 작업을 수행할 수 있는 권한이 없습니다* .

온 보 딩 또는 업데이트 프로세스 중에 클러스터 리소스에서 **모니터링 메트릭 게시자** 역할 할당을 시도 합니다. 컨테이너에 대해 Azure Monitor를 사용 하도록 설정 하는 프로세스를 시작 하거나 메트릭을 수집 하도록 업데이트 하는 프로세스를 시작 하는 사용자는 AKS 클러스터 리소스 범위에 대 한 **Microsoft Authorization/roleAssignments/write** 권한에 액세스할 수 있어야 합니다. **소유자** 및 **사용자 액세스 관리자** 기본 제공 역할의 멤버만이 사용 권한에 대 한 액세스 권한을 부여 받습니다. 보안 정책에서 세부적인 수준 권한 할당을 요구 하는 경우 [사용자 지정 역할](../../role-based-access-control/custom-roles.md) 을 보고 필요한 사용자에 게 할당 하는 것이 좋습니다. 

다음 단계를 수행 하 여 Azure Portal에서이 역할을 수동으로 부여할 수도 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
2. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Kubernetes**을 입력 합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Azure Kubernetes**를 선택 합니다.
3. Kubernetes 클러스터 목록에서 목록 중 하나를 선택 합니다.
2. 왼쪽 메뉴에서 **액세스 제어 (IAM)** 를 클릭 합니다.
3. **+ 추가** 를 선택 하 여 역할 할당을 추가 하 고, **모니터링 메트릭 게시자** 역할을 선택 하 고, **선택** 상자에서 **AKS** 를 입력 하 여 구독에 정의 된 클러스터 서비스 사용자만 결과를 필터링 합니다. 해당 클러스터에 해당 하는 목록에서 하나를 선택 합니다.
4. **저장**을 선택하여 역할 할당을 완료합니다. 

## <a name="azure-monitor-for-containers-is-enabled-but-not-reporting-any-information"></a>컨테이너용 Azure Monitor가 사용되지만 정보를 보고하지 않습니다.
컨테이너에 대 한 Azure Monitor 성공적으로 사용 하도록 설정 및 구성 되었지만 상태 정보를 볼 수 없거나 로그 쿼리에서 결과가 반환 되지 않은 경우 다음 단계를 수행 하 여 문제를 진단 합니다. 

1. 다음 명령을 실행하여 에이전트의 상태를 확인합니다. 

    `kubectl get ds omsagent --namespace=kube-system`

    출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. 다음 명령을 사용하여 에이전트 버전 *06072018* 이상의 배포 상태를 확인합니다.

    `kubectl get deployment omsagent-rs -n=kube-system`

    출력은 다음 예제와 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Pod의 상태를 확인하여 `kubectl get pods --namespace=kube-system` 명령을 통해 실행 중인지 확인합니다.

    출력은 다음 예와 비슷하며 omsagent의 상태는 *Running*입니다.

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. 에이전트 로그를 확인합니다. 컨테이너화된 에이전트가 배포되면 OMI 명령을 실행하여 빠른 검사가 실행되고 에이전트 및 공급자의 버전이 표시됩니다. 

5. 에이전트가 성공적으로 배포 되었는지 확인 하려면 다음 명령을 실행 합니다. `kubectl logs omsagent-484hw --namespace=kube-system`

    상태는 다음 예제와 유사합니다.

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="error-messages"></a>오류 메시지

아래 표에서는 Azure Monitor를 컨테이너에 사용할 때 발생할 수 있는 알려진 오류를 요약하고 있습니다.

| 오류 메시지  | 실행력 |  
| ---- | --- |  
| 오류 메시지: `No data for selected filters`  | 새로 만든 클러스터에 대한 모니터링 데이터 흐름을 설정하는 데는 약간의 시간이 걸릴 수 있습니다. 클러스터에 대 한 데이터가 표시 될 때까지 10 ~ 15 분 이상 허용 합니다. |   
| 오류 메시지: `Error retrieving data` | Azure Kubernetes Service 클러스터가 상태 및 성능 모니터링을 설정 하는 동안 클러스터와 Azure Log Analytics 작업 영역 간에 연결이 설정 됩니다. Log Analytics 작업 영역은 클러스터에 대한 모든 모니터링 데이터를 저장하는 데 사용됩니다. Log Analytics 작업 영역이 삭제 된 경우이 오류가 발생할 수 있습니다. 작업 영역을 삭제 했는지 확인 하 고, 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터의 모니터링을 다시 사용 하도록 설정 하 고 기존을 지정 하거나 새 작업 영역을 만들어야 합니다. 다시 사용 하도록 설정 하려면 클러스터에 대 한 모니터링을 [사용 하지 않도록](container-insights-optout.md) 설정 하 고 컨테이너에 대 한 Azure Monitor를 다시 [사용 하도록 설정](container-insights-enable-new-cluster.md) 해야 합니다. |  
| 오류 메시지: `Error retrieving data`(az aks cli를 통해 컨테이너용 Azure Monitor를 추가한 후) | `az aks cli`를 사용 하 여 모니터링을 사용 하도록 설정 하면 컨테이너에 대 한 Azure Monitor 제대로 배포 되지 않을 수 있습니다. 솔루션이 배포 되었는지 여부를 확인 합니다. 이렇게 하려면 Log Analytics 작업 영역으로 이동하여 왼쪽 창에서 **솔루션**을 선택하여 해당 솔루션을 사용할 수 있는지 확인합니다. 이 문제를 해결하려면 [컨테이너용 Azure Monitor를 배포하는 방법](container-insights-onboard.md)의 지침에 따라 솔루션을 다시 배포해야 합니다. |  

문제 진단을 지원하기 위해 [여기](https://github.com/Microsoft/OMS-docker/tree/ci_feature_prod/Troubleshoot#troubleshooting-script)서 사용할 수 있는 문제 해결 스크립트를 제공했습니다.

## <a name="azure-monitor-for-containers-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>컨테이너 에이전트 ReplicaSet Pod에 대 한 Azure Monitor Azure Kubernetes 클러스터에 예약 되어 있지 않습니다.

컨테이너 에이전트 ReplicaSet Pod에 대 한 Azure Monitor에는 일정에 따라 작업자 (또는 에이전트) 노드의 다음 노드 선택기에 대 한 종속성이 있습니다.

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

작업자 노드에 노드 레이블이 연결 되지 않은 경우 agent ReplicaSet Pod가 예약 되지 않습니다. 레이블을 연결 하는 방법에 대 한 지침은 [Kubernetes assign label 선택기](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/) 를 참조 하세요.

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Azure가 아닌 클러스터에 있는 노드 및 컨테이너의 CPU 또는 메모리는 성능 차트에 표시 되지 않습니다.

컨테이너 에이전트 Pod Azure Monitor는 노드 에이전트의 cAdvisor 엔드포인트을 사용 하 여 성능 메트릭을 수집 합니다. 노드의 컨테이너 화 된 에이전트가 클러스터의 모든 노드에서 `cAdvisor port: 10255` 열 수 있도록 구성 되어 있는지 확인 하 여 성능 메트릭을 수집 합니다.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-azure-monitor-for-containers"></a>비 Azure Kubernetes 클러스터는 컨테이너에 대 한 Azure Monitor에 표시 되지 않습니다.

컨테이너에 대 한 Azure Monitor에서 비 Azure Kubernetes 클러스터를 보려면이 통찰력을 지 원하는 Log Analytics 작업 영역 및 Container Insights 솔루션 리소스 **ContainerInsights (*작업 영역*)** 에서 읽기 권한이 필요 합니다.

## <a name="next-steps"></a>다음 단계

모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
