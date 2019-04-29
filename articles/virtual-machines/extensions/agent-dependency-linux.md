---
title: Linux 용 azure 모니터링 종속성 가상 머신 확장 | Microsoft Docs
description: 가상 머신 확장을 사용 하 여 Linux 가상 머신에서 Azure Monitor 종속성 에이전트를 배포 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: magoedte
ms.openlocfilehash: 16491ccc8a9cdc24b82c4c4e27705fa30da61088
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800240"
---
# <a name="azure-monitor-dependency-virtual-machine-extension-for-linux"></a>Linux 용 azure 모니터링 종속성 가상 머신 확장

VM용 Azure Monitor 맵 기능은 Microsoft Dependency Agent에서 해당 데이터를 가져옵니다. Linux 용 Azure VM 종속성 에이전트 가상 머신 확장을 게시 되 고 Microsoft에서 지원 됩니다. Azure virtual machines에서 종속성 에이전트를 설치 하는 확장 합니다. 이 문서는 지원 되는 플랫폼, 구성 및 Linux 용 Azure VM 종속성 에이전트 가상 머신 확장에 대 한 배포 옵션을 설명합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

Linux 용 Azure VM 종속성 에이전트 확장에 나열 된 지원 되는 운영 체제에 대해 실행할 수 있습니다 합니다 [지원 되는 운영 체제](../../azure-monitor/insights/vminsights-onboard.md#supported-operating-systems) Vm 배포 문서에 대 한 Azure Monitor의 섹션입니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON Azure Linux VM에서 Azure VM 종속성 에이전트 확장에 대 한 스키마를 보여 줍니다. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux Azure VM."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-01-01 |
| publisher | Microsoft.Azure.Monitoring.DependencyAgent |
| 형식 | DependencyAgentLinux |
| typeHandlerVersion | 9.5 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마 Azure Resource Manager 템플릿 배포 하는 동안 Azure VM 종속성 에이전트 확장을 실행 하는 Azure Resource Manager 템플릿에서 사용할 수 있습니다. 

가상 머신 확장에 대한 JSON은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 [자식 리소스의 이름 및 형식 설정](../../azure-resource-manager/resource-group-authoring-templates.md#child-resources)을 참조하세요. 

다음 예제에서는 종속성 에이전트 확장은 가상 머신 리소스 내에서 중첩 되어 있다고 가정 합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 `"resources": []` 개체에 배치됩니다.


```json
{
    "type": "extensions",
    "name": "DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

템플릿의 루트에 JSON 확장을 배치하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함되며 형식은 중첩된 구성을 반영합니다. 

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "<parentVmResource>/DAExtension",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
    }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

기존 가상 컴퓨터에 종속성 에이전트 VM 확장을 배포 하려면 Azure CLI는 사용할 수 있습니다.  

```azurecli

az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 실행 출력은 다음 파일에 기록됩니다.

```
/opt/microsoft/dependcency-agent/log/install.log 
```

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
