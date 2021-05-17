---
title: 고객 관리 키를 설정 하 여 ISEs에서 저장소 데이터 암호화
description: Azure Logic Apps에서 ISEs (통합 서비스 환경)에 대 한 미사용 데이터를 보호 하기 위해 사용자 고유의 암호화 키를 만들고 관리 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: mijos, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: d31fbd813f0c5d63ee9eddbff5b299209618626b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98629677"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>고객 관리 키를 설정 하 여 ISEs (integration service environment)에 대 한 미사용 데이터를 암호화 Azure Logic Apps

Azure Logic Apps은 Azure Storage를 사용 하 여 [미사용 데이터](../storage/common/storage-service-encryption.md)를 저장 하 고 자동으로 암호화 합니다. 이 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. 기본적으로 Azure Storage는 Microsoft 관리 키를 사용 하 여 데이터를 암호화 합니다. Azure Storage 암호화가 작동 하는 방식에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../storage/common/storage-service-encryption.md) 및 [미사용 Azure 데이터 암호화](../security/fundamentals/encryption-atrest.md)를 참조 하세요.

논리 앱을 호스팅하기 위한 [ISE (통합 서비스 환경](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) )를 만들 때 Azure Storage에 사용 되는 암호화 키를 더 많이 제어 하려는 경우 [Azure Key Vault](../key-vault/general/overview.md)를 사용 하 여 사용자 고유의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능을 "Bring Your Own Key" (BYOK) 라고 하며, 키를 "고객 관리 키" 라고 합니다. 이 기능을 사용 하면 키에 [플랫폼 관리 키를 사용 하 여 이중 암호화 또는 *인프라 암호화*](../security/fundamentals/double-encryption.md) 를 자동으로 사용 하도록 Azure Storage. 자세히 알아보려면 [인프라 암호화를 사용 하 여 데이터 이중 암호화](../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)를 참조 하세요.

이 항목에서는 Logic Apps REST API를 사용 하 여 ISE를 만들 때 사용할 사용자 고유의 암호화 키를 설정 하 고 지정 하는 방법을 보여 줍니다. Logic Apps REST API 통해 ISE를 만드는 일반적인 단계는 [Logic Apps REST API를 사용 하 여 ise (integration service environment) 만들기](../logic-apps/create-integration-service-environment-rest-api.md)를 참조 하세요.

## <a name="considerations"></a>고려 사항

* 이 시점에서 ISE에 대 한 고객 관리 키 지원은 미국 서 부 2, 미국 동부 및 미국 서 부와 같은 Azure 지역 에서만 사용할 수 있습니다.

* 이후에는 *ISE를 만들 때만* 고객이 관리 하는 키를 지정할 수 있습니다. ISE를 만든 후에는이 키를 사용 하지 않도록 설정할 수 없습니다. 현재 ISE에 대 한 고객 관리 키 회전에 대 한 지원이 없습니다.

* 고객 관리 키를 지원 하려면 [사용자가 시스템 할당 또는 사용자 할당 관리 id](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)중 하나를 사용 하도록 설정 해야 합니다. ISE는이 id를 사용 하 여 Azure 가상 네트워크에 있거나 연결 되어 있는 가상 머신, 기타 시스템 또는 서비스와 같은 보안 리소스에 대 한 액세스를 인증할 수 있습니다. 이렇게 하면 자격 증명을 사용 하 여 로그인 할 필요가 없습니다.

* 현재, 고객이 관리 하는 키를 지원 하 고 관리 되는 id 유형을 사용 하도록 설정 된 ISE를 만들려면 HTTPS PUT 요청을 사용 하 여 Logic Apps REST API를 호출 해야 합니다.

