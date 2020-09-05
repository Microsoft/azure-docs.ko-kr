---
title: Rest API를 사용 하 여 Azure 파일 공유 백업 관리
description: REST API를 사용 하 여 Azure Backup에서 백업 된 Azure 파일 공유를 관리 하 고 모니터링 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892850"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>REST API를 사용 하 여 Azure 파일 공유 백업 관리

이 문서에서는 [Azure Backup](./backup-overview.md)에 의해 백업 되는 Azure 파일 공유를 관리 하 고 모니터링 하기 위한 작업을 수행 하는 방법을 설명 합니다.

## <a name="monitor-jobs"></a>작업 모니터링

Azure Backup 서비스는 백그라운드에서 실행 되는 작업을 트리거합니다. 여기에는 백업 트리거, 복원 작업 및 백업 해제와 같은 시나리오가 포함 됩니다. 이러한 작업은 해당 ID를 사용하여 추적할 수 있습니다.

### <a name="fetch-job-information-from-operations"></a>작업에서 작업 정보 가져오기

백업 트리거 등의 작업은 항상 응답에서 jobID를 반환 합니다.

예를 들어 [트리거 백업 REST API](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) 작업의 최종 응답은 다음과 같습니다.

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Azure 파일 공유 백업 작업은 **jobId** 필드로 식별 되며 GET 요청을 사용 하 여 [여기](/rest/api/backup/jobdetails/) 에 설명 된 대로 추적할 수 있습니다.

### <a name="tracking-the-job"></a>작업 추적

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName}은 위에서 언급 한 "jobId"입니다. 응답은 항상 "200 OK" 이며 **상태** 필드는 작업의 상태를 나타냅니다. "Completed" 또는 "CompletedWithWarnings" 이면 **Extendedinfo** 섹션에 작업에 대 한 자세한 정보가 표시 됩니다.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>응답

이름  | 유형  |  설명
--- | --- | ----
200 정상 |  JobResource  | 정상

#### <a name="response-example"></a>응답 예제

*GET* URI가 제출 되 면 200 응답이 반환 됩니다.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>정책 수정

파일 공유를 보호 하는 데 사용 되는 정책을 변경 하려면 보호를 사용 하도록 설정 하는 것과 동일한 형식을 사용할 수 있습니다. 요청 정책에 새 정책 ID를 제공 하 고 요청을 제출 하면 됩니다.

예: *testshare* 의 보호 정책을 *schedule1* 에서 *schedule2*로 변경 하려면 요청 본문에 *schedule2* ID를 제공 합니다.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>보호를 중지하지만 기존 데이터는 보존

보호 된 파일 공유에 대 한 보호를 제거할 수 있지만 이미 백업 된 데이터는 보존 됩니다. 이렇게 하려면[백업을 사용 하도록 설정](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) 하 고 요청을 제출 하는 데 사용한 요청 본문에서 정책을 제거 합니다. 정책과의 연결이 제거 되 면 백업이 더 이상 트리거되지 않으며 새 복구 지점이 생성 되지 않습니다.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>샘플 응답

파일 공유에 대 한 보호를 중지 하는 작업은 비동기 작업입니다. 작업은 추적 해야 하는 다른 작업을 만듭니다. 이는 다른 작업을 만들 때 202 (수락 됨) 및 해당 작업이 완료 될 때 200 인 두 개의 응답을 반환 합니다.

작업이 성공적으로 수락 되는 경우 응답 헤더:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

그런 다음 GET 명령을 사용 하 여 location 헤더 또는 AsyncOperation 헤더를 사용 하 여 결과 작업을 추적 합니다.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>응답 본문

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>보호 중지 및 데이터 삭제

보호 된 파일 공유에 대 한 보호를 제거 하 고 백업 데이터도 삭제 하려면 [여기](/rest/api/backup/protecteditems/delete)에 설명 된 대로 삭제 작업을 수행 합니다.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

{ContainerName} 및 {protectedItemName} 매개 변수는 [여기](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)에 설정 되어 있습니다.

다음 예에서는 *azurefilesvault*로 보호 되는 *testshare* 파일 공유에 대 한 보호를 중지 하는 작업을 트리거합니다.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>응답

삭제 보호는 비동기 작업입니다. 작업은 개별적으로 추적 해야 하는 다른 작업을 만듭니다.
다른 작업을 만들 때 202 (수락 됨) 및 작업이 완료 될 때 204 (NoContent) 라는 두 개의 응답을 반환 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 파일 공유에 대 한 백업을 구성 하는 동안 문제를 해결](troubleshoot-azure-files.md)하는 방법을 알아봅니다.
