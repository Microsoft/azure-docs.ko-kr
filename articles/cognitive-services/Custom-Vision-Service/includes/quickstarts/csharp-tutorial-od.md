---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: 3e4206d00d33020098770600e151f9075d160caa
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511331"
---
이 문서에서는 C#과 함께 Custom Vision 클라이언트 라이브러리를 사용하여 개체 검색 모델을 빌드하는 방법을 보여줍니다. 프로젝트를 만든 후에는 태그가 지정된 지역을 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. .NET 애플리케이션을 빌드하기 위한 템플릿으로 이 예제를 사용하세요. 

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Custom Vision 클라이언트 라이브러리 및 샘플 코드 가져오기

Custom Vision을 사용하는 .NET 앱을 작성하려면 Custom Vision NuGet 패키지가 필요합니다. 이러한 패키지는 다운로드할 샘플 프로젝트에 포함되어 있지만, 여기서 개별적으로 액세스할 수도 있습니다.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) 프로젝트를 복제 또는 다운로드합니다. **CustomVision/ObjectDetection** 폴더로 이동하여 Visual Studio에서 _ObjectDetection.csproj_를 엽니다.

이 Visual Studio 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)를 통해 액세스할 수 있는 __My New Project__ 라는 새로운 Custom Vision 프로젝트를 만듭니다. 그런 다음, 이미지를 업로드하여 개체 검색 모델을 교육하고 테스트합니다. 이 프로젝트의 모델은 이미지에서 포트와 가위를 검색하는 교육을 받습니다.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>코드 이해

_Program.cs_ 파일을 열고 코드를 검사합니다. 각각 `CUSTOM_VISION_TRAINING_KEY` 및 `CUSTOM_VISION_PREDICTION_KEY`라고 명명된 학습 및 예측 키에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). 스크립트는 이러한 변수를 찾습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

또한 Custom Vision 웹 사이트의 설정 페이지에서 엔드포인트 URL을 가져옵니다. `CUSTOM_VISION_ENDPOINT`라는 환경 변수에 저장합니다. 이 스크립트는 클래스의 루트에 이에 대한 참조를 저장합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

### <a name="create-a-new-custom-vision-service-project"></a>새 Custom Vision Service 프로젝트 만들기

다음 코드는 개체 검색 프로젝트를 만듭니다. 생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 프로젝트를 만들 때 다른 옵션을 지정하려면 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) 메서드를 참조하세요([탐지기 빌드](../../get-started-build-detector.md) 웹 포털 가이드에 설명되어 있음).  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


### <a name="add-tags-to-the-project"></a>프로젝트에 태그 추가

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

개체 검색 프로젝트의 이미지에 태그를 지정할 때 정규화된 좌표를 사용하여 태그가 지정된 각 개체의 지역을 지정해야 합니다. 다음 코드는 각 샘플 이미지를 태그가 지정된 지역과 연결합니다.

> [!NOTE]
> 영역의 좌표를 표시하는 클릭 및 끌기 유틸리티를 사용하지 않는 경우 [Customvision.ai](https://www.customvision.ai/)에서 웹 UI를 사용할 수 있습니다. 이 예제에서는 좌표가 이미 제공되어 있습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

그런 다음, 이 연결 맵을 사용하여 해당 지역 좌표로 각 샘플 이미지를 업로드합니다. 단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

이 때 모든 샘플 이미지를 업로드하고 연결된 픽셀 사각형을 각 샘플 이미지(**포크** 또는 **가위**)에 연결된 픽셀 사각형으로 태그 지정했습니다.

### <a name="train-the-project"></a>프로젝트 학습

이 코드는 프로젝트의 첫 번째 교육 반복을 만듭니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

### <a name="publish-the-current-iteration"></a>현재 반복 게시

게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

### <a name="create-a-prediction-endpoint"></a>예측 엔드포인트 만들기

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

### <a name="use-the-prediction-endpoint"></a>예측 엔드포인트 사용

이 스크립트 부분은 테스트 이미지를 로드하고, 모델 엔드포인트를 쿼리하고, 예측 데이터를 콘솔에 출력합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션이 실행되면 콘솔 창이 열리고 다음 출력이 기록됩니다.

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

그런 다음, 테스트 이미지(**Images/Test/** 에 있음)에 태그가 적절하게 지정되는지, 검색 지역이 올바른지 확인할 수 있습니다. 이제 아무 키를 눌러 애플리케이션을 종료할 수 있습니다.

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>다음 단계

이제 코드에서 개체 검색 프로세스의 모든 단계를 수행하는 방법을 살펴보았습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다. 다음 가이드에서는 이미지 분류를 다루지만, 원칙은 개체 검색과 비슷합니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)
