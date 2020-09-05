---
title: '빠른 시작: Python을 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 Python을 사용하여 비규격 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 08/10/2020
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: a160b9bc389bc0c902f9644887aa478f80822e60
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134623"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>빠른 시작: Python을 사용하여 비규격 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만듭니다. 완료되면 _비준수_ 가상 머신을 식별하게 됩니다.

Python 라이브러리는 명령줄 또는 스크립트에서 Azure 리소스를 관리하는 데 사용됩니다. 이 가이드에서는 Python 라이브러리를 사용하여 정책 할당을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>정책 라이브러리 추가

Python이 Azure Policy와 작업할 수 있도록 하려면 라이브러리를 추가해야 합니다. 이 라이브러리는 [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬로 설치된 경우를 포함하여 Python을 사용할 수 있을 때마다 작동합니다.

1. 최신 Python이 설치되었는지 확인합니다(**3.8** 이상). 아직 설치되지 않았으면 [Python.org](https://www.python.org/downloads/)에서 다운로드합니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.5.1** 이상). 아직 설치되지 않았으면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

   > [!NOTE]
   > 다음 예시에서 **CLI 기반 인증**을 사용하도록 Python을 설정하려면 Azure CLI가 필요합니다. 다른 옵션들에 대한 자세한 내용은 [Python용 Azure 관리 라이브러리를 사용하여 인증](/azure/developer/python/azure-sdk-authenticate)을 참조하세요.

1. Azure CLI를 통해 인증을 수행합니다.

   ```azurecli
   az login
   ```

1. 선택한 Python 환경에서 Azure Resource Graph에 필요한 라이브러리를 설치합니다.

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > 모든 사용자용으로 Python이 설치된 경우에는 이러한 명령은 관리자 권한 콘솔에서 실행해야 합니다.

1. 라이브러리가 설치되어 있는지 확인 합니다. `azure-mgmt-policyinsights`는 **0.5.0** 이상이고, `azure-mgmt-resource`는 **9.0.0** 이상이고, `azure-cli-core`는 **2.5.0** 이상이어야 합니다.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사**(`06a78e20-9358-41c9-923c-fb736d382a4d`) 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

다음 코드를 실행하여 새 정책 할당을 만듭니다.

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

이 명령은 다음 정보를 사용합니다.

할당 세부 정보:
- **display_name** - 정책 할당에 대한 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다.
- **policy_definition_id** – 할당을 만드는 데 기준으로 사용되는 정책 정의 경로입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다. 이 예제에서는 정책 정의가 기본 제공되며 경로에는 관리 그룹 또는 구독 정보가 포함되지 않습니다.
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 관리 그룹에서 개별 리소스에 이르기까지 다양할 수 있습니다. `{scope}`를 다음 패턴 중 하나로 바꾸어야 합니다.
  - 관리 그룹: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 구독: `/subscriptions/{subscriptionId}`
  - 리소스 그룹: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 리소스: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **설명** - 정책에서 수행하는 작업 또는 이 범위에 할당된 이유에 대한 자세한 설명입니다.

할당 생성:

- 범위 - 이 범위는 정책 할당이 저장되는 위치를 결정합니다. 할당 세부 정보에 설정된 범위는 이 범위 내에 있어야 합니다.
- 이름 - 할당의 실제 이름입니다. 이 예제에서는 _audit-vm-manageddisks_가 사용되었습니다.
- 정책 할당 - 이전 단계에서 만든 Python **PolicyAssignment** 개체입니다.

이제 비규격 리소스를 식별하여 환경의 규정 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>비규격 리소스 식별

만든 정책 할당을 준수하지 않는 리소스를 식별하기 위해 다음 정보를 사용합니다. 다음 코드를 실행하세요.

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

`{subscriptionId}`를 이 정책 할당에 대한 규정 준수 결과를 보려는 구독으로 바꿉니다. 다른 범위와 데이터를 요약하는 방법 목록은 [정책 상태 메서드](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods)를 참조하세요.

결과는 다음 예제와 유사합니다.

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

결과는 Azure Portal 보기에서 정책 할당의 **리소스 규정 준수** 탭에 보이는 것과 일치합니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 명령을 사용합니다.

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

`{scope}`를 정책 할당을 만드는 데 사용한 것과 동일한 범위로 바꿉니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하기 위해 정책 정의를 할당하는 방법에 대해 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)