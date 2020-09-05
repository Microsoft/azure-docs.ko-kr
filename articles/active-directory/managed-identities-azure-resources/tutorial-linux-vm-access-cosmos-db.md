---
title: 자습서`:` 관리 ID를 사용하여 Azure Cosmos DB에 액세스 - Linux - Azure AD
description: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB에 액세스하는 프로세스를 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2006c44d68d9570af0bfa410cc7fe908502d2ba5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267992"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>자습서: Linux VM 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB에 액세스 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


이 자습서에서는 Linux VM(가상 머신)에 대한 시스템 할당 관리 ID를 사용하여 Azure Cosmos DB에 액세스하는 방법을 보여줍니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Cosmos DB 계정 만들기
> * Cosmos DB 계정에서 컬렉션을 만듭니다.
> * Azure Cosmos DB 인스턴스에 대한 시스템 할당 관리 ID 액세스 권한 부여
> * Linux VM의 시스템 할당 관리 ID의 `principalID` 검색
> * 액세스 토큰을 가져와 Azure Resource Manager를 호출하는 데 사용
> * Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

이 자습서의 CLI 스크립트 예제는 두 가지 옵션을 통해 실행할 수 있습니다.

- Azure Portal에서 또는 각 코드 블록의 오른쪽 상단 모서리에 있는 **사용해 보세요** 단추를 통해 [Azure Cloud Shell](~/articles/cloud-shell/overview.md)을 사용합니다.
- 로컬 CLI 콘솔을 사용하려는 경우 [CLI 2.0의 최신 버전(2.0.23 이상)을 설치](/cli/azure/install-azure-cli)합니다.

## <a name="create-a-cosmos-db-account"></a>Cosmos DB 계정 만들기 

Cosmos DB 계정이 아직 없는 경우 지금 만듭니다. 이 단계를 건너뛰고 기존 Cosmos DB 계정을 사용해도 됩니다. 

1. Azure Portal의 왼쪽 위에 있는 **+/새 서비스 만들기** 단추를 클릭합니다.
2. **데이터베이스**를 클릭한 다음, **Azure Cosmos DB**를 클릭하면 새로운 "새 계정" 패널이 표시됩니다.
3. 나중에 사용하는 Cosmos DB 계정에 대한 **ID**를 입력합니다.  
4. **API**는 "SQL"로 설정되어야 합니다. 이 자습서에서 설명하는 방식은 사용 가능한 다른 API 형식으로 사용될 수 있지만 이 자습서의 단계는 SQL API에 대한 것입니다.
5. **구독** 및 **리소스 그룹**은 이전 단계에서 VM을 만들 때 지정한 것과 일치합니다.  Cosmos DB를 사용할 수 있는 **위치**를 선택합니다.
6. **만들기**를 클릭합니다.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Cosmos DB 계정에서 컬렉션을 만듭니다.

다음으로, 이후 단계에서 쿼리할 수 있는 Cosmos DB 계정에서 데이터 컬렉션을 추가합니다.

1. 새로 만든 Cosmos DB 계정으로 이동합니다.
2. **개요** 탭에서 **+/컬렉션 추가** 단추를 클릭하면 "컬렉션 추가" 패널이 슬라이드됩니다.
3. 컬렉션에 데이터베이스 ID, 컬렉션 ID를 제공하고, 스토리지 용량을 선택하고, 파티션 키를 입력하고, 처리량 값을 입력한 다음, **확인**을 클릭합니다.  이 자습서의 경우 데이터베이스 ID 및 컬렉션 ID로 &quot;Test&quot;를 사용하고, 고정된 스토리지 용량 및 가장 낮은 처리량(400RU/s)을 선택하는 데 충분합니다.  

## <a name="retrieve-the-principalid-of-the-linux-vms-system-assigned-managed-identity"></a>Linux VM의 시스템 할당 관리 ID의 `principalID` 검색

다음 섹션에서 Resource Manager의 Cosmos DB 계정 액세스 키에 액세스하려면 Linux VM의 시스템 할당 관리 ID의 `principalID`를 검색합니다.  `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`(VM이 위치한 리소스 그룹) 및 `<VM NAME>` 매개 변수 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
응답은 시스템 할당 관리 ID의 세부 정보를 포함합니다(다음 섹션에서 사용할 보안 주체 ID 기록).

