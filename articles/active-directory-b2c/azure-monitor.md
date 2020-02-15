---
title: Azure Monitor를 사용 하 여 Azure AD B2C 모니터링
titleSuffix: Azure AD B2C
description: 위임 된 리소스 관리를 사용 하 여 Azure Monitor로 Azure AD B2C 이벤트를 기록 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.author: marsma
ms.subservice: B2C
ms.date: 02/10/2020
ms.openlocfilehash: 6f7f0252a6377397ccaccdc44c9c8561da7c9d29
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121388"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Azure Monitor를 사용 하 여 Azure AD B2C 모니터링

Azure Monitor를 사용 하 여 Azure Active Directory B2C (Azure AD B2C) 로그인 및 [감사](view-audit-logs.md) 로그를 다른 모니터링 솔루션으로 라우팅합니다. 장기 사용을 위해 로그를 유지 하거나 타사 SIEM (보안 정보 및 이벤트 관리) 도구와 통합 하 여 사용자 환경에 대 한 통찰력을 얻을 수 있습니다.

다음과 같이 로그 이벤트를 라우팅할 수 있습니다.

* Azure [저장소 계정](../storage/blobs/storage-blobs-introduction.md).
* Azure [이벤트 허브](../event-hubs/event-hubs-about.md) (및 Splunk 및 Sumo 논리 인스턴스와 통합)
* 데이터를 분석 하 고, 대시보드를 만들고, 특정 이벤트에 대해 경고 하는 [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs-collect-workspace.md) 입니다.

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료 하려면 Azure PowerShell 모듈을 사용 하 여 Azure Resource Manager 템플릿을 배포 합니다.

