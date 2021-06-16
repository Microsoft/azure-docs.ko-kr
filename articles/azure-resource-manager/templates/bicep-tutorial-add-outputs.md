---
title: 자습서 - Azure Resource Manager Bicep 파일에 출력 추가
description: Bicep 파일에 출력을 추가하여 구문을 단순화합니다.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 4b7d7a1414091c516dba2c474e1681ba357b55a1
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353169"
---
# <a name="tutorial-add-outputs-to-azure-resource-manager-bicep-file"></a>자습서: Azure Resource Manager Bicep 파일에 출력 추가

이 자습서에서는 배포에서 값을 반환하는 방법을 알아봅니다. 배포된 리소스의 값이 필요한 경우 출력을 사용합니다. 이 자습서를 완료하는 데 **7분** 이 소요됩니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[변수에 대한 자습서](bicep-tutorial-add-variables.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 자습서의 끝 부분에 Bicep 파일의 내용은 다음과 같습니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep":::

스토리지 계정을 배포하지만 스토리지 계정에 대한 정보는 반환하지 않습니다. 나중에 참조할 수 있도록 새 리소스에서 속성을 캡처해야 할 수도 있습니다.

## <a name="add-outputs"></a>출력 추가

출력을 사용하여 배포에서 값을 반환할 수 있습니다. 예를 들어 새 스토리지 계정에 대한 엔드포인트를 가져오는 것이 유용할 수 있습니다.

다음 예제에서는 출력 값을 추가하기 위한 Bicep 파일의 변경 내용을 보여줍니다. 전체 파일을 복사하고 Bicep 파일을 해당 콘텐츠로 바꿉니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.bicep" range="1-33" highlight="33":::

추가한 출력 값과 관련하여 알아두어야 할 몇 가지 중요한 항목이 있습니다.

반환되는 값의 유형은 `object`로 설정되어 템플릿 개체를 반환합니다.

스토리지 계정에서 `primaryEndpoints` 속성을 가져오려면 스토리지 계정 기호화된 이름을 사용합니다. Visual Studio Code의 자동 완성 기능은 속성의 전체 목록을 제공합니다.

   ![Visual Studio Code Bicep 기호 이름 개체 속성](./media/bicep-tutorial-add-outputs/visual-studio-code-bicep-output-properties.png)

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Bicep 파일을 배포하고 반환되는 값을 확인할 준비가 되었습니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 `bicepFile` 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addoutputs `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addoutputs \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

배포 명령의 출력에서는 출력이 JSON 형식인 경우에만 다음 예제와 유사한 개체가 표시됩니다.

```json
{
  "dfs": "https://storeluktbfkpjjrkm.dfs.core.windows.net/",
  "web": "https://storeluktbfkpjjrkm.z19.web.core.windows.net/",
  "blob": "https://storeluktbfkpjjrkm.blob.core.windows.net/",
  "queue": "https://storeluktbfkpjjrkm.queue.core.windows.net/",
  "table": "https://storeluktbfkpjjrkm.table.core.windows.net/",
  "file": "https://storeluktbfkpjjrkm.file.core.windows.net/"
}
```

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="review-your-work"></a>작업 검토

최근 6개의 자습서에서 많은 작업을 수행했습니다. 지금까지 수행한 작업을 검토해 보겠습니다. 제공하기 쉬운 매개 변수를 사용하여 Bicep 파일을 만들었습니다. Bicep 파일은 사용자 지정이 가능하고 필요한 값을 동적으로 만들 수 있으므로 여러 환경에서 재사용할 수 있습니다. 또한 스크립트에서 사용할 수 있는 스토리지 계정에 대한 정보도 반환합니다.

이제 리소스 그룹 및 배포 기록을 살펴보겠습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 수행한 단계에 따라 리소스 그룹에 하나 이상의 스토리지 계정이 있을 것입니다.
1. 또한 기록에 몇 가지 성공적인 배포가 나열되어 있을 것입니다. 해당 링크를 선택합니다.

   ![배포 선택](./media/bicep-tutorial-add-outputs/select-deployments.png)

1. 모든 배포가 기록에 표시됩니다. **addoutputs** 라는 배포를 선택합니다.

   ![배포 기록 표시](./media/bicep-tutorial-add-outputs/show-history.png)

1. 입력을 검토할 수 있습니다.

   ![입력 표시](./media/bicep-tutorial-add-outputs/show-inputs.png)

1. 출력을 검토할 수 있습니다.

   ![출력 표시](./media/bicep-tutorial-add-outputs/show-outputs.png)

1. JSON 템플릿을 검토할 수 있습니다.

   ![템플릿 표시](./media/bicep-tutorial-add-outputs/show-template.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Bicep 파일에 반환 값을 추가했습니다. 다음 자습서에서는 JSON 템플릿을 내보내고 Bicep 파일에서 내보낸 템플릿의 일부를 사용하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [내보낸 템플릿 사용](bicep-tutorial-export-template.md)
