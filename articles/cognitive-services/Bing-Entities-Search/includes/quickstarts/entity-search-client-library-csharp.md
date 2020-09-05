---
title: Bing Entity Search C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 27951a6da2c5e4a9d17e8b332d8ee9706701649d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88931893"
---
C#용 Bing Entity Search 클라이언트 라이브러리를 통해 엔터티 검색을 시작하려면 이 빠른 시작을 사용하세요. Bing Entity Search에는 대부분의 프로그래밍 언어와 호환되는 REST API가 있는 반면, 클라이언트 라이브러리를 사용하면 서비스를 애플리케이션에 쉽게 통합할 수 있습니다. 이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch)에서 확인할 수 있습니다.


## <a name="prerequisites"></a>사전 요구 사항

* [Visual Studio 2017 이상](https://www.visualstudio.com/downloads/)의 모든 버전.
* NuGet 패키지로 사용 가능한 [Json.NET](https://www.newtonsoft.com/json) 프레임워크.
* Linux/MacOS를 사용하는 경우 이 애플리케이션은 [Mono](https://www.mono-project.com/)를 사용하여 실행할 수 있습니다.
* [Bing News Search SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0) 이 패키지를 설치하면 다음도 설치됩니다.
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Bing Entity Search 클라이언트 라이브러리를 Visual Studio 프로젝트에 추가하려면 **솔루션 탐색기**에서 **NuGet 패키지 관리** 옵션을 사용하고 `Microsoft.Azure.CognitiveServices.Search.EntitySearch` 패키지를 추가합니다.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>애플리케이션 만들기 및 초기화

1. Visual Studio에서 새 C# 콘솔 솔루션을 만듭니다. 그런 다음, 주 코드 파일에 다음을 추가합니다.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>클라이언트를 만들고 검색 요청 보내기

1. 새 검색 클라이언트를 만듭니다. 새 `ApiKeyServiceClientCredentials`를 만들어서 구독 키를 추가합니다.

    ```csharp
    var client = new EntitySearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. 클라이언트의 `Entities.Search()` 함수를 사용하여 쿼리를 검색합니다.
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>엔터티 설명을 가져와서 인쇄

1. API에서 검색 결과가 반환된 경우 `entityData`에서 기본 엔터티를 가져옵니다.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. 기본 엔터티에 대한 설명 인쇄 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](../../tutorial-bing-entities-search-single-page-app.md)

* [Bing Entity Search API란?](../../overview.md )
