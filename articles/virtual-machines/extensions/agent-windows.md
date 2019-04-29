---
title: Azure Virtual Machines 에이전트 개요 | Microsoft Docs
description: Azure Virtual Machines 에이전트 개요
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: d17d7c9d7b57e6ca040e4f81c9665789c8bc26e2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799778"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure Virtual Machines 에이전트 개요
Microsoft Azure VM 에이전트(가상 머신 에이전트)는 Azure 패브릭 컨트롤러와 VM(가상 머신)의 상호 작용을 관리하는 안전하고 간단한 프로세스입니다. VM 에이전트는 Azure 가상 머신 확장을 설정하고 실행하는 데 기본적인 역할을 수행합니다. VM 확장을 사용하면 소프트웨어 설치 및 구성과 같은 VM의 배포 후 구성을 설정할 수 있습니다. 또한 VM 확장을 사용하면 VM의 관리 암호를 다시 설정하는 등의 복구 기능도 사용할 수 있습니다. Azure VM 에이전트가 없으면 VM 확장을 실행할 수 없습니다.

이 문서에서는 Azure 가상 머신 에이전트의 설치, 검색 및 제거에 대해 자세히 설명합니다.

## <a name="install-the-vm-agent"></a>VM 에이전트 설치

### <a name="azure-marketplace-image"></a>Azure Marketplace 이미지

Azure VM 에이전트는 Azure Marketplace 이미지에서 배포된 모든 Windows VM에 기본적으로 설치됩니다. 포털, PowerShell, 명령줄 인터페이스 또는 Azure Resource Manager 템플릿에서 Azure Marketplace 이미지를 배포하면 Azure VM 에이전트도 설치됩니다.

Windows 게스트 에이전트 패키지는 두 부분으로 나뉘어 있습니다.

- PA(프로비전 에이전트)
- WinGA(Windows 게스트 에이전트)

VM을 부팅하려면 VM에 PA가 설치되어 있어야 하지만 WinGA는 설치할 필요가 없습니다. VM 배포 시 WinGA를 설치하지 않도록 선택할 수 있습니다. 다음 예제에서는 Azure Resource Manager 템플릿으로 *provisionVmAgent* 옵션을 선택하는 방법을 보여 줍니다.

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

에이전트가 설치되어 있지 않으면 Azure Backup 또는 Azure Security와 같은 일부 Azure 서비스를 사용할 수 없습니다. 이러한 서비스를 사용하려면 확장을 설치해야 합니다. WinGA 없이 VM을 배포한 경우 나중에 최신 버전의 에이전트를 설치할 수 있습니다.

### <a name="manual-installation"></a>수동 설치
Windows VM 에이전트는 Windows 설치 관리자 패키지를 사용하여 수동으로 설치할 수 있습니다. Azure에 배포된 사용자 지정 VM 이미지를 만들 때 수동 설치가 필요할 수 있습니다. Windows VM 에이전트를 수동으로 설치하려면 [VM 에이전트 설치 관리자를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=394789).

VM 에이전트는 Windows 설치 관리자 파일을 두 번 클릭하여 설치할 수 있습니다. VM 에이전트를 자동 또는 무인으로 설치하려면 다음 명령을 실행합니다.

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>VM 에이전트 검색

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 모듈을 사용하여 Azure VM에 대한 정보를 검색할 수 있습니다. Azure VM 에이전트의 프로비전 상태와 같이 VM에 대한 정보를 보려면 [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm)을 사용합니다.

```powershell
Get-AzVM
```

다음의 요약 예제 출력에서는 *OSProfile* 내부에 중첩된 *ProvisionVMAgent* 속성을 보여줍니다. 이 속성을 사용하여 VM 에이전트가 VM에 배포되었는지 여부를 확인할 수 있습니다.

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

다음 스크립트는 VM 이름의 간단한 목록과 VM 에이전트의 상태를 반환하는 데 사용할 수 있습니다.

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>수동 검색

Microsoft VM에 로그인하면 작업 관리자를 사용하여 실행 중인 프로세스를 검사할 수 있습니다. Azure VM 에이전트를 확인하려면 작업 관리자를 열고 *세부 정보* 탭을 클릭하고 **WindowsAzureGuestAgent.exe**라는 프로세스 이름을 찾습니다. 이 프로세스가 있으면 VM 에이전트가 설치되어 있는 것입니다.


## <a name="upgrade-the-vm-agent"></a>VM 에이전트 업그레이드
Windows용 Azure VM 에이전트는 자동으로 업그레이드됩니다. 새 VM이 Azure에 배포되면 VM 프로비전 시 최신 VM 에이전트가 제공됩니다. 사용자 지정 VM 이미지는 이미지를 만들 때 새 VM 에이전트를 포함하도록 수동으로 업데이트해야 합니다.


## <a name="next-steps"></a>다음 단계
VM 확장에 대한 자세한 내용은 [Azure 가상 머신 확장 및 기능 개요](overview.md)를 참조하세요.
