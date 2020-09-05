---
title: 스냅샷에서 VM 만들기(Linux) - PowerShell 샘플
description: Azure PowerShell 스크립트 샘플 - 스냅샷에서 VM 만들기
services: virtual-machines-linux
documentationcenter: virtual-machines
author: ramankumarlive
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: ramankum
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 42c9903b783df4b3392b6aab3a69bbb66da90a75
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078166"
---
# <a name="create-a-virtual-machine-from-a-snapshot-with-powershell-linux"></a>PowerShell를 사용하여 스냅샷에서 가상 컴퓨터 만들기(Linux)

이 스크립트는 OS 디스크의 스냅샷에서 가상 머신을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-from-snapshot/create-vm-from-snapshot.ps1 "Create VM from managed os disk")]

## <a name="clean-up-deployment"></a>배포 정리

다음 명령을 실행하여 리소스 그룹, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 스냅샷 속성을 가져오고 스냅샷에서 관리 디스크를 만들며 VM을 만듭니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) | 스냅샷 이름을 사용하여 스냅샷을 가져옵니다. |
| [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) | 디스크 구성을 만듭니다. 이 구성은 디스크 만들기 프로세스에서 사용됩니다. |
| [New-AzDisk](/powershell/module/az.compute/new-azdisk) | 관리 디스크를 만듭니다. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | VM 구성을 만듭니다. 이 구성은 VM 이름, 운영 체제 및 관리자 자격 증명 등의 정보를 포함합니다. 이 구성은 VM을 만드는 중에 사용됩니다. |
| [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) | 관리 디스크를 가상 머신에 OS 디스크로 연결합니다. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | 공용 IP 주소를 만듭니다. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | 네트워크 인터페이스를 만듭니다. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | 가상 머신을 만듭니다. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
