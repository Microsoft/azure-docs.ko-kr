---
title: Azure Lighthouse에 고객 온보딩
description: Azure Lighthouse에 고객을 등록 하 여 Azure 위임 된 리소스 관리를 통해 자신의 테 넌 트를 통해 해당 리소스에 액세스 하 고 관리할 수 있도록 하는 방법을 알아봅니다.
ms.date: 08/20/2020
ms.topic: how-to
ms.openlocfilehash: db6a819c72f1ef46f542ed47cad6caae23c0d191
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719056"
---
# <a name="onboard-a-customer-to-azure-lighthouse"></a>Azure Lighthouse에 고객 온보딩

이 문서에서는 서비스 공급자로 고객을 Azure Lighthouse에 등록할 수 있는 방법을 설명 합니다. 이렇게 하면 [azure 위임 된 리소스 관리](../concepts/azure-delegated-resource-management.md)를 사용 하 여 고객의 위임 된 리소스 (구독 및/또는 리소스 그룹)를 자체 Azure Active Directory (azure AD) 테 넌 트를 통해 액세스 하 고 관리할 수 있습니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만 [여러 테 넌 트를 관리](../concepts/enterprise.md) 하는 기업은 동일한 프로세스를 사용 하 여 Azure Lighthouse를 설정 하 고 관리 환경을 통합할 수 있습니다.

여러 고객에 대 한 온 보 딩 프로세스를 반복할 수 있습니다. 적절 한 권한이 있는 사용자가 관리 되는 테 넌 트에 로그인 하면 해당 사용자는 모든 개별 고객 테 넌 트에 로그인 하지 않고도 관리 작업을 수행 하기 위해 고객 테 넌 트 범위에서 권한을 부여할 수 있습니다.

고객 계약에 미치는 영향을 추적하고 인지도를 얻으려면 MPN(Microsoft 파트너 네트워크) ID를 온보딩한 각 구독에 대해 액세스 권한이 있는 하나 이상의 사용자 계정과 연결합니다. 서비스 공급자 테 넌 트에서이 연결을 수행 해야 합니다. 간단히 하기 위해, 테넌트에서 MPN ID와 연결된 서비스 주체 계정을 만들고 온보딩한 모든 고객에 대한 읽기 권한자 액세스 권한을 이 계정에 부여하는 것이 좋습니다. 자세한 내용은 [Azure 계정에 파트너 ID 연결](../../cost-management-billing/manage/link-partner-id.md)을 참조하세요.

> [!NOTE]
> 또한 고객은 [Azure Marketplace에 게시](publish-managed-services-offers.md)하는 관리 서비스 제품 (공용 또는 개인)을 구매할 때 Azure Lighthouse에 등록 수 있습니다. 여기에 설명 된 온 보 딩 프로세스를 사용 하 여 Azure Marketplace에 게시 된 제품을 함께 사용할 수도 있습니다.

온보딩 프로세스를 사용하려면 서비스 공급자의 테넌트와 고객의 테넌트 둘 다에서 작업을 수행해야 합니다. 이러한 모든 단계는 이 문서에 설명되어 있습니다.

## <a name="gather-tenant-and-subscription-details"></a>테넌트 및 구독 세부 정보 수집

고객의 테넌트를 온보딩하려면 활성 Azure 구독이 있어야 합니다. 또한 다음 사항을 알고 있어야 합니다.

- 서비스 공급자 테넌트의 테넌트 ID(고객의 리소스를 관리하는 위치)
- 고객 테넌트의 테넌트 ID(서비스 공급자가 관리하는 리소스 포함)
- 서비스 공급자가 관리할(또는 서비스 공급자가 관리할 리소스 그룹을 포함하는) 고객 테넌트에 있는 각 특정 구독의 구독 ID

> [!NOTE]
> 구독 내에서 하나 이상의 리소스 그룹을 등록하려는 경우에도 구독 수준에서 배포를 수행해야 하므로 구독 ID가 필요합니다.

