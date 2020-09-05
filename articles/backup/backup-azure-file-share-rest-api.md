---
title: REST API를 사용 하 여 Azure 파일 공유 백업
description: REST API를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure 파일 공유를 백업 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824868"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Rest API를 통해 Azure Backup를 사용 하 여 Azure 파일 공유 백업

이 문서에서는 REST API를 통해 Azure Backup를 사용 하 여 Azure 파일 공유를 백업 하는 방법을 설명 합니다.

이 문서에서는 파일 공유에 대 한 백업을 구성 하기 위한 Recovery Services 자격 증명 모음 및 정책을 이미 만들었다고 가정 합니다. 그렇지 않은 경우 새 자격 증명 모음 및 정책을 만들기 위한 [자격 증명 모음 만들기](./backup-azure-arm-userestapi-createorupdatevault.md) 및 [정책 만들기](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST API 자습서를 참조 하세요.

이 문서에서는 다음 리소스를 사용 합니다.

- **Recoveryservicesvault**: *azurefilesvault*

- **정책:** *schedule1*

- **리소스 그룹**: *azurefiles*

- **저장소 계정**: *testvault2*

- **파일 공유**: *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>REST API를 사용 하 여 보호 되지 않는 Azure 파일 공유에 대 한 백업 구성

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>보호 되지 않는 Azure 파일 공유를 사용 하 여 저장소 계정 검색

자격 증명 모음은 Recovery Services 자격 증명 모음에 백업할 수 있는 파일 공유를 사용 하 여 구독의 모든 Azure storage 계정을 검색 해야 합니다. [새로 고침 작업](/rest/api/backup/protectioncontainers/refresh)을 사용하여 이 자격 증명 모음을 트리거합니다. 자격 증명 모음이 현재 구독에서 보호 되지 않는 모든 Azure 파일 공유의 최신 목록을 가져오고 ' 캐시 ' 하는 것을 보장 하는 비동기 *POST* 작업입니다. 파일 공유가 ' 캐시 ' 되 면 복구 서비스에서 파일 공유에 액세스 하 여 보호할 수 있습니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

POST URI에는 `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` 및 `{fabricName}` 매개 변수가 있습니다. 이 예제에서 다른 매개 변수에 대 한 값은 다음과 같습니다.

- `{fabricName}`*Azure*

- `{vaultName}`*azurefilesvault*

- `{vaultresourceGroupName}`*azurefiles*

- $filter = backupManagementType eq ' AzureStorage '

모든 필수 매개 변수는 URI에 제공 되므로 별도의 요청 본문이 필요 하지 않습니다.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>새로 고침 작업에 대 한 응답

'새로 고침' 작업은 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이는 다른 작업을 만들 때 202 (수락 됨)와 해당 작업이 완료 될 때 200 (OK)의 두 응답을 반환 합니다.

##### <a name="example-responses-to-the-refresh-operation"></a>새로 고침 작업에 대 한 예제 응답

*POST* 요청을 제출하면 202(수락됨) 응답이 반환됩니다.

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

간단한 *GET* 명령으로 “위치” 헤더를 사용하여 결과 작업 추적

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

모든 Azure Storage 계정이 검색 되 면 GET 명령이 200 (콘텐츠 없음) 응답을 반환 합니다. 이제 자격 증명 모음에서 구독 내에서 백업할 수 있는 파일 공유를 사용 하 여 저장소 계정을 검색할 수 있습니다.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음으로 백업할 수 있는 파일 공유를 사용 하 여 저장소 계정 목록을 가져옵니다.

"캐싱"이 완료 되었는지 확인 하려면 Recovery Services 자격 증명 모음으로 백업할 수 있는 파일 공유를 사용 하 여 구독의 모든 저장소 계정을 나열 합니다. 그런 다음 응답에서 원하는 저장소 계정을 찾습니다. 이 작업은 [GET ProtectableContainers](/rest/api/backup/protectablecontainers/list) 작업을 사용 하 여 수행 됩니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*GET* URI에는 필요한 모든 매개 변수가 있습니다. 추가 요청 본문이 필요없습니다.

응답 본문의 예:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

친숙 한 이름으로 응답 본문에서 *testvault2* 저장소 계정을 찾을 수 있으므로 위에서 수행한 새로 고침 작업이 성공 했습니다. 이제 Recovery Services 자격 증명 모음이 동일한 구독에서 보호 되지 않는 파일 공유의 저장소 계정을 성공적으로 검색할 수 있습니다.

### <a name="register-storage-account-with-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 저장소 계정 등록

이 단계는 저장소 계정을 이전에 자격 증명 모음에 등록 하지 않은 경우에만 필요 합니다. [ProtectionContainers-register 작업](/rest/api/backup/protectioncontainers/register)을 통해 자격 증명 모음을 등록할 수 있습니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

URI에 대 한 변수를 다음과 같이 설정 합니다.

- {resourceGroupName}- *azurefiles*
- {fabricName}- *Azure*
- {vaultName}- *azurefilesvault*
- {containerName}-GET ProtectableContainers 작업의 응답 본문에 있는 name 특성입니다.
   이 예제에서는 *StorageContainer입니다. 저장할 AzureFiles; testvault2*

>[!NOTE]
> 항상 응답의 name 특성을 사용 하 여이 요청에 입력 합니다. 하드 코드 하거나 컨테이너 이름 형식을 만들지 마세요. 이를 만들거나 하드 코딩 하는 경우 나중에 컨테이너 이름 형식이 변경 되 면 API 호출이 실패 합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

만들기 요청 본문은 다음과 같습니다.

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

요청 본문 및 기타 세부 정보에 대 한 전체 정의 목록은 [ProtectionContainers](/rest/api/backup/protectioncontainers/register#azurestoragecontainer)를 참조 하세요.

비동기 작업이 며 작업이 완료 되 면 "200 OK"와 202 같이 작업이 완료 되 면 두 응답을 반환 하 고, 작업이 완료 되 면 "OK"를 반환 합니다.  작업 상태를 추적 하려면 location 헤더를 사용 하 여 작업의 최신 상태를 가져옵니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

작업이 완료 되 면 응답 본문의 예:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

응답 본문의 *registrationstatus* 매개 변수 값에서 등록이 성공 했는지 확인할 수 있습니다. 이 경우 *testvault2*에 등록 된 상태를 표시 하므로 등록 작업이 성공 했습니다.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>저장소 계정에서 보호 되지 않는 모든 파일 공유를 조회 합니다.

[보호 컨테이너-조회](/rest/api/backup/protectioncontainers/inquire) 작업을 사용 하 여 저장소 계정의 보호 가능한 항목을 확인할 수 있습니다. 비동기 작업 이며 location 헤더를 사용 하 여 결과를 추적 해야 합니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

위의 URI에 대 한 변수를 다음과 같이 설정 합니다.

- {vaultName}- *azurefilesvault*
- {fabricName}- *Azure*
- {containerName}-GET ProtectableContainers 작업의 응답 본문에서 name 특성을 참조 하세요. 이 예제에서는 *StorageContainer입니다. 저장할 AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

요청이 성공적으로 완료 되 면 "정상" 상태 코드를 반환 합니다.

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>백업할 파일 공유를 선택 합니다.

구독 아래에 있는 모든 보호 가능한 항목을 나열 하 고 [GET backupprotectableItems](/rest/api/backup/backupprotectableitems/list) 작업을 사용 하 여 백업할 원하는 파일 공유를 찾을 수 있습니다.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

다음과 같이 URI를 생성 합니다.

- {vaultName}- *azurefilesvault*
- {$filter}- *Backupmanagementtype eq ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

샘플 응답:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

응답에는 보호 되지 않는 모든 파일 공유 목록이 포함 되며, Azure 복구 서비스에서 백업을 구성 하는 데 필요한 모든 정보가 포함 되어 있습니다.

### <a name="enable-backup-for-the-file-share"></a>파일 공유에 대 한 백업 사용

관련 파일 공유가 식별 된 이름으로 "식별" 된 후 보호할 정책을 선택 합니다. 자격 증명 모음에서 기존 정책에 대 한 자세한 내용은 [List POLICY API](/rest/api/backup/backuppolicies/list)를 참조 하세요. 그런 다음, 정책 이름을 참조하여 [관련 정책](/rest/api/backup/protectionpolicies/get)을 선택합니다. 정책을 만들려면 [정책 자습서 만들기](./backup-azure-arm-userestapi-createorupdatepolicy.md)를 참조하세요.

보호를 사용 하도록 설정 하는 작업은 "보호 된 항목"을 만드는 비동기 *PUT* 작업입니다.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

GET backupprotectableitems 작업의 응답 본문에서 ID 특성을 사용 하 여 **containername** 및 **protecteditemname** 변수를 설정 합니다.

이 예제에서 보호 하려는 파일 공유의 ID는 다음과 같습니다.

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername}- *storagecontainer; 저장소; azurefiles; testvault2*
- {protectedItemName}- *azurefileshare* 공유

또는 보호 컨테이너의 **이름** 특성과 보호 가능한 항목 응답을 참조할 수 있습니다.

>[!NOTE]
>항상 응답의 name 특성을 사용 하 여이 요청에 입력 합니다. 컨테이너 이름 형식 또는 보호 된 항목 이름 형식을 하드 코딩 하거나 만들지 마세요. 컨테이너 이름 형식이 나 보호 된 항목 이름 형식이 나중에 변경 되는 경우 API 호출이 실패 합니다.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

요청 본문을 만듭니다.

다음 요청 본문은 보호된 항목을 만드는 데 필요한 속성을 정의합니다.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**Sourceresourceid 여야** 은 GET backupprotectableItems에 대 한 응답의 **parentcontainerFabricID** 입니다.

샘플 응답

보호 된 항목을 만드는 작업은 추적 해야 하는 다른 작업을 만드는 비동기 작업입니다. 이 메서드는 다른 작업이 생성 될 때 202 (수락 됨) 및 해당 작업이 완료 될 때 200 (OK)의 두 응답을 반환 합니다.

보호 된 항목 만들기 또는 업데이트에 대 한 *PUT* 요청을 제출 하면 초기 응답은 location 헤더를 사용 하는 202 (수락)입니다.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

그런 다음  *GET* 명령을 사용 하 여 location Header 또는 AsyncOperation 헤더를 사용 하 여 결과 작업을 추적 합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 보호된 항목 콘텐츠를 사용하여 200(정상)을 반환합니다.

샘플 응답 본문:

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

이렇게 하면 파일 공유에 대 한 보호를 사용 하도록 설정 하 고 첫 번째 백업이 정책 일정에 따라 트리거됩니다.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>파일 공유에 대 한 주문형 백업 트리거

Azure 파일 공유가 백업용으로 구성 되 면 백업은 정책 일정에 따라 실행 됩니다. 첫 번째 예약 백업을 대기하거나 언제든 주문형 백업을 트리거할 수 있습니다.

주문형 백업의 트리거는 POST 작업입니다.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} 및 {protectedItemName}은 (는) 백업을 사용 하도록 설정 하는 동안 생성 된 것입니다. 예를 들어 이렇게 하면 다음으로 변환됩니다.

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>요청 본문 만들기

