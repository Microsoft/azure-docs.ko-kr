---
title: ARM(Azure Resource Manager) 템플릿을 사용하여 특정 보안 경고에 대한 보안 자동화 만들기
description: ARM(Azure Resource Manager) 템플릿을 사용하여 특정 Security Center 경고에 의해 트리거되는 논리 앱을 트리거하는 Azure Security Center 자동화를 만드는 방법에 대해 알아봅니다.
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: ecef318978194ac3773c54e2d9c960781086de65
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687216"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-azure-resource-manager-template-arm-template"></a>빠른 시작: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 특정 보안 경고에 대한 자동 응답 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Security Center에 의해 특정 보안 경고가 수신될 때 논리 앱을 트리거하는 워크플로 자동화를 만드는 방법을 설명합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)


## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

Azure Security Center의 워크플로 자동화 기능을 사용하는 데 필요한 역할 및 사용 권한 목록은 [워크플로 자동화](workflow-automation.md)를 참조하세요.


## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>관련 리소스

- [**Microsoft.Security/automations**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftsecurity-resource-provider): 특정 문자열이 포함된 Azure Security Center 경고를 수신할 때 논리 앱을 트리거하는 자동화입니다.
- [**Microsoft.Logic/workflows**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-securitycenter-create-automation-for-alertnamecontains/#microsoftlogic-resource-provider): 트리거가 가능한 빈 논리 앱입니다.

다른 Security Center 빠른 시작 템플릿은 [커뮤니티 제공 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security)을 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

  - **PowerShell**:
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **CLI**:
    ```azurecli-interactive
    az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
    az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
    ```

  - **포털**:

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

    이 배포 옵션에 대한 자세한 내용은 [배포 단추를 사용하여 GitHub 리포지토리에서 템플릿 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal을 사용하여 워크플로 자동화가 배포되었는지 확인합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Security Center**를 엽니다.
1. 상단 메뉴 모음에서 필터 아이콘을 선택하고 새 워크플로 자동화를 배포한 특정 구독을 선택합니다.
1. Security Center의 사이드바에서 **워크플로 자동화**를 열고 새 자동화를 확인합니다.

    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="이미지 대체 텍스트." lightbox="./media/quickstart-automation-alert/validating-template-run.png":::

    >[!TIP]
    > 구독에 많은 워크플로 자동화가 있는 경우 **이름으로 필터링** 옵션을 사용합니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 Azure Portal을 사용하여 워크플로 자동화를 삭제합니다.

1. [Azure Portal](https://portal.azure.com)에서 **Security Center**를 엽니다.
1. 상단 메뉴 모음에서 필터 아이콘을 선택하고 새 워크플로 자동화를 배포한 특정 구독을 선택합니다.
1. Security Center의 사이드바에서 **워크플로 자동화**를 열고 삭제할 자동화를 찾습니다.

    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="이미지 대체 텍스트." lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::

1. 삭제할 항목의 확인란을 선택합니다.
1. 도구 모음에서 삭제를 선택합니다.


## <a name="next-steps"></a>다음 단계

템플릿 만들기 프로세스를 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](/azure/azure-resource-manager/templates/template-tutorial-create-first-template)