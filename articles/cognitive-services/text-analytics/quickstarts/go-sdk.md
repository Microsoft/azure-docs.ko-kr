---
title: '빠른 시작: Go용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 Azure Cognitive Services의 Go Text Analytics 클라이언트 라이브러리를 사용하여 언어를 감지합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 01/29/2020
ms.author: aahi
ms.openlocfilehash: bde8d418792e3d481c9e9a74ee98998492754aae
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992330"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>빠른 시작: Go용 Text Analytics 클라이언트 라이브러리 사용

[참조 설명서](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [패키지(Github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> 이 빠른 시작은 Text Analytics 버전 2.1에만 적용됩니다. 현재 Go용 v3 클라이언트 라이브러리를 사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 최신 버전의 [Go](https://golang.org/dl/)

## <a name="setting-up"></a>설치

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure 리소스 만들기 

[!INCLUDE [text-analytics-resource-creation](../includes/quickstarts/resource-creation.md)]

### <a name="create-a-new-go-project"></a>새 Go 프로젝트 만들기

콘솔 창(cmd, PowerShell, 터미널, Bash)에서 Go 프로젝트에 대한 새 작업 영역을 만들고 해당 작업 영역으로 이동합니다. 작업 영역에는 다음 세 개의 폴더가 있습니다. 

* **src** - 이 디렉터리에는 소스 코드와 패키지가 포함되어 있습니다. 여기에는 `go get` 명령으로 설치된 패키지가 모두 있습니다.
* **pkg** - 이 디렉터리에는 컴파일된 Go 패키지 개체가 포함되어 있습니다. 이러한 파일의 확장명은 모두 `.a`입니다.
* **bin** - 이 디렉터리에는 `go install`을 실행할 때 만들어지는 이진 실행 파일이 포함되어 있습니다.

> [!TIP]
> [Go 작업 영역](https://golang.org/doc/code.html#Workspaces)의 구조에 대해 자세히 알아보세요. 이 가이드에는 `$GOPATH` 및 `$GOROOT` 설정에 대한 정보가 있습니다.

`my-app`이라는 작업 영역과 `src`, `pkg` 및 `bin`에 필요한 하위 디렉터리를 만듭니다.

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Go용 Text Analytics 클라이언트 라이브러리 설치

Go용 클라이언트 라이브러리를 설치합니다. 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

dep를 사용하는 경우에는 리포지토리 내에서 다음을 실행합니다.

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Go 애플리케이션 만들기

다음으로, `src/quickstart.go`라는 파일을 만듭니다.

```bash
$ cd src
$ touch quickstart.go
```

즐겨찾는 IDE 또는 텍스트 편집기에서 `quickstart.go`를 엽니다. 그런 다음, 패키지 이름을 추가하고 다음 라이브러리를 가져옵니다.

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>개체 모델 

Text Analytics 클라이언트는 키를 사용하여 Azure에 인증하는 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 개체입니다. 클라이언트는 텍스트를 단일 문자열 또는 일괄 처리로 분석하는 몇 가지 메서드를 제공합니다. 

텍스트는 사용된 메서드에 따라 `id`, `text` 및 `language` 특성의 조합이 포함된 `dictionary` 개체인 `documents`의 목록으로 API에 보내집니다. `text` 특성은 분석할 텍스트를 `language` 원본에 저장하며, `id`는 임의의 값이 될 수 있습니다. 

응답 개체는 각 문서에 대한 분석 정보가 포함된 목록입니다. 

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 Python용 Text Analytics 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [엔터티 인식](#entity-recognition)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증


새 함수에서 리소스의 Azure 엔드포인트 및 구독 키에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

새 [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) 개체를 만듭니다. 키를 [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 함수에 전달하면 해당 키가 클라이언트의 `authorizer` 속성에 전달됩니다.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>정서 분석

`SentimentAnalysis()`라는 새 함수를 만들고 이전에 만든 `GetTextAnalyticsClient()` 메서드를 사용하여 클라이언트를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `Language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

클라이언트의 [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 함수를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 감정 점수를 인쇄합니다. 점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

프로젝트에서 `SentimentAnalysis()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>언어 검색

`LanguageDetection()`라는 새 함수를 만들고 이전에 만든 `GetTextAnalyticsClient()` 메서드를 사용하여 클라이언트를 만듭니다. 분석하려는 문서가 포함된 [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) 개체 목록을 만듭니다. 각 개체에는 `id` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하며, `id`는 임의의 값이 될 수 있습니다. 

클라이언트의 [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage)를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 검색된 언어를 인쇄합니다.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

프로젝트에서 `LanguageDetection()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>엔터티 인식

`ExtractEntities()`라는 새 함수를 만들고 이전에 만든 `GetTextAnalyticsClient()` 메서드를 사용하여 클라이언트를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다. 

클라이언트의 [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities)를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 엔터티 점수를 인쇄합니다.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

프로젝트에서 `ExtractEntities()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 1
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 0, Length: 9,   Score: 1.0
    Name: Bill Gates,       Type: Person,   Sub-Type: N/A
    Offset: 25, Length: 10, Score: 0.999847412109375
    Name: Paul Allen,       Type: Person,   Sub-Type: N/A
    Offset: 40, Length: 10, Score: 0.9988409876823425
    Name: April 4,  Type: Other,    Sub-Type: N/A
    Offset: 54, Length: 7,  Score: 0.8
    Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
    Offset: 54, Length: 13, Score: 0.8
    Name: BASIC,    Type: Other,    Sub-Type: N/A
    Offset: 89, Length: 5,  Score: 0.8
    Name: Altair 8800,      Type: Other,    Sub-Type: N/A
    Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
    Name: Microsoft,        Type: Organization,     Sub-Type: N/A
    Offset: 21, Length: 9,  Score: 0.999755859375
    Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
    Offset: 60, Length: 7,  Score: 0.9911284446716309
    Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
    Offset: 71, Length: 13, Score: 0.8
    Name: Seattle,  Type: Location, Sub-Type: N/A
    Offset: 88, Length: 7,  Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>핵심 문구 추출

`ExtractKeyPhrases()`라는 새 함수를 만들고 이전에 만든 `GetTextAnalyticsClient()` 메서드를 사용하여 클라이언트를 만듭니다. 분석하려는 문서가 포함된 [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) 개체 목록을 만듭니다. 각 개체에는 `id`, `language` 및 `text` 특성이 포함됩니다. `text` 특성은 분석할 텍스트를 저장하고, `language`는 문서의 언어이고, `id`는 아무 값이나 가능합니다.

클라이언트의 [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases)를 호출하고 결과를 가져옵니다. 그런 다음, 결과를 반복하고 각 문서의 ID 및 추출된 핵심 구를 인쇄합니다.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

프로젝트에서 `ExtractKeyPhrases()`를 호출합니다.

### <a name="output"></a>출력

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
