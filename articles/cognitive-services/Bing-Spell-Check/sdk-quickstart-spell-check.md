---
title: '빠른 시작: C#용 Bing Spell Check SDK로 맞춤법 검사'
titleSuffix: Azure Cognitive Services
description: Bing Spell Check REST API를 사용하여 맞춤법 및 문법 검사를 시작합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 12/16/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e1e05ca9b63bda3373afb8a090118953d89ad8f9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934228"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>빠른 시작: C#용 Bing Spell Check SDK로 맞춤법 검사

이 빠른 시작을 사용하여 C#용 Bing Spell Check SDK로 맞춤법 검사를 시작할 수 있습니다. Bing Spell Check에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, SDK를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck)에서 확인할 수 있습니다.

## <a name="application-dependencies"></a>애플리케이션 종속성

* [Visual Studio 2017 이상](https://visualstudio.microsoft.com/downloads/)의 모든 버전.
* Bing Spell Check [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck)

Bing Spell Check SDK를 프로젝트에 추가하려면 Visual Studio의 **솔루션 탐색기**에서 **NuGet 패키지 관리**를 선택합니다. `Microsoft.Azure.CognitiveServices.Language.SpellCheck` 패키지를 추가합니다. 이 패키지는 다음 종속성도 설치합니다.

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

1. Visual Studio에서 새 C# 콘솔 솔루션을 만듭니다. 다음 `using` 문을 추가합니다.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. 새 클래스를 만듭니다. 그런 다음, 구독 키를 사용하여 맞춤법 검사 요청을 보내는 `SpellCheckCorrection()`이라는 비동기 함수를 만듭니다.

3. 새 `ApiKeyServiceClientCredentials` 개체를 만들어서 클라이언트를 인스턴스화합니다. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>요청 보내기 및 응답 읽기

1. 앞서 작성한 함수에서 다음 단계를 수행합니다. 클라이언트를 통해 맞춤법 검사 요청을 보냅니다. 검사할 텍스트를 `text` 매개 변수에 추가하고 모드를 `proof`로 설정합니다.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. 첫 번째 맞춤법 검사 결과(있는 경우)를 가져옵니다. 철자가 틀린 첫 번째 단어(토큰), 토큰 유형 및 제안 수를 출력합니다.

    ```csharp
    var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
    if (firstspellCheckResult != null)
    {
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. 첫 번째 제안된 수정 사항(있는 경우)을 가져옵니다. 제안 점수 및 제안 단어를 인쇄합니다. 

    ```csharp
    var suggestions = firstspellCheckResult.Suggestions;

    if (suggestions?.Count > 0)
    {
        var firstSuggestion = suggestions.FirstOrDefault();
        Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
        Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
    }
    ```

## <a name="run-the-application"></a>애플리케이션 실행

프로젝트를 빌드한 후 실행합니다. Visual Studio를 사용하는 경우 **F5**를 눌러 파일을 디버그합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](tutorials/spellcheck.md)

- [Bing Spell Check API란?](overview.md)
- [Bing Spell Check C# SDK 참조 가이드](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)