주문형 백업을 트리거하려면 요청 본문의 구성 요소는 다음과 같습니다.

| Name       | 유형                       | 설명                       |
| ---------- | -------------------------- | --------------------------------- |
| 속성 | AzurefilesharebackupReques | BackupRequestResource 속성 |

요청 본문 및 기타 세부 정보에 대한 전체 정의 목록은 [보호된 항목 REST API 문서의 백업 트리거](/rest/api/backup/backups/trigger#request-body)를 참조하세요.

요청 본문 예제

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대 한 응답

주문형 백업의 트리거는 [비동기 작업](../azure-resource-manager/management/async-operations.md)입니다. 즉, 이 작업은 별도로 추적해야 하는 다른 작업을 만듭니다.

이 메서드는 다른 작업이 생성 될 때 202 (수락 됨) 및 해당 작업이 완료 될 때 200 (OK)의 두 응답을 반환 합니다.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>주문형 백업 작업에 대 한 예제 응답

주문형 백업에 대한 *POST* 요청을 제출하면 초기 응답은 위치 헤더 또는 Azure-async-header를 사용한 202(수락됨)입니다.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

그런 다음  *GET* 명령을 사용 하 여 location Header 또는 AsyncOperation 헤더를 사용 하 여 결과 작업을 추적 합니다.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

작업이 완료되면 응답 본문에서 결과 백업 작업의 ID를 사용하여 200(정상)을 반환합니다.

#### <a name="sample-response-body"></a>샘플 응답 본문

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

백업 작업은 장기 실행 작업이므로 [REST API를 사용한 모니터링 작업 문서](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job)에 설명된 대로 추적해야 합니다.

## <a name="next-steps"></a>다음 단계

- [REST API를 사용 하 여 Azure 파일 공유를 복원](restore-azure-file-share-rest-api.md)하는 방법을 알아봅니다.
