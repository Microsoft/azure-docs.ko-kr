---
title: 논리적 조직에 대 한 리소스 태그
description: 태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166595"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>태그를 사용하여 Azure 리소스 구성

Azure 리소스에 태그를 적용 하 여 분류로 논리적으로 구성 합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 "환경" 이름과 "프로덕션" 값을 적용할 수 있습니다.

태그를 적용한 후에는 구독에서 태그 이름과 값으로 모든 리소스를 검색할 수 있습니다. 태그를 사용하면 서로 다른 리소스 그룹에서 관련 리소스를 검색할 수 있습니다. 이 방법은 청구 또는 관리에 대한 리소스를 구성해야 하는 경우에 유용할 수 있습니다.

사용자에 대 한 부담을 줄이고 정확도를 높이는 autotagging 전략 외에도, 분류는 셀프 서비스 메타 데이터 태깅 전략을 고려해 야 합니다.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>제한 사항

다음 제한 사항이 태그에 적용됩니다.

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 각 리소스 또는 리소스 그룹에는 최대 50 개의 태그 이름/값 쌍이 있을 수 있습니다. 허용 되는 최대 수보다 많은 태그를 적용 해야 하는 경우 태그 값에 JSON 문자열을 사용 합니다. JSON 문자열은 단일 태그 이름에 적용되는 여러 값을 포함할 수 있습니다. 리소스 그룹에는 각각 50 개의 태그 이름/값 쌍이 있는 많은 리소스가 포함 될 수 있습니다.
* 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다. 스토리지 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.
* 일반화 된 Vm은 태그를 지원 하지 않습니다.
* 태그는 해당 리소스 그룹의 리소스에 의해 상속되지 않은 리소스 그룹에 적용됩니다.
* Cloud Services와 같은 클래식 리소스에는 태그를 적용할 수 없습니다.
* 태그 이름에는 다음 문자를 포함할 수 없습니다: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > Azure DNS 현재 영역 및 트래픽 관리자 서비스는 태그에서 공백 사용을 허용 하지 않습니다. 

## <a name="required-access"></a>필요한 액세스

리소스에 태그를 적용하려면 사용자에게 해당 리소스 유형에 대한 쓰기 액세스 권한이 있어야 합니다. 모든 리소스 유형에 태그를 적용하려면 [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할을 사용합니다. 하나의 리소스 형식에만 태그를 적용하려면 해당 리소스에 대한 기여자 역할을 사용합니다. 예를 들어, 가상 머신에 태그를 적용하려면 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)를 사용합니다.

## <a name="policies"></a>정책

