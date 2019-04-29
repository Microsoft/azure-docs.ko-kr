---
title: Stackify Retrace Azure Linux 에이전트 확장 | Microsoft Docs
description: Linux 가상 머신에 Stackify Retrace Linux 에이전트를 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: darinhoward
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2018
ms.author: roiyz
ms.openlocfilehash: b9c035c1c9088957f59550bf6564cc02bc7972f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620603"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify Retrace Linux 에이전트 확장

## <a name="overview"></a>개요

Stackify는 문제를 빠르게 찾고 해결할 수 있도록 애플리케이션에 대한 세부 정보를 추적하는 제품을 제공합니다. Retrace는 개발자 팀을 위해 완벽하게 통합된 다중 환경의 강력한 앱 성능입니다. 이는 모든 개발자 팀에 필요한 여러 도구를 결합합니다.

Retrace는 단일 플랫폼에서 모든 환경에 걸쳐 다음 기능을 모두 제공하는 유일한 도구입니다.

* APM(애플리케이션 성능 관리)
* 애플리케이션 및 서버 로깅
* 오류 추적 및 모니터링
* 서버, 애플리케이션 및 사용자 지정 메트릭

**Stackify Linux 에이전트 확장 정보**

이 확장은 Retrace용 Linux 에이전트에 대한 설치 경로를 제공합니다. 

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제 

Retrace 에이전트를 실행할 수 있는 Linux 배포판은 다음과 같습니다.

| 배포 | Version |
|---|---|
| Ubuntu | 16.04 LTS, 14.04 LTS, 16.10 및 17.04 |
| Debian | 7.9+ 및 8.2+, 9 |
| Red Hat | 6.7+, 7.1+ |
| CentOS | 6.3+, 7.0+ |

### <a name="internet-connectivity"></a>인터넷 연결

Linux용 Stackify 에이전트 확장을 사용하려면 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 

Stackify에 연결할 수 있도록 네트워크 구성을 조정해야 할 수도 있습니다(https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall 참조). 


## <a name="extension-schema"></a>확장 스키마

---

다음 JSON은 Stackify Retrace 확장에 대한 스키마를 보여 줍니다. 확장에는 `environment` 및 `activationKey`가 필요합니다.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>템플릿 배포 

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마는 Azure Resource Manager 템플릿 배포 중에 Azure Stackify Retrace Linux 에이전트 확장을 실행하기 위해 Azure Resource Manager 템플릿에서 사용할 수 있습니다.  

가상 머신 확장에 대한 JSON은 가상 머신 리소스 내에 중첩되거나 루트 또는 최상위 수준의 Resource Manager JSON 템플릿에 배치될 수 있습니다. JSON의 배치는 리소스 이름 및 형식 값에 영향을 줍니다. 자세한 내용은 자식 리소스에 대한 이름 및 형식 설정을 참조하세요.

다음 예제에서는 Stackify Retrace Linux 확장이 가상 머신 리소스 내에 중첩되어 있다고 가정합니다. 확장 리소스를 중첩하는 경우 JSON은 가상 머신의 "resources": [] 개체에 배치됩니다.

확장에는 `environment` 및 `activationKey`가 필요합니다.

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

템플릿의 루트에 JSON 확장을 배치하면 리소스 이름에 부모 가상 머신에 대한 참조가 포함되며 형식은 중첩된 구성을 반영합니다.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzVMExtension` 명령을 사용하여 Stackify Retrace Linux 에이전트 가상 머신 확장을 기존 가상 머신에 배포할 수 있습니다. 명령을 실행하기 전에 공용 및 개인 구성을 PowerShell 해시 테이블에 저장해야 합니다.

확장에는 `environment` 및 `activationKey`가 필요합니다.

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포 

Azure CLI 도구를 사용하여 Stackify Retrace Linux 에이전트 가상 머신 확장을 기존 가상 머신에 배포할 수 있습니다.  

확장에는 `environment` 및 `activationKey`가 필요합니다.

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="error-codes"></a>오류 코드

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 10 | 설치 오류 | wget이 필요합니다. |
| 20 | 설치 오류 | python이 필요합니다. |
| 30 | 설치 오류 | sudo가 필요합니다. |
| 40 | 설치 오류 | activationKey가 필요합니다. |
| 51 | 설치 오류 | OS 배포판이 지원되지 않습니다. |
| 60 | 설치 오류 | 환경이 필요합니다. |
| 70 | 설치 오류 | 알 수 없음 |
| 80 | 사용 설정 오류 | 서비스를 설치하지 못했습니다. |
| 90 | 사용 설정 오류 | 서비스를 시작하지 못했습니다. |
| 100 | 사용 해제 오류 | 서비스를 중지하지 못했습니다. |
| 110 | 사용 해제 오류 | 서비스를 제거하지 못했습니다 |
| 120 | 제거 오류 | 서비스를 중지하지 못했습니다. |

도움이 더 필요하면 https://support.stackify.com에서 Stackify 지원 팀에 문의하세요.