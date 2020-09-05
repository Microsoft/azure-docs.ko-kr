---
title: REST를 사용 하 여 Azure Media Services 인코더 표준을 사용 하 여 미리 보기를 생성 하는 방법
description: 이 문서에서는 REST를 사용 하 여 자산을 인코딩하고 동시에 Media Encoder Standard를 사용 하 여 미리 보기를 생성 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: af320b94950d5999b6dd181b7a8e0eb198088e98
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267635"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-rest"></a>REST를 사용 하 여 인코더 표준을 사용 하 여 미리 보기를 생성 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard를 사용하여 입력 동영상에서 하나 이상의 미리 보기를 [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 또는 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 이미지 파일 형식으로 생성할 수 있습니다.

## <a name="recommended-reading-and-practice"></a>권장 읽기 및 연습

사용자 [지정 변환-REST를 사용 하 여 인코딩하는 방법을](custom-preset-rest-howto.md)읽으면 사용자 지정 변환을 익히는 것이 좋습니다.

## <a name="thumbnail-parameters"></a>미리 보기 매개 변수

다음 매개 변수를 설정 해야 합니다.

- **start** -축소판 그림 생성을 시작할 입력 비디오의 위치입니다. 값은 ISO 8601 형식 (예: 5 초에 시작 하는 PT05S) 또는 프레임 수 (10, 10, 10, 10%)의 스트림 기간에 대 한 상대 값 (예: 10% (스트림 기간의 10%)으로 시작 하는 경우)이 될 수 있습니다. 는 인코더에 비디오의 처음 몇 초에서 가장 적합 한 미리 보기를 선택 하도록 지시 하 고, 단계 및 범위에 대 한 다른 설정에 상관 없이 축소판 그림을 하나만 생성 하는 매크로 {Best}도 지원 합니다. 기본값은 매크로 {Best}입니다.
- **단계** -미리 보기가 생성 되는 간격입니다. 값은 ISO 8601 형식 (예: 5 초 마다 하나의 이미지에 대 한 PT05S) 이나 프레임 수 (예: 30 프레임 마다 이미지의 경우 30 개) 또는 스트림 기간에 대 한 상대 값 (예: 스트림 기간의 10% 마다 한 이미지의 경우 10%) 일 수 있습니다. 단계 값은 처음 생성 된 축소판 그림에 영향을 줍니다 .이 축소판 그림은 변환 기본 설정 시작 시간에 지정 된 축소판 그림에 정확 하지 않을 수 있습니다. 인코더 때문입니다 .이는 시작 시간에서 시작 시간 사이에 가장 적합 한 축소판 그림을 첫 번째 출력으로 선택 하려고 시도 합니다. 기본값은 10% 이므로 스트림의 기간이 긴 경우 처음 생성 된 축소판 그림은 시작 시 지정 된 축소판 그림에서 멀리 떨어져 있을 수 있음을 의미 합니다. 첫 번째 축소판 그림이 시작 시간에 근접 하 게 예상 되는 경우 단계에 적합 한 값을 선택 하 고 시작 시간에 하나의 미리 보기만 필요한 경우 범위 값을 1로 설정 합니다.
- **범위** -미리 보기 생성을 중지할 입력 비디오의 미리 설정 시작 시간을 기준으로 하는 위치입니다. 값은 ISO 8601 형식 (예: 시작 시간에서 5 분에서 30 초에 PT5M30S) 또는 프레임 수 (300 예: 시작 시 프레임에서 1/300 프레임에서 중지 하는 경우) 일 수 있습니다. 이 값이 1 인 경우에는 시작 시간에 하나의 축소판 그림을 생성 하거나 스트림 기간에 대 한 상대 값 (예: 시작 시간에서 스트림 기간의 절반에서 중지 하는 50%)을 나타냅니다. 기본값은 100% 이며 스트림의 끝에서 중지 하는 것을 의미 합니다.
- **레이어** -인코더에서 생성할 출력 이미지 계층의 컬렉션입니다.

## <a name="example-of-a-single-png-file-preset"></a>"단일 PNG 파일" 사전 설정의 예

다음 JSON 사전 설정은 입력 비디오의 처음 몇 초에서 단일 출력 PNG 파일을 생성 하는 데 사용할 수 있습니다. 여기서 인코더는 "흥미로운" 프레임을 찾기 위해 최상의 노력을 합니다. 출력 이미지 크기는 100%로 설정되어 있습니다. 다시 말해서, 입력 동영상과 크기와 일치합니다. 또한 "Outputs"의 “Format” 설정이 “Codecs” 섹션의 "PngLayers" 사용과 일치해야 합니다.  

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset for thumbnails",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "{Best}",
                            "step": "25%",
                            "range": "80%",
                            "layers": [
                                {
                                    "width": "50%",
                                    "height": "50%"
                                }
                            ]
                        }
                    ],
                    "formats": [
                        {
                            "@odata.type": "#Microsoft.Media.Mp4Format",
                            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
                            "outputFiles": []
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngFormat",
                            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
                        }
                    ]
                }
            }
        ]
    }
}