* [Azure PowerShell 모듈](https://docs.microsoft.com/powershell/azure/install-az-ps) 버전 6.13.1 이상

최신 버전의 Azure PowerShell 모듈을 포함 하는 [Azure Cloud Shell](https://shell.azure.com)사용할 수도 있습니다.

## <a name="delegated-resource-management"></a>위임 된 리소스 관리

Azure AD B2C는 [Azure Active Directory 모니터링](../active-directory/reports-monitoring/overview-monitoring.md)을 활용 합니다. Azure AD B2C 테 넌 트 내의 Azure Active Directory에서 *진단 설정을* 사용 하도록 설정 하려면 [위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 합니다.

사용자가 Azure AD B2C 디렉터리 ( **서비스 공급자**)의 사용자 또는 그룹에 게 Azure 구독 ( **고객**)이 포함 된 테 넌 트 내에서 Azure Monitor 인스턴스를 구성 하도록 권한을 부여 합니다. 권한 부여를 만들려면 구독이 포함 된 Azure AD 테 넌 트에 [Azure Resource Manager](../azure-resource-manager/index.yml) 템플릿을 배포 합니다. 다음 섹션에서는이 프로세스를 안내 합니다.

## <a name="create-or-choose-resource-group"></a>리소스 그룹 만들기 또는 선택

Azure Monitor에서 데이터를 받을 대상 Azure storage 계정, event hub 또는 Log Analytics 작업 영역을 포함 하는 리소스 그룹입니다. Azure Resource Manager 템플릿을 배포할 때 리소스 그룹 이름을 지정 합니다.

[리소스 그룹을 만들거나](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) Azure AD B2C 테 넌 트를 포함 하는 디렉터리가 *아니라* azure 구독을 포함 하는 Azure Active Directory (azure AD) 테 넌 트에서 기존 리소스 그룹을 선택 합니다.

이 예제에서는 *미국 중부* 지역의 *b2c* 이라는 리소스 그룹을 사용 합니다.

## <a name="delegate-resource-management"></a>리소스 관리 위임

다음에는 다음 정보를 수집 합니다.

Azure AD B2C 디렉터리 (테 넌 트 ID 라고도 함)의 **디렉터리 ID** 입니다.

1. *사용자 관리자* 역할 (또는 이상)이 있는 사용자로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. **Azure Active Directory**를 선택 하 고 **속성**을 선택 합니다.
1. **디렉터리 ID**를 기록 합니다.

구독을 포함 하는 디렉터리에서 이전에 만든 리소스 그룹에 대 한 *참가자* 권한을 부여 하려는 Azure AD B2C 그룹 또는 사용자의 **개체 ID** 입니다.

관리를 용이 하 게 하려면 각 역할에 대해 Azure AD 사용자 *그룹* 을 사용 하 여 해당 사용자에 게 직접 사용 권한을 할당 하는 대신 그룹에 개별 사용자를 추가 하거나 제거할 수 있도록 하는 것이 좋습니다. 이 연습에서는 사용자를 추가 합니다.

1. Azure Portal에서 **Azure Active Directory** 선택 된 상태에서 **사용자를 선택한 다음 사용자를 선택**합니다.
1. 사용자의 **개체 ID**를 기록 합니다.

### <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

Azure AD 테 넌 트 ( **고객**)를 등록 하려면 다음 정보를 사용 하 여 제품에 대 한 [Azure Resource Manager 템플릿을](../lighthouse/how-to/onboard-customer.md) 만듭니다. `mspOfferName` 및 `mspOfferDescription` 값은 Azure Portal의 [서비스 공급자 페이지](../lighthouse/how-to/view-manage-service-providers.md) 에서 제안 세부 정보를 볼 때 표시 됩니다.

| 필드   | 정의 |
|---------|------------|
| `mspOfferName`                     | 이 정의를 설명하는 이름입니다. 예를 들어 *관리 되는 서비스를 Azure AD B2C*합니다. 이 값은 고객에게 제품의 제목으로 표시됩니다. |
| `mspOfferDescription`              | 제안에 대 한 간단한 설명입니다. 예를 들어는 *Azure AD B2C에서 Azure Monitor를 사용 하도록 설정*합니다.|
| `rgName`                           | Azure AD 테 넌 트에서 앞에서 만든 리소스 그룹의 이름입니다. 예: *b2c-monitor*. |
| `managedByTenantId`                | Azure AD B2C 테 넌 트의 **디렉터리 id** (테 넌 트 id 라고도 함)입니다. |
| `authorizations.value.principalId` | 이 Azure 구독의 리소스에 대 한 액세스 권한이 있는 B2C 그룹 또는 사용자의 **개체 ID** 입니다. 이 연습에서는 이전에 기록한 사용자의 개체 ID를 지정 합니다. |

Azure Resource Manager 템플릿 및 매개 변수 파일을 다운로드 합니다.

- [rgDelegatedResourceManagement.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)
- [rgDelegatedResourceManagement.parameters.json](https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)

다음으로 매개 변수 파일을 앞에서 기록한 값으로 업데이트 합니다. 다음 JSON 코드 조각은 Azure Resource Manager 템플릿 매개 변수 파일의 예를 보여 줍니다. `authorizations.value.roleDefinitionId`의 경우 *참가자 역할*에 대 한 [기본 제공 역할](../role-based-access-control/built-in-roles.md) 값 `b24988ac-6180-42a0-ab88-20f7382dd24c`를 사용 합니다.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Azure AD B2C Managed Services"
        },
        "mspOfferDescription": {
            "value": "Enables Azure Monitor in Azure AD B2C"
        },
        "rgName": {
            "value": "azure-ad-b2c-monitor"
        },
        "managedByTenantId": {
            "value": "<Replace with DIRECTORY ID of Azure AD B2C tenant (tenant ID)>"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "<Replace with user's OBJECT ID>",
                    "principalIdDisplayName": "Azure AD B2C tenant administrator",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                }
            ]
        }
    }
}
```

### <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 배포

매개 변수 파일을 업데이트 한 후에는 구독 수준 배포로 Azure Resource Manager 템플릿을 Azure 테 넌 트에 배포 합니다. 이 배포는 구독 수준 배포이므로 Azure Portal에서 시작할 수 없습니다. Azure PowerShell 모듈 또는 Azure CLI를 사용 하 여 배포할 수 있습니다. Azure PowerShell 메서드는 다음과 같습니다.

[AzAccount](/powershell/azure/authenticate-azureps)를 사용 하 여 구독을 포함 하는 디렉터리에 로그인 합니다. `-tenant` 플래그를 사용 하 여 올바른 디렉터리에 대 한 인증을 적용 합니다.

```PowerShell
Connect-AzAccount -tenant contoso.onmicrosoft.com
```

[AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용 하 여 Azure AD 테 넌 트에서 현재 계정이 액세스할 수 있는 구독을 나열 합니다. Azure AD B2C 테 넌 트에 프로젝션 하려는 구독의 ID를 기록 합니다.

```PowerShell
Get-AzSubscription
```

다음으로 Azure AD B2C 테 넌 트에 프로젝션 하려는 구독으로 전환 합니다.

``` PowerShell
Select-AzSubscription <subscription ID>
```

마지막으로, 이전에 다운로드 하 고 업데이트 한 Azure Resource Manager 템플릿 및 매개 변수 파일을 배포 합니다. `Location`, `TemplateFile`및 `TemplateParameterFile` 값을 적절 하 게 바꿉니다.

```PowerShell
New-AzDeployment -Name "AzureADB2C" `
                 -Location "centralus" `
                 -TemplateFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.json" `
                 -TemplateParameterFile "C:\Users\azureuser\Documents\rgDelegatedResourceManagement.parameters.json" `
                 -Verbose
```

