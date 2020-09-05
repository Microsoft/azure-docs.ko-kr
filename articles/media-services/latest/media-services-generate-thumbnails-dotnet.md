---
title: .NET에서 Azure Media Services Encoder Standard를 사용 하 여 미리 보기를 생성 하는 방법
description: 이 문서에서는 .NET을 사용 하 여 자산을 인코딩하고 동시에 Media Encoder Standard를 사용 하 여 미리 보기를 생성 하는 방법을 보여 줍니다.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e4ad7ba75edd1899cbe2d7cd7d3b1b6c124ce35
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267652"
---
# <a name="how-to-generate-thumbnails-using-encoder-standard-with-net"></a>.NET에서 인코더 표준을 사용 하 여 미리 보기를 생성 하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Encoder Standard를 사용하여 입력 동영상에서 하나 이상의 미리 보기를 [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics) 또는 [BMP](https://en.wikipedia.org/wiki/BMP_file_format) 이미지 파일 형식으로 생성할 수 있습니다.

## <a name="recommended-reading-and-practice"></a>권장 읽기 및 연습

사용자 [지정 변환-.net을 사용 하 여 인코딩하는 방법을](customize-encoder-presets-how-to.md)읽으면 사용자 지정 변환을 익히는 것이 좋습니다.

## <a name="transform-code-example"></a>코드 변환 예제

아래 코드 예제에서는 축소판 그림만 만듭니다.  다음 매개 변수를 설정 해야 합니다.

- **start** -축소판 그림 생성을 시작할 입력 비디오의 위치입니다. 값은 ISO 8601 형식 (예: 5 초에 시작 하는 PT05S) 또는 프레임 수 (10, 10, 10, 10%)의 스트림 기간에 대 한 상대 값 (예: 10% (스트림 기간의 10%)으로 시작 하는 경우)이 될 수 있습니다. 는 인코더에 비디오의 처음 몇 초에서 가장 적합 한 미리 보기를 선택 하도록 지시 하 고, 단계 및 범위에 대 한 다른 설정에 상관 없이 축소판 그림을 하나만 생성 하는 매크로 {Best}도 지원 합니다. 기본값은 매크로 {Best}입니다.
- **단계** -미리 보기가 생성 되는 간격입니다. 값은 ISO 8601 형식 (예: 5 초 마다 하나의 이미지에 대 한 PT05S) 이나 프레임 수 (예: 30 프레임 마다 이미지의 경우 30 개) 또는 스트림 기간에 대 한 상대 값 (예: 스트림 기간의 10% 마다 한 이미지의 경우 10%) 일 수 있습니다. 단계 값은 처음 생성 된 축소판 그림에 영향을 줍니다 .이 축소판 그림은 변환 기본 설정 시작 시간에 지정 된 축소판 그림에 정확 하지 않을 수 있습니다. 인코더 때문입니다 .이는 시작 시간에서 시작 시간 사이에 가장 적합 한 축소판 그림을 첫 번째 출력으로 선택 하려고 시도 합니다. 기본값은 10% 이므로 스트림의 기간이 긴 경우 처음 생성 된 축소판 그림은 시작 시 지정 된 축소판 그림에서 멀리 떨어져 있을 수 있음을 의미 합니다. 첫 번째 축소판 그림이 시작 시간에 근접 하 게 예상 되는 경우 단계에 적합 한 값을 선택 하 고 시작 시간에 하나의 미리 보기만 필요한 경우 범위 값을 1로 설정 합니다.
- **범위** -미리 보기 생성을 중지할 입력 비디오의 미리 설정 시작 시간을 기준으로 하는 위치입니다. 값은 ISO 8601 형식 (예: 시작 시간에서 5 분에서 30 초에 PT5M30S) 또는 프레임 수 (300 예: 시작 시 프레임에서 1/300 프레임에서 중지 하는 경우) 일 수 있습니다. 이 값이 1 인 경우에는 시작 시간에 하나의 축소판 그림을 생성 하거나 스트림 기간에 대 한 상대 값 (예: 시작 시간에서 스트림 기간의 절반에서 중지 하는 50%)을 나타냅니다. 기본값은 100% 이며 스트림의 끝에서 중지 하는 것을 의미 합니다.
- **레이어** -인코더에서 생성할 출력 이미지 계층의 컬렉션입니다.

```csharp

private static Transform EnsureTransformExists(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = client.Transforms.Get(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // Create a new Transform Outputs array - this defines the set of outputs for the Transform
        TransformOutput[] outputs = new TransformOutput[]
        {
            // Create a new TransformOutput with a custom Standard Encoder Preset
            // This demonstrates how to create custom codec and layer output settings

          new TransformOutput(
                new StandardEncoderPreset(
                    codecs: new Codec[]
                    {
                        // Generate a set of PNG thumbnails
                        new PngImage(
                            start: "25%",
                            step: "25%",
                            range: "80%",
                            layers: new PngLayer[]{
                                new PngLayer(
                                    width: "50%",
                                    height: "50%"
                                )
                            }
                        )
                    },
                    // Specify the format for the output files for the thumbnails
                    formats: new Format[]
                    {
                        new PngFormat(
                            filenamePattern:"Thumbnail-{Basename}-{Index}{Extension}"
                        )
                    }
                ),
                onError: OnErrorType.StopProcessingJob,
                relativePriority: Priority.Normal
            )
        };

        string description = "A transform that includes thumbnails.";
        // Create the custom Transform with the outputs defined above
        transform = client.Transforms.CreateOrUpdate(resourceGroupName, accountName, transformName, outputs, description);
    }

    return transform;
}
```

## <a name="next-steps"></a>다음 단계
[REST를 사용 하 여 미리 보기 생성](media-services-generate-thumbnails-rest.md)
