---
title: 관리 되는 Id를 사용 하는 관리 되는 앱
description: 기존 리소스에 연결 하 고, Azure 리소스를 관리 하 고, 활동 로그에 운영 id를 제공 하기 위해 관리 되는 Id로 관리 되는 응용 프로그램을 구성
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: dbf75262440474c5cb50a6d733ac7cba212b5f3f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651658"
---
# <a name="azure-managed-application-with-managed-identity"></a>관리 Id를 사용 하는 Azure 관리 되는 응용 프로그램

> [!NOTE]
> 관리 되는 응용 프로그램에 대 한 관리 되는 Id 지원은 현재 미리 보기 상태입니다. 관리 Id를 활용 하려면 2018-09-01-preview api 버전을 사용 하세요.

관리 되는 Id를 포함 하도록 관리 되는 응용 프로그램을 구성 하는 방법에 대해 알아봅니다. 관리 Id를 사용 하 여 고객이 추가 기존 리소스에 대 한 관리 되는 응용 프로그램 액세스 권한을 부여할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. AAD (Azure Active Directory)의 관리 되는 id에 대 한 자세한 내용은 [Azure 리소스에 대 한 관리 되는 id](../../active-directory/managed-identities-azure-resources/overview.md)를 참조 하세요.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID**는 애플리케이션에 연결되어 있어 해당 앱을 삭제하면 이 ID도 삭제됩니다. 앱에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- **사용자 할당 id** 는 앱에 할당할 수 있는 독립 실행형 Azure 리소스입니다. 앱에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="how-to-use-managed-identity"></a>관리 Id를 사용 하는 방법

관리 되는 Id를 사용 하면 관리 되는 응용 프로그램에 대 한 많은 시나리오 해결할 수 있는 몇 가지 일반적인 시나리오는 다음과 같습니다.

- 기존 Azure 리소스에 연결 된 관리 되는 응용 프로그램 배포 [기존 네트워크 인터페이스](../../virtual-network/virtual-network-network-interface-vm.md)에 연결 된 관리 되는 응용 프로그램 내에서 Azure VM (가상 머신)을 배포 하는 경우를 예로 들 수 있습니다.
- 관리 되는 응용 프로그램 및 게시자에 게 **관리 되는 리소스 그룹**외부의 Azure 리소스에 대 한 액세스 권한 부여
- Azure 내에서 활동 로그 및 기타 서비스에 대 한 관리 되는 응용 프로그램의 운영 id를 제공 합니다.

## <a name="adding-managed-identity"></a>관리 Id 추가

관리 되는 Id를 사용 하 여 관리 되는 응용 프로그램을 만들려면 Azure 리소스에서 추가 속성을 설정 해야 합니다. 다음 예에서는 샘플 **id** 속성을 보여 줍니다.

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

**Id**를 사용 하 여 관리 되는 응용 프로그램을 만드는 두 가지 일반적인 방법으로는 [createuidefinition. json](./create-uidefinition-overview.md) 및 [Azure Resource Manager 템플릿이](../templates/template-syntax.md)있습니다. 간단한 단일 만들기 시나리오의 경우에는 보다 풍부한 환경을 제공 하기 때문에 CreateUIDefinition을 사용 하 여 관리 되는 Id를 사용 해야 합니다. 그러나 자동화 된 응용 프로그램이 나 여러 관리 되는 응용 프로그램 배포를 필요로 하는 고급 또는 복합 시스템을 처리할 때 템플릿을 사용할 수 있습니다.

### <a name="using-createuidefinition"></a>CreateUIDefinition 사용

