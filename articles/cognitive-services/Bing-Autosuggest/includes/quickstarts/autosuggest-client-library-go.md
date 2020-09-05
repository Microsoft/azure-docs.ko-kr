---
title: Bing Autosuggest Go 클라이언트 라이브러리 빠른 시작
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: c0129ff25f1df492ab6eba9f49add18d5321a3e8
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88246129"
---
Go용 Bing Autosuggest 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 라이브러리를 설치하고 기본 작업에 대한 예제를 사용해보십시오.

Go용 Bing Autosuggest 클라이언트 라이브러리를 사용하여 부분 쿼리 문자열을 기반으로 검색 제안을 가져옵니다.

[참조 설명서](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [샘플 코드](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 [체험 구독을 만들 수 있습니다](https://azure.microsoft.com/free/cognitive-services).
* 최신 버전의 [Go](https://golang.org/dl/).

Azure 리소스를 만들어 Bing Autosuggest 클라이언트 라이브러리 사용을 시작합니다. 아래에서 적합한 리소스 종류를 선택합니다.

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>환경 변수 만들기

>[!NOTE]
> 2019년 7월 1일 이후에 생성된 리소스의 엔드포인트는 아래에 표시된 사용자 지정 하위 도메인 형식을 사용합니다. 자세한 내용 및 지역별 엔드포인트의 전체 목록은 [Cognitive Services에 대한 사용자 지정 하위 도메인 이름](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains)을 참조하세요.

만든 리소스의 키 및 엔드포인트를 사용하여 인증을 위한 두 가지 환경 변수를 만듭니다.
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`: 요청을 인증하기 위한 리소스 키입니다.
* `AUTOSUGGEST_ENDPOINT`: API 요청을 보내기 위한 리소스 엔드포인트입니다. `https://<your-custom-subdomain>.api.cognitive.microsoft.com`과 같이 표시되어야 합니다.

운영 체제에 대한 지침을 사용합니다.
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

## <a name="create-a-new-go-project"></a>새 Go 프로젝트 만들기

콘솔 창(cmd, PowerShell, 터미널, Bash)에서 Go 프로젝트에 대한 새 작업 영역을 만들고 해당 작업 영역으로 이동합니다. 작업 영역에는 다음 세 개의 폴더가 있습니다.

* **src**: 이 디렉터리에는 소스 코드와 패키지가 포함되어 있습니다. 여기에는 `go get` 명령으로 설치된 패키지가 모두 있습니다.
* **pkg**: 이 디렉터리에는 컴파일된 Go 패키지 개체가 포함되어 있습니다. 이러한 파일의 확장명은 모두 `.a`입니다.
* **bin**: 이 디렉터리에는 `go install`을 실행할 때 만들어지는 이진 실행 파일이 포함되어 있습니다.

> [!TIP]
> [Go 작업 영역](https://golang.org/doc/code.html#Workspaces)의 구조에 대해 자세히 알아보세요. 이 가이드에는 `$GOPATH` 및 `$GOROOT` 설정에 대한 정보가 있습니다.

`my-app`이라는 작업 영역과 `src`, `pkg` 및 `bin`에 필요한 하위 디렉터리를 만들어 보겠습니다.

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Go용 클라이언트 라이브러리 설치

이제 Go용 클라이언트 라이브러리를 설치하겠습니다.

```bash
$ go get -u <library-location-or-url>
```

dep를 사용하는 경우에는 리포지토리 내에서 다음을 실행합니다.

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Go 애플리케이션 만들기

그런 다음, `src/sample-app.go`라는 파일을 만들겠습니다.

```bash
$ cd src
$ touch sample-app.go
```

`sample-app.go`를 열어 패키지 이름을 추가한 후, 다음 라이브러리를 가져옵니다.

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

`main`이라는 함수를 만듭니다. 그런 다음, Bing Autosuggest 키 및 엔드포인트에 대한 환경 변수를 만듭니다.

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>코드 예제

다음 코드 샘플에서는 Go용 Bing Autosuggest 클라이언트 라이브러리를 사용하여 기본 작업을 완료하는 방법을 보여 줍니다.

* [클라이언트 인증](#authenticate-the-client)
* [API 요청 보내기](#send-an-api-request)

### <a name="authenticate-the-client"></a>클라이언트 인증

> [!NOTE]
> 이 빠른 시작에서는 `BING_AUTOSUGGEST_SUBSCRIPTION_KEY`라는 Bing Autosuggest 키와 `BING_AUTOSUGGEST_ENDPOINT`라는 엔드포인트에 대한 [환경 변수를 만들었다고](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) 가정합니다.

`main()` 함수에서 엔드포인트 및 키를 사용하여 클라이언트를 인스턴스화합니다.

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>API 요청 보내기

동일한 메서드에서 클라이언트의 [AutoSuggestMethodAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) 메서드를 사용하여 Bing에 쿼리를 보냅니다. 그런 다음, [Suggestions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions?view=azure-dotnet) 응답을 반복하고 첫 번째 제안을 인쇄합니다.

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션 디렉터리에서 `go run [arguments]` 명령을 사용하여 Go 애플리케이션을 실행합니다.

```Go
go run sample-app.go
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
- [Bing Autosuggest API v7 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
