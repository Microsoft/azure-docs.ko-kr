---
title: Azure AD 자격 증명을 사용 하 여 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스
titleSuffix: Azure Storage
description: PowerShell은 Azure AD 자격 증명으로 로그인 하 여 Azure Storage blob 및 큐 데이터에 대해 명령을 실행할 수 있도록 지원 합니다. 세션에 액세스 토큰이 제공되고 호출 작업에 권한을 부여하는 데 사용됩니다. 사용 권한은 Azure AD 보안 주체에 할당 된 Azure 역할에 따라 달라 집니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 75ca39ad00966928bb7887cb14255470b17579dd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589149"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Azure AD 자격 증명을 사용 하 여 PowerShell 명령을 실행 하 여 blob 또는 큐 데이터에 액세스

Azure Storage는 Azure AD (Azure Active Directory) 자격 증명을 사용 하 여 로그인 하 고 스크립팅 명령을 실행할 수 있는 PowerShell에 대 한 확장을 제공 합니다. Azure AD 자격 증명을 사용 하 여 PowerShell에 로그인 하면 OAuth 2.0 액세스 토큰이 반환 됩니다. 이 토큰은 PowerShell에서 Blob 또는 큐 저장소에 대 한 후속 데이터 작업에 권한을 부여 하는 데 자동으로 사용 됩니다. 지원되는 작업의 경우, 더 이상 명령과 함께 계정 키 또는 SAS 토큰을 전달할 필요가 없습니다.

RBAC (역할 기반 액세스 제어)를 통해 blob에 사용 권한을 할당 하 고 Azure AD 보안 주체에 데이터를 큐에 할당할 수 있습니다. Azure Storage의 Azure 역할에 대 한 자세한 내용은 [RBAC를 사용 하 여 데이터 Azure Storage에 대 한 액세스 권한 관리](storage-auth-aad-rbac.md)를 참조 하세요.

## <a name="supported-operations"></a>지원되는 작업

Azure Storage 확장은 blob 및 큐 데이터의 작업에 대해 지원 됩니다. 호출할 수 있는 작업은 PowerShell에 로그인 하는 데 사용 되는 Azure AD 보안 주체에 부여 된 권한에 따라 달라 집니다. Azure Storage 컨테이너 또는 큐에 대 한 사용 권한은 RBAC를 통해 할당 됩니다. 예를 들어 **Blob 데이터 판독기** 역할이 할당 된 경우 컨테이너 또는 큐에서 데이터를 읽는 스크립팅 명령을 실행할 수 있습니다. **Blob 데이터 참가자** 역할이 할당 된 경우 컨테이너 또는 큐 또는 포함 된 데이터를 읽거나, 쓰고, 삭제 하는 스크립팅 명령을 실행할 수 있습니다.

컨테이너 또는 큐에서 각 Azure Storage 작업에 필요한 사용 권한에 대 한 자세한 내용은 [OAuth 토큰을 사용 하 여 저장소 작업 호출](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)을 참조 하세요.  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Azure AD 자격 증명을 사용 하 여 PowerShell 명령 호출

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure PowerShell를 사용 하 여 로그인 하 고 Azure AD 자격 증명을 사용 하 여 Azure Storage에 대해 후속 작업을 실행 하려면 저장소 계정을 참조 하는 저장소 컨텍스트를 만들고 `-UseConnectedAccount` 매개 변수를 포함 합니다.

다음 예제에서는 Azure AD 자격 증명을 사용 하 여 Azure PowerShell에서 새 저장소 계정으로 컨테이너를 만드는 방법을 보여 줍니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

1. [AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용 하 여 Azure 계정에 로그인 합니다.

    ```powershell
    Connect-AzAccount
    ```

    PowerShell을 사용 하 여 Azure에 로그인 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 사용 하 여 로그인](/powershell/azure/authenticate-azureps)을 참조 하세요.

1. [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 호출 하 여 Azure 리소스 그룹을 만듭니다. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. [AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)를 호출 하 여 저장소 계정을 만듭니다.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. [AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 호출 하 여 새 저장소 계정을 지정 하는 저장소 계정 컨텍스트를 가져옵니다. 저장소 계정에서 동작 하는 경우 자격 증명을 반복 해 서 전달 하는 대신 컨텍스트를 참조할 수 있습니다. `-UseConnectedAccount`AZURE AD 자격 증명을 사용 하 여 후속 데이터 작업을 호출 하는 매개 변수를 포함 합니다.

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. 컨테이너를 만들기 전에 [Storage Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자 인 경우에도 저장소 계정에 대해 데이터 작업을 수행 하려면 명시적 권한이 필요 합니다. Azure 역할 할당에 대 한 자세한 내용은 [Azure Portal에서 azure blob에 대 한 액세스 권한 부여 및 RBAC를 사용 하 여 데이터 큐](storage-auth-aad-rbac.md)지정을 참조 하세요.

    > [!IMPORTANT]
    > Azure 역할 할당을 전파 하는 데 몇 분 정도 걸릴 수 있습니다.

1. [AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)를 호출 하 여 컨테이너를 만듭니다. 이 호출에서 이전 단계에서 만든 컨텍스트를 사용 하므로 컨테이너는 Azure AD 자격 증명을 사용 하 여 만들어집니다.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>다음 단계

- [PowerShell을 사용 하 여 blob 및 큐 데이터에 액세스 하기 위한 Azure 역할 할당](storage-auth-aad-rbac-powershell.md)
- [Azure 리소스에 대 한 관리 id를 사용 하 여 blob 및 큐 데이터에 대 한 액세스 권한 부여](storage-auth-aad-msi.md)