관리 되는 응용 프로그램은 [Createuidefinition. json](./create-uidefinition-overview.md)을 통해 관리 되는 id로 구성할 수 있습니다. [출력 섹션](./create-uidefinition-overview.md#outputs)에서 키 `managedIdentity`를 사용 하 여 관리 되는 응용 프로그램 템플릿의 identity 속성을 재정의할 수 있습니다. 샘플 아래는 관리 되는 응용 프로그램에서 **시스템이 할당** 한 id를 사용 하도록 설정 합니다. 더 복잡 한 id 개체는 소비자에 게 입력을 요청 하는 CreateUIDefinition 요소를 사용 하 여 구성 될 수 있습니다. 이러한 입력은 **사용자 할당 id**를 사용 하 여 관리 되는 응용 프로그램을 구성 하는 데 사용할 수 있습니다.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>관리 Id에 대해 CreateUIDefinition을 사용 하는 경우

관리 되는 응용 프로그램에서 관리 Id를 사용 하도록 설정 하기 위해 CreateUIDefinition을 사용 하는 경우에 대 한 몇 가지 권장 사항은

- 관리 되는 응용 프로그램 생성은 Azure Portal 또는 marketplace를 거칩니다.
- 관리 되는 Id에는 복잡 한 소비자 입력이 필요 합니다.
- 관리 되는 응용 프로그램을 만들려면 관리 되는 Id가 필요 합니다.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned 된 CreateUIDefinition

관리 되는 응용 프로그램에 대해 SystemAssigned id를 사용 하도록 설정 하는 기본 CreateUIDefinition입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
        ],
        "outputs": {
            "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
        }
    }
}
```

#### <a name="userassigned-createuidefinition"></a>UserAssigned 된 CreateUIDefinition

**사용자 할당 id** 리소스를 입력으로 사용 하 고 관리 되는 응용 프로그램에 대해 userassigned 된 id를 사용 하도록 설정 하는 기본 CreateUIDefinition입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "manageIdentity",
                "label": "Identity",
                "subLabel": {
                    "preValidation": "Manage Identities",
                    "postValidation": "Done"
                },
                "bladeTitle": "Identity",
                "elements": [
                    {
                        "name": "userAssignedText",
                        "type": "Microsoft.Common.TextBox",
                        "label": "User assigned managed identity",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('manageIdentity').userAssignedText),':{}}}'))]"
        }
    }
}
```

위의 CreateUIDefinition. json은 소비자가 **사용자에 게 할당 된 id** AZURE 리소스 id를 입력할 수 있는 입력란을 포함 하는 만들기 사용자 환경을 생성 합니다. 생성 된 환경은 다음과 같습니다.

