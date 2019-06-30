---
title: 관리 되는 Id 사용 하 여 azure 관리 되는 응용 프로그램
description: 관리 Id를 사용 하 여 관리 되는 응용 프로그램을 구성 하는 방법에 알아봅니다. 관리 되는 리소스 그룹의 외부에 Azure 리소스 활동 로그에 대 한 관리 되는 응용 프로그램의 작업 id를 제공 하는 응용 프로그램 관리 권한을 부여할 관리 Id를 사용 하 여 기존 리소스에 연결 된 관리 되는 응용 프로그램을 배포할 수 있습니다 및 Azure 내에서 다른 서비스입니다.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.reviewer: ''
ms.author: jobreen
author: jjbfour
ms.date: 05/13/2019
ms.openlocfilehash: be141e208016784b689262394798012c2212ba5b
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312227"
---
# <a name="azure-managed-application-with-managed-identity"></a>관리 되는 Id 사용 하 여 azure 관리 되는 응용 프로그램

> [!NOTE]
> 관리 되는 Identity Managed Applications에 대 한 지원은 현재 미리 보기로 제공에서 됩니다. 관리 Id를 활용 하려면 2018-09-01-미리 보기 api 버전을 사용 하십시오.

관리 Id를 포함 하는 관리 되는 응용 프로그램을 구성 하는 방법에 알아봅니다. 고객 추가 기존 리소스를 관리 되는 응용 프로그램 액세스 권한을 부여할 수 있도록 관리 Id는 사용할 수 있습니다. ID는 Azure 플랫폼에서 관리하며 비밀을 프로비전하거나 회전할 필요가 없습니다. 관리 되는 id에 Azure Active Directory (AAD)에 대 한 자세한 내용은 참조 하세요 [Azure 리소스에 대 한 id 관리](../active-directory/managed-identities-azure-resources/overview.md)합니다.

애플리케이션에 두 가지 형식의 ID를 부여할 수 있습니다.

- **시스템 할당 ID**는 애플리케이션에 연결되어 있어 해당 앱을 삭제하면 이 ID도 삭제됩니다. 앱에는 하나의 시스템 할당 ID만 있을 수 있습니다.
- A **사용자 할당 id** 은 독립 실행형 앱에 할당할 수 있는 Azure 리소스입니다. 앱에는 여러 사용자 할당 ID가 있을 수 있습니다.

## <a name="how-to-use-managed-identity"></a>관리 Id를 사용 하는 방법

관리 Id 관리 되는 응용 프로그램에 대 한 많은 시나리오를 지원합니다. 해결할 수 있는 몇 가지 일반적인 시나리오는:

- 기존 Azure 리소스에 연결 된 관리 되는 응용 프로그램을 배포 합니다. 예제는 Azure 가상 머신 (VM)를 배포 하는 연결 된 관리 되는 응용 프로그램 내에서 프로그램 [기존 네트워크 인터페이스](../virtual-network/virtual-network-network-interface-vm.md)합니다.
- 외부 Azure 리소스에 대 한 관리 되는 응용 프로그램 및 게시자 액세스 권한을 부여 합니다 **관리 되는 리소스 그룹**합니다.
- 활동 로그 및 Azure 내에서 다른 서비스에 대 한 관리 되는 응용 프로그램의 작업 id를 제공합니다.

## <a name="adding-managed-identity"></a>관리 되는 Id를 추가합니다.

관리 Id를 사용 하 여 관리 되는 응용 프로그램을 만드는 Azure 리소스에 대해 설정할 추가 속성이 필요 합니다. 다음 예에서는 예제를 보여 줍니다 **identity** 속성:

```json
{
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.ManagedIdentity/userassignedidentites/myuserassignedidentity": {}
    }
}
```

두 가지 일반적인 사용 하 여 관리 되는 응용 프로그램을 만드는 **identity**: [CreateUIDefinition.json](./create-uidefinition-overview.md) 하 고 [Azure Resource Manager 템플릿](../azure-resource-manager/resource-group-authoring-templates.md)합니다. 단순한 단일에 대 한 시나리오를 만들 CreateUIDefinition 보다 풍부한 환경을 제공 하기 때문에 관리 되는 Id를 사용 하도록 설정 하려면 사용 해야 합니다. 그러나 고급 또는 복잡 한 처리할 때 필요한 시스템을 자동화 또는 템플릿에 여러 관리 되는 응용 프로그램 배포를 사용할 수 있습니다.

### <a name="using-createuidefinition"></a>CreateUIDefinition을 사용 하 여

