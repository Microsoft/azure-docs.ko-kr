---
title: Azure Media Services v3의 동적 패키징
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services의 동적 패키징에 대해 간략하게 설명합니다.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: dfa87921bc6a5a6c34b4dec33f4aae1907507730
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291622"
---
# <a name="dynamic-packaging-in-media-services-v3"></a>Media Services v3의 동적 패키징

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Microsoft Azure Media Services를 사용하여 많은 미디어 원본 파일 형식을 인코딩할 수 있습니다. 모든 주요 디바이스(예: iOS 및 Android 디바이스)에 연결되는 콘텐츠 보호를 사용하거나 사용하지 않는 다양한 스트리밍 프로토콜을 통해 이러한 기능을 제공합니다. 이러한 클라이언트는 여러 다양한 프로토콜을 이해합니다. 예를 들어, iOS는 HLS(HTTP 라이브 스트리밍) 형식으로 스트림이 배달되어야 하 고 Android 디바이스는 HLS와 MPEG DASH를 모두 지원합니다.

Media Services에서 [스트리밍 엔드포인트](streaming-endpoint-concept.md)(원본)는 라이브 및 주문형 콘텐츠를 클라이언트 플레이어 앱에 직접 배달할 수 있는 원본 서비스와 동적(적시) 패키징을 나타냅니다. 또한 다음 섹션에 설명된 일반적인 스트리밍 미디어 프로토콜중 하나를 사용합니다. *동적 패키징*은 모든 스트리밍 엔드포인트(표준 또는 프리미엄)에 표준으로 제공되는 기능입니다.

