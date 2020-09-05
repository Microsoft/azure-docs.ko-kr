---
title: 빠른 시작 - 지역 복제 레지스트리 만들기 - Azure Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용하여 지역 복제 Azure Container Registry를 만드는 방법을 알아봅니다.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 2cfce37ff63a8321f40843ced2a7b786bcfc013e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88649605"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 지역 복제 컨테이너 레지스트리 만들기

이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 Azure Container Registry 인스턴스를 만드는 방법을 보여 줍니다. 이 템플릿은 여러 Azure 지역의 레지스트리 콘텐츠를 자동으로 동기화하는 [지역 복제](container-registry-geo-replication.md) 레지스트리를 설정합니다. 지역 복제는 단일 관리 환경을 제공하는 동시에 지역 배포의 이미지에 대한 네트워크 방식의 액세스를 지원합니다. [프리미엄](container-registry-skus.md) 레지스트리 서비스 계층의 기능입니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/)에서 나온 것입니다. 템플릿은 레지스트리 및 추가 지역 복제본을 설정합니다.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json":::

다음 리소스는 템플릿에 정의되어 있습니다.

* **[Microsoft.ContainerRegistry/registries](/azure/templates/microsoft.containerregistry/registries)** : Azure Container Registry 만들기
* **[Microsoft.ContainerRegistry/registries/replications](/azure/templates/microsoft.containerregistry/registries/replications)** : 컨테이너 레지스트리 복제본 만들기

[빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)에서 더 많은 Azure Container Registry 템플릿 샘플을 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

 1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. 다음 값을 선택하거나 입력합니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기**를 선택하고 리소스 그룹에 고유한 이름을 입력한 다음, **확인**을 선택합니다.
    * **위치**: 리소스 그룹에 대한 위치를 선택합니다. 예제: **미국 중부**
    * **ACR 이름**: 레지스트리에 대해 생성된 이름을 적용하거나 이름을 입력합니다. 전역적으로 고유해야 합니다.
    * **위치**: 레지스트리의 홈 복제본에 대해 생성된 위치를 수락하거나 **미국 중부**와 같은 위치를 입력합니다. 
    * **ACR 복제본 위치**: 지역의 짧은 이름을 사용하여 레지스트리 복제본의 위치를 입력합니다. 홈 레지스트리 위치와 달라야 합니다. 예: **westeurope**.
    * **위에 명시된 사용 약관에 동의함**: 선택합니다.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="템플릿 속성":::

 3. 사용 약관에 동의하는 경우 **구매**를 선택합니다. 레지스트리가 성공적으로 만들어지면 다음과 같이 알림을 받게 됩니다.

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="포털 알림":::

 Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-cli.md)를 참조하세요.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

Azure Portal 또는 Azure CLI와 같은 도구를 사용하여 컨테이너 레지스트리의 속성을 검토합니다.

1. 포털에서 Container Registry를 검색하고, 생성한 컨테이너 레지스트리를 선택합니다.

1. **개요** 페이지에서 레지스트리의 **로그인 서버**를 확인합니다. Docker를 사용하여 이미지에 태그를 지정하고 레지스트리에 푸시할 때 이 URI를 사용합니다. 자세한 내용은 [Docker CLI를 사용하여 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)를 참조하세요.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="레지스트리 개요":::

1. **복제본** 페이지에서 홈 복제본과 템플릿을 통해 추가된 복제본의 위치를 확인합니다. 원하는 경우 이 페이지에서 복제본을 더 추가합니다.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="레지스트리 복제":::

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않으면 리소스 그룹, 레지스트리 및 레지스트리 복제본을 삭제합니다. 이렇게 하려면 Azure Portal로 이동하고, 레지스트리가 포함된 리소스 그룹을 선택한 다음, **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Container Registry를 만들고 다른 위치에 레지스트리 복제본을 구성했습니다. Azure Container Registry 자습서를 계속 진행하여 ACR에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Container Registry 자습서](container-registry-tutorial-prepare-registry.md)

템플릿 만들기 프로세스를 안내하는 단계별 자습서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
