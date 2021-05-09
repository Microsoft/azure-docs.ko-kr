---
title: Azure Kubernetes Service 클러스터 모니터링을 중지하는 방법 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 Azure AKS 클러스터의 모니터링을 중단하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c46986f87141e9c8ad4c900b765e1fb30592a56d
ms.sourcegitcommit: 52491b361b1cd51c4785c91e6f4acb2f3c76f0d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108316932"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>컨테이너 인사이트를 사용하여 AKS(Azure Kubernetes Service) 모니터링을 중단하는 방법

AKS 클러스터를 모니터링하도록 설정한 후 더 이상 모니터링하지 않으려는 경우 클러스터 모니터링을 중지할 수 있습니다. 이 문서에서는 Azure CLI 또는 제공된 Azure Resource Manager 템플릿을 사용하여 옵트아웃하는 방법을 보여줍니다.  


## <a name="azure-cli"></a>Azure CLI

[az aks disable-addons](/cli/azure/aks#az_aks_disable_addons) 명령을 사용하여 컨테이너 인사이트를 사용하지 않도록 설정합니다. 이 명령은 클러스터 노드에서 에이전트를 제거하지만, 이미 수집되어 Azure Monitor 리소스에 저장된 솔루션 또는 데이터는 제거하지 않습니다.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

클러스터에 대한 모니터링을 다시 사용하려면 [Azure CLI를 사용하여 모니터링을 사용하도록 설정](container-insights-enable-new-cluster.md#enable-using-azure-cli)을 참조하세요.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

리소스 그룹에서 솔루션 리소스를 일관적이고 반복적인 방법으로 제거할 수 있는 두 가지 Azure Resource Manager 템플릿이 제공됩니다. 하나는 모니터링을 중지할 구성을 지정하는 JSON 템플릿이고, 다른 하나는 AKS 클러스터 리소스 ID 및 클러스터가 배포되는 리소스 그룹을 지정하기 위해 구성하는 매개 변수 값을 포함하고 있습니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>템플릿은 클러스터와 동일한 리소스 그룹에 배포되어야 합니다. 이 템플릿을 사용할 때 다른 속성이나 추가 기능을 생략하면 해당 항목이 클러스터에서 제거될 수 있습니다. 예를 들어 클러스터에 구현된 Kubernetes RBAC 정책에 대한 *enableRBAC* 또는 AKS 클러스터에 태그가 지정된 경우 *aksResourceTagValues* 입니다.  
>

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.27 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="create-template"></a>템플릿 만들기

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
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. 이 파일을 로컬 폴더에 **OptOutTemplate.json** 으로 저장합니다.

3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. 선택한 클러스터에 대한 **속성** 페이지에서 찾을 수 있는 AKS 클러스터 값을 사용하여 **aksResourceId** 및 **aksResourceLocation** 의 값을 편집합니다.

    ![컨테이너 속성 페이지](media/container-insights-optout/container-properties-page.png)

    **속성** 페이지에서 **작업 영역 리소스 ID** 도 복사합니다. 이 값은 나중에 Log Analytics 작업 영역을 삭제하려는 경우 필요합니다. Log Analytics 작업 영역을 삭제하는 작업은 이 단계에서는 수행되지 않습니다.

    AKS 클러스터에 지정된 기존 태그 값과 일치하도록 **aksResourceTagValues** 의 값을 편집합니다.

5. 이 파일을 로컬 폴더에 **OptOutParam.json** 으로 저장합니다.

6. 이제 이 템플릿을 배포할 수 있습니다.

### <a name="remove-the-solution-using-azure-cli"></a>Azure CLI를 사용하여 솔루션 제거

Linux에서 Azure CLI로 다음 명령을 실행하여 솔루션을 제거하고 AKS 클러스터에서 구성을 정리합니다.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>PowerShell을 사용하여 솔루션 제거

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

템플릿이 포함된 폴더에서 다음 PowerShell 명령을 실행하여 솔루션을 제거하고 AKS 클러스터에서 구성을 정리합니다.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>다음 단계

클러스터 모니터링을 지원하려는 목적으로만 작업 영역을 만들었으며 더 이상 필요하지 않은 경우 수동으로 삭제해야 합니다. 작업 영역을 삭제하는 방법을 모를 경우 [Azure Portal에서 Azure Log Analytics 작업 영역 삭제](../logs/delete-workspace.md)를 참조하세요. 이전에 4단계에서 복사한 **작업 영역 리소스 ID** 는 나중에 필요하므로 기억해두세요.
