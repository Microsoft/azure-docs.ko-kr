---
title: C#-Content Moderator의 사용자 지정 단어 목록에 대해 텍스트 확인
titleSuffix: Azure Cognitive Services
description: C#용 Content Moderator SDK를 사용하여 사용자 지정 용어 목록으로 텍스트를 조정하는 방법
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 90993ea2ee66a23b5b629dfaf5bb34298ce15d9b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936285"
---
# <a name="check-text-against-a-custom-term-list-in-c"></a>C#에서 사용자 지정 단어 목록에 대해 텍스트 확인

Azure Content Moderator에서 기본 전역 용어 목록은 대부분의 콘텐츠 조정 요구에 적합합니다. 그러나 조직에 관련된 용어에 대해 차단해야 할 수 있습니다. 예를 들어 추가 검토를 위해 경쟁 업체 이름에 태그를 지정할 수 있습니다. 

[.Net 용 CONTENT MODERATOR SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 를 사용 하 여 텍스트 조정 API와 함께 사용할 사용자 지정 약관 목록을 만들 수 있습니다.

이 문서에서는 .NET용 Content Moderator SDK를 사용하여 다음 작업을 수행할 수 있도록 지원하는 정보 및 코드 샘플을 제공합니다.
- 목록을 만듭니다.
- 목록에 용어를 추가합니다.
- 목록의 용어에 대해 용어를 차단합니다.
- 목록에서 용어를 삭제합니다.
- 목록을 삭제합니다.
- 목록 정보를 편집합니다.
- 목록에 대한 변경 내용이 새 검색에 포함되도록 인덱스를 새로 고칩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="sign-up-for-content-moderator-services"></a>Content Moderator 서비스 등록

REST API 또는 SDK를 통해 Content Moderator 서비스를 사용하려면 먼저 구독 키가 필요합니다. 이를 구하려면 Azure Portal에서 [Content Moderator 서비스를 구독](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)합니다.

## <a name="create-your-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. 솔루션에 새 **콘솔 앱(.NET Framework)** 프로젝트를 추가합니다.

1. 프로젝트 이름을 **TermLists**로 지정합니다. 이 프로젝트를 솔루션의 단일 시작 프로젝트로 선택합니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

TermLists 프로젝트에 대해 다음 NuGet 패키지를 설치합니다.

- Microsoft.Azure.CognitiveServices.ContentModerator
- Microsoft.Rest.ClientRuntime
- Microsoft.Rest.ClientRuntime.Azure
- Newtonsoft.Json

### <a name="update-the-programs-using-statements"></a>프로그램의 using 문 업데이트

다음 `using` 문을 추가합니다.

```csharp
using Microsoft.Azure.CognitiveServices.ContentModerator;
using Microsoft.Azure.CognitiveServices.ContentModerator.Models;
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
```

### <a name="create-the-content-moderator-client"></a>Content Moderator 클라이언트 만들기

다음 코드를 추가하여 구독에 대한 Content Moderator 클라이언트를 만듭니다. `AzureEndpoint`및 필드를 `CMSubscriptionKey` 끝점 URL 및 구독 키의 값으로 업데이트 합니다. Azure Portal에서 리소스의 **빠른 시작** 탭에서 찾을 수 있습니다.

```csharp
/// <summary>
/// Wraps the creation and configuration of a Content Moderator client.
/// </summary>
/// <remarks>This class library contains insecure code. If you adapt this 
/// code for use in production, use a secure method of storing and using
/// your Content Moderator subscription key.</remarks>
public static class Clients
{
    /// <summary>
    /// The base URL fragment for Content Moderator calls.
    /// </summary>
    private static readonly string AzureEndpoint = "YOUR ENDPOINT URL";

    /// <summary>
    /// Your Content Moderator subscription key.
    /// </summary>
    private static readonly string CMSubscriptionKey = "YOUR API KEY";

    /// <summary>
    /// Returns a new Content Moderator client for your subscription.
    /// </summary>
    /// <returns>The new client.</returns>
    /// <remarks>The <see cref="ContentModeratorClient"/> is disposable.
    /// When you have finished using the client,
    /// you should dispose of it either directly or indirectly. </remarks>
    public static ContentModeratorClient NewClient()
    {
        // Create and initialize an instance of the Content Moderator API wrapper.
        ContentModeratorClient client = new ContentModeratorClient(new ApiKeyServiceClientCredentials(CMSubscriptionKey));

        client.Endpoint = AzureEndpoint;
        return client;
    }
}
```

### <a name="add-private-properties"></a>프라이빗 속성 추가

TermLists 네임스페이스, Program 클래스에 다음 프라이빗 속성을 추가합니다.

```csharp
/// <summary>
/// The language of the terms in the term lists.
/// </summary>
private const string lang = "eng";

/// <summary>
/// The minimum amount of time, in milliseconds, to wait between calls
/// to the Content Moderator APIs.
/// </summary>
private const int throttleRate = 3000;

/// <summary>
/// The number of minutes to delay after updating the search index before
/// performing image match operations against the list.
/// </summary>
private const double latencyDelay = 0.5;
```

## <a name="create-a-term-list"></a>용어 목록 만들기

**ContentModeratorClient.ListManagementTermLists.Create**를 사용하여 용어 목록을 만듭니다. **Create**에 대한 첫 번째 매개 변수는 MIME 형식을 포함하는 문자열로, “application/json”이어야 합니다. 자세한 내용은 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)를 참조 하세요. 두 번째 매개 변수는 새 용어 목록에 대한 이름 및 설명을 포함하는 **Body** 개체입니다.

