---
title: Azure Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리
description: 스토리지 계정 키는 Azure Key Vault와 Azure 스토리지 계정에 대한 키 기반 액세스를 원활하게 통합합니다.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 20418dbc7432000116dcbe73a64987d83f38419d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585069"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Key Vault 및 Azure CLI를 사용하여 스토리지 계정 키 관리

Azure 스토리지 계정은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키는 자동 생성되며 암호화 키가 아닌 암호 역할을 합니다. Key Vault는 스토리지 계정에서 스토리지 계정 키를 주기적으로 다시 생성하여 스토리지 계정 키를 관리하며, 스토리지 계정의 리소스에 대한 위임된 액세스가 가능하도록 공유 액세스 서명 토큰을 제공합니다.

Key Vault 관리형 스토리키 계정 키 기능을 사용하여 키를 나열(Azure 스토리지 계정과 동기화)하고, 주기적으로 키를 다시 생성(순환)할 수 있습니다. 스토리지 계정과 클래식 스토리지 계정의 키를 모두 관리할 수 있습니다.

관리형 스토리지 계정 키 기능을 사용할 때 다음 사항을 고려해야 합니다.

- 키 값은 호출자에게 응답으로 반환되지 않습니다.
- Key Vault에서만 스토리지 계정 키를 관리해야 합니다. 키를 직접 관리하거나 Key Vault 프로세스를 방해하지 마세요.
- 단일 Key Vault 개체에서만 스토리지 계정 키를 관리해야 합니다. 여러 개체에서 키를 관리하도록 허용하지 마세요.
- 사용자 계정을 사용하여 스토리지 계정을 관리하도록 Key Vault에 요청할 수 있지만, 서비스 주체를 사용하여 관리하도록 요청할 수는 없습니다.
- Key Vault만 사용하여 키를 다시 생성해야 합니다. 스토리지 계정 키를 수동으로 다시 생성하지 않습니다.

Microsoft의 클라우드 기반 ID 및 액세스 관리 서비스인 Azure AD(Azure Active Directory)와 Azure Storage를 통합하여 사용하는 것이 좋습니다. Azure AD 통합은 [Azure BLOB 및 큐](../../storage/common/storage-auth-aad.md)에 사용할 수 있으며, Azure Key Vault와 마찬가지로 Azure Storage에 대한 OAuth2 토큰 기반 액세스를 제공합니다.

Azure AD를 사용하면 스토리지 계정 자격 증명 대신 애플리케이션 또는 사용자 ID를 사용하여 클라이언트 애플리케이션을 인증할 수 있습니다. Azure에서 실행할 때 [Azure AD 관리 ID](/azure/active-directory/managed-identities-azure-resources/)를 사용할 수 있습니다. 관리 ID를 사용하면 클라이언트 인증이 필요 없는 것은 물론이고, 애플리케이션에 또는 애플리케이션을 통해 자격 증명을 저장할 필요가 없습니다.

Azure AD는 Key Vault에서도 지원되는 RBAC(역할 기반 액세스 제어)를 사용하여 권한 부여를 관리합니다.

## <a name="service-principal-application-id"></a>서비스 주체 애플리케이션 ID

Azure AD 테넌트는 등록된 각 애플리케이션에 [서비스 주체](/azure/active-directory/develop/developer-glossary#service-principal-object)를 제공합니다. 서비스 주체는 애플리케이션 ID 역할을 하며, RBAC를 통해 다른 Azure 리소스에 액세스하기 위해 권한 부여를 설정하는 동안 사용됩니다.

Key Vault는 모든 Azure AD 테넌트에서 미리 등록되는 Microsoft 애플리케이션입니다. Key Vault는 각 Azure 클라우드에서 동일한 애플리케이션 ID로 등록됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 공용 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>필수 구성 요소

이 가이드를 완료하려면 먼저 다음을 수행해야 합니다.

- [Azure CLI를 설치합니다](/cli/azure/install-azure-cli).
- [키 자격 증명 모음 만들기](quick-create-cli.md)
- [Azure Storage 계정 만들기](../../storage/common/storage-account-create.md?tabs=azure-cli) 스토리지 계정 이름에는 소문자와 숫자만 사용해야 합니다. 이름은 3~24자 사이여야 합니다.
      
## <a name="manage-storage-account-keys"></a>스토리지 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure CLI 세션을 인증합니다.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault에 스토리지 계정에 대한 액세스 권한 부여

Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) 명령을 사용하여 스토리지 계정에 대한 Key Vault 액세스를 제공합니다. 명령에 다음 매개 변수 값을 입력합니다.

