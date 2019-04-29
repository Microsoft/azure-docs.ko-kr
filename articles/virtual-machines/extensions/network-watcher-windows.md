---
title: Windows용 Azure Network Watcher 에이전트 가상 머신 확장 | Microsoft Docs
description: 가상 머신 확장을 사용하여 Windows 가상 머신에 Network Watcher를 배포합니다.
services: virtual-machines-windows
documentationcenter: ''
author: gurudennis
manager: amku
editor: ''
tags: azure-resource-manager
ms.assetid: 27e46af7-2150-45e8-b084-ba33de8c5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: dennisg
ms.openlocfilehash: 6e02f5a5b42da9c99a08782903cdc05ee32ec9d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743329"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Windows용 Network Watcher 에이전트 가상 머신 확장

## <a name="overview"></a>개요

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 Azure 네트워크에 대한 모니터링을 허용하는 네트워크 성능 모니터링, 진단 및 분석 서비스입니다. Network Watcher 에이전트 가상 머신 확장은 Azure 가상 머신에서 요청 시 네트워크 트래픽 캡처 및 기타 고급 기능을 위한 요구 사항입니다.


이 문서에서는 Windows용 Network Watcher 에이전트 가상 머신 확장에 대해 지원되는 플랫폼 및 배포 옵션을 설명합니다. 에이전트 설치는 가상 머신을 중단하거나, 다시 부팅이 필요하지 않습니다. 배포하는 가상 머신에 확장을 배포할 수 있습니다. 가상 머신이 Azure 서비스에서 배포되는 경우 가상 머신에서 확장 설치의 허용 여부를 결정하는 서비스에 대한 설명서를 확인합니다.

## <a name="prerequisites"></a>필수 조건

### <a name="operating-system"></a>운영 체제

Windows용 Network Watcher 에이전트 확장은 Windows Server 2008 R2, 2012, 2012 R2 및 2016 릴리스에 대해 실행할 수 있습니다. Nano Server는 현재 지원되지 않습니다.

### <a name="internet-connectivity"></a>인터넷 연결

일부 Network Watcher 에이전트 기능에서는 대상 가상 머신을 인터넷에 연결해야 합니다. 나가는 연결을 설정하는 기능이 없으면 Network Watcher 에이전트는 패킷 캡처를 저장소 계정에 업로드할 수 없게 됩니다. 자세한 내용은 [Network Watcher 설명서](../../network-watcher/network-watcher-monitoring-overview.md)를 참조하세요.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Network Watcher 에이전트 확장에 대한 스키마를 보여 줍니다. 확장은 사용자 제공 설정을 필요로 하거나 지원하지 않고 기본 구성을 사용합니다.

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
        "type": "NetworkWatcherAgentWindows",
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
| 형식 | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명한 JSON 스키마를 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Network Watcher 에이전트 확장을 실행할 수 있습니다.

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzVMExtension` 명령을 사용하여 Network Watcher 에이전트 가상 머신 확장을 기존 가상 머신에 배포합니다.

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

### <a name="troubleshooting"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure Portal 및 PowerShell에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure PowerShell 모듈을 사용하여 다음 명령을 실행합니다.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 Network Watcher 사용자 가이드 설명서를 참조하거나 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
