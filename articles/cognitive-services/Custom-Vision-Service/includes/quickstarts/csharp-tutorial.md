---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 08/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ba09deda155ac446693a7e1037390a3f1fd2700f
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88919383"
---
이 문서에서는 C#과 함께 Custom Vision 클라이언트 라이브러리를 사용하여 이미지 분류 모델 빌드를 시작할 수 있도록 도와주는 정보와 샘플 코드를 제공합니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. .NET 애플리케이션을 빌드하기 위한 템플릿으로 이 예제를 사용하세요. 코드 _없이_ 분류 모델을 빌드하고 프로세스를 수행하려면 대신 [브라우저 기반 가이드](../../getting-started-build-a-classifier.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio 2015 또는 2017](https://www.visualstudio.com/downloads/)의 모든 버전
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Custom Vision 클라이언트 라이브러리 및 샘플 코드 가져오기

Custom Vision을 사용하는 .NET 앱을 작성하려면 Custom Vision NuGet 패키지가 필요합니다. 이러한 패키지는 다운로드할 샘플 프로젝트에 포함되어 있지만, 여기서 개별적으로 액세스할 수도 있습니다.

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

[Cognitive Services .NET 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples) 프로젝트를 복제 또는 다운로드합니다. **CustomVision/ImageClassification** 폴더로 이동하여 Visual Studio에서 _ImageClassification.csproj_ 를 엽니다.

이 Visual Studio 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)를 통해 액세스할 수 있는 __My New Project__ 라는 새로운 Custom Vision 프로젝트를 만듭니다. 그런 다음, 분류자를 학습하고 테스트하려면 이미지를 업로드합니다. 이 프로젝트의 분류자는 트리가 __솔송나무__ 인지 아니면 __벗나무__ 인지 확인하는 용도로 사용됩니다.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="understand-the-code"></a>코드 이해

_Program.cs_ 파일을 열고 코드를 검사합니다. 각각 `CUSTOM_VISION_TRAINING_KEY` 및 `CUSTOM_VISION_PREDICTION_KEY`라고 명명된 학습 및 예측 키에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). 스크립트는 이러한 변수를 찾습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

또한 Custom Vision 웹 사이트의 설정 페이지에서 엔드포인트 URL을 가져옵니다. `CUSTOM_VISION_ENDPOINT`라는 환경 변수에 저장합니다. 이 스크립트는 클래스의 루트에 이에 대한 참조를 저장합니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

다음 코드 줄은 프로젝트의 기본 기능을 실행합니다.

### <a name="create-a-new-custom-vision-service-project"></a>새 Custom Vision Service 프로젝트 만들기

생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 프로젝트를 만들 때 다른 옵션을 지정하려면 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) 메서드를 참조하세요([분류자 빌드](../../getting-started-build-a-classifier.md) 웹 포털 가이드에 설명되어 있음).   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>프로젝트에서 태그 만들기

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

이 프로젝트의 이미지가 포함되어 있습니다. 이 이미지는 _Program.cs_ 의 **LoadImagesFromDisk** 메서드에서 참조됩니다. 단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>분류자 학습 및 게시

이 코드는 예측 모델의 첫 번째 반복을 만든 다음, 해당 반복을 예측 엔드포인트에 게시합니다. 반복 이름을 사용하여 예측 요청을 보낼 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>예측 엔드포인트 설정

예측 엔드포인트는 현재 모델에 이미지를 제출하고 분류 예측을 가져오는 데 사용할 수 있는 참조입니다.

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>기본 예측 엔드포인트에 이미지 제출

이 스크립트의 테스트 이미지는 **LoadImagesFromDisk** 메서드에 로드되고, 모델의 예측 출력은 콘솔에 표시됩니다. `publishedModelName` 변수의 값은 Custom Vision 포털의 **성능** 탭에 있는 "다음으로 게시된" 값과 일치해야 합니다. 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션이 실행되면 콘솔 창이 열리고 다음 출력이 기록됩니다.

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

그러면 테스트 이미지(**Images/Test/** 에 있음)에 태그가 적절하게 지정되는지 확인할 수 있습니다. 애플리케이션을 종료하려면 아무 키나 누릅니다. [Custom Vision 웹 사이트](https://customvision.ai)로 돌아가서 새로 만든 프로젝트의 현재 상태를 살펴볼 수도 있습니다.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

이제 코드에서 이미지 분류 프로세스의 모든 단계를 수행하는 방법을 살펴보았습니다. 이 샘플은 교육을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 교육하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)
