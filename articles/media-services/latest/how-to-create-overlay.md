---
title: Media Encoder Standard를 사용 하 여 오버레이를 만드는 방법
description: Media Encoder Standard를 사용 하 여 오버레이를 만드는 방법에 대해 알아봅니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 6c93408bce8da9f8cd0e4a0d0bab615e2bd362dc
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267329"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Media Encoder Standard를 사용 하 여 오버레이를 만드는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

미디어 인코더 표준을 사용하면 이미지를 기존 비디오에 오버레이할 수 있습니다. 현재 png, jpg, gif 및 bmp 형식이 지원됩니다.

## <a name="prerequisites"></a>필수 구성 요소

* 샘플에서 파일 * 에appsettings.js* 를 구성 하는 데 필요한 계정 정보를 수집 합니다. 이 작업을 수행 하는 방법을 잘 모르는 경우 [빠른 시작: Microsoft id 플랫폼을 사용 하 여 응용 프로그램 등록](../../active-directory/develop/quickstart-register-app.md)을 참조 하세요. 파일의 *appsettings.js* 에는 다음 값이 필요 합니다.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Region": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

변환을 아직 잘 모르는 경우 다음 작업을 완료 하는 것이 좋습니다.

* [Media Services를 사용 하 여 비디오 및 오디오 인코딩](encoding-concept.md) 읽기
* [사용자 지정 변환-.net을 사용 하 여 인코딩하는 방법](customize-encoder-presets-how-to.md)을 참조 하세요. 이 문서의 단계에 따라 변환 작업에 필요한 .NET을 설정 하 고 여기로 돌아와서 오버레이 미리 설정 샘플을 사용해 보세요.
* [참조 변환 문서](/rest/api/media/transforms)를 참조 하세요.

변형에 대해 잘 알고 있으면 오버레이 샘플을 다운로드 합니다.

## <a name="overlays-preset-sample"></a>오버레이 미리 설정 샘플

오버레이를 시작 하려면 [미디어 서비스 오버레이 샘플](https://github.com/Azure-Samples/media-services-overlays) 을 다운로드 하세요.

## <a name="next-steps"></a>다음 단계

* [Media Services-.NET을 사용 하 여 인코딩할 경우 비디오 하위 클립](subclip-video-dotnet-howto.md)