---
title: PowerShell 샘플 - 다른 지역의 스토리지 계정으로 스냅샷을 VHD로 내보내기/복사
description: Azure PowerShell 스크립트 샘플 - 다른 지역의 스토리지 계정으로 스냅샷을 VHD로 내보내기/복사
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 389c256e951c8a97f0233e5082ea13cc1573302d
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320378"
---
# <a name="exportcopy-managed-snapshots-as-vhd-to-a-storage-account-in-different-region-with-powershell"></a>PowerShell을 사용하여 다른 지역의 스토리지 계정으로 관리 스냅샷을 VHD로 내보내기/복사

이 스크립트는 다른 지역의 스토리지 계정으로 관리 스냅샷을 내보냅니다. 먼저 스냅샷의 SAS URI를 생성한 다음, 이를 사용하여 다른 지역의 스토리지 계정으로 스냅샷을 복사합니다. 이 스크립트를 사용하여 재해 복구를 위해 다른 지역에서 관리 디스크의 백업을 유지 관리합니다.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-storage-account/copy-snapshot-to-storage-account.ps1 "Copy snapshot")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 관리 스냅샷의 SAS URI를 생성하고 SAS URI를 사용하여 스토리지 계정에 스냅샷을 복사합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Grant-AzSnapshotAccess](/powershell/module/az.compute/new-azdisk) | 스토리지 계정에 스냅샷을 복사하는 데 사용되는 스냅샷의 SAS URI를 생성합니다. |
| [New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) | 계정 이름과 키를 사용하여 스토리지 계정 컨텍스트를 만듭니다. 이 컨텍스트는 스토리지 계정에 대한 읽기/쓰기 작업을 수행하는 데 사용할 수 있습니다. |
| [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) | 스냅샷의 기본 VHD를 스토리지 계정에 복사합니다. |

## <a name="next-steps"></a>다음 단계

[VHD에서 관리 디스크 만들기](virtual-machines-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[관리 디스크에서 가상 머신 만들기](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Linux VM 설명서](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