이러한 ID 값을 아직 모르는 경우 다음 방법 중 하나를 통해 검색할 수 있습니다. 배포에서 이러한 정확한 값을 사용해야 합니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal의 오른쪽 위에 있는 계정 이름으로 마우스를 가져가거나 **디렉터리 전환**을 선택하여 테넌트 ID를 확인할 수 있습니다. 테넌트 ID를 선택하고 복사하려면 포털 내에서 "Azure Active Directory"를 검색한 다음, **속성**을 선택하고 **디렉터리 ID** 필드에 표시된 값을 복사합니다. 고객 테넌트에서 구독의 ID를 찾으려면 "구독"을 검색한 다음, 적절한 구독 ID를 선택합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> 여기에 설명된 프로세스를 사용하여 구독 또는 구독 내의 하나 이상의 리소스 그룹을 온보딩하면 **Microsoft.ManagedServices** 리소스 공급자가 해당 구독에 등록됩니다.

## <a name="define-roles-and-permissions"></a>역할 및 권한 정의

서비스 공급자는 단일 고객을 위해 여러 작업을 수행하고, 범위마다 다른 액세스 권한을 요구할 수 있습니다. 테 넌 트의 사용자에 게 적절 한 [RBAC (역할 기반 액세스 제어) 기본 제공 역할](../../role-based-access-control/built-in-roles.md) 을 할당 하기 위해 필요한 만큼의 권한 부여를 정의할 수 있습니다.

관리를 용이 하 게 하려면 각 역할에 대해 Azure AD 사용자 그룹을 사용 하는 것이 좋습니다. 이를 통해 액세스 권한이 있는 그룹에 개별 사용자를 추가 하거나 제거할 수 있으므로 사용자를 변경 하기 위해 온 보 딩 프로세스를 반복할 필요가 없습니다. 자동화 시나리오에 유용할 수 있는 서비스 주체에 역할을 할당할 수 있습니다.

권한 부여를 정의할 때는 사용자가 작업을 완료 하는 데 필요한 권한만 갖도록 최소 권한 원칙을 따라야 합니다. 지원 되는 역할에 대 한 지침 및 정보는 [Azure Lighthouse 시나리오에서 테 넌 트, 사용자 및 역할](../concepts/tenants-users-roles.md)을 참조 하세요.

> [!IMPORTANT]
> Azure AD 그룹에 대한 사용 권한을 추가하려면 **그룹 유형**이 **Office 365**가 아닌 **보안**이어야 합니다. 이 옵션은 그룹을 만들 때 선택됩니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

권한 부여를 정의 하려면 액세스 권한을 부여 하려는 서비스 공급자 테 넌 트의 각 사용자, 사용자 그룹 또는 서비스 사용자에 대 한 ID 값을 알고 있어야 합니다. 또한 할당하려는 각 기본 제공 역할에 대한 역할 정의 ID도 필요합니다. 아직 없는 경우 서비스 공급자 테넌트 내에서 아래 명령을 실행하여 검색할 수 있습니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>' | Get-AzADServicePrincipal).Id

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --id "<yourUPN>" --query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

> [!TIP]
> 나중에 필요할 때 테넌트의 사용자가 [위임에 대한 액세스 권한을 제거](remove-delegation.md)할 수 있도록 고객을 온보딩할 때 [관리 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)을 할당하는 것이 좋습니다. 이 역할을 할당하지 않으면 고객 테넌트의 사용자만 위임된 리소스를 제거할 수 있습니다.

## <a name="create-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

고객을 온보딩하려면 다음 정보가 포함된 제품에 대한 [Azure Resource Manager](../../azure-resource-manager/index.yml) 템플릿을 만들어야 합니다. **MspOfferName** 및 **mspOfferDescription** 값은 Azure Portal의 [서비스 공급자 페이지](view-manage-service-providers.md) 에서 고객에 게 표시 됩니다.