* [ISE의 관리 되는 id에 대 한 key vault 액세스 권한을 부여](#identity-access-to-key-vault)해야 하지만 타이밍은 사용 하는 관리 id에 따라 달라 집니다.

  * **시스템 할당 관리 id**: ise를 만드는 HTTPS PUT 요청을 보낸 *후 30 분* 이내에 [ise의 관리 되는 id에 대 한 key vault 액세스 권한을 부여](#identity-access-to-key-vault)해야 합니다. 그렇지 않으면 ISE 만들기가 실패 하 고 권한 오류가 발생 합니다.

  * **사용자 할당 관리 id**: ise를 만드는 HTTPS PUT 요청을 보내기 전에 [ise의 관리 되는 id에 대 한 key vault 액세스 권한을 부여](#identity-access-to-key-vault)합니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure Portal ISE를 만들 때 [ise에 대 한 액세스를 가능 하 게 하](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) 는 동일한 [필수 구성 요소](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) 및 요구 사항

* **일시 삭제** 및 **제거 안 함** 속성을 사용 하는 Azure key vault

  이러한 속성을 사용 하는 방법에 대 한 자세한 내용은 [Azure Key Vault 일시 삭제 개요](../key-vault/general/soft-delete-overview.md) 및 [Azure Key Vault를 사용 하 여 고객 관리 키 구성](../storage/common/customer-managed-keys-configure-key-vault.md)을 참조 하세요. [Azure Key Vault](../key-vault/general/overview.md)를 처음 접하는 경우 [Azure Portal](../key-vault/general/quick-create-portal.md), [Azure CLI](../key-vault/general/quick-create-cli.md)또는 [Azure PowerShell](../key-vault/general/quick-create-powershell.md)를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보세요.

* 키 자격 증명 모음에서 다음 속성 값을 사용 하 여 만든 키입니다.

  | 속성 | 값 |
  |----------|-------|
  | **키 유형** | RSA |
  | **RSA 키 크기** | 2048 |
  | **Enabled** | 예 |
  |||

  ![고객이 관리 하는 암호화 키 만들기](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  자세한 내용은 [Azure Key Vault를 사용 하 여 고객 관리 키 구성](../storage/common/customer-managed-keys-configure-key-vault.md) 또는 Azure PowerShell 명령 [AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)를 참조 하세요.

* HTTPS PUT 요청을 사용 하 여 Logic Apps REST API를 호출 하 여 ISE를 만드는 데 사용할 수 있는 도구입니다. 예를 들어 [Postman](https://www.getpostman.com/downloads/)을 사용 하거나이 작업을 수행 하는 논리 앱을 빌드할 수 있습니다.

<a name="enable-support-key-managed-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>주요 자격 증명 모음 및 관리 되는 id 지원을 사용 하 여 ISE 만들기

Logic Apps REST API 호출 하 여 ISE를 만들려면 HTTPS PUT 요청을 만듭니다.

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 버전을 사용 하려면 ISE 커넥터에 대 한 HTTPS PUT 요청을 직접 수행 해야 합니다.

배포는 일반적으로 완료 하는 데 2 시간 이내에 수행 됩니다. 배포에 최대 4시간이 걸리는 경우가 간혹 있습니다. 배포 상태를 확인 하려면 [Azure Portal](https://portal.azure.com)의 Azure 도구 모음에서 알림 아이콘을 선택 합니다. 그러면 알림 창이 열립니다.

> [!NOTE]
> 배포가 실패하거나 ISE를 삭제하는 경우 Azure에서 서브넷을 릴리스하기까지 최대 1시간이 걸릴 수 있습니다. 이렇게 지연되면 다른 ISE에서 해당 서브넷을 다시 사용하기 전에 대기해야 할 수도 있습니다.
>
> 가상 네트워크를 삭제하면 Azure에서 서브넷이 해제되기까지 일반적으로 최대 2시간이 걸리지만 더 오래 걸릴 수도 있습니다. 
> 가상 네트워크를 삭제할 때는 그 때까지 연결된 리소스가 없는지 확인해야 합니다. 
> [가상 네트워크 삭제](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)를 참조하세요.

### <a name="request-header"></a>요청 헤더

요청 헤더에 다음 속성을 포함 합니다.

* `Content-type`:이 속성 값을로 설정 `application/json` 합니다.

* `Authorization`: 사용 하려는 Azure 구독 또는 리소스 그룹에 대 한 액세스 권한이 있는 고객에 대 한 전달자 토큰으로이 속성 값을 설정 합니다.

### <a name="request-body"></a>요청 본문

요청 본문에서 ISE 정의에 정보를 제공 하 여 이러한 추가 항목에 대 한 지원을 사용 하도록 설정 합니다.

* ISE에서 키 자격 증명 모음에 액세스 하는 데 사용 하는 관리 id
* 사용 하려는 주요 자격 증명 모음 및 고객 관리 키

#### <a name="request-body-syntax"></a>요청 본문 구문

다음은 ISE를 만들 때 사용할 속성을 설명 하는 요청 본문 구문입니다.

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>요청 본문 예제

이 예제 요청 본문은 샘플 값을 보여 줍니다.

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>키 자격 증명 모음에 대한 액세스 권한 부여

타이밍은 사용 하는 관리 id에 따라 다르지만 [ISE의 관리 되는 id에 대 한 key vault 액세스 권한을 부여](#identity-access-to-key-vault)해야 합니다.

* **시스템 할당 관리 id**: ise를 만드는 HTTPS PUT 요청을 보낸 *후 30 분* 이내에 ise의 시스템 할당 관리 id에 대 한 액세스 정책을 키 자격 증명 모음에 추가 해야 합니다. 그렇지 않으면 ISE에 대 한 만들기가 실패 하 고 사용 권한 오류가 발생 합니다.

* **사용자 할당 관리 id**: ise를 만드는 HTTPS PUT 요청을 보내기 전에 ise의 사용자 할당 관리 id에 대 한 액세스 정책을 키 자격 증명 모음에 추가 합니다.

이 작업의 경우 Azure PowerShell [AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 명령 중 하나를 사용 하거나 Azure Portal에서 다음 단계를 수행할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure key vault를 엽니다.

1. 키 자격 증명 모음 메뉴에서 **액세스 정책**  >  **추가 액세스 정책** 을 선택 합니다. 예를 들면 다음과 같습니다.

   ![시스템 할당 관리 id에 대 한 액세스 정책 추가](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. **액세스 정책 추가** 창이 열리면 다음 단계를 수행 합니다.

   1. 다음 옵션을 선택 합니다.

      | 설정 | 값 |
      |---------|--------|
      | **템플릿에서 구성 (선택 사항) 목록** | 키 관리 |
      | **키 권한** | - **키 관리 작업**: Get, List <p><p>- **암호화 작업**: 래핑 해제 키, 키 래핑 |
      |||

      !["키 관리" > "키 사용 권한"을 선택 합니다.](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. **보안 주체 선택** 에서 선택 **안 함** 을 선택 합니다. **주** 창이 열리면 검색 상자에서 ISE를 찾아 선택 합니다. 완료 되 면 추가 **선택** 을 선택  >  합니다.

      ![주 서버로 사용할 ISE를 선택 합니다.](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. **액세스 정책** 창에서 완료 되 면 **저장** 을 선택 합니다.

자세한 내용은 [Key Vault에 인증 하는 방법](../key-vault/general/authentication.md) 및 [Key Vault 액세스 정책 할당](../key-vault/general/assign-access-policy-portal.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Key Vault](../key-vault/general/overview.md)에 대해 자세히 알아봅니다.
