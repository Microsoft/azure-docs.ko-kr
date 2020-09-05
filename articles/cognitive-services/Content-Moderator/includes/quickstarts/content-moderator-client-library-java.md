---
title: Content Moderator Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Java용 Azure Cognitive Services Content Moderator 클라이언트 라이브러리를 시작하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 08/17/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 2fb18503600d48775c2d5cb981841f5ae627e7eb
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510115"
---
Java용 Content Moderator 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Content Moderator는 자료에서 모욕적이거나, 위험하거나, 원치 않는 텍스트, 이미지 및 비디오 콘텐츠를 확인하는 인지 서비스입니다. 이러한 자료가 발견되면 서비스가 콘텐츠에 적절한 레이블(플래그)을 적용합니다. 그러면 앱이 규정에 부합하고 원하는 사용자 환경을 유지하기 위해 플래그가 지정된 콘텐츠를 처리할 수 있습니다.

Java용 Content Moderator 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 성인 또는 외설 콘텐츠, 텍스트 또는 사람 얼굴에 대한 이미지를 조정합니다.

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/contentmoderator?view=azure-java-stable) | [아티팩트(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator) | [샘플](https://docs.microsoft.com/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자

## <a name="setting-up"></a>설치

### <a name="create-a-content-moderator-azure-resource"></a>Content Moderator Azure 리소스 만들기

Azure Cognitive Services는 구독하는 Azure 리소스로 표시됩니다. 로컬 머신에서 [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) 또는 [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli)를 사용하여 Content Moderator용 리소스를 만듭니다. 다음도 가능합니다.

* [Azure Portal](https://portal.azure.com/)에서 리소스를 확인합니다.

리소스에서 키를 가져온 후 `AZURE_CONTENTMODERATOR_KEY`라는 키에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

### <a name="create-a-new-gradle-project"></a>새 Gradle 프로젝트 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp && cd myapp
```
`gradle init`을 실행합니다. 이 명령은 *build.gradle.kts*를 포함하여 런타임에 애플리케이션을 만들고 구성하는 데 사용되는 Gradle용 필수 빌드 파일을 만듭니다. 작업 디렉터리에서 이 명령을 실행합니다.

```console
gradle init --type basic
```

빌드 스크립트 DSL을 선택하라는 메시지가 표시되면 **Kotlin**을 선택합니다.

*build.gradle.kts*를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 다음 빌드 구성을 복사합니다. 이 구성은 프로젝트를 Java 애플리케이션(진입점이 **ContentModeratorQuickstart** 클래스임)으로 정의합니다. JSON 직렬화를 위한 Gson SDK와 Content Moderator 클라이언트 라이브러리를 가져옵니다.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

작업 디렉터리에서 다음 명령을 실행하여 프로젝트 원본 폴더를 만듭니다.

```console
mkdir -p src/main/java
```

그런 다음, *ContentModeratorQuickstart.java*라는 파일을 새 폴더에 만듭니다. 원하는 편집기 또는 IDE에서 파일을 열고, 다음 라이브러리를 위쪽에 가져옵니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

## <a name="object-model"></a>개체 모델

Content Moderator Java 클라이언트 라이브러리의 주요 기능 중 일부를 처리하는 클래스는 다음과 같습니다.

|이름|설명|
|---|---|
|[ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable)|이 클래스는 모든 Content Moderator 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고, 다른 클래스의 인스턴스를 생성하는 데 사용합니다.|
|[ImageModeration](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations?view=azure-java-stable)|이 클래스는 성인 콘텐츠, 개인 정보 또는 사람 얼굴에 대한 이미지를 분석하는 기능을 제공합니다.|
|[TextModerations](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations?view=azure-java-stable)|이 클래스는 언어, 욕설, 오류 및 개인 정보에 대한 텍스트를 분석하는 기능을 제공합니다.|
|[검토](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews?view=azure-java-stable)|이 클래스는 작업, 사용자 지정 워크플로 및 사용자 검토를 만드는 메서드를 포함하여 검토 API의 기능을 제공합니다.|


## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Java용 Content Moderator 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [이미지 조정](#moderate-images)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 단계에서는 `AZURE_CONTENTMODERATOR_KEY`라는 Content Moderator 키에 대한 [환경 변수를 만들었다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)고 가정합니다.

애플리케이션의 `main` 메서드에서 구독 엔드포인트 값 및 구독 키 환경 변수를 사용하여 [ContentModeratorClient](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient?view=azure-java-stable) 개체를 만듭니다. 

> [!NOTE]
> 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]

## <a name="moderate-images"></a>이미지 조정

### <a name="get-images"></a>이미지 가져오기

프로젝트의 **src/main/** 폴더에서 **resources** 폴더를 만들고 해당 폴더로 이동합니다. 그런 다음, *ImageFiles.txt* 텍스트 파일을 새로 만듭니다. 이 파일에서 분석할 이미지의 URL을 각 줄에 하나씩 추가합니다. 다음 샘플 이미지를 사용할 수 있습니다.

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>도우미 클래스 정의

다음으로, *ContentModeratorQuickstart.java* 파일에서 다음 클래스 정의를 **ContentModeratorQuickstart** 클래스 내에 추가합니다. 이 내부 클래스는 나중에 이미지 조정 프로세스에서 사용됩니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]

### <a name="iterate-through-images"></a>이미지 반복

다음으로, 다음 코드를 `main` 메서드의 아래쪽에 추가합니다. 또는 `main`에서 호출되는 별도의 메서드에 추가할 수 있습니다. 이 코드는 _ImageFiles.txt_ 파일의 각 줄을 단계별로 실행합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_iterate)]

### <a name="check-for-adultracy-content"></a>성인/외설 콘텐츠 확인
이 코드 줄은 지정된 URL의 이미지에서 성인 또는 외설 콘텐츠를 확인합니다. 이러한 용어에 대한 자세한 내용은 이미지 조정 개념 가이드를 참조하세요.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>텍스트 확인
이 코드 줄은 이미지에서 표시되는 텍스트를 확인합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>얼굴 확인
이 코드 줄은 이미지에서 사람의 얼굴을 확인합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

마지막으로, 반환된 정보를 `EvaluationData` 목록에 저장합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>결과 출력

다음 코드를 `while` 루프 뒤에 추가합니다. 이 코드는 결과를 콘솔 및 *src/main/resources/ModerationOutput.json* 출력 파일에 출력합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

`try` 문을 닫고 메서드를 완료하는 `catch` 문을 추가합니다.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`gradle run` 명령을 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

그런 다음, *src/main/resources/ModerationOutput.json* 파일로 이동하여 콘텐츠 조정의 결과를 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Content Moderator Java 라이브러리를 사용하여 조정 작업을 수행하는 방법을 알아보았습니다. 다음으로, 개념 가이드를 참조하여 이미지 또는 다른 미디어의 조정에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
>[이미지 조정 개념](https://docs.microsoft.com/azure/cognitive-services/content-moderator/image-moderation-api)

* [Azure Content Moderator란?](../../overview.md)
* 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)에서 확인할 수 있습니다.