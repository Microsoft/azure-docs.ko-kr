---
title: '빠른 시작: 프로필 또는 엔드포인트 만들기 - Azure Resource Manager 템플릿'
titleSuffix: Azure Content Delivery Network
description: Azure Content Delivery Network 프로필 및 Resource Manager 템플릿 엔드포인트를 만드는 방법 알아보기
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: aca3c9c52260d3ea2ab8def37566377fa67e712d
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705931"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>빠른 시작: Azure CDN 프로필 및 엔드포인트 만들기 - ARM 템플릿

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure CDN(Content Delivery Network)을 시작합니다. 템플릿은 프로필 및 엔드포인트를 배포합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/)에서 나온 것입니다.

이 템플릿은 다음을 만들도록 구성되어 있습니다.

* 프로필
* 엔드포인트

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.

* **[Microsoft.Cdn/profiles](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>템플릿 배포

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>포털

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 패널에서 **리소스 그룹** 을 선택합니다.

3. 이전 섹션에서 만든 리소스 그룹을 선택합니다. 기본 리소스 그룹 이름은 **myResourceGroupCDN**입니다.

4. 다음 리소스가 리소스 그룹에서 만들어졌는지 확인합니다.

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Azure CDN 리소스 그룹" border="true":::

## <a name="clean-up-resources"></a>리소스 정리

### <a name="azure-cli"></a>Azure CLI

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group#az-group-delete) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 제거할 수 있습니다.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>포털

더 이상 필요하지 않으면 리소스 그룹, CDN 프로필 및 모든 관련 리소스를 삭제합니다. CDN 프로필 및 엔드포인트가 포함된 **myResourceGroupCDN** 리소스 그룹을 선택한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 만들었습니다.

* CDN 프로필
* 엔드포인트

Azure CDN 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

* [Azure CDN 개요](cdn-overview.md) 읽기
* [Azure Resource Manager](../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기