> [!NOTE]
> 최대 **5개 용어 목록**으로 제한되고, 각 목록은 **10,000개 용어를 초과하지 않아야** 합니다.

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

> [!NOTE]
> Content Moderator 서비스 키에는 RPS(초당 요청 수) 속도 제한이 있으며, 제한을 초과하는 경우 SDK에서 429 오류 코드로 예외를 throw합니다. 체험 계층 키에는 하나의 RPS 속도 제한이 있습니다.

```csharp
/// <summary>
/// Creates a new term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <returns>The term list ID.</returns>
static string CreateTermList (ContentModeratorClient client)
{
    Console.WriteLine("Creating term list.");

    Body body = new Body("Term list name", "Term list description");
    TermList list = client.ListManagementTermLists.Create("application/json", body);
    if (false == list.Id.HasValue)
    {
        throw new Exception("TermList.Id value missing.");
    }
    else
    {
        string list_id = list.Id.Value.ToString();
        Console.WriteLine("Term list created. ID: {0}.", list_id);
        Thread.Sleep(throttleRate);
        return list_id;
    }
}
```

## <a name="update-term-list-name-and-description"></a>용어 목록 이름 및 설명 업데이트

**ContentModeratorClient.ListManagementTermLists.Update**를 사용하여 용어 목록 정보를 업데이트합니다. **Update**에 대한 첫 번째 매개 변수는 용어 목록 ID입니다. 두 번째 매개 변수는 MIME 형식으로, “application/json”이어야 합니다. 자세한 내용은 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f685)를 참조 하세요. 세 번째 매개 변수는 새 이름 및 설명을 포함하는 **Body** 개체입니다.

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Update the information for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="name">The new name for the term list.</param>
/// <param name="description">The new description for the term list.</param>
static void UpdateTermList (ContentModeratorClient client, string list_id, string name = null, string description = null)
{
    Console.WriteLine("Updating information for term list with ID {0}.", list_id);
    Body body = new Body(name, description);
    client.ListManagementTermLists.Update(list_id, "application/json", body);
    Thread.Sleep(throttleRate);
}
```

## <a name="add-a-term-to-a-term-list"></a>용어 목록에 용어 추가

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Add a term to the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to update.</param>
/// <param name="term">The term to add to the term list.</param>
static void AddTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Adding term \"{0}\" to term list with ID {1}.", term, list_id);
    client.ListManagementTerm.AddTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

## <a name="get-all-terms-in-a-term-list"></a>용어 목록에 모든 용어 가져오기

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Get all terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to get all terms.</param>
static void GetAllTerms(ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Getting terms in term list with ID {0}.", list_id);
    Terms terms = client.ListManagementTerm.GetAllTerms(list_id, lang);
    TermsData data = terms.Data;
    foreach (TermsInList term in data.Terms)
    {
        Console.WriteLine(term.Term);
    }
    Thread.Sleep(throttleRate);
}
```

