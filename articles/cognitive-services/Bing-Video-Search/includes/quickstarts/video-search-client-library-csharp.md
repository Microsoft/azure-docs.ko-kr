---
title: Bing Video Search C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d50a8e2c9d0263616b25e25958be6a6f0fb7fe1
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88929275"
---
C#용 Bing Video Search 클라이언트 라이브러리를 사용하여 뉴스 검색을 시작하려면 이 빠른 시작을 사용합니다. Bing Video Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, 클라이언트 라이브러리를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플에 대한 소스 코드는 추가 주석 및 기능과 함께 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch)에서 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* [Visual Studio 2017 이상](https://visualstudio.microsoft.com/downloads/)의 모든 버전.
* [NuGet 패키지로](https://www.nuget.org/packages/Newtonsoft.Json/) 사용 가능한 Json.NET 프레임워크.

Bing Video Search 클라이언트 라이브러리를 프로젝트에 추가하려면 Visual Studio의 **솔루션 탐색기**에서 **NuGet 패키지 관리**를 선택합니다. `Microsoft.Azure.CognitiveServices.Search.VideoSearch` 패키지를 추가합니다.

[[NuGet Video Search SDK 패키지]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0)를 설치하면 다음 종속성도 설치됩니다.

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>프로젝트 만들기 및 초기화

1. Visual Studio에서 새 C# 콘솔 솔루션을 만듭니다. 그런 다음, 주 코드 파일에 다음을 추가합니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. 구독 키를 사용하여 새 `ApiKeyServiceClientCredentials` 개체를 만들고 생성자를 호출하여 클라이언트를 인스턴스화합니다.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>검색 요청 보내기 및 결과 처리

1. 클라이언트를 사용하여 검색 요청을 보냅니다. 검색 쿼리에 대한 “SwiftKey”를 사용합니다.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. 모든 결과가 반환되면 `videoResults.Value[0]`를 사용하여 첫 번째 결과를 가져옵니다. 그런 다음, 비디오의 ID, 제목 및 URL을 출력합니다.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>참고 항목 

* [Bing Video Search API란?](../../overview.md)
* [Cognitive Services .NET SDK 샘플](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
