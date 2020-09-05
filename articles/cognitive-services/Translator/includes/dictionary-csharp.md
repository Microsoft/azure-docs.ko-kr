---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 4821d4637a90acda12fec5656907ad3eedb24163
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921473"
---
[!INCLUDE [Prerequisites](prerequisites-csharp.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="create-a-net-core-project"></a>.NET Core 프로젝트 만들기

새 명령 프롬프트(또는 터미널 세션)를 열고 이 명령을 실행합니다.

```console
dotnet new console -o alternate-sample
cd alternate-sample
```

첫 번째 명령은 두 가지 작업을 수행합니다. 새 .NET 콘솔 애플리케이션을 만들고 `alternate-sample`이라는 디렉터리를 만듭니다. 두 번째 명령은 프로젝트의 디렉터리로 변경합니다.

그런 다음, Json.Net을 설치해야 합니다. 프로젝트의 디렉터리에서 다음을 실행합니다.

```console
dotnet add package Newtonsoft.Json --version 11.0.2
```

## <a name="add-required-namespaces-to-your-project"></a>프로젝트에 필요한 네임스페이스 추가

이전에 실행한 `dotnet new console` 명령은 `Program.cs`를 포함한 프로젝트를 만듭니다. 이 파일은 애플리케이션 코드가 보관되는 곳입니다. `Program.cs`를 열고 문을 사용하여 기존 항목을 바꿉니다. 이러한 문은 빌드하고 샘플 앱을 빌드하고 실행하는 데 필요한 모든 형식에 액세스할 수 있는지 확인합니다.

```csharp
using System;
using System.Net.Http;
using System.Text;
using Newtonsoft.Json;
```

## <a name="get-subscription-information-from-environment-variables"></a>환경 변수에서 구독 정보 가져오기

`Program` 클래스에 다음 줄을 추가합니다. 이러한 줄은 환경 변수에서 구독 키와 엔드포인트를 읽고 문제가 발생하면 오류를 throw합니다.

```csharp
private const string key_var = "TRANSLATOR_TEXT_SUBSCRIPTION_KEY";
private static readonly string subscriptionKey = Environment.GetEnvironmentVariable(key_var);

private const string endpoint_var = "TRANSLATOR_TEXT_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);

static Program()
{
    if (null == subscriptionKey)
    {
        throw new Exception("Please set/export the environment variable: " + key_var);
    }
    if (null == endpoint)
    {
        throw new Exception("Please set/export the environment variable: " + endpoint_var);
    }
}
// The code in the next section goes here.
```

## <a name="create-a-function-to-get-alternate-translations"></a>대체 번역을 가져오는 함수 만들기

`Program` 클래스 내에서 `AltTranslation`라는 함수를 만듭니다. 이 클래스는 Dictionary 리소스를 호출하는 데 사용되는 코드를 캡슐화하고 콘솔에 결과를 출력합니다.

```csharp
static void AltTranslation()
{
  /*
   * The code for your call to the translation service will be added to this
   * function in the next few sections.
   */
}
```

## <a name="construct-the-uri"></a>URI 생성

`AltTranslation` 함수에 이러한 줄을 추가합니다. `api-version`과 함께 두 개의 매개 변수가 선언되었음을 알 수 있습니다. 이러한 매개 변수는 번역 입력 및 출력을 설정하는 데 사용됩니다. 이 샘플에서 이는 영어(`en`)와 스페인어(`es`)입니다.

```csharp
string route = "/dictionary/lookup?api-version=3.0";
static string params_ = "from=en&to=es";
static string uri = endpoint + path + params_;
```

다음으로, 번역하려는 텍스트를 포함하는 JSON 개체를 만들고 직렬화해야 합니다. `body` 배열에 둘 이상의 개체를 전달할 수 있다는 사실을 참고하세요.

```csharp
System.Object[] body = new System.Object[] { new { Text = @"Elephants" } };
var requestBody = JsonConvert.SerializeObject(body);
```

## <a name="instantiate-the-client-and-make-a-request"></a>클라이언트를 인스턴스화하고 요청 수행

이러한 줄은 `HttpClient` 및 `HttpRequestMessage`를 인스턴스화합니다.

```csharp
using (var client = new HttpClient())
using (var request = new HttpRequestMessage())
{
  // In the next few sections you'll add code to construct the request.
}
```

## <a name="construct-the-request-and-print-the-response"></a>요청 만들기 및 응답 출력

`HttpRequestMessage` 내부에서 다음을 수행합니다.

* HTTP 메서드 선언
* 요청 URI 만들기
* 요청 본문(직렬화된 JSON 개체) 삽입
* 필수 헤더 추가
* 비동기 요청 수행
* 응답 출력

`HttpRequestMessage`에 이 코드를 추가합니다.

```csharp
// Set the method to POST
request.Method = HttpMethod.Post;

// Construct the full URI
request.RequestUri = new Uri(uri);

// Add the serialized JSON object to your request
request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");

// Add the authorization header
request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

// Send request, get response
var response = client.SendAsync(request).Result;
var jsonResponse = response.Content.ReadAsStringAsync().Result;

// Print the response
Console.WriteLine(PrettyPrint(jsonResponse));
Console.WriteLine("Press any key to continue.");
```

`PrettyPrint`를 추가하여 JSON 응답에 서식을 추가합니다.
```csharp
static string PrettyPrint(string s)
{
    return JsonConvert.SerializeObject(JsonConvert.DeserializeObject(s), Formatting.Indented);
}
```

Cognitive Services 다중 서비스 구독을 사용하는 경우 요청 매개 변수에 `Ocp-Apim-Subscription-Region`도 포함해야 합니다. [다중 서비스 구독을 사용한 인증에 대해 자세히 알아봅니다](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="put-it-all-together"></a>모든 요소 결합

마지막 단계는 `Main` 함수에서 `AltTranslation()`를 호출하는 것입니다. `static void Main(string[] args)`을 찾고 이러한 줄을 추가하세요.

```csharp
AltTranslation();
Console.WriteLine("Press any key to continue.");
Console.ReadKey();
```

## <a name="run-the-sample-app"></a>샘플 앱 실행

이제 끝났습니다. 샘플 앱을 실행할 준비가 되었습니다. 명령줄(또는 터미널 세션)에서 프로젝트 디렉터리로 이동하고 다음을 실행합니다.

```console
dotnet run
```

## <a name="sample-response"></a>샘플 응답

```json
[
    {
        "displaySource": "elephants",
        "normalizedSource": "elephants",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "elephants",
                        "frequencyCount": 1207,
                        "normalizedText": "elephants",
                        "numExamples": 5
                    }
                ],
                "confidence": 1.0,
                "displayTarget": "elefantes",
                "normalizedTarget": "elefantes",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="clean-up-resources"></a>리소스 정리

샘플 앱의 소스 코드에서 구독 키와 같은 기밀 정보를 제거해야 합니다.

## <a name="next-steps"></a>다음 단계

Translator로 할 수 있는 모든 것에 대해 알아보려면 API 참조를 살펴보세요.

> [!div class="nextstepaction"]
> [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
