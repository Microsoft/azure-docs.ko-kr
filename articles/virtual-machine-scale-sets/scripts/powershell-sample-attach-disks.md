---
title: Azure PowerShell 샘플 - 데이터 디스크 연결 및 사용
description: 이 스크립트는 Azure 가상 머신 확장 집합을 만들고 PowerShell을 사용하여 데이터 디스크를 연결하고 준비합니다.
author: mimckitt
ms.author: mimckitt
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 06/25/2020
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurepowershell
ms.openlocfilehash: 2a2c93578d8cb4b40e504dd83e3f603bffcccaf6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069649"
---
# <a name="attach-and-use-data-disks-with-a-virtual-machine-scale-set-with-powershell"></a>PowerShell을 사용하여 가상 머신 확장 집합에 데이터 디스크를 연결하고 사용합니다
이 스크립트는 가상 머신 확장 집합을 만들고, 데이터 디스크를 연결하고 준비합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-data-disks/use-data-disks.ps1 "Create a virtual machine scale set with data disks")]

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
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | 가상 머신 확장 집합에 대한 정보를 가져옵니다. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | 사용자 지정 스크립트용 VM 확장을 추가하여 기본 웹 애플리케이션을 설치합니다. |
| [업데이트 AzVmss](/powershell/module/az.compute/update-azvmss) | VM 확장을 적용하기 위해 가상 머신 확장 집합 모델을 업데이트합니다. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 리소스 그룹 및 포함된 모든 리소스를 제거합니다. |

## <a name="next-steps"></a>다음 단계
Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.
