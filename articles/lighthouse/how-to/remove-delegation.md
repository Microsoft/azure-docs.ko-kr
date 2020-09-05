---
title: 위임에 대한 액세스 권한 제거
description: Azure Lighthouse의 서비스 공급자에 게 위임 된 리소스에 대 한 액세스를 제거 하는 방법에 대해 알아봅니다.
ms.date: 08/12/2020
ms.topic: how-to
ms.openlocfilehash: 72a2c49dde8cccfcc298d4128384a10bb7e8840f
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167233"
---
# <a name="remove-access-to-a-delegation"></a>위임에 대한 액세스 권한 제거

고객의 구독 또는 리소스 그룹이 [Azure Lighthouse](../overview.md)의 서비스 공급자에 게 위임 된 후 필요한 경우 위임이 제거 될 수 있습니다. 위임이 제거 되 면 서비스 공급자 테 넌 트의 사용자에 게 이전에 부여 된 [Azure 위임 된 리소스 관리](../concepts/azure-delegated-resource-management.md) 액세스가 더 이상 적용 되지 않습니다.

사용자에 게 적절 한 권한이 있는 한 고객 테 넌 트 또는 서비스 공급자 테 넌 트의 사용자가 위임 제거를 수행할 수 있습니다.

> [!TIP]
> 이 항목의 서비스 공급자 및 고객을 참조 하지만 [여러 테 넌 트를 관리](../concepts/enterprise.md) 하는 기업은 동일한 프로세스를 사용할 수 있습니다.

## <a name="customers"></a>고객

구독에 대 한 [소유자 기본 제공 역할이](../../role-based-access-control/built-in-roles.md#owner) 있는 고객 테 넌 트의 사용자는 해당 구독 또는 해당 구독의 리소스 그룹에 대 한 서비스 공급자 액세스를 제거할 수 있습니다. 이렇게 하려면 고객 테 넌 트의 사용자가 Azure Portal의 [서비스 공급자 페이지로](view-manage-service-providers.md#add-or-remove-service-provider-offers) 이동 하 여 **서비스 공급자가 제공** 하는 화면에서 제품을 찾은 다음 해당 제품에 대 한 행에서 휴지통 아이콘을 선택할 수 있습니다.

삭제를 확인 한 후에는 서비스 공급자의 테 넌 트에 있는 사용자가 이전에 위임 된 리소스에 액세스할 수 없게 됩니다.

## <a name="service-providers"></a>서비스 공급자

관리 테 넌 트의 사용자는 고객의 리소스에 대 한 [관리 되는 서비스 등록 할당 삭제 역할이](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) 부여 된 경우 위임 된 리소스에 대 한 액세스 권한을 제거할 수 있습니다. 서비스 공급자 사용자에 게이 역할을 할당 하지 않은 경우 고객 테 넌 트의 사용자만이 위임을 제거할 수 있습니다.

아래 예제에서는 [온 보 딩 프로세스](onboard-customer.md)중에 매개 변수 파일에 포함할 수 있는 **관리 되는 서비스 등록 할당 삭제 역할** 을 부여 하는 할당을 보여 줍니다.

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Azure Marketplace에 게시할 [관리 서비스 제품을 만들](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#authorization) 때 **권한 부여** 에서이 역할을 선택할 수도 있습니다.

이 권한이 있는 사용자는 다음 방법 중 하나를 사용하여 위임을 제거할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [내 고객 페이지](view-manage-customers.md)로 이동합니다.
2. **위임**을 선택 합니다.
3. 제거 하려는 위임을 찾은 다음 해당 행에 표시 되는 휴지통 아이콘을 선택 합니다.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>다음 단계

- [Azure 위임 리소스 관리](../concepts/azure-delegated-resource-management.md)에 대해 알아봅니다.
- Azure Portal의 **내 고객**으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
