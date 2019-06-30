---
title: Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키를 관리 합니다.
description: 저장소 계정 키는 Azure Key Vault 및 Azure storage 계정에 대 한 키 기반 액세스 간의 원활한 통합을 제공합니다.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 6ac054bc9750e4297080c4ab64030c9c6a5fb55a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312843"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Azure Key Vault 및 Azure CLI를 사용 하 여 저장소 계정 키를 관리 합니다. 

Azure Key Vault는 Azure storage 계정 및 클래식 저장소 계정에 대 한 키를 관리합니다. 여러 키 관리 함수를 완료 하려면 Key Vault 관리 되는 저장소 계정 기능을 사용할 수 있습니다.

[Azure Storage 계정](/azure/storage/storage-create-storage-account)은 계정 이름과 키로 구성된 자격 증명을 사용합니다. 키를 자동으로 생성 됩니다 및 암호를 아니라는 역할도 암호화 키입니다. Key Vault로 저장 하 여 저장소 계정 키 관리 [Key Vault 비밀](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)합니다. 키를 Azure storage 계정 (동기화) 나열 되 고 주기적으로 다시 생성 하거나 _회전_합니다. 

관리 되는 저장소 계정 키 기능을 사용 하는 경우 다음 사항을 고려 합니다.

- 키 값은 호출자에 대 한 응답에서 반환 되지 않습니다.
- Key Vault 에서만 저장소 계정 키를 관리 해야 합니다. 없는 직접 키를 관리 하 고 Key Vault 프로세스를 방해 하지 않습니다.
- 단일 키 자격 증명 모음 개체를 저장소 계정 키를 관리 해야 합니다. 여러 개체에서 키 관리를 허용 하지 마십시오.
- 서비스 주체 있지만 사용자 주체를 사용 하 여 저장소 계정을 관리 하려면 자격 증명 모음 키를 요청할 수 있습니다.
- 키 자격 증명 모음에만 사용 하 여 키를 다시 생성 합니다. 수동으로 없는 저장소 계정 키 다시 생성 합니다. 

