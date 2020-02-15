---
title: 변경을 방지 하기 위해 리소스 잠그기
description: 모든 사용자 및 역할에 대해 잠금을 적용하여 사용자가 중요한 Azure 리소스를 업데이트하거나 삭제하지 못하도록 합니다.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 70fb189adb634b7ac24afe7cc8b94738117da5ef
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77109546"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>예기치 않은 변경을 방지하기 위해 리소스 잠그기

관리자는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있습니다. 잠금 수준을 **CanNotDelete** 또는 **ReadOnly**로 설정할 수 있습니다. 포털에서 잠금은 각각 **삭제** 및 **읽기 전용**으로 지칭됩니다.

* **CanNotDelete**는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다. 
* **ReadOnly**는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 **판독기** 역할에서 부여한 권한을 제한하는 것과 유사합니다.

## <a name="how-locks-are-applied"></a>잠금을 적용하는 방법

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하여 모든 사용자와 역할에 걸쳐 제한을 적용합니다. 사용자 및 역할에 대한 권한 설정에 대해 알아보려면 [Azure 역할 기반 Access Control](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

리소스 관리자 잠금은 `https://management.azure.com`에 전송된 작업으로 구성되는 관리 수준에서 발생된 작업에만 적용됩니다. 잠금은 리소스 자체 기능을 수행하는 방법을 제한하지 않습니다. 리소스 변경은 제한되지만 리소스 작업은 제한되지 않습니다. 예를 들어, SQL Database에 대해 ReadOnly 잠금이 지정되면 데이터베이스를 삭제하거나 수정하지 못합니다. 데이터베이스에서 데이터를 생성, 업데이트 또는 삭제하지 못하게 됩니다. 이러한 작업은 `https://management.azure.com`전송 되지 않으므로 데이터 트랜잭션이 허용 됩니다.

**ReadOnly** 를 적용 하면 리소스를 수정 하지 않는 일부 작업에는 실제로 잠금으로 차단 된 작업이 필요 하기 때문에 예기치 않은 결과가 발생할 수 있습니다. **ReadOnly** 잠금은 리소스를 포함 하는 리소스 그룹 또는 리소스에 적용할 수 있습니다. **ReadOnly** 잠금으로 차단 된 작업의 몇 가지 일반적인 예는 다음과 같습니다.

* 저장소 계정에 대 한 **ReadOnly** 잠금을 설정 하면 모든 사용자가 키를 나열할 수 없습니다. 반환되는 키를 쓰기 작업에 사용할 수 있기 때문에 목록 키 작업은 POST 요청을 통해 처리됩니다.

* App Service 리소스에 **ReadOnly** 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

* 가상 컴퓨터를 포함 하는 리소스 그룹에 대 한 **ReadOnly** 잠금을 설정 하면 모든 사용자가 가상 컴퓨터를 시작 하거나 다시 시작할 수 없습니다. 이러한 작업에는 POST 요청이 필요 합니다.

## <a name="who-can-create-or-delete-locks"></a>잠금을 만들거나 삭제할 수 있는 사람

관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자**에게만 이러한 작업의 권한이 부여됩니다.

## <a name="managed-applications-and-locks"></a>관리 되는 응용 프로그램 및 잠금

Azure Databricks와 같은 일부 Azure 서비스는 [관리 되는 응용 프로그램](../managed-applications/overview.md) 을 사용 하 여 서비스를 구현 합니다. 이 경우 서비스는 두 개의 리소스 그룹을 만듭니다. 한 리소스 그룹에는 서비스에 대 한 개요가 포함 되어 있으며 잠겨 있지 않습니다. 다른 리소스 그룹에는 서비스에 대 한 인프라가 포함 되어 있으며 잠깁니다.

인프라 리소스 그룹을 삭제 하려고 하면 리소스 그룹이 잠겨 있음을 나타내는 오류가 표시 됩니다. 인프라 리소스 그룹에 대 한 잠금을 삭제 하려고 하면 시스템 응용 프로그램이 소유 하 고 있으므로 잠금을 삭제할 수 없다는 오류 메시지가 표시 됩니다.

대신 인프라 리소스 그룹도 삭제 하는 서비스를 삭제 합니다.

관리 되는 응용 프로그램의 경우 배포한 서비스를 선택 합니다.

![서비스 선택](./media/lock-resources/select-service.png)

서비스에 **관리 되는 리소스 그룹**에 대 한 링크가 포함 되어 있는지 확인 합니다. 해당 리소스 그룹은 인프라를 보유 하 고 있으며 잠깁니다. 직접 삭제할 수 없습니다.

![관리 그룹 표시](./media/lock-resources/show-managed-group.png)

잠긴 인프라 리소스 그룹을 포함 하 여 서비스에 대 한 모든 항목을 삭제 하려면 서비스에 대해 **삭제** 를 선택 합니다.

![서비스 삭제](./media/lock-resources/delete-service.png)

## <a name="azure-backups-and-locks"></a>Azure 백업 및 잠금

Azure Backup 서비스에 의해 생성 된 리소스 그룹을 잠그면 백업이 실패 하기 시작 합니다. 이 서비스는 최대 18 개의 복원 지점만 지원 합니다. **Cannotdelete** 잠금을 사용 하 여 백업 서비스에서 복원 지점이 정리 되지 않습니다. 자세한 내용은 질문과 [대답-Azure Vm 백업](../../backup/backup-azure-vm-backup-faq.md)을 참조 하세요.

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>템플릿

리소스 관리자 템플릿을 사용 하 여 잠금을 배포할 때 잠금 범위에 따라 이름 및 형식에 대해 서로 다른 값을 사용 합니다.

**리소스**에 잠금을 적용 하는 경우 다음 형식을 사용 합니다.

* 이름-`{resourceName}/Microsoft.Authorization/{lockName}`
* 유형 - `{resourceProviderNamespace}/{resourceType}/providers/locks`

**리소스 그룹** 또는 **구독**에 잠금을 적용 하는 경우 다음 형식을 사용 합니다.

* 이름-`{lockName}`
* 유형 - `Microsoft.Authorization/locks`

다음 예제에서는 웹 사이트에 App Service 계획, 웹 사이트 및 잠금을 만드는 템플릿을 보여 줍니다. 잠금의 리소스 종류는 잠그려는 리소스의 리소스 종류로, **입니다**. 잠금의 이름은 리소스 이름을 **/Microsoft.Authorization/** 과 연결하여 만들어집니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

리소스 그룹에 대 한 잠금을 설정 하는 예제는 [리소스 그룹을 만들고 잠그기](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)를 참조 하세요.

## <a name="powershell"></a>PowerShell
[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 명령을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

잠금에 대 한 정보를 가져오려면 [AzResourceLock](/powershell/module/az.resources/get-azresourcelock)을 사용 합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

[az lock create](/cli/azure/lock#az-lock-create) 명령을 사용하여 Azure CLI를 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [az lock list](/cli/azure/lock#az-lock-list)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup
```

잠금을 삭제하려면 다음을 사용합니다.

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST API
[관리 잠금을 위한 REST API](https://docs.microsoft.com/rest/api/resources/managementlocks)를 사용하여 배포된 리소스를 잠글 수 있습니다. REST API를 사용하여 잠금을 만들고, 삭제하고, 기존 잠금에 대한 정보를 검색할 수 있습니다.

잠금을 만들려면 다음을 실행합니다.

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. lock-name은 원하는 잠금 이름입니다. Api 버전의 경우 **2016-09-01**을 사용 합니다.

요청에서 잠금에 대한 속성을 지정하는 JSON 개체를 포함합니다.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>다음 단계
* 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](tag-resources.md)
* 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [Azure Policy란?](../../governance/policy/overview.md)을 참조하세요.
* 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.

