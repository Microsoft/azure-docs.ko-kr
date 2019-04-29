---
title: Media Encoder Standard 스키마 | Microsoft 문서
description: 이 문서에서는 Media Encoder Standard 형식 및 코덱에 대한 개요를 제공합니다.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 837235e04ce190a4481e1f19789d8e9ff9cb7578
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61131586"
---
# <a name="media-encoder-standard-schema"></a>Media Encoder Standard 스키마
이 문서에서는 [Media Encoder Standard 기본 설정](media-services-mes-presets-overview.md)의 기반이 되는 XML 스키마의 일부 요소와 형식에 대해 설명하며, 요소 및 해당 유효 값에 대한 설명도 제공합니다.  

## <a name="Preset"></a> 기본 설정(루트 요소)
인코딩 기본 설정을 정의합니다.  

### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Encoding** |[Encoding](media-services-mes-schema.md#Encoding) |루트 요소로서 입력 원본을 인코딩할 것임을 나타냅니다. |
| **Outputs** |[Outputs](media-services-mes-schema.md#Output) |원하는 출력 파일의 컬렉션입니다. |
| **StretchMode**<br/>minOccurs="0"<br/>default=“AutoSize|xs:string|출력 비디오 프레임 크기, 안쪽 여백, 픽셀 또는 표시 가로 세로 비율을 제어합니다. **StretchMode**는 다음 값 중 하나일 수 있습니다. **None**, **AutoSize**(기본값) 또는 **AutoFit**<br/><br/>**없음**: 입력 비디오의 픽셀 가로 세로 비율 또는 표시 가로 세로 비율을 고려하지 않고 출력 해상도(예: 미리 설정의 **Width** 및 **Height**)를 엄격하게 따릅니다. 출력 비디오의 가로 세로 비율이 입력과 다른 시나리오(예: [자르기](media-services-crop-video.md))에서 권장됩니다. <br/><br/>**AutoSize**: 출력 해상도가 미리 설정에서 지정된 창(Width * Height) 안에 맞춰집니다. 그러나 인코더는 사각형(1:1) 픽셀 가로 세로 비율이 있는 출력 비디오를 생성합니다. 따라서 안쪽 여백 없이 입력의 표시 가로 세로 비율과 일치하도록 출력 Width 또는 출력 Height를 재정의할 수 있습니다. 예를 들어, 입력이 1920 X 1080이고 인코딩 미리 설정에서 1280 X 1280을 요청하는 경우 미리 설정의 Height 값이 재정의되고 출력이 1280 X 720이 되어 16:9의 입력 가로 세로 비율이 유지됩니다. <br/><br/>**AutoFit**: 필요한 경우 출력 비디오의 안쪽 여백을 채워(레터박스 또는 필러박스 사용) 원하는 출력 해상도를 적용하는 동시에 출력의 활성 비디오 영역과 입력의 가로 세로 비율을 동일하게 유지합니다. 예를 들어, 입력이1920x1080이고 인코딩 미리 설정에서 1280x1280을 요청한다고 가정합니다. 이 경우 출력 비디오는 1280x1280이지만, 가로 세로 비율이 16:9인 ‘활성 비디오’의 내부 1280x720 사각형과 맨 위와 맨 아래에 280픽셀 높이의 레터박스 영역을 포함합니다. 다른 예로, 입력이1440x1080이고 인코딩 미리 설정에서 1280x720을 요청하는 경우 출력은 1280x720이 되지만, 가로 세로 비율이 4:3인 내부 960x720 사각형과 왼쪽 및 오른쪽에 160픽셀 너비의 필러박스를 포함합니다. 

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **버전**<br/><br/> 필수 |**xs: decimal** |기본 설정 버전입니다. 적용되는 제한 사항으로 xs:fractionDigits value="1" 및 xs:minInclusive value="1"이 있습니다(예: **version="1.0"**). |

## <a name="Encoding"></a> Encoding
다음 요소의 시퀀스를 포함합니다.  

### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **H264Video** |[H264Video](media-services-mes-schema.md#H264Video) |H.264 비디오 인코딩 설정입니다. |
| **AACAudio** |[AACAudio](media-services-mes-schema.md#AACAudio) |AAC 오디오 인코딩 설정입니다. |
| **BmpImage** |[BmpImage](media-services-mes-schema.md#BmpImage) |Bmp 이미지 설정입니다. |
| **PngImage** |[PngImage](media-services-mes-schema.md#PngImage) |Png 이미지 설정입니다. |
| **JpgImage** |[JpgImage](media-services-mes-schema.md#JpgImage) |Jpg 이미지 설정입니다. |

## <a name="H264Video"></a> H264Video
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **TwoPass**<br/><br/> minOccurs="0" |**xs: boolean** |현재는 1 패스 인코딩만 지원됩니다. |
| **KeyFrameInterval**<br/><br/> minOccurs="0"<br/><br/> **default="00:00:02"** |**xs: time** |IDR 프레임 사이의 고정 간격을 초 단위로 결정합니다. 이를 GOP 기간이라고도 합니다. 인코더가 이 값에서 벗어날 수 있는지 여부를 제어하려면 **SceneChangeDetection**을 참조하세요. |
| **SceneChangeDetection**<br/><br/> minOccurs="0"<br/><br/> default=”false” |**xs: boolean** |true로 설정된 경우 인코더에서 비디오 장면 변경을 감지하고 IDR 프레임을 삽입합니다. |
| **Complexity**<br/><br/> minOccurs="0"<br/><br/> default="Balanced" |**xs:string** |인코딩 속도와 비디오 품질 간의 균형을 제어합니다. 다음 값 중 하나일 수 있습니다. **Speed**, **Balanced** 또는 **Quality**<br/><br/> Default: **Balanced** |
| **SyncMode**<br/><br/> minOccurs="0" | |향후 릴리스에서 공개될 기능입니다. |
| **H264Layers**<br/><br/> minOccurs="0" |[H264Layers](media-services-mes-schema.md#H264Layers) |출력 비디오 레이어의 컬렉션입니다. |

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Condition** |**xs:string** | 입력에 비디오가 없으면 인코더가 단색 비디오 트랙을 삽입하도록 강제 지정할 수 있습니다. 이렇게 하려면 Condition="InsertBlackIfNoVideoBottomLayerOnly"(최저 비트 전송률에서만 비디오 삽입) 또는 Condition="InsertBlackIfNoVideo"(모든 출력 비트 전송률에서 비디오 삽입)를 사용합니다. 자세한 내용은 [이](media-services-advanced-encoding-with-mes.md#no_video) 문서를 참조하세요.|

## <a name="H264Layers"></a> H264Layers

기본적으로 오디오만 포함하며 비디오는 없는 입력을 인코더로 보내면 출력 자산에는 오디오 데이터만 들어 있는 파일이 포함됩니다. 일부 플레이어는 해당 출력 스트림을 처리할 수 없습니다. 해당 시나리오에서는 H264Video의 **InsertBlackIfNoVideo** 특성 설정을 사용하여 인코더가 출력에 비디오 트랙을 추가하도록 강제 지정할 수 있습니다. 자세한 내용은 [이](media-services-advanced-encoding-with-mes.md#no_video) 문서를 참조하세요.
              
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **H264Layer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[H264Layer](media-services-mes-schema.md#H264Layer) |H264 레이어의 컬렉션입니다. |

## <a name="H264Layer"></a> H264Layer
> [!NOTE]
> 비디오 제한은 [H264 수준](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC#Levels) 표에서 설명하는 값을 기반으로 합니다.  
> 
> 

### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **프로필**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** |다음 **xs: string** 값 중 하나일 수 있습니다. **Auto**, **Baseline**, **Main**, **High** |
| **Level**<br/><br/> minOccurs="0"<br/><br/> default=”Auto” |**xs: string** | |
| **Bitrate**<br/><br/> minOccurs="0" |**xs:int** |이 비디오 레이어에 사용되는 비트 전송률이며, Kbps 단위로 지정됩니다. |
| **MaxBitrate**<br/><br/> minOccurs="0" |**xs: int** |이 비디오 레이어에 사용되는 최대 비트 전송률이며, Kbps 단위로 지정됩니다. |
| **BufferWindow**<br/><br/> minOccurs="0"<br/><br/> default="00:00:05" |**xs: time** |비디오 버퍼의 길이입니다. |
| **Width**<br/><br/> minOccurs="0" |**xs: int** |출력 비디오 프레임의 너비(픽셀)입니다.<br/><br/> 현재는 너비와 높이를 모두 지정해야 합니다. 너비와 높이는 짝수여야 합니다. |
| **Height**<br/><br/> minOccurs="0" |**xs:int** |출력 비디오 프레임의 높이(픽셀)입니다.<br/><br/> 현재는 너비와 높이를 모두 지정해야 합니다. 너비와 높이는 짝수여야 합니다.|
| **BFrames**<br/><br/> minOccurs="0" |**xs: int** |참조 프레임 사이의 B 프레임 수입니다. |
| **ReferenceFrames**<br/><br/> minOccurs="0"<br/><br/> default=”3” |**xs:int** |GOP의 참조 프레임 수입니다. |
| **EntropyMode**<br/><br/> minOccurs="0"<br/><br/> default=”Cabac” |**xs: string** |다음 값 중 하나일 수 있습니다. **Cabac** 및 **Cavlc** |
| **FrameRate**<br/><br/> minOccurs="0" |유리수입니다. |출력 비디오의 프레임 속도를 결정합니다. 인코더에서 입력 비디오와 동일한 프레임 속도를 사용하도록 하려면 "0/1"(기본값)을 사용합니다. 허용되는 값은 일반적인 비디오 프레임 속도일 것으로 예상되지만, 유효한 유리수는 모두 허용됩니다. 예를 들어 1/1은 1fps이고 사용할 수 있습니다.<br/><br/> - 12/1(12fps)<br/><br/> - 15/1(15fps)<br/><br/> - 24/1(24fps)<br/><br/> - 24000/1001(23.976fps)<br/><br/> - 25/1(25fps)<br/><br/>  - 30/1(30fps)<br/><br/> - 30000/1001(29.97fps) <br/> <br/>**참고** 다중 비트 전송률 인코딩에 대한 사용자 지정 사전 설정을 만들면 사전 설정의 모든 계층에서는 FrameRate와 동일한 값을 사용**해야** 합니다.|
| **AdaptiveBFrame**<br/><br/> minOccurs="0" |**xs: boolean** |Azure Media Encoder에서 복사합니다. |
| **Slices**<br/><br/> minOccurs="0"<br/><br/> default="0" |**xs:int** |프레임이 분할되는 조각 수를 결정합니다. 기본값을 사용하는 것이 좋습니다. |

## <a name="AACAudio"></a> AACAudio
 다음 요소와 그룹의 시퀀스를 포함합니다.  

 AAC에 대한 자세한 내용은 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding)를 참조하세요.  

### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **프로필**<br/><br/> minOccurs="0 "<br/><br/> default="AACLC" |**xs: string** |다음 값 중 하나일 수 있습니다. **AACLC**, **HEAACV1** 또는 **HEAACV2** |

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Condition** |**xs: string** |입력에 오디오가 없을 때 조용한 오디오 트랙을 포함하는 자산을 생성하도록 인코더를 적용하려면 "InsertSilenceIfNoAudio" 값을 지정합니다.<br/><br/> 기본적으로 비디오만 포함하며 오디오는 없는 입력을 인코더로 보내면 출력 자산에는 비디오 데이터만 들어 있는 파일이 포함됩니다. 일부 플레이어는 해당 출력 스트림을 처리할 수 없습니다. 이 시나리오에서는 이 설정을 사용하여 출력에 조용한 오디오 트랙을 추가하는 인코더를 강제할 수 있습니다. |

### <a name="groups"></a>그룹

| 참조 | 설명 |
| --- | --- |
| [AudioGroup](media-services-mes-schema.md#AudioGroup)<br/><br/> minOccurs="0" |각 프로필에 대해 설정할 수 있는 적절한 채널 수, 샘플링 속도 및 비트 전송률을 확인하려면 [AudioGroup](media-services-mes-schema.md#AudioGroup)에 대한 설명을 참조하세요. |

## <a name="AudioGroup"></a> AudioGroup
각 프로필에 유효한 값에 대한 자세한 내용은 뒤에 나오는 "오디오 코덱 세부 정보" 표를 참조하세요.  

### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Channels**<br/><br/> minOccurs="0" |**xs: int** |인코딩된 오디오 채널 수입니다. 유효한 옵션은 1, 2, 5, 6, 8입니다.<br/><br/> Default: 2. |
| **SamplingRate**<br/><br/> minOccurs="0" |**xs: int** |Hz 단위로 지정된 오디오 샘플링 속도입니다. |
| **Bitrate**<br/><br/> minOccurs="0" |**xs: int** |오디오를 인코딩할 때 사용되는 비트 전송률 (Kbps)입니다. |

### <a name="audio-codec-details"></a>오디오 코덱 세부 정보

오디오 코덱|세부 정보  
-----------------|---  
**AACLC** |1:<br/><br/> - 11025: 8 &lt;= 비트 전송률 &lt; 16<br/><br/> - 12000: 8 &lt;= 비트 전송률 &lt; 16<br/><br/> - 16000: 8 &lt;= 비트 전송률 &lt;32<br/><br/>- 22050: 24 &lt;= 비트 전송률 &lt; 32<br/><br/> - 24000: 24 &lt;= 비트 전송률 &lt; 32<br/><br/> - 32000: 32 &lt;= 비트 전송률 &lt;= 192<br/><br/> - 44100: 56 &lt;= 비트 전송률 &lt;= 288<br/><br/> - 48000: 56 &lt;= 비트 전송률 &lt;= 288<br/><br/> - 88200: 128 &lt;= 비트 전송률 &lt;= 288<br/><br/> - 96000: 128 &lt;= 비트 전송률 &lt;= 288<br/><br/> 2:<br/><br/> - 11025: 16 &lt;= 비트 전송률 &lt; 24<br/><br/> - 12000: 16 &lt;= 비트 전송률 &lt; 24<br/><br/> - 16000: 16 &lt;= 비트 전송률 &lt; 40<br/><br/> - 22050: 32 &lt;= 비트 전송률 &lt; 40<br/><br/> - 24000: 32 &lt;= 비트 전송률 &lt; 40<br/><br/> - 32000:  40 &lt;= 비트 전송률 &lt;= 384<br/><br/> - 44100: 96 &lt;= 비트 전송률 &lt;= 576<br/><br/> - 48000: 96 &lt;= 비트 전송률 &lt;= 576<br/><br/> - 88200: 256 &lt;= 비트 전송률 &lt;= 576<br/><br/> - 96000: 256 &lt;= 비트 전송률 &lt;= 576<br/><br/> 5/6:<br/><br/> - 32000: 160 &lt;= 비트 전송률 &lt;= 896<br/><br/> - 44100: 240 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 48000: 240 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 88200: 640 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 96000: 640 &lt;= 비트 전송률 &lt;= 1024<br/><br/> 8:<br/><br/> - 32000: 224 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 44100: 384 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 48000: 384 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 88200: 896 &lt;= 비트 전송률 &lt;= 1024<br/><br/> - 96000: 896 &lt;= 비트 전송률 &lt;= 1024  
**HEAACV1** |1:<br/><br/> - 22050: 비트 전송률 = 8<br/><br/> - 24000: 8 &lt;= 비트 전송률 &lt;= 10<br/><br/> - 32000: 12 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 44100: 20 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 48000: 20 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 88200: 비트 전송률 = 64<br/><br/> 2:<br/><br/> - 32000: 16 &lt;= 비트 전송률 &lt;= 128<br/><br/> - 44100: 16 &lt;= 비트 전송률 &lt;= 128<br/><br/> - 48000: 16 &lt;= 비트 전송률 &lt;= 128<br/><br/> - 88200: 96 &lt;= 비트 전송률 &lt;= 128<br/><br/> - 96000: 96 &lt;= 비트 전송률 &lt;= 128<br/><br/> 5/6:<br/><br/> - 32000: 64 &lt;= 비트 전송률 &lt;= 320<br/><br/> - 44100: 64 &lt;= 비트 전송률 &lt;= 320<br/><br/> - 48000: 64 &lt;= 비트 전송률 &lt;= 320<br/><br/> - 88200: 256 &lt;= 비트 전송률 &lt;= 320<br/><br/> - 96000: 256 &lt;= 비트 전송률 &lt;= 320<br/><br/> 8:<br/><br/> - 32000: 96 &lt;= 비트 전송률 &lt;= 448<br/><br/> - 44100: 96 &lt;= 비트 전송률 &lt;= 448<br/><br/> - 48000: 96 &lt;= 비트 전송률 &lt;= 448<br/><br/> - 88200: 384 &lt;= 비트 전송률 &lt;= 448<br/><br/> - 96000: 384 &lt;= 비트 전송률 &lt;= 448  
**HEAACV2** |2:<br/><br/> - 22050: 8 &lt;= 비트 전송률 &lt;= 10<br/><br/> - 24000: 8 &lt;= 비트 전송률 &lt;= 10<br/><br/> - 32000: 12 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 44100: 20 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 48000: 20 &lt;= 비트 전송률 &lt;= 64<br/><br/> - 88200: 64 &lt;= 비트 전송률 &lt;= 64  
  
## <a name="Clip"></a> Clip
### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **StartTime** |**xs:duration** |프레젠테이션 시작 시간을 지정합니다. StartTime 값이 입력 비디오의 절대 타임스탬프와 일치해야 합니다. 예를 들어 입력 비디오의 첫 번째 프레임에 12:00:10.000 타임스탬프가 있으면 StartTime은 12:00:10.000 이상이어야 합니다. |
| **Duration** |**xs:duration** |프레젠테이션 지속 시간을 지정합니다 (예: 비디오의 오버레이 모양). |

## <a name="Output"></a> Output
### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **FileName** |**xs:string** |출력 파일 이름입니다.<br/><br/> 다음 표에 설명된 매크로를 사용하여 출력 파일 이름을 작성할 수 있습니다. 예를 들면 다음과 같습니다.<br/><br/> **"Outputs": [      {       "FileName": "{Basename}*{Resolution}*{Bitrate}.mp4",       "Format": {         "Type": "MP4Format"       }     }   ]** |

### <a name="macros"></a>Macros

| 매크로 | 설명 |
| --- | --- |
| **{Basename}** |VoD 인코딩을 수행하는 경우 {Basename}은 입력 자산의 기본 파일에 포함된 AssetFile.Name 속성의 처음 32자입니다.<br/><br/> 입력 자산이 라이브 아카이브인 경우 {Basename}은 서버 매니페스트의 trackName 특성에서 파생됩니다. “<VideoStream\>TopBitrate</VideoStream\>”와 같이 TopBitrate를 사용하여 하위 클립 작업을 제출하고 출력 파일에 비디오가 포함된 경우 {Basename}은 가장 높은 비트 전송률을 가진 비디오 레이어에 대한 trackName의 처음 32자입니다.<br/><br/> 대신 “<VideoStream\>*</VideoStream\>”와 같이 모든 입력 비트 전송률을 사용하여 하위 클립 작업을 제출하고 출력 파일에 비디오가 포함된 경우 {Basename}은 해당 비디오 레이어에 대한 trackName의 처음 32자입니다. |
| **{Codec}** |비디오의 경우 “H264”, 오디오의 경우 “AAC”에 매핑됩니다. |
| **{Bitrate}** |비디오와 오디오가 출력 파일에 포함되는 경우 대상 비디오 비트 전송률이고, 오디오만 출력 파일에 포함되는 경우 대상 오디오 비트 전송률입니다. 사용되는 값은 Kbps 단위의 비트 전송률입니다. |
| **{Channel}** |오디오가 파일에 포함되는 경우 오디오 채널 수입니다. |
| **{Width}** |비디오가 출력 파일에 포함되는 경우 이 파일의 비디오 너비(픽셀)입니다. |
| **{Height}** |비디오가 출력 파일에 포함되는 경우 이 파일의 비디오 높이(픽셀)입니다. |
| **{Extension}** |출력 파일의 “Type” 속성에서 상속됩니다. 출력 파일 이름의 확장명은 “mp4”, “ts”, “jpg”, “png” 또는 “bmp” 중 하나입니다. |
| **{Index}** |미리 보기의 경우 필수 항목입니다. 한 번만 존재해야 합니다. |

## <a name="Video"></a> Video(Codec에서 상속되는 복합 형식)
### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **시작** |**xs:string** | |
| **Step** |**xs:string** | |
| **Range** |**xs:string** | |
| **PreserveResolutionAfterRotation** |**xs: boolean** |자세한 설명을 보려면 다음 섹션을 참조하세요. [PreserveResolutionAfterRotation](media-services-mes-schema.md#PreserveResolutionAfterRotation) |

### <a name="PreserveResolutionAfterRotation"></a> PreserveResolutionAfterRotation
**PreserveResolutionAfterRotation** 플래그를 백분율 용어로 표현된 해상도 값(Width=”100%” , Height = “100%”)과 함께 사용하는 것이 좋습니다.  

기본적으로 MES(Media Encoder Standard) 기본 설정의 인코딩 해상도 설정(Width, Height)은 0도 회전의 비디오를 대상으로 합니다. 예를 들어, 입력 비디오가 1280x720이고 0도 회전인 경우 기본 설정은 이와 동일한 해상도를 가진 출력인지 확인하는 것입니다.  

![MESRoation1](./media/media-services-shemas/media-services-mes-roation1.png) 

입력 비디오가 0도가 아닌 회전으로 캡처되는 경우(예: 스마트폰 또는 태블릿의 세로 고정) MES에서는 기본적으로 인코딩 해상도 설정(Width, Height)을 입력 비디오에 적용한 다음 회전을 보정합니다. 예를 들어 다음 그림을 참조하세요. 기본 설정에서 Width = “100%”, Height = “100%”를 사용하며, 이렇게 하면 MES에서 너비 1280픽셀, 높이 720픽셀의 출력인 것으로 해석합니다. 비디오를 회전하면 창에 맞게 왼쪽과 오른쪽에 기둥 상자 영역을 표시하면서 그림을 축소합니다.  

![MESRoation2](./media/media-services-shemas/media-services-mes-roation2.png) 

또는 **PreserveResolutionAfterRotation** 플래그를 사용하여 “true”(기본값은 “false”)로 설정할 수 있습니다. 따라서 기본 설정에 Width = “100%”, Height = “100%”가 있고 PreserveResolutionAfterRotation이 “true”로 설정된 경우, 너비 1280픽셀, 높이 720픽셀, 회전 90도의 입력 비디오는 너비 720픽셀, 높이 1280픽셀, 회전 0도의 출력을 생성합니다. 다음 그림을 참조하세요.  

![MESRoation3](./media/media-services-shemas/media-services-mes-roation3.png) 

## <a name="FormatGroup"></a> FormatGroup(그룹)
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **BmpFormat** |**BmpFormat** | |
| **PngFormat** |**PngFormat** | |
| **JpgFormat** |**JpgFormat** | |

## <a name="BmpLayer"></a> BmpLayer
### <a name="element"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayer"></a> PngLayer
### <a name="element"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="JpgLayer"></a> JpgLayer
### <a name="element"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Width**<br/><br/> minOccurs="0" |**xs:int** | |
| **Height**<br/><br/> minOccurs="0" |**xs:int** | |
| **Quality**<br/><br/> minOccurs="0" |**xs:int** |유효한 값:  1(최악)-100(최적) |

### <a name="attributes"></a>특성

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **Condition** |**xs:string** | |

## <a name="PngLayers"></a> PngLayers
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **PngLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[PngLayer](media-services-mes-schema.md#PngLayer) | |

## <a name="BmpLayers"></a> BmpLayers
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **BmpLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[BmpLayer](media-services-mes-schema.md#BmpLayer) | |

## <a name="JpgLayers"></a> JpgLayers
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **JpgLayer**<br/><br/> minOccurs="0" maxOccurs="unbounded" |[JpgLayer](media-services-mes-schema.md#JpgLayer) | |

## <a name="BmpImage"></a> BmpImage(Video에서 상속되는 복합 형식)
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="JpgImage"></a> JpgImage(Video에서 상속되는 복합 형식)
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="PngImage"></a> PngImage(Video에서 상속되는 복합 형식)
### <a name="elements"></a>요소

| 이름 | 형식 | 설명 |
| --- | --- | --- |
| **PngLayers**<br/><br/> minOccurs="0" |[PngLayers](media-services-mes-schema.md#PngLayers) |Png layers |

## <a name="examples"></a>예
이 스키마에 기반하여 작성된 XML 기본 설정 예제를 보려면 [MES(Media Encoder Standard)의 작업 기본 설정](media-services-mes-presets-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

