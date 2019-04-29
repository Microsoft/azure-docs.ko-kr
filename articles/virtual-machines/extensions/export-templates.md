---
title: VM 확장을 포함하는 Azure 리소스 그룹 내보내기 | Microsoft Docs
description: 가상 머신 확장을 포함하는 Resource Manager 템플릿을 내보냅니다.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: f56cfeeede393dbdb9632ea4120d3a81e89f3f7c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61484051"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>VM 확장을 포함하는 리소스 그룹 내보내기

Azure 리소스 그룹을 새 Resource Manager 템플릿으로 내보낸 후 다시 배포할 수 있습니다. 내보내기 프로세스 중에 기존 리소스가 해석되고, 배포 시 비슷한 리소스 그룹을 생성하는 Resource Manager 템플릿이 만들어집니다. Virtual Machine 확장을 포함하는 리소스 그룹에 대해 리소스 그룹 내보내기 옵션을 사용할 경우 확장 호환성 및 보호된 설정과 같은 몇 가지 항목을 고려해야 합니다.

이 문서에서는 지원되는 확장 목록을 비롯하여 가상 머신 확장과 관련된 리소스 그룹 내보내기 프로세스의 작동 방식과 보안 데이터 처리에 대해 자세히 설명합니다.

## <a name="supported-virtual-machine-extensions"></a>지원되는 Virtual Machine 확장

여러 Virtual Machine 확장을 사용할 수 있습니다. 모든 확장을 "Automation 스크립트" 기능을 사용하여 Resource Manager 템플릿으로 내보낼 수 있는 것은 아닙니다. 가상 머신 확장이 지원되지 않는 경우 내보낸 템플릿에 수동으로 다시 배치해야 합니다.

Automation 스크립트 기능을 사용하여 다음 확장을 내보낼 수 있습니다.

| 내선 번호 ||||
|---|---|---|---|
| Acronis Backup | Datadog Windows Agent | OS Patching For Linux | VM Snapshot Linux
| Acronis Backup Linux | Docker 확장 | Puppet Agent |
| Bg Info | DSC Extension | Site 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Site 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Site 24x7 Windows Server |
| Chef Client | HPE Security Application Defender | Trend Micro DSA |
| Custom Script | IaaS Antimalware | Trend Micro DSA Linux |
| 사용자 지정 스크립트 확장 | IaaS Diagnostics | VM Access For Linux |
| Custom Script for Linux | Linux Chef Client | VM Access For Linux |
| Datadog Linux Agent | Linux Diagnostic | VM Snapshot |

## <a name="export-the-resource-group"></a>리소스 그룹 내보내기

리소스 그룹을 다시 사용할 수 있는 템플릿으로 내보내려면 다음 단계를 완료합니다.

1. Azure Portal에 로그인
2. 허브 메뉴에서 리소스 그룹 클릭
3. 목록에서 대상 리소스 그룹 선택
4. 리소스 그룹 블레이드에서 Automation 스크립트 클릭

![템플릿 내보내기](./media/export-templates/template-export.png)

Azure Resource Manager Automation 스크립트는 Resource Manager 템플릿, 매개 변수 파일, PowerShell 및 Azure CLI와 같은 몇 가지 샘플 배포 스크립트를 생성합니다. 이제 내보낸 템플릿을 다운로드 단추를 사용하여 다운로드하거나, 템플릿 라이브러리에 새 템플릿으로 추가하거나, 배포 단추를 사용하여 다시 배포할 수 있습니다.

## <a name="configure-protected-settings"></a>보호 설정 구성

여러 Azure 가상 머신 확장에는 자격 증명 및 구성 문자열과 같은 중요한 데이터를 암호화하는 보호 설정 구성이 포함됩니다. 보호 설정은 Automation 스크립트를 사용하여 내보낼 수 없습니다. 필요하면 보호 설정을 내보낸 템플릿에 다시 삽입해야 합니다.

### <a name="step-1---remove-template-parameter"></a>1단계 - 템플릿 매개 변수 제거

리소스 그룹을 내보낼 때 내보낸 보호 설정에 값을 제공하는 단일 템플릿 매개 변수가 만들어집니다. 이 매개 변수는 제거할 수 있습니다. 이 매개 변수를 제거하려면 매개 변수 목록을 확인하고 이 JSON 예제와 유사한 매개 변수를 삭제합니다.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>2단계 - 보호 설정 속성 가져오기

각 보호 설정에는 필수 속성 집합이 있으므로 이러한 속성의 목록을 수집해야 합니다. 보호 설정 구성의 각 매개 변수는 [GitHub의 Azure Resource Manager 스키마](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json)에서 찾을 수 있습니다. 이 스키마는 이 문서의 개요 섹션에 나열된 확장에 대한 매개 변수 집합만 포함합니다. 

스키마 리포지토리 내에서 원하는 확장(이 예제에서는 `IaaSDiagnostics`)을 검색합니다. 확장 `protectedSettings` 개체를 찾으면 각 매개 변수를 적어둡니다. `IaasDiagnostic` 확장 예제에서는 `storageAccountName`, `storageAccountKey` 및 `storageAccountEndPoint` 매개 변수가 필요합니다.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>3단계 - 보호되는 구성 다시 만들기

내보낸 템플릿에서 `protectedSettings`를 검색하고 내보낸된 보호 설정 개체를 필수 확장 매개 변수 및 각 매개 변수의 값을 포함하는 새 개체로 바꿉니다.

`IaasDiagnostic` 확장 예제에서 새로운 보호 설정 구성은 다음 예제와 같습니다.

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

최종 확장 리소스는 다음 JSON 예제와 유사합니다.

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

템플릿 매개 변수를 사용하여 속성 값을 제공하려면 이러한 항목을 만들어야 합니다. 보호 설정 값에 대해 템플릿 매개 변수를 만들 경우 중요한 값이 보호되도록 `SecureString` 매개 변수 형식을 사용해야 합니다. 매개 변수 사용에 대한 자세한 내용은 [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)을 참조하세요.

`IaasDiagnostic` 확장 예제에서는 Resource Manager 템플릿의 매개 변수 섹션에서 다음 매개 변수가 만들어집니다.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

이제 템플릿 배포 방법을 사용하여 템플릿을 배포할 수 있습니다.
