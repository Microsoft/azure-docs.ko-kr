---
title: Azure에서 Windows VM에 Symantec Endpoint Protection 설치 | Microsoft Docs
description: 클래식 배포 모델을 사용하여 새로운 또는 기존 Azure VM에 Symantec Endpoint Protection 보안 확장을 설치하고 구성하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: 65b52c88741e618e8048451370918b06db73a651
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617880"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Windows VM에서 Symantec Endpoint Protection을 설치하고 구성하는 방법
> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

이 문서에서는 Windows Server가 실행되는 기존 VM(가상 머신)에서 Symantec Endpoint Protection 클라이언트를 설치하고 구성하는 방법을 설명합니다. 이 전체 클라이언트는 바이러스 및 스파이웨어 보호, 방화벽, 침입 방지와 같은 서비스를 포함합니다. 이 클라이언트는 VM 에이전트를 사용하여 보안 확장 프로그램으로 설치됩니다.

온-프레미스 솔루션용 Symantec의 기존 구독이 있는 경우 Azure 가상 머신을 보호하는 데 사용할 수 있습니다. 아직 구독 고객이 아닌 경우에는 평가판 구독에 등록할 수 있습니다. 이 솔루션에 대한 자세한 내용은 [Microsoft Azure 플랫폼의 Symantec Endpoint Protection][Symantec](영문)을 참조하세요. 이 페이지에는 Symantec 고객을 위한 라이선스 정보 및 이 클라이언트를 설치하는 방법에 대한 링크도 제공되어 있습니다.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>기존 VM에 Symantec Endpoint Protection 설치
이 작업을 시작하려면 다음 조건을 충족해야 합니다.

* Azure PowerShell 모듈 버전 0.8.2 이상이 작업 컴퓨터에 설치되어 있어야 합니다. **Get-Module azure | format-table version** 명령을 사용하여 설치한 Azure PowerShell의 버전을 확인할 수 있습니다. 지침 및 최신 버전에 대한 링크를 보려면 [Azure PowerShell을 설치 및 구성하는 방법][PS]을 참조하세요. `Add-AzureAccount`를 사용하여 Azure 구독에 로그인합니다.
* Azure Virtual Machine에서 VM 에이전트를 실행해야 합니다.

먼저, VM 에이전트가 가상 머신에 이미 설치되어 있는지 확인합니다. 클라우드 서비스 이름과 가상 머신 이름을 입력하고 관리자 수준의 Azure PowerShell 명령 프롬프트에서 다음 명령을 실행합니다. < 및 > 문자를 포함하여 따옴표 안의 모든 항목을 바꿉니다.

> [!TIP]
> 클라우드 서비스 및 가상 머신 이름을 모르는 경우 **Get-AzureVM** 을 실행하여 현재 구독의 모든 가상 머신에 대해 이름을 나열합니다.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

**write-host** 명령에서 **True**가 표시되면 VM 에이전트가 설치되어 있는 것입니다. **False**가 표시되면 Azure 블로그 게시물 [VM 에이전트 및 확장 - 2부][Agent]에서 지침 및 다운로드 링크를 참조합니다.

VM 에이전트가 설치된 경우 이러한 명령을 실행하여 Symantec Endpoint Protection 에이전트를 설치합니다.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Symantec 보안 확장이 설치되고 최신 상태인지 확인하려면 다음을 수행합니다.

1. 가상 머신에 로그온합니다. 지침은 [Windows Server를 실행하는 Virtual Machine에 로그온하는 방법][Logon]을 참조하세요.
2. Windows Server 2008 R2의 경우, **시작 > Symantec Endpoint Protection**을 클릭합니다. Windows Server 2012 또는 Windows Server 2012 R2의 경우, 시작 화면에서 **Symantec**을 입력하고 **Symantec Endpoint Protection**을 클릭합니다.
3. **Status-Symantec Endpoint Protection** 창의 **상태** 탭에서 필요한 경우 업데이트를 적용하거나 다시 시작합니다.

## <a name="additional-resources"></a>추가 리소스
[Windows Server를 실행하는 Virtual Machine에 로그온하는 방법][Logon]

[Azure VM 확장 및 기능][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
