---
title: '빠른 시작: REST API를 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 REST API를 사용하여 비규격 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 04880ef013060bc5ff12618af6a9156295a26a88
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88137094"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-rest-api"></a>빠른 시작: REST API를 사용하여 비규격 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다.
이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만들고 할당하는 과정을 단계별로 안내합니다.

이 과정이 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당을 _비준수_합니다.

REST API는 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 가이드에서는 REST API를 사용하여 Azure 환경 내에서 비규격 리소스를 식별하고 정책 할당을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 아직 설치하지 않은 경우 [ARMClient](https://github.com/projectkudu/ARMClient)를 설치합니다. Azure Resource Manager 기반 REST API에 HTTP 요청을 전송하는 도구입니다. 또는 REST 설명서나 PowerShell의 [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 또는 [Postman](https://www.postman.com)과 같은 도구에서 "사용해보기" 기능을 사용할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사**(`06a78e20-9358-41c9-923c-fb736d382a4d`) 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

정책 할당을 만들려면 다음 명령을 실행합니다.

   - REST API URI

     ```http
     PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
     ```

   - 요청 본문

     ```json
     {
       "properties": {
         "displayName": "Audit VMs without managed disks Assignment",
         "description": "Shows all virtual machines not using managed disks",
         "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d",
       }
     }
     ```

이전 엔드포인트 및 요청 본문은 다음 정보를 사용합니다.

REST API URI:
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 관리 그룹에서 개별 리소스에 이르기까지 다양할 수 있습니다. `{scope}`를 다음 패턴 중 하나로 바꾸어야 합니다.
  - 관리 그룹: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 구독: `/subscriptions/{subscriptionId}`
  - 리소스 그룹: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 리소스: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]{resourceType}/{resourceName}`
- **이름** - 할당의 실제 이름입니다. 이 예제에서는 _audit-vm-manageddisks_가 사용되었습니다.

요청 본문:
- **표시 이름** - 정책 할당에 대한 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다.
- **설명** - 정책에서 수행하는 작업 또는 이 범위에 할당된 이유에 대한 자세한 설명입니다.
- **policyDefinitionId** – 할당을 만드는 데 기준으로 사용되는 정책 정의 ID입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다.

## <a name="identify-non-compliant-resources"></a>비규격 리소스 식별

이 새 할당에서 규정을 준수하지 않는 리소스를 보려면 다음 명령을 실행하여 JSON 파일로 출력되는 비규격 리소스의 리소스 ID를 가져옵니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'&$apply=groupby((ResourceId))"
```

결과는 다음 예제와 유사합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3Id>"
        }

    ]
}
```

결과는 Azure Portal 보기에서 **비준수 리소스** 아래 나열된 것과 비교할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 명령을 사용합니다.

```http
DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/audit-vm-manageddisks?api-version=2019-09-01
```

`{scope}`를 정책 할당을 처음 만들 때 사용한 범위로 바꿉니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)