```output  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Cosmos DB 계정 액세스 키에 대한 Linux VM의 시스템 할당 ID 액세스 권한 부여

Cosmos DB는 Azure AD 인증을 기본적으로 지원하지 않습니다. 그러나 관리 ID를 사용하여 Resource Manager에서 Cosmos DB 액세스 키를 검색한 다음, 해당 키를 사용하여 Cosmos DB에 액세스할 수 있습니다. 이 단계에서 Cosmos DB 계정의 키에 시스템 할당 관리 ID 액세스 권한을 부여합니다.

Azure CLI를 사용하여 Azure Resource Manager의 Cosmos DB 계정에 시스템 할당 관리 ID 액세스 권한을 부여하려면 작업 환경의 `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>` 값을 업데이트합니다. `<MI PRINCIPALID>`를 Linux VM MI의 principalID 검색의 `az resource show` 명령에서 반환된 `principalId` 속성으로 바꿉니다.  Cosmos DB는 액세스 키를 사용할 때 두 가지 수준의 세분성: 계정에 대한 읽기/쓰기 액세스 및 계정에 대한 읽기 전용 액세스를 지원합니다.  계정에 대한 읽기/쓰기 키를 가져오려는 경우 `DocumentDB Account Contributor` 역할을 할당하거나 계정에 대한 읽기 전용 키를 가져오려는 경우 `Cosmos DB Account Reader Role` 역할을 할당합니다.

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

응답에는 생성된 역할 할당에 대한 세부 정보가 포함됩니다.

```output
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Linux VM의 시스템 할당 관리 ID를 통해 액세스 토큰을 가져오고 Azure Resource Manager를 호출하는 데 사용합니다.

자습서의 나머지 부분에서는 이전에 만든 VM에서 작업합니다.

아래의 단계를 완료하려면 SSH 클라이언트가 필요합니다. Windows를 사용 중인 경우 [Linux용 Windows 하위 시스템](/windows/wsl/install-win10)에서 SSH 클라이언트를 사용할 수 있습니다. SSH 클라이언트의 키 구성에 대한 도움이 필요하면 [Azure에서 Windows를 통해 SSH 키를 사용하는 방법](../../virtual-machines/linux/ssh-from-windows.md) 또는 [Azure에서 Linux VM용 SSH 공개 및 프라이빗 키 쌍을 만들고 사용하는 방법](../../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

1. Azure Portal에서 **Virtual Machines**, Linux 가상 머신으로 이동한 후 **개요** 페이지 위쪽의 **연결**을 클릭합니다. VM에 연결하기 위한 문자열을 복사합니다. 
2. SSH 클라이언트를 사용하여 VM에 연결합니다.  
3. 그리고 나면 **Linux VM**을 만들 때 추가했던 **암호**를 입력하라는 메시지가 표시됩니다. 이제 정상적으로 로그인되어야 합니다.  
4. CURL을 사용하여 Azure Resource Manager에 대한 액세스 토큰을 가져옵니다. 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > 이전 요청에서 "resource" 매개 변수의 값은 Azure AD에 필요한 값과 정확하게 일치해야 합니다. Azure Resource Manager 리소스 ID를 사용할 때는 URI에 후행 슬래시를 포함해야 합니다.
    > 다음 응답에서는 간단히 나타내기 위해 access_token 요소를 축약했습니다.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Cosmos DB 호출을 위해 Azure Resource Manager에서 액세스 키 가져오기  

이제 CURL을 사용하여 이전 섹션에서 검색한 액세스 토큰을 사용하여 Resource Manager를 호출하여 Cosmos DB 계정 액세스 키를 검색합니다. 액세스 키가 있으면 Cosmos DB를 쿼리할 수 있습니다. `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` 및 `<COSMOS DB ACCOUNT NAME>` 매개 변수 값을 원하는 값으로 바꾸세요. `<ACCESS TOKEN>` 값을 앞서 검색한 액세스 토큰으로 바꿉니다.  읽기/쓰기 키를 검색하려는 경우 키 작업 유형 `listKeys`를 사용합니다.  읽기 전용 키를 검색하려는 경우 키 작업 유형 `readonlykeys`를 사용합니다.

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> 이전 URL의 텍스트는 대/소문자를 구분하므로 리소스 그룹을 적절하게 반영하는 대/소문자를 사용하는지 확인하세요. 또한 이 요청은 GET 요청이 아닌 POST 요청임을 기억해야 하며, -d(NULL일 수 있음)를 사용하여 길이 제한을 캡처하는 값을 전달해야 합니다.  

CURL 응답에서는 키 목록을 제공합니다.  예를 들어 읽기 전용 키를 가져오는 경우:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

이제 Cosmos DB 계정에 대한 액세스 키가 있으므로 Cosmos DB SDK에 전달하고 계정에 액세스하는 호출을 만들 수 있습니다.  빠른 예제의 경우 Azure CLI에 액세스 키를 전달할 수 있습니다.  Azure Portal에서 Cosmos DB 계정 블레이드의 **개요** 탭에서 `<COSMOS DB CONNECTION URL>`을 가져올 수 있습니다.  `<ACCESS KEY>`를 위에서 얻은 값으로 바꿉니다.

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

이 CLI 명령은 컬렉션에 대한 세부 정보를 반환합니다.

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Linux 가상 머신에서 시스템 할당 관리 ID를 사용하여 Cosmos DB에 액세스하는 방법을 알아보았습니다.  Cosmos DB에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
>[Azure Cosmos DB 개요](../../cosmos-db/introduction.md)