---
title: '자습서: 세션 및 디바이스에서 앵커 공유'
description: 이 자습서에서는 백 엔드 서비스가 있는 Unity의 Android/iOS 디바이스 간에 Azure Spatial Anchors 식별자를 공유하는 방법을 알아봅니다.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 12a15a2d0cd45207b4adefc7315e48c6d642c0f7
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810180"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices"></a>자습서: 세션 및 디바이스에서 Azure Spatial Anchors 공유

이 자습서에서는 [Azure Spatial Anchors](../overview.md)를 사용하여 한 세션에서 앵커를 만든 다음, 동일한 디바이스나 다른 디바이스에서 이 세션을 찾는 방법을 알아봅니다. 같은 위치에 있는 여러 디바이스에서 동시에 동일한 앵커를 찾을 수도 있습니다.

![지속성](./media/persistence.gif)

Azure Spatial Anchors는 시간이 지남에 따라 디바이스에서 위치를 유지하는 개체를 사용하여 혼합 현실 환경을 만들 수 있는 플랫폼 간 개발자 서비스입니다. 작업이 완료되면 둘 이상의 디바이스에 배포할 수 있는 앱이 제공됩니다. 한 인스턴스에서 만든 Azure Spatial Anchors는 다른 사용자와 공유할 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 앵커 공유에 사용할 수 있는 Azure에 ASP.NET Core 웹앱을 배포하여 일정 기간 메모리에 저장합니다.
> * 빠른 시작의 Unity 샘플 내에 AzureSpatialAnchorsLocalSharedDemo 장면을 구성하여 공유 앵커 웹앱을 활용합니다.
> * 하나 이상의 디바이스에 배포하고 실행합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

이 자습서에서는 Unity와 Azure ASP.NET Core 웹앱을 사용하지만 다른 디바이스 간에 Azure Spatial Anchor 식별자를 공유하는 방법에 대한 예를 보여줄 뿐입니다. 다른 언어와 백 엔드 기술을 사용하여 동일한 목표를 달성할 수 있습니다.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>샘플 프로젝트 다운로드

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-your-sharing-anchors-service"></a>공유 앵커 서비스 배포

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Visual Studio를 열고 `Sharing\SharingServiceSample` 폴더에서 프로젝트를 엽니다.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

VS Code에 서비스를 배포하기 전에 리소스 그룹 및 App Service 계획을 만들어야 합니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

<a href="https://portal.azure.com/" target="_blank">Azure Portal</a>로 이동하고 Azure 구독에 로그인합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

**리소스 그룹** 옆에 있는 **새로 만들기**를 선택합니다.

리소스 그룹의 이름을 **myResourceGroup**으로 지정하고 **확인**을 선택합니다.

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

**호스팅 계획** 옆에 있는 **새로 만들기**를 선택합니다.

**호스팅 계획 구성** 대화 상자에서 다음 설정을 사용합니다.

| 설정 | 제안 값 | 설명 |
|-|-|-|
|App Service 플랜| MySharingServicePlan | App Service 플랜의 이름입니다. |
| 위치 | 미국 서부 | 웹앱이 호스팅된 데이터 센터입니다. |
| 크기 | 무료 | 호스팅 기능을 결정하는 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)입니다. |

**확인**을 선택합니다.

Visual Studio Code를 열고 `Sharing\SharingServiceSample` 폴더에서 프로젝트를 엽니다. <a href="https://docs.microsoft.com/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">이 자습서</a>를 따라 Visual Studio Code를 통해 공유 서비스를 배포합니다. 'Visual Studio Code를 사용하여 열기' 섹션에서 시작하는 단계를 따를 수 있습니다. 배포하고 게시해야 하는 프로젝트(SharingServiceSample)가 이미 있으므로 위 단계에서 설명한 대로 다른 ASP.NET 프로젝트를 만들지 마세요.

---

## <a name="deploy-the-sample-app"></a>샘플 앱 배포

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure에서 ASP.NET Core 웹앱을 배포한 다음, Unity 앱을 구성하고 배포했습니다. 앱으로 Spatial Anchors를 만들고 ASP.NET Core 웹앱을 사용하여 다른 디바이스와 공유했습니다.

Azure Cosmos DB를 사용하여 공유된 Spatial Anchors 식별자의 스토리지를 유지하도록 ASP.NET Core 웹앱을 개선할 수 있습니다. Azure Cosmos DB 지원을 추가하면 오늘 ASP.NET Core 웹앱에서 앵커를 만들고, 며칠 후 돌아와서 웹앱에 저장된 앵커 식별자를 사용하여 다시 찾을 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB를 사용하여 앵커 저장](./tutorial-use-cosmos-db-to-store-anchors.md)

