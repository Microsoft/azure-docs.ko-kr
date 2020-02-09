---
title: Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키 관리
description: Storage 계정 키는 Azure storage 계정에 대 한 Azure Key Vault 및 키 기반 액세스 간의 원활한 통합을 제공 합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 880a85676ff7a0364431b33b90093298b12bffed
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980455"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키 관리

Azure storage 계정은 계정 이름과 키를 구성 하는 자격 증명을 사용 합니다. 키가 자동으로 생성 되 고 암호화 키가 아닌 암호로 사용 됩니다. Key Vault 저장소 계정 키를 [Key Vault 비밀](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)으로 저장 하 여 관리 합니다. 

Key Vault 관리 저장소 계정 키 기능을 사용 하 여 Azure storage 계정으로 키를 나열 (동기화) 하 고 키를 주기적으로 다시 생성 (회전) 할 수 있습니다. 저장소 계정 및 클래식 저장소 계정에 대 한 키를 관리할 수 있습니다.

관리 되는 저장소 계정 키 기능을 사용 하는 경우 다음 사항을 고려 하세요.

- 호출자에 대 한 응답으로 키 값이 반환 되지 않습니다.
- Key Vault만 저장소 계정 키를 관리 해야 합니다. 키를 직접 관리 하지 않고 Key Vault 프로세스를 방해 하지 않습니다.
- 단일 Key Vault 개체만 저장소 계정 키를 관리 해야 합니다. 여러 개체의 키 관리를 허용 하지 않습니다.
- 사용자 계정을 사용 하 여 저장소 계정을 관리 하는 Key Vault를 요청 하 고 서비스 사용자는 사용할 수 없습니다.
- Key Vault만 사용 하 여 키를 다시 생성 합니다. 저장소 계정 키를 수동으로 다시 생성 하지 마세요.

Microsoft의 클라우드 기반 id 및 액세스 관리 서비스인 Azure Active Directory (Azure AD)와 Azure Storage 통합 하는 것이 좋습니다. Azure AD 통합은 [azure blob 및 큐](../storage/common/storage-auth-aad.md)에서 사용할 수 있으며, Azure Storage에 대 한 OAuth2 토큰 기반 액세스를 제공 합니다 (Azure Key Vault와 마찬가지로).

Azure AD를 사용 하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 id를 사용 하 여 클라이언트 응용 프로그램을 인증할 수 있습니다. Azure에서 실행할 때 [AZURE AD 관리 id](/azure/active-directory/managed-identities-azure-resources/) 를 사용할 수 있습니다. 관리 되는 id는 클라이언트 인증에 대 한 필요성을 제거 하 고 또는 응용 프로그램에 자격 증명을 저장 합니다.

Azure AD는 RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 부여를 관리 합니다 .이는 Key Vault 에서도 지원 됩니다.

## <a name="service-principal-application-id"></a>서비스 사용자 응용 프로그램 ID

