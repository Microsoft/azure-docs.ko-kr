---
title: 콘텐츠를 인식 인코딩-의 실험적 미리 설정 된 Azure | Microsoft Docs
description: 이 문서에서는 Azure Media Services에서 콘텐츠를 인식 인코딩을 설명합니다
services: media-services
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: sethm
ms.custom: ''
ms.openlocfilehash: 3c50502a8b873503ee937914fac5f2d92cb23a2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733489"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>콘텐츠를 인식 하는 인코딩 사전 설정을 실험적에 있었음

배달 하기 위해 콘텐츠를 준비 하기 위해 [적응 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), 비디오를 다중 비트 전송률 (낮은 높음)에서 인코딩할 수 해야 합니다. 정상적인 저하 품질을 보장 하기 위해 비트 전송률을 줄일 수 있으므로 비디오의 해상도입니다. 이 인해 소위 인코딩 사다리 – 테이블의 해상도 및 비트 전송률을 알 수 있듯이 고정된이 인코딩 사전 설정 중 일부와 같은 [H264MultipleBitrate1080p](../previous/media-services-mes-preset-h264-multiple-bitrate-1080p.md)합니다.

## <a name="overview"></a>개요

1-기본 설정-알맞은-all-비디오 방식을 넘어 이동 하는 관심이 증가 Netflix 게시 된 후 해당 [블로그](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) 2015 년 12 월. 이후 콘텐츠 인식 인코딩에 대 한 여러 솔루션에에서 출시 된 marketplace 참조 [이 문서에서는](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) 에 대 한 개요입니다. 사용자 지정 하거나 개별 비디오의 복잡성에 인코딩 사다리 조정 콘텐츠-알아야 할 개념이입니다. 각 해상도 초과 perceptive 품질 증가 시 키 지 않습니다.-이 최적의 비트 전송률 값에 작동 하는 인코더 비트 전송률이 있습니다. 다음 수준의 최적화 콘텐츠에 따라 해상도 선택 하는 것 – 예를 들어 PowerPoint 프레젠테이션 비디오의 이점은 없습니다 720p 아래 나 가지에서입니다. 계속 진행, 인코더 수 하는 작업을 동영상 내에서 각 샷 설정을 최적화 합니다. 설명 하는 Netflix [이러한 접근 방식을](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) 2018 년에 있습니다.

2017 년 초 Microsoft 출시를 [적응 스트리밍](autogen-bitrate-ladder.md) 품질에서 가변성 문제 및 원본 비디오의 해상도 해결 하기 위해 미리 설정 합니다. 고객에 게 다양 한 콘텐츠, 1080p에서 일부, 720p, 다른 사용자와 혼합 SD와 낮은 해상도에서 몇 가지 했습니다. 또한 일부 원본 콘텐츠는 영화 또는 TV 스튜디오에서 고품질 mezzanines 했습니다. 미리 설정 된 주소를 이러한 문제 스트리밍 비트 전송률 사다리 되지을 초과 하는 해결 방법이 나 입력된 중 2 층의 평균 비트 전송률을 적응 합니다.

실험적 콘텐츠 인식 인코딩 미리 설정을 인코더 광범위 한 컴퓨팅 분석 요구 하지 않고 지정된 된 해상도 대 한 최적의 비트 전송률 값을 검색할 수 있는 사용자 지정 논리를 통합 하 여이 메커니즘을 확장 합니다. Net 결과이 새 미리 설정 적응 스트리밍 사전 설정을 보다 하지만 더 높은 품질로 더 낮은 비트 전송률 있는 출력을 생성 합니다. 와 같은 품질 메트릭을 사용 하 여 비교를 보여주는 다음 샘플 그래프를 볼 [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 하 고 [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)합니다. Movies에서 높은 복잡성 샷 짧은 클립을 연결 하 여 생성 된 원본과 인코더 스트레스 데 tv, 합니다. 정의상,이 사전 결과에서 콘텐츠를 콘텐츠 – 달라 지는 또한 의미는 일부 내용은 없을 비트 전송률에 상당한 감소 또는 품질에서 개선 합니다.

![PSNR를 사용 하 여 속도-왜곡 (RD) 곡선](media/cae-experimental/msrv1.png)

**그림 1: PSNR 메트릭을 사용 하 여 높은 복잡성 원본에 대 한 속도-왜곡 (RD) 곡선**

![VMAF를 사용 하 여 속도-왜곡 (RD) 곡선](media/cae-experimental/msrv2.png)

**그림 2: VMAF 메트릭을 사용 하 여 높은 복잡성 원본에 대 한 속도-왜곡 (RD) 곡선**

현재는 사전 설정 높은 복잡성에 대 한 조정 된 고품질 원본 비디오 (영화, tv). 작업 저하 품질 비디오 및 낮은 복잡성 콘텐츠 (예: PowerPoint 프레젠테이션)에 맞게 진행 중입니다. 또한이 사전 설정은 적응 스트리밍 사전 설정으로 해상도의 동일한 집합을 사용 합니다. Microsoft는 콘텐츠에 따라 해결 최소 집합을 선택 하는 방법 노력 합니다. 다음과 같이 인코더에서 품질이 (낮은 비트 전송률 인해 많은 압축 아티팩트)를 입력 했는지 확인할 수 있는 소스 콘텐츠를 다른 범주에 대 한 결과입니다. 인코더는 대부분의 클라이언트는 상태일 없이 스트림을 재생할 수 있도록 하나의 출력 계층 – 충분히 낮은 비트 전송률로 생성 하기로 실험적으로 사전 설정 하는 참고 합니다.

![PSNR를 사용 하 여 RD 곡선](media/cae-experimental/msrv3.png)

**그림 3: PSNR 저품질 입력 ((1080p)에서 사용 하 여 RD 곡선**

![VMAF를 사용 하 여 RD 곡선](media/cae-experimental/msrv4.png)

**그림 4: VMAF 저품질 입력 ((1080p)에서 사용 하 여 RD 곡선**

## <a name="use-the-experimental-preset"></a>실험적 사전 설정을 사용합니다

이 같이 사전 설정을 사용 하는 변환을 만들 수 있습니다. 자습서를 사용 하는 경우 [이와 같은](stream-files-tutorial-with-api.md), 다음과 같은 코드를 업데이트할 수 있습니다.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> 접두사 "실험적" 기본 알고리즘은 아직 개발 중인 알리기 위해 사용 됩니다. 논리 강력한 이며 다양 한 입력된 조건에 맞게 조정 하는 알고리즘에 수렴의 목표를 사용 하 여 비트 전송률 사다리를 생성 하는 데는 시간이 지남에 따라 변경 됩니다 하 고 있을 수 있습니다. 인코딩 미리 설정 된이 사용 하 여 작업에 따라 출력 청구 분 되며 DASH 및 HLS와 같은 프로토콜에서 스트리밍 끝점에서 출력 자산을 배달 될 수 있습니다.

## <a name="next-steps"></a>다음 단계

비디오를 최적화 하는이 새 옵션에 대 한 알아보았으며, 이제 사용해 것을 권해 드립니다. 이 문서의 끝에서 링크를 사용 하 여 피드백을 보내주세요 두거나에서 보다 직접 참여 <amsved@microsoft.com>합니다.
