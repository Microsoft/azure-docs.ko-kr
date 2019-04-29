---
title: Azure Media Services를 사용하여 썸네일 스프라이트 생성 | Microsoft Docs
description: 이 항목에서는 Azure Media Services를 사용하여 썸네일 스프라이트를 생성하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e5d32d1bc3bd704b03e58c62251a323ed3f4662c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61229053"
---
# <a name="generate-a-thumbnail-sprite"></a>썸네일 스프라이트 생성  

Media Encoder Standard를 사용하여 VTT 파일과 함께 낮은 해상도의 썸네일이 여러 개 포함된 JPEG 파일인 썸네일 스프라이트를 생성할 수 있습니다. 이 VTT 파일은 큰 JPEG 파일 내의 썸네일 크기 및 좌표와 함께 각 썸네일이 나타내는 입력 비디오의 시간 범위를 지정합니다. 비디오 플레이어는 VTT 파일과 스프라이트 이미지를 사용하여 '시각적' 검색 바를 표시하여 비디오 타임라인을 따라 앞뒤로 움직이면 시청자에게 시각적 피드백을 제공합니다.

Media Encoder Standard를 사용하여 썸네일 스프라이트를 생성하려면 다음과 같이 미리 설정해야 합니다.

1. JPG 썸네일 이미지 형식을 사용해야 합니다.
2. Start/Step/Range(시작/단계/범위) 값을 타임스탬프 또는 % 값으로 지정해야 하며, 프레임 수가 아닙니다. 
    
    1. 타임스탬프와 % 값은 혼합할 수 있습니다.

3. 1보다 크거나 같지 않은 음수가 아닌 숫자로 된 SpriteColumn 값이 있어야 합니다.

    1. SpriteColumn이 M >= 1로 설정된 경우 출력 이미지는 M 열이 있는 사각형입니다. #2를 통해 생성된 썸네일 이미지의 수가 M의 정확한 배수가 아닌 경우 마지막 행이 완전하지 않고 검은색 픽셀로 남습니다.  

다음은 예제입니다.

```json
{
    "Version": 1.0,
    "Codecs": [
    {
      "Start": "00:00:01",
      "Type": "JpgImage",
      "Step": "5%",
      "Range": "100%",
      "JpgLayers": [
        {
          "Type": "JpgLayer",
          "Width": "10%",
          "Height": "10%",
          "Quality": 90
        }
      ],
      "SpriteColumn": 10
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

## <a name="known-issues"></a>알려진 문제

1.  이미지의 단일 행이 있는 스프라이트 이미지는 생성할 수 없습니다(SpriteColumn = 1은 단일 열이 있는 이미지를 생성함).
2.  스프라이트 이미지를 중간 크기의 JPEG 이미지의 청크로 분할하는 것은 아직 지원되지 않습니다. 따라서 썸네일 이미지의 수와 해당 크기를 제한하여 결과적으로 이어진 썸네일 스프라이트가 약 8M 픽셀 이하가 되도록 주의해야 합니다.
3.  Azure Media Player는 Microsoft Edge, Chrome 및 Firefox 브라우저에서 스프라이트를 지원합니다. IE11에서는 VTT 구문 분석이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[콘텐츠 인코딩](media-services-encode-asset.md)
