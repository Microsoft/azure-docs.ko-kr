---
title: Media Services v3 CLI-Azure를 사용 하 여 사용자 지정 변환을 인코딩 | Microsoft Docs
description: 이 항목에서는 CLI를 사용 하 여 사용자 지정 변환 인코딩하는 데 Azure Media Services v3을 사용 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: b0329c8c0bfa56e5ae36c5f4223edca91e6510a0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733116"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>사용자 지정 변환-CLI 사용 하 여 인코딩하는 방법

Azure Media Services로 인코딩 되 면 시작할 수 있습니다 신속 하 게 권장 되는 기본 제공 사전 설정 중에에 설명 된 대로 업계 모범 사례에 따라 하나를 사용 하 여 합니다 [파일을 스트리밍](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) 빠른 시작 합니다. 또한 특정 시나리오 또는 장치 요구 사항을 대상으로 사전 설정 사용자 지정을 빌드할 수 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 사항을 고려해 야 합니다.

* 높이 너비 AVC 내용에 대 한 모든 값은 4의 배수 여야 합니다.
* Azure Media Services v3에서는 인코딩 비트 전송률 모두 비트 / 초입니다. 이 킬로 비트/초 단위로 v2 Api 사용 하 여 사전 설정에서 다릅니다. 예를 들어 v2의 비트 전송률 (k b/초) 128로 지정 된, 경우 v3에서 것은에 설정할 128000 (비트/초)입니다.

## <a name="prerequisites"></a>필수 조건 

[Media Services 계정 만들기](create-account-cli-how-to.md) <br/>리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>사용자 지정 사전 설정을 정의 합니다.

다음 예제에서는 새 변환의 요청 본문을 정의 합니다. 이 변환을 사용 하는 경우 생성 하고자 하는 출력의 집합을 정의 합니다. 

이 예제에서는 먼저 오디오 인코딩에 AacAudio 계층과 비디오 인코딩에 대 한 두 H264Video 계층 추가 합니다. 비디오 레이어의 출력 파일 이름에 사용할 수 있도록 레이블을 할당 합니다. 다음으로, 미리 보기 포함 하도록 출력을 하려고 합니다. 아래 예제에서는 PNG 형식으로 입력된 비디오의 해상도의 50 %3의 타임 스탬프-{25%, 50%, 75} 입력된 비디오의 길이에서 생성 되는 이미지를 지정 합니다. 마지막으로, 비디오 + 오디오에 대 한 출력 파일-의 형식 지정 및 미리 보기에 대 한 다른 합니다. 여러 H264Layers 있으므로 계층당 고유한 이름을 생성 하는 매크로 사용 해야 합니다. 사용할 수 있습니다는 `{Label}` 또는 `{Bitrate}` 매크로 예제 전자를 보여 줍니다.

이 변환은 파일에 저장 하려고 합니다. 이 예제에서는 파일 이름을 `customPreset.json`입니다. 

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
                    "codecs": [
                        {
                            "@odata.type": "#Microsoft.Media.AacAudio",
                            "channels": 2,
                            "samplingRate": 48000,
                            "bitrate": 128000,
                            "profile": "AacLc"
                        },
                        {
                            "@odata.type": "#Microsoft.Media.H264Video",
                            "keyFrameInterval": "PT2S",
                            "stretchMode": "AutoSize",
                            "sceneChangeDetection": false,
                            "complexity": "Balanced",
                            "layers": [
                                {
                                    "width": "1280",
                                    "height": "720",
                                    "label": "HD",
                                    "bitrate": 3400000,
                                    "maxBitrate": 3400000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                },
                                {
                                    "width": "640",
                                    "height": "360",
                                    "label": "SD",
                                    "bitrate": 1000000,
                                    "maxBitrate": 1000000,
                                    "bFrames": 3,
                                    "slices": 0,
                                    "adaptiveBFrame": true,
                                    "profile": "Auto",
                                    "level": "auto",
                                    "bufferWindow": "PT5S",
                                    "referenceFrames": 3,
                                    "entropyMode": "Cabac"
                                }
                            ]
                        },
                        {
                            "@odata.type": "#Microsoft.Media.PngImage",
                            "stretchMode": "AutoSize",
                            "start": "25%",
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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>새 변환을 만듭니다  

이 예에서는 만듭니다는 **변환** 앞에서 정의한 사용자 지정 사전 설정을 기반으로 하는 합니다. 변환을 만들 때 하나 이미 있는지를 먼저 확인 해야 합니다. 변환이 있는 경우 다시 사용 합니다. 다음 `show` 명령이 반환 된 `customTransformName` 있으면 변환 합니다.

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

다음 CLI 명령을 (앞에서 정의한) 사용자 지정 미리 설정에 따라 변환을 만듭니다. 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

지정 된 비디오 또는 오디오에 변환을 적용 하려면 Media Services에 대 한 Transform에 따라 작업을 제출 해야 합니다. 변환에서 작업을 제출 하는 방법을 보여 주는 전체 예제를 참조 하세요. [빠른 시작: 비디오 파일-CLI Stream](stream-files-cli-quickstart.md)합니다.

## <a name="see-also"></a>참고 항목

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