## <a name="add-code-to-refresh-the-search-index"></a>검색 인덱스를 새로 고치는 코드 추가

용어 목록을 변경한 후 다음 번에 텍스트를 차단하도록 용어 목록을 사용할 때 포함될 변경 내용에 대한 해당 검색 인덱스를 새로 고칩니다. 데스크톱의 검색 엔진(활성화된 경우) 또는 웹 검색 엔진이 새 파일 또는 페이지를 포함하도록 해당 인덱스를 지속적으로 새로 고치는 방법과 유사합니다.

**ContentModeratorClient.ListManagementTermLists.RefreshIndexMethod**를 사용하여 용어 목록 검색 인덱스를 새로 고칩니다.

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Refresh the search index for the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to refresh.</param>
static void RefreshSearchIndex (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Refreshing search index for term list with ID {0}.", list_id);
    client.ListManagementTermLists.RefreshIndexMethod(list_id, lang);
    Thread.Sleep((int)(latencyDelay * 60 * 1000));
}
```

## <a name="screen-text-using-a-term-list"></a>용어 목록을 사용하여 텍스트 차단

다음 매개 변수를 사용하는 **ContentModeratorClient.TextModeration.ScreenText**로 용어 목록을 사용하여 텍스트를 차단합니다.

- 용어 목록에서 용어의 언어입니다.
- "text/html", "text/xml", "text/markdown" 또는 "text/plain"일 수 있는 MIME 형식입니다.
- 차단할 텍스트입니다.
- 부울 값입니다. 이 필드를 **true**로 설정하여 차단하기 전에 텍스트를 자동으로 고칩니다.
- 부울 값입니다. 텍스트의 개인 데이터를 검색 하려면이 필드를 **true** 로 설정 합니다.
- 용어 목록 ID입니다.

자세한 내용은 [API 참조](https://westus2.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)를 참조 하세요.

**ScreenText**는 Content Moderator가 차단에서 검색한 모든 용어를 나열하는 **Terms** 속성이 있는 **Screen** 개체를 반환합니다. Content Moderator가 차단하는 동안 용어를 검색하지 않는 경우 **Terms** 속성은 **Null** 값을 갖습니다.

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Screen the indicated text for terms in the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to use to screen the text.</param>
/// <param name="text">The text to screen.</param>
static void ScreenText (ContentModeratorClient client, string list_id, string text)
{
    Console.WriteLine("Screening text: \"{0}\" using term list with ID {1}.", text, list_id);
    Screen screen = client.TextModeration.ScreenText(lang, "text/plain", text, false, false, list_id);
    if (null == screen.Terms)
    {
        Console.WriteLine("No terms from the term list were detected in the text.");
    }
    else
    {
        foreach (DetectedTerms term in screen.Terms)
        {
            Console.WriteLine(String.Format("Found term: \"{0}\" from list ID {1} at index {2}.", term.Term, term.ListId, term.Index));
        }
    }
    read.Sleep(throttleRate);
}
```

## <a name="delete-terms-and-lists"></a>용어 및 목록 삭제

용어 또는 목록을 삭제하는 것은 간단합니다. SDK를 사용하여 다음 작업을 수행합니다.

