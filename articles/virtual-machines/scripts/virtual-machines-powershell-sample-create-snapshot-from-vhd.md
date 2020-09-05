---
title: 동일한 관리 디스크를 여러 개 만들기 위한 VHD 스냅샷(Windows) - PowerShell
description: Azure PowerShell 스크립트 샘플 - VHD에서 스냅샷을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기
documentationcenter: storage
author: ramankumarlive
manager: kavithag
ms.service: virtual-machines
ms.subservice: disks
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 47ade94ad2e0be0ce44d346336c6c7eea4fca78b
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322790"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell-windows"></a>PowerShell(Windows)을 사용하여 VHD에서 스냅샷을 만들어 약간의 시간 동안 같은 관리 디스크 여러 개 만들기

이 스크립트는 같은 구독 또는 다른 구독의 스토리지 계정에 VHD 파일의 스냅샷을 만듭니다. 이 스크립트를 사용하여 특수화된(일반화되지 않은/sysprep을 실행하지 않은) VHD를 스냅샷으로 가져온 다음, 스냅샷을 사용하여 약간의 시간 동안 같은 관리 디스크를 여러 개 만듭니다. 또한 이 스크립트를 사용하여 데이터 VHD를 스냅샷으로 가져온 다음, 스냅샷을 사용하여 약간의 시간 동안 관리 디스크를 여러 개 만듭니다. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]


## <a name="next-steps"></a>다음 단계

[스냅샷에서 관리 디스크 만들기](virtual-machines-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[관리 디스크를 OS 디스크로 연결하여 가상 머신 만들기](./virtual-machines-powershell-sample-create-vm-from-managed-os-disks.md)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
