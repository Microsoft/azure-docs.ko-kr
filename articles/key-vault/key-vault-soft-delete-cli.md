---
title: Azure Key Vault - CLI로 일시 삭제를 사용하는 방법
description: CLI 코드 캡처를 통한 일시 삭제의 사용 사례 예제
author: msmbaldwin
manager: barbkess
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: mbaldwin
ms.openlocfilehash: aa9b89b9afec069e97236b7652e0f1d37644f5cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640483"
---
# <a name="how-to-use-key-vault-soft-delete-with-cli"></a>CLI로 Key Vault 일시 삭제를 사용하는 방법

Azure Key Vault의 일시 삭제 기능을 사용하면 삭제된 자격 증명 모음 및 자격 증명 모음 개체를 복구할 수 있습니다. 특히, 일시 삭제는 다음과 같은 시나리오를 해결합니다.

- Key Vault의 복구 가능한 삭제 지원
- Key Vault 개체(예: 키, 비밀 및 인증서)의 복구 가능한 삭제를 지원

## <a name="prerequisites"></a>필수 조건

- Azure CLI - 사용자 환경에 이 단계가 없는 경우 [Azure CLI를 사용한 Key Vault 관리](key-vault-manage-with-cli2.md)를 참조하세요.

CLI에 대한 Key Vault 관련 특정 참조 정보는 [Azure CLI Key Vault 참조](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요.

## <a name="required-permissions"></a>필요한 사용 권한

Key Vault 작업은 RBAC(역할 기반 액세스 제어) 권한을 통해 다음과 같이 별도로 관리됩니다.

| 작업(Operation) | 설명 | 사용자 권한 |
|:--|:--|:--|
|나열|삭제된 Key Vault를 나열합니다.|Microsoft.KeyVault/deletedVaults/read|
|복구|삭제된 Key Vault를 복구합니다.|Microsoft.KeyVault/vaults/write|
|제거|삭제된 Key Vault 및 모든 콘텐츠를 영구적으로 제거합니다.|Microsoft.KeyVault/locations/deletedVaults/purge/action|

권한 및 액세스 제어에 대한 자세한 내용은 [Key Vault 보안](key-vault-secure-your-key-vault.md)을 참조하세요.

## <a name="enabling-soft-delete"></a>일시 삭제를 사용하도록 설정

"일시 삭제"를 사용하여 삭제된 키 자격 증명 모음 또는 키 자격 증명 모음에 저장된 개체를 복구할 수 있습니다.

> [!IMPORTANT]
> 키 자격 증명 모음에 대해 '일시 삭제'를 사용하도록 설정하는 것은 복구 불가능한 동작입니다. 일시 삭제된 속성을 "true"로 설정한 후에는 해당 속성을 변경하거나 제거할 수 없습니다.  

### <a name="existing-key-vault"></a>기존 Key Vault 사용

ContosoVault라는 기존 Key Vault의 경우 다음과 같이 일시 삭제를 사용하도록 설정합니다. 

```azurecli
az resource update --id $(az keyvault show --name ContosoVault -o tsv | awk '{print $1}') --set properties.enableSoftDelete=true
```

### <a name="new-key-vault"></a>새로운 Key Vault

새로운 Key Vault에 대한 일시 삭제를 사용하도록 설정하는 것은 만들면서 생성 명령에 일시 삭제 사용 플래그를 추가하면 됩니다.

```azurecli
az keyvault create --name ContosoVault --resource-group ContosoRG --enable-soft-delete true --location westus
```

### <a name="verify-soft-delete-enablement"></a>일시 삭제 사용 확인

Key Vault에 일시 삭제가 사용할 수 있도록 설정되어 있는지 확인하려면 *show* 명령을 실행하여 ‘일시 삭제 사용?’ 특성:

```azurecli
az keyvault show --name ContosoVault
```

## <a name="deleting-a-soft-delete-protected-key-vault"></a>일시 삭제 보호 키 자격 증명 모음 삭제

키 자격 증명을 삭제하는 명령의 동작은 일시 삭제가 사용하도록 설정되었는지 여부에 따라 변합니다.

> [!IMPORTANT]
>일시 삭제를 사용하도록 설정하지 않은 키 자격 증명 모음에 대해 다음 명령을 실행하면 이 키 자격 증명 모음과 해당 자격 증명 모음의 모든 내용을 복구하는 옵션 없이 영구적으로 삭제하게 됩니다!

```azurecli
az keyvault delete --name ContosoVault
```

### <a name="how-soft-delete-protects-your-key-vaults"></a>일시 삭제가 Key Vault를 보호하는 방식

활성화된 일시 삭제 사용:

- 삭제된 키 자격 증명 모음은 해당 리소스 그룹에서 제거되고 원래 생성되었던 위치와 연결된 예약된 네임스페이스에 놓입니다. 
- 키, 비밀 및 인증서와 같은 삭제된 개체는 해당 개체가 포함하고 있는 키 자격 증명 모음이 삭제된 상태에 있는 한 액세스할 수 없습니다. 
- 삭제된 키 자격 증명 모음에 대한 DNS 이름은 동일한 이름의 새로운 키 자격 증명 모음이 만들어지지 않도록 예약됩니다.  

다음 명령을 사용하여 사용자 구독에 연결된 삭제된 상태 Key Vault를 볼 수도 있습니다.

```azurecli
az keyvault list-deleted
```
- *ID* 복구 또는 제거 하는 경우 해당 리소스를 식별할 수 있습니다. 
- *리소스 ID*는 이 자격 증명 모음의 원본 리소스 ID입니다. 이제 이 Key Vault가 삭제된 상태로 있으므로 해당 리소스 ID를 사용하는 리소스가 없습니다. 
- *예약된 제거 날짜*는 어떤 작업도 수행되지 않는 경우 자격 증명 모음이 영구적으로 삭제되는 시기입니다. *예약된 제거 날짜*를 계산하는 데 사용되는 기본 보존 기간은 90일입니다.

## <a name="recovering-a-key-vault"></a>Key Vault 복구

키 자격 증명 모음을 복구하려면 키 자격 증명 모음, 리소스 그룹 및 위치를 지정합니다. 삭제된 키 자격 증명 모음의 위치 및 리소스 그룹은 복구 프로세스에 필요하므로 적어 둡니다.

```azurecli
az keyvault recover --location westus --resource-group ContosoRG --name ContosoVault
```

키 자격 증명 모음이 복구될 때 해당 키 자격 증명 모음의 원래 리소스 ID를 가진 새로운 리소스가 생성됩니다. 원래 리소스 그룹이 제거된 경우 복구를 시도하기 전에 동일한 이름의 리소스 그룹을 만들어야 합니다.

## <a name="deleting-and-purging-key-vault-objects"></a>키 자격 증명 모음 개체 삭제 및 제거

다음 명령은 일시 삭제를 사용하도록 설정한 ‘ContosoVault’ 키 자격 증명 모음에서 ‘ContosoFirstKey’ 키를 삭제합니다.

```azurecli
az keyvault key delete --name ContosoFirstKey --vault-name ContosoVault
```

일시 삭제가 활성화되어 있는 Key Vault를 사용하면 삭제된 키를 명시적으로 나열하거나 검색할 때 외에는 삭제된 키가 여전히 삭제된 것으로 표시됩니다. 삭제된 상태의 키에서의 작업은 대부분 삭제된 키의 나열, 복구 또는 제거 외에는 실패하게 됩니다. 

예를 들어 Key Vault에서 삭제된 키 나열을 요청하려면 다음 명령을 사용합니다.

```azurecli
az keyvault key list-deleted --vault-name ContosoVault
```

### <a name="transition-state"></a>전환 상태 

일시 삭제가 활성화되어 있는 Key Vault의 키를 삭제하는 경우 전환이 완료되기까지 몇 초 정도 걸릴 수 있습니다. 이 전환 중에 키가 활성 상태 또는 삭제된 상태에 있지 않은 것으로 나타날 수 있습니다. 

### <a name="using-soft-delete-with-key-vault-objects"></a>Key Vault 개체를 통해 일시 삭제 사용

키 자격 증명 모음과 마찬가지로 삭제된 키, 비밀 또는 인증서도 복구하거나 제거하지 않는 한 최대 90일 동안 삭제된 상태로 남아 있습니다.

#### <a name="keys"></a>구성

일시 삭제된 키를 복구하려면 다음을 수행합니다.

```azurecli
az keyvault key recover --name ContosoFirstKey --vault-name ContosoVault
```

일시 삭제된 키를 영구적으로 삭제(일명 제거)하려면 다음을 수행합니다.

> [!IMPORTANT]
> 키를 제거하면 키가 영구적으로 삭제되며, 복구할 수 없습니다! 

```azurecli
az keyvault key purge --name ContosoFirstKey --vault-name ContosoVault
```

**복구** 및 **제거** 작업은 Key Vault 액세스 정책에 연결된 자체 권한이 있습니다. 사용자 또는 서비스 주체가 **복구** 또는 **제거** 동작을 실행할 수 있도록 하려면 이들이 해당 키 또는 비밀에 대한 적절한 권한을 가지고 있어야 합니다. 기본적으로 '모든' 바로 가기를 사용하여 모든 사용 권한을 부여할 때 **제거**는 키 자격 증명 모음의 액세스 정책에 추가되지 않습니다. **제거** 사용 권한을 명시적으로 부여해야 합니다. 

#### <a name="set-a-key-vault-access-policy"></a>Key Vault 액세스 정책 설정

다음 명령은 **제거**를 포함하여 *ContosoVault*의 키에 대해 여러 가지 작업을 사용하는 user@contoso.com 사용 권한을 부여합니다.

```azurecli
az keyvault set-policy --name ContosoVault --key-permissions get create delete list update import backup restore recover purge
```

>[!NOTE] 
> 이제 막 일시 삭제 활성화되도록 기존 Key Vault를 설정한 경우 **복구** 및 **제거** 권한이 없을 수도 있습니다.

#### <a name="secrets"></a>비밀

키와 마찬가지로 비밀도 자체의 명령을 사용하여 관리됩니다.

- SQLPassword라는 비밀 삭제: 
  ```azurecli
  az keyvault secret delete --vault-name ContosoVault -name SQLPassword
  ```

- Key Vault의 모든 삭제된 비밀 나열: 
  ```azurecli
  az keyvault secret list-deleted --vault-name ContosoVault
  ```

- 삭제된 상태의 비밀 복구: 
  ```azurecli
  az keyvault secret recover --name SQLPassword --vault-name ContosoVault
  ```

- 삭제된 상태의 비밀 제거: 

  > [!IMPORTANT]
  > 비밀을 제거하면 비밀이 영구적으로 삭제되며 복구할 수 없습니다! 

  ```azurecli
  az keyvault secret purge --name SQLPAssword --vault-name ContosoVault
  ```

## <a name="purging-a-soft-delete-protected-key-vault"></a>일시 삭제 보호 키 자격 증명 모음 제거

> [!IMPORTANT]
> 키 자격 증명 모음 또는 포함된 해당 개체 중 하나를 제거하면 영구적으로 삭제되며, 이는 복구할 수 없습니다!

제거 함수는 키 자격 증명 모음 개체 또는 전체 key vault가 이전에 일시 삭제는 영구적으로 삭제 됩니다. 이전 섹션에서 설명한 대로 일시 삭제 기능을 사용하도록 설정된 키 자격 증명 모음에 저장된 개체는 다음과 같이 여러 가지 상태를 거칠 수 있습니다.

- **활성**: 삭제 전
- **일시 삭제**: 삭제 후 목록에 나열되고 활성 상태로 다시 복구할 수 있습니다.
- **영구 삭제**: 제거 후 복구할 수 없습니다.

키 자격 증명 모음에 대해서도 마찬가지입니다. 일시 삭제 키 자격 증명 모음 및 해당 내용을 영구적으로 삭제하려면 키 자격 증명 모음 자체를 제거해야 합니다.

### <a name="purging-a-key-vault"></a>키 자격 증명 모음 제거

키 자격 증명 모음이 제거되면 키, 비밀 및 인증서를 포함하여 해당 전체 콘텐츠가 영구적으로 삭제됩니다. 일시 삭제된 키 자격 증명 모음을 제거하려면 `az keyvault purge` 명령을 사용합니다. `az keyvault list-deleted` 명령을 사용하여 구독의 삭제된 Key Vault의 위치를 찾을 수 있습니다.

```azurecli
az keyvault purge --location westus --name ContosoVault
```

### <a name="purge-permissions-required"></a>제거 권한 필요
- 삭제된 키 자격 증명 모음을 제거하려면 사용자가 *Microsoft.KeyVault/locations/deletedVaults/purge/action* 작업에 대한 RBAC 사용 권한을 가지고 있어야 합니다. 
- 삭제된 키 자격 증명 모음을 나열하려면 사용자가 *Microsoft.KeyVault/deletedVaults/read* 작업에 대한 RBAC 사용 권한을 가지고 있어야 합니다. 
- 기본적으로 구독 관리자만 이러한 권한을 갖습니다. 

### <a name="scheduled-purge"></a>예약된 제거

삭제된 키 자격 증명 모음 개체를 나열하면 키 자격 증명 모음에 의해 제거하기로 예약된 시기도 표시됩니다. *예약된 제거 날짜*는 어떤 작업도 수행되지 않는 경우 키 자격 증명 모음 개체가 영구적으로 삭제될 때를 나타냅니다. 기본적으로 삭제된 Key Vault 개체에 대한 보존 기간은 90일입니다.

>[!IMPORTANT]
>해당 *예약된 제거 날짜* 필드에 의해 트리거되는 제거된 자격 증명 모음 개체가 영구적으로 삭제됩니다. 이는 복구할 수 없습니다!

## <a name="enabling-purge-protection"></a>보호 제거를 사용 하도록 설정

보호 제거 삭제 된 자격 증명 모음 또는 개체에 설정 된 경우 90 일의 보존 기간이 경과 될 때까지 상태를 비울 수 없습니다. 즉, 이러한 자격 증명 모음 또는 개체는 여전히 복구할 수 있는 상태입니다. 이 기능을 사용 하면 자격 증명 모음 또는 개체가 될 수 없습니다 영구적으로 추가 된 보증 경과할 때까지 보존 기간을 삭제 합니다.

일시 삭제를 함께 사용 하는 경우에 보호 제거를 사용할 수 있습니다. 

모두 일시 삭제를 설정 하 고 자격 증명 모음을 만들 때 보호 제거를 사용 합니다 [az keyvault 만들기](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령:

```
az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
```

보호 제거 (이미 있는 일시 삭제가 활성화 되어)는 기존 자격 증명 모음에 추가를 사용 합니다 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 명령:

```
az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
```

## <a name="other-resources"></a>기타 리소스

- Key Vault의 일시 삭제 기능에 대한 자세한 내용은 [Azure Key Vault 일시 삭제 개요](key-vault-ovw-soft-delete.md)를 참조하세요.
- Azure Key Vault 사용에 대한 일반적인 개요는 [Azure Key Vault란?](key-vault-overview.md)을 참조하세요.