템플릿이 성공적으로 배포 되 면 다음과 유사한 출력이 생성 됩니다 (간단 하 게 하기 위해 출력 잘림).

```Console
PS /usr/csuser/clouddrive> New-AzDeployment -Name "AzureADB2C" `
>>                  -Location "centralus" `
>>                  -TemplateFile "rgDelegatedResourceManagement.json" `
>>                  -TemplateParameterFile "rgDelegatedResourceManagement.parameters.json" `
>>                  -Verbose
WARNING: Breaking changes in the cmdlet 'New-AzDeployment' :
WARNING:  - The cmdlet 'New-AzSubscriptionDeployment' is replacing this cmdlet.


WARNING: NOTE : Go to https://aka.ms/azps-changewarnings for steps to suppress this breaking change warning, and other information on breaking changes in Azure PowerShell.
VERBOSE: 7:25:14 PM - Template is valid.
VERBOSE: 7:25:15 PM - Create template deployment 'AzureADB2C'
VERBOSE: 7:25:15 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:42 PM - Resource Microsoft.ManagedServices/registrationDefinitions '44444444-4444-4444-4444-444444444444' provisioning status is succeeded
VERBOSE: 7:25:48 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:53 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is running
VERBOSE: 7:25:53 PM - Checking deployment status in 5 seconds
VERBOSE: 7:25:59 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is running
VERBOSE: 7:26:17 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:23 PM - Resource Microsoft.ManagedServices/registrationAssignments '11111111-1111-1111-1111-111111111111' provisioning status is succeeded
VERBOSE: 7:26:23 PM - Checking deployment status in 5 seconds
VERBOSE: 7:26:29 PM - Resource Microsoft.Resources/deployments 'rgAssignment' provisioning status is succeeded

DeploymentName          : AzureADB2C
Location                : centralus
ProvisioningState       : Succeeded
Timestamp               : 1/31/20 7:26:24 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                   Type                       Value
                          =====================  =========================  ==========
                          mspOfferName           String                     Azure AD B2C Managed Services
                          mspOfferDescription    String                     Enables Azure Monitor in Azure AD B2C
...
```

템플릿을 배포한 후 리소스 프로젝션을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 다음 섹션으로 이동 하 여 구독을 선택 하기 전에 몇 분 (일반적으로 5 개이 하)을 기다려야 할 수 있습니다.

