---
title: Azure 청사진 함수
description: Azure Blueprint 정 및 할당에 사용할 함수를 설명합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 0de3e0add804290cdfe27e2e97d8b1a0f240e0a6
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63769303"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure Blueprint를 사용 하 여 사용에 대 한 함수

Azure Blueprint는 보다 동적인 blueprint 정의 만드는 함수를 제공 합니다. 이러한 함수에 대 한 blueprint 정의 사용 하 여 사용 되며 청사진 아티팩트. Resource Manager 템플릿을 아티팩트 blueprint 매개 변수를 통해 동적 값을 시작 하는 것 외에도 Resource Manager 함수의 전체 사용을 지원 합니다.

다음 함수가 지원 됩니다.

- [artifacts](#artifacts)
- [concat](#concat)
- [매개 변수](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>아티팩트

`artifacts(artifactName)`

속성의 개체 채워진 해당 청사진 아티팩트를 사용 하 여 출력을 반환 합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| artifactName |예 |문자열 |청사진 아티팩트 이름입니다. |

### <a name="return-value"></a>반환 값

개체 출력 속성입니다. 출력 속성은 참조 되는 청사진 아티팩트 유형에 따라 달라 집니다. 모든 형식 형식을 따릅니다.

```json
{
  "output": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>정책 할당 아티팩트

```json
{
    "output": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Resource Manager 템플릿 아티팩트

합니다 **출력** 반환된 된 개체의 속성 Resource Manager 템플릿 내에서 정의 되 고 배포 하 여 반환 합니다.

#### <a name="role-assignment-artifact"></a>역할 할당 아티팩트

```json
{
    "output": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>예

ID 사용 하 여 Resource Manager 템플릿 아티팩트 _myTemplateArtifact_ 다음 예제가 포함 된 출력 속성:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

데이터를 검색 하는 몇 가지 예는 _myTemplateArtifact_ 있는 샘플:

| 식 | Type | 값 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").output.myArray]` | 배열 | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").output.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").output.myString]` | String | "내 문자열 값" |
|`[artifacts("myTemplateArtifact").output.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").output.myObject.myProperty]` | String | "value" |
|`[artifacts("myTemplateArtifact").output.myObject.anotherProperty]` | Bool | True  |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

여러 문자열 값을 결합하고 연결된 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| string1 |예 |문자열 |연결할 첫 번째 값입니다. |
| 추가 인수 |아니요 |문자열 |연결 순서로 나타낸 추가 값 |

### <a name="return-value"></a>반환 값

문자열 연결 된 값입니다.

### <a name="remarks"></a>설명

Azure Blueprint는 사용할 수 있으므로 문자열을 사용 하 여 Azure Resource Manager 템플릿 함수에서 서로 다릅니다.

### <a name="example"></a>예

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>매개 변수

`parameters(parameterName)`

Blueprint 매개 변수 값을 반환합니다. 지정된 된 이름은 가진 청사진 아티팩트 또는 blueprint 정의에서 정의 되어야 합니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| parameterName |예 |문자열 |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정 된 blueprint 또는 청사진 아티팩트 매개 변수의 값입니다.

### <a name="remarks"></a>설명

Azure Blueprint는 사용할 수 있으므로 blueprint 매개 변수를 사용 하 여 Azure Resource Manager 템플릿 함수에서 서로 다릅니다.

### <a name="example"></a>예

매개 변수를 정의할 _principalIds_ blueprint 정의에서:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

사용 하 여 _principalIds_ 에 대 한 인수로 `parameters()` 청사진 아티팩트에:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

현재 리소스 그룹을 나타내는 개체를 반환합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>설명

Azure Blueprint는 Azure Resource Manager 템플릿 함수에서 서로 다릅니다. `resourceGroup()` 구독 수준 아티팩트 또는 blueprint 정의에서 함수를 사용할 수 없습니다. 이 리소스 그룹 아티팩트를 포함 된 청사진 아티팩트 사용할 수 있습니다.

일반적인 용도 `resourceGroup()` 를 동일한 리소스 그룹 아티팩트 위치에서 리소스를 만드는 것입니다.

### <a name="example"></a>예

리소스 그룹의 위치를 사용 하려면 blueprint 정의 설정 또는 다른 아티팩트에 대 한 위치와 할당 중 blueprint 정의에 리소스 그룹 자리 표시자 개체를 선언 합니다. 이 예에서 _NetworkingPlaceholder_ 리소스 그룹 자리 표시자의 이름입니다.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

사용 하 여는 `resourceGroup()` 리소스 그룹 자리 표시자 개체를 대상으로 하는 청사진 아티팩트의 컨텍스트에서 함수입니다. 이 예제에서는 템플릿 아티팩트에 배포 되는 _NetworkingPlaceholder_ 리소스 그룹 매개 변수를 제공 하 고 _resourceLocation_ 동적으로 채울는  _NetworkingPlaceholder_ 템플릿에 리소스 그룹 위치입니다. 위치를 _NetworkingPlaceholder_ 리소스 그룹 blueprint 정의에 정의 된 정적 또는 동적으로 할당 하는 동안 정의 된 수 없습니다. 두 경우 모두 템플릿 아티팩트 매개 변수로 해당 정보를 제공 하는 및의 올바른 위치에 리소스를 배포 하는 데 사용 합니다.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

지정 된 리소스 그룹 아티팩트를 나타내는 개체를 반환 합니다. 와 달리 `resourceGroup()`아티팩트의 컨텍스트를 요구 하는,이 함수는 해당 리소스 그룹의 상황에 맞는에 없는 경우 특정 리소스 그룹 자리 표시자의 속성을 가져오는 데 사용 됩니다.

### <a name="parameters"></a>매개 변수

|  매개 변수를 포함해야 합니다. | 필수 | 형식 | 설명 |
|:--- |:--- |:--- |:--- |
| placeholderName |예 |문자열 |자리 표시자 이름 반환할 리소스 그룹 아티팩트입니다. |

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>예

리소스 그룹의 위치를 사용 하려면 blueprint 정의 설정 또는 다른 아티팩트에 대 한 위치와 할당 중 blueprint 정의에 리소스 그룹 자리 표시자 개체를 선언 합니다. 이 예에서 _NetworkingPlaceholder_ 리소스 그룹 자리 표시자의 이름입니다.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

사용 하 여는 `resourceGroups()` 리소스 그룹 자리 표시자 개체에 대 한 참조를 가져오려면 모든 청사진 아티팩트의 컨텍스트에서 함수입니다. 이 예제에서는 서식 파일 아티팩트는 외부로 배포 되는 _NetworkingPlaceholder_ 리소스 그룹 매개 변수를 제공 하 고 _artifactLocation_ 동적으로 채울는  _NetworkingPlaceholder_ 템플릿에 리소스 그룹 위치입니다. 위치를 _NetworkingPlaceholder_ 리소스 그룹 blueprint 정의에 정의 된 정적 또는 동적으로 할당 하는 동안 정의 된 수 없습니다. 두 경우 모두 템플릿 아티팩트 매개 변수로 해당 정보를 제공 하는 및의 올바른 위치에 리소스를 배포 하는 데 사용 합니다.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

현재 blueprint 할당에 대 한 구독에 대 한 세부 정보를 반환 합니다.

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>예

구독의 표시 이름을 사용 하며 `concat()` 함수를 매개 변수로 전달 하는 명명 규칙을 만드는 _resourceName_ 템플릿 아티팩트에.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>다음 단계

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아보기
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결