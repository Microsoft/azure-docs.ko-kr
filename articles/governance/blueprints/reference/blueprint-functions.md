---
title: Azure Blueprints 함수
description: Azure Blueprints 정의 및 할당의 청사진 아티팩트와 함께 사용할 수 있는 함수를 설명합니다.
ms.date: 08/27/2020
ms.topic: reference
ms.openlocfilehash: fe984ee7664b0d50fb891d946f9f40a200ccce09
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050763"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Azure Blueprints와 사용할 수 있는 함수

Azure Blueprints는 더욱 동적인 청사진 정의를 만드는 함수를 제공합니다. 이러한 함수는 청사진 정의 및 청사진 아티팩트와 함께 사용됩니다. Azure Resource Manager 템플릿 (ARM 템플릿) 아티팩트는 청사진 매개 변수를 통해 동적 값을 가져오는 것 외에도 리소스 관리자 함수를 모두 사용할 수 있도록 지원 합니다.

지원되는 함수는 다음과 같습니다.

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [subscription](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

청사진 아티팩트 출력과 함께 채워진 속성의 개체를 반환합니다.

> [!NOTE]
> `artifacts()`함수는 ARM 템플릿 내에서 사용할 수 없습니다. Azure PowerShell 또는 REST API를 사용하여 청사진을 [코드형 청사진](https://github.com/Azure/azure-blueprints/blob/master/README.md)의 일부로 관리할 때 청사진 정의 JSON 또는 아티팩트 JSON에서만 함수를 사용할 수 있습니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| artifactName |예 |문자열 |청사진 아티팩트의 이름입니다. |

### <a name="return-value"></a>반환 값

출력 속성의 개체입니다. **outputs** 속성은 참조되는 청사진 아티팩트의 유형에 따라 다릅니다. 모든 유형은 다음 형식을 따릅니다.

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>정책 할당 아티팩트

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM 템플릿 아티팩트

반환 된 개체의 **출력** 속성은 ARM 템플릿 내에 정의 되 고 배포에서 반환 됩니다.

#### <a name="role-assignment-artifact"></a>역할 할당 아티팩트

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>예제

다음 샘플 출력 속성을 포함 하는 ID _mytemplate 아티팩트가_ 있는 ARM 템플릿 아티팩트:

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

_myTemplateArtifact_ 샘플에서의 데이터 검색 예시 중 일부는 다음과 같습니다.

| 식 | Type | 값 |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

여러 문자열 값을 결합하고 연결된 문자열을 반환합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| string1 |예 |문자열 |연결할 첫 번째 값입니다. |
| 추가 인수 |예 |문자열 |연결할 추가 값(순서대로) |

### <a name="return-value"></a>반환 값

연결된 값의 문자열.

### <a name="remarks"></a>설명

Azure Blueprint 함수는 문자열 에서만 작동 한다는 점에서 ARM 템플릿 함수와 다릅니다.

### <a name="example"></a>예제

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>매개 변수

`parameters(parameterName)`

청사진 매개 변수 값을 반환합니다. 지정된 매개 변수 이름은 청사진 정의 또는 청사진 아티팩트에 정의되어야 합니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| parameterName |예 |문자열 |반환할 매개 변수의 이름입니다. |

### <a name="return-value"></a>반환 값

지정된 청사진 또는 청사진 아티팩트 매개 변수의 값입니다.

### <a name="remarks"></a>설명

Azure Blueprint 함수는 청사진 매개 변수 에서만 작동 한다는 점에서 ARM 템플릿 함수와 다릅니다.

### <a name="example"></a>예제

청사진 정의에서 _principalIds_ 매개 변수 정의:

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
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

이후에는 _principalIds_를 청사진 아티팩트의 `parameters()`에 대한 인수로 사용:

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

Azure Blueprint 함수는 ARM 템플릿 함수와 다릅니다. `resourceGroup()` 함수는 구독 수준 아티팩트 또는 청사진 정의에서 사용할 수 없습니다. 리소스 그룹 아티팩트의 일부인 청사진 아티팩트에만 사용할 수 있습니다.

`resourceGroup()` 함수는 일반적으로 리소스 그룹 아티팩트와 동일한 위치에 리소스를 만드는 데 사용됩니다.

### <a name="example"></a>예제

리소스 그룹의 위치를 사용하려면 청사진 정의에서 또는 할당 도중 다른 아티팩트의 위치로 설정하고, 청사진 정의에서 리소스 그룹 자리 표시자 개체를 선언합니다. 이 예제에서는 _NetworkingPlaceholder_가 리소스 그룹 자리 표시자의 이름입니다.

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

그런 다음 리소스 그룹 자리 표시자 개체를 대상으로 하는 청사진 아티팩트의 컨텍스트에서 `resourceGroup()` 함수를 사용합니다. 이 예제에서 템플릿 아티팩트는 _NetworkingPlaceholder_ 리소스 그룹으로 배포되고 _NetworkingPlaceholder_ 리소스 그룹 위치로 동적으로 채워진 매개 변수 _resourceLocation_을 템플릿에 제공합니다. _NetworkingPlaceholder_ 리소스 그룹의 위치가 청사진 정의에서 정적으로 정의되었거나 할당 도중 동적으로 정의되었을 수 있습니다. 두 경우 모두 템플릿 아티팩트에 해당 정보가 매개 변수로 제공되고 이를 활용하여 리소스를 올바른 위치로 배포합니다.

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

지정된 리소스 그룹 아티팩트를 나타내는 개체를 반환합니다. 아티팩트의 컨텍스트를 필요로 하는 `resourceGroup()`과 달리 이 함수는 해당 리소스 그룹의 컨텍스트가 아닌 특정 리소스 그룹 자리 표시자의 속성을 가져오는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

| 매개 변수 | 필수 | Type | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |예 |문자열 |반환할 리소스 그룹 아티팩트의 자리 표시자 이름. |

### <a name="return-value"></a>반환 값

반환된 개체는 다음 형식으로 되어 있습니다.

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>예제

리소스 그룹의 위치를 사용하려면 청사진 정의에서 또는 할당 도중 다른 아티팩트의 위치로 설정하고, 청사진 정의에서 리소스 그룹 자리 표시자 개체를 선언합니다. 이 예제에서는 _NetworkingPlaceholder_가 리소스 그룹 자리 표시자의 이름입니다.

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

그런 다음 모든 청사진 아티팩트의 컨텍스트에서 `resourceGroups()` 함수를 사용하여 리소스 그룹 자리 표시자 개체에 대한 참조를 가져옵니다. 이 예제에서 템플릿 아티팩트는 _NetworkingPlaceholder_ 리소스 그룹 외부로 배포되고 템플릿에 _NetworkingPlaceholder_ 리소스 그룹 위치로 동적으로 채워진 매개 변수 _artifactLocation_을 제공합니다. _NetworkingPlaceholder_ 리소스 그룹의 위치가 청사진 정의에서 정적으로 정의되었거나 할당 도중 동적으로 정의되었을 수 있습니다. 두 경우 모두 템플릿 아티팩트에 해당 정보가 매개 변수로 제공되고 이를 활용하여 리소스를 올바른 위치로 배포합니다.

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

현재 청사진 할당에 대한 구독 관련 세부 정보를 반환합니다.

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

### <a name="example"></a>예제

구독의 표시 이름 및 `concat()` 함수를 사용하여 템플릿 아티팩트에 매개 변수 _resourceName_으로 전달된 명명 규칙을 만듭니다.

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

- [청사진 수명 주기](../concepts/lifecycle.md)에 대해 알아봅니다.
- [정적 및 동적 매개 변수](../concepts/parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](../concepts/sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](../concepts/resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결