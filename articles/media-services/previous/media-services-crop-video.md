---
title: Media Encoder Standard로 비디오를 자르는 방법 - Azure | Microsoft Docs
description: 자르기는 비디오 프레임 내에서 사각형 창을 선택하는 과정이며 해당 창 내의 픽셀을 인코딩합니다. 이 문서에서는 Media Encoder Standard으로 비디오를 자르는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 4264d1c0d83f14da02b26107d336521250fcf13b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89257860"
---
# <a name="crop-videos-with-media-encoder-standard"></a>미디어 인코더 표준으로 비디오 자르기

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

미디어 인코더 표준(MES)을 사용하여 입력 비디오를 자를 수 있습니다. 자르기는 비디오 프레임 내에서 사각형 창을 선택하는 과정이며 해당 창 내의 픽셀을 인코딩합니다. 다음 다이어그램은 프로세스를 설명하는 데 도움이 됩니다.

![비디오 자르기](./media/media-services-crop-video/media-services-crop-video01.png)

해상도가 1920x1080픽셀(16:9 가로 세로 비율)인 비디오를 입력으로 포함하고 왼쪽 및 오른쪽에 검은색 막대가 있다고 가정하면 4:3 창 또는 1440x1080픽셀만 실제 비디오를 포함합니다. MES를 사용하여 검은색 막대를 자르거나 편집하고 1440x1080 영역을 인코딩할 수 있습니다.

MES에서 자르기는 전처리 단계이므로 인코딩 사전 설정의 자르기 매개 변수를 원래 입력 비디오에 적용합니다. Encoding은 후속 단계이며 너비/높이 설정은 원본 비디오가 아닌 *전처리된* 비디오에 적용됩니다. 사전 설정을 지정할 경우 다음을 수행해야 합니다. (a) 원본 입력 비디오를 기반으로 자르기 매개 변수를 선택하고 (b) 자른 비디오를 기반으로 인코딩 설정을 선택합니다. 인코딩 설정이 자른 비디오와 일치하지 않는 경우 예상한 대로 출력되지 않습니다.

[다음](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) 토픽은 MES로 인코딩 작업을 만드는 방법과 인코딩 작업에 대한 사용자 지정 사전 설정을 지정하는 방법을 보여 줍니다. 

## <a name="creating-a-custom-preset"></a>사용자 지정 사전 설정 만들기
다이어그램에 표시된 예:

1. 원본 입력은 1920x1080입니다.
2. 입력 프레임의 가운데에 있는 1440x1080의 출력으로 잘라야 합니다.
3. 즉, X 오프셋은 (1920 – 1440)/2 = 240이고 Y 오프셋은 0입니다.
4. 자르기 사각형의 너비 및 높이는 각각 1440 및 1080입니다.
5. 인코딩 단계에서 해상도가 각각 1440x1080, 960x720 및 480x360인 세 계층을 생성할지를 묻습니다.

### <a name="json-preset"></a>JSON 사전 설정

```json
{
  "Version": 1.0,
  "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Crop": {
          "X": 240,
          "Y": 0,
          "Width": 1440,
          "Height": 1080
        }
      },
      "Pad": true
    }
  ],
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 3400,
          "MaxBitrate": 3400,
          "BufferWindow": "00:00:05",
          "Width": 1440,
          "Height": 1080,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 2250,
          "MaxBitrate": 2250,
          "BufferWindow": "00:00:05",
          "Width": 960,
          "Height": 720,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        },
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 1250,
          "MaxBitrate": 1250,
          "BufferWindow": "00:00:05",
          "Width": 480,
          "Height": 360,
          "BFrames": 3,
          "ReferenceFrames": 3,
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="restrictions-on-cropping"></a>자르기에 대한 제한 사항
자르기 기능은 수동입니다. 원하는 프레임을 선택 하 고, 커서를 배치 하 여 자르기 사각형의 오프셋을 결정 하 고, 특정 비디오 등에 대해 조정 되는 인코딩 사전 설정을 결정 하는 데 적합 한 편집 도구로 입력 비디오를 로드 해야 합니다. 이 기능은 입력 비디오에서 블랙 박스/pillarbox 테두리의 자동 검색 및 제거와 같은 작업을 사용 하도록 설정 하는 것이 아닙니다.

자르기 기능에는 다음과 같은 제약 조건이 적용됩니다. 이러한 제약 조건이 충족되지 않으면 인코딩 작업이 실패하거나 예기치 않은 출력을 생성할 수 있습니다.

1. 자르기 사각형의 좌표 및 크기가 입력 비디오 내에 맞아야 합니다.
2. 위에서 설명했듯이 인코딩 설정의 너비 및 높이는 자른 비디오와 일치해야 합니다.
3. 자르기는 가로 모드에서 캡처된 비디오에 적용됩니다(즉, 세로로 또는 인물 모드로 스마트폰을 잡고 기록한 비디오에는 적용되지 않음).
4. 정사각형 픽셀로 캡처된 프로그레시브 비디오에서 가장 잘 작동합니다.

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>다음 단계
AMS에서 제공하는 훌륭한 기능에 대해 자세히 알아보려면 Azure Media Services 학습 경로를 참조하세요.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
