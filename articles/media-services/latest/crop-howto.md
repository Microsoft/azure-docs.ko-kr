---
title: Media Services를 사용하여 비디오 파일을 자르는 방법 -.NET | Microsoft Docs
description: 자르기는 비디오 프레임 내에서 사각형 창을 선택하는 과정이며 해당 창 내의 픽셀을 인코딩합니다. 이 항목에서는 .NET을 사용하여 Media Services으로 비디오 파일을 자르는 방법을 보여 줍니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 43d0e1e3b8c0eaa37a3b09557b333c3abafe8b63
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105572437"
---
# <a name="how-to-crop-video-files-with-media-services---net"></a>Media Services를 사용하여 비디오 파일을 자르는 방법 - .NET

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services를 사용하여 입력 비디오를 자를 수 있습니다. 자르기는 비디오 프레임 내에서 사각형 창을 선택하는 과정이며 해당 창 내의 픽셀을 인코딩합니다. 다음 다이어그램은 프로세스를 설명하는 데 도움이 됩니다.

## <a name="pre-processing-stage"></a>전처리 단계

자르기는 전처리 단계이므로 인코딩 사전 설정의 ‘자르기 매개 변수’를 원래 ‘입력’ 비디오에 적용합니다. Encoding은 후속 단계이며 너비/높이 설정은 원본 비디오가 아닌 *전처리된* 비디오에 적용됩니다. 사전 설정을 디자인할 때 다음을 수행합니다.

1. 원본 입력 비디오를 기준으로 하는 자르기 매개 변수를 선택합니다.
1. 자른 비디오를 기준으로 인코딩 설정을 선택합니다.

> [!WARNING]
> 인코딩 설정이 자른 비디오와 일치하지 않는 경우 예상한 대로 출력되지 않습니다.

예를 들어, 해상도가 1920x1080픽셀(16:9 가로 세로 비율)인 비디오를 입력으로 포함하고 왼쪽 및 오른쪽에 검은색 막대가 있으므로 4:3 창 또는 1440x1080픽셀만 실제 비디오를 포함합니다. 검은색 막대를 자르고 1440x1080 영역을 인코딩할 수 있습니다.

## <a name="transform-code"></a>코드 변환

다음 코드 조각에서는 .NET에서 변환을 작성하여 비디오를 자르는 방법을 보여 줍니다.  이 코드에서는 사용할 로컬 파일이 있다고 가정합니다.

- Left는 자르기의 맨 왼쪽 위치입니다.
- Top은 자르기의 맨 위 위치입니다.
- Width는 자르기의 최종 너비입니다.
- Height는 자르기의 최종 높이입니다.

```dotnet
var preset = new StandardEncoderPreset

    {

        Filters = new Filters

        {                   

            Crop = new Rectangle

            {

                Left = "200",

                Top = "200",

                Width = "1280",

                Height = "720"

            }

        },

        Codecs =

        {

            new AacAudio(),

            new H264Video()

            {

                Layers =

                {                           

                    new H264Layer

                    {

                        Bitrate = 1000000,

                        Width = "1280",

                        Height = "720"

                    }

                }

            }

        },

        Formats =

        {

            new Mp4Format

            {

                FilenamePattern = "{Basename}_{Bitrate}{Extension}"

            }

        }

    }

```
