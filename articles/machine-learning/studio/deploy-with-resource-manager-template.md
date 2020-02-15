---
title: Azure Resource Manager를 사용 하 여 Studio (클래식) 작업 영역 배포
titleSuffix: ML Studio (classic) - Azure
description: Azure Resource Manager 템플릿을 사용 하 여 Azure Machine Learning Studio (클래식)에 대 한 작업 영역을 배포 하는 방법
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169361"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 Azure Machine Learning Studio (클래식) 작업 영역 배포

Azure Resource Manager 배포 템플릿을 사용하면 유효성 검사와 상호 연결된 구성 요소를 배포하고 메커니즘을 다시 시도하는 확장성 있는 방법을 제공하여 시간을 절약할 수 있습니다. 예를 들어 Azure Machine Learning Studio (클래식) 작업 영역을 설정 하려면 먼저 Azure storage 계정을 구성 하 고 작업 영역을 배포 해야 합니다. 수백 개의 작업 영역에 대해 이 작업을 수동으로 수행한다고 가정합니다. 보다 쉬운 방법은 Azure Resource Manager 템플릿을 사용 하 여 Studio (클래식) 작업 영역 및 모든 해당 종속성을 배포 하는 것입니다. 이 문서는 이 과정을 단계별로 안내합니다. Azure Resource Manager에 대한 개요는 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>단계별: Machine Learning 작업 영역 만들기
Azure 리소스 그룹을 만든 다음 리소스 관리자 템플릿을 사용 하 여 새 Azure storage 계정 및 새 Azure Machine Learning Studio (클래식) 작업 영역을 배포 합니다. 배포가 완료되면 생성된 작업 영역에 대한 중요한 정보를 인쇄합니다(기본 키, workspaceID 및 작업 영역에 대한 URL).

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

Machine Learning 작업 영역은 연결된 데이터 세트를 저장하려면 Azure Storage 계정이 필요합니다.
다음 템플릿은 리소스 그룹의 이름을 사용하여 스토리지 계정 이름 및 작업 영역 이름을 생성합니다.  또한 작업 영역을 만들 때 속성으로 스토리지 계정 이름을 사용합니다.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
c:\temp\ 아래에 mlworkspace.json 파일로 이 템플릿을 저장합니다.

### <a name="deploy-the-resource-group-based-on-the-template"></a>템플릿을 기반으로 리소스 그룹 배포

* PowerShell 열기
* Azure Resource Manager 및 Azure 서비스 관리에 대한 모듈 설치

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   이러한 단계는 나머지 단계를 완료하는 데 필요한 모듈을 다운로드 및 설치합니다. PowerShell 명령을 실행하는 환경에서 한 번만 수행하면 됩니다.

* Azure에 대한 인증

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
이 단계는 각 세션에 대해 반복해야 합니다. 인증되면 구독 정보가 표시됩니다.

![Azure 계정](./media/deploy-with-resource-manager-template/azuresubscription.png)

이제 Azure에 대한 액세스가 있으므로 리소스 그룹을 만들 수 있습니다.

* 리소스 그룹 만들기

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

리소스 그룹이 올바르게 프로비전되었는지 확인합니다. **ProvisioningState** 는 "Succeeded"여야 합니다.
리소스 그룹 이름은 스토리지 계정 이름을 생성하는 템플릿에 의해 사용됩니다. 스토리지 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다.

![리소스 그룹](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* 리소스 그룹 배포를 사용하여 새 Machine Learning 작업 영역을 배포합니다.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

배포가 완료되면 배포한 작업 영역의 속성에 액세스하는 것은 간단합니다. 예를 들어 기본 키 토큰에 액세스할 수 있습니다.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

기존 작업 영역의 토큰을 검색 하는 또 다른 방법은 AzResourceAction 명령을 사용 하는 것입니다. 예를 들어 모든 작업 영역의 기본 및 보조 토큰을 나열할 수 있습니다.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
작업 영역을 프로 비전 한 후 [에는 Azure Machine Learning Studio (클래식)에 대 한 PowerShell 모듈](https://aka.ms/amlps)을 사용 하 여 많은 Azure Machine Learning Studio (클래식) 작업을 자동화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Resource Manager 템플릿 작성](../../azure-resource-manager/templates/template-syntax.md)에 대해 자세히 알아봅니다.
* [Azure 빠른 시작 템플릿 리포지토리](https://github.com/Azure/azure-quickstart-templates)를 살펴봅니다.
* [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)에 대한 이 동영상을 시청합니다.
* [Resource Manager 템플릿 참조 도움말](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)을 참조하세요.

<!--Link references-->
