---
title: 'Azure Backup: REST API를 사용하여 Azure VM 복원'
description: REST API를 사용하여 Azure VM 백업의 복원 작업 관리
services: backup
author: pvrk
manager: shivamg
keywords: REST API, Azure VM 백업, Azure VM 복원,
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60646659"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>REST API를 사용하여 Azure Virtual Machines 복원

Azure Backup을 사용하여 Azure Virtual Machines의 백업을 완료하면 동일한 백업 복사본에서 전체 Azure Virtual Machines, 디스크 또는 파일을 복원할 수 있습니다. 이 문서에서는 REST API를 사용하여 Azure VM 또는 디스크를 복원하는 방법을 설명합니다.

모든 복원 작업의 경우 먼저 관련 복구 지점을 식별해야 합니다.

## <a name="select-recovery-point"></a>복구 지점 선택

백업 항목의 사용 가능한 복구 지점을 [복구 지점 REST API 목록](https://docs.microsoft.com/rest/api/backup/recoverypoints/list)을 사용하여 나열할 수 있습니다. 모든 관련 값이 있는 간단한 *GET* 작업입니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 [여기](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1)에서 생성됩니다. `{fabricName}`은 "Azure"입니다.

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문은 필요 없음

### <a name="responses"></a>응답

|이름  |형식  |설명  |
|---------|---------|---------|
|200 정상     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       확인  |

#### <a name="example-response"></a>예제 응답

*GET* URI를 제출하면 200(정상) 응답이 반환됩니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

위 응답의 `{name}` 필드로 복구 지점을 식별합니다.

## <a name="restore-disks"></a>디스크 복원

백업 데이터에서 VM 만들기를 사용자 지정해야 하는 경우 선택한 스토리지 계정에 디스크를 복원하고 관련 요구 사항에 따라 해당 디스크에서 VM을 만들 수 있습니다. 스토리지 계정은 복원 서비스 자격 증명 모음과 동일한 지역에 있어야 하며 영역이 중복돼서는 안 됩니다. 디스크 뿐만 아니라 백업 VM("vmconfig.json")의 구성도 지정된 스토리지 계정에 저장됩니다.

디스크 복원을 트리거하면 *POST*를 요청합니다. 디스크 복원 작업에 대한 자세한 내용은 [REST API "복원 트리거"](https://docs.microsoft.com/rest/api/backup/restores/trigger)를 참조하세요.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

`{containerName}` 및 `{protectedItemName}`은 [여기](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1)에서 생성됩니다. `{fabricName}`은 "Azure"이며 `{recoveryPointId}`는 [위](#example-response)에 언급된 복구 지점의 `{name}` 필드입니다.

### <a name="create-request-body"></a>요청 본문 만들기

Azure VM 백업에서 디스크 복원을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

|이름  |형식  |설명  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [REST API 문서 복원 트리거](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)를 참조하세요.

#### <a name="example-request"></a>요청 예

다음 요청 본문은 디스크 복원을 트리거하는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>response

복원 디스크의 트리거는 [비동기 작업](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

두 응답을 반환합니다. 다른 작업을 만드는 경우 202(수락됨) 및 해당 작업이 완료되는 경우 200(정상)

|이름  |형식  |설명  |
|---------|---------|---------|
|202 수락됨     |         |     수락됨    |

#### <a name="example-responses"></a>예제 응답

디스크 복원을 트리거하기 위해 *POST* URI를 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

그런 다음, 간단한 *GET* 명령으로 위치 헤더 또는 Azure-AsyncOperation 헤더를 사용하여 결과 작업을 추적합니다.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 결과 복원 작업의 ID를 사용하여 200(정상)을 반환합니다.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 추적해야 합니다.

이 장기 실행 작업이 완료되면 디스크 및 백업된 가상 머신("VMConfig.json")의 구성은 지정된 스토리지 계정에 표시됩니다.

## <a name="restore-as-another-virtual-machine"></a>다른 가상 머신으로 복원

복원 지점에서 데이터를 사용하여 Azure 가상 머신을 만들려면 아래 지정된 대로 [복구 지점을 선택](#select-recovery-point)하고 요청 본문을 만듭니다.

다음 요청 본문은 가상 머신 복원을 트리거하는 데 필요한 속성을 정의합니다.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2016-12-01",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

이 응답은 [디스크를 복원하기 위해 위에 설명된](#response) 것과 동일한 방식으로 처리해야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Backup REST API에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Recovery Services 공급자 REST API](/rest/api/recoveryservices/)
- [Azure REST API 시작하기](/rest/api/azure/)