![사용자 할당 id CreateUIDefinition 샘플](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

> [!NOTE]
> Marketplace 관리 응용 프로그램 템플릿은 Azure Portal 만들기 환경을 통해 고객에 게 자동으로 생성 됩니다.
> 이러한 시나리오의 경우 CreateUIDefinition의 `managedIdentity` 출력 키를 사용 하 여 id를 활성화 해야 합니다.

Azure Resource Manager 템플릿을 통해 관리 Id를 사용 하도록 설정할 수도 있습니다. 샘플 아래는 관리 되는 응용 프로그램에서 **시스템이 할당** 한 id를 사용 하도록 설정 합니다. 더 복잡 한 id 개체는 Azure Resource Manager 템플릿 매개 변수를 사용 하 여 입력을 제공 하 여 구성 될 수 있습니다. 이러한 입력은 **사용자 할당 id**를 사용 하 여 관리 되는 응용 프로그램을 구성 하는 데 사용할 수 있습니다.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>관리 Id에 Azure Resource Manager 템플릿을 사용 해야 하는 경우

다음은 관리 되는 응용 프로그램에서 관리 Id를 사용 하도록 설정 하기 위해 Azure Resource Manager 템플릿을 사용 해야 하는 경우에 대 한 권장 사항

- 템플릿을 기반으로 관리 되는 응용 프로그램을 프로그래밍 방식으로 배포할 수 있습니다.
- 관리 되는 응용 프로그램을 프로 비전 하려면 관리 되는 Id에 대 한 사용자 지정 역할 할당이 필요 합니다.
- 관리 되는 응용 프로그램에는 Azure Portal 및 마켓플레이스 생성 흐름이 필요 하지 않습니다.

#### <a name="systemassigned-template"></a>SystemAssigned 된 템플릿

**시스템이 할당** 한 id를 사용 하 여 관리 되는 응용 프로그램을 배포 하는 기본 Azure Resource Manager 템플릿입니다.

```json
"resources": [
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned"
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

### <a name="userassigned-template"></a>UserAssigned 된 템플릿

**사용자 할당 id**를 사용 하 여 관리 되는 응용 프로그램을 배포 하는 기본 Azure Resource Manager 템플릿입니다.

```json
"resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('managedIdentityName')]",
      "apiVersion": "2018-11-30",
      "location": "[parameters('location')]"
    },
    {
        "type": "Microsoft.Solutions/applications",
        "name": "[parameters('applicationName')]",
        "apiVersion": "2018-09-01-preview",
        "location": "[parameters('location')]",
        "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',parameters('managedIdentityName'))]": {}
            }
        },
        "properties": {
            "ManagedResourceGroupId": "[parameters('managedByResourceGroupId')]",
            "parameters": { }
        }
    }
]
```

## <a name="granting-access-to-azure-resources"></a>Azure 리소스에 대 한 액세스 권한 부여

관리 되는 응용 프로그램에 id가 부여 되 면 기존 azure 리소스에 대 한 액세스 권한을 부여할 수 있습니다. 이 프로세스는 Azure Portal의 액세스 제어 (IAM) 인터페이스를 통해 수행할 수 있습니다. 관리 되는 응용 프로그램 또는 **사용자 할당 id** 의 이름을 검색 하 여 역할 할당을 추가할 수 있습니다.

![관리 되는 응용 프로그램에 대 한 역할 할당 추가](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>기존 Azure 리소스 연결

> [!NOTE]
> 관리 되는 응용 프로그램을 배포 하기 전에 **사용자 할당 id** 를 [구성](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 해야 합니다. 또한 관리 되는 응용 프로그램의 연결 된 리소스 배포는 **marketplace** 종류에 대해서만 지원 됩니다.

관리 Id를 사용 하 여 배포 중에 기존 리소스에 액세스 해야 하는 관리 되는 응용 프로그램을 배포할 수도 있습니다. 관리 되는 응용 프로그램을 고객이 프로 비전 할 때 **사용자 할당 id** 를 추가 하 여 **maintemplate.json** 배포에 대 한 추가 권한 부여를 제공할 수 있습니다.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>링크 된 리소스를 사용 하 여 CreateUIDefinition 작성

관리 되는 응용 프로그램의 배포를 기존 리소스에 연결할 때 기존 Azure 리소스 및 해당 리소스에 대 한 해당 역할 할당을 가진 **사용자 할당 id** 를 모두 제공 해야 합니다.

 네트워크 인터페이스 리소스 ID와 사용자 할당 id 리소스 id의 두 입력이 필요한 샘플 CreateUIDefinition입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
    "version": "0.1.2-preview",
    "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "managedApplicationSetting",
                "label": "Managed Application Settings",
                "subLabel": {
                    "preValidation": "Managed Application Settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Managed Application Settings",
                "elements": [
                    {
                        "name": "networkInterfaceId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "network interface resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Network/networkInterfaces/existingnetworkinterface",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.Network/networkInterfaces/networkinterface1",
                        "visible": true
                    },
                    {
                        "name": "userAssignedId",
                        "type": "Microsoft.Common.TextBox",
                        "label": "user assigned identity resource id",
                        "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity",
                        "toolTip": "Must represent the identity as an Azure Resource Manager resource identifer format ex. /subscriptions/sub1/resourcegroups/myGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity1",
                        "visible": true
                    }
                ]
            }
        ],
        "outputs": {
            "existingNetworkInterfaceId": "[steps('managedApplicationSetting').networkInterfaceId]",
            "managedIdentity": "[parse(concat('{\"Type\":\"UserAssigned\",\"UserAssignedIdentities\":{',string(steps('managedApplicationSetting').userAssignedId),':{}}}'))]"
        }
    }
}
```

이 CreateUIDefinition은 두 필드를 포함 하는 만들기 사용자 환경을 생성 합니다. 사용자는 첫 번째 필드를 사용 하 여 관리 되는 응용 프로그램 배포에 연결 되는 리소스에 대 한 Azure 리소스 ID를 입력할 수 있습니다. 두 번째는 소비자가 연결 된 Azure 리소스에 대 한 액세스 권한이 있는 **사용자 할당 id** AZURE 리소스 id를 입력 하는 것입니다. 생성 된 환경은 다음과 같습니다.