- 용어를 삭제합니다. (**ContentModeratorClient.ListManagementTerm.DeleteTerm**)
- 목록을 삭제하지 않고 목록의 모든 용어를 삭제합니다. (**ContentModeratorClient.ListManagementTerm.DeleteAllTerms**)
- 목록 및 모든 해당 콘텐츠를 삭제합니다. (**ContentModeratorClient.ListManagementTermLists.Delete**)

### <a name="delete-a-term"></a>용어 삭제

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Delete a term from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete the term.</param>
/// <param name="term">The term to delete.</param>
static void DeleteTerm (ContentModeratorClient client, string list_id, string term)
{
    Console.WriteLine("Removed term \"{0}\" from term list with ID {1}.", term, list_id);
    client.ListManagementTerm.DeleteTerm(list_id, term, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-all-terms-in-a-term-list"></a>용어 목록에서 모든 용어 삭제

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Delete all terms from the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list from which to delete all terms.</param>
static void DeleteAllTerms (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Removing all terms from term list with ID {0}.", list_id);
    client.ListManagementTerm.DeleteAllTerms(list_id, lang);
    Thread.Sleep(throttleRate);
}
```

### <a name="delete-a-term-list"></a>용어 목록 삭제

TermLists 네임스페이스, Program 클래스에 다음 메서드 정의를 추가합니다.

```csharp
/// <summary>
/// Delete the indicated term list.
/// </summary>
/// <param name="client">The Content Moderator client.</param>
/// <param name="list_id">The ID of the term list to delete.</param>
static void DeleteTermList (ContentModeratorClient client, string list_id)
{
    Console.WriteLine("Deleting term list with ID {0}.", list_id);
    client.ListManagementTermLists.Delete(list_id);
    Thread.Sleep(throttleRate);
}
```

## <a name="compose-the-main-method"></a>Main 메서드 작성

**TermLists**네임스페이스, **Program** 클래스에 **Main** 메서드 정의를 추가합니다. 마지막으로, **Program** 클래스 및 **TermLists** 네임스페이스를 닫습니다.

```csharp
static void Main(string[] args)
{
    using (var client = Clients.NewClient())
    {
        string list_id = CreateTermList(client);

        UpdateTermList(client, list_id, "name", "description");
        AddTerm(client, list_id, "term1");
        AddTerm(client, list_id, "term2");

        GetAllTerms(client, list_id);

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        string text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteTerm(client, list_id, "term1");

        // Always remember to refresh the search index of your list
        RefreshSearchIndex(client, list_id);

        text = "This text contains the terms \"term1\" and \"term2\".";
        ScreenText(client, list_id, text);

        DeleteAllTerms(client, list_id);
        DeleteTermList(client, list_id);

        Console.WriteLine("Press ENTER to close the application.");
        Console.ReadLine();
    }
}
```

## <a name="run-the-application-to-see-the-output"></a>애플리케이션을 실행하여 출력 확인

콘솔 출력은 다음과 같습니다.

```console
Creating term list.
Term list created. ID: 252.
Updating information for term list with ID 252.

Adding term "term1" to term list with ID 252.
Adding term "term2" to term list with ID 252.

Getting terms in term list with ID 252.
term1
term2

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term1" from list ID 252 at index 32.
Found term: "term2" from list ID 252 at index 46.

Removed term "term1" from term list with ID 252.

Refreshing search index for term list with ID 252.

Screening text: "This text contains the terms "term1" and "term2"." using term list with ID 252.
Found term: "term2" from list ID 252 at index 46.

Removing all terms from term list with ID 252.
Deleting term list with ID 252.
Press ENTER to close the application.
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작과 기타 .NET용 Content Moderator 빠른 시작을 위한 [Content Moderator .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) 및 [Visual Studio 솔루션](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator)을 가져오고 통합을 시작합니다.
