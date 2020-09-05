---
title: Bing Autosuggest C# 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: a6ae9d8975c36e7ffa8e3eadf880b4685390c5fc
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246335"
---
.NET용 Bing Autosuggest 클라이언트 라이브러리를 시작합니다. 이러한 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다.

.NET용 Bing Autosuggest 클라이언트 라이브러리를 사용하여 부분 쿼리 문자열을 기반으로 검색 제안을 가져옵니다.

[참조 설명서](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [패키지(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [샘플 코드](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 [체험 구독을 만들 수 있습니다](https://azure.microsoft.com/free/cognitive-services).
* 최신 버전의 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>환경 변수 만들기

>[!NOTE]
> 2019년 7월 1일 이후에 생성된 리소스의 엔드포인트는 아래에 표시된 사용자 지정 하위 도메인 형식을 사용합니다. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)을 참조하세요.

만든 리소스의 키 및 엔드포인트를 사용하여 인증을 위한 두 가지 환경 변수를 만듭니다.
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: 요청을 인증하기 위한 리소스 키입니다.
* `AUTOSUGGEST_ENDPOINT`: API 요청을 보내기 위한 리소스 엔드포인트입니다. `https://<your-custom-subdomain>.api.cognitive.microsoft.com`와 비슷한 형식이어야 합니다.

운영 체제에 대한 지침을 사용합니다.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

환경 변수를 추가한 후 콘솔 창을 다시 시작합니다.

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bashrc` 명령을 실행하여 변경 내용을 적용합니다.

### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`을 편집하고, 환경 변수를 추가합니다.

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

환경 변수를 추가한 후에는 콘솔 창에서 `source .bash_profile` 명령을 실행하여 변경 내용을 적용합니다.
***

## <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

선호하는 편집기 또는 IDE에서 .NET Core 애플리케이션을 새로 만듭니다.

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `bing-autosuggest-quickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 *program.cs*라는 단일 소스 파일을 사용하여 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -n bing-autosuggest-quickstart
```

새로 만든 앱 폴더로 디렉터리를 변경합니다. 다음을 통해 애플리케이션을 빌드할 수 있습니다.

```console
dotnet build
```

빌드 출력에 경고나 오류가 포함되지 않아야 합니다.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

선호하는 편집기 또는 IDE에서 프로젝트 디렉터리의 *program.cs* 파일을 엽니다. 다음 `using` 지시문을 추가합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

`Program` 클래스에서 리소스의 Azure 엔드포인트 및 키에 대한 변수를 만듭니다. 애플리케이션을 시작한 후에 환경 변수를 만든 경우 해당 변수에 액세스하려면 이를 실행 중인 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

애플리케이션의 `Main` 메서드에서 나중에 정의할 다음 메서드 호출을 추가합니다.

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

애플리케이션 디렉터리 내에서 다음 명령을 사용하여 .NET용 Bing Autosuggest 클라이언트 라이브러리를 설치합니다.

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Visual Studio IDE를 사용하는 경우 클라이언트 라이브러리는 다운로드 가능한 NuGet 패키지로 제공됩니다.

## <a name="code-examples"></a>코드 예제

여기에 나와 있는 코드 조각에서는 .NET용 Bing Autosuggest 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [Autosuggest 요청 보내기](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `AUTOSUGGEST_SUBSCRIPTION_KEY`라는 Bing Autosuggest 키와 `AUTOSUGGEST_ENDPOINT`라는 엔드포인트에 대한 [환경 변수를 만들었다고](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) 가정합니다.


새 비동기 메서드에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다. 키를 사용하여 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials?view=azure-dotnet) 개체를 만들고, 엔드포인트에서 이를 사용하여 [AutosuggestClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient?view=azure-dotnet) 개체를 만듭니다.

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

### <a name="send-an-autosuggest-request"></a>Autosuggest 요청 보내기

동일한 메서드에서 클라이언트의 [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) 메서드를 사용하여 Bing에 쿼리를 보냅니다. 그런 다음, [Suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) 응답을 반복하고 첫 번째 제안을 인쇄합니다.

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>애플리케이션 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [Azure Portal에서 리소스 그룹을 삭제합니다](../../../cognitive-services-apis-create-account.md#clean-up-resources).
* [Azure CLI에서 리소스 그룹을 삭제합니다](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Autosuggest 자습서](../../tutorials/autosuggest.md)

## <a name="see-also"></a>참고 항목

- [Bing Autosuggest란?](../../get-suggested-search-terms.md)
- [Bing Autosuggest dotnet 참조](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingautosuggest?view=azure-dotnet)
