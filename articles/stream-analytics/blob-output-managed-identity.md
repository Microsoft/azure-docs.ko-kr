---
title: 관리 Id를 사용 하 여 blob 출력 인증 Azure Stream Analytics
description: 이 문서에서는 관리 되는 id를 사용 하 여 Azure Blob storage 출력에 Azure Stream Analytics 작업을 인증 하는 방법을 설명 합니다.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 1854a99cac3ead386f38c1c7fe27a56b672f365c
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73664318"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage-output-preview"></a>관리 Id를 사용 하 여 Azure Blob Storage 출력에 Azure Stream Analytics 작업 인증 (미리 보기)

Azure Blob 저장소에 대 한 출력에 대 한 [관리 id 인증은](../active-directory/managed-identities-azure-resources/overview.md) Azure Stream Analytics에 미리 보기로 제공 됩니다. 이렇게 하면 연결 문자열을 사용 하는 대신 저장소 계정에 직접 액세스 하는 Stream Analytics 작업을 제공 합니다. 이 기능을 사용 하면 보안을 개선 하는 것 외에도 Azure 내에서 VNET (Virtual Network)의 저장소 계정에 데이터를 쓸 수 있습니다.

이 문서에서는 Azure Portal 및 Azure Resource Manager 배포를 통해 Stream Analytics 작업의 Blob 출력에 대해 관리 되는 Id를 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Stream Analytics 작업을 만듭니다.

1. 새 Stream Analytics 작업을 만들거나 Azure Portal에서 기존 작업을 엽니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성**아래에 있는 **관리 되는 id** 를 선택 합니다. "시스템 할당 관리 Id 사용"이 선택 되어 있는지 확인 하 고 화면 아래쪽에 있는 **저장** 단추를 클릭 합니다.

   ![관리 id Stream Analytics 구성](./media/common/stream-analytics-enable-managed-identity.png)

