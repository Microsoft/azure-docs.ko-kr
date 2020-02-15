---
title: 컨테이너에 대 한 Azure Monitor를 사용 하 여 하이브리드 Kubernetes 클러스터 구성 | Microsoft Docs
description: 이 문서에서는 Azure Stack 또는 기타 환경에서 호스트 되는 Kubernetes 클러스터를 모니터링 하도록 컨테이너에 Azure Monitor를 구성 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6d03716b988b1139e01d41120f48ea9a9bf34be1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198057"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>컨테이너에 대 한 Azure Monitor를 사용 하 여 하이브리드 Kubernetes 클러스터 구성

컨테이너에 대 한 Azure Monitor는 azure에서 호스트 되는 자체 관리 되는 Kubernetes 클러스터 인 azure의 AKS (Azure Kubernetes Service) 및 [AKS Engine](https://github.com/Azure/aks-engine)에 대 한 풍부한 모니터링 환경을 제공 합니다. 이 문서에서는 Azure 외부에서 호스트 되는 Kubernetes 클러스터의 모니터링을 사용 하도록 설정 하 고 비슷한 모니터링 환경을 구현 하는 방법을 설명 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 항목이 있는지 확인하십시오.

* Log Analytics 작업 영역.

    컨테이너 Azure Monitor는 [지역별 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)에 나열 된 지역에서 Log Analytics 작업 영역을 지원 합니다. 사용자 고유의 작업 영역을 만들려면 [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)또는 [Azure Portal](../learn/quick-create-workspace.md)를 통해 만들 수 있습니다.

    >[!NOTE]
    >동일한 Log Analytics 작업 영역에 동일한 클러스터 이름을 가진 여러 클러스터의 모니터링을 사용 하도록 설정할 수 없습니다. 클러스터 이름은 고유 해야 합니다.
    >

* 컨테이너 모니터링을 사용 하도록 설정 하는 **Log Analytics 참여자 역할** 의 구성원입니다. Log Analytics 작업 영역에 대 한 액세스를 제어 하는 방법에 대 한 자세한 내용은 [작업 영역 및 로그 데이터에 대 한 액세스 관리](../platform/manage-access.md) 를 참조 하세요.

* 지정 된 Kubernetes 클러스터에 대 한 컨테이너 차트의 Azure Monitor를 등록 하는 [클라이언트](https://helm.sh/docs/using_helm/) 를 작성 합니다.

* 다음 프록시 및 방화벽 구성 정보는 Linux 용 Log Analytics 에이전트의 컨테이너 화 된 버전이 Azure Monitor와 통신 하는 데 필요 합니다.

    |에이전트 리소스|포트 |
    |------|---------|   
    |*.ods.opinsights.azure.com |포트 443 |  
    |*.oms.opinsights.azure.com |포트 443 |  
    |\*.blob.core.windows.net |포트 443 |  
    |*. dc.services.visualstudio.com |포트 443 |

* 컨테이너 화 된 에이전트는 성능 메트릭을 수집 하기 위해 클러스터의 모든 노드에서 Kubelet의 `cAdvisor secure port: 10250` 또는 `unsecure port :10255`를 열어야 합니다. Kubelet의 cAdvisor에 이미 구성 되어 있지 않은 경우 `secure port: 10250`를 구성 하는 것이 좋습니다.

* 컨테이너 화 된 에이전트를 사용 하려면 클러스터 내에서 Kubernetes API 서비스와 통신 하기 위해 컨테이너에서 다음과 같은 환경 변수를 지정 해야 합니다. 인벤토리 데이터 `KUBERNETES_SERVICE_HOST` 및 `KUBERNETES_PORT_443_TCP_PORT`수집 합니다.

>[!IMPORTANT]
>하이브리드 Kubernetes 클러스터 모니터링에 대해 지원 되는 최소 에이전트 버전은 ciprod10182019 이상입니다.

## <a name="supported-configurations"></a>지원되는 구성

다음은 Azure Monitor 컨테이너에 대해 공식적으로 지원 됩니다.

- 환경: Kubernetes 온-프레미스, Azure의 AKS 엔진 및 Azure Stack 자세한 내용은 [Azure Stack AKS Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)을 참조 하세요.
- Kubernetes 및 지원 정책의 버전은 [지원 되는 AKS](../../aks/supported-kubernetes-versions.md)버전과 동일 합니다.
- 컨테이너 런타임: Docker 및 Moby
- 마스터 및 작동 하는 노드에 대 한 Linux OS 릴리스: Ubuntu (18.04 LTS 및 16.04 LTS)
- 지원 되는 액세스 제어: Kubernetes RBAC 및 비 RBAC

## <a name="enable-monitoring"></a>모니터링 사용

하이브리드 Kubernetes 클러스터의 컨테이너에 대 한 Azure Monitor를 사용 하도록 설정 하는 것은 다음 단계를 순서 대로 수행 하는 것입니다.

1. Container Insights 솔루션을 사용 하 여 Log Analytics 작업 영역을 구성 합니다.

2. Log Analytics 작업 영역을 사용 하 여 컨테이너 투구 차트에 Azure Monitor을 사용 하도록 설정 합니다.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Azure Monitor 컨테이너 솔루션을 추가 하는 방법

Azure PowerShell cmdlet `New-AzResourceGroupDeployment` 또는 Azure CLI를 사용 하 여 제공 된 Azure Resource Manager 템플릿으로 솔루션을 배포할 수 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)

* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.59 이상을 실행 해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

이 메서드는 두 가지 JSON 템플릿을 포함합니다. 한 가지 템플릿은 모니터링을 사용하도록 구성을 지정하고, 다른 템플릿은 다음을 지정하도록 구성하는 매개 변수 값을 포함합니다.

- **workspaceResourceId** -Log Analytics 작업 영역의 전체 리소스 ID입니다.
- **workspaceRegion** -작업 영역을 만들 지역으로, Azure Portal에서 볼 때 작업 영역 속성에서 **위치** 라고도 합니다.

먼저 **containerSolutionParams** 파일의 `workspaceResourceId` 매개 변수 값에 필요한 Log Analytics 작업 영역의 전체 리소스 ID를 식별 하려면 다음 단계를 수행한 후 PowerShell cmdlet 또는 Azure CLI 명령을 실행 하 여 솔루션을 추가 합니다.

1. 다음 명령을 사용 하 여 액세스 권한이 있는 모든 구독을 나열 합니다.

    ```azurecli
    az account list --all -o table
    ```

    출력은 다음과 유사합니다.

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    **SubscriptionId**의 값을 복사 합니다.

2. 다음 명령을 사용 하 여 Log Analytics 작업 영역을 호스팅하는 구독으로 전환 합니다.

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. 다음 예에서는 구독의 작업 영역 목록을 기본 JSON 형식으로 표시 합니다.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    출력에서 작업 영역 이름을 찾은 다음, 해당 Log Analytics 작업 영역의 전체 리소스 ID를 필드 **ID**로 복사 합니다.

4. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. 이 파일을 로컬 폴더에 containerSolution로 저장 합니다.

6. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. 3 단계에서 복사한 값을 사용 하 여 **workspaceResourceId** 에 대 한 값을 편집 하 고 **workspaceRegion** 의 경우 Azure CLI 명령 [az monitor log-analytics 작업 영역 표시](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list)를 실행 한 후 **지역** 값을 복사 합니다.

8. 이 파일을 로컬 폴더에 containerSolutionParams로 저장 합니다.

9. 이제 이 템플릿을 배포할 수 있습니다.

   * Azure PowerShell를 사용 하 여 배포 하려면 템플릿이 포함 된 폴더에서 다음 명령을 사용 합니다.

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```powershell
       provisioningState       : Succeeded
       ```

   * Azure CLI를 사용 하 여 배포 하려면 다음 명령을 실행 합니다.

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az group deployment create --resource-group <resource group of log analytics workspace> --template-file ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 다음과 유사한 메시지가 표시되고 결과가 포함됩니다.

       ```azurecli
       provisioningState       : Succeeded
       ```

       모니터링을 사용하도록 설정하고 약 15분 후에 클러스터에 대한 상태 메트릭을 볼 수 있습니다.

## <a name="install-the-chart"></a>차트 설치

투구 차트를 사용 하도록 설정 하려면 다음을 수행 합니다.

1. 다음 명령을 실행 하 여 Azure 차트 리포지토리를 로컬 목록에 추가 합니다.

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. 다음 명령을 실행 하 여 차트를 설치 합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure 중국에 있는 경우 다음 명령을 실행 합니다.

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Log Analytics 작업 영역이 Azure 미국 정부에 있는 경우 다음 명령을 실행 합니다.

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

바랄 차트 버전 1.0.0에서 에이전트 데이터 수집 설정은 ConfigMap에서 제어 됩니다. [여기](container-insights-agent-config.md)에서 에이전트 데이터 컬렉션 설정에 대 한 설명서를 참조 하세요.

차트를 성공적으로 배포한 후 Azure Portal의 컨테이너에 대 한 Azure Monitor에서 하이브리드 Kubernetes 클러스터에 대 한 데이터를 검토할 수 있습니다.  

>[!NOTE]
>수집 대기 시간은 에이전트가 Azure Log Analytics 작업 영역에서 커밋하는 데 5 ~ 10 분 정도 소요 됩니다. 클러스터의 상태는 Azure Monitor에서 필요한 모든 모니터링 데이터를 사용할 수 있을 때까지 데이터를 표시 **하지** 않거나 **알 수 없는** 값을 표시 합니다.

## <a name="troubleshooting"></a>문제 해결

하이브리드 Kubernetes 클러스터에 대 한 모니터링을 사용 하도록 설정 하는 동안 오류가 발생 하는 경우 PowerShell 스크립트 [TroubleshootError_nonAzureK8s.](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) p s 1을 복사 하 여 컴퓨터의 폴더에 저장 합니다. 이 스크립트는 발생 한 문제를 검색 하 고 해결 하는 데 도움이 됩니다. 검색 하 고 수정을 시도 하기 위해 설계 된 문제는 다음과 같습니다.

* 지정한 Log Analytics 작업 영역이 올바릅니다.
* Log Analytics 작업 영역은 컨테이너 솔루션에 대 한 Azure Monitor를 사용 하 여 구성 됩니다. 그렇지 않은 경우 작업 영역을 구성 합니다.
* OmsAgent replicaset pod가 실행 중입니다.
* OmsAgent daemonset pod가 실행 중입니다.
* OmsAgent Health service가 실행 되 고 있습니다.
* 컨테이너 화 된 에이전트에 구성 된 Log Analytics 작업 영역 ID 및 키가 정보를 사용 하 여 구성 된 작업 영역과 일치 합니다.
* Rs pod를 예약 하기 위해 `kubernetes.io/role=agent` 레이블이 있는 모든 Linux 작업자 노드에 대 한 유효성을 검사 합니다. 존재 하지 않는 경우 추가 합니다.
* 클러스터의 모든 노드에서 `cAdvisor secure port:10250` 또는 `unsecure port: 10255`의 유효성을 검사 합니다.

Azure PowerShell를 사용 하 여 실행 하려면 스크립트가 포함 된 폴더에서 다음 명령을 사용 합니다.

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>다음 단계

모니터링을 사용 하 여 하이브리드 Kubernetes 클러스터의 상태 및 리소스 사용률을 수집 하 고 해당 작업에서 실행 되는 작업을 수집 합니다. 컨테이너에 Azure Monitor [를 사용 하는 방법을](container-insights-analyze.md) 알아봅니다.
