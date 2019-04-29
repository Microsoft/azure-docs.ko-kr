---
title: 비디오 플레이어 애플리케이션 개발
description: 이 토픽에서는 Media Services의 스트리밍 미디어를 사용할 수 있는 고유한 클라이언트 애플리케이션을 개발하는 데 사용할 수 있는 플레이어 프레임워크 및 플러그 인에 대한 링크를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 55e419fc-4c39-4902-9c62-f41cfcd86c6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b8d4ff3e833dcbe92802845796e3b826735b68ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465646"
---
# <a name="develop-video-player-applications"></a>비디오 플레이어 애플리케이션 개발
## <a name="overview"></a>개요
Azure Media Services는 iOS 장치, Android 장치, Windows, Windows Phone, Xbox 및 셋톱 박스를 포함한 대부분의 플랫폼에서 풍부한 동적 클라이언트 플레이어 애플리케이션을 만드는 데 필요한 도구를 제공합니다. 또한 이 토픽에서는 Azure Media Services의 스트리밍 미디어를 사용할 수 있는 클라이언트 애플리케이션을 개발하는 데 사용할 수 있는 SDK 및 플레이어 프레임워크 링크를 제공합니다.

>[!NOTE]
>AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 
 
## <a name="azure-media-player"></a>Azure Media Player
[Azure Media Player](https://aka.ms/ampinfo)는 다양한 브라우저 및 디바이스의 Microsoft Azure Media Services에서 미디어 콘텐츠를 재생하기 위해 작성된 웹 비디오 플레이어입니다. Azure Media Player는 풍부해진 적응 스트리밍 환경을 제공할 수 있는 HTML5, 미디어 원본 확장(MSE) 및 암호화된 미디어 확장(EME) 등의 업계 표준을 활용합니다. 이러한 표준을 디바이스 또는 브라우저에서 사용할 수 없는 경우, Azure Media Player는 Flash 및 Silverlight를 대체 기술로 사용합니다. 사용된 재생 기술에 관계 없이 개발자는 API에 액세스하기 위한 통합된 JavaScript 인터페이스가 있어야 합니다. 이렇게 하면 Azure Media Services에서 제공하는 콘텐츠를 추가 작업 없이 광범위한 디바이스 및 브라우저에서 재생할 수 있습니다.

Microsoft Azure Media Services에서 컨텐츠를 DASH, 부드러운 스트리밍 및 HLS 스트리밍 형식으로 제공하여 재생할 수 있습니다. Azure Media Player는 이러한 다양한 형식을 고려하여 플랫폼/브라우저 기능에 따라 최상의 링크를 자동으로 재생합니다. Microsoft Azure Media Services에서 PlayReady 암호화 또는 AES 128 비트 봉투 암호화로 자산의 동적 암호화를 사용할 수 있습니다. 적절하게 구성된 경우 Azure Media Player를 사용하여 PlayReady의 및 AES 128 비트 암호화된 콘텐츠를 암호 해독할 수 있습니다. 

자세한 내용은 다음을 참조하세요.

* [Azure Media Player](https://aka.ms/ampinfo)
* [Azure Media Player 서비스 설명서](https://aka.ms/ampdocs) 
* [Azure Media Player 시작 블로그](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)
* [최신 Azure Media Player 관련 최신 정보를 얻으려면 등록](https://aka.ms/ampsignup)
* [새로운 기능 요청, 아이디어, 사용자 의견 추가](https://aka.ms/ampuservoice) 

## <a name="other-tools-for-creating-player-applications"></a>플레이어 애플리케이션을 만들기 위한 다른 도구
또한 다음 SDK 중 하나를 사용할 수도 있습니다.

* [부드러운 스트리밍 클라이언트 SDK](https://www.iis.net/downloads/microsoft/smooth-streaming) 
* [부드러운 스트리밍 Windows 스토어 앱](media-services-build-smooth-streaming-apps.md)
* [Microsoft Media Platform: 플레이어 프레임워크](https://playerframework.codeplex.com/) 
* [HTML5 플레이어 프레임워크 설명서](https://playerframework.codeplex.com/wikipage?title=HTML5%20Player&referringTitle=Documentation) 
* [OSMF용 Microsoft 부드러운 스트리밍 플러그인](https://www.microsoft.com/download/details.aspx?id=36057) 
* [Microsoft® 부드러운 스트리밍 클라이언트 이식 키트 라이선스](https://aka.ms/sspk) 
* [XBOX 비디오 애플리케이션 개발](https://xbox.create.msdn.com/) 

## <a name="advertising"></a>광고
Azure Media Services는 Windows 미디어 플랫폼: 플레이어 프레임워크를 통해 광고 삽입에 대한 지원을 제공합니다. 광고를 지원하는 플레이어 프레임워크는 Windows 8, Silverlight, Windows Phone 8 및 iOS 디바이스에 사용할 수 있습니다. 각 플레이어 프레임워크는 플레이어 애플리케이션을 구현하는 방법을 보여주는 샘플 코드를 포함합니다. 미디어에 삽입할 수 있는 서로 다른 세 종류의 광고가 있습니다.

선형-기본 비디오를 일시 중지하는 전체 프레임 광고

비선형-기본 비디오를 재생할 때 표시되는 오버레이 광고로, 일반적으로 플레이어 내에 배치된 로고나 기타 정적 이미지

동반-플레이어 외부에 표시되는 광고

광고는 기본 비디오 타임 라인에 언제든지 배치할 수 있습니다. 플레이어에 광고를 재생하는 시기 및 재생하는 광고를 알려야 합니다. 이 작업은 표준 XML 기반 파일 집합을 사용하여 수행됩니다. VAST(Video Ad Service Template), VMAP(Digital Video Multiple Ad Playlist), MAST(Media Abstract Sequencing Template) 및 VPAID(Digital Video Player Ad Interface Definition). VAST 파일은 표시할 광고를 지정합니다. VMAP 파일은 다양한 광고를 재생하고 VAST XML을 포함하는 시기를 지정합니다. 또한 MAST 파일은 VAST XML도 포함할 수 있는 광고를 시퀀스하는 다른 방법입니다. VPAID 파일은 비디오 플레이어와 광고 또는 광고 서버 간의 인터페이스를 정의합니다. 자세한 내용은 [광고 삽입](https://msdn.microsoft.com/library/dn387398.aspx)을 참조하세요.

라이브 스트리밍 비디오의 선택 캡션 및 광고 지원에 대한 정보는 [지원되는 선택 캡션 및 광고 삽입 표준](https://msdn.microsoft.com/library/c49e0b4d-357e-4cca-95e5-2288924d1ff3#caption_ad)을 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>관련 항목
[DASH.js를 사용하여 HTML5 애플리케이션에 MPEG-DASH 적응 스트리밍 비디오 포함](media-services-embed-mpeg-dash-in-html5.md)

[GitHub dash.js 리포지토리](https://github.com/Dash-Industry-Forum/dash.js)

