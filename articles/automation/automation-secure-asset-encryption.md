---
title: Automation에서 보안 자산의 암호화
description: Azure automation은 여러 수준의 암호화를 사용 하 여 보안 자산을 보호 합니다. 기본적으로 암호화는 Microsoft 관리 키를 사용 하 여 수행 됩니다. 고객은 암호화를 위해 고객 관리 키를 사용 하도록 automation 계정을 구성할 수 있습니다. 이 문서에서는 두 가지 암호화 모드와 둘 사이를 전환할 수 있는 방법에 대해 자세히 설명 합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: e645be5ddd51a4fe7e7610e7f639407d5638f746
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920921"
---
# <a name="secure-assets-in-azure-automation"></a>Azure Automation에서 자산 보호

Azure Automation의 안전한 자산에는 자격 증명, 인증서, 연결, 암호화된 변수 등이 있습니다. 이러한 자산은 여러 수준의 암호화를 사용 하 여 Azure Automation에서 보호 됩니다. 암호화에 사용 되는 최상위 키에 따라 암호화에는 두 가지 모델이 있습니다.
-   Microsoft 관리 키 사용
-   고객 관리 키 사용

## <a name="microsoft-managed-keys"></a>Microsoft에서 관리 하는 키

기본적으로 Azure Automation 계정은 Microsoft에서 관리 하는 키를 사용 합니다.

각 보안 자산은 암호화 되어 각 Automation 계정에 대해 생성 되는 고유 키 (데이터 암호화 키)를 사용 하 여 Azure Automation에 저장 됩니다. 이러한 키 자체는 AEK (계정 암호화 키) 라고 하는 각 계정에 대해 생성 된 다른 고유 키를 사용 하 여 암호화 되 고 Azure Automation 저장 됩니다. 이러한 계정 암호화 키는 Microsoft 관리 키를 사용 하 여 Azure Automation에서 암호화 및 저장 됩니다. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Key Vault를 사용 하는 고객 관리 키 (미리 보기)

사용자 고유의 키를 사용 하 여 Automation 계정 수준에서 Azure Automation의 보안 자산에 대한 암호화를 관리할 수 있습니다. Automation 계정 수준에서 고객 관리 키를 지정 하는 경우 해당 키는 automation 계정의 계정 암호화 키에 대한 액세스를 보호 하 고 제어 하는 데 사용 되며,이 키는 모든 보안 자산을 암호화 하 고 암호 해독 하는 데 사용 됩니다. 고객 관리 키를 통해 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다. 보안 자산을 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다. 

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다.  Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Automation 계정에 대해 고객이 관리 하는 키 사용

Automation 계정에 대한 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정 하면 Azure Automation는 연결 된 키 자격 증명 모음에서 고객 관리 키를 사용 하 여 계정 암호화 키를 래핑합니다. 고객 관리 키를 사용 하도록 설정 해도 성능에는 영향을 주지 않으며, 계정은 시간 지연 없이 새 키로 즉시 암호화 됩니다.

Microsoft에서 관리 하는 키를 사용 하 여 새 automation 계정이 항상 암호화 됩니다. 계정을 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 키 자격 증명 모음은 automation 계정과 연결 된 관리 되는 id에 대한 키 권한을 부여 하는 액세스 정책을 사용 하 여 프로 비전 되어야 합니다. 관리 id는 저장소 계정이 만들어진 후에만 사용할 수 있습니다.

고객 관리 키를 사용 하거나 사용 하지 않도록 설정 하거나, 키 버전을 업데이트 하거나, 다른 키를 지정 하 여 Azure Automation 보안 자산 암호화에 사용 되는 키를 수정 하면 계정 암호화 키의 암호화가 변경 되지만 보안 자산은 변경 됩니다. 에서 Azure Automation 계정을 다시 암호화 하지 않아도 됩니다.

다음 세 섹션에서는 Automation 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 하는 메커니즘을 설명 합니다. 

> [!NOTE] 
> 고객이 관리 하는 키를 사용 하도록 설정 하려면 현재 API 버전 2020-01-13-preview를 사용 하 여 Azure Automation REST API 호출 해야 합니다.

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Azure Automation에서 고객이 관리 하는 키를 사용 하기 위한 필수 구성 요소

Automation 계정에 대해 고객이 관리 하는 키를 사용 하도록 설정 하기 전에 다음 필수 구성 요소를 충족 해야 합니다.

 - 고객 관리 되 키가 Azure Key Vault에 저장 됩니다. 
 - 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다. 이러한 기능은 실수로 삭제 하는 경우 키 복구를 허용 하는 데 필요 합니다.
 - Azure Automation 암호화에서는 RSA 키만 지원 됩니다. 키에 대한 자세한 내용은 [Azure Key Vault 키, 암호 및 인증서](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)정보를 참조 하세요.
- Automation 계정과 키 자격 증명 모음은 서로 다른 구독에 있을 수 있지만 동일한 Azure Active Directory 테넌트에 있어야 합니다.

### <a name="assign-an-identity-to-the-automation-account"></a>Automation 계정에 id 할당

Automation 계정에서 고객 관리 키를 사용 하려면 automation 계정이 고객이 관리 하는 키를 저장 하는 keyvault에 대해 인증 해야 합니다. Azure Automation는 시스템 할당 관리 id를 사용 하 여 Key Vault 계정을 인증 합니다. 관리 ID에 대한 자세한 내용은 [Azure 리소스용 관리 ID란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)을 참조하세요.

다음 REST API 호출을 사용 하 여 시스템 할당 관리 id를 automation 계정으로 구성 합니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
요청 본문
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```    

Automation 계정에 대한 시스템 할당 id는 응답으로 반환 됩니다.

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>Key Vault 액세스 정책 구성

관리 id를 Automation 계정에 할당 한 후에는 고객 관리 키를 저장 하는 Key Vault에 대한 액세스를 구성 합니다. Azure Automation에는 고객 관리 키에 대한 **get**, **recover**, **wrapKey**, **UnwrapKey** 가 필요 합니다.

이러한 액세스 정책은 다음 REST API 호출을 사용 하 여 설정할 수 있습니다.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```
요청 본문

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE] 
> **Tenantid** 및 **objectId** 필드는 automation 계정에 대한 관리 되는 id의 응답에서 각각 **identity. Tenantid** 및 **identity. principalid** 의 값을 제공 해야 합니다.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>고객 관리 키를 사용 하도록 automation 계정 구성 변경

마지막으로 다음 REST API 호출을 사용 하 여 automation 계정을 마이크로 Sft 관리 키에서 고객 관리 키로 전환할 수 있습니다.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
요청 본문

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```
샘플 응답

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>고객이 관리 하는 키 수명 주기 관리

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 automation 계정을 업데이트 해야 합니다. 

키를 회전 해도 automation 계정에서 보안 자산의 다시 암호화는 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) 또는 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure Automation 의해 암호화 키에 액세스할 수 없으므로 automation 계정의 모든 보안 자산에 대한 액세스가 효과적으로 차단 됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../key-vault/key-vault-overview.md) 
- [Azure Automation의 인증서 자산](shared-resources/certificates.md)
- [Azure Automation의 자격 증명 자산](shared-resources/credentials.md)
- [Azure Automation의 변수 자산](shared-resources/variables.md)
