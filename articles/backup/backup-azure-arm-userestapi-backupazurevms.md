---
title: 'Azure Backup: REST API를 사용 하 여 Azure Vm 백업'
description: REST API를 사용하여 Azure VM 백업의 백업 작업 관리
services: backup
author: pvrk
manager: shivamg
keywords: REST API, Azure VM 백업, Azure VM 복원,
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 8a47d3cf346d7961e9f8b1c4fa615a2faa6b1da0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646778"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>REST API를 통해 Azure Backup을 사용하여 Azure VM 백업

이 문서에서는 REST API를 통해 Azure Backup을 사용하여 Azure VM의 백업을 관리하는 방법을 설명합니다. 이전에 보호되지 않는 Azure VM에 대한 보호를 처음으로 구성하고, 보호된 Azure VM에 대한 주문형 백업을 트리거하고, 여기에 설명된 대로 REST API를 통해 백업된 VM의 백업 속성을 수정합니다.

새 자격 증명 모음 및 정책 만들기는 [자격 증명 모음 만들기](backup-azure-arm-userestapi-createorupdatevault.md) 및 [정책 만들기](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 자습서를 참조하세요.

기본 정책("DefaultPolicy")을 사용하여 리소스 그룹 "testVaultRG" 내에 있는 Recovery Services 자격 증명 모음 "testVault"에 대해 리소스 그룹 "testRG"의 VM "testVM"을 보호하려는 경우를 가정해봅시다.

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>REST API를 사용하여 보호되지 않는 Azure VM에 대한 백업 구성

### <a name="discover-unprotected-azure-vms"></a>보호되지 않는 Azure VM 검색

먼저 자격 증명 모음은 Azure VM을 식별할 수 있어야 합니다. [새로 고침 작업](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh)을 사용하여 이 자격 증명 모음을 트리거합니다. 이 작업은 비동기 *POST* 작업으로서 자격 증명 모음이 현재 구독의 모든 보호되지 않는 VM의 최신 목록을 가져오고 해당 VM을 ‘캐시’해야 합니다. VM이 '캐시되면' Recovery Services는 해당 VM에 액세스하고 보호할 수 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

POST URI에는 `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` 매개 변수가 있습니다. `{fabricName}`은 "Azure"입니다. 해당 예제에 따라 `{vaultName}`은 "testVault"이고 `{vaultresourceGroupName}`은 "testVaultRG"입니다. 모든 필수 매개 변수가 URI에서 지정되므로 별도 요청 본문이 필요 없습니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>응답

'새로 고침' 작업은 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)

|이름  |형식  |설명  |
|---------|---------|---------|
|204 콘텐츠 없음     |         |  반환된 콘텐츠가 없는 경우 정상      |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses"></a>예제 응답

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

간단한 *GET* 명령으로 “위치” 헤더를 사용하여 결과 작업 추적

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

