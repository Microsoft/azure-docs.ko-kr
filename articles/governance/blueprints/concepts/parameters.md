---
title: 매개 변수를 사용하여 동적 청사진 만들기
description: 정적 및 동적 매개 변수란 무엇이며 동적 청사진을 만드는 데 사용하는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/12/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ac7b662bc9ef4f3ae675c4cbde18e159383d3d8e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767044"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>매개 변수를 통해 동적 청사진 만들기

다양한 아티팩트(예: 리소스 그룹, Resource Manager 템플릿, 정책 또는 역할 할당)를 사용하여 완벽하게 정의된 청사진은 Azure 내에서 개체를 신속하고 일관적으로 만드는 방법을 제공합니다. 이러한 재사용 가능한 디자인 패턴과 컨테이너를 유연하게 사용할 수 있도록, Azure Blueprint는 매개 변수를 지원합니다. 매개 변수는 정의 및 할당 과정에서 청사진을 통해 배포된 아티팩트의 속성을 변경할 수 있는 유연성을 제공합니다.

간단한 예로 리소스 그룹 아티팩트가 있습니다. 리소스 그룹을 만들 때 반드시 제공해야 하는 두 가지 값이 있는데, 그것은 바로 이름과 위치입니다. 청사진에 리소스 그룹을 추가할 때 매개 변수가 없으면 청사진을 사용할 때마다 이름과 위치를 정의해야 합니다. 따라서 이 반복에서는 청사진을 사용할 때마다 동일한 리소스 그룹에 아티팩트를 만들게 됩니다. 이로 인해 해당 리소스 그룹 내부의 리소스가 중복되고 충돌을 일으킵니다.

> [!NOTE]
> 서로 다른 두 청사진에 이름이 같은 리소스 그룹이 포함되더라도 아무 문제가 없습니다.
> 한 청사진에 포함된 리소스 그룹이 이미 있는 경우 청사진이 계속해서 해당 리소스 그룹에 관련 아티팩트를 만듭니다. 이름 및 리소스 종류가 같은 두 리소스가 한 구독 내에 존재할 수 없기 때문에 이로 인해 충돌이 발생할 수 있습니다.

이 문제에 대한 해결 방법은 매개 변수입니다. 청사진을 사용하면 구독에 할당하는 중 아티팩트의 각 속성 값을 정의할 수 있습니다. 매개 변수를 통해 충돌 없이 단일 구독 내에 리소스 그룹과 기타 리소스를 만드는 청사진을 재사용할 수 있습니다.

## <a name="blueprint-parameters"></a>청사진 매개 변수

