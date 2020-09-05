---
title: 콘텐츠 인식 인코딩에 대 한 사전 설정-Azure Media Services
description: 이 문서에서는 Microsoft Azure Media Services v3의 콘텐츠 인식 인코딩에 대해 설명 합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 853381dbccea5374806fbeadb1d29632f0525fdb
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265612"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>콘텐츠 인식 인코딩 미리 설정을 사용 하 여 지정 된 해상도에 대 한 최적의 비트 전송률 값을 찾습니다.

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[적응 비트 전송률 스트리밍을](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)통해 콘텐츠를 준비할 수 있도록 하려면 비디오를 여러 비트 전송률 (높음-낮음)으로 인코딩해야 합니다. 이렇게 하면 비트 전송률이 감소 하므로 비디오의 해상도는 품질의 정상 저하를 보장할 수 있습니다. 이러한 다중 비트 전송률 인코딩은 해상도 및 비트 전송률의 테이블을 사용 하 여이에 대 한 인코딩 사다리를 사용 합니다. [기본 제공 인코딩 기본 설정](/rest/api/media/transforms/createorupdate#encodernamedpreset)Media Services을 참조 하세요.

처리 중인 콘텐츠를 파악 하 고 개별 비디오의 복잡도에 맞게 인코딩 사다리를 사용자 지정/조정 해야 합니다. 각 해상도에서 품질 증가는 perceptive 하지 않는 비트 전송률이 있습니다. 인코더는이 최적의 비트 전송률 값에서 작동 합니다. 다음 수준의 최적화는 콘텐츠를 기반으로 해상도를 선택 하는 것입니다. 예를 들어 PowerPoint 프레젠테이션 비디오에서는 720p를 사용 하는 것이 좋습니다. 더 나아가 인코더는 비디오 내에서 각 샷의 설정을 최적화 하는 일을 할 수 있습니다. 

Microsoft의 [적응 스트리밍](autogen-bitrate-ladder.md) 사전 설정은 원본 비디오의 품질 및 해상도에 대 한 가변성 문제를 부분적으로 해결 합니다. 고객은 다양 한 콘텐츠를 포함 하는 다양 한 콘텐츠를 포함 하 고 있습니다. 일부는 1080p로, 다른 하나는 SD 및 낮은 해상도입니다. 또한 모든 소스 콘텐츠가 필름 또는 TV 스튜디오의 고품질 mezzanines입니다. 적응 스트리밍 사전 설정은 비트 전송률 사다리가 입력 된 메자닌의 해상도 또는 평균 비트 전송률을 초과 하지 않도록 하 여 이러한 문제를 해결 합니다. 그러나이 사전 설정은 해상도 및 비트 전송률 이외의 원본 속성을 검사 하지 않습니다.

## <a name="the-content-aware-encoding"></a>내용 인식 인코딩 

콘텐츠 인식 인코딩 사전 설정은 인코더가 지정 된 해상도에 대해 최적의 비트 전송률 값을 검색할 수 있도록 하는 사용자 지정 논리를 통합 하 여 "적응 비트 전송률 스트리밍" 메커니즘을 확장 하지만 광범위 한 계산 분석이 필요 하지 않습니다. 이 사전 설정은 GOP 정렬 Mp4 집합을 생성 합니다. 입력 콘텐츠를 제공 하는 경우 서비스는 입력 콘텐츠에 대 한 초기 경량 분석을 수행 하 고 결과를 사용 하 여 최적의 계층 수, 적절 한 비트 전송률 및 적응 스트리밍을 통해 배달할 해상도 설정을 결정 합니다. 이 사전 설정은 저렴 하 고 보통 수준의 복잡 한 비디오에 특히 효과적입니다 .이 경우 출력 파일은 적응 스트리밍 사전 설정 보다 더 낮은 비트 전송률이 고 여전히 좋은 경험을 제공 하는 품질로 제공 됩니다. 출력에는 비디오 및 오디오가 있는 MP4 파일이 포함 됩니다.

다음 샘플 그래프는 [Psnr](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) 및 [vmaf](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion)와 같은 품질 메트릭을 사용 하 여 비교를 보여줍니다. 소스는 인코더를 스트레스 하기 위해 영화 및 TV 쇼에서 복잡성이 높은 샷의 짧은 클립을 연결 하 여 만들어졌습니다. 정의에 따라이 미리 설정은 콘텐츠와 콘텐츠가 다른 결과를 생성 합니다. 또한 일부 콘텐츠의 경우 비트 전송률 또는 품질 향상을 크게 줄일 수 없습니다.

![PSNR을 사용 하는 TS (요율 비틀기) 곡선](media/content-aware-encoding/msrv1.png)

**그림 1: 높은 복잡성의 원본에 대 한 PSNR 메트릭을 사용 하는 RD (요율 비틀기) 곡선**

![VMAF를 사용 하는 TS (요율 비틀기) 곡선](media/content-aware-encoding/msrv2.png)

**그림 2: 높은 복잡성의 원본에 대 한 VMAF 메트릭을 사용 하는 TS (요율 비틀기) 곡선**

다음은 다른 범주의 소스 콘텐츠에 대 한 결과입니다. 여기서 인코더는 입력의 품질이 저하 되었음을 확인할 수 있었습니다 (비트 전송률이 낮은 경우 많은 압축 아티팩트). 콘텐츠 인식 사전 설정을 사용 하는 경우 인코더는 하나의 출력 계층도 생성 하기로 결정 했습니다. 따라서 대부분의 클라이언트는 상태일 없이 스트림을 재생할 수 있습니다.

![PSNR을 사용 하는 RD 곡선](media/content-aware-encoding/msrv3.png)

**그림 3: 낮은 품질의 입력에 PSNR을 사용 하는 RD 곡선 (1080p)**

![VMAF를 사용 하는 RD 곡선](media/content-aware-encoding/msrv4.png)

**그림 4: 낮은 품질의 입력을 위해 VMAF를 사용 하는 RD 곡선 (1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>내용 인식 인코딩 미리 설정을 사용 하는 방법 

다음과 같이이 사전 설정을 사용 하는 변환을 만들 수 있습니다. 

변환 출력을 사용 하는 자습서는 [다음 단계](#next-steps) 섹션을 참조 하세요. 출력 자산은 자습서에 표시 된 것 처럼 MPEG-2 및 HLS와 같은 프로토콜의 Media Services 스트리밍 끝점에서 배달할 수 있습니다.

> [!NOTE]
> **ContentAwareEncoding** 사전 설정 ContentAwareEncodingExperimental을 사용 해야 합니다.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> 미리 설정을 사용 하는 인코딩 작업 `ContentAwareEncoding` 은 출력 시간 (분)을 기준으로 요금이 청구 됩니다. 
  
## <a name="next-steps"></a>다음 단계

* [자습서: Media Services v3로 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
* [자습서: URL을 기준으로 원격 파일 인코딩 및 비디오 스트림-CLI](stream-files-cli-quickstart.md)
* [자습서: URL을 기반으로 원격 파일 인코딩 및 비디오 스트리밍-.NET](stream-files-dotnet-quickstart.md)
* [자습서: URL을 기준으로 원격 파일 인코딩 및 비디오 스트림 Node.js](stream-files-nodejs-quickstart.md)
