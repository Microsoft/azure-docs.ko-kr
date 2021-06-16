---
title: 자습서 - Azure Resource Manager Bicep 파일의 리소스에 태그 추가
description: Bicep 파일에서 배포하는 리소스에 태그를 추가합니다. 태그를 사용하여 리소스를 논리적으로 구성할 수 있습니다.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: ea5e078eb692d002b3f86cd43663dd042d692611
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353157"
---
# <a name="tutorial-add-tags-in-azure-resource-manager-bicep-files"></a>자습서: Azure Resource Manager Bicep 파일에 태그 추가

이 자습서에서는 Bicep 파일의 리소스에 태그를 추가하는 방법에 대해 알아봅니다. [태그](../management/tag-resources.md)를 통해 리소스를 논리적으로 구성할 수 있습니다. 태그 값이 비용 보고서에 표시됩니다. 이 자습서를 완료하는 데 **8분** 이 소요됩니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>사전 요구 사항

[빠른 시작 템플릿에 대한 자습서](bicep-tutorial-quickstart-template.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Bicep 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [Bicep 도구](bicep-tutorial-create-first-bicep.md#get-tools)를 참조하세요.

## <a name="review-bicep-file"></a>Bicep 파일 검토

이전 Bicep 파일에서는 스토리지 계정, App Service 계획 및 웹앱을 배포했습니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.bicep":::

이러한 리소스를 배포한 후에는 비용을 추적하고 범주에 속하는 리소스를 찾아야 할 수도 있습니다. 이러한 문제를 해결하는 데 도움이 되는 태그를 추가할 수 있습니다.

## <a name="add-tags"></a>태그 추가

리소스에 태그를 지정하여 사용 여부를 식별하는 데 도움이 되는 값을 추가합니다. 예를 들어 환경 및 프로젝트를 나열하는 태그를 추가할 수 있습니다. 비용 센터 또는 리소스를 소유하는 팀을 식별하는 태그를 추가할 수 있습니다. 조직에 적합한 값을 추가합니다.

다음 예제는 Bicep 파일의 변경 내용을 보여줍니다. 전체 파일을 복사하고 Bicep 파일을 해당 콘텐츠로 바꿉니다.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep" range="1-81" highlight="27-30,38,51,71":::

## <a name="deploy-bicep-file"></a>Bicep 파일 배포

Bicep 파일을 배포하고 결과를 확인할 시간입니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](bicep-tutorial-create-first-bicep.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](bicep-tutorial-create-first-bicep.md#deploy-bicep-file)에 표시된 대로 `bicepFile` 변수를 Bicep 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이 배포 cmdlet을 실행하려면 Azure PowerShell의 [최신 버전](/powershell/azure/install-az-ps)이 있어야 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addtags `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addtags \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 스토리지 계정 리소스와 같은 리소스 중 하나를 선택합니다. 이제 태그가 있음을 볼 수 있습니다.

   ![태그 표시](./media/bicep-tutorial-add-tags/show-tags.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 리소스에 태그를 추가했습니다. 다음 자습서에서는 매개 변수 파일을 사용하여 배포에 값을 간편하게 전달하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [매개 변수 파일 사용](bicep-tutorial-use-parameter-file.md)