모든 Azure VM을 검색하면 GET 명령이 204(콘텐츠 없음) 응답을 반환합니다. 이제 자격 증명 모음은 구독 내의 모든 VM을 검색할 수 있습니다.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>관련 Azure VM 선택

 구독에서 [모든 보호 가능한 항목 나열하기](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)에서 "캐싱"을 수행했는지 확인하고 응답에서 원하는 VM을 찾을 수 있습니다. [이 작업의 응답](#example-responses-1)은 또한 Recovery Services가 VM을 식별하는 방법에 대한 정보를 제공합니다.  패턴에 친숙해지면 이 단계를 건너뛰고 직접 [보호 활성화](#enabling-protection-for-the-azure-vm)를 진행할 수 있습니다.

이 작업은 *GET* 작업입니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

#### <a name="responses"></a>응답

|이름  |형식  |설명  |
|---------|---------|---------|
|200 정상     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       확인 |

##### <a name="example-responses"></a>예제 응답

*GET* 요청이 제출되면 200(정상) 응답이 반환됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> *GET* 응답에서 값의 개수는 '페이지'당 200개로 제한됩니다. 'nextLink' 필드를 사용하여 다음 집합의 응답에 대한 URL을 가져옵니다.

이 응답에는 보호되지 않는 모든 Azure VM 목록이 포함되며 각 `{value}`에는 Azure Recovery Service에서 백업을 구성하는 데 필요한 모든 정보가 포함됩니다. Backup을 구성하려면 `{properties}` 섹션에서 `{name}` 필드 및 `{virtualMachineId}` 필드를 참고합니다. 아래에 설명된 대로 이러한 필드 값에서 두 개의 변수를 생성합니다.

- containerName = "iaasvmcontainer;"+`{name}`
- protectedItemName = "vm;"+ `{name}`
- `{virtualMachineId}`는 나중에 [요청 본문](#example-request-body)에 사용됨

예제에서는 위의 값이 다음으로 변환됩니다.

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Azure VM 보호 사용

관련 VM이 "캐시"되고 "확인"된 경우 정책을 선택하여 보호합니다. 자격 증명 모음의 기존 정책에 대한 자세한 내용은 [정책 API 목록](https://docs.microsoft.com/rest/api/backup/backuppolicies/list)을 참조하세요. 그런 다음, 정책 이름을 참조하여 [관련 정책](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get)을 선택합니다. 정책을 만들려면 [정책 자습서 만들기](backup-azure-arm-userestapi-createorupdatepolicy.md)를 참조하세요. 다음 예제에서 "DefaultPolicy"를 선택합니다.

보호 사용은 '보호된 항목'를 만드는 비동기 *PUT* 작업입니다.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 위에서 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>요청 본문 만들기

보호된 항목을 만들려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |형식  |설명  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |ProtectedItem 리소스 속성         |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서 만들기](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body)를 참조하세요.

##### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}`는 [보호 가능한 항목 목록의 응답](#example-responses-1)의 위에서 설명한 `{virtualMachineId}`입니다.

#### <a name="responses"></a>응답

보호된 항목 만들기는 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)

|이름  |형식  |설명  |
|---------|---------|---------|
|200 정상     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  확인       |
|202 수락됨     |         |     수락됨    |

##### <a name="example-responses"></a>예제 응답

보호된 항목 만들기 또는 업데이트를 위한 *PUT* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 보호된 항목 콘텐츠를 사용하여 200(정상)을 반환합니다.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

이렇게 VM에 대한 보호를 사용하도록 설정하고 정책 일정에 따라 첫 번째 백업을 트리거하는지 확인합니다.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>보호된 Azure VM에 대한 주문형 백업 트리거

백업에 대한 Azure VM을 구성하면 정책 일정에 따라 백업이 수행됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다. 주문형 백업의 보존은 백업 정책의 보존과는 별개이며 특정 날짜/시간을 지정할 수 있습니다. 지정하지 않으면 주문형 백업을 트리거한 날로부터 30일까지로 간주됩니다.

주문형 백업의 트리거는 *POST* 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 [위에서](#responses-1) 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>요청 본문 만들기

주문형 백업을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |형식  |설명  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |BackupRequestResource 속성         |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서의 백업 트리거](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body)를 참조하세요.

#### <a name="example-request-body"></a>요청 본문 예제

다음 요청 본문은 보호된 항목의 백업을 트리거하는 데 필요한 속성을 정의합니다. 보존을 지정하지 않으면 백업 작업을 트리거한 시간에서 30일 동안 유지됩니다.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>응답

주문형 백업의 트리거는 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)

|이름  |형식  |설명  |
|---------|---------|---------|
|202 수락됨     |         |     수락됨    |

#### <a name="example-responses"></a>예제 응답

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 결과 백업 작업의 ID를 사용하여 200(정상)을 반환합니다.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 추적해야 합니다.

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>보호된 Azure VM에 대한 백업 구성 수정

### <a name="changing-the-policy-of-protection"></a>보호 정책 변경

VM을 보호하는 정책을 변경하려면 [보호 사용](#enabling-protection-for-the-azure-vm)과 동일한 형식을 사용할 수 있습니다. [요청 본문](#example-request-body)의 새 정책 ID를 제공하고 요청을 제출합니다. 에 대 한 예를 들어: 'ProdPolicy'를 'DefaultPolicy'에서 testVM 정책을 변경 하려면 요청 본문에 'ProdPolicy' id를 제공 합니다.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

응답은 [보호 사용의 경우](#responses-2)에 설명된 것과 동일한 형식을 따름

### <a name="stop-protection-but-retain-existing-data"></a>보호를 중지하지만 기존 데이터는 보존

보호된 VM에서 보호를 제거하지만 이미 백업된 데이터를 보존하려면 요청 본문에서 정책을 제거하고 요청을 제출합니다. 정책과의 연결을 제거하면 백업은 더 이상 트리거되지 않으며 새 복원 지점도 생성되지 않습니다.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

응답은 [주문형 백업 트리거하는 경우](#example-responses-3)에 설명된 것과 동일한 형식을 따릅니다. [REST API 문서를 사용한 모니터링 작업](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 결과 작업을 추적해야 합니다.

### <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호된 VM에 대한 보호를 제거하고 백업 데이터도 삭제하려면 [여기](https://docs.microsoft.com/rest/api/backup/protecteditems/delete)에 설명된 대로 삭제 작업을 수행합니다.

보호 중지 및 데이터 삭제는 *DELETE* 작업입니다.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 [위에서](#responses-1) 생성됩니다. `{fabricName}`은 "Azure"입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses"></a>응답

보호 *DELETE* 작업은 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 202 (수락 됨) 다른 작업이 만들어질 때 한 다음 204(nocontent) 해당 작업이 완료 되 면 합니다.

|이름  |형식  |설명  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 수락됨     |         |     수락됨    |

## <a name="next-steps"></a>다음 단계

[Azure Virtual Machine 백업에서 데이터 복원](backup-azure-arm-userestapi-restoreazurevms.md).

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)