[Azure Policy](../../governance/policy/overview.md)를 사용하여 태그 지정 규칙을 적용할 수 있습니다. 정책을 만들어 조직에 대해 예상되는 태그를 준수하지 않는 리소스의 시나리오가 구독에 배포되지 않도록 합니다. 수동으로 태그를 적용하거나 준수하지 않는 리소스를 검색하는 대신 배포 중에 필요한 태그를 자동으로 적용하는 정책을 만들 수 있습니다. 이제 새 수정 효과 및 [수정](../../governance/policy/concepts/effects.md#modify) [작업](../../governance/policy/how-to/remediate-resources.md)을 사용 하 여 기존 리소스에 태그를 적용할 수도 있습니다. 다음 섹션에서 태그에 대한 예제 정책을 보여줍니다.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="powershell"></a>PowerShell

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

*지정 된 이름 및 리소스 그룹의 리소스*에 대 한 기존 태그를 보려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

또는 리소스에 대 한 리소스 ID가 있는 경우 해당 리소스 ID를 전달 하 여 태그를 가져올 수 있습니다.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

*특정 태그 이름 및 값을 포함 하는 리소스 그룹*을 가져오려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

*특정 태그 이름 및 값을 포함 하는 리소스*를 가져오려면 다음을 사용 합니다.

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

*특정 태그 이름이 있는 리소스*를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

리소스 또는 리소스 그룹에 태그를 적용할 때마다 해당 리소스 또는 리소스 그룹의 기존 태그가 덮어써집니다. 따라서 리소스 또는 리소스 그룹에 기존 태그가 있는지에 따라 다른 방법을 사용해야 합니다.

*기존 태그를 포함하지 않는 리소스 그룹*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

*기존 태그를 포함하는 리소스 그룹*에 태그를 추가하려면 기존 태그를 검색하고, 새 태그를 추가하고, 태그를 다시 적용합니다.

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

*기존 태그를 포함하지 않는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

리소스 그룹에 동일한 이름의 리소스가 둘 이상 있을 수 있습니다. 이 경우 다음 명령을 사용 하 여 각 리소스를 설정할 수 있습니다.

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

*기존 태그를 포함하는 리소스*에 태그를 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

리소스에 *대 한 기존 태그를 유지 하지 않고*리소스 그룹의 모든 태그를 리소스에 적용 하려면 다음 스크립트를 사용 합니다.

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

리소스 그룹의 모든 태그를 리소스에 적용 하 고 *중복 되지 않는 리소스에 대 한 기존 태그를 유지*하려면 다음 스크립트를 사용 합니다.

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

모든 태그를 제거하려면 빈 해시 테이블을 전달합니다.

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

*리소스 그룹*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
az group show -n examplegroup --query tags
```

그러면 스크립트가 다음 형식을 반환합니다.

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

또는 *지정된 이름, 형식 및 리소스 그룹을 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

리소스 컬렉션을 반복할 때 리소스 ID별로 리소스를 표시하려고 할 수 있습니다. 전체 예제는 이 문서의 뒷부분에 나와 있습니다. *지정된 리소스 ID를 포함한 리소스*에 대한 기존 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

특정 태그가 있는 리소스 그룹을 가져오려면 `az group list`를 사용합니다.

```azurecli-interactive
az group list --tag Dept=IT
```

특정 태그 및 값이 있는 모든 리소스를 가져오려면 `az resource list`를 사용합니다.

```azurecli-interactive
az resource list --tag Dept=Finance
```

리소스 그룹 또는 리소스에 태그를 추가 하는 경우 기존 태그를 덮어쓰거나 기존 태그에 새 태그를 추가할 수 있습니다.

리소스 그룹의 기존 태그를 덮어쓰려면 다음을 사용 합니다.

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

리소스 그룹의 기존 태그에 태그를 추가 하려면 다음을 사용 합니다.

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

리소스의 태그를 덮어쓰려면 다음을 사용 합니다.

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

리소스의 기존 태그에 태그를 추가 하려면 다음을 사용 합니다.

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

리소스에 *대 한 기존 태그를 유지 하지 않고*리소스 그룹의 모든 태그를 리소스에 적용 하려면 다음 스크립트를 사용 합니다.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

리소스 그룹의 모든 태그를 리소스에 적용 하 고 *리소스에 대 한 기존 태그를 유지*하려면 다음 스크립트를 사용 합니다.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

태그 이름 또는 값에 공백이 포함 된 경우 몇 가지 추가 단계를 수행 해야 합니다. 다음 예제에서는 태그에 공백이 포함 될 수 있는 경우 리소스 그룹의 모든 태그를 해당 리소스에 적용 합니다.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>템플릿

배포 하는 동안 리소스에 태그를 표시 하려면 배포 중인 리소스에 `tags` 요소를 추가 합니다. 태그 이름 및 값을 제공합니다.

### <a name="apply-a-literal-value-to-the-tag-name"></a>태그 이름에 리터럴 값 적용

다음 예제에서는 리터럴 값으로 설정된 두 개의 태그(`Dept` 및 `Environment`)가 있는 스토리지 계정을 보여 줍니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

태그를 datetime 값으로 설정 하려면 [utcNow 함수](../templates/template-functions-string.md#utcnow)를 사용 합니다.

### <a name="apply-an-object-to-the-tag-element"></a>개체를 태그 요소에 적용

여러 태그를 저장하는 개체 매개 변수를 정의하고 해당 개체를 태그 요소에 적용할 수 있습니다. 개체의 각 속성은 리소스에 대한 별도의 태그가 됩니다. 다음 예제에는 태그 요소에 적용되는 `tagValues`라는 매개 변수가 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>태그 이름에 JSON 문자열 적용

단일 태그에 여러 값을 저장하려면 값을 나타내는 JSON 문자열을 적용합니다. 전체 JSON 문자열은 256 자를 초과할 수 없는 하나의 태그로 저장 됩니다. 다음 예제에는 JSON 문자열의 여러 값을 포함하는 `CostCenter`라는 단일 태그를 포함합니다.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>리소스 그룹에서 태그 적용

리소스 그룹의 태그를 리소스에 적용 하려면 [resourceGroup](../templates/template-functions-resource.md#resourcegroup) 함수를 사용 합니다. 태그 값을 가져올 때 일부 문자는 점 표기법에서 올바르게 구문 분석 되지 않으므로 `tags.tag-name` 구문 대신 `tags[tag-name]` 구문을 사용 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure Portal 및 PowerShell 둘 다 백그라운드에서 [Resource Manager REST API](/rest/api/resources/) 를 사용합니다. 태그를 다른 환경으로 통합해야 하는 경우 리소스 ID에 대해 **GET**을 사용하여 태그를 가져온 다음 **PATCH** 호출을 사용하여 태그 집합을 업데이트할 수 있습니다.

## <a name="tags-and-billing"></a>태그 및 청구

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 서로 다른 조직에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화합니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

[Azure 리소스 사용 및 RateCard API](../../billing/billing-usage-rate-card-overview.md) 또는 사용 CSV(쉼표로 구분된 값) 파일을 통해 태그에 대한 정보를 검색할 수 있습니다. [Azure 계정 센터](https://account.azure.com/Subscriptions) 또는 Azure Portal에서 사용량 파일을 다운로드할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 다운로드 또는 보기](../../billing/billing-download-azure-invoice-daily-usage-date.md)를 참조하세요. Azure 계정 센터에서 사용량 파일을 다운로드하는 경우 **버전 2**를 선택합니다. 대금 청구에 태그를 지원하는 서비스의 경우 **태그** 열에 태그가 나타납니다.

REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](/rest/api/billing/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 포털 사용에 대한 소개는 [Azure Portal을 사용하여 Azure 리소스 관리](manage-resource-groups-portal.md)를 참조하세요.  
