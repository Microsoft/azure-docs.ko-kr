---
title: HeadPose 특성 사용
titleSuffix: Azure Cognitive Services
description: HeadPose 특성을 사용하여 자동으로 얼굴 사각형을 회전시키거나 비디오 피드에서 머리 제스처를 감지하는 방법을 알아봅니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 05/29/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: fe9cc44542e97880b076d871dde82f5a02a58957
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928975"
---
# <a name="use-the-headpose-attribute"></a>HeadPose 특성 사용

이 가이드에서는 감지된 얼굴의 HeadPose 특성을 사용하여 몇 가지 주요 시나리오를 활성화하는 방법을 알아봅니다.

## <a name="rotate-the-face-rectangle"></a>얼굴 사각형 회전

감지된 모든 얼굴과 함께 반환되는 얼굴 사각형은 이미지에 있는 얼굴의 위치와 크기를 표시합니다. 기본적으로 사각형은 항상 이미지와 맞춥니다(측면은 세로 및 가로임). 이는 기울어진 얼굴을 프레이밍하는 데 비효율적일 수 있습니다. 이미지에서 얼굴을 프로그래밍 방식으로 자르려면 사각형을 회전하여 자르는 것이 좋습니다.

[Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 샘플 앱은 HeadPose 특성을 사용하여 감지된 얼굴 사각형을 회전시킵니다.

### <a name="explore-the-sample-code"></a>샘플 코드 탐색

HeadPose 특성을 사용하여 얼굴 사각형을 프로그래밍 방식으로 회전시킬 수 있습니다. 얼굴을 감지할 때 이 특성을 지정하면([얼굴 감지 방법](HowtoDetectFacesinImage.md) 참조) 나중에 이를 쿼리할 수 있습니다. [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 앱의 다음 메서드는 **DetectedFace** 개체 목록을 가져와서 **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)** 개체 목록을 반환합니다. 여기서 **Face**는 업데이트된 사각형 좌표를 포함하여 얼굴 데이터를 저장하는 사용자 지정 클래스입니다. **top**, **left**, **width** 및 **height**에 대한 새 값이 계산되며, 새 **FaceAngle** 필드에서 회전을 지정합니다.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>업데이트된 사각형 표시

여기서는 반환된 **Face** 개체를 표시에 사용할 수 있습니다. [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml)의 다음 줄에서는 새 사각형이 이 데이터에서 렌더링되는 방법을 보여 줍니다.

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>머리 제스처 감지

HeadPose 변경을 실시간으로 추적하여 머리 끄덕임 및 흔들기와 같은 머리 제스처를 감지할 수 있습니다. 이 기능을 사용자 지정 활동성 감지기로 사용할 수 있습니다.

활동성 감지는 주체가 이미지 또는 비디오 표현이 아니라 실제 사람인지 확인하는 작업입니다. 머리 제스처 감지기는 특히 사람의 이미지 표현과는 달리 활동성을 확인하는 데 도움이 되는 한 가지 방법으로 사용할 수 있습니다.

> [!CAUTION]
> 머리 제스처를 실시간으로 감지하려면 Face API를 높은 속도로 호출해야 합니다(초당 2회 이상). 체험 계층(f0) 구독이 있는 경우 이 방법을 사용할 수 없습니다. 유료 계층 구독이 있는 경우 머리 제스처 감지에 대한 빠른 API 호출에 드는 비용이 계산되었는지 확인합니다.

머리 제스처 감지에 대한 작업 예제는 GitHub의 [Face HeadPose 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample)을 참조하세요.

## <a name="next-steps"></a>다음 단계

회전된 얼굴 사각형에 대한 작업 예제는 GitHub의 [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) 앱을 참조하세요. 또는 HeadPose 특성을 실시간으로 추적하여 머리 움직임을 감지하는 [Face HeadPose 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) 앱을 참조하세요.