---
title: '자습서: Azure Cosmos DB와 앵커 공유'
description: 이 자습서에서는 백 엔드 서비스와 Azure Cosmos DB가 있는 Unity의 Android/iOS 디바이스 간에 Azure Spatial Anchors 식별자를 공유하는 방법을 알아봅니다.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 07/31/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 8ff431c27dd53e82f9003b658c82ceb3efb5d320
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810023"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>자습서: Azure Cosmos DB 백 엔드를 사용하여 세션 및 디바이스에서 Azure Spatial Anchors 공유

이 자습서는 [세션 및 디바이스에서 Azure Spatial Anchors 공유](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md)의 연속입니다. 세션과 디바이스 간에 Azure 공간 앵커를 공유하는 동시에 Azure Cosmos DB를 백 엔드 스토리지로 사용할 수 있도록 몇 가지 기능을 추가하는 프로세스를 안내합니다.

![개체 지속성을 보여주는 GIF](./media/persistence.gif)

이 자습서에서는 Unity와 Azure Cosmos DB를 사용하지만, 디바이스 간에 Spatial Anchor 식별자를 공유하는 방법을 보여드리는 것이 그 목적입니다. 다른 언어와 백 엔드 기술을 사용하여 동일한 목표를 달성할 수 있습니다.

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

이전에 만든 리소스 그룹에 Azure Cosmos 데이터베이스를 추가합니다.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

`Connection String`을 복사합니다. 이후에 필요합니다.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>SharingService 파일을 약간 변경합니다.

**솔루션 탐색기**에서 `SharingService\Startup.cs` 파일을 엽니다.

파일의 맨 위에서 `#define INMEMORY_DEMO` 줄을 찾아 주석 처리합니다. 파일을 저장합니다.

**솔루션 탐색기**에서 `SharingService\appsettings.json` 파일을 엽니다.

`StorageConnectionString` 속성을 찾은 다음, [데이터베이스 계정 만들기 단계](#create-a-database-account)에서 복사한 `Connection String` 값으로 설정합니다. 파일을 저장합니다.

공유 서비스를 다시 게시하고 샘플 앱을 실행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cosmos DB를 사용하여 디바이스 간에 앵커 식별자를 공유했습니다. 새 Unity HoloLens 앱에서 Azure Spatial Anchors를 사용하는 방법을 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [새 Unity HoloLens 앱 시작](./tutorial-new-unity-hololens-app.md)
