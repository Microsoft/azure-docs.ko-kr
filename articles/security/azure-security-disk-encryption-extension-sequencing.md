---
title: Azure 디스크 암호화 및 Azure 가상 머신 확장 집합 확장 시퀀싱
description: 이 문서에서는 Linux IaaS VM용 Microsoft Azure Disk Encryption을 사용하도록 설정하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611262"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>가상 머신 확장을 사용 하 여 사용 하 여 Azure Disk Encryption 확장 시퀀싱 설정

Azure virtual machines 확장에 Azure 디스크 암호화와 같은 확장을 추가할 수 있습니다 지정 된 순서로 설정 합니다. 이 위해 사용 하 여 [확장 시퀀싱](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)합니다. 

일반적으로 디스크에 암호화를 적용 해야 합니다.

- 확장 또는 사용자 지정 스크립트 후 디스크 또는 볼륨을 준비 하는 합니다.
- 확장 또는 사용자 지정 스크립트 이전에 액세스 하는 또는 암호화 된 디스크 또는 볼륨에 데이터를 사용 합니다.

두 경우 모두는 `provisionAfterExtensions` 속성에서 지정 하는 시퀀스의 뒷부분에 나오는 확장을 추가 해야 합니다.

## <a name="sample-azure-templates"></a>샘플 Azure 템플릿

다른 확장 한 후에 Azure Disk Encryption 적용 하려는 경우, 배치는 `provisionAfterExtensions` AzureDiskEncryption 확장 블록의 속성입니다. 

"CustomScriptExtension"를 초기화 하 고 "AzureDiskEncryption" 뒤에 Windows 디스크를 포맷 하는 Powershell 스크립트를 사용 하는 예제는 다음과 같습니다.

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

다른 확장 하기 전에 Azure Disk Encryption을 적용 하려는 경우, 배치는 `provisionAfterExtensions` 속성에 따라 확장의 블록에서입니다.

"AzureDiskEncryption" 뒤에 "VMDiagnosticsSettings", 모니터링을 제공 하는 확장 및 진단 기능을 Windows 기반 Azure VM에서 사용 하는 예제는 다음과 같습니다.


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

더 많은 자세한 템플릿은 다음을 참조 합니다.
* Azure Disk Encryption 확장 (Linux) 디스크를 포맷 하는 사용자 지정 셸 스크립트를 한 후 적용: [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* 초기화 하 고 (Windows) 디스크를 포맷 하는 사용자 지정 Powershell 스크립트를 한 후 Azure Disk Encryption 확장을 적용 합니다. [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* 사용자 지정 Powershell 스크립트를 초기화 하 고 (Windows) 디스크를 포맷 하기 전에 Azure Disk Encryption 확장을 적용 합니다. [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>다음 단계
- 확장 시퀀싱에 대 한 자세한 정보: [Virtual machine scale sets의 확장을 프로 비전 시퀀스](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)합니다.
- 에 대 한 자세한 정보는 `provisionAfterExtensions` 속성: [Microsoft.Compute virtualMachineScaleSets/확장 템플릿 참조](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)합니다.
