---
title: '빠른 시작: C#용 Text Analytics 클라이언트 라이브러리 | Microsoft Docs'
description: C#용 Text Analytics 클라이언트 라이브러리 시작
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987915"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

[v3 참조 설명서](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 패키지(NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

[v2 참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>설치

### <a name="create-a-new-net-core-application"></a>새 .NET Core 애플리케이션 만들기

Visual Studio IDE를 사용하여 새 .NET Core 콘솔 앱을 만듭니다. 이렇게 하면 *program.cs*라는 단일 C# 원본 파일이 포함된 "Hello World" 프로젝트가 생성됩니다.

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

**솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기**를 선택하고, **시험판 포함**을 선택하고, `Azure.AI.TextAnalytics`를 검색합니다. 해당 항목을 클릭한 다음, **설치**를 클릭합니다. [패키지 관리자 콘솔](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)을 사용할 수도 있습니다.

*program.cs* 파일을 열고 다음 `using` 지시문을 추가합니다.

```csharp
using System;
using Azure.AI.TextAnalytics;
```

애플리케이션의 `Program` 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

애플리케이션의 `Main` 메서드를 바꿉니다. 나중에 여기서 호출되는 메서드를 정의합니다.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

**솔루션 탐색기**에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택하여 클라이언트 라이브러리를 설치합니다. 열리는 패키지 관리자에서 **찾아보기**를 선택하고 `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`를 검색합니다. 해당 항목을 클릭한 다음, **설치**를 클릭합니다. [패키지 관리자 콘솔](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)을 사용할 수도 있습니다.

*program.cs* 파일을 열고 다음 `using` 지시문을 추가합니다.

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

애플리케이션의 `Program` 클래스에서 리소스의 키 및 엔드포인트에 대한 변수를 만듭니다. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

애플리케이션의 `Main` 메서드를 바꿉니다. 나중에 여기서 호출되는 메서드를 정의합니다.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>개체 모델

Text Analytics 클라이언트는 키를 사용하여 Azure를 인증하고 텍스트를 단일 문자열 또는 일괄 처리로 허용하는 함수를 제공하는 `TextAnalyticsClient` 개체입니다. 텍스트는 API에 동기식 또는 비동기식으로 보낼 수 있습니다. 응답 개체에는 보내는 각 문서에 대한 분석 정보가 포함됩니다. 

버전 `3.0-preview`를 사용하는 경우 선택적 `TextAnalyticsClientOptions` 인스턴스를 사용하여 다양한 기본 설정(예: 기본 언어 또는 국가 힌트)으로 클라이언트를 초기화할 수 있습니다. Azure Active Directory 토큰을 사용하여 인증할 수도 있습니다. 

## <a name="code-examples"></a>코드 예제

* [감정 분석](#sentiment-analysis)
* [언어 감지](#language-detection)
* [명명된 엔터티 인식](#named-entity-recognition-ner)
* [엔터티 연결](#entity-linking)
* [핵심 구 추출](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>클라이언트 인증

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

이전의 기본 메서드가 엔드포인트와 키를 사용하여 새 클라이언트 개체를 만들었는지 확인합니다. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

자격 증명을 저장하여 클라이언트의 요청에 추가하는 새 `ApiKeyServiceClientCredentials` 클래스를 만듭니다. 이 안에 `Ocp-Apim-Subscription-Key` 헤더에 키를 추가하는 `ProcessHttpRequestAsync()`에 대한 재정의를 만듭니다.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

엔드포인트 및 키를 포함하는 `ApiKeyServiceClientCredentials` 개체를 사용하여 [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) 개체를 인스턴스화하는 메서드를 만듭니다.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>정서 분석

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

앞에서 만든 클라이언트를 사용하고 해당 `AnalyzeSentiment()` 함수를 호출하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 `Response<AnalyzeSentimentResult>` 개체에는 전체 입력 문서의 감정 레이블과 점수뿐 아니라 각 문장의 감정 분석(성공할 경우) 또는 `Value.ErrorMessage`(실패할 경우)가 포함됩니다.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>출력

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

앞에서 만든 클라이언트를 사용하고 해당 [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) 함수를 호출하는 `SentimentAnalysisExample()`이라는 새 함수를 만듭니다. 반환된 [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) 개체에는 성공하면 감정 `Score`가 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 

점수가 0에 가까울수록 부정적인 감정을 나타내고, 1에 가까울수록 긍정적인 감정을 나타냅니다.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>언어 검색

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)


앞에서 만든 클라이언트를 사용하고 해당 `DetectLanguage()` 함수를 호출하는 `LanguageDetectionExample()`이라는 새 함수를 만듭니다. 반환된 `Response<DetectLanguageResult>` 개체에는 성공하면 `Value.PrimaryLanguage`에서 검색된 언어가 포함되고, 그렇지 않으면 `Value.ErrorMessage`가 포함됩니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다. 다른 기본값을 설정하려면 `TextAnalyticsClientOptions.DefaultCountryHint` 속성을 설정하고 클라이언트를 초기화 중에 이를 전달합니다.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>출력

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

앞에서 만든 클라이언트를 사용하고 해당 [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `languageDetectionExample()`이라는 새 함수를 만듭니다. 반환된 [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) 개체에는 성공하면 `DetectedLanguages`에서 검색된 언어 목록이 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다.  처음 반환된 언어를 출력합니다.

> [!Tip]
> 일부 경우에는 입력에 따라 언어를 명확하게 구분하는 것이 어려울 수 있습니다. 2자로 된 국가 코드는 `countryHint` 매개 변수를 사용하여 지정할 수 있습니다. 기본적으로 API는 "US"를 기본 countryHint로 사용합니다. 이 동작을 제거하려면 이 값을 빈 문자열의 `countryHint = ""`로 설정하여 이 매개 변수를 다시 설정하면 됩니다.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>출력

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>NER(명명된 엔터티 인식)

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)


> [!NOTE]
> 버전 `3.0-preview`에서 다음을 수행합니다.
> * NER에는 개인 정보를 검색하기 위한 별도의 방법이 포함되어 있습니다. 
> * 엔터티 연결은 NER과 별개의 요청입니다.

아래 코드는 공개 미리 보기로 제공되는 NER v3를 사용하여 개인 정보를 검색하기 위한 것입니다.


앞에서 만든 클라이언트를 사용하는 `EntityRecognitionExample()`이라는 새 함수를 만들고, `RecognizeEntities()` 함수를 호출하여 결과를 반복합니다. 반환된 `Response<RecognizeEntitiesResult>` 개체에는 성공하면 `Value.NamedEntities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `Value.ErrorMessage`가 포함됩니다. 검색된 각 엔터티에 대해 해당 형식 및 하위 형식(있는 경우)을 인쇄합니다.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>출력

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>NER을 사용하여 개인 정보 검색

앞에서 만든 클라이언트를 사용하는 `EntityPIIExample()`이라는 새 함수를 만들고, `RecognizePiiEntities()` 함수를 호출하여 결과를 반복합니다. 이전 함수와 마찬가지로, 반환된 `Response<RecognizeEntitiesResult>` 개체에는 성공하면 `Value.NamedEntities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `Value.ErrorMessage`가 포함됩니다.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>출력

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>엔터티 연결

앞에서 만든 클라이언트를 사용하는 `EntityLinkingExample()`이라는 새 함수를 만들고, `RecognizeLinkedEntities()` 함수를 호출하여 결과를 반복합니다. 반환된 `Response<RecognizeLinkedEntitiesResult>` 개체에는 성공하면 `Value.LinkedEntities`에서 검색된 엔티티 목록이 포함되고, 그렇지 않으면 `Value.ErrorMessage`가 포함됩니다. 연결된 엔터티가 고유하게 식별되므로 동일한 엔터티의 발생은 `LinkedEntity` 개체 아래에서 `LinkedEntityMatch` 개체 목록으로 그룹화됩니다.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
        }
    }
}
```

### <a name="output"></a>출력

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

> [!NOTE]
> 버전 2.1에서 엔터티 연결은 NER 응답에 포함됩니다.

앞에서 만든 클라이언트를 사용하고 해당 [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `RecognizeEntitiesExample()`이라는 새 함수를 만듭니다. 결과를 반복합니다. 반환된 [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) 개체에는 성공하면 `Entities`에서 검색된 엔티티 목록이 포함되고 그렇지 않으면 `errorMessage`가 포함됩니다. 검색된 각 엔터티에 대해 해당 Type(형식), Sub-Type(하위 형식), Wikipedia 이름(있는 경우) 외에도 원본 텍스트의 위치를 출력합니다.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>핵심 문구 추출

#### <a name="version-30-previewtabversion-3"></a>[버전 3.0 미리 보기](#tab/version-3)

앞에서 만든 클라이언트를 사용하고 해당 `ExtractKeyPhrases()` 함수를 호출하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다. 결과에는 성공하면 `Value.KeyPhrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `Value.ErrorMessage`가 포함됩니다. 검색된 핵심 구를 출력합니다.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>출력

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21tabversion-2"></a>[버전 2.1](#tab/version-2)

앞에서 만든 클라이언트를 사용하고 해당 [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) 함수를 호출하는 `KeyPhraseExtractionExample()`이라는 새 함수를 만듭니다. 결과에는 성공하면 `KeyPhrases`에서 검색된 핵심 구 목록이 포함되고, 그렇지 않으면 `errorMessage`가 포함됩니다. 검색된 핵심 구를 출력합니다.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>출력

```console
Key phrases:
    cat
    veterinarian
```

---