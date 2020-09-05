---
title: 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 Azure Stream Analytics 작업 만들기
description: 이 빠른 시작에서는 Azure Resource Manager 템플릿을 사용하여 Azure Stream Analytics 작업을 만드는 방법을 보여줍니다.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, subject-armqs
ms.date: 05/28/2020
ms.openlocfilehash: 26ae0f5add40125b5e9c1ce9451583040322314b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641894"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Stream Analytics 작업을 만듭니다. 작업을 만든 후에는 배포의 유효성을 검사합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음이 필요합니다.

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-streamanalytics-create/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-streamanalytics-create/azuredeploy.json":::

템플릿에 정의된 Azure 리소스는 [Microsoft.StreamAnalytics/StreamingJobs](/azure/templates/microsoft.streamanalytics/streamingjobs): Azure Stream Analytics 작업 만들기입니다.

## <a name="deploy-the-template"></a>템플릿 배포

이 섹션에서는 ARM 템플릿을 사용하여 Azure Stream Analytics 작업을 만듭니다.

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿에서 Azure Stream Analytics 작업을 만듭니다.

   [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-streamanalytics-create%2Fazuredeploy.json)

2. Azure Stream Analytics 작업을 만드는 데 필요한 값을 입력합니다.

   ![Azure Resource Manager 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](./media/quick-create-azure-resource-manager/create-stream-analytics-job-resource-manager-template.png "Azure Resource Manager 템플릿을 사용하여 Azure Stream Analytics 작업 만들기")

   다음 값을 제공합니다.

   |속성  |Description  |
   |---------|---------|
   |**구독**     | 드롭다운에서 Azure 구독을 선택합니다.        |
   |**리소스 그룹**     | 새 리소스 그룹을 만들지, 아니면 기존 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../azure-resource-manager/management/overview.md)를 참조하세요. |
   |**지역**     | **미국 동부**를 선택합니다. 사용 가능한 다른 영역은 [지역별 사용 가능한 Azure 서비스](https://azure.microsoft.com/regions/services/)를 참조하세요.        |
   |**Stream Analytics 작업 이름**     | Stream Analytics 작업의 이름을 입력합니다.      |
   |**스트리밍 단위의 수**     |  필요한 스트리밍 단위 수를 선택합니다. 자세한 내용은 [스트리밍 단위 이해 및 조정](stream-analytics-streaming-unit-consumption.md)을 참조하세요.       |

3. **검토 + 만들기**를 선택한 다음, **만들기**를 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 Azure Stream Analytics 작업을 확인하거나 다음 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 리소스를 나열합니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter your Azure Stream Analytics job name:" &&
read streamAnalyticsJobName &&
echo "Enter the resource group where the Azure Stream Analytics job exists:" &&
read resourcegroupName &&
az stream-analytics job show -g $resourcegroupName -n $streamAnalyticsJobName
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Stream Analytics job exists"
(Get-AzResource -ResourceType "Microsoft.StreamAnalytics/StreamingJobs" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

## <a name="clean-up-resources"></a>리소스 정리

후속 자습서를 계속 진행하려는 경우 이러한 리소스를 그대로 유지하는 것이 좋습니다. 더 이상 필요하지 않는 경우 리소스 그룹을 삭제하면 Azure Stream Analytics 작업이 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Stream Analytics 작업을 만들고 배포의 유효성을 검사했습니다. 다음 문서로 이동하고 VS Code를 사용하는 기존 작업에 대해 ARM 템플릿을 내보내는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Stream Analytics 작업 ARM 템플릿 내보내기](resource-manager-export.md)