![두 입력이 포함 된 샘플 CreateUIDefinition: 네트워크 인터페이스 리소스 ID 및 사용자 할당 id 리소스 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>링크 된 리소스를 사용 하 여 Maintemplate.json 작성

CreateUIDefinition을 업데이트 하는 것 외에도 기본 템플릿은 전달 된 연결 된 리소스 ID를 수락 하도록 업데이트 해야 합니다. 새 매개 변수를 추가 하 여 새 출력을 허용 하도록 주 템플릿을 업데이트할 수 있습니다. `managedIdentity` 출력은 생성 된 관리 되는 응용 프로그램 템플릿의 값을 재정의 하므로 주 템플릿에 전달 되지 않으므로 매개 변수 섹션에 포함 되어서는 안 됩니다.

CreateUIDefinition에서 제공 하는 기존 네트워크 인터페이스로 네트워크 프로필을 설정 하는 샘플 기본 템플릿입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "existingNetworkInterfaceId": { "type": "string" }
    },
    "variables": {
    },
    "resources": [
        {
            "apiVersion": "2016-04-30-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "myLinkedResourceVM",
            "location": "[resourceGroup().location]",
            "properties": {
                …,
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[parameters('existingNetworkInterfaceId')]"
                        }
                    ]
                }
            }
        }
    ]
}
```

### <a name="consuming-the-managed-application-with-a-linked-resource"></a>연결 된 리소스를 사용 하 여 관리 되는 응용 프로그램 사용

관리 되는 응용 프로그램 패키지를 만든 후에는 Azure Portal를 통해 관리 되는 응용 프로그램을 사용할 수 있습니다. 이를 사용 하려면 몇 가지 필수 구성 요소 단계가 있습니다.

- 필요한 연결 된 Azure 리소스의 인스턴스를 만들어야 합니다.
- **사용자 할당 id** 를 만들고 연결 된 리소스에 대 한 [역할 할당을 지정](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 해야 합니다.
- 기존의 연결 된 리소스 ID 및 **사용자 할당 id** Id는 CreateUIDefinition에 제공 됩니다.

## <a name="accessing-the-managed-identity-token"></a>관리 Id 토큰 액세스

이제 게시자 테넌트에서 `listTokens` api를 통해 관리 되는 응용 프로그램의 토큰에 액세스할 수 있습니다. 예제 요청은 다음과 같습니다.

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1

{
    "authorizationAudience": "https://management.azure.com/",
    "userAssignedIdentities": [
        "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userAssignedIdentityName}"
    ]
}
```

요청 본문 매개 변수:

매개 변수 | 필수 | Description
---|---|---
authorizationAudience | *아니요* | 대상 리소스의 앱 ID URI입니다. 발급 된 토큰의 `aud` (대상) 클레임 이기도 합니다. 기본값은 "https://management.azure.com/"입니다.
userAssignedIdentities | *아니요* | 토큰을 검색할 사용자 할당 관리 id 목록입니다. 지정 하지 않으면 `listTokens`는 시스템 할당 관리 id에 대 한 토큰을 반환 합니다.


샘플 응답은 다음과 같습니다.

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json

{
    "value": [
        {
            "access_token": "eyJ0eXAi…",
            "expires_in": "2…",
            "expires_on": "1557…",
            "not_before": "1557…",
            "authorizationAudience": "https://management.azure.com/",
            "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}",
            "token_type": "Bearer"
        }
    ]
}
```

응답은 `value` 속성 아래에 토큰 배열을 포함 합니다.

매개 변수 | Description
---|---
access_token | 요청된 액세스 토큰입니다.
expires_in | 액세스 토큰이 유효한 시간 (초)입니다.
expires_on | 액세스 토큰이 만료되는 시간 범위입니다. Epoch의 초 수로 표시 됩니다.
not_before | 액세스 토큰이 적용 되는 timespan입니다. Epoch의 초 수로 표시 됩니다.
authorizationAudience | 액세스 토큰이 요청 된 `aud` (대상 그룹)입니다. 이는 `listTokens` 요청에서 제공 된 것과 동일 합니다.
resourceId | 발급 된 토큰에 대 한 Azure 리소스 ID입니다. 관리 되는 응용 프로그램 ID 또는 사용자 할당 id ID입니다.
token_type | 토큰 형식입니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 공급자를 사용 하 여 관리 되는 응용 프로그램을 구성 하는 방법](../custom-providers/overview.md)
