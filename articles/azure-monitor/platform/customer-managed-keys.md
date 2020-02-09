---
title: 고객 관리 키 구성 Azure Monitor
description: CMK (고객이 관리 하는 키)를 구성 하 여 Azure Key Vault 키를 사용 하 여 Log Analytics 작업 영역의 데이터를 암호화 하는 방법에 대 한 정보 및 단계입니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 02/05/2020
ms.openlocfilehash: eff751465c7b64429968b0305e6ad483943c374b
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048178"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>고객 관리 키 구성 Azure Monitor 

이 문서에서는 Log Analytics 작업 영역 및 Application Insights 구성 요소에 대 한 CMK (고객 관리 키)를 구성 하는 단계와 배경 정보를 제공 합니다. 구성 된 후에는 작업 영역 또는 구성 요소에 전송 된 모든 데이터가 Azure Key Vault 키로 암호화 됩니다.

구성 전에 [제한 사항 및 제약 조건을](#limitations-and-constraints) 검토 하는 것이 좋습니다.

## <a name="disclaimers"></a>고지 사항

- Azure Monitor CMK는 초기 액세스 기능이 며 등록 된 구독에 사용할 수 있습니다.

- 이 문서에서 설명 하는 CMK 배포는 초기 액세스 기능 이더라도 프로덕션 품질로 제공 되며 지원 됩니다.

- CMK 기능은 ADX (Azure 데이터 탐색기) 클러스터용 전용 데이터 저장소 클러스터에서 제공 되며, 매일 1TB를 보내는 고객에 게 적합 합니다. 

- CMK 가격 책정 모델은 현재 사용할 수 없으며,이 문서에서 다루지 않습니다. 전용 ADX 클러스터에 대 한 가격 책정 모델은 1 번째 사분기 (CY) 2020에 예상 되며 기존 CMK 배포에 적용 됩니다.

- 이 문서에서는 Log Analytics 작업 영역에 대 한 CMK 구성에 대해 설명 합니다. Application Insights 구성 요소에 대 한 CMK는 부록에 나열 된 경우에도이 문서를 사용 하 여 지원 됩니다.

> [!NOTE]
> Log Analytics 및 Application Insights는 동일한 데이터 저장소 플랫폼과 쿼리 엔진을 사용 합니다.
> 이러한 두 저장소는 Log Analytics Application Insights를 통합 하 여 Azure Monitor에서 단일 통합 로그 저장소를 만드는 과정을 통해 함께 제공 됩니다. 이 변경은 2020 년의 두 번째 분기에 대해 계획 됩니다. 그러면 Application Insights 데이터에 대 한 CMK를 배포할 필요가 없는 경우 통합이 완료 될 때까지 대기 하는 것이 좋습니다. 따라서 로그로 마이그레이션한 후에는 CMK를 다시 구성 해야 합니다. 분석 작업 영역. 일별 1TB는 클러스터 수준에서 적용 되며, 2 분기 동안 통합이 완료 될 때까지 Application Insights 및 Log Analytics 별도의 클러스터가 필요 합니다.

## <a name="customer-managed-key-cmk-overview"></a>CMK (고객 관리 키) 개요

[미사용 암호화](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) 는 조직의 일반적인 개인 정보 및 보안 요구 사항입니다. 암호화 또는 암호화 키를 긴밀 하 게 관리 하는 다양 한 옵션을 사용 하 여 Azure에서 미사용 암호화를 완전히 관리할 수 있습니다.

Azure Monitor 데이터 저장소는 Azure Storage에 저장 되어 있는 동안 Azure 관리 키를 사용 하 여 미사용 데이터를 암호화 하는 모든 데이터를 확인 합니다. 또한 Azure Monitor는 시스템 할당 [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 인증을 사용 하 여 액세스 하는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)에 저장 된 사용자 고유의 키를 사용 하는 데이터 암호화 옵션을 제공 합니다. 이 키는 [소프트웨어 또는 하드웨어 HSM으로 보호](https://docs.microsoft.com/azure/key-vault/key-vault-overview)될 수 있습니다.
암호화 사용 Azure Monitor는 암호화가 작동 하는 [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) 방식과 동일 합니다.

줄 바꿈 및 래핑 해제 작업에 Key Vault Azure Monitor 저장소 액세스 빈도는 6 ~ 60 초 사이입니다. Azure Monitor 저장소는 항상 1 시간 이내에 키 사용 권한을 변경 합니다.

또한 지난 14 일간 수집 데이터는 효율적인 쿼리 엔진 작업을 위해 핫 캐시 (SSD 지원)로 유지 됩니다. CMK 구성에 관계 없이이 데이터는 Microsoft 키를 사용 하 여 암호화 된 상태로 유지 되지만 SSD를 2020 초기에 암호화 된 상태로 유지 하기 위해 노력 하 고 있습니다.

## <a name="how-cmk-works-in-azure-monitor"></a>Azure Monitor에서 CMK가 작동 하는 방식

Azure Monitor는 시스템 할당 관리 id를 활용 하 여 Azure Key Vault에 대 한 액세스 권한을 부여 합니다. 시스템 할당 관리 id는 단일 Azure 리소스와만 연결할 수 있습니다. ADX 클러스터 (Azure Monitor 데이터 저장소)의 id는 클러스터 수준에서 지원 되며,이는 CMK 기능이 전용 ADX 클러스터에서 제공 되도록 규정 합니다. 여러 작업 영역에서 CMK를 지원 하기 위해 새 Log Analytics 리소스 (*클러스터*)는 Key Vault와 Log Analytics 작업 영역 간에 중간 id 연결로 수행 됩니다. 이 개념은 시스템 할당 id 제약 조건을 준수 하 고, 연결 된 모든 작업 영역의 데이터는 Key Vault 키로 보호 되는 동안 ADX 클러스터와 Log Analytics *클러스터* 리소스 사이에서 id가 유지 됩니다. 언더레이 ADX 클러스터 저장소는 *클러스터* 리소스와 연결 된\'관리 되는 id를 사용 하 여 Azure Active Directory를 통해 Azure Key Vault 인증 하 고 액세스 합니다.

![CMK 개요](media/customer-managed-keys/cmk-overview.png)
1.  고객의 Key Vault입니다.
2.  Key Vault에 대 한 권한이 있는 관리 id가 있는 고객의 Log Analytics 클러스터 리소스-id는 ADX 클러스터 (데이터 저장소) 수준에서 지원 됩니다.
3.  전용 ADX 클러스터를 Azure Monitor 합니다.
4.  CMK 암호화에 대 한 클러스터 리소스와 연결 된 고객의 작업 영역입니다.

## <a name="encryption-keys-management"></a>암호화 키 관리

저장소 데이터 암호화에는 세 가지 유형의 키가 있습니다.

- **KEK** 키 암호화 키 Key Vault (cmk)
- **Aek** -계정 암호화 키
- **Dek** -데이터 암호화 키

다음 규칙이 적용됩니다.

- ADX 저장소 계정은 AEK 라는 모든 저장소 계정에 대 한 고유한 암호화 키를 생성 합니다.

- AEK는 디스크에 기록 되는 데이터의 각 블록을 암호화 하는 데 사용 되는 키인 DEKs를 파생 하는 데 사용 됩니다.

- Key Vault에서 키를 구성 하 고 *클러스터* 리소스에서 키를 참조 하는 경우 Azure Key Vault에서 AEK를 KEK Azure Storage 래핑합니다.

- KEK는 Key Vault을 유지 하지 않으며 HSM 키의 경우 하드웨어를 그대로 유지 합니다.

- Azure Storage는 *클러스터* 리소스와 연결 된 관리 되는 id를 사용 하 여 인증 하 고 Azure Active Directory를 통해 Azure Key Vault에 액세스 합니다.

- 읽기/쓰기 작업의 경우 Azure Storage는 암호화 및 암호 해독 작업을 수행 하기 위해 AEK를 래핑하고 래핑 해제 하는 요청을 Azure Key Vault 보냅니다.

## <a name="cmk-provisioning-procedure"></a>CMK 프로 비전 절차

CMK 구성 Application Insights 3 단계와 6 단계에 대 한 부록 콘텐츠를 따르세요.

1. Subscription 허용 목록-이 초기 액세스 기능에 필요 합니다.
2. Azure Key Vault 만들기 및 키 저장
3. *클러스터* 리소스 만들기
4. Key Vault에 대 한 사용 권한 부여
5. ADX 클러스터 (Azure Monitor 데이터 저장소) 프로 비전
6. Log Analytics 작업 영역 연결

이 프로시저는 현재 UI에서 지원 되지 않으며 프로 비전 프로세스는 REST API를 통해 수행 됩니다.

> [!IMPORTANT]
> 모든 API 요청은 요청 헤더에 전달자 권한 부여 토큰을 포함 해야 합니다.

예를 들면 다음과 같습니다.

```rst
GET
https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>?api-version=2015-11-01-preview
Authorization: Bearer eyJ0eXAiO....
```

여기서 *eyJ0eXAiO* 는 전체 권한 부여 토큰을 나타냅니다. 

다음 방법 중 하나를 사용 하 여 토큰을 가져올 수 있습니다.

1. [앱 등록](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) 메서드를 사용 합니다.

2. Azure 포털에서 다음을 수행합니다.
    1. "개발자 도구 (F12)"의 Azure Portal로 이동 합니다.
    1. "Batch? api-version" 인스턴스 중 하나에서 "요청 헤더" 아래에 있는 권한 부여 문자열을 찾습니다. "권한 부여: 전달자 \<토큰\>"와 같습니다. 
    1. 다음 예제에 따라이를 복사 하 여 API 호출에 추가 합니다.

3. Azure REST 설명서 사이트로 이동 합니다. 모든 API에서 "체험"을 누르고 전달자 토큰을 복사 합니다.

### <a name="subscription-whitelisting"></a>구독 허용 목록

CMK 기능은 초기 액세스 기능입니다. *클러스터* 리소스를 만들려는 구독은 Azure 제품 그룹에서 미리 허용 목록 되어야 합니다. Microsoft에 연락처를 사용 하 여 구독 Id를 제공 합니다.

> [!IMPORTANT]
> CMK 기능은 지역입니다. Azure Key Vault, Storage 계정, *클러스터* 리소스 및 연결 된 Log Analytics 작업 영역이 같은 지역에 있어야 하지만 다른 구독에 있을 수 있습니다.

### <a name="storing-encryption-key-kek"></a>암호화 키 저장 (KEK)

Azure Key Vault 리소스를 만든 다음 데이터 암호화에 사용할 키를 생성 하거나 가져옵니다.

키와 Azure Monitor 데이터에 대 한 액세스를 보호 하려면 Azure Key Vault를 복구 가능으로 구성 해야 합니다.

이러한 설정은 CLI 및 PowerShell을 통해 사용할 수 있습니다.
- [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 를 켜야 합니다.
- 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하지 않도록 [보호 하려면 제거 보호](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) 를 켜야 합니다.

### <a name="create-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 작업 영역 간의 중간 id 연결로 사용 됩니다. 구독이 허용 목록 된 것을 확인 한 후에는 작업 영역이 있는 지역에서 Log Analytics *클러스터* 리소스를 만듭니다. Application Insights 및 Log Analytics에는 별도의 클러스터 리소스가 필요 합니다. 클러스터 리소스의 형식은 생성 시 "clusterType" 속성을 ' LogAnalytics ' 또는 ' ApplicationInsights '로 설정 하 여 정의 됩니다. 클러스터 리소스 유형은 변경할 수 없습니다.

CMK를 구성 Application Insights이 단계에 대해서는 부록 콘텐츠를 따르세요.

**만들기**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
   "properties": {
      "clusterType": "LogAnalytics"
    },
   "identity": {
      "type": "systemAssigned"
   }
}
```
"clusterType" 값은 Application Insights CMK에 대 한 "ApplicationInsights"입니다.

**응답**

만든 시간에는 id가 *클러스터* 리소스에 할당 됩니다.

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",    //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}

```
"principalId"는 *클러스터* 리소스에 대 한 관리 id 서비스에 의해 생성 되는 GUID입니다.

> [!IMPORTANT]
> "클러스터 id" 값은 다음 단계에서 필요 하므로 복사 하 고 유지 합니다.

어떤 이유로 든 *클러스터* 리소스를 삭제 하는 경우, 예를 들어 다른 이름 또는 clustertype을 사용 하 여 만들려면 다음 API 호출을 사용 합니다.

```rst
DELETE
https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
```

### <a name="grant-key-vault-permissions"></a>Key Vault 권한 부여

Key Vault를 업데이트 하 고 클러스터 리소스에 대 한 액세스 정책을 추가 합니다. 그런 다음 Key Vault에 대 한 권한이 데이터 암호화에 사용 되는 Azure Monitor 저장소에 전파 됩니다.
Azure Portal에서 Key Vault을 열고 "액세스 정책"을 클릭 한 다음 "+ 액세스 정책 추가"를 클릭 하 여 다음 설정을 사용 하 여 새 정책을 만듭니다.

- 키 사용 권한: ' 가져오기 ', ' 줄 바꿈 키 ' 및 ' 래핑 해제 키 ' 권한을 선택 합니다.

- 보안 주체 선택: 이전 단계의 응답에서 "clusterId" 값인 클러스터 id를 입력 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/grant-key-vault-permissions.png)

사용자의 키와 Azure Monitor 데이터에 대 한 액세스를 보호 하기 위해 Key Vault가 복구 가능한 것으로 구성 되었는지 확인 하려면 *Get* 권한이 필요 합니다.

Azure Resource Manager에서 *클러스터* 리소스가 전파 될 때까지 몇 분 정도 걸립니다. *클러스터* 리소스를 만든 후 즉시이 액세스 정책을 구성 하면 일시적인 오류가 발생할 수 있습니다. 이 경우 몇 분 후에 다시 시도 하세요.

### <a name="update-cluster-resource-with-key-identifier-details"></a>키 식별자 세부 정보를 사용 하 여 클러스터 리소스 업데이트

이 단계에서는 Key Vault의 이후 주요 버전 업데이트를 적용 합니다. Key Vault *키 식별자* 세부 정보를 사용 하 여 *클러스터* 리소스를 업데이트 하 여 Azure Monitor 저장소에서 새 키 버전을 사용할 수 있도록 합니다. 키 식별자 정보를 가져오려면 Azure Key Vault에서 키의 현재 버전을 선택 합니다.

![Key Vault 권한 부여](media/customer-managed-keys/key-identifier-8bit.png)

키 식별자 세부 정보를 사용 하 여 *클러스터* 리소스 KeyVaultProperties를 업데이트 합니다.

**Update**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
            KeyName: "<key-name>",
            KeyVersion: "<current-version>"
            },
   },
   "location":"<region-name>",
   "identity": { 
        "type": "systemAssigned" 
        }
}
```
"KeyVaultProperties"에는 Key Vault 키 식별자 정보가 포함 되어 있습니다.

**응답**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
       "KeyVaultProperties": {
            KeyVaultUri: "https://key-vault-name.vault.azure.net",
            KeyName: "key-name",
            KeyVersion: "current-version"
            },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>ADX 클러스터 (Azure Monitor 데이터 저장소) 프로 비전

이전 단계를 완료 한 후에는이 기능의 초기 액세스 기간 동안 ADX 클러스터가 제품 팀에서 수동으로 프로 비전 됩니다. Microsoft에서 제공 하는 채널을 사용 하 여 다음 세부 정보를 제공 합니다.

- 위의 단계가 성공적으로 완료 되었음을 확인 합니다.

- 이전 단계의 JSON 응답입니다. Get API 호출을 사용 하 여 언제 든 지 검색할 수 있습니다.

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
   Authorization: Bearer <token>
   ```

   **응답**
   ```json
   {
     "identity": {
       "type": "SystemAssigned",
       "tenantId": "tenant-id",
       "principalId": "principal-Id"
     },
     "properties": {
          "KeyVaultProperties": {
               KeyVaultUri: "https://key-vault-name.vault.azure.net",
               KeyName: "key-name",
               KeyVersion: "current-version"
               },
       "provisioningState": "Succeeded",
       "clusterType": "LogAnalytics", 
       "clusterId": "cluster-id"
     },
     "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
     "name": "cluster-name",
     "type": "Microsoft.OperationalInsights/clusters",
     "location": "region-name"
   }
   ```

