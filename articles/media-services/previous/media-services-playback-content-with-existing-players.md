---
title: 기존 플레이어를 사용하여 콘텐츠 재생 - Azure | Microsoft Docs
description: 이 문서에서는 콘텐츠를 재생 하는 데 사용할 수 있는 기존 플레이어를 나열 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: bd09b734f1ac5ac3c98b6c0c717a48de19b0106f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89261073"
---
# <a name="playing-your-content-with-existing-players"></a>기존 플레이어를 사용하여 콘텐츠 재생

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Services는 부드러운 스트리밍, HTTP 라이브 스트리밍 및 Mpeg-dash와 같은 여러 인기 있는 스트리밍 형식을 지원합니다. 이 항목에는 스트림을 테스트하는 데 사용할 수 있는 기존 플레이어가 나와 있습니다.

### <a name="the-azure-portal-media-services-content-player"></a>Azure Portal Media Services 콘텐츠 플레이어
**Azure** portal은 비디오를 테스트 하는 데 사용할 수 있는 콘텐츠 플레이어를 제공 합니다.

원하는 비디오( [게시된](media-services-portal-publish.md)것이어야 함)를 클릭하고 포털 맨 아래에 있는 **재생** 단추를 클릭합니다.

다음과 같은 몇 가지 고려 사항이 적용됩니다.

* **MEDIA SERVICES CONTENT PLAYER** 가 기본 스트리밍 엔드포인트에서 재생됩니다. 기본이 아닌 스트리밍 엔드포인트에서 재생하려면 다른 플레이어를 사용합니다. 예를 들어 [Azure Media Player](https://aka.ms/azuremediaplayer)를 사용합니다.

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player

[Azure Media Player](https://aka.ms/azuremediaplayer)를 사용하여 다음 형식 중 하나로 콘텐츠(일반 또는 보호됨)를 재생합니다.

* 부드러운 스트리밍
* MPEG DASH
* HLS
* 프로그레시브 MP4

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady 토큰

[https://sltoken.azurewebsites.net](https://sltoken.azurewebsites.net)

### <a name="dash-players"></a>DASH 플레이어

[https://dashplayer.azurewebsites.net](https://dashplayer.azurewebsites.net)

[https://dashif.org](https://dashif.org)

### <a name="other"></a>기타
다음을 이용하여 HLS URL을 테스트할 수도 있습니다.

* iOS 디바이스에서 **Safari** 또는
* **3ivx HLS 플레이어**

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
