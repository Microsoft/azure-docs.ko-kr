---
title: 구독에 대한 관리 디스크의 스냅샷(Windows) - PowerShell
description: Azure PowerShell 스크립트 샘플 - 관리 디스크의 스냅샷을 동일한 구독이나 다른 구독으로 복사(이동)
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: b5ac47c8074a3a7bec86a35075a98a141ea0ccd1
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051839"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell-windows"></a>PowerShell을 사용하여 같은 구독 또는 다른 구독에 관리 디스크의 스냅샷 복사(Windows)

이 스크립트는 관리 디스크의 스냅샷을 동일한 구독이나 다른 구독으로 복사합니다. 다음 시나리오에서 이 스크립트를 사용하세요.

1. Premium Storage(Premium_LRS)의 스냅샷을 Standard Storage(Standard_LRS 또는 Standard_ZRS)로 마이그레이션하여 비용을 절감합니다.
1. 로컬 중복 저장소(Premium_LRS, Standard_LRS)의 스냅샷을 영역 중복 저장소(Standard_ZRS)로 마이그레이션하여 ZRS 스토리지의 높은 안정성을 누립니다.
1. 더 오래 보존하기 위해 스냅샷을 같은 지역의 다른 구독으로 이동합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트에서는 다음 명령을 사용하여 원본 스냅샷의 ID를 사용하여 대상 구독에 스냅샷을 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) | 스냅샷 생성에 사용되는 스냅샷 구성을 만듭니다. 부모 스냅샷의 리소스 ID와 부모 스냅샷과 같은 위치를 포함합니다.  |
| [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) | 매개 변수로 전달된 스냅샷 구성, 스냅샷 이름 및 리소스 그룹 이름을 사용하여 스냅샷을 만듭니다. |

## <a name="next-steps"></a>다음 단계

[스냅샷에서 가상 머신 만들기](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

추가 가상 머신 PowerShell 스크립트 샘플은 [Azure Windows VM 설명서](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에서 확인할 수 있습니다.
