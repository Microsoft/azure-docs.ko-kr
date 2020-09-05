---
title: 고객 관리형 키를 사용하여 저장 데이터 암호화
description: Azure container registry의 미사용 암호화 및에 저장 된 고객 관리 키를 사용 하 여 Premium registry를 암호화 하는 방법에 대해 알아봅니다 Azure Key Vault
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: 67fb58d0e11709b3d801a81f15d856e9b3db922b
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225889"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 암호화

이미지 및 기타 아티팩트를 Azure 컨테이너 레지스트리에 저장하면 Azure에서 [서비스 관리형 키](../security/fundamentals/encryption-models.md)를 사용하여 저장된 레지스트리 콘텐츠를 자동으로 암호화합니다. Azure Key Vault에서 만들고 관리하는 키를 사용하여 추가 암호화 계층을 통해 기본 암호화를 보완할 수 있습니다. 이 문서에서는 Azure CLI 및 Azure Portal을 사용하는 단계를 안내합니다.

고객 관리형 키를 사용하는 서버 쪽 암호화는 [Azure Key Vault](../key-vault/general/overview.md)와의 통합을 통해 지원됩니다. 사용자 고유의 암호화 키를 만들어 키 자격 증명 모음에 저장하거나 Azure Key Vault의 API를 사용하여 키를 생성할 수 있습니다. Azure Key Vault를 사용하면 키 사용을 감사할 수도 있습니다.

이 기능은 **프리미엄** 컨테이너 레지스트리 서비스 계층에서 사용할 수 있습니다. 레지스트리 서비스 계층 및 제한에 대한 자세한 내용은 [Azure Container Registry 서비스 계층](container-registry-skus.md)을 참조하세요.


## <a name="things-to-know"></a>알아야 할 사항

