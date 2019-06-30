---
title: Azure CLI에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.
description: Azure CLI를 사용 하 여 Azure Storage 암호화에 대 한 고객 관리 키를 구성 하는 방법에 알아봅니다. 고객 관리 키를 사용 하 여 만들기, 회전, 해제 및 액세스 제어를 취소할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/24/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 925b69e064e260a78a102a068f052ad7d396c380
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357072"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Azure CLI에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

이 문서에서는 Azure CLI를 사용 하 여 고객 관리 키를 사용 하 여 key vault를 구성 하는 방법을 보여 줍니다.

## <a name="assign-an-identity-to-the-storage-account"></a>저장소 계정에 id 할당

저장소 계정에 대 한 고객 관리 키를 사용 하려면 먼저 저장소 계정에 자동으로 지정 된 관리 되는 id를 할당 합니다. Key vault에 액세스 하려면 저장소 계정 권한을 승인 하려면이 관리 되는 id를 사용 합니다.

Azure CLI를 사용 하 여 관리 되는 id를 할당, 호출 [az storage 계정 업데이트](/cli/azure/storage/account#az-storage-account-update)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Azure CLI를 사용 하 여 관리 되는 id 시스템 할당을 구성 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure CLI를 사용 하 여 Azure VM에서 Azure 리소스에 대 한 id를 관리 하는 구성](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)합니다.

## <a name="create-a-new-key-vault"></a>새 key vault 만들기

사용 하 여 Azure Storage 암호화에는 두 가지 키 보호 설정을 사용 하도록 설정 해야 합니다. 고객 관리 키를 저장 하는 key vault **일시 삭제** 하 고 **안 함**합니다. PowerShell 또는 Azure CLI를 사용 하 여 이러한 설정을 사용 하면 새 key vault를 만들려면 다음 명령을 실행 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다. 

Azure CLI를 사용 하 여 새 key vault를 만들려면 호출 [az keyvault 만들기](/cli/azure/keyvault#az-keyvault-create)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>키 자격 증명 모음 액세스 정책 구성

다음으로, 저장소 계정에 액세스할 권한이 있도록 키 자격 증명 모음 액세스 정책은 구성 합니다. 이 단계에서는 저장소 계정에 이전에 할당 된 관리 되는 id를 사용 합니다.

Key vault에 대 한 액세스 정책을 설정 하려면 호출 [az keyvault 설정-정책](/cli/azure/keyvault#az-keyvault-set-policy)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>새 키 만들기

다음으로 key vault에서 키를 만듭니다. 키를 만들려면 호출 [az keyvault key 만들기](/cli/azure/keyvault/key#az-keyvault-key-create)합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화를 구성 합니다.

기본적으로 Azure Storage 암호화는 Microsoft 관리 키를 사용합니다. 고객 관리 키에 대 한 Azure Storage 계정을 구성 하 고 저장소 계정을 사용 하 여 연결할 키를 지정 합니다.

저장소 계정의 암호화 설정 업데이트, 호출 [az storage 계정 업데이트](/cli/azure/storage/account#az-storage-account-update)합니다. 이 예제에서는 또한 key vault URI 및 키 버전에 대 한 쿼리 저장소 계정과 키를 연결 하는 데 필요한 값을 모두 합니다. 대괄호 안의 자리 표시자 값을 고유한 값으로 대체 해야 합니다.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>키 버전 업데이트

키의 새 버전을 만들면 새 버전을 사용 하 여 저장소 계정을 업데이트 해야 합니다. 첫째, 호출 하 여 key vault URI에 대 한 쿼리 [az keyvault show](/cli/azure/keyvault#az-keyvault-show), 및 호출 하 여 키 버전에 대 한 [az keyvault 키 버전 목록](/cli/azure/keyvault/key#az-keyvault-key-list-versions)합니다. 그런 다음 호출 [az 저장소 계정 업데이트](/cli/azure/storage/account#az-storage-account-update) 이전 섹션에 표시 된 대로 키의 새 버전을 사용 하도록 저장소 계정의 암호화 설정을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

- [미사용 데이터에 대 한 azure Storage 암호화](storage-service-encryption.md) 
- [Azure Key Vault 란](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
