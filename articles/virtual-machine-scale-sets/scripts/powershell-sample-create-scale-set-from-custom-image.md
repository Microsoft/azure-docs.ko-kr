---
title: Azure PowerShell 샘플 - 사용자 지정 VM 이미지 사용
description: 이 스크립트는 사용자 지정 VM 이미지를 VM 인스턴스의 원본으로 사용하는 가상 머신 확장 집합을 만듭니다.
author: axayjo
ms.author: akjosh
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh, devx-track-azurepowershell
ms.openlocfilehash: 34a786c133d38e40cfa614ddceed53a6cf52b617
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078931"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-powershell"></a>PowerShell을 사용하여 사용자 지정 VM 이미지에서 가상 머신 확장 집합 만들기
이 스크립트는 사용자 지정 VM 이미지를 VM 인스턴스의 원본으로 사용하는 가상 머신 확장 집합을 만듭니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.ps1 "Create a virtual machine scale set with a custom VM image")]

## <a name="clean-up-deployment"></a>배포 정리
다음 명령을 실행하여 리소스 그룹, 확장 집합 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명
이 스크립트는 다음 명령을 사용하여 배포합니다. 테이블에 있는 각 항목은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | 가상 네트워크, 부하 분산 장치 및 NAT 규칙을 포함하여 가상 머신 확장 집합 및 모든 지원 리소스를 만듭니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.
