---
title: 클라우드 폴더에서 콘텐츠 동기화 - Azure App Service
description: 클라우드 폴더에서 콘텐츠 동기화를 통해 Azure App Service에 앱을 배포하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 88d3a670-303a-4fa2-9de9-715cc904acec
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: 65f372196671e95f7d9af7f47011e9ca1f9de316
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60769653"
---
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>클라우드 폴더에서 Azure App Service로 콘텐츠 동기화
이 문서에서는 Dropbox 및 OneDrive에서 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)로 사용자 콘텐츠를 동기화하는 방법을 보여 줍니다. 

주문형 콘텐츠 동기화 배포는 App Service [Kudu 배포 엔진](https://github.com/projectkudu/kudu/wiki)에서 제공됩니다. 지정된 클라우드 폴더의 콘텐츠 및 사용자 앱 코드로 작업할 수 있습니다. 그런 다음, 단추를 클릭하여 App Service로 동기화할 수 있습니다. 콘텐츠 동기화는 Kudu 빌드 서버를 사용합니다. 

## <a name="enable-content-sync-deployment"></a>콘텐츠 동기화 배포를 사용하도록 설정

콘텐츠 동기화를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **OneDrive** 또는 **Dropbox** > **인증**을 클릭합니다. 표시되는 인증 메시지를 따릅니다. 

![](media/app-service-deploy-content-sync/choose-source.png)

한 번 OneDrive 또는 Dropbox로 인증해야 합니다. 이미 인증된 경우 **계속**을 클릭합니다. **계정 변경**을 클릭하여 인증된 OneDrive 또는 Dropbox 계정을 변경할 수 있습니다.

![](media/app-service-deploy-content-sync/continue.png)

**구성** 페이지에서 동기화할 폴더를 선택합니다. 이 폴더는 OneDrive 또는 Dropbox에서 다음과 같은 지정된 콘텐츠 경로 아래에 생성됩니다. 
   
* **OneDrive**: `Apps\Azure Web Apps`
* **Dropbox**: `Apps\Azure`

작업을 마쳤으면 **계속**을 클릭합니다.

**요약** 페이지에서 옵션을 확인하고 **마침**을 클릭합니다.

## <a name="synchronize-content"></a>콘텐츠 동기화

클라우드 폴더에 있는 콘텐츠를 App Service와 동기화하려는 경우 **배포 센터** 페이지로 돌아가서 **동기화**를 클릭합니다.

![](media/app-service-deploy-content-sync/synchronize.png)
   
   > [!NOTE]
   > API의 기본적인 차이 때문에 **비즈니스용 OneDrive**는 지원되지 않습니다. 
   > 
   > 

## <a name="disable-content-sync-deployment"></a>콘텐츠 동기화 배포를 사용하지 않도록 설정

콘텐츠 동기화를 사용하지 않도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **연결 끊기**를 클릭합니다.

![](media/app-service-deploy-content-sync/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 Git 리포지토리에서 배포](deploy-local-git.md)