Azure AD 테넌트는 등록 된 각 응용 프로그램을 [서비스 사용자](/azure/active-directory/develop/developer-glossary#service-principal-object)에 게 제공 합니다. 서비스 주체는 RBAC를 통해 다른 Azure 리소스에 액세스 하기 위해 권한 부여 설정 중에 사용 되는 응용 프로그램 ID로 사용 됩니다.

Key Vault은 모든 Azure AD 테넌트에서 미리 등록 된 Microsoft 응용 프로그램입니다. Key Vault은 각 Azure 클라우드에서 동일한 응용 프로그램 ID로 등록 됩니다.

| 테넌트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure 퍼블릭 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>필수 조건

이 가이드를 완료 하려면 먼저 다음을 수행 해야 합니다.

- [Azure CLI를 설치합니다](/cli/azure/install-azure-cli).
- [키 자격 증명 모음 만들기](quick-create-cli.md)
- [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-cli) 저장소 계정 이름에는 소문자와 숫자만 사용 해야 합니다. 이름의 길이는 3 자에서 24 자 사이 여야 합니다.
      
## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

### <a name="connect-to-your-azure-account"></a>Azure 계정에 연결

[Az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure CLI 세션을 인증 합니다.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>저장소 계정에 대 한 Key Vault 액세스 권한 부여

Azure CLI [az role 대입문 create](/cli/azure/role/assignment?view=azure-cli-latest) 명령을 사용 하 여 저장소 계정에 대 한 액세스 Key Vault 제공 합니다. 다음 매개 변수 값에 명령을 제공 합니다.

- `--role`: "저장소 계정 키 운영자 서비스 역할" RBAC 역할을 전달 합니다. 이 역할은 저장소 계정에 대 한 액세스 범위를 제한 합니다. 클래식 저장소 계정의 경우 "클래식 저장소 계정 키 운영자 서비스 역할"을 대신 전달 합니다.
- `--assignee-object-id`: Azure 공용 클라우드의 Key Vault에 대 한 개체 ID 인 "93c27d83-f79b-4cb2-8dd4-4aa716542e74" 값을 전달 합니다. Azure Government 클라우드에서 Key Vault에 대 한 개체 ID를 가져오려면 [서비스 사용자 응용 프로그램 id](#service-principal-application-id)를 참조 하세요.
- `--scope`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`형식으로 된 저장소 계정 리소스 ID를 전달 합니다. 구독 ID를 찾으려면 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용 합니다. 저장소 계정 이름 및 저장소 계정 리소스 그룹을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용 합니다.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault 관리 저장소 계정 만들기

 Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) 명령을 사용 하 여 Key Vault 관리 저장소 계정을 만듭니다. 90 일의 다시 생성 기간을 설정 합니다. 90 일 후 Key Vault가 `key1`를 다시 생성 하 고 활성 키를 `key2`에서 `key1`로 바꿉니다. 그러면 `key1` 활성 키로 표시 됩니다. 다음 매개 변수 값에 명령을 제공 합니다.

- `--vault-name`: key vault의 이름을 전달 합니다. Key vault의 이름을 찾으려면 Azure CLI [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) 명령을 사용 합니다.
- `-n`: 저장소 계정의 이름을 전달 합니다. 저장소 계정의 이름을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용 합니다.
- `--resource-id`: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`형식으로 된 저장소 계정 리소스 ID를 전달 합니다. 구독 ID를 찾으려면 Azure CLI [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) 명령을 사용 합니다. 저장소 계정 이름 및 저장소 계정 리소스 그룹을 찾으려면 Azure CLI [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) 명령을 사용 합니다.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>공유 액세스 서명 토큰

Key Vault를 요청 하 여 공유 액세스 서명 토큰을 생성할 수도 있습니다. 공유 액세스 서명은 스토리지 계정의 리소스에 대한 위임된 권한을 제공합니다. 계정 키를 공유 하지 않고 저장소 계정의 리소스에 대 한 액세스 권한을 클라이언트에 부여할 수 있습니다. 공유 액세스 서명은 계정 키를 손상 시 키 지 않고 저장소 리소스를 공유 하는 안전한 방법을 제공 합니다.

이 섹션의 명령은 다음 작업을 완료 합니다.

- 계정 공유 액세스 서명 정의 `<YourSASDefinitionName>`설정 합니다. 이 정의는 키 자격 증명 모음 `<YourKeyVaultName>``<YourStorageAccountName>` Key Vault 관리 저장소 계정에 설정 됩니다.
- Blob, 파일, 테이블 및 큐 서비스에 대 한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 리소스 유형 서비스, 컨테이너 및 개체에 대해 생성 됩니다. 토큰은 지정 된 시작 및 종료 날짜를 사용 하 여 https를 통해 모든 사용 권한으로 만들어집니다.
- 자격 증명 모음에서 관리 되는 저장소 공유 액세스 서명 정의를 Key Vault 설정 합니다. 정의에는 만들어진 공유 액세스 서명 토큰의 템플릿 URI가 있습니다. 이 정의는 `account` 공유 액세스 서명 유형 이며 N 일 동안 유효 합니다.
- 공유 액세스 서명이 키 자격 증명 모음에 암호로 저장 되었는지 확인 합니다.

### <a name="create-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 만들기

Azure CLI [az storage account generate sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 명령을 사용 하 여 공유 액세스 서명 정의를 만듭니다. 이 작업을 수행 하려면 `storage` 및 `setsas` 권한이 있어야 합니다.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
작업이 성공적으로 실행 되 면 출력을 복사 합니다.

```console
"se=2020-01-01&sp=***"
```

이 출력은 다음 단계에서 `--template-id` 매개 변수에 전달 됩니다.

### <a name="generate-a-shared-access-signature-definition"></a>공유 액세스 서명 정의 생성

이전 단계의 출력을 `--template-id` 매개 변수로 전달 하는 Azure CLI [az keyvault 저장소 sas-정의 만들기](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) 명령을 사용 하 여 공유 액세스 서명 정의를 만듭니다.  사용자가 선택한 이름을 `-n` 매개 변수에 제공할 수 있습니다.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>공유 액세스 서명 정의 확인

Azure CLI [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 및 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령을 사용 하 여 공유 액세스 서명 정의가 키 자격 증명 모음에 저장 되었는지 확인할 수 있습니다.

먼저 [az keyvault secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) 명령을 사용 하 여 주요 자격 증명 모음에서 공유 액세스 서명 정의를 찾습니다.

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

SAS 정의에 해당 하는 암호에는 다음 속성이 포함 됩니다.

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

이제 [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) 명령과 `id` 속성을 사용 하 여 해당 암호의 콘텐츠를 볼 수 있습니다.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

이 명령의 출력은 SAS 정의 문자열을`value`으로 표시 합니다.


## <a name="next-steps"></a>다음 단계

- [키, 암호 및 인증서](https://docs.microsoft.com/rest/api/keyvault/)에 대해 자세히 알아보세요.
- [Azure Key Vault 팀 블로그에서](https://blogs.technet.microsoft.com/kv/)문서를 검토 합니다.
- [Az keyvault 저장소](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 참조 설명서를 참조 하세요.
