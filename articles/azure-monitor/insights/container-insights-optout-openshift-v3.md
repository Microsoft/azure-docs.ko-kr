---
title: Azure Red Hat OpenShift 클러스터 모니터링을 중지 하는 방법 | Microsoft Docs
description: 이 문서는 컨테이너에 대 한 Azure Monitor를 사용 하 여 Azure Red Hat OpenShift 클러스터의 모니터링을 중지 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 6ed92cd4cda1f3b5d43cc605d7224236528b94bf
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815439"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Azure Red Hat OpenShift 클러스터 모니터링을 중지 하는 방법 

Azure Red Hat OpenShift 버전 3.x 클러스터의 모니터링을 사용 하도록 설정한 후에는 더 이상 모니터링 하지 않기로 결정 한 경우 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터의 모니터링을 중지할 수 있습니다. 이 문서에서는 제공 된 Azure Resource Manager 템플릿을 사용 하 여이를 수행 하는 방법을 보여 줍니다. 

## <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

리소스 그룹에서 솔루션 리소스를 일관적이고 반복적인 방법으로 제거할 수 있는 두 가지 Azure Resource Manager 템플릿이 제공됩니다. 하나는 모니터링을 중지 하는 구성을 지정 하는 JSON 템플릿이 고, 다른 하나에는 클러스터를 배포 하는 OpenShift 클러스터 리소스 ID 및 Azure 지역을 지정 하도록 구성 하는 매개 변수 값이 포함 됩니다.

템플릿을 사용하여 리소스를 배포하는 개념에 익숙하지 않은 경우 다음을 참조하십시오.
* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)
* [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/templates/deploy-cli.md)

Azure CLI를 사용하도록 선택한 경우, 먼저 CLI를 로컬에 설치하고 사용해야 합니다. Azure CLI 버전 2.0.65 이상을 실행 해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. Azure CLI를 설치하거나 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

### <a name="create-template"></a>템플릿 만들기

1. 다음 JSON 구문을 파일에 복사하여 붙여넣습니다.

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. 이 파일을 로컬 폴더에 **OptOutTemplate.json**으로 저장합니다.

3. 다음 JSON 구문을 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. 선택한 클러스터의 **속성** 페이지에서 찾을 수 있는 openshift 클러스터의 값을 사용 하 여 **Aroresourceid** 및 **aroresourceid** 에 대 한 값을 편집 합니다.

    ![컨테이너 속성 페이지](media/container-insights-optout-openshift/cluster-properties-page.png)

5. 이 파일을 로컬 폴더에 **OptOutParam.json**으로 저장합니다.

6. 이제 이 템플릿을 배포할 수 있습니다.

### <a name="remove-the-solution-using-azure-cli"></a>Azure CLI를 사용하여 솔루션 제거

Linux에서 Azure CLI를 사용 하 여 다음 명령을 실행 하 여 솔루션을 제거 하 고 클러스터의 구성을 정리 합니다.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

구성 변경을 완료하려면 몇 분 정도 걸릴 수 있습니다. 완료되면 결과가 포함된 다음과 비슷한 메시지가 반환됩니다.

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>PowerShell을 사용하여 솔루션 제거

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

템플릿을 포함 하는 폴더에서 다음 PowerShell 명령을 실행 하 여 솔루션을 제거 하 고 클러스터에서 구성을 정리 합니다.    

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

클러스터 모니터링을 지원하려는 목적으로만 작업 영역을 만들었으며 더 이상 필요하지 않은 경우 수동으로 삭제해야 합니다. 작업 영역을 삭제 하는 방법에 익숙하지 않은 경우 [Azure Log Analytics 작업 영역 삭제](../platform/delete-workspace.md)를 참조 하세요.
