---
title: Azure Resource Manager 템플릿을 만드는 방법
description: Azure Resource Manager 템플릿을 작성하는 프로세스에 대해 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: b198f860c10bd207a191057e21a8a159c5ebde26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60551033"
---
# <a name="create-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 만들기

이 문서에서는 Azure Resource Manager 템플릿을 만들 때 필요한 프로세스와 결정에 대해 설명합니다. 템플릿을 작성할 때 도움이 될 수 있는 예제와 기능에 대한 개요를 제공합니다. 이 문서에서는 리소스 그룹에 리소스를 배포한다고 가정합니다. Azure 정책 또는 역할 기반 액세스 제어 할당과 같이 Azure 구독에 리소스를 배포해야 하는 경우 [Azure 구독에 대한 리소스 그룹 및 리소스 만들기](deploy-to-subscription.md)를 참조하세요.

## <a name="select-json-editor"></a>JSON 편집기 선택

Resource Manager 템플릿은 JSON 파일입니다. JSON 파일로 작업하려면 훌륭한 제작 도구가 필요합니다. 여러 가지 옵션이 있지만 원하는 편집기가 없는 경우 [Visual Studio Code(VS Code)](https://code.visualstudio.com/)를 설치합니다. 

VS Code를 설치한 다음, [Azure Resource Manager 도구 확장](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)을 추가합니다. 이 확장을 설치하면 템플릿 작성을 간소화하는 다양한 기능이 추가됩니다.

![Visual Studio Code 템플릿](./media/how-to-create-template/template-visual-studio-code.png)

스크린샷은 Visual Studio Code에서 열린 Resource Manager 템플릿을 보여줍니다. 

Resource Manager 도구 확장 설치 자습서와 VS Code 사용 방법에 대한 자세한 내용은 [빠른 시작: Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.

## <a name="understand-the-template-structure"></a>템플릿 구조체 이해

템플릿의 구성 요소를 검토하여 템플릿을 작성하는 방법에 대해 이해합니다. 템플릿에는 모든 섹션이 포함되지는 않습니다. 초점을 맞출 섹션은 다음과 같습니다.

* 값을 보여주는 [매개 변수](resource-group-authoring-templates.md#parameters) 섹션에서는 배포할 때 배포된 인프라를 사용자 지정할 수 있습니다. 

* [변수](resource-group-authoring-templates.md#variables) 섹션에서는 템플릿에서 사용된 값을 보여줍니다.

* [함수](resource-group-authoring-templates.md#functions) 섹션에서는 템플릿에 사용된 사용자 지정 템플릿 식을 보여줍니다.

* [리소스](resource-group-authoring-templates.md#resources) 섹션에서는 구독에 배포된 Azure 리소스를 보여줍니다.

* [출력](resource-group-authoring-templates.md#outputs) 섹션에서는 배포가 완료된 후 반환되는 값을 보여줍니다.

## <a name="look-for-similar-templates"></a>유사한 템플릿 찾기

종종 필요에 따라 유사한 솔루션을 배포하는 기존 템플릿을 찾을 수 있습니다. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에는 커뮤니티 기여자가 제공한 수백 가지의 템플릿이 있습니다.

![빠른 시작 리포지토리 템플릿](./media/how-to-create-template/template-quickstart-repo.png)

필요에 따라 유사한 템플릿에 대한 리포지토리를 통해 검색합니다. 템플릿이 필요한 것과 똑같지 않더라도 사용자가 사용자 지정을 할 수 있습니다.

템플릿을 찾은 후 **Github에서 찾아보기**를 선택한 다음, 리포지토리에서 **azuredeploy.json** 파일을 복사입니다. VS Code에서 **azuredeploy.json**으로 명명된 새 파일을 만들고 빠른 시작 리포지토리에서 복사한 템플릿 파일의 콘텐츠를 추가합니다.

## <a name="add-resources"></a>리소스 추가

템플릿을 사용자 지정하여 원하는 대로 되었는지 확인하고 싶어합니다. 먼저 배포된 리소스를 검토합니다. 템플릿에서 리소스를 추가, 제거 또는 변경해야 할 수도 있습니다. 리소스의 설명 및 구문에 대한 자세한 내용은 [Azure Resource Manager 템플릿 참조](/azure/templates/)를 참조하세요.

![템플릿 참조](./media/how-to-create-template/template-reference.png)

이러한 속성을 검토한 후 필요한 내용을 변경합니다. 리소스를 정의하는 방법에 대한 추천 사항에 대한 자세한 내용은 [리소스-추천 사례](template-best-practices.md#resources)를 참조하세요.

## <a name="add-or-remove-parameters"></a>매개 변수 추가 또는 제거

템플릿에 대한 매개 변수를 조정해야 할 수도 있습니다. 배포할 때 사용하고자 하는 사용자 지정의 수에 따라 매개 변수를 추가하거나 제거할 수 있습니다. 매개 변수를 사용하는 방법에 대한 추천 사항에 대한 자세한 내용은 [매개 변수-추천 사례](template-best-practices.md#parameters)를 참조하세요.

## <a name="add-tags"></a>태그 추가

태그를 추가하여 논리적으로 범주별로 구성하고 청구 비용을 나눌 수 있습니다. 쉽게 태그를 추가하여 리소스에 대한 JSON에서 태그를 적용할 수 있습니다. 예를 들어 다음과 같은 스토리지 계정에는 두 개의 태그가 있습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

또한 매개 변수에서 태그를 동적으로 적용할 수 있습니다. 자세한 내용은 [템플릿 태그](resource-group-using-tags.md#templates)를 참조하세요.

## <a name="review-template-functions"></a>템플릿 함수 검토

`"[some-expression]"`과 같이 대괄호로 둘러 쌓인 템플릿에서 식을 확인할 수 있습니다. 이러한 식은 템플릿 함수를 사용하여 배포하는 동안 값을 동적으로 생성합니다.

예를 들어 다음과 같은 식을 자주 봅니다.

```json
"name": "[parameters('siteName')]"
```

해당 식은 매개 변수의 값을 가져옵니다. 값은 name 속성에 할당됩니다.

또는 다음과 같은 여러 함수를 사용하는 복잡한 식을 볼 수도 있습니다.

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

해당 식은 스토리지 계정의 속성을 사용하여 개체를 가져옵니다.

함수의 기능을 이해하려면 [템플릿 함수 참조](resource-group-template-functions.md) 설명서를 검토하세요.

## <a name="create-more-than-one-instance"></a>두 개 이상의 인스턴스 만들기

두 개 이상의 리소스 인스턴스를 만들고 싶은 경우가 있습니다. 예를 들어 여러 스토리지 계정이 필요할 수 있습니다. 템플릿을 통해 리소스를 반복하는 것 보다는 `copy` 구문을 사용하여 두 개 이상의 인스턴스를 지정할 수 있습니다.

다음 예제에서는 세 개의 스토리지 계정을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

또한 매개 변수에서 동적으로 인스턴스 수를 지정할 수 있습니다. 자세한 내용은 [Azure Resource Manager 템플릿에서 두 개 이상의 리소스 또는 속성 인스턴스 배포](resource-group-create-multiple.md)를 참조하세요.

## <a name="conditionally-deploy-a-resource"></a>조건부 리소스 배포

템플릿에서 리소스 배포 여부를 배포하는 동안 지정해야 하는 경우가 있습니다. 예를 들어 새 리소스를 배포하거나 기존 리소스를 사용할 지에 대한 유연성이 필요할 수 있습니다. `condition` 요소에서는 리소스에 대한 배포를 켜거나 끌 수 있는 기능을 제공합니다. 조건 요소의 식이 참이면 리소스가 배포됩니다. 거짓인 경우 리소스는 배포하는 동안 건너뜁니다.

다음 예제에서는 조건부로 스토리지 계정을 배포합니다.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

자세한 내용은 [조건 요소](resource-group-authoring-templates.md#condition)를 참조하세요.

## <a name="review-dependencies"></a>종속성 검토

템플릿의 일부 리소스는 다른 리소스에 앞서 배포해야 합니다. 예를 들어 SQL 서버는 SQL Database가 생성되기 전에 존재해야 합니다. Resource Manager는 [함수 참조](resource-group-template-functions-resource.md#reference)를 사용할 때 리소스에 대한 배포 순서를 암시적으로 결정합니다. 그러나 경우에 따라 `dependsOn` 요소를 사용하여 종속성을 명시적으로 정의해야 할 필요가 있습니다. 모든 종속성을 추가해야 하는 경우 참조할 템플릿을 검토합니다. 배포 속도가 느려지거나 순환 참조를 할 수 있는 불필요한 종속성을 추가하지 않도록 주의합니다.

다음 이미지는 다양한 App Service 리소스의 종속성 순서를 보여줍니다.

![웹앱 종속성](./media/how-to-create-template/web-dependencies.png)

다음 예제에서는 종속성을 정의하는 템플릿의 일부를 보여줍니다.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

자세한 내용은 [Azure Resource Manager 템플릿에서 리소스를 배포하는 순서 정의](resource-group-define-dependencies.md)를 참조하세요.

## <a name="review-recommended-practices"></a>권장 사례 검토

템플릿을 배포하기 전에 [Azure Resource Manager 템플릿 모범 사례](template-best-practices.md)를 검토하여 템플릿에서 구현하고자 하는 권장 접근 방식이 있는 지 확인합니다.

다른 Azure 클라우드 환경에서 템플릿을 사용할 경우 [클라우드 일관성을 위한 Azure Resource Manager 템플릿 개발](templates-cloud-consistency.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿을 배포하려면 [Azure CLI를 사용하여 배포](resource-group-template-deploy-cli.md) 또는 [PowerShell을 사용하여 배포](resource-group-template-deploy.md)를 참조하세요.
* 템플릿 만들기에 관한 단계별 빠른 시작에 대한 자세한 내용은 [Visual Studio Code를 사용하여 Azure Resource Manager 템플릿 만들기](resource-manager-quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.
* 템플릿에서 사용할 수 있는 함수 목록은 [템플릿 함수](resource-group-template-functions.md)를 참조하세요.