### <a name="workspace-association-to-cluster-resource"></a>*클러스터* 리소스에 작업 영역 연결

> [!NOTE]
> 이 단계는 **ADX 클러스터 (Azure Monitor 데이터 저장소) 프로 비전이** 충족 된 Microsoft 채널을 통해 제품 그룹에서 확인을 받은 후에 **만** 수행 해야 합니다. 이 **프로 비전**전에 작업 영역을 연결 하 고 데이터를 수집 하는 경우 데이터가 삭제 되며 복구할 수 없습니다.

CMK를 구성 Application Insights이 단계에 대해서는 부록 콘텐츠를 따르세요.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2019-08-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```
*ClusterDefinitionId* 은 이전 단계의 응답에서 제공 된 *clusterId* 값입니다.

**응답**

```json
{
  "properties": {
    "WriteAccessResourceId": "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name/linkedservices/cluster",
  "name": "workspace-name/cluster",
  "type": "microsoft.operationalInsights/workspaces/linkedServices",
}
```

연결 후 작업 영역에 전송 된 데이터는 관리 되는 키로 암호화 된 상태로 저장 됩니다.

### <a name="workspace-association-verification"></a>작업 영역 연결 확인
작업 영역 [– Get](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) response를 살펴보면 작업 영역이 *클러스터* 리소스에 연결 되어 있는지 확인할 수 있습니다. 연결 된 작업 영역에는 *클러스터* 리소스 ID를 포함 하는 ' clusterresourceid ' 속성이 있습니다.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview
```

**응답**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": days,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true/false,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) 해지

Azure Monitor 저장소는 항상 1 시간 이내에 키 사용 권한의 변경 사항을 적용 하 고, 일반적으로 더 일찍 저장소를 사용할 수 없게 됩니다. *클러스터* 리소스와 연결 된 작업 영역에 대 한 모든 데이터 수집 삭제 되 고 쿼리가 실패 합니다. 이전에 수집 데이터는 키가 해지 되는 동안 Azure Monitor 저장소에서 액세스할 수 없는 상태로 유지 되 고 작업 영역은 삭제 되지 않습니다. 액세스할 수 없는 데이터는 데이터 보존 정책에 의해 제어 되며 보존에 도달 하면 제거 됩니다.

저장소는 암호화 키 래핑 해제를 시도 하 고 30 분 내에 액세스 한 후 데이터 수집 및 쿼리를 다시 시작할 수 있도록 주기적으로 Key Vault를 폴링합니다.

## <a name="cmk-kek-rotation"></a>CMK (KEK) 회전

CMK를 회전 하려면 새 Azure Key Vault 키 버전을 사용 하 여 *클러스터* 리소스를 명시적으로 업데이트 해야 합니다. 새 키 버전을 사용 하 여 Azure Monitor를 업데이트 하려면 " *키 식별자* 세부 정보를 사용 하 여 *클러스터* 리소스 업데이트" 단계의 지침을 따르세요.

Key Vault에서 키를 업데이트 하 고 *클러스터* 리소스 *에서 새 *키 식별자* 정보를 업데이트 하지 않는 경우 Azure Monitor 저장소에서 이전 키를 계속 사용 합니다.

## <a name="limitations-and-constraints"></a>제한 사항 및 제약 조건

- CMK 기능은 ADX 클러스터 수준에서 지원 되며 전용 Azure Monitor ADX 클러스터가 필요 합니다.

- 구독 당 최대 *클러스터* 리소스 수는 5 개로 제한 됩니다.

- 작업 영역에 대 한 *클러스터* 리소스 연결은 adx 클러스터 프로 비전이 수행 된 제품 그룹에서 확인을 받은 후에만 수행 해야 합니다. 이 프로 비전 전에 전송 된 데이터는 삭제 되며 복구할 수 없습니다.

- Cmk 암호화는 CMK 구성 후에 새로 수집 데이터에 적용 됩니다. CMK 구성 이전에 수집 된 데이터는 Microsoft 키로 암호화 된 상태로 유지 됩니다. 구성 전후에 데이터를 원활 하 게 쿼리할 수 있습니다.

- 작업 영역이 *클러스터* 리소스와 연결 되 면 데이터는 키로 암호화 되어 Azure Key Vault KEK 없이 액세스할 수 없기 때문에 *클러스터* 리소스에서 연결을 해제할 수 없습니다.

- Azure Key Vault를 복구할 수 있는 것으로 구성 해야 합니다. 이러한 속성은 기본적으로 사용 하도록 설정 되어 있지 않으며 CLI 및 PowerShell을 사용 하 여 구성 해야 합니다.

  - [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 를 켜야 합니다.
  - 일시 삭제 후에도 비밀/자격 증명 모음을 강제로 삭제 하지 않도록 [보호 하려면 제거 보호](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) 를 켜야 합니다.

- Application Insights 및 Log Analytics에는 별도의 *클러스터* 리소스가 필요 합니다. *클러스터* 리소스의 형식은 생성 시 "clustertype" 속성을 ' loganalytics ' 또는 ' applicationinsights '로 설정 하 여 정의 됩니다. *클러스터* 리소스 유형은 변경할 수 없습니다.

- 다른 리소스 그룹 또는 구독에 대 한 *클러스터* 리소스 이동은 현재 지원 되지 않습니다.

- Azure Key Vault, *클러스터* 리소스 및 연결 된 작업 영역은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테넌트에 있어야 하지만 다른 구독에 있을 수 있습니다.

- 다른 *클러스터* 리소스와 연결 된 경우 *클러스터* 리소스에 대 한 작업 영역 연결이 실패 합니다.

## <a name="troubleshooting-and-management"></a>문제 해결 및 관리

- Key Vault 가용성
    - 정상적인 작업에서 저장소는 짧은 기간 동안 AEK를 캐시 하 여 주기적으로 래핑 해제 Key Vault으로 다시 이동 합니다.
    
    - 일시적인 연결 오류입니다. 저장소는 키가 짧은 시간 동안 캐시에 유지 되도록 허용 하 여 일시적인 오류 (시간 제한, 연결 오류, DNS 문제)를 처리 하 고,이를 통해 작은 문제 가용성을 극복 합니다. 쿼리 및 수집 기능은 중단 없이 계속 됩니다.
    
    - 라이브 사이트, 약 30 분을 사용할 수 없게 되 면 저장소 계정을 사용할 수 없게 됩니다. 데이터 손실을 방지 하기 위해 쿼리 기능을 사용할 수 없으며 Microsoft 키를 사용 하 여 몇 시간 동안 수집 데이터를 캐시 합니다. Key Vault에 대 한 액세스가 복원 되 면 쿼리를 사용할 수 있게 되며 임시 캐시 된 데이터는 데이터 저장소에 수집 CMK를 사용 하 여 암호화 됩니다.

- *클러스터* 리소스를 만들고 KeyVaultProperties를 즉시 지정 하는 경우 시스템 Id가 *클러스터* 리소스에 할당 될 때까지 액세스 정책을 정의할 수 없으므로 작업이 실패할 수 있습니다.

- KeyVaultProperties를 사용 하 여 기존 *클러스터* 리소스를 업데이트 하 고 ' Get ' 키 액세스 정책이 Key Vault에 없는 경우 작업이 실패 합니다.

- 작업 영역에 연결 된 *클러스터* 리소스를 삭제 하려고 하면 삭제 작업은 실패 합니다.

- 이 API를 사용 하 여 리소스 그룹에 대 한 모든 *클러스터* 리소스를 가져옵니다.

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **응답**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "properties": {
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

- 이 API 호출을 사용 하 여 구독에 대 한 모든 *클러스터* 리소스를 가져옵니다.

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```
    
  **응답**
    
  ' 리소스 그룹에 대 한*클러스터* 리소스 '와 동일한 응답 이지만 구독 범위에 있습니다.
    
- 이 API 호출을 사용 하 여 *클러스터* 리소스를 삭제 합니다. *클러스터* 리소스를 삭제 하려면 먼저 연결 된 모든 작업 영역을 삭제 해야 합니다.

  ```rst
  DELETE
  https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
  Authorization: Bearer <token>
  ```

  **응답**

  200 OK


## <a name="appendix"></a>부록

CMK (Application Insights Customer 관리 키)도 지원 되지만 응용 프로그램 정보 구성 요소에 대 한 CMK 배포를 계획 하려면 다음 변경 사항을 고려해 야 합니다.

Log Analytics 및 Application Insights는 동일한 데이터 저장소 플랫폼과 쿼리 엔진을 사용 합니다. 이러한 두 저장소는 Application Insights의 통합을 통해 Log Analytics으로 통합 하 여 Azure Monitor에서 단일 통합 로그 저장소를 제공 합니다.
2020. 이렇게 변경 하면 응용 프로그램 정보 데이터를 Log Analytics 작업 영역으로 가져와 쿼리, 통찰력 및 기타 향상 된 기능을 만들 수 있습니다 .이 경우 작업 영역에서 CMK 구성이 Application Insights 데이터에도 적용 됩니다.

> [!NOTE]
> 통합 전에 응용 프로그램 정보 데이터에 대해 CMK를 배포 하지 않아도 되는 경우에는 Application Insights CMK를 사용 하 여 대기 하는 것이 좋습니다. 이러한 배포는 통합에 의해 중단 되므로 로그로 마이그레이션한 후 CMK를 다시 구성 해야 합니다. 분석 작업 영역. 일별 1TB는 클러스터 수준에서 적용 되며, 2 분기 동안 통합이 완료 될 때까지 Application Insights 및 Log Analytics 별도의 클러스터가 필요 합니다.

## <a name="application-insights-cmk-configuration"></a>CMK 구성 Application Insights

Application Insights CMK의 구성은 다음 단계를 제외한 제약 조건 및 문제 해결을 포함 하 여이 문서에 설명 된 프로세스와 동일 합니다.

- *클러스터* 리소스 만들기

- *클러스터* 리소스에 구성 요소 연결

Application Insights에 대해 CMK를 구성 하는 경우 위에 나열 된 단계 대신 이러한 단계를 사용 합니다.

### <a name="create-a-cluster-resource"></a>*클러스터* 리소스 만들기

이 리소스는 Key Vault와 구성 요소 간의 중간 id 연결로 사용 됩니다. 구독이 허용 목록 확인을 받은 후에는 구성 요소가 있는 지역에서 Log Analytics *클러스터* 리소스를 만듭니다. *클러스터* 리소스의 유형은 만들 때 *Clustertype* 속성을 *loganalytics*또는 *applicationinsights*로 설정 하 여 정의 됩니다. Application Insights CMK에 대 한 *Applicationinsights* 여야 합니다. 구성 후에는 *Clustertype* 설정을 변경할 수 없습니다.

**만들기**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "location": "<region-name>",
  "properties": {
      "clusterType":"ApplicationInsights"
  },
  "identity": {
      "type": "systemAssigned"
  }
}
```

**응답**

만든 시간에는 id가 *클러스터* 리소스에 할당 됩니다.

```json

{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights",    //The value is ‘ApplicationInsights’ for Application Insights CMK
    "clusterId": "cluster-id" 
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name", //The cluster resource Id
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```
"principalId"는 관리 되는 id 서비스에 의해 생성 된 GUID입니다.

> [!IMPORTANT]
> "클러스터 id" 값은 다음 단계에서 필요 하므로 복사 하 고 유지 합니다.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-updatehttpsdocsmicrosoftcomrestapiapplication-insightscomponentscreateorupdate-api"></a>구성 요소를 사용 하 여 *클러스터* 리소스에 구성 요소 연결 [-API 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId"은 이전 단계의 응답에서 제공 된 "clusterId" 값입니다.
"kind" 예는 "web"입니다.

**응답**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId"은이 구성 요소에 연결 된 *클러스터* 리소스 ID입니다.

연결 후 구성 요소에 전송 된 데이터는 관리 되는 키로 암호화 된 상태로 저장 됩니다.