> [!NOTE]
> [Azure Portal](https://portal.azure.com/)을 사용하여 v3 [라이브 이벤트](live-events-outputs-concept.md)를 관리하고, v3 [자산](assets-concept.md)을 보고, API 액세스에 대한 정보를 가져올 수 있습니다. 다른 모든 관리 작업(예제: 변환 및 작업)의 경우 [REST API](/rest/api/media/), [CLI](https://aka.ms/ams-v3-cli-ref) 또는 지원되는 [SDK](media-services-apis-overview.md#sdks) 중 하나를 사용합니다.

## <a name="to-prepare-your-source-files-for-delivery"></a>전송할 원본 파일 준비

동적 패키징을 활용하려면 mezzanine(원본) 파일을 여러 개의 비트 전송률 MP4(ISO 기본 미디어 14496-12) 파일 세트로 [인코딩](encoding-concept.md)해야 합니다. Media Services 동적 패키징에 필요한 인코딩된 MP4 및 스트리밍 구성 파일이 포함된 [자산](assets-concept.md)이 있어야 합니다. 이 MP4 파일 세트에서 동적 패키징을 사용하여 아래에 설명된 스트리밍 미디어 프로토콜을 통해 비디오를 전송할 수 있습니다.

Azure Media Services 동적 패키징은 MP4 컨테이너 형식의 비디오 및 오디오 파일만 지원합니다. Dolby 같은 대체 코덱을 사용할 때도 오디오 파일을 MP4 컨테이너로 인코딩해야 합니다.  

> [!TIP]
> MP4 및 스트리밍 구성 파일을 가져오는 한 가지 방법은 [Media Services를 사용하여 mezzanine 파일을 인코딩](#encode-to-adaptive-bitrate-mp4s)하는 것입니다. 

인코딩된 자산의 비디오를 클라이언트에서 재생할 수 있게 하려면 [스트리밍 로케이터](streaming-locators-concept.md)를 만들고 스트리밍 URL을 작성해야 합니다. 그런 다음, 스트리밍 클라이언트 매니페스트에 지정된 형식에 따라(HLS, MPEG DASH 또는 부드러운 스트리밍) 선택한 프로토콜로 스트림을 받게 됩니다.

따라서 사용자는 단일 스토리지 형식으로 파일을 저장하고 해당 파일에 대한 요금을 지불하기만 하면 되며, Media Services 서비스에서 클라이언트의 요청에 따라 적절한 응답을 작성하고 제공합니다.

Media Services 동적 암호화를 사용하여 콘텐츠를 보호하려는 경우 [스트리밍 프로토콜 및 암호화 유형](content-protection-overview.md#streaming-protocols-and-encryption-types)을 참조하세요.

### <a name="hls-protocol"></a>HLS 프로토콜

스트리밍 클라이언트는 다음과 같은 HLS 형식을 지정할 수 있습니다.

|프로토콜|예제|
|---|---|
|HLS V4 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`||
|HLS V3 |`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`||
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`||

### <a name="mpeg-dash-protocol"></a>MPEG-DASH 프로토콜

스트리밍 클라이언트는 다음과 같은 MPEG-DASH 형식을 지정할 수 있습니다.

|프로토콜|예제|
|---|---|
|MPEG-DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` ||
|MPEG-DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` ||

### <a name="smooth-streaming-protocol"></a>부드러운 스트리밍 프로토콜

스트리밍 클라이언트는 다음과 같은 부드러운 스트리밍 형식을 지정할 수 있습니다.

|프로토콜|참고/예제| 
|---|---|
|부드러운 스트리밍| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`||
|부드러운 스트리밍 2.0(레거시 매니페스트)|기본적으로 부드러운 스트리밍 매니페스트 형식에는 반복 태그(r 태그)가 포함됩니다. 그러나 일부 플레이어는 `r-tag`를 지원하지 않습니다. 이러한 플레이어를 사용하는 클라이언트는 r 태그를 사용하지 않도록 설정하는 형식을 사용할 수 있습니다.<br/><br/>`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=fmp4-v20)`|

> [!NOTE]
> 부드러운 스트리밍을 위해서는 오디오와 비디오가 모두 스트림에 있어야 합니다.

## <a name="on-demand-streaming-workflow"></a>주문형 스트리밍 워크플로

다음 단계에서는 Azure Media Services의 표준 인코더와 함께 동적 패키징이 사용되는 일반적인 Media Services 스트리밍 워크플로를 보여 줍니다.

1. MP4, QuickTime/MOV 또는 기타 지원되는 파일 형식과 같은 [입력 파일을 업로드](job-input-from-http-how-to.md)합니다. 이 파일을 mezzanine 또는 원본 파일이라고도 합니다. 지원되는 형식의 목록은 [표준 인코더에서 지원하는 형식](media-encoder-standard-formats.md)을 참조하세요.
1. Mezzanine 파일을 H.264/AAC MP4 적응 비트 전송률 세트로 [인코딩](#encode-to-adaptive-bitrate-mp4s)합니다.

    인코딩된 파일이 이미 있고, 파일을 복사하고, 스트리밍하는 경우 다음을 사용합니다. [CopyVideo](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#copyvideo) 및 [CopyAudio](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#copyaudio) API. 결과적으로 스트리밍 매니페스트(.ism 파일)가 포함된 새 MP4 파일이 생성됩니다.
1. 적응 비트 전송률 MP4 세트가 포함된 출력 자산을 게시합니다. [스트리밍 로케이터](streaming-locators-concept.md)를 만들어 게시합니다.
1. 다양한 형식(HLS, MPEG-DASH 및 부드러운 스트리밍)을 대상으로 하는 URL을 작성합니다. *스트리밍 엔드포인트*는 다양한 형식에 대한 정확한 매니페스트 및 요청의 서비스를 담당합니다.
    
다음 다이어그램은 동적 패키징 워크플로를 사용한 주문형 스트리밍을 보여 줍니다.

![패키징을 사용한 주문형 스트리밍의 워크플로 다이어그램](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

다운로드 경로는 위의 그림에 나와 있는 것처럼 *스트리밍 엔드포인트*(원본)를 통해 MP4 파일을 직접 다운로드할 수 있음을 보여 줍니다. 스트리밍 로케이터에서 다운로드할 수 있는 [스트리밍 정책](streaming-policy-concept.md)을 지정합니다.<br/>동적 패키지 작성 도구에서 파일을 변경하지 않습니다. *스트리밍 엔드포인트*(원본) 기능을 무시하려는 경우 원한다면 Azure BLOB 스토리지 API를 사용해 MP4에 직접 액세스하여 점진적으로 다운로드할 수 있습니다. 

### <a name="encode-to-adaptive-bitrate-mp4s"></a>적응 비트 전송률 MP4를 인코딩합니다.

다음 문서에서는 [Media Services를 사용하여 비디오를 인코딩하는 방법](encoding-concept.md)의 예제를 보여 줍니다.

* [기본 제공 사전 설정을 사용하여 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정 빌드](customize-encoder-presets-how-to.md)

표준 인코더 [형식 및 코덱](media-encoder-standard-formats.md) 목록을 참조하세요.

## <a name="live-streaming-workflow"></a>라이브 스트리밍 워크플로

라이브 이벤트는 *통과*(온-프레미스 라이브 인코더가 다중 비트 전송률 스트림을 전송) 또는 *라이브 인코딩*(온-프레미스 라이브 인코더가 단일 비트 전송률 스트림을 전송)으로 설정할 수 있습니다. 

*동적 패키징*을 사용한 라이브 스트리밍의 일반적인 워크플로는 다음과 같습니다.

1. [라이브 이벤트](live-events-outputs-concept.md)를 만듭니다.
1. 수집 URL을 가져오고, URL을 사용하여 기여 피드를 보내도록 온-프레미스 인코더를 구성합니다.
1. 미리 보기 URL을 가져와서 인코더의 입력이 수신되고 있는지 확인하는 데 사용합니다.
1. 새 자산을 만듭니다.
1. 라이브 출력을 만들고 만든 자산 이름을 사용합니다.<br />라이브 출력은 스트림을 자산에 보관합니다.
1. 스트리밍 정책 유형이 기본 제공된 스트리밍 로케이터를 만듭니다.<br />콘텐츠를 암호화하려면 [콘텐츠 보호 개요](content-protection-overview.md)를 검토하세요.
1. 스트리밍 로케이터에서 경로를 나열하여 사용할 URL을 가져옵니다.
1. 스트림을 내보낼 스트리밍 엔드포인트의 호스트 이름을 가져옵니다.
1. 다양한 형식(HLS, MPEG-DASH 및 부드러운 스트리밍)을 대상으로 하는 URL을 작성합니다. *스트리밍 엔드포인트*는 다양한 형식에 대한 정확한 매니페스트 및 요청의 서비스를 담당합니다.

이 다이어그램은 *동적 패키징*을 사용한 라이브 스트리밍의 워크플로를 보여 줍니다.

![동적 패키징을 사용한 통과 인코딩의 워크플로 다이어그램](./media/live-streaming/pass-through.svg)

Media Services v3의 라이브 스트리밍에 대 한 정보는 [라이브 스트리밍 개요](live-streaming-overview.md)를 참조하세요.

## <a name="video-codecs-supported-by-dynamic-packaging"></a>동적 패키징으로 지원되는 비디오 코덱

동적 패키징은 MP4 컨테이너 파일 형식이며 [H. 264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC)(MPEG-4 AVC 또는 AVC1) 또는 [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding)(HEVC, hev1 또는 hvc1)로 인코딩된 비디오를 포함하고 있는 비디오 파일을 지원합니다.

> [!NOTE]
> 최대 4K의 해상도와 최대 60 프레임/초의 프레임 속도는 *동적 패키징*을 통해 테스트되었습니다. [프리미엄 인코더](../previous/media-services-encode-asset.md#media-encoder-premium-workflow)는 레거시 v2 API를 통해 H.265로의 인코딩을 지원합니다.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>동적 패키징으로 지원되는 오디오 코덱

동적 패키징은 MP4 파일 컨테이너 형식으로 저장되고 다음 코덱 중 하나로 인코딩된 오디오 스트림을 포함하고 있는 오디오 파일도 지원합니다.

* [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)(AAC-LC, HE-AAC v1 또는 HE-AAC v2) 
* [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(향상된 AC-3 또는 E-AC3)  동적 패키징을 사용하려면 인코딩된 오디오를 MP4 컨테이너 형식으로 저장해야 합니다.
* Dolby Atmos

   Dolby Atmos 콘텐츠 스트리밍은 CSF(Common Streaming Format) 또는 CMAF(Common Media Application Format) 조각화된 MP4 및 CMAF와의 HLS(HTTP Live Streaming)를 통해 MPEG-DASH 프로토콜과 같은 표준에서 지원됩니다.
* [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29)<br />
   DASH CSF, CMAF DASH, HLS-M2TS 및 HLS-CMAF 패키징 형식에서 지원되는 DTS 코덱은 다음과 같습니다.  

    * DTS 디지털 서라운드(dtsc)
    * DTS-HD 고해상도 및 DTS-HD 마스터 오디오(dtsh)
    * DTS Express(dtse)
    * DTS-HD 무손실(코어 없음) (dtsl)

동적 패키징은 여러 개의 코덱과 언어가 있는 여러 오디오 트랙을 포함하는 스트리밍 자산에 대해 DASH 또는 HLS(버전 4 이상)에서 여러 오디오 트랙을 지원합니다.

위의 모든 오디오 코덱과 관련하여, 동적 패키징을 사용하려면 인코딩된 오디오를 MP4 컨테이너 형식으로 저장해야 합니다. 이 서비스는 BLOB 스토리지에서 원시 기본 스트림 파일 형식을 지원하지 않습니다. 예를 들어 .dts, .ac3 등은 지원되지 않습니다. 

.mp4a 확장명이 .mp4인 파일만 오디오 패키징을 지원합니다. 

### <a name="limitations"></a>제한 사항

#### <a name="ios-limitation-on-aac-51-audio"></a>AAC 5.1 오디오의 iOS 제한 사항

Apple iOS 디바이스는 5.1 AAC 오디오 코덱을 지원하지 않습니다. 다중 채널 오디오는 Dolby Digital 또는 Dolby Digital Plus 코덱을 사용하여 인코딩해야 합니다.

자세한 내용은 [apple 디바이스에 대한 HLS 제작 사양](https://developer.apple.com/documentation/http_live_streaming/hls_authoring_specification_for_apple_devices)을 참조하세요.

> [!NOTE]
> Media Services는 Dolby Atmos 멀티 채널 오디오 형식으로 Dolby Digital, Dolby Digital Plus 또는 Dolby Digital Plus의 인코딩을 지원하지 않습니다.

#### <a name="dolby-digital-audio"></a>Dolby Digital 오디오

Media Services 동적 패키징은 현재 [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital)(AC3) 오디오를 포함하는 파일을 지원하지 않습니다. 이는 Dolby에서 레거시 코덱으로 간주되기 때문입니다.

## <a name="manifests"></a>매니페스트

Media Services *동적 패키징*에서 HLS, MPEG-DASH 및 부드러운 스트리밍에 대한 스트리밍 클라이언트 매니페스트는 URL의 형식 선택기에 따라 동적으로 생성됩니다.  

매니페스트 파일에는 트랙 유형(오디오, 비디오 또는 텍스트), 트랙 이름, 시작 및 종료 시간, 비트 전송률(품질), 트랙 언어, 프레젠테이션 창(고정 기간의 슬라이딩 창), 비디오 코덱(FourCC) 등의 스트리밍 메타 데이터가 포함됩니다. 또한 다음으로 재생할 수 있는 비디오 조각 및 위치에 대한 정보를 제공하여 다음 조각을 검색하도록 플레이어에 지시합니다. 조각(또는 세그먼트)은 비디오 콘텐츠의 실제 “청크”입니다.

### <a name="examples"></a>예제

#### <a name="hls"></a>HLS

HLS 마스터 재생 목록이라고도 하는 HLS 매니페스트 파일의 예제는 다음과 같습니다. 

```
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(381048)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536608,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(381048)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721495)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884544,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721495)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1154816)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327398,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1154816)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2217354)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2413312,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2217354)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579827)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805760,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579827)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