관리 되는 응용 프로그램을 통해 관리 되는 Id를 사용 하 여 구성할 수 있습니다 합니다 [CreateUIDefinition.json](./create-uidefinition-overview.md)합니다. 에 [출력 섹션](./create-uidefinition-overview.md#outputs), 키 `managedIdentity` Managed Application 템플릿의 identity 속성을 재정의 하려면 사용할 수 있습니다. 샘플 아래 사용 하도록 설정 됩니다 **시스템 할당** 관리 되는 응용 프로그램에서 id입니다. 입력에 대 한 소비자에 게 CreateUIDefinition 요소를 사용 하 여 더 복잡 한 identity 개체를 구성할 수 있습니다. 이러한 입력 사용 하 여 관리 되는 응용 프로그램을 만드는 데 사용할 수 있습니다 **사용자 할당 id**합니다.

```json
"outputs": {
    "managedIdentity": "[parse('{\"Type\":\"SystemAssigned\"}')]"
}
```

#### <a name="when-to-use-createuidefinition-for-managed-identity"></a>관리 되는 Id에 대 한 CreateUIDefinition을 사용 하는 경우

몇 가지 권장 사항 CreateUIDefinition을 사용 하 여 관리 되는 응용 프로그램에서 Id 관리를 사용 하도록 설정 하는 경우에 다음과 같습니다.

- Azure portal 또는 marketplace를 통과 하는 관리 되는 응용 프로그램 만들기.
- 관리 서비스 Id 복잡 한 소비자 입력이 필요 합니다.
- 관리 서비스 Id 관리 되는 응용 프로그램 생성에 필요 합니다.

#### <a name="systemassigned-createuidefinition"></a>SystemAssigned CreateUIDefinition

기본 CreateUIDefinition을 관리 되는 응용 프로그램에 대 한 SystemAssigned id 수 있도록 합니다.

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

#### <a name="userassigned-createuidefinition"></a>UserAssigned CreateUIDefinition

사용 하는 기본 CreateUIDefinition을 **사용자 할당 id** 리소스 입력으로 관리 되는 응용 프로그램에 대 한 UserAssigned id를 사용 하도록 설정 합니다.

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

위의 CreateUIDefinition.json 소비자는 입력 수에 대 한 텍스트 상자에는 만들기 사용자 경험을 생성 합니다 **사용자 할당 id** Azure 리소스 id입니다. 생성 된 환경을 다음과 같이 보입니다.

![사용자 할당 id CreateUIDefinition 샘플](./media/publish-managed-identity/user-assigned-identity.png)

### <a name="using-azure-resource-manager-templates"></a>Azure 리소스 관리자 템플릿 사용

> [!NOTE]
> Marketplace 응용 프로그램 관리 되는 템플릿은 Azure portal을 통해 전환 하는 고객 환경 만들기에 대 한 자동으로 생성 됩니다.
> 이러한 시나리오는 `managedIdentity` 는 CreateUIDefinition의 출력 키를 사용 하 여 identity를 사용 하도록 설정 해야 합니다.

Azure Resource Manager 템플릿을 통해 관리 되는 Id는 설정할 수도 있습니다. 샘플 아래 사용 하도록 설정 됩니다 **시스템 할당** 관리 되는 응용 프로그램에서 id입니다. Azure Resource Manager 템플릿 매개 변수를 사용 하 여 입력을 제공 하 여 더 복잡 한 identity 개체를 구성할 수 있습니다. 이러한 입력 사용 하 여 관리 되는 응용 프로그램을 만드는 데 사용할 수 있습니다 **사용자 할당 id**합니다.

#### <a name="when-to-use-azure-resource-manager-templates-for-managed-identity"></a>관리 되는 Id에 대 한 Azure Resource Manager 템플릿을 사용 하는 경우

다음은 관리 되는 응용 프로그램에서 Id 관리를 사용 하도록 설정 하는 것에 대 한 Azure Resource Manager 템플릿을 사용 하는 경우에 몇 가지 권장 사항입니다.

- 관리 되는 응용 프로그램 템플릿을 기반으로 프로그래밍 방식으로 배포할 수 있습니다.
- 관리 서비스 Id에 대 한 사용자 지정 역할 할당 관리 되는 응용 프로그램을 프로 비전 할 필요 합니다.
- 관리 되는 응용 프로그램에는 Azure 포털 및 marketplace 만들기 흐름이 필요 하지 않습니다.

#### <a name="systemassigned-template"></a>SystemAssigned 템플릿

사용 하 여 관리 되는 응용 프로그램을 배포 하는 기본 Azure Resource Manager 템플릿을 **시스템 할당** identity입니다.

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

### <a name="userassigned-template"></a>UserAssigned 템플릿

기본 Azure Resource Manager 템플릿을 사용 하 여 관리 되는 응용 프로그램을 배포 하는 **사용자 할당 id**합니다.

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

## <a name="granting-access-to-azure-resources"></a>Azure 리소스에 대 한 액세스를 부여합니다.

관리 되는 응용 프로그램 id에 부여 되 면 기존 azure 리소스 액세스 권한을 부여할 수 있습니다. Azure portal에서 액세스 제어 (IAM) 인터페이스를 통해이 프로세스를 수행할 수 있습니다. 관리 되는 응용 프로그램의 이름 또는 **사용자 할당 id** 역할 할당을 추가 하기 위해 검색할 수 있습니다.

![관리 되는 응용 프로그램에 대 한 역할 할당 추가](./media/publish-managed-identity/identity-role-assignment.png)

## <a name="linking-existing-azure-resources"></a>기존 Azure 리소스 연결

> [!NOTE]
> A **사용자 할당 id** 있어야 [구성](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 관리 되는 응용 프로그램을 배포 하기 전에 합니다. 또한 Managed Applications의 연결 된 리소스 배포에 대 한 지원만 되는 **marketplace** 종류입니다.

관리 Id는 배포 하는 동안 기존 리소스에 액세스 해야 하는 관리 되는 응용 프로그램을 배포 하려면 데도 사용할 수 있습니다. 고객에 의해 관리 되는 응용 프로그램 프로 비전 될 때 **사용자 할당 id** 에 추가 권한 부여를 제공 합니다. 추가할 수 있습니다 합니다 **mainTemplate** 배포 합니다.

### <a name="authoring-the-createuidefinition-with-a-linked-resource"></a>연결 된 리소스를 사용 하 여 CreateUIDefinition 제작

기존 리소스를 모두 기존 Azure 리소스 관리 되는 응용 프로그램의 배포를 연결 하는 경우와 **사용자 할당 id** 해당 역할을 사용 하 여 리소스 할당 제공 해야 합니다.

 샘플 입력 두 개가 필요는 CreateUIDefinition: 네트워크 인터페이스 리소스 ID 및 사용자 할당 id 리소스 id입니다.

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

이 CreateUIDefinition.json 두 필드가 있는 만들기 사용자 경험을 생성 합니다. 첫 번째 필드에 사용자를를 관리 되는 응용 프로그램 배포에 연결 된 리소스에 대 한 Azure 리소스 ID에 입력할 수 있습니다. 두 번째는 소비자가 입력 해야 합니다 **사용자 할당 id** 연결된 된 Azure 리소스에 액세스 권한이 있는 Azure 리소스 ID입니다. 생성 된 환경을 다음과 같이 보입니다.

![두 입력을 사용 하 여 CreateUIDefinition 샘플: 네트워크 인터페이스 리소스 ID 및 사용자 할당 id 리소스 ID](./media/publish-managed-identity/network-interface-cuid.png)

### <a name="authoring-the-maintemplate-with-a-linked-resource"></a>연결 된 리소스를 사용 하 여 mainTemplate 제작

CreateUIDefinition을 업데이트 하는 것 외에도 기본 템플릿에 또한 업데이트 해야 연결 된 리소스 id입니다. 전달 된 적용할 새 매개 변수를 추가 하 여 새 출력을 허용 하도록 기본 서식 파일을 업데이트할 수 있습니다. 이후를 `managedIdentity` 출력에서 생성 된 응용 프로그램 관리 되는 서식 파일의 값이 재정의 기본 템플릿에 전달 되지 않습니다 하 고 매개 변수 섹션에 포함 되지 않아야 합니다.

CreateUIDefinition 제공한 기존 네트워크 인터페이스에 네트워크 프로필을 설정 하는 샘플 기본 템플릿.

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

관리 되는 응용 프로그램 패키지를 만든 후 Azure portal을 통해 관리 되는 응용 프로그램을 사용할 수 있습니다. 사용할 수 있습니다, 전에 몇 가지 필수 구성 요소 단계가 있습니다.

- 필요한 연결 된 Azure 리소스의 인스턴스를 만들어야 합니다.
- **사용자 할당 id** 여야 합니다 [생성 및 역할 할당을 지정](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) 연결 된 리소스입니다.
- 기존의 연결 된 리소스 ID 및 **사용자 할당 id** ID는 CreateUIDefinition에 제공 됩니다.

## <a name="accessing-the-managed-identity-token"></a>관리 되는 Id 토큰에 액세스

관리 되는 응용 프로그램의 토큰을 통해 액세스할 수 있습니다는 `listTokens` 게시자 테 넌 트에서 api. 요청 예제와 같습니다.

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Solutions/applications/{applicationName}/listTokens?api-version=2018-09-01-preview HTTP/1.1
```

샘플 응답은 같습니다.

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

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자 지정 공급자를 사용 하 여 관리 되는 응용 프로그램을 구성 하는 방법](./custom-providers-overview.md)
