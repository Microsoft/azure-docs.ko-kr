---
title: 템플릿 사양 개요
description: 템플릿 사양을 만들고 조직의 다른 사용자와 공유 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: fad7ca60e98dcaabc5f6fc106e0d2c1b77085d67
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89227885"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager 템플릿 사양 (미리 보기)

템플릿 사양은 나중에 배포할 수 있도록 Azure에 Azure Resource Manager 템플릿 (ARM 템플릿)을 저장 하기 위한 새로운 리소스 형식입니다. 이 리소스 종류를 사용 하 여 ARM 템플릿을 조직의 다른 사용자와 공유할 수 있습니다. 다른 Azure 리소스와 마찬가지로 RBAC (역할 기반 액세스 제어)를 사용 하 여 템플릿 사양을 공유할 수 있습니다.

**TemplateSpecs/** 는 템플릿 사양에 대 한 새 리소스 형식입니다. 주 템플릿과 연결 된 템플릿 수에 관계 없이 구성 됩니다. Azure는 리소스 그룹에 템플릿 사양을 안전 하 게 저장 합니다. 템플릿 사양에서 [버전 관리](#versioning)를 지원 합니다.

템플릿 사양을 배포 하려면 PowerShell, Azure CLI, Azure Portal, REST 및 기타 지원 되는 Sdk와 클라이언트와 같은 표준 Azure 도구를 사용 합니다. 템플릿과 동일한 명령을 사용 합니다.

> [!NOTE]
> 템플릿 사양은 현재 미리 보기 상태입니다. 이를 사용하려면 [대기 목록에 등록](https://aka.ms/templateSpecOnboarding)해야 합니다.

## <a name="why-use-template-specs"></a>템플릿 사양을 사용 하는 이유

현재 GitHub 리포지토리 또는 저장소 계정에 템플릿이 있는 경우 템플릿을 공유 하 고 사용 하려고 할 때 몇 가지 문제가 발생 합니다. 사용자가 배포 하려면 템플릿이 로컬 이거나 템플릿에 대 한 URL을 공개적으로 액세스할 수 있어야 합니다. 이러한 제한을 해결 하기 위해 템플릿 복사본을 배포 해야 하는 사용자 또는 리포지토리 또는 저장소 계정에 대 한 액세스를 열어야 할 수 있습니다. 사용자가 템플릿의 로컬 복사본을 소유 하는 경우 이러한 복사본은 결국 원래 템플릿에서 분기할 수 있습니다. 리포지토리 또는 저장소 계정을 공개적으로 액세스할 수 있도록 설정 하면 의도 하지 않은 사용자가 템플릿에 액세스 하도록 허용할 수 있습니다.

템플릿 사양을 사용 하는 경우의 장점으로 정식 템플릿을 만들고 조직의 팀과 공유할 수 있습니다. 템플릿 사양은 배포에 Azure Resource Manager 사용할 수 있지만 RBAC 권한이 없는 사용자는 액세스할 수 없기 때문에 안전 합니다. 사용자는 템플릿을 배포 하기 위한 템플릿 사양에 대 한 읽기 권한만 필요 하므로 다른 사용자가 수정할 수 없도록 템플릿을 공유할 수 있습니다.

조직의 요구 사항과 지침을 따르려면 조직의 관리자가 템플릿 사양에 포함 하는 템플릿을 확인 해야 합니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

다음 예제에서는 Azure에서 저장소 계정을 만드는 간단한 템플릿을 보여 줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

템플릿 사양을 만들 때 PowerShell 또는 CLI 명령이 주 템플릿 파일로 전달 됩니다. 기본 템플릿이 연결 된 템플릿을 참조 하는 경우이 명령은 해당 템플릿을 찾아서 패키지 하 여 템플릿 사양을 만듭니다. 자세히 알아보려면 [연결 된 템플릿을 사용 하 여 템플릿 사양 만들기](#create-a-template-spec-with-linked-templates)를 참조 하세요.

다음을 사용 하 여 템플릿 사양을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

다음을 사용 하 여 구독에서 모든 템플릿 사양을 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

다음을 사용 하 여 해당 버전을 비롯 한 템플릿 사양의 세부 정보를 볼 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0"
```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

템플릿 사양을 만든 후에는 템플릿 사양에 대 한 **읽기** 권한이 있는 사용자가이를 배포할 수 있습니다. 액세스 권한을 부여 하는 방법에 대 한 자세한 내용은 [자습서: Azure PowerShell을 사용 하 여 Azure 리소스에 그룹 액세스 권한 부여](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)를 참조 하세요.

템플릿 사양은 포털, PowerShell, Azure CLI 또는 보다 큰 템플릿 배포에서 연결 된 템플릿으로 배포할 수 있습니다. 조직의 사용자는 Azure (리소스 그룹, 구독, 관리 그룹 또는 테 넌 트)의 모든 범위에 템플릿 사양을 배포할 수 있습니다.

템플릿에 대 한 경로 또는 URI를 전달 하는 대신 리소스 ID를 제공 하 여 템플릿 사양을 배포 합니다. 리소스 ID의 형식은 다음과 같습니다.

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

리소스 ID는 템플릿 사양의 버전 번호를 포함 합니다.

예를 들어 다음 명령을 사용 하 여 템플릿 사양을 배포 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

실제로는를 실행 `Get-AzTemplateSpec` 하 여 배포 하려는 템플릿 사양의 ID를 가져옵니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>매개 변수

매개 변수를 템플릿 사양에 전달 하는 것은 ARM 템플릿에 매개 변수를 전달 하는 것과 같습니다. 매개 변수 값을 인라인 또는 매개 변수 파일에 추가 합니다.

매개 변수를 인라인으로 전달 하려면 다음을 사용 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

로컬 매개 변수 파일을 만들려면 다음을 사용 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

그리고 다음과 같이 해당 매개 변수 파일을 전달 합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="create-a-template-spec-with-linked-templates"></a>연결 된 템플릿을 사용 하 여 템플릿 사양 만들기

템플릿 사양의 기본 템플릿이 연결 된 템플릿을 참조 하는 경우 PowerShell 및 CLI 명령은 로컬 드라이브에서 연결 된 템플릿을 자동으로 찾아 패키지할 수 있습니다. 템플릿 사양을 호스팅하도록 저장소 계정 또는 리포지토리를 수동으로 구성할 필요는 없습니다. 모든 항목은 템플릿 사양 리소스에 자체 포함 되어 있습니다.

다음 예에서는 두 개의 연결 된 템플릿이 있는 주 템플릿으로 구성 되어 있습니다. 이 예제는 템플릿의 발췌 한 것입니다. 이라는 속성을 사용 하 여 `relativePath` 다른 템플릿에 연결 합니다. `apiVersion`배포 리소스에 이상을 사용 해야 합니다 `2020-06-01` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

이전 예제에 대해 템플릿 사양을 만드는 PowerShell 또는 CLI 명령이 실행 되 면이 명령은 기본 템플릿, 웹 앱 템플릿 ( `webapp.json` ) 및 데이터베이스 템플릿 ()의 3 개 파일을 찾은 `database.json` 다음 템플릿 사양으로 패키지 합니다.

자세한 내용은 [자습서: 연결 된 템플릿을 사용 하 여 템플릿 사양 만들기](template-specs-create-linked.md)를 참조 하세요.

## <a name="deploy-template-spec-as-a-linked-template"></a>템플릿 사양을 연결 된 템플릿으로 배포

템플릿 사양을 만들었으면 ARM 템플릿 또는 다른 템플릿 사양에서 다시 사용 하는 것이 쉽습니다. 템플릿에 리소스 ID를 추가 하 여 템플릿 사양에 연결 합니다. 기본 템플릿을 배포할 때 연결 된 템플릿 사양이 자동으로 배포 됩니다. 이 동작을 통해 모듈형 템플릿 사양을 개발 하 고 필요에 따라 다시 사용할 수 있습니다.

예를 들어 네트워킹 리소스를 배포 하는 템플릿 사양 및 저장소 리소스를 배포 하는 다른 템플릿 사양을 만들 수 있습니다. ARM 템플릿에서 네트워킹 또는 저장소 리소스를 구성 해야 하는 경우 언제 든 지 이러한 두 템플릿 사양에 연결할 수 있습니다.

다음 예제는 이전 예제와 비슷하지만 속성을 사용 하 여 `id` `relativePath` 로컬 템플릿에 연결 하는 속성이 아닌 템플릿 사양에 연결 합니다. `2020-06-01`배포 리소스에 대 한 API 버전을 사용 합니다. 이 예제에서 템플릿 사양은 **templateSpecsRG**이라는 리소스 그룹에 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

템플릿 사양을 연결 하는 방법에 대 한 자세한 내용은 [자습서: 템플릿 사양을 연결 된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조 하세요.

## <a name="versioning"></a>버전 관리

템플릿 사양을 만들 때이에 대 한 버전 번호를 제공 합니다. 템플릿 코드를 반복 하면 기존 버전을 업데이트 하거나 (핫픽스의 경우) 새 버전을 게시할 수 있습니다. 버전은 텍스트 문자열입니다. 의미 체계 버전 관리를 포함 하 여 모든 버전 관리 시스템을 따르도록 선택할 수 있습니다. 템플릿 사양의 사용자는 배포할 때 사용할 버전 번호를 제공할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 템플릿 사양을 만들고 배포 하려면 [빠른 시작: 템플릿 사양 만들기 및 배포](quickstart-create-template-specs.md)를 참조 하세요.

* 템플릿 사양에서 템플릿 연결에 대 한 자세한 내용은 [자습서: 연결 된 템플릿을 사용 하 여 템플릿 사양 만들기](template-specs-create-linked.md)를 참조 하세요.

* 템플릿 사양을 연결 된 템플릿으로 배포 하는 방법에 대 한 자세한 내용은 [자습서: 템플릿 사양을 연결 된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조 하세요.