#### <a name="mpeg-dash"></a>MPEG-DASH

MPD(MPEG-DASH Media Presentation Description)라고도 하는 MPEG-DASH 매니페스트 파일의 예제는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT1M10.315S" minBufferTime="PT7S">
   <Period>
      <AdaptationSet id="1" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(aac_eng_2_128041_2_1=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60160000" r="10" />
               <S d="41386666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="5_A_aac_eng_2_128041_2_1_1" bandwidth="128041" audioSamplingRate="48000" />
      </AdaptationSet>
      <AdaptationSet id="2" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
         <SegmentTemplate timescale="10000000" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
            <SegmentTimeline>
               <S d="60060000" r="10" />
               <S d="42375666" />
            </SegmentTimeline>
         </SegmentTemplate>
         <Representation id="1_V_video_1" bandwidth="3579827" width="1280" height="720" />
         <Representation id="1_V_video_2" bandwidth="2217354" codecs="avc1.64001F" width="960" height="540" />
         <Representation id="1_V_video_3" bandwidth="1154816" codecs="avc1.64001E" width="640" height="360" />
         <Representation id="1_V_video_4" bandwidth="721495" codecs="avc1.640015" width="480" height="270" />
         <Representation id="1_V_video_5" bandwidth="381048" codecs="avc1.64000D" width="320" height="180" />
      </AdaptationSet>
   </Period>
</MPD>
```
#### <a name="smooth-streaming"></a>부드러운 스트리밍

부드러운 스트리밍 매니페스트 파일의 예제는 다음과 같습니다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="703146666" TimeScale="10000000">
   <StreamIndex Chunks="12" Type="audio" Url="QualityLevels({bitrate})/Fragments(aac_eng_2_128041_2_1={start time})" QualityLevels="1" Language="eng" Name="aac_eng_2_128041_2_1">
      <QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="128041" FourCC="AACL" CodecPrivateData="1190" Channels="2" PacketSize="4" SamplingRate="48000" />
      <c t="0" d="60160000" r="11" />
      <c d="41386666" />
   </StreamIndex>
   <StreamIndex Chunks="12" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="5">
      <QualityLevel Index="0" Bitrate="3579827" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="0000000167640020ACD9405005BB011000003E90000EA600F18319600000000168EBECB22C" />
      <QualityLevel Index="1" Bitrate="2217354" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FACD940F0117EF01100000303E90000EA600F1831960000000168EBECB22C" />
      <QualityLevel Index="2" Bitrate="1154816" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EACD940A02FF9701100000303E90000EA600F162D960000000168EBECB22C" />
      <QualityLevel Index="3" Bitrate="721495" FourCC="H264" MaxWidth="480" MaxHeight="270" CodecPrivateData="0000000167640015ACD941E08FEB011000003E90000EA600F162D9600000000168EBECB22C" />
      <QualityLevel Index="4" Bitrate="381048" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DACD941419F9F011000003E90000EA600F14299600000000168EBECB22C" />
      <c t="0" d="60060000" r="11" />
      <c d="42375666" />
   </StreamIndex>
</SmoothStreamingMedia>
```

### <a name="naming-of-tracks-in-the-manifest"></a>매니페스트에서 트랙 이름 지정

오디오 트랙 이름이 .ism 파일에 지정되면 Media Services에서 `Label` 요소를 `AdaptationSet` 내에 추가하여 특정 오디오 트랙에 대한 텍스트 정보를 지정합니다. 출력 DASH 매니페스트의 예제는 다음과 같습니다.

```xml
<AdaptationSet codecs="mp4a.40.2" contentType="audio" lang="en" mimeType="audio/mp4" subsegmentAlignment="true" subsegmentStartsWithSAP="1">
  <Label>audio_track_name</Label>
  <Role schemeIdUri="urn:mpeg:dash:role:2011" value="main"/>
  <Representation audioSamplingRate="48000" bandwidth="131152" id="German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg">
    <BaseURL>German_Forest_Short_Poem_english-en-68s-2-lc-128000bps_seg.mp4</BaseURL>
  </Representation>
</AdaptationSet>
```

플레이어에서 `Label` 요소를 사용하여 UI에 표시할 수 있습니다.

### <a name="signaling-audio-description-tracks"></a>오디오 설명 트랙 신호 보내기

비디오에 내레이션 트랙을 추가하여 시각적으로 장애가 있는 클라이언트가 내레이션을 들어 비디오 녹화를 따르도록 할 수 있습니다. 매니페스트에서 오디오 트랙을 오디오 설명의 주석으로 추가해야 합니다. 이렇게 하려면 "accessibility" 및 "role" 매개 변수를 .ism 파일에 추가합니다. 오디오 트랙을 오디오 설명으로 알리기 위해 이러한 매개 변수를 올바르게 설정하는 것은 사용자의 책임입니다. 예를 들어 특정 오디오 트랙에 대한 .ism 파일에 `<param name="accessibility" value="description" />` 및 `<param name="role" value="alternate"`를 추가합니다. 

자세한 내용은 [설명이 포함된 오디오 트랙을 알리는 방법](signal-descriptive-audio-howto.md) 예제를 참조하세요.

#### <a name="smooth-streaming-manifest"></a>부드러운 스트리밍 매니페스트

부드러운 스트리밍 스트림을 재생하는 경우 매니페스트는 해당 오디오 트랙에 대한 `Accessibility` 및 `Role` 특성에 값을 전달합니다. 예를 들어 `StreamIndex` 요소에 `Role="alternate" Accessibility="description"`을 추가하여 오디오 설명임을 나타낼 수 있습니다.

#### <a name="dash-manifest"></a>DASH 매니페스트

DASH 매니페스트의 경우 오디오 설명을 알리기 위해 다음 두 요소가 추가됩니다.

```xml
<Accessibility schemeIdUri="urn:mpeg:dash:role:2011" value="description"/>
<Role schemeIdUri="urn:mpeg:dash:role:2011" value="alternate"/>
```

#### <a name="hls-playlist"></a>HLS 재생 목록

HLS v7 이상의 경우 `(format=m3u8-cmaf)` 해당 재생 목록은 오디오 설명 트랙이 신호를 받으면 `AUTOSELECT=YES,CHARACTERISTICS="public.accessibility.describes-video"`를 전달합니다.

#### <a name="example"></a>예제

자세한 내용은 [오디오 설명 트랙을 알리는 방법](signal-descriptive-audio-howto.md)을 참조하세요.

## <a name="dynamic-manifest"></a>동적 매니페스트

플레이어에 전송되는 트랙 수, 형식, 비트 전송률 및 프레젠테이션 창을 제어하기 위해 Media Services 동적 패키지 작성 도구에서 동적 필터링을 사용할 수 있습니다. 자세한 내용은 [동적 패키지 작성 도구를 사용하여 매니페스트 미리 필터링](filters-dynamic-manifest-overview.md)을 참조하세요.

## <a name="dynamic-encryption"></a>동적 암호화

*동적 암호화*를 사용하여 AES-128 또는 세 가지 주요 DRM(디지털 권한 관리) 시스템 중 하나에서 라이브 또는 주문형 콘텐츠를 동적으로 암호화할 수 있습니다. 동적 암호화된 라이브 콘텐츠 및 주문형 콘텐츠를 제공할 수 있습니다. 또한 Media Services는 인증된 클라이언트에 AES 키 및 DRM 라이선스를 전달하는 서비스를 제공합니다. 자세한 정보는 [동적 암호화](content-protection-overview.md)를 참조하세요.

> [!NOTE]
> Widevine은 Google Inc.에서 제공하는 서비스로, Google Inc.의 서비스 약관 및 개인정보처리방침을 따릅니다.

## <a name="more-information"></a>자세한 정보

[Azure Media Services 커뮤니티](media-services-community.md)에서 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 받을 수 있습니다.

## <a name="need-help"></a>도움 필요 시

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

[비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