## <a name="select-your-subscription"></a>구독 선택

템플릿을 배포 하 고 리소스 프로젝션이 완료 될 때까지 몇 분 정도 기다린 후에는 다음 단계를 사용 하 여 구독을 Azure AD B2C 디렉터리에 연결 합니다.

1. 현재 로그인 되어 있는 경우 Azure Portal에서 **로그 아웃** 합니다. 이 단계와 다음 단계를 수행 하 여 포털 세션에서 자격 증명을 새로 고칠 수 있습니다.
1. Azure AD B2C 관리 계정으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 합니다.
1. 구독을 포함 하는 디렉터리를 선택 합니다.

    ![디렉터리 전환](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)
1. 올바른 디렉터리 및 구독을 선택 했는지 확인 합니다. 이 예에서는 모든 디렉터리와 구독이 선택 됩니다.

    ![디렉터리 & 구독 필터에서 선택한 모든 디렉터리](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="configure-diagnostic-settings"></a>진단 설정 구성

진단 설정은 리소스에 대 한 로그 및 메트릭을 보내야 하는 위치를 정의 합니다. 가능한 대상은 다음과 같습니다.

- [Azure Storage 계정](../azure-monitor/platform/resource-logs-collect-storage.md)
- [Event hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) 솔루션.
- [Log Analytics 작업 영역](../azure-monitor/platform/resource-logs-collect-workspace.md)

아직 선택 하지 않은 경우 [Azure Resource Manager 템플릿에서](#create-an-azure-resource-manager-template)지정한 리소스 그룹에서 선택한 대상 유형의 인스턴스를 만듭니다.

### <a name="create-diagnostic-settings"></a>진단 설정 만들기

Azure Portal에서 [진단 설정을 만들](../active-directory/reports-monitoring/overview-monitoring.md) 준비가 되었습니다.

활동 로그 Azure AD B2C에 대 한 모니터링 설정을 구성 하려면:

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테 넌 트가 포함 된 디렉터리를 선택 합니다.
1. **Azure Active Directory** 선택
1. **모니터링** 아래에서 **진단 설정**을 선택합니다.
1. 리소스에 기존 설정이 있는 경우 이미 구성 된 설정 목록이 표시 됩니다. **진단 설정 추가** 를 선택 하 여 새 설정을 추가 하거나 설정을 **편집** 하 여 기존 설정을 편집 합니다. 각 설정에는 대상 유형 중 하나만 지정할 수 있습니다.

    ![Azure Portal의 진단 설정 창](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. 아직 없는 경우 설정 이름을 지정 합니다.
1. 로그를 보낼 각 대상에 대 한 확인란을 선택 합니다. **구성** 을 선택 하 여 다음 표에 설명 된 대로 설정을 지정 합니다.

    | 설정 | Description |
    |:---|:---|
    | 스토리지 계정에 보관 | 저장소 계정의 이름입니다. |
    | 이벤트 허브로 스트림 | 이벤트 허브가 생성 되는 네임 스페이스 (처음으로 로그를 스트리밍하는 경우) 또는 스트리밍되는 경우 (해당 로그 범주를이 네임 스페이스로 스트리밍하는 리소스가 이미 있는 경우).
    | Log Analytics에 보내기 | 작업 영역의 이름입니다. |

1. **AuditLogs** 및 **SignInLogs**를 선택 합니다.
1. **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure Monitor에서 진단 설정을 추가 하 고 구성 하는 방법에 대 한 자세한 내용은 [자습서: Azure 리소스에서 리소스 로그 수집 및 분석](../azure-monitor/insights/monitor-azure-resource.md)을 참조 하세요.

Azure AD 로그를 이벤트 허브로 스트리밍하는 방법에 대 한 자세한 내용은 [자습서: azure 이벤트 허브에 로그 Azure Active Directory](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)스트리밍을 참조 하세요.