|필드  |정의  |
|---------|---------|
|**mspOfferName**     |이 정의를 설명하는 이름입니다. 이 값은 고객에 게 제품의 제목으로 표시 되며 고유한 값 이어야 합니다.        |
|**mspOfferDescription**     |제품에 대한 간단한 설명(예: "Contoso VM 관리 제품")      |
|**managedByTenantId**     |테넌트 ID          |
|**권한 부여**     |테넌트의 사용자/그룹/SPN에 대한 **principalid** 값으로, 각 값은 고객이 권한 부여의 목적을 이해하는 데 도움이 되는 **principalIdDisplayName** 값을 가지며, 액세스 수준을 지정하는 기본 제공 **roleDefinitionId** 값에 매핑되어 있습니다.      |

온보딩 프로세스에는 [샘플 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)에 제공되는 Azure Resource Manager 템플릿 및 구성과 일치하도록 수정하고 사용자의 권한 부여를 정의하는 해당 매개 변수 파일이 필요합니다.

선택하는 템플릿은 전체 구독, 리소스 그룹 또는 구독 내의 여러 리소스 그룹 중 어떤 항목을 온보딩할지에 따라 달라집니다. 또한 이러한 방식으로 구독을 온보딩하려는 경우, Azure Marketplace에 게시한 관리형 서비스 제품을 구매한 고객에게 사용할 수 있는 템플릿도 제공합니다.

|온보딩할 대상  |사용하는 Azure Resource Manager 템플릿  |수정할 매개 변수 파일 |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|구독 내의 여러 리소스 그룹   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|구독(Azure Marketplace 게시된 제품을 사용하는 경우)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> 여기에 설명 된 프로세스에는 동일한 고객 테 넌 트에 구독을 등록 하는 경우에도 등록 되는 각 구독에 대 한 별도의 배포가 필요 합니다. 동일한 고객 테넌트의 여러 다른 구독 내에서 여러 리소스 그룹을 온보딩하는 경우에도 별도의 배포가 필요합니다. 그러나 단일 구독 내에 여러 리소스 그룹을 온보딩하는 것은 하나의 배포에서 수행할 수 있습니다.
>
> 동일한 구독 또는 구독 내의 리소스 그룹에 여러 제품이 적용되는 경우 별도의 배포도 필요합니다. 적용된 각 제품은 다른 **mspOfferName**을 사용해야 합니다.

