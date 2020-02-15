---
title: 템플릿을 사용한 조건부 배포
description: Azure Resource Manager 템플릿에서 리소스를 조건부로 배포 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: 83aa22ba57e0111d060665778922437723481c69
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207794"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>리소스 관리자 템플릿의 조건부 배포

경우에 따라 템플릿에서 리소스를 배포 해야 합니다. `condition` 요소를 사용 하 여 리소스 배포 여부를 지정 합니다. 이 요소 값은 true 또는 false로 확인됩니다. 값이 true이면 리소스가 만들어집니다. 값이 false이면 리소스가 만들어지지 않습니다. 값은 전체 리소스에만 적용할 수 있습니다.

## <a name="new-or-existing-resource"></a>신규 또는 기존 리소스

조건부 배포를 사용 하 여 새 리소스를 만들거나 기존 리소스를 사용할 수 있습니다. 다음 예제에서는 조건을 사용 하 여 새 저장소 계정을 배포 하거나 기존 저장소 계정을 사용 하는 방법을 보여 줍니다.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

**Neworexisting** 매개 변수가 **new**로 설정 된 경우 조건은 true로 평가 됩니다. 저장소 계정이 배포 됩니다. 그러나 **Neworexisting** 이 **기존**으로 설정 된 경우 조건이 false로 평가 되 고 저장소 계정이 배포 되지 않습니다.

`condition` 요소를 사용하는 전체 예제 템플릿은 [신규 또는 기존 가상 네트워크, 스토리지 및 공용 IP를 사용하는 VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)을 참조하세요.

## <a name="allow-condition"></a>허용 조건

조건이 허용 되는지 여부를 나타내는 매개 변수 값을 전달할 수 있습니다. 다음 예에서는 SQL server를 배포 하 고 선택적으로 Azure Ip를 허용 합니다.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

전체 템플릿은 [AZURE SQL 논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)를 참조 하세요.

## <a name="runtime-functions"></a>런타임 함수

조건부로 배포 되는 리소스에 [참조](template-functions-resource.md#reference) 또는 [목록](template-functions-resource.md#list) 함수를 사용 하는 경우 리소스가 배포 되지 않은 경우에도 함수가 평가 됩니다. 함수가 존재 하지 않는 리소스를 참조 하는 경우 오류가 발생 합니다.

[If](template-functions-logical.md#if) 함수를 사용 하 여 리소스가 배포 될 때 함수가 조건에 대해서만 계산 되도록 합니다. If를 사용 하 고 조건부로 배포 된 리소스를 참조 하는 샘플 템플릿은 [if 함수](template-functions-logical.md#if) 를 참조 하세요.

리소스를 다른 리소스와 동일 하 게 조건부 리소스에 [종속 된 것으로](define-resource-dependency.md) 설정 합니다. 조건부 리소스가 배포 되지 않은 경우 Azure Resource Manager은 필요한 종속성에서 자동으로 제거 합니다.

## <a name="condition-with-complete-mode"></a>Complete 모드의 조건

[전체 모드](deployment-modes.md) 를 사용 하 여 템플릿을 배포 하 고 조건이 false로 평가 되므로 리소스가 배포 되지 않은 경우 결과는 템플릿을 배포 하는 데 사용 하는 REST API 버전에 따라 달라 집니다. 2019-05-10 이전 버전을 사용 하는 경우 리소스는 **삭제 되지 않습니다**. 2019-05-10 이상에서는 리소스가 **삭제 됩니다**. 최신 버전의 Azure PowerShell 하 고 조건이 false 인 경우 리소스를 삭제 Azure CLI.

## <a name="next-steps"></a>다음 단계

* 템플릿을 만드는 방법에 대한 권장 사항은 [Azure Resource Manager 템플릿 모범 사례](template-best-practices.md)를 참조하세요.
* 리소스의 여러 인스턴스를 만들려면 [Azure Resource Manager 템플릿에서 리소스 반복](copy-resources.md)을 참조 하세요.