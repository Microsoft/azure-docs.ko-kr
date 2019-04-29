---
title: Azure 주문형 미디어 인코더 비교 | Microsoft Docs
description: 이 항목에서는 **Media Encoder Standard** 및 **Media Encoder Premium Workflow**의 인코딩 기능을 비교합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;anilmur
ms.openlocfilehash: bb827b80f79a53f30074b9230efe3e2049471051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465714"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Azure 주문형 미디어 인코더 비교  

이 항목에서는 **Media Encoder Standard** 및 **Media Encoder Premium Workflow**의 인코딩 기능을 비교합니다.

## <a name="video-and-audio-processing-capabilities"></a>비디오 및 오디오 처리 기능

다음 표에서는 MES(Media Encoder Standard) 및 MEPW(Media Encoder Premium Workflow)의 기능을 비교합니다. 

|기능|미디어 인코더 표준|미디어 인코더 Premium 워크플로|
|---|---|---|
|인코딩 중에 조건부 논리 적용<br/>(예를 들어, 입력이 HD인 경우 인코드 5.1 오디오)|아닙니다.|예|
|선택 자막|아닙니다.|[예](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professional Loudness Correction](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> Dialogue Intelligence™ 사용|아닙니다.|예|
|디-인터레이스, 역텔레시네|Basic|브로드캐스트 품질|
|검은색 테두리 감지 및 제거 <br/>(필러박스, 레터박스)|아닙니다.|예|
|썸네일 생성|[예](media-services-dotnet-generate-thumbnail-with-mes.md)|[예](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|비디오 클리핑/잘라내기 및 붙이기|[예](media-services-advanced-encoding-with-mes.md#trim_video)|예|
|오디오 또는 비디오 오버레이|[예](media-services-advanced-encoding-with-mes.md#overlay)|[예](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|그래픽 오버레이|이미지 원본에서|이미지 및 텍스트 원본에서|
|다중 오디오 언어 트랙|제한적|[예](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>각 인코더에서 사용되는 요금 청구 기준
| 미디어 프로세서 이름 | 적용 가능한 가격 | 메모 |
| --- | --- | --- |
| **미디어 인코더 표준** |인코더 |Encoding 작업은 [여기][1]의 인코더 열 아래에 지정된 요율을 기준으로, 출력으로 생성된 모든 미디어 파일의 전체 재생 시간(분)에 따라 요금이 부과됩니다. |
| **미디어 인코더 Premium 워크플로** |프리미엄 인코더 |Encoding 작업은 [여기][1]의 프리미엄 인코더 열 아래에 지정된 요율을 기준으로, 출력으로 생성된 모든 미디어 파일의 전체 재생 시간(분)에 따라 요금이 부과됩니다. |

## <a name="input-containerfile-formats"></a>입력 컨테이너/파일 형식
| 입력 컨테이너/파일 형식 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| Adobe® Flash® F4V |예 |예 |
| MXF/SMPTE 377M |예 |예 |
| GXF |예 |예 |
| MPEG-2 전송 스트림 |예 |예 |
| MPEG-2 프로그램 스트림 |예 |예 |
| MPEG-4/MP4 |예 |예 |
| Windows Media/ASF |예 |예 |
| AVI(압축되지 않은 8비트/10비트) |예 |예 |
| 3GPP/3GPP2 |예 |아닙니다. |
| 부드러운 스트리밍 파일 형식(PIFF 1.3) |예 |아닙니다. |
| [DVR-MS(Microsoft Digital Video Recording)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |예 |아닙니다. |
| Matroska/WebM |예 |아닙니다. |
| QuickTime(.mov) |예 |아닙니다. |

## <a name="input-video-codecs"></a>입력 비디오 코덱
| 입력 비디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AVC 8비트/10비트, 최대 4:2:2, AVCIntra 포함 |8비트 4:2:0 및 4:2:2 |예 |
| Avid DNxHD(MXF) |예 |예 |
| DVCPro/DVCProHD(MXF) |예 |예 |
| JPEG2000 |예 |예 |
| MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함) |최대 422 프로필 |예 |
| MPEG-1 |예 |예 |
| Windows Media 비디오/VC-1 |예 |예 |
| Canopus HQ/HQX |아닙니다. |아닙니다. |
| Mpeg-4 2부 |예 |아닙니다. |
| [Theora](https://en.wikipedia.org/wiki/Theora) |예 |아닙니다. |
| Apple ProRes 422 |예 |아닙니다. |
| Apple ProRes 422 LT |예 |아닙니다. |
| Apple ProRes 422 HQ |예 |아닙니다. |
| Apple ProRes Proxy |예 |아닙니다. |
| Apple ProRes 4444 |예 |아닙니다. |
| Apple ProRes 4444 XQ |예 |아닙니다. |
| HEVC/H.265|기본 프로필|기본 및 기본 10 프로필|

## <a name="input-audio-codecs"></a>입력 오디오 코덱
| 입력 오디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AES(SMPTE 331M 및 302M, AES3-2003) |아닙니다. |예 |
| Dolby® E |아닙니다. |예 |
| Dolby® Digital(AC3) |아닙니다. |예 |
| Dolby® Digital Plus(E-AC3) |아닙니다. |예 |
| AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1) |예 |예 |
| MPEG Layer 2 |예 |예 |
| MP3(MPEG-1 Audio Layer 3) |예 |예 |
| Windows Media 오디오 |예 |예 |
| WAV/PCM |예 |예 |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |예 |아닙니다. |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |예 |아닙니다. |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |예 |아닙니다. |

## <a name="output-containerfile-formats"></a>출력 컨테이너/파일 형식
| 출력 컨테이너/파일 형식 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| Adobe® Flash® F4V |아닙니다. |예 |
| MXF(OP1a, XDCAM 및 AS02) |아닙니다. |예 |
| DPP(AS11 포함) |아닙니다. |예 |
| GXF |아닙니다. |예 |
| MPEG-4/MP4 |예 |예 |
| MPEG-TS |예 |예 |
| Windows Media/ASF |아닙니다. |예 |
| AVI(압축되지 않은 8비트/10비트) |아닙니다. |예 |
| 부드러운 스트리밍 파일 형식(PIFF 1.3) |아닙니다. |예 |

## <a name="output-video-codecs"></a>출력 비디오 코덱
| 출력 비디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AVC(H.264, 8비트, 최대 High Profile, 수준 5.2, 4K Ultra HD, AVC Intra) |8비트 4:2:0만 |예 |
| HEVC(H.265; 8비트 및 10비트;)  |아닙니다. |예 |
| Avid DNxHD(MXF) |아닙니다. |예 |
| MPEG-2(최대 422 프로필 및 높은 수준, XDCAM, XDCAM HD, XDCAM IMX, CableLabs® 및 D10과 같은 변형 포함) |아닙니다. |예 |
| MPEG-1 |아닙니다. |예 |
| Windows Media 비디오/VC-1 |아닙니다. |예 |
| JPEG 축소판 그림 만들기 |예 |예 |
| PNG 썸네일 만들기 |예 |예 |
| BMP 썸네일 만들기 |예 |아닙니다. |

## <a name="output-audio-codecs"></a>출력 오디오 코덱
| 출력 오디오 코덱 | 미디어 인코더 표준 | 미디어 인코더 Premium 워크플로 |
| --- | --- | --- |
| AES(SMPTE 331M 및 302M, AES3-2003) |아닙니다. |예 |
| Dolby® Digital(AC3) |아닙니다. |예 |
| Dolby® Digital Plus(E-AC3) 최대 7.1 |아닙니다. |예 |
| AAC(AAC-LC, AAC-HE 및 AAC-HEv2, 최대 5.1) |예 |예 |
| MPEG Layer 2 |아닙니다. |예 |
| MP3(MPEG-1 Audio Layer 3) |아닙니다. |예 |
| Windows Media 오디오 |아닙니다. |예 |

>[!NOTE]
>Dolby® Digital(AC3)로 인코딩하면 출력은 ISO MP4 파일에만 쓸 수 있습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>관련 문서
* [미디어 인코더 표준 사전 설정을 사용자 지정하여 고급 인코딩 작업 수행](media-services-custom-mes-presets-with-dotnet.md)
* [할당량 및 제한 사항](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