다음 예제에서는 구독을 온보딩하는 데 사용할 수 있는 수정된 **delegatedResourceManagement.parameters.json** 파일을 보여 줍니다. 리소스 그룹 매개 변수 파일([rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) 폴더에 있음)은 유사하지만 온보딩할 특정 리소스 그룹을 식별하기 위한 **rgName** 매개 변수를 포함합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```

위 예제의 마지막 권한 부여는 사용자 액세스 관리자 역할이 있는 **principalId**를 추가합니다(18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). 이 역할을 할당할 때 **delegatedRoleDefinitionIds** 속성 및 하나 이상의 기본 제공 역할을 포함해야 합니다. 이 권한 부여에서 만든 사용자는 이러한 기본 제공 역할을 고객 테 넌 트의 [관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md) 에 할당할 수 있습니다. 이러한 기본 제공 역할은 [재구성 가능한 정책을 배포](deploy-policy-remediation.md)하는 데 필요 합니다.  또한 사용자는 지원 인시던트를 만들 수 있습니다.  사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 이 사용자에게 적용되지 않습니다.

## <a name="deploy-the-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿 배포

매개 변수 파일을 업데이트 한 후 고객 테 넌 트의 사용자는 테 넌 트 내에 Azure Resource Manager 템플릿을 배포 해야 합니다. 등록 하려는 각 구독 (또는 등록 하려는 리소스 그룹을 포함 하는 각 구독)에 대해 별도의 배포가 필요 합니다.

> [!IMPORTANT]
> 이 배포는 등록 중인 구독에 대 한 [소유자 기본 제공 역할](../../role-based-access-control/built-in-roles.md#owner) 을 가진 고객 테 넌 트의 비 게스트 계정 (또는 등록 되는 리소스 그룹을 포함 하는)에 의해 수행 되어야 합니다. 구독을 위임할 수 있는 모든 사용자를 보기 위해 고객 테넌트의 사용자는 Azure Portal에서 구독을 선택하고, **IAM(액세스 제어)** 을 열고, [소유자 역할이 있는 모든 소유자를 볼 수 있습니다](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription). 
>
> [CSP(클라우드 솔루션 공급자) 프로그램](../concepts/cloud-solution-provider.md)를 통해 구독을 만든 경우 서비스 공급자 테넌트에서 [관리 에이전트](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) 역할이 있는 모든 사용자가 배포를 수행할 수 있습니다.

배포는 아래와 같이 PowerShell을 사용 하거나 Azure CLI를 사용 하 여 Azure Portal에서 수행할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [GitHub 리포지토리](https://github.com/Azure/Azure-Lighthouse-samples/)에서 사용 하려는 템플릿 옆에 있는 **Azure에 배포** 단추를 선택 합니다. 그러면 Azure Portal에서 템플릿이 열립니다.
1. **Msp 제품 이름**, **msp 제안 설명**, **테 넌 트 Id로 관리**및 **권한 부여**에 대 한 값을 입력 합니다. 원하는 경우 매개 **변수 편집** 을 선택 하 여 `mspOfferName` `mspOfferDescription` `managedbyTenantId` `authorizations` 매개 변수 파일에서,, 및에 대 한 값을 직접 입력할 수 있습니다. 템플릿의 기본값을 사용 하는 대신 이러한 값을 업데이트 해야 합니다.
1. **검토 및 만들기**를 선택 하 고 **만들기**를 선택 합니다.

몇 분 후에 배포가 완료 되었다는 알림이 표시 됩니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzSubscriptionDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create --name <deploymentName> \
                     --location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>온보딩 성공 확인

고객 구독이 Azure Lighthouse로 성공적으로 등록 된 경우 서비스 공급자의 테 넌 트에 있는 사용자는 구독 및 해당 리소스를 볼 수 있습니다 (위의 프로세스를 통해 개별적으로 또는 적절 한 권한이 있는 Azure AD 그룹의 멤버로 액세스 권한이 부여 된 경우). 이를 확인하려면 다음 방법 중 하나를 사용하여 구독이 표시되는지 확인합니다.  

### <a name="azure-portal"></a>Azure portal

서비스 공급자의 테넌트에서 다음을 수행합니다.

1. [내 고객 페이지](view-manage-customers.md)로 이동합니다.
2. **고객**을 선택합니다.
3. Resource Manager 템플릿에서 제공한 제품 이름의 구독을 볼 수 있는지 확인합니다.

> [!IMPORTANT]
> [내 고객](view-manage-customers.md)의 위임 된 구독을 보려면 구독이 등록 때 서비스 공급자의 테 넌 트에 있는 사용자에 게 [읽기](../../role-based-access-control/built-in-roles.md#reader) 권한자 역할 (또는 읽기 권한자 액세스를 포함 하는 다른 기본 제공 역할)이 부여 되어 있어야 합니다.

고객의 테넌트에서 다음을 수행합니다.

1. [서비스 공급자 페이지](view-manage-service-providers.md)로 이동합니다.
2. **서비스 공급자 제품**을 선택합니다.
3. Resource Manager 템플릿에서 제공한 제품 이름의 구독을 볼 수 있는지 확인합니다.

> [!NOTE]
> 배포가 완료되고 업데이트가 Azure Portal에 반영되기까지 몇 분 정도 걸릴 수 있습니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
- 이전에 등록된 [위임에 대한 액세스 권한 제거](remove-delegation.md) 방법을 알아봅니다.
