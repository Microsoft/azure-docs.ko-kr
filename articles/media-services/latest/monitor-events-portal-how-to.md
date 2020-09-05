---
title: 포털에서 Event Grid를 사용 하 여 Azure Media Services 이벤트 모니터링
description: 이 문서에서는 Azure Media Services 이벤트를 모니터링하기 위해 Event Grid를 구독하는 방법을 설명합니다.
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 26e6e56d7c14cfa0ab54776003ae4489bb254094
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265850"
---
# <a name="create-and-monitor-media-services-events-with-event-grid-using-the-azure-portal"></a>Azure Portal을 사용하여 Event Grid에서 Media Services 이벤트 만들기 및 모니터링

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 서비스는 [이벤트 구독](../../event-grid/concepts.md#event-subscriptions) 을 사용 하 여 이벤트 메시지를 구독자로 라우팅합니다. Media Services 이벤트에는 데이터 변경에 대응하는 데 필요한 모든 정보가 포함되어 있습니다. Media Services 이벤트는 eventType 속성이 "Microsoft.Media"로 시작하는 것으로 식별할 수 있습니다. 자세한 내용은 [Media Services 이벤트 스키마](media-services-event-schemas.md)를 참조하세요.

이 문서에서는 Azure Portal을 사용하여 Azure Media Services 계정에 대한 이벤트를 구독합니다. 그런 다음, 이벤트를 트리거하여 결과를 봅니다. 일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 이 문서에서는 메시지를 수집 하 고 표시 하는 웹 앱으로 이벤트를 보냅니다.

작업을 완료하면 이벤트 데이터가 웹앱에 보내진 것을 확인할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소 

* 활성 Azure 구독.
* [이 빠른 시작](./create-account-howto.md)에서 설명된 대로 새로운 Azure Media Services 계정을 만듭니다.

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

Media Services 계정에 대한 이벤트를 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 문서에서는 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

1. **Azure에 배포**를 선택하여 구독에 솔루션을 배포합니다. Azure Portal에서 매개 변수에 대한 값을 제공합니다.

   [!["Azure에 배포"라는 레이블이 지정된 단추를 보여주는 이미지](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)

1. 배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

"Azure Event Grid 뷰어" 사이트로 전환하더라도 이벤트가 아직 표시되지 않습니다.
   
[!INCLUDE [event-grid-register-provider-portal.md](../../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-media-services-events"></a>Media Services 이벤트 구독

항목을 구독하여 Event Grid에 추적하려는 이벤트와 이벤트를 보낼 위치를 알립니다.

1. 포털에서 Media Services 계정을 선택하고 **이벤트**를 선택합니다.
1. 이벤트 뷰어 앱에 이벤트를 보내려면 엔드포인트에 대한 웹 후크를 사용합니다. 

   ![웹 후크 선택](./media/monitor-events-portal/select-web-hook.png)

1. 이벤트 구독은 Media Services 계정에 대한 값으로 미리 채워집니다. 
1. **끝점 형식**에 대해 ' 웹 후크 '를 선택 합니다.
1. 이 항목에서는 **모든 이벤트 유형 구독**을 선택한 상태로 둡니다. 그러나 이 옵션을 선택 취소하고 특정 이벤트 유형을 필터링할 수 있습니다. 
1. **엔드포인트 선택** 링크를 클릭합니다.

    웹 후크 엔드포인트의 경우 웹앱의 URL을 제공하고 `api/updates`를 홈 페이지 URL에 추가합니다. 

1. **선택 확인**을 누릅니다.
1. **만들기**를 누릅니다.
1. 구독에 이름을 지정합니다.

   ![로그 선택](./media/monitor-events-portal/create-subscription.png)

1. 웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 

    Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 엔드포인트에서 `validationResponse`를 `validationCode`로 설정해야 합니다. 자세한 내용은 [Event Grid 보안 및 인증](../../event-grid/security-authentication.md)을 참조하세요. 웹앱 코드를 보고 구독의 유효성을 검사하는 방법을 확인할 수 있습니다.

이제 이벤트를 트리거하여 Event Grid에서 메시지를 사용자 엔드포인트에 배포하는 방법을 살펴보겠습니다.

## <a name="send-an-event-to-your-endpoint"></a>엔드포인트에 이벤트 보내기

인코딩 작업을 실행하여 Media Services 계정에 대한 이벤트를 트리거할 수 있습니다. [이 빠른 시작](stream-files-dotnet-quickstart.md)에 따라 파일을 인코딩하고 이벤트 보내기를 시작할 수 있습니다. 모든 이벤트를 구독한 경우, 다음과 유사한 화면이 표시됩니다.

> [!TIP]
> 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. 모든 이벤트를 보려면 페이지를 새로 고치지 마세요.

![구독 이벤트 보기](./media/monitor-events-portal/view-subscription-event.png)

## <a name="next-steps"></a>다음 단계

[업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
