---
title: 관리 Id를 사용 하 여 Power BI 출력에 Azure Stream Analytics 작업 인증 (미리 보기)
description: 이 문서에서는 관리 되는 id를 사용 하 여 Azure Stream Analytics 작업을 Power BI 출력에 인증 하는 방법을 설명 합니다.
author: cedarbaum
ms.author: sacedarb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: 264c434849d5d5afb5934873c75d172a3783ac86
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459680"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi-preview"></a>관리 Id를 사용 하 여 Power BI에 Azure Stream Analytics 작업 인증 (미리 보기)

Power BI 출력을 위한 [관리 되는 id 인증은](../active-directory/managed-identities-azure-resources/overview.md) Azure Stream Analytics에 미리 보기로 제공 됩니다. 이렇게 하면 Stream Analytics 작업에서 Power BI 계정 내 작업 영역에 직접 액세스할 수 있습니다. 이 기능을 사용 하면 사용자가 Azure Portal를 통해 Power BI에 대화형으로 로그인 하는 데 더 이상 필요 하지 않으므로 Stream Analytics 작업의 배포가 완전히 자동화 될 수 있습니다. 또한 Power BI에 기록 하는 장기 실행 작업이 이제 정기적으로 다시 인증 작업을 수행 하지 않아도 되므로 더 잘 지원 됩니다.

이 문서에서는 Azure Portal 및 Azure Resource Manager 배포를 통해 Stream Analytics 작업의 Power BI 출력에 대해 관리 되는 Id를 사용 하도록 설정 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 기능을 사용 하려면 다음이 필요 합니다.

