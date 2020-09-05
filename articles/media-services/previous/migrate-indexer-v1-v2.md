---
title: 인덱서 v1 및 v 2에서 Azure Media Services Video Indexer로 마이그레이션 | Microsoft Docs
description: 이 항목에서는 Azure Media Indexer v1 및 v 2에서 Azure Media Services Video Indexer로 마이그레이션하는 방법에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 3da6dd877a94da64f85f093ae98a0b8a109ab2e1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255106"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Media Indexer에서 Media Indexer 2에서 Video Indexer으로 마이그레이션

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[Azure Media Indexer](media-services-index-content.md) media 프로세서 및 [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) 미디어 프로세서를 사용 중지 하 고 있습니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 항목을 참조하세요. [Azure Media Services Video Indexer](../video-indexer/index.yml)는 이러한 레거시 미디어 프로세서를 대체합니다.

Azure Media Services Video Indexer Azure 미디어 분석, Azure Cognitive Search, Cognitive Services (예: Face API, Microsoft Translator, Computer Vision API, Custom Speech Service)를 기반으로 합니다. 이 애플리케이션에서 Video Indexer 비디오 및 오디오 모델을 사용하여 비디오의 인사이트를 추출할 수 있습니다. 에서 사용할 수 Video Indexer 시나리오, 제공 하는 기능 및 시작 하는 방법에 대 한 자세한 내용은 [비디오 및 오디오 모델 Video Indexer](../video-indexer/video-indexer-overview.md)을 참조 하세요. 

[Azure Media Services v3 analyzer 미리 설정을](../latest/analyzing-video-audio-files-concept.md) 사용 하 여 비디오 및 오디오 파일에서 정보를 추출 하거나 [Video Indexer api](https://api-portal.videoindexer.ai/)를 사용 하 여 직접 정보를 추출할 수 있습니다. 현재 Video Indexer Api 및 Media Services v3 Api에서 제공 하는 기능 사이에 중복이 있습니다.

> [!NOTE]
> Video Indexer와 Media Services analyzer 미리 설정을 사용 하려는 경우를 이해 하려면 [비교 문서](../video-indexer/compare-video-indexer-with-media-services-presets.md)를 확인 하세요. 

이 문서에서는 Azure Media Indexer Azure Media Indexer 2에서 Azure Media Services Video Indexer로 마이그레이션하는 단계를 설명 합니다.  

## <a name="migration-options"></a>마이그레이션 옵션 

|필요한 경우  |다음으로 |
|---|---|
|VTT, SRT 또는 TTML의 폐쇄 캡션 파일 형식으로 모든 미디어 파일 형식에 대 한 음성-텍스트 기록을 제공 하는 솔루션입니다.<br/>키워드, 토픽 추론, 음향 이벤트, 스피커 diarization, 엔터티 추출 및 변환과 같은 추가 오디오 통찰력도 있습니다.| Video Indexer v2 REST API 또는 Azure Media Services v3 Audio Analyzer 미리 설정을 통해 Azure Video Indexer 기능을 사용 하도록 응용 프로그램을 업데이트 합니다.|
|음성 텍스트 기능| Cognitive Services Speech API를 직접 사용 합니다.|  

## <a name="getting-started-with-video-indexer"></a>Video Indexer 시작

다음 섹션에서는 관련 링크를 제공 합니다. [Video Indexer 시작 하려면 어떻게 해야 하나요?](../video-indexer/video-indexer-overview.md#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 Api 시작

Azure Media Services v3 API를 사용 하면 [Azure Media Services v3 분석기 사전 설정을](../latest/analyzing-video-audio-files-concept.md)통해 비디오 및 오디오 파일에서 통찰력을 추출할 수 있습니다. 

**AudioAnalyzerPreset**를 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 녹음/출력에 대 한 VTT 또는 TTML 파일과 JSON 파일 (추가 오디오 정보 모두 포함)이 포함 됩니다. 오디오 정보에는 키워드, 스피커 인덱싱 및 음성 감정 분석이 포함 됩니다. AudioAnalyzerPreset는 특정 언어에 대 한 언어 검색도 지원 합니다. 자세한 내용은 [변환](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)을 참조 하세요.

### <a name="get-started"></a>시작하기

시작하려면

* [자습서](../latest/analyze-videos-tutorial-with-api.md)
* AudioAnalyzerPreset 샘플: [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 또는 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 샘플: [JAVA sdk](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 또는 [.net sdk](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services Speech Services 시작

[Azure Cognitive Services](../../cognitive-services/index.yml) 는 응용 프로그램, 도구 또는 장치가 사용 하거나 표시할 수 있는 실시간으로 오디오 스트림을 텍스트로 speech 하는 음성-텍스트 서비스를 제공 합니다. 음성 텍스트를 사용 하 여 [고유한 음향 모델, 언어 모델 또는 발음 모델을 사용자 지정할](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)수 있습니다. 자세한 내용은 [Cognitive Services 음성 텍스트](../../cognitive-services/speech-service/speech-to-text.md)를 참조 하세요. 

> [!NOTE] 
> 음성 텍스트 서비스는 비디오 파일 형식을 사용 하지 않으며 [특정 오디오 형식만](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)사용 합니다. 

텍스트 음성 변환 서비스 및 시작 하는 방법에 대 한 자세한 내용은 음성-텍스트 란 [?](../../cognitive-services/speech-service/speech-to-text.md) 을 참조 하세요.

## <a name="known-differences-from-deprecated-services"></a>사용 되지 않는 서비스의 알려진 차이점 

Video Indexer, Azure Media Services v3 AudioAnalyzerPreset 및 Cognitive Services Speech Services 서비스를 사용 하는 것이 더 안정적 이며 사용 중지 된 Azure Media Indexer 1 및 Azure Media Indexer 2 프로세서 보다 우수한 품질의 출력을 생성 합니다.  

몇 가지 알려진 차이점은 다음과 같습니다. 

* Cognitive Services Speech Services는 키워드 추출을 지원 하지 않습니다. 그러나 Video Indexer 및 Media Services v3 AudioAnalyzerPreset는 JSON 파일 형식에서 보다 강력한 키워드 집합을 제공 합니다. 

## <a name="need-help"></a>도움 필요 시

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