* 고객 관리형 키는 현재 레지스트리를 만들 때만 사용하도록 설정할 수 있습니다.
* 레지스트리에서 고객 관리형 키를 사용하도록 설정한 후에는 사용하지 않도록 설정할 수 없습니다.
* [콘텐츠 신뢰](container-registry-content-trust.md)는 현재 고객 관리형 키로 암호화된 레지스트리에서 지원되지 않습니다.
* 고객 관리형 키로 암호화된 레지스트리에서 [ACR 작업](container-registry-tasks-overview.md)에 대한 실행 로그는 현재 24시간 동안만 유지됩니다. 로그를 더 오랜 기간 동안 유지해야 하는 경우 [작업 실행 로그 내보내기 및 저장](container-registry-tasks-logs.md#alternative-log-storage)에 대한 지침을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서에서 Azure CLI 단계를 사용하려면 Azure CLI 버전 2.2.0 이상이 필요합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="enable-customer-managed-key---cli"></a>고객 관리형 키 사용 - CLI

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

필요한 경우 [az group create][az-group-create] 명령을 실행하여 키 자격 증명 모음, 컨테이너 레지스트리 및 기타 필요한 리소스를 만들기 위한 리소스 그룹을 만듭니다.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

[az identity create][az-identity-create] 명령을 사용하여 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 이 ID는 레지스트리에서 Key Vault 서비스에 액세스하는 데 사용됩니다.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

명령 출력에서 `id` 및 `principalId` 값을 적어 둡니다. 이러한 값은 이후 단계에서 키 자격 증명 모음에 대한 레지스트리 액세스를 구성하는 데 필요합니다.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

편의를 위해 이러한 값을 환경 변수에 저장합니다.

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

[az keyvault create][az-keyvault-create]를 사용하여 키 자격 증명 모음을 만들어 레지스트리 암호화를 위한 고객 관리형 키를 저장합니다.

실수로 키 또는 키 자격 증명 모음을 삭제하여 발생하는 데이터 손실을 방지하려면 **일시 삭제** 및 **제거 보호** 설정을 사용하도록 설정해야 합니다. 다음 예제에는 이러한 설정에 대한 매개 변수가 포함되어 있습니다.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음액세스 정책 추가

ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다. 다음 [az keyvault set-policy][az-keyvault-set-policy] 명령에서는 이전에 만들어 환경 변수에 저장한 관리 ID의 보안 주체 ID를 전달합니다. 키 권한을 **get**, **unwrapKey** 및 **wrapKey**로 설정합니다.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

### <a name="create-key-and-get-key-id"></a>키 만들기 및 키 ID 가져오기

[az keyvault key create][az-keyvault-key-create] 명령을 실행하여 키를 키 자격 증명 모음에 만듭니다.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

명령 출력에서 키의 ID(`kid`)를 적어 둡니다. 이 ID는 다음 단계에서 사용합니다.

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
편의를 위해 이 값을 환경 변수에 저장합니다.

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>고객 관리형 키를 사용하여 레지스트리 만들기

[az acr create][az-acr-create] 명령을 실행하여 레지스트리를 프리미엄 서비스 계층에 만들고 고객 관리형 키를 사용하도록 설정합니다. 이전에 환경 변수에 저장한 관리 ID 보안 주체 ID와 키 ID를 전달합니다.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>암호화 상태 표시

고객 관리형 키를 사용하는 레지스트리 암호화를 사용하도록 설정되었는지 여부를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

출력은 다음과 비슷합니다.

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>고객 관리형 키 사용 - 포털

### <a name="create-a-managed-identity"></a>관리 ID 만들기

Azure Portal에서 [Azure 리소스에 대해 사용자가 할당한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 만듭니다. 단계는 [사용자 할당 ID 만들기](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조하세요.

ID 이름은 이후 단계에서 사용합니다.

![Azure Portal에서 사용자가 할당한 관리 ID 만들기](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>키 자격 증명 모음 만들기

키 자격 증명 모음을 만드는 단계는 [빠른 시작: Azure Portal을 사용하여 Azure Key Vault에서 비밀 설정 및 검색](../key-vault/secrets/quick-create-portal.md)을 참조하세요.

고객 관리형 키에 대한 키 자격 증명 모음을 만들 때 **기본 사항** 탭에서 **일시 삭제** 및 **제거 보호** 보호 설정을 사용하도록 설정합니다. 이러한 설정을 사용하면 실수로 키 또는 키 자격 증명 모음을 삭제하여 발생하는 데이터 손실을 방지할 수 있습니다.

![Azure Portal에서 키 자격 증명 모음 만들기](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>키 자격 증명 모음액세스 정책 추가

ID에서 액세스할 수 있도록 키 자격 증명 모음에 대한 정책을 구성합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가**를 차례로 선택합니다.
1. **키 권한**을 선택하고, **가져오기**, **키 래핑 해제** 및 **키 래핑**을 선택합니다.
1. **보안 주체 선택**을 선택하고, 사용자가 할당한 관리 ID의 리소스 이름을 선택합니다.  
1. **추가**, **저장**을 차례로 선택합니다.

![키 자격 증명 모음 액세스 정책 만들기](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>키 만들기

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **키**를 차례로 선택합니다.
1. **+ 생성/가져오기**를 선택하고, 키에 대한 고유한 이름을 입력합니다.
1. 나머지 기본값을 그대로 적용하고 **만들기**를 선택합니다.
1. 만들어지면 키를 선택하고 현재 키 버전을 적어 둡니다.

### <a name="create-azure-container-registry"></a>Azure 컨테이너 레지스트리 만들기

1. **리소스 만들기** > **컨테이너** > **컨테이너 레지스트리**를 선택합니다.
1. **기본 사항** 탭에서 리소스 그룹을 선택하거나 만들고, 레지스트리 이름을 입력합니다. **SKU**에서 **프리미엄**을 선택합니다.
1. **암호화** 탭의 **고객 관리형 키**에서 **사용**을 선택합니다.
1. **ID**에서 사용자가 만든 관리 ID를 선택합니다.
1. **암호화**에서 **Key Vault에서 선택**을 선택합니다.
1. **Azure Key Vault에서 키 선택** 창에서 이전 섹션에서 만든 키 자격 증명 모음, 키 및 버전을 선택합니다.
1. **암호화** 탭에서 **검토 + 만들기**를 선택합니다.
1. **만들기**를 선택하여 레지스트리 인스턴스를 배포합니다.

![Azure Portal에서 컨테이너 레지스트리 만들기](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

포털에서 레지스트리의 암호화 상태를 확인하려면 레지스트리로 이동합니다. **설정** 아래에서 **암호화**를 선택합니다.

## <a name="enable-customer-managed-key---template"></a>고객 관리형 키 사용 - 템플릿

Resource Manager 템플릿을 사용하여 레지스트리를 만들고 고객 관리형 키를 사용하는 암호화를 사용하도록 설정할 수도 있습니다.

다음 템플릿은 새 컨테이너 레지스트리 및 사용자가 할당한 관리 ID를 만듭니다. 다음 내용을 새 파일에 복사하고 `CMKtemplate.json`과 같은 파일 이름을 사용하여 저장합니다.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

이전 섹션의 단계에 따라 다음 리소스를 만듭니다.

* 키 자격 증명 모음(이름으로 식별됨)
* 키 자격 증명 모음 키(키 ID로 식별됨)

다음 [az group deployment create][az-group-deployment-create] 명령을 실행하여 이전 템플릿 파일을 통해 레지스트리를 만듭니다. 표시되면 새 레지스트리 이름 및 관리 ID 이름과 사용자가 만든 키 자격 증명 모음 이름 및 키 ID를 제공합니다.

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>암호화 상태 표시

레지스트리 암호화 상태를 표시하려면 [az acr encryption show][az-acr-encryption-show] 명령을 실행합니다.

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>레지스트리 사용

레지스트리에서 고객 관리형 키를 사용하도록 설정되면 고객 관리형 키로 암호화되지 않은 레지스트리에서 수행하는 것과 동일한 레지스트리 작업을 수행할 수 있습니다. 예를 들어 레지스트리로 인증하고 Docker 이미지를 푸시할 수 있습니다. [이미지 밀어넣기 및 끌어오기](container-registry-get-started-docker-cli.md)의 명령 예제를 참조하세요.

## <a name="rotate-key"></a>키 회전

레지스트리 암호화에 사용되는 고객 관리형 키를 규정 준수 정책으로 회전시킵니다. 새 키를 만들거나 키 버전을 업데이트한 다음, 키를 사용하여 데이터를 암호화하도록 레지스트리를 업데이트합니다. 이러한 단계는 Azure CLI 또는 포털을 사용하여 수행할 수 있습니다.

키를 회전시키는 경우 일반적으로 레지스트리를 만들 때 사용되는 것과 동일한 ID를 지정합니다. 필요에 따라 키 액세스를 위해 사용자가 할당한 새 ID를 구성하거나 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하고 지정합니다.

> [!NOTE]
> 필요한 [키 자격 증명 모음 액세스 정책](#add-key-vault-access-policy)이 키 액세스를 위해 구성하는 ID에 설정되어 있는지 확인하세요.

### <a name="azure-cli"></a>Azure CLI

[az keyvault key][az-keyvault-key] 명령을 사용하여 키 자격 증명 모음 키를 만들거나 관리합니다. 예를 들어 새 키 버전 또는 키를 만들려면 [az acr encryption rotate-key][az-keyvault-key-create] 명령을 실행합니다.

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name>
```

그런 다음, [az acr encryption rotate-key][az-acr-encryption-rotate-key] 명령을 실행하여 새 키 ID와 구성할 ID를 전달합니다.

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>포털

레지스트리의 **암호화** 설정을 사용하여 고객 관리형 키에 사용되는 키 버전, 키, 키 자격 증명 모음 또는 ID 설정을 업데이트합니다.

예를 들어 새 키 버전을 생성하고 구성하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** 아래에서 **암호화** > **키 변경**을 차례로 선택합니다.
1. **키 선택**을 선택합니다.

    ![Azure Portal에서 키 회전](./media/container-registry-customer-managed-keys/rotate-key.png)
1. **Azure Key Vault에서 키 선택** 창에서 이전에 구성한 키 자격 증명 모음 및 키를 선택하고, **버전**에서 **새로 만들기**를 선택합니다.
1. **키 만들기** 창에서 **생성**, **만들기**를 차례로 선택합니다.
1. 키 선택을 완료하고, **저장**을 선택합니다.

## <a name="revoke-key"></a>키 철회

키 자격 증명 모음의 액세스 정책을 변경하거나 키를 삭제하여 고객 관리형 암호화 키를 철회합니다. 예를 들어 [az keyvault delete-policy][az-keyvault-delete-policy] 명령을 사용하여 레지스트리에서 사용하는 관리 ID의 액세스 정책을 변경합니다.

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

레지스트리는 암호화 키에 액세스할 수 없으므로 키를 철회하면 모든 레지스트리 데이터에 대한 액세스가 효과적으로 차단됩니다. 키에 대한 액세스를 사용하도록 설정되거나 삭제된 키가 복원되는 경우 레지스트리에서 암호화된 레지스트리 데이터에 다시 액세스할 수 있도록 키를 선택합니다.

## <a name="advanced-scenarios"></a>고급 시나리오

### <a name="system-assigned-identity"></a>시스템 할당 ID

암호화 키에 대한 키 자격 증명 모음에 액세스하도록 레지스트리의 시스템이 할당한 관리 ID를 구성할 수 있습니다. Azure 리소스에 대한 다양한 관리 ID에 익숙하지 않은 경우 [개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

포털에서 레지스트리의 시스템이 할당한 ID를 사용하도록 설정하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** >  **ID**를 차례로 선택합니다.
1. **시스템 할당** 아래에서 **상태**를 **켜기**로 설정합니다. **저장**을 선택합니다.
1. ID의 **개체 ID**를 복사합니다.

키 자격 증명 모음에 대한 ID 액세스 권한을 부여하려면 다음을 수행합니다.

1. 키 자격 증명 모음으로 이동합니다.
1. **설정** > **액세스 정책 > + 액세스 정책 추가**를 차례로 선택합니다.
1. **키 권한**을 선택하고, **가져오기**, **키 래핑 해제** 및 **키 래핑**을 선택합니다.
1. **보안 주체 선택**을 선택하고, 시스템이 할당한 관리 ID의 개체 ID 또는 레지스트리 이름을 검색합니다.  
1. **추가**, **저장**을 차례로 선택합니다.

ID를 사용하도록 레지스트리의 암호화 설정을 업데이트하려면 다음을 수행합니다.

1. 포털에서 레지스트리로 이동합니다.
1. **설정** 아래에서 **암호화** > **키 변경**을 차례로 선택합니다.
1. **ID**에서 **시스템 할당**을 선택하고, **저장**을 선택합니다.

### <a name="key-vault-firewall"></a>Key Vault 방화벽

Azure 키 자격 증명 모음이 Key Vault 방화벽을 사용하는 가상 네트워크에 배포되는 경우 다음 단계를 수행합니다.

1. 레지스트리의 시스템이 할당한 ID를 사용하도록 레지스트리 암호화를 구성합니다. 이전 섹션을 참조하세요.
2. [신뢰할 수 있는 서비스](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)에서 액세스할 수 있도록 키 자격 증명 모음을 구성합니다.

자세한 단계는 [Azure Key Vault 방화벽 및 가상 네트워크 구성](../key-vault/general/network-security.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure에서 저장 데이터 암호화](../security/fundamentals/encryption-atrest.md)에 대해 자세히 알아봅니다.
* 액세스 정책 및 [키 자격 증명 모음에 대한 액세스를 보호하는 방법](../key-vault/general/secure-your-key-vault.md)에 대해 자세히 알아봅니다.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
