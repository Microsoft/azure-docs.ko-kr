---
title: Linux VM 사용자 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스
description: Linux VM에서 사용자 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스하는 프로세스를 설명하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: barclayn
ROBOTS: NOINDEX,NOFOLLOW
ms.collection: M365-identity-device-management
ms.openlocfilehash: c27480f29a29f4805f8a9cafcfd388cb0638519e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269321"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-linux-vm-to-access-azure-resource-manager"></a>자습서: Linux VM에서 사용자 할당 관리 ID를 사용하여 Azure Resource Manager에 액세스

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

이 자습서에서는 사용자 할당 관리 ID를 만들어서, Linux VM(가상 머신)에 할당한 다음, 이 ID를 사용하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다. Azure 리소스에 대한 관리 ID는 Azure에서 자동으로 관리됩니다. 그러면 코드에 자격 증명을 포함할 필요 없이 Azure AD 인증을 지원하는 서비스에 인증할 수 있습니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 사용자 할당 관리 ID 만들기
> * 사용자 할당 관리 ID를 Linux VM에 할당 
> * 사용자 할당 관리 ID에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 
> * 사용자 할당 관리 ID를 사용하여 액세스 토큰을 가져와서 Azure Resource Manager를 호출하는 데 사용 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Azure Portal에 로그인](https://portal.azure.com)

- [Linux 가상 머신 만들기](../../virtual-machines/linux/quick-create-portal.md)

- CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 2.0 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

1. Azure Cloud Shell 세션 대신 CLI 콘솔을 사용하는 경우 먼저 Azure에 로그인합니다. 새 사용자 할당 관리 ID를 만들려는 Azure 구독과 연결된 계정을 사용하세요.

    ```azurecli
    az login
    ```

2. [az identity create](/cli/azure/identity#az-identity-create)를 사용하여 사용자 할당 관리 ID를 만듭니다. `-g` 매개 변수는 사용자 할당 관리 ID가 만들어진 리소스 그룹을 지정하고 `-n` 매개 변수는 그 이름을 지정합니다. `<RESOURCE GROUP>` 및 `<UAMI NAME>` 매개 변수 값을 원하는 값으로 바꾸세요.
    
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <UAMI NAME>
```

응답에는 다음 예제와 같이 생성된 사용자 할당 관리 ID에 대한 세부 정보가 포함됩니다. 다음 단계에서 사용되므로 사용자 할당 관리 ID의 `id` 값을 적어둡니다.

```json
{
"clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
"clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>/credentials?tid=5678&oid=9012&aid=12344643-8088-4d70-9532-c3a0fdc190fz",
"id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>",
"location": "westcentralus",
"name": "<UAMI NAME>",
"principalId": "9012",
"resourceGroup": "<RESOURCE GROUP>",
"tags": {},
"tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
"type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

## <a name="assign-a-user-assigned-managed-identity-to-your-linux-vm"></a>Linux VM에 사용자 할당 관리 ID 할당

사용자 할당 관리 ID는 여러 Azure 리소스에 있는 클라이언트에 사용될 수 있습니다. 다음 명령을 사용하여 사용자 할당 관리 ID를 단일 VM에 할당합니다. 이전 단계에서 반환된 `Id` 속성을 `-IdentityID` 매개 변수에 사용합니다.

[az vm identity assign](/cli/azure/vm)을 사용하여 Linux VM에 사용자 할당 관리 ID를 할당합니다. `<RESOURCE GROUP>` 및 `<VM NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. 이전 단계에서 반환된 `id` 속성을 `--identities` 매개 변수 값에 사용합니다.

```azurecli-interactive
az vm identity assign -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAMI NAME>"
```

## <a name="grant-your-user-assigned-managed-identity-access-to-a-resource-group-in-azure-resource-manager"></a>사용자 할당 관리 ID에 Azure Resource Manager의 리소스 그룹 액세스 권한 부여 

Azure 리소스에 대한 관리 ID는 Azure AD 인증을 지원하는 리소스 API를 인증하는 액세스 토큰을 요청하기 위해 코드에 사용할 수 있는 ID를 제공합니다. 이 자습서에서 코드는 Azure Resource Manager API에 액세스합니다.  

코드가 API에 액세스할 수 있으려면 그 전에 Azure Resource Manager의 리소스에 ID 액세스 권한을 부여해야 합니다. 이 경우에는 VM이 포함된 리소스 그룹입니다. `<SUBSCRIPTION ID>` 및 `<RESOURCE GROUP>`의 값을 환경에 적합하게 업데이트합니다. 또한 `<UAMI PRINCIPALID>`를 [사용자 할당 관리 ID 만들기](#create-a-user-assigned-managed-identity)의 `az identity create` 명령에서 반환한 `principalId` 속성으로 바꿉니다.

```azurecli-interactive
az role assignment create --assignee <UAMI PRINCIPALID> --role 'Reader' --scope "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP> "
```

응답에는 다음 예제와 같이 만든 역할 할당에 대한 세부 정보가 포함됩니다.

```json
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Authorization/roleAssignments/b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "name": "b402bd74-157f-425c-bf7d-zed3a3a581ll",
  "properties": {
    "principalId": "f5fdfdc1-ed84-4d48-8551-999fb9dedfbl",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}

```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>VM의 ID를 사용하여 액세스 토큰을 가져온 다음 Resource Manager를 호출하는 데 사용 

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. 

1. Azure [Portal](https://portal.azure.com)에 로그인합니다.
2. 포털에서 **Virtual Machines** -> Linux 가상 머신으로 이동한 다음, **개요**에서 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다.
3. 선택한 SSH 클라이언트를 사용하여 VM에 연결합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/about)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](~/articles/virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](~/articles/virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.
4. 터미널 창에서 CURL을 사용하여 Azure Instance Metadata Service(IMDS) ID 엔드포인트에 대한 요청을 수행해 Azure Resource Manager용 액세스 토큰을 가져옵니다.  

   다음 예제에는 액세스 토큰을 획득하는 CURL 요청이 나와 있습니다. `<CLIENT ID>`를 [사용자 할당 관리 ID 만들기](#create-a-user-assigned-managed-identity)의 `az identity create` 명령에서 반환한 `clientId` 속성으로 바꿉니다. 
    
   ```bash
   curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<UAMI CLIENT ID>"   
   ```
    
    > [!NOTE]
    > `resource` 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. 리소스 관리자 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다. 
    
    응답에는 Azure Resource Manager에 액세스하는 데 필요한 액세스 토큰이 포함되어 있습니다. 
    
    응답 예제:  

    ```bash
    {
    "access_token":"eyJ0eXAiOi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"
    } 
    ```

5. 액세스 토큰을 사용하여 Azure Resource Manager에 액세스하고, 이전에 사용자 할당 관리 ID 액세스 권한을 부여했던 리소스 그룹의 속성을 읽습니다. `<SUBSCRIPTION ID>` 및 `<RESOURCE GROUP>`을 앞에서 지정한 값으로 바꾸고, `<ACCESS TOKEN>`을 이전 단계에서 반환된 토큰으로 바꿔야 합니다.

    > [!NOTE]
    > URL은 대/소문자를 구분하므로 앞서 리소스 그룹의 이름을 지정할 때 사용했던 것과 정확히 동일한 대/소문자 및 `resourceGroups`의 대문자 "G"를 사용해야 합니다.  

    ```bash 
    curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>?api-version=2016-09-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    응답은 다음 예제와 비슷하게 특정 리소스 그룹 정보를 포함합니다. 

    ```bash
    {
    "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/DevTest",
    "name":"DevTest",
    "location":"westus",
    "properties":{"provisioningState":"Succeeded"}
    } 
    ```
    
## <a name="next-steps"></a>다음 단계

이 자습서에서는 사용자 할당 관리 ID를 만들고 이를 Linux 가상 머신에 연결하여 Azure Resource Manager API에 액세스하는 방법을 설명합니다.  Azure Resource Manager에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure 리소스 관리자](../../azure-resource-manager/management/overview.md)