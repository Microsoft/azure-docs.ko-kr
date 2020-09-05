---
title: 'PowerShell 스크립트: Azure Lab Services에서 허용되는 VM 크기 설정 | Microsoft Docs'
description: 이 문서에는 Azure Lab Services에서 허용 되는 VM (가상 머신) 크기를 설정 하는 샘플 PowerShell 스크립트가 포함 되어 있습니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136153"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell을 사용하여 Azure Lab Services에서 허용되는 VM 크기 설정

이 샘플 PowerShell 스크립트는 Azure Lab Services에서 허용되는 VM(가상 머신) 크기를 설정합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>필수 조건
* **랩**. 스크립트를 사용하려면 기존 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| AzResource | 지정된 매개 변수를 기반으로 리소스를 검색합니다. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 리소스를 가져옵니다. |
| [집합 AzResource](/powershell/module/az.resources/set-azresource) | 리소스를 수정합니다. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | 리소스를 만듭니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
