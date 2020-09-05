---
title: PowerShell-Azure Lab Services의 VHD 파일에서 사용자 지정 이미지 만들기
description: 이 PowerShell 스크립트는 Azure Lab Services에서 VHD 파일을 사용하여 사용자 지정 이미지를 만듭니다.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: d22e6e1d226e826bf114a0fdb378879b828d9b4a
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136187"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>PowerShell을 사용하여 Azure Lab Services의 VHD 파일에서 사용자 지정 이미지 만들기

이 샘플 PowerShell 스크립트는 Azure Lab Services에서 VHD 파일을 사용하여 사용자 지정 이미지를 만듭니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>필수 조건
* **랩**. 스크립트를 사용하려면 기존 랩이 있어야 합니다. 

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 

| 명령 | 메모 |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 리소스를 가져옵니다. |
| [AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Azure Storage 계정의 선택키를 가져옵니다. |
| [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) | 리소스 그룹에 Azure 배포를 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/)를 참조하세요.

추가 Azure Lab Services PowerShell 스크립트 샘플은 [Azure Lab Services PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
