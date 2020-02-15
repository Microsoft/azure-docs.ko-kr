---
title: 세션 호스트 가상 컴퓨터에 Windows 라이선스 적용-Azure
description: Windows 가상 데스크톱 Vm에 대 한 Windows 라이선스를 적용 하는 방법을 설명 합니다.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201478"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>세션 호스트 가상 컴퓨터에 Windows 라이선스 적용

Windows 가상 데스크톱 워크 로드를 실행 하는 데 적절 한 라이선스가 있는 고객은 해당 세션 호스트 가상 컴퓨터에 Windows 라이선스를 적용 하 고 다른 라이선스를 지불 하지 않고 실행할 수 있습니다. 자세한 내용은 [Windows 가상 데스크톱 가격 책정](https://azure.microsoft.com/pricing/details/virtual-desktop/)을 참조 하세요.

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Windows 가상 데스크톱 라이선스를 사용 하는 방법
Windows 가상 데스크톱 라이선스를 사용 하면 호스트 풀에서 세션 호스트로 등록 된 Windows 또는 Windows Server 가상 컴퓨터에 라이선스를 적용 하 고 사용자 연결을 받을 수 있습니다. 이 라이선스는 파일 공유 서버, 도메인 컨트롤러 등으로 실행 되는 가상 컴퓨터에는 적용 되지 않습니다.

Windows 가상 데스크톱 라이선스를 사용 하는 몇 가지 방법이 있습니다.
- [Azure Marketplace 제공](./create-host-pools-azure-marketplace.md)을 사용 하 여 호스트 풀 및 해당 세션 호스트 가상 컴퓨터를 만들 수 있습니다. 이러한 방식으로 만든 가상 머신은 라이선스를 자동으로 적용 합니다.
- [GitHub Azure Resource Manager 템플릿을](./create-host-pools-arm-template.md)사용 하 여 호스트 풀 및 해당 세션 호스트 가상 컴퓨터를 만들 수 있습니다. 이러한 방식으로 만든 가상 머신은 라이선스를 자동으로 적용 합니다.
- 기존 세션 호스트 가상 머신에 라이선스를 적용할 수 있습니다. 이렇게 하려면 먼저 [PowerShell을 사용 하 여 호스트 풀 만들기](./create-host-pools-powershell.md) 의 지침에 따라 호스트 풀 및 연결 된 vm을 만든 다음이 문서로 돌아와서 라이선스를 적용 하는 방법을 알아보세요.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>세션 호스트 VM에 Windows 라이선스 적용
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azure/overview)했는지 확인합니다. 다음 PowerShell cmdlet을 실행 하 여 Windows 라이선스를 적용 합니다.

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>세션 호스트 VM이 라이선스 혜택을 활용 하는지 확인
VM을 배포한 후 라이선스 유형 확인에서이 cmdlet을 실행 합니다.
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

적용 된 Windows 라이선스가 있는 세션 호스트 VM은 다음과 같이 표시 됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

적용 된 Windows 라이선스가 없는 Vm에는 다음과 같은 내용이 표시 됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

다음 cmdlet을 실행 하 여 Azure 구독에 Windows 라이선스가 적용 된 모든 세션 호스트 Vm 목록을 확인 합니다.

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
