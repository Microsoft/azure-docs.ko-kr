---
title: 인식 모델을 지정 하는 방법-얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 응용 프로그램에 사용할 인식 모델을 선택 하는 방법을 보여 줍니다.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: da34c2aa8a178a99f325bef8beb4a7e7685b3cbc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931474"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 Azure Face 서비스를 사용 하 여 얼굴 감지, 식별 및 유사성 검색을 위한 얼굴 인식 모델을 지정 하는 방법을 보여 줍니다.

얼굴 서비스는 기계 학습 모델을 사용 하 여 이미지에서 인간 얼굴에 대 한 작업을 수행 합니다. 사용자 의견에 따라 모델의 정확도를 지속적으로 향상 시키고 연구를 진행 하면서 이러한 향상 된 기능을 모델 업데이트로 제공 합니다. 개발자는 사용 하려는 얼굴 인식 모델의 버전을 지정 하는 옵션을 사용할 수 있습니다. 사용 사례에 가장 잘 맞는 모델을 선택할 수 있습니다.

Azure Face 서비스는 세 가지 인식 모델을 사용할 수 있습니다. 이러한 모델을 사용 하 여 만든 FaceLists 또는 **PersonGroup**를 사용 하는 고객에 대해 이전 버전과의 호환성을 보장 하기 위해 모델 _recognition_01_ (게시 된 2017) 및 _recognition_02_ (게시 2019)가 계속 지원 됩니다. **FaceList** 또는 **Persongroup** 는 항상 생성 된 인식 모델을 사용 하며, 새 얼굴은 추가 될 때이 모델과 연결 됩니다. 이를 만든 후에는 변경할 수 없으며, 고객은 해당 **FaceList** 또는 **PersonGroup**에 해당 하는 인식 모델을 사용 해야 합니다.

사용자 편의를 위해 이후 인식 모델로 이동할 수 있습니다. 그러나 사용자가 선택한 인식 모델을 사용 하 여 새 FaceLists 및 PersonGroups를 만들어야 합니다.

_Recognition_03_ 모델 (게시 된 2020)은 현재 사용할 수 있는 가장 정확한 모델입니다. 새 고객의 경우이 모델을 사용 하는 것이 좋습니다. _Recognition_03_ 은 유사성 비교와 사용자 일치 비교에 대 한 향상 된 정확도를 제공 합니다. 각 모델은 서로 독립적으로 작동 하며 한 모델에 대해 설정 된 신뢰도 임계값은 다른 인식 모델에서 비교할 수 없습니다.

