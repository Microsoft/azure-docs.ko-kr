---
title: Azure Resource Manager 템플릿을 사용 하 여 작업 영역 만들기
titleSuffix: Azure Machine Learning service
description: Azure Resource Manager 템플릿을 사용하여 새 Azure Machine Learning Service 작업 영역을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2019
ms.custom: seoapril2019
ms.openlocfilehash: 7349998325e56d5ebb78de5ca30c0127f09102aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819537"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning 서비스에 대 한 작업 영역 만들기

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Machine Learning Service 작업 영역을 만드는 몇 가지 방법을 설명합니다. Resource Manager 템플릿을 사용하면 조정된 단일 작업으로 리소스를 쉽게 만들 수 있습니다. 템플릿은 배포에 필요한 리소스를 정의하는 JSON 문서입니다. 배포 매개 변수도 지정할 수 있습니다. 매개 변수는 템플릿 사용 시 입력 값을 제공하는 데 사용됩니다.

자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](../../azure-resource-manager/resource-group-template-deploy.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Azure 구독**. 구독이 없는 경우[Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

* CLI에서 템플릿을 사용하려면 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)가 필요합니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿

다음 Resource Manager 템플릿은 Azure Machine Learning 서비스 작업 영역 및 연결 된 Azure 리소스를 만드는 데 사용할 수 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "The name of the Azure Machine Learning service workspace."
            }
        },
        "location": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "eastus2",
                "southcentralus",
                "southeastasia",
                "westcentralus",
                "westeurope",
                "westus2"
            ],
            "metadata": {
                "description": "The location where the workspace will be created."
            }
        }
    },
    "variables": {
        "storageAccount": {
            "name": "[concat('sa',uniqueString(resourceGroup().id))]",
            "type": "Standard_LRS"
        },
        "keyVault": {
            "name": "[concat('kv',uniqueString(resourceGroup().id))]",
            "tenantId": "[subscription().tenantId]"
        },
        "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
        "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "name": "[variables('storageAccount').name]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2018-07-01",
            "sku": {
                "name": "[variables('storageAccount').type]"
            },
            "kind": "StorageV2",
            "properties": {
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                },
                "supportHttpsTrafficOnly": true
            }
        },
        {
            "name": "[variables('keyVault').name]",
            "type": "Microsoft.KeyVault/vaults",
            "apiVersion": "2018-02-14",
            "location": "[parameters('location')]",
            "properties": {
                "tenantId": "[variables('keyVault').tenantId]",
                "sku": {
                    "name": "standard",
                    "family": "A"
                },
                "accessPolicies": []
            }
        },
        {
            "name": "[variables('applicationInsightsName')]",
            "type": "Microsoft.Insights/components",
            "apiVersion": "2015-05-01",
            "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
            "kind": "web",
            "properties": {
                "Application_Type": "web"
            }
        },
        {
            "name": "[variables('containerRegistryName')]",
            "type": "Microsoft.ContainerRegistry/registries",
            "apiVersion": "2017-10-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard"
            },
            "properties": {
                "adminUserEnabled": true
            }
        },
        {
            "name": "[parameters('workspaceName')]",
            "type": "Microsoft.MachineLearningServices/workspaces",
            "apiVersion": "2018-11-19",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[variables('storageAccount').name]",
                "[variables('keyVault').name]",
                "[variables('applicationInsightsName')]",
                "[variables('containerRegistryName')]"
            ],
            "identity": {
                "type": "systemAssigned"
            },
            "properties": {
                "friendlyName": "[parameters('workspaceName')]",
                "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVault').name)]",
                "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
                "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
                "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccount').name)]"
            }
        }
    ]
}
```

이 템플릿은 다음과 같은 Azure 서비스를 만듭니다.

* Azure 리소스 그룹
* Azure Storage 계정
* Azure Key Vault
* Azure Application Insights
* Azure 컨테이너 레지스트리
* Azure Machine Learning 작업 영역

리소스 그룹은 서비스를 보관하는 컨테이너입니다. Azure Machine Learning 작업 영역에는 다양한 서비스가 필요합니다.

예제 템플릿에는 다음 두 개의 매개 변수가 있습니다.

* **location** - 리소스 그룹 및 서비스가 생성되는 위치입니다.

    선택한 위치는 템플릿에서 대부분의 리소스에 사용됩니다. 단, 다른 서비스를 사용할 수 있는 일부 위치에서 사용할 수 없는 Application Insights 서비스는 예외입니다. 서비스를 사용할 수 없는 위치를 선택하면 미국 중남부 위치에 서비스가 생성됩니다.

* **workspace name** - Azure Machine Learning 작업 영역의 이름입니다.

    다른 서비스의 이름은 임의로 생성됩니다.

템플릿에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 리소스 관리자 템플릿 작성](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager 템플릿으로 애플리케이션 배포](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft.MachineLearningServices 리소스 종류](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [사용자 지정 템플릿에서 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)의 단계를 수행합니다. __템플릿 편집__ 화면이 표시되면 이 문서의 템플릿을 붙여넣습니다.
1. __저장__을 선택하여 템플릿을 사용합니다. 다음 정보를 제공하고 나열된 사용 약관에 동의합니다.

   * 구독: 이러한 리소스에 사용할 Azure 구독을 선택합니다.
   * 리소스 그룹: 서비스를 포함할 리소스 그룹을 선택하거나 만듭니다.
   * 작업 영역 이름: 생성될 Azure Machine Learning 작업 영역에 사용할 이름입니다. 작업 영역 이름은 3자에서 33자 사이여야 합니다. 영숫자와 ‘-’만 포함할 수 있습니다.
   * 위치: 리소스가 생성될 위치를 선택합니다.

     ![Azure Portal의 템플릿 매개 변수](media/how-to-create-workspace-template/template-parameters.png)

자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template)를 참조하세요.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy.md) 및 [SAS 토큰과 Azure PowerShell을 사용하여 개인 Resource Manager 템플릿 배포](../../azure-resource-manager/resource-manager-powershell-sas-token.md)를 참조하세요.

## <a name="use-azure-cli"></a>Azure CLI 사용

이 예제에서는 현재 디렉터리의 `azuredeploy.json` 파일에 템플릿을 저장했다고 가정합니다.

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace
```

자세한 내용은 [Resource Manager 템플릿과 Azure CLI로 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-cli.md) 및 [SAS 토큰과 Azure CLI를 사용하여 개인 Resource Manager 템플릿 배포](../../azure-resource-manager/resource-manager-cli-sas-token.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Resource Manager 템플릿 및 Resource Manager REST API를 사용하여 리소스 배포](../../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Visual Studio를 통해 Azure 리소스 그룹 만들기 및 배포](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