> [!NOTE]
> Azure Active Directory (Azure AD)를 사용 하 여 azure Storage 통합은 Microsoft의 클라우드 기반 id 및 액세스 관리 서비스입니다.
> Azure AD 통합은 사용할 수 있습니다 [Azure blob 및 큐](https://docs.microsoft.com/azure/storage/common/storage-auth-aad)합니다.
> Azure AD 인증 및 권한 부여를 사용 합니다.
> Azure AD는 Azure Key Vault와 마찬가지로 Azure Storage에 OAuth2 토큰 기반 액세스를 제공합니다.
>
> Azure AD를 사용 하면 저장소 계정 자격 증명 대신 응용 프로그램 또는 사용자 id를 사용 하 여 클라이언트 응용 프로그램을 인증할 수 있습니다.
> 사용할 수는 [Azure AD 관리 id](/azure/active-directory/managed-identities-azure-resources/) Azure에서 실행 하는 경우. 관리 되는 id 또는 응용 프로그램을 사용 하 여 자격 증명을 저장 하 고 클라이언트 인증에 대 한 필요성을 제거 합니다.
> Azure AD는 또한 Key Vault에서 지원 되는 역할 기반 액세스 제어 (RBAC) 권한 부여 관리를 사용 합니다.

### <a name="service-principal-application-id"></a>서비스 주체 응용 프로그램 ID

Azure AD 테 넌 트를 사용 하 여 등록 된 각 응용 프로그램 제공을 [서비스 주체](/azure/active-directory/develop/developer-glossary#service-principal-object)합니다. 서비스 주체 (ID) 응용 프로그램 id로 사용 됩니다. 응용 프로그램 ID는 RBAC 통해 다른 Azure 리소스에 대 한 액세스 권한 부여 설치 하는 동안 사용 됩니다.

Key Vault는 모든 Azure AD 테 넌 트에 미리 등록 되어 있는 Microsoft 응용 프로그램. 동일한 응용 프로그램 id 및 각 Azure 클라우드 내에서 키 자격 증명 모음 등록 됩니다.

| 테 넌 트 | 클라우드 | 애플리케이션 UI |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | 공용 azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| 기타  | 모두 | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>필수 조건

저장소 계정 키를 관리 하려면 Key Vault를 사용 하기 전에 필수 구성 요소를 검토 합니다.

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)를 설치합니다.
- 만들기는 [Azure storage 계정](https://azure.microsoft.com/services/storage/)합니다. 따릅니다 [이 단계](https://docs.microsoft.com/azure/storage/)합니다.
- 저장소 계정 이름은 소문자 문자 및 숫자만 사용 해야 합니다. 이름의 길이 3 ~ 24 자 사이 여야 합니다.        
      
## <a name="manage-storage-account-keys"></a>저장소 계정 키 관리

Key Vault를 사용 하 여 저장소 계정 키를 관리 하는 네 가지 기본 단계는

1. 기존 저장소 계정을 가져옵니다.
1. 기존 key vault를 가져옵니다.
1. 관리 되는 Key Vault 저장소 계정 자격 증명 모음에 추가 합니다. 설정 `key1` 180 일 동안 다시 생성 기간을 사용 하 여 활성 키로 합니다.
1. 사용 하 여 `key1` 지정된 된 저장소 계정에 대 한 저장소 컨텍스트를 설정 합니다.

> [!NOTE]
> Key Vault 서비스로 운영자 권한은 저장소 계정에 할당 됩니다.
> 
> Azure Key Vault 관리 되는 저장소 계정 키를 설정 하면 키를 Key Vault를 사용 하 여 변경만.
> 관리 되는 저장소 계정 키에 대 한 Key Vault 저장소 계정 키 회전을 관리합니다.

1. 저장소 계정을 만든 후에 관리 하는 저장소 계정의 리소스 ID를 가져오려면 다음 명령을 실행 합니다.
    ```
    az storage account show -n storageaccountname
    ```

    명령 출력에서 리소스 ID 값을 복사 합니다.
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    예제 출력:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Key Vault에 "저장소 계정 키 운영자 서비스 역할" RBAC 역할을 할당 합니다. 이 역할은 저장소 계정에 대 한 액세스 범위를 제한합니다. 클래식 저장소 계정에 대 한 "클래식 Storage 계정 키 운영자 서비스 역할" 역할을 사용 합니다.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` Azure 공용 클라우드에서 Key Vault에 대 한 개체 ID입니다. Azure Government 클라우드에서 Key Vault에 대 한 개체 ID를 가져오려면 참조 [서비스 주체 응용 프로그램 ID](#service-principal-application-id)합니다.
    
1. 키 자격 증명 모음 관리 되는 저장소 계정을 만듭니다.

    90 일 동안 다시 생성 기간을 설정 합니다. 90 일 후 자격 증명 모음 키 다시 생성 `key1` 하 고 활성 키 교환 `key2` 에 `key1`입니다. `key1` 활성 키로 표시 됩니다. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>토큰을 생성 합니다.

또한 공유 액세스 서명 토큰을 생성 하는 Key Vault를 요청할 수 있습니다. 공유 액세스 서명은 저장소 계정의 리소스에 대한 위임된 권한을 제공합니다. 부여할 수 있습니다 클라이언트 리소스에 대 한 액세스 저장소 계정의 계정 키를 공유 하지 않고 있습니다. 공유 액세스 서명은 계정 키를 손상 시 키 지 않고 저장소 리소스를 공유할 수 있는 안전한 방법을 제공 합니다.

이 섹션의 명령에서에서 다음 작업을 완료합니다.

- 계정 공유 액세스 서명을 정의 설정 `<YourSASDefinitionName>`합니다. 정의 관리 하는 Key Vault 저장소 계정에 설정 되어 `<YourStorageAccountName>` key vault에서 `<VaultName>`합니다.
- Blob, 파일, 테이블 및 큐 서비스에 대 한 계정 공유 액세스 서명 토큰을 만듭니다. 토큰은 서비스, 컨테이너 및 개체 리소스 형식에 대해 만들어집니다. 토큰에 https 통해 모든 사용 권한 및 지정 된 시작 및 종료 날짜를 사용 하 여 만들어집니다.
- 자격 증명 모음에서 관리 되는 Key Vault 저장소 공유 액세스 서명 정의 설정 합니다. 정의 템플릿 생성 된 공유 액세스 서명 토큰의 URI에 있습니다. 공유 액세스 서명 유형 정의가 `account` N 일 동안 유효 합니다.
- 공유 액세스 서명 정의에 해당 하는 Key Vault 비밀의 실제 액세스 토큰을 검색 합니다.

이전 섹션의 단계를 완료 한 후에 공유 액세스 서명 토큰을 생성 하는 Key Vault를 요청 하려면 다음 명령을 실행 합니다. 

1. 공유 액세스 서명 정을 만듭니다. 공유 액세스 서명 정의 만든 후 더 공유 액세스 서명 토큰을 생성 하는 Key Vault를 요청 합니다. 이 작업에 필요 합니다 `storage` 및 `setsas` 권한.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    작업에 대 한 도움말을 참조 하세요. 합니다 [az storage 계정 sas 생성](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) 설명서를 참조 합니다.

    작업을 성공적으로 실행 출력을 복사 합니다.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. 사용 된 `$sasToken` 이전 명령에 의해 생성 및 공유 액세스 서명 정의 만듭니다. 명령 매개 변수에 대 한 자세한 내용은 참조는 [az keyvault 저장소 sas 정의 만들기](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) 설명서를 참조 합니다.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    사용자는 저장소 계정에 권한이 없는, 하는 경우 먼저 사용자의 개체 ID를 가져옵니다.
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>코드에서 토큰을 가져오지

인출 하 여 저장소 계정에 대 한 작업 실행 [공유 액세스 서명 토큰](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) Key Vault에서.

Key Vault에 인증 하는 방법은 세 가지가 있습니다.

- 관리 서비스 id를 사용 합니다. 이 이렇게를 사용 하는 것이 좋습니다.
- 서비스 주체 및 인증서를 사용 합니다. 
- 서비스 주체 및 암호를 사용 합니다. 이 방법은 권장 되지 않습니다.

자세한 내용은 참조 하세요. [Azure Key Vault: 기본 개념](key-vault-whatis.md#basic-concepts)합니다.

다음 예제에서는 공유 액세스 서명 토큰을 인출 하는 방법을 보여 줍니다. 공유 액세스 서명 정의 만든 후에 토큰을 가져옵니다. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

공유 액세스 서명 토큰이 만료 될 경우 Key Vault에서 공유 액세스 서명 토큰을 다시 인출 하 고 코드를 업데이트 합니다.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Azure CLI 명령

관리 되는 저장소 계정에 관련 된 Azure CLI 명령에 대 한 자세한 내용은 참조는 [az keyvault 저장소](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) 설명서를 참조 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [키, 비밀 및 인증서](https://docs.microsoft.com/rest/api/keyvault/)합니다.
- 문서를 검토 합니다 [Azure Key Vault 팀 블로그](https://blogs.technet.microsoft.com/kv/)합니다.
