---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 750ce2d19334f549242c37494a11a1f683469f12
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926146"
---
## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인해야 합니다.

> [!div class="checklist"]
> * [개발 환경을 설정하고 빈 프로젝트 만들기](../../../../quickstarts/setup-platform.md?tabs=dotnet&pivots=programmming-language-csharp)
> * [Azure Speech 리소스 만들기](../../../../get-started.md)
> * [Azure Blob에 원본 파일 업로드](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio에서 프로젝트 열기

첫 번째 단계로 Visual Studio에서 프로젝트를 열어야 합니다.

1. Visual Studio 2019를 시작합니다.
2. 프로젝트를 로드하고 `Program.cs`를 엽니다.

## <a name="add-a-reference-to-newtonsoftjson"></a>Newtonsoft.Json에 참조 추가

1. 솔루션 탐색기에서 **helloworld** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 선택하여 NuGet 패키지 관리자를 표시합니다.
1. 오른쪽 위 모서리에서 **패키지 원본** 드롭다운 상자를 찾아서 **`nuget.org`** 를 선택합니다.
1. 왼쪽 위 모서리에서 **찾아보기**를 선택합니다.
1. 검색 상자에 *newtonsoft.json*을 입력하고 **Enter** 키를 선택합니다.
1. 검색 결과에서 [**Newtonsoft.Json**](https://www.nuget.org/packages/Newtonsoft.Json) 패키지를 선택한 다음, **설치**를 선택하여 안정적인 최신 버전을 설치합니다.
1. 설치를 시작하려면 모든 계약 및 라이선스를 수락합니다.
   패키지를 설치하면 **패키지 관리자 콘솔** 창에 확인 메시지가 나타납니다.

## <a name="start-with-some-boilerplate-code"></a>몇 가지 상용구 코드로 시작

프로젝트에 대한 기본 사항으로 작동하는 몇 가지 코드를 추가해 보겠습니다.

```csharp
class Program
{
    // Replace with your subscription key
    const string SubscriptionKey = "YourSubscriptionKey";

    // Update with your service region
    const string Region = "YourServiceRegion";
    const int Port = 443;
 
    // Recordings and locale
    const string Locale = "en-US";
    const string RecordingsBlobUri = "YourFileUrl";
 
    // Name and description
    const string Name = "Simple transcription";
    const string Description = "Simple transcription description";
 
    const string SpeechToTextBasePath = "api/speechtotext/v2.0/";
 
    static async Task Main()
    {
        // Cognitive Services follows security best practices.
        // If you experience connectivity issues, see:
        // https://docs.microsoft.com/en-us/dotnet/framework/network-programming/tls
 
        await TranscribeAsync();
    }
 
    static async Task TranscribeAsync()
    {
        Console.WriteLine("Starting transcriptions client...");
    }
}
```

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="json-wrappers"></a>JSON 래퍼

REST API는 JSON 형식으로 요청을 가져오고 JSON으로 결과를 반환하므로 문자열만 사용하여 상호 작용할 수 있지만 권장되지는 않습니다.
요청 및 응답을 보다 쉽게 관리할 수 있도록 JSON을 직렬화/역직렬화하는 데 사용할 몇 가지 클래스를 선언합니다.

계속해서 `TranscribeAsync` 뒤에 선언을 배치합니다.

```csharp
public class ModelIdentity
{
    ModelIdentity(Guid id) => Id = id;

    public Guid Id { get; private set; }

    public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
}

public class Transcription
{
    [JsonConstructor]
    Transcription(
        Guid id,
        string name,
        string description,
        string locale,
        DateTime createdDateTime,
        DateTime lastActionDateTime,
        string status,
        Uri recordingsUrl,
        IReadOnlyDictionary<string, string> resultsUrls)
    {
        Id = id;
        Name = name;
        Description = description;
        CreatedDateTime = createdDateTime;
        LastActionDateTime = lastActionDateTime;
        Status = status;
        Locale = locale;
        RecordingsUrl = recordingsUrl;
        ResultsUrls = resultsUrls;
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public string Locale { get; set; }

    public Uri RecordingsUrl { get; set; }

    public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

    public Guid Id { get; set; }

    public DateTime CreatedDateTime { get; set; }

    public DateTime LastActionDateTime { get; set; }

    public string Status { get; set; }

    public string StatusMessage { get; set; }
}

public class TranscriptionDefinition
{
    TranscriptionDefinition(
        string name,
        string description,
        string locale,
        Uri recordingsUrl,
        IEnumerable<ModelIdentity> models)
    {
        Name = name;
        Description = description;
        RecordingsUrl = recordingsUrl;
        Locale = locale;
        Models = models;
        Properties = new Dictionary<string, string>
        {
            ["PunctuationMode"] = "DictatedAndAutomatic",
            ["ProfanityFilterMode"] = "Masked",
            ["AddWordLevelTimestamps"] = "True"
        };
    }

    public string Name { get; set; }

    public string Description { get; set; }

    public Uri RecordingsUrl { get; set; }

    public string Locale { get; set; }

    public IEnumerable<ModelIdentity> Models { get; set; }

    public IDictionary<string, string> Properties { get; set; }

    public static TranscriptionDefinition Create(
        string name,
        string description,
        string locale,
        Uri recordingsUrl)
        => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
}
```

## <a name="create-and-configure-an-http-client"></a>Http 클라이언트 만들기 및 구성
가장 먼저 필요한 것은 올바른 기본 URL과 인증 집합이 있는 Http 클라이언트입니다.
이 코드를 `TranscribeAsync`에 삽입합니다.

```csharp
var client = new HttpClient
{
    Timeout = TimeSpan.FromMinutes(25),
    BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
    DefaultRequestHeaders =
    {
        { "Ocp-Apim-Subscription-Key", SubscriptionKey }
    }
};
```

## <a name="generate-a-transcription-request"></a>전사 요청 생성
다음으로, 전사 요청을 생성합니다. `TranscribeAsync`에 이 코드를 추가합니다.

```csharp
var transcriptionDefinition =
    TranscriptionDefinition.Create(
        Name,
        Description,
        Locale,
        new Uri(RecordingsBlobUri));

var res = JsonConvert.SerializeObject(transcriptionDefinition);
var sc = new StringContent(res);
sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;
```

## <a name="send-the-request-and-check-its-status"></a>요청을 보내고 상태 확인
이제 Speech Service에 요청을 게시하고 초기 응답 코드를 확인합니다. 이 응답 코드는 서비스에서 요청을 받았는지 여부를 표시합니다. 서비스는 전사 상태를 저장할 위치에 해당하는 Url을 응답 헤더에 반환합니다.

```csharp
Uri transcriptionLocation = null;
using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
{
    if (!response.IsSuccessStatusCode)
    {
        Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
        return;
    }

    transcriptionLocation = response.Headers.Location;
}
```

## <a name="wait-for-the-transcription-to-complete"></a>전사를 완료할 때까지 대기
서비스는 전사를 비동기식으로 처리하므로 상태를 폴링해야 하는 경우가 있습니다. 5초마다 확인합니다.

요청을 게시할 때 가져온 Url에서 콘텐츠를 검색하여 상태를 확인할 수 있습니다. 콘텐츠를 다시 가져오는 경우 쉽게 상호 작용할 수 있도록 도우미 클래스 중 하나로 역직렬화합니다.

다음은 성공적인 완료를 제외한 모든 상태에 대한 상태 표시가 있는 폴링 코드입니다. 다음에 수행하겠습니다.

```csharp
Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
Console.WriteLine("Checking status.");

var completed = false;

// Check for the status of our transcriptions periodically
while (!completed)
{
    Transcription transcription = null;
    using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
    {
        var contentType = response.Content.Headers.ContentType;
        if (response.IsSuccessStatusCode &&
            string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
        {
            transcription = await response.Content.ReadAsAsync<Transcription>();
        }
        else
        {
            Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
            continue;
        }
    }

    switch (transcription.Status)
    {
        case "Failed":
            completed = true;
            Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
            break;

        case "Succeeded":
            break;

        case "Running":
            Console.WriteLine("Transcription is still running.");
            break;

        case "NotStarted":
            Console.WriteLine("Transcription has not started.");
            break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}

Console.WriteLine("Press any key...");
Console.ReadKey();
```

## <a name="display-the-transcription-results"></a>전사 결과 표시
서비스가 전사를 성공적으로 완료하면 결과가 상태 응답에서 가져올 수 있는 다른 Url에 저장됩니다. 여기에서 이러한 결과를 읽고 역직렬화하기 전에 임시 파일에 다운로드하도록 요청합니다.
결과가 로드된 후에는 콘솔에 출력할 수 있습니다. `case "Succeeded":` 레이블에 다음 코드를 추가합니다.

```csharp
completed = true;
var webClient = new WebClient();
var filename = Path.GetTempFileName();
webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
var results = File.ReadAllText(filename);
Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
File.Delete(filename);
```

## <a name="check-your-code"></a>코드 확인
이 시점에서 코드는 다음과 같습니다. (이 버전에 몇 가지 주석을 추가함)

```csharp
using Newtonsoft.Json;
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Threading.Tasks;
using System.Net.Http;
using System.Net.Http.Formatting;

namespace BatchClient
{
    class Program
    {
        // Replace with your subscription key
        const string SubscriptionKey = "YourSubscriptionKey";

        // Update with your service region
        const string Region = "YourServiceRegion";
        const int Port = 443;

        // Recordings and locale
        const string Locale = "en-US";
        const string RecordingsBlobUri = "YourFileUrl";

        // Name and description
        const string Name = "Simple transcription";
        const string Description = "Simple transcription description";

        const string SpeechToTextBasePath = "api/speechtotext/v2.0/";

        static async Task Main()
        {
            // For non-Windows 10 users.
            ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12;

            await TranscribeAsync();
        }

        static async Task TranscribeAsync()
        {
            Console.WriteLine("Starting transcriptions client...");

            // Create the client object and authenticate
            var client = new HttpClient
            {
                Timeout = TimeSpan.FromMinutes(25),
                BaseAddress = new UriBuilder(Uri.UriSchemeHttps, $"{Region}.cris.ai", Port).Uri,
                DefaultRequestHeaders =
                {
                    { "Ocp-Apim-Subscription-Key", SubscriptionKey }
                }
            };

            var transcriptionDefinition =
                TranscriptionDefinition.Create(
                    Name,
                    Description,
                    Locale,
                    new Uri(RecordingsBlobUri));

            var res = JsonConvert.SerializeObject(transcriptionDefinition);
            var sc = new StringContent(res);
            sc.Headers.ContentType = JsonMediaTypeFormatter.DefaultMediaType;

            Uri transcriptionLocation = null;

            using (var response = await client.PostAsync($"{SpeechToTextBasePath}Transcriptions/", sc))
            {
                if (!response.IsSuccessStatusCode)
                {
                    Console.WriteLine("Error {0} starting transcription.", response.StatusCode);
                    return;
                }

                transcriptionLocation = response.Headers.Location;
            }

            Console.WriteLine($"Created transcription at location {transcriptionLocation}.");
            Console.WriteLine("Checking status.");

            var completed = false;

            // Check for the status of our transcriptions periodically
            while (!completed)
            {
                Transcription transcription = null;

                // Get all transcriptions for the user
                using (var response = await client.GetAsync(transcriptionLocation.AbsolutePath))
                {
                    var contentType = response.Content.Headers.ContentType;
                    if (response.IsSuccessStatusCode &&
                        string.Equals(contentType.MediaType, "application/json", StringComparison.OrdinalIgnoreCase))
                    {
                        transcription = await response.Content.ReadAsAsync<Transcription>();
                    }
                    else
                    {
                        Console.WriteLine("Error with status {0} getting transcription result", response.StatusCode);
                        continue;
                    }
                }

                // For each transcription in the list we check the status
                switch (transcription.Status)
                {
                    case "Failed":
                        completed = true;
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.StatusMessage);
                        break;

                    case "Succeeded":
                        completed = true;
                        var webClient = new WebClient();
                        var filename = Path.GetTempFileName();
                        webClient.DownloadFile(transcription.ResultsUrls["channel_0"], filename);
                        var results = File.ReadAllText(filename);
                        Console.WriteLine($"Transcription succeeded. Results: {Environment.NewLine}{results}");
                        File.Delete(filename);
                        break;

                    case "Running":
                        Console.WriteLine("Transcription is still running.");
                        break;

                    case "NotStarted":
                        Console.WriteLine("Transcription has not started.");
                        break;
                }

                await Task.Delay(TimeSpan.FromSeconds(5));
            }

            Console.WriteLine("Press any key...");
            Console.ReadKey();
        }
    }

    public class ModelIdentity
    {
        ModelIdentity(Guid id) => Id = id;

        public Guid Id { get; private set; }

        public static ModelIdentity Create(Guid Id) => new ModelIdentity(Id);
    }

    public class Transcription
    {
        [JsonConstructor]
        Transcription(
            Guid id,
            string name,
            string description,
            string locale,
            DateTime createdDateTime,
            DateTime lastActionDateTime,
            string status,
            Uri recordingsUrl,
            IReadOnlyDictionary<string, string> resultsUrls)
        {
            Id = id;
            Name = name;
            Description = description;
            CreatedDateTime = createdDateTime;
            LastActionDateTime = lastActionDateTime;
            Status = status;
            Locale = locale;
            RecordingsUrl = recordingsUrl;
            ResultsUrls = resultsUrls;
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public string Locale { get; set; }

        public Uri RecordingsUrl { get; set; }

        public IReadOnlyDictionary<string, string> ResultsUrls { get; set; }

        public Guid Id { get; set; }

        public DateTime CreatedDateTime { get; set; }

        public DateTime LastActionDateTime { get; set; }

        public string Status { get; set; }

        public string StatusMessage { get; set; }
    }

    public class TranscriptionDefinition
    {
        TranscriptionDefinition(
            string name,
            string description,
            string locale,
            Uri recordingsUrl,
            IEnumerable<ModelIdentity> models)
        {
            Name = name;
            Description = description;
            RecordingsUrl = recordingsUrl;
            Locale = locale;
            Models = models;
            Properties = new Dictionary<string, string>
            {
                ["PunctuationMode"] = "DictatedAndAutomatic",
                ["ProfanityFilterMode"] = "Masked",
                ["AddWordLevelTimestamps"] = "True"
            };
        }

        public string Name { get; set; }

        public string Description { get; set; }

        public Uri RecordingsUrl { get; set; }

        public string Locale { get; set; }

        public IEnumerable<ModelIdentity> Models { get; set; }

        public IDictionary<string, string> Properties { get; set; }

        public static TranscriptionDefinition Create(
            string name,
            string description,
            string locale,
            Uri recordingsUrl)
            => new TranscriptionDefinition(name, description, locale, recordingsUrl, new ModelIdentity[0]);
    }
}
```

## <a name="build-and-run-your-app"></a>앱 빌드 및 실행

이제 앱을 빌드하고 음성 서비스를 사용하여 음성 인식을 테스트할 준비가 되었습니다.

1. **코드 컴파일** - Visual Studio의 메뉴 모음에서 **빌드** > **빌드 솔루션**을 선택합니다.
2. **앱 시작** - 메뉴 모음에서 **디버그** > **디버깅 시작**을 선택하거나 **F5** 키를 누릅니다.
3. **인식 시작** - 영어로 문구를 말하라는 메시지가 표시됩니다. 음성은 Speech Service로 전송되어 텍스트로 변환되고 콘솔에 렌더링됩니다.

## <a name="next-steps"></a>다음 단계

[!INCLUDE [footer](./footer.md)]
