---
title: 휴지 상태의 Azure Event Hubs 데이터를 암호화 하기 위한 고유한 키 구성
description: 이 문서에서는 Azure Event Hubs 데이터 rest를 암호화 하기 위한 고유한 키를 구성 하는 방법에 대한 정보를 제공 합니다.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: spelluru
ms.openlocfilehash: 50d12a0aba9018b1ecb30c018249e8f94ebe6d95
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903281"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 미사용 Azure Event Hubs 데이터를 암호화 하기 위한 고객 관리 키 구성
Azure Event Hubs는 Azure SSE (Azure Storage 서비스 암호화)를 사용 하 여 미사용 데이터의 암호화를 제공 합니다. Event Hubs는 Azure Storage를 사용 하 여 데이터를 저장 하 고, 기본적으로 Azure Storage와 함께 저장 되는 모든 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 

## <a name="overview"></a>개요
이제 Azure Event Hubs는 Microsoft에서 관리 하는 키 또는 고객이 관리 하는 키 (Bring Your Own Key – BYOK)를 사용 하 여 미사용 데이터를 암호화 하는 옵션을 지원 합니다. 이 기능을 사용 하면 미사용 Azure Event Hubs 데이터를 암호화 하는 데 사용 되는 고객 관리 키에 대한 액세스를 만들고, 회전 하 고, 사용 하지 않도록 설정할 수 있습니다.

BYOK 기능을 사용 하도록 설정 하는 작업은 네임 스페이스에서 한 번만 설정 하면 됩니다.

> [!NOTE]
> BYOK 기능은 [Event Hubs 전용 단일 테넌트](event-hubs-dedicated-overview.md) 클러스터에서 지원 됩니다. 표준 Event Hubs 네임 스페이스에 대해 사용 하도록 설정할 수 없습니다.

Azure Key Vault를 사용 하 여 키를 관리 하 고 키 사용을 감사할 수 있습니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../key-vault/key-vault-overview.md)

이 문서에서는 Azure Portal를 사용 하 여 고객이 관리 하는 키로 key vault를 구성 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 주요 자격 증명 모음을 만드는 방법을 알아보려면 [빠른 시작: Azure Portal을 사용 하 여 Azure Key Vault에서 비밀 설정 및 검색](../key-vault/quick-create-portal.md)을 참조 하세요.

> [!IMPORTANT]
> Azure Event Hubs에서 고객 관리 키를 사용 하려면 키 자격 증명 모음에 두 개의 필수 속성이 구성 되어 있어야 합니다. **일시 삭제** 및 **제거 안 함**입니다. 이러한 속성은 Azure Portal에서 새 키 자격 증명 모음을 만들 때 기본적으로 사용 하도록 설정 됩니다. 그러나 기존 키 자격 증명 모음에서 이러한 속성을 사용 하도록 설정 해야 하는 경우에는 PowerShell 또는 Azure CLI를 사용 해야 합니다.

