---
title: 배포 모드
description: Azure Resource Manager를 사용하여 전체 또는 증분 배포 모드를 사용할지 여부를 지정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 9cc0e8eb20a0375a98906524d4d598ccb473d2a8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207590"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 배포 모드

리소스를 배포할 때 배포를 증분 업데이트 또는 전체 업데이트할지 지정합니다. 이러한 두 모드의 차이는 리소스 관리자 템플릿에 없는 리소스 그룹의 기존 리소스를 처리 하는 방법입니다.

두 모드에서 모두, Resource Manager는 템플릿에 지정된 모든 리소스를 만들려고 합니다. 리소스 그룹에 이미 리소스가 있고 해당 설정이 변경되지 않은 경우 해당 리소스에 대한 작업이 수행되지 않습니다. 리소스의 속성 값을 변경하는 경우 리소스가 새 값으로 업데이트됩니다. 기존 리소스의 위치 또는 종류를 업데이트하려고 하면 오류가 발생하여 배포에 실패합니다. 대신, 새 리소스를 필요한 위치 또는 유형으로 배포합니다.

기본 모드는 증분입니다.

## <a name="complete-mode"></a>전체 모드

전체 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **삭제**합니다.

[조건이](conditional-resource-deployment.md) false로 평가 되므로 템플릿에 배포 되지 않은 리소스가 포함 된 경우에는 템플릿을 배포 하는 데 사용 하는 REST API 버전에 따라 결과가 달라 집니다. 2019-05-10 이전 버전을 사용 하는 경우 리소스는 **삭제 되지 않습니다**. 2019-05-10 이상에서는 리소스가 **삭제 됩니다**. 최신 버전의 Azure PowerShell 및 Azure CLI 리소스를 삭제 합니다.

[복사 루프](copy-resources.md)에서 전체 모드를 사용할 때는 주의 해야 합니다. 복사 루프를 확인 한 후 템플릿에 지정 되지 않은 모든 리소스는 삭제 됩니다.

[템플릿에서 둘 이상의 리소스 그룹](cross-resource-group-deployment.md)에 배포 하는 경우 배포 작업에 지정 된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제 되지 않습니다.

리소스 형식에서 완료 모드 삭제를 처리 하는 방법에는 몇 가지 차이점이 있습니다. 전체 모드로 배포된 템플릿에 없는 경우 부모 리소스가 자동으로 삭제됩니다. 일부 자식 리소스는 템플릿에 없더라도 자동으로 삭제되지 않습니다. 그러나 이러한 자식 리소스는 부모 리소스가 삭제 되는 경우 삭제 됩니다.

예를 들어 리소스 그룹에 DNS 영역(Microsoft.Network/dnsZones 리소스 종류)과 CNAME 레코드(Microsoft.Network/dnsZones/CNAME 리소스 종류)가 포함된 경우 DNS 영역은 CNAME 레코드의 부모 리소스입니다. 전체 모드로 배포하고 템플릿에 DNS 영역을 포함하지 않으면 DNS 영역과 CNAME 레코드가 둘 다 삭제됩니다. 템플릿에 DNS 영역을 포함 하지만 CNAME 레코드를 포함 하지 않는 경우 CNAME은 삭제 되지 않습니다.

리소스 종류의 삭제 처리 방식에 대한 목록은 [전체 모드 배포에 대한 Azure 리소스 삭제](complete-mode-deletion.md)를 참조하세요.

리소스 그룹이 [잠겨](../management/lock-resources.md)있으면 전체 모드에서 리소스를 삭제 하지 않습니다.

> [!NOTE]
> 루트 수준 템플릿만 전체 배포 모드를 지원합니다. [연결된 또는 중첩된 템플릿](linked-templates.md)의 경우 증분 모드만 사용해야 합니다.
>
> [구독 수준 배포](deploy-to-subscription.md) 는 완료 모드를 지원 하지 않습니다.
>
> 현재 포털에서 전체 모드를 지원 하지 않습니다.
>

## <a name="incremental-mode"></a>증분 모드

증분 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **변경하지 않고 유지**합니다. 템플릿의 리소스가 리소스 그룹에 **추가 됩니다** .

> [!NOTE]
> 증분 모드에서 기존 리소스를 다시 배포 하는 경우 모든 속성이 다시 적용 됩니다. **속성은 증분 추가 되지 않습니다**. 일반적인 있다면 오해 템플릿에 지정 되지 않은 속성은 변경 되지 않은 상태로 유지 하는 것입니다. 특정 속성을 지정 하지 않을 경우 리소스 관리자는 배포를 해당 값을 덮어쓰는 것으로 해석 합니다. 템플릿에 포함 되지 않은 속성은 기본값으로 다시 설정 됩니다. 업데이트 하는 것 뿐만 아니라 리소스에 대해 기본값이 아닌 값을 모두 지정 합니다. 템플릿의 리소스 정의는 항상 리소스의 최종 상태를 포함 합니다. 기존 리소스에 대 한 부분 업데이트를 나타낼 수 없습니다.

## <a name="example-result"></a>결과 예제

증분 및 전체 모드 간의 차이를 보여주려면 다음 시나리오를 고려합니다.

**리소스 그룹**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C

**템플릿**은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

리소스 그룹이 **증분** 모드로 배포된 경우 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C
* 리소스 D

리소스 C가 **전체** 모드로 배포된 경우 삭제됩니다. 리소스 그룹은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

## <a name="set-deployment-mode"></a>배포 모드 설정

PowerShell을 사용하여 배포하는 경우 배포 모드를 설정하려면 `Mode` 매개 변수를 사용합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Azure CLI를 사용하여 배포하는 경우 배포 모드를 설정하려면 `mode` 매개 변수를 사용합니다.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

다음 예제에서는 증분 배포 모드로 연결된 템플릿 세트를 보여줍니다.

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>다음 단계

* 리소스 관리자 템플릿을 만드는 방법에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성](template-syntax.md)을 참조하세요.
* 리소스 배포에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿을 사용하여 애플리케이션 배포](deploy-powershell.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.
