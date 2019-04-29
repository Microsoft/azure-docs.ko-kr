---
title: Linux용 Azure Network Watcher 에이전트 가상 머신 확장 | Microsoft Docs
description: 가상 머신 확장을 사용하여 Linux 가상 머신에 Network Watcher를 배포합니다.
services: virtual-machines-linux
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 5c81e94c-e127-4dd2-ae83-a236c4512345
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 5ed5e791cd6e611218769650115c78afd1869f67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743432"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-linux"></a>Linux용 Network Watcher 에이전트 가상 머신 확장

## <a name="overview"></a>개요

[Azure Network Watcher](/azure/network-watcher/)는 Azure 네트워크에 대한 모니터링을 허용하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Network Watcher 에이전트 VM(가상 머신) 확장은 요청 시 네트워크 트래픽 캡처 및 기타 고급 기능 등 Azure VM에서 Network Watcher 일부 기능을 위한 요구 사항입니다.

이 아티클에서는 Linux용 Network Watcher 에이전트 VM 확장에 대해 지원되는 플랫폼 및 배포 옵션을 설명합니다. 에이전트 설치는 VM을 중단하거나, 다시 부팅이 필요하지 않습니다. 배포하는 가상 머신에 확장을 배포할 수 있습니다. 가상 머신이 Azure 서비스에서 배포되는 경우 가상 머신에서 확장 설치의 허용 여부를 결정하는 서비스에 대한 설명서를 확인합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

Network Watcher 에이전트 확장은 다음 Linux 배포판에 대해 구성될 수 있습니다.

| 배포 | Version |
|---|---|
| Ubuntu | 12+ |
| Debian | 7 및 8 |
| Red Hat | 6 및 7 |
| Oracle Linux | 6.8+ 및 7 |
| SUSE Linux Enterprise Server | 11 및 12 |
| OpenSUSE Leap | 42.3+ |
| CentOS | 6.5+ 및 7 |
| CoreOS | 899.17.0+ |


### <a name="internet-connectivity"></a>인터넷 연결

일부 Network Watcher 에이전트 기능에서는 VM을 인터넷에 연결해야 합니다. 일부 Network Watcher 에이전트 기능에 나가는 연결을 설정하는 기능이 없는 경우 오작동하거나 사용할 수 없게 됩니다. 에이전트를 필요로 하는 Network Watcher 기능에 대한 자세한 내용은 [Network Watcher 설명서](/azure/network-watcher/)를 참조하세요.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Network Watcher 에이전트 확장에 대한 스키마를 보여 줍니다. 확장은 사용자가 제공한 설정을 요구하거나 지원하지 않습니다. 확장은 해당 기본 구성을 사용합니다.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentLinux",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>속성 값

| 이름 | 값/예제 |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| publisher | Microsoft.Azure.NetworkWatcher |
| 형식 | NetworkWatcherAgentLinux |
| typeHandlerVersion | 1.4 |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. Network Watcher 에이전트 확장을 배포하려면 템플릿에서 이전 json 스키마를 사용합니다.

## <a name="azure-classic-cli-deployment"></a>Azure 클래식 CLI 배포

다음 예제에서는 클래식 배포 모델을 통해 배포된 기존 VM에 Network Watcher 에이전트 VM 확장을 배포합니다.

```azurecli
azure config mode asm
azure vm extension set myVM1 NetworkWatcherAgentLinux Microsoft.Azure.NetworkWatcher 1.4
```

## <a name="azure-cli-deployment"></a>Azure CLI 배포

다음 예제에서는 Resource Manager를 통해 배포된 기존 VM에 Network Watcher 에이전트 VM 확장을 배포합니다.

```azurecli
az vm extension set --resource-group myResourceGroup1 --vm-name myVM1 --name NetworkWatcherAgentLinux --publisher Microsoft.Azure.NetworkWatcher --version 1.4
```

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

### <a name="troubleshooting"></a>문제 해결

Azure Portal 및 Azure CLI를 사용하여 확장 배포 상태에 대한 데이터를 검색할 수 있습니다.

다음 예제에서는 Azure 클래식 CLI를 사용하여 클래식 배포 모델을 통해 배포된 VM에 대한 확장의 배포 상태를 보여 줍니다.

```azurecli
azure config mode asm
azure vm extension get myVM1
```
확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```
/var/log/azure/Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentLinux/
```

다음 예제에서는 Azure CLI를 사용하여 Resource Manager를 통해 배포된 VM에 대한 NetworkWatcherAgentLinux 확장의 배포 상태를 보여 줍니다.

```azurecli
az vm extension show --name NetworkWatcherAgentLinux --resource-group myResourceGroup1 --vm-name myVM1
```

### <a name="support"></a>지원

이 아티클의 어디에서든 도움이 필요한 경우 [Network Watcher 설명서](/azure/network-watcher/)를 참조하거나 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
