---
author: areddish
ms.custom: devx-track-java
ms.author: areddish
ms.service: cognitive-services
ms.date: 08/17/2020
ms.openlocfilehash: cd6388e6c6313ba84978d43d388855b114a4875d
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508544"
---
이 문서에서는 Custom Vision Java 클라이언트 라이브러리를 사용하여 이미지 분류 모델을 빌드하는 방법을 보여줍니다. 프로젝트를 만든 후에는 태그를 추가하고, 이미지를 업로드하고, 프로젝트를 학습하고, 프로젝트의 기본 예측 엔드포인트 URL를 획득하고, 해당 엔드포인트를 사용하여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. Java 애플리케이션을 빌드하기 위한 템플릿으로 이 예제를 사용하세요. 코드 _없이_ 분류 모델을 빌드하고 사용하는 방법을 알아보려면 [브라우저 기반 가이드](../../getting-started-build-a-classifier.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- 원하는 Java IDE
- [JDK 7 또는 8](https://aka.ms/azure-jdks)이 설치됨.
- [Maven](https://maven.apache.org/) 설치
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="get-the-custom-vision-client-library-and-sample-code"></a>Custom Vision 클라이언트 라이브러리 및 샘플 코드 가져오기

Custom Vision을 사용하는 Java 앱을 작성하려면 Custom Vision maven 패키지가 필요합니다. 이러한 패키지는 다운로드할 샘플 프로젝트에 포함되어 있지만, 여기서 개별적으로 액세스할 수도 있습니다.

maven 중앙 리포지토리에서 Custom Vision 클라이언트 라이브러리를 찾을 수 있습니다.

- [교육 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
- [예측 SDK](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

[Cognitive Services Java SDK 샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) 프로젝트를 복제 또는 다운로드합니다. **Vision/CustomVision/** 폴더로 이동합니다.

이 Java 프로젝트는 [Custom Vision 웹 사이트](https://customvision.ai/)를 통해 액세스할 수 있는 __샘플 Java 프로젝트__ 라는 새로운 Custom Vision 이미지 분류 프로젝트를 만듭니다. 그런 다음, 분류자를 학습하고 테스트하려면 이미지를 업로드합니다. 이 프로젝트의 분류자는 트리가 __솔송나무__ 인지 아니면 __벗나무__ 인지 확인하는 용도로 사용됩니다.

[!INCLUDE [get-keys](../../includes/get-keys.md)]

프로그램은 주요 데이터를 환경 변수로 참조하도록 구성됩니다. **Vision/CustomVision** 폴더로 이동하고, 다음 PowerShell 명령을 입력하여 환경 변수를 설정합니다. 

> [!NOTE]
> Windows 이외의 운영 체제를 사용하는 경우 지침은 [환경 변수 구성](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication)을 참조하세요.

```powershell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>코드 이해

Java IDE에서 `Vision/CustomVision` 프로젝트를 로드하고 _CustomVisionSamples.java_ 파일을 엽니다. **runSample** 메서드를 찾아서 **ObjectDetection_Sample** 메서드 호출이라는 주석으로 처리합니다.&mdash;이렇게 하면 개체 검색 시나리오가 실행되는데, 이 내용은 본 가이드에서 다루지 않습니다. **ImageClassification_Sample** 메서드는 이 예제의 기본 기능을 구현합니다. 이 메서드의 정의로 이동하여 코드를 검사하세요.

### <a name="create-a-custom-vision-service-project"></a>Custom Vision Service 프로젝트 만들기

코드의 첫 번째 비트는 이미지 분류 프로젝트를 만듭니다. 생성된 프로젝트는 앞에서 방문한 [Custom Vision 웹 사이트](https://customvision.ai/)에 표시됩니다. 프로젝트를 만들 때 다른 옵션을 지정하려면 [CreateProject](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject?view=azure-java-stable#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_) 메서드 오버로드를 참조하세요([분류자 빌드](../../getting-started-build-a-classifier.md) 웹 포털 가이드에 설명되어 있음).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>프로젝트에서 태그 만들기

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>이미지 업로드 및 태그 지정

샘플 이미지는 프로젝트의 **src/main/resources** 프로젝트에 포함되어 있습니다. 이 위치에서 읽히고 적절한 태그를 사용하여 서비스에 업로드됩니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

이전 코드 조각은 이미지를 리소스 스트림으로 검색하여 서비스에 업로드하는 두 개의 도우미 함수를 사용합니다(단일 일괄 처리에서 최대 64개의 이미지를 업로드할 수 있음).

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

### <a name="train-the-classifier-and-publish"></a>분류자 학습 및 게시

이 코드는 예측 모델의 첫 번째 반복을 만든 다음, 해당 반복을 예측 엔드포인트에 게시합니다. 게시된 반복에 부여된 이름은 예측 요청을 보내는 데 사용할 수 있습니다. 반복은 게시될 때까지 예측 엔드포인트에서 사용할 수 없습니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]

### <a name="use-the-prediction-endpoint"></a>예측 엔드포인트 사용

여기서는 `predictor` 개체를 통해 표현되는 예측 엔드포인트는 현재 모델에 이미지를 제출하고 분류 예측을 가져오는 데 사용되는 참조입니다. 이 샘플에서 `predictor`는 예측 키 환경 변수를 사용하여 다른 곳에 정의됩니다.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>애플리케이션 실행

Maven을 사용하여 솔루션을 컴파일하고 실행하려면 명령 프롬프트에서 프로젝트 디렉터리(**Vision/CustomVision**)로 이동하여 실행 명령을 실행합니다.

```bash
mvn compile exec:java
```

애플리케이션의 콘솔 출력은 다음 텍스트와 비슷합니다.

```console
Creating project...
Adding images...
Adding image: hemlock_1.jpg
Adding image: hemlock_2.jpg
Adding image: hemlock_3.jpg
Adding image: hemlock_4.jpg
Adding image: hemlock_5.jpg
Adding image: hemlock_6.jpg
Adding image: hemlock_7.jpg
Adding image: hemlock_8.jpg
Adding image: hemlock_9.jpg
Adding image: hemlock_10.jpg
Adding image: japanese_cherry_1.jpg
Adding image: japanese_cherry_2.jpg
Adding image: japanese_cherry_3.jpg
Adding image: japanese_cherry_4.jpg
Adding image: japanese_cherry_5.jpg
Adding image: japanese_cherry_6.jpg
Adding image: japanese_cherry_7.jpg
Adding image: japanese_cherry_8.jpg
Adding image: japanese_cherry_9.jpg
Adding image: japanese_cherry_10.jpg
Training...
Training status: Training
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

그 후 테스트 이미지 예측(출력의 마지막 몇 줄)이 올바른지 확인할 수 있습니다.

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>다음 단계

지금까지 개체 검색 프로세스의 모든 단계를 코드로 수행하는 방법을 살펴보았습니다. 이 샘플은 학습을 한 번만 반복하지만, 정확도를 높이기 위해 모델을 여러 차례 학습하고 테스트해야 하는 경우가 많습니다.

> [!div class="nextstepaction"]
> [모델 테스트 및 재교육](../../test-your-model.md)