2. Azure Blob 저장소 출력 싱크의 출력 속성 창에서 인증 모드 드롭다운을 선택 하 고 **관리 되는 id**를 선택 합니다. 다른 출력 속성에 대 한 자세한 내용은 [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)를 참조 하세요. 작업을 마쳤으면 **저장**을 클릭합니다.

   ![Azure Blob 저장소 출력 구성](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. 작업이 생성 되었으므로이 문서의 [저장소 계정에 대 한 Stream Analytics 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-storage-account) 섹션을 참조 하세요.

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager를 사용 하면 Stream Analytics 작업의 배포를 완벽 하 게 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용 하 여 리소스 관리자 템플릿을 배포할 수 있습니다. 아래 예제에서는 Azure CLI을 사용 합니다.


1. 리소스 관리자 템플릿의 리소스 섹션에 다음 속성을 포함 하 여 관리 되는 Id를 사용 하 여 **Microsoft StreamAnalytics/streamingjobs** 리소스를 만들 수 있습니다.

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   이 속성은 Stream Analytics 작업에 대 한 id를 만들고 관리 Azure Resource Manager를 알려 줍니다. 다음은 관리 되는 Id를 사용 하는 Stream Analytics 작업을 배포 하 고 관리 되는 Id를 사용 하는 Blob 출력 싱크에 리소스 관리자 템플릿 예제입니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    아래 Azure CLI 명령을 사용 하 여 리소스 그룹 **ExampleGroup** 에 위의 작업을 배포할 수 있습니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure Resource Manager를 사용 하 여 작업의 전체 정의를 검색할 수 있습니다.

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    위의 명령은 다음과 같은 응답을 반환 합니다.

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   작업 정의에서 **Principalid** 를 적어 둡니다 .이 id는 Azure Active Directory 내에서 작업의 관리 되는 id를 식별 하 고, 다음 단계에서 저장소 계정에 대 한 Stream Analytics 작업 액세스 권한을 부여 하는 데 사용 됩니다.

3. 작업이 생성 되었으므로이 문서의 [저장소 계정에 대 한 Stream Analytics 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-storage-account) 섹션을 참조 하세요.


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Stream Analytics 작업에 저장소 계정에 대 한 액세스 권한 부여

Stream Analytics 작업을 제공 하기 위해 선택할 수 있는 두 가지 수준의 액세스 권한이 있습니다.

1. **컨테이너 수준 액세스:** 이 옵션은 특정 기존 컨테이너에 대 한 작업 액세스를 제공 합니다.
2. **계정 수준 액세스:** 이 옵션은 새 컨테이너를 만들 수 있는 기능을 포함 하 여 저장소 계정에 대 한 일반 액세스를 제공 합니다.

사용자를 대신 하 여 컨테이너를 만드는 작업이 필요한 경우가 아니면 **컨테이너 수준 액세스** 를 선택 해야 합니다 .이 옵션은 필요한 최소 수준의 액세스를 작업에 부여 합니다. 두 옵션은 모두 Azure Portal 및 명령줄에 대해 설명 되어 있습니다.

### <a name="grant-access-via-the-azure-portal"></a>Azure Portal를 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

1. 저장소 계정 내 컨테이너의 구성 창으로 이동 합니다.

2. 왼쪽에서 **Access Control (IAM)** 을 선택 합니다.

3. "역할 할당 추가" 섹션에서 **추가**를 클릭 합니다.

4. 역할 할당 창에서 다음을 수행 합니다.

    1. **역할** 을 "저장소 Blob 데이터 참가자"로 설정 합니다.
    2. 드롭다운 **에 대 한 액세스 할당** 이 "Azure AD 사용자, 그룹 또는 서비스 사용자"로 설정 되어 있는지 확인 합니다.
    3. 검색 필드에 Stream Analytics 작업의 이름을 입력 합니다.
    4. Stream Analytics 작업을 선택 하 고 **저장**을 클릭 합니다.

   ![컨테이너 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>계정 수준 액세스

1. 스토리지 계정으로 이동합니다.

2. 왼쪽에서 **Access Control (IAM)** 을 선택 합니다.

3. "역할 할당 추가" 섹션에서 **추가**를 클릭 합니다.

4. 역할 할당 창에서 다음을 수행 합니다.

    1. **역할** 을 "저장소 Blob 데이터 참가자"로 설정 합니다.
    2. 드롭다운 **에 대 한 액세스 할당** 이 "Azure AD 사용자, 그룹 또는 서비스 사용자"로 설정 되어 있는지 확인 합니다.
    3. 검색 필드에 Stream Analytics 작업의 이름을 입력 합니다.
    4. Stream Analytics 작업을 선택 하 고 **저장**을 클릭 합니다.

   ![계정 액세스 권한 부여](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>명령줄을 통해 액세스 권한 부여

#### <a name="container-level-access"></a>컨테이너 수준 액세스

특정 컨테이너에 대 한 액세스 권한을 부여 하려면 Azure CLI을 사용 하 여 다음 명령을 실행 합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>계정 수준 액세스

전체 계정에 대 한 액세스 권한을 부여 하려면 Azure CLI을 사용 하 여 다음 명령을 실행 합니다.

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET 액세스 사용

저장소 계정의 **방화벽 및 가상 네트워크**를 구성 하는 경우 다른 신뢰할 수 있는 Microsoft 서비스의 네트워크 트래픽을 선택적으로 허용할 수 있습니다. Stream Analytics 관리 Id를 사용 하 여 인증 하는 경우 요청을 신뢰할 수 있는 서비스에서 시작 하는 증명을 제공 합니다. 이 VNET 액세스 예외를 사용 하도록 설정 하는 지침은 다음과 같습니다.

1.  저장소 계정의 구성 창에서 "방화벽 및 가상 네트워크" 창으로 이동 합니다.
2.  "신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스 하도록 허용" 옵션을 사용 하도록 설정 했는지 확인 합니다.
3.  사용 하도록 설정한 경우 **저장**을 클릭 합니다.

   ![VNET 액세스 사용](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="limitations"></a>제한 사항
이 기능의 현재 제한 사항은 다음과 같습니다.

1. 클래식 Azure Storage 계정.

2. Azure Active Directory 없는 Azure 계정.

3. 다중 테넌트 액세스는 지원 되지 않습니다. 지정 된 Stream Analytics 작업에 대해 만든 서비스 주체는 작업이 만들어진 동일한 Azure Active Directory 테넌트에 상주해 야 하며, 다른 Azure Active Directory 테넌트에 있는 리소스와 함께 사용할 수 없습니다.

4. [사용자 할당 id](../active-directory/managed-identities-azure-resources/overview.md) 는 지원 되지 않습니다. 즉, 사용자는 자신의 Stream Analytics 작업에서 사용할 자신의 서비스 사용자를 입력할 수 없습니다. Azure Stream Analytics에서 서비스 주체를 생성 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
* [사용자 지정 blob 출력 분할 Azure Stream Analytics](./stream-analytics-custom-path-patterns-blob-storage-output.md)
