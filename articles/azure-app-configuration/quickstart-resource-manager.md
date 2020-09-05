---
title: Azure App Configuration을 사용하여 자동화된 VM 배포 빠른 시작
description: 이 빠른 시작에서는 Azure PowerShell 모듈 및 Azure Resource Manager 템플릿을 사용하여 Azure App Configuration 저장소를 배포하는 방법을 보여 줍니다. 그런 다음, 저장소의 값을 사용하여 VM을 배포합니다.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661472"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>빠른 시작: App Configuration 및 Resource Manager 템플릿을 사용하여 자동화된 VM 배포(ARM 템플릿)

Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 Azure App Configuration 저장소를 배포하는 방법, 저장소에 키-값을 추가하는 방법 및 저장소에서 키-값을 사용하여 이 예제에서는 Azure 가상 머신과 같은 Azure 리소스를 배포하는 방법을 알아봅니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-templates"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 나온 것입니다. [첫 번째 템플릿](https://azure.microsoft.com/resources/templates/101-app-configuration-store/)은 App Configuration 저장소를 만듭니다.

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

템플릿에 다음과 같은 Azure 리소스 하나가 정의되어 있습니다.

- [Microsoft.AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): App Configuration 저장소를 만듭니다.

[두 번째 템플릿](https://azure.microsoft.com/resources/templates/101-app-configuration/)은 저장소의 키 값을 사용하여 가상 머신을 만듭니다. 이 단계를 수행하기 전에 포털 또는 Azure CLI를 사용하여 키-값을 추가해야 합니다.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>템플릿 배포

### <a name="create-an-app-configuration-store"></a>App Configuration 저장소 만들기

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 App Configuration 저장소를 만듭니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. 다음 값을 선택하거나 입력합니다.

    - **구독**: App Configuration 저장소를 만드는 데 사용되는 Azure 구독을 선택합니다.
    - **리소스 그룹**: 기존 리소스 그룹을 사용하려는 경우가 아니면 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.
    - **지역**: 리소스 그룹에 대한 위치를 선택합니다.  예: **미국 동부**
    - **구성 저장소 이름**: 새 App Configuration 저장소 이름을 입력합니다.
    - **위치**: App Configuration 저장소의 위치를 지정합니다.  기본값을 사용합니다.
    - **SKU 이름**: App Configuration 저장소의 SKU 이름을 지정합니다. 기본값을 사용합니다.

1. **검토 + 만들기**를 선택합니다.
1. 페이지에 **전달된 유효성 검사**가 표시되는지 확인하고 **만들기**를 선택합니다.

리소스 그룹 이름 및 App Configuration 저장소 이름을 적어 둡니다.  가상 머신을 배포할 때 이 값이 필요합니다.
### <a name="add-vm-configuration-key-values"></a>VM 구성 키-값 추가

App Configuration 저장소를 만든 후 Azure Portal 또는 Azure CLI를 사용하여 저장소에 키-값을 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, 새로 만든 App Configuration 저장소로 이동합니다.
1. 왼쪽 메뉴에서 **구성 탐색기** 를 선택합니다.
1. **만들기**를 선택하여 다음 키-값 쌍을 추가합니다.

   |키|값|레이블|
   |-|-|-|
   |windowsOsVersion|2019-Datacenter|template|
   |diskSizeGB|1023|template|

   **콘텐츠 형식**은 비워 둡니다.

Azure CLI를 사용하려면 [Azure App Configuration 저장소에서 키 값 사용](./scripts/cli-work-with-keys.md)을 참조하세요.

### <a name="deploy-vm-using-stored-key-values"></a>저장된 키-값을 사용하여 VM 배포

이제 키-값을 저장소에 추가했으므로 Azure Resource Manager 템플릿을 사용하여 VM을 배포할 준비가 되었습니다. 템플릿은 사용자가 만든 **windowsOsVersion** 및 **diskSizeGB** 키를 참조합니다.

> [!WARNING]
> ARM 템플릿은 프라이빗 링크를 사용하도록 설정된 앱 구성 저장소의 키를 참조할 수 없습니다.

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 템플릿은 App Configuration 저장소에서 저장된 키-값을 사용하여 가상 머신을 만듭니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. 다음 값을 선택하거나 입력합니다.

    - **구독**: 가상 머신을 만드는 데 사용되는 Azure 구독을 선택합니다.
    - **리소스 그룹**: App Configuration 저장소와 동일한 리소스 그룹을 지정하거나 **새로 만들기**를 선택하여 새 리소스 그룹을 만듭니다.
    - **지역**: 리소스 그룹에 대한 위치를 선택합니다.  예: **미국 동부**
    - **위치** - 가상 머신의 위치를 지정합니다. 기본값을 사용합니다.
    - **관리자 사용자 이름**: 가상 머신에 대한 관리자 사용자 이름을 지정합니다.
    - **관리자 암호**: 가상 머신에 대한 관리자 암호를 지정합니다.
    - **도메인 이름 레이블**: 고유한 도메인 이름을 지정합니다.
    - **스토리지 계정 이름**: 가상 머신과 연결된 스토리지 계정에 대한 고유한 이름을 지정합니다.
    - **앱 구성 저장소 리소스 그룹**: App Configuration 저장소가 포함된 리소스 그룹을 지정합니다.
    - **앱 구성 저장소 이름**: Azure App Configuration 저장소의 이름을 지정합니다.
    - **VM SKU 키**: **windowsOsVersion**을 지정합니다.  저장소에 추가한 키 값 이름입니다.
    - **디스크 크기 키**: **diskSizeGB**를 지정합니다. 저장소에 추가한 키 값 이름입니다.

1. **검토 + 만들기**를 선택합니다.
1. 페이지에 **전달된 유효성 검사**가 표시되는지 확인하고 **만들기**를 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인한 다음, 새로 만든 가상 머신으로 이동합니다.
1. 왼쪽 메뉴에서 **개요**를 선택하고 **SKU**가 **2019-Datacenter**인지 확인합니다.
1. 왼쪽 메뉴에서 **디스크**를 선택하고 데이터 디스크의 크기가 **2013**인지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, App Configuration 저장소, VM 및 모든 관련 리소스를 삭제합니다. 나중에 App Configuration 저장소 또는 VM을 사용하려는 경우 삭제를 건너뛸 수 있습니다. 이 작업을 계속 사용하지 않으려면 다음과 같은 cmdlet을 실행하여 빠른 시작에서 만든 리소스를 모두 삭제합니다.

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Resource Manager 템플릿 및 Azure App Configuration의 키-값을 사용하여 VM을 배포했습니다.

Azure App Configuration을 사용하여 다른 애플리케이션을 만드는 방법에 대해 자세히 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [빠른 시작: Azure App Configuration을 사용하여 ASP.NET Core 앱 만들기](quickstart-aspnet-core-app.md)
