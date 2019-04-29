---
title: Azure Media Services의 LiveEvent 대기 시간 | Microsoft Docs
description: 이 토픽에서는 LiveEvent 대기 시간에 대해 간략히 설명하고 짧은 대기 시간을 설정하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 04/22/2019
ms.author: juliako
ms.openlocfilehash: 393b87aeed759950b946ccb45a008da9af4b7ebe
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766850"
---
# <a name="live-event-latency-in-media-services"></a>Media Services의 라이브 이벤트 대기 시간

이 문서에서는 [라이브 이벤트](https://docs.microsoft.com/rest/api/media/liveevents)에서 짧은 대기 시간을 설정하는 방법을 보여줍니다. 또한 다양한 플레이어에서 짧은 대기 시간 설정을 사용하는 경우 표시되는 일반적인 결과에 대해 설명합니다. 그 결과는 CDN 및 네트워크 대기 시간에 따라 다릅니다.

새로운 **LowLatency** 기능을 사용하려면 **LiveEvent**에서 **StreamOptionsFlag**를 **LowLatency**로 설정할 수 있습니다. HLS 재생에 대한 [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs)을 만들 때 [LiveOutput.Hls.fragmentsPerTsSegment](https://docs.microsoft.com/rest/api/media/liveoutputs/create#hls)를 1로 설정합니다. 스트림이 실행되면 [Azure Media Player](https://ampdemo.azureedge.net/)(AMP) 데모 페이지를 사용할 수 있으며, 재생 옵션을 설정하여 "짧은 대기 시간 추론 프로필"을 사용할 수 있습니다.

> [!NOTE]
> 현재 Azure Media Player에서 LowLatency HeuristicProfile 용인지 CSF 또는 CMAF 형식으로 사용 하 여 MPEG DASH 프로토콜에서 스트림을 재생 (예를 들어 `format=mdp-time-csf` 또는 `format=mdp-time-cmaf`). 

다음 .NET 예제에서는 **LiveEvent**에서 **LowLatency**를 설정하는 방법을 보여줍니다.

```csharp
LiveEvent liveEvent = new LiveEvent(
            location: mediaService.Location, 
            description: "Sample LiveEvent for testing",
            vanityUrl: false,
            encoding: new LiveEventEncoding(
                        // Set this to Standard to enable a transcoding LiveEvent, and None to enable a pass-through LiveEvent
                        encodingType:LiveEventEncodingType.None, 
                        presetName:null
                    ),
            input: new LiveEventInput(LiveEventInputProtocol.RTMP,liveEventInputAccess), 
            preview: liveEventPreview,
            streamOptions: new List<StreamOptionsFlag?>()
            {
                // Set this to Default or Low Latency
                // To use low latency optimally, you should tune your encoder settings down to 1 second "Group Of Pictures" (GOP) length instead of 2 seconds.
                StreamOptionsFlag.LowLatency
            }
        );
```                

전체 목록을 참조하세요. [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

## <a name="live-events-latency"></a>라이브 이벤트 대기 시간

다음 표는 기여 피드가 서비스에 도달하는 시점부터 뷰어가 플레이어에서 재생을 확인하는 시점 사이를 측정한 Media Services의 일반적인 대기 시간 결과를 보여줍니다(LowLatency 플래그를 사용하는 경우). 짧은 대기 시간을 사용하려면 인코더 설정을 1초 GOP(Group of Pictures) 길이로 조정해야 합니다. 더 높은 GOP 길이를 사용하면 동일한 프레임에서 대역폭 사용량이 최소화되고 비트 전송률이 낮아집니다. 움직임이 많지 않은 비디오에 특히 유용합니다.

### <a name="pass-through"></a>통과 

||2초 GOP 짧은 대기 시간 사용|1초 GOP 짧은 대기 시간 사용|
|---|---|---|
|AMP의 DASH|10초|8초|
|기본 iOS 플레이어의 HLS|14초|10초|

### <a name="live-encoding"></a>라이브 인코딩

||2초 GOP 짧은 대기 시간 사용|1초 GOP 짧은 대기 시간 사용|
|---|---|---|
|AMP의 DASH|14초|10초|
|기본 iOS 플레이어의 HLS|18초|13초|

> [!NOTE]
> 로컬 네트워크 조건에 따라 또는 CDN 캐싱 계층을 도입하여 엔드투엔드 대기 시간이 달라질 수 있습니다. 정확한 구성을 테스트해야 합니다.

## <a name="next-steps"></a>다음 단계

- [라이브 스트리밍 개요](live-streaming-overview.md)
- [라이브 스트리밍 자습서](stream-live-tutorial-with-api.md)