- `--role`: "스토리지 계정 키 운영자 서비스 역할" Azure 역할을 전달합니다. 이 역할은 액세스 범위를 스토리지 계정으로 제한합니다. 클래식 스토리지 계정의 경우 이 역할 대신 "클래식 스토리지 계정 키 운영자 서비스 역할"을 전달합니다.
- `--assignee`: "https://vault.azure.net" 값을 전달합니다. 이 값은 Azure 퍼블릭 클라우드의 Key Vault URL입니다. (Azure Goverment 클라우드의 경우 '--asingee-object-id'를 대신 사용합니다. [서비스 주체 애플리케이션 ID](#service-principal-application-id)를 참조하세요.)
- `--scope`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 형식의 스토리지 계정 리소스 ID를 전달합니다. 구독 ID를 찾으려면 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용하고, 스토리지 계정 이름 및 스토리지 계정 리소스 그룹을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>관리 스토리지 계정에 사용자 계정 권한 부여

Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) cmdlet을 사용하여 Key Vault 액세스 정책을 업데이트하고 사용자 계정에 스토리지 계정 권한을 부여합니다.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

스토리지 계정에 대한 권한은 Azure Portal의 스토리지 계정 “액세스 정책” 페이지에서 제공되지 않습니다.
### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault 관리형 스토리지 계정 만들기

 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) 명령을 사용하여 Key Vault 관리형 스토리지 계정을 만듭니다. 다시 생성 기간을 90일로 설정합니다. 순환할 시간이 되면 Key Vault는 키를 다시 생성한 다음(활성 상태가 아님), 활성 상태로 설정합니다. 한 번에 하나의 키만 SAS 토큰을 발급하는 데 사용되며, 이 키가 활성 키입니다. 명령에 다음 매개 변수 값을 입력합니다.

- `--vault-name`: 키 자격 증명 모음의 이름을 전달합니다. 키 자격 증명 모음의 이름을 찾으려면 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) 명령을 사용합니다.
- `-n`: 스토리지 계정의 이름을 전달합니다. 스토리지 계정의 이름을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.
- `--resource-id`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` 형식의 스토리지 계정 리소스 ID를 전달합니다. 구독 ID를 찾으려면 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용하고, 스토리지 계정 이름 및 스토리지 계정 리소스 그룹을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용합니다.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>공유 액세스 서명 토큰

Key Vault에 공유 액세스 서명 토큰을 생성하고 요청할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유하지 않고도 스토리지 계정의 리소스에 대한 액세스 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상시키지 않고 스토리지 리소스를 공유할 수 있는 안전한 방법을 제공합니다.

이 섹션의 명령은 다음 작업을 완료합니다.

- 계정 공유 액세스 서명 정의 `<YourSASDefinitionName>`을 설정합니다. 이 정의는 키 자격 증명 모음 `<YourKeyVaultName>`의 Key Vault 관리형 스토리지 계정 `<YourStorageAccountName>`에서 설정합니다.
- Blob, 파일, 테이블 및 큐 서비스에 대한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 종류 서비스, 컨테이너 및 개체에 대해 생성됩니다. 토큰은 모든 권한을 사용하여, https를 통해, 지정된 시작 및 종료 날짜를 사용하여 생성됩니다.
- 자격 증명 모음에서 Key Vault 관리형 스토리지 공유 액세스 서명 정의를 설정합니다. 정의에는 생성된 공유 액세스 서명 토큰의 템플릿 URI가 포함됩니다. 정의의 공유 액세스 서명 유형은 `account`이며 N일 동안 유효합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 비밀로 저장되었는지 확인합니다.

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure CLI [az storage account generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 명령을 사용하여 공유 액세스 서명 정의를 만듭니다. 이 작업에는 `storage` 및 `setsas` 권한이 필요합니다.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
작업이 성공적으로 실행되면 출력을 복사합니다.

```console
"se=2020-01-01&sp=***"
```

이 출력은 다음 단계에서 `--template-uri` 매개 변수에 전달됩니다.

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) 명령을 사용하여 이전 단계의 출력을 `--template-uri` 매개 변수에 전달하고, 공유 액세스 서명 정의를 만듭니다.  `-n` 매개 변수 이름을 원하는 대로 지정할 수 있습니다.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 및 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용하여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장되었는지 확인할 수 있습니다.

먼저 [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 명령을 사용하여 키 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

SAS 정의에 해당하는 비밀의 속성은 다음과 같습니다.

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

이제 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령 및 `id` 속성을 사용하여 해당 비밀의 내용을 볼 수 있습니다.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

이 명령의 출력은 SAS 정의 문자열을 `value`로 보여줍니다.


## <a name="next-steps"></a>다음 단계

- [키, 비밀 및 인증서](https://docs.microsoft.com/rest/api/keyvault/)에 대해 자세히 알아보세요.
- [Azure Key Vault 팀 블로그](https://blogs.technet.microsoft.com/kv/)의 문서를 검토하세요.
- [az keyvault 스토리지](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 참조 설명서를 참조하세요.
