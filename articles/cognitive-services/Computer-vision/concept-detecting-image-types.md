---
title: 이미지 형식 검색 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API의 이미지 형식 검색 기능과 관련된 개념입니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: a658f4ea604ae7ab29d7657ba2c89c62a0108ba2
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109714749"
---
# <a name="detecting-image-types-with-computer-vision"></a>Computer Vision으로 이미지 형식 검색

[Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API를 사용하면 Computer Vision이 이미지의 콘텐츠 형식을 분석하여 이미지가 클립 아트인지 또는 선 그리기인지 표시할 수 있습니다.

## <a name="detecting-clip-art"></a>클립 아트 검색

Computer Vision은 다음 표에 설명된 대로 이미지를 분석하고, 이미지가 클립 아트인 가능성을 0부터 3까지로 평가합니다.

| 값 | 의미 |
|-------|---------|
| 0 | 클립 아트 아님 |
| 1 | 모호함 |
| 2 | 보통 클립 아트 |
| 3 | 좋은 클립 아트 |

### <a name="clip-art-detection-examples"></a>클립 아트 검색 예제

다음 JSON 응답에서는 예제 이미지가 클립 아트일 가능성을 평가할 때 Computer Vision이 반환하는 내용을 보여줍니다.

![치즈 조각의 클립 아트 이미지](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![파란색 집 및 앞 마당](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>선 그리기 검색

Computer Vision는 이미지를 분석하고 이미지가 선 그리기인지 여부를 나타내는 부울 값을 반환합니다.

### <a name="line-drawing-detection-examples"></a>선 그리기 검색 예제

다음 JSON 응답에서는 예제 이미지가 선 그리기인지 나타낼 때 Computer Vision이 반환하는 내용을 보여줍니다.

![사자의 선 그리기 이미지](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![녹색 배경의 흰색 꽃](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>API 사용

이미지 형식 검색 기능은 [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) API의 일부입니다. 이 API는 네이티브 SDK 또는 REST 호출을 통해 호출할 수 있습니다. **visualFeatures** 쿼리 매개 변수에 `ImageType`을 포함합니다. 그런 다음 전체 JSON 응답을 받으면 `"imageType"` 섹션의 내용에 대한 문자열을 구문 분석하기만 하면 됩니다.

* [빠른 시작: Computer Vision REST API 또는 클라이언트 라이브러리](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
