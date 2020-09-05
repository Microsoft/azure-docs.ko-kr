---
title: Azure Media Player를 사용하여 재생 - Azure | Microsoft Docs
description: Azure Media Player는 다양한 브라우저 및 디바이스의 Microsoft Azure Media Services에서 미디어 콘텐츠를 재생하기 위해 작성된 웹 비디오 플레이어입니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2019
ms.author: inhenkel
ms.openlocfilehash: a3b32110a7236d47946c3a1f901aaedf51b47775
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89253984"
---
# <a name="playback-with-azure-media-player"></a>Azure Media Player 재생

Azure Media Player는 다양한 브라우저 및 디바이스의 Microsoft Azure Media Services에서 미디어 콘텐츠를 재생하기 위해 작성된 웹 비디오 플레이어입니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, 미디어 원본 확장(MSE) 및 암호화된 미디어 확장(EME) 등의 업계 표준을 활용합니다. 이러한 표준을 디바이스 또는 브라우저에서 사용할 수 없는 경우, Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용합니다. 사용된 재생 기술에 관계 없이 개발자는 API에 액세스하기 위한 통합된 JavaScript 인터페이스가 있어야 합니다. 이렇게 하면 Azure Media Services에서 제공하는 콘텐츠를 추가 작업 없이 광범위한 디바이스 및 브라우저에서 재생할 수 있습니다.

Microsoft Azure Media Services에서 콘텐츠를 HLS, DASH, 부드러운 스트리밍 형식으로 제공하여 재생할 수 있습니다. Azure Media Player는 이러한 다양한 형식을 고려하여 플랫폼/브라우저 기능에 따라 최상의 링크를 자동으로 재생합니다. Media Services에서 PlayReady 암호화 또는 AES 128비트 봉투 암호화로 자산의 동적 암호화를 사용할 수 있습니다. 적절하게 구성된 경우 Azure Media Player를 사용하여 PlayReady의 및 AES 128 비트 암호화된 콘텐츠를 암호 해독할 수 있습니다.

> [!NOTE]
> 암호화 된 콘텐츠를 위해 HTTPS 재생이 필요 합니다.

## <a name="use-azure-media-player-demo-page"></a>Azure Media Player 데모 페이지 사용

### <a name="start-using"></a>사용 시작

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer)를 사용하여 Azure Media Services 샘플 또는 고유한 스트림을 재생할 수 있습니다.  

새 비디오를 재생하려면 다른 URL을 붙여넣고 **업데이트** 키를 누릅니다.

다양한 재생 옵션(예: 기술, 언어 또는 암호화)을 구성하려면 **고급 옵션**을 누릅니다.

![Azure Media Player](./media/azure-media-player/home-page.png)

### <a name="monitor-diagnostics-of-a-video-stream"></a>비디오 스트림의 진단 모니터링

[Azure Media Player 데모 페이지](https://aka.ms/azuremediaplayer)를 사용하여 비디오 스트림의 진단을 모니터링할 수 있습니다.

![Azure Media Player 진단](./media/azure-media-player/diagnostics.png)

## <a name="set-up-azure-media-player-in-your-html"></a>HTML에서 Azure Media Player 설정

Azure Media Player는 설정하기 쉽습니다. Media Services 계정에서 미디어 콘텐츠의 기본 재생을 활용하려면 몇 분 정도 걸립니다. [Azure Media Player 설명서](../azure-media-player/azure-media-player-overview.md)에서 Azure Media Player를 설정 및 구성하는 방법에 대한 자세한 내용을 참조하세요.

## <a name="additional-notes"></a>추가적인 참고 사항

* Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* [Azure Media Player 설명서](../azure-media-player/azure-media-player-overview.md)
* [Azure Media Player 샘플](https://github.com/Azure-Samples/azure-media-player-samples)