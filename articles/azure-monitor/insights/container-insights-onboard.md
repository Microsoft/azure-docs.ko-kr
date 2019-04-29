---
title: 컨테이너용 Azure Monitor 등록 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너의 성능과 어떤 성능 관련 문제가 확인되었는지 이해할 수 있도록 컨테이너용 Azure Monitor를 등록하고 구성하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: b5ba9a0abe8ec0f72cfaf42c747616e733fb3f32
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101156"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>컨테이너용 Azure Monitor 등록 방법  

이 문서에서는 컨테이너용 Azure Monitor를 설정하여 Kubernetes 환경에 배포되고 [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/)에서 호스트되는 워크로드의 성능을 모니터링하는 방법을 설명합니다.

컨테이너용 Azure Monitor는 다음과 같은 지원되는 방법을 사용하여 AKS의 새 배포 또는 하나 이상의 기존 배포에 사용할 수 있습니다.

* Azure 포털, Azure PowerShell 또는 Azure CLI를 사용 하 여
* [Terraform 및 AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) 사용

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 조건 
시작하기 전에 다음 항목이 있는지 확인하십시오.

- **Log Analytics 작업 영역입니다.** 새 AKS 클러스터 모니터링을 사용하도록 설정할 때 만들거나 온보드 환경에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 영역을 만들도록 할 수 있습니다. 직접 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)를 통해 만들거나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 만들거나 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 만들 수 있습니다.
- 멤버인 사용자가 합니다 **Log Analytics 참가자 역할** 컨테이너 모니터링을 사용 하도록 설정 합니다. Log Analytics 작업 영역에 대한 액세스를 제어하는 방법에 대한 자세한 내용은 [작업 영역 관리](../../azure-monitor/platform/manage-access.md)를 참조하세요.
- 멤버인 사용자가 합니다 **[소유자](../../role-based-access-control/built-in-roles.md#owner)** AKS 클러스터 리소스에는 역할입니다. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>구성 요소 

성능을 모니터링하는 기능은 컨테이너용 Azure Monitor를 위해 특별히 개발된 Linux용 컨테이너화 Log Analytics 에이전트에 따라 달라집니다. 이 특수 에이전트는 클러스터의 모든 노드에서 성능 및 이벤트 데이터를 수집하며, 배포 과정에서 에이전트가 지정된 Log Analytics 작업 영역에 자동으로 배포 및 등록됩니다. 에이전트 버전은 microsoft/oms:ciprod04202018 이상이며, *mmddyyyy* 형식의 날짜로 표시됩니다. 

새로운 버전의 에이전트가 릴리스되면 AKS(Azure Kubernetes Service)에 호스트된 관리형 Kubernetes 클러스터에서 에이전트가 자동으로 업그레이드됩니다. 릴리스된 버전을 따르려면 [에이전트 릴리스 알림](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)을 참조하세요. 

>[!NOTE] 
>AKS 클러스터를 이미 배포한 경우, 이 문서의 뒷부분에 설명된 대로, Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 모니터링을 사용하도록 설정할 수 있습니다. `kubectl`을 사용하여 에이전트를 업그레이드, 삭제, 다시 배포 또는 배포할 수 없습니다. 템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다. 

## <a name="enable-monitoring-for-a-new-cluster"></a>새 클러스터에 대한 모니터링 사용
배포하는 동안 Azure Portal, Azure CLI 또는 Terraform을 사용하여 새 AKS 클러스터의 모니터링을 사용하도록 설정할 수 있습니다.  포털을 사용 하려면 빠른 시작 문서의 단계를 수행 [Azure Kubernetes Service (AKS) 클러스터 배포](../../aks/kubernetes-walkthrough-portal.md) 섹션의 단계에 따라 **상태 및 로그 모니터링**합니다.  

>[!NOTE]
>Portal에서 AKS 클러스터에 대 한 모니터링을 사용 하도록 빠른 시작 문서의 단계를 수행 하는 경우 기존 Log Analytics 작업 영역을 선택 하거나 새로 만듭니다를 하 라는 메시지가 표시 됩니다. 

### <a name="enable-using-azure-cli"></a>Azure CLI 사용
Azure CLI로 만든 새로운 AKS 클러스터에 대한 모니터링을 활성화하려면 [AKS 클러스터 만들기](../../aks/kubernetes-walkthrough.md#create-aks-cluster) 섹션 아래 빠른 시작 문서의 단계를 수행하세요.  

>[!NOTE]
>Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 실행 해야 이상. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 
>

### <a name="enable-using-terraform"></a>Terraform 사용
[Terraform을 사용하여 새 AKS 클러스터를 배포](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)하는 경우 기존 항목을 지정하도록 선택하지 않으면 [Log Analytics 작업 영역을 만들도록](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) 프로필에서 필요한 인수를 지정합니다. 

>[!NOTE]
>Terraform을 사용하도록 선택하는 경우 Terraform Azure RM Provider 버전 1.17.0 이상을 실행해야 합니다.

컨테이너용 Azure Monitor를 작업 영역에 추가하려면 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)을 참조하여 [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile)을 포함하고 **oms_agent**를 지정하여 프로필을 완료하세요. 

모니터링을 사용하도록 설정하고 모든 구성 작업이 성공적으로 완료되면 다음 두 가지 방법 중 하나로 클러스터의 성능을 모니터링할 수 있습니다.

* 왼쪽 창에서 **상태**를 선택하여 AKS 클러스터에서 직접 모니터링합니다.
* 선택한 클러스터에 대한 AKS 클러스터 페이지에서 **컨테이너 정보 모니터링** 타일을 선택하고 Azure Monitor의 왼쪽 창에서 **상태**를 선택합니다. 

  ![AKS의 컨테이너에 대한 Azure Monitor 선택용 옵션](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>관리되는 기존 클러스터에 대해 모니터링 사용
지원 되는 방법 중 하나를 사용 하 여 이미 배포 된 AKS 클러스터의 모니터링을 사용할 수 있습니다.

* Azure CLI
* Terraform
* [Azure monitor에서](#enable-from-azure-monitor-in-the-portal) 나 [AKS 클러스터에서 직접](#enable-directly-from-aks-cluster-in-the-portal) Azure portal에서 
* 사용 하 여 합니다 [Azure Resource Manager 템플릿을 제공](#enable-using-an-azure-resource-manager-template) Azure PowerShell cmdlet을 사용 하 여 `New-AzResourceGroupDeployment` 또는 Azure CLI를 사용 하 여 합니다. 

### <a name="enable-using-azure-cli"></a>Azure CLI 사용
다음 단계에서는 Azure CLI를 사용하여 AKS 클러스터의 모니터링을 사용하도록 설정합니다. 이 예제에서는 기존 작업 영역을 미리 만들거나 지정할 필요가 없습니다. 이 명령은 해당 지역에서 AKS 클러스터 구독의 기본 리소스 그룹에 기본 작업 공간이 아직 없는 경우 기본 작업 공간을 만들어서 프로세스를 간소화합니다.  만든 기본 작업 영역은 *DefaultWorkspace-\<GUID>-\<Region>* 형식과 비슷합니다.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

출력은 다음과 유사합니다.

```azurecli
provisioningState       : Succeeded
```

기존 작업 영역과 통합하려는 경우 다음 명령을 사용하여 해당 작업 영역을 지정합니다.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

출력은 다음과 유사합니다.

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-using-terraform"></a>Terraform 사용
1. 기존 [azurerm_kubernetes_cluster 리소스](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)에 **oms_agent** 추가 프로필을 추가합니다.

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Terraform 설명서의 단계에 따라 [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html)을 추가합니다.

### <a name="enable-from-azure-monitor-in-the-portal"></a>포털에서 Azure Monitor에서 사용 하도록 설정 
Azure Monitor의 Azure Portal에서 AKS 클러스터의 모니터링을 사용하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모니터**를 선택합니다. 
2. 목록에서 **컨테이너**를 선택합니다.
3. **모니터 - 컨테이너** 페이지에서 **모니터링되지 않는 클러스터**를 선택합니다.
4. 모니터링되지 않는 클러스터 목록에서 이 컨테이너를 찾아 **사용**을 클릭합니다.   
5. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너용 Azure Monitor에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 AKS 컨테이너가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다. 

    ![AKS 컨테이너 정보 모니터링 사용](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. AKS 컨테이너가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다. 
 
모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다. 

### <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Portal에서 AKS 클러스터에서 직접 사용 하도록 설정
Azure portal에서 AKS 클러스터 중 하나에서 직접 모니터링을 사용 하도록 설정 하려면 다음을 수행 합니다.

1. Azure Portal에서 **모든 서비스**를 선택합니다. 
2. 리소스 목록에서 **컨테이너** 입력을 시작합니다.  
    입력한 내용을 기반으로 목록이 필터링됩니다. 
3. **Kubernetes 서비스**를 선택합니다.  

    ![Kubernetes 서비스 링크](./media/container-insights-onboard/portal-search-containers-01.png)

4. 컨테이너 목록에서 컨테이너를 선택합니다.
5. 컨테이너 개요 페이지에서 **컨테이너 모니터링**을 선택합니다.  
6. 클러스터와 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 **컨테이너용 Azure Monitor에 온보딩** 페이지의 드롭다운 목록에서 해당 작업 영역을 선택합니다.  
    구독에서 AKS 컨테이너가 배포된 기본 작업 영역 및 위치가 미리 선택됩니다. 

    ![AKS 컨테이너 상태 모니터링 사용](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >클러스터의 모니터링 데이터를 저장하기 위해 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. AKS 컨테이너가 배포된 구독과 동일한 구독에 작업 영역을 만들어야 합니다. 
 
모니터링을 사용하도록 설정한 후 약 15분 후에 클러스터에 대한 운영 데이터를 볼 수 있습니다. 

### <a name="enable-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여 사용 하도록 설정
이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

* AKS 컨테이너 리소스 ID. 
* 클러스터가 배포된 리소스 그룹.

>[!NOTE]
>템플릿을 클러스터와 동일한 리소스 그룹에 배포해야 합니다.
>

Log Analytics 작업 영역에 Azure PowerShell 또는 CLI를 사용 하 여 모니터링을 활성화 하기 전에 만들어야 합니다. 작업 영역을 만들려면 [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)나 [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)을 통해 또는 [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)에서 설정할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 실행 해야 이상. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요. 

#### <a name="create-and-execute-a-template"></a>템플릿 만들기 및 실행

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "aksResourceId": {
        "type": "string",
        "metadata": {
           "description": "AKS Cluster Resource ID"
           }
    },
    "aksResourceLocation": {
    "type": "string",
     "metadata": {
        "description": "Location of the AKS resource e.g. \"East US\""
       }
    },
    "workspaceResourceId": {
      "type": "string",
      "metadata": {
         "description": "Azure Monitor Log Analytics Resource ID"
       }
    }
    },
    "resources": [
      {
    "name": "[split(parameters('aksResourceId'),'/')[8]]",
    "type": "Microsoft.ContainerService/managedClusters",
    "location": "[parameters('aksResourceLocation')]",
    "apiVersion": "2018-03-31",
    "properties": {
      "mode": "Incremental",
      "id": "[parameters('aksResourceId')]",
      "addonProfiles": {
        "omsagent": {
          "enabled": true,
          "config": {
            "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
          }
         }
       }
      }
     }
     ]
    }
    ```

2. 이 파일을 **existingClusterOnboarding.json**으로 로컬 폴더에 저장합니다.
3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       }  
     }
    }
    ```

4. 값을 편집 **aksResourceId** 하 고 **aksResourceLocation** 에 값을 사용 하는 **AKS 개요** AKS 클러스터에 대 한 페이지입니다. **workspaceResourceId** 값은 Log Analytics 작업 영역의 전체 리소스 ID 이며, 작업 영역 이름을 포함합니다. 
5. 이 파일을 **existingClusterParam.json**으로 로컬 폴더에 저장합니다.
6. 이제 이 템플릿을 배포할 수 있습니다. 

   * Azure PowerShell을 사용 하 여를 배포 하려면 템플릿에 포함 된 폴더에서 다음 명령을 사용 하 여:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLI를 사용 하 여 배포 하려면 다음 명령을 실행 합니다.
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```azurecli
       provisioningState       : Succeeded
       ```
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

* 모니터링을 사용하여 AKS 클러스터 노드와 Pod에 관한 상태 메트릭을 캡처하면, Azure Portal에서 해당 상태 메트릭을 사용할 수 있습니다. 컨테이너용 Azure Monitor 사용 방법을 알아보려면 [Azure Kubernetes Service 상태 보기](container-insights-analyze.md)를 참조하세요.