## <a name="enable-customer-managed-keys"></a>고객이 관리 하는 키 사용
Azure Portal에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Event Hubs Dedicated 클러스터로 이동 합니다.
1. BYOK를 사용 하도록 설정 하려는 네임 스페이스를 선택 합니다.
1. Event Hubs 네임 스페이스의 **설정** 페이지에서 **암호화**를 선택 합니다. 
1. 다음 그림에 표시 된 것 처럼 **미사용에서 고객이 관리 하는 키 암호화** 를 선택 합니다. 

    ![고객 관리 키 사용](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>키를 사용 하 여 키 자격 증명 모음 설정
고객이 관리 하는 키를 사용 하도록 설정한 후에는 고객 관리 키를 Azure Event Hubs 네임 스페이스와 연결 해야 합니다. Event Hubs은 Azure Key Vault만 지원 합니다. 이전 섹션에서 **고객이 관리 하는 키로 암호화** 옵션을 사용 하도록 설정 하는 경우 키를 Azure Key Vault으로 가져와야 합니다. 또한 키에는 **일시 삭제** 및 키에 대해 구성 된 **제거 안 함** 이 있어야 합니다. 이러한 설정은 [PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 또는 [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection)를 사용 하 여 구성할 수 있습니다.

1. 새 키 자격 증명 모음을 만들려면 Azure Key Vault [빠른](../key-vault/key-vault-overview.md)시작을 수행 합니다. 기존 키를 가져오는 방법에 대한 자세한 내용은 [키, 암호 및 인증서 정보](../key-vault/about-keys-secrets-and-certificates.md)를 참조 하세요.
1. 자격 증명 모음을 만들 때 일시 삭제 및 제거 보호를 모두 켜려면 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용 합니다.

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 이미 일시 삭제를 사용 하도록 설정한 상태에서 기존 자격 증명 모음에 제거 방지를 추가 하려면 [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) 명령을 사용 합니다.

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 다음 단계를 수행 하 여 키를 만듭니다.
    1. 새 키를 만들려면 **설정** 아래 **키** 메뉴에서 **생성/가져오기**를 선택합니다.
        
        ![생성/가져오기 단추를 선택 합니다.](./media/configure-customer-managed-key/select-generate-import.png)
    1. **옵션**을 **생성**으로 설정하고 키에 이름을 지정합니다.

        ![키 만들기](./media/configure-customer-managed-key/create-key.png) 
    1. 이제이 키를 선택 하 여 드롭다운 목록에서 암호화할 Event Hubs 네임 스페이스와 연결할 수 있습니다. 

        ![키 자격 증명 모음에서 키 선택](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. 키에 대한 세부 정보를 입력 하 고 **선택**을 클릭 합니다. 이렇게 하면 고객이 관리 하는 키를 사용 하 여 네임 스페이스에 있는 미사용 데이터를 암호화할 수 있습니다. 


## <a name="rotate-your-encryption-keys"></a>암호화 키 회전
Azure 키 자격 증명 모음 회전 메커니즘을 사용 하 여 키 자격 증명 모음에서 키를 회전할 수 있습니다. 자세한 내용은 [키 회전 및 감사 설정](../key-vault/key-vault-key-rotation-log-monitoring.md)을 참조 하세요. 키 회전을 자동화 하도록 활성화 및 만료 날짜를 설정할 수도 있습니다. Event Hubs 서비스는 새 키 버전을 검색 하 고 자동으로 사용을 시작 합니다.

## <a name="revoke-access-to-keys"></a>키에 대한 액세스 취소
암호화 키에 대한 액세스를 취소 해도 Event Hubs의 데이터는 제거 되지 않습니다. 그러나 Event Hubs 네임 스페이스에서 데이터에 액세스할 수 없습니다. 액세스 정책을 통해 또는 키를 삭제 하 여 암호화 키를 해지할 수 있습니다. 키 자격 증명 [모음에 대한 보안 액세스](../key-vault/key-vault-secure-your-key-vault.md)에서 액세스 정책 및 키 자격 증명 모음 보안에 대해 자세히 알아보세요.

암호화 키가 해지 되 면 암호화 된 네임 스페이스의 Event Hubs 서비스가 작동 하지 않게 됩니다. 키에 대한 액세스를 사용 하도록 설정 하거나 delete 키를 복원 하는 경우 암호화 된 Event Hubs 네임 스페이스의 데이터에 액세스할 수 있도록 Event Hubs 서비스에서 키를 선택 합니다.

## <a name="set-up-diagnostic-logs"></a>진단 로그 설정 
BYOK 사용 네임 스페이스에 대한 진단 로그를 설정 하면 고객이 관리 하는 키를 사용 하 여 네임 스페이스를 암호화할 때 작업에 대한 필수 정보가 제공 됩니다. 이러한 로그를 사용 하도록 설정 하 고 나중에 이벤트 허브로 스트림 하거나, log analytics를 통해 분석 하거나, 사용자 지정 분석을 수행 하기 위해 저장소로 스트리밍할 수 있습니다. 진단 로그에 대한 자세한 내용은 [Azure 진단 로그 개요](../azure-monitor/platform/platform-logs-overview.md)를 참조 하세요.

## <a name="enable-user-logs"></a>사용자 로그 사용
고객 관리 키에 대한 로그를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 BYOK를 사용 하는 네임 스페이스로 이동 합니다.
1. **모니터링**아래에서 **진단 설정** 을 선택 합니다.

    ![진단 설정 선택](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **+ 진단 설정 추가**를 선택 합니다. 

    ![진단 설정 추가를 선택 합니다.](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. **이름을** 입력 하 고 로그를 스트리밍할 위치를 선택 합니다.
1. **Customermanagedkeyuserlogs** 를 선택 하 고 **저장**을 선택 합니다. 이 작업을 통해 네임 스페이스에 대한 BYOK 로그가 활성화 됩니다.

    ![고객 관리 키 사용자 로그 옵션을 선택 합니다.](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>로그 스키마 
모든 로그는 JSON(JavaScript Object Notation) 형식으로 저장됩니다. 각 항목에는 다음 표에 설명 된 형식을 사용 하는 문자열 필드가 있습니다. 

| 이름 | Description |
| ---- | ----------- | 
| TaskName | 실패한 작업에 대한 설명입니다. |
| ActivityId | 추적에 사용 되는 내부 ID입니다. |
| category | 태스크의 분류를 정의 합니다. 예를 들어 키 자격 증명 모음의 키를 사용 하지 않도록 설정 하는 경우 정보 범주 이거나 키를 래핑 해제할 수 없는 경우 오류가 발생할 수 있습니다. |
| resourceId | Azure Resource Manager 리소스 ID |
| keyVault | 키 자격 증명 모음의 전체 이름입니다. |
| key | Event Hubs 네임 스페이스를 암호화 하는 데 사용 되는 키 이름입니다. |
| 버전 | 사용 되는 키의 버전입니다. |
| operation(작업) | 키 자격 증명 모음의 키에 대해 수행 되는 작업입니다. 예를 들어 키, 줄 바꿈 또는 래핑 해제를 사용 하거나 사용 하지 않도록 설정 합니다. |
| 코드 | 작업과 연결 된 코드입니다. 예: 오류 코드 404는 키를 찾을 수 없음을 의미 합니다. |
| message | 작업과 관련 된 오류 메시지 |

고객 관리 키에 대한 로그의 예는 다음과 같습니다.

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>문제 해결
모범 사례로, 이전 섹션에 표시 된 것과 같이 항상 로그를 사용 하도록 설정 합니다. BYOK 암호화를 사용 하는 경우 활동을 추적 하는 데 도움이 됩니다. 문제의 범위를 지정 하는 데에도 도움이 됩니다.

BYOK 암호화를 사용 하는 경우 다음은 일반적인 오류 코드입니다.

| 실행력 | 오류 코드 | 데이터의 결과 상태 |
| ------ | ---------- | ----------------------- | 
| 키 자격 증명 모음에서 래핑/래핑 해제 권한 제거 | 403 |    액세스할 수 없음 |
| 줄 바꿈/래핑 해제 권한이 부여 된 AAD 사용자에서 AAD 역할 멤버 자격 제거 | 403 |  액세스할 수 없음 |
| 키 자격 증명 모음에서 암호화 키를 삭제 합니다. | 404 | 액세스할 수 없음 |
| 키 자격 증명 모음 삭제 | 404 | 액세스할 수 없습니다 (필요한 설정인 일시 삭제를 사용 하는 것으로 가정). |
| 암호화 키의 만료 기간을 변경 하 여 이미 만료 된 경우 | 403 |   액세스할 수 없음  |
| 키 암호화 키가 활성화 되지 않도록 NBF (이전 아님) 변경 | 403 | 액세스할 수 없음  |
| 키 자격 증명 모음 방화벽에 대해 **MSFT 서비스 허용** 옵션을 선택 하거나, 암호화 키가 있는 key vault에 대한 네트워크 액세스를 차단 합니다. | 403 | 액세스할 수 없음 |
| 다른 테넌트로 키 자격 증명 모음 이동 | 404 | 액세스할 수 없음 |  
| 일시적인 네트워크 문제 또는 DNS/AAD/MSI 중단 |  | 캐시 된 데이터 암호화 키를 사용 하 여 액세스 가능 |

> [!IMPORTANT]
> BYOK 암호화를 사용 하는 네임 스페이스에서 지역 DR을 사용 하도록 설정 하려면 페어링의 보조 네임 스페이스가 전용 클러스터에 있어야 하 고이 네임 스페이스에 시스템 할당 관리 id가 설정 되어 있어야 합니다. 자세히 알아보려면 [Azure 리소스에 대한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.
- [Event Hubs 개요](event-hubs-about.md)
- [Key Vault 개요](../key-vault/key-vault-overview.md)




