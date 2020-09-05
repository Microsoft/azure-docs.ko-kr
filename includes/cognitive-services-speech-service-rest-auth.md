---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 03/29/2019
ms.author: erhopf
ms.openlocfilehash: b5a3ec1d6e33c08b460088c9aeb4fd18f6bf29ff
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864731"
---
## <a name="authentication"></a>인증

각 요청에는 인증 헤더가 필요 합니다. 이 표에서는 각 서비스에 대해 지원되는 헤더를 보여 줍니다.

| 지원되는 인증 헤더 | 음성 텍스트 변환 | 텍스트 음성 변환 |
|------------------------|----------------|----------------|
| Ocp-Apim-Subscription-Key | 예 | 아니요 |
| Authorization: Bearer | 예 | 예 |

`Ocp-Apim-Subscription-Key` 헤더를 사용하는 경우 구독 키만 제공하면 됩니다. 예를 들어:

```http
'Ocp-Apim-Subscription-Key': 'YOUR_SUBSCRIPTION_KEY'
```

`Authorization: Bearer` 헤더를 사용하는 경우 `issueToken` 엔드포인트에 요청해야 합니다. 이 요청에서는 10분 동안 유효한 액세스 토큰에 대한 구독 키를 교환합니다. 다음 섹션에서는 토큰을 가져오고 토큰을 사용 하는 방법에 대해 알아봅니다.

### <a name="how-to-get-an-access-token"></a>액세스 토큰을 가져오는 방법

액세스 토큰을 가져오려면 `Ocp-Apim-Subscription-Key` 및 구독 키를 사용하여 `issueToken` 엔드포인트에 요청해야 합니다.

`issueToken`끝점의 형식은 다음과 같습니다.

```http
https://<REGION_IDENTIFIER>.api.cognitive.microsoft.com/sts/v1.0/issueToken
```

`<REGION_IDENTIFIER>`이 테이블에서 구독의 지역과 일치 하는 식별자로 대체 합니다.

[!INCLUDE [](cognitive-services-speech-service-region-identifier.md)]

이 샘플을 사용하여 액세스 토큰 요청을 만듭니다.

#### <a name="http-sample"></a>HTTP 샘플

이 예제는 토큰을 가져오는 간단한 HTTP 요청입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 구독이 미국 서부 지역이 아닌 경우 `Host` 헤더를 사용자 지역의 호스트 이름으로 바꿉니다.

```http
POST /sts/v1.0/issueToken HTTP/1.1
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.api.cognitive.microsoft.com
Content-type: application/x-www-form-urlencoded
Content-Length: 0
```

응답 본문에는 JWT(JSON Web Token) 형식의 액세스 토큰이 포함됩니다.

#### <a name="powershell-sample"></a>PowerShell 샘플

이 예제는 액세스 토큰을 가져오는 간단한 PowerShell 스크립트입니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 사용자 구독과 일치하는 지역에 맞는 엔드포인트를 사용해야 합니다. 이 예제는 현재 미국 서부로 설정되어 있습니다.

```powershell
$FetchTokenHeader = @{
  'Content-type'='application/x-www-form-urlencoded';
  'Content-Length'= '0';
  'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
}

$OAuthToken = Invoke-RestMethod -Method POST -Uri https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken
 -Headers $FetchTokenHeader

# show the token received
$OAuthToken

```

#### <a name="curl-sample"></a>cURL 샘플

cURL은 Linux(Linux용 Windows 하위 시스템)에서 사용할 수 있는 명령줄 도구입니다. 이 cURL 명령은 액세스 토큰을 가져오는 방법을 설명합니다. `YOUR_SUBSCRIPTION_KEY`를 Speech Service 구독 키로 바꿉니다. 사용자 구독과 일치하는 지역에 맞는 엔드포인트를 사용해야 합니다. 이 예제는 현재 미국 서부로 설정되어 있습니다.

```console
curl -v -X POST \
 "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
 -H "Content-type: application/x-www-form-urlencoded" \
 -H "Content-Length: 0" \
 -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

#### <a name="c-sample"></a>C# 샘플

이 C# 클래스는 액세스 토큰을 가져오는 방법을 설명합니다. 이 클래스를 인스턴스화할 때 Speech Service 구독 키를 전달합니다. 구독이 미국 서부 지역이 아닌 경우 `FetchTokenUri`의 값을 구독의 지역과 일치하도록 변경합니다.

```csharp
public class Authentication
{
    public static readonly string FetchTokenUri =
        "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken";
    private string subscriptionKey;
    private string token;

    public Authentication(string subscriptionKey)
    {
        this.subscriptionKey = subscriptionKey;
        this.token = FetchTokenAsync(FetchTokenUri, subscriptionKey).Result;
    }

    public string GetAccessToken()
    {
        return this.token;
    }

    private async Task<string> FetchTokenAsync(string fetchUri, string subscriptionKey)
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(fetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null);
            Console.WriteLine("Token Uri: {0}", uriBuilder.Uri.AbsoluteUri);
            return await result.Content.ReadAsStringAsync();
        }
    }
}
```

#### <a name="python-sample"></a>Python 샘플

```python
# Request module must be installed.
# Run pip install requests if necessary.
import requests

subscription_key = 'REPLACE_WITH_YOUR_KEY'


def get_token(subscription_key):
    fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
    headers = {
        'Ocp-Apim-Subscription-Key': subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    access_token = str(response.text)
    print(access_token)
```

### <a name="how-to-use-an-access-token"></a>액세스 토큰을 사용하는 방법

액세스 토큰은 `Authorization: Bearer <TOKEN>` 헤더로 서비스에 전송되어야 합니다. 각 액세스 토큰은 10분 동안 유효합니다. 언제든지 새 토큰을 가져올 수 있지만, 네트워크 트래픽 및 대기 시간을 최소화하려면 동일한 토큰을 9분 동안 사용하는 것이 좋습니다.

다음은 Text-to-Speech REST API에 대한 샘플 HTTP 요청입니다.

```http
POST /cognitiveservices/v1 HTTP/1.1
Authorization: Bearer YOUR_ACCESS_TOKEN
Host: westus.stt.speech.microsoft.com
Content-type: application/ssml+xml
Content-Length: 199
Connection: Keep-Alive

// Message body here...
```