모델 충돌을 방지 하면서 여러 얼굴 작업에서 선택한 모델을 지정 하는 방법을 알아봅니다. 고급 사용자 이며 최신 모델로 전환 해야 하는지 여부를 결정 하려는 경우 [다른 모델 평가](#evaluate-different-models) 섹션으로 건너뛰어 새 모델을 평가 하 고 현재 데이터 집합을 사용 하 여 결과를 비교 합니다.


## <a name="prerequisites"></a>필수 구성 요소

AI 얼굴 감지 및 식별의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 먼저 다음 방법 가이드를 참조 하세요.

* [이미지에서 얼굴을 감지 하는 방법](HowtoDetectFacesinImage.md)
* [이미지에서 얼굴을 식별 하는 방법](HowtoIdentifyFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 감지

얼굴 검색은 인간 얼굴의 시각적 랜드마크 식별 하 고 경계 상자 위치를 찾습니다. 또한 얼굴 기능을 추출 하 여 식별에 사용할 수 있도록 저장 합니다. 이 모든 정보는 한 면의 표현을 형성 합니다.

인식 모델은 얼굴 기능이 추출 될 때 사용 되므로 검색 작업을 수행할 때 모델 버전을 지정할 수 있습니다.

[얼굴 검색] API를 사용 하는 경우 매개 변수를 사용 하 여 모델 버전을 할당 `recognitionModel` 합니다. 사용 가능한 값은
* recognition_01
* recognition_02
* recognition_03


필요에 따라 _returnRecognitionModel_ 매개 변수 (기본값 **false**)를 지정 하 여 응답에서 _recognitionModel_ 을 반환할지 여부를 나타낼 수 있습니다. 따라서 [얼굴 검색] REST API에 대 한 요청 URL은 다음과 같이 표시 됩니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용 하는 경우 `recognitionModel` 버전을 나타내는 문자열을 전달 하 여에 대 한 값을 할당할 수 있습니다. 할당 되지 않은 상태로 두면의 기본 모델 버전이 사용 됩니다 `recognition_01` . .NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 식별

얼굴 서비스는 이미지에서 얼굴 데이터를 추출 하 고이를 **Person** 개체와 연결 (예: [face API 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 호출을 통해) 할 수 있으며, 여러 **person** 개체를 **PersonGroup**에 함께 저장할 수 있습니다. 그런 다음 **PersonGroup** 를 사용 [하 여 새] 얼굴을 비교 하 고 해당 그룹 내의 일치 하는 사용자를 식별할 수 있습니다.

**PersonGroup** 에는 모든 **사용자**에 대 한 고유한 인식 모델이 하나씩 있어야 하 고 그룹을 만들 때 매개 변수를 사용 하 여이를 지정할 수 있습니다 `recognitionModel` ([PersonGroup-create] 또는 [LargePersonGroup-create]). 이 매개 변수를 지정 하지 않으면 원래 `recognition_01` 모델이 사용 됩니다. 그룹은 항상 생성 된 인식 모델을 사용 하 고 새 얼굴은이 모델에 추가 될 때이 모델에 연결 됩니다. 그룹을 만든 후에는 변경할 수 없습니다. **PersonGroup** 구성 된 모델을 확인 하려면 _returnRecognitionModel_ 매개 변수를 **True**로 설정 하 여 [PersonGroup-Get] API를 사용 합니다.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

이 코드에서는 ID가 인 **PersonGroup** 을 `mypersongroupid` 만들고, _recognition_02_ 모델을 사용 하 여 얼굴 기능을 추출 하도록 설정 합니다.

마찬가지로 [얼굴 검색] API를 통해이 **PersonGroup** 비교할 얼굴을 검색할 때 사용할 모델을 지정 해야 합니다. 사용 하는 모델은 항상 **PersonGroup**의 구성과 일치 해야 합니다. 그렇지 않으면 호환 되지 않는 모델로 인해 작업이 실패 합니다.

[얼굴 식별] API에는 변경 내용이 없습니다. 검색 에서만 모델 버전을 지정 해야 합니다.

## <a name="find-similar-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 유사한 얼굴 찾기

유사성 검색을 위한 인식 모델을 지정할 수도 있습니다. `recognitionModel` [FaceList] API 또는 [LargeFaceList-create]를 사용 하 여 얼굴 목록을 만들 때를 사용 하 여 모델 버전을 할당할 수 있습니다. 이 매개 변수를 지정 하지 않으면 `recognition_01` 기본적으로 모델이 사용 됩니다. 얼굴 목록에는 항상 생성 된 인식 모델이 사용 되며 새 얼굴은 목록에 추가 될 때이 모델과 연결 됩니다. 만든 후에는 변경할 수 없습니다. 얼굴 목록이로 구성 된 모델을 확인 하려면 _returnRecognitionModel_ 매개 변수를 **true**로 설정 하 여 [FaceList] API를 사용 합니다.

.NET 클라이언트 라이브러리에 대 한 다음 코드 예제를 참조 하세요.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

이 코드는 `My face collection` 기능 추출에 대 한 _recognition_03_ 모델을 사용 하 여 라는 얼굴 목록을 만듭니다. 이 얼굴 목록을 검색 하 여 새롭게 검색 된 면에서 유사한 얼굴을 검색 하는 경우에는 _recognition_03_ 모델을 사용 하 여 해당 얼굴을 감지 해야 합니다 ([얼굴 감지]). 이전 섹션과 마찬가지로 모델은 일관적 이어야 합니다.

[유사한 API 찾기] 에는 변경 내용이 없습니다. 검색 에서만 모델 버전을 지정 합니다.

## <a name="verify-faces-with-specified-model"></a>지정 된 모델을 사용 하 여 얼굴 확인

[얼굴 확인] API는 두 얼굴이 동일한 사람에 게 속하는지 여부를 확인 합니다. 인식 모델과 관련 하 여 확인 API에는 변경 내용이 없지만 동일한 모델을 사용 하 여 검색 된 면만 비교할 수 있습니다.

## <a name="evaluate-different-models"></a>다른 모델 평가

사용자 고유의 데이터에 대 한 다양 한 인식 모델의 성능을 비교 하려는 경우 다음을 수행 해야 합니다.
1. _Recognition_01_, _recognition_02_및 _recognition_03_ 를 각각 사용 하 여 세 개의 PersonGroups를 만듭니다.
1. 이미지 데이터를 사용 하 여 얼굴을 감지 하 고이 세 **PersonGroup**내에 있는 **사람**에 게 등록 합니다. 
1. PersonGroup 교육 API를 사용 하 여 PersonGroups를 학습 합니다.
1. 세 개의 **PersonGroup**를 모두 확인 하 고 결과를 비교 하 여 테스트 합니다.


일반적으로 신뢰도 임계값을 지정 하는 경우 (0에서 1 사이의 값과 얼굴을 식별 하는 모델의 확신 정도를 결정 하는 값) 모델 마다 다른 임계값을 사용 해야 할 수 있습니다. 한 모델에 대 한 임계값은 다른 모델에 대 한 임계값을 공유 하는 것이 아니며 반드시 동일한 결과를 생성 하는 것은 아닙니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다른 얼굴 서비스 Api와 함께 사용할 인식 모델을 지정 하는 방법을 배웠습니다. 그런 다음 빠른 시작을 따라 얼굴 감지를 사용 하 여 시작 합니다.

* [Face .NET SDK](../Quickstarts/csharp-sdk.md)
* [Face Python SDK](../Quickstarts/python-sdk.md)
* [얼굴 이동 SDK](../Quickstarts/go-sdk.md)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face-유사 찾기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[얼굴-확인]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList-Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList-만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
