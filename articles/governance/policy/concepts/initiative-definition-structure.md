---
title: 이니셔티브 정의 구조의 세부 정보
description: 정책 이니셔티브 정의를 사용 하 여 조직의 Azure 리소스에 배포 하기 위한 정책 정의를 그룹화 하는 방법을 설명 합니다.
ms.date: 08/17/2020
ms.topic: conceptual
ms.openlocfilehash: d7b4adf15193e2cd1b9e516a04c7c989dc442ee9
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048502"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure Policy 이니셔티브 정의 구조

그룹을 단일 항목으로 작업할 수 있기 때문에 이니셔티브를 사용하면 여러 관련 정책 정의를 그룹화할 수 있어 할당 및 관리를 간소화합니다. 예를 들어 관련 태그 지정 정책 정의를 단일 이니셔티브로 그룹화할 수 있습니다. 각 정책을 개별적으로 할당하는 대신 이니셔티브를 적용합니다.

JSON을 사용 하 여 정책 이니셔티브 정의를 만듭니다. 정책 이니셔티브 정의에는 다음에 대 한 요소가 포함 됩니다.

- 표시 이름
- description
- metadata
- 매개 변수
- 정책 정의
- 정책 그룹 (이 속성은 [규정 준수 (미리 보기) 기능의](./regulatory-compliance.md)일부)

다음 예제에서는 두 태그 `costCenter`과 `productName`를 처리하기 위한 이니셔티브를 만드는 방법을 보여 줍니다. 기본 태그 값을 적용하려면 두 가지 기본 제공 정책을 사용합니다.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy 기본 제공 및 패턴은 [Azure Policy 샘플](../samples/index.md)에 있습니다.

## <a name="metadata"></a>메타데이터

선택적 `metadata` 속성은 정책 이니셔티브 정의에 대 한 정보를 저장 합니다.
고객은에서 조직에 유용한 모든 속성 및 값을 정의할 수 있습니다 `metadata` . 그러나 Azure Policy 및 기본 제공에서 사용 하는 몇 가지 _공통_ 속성이 있습니다.

### <a name="common-metadata-properties"></a>공통 메타 데이터 속성

- `version` (string): 정책 이니셔티브 정의의 콘텐츠 버전에 대 한 세부 정보를 추적 합니다.
- `category` (string): Azure Portal 정책 정의가 표시 되는 범주를 결정 합니다.

  > [!NOTE]
  > [규정 준수](./regulatory-compliance.md) 이니셔티브의 경우는 `category` **규정 준수**여야 합니다.

- `preview` (부울): 정책 이니셔티브 정의가 _미리 보기_인 경우 True 또는 false 플래그입니다.
- `deprecated` (부울): 정책 이니셔티브 정의가 _사용 되지 않는_것으로 표시 된 경우에 대 한 True 또는 false 플래그입니다.

> [!NOTE]
> Azure Policy 서비스는 `version`, `preview` 및 `deprecated` 속성을 사용하여 기본 제공 정책 정의 또는 이니셔티브 및 상태에 대한 변경 수준 전달합니다. `version`의 형식은 `{Major}.{Minor}.{Patch}`입니다. _deprecated_ 또는 _preview_와 같은 특정 상태가 `version` 속성에 추가되거나 다른 속성에 **부울**로 추가됩니다. 버전 Azure Policy 기본 제공 되는 방법에 대 한 자세한 내용은 [기본 제공 버전 관리](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)를 참조 하세요.

## <a name="parameters"></a>매개 변수

매개 변수는 정책 정의의 수를 줄여 정책 관리를 간소화하는 데 도움이 됩니다. 양식의 필드 `name`, `address`, `city`, `state`와 같은 매개 변수에 관해 생각해 봅니다. 이러한 매개 변수는 항상 그대로 유지되지만, 그 값은 양식을 작성하는 개별 값에 기초하여 달라집니다.
매개 변수는 정책 이니셔티브를 구축할 때와 동일한 방식으로 작동 합니다. 정책 이니셔티브 정의에 매개 변수를 포함 하 여 포함 된 정책에서 해당 매개 변수를 다시 사용할 수 있습니다.

> [!NOTE]
> 이니셔티브를 할당 한 후에는 initative 수준 매개 변수를 변경할 수 없습니다. 따라서 매개 변수를 정의할 때 **defaultValue**를 설정하는 것이 좋습니다.

### <a name="parameter-properties"></a>매개 변수 속성

매개 변수에는 정책 이니셔티브 정의에 사용 되는 다음과 같은 속성이 있습니다.

