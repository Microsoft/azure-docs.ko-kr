---
title: PowerShell을 사용 하 여 리소스에 관리 id 액세스 할당-Azure AD
description: PowerShell을 사용 하 여 한 리소스에 관리 되는 id를 할당 하 고 다른 리소스에 액세스 하는 방법에 대 한 단계별 지침입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ae2da130f61d31db4904ed2dd5ac18444929950
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177502"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell을 사용하여 리소스에 관리 ID 액세스 권한 할당

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

관리 ID를 사용하여 Azure 리소스를 구성하면 모든 보안 주체와 마찬가지로 다른 리소스에 관리 ID 액세스 권한을 제공할 수 있습니다. 이 예제에서는 PowerShell을 사용하여 Azure 가상 머신의 관리 ID 액세스 권한을 Azure Storage 계정에 제공하는 방법을 보여 줍니다.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

- Azure 리소스에 대한 관리 ID를 잘 모르는 경우 [개요 섹션](overview.md)을 확인하세요. **[시스템 할당 ID와 사용자 할당 관리 ID의 차이점](overview.md#managed-identity-types)을 반드시 검토하세요**.
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- 아직 설치하지 않은 경우 [Azure PowerShell 최신 버전](/powershell/azure/install-az-ps)을 설치합니다.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Azure RBAC를 사용 하 여 다른 리소스에 관리 되는 id 액세스 권한 부여

[Azure VM과 같은](qs-configure-powershell-windows-vm.md) Azure 리소스에서 관리 ID를 사용하도록 설정한 후 다음을 수행합니다.

1. `Connect-AzAccount` cmdlet을 사용하여 Azure에 로그인합니다. 관리 ID를 구성한 Azure 구독과 연결된 계정을 사용하세요.

   ```powershell
   Connect-AzAccount
   ```
2. 이 예제에서는 Azure VM에 스토리지 계정 액세스 권한을 제공합니다. 먼저 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용하여 관리 ID를 사용하도록 설정할 때 만든 `myVM`이라는 VM의 서비스 주체를 가져옵니다. 그런 다음, [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment)를 사용하여 VM **Reader** 액세스 권한을 `myStorageAcct`라는 스토리지 계정에 제공합니다.

    ```powershell
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID 개요](overview.md)
- Azure 가상 머신에서 관리 ID를 사용하려면 [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-powershell-windows-vm.md)을 참조하세요.
