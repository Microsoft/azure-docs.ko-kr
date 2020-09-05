---
title: 자습서`:` 관리 ID를 사용하여 Azure Resource Manager에 액세스 - Windows - Azure AD
description: Windows VM에서 사용자 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 설명하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 030f2b893cd429bfdb451d24e799689fdb8a3cf8
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255701"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>자습서: Windows VM에서 사용자 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 사용자 할당 ID를 만들어서, Windows VM(가상 머신)에 할당한 다음, 이 ID를 사용하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다. 관리 서비스 ID는 Azure에서 자동으로 관리됩니다. 그러면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용자 할당 관리 ID 만들기
> * 사용자 할당 ID를 Windows VM에 할당
> * 사용자 할당 ID에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * 사용자 할당 ID를 사용하여 액세스 토큰을 가져와서 Azure Resource Manager를 호출하는 데 사용 
> * Resource Group의 속성 읽기

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Azure Portal에 로그인](https://portal.azure.com)

- [Windows 가상 머신 만들기](../../virtual-machines/windows/quick-create-portal.md)

- 이 자습서에서 필요한 리소스 만들기 및 역할 관리 단계를 수행하려면 적절한 범위(사용자 구독 또는 리소스 그룹)에서 계정에 “소유자” 권한이 필요합니다. 역할 할당에 관한 도움이 필요한 경우 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.
- [최신 버전의 Azure PowerShell 모듈을 설치합니다.](/powershell/azure/install-az-ps) 
- `Connect-AzAccount`를 실행하여 Azure와 연결합니다.
- [PowerShellGet 최신 버전](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)을 설치합니다.
- `Install-Module -Name PowerShellGet -AllowPrerelease` 명령을 실행하여 `PowerShellGet` 모듈의 시험판 버전을 가져옵니다. 이 명령을 실행한 후 `Az.ManagedServiceIdentity` 모듈을 설치하기 위해 현재 PowerShell 세션에서 `Exit`해야 할 수도 있습니다.
- `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` 명령을 실행하여 `Az.ManagedServiceIdentity` 모듈의 시험판 버전을 설치하고 이 문서의 사용자 할당 ID 작업을 수행합니다.


## <a name="enable"></a>사용

사용자 할당 ID를 기반으로 하는 시나리오의 경우 다음 단계를 수행해야 합니다.

- ID 만들기
 
- 새로 만든 ID 할당

### <a name="create-identity"></a>ID 만들기

이 섹션에서는 사용자 할당 ID를 만드는 방법을 보여줍니다. 사용자 할당 ID는 독립 실행형 Azure 리소스로 생성됩니다. [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity)를 사용하면 하나 이상의 Azure 서비스 인스턴스를 할당할 수 있는 Azure AD 테넌트에 ID가 만들어집니다.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

응답에는 다음 예제와 같이 생성된 사용자 할당 ID에 대한 세부 정보가 포함됩니다. 사용자 할당 ID의 `Id` 및 `ClientId` 값을 적어 둡니다. 후속 단계에서 사용됩니다.

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

### <a name="assign-identity"></a>ID 할당

이 섹션에서는 사용자 할당 ID를 Windows VM에 할당하는 방법을 보여줍니다. 사용자 할당 ID는 여러 Azure 리소스에 있는 클라이언트에 사용될 수 있습니다. 다음 명령을 사용하여 사용자 할당 ID를 단일 VM에 할당합니다. 이전 단계에서 반환된 `Id` 속성을 `-IdentityID` 매개 변수에 사용합니다.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>액세스 권한 부여 

이 섹션에서는 Azure Resource Manager의 리소스 그룹에 사용자 할당 ID 액세스 권한을 부여하는 방법을 보여줍니다. Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 리소스 API를 인증하는 액세스 토큰을 요청하기 위해 코드에 사용할 수 있는 ID를 제공합니다. 이 자습서에서 코드는 Azure Resource Manager API에 액세스합니다. 

코드가 API에 액세스할 수 있으려면 그 전에 Azure Resource Manager의 리소스에 ID 액세스 권한을 부여해야 합니다. 이 경우에는 VM이 포함된 리소스 그룹입니다. `<SUBSCRIPTION ID>`의 값을 환경에 적합하게 업데이트합니다.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

응답에는 다음 예제와 같이 만든 역할 할당에 대한 세부 정보가 포함됩니다.

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="access-data"></a>데이터 액세스

### <a name="get-an-access-token"></a>액세스 토큰 가져오기 

자습서의 나머지 부분에서는 앞서 만든 VM에서 작업합니다.

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

2. Portal에서 **Virtual Machines** -> Windows 가상 머신으로 이동한 다음, **개요**에서 **연결**을 클릭합니다.

3. Windows VM을 만들 때 사용한 **사용자 이름**과 **암호**를 입력합니다.

4. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다.

5. PowerShell의 `Invoke-WebRequest`를 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져오도록 Azure 리소스 엔드포인트의 로컬 관리 ID에 요청합니다.  `client_id` 값은 사용자 할당 관리 ID를 만들 때 반환되는 값입니다.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>속성 읽기

이전 단계에서 검색한 액세스 토큰을 사용하여 Azure Resource Manager에 액세스하고, 사용자 할당 ID 액세스 권한을 부여한 리소스 그룹의 속성을 읽습니다. `<SUBSCRIPTION ID>`를 환경의 구독 ID로 바꿉니다.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
응답은 다음 예제와 비슷하게 특정 리소스 그룹 정보를 포함합니다.

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 할당 ID를 만들고 이를 Azure Virtual Machine에 연결하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다.  Azure Resource Manager에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure 리소스 관리자](../../azure-resource-manager/management/overview.md)