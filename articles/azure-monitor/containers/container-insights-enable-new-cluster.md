---
title: 새 Azure Kubernetes Service(AKS) 클러스터 모니터링 | Microsoft Docs
description: Container 인사이트 구독을 사용하여 새 Azure Kubernetes Service(AKS) 클러스터에 대해 모니터링을 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform
ms.openlocfilehash: fd00c59b277bff05abe0c8bb4d95ca56364a8b10
ms.sourcegitcommit: 2e123f00b9bbfebe1a3f6e42196f328b50233fc5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2021
ms.locfileid: "108071508"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>새 Azure Kubernetes Service(AKS) 클러스터의 모니터링 사용

이 문서에서는 구독에서 배포하기 위해 준비하고 있는 관리형 Kubernetes 클러스터의 호스트인 [Azure Kubernetes Service](../../aks/index.yml)를 모니터링하도록 Container 인사이트를 설정하는 방법을 설명합니다.

지원되는 메서드 중 하나를 사용하여 AKS 클러스터의 모니터링을 사용하도록 설정할 수 있습니다.

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Azure CLI 사용

Azure CLI로 만든 새로운 AKS 클러스터에 대한 모니터링을 활성화하려면 [AKS 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster) 섹션 아래 빠른 시작 문서의 단계를 수행하세요.  

>[!NOTE]
>Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.74 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요. aks 미리 보기 CLI 확장 버전 0.4.12 이상을 설치한 경우 Azure US Governmnet 클라우드에서 AKS 미리 보기 기능을 사용할 수 없기 때문에 기본 Azure CLI 동작을 재정의할 수 있으므로 미리 보기 확장을 사용하도록 설정된 모든 변경 내용을 제거합니다.

## <a name="enable-using-terraform"></a>Terraform 사용

[Terraform을 사용하여 새 AKS 클러스터를 배포](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks)하는 경우 기존 항목을 지정하도록 선택하지 않으면 [Log Analytics 작업 영역을 만들도록](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) 프로필에서 필요한 인수를 지정합니다. 

>[!NOTE]
>Terraform을 사용하도록 선택하는 경우 Terraform Azure RM Provider 버전 1.17.0 이상을 실행해야 합니다.

Container 인사이트를 작업 영역에 추가하려면 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)을 참조하여 [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile)을 포함하고 **oms_agent** 를 지정하여 프로필을 완료하세요. 

모니터링을 사용하도록 설정하고 모든 구성 작업이 성공적으로 완료되면 다음 두 가지 방법 중 하나로 클러스터의 성능을 모니터링할 수 있습니다.

* 왼쪽 창에서 **상태** 를 선택하여 AKS 클러스터에서 직접 모니터링합니다.
* 선택한 클러스터에 대한 AKS 클러스터 페이지에서 **컨테이너 정보 모니터링** 타일을 선택하고 Azure Monitor의 왼쪽 창에서 **상태** 를 선택합니다. 

  ![AKS에서 Container 인사이트를 선택하기 위한 옵션](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

## <a name="verify-agent-and-solution-deployment"></a>에이전트 및 솔루션 배포 확인
에이전트 *06072018* 또는 이후 버전을 사용하여 에이전트 및 솔루션이 모두 성공적으로 배포되었는지 확인할 수 있습니다. 이전 버전의 에이전트를 사용하면 에이전트 배포만 확인할 수 있습니다.

### <a name="agent-version-06072018-or-later"></a>에이전트 06072018 또는 이후 버전
다음 명령을 실행하여 에이전트가 성공적으로 배포되었는지 확인합니다. 

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

솔루션의 배포를 확인하려면 다음 명령을 실행합니다.

```
kubectl get deployment omsagent-rs -n=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018 이전 에이전트 버전

*06072018* 이전에 릴리스된 Log Analytics 에이전트 버전이 제대로 배포되었는지 확인하려면 다음 명령을 실행합니다.  

```
kubectl get ds omsagent --namespace=kube-system
```

출력은 다음과 유사해야 하며, 이 출력은 제대로 배포된 것을 나타냅니다.  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>CLI로 구성 보기
`aks show` 명령을 사용하면 솔루션을 사용하도록 설정되어 있는지 여부, Log Analytics 작업 영역 resourceID, 클러스터에 대한 요약 정보와 같은 세부 정보를 얻을 수 있습니다.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

몇 분 후 명령이 완료되면 솔루션에 대한 JSON 형식 정보가 반환됩니다.  명령의 결과에는 모니터링 추가 항목 프로필이 표시되며 다음 예제 출력과 유사합니다.

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>다음 단계

* 솔루션을 등록하는 동안 문제가 발생하는 경우 [문제 해결 가이드](container-insights-troubleshoot.md)를 검토하세요.

* 모니터링을 사용하여 AKS 클러스터의 상태 및 리소스 사용률을 수집하고 해당 작업에서 실행되는 작업을 수집합니다. Container 인사이트 [사용 방법](container-insights-analyze.md)을 알아보세요.

