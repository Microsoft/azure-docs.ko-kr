---
title: Media Services v3 Azure CLI를 사용 하 여 사용자 지정 변환 인코딩 Microsoft Docs
description: 이 항목에서는 Azure CLI를 사용 하 여 사용자 지정 변환을 인코딩하는 Azure Media Services v3을 사용 하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f7b1f7a858c465629e075bcdb6d32bec29863f0a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267890"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>사용자 지정 변환-Azure CLI를 사용 하 여 인코딩하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용 하 여 인코딩하면 [스트리밍 파일](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) 퀵 스타트에 설명 된 대로 업계 모범 사례를 기반으로 권장 되는 기본 제공 사전 설정 중 하나를 사용 하 여 빠르게 시작할 수 있습니다. 특정 시나리오 또는 장치 요구 사항을 대상으로 하는 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용 됩니다.

* AVC 콘텐츠의 높이 및 너비에 대 한 모든 값은 4의 배수 여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 전송률은 초당 비트 단위입니다. 이는 킬로 비트/초를 단위로 사용한 v2 Api와는 다릅니다. 예를 들어 v 2의 비트 전송률이 128 (k b/초)로 지정 된 경우 v3에서 128000 (비트/초)로 설정 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[Media Services 계정 만들기](./create-account-howto.md)

리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>사용자 지정 사전 설정 정의

다음 예제에서는 새 변환의 요청 본문을 정의 합니다. 이 변환이 사용 될 때 생성 하려는 출력 집합을 정의 합니다.

이 예제에서는 먼저 오디오 인코딩에 대해 AacAudio 계층을 추가 하 고, 비디오 인코딩에는 두 개의 경우 h264video 계층을 추가 합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당 합니다. 다음에는 출력에 미리 보기도 포함 하려고 합니다. 아래 예제에서는 입력 비디오의 해상도는 50%로 생성 되 고 입력 비디오의 길이는 {25%, 50%, 75} 인 3 개의 타임 스탬프에 생성 된 이미지를 PNG 형식으로 지정 합니다. 마지막으로, 출력 파일에 대 한 형식을 지정 합니다. 즉, 비디오 + 오디오의 경우, 다른 하나는 미리 보기로 지정 합니다. 여러 H264Layers 있으므로 계층 당 고유한 이름을 생성 하는 매크로를 사용 해야 합니다. `{Label}`또는 매크로를 사용할 수 있습니다 `{Bitrate}` .이 예제에서는 이전을 보여 줍니다.

이 변환을 파일에 저장할 예정입니다. 이 예제에서는 파일의 이름을로 `customPreset.json` 합니다.

```json
{
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
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>새 변환 만들기  

이 예제에서는 앞에서 정의한 사용자 지정 미리 설정을 기반으로 하는 **변환을** 만듭니다. 변환을 만들 때 먼저 이미 존재 하는지 확인 해야 합니다. 변환이 있는 경우 다시 사용 합니다. 다음 `show` 명령은 변환이 있는 경우이를 반환 합니다 `customTransformName` .

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

다음 Azure CLI 명령은 사용자 지정 사전 설정 (이전에 정의 됨)을 기반으로 변환을 만듭니다.

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

지정 된 비디오 또는 오디오에 변환을 적용 하는 Media Services 해당 변환에서 작업을 제출 해야 합니다. 변환 하에서 작업을 제출 하는 방법을 보여 주는 전체 예제는 빠른 시작 [: 비디오 파일 스트리밍-Azure CLI](stream-files-cli-quickstart.md)를 참조 하세요.

## <a name="see-also"></a>참고 항목

[Azure CLI](/cli/azure/ams)
