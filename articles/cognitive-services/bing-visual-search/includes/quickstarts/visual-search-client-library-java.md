---
title: Bing Visual Search Java 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: 5ac80cad20e89a7870c26960aedb58f34fa21380
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934420"
---
이 빠른 시작의 안내에 따라 Java 클라이언트 라이브러리를 사용하여 Bing Visual Search 서비스에서 이미지 인사이트 가져오기를 시작하세요. Bing Visual Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, 클라이언트 라이브러리를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 빠른 시작의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch)에서 확인할 수 있습니다.

Java용 Bing Visual Search 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* Visual Search 요청에 전송할 이미지를 업로드합니다.
* 이미지 인사이트 토큰 및 Visual Search 태그를 가져옵니다.

[참조 설명서](https://docs.microsoft.com/java/api/overview/azure/cognitiveservices/client/bingvisualsearch?view=azure-java-stable) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch) | [아티팩트(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/) | [샘플](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [JDK(Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)의 현재 버전
* [Gradle 빌드 도구](https://gradle.org/install/) 또는 다른 종속성 관리자

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

리소스에서 키를 가져온 후 `BING_SEARCH_V7_SUBSCRIPTION_KEY`라는 키에 대해 [환경 변수를 만듭니다](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication).

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

*build.gradle.kts*를 찾고, 원하는 IDE 또는 텍스트 편집기에서 엽니다. 그런 다음, 이 빌드 구성에서 복사합니다.

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

샘플 앱에 대한 폴더를 만듭니다. 작업 디렉터리에서 다음 명령을 실행합니다.

```console
mkdir -p src/main/java
```

API에 업로드하려는 이미지에 대한 폴더를 만듭니다. 이미지를 **resources** 폴더에 배치합니다.

```console
mkdir -p src/main/resources
``` 

새 폴더로 이동하여 *BingVisualSearchSample.java*라는 파일을 만듭니다. 원하는 편집기 또는 IDE에서 이 파일을 열고, 다음 `import` 문을 추가합니다.

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

그런 다음, 새 클래스를 만듭니다.

```java
public class BingVisualSearchSample {
}
```

애플리케이션의 `main` 메서드에서, 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어 해당 변수에 액세스해야 합니다. 그런 다음, 업로드할 이미지에 대한 `byte[]`를 만듭니다. 나중에 정의할 메서드에 대해 `try` 블록을 만들고, `toByteArray()`를 사용하여 이미지를 로드하고 바이트로 변환합니다.

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

이 빠른 시작에서는 Gradle 종속성 관리자를 사용합니다. 다른 종속성 관리자에 대한 클라이언트 라이브러리 및 정보는 [Maven 중앙 리포지토리](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)에서 찾을 수 있습니다.

프로젝트의 *build.gradle.kts* 파일에서 클라이언트 라이브러리를 `implementation` 문으로 포함시켜야 합니다. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>코드 예제

이러한 코드 조각에서는 Java용 Bing Visual Search 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [Visual Search 요청 전송](#send-a-visual-search-request)
* [이미지 인사이트 토큰 및 Visual Search 태그 인쇄](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `BING_SEARCH_V7_SUBSCRIPTION_KEY`라는 Bing Visual Search 키에 대한 [환경 변수를 만들었다고](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) 가정합니다.


기본 메서드에서는 구독 키를 사용하여 [BingVisualSearchAPI](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi?view=azure-java-stable) 개체를 인스턴스화해야 합니다.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Visual Search 요청 전송

새 메서드에서 클라이언트의 [bingImages().visualSearch()](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch?view=azure-java-stable#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) 메서드를 사용하여 `main()` 메서드에서 만든 이미지 바이트 배열을 보냅니다. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>이미지 인사이트 토큰 및 Visual Search 태그 인쇄

[ImageKnowledge](https://docs.microsoft.com/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge?view=azure-java-stable) 개체가 null인지 확인합니다. 아니라면 이미지 인사이트 토큰, 태그의 수, 작업의 수 및 첫 번째 작업 유형을 인쇄합니다.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>애플리케이션 실행

다음을 사용하여 앱을 빌드할 수 있습니다.

```console
gradle build
```

`run` 목표를 사용하여 애플리케이션을 실행합니다.

```console
gradle run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 빌드](../../tutorial-bing-visual-search-single-page-app.md)
