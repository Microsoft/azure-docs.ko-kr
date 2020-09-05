---
title: Media Services v3 REST를 사용 하 여 사용자 지정 변환 인코딩-Azure | Microsoft Docs
description: 이 항목에서는 Azure Media Services v 3을 사용 하 여 REST를 사용 하 여 사용자 지정 변환을 인코딩하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 9bdea0998b5d6b4c10a96d2ef593f46d6b7c02bf
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267006"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>사용자 지정 변환을 사용 하 여 인코딩하는 방법-REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services를 사용 하 여 인코딩하면 [스트리밍 파일](stream-files-tutorial-with-rest.md#create-a-transform) 자습서에서 설명한 대로 업계 모범 사례를 기반으로 권장 되는 기본 제공 기본 설정 중 하나를 사용 하 여 빠르게 시작할 수 있습니다. 특정 시나리오 또는 장치 요구 사항을 대상으로 하는 사용자 지정 사전 설정을 빌드할 수도 있습니다.

## <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용 됩니다.

* AVC 콘텐츠의 높이 및 너비에 대 한 모든 값은 4의 배수 여야 합니다.
* Azure Media Services v3에서 모든 인코딩 비트 전송률은 초당 비트 단위입니다. 이는 킬로 비트/초를 단위로 사용한 v2 Api와는 다릅니다. 예를 들어 v 2의 비트 전송률이 128 (k b/초)로 지정 된 경우 v3에서 128000 (비트/초)로 설정 됩니다.

## <a name="prerequisites"></a>사전 요구 사항 

- [Media Services 계정 만들기](./create-account-howto.md) <br/>리소스 그룹 이름과 Media Services 계정 이름을 기억해 두어야 합니다. 
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)<br/>[Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 

## <a name="define-a-custom-preset"></a>사용자 지정 사전 설정 정의

다음 예제에서는 새 변환의 요청 본문을 정의 합니다. 이 변환이 사용 될 때 생성 하려는 출력 집합을 정의 합니다. 

이 예제에서는 먼저 오디오 인코딩에 대해 AacAudio 계층을 추가 하 고, 비디오 인코딩에는 두 개의 경우 h264video 계층을 추가 합니다. 비디오 레이어에서 출력 파일 이름에 사용할 수 있도록 레이블을 할당 합니다. 다음에는 출력에 미리 보기도 포함 하려고 합니다. 아래 예제에서는 입력 비디오의 해상도는 50%로 생성 되 고 입력 비디오의 길이는 {25%, 50%, 75} 인 3 개의 타임 스탬프에 생성 된 이미지를 PNG 형식으로 지정 합니다. 마지막으로, 출력 파일에 대 한 형식을 지정 합니다. 즉, 비디오 + 오디오의 경우, 다른 하나는 미리 보기로 지정 합니다. 여러 H264Layers 있으므로 계층 당 고유한 이름을 생성 하는 매크로를 사용 해야 합니다. `{Label}`또는 매크로를 사용할 수 있습니다 `{Bitrate}` .이 예제에서는 이전을 보여 줍니다.

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

## <a name="create-a-new-transform"></a>새 변환 만들기  

이 예제에서는 앞에서 정의한 사용자 지정 미리 설정을 기반으로 하는 **변환을** 만듭니다. 변환을 만들 때 먼저 [Get](/rest/api/media/transforms/get) 을 사용 하 여 이미 존재 하는지 확인 해야 합니다. 변환이 있는 경우 다시 사용 합니다. 

다운로드 한 postman의 컬렉션에서 **변환 및 작업** -> **변환 만들기 또는 업데이트**를 선택 합니다.

**PUT** HTTP 요청 메서드는 다음과 유사합니다.

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

**본문** 탭을 선택 하 고 본문을 [앞에서 정의한](#define-a-custom-preset)json 코드로 바꿉니다. 지정 된 비디오 또는 오디오에 변환을 적용 하는 Media Services 해당 변환에서 작업을 제출 해야 합니다.

**보내기**를 선택합니다. 

지정 된 비디오 또는 오디오에 변환을 적용 하는 Media Services 해당 변환에서 작업을 제출 해야 합니다. 변환에서 작업을 제출 하는 방법을 보여 주는 전체 예제는 [자습서: 비디오 파일 스트리밍-REST](stream-files-tutorial-with-rest.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[다른 REST 작업](/rest/api/media/) 을 참조 하세요.