- `name`: 매개 변수의 이름입니다. 정책 규칙 내의 `parameters` 배포 함수에서 사용됩니다. 자세한 내용은 [매개 변수 값 사용](#passing-a-parameter-value-to-a-policy-definition)을 참조하세요.
- `type`: 매개 변수가 **문자열**, **배열**, **개체**, **부울**, **정수**, **float**, **datetime** 중에 무엇인지 확인합니다.
- `metadata`: Azure Portal에서 사용자에게 친숙한 정보를 표시하는 데 주로 사용되는 하위 속성을 정의합니다.
  - `description`: 매개 변수의 용도에 대한 설명입니다. 허용 가능한 값의 예를 제공하는 데 사용할 수 있습니다.
  - `displayName`: 매개 변수에 대해 포털에 표시되는 이름입니다.
  - `strongType`: (선택 사항) 포털을 통해 정책 정의를 할당할 때 사용됩니다. 컨텍스트 인식 목록을 제공합니다. 자세한 내용은 [strongType](#strongtype)을 참조하세요.
- `defaultValue`: (선택 사항) 값이 지정되지 않은 경우 할당에서 매개 변수의 값을 설정합니다.
- `allowedValues`: (선택 사항) 할당 중에 매개 변수가 허용하는 값의 배열을 제공합니다.

예를 들어 정책 이니셔티브 정의를 정의 하 여 포함 된 다양 한 정책 정의의 리소스 위치를 제한할 수 있습니다. 해당 정책 이니셔티브 정의에 대 한 매개 변수는 **Allowedlocations**일 수 있습니다. 그런 다음 매개 변수는 포함 된 각 정책 정의에서 사용할 수 있으며 정책 이니셔티브를 할당 하는 동안 정의 됩니다.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>정책 정의에 매개 변수 값 전달

이니셔티브 정의의 [policydefinitions](#policy-definitions) 배열에 포함 된 정책 정의에 전달 하는 이니셔티브 매개 변수를 선언 합니다. 매개 변수 이름은 동일할 수 있지만 정책 정의에 있는 것과 다른 이름을 사용 하 여 코드 가독성을 간소화 합니다.

예를 들어 앞에서 정의한 **init_allowedLocations** 이니셔티브 매개 변수는 다음과 같이 몇 가지 포함 된 정책 정의 및 해당 매개 변수에 전달 될 수 있습니다. **sql_locations** 및 **vm_locations**.

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

이 샘플은 [매개 변수 속성](#parameter-properties)에서 보여 준 **init_allowedLocations** 매개 변수를 참조 합니다.

### <a name="strongtype"></a>strongType

`metadata` 속성 안에 **strongType**을 사용하여 Azure Portal 내에서 다중 선택 옵션 목록을 제공할 수 있습니다. **strongType**은 지원되는 리소스 유형이거나 허용되는 값일 수 있습니다. 리소스 유형이 **strongType**에 유효한지 확인하려면 [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider)를 사용합니다.

Get-AzResourceProvider에서 반환하지 않는 일부 리소스 유형이 지원됩니다. 이러한 리소스 종류는 다음과 같습니다.

- `Microsoft.RecoveryServices/vaults/backupPolicies`

**StrongType** 에 대해 허용 되지 않는 리소스 유형 값은 다음과 같습니다.

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>정책 정의

`policyDefinitions`이니셔티브 정의의 부분은 기존 정책 정의가 이니셔티브에 포함 된 _배열_ 입니다. [매개 변수 값을 정책 정의에 전달](#passing-a-parameter-value-to-a-policy-definition)하는 방법에 설명 된 대로이 속성은 [이니셔티브 매개 변수가](#parameters) 정책 정의에 전달 되는 위치입니다.

### <a name="policy-definition-properties"></a>정책 정의 속성

정책 정의를 나타내는 각 _배열_ 요소에는 다음과 같은 속성이 있습니다.

- `policyDefinitionId` (string): 포함할 사용자 지정 또는 기본 제공 정책 정의의 ID입니다.
- `policyDefinitionReferenceId` (string): 포함 된 정책 정의의 짧은 이름입니다.
- `parameters`: (선택 사항) 포함 된 정책 정의에 이니셔티브 매개 변수를 해당 정책 정의의 속성으로 전달 하는 이름/값 쌍입니다. 자세한 내용은 [매개 변수](#parameters)를 참조하세요.
- `groupNames` (문자열 배열): (선택 사항) 정책 정의가 멤버로 속해 있는 그룹입니다. 자세한 내용은 [정책 그룹](#policy-definition-groups)을 참조 하세요.

다음은 `policyDefinitions` 각각 동일한 이니셔티브 매개 변수로 전달 되는 두 개의 정책 정의가 포함 된의 예입니다.

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definitions-groups-preview"></a><a name="policy-definition-groups"></a>정책 정의 그룹 (미리 보기)

Azure Policy의 [규정 준수](./regulatory-compliance.md) (미리 보기) 기능의 일부로 이니셔티브 정의의 정책 정의를 그룹화 할 수 있습니다. 이 정보는 `policyDefinitionGroups` _배열_ 속성에서 정의 됩니다. 이러한 그룹에는 정책 정의에서 제공 하는 **제어** 및 **규정 준수 도메인과** 같은 추가 정보가 포함 됩니다.
Microsoft에서 만든 **Policymetadata** 개체에서 추가 그룹화 세부 정보를 찾을 수 있습니다. 자세한 내용은 [메타 데이터 개체](#metadata-objects)를 참조 하세요.

### <a name="policy-definition-groups-parameters"></a>정책 정의 그룹 매개 변수

의 각 _배열_ 요소에 `policyDefinitionGroups` 는 다음 속성이 모두 있어야 합니다.

- `name` (문자열) \[ 필수 \] : **컨트롤**의 약식 이름입니다. 이 속성의 값은에서 사용 됩니다 `groupNames` `policyDefinitions` .
- `category` (string): 컨트롤의 **규정 준수 도메인** 입니다.
- `displayName` (string): **컨트롤**의 이름입니다. 포털에서 사용 됩니다.
- `description` (string): **컨트롤** 의 용도에 대 한 설명입니다.
- `additionalMetadataId`(string): **제어** 및 **준수 도메인**에 대 한 추가 정보를 포함 하는 [policymetadata](#metadata-objects) 개체의 위치입니다.

  > [!NOTE]
  > 고객은 기존 [Policymetadata](#metadata-objects) 개체를 가리킬 수 있습니다. 그러나 이러한 개체는 _읽기 전용_ 이며 Microsoft 에서만 생성 됩니다.

`policyDefinitionGroups`NIST 기본 제공 이니셔티브 정의의 속성 예는 다음과 같습니다.

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>메타데이터 개체

Microsoft에서 만든 규정 준수 기본 기능에는 각 컨트롤에 대 한 추가 정보가 있습니다.
이 정보는 다음과 같습니다.

- 규정 준수 이니셔티브에 대 한 **컨트롤** 개요의 Azure Portal에 표시 됩니다.
- REST API를 통해 사용할 수 있습니다. `Microsoft.PolicyInsights`리소스 공급자 및 [policymetadata 작업 그룹](/rest/api/policy-insights/policymetadata/getresource)을 참조 하세요.
- Azure CLI를 통해 사용할 수 있습니다. [Az policy metadata](/cli/azure/policy/metadata) 명령을 참조 하세요.

> [!IMPORTANT]
> 규정 준수에 대 한 메타 데이터 개체는 _읽기 전용_ 이며 고객은 만들 수 없습니다.

정책 그룹화에 대 한 메타 데이터의 노드에는 다음 정보가 포함 됩니다 `properties` .

- `metadataId`: 그룹화가 관련 된 **컨트롤 ID** 입니다.
- `category`(필수): **컨트롤이** 속한 **준수 도메인** 입니다.
- `title` (필수): **컨트롤 ID**의 이름입니다.
- `owner` (필수): Azure에서 컨트롤을 담당 하는 사용자를 식별 합니다. _Customer_, _Microsoft_, _Shared_.
- `description`: 컨트롤에 대 한 추가 정보입니다.
- `requirements`: 컨트롤의 구현 책임에 대해 자세히 설명 합니다.
- `additionalContentUrl`: 컨트롤에 대 한 자세한 정보에 대 한 링크입니다. 이 속성은 일반적으로 규정 준수 표준에서이 컨트롤을 다루는 설명서의 섹션에 대 한 링크입니다.

다음은 **Policymetadata** 개체의 예입니다. 이 예제 메타 데이터는 _NIST SP 800-53 4, AC-1_ 컨트롤에 속합니다.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>다음 단계

- [정의 구조](./definition-structure.md) 참조
- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [정책 효과 이해](effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 만드는](../how-to/programmatically-create.md) 방법을 이해합니다.
- [규정 준수 데이터를 가져오는](../how-to/get-compliance-data.md) 방법을 알아봅니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
