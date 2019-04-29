---
title: Azure Automation Runbook에 Azure Resource Manager 템플릿 배포
description: Runbook에서 Azure Storage에 저장된 Azure Resource Manager 템플릿을 배포하는 방법
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073749"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Azure Automation PowerShell Runbook에 Azure Resource Manager 템플릿 배포

[Azure Resource Management 템플릿](../azure-resource-manager/resource-manager-create-first-template.md)을 사용하여 Azure 리소스를 배포하는 [Azure Automation PowerShell Runbook](automation-first-runbook-textual-powershell.md)을 작성할 수 있습니다.

이렇게 하면 Azure 리소스 배포를 자동화할 수 있습니다. Azure Storage와 같은 안전한 중앙 위치에서 Resource Manager 템플릿을 유지 관리할 수 있습니다.

이 문서에서는 [Azure Storage](../storage/common/storage-introduction.md)에 저장된 Resource Manager 템플릿을 사용하여 새 Azure Storage 계정을 배포하는 PowerShell Runbook을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음과 같은 항목이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [체험 계정을 등록](https://azure.microsoft.com/free/)할 수 있습니다.
* [Automation 계정](automation-sec-configure-azure-runas-account.md) .  이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* [Azure Storage 계정](../storage/common/storage-create-storage-account.md) - Resource Manager 템플릿을 저장하는 위치입니다.
* 로컬 컴퓨터에 설치된 Azure Powershell. Azure PowerShell을 얻는 방법에 대한 자세한 내용은 [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps)(Azure Powershell 설치 및 구성)을 참조하세요.

## <a name="create-the-resource-manager-template"></a>리소스 관리자 템플릿 만들기

이 예제에서는 새 Azure Storage 계정을 배포하는 Resource Manager 템플릿을 사용합니다.

텍스트 편집기에서 다음 텍스트를 복사합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

파일을 `TemplateTest.json`(으)로 로컬에 저장합니다.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Azure Storage에 Resource Manager 템플릿 저장

이제 PowerShell을 사용하여 Azure Storage 파일 공유를 만들고 `TemplateTest.json` 파일을 업로드합니다.
Azure Portal에서 파일 공유를 만들고 파일을 업로드하는 방법에 대한 지침은 [Windows에서 Azure File Storage 시작](../storage/files/storage-dotnet-how-to-use-files.md)을 참조하세요.

로컬 컴퓨터에서 PowerShell을 시작하고, 다음 명령을 실행하여 파일 공유를 만들고 Resource Manager 템플릿을 해당 파일 공유에 업로드합니다.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>PowerShell Runbook 스크립트 만들기

이제 Azure Storage에서 `TemplateTest.json` 파일을 가져오고 템플릿을 배포하여 새 Azure Storage 계정을 만드는 PowerShell 스크립트를 만듭니다.

텍스트 편집기에서 다음 텍스트를 붙여넣습니다.

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

파일을 `DeployTemplate.ps1`(으)로 로컬에 저장합니다.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Azure Automation 계정으로 Runbook 가져오기 및 게시

이제 PowerShell을 사용하여 Runbook을 Azure Automation 계정으로 가져온 다음 이 Runbook을 게시합니다.
Azure Portal에서 Runbook을 가져오고 게시하는 방법에 대한 자세한 내용은 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.

`DeployTemplate.ps1`을 PowerShell Runbook으로 Automation 계정에 가져오려면 다음 PowerShell 명령을 실행합니다.

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Runbook을 시작합니다.

이제 [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet을 호출하여 Runbook을 시작합니다.

Azure Portal에서 Runbook을 시작하는 방법에 대한 자세한 내용은 [Azure Automation에서 Runbook 시작](automation-starting-a-runbook.md)을 참조하세요.

PowerShell 콘솔에서 다음 명령을 실행합니다.

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Runbook이 실행되고 `$job.Status`를 실행하여 상태를 확인할 수 있습니다.

Runbook은 Resource Manager 템플릿을 가져와서 새 Azure Storage 계정을 배포하는 데 사용합니다.
다음 명령을 실행하여 새 저장소 계정을 만들었음을 확인할 수 있습니다.
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>요약

이것으로 끝입니다. 이제 Azure Automation, Azure Storage 및 Resource Manager 템플릿을 사용하여 모든 Azure 리소스를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* Resource Manager 템플릿에 대한 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/resource-group-overview.md)를 참조하세요.
* Azure Storage를 시작하려면 [Azure Storage 소개](../storage/common/storage-introduction.md)를 참조하세요.
* 다른 유용한 Azure Automation Runbook을 찾으려면 [Azure Automation용 Runbook 및 모듈 갤러리](automation-runbook-gallery.md)를 참조하세요.
* 다른 유용한 Resource Manager 템플릿을 찾으려면 [Azure 퀵 스타트 템플릿](https://azure.microsoft.com/resources/templates/)을 참조하세요.