- [Pro 라이선스가](https://docs.microsoft.com/power-bi/service-admin-purchasing-power-bi-pro)있는 Power BI 계정

- Power BI 계정 내에서 업그레이드 된 작업 영역입니다. 자세한 내용은이 기능에 대 한 [Power BI의 공지](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) 를 참조 하세요.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Stream Analytics 작업 만들기

1. 새 Stream Analytics 작업을 만들거나 Azure Portal에서 기존 작업을 엽니다. 화면 왼쪽에 있는 메뉴 모음에서 **구성**아래에 있는 **관리 되는 id** 를 선택 합니다. "시스템 할당 관리 Id 사용"이 선택 되어 있는지 확인 한 다음 화면 아래쪽에 있는 **저장** 단추를 선택 합니다.

   ![관리 id Stream Analytics 구성](./media/common/stream-analytics-enable-managed-identity.png)

2. 출력을 구성 하기 전에이 문서의 [Power BI 작업 영역에 대 한 Stream Analytics 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) 섹션의 지침에 따라 Stream Analytics 작업에 Power BI 작업 영역에 대 한 액세스 권한을 부여 합니다.

3. Stream analytics 작업의 **출력** 섹션으로 이동 하 고 **+ 추가**를 선택한 다음 **Power BI**를 선택 합니다. 그런 다음 **권한 부여** 단추를 선택 하 고 Power BI 계정으로 로그인 합니다.

   ![Power BI 계정으로 권한 부여](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. 권한이 부여 되 면 사용자가 액세스할 수 있는 모든 작업 영역이 드롭다운 목록에 채워집니다. 이전 단계에서 권한이 부여 된 작업 영역을 선택 합니다. 그런 다음 "인증 모드"로 **관리 되는 id** 를 선택 합니다. 마지막으로 **저장** 단추를 선택 합니다.

   ![관리 Id를 사용 하 여 출력 Power BI 구성](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager 배포

Azure Resource Manager를 사용 하 여 Stream Analytics 작업의 배포를 완벽 하 게 자동화할 수 있습니다. Azure PowerShell 또는 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)를 사용 하 여 리소스 관리자 템플릿을 배포할 수 있습니다. 아래 예제에서는 Azure CLI을 사용 합니다.


1. 리소스 관리자 템플릿의 리소스 섹션에 다음 속성을 포함 하 여 관리 되는 Id를 사용 하 여 **Microsoft StreamAnalytics/streamingjobs** 리소스를 만들 수 있습니다.

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   이 속성은 Stream Analytics 작업에 대 한 id를 만들고 관리 Azure Resource Manager를 알려 줍니다. 다음은 관리 되는 Id를 사용 하는 Stream Analytics 작업을 배포 하 고 관리 되는 Id를 사용 하는 Power BI 출력 싱크에 리소스 관리자 템플릿 예제입니다.

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
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
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
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

    아래 Azure CLI 명령을 사용 하 여 리소스 그룹 **ExampleGroup** 위의 작업을 배포 합니다.

    ```azurecli
    az group deployment create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. 작업을 만든 후 Azure Resource Manager를 사용 하 여 작업의 전체 정의를 검색 합니다.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    위의 명령은 다음과 같은 응답을 반환 합니다.

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
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
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Power BI의 REST API를 사용 하 여 Power BI 작업 영역에 Stream Analytics 작업을 추가 하려는 경우 반환 된 "principalId"를 적어둡니다.

3. 이제 작업이 만들어졌으므로이 문서의 [Power BI 작업 영역에 대 한 Stream Analytics 작업 액세스 권한 부여](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) 섹션으로 이동 합니다.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Stream Analytics 작업에 Power BI 작업 영역에 대 한 액세스 권한 부여

이제 Stream Analytics 작업이 만들어졌으므로 Power BI 작업 영역에 대 한 액세스 권한을 부여할 수 있습니다.

### <a name="use-the-power-bi-ui"></a>Power BI UI 사용

   > [!Note]
   > UI를 사용 하 여 Power BI 작업 영역에 Stream Analytics 작업을 추가 하려면 Power BI 관리자 포털의 **개발자 설정** 에서 서비스 사용자 액세스를 사용 하도록 설정 해야 합니다. 자세한 내용은 [서비스 주체 시작](https://docs.microsoft.com/power-bi/developer/embed-service-principal#get-started-with-a-service-principal) 을 참조 하세요.

1. 작업 영역 액세스 설정으로 이동 합니다. 자세한 내용은 [작업 영역에 대 한 액세스 권한 부여](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace)문서를 참조 하세요.

2. 텍스트 상자에 Stream Analytics 작업 이름을 입력 하 고 액세스 수준으로 **참가자** 를 선택 합니다.

3. **추가** 를 선택 하 고 창을 닫습니다.

   ![Power BI 작업 영역에 Stream Analytics 작업 추가](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Power BI PowerShell cmdlet 사용

1. Power BI `MicrosoftPowerBIMgmt` PowerShell cmdlet을 설치 합니다.

   > [!Important]
   > Cmdlet의 버전 1.0.821 이상을 사용 하 고 있는지 확인 하세요.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Power BI에 로그인 합니다.

```powershell
Login-PowerBI
```

3. 작업 영역에 Stream Analytics 작업을 참가자로 추가 합니다.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Power BI REST API 사용

"그룹 사용자 추가" REST API를 직접 사용 하 여 Stream Analytics 작업을 작업 영역에 참가자로 추가할 수도 있습니다. 이 API에 대 한 전체 설명서는 [그룹-그룹 사용자 추가에서](https://docs.microsoft.com/rest/api/power-bi/groups/addgroupuser)찾을 수 있습니다.

**샘플 요청**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
요청 본문
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

## <a name="limitations"></a>제한 사항
이 기능의 제한 사항은 다음과 같습니다.

- 클래식 Power BI 작업 영역은 지원 되지 않습니다.

- Azure Active Directory 없는 Azure 계정.

- 다중 테넌트 액세스는 지원 되지 않습니다. 지정 된 Stream Analytics 작업에 대해 만든 서비스 주체는 작업이 만들어진 동일한 Azure Active Directory 테넌트에 상주해 야 하며, 다른 Azure Active Directory 테넌트에 있는 리소스와 함께 사용할 수 없습니다.

- [사용자 할당 id](../active-directory/managed-identities-azure-resources/overview.md) 는 지원 되지 않습니다. 즉, Stream Analytics 작업에서 사용할 고유한 서비스 주체를 입력할 수 없습니다. Azure Stream Analytics에서 서비스 주체를 생성 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics와 Power BI 대시보드 통합](./stream-analytics-power-bi-dashboard.md)
* [Azure Stream Analytics의 출력 이해](./stream-analytics-define-outputs.md)