```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>"일련의 JPEG 이미지" 사전 설정의 예

다음 JSON 사전 설정을 사용 하 여 입력 타임 라인의 5%, 15%, ..., 95%의 타임 스탬프에서 10 개의 이미지 집합을 생성할 수 있습니다. 여기서 이미지 크기는 입력 비디오의 1/4로 지정 됩니다.

### <a name="json-preset"></a>JSON 사전 설정

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>"특정 타임스탬프의 1개 이미지" 사전 설정의 예

다음 JSON 사전 설정은 입력 비디오의 30 초 표시에 단일 JPEG 이미지를 생성 하는 데 사용할 수 있습니다. 이 기본 설정에서는 입력 동영상이 30초를 넘을 것으로 예상합니다(그렇지 않으면 작업이 실패함).

### <a name="json-preset"></a>JSON 사전 설정

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "25%",
          "Height": "25%"
        }
      ],
      "Start": "00:00:30",
      "Step": "1",
      "Range": "1",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>"다른 해상도의 미리 보기" 사전 설정의 예

다음 사전 설정은 하나의 작업의 다른 해상도에서 미리 보기를 생성하는 데 사용될 수 있습니다. 예제에서는 입력 타임라인의 위치 5%, 15%, …, 95%에서 인코더가 입력 동영상 해상도 100%에서 하나 그리고 50%에서 다른 하나로, 두 개의 이미지를 생성합니다.

FileName에서 {해상도} 매크로를 사용하는 것은 출력 이미지의 파일 이름을 생성하는 동안 사전 설정의 인코딩 섹션에 지정한 너비 및 높이를 사용하는 인코더를 나타냅니다. 또한 여러 이미지를 쉽게 구분할 수 있습니다.

### <a name="json-preset"></a>JSON 사전 설정

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "JpgLayers": [
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "100%",
  "Height": "100%"
},
{
  "Quality": 90,
  "Type": "JpgLayer",
  "Width": "50%",
  "Height": "50%"
}

      ],
      "Start": "5%",
      "Step": "10%",
      "Range": "96%",
      "Type": "JpgImage"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
      "Format": {
"Type": "JpgFormat"
      }
    }
  ]
}
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>인코딩하는 동안 미리 보기 생성의 예

위의 모든 예제는 이미지만을 생성하는 인코딩 작업을 전송하는 방법을 설명했지만 미리 보기 생성으로 동영상/오디오 인코딩을 결합할 수도 있습니다. 다음 JSON 사전 설정은 인코딩 중에 미리 보기를 생성 하도록 인코더 표준을 지시 합니다.

### <a name="json-preset"></a>JSON 사전 설정

스키마에 대한 자세한 내용은 [이 문서](/azure/media-services/previous/media-services-mes-schema)를 참조하세요.

```json
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:02",
      "SceneChangeDetection": "true",
      "H264Layers": [
        {
          "Profile": "Auto",
          "Level": "auto",
          "Bitrate": 4500,
          "MaxBitrate": 4500,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cabac",
          "AdaptiveBFrame": true,
          "Type": "H264Layer",
          "FrameRate": "0/1"

        }
      ],
      "Type": "H264Video"
    },
    {
      "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        }
      ],
      "Start": "{Best}",
      "Type": "JpgImage"
    },
    {
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 128,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Index}{Extension}",
      "Format": {
        "Type": "JpgFormat"
      }
    },
    {
      "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
[.NET을 사용 하 여 미리 보기 생성](media-services-generate-thumbnails-dotnet.md)
