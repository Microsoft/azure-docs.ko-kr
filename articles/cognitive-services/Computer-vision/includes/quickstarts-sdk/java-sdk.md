---
title: '빠른 시작: Java용 Computer Vision 클라이언트 라이브러리'
description: 이 빠른 시작에서 Java용 Computer Vision 클라이언트 라이브러리를 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/19/2019
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 6eacaf2ec75c485dbdd7e66a73cdd36787da6126
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88753060"
---
<a name="HOLTop"></a>

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable) | [아티팩트(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision) | [샘플](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 각각 `COMPUTER_VISION_SUBSCRIPTION_KEY` 및 `COMPUTER_VISION_ENDPOINT`라는 키 및 서비스 엔드포인트 URL에 대한 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

## <a name="setting-up"></a>설치

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```

작업 디렉터리에서 `gradle init` 명령을 실행합니다. 이 명령은 *build.gradle.kts*를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다.

```console
gradle init --type basic
```

**DSL**을 선택하라는 메시지가 표시되면 **Kotlin**을 선택합니다.

*build.gradle.kts*를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **ComputerVisionQuickstarts** 클래스임)으로 정의합니다. Computer Vision 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
```

작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

새 폴더로 이동하여 *ComputerVisionQuickstarts.java*라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports)]

그런 다음, **ComputerVisionQuickstarts**에 대한 클래스 정의를 추가합니다.

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)에서 찾을 수 있습니다.

프로젝트의 *build.gradle.kts* 파일에서 Computer Vision 클라이언트 라이브러리를 종속성으로 포함시킵니다.

```kotlin
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.2-beta")
}
```

## <a name="object-model"></a>개체 모델

Computer Vision Java SDK의 주요 기능 중 일부를 처리하는 클래스와 인터페이스는 다음과 같습니다.

|Name|Description|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) | 이 클래스는 모든 Computer Vision 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ComputerVision](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision?view=azure-java-stable)| 이 클래스는 클라이언트 개체에서 제공되며, 이미지 분석, 텍스트 검색 및 썸네일 생성과 같은 모든 이미지 작업을 직접 처리합니다.|
|[VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable)| 이 열거형은 표준 Analyze(분석) 작업에서 수행할 수 있는 다양한 유형의 이미지 분석을 정의합니다. 필요에 따라 VisualFeatureTypes 값 세트를 지정합니다. |

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 Computer Vision 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 분석](#analyze-an-image)
* [인쇄 텍스트 및 필기 텍스트 읽기](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `COMPUTER_VISION_SUBSCRIPTION_KEY`라는 Computer Vision 키에 대한 [환경 변수를 만들었다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)고 가정합니다.

다음 코드에서는 `main` 메서드를 클래스에 추가하고, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. Azure Portal의 **개요** 섹션을 확인하여 찾을 수 있는 고유한 엔드포인트 문자열을 입력해야 합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_mainvars)]

다음으로, 다음 코드를 추가하여 [ComputerVisionClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-java-stable) 개체를 만들고, 나중에 정의하는 다른 메서드에 전달합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_client)]

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

## <a name="analyze-an-image"></a>이미지 분석

다음 코드에서는 클라이언트 개체를 사용하여 로컬 이미지를 분석하고 결과를 출력하는 `AnalyzeLocalImage` 메서드를 정의합니다. 이 메서드는 텍스트 설명, 분류, 태그 목록, 감지된 얼굴, 성인 콘텐츠 플래그, 기본 색 및 이미지 형식을 반환합니다.

### <a name="set-up-test-image"></a>테스트 이미지 설정

