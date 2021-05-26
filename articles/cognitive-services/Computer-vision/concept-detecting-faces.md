---
title: 얼굴 감지 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 얼굴 감지 기능과 관련된 개념을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a77a8e72dcbd74eb05340e7dd48d3ab6516f8043
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110465420"
---
# <a name="face-detection-with-computer-vision"></a>Computer Vision으로 얼굴 감지

Computer Vision은 이미지에서 사람 얼굴을 감지하고 감지된 각 얼굴에 대해 연령, 성별 및 사각형을 생성합니다. 

> [!NOTE]
> 이 기능은 Azure [Face](../face/index.yml) 서비스를 통해서도 제공됩니다. 얼굴 인식 및 자세 감지를 비롯한 더 자세한 얼굴 감지에 대해서는 이 대안을 참조하세요. 

## <a name="face-detection-examples"></a>얼굴 감지 예제

다음 예제는 사람 얼굴이 하나인 이미지에 대해 Computer Vision에서 반환된 JSON 응답을 보여줍니다.

![비전 분석 여성 지붕 얼굴](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

다음 예제는 사람 얼굴이 여러 개인 이미지에 대해 반환된 JSON 응답을 보여줍니다.

![비전 분석 가족 사진 얼굴](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>API 사용

얼굴 감지 기능은 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. **visualFeatures** 쿼리 매개 변수에 `Faces`을 포함합니다. 그런 다음 전체 JSON 응답을 받으면 `"faces"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/image-analysis-client-library.md?pivots=programming-language-csharp)
