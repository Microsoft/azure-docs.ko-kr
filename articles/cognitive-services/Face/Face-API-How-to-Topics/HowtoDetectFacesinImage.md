---
title: 이미지에서 얼굴 감지-얼굴
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 얼굴 감지를 사용 하 여 지정 된 이미지에서 성별, age 또는 포즈와 같은 특성을 추출 하는 방법을 보여 줍니다.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 231f30f5532d0934ba41e591aa821d56b11d5856
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928006"
---
# <a name="get-face-detection-data"></a>얼굴 감지 데이터 가져오기

이 가이드에서는 얼굴 감지를 사용 하 여 지정 된 이미지에서 성별, age 또는 포즈와 같은 특성을 추출 하는 방법을 보여 줍니다. 이 가이드의 코드 조각은 Azure Cognitive Services Face 클라이언트 라이브러리를 사용 하 여 c #으로 작성 되었습니다. [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 통해 동일한 기능을 사용할 수 있습니다.

이 가이드에서는 다음을 수행 하는 방법을 보여 줍니다.

- 이미지에서 얼굴의 위치와 크기를 가져옵니다.
- 이미지에서 pupils, 코, 입 등 다양 한 얼굴 랜드마크의 위치를 가져옵니다.
- 검색 된 면의 성별, age, emotion 및 기타 특성을 추측 합니다.

## <a name="setup"></a>설치

이 가이드에서는 [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) `faceClient` 얼굴 구독 키와 끝점 URL을 사용 하 여 라는 FaceClient 개체를 이미 생성 했다고 가정 합니다. 여기서는이 가이드에서 사용 되는 [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)또는 [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)를 호출 하 여 얼굴 감지 기능을 사용할 수 있습니다. 이 기능을 설정 하는 방법에 대 한 지침은 빠른 시작 중 하나를 수행 합니다.

이 가이드는 전달할 수 있는 인수와 반환 된 데이터로 수행할 수 있는 작업을 비롯 하 여 검색 호출의 세부 사항을 중점적으로 설명 합니다. 필요한 기능만 쿼리 하는 것이 좋습니다. 각 작업을 완료 하려면 추가 시간이 필요 합니다.

## <a name="get-basic-face-data"></a>기본 얼굴 데이터 가져오기

얼굴을 찾고 이미지에서 해당 위치를 가져오려면 _Returnfaceid_ 매개 변수를 **true**로 설정 하 여 메서드를 호출 합니다. 이 설정은 기본값입니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, false, null);
```

반환 된 [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) 개체의 고유 id와 표면의 픽셀 좌표를 제공 하는 사각형을 쿼리할 수 있습니다.

```csharp
foreach (var face in faces)
{
    string id = face.FaceId.ToString();
    FaceRectangle rect = face.FaceRectangle;
}
```

글꼴의 위치 및 크기를 구문 분석 하는 방법에 대 한 자세한 내용은 [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet)를 참조 하세요. 일반적으로이 사각형에는 눈, eyebrows, 코 및 입가 포함 됩니다. Head, 귀 및 chin의 위쪽은 반드시 포함 되는 것은 아닙니다. 표면 사각형을 사용 하 여 완전 한 헤드를 자르거나, 영상 ID 유형 이미지의 경우 각 방향으로 사각형을 확장할 수 있습니다.

## <a name="get-face-landmarks"></a>얼굴 랜드마크 가져오기

[얼굴 랜드마크](../concepts/face-detection.md#face-landmarks) 는 pupils 또는 코의 팁과 같이 얼굴에서 찾기 쉬운 점으로 이루어진 집합입니다. 얼굴 랜드마크 데이터를 가져오려면 _returnFaceLandmarks_ 매개 변수를 **true**로 설정 합니다.

```csharp
IList<DetectedFace> faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, null);
```

다음 코드는 코 및 pupils의 위치를 검색 하는 방법을 보여 줍니다.

```csharp
foreach (var face in faces)
{
    var landmarks = face.FaceLandmarks;

    double noseX = landmarks.NoseTip.X;
    double noseY = landmarks.NoseTip.Y;

    double leftPupilX = landmarks.PupilLeft.X;
    double leftPupilY = landmarks.PupilLeft.Y;

    double rightPupilX = landmarks.PupilRight.X;
    double rightPupilY = landmarks.PupilRight.Y;
}
```

얼굴 랜드마크 데이터를 사용 하 여 면의 방향을 정확 하 게 계산할 수도 있습니다. 예를 들어, 고의 중심에서 눈 중심까지 표면의 회전을 벡터로 정의할 수 있습니다. 다음 코드는이 벡터를 계산 합니다.

```csharp
var upperLipBottom = landmarks.UpperLipBottom;
var underLipTop = landmarks.UnderLipTop;

var centerOfMouth = new Point(
    (upperLipBottom.X + underLipTop.X) / 2,
    (upperLipBottom.Y + underLipTop.Y) / 2);

var eyeLeftInner = landmarks.EyeLeftInner;
var eyeRightInner = landmarks.EyeRightInner;

var centerOfTwoEyes = new Point(
    (eyeLeftInner.X + eyeRightInner.X) / 2,
    (eyeLeftInner.Y + eyeRightInner.Y) / 2);

Vector faceDirection = new Vector(
    centerOfTwoEyes.X - centerOfMouth.X,
    centerOfTwoEyes.Y - centerOfMouth.Y);
```

얼굴 방향을 알고 있는 경우 사각형 얼굴을 회전 하 여 더 정확 하 게 정렬할 수 있습니다. 이미지에서 얼굴을 자르려면 모양이 항상 수직이 되도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.

## <a name="get-face-attributes"></a>얼굴 특성 가져오기

얼굴 검색 API는 얼굴 사각형과 랜드마크 외에도 얼굴의 여러 개념 특성을 분석할 수 있습니다. 전체 목록은 [Face 특성](../concepts/face-detection.md#attributes) 개념 섹션을 참조 하세요.

얼굴 특성을 분석 하려면 _returnFaceAttributes_ 매개 변수를 [FaceAttributeType 열거형](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) 값 목록으로 설정 합니다.

```csharp
var requiredFaceAttributes = new FaceAttributeType[] {
    FaceAttributeType.Age,
    FaceAttributeType.Gender,
    FaceAttributeType.Smile,
    FaceAttributeType.FacialHair,
    FaceAttributeType.HeadPose,
    FaceAttributeType.Glasses,
    FaceAttributeType.Emotion
};
var faces = await faceClient.DetectWithUrlAsync(imageUrl, true, false, requiredFaceAttributes);
```

그런 다음 반환 된 데이터에 대 한 참조를 가져오고 필요에 따라 더 많은 작업을 수행 합니다.

```csharp
foreach (var face in faces)
{
    var attributes = face.FaceAttributes;
    var age = attributes.Age;
    var gender = attributes.Gender;
    var smile = attributes.Smile;
    var facialHair = attributes.FacialHair;
    var headPose = attributes.HeadPose;
    var glasses = attributes.Glasses;
    var emotion = attributes.Emotion;
}
```

각 특성에 대해 자세히 알아보려면 [얼굴 검색 및 특성](../concepts/face-detection.md) 개념 가이드를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 얼굴 검색의 다양 한 기능을 사용 하는 방법을 알아보았습니다. 다음으로, 심층 자습서를 수행 하 여 이러한 기능을 앱에 통합 합니다.

- [자습서: 이미지에서 얼굴 데이터를 표시하는 WPF 앱 만들기](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>관련 항목

- [참조 설명서 (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서 (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)