먼저, **resources/** 폴더를 프로젝트의 **src/main/** 폴더에 만들고, 분석하려는 이미지를 추가합니다. 그런 다음, 다음 메서드 정의를 **ComputerVisionQuickstarts** 클래스에 추가합니다. 필요한 경우 이미지 파일과 일치하도록 `pathToLocalImage`의 값을 변경합니다. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_refs)]

> [!NOTE]
> URL을 사용하여 원격 이미지를 분석할 수도 있습니다. 원격 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)의 샘플 코드를 참조하세요.

### <a name="specify-visual-features"></a>시각적 기능 지정

다음으로, 분석에서 추출하려는 시각적 기능을 지정합니다. 전체 목록은 [VisualFeatureTypes](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-java-stable) 열거형을 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_features)]

### <a name="analyze"></a>분석
이 메서드는 각 이미지 분석 범위에 대한 자세한 결과를 콘솔에 출력합니다. 이 메서드 호출은 Try/Catch 블록으로 묶는 것이 좋습니다. **analyzeImageInStream** 메서드는 추출된 모든 정보가 포함된 **ImageAnalysis** 개체를 반환합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_analyze)]

다음 섹션에서는 이 정보를 자세히 구문 분석하는 방법을 보여줍니다.

### <a name="get-image-description"></a>이미지 설명 가져오기

다음 코드는 이미지에 대해 생성된 캡션 목록을 가져옵니다. 자세한 내용은 [이미지 설명](../../concept-describing-images.md)을 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_captions)]

### <a name="get-image-category"></a>이미지 범주 가져오기

다음 코드는 검색된 이미지 범주를 가져옵니다. 자세한 내용은 [범주 이미지](../../concept-categorizing-images.md)를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_category)]

### <a name="get-image-tags"></a>이미지 태그 가져오기

다음 코드는 이미지에서 검색된 범주 세트를 가져옵니다. 자세한 내용은 [콘텐츠 태그](../../concept-tagging-images.md)를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_tags)]

### <a name="detect-faces"></a>얼굴 감지

다음 코드는 사각형 좌표를 사용하여 이미지에서 검색된 얼굴을 반환하고 얼굴 특성을 선택합니다. 자세한 내용은 [얼굴 감지](../../concept-detecting-faces.md)를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>성인, 외설 또는 폭력 콘텐츠 검색

다음 코드는 이미지에 있는 성인 콘텐츠의 검색된 상태를 출력합니다. 자세한 내용은 [성인, 외설, 폭력 콘텐츠](../../concept-detecting-adult-content.md)를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_adult)]

### <a name="get-image-color-scheme"></a>이미지 색 구성표 가져오기

다음 코드는 주조색 및 강조 색과 같이 이미지에서 검색된 색 특성을 출력합니다. 자세한 내용은 [색 구성표](../../concept-detecting-color-schemes.md)를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_colors)]

### <a name="get-domain-specific-content"></a>도메인 특정 콘텐츠 가져오기

Computer Vision은 특수 모델을 사용하여 이미지에 대한 추가 분석을 수행할 수 있습니다. 자세한 내용은 [도메인 특정 콘텐츠](../../concept-detecting-domain-content.md)를 참조하세요. 

다음 코드는 이미지에서 검색된 유명인에 대한 데이터를 구문 분석합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_celebrities)]

다음 코드는 이미지에서 검색된 랜드마크에 대한 데이터를 구문 분석합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_analyzelocal_landmarks)]

### <a name="get-the-image-type"></a>이미지 형식 가져오기

다음 코드는 이미지 형식이 클립 아트인지 아니면 선 그리기인지 여부에 관계없이 이미지 형식에 대한 정보를 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imagetype)]

## <a name="read-printed-and-handwritten-text"></a>인쇄 텍스트 및 필기 텍스트 읽기

Computer Vision은 이미지 속의 시각적 텍스트를 읽고 문자 스트림으로 변환할 수 있습니다.

> [!NOTE]
> URL을 사용하여 원격 이미지의 텍스트를 읽을 수도 있습니다. 원격 이미지와 관련된 시나리오는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)의 샘플 코드를 참조하세요.

### <a name="call-the-recognize-api"></a>Recognize API 호출

먼저 다음 코드를 사용하여 지정된 이미지에 대한 **recognizePrintedTextInStream** 메서드를 호출합니다. 이 코드를 프로젝트에 추가할 때 `localTextImagePath`의 값을 로컬 이미지의 경로로 바꾸어야 합니다. [샘플 이미지](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg)를 다운로드하여 여기에서 사용할 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

### <a name="print-recognize-results"></a>Recognize 결과 인쇄

다음 코드 블록은 반환된 텍스트를 처리하고 구문 분석하여 각 줄의 첫 번째 단어를 인쇄합니다. 이 코드를 사용하여 **OcrResult** 인스턴스의 구조를 빠르게 파악할 수 있습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_print)]

마지막으로 try/catch 블록과 메서드 정의를 닫습니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`gradle run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Computer Vision Java 라이브러리를 사용하여 기본 작업을 수행하는 방법을 알아보았습니다. 다음으로 라이브러리에 대해 자세히 알아보려면 참조 설명서를 살펴보세요.

> [!div class="nextstepaction"]
>[Computer Vision 참조(Java)](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/computervision?view=azure-java-stable)

* [Computer Vision이란?](../../Home.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)에서 확인할 수 있습니다.