REST API를 통해 청사진 자체에 매개 변수를 만들 수 있습니다. 이러한 매개 변수는 지원되는 각 아티팩트의 매개 변수와 다릅니다. 청사진에 매개 변수를 만들면 해당 청사진의 아티팩트에서 매개 변수를 사용할 수 있습니다. 리소스 그룹의 이름을 지정하는 접두사를 예로 들 수 있습니다. 아티팩트는 청사진 매개 변수를 사용하여 “대체로 동적인” 매개 변수를 만들 수 있습니다. 할당 중에 매개 변수를 정의할 수도 있으므로 이 패턴은 명명 규칙을 준수할 수 있는 일관성을 허용합니다. 자세한 단계는 [정적 매개 변수 설정 - 청사진 수준 매개 변수](#blueprint-level-parameter)를 참조하세요.

### <a name="using-securestring-and-secureobject-parameters"></a>secureString 및 secureObject 매개 변수 사용

Resource Manager 템플릿 _아티팩트_가 **secureString** 및 **secureObject** 유형의 매개 변수를 지원하지만, Azure Blueprints는 각 매개 변수를 Azure Key Vault와 연결할 것을 요구합니다.
이 보안 조치를 사용하면 비밀을 청사진과 함께 저장하는 안전하지 않은 사례를 방지하고 안전한 패턴을 사용하도록 장려할 수 있습니다. Azure Blueprints는 Resource Manager 템플릿 _아티팩트_에 보안 매개 변수가 포함되었는지 검색하여 이 보안 조치를 지원합니다. 그런 다음, 서비스에서 할당 중에 검색된 보안 매개 변수를 기준으로 다음 Key Vault 속성을 확인하는 메시지를 표시합니다.

- Key Vault 리소스 ID
- Key Vault 비밀 이름
- Key Vault 비밀 버전

Blueprint 할당을 사용 하는 경우는 **관리 시스템 할당 id**, Key Vault 참조 _해야_ blueprint 정의에 할당 된 동일한 구독에 존재 합니다.

Blueprint 할당을 사용 하는 경우는 **사용자 할당 관리 id**, Key Vault 참조 _수 있습니다_ 중앙 집중화 된 구독에 존재 합니다. 관리 되는 id blueprint 할당 하기 전에 Key Vault에 적절 한 권한이 부여 되어야 합니다.

두 경우 모두에 Key Vault가 있어야 합니다 **Azure Resource Manager 템플릿 배포에 대 한 액세스를 사용 하도록 설정** 에서 구성 합니다 **액세스 정책** 페이지입니다. 이 기능을 사용하는 방법에 대한 지침은 [Key Vault - 템플릿 배포 사용](../../../managed-applications/key-vault-access.md#enable-template-deployment)을 참조하세요.

Azure Key Vault에 대한 자세한 내용은 [Key Vault 개요](../../../key-vault/key-vault-overview.md)를 참조하세요.

## <a name="parameter-types"></a>매개 변수 유형

### <a name="static-parameters"></a>정적 매개 변수

청사진 정의에 정의된 매개 변수 값은 청사진을 사용할 때마다 해당 정적 값으로 아티팩트가 배포되기 때문에 **정적 매개 변수**라고 합니다. 리소스 그룹 예제에서, 리소스 그룹의 이름에는 이 내용이 적용되지 않지만 위치에는 적용됩니다. 그리고 청사진을 할당할 때마다 이름이 무엇이 됐든 같은 위치에 리소스 그룹이 생성됩니다. 이러한 유연성 때문에 필수로 정의할 항목과 할당 과정에서 변경 가능한 항목을 선택할 수 있습니다.

#### <a name="setting-static-parameters-in-the-portal"></a>포털에서 정적 매개 변수 설정

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다.

1. 기존 blueprint를 클릭 하 고 클릭 **편집 blueprint** 누르거나 **+ 만들기 blueprint** 에서 정보를 입력 하 고는 **기본 사항** 탭 합니다.

1. **다음: 아티팩트**를 클릭하거나 **아티팩트** 탭을 클릭합니다.

1. 청사진에 추가되고 매개 변수 옵션이 있는 아티팩트는 **매개 변수** 열에 **X/Y 매개 변수 채워짐**으로 표시됩니다. 아티팩트 행을 클릭하여 아티팩트 매개 변수를 편집합니다.

   ![Blueprint 정의에서 blueprint 매개 변수](../media/parameters/parameter-column.png)

1. **아티팩트 편집** 페이지에는 클릭한 아티팩트에 적절한 값 옵션이 표시됩니다. 아티팩트의 각 매개 변수에는 제목, 값 상자 및 확인란이 있습니다. **정적 매개 변수**로 만들려면 확인란을 선택 취소하도록 상자를 설정합니다. 아래 예제에서는 _위치_가 선택 취소되고 _리소스 그룹 이름_이 선택되었기 때문에 위치만이 **정적 매개 변수**입니다.

   ![청사진 아티팩트에 blueprint 정적 매개 변수](../media/parameters/static-parameter.png)

#### <a name="setting-static-parameters-from-rest-api"></a>REST API에서 정적 매개 변수 설정

각 REST API URI에는 사용자가 자신의 값으로 대체해야 하는 변수가 있습니다.

- `{YourMG}` - 사용자의 관리 그룹 이름으로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

##### <a name="blueprint-level-parameter"></a>청사진 수준 매개 변수

REST API를 통해 청사진을 만들 때 [청사진 매개 변수](#blueprint-parameters)를 만들 수 있습니다. 이렇게 하려면 다음 REST API URI 및 본문 형식을 사용합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

청사진 수준 매개 변수가 만들어지면 해당 청사진에 추가된 아티팩트에 사용할 수 있습니다.
다음 REST API 예제에서는 청사진에 역할 할당 아티팩트를 만들고 청사진 수준 매개 변수를 사용합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- 요청 본문

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

이 예제에서는 **principalIds** 속성이 **owners** 청사진 수준 매개 변수를 `[parameters('owners')]` 값으로 사용합니다. 청사진 수준 매개 변수를 사용하는 아티팩트에 매개 변수를 설정하는 것도 **정적 매개 변수**의 예입니다. 청사진 수준 매개 변수는 청사진 할당 중에 설정할 수 없으며 각 할당에서 동일한 값이 됩니다.

##### <a name="artifact-level-parameter"></a>아티팩트 수준 매개 변수

아티팩트에서 **정적 매개 변수**를 만드는 방법도 비슷하지만, `parameters()` 함수 대신 직접 값을 사용합니다. 다음은 두 개의 정적 매개 변수 **tagName** 및 **tagValue**를 만드는 예제입니다. 각 매개 변수의 값은 직접 제공되며 함수 호출을 사용하지 않습니다.

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- 요청 본문

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>동적 매개 변수

정적 매개 변수와 대조되는 것이 **동적 매개 변수**입니다. 이 매개 변수는 청사진에서 정의되는 것이 아니라 청사진을 할당할 때 정의됩니다. 리소스 그룹 예제에서는 **동적 매개 변수**를 리소스 그룹 이름에 사용하는 것이 좋습니다. 청사진의 모든 할당에 다른 이름을 제공합니다. Blueprint 함수의 목록을 보려면 참조는 [blueprint 함수](../reference/blueprint-functions.md) 참조 합니다.

#### <a name="setting-dynamic-parameters-in-the-portal"></a>포털에서 동적 매개 변수 설정

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의**를 선택합니다.

1. 할당하려는 청사진을 마우스 오른쪽 단추로 클릭하고 선택 **할당 blueprint** 또는 청사진에 할당 하려면 클릭 합니다 **할당 blueprint** 단추입니다.

1. 에 **할당 blueprint** 페이지에서 찾을 합니다 **아티팩트 매개 변수** 섹션. **동적 매개 변수**가 하나 이상 있는 아티팩트는 아티팩트 및 구성 옵션을 표시합니다. 매개 변수에 필요한 값을 제공한 후 청사진을 할당합니다. 아래 예제에서 _이름_은 청사진을 완료하려면 정의해야 하는 **동적 매개 변수**입니다.

   ![Blueprint 할당 중에 blueprint 동적 매개 변수](../media/parameters/dynamic-parameter.png)

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API에서 정적 매개 변수 설정

할당 과정에서 **동적 매개 변수**를 설정하는 작업은 값을 직접 입력하는 방식으로 수행됩니다. 와 같은 함수를 사용 하는 대신 [parameters()](../reference/blueprint-functions.md#parameters), 제공 된 값은 적절 한 문자열입니다. 리소스 그룹에 대한 아티팩트는 “템플릿 이름”, **이름** 및 **위치** 속성으로 정의됩니다. 포함된 아티팩트에 대한 그 외의 매개 변수는 **\<이름\>** 및 **값** 키 쌍을 사용하여 **매개 변수** 아래에 정의됩니다. 할당 과정에서 제공하지 않는 동적 매개 변수에 대해 청사진이 구성된 경우 할당이 실패합니다.

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- 요청 본문

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>다음 단계

- 목록은 [함수를 blueprint](../reference/blueprint-functions.md)합니다.
- [청사진 수명 주기](lifecycle.md)에 대해 알아보기
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결